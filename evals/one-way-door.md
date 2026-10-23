# Eval: a one way door gets the cross check at any tier

A hard to undo decision made at a low tier. Before this case, `/architect` picked the cross check recommendation from the tier alone (at `Prototype`: `Skip`, or `Same model` for a foundational spec), and no spec said which pattern it followed or whether it could be undone.

## Input

A repo holding only these two files, then `/architect primary database`.

`AGENTS.md`:

> Reading tracker. Stack: Next.js (App Router), TypeScript, deployed on Vercel. No database chosen yet.

`docs/scope/scope.md`:

> **Workflow:** Prototype
>
> ### 3. Primary database · needs a decision
> Where readers, their books, and reading status are stored.
> **Done when:** the database is chosen and a reader's books survive a redeploy.
> - [ ] Design it (spec): `/architect primary database`

No engineer answers questions: the agent writes each decision panel, with its recommended option, to `panels.md`, then takes that option. At the cross check panel it writes the panel and picks `Skip`, so the run ends there.

## Scoring (by hand)

Whether a database is a one way door is the agent's call (standard Postgres with plain SQL migrations can fairly be called two way), so the case scores that it states one and acts on it, not which one it picks.

- **Pass:** the spec's Decision has a `Follows` line naming a pattern the design really has, its Rationale opens with a `Reversibility` line with its reason (what makes undoing costly, or what undoes it), and the cross check recommendation follows from it: `Another model` for a `one way door`; for a `two way door` at `Prototype`, `Skip` (or `Same model`, since this spec is foundational).
- **Fail:** either line is missing or bare, or the recommendation contradicts the line.

## Results

| Date | Commit | Verdict | Notes |
|---|---|---|---|
| 2026-10-22 | f7774fc (baseline) | Fail | No `Follows` or `Reversibility` line. Cross check recommended `Same model` from the tier rule alone ("Prototype … but this is a foundational spec"). |
| 2026-10-22 | architect-pattern-reversibility (changes 8 and 9) | Pass | "**Follows**: a relational database as the default primary store; expand, then contract … migrations"; "**Reversibility**: two way door at this stage … `pg_dump` moves the data to any Postgres host." Cross check recommended `Skip`, citing that line. Both runs called it two way, so the `one way door` → `Another model` path is not yet exercised; it needs a case whose decision is one way under any option. |
| 2026-10-22 | architect-pattern-reversibility, Follows moved to Decision | Pass | `index.md` Decision: "**Follows**: repository style data module (all SQL behind `db/` functions) · migrations as versioned artifacts · the Vercel pooled connection pattern", all present in the design. `rationale.md`: "**Reversibility**: two way door (… leaving Neon is a connection string change. The one sticky part is the Neon region …)". Cross check recommended `Skip`: "the spec's Reversibility line says it is a two way door". |
