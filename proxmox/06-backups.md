# 6 – Backups

How my three containers are backed up, what that does and does not protect me from,
and how I check that it works.

## At a glance

| Setting            | Value                                   |
|--------------------|-----------------------------------------|
| Tool               | Proxmox backup job (`vzdump`)           |
| What is backed up  | All three containers (IDs 100, 101, 102) |
| Schedule           | Every day at 16:00                      |
| Mode               | `snapshot`                              |
| Compression        | `zstd`                                  |
| Target storage     | `local` (folder `/var/lib/vz/dump`)     |
| Retention          | `keep-last 2` (the two newest backups per container) |
| Notification       | Mail after every run (`always`)         |
| Bandwidth limit    | None                                    |
| If the host is off | The run is skipped (`repeat-missed` is off) |

Status when I wrote this: the job exists and the first run is scheduled. The backup folder
was still empty, and I have not tested a restore yet.

## What the settings mean

- **Snapshot mode.** Proxmox takes a snapshot of the container disk and copies the snapshot.
  The container keeps running, so there is no downtime for Uptime Kuma, Tailscale or the proxy.
  The other modes (`suspend`, `stop`) interrupt the container.
- **`zstd` compression.** A fast compression that makes the backup files much smaller than the raw disk.
- **`keep-last 2`.** After each run Proxmox deletes older backups and keeps only the two newest per
  container. This saves space, but it also means I can only go back about two days.
- **Storage `local`.** A normal folder on the system partition. It also holds ISOs and templates
  (see [Storage](02-storage.md)). Backups are plain files in `/var/lib/vz/dump`.
- **Mail notification.** Proxmox sends a report after each run. This only works if the host can send
  mail, which I have not verified yet.

## Create the job

1. **Datacenter → Backup → Add**
2. **Storage:** `local`
3. **Schedule:** daily at 16:00
4. **Selection mode:** include selected VMs → tick 100, 101, 102
5. **Mode:** Snapshot, **Compression:** ZSTD
6. **Retention:** Keep Last = 2
7. **Notification:** set the mail address and the mode to "Always"
8. **Create**

To run it once right away: select the job → **Run now**.

## Check that it works

```bash
pvesh get /cluster/backup --output-format json-pretty   # shows the job and its settings
ls -lh /var/lib/vz/dump                                  # lists the backup files and their size
```

An empty `/var/lib/vz/dump` after the scheduled time means the job did not run or failed.
Check **Node → Tasks** for a red entry.

## Restore

A backup that was never restored is only a hope. To test a restore without touching the
running container, restore it under a **new ID**:

```bash
pct restore 201 /var/lib/vz/dump/<backup-file>.tar.zst --storage local-lvm
```

- `pct restore` creates a container from a backup file.
- `201` is a free ID for the copy, so the original (for example 101) stays untouched.
- `--storage local-lvm` puts the copy's disk on the container storage.

**Do not start the copy while the original runs.** The backup contains the static IP, so both
would use the same address. Check that the copy exists and its config looks right, then delete it.

In the web UI the same thing is under **Storage `local` → Backups → select a file → Restore**.

## Limitations

| Limitation | Why it matters | Possible fix |
|------------|----------------|--------------|
| **Backups are on the same disk** | `local` lives on the same NVMe SSD as the containers. The backup protects against a broken update or a wrong command, **not** against a dead SSD | Second backup target on a different device (spare HP ProLiant MicroServer) |
| **Only two versions** | A problem I notice after three days is already gone from the backups | Higher `keep-last`, or keep daily plus weekly versions |
| **Missed runs are skipped** | If the host is off at 16:00, there is no backup that day | Pick a time when the host is on, or enable `repeat-missed` |
| **Mail not verified** | A failed backup could go unnoticed | Send a test mail from the host, and watch the Tasks list meanwhile |
| **Restore not tested yet** | Until then I do not know if the backups are usable | Restore one container under a new ID (see above) |

## Next steps

- [ ] Check the first backup files after the first run
- [ ] Test a restore of one container under a new ID
- [ ] Second backup target on a different device
- [ ] Verify that the notification mail arrives
