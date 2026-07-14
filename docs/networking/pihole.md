# Pi-hole

> **Status:** Active

## Overview
Pi-hole is the internal DNS server and ad blocker for the Tosh
Systems lab, running as an LXC container on the Proxmox node. It
resolves DNS for devices pointed at it and filters known ad and
tracker domains before they ever load.

## Purpose
Pi-hole provides two functions for the network. It answers DNS
queries for lab devices, and it refuses to resolve known ad and
tracking domains, removing them at the network level rather
than per device. Anything it can't answer locally is forwarded
upstream to Cloudflare.

## Why This Choice
Pi-hole was chosen over browser extensions because extensions
only cover browsers. A TV or a phone app can't run one, while a
network-level resolver covers every device. It runs as an LXC
container rather than a full VM because a single lightweight
service doesn't justify a VM's overhead. Cloudflare (1.1.1.1)
serves as the upstream resolver over the ISP's default for its
faster response times and stronger privacy practices around
query data.

## Architecture
```
Device → Pi-hole (LXC, internal DNS)
              ↓
      known ad/tracker? → blocked (no answer)
              ↓
      everything else → forwarded upstream to Cloudflare 1.1.1.1
```
DNS steering for mobile devices happens at the Tailscale layer.
Pi-hole's tailnet address is set as the global nameserver, so
enrolled devices resolve through it from any location. See the
[DNS case study](../case-studies/dns.md) for how that design
came about.

## Configuration
| Setting | Value |
|---------|-------|
| Container ID | 100 (LXC, unprivileged) |
| Base OS | Ubuntu 24.04 |
| RAM | 1 GB |
| Static IP | <redacted-static-ip> |
| Upstream DNS | 1.1.1.1 / 1.0.0.1 (Cloudflare) |
| Blocklist | StevenBlack default (~78,000 domains) |
| Web interface | http://<redacted-static-ip>/admin |
| Start at boot | Enabled |

## Access
The admin dashboard is reachable on the LAN and over Tailscale,
with query logging enabled for visibility into DNS activity.
Nothing is exposed publicly.

## Enterprise Equivalent
Pi-hole plays the role that internal DNS zones and DNS-layer
filtering platforms like Cisco Umbrella play in a corporate
environment: resolution for internal clients, with a security
policy applied at the DNS layer. Its upstream forwarding to
Cloudflare mirrors how enterprise resolvers use conditional
forwarders for queries they can't answer locally.

## Related
- [DNS Case Study](../case-studies/dns.md) covers the build
  story, including the nameserver troubleshooting and the
  location-independent redesign
- [Tailscale](tailscale.md) carries the DNS steering for
  mobile devices
- [Cloudflare](cloudflare.md) is the upstream resolver
