# Stack Research

**Domain:** Static single-page project portfolio dashboard (GitHub-backed)
**Researched:** 2026-04-13
**Confidence:** HIGH

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Vanilla HTML5 + CSS3 | Current spec | Page structure and styling | Single HTML file constraint -- no build step, no framework overhead. CSS Grid and custom properties handle the card layout and theming natively. |
| Vanilla JavaScript (ES2022+) | Current spec | Fetching data, rendering, filtering | All modern browsers support async/await, optional chaining, modules. No framework needed for a read-only dashboard with ~100 items. |
| GitHub REST API | v2022-11-28 (default) | Repository data source | Stable version, returns description + topics. The newer v2026-03-10 exists but v2022-11-28 is the safe default and sufficient for `GET /users/{username}/repos`. |
| CSS Custom Properties | Current spec | Theming / Expedia brand colors | Apply EG Blue (#000099) and brand palette without a preprocessor. Native, zero-dependency. |

### Supporting Libraries

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| None (native `fetch`) | Built-in | HTTP requests to GitHub API | Always. `fetch` handles the 1-2 API calls needed. Octokit is overkill for public repo listing. |
| Fuse.js (via CDN) | 7.1.0 | Client-side fuzzy search | If keyword search needs to be fuzzy (typo-tolerant). Load from `cdn.jsdelivr.net/npm/fuse.js@7.1.0`. Optional -- native `String.includes()` or `filter()` may suffice for exact matching. |

### Development Tools

| Tool | Purpose | Notes |
|------|---------|-------|
| Live Server (VS Code extension) | Local dev server with reload | Avoids CORS issues when testing locally. Any static file server works. |
| Browser DevTools | Debugging, network inspection | Monitor API calls, check rate limits in response headers. |
| GitHub Pages | Hosting (optional) | Free, serves static files, custom domain support. Zero config for a single HTML file. |

## No Build Step Required

This project explicitly avoids npm, bundlers, and frameworks. Everything loads via CDN or is written inline.

```html
<!-- The entire "installation" -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Project Dashboard</title>
  <!-- Optional: Fuse.js for fuzzy search -->
  <script src="https://cdn.jsdelivr.net/npm/fuse.js@7.1.0/dist/fuse.min.js"></script>
  <style>
    /* All CSS inline */
  </style>
</head>
<body>
  <script>
    /* All JS inline */
  </script>
</body>
</html>
```

## GitHub API Strategy

**Endpoint:** `GET /users/{username}/repos`

**Key parameters:**
- `per_page=100` (max per request)
- `sort=updated` (most recently active first)
- `type=owner` (only repos the user owns, not forks)

**Rate limits:**
- Unauthenticated: 60 requests/hour (sufficient for personal use -- each page load is 1-3 requests)
- Authenticated (token): 5,000 requests/hour (use if rate-limited)

**Authentication approach:** Store a GitHub personal access token in `localStorage` with a settings UI, or hardcode for personal use. Token is optional -- public repos are accessible without auth.

**Pagination:** If user has >100 repos, follow `Link` header for next page. Parse with regex or manual URL construction.

**Fields used from response:**
- `name`, `description` -- display
- `topics` -- tags/filtering
- `html_url` -- link to repo
- `homepage` -- link to hosted page (GitHub Pages)
- `language` -- tech stack filter
- `pushed_at` -- recency filter
- `fork` -- exclude if desired

## Alternatives Considered

| Recommended | Alternative | When to Use Alternative |
|-------------|-------------|-------------------------|
| Vanilla JS | React/Vue/Svelte | Never for this project. Adds build step, bundle size, and complexity for a single-page read-only dashboard. |
| Vanilla JS | Alpine.js (via CDN) | If the filtering/rendering logic becomes complex enough to warrant declarative reactivity. Unlikely for ~100 items. |
| Native `fetch` | @octokit/rest v22.0.1 | If you need authenticated pagination helpers or GraphQL. Overkill here -- adds 40KB+ for one API call. |
| Native `fetch` | octokit.js v5.0.5 | Same as above. Full SDK is unnecessary for listing public repos. |
| Fuse.js | Lunr.js | If you need full-text indexing with scoring. Fuse.js is simpler for fuzzy matching on short strings (repo names, descriptions). |
| CSS Grid | Masonry layout libs | Never. CSS Grid handles card layouts natively. `grid-template-columns: repeat(auto-fill, minmax(300px, 1fr))` covers it. |
| GitHub REST API | GitHub GraphQL API | If you need to fetch specific nested fields (e.g., README content, contributor count) in a single request. REST is simpler for listing repos with topics. |
| CDN imports | npm + bundler | Never for this project. The constraint is a single HTML file with no build step. |

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| React / Vue / Angular | Massive overkill for a static dashboard. Adds build tooling, bundle size, and maintenance burden for zero benefit. | Vanilla JS with `document.createElement` or template literals |
| npm / Webpack / Vite | Project constraint is a single HTML file. Build tools add complexity with no payoff for this scope. | Inline `<script>` and `<style>` tags, CDN for any library |
| jQuery | Obsolete for modern browsers. `fetch`, `querySelector`, `classList` cover everything jQuery offered. | Native DOM APIs |
| Bootstrap / Tailwind | Adds dependency weight for a custom-branded dashboard. You need Expedia brand colors and layout, not a generic framework. | Custom CSS with CSS Grid and custom properties |
| @octokit/rest or octokit.js | 40-100KB+ of JavaScript to wrap a single `fetch` call. The GitHub REST API is simple enough to call directly. | `fetch('https://api.github.com/users/{username}/repos?per_page=100')` |
| localStorage for caching repos | Stale data problems, cache invalidation complexity. The API call is fast enough for personal use. | Fresh fetch on each page load (consider `sessionStorage` if load time becomes an issue) |
| Service Workers | Over-engineering for a personal dashboard. Adds complexity for offline support you don't need. | Direct fetch on page load |

## Stack Patterns by Variant

**If repos exceed 100 (pagination needed):**
- Parse `Link` header from GitHub API response
- Fetch pages sequentially or in parallel with `Promise.all`
- Display loading indicator during multi-page fetch

**If fuzzy search is needed:**
- Add Fuse.js via CDN (7KB gzipped)
- Index on `name`, `description`, and `topics` fields
- Threshold of 0.3-0.4 works well for repo names

**If authentication is needed (rate limits or private repos):**
- Add a settings panel with token input stored in `localStorage`
- Pass token via `Authorization: Bearer {token}` header
- Never commit the token -- it lives only in the browser

**If the project grows beyond a single file:**
- Split into `index.html`, `style.css`, `app.js`
- Still no build step -- just `<link>` and `<script>` tags
- Host on GitHub Pages from a `/docs` folder

## Version Compatibility

| Technology | Browser Support | Notes |
|-----------|-----------------|-------|
| `fetch` API | All modern browsers (Chrome 42+, Firefox 39+, Safari 10.1+, Edge 14+) | No IE11 support needed for personal dashboard |
| CSS Grid | All modern browsers (Chrome 57+, Firefox 52+, Safari 10.1+, Edge 16+) | `auto-fill` and `minmax` fully supported |
| ES2022+ (async/await, optional chaining) | All modern browsers | No transpilation needed |
| CSS Custom Properties | All modern browsers (Chrome 49+, Firefox 31+, Safari 9.1+, Edge 15+) | Full support, no preprocessor needed |
| GitHub REST API v2022-11-28 | N/A (server-side) | Supported for at least 24 months after v2026-03-10 release |

## Sources

- GitHub REST API docs (https://docs.github.com/en/rest) -- API versions, endpoints, rate limits [HIGH confidence]
- GitHub API Versions page -- confirmed v2022-11-28 default, v2026-03-10 latest [HIGH confidence]
- Octokit releases (https://github.com/octokit/octokit.js/releases) -- v5.0.5 confirmed [HIGH confidence]
- @octokit/rest on npm via jsDelivr -- v22.0.1 confirmed [HIGH confidence]
- Fuse.js (https://www.fusejs.io/) -- v7.1.0 current, lightweight fuzzy search [MEDIUM confidence, version from training data]
- MDN Web Docs -- browser compatibility for fetch, CSS Grid, ES2022+ features [HIGH confidence]

---
*Stack research for: Static project portfolio dashboard*
*Researched: 2026-04-13*
