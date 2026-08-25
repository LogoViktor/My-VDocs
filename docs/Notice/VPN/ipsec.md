---
title: "IPSec"
---
# 🔐 VPN IPSec avec strongSwan

Ce guide déploie un tunnel VPN **IPSec** entre deux sites sous **Debian**, à l'aide de strongSwan. Voir aussi : [OpenVPN](openvpn.md) · [WireGuard](wireguard.md).

## 📊 Où se situe IPSec parmi les solutions VPN

| Critère | IPSec (strongSwan) | OpenVPN | WireGuard |
|---|---|---|---|
| Port / protocole | UDP 500 & 4500 | UDP/TCP 1194 (configurable) | UDP 51820 |
| Performance | Très bonne (traitement noyau) | Moyenne (espace utilisateur) | Excellente (code minimal, noyau) |
| Complexité de config | Élevée | Moyenne (PKI à gérer) | Faible |
| Chiffrement | IKEv2, suites AES/SHA | TLS/SSL, certificats X.509 | Curve25519, ChaCha20, Poly1305 |
| Cas d'usage type | Tunnel site-à-site entreprise | Accès distant classique, très compatible | Accès distant moderne, mobile, léger |

---

## 1. Installation

```
sudo apt update
sudo apt install strongswan strongswan-pki libcharon-extra-plugins
```

## 2. Configuration du tunnel (site à site)

Modifiez `/etc/ipsec.conf` :

```
config setup
    charondebug="ike 2, knl 2, cfg 2"

conn site-to-site
    authby=secret
    left=<IP_LOCALE>
    leftsubnet=<RESEAU_LOCAL>/24
    right=<IP_DISTANTE>
    rightsubnet=<RESEAU_DISTANT>/24
    ike=aes256-sha256-modp2048!
    esp=aes256-sha256!
    keyingtries=0
    ikelifetime=1h
    lifetime=8h
    dpddelay=30
    dpdtimeout=120
    dpdaction=restart
    auto=start
```

> **Exemple :**
>
> ```
> conn paris-lyon
>     authby=secret
>     left=203.0.113.10
>     leftsubnet=192.168.1.0/24
>     right=203.0.113.20
>     rightsubnet=192.168.2.0/24
>     ike=aes256-sha256-modp2048!
>     esp=aes256-sha256!
>     auto=start
> ```

Déclarez la clé pré-partagée dans `/etc/ipsec.secrets` :

```
<IP_LOCALE> <IP_DISTANTE> : PSK "CLE_PARTAGEE_SECRETE"
```

## 3. Activation et vérification

```
sudo systemctl restart strongswan-starter
sudo ipsec status
sudo ipsec statusall
```

!!! tip "Astuce"
    Si le tunnel ne monte pas, vérifiez en premier lieu que le pare-feu laisse passer les ports UDP 500 et 4500 (et l'ESP, protocole IP 50), et que `net.ipv4.ip_forward=1` est actif sur les deux passerelles.

## 4. Diagnostic

| Commande | Utilité |
|---|---|
| `sudo ipsec statusall` | État détaillé des tunnels |
| `journalctl -u strongswan-starter` | Logs du service |
| `sudo ipsec restart` | Relance propre du service |
