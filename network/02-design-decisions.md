# 2 – Design decisions

## Servers use static IPs

**Why:** my Tailscale container once lost its DHCP lease after a network outage.
It came back without an address and showed *"Network unreachable"*, so remote access to the
whole lab was gone. Since then every server and container gets a static IP.

**Trade-off:** static IPs have to be tracked by hand. That's why they live in a fixed range
(see [Address scheme](01-address-scheme.md)).

## Static and DHCP ranges don't overlap

**Why:** the router doesn't know about my static IPs. If its DHCP range reached into the
server range, it could hand out an address that is already taken. The DHCP range ends
below `.200`, the servers start at `.200`.

## Servers are wired to the switch

**Why:** Wi-Fi drops and fluctuates. No server uses Wi-Fi directly.
The honest catch: the switch itself still reaches the router over a Wi-Fi link
(see [Limitations](04-limitations.md)).

## The router is the DNS server (for now)

**Why:** simplest setup that works. The downside is that the router doesn't know my lab names,
so they only work via `/etc/hosts` on my workstation.
See [Nginx Proxy Manager → Name resolution](../nginx-proxy-manager/03-name-resolution.md).
