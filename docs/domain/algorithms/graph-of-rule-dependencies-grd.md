# Graph of rule dependencies (GRD)

The graph of rule dependencies has one node per rule and an edge from `ρ1` to `ρ2` when applying `ρ1` may produce a new trigger for `ρ2`. Its strongly connected components drive chase scheduling, class combination and algorithm selection. It is finer than the predicate dependency graph used for stratification, and refined dependency notions (reliances, restraints) remove further edges.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Definition via piece-unifiers (existential rules) or unification (Datalog, function terms).
- [ ] GRD vs predicate dependency graph; positive and negative dependencies.
- [ ] SCC decomposition and topological order; acyclic GRD (aGRD) as a decidable class.
- [ ] Refined dependencies: positive reliances and restraints; their cost and scalability.
- [ ] Combination of classes along the SCC DAG.
- [ ] Transformations that shrink SCCs (research topic).

## Related pages

- [piece-unifiers](piece-unifiers.md): edge test.
- [SCC-driven chase](scc-driven-chase.md): scheduling.
- [decidability classes](../concepts/decidability-classes.md): class combination.
- [rule-set analysis tools](rule-set-analysis-tools.md): tools computing the GRD.

## Key references

- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- M. Leclère, M.-L. Mugnier, S. Rocher. *Kiabora: an analyzer of existential rule bases*. RR 2013.
- L. González, A. Ivliev, M. Krötzsch, S. Mennicke. *Efficient dependency analysis for rule-based ontologies*. ISWC 2022. https://arxiv.org/abs/2207.09669
- M. Krötzsch. *Computing cores for existential rules with the standard chase and ASP*. KR 2020.
- J.-F. Baget, F. Garreau, M.-L. Mugnier, S. Rocher. *Extending acyclicity notions for existential rules*. ECAI 2014.
