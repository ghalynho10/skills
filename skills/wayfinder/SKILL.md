---
name: wayfinder
allowed-tools: Bash, Read, Grep, Glob, Write, Edit
argument-hint: <a loose idea | an existing map>
description: >-
  Run /wayfinder when an effort is too big and too foggy for one session: the
  destination is clear enough to name, but the way there is not, and you cannot
  yet state every question that stands between you and it. Charts the effort as
  a map of investigation tickets under docs/wayfinding/, then resolves them one
  per session until the way is clear. Plans decisions, never builds. Hand the
  cleared result back to /scope or /architect.
---

## Output style (plain words, no dashes, no hyphens)

Write everything this skill produces, files and messages alike, in plain simple language. Keep technical terms that carry real meaning; explain each in plain words. Never use a dash or a hyphen as punctuation: no em dash, no en dash, and no hyphenated compounds. Write `read only`, not `read-only`. Say it in simple words, or reword the sentence. Code, file paths, command flags, and values other skills match on keep their hyphens. Use short sentences, commas, or parentheses. Clear beats clever.

## What this skill does

A loose idea has arrived. It is too big for one session, and the way from here to the destination is wrapped in fog. This skill is about finding that way, not charging at the destination.

It charts the effort as a **map**: one markdown file holding the destination and the decisions made so far, plus a folder of **tickets**, each holding one question. Sessions work tickets one at a time. Resolving a ticket clears the fog ahead of it, which turns whatever just became sayable into fresh tickets. The map is done when nothing is left to decide before someone goes and does the thing.

**Plan, do not do.** Every ticket resolves a decision. The pull to just build the thing is usually the signal that you have reached the edge of the map, and it is time to hand off. Produce decisions, not deliverables.

### When this is the wrong skill

Reach for `/scope` instead when you have a product idea and simply have not answered the questions yet. `/scope`'s interview is built for that, and it will get you further faster. Ordinary not yet decided is not fog.

Fog is narrower: a question you cannot state precisely right now, because it hangs on something else nobody has settled. If you keep starting to plan and finding you cannot, because each answer waits on another unsettled thing, that stuck feeling is the signal.

**The test is whether you can state the question precisely now, not whether you can answer it now.** A sharp question you cannot answer yet is a ticket. A question you cannot even phrase is fog.

If charting turns up no fog at all, the way is already clear and this whole effort fits in one planning session. Say so and stop; point the engineer at `/scope`.

## Where it fits with the rest of the pipeline

This skill sits **before** `/scope` can do its job on one particular patch, not alongside it. The normal shape:

1. `/scope` plans the project and leaves one genuinely foggy feature thin, because it cannot be sharpened in the room.
2. `/wayfinder` charts that one patch and works it until the way is clear.
3. The cleared result goes **back** to `/scope <the feature>`, which finds the existing thin row and thickens it in place rather than adding a duplicate. If the result is a load bearing decision in its own right, take it to `/architect <the feature>` instead.

Most projects never need this skill. One patch of one project might.

## Pick the mode (route before doing anything else)

Look at what followed `/wayfinder`:

- **A loose idea, and no map exists for it** → chart it. Read `modes/chart.md` and follow it fully.
- **A path to an existing map**, or a bare `/wayfinder` when exactly one map exists → work through it. Read `modes/work.md` and follow it fully.
- **A bare `/wayfinder` when several maps exist** → list them by name and ask which one. Do not guess.
- **A bare `/wayfinder` when none exist** → ask what the effort is, then chart it.

**Never resolve more than one ticket per session**, in either mode. Charting is a whole session's work on its own; do not chart and then start resolving.

## Refer by name, never by number

Every map and every ticket has a title. In everything a person reads, narration and the map's decision list alike, refer to it by that title. A wall of `0042, 0043, 0044` is unreadable; names read at a glance. The number and the path do not vanish, they ride inside the name as its link, but they never stand in for it.

## Where the map lives

```
docs/wayfinding/<effort-slug>/
├── map.md
└── tickets/
    ├── 0001-<slug>.md
    └── 0002-<slug>.md
```

**The map is an index, not a store.** It lists the decisions made and points at the tickets holding the detail. A decision lives in exactly one place, its own ticket, so the map only gists it and links.

**Artifact base.** `docs/` by default. If `docs/` is a published docs site (`docusaurus.config.*`, `.vitepress/`, `mkdocs.yml`, Astro Starlight, or Nextra detected), use `.workflow/wayfinding/` instead. Follow whichever base the project already uses.

## Ticket types

Every ticket is either **worked with a person**, through live back and forth, or **worked alone** by the agent. A ticket that needs a person only resolves through that exchange; the agent never answers the person's side of it for them. An interview where the agent supplies both the questions and the answers has failed.

| Type | Who works it | What it is |
|---|---|---|
| `research` | Agent alone | Reading documentation, third party APIs, or local material. Produces a written summary saved beside the ticket. Use when the knowledge needed is outside this codebase. |
| `prototype` | With a person | Make something cheap and rough to react to: an outline, a stub, a sketch of the behavior. Raises the fidelity of the conversation. Use when "how should this look or behave" is the real question. |
| `interview` | With a person | Structured conversation, one question at a time, to pin down what the person knows or wants. The default type. |
| `task` | Either | Manual work that must happen before a decision can be made. Signing up for a service so its API can be judged, provisioning access, moving data so its shape can be seen. This is the one type that does rather than decides, and it earns its place by unblocking a decision, never by delivering the destination. |

## Fog of war

The map is deliberately incomplete. Do not chart what you cannot yet see.

Beyond the live tickets lies fog: decisions you can tell are coming but cannot yet pin down, because they hang on questions still open. The map's **Not yet sharp** section is where that dim view gets written down, as loosely as the view allows. It doubles as a signpost for anyone reading where the effort is heading.

Do not pre slice the fog into ticket sized pieces. It is coarser than a ticket, and one patch of it may graduate into several tickets, or none, once the frontier reaches it.

**Not yet sharp** excludes anything already decided, anything already a live ticket, and anything out of scope.

## Out of scope

Fog only ever gathers toward the destination. The destination fixes the scope, so work beyond it is out of scope. It is not fog, and it does not belong in **Not yet sharp**.

It gets its own **Out of scope** section: work consciously ruled out of this effort. Scope, not sharpness, lands it there. Out of scope work never graduates; the frontier stops at the destination. It returns only if the destination is redrawn, and then as a fresh effort, not a resumption.

When a ticket that already exists turns out to sit past the destination, close it as `out-of-scope` and leave one line in the **Out of scope** section: the gist, why it is out, and a link to the closed ticket. It stays out of the decision list, which records the route actually walked. A scope boundary is not a step on that route.

## Asks vs acts

**Acts** in both modes. It reads, writes the map and tickets, and reports. It asks in three places, all deliberate:

- **Naming the destination**, at the start of charting. This fixes the scope of everything after it, so it is settled with the engineer, not guessed.
- **Any ticket typed `interview` or `prototype`**, which only resolve through live exchange with a person.
- **When several maps exist and none was named.** It lists them and asks; it does not pick.

It never invents an answer to a ticket, and never records a decision nobody made.

## Artifact ownership

Owns `docs/wayfinding/` entirely: the maps, the tickets, and any material saved beside a ticket while resolving it.

Never edits `docs/scope/`, `docs/specs/`, `AGENTS.md`, or any source code. Those stay owned by `/scope`, `/architect`, `/audit`, and `/develop`. When a map clears, this skill **recommends** the next command and stops; it does not run `/scope` or `/architect` itself, and it does not write their files on their behalf.

## Portability (any OS, any agent)

Any Agent Skills client on macOS, Linux, or Windows. No CLI beyond your agent's own file tools is required. No issue tracker, no external service, no network access needed.

Bundled files live in this skill's folder: `modes/chart.md`, `modes/work.md`, and `map-template.md`. Read only the mode file you routed to, and read `map-template.md` only at write time.

## Reference files

- `modes/chart.md`: charting a new map
- `modes/work.md`: resolving one ticket on an existing map
- `map-template.md`: the structure of `map.md` and of a ticket file
