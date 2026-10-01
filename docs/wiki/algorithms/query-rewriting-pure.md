# Query rewriting (PURE-style UCQ rewriting)

Backward chaining by rewriting: a query is rewritten with the rules into a union of conjunctive queries that can be evaluated directly on the data, without materialisation. PURE computes a sound, complete and minimal UCQ rewriting with piece-unifiers; it terminates on FUS rule sets. It is a must-have (E4).

> **Status in this project:** `F2` `open` (v0 inclusion open) — [E4](../project/requirements.md#e4), [E10](../project/requirements.md#e10); in F2, SLD unfolding on lb(K) (report 11 §8.3); under negation [D5](../project/decisions.md#d5).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Rewriting operator, breadth-first exploration, subsumption pruning, minimality.
- [ ] Termination: FUS, and bounded rewriting as a static completeness proof.
- [ ] UCQ blow-up; alternatives: Datalog rewritings, tree-witness, compiled rewritings (Ontop, Graal ID compilation).
- [ ] F2 adaptation: SLD unfolding with function terms, pruning rules (report 11 §8.3).
- [ ] Graal implementation issues (report 02: no bound, FIXME returning null).
- [ ] Example: rewriting a query over E3-ex (existential reading) — finite despite infinite chase.

## Related pages

- [piece unifiers](../algorithms/piece-unifiers.md)
- [precomputed rewriting](../algorithms/precomputed-rewriting.md)
- [hybrid strategies](../algorithms/hybrid-strategies.md)
- [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md)
- [conjunctive queries and ucq](../concepts/conjunctive-queries-and-ucq.md)

## Key references

- [report 07 §2](../../preliminary-analysis/07-sota-theory.md)
- [report 02 §3](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 11 §8.3](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 06 §5 idea 9](../../preliminary-analysis/06-sota-engines.md)
- König, Leclère, Mugnier, Thomazo. SWJ 2015.
