# Decisions D1-D19

The decisions taken by the project owner, with their rationale, consequences and the pages they govern. The [README Decisions section](../preliminary-analysis/README.md#decisions) is **authoritative**: if this page and the README ever differ, the README wins and the difference must be reported.

> **Status in this project:** `v0` `F2` — decisions in force as of 2026-10-01.
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

Decisions recorded before D1 (in the README *Options* table): **option B chosen** (new core guided by Graal, with Graal as test oracle on the fragment where it is valid); refurbishing Graal (A), adopting Nemo as the core (D) and adopting InteGraal (E) rejected; Nemo kept as optional backend / second oracle; language open (C, E6).

| # | Date | Topic | Amended by |
|---|---|---|---|
| [D1](#d1) | 2026-09-30 | v1 framework F2, three term kinds | |
| [D2](#d2) | 2026-09-30 | lookup-before-invent default | refined by D14 |
| [D3](#d3) | 2026-09-30 | `T(P)` as the bridge | |
| [D4](#d4) | 2026-09-30 | rule-transformation deliverable postponed | |
| [D5](#d5) | 2026-09-30 | rewriting under negation (hybrid), stored-rewriting guard | D15 (guard), D16 (timing) |
| [D6](#d6) | 2026-10-01 | v0 = plain positive Datalog | |
| [D7](#d7) | 2026-10-01 | rounding chosen by the modeller | **D13** |
| [D8](#d8) | 2026-10-01 | late materialisation of Skolem terms | **D10** |
| [D9](#d9) | 2026-10-01 | labelled nulls in F2: reservation, input rejected | |
| [D10](#d10) | 2026-10-01 | distinct Skolem terms denote distinct individuals in v1 | |
| [D11](#d11) | 2026-10-01 | functional terms allowed in input; output encoding parsed back | |
| [D12](#d12) | 2026-10-01 | four completeness statuses | |
| [D13](#d13) | 2026-10-01 | five rounding modes, no default | |
| [D14](#d14) | 2026-10-01 | lookup source: any predicate if stratifiable | |
| [D15](#d15) | 2026-10-01 | strict guard on pre-computed rewriting | |
| [D16](#d16) | 2026-10-01 | incremental strategy: v0/v1 pure chase | |
| [D17](#d17) | 2026-10-01 | working principle: cap first, then small verified steps (= E13) | |
| [D18](#d18) | 2026-10-01 | rounding modes match mainstream languages and are explicitly documented; `round` = half away from zero | refines D13 |
| [D19](#d19) | 2026-10-01 | COMPLETE-STATIC requires a formal proof; functional terms excluded at first | |

<a id="d1"></a>
## D1 Framework for v1: F2 (2026-09-30)

- **Decision.** The v1 theoretical framework is **F2**: stratified Datalog with **named Skolem functions**, perfect-model semantics, exact decimals ([report 09 §6](../preliminary-analysis/09-skolem-function-frameworks.md)). The term model has **three term kinds** from day one: constant, named functional term, labelled null. This allows evolution towards F3 (hybrid with existential variables).
- **Rationale.** F2 matches the owner's reading of the running examples (identity of "the manager", determinism, closed world over invented objects). It has mature logic-programming semantics and is the cheapest to build and test. It has exact oracles (clingo/DLV), plus Graal via `T(P)` (report 09 §6 comparison).
- **Consequences.** No existential variables in v1 syntax; labelled nulls exist only in the term model ([D9](#d9)). Formal definition: [report 11](../preliminary-analysis/11-f2-framework-definition.md), validated on 2026-10-01 except OP-3.
- **Domain background.** [Skolem functions and terms](../domain/concepts/skolem-functions-and-terms.md), [perfect-model semantics](../domain/concepts/perfect-model-semantics.md), [labelled nulls](../domain/concepts/labelled-nulls.md). **Project pages.** [architecture principles](architecture-principles.md).

<a id="d2"></a>
## D2 Lookup-before-invent is the default for named functions (2026-09-30)

- **Decision.** A function value is taken from data when recorded, and invented only otherwise. User-declared functional dependencies are **integrity constraints** (checked, violations reported). They are never used for equality reasoning. The analyser must detect non-stratifiable programs created by this mechanism (a value that is itself derived through the function).
- **Rationale.** Avoids equality reasoning (EGDs) and its undecidability, while matching LogicBlox-style constructor semantics (report 09 §1.3, §4).
- **Consequences.** Report 11 §1.5 and §3.1 define it by a translation `lb(K)` into stratified negation. This interacts with D5 and D15: every query touching a lookup-declared function is excluded from stored rewriting. Lookup source: [D14](#d14). Several recorded values for one argument: **OP-3, not decided** ([open questions](open-questions.md#op-3)).
- **Domain background.** [value-invention strategies](../domain/concepts/value-invention-strategies.md), [equality and UNA](../domain/concepts/equality-and-una.md), [explanations and diagnostics](../domain/concepts/explanations-and-diagnostics.md).

<a id="d3"></a>
## D3 The function-graph translation T(P) is the bridge (2026-09-30)

- **Decision.** The translation `T(P)` of [report 09 §1.2](../preliminary-analysis/09-skolem-function-frameworks.md) is accepted as the bridge between existential and Skolem readings (an approach already known to the owner).
- **Consequences.** It imports existential-rule decidability results and makes Graal an exact oracle on a fragment (report 11 §9).
- **Domain background.** [Skolemisation and function-graph translations](../domain/concepts/skolemisation-and-function-graph-translations.md), [benchmarks and test oracles](../domain/evaluation/benchmarks-and-test-oracles.md). **Project pages.** [test strategy](test-strategy.md).

<a id="d4"></a>
## D4 Rule-transformation deliverable postponed (2026-09-30)

- **Decision.** The former deliverable "10" (rule transformations and proof sheets) is postponed: it is not a prerequisite for a first prototype.
- **Consequences.** E8, E9 and E12 remain requirements, but no transformation is implemented before the deliverable exists.
- **Domain background.** [rule-set transformations and equivalence](../domain/algorithms/rule-set-transformations-and-equivalence.md), [equivalence notions](../domain/concepts/equivalence-notions.md).

<a id="d5"></a>
## D5 Query rewriting in presence of negation (2026-09-30)

- **Decision.** Rewrite **stratum by stratum**. Negated literals are evaluated against lower strata materialised by the chase (hybrid). v1 guard: **pre-computed** rewriting only for queries that depend on no negation, directly or transitively. Pre-computed rewritings are invalidated when rules change. See [report 11 §8.4](../preliminary-analysis/11-f2-framework-definition.md).
- **Refinements.** The guard counts negation, aggregates and lookup-induced edges ([D15](#d15)). The hybrid per-stratum rewriting stays defined in report 11 but is **not implemented in v1** ([D16](#d16)).
- **Domain background.** [hybrid strategies](../domain/algorithms/hybrid-strategies.md), [query rewriting](../domain/algorithms/query-rewriting.md).

<a id="d6"></a>
## D6 First engine scope (v0): plain positive Datalog (2026-10-01)

- **Decision.** v0 = plain positive Datalog: no existential variables, no Skolem functions, no negation (and therefore no aggregation).
- **Rationale.** Avoid blocking on the open theoretical questions of value invention ([Q1](open-questions.md#q1)).
- **v0 is not throwaway.** The analyser will detect when a formalisation falls in this fragment and dispatch it to a specialised, faster algorithm. The architecture must anticipate F2 (three term kinds, strata), so that v0 **extends** rather than gets rewritten.
- **Domain background.** [Datalog](../domain/concepts/datalog.md), [semi-naive evaluation](../domain/algorithms/semi-naive-evaluation.md). **Project pages.** [roadmap](roadmap.md), [architecture principles](architecture-principles.md).

<a id="d7"></a>
## D7 Rounding chosen by the modeller (2026-10-01) — amended by D13

- **Decision (as recorded).** Rounding is chosen by the human modeller; three modes: `floor`, `round` (half-up), `bank_round` (half-even). It superseded the single default of report 11 OP-7.
- **Amended by [D13](#d13):** five modes, explicit choice, no default.

<a id="d8"></a>
## D8 Late materialisation of Skolem terms (direction for F2, not v0) (2026-10-01) — amended by D10

- **Decision (direction).** Keep Skolem terms symbolic, with their creation context, during reasoning. Turn them into output identifiers only when results are returned. This keeps the Skolem chase order-independent.
- **Owner's caveat.** Distinct Skolem terms (e.g. `manager(Tom)`, `manager(Anna)`) might denote the same individual. This is not theoretically neutral (unique-name assumption vs equality; impact on counting and aggregates).
- **Amended by [D10](#d10):** in v1, distinct Skolem terms denote distinct individuals. The caveat now describes the **co-reference option**, anticipated in the architecture and activatable later.
- **Domain background.** [Skolem functions and terms](../domain/concepts/skolem-functions-and-terms.md), [equality and UNA](../domain/concepts/equality-and-una.md), [aggregation](../domain/concepts/aggregation.md).

<a id="d9"></a>
## D9 Labelled nulls in F2: a pure reservation (2026-10-01)

- **Decision.** Labelled nulls are a pure reservation in the term model. An input containing a labelled null is **rejected**. Accepting RDF blank nodes as input is a future lead, to be adopted only after an impact study (report 11 OP-13).
- **Domain background.** [labelled nulls](../domain/concepts/labelled-nulls.md), [RDF and SPARQL](../domain/adjacent/rdf-and-sparql.md).

<a id="d10"></a>
## D10 Identity of Skolem terms (2026-10-01)

- **Decision.** In v1, distinct Skolem terms denote distinct individuals (`manager(tom) ≠ manager(anna)`). Allowing distinct terms to co-refer is logically legitimate but complicates algorithms. It becomes an **option**, anticipated in the architecture (e.g. term ids with an indirection to a class representative) and activatable later.
- **Consequences.** Resolves the tension between D8 and report 11 §1.3/§4.1 (former inconsistency I7). How the option articulates with `@lookup` and with D11 is open: **OP-23**.
- **Domain background.** [equality and UNA](../domain/concepts/equality-and-una.md).

<a id="d11"></a>
## D11 Functional terms in input data (2026-10-01)

- **Decision.** Functional terms may appear in input data. The output encoding of Skolem terms (e.g. a `skolem:` prefix with the function name, normalised argument values and unambiguous delimiters; exact syntax to be specified) is parsed back into structured terms. This guarantees the round-trip for AI agents that store answers and send them back. Termination analysis must account for functional terms present in the data. Supersedes the default of report 11 OP-4.
- **Domain background.** [Skolem functions and terms](../domain/concepts/skolem-functions-and-terms.md), [chase termination](../domain/concepts/chase-termination.md) (critical instance).

<a id="d12"></a>
## D12 Four completeness statuses (2026-10-01)

- **Decision.** Four statuses: **COMPLETE-STATIC** (static proof), **COMPLETE-DYNAMIC** (fixpoint reached), **NOT-GUARANTEED** (returned answers sound, absent answers unknown), **UNKNOWN** (exposed result; nothing returned, see [report 11 §6.3](../preliminary-analysis/11-f2-framework-definition.md) and its normative rule N1). Supersedes the list of three statuses (former inconsistency I2).
- **Domain background.** [soundness and completeness of partial results](../domain/concepts/soundness-and-completeness-of-partial-results.md).

<a id="d13"></a>
## D13 Rounding: five modes, no default (2026-10-01)

- **Decision.** Five modes: `floor`, `ceil`, `truncate`, `round` (half-up) and `bank_round` (half-even). The modeller must always choose the mode explicitly; **there is no default**. Amends D7; supersedes the defaults of report 11 OP-6/OP-7 where they conflict (former inconsistency I1). Division requires a mode (OP-6).
- **Tie direction of `round` on negatives:** settled by [D18](#d18).
- **Domain background.** [exact decimals and rounding](../domain/concepts/exact-decimals-and-rounding.md).

<a id="d14"></a>
## D14 Lookup source (2026-10-01)

- **Decision.** The lookup source of a named function may be any predicate (base or derived), provided the whole rule set remains stratifiable. This is checked, and a rejection names the cycle. The `p@db` sugar is postponed: validate without it first (report 11 OP-2).
- **Domain background.** [value-invention strategies](../domain/concepts/value-invention-strategies.md), [stratified negation](../domain/concepts/stratified-negation.md).

<a id="d15"></a>
## D15 Strict guard on pre-computed rewriting (2026-10-01)

- **Decision.** The guard of D5 stays strict: negation, aggregates and lookup-induced edges all count (report 11 OP-16).

<a id="d16"></a>
## D16 Incremental strategy (2026-10-01)

- **Decision.** v0/v1 = **pure chase only** (no rewriting, no backward chaining). Rewriting or backward chaining comes next, then hybrid strategies. The hybrid per-stratum rewriting (D5) remains defined in report 11 but is not implemented in v1 (report 11 OP-17, E10).
- **Domain background.** [chase variants](../domain/algorithms/chase-variants.md), [semi-naive evaluation](../domain/algorithms/semi-naive-evaluation.md).

<a id="d17"></a>
## D17 Working principle (2026-10-01)

- **Decision** (also requirement [E13](requirements.md#e13)). First define the target (the "cap"). Then follow a plan that reaches it in small, iterative, incremental steps, each verifiable and validated.

<a id="d18"></a>
## D18 Rounding semantics (2026-10-01)

- **Decision.** Each rounding mode must behave like its counterpart in mainstream programming languages, and its behaviour must always be explicitly documented, including negative numbers and ties. Refines [D13](#d13).
- **Why a resolution is needed.** Languages disagree on `round` for ties on negatives. Away from zero (`round(-2.5) = -3`): C `round`, PHP `round` (default `PHP_ROUND_HALF_UP`), Excel `ROUND`, COBOL `ROUNDED`, Java `BigDecimal` `HALF_UP`, and `ROUND` on exact numerics in PostgreSQL and MySQL. Towards +∞ (`-2.5 → -2`): Java `Math.round`, JavaScript `Math.round`. To even: Python 3 `round`.
- **Resolution.** `round` = half away from zero. `bank_round` = half to even (Python 3, IEEE 754 default, Java `HALF_EVEN`). `floor` (towards −∞), `ceil` (towards +∞) and `truncate` (towards 0) as usual.
- **Consequences.** [Report 11 §4.3](../preliminary-analysis/11-f2-framework-definition.md) gives, for each mode, a table of results on 2.5, 3.5, -2.5, -3.5, 2.4, -2.6 and the corresponding function in C, Java, JavaScript, Python, PHP, COBOL and SQL; these become conformance tests. Closes the tie flag of [OP-7](open-questions.md#op-7).
- **Domain background.** [exact decimals and rounding](../domain/concepts/exact-decimals-and-rounding.md).

<a id="d19"></a>
## D19 Static completeness requires a proof (2026-10-01)

- **Decision.** COMPLETE-STATIC requires a **formal proof** of decidability/termination. Start with simple cases and improve the detection of decidable cases over time. In the first versions, any unit whose rules contain functional terms (in bodies, or in data per [D11](#d11)) is excluded from COMPLETE-STATIC; at best it is COMPLETE-DYNAMIC.
- **Consequences.** Confirms the conservative rule of [report 11 §7.1](../preliminary-analysis/11-f2-framework-definition.md). A new decidable case enters the certified class only with its proof.

## Corrections (2026-10-01)

- The running example **E3-ex** is `employee(x) ∧ not isCompanyDirector(x) → ∃y managerOf(y, x)`: default negation on a data predicate, meant to stop the manager chain at the company director. See [running examples](running-examples.md#e3-ex).
- Reports 09 and 11 illustrate E3-ex with `hasManager` (and `recordedManager` lookup). These remain valid illustrations of named functions and of D2, but are not the owner's rule.
- [Report 12](../preliminary-analysis/12-invention-under-negation.md) was built on a misreading (`hasBoss`, i.e. lookup-before-invent). Its variants remain valid test scenarios but do not model the owner's rule.
- With pure Skolem terms the chain does not stop at the director (an invented term is never equal to a constant), unless equality between invented terms and constants is supported. See [Q1](open-questions.md#q1).

## Terminology (owner decision, README)

"Labelled null" names the objects produced in facts for unknown individuals. "Existential variable" is used only for rule syntax. "Existential witness" is only an explanatory gloss. See [domain conventions §5](../domain/conventions.md#5-notation).

## What is NOT decided

- **OP-3** (several recorded values for one argument): pending [report 14](../preliminary-analysis/14-uniqueness-and-functionality.md). The default "use all values and report a violation", with an optional strict mode, is a provisional proposal only.
- **OP-23** (articulation of the D10 co-reference option with `@lookup` and D11).
- **Q1** leads A-C (infinite invention chains).
- Language (E6); concrete syntax; output encoding syntax of Skolem terms (D11); where the new code lives; licence of the new code.

See [open questions](open-questions.md).

## Related pages

- [README (start here)](README.md), [requirements](requirements.md), [open questions](open-questions.md), [roadmap](roadmap.md).

## References

- [README Decisions, Corrections, Options](../preliminary-analysis/README.md#decisions) (authoritative).
- [Report 09 §6](../preliminary-analysis/09-skolem-function-frameworks.md) (F1/F2/F3), [report 11](../preliminary-analysis/11-f2-framework-definition.md) (F2 definition, validated except OP-3; §11 lists every open point and its resolution), [report 12](../preliminary-analysis/12-invention-under-negation.md).
