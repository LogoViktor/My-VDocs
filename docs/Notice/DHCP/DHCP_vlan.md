---
title: "Configuration VLAN"
---
# **Configuration des VLANs sur le Serveur DHCP**

---

## **1. Configuration du Switch**
- Assurez-vous que le serveur et les clients sont correctement connectés au **switch**.
- Configurez les VLANs `<NUMÉRO_VLAN_1>` et `<NUMÉRO_VLAN_2>` sur le switch.

---

## **2. Configuration des Sous-Interfaces**
- Ajoutez les sous-interfaces pour les VLANs (`<NOM_INTERFACE>.<NUMÉRO_VLAN_1>` et `<NOM_INTERFACE>.<NUMÉRO_VLAN_2>`) en utilisant `nmtui` :

```bash
sudo nmtui
```

---

## **3. Attribution des Adresses IP**
- Attribuez les adresses IP suivantes aux sous-interfaces :
  - `<NOM_INTERFACE>.<NUMÉRO_VLAN_1>` : `<ADRESSE_IP_VLAN_1>/<MASQUE_CIDR_VLAN_1>`
  - `<NOM_INTERFACE>.<NUMÉRO_VLAN_2>` : `<ADRESSE_IP_VLAN_2>/<MASQUE_CIDR_VLAN_2>`

---

## **4. Mise à Jour de `/etc/dhcp/dhcpd.conf`**
Ajoutez les configurations pour les nouveaux VLANs :

```plaintext
subnet <ADRESSE_RÉSEAU_VLAN_1> netmask <MASQUE_SOUS-RÉSEAU_VLAN_1> {
    range <ADRESSE_IP_DÉBUT_VLAN_1> <ADRESSE_IP_FIN_VLAN_1>;
    option routers <ADRESSE_IP_PASSERELLE_VLAN_1>;
    option domain-name-servers <ADRESSE_IP_DNS_VLAN_1>;
}

subnet <ADRESSE_RÉSEAU_VLAN_2> netmask <MASQUE_SOUS-RÉSEAU_VLAN_2> {
    range <ADRESSE_IP_DÉBUT_VLAN_2> <ADRESSE_IP_FIN_VLAN_2>;
    option routers <ADRESSE_IP_PASSERELLE_VLAN_2>;
    option domain-name-servers <ADRESSE_IP_DNS_VLAN_2>;
}
```

> **Exemple :**
> ```plaintext
> subnet 192.168.0.0 netmask 255.255.255.0 {
>     range 192.168.0.10 192.168.0.100;
>     option routers 192.168.0.254;
>     option domain-name-servers 192.168.0.253;
> }
>
> subnet 172.16.0.0 netmask 255.255.0.0 {
>     range 172.16.0.10 172.16.0.100;
>     option routers 172.16.0.254;
>     option domain-name-servers 172.16.0.253;
> }
> ```

---

## **5. Redémarrer le Service DHCP**
Pour appliquer les modifications, redémarrez le service DHCP :

```bash
sudo systemctl restart isc-dhcp-server
```

---

## **6. Tester la Configuration**
- Démarrez les clients et vérifiez qu'ils obtiennent les adresses IP correctes via DHCP.
