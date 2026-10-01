# Blocking and finite representations

When the chase does not terminate, the infinite universal model may still have a finite representation: an individual whose 'type' repeats that of an earlier one need not be expanded, provided queries are answered on a sufficiently unfolded structure. Blocking comes from description-logic tableaux and underlies algorithms for greedy bounded-treewidth sets and isomorphism-based termination control.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Blocking in DL tableaux: subset, equality, pairwise blocking.
- [ ] Types of invented individuals; what must be in the type (positive atoms, negated atoms, built-ins).
- [ ] Finite representations of infinite chase results for GBTS; querying by unfolding up to the query size.
- [ ] Isomorphism-based pruning in warded rule sets.
- [ ] Relation with FDNC and finitary programs (logic programming with infinite models).
- [ ] Simple cases: linear chains of invented individuals.
- [ ] Example: managers are employees.

## Related pages

- [chase termination](../concepts/chase-termination.md): when finite representations are needed.
- [decidability classes](../concepts/decidability-classes.md): BTS, GBTS, FDNC.
- [chase variants](chase-variants.md): termination control.
- [description logics and OWL](../adjacent/description-logics-and-owl.md): tableau blocking.
- [explanations and diagnostics](../concepts/explanations-and-diagnostics.md): reporting infinite structures.

## Key references

- M. Thomazo, J.-F. Baget, M.-L. Mugnier, S. Rudolph. *A generic querying algorithm for greedy sets of existential rules*. KR 2012.
- M. Thomazo. *Conjunctive query answering under existential rules: decidability, complexity and algorithms*. PhD thesis, Université Montpellier 2, 2013. [U]
- I. Horrocks, U. Sattler. *A description logic with transitive and inverse roles and role hierarchies*. JLC 9(3), 1999. [U]
- L. Bellomarini, E. Sallinger, G. Gottlob. *The Vadalog system: Datalog-based reasoning for knowledge graphs*. PVLDB 11(9), 2018.
- T. Eiter, M. Šimkus. *FDNC: Decidable nonmonotonic disjunctive logic programs with function symbols*. ACM TOCL 11(2), 2010. https://doi.org/10.1145/1656242.1656249
