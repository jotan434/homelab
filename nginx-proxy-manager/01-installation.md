# 1 – Installation

I installed Nginx Proxy Manager (NPM) as an LXC container on Proxmox VE, using the
[Proxmox VE Community Scripts](https://community-scripts.org/scripts/nginxproxymanager).

## Prerequisites

- A running Proxmox VE host with internet access (the script downloads packages)
- A free IP address in your network for the container

## What the script creates

Defaults at the time of writing (they change, so check the script page first):

| Setting   | Default                   |
|-----------|---------------------------|
| Type      | Unprivileged LXC container |
| OS        | Debian 13                 |
| CPU       | 2 cores                   |
| RAM       | 2048 MB                   |
| Disk      | 8 GB                      |
| Admin UI  | Port 81                   |

## Step 1 – Read the script before running it

The script runs as root on your hypervisor. That means you trust it completely.
Skim it first: [ct/nginxproxymanager.sh](https://github.com/community-scripts/ProxmoxVE/blob/main/ct/nginxproxymanager.sh)

## Step 2 – Run it in the Proxmox shell

Proxmox web UI → select the node → **Shell**:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/nginxproxymanager.sh)"
```

| Part              | Meaning                                                                 |
|-------------------|-------------------------------------------------------------------------|
| `curl -fsSL <url>` | Download the script. `-f` fail on HTTP errors, `-s` silent, `-S` still show errors, `-L` follow redirects |
| `$( ... )`        | Command substitution: the downloaded text is inserted here              |
| `bash -c "..."`   | Run that text as a bash script                                          |

## Step 3 – Choose the settings

The script asks for **Default** or **Advanced** settings. Advanced lets you set the
container ID, hostname, resources and network.

**Give the container a static IP.** Either in the advanced settings of the script, or
later in Proxmox under *Container → Network* (IPv4: Static, `<npm-ip>/24`, gateway = your router).

Why: every proxy host and every `/etc/hosts` or DNS entry points to this IP.
If it changes through DHCP, all names break at once. Another container in my lab
once lost its DHCP lease and became unreachable, see [network design decisions](../network/02-design-decisions.md).

## What the script installs

The script does **not** use Docker. It installs NPM directly inside the container:

| Component   | Purpose                                                        |
|-------------|----------------------------------------------------------------|
| OpenResty   | Nginx with Lua, compiled from source. This is the actual proxy |
| Node.js 22  | Runs the NPM backend and the admin UI                          |
| Certbot     | In a Python venv, for Let's Encrypt certificates               |
| `openresty` and `npm` | The two systemd services                             |
| `/data`     | SQLite database and generated Nginx configs                    |

This is why responses from NPM carry the header `Server: openresty`.
Compiling OpenResty takes a few minutes, so the script is not fast.

## Step 4 – First login

Open `http://<npm-ip>:81`.

Current NPM versions show a **setup wizard** on the first visit, where you create the
admin account. There are no default credentials anymore. Older guides mention
`admin@example.com` / `changeme` – that no longer applies.

Use a strong password and store it in a password manager.

## Container DNS

My container uses the DNS settings of the Proxmox host. This only affects lookups
the **container itself** makes (for example downloading updates).
It has nothing to do with how clients find NPM → see [Name resolution](03-name-resolution.md).

## Updating

Take a Proxmox snapshot or backup of the container first. Then, in the container console:

```bash
update
```

The helper script adds this `update` command to the container. It re-runs the script in update mode.
