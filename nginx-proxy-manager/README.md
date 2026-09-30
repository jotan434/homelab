# Nginx Proxy Manager

Reverse proxy for my homelab. Instead of remembering `IP:port` for every service,
I open it by name, for example `http://kuma.home.arpa`.

## How it works

```
Browser: http://kuma.home.arpa
   │
   │  1. Name → IP   (/etc/hosts or DNS answers with the NPM address)
   ▼
Nginx Proxy Manager   <npm-ip>:80
   │
   │  2. Finds the proxy host "kuma.home.arpa"
   ▼
Uptime Kuma           <kuma-ip>:3001
```

## At a glance

| Topic            | My setup                                                  |
|------------------|-----------------------------------------------------------|
| Runs as          | LXC container on Proxmox, installed with a helper script  |
| Software         | OpenResty (Nginx) + Node.js, no Docker                    |
| Proxied services | Uptime Kuma                                               |
| Name resolution  | `/etc/hosts` on my workstation (no local DNS server yet)  |
| TLS              | None, HTTP only                                           |

## Documentation

| #  | File                                                   | What's inside                                          |
|----|--------------------------------------------------------|--------------------------------------------------------|
| 1  | [Installation](01-installation.md)                     | Helper script, what it installs, static IP, first login |
| 2  | [Proxy hosts](02-proxy-hosts.md)                       | Adding a service step by step, websockets              |
| 3  | [Name resolution](03-name-resolution.md)               | `/etc/hosts`, local DNS, why `.home.arpa`              |
| 4  | [Testing & troubleshooting](04-troubleshooting.md)     | Test commands, errors and their causes                 |
| 5  | [Limitations & lessons learned](05-limitations-and-lessons.md) | What doesn't work yet, what I learned          |

Placeholders like `<npm-ip>` or `<kuma-ip>` stand for addresses in your own network.
