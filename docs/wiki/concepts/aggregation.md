# Aggregation

Aggregates (`#count`, `#sum`, `#min`, `#max`) compute a value from a collection of facts, under a closed-world reading of a fully computed lower stratum. They are needed for business thresholds (E2-ex) and are part of F2, non-recursive in v1.

> **Status in this project:** `F2` — not in v0 ([D6](../project/decisions.md#d6)); [E3](../project/requirements.md#e3); definition report 11 §4.4 (DRAFT); groups [OP-9](../project/open-questions.md#op-9); D8 caveat on counting Skolem terms ([D8](../project/decisions.md#d8)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Syntax: aggregate atoms with tuple keys (set of tuples, ASP-style), local vs group-by variables.
- [ ] Semantics (report 11 §4.4): grounded vs implicit groups (OP-9), empty collections, undefined cases, infinite collections.
- [ ] Stratification: aggregate edges as strict edges; non-recursive in v1; monotone recursive aggregation as future work (Ross-Sagiv, limit Datalog, Vadalog).
- [ ] Interaction with named terms: counting `manager(dave)` as an object; D8 caveat on distinct terms denoting the same individual.
- [ ] Soundness under incompleteness: partial sums are wrong, not partial; statuses (report 11 §6).
- [ ] Oracles: clingo integer aggregates with scaled decimals.
- [ ] Running example E2-ex.

## Related pages

- [exact decimals and rounding](../concepts/exact-decimals-and-rounding.md)
- [stratified negation](../concepts/stratified-negation.md)
- [perfect model semantics](../concepts/perfect-model-semantics.md)
- [completeness statuses](../concepts/completeness-statuses.md)
- [equality and una](../concepts/equality-and-una.md)

## Key references

- [report 11 §1.3, §4.4, OP-9, OP-10](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 09 §5.2](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 07 §4.2](../../preliminary-analysis/07-sota-theory.md)
- Ross, Sagiv. *Monotonic aggregation in deductive databases*. JCSS 1997 [U].
- Kaminski et al. *Foundations of declarative data analysis using limit Datalog programs*. IJCAI 2017 [U].
