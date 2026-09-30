# Uptime Kuma

Monitoring for my homelab. Uptime Kuma checks every minute whether my network and
servers are reachable and sends me an email when something goes down.

## What it watches

```
Connection                    is my internet path working?
├── Internet                  a public server on the internet
├── Router                    my home router
└── Repeater                  the Wi-Fi-to-LAN bridge for the lab

Homelab
└── Proxmox-server            group for the Proxmox host (tag: EliteDesk)
    ├── Proxmox-VE            the Proxmox host itself
    └── Tailscale             remote-access container
```

The order is on purpose: if **Router** is down, **Internet** being down is no surprise.
The groups show at a glance *where* the problem is.

## At a glance

| Topic         | My setup                                                       |
|---------------|----------------------------------------------------------------|
| Runs as       | LXC container on Proxmox, installed with a helper script       |
| Version       | Uptime Kuma 2.x                                                |
| Monitors      | 5 ping monitors in 2 groups, checked every 60 seconds          |
| Alerts        | Email via Gmail SMTP                                           |
| Access        | `http://kuma.home.arpa` through [Nginx Proxy Manager](../nginx-proxy-manager/) |

## Documentation

| #  | File                                                      | What's inside                                          |
|----|-----------------------------------------------------------|--------------------------------------------------------|
| 1  | [Installation](01-installation.md)                        | Helper script, what it installs, first login, updating |
| 2  | [Monitors](02-monitors.md)                                | Groups, ping monitors, what "retries" mean, adding a monitor |
| 3  | [Notifications](03-notifications.md)                      | Gmail SMTP with an app password, testing the alert     |
| 4  | [Limitations & next steps](04-limitations-and-next-steps.md) | What is not monitored yet and why it matters       |

Placeholders like `<kuma-ip>` or `<router-ip>` stand for addresses in your own network.
