---
title: "Routage Inter-VLAN"
---
# Guide : Routage Inter-VLAN (Switch Niveau 3)

Le routage inter-VLAN permet à des postes de travail situés dans des VLANs différents (et donc des réseaux IP différents) de communiquer ensemble. En utilisant un Switch de Niveau 3 (L3), le routage se fait en interne sans avoir besoin d'un routeur externe.

L'équipement va agir comme passerelle par défaut pour chaque VLAN grâce à des interfaces virtuelles.

---

### 🔵 Configuration sur Switch Cisco (IOS)

Chez Cisco, les interfaces de routage liées aux VLANs sont appelées **SVI (Switched Virtual Interfaces)**.

1. **Activer le routage global:** Prérequis absolu.
Par défaut, un switch Cisco, même de niveau 3, se comporte uniquement en niveau 2. Il faut lui dire d'activer sa table de routage.

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# ip routing

```


2. **Déclarer les VLANs:**
Création des identifiants et des noms dans la base de données du switch.

```cisco
Switch(config)# vlan 10
Switch(config-vlan)# name SERVEURS
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name UTILISATEURS
Switch(config-vlan)# exit

```


3. **Créer les interfaces de routage (SVI):**
Ces interfaces virtuelles portent les adresses IP qui seront les **passerelles par défaut** des PC.

```cisco
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.254 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit

Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.254 255.255.255.0
Switch(config-if)# no shutdown
Switch(config-if)# exit

```


4. **Placer les ports physiques dans les VLANs:**
Affectation des ports (mode access) pour y brancher les clients.

```cisco
Switch(config)# interface FastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# interface FastEthernet 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# end

```


5. **Vérifications:**
Commandes utiles pour valider le routage :

* `show ip interface brief` (Vérifier que les int vlan sont *up/up*)
* `show ip route` (La table de routage doit contenir les réseaux 192.168.10.0 et 192.168.20.0 marqués comme *Connected* "C")


---

### 🟣 Configuration sur Switch Alcatel (AOS)

Chez Alcatel, les interfaces de routage sont appelées simplement **IP Interfaces**. Le routage global s'active automatiquement dès que plusieurs interfaces IP sont créées et actives.

1. **Déclarer et activer les VLANs:** Création.
*Note : Sur les versions AOS récentes (AOS 8), il faut spécifier "admin-state enable". Sur AOS 6, "enable" n'est pas nécessaire.*

```alcatel
-> vlan 10 admin-state enable name "SERVEURS"
-> vlan 20 admin-state enable name "UTILISATEURS"

```


2. **Créer les Interfaces IP:**
On définit la passerelle par défaut. Contrairement à Cisco, on donne un nom explicite à l'interface puis on la lie au VLAN.

```alcatel
-> ip interface "GW_SERVEURS" address 192.168.10.254 mask 255.255.255.0 vlan 10
-> ip interface "GW_UTILISATEURS" address 192.168.20.254 mask 255.255.255.0 vlan 20

```


3. **Placer les ports physiques dans les VLANs:**
Pour affecter un port (sans tag/untagged), on le définit comme port par défaut du VLAN.

```alcatel
-> vlan 10 port default 1/1
-> vlan 20 port default 1/2

```


4. **Vérifications:**
Commandes utiles pour valider le routage :

* `show ip interface` (Vérifier que le statut est *UP*)
* `show ip route` (Vérifier la présence des routes directes)