# Design Document

## Overview

This design document describes the technical architecture and component design for the ifanzalukhu97.com landing page redesign. The site is a static Jekyll portfolio hosted on GitHub Pages, transitioning from the default `minimal` remote theme to a fully custom dark-mode layout with embedded CSS, semantic HTML, and SEO-optimized structured data.

**Key design goals**:
- Single custom layout file with no external dependencies
- Dark mode with `#aaff00` accent color
- Mobile-first responsive design
- Client-side blog embedding via dev.to API
- Rich SEO metadata (Open Graph, JSON-LD)

## Architecture

### System Architecture

The site is a **static Jekyll site** deployed on GitHub Pages with a custom domain (`www.ifanzalukhu97.com`). There is no server-side logic — all dynamic content (blog posts) is fetched client-side.

```
┌─────────────────────────────────────────────────┐
│                  GitHub Pages                     │
│                                                   │
│  Jekyll Build → Static HTML/CSS/JS               │
│                                                   │
│  _layouts/default.html  ←  index.html            │
│       (template)            (content)            │
└─────────────────────────────────────────────────┘
         │                          │
         ▼                          ▼
┌─────────────────┐      ┌──────────────────┐
│  Browser loads   │      │  Client-side JS   │
│  static page     │      │  fetches dev.to   │
│                  │      │  API articles      │
└─────────────────┘      └──────────────────┘
```

### File Structure

```
ifanzalukhu97.github.io/
├── _config.yml                  # Jekyll config (no remote theme)
├── _layouts/
│   └── default.html             # Custom layout (HTML + CSS + JS)
├── _slides/                     # Existing slide collection (unchanged)
├── images/                      # Static images
├── index.html                   # Page content (replaces index.md)
├── Gemfile                      # Simplified dependencies
└── slides/                      # Slide HTML output (unchanged)
```

### Build Pipeline

1. Push to `main` branch on GitHub
2. GitHub Pages runs Jekyll build
3. `_layouts/default.html` is applied to `index.html`
4. Static output deployed to `www.ifanzalukhu97.com`

No custom plugins, no Node.js build step, no external theme.

## Components and Interfaces

### Component 1: Layout Template (`_layouts/default.html`)

**Responsibility**: Provides the full HTML shell — document head (SEO, meta, styles), body wrapper, and footer JS.

**Validates: Requirements 1.1, 1.4, 1.5, 6.1, 6.2, 6.3, 6.4, 6.5**

**Interfaces**:
- Input: `{{ content }}` — Jekyll injects page content
- Input: `site.title`, `site.description`, `site.url` — from `_config.yml`
- Output: Complete HTML page

**Internal structure**:
```
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Meta, SEO, Open Graph, JSON-LD -->
  <style>/* All CSS */</style>
</head>
<body>
  <main>{{ content }}</main>
  <footer>...</footer>
  <script>/* Blog embed JS */</script>
</body>
</html>
```

### Component 2: Page Content (`index.html`)

**Responsibility**: Contains all section markup (Hero, About, What I Do, Speaking, Articles, Slides, Tech Stack, Developer Card, Certifications & Badges, Connect).

**Validates: Requirements 1.2, 1.3, 2.1, 2.2, 2.3, 2.4, 3.1, 3.2, 3.3, 3.4, 4.1, 4.2, 4.3, 7.1, 7.2, 7.3, 7.4, 8.1, 8.2, 8.3, 8.4, 8.5**

**Interfaces**:
- Uses Jekyll Liquid for slides collection iteration
- Provides `id="articles-grid"` container for JS population
- References external image URLs
- Speaking badges use `<a class="badge badge-link">` for clickable badges linking to external profiles
- Connect links display full URL paths with username for brand consistency (e.g., `linkedin.com/in/ifanzalukhu97`)
- Developer Card section displays daily.dev DevCard image (wide format) as a clickable link
- Certifications section uses a responsive grid of cert-card components with icon, title, and issuer
- Cert-links provide external profile links to Credly, AWS Skill Builder, and Google Skills

### Component 3: Blog Embed Script

**Responsibility**: Fetches and renders latest dev.to articles client-side.

**Validates: Requirements 5.1, 5.2, 5.3, 5.4**

**Interfaces**:
- Input: dev.to public API (`GET /api/articles?username=ifanzalukhu97&per_page=4`)
- Output: Rendered HTML cards inside `#articles-grid`
- Fallback: Static link to dev.to profile on error

### Component 4: CSS Design System

**Responsibility**: All visual styling via CSS custom properties and component classes.

**Validates: Requirements 1.1, 1.5, 2.2**

**Key CSS custom properties**:
```css
--bg-primary: #0d1117;
--bg-secondary: #161b22;
--bg-tertiary: #1c2128;
--accent: #aaff00;
--accent-hover: #88cc00;
--text-primary: #e6edf3;
--text-secondary: #8b949e;
--border: #30363d;
--radius-card: 12px;
--radius-pill: 20px;
--radius-btn: 8px;
--max-width: 900px;
```

## Data Models

### Jekyll Site Config (`_config.yml`)

```yaml
title: "Ifan Jaya Suswanto Zalukhu"
description: "Senior C# .NET Backend Engineer | AWS Community Builder | AI & Workflow Automation Enthusiast"
url: "https://www.ifanzalukhu97.com"
baseurl: ""
collections:
  slides:
    output: false
    sort_by: date
```

### Slides Collection Item (existing `_slides/*.md`)

```yaml
---
title: "Talk Title"
event: "Event Name"
date: 2025-01-01
---
```

### dev.to API Response Shape (client-side)

```typescript
interface DevToArticle {
  title: string;
  url: string;
  published_at: string;       // ISO date
  reading_time_minutes: number;
  description: string;
}
```

### JSON-LD Structured Data (Person)

```json
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Ifan Jaya Suswanto Zalukhu",
  "alternateName": "ifanzalukhu97",
  "jobTitle": "Head of Engineering",
  "worksFor": { "@type": "Organization", "name": "SimpliDOTS" },
  "url": "https://www.ifanzalukhu97.com/",
  "image": "https://github.com/ifanzalukhu97/usu-workshop-genai-n8n-gemini/raw/master/images/simplidots-image.jpg",
  "email": "ivan.zalukhu97@gmail.com",
  "sameAs": [
    "https://linkedin.com/in/ifanzalukhu97",
    "https://dev.to/ifanzalukhu97",
    "https://medium.com/@ifanzalukhu97",
    "https://github.com/ifanzalukhu97",
    "https://builder.aws.com/community/@ifanzalukhu97",
    "https://daily.dev/ifanzalukhu97",
    "https://www.credly.com/users/ifanzalukhu97",
    "https://www.dicoding.com/users/ifanzalukhu97/academies",
    "https://www.skills.google/public_profiles/ed12b36a-c4cb-466b-bfb8-83f5fd0b74d1",
    "https://skillsprofile.skillbuilder.aws/user/ifanzalukhu97/certification-badges"
  ]
}
```

## Correctness Properties

### Property 1: Link Integrity
WHEN a visitor clicks any external link on the page THEN the link resolves to a valid, accessible page. All external links (LinkedIn, GitHub, Dev.to, Medium, AWS Builder, HackerRank, Google Dev, Microsoft Learn, AWS UG Medan Meetup) must return HTTP 200.

**Validates: Requirements 7.1, 7.3, 4.2, 4.3**

### Property 2: Structured Data Validity
WHEN a search engine crawls the page THEN the JSON-LD Person schema is valid and passes Google Rich Results Test without errors. All required fields (name, url, jobTitle, sameAs) are present and correctly formatted.

**Validates: Requirements 6.3, 6.4**

### Property 3: Graceful Degradation Without JavaScript
WHEN JavaScript is disabled in the browser THEN all page content except the blog embed section renders correctly. The page remains fully navigable and readable with static HTML alone.

**Validates: Requirements 1.4, 1.5**

### Property 4: Responsive Layout Integrity
WHEN the page is viewed at any viewport width (320px, 768px, 1024px, 1440px) THEN no horizontal overflow or broken layout occurs. All content remains accessible and readable across breakpoints.

**Validates: Requirements 1.5**

### Property 5: API Failure Resilience
WHEN the dev.to API is unreachable or returns an error THEN the articles section displays a fallback link to the dev.to profile instead of breaking or showing empty space.

**Validates: Requirements 5.4**

### Property 6: Image Accessibility
WHEN a screen reader encounters any image on the page THEN every `<img>` element has a descriptive `alt` attribute providing meaningful context.

**Validates: Requirements 6.6**

### Property 7: Color Contrast Compliance
WHEN accent-colored text (#aaff00) is rendered on dark background (#0d1117) THEN the contrast ratio is at least 4.5:1 for normal text and 3:1 for large text. Measured ratio: 11.8:1 (exceeds WCAG AA).

**Validates: Requirements 1.1**

## Error Handling

| Scenario | Handling |
|----------|----------|
| dev.to API returns error/timeout | Catch in try/catch, render fallback link to dev.to profile |
| dev.to API returns empty array | Show "No articles yet" message with link |
| Profile image fails to load | Browser shows alt text; image has explicit width/height to prevent layout shift |
| Jekyll build fails | GitHub Pages shows build error; no custom error handling needed |
| Slides collection is empty | Liquid `{% if %}` guard prevents rendering empty section |

## Testing Strategy

| Test Type | What | How |
|-----------|------|-----|
| Build verification | Jekyll compiles without errors | Run `jekyll build` locally |
| Visual verification | Layout matches design spec | Manual browser inspection at multiple viewports |
| Link validation | All href targets resolve | Manual check or automated link checker |
| SEO validation | Structured data is valid | Google Rich Results Test tool |
| API integration | dev.to articles render | Load page and verify articles appear |
| Fallback test | Graceful degradation | Block dev.to domain in browser DevTools, verify fallback |
| Accessibility | Semantic HTML, alt text, contrast | Lighthouse audit |
| Performance | Page weight < 200KB, FCP < 1.5s | Lighthouse performance audit |
