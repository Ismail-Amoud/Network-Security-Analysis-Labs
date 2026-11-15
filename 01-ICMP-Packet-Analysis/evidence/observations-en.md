# Observed evidence — LAB 01

This document transcribes **values actually visible** in Windows PowerShell and Wireshark screenshots. Infrastructure identifiers are replaced with `WIN11_LAB` and `UBUNTU_LAB` placeholders.

> These are reviewed transcriptions, not original packet captures or raw logs. Image publication is pending final redaction review.

## Ping / ICMP

Windows command: `ping -4 -n 4 <UBUNTU_LAB_IP>`.

- Sent: **4**; received: **4**; lost: **0 (0%)**.
- RTT reported by Windows: minimum **0 ms**, maximum **8 ms**, average **2 ms**.
- Wireshark showed **4 Echo Requests and 4 Echo Replies**.
- Captured frames: **74 bytes**.
- Observed request TTL: **128**; reply TTL: **64**.

## Inspected ICMP Echo Request

```text
Frame length: 74 bytes
Ethernet II / IPv4 / ICMP
IPv4 header length: 20 bytes
IPv4 total length: 60 bytes
IPv4 TTL: 128
IPv4 Protocol: ICMP (1)
IPv4 header checksum status: Unverified
ICMP Type: 8 (Echo Request); Code: 0
ICMP checksum status: Good
ICMP identifier (BE): 1
ICMP sequence (BE): 1
ICMP data: 32 bytes
```

## Separately inspected ICMP Echo Reply

```text
IPv4 header length: 20 bytes
IPv4 total length: 60 bytes
IPv4 TTL: 64
IPv4 Protocol: ICMP (1)
IPv4 Header Checksum: 0x3a64 [validation disabled]
IPv4 header checksum status: Unverified
ICMP Type: 0 (Echo Reply); Code: 0
ICMP Checksum: 0x555a [correct]
ICMP checksum status: Good
ICMP identifier (BE): 1
ICMP sequence (BE): 1
Request frame: 144
Response time: 17.916 ms
ICMP data: 32 bytes
```

The request and reply details above were obtained from different capture sessions; they are not represented as one matched pair.

## ARP

Wireshark captured ARP requests asking for the Ubuntu VM's IPv4 address, ARP replies associating it with a virtual NIC MAC address, and a matching dynamic Windows ARP cache entry. MAC values and source IPs are omitted here.

## Traceroute

```text
Tracing route to <UBUNTU_LAB_IP> over a maximum of 30 hops

1    <1 ms    <1 ms    <1 ms    <UBUNTU_LAB_IP>

Trace complete.
```

The destination appeared directly at the first displayed hop, consistent with a shared virtual network.

## Verified limitations

- ICMP checksum validation succeeded for the inspected messages; IPv4 header checksum validation was disabled/unverified.
- Windows had Internet access; complete network isolation was not established.
- No multi-router TTL decrement was directly captured.
- No production incident or compromise is claimed.
- Redacted images remain under final review and are not yet published.
