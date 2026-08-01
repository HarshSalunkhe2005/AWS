# CA1 — Cloud Computing: Task 1 & Task 2

Working notes for the CA1 group project. This repo is the source of truth for
both tasks' content — the final PPTs (submitted to the Google Drive folder)
get built from what's documented here.

Due: 3rd August 2026 · Case study demo: 4–6 August 2026

## Structure

```
Task 1 PPT contents/    Task 1 — final PPT-ready content: architecture, full deployment
                         journey (all stages + every incident/fix), and screenshots
task2-multi-vm/          Task 2 — multi-VM virtualized environment on a single host
progress-log.md          Running log of what's been done, in order
```

## Task 1 — summary — DONE, working end-to-end

**Project**: [Retail-Agentic-AI](https://github.com/HarshSalunkhe2005/Retail-Agentic-AI) —
an existing PBL1 project. ML-powered retail decision-support system (FastAPI backend +
React/Vite frontend) with pricing intelligence, customer churn/segmentation, demand
forecasting, market basket analysis, and inventory/PO recommendations.

**Deployed to**: AWS Free Tier — EC2 (backend, Nginx + systemd) + S3 static website
hosting (frontend). Live, verified end-to-end with real data through the UI.

**Course tie-in (Unit 1)**: demonstrates on-demand self-service, IaaS (EC2 compute) and
a static-hosting pattern (S3), one of AWS's public cloud deployment models.

See `Task 1 PPT contents/` for the full writeup and screenshots.

## Task 2 — summary

**Goal**: minimum 3 cloud instances / VMs virtualized on a single host.

**Course tie-in (Unit 2)**: hypervisor theory (Type 1 vs Type 2), Xen Dom0/DomU
architecture, KVM, full vs para-virtualization — directly from the syllabus. See
`task2-multi-vm/` for how the hands-on VirtualBox/KVM setup maps to these concepts.

## Group

Group 3 — Dharna Sharma, Harsh Salunkhe, Rafin Aryan, Jigesha Kapoor
