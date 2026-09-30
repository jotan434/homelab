# 4 – Limitations

| Limitation | Impact | Plan |
|------------|--------|------|
| The switch reaches the router over a **Wi-Fi link** (repeater) | Bottleneck and single point of failure for the whole lab. If the Wi-Fi link drops, every server is offline | Replace it with a LAN cable or powerline adapter once the lab grows |
| **No local DNS server** | Lab names like `kuma.home.arpa` only work on my workstation, other devices need `IP:port` | Local DNS server (Pi-hole or AdGuard Home) |
| **Static IPs are tracked by hand** | A new server could get an address that is already used | Check with `ping` before assigning, keep the list in my notes up to date |
