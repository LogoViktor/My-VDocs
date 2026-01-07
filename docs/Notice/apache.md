---
title: "APACHE"
---
# Configuration d'Apache sous Debian

Ce guide vous explique comment installer et configurer un serveur **Apache** sous Debian, y compris la gestion des Virtual Hosts basés sur l'IP.

---

## 📋 Prérequis

- Une machine serveur Debian.
- Une machine cliente avec un navigateur web.
- Accès à une VM Debian ou une machine Linux avec interface graphique.

---

## 🌐 Configuration du Serveur Web Apache

### 1. Installation d'Apache2

Mettez à jour la liste des paquets et installez Apache2 :

```bash
sudo apt update
sudo apt install apache2
```

### 2. Vérification du service Apache

Vérifiez que le service Apache est actif :

```bash
sudo systemctl status apache2
```

### 3. Test d'accès au serveur

- Ouvrez un navigateur web.
- Accédez à `http://<adresse_IP_du_serveur>` pour vérifier que le serveur Apache fonctionne correctement.

---

## 🔧 Configuration de Virtual Hosts basés sur l'IP

### 1. Configuration des adresses IP virtuelles

Utilisez `nmtui` pour ajouter des adresses IP statiques supplémentaires à votre serveur.

---

### 2. Création des fichiers de configuration des Virtual Hosts

Créez un fichier de configuration pour vos Virtual Hosts :

```bash
sudo nano /etc/apache2/sites-available/myvhost.conf
```

Ajoutez les configurations suivantes (adaptez les adresses IP selon votre plan d'adressage) :

```apache
# Premier Virtual Host sur la première IP
<VirtualHost 192.168.111.46:80>
    ServerName premierVHost
    DocumentRoot /var/www/sitePrincipal
    <Directory /var/www/sitePrincipal>
        Require all granted
    </Directory>
</VirtualHost>

# Deuxième Virtual Host sur la deuxième IP
<VirtualHost 192.168.111.47:80>
    ServerName deuxiemeVHost
    DocumentRoot /srv/siteSecondaire
    <Directory /srv/siteSecondaire>
        Require all granted
    </Directory>
</VirtualHost>
```

---

### 3. Création des répertoires et des fichiers `index.html`

Créez les répertoires pour vos sites et ajoutez-y un fichier `index.html` :

```bash
sudo mkdir -p /var/www/sitePrincipal
sudo mkdir -p /srv/siteSecondaire
echo "<h1>Bienvenue sur le site principal !</h1>" | sudo tee /var/www/sitePrincipal/index.html
echo "<h1>Bienvenue sur le site secondaire !</h1>" | sudo tee /srv/siteSecondaire/index.html
```

---

### 4. Activation des Virtual Hosts

Désactivez le site par défaut et activez votre nouveau site :

```bash
sudo a2dissite 000-default
sudo a2ensite myvhost
sudo systemctl restart apache2
```

---

### 5. Modification des ports d'écoute

Modifiez le fichier `/etc/apache2/ports.conf` pour ajouter les ports 8080 et 8081 :

```bash
sudo nano /etc/apache2/ports.conf
```

Ajoutez les lignes suivantes :

```apache
Listen 80
Listen 8080
```

Redémarrez Apache pour appliquer les modifications :

```bash
sudo systemctl restart apache2
```

---

## 📌 Notes supplémentaires

- **Permissions** : Assurez-vous que les répertoires de vos sites ont les bonnes permissions pour que Apache puisse y accéder.
- **Sécurité** : Pour une meilleure sécurité, envisagez de configurer HTTPS avec Let's Encrypt.
- **Tests** : Après chaque modification, testez vos sites pour vérifier qu'ils sont accessibles.