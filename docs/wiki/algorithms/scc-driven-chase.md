# SCC-driven chase

Saturating the rule set component by component in the topological order of the GRD (or of the predicate graph), so that each SCC is run to fixpoint once its inputs are complete. This is a must-have technique (E4), the natural unit for statuses and budgets in F2, and the v0 evaluation skeleton.

> **Status in this project:** `v0` `F2` — [E4](../project/requirements.md#e4); evaluation units of report 11 §6.2 (DRAFT); spike scope ([E6](../project/requirements.md#e6)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Scheduling: topological order of SCCs, non-recursive components evaluated once, recursive ones by semi-naive iteration.
- [ ] Interaction with strata (negation, aggregation) and with the status machinery (units, fix(C), N1).
- [ ] Per-SCC algorithm choice by the analyser (materialise, rewrite, skip).
- [ ] Parallelism across independent SCCs [U].
- [ ] Graal SccChase and its hazards (report 02).
- [ ] v0-ex and E3-ex trap as examples.

## Related pages

- [graph of rule dependencies grd](../algorithms/graph-of-rule-dependencies-grd.md)
- [semi naive evaluation](../algorithms/semi-naive-evaluation.md)
- [chase variants](../algorithms/chase-variants.md)
- [completeness statuses](../concepts/completeness-statuses.md)
- [decidability analyser](../algorithms/decidability-analyser.md)

## Key references

- [report 02 §3](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 11 §6.2, §8.1](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 07 §2](../../preliminary-analysis/07-sota-theory.md)
- [README E4](../../preliminary-analysis/README.md)
