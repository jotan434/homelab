# 3 – Name resolution

The part that confused me most: **a name has to exist in two places.**

| Place                 | Question it answers                                               |
|-----------------------|-------------------------------------------------------------------|
| DNS or `/etc/hosts`   | "Which IP is `kuma.home.arpa`?" → must answer with the **NPM** IP |
| NPM proxy host        | "I got a request for `kuma.home.arpa`. Where do I send it?"       |

The name must point to NPM, not to the service. If it points to the service IP,
NPM is skipped and the browser tries port 80 on the service directly.

## My current setup: `/etc/hosts`

There is no local DNS server in my lab yet, so the names are in the hosts file of my workstation:

```
<npm-ip>   kuma.home.arpa
```

| OS      | File                                    | Edit with                          |
|---------|-----------------------------------------|------------------------------------|
| Linux   | `/etc/hosts`                            | `sudo nano /etc/hosts`             |
| Windows | `C:\Windows\System32\drivers\etc\hosts` | Notepad, started as administrator |

**Why this works:** on Linux, the `hosts:` line in `/etc/nsswitch.conf` lists `files`
before `dns`. The hosts file is checked first, the DNS server only if nothing is found.

**Downside:** only this one machine knows the name. Every other device still needs `IP:port`.

## Planned: local DNS server

A local DNS server (for example Pi-hole or AdGuard Home) would hold one record per name,
all pointing to the NPM IP. If the router hands out that DNS server via DHCP,
every device in the network can use the names without editing any file.

## Why `.home.arpa`

| Domain       | Problem                                                                 |
|--------------|-------------------------------------------------------------------------|
| `.home`      | Not reserved. What I used first, switched away from it                  |
| `.local`     | Reserved for mDNS (Avahi / Bonjour). Mixing it with DNS causes odd lookups |
| `.home.arpa` | Officially reserved for home networks ([RFC 8375](https://www.rfc-editor.org/rfc/rfc8375)) ✔ |
