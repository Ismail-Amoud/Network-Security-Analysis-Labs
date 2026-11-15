# LAB 01 — Relevés des observations réseau

**Source :** valeurs relevées dans les véritables captures Windows PowerShell et Wireshark réalisées pendant le laboratoire.  
**Format :** transcription technique ; ni PCAP brut, ni journal exporté, ni capture d'écran.

Les adresses d'infrastructure, MAC et identifiants d'interface ont été omis lorsque leur valeur exacte n'était pas nécessaire.

## Ping Windows

Commande : `ping -4 -n 4 <IP_UBUNTU>`

| Indicateur | Valeur observée |
|---|---|
| Paquets envoyés | 4 |
| Paquets reçus | 4 |
| Perte | 0 (0 %) |
| RTT minimum | 0 ms (affichage arrondi) |
| RTT maximum | 8 ms |
| RTT moyen | 2 ms |
| Messages ICMP dans Wireshark | 4 demandes, 4 réponses |

## Wireshark : Echo Request examiné

```text
Frame Length: 74 bytes
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

**Numéro de trame dans cette capture :** 15857. Wireshark indiquait la trame 15858 comme sa réponse, au sein de cette même capture.

## Wireshark : Echo Reply examiné séparément

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

**Important :** cette réponse provient d'une autre capture. Sa demande correspondante était la trame 144 **dans cette autre capture**, et non la trame 15857 de la première. Les deux relevés servent à comparer les champs, pas à présenter un échange unique apparié.

Un checksum IPv4 affiché à `0x0000` sur un paquet sortant peut résulter du mécanisme de capture ou du déchargement du calcul ; sa validité n'a pas été vérifiée.

## ARP

- Windows a recherché la MAC correspondant à l'adresse IPv4 privée de la VM Ubuntu.
- Wireshark a capturé des requêtes et réponses ARP.
- `arp -a` affichait une entrée dynamique IPv4–MAC pour la destination.

## Traceroute

```text
Tracing route to <IP_UBUNTU> over a maximum of 30 hops

1    <1 ms    <1 ms    <1 ms    <IP_UBUNTU>

Trace complete.
```

La destination apparaît dès le premier saut affiché ; aucun routeur intermédiaire n'a été observé.

## Précision sur le périmètre

Ces relevés décrivent les observations réseau effectuées. L'isolation complète n'a pas été établie et aucune route à plusieurs sauts n'a été capturée. L'interprétation est détaillée dans le rapport.
