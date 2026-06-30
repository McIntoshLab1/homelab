#Case Study: Proxmox VE Installation on UGREEN DXP4800 Plus


#Status: Completed. Proxmox VE 9.1 running on the Crucial
P310 NVMe, reachable over the network and Tailscale.



#Objective

Install Proxmox VE as the hypervisor foundation for the Tosh
Systems lab on a UGREEN DXP4800 Plus, using a Crucial P310 500GB
NVMe as the boot/OS drive while leaving the HDD bays free for
future storage. Establish remote management before moving on to
containers and services.

#Environment


* Host: UGREEN DXP4800 Plus (Intel Pentium Gold 8505, 8GB DDR5)
* Boot drive: Crucial P310 500GB NVMe (M.2 slot)
* Hypervisor: Proxmox VE 9.1
* Network: Verizon router (192.168.1.x), UniFi Switch Lite 8 PoE
* Installer: USB flashed from macOS


#Outcome

Proxmox VE 9.1 installed to the P310, web UI reachable at
https://<static-ip>:8006, node joined to Tailscale for remote
management. Three significant issues were diagnosed and resolved
during the build which are documented below.


Issue 1 — USB Installer Would Not Boot

#Problem

The DXP4800 Plus refused to boot from the USB installer. The
BIOS could not be reached with the usual keys, and the USB never
appeared in any boot menu — the system booted straight into the
stock UGREEN OS (GRUB).

#Diagnosis

* Standard BIOS keys (DEL, F2, F11, ESC) did nothing
* Inspected drives from the GRUB command line (ls) — USB
partitions were unreadable. 
* First, the USB (SanDisk Ultra Slider 64GB) failed to reformat with
error -69772 — firmware-level write protection, no physical switch to override 
* balenaEtcher and UNetbootin both failed (UNetbootin additionally blocked by macOS Gatekeeper)


#Resolution


* Switched to a second USB drive and flashed the ISO directly with dd, using the exact filename:

sudo dd if=/Users/$(whoami)/Downloads/proxmox-ve_9.1-1.iso \of=/dev/rdiskN  bs=1m status=progress

[Replace rdiskN with your actual USB device number (verify
with diskutil list).]


* Discovered BIOS access on this hardware is Ctrl+F2, not
DEL or F2 alone
* Set Boot Option #1 to USB Hard Disk: UEFI: USB, Partition 2
in the AMI/Aptio BIOS, saved with F10


#Root Cause

Two compounding issues: the first USB had firmware write
protection with no override, and the DXP4800's BIOS uses a
non-standard Ctrl+F2 shortcut. The initial dd flash also
failed because the filename didn't match exactly.

#Takeaway

Hardware-specific BIOS shortcuts and firmware-level USB write
protection are easy to misread as "the installer is broken."
Verifying the flash actually wrote (exact filename, correct
device) and confirming the vendor's BIOS key saved hours of
chasing the wrong problem.


#Issue 2 — Web UI Unreachable After Install

#Problem

Install completed, but https://<static-ip>:8006 would not load,
and the Proxmox node was not visible on the UniFi switch.

#Diagnosis


* Confirmed the management Mac was on the same 192.168.1.x network
* Confirmed the static IP from the Proxmox login console
* Checked the UniFi switch — the Proxmox port showed no active link
* Reviewed the install config: nic0 (Atlantic) had been selected
as the management interface


#Root Cause

The ethernet cable was plugged into LAN 2, which maps to
nic1 (igc) — but nic0 (Atlantic) was selected during install.
Interfaces are zero-indexed (LAN 1 = nic0, LAN 2 = nic1), so
Proxmox was broadcasting management on a port with no cable in it.

#Resolution

Moved the cable from LAN 2 to LAN 1 to match the nic0
interface chosen at install. The web UI became reachable
immediately and the node appeared on the switch.

#Takeaway

A working install can still be unreachable purely from a
physical/interface mismatch. Mapping the selected NIC to the
correct physical port (and remembering zero-based numbering) is
a fast root cause once you stop assuming the network config is
wrong.


#Issue 3 — Enterprise Repository Warnings

#Problem

Proxmox shipped with the enterprise (paid-subscription) APT
repositories enabled and producing update errors on a no-subscription
homelab node.

#Resolution

*Disabled the enterprise repos. On Proxmox VE 9.x these use the
newer .sources (deb822) format rather than the old .list
files, so the change was applied against the .sources file
rather than commenting out a .list line.

#Takeaway

Repo handling changed format between Proxmox versions; applying
the old .list fix would have silently done nothing. Matching
the fix to the actual file format in use is the detail that
matters.


#Post-Install


* Tailscale installed on the Proxmox node, joining it to the
* private mesh for remote management from any network
* Node confirmed reachable both on-LAN and over Tailscale
* First LXC container (Pi-hole) created on Ubuntu 24.04 with a static IP as the next build step.


#Skills Demonstrated

* Hypervisor installation
* Low-level USB imaging (dd) and boot troubleshooting
* BIOS/UEFI boot configuration on non-standard hardware
* Network interface diagnosis (NIC-to-port mapping)
* Linux package repository management
* Remote management via zero-trust networking (Tailscale)


#Enterprise Relevance

Creating a hypervistor , diagnosing boot and
network-interface issues, and establishing secure remote
management mirror the foundational tasks of provisioning and
trouble shooting enterprise environments.
