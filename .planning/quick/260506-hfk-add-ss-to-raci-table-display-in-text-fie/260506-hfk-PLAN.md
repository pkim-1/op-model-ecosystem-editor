---
phase: quick
plan: 260506-hfk
type: execute
wave: 1
depends_on: []
files_modified: [Desktop/op-model-ecosystem-editor-LATEST.html]
autonomous: true
must_haves:
  truths:
    - "SS appears in the legend alongside R, A, C, I"
  artifacts:
    - path: "Desktop/op-model-ecosystem-editor-LATEST.html"
      provides: "RACI legend with SS role included"
      contains: "SS"
  key_links: []
---

<objective>
Add SS (Shared Services) to the RACI legend display in op-model-ecosystem-editor-LATEST.html.

Purpose: The SS role was added as a selectable RACI role (quick-260506-gur) but the static HTML legend at the bottom of the diagram area still only shows R, A, C, I.
Output: Legend updated to include SS with its coral color.
</objective>

<context>
@Desktop/op-model-ecosystem-editor-LATEST.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add SS to the RACI legend</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
In the legend div (around line 904-921), add a new legend-item after the "I -- Informed" entry:

```html
<div class="legend-item">
    <div class="legend-dot" style="background: var(--accent-coral, #FF6B6B);"></div>
    <span>SS — Shared Services</span>
</div>
```

This uses the same color (`var(--accent-coral, #FF6B6B)`) already defined for SS in `RACI_DOT_COLORS` and `RACI_COLORS` throughout the file.
  </action>
  <verify>
    <automated>grep -c "SS.*Shared Services" /Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html | grep -q "[1-9]" && echo "PASS" || echo "FAIL"</automated>
  </verify>
  <done>The legend displays 5 items: R, A, C, I, SS with correct colors and labels.</done>
</task>

</tasks>

<verification>
Open the file in a browser and confirm the legend at the bottom-right of the diagram area shows all 5 RACI roles including SS with a coral-colored dot.
</verification>

<success_criteria>
- Legend contains SS -- Shared Services entry
- SS dot uses var(--accent-coral, #FF6B6B) color
- Visual alignment matches existing legend items
</success_criteria>

<output>
After completion, update STATE.md quick tasks table.
</output>
