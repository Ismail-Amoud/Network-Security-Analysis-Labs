# Technical Report — LAB 01: ICMP Packet Analysis

**Author:** Ismail Amoud  
**Status:** Completed  
**Platform:** Proxmox VE · Windows 11 · Ubuntu · Wireshark

## Executive summary

An ICMPv4 connectivity test between Windows 11 and Ubuntu virtual machines was captured and analyzed with Wireshark. Windows sent four Echo Requests and received four replies, reporting **0% packet loss**. The analysis covered Ethernet/IPv4/ICMP encapsulation, TTL values, checksum fields, IPv4-to-MAC resolution using ARP, and a Windows traceroute that reached the destination on its first displayed hop.

## Objectives

- Capture and interpret ICMP Echo Requests and Echo Replies.
- Identify Ethernet II, IPv4 and ICMP fields and explain encapsulation.
- Observe ARP resolution and interpret a dynamic ARP cache entry.
- Interpret TTL and IPv4/ICMP checksum fields.
- Use traceroute to examine a directly reachable host.

## Setup and procedure

The test was performed with a Windows 11 VM and an Ubuntu VM attached to the same Proxmox virtual bridge, within the same private IPv4 /24 subnet. Both used VirtIO virtual network adapters. Wireshark 4.6.9 with Npcap captured traffic on Windows.

Windows commands included `Get-NetAdapter`, `Get-NetIPAddress`, `ping -4 -n 4`, `arp -a` and `tracert -4 -d`. Ubuntu commands included `ip -br addr` and `ip route`. Wireshark display filters `icmp` and `arp` were used to inspect the captured traffic.

## Results

### 1. ICMP connectivity

Windows reported **4 sent, 4 received, 0 lost**. For that run, Windows displayed an **average round-trip time of 2 ms**, a minimum of **0 ms** (rounded display) and a maximum of **8 ms**. Wireshark displayed four requests and four replies.

### 2. Ethernet/IPv4/ICMP encapsulation

One inspected Echo Request was **74 bytes** as captured:

| Component | Size |
|---|---:|
| Ethernet II header | 14 bytes |
| IPv4 header (no options) | 20 bytes |
| ICMP Echo header | 8 bytes |
| ICMP payload | 32 bytes |
| **Captured total** | **74 bytes** |

The IPv4 `Total Length` was **60 bytes**, excluding the Ethernet header. Ethernet's preamble and any noncaptured FCS are not included in this breakdown.

### 3. IPv4 and ICMP fields

The inspected request had **ICMP Type 8 / Code 0** and TTL **128**. A reply inspected in a separate capture had **ICMP Type 0 / Code 0** and TTL **64**. Both displayed an identifier and sequence number of **1** in network byte order; these matching values across captures do **not** establish a matched request/reply pair.

The request and reply TTL values are consistent with their respective hosts' common initial settings. They do **not** represent a drop from 128 to 64 across 64 routers.

Wireshark reported **Good** for the inspected ICMP checksum. The inspected IPv4 header checksum field was visible, but its status was **Unverified** because validation was disabled. For the separately examined reply, Wireshark displayed an IPv4 header checksum of `0x3a64`; it was **not validated**.

### 4. ARP address resolution

ARP requests asked which station held the Ubuntu VM's private IPv4 address. ARP replies associated that address with the Ubuntu virtual interface's MAC address. Windows `arp -a` also showed a dynamic entry for the destination.

This demonstrates IPv4-to-MAC address resolution on the local Ethernet segment. ARP messages are transported directly in Ethernet frames, rather than encapsulated in IPv4 packets.

### 5. Traceroute

Windows `tracert -4 -d` showed the destination on the **first displayed hop**, with three round-trip measurements reported as **less than 1 ms**. No intermediate IP router was observed, consistent with the shared local network. The tool's **30-hop maximum** is a probe limit, not the number of routers traversed.

## Interpretation

The lab establishes the observed connectivity and illustrates local Ethernet delivery, IPv4 and ICMP encapsulation, ARP resolution and traceroute interpretation. The TTL decrement performed by forwarding routers was studied conceptually rather than observed on a multi-hop path. Full network isolation was not established because Windows was also able to access the Internet.

The analyzed request and reply examples were taken from different capture sessions. Their measurements should not be treated as one paired exchange, and the 17.916 ms response time from one separately inspected reply should not be compared to the 2 ms average from the earlier run.

## Conclusion

The ICMP, ARP and traceroute exercises were successfully performed and documented. This completed lab demonstrates practical packet-analysis, network-protocol interpretation and connectivity-troubleshooting skills with Wireshark and Windows networking tools.

**Supporting field records:** [English evidence notes](evidence/observations-en.md) · [Notes en français](evidence/observations-fr.md).
