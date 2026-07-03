# Tosh Systems — Enterprise Homelab Infrastructure
 
This repository documents the infrastructure supporting a fictional 
organization called Tosh Systems.

The objective is to replicate enterprise IT environments using Microsoft 
365 cloud services, virtualization, containerization, networking, 
automation, and security — built and documented as if supporting a 
real organization.

>## ⚠️ **Security Notice**
> This repository contains sanitized documentation 
> and example configurations of my homelab environment.
> - No real credentials, API keys, or secrets are included
> - All domains, IP addresses, and identifiers are 
>   redacted or replaced with placeholders
> - Configuration files are provided as examples only
>
> This project is intended to demonstrate architecture, 
> security practices, and system design, not expose 
> a live environment.

---
 
## Hardware
- UGREEN DXP4800 Plus (Intel Pentium Gold 8505, 8GB DDR5)
- Crucial P310 500GB NVMe (hypervisor boot drive)
- 4x HDD bays (expanding for storage + redundancy)

## Stack
- Hypervisor: Proxmox VE
- Identity & Cloud: Microsoft 365 / Entra ID, Intune, Exchange Online (planned)
- IaC: Terraform (bpg/proxmox) + Ansible
- Containers: Docker + Docker Compose
- Networking: Tailscale (mesh VPN + subnet routing, PHASE 2: exit node + Tailscale SSH )
- Monitoring: Grafana + Prometheus + Uptime Kuma
- Documentation: BookStack (internal wiki)
- Web Access: Nginx Proxy Manager (reverse proxy + SSL)
- DNS: Pi-hole (internal DNS) & Cloudflare (external DNS + domain)

## Architecture


*System architecture - layers and services*

<a href="diagrams/architecture.png">
  <img src="diagrams/architecture.png" alt="Architecture Diagram" width="600">
</a>

<br><br>

*Network topology - physical devices and Tailscale overlay*

<a href="diagrams/network-diagram.png">
  <img src="diagrams/network-diagram.png" alt="Network Diagram" width="600">
</a>





> Diagrams are redacted to remove sensitive 
> network details. See docs/architecture/ for 
> full documentation.
>

## Services
| Service | Purpose | Status |
|---------|---------|--------|
| Microsoft Entra ID | Cloud identity + access | Planned |
| Microsoft Intune | Endpoint management | Planned |
| Exchange Online | Email | Planned |
| Proxmox VE | Hypervisor | Completed |
| Tailscale | Zero trust vpn | Active|
| BookStack | Internal documentation (wiki) | Planned |
| Nginx Proxy Manager | Reverse proxy + SSL | Phase 2 |
| Pi-hole | Internal DNS + Ad blocking | In Progress |
| Cloudflare | External DNS + Domain | Completed |
| Docker | Container runtime  | Planned |
| Grafana | Monitoring dashboard | Planned |
| Uptime Kuma | Service uptime monitoring | Planned |

## Security Approach
| Layer | Implementation |
|-------|---------------|
| Identity | Entra ID + Conditional Access + MFA (planned)| 
| Access Gateway | Cloudflare Access — Phase 2 (planned) |
| Network | Tailscale zero trust mesh |
| Proxy | Nginx Proxy Manager access lists |
| Application | Per service authentication |
| Secrets | .env files, never committed |
| DNS | Pi-hole internal, Cloudflare external |

See [docs/security/](docs/security/) for full details.

---

## Case Studies
| Study | Description | Status |
|-------|-------------|--------|
| [Terraform Provisioning](docs/case-studies/terraform-provisioning.md) | VM provisioning via Proxmox API (bpg/proxmox) | Planned |
| [Ansible Configuration](docs/case-studies/ansible-configuration.md) | Automated VM config (Docker, Tailscale, users) | Planned |
| [Entra ID Identity Design](docs/case-studies/entra-id-design.md) | Tenant setup, users, groups, roles, naming conventions | Planned |
| [Intune Endpoint Management](docs/case-studies/intune-endpoint.md) | Device enrollment, compliance, configuration profiles | Planned |
| [Conditional Access](docs/case-studies/conditional-access.md) | Zero trust access policies + MFA enforcement | Planned |
| [Microsoft 365 DNS](docs/case-studies/microsoft-365-dns.md) | Domain verification, Exchange, SPF/DKIM/DMARC via Cloudflare | Planned |
| [Proxmox Setup](docs/case-studies/proxmox-setup.md) | Hypervisor install + VM architecture | Completed |
| [Tailscale Access](docs/case-studies/tailscale-access.md) | Zero trust remote access implementation | Ongoing |
| [Reverse Proxy](docs/case-studies/reverse-proxy.md) | Nginx Proxy Manager + SSL setup | Planned |
| [DNS Architecture](docs/case-studies/dns.md) | Pi-hole + Cloudflare split DNS | Planned |
| [Monitoring Stack](docs/case-studies/monitoring.md) | Grafana +  Prometheus + Uptime Kuma deployment | Planned |
| [BookStack](docs/case-studies/bookstack.md) | Internal documentation platform behind reverse proxy | Planned |
| [Authentication](docs/case-studies/authentication.md) | Multi layer auth implementation | Planned |
| [Automation](docs/case-studies/automation.md) | Alerting and script automation | Planned |
| [Backups](docs/case-studies/backups.md) | Proxmox Backup Server setup | Planned |
  
