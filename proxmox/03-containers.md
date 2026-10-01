# 3 – Containers

## LXC container or VM?

| | LXC container | Virtual machine (VM) |
|---|---|---|
| Kernel | Shares the kernel of the Proxmox host | Has its own kernel |
| Operating system | Linux only | Anything (Linux, Windows, …) |
| Resources | Very small (my containers use 512 MB–2 GB RAM) | Needs more RAM and disk |
| Start time | Seconds | Like a normal PC boot |

All my services are Linux tools, so they run in LXC containers. A Windows server would need a VM.

## My containers

| ID  | Name                | Cores | RAM     | Disk | Created with      | Docs |
|-----|---------------------|-------|---------|------|-------------------|------|
| 100 | `tailscale`         | 1     | 512 MB  | 8 GB | Debian LXC        | Planned |
| 101 | `uptimekuma`        | 1     | 1024 MB | 4 GB | Helper script     | [uptime-kuma](../uptime-kuma/) |
| 102 | `nginxproxymanager` | 2     | 2048 MB | 8 GB | Helper script     | [nginx-proxy-manager](../nginx-proxy-manager/) |

Every container also has 512 MB swap. All disks are on `local-lvm`.

## Settings all my containers share

| Setting | Value | What it means |
|---------|-------|---------------|
| Unprivileged | Yes | `root` inside the container is **not** `root` on the host. If a container is hacked, the host is still protected. Default and recommended |
| Start at boot | Yes | After a reboot or power cut, Proxmox starts the containers on its own |
| Nesting | On | Newer systemd versions inside the container need it. Proxmox turns it on by default for unprivileged containers |
| Network | Bridge `vmbr0`, static IPv4 | Every container is a normal device in my network with its own fixed IP |
| DNS | Same as the host | No custom DNS server inside the containers |

## Creating a container

**With a helper script** (how I created Uptime Kuma and Nginx Proxy Manager):
one command in the Proxmox shell creates the container and installs the app.
See [Nginx Proxy Manager → Installation](../nginx-proxy-manager/01-installation.md).

**By hand** in the web UI: **Create CT** (top right) → hostname, password → template
(e.g. Debian 13) → disk → CPU → memory → network (bridge `vmbr0`, static IP) → DNS → finish.

## Useful commands (Proxmox shell)

```bash
pct list                 # all containers with ID, status and name
pct config 101           # full configuration of container 101
pct enter 101            # open a root shell inside container 101
pct stop 101 && pct start 101   # restart container 101
```
