# Live Demo Script

Run through in this order — each step has a clear visible "proof" moment for the examiner.

## 1. Show all 3 VMs running simultaneously (30 sec)
Open VirtualBox Manager, start VM1, VM2, VM3 if not already running. Arrange the 3
windows visibly on screen (or switch between them quickly) — this alone proves
"minimum 3 instances on a single host."

**Say**: "These are three independent Ubuntu virtual machines, all running
simultaneously on this one physical laptop, managed by Oracle VirtualBox — a
Type-2 hosted hypervisor."

## 2. Show the network configuration (30 sec)
On any VM, open Settings → Network (or show it beforehand) — point out Adapter 1
(NAT) and Adapter 2 (Host-only).

**Say**: "Each VM has two network adapters — NAT for internet access to install
packages, and a Host-only adapter that puts all three VMs on a private
192.168.56.0/24 network so they can talk directly to each other."

## 3. Prove VM1 is a working web server (30 sec)
In VM1's terminal:
```bash
systemctl status apache2
```
Point out `active (running)` in green. Then open VM1's own browser to
`http://localhost` and show the custom page loading.

## 4. Prove VM-to-VM communication — the core of Task 2 (60 sec)
Switch to VM2's terminal:
```bash
ping 192.168.56.103
```
Let 3-4 replies print, then Ctrl+C. **Say**: "This confirms VM2 can reach VM1
directly over the private network, independent of the internet."

Then open VM2's browser to `http://192.168.56.103` — the same custom page loads,
now fetched **across the network from a separate VM**, not localhost.

Repeat the same two checks on VM3 for a complete demonstration.

## 5. Wrap-up statement
**Say**: "This demonstrates a working multi-VM virtualized environment — three
independent instances, networked together on a single host, with one machine
providing an actual service that the other two consume over the network — the
same conceptual pattern (a server + networked clients) used in real cloud
deployments, just running locally via a Type-2 hypervisor instead of AWS's Type-1
hypervisor infrastructure covered in Task 1."
