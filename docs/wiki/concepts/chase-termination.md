# Chase termination

When does forward chaining with value invention stop? Termination depends on the chase variant, on whether it is required for one instance or all instances, and on the order of rule applications; it is undecidable in general. This page collects the notions and results the analyser and the status machinery rely on.

> **Status in this project:** `background` `F2` — [E1](../project/requirements.md#e1), [E2](../project/requirements.md#e2)/[E7](../project/requirements.md#e7); certificates and witnesses (report 11 §7.3); budgets (report 11 §6.4, [OP-19](../project/open-questions.md#op-19)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Notions: termination on one instance vs all instances; ∀-sequence vs ∃-sequence for the restricted chase; fairness.
- [ ] Critical instance and Marnette's result; when it holds for named functions (positive, function-free data, built-in-free) and when it fails (negation, arithmetic).
- [ ] Undecidability results (Deutsch-Nash-Remmel, Gogacz-Marcinkowski, Carral et al. PODS 2025).
- [ ] Sufficient conditions (link decidability classes) and non-termination evidence (MFA cyclic term, RMFC).
- [ ] The abstraction `P^abs` of report 11 §7.1 (drop negation, arithmetic as invention).
- [ ] Dynamic termination (fixpoint observed) vs static certification; data-dependent termination (E3-ex with lookup).
- [ ] Budgets: depth, rounds, facts, digits, time; determinism.
- [ ] E3-ex trap as the canonical non-terminating case.

## Related pages

- [chase variants](../algorithms/chase-variants.md)
- [decidability classes](../concepts/decidability-classes.md)
- [completeness statuses](../concepts/completeness-statuses.md)
- [decidability analyser](../algorithms/decidability-analyser.md)
- [running examples](../project/running-examples.md)

## Key references

- [report 07 §1.4](../../preliminary-analysis/07-sota-theory.md)
- [report 09 §2](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 11 §6.4, §7](../../preliminary-analysis/11-f2-framework-definition.md)
- Deutsch, Nash, Remmel. *The chase revisited*. PODS 2008.
- Marnette. PODS 2009.
- Carral, Dragoste, Krötzsch. *Restricted chase (non)termination for existential rules with disjunctions*. IJCAI 2017.
