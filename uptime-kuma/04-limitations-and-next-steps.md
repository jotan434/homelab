# 4 – Limitations & next steps

## Known limitations

| Limitation | Why it matters | Possible fix |
|------------|----------------|--------------|
| **Ping only** | A device can answer ping while its service is broken (e.g. the Proxmox web UI) | `HTTP(s)` monitors for web services |
| **Not everything is monitored** | Nginx Proxy Manager and n8n have no monitor yet. If the proxy dies, `kuma.home.arpa` is gone and I would not get an alert | Add monitors for both |
| **Uptime Kuma runs on the host it watches** | If the Proxmox host goes down, Uptime Kuma goes down with it and **no** email is sent | A second, external check (another device or an online service) |
| **Alerts need the internet** | If the internet is down, the "Internet is down" email cannot leave the house. It arrives after the connection is back | A second alert channel that works without my internet, or accept the delay |
| **Alert only tested with the Test button** | The SMTP settings work, but a real outage has not triggered an email yet | Real test (see below) |
| **Tailscale has 0 retries** | One lost ping is enough for an alert, so false alarms are possible | Raise to 1–2 if it gets noisy |

## Next steps

- [ ] **Real alert test:** stop the Tailscale container in Proxmox, wait for the email, start it again
- [ ] Monitor Nginx Proxy Manager and n8n
- [ ] `HTTP(s)` monitor for the Proxmox web interface
- [ ] WhatsApp alerts as a second channel
