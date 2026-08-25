---
title: "OpenVPN"
---
# 🌐 VPN OpenVPN

Ce guide déploie un serveur **OpenVPN** pour de l'accès distant sous **Debian**. Voir aussi : [IPSec](ipsec.md) · [WireGuard](wireguard.md).

## 📊 Où se situe OpenVPN parmi les solutions VPN

| Critère | IPSec (strongSwan) | OpenVPN | WireGuard |
|---|---|---|---|
| Port / protocole | UDP 500 & 4500 | UDP/TCP 1194 (configurable) | UDP 51820 |
| Performance | Très bonne (traitement noyau) | Moyenne (espace utilisateur) | Excellente (code minimal, noyau) |
| Complexité de config | Élevée | Moyenne (PKI à gérer) | Faible |
| Chiffrement | IKEv2, suites AES/SHA | TLS/SSL, certificats X.509 | Curve25519, ChaCha20, Poly1305 |
| Cas d'usage type | Tunnel site-à-site entreprise | Accès distant classique, très compatible | Accès distant moderne, mobile, léger |

---

## 1. Installation et PKI (easy-rsa)

```
sudo apt update
sudo apt install openvpn easy-rsa

make-cadir ~/easy-rsa
cd ~/easy-rsa
./easyrsa init-pki
./easyrsa build-ca nopass
./easyrsa gen-req server nopass
./easyrsa sign-req server server
./easyrsa gen-dh
openvpn --genkey secret ta.key
```

## 2. Configuration serveur

Créez `/etc/openvpn/server.conf` :

```
port 1194
proto udp
dev tun
ca ca.crt
cert server.crt
key server.key
dh dh.pem
tls-auth ta.key 0
topology subnet
server 10.8.0.0 255.255.255.0
push "redirect-gateway def1 bypass-dhcp"
push "dhcp-option DNS 9.9.9.9"
keepalive 10 120
cipher AES-256-GCM
persist-key
persist-tun
status openvpn-status.log
verb 3
```

## 3. Configuration client (`client.ovpn`)

```
client
dev tun
proto udp
remote <IP_PUBLIQUE_SERVEUR> 1194
resolv-retry infinite
nobind
persist-key
persist-tun
remote-cert-tls server
cipher AES-256-GCM
verb 3
<ca>
... contenu de ca.crt ...
</ca>
<cert>
... contenu du certificat client ...
</cert>
<key>
... contenu de la clé privée client ...
</key>
<tls-auth>
... contenu de ta.key ...
</tls-auth>
```

## 4. Démarrage et vérification

```
sudo systemctl start openvpn@server
sudo systemctl enable openvpn@server
sudo systemctl status openvpn@server
ip a show tun0
```

!!! note "Bonne pratique"
    Générez un certificat client distinct par utilisateur/machine (jamais de certificat partagé) pour pouvoir révoquer un accès individuellement avec `easyrsa revoke`.

## 5. Diagnostic

| Commande | Utilité |
|---|---|
| `sudo systemctl status openvpn@server` | État du service |
| `journalctl -u openvpn@server` | Logs du service |
| `cat /etc/openvpn/openvpn-status.log` | Clients actuellement connectés |
