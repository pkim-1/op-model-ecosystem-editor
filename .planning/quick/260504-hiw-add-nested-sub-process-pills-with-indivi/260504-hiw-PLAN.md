---
phase: quick
plan: 260504-hiw
type: execute
wave: 1
depends_on: []
files_modified:
  - /Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html
autonomous: true
requirements: [nested-sub-process-pills]
must_haves:
  truths:
    - "Parent process pills can expand to reveal nested sub-process pills"
    - "Sub-process pills have their own name, explanation, and team associations"
    - "Clicking a sub-pill in present mode highlights only that sub-pill's teams"
    - "Edit mode allows adding, editing, and deleting sub-pills"
    - "Data persists correctly through save/load cycle"
  artifacts:
    - path: "/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html"
      provides: "Nested sub-process pill functionality"
  key_links:
    - from: "state.processes[].children[]"
      to: "renderProcessPills()"
      via: "forEach loop renders parent then children"
    - from: "sub-pill click in present mode"
      to: "highlightTeamsForPill()"
      via: "passes sub-pill's teams to highlighting logic"
---

<objective>
Add nested sub-process pills to the TPA Ecosystem Editor. Each top-level process pill gains a `children` array where each child has its own name, explanation, and team associations. Sub-pills display indented below their parent, support expand/collapse, and work in both edit and present modes.

Purpose: Enable a second hierarchy level in the process flow bar for more granular process documentation and team highlighting.
Output: Updated single HTML file with nested sub-process pill support.
</objective>

<context>
@/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Extend data model and migration, add CSS for nested pills</name>
  <files>/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
1. Extend the process data structure to include a `children` array:
   ```
   { name: 'Phase 1', explanation: '', teams: [], children: [] }
   ```
   Each child has the same shape: `{ name, explanation, teams }` (no further nesting).

2. Update `migrateProcesses()` to add `children: []` to existing processes that lack it, preserving backward compatibility with saved data.

3. Add CSS styles for the nested structure:
   - `.process-pill-group`: flex container (column) wrapping a parent pill and its children
   - `.sub-pill-row`: flex row below parent, left-padded ~1.5rem, gap 0.5rem
   - `.sub-pill`: similar to `.process-pill` but smaller (font-size: 0.75rem, padding: 0.35rem 0.9rem), border-radius 14px, slightly muted border color
   - `.sub-pill.active-pill`: same active styling as parent
   - `.sub-pill .pill-delete`: same delete button pattern, scaled down
   - `.process-pill .expand-toggle`: small chevron indicator (right of pill text) showing expand/collapse state
   - `.process-pill-group.collapsed .sub-pill-row`: display none
   - In present mode, `.sub-pill.present-clickable` with hover effect

4. Add a `collapsedPills` Set (or track on state) to remember which parent indices are collapsed. Default: expanded.
  </action>
  <verify>Open the file in browser, confirm no JS errors in console, existing pills still render correctly with no visual regression.</verify>
  <done>Data model extended with children array, migration handles legacy data, CSS for sub-pills defined, collapse tracking in place.</done>
</task>

<task type="auto">
  <name>Task 2: Render nested pills and wire edit/present mode interactions</name>
  <files>/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
1. Update `renderProcessPills()`:
   - Wrap each parent pill + its children in a `.process-pill-group` div
   - Add expand/collapse toggle (chevron character) on parent pill when children exist or in edit mode
   - Clicking chevron toggles collapsed state for that group
   - Below parent pill, render a `.sub-pill-row` containing sub-pills from `proc.children`
   - Each sub-pill gets same interaction patterns as parent:
     - Edit mode: delete button on hover, double-click to inline rename, single-click to open sub-pill's explanation panel
     - Present mode: click to highlight that sub-pill's associated teams
   - Add an "Add Sub-step" button (small "+") at the end of the sub-pill-row in edit mode
   - Clicking "Add Sub-step" pushes `{ name: 'New Step', explanation: '', teams: [] }` to `proc.children`

2. Update `showEditExplanationPanel()`:
   - Accept a second parameter `subIndex` (optional). When provided, edit `state.processes[index].children[subIndex]` instead of the parent.
   - Show the sub-pill's name, explanation textarea, and team checklist (same UI pattern as parent)
   - Add an "Add Sub-step" button at the bottom of the parent's explanation panel that appends a new child

3. Update `showPresentExplanationPanel()`:
   - Accept optional `subIndex`. When showing a sub-pill, display its name/explanation and highlight its teams.
   - When showing a parent pill (no subIndex), highlight the parent's teams only (existing behavior).

4. Update `highlightTeamsForPill()` to accept optional `subIndex` parameter:
   - If subIndex provided, use `state.processes[index].children[subIndex].teams`
   - Otherwise use `state.processes[index].teams` (existing behavior)

5. Track selection state: extend `selectedPillIndex` to also track `selectedSubIndex` (null when parent selected). Reset both on mode toggle.

6. Update `deriveConnections()`: iterate both parent and child process teams when building connection set.

7. Update save/load (`JSON.stringify`/parse of state) -- children array already part of state.processes, so verify it serializes and deserializes correctly. The `migrateProcesses` from Task 1 handles legacy data.

8. Update delete-card logic (line ~1306): also remove card ID from `proc.children[].teams` arrays.
  </action>
  <verify>
    In edit mode: add a sub-pill to a parent, rename it, add explanation and team associations, delete it. Toggle collapse. Switch to present mode: click sub-pill, verify only its teams highlight. Click parent pill, verify parent's teams highlight. Save state (JSON export or localStorage), reload, verify nested structure persists.
  </verify>
  <done>
    - Sub-pills render below parent pills with clear visual hierarchy
    - Expand/collapse works on parent pills
    - Edit mode: add, rename, delete sub-pills; edit explanation and teams in panel
    - Present mode: clicking sub-pill highlights its teams; clicking parent highlights parent's teams
    - Data round-trips through save/load without loss
  </done>
</task>

</tasks>

<verification>
1. Open file in browser -- no console errors
2. Existing parent pills work exactly as before (no regression)
3. Add sub-pills to a parent, verify indented display
4. Collapse/expand parent to show/hide sub-pills
5. In present mode, click sub-pill -- only its teams highlight
6. Save and reload -- nested data persists
</verification>

<success_criteria>
- Process pills support a nested hierarchy with sub-process pills
- Sub-pills have independent name, explanation, and team associations
- Both edit and present modes fully support the nested structure
- Visual hierarchy is clear (indented, smaller sub-pills)
- Backward compatible with existing saved data (no children = empty array after migration)
</success_criteria>

<output>
After completion, copy final file to Desktop as the LATEST version.
</output>
