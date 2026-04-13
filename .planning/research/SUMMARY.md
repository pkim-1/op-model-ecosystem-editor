# Project Research Summary

**Project:** Project Portfolio Dashboard
**Domain:** Personal project discovery tool (static HTML, GitHub API-backed)
**Researched:** 2026-04-13
**Confidence:** HIGH

## Executive Summary

This is a personal project index -- a single HTML file that fetches GitHub repos via the REST API and presents them with search and filtering. Experts build this as a zero-dependency, no-build-step vanilla JS application. The entire stack is HTML + CSS + inline JavaScript, with the GitHub REST API as the sole data source. This is the right call: frameworks, bundlers, and backends are unnecessary overhead for a read-only dashboard rendering fewer than 500 items client-side.

The recommended approach is fetch-once, filter-in-memory. Paginate all repos on page load, normalize them into a flat array, and run all search/filter/sort operations client-side against that array. CSS Grid handles the card layout, CSS custom properties handle Expedia brand theming, and the whole thing fits in a single file. The only optional dependency is Fuse.js (7KB via CDN) if fuzzy search proves necessary -- but native string matching is likely sufficient.

The primary risks are: (1) GitHub API rate limiting if the fetch strategy involves N+1 calls instead of a single paginated list endpoint, (2) token exposure if a PAT gets hardcoded in the source, and (3) sparse GitHub metadata making filters useless. Risk 1 and 2 are fully preventable with correct Phase 1 design. Risk 3 requires a metadata audit before or during Phase 1 -- the dashboard is only as good as the descriptions and topics on the repos themselves.

## Key Findings

### Recommended Stack

No build step. No npm. No frameworks. Everything inline or via CDN.

**Core technologies:**
- **Vanilla HTML5 + CSS3:** Page structure and layout. CSS Grid with `repeat(auto-fill, minmax(300px, 1fr))` for responsive cards. CSS custom properties for EG brand colors.
- **Vanilla JavaScript (ES2022+):** Fetch API for GitHub calls, DOM manipulation via template literals, client-side filtering. All modern browsers support this natively.
- **GitHub REST API (v2022-11-28):** `GET /users/{username}/repos?per_page=100` returns name, description, topics, language, homepage, and dates in one call. No extra endpoints needed.
- **Fuse.js 7.1.0 (optional, via CDN):** Only if fuzzy/typo-tolerant search is needed. Native `String.includes()` filtering is the starting point.

### Expected Features

**Must have (table stakes):**
- GitHub API repo fetching with pagination
- Project cards: name, description, topics, language, last updated
- Keyword search across name + description + topics
- Filter by topic/tag
- Filter by language
- Sort by recency (default: most recently updated)
- Click-through to GitHub repo
- Responsive card grid layout

**Should have (differentiators over GitHub's own UI):**
- Multi-filter combination (type + language + recency simultaneously) -- this is the core UX win
- GitHub Pages link detection ("View Live" when `homepage` is set)
- Pinned/featured projects surfaced at top
- Visual type indicators (color-coded by project type)
- Empty state guidance when filters match nothing

**Defer (v2+):**
- localStorage for remembering last-used filters
- PAT support for private repos
- Dark/light theme toggle

### Architecture Approach

Three-layer architecture within a single HTML file: Data Layer (API client, cache, transform), State Manager (central state object driving all rendering), and Presentation Layer (search bar, filter controls, project grid). The key pattern is derived-state rendering -- UI is a pure function of state, any state change triggers re-render of the affected section. Filter functions are pure and composable (AND-composed). All filter options (available languages, topics) are dynamically derived from fetched data, not hardcoded.

**Major components:**
1. **GitHub API Client** -- Paginated fetch, auth header injection, error handling
2. **Data Transform** -- Normalize API response into display-friendly objects
3. **State Manager** -- Central state object (repos, search, filters, sort); changes trigger render
4. **Filter Engine** -- Pure functions: filterRepos(repos, filters, search) returns filtered array
5. **Rendering Layer** -- Separate render functions per section (grid, filters, header, empty state)

### Critical Pitfalls

1. **API rate limiting (CRITICAL)** -- Use the list endpoint only (`/users/{username}/repos?per_page=100`). Never make per-repo detail calls. Design token as optional with graceful degradation.
2. **Token exposure (CRITICAL)** -- Never hardcode tokens in source. Use localStorage prompt pattern. Send via Authorization header only, never as query parameter.
3. **Pagination truncation (CRITICAL)** -- Implement pagination from day one. Loop until response length < per_page. Display total repo count so truncation is immediately visible.
4. **Sparse metadata (HIGH)** -- Audit repo metadata quality before building filter UI. Design for graceful handling of missing descriptions/topics. Consider a "needs metadata" indicator.
5. **Over-building UI before validating core value (MEDIUM)** -- Ship minimal searchable list first. Define "done" as "find any project in under 5 seconds," not visual impressiveness.

## Implications for Roadmap

Based on research, suggested phase structure:

### Phase 1: Data Foundation and Minimal Dashboard

**Rationale:** Everything depends on the API client being correct. Rate limiting, pagination, and token handling must be right from the start -- retrofitting is painful. A minimal card grid validates the core value proposition before investing in UI polish.
**Delivers:** Working single-file dashboard that fetches all repos, displays cards, and links to GitHub. Basic search. Usable immediately.
**Addresses:** GitHub API fetching, project cards, keyword search, click-through to repo, sort by recency, responsive layout
**Avoids:** Rate limiting (single list endpoint), token exposure (localStorage pattern), pagination truncation (loop from day one)

### Phase 2: Filtering and Discovery

**Rationale:** With the data layer proven and the basic dashboard in use, add the features that differentiate this from just browsing GitHub. Filters depend on the data layer being stable and on understanding which filters are actually useful (informed by Phase 1 usage).
**Delivers:** Multi-dimensional filtering (topic, language, type), combined filter state, dynamic filter options derived from data
**Addresses:** Topic/tag filtering, language filtering, multi-filter combination, filter by project type, empty state guidance
**Avoids:** Building filters for sparse metadata (audit metadata after Phase 1), over-building UI before validating core

### Phase 3: Polish and Enhancement

**Rationale:** Polish features that improve scanning speed and visual clarity. Only build these after the core search/filter loop feels right from real usage.
**Delivers:** Visual refinements, featured projects, live demo links, caching for faster loads
**Addresses:** Pinned/featured projects, GitHub Pages link detection, visual type indicators, sessionStorage caching, loading states
**Avoids:** Scope creep (each enhancement is independently valuable and optional)

### Phase Ordering Rationale

- **Dependencies flow downward:** Every feature depends on the API client and data layer (Phase 1). Filters depend on card rendering existing (Phase 1 delivers this). Polish depends on filters being stable (Phase 2 delivers this).
- **Risk front-loading:** The three critical pitfalls (rate limiting, token exposure, pagination) are all addressed in Phase 1. If Phase 1 is built correctly, the remaining phases carry minimal technical risk.
- **Validation gates:** Phase 1 produces a usable tool. Real usage between phases informs what to prioritize in Phase 2 and whether Phase 3 features are even needed.
- **Metadata dependency:** Sparse metadata (Pitfall 4) should be audited after Phase 1 is working -- the dashboard itself becomes the audit tool by showing which repos lack descriptions/topics.

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 1:** GitHub API response shape verification -- confirm that the list endpoint returns topics without a special Accept header in the current API version. Quick validation call recommended.

Phases with standard patterns (skip research-phase):
- **Phase 2:** Client-side filtering is a well-documented pattern. No research needed.
- **Phase 3:** All features (caching, loading states, visual badges) are standard web development. No research needed.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | Official GitHub API docs, MDN browser compatibility. Zero-dependency approach is proven. |
| Features | HIGH | Feature set derived from PROJECT.md constraints and competitor analysis. Clear MVP definition. |
| Architecture | HIGH | Fetch-once/filter-in-memory is an established pattern for small datasets. Single-file constraint simplifies decisions. |
| Pitfalls | HIGH | All pitfalls sourced from official GitHub API documentation (rate limits, pagination). Token security is well-documented. |

**Overall confidence:** HIGH

### Gaps to Address

- **Metadata completeness:** Unknown how many of the user's repos have descriptions and topics populated. Run an audit (even just opening the API endpoint in a browser) before finalizing filter design in Phase 2.
- **Fuse.js necessity:** Unknown whether fuzzy search adds real value over native string matching for this dataset size. Start with native matching; add Fuse.js only if exact matching proves frustrating.
- **Repo count:** Unknown total repo count. If under 100, pagination code is still needed (defensive) but caching is less urgent. If over 200, caching and a loading indicator become important in Phase 1.
- **Topics header requirement:** The PITFALLS research flags that topics may require a specific Accept header (`application/vnd.github.mercy-preview+json`). Verify this during Phase 1 implementation -- recent API versions may include topics by default.

## Sources

### Primary (HIGH confidence)
- GitHub REST API documentation -- endpoints, rate limits, pagination, response fields (https://docs.github.com/en/rest)
- GitHub API Versions page -- v2022-11-28 default confirmed
- MDN Web Docs -- browser compatibility for fetch, CSS Grid, ES2022+

### Secondary (MEDIUM confidence)
- Fuse.js documentation (https://www.fusejs.io/) -- v7.1.0, fuzzy search configuration
- GitHub portfolio ecosystem analysis -- gitprofile, masterPortfolio patterns

---
*Research completed: 2026-04-13*
*Ready for roadmap: yes*
