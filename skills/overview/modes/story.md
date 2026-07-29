# /overview story (prose telling)

The `story` mode of `/overview`: writes `docs/project-story.md`, a plain prose telling of the project for a person. Follow it fully.

## What this mode does

**Your role:** the person who can tell someone what was built and why it mattered, over coffee, without a slide deck. Not a documenter. The reference document already exists and does that job; this is the telling that a document cannot do.

Reads `docs/overview.md` and the reasoning behind its load bearing decisions, then writes a continuous prose account. Read only on everything except `docs/project-story.md`.

Run this when the project is far enough along to be worth telling: a milestone, a release, or completion. Re run it whenever the project has changed enough that the last telling is out of date.

## Prerequisite

This mode reads `docs/overview.md` as its primary source. If that file does not exist, stop and say so: run `/overview update` first, then come back. Do not attempt the telling from raw scope and specs; the reference document is what makes the story cheap to write and accurate.

If the overview exists but is visibly stale (its Last updated line is old, or `/overview check` has reported drift), say so and recommend `update` first. Proceed only if the engineer wants to anyway.

## Execution

### Step 1: Confirm the reader (the one deliberate pause)

Before writing anything, ask who this is for. The answer changes the whole telling, so this is not skippable.

Present these options (your agent's picker, or plain text with the same choices where it has none):

```
Who is this telling for?
  • A general reader        no technical background assumed, explain everything in plain terms
  • A technical reader      knows software, does not know this codebase or domain
  • Someone specific        tell me who, and I will pitch it for them
```

Wait for the answer. Carry it through the whole piece; do not drift between registers halfway.

### Step 2: Read for substance

- `docs/overview.md` in full. This gives you the what, the how, and which decisions were load bearing.
- For each decision the overview calls load bearing: the `rationale.md` (directory spec) or the Rationale and Options considered sections (single file spec) of the spec behind it. **This is where the story's texture comes from.** A telling that only knows what was decided, and not what was rejected or what it cost, has no tension in it and teaches the reader nothing.

Do not read the codebase. If a fact is not in the overview or a spec's reasoning, it does not go in the story.

### Step 3: Write the story

Write `docs/project-story.md` as continuous prose. The rules:

- **No section headings, no tables, no bullet lists.** It is a telling, not a reference. If you find yourself wanting a heading, the piece needs a better transition instead.
- **Open with the problem, not the technology.** A reader should understand what was at stake before they hear a single tool name. Leading with the stack is the most common way this kind of writing fails.
- **Cover, in whatever order reads best**: what the project does, why it was worth building, how it came together, the decisions that were genuinely hard or interesting and what they cost, and where it stands now.
- **Name technologies only where the choice mattered to the story.** A stack list is not a narrative. "It runs on Postgres" earns its place only if choosing Postgres was a real decision with a real alternative.
- **Be honest about tradeoffs and things that did not work.** A telling with no friction in it reads as marketing. The rejected options in the specs' Options considered sections are usually the most interesting material you have; use them.
- **Do not invent.** Same rule as `update`: no motivations, reactions, or reasoning that no file supports. A story is a place where invention is especially tempting and especially damaging, because prose makes an invented detail sound settled.
- **Length**: long enough to be worth reading, short enough to finish in one sitting. If it runs past a few pages, the project probably needs more than one story, not a longer one.

Match the register to the confirmed reader throughout. For a general reader, every domain term gets unpacked the first time. For a technical reader, assume software fluency but never assume familiarity with this project or its domain.

### Step 4: Report

```
## /overview story complete

Written for: <the confirmed reader>
File: docs/project-story.md
<One line on what the telling covers.>
```
