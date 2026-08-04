---
name: checkpoint
allowed-tools: Bash, Read, Grep, Glob, Write, Edit
argument-hint: <save | restore>
description: >-
  Run /checkpoint save before ending a session that has open threads not yet
  worth a spec or scope row: hypotheses ruled out, standing instructions,
  things deliberately deferred. Run /checkpoint restore at the start of a
  new session to recall them and confirm before continuing. Never restates
  what docs/scope, docs/specs, or AGENTS.md already own; only covers what
  those files do not.
---

## Output style (plain words, no dashes, no hyphens)

Write everything this skill produces, files and messages alike, in plain simple language. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.

## What this skill does

The pipeline already keeps durable state in files: `docs/scope/` for what to build, `docs/specs/` for decisions, `AGENTS.md` for stack and conventions. A new session can pick up almost everything by reading those. What they do not hold is the in session residue that has not earned a place in any of them yet: a hypothesis you tested and ruled out, a standing instruction that is not a decision ("do not touch the payments module today"), an open question you are still turning over, or a tradeoff you talked through but decided is not spec worthy yet.

`/checkpoint` exists only for that residue. It is deliberately small.

- **`save`**: writes or updates a short, plain notes file capturing only what the owned files do not already say.
- **`restore`**: reads that file plus a light pass over current scope and recent specs, then states back what it found and asks you to confirm before continuing.

This is not a replacement for `/sync`. `/sync` reconciles the durable files to match reality. `/checkpoint` captures the parts of a session that were never meant to become durable files in the first place, but would otherwise be lost when the session ends.

## When to use

- **`save`**: before ending a session where something worth keeping is not yet in scope, a spec, or AGENTS.md, and would be a waste to rediscover or re litigate next time. Not every session needs this. If everything of value already landed in a spec or scope row, there is nothing to save.
- **`restore`**: at the start of a session picking up recent work, when you want the open threads back, not just the current state of the files.

## Asks vs acts

**`save`** acts. It writes the notes file without asking, but only ever appends or updates entries; it never deletes an entry itself; stale entries are removed by you, or by `save` itself once it confirms the thing they refer to now has a home in scope or a spec (see _Aging out_).

**`restore`** acts, then confirms. It reads and reports what it found, then explicitly asks you to confirm the picture is accurate before continuing, the one place this skill pauses on purpose. A stale or wrong carried over assumption is worse than no memory at all.

## Artifact ownership

`docs/session-notes.md` is a **shared file**, not this skill's private document. This distinction matters and is the source of the worst failure mode available here, so it is stated plainly.

Ownership splits two ways, writing and lifecycle, which is finer than one owner per section:

| Section                    | Written by               | Lifecycle                   |
| -------------------------- | ------------------------ | --------------------------- |
| `## Open threads`          | `/checkpoint`            | `/checkpoint`               |
| `## Ruled out`             | `/checkpoint`            | `/checkpoint`               |
| `## Standing instructions` | `/checkpoint`            | `/checkpoint`               |
| `## Reset notes`           | `/recover`               | `/checkpoint`, removal only |
| anything else              | whichever skill wrote it | that skill                  |

Reset notes are the one case where the two come apart, deliberately. `/recover` writes them, under pressure, at the end of a session that went wrong. This skill removes them once they are no longer needed, because the skill deciding a note has stopped mattering should not be the one that wrote it. This skill never edits the content of a reset note, only removes an entry whole.

**This skill may read the whole file. It may only mutate entries under the three headings it writes, plus remove a spent reset note.** Every other top level section, whether written by `/recover` or by a skill added later, must survive a `save` byte for byte. Do not reorder sections, do not normalize headings, and do not rewrite the file from a template of the headings this skill knows about. Regenerating the file from its own model is how another skill's content silently disappears.

Never edits `docs/scope/`, `docs/specs/`, or `AGENTS.md`; those stay owned by `scope`, `architect`, and `audit`/`sync`.

`docs/session-notes.md` is a flat file, not a directory, and stays short by design; if it is getting long, that is a signal entries should have graduated to specs or scope rows already, not a reason to keep appending.

**One constraint on any section written to this file, by any skill:** entry bodies must not contain top level (`##`) headings of their own, because section boundaries in this file are found by scanning for them. Use plain labeled lines inside an entry instead.

## Execution

### `/checkpoint save`

1. Read the current `docs/session-notes.md` if it exists. Read the **whole** file, including sections this skill does not own; you need them intact to write them back.
2. Ask yourself, based on the session so far, whether there is anything genuinely not covered by scope, a spec, or `AGENTS.md`. If nothing qualifies, say so plainly and write nothing: "Nothing to save, everything of value is already in scope or a spec."
3. For anything that qualifies, write a short entry under one of three headings:

   ```
   ## Open threads
   - <a question or direction still being worked out, one line, with enough context to pick back up>

   ## Ruled out
   - <a hypothesis or approach tried and rejected, and why, so it is not re tried>

   ## Standing instructions
   - <something you told the agent that is not a decision worth a spec, e.g. a temporary constraint or preference>
   ```

   Omit any heading with nothing under it. Keep each entry to one or two lines; this file is a pointer back into your memory, not a transcript.

4. **Aging out, section scoped.** Before writing, check whether any existing entry **under the three headings this skill owns** now clearly has a home in `docs/scope/` or `docs/specs/` (the thing it described got built, decided, or written up properly). If so, remove that entry; it has graduated and repeating it here would be a second source of truth.

   Aging out never runs against a section this skill does not own, with one exception, below.

5. **Reset notes, the one exception.** A `## Reset notes` entry written by `/recover` may be removed by `save`, but only once its content is clearly represented in `docs/scope/`, `docs/specs/`, or the session has demonstrably moved past it (the feature it describes was rebuilt and closed). This mirrors the aging out rule for owned sections rather than inventing a second lifecycle. When in doubt, leave it; a stale reset note is a minor annoyance, a deleted one is lost context at the exact moment someone needed it.

6. Write the file back, with all non owned sections preserved exactly as read. Report what was added and what aged out, one line each.

### `/checkpoint restore`

1. Read `docs/session-notes.md`. If it does not exist, say so and stop; there is nothing to restore, proceed normally.
2. Read the current state of `docs/scope/` (feature list and statuses) and the most recently modified files in `docs/specs/`, cheaply, names and statuses only, not full contents, the same light touch other skills in this pipeline use for pre flight.
3. Present a short combined picture: the open threads, ruled out approaches, and standing instructions from the notes file, plus a one line summary of what scope and specs currently show.

   **If a `## Reset notes` section is present, lead with it.** A reset note means the previous session ended badly and deliberately, and it names what to avoid and where to restart. It is the single most important thing in the file when it exists, and it is the reason `/recover` tells engineers to run `restore` first in a fresh session.

4. **Confirm before continuing.** Ask plainly: "Is this still accurate? Anything changed, resolved, or no longer relevant?" Wait for the answer. Do not proceed to other work until confirmed; a wrong carried over assumption is the failure mode this step exists to catch.
5. If the engineer says something is now stale, remove or correct it in `docs/session-notes.md` before continuing, so the file does not keep drifting from reality. This is the one case where `restore` may edit a section this skill does not own, because the engineer has explicitly said the content is wrong.

## Portability (any OS, any agent)

Any Agent Skills client on macOS, Linux, or Windows. No CLI beyond your agent's own file tools is required; this skill never shells out. `docs/session-notes.md` is a plain path, created with your write tool if missing.

## Report format

For `save`:

```
## /checkpoint save complete

Added: <n> entries (<headings touched>)
Aged out: <n> entries, now covered by <spec or scope reference>   (omit if none)
Preserved: <n> sections owned by other skills                     (omit if none)
```

For `restore`:

```
## /checkpoint restore

Reset note present: <feature name, from the previous session>   (omit if none)
Open threads: <n>
Ruled out: <n>
Standing instructions: <n>
Scope: <n features in progress, n pending>
Recent specs: <n touched in the last session>

Is this still accurate? Anything changed, resolved, or no longer relevant?
```
