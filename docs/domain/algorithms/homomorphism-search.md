# Homomorphism search

Finding homomorphisms from a conjunction of atoms into a set of facts is the inner loop of query answering, rule application, restricted-chase checks and subsumption tests in rewriting. The problem is NP-complete in general but tractable for queries of bounded (hyper)treewidth. Practical algorithms combine backtracking with ordering heuristics, constraint propagation, decompositions and indexes.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Problem variants: enumerate all homomorphisms, existence check, homomorphism between two CQs (containment), with some terms fixed.
- [ ] Complexity: NP-complete (CSP); tractable classes (acyclic, bounded treewidth, bounded hypertree width).
- [ ] Backtracking with variable or atom ordering heuristics, forward checking, conflict-directed backjumping.
- [ ] Decompositions: bi-connected components of the query graph, join trees, hypertree and generalised hypertree decompositions.
- [ ] Indexing for small instances vs large ones; relation with join algorithms.
- [ ] Benchmarks for CQ evaluation and homomorphism checks.

## Related pages

- [foundations](../concepts/foundations.md): homomorphisms.
- [conjunctive queries and UCQ](../concepts/conjunctive-queries-and-ucq.md): queries.
- [worst-case-optimal joins](worst-case-optimal-joins.md): join-based evaluation.
- [Graal](../systems/graal.md): BCC-based homomorphism search.

## Key references

- A. K. Chandra, P. M. Merlin. *Optimal implementation of conjunctive queries in relational data bases*. STOC 1977.
- G. Gottlob, N. Leone, F. Scarcello. *Hypertree decompositions and tractable queries*. JCSS 64(3), 2002.
- R. Dechter. *Constraint Processing*. Morgan Kaufmann, 2003.
- P. Prosser. *Hybrid algorithms for the constraint satisfaction problem*. Computational Intelligence 9(3), 1993.
- J.-F. Baget. *Simple conceptual graphs revisited: hypergraphs and conjunctive types for efficient projection algorithms*. ICCS 2003. [U]
