# Reverse Proxy – Nginx Proxy Manager (NPM)

## Résumé
Ce projet implémente un reverse proxy centralisé basé sur **Nginx Proxy Manager**, utilisé pour publier plusieurs services internes via des domaines/hosts, avec gestion simplifiée du SSL.

Objectif : architecture propre, maintenable, et sécurisée (équivalent environnement PME).

---

## Objectifs
- Centraliser l’accès à plusieurs services (WordPress, panels, apps)
- Simplifier la gestion DNS/hosts
- Générer des certificats SSL facilement (Let’s Encrypt)
- Isoler les services derrière un seul point d’entrée

---

## Architecture
### Principe
Client → Reverse Proxy (NPM) → Service interne

Exemples :
- `site.ca` → VM WordPress
- `panel.site.ca` → panel admin
- `status.site.ca` → monitoring

✅ Avantages :
- un seul point d’entrée
- SSL centralisé
- règles de redirection + headers de sécurité uniformes

---

## Configuration (haut niveau)
### 1) Déploiement NPM
- Installation via Docker (recommandé)
- Accès UI admin via port interne
- Stockage persistant (volumes)

### 2) Ajout d’un Proxy Host
- Domaine : `lediagnosticieninformatique.ca`
- Destination : IP interne (ex: `192.168.x.x:80`)
- SSL : Let’s Encrypt (si applicable selon architecture)

### 3) SSL / Certificats
- Génération Let’s Encrypt via NPM
- HTTPS forcé
- Renouvellement automatique

---

## Sécurité
Mes bonnes pratiques :
- Interface admin non exposée publiquement
- comptes admin uniques + mot de passe fort
- accès réseau restreint (LAN seulement si possible)
- headers de sécurité (selon besoin)

---

## Troubleshooting
### Problème : erreur 502 Bad Gateway
Checklist :
- VM/service destination up ?
- bon port ?
- firewall interne ?
- DNS correct ?
- logs NPM / nginx

### Problème : certificat SSL échoue
- vérifier DNS
- vérifier port 80/443 (si Let’s Encrypt standard)
- utiliser DNS challenge si nécessaire

---

## Résultats
- Gestion simple de multiples services
- SSL automatique et centralisé
- Architecture propre et scalabilité (ajouter un site = 2 minutes)
- Base solide pour héberger plusieurs clients/services

---

## Améliorations futures
- ajout d’authentification (basic auth) sur certains hosts
- intégration monitoring
- segmentation via VLAN/services
