# Network

Overview of my home network and IP plan.

## Topology

```
Internet
   │
ARRIS router (living room)
   │  Wi-Fi link
FRITZ!Repeater 1700
   │  LAN
Switch
   └── all my lab devices
```

## Basics

| Setting         | Value                          |
|-----------------|--------------------------------|
| Subnet          | 192.168.0.0/24                 |
| Subnet mask     | 255.255.255.0                  |
| Default gateway | 192.168.0.1                    |
| DNS server      | 192.168.0.1                    |

## IP plan (static)

| IP            | Host        | Type                              | Purpose                  |
|---------------|-------------|-----------------------------------|--------------------------|
| 192.168.0.1   | Router      | Hardware                          | Gateway, DNS             |
| 192.168.0.206 | uptime-kuma | LXC on Proxmox                    | Monitoring (port 3001)   |
| 192.168.0.212 | tailscale   | LXC on Proxmox                    | Remote access VPN        |
| 192.168.0.214 | nginx-proxy | LXC on Proxmox                    | Reverse proxy            |
| 192.168.0.222 | proxmox     | HP EliteDesk 800 G4               | Hypervisor               |


## Design decisions

- **Servers use static IPs.** The Tailscale LXC once lost its DHCP lease after a
  network outage and became unreachable. Since then every server gets a static IP.
- **Servers are wired to the switch.** No server uses Wi-Fi directly.

## Known limitations

- The switch is connected to the router over a **Wi-Fi link** (FRITZ!Repeater 1700).
  This is the bottleneck and single point of failure for my whole lab.
  Plan: replacing it with a LAN cable or powerline adapter when my lab gets more filled.
