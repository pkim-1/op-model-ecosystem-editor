# Pitfalls Research

**Domain:** Personal project portfolio dashboard (static HTML+JS, GitHub API)
**Researched:** 2026-04-13
**Confidence:** HIGH

## Critical Pitfalls

### Pitfall 1: GitHub API Rate Limiting Kills the Dashboard on Load

**What goes wrong:**
Unauthenticated GitHub API requests are limited to 60 per hour. If the dashboard fetches repos, then fetches topics/languages/details for each repo in separate calls, you burn through 60 requests instantly. The dashboard shows partial data or errors after 2-3 page loads.

**Why it happens:**
Developers test with a personal access token (5,000 req/hr), everything works fine. They forget that opening the file locally without a token -- or sharing it -- hits the 60/hr wall immediately. Even with a token, naive per-repo detail fetching is wasteful.

**How to avoid:**
- Use the `GET /users/{username}/repos?per_page=100` endpoint which returns topics, description, language, and dates in a single response. This covers all the metadata needed for filtering without extra calls.
- Design the token as optional: dashboard works without it (cached/localStorage fallback), but prompts once for a token if rate-limited.
- Never make N+1 API calls (one list call + one detail call per repo).

**Warning signs:**
- Code that calls `/repos/{owner}/{repo}` or `/repos/{owner}/{repo}/topics` in a loop
- Dashboard works for you but fails for others or after a few refreshes
- Fetch errors with HTTP 403 and `X-RateLimit-Remaining: 0` header

**Phase to address:**
Phase 1 (core data fetching) -- get the API strategy right from the start. Retrofitting caching onto a bad fetch pattern is painful.

---

### Pitfall 2: Token Exposed in Source Code or Git History

**What goes wrong:**
The personal access token gets hardcoded into the HTML/JS file, committed to git, or embedded in a way that's visible in browser source. GitHub's secret scanning may revoke it automatically, breaking the dashboard. Or worse, it stays live and grants repo access.

**Why it happens:**
For a "personal tool" people skip proper credential handling. They paste the token directly into a variable during development and forget to remove it.

**How to avoid:**
- Never store the token in the source file. Use `localStorage` -- prompt the user to paste their token once, store it in the browser, and read from there on every fetch.
- Add a "Settings" UI element (even just a modal) for token entry/clearing.
- Add the token via `Authorization` header, never as a URL query parameter (query params appear in server logs and browser history).

**Warning signs:**
- Any string starting with `ghp_` or `github_pat_` in the codebase
- Token passed as `?access_token=` query parameter
- No UI for entering/managing the token

**Phase to address:**
Phase 1 (core data fetching) -- token handling must be designed correctly before any API calls are written.

---

### Pitfall 3: Pagination Silently Truncates Repos

**What goes wrong:**
The GitHub API returns max 100 repos per page (default 30). If you have 120 repos and don't paginate, the dashboard shows 100 and you never notice the missing 20. There is no error -- it just silently omits them.

**Why it happens:**
During development you might have fewer than 100 repos, or you test with `per_page=100` and it works. Months later your repo count crosses 100 and you lose visibility into newer projects -- exactly the ones you most need to find.

**How to avoid:**
- Always implement pagination from day one. Follow the `Link` header or iterate pages until a response returns fewer items than `per_page`.
- Display total repo count on the dashboard so truncation is immediately visible.
- Simple loop: fetch page 1, if result length equals `per_page`, fetch page 2, etc.

**Warning signs:**
- No pagination logic in fetch code
- No display of total repo count
- Dashboard repo count does not match what you see on your GitHub profile

**Phase to address:**
Phase 1 (core data fetching) -- pagination is part of the fetch layer, not something to bolt on later.

---

### Pitfall 4: Metadata Quality Makes Filtering Useless

**What goes wrong:**
You build search and filter UI for topics, tech stack, and project type. But most of your repos have no description, no topics, and only auto-detected language. The filters show mostly empty categories or "uncategorized." The dashboard technically works but is useless for discovery.

**Why it happens:**
The dashboard design assumes GitHub metadata is already populated. The developer builds the tool, opens it, sees a wall of untitled cards, and realizes they need to go back-fill metadata on 50+ repos. This is tedious enough that it never happens completely.

**How to avoid:**
- Before building the dashboard, audit your repos: how many have descriptions? Topics? Run a quick script or API call to assess metadata completeness.
- Design the dashboard to surface "incomplete" repos prominently -- make it a tool for improving metadata, not just consuming it.
- Show the auto-detected `language` field as a fallback filter even when topics are missing.
- Consider a "needs metadata" badge/filter to gamify back-filling.

**Warning signs:**
- Building filter UI before checking how many repos actually have topics
- No plan for the back-fill effort
- Mockups that assume every card has rich metadata

**Phase to address:**
Phase 1 (planning/data audit) -- assess metadata state before committing to a filter design. Phase 2 (UI) should handle sparse metadata gracefully.

---

### Pitfall 5: Building Too Much UI Before Validating the Core Value

**What goes wrong:**
You spend time on card layouts, animations, responsive design, color schemes, and filter dropdowns. Then you open the finished dashboard and realize you actually just need a searchable list with links. The elaborate UI adds friction rather than speed. Or worse, the project stalls during UI polish and never ships.

**Why it happens:**
Dashboard projects are inherently visual, which makes it tempting to focus on presentation. The core value (quickly find a project) is simple, but "dashboard" implies a rich visual experience.

**How to avoid:**
- Ship a minimal version first: a styled list with a search box and one filter. Use it for a week. Then decide what UI is actually needed.
- Define "done" as "I can find any project in under 5 seconds" not "it looks impressive."
- Resist adding features until the basic version feels insufficient.

**Warning signs:**
- Spending more than 30 minutes on card layout before search works
- Building filter dropdowns for categories that have fewer than 5 items
- Adding features you have not personally needed yet

**Phase to address:**
Phase 1 should deliver a working, minimal dashboard. Phase 2 adds polish only after Phase 1 is used and validated.

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Hardcoded username in fetch URL | Faster to build | Cannot reuse or share; breaks if username changes | MVP only, parameterize in Phase 2 |
| No localStorage caching of API responses | Simpler code | Every page load burns API calls; slow on poor connections | Never -- caching is low effort and essential |
| Inline CSS instead of a stylesheet | One-file simplicity | Hard to maintain visual consistency as UI grows | Acceptable if using a single HTML file by design |
| String matching for search instead of proper indexing | Simpler implementation | Slow with 200+ repos, no fuzzy matching | Fine for under 200 repos (this project's likely scale) |

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| GitHub REST API | Fetching repo details one-by-one (N+1 calls) | Use list endpoint with `per_page=100`; it includes topics, description, language |
| GitHub REST API | Not handling 403 rate limit responses | Check `X-RateLimit-Remaining` header; show user-friendly message with reset time |
| GitHub REST API | Assuming `topics` field is always present | The `/users/{user}/repos` endpoint includes topics only when using `Accept: application/vnd.github.mercy-preview+json` header (or the default v3 header in recent API versions). Verify the response shape. |
| GitHub Pages hosting | Assuming file:// protocol works the same as https:// | Fetch API has different CORS behavior on file://. GitHub API does support CORS from browsers, but test both local file and hosted scenarios. |
| localStorage | Storing token without any expiry or validation check | Validate token on load with a lightweight API call; clear if revoked |

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Fetching all repos on every page load | Slow initial render, wasted API calls | Cache in localStorage with a TTL (e.g., 15 minutes); add a manual "Refresh" button | Immediately noticeable with 50+ repos or slow connections |
| Rendering all cards to the DOM at once | Scroll jank, slow paint on mobile | For personal use with under 200 repos, this is fine. Only virtualize if repo count exceeds 500. | Over 500 DOM nodes with complex card markup |
| Client-side search re-filtering on every keystroke | Input lag when typing search queries | Debounce search input by 200-300ms | Over 100 repos with complex filter logic |
| Loading full README content for preview | Massive payload, slow renders | Do not fetch READMEs. Use the description field only. | Immediately -- even 10 README fetches is too many |

## Security Mistakes

| Mistake | Risk | Prevention |
|---------|------|------------|
| Token in source code or git history | Token leaked; GitHub may auto-revoke; attacker gets repo access | Use localStorage prompt pattern. Add `ghp_` and `github_pat_` to `.gitignore` patterns or a pre-commit hook. |
| Token with excessive scopes | A leaked read-only token is bad; a leaked `repo` (write) token is catastrophic | Create a fine-grained token with read-only access to public repos only. No write permissions. |
| Token sent as URL query parameter | Logged in browser history, server access logs, referrer headers | Always use `Authorization: Bearer {token}` header |

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| No loading state during API fetch | User sees blank page, assumes broken | Show skeleton/spinner on load; show cached data immediately, refresh in background |
| Filters that produce zero results with no explanation | User thinks dashboard is broken | Show "No projects match" with a link to clear filters |
| Cards that all look identical | Cannot scan visually for the right project | Differentiate by language color dot, recency indicator, or type icon |
| No indication of stale cached data | User sees outdated repos, misses new ones | Show "last updated: X minutes ago" with a refresh button |
| Search only matching repo name, not description or topics | User cannot find project by what it does | Search across name + description + topics. These are the fields users actually remember. |

## "Looks Done But Isn't" Checklist

- [ ] **API fetch:** Works without a token (graceful degradation) -- verify by clearing localStorage and reloading
- [ ] **Pagination:** Returns all repos, not just first page -- verify by comparing count to your GitHub profile
- [ ] **Search:** Matches description and topics, not just repo name -- verify by searching for a topic keyword
- [ ] **Empty states:** Every filter combination has a meaningful empty state -- verify by selecting impossible filter combos
- [ ] **Error handling:** Rate limit and network errors show user-friendly messages -- verify by throttling network in DevTools
- [ ] **Token persistence:** Token survives page reload -- verify by entering token, closing browser, reopening
- [ ] **Mobile:** Dashboard is usable on phone -- verify by actually opening it on your phone, not just DevTools responsive mode
- [ ] **Forked repos:** Decide whether to show forks (probably not) -- verify `fork: true` repos are filtered out

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Token leaked in git history | MEDIUM | Revoke token on GitHub immediately. Create new fine-grained token. Run `git filter-branch` or BFG to scrub history. |
| Rate limiting breaking the dashboard | LOW | Add localStorage caching with TTL. Show cached data while rate-limited. Display reset countdown. |
| Pagination not implemented (missing repos) | LOW | Add pagination loop to fetch function. Usually a 10-line fix. |
| Metadata is sparse (filters useless) | HIGH | Manual effort: go through each repo on GitHub and add description + topics. No technical shortcut. |
| Over-engineered UI (scope creep) | MEDIUM | Strip back to working list + search. Archive the complex UI branch. Rebuild incrementally. |

## Pitfall-to-Phase Mapping

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| API rate limiting | Phase 1 (data fetching) | Dashboard loads without token; shows cached data; handles 403 gracefully |
| Token exposure | Phase 1 (data fetching) | No token strings in source; localStorage prompt works; git history clean |
| Pagination truncation | Phase 1 (data fetching) | Repo count matches GitHub profile count |
| Sparse metadata | Pre-Phase 1 (data audit) | Run audit script; at least 80% of repos have description + 1 topic |
| Over-building UI | Phase 1 (MVP) | Ship minimal version first; use it for a week before adding features |
| No caching | Phase 1 (data fetching) | Page loads instantly on second visit; works offline with cached data |
| Search too narrow | Phase 2 (search/filter) | Search matches across name, description, and topics fields |
| Forked repos cluttering results | Phase 1 (data fetching) | Add `fork` field filter; default to hiding forks |

## Sources

- GitHub REST API rate limiting documentation: https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api (HIGH confidence -- official docs)
- GitHub REST API repos endpoint: https://docs.github.com/en/rest/repos/repos (HIGH confidence -- official docs)
- GitHub API pagination: per_page max 100, default 30, Link header pagination (HIGH confidence -- official docs)
- Token security patterns: GitHub fine-grained personal access tokens documentation (HIGH confidence)
- localStorage caching patterns: standard web platform knowledge (HIGH confidence)

---
*Pitfalls research for: Personal project portfolio dashboard (static HTML+JS, GitHub API)*
*Researched: 2026-04-13*
