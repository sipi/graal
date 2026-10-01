# Test strategy

How the conformance and quality suite is built: from the specification, independently of and before the implementation (E11), with expected answers and expected statuses and diagnostics, differential tests for transformations (E9), oracle cross-checks, and deterministic budgets.

> **Status in this project:** `v0` `F2` — [E11](requirements.md#e11), [E9](requirements.md#e9), [E13](requirements.md#e13), README Next steps phase 2; v0 first ([D6](decisions.md#d6)); statuses [D12](decisions.md#d12).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Free sections, keeping title, summary, status box, related pages and references ([project page conventions](README.md#conventions-for-project-pages)). Cover at least:

- [ ] Principles: spec-derived expected outputs (README decisions, report 11 validated except OP-3); implementation agents never edit expected outputs; owner validates contested cases; tests define the target of each step before implementation (E13/D17).
- [ ] Test kinds: semantic conformance (answers + the four statuses of D12 + diagnostics), analyser classification, rejection tests (non-stratifiable incl. lookup cycles D14, unsafe, labelled nulls in input D9, missing rounding mode D13), round-trip of functional terms through the output encoding (D11), rounding tests for the five modes including negative values (D13), oracle cross-checks, property-based tests, performance benchmarks. Strategy-vs-strategy differential tests (report 11 §8.5 item 4) wait for a second strategy (D16).
- [ ] Oracle matrix (project choice; domain background in [benchmarks and test oracles](../domain/evaluation/benchmarks-and-test-oracles.md)): Graal via T(P) EXACT / LOWER-BOUND / NONE (report 09 §1.4, report 11 §9.2); clingo/DLV with decimals scaled to integers; Nemo for constant answers of positive queries only (report 12 §8 T5); Soufflé for v0; comparison up to homomorphic equivalence for chase oracles (report 12 T6); running Graal on JDK 21 (report 03).
- [ ] Test case format (proposal; open): input KB, query, mode, budget, expected answers, expected status, expected diagnostics, provenance of the expectation.
- [ ] Existing scenario lists: report 12 §9 (T12-01..14), report 11 §10 worked examples, running examples.
- [ ] v0 test plan for positive Datalog.
- [ ] Location of the suite in the repository (open).

## Related pages

- [benchmarks and test oracles](../domain/evaluation/benchmarks-and-test-oracles.md)
- [how agents work here](how-agents-work-here.md)
- [running examples](running-examples.md)
- [decisions D12](decisions.md#d12); domain: [soundness and completeness of partial results](../domain/concepts/soundness-and-completeness-of-partial-results.md)

## Key references

- [README E9, E11, Next steps](../preliminary-analysis/README.md)
- [report 11 §6.4, §8.5, §10](../preliminary-analysis/11-f2-framework-definition.md)
- [report 12 §8-§9](../preliminary-analysis/12-invention-under-negation.md)
- [report 09 §1.4](../preliminary-analysis/09-skolem-function-frameworks.md), [report 03 §2](../preliminary-analysis/03-graal-build-and-dependencies.md)
