# Build Log

Actual implementation history — kept for the report/appendix, showing real
troubleshooting rather than a clean first-try.

## Initial plan vs. final implementation
Original plan called for distinct roles per VM (web / database / storage). After
weighing setup time against the deadline, the final implementation instead used
**1 web server (VM1) + 2 client machines (VM2, VM3)** — this still satisfies the
literal requirement (minimum 3 instances, on a single host, networked and
demonstrated) with a simpler, more reliable build.

## Timeline

1. **VM1 first attempt** (`hharsh` / hostname `harsh`) — created, installed Ubuntu
   Server, configured NAT + Host-only networking (Host-only IP `192.168.56.101`).
   OpenSSH's install checkbox in the installer didn't actually install the
   package; fixed manually with `sudo apt install -y openssh-server`. Confirmed
   working via `ping` and `ssh` from the host. This VM was later removed/replaced
   in favor of a cleaner rebuild (see below) — not part of the final 3.

2. **Resource troubleshooting** — During VM1's install, the installer hit a kernel
   `soft lockup - CPU stuck` warning and stalled repeatedly. Root cause: host RAM
   pressure — Task Manager showed 90-95% memory used before install even started,
   driven by BlueStacks (an Android emulator, itself resource-heavy) and many
   Chrome tabs. Closing BlueStacks and excess tabs reduced pressure enough for the
   install to complete. Baseline idle RAM usage on this laptop stayed high
   (~7.7GB / 66%) even afterward, due to manufacturer software (Lenovo Vantage,
   NVIDIA services) and Bitdefender's real-time scanning — confirmed not malware,
   just a heavy default software stack on a gaming-laptop SKU with only 1 of 2 RAM
   slots populated.

3. **Final 3-VM build** — `rrafin` (VM1, web server), `ubuntu2` (VM2, client),
   `ubuntu2 Clone` (VM3, client, created by cloning VM2 with a regenerated MAC
   address to avoid network conflicts). Each VM: 1GB RAM, 1 vCPU, 20GB dynamic
   disk, NAT + Host-only (`192.168.56.0/24`) adapters.

4. **Apache deployed on VM1** — `sudo apt install -y apache2`, confirmed
   `active (running)`, default page replaced with a custom `index.html` showing
   the project title and VM1's IP.

5. **Connectivity verified** — `ping 192.168.56.103` from both VM2 and VM3
   succeeded; both VMs' browsers successfully loaded VM1's page via
   `http://192.168.56.103`, confirming real VM-to-VM communication over the
   host-only network.

## Lessons learned (worth stating in the report)
- Host RAM, not CPU, was the real bottleneck for local multi-VM work — the CPU
  (8-core/12-thread) was barely touched throughout.
- Background consumer software (emulators, antivirus, manufacturer utilities) has
  a real, measurable impact on how many VMs a laptop can comfortably run.
- VirtualBox's unattended-installer options (like the OpenSSH checkbox) aren't
  always reliable — verifying and manually fixing afterward is a normal part of
  the process, not a failure.
