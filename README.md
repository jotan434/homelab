# Homelab

My self-hosted lab for learning virtualization, Linux, networking and monitoring.
Everything here is built and documented by me as I learn. Mistakes and fixes included.

## What's running

| Service             | Runs on                     | IP            | Purpose                                              |
|---------------------|-----------------------------|---------------|------------------------------------------------------|
| Proxmox VE          | HP EliteDesk 800 G4 Mini    | 192.168.0.222 | Hypervisor for VMs and LXC containers                |
| Uptime Kuma         | LXC on Proxmox              | 192.168.0.206 | Monitoring with email alerts (SMTP)                  |
| Tailscale           | LXC on Proxmox              | 192.168.0.212 | Remote access to the lab from anywhere               |
| Nginx Proxy Manager | LXC on Proxmox              | 192.168.0.214 | Reverse proxy: open services by name instead of IP:port |
| n8n                 | Docker on HP ProBook 640 G5 | –             | Workflow automation                                  |

**Paused:** Windows Server 2025 domain controller (DC01) as a VirtualBox VM on my workstation.

## Hardware

### Servers

| Device                      | Qty | Specs                               | Role                          |
|-----------------------------|-----|-------------------------------------|-------------------------------|
| HP EliteDesk 800 G4 Mini    | 5   | 8–16 GB RAM, 256 GB SSD             | 1× Proxmox host, 4× spare     |
| HP ProLiant MicroServer     | 1   | 8 GB RAM, 2× 1 TB HDD               | Spare (storage candidate)     |
| Dell OptiPlex 3060          | 1   | 16 GB RAM, 125 GB SSD               | Spare                         |
| Dell Wyse 5010 Thin Client  | 1   | 2 GB RAM, 8 GB mSATA SSD            | Spare                         |

### Workstations

| Device                | OS               | Specs                                     | Role                              |
|-----------------------|------------------|-------------------------------------------|-----------------------------------|
| HP ProBook 640 G5     | Linux Mint       | 32 GB RAM, 1 TB SSD                       | Main workstation, Docker host     |
| HP EliteBook 845 G8   | Linux Mint 22.3  | Ryzen 3 PRO 5450U, 16 GB RAM, 256 GB SSD  | Spare                             |
| Dell laptop           | –                | 16 GB RAM, 250 GB SSD                     | Spare                             |

### Other

- **Networking:** FRITZ!Repeater 1700 as Wi-Fi-to-LAN bridge for the lab, switch
- **Microcontrollers:** ESP32, ESP32-WROOM, Arduino kit
- **Android test devices:** ZTE Blade A72 5G, Samsung Galaxy A33 5G, 2× Samsung Galaxy A12

## Documentation

| Topic                  | Status      |
|------------------------|-------------|
| [Network](network/)    | Done        |
| Proxmox                | Planned     |
| Uptime Kuma            | Planned     |
| Tailscale              | Planned     |
| Nginx Proxy Manager    | Planned     |
| n8n                    | Planned     |

## Roadmap

- [ ] Document Proxmox, Uptime Kuma, Tailscale, Nginx Proxy Manager and n8n
- [ ] Offline AI with Ollama on a spare EliteDesk
- [ ] ESP32 status panel: LEDs that light up when a host is reachable
