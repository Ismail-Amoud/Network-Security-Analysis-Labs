# LAB 01 — Analyse ICMP et encapsulation réseau

**Statut : Terminé** · **Environnement :** Proxmox VE, Windows 11, Ubuntu, Wireshark 4.6.9 et Npcap.

## Présentation

Ce laboratoire pratique étudie les échanges ICMP Echo entre deux machines virtuelles Windows et Ubuntu connectées au même réseau Ethernet virtuel. Il met en évidence la capture de paquets, l'encapsulation Ethernet/IPv4/ICMP, la résolution ARP et le diagnostic du chemin réseau.

## Réalisations

| Domaine | Résultat observé |
|---|---|
| Connectivité | 4 demandes Echo Request, 4 réponses Echo Reply et **0 % de perte** |
| Wireshark | Trame Echo Request de 74 octets : Ethernet II → IPv4 → ICMP → données |
| IPv4 | En-tête de 20 octets, longueur IPv4 totale de 60 octets, protocole **1 (ICMP)** |
| ICMP | Demande **Type 8 / Code 0** ; réponse **Type 0 / Code 0** |
| TTL | **128** sur une demande Windows et **64** sur une réponse Ubuntu examinées |
| Checksum | Checksum ICMP indiqué **Good** ; validation de l'en-tête IPv4 **non activée** |
| ARP | Requêtes et réponses ARP, avec entrée dynamique correspondante dans Windows |
| Traceroute | Destination au premier saut affiché, avec trois mesures inférieures à 1 ms |

## Environnement réseau

- VM Windows 11 et Ubuntu, interfaces VirtIO et bridge Linux commun sur Proxmox.
- Machines configurées sur le même sous-réseau privé IPv4 /24.
- Communication directe observée. Le réseau n'est pas présenté comme entièrement isolé, puisque Windows disposait aussi d'un accès Internet.
- Aucun routeur intermédiaire observé lors du test local.

## Commandes et analyse

Commandes Windows PowerShell exécutées :

```powershell
Get-NetAdapter | Select-Object Name, Status, LinkSpeed
Get-NetIPAddress -AddressFamily IPv4 | Select-Object InterfaceAlias, IPAddress, PrefixLength
ping -4 -n 4 <IP_UBUNTU>
arp -a
tracert -4 -d <IP_UBUNTU>
```

Commandes exécutées sur Ubuntu :

```bash
ip -br addr
ip route
```

Filtres d'affichage Wireshark : `icmp` et `arp`.

**Précision :** `<IP_UBUNTU>` remplace l'adresse réelle dans cette documentation ; ce n'est pas la chaîne saisie telle quelle dans PowerShell.

## Documentation

- [Rapport technique détaillé — Français](rapport-fr.md)
- [Detailed technical report — English](report-en.md)
- [Champs des paquets observés — Français](evidence/observations-fr.md)
- [Observed packet fields — English](evidence/observations-en.md)

## Conclusion

Ce laboratoire a permis de démontrer la connectivité ICMP, l'encapsulation IPv4, la résolution des adresses Ethernet et l'analyse de traceroute sur un réseau virtuel. Les résultats reposent sur les sorties PowerShell et les observations Wireshark recueillies pendant les manipulations.

**Périmètre technique :** la validité du checksum IPv4 n'a pas été vérifiée et aucune décrémentation du TTL par un routeur n'a été testée. Les exemples détaillés provenant de captures différentes sont identifiés comme tels.
