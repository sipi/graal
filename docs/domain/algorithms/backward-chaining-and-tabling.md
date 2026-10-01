# Backward chaining and tabling

Backward chaining evaluates a query goal-directedly, from the query towards the data. SLD resolution (Prolog) is incomplete on left-recursive programs; tabling (SLG resolution) memoises subgoals and answers and detects completion, making evaluation terminate on Datalog and complete on many programs with function symbols. Magic sets achieve the same goal-direction inside bottom-up evaluation. This page also covers Prolog systems with tabling.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] SLD resolution and its incompleteness; Prolog's depth-first strategy, cut and other non-declarative features.
- [ ] Tabling (SLG): subgoal tables, completion, stratified and well-founded negation (delaying).
- [ ] Magic sets and sideways information passing; magic sets for existential rules.
- [ ] Completeness and termination: finitely recursive queries, finitary programs; goal-directed completion when the model is infinite.
- [ ] Prolog systems with tabling: XSB, SWI-Prolog, others; tabling modes (answer subsumption).
- [ ] Comparison with query rewriting and with materialisation.
- [ ] Example: chain of command (left recursion).

## Related pages

- [query rewriting](query-rewriting.md): a-priori rewriting.
- [Datalog](../concepts/datalog.md): the language.
- [logic programming and ASP](../concepts/logic-programming-and-asp.md): semantics of negation.
- [hybrid strategies](hybrid-strategies.md): combining with materialisation.
- [soundness and completeness of partial results](../concepts/soundness-and-completeness-of-partial-results.md): goal-directed completion.

## Key references

- W. Chen, D. S. Warren. *Tabled evaluation with delaying for general logic programs*. JACM 43(1), 1996.
- H. Tamaki, T. Sato. *OLD resolution with tabulation*. ICLP 1986.
- F. Bancilhon, D. Maier, Y. Sagiv, J. D. Ullman. *Magic sets and other strange ways to implement logic programs*. PODS 1986.
- M. Alviano, N. Leone, M. Manna, G. Terracina, P. Veltri. *Magic-sets for Datalog with existential quantifiers*. Datalog 2.0, LNCS 7494, 2012.
- T. Swift, D. S. Warren. *XSB: extending Prolog with tabled logic programming*. TPLP 12(1-2), 2012.
- J. Wielemaker, T. Schrijvers, M. Triska, T. Lager. *SWI-Prolog*. TPLP 12(1-2), 2012.
- P. A. Bonatti. *Reasoning with infinite stable models*. Artificial Intelligence 156(1), 2004.
