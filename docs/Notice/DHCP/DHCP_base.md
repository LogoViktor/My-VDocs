# Configuration Serveur DHCP sous Debian

## Installation du serveur DHCP

### 1. Installer le serveur DHCP
Pour installer le paquet `isc-dhcp-server`, exécutez les commandes suivantes :

```bash
sudo apt update
sudo apt install isc-dhcp-server
```

### 2. Configurer l'adressage statique
- Utilisez `nmtui` pour configurer une adresse IP statique sur le serveur.
- Redémarrez le service réseau pour appliquer les modifications :

```bash
sudo systemctl restart networking
```

### 3. Configurer le serveur DHCP
#### a. Modifier le fichier `/etc/dhcp/dhcpd.conf`
Ajoutez ou modifiez les paramètres suivants :

```plaintext
default-lease-time 3600;
max-lease-time 7200;
option domain-name "151.rt.test";
option domain-name-servers 192.168.Gx.253;

subnet 192.168.Gx.0 netmask 255.255.255.0 {
    range 192.168.151.1 192.168.151.10;
    option routers 192.168.151.254;
    option broadcast-address 192.168.151.255;
}
```

#### b. Configurer l'interface réseau
Modifiez le fichier `/etc/default/isc-dhcp-server` pour spécifier l'interface réseau :

```plaintext
INTERFACESv4="eno1"
```

> **Note** : Remplacez `"eno1"` par le nom de votre interface réseau (vous pouvez le trouver avec la commande `ip a`).

### 4. Redémarrer le service DHCP
Pour appliquer les modifications, redémarrez le service DHCP :

```bash
sudo systemctl restart isc-dhcp-server
```

### 5. Vérifier la configuration sur le client Windows 10
- Ouvrez l'invite de commandes (`Win+R` → `cmd`).
- Exécutez la commande suivante pour vérifier l'adresse IP attribuée :

```bash
ipconfig
```

Vérifiez que l'adresse IP attribuée est bien dans le pool configuré.

---
## Attribuer une adresse IP fixe

### 1. Obtenir l'adresse MAC du client Debian
Pour obtenir l'adresse MAC de l'interface réseau du client Debian, utilisez la commande suivante :

```bash
ip link show
```

### 2. Configurer le serveur DHCP
- Éditez le fichier `/etc/dhcp/dhcpd.conf` et ajoutez une déclaration de bail fixe :

```plaintext
host debian-client {
    hardware ethernet XX:XX:XX:XX:XX:XX;  # Remplacez par l'adresse MAC du client
    fixed-address 192.168.151.X;          # Remplacez par l'adresse IP fixe souhaitée
}
```

### 3. Redémarrer le service DHCP
Pour appliquer les modifications, redémarrez le service DHCP :

```bash
sudo systemctl restart isc-dhcp-server
```

### 4. Renouveler le bail DHCP sur le client
Sur le client Debian, redémarrez le service réseau pour renouveler le bail DHCP :

```bash
sudo systemctl restart networking
```