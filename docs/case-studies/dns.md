# Case Study: Internal DNS + Ad Blocking with Pi-hole

> **Status:** Completed

## Summary
The network needed a single point for DNS resolution and ad
blocking coverage that could eventually cover every device,
not just browsers running extensions. Pi-hole fills that role 
running as an LXC containeron Proxmox. Getting there involved two significant obstacles: 
a container networking bug that blocked the installation, and
once everything was working, a laptop that lost internet
access entirely at a second location. Troubleshooting that
second failure revealed the DNS design itself was the problem,
and the resolution moved DNS steering into Tailscale so it
works from any location.

## Purpose
Every device on the network needed DNS resolution, and ad
blocking had to extend beyond browsers for devices that 
can't run a browser extension. The answer had to live at the network
level, where a single resolver covers everything. The rollout is staged: 
client devices first, with router-level DNS for the rest of the LAN planned 
once the resolver has proven stable. What wasn't
obvious at the beginning was that this resolver would also need
to keep working when a device left the house, and that
requirement ended up reshaping the design.

## Why These Choices
| Tool/Function | Decision | Alternative | Reason |
|----------|--------|------|--------|
| Ad blocking | Pi-hole | Browser extensions | Covers every device including those that can't run extensions |
| Deployment | LXC container | Full VM | A single lightweight service doesn't justify a VM's overhead |
| Upstream DNS | Cloudflare (1.1.1.1) | ISP resolver | Faster response times and a stronger privacy posture |
| Install method | Unattended (setupVars) | Interactive wizard | The wizard's dialogs failed to render reliably; unattended installation is deterministic |
| Client DNS target | Pi-hole's Tailscale IP, set tailnet-wide | Pi-hole's LAN IP, set per-device | A LAN address is only reachable on the home network |

## Implementation
The build started with an Ubuntu 24.04 container with a static
IP and 1 GB of RAM, and almost immediately ran into the
networking bug covered below. Once that was resolved, Pi-hole
was installed unattended with a pre-seeded `setupVars.conf`,
followed by `pihole -g` to pull down the blocklist of roughly
78,000 ad and tracker domains. The remaining steps were
operational: setting an admin password and enabling "Start at
boot," since a DNS server that doesn't survive a reboot takes
the network's name resolution down with it.

With a Mac pointed at the container and resolution confirmed at
roughly 5 ms, the deployment appeared complete. A later failure
at a second location proved otherwise.

## Challenges & Troubleshooting

### Package installation hung inside the container
The first problem appeared before Pi-hole was even involved:
`apt update` inside the container hung indefinitely, with no
error and no progress — the container appeared to have no
internet access at all.

Two pings isolated the failure. `ping 1.1.1.1` — a raw IP
requiring no name lookup, succeeded. While `ping google.com`
returned "Temporary failure in name resolution." Together,
those results showed the network itself was functional and only
name resolution was broken, which pointed directly at the
container's DNS configuration.

The container had been created with a static IP but no
nameserver. A static configuration bypasses DHCP, which is 
what normally supplies a DNS server, so the container never 
received one. As a result, apt was hanging on its first action: 
resolving the repository's hostname. The fix was two commands
from the Proxmox host:

```bash
pct set 100 --nameserver 1.1.1.1
pct reboot 100
```

The same `apt update` that had hung indefinitely completed in
about two seconds.

### The installer's dialogs failed to render
With networking fixed, the next obstacle was the installer
itself. Pi-hole's interactive setup draws text-based dialog
screens, and in this environment they rendered as blank blue
screens and installer exiting at the static-IP notice on
every attempt. Rather than continue debugging the terminal, the
solution was to bypass the interactive layer entirely:
pre-seeding every answer in `setupVars.conf` and running the
installer with `--unattended`. With no dialogs to render, the
installation completed.

### `pihole: command not found` on a working install
One final issue surfaced after installation: running `pihole`
returned "command not found," even though the binary existed at
`/usr/local/bin/pihole`. The session's PATH did not include
`/usr/local/bin`, so the shell never searched there. Adding the
directory to PATH resolved the command, and Pi-hole reported
healthy: FTL listening on port 53, blocking enabled.

### No internet access at a second location
Everything worked at home and continued working until the
laptop left the network. At a the second location, the Mac appeared
completely offline: no pages loaded and nothing resolved.

The same layered diagnostic settled it again. `ping 1.1.1.1`
succeeded, confirming connectivity was intact and DNS was the
failing layer. The cause became clear on inspection: the Mac's
DNS had been pointed at Pi-hole's **LAN IP** — an address that
only exists on the home network. From any other location, every
lookup timed out against an unreachable server, making the
entire internet appear down.

Restoring access was straightforward: removing the Pi-hole
entry from the Mac's DNS settings (System Settings → Wi-Fi →
DNS) and letting it fall back to the network's default DNS
brought the internet back immediately.

That was a temporary measure, not a fix, and it would have meant
manually switching DNS every time the laptop changed networks.
The underlying issue was the design itself: a location-dependent
address had been assigned to a device that moves.

The durable fix was to relocate the DNS steering. Instead of
pointing the Mac at Pi-hole's LAN address, the configuration now
uses Pi-hole's **Tailscale** address set once at the tailnet
level rather than per machine. In the Tailscale admin console,
Pi-hole's 100.x address was added as the global nameserver with
"Override local DNS" enabled, and the Mac's own DNS settings
returned to automatic.

The result is DNS that travels with the device. At home, queries
take a short hop to a container on the same LAN; at the second
location or any other network, the same resolver answers over the
tailnet with nothing to reconfigure. If the node is ever
unreachable, toggling Tailscale off falls back to DHCP DNS.

## Outcome
Pi-hole now serves DNS for the tailnet (currently the mac), blocking roughly 78,000
ads tracker domains and resolving queries in about 5 ms. It
survives host reboots, works identically from any location
without per-device configuration, and has a single-toggle
fallback if the node is ever unreachable. Extending coverage to the rest of the LAN (TVs,
phones, IoT devices) by pointing the router's DHCP DNS at
Pi-hole is the planned next step and is currently deferred until the resolver
has proven stable enough for the whole network to depend on.

## Design Note — Single Point of Failure
Making Pi-hole the tailnet's only nameserver carries a
trade-off: if Pi-hole or the node goes down, every device on
the tailnet loses DNS with it.

The intuitive fix was adding 1.1.1.1 as a second global
nameserver which doesn't behave as expected. Tailscale treats
multiple global nameservers as a pool and load-balances across
them rather than performing strict primary/secondary failover,
meaning a portion of queries would bypass Pi-hole and quietly
weaken the ad blocking. A proper solution could be second Pi-hole or 
a dnsmasq instance on the tailnet which is planned as a future build.

## Skills Demonstrated
- LXC deployment and configuration
- Isolating failures by layer (connectivity vs. resolution)
- Linux DNS and resolver configuration
- Unattended, non-interactive installation
- Diagnosing a location-dependent failure and redesigning for
  mobility
- DNS integration with a zero-trust overlay network
- Evaluating single points of failure and availability trade-offs

## Enterprise Relevance
Internal DNS and DNS-layer filtering are standard corporate
controls. The failure documented here is a roaming device
pointed at an internal-only resolver and it's the same problem
enterprises solve with split-tunnel VPNs and cloud-delivered DNS
platforms such as Cisco Umbrella. The single-point-of-failure
analysis reflects the availability planning expected around any
production resolver: what breaks, who is affected, and what the
fallback is.
