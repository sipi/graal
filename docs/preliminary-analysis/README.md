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
- **E8** Rule-set optimisation pass:
  - the original rule set is always kept;
  - the rewritten set is logically equivalent (query-answer equivalence may come later, low priority);
  - every transformation is backed by a formal proof of logical equivalence;
  - full traceability back to the original rules (explanations cite original rules).
- **Scope:** existential rules first, then stratified negation (an existing external module to integrate), then aggregation. Uncertainty and time are out of scope for now.
- **Target domains:** enterprise / complex business-domain modelling (small-to-medium KBs), later aerospace/defense (potentially large KBs).

## Legal note

Graal was written under an employment contract, so copyright is likely held by the former employer(s), under CeCILL 2.1. Porting code creates a derivative work under CeCILL; re-implementing algorithms from publications avoids that. To be validated by legal counsel.

## Next steps

1. State of the art on alternatives and their strong ideas.
2. Theory survey: semantics, equivalence-preserving transformations, decidability classes (including warded).
3. Kotlin vs Rust decision.
4. Phase 0: Graal as oracle.
