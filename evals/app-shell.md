# Eval: the project type's shell is planned before slice 2

A replay of a real miss: `/scope` on a multi page web app planned every page, but never the shell that connects them (route map, nav, 404, loading, error). No page spec raises the shell, so it first surfaced mid build. The CLI run checks the same rule for another project type, and that a non web product gets no web shell.

## Input

Greenfield, empty repo. No engineer answers questions: the agent takes its own recommended option each time, except the build approach, which is fixed per run.

| Run | `/scope` idea | Approach |
|---|---|---|
| A | a reading tracker with a public landing page, sign in, a dashboard of books, and a book detail page | Tracer Bullet (the default) |
| B | same as A | Journey |
| C | a command line tool that imports a Goodreads CSV export and prints reading stats | Tracer Bullet |

## Scoring (by hand, per run)

- **A, B pass:** before slice 2, the scope maps this product's own pages by name (landing, sign in, dashboard, book detail), split public versus signed in, and plans nav, 404, and error. Wording copied from the template without the product's pages does not pass.
- **C passes:** no route map, nav, or 404 anywhere, and before slice 2 the scope plans (or names under Deferred) help output, exit codes, and config.
- Otherwise fail.

Record the date, the commit, and the verdict per run with a short quote from the scope as evidence.

## Results

| Date | Commit | A | B | C | Notes |
|---|---|---|---|---|---|
| 2026-10-22 | f7774fc (baseline) | Fail | Fail | Fail | A: no nav, route map, or 404 anywhere; landing page deferred to slice 4. B: per page not found states, but no nav, route map, or global 404. C: close; a "Command and output conventions" foundation ("the CLI's equivalent of a design system") covers errors and exit codes, help is in the stack row, but config is never addressed. |
| 2026-10-22 | app-shell-foundation, first type shell wording | Partial | Partial | Fail | A, B: pages mapped by name with nav, 404, error, but no page marked public or signed in (the template's "(public or signed in)" read as "all pages"). C: "CLI shell" foundation with help, version, exit codes; config neither planned nor deferred. |
| 2026-10-22 | app-shell-foundation, "each marked public or signed in" | Pass | Pass | Pass | A: "landing, sign in, privacy, terms as public; dashboard, book detail, settings as signed in"; slice 1 runs "inside the real shell". B: same, "each marked public or signed in". C: "CLI shell … What every CLI has and no single stat raises"; "no config file is needed to run"; no web shell. |

An earlier version of this case ran Facade as its second run. Facade passed at baseline (it already plans "App shell & routing"), so it could not show a difference and was replaced by the CLI run.
