# Piece-unifiers

Piece-unifiers are the unification notion for existential rules: a query sub-conjunction (a "piece") can unify with a rule head only if the existential variables it touches are fully covered. They underpin GRD edges and PURE query rewriting (E4). In F2 without existential variables they reduce to classical term unification.

> **Status in this project:** `F2` (via T(P)) `later` (F3) — [E4](../project/requirements.md#e4); report 11 §8.3 (no piece-unifiers needed in F2 rewriting).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definition (König, Leclère, Mugnier, Thomazo): pieces, separating variables, most general piece-unifiers.
- [ ] Correctness of rewriting steps; single-piece vs aggregated unifiers.
- [ ] Use in GRD edges (dependency = existence of a piece-unifier, NP-complete in general, PTIME for atomic heads [U]).
- [ ] Reduction to term unification for F2 named functions (occurs check, function symbols unify only with themselves).
- [ ] Graal implementation notes and test gap (9 tests, report 02).
- [ ] Worked example: E3-ex existential reading with a query on managerOf.

## Related pages

- [query rewriting pure](../algorithms/query-rewriting-pure.md)
- [graph of rule dependencies grd](../algorithms/graph-of-rule-dependencies-grd.md)
- [existential rules](../concepts/existential-rules.md)
- [function graph translation tp](../concepts/function-graph-translation-tp.md)

## Key references

- [report 07 §1.3, §2](../../preliminary-analysis/07-sota-theory.md)
- [report 02 §3](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 11 §8.3](../../preliminary-analysis/11-f2-framework-definition.md)
- König, Leclère, Mugnier, Thomazo. *Sound, complete and minimal UCQ-rewriting for existential rules*. SWJ 2015.
- Baget, Leclère, Mugnier, Salvat. AIJ 2011.
