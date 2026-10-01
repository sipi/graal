# How agents work here

The working rules for AI agents on this project: the development model, what an agent may and may not decide, how to handle conflicts, language and commit conventions, and where things live in the repository. Read this before your first commit.

> **Status in this project:** `v0` `F2` `later` — governed by [E11](requirements.md#e11) (development model), [E9](requirements.md#e9) (proof sheets), [conventions](../conventions.md).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

## Development model (E11)

- **AI agents write 100% of the code**, tests and documentation.
- **The project owner** (original author of Graal, expert in existential rules) **validates** theoretical choices and architecture. Agents propose; the owner decides.
- **Tests come first and are independent of the implementation.** The conformance and quality suite and the benchmarks are derived from the specification (README decisions, report 11 once validated), not from what an implementation happens to do. An agent implementing a feature must not edit expected outputs to make its code pass; a wrong expected output is a specification issue to report. See [test strategy](../engineering/test-strategy.md).
- **Acceptance criterion for code** (README Next steps, phase 4): passes the conformance suite, and the architecture remains reviewable by the owner.

## Rules of conduct

1. **Never silently change semantics.** Any change to what the engine computes (a definition, an answer, a status, a diagnostic) must be traceable to a decision, a requirement or a validated report. If your task seems to require a semantic change, stop and report it.
2. **Never invent decisions.** If something is not decided, label it `open` and add it to [open questions](open-questions.md). Recommended defaults are welcome, clearly labelled as proposals.
3. **Report conflicts, do not resolve them.** If two sources disagree (README vs report, report vs report, wiki vs anything), follow the higher source of the [hierarchy](../main.md#source-of-truth-hierarchy) for your immediate work, record the conflict in [known inconsistencies](open-questions.md#known-inconsistencies) and mention it in your final report.
4. **Respect scope.** v0 is positive Datalog ([D6](decisions.md#d6)); do not add negation, functions or existentials to v0 code paths. But do not paint v0 into a corner: three term kinds and strata are designed in from day one ([architecture principles](../engineering/architecture-principles.md)).
5. **Tag what you did not verify** (`[U]`, see [conventions §6](../conventions.md#6-tagging-claims)). Never present a `[U-own]` proposition as a theorem: it needs a proof sheet (E9).
6. **Do not port Graal code.** Re-implement from publications and specifications; see [licensing](../legal/licensing-and-provenance.md). Reading Graal to understand behaviour, or running it as an oracle, is fine.
7. **Stay in your lane.** Touch only the files your task names. Other agents may work concurrently on other folders.
8. **Explanations cite original rules** (E8): never expose internal rewritten rules to users without the mapping back.

## Language

English everywhere: code, identifiers, comments, documentation, commit messages, test names. The owner is French-speaking; the [glossary](../glossary.md) gives French equivalents. No emojis.

## Commit and push conventions

Observed convention in this repository's history:

```
<Imperative subject line, sentence case, no trailing period, <= 72 chars>

<Body: what and why, wrapped at ~72 columns. Optional for trivial changes.>

Co-Authored-By: <agent model> <noreply@anthropic.com>
Claude-Session: <session URL>
```

- One logical change per commit. Mention in the body any conflict found or any semantic question raised.
- Work on the branch you are given; do not push to the default branch.
- Several agents may push to the same branch: before pushing, `git pull --rebase`, then push; on a non-fast-forward rejection, rebase again; retry network failures with backoff.
- Leave no untracked files; temporary files go to your scratchpad, not the repository.

## Where things live

| Path | Content | Who edits |
|---|---|---|
| `docs/preliminary-analysis/README.md` | requirements E*, decisions D*, corrections, Q* (authoritative) | owner, or agents recording an explicit owner decision |
| `docs/preliminary-analysis/NN-*.md` | analysis reports 01-13 (11 is a DRAFT framework definition) | report authors; fixes need care |
| `docs/wiki/` | this wiki | wiki agents, following [conventions](../conventions.md) |
| `graal-*/`, `rdf4j-common/`, `pom.xml` | legacy Graal code base (Java, CeCILL 2.1), kept as reference and oracle | do not modify for the new engine |
| `docs/dev/` | legacy Graal release process | legacy |
| new engine code, conformance suite, benchmarks | **not yet created; location open** | to be decided in phase 3 |

## Related pages

- [conventions](../conventions.md), [decisions](decisions.md), [open questions](open-questions.md), [test strategy](../engineering/test-strategy.md), [roadmap](roadmap.md).

## References

- [README](../../preliminary-analysis/README.md): E11, E9, Legal note, Next steps.
- [Report 08 §2.4](../../preliminary-analysis/08-kotlin-vs-rust.md) (proof in Lean + tested implementation).
