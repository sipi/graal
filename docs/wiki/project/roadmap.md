# Roadmap

The phase order decided by the project owner, the v0 / F2 / later split, and the deliverables of each phase. Dates are not set; only the order is decided.

> **Status in this project:** `v0` `F2` `later` — governed by README *Next steps*, [D4](decisions.md#d4), [D6](decisions.md#d6), [E11](requirements.md#e11).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

## Phases (decided order)

| # | Phase | Deliverables | State (2026-10-01) |
|---|---|---|---|
| 1 | **Theoretical framework** | 09 frameworks (done, F2 chosen, [D1](decisions.md#d1)); 10 rule transformations and proof sheets (**postponed**, [D4](decisions.md#d4)); 11 framework definition (**drafted, awaiting owner validation**) | in progress |
| 2 | **Test scenarios and quality benchmark** | conformance suite with expected outputs and statuses, built independently of the implementation; oracles: Graal on its valid fragment, clingo/DLV for negation/aggregation over invented terms | not started; report 12 §9 already lists 14 scenarios |
| 3 | **Software specification and architecture** | module boundaries, term model, storage, APIs, analyser output format | not started |
| 4 | **Prototype** | engine passing the conformance suite; language decided by the spike (E6) | not started |

Per [D6](decisions.md#d6), phases 2-4 are first run **for the v0 fragment** (positive Datalog); F2 features follow by extension.

## Release scopes

### v0: plain positive Datalog ([D6](decisions.md#d6))

- **Language:** Datalog rules and facts over constants; conjunctive queries (unions open).
- **Semantics:** least Herbrand model; certain answers coincide with model answers ([Datalog](../concepts/datalog.md)).
- **Engine core:** term model with three kinds (only constants populated), dictionary encoding, semi-naive evaluation per SCC, homomorphism search for queries, GRD (for Datalog, plain atom unification between heads and bodies) and strata scaffolding.
- **Analyser:** recognises the plain Datalog fragment and dispatches it; always COMPLETE-STATIC (Datalog terminates).
- **Tests:** conformance suite for Datalog; oracles: Graal, Nemo, Soufflé, clingo ([benchmarks and oracles](../engineering/benchmarks-and-test-oracles.md)).
- **Open for v0:** whether v0 includes query rewriting / backward chaining for Datalog, incremental maintenance, explanations, and which APIs (CLI, library, MCP). To be decided by the owner in phase 3.

### F2 (v1)

Everything of [report 11](../../preliminary-analysis/11-f2-framework-definition.md) once validated: named functions with lookup-before-invent, stratified negation, non-recursive aggregation with exact decimals and D7 rounding, constraints, statuses and budgets, termination portfolio, strategies (materialisation, tabled backward chaining, pre-computed rewriting under the D5 guard; hybrid rewriting possibly v1.1, OP-17). Q1 leads to be revisited here.

### Later

- F3 hybrid (existential variables, labelled nulls, [D1](decisions.md#d1)).
- Rule-set optimisation with proof sheets ([D4](decisions.md#d4), E8, E9, E12), then Lean mechanisation (E9 level b).
- Recursive monotone aggregation; answer-level soundness via provenance (OP-10).
- Large-KB and aerospace/defense requirements.

## Cross-cutting activities

- The language spike (E6) can run once the v0 specification exists; its scope (README E6): data model, homomorphism with bi-connected components, GRD + SCC chase, benchmarks (LUBM, 1,000 small KBs, against Nemo and Graal).
- Legal check of the licensing note by counsel ([licensing](../legal/licensing-and-provenance.md)).

## Related pages

- [vision and scope](vision-and-scope.md), [decisions](decisions.md), [open questions](open-questions.md), [test strategy](../engineering/test-strategy.md).

## References

- [README, Next steps](../../preliminary-analysis/README.md#next-steps).
- [Report 08 §8](../../preliminary-analysis/08-kotlin-vs-rust.md) (staged de-risking plan), [report 11](../../preliminary-analysis/11-f2-framework-definition.md), [report 12 §9](../../preliminary-analysis/12-invention-under-negation.md).
