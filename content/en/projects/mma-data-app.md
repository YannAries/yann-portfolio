+++
title = "MMA Meets Data"
date = "2025-01-09"
description = "Fighter stats and data visualization with modern charts"
demo = "https://mmameetsdata.com/"
tags = ["mma", "data-visualization", "sports-analytics", "python"]
categories = ["sports", "analytics"]
featured = true
+++

Data visualization app for comparing MMA fighter statistics through modern, accessible charts. Built for analysts, fans, and medias.

## Overview

This application scrapes fighter data from Tapology.com and generates high-quality chart visualizations comparing two fighters across multiple performance dimensions. The tool emphasizes accessibility, professional design, and reliable data extraction.

### Key features

- **Comprehensive stats** — Win/loss rates, finish types (KO/TKO, submission, decision)
- **Modern design system** — Color-blind friendly palette with high contrast
- **Intelligent caching** — Reduces API load with local content caching
- **Dual scraping modes** — Playwright (headless browser) with requests fallback
- **Export ready** — High-resolution PNG output (3x scale, 700px height)
- **Professional attribution** — Automatic source citation and creator credits

## Technical architecture

### Backend stack

- **Python 3.x** — Core application runtime
- **Plotly** — Advanced charting and visualization
- **BeautifulSoup4** — HTML parsing and data extraction
- **Playwright** (optional) — JavaScript-enabled web scraping
- **Requests** — HTTP client with retry logic

### Data Pipeline

1. **URL input** — Tapology.com fighter profile URLs
2. **Smart scraping** — Playwright primary, requests fallback
3. **Content caching** — MD5-based local storage
4. **Data extraction** — CSS selectors for stats parsing
5. **Visualization** — 8-category radar chart generation
6. **Export** — High-DPI PNG output

### Scraping strategy

The application employs sophisticated scraping techniques:

- **User-agent rotation** — Modern Chrome 131 profiles
- **Rate limiting** — Random delays (2-5 seconds)
- **Retry logic** — Exponential backoff for HTTP errors
- **Anti-detection** — WebDriver property masking
- **Graceful fallback** — Automatic mode switching on failure

## Visualization design

### Design system

The chart follows a 2025-era design system with accessibility as priority:

**Color palette:**
- Fighter 1: Sky blue (#0EA5E9) — Primary, visible to all
- Fighter 2: Orange (#F97316) — Color-blind friendly alternative to red
- Background: Deep navy (#0A0F19) with radial gradient
- Text: Near white (#F8FAFC) with slate gray secondaries

**Typography:**
- Font Family: Inter
- Title: 24px, weight 700
- Body: 14px, weight 400
- Caption: 11px, weight 400

### Chart categories

The radar chart visualizes 8 performance dimensions:

1. **Win rate** — Overall win percentage
2. **KO/TKO wins** — Knockout finish rate
3. **Submission wins** — Submission finish rate
4. **Decision wins** — Decision win rate
5. **Loss rate** — Overall loss percentage
6. **KO/TKO losses** — Knockout loss rate
7. **Submission losses** — Submission loss rate
8. **Decision losses** — Decision loss rate

### Accessibility features

- **Color-blind safe palette** — No red/green combinations
- **High contrast ratios** — WCAG AA compliant
- **Clear labels** — No ambiguous abbreviations
- **Hover tooltips** — Detailed stats on interaction
- **Legend with records** — Win-Loss-Draw (NC if applicable)

## Data extraction

### Fighter profile parsing

The application extracts:

**Identity:**
- First name
- Last name
- Full name

**Record:**
- Wins, losses, draws
- No contests (NC)
- Total fights

**Win methods:**
- KO/TKO wins (percentage → count)
- Submission wins (percentage → count)
- Decision wins (percentage → count)

**Loss methods:**
- KO/TKO losses (percentage → count)
- Submission losses (percentage → count)
- Decision losses (percentage → count)

### CSS selectors

Data is extracted using specific CSS selectors:
- Name: `.text.font-bold`
- Record: `#standardDetails > div:nth-child(2) span`
- Win stats: `#tkoRecordStatsWin`, `#subRecordStatsWin`, `#decRecordStatsWin`
- Loss stats: `#tkoRecordStatsLoss`, `#subRecordStatsLoss`, `#decRecordStatsLoss`

## Configuration

### Scraping modes

**Playwright mode** (preferred):
- Full JavaScript rendering
- Network idle detection
- Anti-bot protection bypass
- Higher success rate

**Requests mode** (Fallback):
- Lighter resource usage
- Standard HTTP requests
- Works without browser dependencies
- Retry logic with exponential backoff

### Caching system

- **Storage:** `.cache/` directory with JSON files
- **Key generation:** MD5 hash of URL
- **Validation:** Content length check (>100 chars)
- **Persistence:** Survives script restarts

## Output specifications

### Chart dimensions

- **Height:** 700px (2100px at 3x scale)
- **Margins:** 160px top, 80px bottom/sides
- **Format:** PNG with high DPI
- **File Naming:** `{fighter1-lastname}-{fighter2-lastname}-radar-modern.png`

### Annotations

**Title:** "Fighter performance comparison"

**Footer:**
1. Source: Tapology.com - {current_year}
2. Crafted by @AnalyticsYann
3. Made with Plotly

## Usage example

The application analyzes fighter matchups like:

- Valentina Shevchenko vs. Manon Fiorot
- Any two Tapology profile URLs

**Typical Output:**
```
Mode: PLAYWRIGHT
Scraping fighters...
Fighter 1: https://www.tapology.com/fightcenter/fighters/11614-valentina-shevchenko
Fighter 2: https://www.tapology.com/fightcenter/fighters/136168-manon-fiorot
Creating visualization...
Saved: shevchenko-fiorot-radar-chart.png
```

## Dependencies

Core requirements:
- plotly
- beautifulsoup4
- requests
- playwright (optional, recommended)

Installation:
```bash
pip install plotly beautifulsoup4 requests
playwright install chromium  # Optional but recommended
```

## Rate Limiting & Ethics

The application implements responsible scraping:

- **Random delays:** 2-5 seconds between requests
- **Retry after:** Respects 429 status codes
- **Content caching:** Minimizes repeat requests
- **User-agent rotation:** Identifies as legitimate browser
- **Source attribution:** Credits Tapology in output

## Known Limitations

- **Tapology-specific:** Only works with Tapology.com URLs
- **CSS selectors:** May break if site structure changes
- **No API:** Relies on web scraping (no official API available)
- **Static data:** No live fight updates
- **English only:** Parsing assumes English text

## Future Enhancements

Potential improvements:
- Multiple fighter comparisons (3+)
- Historical performance trends
- Weight class context
- Opponent strength analysis
- Interactive HTML exports

## Credits

**Data Source:** [Tapology.com](https://www.tapology.com/)

**Created by:** @AnalyticsYann

**Visualization:** Plotly Python

---

**Note:** This is a closed-source application. The documentation describes functionality and design decisions without including implementation code.