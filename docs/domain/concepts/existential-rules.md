# Existential rules

Existential rules (tuple-generating dependencies, Datalog±) allow existentially quantified variables in rule heads, so reasoning can assert the existence of individuals that are not named in the data. They unify ontology languages (Horn description logics) and database dependencies (data exchange, integration). Query answering is undecidable in general, which motivates the decidability classes and chase variants described elsewhere.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Definition: TGDs, frontier, existential variables; single-head vs multi-head; normal forms and their effect on termination.
- [ ] Semantics: first-order models, certain answers, universal models; the chase as a reasoning procedure.
- [ ] Undecidability of CQ entailment and semi-decidability; overview of FES/FUS/BTS (link decidability classes).
- [ ] The Datalog± family: linear, guarded, frontier-guarded, sticky, warded, shy (short, link).
- [ ] Relation with Horn description logics (DL-Lite, EL, Horn-SHIQ) and with data-exchange dependencies.
- [ ] Three readings of 'every employee has a manager': existential variable, rule-local Skolem function, shared function (link Skolem pages).
- [ ] Examples: teaching ontology; managers are employees (non-terminating).
- [ ] Negative constraints and EGDs alongside TGDs.

## Related pages

- [labelled nulls](labelled-nulls.md): the terms the chase creates.
- [chase variants](../algorithms/chase-variants.md): the reasoning procedure.
- [decidability classes](decidability-classes.md): where reasoning is decidable.
- [Skolem functions and terms](skolem-functions-and-terms.md): the functional alternative.
- [query rewriting](../algorithms/query-rewriting.md): backward reasoning.
- [description logics and OWL](../adjacent/description-logics-and-owl.md): related ontology languages.

## Key references

- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- A. Calì, G. Gottlob, T. Lukasiewicz. *A general Datalog-based framework for tractable query answering over ontologies*. Journal of Web Semantics 14, 2012.
- A. Calì, G. Gottlob, M. Kifer. *Taming the infinite chase: query answering under expressive relational constraints*. JAIR 48, 2013.
- R. Fagin, P. G. Kolaitis, R. J. Miller, L. Popa. *Data exchange: semantics and query answering*. Theoretical Computer Science 336(1), 2005.
- M.-L. Mugnier, M. Thomazo. *An introduction to ontology-based query answering with existential rules*. Reasoning Web 2014, LNCS 8714.
- C. Beeri, M. Y. Vardi. *A proof procedure for data dependencies*. JACM 31(4), 1984.
