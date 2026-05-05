---
phase: quick
plan: 260504-qh5
status: complete
tasks_completed: 1
tasks_total: 1
commit: b8d5d2b
date: 2026-05-05
---

# Quick Task 260504-qh5 Summary

**Goal:** Fix hub drag offset calculation to prevent jump when starting to drag

## What Changed

Fixed the hub (yellow circle) drag behavior by changing the offset calculation in the mousedown handler to use the same coordinate system as the mousemove handler.

**Root cause:** The offset was calculated using `hub.getBoundingClientRect()` which gave screen coordinates, but the hub position is stored in `state.centerHub.x/y` which are canvas coordinates. This mismatch caused the hub to jump to a different location when dragging started.

**Solution:** Changed the offset calculation to use `state.centerHub` coordinates with container offset and zoom level, matching exactly how the position is updated during mousemove.

## Tasks Completed

✓ **Task 1**: Fix hub drag offset calculation
- Changed offset calculation from bounding rect to state coordinates
- Now uses `(e.clientX - containerRect.left) / zoomLevel - state.centerHub.x`
- Consistent with mousemove handler coordinate transformation

## Commits

- `b8d5d2b` - fix(quick-260504-qh5): fix hub drag offset calculation to prevent jump when starting to drag

## Verification

The hub now stays exactly where it is when you first click on it, and follows the cursor smoothly without any initial jump.

## Files Modified

- `Desktop/op-model-ecosystem-editor-LATEST.html` (3 lines changed)
