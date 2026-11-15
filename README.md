# Network Security Analysis Labs

**Ismail Amoud** · Hands-on network traffic analysis and defensive networking projects developed in a personal Proxmox VE environment.

## Projects

| Lab | Focus | Status | Documentation |
|---|---|---|---|
| **01** | **ICMP Packet Analysis & Network Encapsulation** — Wireshark, IPv4, ARP, TTL and traceroute | **Completed** | [English](01-ICMP-Packet-Analysis/README.md) · [Français](01-ICMP-Packet-Analysis/README-FR.md) |

## LAB 01 — Highlights

- Captured **four ICMP Echo Requests and four Echo Replies** between Windows 11 and Ubuntu; Windows reported **0% packet loss**.
- Examined Ethernet II, IPv4 and ICMP fields in Wireshark, including a **74-byte captured frame**.
- Observed ARP request/reply traffic and a matching dynamic ARP entry.
- Used Windows traceroute to confirm that the Ubuntu VM appeared at the first displayed hop.

**Explore:** [Technical report (EN)](01-ICMP-Packet-Analysis/report-en.md) · [Rapport technique (FR)](01-ICMP-Packet-Analysis/rapport-fr.md) · [Recorded observations](01-ICMP-Packet-Analysis/evidence/observations-en.md)

All results are from a personal lab. The evidence folder contains field transcriptions based on actual captures; it does not represent raw packet capture files.


## Plain-text evidence

LAB 01 includes [sanitized English observations](01-ICMP-Packet-Analysis/evidence/icmp-analysis.txt) and [French observations](01-ICMP-Packet-Analysis/evidence/analyse-icmp.txt). These are transcripts of actual observed results, not exported logs, screenshots or PCAPs.
