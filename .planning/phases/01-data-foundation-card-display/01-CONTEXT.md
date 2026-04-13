# Phase 1: Data Foundation & Card Display - Context

**Gathered:** 2026-04-13
**Status:** Ready for planning

<domain>
## Phase Boundary

Build the foundation layer that fetches all GitHub repositories via API and displays them as browsable project cards in a responsive grid layout. This phase delivers a working dashboard that shows all projects — no search or filtering yet, just browse and click through to repos.

</domain>

<decisions>
## Implementation Decisions

### GitHub API & Authentication
- **D-01:** Start unauthenticated by default (60 requests/hour is sufficient for personal use)
- **D-02:** Offer optional GitHub PAT input via UI prompt when user wants higher rate limits (5000 req/hr)
- **D-03:** Store PAT in localStorage only — never hardcode in HTML source
- **D-04:** Use GitHub REST API v2022-11-28 (stable default): `GET /users/{username}/repos?per_page=100`
- **D-05:** Handle pagination automatically — fetch all pages sequentially until no `Link: next` header

### Card Content & Layout
- **D-06:** Each card displays: repo name (prominent), description, topics (as chips), primary language (as badge)
- **D-07:** Use CSS Grid layout: `repeat(auto-fill, minmax(300px, 1fr))` for responsive cards
- **D-08:** Visual hierarchy: name is largest/boldest, description is readable body text, metadata (topics/language) is smaller/subtle
- **D-09:** Apply Expedia brand colors: EG Blue (#000099) for primary elements (links, buttons), Pacific Blue (#4248ED) for accents
- **D-10:** Each card is clickable — clicking anywhere on card opens GitHub repo in new tab (target="_blank")

### Loading Experience
- **D-11:** Show simple "Loading repositories..." text while fetching (no elaborate skeleton screens for v1)
- **D-12:** Handle pagination silently — user doesn't see multiple loading states, just waits for all repos to load
- **D-13:** Display repo count once loaded: "Showing X repositories"

### Error Handling
- **D-14:** API failures: display error message with retry button ("Failed to fetch repositories. [Retry]")
- **D-15:** Rate limit hit: show clear message explaining the limit + offer token input to unlock 5000 req/hr
- **D-16:** Network errors: show friendly message suggesting user check connection
- **D-17:** Empty state: if user has zero repos, show "No repositories found" with link to create one on GitHub

### Claude's Discretion

Claude has flexibility on:
- Exact card spacing, padding, border radius (keep consistent with modern web design)
- Hover effects on cards (subtle, not distracting)
- Topic chip styling (as long as topics are clearly visible and clickable-looking)
- Error message exact wording (as long as it's clear and actionable)

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Research Artifacts
- `.planning/research/STACK.md` — Vanilla HTML/CSS/JS stack, no dependencies, GitHub REST API specifics
- `.planning/research/FEATURES.md` — Table stakes features, MVP scope, anti-features to avoid
- `.planning/research/ARCHITECTURE.md` — Component structure, data flow, build order
- `.planning/research/PITFALLS.md` — Rate limiting prevention, pagination requirements, token security

### Requirements
- `.planning/REQUIREMENTS.md` — DATA-01 through DATA-04, DISP-01 through DISP-04

### Project Context
- `.planning/PROJECT.md` — Single HTML file constraint, static HTML + JS only, Expedia brand colors
- `CLAUDE.md` — Technology stack section with GitHub API details and brand guidelines

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
None — greenfield project (no existing code to reuse)

### Established Patterns
None — this is the first phase, establishing patterns

### Integration Points
This phase creates the foundation:
- Single HTML file will contain structure, styling, and GitHub API client
- Future phases will extend this file (add search inputs, filter controls, type badges)

</code_context>

<specifics>
## Specific Ideas

### Brand Application
- Expedia Group color palette per brand guidelines in CLAUDE.md
- EG Blue (#000099) for primary interactive elements
- Pacific Blue (#4248ED) for hover states and accents
- White for card backgrounds
- Use Centra No2 font if available, fallback to system sans-serif

### Research-Informed Choices
- Research emphasizes rate limiting as #1 killer — D-05 (pagination) and D-15 (rate limit handling) directly address this
- Research recommends localStorage for token (D-03) as the only safe approach for static HTML
- Research suggests minimal approach for v1 (D-11) — elaborate loading states can come later

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 01-data-foundation-card-display*
*Context gathered: 2026-04-13*
