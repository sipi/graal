# Provenance and explanations

An explanation is a finite proof tree showing why a fact or answer holds, expressed over the user's original rules. Provenance is the bookkeeping that makes explanations possible. E8 requires traceability to original rules; report 11 defines explanation leaves for F2 (lookup, invention, negation, aggregates).

> **Status in this project:** `F2` `open` (v0 inclusion open) — [E8](../project/requirements.md#e8); report 11 §5.3 (DRAFT); answer-level soundness via provenance [OP-10](../project/open-questions.md#op-10).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Proof trees: internal nodes as instances of original rules; leaf kinds (report 11 §5.3) including `invented f(s̄)` and `not a`.
- [ ] Provenance designs (report 06 §4): semiring provenance, (rule, height) annotations, re-derivation on demand, why-provenance; costs.
- [ ] Recommended design: provenance as a parameter of the evaluation kernel; trigger recording for invented terms.
- [ ] Origin links from rewritten/optimised/translated rules (lb(K), T(P), simplification) back to source rules.
- [ ] Explanations of non-answers and of statuses (blocking units, analyser witnesses).
- [ ] Minimal explanations (not required in v1).

## Related pages

- [modeller diagnostics](../concepts/modeller-diagnostics.md)
- [completeness statuses](../concepts/completeness-statuses.md)
- [rule set simplification](../algorithms/rule-set-simplification.md)
- [souffle](../systems/souffle.md)
- [others](../systems/others.md)

## Key references

- [report 06 §4, §5 ideas 5 and 11](../../preliminary-analysis/06-sota-engines.md)
- [report 11 §5.3](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 09 §7 item 9](../../preliminary-analysis/09-skolem-function-frameworks.md)
- Green, Karvounarakis, Tannen. *Provenance semirings*. PODS 2007 [U].
- Zhao, Subotić, Scholz. *Debugging large-scale Datalog: a scalable provenance evaluation strategy*. TOPLAS 2020 [U].
