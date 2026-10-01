# clingo and DLV

clingo (Potassco) and DLV/DLV2 (Calabria) are answer-set programming systems: grounders plus solvers for logic programs with function symbols, negation, aggregates and disjunction. They are the planned exact oracles for F2 features over invented terms (negation, aggregation), with integer arithmetic.

> **Status in this project:** `background` — oracle for F2 ([D1](../project/decisions.md#d1) features); README Next steps phase 2.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] Grounding with function symbols, finitely ground programs, non-termination on infinite models (E3-ex).
- [ ] Aggregates (integer only: scale decimals), stratified programs = unique stable model.
- [ ] How report 12 used clingo (encodings, `se` self-exempt encoding, depth guard).
- [ ] DLV∃ (existential rules, shy, parsimonious chase) and DLV2 magic sets.
- [ ] Licences (clingo MIT [U]; DLV2 terms [U]).
- [ ] Multi-shot solving as incremental API model; delegation of non-stratified negation.

## Related pages

- [benchmarks and test oracles](../engineering/benchmarks-and-test-oracles.md)
- [stratified negation](../concepts/stratified-negation.md)
- [aggregation](../concepts/aggregation.md)

## Key references

- [report 06 §1.4, §1.5](../../preliminary-analysis/06-sota-engines.md)
- [report 09 §1.4](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 12 Appendix A](../../preliminary-analysis/12-invention-under-negation.md)
- [report 11 §9.2](../../preliminary-analysis/11-f2-framework-definition.md)
