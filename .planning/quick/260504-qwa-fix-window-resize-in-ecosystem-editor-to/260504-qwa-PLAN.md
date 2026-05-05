---
phase: quick
plan: 260504-qwa
type: execute
wave: 1
depends_on: []
files_modified: [Desktop/op-model-ecosystem-editor-LATEST.html]
autonomous: true
---

<objective>
Fix window resize in ecosystem editor to scale card and hub positions proportionally, maintaining spatial relationships when container size changes.

Purpose: Currently cards stay at fixed pixel positions on resize, breaking the spatial layout. After this fix, all positions scale proportionally relative to the container.
Output: Updated resize handler that scales all element positions.
</objective>

<context>
@Desktop/op-model-ecosystem-editor-LATEST.html
</context>

<tasks>

<task type="auto">
  <name>Task 1: Add baseline tracking and proportional scaling to resize handler</name>
  <files>Desktop/op-model-ecosystem-editor-LATEST.html</files>
  <action>
1. After the state definition (around line 940), add baseline dimension variables:
   ```javascript
   let baselineDimensions = null; // { width, height } - set when cards first positioned
   ```

2. Create a helper function `captureBaseline()` that reads container dimensions and stores them in `baselineDimensions` if not already set:
   ```javascript
   function captureBaseline() {
       if (!baselineDimensions) {
           const rect = container.getBoundingClientRect();
           baselineDimensions = { width: rect.width, height: rect.height };
       }
   }
   ```
   Call this at end of `init()` and after any card drag/drop completes and after `loadState()`.

3. Replace the resize handler (lines 2598-2618) with proportional scaling logic:
   - If `baselineDimensions` is null, just call `captureBaseline()` and return
   - Get new container dimensions from `container.getBoundingClientRect()`
   - Calculate scaleX = newWidth / baselineDimensions.width, scaleY = newHeight / baselineDimensions.height
   - Scale `state.centerHub.x` and `state.centerHub.y` by the respective factors
   - Scale each `card.x` and `card.y` in `state.cards[]` by the respective factors
   - Update `baselineDimensions` to the new dimensions (so subsequent resizes are relative to current)
   - Keep the existing explanation panel width adjustment
   - Call `renderAll()` (not just renderConnections) inside requestAnimationFrame
   - Keep the 150ms debounce

4. Make sure `renderAll` or equivalent re-renders cards at their new positions (it already calls createCardElement which reads card.x/card.y, so this should work if cards are re-rendered).
  </action>
  <verify>
    Open the file in a browser, add some cards, resize the window. Cards and hub should maintain their relative spatial arrangement (proportional positions) rather than staying at fixed pixel coordinates.
  </verify>
  <done>Window resize scales all card and hub positions proportionally. Spatial relationships between elements are preserved across resize events.</done>
</task>

</tasks>

<verification>
- Open editor, position cards around hub
- Resize browser window smaller: cards move inward proportionally
- Resize browser window larger: cards spread out proportionally
- Hub stays proportionally centered
- No cards jump to unexpected positions
- Drag a card after resize: positions remain correct
</verification>

<success_criteria>
Card and hub positions scale proportionally on window resize, maintaining spatial relationships.
</success_criteria>

<output>
After completion, the file Desktop/op-model-ecosystem-editor-LATEST.html has proportional resize behavior.
</output>
