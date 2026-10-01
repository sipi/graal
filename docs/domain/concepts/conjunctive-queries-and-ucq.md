# Conjunctive queries and unions (CQ, UCQ)

Conjunctive queries are existentially quantified conjunctions of atoms (select-project-join queries); unions of CQs are what query rewriting produces. They are the standard query language of rule-based reasoning because their answers are preserved under homomorphisms. This page covers CQs, UCQs, extensions with negation, comparisons and aggregates, and containment and minimisation.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Definitions: CQ, Boolean CQ, answer variables, UCQ; CQs with inequalities, negation (CQ¬), aggregates.
- [ ] Evaluation by homomorphism; certain answers vs answers on a designated model; answers containing nulls or function terms.
- [ ] Containment and equivalence (Chandra-Merlin homomorphism theorem); UCQ containment (Sagiv-Yannakakis); minimisation via cores.
- [ ] Complexity: NP-complete combined, AC0 data; tractable classes (acyclic, bounded treewidth or hypertree width).
- [ ] Queries known in advance vs ad hoc queries (relevance for rewriting).
- [ ] Examples: chain of command, teaching ontology.

## Related pages

- [foundations](foundations.md): homomorphisms, certain answers.
- [homomorphism search](../algorithms/homomorphism-search.md): evaluation.
- [query rewriting](../algorithms/query-rewriting.md): UCQ rewritings.
- [equivalence notions](equivalence-notions.md): containment of rule sets.

## Key references

- A. K. Chandra, P. M. Merlin. *Optimal implementation of conjunctive queries in relational data bases*. STOC 1977.
- Y. Sagiv, M. Yannakakis. *Equivalences among relational expressions with the union and difference operators*. JACM 27(4), 1980.
- M. Yannakakis. *Algorithms for acyclic database schemes*. VLDB 1981.
- G. Gottlob, N. Leone, F. Scarcello. *Hypertree decompositions and tractable queries*. JCSS 64(3), 2002.
- S. Abiteboul, R. Hull, V. Vianu. *Foundations of Databases*, chapters 4-6 (conjunctive queries), 12-15 (Datalog, negation). Addison-Wesley, 1995.
