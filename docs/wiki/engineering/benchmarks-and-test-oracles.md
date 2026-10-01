# Benchmarks and test oracles

Which benchmark suites measure correctness and performance (ChaseBench, LUBM/UOBM, iWarded, termination suites, own enterprise KBs, 1,000 small KBs), and which external systems serve as oracles on which fragment (Graal via T(P), clingo/DLV, Nemo, Soufflé), with their exact validity boundaries.

> **Status in this project:** `v0` `F2` — README Next steps phase 2; oracle boundaries report 09 §1.4, report 11 §9.2; [E11](../project/requirements.md#e11).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Free sections, keeping title, summary, status box, related pages and references ([conventions](../conventions.md#2-page-template)). Cover at least:

- [ ] Benchmark catalogue (report 06 §3): ChaseBench (STB, ONT, Doctors, LUBM, Deep), iBench, LUBM/UOBM, real ontologies, iWarded, B-Runner, termination test ontologies.
- [ ] Small-KB workload (agents): 1,000 small KBs, latency (report 05 §3.5, §7).
- [ ] Oracle matrix: Graal EXACT / LOWER-BOUND / NONE; clingo/DLV with scaled integers; Nemo constant answers of positive queries only (report 12 T5); Soufflé for v0.
- [ ] Determinism: deterministic budgets in conformance tests; comparison up to homomorphic equivalence for chase oracles (report 12 T6).
- [ ] How to run Graal on JDK 21 (report 03).

## Related pages

- [test strategy](../engineering/test-strategy.md)
- [graal](../systems/graal.md)
- [clingo and dlv](../systems/clingo-and-dlv.md)
- [nemo](../systems/nemo.md)
- [function graph translation tp](../concepts/function-graph-translation-tp.md)

## Key references

- [report 06 §3](../../preliminary-analysis/06-sota-engines.md)
- [report 09 §1.4](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 11 §9.2](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 12 §8-§9](../../preliminary-analysis/12-invention-under-negation.md)
- [report 03 §2](../../preliminary-analysis/03-graal-build-and-dependencies.md)
- [report 05 §3, §7](../../preliminary-analysis/05-nemo-and-rust-option.md)
