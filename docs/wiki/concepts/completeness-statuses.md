# Completeness statuses

Every result returned by the engine carries a status saying whether it is complete (by static proof or by an observed fixpoint), sound but possibly incomplete, or unknown. This implements E1 and is what lets an AI agent distinguish facts from guesses. Report 11 refines the README's three statuses into four and adds the normative rule N1.

> **Status in this project:** `v0` (COMPLETE-STATIC only) `F2` — [E1](../project/requirements.md#e1); report 11 §6 (DRAFT); README lists three statuses, report 11 four (inconsistency I2, [open questions](../project/open-questions.md#known-inconsistencies)); [OP-10](../project/open-questions.md#op-10), [OP-11](../project/open-questions.md#op-11).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] The README three statuses (static proof, dynamic fixpoint, not guaranteed) and report 11's four (COMPLETE-STATIC, COMPLETE-DYNAMIC, NOT-GUARANTEED, UNKNOWN) — present both, flag I2.
- [ ] Evaluation units, `fix(C)`, complete / exposed / sound-partial units, rule N1 (report 11 §6.2).
- [ ] Proposition 3 (soundness and completeness, sketch) and its assumptions (fair monotone evaluation).
- [ ] Status of Boolean queries, queries with negation, constraint checks; blocking units in UNKNOWN results.
- [ ] Budgets and determinism; conformance tests must use deterministic budgets.
- [ ] Examples: E3-ex trap statuses (report 11 §10.4 table), report 12 V6b (spurious answers N1 prevents).
- [ ] v0: Datalog always terminates, so COMPLETE-STATIC unless a time budget cuts it.
- [ ] Q1 lead A (depth guard) as a status-producing safety net.

## Related pages

- [chase termination](../concepts/chase-termination.md)
- [decidability analyser](../algorithms/decidability-analyser.md)
- [stratified negation](../concepts/stratified-negation.md)
- [provenance and explanations](../concepts/provenance-and-explanations.md)
- [modeller diagnostics](../concepts/modeller-diagnostics.md)

## Key references

- [report 11 §6, §10.4](../../preliminary-analysis/11-f2-framework-definition.md)
- [README E1, Key theory points, Q1](../../preliminary-analysis/README.md)
- [report 12 §7.3](../../preliminary-analysis/12-invention-under-negation.md)
- [report 07 §1.1](../../preliminary-analysis/07-sota-theory.md)
