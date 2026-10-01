# Stage 2 plan: filling the stub pages

Work plan for the writer agents of stage 2. The 48 stub pages created in stage 1 are split into four balanced batches that can be written in parallel. Each batch lists its pages, the sources to read, the cross-links it must respect, and the consistency risks to watch. Stage 1 pages (main, conventions, glossary, `project/*`, `concepts/foundations.md`) are not to be rewritten; propose changes to them in your final report instead.

> **Status in this project:** `v0` `F2` — work plan; governed by [conventions](conventions.md) and the [source-of-truth hierarchy](main.md#source-of-truth-hierarchy).
> **Page maturity:** reviewed-by-architect · 2026-10-01

## Rules common to all batches

1. Read first: [main](main.md), [conventions](conventions.md), [decisions](project/decisions.md), [requirements](project/requirements.md), [open questions](project/open-questions.md) (including *Known inconsistencies* I1-I8), [running examples](project/running-examples.md), and [foundations](concepts/foundations.md) as the model page.
2. Fill the template of [conventions §2](conventions.md#2-page-template); tick off or delete the TODO list; set *Page maturity* to `draft`.
3. Keep the status box, update it if needed, never upgrade a scope label without a decision.
4. Link to reports with section numbers instead of copying them; keep pages dense (typically 80-250 lines).
5. Use the running examples (v0-ex, E1-ex, E2-ex, E3-ex corrected) before inventing new ones.
6. Tag unverified claims `[U]`; inherit `[U]` / `[U-own]` / `[choice]` from reports.
7. Do not resolve inconsistencies: cite both sources and refer to the I-number. If you find a new one, add it to your final report (the architect adds it to the open questions page).
8. Edit only the files of your batch (plus, if needed, new glossary rows: report them rather than editing `glossary.md`, to avoid merge conflicts).
9. Commit once per batch with a descriptive message; `git pull --rebase` before pushing.

## Batch A: semantics core (12 pages)

| Page | Main sources |
|---|---|
| [concepts/datalog](concepts/datalog.md) | report 07 §1.3; report 11 §1-§4 |
| [concepts/existential-rules](concepts/existential-rules.md) | report 07 §1; report 09 §1.1 |
| [concepts/skolem-functions-and-terms](concepts/skolem-functions-and-terms.md) | report 09 §1, §3, §6; report 11 §1.1, §1.3, §4.1, §5.2; D1, D8 |
| [concepts/labelled-nulls](concepts/labelled-nulls.md) | reports 02 §3, 04 §2, 07 §4; report 11 OP-13; E5 |
| [concepts/conjunctive-queries-and-ucq](concepts/conjunctive-queries-and-ucq.md) | report 11 §1.6, §5; report 07 §2 |
| [concepts/perfect-model-semantics](concepts/perfect-model-semantics.md) | report 11 §3-§5 |
| [concepts/stratified-negation](concepts/stratified-negation.md) | report 11 §2-§4, §6; report 12 §3, §7; report 07 §4 |
| [concepts/aggregation](concepts/aggregation.md) | report 11 §4.4, OP-9, OP-10; report 09 §5.2 |
| [concepts/equality-and-una](concepts/equality-and-una.md) | report 09 §1.3, §4; D2, D8, Corrections, Q1 |
| [concepts/exact-decimals-and-rounding](concepts/exact-decimals-and-rounding.md) | D7; report 11 §1.2, §4.3, OP-5..OP-8 |
| [concepts/lookup-before-invent](concepts/lookup-before-invent.md) | D2; report 11 §1.5, §3, §4.5, §10.3; report 12 §1.2, §7 |
| [concepts/function-graph-translation-tp](concepts/function-graph-translation-tp.md) | D3; report 09 §1.2, §1.4, §3; report 11 §9 |

- **Cross-links to respect:** foundations (notation), completeness-statuses (batch B) for anything about incomplete strata, chase-variants (batch B) for chase readings, decidability-classes (batch B) for class transfer.
- **Consistency risks:**
  - I1 (rounding mode names and default): D7 prevails; present report 11 names as superseded, do not pick a negative-number behaviour.
  - I3 (E3-ex reading): use the corrected `not isCompanyDirector` reading as *the* E3-ex; present `hasManager`/`recordedManager`/`hasBoss` as report variants.
  - I7 (D8 vs free-constructor reading): present both, label as open.
  - Do not describe F2 as having existential variables; nulls exist only in the data model (D1, OP-13 `[choice]`).
  - Report 11 is a DRAFT: say "report 11 proposes" for every `[choice]`.

## Batch B: decidability, guarantees and analysis (12 pages)

| Page | Main sources |
|---|---|
| [concepts/decidability-classes](concepts/decidability-classes.md) | report 07 §1; report 09 §2-§3; report 11 §7 |
| [concepts/chase-termination](concepts/chase-termination.md) | report 07 §1.4; report 09 §2; report 11 §6.4, §7 |
| [concepts/completeness-statuses](concepts/completeness-statuses.md) | report 11 §6, §10.4; README E1, key theory points, Q1; report 12 §7.3 |
| [concepts/equivalence-notions](concepts/equivalence-notions.md) | report 07 §3; report 09 §7; E8, E9, E12 |
| [concepts/provenance-and-explanations](concepts/provenance-and-explanations.md) | report 06 §4-§5; report 11 §5.3 |
| [concepts/modeller-diagnostics](concepts/modeller-diagnostics.md) | README Q1 lead C, D2; report 11 §3.3, §7.3; report 12 §7-§8 |
| [algorithms/chase-variants](algorithms/chase-variants.md) | report 07 §1.4; report 12 §3; report 02 §3; report 05 §1.4 |
| [algorithms/piece-unifiers](algorithms/piece-unifiers.md) | report 07 §1.3, §2; report 02 §3; report 11 §8.3 |
| [algorithms/graph-of-rule-dependencies-grd](algorithms/graph-of-rule-dependencies-grd.md) | report 07 §1.3; report 02 §3; report 11 §3.2, §7 |
| [algorithms/decidability-analyser](algorithms/decidability-analyser.md) | README E2/E7, D6; report 11 §7, §8.5; report 07 §1-§2; report 02 §3 |
| [algorithms/rule-set-simplification](algorithms/rule-set-simplification.md) | report 07 §3, §5; E8, E9, E12, D4 |
| [algorithms/blocking-of-recursive-chains](algorithms/blocking-of-recursive-chains.md) | README Q1 lead B; report 06 §1.2; report 09 §2 |

- **Cross-links to respect:** completeness-statuses is the single place defining the statuses and N1; others link to it. decidability-classes is the single place for class definitions; chase-termination links there for sufficient conditions. decidability-analyser links to modeller-diagnostics for output messages.
- **Consistency risks:**
  - I2 (three vs four statuses): present both, report 11's four as the more specific DRAFT proposal.
  - Blocking (Q1 lead B) is **not decided**; GBTS algorithms are out of scope (E10). Do not write it as planned work.
  - Modeller diagnostics format is not specified (lead C): describe needs and candidates only.
  - Rule-set simplification is postponed (D4): write as background and future design, not as v0/F2 work.
  - Complexity bounds tagged `[U]` in report 07 must keep the tag.

## Batch C: evaluation algorithms and engineering core (12 pages)

| Page | Main sources |
|---|---|
| [algorithms/homomorphism-search](algorithms/homomorphism-search.md) | report 02 §3; report 04 §1; report 06 §5; E4 |
| [algorithms/semi-naive-evaluation](algorithms/semi-naive-evaluation.md) | report 11 §6.4, §8.1; report 06 §5; report 05 §1.3 |
| [algorithms/worst-case-optimal-joins](algorithms/worst-case-optimal-joins.md) | report 06 §5 idea 2; report 05 §1, §3; report 08 §1.1 |
| [algorithms/scc-driven-chase](algorithms/scc-driven-chase.md) | report 02 §3; report 11 §6.2, §8.1; E4 |
| [algorithms/query-rewriting-pure](algorithms/query-rewriting-pure.md) | report 07 §2; report 02 §3; report 11 §8.3; report 06 §5 idea 9 |
| [algorithms/backward-chaining-and-tabling](algorithms/backward-chaining-and-tabling.md) | report 11 §8.2, §10.4; report 07 §2; report 09 §2 |
| [algorithms/precomputed-rewriting](algorithms/precomputed-rewriting.md) | E10, D5; report 11 §8.3-§8.5; report 12 §8 T4 |
| [algorithms/hybrid-strategies](algorithms/hybrid-strategies.md) | report 07 §2; report 11 §8.4-§8.5, §10.1; D5 |
| [algorithms/incremental-maintenance](algorithms/incremental-maintenance.md) | report 07 §6; report 06 §5 idea 7 |
| [engineering/architecture-principles](engineering/architecture-principles.md) | D1, D6, E5; report 02 §2, §5; report 06 §5; report 11 §1, §6 |
| [engineering/test-strategy](engineering/test-strategy.md) | E9, E11; report 11 §6.4, §8.5, §10; report 12 §9 |
| [engineering/benchmarks-and-test-oracles](engineering/benchmarks-and-test-oracles.md) | report 06 §3; report 09 §1.4; report 11 §9.2; report 12 §8-§9; reports 03, 05 |

- **Cross-links to respect:** piece-unifiers and GRD (batch B) for rewriting foundations; completeness-statuses (batch B) for every status mention; systems pages (batch D) for implementation prior art.
- **Consistency risks:**
  - v0 is positive Datalog only (D6): mark clearly which parts of each algorithm are v0 and which are F2. Whether v0 includes rewriting, backward chaining, incremental maintenance or explanations is **open** (see [roadmap](project/roadmap.md)).
  - D5 guard scope (OP-16) and hybrid rewriting timing (OP-17) are `[choice]`, not decisions.
  - Architecture: only the invariants fixed by D1, D6, E5 are decided; everything else is "open until phase 3". Do not choose the language (E6) or the code location.
  - Test strategy must state that expected outputs come from the specification, not from an implementation (E11).

## Batch D: systems, language and legal (12 pages)

| Page | Main sources |
|---|---|
| [systems/graal](systems/graal.md) | reports 02, 03, 01 §1; report 09 §1.4; report 11 §9.2 |
| [systems/integraal](systems/integraal.md) | report 04; report 01 §2; report 06 §1.7 |
| [systems/nemo](systems/nemo.md) | report 05; report 06 §1.3; report 12 §8 T5, Appendix C |
| [systems/vlog-rulewerk](systems/vlog-rulewerk.md) | report 06 §1.3; report 01 §3 |
| [systems/rdfox](systems/rdfox.md) | report 06 §0.1, §1.1; README Clarifications; report 09 §1.3 |
| [systems/vadalog](systems/vadalog.md) | report 06 §0.2, §1.2; README Clarifications |
| [systems/jena-rules](systems/jena-rules.md) | **report 13** (`../preliminary-analysis/13-jena-rule-engine.md`, written concurrently: wait for it or read its latest version); reports 02, 03 |
| [systems/souffle](systems/souffle.md) | report 06 §1.6, §4, §5 |
| [systems/clingo-and-dlv](systems/clingo-and-dlv.md) | report 06 §1.4-§1.5; report 09 §1.4; report 12 Appendix A; report 11 §9.2 |
| [systems/others](systems/others.md) | report 06 §1.8-§1.12, §5; report 01 §3 |
| [engineering/language-choice-kotlin-vs-rust](engineering/language-choice-kotlin-vs-rust.md) | README E6; reports 08, 05 |
| [legal/licensing-and-provenance](legal/licensing-and-provenance.md) | README Legal note; reports 01, 03 |

- **Cross-links to respect:** each system page links to the concept/algorithm pages of the ideas it borrows (batches A-C) and to benchmarks-and-test-oracles for oracle use.
- **Consistency risks:**
  - I4 (spike duration 2+3 vs 3+4 weeks) and I8 (report 05 Kotlin vs report 08 Rust): present both; E6 stays **open**.
  - Licences: tag every licence not verified against the source `[U]`; the legal note must be validated by counsel; no legal conclusion beyond the README.
  - Oracle boundaries must match report 11 §9.2 and report 12 T5/T6 exactly (Nemo: constant answers of positive queries only).
  - Jena page depends on report 13: summarise, do not duplicate; if report 13 contradicts another report, record it as a new inconsistency.
  - System facts change: date-stamp maintenance and version claims ("as of 2026-09-30").

## After stage 2

The architect (or a reviewer agent) cross-checks all pages, merges proposed glossary rows, updates the *Known inconsistencies* table, and sets `reviewed-by-architect` where appropriate. The owner then validates theory pages (`validated-by-owner`).
