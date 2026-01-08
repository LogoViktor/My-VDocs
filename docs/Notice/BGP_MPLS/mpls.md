---
title: "MPLS"
---

# **Configuration et Débogage de MPLS : LDP, L3VPN IPv4 et L2VPN**
---

## **1. Introduction**
Ce guide vous explique **pourquoi** et **comment** configurer **MPLS LDP**, **L3VPN IPv4**, et **L2VPN** sur des routeurs Cisco. Chaque section inclut :
- **Le contexte** : Pourquoi cette étape est nécessaire.
- **Les commandes** : Comment la configurer.
- **Les vérifications** : Comment s’assurer que cela fonctionne.
- **Le débogage** : Que faire si cela ne fonctionne pas.

---

## **2. Configuration de MPLS LDP**

### **2.1. Pourquoi configurer LDP ?**
**LDP (Label Distribution Protocol)** est utilisé pour échanger des **labels MPLS** entre les routeurs. Sans LDP, les routeurs ne peuvent pas établir de **LSP (Label Switched Path)**, et le trafic MPLS ne peut pas être acheminé.

---

### **2.2. Configuration de base de LDP**

#### **1. Définir le LSR ID**
**Pourquoi ?**
Le **LSR ID** (Label Switch Router Identifier) identifie de manière unique un routeur dans le domaine MPLS. Il est généralement défini comme l’adresse de la **loopback 0**, car celle-ci est stable et toujours disponible.

**Commande :**
```bash
configure terminal
mpls ldp router-id loopback0 force
end
```

**Vérification :**
```bash
show mpls ldp parameters
```
- **Que vérifier ?** Le champ `LSR ID` doit afficher l’adresse de votre loopback 0.

---

#### **2. Activer MPLS sur les interfaces**
**Pourquoi ?**
MPLS doit être activé sur les interfaces qui transportent le trafic MPLS. Sans cette activation, les paquets IP ne seront pas encapsulés avec des labels MPLS.

**Commande :**
```bash
configure terminal
interface GigabitEthernet0/0
 mpls ip
end
```

**Vérification :**
```bash
show mpls interface
```
- **Que vérifier ?** L’interface doit apparaître dans la liste avec `enabled` à `yes`.

---

#### **3. Vérifier les voisins LDP**
**Pourquoi ?**
Les voisins LDP doivent être établis pour que les routeurs puissent échanger des labels. Sans voisins LDP, aucun LSP ne peut être créé.

**Commande :**
```bash
show mpls ldp neighbor
```

- **Que vérifier ?**
  - L’état (`State`) doit être `Operational`.
  - Le nombre de messages envoyés/reçus (`MsgSent/MsgRcvd`) doit augmenter.

**Débogage :**
Si les voisins ne s’établissent pas :
```bash
debug mpls ldp all
```
- **Que faire ?** Vérifiez la connectivité IP entre les routeurs et assurez-vous que LDP est activé sur les deux côtés.

---

### **2.3. Étude des Loopbacks et des Labels MPLS**

#### **1. Capturer les paquets entre P1 et P2**
**Pourquoi ?**
Cela permet de visualiser comment les labels MPLS sont ajoutés et retirés des paquets lors de leur transit dans le réseau MPLS.

**Commande :**
```bash
monitor capture buffer P1P2 size 1000
monitor capture point ip cef P1P2 GigabitEthernet0/0 both
monitor capture start P1P2
ping <adresse_IP_loopback_PE_voisin>
monitor capture stop P1P2
show monitor capture buffer P1P2 dump
```

---

#### **2. Analyser les paquets ICMP**
**Pourquoi ?**
- **Echo Request** : Contient **2 labels** (un label de transport pour le routage dans le cœur MPLS, et un label de service pour identifier le trafic spécifique au VPN).
- **Echo Reply** : Contient **1 label** (seulement le label de transport, car le trafic de retour est déjà identifié par le routeur de destination).

**Explication :**
Les labels MPLS sont utilisés pour **acheminer le trafic** sans avoir besoin d’analyser l’en-tête IP à chaque saut. Le label de service est retiré par le dernier routeur (PE) avant que le paquet ne quitte le réseau MPLS.

---

#### **3. Afficher la table des labels sur P2**
**Pourquoi ?**
La table des labels montre comment les paquets sont **étiquetés** et **acheminés** à travers le réseau.

**Commande :**
```bash
show mpls forwarding-table
```

- **Que vérifier ?**
  - `Local Label` : Le label que P2 utilise pour recevoir des paquets.
  - `Outgoing Label` : Le label que P2 utilise pour envoyer des paquets.
  - `Next Hop` : L’adresse IP du prochain saut.

---

## **3. Configuration de L3VPN IPv4**

### **3.1. Pourquoi configurer un L3VPN ?**
Un **L3VPN** permet de créer des **réseaux privés virtuels** (VPN) au niveau de la couche 3 (IP). Cela isole le trafic entre différents clients ou services sur une même infrastructure physique.

---

### **3.2. Activation de l’address-family VPNv4**
**Pourquoi ?**
L’**address-family VPNv4** permet aux routeurs PE d’échanger des routes **spécifiques aux VRF** via BGP. Sans cela, les routes des VPN ne peuvent pas être propagées.

**Commande :**
```bash
configure terminal
router bgp <AS>
 neighbor <adresse_IP_PE_voisin> remote-as <AS>
 !
 address-family vpnv4
  neighbor <adresse_IP_PE_voisin> activate
 exit-address-family
end
```

**Vérification :**
```bash
show bgp vpnv4 unicast all summary
```
- **Que vérifier ?** La colonne `State/PfxRcd` doit indiquer un nombre de préfixes reçus.

---

### **3.3. Création des VRF**

#### **1. Définir les VRF sur les PE**
**Pourquoi ?**
Les **VRF (Virtual Routing and Forwarding)** permettent de créer des tables de routage **isolées** pour chaque VPN. Chaque VRF a :
- Un **RD (Route Distinguisher)** : Pour rendre les routes uniques.
- Un **Route-Target** : Pour contrôler l’import/export des routes entre les VRF.

**Commande :**
```bash
configure terminal
ip vrf RED
 rd 10:10
 route-target export 10:10
 route-target import 10:10
exit

ip vrf GREEN
 rd 10:20
 route-target export 10:20
 route-target import 10:20
exit
end
```

**Explication :**
- **RD 10:10** : Rend les routes de la VRF `RED` uniques.
- **Route-Target 10:10** : Permet aux routeurs PE d’échanger les routes de la VRF `RED`.

---

#### **2. Associer les interfaces PE-CE aux VRF**
**Pourquoi ?**
Les interfaces **PE-CE** doivent être associées à une VRF pour que le trafic soit correctement isolé et routé.

**Commande :**
```bash
configure terminal
interface GigabitEthernet0/0
 ip vrf forwarding RED
 ip address <adresse_IP> <masque>
 no shutdown
exit
end
```

**Vérification :**
```bash
show ip vrf interfaces
```
- **Que vérifier ?** L’interface doit apparaître dans la liste avec la VRF correcte.

---

### **3.4. Routage dans les VRF**

#### **1. Propager les routes connectées**
**Pourquoi ?**
Les routes **directement connectées** (comme les réseaux des CE) doivent être propagées dans la VRF pour que les autres PE puissent les apprendre.

**Commande :**
```bash
configure terminal
router bgp <AS>
 !
 address-family ipv4 vrf RED
  redistribute connected
 exit-address-family
end
```

**Vérification :**
```bash
show ip route vrf RED
```
- **Que vérifier ?** Les routes connectées doivent apparaître avec le code `C`.

---

### **3.5. Routage PE-CE**

#### **1. VRF Rouge (Routes statiques)**
**Pourquoi ?**
Les **routes statiques** sont utilisées pour connecter les CE aux PE dans la VRF `RED`. Cela permet aux CE de communiquer avec le reste du VPN.

**Commande :**
- **Sur CE1 et CE4** :
  ```bash
  configure terminal
  ip route 0.0.0.0 0.0.0.0 <adresse_IP_PE>
  end
  ```

- **Sur PE1 et PE3** :
  ```bash
  configure terminal
  ip route vrf RED <réseau_CE> <masque> <next-hop>
  !
  router bgp <AS>
   address-family ipv4 vrf RED
    redistribute static
   exit-address-family
  end
  ```

**Vérification :**
```bash
ping vrf RED <adresse_IP_PC4>
```
- **Que faire si le ping échoue ?**
  - Vérifiez les routes statiques avec `show ip route vrf RED`.
  - Assurez-vous que les interfaces sont dans la bonne VRF.

---

#### **2. VRF Verte (eBGP)**
**Pourquoi ?**
**eBGP** est utilisé pour échanger des routes entre les PE et les CE dans la VRF `GREEN`. Cela permet une **scalabilité** et une **flexibilité** accrues.

**Commande :**
- **Sur PE1, PE2 et PE3** :
  ```bash
  configure terminal
  router bgp <AS>
   !
   address-family ipv4 vrf GREEN
    neighbor <adresse_IP_CE> remote-as <AS_CE>
    neighbor <adresse_IP_CE> activate
   exit-address-family
  end
  ```

- **Sur CE2, CE3 et CE5** :
  ```bash
  configure terminal
  router bgp <AS_CE>
   neighbor <adresse_IP_PE> remote-as <AS>
   !
   address-family ipv4
    neighbor <adresse_IP_PE> activate
    redistribute connected
   exit-address-family
  end
  ```

**Vérification :**
```bash
show bgp vpnv4 unicast vrf GREEN summary
```
- **Que vérifier ?** La session BGP doit être `Established`.

---

## **4. Service Internet pour le VPN MPLS « Rouge »**

### **4.1. Pourquoi un multi-service ?**
Le **multi-service** permet de fournir **plusieurs services** (VPN MPLS, Internet) sur un même lien physique en utilisant des **VLAN**. Cela optimise l’utilisation des ressources.

---

### **4.2. Configuration du multi-service**

#### **1. Créer des sous-interfaces VLAN**
**Pourquoi ?**
Les **sous-interfaces VLAN** permettent de séparer logiquement le trafic VPN et Internet sur un même lien physique.

**Commande :**
- **Sur PE1** :
  ```bash
  configure terminal
  interface GigabitEthernet0/1.100
   encapsulation dot1Q 100
   ip vrf forwarding RED
   ip address <adresse_IP> <masque>
   no shutdown
  exit
  !
  interface GigabitEthernet0/1.200
   encapsulation dot1Q 200
   ip address 198.51.100.32 255.255.255.254
   no shutdown
  exit
  end
  ```

- **Sur CE1** :
  ```bash
  configure terminal
  interface GigabitEthernet0/1.100
   encapsulation dot1Q 100
   ip address <adresse_IP> <masque>
   no shutdown
  exit
  !
  interface GigabitEthernet0/1.200
   encapsulation dot1Q 200
   ip address 198.51.100.33 255.255.255.254
   no shutdown
  exit
  end
  ```

**Vérification :**
```bash
show ip interface brief
```
- **Que vérifier ?** Les sous-interfaces doivent être `up/up`.

---

### **4.3. Reconfiguration du routage**
**Pourquoi ?**
Les routes doivent être mises à jour pour utiliser les nouvelles sous-interfaces.

**Commande :**
- **Sur CE1** :
  ```bash
  configure terminal
  ip route 0.0.0.0 0.0.0.0 198.51.100.32
  ip route <réseau_VPN> <masque> GigabitEthernet0/1.100
  end
  ```

- **Sur PE1** :
  ```bash
  configure terminal
  ip route vrf RED 0.0.0.0 0.0.0.0 <adresse_IP_CE1_VLAN100>
  !
  router bgp <AS>
   address-family ipv4 vrf RED
    default-information originate
   exit-address-family
  end
  ```

**Explication :**
- **`default-information originate`** : Propage une route par défaut dans la VRF `RED` pour permettre l’accès à Internet.

---

### **4.4. Activation du NAT sur CE1**
**Pourquoi ?**
Le **NAT** permet aux hôtes du VPN d’accéder à Internet en traduisant leurs adresses privées en une adresse publique.

**Commande :**
```bash
configure terminal
access-list 100 permit ip <réseau_LAN_PC1> any
access-list 100 permit ip <réseau_VPN> any
!
ip nat inside source list 100 interface GigabitEthernet0/1.200 overload
!
interface GigabitEthernet0/0
 ip nat inside
exit
!
interface GigabitEthernet0/1.100
 ip nat inside
exit
!
interface GigabitEthernet0/1.200
 ip nat outside
exit
end
```

**Test :**
```bash
ping 8.8.8.8
```
- **Que faire si le ping échoue ?**
  - Vérifiez les règles NAT avec `show ip nat translations`.
  - Assurez-vous que la route par défaut est correcte.

---

## **5. Service L2VPN Point à Point**

### **5.1. Pourquoi un L2VPN ?**
Un **L2VPN** permet d’étendre un réseau **Ethernet** (couche 2) à travers un réseau MPLS. Cela permet de connecter des sites distants comme s’ils étaient sur le même réseau local.

---

### **5.2. Configuration du L2VPN**

#### **1. Activer les ports eth0/3**
**Pourquoi ?**
Les ports doivent être activés pour établir la connexion L2VPN.

**Commande :**
```bash
configure terminal
interface GigabitEthernet0/3
 no shutdown
exit
end
```

---

#### **2. Configurer le service L2VPN**
**Pourquoi ?**
La commande `xconnect` établit un **circuit virtuel** (VC) entre deux PE pour transporter le trafic de couche 2.

**Commande :**
```bash
configure terminal
interface GigabitEthernet0/3
 xconnect <adresse_IP_loopback_PE_voisin> 1 encapsulation mpls
exit
end
```

**Vérification :**
```bash
show mpls l2transport vc
```
- **Que vérifier ?** Le VC doit être `up`.

**Test :**
```bash
ping <adresse_IP_SRV2>
```

---

## **6. Commandes de Débogage Utiles**

### **6.1. Débogage LDP et MPLS**
```bash
debug mpls ldp all
debug mpls packet
show mpls ldp bindings
show mpls forwarding-table detail
```

### **6.2. Débogage BGP et VRF**
```bash
debug ip bgp
show ip bgp neighbors
show ip route vrf RED
show bgp vpnv4 unicast vrf GREEN
```