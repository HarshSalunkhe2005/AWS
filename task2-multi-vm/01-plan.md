# Task 2 — Multi-VM Virtualized Environment (Plan)

**Requirement**: design, implement, and demonstrate a multi-VM virtualized cloud
environment (minimum 3 instances) on a single host, using any hypervisor.

## Course tie-in (Unit 2) — use this in the PPT/demo explanation

Straight from the syllabus:

- **Hypervisor types**:
  - *Type 1 (bare-metal)*: runs directly on hardware, no host OS underneath —
    e.g. Xen, VMware ESX. This is what AWS EC2 itself runs on under the hood.
  - *Type 2 (hosted)*: runs as an application on top of a host OS — e.g. VirtualBox,
    VMware Workstation. **This is what we'll actually use for Task 2**, since we
    need 3 VMs on a single host we control (a laptop), not bare-metal server access.
- **Xen architecture**: Dom0 (privileged domain, controls hardware, runs device
  drivers) + DomU (unprivileged guest domains) — worth mentioning even though we're
  using VirtualBox, to show we understand the underlying model AWS/Xen use.
- **Full virtualization vs para-virtualization**: VirtualBox uses hardware-assisted
  full virtualization (Intel VT-x/AMD-V) — guest OS is unmodified and unaware it's
  virtualized, unlike para-virtualization (Xen/KVM) which modifies the guest kernel
  to use hypercalls.

## Planned setup

- **Hypervisor**: VirtualBox (Type 2, free, straightforward for a laptop-based demo)
- **3 VMs minimum**, one plausible split for a "cloud environment" story matching
  the three-tier pattern also seen elsewhere in the class (see Group 5's "Three-Tier
  Web Infrastructure" problem statement — ours should be distinct but can use the
  same conceptual pattern):
  1. VM 1 — Web/App server
  2. VM 2 — Database server
  3. VM 3 — Storage / backup or a load-balancer node
- Networked together via VirtualBox's internal/host-only networking so the VMs can
  talk to each other, demonstrating a real multi-tier environment, not just 3
  isolated boxes.

## Status: in progress

Host machine: Windows laptop, Intel i5-12450HX (8 cores/12 threads), 12GB RAM
(1 of 2 slots populated). RAM is the binding constraint, not CPU — per-VM memory
kept to 1GB, and not all 3 VMs need to run simultaneously except for the live demo.

Each VM gets 2 network adapters: Adapter 1 = NAT (internet access for `apt`),
Adapter 2 = Host-only Adapter (lets host + all VMs reach each other directly —
this is what makes it a real "networked multi-VM environment" and not 3 isolated boxes).

- [x] VM1 — Web role. VirtualBox name `hharsh`, hostname `harsh`.
      NAT IP `10.0.2.15`, Host-only IP `192.168.56.101`. OpenSSH installed
      manually after the unattended-installer's SSH checkbox didn't take;
      confirmed reachable via `ping` and `ssh` from the host over the
      host-only network.
- [ ] VM2 — Database role. Not created yet.
- [ ] VM3 — Storage/backup role. Not created yet.
- [ ] Confirm VM-to-VM connectivity (not just host-to-VM) once VM2/VM3 exist
- [ ] Install actual role software on each (e.g. nginx on VM1, MySQL/Postgres
      on VM2, an NFS/Samba share or backup script on VM3) so the demo shows
      real services, not just empty VMs
- [ ] Screenshots/recording of all 3 running + talking to each other, for the PPT

### Incidents

- During VM1's Ubuntu Server install, the installer hit a kernel
  `soft lockup - CPU stuck` warning and stalled repeatedly. Root cause: host
  RAM pressure — Task Manager showed 90-95% memory used before install even
  started, from BlueStacks (an Android emulator, itself resource-heavy),
  many Chrome tabs, and background software. Closing BlueStacks + excess
  Chrome tabs reduced pressure enough for the install to complete. Baseline
  idle RAM usage on this laptop is still high (~7.7GB / 66%) due to
  manufacturer bloat (Lenovo Vantage, NVIDIA services) and Bitdefender's
  real-time scan service — not a virus, just a heavy default software stack
  on a gaming-laptop SKU.
