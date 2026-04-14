# Phase 3: Visual Type Indicators - Context

**Gathered:** 2026-04-14
**Status:** Ready for planning

<domain>
## Phase Boundary

Add color-coded type badges to project cards that visually indicate project type (Analysis, Tool, Visualization) at a glance. Types are automatically derived from GitHub topics using keyword mapping rules.

</domain>

<decisions>
## Implementation Decisions

### Badge Placement & Hierarchy
- **D-01:** Type badge appears in top-left corner of card (absolute positioned)
- **D-02:** Badge floats above card content with subtle shadow for visibility
- **D-03:** Badge does not interfere with title or card click target

### Type Detection Logic
- **D-04:** Use keyword mapping to derive type from GitHub topics:
  - **Analysis**: topics containing "analysis", "data-science", "jupyter", "research", "statistics", "analytics"
  - **Tool**: topics containing "tool", "cli", "library", "framework", "utility", "automation", "dashboard"
  - **Visualization**: topics containing "visualization", "dashboard", "chart", "graph", "plot", "dataviz"
- **D-05:** First matching topic wins (scan topics array left to right)
- **D-06:** If no match: no badge displayed (not all projects need type classification)
- **D-07:** Matching is case-insensitive

### Type Categories & Colors
- **D-08:** Support 3 core types: Analysis, Tool, Visualization
- **D-09:** Color scheme (semantic):
  - **Tool**: `#4248ED` (Pacific Blue — matches existing language badge, represents technical/utility)
  - **Analysis**: `#FF8C00` (Dark Orange — warm, represents insight/data work)
  - **Visualization**: `#9B59B6` (Purple — creative, represents visual output)
- **D-10:** All badge text uses white color for contrast

### Badge Style
- **D-11:** Text-only pill badge (matches existing `.language-badge` pattern)
- **D-12:** Badge styling: rounded pill (`border-radius: 12px`), uppercase text, font-size `0.7rem`, padding `3px 10px`
- **D-13:** No icons — keeps implementation simple for static HTML (no icon font dependency)

### Claude's Discretion

Claude has flexibility on:
- Exact badge positioning offset from card corner (as long as it's visible and doesn't overlap title)
- Badge shadow strength (keep subtle, ensure readability)
- Transition effects when cards render with badges (keep performant)
- Additional keywords for type matching (as long as core keywords from D-04 are covered)

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Prior Phase Context
- `.planning/phases/01-data-foundation-card-display/01-CONTEXT.md` — Card structure, Expedia brand colors, topic chip styling
- `.planning/phases/02-search-filtering/02-CONTEXT.md` — Filter chips pattern, Pacific Blue for active states, window.allRepos global
- `index.html` — Current implementation with card layout, CSS custom properties, existing badge styles

### Requirements
- `.planning/REQUIREMENTS.md` — UI-01

### Project Context
- `.planning/PROJECT.md` — Single HTML file constraint, static HTML + JS only, user has analysis/tool/visualization projects
- `CLAUDE.md` — Expedia brand colors established

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- **`.language-badge`** class — Existing pill badge style (Pacific Blue background, white text, rounded) can be adapted for type badges
- **`.card-meta`** structure — Already contains topics and language, provides reference for badge sizing
- **CSS custom properties** — `--pacific-blue`, `--white` already defined for badge colors
- **`window.allRepos`** — Contains all repo data including topics array for type detection

### Established Patterns
- **Pill badge pattern** — `.language-badge` uses `border-radius: 12px`, `padding: 2px 10px`, `font-size: 0.75rem`
- **Card structure** — `.card` with `.card-meta` at bottom provides clear hierarchy for badge placement
- **Topic matching** — Phase 2 already scans topics array for filter chips, similar logic applies here

### Integration Points
- Add type badge to card markup (before title, positioned absolutely in top-left)
- Type detection function runs during card rendering in `renderGrid()`
- New CSS rules for `.type-badge` class with semantic color variants (`.type-badge.tool`, `.type-badge.analysis`, `.type-badge.visualization`)

</code_context>

<specifics>
## Specific Ideas

### Visual Consistency
- Type badge uses same pill shape and sizing as language badge — maintains visual language
- Pacific Blue reused for Tool type — reinforces technical/utility association established by language badges
- Badge corner placement keeps card face clean — title and description remain focal points

### Type Detection Robustness
- Keyword matching covers common GitHub topic patterns ("data-science", "dataviz", "jupyter-notebook")
- Case-insensitive matching handles varied topic casing
- No badge for unclassified projects — avoids "Unknown" badge noise, cleaner cards

### Color Rationale
- Tool = Pacific Blue: matches existing technical badge (language), familiar to users
- Analysis = Dark Orange: warm color suggests insight/data work, visually distinct from Tool
- Visualization = Purple: creative/visual color, clearly different from technical (blue) and analytical (orange)

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 03-visual-type-indicators*
*Context gathered: 2026-04-14*
