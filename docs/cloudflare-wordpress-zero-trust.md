# WordPress sécurisé via Cloudflare Zero Trust (Tunnel)

## Résumé
Ce projet consiste à protéger un site WordPress (WooCommerce) en supprimant complètement l’exposition directe du serveur Internet.  
Le site est publié via **Cloudflare Zero Trust Tunnel**, ce qui permet :
- **0 ports ouverts** chez moi / sur la VM
- IP réelle cachée (anti-DDoS / anti-scan)
- couche WAF et règles Cloudflare

---

## Objectifs
- Éliminer le port forwarding (80/443) vers mon serveur
- Protéger l’origine (serveur WordPress) avec Cloudflare
- Améliorer la performance (cache) sans briser WooCommerce
- Garder un fonctionnement stable et maintenable

---

## Architecture
### Avant (classique)
Internet → IP publique → NAT/Firewall → Reverse Proxy → WordPress

### Après (Zero Trust)
Internet → **Cloudflare** → **Tunnel** → VM WordPress (HTTP local)

✅ Avantages :
- Aucune exposition du réseau résidentiel
- Cloudflare absorbe la majorité des attaques
- Contrôle total des règles (WAF, cache, bot protection)

---

## Étapes réalisées (haut niveau)
1. Configuration du tunnel Cloudflared
2. Publication du hostname (ex: `lediagnosticieninformatique.ca`)
3. Configuration DNS Cloudflare
4. Ajustement de WordPress pour fonctionner en mode proxy/tunnel
5. Mise en place règles cache WooCommerce (Cache Everything + Bypass)

---

## Règles Cache (important pour WooCommerce)
### Cache Everything (public)
- Pages publiques : accueil, pages de services, articles, etc.

### Bypass cache (obligatoire)
Pour éviter des problèmes (panier/compte/commande) :
- `/cart*`
- `/checkout*`
- `/my-account*`
- `/wp-admin*`
- `*preview=true*`
- `*add-to-cart=*`

✅ Objectif : ne jamais cacher de pages dynamiques (session user).

---

## Sécurité appliquée
### Protections Cloudflare
- Proxy Cloudflare (masque l’IP)
- WAF (Web Application Firewall)
- Règles anti-bot / challenge si besoin
- HTTPS forcé

### Bonnes pratiques WordPress
- mise à jour WP/plugins
- comptes admin limités
- mots de passe forts
- suppression / protection endpoints inutiles

---

## Validation / Tests
Outils / méthodes :
- Vérification headers Cloudflare (`cf-cache-status`, `age`, cache-control)
- Test navigation WooCommerce (add-to-cart, checkout)
- Test login WP-admin
- Performance test (Lighthouse / PageSpeed)

✅ Résultat : sécurité + performance sans briser la boutique.

---

## Dépannage / Troubleshooting
### Symptôme : cache qui brise le checkout
➡️ vérifier les bypass rules

### Symptôme : IP serveur visible
➡️ DNS doit être en mode Cloudflare Proxy (orange cloud)

### Symptôme : mauvais IP dans logs
➡️ config Real IP / Cloudflare headers (selon stack web)

---

## Résultats
- Publication sécurisée sans port ouvert
- Réduction majeure surface d’attaque
- Performance améliorée par cache Cloudflare
- Conception “professionnelle” orientée infra & sécurité

---

## Améliorations futures
- règles WAF plus strictes sur `/wp-login.php`
- rate limit sur endpoints sensibles
- monitoring disponibilité et alerting
