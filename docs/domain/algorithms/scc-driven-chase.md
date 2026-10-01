# SCC-driven chase

Saturating a rule set component by component, in the topological order of the strongly connected components of a dependency graph, so that each component is run to fixpoint once its inputs are complete. Non-recursive components are evaluated once. The technique reduces redundant work, provides natural units for strata and guarantees, and lets different algorithms be used per component.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Scheduling: topological order of SCCs of the GRD or of the predicate dependency graph; non-recursive vs recursive components.
- [ ] Interaction with strata (negation, aggregation).
- [ ] Per-component algorithm choice (materialise, rewrite, skip).
- [ ] Parallelism across independent components.
- [ ] Completeness and termination per component.
- [ ] Example: chain of command; managers are employees.

## Related pages

- [graph of rule dependencies](graph-of-rule-dependencies-grd.md): the dependency graph.
- [semi-naive evaluation](semi-naive-evaluation.md): evaluation inside a component.
- [chase variants](chase-variants.md): which chase inside a component.
- [hybrid strategies](hybrid-strategies.md): mixing algorithms per component.

## Key references

- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- M. Leclère, M.-L. Mugnier, S. Rocher. *Kiabora: an analyzer of existential rule bases*. RR 2013.
- J.-F. Baget, M. Leclère, M.-L. Mugnier, S. Rocher, C. Sipieter. *Graal: a toolkit for query answering with existential rules*. RuleML 2015.
