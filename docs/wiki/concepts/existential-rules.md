# Existential rules

Existential rules (tuple-generating dependencies, Datalog+/-) allow existentially quantified variables in rule heads, so reasoning can create new, anonymous individuals (labelled nulls). They are the formalism of Graal and of most of the decidability literature the analyser relies on. They are not in v0 nor in F2 syntax, but they are the theory imported through T(P) and the basis of the future F3.

> **Status in this project:** `background` `later` — not in v0 ([D6](../project/decisions.md#d6)) nor F2 syntax ([D1](../project/decisions.md#d1)); bridge via T(P) ([D3](../project/decisions.md#d3)); F3 hybrid later.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definition of TGDs, frontier, existential variables; single-head vs multi-head; normal forms (and that normalisation changes termination, report 07 §1.4).
- [ ] Semantics: FO models, certain answers, universal models; chase as reasoning procedure.
- [ ] Undecidability of CQ entailment; semi-decidability; FES/FUS/BTS overview (link to decidability classes).
- [ ] Datalog+/- family: linear, guarded, sticky, warded (short, link).
- [ ] Relation to description logics (DL-Lite, EL, Horn-SHIQ) [U].
- [ ] Comparison with named Skolem functions: three readings of "every employee has a manager" (report 09 §1.1).
- [ ] E3-ex corrected reading in existential form; the infinite chain.
- [ ] Why this project chose F2 for v1 (D1) and keeps nulls for F3.

## Related pages

- [foundations](../concepts/foundations.md)
- [labelled nulls](../concepts/labelled-nulls.md)
- [skolem functions and terms](../concepts/skolem-functions-and-terms.md)
- [function graph translation tp](../concepts/function-graph-translation-tp.md)
- [decidability classes](../concepts/decidability-classes.md)
- [chase variants](../algorithms/chase-variants.md)
- [graal](../systems/graal.md)

## Key references

- [report 07 §1](../../preliminary-analysis/07-sota-theory.md)
- [report 09 §1](../../preliminary-analysis/09-skolem-function-frameworks.md)
- Baget, Leclère, Mugnier, Salvat. *On rules with existential variables: walking the decidability line*. AIJ 2011.
- Calì, Gottlob, Lukasiewicz. *A general Datalog-based framework for tractable query answering over ontologies*. JWS 2012 [U].
- Mugnier, Thomazo. *An introduction to ontology-based query answering with existential rules*. Reasoning Web 2014 [U].
