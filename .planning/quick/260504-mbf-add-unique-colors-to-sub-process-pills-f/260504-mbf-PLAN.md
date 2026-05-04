---
phase: quick
plan: 260504-mbf
type: execute
wave: 1
depends_on: []
files_modified: ["/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html"]
autonomous: true
must_haves:
  truths:
    - "Each sub-pill within a parent process has a unique background color"
    - "Colors cycle through a defined palette based on sub-pill index"
    - "Active/selected sub-pill still shows yellow border highlight"
    - "Present mode highlighting still works on colored sub-pills"
    - "Colors persist through save/load (derived from index, not stored)"
  artifacts:
    - path: "/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html"
      provides: "Sub-pill color palette and rendering"
      contains: "sub-pill-color"
  key_links:
    - from: "renderProcessPills forEach loop"
      to: "sub-pill inline style"
      via: "subIdx modulo color palette length"
      pattern: "subPillColors\\[subIdx"
---

<objective>
Add unique background colors to sub-process pills so each sub-step within a parent process is visually distinct.

Purpose: Improve visual scanning -- users can quickly differentiate sub-steps at a glance instead of reading every label.
Output: Updated HTML file with colored sub-pills that complement the Expedia brand palette.
</objective>

<execution_context>
@/Users/paulkim/.claude/get-shit-done/workflows/execute-plan.md
@/Users/paulkim/.claude/get-shit-done/templates/summary.md
</execution_context>

<context>
@/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add sub-pill color palette and apply colors by index</name>
  <files>/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
1. Define a CSS custom property set for sub-pill colors in the :root block. Use 8 colors that complement the existing brand palette (dark background context). Suggested palette using semi-transparent backgrounds for readability:
   - --sub-pill-1: rgba(66, 72, 237, 0.25)   (pacific blue tint)
   - --sub-pill-2: rgba(155, 81, 224, 0.25)   (purple tint)
   - --sub-pill-3: rgba(0, 217, 165, 0.25)    (green tint)
   - --sub-pill-4: rgba(255, 184, 0, 0.2)     (yellow tint)
   - --sub-pill-5: rgba(255, 107, 107, 0.25)  (coral tint)
   - --sub-pill-6: rgba(72, 199, 235, 0.25)   (cyan tint)
   - --sub-pill-7: rgba(255, 159, 67, 0.25)   (orange tint)
   - --sub-pill-8: rgba(162, 217, 80, 0.25)   (lime tint)

2. Define matching border colors (slightly more opaque) for each:
   - --sub-pill-border-1: rgba(66, 72, 237, 0.5)
   - --sub-pill-border-2: rgba(155, 81, 224, 0.5)
   - --sub-pill-border-3: rgba(0, 217, 165, 0.5)
   - --sub-pill-border-4: rgba(255, 184, 0, 0.4)
   - --sub-pill-border-5: rgba(255, 107, 107, 0.5)
   - --sub-pill-border-6: rgba(72, 199, 235, 0.5)
   - --sub-pill-border-7: rgba(255, 159, 67, 0.5)
   - --sub-pill-border-8: rgba(162, 217, 80, 0.5)

3. In the JavaScript, define a `subPillColors` array of objects with `bg` and `border` keys (referencing the CSS vars or inline values). Place this near the top of the script section with other constants.

4. In `renderProcessPills()`, inside the `proc.children.forEach((child, subIdx)` loop (around line 1736), after creating the subPill element, apply the color:
   ```javascript
   const colorIdx = subIdx % subPillColors.length;
   subPill.style.background = subPillColors[colorIdx].bg;
   subPill.style.borderColor = subPillColors[colorIdx].border;
   ```

5. Update the `.sub-pill` base CSS to remove the static `background: var(--card-bg)` and `border: 1px solid rgba(58, 63, 82, 0.7)` -- keep only layout/sizing properties. The border width and style should remain (`border: 1px solid transparent`) so the inline colors apply correctly.

6. Ensure `.sub-pill.active-pill` styles still override with yellow (uses `!important` or higher specificity via inline style removal on active state). The active-pill class already uses `border-color: var(--accent-yellow)` and `background: rgba(255, 184, 0, 0.15)` -- these should win. Apply active state via inline override in JS: when `active-pill` class is present, do NOT apply the color inline styles (wrap the color assignment in a condition checking `selectedPillIndex === index && selectedSubIndex === subIdx`).

7. Ensure `.sub-pill.present-clickable:hover` still shows the yellow hover effect. Since hover is CSS-based and uses `border-color: var(--accent-yellow)`, the inline `style.borderColor` will override it. Fix: use CSS classes instead of inline styles. Add classes `.sub-pill-color-0` through `.sub-pill-color-7` with the background/border colors, and apply the class via `subPill.classList.add('sub-pill-color-' + colorIdx)`. Then the `.active-pill` and `:hover` rules with higher specificity will naturally override.

Final approach (CSS classes, not inline styles):
- Add 8 CSS rules: `.sub-pill-color-0 { background: rgba(66,72,237,0.25); border-color: rgba(66,72,237,0.5); }` etc.
- In JS: `subPill.classList.add('sub-pill-color-' + (subIdx % 8));`
- `.sub-pill.active-pill` keeps its existing styles (will override due to specificity)
- `.sub-pill.present-clickable:hover` keeps its existing styles (will override due to combined selector specificity)
  </action>
  <verify>
    <automated>grep -c "sub-pill-color-" /Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html | xargs test 8 -le</automated>
  </verify>
  <done>Sub-pills display unique colors cycling through an 8-color palette. Active/selected pills still show yellow highlight. Present mode hover still works. Colors are deterministic from index (no storage needed).</done>
</task>

<task type="checkpoint:human-verify" gate="blocking">
  <what-built>Sub-process pills now each have a unique color from an 8-color palette that cycles based on their position index within the parent process.</what-built>
  <how-to-verify>
    1. Open /Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html in browser
    2. In edit mode, observe that sub-pills under a parent process each have different background tints
    3. Click a sub-pill -- it should show yellow active highlight (overriding the color)
    4. Switch to present mode -- hover on sub-pills should still show yellow hover
    5. Click a sub-pill in present mode -- team highlighting (green glow) should still work
    6. Save and reload -- colors should reappear in the same pattern
  </how-to-verify>
  <resume-signal>Type "approved" or describe issues</resume-signal>
</task>

</tasks>

<verification>
- Each sub-pill has a distinct background color within its parent group
- Active/selected state (yellow) overrides the color
- Present mode interactions unaffected
- Colors are deterministic (same pill always gets same color)
</verification>

<success_criteria>
Sub-process pills are visually distinct through unique cycling colors, maintaining all existing interaction patterns (selection, editing, present mode).
</success_criteria>

<output>
After completion, create `.planning/quick/260504-mbf-add-unique-colors-to-sub-process-pills-f/260504-mbf-SUMMARY.md`
</output>
