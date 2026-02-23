---
title: "Portfolio personnel & blog"
date: 2025-01-08
draft: false
featured: true
weight: 1
tags: ["hugo", "web-design", "performance", "jamstack"]
summary: "Portfolio minimaliste en mode sombre construit avec Hugo, esthétique terminal, infrastructure CDN européenne et charge d'icônes réduite de 90%."
github: "https://github.com/yourusername/portfolio"
demo: "https://yoursite.com"
image: "/images/projects/portfolio-preview.png"
---

## Aperçu

Site portfolio axé performance construit avec Hugo, avec esthétique geek et terminal personnalisée et optimisé pour la vitesse. Remplacement des bibliothèques d'icônes traditionnelles par une approche hybride combinant police *Simple Icons* pour marques et SVG inline pour éléments UI, réduisant charge icônes de 95% (de 800KB à 42KB).

## Caractéristiques principales

- **Zéro JavaScript** pour fonctionnalités principales - HTML5/CSS3 pur
- **Conformité CDN européen** - Bunny Fonts (Slovénie) + jsDelivr (Bunny CDN + GCore)
- **Esthétique terminal** - Fonds grille, lignes scan, animations curseur, diviseurs ASCII
- **Système icônes intelligent** - *Simple Icons* pour marques, SVG inline pour UI (- de 10 icônes chargées)
- **Design entièrement responsive** - Approche mobile-first avec breakpoints optimisés
- **Mode sombre prioritaire** - Thème accent cyan avec transitions fluides

## Implémentation technique

### Architecture frontend
- **Générateur site statique Hugo** - Builds rapides, aucune surcharge runtime
- **Architecture CSS personnalisée** - Fichiers séparés pour base, composants, responsive et styles custom
- **Optimisation icônes** - Remplacement Font Awesome (300KB) + Devicon (500KB) par police Simple Icons (40KB) + SVG inline (2KB)

### Typographie & design
- **Inter** pour texte corps (via Bunny Fonts)
- **JetBrains Mono** pour éléments code et terminal
- Schéma couleurs cyan personnalisé (#22d3ee) remplaçant violet original

### Optimisations performance
- Élimination lucide-static (chargerait 1500+ icônes)
- Icônes SVG inline critiques (9 total)
- CDN européen pour conformité RGPD
- Pipeline assets optimisé via Hugo

## Stack technique

**Principal :**
- Hugo
- HTML5 / CSS3
- JavaScript minimal (toggle thème, recherche)

**Infrastructure :**
- Bunny Fonts (CDN Européen)
- jsDelivr (Bunny CDN + GCore Luxembourg)
- Simple Icons v16
- GLightbox pour galeries images

**Développement :**
- Docker pour développement local
- Git pour contrôle version
- Modules Hugo pour gestion thème

## Décisions design

**Pourquoi Hugo plutôt que Next.js/Gatsby ?**
- Aucune exigence JavaScript
- Builds instantanés (<100ms)
- Scores Lighthouse parfaits (100/100)
- Pas de runtime côté client

**Pourquoi système icônes personnalisé ?**
- Polices icônes traditionnelles chargent milliers d'icônes inutilisées
- Approche hybride : marques via police, UI via SVG inline
- Réduction 95% charge icônes
- Charge uniquement ce qui est utilisé

**Pourquoi CDN européens ?**
- Conformité RGPD
- Bunny Fonts comme alternative Google Fonts
- jsDelivr utilise infrastructure européenne
- Meilleure confidentialité visiteurs UE

## Défis & solutions

**Défi :** CSS thème en conflit avec mise en page footer personnalisée  
**Solution :** Noms classes uniques créés avec overrides `!important`

**Défi :** Incohérence rendu icônes  
**Solution :** Logique rendu conditionnel dans template footer pour détecter type icône (Simple Icons vs SVG inline)

**Défi :** Guillemets intelligents cassant templates Hugo  
**Solution :** Paramètre VSCode pour empêcher conversion auto guillemets droits

## Résultats

- **Chargement page :** <1s sur 3G
- **Score Lighthouse :** +90/100 performance
- **Taille bundle :** 42KB (icônes) vs 800KB (précédent)
- **Temps build :** ~50ms moyenne
- **CDN européen :** 100% conforme

## Améliorations futures

- [x] Implémenter toggle thème sombre/clair dans navbar
- [ ] Créer pipeline déploiement automatisé
- [ ] Intégrer analytique (Plausible/Umami)

---

**Site Live :** [yoursite.com](https://yoursite.com)  
**Code Source :** [GitHub](https://github.com/yourusername/portfolio)