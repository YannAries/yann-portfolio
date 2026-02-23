+++
title = "About me"
subtitle = "Web developer | Self-taught data analyst | Open source enthusiast"
layout = "about-alternative"
+++

Web developer & self-taught data analyst passionate about building custom web solutions and apps. I specialize in full-stack development and data analysis, combining code and analytics to solve real-world problems.
## What I do

I work across the full stack with varying degrees of expertise in:

- **Frontend development** — Vue, Wasp, React, modern JavaScript frameworks and CSS
- **Backend development** — Python, FastAPI, Plotly, Dash, Redis/Aiven
- **Cloud** — AWS, Hetzner, OVH, Docker, Coolify, Forgejo, Gitlab, GitHub, CI/CD pipelines
- **AI** — Mistral AI API
- **Open source** — Contributing to projects and building developer tools

## Current projects

Right now I'm focused on:

- Deploying cloud projects using Hetzner or OVH, Docker, Coolify, and CI/CD automation
- Writing about web performance and developer experience
- Contributing to open-source projects in the web ecosystem

## About this layout

> This page demonstrates the **alternative about layout** with a sidebar profile card.

### Key features

This layout includes:

1. **Left sidebar profile card** with:
   - Avatar/profile image (or placeholder icon)
   - Name and role
   - Location indicator
   - Customizable stats (configured in `hugo.toml`)
   - Social media links

2. **Main content area** with:
   - Introduction section
   - Experience cards (from `---` separators)
   - Tech stack badges (configured in `hugo.toml`)

### How to configure

**Stats and skills** are parametrized in your `hugo.toml`:

```toml
[params.about.alt]
  # Custom stats
  [[params.about.alt.stats]]
    value = "1"
    label = "APIs Built"

  # Tech stack with icons
  [[params.about.alt.skills]]
    label = "JavaScript"
    icon = "devicon-javascript-plain"
```

This makes it easy to update your stats and skills without editing this page!

---

**Administrator and web integrator** — [Congrégation des Petites Filles de Saint-Joseph](https://www.pfsj.ca/)
*2022 – 2022 • Montreal, QC, Canada | remote*

Optimized and maintained HTML structure for key pages;
Developed dynamic functionality using PHP;
Integrated and managed image assets within the frontend.

---

**Front-end web developer** — [Libéo](https://libeo.com/)
*2021 – 2022 • Montreal, QC, Canada | remote*

Built a Nuxt.js web interface for a weather application, integrating the frontend with a backend API using server-side routing and HTTP clients like Axios or native fetch, ensuring seamless data flow and dynamic rendering.
Used Swagger to test and consume an internal RESTful API, validating endpoints and interactions directly from the API specification.

---

**Localization QA tester** — [GlobalStep](https://globalstep.com/)
*2021 – 2021 • Montreal, QC, Canada | remote*

Verified the accuracy and fluency of video game and software translations using localization technologies with internal tools, translation memory systems, and automated QA platforms, while ensuring linguistic and cultural integrity across text, audio, and UI elements on multiple platforms and media.<br><br>
Adapted content linguistically and culturally for the French (France) market, ensuring alignment with local language, cultural references, and regional norms;
Documented and communicated all testing activities, findings, and evaluations to the Lead Tester using standardized checklists or assessment forms.

---

**Front-end web developer** — [Festival Fikas](https://www.fikasfest.com/)
*2019 – 2019 • remote*

Administrated the fikasfest.com website using WordPress – 2019 edition.

---

**Full-stack web developer** — [Wink Strategies](https://winkstrategies.com/)
*2017 – 2017 • Montreal, QC, Canada*

Translated mockups into interactive pages respecting modern standards, using Vue.js and Laravel in the backend;
Used Bootstrap's grid system and media queries to develop responsive pages;
Used npm for the development environment;
Worked with Sass to design optimised CSS.

---

## Layout comparison

| Feature | Standard about | Alternative about |
|---------|---------------|-------------------|
| Layout | Centered, single column | Sidebar + content |
| Profile info | Top hero section | Left sidebar card |
| Stats | Not included | Configurable stats grid |
| Tech stack | Not included | Icon badges |
| Timeline | Vertical with markers | Card-based grid |
| Best for | Traditional resumes | Modern portfolios |

Try both layouts to see which fits your style! Switch by changing `layout = "about"` or `layout = "about-alternative"` in the frontmatter.

### Responsive design

Both layouts are fully responsive:
- **Desktop:** Sidebar + content (alternative) or centered (standard)
- **Tablet:** Stacked layout with adjusted spacing
- **Mobile:** Single column, optimized for small screens

---

## Get started

To use this layout on your site:

1. Copy this content file structure
2. Set `layout = "about-alternative"` in frontmatter
3. Configure stats and skills in `hugo.toml`
4. Add your own content and experience
5. Optionally add an avatar image to `static/images/`