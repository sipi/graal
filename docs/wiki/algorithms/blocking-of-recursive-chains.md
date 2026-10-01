# Blocking of recursive chains

A candidate technique (Q1 lead B) to give a finite representation of an infinite model when a recursive pattern is attached only to invented individuals: when a new link has the same type as an earlier one, stop expanding and keep a back-link; answer queries on the chain unfolded up to the query size. A simple case of GBTS-style blocking, potentially in F2 scope, not decided.

> **Status in this project:** `open` — [Q1](../project/open-questions.md#q1) lead B (not a decision); general GBTS algorithms out of scope ([E10](../project/requirements.md#e10)); related [OP-15](../project/open-questions.md#op-15).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Problem: E3-ex corrected trap (infinite manager chain).
- [ ] Blocking in tableaux and GBTS algorithms (Thomazo et al.) — what the simple case keeps.
- [ ] Type definition: atoms of the invented individual, including negated predicates (easy when data-only).
- [ ] Query answering on the unfolded chain up to query size; completeness argument to prove [U-own].
- [ ] Relation with Vadalog isomorphism pruning, FDNC, finitary programs.
- [ ] Interaction with statuses (would turn NOT-GUARANTEED into complete) and with diagnostics (lead C).
- [ ] Scope nuance: GBTS out (E10), simple chains possibly in (owner to decide). Potentially publishable.

## Related pages

- [open questions](../project/open-questions.md)
- [decidability classes](../concepts/decidability-classes.md)
- [modeller diagnostics](../concepts/modeller-diagnostics.md)
- [chase termination](../concepts/chase-termination.md)
- [vadalog](../systems/vadalog.md)

## Key references

- [README Q1](../../preliminary-analysis/README.md)
- [report 06 §1.2, §5 idea 4](../../preliminary-analysis/06-sota-engines.md)
- [report 09 §2 (FDNC, finitary)](../../preliminary-analysis/09-skolem-function-frameworks.md)
- Thomazo, Baget, Mugnier, Rudolph. *A generic querying algorithm for greedy sets of existential rules*. KR 2012 [U].
