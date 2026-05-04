---
status: complete
quick_id: 260504-psr
date: 2026-05-04
commit: c23bafe
---

# Quick Task 260504-psr: Context-Aware RACI Dots

## Summary

Made RACI dots context-aware so they filter based on the currently selected process. Cards now show only relevant roles for the selected context instead of all roles across all processes.

## Problem

Previously, `getRaciRolesForTeam()` scanned ALL processes and returned all roles a team has across the entire system. This meant when viewing a specific L2 sub-process, cards would show dots for roles in completely unrelated processes, making it confusing to understand "for THIS process, what role does this team have?"

## Solution

Modified `getRaciRolesForTeam()` to accept context parameters and filter roles based on the current selection:

### Three-Way Context Logic

1. **L2 sub-process selected** (`selectedSubIndex !== null`)
   - Cards show ONLY roles from that specific L2 sub-process
   - Example: If "Phase 1.2" is selected, cards only show roles assigned in Phase 1.2

2. **L1 parent process selected** (`selectedPillIndex !== null`, but `selectedSubIndex === null`)
   - Cards show rollup of ALL roles from that parent's L2 children
   - Example: If "Phase 1" is selected, cards show all roles from Phase 1.1, 1.2, 1.3, etc.

3. **Nothing selected** (`selectedPillIndex === null`)
   - Cards show ALL roles across all processes (global view)
   - Default state when no pill is clicked

## Implementation Details

### Changes to getRaciRolesForTeam()

**Signature:** `getRaciRolesForTeam(teamId)` → `getRaciRolesForTeam(teamId, filterPillIndex = null, filterSubIndex = null)`

**Filtering logic:**
- If `filterSubIndex` is provided: scan only `state.processes[filterPillIndex].children[filterSubIndex]`
- Else if `filterPillIndex` is provided: scan `state.processes[filterPillIndex]` and all its children
- Else: scan all processes (original behavior)

### renderCards() Calls Added

Added `renderCards()` at 7 selection-change points to ensure dots update immediately:
1. Parent pill click handler (edit mode)
2. Parent pill click handler (present mode)
3. Sub-pill click handler (edit mode)
4. Sub-pill click handler (present mode)
5. Canvas background click handler (deselect)
6. hideExplanationPanel() (when closing sidebar)
7. Mode toggle (when switching between edit/present)

## Commits

- c23bafe: feat(quick-260504-psr): make RACI dots context-aware based on selected process

## Verification

- [x] L2 sub-process selected → cards show only that L2's roles
- [x] L1 parent process selected → cards show rollup from all L2 children
- [x] Nothing selected → cards show all roles (global view)
- [x] Dots update immediately when selection changes
