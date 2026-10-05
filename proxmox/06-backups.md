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
| Notification       | Mail after every run, sent through an SMTP target (see [below](#mail-notifications-the-problem-and-the-fix)) |
| Bandwidth limit    | None                                    |
| If the host is off | The missed run is caught up as soon as the host is back (`repeat-missed` is on) |

Status: the job works. My first run (started by hand with **Run now**) created all three backups and
finished with `OK`. Mail notifications work since I fixed a Gmail rejection (see
[Mail notifications](#mail-notifications-the-problem-and-the-fix)). I have not tested a restore, and I do not plan to for now.

## What the settings mean

- **Snapshot mode.** Proxmox takes a snapshot of the container disk and copies the snapshot.
  The container keeps running, so there is no downtime for Uptime Kuma, Tailscale or the proxy.
  The other modes (`suspend`, `stop`) interrupt the container.
- **`zstd` compression.** A fast compression that makes the backup files much smaller than the raw disk.
- **`keep-last 2`.** After each run Proxmox deletes older backups and keeps only the two newest per
  container. This saves space, but it also means I can only go back about two days.
- **Storage `local`.** A normal folder on the system partition. It also holds ISOs and templates
  (see [Storage](02-storage.md)). Backups are plain files in `/var/lib/vz/dump`.
- **Mail notification.** Proxmox sends a report after each run. This needs a working mail path.
  Mine did not work at first, see [Mail notifications](#mail-notifications-the-problem-and-the-fix).
- **`repeat-missed` on.** If the host was switched off at 16:00, Proxmox runs the job as soon as possible
  after the host is back. Without it, that day would simply have no backup.

## Create the job

1. **Datacenter → Backup → Add**
2. **Storage:** `local`
3. **Schedule:** daily at 16:00
4. **Selection mode:** include selected VMs → tick 100, 101, 102
5. **Mode:** Snapshot, **Compression:** ZSTD
6. **Retention:** Keep Last = 2
7. **Repeat missed:** on (in the job's advanced options), so a run missed while the host was off is caught up
8. **Notification:** set the mode to "Use global notification settings", so the report goes to my
   SMTP target (see [Mail notifications](#mail-notifications-the-problem-and-the-fix))
9. **Create**

To run it once right away: select the job → **Run now**.

## Check that it works

```bash
pvesh get /cluster/backup --output-format json-pretty   # shows the job and its settings
ls -lh /var/lib/vz/dump                                  # lists the backup files and their size
```

An empty `/var/lib/vz/dump` after the scheduled time means the job did not run or failed.
Check **Node → Tasks** for a red entry.

My first run created these backups (about 2.8 GB in total):

| Container | Name | Backup size |
|-----------|------|-------------|
| 100 | tailscale | 269 MB |
| 101 | uptimekuma | 728 MB |
| 102 | nginxproxymanager | 1.9 GB |

With `keep-last 2` that is at most about 6 GB of the roughly 68 GiB on `local`.

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

## Mail notifications: the problem and the fix

**Problem.** After the first manual run the backup finished with `OK`, but no mail arrived.
The log of the host's mail service showed why:

```bash
journalctl -u postfix -n 30 --no-pager   # last 30 lines of the host's mail log
```

Gmail had rejected the message with `550-5.7.26 ... the sender is unauthenticated`
(SPF and DKIM failed). The same rejection was already in the log from days earlier.
Other Proxmox mails had been lost in the same way, and I never noticed.

**Cause.** In the legacy mail mode, Proxmox hands the mail to its local mail service (postfix),
which delivers it straight to Gmail as `root@<hostname>`. Gmail only accepts senders that
prove who they are (SPF/DKIM) or log in. A homelab hostname does neither.

**Fix.** Send the mail through Gmail's SMTP server with a login instead:

1. Google account → Security → **App passwords** → create one named `proxmox`
   (needs 2-step verification). Google shows it only once. It is not my normal Google password.
2. Proxmox: **Datacenter → Notifications → Targets → Add → SMTP**.
   Server `smtp.gmail.com`, port `587`, encryption STARTTLS. Username, from address and recipient
   are my Gmail address, the password is the app password. Press **Test**.
3. **Notifications → Matchers → default-matcher**: add the new target.
4. **Datacenter → Backup**: edit the job and set the notification mode to
   **Use global notification settings**.

**Result.** After **Run now**, the mail arrives with the full report.
Proxmox stores the app password on the host (readable by root only). It is not part of this repository.

**Lesson.** A notification that was never tested is not a notification. The backup itself was fine,
but the alarm path was broken and nothing told me. An error that happens silently every day is
more dangerous than one that fails loudly.

## Limitations

| Limitation | Why it matters | Possible fix |
|------------|----------------|--------------|
| **Backups are on the same disk** | `local` lives on the same NVMe SSD as the containers. The backup protects against a broken update or a wrong command, **not** against a dead SSD | Second backup target on a different device (spare HP ProLiant MicroServer) |
| **Only two versions** | A problem I notice after three days is already gone from the backups | Higher `keep-last`, or keep daily plus weekly versions |
| **Mail depends on one app password** | If the app password is revoked or changed, the mails stop and I may not notice | Press **Test** on the target now and then, and look at the Tasks list |
| **Restore not tested** | Until it is, I do not know for sure that the backups are usable. A restore test is not planned for now | If needed later: restore one container under a new ID (see [Restore](#restore)) |

## Next steps

- [x] Check the first backup files after the first run
- [x] Verify that the notification mail arrives (fixed, see above)
- [ ] Second backup target on a different device, once something important runs on the host
- Not planned for now: a restore test. Until it is done, the backups stay unproven.
