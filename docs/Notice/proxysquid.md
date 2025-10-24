# Configuration du Serveur Proxy Squid

Ce guide vous explique comment installer, configurer et sécuriser un serveur proxy **Squid** sous Debian, incluant la gestion des ACL, l'authentification des utilisateurs et le filtrage avancé avec SquidGuard.

---

## 📦 Installation de Squid

### 1. Installer Squid
Mettez à jour la liste des paquets et installez Squid :

```bash
sudo apt update && sudo apt install squid
```

### 2. Configurer le proxy sur le navigateur
- Dans les paramètres réseau de votre navigateur, configurez :
  - **Adresse IP** : L'adresse IP de votre serveur Squid.
  - **Port** : `3128`.

---

## 🔧 Configuration du fichier `squid.conf`

### 1. Sauvegarder et nettoyer le fichier de configuration
Avant de modifier le fichier de configuration, faites une sauvegarde et nettoyez-le :

```bash
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.backup
cat /etc/squid/squid.conf.backup | grep -v ^\# | grep -v ^\$ > /etc/squid/squid.conf
```

---

### 2. Activer le cache
Ajoutez les paramètres de cache à la fin du fichier `/etc/squid/squid.conf` :

```bash
sudo nano /etc/squid/squid.conf
```

Ajoutez les lignes suivantes :

```plaintext
shutdown_lifetime 2 seconds
cache_effective_user proxy
cache_effective_group proxy
cache_mem 16 MB
cache_dir ufs /var/spool/squid 120 16 128
```

---

## 🔐 Configuration des ACL

### 1. Définir les ACL pour le réseau local
Ouvrez le fichier de configuration de Squid :

```bash
sudo nano /etc/squid/squid.conf
```

Ajoutez les lignes suivantes pour autoriser l'accès au réseau local :

```plaintext
# Définir une ACL pour le réseau local
acl lan src 192.168.111.0/24  # Adapter avec votre plan IP
# Autoriser l'accès pour le réseau local
http_access allow lan
```

---

### 2. Configuration des plages horaires
Ajoutez les ACL pour limiter l'accès à certaines heures :

```plaintext
acl allowed_hosts src 192.168.111.10 192.168.111.11
acl limithour time MTWHF 08:00-17:00
http_access allow allowed_hosts limithour
```

---

## 👥 Authentification des Utilisateurs

### 1. Créer les utilisateurs avec `htpasswd`
Créez un fichier pour stocker les utilisateurs et ajoutez des comptes :

```bash
sudo touch /etc/squid/squidusers
sudo htpasswd -b /etc/squid/squidusers bob sponge
sudo htpasswd -b /etc/squid/squidusers patrick star
```

---

### 2. Configurer l'authentification dans `squid.conf`
Ajoutez les paramètres d'authentification au début du fichier `/etc/squid/squid.conf` :

```bash
sudo nano /etc/squid/squid.conf
```

Ajoutez les lignes suivantes :

```plaintext
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/squidusers
auth_param basic children 5
auth_param basic realm TP4 R203
authenticate_ttl 1 hour
authenticate_ip_ttl 60 seconds
acl utilisateurs proxy_auth REQUIRED
http_access allow utilisateurs
```

---

## 🛡️ Filtrage Avancé avec SquidGuard

### 1. Installer SquidGuard
Installez SquidGuard pour un filtrage avancé :

```bash
sudo apt install squidguard
```

---

### 2. Configurer SquidGuard
Téléchargez et configurez la liste noire pour SquidGuard :

```bash
cd /var/lib/squidguard/db
sudo wget http://dsi.ut-capitole.fr/blacklists/blacklists.tar.gz
sudo tar -xf blacklists.tar.gz
sudo cp -r blacklists/* .
```

---

### 3. Modifier le fichier `squidGuard.conf`
Éditez le fichier de configuration de SquidGuard :

```bash
sudo nano /etc/squid/squidGuard.conf
```

Ajoutez ou modifiez les lignes suivantes :

```plaintext
dbhome /var/lib/squidguard/db
logdir /var/log/squid
src lan {
   ip 192.168.111.0-192.168.111.254
}
dest games {
    domainlist games/domains
    urllist games/urls
}
dest local {
}
acl {
    lan {
        pass !games all
        redirect http://@IP_Apache/proxy.html
    }
    default {
      pass local none
   }
}
```

---

### 4. Redémarrer et tester
Redémarrez Squid et SquidGuard pour appliquer les modifications, puis testez l'accès :

```bash
sudo systemctl restart squid
sudo systemctl restart squidguard
```

---

## 📌 Notes supplémentaires

- **Permissions** : Assurez-vous que les fichiers de configuration et les répertoires ont les bonnes permissions.
- **Sécurité** : Pour une meilleure sécurité, limitez l'accès aux adresses IP spécifiques et utilisez des mots de passe robustes.
- **Tests** : Après chaque modification, testez votre configuration pour vérifier que tout fonctionne correctement.