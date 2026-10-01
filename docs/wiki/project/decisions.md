# Decisions D1-D8

The decisions taken by the project owner, with their rationale, consequences and the pages they govern. The [README Decisions section](../../preliminary-analysis/README.md#decisions) is **authoritative**: if this page and the README ever differ, the README wins and the difference must be reported.

> **Status in this project:** `v0` `F2` — decisions in force as of 2026-10-01.
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

Decisions recorded before D1 (in the README *Options* table): **option B chosen** (new core guided by Graal, with Graal as test oracle on the fragment where it is valid); refurbishing Graal (A), adopting Nemo as the core (D) and adopting InteGraal (E) rejected; Nemo kept as optional backend / second oracle; language open (C, E6).

<a id="d1"></a>
## D1 Framework for v1: F2 (2026-09-30)

- **Decision.** The v1 theoretical framework is **F2**: stratified Datalog with **named Skolem functions**, perfect-model semantics, exact decimals ([report 09 §6](../../preliminary-analysis/09-skolem-function-frameworks.md)). The term model has **three term kinds** from day one: constant, named functional term, labelled null, to allow evolution towards F3 (hybrid with existential variables).
- **Rationale.** F2 matches the owner's reading of the running examples (identity of "the manager", determinism, closed world over invented objects), has mature logic-programming semantics, is the cheapest to build and test, and has exact oracles (clingo/DLV) plus Graal via `T(P)` (report 09 §6 comparison).
- **Consequences.** No existential variables in v1 syntax; nulls exist only in the data model. Formal definition drafted in [report 11](../../preliminary-analysis/11-f2-framework-definition.md) (DRAFT).
- **Pages.** [Skolem functions](../concepts/skolem-functions-and-terms.md), [perfect-model semantics](../concepts/perfect-model-semantics.md), [labelled nulls](../concepts/labelled-nulls.md), [architecture principles](../engineering/architecture-principles.md).

<a id="d2"></a>
## D2 Lookup-before-invent is the default for named functions (2026-09-30)

- **Decision.** A function value is taken from data when recorded, and invented only otherwise. User-declared functional dependencies are **integrity constraints** (checked, violations reported), never used for equality reasoning. The analyser must detect non-stratifiable programs created by this mechanism (a value that is itself derived through the function).
- **Rationale.** Avoids equality reasoning (EGDs) and its undecidability, while matching LogicBlox-style constructor semantics (report 09 §1.3, §4).
- **Consequences.** Report 11 §1.5 and §3.1 define it by a translation `lb(K)` into stratified negation; this interacts with D5 (report 11 §11, "D2 + D5").
- **Pages.** [lookup-before-invent](../concepts/lookup-before-invent.md), [equality and UNA](../concepts/equality-and-una.md), [modeller diagnostics](../concepts/modeller-diagnostics.md).

<a id="d3"></a>
## D3 The function-graph translation T(P) is the bridge (2026-09-30)

- **Decision.** The translation `T(P)` of [report 09 §1.2](../../preliminary-analysis/09-skolem-function-frameworks.md) is accepted as the bridge between existential and Skolem readings (approach already known to the owner).
- **Consequences.** It imports existential-rule decidability results and makes Graal an exact oracle on a fragment (report 11 §9).
- **Pages.** [function-graph translation T(P)](../concepts/function-graph-translation-tp.md), [benchmarks and oracles](../engineering/benchmarks-and-test-oracles.md).

<a id="d4"></a>
## D4 Rule-transformation deliverable postponed (2026-09-30)

- **Decision.** The former deliverable "10" (rule transformations and proof sheets) is postponed: not a prerequisite for a first prototype.
- **Consequences.** E8, E9 and E12 remain requirements; their pages may be written as background, but no transformation is implemented before the deliverable exists.
- **Pages.** [rule-set simplification](../algorithms/rule-set-simplification.md), [equivalence notions](../concepts/equivalence-notions.md).

<a id="d5"></a>
## D5 Query rewriting in presence of negation (2026-09-30)

- **Decision.** Rewrite **stratum by stratum**, negated literals being evaluated against lower strata materialised by the chase (hybrid). v1 guard: **pre-computed** rewriting only for queries that depend on no negation, directly or transitively. Pre-computed rewritings are invalidated when rules change. See [report 11 §8.4](../../preliminary-analysis/11-f2-framework-definition.md).
- **Open refinements.** Whether aggregate and lookup-induced edges count as "negation" for the guard (report 11 OP-16, `[choice]`: all three); EDB-only negation (report 12 §8, T4).
- **Pages.** [hybrid strategies](../algorithms/hybrid-strategies.md), [precomputed rewriting](../algorithms/precomputed-rewriting.md).

<a id="d6"></a>
## D6 First engine scope (v0): plain positive Datalog (2026-10-01)

- **Decision.** v0 = plain positive Datalog: no existential variables, no Skolem functions, no negation (and therefore no aggregation).
- **Rationale.** Avoid blocking on the open theoretical questions of value invention ([Q1](open-questions.md#q1)).
- **v0 is not throwaway.** The analyser will detect when a formalisation falls in this fragment and dispatch it to a specialised, faster algorithm. The architecture must anticipate F2 (three term kinds, strata) so that v0 **extends** rather than gets rewritten.
- **Pages.** [Datalog](../concepts/datalog.md), [semi-naive evaluation](../algorithms/semi-naive-evaluation.md), [roadmap](roadmap.md), [architecture principles](../engineering/architecture-principles.md).

<a id="d7"></a>
## D7 Rounding chosen by the modeller; three modes (2026-10-01)

- **Decision.** Rounding is chosen by the human modeller. Three modes must be available: `floor`, `round` (half-up) and `bank_round` (half-even). This **supersedes** the single default of report 11 open point OP-7.
- **Open details.** Exact syntax; whether a default mode exists at all; behaviour of `floor` and `round` on negative numbers (report 11 lists `half_up` as "half away from zero" and also `down`/`floor`/`ceiling`). See [exact decimals and rounding](../concepts/exact-decimals-and-rounding.md) and [known inconsistencies](open-questions.md#known-inconsistencies).
- **Scope.** F2 (arithmetic is not in v0).

<a id="d8"></a>
## D8 Late materialisation of Skolem terms (direction for F2, not v0) (2026-10-01)

- **Decision (direction).** Keep Skolem terms symbolic, with their creation context, during reasoning; turn them into output identifiers only when results are returned. This keeps the Skolem chase order-independent.
- **Owner's caveat.** It requires accepting that two distinct Skolem terms (e.g. `manager(Tom)`, `manager(Anna)`) may denote the same individual. This is not theoretically neutral: unique-name assumption vs equality, impact on counting and aggregates. It is in tension with report 11 §1.3/§4.1 (distinct ground terms denote distinct objects); this tension is **open**.
- **Pages.** [Skolem functions](../concepts/skolem-functions-and-terms.md), [equality and UNA](../concepts/equality-and-una.md), [aggregation](../concepts/aggregation.md).

## Corrections (2026-10-01)

- The running example **E3-ex** is `employee(x) ∧ ¬isCompanyDirector(x) → ∃y managerOf(y, x)`: negation on a data predicate, meant to stop the manager chain at the company director. See [running examples](running-examples.md#e3-ex).
- [Report 12](../../preliminary-analysis/12-invention-under-negation.md) was built on a misreading (`hasBoss`, i.e. lookup-before-invent); its variants remain valid test scenarios but do not model the owner's rule.
- With pure Skolem terms the chain does not stop at the director (an invented term is never equal to a constant) unless equality between invented terms and constants is supported. Links to the D8 caveat and to [Q1](open-questions.md#q1).

## What is NOT decided

Language (E6); concrete syntax; report 11 as a whole (DRAFT) and all its `[choice]` items (OP-1 to OP-20, plus OP-21/22 proposed by report 12); Q1 leads A-C; where the new code lives in the repository; licence of the new code. See [open questions](open-questions.md).

## Related pages

- [requirements](requirements.md), [open questions](open-questions.md), [roadmap](roadmap.md).

## References

- [README Decisions, Corrections, Options](../../preliminary-analysis/README.md#decisions) (authoritative).
- [Report 09 §6](../../preliminary-analysis/09-skolem-function-frameworks.md) (F1/F2/F3), [report 11](../../preliminary-analysis/11-f2-framework-definition.md) (F2 definition, DRAFT), [report 12](../../preliminary-analysis/12-invention-under-negation.md).
