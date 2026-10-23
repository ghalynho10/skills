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

- **Pass:** the spec, or a panel before it is written, flags the notes' rigor as heavier than a solo portfolio tool needs, and either scales it to that goal (each guard it keeps protects money, data, or against a misleading result) or asks the engineer before building it in.
- **Fail:** the research devices (blind rulings, control items, a sealed hash, repeated rounds) are built in with no flag, or dropped with no flag.

Record the acceptance criteria count and the `Follows` line as notes; they are not scored.

## Results

| Date | Commit | Verdict | Notes |
|---|---|---|---|
| 2026-10-22 | 96e2c44 (baseline) | Fail | Built every device the notes asked for, unflagged: "a blind two arm study with a hash sealed key", "2 control marks drawn from the off arm", an entity round, a link round "only if entities `more_accurate`", then a 10 mark blind rejudge. A requirements panel asked whether to include the study but recommended "Both the release pass and the blind study", calling the design "what makes the result trustworthy". 26 acceptance criteria. **Follows**: "blinded evaluation with an allocation key sealed by a hash commitment (commit, then reveal), plus a feature toggle whose default is flipped by evidence." |
| 2026-10-22 | architect-proportion | Pass | Round 1, before any design: "Your scope notes ask for a fairly heavy method … That is more rigor than a reversible default strictly needs … So I'm asking about each piece rather than silently building it all or silently dropping it", then one panel per device. Rationale: "Each piece was kept on purpose because each is cheap and each guards against the one real risk, a misleading result." Self agreement is reported but no longer gates. But every recommended option kept its device (hash, controls, rejudge, gated link round), so taking the defaults still builds nearly the full study; the change made the rigor a decision rather than scaling it down. 30 acceptance criteria. **Follows**: "before and after comparison on a fixed eval set (the same run files), with blind human judging, condition labels sealed before ruling, and planted controls." |
