# Homelab

My self-hosted lab for learning virtualization, Linux, networking and monitoring.
Everything here is built and documented by me as I learn. Mistakes and fixes included.

## What's running

| Service             | Runs on                   | Purpose                                     | Docs                                  |
|---------------------|---------------------------|---------------------------------------------|---------------------------------------|
| Proxmox VE          | HP EliteDesk 800 G4 Mini  | Hypervisor for VMs and LXC containers       | [proxmox](proxmox/)                   |
| Uptime Kuma         | LXC on Proxmox            | Monitoring with email alerts (SMTP)         | [uptime-kuma](uptime-kuma/)           |
| Tailscale           | LXC on Proxmox            | Remote access to the lab from anywhere      | Planned                               |
| Nginx Proxy Manager | LXC on Proxmox            | Reverse proxy: services by name, not IP:port | [nginx-proxy-manager](nginx-proxy-manager/) |
| n8n                 | Docker on my workstation  | Workflow automation                         | Planned                               |

**Paused:** Windows Server 2025 domain controller (Active Directory) as a VirtualBox VM on my workstation.

## Repository structure

| Folder / file                                  | What's inside                                              |
|------------------------------------------------|------------------------------------------------------------|
| [hardware.md](hardware.md)                     | All devices and their role                                 |
| [network/](network/)                           | Topology, address scheme, design decisions, troubleshooting |
| [proxmox/](proxmox/)                           | Hypervisor: installation, storage, containers, updates     |
| [nginx-proxy-manager/](nginx-proxy-manager/)   | Reverse proxy: installation, proxy hosts, name resolution  |
| [uptime-kuma/](uptime-kuma/)                   | Monitoring: installation, monitors, email alerts           |

Every folder works the same way: `README.md` is a short intro with an index,
the numbered files (`01-…`, `02-…`) are the actual docs in reading order.

## Roadmap

- [ ] Document Tailscale and n8n
- [ ] Second backup target on a different device (a daily backup job to the same disk already exists)
- [ ] Local DNS server (Pi-hole or AdGuard Home), so lab names work on every device
- [ ] Offline AI with Ollama on a spare EliteDesk
- [ ] ESP32 status panel: LEDs that light up when a host is reachable
