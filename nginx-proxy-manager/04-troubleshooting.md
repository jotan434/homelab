# 4 – Testing & troubleshooting

Test in the same order a request travels: **name → NPM → service.**
The first step that fails tells you where the problem is.

## Step 1 – Does the name resolve to NPM?

```bash
getent hosts kuma.home.arpa
# expected: <npm-ip>   kuma.home.arpa
```

Use `getent`, not `nslookup` or `dig`. `getent` uses the same lookup order as the browser
(including `/etc/hosts`). `nslookup` and `dig` ask the DNS server directly and ignore `/etc/hosts`.

## Step 2 – Does NPM answer and forward?

```bash
curl -I http://kuma.home.arpa
# -I = fetch only the response headers
```

Expected (shortened):

```
HTTP/1.1 302 Found
Server: openresty
X-Served-By: kuma.home.arpa
```

| Header                 | Meaning                                                        |
|------------------------|----------------------------------------------------------------|
| `302 Found`            | Uptime Kuma answered and redirects to its start page           |
| `Server: openresty`    | The answer came through NPM                                    |
| `X-Served-By`          | Which proxy host in NPM matched                                |

## Step 3 – Is the service reachable directly?

```bash
curl -I http://<kuma-ip>:3001
```

If this fails, the problem is the service, not NPM.

## Errors

| Symptom                                           | Likely cause                                               | Fix                                                          |
|---------------------------------------------------|------------------------------------------------------------|--------------------------------------------------------------|
| Browser: *"DNS address could not be found"*       | Name missing in `/etc/hosts` / DNS, or typo                | Step 1. **Happened to me:** I renamed `kuma.home` → `kuma.home.arpa` in NPM but not in `/etc/hosts` |
| `502 Bad Gateway`                                 | NPM can't reach the service: wrong IP, port or scheme, or service down | Step 3, then check the proxy host fields          |
| NPM *"Congratulations"* default page              | NPM got the request, but no proxy host matches the name    | Domain name in the proxy host must match exactly            |
| Uptime Kuma: *"Cannot connect to the socket server"* | Websockets Support is off                               | Enable it in the proxy host                                 |
| Admin UI on port 81 not reachable                 | Services stopped                                           | See below                                                    |

## Checking the services (inside the container)

```bash
systemctl status npm openresty   # are both services running?
journalctl -u npm -e             # latest log lines of the NPM backend
```
