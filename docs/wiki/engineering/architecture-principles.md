# Architecture principles

The architectural invariants already fixed by decisions: three term kinds from day one, consistent across stores; strata and evaluation units in the core even when v0 has one stratum; analyser-driven dispatch; explanations over original rules; v0 must extend to F2 rather than be rewritten. Everything else in the architecture is open until phase 3.

> **Status in this project:** `v0` `F2` — [D1](../project/decisions.md#d1), [E5](../project/requirements.md#e5), [D6](../project/decisions.md#d6), [E2](../project/requirements.md#e2)/[E7](../project/requirements.md#e7), [E8](../project/requirements.md#e8); phase 3 not started.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Free sections, keeping title, summary, status box, related pages and references ([conventions](../conventions.md#2-page-template)). Cover at least:

- [ ] Fixed invariants (with the decision each comes from).
- [ ] Term model: constants (symbols, typed literals with value identity), named functional terms (hash-consed), labelled nulls; dictionary encoding (proposal).
- [ ] Strata and evaluation units as first-class objects (statuses, budgets).
- [ ] Rule model pipeline with origin links (lb(K), T(P), optimisation).
- [ ] Analyser → strategy dispatch boundary; pluggable strategies.
- [ ] Storage interfaces (many small KBs vs large KBs); no global mutable state (Graal lesson).
- [ ] Determinism requirements (report 11 §6.4).
- [ ] Open: module boundaries, APIs (library, CLI, MCP), persistence, concurrency.

## Related pages

- [labelled nulls](../concepts/labelled-nulls.md)
- [skolem functions and terms](../concepts/skolem-functions-and-terms.md)
- [decidability analyser](../algorithms/decidability-analyser.md)
- [language choice kotlin vs rust](../engineering/language-choice-kotlin-vs-rust.md)
- [roadmap](../project/roadmap.md)

## Key references

- [README D1, D6, E5](../../preliminary-analysis/README.md)
- [report 02 §2, §5](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 06 §5](../../preliminary-analysis/06-sota-engines.md)
- [report 11 §1, §6](../../preliminary-analysis/11-f2-framework-definition.md)
