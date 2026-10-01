# 2 – Storage

The installer splits the disk into two storages. Both show up under the node in the web UI.

## My storages

| Storage     | Type                 | Size       | Holds                                      |
|-------------|----------------------|------------|--------------------------------------------|
| `local`     | Directory            | ~68 GiB    | ISO images, container templates, backups   |
| `local-lvm` | LVM-thin             | ~141 GiB   | Disks of containers and VMs                |

Both live on the same 256 GB NVMe SSD. `local` is a folder on the system partition, so the same
~68 GiB also hold Proxmox itself. The rest of the disk is swap and the boot partition.

## Why two storages?

| Storage     | Think of it as                    | Example                                         |
|-------------|-----------------------------------|-------------------------------------------------|
| `local`     | A normal folder with files        | `debian-13-standard.tar.zst` (a container template) |
| `local-lvm` | A pool of raw disk space          | The 4 GB root disk of the Uptime Kuma container |

**Lesson I learned:** I once wrote down 68 GiB as the space for my containers. That number was
`local`. The containers actually live on `local-lvm`, which has about twice as much.

## Thin provisioning

`local-lvm` is **thin**: a container with an 8 GB disk only uses the space it has actually written.
That's why my three containers have 20 GB of disks configured, but use only about 9 GiB.

The catch: you can promise more space than the pool really has. If all containers fill their
disks at once, the pool runs full and the containers stop. Keep an eye on the usage in the web UI.

## Checking it

Web UI: node → **Disks** shows the physical disk, each storage under the node shows its usage.
In the Proxmox shell:

```bash
pvesm status     # all storages with total, used and available space
lvs              # LVM volumes, including the thin pool "data" and each container disk
```
