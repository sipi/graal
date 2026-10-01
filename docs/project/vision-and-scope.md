# Vision and scope

What the engine is for, who uses it, what makes it different, and what is in and out of scope at each phase. The scope statements here are derived from the README decisions; they add nothing to them.

> **Status in this project:** `v0` `F2` `later` `out-of-scope` — governed by [D1](decisions.md#d1), [D6](decisions.md#d6), [E10](requirements.md#e10), README *Scope* and *Target domains*.
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

## Vision

A **homemade but solid logical reasoning engine** for rule-based knowledge representation:
- **Solid** means: always sound; complete whenever completeness can be proved or observed; explicit status otherwise ([E1](requirements.md#e1)); explicit semantics ([E3](requirements.md#e3)); explanations that cite the user's original rules ([E8](requirements.md#e8)).
- **Homemade** means: a new core written in this repository (option B of the README), guided by Graal's design and by the literature, not a fork of an existing engine.
- **Analyser-driven** means: a static analysis of the rule set (decidability, termination, complexity) chooses the reasoning algorithm per component ([E2/E7](requirements.md#e2)). This combination is the differentiator: no existing system offers analysis, automatic algorithm selection and high performance together (README, Clarifications).

## Users and target domains

| Phase | Domain | Typical KB | What matters |
|---|---|---|---|
| Now | **Formal reasoning layer for AI agents** (LLM agents formalise, the engine reasons) | many small KBs, built and queried quickly | latency, statuses, diagnostics for the modeller, explanations |
| Now | **Enterprise / complex business-domain modelling** | small to medium | defaults and exceptions, exact money arithmetic, identity of invented objects |
| Later | **Aerospace / defense** | potentially large | assurance, determinism, certificates, performance, possibly certified toolchains |

## Scope by phase

| Feature | v0 | F2 (v1) | Later | Out of scope |
|---|---|---|---|---|
| Positive Datalog, semi-naive materialisation | yes | yes | | |
| Three term kinds in the data model (constant, named functional term, labelled null) | **designed in**, only constants used | constants + named terms; functional terms accepted in input and round-tripped ([D11](decisions.md#d11)); nulls reserved, rejected in input ([D9](decisions.md#d9)) | nulls (F3) | |
| Strata in the architecture | designed in (one stratum) | stratified negation | | |
| Named Skolem functions, lookup-before-invent | | yes ([D1](decisions.md#d1), [D2](decisions.md#d2)) | | |
| Existential variables (rule syntax; labelled nulls in facts) | | no syntax | F3 hybrid | |
| Co-reference of distinct Skolem terms | | no: distinct terms, distinct individuals ([D10](decisions.md#d10)) | option, anticipated in the architecture (OP-23) | |
| Stratified negation | | yes | | non-stratified (ASP-style), unless delegated |
| Aggregation (count, sum, min, max), exact decimals, rounding modes | | yes, non-recursive; five explicit rounding modes, no default ([D13](decisions.md#d13)) | recursive monotone aggregation | |
| Decidability analyser | recognises the Datalog fragment | portfolio per SCC (report 11 §7) | richer classes | |
| Materialisation (chase) | yes | yes (only strategy, [D16](decisions.md#d16)) | | |
| Query rewriting, backward chaining, hybrid | no ([D16](decisions.md#d16)) | no ([D16](decisions.md#d16)) | yes: rewriting or backward chaining first, then hybrid ([E10](requirements.md#e10), [D5](decisions.md#d5), [D15](decisions.md#d15)) | GBTS-specific algorithms ([E10](requirements.md#e10)) |
| Blocking of simple recursive chains | | **open** ([Q1](open-questions.md#q1) lead B) | | general GBTS |
| Rule-set optimisation with proofs | | postponed ([D4](decisions.md#d4)) | Lean mechanisation (E9 level b) | verified implementation (E9 level c) |
| Incremental maintenance | | candidate (FBF/DRed) | | |
| Uncertainty, time | | | | out of scope for now (README) |

Rewriting and backward chaining are excluded from v0 and v1 by [D16](decisions.md#d16). Other v0 features beyond plain materialisation (incremental maintenance, explanations, APIs) are open; see the [roadmap](roadmap.md).

## Non-goals

- Re-using Graal's code (legal and quality reasons, see [licensing](licensing-and-provenance.md) and [Graal](../domain/systems/graal.md)).
- A hybrid Rust kernel with a Kotlin outer layer (README E6).
- Silent approximations: no float arithmetic, no silent rounding, no unflagged incomplete answers.

## Related pages

- [requirements](requirements.md), [decisions](decisions.md), [roadmap](roadmap.md), [main](README.md).

## References

- [README](../preliminary-analysis/README.md): Purpose, Options, Scope, Target domains, Clarifications.
- [Report 01](../preliminary-analysis/01-ecosystem-and-alternatives.md) (ecosystem), [report 06](../preliminary-analysis/06-sota-engines.md) (engines), [report 09 §6](../preliminary-analysis/09-skolem-function-frameworks.md) (F1/F2/F3).
