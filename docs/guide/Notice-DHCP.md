# Notice : Configuration Serveur DHCP sous Debian


## Prérequis

- Une machine sous Debian en GUI (serveur)
- Une machine sous Windows 10 (client)
- Connexion directe via un câble réseau

## Installation du serveur DHCP
1. **Installer le serveur DHCP :**
   - Installer le paquet `isc-dhcp-server`:
     ```bash
     sudo apt update
     sudo apt install isc-dhcp-server
     ```

2. **Configurer l'adressage statique :**
   - Utiliser `nmtui` pour configurer une adresse IP statique.
   - Redémarrer le service réseau:
     ```bash
     sudo systemctl restart networking
     ```

3. **Configurer le serveur DHCP :**
   - Sauvegarder et modifier le fichier de configuration `/etc/dhcp/dhcpd.conf` pour inclure les paramètres réseau souhaités:
     ```plaintext
     default-lease-time 3600;   
     max-lease-time 7200;        
     option domain-name "151.rt.test";
     option domain-name-servers 192.168.Gx.253;

     subnet 192.168.Gx.0 netmask 255.255.255.0 {
         range 192.168.151.1 192.168.151.10;  # Plage d'adresses IP pour les clients
         option routers 192.168.151.254;    # Passerelle par défaut
         option broadcast-address 192.168.151.255;
     }
     ```
   - Configurer `/etc/default/isc-dhcp-server` pour spécifier l'interface réseau:
     ```plaintext
     INTERFACESv4="eno1" # Dans notre cas eno1
     ```
     En faisant un `ip a` trouver le nom votre interface.
     Remplacez `"eno1"` par le nom de votre interface réseau.  

4. **Redémarrer le service DHCP :**
   ```bash
   sudo systemctl restart isc-dhcp-server
   ```

5. **Démarrer votre machine sous Windows 10 :**
   - Faites `Win+R` puis `cmd` et taper la commande suivante :
      ```bash
      ipconfig
      ```
   Vérifier que vous avez bien pris une IP de votre pool d'adresse
   <br>

## Attribuer une adresse IP fixe

1. **Obtenir l'adresse MAC du client Debian :** 
   - Utilisez la commande suivante pour obtenir l'adresse MAC de l'interface réseau du client :
     ```bash
     ip link show
     ```


2. **Configurer le serveur DHCP :** 
   - Ouvrez le fichier de configuration du serveur DHCP :
     ```bash
     sudo nano /etc/dhcp/dhcpd.conf
     ```
   - Ajoutez une déclaration de bail fixe dans le fichier :
     ```plaintext
     host debian-client {
         hardware ethernet XX:XX:XX:XX:XX:XX;  # Remplacez par l'adresse MAC du client
         fixed-address 192.168.151.X;          # Adresse IP fixe à attribuer
     }
     ```
     

3. **Redémarrer le service DHCP :**
   - Redémarrez le service DHCP pour appliquer les modifications :
     ```bash
     sudo systemctl restart isc-dhcp-server
     ```
     

4. **Redémarrer ou renouveler le bail DHCP sur le client :**
   - Sur le client Debian, redémarrez le service réseau :
     ```bash
     sudo systemctl restart networking
     ```
<br>

## Configuration des VLANs sur le Serveur DHCP

1. **Configuration du Switch :**
    - Câblage de l'architecture : Assurez-vous que le serveur et les clients sont correctement connectés au switch.
    - Configuration des VLANs : Configurez le switch pour gérer les VLANs 10 et 20.

2. **Configuration des Sous-interfaces sur le Serveur :**
    - Ajout des sous-interfaces : Utilisez `nmtui` ou un autre outil pour ajouter les sous-interfaces pour les VLANs. Les sous-interfaces sont nommées `eno1.10` et `eno1.20`.
      ```bash
      sudo nmtui
      ```

3. **Configuration des adresses IP :**
    - Attribuez les adresses IP suivantes aux sous-interfaces :
      - `eno1.10` : `192.168.0.1/24`
      - `eno1.20` : `172.16.0.1/16`

4. **Modification du fichier de configuration DHCP :**

    - `/etc/dhcp/dhcpd.conf` : Ajoutez les configurations pour les nouveaux VLANs.
      ```plaintext
      subnet 192.168.0.0 netmask 255.255.255.0 {
          range 192.168.0.10 192.168.0.100;
          option routers 192.168.0.254;
          option domain-name-servers 192.168.0.253;
      }

      subnet 172.16.0.0 netmask 255.255.0.0 {
          range 172.16.0.10 172.16.0.100;
          option routers 172.16.0.254;
          option domain-name-servers 172.16.0.253;
      }
      ```
5. **Redémarrage du service DHCP :** 
    - Redémarrez le service DHCP pour appliquer les modifications.
      ```bash
      sudo systemctl restart isc-dhcp-server
      ```

6. **Test de la Configuration :**
    - Démarrage des clients : Démarrez les clients et vérifiez qu'ils obtiennent les adresses IP correctes via DHCP.


<script type="text/javascript"

src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

<script type="text/x-mathjax-config"> MathJax.Hub.Config({ tex2jax:

{inlineMath: [['$', '$']]}, messageStyle: "none" });</script>
