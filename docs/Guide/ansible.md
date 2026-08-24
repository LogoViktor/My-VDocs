```markdown
# 🤖 Guide d'Automatisation : Ansible

Ansible est un outil open-source de gestion de configuration et d'automatisation. Il permet de configurer des serveurs et des équipements de manière centralisée et sans agent à installer sur les machines cibles.

## 📋 1. Installation du Maître Ansible
Sur votre machine d'administration (généralement sous Linux Debian/Ubuntu), installez le paquet Ansible :

```bash
sudo apt update
sudo apt install ansible

```

## ⚙️ 2. Configuration de l'Inventaire (`hosts`)

L'inventaire est le fichier qui liste toutes les machines que vous souhaitez gérer. Créez un fichier nommé `hosts` :

```ini
[serveurs_web]
192.168.1.10
192.168.1.11

[serveurs_bdd]
192.168.1.20

```

## 🚀 3. Commandes Utiles (Ad-Hoc)

Voici quelques commandes rapides pour interagir directement avec vos parcs de machines :

* **Tester la connectivité (Module Ping) :**
```bash
ansible all -i hosts -m ping -u root

```


* **Mettre à jour tous les paquets des serveurs web :**
```bash
ansible serveurs_web -i hosts -m apt -a "update_cache=yes upgrade=yes" -u root --become

```


* **Vérifier l'espace disque disponible sur un groupe :**
```bash
ansible serveurs_bdd -i hosts -m shell -a "df -h" -u root

```



!!! info "Le saviez-vous ?"
L'option `--become` (ou `-b`) permet à Ansible de passer en mode administrateur (`sudo`) sur la machine distante pour exécuter des tâches qui nécessitent des privilèges élevés.

```