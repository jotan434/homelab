# Network

Overview of my home network and IP plan.

## Topology

```
Internet
   │
ARRIS router (living room)
   │  Wi-Fi link
FRITZ! repeater
   │  LAN
Switch
   ├── Proxmox host
   ├── DC01 (Windows Server 2025)
   └── other lab devices
```

## Basics

| Setting         | Value                          |
|-----------------|--------------------------------|
| Subnet          | 192.168.0.0/24                 |
| Subnet mask     | 255.255.255.0                  |
| Default gateway | 192.168.0.1                    |
| DNS server      | DC01 (forwarder: 8.8.8.8)      |

## IP plan (static)

| IP            | Host        | Type                              | Purpose                  |
|---------------|-------------|-----------------------------------|--------------------------|
| 192.168.0.1   | Router      | Hardware                          | Gateway                  |
| 192.168.0.206 | uptime-kuma | LXC on Proxmox                    | Monitoring (port 3001)   |
| 192.168.0.212 | tailscale   | LXC on Proxmox                    | Remote access VPN        |
| 192.168.0.222 | proxmox     | HP EliteDesk 800 G4               | Hypervisor               |
| 192.168.0.240 | DC01        | Windows Server 2025 (OptiPlex 3060) | Active Directory, DNS  |

## Design decisions

- **Servers use static IPs.** The Tailscale LXC once lost its DHCP lease after a
  network outage and became unreachable. Since then every server gets a static IP.
- **Servers are wired only**, no Wi-Fi.
- **Static IPs live in the .200+ range** to keep them apart from DHCP clients.
