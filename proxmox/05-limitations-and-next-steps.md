# 5 – Limitations & next steps

## Known limitations

| Limitation | Why it matters | Possible fix |
|------------|----------------|--------------|
| **Backups only on the same disk** | A daily backup job exists, but its target `local` is the same SSD as the containers. It helps after a wrong command, not after a dead SSD (details: [Backups](06-backups.md)) | A second backup target on a different device |
| **One disk, no redundancy** | Everything (Proxmox, all containers) is on a single 256 GB NVMe SSD. If it fails, the whole lab is gone | Backups on a **different** device, e.g. the spare HP ProLiant MicroServer as storage |
| **Single node** | If this one PC is off, every service is off. That includes the monitoring, so no alert is sent | Accept for a homelab, or a second node later |
| **Monitoring is ping only** | Uptime Kuma checks whether the host answers, not whether the web UI works | `HTTP(s)` monitor for port 8006 |

## Next steps

- [x] Backup job for all containers (to `local` first, see [Backups](06-backups.md))
- [ ] Test a restore once. A backup that was never restored is only a hope
- [ ] Decide where backups should live long term (ProLiant MicroServer?)
- [ ] `HTTP(s)` monitor for the web UI in Uptime Kuma
