# Stage 2 — EC2 Launch (Done)

## Why

EC2 is AWS's IaaS compute service — a rented virtual machine, billed hourly (free
tier: 750 hrs/month of t2/t3.micro for 12 months, enough for one instance running
24/7). Under the hood, EC2 runs guest instances on a hypervisor (historically Xen,
now increasingly AWS Nitro/KVM-based) — the same Dom0/DomU, Type-1 bare-metal
hypervisor model covered in Unit 2. Our EC2 instance is itself a live example of
those virtualization concepts.

## What we did

1. Logged into the AWS Console as the IAM user (not root)
2. EC2 → Launch Instance
3. Name: `retail-agentic-ai-backend`
4. AMI: **Ubuntu Server 26.04 LTS (HVM), SSD Volume Type** — free tier eligible
   - Note: first attempt accidentally picked a Windows+SQL Server AMI from the
     Quick Start list, which threw "Microsoft SQL Server is not supported for the
     instance type" — fixed by explicitly selecting the Ubuntu tab.
5. Instance type: `t3.micro` (free tier eligible in this account's region —
   AWS phased newer accounts from t2.micro to t3.micro as the default free-tier type)
6. Key pair: created new RSA key pair, `.pem` format, downloaded and saved
7. Network settings: default VPC, new security group created with SSH (port 22)
   allowed only from "My IP" (not 0.0.0.0/0 — don't expose SSH to the whole internet)
8. Storage: default 8 GB gp3 (within the 30 GB free tier allowance)
9. Launched — instance reached **Running** state with a public IPv4 address assigned

## Status: done

Next: Stage 3 — Security Groups (open the ports the app actually needs: 80/443 for
Nginx, and confirm 22 stays locked to our IP).
