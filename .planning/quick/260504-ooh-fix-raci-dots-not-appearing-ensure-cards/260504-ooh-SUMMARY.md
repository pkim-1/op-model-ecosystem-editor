---
status: complete
quick_id: 260504-ooh
date: 2026-05-04
commit: a69f6b7
---

# Quick Task 260504-ooh: Fix RACI Dots Not Appearing

## Summary

Fixed RACI dots not appearing after team assignment changes by adding `renderCards()` calls after team/RACI changes in the edit panel.

## Root Cause

The RACI dots rendering logic was complete and correct (`getRaciRolesForTeam()`, `RACI_DOT_COLORS`, `createCardElement()`), but cards were never re-rendered after team assignments changed. The team toggle handler and RACI role cycle handler only called `deriveConnections()` + `renderConnections()`, not `renderCards()`.

## Changes Made

### Task 1: Add renderCards() After Team Assignment Changes

Modified two event handlers in the edit panel:

1. **Team toggle click handler** (~line 2065): Added `renderCards()` after `renderConnections()`
   - When user clicks team badges to add/remove teams from a process
   
2. **RACI role cycle click handler** (~line 2085): Added `renderCards()` after `renderConnections()`
   - When user clicks RACI badges (R/A/C/I) to change team roles

### Task 2: Verify Save/Load Preserves RACI Data

Verified that save/load already correctly handles the new team data structure:
- Teams stored as `{id, role}` objects
- `migrateTeams()` function converts old format (plain IDs) to new format on load
- Save button saves full state including RACI roles to localStorage

## Implementation Details

- Cards now immediately update when team assignments or RACI roles change
- RACI dots appear/disappear dynamically based on actual team assignments
- Color mapping: R=green, A=yellow, C=blue, I=gray

## Commits

- a69f6b7: fix(quick-260504-ooh): add renderCards() after team/RACI changes so dots appear

## Verification

- [x] RACI dots appear after assigning teams in edit mode
- [x] Dots update immediately when changing RACI roles
- [x] Save/load preserves RACI role assignments
- [x] Cards show correct colored dots for each role
