# Requirements: Project Portfolio Dashboard

**Defined:** 2026-04-13
**Core Value:** Quickly find and access any project through search and filtering, without manually digging through GitHub repositories.

## v1 Requirements

Requirements for initial release. Each maps to roadmap phases.

### Data Fetching

- [ ] **DATA-01**: Dashboard fetches all user's public GitHub repositories via REST API
- [ ] **DATA-02**: Dashboard handles API rate limiting (60 req/hr unauthenticated)
- [ ] **DATA-03**: Dashboard handles pagination for users with >100 repositories
- [ ] **DATA-04**: User can optionally provide GitHub PAT for authenticated access (5000 req/hr)

### Display

- [ ] **DISP-01**: Display project cards showing name, description, topics, and language
- [ ] **DISP-02**: Cards use responsive grid layout (works on desktop and mobile)
- [ ] **DISP-03**: Each card has click-through link to GitHub repository
- [ ] **DISP-04**: Dashboard loads and displays within single HTML file

### Search & Filtering

- [ ] **SEARCH-01**: User can search projects by keyword (searches name, description, topics)
- [ ] **SEARCH-02**: User can filter projects by topic/tag (from GitHub topics)
- [ ] **SEARCH-03**: User can filter projects by programming language
- [ ] **SEARCH-04**: User can sort projects by recency (most recently updated first)

### Visual Design

- [ ] **UI-01**: Projects display visual type indicators (color-coded badges for analysis/tool/visualization)

## v2 Requirements

Deferred to future release. Tracked but not in current roadmap.

### Search & Filtering

- **SEARCH-05**: User can apply multiple filters simultaneously (e.g., topic + language + type)
- **SEARCH-06**: User can filter by project type (analysis, tool, visualization)
- **UI-02**: Display helpful message when no projects match current filters

### Visual & Enhancement

- **UI-03**: Detect and display GitHub Pages link with "View Live" button when available
- **UI-04**: Surface pinned/featured projects at top of list
- **PREF-01**: Remember last-used filters in localStorage across sessions
- **PREF-02**: Support private repository display with GitHub PAT

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Backend server or database | Static HTML + JS only per project constraints; adds unnecessary complexity |
| Manual metadata entry | GitHub repo description/topics are source of truth; manual entry creates maintenance burden |
| Real-time auto-refresh | Page refresh is sufficient for personal tool; wastes API rate limit |
| Full-text code search | Way beyond scope; keyword search covers 95% of discovery needs |
| Analytics/tracking | Personal tool, not product; adds complexity for zero value |
| Dark/light theme toggle | One well-chosen theme sufficient for v1 |
| User authentication UI | For local HTML file, not needed; PAT in localStorage if private repos needed |
| Edit projects from dashboard | GitHub is the editor; keep single source of truth |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| DATA-01 | Pending | Pending |
| DATA-02 | Pending | Pending |
| DATA-03 | Pending | Pending |
| DATA-04 | Pending | Pending |
| DISP-01 | Pending | Pending |
| DISP-02 | Pending | Pending |
| DISP-03 | Pending | Pending |
| DISP-04 | Pending | Pending |
| SEARCH-01 | Pending | Pending |
| SEARCH-02 | Pending | Pending |
| SEARCH-03 | Pending | Pending |
| SEARCH-04 | Pending | Pending |
| UI-01 | Pending | Pending |

**Coverage:**
- v1 requirements: 13 total
- Mapped to phases: 0 (pending roadmap creation)
- Unmapped: 13 ⚠️

---
*Requirements defined: 2026-04-13*
*Last updated: 2026-04-13 after initial definition*
