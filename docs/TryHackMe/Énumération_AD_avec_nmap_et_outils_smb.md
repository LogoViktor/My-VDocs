# Énumération Active Directory avec Nmap et Outils SMB

Ce guide vous explique comment identifier et énumérer les services Active Directory (AD) et les partages SMB dans un réseau cible à l'aide d'outils comme **Nmap**, **smbclient**, et **smbmap**. Ces techniques sont essentielles pour découvrir des informations critiques lors d'un test d'intrusion.

---

## 📋 Prérequis

- Une machine Linux (comme Kali Linux ou une VM Debian).
- Accès à un réseau cible ou à un environnement de test (comme TryHackMe).
- Outils installés : Nmap, Samba (pour `smbclient` et `smbmap`).
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

## 📁 Énumération des Partages SMB

### 1. Utilisation de `smbclient` pour lister les partages

Pour lister les partages SMB accessibles, utilisez `smbclient` avec une connexion anonyme :

```bash
smbclient -L //10.211.11.10 -N
```

- `-L` : Liste les partages disponibles.
- `-N` : Connexion anonyme (sans mot de passe).

Exemple de sortie :
```
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
AnonShare       Disk
C$              Disk      Default share
IPC$            IPC       Remote IPC
NETLOGON        Disk      Logon server share
SharedFiles     Disk
SYSVOL          Disk      Logon server share
UserBackups     Disk
```

---

### 2. Utilisation de `smbmap` pour énumérer les permissions

Pour identifier les permissions des partages, utilisez `smbmap` :

```bash
smbmap -H 10.211.11.10
```

Exemple de sortie :
```
Disk                     Permissions     Comment
----                     -----------     -------
ADMIN$                   NO ACCESS       Remote Admin
AnonShare                READ, WRITE
C$                       NO ACCESS       Default share
IPC$                     NO ACCESS       Remote IPC
NETLOGON                 NO ACCESS       Logon server share
SharedFiles              READ, WRITE
SYSVOL                   NO ACCESS       Logon server share
UserBackups              READ, WRITE
```

---

### 3. Accès aux partages SMB

Pour accéder à un partage SMB, utilisez `smbclient` :

```bash
smbclient //10.211.11.10/SharedFiles -N
```

- Téléchargez un fichier avec la commande `get` :
  ```bash
  get Mouse_and_Malware.txt
  ```

---

## 🔧 Autres Outils Utiles

### 1. **Impacket**
Utilisez `impacket-smbclient` pour une alternative Python à `smbclient`.

### 2. **CrackMapExec**
Outil puissant pour l'énumération et les tests de crédentials sur SMB.

### 3. **enum4linux**
Pour une énumération approfondie des services SMB :
```bash
enum4linux -a 10.211.11.10
```

---

## 📌 Notes Supplémentaires

- **Sécurité** : Les partages SMB anonymes sont une mauvaise pratique, mais peuvent exister pour des raisons de compatibilité avec des systèmes hérités.
- **Exploitation** : Les fichiers trouvés dans les partages SMB peuvent contenir des informations sensibles comme des identifiants ou des configurations.
- **Tests** : Toujours tester les permissions et les accès après chaque modification ou découverte.