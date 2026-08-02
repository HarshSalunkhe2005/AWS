# Progress Log

## Task 1 — AWS Deployment (Retail Agentic AI)

- [x] Picked project: Retail-Agentic-AI (existing PBL1, FastAPI + React)
- [x] Audited repo for deployment readiness, cleaned up Railway-specific files,
      removed unused orphan scripts, fixed stale README/devcontainer
      (see `harshsalunkhe2005/Retail-Agentic-AI`, commit `b133a47`)
- [x] Created AWS account
- [x] Stage 1 — IAM: created a non-root IAM user with AdministratorAccess +
      generated a CLI access key pair
- [x] Stage 2 — Launch EC2 instance (Ubuntu 26.04 LTS, t3.micro, `retail-agentic-ai-backend`)
- [x] Stage 3 — Security Groups (opened HTTP/80 publicly, 8000 temp for testing, SSH stayed IP-locked)
- [x] Stage 4a — SSH access confirmed (Windows PowerShell + OpenSSH, icacls instead of chmod)
- [x] Stage 4b (part 1) — backend running as a systemd service (`retail-backend`),
      confirmed reachable at `http://<public-ip>:8000/health` → `{"status":"ok"}`
- [x] Stage 4b (part 2) — Nginx reverse proxy configured (80 → 127.0.0.1:8000),
      confirmed `http://<public-ip>/health` works without `:8000`; port 8000
      closed in the Security Group afterward (Nginx is now the only public entry)
- [x] Stage 5 — S3 static website hosting for the frontend (skipped CloudFront —
      would need HTTPS on the backend too, mixed-content issue; plain S3+HTTP
      matches the backend and was the pragmatic call given the deadline)
- [x] Stage 6 — CORS locked to the S3 origin, backend service restarted, full
      end-to-end test done in-browser with a real dataset upload — works
- [x] Task 1 deployment complete. Full writeup + screenshots in
      `Task 1 PPT contents/`
- [ ] Fill in the Task 1 tracking sheet (problem statement, platform, hosting link)
- [ ] Build Task 1 section of the PPT (content ready in `Task 1 PPT contents/`)

## Task 2 — Multi-VM Virtualized Environment

- [x] VirtualBox installed, 3 Ubuntu Server VMs created: `rrafin` (VM1, web
      server), `ubuntu2` (VM2, client), `ubuntu2 Clone` (VM3, client)
- [x] Networking: NAT + Host-only adapters on each VM, private network
      `192.168.56.0/24`
- [x] Apache2 installed on VM1, custom page live, confirmed `active (running)`
- [x] VM-to-VM connectivity verified: `ping` + cross-VM HTTP requests from both
      VM2 and VM3 to VM1, all successful
- [x] Task 2 implementation complete. Full writeup, architecture diagram, demo
      script, viva Q&A in `Task 2 PPT contents/`
- [ ] Delete orphaned first-attempt VM (`hharsh`) if not already done
- [ ] Take final screenshots (all 3 VMs running side-by-side, `ip a` per VM)
- [ ] Build Task 2 section of the PPT (content ready in `Task 2 PPT contents/`)

## Incidents / gotchas encountered (worth mentioning in the report)

- `pip install -r requirements.txt` (Prophet/cmdstanpy build) OOM-killed the SSH
  session on t3.micro's 1 GB RAM — fixed by adding a 2 GB swap file.
- SSH then started timing out entirely — turned out to be Cloudflare WARP VPN
  (used on college WiFi) constantly changing the effective public IP, so the
  Security Group's "My IP" SSH rule kept going stale.
- **TEMP FIX**: SSH (port 22) inbound rule set to `0.0.0.0/0` (anywhere) to
  unblock development. **TODO before final demo: lock SSH source back down**
  (either a specific IP with WARP off, or remove the rule and use EC2 Instance
  Connect / Session Manager instead). Key-based auth only (no password login)
  limits the real risk in the meantime, but this should not ship open.
- `pip install` then failed with `OSError: [Errno 122] Disk quota exceeded` while
  downloading the 223 MB `xgboost` wheel, even with 14G free on `/`. Root cause:
  pip's temp download directory defaults to `/tmp`, which on this AMI is a
  **tmpfs** (RAM-backed, capped at 455 MB) — unrelated to real disk space.
  Fixed by resizing the EBS volume (8G → 20G via Console + `growpart` +
  `resize2fs`, which also gave breathing room generally) **and** setting
  `TMPDIR=~/tmp` (a real on-disk directory) before running pip.
- Net result: backend Python dependencies (FastAPI, XGBoost, Prophet, scikit-learn,
  mlxtend, etc.) installed successfully in the venv.

## Notes

- AWS hosting must stay within Free Tier (t2.micro/t3.micro EC2, S3 free tier — no paid services)
- No AI/Claude attribution anywhere in commits, docs, or the PPT — all authored as the group
