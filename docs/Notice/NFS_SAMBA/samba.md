---
title: "SAMBA"
---
# Configuration de Samba

Ce guide vous explique comment installer et configurer un serveur **Samba** sous Debian, ainsi que la configuration des partages et des utilisateurs.

---

## 📦 Installation du service Samba

### 1. Installer les paquets Samba
```bash
sudo apt install samba
sudo apt install smbclient
```

### 2. Configurer le fichier `/etc/samba/smb.conf`
Éditez le fichier de configuration principal :
```bash
sudo nano /etc/samba/smb.conf
```

Ajoutez ou modifiez les lignes suivantes dans la section `[global]` :
```plaintext
[global]
   workgroup = ETUDIANTGx
   netbios name = ServeurGx
   security = user
   ###### Authentication #####
   # Commenter la dernière ligne :
   # map to guest = bad user
```

### 3. Vérifier la configuration
```bash
testparm
```

### 4. Redémarrer les services Samba
```bash
sudo systemctl restart smbd nmbd
```

---

## 👥 Configuration des utilisateurs Samba

### 1. Créer les utilisateurs Unix
```bash
sudo adduser geralt
# Mot de passe : Geralt1
sudo adduser ciri
# Mot de passe : Ciri1
sudo adduser yennefer
# Mot de passe : Yennefer1
```

### 2. Ajouter les utilisateurs Samba
```bash
sudo smbpasswd -a geralt
# Mot de passe : Geralt1
sudo smbpasswd -a ciri
# Mot de passe : Ciri1
sudo smbpasswd -a yennefer
# Mot de passe : Yennefer1
```

---

## 🗄️ Configuration des partages Samba

### 1. Créer les répertoires à partager
```bash
sudo mkdir -p /home/public /home/magic
sudo chown -R root:users /home/public
sudo chmod -R 1777 /home/public
sudo chown -R root:users /home/magic
sudo chmod -R 770 /home/magic
```

### 2. Configurer les partages dans `/etc/samba/smb.conf`
Éditez le fichier de configuration :
```bash
sudo nano /etc/samba/smb.conf
```

Ajoutez les sections suivantes à la fin du fichier :
```plaintext
[public]
   path = /home/public
   browsable = yes
   writable = yes
   guest ok = yes

[magic]
   path = /home/magic
   browsable = yes
   writable = yes
   read only = no
   valid users = ciri yennefer
```

### 3. Redémarrer les services Samba
```bash
sudo systemctl restart smbd nmbd
```

---

## 🧪 Test des partages Samba

### 1. Depuis un poste Windows
- Ouvrez l'explorateur de fichiers et tapez :
  ```
  \\ServeurGx
  ```
- Connectez-vous avec les identifiants des utilisateurs créés.

### 2. Depuis un poste Linux
- Installez `cifs-utils` pour monter les partages :  
  ```bash  
  sudo apt install cifs-utils
  ```  
  ```bash  
  sudo mkdir -p /mnt/point_montage
  ```  
  ```bash  
  sudo mount -t cifs //ServeurGx/public /mnt/point_montage -o username=geralt,password=Geralt1
  ```

---

## 📌 Notes supplémentaires

- **Permissions** : Assurez-vous que les utilisateurs et groupes ont les bonnes permissions sur les répertoires partagés.
- **Sécurité** : Utilisez des mots de passe robustes pour les utilisateurs Samba.
- **Montage automatique** : Pour monter automatiquement les partages au démarrage, ajoutez une entrée dans `/etc/fstab` :
  ```plaintext
  //ServeurGx/public  /mnt/point_montage  cifs  username=geralt,password=Geralt1,uid=1000,gid=1000  0  0
  ```
