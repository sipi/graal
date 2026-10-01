# Nemo

Nemo (TU Dresden, Rust, Apache-2.0/MIT) is a fast rule engine: columnar tries, leapfrog-style joins, semi-naive evaluation, Datalog-first restricted chase, stratified negation, simple aggregates, tracing. It lacks query rewriting, piece-unifier GRD, decidability analysis on main, incremental updates and a JVM binding. Rejected as the core (option D), kept as optional backend and second oracle.

> **Status in this project:** `background` — option D rejected; optional backend / second oracle; architectural inspiration for storage and joins.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] Architecture (crates, data model, execution model) and chase variant (report 05 §1).
- [ ] Performance evidence and small-KB latency (report 05 §3, §7).
- [ ] Use as oracle: constant answers of positive queries only; not a per-trigger Datalog-first chase on cyclic programs (report 12 T5).
- [ ] Ideas to borrow: columnar storage, WCOJ, rule-model pipeline with origins.

## Related pages

- [worst case optimal joins](../algorithms/worst-case-optimal-joins.md)
- [vlog rulewerk](../systems/vlog-rulewerk.md)
- [benchmarks and test oracles](../engineering/benchmarks-and-test-oracles.md)
- [language choice kotlin vs rust](../engineering/language-choice-kotlin-vs-rust.md)

## Key references

- [report 05](../../preliminary-analysis/05-nemo-and-rust-option.md)
- [report 06 §1.3](../../preliminary-analysis/06-sota-engines.md)
- [report 12 §8 T5, Appendix C](../../preliminary-analysis/12-invention-under-negation.md)
- Ivliev, Gerlach, Meusel, Steinberg, Krötzsch. *Nemo: your friendly and versatile rule reasoning toolkit*. KR 2024.
