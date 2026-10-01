# Graph of rule dependencies (GRD)

The GRD has one node per rule and an edge r1 → r2 when applying r1 may trigger a new application of r2. Its strongly connected components drive chase scheduling, the decidability analyser and strategy selection (E4). Report 11 distinguishes it from the coarser predicate dependency graph used for stratification.

> **Status in this project:** `v0` `F2` — [E4](../project/requirements.md#e4), [E2](../project/requirements.md#e2)/[E7](../project/requirements.md#e7); recompute after every transformation (README key theory points); report 11 §3.2, §7.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definition via piece-unifiers (existential rules) or term unification (Datalog, F2).
- [ ] GRD vs predicate dependency graph (stratification is predicate-level in F2, report 11 §3.2).
- [ ] SCC decomposition, topological order, use for scheduling and per-SCC classification.
- [ ] Refined dependencies: positive reliances and restraints (Krötzsch et al.), productivity checks [U].
- [ ] Cost: O(n²) unification tests naively; scaling to large rule sets (ISWC 2022 reliances).
- [ ] Kiabora combination of classes along the SCC DAG.
- [ ] Shrinking SCCs by transformations (research gap, README).
- [ ] Graal implementation issues (report 02: equals/hashCode).

## Related pages

- [piece unifiers](../algorithms/piece-unifiers.md)
- [scc driven chase](../algorithms/scc-driven-chase.md)
- [decidability analyser](../algorithms/decidability-analyser.md)
- [decidability classes](../concepts/decidability-classes.md)
- [stratified negation](../concepts/stratified-negation.md)

## Key references

- [report 07 §1.3](../../preliminary-analysis/07-sota-theory.md)
- [report 02 §3](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 11 §3.2, §7](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 09 §3](../../preliminary-analysis/09-skolem-function-frameworks.md)
- Baget, Leclère, Mugnier, Salvat. AIJ 2011.
- González, Ivliev, Krötzsch, Mennicke. *Efficient dependency analysis for rule-based ontologies*. ISWC 2022.
