# Project Portfolio Dashboard

## What This Is

A personal project dashboard — a single HTML page that automatically fetches and displays all GitHub projects with search and filtering capabilities. Helps quickly find and access any project by tags, type, date, or tech stack.

## Core Value

Quickly find and access any project through search and filtering, without manually digging through GitHub repositories.

## Requirements

### Validated

- [x] Dashboard fetches GitHub repositories automatically via API — Validated in Phase 1: Data Foundation & Card Display
- [x] Display project cards showing purpose, outputs, and tags — Validated in Phase 1: Data Foundation & Card Display
- [x] Click project card to open GitHub repo or hosted page — Validated in Phase 1: Data Foundation & Card Display
- [x] Use GitHub repo description for project purpose — Validated in Phase 1: Data Foundation & Card Display
- [x] Use GitHub topics for tags and categorization — Validated in Phase 1: Data Foundation & Card Display

### Active

- [ ] Search projects by keyword or tag
- [ ] Filter projects by type (analysis, tool, visualization, etc.)
- [ ] Filter projects by date/recency
- [ ] Filter projects by tech stack
- [ ] Local projects can be uploaded to GitHub and included

### Out of Scope

- Backend server or database — Static HTML + JS only
- Manual metadata entry — Relies on GitHub's native description/topics
- Real-time updates — Refresh page to fetch latest data
- Project editing from dashboard — Edit metadata in GitHub directly

## Context

User has multiple projects across GitHub:
- Analysis work (e.g., lodging provisions analysis)
- Code projects and tools
- Various types: analysis, visualization, research, etc.

Local files (HTML analyses, documents) can be uploaded to GitHub repos or GitHub Pages, making everything GitHub-based for consistent access.

The dashboard serves as a personal index — not public-facing, just for personal organization and quick discovery.

## Constraints

- **Tech**: Static HTML + JavaScript (no backend server)
- **Data source**: GitHub API for fetching repositories
- **Metadata**: GitHub repository description and topics only
- **Hosting**: Single HTML file that can be opened locally or served anywhere

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| GitHub-only data source | All projects hosted on GitHub, local files uploaded first | — Pending |
| Static HTML + JS | No server needed, simple to maintain and use | — Pending |
| GitHub metadata only | Use built-in description/topics instead of separate config | — Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `/gsd-transition`):
1. Requirements invalidated? → Move to Out of Scope with reason
2. Requirements validated? → Move to Validated with phase reference
3. New requirements emerged? → Add to Active
4. Decisions to log? → Add to Key Decisions
5. "What This Is" still accurate? → Update if drifted

**After each milestone** (via `/gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check — still the right priority?
3. Audit Out of Scope — reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-04-14 after Phase 1 completion*
