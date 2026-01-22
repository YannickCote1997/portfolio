# Homelab – Infrastructure Proxmox VE (Virtualisation)

## Résumé
Ce projet représente mon environnement **Homelab** basé sur **Proxmox VE**, conçu comme un laboratoire d’apprentissage et de mise en pratique en administration systèmes/réseaux. L’objectif est d’héberger plusieurs services (web, proxy, stockage, services Linux) de manière **stable, sécurisée et maintenable**, similaire à une infrastructure d’entreprise.

---

## Objectifs
- Mettre en place une plateforme de virtualisation robuste et évolutive.
- Héberger plusieurs services dans des VMs isolées.
- Structurer une base solide pour : **infra, sécurité, réseau, backups, monitoring**.
- Tester des scénarios réels : panne, restauration, migration, mise à jour, hardening.

---

## Environnement / Matériel (exemple)
- **Hyperviseur** : Proxmox VE
- **CPU** : Ryzen 9 5950X
- **RAM** : 128 GB
- **Stockage** : HDD NAS (WD Red Plus 4TB) + NVMe (cache)
- **Réseau** : 1Gb/2.5Gb selon les segments

> *(Si tu veux, on peut ajuster cette section exactement selon ton setup final.)*

---

## Architecture (Vue d’ensemble)
### Services hébergés (exemples)
- **VM WordPress** (LDI)
- **Nginx Proxy Manager** (reverse proxy)
- **Cloudflared / tunnels** (Zero Trust)
- **TrueNAS VM** (NAS / stockage)
- **VM Ubuntu/Debian** (services divers / game servers / tests)
- *(optionnel)* monitoring (Uptime Kuma, Grafana/Prometheus)

### Pourquoi des VMs séparées ?
- Sécurité (isolation)
- Stabilité (un service ne casse pas les autres)
- Maintenance (snapshot/backup par VM)
- Scalabilité (ajout rapide de nouveaux services)

---

## Sauvegardes et restauration
### Stratégie
- Snapshots avant modifications majeures
- Backups réguliers (VMs critiques)
- Conservation de versions pour rollback

### Tests
- Tests de restauration de VM
- Vérification cohérence services post-restore

✅ Résultat : une infrastructure pouvant être restaurée rapidement, comme un environnement professionnel.

---

## Sécurité (Hardening)
Mes principes appliqués :
- Aucun accès inutile exposé publiquement
- Séparation des services (VM dédiée)
- Mots de passe forts + gestion accès
- Mises à jour OS régulières

**Objectif sécurité :** Réduire la surface d’attaque et éviter les services “open internet” inutiles.

---

## Dépannage / Troubleshooting (exemples)
Checklist rapide utilisée :
- Vérifier connectivité VM (ping, gateway)
- Vérifier DNS (résolution domaine)
- Vérifier services (systemctl status)
- Vérifier stockage (espace disque, erreurs)
- Vérifier logs (journalctl, logs applicatifs)

---

## Résultats / Valeur ajoutée
- Homelab stable 24/7 pour tests et apprentissage
- Plateforme pratique pour les projets cloud/sécurité
- Démonstration concrète de compétences SysAdmin :
  - virtualisation
  - gestion services Linux
  - réseau et segmentation
  - backup/restore
  - documentation et procédures

---

## Améliorations futures
- Ajout monitoring complet (Grafana/Prometheus)
- Centralisation logs
- Automatisation via scripts (déploiements, backups)
- VLAN dédiés (services / management / invités)

