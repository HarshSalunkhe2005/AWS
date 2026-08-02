# Title & Abstract

## Title
**Design and Implementation of a Multi-VM Virtualized Environment on a Single Host using Oracle VirtualBox**

## Abstract
This project demonstrates the design, implementation, and testing of a multi-VM
virtualized environment on a single physical host, satisfying the CA1 Task 2
requirement of a minimum 3-instance virtualized setup using a hypervisor platform.
Using Oracle VirtualBox — a Type-2 (hosted) hypervisor — three Ubuntu Server virtual
machines were provisioned on a single Windows 11 laptop. Each VM was configured with
two virtual network adapters: a NAT adapter for outbound internet access, and a
Host-only adapter placing all three VMs (and the host) on a shared private network
(192.168.56.0/24), enabling direct VM-to-VM communication independent of the internet.
One VM (VM1) was configured as a web server running Apache2, serving a custom HTML
page. The remaining two VMs (VM2, VM3) act as client machines on the same private
network, and both were verified — via `ping` and direct HTTP requests — to reach
VM1's web service over the host-only network, confirming a functioning multi-VM
networked environment on a single host.

## Course tie-in (Unit 2: Virtualization)
- **Hypervisor type**: VirtualBox is a **Type-2 (hosted) hypervisor** — it runs as
  an application on top of the host OS (Windows 11), unlike a Type-1 (bare-metal)
  hypervisor such as Xen, which runs directly on hardware with no host OS
  underneath (the model AWS EC2 itself uses, per Task 1).
- **Full virtualization**: VirtualBox uses hardware-assisted full virtualization
  (Intel VT-x on this host's CPU) — each guest OS runs unmodified and unaware it is
  virtualized, as opposed to para-virtualization (used by Xen/KVM), where the guest
  kernel is modified to issue hypercalls directly to the hypervisor.
- **Resource pooling**: the single host's CPU, RAM, and disk are partitioned and
  shared across all 3 guest VMs simultaneously — a direct, hands-on demonstration
  of the "resource pooling" characteristic covered in Unit 1.
