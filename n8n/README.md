# n8n

Workflow automation for my homelab. n8n connects services and runs workflows
(trigger → steps → result) without me writing a full program for each one.

## How it runs

```
My workstation (Linux Mint)
└── Docker
    └── container "n8n"          image: docker.n8n.io/n8nio/n8n
        ├── port 5678            web interface and webhooks
        └── volume "n8n_data"    all workflows, credentials and settings
```

n8n runs as a single Docker container directly on my workstation, not on Proxmox
and not in a VM. I started it with one `docker run` command, not with a Compose file.

## At a glance

| Topic          | My setup                                                  |
|----------------|-----------------------------------------------------------|
| Runs as        | Docker container named `n8n` on my workstation            |
| Started with   | `docker run` (no Compose file)                            |
| Version        | n8n 2.x (image without a version tag, so `latest`)        |
| Port           | 5678, published on all network interfaces of the workstation |
| Data           | Docker volume `n8n_data`                                  |
| Restart        | `unless-stopped`: starts again after a reboot             |
| Backup         | None yet                                                  |
| Monitoring     | None yet                                                  |

## Documentation

| #  | File                                                     | What's inside                                             |
|----|----------------------------------------------------------|-----------------------------------------------------------|
| 1  | [Installation](01-installation.md)                       | The `docker run` command explained, first start, updating |
| 2  | [Data & backup](02-data-and-backup.md)                   | What the volume holds, the encryption key, how to back it up |
| 3  | [Limitations & next steps](03-limitations-and-next-steps.md) | What is missing and what I want to fix first         |

Placeholders like `<workstation-ip>` stand for addresses in your own network.
