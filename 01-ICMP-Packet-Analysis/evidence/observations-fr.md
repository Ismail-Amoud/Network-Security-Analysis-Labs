# Preuves observées — LAB 01

Ce dossier documente les **données réellement visibles** dans les captures de Windows PowerShell et Wireshark. Les identifiants du réseau ont été remplacés par des rôles : `WIN11_LAB` et `UBUNTU_LAB`.

> Les transcriptions suivantes ne sont pas des journaux bruts exportés, ni des captures originales. Elles reprennent les champs relevés sur les captures du laboratoire. Les images anonymisées seront ajoutées après validation finale de leur publication.

## 1. Ping et ICMP

Commande Windows : `ping -4 -n 4 <UBUNTU_LAB_IP>`.

- Envoyés : **4**
- Reçus : **4**
- Perdus : **0 (0 %)**
- RTT minimal : **0 ms** affiché
- RTT maximal : **8 ms**
- RTT moyen : **2 ms**
- Wireshark : **4 Echo Requests + 4 Echo Replies**
- Longueur de trame affichée : **74 octets**
- TTL des demandes observées : **128**
- TTL des réponses observées : **64**

## 2. Trame ICMP Echo Request

Extrait des champs du paquet examiné :

```text
Ethernet II
Internet Protocol Version 4:
    Version: 4
    Header Length: 20 bytes
    Total Length: 60
    Time to Live: 128
    Protocol: ICMP (1)
    Header checksum status: Unverified
ICMP:
    Type: Echo (ping) request (8)
    Code: 0
    Checksum Status: Good
    Identifier (BE): 1
    Sequence Number (BE): 1
    Data: 32 bytes
Frame Length: 74 bytes
```

Ces valeurs sont issues d'une demande spécifique ; elles ne doivent pas être artificiellement appariées à une réponse provenant d'une autre session.

## 3. Echo Reply examiné

```text
Internet Protocol Version 4:
    Header Length: 20 bytes
    Total Length: 60
    Time to Live: 64
    Protocol: ICMP (1)
    Header Checksum: 0x3a64 [validation disabled]
    Header checksum status: Unverified
ICMP:
    Type: Echo (ping) reply (0)
    Code: 0
    Checksum: 0x555a [correct]
    Checksum Status: Good
    Identifier (BE): 1
    Sequence Number (BE): 1
    Request frame: 144
    Response time: 17.916 ms
    Data: 32 bytes
```

Cet exemple provient d'une capture distincte de celle de la demande détaillée.

## 4. ARP

- Des messages **Who has <UBUNTU_LAB_IP>? Tell <WIN11_LAB_IP>** ont été observés.
- Les réponses associent l'adresse IPv4 d'Ubuntu à l'adresse MAC de son interface virtuelle.
- `arp -a` indiquait une correspondance dynamique entre l'IPv4 d'Ubuntu et une adresse MAC.
- Le filtre Wireshark utilisé était `arp`.

## 5. Traceroute

```text
Tracing route to <UBUNTU_LAB_IP> over a maximum of 30 hops

1    <1 ms    <1 ms    <1 ms    <UBUNTU_LAB_IP>

Trace complete.
```

**Interprétation :** la destination était joignable directement sur le même réseau virtuel, sans routeur intermédiaire observé.

## 6. Limites vérifiées

- Le checksum ICMP examiné était correct ; **le checksum de l'en-tête IPv4 n'a pas été validé**.
- L'accès Internet était possible depuis Windows ; **l'isolation complète du réseau n'a pas été établie**.
- Aucun test de routage à plusieurs sauts n'a été effectué.
- Aucune attaque, compromission ou activité de production n'a été simulée ou constatée.

## Statut des images

Les six captures préparées nécessitent une dernière inspection visuelle après masquage des adresses et autres identifiants avant toute mise en ligne. Ne pas publier les captures brutes.
