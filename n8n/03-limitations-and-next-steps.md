# 3 – Limitations & next steps

## Known limitations

| Limitation | Why it matters | Possible fix |
|------------|----------------|--------------|
| **Runs on my workstation** | If the laptop is off, asleep or closed, n8n and all its workflows are offline | Move n8n to its own always-on machine, for example a spare EliteDesk or an LXC/VM on Proxmox |
| **No backup** | A broken disk or a deleted volume means all workflows and credentials are gone | Regular volume backup to another device (see [Data & backup](02-data-and-backup.md)) |
| **Encryption key only inside the volume** | Without the volume, saved credentials cannot be recovered even if I had other copies | Set `N8N_ENCRYPTION_KEY` myself and keep a copy in a password manager |
| **Not monitored** | If n8n stops, I get no alert | Add an `HTTP(s)` monitor in [Uptime Kuma](../uptime-kuma/) |
| **Started with a single `docker run` command** | The setup only exists in my shell history. On a new machine I would have to rebuild it from memory | Write it as a `docker-compose.yml` and keep it in this repo (secrets in an `.env` file that is excluded with `.gitignore`) |
| **Image without a version tag** | `latest` can change on the next pull, so an update can bring breaking changes | Pin a version tag and update on purpose |
| **Port published on all interfaces** | Every device in my LAN can reach the n8n login page | Fine at home, but bind to `127.0.0.1` or put it behind the reverse proxy if that changes |
| **HTTP only** | The login and webhooks are not encrypted inside the LAN | TLS through a reverse proxy |
| **Not part of the Proxmox backup job** | The existing backup only covers the three LXC containers | Separate backup, see above |

## Next steps

- [ ] Back up the `n8n_data` volume and test it once
- [ ] Set my own `N8N_ENCRYPTION_KEY` and store it in a password manager
- [ ] Turn the `docker run` command into a `docker-compose.yml` in this repo (with `.env` and `.gitignore`)
- [ ] Add an `HTTP(s)` monitor for n8n in Uptime Kuma
- [ ] Pin the image to a version tag
- [ ] Decide where n8n should live long term (always-on machine instead of the workstation)
