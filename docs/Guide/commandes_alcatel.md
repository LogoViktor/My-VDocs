---
title: "Commandes Switch Alcatel"
---
# Commandes Alcatel : Switchs (AOS6 et AOS8)

## Gestion de la configuration

| Commande | Description |
|----------|-------------|
| `show running-config` | Afficher la configuration en cours |
| `show configuration snapshot all` | Afficher la configuration complète |
| `write memory` | Sauvegarder la configuration |
| `copy working certified` | Synchroniser la configuration "working" avec la "certified" |
| `reload working no rollback-timeout` | Redémarrer le switch en mode "working" |
| `reload from working no rollback-timeout` | Redémarrer le switch (AOS8) |

---

## Gestion des VLANs

| Commande | Description |
|----------|-------------|
| `vlan <ID_VLAN> name <NOM>` | Créer un VLAN (AOS6) |
| `vlan <ID_VLAN> admin-state enable name <NOM>` | Créer un VLAN (AOS8) |
| `no vlan <ID_VLAN>` | Supprimer un VLAN |
| `vlan <ID_VLAN> 802.1q <chassis/port>` | Tagger un VLAN sur un port (AOS6) |
| `vlan <ID_VLAN> members port <chassis/slot/port> tagged` | Tagger un VLAN sur un port (AOS8) |
| `show vlan` | Afficher la liste des VLANs |

---

## Gestion des ports

| Commande | Description |
|----------|-------------|
| `interface <chassis/port> alias <NOM>` | Renommer un port (AOS6) |
| `interface <chassis/slot/port> alias <NOM>` | Renommer un port (AOS8) |
| `show interfaces <chassis/port>` | Afficher les informations d'un port |
| `show lldp remote-system` | Afficher les périphériques connectés via LLDP |

---

## Gestion des agrégations de liens (Linkagg)

| Commande | Description |
|----------|-------------|
| `staticlinkagg <ID> size 2 name <NOM> admin state enable` | Créer une agrégation statique (AOS6) |
| `linkagg staticagg <ID> size 2 name <NOM> admin-state enable` | Créer une agrégation statique (AOS8) |
| `show linkagg` | Afficher les agrégations de liens |

---

## Gestion IP et routage

| Commande | Description |
|----------|-------------|
| `ip interface <NOM> address <IP> mask <MASQUE> vlan <ID_VLAN>` | Configurer une IP statique |
| `ip interface dhcp-client vlan <ID_VLAN>` | Configurer une IP via DHCP |
| `ip static-route <IP> mask <MASQUE> gateway <PASSERELLE>` | Ajouter une route statique |
| `show ip interface` | Afficher les interfaces IP |

---

## Gestion du Virtual Chassis (AOS8)

| Commande | Description |
|----------|-------------|
| `virtual-chassis chassis-id <ID> configured-chassis-id <ID>` | Configurer un Virtual Chassis |
| `virtual-chassis vf-link-mode auto` | Activer le mode automatique pour les liens virtuels |
| `show virtual-chassis topology` | Afficher la topologie du Virtual Chassis |

---

## Gestion du PoE

| Commande | Description |
|----------|-------------|
| `lanpower chassis <chassis> service start` | Activer le PoE sur un chassis (AOS8) |
| `show lanpower slot <chassis/slot>` | Afficher la consommation PoE |

---

## Gestion du Spanning Tree

| Commande | Description |
|----------|-------------|
| `spantree mode flat` | Rendre le Spanning Tree compatible avec d'autres marques |
| `show spantree ports` | Afficher les ports bloqués par le Spanning Tree |

---

## Gestion du SNMP

| Commande | Description |
|----------|-------------|
| `snmp security no-security` | Désactiver la sécurité SNMP (AOS8) |
| `snmp community-map <COMMUNAUTE> user <UTILISATEUR> enable` | Configurer une communauté SNMP |

---

## Commandes de dépannage

| Commande | Description |
|----------|-------------|
| `show log swlog` | Afficher les logs système |
| `show mac-address-table` | Afficher la table des adresses MAC |
| `show arp` | Afficher la table ARP |
| `show system` | Afficher les informations système |
| `show temperature` | Afficher la température du switch |

