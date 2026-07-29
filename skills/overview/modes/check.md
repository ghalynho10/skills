# /overview check (drift report)

The `check` mode of `/overview`: reports where `docs/overview.md` has gone stale against what scope and specs now show. Follow it fully.

## What this mode does

**Your role:** the reader who notices the document no longer matches the project. You report; you do not fix. The point of a check is that a person sees what changed and decides what to do about it, which a silent repair takes away from them.

Read only, everywhere. Writes nothing, not even the overview.

Run this before showing the overview to anyone who will rely on it, or any time you suspect it has drifted.

## Prerequisite

If `docs/overview.md` does not exist, say so in one line and stop: there is nothing to check, run `/overview update` to create it.

## Execution

### Step 1: Read

- `docs/overview.md` in full.
- The current `docs/scope/`: feature names and statuses.
- `docs/specs/`: **status lines and titles only.** You are looking for superseded decisions and new shape defining ones, not re reading the reasoning.

### Step 2: Compare and report drift

Look for these, and only these:

- **Features the overview describes that scope no longer shows**, or now shows under a different name, or shows as cut.
- **Decisions the overview cites whose spec is now `Superseded`.** The overview is stating a decision that has since been replaced, which is the most damaging kind of drift because it reads as current.
- **Decisions the overview cites whose spec is still `Assumed`.** The overview is presenting a provisional decision as settled; flag it so it is either ratified via `/architect` or softened in the overview.
- **Features scope shows as done that the overview never mentions.** The project grew and the document did not.
- **Anything in `## Current state` that no longer matches scope.** This section goes stale fastest; check it line by line.
- **New load bearing decisions with no mention in `## Decisions that shaped it`.** A stack change or a replaced provider that the overview does not explain.

Ignore wording, tone, and structure. This is a factual drift check, not an edit pass; do not report that a section could be phrased better.

### Step 3: Report

```
## /overview check

Drift found: <n> items   (or "None, the overview matches scope and specs.")
- <what is stale, one line each, with the file or feature it refers to>

Recommend: /overview update   (omit if no drift)
```

Never fix anything in this mode, even something trivial. If the engineer wants it fixed, that is `/overview update`, a normal next step.
