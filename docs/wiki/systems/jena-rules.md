# Apache Jena rule engine

Apache Jena (Java, Apache-2.0) ships a general-purpose rule engine for RDF with forward (RETE), backward (tabled) and hybrid modes, used for RDFS/OWL-lite inference. Report 13 studies it; this page summarises what is relevant to the project.

> **Status in this project:** `background` — to be summarised from report 13 (in preparation by another agent).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] Summarise report 13 (do not duplicate): rule language, forward RETE engine, backward tabled engine, hybrid mode, builtins, negation (`noValue`), Skolem-like constructs (`makeSkolem`, `makeTemp`) [U until report 13 is read].
- [ ] Semantics guarantees and gaps vs E1/E3.
- [ ] Ideas to borrow and pitfalls.
- [ ] Licence and integration (JVM).
- [ ] Note: Graal used Jena 2.13 only for SPARQL parsing (reports 02, 03).

## Related pages

- [rdfox](../systems/rdfox.md)
- [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md)
- [language choice kotlin vs rust](../engineering/language-choice-kotlin-vs-rust.md)

## Key references

- [report 13](../../preliminary-analysis/13-jena-rule-engine.md)
- [report 02 §1](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 03 §1.6](../../preliminary-analysis/03-graal-build-and-dependencies.md)
