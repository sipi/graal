# Soufflé

Soufflé is a Datalog compiler, originally from Oracle Labs and the University of Sydney, designed for static program analysis. It compiles Datalog to parallel C++ with automatic index selection and specialised data structures, and offers cheap provenance annotations. It supports stratified negation, aggregates, arithmetic and algebraic data types.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Overview: licence (UPL), status (as of a date).
- [ ] Language: Datalog with components, types, ADTs, negation, aggregates.
- [ ] Staged compilation; index selection (minimum chain cover); data structures (B-trees, eqrel).
- [ ] Provenance by (rule, height) annotations.
- [ ] Value invention via ADTs/records and non-termination with arithmetic [U].

## Related pages

- [Datalog](../concepts/datalog.md): the language.
- [semi-naive evaluation](../algorithms/semi-naive-evaluation.md): evaluation.
- [provenance](../concepts/provenance.md): annotations.

## Key references

- H. Jordan, B. Scholz, P. Subotić. *Soufflé: on synthesis of program analyzers*. CAV 2016.
- D. Zhao, P. Subotić, B. Scholz. *Debugging large-scale Datalog: a scalable provenance evaluation strategy*. ACM TOPLAS 42(2), 2020. [U]
- P. Subotić, H. Jordan, L. Chang, A. Fekete, B. Scholz. *Automatic index selection for large-scale Datalog computation*. PVLDB 12(2), 2018.
- Soufflé documentation. https://souffle-lang.github.io/
