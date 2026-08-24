---
title: "Bind9"
---
# 🌐 Serveur DNS (Bind9) sous Debian

Le service DNS (*Domain Name System*) est indispensable : il permet de traduire les noms de domaine compréhensibles par l'humain (ex: `www.mondomaine.local`) en adresses IP compréhensibles par les machines (ex: `192.168.1.20`). 

Ce guide détaille l'installation, la structure théorique et la configuration d'un serveur DNS maître avec **Bind9** sous Debian.

---

## 📋 1. Installation de Bind9

Mettez à jour vos paquets et installez Bind9 ainsi que les utilitaires indispensables pour tester et administrer le service :

```bash
sudo apt update
sudo apt install bind9 bind9utils bind9-doc dnsutils

```

* **`bind9`** : Le daemon (service) principal du serveur DNS.
* **`dnsutils`** : Contient les outils de diagnostic essentiels comme `nslookup` ou `dig`.

Vérifiez que le service est bien en cours d'exécution :

```bash
sudo systemctl status bind9

```

---

## ⚙️ 2. Déclaration de la Zone Locale

Sur Bind9, la configuration principale se fait dans le fichier `named.conf.local`. C'est ici qu'on déclare les zones dont notre serveur a la charge (zones autoritatives).

Ouvrez le fichier de configuration :

```bash
sudo nano /etc/bind/named.conf.local

```

Ajoutez la déclaration de votre zone maître (remplacez `mondomaine.local` par le nom de votre choix) :

```text
zone "mondomaine.local" {
    type master;
    file "/etc/bind/db.mondomaine.local";
};

```

---

## 📝 3. Création et Structure du Fichier de Zone

Le fichier de zone contient tous les enregistrements DNS (les correspondances entre noms et IP).

Dupliquez le fichier de modèle vide fourni par défaut, puis éditez-le :

```bash
sudo cp /etc/bind/db.empty /etc/bind/db.mondomaine.local
sudo nano /etc/bind/db.mondomaine.local

```

Remplacez le contenu par la structure suivante :

```text
; =========================================================================
; Fichier de zone pour mondomaine.local
; =========================================================================
$TTL    86400
@       IN      SOA     ns1.mondomaine.local. admin.mondomaine.local. (
                              3         ; Serial (Incrémenter à chaque modif !)
                         604800         ; Refresh (Délai avant rafraîchissement)
                          86400         ; Retry (Délai avant nouvelle tentative si échec)
                        2419200         ; Expire (Délai d'expiration de la zone)
                          86400 )       ; Negative Cache TTL (Durée de mise en cache d'une erreur)
;
; --- Définition des serveurs de noms (NS) et de l'IP du serveur DNS ---
@       IN      NS      ns1.mondomaine.local.
ns1     IN      A       192.168.1.10    ; Adresse IP de votre serveur DNS

; --- Enregistrements de vos services (A = IPv4) ---
www     IN      A       192.168.1.20    ; Correspond à www.mondomaine.local
app     IN      A       192.168.1.30    ; Correspond à app.mondomaine.local

```

### 💡 Rappel théorique sur les enregistrements :

* **SOA (*Start of Authority*)** : Définit les paramètres généraux de la zone et le serveur de référence principal (`ns1`).
* **NS (*Name Server*)** : Indique quel serveur est autorisé à répondre pour ce domaine.
* **A (*Address*)** : Associe un nom d'hôte à une adresse IP IPv4.

---

## 🔄 4. Validation et Redémarrage du Service

Avant de redémarrer le service, il est **impératif** de valider la syntaxe pour éviter de casser la résolution DNS :

* **Vérifier la configuration globale :**
```bash
named-checkconf

```


* **Vérifier la syntaxe du fichier de zone :**
```bash
named-checkzone mondomaine.local /etc/bind/db.mondomaine.local

```



Si la sortie renvoie `OK`, vous pouvez redémarrer Bind9 en toute sécurité :

```bash
sudo systemctl restart bind9

```

---

## 🧪 5. Tests et Dépannage

Testez le bon fonctionnement de votre serveur depuis une machine cliente (ou en local) en interrogeant directement votre IP de serveur DNS :

```bash
nslookup www.mondomaine.local 192.168.1.10

```

!!! warning "Points de vigilance en cas d'échec"
*   **Le Serial :** Pensez à incrémenter le numéro de série (`Serial`) dans le fichier de zone à chaque fois que vous y apportez une modification, sinon les clients ne prendront pas en compte les changements.
*   **Pare-feu :** Assurez-vous que le port **53 (TCP et UDP)** est bien ouvert sur le pare-feu de votre serveur (`ufw` ou `iptables`).

```