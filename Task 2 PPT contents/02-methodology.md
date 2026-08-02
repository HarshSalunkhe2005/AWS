# Methodology

## 1. Environment setup
- **Host**: Windows 11 laptop, Intel i5-12450HX (8 cores / 12 threads), 12GB RAM.
- **Hypervisor**: Oracle VirtualBox installed on the host, with the VirtualBox
  Extension Pack added.
- Hardware virtualization (Intel VT-x) confirmed enabled in firmware before starting
  (checked via Task Manager → Performance → CPU → "Virtualization: Enabled").

## 2. VM provisioning
Three VMs were created, each with:
- **OS**: Ubuntu Server (installed via the official ISO, using the guided/unattended
  installer)
- **RAM**: 1024 MB per VM (kept low deliberately — see Resource Constraints below)
- **vCPUs**: 1 per VM
- **Disk**: 20 GB, dynamically allocated

| VM | Role |
|---|---|
| VM1 (`rrafin`) | Web server |
| VM2 (`ubuntu2`) | Client |
| VM3 (`ubuntu2 Clone`) | Client (created by cloning VM2, with a fresh network MAC address to avoid conflicts) |

## 3. Network configuration
Each VM was given **two virtual network adapters**:
- **Adapter 1 — NAT**: routes outbound traffic through the host's internet
  connection, used for `apt` package installation/updates. VMs are not directly
  reachable from outside via this adapter.
- **Adapter 2 — Host-only Adapter**: places all 3 VMs (and the host itself) on a
  shared private virtual network, `192.168.56.0/24`, with IPs assigned via
  VirtualBox's internal DHCP. This is what makes the environment an actual
  *networked multi-VM system* rather than 3 isolated machines.

## 4. Service deployment
On VM1:
```bash
sudo apt update
sudo apt install -y apache2
sudo systemctl status apache2   # confirmed: active (running)
```
The default Apache page was replaced with a custom `index.html` at
`/var/www/html/index.html`, displaying the project title and VM1's IP address —
so the page itself doubles as visible proof of which machine is serving it.

## 5. Connectivity testing
From both VM2 and VM3:
```bash
ping 192.168.56.103        # VM1's host-only IP — confirmed successful replies
```
Then, from each VM's browser:
```
http://192.168.56.103
```
confirmed to load VM1's custom page from a separate VM over the private network —
the core proof that this is a genuinely networked multi-instance environment.

## Resource constraints and how they were handled
The host's 12GB RAM (with meaningful background usage from OS/driver/security
software) meant memory, not CPU, was the binding constraint. Each VM was kept to
1GB RAM to allow all 3 to run simultaneously with headroom to spare, and VMs not
actively being demonstrated were shut down during setup/testing rather than left
running continuously.
