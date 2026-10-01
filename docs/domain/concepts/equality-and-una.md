# Equality and the unique-name assumption

Whether two names can denote the same individual is a semantic choice. The unique-name assumption (UNA) says distinct constants denote distinct individuals; the Herbrand reading extends it to function terms. Equality-generating dependencies (EGDs), functional dependencies and keys either act as constraints (violations are errors) or as rules that merge terms, with very different computational consequences.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] UNA for constants; Herbrand UNA for function terms; first-order reading without UNA.
- [ ] EGDs and functional dependencies: as integrity constraints vs as equality rules; undecidability of FDs with inclusion dependencies; separable and non-conflicting keys.
- [ ] Equality reasoning techniques: union-find with representatives, rewriting, singularisation, e-graphs.
- [ ] Co-reference of invented individuals: two Skolem terms, or a term and a constant, denoting the same individual; impact on counting and aggregates.
- [ ] Negative constraints (`B → ⊥`) and inconsistency; inconsistency-tolerant semantics (pointers).
- [ ] owl:sameAs and equality in RDF/OWL systems.
- [ ] Example: managers are employees (intent requires an invented individual to equal a constant).

## Related pages

- [foundations](foundations.md): UNA, Herbrand reading.
- [Skolem functions and terms](skolem-functions-and-terms.md): identity of function terms.
- [value-invention strategies](value-invention-strategies.md): keys and constructor predicates.
- [RDFox](../systems/rdfox.md): equality by representative.
- [others (egglog)](../systems/others.md): e-graphs.

## Key references

- J. C. Mitchell. *The implication problem for functional and inclusion dependencies*. Information and Control 56(3), 1983; A. K. Chandra, M. Y. Vardi. *The implication problem for functional and inclusion dependencies is undecidable*. SIAM J. Comput. 14(3), 1985.
- A. Calì, G. Gottlob, A. Pieris. *Towards more expressive ontology languages: the query answering problem*. Artificial Intelligence 193, 2012.
- L. Bellomarini, D. Benedetto, M. Brandetti, E. Sallinger. *Exploiting the power of equality-generating dependencies in ontological reasoning*. PVLDB 15(13), 2022.
- B. Motik, Y. Nenov, R. Piro, I. Horrocks. *Handling owl:sameAs via rewriting*. AAAI 2015.
- M. Willsey, C. Nandi, Y. R. Wang, O. Flatt, Z. Tatlock, P. Panchekha. *egg: fast and extensible equality saturation*. POPL 2021.
- M. Bienvenu, C. Bourgaux. *Inconsistency-tolerant querying of description logic knowledge bases*. Reasoning Web 2016. [U]
