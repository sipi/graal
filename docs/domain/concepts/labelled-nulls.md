# Labelled nulls

A labelled null is a term occurring in an instance that stands for an unknown individual, typically created by the chase for an existential variable. Nulls behave like constants during evaluation but may be renamed or mapped to other terms by homomorphisms, and answers containing them are not certain answers. Representing them consistently (identity, serialisation, round-trip across stores) is a recurring engineering difficulty.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Definition: nulls vs constants vs variables; terminology (labelled null, marked null, blank node, anonymous individual); an existential variable is not a null.
- [ ] Homomorphisms that move nulls; isomorphism up to null renaming; cores.
- [ ] Certain answers exclude nulls; Boolean queries can still be entailed through nulls.
- [ ] Nulls in input data: incomplete databases (Codd tables, naive tables), RDF blank nodes.
- [ ] Negation and aggregation over nulls: why closed-world operators on null positions are delicate.
- [ ] Relation with Skolem terms: a null is a Skolem term whose structure is forgotten.
- [ ] Representation: identity, fresh-name generation, serialisation, collisions with constants, consistency across storage back-ends.
- [ ] Example: teaching ontology (`n1`).

## Related pages

- [existential rules](existential-rules.md): where nulls come from.
- [chase variants](../algorithms/chase-variants.md): how nulls are created.
- [Skolem functions and terms](skolem-functions-and-terms.md): the named alternative.
- [RDF and SPARQL](../adjacent/rdf-and-sparql.md): blank nodes.
- [foundations](foundations.md): certain answers.

## Key references

- R. Fagin, P. G. Kolaitis, R. J. Miller, L. Popa. *Data exchange: semantics and query answering*. Theoretical Computer Science 336(1), 2005.
- T. Imieliński, W. Lipski. *Incomplete information in relational databases*. JACM 31(4), 1984.
- E. F. Codd. *Extending the database relational model to capture more meaning*. ACM TODS 4(4), 1979.
- A. Deutsch, A. Nash, J. Remmel. *The chase revisited*. PODS 2008. https://doi.org/10.1145/1376916.1376938
- A. Hogan. *Skolemising blank nodes while preserving isomorphism*. WWW 2015.
