---
phase: quick
plan: 260504-ooh
type: execute
wave: 1
depends_on: []
files_modified: [Desktop/op-model-ecosystem-editor-LATEST.html]
autonomous: true
requirements: [fix-raci-dots]
must_haves:
  truths:
    - "RACI dots appear on team cards immediately after assigning a team to a process pill"
    - "RACI dots update immediately when cycling through R/A/C/I roles"
    - "RACI dots persist after saving and reloading the page"
  artifacts:
    - path: "Desktop/op-model-ecosystem-editor-LATEST.html"
      provides: "Fixed RACI dot rendering"
  key_links:
    - from: "team assignment click handler (~line 2082)"
      to: "renderCards()"
      via: "calling renderCards after team changes"
    - from: "RACI role cycle click handler (~line 2064)"
      to: "renderCards()"
      via: "calling renderCards after role changes"
---

<objective>
Fix RACI dots not appearing on team cards after assignment changes.

Purpose: The RACI dot indicators on team cards never update because the card re-render is not triggered after team assignment or role cycling changes.

Output: Working RACI dot display that updates in real-time and persists across save/load.
</objective>

<context>
@Desktop/op-model-ecosystem-editor-LATEST.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add renderCards() calls after team assignment changes</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
The root cause: when teams are assigned/unassigned or RACI roles are cycled, only `deriveConnections()` and `renderConnections()` are called. The cards themselves are never re-rendered, so `getRaciRolesForTeam()` (which correctly extracts roles) is never re-invoked to generate the dot HTML.

Fix TWO locations:

1. **RACI role cycle handler (~line 2063-2064)**: After the existing `deriveConnections(); renderConnections();` add `renderCards();`
   - This is inside `badge.addEventListener('click', ...)` where cycling through R->A->C->I->remove happens

2. **Team toggle handler (~line 2082-2083)**: After the existing `deriveConnections(); renderConnections();` add `renderCards();`
   - This is inside `item.addEventListener('click', ...)` where teams are added/removed from process pills

Both locations are within the `showEditExplanationPanel` function's team checklist rendering section.

Note: `renderCards()` removes all `.team-card` elements and re-creates them. This is safe because it re-reads from `state.cards` which holds positions, and `createCardElement` calls `getRaciRolesForTeam` which reads the updated `state.processes` teams data.
  </action>
  <verify>
    <automated>grep -n "renderCards" /Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html | grep -c "renderCards"</automated>
  </verify>
  <done>
- renderCards() is called after both team assignment toggle and RACI role cycling
- Opening the file in a browser and assigning a team to a process pill immediately shows the RACI dot on the card
- Cycling through roles (clicking the RACI badge) immediately updates the dot color on the card
  </done>
</task>

<task type="auto">
  <name>Task 2: Verify save/load preserves RACI data correctly</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
The save/load path is already correct but verify by testing:

1. Save handler (line 1582): `const { connections, ...saveState } = state` -- this correctly includes `state.processes` with team objects `{id, role}` in the spread.

2. Load handler (line 1098-1106): `state.processes = migrateProcesses(parsed.processes || [...])` correctly calls `migrateProcesses` which calls `migrateTeams` on each process and child.

3. `migrateTeams` (line 966): Already handles both formats -- objects pass through, plain IDs get wrapped as `{id: t, role: 'R'}`.

No code change needed here IF the above is confirmed working. However, there is one potential issue: after loading from localStorage, `renderAll()` is called (line 1118) which calls `renderCards()` which calls `getRaciRolesForTeam()`. This should work.

If during testing RACI dots DON'T show after reload, check that `migrateProcesses` is being called BEFORE `renderAll()` -- which it is (line 1106 then 1118). This task is verification-only unless a bug is found.
  </action>
  <verify>
    <automated>node -e "const fs=require('fs'); const html=fs.readFileSync('/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html','utf8'); const hasRenderCardsAfterToggle=html.includes('renderConnections();\n                        renderCards()'); const hasRenderCardsNearDerive=html.match(/deriveConnections\(\);\s*\n\s*renderConnections\(\);\s*\n\s*renderCards\(\)/g); console.log('renderCards after connection render:', hasRenderCardsNearDerive ? hasRenderCardsNearDerive.length + ' occurrences' : 'NOT FOUND - needs fix');"</automated>
  </verify>
  <done>
- After save and page reload, RACI dots appear on previously assigned team cards
- Both old format (plain IDs) and new format ({id, role}) are handled on load
  </done>
</task>

</tasks>

<verification>
1. Open the HTML file in browser
2. Enter edit mode
3. Click a process pill to open the explanation/team panel
4. Assign a team by clicking its name in the checklist
5. Verify: RACI dot appears on that team's card immediately
6. Click the RACI badge to cycle roles (R -> A -> C -> I -> remove)
7. Verify: dot color changes on the card in real-time
8. Click Save button
9. Refresh the page
10. Verify: RACI dots still appear on assigned team cards
</verification>

<success_criteria>
- RACI dots render on team cards immediately after any team assignment change
- RACI dots update color when role is cycled
- Dots persist across save/reload
- No visual glitches or card position jumps from re-rendering
</success_criteria>
