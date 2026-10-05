# Proxmox VE

The hypervisor of my homelab. One small office PC runs Proxmox VE,
and every service in the lab lives in its own LXC container on it.

## What runs on it

```
HP EliteDesk 800 G4 Mini  (Proxmox VE 9)
└── vmbr0  (Linux bridge = virtual switch, connected to the physical network port)
    ├── 100  tailscale            remote access
    ├── 101  uptimekuma           monitoring
    └── 102  nginxproxymanager    reverse proxy
```

All three are LXC containers. There are no full virtual machines at the moment.

## At a glance

| Topic          | My setup                                                      |
|----------------|---------------------------------------------------------------|
| Hardware       | HP EliteDesk 800 G4 Mini: Intel Core i5-8500 (6 cores), 16 GB RAM, 256 GB NVMe SSD |
| Version        | Proxmox VE 9.2 (based on Debian 13 "Trixie")                  |
| Setup          | Single node, no cluster                                       |
| Web UI         | `https://<proxmox-ip>:8006`                                   |
| Guests         | 3 LXC containers, all start automatically at boot             |
| Backups        | Daily job for all containers, stored on the same disk (see [Backups](06-backups.md)) |

## Why Proxmox

- **VMs and containers in one place.** One web interface for full virtual machines and lightweight LXC containers.
- **Free and open source.** Every feature works without a subscription. The subscription only adds the
  enterprise update repository and official support.

## Documentation

| #  | File                                                      | What's inside                                            |
|----|-----------------------------------------------------------|----------------------------------------------------------|
| 1  | [Installation](01-installation.md)                        | USB stick, installer choices, first login                |
| 2  | [Storage](02-storage.md)                                  | `local` vs. `local-lvm`, what goes where, thin provisioning |
| 3  | [Containers](03-containers.md)                            | LXC vs. VM, my containers and their settings explained    |
| 4  | [Updates & repositories](04-updates-and-repositories.md)  | Enterprise vs. no-subscription, the update error I found  |
| 5  | [Limitations & next steps](05-limitations-and-next-steps.md) | Backups on the same disk, one disk, one node          |
| 6  | [Backups](06-backups.md)                                  | Backup job settings, mail fix, restore how-to, limits    |

Placeholders like `<proxmox-ip>` stand for addresses in your own network.
