# Test strategy

How the conformance and quality suite is built: from the specification, independently of and before the implementation (E11), with expected answers and expected statuses and diagnostics, differential tests for transformations (E9), oracle cross-checks, and deterministic budgets.

> **Status in this project:** `v0` `F2` — [E11](../project/requirements.md#e11), [E9](../project/requirements.md#e9), README Next steps phase 2; v0 first ([D6](../project/decisions.md#d6)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Free sections, keeping title, summary, status box, related pages and references ([conventions](../conventions.md#2-page-template)). Cover at least:

- [ ] Principles: spec-derived expected outputs; implementation agents never edit expected outputs; owner validates contested cases.
- [ ] Test kinds: semantic conformance (answers + statuses + diagnostics), analyser classification, rejection tests (non-stratifiable, unsafe), differential (strategy vs strategy on complete units, report 11 §8.5 item 4), oracle cross-checks, property-based tests, performance benchmarks.
- [ ] Test case format (proposal; open): input KB, query, mode, budget, expected answers, expected status, expected diagnostics, provenance of the expectation.
- [ ] Existing scenario lists: report 12 §9 (T12-01..14), report 11 §10 worked examples, running examples.
- [ ] v0 test plan for positive Datalog.
- [ ] Location of the suite in the repository (open).

## Related pages

- [benchmarks and test oracles](../engineering/benchmarks-and-test-oracles.md)
- [how agents work here](../project/how-agents-work-here.md)
- [running examples](../project/running-examples.md)
- [completeness statuses](../concepts/completeness-statuses.md)

## Key references

- [README E9, E11, Next steps](../../preliminary-analysis/README.md)
- [report 11 §6.4, §8.5, §10](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 12 §9](../../preliminary-analysis/12-invention-under-negation.md)
