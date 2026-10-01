# Graal

Graal is a Java platform for query answering with existential rules, developed by the GraphIK team (Inria and LIRMM, Montpellier) and released under CeCILL 2.1; development stopped around 2019. It implements the DLGP format, several chase variants, piece-unifier query rewriting (PURE), the graph of rule dependencies and rule-base analysis (Kiabora), and homomorphism search exploiting the bi-connected components of queries.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Overview: modules, data model (terms, atoms, stores), licence, maintenance status (as of a date).
- [ ] Language and semantics: DLGP, existential rules, negative constraints; chase variants offered; no default negation in the core [U].
- [ ] Algorithms: homomorphism search with BCC decomposition, chase drivers, SCC-driven chase, PURE rewriting, GRD, Kiabora analyser.
- [ ] Limitations reported in the literature or issue trackers (null handling across stores, Skolem naming, timeouts) [U].
- [ ] Running it today (JDK versions).

## Related pages

- [InteGraal](integraal.md): successor.
- [query rewriting](../algorithms/query-rewriting.md): PURE.
- [homomorphism search](../algorithms/homomorphism-search.md): BCC decomposition.
- [rule-set analysis tools](../algorithms/rule-set-analysis-tools.md): Kiabora.

## Key references

- J.-F. Baget, M. Leclère, M.-L. Mugnier, S. Rocher, C. Sipieter. *Graal: a toolkit for query answering with existential rules*. RuleML 2015.
- M. König, M. Leclère, M.-L. Mugnier, M. Thomazo. *Sound, complete and minimal UCQ-rewriting for existential rules*. Semantic Web 6(5), 2015.
- M. Leclère, M.-L. Mugnier, S. Rocher. *Kiabora: an analyzer of existential rule bases*. RR 2013.
- Graal source repository and documentation. https://graphik-team.github.io/graal/ [U]
