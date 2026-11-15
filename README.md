# Network Security Analysis Labs

Hands-on, personally operated network analysis and defensive cybersecurity laboratories.

**Author:** Ismail Amoud | **Environment:** Personal Proxmox VE home lab | **Languages:** English / Français

## Documented lab

| Lab | Focus | Status | Documentation |
|---|---|---|---|
| 01 | ICMP Packet Analysis & Network Encapsulation | Completed — network analysis documented | [English](01-ICMP-Packet-Analysis/README.md) · [Français](01-ICMP-Packet-Analysis/README-FR.md) |

## Lab 01 — Confirmed observations

- Windows 11 sent four ICMP Echo Requests to an Ubuntu VM; four Echo Replies were captured, with 0% reported loss.
- Wireshark displayed Ethernet II / IPv4 / ICMP encapsulation and corresponding request/reply fields.
- ARP requests and replies were observed; the destination's IPv4-to-MAC mapping was present in the Windows ARP cache.
- Windows `tracert` reached the Ubuntu destination on its first displayed hop.
- The IPv4 header checksum field was inspected, **but its correctness was not validated**.
- Complete network isolation was **not established**: the Windows VM had Internet connectivity.

See the individual lab documentation for detailed methodology, limitations and planned evidence review.

## Publication and privacy

- All work is from a **personal lab**, not paid production SOC experience.
- Raw PCAP files, internal identifiers, MAC addresses, public IPs, hostnames, infrastructure configuration and other sensitive details must be reviewed before publishing.
- No screenshots or raw packet captures are published here until they have been redacted and checked.
- All recorded results are separated from expected results and interpretations.
