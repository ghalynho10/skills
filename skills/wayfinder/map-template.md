# Map Template (read at write time)

The structure of `map.md` and of a ticket file. Read this only when you are about to write.

Use only the parts between the START and END markers as structure. Everything after the last END is guidance for you, not content to copy.

---

=== MAP TEMPLATE START ===

# <Effort name>

## Destination

<What reaching the end of this map looks like: the decision, specification, or change this effort is finding its way to. One or two lines. Every session reads this before choosing a ticket, so it has to be unambiguous.>

## Notes

<The domain. Anything every session should know or consult. Standing preferences for this effort. Keep it short; this is read every session.>

## Decisions so far

<!-- The index. One line per closed ticket: enough to judge relevance, then follow the link for the detail the ticket holds. Never restate the decision here. -->

- [<ticket title>](tickets/NNNN-<slug>.md): <one line gist of the answer>

## Not yet sharp

<!-- Fog: in scope, but you cannot state the question precisely yet. Written as loosely as the view allows. Graduates into tickets as the frontier advances, and is cleared from here when it does. -->

- <the suspected question or the area to revisit, however vaguely you can put it>

## Out of scope

<!-- Work consciously ruled beyond the destination. Never graduates. Kept so nobody re raises it. -->

- <the gist>, out of scope because <why>. ([<closed ticket title>](tickets/NNNN-<slug>.md), if it was ever a ticket)

=== MAP TEMPLATE END ===

---

=== TICKET TEMPLATE START ===

# <Ticket title>

Type: <research | prototype | interview | task>
Status: <open | closed | out-of-scope>
Blocked by: <ticket titles as links, or "nothing">
Claimed: <date and who, or empty if unclaimed>

## Question

<The one decision or investigation this ticket resolves. Stated precisely enough that
someone else could work it without asking what it means.>

## Answer

<!-- Written on resolution. Empty until then. -->

=== TICKET TEMPLATE END ===

---

## Guidance (not part of either document)

**The map is an index, not a store.** This is the rule most worth holding onto. Every decision lives in exactly one place, its own ticket. The map gists and links. If you find yourself writing a paragraph into the decision list, it belongs in the ticket.

**Open tickets are not listed on the map.** They are files in `tickets/`, found by reading the folder. Listing them on the map too would mean two places to keep in sync, and one of them would go stale.

**Omit an empty section rather than leaving a bare heading.** A new map legitimately has an empty decision list, so keep that one; but if there is no fog and nothing out of scope, leave those headings out until there is.

**Numbering.** Tickets are numbered in the order they are written, from `0001`, and the number never changes once assigned. New tickets from a resolution continue the sequence; they are not renumbered to sit near related work. The order of a ticket's number says when it was written, nothing more.

**Slugs stay readable.** `0003-which-watch-platforms.md`, not `0003-ticket.md` and not `0003-q3.md`. The filename is part of how a person navigates this.

**The `Claimed:` line is the whole concurrency mechanism.** Set it before working, leave it set after closing (it records who resolved it). An open ticket with an empty `Claimed:` line is free to take.

**Blocked by uses titles as links**, not bare numbers, same as everywhere else in this skill. `Blocked by: [Which watch platforms do our users use?](0001-which-watch-platforms.md)`.
