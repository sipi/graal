# Backward chaining and tabling

Goal-directed evaluation at query time: SLD resolution with tables (SLG) that memoise subgoals and detect completion, or magic-set rewriting that makes bottom-up evaluation goal-directed. It can complete queries even when the full model is infinite (E3-ex), which gives COMPLETE-DYNAMIC statuses.

> **Status in this project:** `F2` `open` (v0 inclusion open) — [E10](../project/requirements.md#e10) (b); report 11 §8.2 (DRAFT); goal-directed decidability [OP-15](../project/open-questions.md#op-15).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] SLD resolution, tabling (SLG), completion, stratified completion for negation.
- [ ] Magic sets (Datalog and existential rules, Alviano et al.) and sideways information passing.
- [ ] Completeness and termination: finitely recursive queries, finitary programs, FDNC.
- [ ] Evaluation units for statuses (tables, SCCs of subgoals).
- [ ] Report 11 §10.4 example: backward chaining completes `externalContact` where materialisation is UNKNOWN.
- [ ] Implementation references: XSB, Soufflé magic sets [U].

## Related pages

- [query rewriting pure](../algorithms/query-rewriting-pure.md)
- [hybrid strategies](../algorithms/hybrid-strategies.md)
- [completeness statuses](../concepts/completeness-statuses.md)
- [decidability classes](../concepts/decidability-classes.md)

## Key references

- [report 11 §8.2, §10.4](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 07 §2](../../preliminary-analysis/07-sota-theory.md)
- [report 09 §2](../../preliminary-analysis/09-skolem-function-frameworks.md)
- Chen, Warren. *Tabled evaluation with delaying for general logic programs*. JACM 1996 [U].
- Alviano, Leone, Manna, Terracina, Veltri. *Magic-sets for Datalog with existential quantifiers*. Datalog 2.0, 2012.
