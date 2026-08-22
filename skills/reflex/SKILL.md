---
name: reflex
allowed-tools: Read, Grep, Glob, Write, Edit
argument-hint: <the rule in words | audit | (nothing)>
description: >-
  Run /reflex right after correcting an agent, to turn that correction into a
  standing rule in docs/reflexes.md that later sessions read, so the same
  correction is not given twice. Run /reflex audit to find contradictions,
  duplicates, rules gone stale, and rules that have become plain conventions.
---

## Output style (plain words, no dashes, no hyphens)

Write everything this skill produces, files and messages alike, in plain simple language. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.

## What this skill does

Captures the rule that exists only because you said it out loud, and would otherwise die at `/clear`.

- **capture** (the default, run right after a correction): turns what you just said into one rule, checks it against the rules already there, and writes it once you confirm the wording.
- **audit**: finds contradictions, duplicates, rules pointing at things that no longer exist, and rules that have become plain conventions.

## Pick the mode (route before doing anything else)

Look at what followed `/reflex`:

- **`audit`** (any case), alone or with only filler after it (`audit please`, `audit the file`) → _Execution: audit_.
- **A rule in words** → _Execution: capture_, using those words. Words that begin with `audit` but state a rule ("audit the schema before every migration") are a rule, not the mode.
- **Nothing** → _Execution: capture_, finding the rule yourself.

## What belongs here, and what does not

| The correction you want to keep                            | Where it belongs                                              |
| ---------------------------------------------------------- | ------------------------------------------------------------- |
| A rule for how work is done here, one correction's worth    | `docs/reflexes.md`, this skill                                 |
| Mechanical and deterministic, needs no judgment             | a hook in your agent's own config, so it **runs** rather than being remembered. This one leaves the pipeline: name the hook, do not write it |
| A standard to enforce across the whole codebase             | a spec, `/architect` in `CROSS-CUTTING` mode                    |
| A decision about what to build or why                       | a spec, `/architect`                                            |
| A fact about the stack or conventions, visible in the code  | `AGENTS.md`, `/audit` or `/sync`                                |
| True only for this session or this week                     | `docs/session-notes.md`, `/checkpoint save`                     |
| A one off fix that generalizes to nothing                   | nowhere, do not capture it                                      |

The first two rows are the ones most often got wrong. "Format after every edit" and "run the tests before committing" written here are a rule an agent may or may not honor; wired as a hook they are enforced. And one correction is a reflex, while a pattern you want standardized everywhere is a deliberated decision, so it is owed a spec.

## Artifact ownership

Owns `docs/reflexes.md` entirely, both modes. Never edits `AGENTS.md`, `docs/scope/`, `docs/specs/`, `docs/session-notes.md`, or source code. The root `AGENTS.md` pointer and the root `CLAUDE.md` import of it, which are what make this file get read, are both written by `/audit`.

## Execution: capture

### Step 1: find the candidate rule

**If words were given**, that is the candidate.

**If nothing was given**, look back over this session for a correction that **states a general rule**: you were told to always or never do something, or told an approach was wrong in a way that will apply again. Take the most recent one that generalizes, not simply the most recent one. A local fix ("put that in `utils.ts`") is not a candidate.

If nothing qualifies, say so plainly and write nothing: "No correction in this session that generalizes, nothing to capture." Do not manufacture a rule to have something to write.

### Step 2: route it

Check the candidate against the table above. If it belongs elsewhere, say where in one line and stop.

### Step 3: check it against the rules already there

Read `docs/reflexes.md` if it exists. Count the rules: lines beginning `- ` under `## Reflexes`.

At or past 20 rules, say so and recommend `audit` first. Continue on the engineer's go, and work the checks below either way.

Then check these in order, first match wins:

- **Narrower case** of an existing rule → tighten that rule in place. Confirm at Step 4, and Step 5 replaces that one line instead of appending.
- **Already covered**, exactly, by an existing rule → say so, name the rule, write nothing, stop.
- **Contradicts** an existing rule → show both and ask which holds now, recommending the new one, since it came from a live correction and the old one evidently did not hold. If the new one holds, Step 5 replaces the old line; if the old one holds, write nothing and stop.
- **No match** → go to Step 4.

If `docs/session-notes.md` has the same rule under `## Standing instructions`, say so in the report: it belongs in one file, and now that it is permanent, this one.

### Step 4: draft the line and confirm it

Draft one line:

```
- When <trigger>, <action>. (added <YYYY MM DD>)
```

Add at most one clause of why, and only where it stops the rule being applied in the wrong place.

Show the exact line and ask, with the recommendation:

> "Capture this as a standing rule? Recommended: yes, it came from a live correction and it generalizes. It will be read by every later session."

Wait for the answer. If corrected, adjust the wording and show it again.

### Step 5: write it

Write the confirmed line to `docs/reflexes.md` under `## Reflexes`, creating the file with that heading if missing: append it, or replace the single line Step 3 named. Touch no other line, and do not reorder or reformat the file.

If root `AGENTS.md` has no pointer to the reflexes file, or root `CLAUDE.md` is missing or has no import of it, say so in the report and recommend `/audit`.

## Execution: audit

1. Read the reflexes file (`docs/reflexes.md`, or the Artifact base below). If it does not exist, say so and stop.
2. Mark each rule, most serious first where more than one fits:
   - **contradicts** another rule, name both
   - **duplicate or near duplicate** of another, name both
   - **stale**, its trigger names a file or path that no longer exists. Check with your file tools first; if it names a command or flag you cannot check, leave it alone
   - **graduation candidate**, among the oldest by `(added ...)` date and never contradicted since, so it may be a plain convention now. Compare the dates to each other; do not compute an age. A rule with no date is never a candidate
   - **holds**, leave it alone
3. Report the change list: per flagged rule one action (**remove**, **merge into `<rule>`**, or **rewrite as `<line>`**), a one line why, and the resulting count: "18 rules now, 12 after these changes." Graduation is the engineer's call, never a recommendation to remove.
4. Confirm before touching the file. Apply exactly what was agreed, nothing more.
5. For a rule the engineer graduates, move it under a `## Graduating` heading in `docs/reflexes.md` and print it as a ready to paste bullet for root `AGENTS.md`. **The engineer moves it across; no skill does.** `/audit` derives its root gaps from scanning code and never reads this file, so a rule dropped here on the promise that some later run will collect it is simply lost. Delete it from `## Graduating` once they confirm it landed. Rules there do not count toward the 20.

## Portability (any OS, any agent)

Any Agent Skills client on macOS, Linux, or Windows. No CLI is required; this skill never shells out. `docs/reflexes.md` is a plain path, created with your write tool if missing.

**Artifact base.** `docs/` by default. If `docs/` is a published docs site (`docusaurus.config.*`, `.vitepress/`, `mkdocs.yml`, Astro Starlight, or Nextra detected), use `.workflow/reflexes.md` instead. Follow whichever base the project already uses.

## Report format

For capture:

```
## /reflex captured

Added or edited: <the rule as written>
File: docs/reflexes.md (<n> rules, after this write)

Heads up: <contradiction resolved, duplicate left in session notes, or no AGENTS.md pointer or CLAUDE.md import yet, run /audit>   (omit if none)
```

For a capture that wrote nothing, one line is the whole report: the rule, and why. Which file or mechanism owns it instead, or the existing rule that already covers it, or that no correction in this session generalized.

For audit:

```
## /reflex audit

<n> rules, <n> flagged: <n> contradicting, <n> duplicate, <n> stale, <n> graduation candidates
Applied: <n> removed, <n> rewritten, <n> merged, leaving <n>
Next: move <n> graduating rules into AGENTS.md yourself, then /reflex to clear them   (omit if none)
```
