# Nginx Proxy Manager

Reverse proxy that lets me open lab services by name instead of `IP:port`.

## Overview

| Setting        | Value                              |
|----------------|------------------------------------|
| Runs on        | LXC container on Proxmox           |
| IP             | 192.168.0.214                      |
| Admin UI       | http://192.168.0.214:81            |
| Container DNS  | Uses the Proxmox host settings     |

## Proxy hosts

| Name             | Destination                  | Service      | SSL        |
|------------------|------------------------------|--------------|------------|
| kuma.home.arpa   | http://192.168.0.206:3001    | Uptime Kuma  | HTTP only  |

## How a request flows

```
Browser: http://kuma.home.arpa
   │
   ▼
/etc/hosts on my workstation  →  kuma.home.arpa = 192.168.0.214
   │
   ▼
Nginx Proxy Manager (192.168.0.214:80)  →  looks up the proxy host "kuma.home.arpa"
   │
   ▼
Uptime Kuma (192.168.0.206:3001)
```

## Name resolution

There is no local DNS server in the lab yet. The names are set in `/etc/hosts`
on my workstation:

```
192.168.0.214   kuma.home.arpa
```

`/etc/hosts` is checked before any DNS server, so this works without touching the router.

## Why `.home.arpa`

`.home.arpa` is reserved for home networks (RFC 8375). I started with `.home`,
which is not an officially reserved domain, and switched to avoid conflicts later.

## Testing

```bash
getent hosts kuma.home.arpa      # should print 192.168.0.214
curl -I http://kuma.home.arpa    # should return HTTP 302 from openresty (NPM)
```

## Lessons learned

- **A name lives in two places.** When I renamed `kuma.home` to `kuma.home.arpa` in NPM,
  the browser showed "DNS address could not be found", because `/etc/hosts` still had the
  old name. NPM decides where a request goes, DNS / `/etc/hosts` decides how you reach NPM.
- **"Online" in NPM** only means NPM can reach the backend service.
  It says nothing about whether clients can resolve the name.

## Known limitations

- Names only work on my workstation (`/etc/hosts`). Other devices still need `IP:port`.
  A local DNS server (e.g. Pi-hole or AdGuard Home) would fix this for the whole network.
- HTTP only, no TLS.
