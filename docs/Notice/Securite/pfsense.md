# 🛡️ Guide de Base : Pare-feu pfSense

pfSense est une solution open-source de pare-feu et de routage basée sur FreeBSD, très largement utilisée en entreprise et dans les environnements réseaux/systèmes.

## 📋 1. Architecture des Interfaces
Une installation standard de pfSense nécessite au minimum deux interfaces réseau virtuelles ou physiques :
*   **WAN (Wide Area Network) :** Interface tournée vers l'extérieur (Internet ou le réseau de l'école). Elle récupère généralement une IP en DHCP.
*   **LAN (Local Area Network) :** Interface tournée vers votre réseau interne protégé. Elle nécessite une configuration IP statique.

## ⚙️ 2. Configuration Initiale (Console)
Lors du premier démarrage de la machine virtuelle, utilisez le menu textuel de la console pour initialiser le réseau :
1. **Assign Interfaces (Option 1) :** Associez vos cartes (ex: `em0` pour le WAN, `em1` pour le LAN).
2. **Set interface(s) IP address (Option 2) :** Configurez l'adresse IP statique du réseau LAN (par exemple `192.168.10.254` avec un masque en `24`).
3. Activez le serveur DHCP intégré sur le LAN si vous le souhaitez pour vos machines clientes.

## 🌐 3. Accès à l'Interface d'Administration Web
Une fois la configuration réseau de base terminée depuis la console :
* Connectez un client sur le réseau LAN.
* Ouvrez un navigateur web et accédez à l'adresse IP du LAN : `https://192.168.10.254`
* **Identifiants par défaut :** `admin` / `pfsense`

!!! warning "Règles de filtrage par défaut"
    * **WAN :** Tout le trafic entrant est bloqué par défaut (politique de sécurité stricte).
    * **LAN :** Tout le trafic sortant vers le WAN est autorisé par défaut pour les clients du réseau interne.