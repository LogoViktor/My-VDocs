# <u>Configuration de NFS :</u>

### <u>Installation du service NFS :</u>

1. **Mettre à jour la liste des paquets :**
   ```bash
   sudo apt update
   ```
2. **Installer NFS :** 
   ```bash
   sudo apt install nfs-kernel-server
   ```
3. **Vérifier la disponibilité du service :**
   ```bash
   sudo systemctl status nfs-kernel-server
   ```

### <u>Configuration du serveur NFS :</u>

1. **Créer les utilisateurs et groupes sur le serveur :**
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
2. **Créer le répertoire à partager :**
   ```bash
   sudo mkdir -p /home/secretlab
   sudo chown -R root\:chemist /home/secretlab
   sudo chmod -R 2770 /home/secretlab
   ```
3. **Configurer le fichier /etc/exports :**
   ```bash
   sudo nano /etc/exports
   #Ajoutez :
   /home/secretlab  172.16.0.0/16(rw,sync,no_subtree_check)
   ```
4. **Redémarrer le service NFS :**
   ```bash
   sudo systemctl restart nfs-kernel-server
   ```
5. **Vérifier le partage :**
   ```bash
   showmount -e 127.0.0.1
   ```

### <u>Configuration du client NFS :</u>

1. **Installer le paquet NFS sur le client :**
   ```bash
   sudo apt install nfs-common
   ```
2. **Créer les utilisateurs et groupes sur le client :**
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
3. **Créer le point de montage :**
   ```bash
   sudo mkdir -p /mnt/labaccess
   ```
4. **Monter le partage :**
   ```bash
   sudo mount -t nfs 172.16.0.X:/home/secretlab /mnt/labaccess
   # Adapter l'adrresse IP en fonction de l'ip du serveur
   ```
5. **Vérifier le montage :**
   ```bash
   df -h
   ```