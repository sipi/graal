# Value-invention strategies

Rule languages create new individuals in different ways: labelled nulls from existential variables, Skolem terms, built-ins that mint identifiers, and constructor predicates that create or retrieve an entity per key. A frequent practical need is to reuse a recorded value when one exists and invent one otherwise ('lookup before invent'). This page compares the mechanisms, their semantics and the patterns systems offer.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Catalogue: existential variables + chase variant; rule-local Skolem terms; shared function symbols; identifier-minting built-ins; constructor predicates.
- [ ] System mechanisms: LogicBlox constructor predicates; Jena `makeSkolem`, `makeInstance`, `makeTemp`; RDFox `SKOLEM`; Vadalog Skolem functions; ASP function terms; SQL sequences and UUIDs as a contrast.
- [ ] Determinism and order independence: when the same input yields the same invented identifiers.
- [ ] Lookup-before-invent patterns: restricted-chase behaviour (invent only if no witness exists); explicit encodings with default negation ('invent unless recorded'); their stratification constraints (the lookup source must not depend on the invented value).
- [ ] Invention under negation: self-defeating and cross-defeating invention; order dependence.
- [ ] Functionality and keys: one value per key as a constraint vs as an equality rule.
- [ ] Examples: every employee has a manager, with and without recorded managers.

## Related pages

- [Skolem functions and terms](skolem-functions-and-terms.md): function terms.
- [labelled nulls](labelled-nulls.md): anonymous invention.
- [chase variants](../algorithms/chase-variants.md): restricted chase as implicit lookup.
- [stratified negation](stratified-negation.md): encoding lookup with negation.
- [equality and UNA](equality-and-una.md): keys and co-reference.
- [systems: Jena rules](../systems/jena-rules.md): makeSkolem, makeInstance.

## Key references

- LogicBlox reference manual, *Constructor predicates*. https://developer.logicblox.com/content/docs/core-reference/webhelp/constructor-predicates.html
- M. Aref, B. ten Cate, T. J. Green, B. Kimelfeld, D. Olteanu, E. Pasalic, T. L. Veldhuizen, G. Washburn. *Design and implementation of the LogicBlox system*. SIGMOD 2015.
- Apache Jena documentation, *Reasoners and rule engines* (builtins `makeSkolem`, `makeInstance`, `makeTemp`, `noValue`). https://jena.apache.org/documentation/inference/
- RDFox documentation, *Reasoning* (`SKOLEM` built-in). https://docs.oxfordsemantic.tech/reasoning.html
- L. Bellomarini, E. Sallinger, G. Gottlob. *The Vadalog system: Datalog-based reasoning for knowledge graphs*. PVLDB 11(9), 2018.
- D. Carral, I. Dragoste, M. Krötzsch. *Restricted chase (non)termination for existential rules with disjunctions*. IJCAI 2017. https://doi.org/10.24963/ijcai.2017/128
- B. Marnette. *Resolution and datalog rewriting under value invention and equality constraints*. arXiv:1212.0254, 2012. [U]
