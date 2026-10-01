# Other systems

A short tour of other systems with transferable ideas: egglog (equality saturation with e-graphs and Datalog), Scallop (provenance semirings, neurosymbolic programming), ascent and other embedded Datalog libraries, DDlog (differential Datalog), ELK (consequence-based EL reasoning), Ontop (ontology-based data access), LogicBlox (constructor predicates), and data-exchange chase engines (Llunatic, ChaseFUN, PDQ).

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] One short section per system: what it is, licence, status (as of a date), the notable idea, limits.
- [ ] egglog: union-find with rebuilding, equality saturation.
- [ ] Scallop: provenance as a semiring parameter of evaluation.
- [ ] ascent, datafrog, crepe: embedded Datalog in Rust; lattices.
- [ ] DDlog: differential dataflow; archived status.
- [ ] ELK: consequence-based reasoning, goal-directed tracing.
- [ ] Ontop: T-mappings, tree-witness rewriting, SQL generation.
- [ ] LogicBlox: LogiQL, constructor predicates.
- [ ] Llunatic, ChaseFUN, PDQ: chase in relational databases, EGD handling, proof-based query planning.

## Related pages

- [equality and UNA](../concepts/equality-and-una.md): e-graphs.
- [provenance](../concepts/provenance.md): semirings.
- [ontology-based data access](../adjacent/ontology-based-data-access.md): Ontop.
- [value-invention strategies](../concepts/value-invention-strategies.md): constructors.

## Key references

- Y. Zhang, Y. R. Wang, O. Flatt, D. Cao, P. Zucker, E. Rosenthal, Z. Tatlock, M. Willsey. *Better together: unifying Datalog and equality saturation*. PLDI 2023.
- Z. Li, J. Huang, M. Naik. *Scallop: a language for neurosymbolic programming*. PLDI 2023. [U]
- Y. Kazakov, M. Krötzsch, F. Simančík. *The incredible ELK*. JAR 53(1), 2014.
- D. Calvanese, B. Cogrel, S. Komla-Ebri, R. Kontchakov, D. Lanti, M. Rezk, M. Rodriguez-Muro, G. Xiao. *Ontop: answering SPARQL queries over relational databases*. Semantic Web 8(3), 2017.
- M. Aref, B. ten Cate, T. J. Green, B. Kimelfeld, D. Olteanu, E. Pasalic, T. L. Veldhuizen, G. Washburn. *Design and implementation of the LogicBlox system*. SIGMOD 2015.
- F. Geerts, G. Mecca, P. Papotti, D. Santoro. *That's all folks! LLUNATIC goes open source*. PVLDB 7(13), 2014. [U]
- M. Benedikt, J. Leblay, E. Tsamoura. *PDQ: proof-driven query answering over web-based data*. PVLDB 7(13), 2014. [U]
