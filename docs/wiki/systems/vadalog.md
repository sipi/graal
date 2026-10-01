# Vadalog

Vadalog (Oxford/TU Wien, now Prometheux; commercial) implements Warded Datalog+/- (Arenas, Gottlob, Pieris 2014; Gottlob, Pieris 2015) with isomorphism-based termination control, deterministic injective Skolem functions, and monotonic aggregation. Not used by RDFox.

> **Status in this project:** `background` — inspiration (termination control, warded class, Skolem functions); related to Q1 lead B.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] Warded Datalog+/-, termination control via warded forest / isomorphism pruning.
- [ ] Skolem function semantics (injective, range-disjoint).
- [ ] Monotonic aggregation in recursion.
- [ ] Relevance to Q1 lead B blocking.
- [ ] iWarded benchmark generator.

## Related pages

- [decidability classes](../concepts/decidability-classes.md)
- [blocking of recursive chains](../algorithms/blocking-of-recursive-chains.md)
- [aggregation](../concepts/aggregation.md)
- [skolem functions and terms](../concepts/skolem-functions-and-terms.md)

## Key references

- [report 06 §0.2, §1.2](../../preliminary-analysis/06-sota-engines.md)
- [README Clarifications](../../preliminary-analysis/README.md)
- Bellomarini, Sallinger, Gottlob. *The Vadalog system*. PVLDB 2018.
