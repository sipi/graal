# Ontology-based data access

Ontology-based data access (OBDA), also called virtual knowledge graphs, lets users query data sources through an ontology and mappings: queries over the ontology vocabulary are rewritten with the ontology (typically DL-Lite / OWL 2 QL or existential rules) and unfolded through the mappings into SQL over the sources, without materialising the data.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (adjacent-page variant: keep it short), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Architecture: ontology, mappings (R2RML), sources; virtual vs materialised approaches.
- [ ] First-order rewritability (FUS, DL-Lite) as the enabling property.
- [ ] Rewriting and unfolding; T-mappings; tree-witness rewriting; query optimisation.
- [ ] Data integration and data exchange as related settings (source-to-target dependencies).
- [ ] Systems: Ontop, Mastro; existential-rule engines for integration.

## Related pages

- [query rewriting](../algorithms/query-rewriting.md): the core technique.
- [description logics and OWL](description-logics-and-owl.md): DL-Lite, OWL 2 QL.
- [existential rules](../concepts/existential-rules.md): rule-based OBDA.
- [others (Ontop)](../systems/others.md): an OBDA system.

## Key references

- A. Poggi, D. Lembo, D. Calvanese, G. De Giacomo, M. Lenzerini, R. Rosati. *Linking data to ontologies*. Journal on Data Semantics X, 2008.
- G. Xiao, D. Calvanese, R. Kontchakov, D. Lembo, A. Poggi, R. Rosati, M. Zakharyaschev. *Ontology-based data access: a survey*. IJCAI 2018.
- D. Calvanese, B. Cogrel, S. Komla-Ebri, R. Kontchakov, D. Lanti, M. Rezk, M. Rodriguez-Muro, G. Xiao. *Ontop: answering SPARQL queries over relational databases*. Semantic Web 8(3), 2017.
- D. Calvanese, G. De Giacomo, D. Lembo, M. Lenzerini, R. Rosati. *Tractable reasoning and efficient query answering in description logics: the DL-Lite family*. JAR 39(3), 2007.
- W3C. *R2RML: RDB to RDF Mapping Language*. W3C Recommendation, 2012.
- M. Lenzerini. *Data integration: a theoretical perspective*. PODS 2002.
