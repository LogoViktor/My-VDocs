---
title: "VLAN et Trunking 802.1Q"
---
# 🔀 Guide Complet : VLAN et Trunking 802.1Q

## 📖 1. Concepts de base

- **VLAN (Virtual LAN)** : segment logique qui isole un groupe de machines au niveau 2, indépendamment de leur emplacement physique sur le réseau.
- **Port access** : port relié à un seul VLAN, sans tag — utilisé pour connecter un poste utilisateur ou un serveur.
- **Port trunk** : port qui transporte plusieurs VLANs simultanément, en ajoutant un tag 802.1Q (4 octets) à chaque trame pour identifier son VLAN d'origine.
- **VLAN natif** : seul VLAN d'un trunk dont les trames circulent **sans tag** — il doit être identique des deux côtés du lien, sinon risque de fuite entre VLANs (VLAN hopping).

---

## ⚙️ 2. Configuration sur switch Cisco (IOS)

### a. Création des VLANs

```
Switch> enable
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name SERVEURS
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# name UTILISATEURS
Switch(config-vlan)# exit
```

### b. Port en mode access

```
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit
```

### c. Port en mode trunk (802.1Q)

```
Switch(config)# interface FastEthernet0/24
Switch(config-if)# switchport trunk encapsulation dot1q
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan 10,20
Switch(config-if)# switchport trunk native vlan 99
Switch(config-if)# exit
```

> **Exemple :** liaison entre deux switches transportant les VLANs 10 et 20, avec un VLAN natif dédié (99) pour éviter tout trafic non tagué indésirable.

### d. Vérification

```
Switch# show vlan brief
Switch# show interfaces trunk
Switch# show interfaces FastEthernet0/24 switchport
```

---

## ⚙️ 3. Équivalent sur switch Alcatel (AOS)

```
-> vlan 10 name SERVEURS
-> vlan 20 name UTILISATEURS
-> vlan 10 port default 1/1
-> vlan 10 802.1q 1/24
-> vlan 20 802.1q 1/24
```

Vérification :

```
-> show vlan
-> show vlan port
```

!!! tip "Astuce"
    Sur AOS, `vlan X port default` place un port en access ; `vlan X 802.1q` ajoute un VLAN taggé sur un port. Il n'y a pas de commande unique « mode trunk » comme chez Cisco : le mode se déduit du nombre de VLANs associés au port.

---

## 🐧 4. Tagging VLAN côté serveur Debian

Pour qu'un serveur Debian dialogue directement sur plusieurs VLANs via un lien trunk :

```
sudo apt update
sudo apt install vlan
sudo modprobe 8021q
echo "8021q" | sudo tee -a /etc/modules
```

Dans `/etc/network/interfaces` :

```
auto eth0.10
iface eth0.10 inet static
    address 192.168.10.1
    netmask 255.255.255.0
    vlan-raw-device eth0

auto eth0.20
iface eth0.20 inet static
    address 192.168.20.1
    netmask 255.255.255.0
    vlan-raw-device eth0
```

Appliquez et vérifiez :

```
sudo systemctl restart networking
ip -d link show eth0.10
```

---

## 🔍 5. Dépannage courant

- **Le trafic d'un VLAN ne passe pas le trunk** → vérifiez `switchport trunk allowed vlan` des deux côtés du lien.
- **VLAN natif différent des deux côtés** → Cisco remonte un warning « native VLAN mismatch » ; alignez avec `switchport trunk native vlan`.
- **Deux hôtes du même VLAN ne se joignent pas** → vérifiez que le port est bien en access sur le bon VLAN (`show interfaces ... switchport`) et pas resté sur le VLAN 1 par défaut.
- **Un serveur taggé ne reçoit rien** → confirmez que le module `8021q` est chargé (`lsmod | grep 8021q`) et que l'interface `ethX.Y` est bien up (`ip a`).

---

## 🧰 6. Récapitulatif des commandes

| Action | Cisco IOS | Alcatel AOS |
|---|---|---|
| Créer un VLAN | `vlan 10` puis `name X` | `vlan 10 name X` |
| Port access | `switchport mode access` / `switchport access vlan X` | `vlan X port default slot/port` |
| Port trunk | `switchport mode trunk` | `vlan X 802.1q slot/port` |
| Vérifier les VLANs | `show vlan brief` | `show vlan` |
| Vérifier un trunk | `show interfaces trunk` | `show vlan port` |
