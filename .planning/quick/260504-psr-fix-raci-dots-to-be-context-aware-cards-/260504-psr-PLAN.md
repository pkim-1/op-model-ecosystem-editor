---
type: quick
description: Fix RACI dots to be context-aware based on selected process
files_modified:
  - Desktop/op-model-ecosystem-editor-LATEST.html
---

<objective>
Make RACI role dots on team cards context-aware so they reflect only the roles relevant to the currently selected process, not all roles across the entire system.

Purpose: When a user selects a specific L2 sub-process or L1 parent process, the RACI dots on cards should show only the roles for that context, making the visualization meaningful.
Output: Updated `op-model-ecosystem-editor-LATEST.html` with context-aware RACI filtering.
</objective>

<context>
@Desktop/op-model-ecosystem-editor-LATEST.html

Key code locations:
- Line 1004-1005: `selectedPillIndex` and `selectedSubIndex` globals track current selection
- Line 1218-1233: `getRaciRolesForTeam(teamId)` scans ALL processes unconditionally
- Line 1235-1258: `createCardElement(card)` calls `getRaciRolesForTeam` without context
- Line 1205-1213: `renderCards()` re-renders all cards (removes and recreates)
- Pill click handlers (edit ~line 1883, present ~line 1899) set selection and call `renderProcessPills()` but NOT `renderCards()`

Data structure: `state.processes` is an array where each process has `{ name, teams: [{id, role}], children: [{ name, teams: [{id, role}] }] }`. `selectedPillIndex` maps to index in `state.processes`, `selectedSubIndex` maps to index in `proc.children`.
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add context filtering to getRaciRolesForTeam and trigger re-render on selection</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
1. Modify `getRaciRolesForTeam` (line 1218) to accept optional filter parameters:

```javascript
function getRaciRolesForTeam(teamId, filterPillIndex, filterSubIndex) {
    const roles = new Set();
    
    // If no filter, scan everything (current behavior)
    if (filterPillIndex === null || filterPillIndex === undefined) {
        state.processes.forEach(proc => {
            if (proc.teams) {
                proc.teams.forEach(t => { if (t.id === teamId && t.role) roles.add(t.role); });
            }
            if (proc.children) {
                proc.children.forEach(child => {
                    if (child.teams) {
                        child.teams.forEach(t => { if (t.id === teamId && t.role) roles.add(t.role); });
                    }
                });
            }
        });
    } else if (filterSubIndex !== null && filterSubIndex !== undefined) {
        // Specific L2 child selected - only that child's roles
        const proc = state.processes[filterPillIndex];
        if (proc && proc.children && proc.children[filterSubIndex]) {
            const child = proc.children[filterSubIndex];
            if (child.teams) {
                child.teams.forEach(t => { if (t.id === teamId && t.role) roles.add(t.role); });
            }
        }
    } else {
        // L1 parent selected - rollup of parent + all its children
        const proc = state.processes[filterPillIndex];
        if (proc) {
            if (proc.teams) {
                proc.teams.forEach(t => { if (t.id === teamId && t.role) roles.add(t.role); });
            }
            if (proc.children) {
                proc.children.forEach(child => {
                    if (child.teams) {
                        child.teams.forEach(t => { if (t.id === teamId && t.role) roles.add(t.role); });
                    }
                });
            }
        }
    }
    return Array.from(roles);
}
```

2. Update the call site in `createCardElement` (line 1242) to pass the current selection:

```javascript
const roles = getRaciRolesForTeam(card.id, selectedPillIndex, selectedSubIndex);
```

3. Add `renderCards()` call after selection state changes in ALL pill click handlers. There are four locations where selection changes and `renderProcessPills()` is called:
   - Edit mode sub-pill click (~line 1883-1896): add `renderCards();` after `renderProcessPills();`
   - Present mode sub-pill click (~line 1899-1911): add `renderCards();` after `renderProcessPills();`
   - Edit mode parent pill click (~lines 1778-1800): add `renderCards();` after `renderProcessPills();`
   - Present mode parent pill click (~lines 1794-1800): add `renderCards();` after `renderProcessPills();`

Also add `renderCards();` in the deselection paths (where `hideExplanationPanel()` is called after setting selectedPillIndex/selectedSubIndex to null) so that deselecting restores the full global view.

Search for all places where `selectedPillIndex` or `selectedSubIndex` is set followed by `renderProcessPills()` and ensure `renderCards()` is called right after.
  </action>
  <verify>
    <automated>Open the file in browser. Select a sub-pill and confirm cards only show RACI dots for that specific sub-process. Select a parent pill and confirm cards show rollup of all children roles. Deselect and confirm all roles return.</automated>
  </verify>
  <done>RACI dots on team cards are context-aware: they filter based on selected pill/sub-pill state, showing only relevant roles for the current selection context.</done>
</task>

</tasks>

<verification>
- Select an L2 sub-process pill: cards show only roles assigned in that sub-process
- Select an L1 parent pill: cards show rollup of roles from all L2 children under that parent
- Deselect all pills: cards show all roles across all processes (original behavior)
- Cards with no role in the selected context show no dots
- Switching between selections updates dots immediately
</verification>

<success_criteria>
RACI dots dynamically reflect the currently selected process context instead of always showing global roles.
</success_criteria>
