---
phase: quick
plan: 260505-n1b
subsystem: ecosystem-editor
tags: [bug-fix, drag-interaction, zoom]
key-files:
  modified:
    - Desktop/op-model-ecosystem-editor-LATEST.html
decisions:
  - "Only fixed hub drag lines; card drag uses screen-space offset where the division is correct"
metrics:
  tasks_completed: 1
  tasks_total: 1
  files_modified: 1
---

# Quick Task 260505-n1b: Fix Hub Drag Offset Calculation Summary

**One-liner:** Remove double zoomLevel division in hub mousemove handler that caused jump-on-grab

## What Was Done

Removed the second `/ zoomLevel` division from the hub drag mousemove handler (lines 1521-1522). The `dragOffset` is already computed in world-space coordinates in the mousedown handler (`mouseWorld - hubPosition`), so dividing it by zoomLevel again in mousemove produced incorrect positioning causing the hub to jump off screen when grabbed.

## Changes

| File | Change |
|------|--------|
| Desktop/op-model-ecosystem-editor-LATEST.html | Lines 1521-1522: `- dragOffset.x / zoomLevel` changed to `- dragOffset.x` (same for y) |

## Commits

| Task | Commit | Description |
|------|--------|-------------|
| 1 | c45eb45 | fix(quick-260505-n1b): remove double zoom division from hub drag offset calculation |

## Deviations from Plan

None - plan executed exactly as written.

## Notes

The verification command in the plan (`grep "dragOffset.x / zoomLevel"`) still finds matches on lines 1513-1514 which are for card dragging. The card drag uses a screen-space dragOffset (`clientX - rect.left`) so the division there is correct and intentional. Only the hub drag (lines 1521-1522) had the bug.

## Self-Check: PASSED
