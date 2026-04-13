# Feature Research

**Domain:** Personal project portfolio dashboard (GitHub-backed, static HTML)
**Researched:** 2026-04-13
**Confidence:** HIGH

## Feature Landscape

### Table Stakes (Users Expect These)

Features that make this useful as a personal project index. Without these, the dashboard provides no value over just browsing GitHub directly.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| GitHub API repo fetching | Core data source; without it there's no dashboard | MEDIUM | Use GitHub REST API v3. Unauthenticated rate limit is 60 req/hr, sufficient for personal use but consider a PAT for >100 repos |
| Project cards with name, description, topics | Minimum useful display unit; this IS the dashboard | LOW | Map directly from GitHub API fields: `name`, `description`, `topics` |
| Keyword search (text input) | Primary discovery mechanism -- the whole point is finding projects fast | LOW | Client-side filter over fetched data. Search across name + description + topics |
| Filter by topic/tag | GitHub topics are the categorization system per PROJECT.md | LOW | Extract unique topics from all repos, render as clickable filter chips |
| Filter by language/tech stack | Natural grouping for developer projects | LOW | GitHub API returns `language` field per repo. Use it directly |
| Click-through to GitHub repo | Dashboard is an index, not a destination -- must link out | LOW | Link to `html_url` from API response |
| Responsive layout | Even personal tools get used on phones occasionally | LOW | CSS Grid or Flexbox. No framework needed for card layout |
| Sort by recency | Most common access pattern: "what was I working on recently?" | LOW | Sort by `updated_at` or `pushed_at` from API |

### Differentiators (Competitive Advantage)

Features that make this genuinely better than GitHub's own repo list or profile page. These justify the dashboard's existence.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Multi-filter combination | GitHub's UI only filters one dimension at a time. Combining type + language + recency simultaneously is the core UX win | MEDIUM | Maintain filter state object, apply all active filters to card list |
| Filter by project type (analysis, tool, viz) | GitHub has no concept of "project type" but topics can encode it. Surface these as first-class filters | LOW | Convention: use topics like `type-analysis`, `type-tool`, `type-visualization` or just recognize common patterns |
| Visual project type indicators | Quick scanning without reading every card -- color-coded badges or icons by type | LOW | Map project types to colors/icons. Use EG color palette per brand guidelines |
| Instant filter feedback | No page reload, no loading spinners. Filter changes should feel instantaneous since all data is client-side | LOW | All filtering is in-memory JS over already-fetched data |
| Pinned/featured projects | Surface most important projects at top regardless of filters | LOW | Convention: use GitHub "pinned repos" API or a topic like `featured` |
| GitHub Pages link detection | Some repos have hosted outputs. Surface the live URL, not just the repo | LOW | GitHub API returns `homepage` field. Display as separate "View Live" link when present |
| Empty state guidance | When filters match nothing, help the user adjust rather than showing a blank page | LOW | Show "No projects match. Try removing a filter." with active filter summary |

### Anti-Features (Commonly Requested, Often Problematic)

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| Backend server or database | Persistent config, user accounts, caching | Massively increases complexity for a personal tool. Violates the single-HTML-file constraint | Client-side localStorage for preferences. GitHub API for data |
| Manual metadata entry | Richer descriptions than GitHub provides | Creates a maintenance burden -- two places to update info. Will drift immediately | Improve GitHub repo descriptions and topics instead. They're the source of truth |
| Real-time auto-refresh | Always up to date | Unnecessary for personal use. Wastes API rate limit. Page refresh is fine | Manual refresh button or refresh on page load |
| Full-text repo content search | Search inside code/files, not just metadata | Requires cloning repos or using GitHub Search API (complex, rate-limited). Way beyond scope | Keyword search across name + description + topics covers 95% of discovery needs |
| Analytics/tracking | See which projects you visit most | This is a personal tool, not a product. Analytics adds complexity and privacy concerns for zero value | Not needed. You know what you use |
| Dark/light theme toggle | Common in dev tools | Scope creep for v1. One well-chosen theme is fine | Pick one theme. Add toggle later if actually wanted |
| User authentication | Secure access to private repos | For a locally-opened HTML file, auth adds OAuth complexity. Public repos are the target | Use a GitHub PAT stored in localStorage if private repo access is ever needed |
| Project editing from dashboard | Quick metadata updates | Splits the editing interface. GitHub is already the editor. Two editors = confusion | Link directly to GitHub repo settings for editing |

## Feature Dependencies

```
[GitHub API Fetching]
    |
    +--requires--> [Project Cards Display]
    |                  |
    |                  +--enables--> [Keyword Search]
    |                  +--enables--> [Topic Filtering]
    |                  +--enables--> [Language Filtering]
    |                  +--enables--> [Sort by Recency]
    |                  +--enables--> [Click-through to Repo]
    |
    +--enables--> [Multi-filter Combination]
                      |
                      +--requires--> [Topic Filtering]
                      +--requires--> [Language Filtering]
                      +--requires--> [Sort by Recency]

[GitHub Pages Link Detection]
    +--requires--> [Project Cards Display]

[Pinned/Featured Projects]
    +--requires--> [Project Cards Display]
    +--requires--> [Topic convention OR Pinned Repos API]

[Visual Type Indicators]
    +--requires--> [Project Type Classification]
    +--requires--> [Project Cards Display]
```

### Dependency Notes

- **All features require GitHub API Fetching:** This is the foundational data layer. Nothing works without it.
- **Multi-filter Combination requires individual filters first:** Build each filter independently, then compose them.
- **Pinned Projects requires a convention:** Either use GitHub's pinned repos API (GraphQL only, more complex) or a `featured` topic convention (simple, REST API compatible).
- **Visual Type Indicators require type classification:** Need a convention for encoding project type in GitHub topics before you can render type-specific visuals.

## MVP Definition

### Launch With (v1)

Minimum viable product -- enough to replace manually browsing GitHub.

- [ ] GitHub API repo fetching (all public repos for user) -- foundation for everything
- [ ] Project cards with name, description, topics, language -- the display layer
- [ ] Keyword search across name + description + topics -- primary discovery
- [ ] Filter by topic/tag -- the categorization the user already maintains
- [ ] Filter by language -- natural grouping, trivial to implement
- [ ] Sort by recency (most recently updated first) -- default useful ordering
- [ ] Click-through to GitHub repo -- the exit point
- [ ] Responsive card grid layout -- usable on any device

### Add After Validation (v1.x)

Features to add once the core search/filter loop feels right.

- [ ] Multi-filter combination (type + language + recency simultaneously) -- add when single filters feel limiting
- [ ] GitHub Pages link detection (show "View Live" when `homepage` is set) -- add when you notice repos have live demos
- [ ] Pinned/featured projects at top -- add when you have clear favorites to surface
- [ ] Visual type indicators (color-coded badges) -- add when project types are well-established in topics
- [ ] Empty state with filter guidance -- add after you've hit the "no results" state enough to be annoyed

### Future Consideration (v2+)

- [ ] localStorage for remembering last-used filters -- defer until usage patterns stabilize
- [ ] PAT support for private repos -- defer unless private repo access becomes a real need
- [ ] Dark/light theme toggle -- defer indefinitely; one good theme is enough

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| GitHub API fetching | HIGH | MEDIUM | P1 |
| Project cards display | HIGH | LOW | P1 |
| Keyword search | HIGH | LOW | P1 |
| Topic/tag filtering | HIGH | LOW | P1 |
| Language filtering | MEDIUM | LOW | P1 |
| Sort by recency | HIGH | LOW | P1 |
| Click-through to repo | HIGH | LOW | P1 |
| Responsive layout | MEDIUM | LOW | P1 |
| Multi-filter combination | HIGH | MEDIUM | P2 |
| GitHub Pages link | MEDIUM | LOW | P2 |
| Pinned/featured projects | MEDIUM | LOW | P2 |
| Visual type indicators | MEDIUM | LOW | P2 |
| Empty state guidance | LOW | LOW | P2 |
| Filter state persistence | LOW | LOW | P3 |
| Private repo support | LOW | MEDIUM | P3 |

**Priority key:**
- P1: Must have for launch -- the core search and filter loop
- P2: Should have, add when core is solid
- P3: Nice to have, add only if actually needed

## Competitor Feature Analysis

| Feature | GitHub Profile Page | GitProfile/DevFolio | Our Approach |
|---------|-------------------|---------------------|--------------|
| Repo listing | Yes, but no multi-filter | Yes, limited filtering | Multi-dimensional filtering is our core differentiator |
| Search | Global GitHub search only | Usually none | Local instant search across own repos only |
| Topic filtering | Single topic click only | Usually none | Multiple topics simultaneously |
| Language filtering | Available but separate page | Sometimes via GitHub API | Combined with other filters inline |
| Project type grouping | No concept of this | No | Encoded via topic conventions, first-class filter |
| Sort options | Limited (stars, updated, name) | Stars or updated | Recency-first default, most useful for personal discovery |
| Live demo links | Shows homepage if set | Sometimes | Prominent "View Live" button when homepage exists |
| Customization | None | Themes, colors | EG brand palette, single coherent design |
| Hosting | GitHub.com only | GitHub Pages / Vercel | Single HTML file, open anywhere |

## Sources

- GitHub REST API documentation (repos endpoint fields: name, description, topics, language, homepage, updated_at)
- GitHub portfolio ecosystem: masterPortfolio, gitprofile, and GitHub Topics for portfolio-dashboard and github-portfolio
- PROJECT.md constraints: static HTML + JS, GitHub API only, no backend

---
*Feature research for: Personal Project Portfolio Dashboard*
*Researched: 2026-04-13*
