# Phase 3: Visual Type Indicators - Research

**Researched:** 2026-04-14
**Domain:** CSS badges, DOM manipulation, keyword-based classification
**Confidence:** HIGH

## Summary

Phase 3 adds color-coded type badges (Analysis, Tool, Visualization) to project cards. Types are derived from GitHub topics using keyword mapping. The implementation is entirely client-side: a classification function scans each repo's topics array, and a CSS-styled badge is injected into the card markup during rendering.

This is a contained, low-risk phase. All decisions are locked (badge placement, colors, keywords, styling). The existing codebase already has a pill badge pattern (`.language-badge`) that serves as the template. The primary work is: (1) a `detectType()` function, (2) CSS rules for three color variants, and (3) a small modification to `renderCard()`.

**Primary recommendation:** Extend the existing `renderCard()` function to call a new `detectType(repo)` helper. Add `.type-badge` CSS with modifier classes for each type. Follow the locked decisions exactly -- no design exploration needed.

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **D-01:** Type badge appears in top-left corner of card (absolute positioned)
- **D-02:** Badge floats above card content with subtle shadow for visibility
- **D-03:** Badge does not interfere with title or card click target
- **D-04:** Keyword mapping for type detection:
  - **Analysis**: "analysis", "data-science", "jupyter", "research", "statistics", "analytics"
  - **Tool**: "tool", "cli", "library", "framework", "utility", "automation", "dashboard"
  - **Visualization**: "visualization", "dashboard", "chart", "graph", "plot", "dataviz"
- **D-05:** First matching topic wins (scan left to right)
- **D-06:** No match = no badge (not all projects need classification)
- **D-07:** Case-insensitive matching
- **D-08:** 3 core types: Analysis, Tool, Visualization
- **D-09:** Colors: Tool=#4248ED (Pacific Blue), Analysis=#FF8C00 (Dark Orange), Visualization=#9B59B6 (Purple)
- **D-10:** White text on all badges
- **D-11:** Text-only pill badge (matches `.language-badge` pattern)
- **D-12:** border-radius: 12px, uppercase, font-size: 0.7rem, padding: 3px 10px
- **D-13:** No icons

### Claude's Discretion
- Exact badge positioning offset from card corner
- Badge shadow strength (subtle, readable)
- Transition effects when cards render with badges (performant)
- Additional keywords for type matching beyond core D-04 set

### Deferred Ideas (OUT OF SCOPE)
None
</user_constraints>

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|------------------|
| UI-01 | Projects display visual type indicators (color-coded badges for analysis/tool/visualization) | Full support -- keyword detection from topics, CSS badge variants, card markup integration |
</phase_requirements>

## Architectural Responsibility Map

| Capability | Primary Tier | Secondary Tier | Rationale |
|------------|-------------|----------------|-----------|
| Type detection logic | Browser / Client | -- | Runs in-browser against `window.allRepos` topics array during card rendering |
| Badge rendering | Browser / Client | -- | DOM element creation in `renderCard()` function |
| Badge styling | Browser / Client | -- | CSS rules in `<style>` block of single HTML file |
| Keyword configuration | Browser / Client | -- | Hardcoded mapping object in `<script>` block (no server, no config file) |

## Standard Stack

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| Vanilla CSS | Current spec | Badge styling with color variants | Already established in phases 1-2. CSS custom properties + modifier classes handle the three badge colors. No preprocessor needed. |
| Vanilla JavaScript (ES2022+) | Current spec | Type detection and badge DOM creation | Already established. Simple keyword matching + `document.createElement` -- no library warranted. |

### Supporting
None required. This phase uses zero external dependencies.

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Hardcoded keyword map | Configurable mapping via UI | Over-engineering for 3 types with stable keywords. Defer to v2 if needed. |
| CSS modifier classes | data-attribute selectors | Modifier classes (`.type-badge.tool`) are more readable and match existing patterns. |

## Architecture Patterns

### System Architecture Diagram

```
window.allRepos (in-memory)
    |
    v
renderCard(repo)
    |
    +---> detectType(repo.topics)
    |         |
    |         v
    |     TYPE_KEYWORDS map
    |         |
    |         v
    |     matched type or null
    |
    +---> if type: create .type-badge element
    |         |
    |         v
    |     badge positioned absolute in .card (position: relative)
    |
    +---> rest of card (name, description, meta)
    |
    v
DOM fragment --> #project-grid
```

### Recommended Changes (within index.html)

```
index.html (single file)
  <style>
    + .card { position: relative; }     /* enable absolute badge positioning */
    + .type-badge { ... }               /* base badge style */
    + .type-badge.tool { ... }          /* Pacific Blue */
    + .type-badge.analysis { ... }      /* Dark Orange */
    + .type-badge.visualization { ... } /* Purple */
  </style>
  <script>
    + TYPE_KEYWORDS map                  /* keyword -> type mapping */
    + detectType(topics) function        /* returns type string or null */
    ~ renderCard(repo) modified          /* adds badge if type detected */
  </script>
```

### Pattern 1: Type Detection via Keyword Mapping
**What:** A pure function that takes a topics array and returns a type string or null.
**When to use:** Called once per card during `renderCard()`.
**Example:**
```javascript
// Source: Phase 3 CONTEXT.md D-04 through D-07
const TYPE_KEYWORDS = {
  analysis: ['analysis', 'data-science', 'jupyter', 'research', 'statistics', 'analytics'],
  tool: ['tool', 'cli', 'library', 'framework', 'utility', 'automation', 'dashboard'],
  visualization: ['visualization', 'dashboard', 'chart', 'graph', 'plot', 'dataviz'],
};

function detectType(topics) {
  if (!topics || topics.length === 0) return null;
  for (const topic of topics) {
    const lower = topic.toLowerCase();
    for (const [type, keywords] of Object.entries(TYPE_KEYWORDS)) {
      if (keywords.some(kw => lower.includes(kw))) {
        return type;
      }
    }
  }
  return null;
}
```

### Pattern 2: Badge DOM Creation
**What:** Creates a positioned badge element and prepends it to the card.
**When to use:** Inside `renderCard()` when `detectType()` returns a non-null value.
**Example:**
```javascript
// Source: Phase 3 CONTEXT.md D-01, D-02, D-10, D-11, D-12
const type = detectType(repo.topics);
if (type) {
  const badge = document.createElement('span');
  badge.className = `type-badge ${type}`;
  badge.textContent = type.charAt(0).toUpperCase() + type.slice(1);
  card.appendChild(badge);
}
```

### Pattern 3: CSS Badge Styling with Modifier Classes
**What:** Base `.type-badge` class with `.tool`, `.analysis`, `.visualization` modifiers.
**When to use:** Styling the badge per locked decisions.
**Example:**
```css
/* Source: Phase 3 CONTEXT.md D-09, D-10, D-11, D-12 */
.card {
  position: relative; /* enable absolute badge positioning */
}

.type-badge {
  position: absolute;
  top: 10px;
  left: 10px;
  display: inline-block;
  color: #FFFFFF;
  padding: 3px 10px;
  border-radius: 12px;
  font-size: 0.7rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.03em;
  box-shadow: 0 1px 3px rgba(0,0,0,0.15);
  z-index: 1;
}

.type-badge.tool { background: #4248ED; }
.type-badge.analysis { background: #FF8C00; }
.type-badge.visualization { background: #9B59B6; }
```

### Anti-Patterns to Avoid
- **Adding badge outside card element:** Badge must be a child of `.card` for absolute positioning to work relative to the card. Do not place it as a sibling.
- **Using `innerHTML` to inject badge:** The existing pattern uses `document.createElement`. Stay consistent -- `innerHTML` risks XSS if topic data is malformed.
- **Scanning keywords with exact match only:** D-04 keywords like "data-science" must match via `includes()`, not `===`, because topics may contain compound strings.

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Color contrast checking | Manual contrast calculation | Pre-verified color pairs from D-09/D-10 | White text on Pacific Blue, Dark Orange, and Purple all pass WCAG AA contrast. Already decided. [ASSUMED] |

**Key insight:** This phase has no complex problems that need library solutions. Everything is achievable with native CSS and JS.

## Common Pitfalls

### Pitfall 1: "dashboard" Keyword Collision
**What goes wrong:** "dashboard" appears in both Tool and Visualization keyword lists (D-04). A repo with topic "dashboard" could match either.
**Why it happens:** The keyword lists overlap by design -- some topics genuinely span categories.
**How to avoid:** D-05 specifies "first matching topic wins, scan left to right." The TYPE_KEYWORDS iteration order determines priority. Place the desired priority type first in the object iteration. Note: the outer loop is over topics (left to right), inner loop over types. Ensure consistent type priority by iterating types in a fixed order.
**Warning signs:** A "dashboard" repo classified as the wrong type.

### Pitfall 2: Card Layout Shift from Absolute Badge
**What goes wrong:** Absolute-positioned badge overlaps card title text if title is long or card is narrow.
**Why it happens:** Badge sits at top-left, title also starts at top of card.
**How to avoid:** Add `padding-top` to card content (or title specifically) to leave room for the badge. Only needed on cards that have a badge -- use a CSS class like `.card.has-type-badge` or apply padding conditionally in JS.
**Warning signs:** Badge obscures first line of repo name on mobile or narrow cards.

### Pitfall 3: `position: relative` Override on `.card`
**What goes wrong:** Adding `position: relative` to `.card` may affect existing hover transforms (`translateY(-2px)`) or box-shadow stacking.
**Why it happens:** The card already has `transition: all 0.2s ease` with transform on hover.
**How to avoid:** Test that hover still works correctly after adding `position: relative`. In practice, `position: relative` and `transform` coexist fine -- `transform` creates a new stacking context regardless.
**Warning signs:** Badge shifts or flickers during card hover animation.

### Pitfall 4: Keyword Matching Too Greedy
**What goes wrong:** `includes('graph')` matches "graph" but also "photography" or "paragraph" if such topics existed.
**Why it happens:** Substring matching on short keywords.
**How to avoid:** GitHub topics use hyphens, not spaces (e.g., "data-science", not "data science"). The risk is low for actual GitHub topic strings. For safety, match on the full topic string containing the keyword as a word boundary or exact segment. However, D-04 keywords are specific enough that false positives are unlikely in practice.
**Warning signs:** Unexpected type classifications on repos with unusual topics.

## Code Examples

### Complete renderCard Modification
```javascript
// Source: Existing index.html renderCard() + Phase 3 CONTEXT.md decisions
function renderCard(repo) {
  const card = document.createElement('article');
  card.className = 'project-card card';
  // ... existing click handler, keyboard handler ...

  // Type badge (Phase 3)
  const type = detectType(repo.topics);
  if (type) {
    const badge = document.createElement('span');
    badge.className = `type-badge ${type}`;
    badge.textContent = type.charAt(0).toUpperCase() + type.slice(1);
    card.appendChild(badge);
  }

  const name = document.createElement('h3');
  name.className = 'card-name';
  // Add top padding when badge present to avoid overlap
  if (type) {
    name.style.paddingTop = '24px';
  }
  name.textContent = repo.name;
  card.appendChild(name);

  // ... rest of existing card rendering ...
  return card;
}
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Icon fonts for badges | Text-only pill badges | N/A (design decision) | Simpler, no font dependency, matches existing `.language-badge` |
| Framework-based state | Vanilla JS keyword map | N/A (project constraint) | Zero dependencies, fast, maintainable |

## Assumptions Log

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| A1 | White text on #FF8C00 (Dark Orange) and #9B59B6 (Purple) passes WCAG AA contrast | Don't Hand-Roll | LOW -- if contrast fails, adjust badge text weight or add text-shadow. Can be verified with contrast checker during implementation. |
| A2 | GitHub topic strings won't produce false positives with `includes()` matching | Pitfalls | LOW -- GitHub topics are developer-chosen, short, hyphenated strings. Unlikely to contain accidental substring matches for these specific keywords. |

## Open Questions

1. **Type priority when "dashboard" matches both Tool and Visualization**
   - What we know: D-05 says "first matching topic wins, scan left to right" -- this means the outer loop is over topics, not types. If a repo's first topic is "dashboard," it will match whichever type is checked first in the inner loop.
   - What's unclear: The intended priority order of types when keywords overlap.
   - Recommendation: Use the order from D-04 (Analysis first, Tool second, Visualization third). This means "dashboard" matches Tool before Visualization, which seems intentionally correct since Tool is listed first.

## Validation Architecture

### Test Framework
| Property | Value |
|----------|-------|
| Framework | Manual browser testing (no JS test framework in project) |
| Config file | none |
| Quick run command | Open `index.html` in browser, inspect cards |
| Full suite command | N/A (single HTML file, no automated tests) |

### Phase Requirements to Test Map
| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| UI-01 | Color-coded type badges visible on cards with matching topics | manual | Open index.html, verify badges appear with correct colors | N/A |
| UI-01a | detectType returns correct type for known keywords | unit (potential) | Console: `detectType(['jupyter', 'analysis'])` should return 'analysis' | Wave 0 |
| UI-01b | No badge on repos without matching topics | manual | Find a repo without analysis/tool/viz topics, verify no badge | N/A |
| UI-01c | Badge does not overlap title | manual | Check card layout on desktop and mobile viewport | N/A |

### Sampling Rate
- **Per task commit:** Open index.html, visually verify badges
- **Per wave merge:** Full visual check across card types + responsive breakpoint
- **Phase gate:** All three badge colors visible, no layout overlap, keyword matching correct

### Wave 0 Gaps
- [ ] Consider adding `detectType()` unit tests callable from browser console for quick validation
- [ ] No test framework to install -- manual testing is appropriate for a single HTML file project

## Security Domain

### Applicable ASVS Categories

| ASVS Category | Applies | Standard Control |
|---------------|---------|-----------------|
| V2 Authentication | no | -- |
| V3 Session Management | no | -- |
| V4 Access Control | no | -- |
| V5 Input Validation | yes | Topic strings from GitHub API are rendered via `textContent` (not `innerHTML`), preventing XSS. Existing pattern is safe. |
| V6 Cryptography | no | -- |

### Known Threat Patterns

| Pattern | STRIDE | Standard Mitigation |
|---------|--------|---------------------|
| XSS via topic strings | Tampering | Use `document.createElement` + `textContent` (already established pattern in codebase) |

No new attack surface introduced. Badge content is derived from topic strings already displayed as `.topic-chip` elements using safe DOM APIs.

## Sources

### Primary (HIGH confidence)
- `index.html` -- Current implementation reviewed: card structure, CSS patterns, renderCard function, existing `.language-badge` styling
- Phase 3 CONTEXT.md -- All 13 locked decisions with exact specifications
- Phase 1 CONTEXT.md -- Established card layout, brand colors, DOM patterns
- Phase 2 CONTEXT.md -- Filter chip pattern, `window.allRepos` global, `applyFilters()` pipeline

### Secondary (MEDIUM confidence)
- None needed -- all implementation details are specified in locked decisions and existing code

### Tertiary (LOW confidence)
- None

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH -- No new libraries. Vanilla CSS + JS already established.
- Architecture: HIGH -- Single function addition to existing renderCard, new CSS rules. Straightforward.
- Pitfalls: HIGH -- "dashboard" keyword collision and layout shift are real but manageable with clear type priority and padding.

**Research date:** 2026-04-14
**Valid until:** 2026-05-14 (stable -- no external dependencies to drift)
