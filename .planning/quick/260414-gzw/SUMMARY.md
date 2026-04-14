---
phase: quick
plan: 01
subsystem: dashboard-ui
tags: [card-links, homepage, ux]
key-files:
  modified:
    - /Users/paulkim/index.html
decisions:
  - Use ternary with trim() check to handle empty/whitespace homepage values
metrics:
  completed: "2026-04-14"
  tasks: 1
  files: 1
---

# Quick Plan 01: Update Card Links to Prefer Homepage URL

Cards now open the deployed tool/site (repo.homepage) instead of the GitHub repo URL when a homepage is set.

## What Changed

In `renderCard()`, added a `cardUrl` variable that checks `repo.homepage` first (with trim to handle empty strings), falling back to `repo.html_url`. Both the click handler and keyboard handler (Enter/Space) use this single variable.

## Commits

| Task | Commit | Description |
|------|--------|-------------|
| 1 | 2c109e2 | Prefer repo.homepage over repo.html_url for card links |

## Deviations from Plan

None - plan executed exactly as written.

## Verification

- `cardUrl` defined with homepage-first logic (line 565)
- Click handler uses `cardUrl` (line 567)
- Keyboard handler uses `cardUrl` (line 574)
- No remaining `repo.html_url` references in click/keydown handlers

## Self-Check: PASSED
