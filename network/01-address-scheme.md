# 1 – Address scheme

Instead of a list of single IPs, the network is split into **ranges**.
Every device knows by its role which range it belongs to.

## Ranges

Only the last number of the address is shown (the network is a /24, so only that part changes).

| Range        | Used for                                   | Assigned by       |
|--------------|--------------------------------------------|-------------------|
| `.1`         | Router (gateway + DNS)                     | Fixed             |
| below `.200` | Clients: laptops, phones, guests           | Router via DHCP   |
| `.200`–`.254`| Servers and containers                     | Me, static        |

I checked in the router that the DHCP range ends **below** `.200`.
That matters: if the ranges overlapped, the router could give a phone the same address
a server already uses. Two devices with one IP = both become unreachable at random.

## What gets a static IP

| Device / service        | Type                |
|-------------------------|---------------------|
| Proxmox VE host         | Physical server     |
| Uptime Kuma             | LXC on Proxmox      |
| Tailscale               | LXC on Proxmox      |
| Nginx Proxy Manager     | LXC on Proxmox      |

Rule: **anything other devices need to find gets a static IP.** Laptops and phones don't.

## Setting a static IP for an LXC container in Proxmox

Proxmox web UI → select the container → **Network** → select `net0` → **Edit**:

| Field       | Value                                  |
|-------------|----------------------------------------|
| IPv4        | Static                                 |
| IPv4/CIDR   | `<static-ip>/24` (a free address from `.200` up) |
| Gateway     | `<router-ip>` (the `.1` address)       |

Then check inside the container:

```bash
ip a              # does the interface show the new address?
ip route          # is the default route via the router?
ping -c 3 <router-ip>   # can the container reach the gateway?
```

Before picking an address, make sure nothing uses it yet: `ping -c 3 <static-ip>` should get **no** reply.
This is not 100 % reliable (some devices ignore ping), so the list of used static IPs in my notes is the real reference.
