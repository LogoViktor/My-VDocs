# Configuration d'OSPFv2 (Déclaration par Interfaces)

Ce guide vous explique comment configurer **OSPFv2** sur des routeurs en utilisant une **déclaration par interfaces**.
OSPFv2 est la version d’OSPF conçue pour fonctionner avec **IPv4**.

---

## 📋 Prérequis

* Deux routeurs (ou plus) compatibles OSPFv2.
* Une topologie réseau connectée (par exemple, via des interfaces Ethernet ou série).
* Accès en ligne de commande (CLI) aux routeurs.
* IPv4 activé et configuré sur les interfaces.

---

## 🌐 Configuration de Base d'OSPFv2

### 1. Activer OSPFv2 sur le routeur

Pour activer OSPFv2 sur un routeur Cisco, utilisez la commande suivante en mode configuration globale :

```bash
Router> enable
Router# configure terminal
Router(config)# router ospf <ID_Processus>
```

* `<ID_Processus>` : Identifiant du processus OSPFv2 (par exemple, `1`).

---

### 2. Déclarer les interfaces OSPFv2

Avec la configuration par **interface**, OSPFv2 est activé directement sur les interfaces réseau.

#### Exemple de configuration :

```bash
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip ospf <ID_Processus> area <ID_Area>
```

* `<ID_Processus>` : Doit correspondre à l’ID du processus OSPFv2 activé précédemment.
* `<ID_Area>` : Identifiant de l’aire OSPF (par exemple, `0` pour l’aire principale).

---

### 3. Configuration complète par interface

Voici un exemple complet de configuration pour un routeur avec deux interfaces IPv4 :

```bash
Router> enable
Router# configure terminal

! Activation du processus OSPFv2
Router(config)# router ospf 1

! Configuration de l'interface GigabitEthernet0/0
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# ip ospf 1 area 0
Router(config-if)# no shutdown

! Configuration de l'interface GigabitEthernet0/1
Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip address 192.168.2.1 255.255.255.0
Router(config-if)# ip ospf 1 area 0
Router(config-if)# no shutdown

Router(config)# end
Router# write memory
```

---

## 🔧 Configuration Avancée

### 1. Définir le coût d'une interface

Le coût OSPFv2 peut être défini manuellement pour influencer le choix du chemin utilisé par le trafic.

```bash
Router(config-if)# ip ospf cost <valeur>
```

* `<valeur>` : Coût de l’interface (par exemple, `100`).

---

### 2. Configurer l’authentification OSPFv2

OSPFv2 permet une authentification simple basée sur un mot de passe.

#### Exemple de configuration :

```bash
Router(config-if)# ip ospf authentication
Router(config-if)# ip ospf authentication-key <mot_de_passe>
```

* `<mot_de_passe>` : Mot de passe utilisé pour l’authentification OSPF.

---

### 3. Configurer le type de réseau OSPFv2

OSPFv2 peut être configuré pour fonctionner sur différents types de réseau selon la topologie.

```bash
Router(config-if)# ip ospf network <type>
```

* `<type>` : Type de réseau (par exemple, `broadcast`, `point-to-point`, etc.).

---

## 🔍 Vérification de la Configuration

### 1. Vérifier les voisins OSPFv2

Pour vérifier que les routeurs OSPFv2 ont établi des relations de voisinage :

```bash
Router# show ip ospf neighbor
```

---

### 2. Vérifier les routes OSPFv2

Pour afficher les routes IPv4 apprises via OSPFv2 :

```bash
Router# show ip route ospf
```

---

### 3. Vérifier les interfaces OSPFv2

Pour voir les interfaces sur lesquelles OSPFv2 est activé :

```bash
Router# show ip ospf interface brief
```

---

## 📌 Notes et Bonnes Pratiques

* **ID de routeur** : Chaque routeur doit avoir un ID unique (par défaut, la plus haute adresse IPv4 de loopback ou d’interface active).
* **Aires OSPF** : Utilisez les aires pour segmenter votre réseau et améliorer les performances.
* **Sécurité** : Activez toujours l’authentification OSPF pour sécuriser les échanges.
* **Coût des interfaces** : Ajustez le coût pour influencer le chemin emprunté par le trafic.
* **Compatibilité** : OSPFv2 fonctionne uniquement avec **IPv4**.

---
