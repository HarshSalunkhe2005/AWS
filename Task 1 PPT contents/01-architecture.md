# Task 1 — Architecture

## What we're deploying

**Retail Agentic AI** — an ML-powered retail decision-support system, originally built
as a PBL1 project.

- **Backend**: FastAPI (Python) — 6 modules: pricing intelligence, customer
  churn/segmentation, demand forecasting (Prophet), market basket analysis
  (FP-Growth), inventory/PO recommendations, model-compatibility check.
  Loads pre-trained `.pkl` models (XGBoost, KMeans, Prophet) into memory at startup.
- **Frontend**: React 19 + TypeScript + Vite SPA, calls the backend over REST.

## Target AWS architecture

```
                    ┌─────────────────────┐
   Browser  ───────▶│  S3 + CloudFront     │  ← React frontend (static build)
                    │  (frontend)          │
                    └──────────┬───────────┘
                               │ HTTPS API calls
                               ▼
                    ┌─────────────────────┐
                    │  EC2 (t2.micro)      │  ← FastAPI backend + ML models
                    │  Nginx → Uvicorn     │     Security Group = firewall
                    └─────────────────────┘
```

**Why split like this**: the frontend is static files with no server-side logic — no
reason to pay for/run compute to serve it, so S3 (object storage) + CloudFront (CDN)
is the natural fit and it's free-tier eligible. The backend has to keep a Python
process running with ML models loaded in memory, so it needs real, continuous
compute — that's EC2.

## Course tie-in (Unit 1)

- **On-demand self-service**: we provision EC2/S3 ourselves via the AWS Console/IAM
  user, no request ticket to a human.
- **IaaS**: EC2 is Infrastructure-as-a-Service — we manage the OS, runtime, and app;
  AWS manages the physical hardware, hypervisor, and networking underneath.
- **Public cloud deployment model**: AWS is a public cloud provider (per the "Four
  Types of Deployment Models" slide — public/private/community/hybrid).
