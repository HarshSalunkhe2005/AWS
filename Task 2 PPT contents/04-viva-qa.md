# Viva / Demo Q&A Prep

### Q: What is a hypervisor, and which type did you use?
A hypervisor is software that creates and manages virtual machines, allowing
multiple guest operating systems to run on shared physical hardware. We used
**VirtualBox**, a **Type-2 (hosted) hypervisor** — it runs as an application on
top of our host OS (Windows 11), unlike a Type-1 (bare-metal) hypervisor like Xen
or VMware ESXi, which runs directly on hardware with no host OS underneath.

### Q: Why Type-2 and not Type-1 for this task?
Type-1 hypervisors need to be installed directly on hardware with no OS in the
way — that requires either dedicated server hardware or booting the machine into
the hypervisor itself. Since we needed 3 VMs on a single laptop we still use
normally for everything else, a Type-2 hypervisor running as a regular Windows
application was the practical choice.

### Q: What's the difference between full virtualization and para-virtualization?
Full virtualization (what VirtualBox uses, hardware-assisted via Intel VT-x) runs
an unmodified guest OS — the guest has no idea it's virtualized. Para-virtualization
(used by Xen/KVM) modifies the guest kernel to issue "hypercalls" directly to the
hypervisor instead of executing certain privileged instructions normally, which
can improve performance but requires a modified guest OS.

### Q: Explain your network setup — why two adapters per VM?
Each VM has a NAT adapter (Adapter 1) purely for outbound internet access to run
`apt update`/`apt install` — VMs aren't reachable from outside through this. The
second adapter is a Host-only Adapter, which creates a private virtual network
(192.168.56.0/24) shared by the host and all VMs. This is what lets the VMs
actually talk to each other directly — without it, they'd be 3 isolated machines
that each just happen to have internet access.

### Q: What's the difference between NAT, Host-only, Bridged, and Internal
networking in VirtualBox?
- **NAT**: outbound-only internet access, VM is hidden behind the host, not
  reachable by other VMs.
- **Host-only**: private network between the host and VMs only — no route to the
  outside internet through this adapter. This is what we used for VM-to-VM
  communication.
- **Bridged**: VM appears as its own device directly on the physical LAN, gets an
  IP from the same router as the host — not used here since it would expose the
  VMs to the whole home/college network.
- **Internal**: like Host-only, but the *host itself* cannot access it either —
  only VMs can talk to each other. We used Host-only instead specifically so we
  could also test/access from the host machine.

### Q: How did you prove the VMs are actually networked, not just running
independently?
By running `ping 192.168.56.103` from VM2 and VM3 to VM1's host-only IP and
getting real replies, and then loading VM1's Apache webpage from VM2/VM3's
browsers using that same IP — proving an actual HTTP request crossed from one VM
to another over the virtual network.

### Q: What role does each VM play?
VM1 runs Apache2 and serves a web page — it's the service provider. VM2 and VM3
are client machines that consume that service over the network — demonstrating a
basic server/client pattern within the virtualized environment.

### Q: What resource constraints did you encounter, and how did you solve them?
Our host has 12GB RAM, and background software (antivirus, GPU drivers,
manufacturer utilities) already used a meaningful share of it at idle. Running a
VM install under memory pressure caused kernel "soft lockup" warnings during
package installation. We resolved it by closing unnecessary background
applications to free RAM headroom, and by deliberately keeping each VM's
allocated RAM to 1GB so all three could run together comfortably.

### Q: How does this relate to what you did for Task 1 (AWS deployment)?
Task 1 used real cloud infrastructure — AWS EC2, which itself runs on a Type-1
hypervisor (historically Xen, increasingly AWS's own Nitro/KVM-based hypervisor).
Task 2 demonstrates the same underlying virtualization concept — multiple
isolated OS instances sharing one physical machine's resources — just locally,
using a Type-2 hypervisor instead of a cloud provider's Type-1 infrastructure.

### Q: Could this scale beyond 3 VMs?
Yes — limited only by host RAM/CPU/disk. Each additional VM needs its own
allocation of RAM and a host-only adapter added to the same network to join the
existing private network.
