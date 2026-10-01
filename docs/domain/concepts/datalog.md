# Datalog

Datalog is the language of function-free Horn rules in which every head variable occurs in the body, so no new value is ever invented and evaluation always terminates on finite data. It is the common core of deductive databases, of most rule engines, and of the extensions (negation, aggregation, functions, existential variables) covered by other pages. This page covers its syntax, least-model semantics, complexity and expressive power.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Syntax: facts, rules, safety (range restriction), EDB/IDB predicates; constants in heads.
- [ ] Semantics: model-theoretic (least Herbrand model), fixpoint (`T_P`), proof-theoretic; equivalence of the three; certain answers coincide with answers on the least model.
- [ ] Complexity: data complexity PTIME-complete, combined complexity EXPTIME-complete; linear, monadic and non-recursive fragments.
- [ ] Expressive power: captures PTIME on ordered structures with stratified negation; what positive Datalog cannot express (non-monotone queries, value invention).
- [ ] Evaluation overview: naive and semi-naive bottom-up, top-down with tabling, magic sets (link algorithm pages).
- [ ] Extensions and their pages: stratified negation, aggregation, arithmetic built-ins, function symbols, existential rules, disjunction.
- [ ] Example: chain of command (examples.md).
- [ ] Pitfalls: unsafe rules, set vs bag semantics, arithmetic making evaluation non-terminating.

## Related pages

- [foundations](foundations.md): terms, models, entailment.
- [semi-naive evaluation](../algorithms/semi-naive-evaluation.md): bottom-up evaluation.
- [stratified negation](stratified-negation.md): first extension.
- [perfect-model semantics](perfect-model-semantics.md): semantics of stratified Datalog.
- [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md): top-down evaluation, magic sets.
- [Soufflé](../systems/souffle.md): a Datalog compiler.

## Key references

- S. Abiteboul, R. Hull, V. Vianu. *Foundations of Databases*, chapters 4-6 (conjunctive queries), 12-15 (Datalog, negation). Addison-Wesley, 1995.
- E. Dantsin, T. Eiter, G. Gottlob, A. Voronkov. *Complexity and expressive power of logic programming*. ACM Computing Surveys 33(3), 2001.
- S. Ceri, G. Gottlob, L. Tanca. *What you always wanted to know about Datalog (and never dared to ask)*. IEEE TKDE 1(1), 1989.
- M. H. van Emden, R. A. Kowalski. *The semantics of predicate logic as a programming language*. JACM 23(4), 1976.
- T. J. Green, S. S. Huang, B. T. Loo, W. Zhou. *Datalog and recursive query processing*. Foundations and Trends in Databases 5(2), 2013.
