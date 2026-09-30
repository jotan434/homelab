# 1 – Installation

I installed Uptime Kuma as an LXC container on Proxmox VE with the
[Proxmox VE Community Scripts](https://community-scripts.org/scripts/uptimekuma).
The process is the same as for [Nginx Proxy Manager](../nginx-proxy-manager/01-installation.md),
where every part of the command is explained.

## What the script creates

Defaults at the time of writing (check the script page before installing):

| Setting   | Default                     |
|-----------|-----------------------------|
| Type      | Unprivileged LXC container  |
| OS        | Debian 13                   |
| CPU       | 1 core                      |
| RAM       | 1024 MB                     |
| Disk      | 4 GB                        |
| Web UI    | Port 3001                   |

## Run it in the Proxmox shell

Read the script first: [ct/uptimekuma.sh](https://github.com/community-scripts/ProxmoxVE/blob/main/ct/uptimekuma.sh).
Then, in the Proxmox web UI → select the node → **Shell**:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/uptimekuma.sh)"
```

## What the script installs

No Docker. Everything runs directly in the container:

| Component              | Purpose                                                        |
|------------------------|----------------------------------------------------------------|
| Node.js 22             | Runs Uptime Kuma                                               |
| Uptime Kuma            | Downloaded from the official GitHub release to `/opt/uptime-kuma` |
| Chromium               | Needed only for the "Real Browser" monitor type               |
| `uptime-kuma` service  | systemd service that starts Uptime Kuma on boot                |
| `/opt/uptime-kuma/data`| Database and settings. This is what a backup must contain      |

## Static IP

My container first ran with a DHCP address. Like every server in my lab, it now has a
static IP. Here it matters because the proxy host in Nginx Proxy Manager points to this
address: if it changed, `kuma.home.arpa` would break.
See [network design decisions](../network/02-design-decisions.md).

## First login

Open `http://<kuma-ip>:3001`.

1. Uptime Kuma 2 first asks which **database** to use. For a small homelab the built-in
   SQLite option is the simple choice, no extra database server needed.
2. Then you create the **admin account**. There are no default credentials.

## Updating

Take a Proxmox snapshot of the container first. Then, in the container console:

```bash
update
```

The helper script adds this command. It downloads the newest release and restarts the service.

## Checking the service (inside the container)

```bash
systemctl status uptime-kuma    # is it running?
journalctl -u uptime-kuma -e    # latest log lines
```
