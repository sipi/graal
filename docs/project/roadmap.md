# Roadmap

The phase order decided by the project owner, the v0 / F2 / later split, and the deliverables of each phase. Dates are not set; only the order is decided.

> **Status in this project:** `v0` `F2` `later` — governed by README *Next steps*, [D4](decisions.md#d4), [D6](decisions.md#d6), [D16](decisions.md#d16), [D17](decisions.md#d17)/[E13](requirements.md#e13), [E11](requirements.md#e11).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

## Working principle

Every phase follows [D17](decisions.md#d17) / [E13](requirements.md#e13): first define the target (the "cap"), then reach it in small, iterative, incremental steps, each verifiable and validated.

## Phases (decided order)

| # | Phase | Deliverables | State (2026-10-01) |
|---|---|---|---|
| 1 | **Theoretical framework** | 09 frameworks (done, F2 chosen, [D1](decisions.md#d1)); 10 rule transformations and proof sheets (**postponed**, [D4](decisions.md#d4)); 11 framework definition (**validated 2026-10-01 except OP-3**, D9-D16); 14 uniqueness and functionality (**in progress**, input for OP-3) | nearly done |
| 2 | **Test scenarios and quality benchmark** | conformance suite with expected outputs and statuses, built independently of the implementation; oracles: Graal on its valid fragment, clingo/DLV for negation/aggregation over invented terms | not started; report 12 §9 already lists 14 scenarios |
| 3 | **Software specification and architecture** | module boundaries, term model, storage, APIs, analyser output format | not started |
| 4 | **Prototype** | engine passing the conformance suite; language decided by the spike (E6) | not started |

Per [D6](decisions.md#d6), phases 2-4 are first run **for the v0 fragment** (positive Datalog); F2 features follow by extension. Per [D16](decisions.md#d16), strategies are staged: pure chase in v0/v1, then rewriting or backward chaining, then hybrid.

## Release scopes

### v0: plain positive Datalog ([D6](decisions.md#d6))

- **Language:** Datalog rules and facts over constants; conjunctive queries (unions open).
- **Semantics:** least Herbrand model; certain answers coincide with model answers ([Datalog](../domain/concepts/datalog.md)).
- **Engine core:** term model with three kinds (only constants populated), dictionary encoding, semi-naive evaluation per SCC, homomorphism search for queries, GRD (for Datalog, plain atom unification between heads and bodies) and strata scaffolding.
- **Strategy:** materialisation only ([D16](decisions.md#d16)): no query rewriting, no backward chaining.
- **Analyser:** recognises the plain Datalog fragment and dispatches it; always COMPLETE-STATIC (Datalog terminates), unless a time budget cuts the run ([D12](decisions.md#d12)).
- **Tests:** conformance suite for Datalog; oracles: Graal, Nemo, Soufflé, clingo ([benchmarks and oracles](../domain/evaluation/benchmarks-and-test-oracles.md)).
- **Open for v0:** incremental maintenance, explanations, and which APIs (CLI, library, MCP). To be decided by the owner in phase 3. (Rewriting and backward chaining are excluded from v0 by [D16](decisions.md#d16).)

### F2 (v1)

The [report 11](../preliminary-analysis/11-f2-framework-definition.md) contract (validated except OP-3): named functions with lookup-before-invent ([D2](decisions.md#d2), [D14](decisions.md#d14)); functional terms allowed in input with a round-trip output encoding ([D11](decisions.md#d11)); distinct Skolem terms denote distinct individuals ([D10](decisions.md#d10)); labelled nulls reserved, rejected in input ([D9](decisions.md#d9)); stratified negation; non-recursive aggregation with exact decimals and five explicit rounding modes ([D13](decisions.md#d13)); constraints; four statuses and budgets ([D12](decisions.md#d12)); termination portfolio. **Strategy: materialisation (chase) only** ([D16](decisions.md#d16)). OP-3 and OP-23 must be settled before the corresponding features are specified. Q1 leads to be revisited here.

### Later

- Query rewriting or backward chaining (tabling), then hybrid strategies including D5 per-stratum rewriting and pre-computed rewritings under the strict guard ([D16](decisions.md#d16), [D5](decisions.md#d5), [D15](decisions.md#d15)).
- Co-reference option for Skolem terms ([D10](decisions.md#d10), OP-23).
- F3 hybrid (existential variables, labelled nulls, [D1](decisions.md#d1)); RDF blank nodes as input after an impact study ([D9](decisions.md#d9)).
- Rule-set optimisation with proof sheets ([D4](decisions.md#d4), E8, E9, E12), then Lean mechanisation (E9 level b).
- Recursive monotone aggregation; answer-level soundness via provenance (OP-10).
- Large-KB and aerospace/defense requirements.

## Cross-cutting activities

- The language spike (E6, 2 weeks Kotlin + 3 weeks Rust) can run once the v0 specification exists; its scope (README E6): data model, homomorphism with bi-connected components, GRD + SCC chase, benchmarks (LUBM, 1,000 small KBs, against Nemo and Graal).
- Legal check of the licensing note by counsel ([licensing](licensing-and-provenance.md)).

## Related pages

- [vision and scope](vision-and-scope.md), [decisions](decisions.md), [open questions](open-questions.md), [test strategy](test-strategy.md).

## References

- [README, Next steps](../preliminary-analysis/README.md#next-steps).
- [Report 08 §8](../preliminary-analysis/08-kotlin-vs-rust.md) (staged de-risking plan), [report 11](../preliminary-analysis/11-f2-framework-definition.md), [report 12 §9](../preliminary-analysis/12-invention-under-negation.md).
