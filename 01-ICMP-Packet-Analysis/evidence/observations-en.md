# LAB 01 — Recorded packet observations

**Source:** Values transcribed from actual Windows PowerShell and Wireshark screenshots obtained during this lab.  
**Format:** Technical transcription; not a raw PCAP, system-exported log or image.

Infrastructure addresses, MAC addresses and interface identifiers have been omitted from the transcription where unnecessary.

## Windows ping

Command: `ping -4 -n 4 <UBUNTU_IP>`

| Metric | Observed |
|---|---|
| Packets sent | 4 |
| Packets received | 4 |
| Lost | 0 (0%) |
| Minimum RTT | 0 ms (rounded display) |
| Maximum RTT | 8 ms |
| Average RTT | 2 ms |
| Wireshark ICMP messages | 4 requests, 4 replies |

## Wireshark: inspected Echo Request

```text
Frame length: 74 bytes
Ethernet II / IPv4 / ICMP
IPv4 Version: 4
IPv4 Header Length: 20 bytes
IPv4 Total Length: 60 bytes
IPv4 Protocol: ICMP (1)
IPv4 TTL: 128
IPv4 Header Checksum: 0x0000 [validation disabled]
IPv4 Header Checksum Status: Unverified
ICMP Type: 8 (Echo Request)
ICMP Code: 0
ICMP Checksum: 0x4d5a [correct]
ICMP Checksum Status: Good
ICMP Identifier (BE): 1
ICMP Sequence Number (BE): 1
ICMP Data: 32 bytes
```

**Frame number displayed in that capture:** 15857. Wireshark linked its response to frame 15858 in that same capture.

## Wireshark: separately inspected Echo Reply

```text
IPv4 Header Length: 20 bytes
IPv4 Total Length: 60 bytes
IPv4 Protocol: ICMP (1)
IPv4 TTL: 64
IPv4 Header Checksum: 0x3a64 [validation disabled]
IPv4 Header Checksum Status: Unverified
ICMP Type: 0 (Echo Reply)
ICMP Code: 0
ICMP Checksum: 0x555a [correct]
ICMP Checksum Status: Good
ICMP Identifier (BE): 1
ICMP Sequence Number (BE): 1
ICMP Request Frame: 144
ICMP Response Time: 17.916 ms
ICMP Data: 32 bytes
```

**Important:** This reply comes from a later capture session. Its corresponding request was frame 144 **in that capture**, not frame 15857 of the earlier capture. The two field excerpts are illustrative comparisons, not a single matched pair.

An IPv4 checksum displayed as `0x0000` in an outgoing capture may reflect capture/offload behavior; no conclusion about its validity is possible without further validation.

## ARP

- Windows queried the MAC address associated with the Ubuntu VM's private IPv4 address.
- Wireshark captured ARP requests and replies.
- Windows `arp -a` contained the destination address as a dynamic IPv4-to-MAC entry.

## Traceroute

```text
Tracing route to <UBUNTU_IP> over a maximum of 30 hops

1    <1 ms    <1 ms    <1 ms    <UBUNTU_IP>

Trace complete.
```

The destination was reached at the first displayed hop; no intermediate router was observed.

## Scope note

The records demonstrate the packet and connectivity observations above. Full network isolation was not established and no multi-router route was captured. The accompanying report provides technical interpretation.
