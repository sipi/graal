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
| 11 | [F2 framework definition](11-f2-framework-definition.md) | **VALIDATED by owner on 2026-10-01, except OP-3 (pending report 14)**; see D9-D19. Formal definition of the v1 framework F2: syntax, lookup-before-invent translation, stratification, perfect-model semantics with exact decimals, reasoning tasks, soundness under non-termination and completeness statuses, termination portfolio, strategies (incl. hybrid rewriting, D5), relation to existential rules, worked examples, open points. |
| 12 | [Invention under negation](12-invention-under-negation.md) (see Corrections) | Value invention correlated with negation: behaviour across approaches, test scenarios. Covers `employee(x), not hasBoss(x) → ∃y managerOf(y,x)` and variants under F2, restricted/Skolem chase, stable models and well-founded semantics, checked with clingo, a chase simulator and Nemo. Also covers the restricted chase as an implicit self-negation, recommends rejecting self- and cross-defeating invention, and gives 14 test scenarios. |
| 13 | [Apache Jena rule engine](13-jena-rule-engine.md) | Apache Jena rule engine: hybrid forward/backward reasoning, makeSkolem and noValue builtins, behaviour on the running examples. |
| 14 | [Uniqueness and functionality](14-uniqueness-and-functionality.md) | Uniqueness and functionality: UNA vs co-reference, conflicting recorded values, inconsistency-tolerant semantics — in progress (input for OP-3). |

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
  - Decision via a reduced, time-boxed spike: 2 weeks in Kotlin + 3 weeks in Rust. (Editorial note, 2026-10-01: [report 08](08-kotlin-vs-rust.md) §8 Stage 1 proposes 3 + 4 weeks; the 2 + 3 weeks stated here are authoritative.) Scope: data model, homomorphism with bi-connected components, GRD + SCC chase, and benchmarks (LUBM, 1,000 small KBs, compared with Nemo and Graal). MCP/Python/WASM integration is assessed on documentation only.
  - Go/no-go: Rust if it reaches feature parity with at most 1.6x the Kotlin hours, otherwise Kotlin. If both pass: Rust if defense prospects are real, Kotlin if the next 18 months are enterprise-only.
  - Avoid a hybrid Rust kernel + Kotlin outer layer: the boundary would cut through the homomorphism hot loop used by rewriting.
  - Editorial note, 2026-10-01: [report 05](05-nemo-and-rust-option.md) recommends a Kotlin core and [report 08](08-kotlin-vs-rust.md) a Rust core conditional on the spike. Report 05's recommendation is historical; report 08 is the most recent analysis; the language choice remains open.
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
- **E13** Working principle (= D17): first define the target (the "cap"), then a plan that reaches it in small, iterative, incremental steps, each of which is verifiable and validated.
- **Scope** *(superseded by D1 and D6: v0 is plain positive Datalog, then F2 with named functions, stratified negation and aggregation; kept for history)*: existential rules first, then stratified negation (an existing external module to integrate), then aggregation. Uncertainty and time are out of scope for now.
- **Target domains:** enterprise / complex business-domain modelling (small-to-medium KBs), later aerospace/defense (potentially large KBs).

## Clarifications

- RDFox has no existential rule heads: it uses the `rdfox:SKOLEM` built-in, i.e. a hand-controlled Skolem chase.
- Warded Datalog+- comes from Arenas, Gottlob & Pieris (PODS 2014) and Gottlob & Pieris (IJCAI 2015). It is implemented in Vadalog (VLDB 2018, now Prometheux) and is not used by RDFox.
- No existing system combines decidability analysis, automatic algorithm selection and high performance: this is the differentiator.

## Key theory points

- Membership in FES / FUS / BTS is undecidable, so use a portfolio of sufficient tests per GRD SCC.
- Four completeness statuses (D12): COMPLETE-STATIC (static proof), COMPLETE-DYNAMIC (fixpoint reached), NOT-GUARANTEED, UNKNOWN (exposed result, see [report 11](11-f2-framework-definition.md) §6.3). The earlier list of three statuses is superseded.
- Logical equivalence of rule sets reduces to entailment, which is decidable within decidable classes.
- Some transformations are true logical equivalences; others give only conservative extensions (see [report 07](07-sota-theory.md)).
- Recompute the GRD after every transformation.
- *(Superseded by D1: F2, i.e. Skolem perfect-model semantics, is the v1 framework; kept for history.)* Default semantics candidate: negation and aggregation only on null-free positions (independent of the chase variant); Skolem perfect-model semantics as opt-in.
- The Skolem chase serves as the materialisation backend, enabling FBF / B-F incremental maintenance.
- Open research gaps: incremental restricted/core chase; breaking GRD SCCs under logical equivalence.

## Legal note

Graal was written under an employment contract, so copyright is likely held by the former employer(s), under CeCILL 2.1. Porting code creates a derivative work under CeCILL; re-implementing algorithms from publications avoids that. To be validated by legal counsel.

## Running business examples

- **E1-ex** "If no specific condition applies then general conditions apply": stratified negation, closed-world.
- **E2-ex** "Basket > 200€ ⇒ free delivery": sum aggregate, comparison, exact decimals.
- **E3-ex** "Every employee has a line manager" (exact rule: see Corrections): existential vs named Skolem function. With "every manager is an employee", the Skolem chase does not terminate; this is the first test case for the analyser.

## Decisions

Recorded 2026-09-30:

- **D1** Theoretical framework for v1: F2, i.e. stratified Datalog with named Skolem functions (perfect-model semantics, exact decimals), see [report 09](09-skolem-function-frameworks.md) §6. The term model is designed from day one with three term kinds (constant, named functional term, labelled null) to allow later evolution towards F3 (hybrid).
- **D2** "Lookup before invent" is the default for named functions: a function value is taken from data when recorded and invented only otherwise. User-declared functional dependencies are checked as integrity constraints, not used for equality reasoning. The analyser must detect non-stratifiable programs created by this mechanism (a value that is itself derived through the function).
- **D3** The function-graph translation T(P) ([report 09](09-skolem-function-frameworks.md) §1.2) is accepted as the bridge between existential and Skolem readings (approach already known to the project owner).
- **D4** The rule-transformation deliverable (former "10") is postponed: it is not a prerequisite for a first prototype.
- **D5** Query rewriting in presence of negation: rewrite stratum by stratum, negated literals being evaluated against lower strata materialised by the chase (hybrid). v1 guard: pre-computed rewriting only for queries that depend on no negation (directly or transitively). Pre-computed rewritings are invalidated when rules change. See [report 11](11-f2-framework-definition.md) §8.4.

Recorded 2026-10-01:

- **D6** First engine scope (v0): plain positive Datalog — no existential variables, no Skolem functions, no negation (and therefore no aggregation). Rationale: avoid blocking on the open theoretical questions of value invention. v0 is not throwaway: the analyser will detect when a formalisation falls in this fragment and dispatch it to a specialised, faster algorithm. The architecture must still anticipate F2 (three term kinds, strata) so that v0 extends rather than gets rewritten.
- **D7** Rounding is chosen by the human modeller; three modes must be available: `floor`, `round` (half-up) and `bank_round` (half-even). This supersedes the single default proposed in [report 11](11-f2-framework-definition.md) open point 7. *(Amended by D13: five modes, no default.)*
- **D8** Late materialisation of Skolem terms (direction for F2, not v0): keep Skolem terms symbolic, with their creation context, during reasoning, and turn them into output identifiers only when results are returned; this keeps the Skolem chase order-independent. Caveat recorded by the owner: it requires accepting that two distinct Skolem terms (e.g. `manager(Tom)`, `manager(Anna)`) may denote the same individual, which is not theoretically neutral (unique-name assumption vs equality; impact on counting and aggregates). *(Amended by D10: in v1 distinct Skolem terms denote distinct individuals; this caveat now describes the co-reference option, anticipated in the architecture and activatable later.)*

Recorded 2026-10-01 (owner validation of [report 11](11-f2-framework-definition.md)):

- **D9** Labelled nulls in F2: a pure reservation in the term model; an input containing a labelled null is rejected. Accepting RDF blank nodes as input is a future lead, to be adopted only after an impact study. (Report 11 OP-13.)
- **D10** Identity of Skolem terms: in v1, distinct Skolem terms denote distinct individuals (`manager(tom) ≠ manager(anna)`). Allowing distinct terms to co-refer is logically legitimate but complicates algorithms: it becomes an OPTION, anticipated in the architecture (e.g. term ids with an indirection to a class representative) and activatable later. Resolves the tension between D8 and report 11 §1.3/§4.1; D8's caveat now describes this option.
- **D11** Functional terms may appear in input data. The output encoding of Skolem terms (e.g. a `skolem:` prefix with the function name, normalised argument values and unambiguous delimiters; exact syntax to be specified) is parsed back into structured terms. This guarantees the round-trip for AI agents that store answers and send them back. Termination analysis must account for functional terms present in the data. (Supersedes the default of report 11 OP-4.)
- **D12** Four completeness statuses: COMPLETE-STATIC, COMPLETE-DYNAMIC, NOT-GUARANTEED, UNKNOWN ([report 11](11-f2-framework-definition.md) §6.3).
- **D13** Rounding: five modes, `floor`, `ceil`, `truncate`, `round` (half-up) and `bank_round` (half-even). The modeller must always choose the mode explicitly; there is no default. Amends D7; supersedes the defaults of report 11 OP-6/OP-7 where they conflict.
- **D14** Lookup source: any predicate (base or derived), provided the whole rule set remains stratifiable (checked; a rejection names the cycle). The `p@db` sugar is postponed: validate without it first. (Report 11 OP-2.)
- **D15** The guard on pre-computed rewriting stays strict: negation, aggregates and lookup-induced edges all count (report 11 OP-16).
- **D16** Incremental strategy (report 11 OP-17 and E10): v0/v1 = pure chase only (no rewriting, no backward chaining); then add rewriting or backward chaining; then hybrid. The hybrid per-stratum rewriting (D5) remains defined in report 11 but is not implemented in v1.
- **D17** Working principle (also requirement E13): first define the target (the "cap"), then a plan that reaches it in small, iterative, incremental steps, each verifiable and validated.
- **D18** Rounding semantics (2026-10-01): each rounding mode must behave like its counterpart in mainstream programming languages, and its behaviour must always be explicitly documented (negative numbers and ties included). Languages disagree on `round` for ties on negatives, so the resolution is: `round` = half away from zero (`round(-2.5) = -3`; C `round`, PHP `round`, Excel `ROUND`, COBOL `ROUNDED`, Java `BigDecimal` `HALF_UP`); `bank_round` = half to even (Python 3 `round`, IEEE 754 default, Java `HALF_EVEN`); `floor`, `ceil`, `truncate` as usual. Each mode is documented with a table of examples ([report 11 §4.3](11-f2-framework-definition.md)). Refines D13; closes the tie-on-negatives flag of OP-7.
- **D19** Static completeness (2026-10-01): COMPLETE-STATIC requires a **formal proof** of decidability/termination. Start with simple cases and improve the detection of decidable cases over time. In the first versions, any unit whose rules contain functional terms (in bodies, or in data per D11) is excluded from COMPLETE-STATIC (at best COMPLETE-DYNAMIC). Confirms the conservative rule of [report 11 §7.1](11-f2-framework-definition.md).
- **D20** Numeric functions (2026-10-01): every numeric function (`round` and all others) has exactly one semantics regardless of the numeric type of its arguments (integer, decimal, …); no behaviour depends on internal representation. For example, `round(2.5)`, `round(2.50)` and `round(5/2)` are identical for a given mode. Motivation: PostgreSQL/MySQL `ROUND` differs between numeric and floating-point types, which the owner judges very dangerous. The conformance suite must test it across all numeric types.
- **Notation and syntax (note, 2026-10-01).** The domain notation uses `not` for default negation and `¬` for classical negation; the engine's concrete input syntax must transpose `¬` to an ASCII form (to be specified).
- **Not decided: report 11 OP-3** (several recorded values for one argument). It raises the uniqueness problem; a dedicated study ([report 14](14-uniqueness-and-functionality.md)) is in progress. The default "use all values and report a violation", with an optional strict mode, is a provisional proposal only.
- **Terminology.** "Labelled null" (the standard term in databases and the chase literature) names the objects produced in facts; "existential variable" is used only for rule syntax; "existential witness" is only an explanatory gloss.

## Corrections

Recorded 2026-10-01:

- The running example E3-ex is the rule `employee(x) AND NOT isCompanyDirector(x) -> exists y managerOf(y, x)`: negation on a data predicate, intended to stop the manager chain at the company director, who is the only employee without a boss.
- Reports [09](09-skolem-function-frameworks.md) and [11](11-f2-framework-definition.md) illustrate E3-ex with `employee(X) -> hasManager(X, manager(X))` (no negation; in report 11 with lookup-before-invent on `recordedManager`). These remain valid illustrations of named functions and of D2, but are not the owner's rule.
- [Report 12](12-invention-under-negation.md) was built on a misreading (the negated predicate was interpreted as `hasBoss`, i.e. lookup-before-invent). Its variants remain valid test scenarios but do not model the owner's intended rule.
- With pure Skolem terms the chain does not stop at the director: an invented `manager(...)` term is never equal to the director constant, unless equality between invented terms and constants is supported. This links to the caveat of D8.

## Open questions and solution leads

Recorded 2026-10-01. These are open questions and candidate leads, NOT decisions.

### Q1 Infinite value-invention chains despite a "stopping" negation

- **Problem:** `employee(x), not isCompanyDirector(x) -> exists y managerOf(y, x)` together with `managerOf(y, x) -> employee(y)` recurses forever, with existential variables as well as with Skolem terms: invented individuals are never the director, so the negation stops nothing. The modeller's intent (stop at the director) cannot be expressed without equality between invented terms and constants (see D8 caveat).
- **Lead A, depth guard:** cap the length of such recursions. Sound but not complete (status NOT-GUARANTEED); if a negation sits above the cut chain, answers may become wrong, so the status is UNKNOWN (rule N1 of [report 11](11-f2-framework-definition.md), scenario V6b of [report 12](12-invention-under-negation.md)). Useful only as a safety net.
- **Lead B, pattern detection and blocking (a simple case of GBTS algorithms):** a recursive pattern attached only to invented individuals (Xn -managerOf-> Xn-1 -managerOf-> Xn-2 ...) is detectable; when a new link has the same type as an earlier one, stop expanding and keep a back-link, giving a finite representation of the infinite model.
  - Query answering must evaluate on the chain unfolded up to the query size to stay complete.
  - The type must include negated predicates: easy when they are data-only (an invented individual is never a director), harder when they can be derived for invented individuals.
  - Related: Vadalog isomorphism-based pruning ([report 06](06-sota-engines.md), idea 4), FDNC / finitary programs ([report 09](09-skolem-function-frameworks.md)).
  - Possible scope nuance to discuss later: general GBTS stays out of scope (E10), but blocking of simple recursive chains could enter F2. Potentially publishable.
- **Lead C, diagnostics to the modeller (owner: "high value"):** an engine that detects the pattern can tell the human or LLM modeller, e.g. "your rules imply an infinite chain of managers starting from Tom; did you mean it to stop at the company director?". For a formalisation layer used by AI agents, such diagnostics may be worth as much as the answers themselves. Candidate for a first-class output of the analyser (to be specified later).
- **Not relevant to v0** (positive Datalog, D6); to be revisited with F2.

## Next steps

Phase order decided by the project owner:

1. **Theoretical framework.** Deliverables:
   - 09 frameworks (done, validated: F2 chosen, see D1);
   - 10 rule transformations and proof sheets (postponed, see D4);
   - 11 framework definition document: syntax, semantics, reasoning tasks, completeness statuses (validated on 2026-10-01 except OP-3, see D9-D19: [report 11](11-f2-framework-definition.md), open points in §11);
   - 14 uniqueness and functionality (in progress: [report 14](14-uniqueness-and-functionality.md), input for OP-3).

   v0 (D6): tests, specification/architecture and prototype for the positive Datalog fragment first; F2 features follow.
   Strategies follow D16: pure chase first (v0/v1), then rewriting or backward chaining, then hybrid. Every phase follows D17/E13: define the target first, then reach it in small, verifiable, validated steps.
2. **Test scenarios and quality benchmark** (correct and complete results), built independently of the implementation. Oracles: Graal on the fragment where it is valid (see [report 09](09-skolem-function-frameworks.md) §1.4), clingo/DLV for negation/aggregation over invented terms.
3. **Software specifications and architecture.**
4. **Prototype** (Kotlin vs Rust decision). With agent-written code the criterion becomes: passes the conformance suite and the architecture remains reviewable by the owner; Rust's compiler-enforced safety is an extra argument.
