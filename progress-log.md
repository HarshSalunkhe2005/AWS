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
- [ ] Stage 3 — Security Groups (firewall rules for the instance)
- [ ] Stage 4 — SSH in, deploy backend (Python, systemd service, Nginx reverse proxy)
- [ ] Stage 5 — S3 + CloudFront for the frontend
- [ ] Stage 6 — Wire frontend/backend together (CORS, env vars), final test
- [ ] Fill in the Task 1 tracking sheet (problem statement, platform, hosting link)
- [ ] Build Task 1 section of the PPT

## Task 2 — Multi-VM Virtualized Environment

- [ ] Not started yet — plan: 3+ VMs on a single host via VirtualBox/KVM,
      tying setup back to Unit 2 hypervisor/Xen/KVM theory
- [ ] Build Task 2 section of the PPT

## Notes

- AWS hosting must stay within Free Tier (t2.micro/t3.micro EC2, S3 free tier — no paid services)
- No AI/Claude attribution anywhere in commits, docs, or the PPT — all authored as the group
