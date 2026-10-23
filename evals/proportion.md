# Eval: rigor in the design matches the project's goal

A replay of a real miss: `/architect` on tracepath, a solo personal project, was handed notes asking for research style rigor and built all of it into the evaluation of an accuracy fix: blind rulings, control items, a sealed hashed arm key, and staged rounds of rulings. The workflow tier scales the pipeline; nothing scaled the design itself.

## Input

A repo holding only these two files, then `/architect extraction accuracy`.

`AGENTS.md`:

> tracepath. A command line tool that answers "why was this built this way?" from a project's decision records: a model extracts each record into small cited items (entities and links), stored in a graph, and a question walks the graph. Solo personal project, built to learn and to show in a portfolio. One user: the author. Stack: Python 3.12, Typer, Neo4j in Docker, Claude Sonnet via the Anthropic SDK for extraction. One extraction call costs about $0.20. Run files in `artifacts/runs/` are the source of truth; the graph is rebuilt from them.

`docs/scope/scope.md`:

> **Workflow:** Beta
>
> ### 13. Extraction accuracy · needs a decision
> Whether extracted items are right, not only repeatable. The last check failed the accuracy bar (at least two thirds of sampled entity marks and of link marks per unit ruled correct by the author) on 3 of 6 units. Candidate fix: a code pass that releases an acceptance criterion item held only for its `multi_condition_split` flag when its three extraction runs agree on its id and type.
> **Method notes:** compare the pass off against on over the same run files. The author rules marks blind: opaque ids, shuffled, flags hidden, with 2 control items per unit from the off arm mixed in. Hash and seal the arm key before ruling starts. Rule entities first; if they pass, run a second round of rulings on links, then re-judge 10 rulings blind for self agreement.
> **Done when:** we know whether the fix makes extraction more accurate, and it is on or off by default accordingly.
> - [ ] Design it (spec): `/architect extraction accuracy`

No engineer answers questions: the agent writes each decision panel, with its recommended option, to `panels.md`, then takes that option. At the cross check panel it writes the panel and picks `Skip`, so the run ends there.

## Scoring (by hand)

- **Pass**, all three:
  - For each piece of rigor the notes ask for, the recommended option is the lightest one that still covers a named, concrete failure for this project's goal.
  - A piece that guards against no named failure is recommended as dropped or deferred.
  - The spec's acceptance criteria count does not exceed the main baseline's (26).
- **Fail:** any clause missed. "It could mislead" alone is not a named failure.

Record the `Follows` line as a note.

## Held out variant

Run once after the wording is final, never tuned against. Same `AGENTS.md`; in the scope row, the **Method notes** line is replaced by:

> **Process notes:** every spec gets sign off from three reviewers before build, and every module gets mutation testing before merge.

Scored by the first two clauses; with no main run of this variant, its acceptance criteria count is recorded, not scored.

## Results

| Date | Commit | Verdict | Notes |
|---|---|---|---|
| 2026-10-22 | 96e2c44 (baseline) | Fail | Built every device the notes asked for, unflagged: "a blind two arm study with a hash sealed key", "2 control marks drawn from the off arm", an entity round, a link round "only if entities `more_accurate`", then a 10 mark blind rejudge. A requirements panel asked whether to include the study but recommended "Both the release pass and the blind study". 26 acceptance criteria. **Follows**: "blinded evaluation with an allocation key sealed by a hash commitment (commit, then reveal), plus a feature toggle whose default is flipped by evidence." Re-scored 2026-10-23 under the three clause rule: still Fail. |
| 2026-10-22 | f2ff943 (first wording) | Fail (re-scored 2026-10-23) | Flagged the notes as "more rigor than a reversible default strictly needs" and asked one panel per device, but recommended keeping each. Hash: recommended "store its SHA256, and check the hash again", over "don't open it until scoring, no hash: same protection against peeking", so not the lightest option covering a named failure. Self agreement: kept as "report only" against no concrete failure, when "the default is easy to flip later" was offered. Controls ("every mark shown is one the pass released") and the entity first round order (saves rulings) pass. 30 acceptance criteria, over the baseline's 26. Scored Pass under the earlier flag and ask rule. **Follows**: "before and after comparison on a fixed eval set (the same run files), with blind human judging, condition labels sealed before ruling, and planted controls." |
| 2026-10-23 | architect-proportion (lighter option wording) | Pass | Round 3, before design: "Your method notes ask for a lot of ceremony … for each piece I name the failure it catches here and recommend the lighter option where a lighter check covers it." Hash: dropped for "commit it before you start ruling", since the hash "matters when someone else has to trust the result". Self agreement: dropped; "judge again only when a verdict lands within one ruling of its threshold". Blinding: kept, at its cheapest, for a named failure: "seeing the `multi_condition_split` flag tells you a row is a released item". Controls: kept, "without controls every row is a released mark and the blinding hides nothing". Entity first order: kept as already the lighter path. Also turned down hashing run files ("a guard for a problem the first option cannot have"). 23 acceptance criteria. **Follows**: "before and after on a fixed eval set (both arms over the same run files), with blind rating and control items." |
| 2026-10-23 | architect-proportion, held out (process notes) | Pass | Round 3: "Your scope row's notes ask for more process than this project's goal … usually calls for. I'm flagging them so you decide, not dropping them silently." Three reviewer sign off: recommended "Your own review of this spec, plus the cross check", since the error reviewers would catch, "a decision rule bent to fit the result, is already blocked by writing the rule down before you mark". Mutation testing: recommended "A table of unit tests over the release rule, one row per case", since "a wrong release is the failure". 22 acceptance criteria (recorded, not scored). **Follows**: "before and after on a fixed eval set (decision rule fixed in advance) · feature flag … · derived state rebuilt from a source of truth." |
