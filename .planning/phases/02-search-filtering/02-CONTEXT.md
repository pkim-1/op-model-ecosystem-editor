# Phase 2: Search & Filtering - Context

**Gathered:** 2026-04-14
**Status:** Ready for planning

<domain>
## Phase Boundary

Add keyword search, topic filtering, language filtering, and recency sorting to the existing project dashboard. Users should be able to instantly find any project through real-time search and filters, without leaving the single-page interface.

</domain>

<decisions>
## Implementation Decisions

### Search Input
- **D-01:** Search input appears in header, below title and repo count
- **D-02:** Search placeholder: "Search projects by name, description, or topic..."
- **D-03:** Real-time filtering — results update as user types (no "Search" button needed)
- **D-04:** Search is case-insensitive and matches across: repo name, description, and topics array
- **D-05:** Clear button (X icon) appears in search input when text is present

### Filter UI Pattern
- **D-06:** Filters appear as chip row below search input, before card grid
- **D-07:** Topic filter: display all unique topics from repos as clickable chips — click to filter, click again to remove
- **D-08:** Language filter: display all unique languages from repos as clickable chips — same toggle behavior
- **D-09:** Active filter chips use Pacific Blue background (#4248ED), inactive use light gray
- **D-10:** Show active filter count badge on filter section when filters are applied

### Sort Order
- **D-11:** Sort dropdown appears to right of search input (same row)
- **D-12:** Sort options: "Updated (newest)", "Updated (oldest)", "Name (A-Z)"
- **D-13:** Default sort: "Updated (newest)" — matches GitHub's default, shows most recent work first
- **D-14:** Sort persists with filters — sorting doesn't clear search/filters

### Filtering Behavior
- **D-15:** All filters combine with AND logic — search + topic + language all must match
- **D-16:** Filtering happens client-side (no API calls) — operates on `window.allRepos` array
- **D-17:** Filter state updates URL hash (e.g., `#search=react&topic=javascript`) so users can bookmark filtered views

### Empty State Handling
- **D-18:** When filters return zero results: show centered message "No projects match your filters" with "Clear all filters" button
- **D-19:** When user has zero repos initially (before any filters): keep Phase 1's empty state ("No repositories found" with GitHub link)

### Claude's Discretion

Claude has flexibility on:
- Exact spacing between search input and filter chips
- Filter chip hover effects (as long as active/inactive states are clear)
- Transition animations when cards filter in/out (keep subtle, performance-friendly)
- URL hash format for filter state (as long as it's readable and bookmark-friendly)

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Phase 1 Foundation
- `.planning/phases/01-data-foundation-card-display/01-CONTEXT.md` — Established patterns: card layout, Expedia brand colors, topic chips styling
- `index.html` — Current implementation to extend (window.allRepos, renderGrid function, existing CSS)

### Requirements
- `.planning/REQUIREMENTS.md` — SEARCH-01 through SEARCH-04

### Project Context
- `.planning/PROJECT.md` — Single HTML file constraint, static HTML + JS only, core value is "quickly find projects"
- `CLAUDE.md` — Expedia brand colors already established in Phase 1

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- **window.allRepos** — Global array of all fetched repos, already populated by Phase 1's `fetchAllRepos()`
- **renderGrid(repos)** function — Already accepts filtered array, can be reused without modification
- **Topic chip CSS** — `.topic-chip` class already styled, can be reused for filter chips
- **CSS custom properties** — `--eg-blue`, `--pacific-blue`, `--light-gray` already defined for filter UI

### Established Patterns
- **CSS Grid layout** — `#project-grid` with `repeat(auto-fill, minmax(300px, 1fr))`
- **Expedia brand colors** — EG Blue for primary, Pacific Blue for accents
- **Client-side rendering** — All data in memory (`window.allRepos`), no API calls for filtering

### Integration Points
- Add search input and filter chips to header section (before `#project-grid`)
- Filter functions will call existing `renderGrid(filteredRepos)` to update display
- Filter state can be tracked in global variables (e.g., `window.currentSearch`, `window.activeFilters`)

</code_context>

<specifics>
## Specific Ideas

### Reusing Phase 1 Patterns
- Topic filter chips should look identical to topic chips on cards — visual consistency helps users understand they're related
- Search input should use EG Blue for focus border — matches existing link color
- Filter chip toggle should use Pacific Blue for active state — matches existing hover/accent pattern

### Performance Considerations
- Real-time search is feasible because filtering is client-side — no network latency
- For large repo counts (>500), consider debouncing search input (300ms) to avoid excessive re-renders
- Use DocumentFragment in renderGrid (already implemented in Phase 1) for efficient DOM updates

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 02-search-filtering*
*Context gathered: 2026-04-14*
