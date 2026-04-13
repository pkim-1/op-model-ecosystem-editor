# Phase 1: Data Foundation & Card Display - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-04-13
**Phase:** 1-Data Foundation & Card Display
**Areas discussed:** GitHub authentication, Card content & layout, Loading experience, Error handling
**Mode:** Auto-selected recommendations

---

## GitHub Authentication

| Option | Description | Selected |
|--------|-------------|----------|
| PAT required upfront | Force token input before showing dashboard | |
| Optional PAT via localStorage | Start unauthenticated (60 req/hr), offer token input if needed | ✓ |
| No authentication | Always unauthenticated, no token support | |

**User's choice:** Optional PAT via localStorage (recommended default)
**Notes:** Research recommends this as the only safe approach for static HTML. 60 req/hr is sufficient for personal use; token increases to 5000 req/hr if needed.

---

## Card Content & Layout

| Option | Description | Selected |
|--------|-------------|----------|
| Minimal cards | Name + language only | |
| Standard cards | Name, description, topics, language with CSS Grid | ✓ |
| Rich cards | Add stars, fork count, last commit date | |

**User's choice:** Standard cards (recommended default)
**Notes:** Research indicates name, description, topics, language are the core fields needed for browsing. CSS Grid with `minmax(300px, 1fr)` handles responsive layout natively. Apply Expedia brand colors per guidelines.

---

## Loading Experience

| Option | Description | Selected |
|--------|-------------|----------|
| Skeleton screens | Animated placeholders while loading | |
| Simple loading indicator | "Loading repositories..." text | ✓ |
| Progressive loading | Show repos as each page arrives | |

**User's choice:** Simple loading indicator with silent pagination (recommended default)
**Notes:** Research suggests keeping v1 simple. Pagination is handled silently in background — user waits once for all repos to load.

---

## Error Handling

| Option | Description | Selected |
|--------|-------------|----------|
| Silent failures | Suppress errors, show empty state | |
| Clear error messages | Display specific error with retry/token prompt | ✓ |
| Verbose logging | Show all API details in console | |

**User's choice:** Clear error messages with token prompt on rate limit (recommended default)
**Notes:** Research identifies rate limiting as #1 risk. Error handling shows clear messages for API failures, network errors, and rate limits (with offer to input token for higher limits).

---

## Claude's Discretion

Areas where Claude has flexibility:
- Exact card spacing, padding, border radius (modern web design standards)
- Hover effects on cards (subtle, not distracting)
- Topic chip styling (clearly visible and clickable-looking)
- Error message exact wording (clear and actionable)

---

## Deferred Ideas

None — all discussion stayed within phase scope.
