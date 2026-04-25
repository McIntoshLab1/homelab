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
- DNS: Pi-hole + Cloud Fare

## Architecture
[ DIAGRAM COMING SOON] 

## Services
| Service | Purpose | Status |
|---------|---------|--------|
|  Promox | Hypervisor | Planned |
| Tailscale | Zero trust vpn | Planned |
| Jellyfin | Media server | Planned |
| Nginx Proxy Manager | Reverse proxy + SSL | Planned |
| Pi-hole | Internal DNS + Ad blocking | Planned |
| Cloudflare | External DNS + Domain | Planned |
| Kubernetes: K3s | Container orchestration | Planned |
| Grafana | Monitoring dashboard | Planned |

## Case Studies
- [Promox Setup](docs/proxmox.md)
- [Tailscale Zero Trust](docs/tailscale.md)
- [Jellyfin Media Server](docs/jellyfin.md)
  
