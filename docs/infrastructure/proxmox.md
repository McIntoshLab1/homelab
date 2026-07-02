<h1> Proxmox VE </h1
                  
 **Status:** Completed

<h3>  Overview </h3>
Proxmox VE 9.1 is the hypervisor at the foundation of the Tosh
Systems lab. It runs on the UGREEN DXP4800 Plus and hosts all
virtual machines and LXC containers in the environment. Every
other service in the lab runs on top of this layer.

<h3>  Purpose </h3>
Provides the virtualization platform for the entire lab —
creating, running, and managing VMs and containers from a single
web interface. Proxmox is the base that Terraform provisions
against and that all services (Pi-hole, monitoring, Docker hosts)
ultimately run on.

## Why This Choice
| Tool/Function | Decision | Alternative | Reason |
|----------|--------|------|--------|
| Hypervisor | Proxmox VE | VMware ESXi | ESXi free tier killed by Broadcom in 2024 |
| Hypervisor | Proxmox VE | Hyper-V | Would require Windows Server licensing on Linux-native hardware |
| Boot drive | Crucial P310 NVMe | HDD bays | Keeps OS fast and separate and leaves all 4 bays free for storage |

Proxmox is open-source, KVM-based, and the platform enterprises
are actively migrating to post-VMware — making it both the
practical and the most resume-relevant choice.

---

<h1>Architecture </h1> 


 ## Configuration 
| Setting | Value |
|---------|-------|
| Version | Proxmox VE 9.1 |
| Boot / OS drive | Crucial P310 500GB NVMe (M.2) |
| Management interface | nic0 (Atlantic) |
| Physical port | LAN 1 (maps to nic0) |
| Management IP | [redacted] |
| Web UI | https://<redacted-static-ip>:8006 |
| Repositories | No subscription (enterprise repo disabled) |
| HDD bays | Reserved for future storage |



## Access
- **Local:** Web UI at `https://<redacted-static-ip>:8006` on the
  LAN
- **Remote:** Reachable over Tailscale — the Proxmox node is
  joined to the private mesh, so the web UI and SSH are available
  from any network without exposing any ports publicly.

 ## Enterprise Equivalent 
| Homelab | Enterprise |
|---------|-----------|
| Proxmox VE | VMware vSphere / Nutanix AHV / Hyper-V |
| Proxmox web UI | vCenter |
| LXC / VMs | Enterprise VM + container workloads |

## Related
- [Proxmox Setup — Case Study](../case-studies/proxmox-setup.md) — the build story and repo troubleshooting
- [Proxmox Backup](proxmox-backup.md) — backup strategy for this node
- [Tailscale](../networking/tailscale.md) — provides remote access to this node
