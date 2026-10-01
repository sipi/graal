# Rule-set simplification

An optimisation pass that rewrites the rule set into a logically equivalent, cheaper one (fewer or smaller rules, smaller SCCs), while keeping the original set and full traceability. Each transformation needs a proof sheet. Includes premise simplification using other rules with a decreasing measure (E12). The deliverable is postponed (D4).

> **Status in this project:** `later` — [E8](../project/requirements.md#e8), [E9](../project/requirements.md#e9), [E12](../project/requirements.md#e12); postponed by [D4](../project/decisions.md#d4).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Catalogue of transformations and their status (report 07 §3.2): equivalences vs conservative extensions.
- [ ] E12 premise simplification `{a→b, a∧b→c} ≡ {a→b, a→c}` and the termination measure.
- [ ] Transformations that shrink GRD SCCs (report 07 §3.3); recompute GRD after each.
- [ ] Proof-sheet format (E9) and differential tests; Lean later.
- [ ] Traceability: origin links, explanations over original rules.
- [ ] Named functions: which transformations stay valid ("Skolem equivalence", report 09 §7 item 7).
- [ ] Prior art: InteGraal redundancy/forgetting, PDQ certificates, egglog equality saturation (research).

## Related pages

- [equivalence notions](../concepts/equivalence-notions.md)
- [provenance and explanations](../concepts/provenance-and-explanations.md)
- [graph of rule dependencies grd](../algorithms/graph-of-rule-dependencies-grd.md)
- [test strategy](../engineering/test-strategy.md)

## Key references

- [report 07 §3, §5](../../preliminary-analysis/07-sota-theory.md)
- [README E8, E9, E12, D4](../../preliminary-analysis/README.md)
- [report 06 §5 ideas 11, 16](../../preliminary-analysis/06-sota-engines.md)
- [report 09 §7](../../preliminary-analysis/09-skolem-function-frameworks.md)
