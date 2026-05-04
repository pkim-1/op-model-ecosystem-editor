---
phase: quick
plan: 260504-qcl
type: execute
wave: 1
depends_on: []
files_modified:
  - Desktop/op-model-ecosystem-editor-LATEST.html
autonomous: true
---

<objective>
Remove the CSS max-width: 400px constraint on the explanation panel to allow it to expand to the full 1/3 window width as intended by the JavaScript resize logic.
</objective>

<context>
The previous task (260504-q3f) updated the JavaScript to allow the panel to resize up to 1/3 of the window width (containerRect.width * 0.333), but the CSS still has max-width: 400px which prevents the panel from expanding beyond 400px even on larger screens.
</context>

<tasks>

<task type="auto">
  <name>Remove max-width constraint from .explanation-panel CSS</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
Remove the line `max-width: 400px;` from the .explanation-panel CSS rule (around line 503).
  </action>
  <verify>
    <manual>Open the file in a browser and drag the explanation panel resize handle to verify it can now expand beyond 400px up to 1/3 of the window width</manual>
  </verify>
  <done>
    - max-width constraint removed
    - Panel can now resize to full 1/3 window width
  </done>
</task>

</tasks>

<success_criteria>
- Panel can be resized up to 1/3 of the viewport width on large screens
- No CSS constraint blocking the JavaScript resize logic
</success_criteria>
