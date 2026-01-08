---
title: "BGP"
---

# **Guide Complet : Configuration et Débogage BGP (incluant les routes agrégées)**

---

## 📋 **Prérequis**
- Routeurs Cisco configurés pour IPv4/IPv6.
- Topologie réseau entre AS123 et AS4652.
- Accès CLI aux routeurs (R2, R7, etc.).
- Connaissances de base en BGP et routage statique.

---

## 🌐 **1. Configuration de Base des Sessions BGP**

### **1.1. Activer le processus BGP**
```bash
Router> enable
Router# configure terminal
Router(config)# router bgp <AS_NUMBER>
```
- Remplacez `<AS_NUMBER>` par le numéro d'AS local (ex: `123` pour AS123, `4652` pour AS4652).

---

### **1.2. Désactiver l'annonce IPv4 par défaut**
```bash
Router(config-router)# no bgp default ipv4-unicast
```
- **Pourquoi ?** Pour éviter l'annonce automatique des routes IPv4 non désirées.

---

### **1.3. Configurer le Router-ID**
```bash
Router(config-router)# bgp router-id <IP_LOOPBACK>
```
- Utilisez l'IP de la Loopback0 (ex: `192.168.1.1`).

---

## 🔄 **2. Configuration des Sessions iBGP (Full-Mesh)**
### **2.1. Déclarer les voisins iBGP**
```bash
Router(config-router)# neighbor <IP_LOOPBACK_VOISIN> remote-as <AS_NUMBER>
Router(config-router)# neighbor <IP_LOOPBACK_VOISIN> update-source Loopback0
```
- **Exemple pour R2 (AS123)** :
  ```bash
  neighbor 192.168.2.2 remote-as 123
  neighbor 192.168.2.2 update-source Loopback0
  ```

### **2.2. Activer l'address-family IPv4/IPv6**
```bash
Router(config-router)# address-family ipv4
Router(config-router-af)# neighbor <IP_LOOPBACK_VOISIN> activate
Router(config-router-af)# exit-address-family
```
- Répétez pour IPv6 si nécessaire.

---

## 🌍 **3. Configuration des Sessions eBGP**
### **3.1. Déclarer les voisins eBGP**
```bash
Router(config-router)# neighbor <IP_INTERCONNEXION> remote-as <AS_VOISIN>
```
- **Exemple pour R2 (AS123) vers R7 (AS4652)** :
  ```bash
  neighbor 10.0.0.7 remote-as 4652
  ```

### **3.2. Activer l'address-family IPv4/IPv6**
```bash
Router(config-router)# address-family ipv4
Router(config-router-af)# neighbor <IP_INTERCONNEXION> activate
Router(config-router-af)# exit-address-family
```
- Répétez pour IPv6.

---

## 📦 **3.2. Annonces de Routes BGP (Routes Agrégées)**

### **Objectif**
Les AS **123** et **4652** annoncent uniquement des **routes agrégées** (pas de détail du réseau interne).

---

### **3.2.1. Sur R2 (AS123)**
#### **Créer des routes statiques vers Null0**
```bash
Router(config)# ip route 212.51.160.0 255.255.224.0 Null0
Router(config)# ipv6 route 2001:db8::/36 Null0
```

#### **Importer les routes dans BGP**
```bash
Router(config-router)# address-family ipv4
Router(config-router-af)# network 212.51.160.0 mask 255.255.224.0
Router(config-router-af)# exit-address-family
Router(config-router)# address-family ipv6
Router(config-router-af)# network 2001:db8::/36
Router(config-router-af)# exit-address-family
```

---

### **3.2.2. Sur R7 (AS4652)**
#### **Créer des routes statiques vers Null0**
```bash
Router(config)# ip route 198.51.100.0 255.255.255.0 Null0
Router(config)# ipv6 route 2001:db8:2::/48 Null0
```

#### **Importer les routes dans BGP**
```bash
Router(config-router)# address-family ipv4
Router(config-router-af)# network 198.51.100.0 mask 255.255.255.0
Router(config-router-af)# exit-address-family
Router(config-router)# address-family ipv6
Router(config-router-af)# network 2001:db8:2::/48
Router(config-router-af)# exit-address-family
```

---

## 🔍 **4. Vérification et Débogage**

### **4.1. Vérifier l'état des sessions BGP**
```bash
Router# show bgp summary
```
- **À vérifier** :
  - Les sessions doivent être **établies** (colonne "State" = nombre, ex: `0`).
  - Si une session est en `Idle` ou `Active`, vérifiez la connectivité réseau.

---

### **4.2. Afficher les routes BGP**
```bash
Router# show bgp ipv4 unicast
Router# show bgp ipv6 unicast
```
- **À vérifier** :
  - Les routes agrégées (`212.51.160.0/19`, `198.51.100.0/24`, `2001:db8::/36`, `2001:db8:2::/48`) sont-elles présentes ?

---

### **4.3. Vérifier la table de routage**
```bash
Router# show ip route bgp
Router# show ipv6 route bgp
```
- **À vérifier** :
  - Les routes BGP sont-elles installées dans la table de routage ?

---

### **4.4. Tester la connectivité entre PC1 et PC2**
```bash
PC1> ping 198.51.100.1
PC1> ping 2001:db8:2::1
```
- **Si la connectivité échoue** :
  - Vérifiez les routes avec `show ip route` et `show ipv6 route`.
  - Utilisez `traceroute` pour identifier où le trafic est bloqué.
  - Activez le débogage BGP :
    ```bash
    Router# debug ip bgp updates
    Router# debug ipv6 bgp updates
    ```

---

## 🐞 **5. Commandes de Débogage Avancé**

| **Problème**                     | **Commande de Débogage**                     | **Solution Possible**                          |
|-----------------------------------|---------------------------------------------|-----------------------------------------------|
| Session BGP en `Idle`             | `debug ip bgp`                              | Vérifiez la connectivité (`ping`, `traceroute`). |
| Pas de routes reçues              | `show ip bgp neighbors`                     | Vérifiez les filtres (`prefix-list`, `route-map`). |
| Routes non installées             | `show ip route bgp`                         | Vérifiez la politique de routage (`show running-config`). |
| Erreur `Hold Time expired`        | `show bgp neighbors`                        | Ajustez les timers : `neighbor <IP> timers 30 90`. |

---

## 📌 **6. Bonnes Pratiques**
- **Utilisez des Loopbacks** pour les sessions iBGP (stabilité).
- **Filtrez les routes** avec des `prefix-lists` pour éviter les fuites.
- **Documentation** : Notez les configurations et les changements.
- **Testez la connectivité** après chaque modification.

---

## 📄 **Exemple de Configuration Complète (R2 - AS123)**
```bash
router bgp 123
 no bgp default ipv4-unicast
 bgp router-id 192.168.1.2
 neighbor 192.168.1.1 remote-as 123
 neighbor 192.168.1.1 update-source Loopback0
 neighbor 10.0.0.7 remote-as 4652
 !
 address-family ipv4
  network 212.51.160.0 mask 255.255.224.0
  neighbor 192.168.1.1 activate
  neighbor 10.0.0.7 activate
 exit-address-family
 !
 address-family ipv6
  network 2001:db8::/36
  neighbor 192.168.1.1 activate
  neighbor 10.0.0.7 activate
 exit-address-family
```
