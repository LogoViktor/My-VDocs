## Configuration des VLANs sur le Serveur DHCP

### 1. Configuration du Switch
- Assurez-vous que le serveur et les clients sont correctement connectés au switch.
- Configurez les VLANs 10 et 20 sur le switch.

### 2. Configuration des sous-interfaces
- Ajoutez les sous-interfaces pour les VLANs (`eno1.10` et `eno1.20`) en utilisant `nmtui` :

```bash
sudo nmtui
```

### 3. Attribuer les adresses IP
- Attribuez les adresses IP suivantes aux sous-interfaces :
  - `eno1.10` : `192.168.0.1/24`
  - `eno1.20` : `172.16.0.1/16`

### 4. Mettre à jour `/etc/dhcp/dhcpd.conf`
Ajoutez les configurations pour les nouveaux VLANs :

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

### 5. Redémarrer le service DHCP
Pour appliquer les modifications, redémarrez le service DHCP :

```bash
sudo systemctl restart isc-dhcp-server
```

### 6. Tester la configuration
- Démarrez les clients et vérifiez qu'ils obtiennent les adresses IP correctes via DHCP.