# Query rewriting

Query rewriting answers a query by reformulating it with the rules into a query (a UCQ, or a Datalog program) that can be evaluated directly on the data, without materialising consequences. UCQ rewriting with piece-unifiers is sound and complete for existential rules and terminates exactly on finite unification sets. For queries known in advance, the rewriting can be computed once and reused until the rules change.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Rewriting operator, breadth-first exploration, subsumption pruning, minimality (PURE-style algorithms).
- [ ] Termination: FUS rule sets; a finite rewriting proves completeness for that query.
- [ ] Size blow-up of UCQ rewritings; alternatives: Datalog rewritings, tree-witness rewritings, rewritings for guarded rules (saturation to Datalog).
- [ ] Rewriting with function terms (SLD-style unfolding) and with default negation (per stratum, with lower strata materialised).
- [ ] Rewriting queries known in advance: compile once, store, invalidate when rules change (data changes do not invalidate); fingerprinting the relevant part of the rule set.
- [ ] OBDA: rewriting into SQL (link ontology-based data access).
- [ ] Example: teaching ontology.

## Related pages

- [piece-unifiers](piece-unifiers.md): rewriting steps.
- [conjunctive queries and UCQ](../concepts/conjunctive-queries-and-ucq.md): the query language.
- [backward chaining and tabling](backward-chaining-and-tabling.md): query-time alternative.
- [hybrid strategies](hybrid-strategies.md): rewriting above materialisation.
- [ontology-based data access](../adjacent/ontology-based-data-access.md): rewriting to SQL.

## Key references

- M. König, M. Leclère, M.-L. Mugnier, M. Thomazo. *Sound, complete and minimal UCQ-rewriting for existential rules*. Semantic Web 6(5), 2015.
- D. Calvanese, G. De Giacomo, D. Lembo, M. Lenzerini, R. Rosati. *Tractable reasoning and efficient query answering in description logics: the DL-Lite family*. JAR 39(3), 2007.
- G. Gottlob, T. Schwentick. *Rewriting ontological queries into small nonrecursive datalog programs*. KR 2012.
- M. Benedikt, M. Buron, S. Germano, K. Kappelmann, B. Motik. *Rewriting the infinite chase*. PVLDB 15(11), 2022.
- S. Kikot, R. Kontchakov, M. Zakharyaschev. *Conjunctive query answering with OWL 2 QL*. KR 2012.
- A. Calì, G. Gottlob, A. Pieris. *Query answering under non-guarded rules in Datalog+/-*. RR 2010.
