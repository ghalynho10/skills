# /overview update (reference document)

The `update` mode of `/overview`, and its default: keeps `docs/overview.md` current as the project's single reference document. Follow it fully.

## What this mode does

**Your role:** the person who can explain this whole project to someone who has never seen it, without hand waving and without assuming they already know the domain. You compose, you do not decide. Every fact you write traces to something the durable files already say; where they say nothing, you say so plainly rather than filling the gap with something plausible.

Reads the durable files, composes them into a picture, and updates `docs/overview.md`. Read only on code and on every file except the overview itself.

Run this after a feature reaches done, at the same point in the flow where `/sync` runs. Also run it after any decision that changes the project's shape (a new stack piece, a replaced provider, a new major surface), even when no feature closed.

## Execution

### Step 1: Read the durable files, cheaply

Keep this pass light; you are composing, not auditing.

- Root `AGENTS.md` (fall back to `CLAUDE.md`, else note MISSING), plus any nested `<area>/AGENTS.md` whose area the project's shape depends on.
- The current `docs/scope/`: feature names and statuses. This is where `## Current state` comes from, so read it accurately.
- `docs/specs/`: **titles and status lines first**. Open in full only the specs whose decisions shaped the project's overall shape (stack choices, foundational architecture, cross cutting standards, a replaced provider). Do not open every spec; a project with forty specs does not have forty shape defining decisions.
- For a directory spec, the `index.md` `## Decision` section is enough; go to `rationale.md` only when you need the why for the `## Decisions that shaped it` section and the index does not carry it.

### Step 2: Read the existing overview

Read `docs/overview.md` if it exists. **You are updating it, not rewriting it from nothing.** Preserve wording that is still accurate. A diff a person can follow beats a fresh draft every time; if every run rewrites the whole file, nobody can see what actually changed.

**First run only**: if no overview exists and the project's purpose is not clearly derivable from what you read in Step 1, ask the one purpose question:

> "What is this project for, in a sentence or two, in your own words? I can derive the rest."

Wait for the answer. Otherwise derive it and do not ask.

### Step 3: Write the overview

Read `overview-template.md` now (only now, so it does not sit in context through Steps 1 and 2) and follow its section structure and field guidance. Write or update `docs/overview.md`.

Rules that bind every section:

- **Write for zero prior context.** The reader has not seen the codebase, the scope, or any spec. Domain jargon gets explained the first time it appears.
- **Never invent a why.** If a decision's reasoning is not recorded in any spec, write what was decided and mark the reasoning as not recorded. An invented rationale reads as authoritative to a future reader and is worse than an admitted gap.
- **Load bearing only, in the decisions section.** If every decision is listed, none of them stand out. A reader should finish that section understanding why the project looks like this rather than some other way, not holding a complete decision log. That is what `docs/specs/` is for, and each entry links there.
- **Omit empty sections.** An empty heading is worse than no heading.
- **Current state comes from scope, not from memory.** It is the section most likely to go stale, so derive it fresh every run.

### Step 4: Report

```
## /overview update complete

Updated: <sections touched>
Added: <anything newly documented>   (omit if none)
Now reflects: <n> features in scope, <n> load bearing decisions
```

If nothing had changed since the last run, say so in one line and leave the file alone rather than rewriting it with a new date.
