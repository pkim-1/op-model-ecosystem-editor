---
phase: 03
slug: visual-type-indicators
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-04-14
---

# Phase 03 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | none — manual browser verification (static HTML project) |
| **Config file** | none — Wave 0 installs |
| **Quick run command** | `open index.html` |
| **Full suite command** | `open index.html` |
| **Estimated runtime** | ~10 seconds (manual visual inspection) |

---

## Sampling Rate

- **After every task commit:** Open `index.html` in browser, verify badge rendering
- **After every plan wave:** Full visual verification across all project types
- **Before `/gsd-verify-work`:** All badge variations must render correctly
- **Max feedback latency:** 10 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Threat Ref | Secure Behavior | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|------------|-----------------|-----------|-------------------|-------------|--------|
| TBD | TBD | TBD | UI-01 | — | N/A | manual | `open index.html` | ✅ | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

Existing infrastructure covers all phase requirements.

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Type badge displays correct color for each type | UI-01 | Visual verification of color-coded badges | 1. Open index.html 2. Verify Tool badges are Pacific Blue 3. Verify Analysis badges are Dark Orange 4. Verify Visualization badges are Purple |
| Badge placement doesn't overlap title | UI-01 | Visual verification of layout | 1. Open index.html 2. Verify badges appear in top-left corner 3. Verify no overlap with card title |
| Badge derives type from GitHub topics | UI-01 | Data-driven verification | 1. Open index.html 2. Inspect repos with "tool" topics → should show Tool badge 3. Inspect repos with "analysis" topics → should show Analysis badge 4. Inspect repos with "visualization" topics → should show Visualization badge |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 10s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
