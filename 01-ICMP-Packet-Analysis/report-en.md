# Technical Report — LAB 01: ICMP Packet Analysis

**Author:** Ismail Amoud  
**Scope:** Controlled personal home-lab network diagnostics  
**Evidence:** User-observed PowerShell and Wireshark screenshots (public redaction pending)

## Executive summary

A Windows 11 VM and an Ubuntu VM were configured on the same Proxmox virtual bridge and /24 IPv4 subnet. A four-request ping generated four observed Echo Requests and four Echo Replies, with no loss reported by the Windows command. Wireshark confirmed the expected Ethernet/IPv4/ICMP structure. The Windows ARP table and packet capture demonstrated IPv4-to-MAC resolution, while traceroute reached Ubuntu directly at its first displayed hop.

## Research questions

1. How is ICMPv4 encapsulated within an Ethernet frame?
2. How do an Echo Request and Echo Reply differ?
3. How does a sender identify a destination MAC address on the local link?
4. What do TTL and the IPv4 header checksum represent?
5. What does a local traceroute show when no router is traversed?

## Environment and observed operations

| Component | Verified detail |
|---|---|
| Hypervisor | Proxmox VE |
| Windows | Windows 11 VM, VirtIO Ethernet |
| Linux | Ubuntu VM, VirtIO Ethernet |
| Link | Both VMs attached to `vmbr1`, same /24 subnet |
| Capture | Wireshark 4.6.9 with Npcap |
| Commands | `ping -4 -n 4`, `arp -a`, `tracert -4 -d` plus interface checks |

## Packet analysis

- Captured Echo Request: **74 bytes** total at Wireshark capture interface = **14 Ethernet + 20 IPv4 + 8 ICMP Echo + 32 data**.
- IPv4 Total Length **60 bytes**, excluding Ethernet header.
- IPv4 protocol number **1** denotes ICMP.
- Request: ICMP **Type 8, Code 0**, TTL **128**.
- Reply: ICMP **Type 0, Code 0**, TTL **64**.
- Observed ICMP identifiers/sequences: **1** in the inspected packets.
- ICMP checksum validated as **Good** by Wireshark on the inspected message(s).
- IPv4 header checksum displayed but validation was **disabled/unverified**. No conclusion about validity.

## Connectivity result

Windows reported **4 packets sent, 4 received, 0 lost (0%)**, with an **average RTT of 2 ms** in the first documented run. A separately inspected Echo Reply reported a **17.916 ms** Wireshark response time; it belonged to a different capture and should not be compared as the same ping series.

## ARP analysis

Captured requests asked which node held the target private IPv4 address, and ARP replies supplied a destination MAC address. The Windows ARP cache contained a dynamic entry for that destination. This demonstrates local IPv4-to-MAC resolution. Wireshark also showed additional ARP traffic unrelated to this particular request/reply pair.

## Traceroute

Windows `tracert -4 -d` displayed the target on its first line with **three RTTs below 1 ms**. There was no intermediate IPv4 router visible, consistent with both VMs sharing one virtual broadcast domain. “Maximum 30 hops” was a search limit, not an observed path length.

## Interpretation

The results demonstrate baseline local-network connectivity, L2/L3 encapsulation, ICMP echo exchanges, ARP resolution and a direct traceroute result. They **do not** prove network isolation, an actual multi-router TTL decrement, an IPv4 checksum validation, or an intrusion.

## Evidence checklist before public release

- [ ] Redact interface GUIDs, VM/nodes names, full MAC addresses and sensitive infrastructure details.
- [ ] Crop screenshots to the relevant fields, retaining packet values and provenance.
- [ ] Verify no public IPs, internal credentials or sensitive host identifiers.
- [ ] Label individual captures by test session and avoid combining unmatched request/reply frames.
- [ ] Publish the reviewed artifacts with captions and document any removal.

## Conclusion

The ICMP and ARP tests and local traceroute were performed successfully. The work is appropriate to describe as a **completed network analysis experiment**, with redacted public evidence and checksum/routing extensions still pending. It is not a production security incident.
