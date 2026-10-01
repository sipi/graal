# RDF and SPARQL

RDF is the W3C graph data model of subject-predicate-object triples over IRIs, literals and blank nodes; SPARQL is its query language. Rule-based reasoning over RDF appears as RDFS and OWL RL entailment, SPARQL entailment regimes, and rule languages (SWRL, RIF, engine-specific rule syntaxes). Blank nodes are the RDF counterpart of labelled nulls, and XSD datatypes fix literal values.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (adjacent-page variant: keep it short), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] RDF data model; IRIs, literals and XSD datatypes, blank nodes; RDF graphs as instances over a ternary predicate.
- [ ] RDF and RDFS semantics; entailment regimes for SPARQL.
- [ ] SPARQL basics: basic graph patterns as CQs, OPTIONAL, FILTER NOT EXISTS (negation), aggregates.
- [ ] Rule languages over RDF: SWRL, RIF, N3 rules, engine-specific syntaxes; SHACL rules and constraints.
- [ ] Blank nodes vs labelled nulls; Skolemisation of blank nodes.
- [ ] Reasoners and stores with rule support (link systems pages).

## Related pages

- [labelled nulls](../concepts/labelled-nulls.md): blank nodes.
- [description logics and OWL](description-logics-and-owl.md): OWL.
- [Jena rules](../systems/jena-rules.md): an RDF rule engine.
- [RDFox](../systems/rdfox.md): an RDF Datalog reasoner.
- [exact decimals and rounding](../concepts/exact-decimals-and-rounding.md): XSD numeric types.

## Key references

- W3C. *RDF 1.1 Concepts and Abstract Syntax*; *RDF 1.1 Semantics*. W3C Recommendations, 2014.
- W3C. *SPARQL 1.1 Query Language*; *SPARQL 1.1 Entailment Regimes*. W3C Recommendations, 2013.
- I. Horrocks et al. *SWRL: a semantic web rule language combining OWL and RuleML*. W3C Member Submission, 2004.
- W3C. *RIF Core Dialect*. W3C Recommendation, 2010 (second edition 2013).
- W3C. *Shapes Constraint Language (SHACL)*. W3C Recommendation, 2017.
- A. Hogan. *Skolemising blank nodes while preserving isomorphism*. WWW 2015.
