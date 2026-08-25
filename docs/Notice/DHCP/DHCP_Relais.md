---
title: "Relais DHCP"
---
# Guide : Relais DHCP (IP Helper)

Par défaut, les requêtes DHCP émises par les clients sont des messages de diffusion (Broadcasts). Puisqu'un routeur ou un switch de niveau 3 bloque les diffusions pour isoler les domaines de broadcast, une machine située dans le VLAN 10 ne pourra jamais atteindre un serveur DHCP situé dans le VLAN 99.

Le **Relais DHCP** résout ce problème : le switch L3 écoute les requêtes DHCP sur ses interfaces virtuelles (SVI), les intercepte, et les renvoie sous forme de message direct (Unicast) au serveur DHCP distant.

---

### 🔵 Configuration sur Switch Cisco (IOS)

Chez Cisco, la configuration s'applique directement sur l'interface virtuelle (SVI) du VLAN qui a besoin du service, c'est-à-dire du côté des clients.

1. **Ajouter l'adresse IP Helper:** Sur l'interface de routage.
Entrez dans la configuration de l'interface VLAN où se trouvent vos postes clients (ex: VLAN 10), puis indiquez l'adresse IP de votre serveur DHCP central.

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# interface vlan 10
Switch(config-if)# ip helper-address 192.168.99.10
Switch(config-if)# exit

```


2. **Appliquer aux autres VLANs:** Répétition.
Si d'autres VLANs ont besoin d'adresses IP, répétez l'opération sur leurs SVI respectives.

```cisco
Switch(config)# interface vlan 20
Switch(config-if)# ip helper-address 192.168.99.10
Switch(config-if)# end

```


3. **Vérification:**
Vous pouvez vérifier que le relais est bien appliqué sur l'interface avec la commande suivante :

```cisco
Switch# show ip interface vlan 10

```

*(Cherchez la ligne "Helper address is...")*


---

### 🟣 Configuration sur Switch Alcatel (AOS)

Sur Alcatel, la philosophie est plus globale. On définit l'adresse du serveur DHCP, on l'associe au VLAN source (celui des clients), puis on active le service relais.

1. **Définir le serveur et le VLAN source:** Ciblage.
Indiquez l'adresse IP du serveur DHCP et liez-la au VLAN où les clients feront leurs requêtes broadcast.

```alcatel
-> ip helper address 192.168.99.10 vlan 10
-> ip helper address 192.168.99.10 vlan 20

```


2. **Activer le service IP Helper:** Indispensable.
Le service de relais doit être explicitement démarré sur l'OmniSwitch (la commande peut varier très légèrement selon la version AOS, ici compatible AOS 7/8).

```alcatel
-> ip helper admin-state enable

```


3. **Optimisation (Optionnel):**
Par défaut, certains switchs Alcatel appliquent un délai avant de transférer la requête. Vous pouvez le réduire à zéro pour une attribution instantanée.

```alcatel
-> ip helper forward delay 0

```


4. **Vérification:**
Affichez la table de configuration du relais DHCP pour vous assurer que les requêtes seront bien acheminées :

```alcatel
-> show ip helper
-> show ip helper statistics

```