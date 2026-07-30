---
inclusion: always
---

# Product Overview

## What Is This

Personal landing page dan portfolio site untuk **Ifan Jaya Suswanto Zalukhu** — seorang Senior C# .NET Backend Engineer, Head of Engineering di SimpliDOTS, dan AWS Community Builder di bidang AI Engineering.

**Domain:** [www.ifanzalukhu97.com](https://www.ifanzalukhu97.com)

## Purpose

1. **Professional showcase** — Menampilkan expertise, certifications, tech stack, dan community involvement
2. **Content hub** — Menampilkan artikel terbaru dari Dev.to secara otomatis via API
3. **Presentation platform** — Hosting slide deck HTML untuk speaking engagements (conferences, workshops, meetups)
4. **Badge & certification tracker** — Menampilkan stats terbaru dari Credly, Google Cloud Skills Boost, AWS Skill Builder, Microsoft Learn

## Target Audience

- Recruiters dan hiring managers
- Fellow engineers dan community members
- Event organizers mencari speaker
- Students dan mentees

## Key Sections

| Section | Fungsi |
|---------|--------|
| Hero | Intro, tagline, CTA (email & LinkedIn) |
| About | Background singkat, experience summary |
| What I Do | 4 cards: Backend, AI/Automation, Cloud, Leadership |
| Speaking & Community | Photo grid + badges (AWS CB, GDG Speaker, AWS UG Co-organizer) |
| Recent Articles | Auto-fetch dari Dev.to API (4 artikel terbaru) |
| Recent Slides | Collection dari `_slides/` — sorted by date |
| Tech Stack | Categorized pills (Backend, DB, AWS, Azure, DevOps, etc.) |
| Developer Card | daily.dev DevCard |
| Certifications & Badges | Cert cards + links ke profil platform |
| Connect | Links ke semua platform (LinkedIn, GitHub, Medium, etc.) |

## Business Objectives

- Meningkatkan professional visibility dan personal branding
- Menyediakan single source of truth untuk semua online presence
- Menunjukkan technical depth (certifications, community, articles)
- Mendukung speaking engagement dengan slide hosting

## Content Update Patterns

- **Certifications/Badges:** Di-update periodik via `/update-badges-certs` command
- **Articles:** Auto-fetch realtime dari Dev.to API (client-side JS)
- **Slides:** Ditambahkan manual sebagai HTML files + markdown metadata di `_slides/`
- **General content:** Update manual di `index.html`
