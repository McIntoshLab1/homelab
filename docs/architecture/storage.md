# Storage

**Status:** Boot/OS drive in service · HDD bays reserved (planned)

## Overview
Storage in the lab is split: a fast NVMe drive runs
the hypervisor and its workloads, while the UGREEN DXP4800 Plus's
four HDD bays are kept free for a future bulk-storage and
redundancy. This keeps the OS fast and isolated from data
storage.

## Current Layout
| Role | Device | Notes |
|------|--------|-------|
| Boot / OS | Crucial P310 500GB NVMe (M.2) | Runs Proxmox VE 9.1 and all VMs/containers |
| Bulk storage | 4× HDD bays | **Empty — reserved for future storage + redundancy** |

## Why This Choice
| Decision | Alternative | Reason |
|----------|-------------|--------|
| NVMe boot drive | Install OS onto an HDD bay | Keeps the OS fast and separate; leaves all 4 bays free |
| Reserve bays for later | Populate now | Storage/redundancy tier is a planned phase, not yet built |

Putting Proxmox on the M.2 NVMe means none of the four drive bays
are consumed by the OS, so the entire bay capacity remains
available for a dedicated storage pool when that phase begins.

## Planned
The four HDD bays are earmarked for a redundant bulk-storage pool
(expansion for storage + redundancy). Layout, filesystem, and
redundancy scheme are not yet decided and will be documented here
when built.

## Enterprise Equivalent
| Homelab | Enterprise |
|---------|-----------|
| NVMe OS drive + separate data bays | Boot/OS tier separated from data tier |
| Reserved redundant HDD pool | RAID / SAN / redundant storage array |

## Related
- [Proxmox](../infrastructure/proxmox.md) — runs from the P310 NVMe
- [Proxmox Backup](../infrastructure/proxmox-backup.md) — backup strategy (planned)
- [Architecture overview](overview.md)
