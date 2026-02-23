---
title: "Personal portfolio & blog"
date: 2025-01-08
draft: false
featured: true
weight: 1
tags: ["hugo", "web-design", "performance", "jamstack"]
summary: "A minimal, dark-mode portfolio built with Hugo featuring terminal aesthetics, European CDN infrastructure, and 90% smaller icon payload."
github: "https://github.com/yourusername/portfolio"
demo: "https://yoursite.com"
image: "/images/projects/portfolio-preview.png"
---

## Overview

A performance-focused portfolio site built with Hugo, featuring a custom geek and terminal aesthetic and optimized for speed. Replaced traditional icon libraries with a hybrid approach combining Simple Icons font for brands and inline SVG for UI elements, reducing icon payload by 95% (from 800KB to 42KB).

## Key features

- **Zero JavaScript** for core functionality - pure HTML5/CSS3
- **European CDN compliance** - Bunny Fonts (Slovenia) + jsDelivr (Bunny CDN + GCore)
- **Terminal aesthetic** - Grid backgrounds, scanlines, cursor animations, ASCII dividers
- **Smart icon system** - *Simple Icons* for brands, inline SVG for UI (only 9 icons loaded)
- **Full responsive design** - Mobile-first approach with optimized breakpoints
- **Dark mode first** - Cyan accent theme with smooth transitions

## Technical implementation

### Frontend architecture
- **Hugo static site generator** - Fast builds, no runtime overhead
- **Custom CSS architecture** - Separate files for base, components, responsive, and custom styles
- **Icon optimization** - Replaced Font Awesome (300KB) + Devicon (500KB) with Simple Icons font (40KB) + inline SVG (2KB)

### Typography & design
- **Inter** for body text (via Bunny Fonts)
- **JetBrains Mono** for code and terminal elements
- Custom cyan color scheme (#22d3ee) replacing original purple

### Performance optimizations
- Eliminated lucide-static (would load 1500+ icons)
- Inline critical SVG icons (9 total)
- European CDN for GDPR compliance
- Optimized asset pipeline through Hugo

## Tech stack

**Core:**
- Hugo
- HTML5 / CSS3
- Minimal JavaScript (theme toggle, search)

**Infrastructure:**
- Bunny Fonts (European CDN)
- jsDelivr (Bunny CDN + GCore Luxembourg)
- Simple Icons v16
- GLightbox for image galleries

**Development:**
- Docker for local development
- Git for version control
- Hugo modules for theme management

## Design decisions

**Why Hugo over Next.js/Gatsby?**
- Zero JavaScript requirement
- Instant builds (<100ms)
- Perfect Lighthouse scores (100/100)
- No client-side runtime

**Why custom icon system?**
- Traditional icon fonts load thousands of unused icons
- Hybrid approach: brands via font, UI via inline SVG
- 95% reduction in icon payload
- Only load what's actually used

**Why European CDNs?**
- GDPR compliance
- Bunny Fonts as Google Fonts alternative
- jsDelivr uses European infrastructure
- Better privacy for EU visitors

## Challenges & solutions

**Challenge:** Theme's CSS conflicting with custom footer layout  
**Solution:** Created unique class names with `!important` overrides

**Challenge:** Icon rendering inconsistency  
**Solution:** Built conditional rendering logic in footer template to detect icon type (Simple Icons vs inline SVG)

**Challenge:** Smart quotes breaking Hugo templates  
**Solution:** VSCode setting to prevent auto-conversion of straight quotes

## Results

- **Page load:** <1s on 3G
- **Lighthouse score:** +90/100 performance
- **Bundle size:** 42KB (icons) vs 800KB (previous)
- **Build time:** ~50ms average
- **European CDN:** 100% compliant

## Future enhancements

- [x] Implement dark/light theme toggle in navbar
- [ ] Create automated deployment pipeline
- [ ] Integrate analytics (Plausible/Umami)

---

**Live Site:** [yoursite.com](https://yoursite.com)  
**Source Code:** [GitHub](https://github.com/yourusername/portfolio)