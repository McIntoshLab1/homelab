# Case Study: Zero-Trust Remote Access with Tailscale

> **Status:** Completed

## Summary
The lab needed to be reachable from outside the house without
opening a single port to the internet. Tailscale solves that by
joining the Proxmox node and trusted devices to an encrypted
mesh, so remote management works from any network while the
environment stays fully private. The deployment itself was
quick, with one troubleshooting detour, and the Tailscale layer
later became the backbone of the lab's DNS design as well.

## Purpose
The lab had to work from more than one location, including
networks the lab has no control over, without exposing the
Proxmox host publicly. The goal was making "fully private" and
"accessible from anywhere" true at the same time. Tailscale
became the layer that delivers both, and it has since taken on
a second role: carrying DNS steering so devices resolve through
the lab's Pi-hole from any location.

## Why These Choices
| Tool/Function | Decision | Alternative | Reason |
|----------|--------|------|--------|
| Remote access | Tailscale mesh | Port forwarding | Opening router ports exposes the home IP and creates a public attack surface |
| Remote access | Tailscale mesh | Self-hosted VPN (WireGuard/OpenVPN server) | No server to expose, patch, and maintain; same WireGuard encryption underneath |
| Deployment | Host-level on the Proxmox node | Tailscale-in-Docker sidecar | The whole node on the mesh covers every service; per-container identity wasn't needed for a single-host lab |

## Implementation
Tailscale went onto the Proxmox node first, using the official
install script:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

Bringing the node onto the mesh was a single command,
authenticated through a browser link from the management Mac:

```bash
tailscale up
```

The Mac and a mobile device were then enrolled as clients. Each
device received a stable address in Tailscale's 100.64.0.0/10
range, a second permanent identity that works from any network
and never collides with the home LAN's addressing. The node was
verified reachable both on the LAN and over the mesh from an
external network, covering the Proxmox web UI and SSH.

Later, the mesh took on DNS duty as well. Pi-hole's tailnet
address was set as the global nameserver in the Tailscale admin
console with "Override local DNS" enabled, so every enrolled
device resolves through Pi-hole from any location. The full
story behind that design lives in the
[DNS case study](dns.md).

## Challenges & Troubleshooting

### Blank status after initial authentication
After installing and authenticating, both `tailscale ip` and
`tailscale status` returned nothing at all. No address, no
peers, no error.

Checking the service with `systemctl status tailscaled`
clarified the situation. The `tailscale` command is only a
client that talks to the background daemon, `tailscaled`, and
blank output meant the daemon was running but had not properly
initialized after the fresh install. A restart resolved it:

```bash
systemctl restart tailscaled
tailscale up
```

After the restart, the node registered correctly and reported
its mesh address. The rest of the deployment was
straightforward, a fair reflection of how much operational
friction a managed mesh removes compared to running a VPN
server yourself.

## Outcome
The Proxmox web UI and SSH are reachable from any network
through the node's Tailscale address, with zero inbound ports
opened and the home IP never exposed. Management works
identically from the home LAN or an external network, and the
same mesh now carries DNS steering to Pi-hole for every
enrolled device. This access model is what allows every other
service in the lab to remain fully private.

## Skills Demonstrated
- Zero-trust network access (ZTNA) implementation
- Mesh VPN deployment (WireGuard-based)
- Linux service management with systemd
- CLI troubleshooting and root-cause isolation
- Secure remote administration design
- Tailnet-level DNS integration

## Enterprise Relevance
This mirrors the zero-trust access pattern used in corporate
and financial-services environments. Devices authenticate to a
trust fabric rather than a network perimeter, private resources
are never exposed publicly, and administration happens over an
encrypted overlay. The enterprise equivalents are ZTNA
platforms such as Zscaler Private Access and Cloudflare Access.
