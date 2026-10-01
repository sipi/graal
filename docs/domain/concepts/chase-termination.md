# Chase termination

When does forward chaining with value invention stop? Termination depends on the chase variant, on whether it is required for one instance or for all instances, and, for the restricted chase, on the order of rule applications. It is undecidable in general, so practical guarantees come from sufficient conditions, while non-termination can sometimes be certified.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Notions: termination on one instance vs all instances; all fair sequences vs some sequence (restricted chase); fairness.
- [ ] Critical instance: all-instance termination of the (semi-)oblivious chase equals termination on the critical instance; when the property fails (negation, built-ins, constants in rules).
- [ ] Undecidability results; recent results on restricted-chase termination beyond recursive enumerability.
- [ ] Decidable cases: linear, guarded, sticky rules for given variants.
- [ ] Sufficient conditions (link decidability classes) and non-termination certificates (cyclic Skolem terms in MFA, restricted cyclicity).
- [ ] Dynamic termination (fixpoint observed on the given data) vs static guarantee.
- [ ] Budgets and their effect on results (link soundness and completeness of partial results).
- [ ] Normalisation changes termination.
- [ ] Example: managers are employees.

## Related pages

- [chase variants](../algorithms/chase-variants.md): the variants.
- [decidability classes](decidability-classes.md): sufficient conditions.
- [soundness and completeness of partial results](soundness-and-completeness-of-partial-results.md): stopping early.
- [blocking and finite representations](../algorithms/blocking-and-finite-representations.md): finite representations instead of termination.

## Key references

- A. Deutsch, A. Nash, J. Remmel. *The chase revisited*. PODS 2008. https://doi.org/10.1145/1376916.1376938
- B. Marnette. *Generalized schema-mappings: from termination to tractability*. PODS 2009.
- T. Gogacz, J. Marcinkowski. *All-instances termination of chase is undecidable*. ICALP 2014.
- G. Grahne, A. Onet. *Anatomy of the chase*. Fundamenta Informaticae 157(3), 2018.
- D. Carral, L. Gerlach, L. Larroque, M. Thomazo. *Restricted chase termination: you want more than fairness*. PODS 2025 / PACMMOD 3(2). https://doi.org/10.1145/3725246
- D. Carral, I. Dragoste, M. Krötzsch. *Restricted chase (non)termination for existential rules with disjunctions*. IJCAI 2017. https://doi.org/10.24963/ijcai.2017/128
- M. Leclère, M.-L. Mugnier, M. Thomazo, F. Ulliana. *A single approach to decide chase termination on linear existential rules*. ICDT 2019.
- D. Carral, L. Larroque, M.-L. Mugnier, M. Thomazo. *Normalisations of existential rules: not so innocuous!* KR 2022.
- M. Calautti, G. Gottlob, A. Pieris. *Chase termination for guarded existential rules*. PODS 2015.
