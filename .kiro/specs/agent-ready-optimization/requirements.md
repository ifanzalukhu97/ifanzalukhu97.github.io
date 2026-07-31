# Requirements Document

## Introduction

Cloudflare's "Is Your Site Agent-Ready?" scan (isitagentready.com) flagged 14 findings across discoverability, bot access control, agent-native discovery, and API/auth metadata for `www.ifanzalukhu97.com`. This spec addresses the subset of findings that are implementable within this Jekyll/GitHub Pages repository, aligned with the site's actual purpose (a static personal portfolio, not an API provider).

**Goal**: Make the site discoverable and consumable by AI agents/crawlers in a way that supports Ifan's personal branding and visibility goals, without introducing a backend, build tooling, or infrastructure the site doesn't need.

**Confirmed policy decisions** (from stakeholder):
- Allow all AI bots (GPTBot, Claude-Web, Google-Extended, etc.) to crawl the site — visibility is the goal.
- Content Signals: `ai-train=yes, search=yes, ai-input=yes` — no restriction on AI use of content.
- Include Agent Skills Discovery index and WebMCP, even though niche/experimental, per explicit request.

## Glossary

| Term | Definition |
|------|-----------|
| RFC 9309 | The Robots Exclusion Protocol standard (robots.txt) |
| Content Signals | A robots.txt extension declaring AI usage preferences (contentsignals.org) |
| Agent Skills Discovery | Cloudflare-proposed RFC for publishing a machine-readable index of "skills" (SKILL.md documents) a site exposes to AI agents |
| WebMCP | Browser API (`navigator.modelContext`) letting a page register callable tools for AI agents/browsers |
| SKILL.md | A markdown document describing one discrete capability, per the Agent Skills format |
| well-known URI | A reserved path prefix (`/.well-known/`) per RFC 8615 for site-wide machine-readable metadata |

## Out of Scope (with reasoning)

| Finding | Reason |
|---|---|
| Link response headers (RFC 8288) | GitHub Pages is static hosting and cannot set custom HTTP response headers. Would require a Cloudflare Worker/Transform Rule outside this repo. |
| Markdown Negotiation (`Accept: text/markdown`) | Requires server-side content negotiation logic. Not possible on static GitHub Pages hosting. |
| DNS for AI Discovery (DNS-AID / SVCB records + DNSSEC) | DNS-zone-level change, managed in Cloudflare DNS dashboard, not in this repository. |
| API Catalog (RFC 9727) | Site has no public API to catalog. |
| OAuth/OIDC discovery, OAuth Protected Resource, Auth.md | Site has no protected API or agent-authentication flow. |
| MCP Server Card | Site does not host an MCP server. |

These six items are not actioned by this spec. Revisit if the site ever exposes an API or the owner manages the Cloudflare zone directly.

## Requirements

### Requirement 1: Valid robots.txt with AI Crawler Rules and Content Signals

**User Story:** As the site owner, I want a correct, explicit robots.txt that allows all crawlers including AI bots, so that search engines and AI agents can discover and use my content for visibility.

#### Acceptance Criteria

- [ ] `/robots.txt` exists at the site root, is served as `text/plain`, and returns HTTP 200
- [ ] File includes a `User-agent: *` block with `Allow: /`
- [ ] File includes explicit `User-agent` blocks (with `Allow: /`) for named AI crawlers: `GPTBot`, `OAI-SearchBot`, `ChatGPT-User`, `Claude-Web`, `anthropic-ai`, `Google-Extended`, `Amazonbot`, `Bytespider`, `CCBot`, `Applebot-Extended`
- [ ] File includes a `Content-Signal: ai-train=yes, search=yes, ai-input=yes` directive applied to the wildcard block
- [ ] File references the sitemap: `Sitemap: https://www.ifanzalukhu97.com/sitemap.xml`
- [ ] robots.txt is a plain static file so Cloudflare's managed-robots.txt injection (if any) merges with it rather than replacing it silently

---

### Requirement 2: XML Sitemap

**User Story:** As the site owner, I want an up-to-date sitemap referenced from robots.txt, so that crawlers and agents can enumerate all public pages without guessing.

#### Acceptance Criteria

- [ ] `/sitemap.xml` exists, returns HTTP 200, valid XML per sitemaps.org protocol
- [ ] Sitemap includes the homepage and both standalone slide deck pages (`/slides/from-problem-to-product.html`, `/slides/demo-aws-summit-jkt-2026.html`)
- [ ] Sitemap regenerates automatically on every Jekyll build (no manual maintenance step) so it stays in sync when new slides/pages are added
- [ ] robots.txt points to this file

---

### Requirement 3: Agent Skills Discovery Index

**User Story:** As the site owner, I want to publish a small set of "skills" describing how an AI agent can retrieve structured information about me (profile, articles, talks), so that agents can act on my site's content instead of only scraping HTML.

#### Acceptance Criteria

- [ ] `/.well-known/agent-skills/index.json` exists, returns HTTP 200, `$schema` set to `https://schemas.agentskills.io/discovery/0.2.0/schema.json`
- [ ] `skills` array lists at least: profile lookup, recent articles, speaking/slides — each with `name`, `type: "skill-md"`, `description`, `url`, and a `digest` (`sha256:{hex}`) matching the referenced SKILL.md file's actual content
- [ ] Each referenced SKILL.md file exists at the given URL and documents the skill in plain language (what data it returns, where to find it on the site)
- [ ] Skills content stays truthful to what the site actually offers (static content, not live API endpoints) — no skill claims capabilities the site doesn't have

---

### Requirement 4: WebMCP Tool Exposure

**User Story:** As the site owner, I want the homepage to expose a couple of read-only tools via the WebMCP browser API, so that AI browser agents visiting the page can query my profile/articles/talks programmatically instead of parsing the DOM.

#### Acceptance Criteria

- [ ] On page load, if `navigator.modelContext` exists, the script registers tools via feature detection (no errors thrown in browsers without WebMCP support)
- [ ] At least these read-only tools are registered: `get_profile` (name, title, links), `list_talks` (slide decks with title/event/date/url), `list_recent_articles` (reuses already-fetched dev.to data)
- [ ] Each tool has `name`, `description`, `inputSchema` (JSON Schema), and an `execute` callback returning plain JSON-serializable data
- [ ] Implementation does not block page render or interfere with existing dev.to fetch logic
- [ ] No tool exposes write/mutating actions (site has no backend to mutate)

---

## Constraints

- No new build tooling: sitemap generation must use the official `jekyll-sitemap` gem (already compatible with the existing `bundle exec jekyll build` pipeline, no GitHub Pages plugin whitelist restriction since the workflow builds Jekyll directly)
- No external JS frameworks; WebMCP registration is vanilla JS appended to the existing inline script in `_layouts/default.html`
- `.well-known/` must be explicitly included in Jekyll's `include:` config since dot-prefixed paths are excluded by default
- All new static files (robots.txt, SKILL.md files, JSON) live at the repo root / `.well-known/` and pass through Jekyll untouched (no Liquid needed, but `.txt`/`.json`/`.md` are plain static output)
- sha256 digests in the Agent Skills index must be manually recomputed and updated whenever a referenced SKILL.md file's content changes (no automated hash step in the build, per "no build tooling beyond Jekyll" constraint) — this is a documented manual maintenance step
