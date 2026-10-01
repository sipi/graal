# Piece-unifiers

Piece-unifiers are the unification notion for existential rules. A part of a query (a 'piece') can be unified with a rule head only if every query variable mapped to an existential variable has all its atoms in the piece. They underpin sound and complete query rewriting and the definition of rule dependencies. Without existential variables they reduce to classical unification.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Definitions: pieces, separating variables, piece-unifiers, most general piece-unifiers.
- [ ] Correctness of one rewriting step; single-piece vs aggregated unifiers.
- [ ] Use for rule dependencies (an edge exists iff a piece-unifier exists); complexity.
- [ ] Reduction to classical unification for Datalog and for rules with function terms (occurs check).
- [ ] Example: teaching ontology.

## Related pages

- [query rewriting](query-rewriting.md): rewriting with piece-unifiers.
- [graph of rule dependencies](graph-of-rule-dependencies-grd.md): dependency edges.
- [existential rules](../concepts/existential-rules.md): the language.
- [foundations](../concepts/foundations.md): unifiers.

## Key references

- M. König, M. Leclère, M.-L. Mugnier, M. Thomazo. *Sound, complete and minimal UCQ-rewriting for existential rules*. Semantic Web 6(5), 2015.
- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- M. König, M. Leclère, M.-L. Mugnier, M. Thomazo. *A sound and complete backward chaining algorithm for existential rules*. RR 2012.
