# Exact decimals and rounding

Business rules compare and add money amounts exactly, so F2 uses exact decimal arithmetic (no binary floats) and lets the modeller choose the rounding mode among `floor`, `round` (half-up) and `bank_round` (half-even), per D7. This page defines the value space, arithmetic, division, rounding and the open points.

> **Status in this project:** `F2` `open` — [D7](../project/decisions.md#d7) (three modes, modeller's choice; supersedes [OP-7](../project/open-questions.md#op-7)); report 11 §1.2, §4.3 (DRAFT); [OP-5](../project/open-questions.md#op-5), [OP-6](../project/open-questions.md#op-6), [OP-8](../project/open-questions.md#op-8); inconsistency I1 in [open questions](../project/open-questions.md#known-inconsistencies).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Value space `𝔻` (finite decimal fractions), integers as a subtype, value identity `2 = 2.00` (OP-5).
- [ ] Exact `+ − ×`; partial division and explicit `div(…, s, mode)` (OP-6); digits budget (OP-8).
- [ ] D7 rounding modes: precise definitions of `floor`, `round` (half-up), `bank_round` (half-even), including negative numbers (open: half-up vs half away from zero; floor toward −∞).
- [ ] Mapping between D7 names and report 11 names (`half_up`, `half_even`, `floor`, `down`, ...), flagged as inconsistency I1 — do not resolve.
- [ ] Open: whether a default mode exists at all, and the concrete syntax.
- [ ] Comparisons across datatypes; ill-typed built-ins (report 11 §2.2).
- [ ] Worked table from E2-ex (5% discount) and tests to write; oracle with scaled integers in clingo.
- [ ] Implementation notes: arbitrary-precision decimal libraries in Kotlin/Rust [U].

## Related pages

- [aggregation](../concepts/aggregation.md)
- [running examples](../project/running-examples.md)
- [test strategy](../engineering/test-strategy.md)
- [open questions](../project/open-questions.md)

## Key references

- [README D7](../../preliminary-analysis/README.md)
- [report 11 §1.2, §4.3, OP-5..OP-8](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 09 §5.3](../../preliminary-analysis/09-skolem-function-frameworks.md)
- IEEE 754-2008 decimal rounding-direction attributes (roundTiesToEven, roundTiesToAway) [U].
