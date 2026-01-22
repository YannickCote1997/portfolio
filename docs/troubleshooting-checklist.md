# Troubleshooting Checklist – Infrastructure / Réseau / Services

## Objectif
Ce document sert de **checklist rapide** pour diagnostiquer efficacement une panne en environnement TI.
Il est conçu pour :
- réduire le temps de résolution (MTTR)
- éviter les oublis dans l’analyse
- standardiser les étapes de troubleshooting
- faciliter l’escalade et la documentation

> Approche générale : **du plus simple au plus complexe**, et toujours valider si le problème est **local**, **réseau**, **service**, ou **dépendance** (DNS/DB/SSL).

---

## Méthodologie (structure)
### 1) Identifier l’impact
- Qui est impacté ?
  - 1 utilisateur / 1 service / toute l’entreprise
- Depuis quand ?
- Est-ce intermittent ?
- Quel message d’erreur exact ?
- Quel changement récent ? (update, règle firewall, config, DNS…)

### 2) Classifier le type de panne
- **Poste utilisateur** (hardware / OS / profil / app)
- **Réseau** (DNS, DHCP, VLAN, route, internet)
- **Serveur/VM** (CPU/RAM, stockage, services down)
- **Applicatif** (WordPress, ERP, DB, téléphonie)
- **Sécurité** (WAF bloque, MFA, droits)

### 3) Isoler le problème
- Tester depuis :
  - un autre poste
  - un autre réseau (wifi/cellulaire)
  - directement depuis le serveur
- Comparer : local vs externe

### 4) Corriger + prévenir
- Correction temporaire (restore service)
- Correction permanente (cause root)
- Mesures préventives (monitoring, backup, change mgmt)

---

# A) Checklist – Panne utilisateur (poste)
### Symptômes fréquents
- application qui ne démarre pas
- lenteur
- pas accès à un partage
- login impossible

### Étapes rapides
- Redémarrage poste + app
- Vérifier :
  - réseau (wifi/cable)
  - VPN (si applicable)
  - heure/sync (important pour authentification)
- Vérifier erreurs exactes / code erreur
- Si accès ressources :
  - tester un autre compte
  - tester un autre poste

### Commandes utiles (Windows)
```powershell
ipconfig /all
ping 8.8.8.8
ping google.com
nslookup google.com
whoami
gpresult /r
```
B) Checklist – Panne DNS (LE classique)
Symptômes

internet semble up mais les sites ne chargent pas

"DNS_PROBE_FINISHED_NXDOMAIN"

ping IP fonctionne mais pas nom de domaine

Tests
1) Vérifier résolution
nslookup google.com
nslookup ton-domaine.com


ou

dig google.com

2) Tester en IP direct

ping 8.8.8.8 (si OK, c’est probablement DNS)

3) Vérifier serveur DNS configuré

DHCP donne le bon DNS ?

changement récent ?

Fix rapide

flush DNS client :

ipconfig /flushdns


redémarrer service DNS (si serveur interne)

fallback temporaire (Google DNS / Cloudflare DNS)

Points à vérifier

zone DNS (enregistrements)

propagation si changements récents

règles firewall bloquant port 53

split DNS (interne/externe)

C) Checklist – Panne DHCP
Symptômes

les postes reçoivent une IP APIPA (169.254.x.x)

pas de passerelle

pas d’accès réseau

Tests rapides

vérifier IP du poste :

ipconfig /all


release/renew :

ipconfig /release
ipconfig /renew

Vérifications serveur

DHCP scope plein ? (plus d’adresses)

DHCP service down ?

VLAN / DHCP relay (ip helper-address) ?

conflits IP ?

Fix

redémarrer service DHCP

augmenter scope / réduire lease time

corriger relay/VLAN

D) Checklist – Panne réseau / Internet
Symptômes

aucun accès internet

accès LAN OK mais pas WAN

lenteur généralisée

Étapes
1) Test basique

ping passerelle :

ping 192.168.0.1


ping DNS public :

ping 1.1.1.1

2) Traceroute
tracert google.com

3) Vérifier équipements

switch : ports up / erreurs

routeur/firewall : WAN up ?

logs firewall

4) Vérifier changements

mise à jour firewall ?

règle NAT ?

changement DNS ?

E) Checklist – VM / Serveur Down
Symptômes

service inaccessible

ping serveur ne répond pas

performance dégradée

Étapes
1) Réseau

ping VM

vérifier IP/config interface

vérifier route / gateway

2) Ressources

CPU/RAM saturées ?

stockage plein ?

IO disque ?

3) Service

Sur Linux :

systemctl status <service>
journalctl -u <service> --no-pager -n 50
df -h
free -m
top


Sur Windows :

Services.msc

Event Viewer

Task Manager (CPU/RAM/Disk)

4) Fix possible

restart service

restart VM (si nécessaire)

rollback snapshot (si changement récent)

F) Checklist – WordPress Down / Erreurs 500 / Site lent
Symptômes

502 / 504 gateway

500 Internal Server Error

site très lent

login WP-admin impossible

Étapes
1) Vérifier reverse proxy / tunnel

NPM up ?

cloudflared up ?

systemctl status cloudflared

2) Vérifier serveur web

Nginx :

systemctl status nginx
journalctl -u nginx --no-pager -n 50

3) Vérifier PHP-FPM
systemctl status php8.3-fpm
journalctl -u php8.3-fpm --no-pager -n 50

4) Vérifier DB
systemctl status mariadb

5) Vérifier logs WordPress

wp-config.php debug (si nécessaire)

logs nginx/apache

disk full :

df -h

Fix rapides

restart services

désactiver plugin suspect

rollback snapshot backup si update a cassé

G) Checklist – SSL / Certificat expiré
Symptômes

navigateur : "Not Secure"

erreurs SSL handshake

cert expiré

Étapes

vérifier cert dans NPM / Cloudflare

vérifier expiration

vérifier DNS correct

vérifier redirect (http→https)

Fix

renew cert Let’s Encrypt

vérifier challenge (DNS/HTTP)

forcer HTTPS

H) Checklist – Cloudflare / Tunnel down
Symptômes

erreur 521 / 522

tunnel disconnected

site unreachable de l’extérieur mais OK localement

Étapes
1) Vérifier tunnel

Sur le serveur :

systemctl status cloudflared
journalctl -u cloudflared --no-pager -n 80

2) Vérifier config ingress

hostname correct ?

service local correct ? (port 80/443)

tester local :

curl -I http://localhost

3) Cloudflare dashboard

tunnel health

DNS record proxied ?

I) Checklist – Accès partage réseau / permissions
Symptômes

Access Denied

accès lecture OK mais écriture non

Étapes

vérifier groupe AD

vérifier NTFS permissions

vérifier share permissions

vérifier mapping via GPO

tester avec un autre user

J) Checklist – Téléphonie / VoIP (si applicable)
Symptômes

poste IP ne s’enregistre pas

pas de tonalité / appels coupés

voix hachée

Étapes

vérifier PoE / switchport

vérifier VLAN voice

vérifier DHCP options (66/150 selon système)

vérifier QoS

vérifier DNS/SIP server

Communication + Documentation (important)
Communication

informer les usagers dès le début : “incident en cours d’analyse”

donner un ETA seulement si réaliste

donner update régulier si impact majeur

Documentation post-mortem

symptôme

cause

solution

prévention

temps total / impact

Commandes rapides utiles (résumé)
Linux
ip a
ip r
ping -c 4 1.1.1.1
dig google.com
systemctl status <service>
journalctl -u <service> --no-pager -n 50
df -h
free -m

Windows
ipconfig /all
ipconfig /flushdns
nslookup google.com
tracert google.com
gpresult /r


---
