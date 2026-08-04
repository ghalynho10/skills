# /recover hard reset (the session has gone wrong)

For failure mode two: fixes have been patching fixes, the session's context is full of failed attempts, and the original problem is no longer clear. Follow it fully.

## What this mode does

**Your role:** the person willing to say a session cannot be saved and mean it. Not a failure to admit, the correct response to a polluted context. The temptation this mode exists to resist is one more attempt, one more corrective prompt, because the alternative feels like giving up. It is not giving up. A fresh session with a clear starting point is faster than continuing to patch a session that has already gone wrong.

## Execution

### Step 1: say so plainly

Tell the engineer directly, before doing anything else:

> "This session has gone too far in the wrong direction to recover by patching. The right move is a clean start. This is not a failure, it is the correct response to a polluted context. A fresh session with clear intent will be faster than continuing here."

### Step 2: capture what is worth keeping

Before the session ends, extract what should survive it. Ask, or answer from what the session already shows:

- What was the original feature or fix supposed to do
- What parts of the current implementation, if any, are actually correct and worth keeping
- What has been learned about what does not work, specifically enough that the next session does not repeat it
- Where the next session should start

### Step 3: write the reset note

`docs/session-notes.md` is shared with `/checkpoint`. Read the whole file first, write only the `## Reset notes` section, and write every other section back exactly as you found it. Do not reorder sections and do not normalize headings.

Write under this heading:

```
## Reset notes

### <Feature or fix name>, reset on <date>

What we were building: <original description>

What went wrong: <honest summary of how the session went off track>

What to avoid next time: <specific approaches or patterns that did not work>

Starting point for next session: <where to begin, what to keep, what to discard>
```

Do not use top level (`##`) headings inside the note body. Section boundaries in this file are found by scanning for them, so a `##` inside an entry would split the document wrongly. The labeled lines above are the format for exactly this reason.

If `docs/session-notes.md` does not exist yet, create it with this heading; `/checkpoint`'s own conventions for the rest of the file still apply.

If a `## Reset notes` section already exists from an earlier reset, append the new entry under it rather than replacing what is there. An older reset note is aged out by `/checkpoint save` once its content is represented in scope or a spec, not overwritten here.

### Step 4: instruct the engineer

```
Next steps.

Save this reset note, it is now in docs/session-notes.md.
End this session completely, do not continue in it.
Start a fresh session.
Run /checkpoint restore first, then approach this again with the reset note as context.
```

Do not continue working in the current session after writing the note. Continuing here, even briefly, is the exact pattern this mode exists to stop.

## Report

```
## /recover, hard reset

Reset note written to docs/session-notes.md
Feature: <name>
Start this again in a fresh session, beginning with /checkpoint restore.
```