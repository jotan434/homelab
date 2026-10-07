# 1 – Installation

How I run n8n and what each part of the command does.

## Why Docker and not an LXC container

My other services run as LXC containers on Proxmox. n8n is different on purpose:

- **LXC** is a small Linux system in a container, and I install the program inside it.
- **Docker** packages one application with everything it needs. The image is maintained by the n8n project, so I do not install Node.js or dependencies myself.

n8n is distributed as a ready-made Docker image, and my workstation already runs Docker,
so this was the shortest path.

## The command

```bash
docker run -d --name n8n --restart unless-stopped \
  -p 5678:5678 \
  -e GENERIC_TIMEZONE=Europe/Vienna \
  -e TZ=Europe/Vienna \
  -v n8n_data:/home/node/.n8n \
  docker.n8n.io/n8nio/n8n
```

| Part                                   | What it does                                                           |
|----------------------------------------|------------------------------------------------------------------------|
| `docker run`                           | Creates a new container from an image and starts it                    |
| `-d`                                   | Detached: runs in the background, the terminal stays free              |
| `--name n8n`                           | Gives the container a fixed name, so I can write `docker logs n8n` instead of an ID |
| `--restart unless-stopped`             | Starts the container again after a reboot or crash, unless I stopped it myself |
| `-p 5678:5678`                         | Maps port 5678 of the workstation to port 5678 in the container (host:container) |
| `-e GENERIC_TIMEZONE=Europe/Vienna`    | Time zone for n8n's schedules, so a workflow set to 08:00 runs at 08:00 my time |
| `-e TZ=Europe/Vienna`                  | Time zone of the container itself (log timestamps)                     |
| `-v n8n_data:/home/node/.n8n`          | Stores all n8n data in a named volume, so it survives deleting the container |
| `docker.n8n.io/n8nio/n8n`              | The image. No tag after the name means `latest`                        |

The volume line is the most important one. Without it, deleting the container would delete every workflow.

## First start

1. Run the command above.
2. Open `http://localhost:5678` in the browser on the workstation (other devices in the LAN use `http://<workstation-ip>:5678`).
3. n8n shows a setup page for the owner account (name, email, password). There is no default login.

## Check that it runs

```bash
docker ps                      # lists running containers: is "n8n" there, and is the port mapped?
docker logs n8n --tail 20      # last 20 log lines, useful when something fails
docker exec n8n n8n --version  # runs the version command inside the container
```

## Check how it was started

I started n8n a while ago, so I looked up how I did it:

```bash
docker inspect n8n --format '{{.HostConfig.RestartPolicy.Name}}'              # restart policy
docker inspect n8n --format '{{json .Mounts}}'                                # where the data lives
docker inspect n8n --format '{{index .Config.Labels "com.docker.compose.project"}}'   # empty = not started with Compose
history | grep "docker run"                                                   # my own shell history
```

- The Compose label is empty, so n8n was **not** started with Compose.
- My shell history still held the exact command, which is the one documented above.

## Updating

Containers cannot be updated in place. The image is replaced and the container is created again,
**with the same volume**:

```bash
docker pull docker.n8n.io/n8nio/n8n    # download the newest image
docker stop n8n                        # stop the old container
docker rm n8n                          # remove the old container (the volume stays!)
# then run the docker run command from above again
```

Do a backup first (see [Data & backup](02-data-and-backup.md)). I have not updated n8n this way yet.
