---
title: "WireGuard"
---
# ⚡ VPN WireGuard

Ce guide déploie un serveur **WireGuard** sous **Debian**, une solution VPN moderne et légère. Voir aussi : [IPSec](ipsec.md) · [OpenVPN](openvpn.md).

## 📊 Où se situe WireGuard parmi les solutions VPN

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
sudo apt install wireguard
```

## 2. Génération des clés

À exécuter sur le serveur et sur chaque client :

```
wg genkey | tee privatekey | wg pubkey > publickey
```

## 3. Configuration serveur

Créez `/etc/wireguard/wg0.conf` :

```
[Interface]
PrivateKey = <CLE_PRIVEE_SERVEUR>
Address = 10.10.0.1/24
ListenPort = 51820
PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
PublicKey = <CLE_PUBLIQUE_CLIENT>
AllowedIPs = 10.10.0.2/32
```

## 4. Configuration client

```
[Interface]
PrivateKey = <CLE_PRIVEE_CLIENT>
Address = 10.10.0.2/24
DNS = 9.9.9.9

[Peer]
PublicKey = <CLE_PUBLIQUE_SERVEUR>
Endpoint = <IP_PUBLIQUE_SERVEUR>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
```

## 5. Activation et vérification

```
sudo sysctl -w net.ipv4.ip_forward=1
sudo wg-quick up wg0
sudo systemctl enable wg-quick@wg0
sudo wg show
```

!!! note "Bonnes pratiques sécurité"
    Changez le port par défaut (51820) en production, limitez les `AllowedIPs` au strict nécessaire par peer, et prévoyez une rotation régulière des clés.

## 6. Diagnostic

| Commande | Utilité |
|---|---|
| `sudo wg show` | État des peers et du trafic |
| `journalctl -u wg-quick@wg0` | Logs du service |
| `sudo wg-quick down wg0 && sudo wg-quick up wg0` | Relance propre du tunnel |
