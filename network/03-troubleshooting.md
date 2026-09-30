# 3 – Troubleshooting

## Check from the bottom up

Start at the lowest layer. The first step that fails is where the problem is.

| Step | Question                        | Command (Linux)              | Windows              |
|------|---------------------------------|------------------------------|----------------------|
| 1    | Do I have an IP address?        | `ip a`                       | `ipconfig`           |
| 2    | Is the default route correct?   | `ip route`                   | `ipconfig`           |
| 3    | Can I reach the gateway?        | `ping -c 3 <router-ip>`      | `ping <router-ip>`   |
| 4    | Can I reach the internet by IP? | `ping -c 3 1.1.1.1`          | `ping 1.1.1.1`       |
| 5    | Does DNS work?                  | `getent hosts example.com`   | `nslookup example.com` |
| 6    | Does the service answer?        | `curl -I http://<host>:<port>` | Browser            |

If step 4 works but step 5 fails, it's DNS. If step 3 fails, don't look at DNS at all.

## Errors I actually ran into

| Error                                           | Cause                                        | Fix                                              |
|-------------------------------------------------|----------------------------------------------|--------------------------------------------------|
| LXC: *"Network unreachable"*                    | Container lost its DHCP lease                | Static IP (see [Address scheme](01-address-scheme.md)) |
| `ping` → *Request timed out*                    | Wrong gateway configured                     | Gateway must be the router address (`.1`)        |
| `curl: (6) Could not resolve host`              | DNS server wrong or unreachable              | Check DNS setting, test with a public DNS server |
| `curl: (60) SSL certificate problem`            | System date was wrong, so certificates looked invalid | Correct the system date and time        |
| Browser: *"DNS address could not be found"* for a lab name | Name missing in `/etc/hosts`      | See [NPM troubleshooting](../nginx-proxy-manager/04-troubleshooting.md) |

## Useful commands

| Command            | What it shows                                  |
|--------------------|------------------------------------------------|
| `ip a`             | Own interfaces and IP addresses                |
| `ip route`         | Routing table, including the default gateway   |
| `arp -a`           | Devices the machine has recently talked to     |
| `getent hosts NAME`| What the machine resolves a name to (incl. `/etc/hosts`) |
| `curl -I URL`      | Only the HTTP response headers                 |
