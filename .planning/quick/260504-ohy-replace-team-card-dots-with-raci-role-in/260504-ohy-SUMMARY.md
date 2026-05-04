---
status: complete
quick_id: 260504-ohy
date: 2026-05-04
commit: f83056e
---

# Quick Task 260504-ohy: RACI Dots in Team Cards

## Summary

Replaced static green dots in team cards with dynamic RACI role indicators. Removed the "Active Tool/Connected/Core Team" legend and replaced it with RACI role indicators (R/A/C/I with distinct colors).

## Changes Made

### Task 1: Update Legend
- Replaced legend labels from "Active Tool", "Connected", "Core Team" to RACI role indicators
- R (Responsible) - Purple/accent-purple
- A (Accountable) - Blue/pacific-blue  
- C (Consulted) - Yellow/accent-yellow
- I (Informed) - Green/accent-green

### Task 2: Dynamic RACI Dots on Team Cards
- Added `getRaciRolesForTeam()` helper function that:
  - Scans all processes and sub-processes for team assignments
  - Collects unique RACI roles for each team
  - Returns role set for display
- Modified `createCardElement()` to:
  - Call `getRaciRolesForTeam()` for each card
  - Render colored dots based on actual RACI roles assigned
  - Show no dots if team has no process assignments
  - Display dots in fixed order: R, A, C, I

## Implementation Details

- RACI role data stored in `state.processes[].teams[]` as `{id, role}` objects
- Color mapping: R=purple, A=blue, C=yellow, I=green
- Dots update on card render and remain in sync with team assignments

## Commits

- f83056e: feat(quick-260504-ohy): replace team card dots with dynamic RACI role indicators

## Verification

- [x] Legend shows RACI role indicators with colors
- [x] Team cards display colored dots for assigned RACI roles
- [x] Cards with no assignments show no dots
- [x] Dots match the role assignments from the RACI matrix in edit mode
