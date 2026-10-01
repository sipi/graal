# Vadalog

Vadalog is a reasoning system for Warded Datalog± developed at Oxford and TU Wien and commercialised by Prometheux. It controls chase termination through isomorphism-based pruning on warded rule sets, supports Skolem functions, monotonic aggregation in recursion and many built-ins, and targets knowledge-graph applications in finance.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (system-page variant), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Overview: licence, status (as of a date).
- [ ] Warded Datalog±: definition and complexity; termination control.
- [ ] Skolem function semantics (injectivity, disjoint ranges).
- [ ] Monotonic aggregation in recursion.
- [ ] Benchmark generator iWarded.

## Related pages

- [decidability classes](../concepts/decidability-classes.md): warded rules.
- [blocking and finite representations](../algorithms/blocking-and-finite-representations.md): isomorphism-based pruning.
- [aggregation](../concepts/aggregation.md): monotonic aggregation.
- [value-invention strategies](../concepts/value-invention-strategies.md): Skolem functions.

## Key references

- L. Bellomarini, E. Sallinger, G. Gottlob. *The Vadalog system: Datalog-based reasoning for knowledge graphs*. PVLDB 11(9), 2018.
- M. Arenas, G. Gottlob, A. Pieris. *Expressive languages for querying the semantic web*. PODS 2014.
- G. Gottlob, A. Pieris. *Beyond SPARQL under OWL 2 QL entailment regime: rules to the rescue*. IJCAI 2015.
- T. Baldazzi, L. Bellomarini, E. Sallinger, P. Atzeni. *iWarded: a versatile generator to benchmark warded Datalog+/- reasoning*. RuleML+RR 2021. https://arxiv.org/abs/2103.08588
