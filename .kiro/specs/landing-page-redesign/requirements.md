# Requirements Document

## Introduction

Redesign the personal landing page for Ifan Jaya Suswanto Zalukhu (ifanzalukhu97.com) from a basic Jekyll Minimal theme into a modern, professional dark-mode portfolio page. The goal is to build personal branding, improve discoverability (Google SEO + AI indexing), and present a polished image as a hands-on builder and engineering leader.

**Target audience**: Recruiters, potential collaborators, event organizers, fellow engineers, and anyone discovering Ifan via search or social links.

**Key outcomes**:
- Professional visual identity with dark mode + neon green accent
- Clear CTAs for contact/collaboration
- SEO & AI discoverability via structured data
- Auto-embedded blog content from dev.to

## Glossary

| Term | Definition |
|------|-----------|
| CTA | Call-to-Action — a button or link prompting user interaction |
| JSON-LD | JavaScript Object Notation for Linked Data — structured data format for SEO |
| OG | Open Graph — meta tag protocol for rich link previews |
| dev.to API | Public REST API for fetching articles from dev.to platform |
| Jekyll | Static site generator used by GitHub Pages |
| Pills/Badges | Small rounded UI elements displaying individual tech skills |

## Requirements

### Requirement 1: Professional First Impression

**User Story:** As a visitor (recruiter, collaborator, event organizer), I want to see a visually polished, modern dark-mode landing page with clear identity, so that I immediately understand who Ifan is and what he does.

#### Acceptance Criteria

- [ ] Page uses dark background (`#0d1117`) with neon green accent (`#aaff00`)
- [ ] Hero section displays profile photo, full name, and tagline
- [ ] Tagline reads: "Senior C# .NET Backend Engineer | AWS Community Builder | AI & Workflow Automation Enthusiast"
- [ ] Page loads in under 3 seconds on mobile (no heavy frameworks)
- [ ] Layout is responsive and looks good on mobile, tablet, and desktop

---

### Requirement 2: Clear Call-to-Action

**User Story:** As a potential collaborator or hiring manager, I want to easily find how to contact Ifan, so that I can reach out for collaboration, speaking invites, or job opportunities.

#### Acceptance Criteria

- [ ] Hero section has two CTA buttons: "Let's Talk" (mailto email) and "Connect on LinkedIn"
- [ ] CTA buttons use the accent color and are immediately visible
- [ ] Email link opens default mail client with pre-filled recipient (ivan.zalukhu97@gmail.com)
- [ ] LinkedIn button links to https://linkedin.com/in/ifanzalukhu97

---

### Requirement 3: Showcase Expertise & Identity

**User Story:** As a visitor, I want to quickly understand Ifan's technical expertise and professional identity, so that I can assess fit for collaboration or hiring.

#### Acceptance Criteria

- [ ] "About" section conveys builder/maker tone with engineering leadership credibility
- [ ] "What I Do" section shows 3-4 focus areas as visual cards (Backend Systems, AI & Automation, Cloud, Engineering Leadership)
- [ ] Tech stack displayed as visual pills/badges grouped by category
- [ ] Content is in English throughout

---

### Requirement 4: Speaking & Community Presence

**User Story:** As an event organizer or community member, I want to see evidence of Ifan's speaking and community involvement, so that I'm confident inviting him to speak or collaborate.

#### Acceptance Criteria

- [ ] Section displays grid of speaking photos (GDG DevFest, Build with AI, AWS events)
- [ ] AWS Community Builder badge links to the badges tab (`builder.aws.com/community/@ifanzalukhu97?tab=badges`)
- [ ] AWS User Group Medan badge links to the meetup page (`meetup.com/aws-user-group-medan/`)
- [ ] GDG DevFest Speaker badge displayed (non-clickable)
- [ ] Recent presentation slides listed with links

---

### Requirement 5: Blog / Article Discovery

**User Story:** As a developer or tech reader, I want to see Ifan's latest articles, so that I can read his technical content and assess thought leadership.

#### Acceptance Criteria

- [ ] Latest 3-4 dev.to articles are embedded via client-side API fetch
- [ ] Each article card shows title, date, and link
- [ ] Links to Medium and AWS Builder Center are shown as "Read More" buttons
- [ ] Fallback gracefully if dev.to API is unavailable (show link instead)

---

### Requirement 6: SEO & AI Discoverability

**User Story:** As the site owner, I want the page to be well-indexed by Google and AI systems, so that I'm easier to find when people search for my name or expertise.

#### Acceptance Criteria

- [ ] Page has proper `<title>` and `<meta description>`
- [ ] Open Graph and Twitter Card meta tags present
- [ ] JSON-LD structured data (Person schema) with name, jobTitle, url, sameAs links
- [ ] Canonical URL set to `https://www.ifanzalukhu97.com/`
- [ ] Semantic HTML used (`<main>`, `<section>`, `<header>`, `<footer>`, `<nav>`)
- [ ] All images have descriptive alt text

---

### Requirement 7: Connect & Social Presence

**User Story:** As a visitor, I want to find all of Ifan's social/professional profiles in one place, so that I can follow or connect on my preferred platform.

#### Acceptance Criteria

- [ ] Connect section displays links to: LinkedIn, GitHub, Dev.to, Medium, AWS Builder Center, HackerRank, Google Dev, Microsoft Learn, daily.dev, Credly, Google Skills, Dicoding, AWS Skill Builder
- [ ] Links display full URL paths with username visible (e.g., `linkedin.com/in/ifanzalukhu97`) for brand consistency
- [ ] Links are styled as icon/buttons, not a plain table
- [ ] All links open in new tab

---

---

### Requirement 8: Certifications & Developer Credentials

**User Story:** As a visitor (recruiter, collaborator), I want to see Ifan's professional certifications and developer credentials in one place, so that I can quickly assess his verified skills and continuous learning commitment.

#### Acceptance Criteria

- [ ] "Developer Card" section displays daily.dev DevCard image (wide format) linking to daily.dev profile
- [ ] "Certifications & Badges" section shows key certifications: Azure AI Engineer Associate, Azure AI Fundamentals, Foundation of Generative AI, Google Cloud Diamond League
- [ ] Certification cards display icon, title, and issuing organization
- [ ] Links to full profiles on Credly, AWS Skill Builder, and Google Skills are provided
- [ ] Connect section includes additional platform links: daily.dev, Credly, Google Skills, Dicoding, AWS Skill Builder

---

## Constraints

- Must remain a static Jekyll site deployable on GitHub Pages
- No external CSS frameworks (Tailwind, Bootstrap) — pure custom CSS
- No build tooling beyond Jekyll
- Minimal JavaScript (only for blog embed fetch)
- Profile photo URL: `https://github.com/ifanzalukhu97/usu-workshop-genai-n8n-gemini/raw/master/images/simplidots-image.jpg`
- Custom domain: `www.ifanzalukhu97.com`
- Full English content
