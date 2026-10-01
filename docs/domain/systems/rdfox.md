# RDFox

RDFox is a commercial in-memory RDF store and Datalog reasoner, originally from the University of Oxford (Oxford Semantic Technologies, later acquired by Samsung). It offers parallel materialisation, incremental maintenance, equality reasoning with representatives, stratified negation and aggregation. It has no existential rule heads: value invention uses the `SKOLEM` built-in.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Overview: licence, versions, status (as of a date).
- [ ] Language: Datalog over RDF, negation, aggregates, built-ins, `SKOLEM`.
- [ ] Parallel materialisation; incremental maintenance (DRed, FBF, B/F); equality by representative (owl:sameAs).
- [ ] Explanation features [U].
- [ ] Limitations.

## Related pages

- [incremental maintenance](../algorithms/incremental-maintenance.md): FBF, B/F.
- [equality and UNA](../concepts/equality-and-una.md): sameAs handling.
- [value-invention strategies](../concepts/value-invention-strategies.md): SKOLEM.
- [RDF and SPARQL](../adjacent/rdf-and-sparql.md): data model.

## Key references

- Y. Nenov, R. Piro, B. Motik, I. Horrocks, Z. Wu, J. Banerjee. *RDFox: a highly-scalable RDF store*. ISWC 2015.
- B. Motik, Y. Nenov, R. Piro, I. Horrocks. *Maintenance of Datalog materialisations revisited*. Artificial Intelligence 269, 2019. [U]
- RDFox documentation, *Reasoning* (`SKOLEM` built-in). https://docs.oxfordsemantic.tech/reasoning.html
- B. Motik, Y. Nenov, R. Piro, I. Horrocks. *Handling owl:sameAs via rewriting*. AAAI 2015.
