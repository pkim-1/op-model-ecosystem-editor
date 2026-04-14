# Phase 2: Search & Filtering - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-04-14
**Phase:** 02-search-filtering
**Areas discussed:** Search UI placement, Filter UI pattern, Search timing, Sort UI, Empty filter state
**Mode:** Auto (all decisions auto-selected with recommended defaults)

---

## Search UI placement

| Option | Description | Selected |
|--------|-------------|----------|
| In header below title | Prominent, always visible, standard pattern | ✓ |
| Above card grid | Keeps header minimal, search closer to results | |
| Floating sidebar | Persistent filters, more space for advanced options | |

**Auto-selected:** In header below title (recommended default)

---

## Filter UI pattern

| Option | Description | Selected |
|--------|-------------|----------|
| Chip-based filters | Reuses existing chip styling, visual, no dropdown complexity | ✓ |
| Dropdown selects | Compact, standard form pattern | |
| Filter sidebar | More space, advanced options, separates from search | |

**Auto-selected:** Chip-based filters below search (recommended default)

---

## Search timing

| Option | Description | Selected |
|--------|-------------|----------|
| Real-time as user types | Instant feedback, modern UX, low latency with client-side filtering | ✓ |
| Explicit "Search" button | User controls when filtering happens | |
| Debounced (300ms delay) | Balance between real-time and performance | |

**Auto-selected:** Real-time as user types (recommended default)

---

## Sort UI

| Option | Description | Selected |
|--------|-------------|----------|
| Dropdown with options | Simple, standard, doesn't clutter UI | ✓ |
| Toggle buttons | More visible, direct control | |
| Inline links | Minimal UI footprint | |

**Auto-selected:** Dropdown with options: Updated (newest), Updated (oldest), Name (A-Z) (recommended default)

---

## Empty filter state

| Option | Description | Selected |
|--------|-------------|----------|
| Clear message + 'Clear filters' button | Helps user recover, prevents dead-end | ✓ |
| Suggestion: "Try searching for..." | Guides user, but may be too prescriptive | |
| Show recent searches | Useful pattern, but adds complexity for v1 | |

**Auto-selected:** Clear message + 'Clear filters' button (recommended default)

---

## Claude's Discretion

Areas where Claude has implementation flexibility:
- Exact spacing between search input and filter chips
- Filter chip hover effects
- Transition animations for card filtering
- URL hash format for filter state

---

## Deferred Ideas

None — all discussion stayed within phase scope
