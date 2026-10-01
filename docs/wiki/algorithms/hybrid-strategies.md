# Hybrid strategies

Combining materialisation and rewriting: materialise the lower part of the rule set (e.g. FES or negated strata), rewrite the query with the upper part (e.g. FUS). This covers the classical FES-below/FUS-above decomposition and D5's stratum-by-stratum rewriting under negation, plus the default per-query selection policy.

> **Status in this project:** `F2` — [E10](../project/requirements.md#e10) (d), [D5](../project/decisions.md#d5); report 11 §8.4-§8.5 (DRAFT); hybrid rewriting possibly deferred to v1.1 ([OP-17](../project/open-questions.md#op-17)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Kiabora-style decomposition along GRD strata (FES then FUS, BTS then FUS).
- [ ] D5: materialise the cones of negated/aggregated predicates, rewrite the rest stratum by stratum (Prop. 6).
- [ ] Combined approach (Lutz et al.): unsound intermediate model plus filtering — soundness stated on returned answers.
- [ ] Default selection policy (report 11 §8.5) and cross-checking strategies in test mode.
- [ ] Examples: E1-ex hybrid rewriting.

## Related pages

- [precomputed rewriting](../algorithms/precomputed-rewriting.md)
- [query rewriting pure](../algorithms/query-rewriting-pure.md)
- [scc driven chase](../algorithms/scc-driven-chase.md)
- [decidability analyser](../algorithms/decidability-analyser.md)
- [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md)

## Key references

- [report 07 §2](../../preliminary-analysis/07-sota-theory.md)
- [report 11 §8.4-§8.5, §10.1](../../preliminary-analysis/11-f2-framework-definition.md)
- [README D5, E10](../../preliminary-analysis/README.md)
