# Decidability classes

The map of decidable fragments for reasoning with value invention: abstract classes defined by semantic properties (FES, FUS, BTS, GBTS), recognisable sufficient conditions (acyclicity notions, guardedness, stickiness, wardedness, linearity, shyness) and logic-programming classes with function symbols (finitely ground, argument-restricted, FDNC, finitary). Membership in the abstract classes is undecidable, so practical tools use portfolios of sufficient tests.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Abstract classes FES, FUS, BTS, GBTS: definitions, inclusions, undecidability of membership.
- [ ] Concrete classes with data and combined complexity: Datalog, linear, guarded, frontier-guarded, weakly (frontier-)guarded, sticky, weakly sticky, sticky-join, warded, piece-wise linear warded, shy, aGRD.
- [ ] Acyclicity notions: WA, SWA, JA, MSA, MFA, RJA/RMFA, cyclicity (non-termination) criteria; inclusions.
- [ ] LP classes with function symbols: finitely ground, argument-restricted, Γ-acyclic, bounded, FDNC, finitary, finitely recursive.
- [ ] Combination along the GRD: FES below FUS, BTS below FUS; what does not combine.
- [ ] Transfer of classes through Skolemisation and function-graph translations.
- [ ] Pitfalls: membership undecidable; normalisation changes classes; data vs combined complexity.

## Related pages

- [chase termination](chase-termination.md): termination notions.
- [graph of rule dependencies](../algorithms/graph-of-rule-dependencies-grd.md): combination along SCCs.
- [rule-set analysis tools](../algorithms/rule-set-analysis-tools.md): recognition in practice.
- [existential rules](existential-rules.md): the language.

## Key references

- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- B. Cuenca Grau, I. Horrocks, M. Krötzsch, C. Kupke, D. Magka, B. Motik, Z. Wang. *Acyclicity notions for existential rules and their application to query answering in ontologies*. JAIR 47, 2013. https://doi.org/10.1613/jair.3964
- M. Krötzsch, S. Rudolph. *Extending decidable existential rules by joining acyclicity and guardedness*. IJCAI 2011.
- A. Calì, G. Gottlob, M. Kifer. *Taming the infinite chase: query answering under expressive relational constraints*. JAIR 48, 2013.
- A. Calì, G. Gottlob, A. Pieris. *Towards more expressive ontology languages: the query answering problem*. Artificial Intelligence 193, 2012.
- M. Arenas, G. Gottlob, A. Pieris. *Expressive languages for querying the semantic web*. PODS 2014.
- N. Leone, M. Manna, G. Terracina, P. Veltri. *Fast query answering over existential rules*. ACM TOCL 20(2), 2019.
- B. Marnette. *Generalized schema-mappings: from termination to tractability*. PODS 2009.
- T. Syrjänen. *Omega-restricted logic programs*. LPNMR 2001.
- M. Alviano, W. Faber, N. Leone. *Disjunctive ASP with functions: decidable queries and effective computation*. TPLP 10(4-6), 2010.
- T. Eiter, M. Šimkus. *FDNC: Decidable nonmonotonic disjunctive logic programs with function symbols*. ACM TOCL 11(2), 2010. https://doi.org/10.1145/1656242.1656249
- Y. Lierler, V. Lifschitz. *One more decidable class of finitely ground programs*. ICLP 2009.
