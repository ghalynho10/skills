---
name: overview
allowed-tools: Bash, Read, Grep, Glob, Write, Edit
argument-hint: <update | story | check>
description: >-
  Run /overview update after a feature ships to keep docs/overview.md current,
  a living document explaining what this project is, how it is built, and why
  it looks the way it does, written for someone with no prior context. Run
  /overview story when the project is far enough along to want a plain prose
  telling of it for a person. Run /overview check to find where the document
  has drifted from what scope and specs now show. Bare /overview runs update.
---

## Output style (plain words, no dashes, no hyphens)

Write everything this skill produces, files and messages alike, in plain simple language. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.

## What this skill does

The pipeline's files each answer one narrow question. `AGENTS.md` says how to work in this codebase. `docs/scope/` says what is being built. `docs/specs/` say what was decided and why, one decision at a time. None of them answer the broadest question: what *is* this project, taken as a whole, for someone who has never seen it.

`/overview` owns that answer, in three modes:

- **`update`** (the default, run often): keeps `docs/overview.md` current. A reference document: what the project does, who it is for, how it is built, what the moving parts are, and the reasoning behind the shape it has taken. Structured, skimmable, and complete enough to hand to a new agent session or a new engineer as full context. Read only on code; owns the overview file.
- **`story`** (run rarely, on demand): writes `docs/project-story.md`, a plain prose telling of the same project for a person, not a reference. No headings to skim, no tables. What problem it solves, how it came together, what the interesting decisions were. Written so a stranger who has never touched the codebase understands what was built and why it mattered.
- **`check`** (run when you suspect drift): reads `docs/overview.md` against the current `docs/scope/` and `docs/specs/` and reports where the document has gone stale. Reports only, changes nothing.

### Why both a reference and a story

The reference document is the one that earns its keep day to day: it is what you paste into a fresh chat, what a new teammate reads first, and what keeps a long project explainable to itself. The story is a different artifact for a different reader, a person who wants to understand the project without needing to work on it. Generating the story from a well kept reference document is easy; reconstructing either one from a finished codebase months later is not, which is why `update` runs as you go rather than only at the end.

## Pick the mode (route before doing anything else)

This is the first step, always, before reading any mode file or touching the repo. Look at what followed `/overview`:

- The argument **starts with `update`** → reference document update. Read `modes/update.md` and follow it fully.
- The argument **starts with `story`** → narrative telling. Read `modes/story.md` and follow it fully.
- The argument **starts with `check`** → drift report. Read `modes/check.md` and follow it fully.
- **Nothing followed, or anything ambiguous** → run `update`, the default. Say so plainly in the first line of your reply so the engineer always knows which mode ran and that the others exist:

  > "Running `update`, the default. Use `/overview story` for a prose telling, or `/overview check` to find drift."

  Do not present a menu and do not stop to ask. `update` is the mode this skill exists to run often, it is safe (it updates one document you own and reports the diff), and a prompt every time would make the habit too expensive to keep. The other two modes are opt in by name.

If a topic or scope was passed with a mode word (`/overview update auth`), carry it through as the target once routed.

Do not run two modes in one invocation. If the engineer asks for both `update` and `story`, do `update` first (the story reads the reference document, so it should read a current one), then offer `story` as the next step.

## Asks vs acts

**Acts**, in all three modes. It reads the durable files, writes its document, and reports what changed. It asks only in two cases:

- On the very first `update` run, when `docs/overview.md` does not yet exist and the project's purpose cannot be derived from scope, specs, or `AGENTS.md`, it asks one question: what is this project for, in a sentence or two, in your own words. Everything else it infers.
- On `story`, it confirms the intended reader before writing, because that choice changes the whole telling. This pause stays: `story` is rare, expensive to get wrong, and the answer genuinely changes the output.

It never invents purpose, users, or reasoning that no file supports. Where the reasoning behind something genuinely is not recorded anywhere, it writes what was decided and marks the why as not recorded, rather than inventing a plausible one.

## Artifact ownership

Owns `docs/overview.md` (written by `update`) and `docs/project-story.md` (written by `story`). `check` writes nothing. Never edits `docs/scope/`, `docs/specs/`, `AGENTS.md`, or `CLAUDE.md`; those stay owned by `scope`, `architect`, and `audit`/`sync`.

This skill is downstream of all of them: it reads what they own and composes a picture from it. It is never a source of truth for a decision. If the overview and a spec disagree, the spec is right and the overview is stale; `check` exists to catch exactly that.

**Artifact base.** These live under `docs/` by default. If `docs/` is a published docs site (`docusaurus.config.*`, `.vitepress/`, `mkdocs.yml`, Astro Starlight, or Nextra detected), use `.workflow/` instead. Always follow whichever base already exists (paths here assume `docs/`).

## Portability (any OS, any agent)

Any Agent Skills client on macOS, Linux, or Windows. No CLI beyond your agent's own file tools is required; this skill never shells out. Use your knowledge of today's date for the Last updated line.

Bundled files live in this skill's folder: `modes/update.md`, `modes/story.md`, `modes/check.md`, and `overview-template.md`. Read only the mode file you routed to, and read `overview-template.md` only at write time (in `update`), so the structure does not sit in context while you are reading the durable files.

## Reference files

- `modes/update.md`: the reference document mode
- `modes/story.md`: the narrative mode
- `modes/check.md`: the drift report mode
- `overview-template.md`: the section structure for `docs/overview.md`, read at write time by `update` only
