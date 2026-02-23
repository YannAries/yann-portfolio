+++
title = "À propos"
subtitle = "Développeur web | Analyste de données autodidacte | Passionné d'open source"
layout = "about-alternative"
+++

Développeur web et analyste de données autodidacte passionné par la création de solutions web et apps personnalisées. Je me spécialise dans le développement full-stack et l'analyse de données, combinant code et analytique pour résoudre des problèmes concrets.

## Ce que je fais

Je travaille sur l'ensemble de la stack technologique avec divers degrés d'expertise en :

- **Développement frontend** — Vue, Wasp, React, frameworks JavaScript modernes et CSS
- **Développement backend** — Python, FastAPI, Plotly, Dash, Redis/Aiven
- **Cloud** — AWS, Hetzner, OVH, Docker, Coolify, Forgejo, Gitlab, GitHub, pipelines CI/CD
- **IA** — API Mistral AI
- **Open source** — Contribution aux projets et création d'outils pour développeurs

## Projets actuels

En ce moment, je me concentre sur :

- Le déploiement de projets cloud avec Hetzner ou OVH, Docker, Coolify et automatisation CI/CD
- La rédaction d'articles sur les performances web et l'expérience développeur
- La contribution à des projets open-source dans l'écosystème web

## À propos de cette mise en page

> Cette page démontre la **mise en page alternative** avec une carte de profil latérale.

### Caractéristiques principales

Cette mise en page inclut :

1. **Carte de profil latérale gauche** avec :
   - Avatar/image de profil (ou icône de remplacement)
   - Nom et rôle
   - Indicateur de localisation
   - Statistiques personnalisables (configurées dans `hugo.toml`)
   - Liens réseaux sociaux

2. **Zone de contenu principale** avec :
   - Section d'introduction
   - Cartes d'expérience (depuis les séparateurs `---`)
   - Badges de stack technologique (configurés dans `hugo.toml`)

### Comment configurer

**Les statistiques et compétences** sont paramétrées dans votre `hugo.toml` :

```toml
[params.about.alt]
  # Statistiques personnalisées
  [[params.about.alt.stats]]
    value = "5+"
    label = "Années de code"

  # Stack technologique avec icônes
  [[params.about.alt.skills]]
    label = "JavaScript"
    icon = "devicon-javascript-plain"
```

Cela facilite la mise à jour de vos statistiques et compétences sans modifier cette page !

---

**Administrateur et Intégrateur Web** — [Congrégation des Petites Filles de Saint-Joseph](https://www.pfsj.ca/)
*2022 – 2022 • Montréal, QC, Canada | télétravail*

Optimisation et maintenance de la structure HTML pour les pages clés ;
Développement de fonctionnalités dynamiques en PHP ;
Intégration et gestion des ressources images dans le frontend.

---

**Développeur Web Front-end** — [Libéo](https://libeo.com/)
*2021 – 2022 • Montréal, QC, Canada | télétravail*

Création d'une interface web Nuxt.js pour une application météo, intégrant le frontend avec une API backend via le routage côté serveur et des clients HTTP comme Axios ou fetch natif, assurant un flux de données fluide et un rendu dynamique.
Utilisation de Swagger pour tester et consommer une API RESTful interne, validant les endpoints et interactions directement depuis la spécification API.

---

**Testeur QA localisation** — [GlobalStep](https://globalstep.com/)
*2021 – 2021 • Montréal, QC, Canada | télétravail*

Vérification de l'exactitude et de la fluidité des traductions de jeux vidéo et logiciels en utilisant des technologies de localisation avec outils internes, systèmes de mémoire de traduction et plateformes QA automatisées, tout en assurant l'intégrité linguistique et culturelle à travers les éléments texte, audio et UI sur plusieurs plateformes et médias.<br><br>
Adaptation linguistique et culturelle du contenu pour le marché français (France), garantissant l'alignement avec la langue locale, les références culturelles et les normes régionales ;
Documentation et communication de toutes les activités de test, résultats et évaluations au testeur principal via des listes de contrôle standardisées ou formulaires d'évaluation.

---

**Développeur web front-end** — [Festival Fikas](https://www.fikasfest.com/)
*2019 – 2019 • Télétravail*

Administration du site fikasfest.com avec WordPress – édition 2019.

---

**Développeur web full-stack** — [Wink Strategies](https://winkstrategies.com/)
*2017 – 2017 • Montréal, QC, Canada*

Traduction de maquettes en pages interactives respectant les standards modernes, utilisant Vue.js et Laravel en backend ;
Utilisation du système de grille Bootstrap et des media queries pour développer des pages responsive ;
Utilisation de npm pour l'environnement de développement ;
Travail avec Sass pour concevoir du CSS optimisé.

---

## Comparaison des mises en Page

| Caractéristique | À propos standard | À propos alternative |
|-----------------|-------------------|----------------------|
| Mise en page | Centré, colonne unique | Barre latérale + contenu |
| Info profil | Section hero en haut | Carte barre latérale gauche |
| Statistiques | Non incluses | Grille de stats configurable |
| Stack tech | Non incluse | Badges avec icônes |
| Chronologie | Verticale avec marqueurs | Grille de cartes |
| Idéal pour | CV traditionnels | Portfolios modernes |

Essayez les deux mises en page pour voir laquelle correspond à votre style ! Changez avec `layout = "about"` ou `layout = "about-alternative"` dans le frontmatter.

### Design responsive

Les deux mises en page sont entièrement responsive :
- **Desktop :** Barre latérale + contenu (alternative) ou centré (standard)
- **Tablette :** Disposition empilée avec espacement ajusté
- **Mobile :** Colonne unique, optimisée pour petits écrans

---

## Commencer

Pour utiliser cette mise en page sur votre site :

1. Copiez cette structure de fichier de contenu
2. Définissez `layout = "about-alternative"` dans le frontmatter
3. Configurez les statistiques et compétences dans `hugo.toml`
4. Ajoutez votre propre contenu et expérience
5. Optionnellement, ajoutez une image avatar dans `static/images/`