---
phase: quick
plan: 260505-mtx
type: execute
wave: 1
depends_on: []
files_modified: [Desktop/op-model-ecosystem-editor-LATEST.html]
autonomous: true
must_haves:
  truths:
    - "Hub remains centered in container after window resize"
    - "Cards maintain proportional distances from hub center after resize"
  artifacts:
    - path: "Desktop/op-model-ecosystem-editor-LATEST.html"
      provides: "Fixed resize handler with center-relative scaling"
  key_links:
    - from: "resize handler"
      to: "state.centerHub and state.cards positions"
      via: "center-relative offset scaling"
      pattern: "newCenter.*offset.*scale"
---

<objective>
Fix diagram centering on window resize by keeping the hub centered and scaling card distances from center rather than scaling absolute positions.

Purpose: The current proportional scaling drifts the diagram off-center because it scales absolute x/y positions. The fix calculates offsets from hub, scales those, then repositions relative to the new center.
Output: Updated resize handler in the ecosystem editor HTML file.
</objective>

<execution_context>
@/Users/paulkim/.claude/get-shit-done/workflows/execute-plan.md
@/Users/paulkim/.claude/get-shit-done/templates/summary.md
</execution_context>

<context>
@Desktop/op-model-ecosystem-editor-LATEST.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Fix resize handler to use center-relative scaling</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
In the resize handler (around lines 2637-2660), replace the current scaling logic that multiplies absolute positions by scaleX/scaleY with center-relative scaling:

1. Calculate the new center: newCenterX = newRect.width / 2, newCenterY = newRect.height / 2
2. Before updating hub position, iterate cards and calculate each card's offset from the current hub (card.x - state.centerHub.x, card.y - state.centerHub.y)
3. Scale those offsets by scaleX/scaleY
4. Reposition each card at newCenter + scaled offset
5. Set state.centerHub.x = newCenterX, state.centerHub.y = newCenterY

Replace:
```javascript
state.centerHub.x *= scaleX;
state.centerHub.y *= scaleY;
state.cards.forEach(card => {
    card.x *= scaleX;
    card.y *= scaleY;
});
```

With:
```javascript
const newCenterX = newRect.width / 2;
const newCenterY = newRect.height / 2;

state.cards.forEach(card => {
    const oldOffsetX = card.x - state.centerHub.x;
    const oldOffsetY = card.y - state.centerHub.y;
    card.x = newCenterX + (oldOffsetX * scaleX);
    card.y = newCenterY + (oldOffsetY * scaleY);
});

state.centerHub.x = newCenterX;
state.centerHub.y = newCenterY;
```

Also update baselineDimensions to newRect after applying the transformation.
  </action>
  <verify>
    <automated>grep -n "newCenterX\|newCenterY\|oldOffsetX\|oldOffsetY" Desktop/op-model-ecosystem-editor-LATEST.html | head -10</automated>
  </verify>
  <done>Resize handler uses center-relative offset scaling. Hub is always repositioned to container center. Cards maintain proportional distances from hub.</done>
</task>

</tasks>

<verification>
- Open the HTML file in a browser
- Resize the window - hub should remain centered
- Cards should maintain their relative positions around the hub
- No drift on repeated resizes
</verification>

<success_criteria>
Hub stays centered in container on resize. Cards scale their distance from hub proportionally without absolute position drift.
</success_criteria>

<output>
After completion, create `.planning/quick/260505-mtx-fix-diagram-centering-on-window-resize-k/260505-mtx-SUMMARY.md`
</output>
