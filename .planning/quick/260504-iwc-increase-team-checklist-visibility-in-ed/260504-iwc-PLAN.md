---
phase: quick
plan_id: 260504-iwc
description: Increase team checklist visibility in edit mode explanation panel
created: 2026-05-04
---

# Quick Task 260504-iwc: Increase Team Checklist Visibility

## Goal

Make all team cards visible in the edit mode explanation panel by increasing the team checklist max-height.

## Context

Currently the team checklist in the explanation panel has `max-height: 100px`, which only shows about 3-4 team cards before requiring scrolling. With 24 teams in the default ecosystem, users can't see most of the available teams without scrolling, making it hard to discover which teams exist.

## Tasks

### Task 1: Increase team-checklist max-height

**Files:**
- `/Users/paulkim/Desktop/op-model-ecosystem-editor-LATEST.html`

**Action:**
- Change `.explanation-panel .team-checklist` CSS `max-height` from `100px` to `400px`
- This will show approximately 12-16 team cards before scrolling is needed
- Keeps overflow-y: auto so scrolling is still available if needed

**Verify:**
- [x] CSS line ~570 updated
- [x] Team checklist displays more cards without scrolling
- [x] Panel still scrolls if teams exceed 400px

**Done:** ✓
