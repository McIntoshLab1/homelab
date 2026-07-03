# Tailscale

> **Status:** Active

## Overview
Tailscale is the private access layer of the Tosh Systems lab.
It creates an encrypted mesh VPN connecting trusted devices to
the Proxmox node, providing full remote management from any
network — with zero services exposed to the public internet.

## Purpose
Enables secure remote access to the entire lab from anywhere
(any network, any location) while keeping the environment fully
private. Tailscale is the reason the lab can be both "nothing
exposed publicly" and "manageable from anywhere" at the same
time.

## Why This Choice
| Tool/Function | Decision | Alternative | Reason |
|----------|--------|------|--------|
| Remote access | Tailscale mesh | Port forwarding | No open ports, no public attack surface |
| Remote access | Tailscale mesh | Traditional VPN server | No VPN server to host, patch, or expose |
| Deployment | Host-level install | Docker sidecar | Whole node on mesh covers all services; sidecar only needed for per-container identity |

Tailscale is built on WireGuard and uses a coordination server
only for key exchange — traffic flows peer-to-peer between
devices where possible.

Each enrolled device receives a stable Tailscale address which allows me to
wors from anywhere and it never conflict with the home LAN.

## Configuration
| Setting | Value |
|---------|-------|
| Install method | Official install script (curl) |
| Deployment | Host-level on the Proxmox node |
| Enrolled devices | Proxmox node, Laptop (management), mobile |
| Addressing | Tailscale-assigned 100.x.x.x (redacted) |
| Exposed ports | No inbound ports opened |

## Access
All remote management flows over the mesh:
- Proxmox web UI via the node's Tailscale address
- SSH to the node over the mesh
- No port forwarding, no dynamic DNS, no public exposure

## Enterprise Equivalent
| Homelab | Enterprise |
|---------|-----------|
| Tailscale mesh | ZTNA (Zscaler Private Access, Cloudflare Access) |
| Device enrollment | Managed device trust |
| No exposed ports | Zero-trust network posture |

## Related
- [Tailscale Access — Case Study](../case-studies/tailscale-access.md)
- [Proxmox](../infrastructure/proxmox.md) — the node this provides access to
