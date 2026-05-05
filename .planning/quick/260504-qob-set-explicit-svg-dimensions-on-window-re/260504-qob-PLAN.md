---
phase: quick
plan: 260504-qob
type: execute
wave: 1
depends_on: []
files_modified:
  - Desktop/op-model-ecosystem-editor-LATEST.html
autonomous: true
---

<objective>
Set explicit SVG width and height attributes on window resize to ensure the connections SVG element properly adjusts to the new diagram area dimensions and renders correctly.
</objective>

<context>
The SVG element for connections has CSS width/height set to 100%, but the browser may not properly recalculate the SVG coordinate system when the window is resized, especially when the explanation panel changes width. Setting explicit width/height attributes ensures the SVG viewport matches the diagram area.
</context>

<tasks>

<task type="auto">
  <name>Add explicit SVG dimension setting to renderConnections()</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
In the renderConnections() function (around line 1327), add code to set explicit SVG dimensions at the start of the function:

```javascript
function renderConnections() {
    // Set explicit SVG dimensions to match diagram area
    const diagramRect = diagramArea.getBoundingClientRect();
    svg.setAttribute('width', diagramRect.width);
    svg.setAttribute('height', diagramRect.height);

    // Clear existing paths but keep preview line if any
    svg.querySelectorAll('path').forEach(p => p.remove());
    // ... rest of function
}
```

This ensures the SVG coordinate system is properly sized every time connections are rendered, including after window resize.
  </action>
  <verify>
    <manual>
1. Open the file in a browser
2. Resize the browser window (make it wider and narrower)
3. Verify connection lines render correctly and stay connected to cards
4. Open/close the explanation panel and verify connections adjust
    </manual>
  </verify>
  <done>
    - SVG dimensions explicitly set on every renderConnections() call
    - Connections render correctly after window resize
    - SVG viewport matches diagram area dimensions
  </done>
</task>

</tasks>

<success_criteria>
- Connection lines render correctly after window resize
- SVG coordinate system matches diagram area dimensions
- Connections stay properly attached to cards in all window sizes
</success_criteria>
