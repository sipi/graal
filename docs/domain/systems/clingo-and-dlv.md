# clingo and DLV

clingo (Potassco, University of Potsdam) and DLV/DLV2 (University of Calabria) are answer set programming systems: grounders plus solvers for logic programs with function symbols, default and classical negation, disjunction, constraints and aggregates. On stratified programs they compute the unique stable model, so they also serve as reference implementations of stratified semantics.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Overview: licences, versions, status (as of a date).
- [ ] Grounding with function symbols; finitely ground programs; non-termination on infinite models.
- [ ] Aggregates (integer arithmetic; scaling decimals).
- [ ] Multi-shot solving; Python API.
- [ ] DLV∃ (existential rules, shy programs, parsimonious chase) and magic sets in DLV2.
- [ ] Use as reference systems for stratified negation and aggregation.

## Related pages

- [logic programming and ASP](../concepts/logic-programming-and-asp.md): semantics.
- [aggregation](../concepts/aggregation.md): ASP aggregates.
- [benchmarks and test oracles](../evaluation/benchmarks-and-test-oracles.md): use as oracle.

## Key references

- M. Gebser, R. Kaminski, B. Kaufmann, T. Schaub. *Multi-shot ASP solving with clingo*. TPLP 19(1), 2019.
- M. Gebser, R. Kaminski, B. Kaufmann, T. Schaub. *Answer Set Solving in Practice*. Morgan & Claypool, 2012.
- M. Alviano, F. Calimeri, C. Dodaro, D. Fuscà, N. Leone, S. Perri, F. Ricca, P. Veltri, J. Zangari. *The ASP system DLV2*. LPNMR 2017.
- N. Leone, G. Pfeifer, W. Faber, T. Eiter, G. Gottlob, S. Perri, F. Scarcello. *The DLV system for knowledge representation and reasoning*. ACM TOCL 7(3), 2006.
- N. Leone, M. Manna, G. Terracina, P. Veltri. *Fast query answering over existential rules*. ACM TOCL 20(2), 2019.
