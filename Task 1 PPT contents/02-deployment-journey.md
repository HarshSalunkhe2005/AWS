# Task 1 — Deployment Journey (PPT script)

End-to-end account of deploying Retail Agentic AI to AWS. Written so each numbered
section can become a slide (or a couple of slides) — problem → what we did → why →
what went wrong → how we fixed it.

## 1. Repo cleanup, pre-deployment

Before touching AWS, the project itself was audited and cleaned:
- Removed Railway-specific config (`railway.json`, `Procfile`) — the project had
  previously been hosted on Railway and had deployment problems there
- Removed two orphaned Colab notebook exports (~120 KB) that weren't wired into
  the app at all
- Fixed a stale README that still described a Streamlit app the project had
  since evolved away from — it's actually a FastAPI backend + React/Vite frontend
- Stopped force-committing `.env` files via a `.gitignore` negation bug

## 2. IAM — access control, done right

Created a dedicated IAM user (`AdministratorAccess`) instead of using the AWS
root account for daily work — root has unlimited billing/account power and
should never be used for routine operations. Generated a CLI access key pair
for this user.

## 3. EC2 — the backend compute layer

Launched a `t3.micro` instance (AWS Free Tier: 750 hrs/month for 12 months) on
**Ubuntu 26.04 LTS**. First attempt accidentally selected a Windows+SQL Server
AMI from the Quick Start list ("Microsoft SQL Server is not supported for the
instance type" error) — fixed by explicitly picking the Ubuntu tab.

EC2 itself is a real-world instance of Unit 2's virtualization theory: guest
instances run on a hypervisor (historically Xen — Type 1/bare-metal, the exact
Dom0/DomU model from the syllabus; increasingly AWS's own Nitro/KVM-based
hypervisor on newer instance types).

## 4. Security Groups — the firewall

AWS's instance-level virtual firewall. Configured to allow SSH (22) and later
HTTP (80) — deliberately keeping the attack surface minimal. See the incidents
section below for how this rule caused real problems during development.

## 5. Backend deployment — and everything that went wrong

Deployed FastAPI backend via SSH: cloned the repo, created a Python virtual
environment, installed dependencies, ran it as a permanent **systemd service**
(auto-restart, survives reboots/disconnects), then put **Nginx** in front of it
as a reverse proxy (port 80 → internal port 8000) so the raw application port
never needs to be publicly exposed.

### Incident 1 — OOM kill during dependency install
`pip install` (building Prophet's `cmdstanpy` backend, which compiles native
C++ code) killed the SSH session outright on `t3.micro`'s 1 GB RAM. **Fix**:
added a 2 GB swap file so the OS has overflow memory for heavy compilation.

### Incident 2 — SSH access became unreliable
After the swap fix, SSH started timing out entirely and unpredictably. Root
cause: **Cloudflare WARP VPN** (used on college WiFi) kept changing the
machine's effective public IP, so the Security Group's "My IP" SSH rule went
stale between sessions. Temporarily widened SSH to `0.0.0.0/0` to unblock
development, with an explicit note to lock it back down before the demo.
Key-based auth only (EC2 disables password login by default) limited the real
exposure in the meantime.

### Incident 3 — disk quota exceeded, despite having free disk space
`pip install` failed with `OSError: [Errno 122] Disk quota exceeded` while
downloading the 223 MB `xgboost` wheel — even though `df -h` showed 14 GB free.
**Root cause**: error 122 is `EDQUOT`, not `ENOSPC` — a different error
entirely. Pip's temp download directory defaults to `/tmp`, which on this AMI
is a **tmpfs** (RAM-backed filesystem, capped at 455 MB) — completely unrelated
to the 20 GB root disk. Fixed two ways: resized the EBS volume 8 GB → 20 GB
(via Console **Modify volume** + `growpart` + `resize2fs` to extend the actual
partition/filesystem into the new space — resizing the volume alone doesn't
automatically grow the filesystem), and set `TMPDIR` to a real on-disk
directory before running pip, keeping large temp files off the RAM-backed
tmpfs entirely.

**End state**: backend running as `systemctl status retail-backend` →
`active (running)`, reachable at `http://<public-ip>/health` → `{"status":"ok"}`.

## 6. Frontend deployment — S3 static hosting

Built the React/Vite frontend locally (`npm run build`), with `VITE_API_BASE_URL`
pointed at the EC2 backend's URL — baked in at **build time** since Vite inlines
env vars into the compiled JS, unlike a runtime env var.

Architecture decision: skipped CloudFront. CloudFront serves HTTPS by default,
but the backend is plain HTTP (no domain/cert set up for it) — browsers block
an HTTPS page calling an HTTP API ("mixed content"). Plain **S3 static website
hosting** (HTTP) matches the backend and avoids the problem entirely; CloudFront
is a valid "next step" polish item, not needed for a working deployment.

Bucket setup: created with public access unblocked (deliberately, scoped to
read-only via a bucket policy — not a blanket opening), static website hosting
enabled (`index.html` as both index *and* error document, since this is a
React Router SPA — unknown paths should still load the app shell), uploaded the
`dist/` build output.

### Incident 4 — bucket policy "invalid resource"
The bucket name AWS actually assigned (`retail-agentic-ai-frontend-<account-id>-<region>`,
using the account-ID-suffixed uniqueness option) didn't match what the policy's
ARN referenced. Fixed by matching the policy's `Resource` field to the real
bucket name/ARN exactly.

## 7. Final wiring — CORS lockdown

Backend's `.env` had `CORS_ORIGINS=*` during setup/testing. Locked to the
specific S3 website origin once the frontend was live, then restarted the
`retail-backend` systemd service to pick up the change.

## Result

End-to-end working deployment: S3-hosted React frontend → calls → EC2-hosted
FastAPI backend (behind Nginx) → runs the actual ML models (pricing, churn
segmentation, demand forecasting, basket analysis) → returns live results,
verified in-browser with a real dataset upload through the Wizard flow. See
`screenshots/` for the running app.
