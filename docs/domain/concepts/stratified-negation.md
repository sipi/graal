# Stratified negation

Default negation (`not p(x̄)`, negation as failure) under the closed-world reading, restricted to programs where no recursion goes through negation. It expresses defaults and exceptions. The page covers the predicate dependency graph, stratification checks, safety, and the interaction of negation with value invention and with incomplete computations.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Syntax and safety of negated literals; anonymous variables inside negation.
- [ ] Predicate dependency graph, positive and negative (strict) edges, stratifiability test, canonical stratification by SCCs.
- [ ] Closed world vs open world; negation on base (EDB) predicates vs derived predicates.
- [ ] Negation and value invention: negating atoms about invented individuals; invention that defeats its own condition (self-defeating, cross-defeating).
- [ ] The restricted chase as an implicit negation (a trigger fires only if its head is not satisfied).
- [ ] Negation over an incomplete lower stratum is unsound, not just incomplete.
- [ ] Beyond stratification: local stratification, stable models, well-founded semantics (link logic programming and ASP).
- [ ] Examples: default conditions; every employee has a manager (negation on a data predicate).

## Related pages

- [perfect-model semantics](perfect-model-semantics.md): the semantics.
- [logic programming and ASP](logic-programming-and-asp.md): non-stratified negation.
- [soundness and completeness of partial results](soundness-and-completeness-of-partial-results.md): negation over incomplete results.
- [value-invention strategies](value-invention-strategies.md): invention under negation.
- [aggregation](aggregation.md): the other non-monotone operator.

## Key references

- K. R. Apt, H. A. Blair, A. Walker. *Towards a theory of declarative knowledge*. In J. Minker (ed.), *Foundations of Deductive Databases and Logic Programming*, Morgan Kaufmann, 1988.
- A. Chandra, D. Harel. *Horn clause queries and generalizations*. JLP 2(1), 1985. [U]
- M. Gelfond, V. Lifschitz. *The stable model semantics for logic programming*. ICLP/SLP 1988.
- A. Van Gelder, K. A. Ross, J. S. Schlipf. *The well-founded semantics for general logic programs*. JACM 38(3), 1991.
- K. R. Apt, R. N. Bol. *Logic programming and negation: a survey*. JLP 19-20, 1994.
- D. Magka, M. Krötzsch, I. Horrocks. *Computing stable models for nonmonotonic existential rules*. IJCAI 2013.
- A. Calì, G. Gottlob, T. Lukasiewicz. *A general Datalog-based framework for tractable query answering over ontologies*. Journal of Web Semantics 14, 2012.
