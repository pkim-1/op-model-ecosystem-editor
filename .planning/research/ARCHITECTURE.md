# Architecture Research

**Domain:** Personal project portfolio dashboard (single-page, static)
**Researched:** 2026-04-13
**Confidence:** HIGH

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                     Presentation Layer                       │
├─────────────────────────────────────────────────────────────┤
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ Search   │  │ Filter   │  │ Project  │  │ Detail   │   │
│  │ Bar      │  │ Controls │  │ Grid     │  │ Panel    │   │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘   │
│       │              │             │              │         │
├───────┴──────────────┴─────────────┴──────────────┴─────────┤
│                     State Manager                            │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Filter State | Search Query | Sort Order | Repos    │   │
│  └──────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│                     Data Layer                               │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐                  │
│  │ GitHub   │  │ Cache    │  │ Data     │                  │
│  │ API      │  │ (session)│  │ Transform│                  │
│  │ Client   │  │          │  │          │                  │
│  └──────────┘  └──────────┘  └──────────┘                  │
└─────────────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────┐
│  GitHub REST API │
│  GET /user/repos │
└─────────────────┘
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| Search Bar | Free-text search across repo name, description, topics | Input element with debounced keyup handler filtering in-memory data |
| Filter Controls | Tag/type/date/stack filtering with active filter display | Button groups or dropdowns that update filter state |
| Project Grid | Renders repo cards in responsive grid layout | CSS Grid of card elements, re-rendered on state change |
| Detail Panel | Shows expanded info and links for a selected project | Modal or side panel triggered by card click |
| State Manager | Holds current filter/search/sort state, triggers re-renders | Plain JS object with pub/sub or simple event emitter |
| GitHub API Client | Fetches repos, handles pagination, manages auth token | Fetch wrapper with pagination loop and error handling |
| Cache (session) | Avoids re-fetching repos on every page interaction | sessionStorage or in-memory variable holding fetched repos |
| Data Transform | Normalizes API response into display-friendly shape | Map function extracting name, description, topics, language, dates, URLs |

## Recommended Project Structure

Since this is a single HTML file with embedded JS/CSS, the "structure" is logical sections within the file rather than a directory tree.

```
index.html
├── <style>                    # All CSS (grid, cards, filters, responsive)
├── <body>                     # Markup structure
│   ├── header                 # Title, search bar
│   ├── filter-bar             # Filter controls (type, language, date range)
│   ├── project-grid           # Container for project cards
│   └── detail-panel           # Overlay/modal for expanded view
├── <script>                   # All JavaScript
│   ├── // --- Config ---      # API token, username, settings
│   ├── // --- API Client ---  # fetchRepos(), pagination handling
│   ├── // --- Data Transform  # normalizeRepo(), extractTags()
│   ├── // --- State ---       # App state object, update functions
│   ├── // --- Filters ---     # Filter/search logic (pure functions)
│   ├── // --- Rendering ---   # renderGrid(), renderCard(), renderFilters()
│   └── // --- Init ---        # Entry point, event listeners, initial fetch
```

### Structure Rationale

- **Single file:** Project constraint. Logical sections with comment headers keep it navigable.
- **Config at top of script:** Token and username easy to find and change.
- **Pure filter functions:** Testable logic separated from DOM manipulation.
- **Rendering at bottom:** Depends on everything above; natural reading order.

## Architectural Patterns

### Pattern 1: Fetch-Once, Filter-In-Memory

**What:** Fetch all repositories on page load, store in memory, filter/search entirely client-side.
**When to use:** When total repo count is manageable (under ~500 repos, typical for personal accounts).
**Trade-offs:** Fast filtering after initial load. Simple code. But initial load fetches everything.

**Example:**
```javascript
let allRepos = [];

async function fetchAllRepos(username) {
  let page = 1;
  const perPage = 100;
  while (true) {
    const response = await fetch(
      `https://api.github.com/users/${username}/repos?per_page=${perPage}&page=${page}`,
      { headers: { 'Authorization': `token ${TOKEN}` } }
    );
    const repos = await response.json();
    allRepos.push(...repos);
    if (repos.length < perPage) break;
    page++;
  }
}

function filterRepos(repos, { search, language, type }) {
  return repos.filter(repo => {
    if (search && !matchesSearch(repo, search)) return false;
    if (language && repo.language !== language) return false;
    if (type && !repo.topics.includes(type)) return false;
    return true;
  });
}
```

### Pattern 2: Derived State Rendering

**What:** UI is a pure function of state. Any state change triggers a full re-render of the affected section.
**When to use:** Simple apps without a framework. Avoids manual DOM diffing.
**Trade-offs:** Simple mental model. Slightly wasteful DOM updates, but irrelevant at this scale (dozens of cards, not thousands).

**Example:**
```javascript
const state = {
  repos: [],
  search: '',
  filters: { language: null, type: null, dateRange: null },
  sort: 'updated',
};

function render() {
  const filtered = filterRepos(state.repos, state.filters, state.search);
  const sorted = sortRepos(filtered, state.sort);
  document.getElementById('project-grid').innerHTML = sorted.map(renderCard).join('');
  renderActiveFilters();
}

// Any state mutation calls render()
function setSearch(query) {
  state.search = query;
  render();
}
```

### Pattern 3: Progressive Enhancement for Auth

**What:** Work without a token (public repos only, 60 req/hr), work better with a token (all repos, 5000 req/hr).
**When to use:** When the dashboard might be opened on different machines or shared.
**Trade-offs:** Slightly more code to handle both paths, but makes the tool more resilient.

**Example:**
```javascript
function getHeaders() {
  const token = localStorage.getItem('github_token');
  const headers = { 'Accept': 'application/vnd.github+json' };
  if (token) headers['Authorization'] = `token ${token}`;
  return headers;
}
```

## Data Flow

### Initial Load Flow

```
Page Load
    |
    v
Read token from localStorage (or prompt)
    |
    v
GitHub API Client: paginate GET /user/repos (or /users/{username}/repos)
    |
    v
Data Transform: normalize each repo -> { name, description, topics, language, url, homepage, updatedAt, createdAt }
    |
    v
Store in state.repos (and optionally sessionStorage cache)
    |
    v
Extract unique languages, topics from all repos -> populate filter options
    |
    v
render() -> display all cards in grid
```

### User Interaction Flow

```
User types in search / clicks filter / changes sort
    |
    v
Event handler updates state (state.search, state.filters, state.sort)
    |
    v
filterRepos(state.repos, state.filters, state.search)
    |
    v
sortRepos(filtered, state.sort)
    |
    v
render() -> re-render grid with filtered/sorted results
```

### Card Click Flow

```
User clicks project card
    |
    v
Option A: Open GitHub repo URL directly (window.open)
Option B: Show detail panel with description, topics, links, last updated
    |
    v
Detail panel has "Open on GitHub" and "Open Homepage" buttons
```

### Key Data Flows

1. **Repo fetch:** One-time paginated fetch on load. All data lives in memory for the session. No subsequent API calls needed for filtering/searching.
2. **Filter cascade:** Filters are AND-composed. Each active filter narrows the set. Search is fuzzy across name + description + topics.
3. **Dynamic filter options:** Available filter values (languages, topics) are derived from the fetched repo data, not hardcoded.

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| 1-50 repos | Current architecture is ideal. Single fetch, instant filtering. |
| 50-200 repos | Still fine. Pagination handles the fetch. Consider lazy-loading card images if added. |
| 200-500 repos | May want to add a loading indicator during initial fetch. sessionStorage cache becomes more valuable. |
| 500+ repos | Consider GitHub GraphQL API to fetch only needed fields (smaller payloads). Virtualized rendering if grid gets slow. |

### Scaling Priorities

1. **First bottleneck:** Initial fetch time for many repos. Mitigate with sessionStorage caching and a loading state.
2. **Second bottleneck:** DOM rendering of hundreds of cards. Mitigate with pagination within the grid (show 50 at a time with "load more") or virtual scrolling. Unlikely to be hit for a personal account.

## Anti-Patterns

### Anti-Pattern 1: Re-fetching on Every Filter Change

**What people do:** Call the GitHub API every time a filter is applied.
**Why it's wrong:** Wastes rate limit budget, adds latency to every interaction, makes filtering feel slow.
**Do this instead:** Fetch once, filter in memory. GitHub API has no server-side topic/keyword filtering anyway.

### Anti-Pattern 2: Storing Token in HTML Source

**What people do:** Hardcode the GitHub personal access token directly in the HTML file.
**Why it's wrong:** If the file is ever shared, committed to a repo, or served publicly, the token is exposed.
**Do this instead:** Prompt for token on first use, store in localStorage. Or use a token with minimal scopes (read-only public repos needs no token at all).

### Anti-Pattern 3: innerHTML Without Sanitization

**What people do:** Inject repo descriptions directly via innerHTML.
**Why it's wrong:** Repo descriptions could contain HTML/script content (XSS vector, even if self-controlled data).
**Do this instead:** Use textContent for user-generated strings, or sanitize before innerHTML insertion.

### Anti-Pattern 4: Monolithic Render Function

**What people do:** One giant function that builds the entire page.
**Why it's wrong:** Hard to modify individual sections, impossible to do partial updates.
**Do this instead:** Separate render functions per section (renderGrid, renderFilters, renderHeader) even within a single file.

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| GitHub REST API | `GET /user/repos` with pagination, `GET /users/{username}/repos` for public-only | Rate limit: 5000/hr authenticated, 60/hr unauthenticated. Max 100 per page. Use Link header for pagination. |
| GitHub Pages (optional) | Host the single HTML file | Free hosting, custom domain support. File just needs to be in a repo. |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| API Client <-> State | API client returns normalized data, state stores it | One-way: API populates state on load, never the reverse |
| State <-> Rendering | State changes trigger render calls | One-way: state drives UI. UI events update state, which triggers re-render. |
| Filters <-> State | Pure functions that take state and return filtered array | Filters never mutate state directly; they produce derived data. |

## Build Order (Suggested Implementation Sequence)

Components have clear dependencies that dictate build order:

1. **GitHub API Client + Data Transform** -- Foundation. Nothing works without data. Build the fetch, pagination, and normalization first. Test with console.log.
2. **State Manager** -- Holds fetched repos and filter/search state. Wire it to the API client output.
3. **Project Grid + Card Rendering** -- Display the data. Basic cards showing name, description, language, topics, last updated. Clickable to open repo.
4. **Search** -- Add text search across the in-memory data. Wire input to state, state to re-render.
5. **Filter Controls** -- Add language filter, topic/type filter, date filter. Derive available options from data.
6. **Sort Controls** -- Add sort by name, date updated, date created.
7. **Polish** -- Loading states, error handling, empty states, responsive layout, token management UX.

Each step produces a working (if minimal) dashboard, enabling incremental delivery.

## Sources

- GitHub REST API docs: https://docs.github.com/en/rest/repos/repos (HIGH confidence -- official docs)
- GitHub rate limits: https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api (HIGH confidence -- official docs)
- Architecture patterns: Based on established single-page application patterns for vanilla JS (HIGH confidence -- well-known patterns)

---
*Architecture research for: Personal Project Portfolio Dashboard*
*Researched: 2026-04-13*
