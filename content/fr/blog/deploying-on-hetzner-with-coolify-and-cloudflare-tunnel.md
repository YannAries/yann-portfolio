+++
title = "Déployer une application sur VPS avec Coolify et Cloudflare Tunnel"
date = "2025-01-08"
description = "Auto-hébergement avec Coolify sur Hetzner et Cloudflare Tunnel"
tags = ["cloud", "docker", "ssl", "cloudflare", "hetzner", "coolify"]
categories = ["infrastructure", "tutoriels"]
featured = true
+++

Mettre en place mon VPS sur Hetzner Cloud pour déployer mon appli web et son API m'a pris du temps, surtout avec les certificats SSL et le reverse proxy.

## La stack

- **VPS** : Hetzner Cloud (2 vCPU, 4GB RAM, 4,90€/mois)
- **Plateforme** : Coolify (PaaS auto-hébergé)
- **Reverse proxy** : Traefik (via Coolify)
- **SSL/CDN** : Cloudflare Tunnel
- **Apps** : Backend FastAPI + frontend Dash/Plotly

## Configuration initiale

### 1. Configuration du VPS

Après avoir créé un serveur Hetzner Cloud avec Ubuntu 24.04 (fourni par Hetzner) :

```bash
# Créer un utilisateur non-root
adduser myusername
usermod -aG sudo myusername

# Configurer SSH (port 2222)
nano /etc/ssh/sshd_config
# Définir : Port 2222, PermitRootLogin no, PasswordAuthentication no
systemctl restart sshd

# Pare-feu basique
ufw allow 2222/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw --force enable
```

### 2. Installation de Coolify

```bash
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```

Coolify fournit :
- Déploiements basés sur Docker
- Reverse proxy Traefik
- CI/CD intégré depuis Git
- Interface web sur le port 8000

Accès via tunnel SSH (plus sécurisé que d'exposer le port 8000) :
```bash
ssh -L 9000:localhost:8000 -p 2222 user@votre-ip-serveur
# Visiter : http://localhost:9000
```

### 3. Déploiement des applications

Dans Coolify :
1. Créer une nouvelle application depuis un dépôt Git
2. Configurer les paramètres de build et variables d'environnement
3. Définir les domaines (initialement essayé avec `https://` mais ça m'a causé des problèmes)
4. Déployer

## Le défi SSL

### Problème : Boucle de redirection HTTP→HTTPS avec Traefik

Traefik de Coolify est configuré pour rediriger HTTP vers HTTPS et demander des certificats Let's Encrypt. Cependant :

- Le challenge HTTP-01 de Let's Encrypt nécessite un accès HTTP à `/.well-known/acme-challenge/`
- Traefik redirige immédiatement vers HTTPS avec un certificat auto-signé
- La validation du challenge échoue → timeout → pas de SSL

**Solutions tentées qui ont échoué :**
- Supprimer `acme.json` et régénérer
- Passer à ZeroSSL
- Configurer manuellement les middlewares Traefik
- Désactiver la redirection HTTP (Coolify la régénère au déploiement)

### Solution : Cloudflare Tunnel

Cloudflare Tunnel contourne le besoin de SSL d'origine en créant un tunnel chiffré du serveur vers l'edge de Cloudflare, qui gère ensuite le SSL pour les visiteurs.

**Installation :**

```bash
# Installer Cloudflared
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
sudo dpkg -i cloudflared-linux-amd64.deb

# S'authentifier
cloudflared tunnel login
```

**Configuration via le dashboard Cloudflare :**

1. Zero Trust → Tunnels → Créer un tunnel
2. Installer le connecteur (déjà fait ci-dessus)
3. Ajouter des noms d'hôte publics :
   - `api.mmameetsdata.com` → `http://127.0.0.1:80` (En-tête Host : `api.mmameetsdata.com`)
   - `mmameetsdata.com` → `http://127.0.0.1:80` (En-tête Host : `mmameetsdata.com`)

**Mettre à jour le DNS :**

Remplacer les enregistrements A par le DNS géré par Cloudflare (serveurs de noms changés vers ceux de Cloudflare).

**Mettre à jour les apps Coolify pour utiliser HTTP :**

```bash
sudo docker exec coolify-db psql -U coolify -d coolify -c "UPDATE applications SET fqdn = 'http://api.mmameetsdata.com' WHERE name = 'API';"
```

Redéployer les apps. Cloudflare Tunnel gère maintenant le SSL, Traefik sert du HTTP en interne.

**Démarrer le service tunnel :**

```bash
sudo systemctl enable --now cloudflared
```

## Leçons clés

### 1. Le protocole d'origine compte

Lors de l'utilisation de solutions CDN/tunnel :
- L'origine doit servir du HTTP (Cloudflare gère le SSL)
- Éviter les boucles de redirection entre CDN et origine
- Utiliser `127.0.0.1` au lieu de `localhost` (problèmes IPv6)

### 2. Permissions Coolify

Après mise à jour de Coolify, corriger les permissions :

```bash
sudo chown -R 9999:9999 /data/coolify
sudo chmod -R 755 /data/coolify
```

Créer un script de mise à jour :

```bash
#!/bin/bash
# Sauvegarder la base de données
sudo docker exec coolify-db pg_dump -U coolify coolify > ~/coolify-backup-$(date +%Y%m%d).sql

# Corriger les permissions
sudo chown -R 9999:9999 /data/coolify
sudo chmod -R 755 /data/coolify

# Mettre à jour
cd /data/coolify/source
sudo docker compose pull
sudo docker compose up -d

# Redémarrer les services
sudo docker restart coolify coolify-proxy
sudo systemctl restart cloudflared
```

### 3. Durcissement de la Sécurité

**Pare-feu (Console Hetzner Cloud) :**
- Entrant : SSH (2222), HTTP (80), HTTPS (443)
- Bloquer : 8000, 8080, 9000 (panneaux d'administration)

**Accéder aux services internes via tunnel SSH :**
```bash
ssh -L 9000:localhost:8000 -L 9001:localhost:3000 -p 2222 user@ip-serveur
```

**Désactiver les services inutiles :**
```bash
sudo systemctl disable --now multipathd open-vm-tools iscsid packagekit
```

**Limites de ressources par conteneur :**
```yaml
API:
  CPUs: 1
  Memory: 512MB (soft: 256MB)
  Swap: 0

Frontend:
  CPUs: 0.8
  Memory: 1GB (soft: 512MB)
  Swap: 0
```

### 4. Prévenir les démarrages à froid

Utiliser une surveillance externe pour maintenir les apps actives :
- **UptimeRobot** (gratuit, vérifie toutes les 5 min)
- Ajouter des endpoints de contrôle de santé
- Configurer gunicorn avec le flag `--preload`

## Conseils de dépannage

**Erreurs 502 après déploiement :**
- Vérifier les logs du conteneur : `sudo docker logs <container-id>`
- Vérifier les mappings de ports : `sudo docker ps`
- Tester l'origine directement : `curl -H "Host: domain.com" http://localhost:80`

**Erreurs 522 Cloudflare :**
- Le tunnel ne peut pas atteindre l'origine
- Vérifier les logs cloudflared : `sudo journalctl -u cloudflared -n 50`
- Vérifier que le service écoute : `sudo netstat -tlnp | grep :80`
- Utiliser `127.0.0.1` et non `localhost` dans la config du tunnel

**Permission refusée au déploiement :**
- Exécuter le script de correction des permissions
- Redémarrer Coolify : `sudo docker restart coolify`
- Redéployer l'application

## Architecture finale

```
Internet
   ↓
Cloudflare Edge (terminaison SSL)
   ↓
Cloudflare Tunnel (chiffré)
   ↓
VPS (Hetzner) - Port 80/443
   ↓
Traefik (routage HTTP)
   ↓
Conteneurs Docker (API + Frontend)
```

## Coûts

- **VPS** : 4,90€/mois (Hetzner CPX21)
- **Cloudflare** : Forfait gratuit
- **Domaine** : ~10€/an
- **Total** : ~6€/mois

## Ressources

- [Documentation Coolify](https://coolify.io/docs)
- [Guide Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
- [Hetzner Cloud](https://www.hetzner.com/cloud)

## Conclusion

Bien que l'auto-hébergement ajoute de la complexité comparé aux plateformes gérées, ça m'apporte surtout :
- Contrôle total sur l'infrastructure
- Coûts réduits à l'échelle
- Opportunités d'apprentissage
- Souveraineté des données

Cloudflare Tunnel a résolu nos défis SSL de manière élégante, évitant d'avoir à lutter avec la validation Let's Encrypt à travers plusieurs couches de reverse proxies.

La configuration finale est prête pour la production, sécurisée, et coûte moins qu'une tasse de café par mois.

---

**En ligne :**
- [mmameetsdata.com](https://mmameetsdata.com) - Outil de comparaison de combattants MMA
- [Documentation API](https://api.mmameetsdata.com/api/docs) - Backend FastAPI