# Architecture principles

The architectural invariants already fixed by decisions: three term kinds from day one, consistent across stores; strata and evaluation units in the core even when v0 has one stratum; analyser-driven dispatch; explanations over original rules; v0 must extend to F2 rather than be rewritten. Everything else in the architecture is open until phase 3.

> **Status in this project:** `v0` `F2` — [D1](decisions.md#d1), [E5](requirements.md#e5), [D6](decisions.md#d6), [D9](decisions.md#d9)-[D12](decisions.md#d12), [D16](decisions.md#d16), [E2](requirements.md#e2)/[E7](requirements.md#e7), [E8](requirements.md#e8); phase 3 not started.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Free sections, keeping title, summary, status box, related pages and references ([project page conventions](README.md#conventions-for-project-pages)). Cover at least:

- [ ] Fixed invariants (with the decision each comes from), defined first as the target of phase 3 (E13/D17).
- [ ] Term model: constants (symbols, typed literals with value identity), named functional terms (hash-consed), labelled nulls reserved and rejected in input (D9); functional terms accepted in input and parsed back from the output encoding (D11); term ids with an indirection to a class representative so that the co-reference option can be activated later (D10, OP-23); dictionary encoding (proposal).
- [ ] Strata and evaluation units as first-class objects carrying the four statuses of D12 and budgets.
- [ ] Rule model pipeline with origin links (lb(K), T(P), optimisation).
- [ ] Analyser → strategy dispatch boundary; pluggable strategies, with only the chase implemented in v0/v1 (D16).
- [ ] Storage interfaces (many small KBs vs large KBs); no global mutable state (Graal lesson).
- [ ] Determinism requirements (report 11 §6.4).
- [ ] Open: module boundaries, APIs (library, CLI, MCP), persistence, concurrency.

## Related pages

- [labelled nulls](../domain/concepts/labelled-nulls.md) (domain)
- [Skolem functions and terms](../domain/concepts/skolem-functions-and-terms.md) (domain)
- [rule-set analysis tools](../domain/algorithms/rule-set-analysis-tools.md) (domain)
- [language choice kotlin vs rust](language-choice-kotlin-vs-rust.md)
- [roadmap](roadmap.md)

## Key references

- [README D1, D6, D9-D12, D16, E5](../preliminary-analysis/README.md)
- [report 02 §2, §5](../preliminary-analysis/02-graal-architecture-audit.md)
- [report 06 §5](../preliminary-analysis/06-sota-engines.md)
- [report 11 §1, §6](../preliminary-analysis/11-f2-framework-definition.md)
