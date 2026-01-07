---
title: "NFS"
---
# Configuration de NFS

Ce guide vous explique comment installer et configurer un serveur **NFS** (Network File System) sous Debian, ainsi que la configuration du client pour accéder aux partages.

---

## 📦 Installation du service NFS

### 1. Mettre à jour la liste des paquets
```bash
sudo apt update
```

### 2. Installer NFS
```bash
sudo apt install nfs-kernel-server
```

### 3. Vérifier la disponibilité du service
```bash
sudo systemctl status nfs-kernel-server
```

---

## 🔧 Configuration du serveur NFS

### 1. Créer les utilisateurs et groupes
```bash
sudo adduser walter
sudo adduser jessie
sudo adduser badger
sudo adduser hank
sudo addgroup chemist
sudo addgroup cops
sudo adduser walter chemist
sudo adduser jessie chemist
sudo adduser hank cops
```

### 2. Créer le répertoire à partager
```bash
sudo mkdir -p /home/secretlab
sudo chown -R root:chemist /home/secretlab
sudo chmod -R 2770 /home/secretlab
```

### 3. Configurer le fichier `/etc/exports`
Éditez le fichier `/etc/exports` :
```bash
sudo nano /etc/exports
```
Ajoutez la ligne suivante :
```
/home/secretlab  172.16.0.0/16(rw,sync,no_subtree_check)
```

### 4. Redémarrer le service NFS
```bash
sudo systemctl restart nfs-kernel-server
```

### 5. Vérifier le partage
```bash
showmount -e 127.0.0.1
```

---

## 💻 Configuration du client NFS

### 1. Installer le paquet NFS sur le client
```bash
sudo apt install nfs-common
```

### 2. Créer les utilisateurs et groupes sur le client
```bash
sudo adduser walter
sudo adduser jessie
sudo adduser badger
sudo adduser hank
sudo addgroup chemist
sudo addgroup cops
sudo adduser walter chemist
sudo adduser jessie chemist
sudo adduser hank cops
```

### 3. Créer le point de montage
```bash
sudo mkdir -p /mnt/labaccess
```

### 4. Monter le partage
Remplacez `172.16.0.X` par l'adresse IP de votre serveur NFS :
```bash
sudo mount -t nfs 172.16.0.X:/home/secretlab /mnt/labaccess
```

### 5. Vérifier le montage
```bash
df -h
```

---

## 📌 Notes supplémentaires

- **Permissions** : Assurez-vous que les utilisateurs et groupes ont les bonnes permissions sur le serveur et le client.
- **Sécurité** : Limitez l'accès au partage NFS en utilisant des plages d'adresses IP restreintes.
- **Montage automatique** : Pour monter automatiquement le partage au démarrage, ajoutez une entrée dans `/etc/fstab` :
  ```
  172.16.0.X:/home/secretlab  /mnt/labaccess  nfs  defaults  0  0
  ```