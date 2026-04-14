# Phase 3: Visual Type Indicators - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-04-14
**Phase:** 03-visual-type-indicators
**Areas discussed:** Badge placement, Type detection, Type categories, Badge style
**Mode:** Auto-selected (--auto flag)

---

## Badge Placement & Visual Hierarchy

| Option | Description | Selected |
|--------|-------------|----------|
| Top-left corner | Most prominent, doesn't interfere with title | ✓ |
| Next to card title | Integrated with name, visible | |
| With other metadata | Grouped with topics/language | |

**Auto-selected choice:** Top-left corner
**Rationale:** Most scannable, doesn't compete with title, common pattern for type/status badges

---

## Type Detection Logic

| Option | Description | Selected |
|--------|-------------|----------|
| Keyword mapping | e.g., "analysis" → Analysis, "dashboard" → Tool | ✓ |
| First topic wins | Simplest, user controls via topic order | |
| Combined logic | Keywords + fallback rules | |

**Auto-selected choice:** Keyword mapping
**Rationale:** Explicit rules, predictable, handles common cases like "jupyter-notebook" → Analysis

---

## Type Categories

| Option | Description | Selected |
|--------|-------------|----------|
| Core 3: Analysis, Tool, Visualization | Covers stated use cases | ✓ |
| Extended 5: add Research, Documentation | More granular | |
| Minimal 2: Technical, Analysis | Broad buckets | |

**Auto-selected choice:** Core 3 (Analysis, Tool, Visualization)
**Rationale:** Matches the stated project types in PROJECT.md context section

---

## Color Coding

| Option | Description | Selected |
|--------|-------------|----------|
| Semantic colors | Blue for tools, orange for analysis, purple for viz | ✓ |
| Monochrome variations | Shades of brand blue | |
| High-contrast palette | Distinct hues for quick scanning | |

**Auto-selected choice:** Semantic colors
**Rationale:** Blue/teal for Tool matches existing language badge, orange for Analysis, purple for Visualization provides clear visual distinction

---

## Badge Style

| Option | Description | Selected |
|--------|-------------|----------|
| Text-only pill | Like language badge, consistent | ✓ |
| Icon + text | More visual, requires icon set | |
| Color bar only | Minimal, relies on color memory | |

**Auto-selected choice:** Text-only pill
**Rationale:** Matches existing language badge pattern, readable, no icon dependencies for static HTML

---

## Claude's Discretion

- Badge positioning offset from card corner
- Badge shadow strength
- Transition effects
- Additional keywords for type matching

## Deferred Ideas

None — phase scope remained focused on visual type indicators
