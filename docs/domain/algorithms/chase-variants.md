# Chase variants

The chase is forward chaining with value invention: it repeatedly applies rules, creating labelled nulls for existential variables, and produces a universal model when it terminates. Its variants (oblivious, semi-oblivious or Skolem, restricted, Datalog-first restricted, parallel, core, equivalent, parsimonious, termination-controlled) differ in when a rule application is considered redundant, which changes result size, termination and order dependence.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Triggers, active triggers, rounds; definitions of each variant.
- [ ] Semi-oblivious = Skolem chase (up to null renaming).
- [ ] Order dependence of the restricted chase; Datalog-first strategy.
- [ ] Core chase and FES; equivalent chase; parsimonious chase (shy programs); isomorphism-based termination control (warded rules).
- [ ] Termination hierarchy between variants.
- [ ] Chase with default negation: what 'active' means and order effects.
- [ ] Example: teaching ontology; managers are employees under each variant.

## Related pages

- [existential rules](../concepts/existential-rules.md): the language.
- [chase termination](../concepts/chase-termination.md): termination.
- [labelled nulls](../concepts/labelled-nulls.md): created terms.
- [value-invention strategies](../concepts/value-invention-strategies.md): restricted chase as lookup.
- [systems: Nemo, VLog, Vadalog, Graal](../systems/nemo.md): implementations.

## Key references

- A. Onet. *The chase procedure and its applications in data exchange*. Dagstuhl Follow-Ups 5, 2013.
- G. Grahne, A. Onet. *Anatomy of the chase*. Fundamenta Informaticae 157(3), 2018.
- M. Benedikt, G. Konstantinidis, G. Mecca, B. Motik, P. Papotti, D. Santoro, E. Tsamoura. *Benchmarking the chase*. PODS 2017.
- A. Deutsch, A. Nash, J. Remmel. *The chase revisited*. PODS 2008. https://doi.org/10.1145/1376916.1376938
- B. Marnette. *Generalized schema-mappings: from termination to tractability*. PODS 2009.
- D. Carral, I. Dragoste, M. Krötzsch. *Restricted chase (non)termination for existential rules with disjunctions*. IJCAI 2017. https://doi.org/10.24963/ijcai.2017/128
- N. Leone, M. Manna, G. Terracina, P. Veltri. *Fast query answering over existential rules*. ACM TOCL 20(2), 2019.
- L. Bellomarini, E. Sallinger, G. Gottlob. *The Vadalog system: Datalog-based reasoning for knowledge graphs*. PVLDB 11(9), 2018.
- S. Rocher. *Querying existential rule knowledge bases: decidability and complexity*. PhD thesis, Université de Montpellier, 2016. [U]
