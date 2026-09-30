# 2 – Proxy hosts

A proxy host is one rule: *"requests for this name go to that address"*.

## Add a proxy host

Admin UI → **Hosts** → **Proxy Hosts** → **Add Proxy Host**.

### Details tab

| Field                  | What it means                                            | My value for Uptime Kuma |
|------------------------|----------------------------------------------------------|--------------------------|
| Domain Names           | The name clients type in the browser                     | `kuma.home.arpa`         |
| Scheme                 | How NPM talks **to the service** (`http` or `https`)     | `http`                   |
| Forward Hostname / IP  | Where the service runs                                   | `<kuma-ip>`              |
| Forward Port           | The port of the service                                  | `3001`                   |
| Websockets Support     | Allows long-lived live connections through the proxy     | On                       |
| Access List            | Who may use this proxy host                              | Publicly Accessible      |

> **Scheme is not about the browser.** It describes the connection NPM → service.
> Whether the browser uses HTTPS is decided on the **SSL** tab.

### SSL tab

Nothing set. My setup is HTTP only (see [Limitations](05-limitations-and-lessons.md)).

### Save

After saving, the proxy host shows **Online**. That only means NPM can reach the
service. It does **not** mean clients can resolve the name yet → [Name resolution](03-name-resolution.md).

## Why Uptime Kuma needs websockets

The Uptime Kuma dashboard receives its live status updates over a WebSocket connection.
Without *Websockets Support* the page loads, but then shows
*"Cannot connect to the socket server"* and never updates.

## My proxy hosts

| Name             | Service      | Port | Websockets | SSL        |
|------------------|--------------|------|------------|------------|
| `kuma.home.arpa` | Uptime Kuma  | 3001 | On         | None (HTTP) |

## Checklist for the next service

1. Test the service directly: `curl -I http://<service-ip>:<port>`
2. Add the proxy host in NPM (table above)
3. Add the name to name resolution → [03](03-name-resolution.md)
4. Test the whole chain → [04](04-troubleshooting.md)
