# 🐳 Guide Complet : Docker pour les Réseaux et Systèmes

Ce guide présente les commandes essentielles et la configuration de base pour utiliser Docker dans le cadre de vos projets ou de votre administration système.

## 📋 1. Installation de Docker sous Debian
Mettez à jour vos paquets et installez les dépendances nécessaires :
```bash
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common

```

Téléchargez la clé GPG officielle et ajoutez le dépôt Docker :

```bash
curl -fsSL [https://download.docker.com/linux/debian/gpg](https://download.docker.com/linux/debian/gpg) | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] [https://download.docker.com/linux/debian](https://download.docker.com/linux/debian) $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```

Installez enfin le moteur Docker :

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

```

## 🚀 2. Commandes de Base

Voici un récapitulatif des commandes indispensables pour gérer vos conteneurs au quotidien :

| Commande | Description |
| --- | --- |
| `docker run -d -p 80:80 nginx` | Télécharge et lance un conteneur Nginx en arrière-plan sur le port 80 |
| `docker ps` | Liste les conteneurs actuellement en cours d'exécution |
| `docker ps -a` | Liste tous les conteneurs (actifs et arrêtés) |
| `docker stop <ID>` | Arrête un conteneur en cours |
| `docker rm <ID>` | Supprime un conteneur arrêté |
| `docker images` | Affiche la liste des images présentes sur la machine |

!!! info "Astuce d'administration"
Pour exécuter des commandes Docker sans avoir à taper `sudo` à chaque fois, ajoutez votre utilisateur courant au groupe docker :
`sudo usermod -aG docker $USER` (nécessite de fermer et rouvrir votre session).

## 📄 3. Exemple de Fichier Dockerfile minimaliste

Pour créer votre propre image personnalisée :

```dockerfile
FROM debian:latest
RUN apt update && apt install -y apache2
EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]

```