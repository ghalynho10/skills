---
name: imprint
allowed-tools: Bash, Read, Grep, Glob, Write, Edit
argument-hint: <filepath | audit | (nothing)>
description: >-
  Run /imprint after building any UI component to extract the visual patterns
  that matter for consistency (background, border, radius, text, spacing,
  hover state) and save them to ui-registry.md, so every component built
  after this one matches what came before. Run /imprint audit to establish a
  baseline against an existing codebase, or against UI freshly exported from
  a design tool (Stitch, MagicPath, paper.design, Claude Design), before
  ordinary /imprint runs going forward.
---

## Output style (plain words, no dashes, no hyphens)

Write everything this skill produces, files and messages alike, in plain simple language. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.

## What this skill does

UI consistency does not happen by accident. It happens because every component is built with awareness of what already exists. The failure mode this skill exists to prevent: an agent builds a component in one session with no memory of what it built three sessions ago, so spacing drifts, a second shade of blue creeps in, border radius goes uneven, and the app slowly looks like it was built by several people with different taste, because in a meaningful sense it was.

Two operations, both owning `ui-registry.md`:

- **capture** (the default, run after building any component): reads what was just built, extracts the values that matter for visual consistency, and appends or updates an entry in the registry.
- **audit**: scans a whole body of UI at once, whether that is an existing codebase never previously tracked or UI freshly exported from a design tool, finds every conflicting variant of each property, and proposes a baseline for you to confirm before anything is written.

This skill does not decide what the design should look like. It records what has been built and flags where it disagrees with itself. The decision of which variant becomes the standard is always yours, confirmed explicitly in audit mode.

### Relationship to ui-tokens.md and ui-rules.md, if your project has them

Some projects keep a separate `ui-tokens.md` (the raw values: colors, spacing scale, font, radius) and `ui-rules.md` (the constraints on how those values get used, such as which surfaces may carry color). This skill does not own either file and never writes to them.

The relationship: tokens and rules are the standard, decided in advance, ideally by a person. The registry is the record of what has actually been built, kept current by this skill. Where the two disagree, that disagreement is worth surfacing (a component using a color not in the token set, a pattern that violates a stated rule) but this skill only reports it; it does not resolve it or edit those files. If your project has no tokens or rules file yet, the registry can still stand alone; it just means there is no separate upstream standard to check the registry against.

## Pick the mode (route before doing anything else)

Look at what followed `/imprint`:

- **A filepath** → capture from that file specifically. Go to `modes/capture.md`, targeted mode.
- **`audit`** → establish or re establish a baseline across many files at once. Read `modes/audit.md` and follow it fully.
- **Nothing** → capture from whatever was most recently built in this session. Go to `modes/capture.md`, automatic mode.

Do not run both modes in one invocation. If asked to do both, run `audit` first when no registry exists yet or when consistency is genuinely uncertain, since capturing onto an unaudited registry just adds one more opinion to a file that may already be internally inconsistent.

## When to use audit instead of an ordinary capture

- The project's UI already exists and consistency is uncertain.
- Several sessions have passed without running `/imprint`.
- Something looks visually off but it is hard to pinpoint why.
- Before establishing `ui-registry.md` for the first time on a project with UI already in it.
- **UI has just been imported from a design tool** (Stitch, MagicPath, paper.design, Claude Design, or similar) and needs to become this project's written standard before any hand built component joins it. See _Design tool imports_ in `modes/audit.md` for the specifics of this case; the scan and report mechanics are identical to auditing an existing codebase, the source of the files is the only difference.

## Asks vs acts

**capture** acts. It reads, extracts, and writes the registry entry without asking, then reports what it captured. It asks only when it cannot tell which file to capture from (nothing obviously new or changed since the last capture).

**audit** acts up to a point, then stops and asks. It scans and produces the conflict report and a recommended baseline on its own, but never writes that baseline to the registry until you confirm it, correct it, or tell it to proceed. This is the one deliberate pause in this skill, because a baseline is a real design decision and this skill's job is to surface the choice clearly, not make it on your behalf.

## Artifact ownership

Owns `ui-registry.md` entirely, both modes. Never edits `ui-tokens.md`, `ui-rules.md`, source components, or any file outside the registry. If audit mode's fix list implies changes to real component files, it lists them for you or a later session to act on; it does not make those edits itself.

## Portability (any OS, any agent)

Any Agent Skills client on macOS, Linux, or Windows. No CLI beyond your agent's own file tools is required; this skill never shells out. `ui-registry.md` is a plain path, created with your write tool if missing.

Bundled files live in this skill's folder: `modes/capture.md` and `modes/audit.md`. Read only the mode file you routed to.

## Reference files

- `modes/capture.md`: capturing patterns from one component, targeted or automatic
- `modes/audit.md`: scanning many files at once, including the design tool import case, and confirming a baseline
