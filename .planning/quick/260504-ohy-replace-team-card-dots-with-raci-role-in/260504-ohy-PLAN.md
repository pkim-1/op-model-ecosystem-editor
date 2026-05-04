---
phase: quick
plan: 260504-ohy
type: execute
wave: 1
depends_on: []
files_modified: [Desktop/op-model-ecosystem-editor-LATEST.html]
autonomous: true
requirements: [replace-team-card-dots-with-raci]
must_haves:
  truths:
    - "Team cards display colored RACI role dots instead of generic green dots"
    - "Legend shows R/A/C/I roles with their colors instead of Active Tool/Connected/Core Team"
    - "RACI dots update dynamically when team assignments change"
  artifacts:
    - path: "Desktop/op-model-ecosystem-editor-LATEST.html"
      provides: "Updated ecosystem editor with RACI role indicators on team cards"
  key_links:
    - from: "createCardElement function"
      to: "state.processes team assignments"
      via: "lookup of RACI roles for each team card"
      pattern: "processes.*teams.*role"
---

<objective>
Replace static green dots in team cards with dynamic RACI role indicators that show each team's assigned roles across all processes. Update the legend to reflect RACI color meanings.

Purpose: Make team cards immediately communicate what RACI roles each team holds across processes.
Output: Updated HTML file with RACI-aware team cards and legend.
</objective>

<execution_context>
@/Users/paulkim/.claude/get-shit-done/workflows/execute-plan.md
@/Users/paulkim/.claude/get-shit-done/templates/summary.md
</execution_context>

<context>
@Desktop/op-model-ecosystem-editor-LATEST.html

Key structures:
- `state.processes[]` — each has `.teams[]` array of `{id, role}` objects (role is R/A/C/I)
- `state.processes[].children[]` — sub-processes also have `.teams[]` with same structure
- `RACI_COLORS = { R: 'var(--accent-green)', A: 'var(--accent-yellow)', C: 'var(--pacific-blue)', I: 'var(--text-muted)' }` already defined
- `.tool-dot` CSS class (line 305) — current green dot styling
- `.legend` (line 390) — current legend at bottom-left
- `createCardElement()` (line 1205) — renders team cards with static `.tool-dot`
</context>

<tasks>

<task type="auto">
  <name>Task 1: Update legend from generic dots to RACI role indicators</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
Replace the legend HTML (around line 903-915) that shows "Active Tool", "Connected", "Core Team" with RACI role legend items:
- R (Responsible) — var(--accent-green)
- A (Accountable) — var(--accent-yellow)
- C (Consulted) — var(--pacific-blue)
- I (Informed) — var(--text-muted)

Each legend item keeps the existing .legend-item/.legend-dot structure but with RACI colors and labels.
  </action>
  <verify>Open file in browser, confirm legend at bottom-left shows R/A/C/I with correct colors</verify>
  <done>Legend displays four RACI role indicators with appropriate colors and labels</done>
</task>

<task type="auto">
  <name>Task 2: Replace static green dots with dynamic RACI role dots in team cards</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
Modify `createCardElement()` (line 1205) to:
1. Instead of rendering a single static `<div class="tool-dot"></div>`, compute the RACI roles for that card by scanning `state.processes` (and their `.children`) for any team entry matching `card.id`
2. Collect unique roles assigned to that team (deduplicate — show at most one dot per role type)
3. Render a colored dot for each unique role, using the existing RACI_COLORS mapping: R=green, A=yellow, C=blue, I=muted
4. Each dot should have a title attribute showing the role name (e.g., title="Responsible")

Create a helper function `getRaciRolesForTeam(teamId)` that:
- Iterates `state.processes` and their `.children`
- Collects all unique roles where team entry id matches
- Returns array of unique role characters (e.g., ['R', 'A'])

Update the `.tool-dot` CSS to support colored variants — add the color via inline style on each dot element rather than changing the base class (keeps backward compat simple).

Also update `renderCards()` or ensure cards re-render when process assignments change (the existing render flow should handle this since `renderCards()` is called after state changes).
  </action>
  <verify>Open file in browser, assign a team to multiple processes with different RACI roles, confirm the team card shows multiple colored dots matching the assigned roles</verify>
  <done>Team cards dynamically display colored RACI dots reflecting their actual role assignments across all processes and sub-processes</done>
</task>

</tasks>

<verification>
- Legend shows R (green), A (yellow), C (blue), I (gray) labels
- Team card with no process assignments shows no dots
- Team card assigned as R in one process shows one green dot
- Team card assigned as R and A in different processes shows green + yellow dots
- Changing a RACI assignment in the process panel updates the team card dots
</verification>

<success_criteria>
Team cards visually communicate RACI participation at a glance. Legend accurately describes the color coding. No regression to existing RACI badge functionality in the process/explanation panels.
</success_criteria>

<output>
After completion, create `.planning/quick/260504-ohy-replace-team-card-dots-with-raci-role-in/260504-ohy-SUMMARY.md`
</output>
