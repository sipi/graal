# Precomputed rewriting of registered queries

For queries known in advance (pre-registered), the rewriting can be computed once, stored and evaluated directly on the data, saving considerable run time when the rewriting is bounded (E10 c). Stored rewritings must be invalidated when rules change, and under D5 they are allowed only for queries that depend on no negation.

> **Status in this project:** `F2` — [E10](../project/requirements.md#e10); [D5](../project/decisions.md#d5) guard; report 11 §8.3-§8.4; [OP-16](../project/open-questions.md#op-16), [OP-18](../project/open-questions.md#op-18).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Pre-registered queries (`@query`), rewriting to base predicates, saturation up to subsumption, analysis budget.
- [ ] Bounded rewriting as a static completeness proof (COMPLETE-STATIC).
- [ ] D5 guard and its scope (OP-16: aggregates, lookup-induced edges; report 12 T4 EDB-only negation).
- [ ] Invalidation: fingerprints of the query cone vs any rule change (OP-18); data changes do not invalidate.
- [ ] Storage and versioning of rewritings.
- [ ] Example: report 11 §10.3 O0 contrast `mgrOfPaul`.

## Related pages

- [query rewriting pure](../algorithms/query-rewriting-pure.md)
- [hybrid strategies](../algorithms/hybrid-strategies.md)
- [decidability analyser](../algorithms/decidability-analyser.md)
- [incremental maintenance](../algorithms/incremental-maintenance.md)

## Key references

- [README E10, D5](../../preliminary-analysis/README.md)
- [report 11 §8.3-§8.5, §10.3](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 12 §8 T4](../../preliminary-analysis/12-invention-under-negation.md)
