---
name: recover
allowed-tools: Bash, Read, Grep, Glob, Write, Edit
argument-hint: <describe what went wrong>
description: >-
  Run /recover when something has gone wrong during a build and it is not
  obvious what kind of failure this is. Diagnoses whether this is an isolated
  bug, a session that has gone wrong through repeated patching, or a
  foundation built on a wrong assumption, then prescribes the response that
  fits. This is the skill AGENTS.md's circuit breaker should point at: if the
  same problem persists after one corrective prompt, stop and run /recover
  before trying again.
---

## Output style (plain words, no dashes, no hyphens)

Write everything this skill produces, files and messages alike, in plain simple language. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.

## What this skill does

Not every problem is a bug. Not every bug needs debugging. The instinct when something goes wrong during agent driven work is to keep prompting, describe the problem, get a fix, describe the next problem, get another fix. The session gets longer, the context fills with failed attempts, and the code gets worse. The actual problem is often not the code. It is not knowing what kind of failure this is before responding to it.

`/debug` already exists in this pipeline and it does its job well, but it does one job: it assumes the code is wrong and finds the root cause. That is correct for a large share of failures and wrong for the rest. A session that has been patched three times and is now tangled is not helped by finding one more root cause. An implementation that confidently misunderstood a library API from the start is not helped by fixing its symptoms one at a time.

`/recover` diagnoses first, before any response is chosen. Three failure modes, three different responses:

- **A specific thing is broken**, isolated, clear, first or second attempt. A normal bug. Hands off to `/debug`.
- **The session has gone wrong**, fixes patching fixes, context full of failed attempts, the original problem no longer clear. Patching further compounds the damage. The response is a hard reset: capture what is worth keeping, end the session, start fresh.
- **The foundation is wrong**, the code runs but rests on a wrong assumption about a requirement, a library, or the architecture. The response is a rethink: name the wrong assumption, propose the right approach, change nothing until the engineer agrees.

This is the skill the pipeline's circuit breaker should name. The breaker rule ("if the same problem persists after one corrective prompt, stop") produces exactly the signal this skill's first question asks for: how many times has this been tried already. Routing that signal straight to `/debug` throws the signal away and assumes mode one every time, which is the failure this skill exists to prevent.

## Pick the mode (route before doing anything else)

Diagnosis happens in the router itself, not in a mode file, because the diagnosis is what determines which mode file gets read. Do not skip to a mode based on a guess.

### Step 1: listen first

Ask the engineer to describe what went wrong, specifically:

> "Describe what is wrong. What did you expect to happen? What happened instead? How many times have you tried to fix it already?"

Read the answer carefully before doing anything else. The number of attempts matters more than it looks: it is the difference between a fresh problem and a session that has already gone wrong.

### Step 2: diagnose the failure mode

**A specific thing is broken** if: the problem is isolated to one component, function, or route, the rest of the project works, this is the first or second attempt, and the error or wrong behavior is clear and specific. This is a normal bug with a findable root cause.

**The session has gone wrong** if: multiple fix attempts have made things worse or created new problems, the code has become tangled with fixes patching fixes, the session's context is full of failed attempts, and it is no longer clear what the original problem even was.

**The foundation is wrong** if: the code runs but produces fundamentally wrong behavior, the agent has been confidently building something that misunderstands a core requirement, a library's API, or an architectural pattern, and fixing individual pieces will not help because the approach itself is incorrect.

State the diagnosis before proceeding, in one line, with the reason:

> "This looks like [a specific bug / a session that has gone wrong / a wrong foundation], because [one sentence]. Here is how we handle it."

### Step 3: route

- **A specific thing is broken** → this is `/debug`'s job. Say so and hand off: "This is a normal bug. Run `/debug` and it will take it from here." Do not duplicate `/debug`'s reproduce, localize, hypothesize, fix, verify loop inside this skill.
- **The session has gone wrong** → read `modes/hard-reset.md` and follow it fully.
- **The foundation is wrong** → read `modes/rethink.md` and follow it fully.

## Asks vs acts

**Diagnosis acts**, mostly: it reads the engineer's description and states a conclusion. It does not ask permission to diagnose.

**Both real modes pause before changing anything.** Hard reset does not end the session or discard work until the engineer has seen the reset note. Rethink does not touch code until the engineer has confirmed the corrected understanding is right. These pauses exist because both responses are more disruptive than a normal fix, and getting the diagnosis wrong here is expensive to undo.

## Artifact ownership

Owns exactly one section of one shared file: the `## Reset notes` section of `docs/session-notes.md`.

That file is shared, not owned by any single skill. `/checkpoint` owns `## Open threads`, `## Ruled out`, and `## Standing instructions`; this skill owns `## Reset notes`; a skill added later may own another section. The rule that keeps this safe runs in both directions: **read the whole file, mutate only your own section, write every other section back byte for byte.** Do not reorder sections, do not normalize headings, and never rewrite the file from a template of the sections you know about.

The reasoning for sharing rather than owning a separate file: a reset note is the same category of thing `/checkpoint` already holds, in session residue that has not earned a spec or scope row, and an engineer looking for "what is unresolved" should have one place to look rather than two.

**One constraint this imposes on the reset note itself:** its body must not contain top level (`##`) headings, because section boundaries in the shared file are found by scanning for them. Use plain labeled lines instead. The template in `modes/hard-reset.md` already follows this.

**Lifecycle.** A reset note is written for the next fresh session and is meant to be consumed, not kept forever. `/checkpoint save` may age one out, but only once its content is clearly represented in `docs/scope/` or `docs/specs/`, or the feature it describes has been rebuilt and closed. This skill never removes its own reset notes on a later run; it appends a new one.

Never edits `docs/scope/`, `docs/specs/`, or `AGENTS.md`. If a rethink's corrected approach turns out to be a load bearing design decision worth recording properly, this skill says so and recommends `/architect`; it does not write the spec itself.

## Relationship to /checkpoint

A hard reset ends with the same instruction `/checkpoint restore` is built for: start a fresh session and recall the open threads before continuing. Point the engineer there explicitly rather than assuming they remember the command exists.

## Portability (any OS, any agent)

Any Agent Skills client on macOS, Linux, or Windows. No CLI beyond your agent's own file tools is required; this skill never shells out.

Bundled files live in this skill's folder: `modes/hard-reset.md` and `modes/rethink.md`. Read only the mode file the diagnosis routed to.

## Reference files

- `modes/hard-reset.md`: ending a polluted session cleanly and capturing what is worth keeping
- `modes/rethink.md`: naming a wrong foundational assumption and proposing the corrected approach

## The principle

The worst thing to do when something is broken is keep doing the same thing faster. Diagnose first. Respond to what is actually wrong, not to what the last few attempts assumed was wrong.
