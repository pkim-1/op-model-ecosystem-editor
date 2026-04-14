# Roadmap: Project Portfolio Dashboard

## Overview

Build a single-file GitHub project dashboard in three phases: first fetch and display repositories as browsable cards, then add search and filtering for quick discovery, then add visual type indicators for scanability. Each phase delivers a usable improvement -- the dashboard works after Phase 1, gets powerful after Phase 2, and gets polished after Phase 3.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Data Foundation & Card Display** - Fetch all GitHub repos and render browsable project cards in a responsive layout
- [ ] **Phase 2: Search & Filtering** - Add keyword search, topic/language filtering, and recency sorting
- [ ] **Phase 3: Visual Type Indicators** - Add color-coded badges for project type classification

## Phase Details

### Phase 1: Data Foundation & Card Display
**Goal**: Users can browse all their GitHub projects as cards in a responsive grid, with working links back to each repo
**Depends on**: Nothing (first phase)
**Requirements**: DATA-01, DATA-02, DATA-03, DATA-04, DISP-01, DISP-02, DISP-03, DISP-04
**Success Criteria** (what must be TRUE):
  1. Opening the HTML file fetches and displays all user repositories as cards showing name, description, topics, and language
  2. Clicking any project card opens the corresponding GitHub repository in a new tab
  3. The card grid layout adapts responsively from desktop to mobile screen sizes
  4. The dashboard handles users with more than 100 repositories without missing any
  5. The dashboard works without a GitHub token (unauthenticated) and optionally accepts a PAT for higher rate limits
**Plans:** 2 plans
Plans:
- [x] 01-01-PLAN.md -- HTML structure, CSS, and GitHub API client with pagination/auth/error handling
- [x] 01-02-PLAN.md -- Card rendering, responsive grid display, and visual verification
**UI hint**: yes

### Phase 2: Search & Filtering
**Goal**: Users can find any project in seconds through keyword search, topic/language filtering, and recency sorting
**Depends on**: Phase 1
**Requirements**: SEARCH-01, SEARCH-02, SEARCH-03, SEARCH-04
**Success Criteria** (what must be TRUE):
  1. User can type a keyword and see cards filtered in real-time across name, description, and topics
  2. User can select a topic tag to show only projects with that topic
  3. User can select a programming language to show only projects in that language
  4. User can sort the project list by most recently updated
**Plans:** 2 plans
Plans:
- [x] 02-01-PLAN.md -- Search input, sort dropdown, and core filtering engine with URL hash state
- [x] 02-02-PLAN.md -- Topic and language filter chips with visual verification
**UI hint**: yes

### Phase 3: Visual Type Indicators
**Goal**: Users can visually scan project types at a glance through color-coded badges
**Depends on**: Phase 2
**Requirements**: UI-01
**Success Criteria** (what must be TRUE):
  1. Each project card displays a color-coded badge indicating its type (analysis, tool, visualization, etc.)
  2. Badge types are derived from GitHub topics -- no manual configuration needed
**Plans**: TBD
**UI hint**: yes

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Data Foundation & Card Display | 2/2 | Complete | 2026-04-14 |
| 2. Search & Filtering | 0/2 | Not started | - |
| 3. Visual Type Indicators | 0/TBD | Not started | - |
