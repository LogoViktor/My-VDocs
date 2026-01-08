---
title: "Installation et configuration de base"
---

# **Installation et Configuration de Base d’un Serveur DHCP sous Debian**

---

## **1. Installation du Serveur DHCP**

### **Installer le paquet `isc-dhcp-server`**
Exécutez les commandes suivantes pour installer le serveur DHCP :

```bash
sudo apt update
sudo apt install isc-dhcp-server
```

---

## **2. Configuration de l’Adressage IP Statique**

- Configurez une adresse IP statique sur le serveur en utilisant un outil comme `nmtui` ou en modifiant manuellement les fichiers de configuration réseau.
- Redémarrez le service réseau pour appliquer les modifications :

```bash
sudo systemctl restart networking
```

---

## **3. Configuration du Serveur DHCP**

### **a. Modifier le fichier `/etc/dhcp/dhcpd.conf`**
Ajoutez ou modifiez les paramètres suivants en fonction de votre réseau :

```plaintext
default-lease-time <DURÉE_EN_SECONDES_PAR_DÉFAUT>;
max-lease-time <DURÉE_MAX_EN_SECONDES>;

option domain-name "<NOM_DE_DOMAINE>";
option domain-name-servers <ADRESSE_IP_DU_SERVEUR_DNS>;

subnet <ADRESSE_RÉSEAU> netmask <MASQUE_SOUS-RÉSEAU> {
    range <ADRESSE_IP_DE_DÉBUT> <ADRESSE_IP_DE_FIN>;
    option routers <ADRESSE_IP_PASSERELLE>;
    option broadcast-address <ADRESSE_IP_DIFFUSION>;
}
```

> **Exemple :**
> ```plaintext
> default-lease-time 3600;
> max-lease-time 7200;
> option domain-name "exemple.local";
> option domain-name-servers 192.168.1.1;
>
> subnet 192.168.1.0 netmask 255.255.255.0 {
>     range 192.168.1.100 192.168.1.200;
>     option routers 192.168.1.1;
>     option broadcast-address 192.168.1.255;
> }
> ```

### **b. Configurer l’interface réseau**
Modifiez le fichier `/etc/default/isc-dhcp-server` pour spécifier l’interface réseau :

```plaintext
INTERFACESv4="<NOM_DE_L'INTERFACE_RÉSEAU>"
```

> **Note :** Utilisez la commande `ip a` pour trouver le nom de votre interface réseau.

---

## **4. Redémarrer le Service DHCP**
Appliquez les modifications en redémarrant le service DHCP :

```bash
sudo systemctl restart isc-dhcp-server
```

---

## **5. Vérification sur un Client**

### **Sur un client Windows 10**
- Ouvrez l’invite de commandes (`Win+R` → `cmd`).
- Exécutez la commande suivante pour vérifier l’adresse IP attribuée :

```bash
ipconfig
```

Vérifiez que l’adresse IP attribuée est bien dans la plage configurée.

---

## **6. Attribution d’une Adresse IP Fixe**

### **a. Obtenir l’adresse MAC du client**
Pour obtenir l’adresse MAC de l’interface réseau du client, utilisez la commande suivante :

```bash
ip link show
```

### **b. Configurer le serveur DHCP**
Éditez le fichier `/etc/dhcp/dhcpd.conf` et ajoutez une déclaration de bail fixe :

```plaintext
host <NOM_DU_CLIENT> {
    hardware ethernet <ADRESSE_MAC_DU_CLIENT>;
    fixed-address <ADRESSE_IP_FIXE_SOUHAITÉE>;
}
```

> **Exemple :**
> ```plaintext
> host client-debian {
>     hardware ethernet AA:BB:CC:DD:EE:FF;
>     fixed-address 192.168.1.50;
> }
> ```

### **c. Redémarrer le service DHCP**
Appliquez les modifications en redémarrant le service DHCP :

```bash
sudo systemctl restart isc-dhcp-server
```

### **d. Renouveler le bail DHCP sur le client**
Sur le client, redémarrez le service réseau pour renouveler le bail DHCP :

```bash
sudo systemctl restart networking
```