---
name: recall
allowed-tools: Bash, Read, Grep, Glob, Write, Edit
argument-hint: [feature name | (nothing)]
description: >-
  Run /recall before marking a feature done, to prove you can still explain
  code an agent wrote for you. It reads the implementation, asks you four to
  six questions drawn from it, and grades what you write from memory, naming
  where you are wrong or vague. It never tells you what the code does: it
  marks, it does not teach.
---

## Output style (plain words, no dashes, no hyphens)

Write everything this skill produces, files and messages alike, in plain simple language. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.

## What this skill does

Catches the gap between explaining your decisions and explaining your code.

You can talk fluently about why a feature is built the way it is, because `/architect` made you decide every part of it. Nothing downstream asks whether you can still walk someone through the code that resulted: `/develop` builds it, `/check verify` proves it runs, `/test` locks the behavior, `/check review` reads the diff. A feature passes all four with your understanding intact or entirely absent, and nothing tells the difference.

This skill is the marker for an exercise you cannot mark yourself: write the feature's data and control flow from memory, then find out where you were wrong.

## The one rule that makes this work

**Never explain the code.** The report may quote the engineer and may cite file and line. It may never quote or paraphrase the source.

Say where they are wrong, not what is right. Name the file to read, never summarize it. "You said the token check happens in the middleware. It does not. Read `auth/session.ts:40`" is feedback. Adding what the middleware actually does converts the exercise into recognition, which is the failure this exists to prevent. If a finding cannot be stated without pasting the code, point at the code instead.

This holds in the report, in answers to follow up questions, and for the rest of the conversation after the run. Asked to explain a finding, say where to read it. The one exception is the third pass exit in Step 4: explicit, limited to the claim that earned it, and labeled.

## Asks vs acts

Acts on its own to pick the target, read the implementation, and write the questions.

Stops twice, both unconditional:

- **After printing the questions**, for what the engineer writes. Never continues on its own, never drafts an example answer, never fills in a question they skipped.
- **Before writing `flow.md`**, which lands only on their go.

Nothing else is ever written: no code edits, no scope boxes, no spec edits.

## Artifact ownership

Owns `docs/specs/<feature>/flow.md`, and nothing else. Read only on source code, `docs/scope/`, and the spec. Never edits the scope, the spec, `AGENTS.md`, `docs/session-notes.md`, `docs/reflexes.md`, or any source file. Ticks no box: this skill records its outcome in `flow.md` alone, so nothing downstream has to reconcile a box with evidence that does not exist in the repo.

## Execution

### Step 1: pick the target

**Feature name given** → its row in `docs/scope/`, and its spec.

**Nothing given** → the feature most recently built in this session, else the last scope feature whose `Build it` box is ticked. Name the target and confirm it before reading further; a wrong target wastes the engineer's writing, not just tokens.

Gather the files: the scope row's code pointer, the paths in the spec's `## Build plan`, and, where `git` is available, the diff that built the feature. If the engineer says they wrote part of it by hand, leave that part out. The hole forms where an agent wrote and they did not read.

### Step 2: read the implementation, then write the questions

Read the code. Write four to six questions, holding these rules:

- **Ground every question in the implementation, never in the spec alone.** The spec is what they already know; the decisions are not the gap.
- **A question names a starting point and a destination, never a mechanism.** "Trace what happens to a request that arrives with an expired token" is a question. "Explain why the retry wrapper falls back to the cache on a 503" hands over the answer.
- **At least one traces a failure path**, and **at least one asks what calls what, and what comes back**. Seams and error paths are where code they never read hides.
- Do not order them by difficulty and do not say which is the hard one.

Print the questions, then one line: write it with the code closed, nothing here works if you look. **Stop and wait.**

### Step 3: grade what they wrote

Read their account against the code. Mark each claim as one of four, and keep the report to what that verdict allows:

| Verdict | What the report may say |
| --- | --- |
| **correct** | that it is correct, nothing more |
| **wrong** | their claim, plus the file and line where the truth lives. No description of it. |
| **vague** | their own sentence quoted back, and the word doing unearned work. No hint at the answer. |
| **missing** | what they never mentioned, named by its location, never by its behavior |

**No score.** A percentage invites them to pass rather than to read. The output is a list of holes and where to look.

Mark `missing` only where a question asked for the thing. Silence on something never asked about is not a gap.

### Step 4: second pass, and the ways out

After the report the engineer reads the flagged places and rewrites those parts. When they come back, grade **only what was flagged**: what was already correct stays correct, do not re examine it, and do not widen the question set. The reading between the two passes is what produces the understanding. The report is not the product.

Three things keep this from becoming a loop with no exit:

- **Nothing requires a second pass.** Say so with the first report: they can keep the gaps, mark the feature `done`, and move on. Knowing where the holes are is worth having even if they never close them.
- **Narrow the pointer each pass, never repeat it.** First pass names the file, second the line range, third the function or the exact call. The target shrinks; the answer does not arrive.
- **On the third failed pass of one claim, tell them, and mark that claim `given`.** Say what the code does, briefly and once. The rule guards against being handed the answer before the work, and after three passes the work is done; the label is what stops anything later reading a given claim as a recalled one.

**Two failed passes may be the code, not the engineer.** Where that is genuinely it (a path threaded through many files, a name that says the opposite of what it does, control flow nobody could hold in their head), say so and point at `/check review <feature>`. Never as consolation for a wrong claim about plain code.

### Step 5: the artifact, on confirmation

When nothing is left marked `wrong` or `missing`, offer to keep it:

> "Nothing left wrong or missing. Save this to `docs/specs/<feature>/flow.md`? Recommended: yes, it is your own account of the code and it held up, which is worth having at review time and months later."

A claim that ended as `given` does not block the save, and carries `(given, not recalled)` on its line in the file.

On their go, write the **corrected** account, never the first draft: a draft that was wrong in the flagged places is a stale document from the day it lands. First line:

```markdown
# Flow: <feature> · spec NNNN · written from memory <YYYY MM DD>
```

**No spec directory for this feature** → say so and write nothing. Do not invent a home and do not create `docs/specs/` yourself.

**Single file spec** (`docs/specs/NNNN-feature.md`) → say that saving needs the directory shape `/develop` creates when it saves verify steps, and give the account back inline instead. Never promote the spec yourself; that is `/develop`'s.

## Portability (any OS, any agent)

Any Agent Skills client on macOS, Linux, or Windows. `git` is the only CLI, used only to narrow the file set to what the feature changed; with no git, the scope's code pointer and the spec's build plan are enough. `flow.md` is a plain path, written with your agent's own write tool.

## Report format

For a grading pass:

```
## /recall <feature>

<n> claims: <n> correct, <n> wrong, <n> vague, <n> missing, <n> given

Wrong
- You wrote "<claim>". Read `<path>:<line>`.

Vague
- "<their sentence>" · "<word>" is doing the work here. What, exactly?

Missing
- `<path>` is on this path and you did not account for it.

Given (third pass, not recalled)
- `<path>:<line>` · <what it does, in one line>

Next: read those places, rewrite only those parts, run /recall <feature> again.
Or keep the gaps and mark the feature done, your call.
```

Omit any heading with nothing under it. On a clean pass the whole report is the count line and the save offer.
