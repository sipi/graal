# VLog and Rulewerk

VLog is a C++ column-oriented engine for Datalog and existential rules from TU Dresden and VU Amsterdam; Rulewerk is its Java API and toolkit. They brought the Datalog-first restricted chase and dependency-based scheduling into practice and were evaluated on large real-world ontologies. They are succeeded by Nemo.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Overview: licences, maintenance status (as of a date).
- [ ] Columnar storage, Datalog-first restricted chase, restraints and reliances.
- [ ] Benchmarks used in the VLog papers.
- [ ] Rulewerk API and formats.

## Related pages

- [Nemo](nemo.md): successor.
- [chase variants](../algorithms/chase-variants.md): Datalog-first chase.
- [graph of rule dependencies](../algorithms/graph-of-rule-dependencies-grd.md): reliances.

## Key references

- J. Urbani, M. Krötzsch, C. Jacobs, I. Dragoste, D. Carral. *Efficient model construction for Horn logic with VLog*. IJCAR 2018.
- D. Carral, I. Dragoste, L. González, C. Jacobs, M. Krötzsch, J. Urbani. *VLog: a rule engine for knowledge graphs*. ISWC 2019.
