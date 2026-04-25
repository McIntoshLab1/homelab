# Homelab Infrastructure

## Overview 
Self-hosted infrastructure environment designed to simulate enterprise systems

## Hardware
- UGREEN DXP4800 Plus (Intel Pentium Gold 8505, 8GB DDR5)
- Crucial P310 500GB NVMe (hypervisor boot drive)
- 4x HDD bays (expanding for storage + redundancy)

## Stack
- Hypervisor: Proxmox VE
- Containers: Docker + K3s (lightweight Kubernetes)
- Networking: Tailscale (secure remote access)
- Monitoring: Grafana + Uptime Kuma
- Media Server: Jellyfin
- Web Access: Nginx Proxy Manager (reverse proxy + SSL)
- DNS: Pi-hole (internal DNS) & Cloudflare (external DNS + domain)


## Architecture
[ DIAGRAM COMING SOON] 

## Services
| Service | Purpose | Status |
|---------|---------|--------|
| Proxmox VE | Hypervisor | Planned |
| Tailscale | Zero trust vpn | Planned |
| Jellyfin | Media server | Planned |
| Nginx Proxy Manager | Reverse proxy + SSL | Planned |
| Pi-hole | Internal DNS + Ad blocking | Planned |
| Cloudflare | External DNS + Domain | Planned |
| Kubernetes: K3s | Container orchestration | Planned |
| Grafana | Monitoring dashboard | Planned |

## Case Studies
- [Monitoring stack with Grafana + Uptime Kuma](docs/monitoring.md)
- [Internal + external DNS architecture (Pi-hole + Cloudflare)](docs/dns.md)
- [Reverse proxy + SSL with Nginx Proxy Manager](docs/reverse-proxy.md)
- [Proxmox setup + VM architecture](docs/proxmox.md)
- [Tailscale zero-trust remote access](docs/tailscale.md)
- [Jellyfin deployment behind reverse proxy](docs/jellyfin.md)
  
