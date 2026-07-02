# Network Topology

**Status:** Physical LAN complete and Tailscale overlay active

## Overview
The network has two planes: a physical LAN behind the home router
and switch, and a Tailscale overlay mesh that provides secure
remote access without exposing any ports. Every lab device lives
on the physical LAN; management-critical nodes also join the
Tailscale mesh.

## Physical Layer
| Element | Detail |
|---------|--------|
| Router / gateway | Verizon router, `192.168.1.x` subnet |
| Switch | UniFi Switch Lite 8 PoE |
| Host | UGREEN DXP4800 Plus (Proxmox node) |
| Uplink | Proxmox management on nic0 → **LAN 1** |

Traffic flows: devices → UniFi Switch Lite 8 PoE → Verizon router
→ internet. The Proxmox host connects to the switch on the port
that maps to its selected management NIC.

### NIC-to-port mapping (important)
Interfaces are **zero-indexed**: `LAN 1 = nic0`, `LAN 2 = nic1`.
Proxmox management was configured on **nic0 (Atlantic)**, so the
cable must be in **LAN 1**. A mismatch here (cable in LAN 2 while
nic0 is selected) makes the node unreachable even on a working
install — this was a real issue during the Proxmox build.

## Overlay Layer — Tailscale
Tailscale provides a zero-trust WireGuard mesh over the top of the
physical network. Nodes joined to the mesh (e.g. the Proxmox
node) are reachable — web UI and SSH — from any network without
forwarding a single port on the Verizon router. Access between
devices is governed by Tailscale ACLs.

| ACL tier | Access |
|----------|--------|
| Admin devices | Full access to all services |
| Mobile devices | Limited service access |
| Guest devices | No access |

## Addressing & Redaction
The `192.168.1.x` subnet is shown because it is the router default
and carries no sensitive detail. Specific static IPs (e.g. the
Proxmox management address and web UI) are redacted in this repo
and kept in private notes and IP addresses are treated as secrets.

## Enterprise Equivalent
| Homelab | Enterprise |
|---------|-----------|
| Verizon router + UniFi switch | Core/edge routing + managed switching |
| Tailscale overlay | Corporate VPN / SD-WAN / ZTNA mesh |
| Tailscale ACLs | Firewall rules / NAC segmentation |

## Related
- [Tailscale](../networking/tailscale.md)
- [Pi-hole](../networking/pihole.md) — internal DNS on this LAN
- [Proxmox](../infrastructure/proxmox.md) — the host and its NIC mapping
- [Access control](../security/access-control.md) — the ACL tiers in full
