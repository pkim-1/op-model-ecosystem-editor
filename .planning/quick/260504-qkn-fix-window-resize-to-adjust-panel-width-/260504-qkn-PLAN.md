---
phase: quick
plan: 260504-qkn
type: execute
wave: 1
depends_on: []
files_modified:
  - Desktop/op-model-ecosystem-editor-LATEST.html
autonomous: true
---

<objective>
Fix the window resize handler to properly adjust the explanation panel width when the window is resized, ensuring it doesn't exceed the 1/3 maximum width constraint and that connections are redrawn correctly.
</objective>

<context>
The window resize handler currently only calls renderConnections() but doesn't check if the explanation panel's fixed pixel width (set by manual dragging) now exceeds the new maximum after a window resize. This can cause layout issues.

When the user resizes the browser window smaller, the panel might exceed 1/3 of the new window width.
</context>

<tasks>

<task type="auto">
  <name>Add panel width constraint check to resize handler</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
In the window resize event handler (around line 2593), add logic to check and adjust the panel width before redrawing connections:

```javascript
// Adjust panel width if it exceeds the new maximum
if (explanationPanel.classList.contains('visible')) {
    const containerRect = container.getBoundingClientRect();
    const maxWidth = containerRect.width * 0.333;
    const currentWidth = explanationPanel.offsetWidth;
    if (currentWidth > maxWidth) {
        explanationPanel.style.width = maxWidth + 'px';
    }
}
renderConnections();
```

This ensures the panel respects the 1/3 maximum width constraint after window resize.
  </action>
  <verify>
    <manual>
1. Open the file in a browser
2. Drag the panel to near 1/3 width
3. Make the browser window smaller
4. Verify the panel shrinks to stay within 1/3 of the new window width
5. Verify connections redraw correctly
    </manual>
  </verify>
  <done>
    - Panel width is checked and constrained on window resize
    - Connections redraw properly after resize
    - Layout remains consistent in both edit and present modes
  </done>
</task>

</tasks>

<success_criteria>
- Panel width automatically adjusts when window is resized smaller
- Panel respects 1/3 maximum width constraint after resize
- Connections are properly redrawn to match new layout
- Works in both edit and present modes
</success_criteria>
