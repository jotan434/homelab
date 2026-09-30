# 2 – Monitors

## My monitors

All monitors are of type **Ping** and are checked every **60 seconds**.

| Group                     | Monitor     | Target             | Retries | Alert after about |
|---------------------------|-------------|--------------------|---------|-------------------|
| Connection                | Internet    | `1.1.1.1` (Cloudflare DNS) | 3 | 4 minutes      |
| Connection                | Router      | `<router-ip>`      | 2       | 3 minutes         |
| Connection                | Repeater    | `<repeater-ip>`    | 2       | 3 minutes         |
| Homelab → Proxmox-server  | Proxmox-VE  | `<proxmox-ip>`     | 1       | 2 minutes         |
| Homelab → Proxmox-server  | Tailscale   | `<tailscale-ip>`   | 0       | 1 minute          |

## What "retries" mean

*Retries* = how many **extra** failed checks Uptime Kuma waits for before it marks a monitor
as **DOWN** and sends a notification. Until then the monitor shows **PENDING**.

With a 60-second interval and a 60-second retry interval, the time until the alert is roughly:

```
(retries + 1) × 1 minute
```

| Retries | Effect                                                                 |
|---------|------------------------------------------------------------------------|
| 0       | One lost ping = alert. Fast, but a single hiccup causes a false alarm  |
| 2–3     | Short hiccups are ignored, real outages are reported a few minutes later |

That is the trade-off: **faster alerts vs. fewer false alarms.**

## Why ping

Ping answers one question: *is the device on the network?* It is the simplest check
and works for anything with an IP address, including the router and the repeater.

It does **not** show whether the service on that device works.
Proxmox can answer ping while its web interface is broken.
See [Limitations](04-limitations-and-next-steps.md).

## Groups and tags

| Feature | What it does                                                                 | How I use it |
|---------|------------------------------------------------------------------------------|--------------|
| Group   | A monitor that contains other monitors. It is DOWN if one of its children is DOWN | *Connection* for the path to the internet, *Homelab* for my servers |
| Tag     | A colored label, useful for filtering                                         | `EliteDesk` marks the hardware the Proxmox group runs on |

## Adding a monitor

**+ Add New Monitor**, then:

| Field                | What to enter                                   |
|----------------------|-------------------------------------------------|
| Monitor Type         | `Ping` (or `HTTP(s)` for a web service)         |
| Friendly Name        | Short name that shows up in the dashboard and the email |
| Hostname             | IP or name of the device                        |
| Heartbeat Interval   | `60` seconds                                    |
| Retries              | See the table above                             |
| Monitor Group        | The group it belongs to                         |
| Notifications        | Tick the email notification                     |

Save, then check that the monitor turns green within a minute.
