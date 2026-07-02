# Architecture Overview

**Status:** Active

## Overview
Tosh Systems is a homelab built to replicate an enterprise IT
environment: cloud identity, virtualization, containers,
networking, automation, monitoring, and security, are included to simulate real world situations.
This page is the top-level map of how those pieces fit together and where each one currently
stands.

## Layers
The environment is organized as a stack, each layer building on
the one below it.

| Layer | Components | Status |
|-------|-----------|--------|
| Hardware | UGREEN DXP4800 Plus, Crucial P310 NVMe (boot), 4× HDD bays | Base drive done; bays reserved |
| Hypervisor | Proxmox VE 9.1 | Completed |
| Identity & Cloud | Microsoft 365 / Entra ID, Intune, Exchange Online | Planned |
| Infrastructure as Code | Terraform (bpg/proxmox) + Ansible | Planned |
| Containers | Docker + Docker Compose | Planned |
| Networking | Tailscale (remote), Pi-hole (internal DNS), Cloudflare (external DNS) | Tailscale active · Pi-hole in progress · Cloudflare done |
| Web Access | Nginx Proxy Manager (reverse proxy + SSL) | Phase 2 |
| Monitoring | Grafana + Prometheus + Uptime Kuma | Planned |
| Documentation | BookStack (internal wiki) | Planned |

## How It Fits Together
Proxmox is the foundation and every VM and container runs on it, and
it is the target Terraform provisions against. Ansible then
configures those VMs (Docker, Tailscale, users). Networking wraps
the whole thing, Tailscale provides zero-trust remote access,
Pi-hole resolves internal names and blocks ads, and Cloudflare
handles public DNS and the domain. On top of the container layer
sit the services: monitoring (Grafana/Prometheus/Uptime Kuma),
documentation (BookStack), and web access via Nginx Proxy Manager.
Identity is the parallel cloud track: Microsoft 365 / Entra ID for
users, groups, and access, with Intune and Exchange to follow.

## Two Tracks
The build runs on two largely independent tracks:

- **On-prem / homelab**:  Proxmox > Terraform > Ansible > Docker >
  services, wrapped in Tailscale + Pi-hole + Cloudflare networking.
- **Cloud identity**: Microsoft 365 / Entra ID → Intune →
  Conditional Access → Exchange, wired to `toshsystems.com` via
  Cloudflare DNS.

They meet at the domain (`toshsystems.com`) and at the security
model that spans both.

## Related
- [Network topology](network.md)
- [Storage](storage.md)
- [Security model](security-model.md)
- [Proxmox](../infrastructure/proxmox.md) — the hypervisor foundation
- Full service + case-study index in the repo [README](../../README.md)
