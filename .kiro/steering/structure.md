---
inclusion: always
---

# Project Structure

## Directory Layout

```
ifanzalukhu97.github.io/
├── _config.yml              # Jekyll configuration (title, description, collections)
├── _layouts/
│   └── default.html         # Single layout — contains ALL CSS + JS inline
├── _slides/                 # Jekyll collection: slide metadata (not output)
│   ├── from-problem-to-product.md
│   └── demo-aws-summit-jkt-2026.md
├── slides/                  # Built standalone HTML slide decks
│   ├── from-problem-to-product.html
│   └── demo-aws-summit-jkt-2026.html
├── images/                  # Static images (speaker photos, QR codes)
│   └── .gitkeep
├── index.html               # Main landing page (uses default layout)
├── CNAME                    # Custom domain: www.ifanzalukhu97.com
├── Gemfile                  # Ruby dependencies (jekyll, webrick)
├── Gemfile.lock             # Locked dependency versions
├── _site/                   # Jekyll build output (gitignored content)
├── .jekyll-cache/           # Jekyll build cache
└── .kiro/                   # Kiro configuration
    ├── agents/              # Custom agents (update-badges-certs)
    ├── settings/            # MCP and other settings
    ├── skills/              # Custom skills
    ├── specs/               # Feature specs
    └── steering/            # This file and other steering docs
```

## File Responsibilities

### Core Pages

| File | Role |
|------|------|
| `index.html` | Satu-satunya page — semua content ada di sini |
| `_layouts/default.html` | Layout wrapper: `<head>` (CSS, meta, structured data), `<main>{{ content }}</main>`, `<footer>`, `<script>` |

### Slides Collection

- **`_slides/*.md`** — Front matter only (title, date, event, url). Tidak ada body content.
- **`slides/*.html`** — Standalone HTML presentations, self-contained (CSS + JS inline)
- Slides tidak di-generate oleh Jekyll — mereka di-commit sebagai static HTML
- `_slides/` metadata dipakai oleh Liquid template di index.html untuk listing

### Images

- `images/` — Local static images (photos, QR codes)
- External images dari `raw.githubusercontent.com` — Dipakai untuk avatar dan event photos

## Naming Conventions

| Item | Convention | Example |
|------|-----------|---------|
| Slide files | `kebab-case.html` | `from-problem-to-product.html` |
| Slide metadata | `kebab-case.md` | `from-problem-to-product.md` |
| Images | Descriptive, spaces OK | `GDG-Sharing-2.jpg` |
| CSS classes | `kebab-case` | `.cert-card`, `.slide-item` |
| CSS variables | `--kebab-case` | `--bg-primary`, `--accent` |
| Sections | Semantic class names | `.hero`, `.about`, `.certifications` |

## Architecture Decisions

1. **Single-page site** — Semua content di `index.html`. Tidak ada multi-page routing.
2. **Inline everything** — CSS dan JS langsung di layout file. Zero external requests untuk styling/scripts.
3. **No build pipeline** — Hanya Jekyll. No preprocessors, no bundlers.
4. **Slides are independent** — Setiap slide deck adalah standalone HTML. Tidak share CSS/JS dengan main site.
5. **Client-side data fetching** — Articles di-fetch via JS dari Dev.to API. Tidak di-generate saat build.
6. **Collection tanpa output** — `_slides` collection punya `output: false`. Hanya metadata yang dipakai.

## How to Add New Content

### New Slide Deck
1. Create `slides/nama-slide.html` — Standalone HTML presentation
2. Create `_slides/nama-slide.md` — Front matter: title, date, event, url
3. Slide akan otomatis muncul di "Recent Slides" section (sorted by date, limit 4)

### New Certification Card
Add di `index.html` dalam `<section class="certifications">`:
```html
<div class="cert-card">
  <div class="cert-icon">🎖️</div>
  <h3>Certification Name</h3>
  <p>Issuer · Date</p>
</div>
```

### New Connect Link
Add di `index.html` dalam `<section class="connect">`:
```html
<a class="connect-link" href="URL" target="_blank" rel="noopener">EMOJI platform.com/username</a>
```

## Things to Preserve

- HTML structure dan class names di `index.html` — agent `update-badges-certs` depends on them
- Section order di landing page
- CSS variable names (design system)
- Dev.to API fetch logic di layout script
- JSON-LD structured data schema
- Open Graph dan Twitter Card meta tags
