# Decidability analyser

The central component (E2/E7): it computes dependency graphs and strata, classifies each SCC with a portfolio of sufficient termination and decidability tests, produces certificates and witnesses, detects ill-formed programs, and selects the reasoning strategy per component. In v0 it recognises the plain Datalog fragment and dispatches it.

> **Status in this project:** `v0` (fragment recognition) `F2` (portfolio) — [E2](../project/requirements.md#e2)/[E7](../project/requirements.md#e7), [D6](../project/decisions.md#d6), [D2](../project/decisions.md#d2) (non-stratifiability detection); report 11 §7 (DRAFT); diagnostics [Q1](../project/open-questions.md#q1) lead C.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Pipeline: parse → well-formedness (safety, typing) → lb(K) → dependency graphs → stratification → per-SCC portfolio → labels → strategy selection.
- [ ] Portfolio order and costs (report 11 §7.2): term-free, WA, JA, AR, Γ-acyclicity, MSA, MFA, with budgets.
- [ ] Labels TERMINATES / NOT-CERTIFIED with certificates and witnesses (report 11 §7.3).
- [ ] Kiabora/Graal analyser as prior art (20 properties, report 02) and its bugs.
- [ ] Algorithm selection rules (report 07 §2 decision procedure; report 11 §8.5).
- [ ] Diagnostics output (link modeller diagnostics).
- [ ] v0: recognise positive Datalog and dispatch to the specialised engine.
- [ ] E3-ex trap as first test case (README).

## Related pages

- [decidability classes](../concepts/decidability-classes.md)
- [chase termination](../concepts/chase-termination.md)
- [graph of rule dependencies grd](../algorithms/graph-of-rule-dependencies-grd.md)
- [modeller diagnostics](../concepts/modeller-diagnostics.md)
- [completeness statuses](../concepts/completeness-statuses.md)
- [hybrid strategies](../algorithms/hybrid-strategies.md)

## Key references

- [README E2, E7, D6, Running examples](../../preliminary-analysis/README.md)
- [report 11 §7, §8.5](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 07 §1-§2](../../preliminary-analysis/07-sota-theory.md)
- [report 09 §2-§3](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 02 §3](../../preliminary-analysis/02-graal-architecture-audit.md)
- Leclère, Mugnier, Rocher. *Kiabora: an analyzer of existential rule bases*. RR 2013.
