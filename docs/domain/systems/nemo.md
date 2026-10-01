# Nemo

Nemo is a rule engine written in Rust by the Knowledge-Based Systems group at TU Dresden, successor of VLog. It uses columnar, trie-based storage and leapfrog-style joins, implements the Datalog-first restricted chase for existential rules, stratified negation, aggregates, datatypes and tracing of derivations, and reads common data formats.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Overview: licence, versions, maintenance status (as of a date).
- [ ] Language: rule syntax, existential rules, negation, aggregates, datatypes and built-ins.
- [ ] Chase variant and its exact behaviour on cyclic programs [U].
- [ ] Storage and joins: columnar tries, leapfrog triejoin; performance evidence.
- [ ] Tracing and explanations.
- [ ] Limitations (as documented): no query rewriting, limited incremental updates [U].

## Related pages

- [VLog and Rulewerk](vlog-rulewerk.md): predecessors.
- [chase variants](../algorithms/chase-variants.md): Datalog-first chase.
- [worst-case-optimal joins](../algorithms/worst-case-optimal-joins.md): join algorithm.

## Key references

- A. Ivliev, L. Gerlach, S. Meusel, J. Steinberg, M. Krötzsch. *Nemo: your friendly and versatile rule reasoning toolkit*. KR 2024.
- Nemo source repository. https://github.com/knowsys/nemo [U]
- L. González, A. Ivliev, M. Krötzsch, S. Mennicke. *Efficient dependency analysis for rule-based ontologies*. ISWC 2022. https://arxiv.org/abs/2207.09669
