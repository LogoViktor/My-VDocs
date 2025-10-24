# <u>Configuaration de Samba :</u>

## <u>Installation du service Samba :</u>

1. **Installer les paquets Samba :**
   ```bash
   sudo apt install samba
   sudo apt install smbclient
   ```
2. **Configurer le fichier /etc/samba/smb.conf :**
   ```bash
   sudo nano /etc/samba/smb.conf
   ```
   - Ajouter dans la section [global] :
   ```bash
   [global]
      workgroup = ETUDIANTGx
      netbios name = ServeurGx
      security = user
    ###### Authentication #####
    # Commenter la derniere ligne :
    # map to guest = bad user
    ```
3. **Vérifier la configuration :**
   ```bash
   testparm
   ```
4. **Redémarrer les services Samba :**
   ```bash
   sudo systemctl restart smbd nmbd
   ```

### <u>Configuration des utilisateurs Samba :</u>

1. **Créer les utilisateurs Unix et Samba :**
   ```bash
   sudo adduser geralt
   # Entrez "Geralt1" comme mot de passe
   sudo adduser ciri
   # Entrez "Ciri1" comme mot de passe
   sudo adduser yennefer
   # Entrez "Yennefer1" comme mot de passe
   ```
   - Ajouter les utilisateurs Samba :
   ```bash
   sudo smbpasswd -a geralt
   # Entrez "Geralt1" comme mot de passe
   sudo smbpasswd -a ciri
   # Entrez "Ciri1" comme mot de passe
   sudo smbpasswd -a yennefer
   # Entrez "Yennefer1" comme mot de passe
   ```

### <u>Configuration des partages Samba :</u>

1. **Créer les répertoires à partager :**
   ```bash
   sudo mkdir -p /home/public /home/magic
   sudo chown -R root:users /home/public
   sudo chmod -R 1777 /home/public
   sudo chown -R root:users /home/magic
   sudo chmod -R 770 /home/magic
   ```
2. **Configurer le fichier /etc/samba/smb.conf pour les partages :**
   ```bash
   sudo nano /etc/samba/smb.conf
   ```
   - Ajouter :
   ```bash
   [public]
      path = /home/public
      browsable = yes
      writable = yes
      guest ok = yes

   [magic]
      path = /home/magic
      browsable = yes
      writable = yes
      read only = yes
      valid users = ciri yennefer
    ```
3. **Redémarrer les services Samba :**
   ```bash
   sudo systemctl restart smbd nmbd
   ```

### <u>Test des partages Samba :</u>

1. **Depuis un poste Windows :**
   - Ouvrir l'explorateur de fichiers et taper 
   ```
   \\ServeurGx
   ```
   - Se connecter avec les identifiants des utilisateurs créés.

2. **Depuis un poste Linux :**
   - Utiliser smbclient pour tester les connexions.
   - Monter les partages avec cifs-utils :
   ```bash
   sudo apt install cifs-utils
   sudo mkdir -p /mnt/point_montage
   sudo mount -t cifs //ServeurGx/partage_distant /mnt/point_montage
   ```