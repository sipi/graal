# Datalog

Function-free Horn rules over a finite database: every head variable occurs in the body, so no new value is ever invented and evaluation always terminates. Datalog is the v0 fragment of the engine and the base layer of F2. This page covers its syntax, least-model semantics, complexity and the extensions (negation, aggregation, functions) that lead to F2.

> **Status in this project:** `v0` `F2` — v0 is plain positive Datalog ([D6](../project/decisions.md#d6)); F2 = stratified Datalog with named functions ([D1](../project/decisions.md#d1)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Syntax: safe rules, facts, EDB/IDB (and the fact that F2 has no strict EDB/IDB split, report 11 §1.4).
- [ ] Semantics: least Herbrand model, immediate-consequence operator `T_P`, fixpoint characterisation; equality with certain answers for positive programs.
- [ ] Complexity: data PTIME-complete, combined EXPTIME-complete (report 07 §1.3 table).
- [ ] Evaluation: naive vs semi-naive (link), magic sets (link); recursion and SCCs.
- [ ] Expressiveness limits: no value invention; why business examples (E3-ex) need more.
- [ ] How the analyser recognises the Datalog fragment and dispatches it (D6 "v0 is not throwaway").
- [ ] Running example: v0-ex chain of command.
- [ ] Pitfalls: unsafe rules, constants in heads, duplicate elimination, set vs bag semantics.

## Related pages

- [foundations](../concepts/foundations.md)
- [perfect model semantics](../concepts/perfect-model-semantics.md)
- [stratified negation](../concepts/stratified-negation.md)
- [semi naive evaluation](../algorithms/semi-naive-evaluation.md)
- [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md)
- [souffle](../systems/souffle.md)

## Key references

- [report 07 §1.3](../../preliminary-analysis/07-sota-theory.md)
- [report 11 §1-§4](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 09 §6 (F2)](../../preliminary-analysis/09-skolem-function-frameworks.md)
- Dantsin, Eiter, Gottlob, Voronkov. *Complexity and expressive power of logic programming*. ACM CSUR 2001 [U].
- Abiteboul, Hull, Vianu. *Foundations of Databases*, 1995, part D [U].
- Ceri, Gottlob, Tanca. *What you always wanted to know about Datalog (and never dared to ask)*. IEEE TKDE 1989 [U].
