---
phase: quick
plan: 260505-mtx
status: complete
commit: 19b18b1
date: 2026-05-05
---

# Quick Task 260505-mtx Summary

**Task:** Fix diagram centering on window resize - keep hub centered and scale card distances from center

## Completed

### Task 1: Replace absolute position scaling with center-relative scaling

**Changes made:**
- Changed resize handler to always center the hub at container center (width/2, height/2)
- Calculate each card's offset from the hub before scaling
- Apply scale factors to offsets, not absolute positions
- Reposition cards based on scaled offsets from the newly centered hub
- This keeps the diagram visually centered as the window resizes

**Previous behavior:**
- Hub and cards were scaled by multiplying their absolute positions (x, y) by scaleX/scaleY
- This caused the entire diagram to drift off-center when window resized

**New behavior:**
- Hub stays at the center of the container
- Cards maintain their relative distances from the hub, scaled proportionally
- Diagram remains centered regardless of window size

**Files modified:**
- Desktop/op-model-ecosystem-editor-LATEST.html

**Verification:**
The hub (yellow circle) now stays centered on the screen, and cards scale their distances from the center proportionally when the window resizes.

**Commit:** 19b18b1

## Outcome

Window resize now keeps the diagram centered with the hub in the middle of the screen. Card positions scale their distances from the center rather than their absolute positions.
