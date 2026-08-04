# Engineering Workflow Skills

A set of [Agent Skills](https://agentskills.io) that take a change from a vague idea to shipped, verified, documented code, for any AI coding agent. One skill per phase. Run only the ones a change needs, in any order.

The state lives in files (a scope, specs, AGENTS.md, tests), not in a chat session. So work survives across sessions, picks up where it left off, and works for a whole team.

```
idea → /scope → /audit → /architect → /develop → /check verify → /test → /check review → /document → /sync
```

Run `/debug` anytime something breaks, or `/recover` when it is not yet clear *what kind* of thing broke. Run a bare `/scope` anytime to see where things stand.

> 📖 **Want the full picture?** Read the **[Workflow Guide](docs/workflow-guide.md)** — a plain-language walkthrough of every skill, the files that carry the work, who owns what, and one idea followed all the way from scope to shipped.

## About this fork

This is a fork of [jsmastery-pro/skills](https://github.com/jsmastery-pro/skills) with five changes:

- **`/checkpoint`** (new) — captures the in-session working memory the pipeline's files don't own: open threads, ruled-out approaches, and standing instructions. Saves to `docs/session-notes.md` and confirms before restoring. Fills the gap between "state lives in files" and the parts of a session that never became a spec or scope row.
- **`/overview`** (new) — the project-wide picture none of the other files hold. `update` keeps `docs/overview.md` current as a reference document, `story` writes a prose telling for a person, `check` reports drift. Bare `/overview` runs `update`.
- **`/wayfinder`** (new) — for the rare effort too big and too foggy for `/scope`'s interview to sharpen in one sitting. Charts a map of investigation tickets under `docs/wayfinding/`, resolved one per session, then hands the cleared result back to `/scope` or `/architect`. Adapted from mattpocock/skills' `wayfinder`, with the issue tracker and its external skill dependencies replaced by plain files native to this pipeline.
- **`/recover`** (new) — diagnoses *what kind* of failure occurred before prescribing a response: an isolated bug, a session that has gone wrong through repeated patching, or a foundation resting on a wrong assumption. Ported from the older [jsmastery-pro/jsm-agent-skill](https://github.com/jsmastery-pro/jsm-agent-skill) repo and adapted to this fork.
- **`/debug`** (modified) — a stricter bar for Step 1: a reproduction must be red-capable, deterministic or pinned to a high rate, and already run once before moving on. Adds an explicit escalation protocol for flaky bugs.

Everything else is unchanged from upstream. The added skills are downstream of the existing ones: they read what `scope`, `architect`, and `audit`/`sync` own and never write to a file another skill owns. The one shared file is `docs/session-notes.md`, split by section between `/checkpoint` and `/recover`; see [What gets written, and where](#what-gets-written-and-where).

Note that the [Workflow Guide](docs/workflow-guide.md) is upstream's and does not cover this fork's added skills; this README is the reference for those.

## The skills

| Skill | What it does |
|---|---|
| `scope` | Turns a product idea into a living, coarse scope and keeps it current as you ship. |
| `audit` | Writes the AGENTS.md context files every other skill reads. |
| `architect` | Makes a load bearing decision and writes it as a build spec in `docs/specs/`. |
| `develop` | Builds a feature, UI or backend, from its spec. Gates to `/architect` if a decision is owed. |
| `check` | Confirms a change before merge. `/check verify` runs the real app; `/check review` reads the code on a second model. |
| `test` | Writes a test suite for the code you just changed. |
| `document` | Writes the PR text, changelog, release note, or postmortem from the real diff. |
| `sync` | Keeps AGENTS.md, the scope, and spec statuses current after a change. |
| `debug` | Finds and fixes the root cause of a bug, then hands a regression test to `/test`. |
| `overview` | Keeps a project-wide reference document current; can also write a prose telling. *(fork addition)* |
| `checkpoint` | Saves and restores in-session notes that aren't yet a spec or scope row. *(fork addition)* |
| `wayfinder` | Charts and resolves large, foggy, multi-session efforts as a map of tickets. *(fork addition)* |
| `recover` | Diagnoses what kind of failure occurred before responding to it. *(fork addition)* |

Hardening (systems level failure mode analysis) is temporarily removed and will return as a system design specialization.

## Install

Uses [npx skills](https://github.com/vercel-labs/skills). Pick your agent:

```bash
# Claude Code (installs into .claude/skills, then restart Claude Code)
npx skills@latest add ghalynho10/skills -a claude-code

# Generic .agents/skills, read by Codex and other agents
npx skills@latest add ghalynho10/skills
```

Install individual skills:

```bash
npx skills@latest add ghalynho10/skills/skills/checkpoint
npx skills@latest add ghalynho10/skills/skills/overview
npx skills@latest add ghalynho10/skills/skills/wayfinder
npx skills@latest add ghalynho10/skills/skills/recover
npx skills@latest add ghalynho10/skills/skills/debug
```

`/recover` and `/checkpoint` share `docs/session-notes.md` by section, so install both together or neither. Installing `/recover` against an older `/checkpoint` risks reset notes being dropped on the next `/checkpoint save`.

Works on any Agent Skills client (Claude Code, Cursor, Codex, Gemini CLI, and [more](https://agentskills.io/clients)). Commit the installed skills folder to share the workflow with your team.

Each skill's instructions live in its `SKILL.md`, which is what every client reads. The `agents/openai.yaml` beside it is interface metadata only (the name, blurb, and opening prompt Codex shows in its agent picker); it carries no logic of its own.

## Where to start

**New product (greenfield):** `/scope` the idea, then `/architect` the stack, then scaffold the project, then `/audit` to seed AGENTS.md from the real project, then the feature loop. The stack is decided and the project scaffolded before `/audit` runs, so it reads a real project, not an empty one.

**Existing codebase (brownfield):** `/audit` first so every skill understands your project, then `/scope` the next slice on top of what exists, then the feature loop.

**Any single change:** run only what it needs. A bug goes straight to `/debug`. A small change can be `/develop` then `/check verify`.

**Monorepo:** everything scopes to the target workspace, which has its own AGENTS.md, scope, stack, and commands.

### The feature loop

```
/architect → /develop → /check verify → /test → /check review → /document → /sync
```

At the end of `/scope` you also pick a **workflow depth** for the project (override per feature anytime): `Prototype` (just `/develop`, self-checked, for throwaway work), `Alpha` (adds `/check verify`), `Beta` (adds `/test`), or `GA` (adds a fresh-model `/check review` and `/document`). The depth is a *suggested* checking tail after `/develop`, never a track you're locked onto: you're in charge, you run or skip any step and mark a feature `done` when you decide it is. The one thing the workflow asks — at every depth — is that a load-bearing decision gets written down (`/architect`), not that any check be run.

`/scope` fixes what to build. `/architect` designs how, as a spec whose acceptance criteria are the contract; every later step traces back to that contract. `/develop` gates on the spec: if building would mean inventing an undecided design, provider, or data model, it stops and routes you to `/architect`. You can override and build anyway, but the override is not free: the assumption is recorded as an `Assumed` spec in `docs/specs/` and flagged on the feature until `/architect` ratifies it. The flag doesn't block you from marking `done` — it's a standing reminder that a decision still owes ratification, so it never gets silently lost in chat.

The gate is layered, not magic: `/architect` names the source of every value a feature must produce (so gaps surface at design time), `/develop` checks that coverage again before building, and at Beta+ `/architect` recommends running an independent cross-model critic over the spec for decisions it never settled (you decide, and you decide on any gaps it finds). It's a strong, defense-in-depth gate that catches the vast majority — not an absolute guarantee, no prompt can be. Behavioral correctness is caught by the `/check verify` and `/test` layers.

Around the loop, the fork additions run on their own cadence: `/overview update` alongside `/sync` when a feature closes, `/checkpoint save` at the end of a session that leaves open threads behind, `/wayfinder` off to the side entirely when a patch of the plan is too foggy for `/scope` to sharpen in the room, and `/recover` only when something has gone wrong.

### When something goes wrong

`/debug` and `/recover` are not interchangeable, and reaching for the wrong one is itself a failure mode.

`/debug` assumes the code is wrong and finds the root cause. That is right for most failures. `/recover` assumes nothing yet: it asks what you expected, what happened, and **how many times you have already tried to fix it**, then decides whether this is an ordinary bug (handing off to `/debug`), a session that has gone wrong through repeated patching (hard reset), or a foundation resting on a wrong assumption (rethink).

The practical rule, worth writing into your project's AGENTS.md as a circuit breaker:

> If the same problem persists after one corrective prompt, stop immediately and run `/recover`.

Routing that straight to `/debug` instead throws away the signal the breaker just produced and assumes an ordinary bug every time, which is the drift the breaker exists to catch.

## What gets written, and where

| Artifact | Path | Owner |
|---|---|---|
| Scope | `docs/scope/` | scope |
| Specs | `docs/specs/` | architect |
| Context files | AGENTS.md (plus a thin CLAUDE.md pointer) | audit, kept current by sync |
| Design system | `design.md` (art direction; token values live in CSS) | develop |
| Review findings | `docs/reviews/` | check |
| Tests | your test dirs | test |
| App code | your source tree | develop |
| Human docs | PR body, CHANGELOG.md, `docs/releases/`, `docs/postmortems/` | document |
| Project overview | `docs/overview.md`, `docs/project-story.md` | overview *(fork addition)* |
| Session notes | `docs/session-notes.md` | **shared by section** *(fork addition)* |
| Wayfinding maps and tickets | `docs/wayfinding/` | wayfinder *(fork addition)* |

`docs/session-notes.md` is the one file in this pipeline with more than one owner. Ownership is per section:

| Section | Owner |
|---|---|
| `## Open threads` | checkpoint |
| `## Ruled out` | checkpoint |
| `## Standing instructions` | checkpoint |
| `## Reset notes` | recover |
| anything else | whichever skill wrote it |

Each skill reads the whole file, mutates only its own sections, and writes every other section back unchanged. No skill regenerates the file from a template of the sections it knows about; that is how another skill's content silently disappears. Entry bodies must not contain top-level (`##`) headings, since section boundaries are found by scanning for them.

If `docs/` is a published docs site, these move to `.workflow/` so they do not ship with your site. Because state lives in files, each skill suggests `/clear` at handoffs, so a fresh session reads from disk again and long chats do not pile up cost.

## Skill reference

For each skill: what it does, and when to run it.

**scope**: Turns an idea into a living, coarse plan of what to build, in order.
When: to start a new product or plan the next slice. Greenfield: run it first. Brownfield: it enrolls what already exists, then plans the new work. Monorepo: writes one scope per workspace.

**audit**: Writes the AGENTS.md context files that give every skill your project's stack, commands, and conventions.
When: brownfield, run it first. Greenfield, run it after the stack is chosen and the project is scaffolded. Monorepo: gives each workspace its own nested AGENTS.md.

**architect**: Runs a deep design conversation and writes the decision as a build spec.
When: a load bearing choice is unmade (a stack, a data model, a provider, a page design), or `/develop` says a decision is owed. Greenfield: it decides the stack first. Monorepo: reads the target workspace's stack.

**develop**: Builds a feature, UI or backend, from its spec, runs migrations, and advances the scope.
When: after the decision exists. It gates to `/architect` if a design is owed. Monorepo: builds inside the target workspace using its commands.

**check**: Confirms a change before merge, in two modes.
When: `/check verify` after `/develop` to run the real app and prove the feature works against the spec; `/check review` before a PR for a senior review on a different model than wrote the code. Any project type.

**test**: Writes a senior test suite for your uncommitted change and saves your framework choice.
When: after building a feature or fixing a bug. Monorepo: resolves the framework per package.

**document**: Writes the human facing prose (PR, changelog, release note, postmortem) from the real diff.
When: a finished change needs writing up. Any project type.

**sync**: Reconciles AGENTS.md, the scope, and spec statuses to what the repo now shows.
When: the last step around merge. Monorepo: reconciles the right workspace.

**debug**: Runs a disciplined root cause loop and hands a regression test to `/test`.
When: anytime something is failing, throwing, or behaving wrong, *and you already know it is a code-level bug*. If you don't, or if a fix attempt has already failed, start at `/recover` instead. Not tied to project type. *(This fork raises the bar on the reproduction step; see About this fork.)*

**overview** *(fork addition)*: Keeps the project-wide picture that no other file holds, in three modes.
When: `/overview update` (the default) after a feature closes, alongside `/sync`, or after any decision that changes the project's shape; `/overview story` at a milestone or completion, for a prose telling aimed at a person; `/overview check` when you suspect the document has drifted from scope and specs. Downstream of everything: if the overview and a spec disagree, the spec is right.

**checkpoint** *(fork addition)*: Saves and restores the in-session residue that never became a durable file.
When: `/checkpoint save` before ending a session that leaves open threads, ruled-out approaches, or standing instructions behind; `/checkpoint restore` at the start of a session picking that work back up, and always after a `/recover` hard reset. Not every session needs it: if everything of value already landed in a spec or scope row, there is nothing to save.

**wayfinder** *(fork addition)*: Charts a large, foggy, multi-session effort as a map of investigation tickets and resolves them one at a time.
When: `/scope`'s interview cannot sharpen one part of the plan in the room, because each answer waits on some other unsettled thing. Charts the effort under `docs/wayfinding/`, works one ticket per session, and hands the cleared result back to `/scope` or `/architect`. Most projects never need it. Adapted from mattpocock/skills; the issue tracker and its external skill dependencies are replaced by plain files and inline ticket types native to this pipeline.

**recover** *(fork addition)*: Diagnoses what kind of failure occurred, then prescribes the response that fits.
When: something has gone wrong and it is not obvious whether the code is at fault, the session is, or the approach is. Especially after a corrective prompt has already failed once. Three modes: an ordinary bug hands off to `/debug`; a session gone wrong gets a hard reset (a note into `docs/session-notes.md`, end the session, restart with `/checkpoint restore`); a wrong foundation gets a rethink (name the assumption, propose the correction, change nothing until you agree). Ported from jsmastery-pro/jsm-agent-skill; split into a router plus modes, mode one delegated to this fork's `/debug` rather than reimplemented, and its `/remember restore` handoff swapped for `/checkpoint restore`.

## Learn more

The **[Workflow Guide](docs/workflow-guide.md)** is the deep dive: how the scope, specs, AGENTS.md, and design system live and who is allowed to change them; the acceptance-criteria thread that ties every stage together; a full worked example from idea to shipped; the debug loop; and how the same flow runs on an existing codebase and in a monorepo. It is upstream's guide and predates this fork's added skills.

---

Built with the [Agent Skills](https://agentskills.io) open format.