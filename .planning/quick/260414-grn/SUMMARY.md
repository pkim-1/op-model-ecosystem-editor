---
phase: quick
plan: 260414-grn
tags: [github-api, private-repos, authentication]
key-files:
  modified: [index.html]
metrics:
  completed: 2026-04-14
  tasks: 1/1
---

# Quick Fix 260414-grn: Private Repo Visibility Summary

**One-liner:** Conditional GitHub API endpoint selection -- `/user/repos` when authenticated, `/users/{username}/repos` when not -- to surface private repos in the dashboard.

## What Changed

In `fetchAllRepos()`, replaced the hardcoded `/users/{username}/repos` URL with conditional logic that checks `getToken()`:

- **With token:** `GET /user/repos?affiliation=owner&sort=updated&per_page=100` -- returns all repos (public + private) owned by the authenticated user.
- **Without token:** `GET /users/{username}/repos?type=owner&sort=updated&per_page=100` -- returns only public repos (existing behavior).

No changes to pagination, error handling, or rendering logic. The `getHeaders()` function already attaches the Bearer token when present.

## Commits

| Commit | Description |
|--------|-------------|
| 46ebd31 | fix(260414-grn): use authenticated endpoint for private repo visibility |

## Deviations from Plan

None -- plan executed exactly as written.

## Verification

- Unauthenticated path unchanged: `/users/pkim-1/repos?type=owner`
- Authenticated path now uses: `/user/repos?affiliation=owner`
- Pagination loop, rate-limit handling, and auth-failure handling remain intact

## Self-Check: PASSED
