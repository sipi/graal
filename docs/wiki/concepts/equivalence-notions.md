# Equivalence notions for rule sets

Rule-set optimisation must preserve meaning, but "same meaning" has several precise readings: logical equivalence, conservative extension, query (answer) equivalence, and, with negation or named functions, strong or uniform equivalence. E8 requires logical equivalence; this page defines the notions and how to check them.

> **Status in this project:** `later` — [E8](../project/requirements.md#e8), [E9](../project/requirements.md#e9), [E12](../project/requirements.md#e12); transformation deliverable postponed ([D4](../project/decisions.md#d4)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definitions: logical equivalence, model- and deductive-conservative extension, query equivalence relative to schemas (report 07 §3.1).
- [ ] Freezing proposition: entailment of a rule reduces to CQ entailment on the frozen body; decidability inherited from decidable classes.
- [ ] CQ-equivalence over the full signature equals logical equivalence for positive rules; undecidability with EDB/IDB separation (Shmueli).
- [ ] Strong and uniform equivalence for programs with negation; "Skolem equivalence" for named functions (report 09 §7 item 7).
- [ ] Which transformations are equivalences vs conservative extensions (report 07 §3.2).
- [ ] Proof-sheet format (E9).

## Related pages

- [rule set simplification](../algorithms/rule-set-simplification.md)
- [foundations](../concepts/foundations.md)
- [provenance and explanations](../concepts/provenance-and-explanations.md)

## Key references

- [report 07 §3](../../preliminary-analysis/07-sota-theory.md)
- [report 09 §7](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [README E8, E9, E12, D4](../../preliminary-analysis/README.md)
- Sagiv. *Optimizing Datalog programs*. 1988 [U].
- Lifschitz, Pearce, Valverde. *Strongly equivalent logic programs*. ACM TOCL 2001 [U].
