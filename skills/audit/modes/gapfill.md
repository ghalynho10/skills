# Audit Mode: gap-fill (Phase 4: root AGENTS.md already exists)

Trigger: no area argument, codebase exists, AND root AGENTS.md already exists (including right after a legacy `CLAUDE.md` migration). Audit the whole codebase against what's written and fill the holes, conservatively.

Pre-flight additionally: note root `AGENTS.md`'s path, and list all nested `AGENTS.md` paths (excluding `node_modules` and `.git`) to pass inline.

The main thread does the gap-fill itself, following the GAP-FILL phase in `agent-prompt.md`. The scan reads the whole codebase against the existing docs: for a large repo, offload the reading to a read only `scout` subagent on the cheapest model (Claude Code: `haiku`, not inheriting the session model) that returns a compact map, then work from it; a small repo, read directly. Read `agent-prompt.md` at write time. The inputs to apply: `PHASE=gap-fill`, root AGENTS.md path, nested AGENTS.md paths list.

After the scan, handle proposals before applying:
- Nested docs you created for clearly undocumented areas → already written; list them in the relay.
- `ROOT_GAPS` and `PROPOSED_ADDITIONS` to existing files → ask (`Add them now` / `Show me the diff` / `Skip for now`), one option marked recommended, then:
<!-- ROOT-GAPS-APPLY:START (identical in modes/area.md and modes/gapfill.md; edit both or neither) -->
- On `Add them now`: locate the `ROOT_GAPS:` block and extract each line starting with `- `. Each line names its own target: `target section: ## <section>` goes under that heading in root `AGENTS.md`, `target file: <path>` goes into that file. Apply one Edit call per gap, into the target it names. Do not paraphrase.
<!-- ROOT-GAPS-APPLY:END -->
- `PROPOSED_ADDITIONS`: apply with `Edit` into the file each names, verbatim, no paraphrase.
- `CONTRADICTIONS` (docs the code disproves) → surface to the engineer, do not auto fix (these touch possibly curated lines). Relay each as "`<doc>` says *X*, but the code/spec shows *Y*" and let them decide (correct it, or update the code). Never silently overwrite.
