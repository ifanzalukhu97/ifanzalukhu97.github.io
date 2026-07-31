# Implementation Plan: Agent-Ready Optimization

## Overview

Implements the 4 in-scope findings from the Cloudflare "Is Your Site Agent-Ready?" scan: robots.txt (with AI bot rules + Content Signals), sitemap.xml, Agent Skills Discovery index, and WebMCP tool exposure. 6 findings (Link headers, Markdown Negotiation, DNS-AID, API Catalog, OAuth/OIDC discovery, MCP Server Card) are explicitly out of scope per `requirements.md`.

**Estimated effort**: ~1-1.5 hours
**Dependencies**: None external — everything ships as static files through the existing Jekyll build

## Tasks

- [x] 1. Create `/robots.txt` at repo root per the design's structure: `User-agent: *` block with `Allow: /` and `Content-Signal: ai-train=yes, search=yes, ai-input=yes`; explicit `User-agent`/`Allow: /` blocks for GPTBot, OAI-SearchBot, ChatGPT-User, Claude-Web, anthropic-ai, Google-Extended, Amazonbot, Bytespider, CCBot, Applebot-Extended; and a trailing `Sitemap: https://www.ifanzalukhu97.com/sitemap.xml` line.
  - _Validates: Requirements 1.1, 1.2, 1.3, 1.4, 1.5_

- [x] 2. Create `/sitemap.xml` at repo root as a Jekyll page with `layout: null` and `permalink: /sitemap.xml` front matter, containing a Liquid loop that emits a `<url><loc></loc></url>` entry for the homepage and one for every item in `site.slides` (with `<lastmod>` from `slide.date`), per the design's template.
  - _Validates: Requirements 2.1, 2.2, 2.3, 2.4_

- [x] 3. Add `include: [.well-known]` to `_config.yml` so the dot-prefixed `.well-known/` directory is not excluded from the Jekyll build output.
  - _Validates: Requirement 3.1 (prerequisite for the index and SKILL.md files to be served)_

- [x] 4. Create `.well-known/agent-skills/profile/SKILL.md`, `.well-known/agent-skills/articles/SKILL.md`, and `.well-known/agent-skills/talks/SKILL.md`, each documenting in plain language a real, already-existing data source: `profile` points to `/llms.txt`, `articles` points to the public dev.to API endpoint (`https://dev.to/api/articles?username=ifanzalukhu97&per_page=4`), `talks` points to the homepage's Recent Slides section / `site.slides` data. No endpoint or capability described may be one the site does not actually have.
  - _Validates: Requirements 3.2, 3.3, 3.4_

- [x] 5. Compute the SHA-256 digest of each SKILL.md file created in task 4 (`shasum -a 256 <file>`), then create `.well-known/agent-skills/index.json` with `$schema: "https://schemas.agentskills.io/discovery/0.2.0/schema.json"` and a `skills` array containing one entry per file with `name`, `type: "skill-md"`, `description`, `url`, and `digest: "sha256:{computed-hex}"`.
  - _Validates: Requirements 3.1, 3.2_

- [x] 6. Append a feature-detected WebMCP registration IIFE to the existing inline `<script>` block in `_layouts/default.html`, placed after the current dev.to fetch logic without modifying it. Guard on `navigator.modelContext && typeof navigator.modelContext.registerTool === 'function'` before doing anything. Register three read-only tools: `get_profile` (returns a static profile object with name/title/tagline/links), `list_talks` (returns an array built via Liquid from `site.slides`, each with title/event/date/url), and `list_recent_articles` (fetches the same dev.to endpoint used elsewhere, returning `[]` on failure). Each tool must define `name`, `description`, `inputSchema`, and `execute`.
  - _Validates: Requirements 4.1, 4.2, 4.3, 4.4, 4.5_

- [x] 7. Run `bundle exec jekyll build` and fix any build errors, then run a one-off shell verification check confirming: `_site/robots.txt` exists and contains `User-agent`; `_site/sitemap.xml` exists, is well-formed XML, and contains a `<loc>` entry for the homepage and for each slide in `_slides/`; `_site/.well-known/agent-skills/index.json` exists and each of its `digest` values matches a fresh `shasum -a 256` of the corresponding SKILL.md file in `_site/`.
  - _Validates: Requirements 1.1, 2.1, 2.2, 3.1, 3.2 (build-time correctness of all preceding tasks)_

## Task Dependency Graph

```json
{
  "waves": [
    ["1", "2", "3"],
    ["4"],
    ["5"],
    ["6"],
    ["7"]
  ]
}
```

## Notes

- Task 3 must land before task 7's verification can find `.well-known` output in `_site/`, but tasks 1, 2, 4 can be authored in parallel — the wave grouping reflects build/verification order, not a hard authoring dependency.
- No changes to `index.html` section markup, CSS, or the existing dev.to fetch IIFE — task 6 only appends to the script block.
- Out-of-scope items (Link headers, Markdown Negotiation, DNS-AID, API Catalog, OAuth/OIDC, MCP Server Card) are not tracked here — see `requirements.md` "Out of Scope" table if revisited later.
- Digest maintenance (task 5) is a manual step going forward: if a SKILL.md file's content changes later, its digest in `index.json` must be recomputed and updated by hand.
- The Cloudflare robots.txt-merge behavior (whether Cloudflare still prepends a managed block after this file is deployed) can only be confirmed by fetching the live URL post-deploy — it is outside what task 7's local build verification can check.
