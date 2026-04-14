---
phase: quick
plan: 01
subsystem: dashboard-ui
tags: [card-url, github-pages, url-fallback]
dependency_graph:
  requires: [CONFIG.username]
  provides: [three-tier-cardUrl-fallback]
  affects: [renderCard]
tech_stack:
  added: []
  patterns: [template-literal-url-construction]
key_files:
  modified: [index.html]
decisions:
  - Removed repo.html_url fallback -- all cards without homepage get GitHub Pages URL attempt
metrics:
  duration: 45s
  completed: 2026-04-14
  tasks_completed: 1
  tasks_total: 1
---

# Quick Task 260414-hk7: Card URL GitHub Pages Auto-Construction Summary

**One-liner:** Three-tier cardUrl fallback: homepage > auto-constructed GitHub Pages URL > (removed direct repo fallback)

## What Changed

Replaced the two-tier cardUrl logic in `renderCard()` (line 565 of `index.html`) with a GitHub Pages URL auto-construction fallback.

**Before:** Cards without `repo.homepage` linked to `repo.html_url` (the GitHub source code page).

**After:** Cards without `repo.homepage` link to `https://pkim-1.github.io/{repo.name}/`, with a special case for the root user site repo (`pkim-1.github.io`) which uses `https://pkim-1.github.io/` without a trailing repo name.

## Tasks Completed

| Task | Name | Commit | Files |
|------|------|--------|-------|
| 1 | Implement three-tier cardUrl fallback | 6e5a420 | index.html |

## Deviations from Plan

None -- plan executed exactly as written.

## Known Stubs

None.

## Self-Check: PASSED
