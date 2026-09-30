# Preliminary analysis: refurbish Graal or build a new core?

## Purpose

Evaluate whether to refurbish Graal (dependency upgrade, modernization, partial Kotlin conversion) in order to build a homemade but solid logical reasoning engine.

**Conclusion:** build a NEW core (language, Kotlin vs Rust, still under debate), using Graal as a test oracle.

## Reports

| # | Report | Summary |
|---|--------|---------|
| 01 | [Ecosystem and alternatives](01-ecosystem-and-alternatives.md) | Landscape of existential-rule / Datalog+- reasoners, their maintenance status, licences and strong ideas. |
| 02 | [Graal architecture audit](02-graal-architecture-audit.md) | Module structure, size, core data-model and code-quality issues, and which parts matter for a reasoning core. |
| 03 | [Graal build and dependencies](03-graal-build-and-dependencies.md) | Build on JDK 21, test results, dependency ageing, CVE exposure and modernization effort estimates. |
| 04 | [Graal vs InteGraal](04-graal-vs-integraal.md) | Gap analysis of InteGraal (Graal's successor) against the needs of a solid reasoning core. |
| 05 | [Nemo and the Rust option](05-nemo-and-rust-option.md) | Evaluation of Nemo (Rust) as a core or backend, benchmarks, and implications of a Rust rewrite. |
| 06 | [State of the art: existing engines](06-sota-engines.md) | Strong ideas of existing engines, plus a prioritised list of ideas to borrow. |
| 07 | [State of the art: theory](07-sota-theory.md) | Theory survey (semantics, equivalence-preserving transformations, decidability classes) with a checklist of 20 specification decisions. |
| 08 | [Kotlin vs Rust](08-kotlin-vs-rust.md) | Weighted analysis: Rust 4.10 vs Kotlin 3.35; recommends a Rust core, conditional on a spike. |

## Key findings

- Graal upstream is abandoned (last commit 2019, CeCILL 2.1).
- Graal builds on JDK 21 and all 492 tests pass once `--add-opens`/`--add-exports` are added to the surefire `argLine`; this makes it useful as an oracle.
- Core data-model issues: labelled nulls are represented inconsistently across stores, `AbstractRule` `equals`/`hashCode` are inconsistent, and there is global mutable state.
- InteGraal (Apache-2.0, 2.0.7, June 2025) is positioned for data integration. It lacks bi-connected-component homomorphism in its main path, reuses Graal's rule analyser verbatim, has only partial stratified negation, has no group-by aggregation, and nulls still do not round-trip across stores.
- Nemo (Rust) is fast (materialisation, stratified negation, simple aggregates, tracing) but lacks query rewriting, piece-unifier GRD, decidability analysis on `main`, incremental updates and a JVM binding.

## Options

| Option | Description | Status |
|--------|-------------|--------|
| A | Refurbish Graal | Rejected |
| B | New core guided by Graal, with Graal as test oracle | **Chosen** |
| C | Rust rewrite | Language still open (see E6) |
| D | Nemo as the core | Rejected; kept as optional backend / second oracle |
| E | Adopt InteGraal | Rejected |

## Requirements agreed so far

- **E1** Always sound; complete when decidability is proven; otherwise an explicit "completeness not guaranteed" status.
- **E2/E7** The decidability and complexity analyser is central and drives algorithm selection (materialisation, rewriting, hybrid).
- **E3** Explicit, controlled semantics: chase variant, stratified negation, then aggregation; treatment of labelled nulls.
- **E4** Must-have techniques: GRD via piece-unifiers, SCC-driven chase, UCQ query rewriting (PURE-style), homomorphism exploiting bi-connected components of the query.
- **E5** Labelled nulls must be a distinct term kind, consistent across all stores (first design decision).
- **E6** Language: Kotlin or Rust, to be decided.
  - The weighted analysis ([report 08](08-kotlin-vs-rust.md)) favours Rust (assurance, aerospace/defense, integration) and Kotlin on velocity. Sensitivity check: with velocity at 30% and assurance at 15%, Rust 4.0 vs Kotlin 3.55.
  - Decision via a reduced, time-boxed spike: 2 weeks in Kotlin + 3 weeks in Rust. Scope: data model, homomorphism with bi-connected components, GRD + SCC chase, and benchmarks (LUBM, 1,000 small KBs, compared with Nemo and Graal). MCP/Python/WASM integration is assessed on documentation only.
  - Go/no-go: Rust if it reaches feature parity with at most 1.6x the Kotlin hours, otherwise Kotlin. If both pass: Rust if defense prospects are real, Kotlin if the next 18 months are enterprise-only.
  - Avoid a hybrid Rust kernel + Kotlin outer layer: the boundary would cut through the homomorphism hot loop used by rewriting.
- **E8** Rule-set optimisation pass:
  - the original rule set is always kept;
  - the rewritten set is logically equivalent (query-answer equivalence may come later, low priority);
  - every transformation is backed by a formal proof of logical equivalence;
  - full traceability back to the original rules (explanations cite original rules).
- **E9** Transformation proofs are, at first, pen-and-paper proofs of logical equivalence (level a). Each is written as a standard proof sheet:
  - statement and applicability condition;
  - proof in both directions;
  - counter-example for a close non-equivalent variant;
  - associated differential test.

  This lets them be mechanised later in Lean (level b), e.g. via a small verified certificate checker. A verified implementation (level c) is out of scope.
- **Scope:** existential rules first, then stratified negation (an existing external module to integrate), then aggregation. Uncertainty and time are out of scope for now.
- **Target domains:** enterprise / complex business-domain modelling (small-to-medium KBs), later aerospace/defense (potentially large KBs).

## Clarifications

- RDFox has no existential rule heads: it uses the `rdfox:SKOLEM` built-in, i.e. a hand-controlled Skolem chase.
- Warded Datalog+- comes from Arenas, Gottlob & Pieris (PODS 2014) and Gottlob & Pieris (IJCAI 2015). It is implemented in Vadalog (VLDB 2018, now Prometheux) and is not used by RDFox.
- No existing system combines decidability analysis, automatic algorithm selection and high performance: this is the differentiator.

## Key theory points

- Membership in FES / FUS / BTS is undecidable, so use a portfolio of sufficient tests per GRD SCC.
- Three completeness statuses: static proof, dynamic (fixpoint reached), not guaranteed.
- Logical equivalence of rule sets reduces to entailment, which is decidable within decidable classes.
- Some transformations are true logical equivalences; others give only conservative extensions (see [report 07](07-sota-theory.md)).
- Recompute the GRD after every transformation.
- Default semantics candidate: negation and aggregation only on null-free positions (independent of the chase variant); Skolem perfect-model semantics as opt-in.
- The Skolem chase serves as the materialisation backend, enabling FBF / B-F incremental maintenance.
- Open research gaps: incremental restricted/core chase; breaking GRD SCCs under logical equivalence.

## Legal note

Graal was written under an employment contract, so copyright is likely held by the former employer(s), under CeCILL 2.1. Porting code creates a derivative work under CeCILL; re-implementing algorithms from publications avoids that. To be validated by legal counsel.

## Next steps

1. Phase 0: Graal as oracle (Java 21 green, drop the OWL/Neo4j/SQL modules, collect a test KB corpus).
2. Phase 1a: specification, resolving the 20 decisions of [report 07](07-sota-theory.md).
3. Phase 1b: language spike (Kotlin vs Rust, see E6).
4. Later phases: core, rewriting, negation, aggregation, benchmarks.
