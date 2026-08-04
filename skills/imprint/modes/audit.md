# /imprint audit (establish a baseline across many files)

Scanning a whole body of UI at once, finding where it disagrees with itself, and confirming a baseline before writing anything. Follow it fully.

## What this mode does

**Your role:** the person who reads everything at once and tells the truth about what it actually contains, including the parts that do not agree with each other. You do not pick the standard yourself. You surface the conflict clearly enough that the person driving can pick it in one confirming pass.

Two situations bring you here, and the scan and report mechanics are identical for both. The only difference is where the files came from.

**An existing, previously untracked codebase.** UI was built over time, by hand or across many agent sessions, with no registry keeping it consistent. You are establishing the first baseline retroactively.

**UI freshly exported from a design tool.** Screens or components were generated in a tool such as Stitch, MagicPath, paper.design, or Claude Design, then exported as code, Figma layers, or a token file, and brought into the project. Even a single generation from one of these tools is not guaranteed to be internally consistent; several independent reviews of these tools note they can produce output that does not fully agree with itself, since each screen or component may be generated somewhat independently. Auditing this import before any hand built component joins it does two things at once: it turns the design tool's intent into this project's written standard, and it catches the tool's own inconsistencies before anything is built on top of them.

Either way, you are not designing anything. You are reading what exists, naming where it conflicts, and asking which variant should win.

## Execution

### Step 1: Gather the files to scan

**Existing codebase case:** find every component file in the project, in the usual UI locations. Read each one.

**Design tool import case:** the files will typically sit wherever the export landed, a dedicated import folder, a components directory freshly populated from the tool, or Figma layers if that is the only form available. If the location is not obvious, ask:

> "Where did the design tool export land? Point me at the folder or files to scan."

If what you have is a Figma file rather than code, and you have no way to read Figma directly, say so plainly and ask whether the values can be exported as code, a token file, or listed manually, rather than guessing at values you cannot see.

Either way, read every file in scope. Build a complete picture of what visual values are currently in use across all of it before judging anything.

### Step 2: Identify conflicts

For each property that matters for consistency, list every variation found, then recommend which one should become the standard.

```
## UI Consistency Audit

Source: <existing codebase, or the design tool and export path used>

### Conflicts found

Border radius
<every variant found, and which components use it>
Recommendation: <which one to standardize on, and why, for instance most components already use it, or it matches a stated token if one exists>

Background colors
<every background value found, flag anything hardcoded rather than a token or shared variable>
Recommendation: <what should replace the hardcoded values>

Text colors
<every text color value found, flag anything that bypasses an existing token system>
Recommendation: <...>

Spacing
<padding and gap variations found>
Recommendation: <...>

Border colors
<every border color value found>
Recommendation: <...>

Interactive states
<hover, focus, active variations found>
Recommendation: <...>

### Hardcoded values found

<every hardcoded hex value or raw, non token value found, with the file it appears in>
These are candidates to replace with shared tokens if the project keeps a `ui-tokens.md`, or to at least name consistently if it does not.

### Recommended baseline

<the proposed value for each property, based on what the majority already uses correctly, and, if the project has `ui-tokens.md` or `ui-rules.md`, checked against those first>
```

If the project has `ui-tokens.md` or `ui-rules.md`, read them before writing recommendations, and check each recommended value against them. A recommendation that matches an existing token is stronger than one inferred purely from majority usage; say so where it applies. If a recommendation conflicts with a stated rule, for instance a component using color on a card surface when the rule says color only lives inside cards, name that conflict explicitly rather than quietly picking the majority value.

### Step 3: Wait for confirmation

Present the audit report. Do not fix anything yet. Do not write to `ui-registry.md` yet.

Ask plainly:

> "Audit complete. <n> conflicts found across <n> properties. Before I set this as the baseline in `ui-registry.md`: do the recommendations above look right? Are there conflicts you would resolve differently? Should the hardcoded values be flagged as issues to fix later? Confirm the baseline and I will write it."

Wait for the answer. This is the one place this skill pauses on purpose, because a baseline is a real decision and getting it wrong here means every future capture measures against the wrong standard.

### Step 4: Write the confirmed baseline

Once confirmed, write it to `ui-registry.md` as the foundation, labeled clearly so it is obvious this came from an audit rather than an ordinary capture:

```
## Baseline, established <date>

Source: <existing codebase audit, or design tool import from <tool name>>

| Property | Confirmed value |
| --- | --- |
| Card background | <value> |
| Card border | <value> |
| Card radius | <value> |
| Button primary | <value> |
| Button secondary | <value> |
| Text primary | <value> |
| Text secondary | <value> |
| Text muted | <value> |
| Input background | <value> |
| Input border | <value> |
```

### Step 5: List what needs fixing

After writing the baseline, produce a fix list, every file that deviates from it:

```
## Components to fix

<file>: <what is wrong> should be <what the baseline says>
<file>: <...>
```

This skill does not make these edits. The engineer fixes them systematically, or as each component is next touched. Once the baseline is written, ordinary `/imprint` keeps new and updated components matching it going forward.

## After a design tool import specifically

Two things worth doing once the baseline from an import is written, though neither is this skill's job to do:

If the project does not yet have `ui-tokens.md`, this confirmed baseline is a reasonable starting point for one; it is the same values, just promoted from an observed record to a stated standard. That promotion is a decision for the engineer to make, not something this skill writes on its own.

If several screens came out of the design tool with real disagreement (not just one outlier but a genuine split), that is worth surfacing in the report as its own note, since it may mean the prompt or reference material given to the design tool was itself inconsistent, which is useful to know before generating more screens the same way.