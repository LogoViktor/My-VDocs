---
title: "Installation et configuration de base"
---
# Installation de Windows Server 2019 et du service Active Directory

Ce guide vous explique comment installer **Windows Server 2019** et configurer le service **Active Directory** pour transformer votre serveur en contrôleur de domaine.

---

## 🖥️ Installation du système d'exploitation Windows Server 2019

### 1. Configuration d'une VM pour Windows Server 2019

Voici un exemple de configuration pour une machine virtuelle :

- **ISO image** : Téléchargez l'ISO de Windows Server 2019.
- **Type** : Microsoft Windows.
- **Version** : Windows Server 2019.
- **RAM** : 8192 MB.
- **CPU** : 6 cœurs.
- **Carte réseau** : Accès par pont.

---

### 2. Configuration de la VM

Après la création de la VM :

- Dans **Configuration > System** :
  - Activer **EFI**.
  - Activer **"Enable secure boot"**.
  - Cliquer sur **"Reset keys to default"**.

---

### 3. Installation de Windows Server 2019

Lorsque l'OS se lance :

- Cliquez sur **"Je n'ai pas de clé de produit"**.
- Sélectionnez **Windows Server 2019 Standard (expérience de bureau)**.
- Sélectionnez le seul disque disponible.
- Entrez un mot de passe administrateur.
- Installez puis redémarrez.

---

## 🔧 Configuration de Windows Server

### 1. Configurer l'interface réseau

Configurez l'interface réseau du serveur avec une **adresse IP statique** incluse dans la plage IP de votre réseau pour éviter les renouvellements de bail DHCP.

---

### 2. Changer le nom du serveur

- Allez dans **Serveur local > Nom de l'ordinateur > Modifier**.
- Renommez le serveur, par exemple : `W2019_nom_de_famille`.

---

### 3. Test de configuration

- Testez la connectivité en effectuant un **ping** vers le PC client.

---

## 🏢 Installation d'Active Directory

### 1. Installation d'un nouveau domaine

- Dans le **tableau de bord de Windows Server** :
  - Cliquez sur **Ajouter des rôles et des fonctionnalités**.
  - Sélectionnez le serveur à configurer.
  - Allez dans **Rôles de serveurs > Services AD DS**.
  - Cliquez sur **Suivant** jusqu'à l'installation.

---

## 🌐 Configuration du serveur DNS

### 1. Installation du rôle DNS

- Dans le **tableau de bord de Windows Server** :
  - Cliquez sur **Ajouter des rôles et des fonctionnalités**.
  - Sélectionnez le serveur à configurer.
  - Allez dans **Rôles de serveurs > Serveur DNS**.
  - Cliquez sur **Suivant** jusqu'à l'installation.

---

### 2. Configurer la carte réseau

- Configurez la carte réseau du serveur avec l'adresse DNS suivante : `127.0.0.1` (loopback).

---

## 🔄 Conversion du serveur en contrôleur de domaine

### 1. Promouvoir le serveur en contrôleur de domaine

- Dans le **gestionnaire de serveur**, cliquez sur le drapeau > **Promouvoir ce serveur en contrôleur de domaine**.
- Sélectionnez **Configuration de déploiement > Ajouter une nouvelle forêt**.
- Entrez le **nom de domaine racine** (par exemple : `PAILLART.test`).

---

## 📌 Notes supplémentaires

- **Adresses IP** : Assurez-vous que les adresses IP statiques sont correctement configurées pour éviter les conflits.
- **Sécurité** : Utilisez des mots de passe robustes pour le compte administrateur.
- **Tests** : Après chaque étape, testez la connectivité et la configuration pour vérifier que tout fonctionne correctement.