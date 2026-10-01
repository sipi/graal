# Lookup-before-invent

The default behaviour of named functions in F2 (D2): the value of `f(t̄)` is taken from data when recorded, and a new object is invented only otherwise. It is defined by a translation `lb(K)` into stratified negation, so it brings stratification constraints and diagnostics with it.

> **Status in this project:** `F2` — [D2](../project/decisions.md#d2); report 11 §1.5, §3.1, §3.3, §4.5 (DRAFT); [OP-2](../project/open-questions.md#op-2), [OP-3](../project/open-questions.md#op-3), [OP-16](../project/open-questions.md#op-16); interaction with [D5](../project/decisions.md#d5).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Declaration `@lookup f(x̄) = y :- λ`, implicit functionality constraint, D2 conflicts (OP-3).
- [ ] Translation `lb(K)`: `$L_f`, `$D_f`, look/invent variants, 2^k blow-up.
- [ ] Proposition 1 (non-stratifiability iff the lookup source depends on f) and the required diagnostic; generalisation to hand-written invention (report 12, OP-22).
- [ ] Proposition 2 (value semantics, Datalog-first correspondence).
- [ ] Lookup source options and `p@db` sugar (OP-2).
- [ ] Interaction with D5 guard (OP-16) and with T(P) bridge rules.
- [ ] Examples: report 11 §10.3 (`recordedManager`), report 12 V1/V5; note that the corrected E3-ex is *not* a lookup pattern (I3).

## Related pages

- [skolem functions and terms](../concepts/skolem-functions-and-terms.md)
- [stratified negation](../concepts/stratified-negation.md)
- [equality and una](../concepts/equality-and-una.md)
- [modeller diagnostics](../concepts/modeller-diagnostics.md)
- [hybrid strategies](../algorithms/hybrid-strategies.md)

## Key references

- [README D2](../../preliminary-analysis/README.md)
- [report 11 §1.5, §3, §4.5, §10.3](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 09 §4 (O2)](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 12 §1.2, §7](../../preliminary-analysis/12-invention-under-negation.md)
