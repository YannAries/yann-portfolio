+++
title = "MMA Meets Data"
date = "2025-01-09"
description = "Data visualisation et stats de combattants avec graphiques modernes"
demo = "https://mmameetsdata.com/"
tags = ["mma", "visualisation-données", "analytique-sports", "python"]
categories = ["sports", "analytique"]
featured = true
+++

Application de visualisation de données pour comparer les statistiques de combattants MMA via des graphiques modernes et accessibles. Conçu pour les analystes, fans et médias specialisés.

## Aperçu

Cette application scrape les données de combattants depuis Tapology.com et génère des visualisations graphiques haute qualité comparant deux combattants sur plusieurs dimensions de performance. L'outil met l'accent sur l'accessibilité, le design professionnel et l'extraction fiable de données.

### Caractéristiques principales

- **Stats complètes** — Taux victoires/défaites, types de finitions (KO/TKO, soumission, décision)
- **Système de design moderne** — Palette conviviale daltoniens avec contraste élevé
- **Cache intelligent** — Réduit charge API avec mise en cache locale du contenu
- **Modes de scraping doubles** — Playwright (navigateur headless) avec fallback requests
- **Prêt export** — Sortie PNG haute résolution (échelle 3x, hauteur 700px)
- **Attribution professionnelle** — Citation source automatique et crédits créateur

## Architecture technique

### Stack backend

- **Python 3.x** — Runtime application principal
- **Plotly** — Visualisation et graphiques avancés
- **BeautifulSoup4** — Analyse HTML et extraction de données
- **Playwright** (optionnel) — Web scraping compatible JavaScript
- **Requests** — Client HTTP avec logique retry

### Pipeline de données

1. **Entrée URL** — URLs profils combattants Tapology.com
2. **Scraping intelligent** — Playwright primaire, requests fallback
3. **Mise en cache contenu** — Stockage local basé MD5
4. **Extraction données** — Sélecteurs CSS pour analyse stats
5. **Visualisation** — Génération graphique radar 8 catégories
6. **Export** — Sortie PNG haute résolution

### Stratégie de scraping

L'application emploie des techniques de scraping sophistiquées :

- **Rotation user-agent** — Profils Chrome 131 modernes
- **Limitation de débit** — Délais aléatoires (2-5 secondes)
- **Logique retry** — Backoff exponentiel pour erreurs HTTP
- **Anti-détection** — Masquage propriétés WebDriver
- **Fallback gracieux** — Changement mode automatique en cas d'échec

## Design de visualisation

### Système de design

Le graphique suit un système de design ère 2025 avec accessibilité en priorité :

**Palette de couleurs :**
- Combattant 1 : Bleu ciel (#0EA5E9) — Primaire, visible par tous
- Combattant 2 : Orange (#F97316) — Alternative conviviale daltoniens au rouge
- Fond : Bleu marine profond (#0A0F19) avec dégradé radial
- Texte : Presque blanc (#F8FAFC) avec gris ardoise secondaires

**Typographie :**
- Famille Police : Inter
- Titre : 24px, poids 700
- Corps : 14px, poids 400
- Légende : 11px, poids 400

### Catégories graphique

Le graphique radar visualise 8 dimensions de performance :

1. **Taux victoires** — Pourcentage victoires global
2. **Victoires KO/TKO** — Taux finitions knockout
3. **Victoires soumission** — Taux finitions soumission
4. **Victoires décision** — Taux victoires décision
5. **Taux défaites** — Pourcentage défaites global
6. **Défaites KO/TKO** — Taux défaites knockout
7. **Défaites soumission** — Taux défaites soumission
8. **Défaites décision** — Taux défaites décision

### Fonctionnalités accessibilité

- **Palette sûre daltoniens** — Aucune combinaison rouge/vert
- **Ratios contraste élevés** — Conforme WCAG AA
- **Étiquettes claires** — Aucune abréviation ambiguë
- **Infobulles survol** — Stats détaillées sur interaction
- **Légende avec records** — Victoires-Défaites-Nuls (NC si applicable)

## Extraction de données

### Analyse profil combattant

L'application extrait :

**Identité :**
- Prénom
- Nom de famille
- Nom complet

**Record :**
- Victoires, défaites, nuls
- Combats sans décision (NC)
- Total combats

**Méthodes victoires :**
- Victoires KO/TKO (pourcentage → compte)
- Victoires soumission (pourcentage → compte)
- Victoires décision (pourcentage → compte)

**Méthodes défaites :**
- Défaites KO/TKO (pourcentage → compte)
- Défaites soumission (pourcentage → compte)
- Défaites décision (pourcentage → compte)

### Sélecteurs CSS

Données extraites via sélecteurs CSS spécifiques :
- Nom : `.text.font-bold`
- Record : `#standardDetails > div:nth-child(2) span`
- Stats victoires : `#tkoRecordStatsWin`, `#subRecordStatsWin`, `#decRecordStatsWin`
- Stats défaites : `#tkoRecordStatsLoss`, `#subRecordStatsLoss`, `#decRecordStatsLoss`

## Configuration

### Modes de scraping

**Mode Playwright** (préféré) :
- Rendu JavaScript complet
- Détection inactivité réseau
- Contournement protection anti-bot
- Taux succès plus élevé

**Mode requests** (Fallback) :
- Utilisation ressources légère
- Requêtes HTTP standard
- Fonctionne sans dépendances navigateur
- Logique retry avec backoff exponentiel

### Système de cache

- **Stockage :** Répertoire `.cache/` avec fichiers JSON
- **Génération clé :** Hash MD5 de l'URL
- **Validation :** Vérification longueur contenu (>100 caractères)
- **Persistance :** Survit aux redémarrages script

## Spécifications de sortie

### Dimensions graphique

- **Hauteur :** 700px (2100px à échelle 3x)
- **Marges :** 160px haut, 80px bas/côtés
- **Format :** PNG haute résolution
- **Nomenclature Fichier :** `{nom-combattant1}-{nom-combattant2}-radar-modern.png`

### Annotations

**Titre :** "Fighter performance comparison"

**Pied de page :**
1. Source : Tapology.com - {année_actuelle}
2. Crafted by @AnalyticsYann
3. Made with Plotly

## Exemple d'utilisation

L'application analyse des affrontements comme :

- Valentina Shevchenko vs. Manon Fiorot
- Deux URLs profils Tapology quelconques

**Sortie Typique :**
```
Mode: PLAYWRIGHT
Scraping fighters...
Fighter 1: https://www.tapology.com/fightcenter/fighters/11614-valentina-shevchenko
Fighter 2: https://www.tapology.com/fightcenter/fighters/136168-manon-fiorot
Creating visualization...
Saved: shevchenko-fiorot-radar-chart.png
```

## Dépendances

Exigences principales :
- plotly
- beautifulsoup4
- requests
- playwright (optionnel, recommandé)

Installation :
```bash
pip install plotly beautifulsoup4 requests
playwright install chromium  # Optionnel mais recommandé
```

## Limitation de débit & éthique

L'application implémente un scraping responsable :

- **Délais aléatoires :** 2-5 secondes entre requêtes
- **Retry after :** Respecte codes statut 429
- **Mise en cache contenu :** Minimise requêtes répétées
- **Rotation user-agent :** S'identifie comme navigateur légitime
- **Attribution source :** Crédite Tapology dans sortie

## Limitations connues

- **Spécifique Tapology :** Fonctionne uniquement avec URLs Tapology.com
- **Sélecteurs CSS :** Peuvent casser si structure site change
- **Pas d'API :** Repose sur web scraping (aucune API officielle disponible)
- **Données statiques :** Pas de mises à jour combats en direct
- **Anglais uniquement :** Analyse suppose texte anglais

## Améliorations futures

Améliorations potentielles :
- Comparaisons multiples combattants (3+)
- Tendances performance historiques
- Contexte catégorie poids
- Analyse force adversaires
- Exports HTML interactifs

## Crédits

**Source données :** [Tapology.com](https://www.tapology.com/)

**Créé par :** @AnalyticsYann

**Visualisation :** Plotly Python

---

**Note :** Ceci est une application source fermée. La documentation décrit fonctionnalités et décisions de design sans inclure code d'implémentation.