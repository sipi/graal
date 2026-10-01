# Soufflé

Soufflé (Oracle Labs / University of Sydney; C++, UPL) is a Datalog compiler for program analysis: staged compilation to C++, automatic index selection, specialised data structures, parallel semi-naive evaluation and cheap provenance annotations. Datalog only (no value invention beyond constructors/ADTs [U]).

> **Status in this project:** `background` — inspiration (indexing, provenance, compilation); possible v0 oracle.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] Index selection (minimum chain cover), eqrel, B-trees.
- [ ] Provenance by (rule, height) annotations (TOPLAS 2020).
- [ ] Staged compilation; when worth it.
- [ ] Use as v0 Datalog oracle.

## Related pages

- [semi naive evaluation](../algorithms/semi-naive-evaluation.md)
- [provenance and explanations](../concepts/provenance-and-explanations.md)
- [benchmarks and test oracles](../engineering/benchmarks-and-test-oracles.md)

## Key references

- [report 06 §1.6, §4, §5 ideas 5, 12, 17](../../preliminary-analysis/06-sota-engines.md)
- Jordan, Scholz, Subotić. *Soufflé: on synthesis of program analyzers*. CAV 2016 [U].
