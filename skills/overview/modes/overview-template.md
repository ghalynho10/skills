# Overview Template (read by /overview update at write time)

The section structure and field guidance for `docs/overview.md`. Read this only when you are about to write, not during the reading passes.

Use only the part between the START and END markers as the document structure. Everything after END is guidance for you, not content to copy.

---

=== OVERVIEW TEMPLATE START ===

# <Project name>

## What it is

<Two to four sentences. What the project does and who it is for, in plain language, assuming the reader knows nothing about the domain or the codebase. No stack names in this section; a reader should understand the product before they hear how it is built.>

## Why it exists

<The problem it solves, concretely. What was painful, missing, or manual before this existed. If the project is a rebuild or replacement, say what it replaced and what was wrong with it.>

## How it is built

<The stack and the shape: the main pieces, what each is responsible for, and how they fit together. Named technologies belong here. Keep it to what someone needs to hold in their head to reason about the system, not an exhaustive dependency list. A reader should come away able to picture the architecture, not able to reconstruct package.json.>

## Main surfaces

<The parts a user or a caller actually touches: pages, endpoints, commands, scheduled jobs, webhooks. One line each, what it is for. This is the "what can you actually do with it" section.>

## Decisions that shaped it

<The handful of choices that explain why the project looks like this rather than some other way. One line each, with a link to the spec that holds the full reasoning:

- **<the decision>**, <the one line why>. See [NNNN](specs/NNNN-title.md).

Load bearing only. If every decision is listed, none of them stand out. Where the reasoning is not recorded in any spec, write the decision and say the reasoning is not recorded, rather than inventing one.>

## Where things live

<A short map of the repo: what is in which directory, and what belongs there. Enough for a newcomer to navigate and to know where new code should go. Not a file listing.>

## Current state

<What is built and working, what is in progress, what is deliberately not built yet. Derived fresh from `docs/scope/` every run, so it stays honest. Being explicit about what is deliberately out of scope is as useful as listing what is done; it stops a reader assuming an absence is an oversight.>

---
*Last updated: <date>. Reference document, kept current by `/overview update`. Specs in `docs/specs/` are the source of truth for any decision.*

=== OVERVIEW TEMPLATE END ===

---

## Guidance (not part of the document)

**Section order is deliberate.** It moves from what a stranger needs first (what and why) through how it works, to what it can do, to why it is shaped this way, to where things are, to where it stands. A reader can stop at any point and have a coherent, if less complete, picture. Do not reorder.

**Omit any section with nothing real to say.** An empty heading is worse than no heading. A brand new project may legitimately have no `## Decisions that shaped it` yet.

**Length.** This is a reference document, not a book. If a section is running long, the detail probably belongs in a spec that this section links to instead. The whole document should be readable in one sitting and pasteable into a chat as context without dominating it.

**Voice.** Plain and factual. The prose telling lives in `docs/project-story.md` and is written by `/overview story`; do not let narrative flourish leak into this document, and do not let this document's structure leak into the story.

**Links.** Spec links are relative to the overview's own location (`specs/NNNN-title.md` when the overview sits at `docs/overview.md`). For a directory spec, link the `index.md`. For a monorepo, include the workspace segment.

**On the Last updated line.** Use today's actual date. Leave the sentence about specs being the source of truth intact on every write; it is what stops a future reader treating a stale overview as authoritative.
