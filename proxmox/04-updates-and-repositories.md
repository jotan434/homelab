# 4 – Updates & repositories

## Enterprise vs. no-subscription

Proxmox offers its packages through different repositories:

| Repository        | Needs a subscription | Who it is for                                         |
|-------------------|----------------------|-------------------------------------------------------|
| `pve-enterprise`  | Yes                  | Companies. Packages are tested longer before release  |
| `pve-no-subscription` | No               | Homelabs and testing. Same software, released earlier |
| Ceph repositories | Enterprise: yes      | Only needed if you use Ceph storage (I don't)         |

A fresh installation enables only the **enterprise** repositories.
Without a subscription, the server is not allowed to download from them, so every update check fails.

## The problem I found

| | |
|---|---|
| **Symptom** | Red entries in the task list: *Update package database* → `command 'apt-get update' failed: exit code 100`. It failed every day |
| **Cause** | Only the enterprise repositories (`pve-enterprise` and Ceph enterprise) were enabled. There was no `pve-no-subscription` repository |
| **Effect** | Proxmox got **no updates**, including security updates, and nobody noticed |
| **Fix** | See below |

## The fix (web UI)

1. Node → **Updates** → **Repositories**
2. Select `pve-enterprise` → **Disable**
3. Select the Ceph enterprise repository → **Disable**
4. **Add** → Repository: **No-Subscription** → **Add**
5. Node → **Updates** → **Refresh** → the task window must end with `TASK OK`

The same check in the Proxmox shell:

```bash
apt update               # must finish without "401 Unauthorized" or "E:" lines
apt list --upgradable    # which updates are waiting
```

## Installing updates

Node → **Updates** → **Upgrade** opens a shell and runs the upgrade.
If a new kernel was installed, Proxmox needs a reboot to use it. The containers start again
on their own (they are set to start at boot).

## Lesson learned

An error that happens silently every day is worse than a loud one.
The failed task was right there in the task list, but nothing alerted me.
