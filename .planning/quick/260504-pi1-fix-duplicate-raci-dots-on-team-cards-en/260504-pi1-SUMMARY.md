---
status: complete
quick_id: 260504-pi1
date: 2026-05-04
commit: 5cbbc71
---

# Quick Task 260504-pi1: Fix Duplicate RACI Dots on Team Cards

## Summary

Fixed duplicate RACI dots appearing on team cards by preventing duplicate team entries when toggling teams on/off and adding deduplication on state load to clean existing corrupted data.

## Root Cause

Teams were being added multiple times to the same process with the same or different roles because:
1. **Incomplete removal logic**: Used `splice(idx, 1)` which only removes the FIRST matching entry - if duplicates already existed, subsequent ones remained
2. **No deduplication on load**: Corrupted data with duplicate team entries persisted across sessions

## Changes Made

### 1. Replace splice() with filter() for Complete Removal

Modified the team toggle click handler to use `filter()` instead of `splice()`:
- Old: `target.teams.splice(idx, 1)` - only removes first match
- New: `target.teams = target.teams.filter(t => (t.id || t) !== card.id)` - removes ALL matches

### 2. Add Deduplication in migrateTeams()

Added deduplication logic when loading state from localStorage:
```javascript
// Keep only the last occurrence of each team ID (most recent assignment)
const seen = new Map();
teams.forEach(t => seen.set(t.id, t));
return Array.from(seen.values());
```

This ensures:
- Existing corrupted data is cleaned on load
- Each team appears only once per process
- Most recent role assignment is preserved

## Implementation Details

- L2 sub-processes now correctly show only ONE RACI dot per team
- L1 parent processes correctly show rollup of multiple roles from different L2 children
- Deduplication happens on every state load, providing automatic cleanup

## Commits

- 5cbbc71: fix(quick-260504-pi1): prevent duplicate RACI dots on team cards

## Verification

- [x] Teams can only appear once in each process's teams array
- [x] L2 sub-processes show one RACI dot per team
- [x] L1 parent processes show rollup of roles from children
- [x] Existing duplicate data is cleaned on page load
