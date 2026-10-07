# 2 – Data & backup

Where n8n keeps its data, what is in it, and why the encryption key matters.

## Where the data lives

```bash
docker inspect n8n --format '{{json .Mounts}}'
```

| Setting     | Value                                          |
|-------------|------------------------------------------------|
| Type        | Docker named volume                            |
| Name        | `n8n_data`                                     |
| In container | `/home/node/.n8n`                             |
| On the host | `/var/lib/docker/volumes/n8n_data/_data`       |

Everything n8n needs is in this one volume:

- the database (SQLite by default) with **workflows** and execution history
- the **credentials** (logins and API keys that workflows use), stored encrypted
- the n8n `config` file, which contains the **encryption key**

## The encryption key

n8n encrypts stored credentials with a key. I did **not** set `N8N_ENCRYPTION_KEY` in my
`docker run` command (the environment variables are listed with
`docker inspect n8n --format '{{range .Config.Env}}{{println .}}{{end}}' | cut -d= -f1`, which prints only the names, not the values).

When no key is set, n8n generates one on the first start and saves it in the `config` file
**inside the same volume**. That has a consequence:

| Situation                              | Result                                                       |
|----------------------------------------|--------------------------------------------------------------|
| Container deleted, volume kept         | Everything works after creating the container again          |
| Volume lost, no backup                 | Workflows **and** credentials are gone                       |
| Workflows restored, key lost           | Workflows come back, but the saved credentials cannot be decrypted. I would have to enter every login again |

So the key and the data have to be backed up together, or the key has to live somewhere separate
(for example in an `.env` file or a password manager, passed with `-e N8N_ENCRYPTION_KEY=...`).
The key never goes into this repository.

## Backup

**Status: there is no backup of n8n yet.** My Proxmox backup job covers the three LXC containers,
but n8n does not run on Proxmox, so it is not part of it.

The simplest approach is to pack the whole volume into one archive. This is how I plan to do it
(not tested yet):

```bash
docker stop n8n                                    # stop n8n so the database file is not written while copying
docker run --rm \
  -v n8n_data:/data \
  -v "$PWD":/backup \
  alpine tar czf /backup/n8n_data.tar.gz -C /data . # a short-lived helper container packs the volume into a .tar.gz
docker start n8n                                   # start n8n again
```

| Part                       | What it does                                                         |
|----------------------------|----------------------------------------------------------------------|
| `--rm`                     | Removes the helper container as soon as it is done                   |
| `-v n8n_data:/data`        | Mounts the n8n volume into the helper at `/data`                     |
| `-v "$PWD":/backup`        | Mounts the current folder into the helper at `/backup`, so the archive lands there |
| `tar czf … -C /data .`     | Creates a compressed archive of everything inside `/data`            |

The archive contains the credentials and the key, so it must **not** be uploaded to a public place.
Backing up to the same disk does not protect against a dead disk, so a copy on another device is needed.
