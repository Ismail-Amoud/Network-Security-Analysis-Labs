# Rapport technique — LAB 01 : Analyse des paquets ICMP

**Auteur :** Ismail Amoud  
**Statut :** Terminé  
**Plateforme :** Proxmox VE · Windows 11 · Ubuntu · Wireshark

## Résumé

Une communication ICMPv4 entre deux machines virtuelles Windows 11 et Ubuntu a été capturée et analysée avec Wireshark. Windows a envoyé quatre demandes Echo Request et reçu quatre réponses, soit **0 % de perte**. L'analyse a porté sur l'encapsulation Ethernet/IPv4/ICMP, le TTL, les sommes de contrôle, la résolution IPv4–MAC avec ARP et un traceroute atteignant directement la destination.

## Objectifs

- Capturer et interpréter les messages ICMP Echo Request et Echo Reply.
- Identifier les champs Ethernet II, IPv4 et ICMP et expliquer l'encapsulation.
- Observer la résolution ARP et interpréter la table ARP dynamique.
- Comprendre les champs TTL et les sommes de contrôle IPv4/ICMP.
- Examiner avec traceroute le chemin vers un hôte accessible directement.

## Environnement et méthode

Les tests ont utilisé une VM Windows 11 et une VM Ubuntu reliées au même bridge virtuel Proxmox, sur un sous-réseau IPv4 privé /24. Les interfaces réseau virtuelles utilisaient VirtIO. Wireshark 4.6.9 et Npcap ont permis la capture sur Windows.

Les commandes Windows comprenaient `Get-NetAdapter`, `Get-NetIPAddress`, `ping -4 -n 4`, `arp -a` et `tracert -4 -d`. Ubuntu a été examiné avec `ip -br addr` et `ip route`. Les filtres Wireshark `icmp` et `arp` ont servi à sélectionner les échanges.

## Résultats

### 1. Connectivité ICMP

Windows a affiché **4 paquets envoyés, 4 reçus et 0 perdu**. Pour cette série, le temps aller-retour moyen indiqué était de **2 ms**, le minimum de **0 ms** (affichage arrondi) et le maximum de **8 ms**. Wireshark montrait quatre demandes et quatre réponses.

### 2. Encapsulation Ethernet/IPv4/ICMP

Une demande Echo Request inspectée mesurait **74 octets** dans la capture :

| Élément | Taille |
|---|---:|
| En-tête Ethernet II | 14 octets |
| En-tête IPv4 (sans options) | 20 octets |
| En-tête ICMP Echo | 8 octets |
| Données ICMP | 32 octets |
| **Total capturé** | **74 octets** |

Le champ IPv4 `Total Length` indiquait **60 octets**, sans l'en-tête Ethernet. Le préambule Ethernet et un éventuel FCS non capturé ne figurent pas dans cette décomposition.

### 3. Champs IPv4 et ICMP

La demande examinée avait **ICMP Type 8 / Code 0** et un TTL de **128**. Une réponse examinée dans une autre capture présentait **ICMP Type 0 / Code 0** et un TTL de **64**. Les paquets inspectés avaient un identifiant et un numéro de séquence de **1** en ordre réseau ; l'identité de ces valeurs entre deux captures ne prouve **pas** qu'il s'agit d'un couple demande/réponse.

Les TTL observés sont cohérents avec les valeurs initiales courantes de Windows et d'Ubuntu. Ils ne représentent pas une diminution de 128 à 64 due au passage par 64 routeurs.

Wireshark a indiqué **Good** pour le checksum ICMP examiné. Le champ checksum de l'en-tête IPv4 était visible, mais son statut restait **Unverified**, car la validation était désactivée. Sur la réponse examinée séparément, la valeur du checksum IPv4 affichée était `0x3a64` ; sa validité n'a **pas** été vérifiée.

### 4. Résolution ARP

Les requêtes ARP recherchaient l'interface possédant l'adresse IPv4 privée d'Ubuntu. Les réponses fournissaient la correspondance avec l'adresse MAC de cette interface. La commande Windows `arp -a` montrait également une entrée dynamique pour cette destination.

Ces observations démontrent la résolution IPv4–MAC sur le segment Ethernet local. Contrairement au message ICMP, ARP est transporté directement dans une trame Ethernet et non dans un paquet IPv4.

### 5. Traceroute

La commande Windows `tracert -4 -d` affichait la destination au **premier saut présenté**, avec trois temps aller-retour **inférieurs à 1 ms**. Aucun routeur IP intermédiaire n'a été observé, ce qui est cohérent avec la présence des deux VM sur le même réseau local. La limite de **30 hops** ne correspond pas à trente routeurs traversés.

## Interprétation

Le laboratoire confirme la connectivité observée et illustre la livraison locale sur Ethernet, l'encapsulation IPv4/ICMP, la résolution ARP et l'analyse de traceroute. La diminution du TTL par des routeurs a été étudiée théoriquement, sans capture de trajet à plusieurs sauts. L'isolation complète n'a pas été établie, puisque Windows pouvait également accéder à Internet.

Les exemples détaillés de demandes et de réponses provenaient de captures distinctes. Ils ne sont pas présentés comme une paire unique ; notamment, le temps de réponse de **17,916 ms** observé sur une réponse séparée ne doit pas être comparé à la moyenne de **2 ms** de la première série.

## Conclusion

Les manipulations ICMP, ARP et traceroute ont été réalisées et documentées avec succès. Ce laboratoire terminé démontre des compétences pratiques en analyse de paquets, en compréhension des protocoles réseau et en diagnostic de connectivité à l'aide de Wireshark et des outils Windows.

**Relevés des champs :** [Français](evidence/observations-fr.md) · [English](evidence/observations-en.md).
