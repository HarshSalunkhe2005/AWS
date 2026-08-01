# Stage 1 — IAM Setup (Done)

## Why

The root AWS account (tied to the sign-up email) has unlimited power — billing,
deleting resources, everything. Standard AWS practice is to never use root for
day-to-day work; instead create an IAM user scoped to what's actually needed, and
use that for everything else.

## What we did

1. AWS Console → IAM → Users → Create user (`harsh-admin`)
2. Enabled console access with a password
3. Attached the `AdministratorAccess` policy directly
   - For a real company you'd scope this down (least privilege) — full admin on a
     non-root IAM user is the practical standard for a single-person course project.
4. Generated a CLI access key pair (Security credentials tab → Access keys →
   Create access key → CLI use case)

## Status: done

Next: Stage 2 — launch the EC2 instance.
