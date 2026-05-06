---
phase: quick
plan: 260506-gur
type: execute
wave: 1
depends_on: []
files_modified: [Desktop/op-model-ecosystem-editor-LATEST.html]
autonomous: true
---

<objective>
Add "SS" (Shared Services) as a selectable RACI role in the op-model ecosystem editor.

Purpose: Expand team assignment options to include Shared Services designation alongside R/A/C/I.
Output: SS cycles correctly in badge click sequence (R->A->C->I->SS->remove) and displays with appropriate color.
</objective>

<context>
@Desktop/op-model-ecosystem-editor-LATEST.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add SS to all RACI role constants and color mappings</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
Update the following locations in Desktop/op-model-ecosystem-editor-LATEST.html:

1. Line ~1226 - RACI_ROLE_NAMES: Add `SS: 'Shared Services'`
2. Line ~1227 - RACI_DOT_COLORS: Add `SS: 'var(--accent-coral, #FF6B6B)'` (use a distinct color not already used by R/A/C/I)
3. Line ~2092 - RACI_ROLES array: Change from `['R', 'A', 'C', 'I']` to `['R', 'A', 'C', 'I', 'SS']`
4. Line ~2093 - RACI_COLORS: Add `SS: 'var(--accent-coral, #FF6B6B)'` (same color as dot)
5. Line ~2235 - RACI_COLORS_PRESENT: Add `SS: 'var(--accent-coral, #FF6B6B)'`
6. Line ~2236 - RACI_LABELS: Add `SS: 'Shared Services'`
7. Line ~2237 - RACI_ORDER: Change from `['R', 'A', 'C', 'I']` to `['R', 'A', 'C', 'I', 'SS']`

For the badge text color on SS (line ~2258 area), SS should use light text (like I does) since coral is a medium-tone background. Update the color conditional from `role === 'I'` to `role === 'I' || role === 'SS'` wherever it determines text color contrast.

The cycling logic already works generically via RACI_ROLES array indexing (lines 2132-2138), so adding SS to the array is sufficient for R->A->C->I->SS->remove cycling.
  </action>
  <verify>
Open Desktop/op-model-ecosystem-editor-LATEST.html in browser. Enter edit mode, select a process pill, click a team card to assign R role. Click the R badge repeatedly to cycle: R->A->C->I->SS->remove. Verify SS badge appears in coral color with readable text. In presentation mode, verify SS dots appear on cards and SS group shows in the vertical RACI display.

Automated check: grep -c "SS" Desktop/op-model-ecosystem-editor-LATEST.html should return 7+ matches (the new additions).
  </verify>
  <done>SS appears in all RACI constant objects, cycles correctly as the 5th role before removal, displays with a distinct coral color, and shows properly in both edit and presentation modes.</done>
</task>

</tasks>

<verification>
- SS added to RACI_ROLES, RACI_COLORS, RACI_DOT_COLORS, RACI_ROLE_NAMES, RACI_LABELS, RACI_COLORS_PRESENT, RACI_ORDER
- Badge click cycles R->A->C->I->SS->remove
- SS badge has readable text contrast
- SS dots appear on team cards when role is assigned
</verification>

<success_criteria>
SS is fully functional as a RACI role: assignable, visible, and removable via the same cycling mechanism as existing roles.
</success_criteria>
