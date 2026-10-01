# Perfect-model semantics

The perfect model of a stratified program is computed stratum by stratum, each stratum as a least fixpoint over the fixed result of the lower ones. It is the semantics of F2 (with named functions and exact decimals) and generalises the least model of v0 Datalog.

> **Status in this project:** `F2` — semantics of v1 ([D1](../project/decisions.md#d1)); definition in report 11 §4 (DRAFT); v0 is the one-stratum special case ([D6](../project/decisions.md#d6)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definition (report 11 §4.2): strata, `T_i`, `M_i`, independence from the chosen stratification (Apt, Blair, Walker; Przymusinski).
- [ ] Possibly infinite models with function symbols (E3-ex trap).
- [ ] Relation with stable models and the well-founded semantics for stratified programs (they coincide) [U].
- [ ] Answers as truth in the perfect model vs certain answers (report 11 §5.1, Prop. 5).
- [ ] Lookup translation `lb(K)` as the program on which the semantics is defined.
- [ ] Worked examples E1-ex, E2-ex.

## Related pages

- [stratified negation](../concepts/stratified-negation.md)
- [aggregation](../concepts/aggregation.md)
- [datalog](../concepts/datalog.md)
- [lookup before invent](../concepts/lookup-before-invent.md)
- [completeness statuses](../concepts/completeness-statuses.md)

## Key references

- [report 11 §3-§5](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 09 §5-§6](../../preliminary-analysis/09-skolem-function-frameworks.md)
- Apt, Blair, Walker. *Towards a theory of declarative knowledge*. 1988 [U].
- Przymusinski. *On the declarative semantics of deductive databases and logic programs*. 1988 [U].
