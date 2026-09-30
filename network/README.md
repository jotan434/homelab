# Network

How my home network is built and how the lab fits into it.

## Topology

```
Internet
   │
ARRIS router (living room)      gateway + DNS, hands out DHCP addresses
   │  Wi-Fi link
FRITZ!Repeater 1700             turns the Wi-Fi link into LAN
   │  LAN
Switch
   └── all lab devices (wired)
```

## At a glance

| Topic          | My setup                                                     |
|----------------|--------------------------------------------------------------|
| Network size   | One /24 home network (254 usable addresses)                  |
| Gateway + DNS  | The router (`.1`)                                            |
| Addresses      | DHCP for clients below `.200`, static IPs for servers from `.200` |
| Local DNS      | None yet. Lab names only work via `/etc/hosts`               |
| Lab uplink     | Wi-Fi link through a repeater (known bottleneck)             |

## Documentation

| #  | File                                            | What's inside                                       |
|----|-------------------------------------------------|-----------------------------------------------------|
| 1  | [Address scheme](01-address-scheme.md)          | Which range is for what, setting a static IP in Proxmox |
| 2  | [Design decisions](02-design-decisions.md)      | Why static IPs, why wired, why this range           |
| 3  | [Troubleshooting](03-troubleshooting.md)        | Bottom-up checklist and errors I actually hit       |
| 4  | [Limitations](04-limitations.md)                | What is weak right now and the plan for it          |
