# Modeller diagnostics

Diagnostics are analyser outputs addressed to the human or LLM who wrote the rules: non-stratifiable cycles, invention under negation, non-termination witnesses, suspected unintended infinite chains. For a formalisation layer used by AI agents they may be worth as much as the answers (owner, Q1 lead C). This page collects what must be diagnosed and how.

> **Status in this project:** `F2` `open` — [Q1](../project/open-questions.md#q1) lead C (candidate first-class analyser output, to be specified); [D2](../project/decisions.md#d2) (non-stratifiable lookup cycles must be detected); report 11 §3.3, §7.3; report 12 §7.2 ([OP-21](../project/open-questions.md#op-21), [OP-22](../project/open-questions.md#op-22)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Catalogue: lookup-induced non-stratifiability (D2, report 11 §3.3); negation through invention, self- vs cross-defeating (report 12 §7.2); non-termination witnesses (MFA cyclic term, special-edge cycles); UNKNOWN blocking units; D2 conflicts and constraint violations; typing errors; undefined arithmetic.
- [ ] Q1 lead C: detecting "infinite chain attached to invented individuals" and phrasing it as a question to the modeller (E3-ex corrected).
- [ ] Remedy synthesis (report 12 §7.2 item 3), pattern recognition "invent unless recorded".
- [ ] Output format: machine-readable for LLM agents and readable for humans; always citing original rules.
- [ ] What is decided (D2 detection) vs open (format, scope of lead C).

## Related pages

- [provenance and explanations](../concepts/provenance-and-explanations.md)
- [decidability analyser](../algorithms/decidability-analyser.md)
- [blocking of recursive chains](../algorithms/blocking-of-recursive-chains.md)
- [lookup before invent](../concepts/lookup-before-invent.md)
- [open questions](../project/open-questions.md)

## Key references

- [README Q1 lead C, D2](../../preliminary-analysis/README.md)
- [report 11 §3.3, §6.3, §7.3](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 12 §7, §8](../../preliminary-analysis/12-invention-under-negation.md)
