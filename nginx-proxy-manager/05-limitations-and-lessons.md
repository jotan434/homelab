# 5 – Limitations & lessons learned

## Known limitations

| Limitation | Impact | Possible fix |
|------------|--------|--------------|
| Names only work on my workstation | Other devices still need `IP:port` | Local DNS server (planned) |
| HTTP only, no TLS | Traffic in the LAN is unencrypted, including the Uptime Kuma login | Certificate via DNS challenge with a real domain, or an own local CA |
| NPM is a single point of failure for names | If the container is down, `kuma.home.arpa` is dead | `IP:port` still works as fallback |
| Admin UI (port 81) runs over HTTP | Password is sent unencrypted in the LAN | Keep it LAN-only, never forward port 81 on the router |

## Lessons learned

- **A name lives in two places.** NPM decides *where a request goes*. DNS or `/etc/hosts`
  decides *how the client reaches NPM*. Renaming means changing both.
- **"Online" in NPM is not "works for clients".** It only means NPM can reach the service.
- **Container DNS is not client DNS.** The DNS setting of the LXC container is for the
  container's own lookups. It does not help any client find a name.
- **Use reserved domains.** I started with `.home` and had to rename everything to `.home.arpa`.
  Picking the right name at the start saves work.
