# LAB 01 — ICMP Packet Analysis & Network Encapsulation

**Status: Completed** · **Environment:** Proxmox VE, Windows 11, Ubuntu, Wireshark 4.6.9 and Npcap.

## Overview

This hands-on lab examines how ICMP Echo traffic travels between Windows and Ubuntu virtual machines on the same virtual Ethernet network. It demonstrates packet capture, Ethernet/IPv4/ICMP encapsulation, ARP address resolution and local-route diagnostics.

## Completed work

| Area | Observed result |
|---|---|
| Connectivity | 4 ICMP Echo Requests and 4 Echo Replies; **0% loss** |
| Wireshark | A 74-byte Echo Request frame decoded as Ethernet II → IPv4 → ICMP → payload |
| IPv4 | 20-byte header, 60-byte IPv4 Total Length, protocol **1 (ICMP)** |
| ICMP | **Type 8 / Code 0** request; **Type 0 / Code 0** reply |
| TTL | **128** on an inspected Windows request; **64** on an inspected Ubuntu reply |
| Checksums | Inspected ICMP checksum reported **Good**; IPv4 header checksum validation was **not enabled** |
| ARP | Request/reply exchanges and a corresponding dynamic Windows ARP cache entry |
| Traceroute | Target visible at the first displayed hop, with three RTT measurements below 1 ms |

## Network environment

- Windows 11 and Ubuntu virtual machines with VirtIO adapters on one Proxmox Linux bridge.
- Both machines used addresses within the same private IPv4 /24 subnet.
- Their direct communication was observed. The network was not treated as fully isolated: Internet access from Windows was also confirmed.
- No router between the two machines was observed in this local test.

## Commands and analysis

Commands executed in Windows PowerShell:

```powershell
Get-NetAdapter | Select-Object Name, Status, LinkSpeed
Get-NetIPAddress -AddressFamily IPv4 | Select-Object InterfaceAlias, IPAddress, PrefixLength
ping -4 -n 4 <UBUNTU_IP>
arp -a
tracert -4 -d <UBUNTU_IP>
```

Commands executed on Ubuntu:

```bash
ip -br addr
ip route
```

Wireshark display filters: `icmp`, `arp`.

**Note:** `<UBUNTU_IP>` is a documentation placeholder, not literal syntax entered into PowerShell.

## Documentation

- [Detailed technical report — English](report-en.md)
- [Rapport technique détaillé — Français](rapport-fr.md)
- [Observed packet fields — English](evidence/observations-en.md)
- [Champs observés — Français](evidence/observations-fr.md)

## Conclusion

The lab successfully demonstrated ICMP connectivity, IPv4 encapsulation, Ethernet address resolution and traceroute analysis in a virtual network. Packet findings are supported by recorded PowerShell output and Wireshark observations.

**Technical scope:** IPv4 header checksum correctness was not validated, and TTL decrement across a router was not tested. Request and reply detail examples drawn from separate capture sessions are labeled accordingly.
