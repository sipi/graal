# Apache Jena rule engine

Apache Jena (Java, Apache-2.0) includes a general-purpose rule engine for RDF with a forward engine (RETE-based), a backward engine with tabling, and a hybrid mode in which forward rules can generate backward rules. It is used for RDFS and OWL-lite inference and for custom rule sets, with builtins for negation-like tests (`noValue`) and value invention (`makeSkolem`, `makeInstance`, `makeTemp`).

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Overview: licence, versions, status (as of a date).
- [ ] Rule language: triple patterns, builtins, functors.
- [ ] Forward (RETE), backward (tabled) and hybrid engines; their semantics and guarantees.
- [ ] Negation-like builtins (`noValue`, `notEqual`) and their non-monotone behaviour.
- [ ] Value invention builtins and their determinism.
- [ ] Limitations.

## Related pages

- [RDF and SPARQL](../adjacent/rdf-and-sparql.md): data model.
- [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md): backward engine.
- [value-invention strategies](../concepts/value-invention-strategies.md): makeSkolem, makeInstance.

## Key references

- Apache Jena documentation, *Reasoners and rule engines* (builtins `makeSkolem`, `makeInstance`, `makeTemp`, `noValue`). https://jena.apache.org/documentation/inference/
- Apache Jena source repository. https://github.com/apache/jena
- C. L. Forgy. *Rete: a fast algorithm for the many pattern/many object pattern match problem*. Artificial Intelligence 19(1), 1982.
