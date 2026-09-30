# Preliminary analysis: refurbish Graal or build a new core?

## Purpose

Evaluate whether to refurbish Graal (dependency upgrade, modernization, partial Kotlin conversion) in order to build a homemade but solid logical reasoning engine.

**Conclusion:** build a NEW core (language, Kotlin vs Rust, still under debate), using Graal as a test oracle (valid only on a fragment once named Skolem functions are used, see [report 09](09-skolem-function-frameworks.md) §1.4).

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
| 09 | [Skolem-function frameworks](09-skolem-function-frameworks.md) | Theoretical frameworks with named Skolem functions: function-graph translation T(P), transfer of decidability classes, equality options (lookup-before-invent), Graal-as-oracle boundaries, frameworks F1/F2/F3. |
| 11 | [F2 framework definition](11-f2-framework-definition.md) | **DRAFT — awaiting owner validation.** Formal definition of the v1 framework F2: syntax, lookup-before-invent translation, stratification, perfect-model semantics with exact decimals, reasoning tasks, soundness under non-termination and completeness statuses, termination portfolio, strategies (incl. hybrid rewriting, D5), relation to existential rules, worked examples, open points. |
| 12 | [Invention under negation](12-invention-under-negation.md) | Value invention correlated with negation: behaviour across approaches, test scenarios. Covers `employee(x), not hasBoss(x) → ∃y managerOf(y,x)` and variants under F2, restricted/Skolem chase, stable models and well-founded semantics, checked with clingo, a chase simulator and Nemo. Also covers the restricted chase as an implicit self-negation, recommends rejecting self- and cross-defeating invention, and gives 14 test scenarios. |

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
- **E10** Reasoning strategies, selected by the analyser: (a) forward chaining (chase / materialisation); (b) backward chaining, performed dynamically at query time; (c) query rewriting, which shares foundations with backward chaining but can be executed a priori on queries known in advance (e.g. pre-registered queries), saving considerable run time when the rewriting is bounded; (d) combinations of these. Pre-computed rewritings must be invalidated when the rule set changes. GBTS-specific algorithms are out of scope.
- **E11** Development model: 100% of the code is written by AI agents; theoretical choices and architecture are validated by the project owner; the conformance/quality test suite and benchmarks are built independently of (and before) the implementation.
- **E12** Rule-set simplification includes premise simplification using other rules (e.g. {a→b, a∧b→c} ≡ {a→b, a→c}); termination is guaranteed by a strictly decreasing measure (e.g. total body size).
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

## Running business examples

- **E1-ex** "If no specific condition applies then general conditions apply": stratified negation, closed-world.
- **E2-ex** "Basket > 200€ ⇒ free delivery": sum aggregate, comparison, exact decimals.
- **E3-ex** "Every employee has a line manager": existential vs named Skolem function. With "every manager is an employee", the Skolem chase does not terminate; this is the first test case for the analyser.

## Decisions

Recorded 2026-09-30:

- **D1** Theoretical framework for v1: F2, i.e. stratified Datalog with named Skolem functions (perfect-model semantics, exact decimals), see [report 09](09-skolem-function-frameworks.md) §6. The term model is designed from day one with three term kinds (constant, named functional term, labelled null) to allow later evolution towards F3 (hybrid).
- **D2** "Lookup before invent" is the default for named functions: a function value is taken from data when recorded and invented only otherwise. User-declared functional dependencies are checked as integrity constraints, not used for equality reasoning. The analyser must detect non-stratifiable programs created by this mechanism (a value that is itself derived through the function).
- **D3** The function-graph translation T(P) ([report 09](09-skolem-function-frameworks.md) §1.2) is accepted as the bridge between existential and Skolem readings (approach already known to the project owner).
- **D4** The rule-transformation deliverable (former "10") is postponed: it is not a prerequisite for a first prototype.
- **D5** Query rewriting in presence of negation: rewrite stratum by stratum, negated literals being evaluated against lower strata materialised by the chase (hybrid). v1 guard: pre-computed rewriting only for queries that depend on no negation (directly or transitively). Pre-computed rewritings are invalidated when rules change. See [report 11](11-f2-framework-definition.md) §8.4.

## Next steps

Phase order decided by the project owner:

1. **Theoretical framework.** Deliverables:
   - 09 frameworks (done, validated: F2 chosen, see D1);
   - 10 rule transformations and proof sheets (postponed, see D4);
   - 11 framework definition document: syntax, semantics, reasoning tasks, completeness statuses (drafted, awaiting owner validation: [report 11](11-f2-framework-definition.md), open points in §11).
2. **Test scenarios and quality benchmark** (correct and complete results), built independently of the implementation. Oracles: Graal on the fragment where it is valid (see [report 09](09-skolem-function-frameworks.md) §1.4), clingo/DLV for negation/aggregation over invented terms.
3. **Software specifications and architecture.**
4. **Prototype** (Kotlin vs Rust decision). With agent-written code the criterion becomes: passes the conformance suite and the architecture remains reviewable by the owner; Rust's compiler-enforced safety is an extra argument.
