# Rapport technique — LAB 01 : Analyse des paquets ICMP

**Auteur :** Ismail Amoud  
**Périmètre :** diagnostics réseau réalisés dans un laboratoire personnel  
**Sources d'analyse :** résultats PowerShell et captures Wireshark recueillis dans le laboratoire

## Résumé

Deux VM, Windows 11 et Ubuntu, ont été identifiées sur le même bridge Proxmox et le même sous-réseau IPv4 /24. Une série de quatre pings a produit quatre demandes et quatre réponses observées, sans perte signalée par Windows. Wireshark a permis d'analyser l'encapsulation Ethernet/IPv4/ICMP. Une table ARP et des messages ARP capturés montrent la résolution IPv4–MAC. Enfin, traceroute a atteint directement Ubuntu dès le premier saut affiché.

## Objectifs de l'analyse

1. Comment ICMPv4 est-il encapsulé dans Ethernet ?
2. Quelles différences entre Echo Request et Echo Reply ?
3. Comment Windows découvre-t-il une adresse MAC à partir d'une IPv4 locale ?
4. Quel est le rôle du TTL et du checksum de l'en-tête IPv4 ?
5. Comment interpréter traceroute sur un lien sans routeur intermédiaire ?

## Environnement et commandes

| Élément | Résultat vérifié |
|---|---|
| Hyperviseur | Proxmox VE |
| Source | Windows 11, carte VirtIO |
| Destination | Ubuntu, carte VirtIO |
| Réseau | `vmbr1`, même sous-réseau /24 |
| Analyseur | Wireshark 4.6.9 et Npcap |
| Commandes | `ping -4 -n 4`, `arp -a`, `tracert -4 -d`, vérifications d'interfaces |

## Analyse du paquet

- Trame Echo Request capturée : **74 octets = 14 Ethernet + 20 IPv4 + 8 ICMP Echo + 32 données**.
- `Total Length` IPv4 : **60 octets** ; n'inclut pas Ethernet.
- Champ `Protocol` IPv4 : **1 (ICMP)**.
- Demande : **ICMP Type 8 / Code 0**, TTL **128**.
- Réponse : **ICMP Type 0 / Code 0**, TTL **64**.
- Identifiant et numéro de séquence observés : **1** en ordre réseau.
- Checksum ICMP : **Good**, validé par Wireshark sur les messages examinés.
- Checksum d'en-tête IPv4 : valeur visible mais **validation désactivée / non vérifiée**.

## Résultats de connectivité

Windows a affiché **4 paquets envoyés, 4 reçus, 0 % de perte**, et une **moyenne de 2 ms** pour la première série documentée. Une autre réponse ICMP examinée séparément avait un temps de réponse Wireshark de **17,916 ms** : ce n'est pas le même échange que celui de la première série.

## Analyse ARP

Les messages ARP capturés demandaient l'adresse MAC de la VM Ubuntu ; les réponses indiquaient une correspondance IPv4–MAC. Une entrée dynamique apparaissait également dans `arp -a`. D'autres messages ARP visibles dans la capture n'appartenaient pas forcément au même échange.

## Traceroute

La commande `tracert -4 -d` a affiché Ubuntu dès la **première ligne**, avec **trois temps inférieurs à 1 ms**. Aucun routeur IPv4 intermédiaire n'a été observé. « Maximum 30 hops » désigne une limite de sondage, pas trente routeurs traversés.

## Résultats et interprétation

Les résultats confirment la connectivité entre les deux machines virtuelles et illustrent le fonctionnement d'ICMP sur IPv4. L'analyse dans Wireshark a permis d'identifier les en-têtes Ethernet, IPv4 et ICMP, de comparer les demandes et réponses d'écho, et d'observer la résolution ARP. Le traceroute atteint directement la machine Ubuntu sur le réseau virtuel partagé.

Le checksum ICMP des messages examinés a été validé par Wireshark. Le champ checksum IPv4 a été analysé, mais sa validation n'était pas activée dans la capture.

## Conclusion

Ce laboratoire a permis d'analyser les communications réseau entre deux machines virtuelles Windows 11 et Ubuntu au sein d'un environnement Proxmox VE.

À l'aide de Wireshark, nous avons capturé et analysé des échanges ICMP Echo Request et Echo Reply, étudié l'encapsulation Ethernet/IPv4/ICMP, examiné les champs TTL et checksum, et observé le fonctionnement du protocole ARP.

Les tests de connectivité ont confirmé la réception de quatre réponses ICMP sans perte de paquets. L'utilisation de traceroute a également permis de vérifier la communication directe entre les deux machines sur leur réseau virtuel.

Ce laboratoire démontre des compétences pratiques en analyse de paquets, en compréhension des protocoles réseau et en diagnostic de connectivité avec Wireshark et les outils Windows.
