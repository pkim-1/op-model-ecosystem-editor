---
phase: quick
plan: 260504-hiw
subsystem: tpa-ecosystem-editor
tags: [nested-pills, sub-process, hierarchy, editor]
dependency_graph:
  requires: []
  provides: [nested-sub-process-pills]
  affects: [op-model-ecosystem-editor]
tech_stack:
  added: []
  patterns: [expand-collapse-groups, hierarchical-pill-rendering]
key_files:
  created: []
  modified:
    - /Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html
decisions:
  - Sub-pills use same shape {name, explanation, teams} as parents but no further nesting
  - Collapse state tracked in a Set by parent index rather than persisted in state
  - Sub-pill row always visible in edit mode with "+ sub" button for discoverability
metrics:
  duration: 250s
  completed: 2026-05-04
---

# Quick Task 260504-hiw: Add Nested Sub-Process Pills Summary

Nested sub-process pill hierarchy in TPA Ecosystem Editor with independent team highlighting per sub-pill

## Changes Made

### Task 1: Extend data model and migration, add CSS for nested pills
- Added `children: []` array to process data structure (each child: `{name, explanation, teams}`)
- Updated `migrateProcesses()` to add `children` to legacy saved data
- Added `collapsedPills` Set and `selectedSubIndex` tracking variable
- Added CSS for `.process-pill-group`, `.sub-pill-row`, `.sub-pill`, expand/collapse toggle, and present mode styles
- **Commit:** 4b9f691

### Task 2: Render nested pills and wire edit/present mode interactions
- Rewrote `renderProcessPills()` to wrap each parent pill + children in a `.process-pill-group` div
- Added expand/collapse chevron toggle on parent pills (right-arrow when collapsed, down-arrow when expanded)
- Sub-pills support: inline rename (double-click), delete button on hover, click to open explanation panel
- Updated `showEditExplanationPanel(index, subIndex)` to edit sub-pill name/explanation/teams when subIndex provided
- Updated `showPresentExplanationPanel(index, subIndex)` to display sub-pill info with parent context
- Updated `highlightTeamsForPill(index, subIndex)` to use sub-pill's own teams for independent highlighting
- Updated `deriveConnections()` to iterate both parent and child team arrays when building connection set
- Updated `deleteCard()` to remove card ID from `proc.children[].teams` arrays
- Reset `selectedSubIndex` on mode toggle and canvas click deselect
- **Commit:** bf1c66e

## Deviations from Plan

None - plan executed exactly as written.

## Known Stubs

None.

## Self-Check: PASSED
