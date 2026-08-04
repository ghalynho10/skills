# /wayfinder chart (chart a new map)

Charting a new map for a loose idea. Follow it fully.

## What this mode does

**Your role:** the person who can look at a foggy effort and say honestly what is already sharp, what is merely sensed, and what lies past the edge of what this effort is even for. You resist the two easy failures: pretending the fog is sharper than it is by writing tickets you cannot really phrase, and pretending it is foggier than it is by leaving sayable questions vague.

Charting is one whole session's work. **Do not chart and then start resolving tickets.** Stop when the map is written.

## Execution

### Step 1: Name the destination

Before anything else, settle what this effort is finding its way to. The destination fixes the scope, so it is decided first and with the engineer.

A destination is one of:
- **A decision** to lock before planning can continue ("which watch platforms we support and how sync works")
- **A specification** to hand off and iterate on
- **A change made in place**, like a data structure migration

Interview the engineer to pin it down. One question at a time. Keep going until you can state the destination in one or two lines that someone else would read the same way you do.

Then check the obvious: **is this effort actually small enough not to need a map?** If the destination is one decision that a single conversation could settle, say so plainly and stop. Point them at `/architect <the thing>` if it is a load bearing design decision, or `/scope` if it is really a planning question. A map for a one session effort is overhead with no payoff.

### Step 2: Map the frontier, breadth first

Interview again, and this time deliberately **fan out across the whole space rather than going deep on any one thread**. You are surfacing what stands between here and the destination, not resolving any of it.

For each thing that surfaces, sort it into exactly one of three piles using the sharpness test:

- **Can you state the question precisely right now?** → it becomes a ticket, even if it is blocked and nobody can act on it yet.
- **Can you tell something is coming there but not phrase it?** → it goes in **Not yet sharp**.
- **Does it sit past the destination?** → it goes in **Out of scope**.

**If nothing lands in the fog pile**, the way to the destination is already clear and this effort fits in one planning session. Stop, say so, and ask how the engineer wants to proceed. Do not write a map nobody needs.

### Step 3: Write the map

Read `map-template.md` now (only now) and follow its structure.

Create `docs/wayfinding/<effort-slug>/map.md` with:
- The destination, filled in from Step 1
- Notes: the domain, any standing preferences for this effort, anything every session should know
- An empty decision list
- The fog sketched into **Not yet sharp**
- Anything already ruled out in **Out of scope**

The slug comes from the destination, kept short and readable: `smartwatch-sync`, not `0001` and not `effort-2026-07-30`.

### Step 4: Write the tickets you can specify now

Create `docs/wayfinding/<effort-slug>/tickets/NNNN-<slug>.md` for each sharp question, numbered from `0001` in the order they surfaced.

Each ticket gets a type (`research`, `prototype`, `interview`, or `task`) and one question, sized to what one session could reasonably resolve.

**Wire the blocking in a second pass.** Write every ticket first, then go back and fill in each `Blocked by:` line, because a ticket needs to exist before another can point at it.

Once wired, every ticket is either on the **frontier** (open, unclaimed, and blocked by nothing still open) or blocked behind something. You do not need to record which; it falls out of reading the files.

### Step 5: Stop

Charting is done. Report and end the session.

Do not resolve a ticket in the same session that charted the map. The map deserves a fresh read before anyone acts on it, and charting has already filled this session's context with the shape of the whole effort rather than the depth of any one question.

## Report

```
## /wayfinder chart complete: <destination, one line>

Map: docs/wayfinding/<slug>/map.md
Tickets: <n> written, <n> on the frontier now
Not yet sharp: <n> patches of fog
Out of scope: <n> items   (omit if none)

Next: /wayfinder <slug>   (resolves the first frontier ticket, one per session)
```
