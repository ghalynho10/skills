# Eval: app shell planned before slice 2

A replay of a real miss: `/scope` on a multi page web app planned every page, but never the shell that connects them (route map, nav, 404, loading, error). No page spec raises the shell, so it first surfaced mid build.

## Input

Greenfield, empty repo, `/scope` with:

> a reading tracker with a public landing page, sign in, a dashboard of books, and a book detail page

No engineer answers questions: the agent takes its own recommended option each time, except the build approach, which is fixed per run.

| Run | Approach |
|---|---|
| A | Tracer Bullet (the default) |
| B | Facade |
| C | Journey |

## Scoring (by hand, per run)

- **Pass:** the foundations or slice 1 plan the route map (which pages exist, public versus signed in) and the shell layout (nav, 404, error) before slice 2.
- **Fail:** navigation or layout first appears in slice 2 or later, or nowhere.

Record the date, the commit, and the verdict per run with a one line quote from the scope as evidence.

## Results

| Date | Commit | A | B | C | Notes |
|---|---|---|---|---|---|
| 2026-10-22 | f7774fc (baseline) | Fail | Pass | Fail | A: no nav, route map, or 404 anywhere; landing page deferred to slice 4. B: own foundation row, "App shell & routing … a missing route shows a friendly not found page." C: per page not found states, but no nav, route map, or global 404. |
| 2026-10-22 | app-shell-foundation (changes 4 to 7) | Pass | Pass | Pass | All three: foundation 4 names the route map page by page and "the shell renders nav, 404, loading, and error states." A: slice 1 runs "inside the real shell." B: the separate shell row folded into foundation 4. C: the skeleton renders "inside the app shell." |
