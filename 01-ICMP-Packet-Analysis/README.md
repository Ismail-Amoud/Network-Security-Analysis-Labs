# LAB 01 — ICMP Packet Analysis & Network Encapsulation

**Status:** Completed — ICMP, ARP and traceroute analysis performed and documented.  
**Type:** Personal Proxmox VE home lab, not a production network.  
**Technical report:** [English](report-en.md) · [Français](rapport-fr.md)

## Objective

Observe and explain IPv4 ICMP echo traffic between Windows 11 and Ubuntu, including Ethernet/IPv4/ICMP encapsulation, MAC-address resolution with ARP, TTL and checksum fields, and traceroute behavior.

## Verified lab context

- Hypervisor: Proxmox VE.
- Source: Windows 11 VM, using a VirtIO virtual network adapter.
- Destination: Ubuntu VM, using a VirtIO adapter.
- Both VMs were shown on the same Linux bridge (`vmbr1`) and on the same /24 IPv4 lab subnet.
- The Proxmox bridge had no physical member port, but was assigned an IPv4 address; the Windows VM had Internet access. **Full isolation was not demonstrated.**
- Wireshark 4.6.9 and Npcap were installed on Windows.

## Commands actually executed

Windows PowerShell:

```powershell
Get-NetAdapter | Select-Object Name, Status, LinkSpeed
Get-NetIPAddress -AddressFamily IPv4 | Select-Object InterfaceAlias, IPAddress, PrefixLength
ping -4 -n 4 <UBUNTU_LAB_IP>
arp -a
tracert -4 -d <UBUNTU_LAB_IP>
```

Ubuntu:

```bash
ip -br addr
ip route
```

Wireshark display filters: `icmp` and `arp`.

`<UBUNTU_LAB_IP>` is a privacy placeholder replacing the lab's actual private IPv4 address.

## Confirmed observations

1. **ICMP:** Four requests and four replies were visible in Wireshark. Windows reported 4 sent, 4 received, 0% lost, average RTT 2 ms for one recorded run.
2. **Encapsulation:** A selected Echo Request was recorded as 74 captured bytes: 14-byte Ethernet II header, 20-byte IPv4 header, 8-byte ICMP Echo header, and 32 bytes of payload. IPv4 Total Length was 60 bytes.
3. **ICMP detail:** The Echo Request had Type 8 / Code 0; a separately examined Echo Reply had Type 0 / Code 0. The observed sequence and identifier were 1 (network byte order).
4. **TTL:** The request's observed IPv4 TTL was 128, and the reply's was 64. These are consistent with different host initial TTL settings, not evidence of a 64-router path.
5. **Checksums:** Wireshark marked the inspected ICMP checksum as Good. IPv4 header checksum validation was disabled/unverified; its validity cannot be claimed.
6. **ARP:** Captured ARP requests asked for the Ubuntu VM's IPv4 address and replies supplied its MAC address. The mapping also appeared in `arp -a`.
7. **Traceroute:** Windows displayed the target on the first traceroute line; three probes were reported as less than 1 ms. This is consistent with the source and destination communicating directly on their shared virtual network.

## Important limitations

- Original screenshots and raw PCAPs have **not** yet been published after redaction.
- These results show network diagnostics, **not** a security compromise or an intrusion investigation.
- Full bridge/VM isolation was not established.
- No routed multi-hop topology was tested; the effect of a router decrementing TTL was explained conceptually, not directly demonstrated.
- IPv4 header checksum correctness was not independently validated.
- The displayed request and reply detail screenshots were from separate captures; do not present them as a single matched request/reply pair.

## Evidence status

| Artifact | Status |
|---|---|
| PowerShell ping output | Observed in session; public redacted screenshot pending |
| Wireshark ICMP list and packet details | Observed in session; public redacted screenshot pending |
| PowerShell ARP cache and Wireshark ARP | Observed in session; public redacted screenshot pending |
| Windows traceroute | Observed in session; public redacted screenshot pending |
| Raw PCAP | Not publicly released |
| Network isolation confirmation | Not established |

## Oral presentation summary

“I captured ICMP Echo Requests and Echo Replies between Windows and Ubuntu in my Proxmox lab. I identified Ethernet, IPv4 and ICMP fields, confirmed ARP resolution and examined TTL and checksum fields. Windows reported four replies and no loss. The destination appeared directly in traceroute. I also documented the limits of the test, including unchecked IPv4 header checksum validation and unverified full network isolation.”



## Packet evidence / Preuves de paquets

- [Observed packet values — English](evidence/observations-en.md)
- [Valeurs des paquets observés — Français](evidence/observations-fr.md)

Original screenshots are reviewed separately before publication; the linked text is a transcription of actual observed fields, not a raw PCAP.
