---
name: recall
allowed-tools: Bash, Read, Grep, Glob, Write, Edit
argument-hint: [feature name | (nothing)]
description: >-
  Run /recall before marking a feature done, to prove you can still explain
  code an agent wrote for you. It reads the implementation, asks you four to
  six questions drawn from it, then grades the account you write from memory,
  naming where you are wrong or vague instead of explaining the code back to
  you. It marks; it does not teach.
---

## Output style (plain words, no dashes, no hyphens)

Write everything this skill produces, files and messages alike, in plain simple language. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.

## What this skill does

Marks the one thing nothing else checks: whether the engineer can still explain the code. `/develop` builds it, `/check verify` proves it runs, `/test` locks the behavior, `/check review` reads the diff, and a feature passes all four whether their understanding is intact or entirely absent.

It asks questions drawn from the implementation, takes the account they write from memory, and names where that account is wrong.

## The one rule that makes this work

**Never explain the code.** Output may quote the engineer and may cite file and line. It may never quote or paraphrase the source.

Say where they are wrong, not what is right. Name the place to read, never summarize it. "You said the token check happens in the middleware. It does not. Read `auth/session.ts:40`" is feedback; adding what the middleware does turns the exercise into recognition. If a finding cannot be stated without pasting the code, point at the code instead.

**A guess in chat is a pass.** Never confirm or deny a guess outside a graded pass. Asked "is it the cache?", either grade it as that claim's next pass, counted and labeled, or say to put it in the rewrite. Twenty questions leaks the whole answer without ever quoting the source, which is how this rule dies in practice.

**Scope.** The rule binds this skill's claims until the engineer ends the run, not the conversation around it: a later `/debug` or `/check review` explains code because that is its job. The one exception inside the run is the third pass exit in Step 4.

## Pick the pass (route before doing anything else)

- **A `/recall` report for this feature is already in this conversation** → second pass. Go to Step 4 and grade only what it flagged. Do not regenerate questions.
- **Otherwise** → first pass, Step 1.

Passes live in one conversation. After `/clear` the pass count is gone, so a fresh run starts at pass one; say so rather than guessing at a prior pass.

## Asks vs acts

Acts on its own to pick the target, read the implementation, and write the questions.

Stops: on Step 1 when the target is not certain; after printing the questions, on every pass, for what the engineer writes; and before writing `flow.md`, which lands only on their go.

## Artifact ownership

Owns the feature's `flow.md`, written beside its spec, and nothing else. Read only on source code, `docs/scope/`, and the spec. Never edits the scope, the spec, `AGENTS.md`, `docs/session-notes.md`, `docs/reflexes.md`, or any source file. Ticks no box, so nothing downstream has to reconcile a box against evidence that does not exist in the repo.

## Execution

### Step 1: pick the target

**Feature name given** → its row in `docs/scope/`, and its spec.

**Nothing given** → the feature most recently built in this session, else the last scope feature whose `Build it` box is ticked. Name it and confirm before reading further.

**Name given with no matching row, or no `docs/scope/` at all** (a one off `/develop` leaves no row) → ask which paths to read. Never guess the target.

Gather the files: the scope row's code pointer, the paths in the spec's `## Build plan`, and, where `git` is available, the diff that built the feature. Leave out anything the engineer says they wrote by hand.

### Step 2: read the implementation, then write the questions

Read the code. Write four to six questions, holding these rules:

- **Ground every question in the implementation, never in the spec alone.**
- **A question names a starting point and a destination, never a mechanism.** "Trace what happens to a request that arrives with an expired token" is a question. "Explain why the retry wrapper falls back to the cache on a 503" hands over the answer.
- **At least one traces a failure path**, and **at least one asks what calls what, and what comes back**.
- Do not order them by difficulty and do not say which is the hard one.

Print the questions, then one line: write one account of the data and control flow, answering these in order, with the code closed. **Stop and wait.**

### Step 3: grade what they wrote

Read their account against the code. Mark each claim as one of four, and keep the report to what that verdict allows:

| Verdict | What the report may say |
| --- | --- |
| **correct** | that it is correct, nothing more |
| **wrong** | their claim, plus the file and line where the truth lives. No description of it. |
| **vague** | their own sentence quoted back, and the word doing unearned work. No hint at the answer. |
| **missing** | what they never mentioned, named by its location, never by its behavior |

**No score.**

Mark `missing` only where a question asked for the thing. Silence on something never asked about is not a gap.

### Step 4: second pass, and the ways out

Grade **only what the last report flagged**: what was already correct stays correct, do not re examine it, and do not widen the question set.

Three things keep this from becoming a loop with no exit:

- **Nothing requires a second pass.** Say so with every report: they can keep the gaps, mark the feature `done`, and move on.
- **Narrow the pointer each pass, never repeat it.** First pass names the file and line. Second names the function or the exact call. A `vague` claim narrows the same way, from the word doing the work to the file and line where the precision lives, still without saying what it is.
- **On the third failed pass of one claim, tell them, and mark that claim `given`.** One line, that claim's file and line only, nothing about what calls it or what it returns. The label is what stops anything later reading a given claim as a recalled one.

**A flagged claim they did not rewrite is not a failed pass.** Leave it flagged at the pointer it already had, and say so.

**Two failed passes may be the code, not the engineer.** Say only that, and point at `/check review <feature>`. Do not say what makes it hard: naming the shape of the code is explaining it. Never as consolation for a wrong claim about plain code.

### Step 5: the artifact, on confirmation

When nothing is left marked `wrong`, `vague`, or `missing`, offer to keep it:

> "Nothing left open. Save this beside the spec as `flow.md`? Recommended: yes, it is your own account of the code and it held up, which is worth having at review time and months later."

On their go, write **the engineer's own corrected sentences, unchanged and in their order**. Write none of your own prose into the file: the header says written from memory, and a line you smoothed or authored makes that false. A claim that ended `given` keeps their sentence and gains `(given, not recalled)` at the end of its line. First line:

```markdown
# Flow: <feature> · spec NNNN · written from memory <YYYY MM DD>
```

**An existing `flow.md`** → replace it and say so. It is one account of the code as it stands, not a log.

**No spec directory for this feature** → say so and write nothing. Do not invent a home for it.

**Single file spec** (`docs/specs/NNNN-feature.md`) → say that saving needs the directory shape `/develop` creates when it saves verify steps, and give the account back inline instead. Never promote the spec yourself; that is `/develop`'s.

## Portability (any OS, any agent)

Any Agent Skills client on macOS, Linux, or Windows. `git` is the only CLI, used only to narrow the file set to what the feature changed; with no git, the scope's code pointer and the spec's build plan are enough.

**Artifact base.** `flow.md` goes beside the feature's spec (`docs/specs/NNNN-feature/flow.md`), so it follows whatever base the project already uses for specs, including `.workflow/specs/` where `docs/` is a published docs site. Follow the one already there; never start a second.

## Report format

For a grading pass:

```
## /recall <feature>

<n> claims: <n> correct, <n> wrong, <n> vague, <n> missing, <n> given

Wrong
- You wrote "<claim>". Read `<path>:<line>`.

Vague
- "<their sentence>" · "<word>" is doing the work here. Name the steps in your rewrite.

Missing
- `<path>` is on this path and you did not account for it.

Given (third pass, not recalled)
- `<path>:<line>` · <what it does, in one line>

Next: read those places, rewrite only those parts, and paste the rewrite here.
Or keep the gaps and mark the feature done, your call.
```

Omit any heading with nothing under it. On a clean pass the whole report is the count line and the save offer.
