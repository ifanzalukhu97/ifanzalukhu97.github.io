# Implementation Plan: Landing Page Redesign

## Overview

This plan implements the redesign of ifanzalukhu97.com from a basic Jekyll Minimal theme to a custom dark-mode portfolio with embedded CSS, SEO-optimized markup, and client-side blog embedding. The work is split into 7 sequential tasks that build upon each other.

**Estimated effort**: ~2-3 hours of implementation
**Dependencies**: None external — all work is within the repository

## Tasks

- [x] 1. Update Jekyll Configuration: Remove remote theme dependency and simplify config to support custom layout. Remove `remote_theme` from `_config.yml`, remove `jekyll-remote-theme` from plugins and Gemfile, add `url` and `baseurl` settings.
- [x] 2. Create Custom Layout HTML Shell and SEO: Create `_layouts/default.html` with HTML5 doctype, head section with charset/viewport/title/meta description, Open Graph meta tags, Twitter Card meta tags, canonical URL, JSON-LD Person schema, and opening style tag with CSS custom properties and base reset styles.
- [x] 3. Add CSS Components and Responsive Design: Add all CSS rules inside the style block including hero styles, CTA button styles, section layout, card components, grid layout, tech pill badges, photo grid, article cards, connect buttons, footer, responsive breakpoints, and transition utilities.
- [x] 4. Build Content for Hero About and What I Do: Create `index.html` with layout front matter, Hero section (avatar, name, tagline, 2 CTA buttons), About section (builder-tone paragraph), What I Do section (4 cards grid), and delete old `index.md`.
- [x] 5. Build Content for Speaking Articles and Slides: Add Speaking and Community section with photo grid and role badges, Recent Articles section with JS container and dev.to API fetch script with fallback, Read More links to Medium and AWS Builder Center, and Recent Slides section with Jekyll Liquid loop.
- [x] 6. Build Content for Tech Stack Connect and Footer: Add Tech Stack section with category headings and pill badges, Connect section with styled link buttons for all platforms with target blank, and Footer with copyright text.
- [x] 7. Final Polish and Verification: Run jekyll build to verify no errors, check all external links, verify alt text on images, confirm semantic HTML structure, ensure no old theme references remain, validate JSON-LD, and test responsive layout.

## Task Dependency Graph

```json
{
  "waves": [
    ["1"],
    ["2"],
    ["3"],
    ["4"],
    ["5"],
    ["6"],
    ["7"]
  ]
}
```
## Notes

- The `_slides/` collection and `slides/` output directory remain unchanged
- The `images/` directory remains unchanged — existing photos are referenced by URL
- Profile photo is hosted externally on GitHub raw; can be moved to `images/` later if needed
- No external CSS/JS frameworks are introduced — everything is self-contained
- The JavaScript for blog embedding is intentionally minimal (~20 lines) and non-blocking
- Medium and AWS Builder Center do not have public embed APIs, so they are linked as buttons only
