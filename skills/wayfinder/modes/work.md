# /wayfinder work (resolve one ticket)

Working an existing map: resolve exactly one ticket, record it, and let the fog it clears turn into fresh tickets. Follow it fully.

## What this mode does

**Your role:** the person who resolves one question properly and then stops. The discipline that makes a map work is that each ticket gets a real answer, recorded where the next session will find it, before anything else is touched. A session that half resolves three tickets has produced nothing anyone can build on.

**Resolve exactly one ticket per session.** This is the rule that keeps context clean and answers honest.

## Execution

### Step 1: Load the map, not everything

Start with `map.md`. It is the low resolution view: the destination, the notes, what has been decided, what is still fog.

**Read ticket headers, not bodies.** Choosing a ticket needs each one's `Type:`, `Status:`, `Blocked by:`, and `Claimed:` lines, so read those four lines across the `tickets/` folder. Read a full body only for the ticket you are about to work, plus any closed ticket whose detail turns out to matter. Zoom into a body on demand, not up front.

### Step 2: Choose a ticket and claim it

**If the engineer named one**, use that one.

**Otherwise take the first frontier ticket**: open, unclaimed, and blocked by nothing that is still open. Lowest number first among those.

Numbering records when a ticket was written, not how much it matters, so lowest first is a tie breaker rather than a priority ordering. If something else on the frontier is clearly more urgent or more likely to clear fog, say so and take that one instead.

**Claim it before doing any work.** Set the `Claimed:` line in the ticket file to the current date and whoever is driving. An open ticket with an empty `Claimed:` line is unclaimed and free for anyone; claiming first is what stops two sessions from working the same question in parallel.

**Then read the ticket once more before starting.** Writing a line in a file is not an atomic claim the way a tracker's assignee field is: two sessions can both see an empty `Claimed:` line and both write to it. Re reading closes most of that window. If the line now names someone else, they got there first. Stop, say so, and either take the next frontier ticket or end the session.

If nothing is on the frontier, say so plainly. Either everything open is blocked (report what is blocking what), or the map is done (go to Step 6).

### Step 3: Resolve it

Work the ticket according to its type:

- **`research`**: read the documentation, the API, or the material. Write your findings as a summary saved beside the ticket at `tickets/NNNN-<slug>.notes.md`, and link it from the answer. Do not paste a wall of source material into the ticket itself.
- **`prototype`**: make the cheap rough artifact, show it to the person, and capture what their reaction settled. The artifact gets saved beside the ticket and linked, not pasted in.
- **`interview`**: talk it through with the person, one question at a time. **Never answer their side for them.** If they are not available, stop and say the ticket needs them; do not resolve it alone.
- **`task`**: do the work if you can, or hand the person a precise checklist if you cannot. Resolved when the work is done. The answer records what was done plus any resulting facts later tickets will depend on: where credentials live, new URLs, row counts.

Zoom as needed while working: fetch the full body of any related or closed ticket whose detail you need. The map's `## Notes` section may name things every session should consult; honour it.

### Step 4: Record the resolution

Three writes, in this order:

1. **Write the answer into the ticket** under `## Answer`, and set `Status: closed`. The answer is the real content, written so someone reading it cold in a month understands what was decided and why.
2. **Append one line to the map's decision list**: the ticket title as a link, plus a one line gist of the answer. **Gist, never restate.** The decision lives in the ticket; the map only points at it.
3. **Clear any fog the answer just sharpened**: if something in **Not yet sharp** can now be stated as a precise question, write it as a new ticket and remove that patch from the fog section, so it lives in exactly one place.

### Step 5: Adjust the map for what the answer changed

- **New tickets**: write them, then wire their `Blocked by:` lines in a second pass, same as charting.
- **Newly out of scope**: if the answer reveals that this ticket or another sits past the destination, close it as `out-of-scope` and add one line to the **Out of scope** section. It does not go in the decision list; a scope boundary is not a step on the route.
- **Invalidated tickets**: if the decision makes another ticket wrong or pointless, update its question or close it, and say so in the report. Do not leave a stale question sitting on the frontier for the next session to waste time on.

### Step 6: If the map is now clear

The map is done when nothing is left to decide before someone goes and does the thing: no open tickets, and no fog left in **Not yet sharp**.

Check the open tickets by reading the `tickets/` folder, not the map. Open tickets are never listed on the map, so its decision list tells you what has been resolved and nothing about what remains.

When that happens, say so and **hand off**:

- **If the cleared result is a feature that already has a thin row in `docs/scope/`** → recommend `/scope <the feature>`. It will find the existing row and thicken it in place rather than adding a duplicate.
- **If the result is a load bearing design decision** → recommend `/architect <the thing>`, which will turn it into a real spec.
- **If it is neither**, say what the result actually is and let the engineer decide where it goes.

**Recommend and stop.** Do not run `/scope` or `/architect` yourself, and do not write into their files. The handoff is theirs to make.

## Report

```
## /wayfinder: <ticket title>

Answer: <one line>
Map: <n> decided, <n> open, <n> on the frontier now
Added: <new ticket titles>   (omit if none)
Out of scope: <what got ruled out and why>   (omit if none)

Next: /wayfinder <slug>   (or the handoff command, if the map is clear)
```
