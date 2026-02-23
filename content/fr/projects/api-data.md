+++
title = "Unofficial Tapology API"
date = "2025-01-18"
description = "API pour données de combattants MMA et calendriers d'événements"
demo = "https://api.tapology-unofficial.com"
tags = ["api", "mma", "sports", "fastapi", "python"]
categories = ["api"]
featured = true
+++

API REST qui récupére des informations complètes sur les combattants MMA et les calendriers d'événements (passés et futurs) depuis Tapology.com, avec cache Redis optimisé.

## Aperçu

L'API Tapology Non Officielle fournit un accès programmatique aux profils détaillés de combattants MMA, historiques de combats et calendriers d'événements. Conçue pour la performance et la fiabilité, elle sert les données avec des temps de réponse inférieurs à la seconde grâce à une mise en cache intelligente et des mécanismes de basculement.

### Caractéristiques principales

- **Profils de combattants** — Informations détaillées incluant records, statistiques et données biographiques
- **Résultats de combats** — Historique complet des combats avec résultats, méthodes et records
- **Calendriers d'événements** — Événements MMA à venir avec cartes de combat complètes par région
- **Événements passés** — Événements historiques avec résultats et détails complets
- **Requêtes groupées** — Récupérer plusieurs combattants ou régions en un seul appel (jusqu'à 3-5 par requête)
- **Cache optimisé** — Redis avec compression pour temps de réponse rapides
- **Haute disponibilité** — Pattern circuit breaker maintient la stabilité sous charge
- **Filtrage de champs** — Demander uniquement les champs nécessaires pour minimiser la bande passante

## Architecture technique

### Stack backend

- **FastAPI** — Framework web Python async moderne
- **Redis** — Cache distribué avec compression
- **BeautifulSoup4** — Analyse HTML efficace
- **Python 3.12.8** — Python dernière version avec améliorations de performance
- **Gunicorn + Uvicorn** — Serveur ASGI de niveau production

### Fonctionnalités de performance

- **Connection pooling** — Réutilise les connexions efficacement
- **Logique de retry** — Backoff exponentiel avec tenacity
- **Compression** — Compression automatique des réponses pour charges utiles volumineuses
- **Logging structuré** — Suivi complet des requêtes avec structlog

## Points de terminaison API

### Profils de combattants

Obtenir des informations détaillées sur les combattants incluant records, stats et données bio.

**Combattant unique :**
```http
GET /api/fighter/{fighter_id}
```

**Requête groupée (jusqu'à 3 combattants) :**
```http
GET /api/fighter/profiles/{fighter_ids}
```

**Exemple de réponse :**
```json
{
  "data": {
    "firstname": "Georges",
    "lastname": "St-Pierre",
    "nickname": "Rush",
    "age": "43",
    "date_of_birth": "May 19, 1981",
    "weight_class": "Welterweight",
    "full_record": "26-2-0",
    "wins": 26,
    "losses": 2,
    "tko_ko": {"wins": 8, "losses": 1},
    "submission": {"wins": 6, "losses": 0},
    "decision": {"wins": 12, "losses": 1}
  }
}
```

### Résultats de combattants

Récupérer l'historique complet des combats avec résultats détaillés.

**Combattant unique :**
```http
GET /api/fighter/result/{fighter_id}
```

**Requête groupée (jusqu'à 3 combattants) :**
```http
GET /api/fighter/results/{fighter_ids}
```

**Exemple de réponse :**
```json
{
  "results": [
    {
      "opponent_name": "Michael Bisping",
      "event_date": "2017-11-04",
      "method": "Submission, Technical Choke",
      "event": "UFC 217",
      "result": "win",
      "fighter_record_before_fight": "25-2-0"
    }
  ]
}
```

### Calendriers d'événements

Accéder aux événements à venir par région (1-121).

**Région unique :**
```http
GET /api/schedule/events/{region_id}
```

**Requête groupée (jusqu'à 3 régions) :**
```http
GET /api/schedule/events/{region_ids}
```

**Exemple de réponse :**
```json
{
  "events": [
    {
      "organization": "UFC",
      "main_event": "Islam Makhachev vs. Dustin Poirier",
      "datetime": "June 01, 2025",
      "city": "Las Vegas, Nevada",
      "fight_card": {
        "fight_1": {
          "fight": "Islam Makhachev vs. Dustin Poirier",
          "weight_class": "155",
          "title_bout": true
        }
      }
    }
  ]
}
```

### Événements passés

Parcourir les événements historiques avec résultats complets.

**Région unique :**
```http
GET /api/schedule/past-events/{region_id}?page=1
```

**Requête groupée (jusqu'à 3 régions) :**
```http
GET /api/schedule/past-events/{region_ids}?page=1
```

## Paramètres de requête

### Filtrage de champs

Demander uniquement des champs spécifiques pour réduire la taille de la réponse :
```http
GET /api/fighter/117305-alex-pereira?fields=firstname,lastname,full_record,wins,losses
```

**Champs valides par point de terminaison :**

**Profils de combattants :** firstname, lastname, nickname, age, date_of_birth, born, weight_class, last_weigh_in, full_record, current_streak, last_fight, wins, losses, draws, no_contest, tko_ko, submission, decision, disqualification

**Résultats de combattants :** fighter_name, opponent_name, result, method, event, event_date, level, title_bout, billing, weight, duration, odds, referee

**Événements :** organization, main_event, weight_class, datetime, city, subregion, broadcast, title_bout_desc, fight_card

### Pagination

Les événements passés supportent la pagination :
```http
GET /api/schedule/past-events/1?page=2
```

## Configuration

### Variables d'environnement
```bash
# Application
APP_ENV=production
PORT=8080
WORKERS=4

# Redis
REDIS_URL=redis://localhost:6379/0
CACHE_EXPIRY=3600

# Scraping
SCRAPING_TIMEOUT=30
SCRAPING_RETRIES=3

# Circuit breaker
CIRCUIT_BREAKER_THRESHOLD=5
CIRCUIT_BREAKER_TIMEOUT=30
```

### Comportement du cache

- **TTL :** 3600 secondes (1 heure) en production
- **Compression :** Automatique pour réponses > 1KB
- **Stratégie :** Politique d'éviction allkeys-lru

## Limitation de débit & bonnes pratiques

### Utilisation recommandée

- **Respecter le cache :** Les requêtes répétées pour les mêmes données retournent des réponses en cache
- **Points de terminaison Groupés :** Utiliser les endpoints groupés au lieu de multiples requêtes simples
- **Filtrage de champs :** Demander uniquement les champs nécessaires pour réduire la bande passante
- **Gestion d'erreurs :** Implémenter un backoff exponentiel pour les retries

### Temps de réponse

- **Requêtes en cache :** < 50ms
- **Scraping :** 2-5 secondes
- **Requêtes Groupées :** 3-8 secondes (selon l'état du cache)

## Gestion des erreurs

### Codes de statut HTTP

- `200` — Succès
- `400` — Requête invalide (mauvais ID combattant, champs invalides, etc.)
- `404` — Combattant ou ressource non trouvé
- `500` — Erreur serveur interne

### Format de réponse d'erreur
```json
{
  "detail": "Fighter not found: invalid-fighter-id"
}
```

## Déploiement

Conçu pour déploiement conteneurisé :

### Docker
```bash
docker build -t tapology-api .
docker run -p 8080:8080 \
  -e REDIS_URL=redis://redis:6379/0 \
  tapology-api
```

### Docker compose
```yaml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      - REDIS_URL=redis://redis:6379/0
    depends_on:
      - redis
  
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
```

## Contrôles de santé

Surveiller la santé de l'API :
```http
GET /api/health
```

**Réponse :**
```json
{
  "status": "ok",
  "version": "1.3.0",
  "environment": "production"
}
```

## Points techniques saillants

### Stratégies d'optimisation

- **Connection pooling** — Réutilise les connexions HTTP (taille du pool : 45)
- **Architecture async** — I/O non bloquant partout
- **Cache intelligent** — Stockage basé sur hash avec compression
- **Circuit breaking** — Échec rapide pendant les pannes
- **Logging structuré** — IDs de requête pour débogage

### Fonctionnalités de fiabilité

- **Retries automatiques** — 3 tentatives avec backoff exponentiel
- **Gestion des timeouts** — Configurable par opération
- **Dégradation gracieuse** — Retourne données en cache quand le scraping échoue
- **Surveillance de santé** — Points de terminaison de contrôle de santé intégrés

## Cas d'usage

Parfait pour :

- **Outils de paris sportifs** — Accéder aux stats de combattants et cotes
- **Applications de fans** — Construire des outils de comparaison de combattants
- **Projets de recherche** — Analyser les tendances et statistiques MMA
- **Agrégateurs de contenu** — Afficher événements à venir et résultats

---

**Démo API :** [https://api.tapology-unofficial.com](https://api.tapology-unofficial.com)

**Documentation :** Documentation API interactive à `/api/docs`

**Version :** 1.3.0