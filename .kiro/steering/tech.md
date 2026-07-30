---
inclusion: always
---

# Technology Stack

## Static Site Generator

- **Jekyll 4.3** — untuk build static site
- **Ruby** — required oleh Jekyll (via Bundler/Gemfile)
- **GitHub Pages** — hosting & deployment (push to main = auto deploy)
- **Custom domain:** www.ifanzalukhu97.com (via CNAME)

## Build & Development

| Tool | Command |
|------|---------|
| Install deps | `bundle install` |
| Local dev | `bundle exec jekyll serve` |
| Build | `bundle exec jekyll build` |
| Output | `_site/` directory |

## Frontend

- **Pure HTML/CSS** — Tidak pakai framework JS (React, Vue, dll)
- **Vanilla JavaScript** — Untuk fetch articles dari Dev.to API dan interaktivitas slides
- **CSS Custom Properties** — Design system via `:root` variables
- **Responsive design** — Media queries, no CSS framework (no Bootstrap/Tailwind)
- **Dark theme** — Default, dengan color scheme: `#0d1117` background, `#aaff00` accent

## Design System (CSS Variables)

```css
--bg-primary: #0d1117;
--bg-secondary: #161b22;
--accent: #aaff00;
--text-primary: #e6edf3;
--text-secondary: #8b949e;
--border: #30363d;
--radius-card: 12px;
--max-width: 900px;
```

## Jekyll Features Used

- **Layouts:** Single layout (`_layouts/default.html`) — all CSS inline di head
- **Collections:** `_slides` collection (output: false, sort_by: date)
- **Liquid templating:** Untuk iterate slides di index.html
- **Front matter:** YAML metadata di slides dan pages

## External Integrations

| Integration | Method | Notes |
|-------------|--------|-------|
| Dev.to Articles | Client-side fetch API | `https://dev.to/api/articles?username=ifanzalukhu97&per_page=4` |
| daily.dev DevCard | Image embed | Static image URL |
| GitHub images | Direct links to raw.githubusercontent.com | Speaker photos, avatar |

## Slide Decks

- **Self-contained HTML** — Setiap slide adalah standalone HTML file (no external deps)
- **Custom slide engine** — Vanilla JS: keyboard nav, touch gestures, speaker notes, dark/light toggle
- **Location:** `slides/*.html` (built files) dan `_slides/*.md` (metadata)

## Constraints

- No build tool selain Jekyll (no Webpack, Vite, etc.)
- No npm/package.json — purely Ruby-based tooling
- No JavaScript frameworks
- All CSS inline di layout (no external stylesheet files)
- Site harus fast & lightweight — no heavy dependencies
- Must work on GitHub Pages deployment environment

## SEO & Structured Data

- Open Graph meta tags
- Twitter Card meta tags
- JSON-LD Person schema
- Canonical URL
- Meta description & keywords
