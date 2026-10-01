# Stratified negation

Negation as failure (`not p(X)`) under the closed-world reading, restricted to programs where no recursion goes through negation. It expresses defaults and exceptions (E1-ex) and is part of F2. The page covers stratification, its checks, safety, and how negation interacts with value invention and with incomplete computations.

> **Status in this project:** `F2` — not in v0 ([D6](../project/decisions.md#d6)); [E3](../project/requirements.md#e3), [D1](../project/decisions.md#d1); D2 lookup creates negative edges; soundness rule N1 (report 11 §6).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Syntax and safety of negated literals; anonymous variables inside negation (report 11 §2.1).
- [ ] Predicate dependency graph, strict edges, stratifiability check, canonical stratification by SCCs (report 11 §3.2).
- [ ] Non-stratifiability created by lookup-before-invent (D2, report 11 §3.3) and by hand-written invention (report 12, OP-22).
- [ ] Closed world vs open world; why negation over invented terms is delicate (report 07 §4, report 09 §5.1).
- [ ] Unsoundness of negation over an incomplete lower stratum; rule N1; statuses.
- [ ] The restricted chase as implicit self-negation (report 12 §3).
- [ ] Non-stratified alternatives (stable models, WFS) and delegation to ASP (out of scope for F2).
- [ ] Running example E1-ex; E3-ex corrected (negation on a data predicate that stops nothing).

## Related pages

- [perfect model semantics](../concepts/perfect-model-semantics.md)
- [completeness statuses](../concepts/completeness-statuses.md)
- [lookup before invent](../concepts/lookup-before-invent.md)
- [modeller diagnostics](../concepts/modeller-diagnostics.md)
- [clingo and dlv](../systems/clingo-and-dlv.md)

## Key references

- [report 11 §2-§4, §6](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 12 §3, §7](../../preliminary-analysis/12-invention-under-negation.md)
- [report 07 §4](../../preliminary-analysis/07-sota-theory.md)
- [report 09 §5.1](../../preliminary-analysis/09-skolem-function-frameworks.md)
- Apt, Blair, Walker 1988 [U].
- Gelfond, Lifschitz. *The stable model semantics for logic programming*. ICLP 1988 [U].
