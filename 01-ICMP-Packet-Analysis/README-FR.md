# LAB 01 — Analyse ICMP et encapsulation réseau

**Statut :** Terminé — Analyse ICMP, ARP et traceroute réalisée et documentée.  
**Cadre :** laboratoire personnel Proxmox VE, et non réseau de production.  
**Rapports techniques :** [Français](rapport-fr.md) · [English](report-en.md)

## Objectif

Capturer et expliquer les échanges ICMPv4 entre Windows 11 et Ubuntu : encapsulation Ethernet/IPv4/ICMP, résolution ARP, TTL, sommes de contrôle et traceroute.

## Environnement vérifié

- Hyperviseur : Proxmox VE.
- VM source : Windows 11, interface virtuelle VirtIO.
- VM cible : Ubuntu, interface virtuelle VirtIO.
- Deux VM sur le même bridge `vmbr1` et dans le même sous-réseau IPv4 /24.
- Bridge sans port physique, mais avec adresse IPv4 sur l'hôte ; accès Internet fonctionnel depuis Windows. **Isolation complète non démontrée.**
- Wireshark 4.6.9 avec Npcap sur Windows.

## Commandes exécutées

Windows PowerShell :

```powershell
Get-NetAdapter | Select-Object Name, Status, LinkSpeed
Get-NetIPAddress -AddressFamily IPv4 | Select-Object InterfaceAlias, IPAddress, PrefixLength
ping -4 -n 4 <IP_UBUNTU_LAB>
arp -a
tracert -4 -d <IP_UBUNTU_LAB>
```

Ubuntu :

```bash
ip -br addr
ip route
```

Filtres Wireshark : `icmp` et `arp`. Les adresses réelles sont remplacées dans ce dépôt par des marqueurs anonymisés.

## Observations confirmées

1. **ICMP :** quatre demandes Echo Request et quatre réponses Echo Reply observées. Windows a affiché 4 paquets envoyés, 4 reçus, 0 % de perte et un temps aller-retour moyen de 2 ms pour une série.
2. **Encapsulation :** une demande comprenait 74 octets capturés : 14 Ethernet, 20 IPv4, 8 ICMP et 32 de données. `Total Length` IPv4 valait 60 octets.
3. **Types ICMP :** demande Type 8 / Code 0 ; réponse Type 0 / Code 0 ; identifiant et séquence observés à 1 en ordre réseau.
4. **TTL :** 128 dans la demande Windows, 64 dans la réponse Ubuntu. Cela correspond à des réglages initiaux distincts, pas à la traversée démontrée de 64 routeurs.
5. **Sommes de contrôle :** checksum ICMP marqué correct ; validation du checksum d'en-tête IPv4 désactivée/non vérifiée.
6. **ARP :** des demandes « Who has…? » et des réponses IPv4–MAC ont été capturées ; le cache `arp -a` présentait également l'association.
7. **Traceroute :** la destination a été atteinte dès la première ligne de résultat, avec trois mesures inférieures à 1 ms, sans routeur intermédiaire observé.

## Limites

- Les captures originales et fichiers PCAP ne sont pas encore publiés après contrôle de confidentialité.
- Il s'agit de diagnostic réseau, pas d'une compromission ou investigation d'incident.
- L'isolation réseau complète n'a pas été confirmée.
- Aucun routage à plusieurs sauts n'a été testé ; la décrémentation du TTL par les routeurs a été expliquée théoriquement.
- La validité du checksum IPv4 n'a pas été vérifiée.
- Les captures détaillées de la demande et de la réponse analysées provenaient de séries distinctes : elles ne constituent pas une paire appariée unique.

## État des preuves

| Élément | Situation |
|---|---|
| Résultat ping PowerShell | Observé ; capture anonymisée publique en attente |
| Wireshark ICMP et détails du paquet | Observés ; captures publiques en attente |
| ARP dans PowerShell et Wireshark | Observé ; captures publiques en attente |
| Traceroute | Observé ; capture publique en attente |
| Fichier PCAP brut | Non publié |
| Isolation complète | Non démontrée |

## Présentation orale

« Dans mon laboratoire Proxmox, j'ai capturé des échanges ICMP entre Windows 11 et Ubuntu. J'ai analysé l'encapsulation Ethernet/IPv4/ICMP, observé la résolution ARP et étudié le TTL et les champs de checksum. Quatre réponses ont été reçues sans perte. Traceroute a atteint directement la destination. J'ai également documenté les limites de la démonstration, notamment la validation non effectuée du checksum IPv4 et l'isolation réseau non confirmée. »


## Packet evidence / Preuves de paquets

- [Observed packet values — English](evidence/observations-en.md)
- [Valeurs des paquets observés — Français](evidence/observations-fr.md)

Original screenshots are reviewed separately before publication; the linked text is a transcription of actual observed fields, not a raw PCAP.
