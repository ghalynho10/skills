# /recover rethink (the foundation is wrong)

For failure mode three: the code runs but rests on a wrong assumption about a requirement, a library, or the architecture, and fixing individual pieces will not help because the approach itself is incorrect. Follow it fully.

## What this mode does

**Your role:** the person who says the assumption underneath all of this was wrong, and means the assumption, not the code built on it. The code may be well written. That is exactly what makes this failure mode dangerous: confident, clean implementation of the wrong thing is harder to notice than a messy implementation of the right thing.

This mode never rebuilds anything on its own authority. It names what was assumed, states what is actually true, and proposes a correction, then stops and waits.

## Execution

### Step 1: find the wrong assumption

Read enough of the current implementation to state precisely what was assumed, not just that something is wrong. A vague "this does not work" is not a diagnosis. The assumption is usually one of: a misunderstanding of what the feature actually needed to do, a wrong belief about how a library or API behaves, or an architectural pattern applied where it does not fit this codebase's conventions.

### Step 2: name it

State the assumption and the reality side by side:

```
The core issue is not a bug, it is a wrong assumption.

Assumed: <what was assumed>
Reality: <what is actually true>

This means the current implementation cannot be fixed by patching. The approach needs to change.
```

### Step 3: propose the correct approach

```
Correct approach: <description>

Key difference from the current approach: <explanation>

What needs to be discarded: <what cannot be salvaged>
What can be kept: <what is still valid and does not need to change>
```

Be honest about how much survives. Sometimes most of the surrounding code is fine and only one assumption needs correcting. Sometimes very little survives. Overstating what can be kept, to soften the news, defeats the point of diagnosing this as foundation level in the first place.

### Step 4: wait

Do not touch any code yet. Ask plainly:

> "Does this diagnosis match your understanding? If yes, we can start fresh with the correct approach. If no, tell me what I am getting wrong."

Wait for the answer. This confirmation is not a formality. A wrong diagnosis acted on immediately is worse than a right diagnosis delayed by one question, because rebuilding on a still wrong understanding produces the same failure mode a second time.

### Step 5: if this is a load bearing decision

If the corrected approach is a real design decision, something that should be recorded rather than just implemented, say so and recommend `/architect` for it rather than proceeding straight into `/develop`. Not every rethink rises to that level; a wrong assumption about one function's behavior does not need a spec. A wrong assumption about how a whole feature should be structured usually does.

### Step 6: only after agreement

Once the engineer confirms, rebuilding can begin, either directly if the correction is small, or via `/architect` then `/develop` if it is load bearing. This mode's job ends at the confirmed diagnosis; it does not carry out the rebuild itself.

## Report

```
## /recover, rethink

Assumed: <one line>
Reality: <one line>
Proposed approach: <one line>

Waiting for confirmation before any rebuild.
```