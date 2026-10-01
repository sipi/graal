# Other systems

A short tour of other systems with transferable ideas: egglog (equality saturation, e-graphs), Scallop (provenance semirings, neurosymbolic), ascent (Rust Datalog macros, lattices), DDlog (differential Datalog, archived), ELK (EL reasoner, goal-directed tracing), Ontop (OBDA, query rewriting to SQL), plus Llunatic/ChaseFUN/PDQ.

> **Status in this project:** `background` — inspiration only.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] One short section per system: what it is, licence, status, the idea to borrow (report 06 §5), why not more.
- [ ] egglog: union-find + rebuild, equality saturation over rule sets (research for optimisation).
- [ ] Scallop: provenance as a semiring parameter.
- [ ] ascent / datafrog / crepe: Rust embedded Datalog (relevant to E6).
- [ ] DDlog: why differential dataflow is not recommended.
- [ ] ELK: consequence-based reasoning, goal-directed tracing.
- [ ] Ontop: T-mappings, tree-witness rewriting, OBDA over RDBMS.
- [ ] Llunatic, ChaseFUN, PDQ: chase in RDBMS, EGD stratification, proof certificates.

## Related pages

- [provenance and explanations](../concepts/provenance-and-explanations.md)
- [incremental maintenance](../algorithms/incremental-maintenance.md)
- [query rewriting pure](../algorithms/query-rewriting-pure.md)
- [language choice kotlin vs rust](../engineering/language-choice-kotlin-vs-rust.md)

## Key references

- [report 06 §1.8-§1.12, §5](../../preliminary-analysis/06-sota-engines.md)
- [report 01 §3](../../preliminary-analysis/01-ecosystem-and-alternatives.md)
