# Configuration d'OSPFv3 (Déclaration par Interfaces)

Ce guide vous explique comment configurer **OSPFv3** sur des routeurs en utilisant une **déclaration par interfaces**. OSPFv3 est la version d'OSPF conçue pour fonctionner avec **IPv6**.

---

## 📋 Prérequis

- Deux routeurs (ou plus) compatibles OSPFv3.
- Une topologie réseau connectée (par exemple, via des interfaces Ethernet ou série).
- Accès en ligne de commande (CLI) aux routeurs.
- IPv6 activé et configuré sur les interfaces.

---

## 🌐 Configuration de Base d'OSPFv3

### 1. Activer OSPFv3 sur le routeur

Pour activer OSPFv3 sur un routeur Cisco, utilisez la commande suivante en mode configuration globale :

```bash
Router> enable
Router# configure terminal
Router(config)# ipv6 router ospf <ID_Processus>
```

- `<ID_Processus>` : Identifiant du processus OSPFv3 (par exemple, `1`).

---

### 2. Déclarer les interfaces OSPFv3

Avec OSPFv3, la configuration se fait directement sur les interfaces.

#### Exemple de configuration :

```bash
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 ospf <ID_Processus> area <ID_Area>
```

- `<ID_Processus>` : Doit correspondre à l'ID du processus OSPFv3 activé précédemment.
- `<ID_Area>` : Identifiant de l'aire OSPF (par exemple, `0` pour l'aire principale).

---

### 3. Configuration complète par interface

Voici un exemple complet de configuration pour un routeur avec deux interfaces IPv6 :

```bash
Router> enable
Router# configure terminal

! Activation du processus OSPFv3
Router(config)# ipv6 router ospf 1

! Configuration de l'interface GigabitEthernet0/0
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 address 2001:DB8:1::1/64
Router(config-if)# ipv6 ospf 1 area 0
Router(config-if)# no shutdown

! Configuration de l'interface GigabitEthernet0/1
Router(config)# interface GigabitEthernet0/1
Router(config-if)# ipv6 address 2001:DB8:2::1/64
Router(config-if)# ipv6 ospf 1 area 0
Router(config-if)# no shutdown

Router(config)# end
Router# write memory
```

---

## 🔧 Configuration Avancée

### 1. Définir le coût d'une interface

Le coût OSPFv3 peut être défini manuellement pour influencer le chemin emprunté par le trafic.

```bash
Router(config-if)# ipv6 ospf cost <valeur>
```

- `<valeur>` : Coût de l'interface (par exemple, `100`).

---

### 2. Configurer l'authentification OSPFv3

OSPFv3 utilise **IPsec** pour l'authentification. Voici un exemple de configuration de base :

```bash
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 ospf authentication ipsec spi 500 md5 <mot_de_passe>
```

- `<mot_de_passe>` : Mot de passe pour l'authentification.

---

### 3. Configurer le type de réseau OSPFv3

OSPFv3 peut fonctionner en différents modes selon le type de réseau.

```bash
Router(config-if)# ipv6 ospf network <type>
```

- `<type>` : Type de réseau (par exemple, `point-to-point`).

---

## 🔍 Vérification de la Configuration

### 1. Vérifier les voisins OSPFv3

Pour vérifier que les routeurs OSPFv3 se voient mutuellement :

```bash
Router# show ipv6 ospf neighbor
```

---

### 2. Vérifier les routes OSPFv3

Pour afficher les routes IPv6 apprises via OSPFv3 :

```bash
Router# show ipv6 route ospf
```

---

### 3. Vérifier les interfaces OSPFv3

Pour voir les interfaces sur lesquelles OSPFv3 est activé :

```bash
Router# show ipv6 ospf interface brief
```

---

## 📌 Notes et Bonnes Pratiques

- **ID de routeur** : Assurez-vous que chaque routeur a un ID unique (par défaut, c'est la plus haute adresse IPv6 de loopback ou d'interface active).
- **Aires OSPF** : Utilisez des aires pour segmenter votre réseau et optimiser les performances.
- **Sécurité** : Activez toujours l'authentification pour sécuriser les échanges OSPFv3.
- **Coût des interfaces** : Ajustez le coût des interfaces pour influencer le chemin emprunté par le trafic.
- **IPv6** : Assurez-vous que IPv6 est correctement configuré sur toutes les interfaces.

---