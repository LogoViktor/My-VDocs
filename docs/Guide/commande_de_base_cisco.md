---
title: "Commandes Switch/Routeur Cisco"
---
# Commandes Cisco : Switch et Routeur

## Commandes de base communes

| Commande | Description |
|----------|-------------|
| `enable` | Passer en mode privilégié (EXEC) |
| `disable` | Quitter le mode privilégié |
| `configure terminal` | Passer en mode configuration globale |
| `exit` | Quitter le mode actuel |
| `end` | Retourner au mode EXEC privilégié |
| `show running-config` | Afficher la configuration en cours |
| `show startup-config` | Afficher la configuration sauvegardée |
| `copy running-config startup-config` | Sauvegarder la configuration en cours |
| `reload` | Redémarrer l'appareil |
| `show version` | Afficher les informations sur le matériel et le logiciel |
| `show interface [interface]` | Afficher les informations sur une interface spécifique |
| `ping [adresse]` | Tester la connectivité réseau |
| `traceroute [adresse]` | Tracer le chemin vers une adresse |

---

## Commandes spécifiques aux Switchs

### Configuration de base
| Commande | Description |
|----------|-------------|
| `vlan [numéro]` | Créer ou accéder à un VLAN spécifique |
| `name [nom]` | Donner un nom à un VLAN |
| `interface vlan [numéro]` | Accéder à l'interface d'un VLAN |
| `switchport mode access` | Configurer un port en mode accès |
| `switchport access vlan [numéro]` | Assigner un VLAN à un port en mode accès |
| `switchport mode trunk` | Configurer un port en mode trunk |
| `switchport trunk allowed vlan [numéros]` | Autoriser des VLANs spécifiques sur un trunk |
| `show vlan brief` | Afficher la liste des VLANs et leurs ports associés |
| `show interface trunk` | Afficher les informations sur les ports trunk |

### Sécurité
| Commande | Description |
|----------|-------------|
| `switchport port-security` | Activer la sécurité sur un port |
| `switchport port-security maximum [nombre]` | Limiter le nombre d'adresses MAC autorisées sur un port |
| `switchport port-security mac-address [adresse]` | Assigner une adresse MAC spécifique à un port |
| `show port-security` | Afficher les informations sur la sécurité des ports |

---

## Commandes spécifiques aux Routeurs

### Configuration de base
| Commande | Description |
|----------|-------------|
| `interface [type] [numéro]` | Accéder à une interface spécifique |
| `ip address [adresse] [masque]` | Configurer une adresse IP sur une interface |
| `no shutdown` | Activer une interface |
| `ip route [réseau] [masque] [prochaine adresse]` | Ajouter une route statique |
| `show ip route` | Afficher la table de routage |
| `show ip interface brief` | Afficher un résumé des interfaces IP |

### Protocoles de routage
| Commande | Description |
|----------|-------------|
| `router rip` | Activer le protocole RIP |
| `router ospf [ID]` | Activer le protocole OSPF |
| `network [adresse] [wildcard]` | Définir un réseau pour le protocole de routage |
| `show ip protocols` | Afficher les informations sur les protocoles de routage actifs |

---

## Commandes avancées

### VLANs avancés
| Commande | Description |
|----------|-------------|
| `vlan database` | Accéder au mode de configuration des VLANs (obsolète sur certains modèles) |
| `vlan [numéro] name [nom]` | Créer un VLAN avec un nom spécifique |
| `vtp mode [client|server|transparent]` | Configurer le mode VTP |

### Routage avancé
| Commande | Description |
|----------|-------------|
| `ipv6 unicast-routing` | Activer le routage IPv6 |
| `ipv6 address [adresse]/[préfixe]` | Configurer une adresse IPv6 sur une interface |
| `ipv6 route [réseau]/[préfixe] [prochaine adresse]` | Ajouter une route statique IPv6 |

---

## Commandes de dépannage

| Commande | Description |
|----------|-------------|
| `show cdp neighbors` | Afficher les voisins CDP |
| `show spanning-tree` | Afficher les informations sur le protocole STP |
| `debug ip rip` | Activer le débogage pour le protocole RIP |
| `debug ip ospf events` | Activer le débogage pour les événements OSPF |
| `show logging` | Afficher les logs système |
| `clear logging` | Effacer les logs système |
| `show mac address-table` | Afficher la table des adresses MAC |
| `show arp` | Afficher la table ARP |
