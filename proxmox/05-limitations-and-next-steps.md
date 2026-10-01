# 5 – Limitations & next steps

## Known limitations

| Limitation | Why it matters | Possible fix |
|------------|----------------|--------------|
| **No backups** | No backup job, no backup files. If a container breaks after an update or a wrong command, it is gone | Backup job in Proxmox (Datacenter → Backup), at least weekly |
| **One disk, no redundancy** | Everything (Proxmox, all containers) is on a single 256 GB NVMe SSD. If it fails, the whole lab is gone | Backups on a **different** device, e.g. the spare HP ProLiant MicroServer as storage |
| **Single node** | If this one PC is off, every service is off. That includes the monitoring, so no alert is sent | Accept for a homelab, or a second node later |
| **Monitoring is ping only** | Uptime Kuma checks whether the host answers, not whether the web UI works | `HTTP(s)` monitor for port 8006 |

## Next steps

- [ ] Backup job for all containers (first to `local`, later to a second device)
- [ ] Test a restore once. A backup that was never restored is only a hope
- [ ] Decide where backups should live long term (ProLiant MicroServer?)
- [ ] `HTTP(s)` monitor for the web UI in Uptime Kuma
