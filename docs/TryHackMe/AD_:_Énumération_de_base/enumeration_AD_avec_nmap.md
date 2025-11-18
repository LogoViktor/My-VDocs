# Énumération Active Directory avec Nmap

Ce guide vous explique comment utiliser **Nmap** pour identifier et énumérer les services Active Directory (AD) dans un réseau cible. Ces techniques sont essentielles pour découvrir des informations critiques lors d'un test d'intrusion.

---

## 📋 Prérequis

- Une machine Linux (comme Kali Linux ou une VM Debian).
- Accès à un réseau cible ou à un environnement de test (comme TryHackMe).
- Nmap installé.
- Connaissances de base en ligne de commande.

---

## 🔍 Identification des Hôtes Actifs

### 1. Utilisation de `fping` pour découvrir les hôtes actifs

Pour identifier les hôtes actifs dans un sous-réseau, utilisez `fping` avec les options suivantes :

```bash
fping -agq 10.211.11.0/24
```

- `-a` : Affiche uniquement les hôtes actifs.
- `-g` : Génère une liste de cibles à partir d'une plage d'adresses IP.
- `-q` : Mode silencieux, sans affichage des erreurs.

Exemple de sortie :
```
10.211.11.1
10.211.11.10
10.211.11.20
10.211.11.250
```

---

### 2. Utilisation de Nmap pour un scan ping

Pour confirmer les hôtes actifs avec Nmap, utilisez la commande suivante :

```bash
nmap -sn 10.211.11.0/24
```

- `-sn` : Effectue un scan ping pour identifier les hôtes actifs sans scanner les ports.

---

## 🎯 Identification des Services Active Directory

### 1. Scan des ports spécifiques liés à Active Directory

Pour identifier les services Active Directory, utilisez Nmap pour scanner les ports suivants : 88 (Kerberos), 135 (RPC), 139 (NetBIOS), 389 (LDAP), 445 (SMB), et 636 (LDAPS).

```bash
nmap -p 88,135,139,389,445,636 -sV -sC -iL hosts.txt
```

- `-sV` : Détecte les versions des services en cours d'exécution.
- `-sC` : Exécute les scripts NSE par défaut pour une énumération approfondie.
- `-iL hosts.txt` : Lit la liste des hôtes cibles depuis un fichier.

Exemple de sortie :
```
PORT    STATE SERVICE      VERSION
88/tcp  open  kerberos-sec Microsoft Windows Kerberos
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp open  ldap         Microsoft Windows Active Directory LDAP
445/tcp open  microsoft-ds Windows Server 2019 Datacenter 17763 microsoft-ds
636/tcp open  tcpwrapped
```

---

### 2. Identification du Contrôleur de Domaine (DC)

Un contrôleur de domaine est généralement identifiable par les ports ouverts suivants :
- **88 (Kerberos)** : Utilisé pour l'authentification.
- **389 (LDAP)** : Protocole pour accéder aux annuaires Active Directory.
- **445 (SMB)** : Partage de fichiers et administration à distance.

---

### 3. Scan complet des ports (optionnel)

Pour un scan complet des ports ouverts sur les hôtes identifiés, utilisez :

```bash
nmap -sS -p- -T3 -iL hosts.txt -oN full_port_scan.txt
```

- `-sS` : Scan TCP SYN, plus furtif.
- `-p-` : Scan tous les ports TCP.
- `-T3` : Équilibre entre vitesse et discrétion.
- `-oN` : Enregistre les résultats dans un fichier.

---

## 📌 Notes Supplémentaires

- **Sécurité** : Assurez-vous d'avoir l'autorisation avant de scanner un réseau.
- **Optimisation** : Adaptez les options de Nmap en fonction de vos besoins (furtivité, vitesse, etc.).
- **Analyse** : Les résultats peuvent révéler des services vulnérables ou mal configurés.
