# Soundness and completeness of partial results

What does a reasoner guarantee when it stops before reaching a fixpoint (budget, timeout, non-terminating chase), or when it cannot prove that it reached one? For monotone (positive) rules, every derived fact is sound and only completeness is at risk. With negation or aggregation, a lower layer that is incomplete can make upper-layer results unsound. This page collects the notions used to state such guarantees.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Soundness and completeness of answers w.r.t. a semantics; certain answers 'in the limit' (the chase is complete for CQs).
- [ ] Monotone case: any fair prefix of the chase is sound; completeness by termination, by a static certificate, or by observing a fixpoint.
- [ ] Non-monotone case: negation and aggregates over an incomplete stratum; exposed vs unaffected parts of a program; unit-level vs answer-level guarantees.
- [ ] Typical status taxonomies found in tools and papers (complete by proof, complete by observation, sound but possibly incomplete, unknown) and anytime / approximate reasoning.
- [ ] Budgets (depth, rounds, facts, time) and determinism of budgeted results.
- [ ] Goal-directed completion: a query can be complete even when the model is infinite.
- [ ] Examples: default conditions with an incomplete lower stratum; managers are employees.

## Related pages

- [chase termination](chase-termination.md): when a computation stops.
- [stratified negation](stratified-negation.md): non-monotone layers.
- [aggregation](aggregation.md): partial sums.
- [explanations and diagnostics](explanations-and-diagnostics.md): reporting guarantees.
- [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md): goal-directed completion.

## Key references

- A. Deutsch, A. Nash, J. Remmel. *The chase revisited*. PODS 2008. https://doi.org/10.1145/1376916.1376938
- C. Beeri, M. Y. Vardi. *A proof procedure for data dependencies*. JACM 31(4), 1984.
- K. R. Apt, H. A. Blair, A. Walker. *Towards a theory of declarative knowledge*. In J. Minker (ed.), *Foundations of Deductive Databases and Logic Programming*, Morgan Kaufmann, 1988.
- M. Schaerf, M. Cadoli. *Tractable reasoning via approximation*. Artificial Intelligence 74(2), 1995. [U]
- S. Zilberstein. *Using anytime algorithms in intelligent systems*. AI Magazine 17(3), 1996. [U]
