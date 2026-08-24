# 📊 Supervision avec Zabbix (Agent)

Zabbix est une solution de supervision Open Source très puissante pour surveiller la disponibilité et les performances de vos infrastructures réseaux, serveurs et applications.

## 📋 1. Installation de l'Agent Zabbix sous Debian
Pour commencer, téléchargez et installez le dépôt officiel de Zabbix, puis installez l'agent :

```bash
wget [https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_6.0-4+debian11_all.deb](https://repo.zabbix.com/zabbix/6.0/debian/pool/main/z/zabbix-release/zabbix-release_6.0-4+debian11_all.deb)
sudo dpkg -i zabbix-release_6.0-4+debian11_all.deb
sudo apt update
sudo apt install zabbix-agent

```

## ⚙️ 2. Configuration de l'Agent

Il faut indiquer à l'agent sur quelle machine il doit envoyer ses métriques (votre serveur Zabbix central). Ouvrez le fichier de configuration :

```bash
sudo nano /etc/zabbix/zabbix_agentd.conf

```

Modifiez les paramètres clés suivants avec vos propres adresses :

```ini
Server=192.168.1.100       # Adresse IP de votre serveur Zabbix principal
ServerActive=192.168.1.100 # Pour les contrôles actifs
Hostname=SRV-WEB-01        # Nom unique de cette machine cible (doit correspondre à la configuration sur le serveur)

```

## 🔄 3. Démarrage etactivation du service

Appliquez les modifications en redémarrant le service et en l'activant au démarrage de la machine :

```bash
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent

```

!!! warning "Points de vigilance"
* **Pare-feu :** Assurez-vous que le port **10050/TCP** est ouvert sur la machine cliente pour que le serveur Zabbix puisse l'interroger.
* **Vérification :** Vous pouvez tester le bon fonctionnement depuis le serveur Zabbix avec la commande `zabbix_get -s <IP_CLIENT> -k agent.ping`.

```