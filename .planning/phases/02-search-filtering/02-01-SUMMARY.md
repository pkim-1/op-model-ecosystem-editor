---
phase: 02-search-filtering
plan: 01
subsystem: search-filtering
tags: [search, sort, filtering, url-hash]
dependency_graph:
  requires: [01-01, 01-02]
  provides: [applyFilters, updateHash, readHash, currentSearch, activeTopics, activeLanguages]
  affects: [index.html]
tech_stack:
  added: []
  patterns: [real-time-filtering, url-hash-state, case-insensitive-search]
key_files:
  created: []
  modified: [index.html]
key_decisions:
  - "Used String.includes() for search instead of Fuse.js -- sufficient for exact matching on small dataset"
  - "Comma-separated hash format for multi-value filters (topic=a,b) -- simple and readable"
metrics:
  duration: "160s"
  completed: "2026-04-14"
  tasks_completed: 2
  tasks_total: 2
  files_changed: 1
---

# Phase 02 Plan 01: Search Input, Sort Controls, and Filter Engine Summary

Real-time keyword search with sort dropdown and URL hash state sync for bookmarkable filter combinations.

## What Was Done

### Task 1: Add search input, sort dropdown, and CSS for controls (2f6d4ce)

Added the controls UI section between header and content area:
- Search input with placeholder text and clear (X) button
- Sort dropdown with three options: Updated newest/oldest, Name A-Z
- No-results empty state with "Clear all filters" button
- Responsive CSS that stacks controls vertically on mobile (600px breakpoint)
- Controls div hidden during loading, shown after repos load

### Task 2: Implement applyFilters engine, URL hash sync, and wire controls (45c0a4d)

Implemented the core filtering and state management:
- `applyFilters()` function that filters `window.allRepos` by search term, active topics (AND logic), and active languages (OR logic), then sorts and re-renders
- Search matches case-insensitively across repo name, description, and topics array
- `updateHash()` / `readHash()` for URL hash state sync with format `#search=term&topic=a,b&lang=JavaScript&sort=name-asc`
- Malformed hashes handled gracefully (reset to defaults, no errors)
- Event listeners on search input (real-time), sort dropdown (change), clear button, and clear-all-filters button
- Filter state globals (`currentSearch`, `activeTopics`, `activeLanguages`) ready for Plan 02 chip filters
- Repo count updates to "Showing X of Y repositories" when filters active
- `hashchange` listener for back/forward navigation support

## Deviations from Plan

None - plan executed exactly as written.

## Threat Mitigations Applied

- **T-02-01 (URL hash injection):** All hash values processed through `decodeURIComponent`, never injected into innerHTML. Display uses `textContent` only. Malformed hashes fall back to defaults.
- **T-02-02 (Search input injection):** Search value used only for `String.includes()` comparison. Never rendered via innerHTML. All DOM updates use `textContent`.

## Known Stubs

None. All functionality is fully wired. `activeTopics` and `activeLanguages` arrays are initialized empty -- Plan 02 will add the UI chip toggles to populate them. This is intentional scaffolding, not a stub.

## Self-Check: PASSED
