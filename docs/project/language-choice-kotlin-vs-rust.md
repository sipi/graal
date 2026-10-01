# Language choice: Kotlin vs Rust

The implementation language of the new core is open (E6): Kotlin/JVM or Rust. Report 08 (the most recent analysis) scores Rust higher on assurance, aerospace/defense suitability and integration, Kotlin on velocity; report 05's Kotlin recommendation is historical. The decision will be taken by a time-boxed dual spike with explicit go/no-go criteria.

> **Status in this project:** `open` — [E6](requirements.md#e6); spike 2 weeks Kotlin + 3 weeks Rust (README; former inconsistencies I4 and I8 resolved editorially, [open questions](open-questions.md#known-inconsistencies)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Free sections, keeping title, summary, status box, related pages and references ([project page conventions](README.md#conventions-for-project-pages)). Cover at least:

- [ ] Summary of the weighted analysis and sensitivity check (report 08 §0, README E6).
- [ ] Spike scope and go/no-go criteria exactly as in the README (2 + 3 weeks; report 08's 3 + 4 weeks superseded).
- [ ] Why no hybrid Rust kernel + Kotlin outer layer.
- [ ] Effect of agent-written code (E11): passes the conformance suite, reviewable architecture; Rust compiler-enforced safety as extra argument.
- [ ] Integration targets: MCP, Python, WASM, JVM; Lean as second language for proofs/certificate checker.
- [ ] Ecosystem per language (report 08 §6).

## Related pages

- [architecture principles](architecture-principles.md)
- [Nemo](../domain/systems/nemo.md) (domain)
- [roadmap](roadmap.md)

## Key references

- [README E6, Next steps](../preliminary-analysis/README.md)
- [report 08](../preliminary-analysis/08-kotlin-vs-rust.md)
- [report 05 §0, §6](../preliminary-analysis/05-nemo-and-rust-option.md)
