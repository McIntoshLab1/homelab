
# Cloudflare — External DNS & Domain

**Status:** Completed

## Overview
Cloudflare is the external-facing DNS and domain layer for the
Tosh Systems environment. It holds the authoritative public DNS
zone for `toshsystems.com` and serves as the registrar/nameserver
side of the split-DNS design — everything the outside world (and
Microsoft 365) resolves for the domain comes from here.

## Purpose
Provides public name resolution for `toshsystems.com` and the DNS
records that connect the domain to Microsoft 365 (domain
verification and, once Exchange Online is live, mail-flow records).
It is the external half of the lab's split DNS: Cloudflare answers
public queries, while Pi-hole handles internal resolution on the
LAN.

## Why This Choice
| Tool/Function | Decision | Alternative | Reason |
|------|----------|-------------|--------|
| External DNS | Cloudflare | Registrar default DNS | Fast global anycast DNS, free tier, granular record control |
| Access gateway (roadmap) | Cloudflare Access / Tunnel | Port-forwarding | Publishes services with zero open inbound ports |

## Architecture
Cloudflare sits at the public edge. It is the authoritative
nameserver for `toshsystems.com` and the point where the public
domain meets the Microsoft 365 tenant. Internal LAN resolution
never touches Cloudflare, that is Pi-hole's job - so the two DNS
systems stay cleanly separated by scope (public vs internal).

Public queries → Cloudflare (authoritative zone)
Internal queries → Pi-hole (LAN resolver)

## Configuration
| Setting | Value |
|---------|-------|
| Domain | toshsystems.com |
| Role | Authoritative external DNS + domain |
| Zone records | Managed in Cloudflare dashboard |
| M365 wiring | Domain verification + mail records (see cloudflare-dns.md) |
| Cloudflare Access | Phase 2 (planned) |
| Cloudflare Tunnel | Phase 2 — public publishing (planned) |

The Cloudflare Tunnel token is stored only in the local `.env`
(`CLOUDFLARE_TUNNEL_TOKEN`) and is never committed — see
[secrets management](../security/secrets-management.md).

## Access
The Cloudflare zone is managed through the Cloudflare dashboard
under the account owner. No public inbound ports are opened on the
lab; when the Access/Tunnel layer lands in Phase 2, services will
be published through Cloudflare rather than by exposing the network
directly.

## Enterprise Equivalent
| Homelab | Enterprise |
|---------|-----------|
| Cloudflare external DNS | Managed authoritative DNS (Route 53, Azure DNS, NS1) |
| Cloudflare Access (planned) | ZTNA / identity-aware proxy |
| Cloudflare Tunnel (planned) | Reverse-proxy / WAF publishing edge |

## Related
- [Cloudflare DNS records](../domain/cloudflare-dns.md) — the zone records and M365 wiring
- [Cloudflare Access](cloudflare-access.md) — Phase 2 access gateway
- [Pi-hole](pihole.md) — internal DNS (the other half of split DNS)
- [Microsoft 365 DNS — Case Study](../case-studies/microsoft-365-dns.md)
