# Logic programming and answer set programming

Logic programming reads rules with function symbols and default negation under a designated model (least, perfect, stable or well-founded), with Herbrand interpretations. Answer set programming (ASP) uses stable-model semantics, disjunction, constraints, choice rules and aggregates for declarative problem solving, implemented by grounders and solvers. This page connects these semantics with the first-order reading of rules used elsewhere.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Normal, extended (classical negation) and disjunctive programs; Herbrand universe and base.
- [ ] Semantics: least model (definite programs), perfect model (stratified), stable models (Gelfond-Lifschitz reduct), well-founded model; when they coincide.
- [ ] Classical vs default negation; constraints; choice rules; weak constraints (overview).
- [ ] Function symbols: undecidability, finitely ground programs, decidable classes (FDNC, finitary, argument-restricted; link decidability classes).
- [ ] Grounding and solving: ground-and-solve architecture, intelligent grounding, CDCL-based solvers.
- [ ] Aggregates in ASP (ASP-Core-2; FLP and other semantics for recursive aggregates).
- [ ] Relation with existential rules: Skolemisation, DLV∃ and the parsimonious chase.
- [ ] Brave vs cautious reasoning; relation with certain answers.
- [ ] Examples: default conditions; managers are employees (non-termination of grounding).

## Related pages

- [stratified negation](stratified-negation.md): the stratified case.
- [perfect-model semantics](perfect-model-semantics.md): stratified semantics.
- [aggregation](aggregation.md): aggregates.
- [decidability classes](decidability-classes.md): LP classes with functions.
- [clingo and DLV](../systems/clingo-and-dlv.md): ASP systems.
- [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md): Prolog, SLD, SLG.

## Key references

- J. W. Lloyd. *Foundations of Logic Programming*, 2nd ed. Springer, 1987.
- M. Gelfond, V. Lifschitz. *The stable model semantics for logic programming*. ICLP/SLP 1988.
- A. Van Gelder, K. A. Ross, J. S. Schlipf. *The well-founded semantics for general logic programs*. JACM 38(3), 1991.
- M. Gelfond, V. Lifschitz. *Classical negation in logic programs and disjunctive databases*. New Generation Computing 9, 1991.
- G. Brewka, T. Eiter, M. Truszczyński. *Answer set programming at a glance*. CACM 54(12), 2011.
- M. Gebser, R. Kaminski, B. Kaufmann, T. Schaub. *Answer Set Solving in Practice*. Morgan & Claypool, 2012.
- F. Calimeri et al. *ASP-Core-2 input language format*. TPLP 20(2), 2020.
- F. Calimeri, S. Cozza, G. Ianni, N. Leone. *Computable functions in ASP: theory and implementation*. ICLP 2008.
- T. Eiter, M. Šimkus. *FDNC: Decidable nonmonotonic disjunctive logic programs with function symbols*. ACM TOCL 11(2), 2010. https://doi.org/10.1145/1656242.1656249
