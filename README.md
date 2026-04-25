# Homelab Infrastructure

## Overview 
Private cloud infrastructure running on UGREEN NAS, built for learning enterprise virtualization, container orchestration, and self-hosted services.

## Hardware
- UGREEN DXP4800 Plus (Intel Pentium Gold 8505, 8GB DDR5)
- Crucial P310 500GB NVMe (Proxmox boot drive)
- 4x HDD bays (currently expanding)

## Stack
- Hypervisor: Proxmox VE
- Containers: Docker + kubernetes: K3s
- Networking: Tailscale (zero trust)
- Monitoring: Grafana + Uptime Kuma
- Media: Jellyfin
- DNS: Pi-hole

## Architecture
[ DIAGRAM COMING SOON] 

## Services
| Service | Purpose | Status |
|---------|---------|--------|
|  Promox | Hypervisor | Not Started |
| Tailscale | Zero trust vpn | Not Started |
| Jellyfin | Media server | Not Started |
| Pi-hole | Network DNS/Adblock | Not started |
| Kubernetes: K3s | Container orchestration | Not Started |
| Grafana | Monitoring dashboard | Not Started |

## Case Studies
- [Promox Setup](docs/proxmox.md)
- [Tailscale Zero Trust](docs/tailscale.md)
- [Jellyfin Media Server](docs/jellyfin.md)
