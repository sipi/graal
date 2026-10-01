# Language choice: Kotlin vs Rust

The implementation language of the new core is open (E6): Kotlin/JVM or Rust. Report 08 scores Rust higher on assurance, aerospace/defense suitability and integration, Kotlin on velocity; report 05 leaned to Kotlin. The decision will be taken by a time-boxed dual spike with explicit go/no-go criteria.

> **Status in this project:** `open` — [E6](../project/requirements.md#e6); inconsistency I4 on spike duration ([open questions](../project/open-questions.md#known-inconsistencies)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Free sections, keeping title, summary, status box, related pages and references ([conventions](../conventions.md#2-page-template)). Cover at least:

- [ ] Summary of the weighted analysis and sensitivity check (report 08 §0, README E6).
- [ ] Spike scope and go/no-go criteria exactly as in the README (README wins over report 08 on duration: I4).
- [ ] Why no hybrid Rust kernel + Kotlin outer layer.
- [ ] Effect of agent-written code (E11): passes the conformance suite, reviewable architecture; Rust compiler-enforced safety as extra argument.
- [ ] Integration targets: MCP, Python, WASM, JVM; Lean as second language for proofs/certificate checker.
- [ ] Ecosystem per language (report 08 §6).

## Related pages

- [architecture principles](../engineering/architecture-principles.md)
- [nemo](../systems/nemo.md)
- [roadmap](../project/roadmap.md)

## Key references

- [README E6, Next steps](../../preliminary-analysis/README.md)
- [report 08](../../preliminary-analysis/08-kotlin-vs-rust.md)
- [report 05 §0, §6](../../preliminary-analysis/05-nemo-and-rust-option.md)
