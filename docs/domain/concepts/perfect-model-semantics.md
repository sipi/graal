# Perfect-model semantics

The perfect model of a stratified program is computed stratum by stratum, each stratum as a least fixpoint over the fixed result of the lower ones. It generalises the least model of positive programs and is the standard semantics of Datalog with stratified negation and aggregation. It does not depend on the chosen stratification and coincides with the stable and well-founded models on stratified programs.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Definition: strata, stratum-wise fixpoints, independence from the stratification.
- [ ] Local stratification and the perfect model of Przymusinski (priority between ground atoms).
- [ ] Relation with stable models and the well-founded model on stratified programs.
- [ ] Infinite perfect models with function symbols.
- [ ] Answers as truth in the perfect model vs certain answers over first-order models; when they agree.
- [ ] Examples: default conditions; basket threshold.

## Related pages

- [stratified negation](stratified-negation.md): stratification.
- [Datalog](datalog.md): least model.
- [logic programming and ASP](logic-programming-and-asp.md): stable and well-founded semantics.
- [aggregation](aggregation.md): stratified aggregates.

## Key references

- K. R. Apt, H. A. Blair, A. Walker. *Towards a theory of declarative knowledge*. In J. Minker (ed.), *Foundations of Deductive Databases and Logic Programming*, Morgan Kaufmann, 1988.
- T. C. Przymusinski. *On the declarative semantics of deductive databases and logic programs*. Same volume, 1988. [U]
- M. Gelfond, V. Lifschitz. *The stable model semantics for logic programming*. ICLP/SLP 1988.
- A. Van Gelder, K. A. Ross, J. S. Schlipf. *The well-founded semantics for general logic programs*. JACM 38(3), 1991.
- A. Van Gelder. *Negation as failure using tight derivations for general logic programs*. JLP 6(1-2), 1989. [U]
