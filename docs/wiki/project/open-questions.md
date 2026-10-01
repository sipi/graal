# Open questions

Everything that is **not decided**: the owner's open question Q1 and its solution leads, the open points of the F2 draft (report 11 §11, plus those proposed by report 12), the language choice, and the known inconsistencies between sources. Nothing on this page is a decision; leads and recommended defaults are proposals awaiting the owner.

> **Status in this project:** `open` — sources: [README Q1](../../preliminary-analysis/README.md#open-questions-and-solution-leads), [report 11 §11](../../preliminary-analysis/11-f2-framework-definition.md), [report 12 §8](../../preliminary-analysis/12-invention-under-negation.md).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

<a id="q1"></a>
## Q1 Infinite value-invention chains despite a "stopping" negation

**Problem.** E3-ex (corrected, see [running examples](running-examples.md#e3-ex)):

```prolog
managerOf(Y, X) :- employee(X), not isCompanyDirector(X).   % Y existential, or Y = manager(X) in F2
employee(Y)     :- managerOf(Y, X).                          % every manager is an employee
```

recurses forever, with existential variables as well as with Skolem terms: invented individuals are never the director (a constant), so the negation stops nothing. The modeller's intent ("stop at the director") cannot be expressed without equality between invented terms and constants (D8 caveat). **Not relevant to v0** ([D6](decisions.md#d6)); to be revisited with F2.

| Lead | Idea | Guarantee | Pages |
|---|---|---|---|
| **A, depth guard** | Cap the length of such recursions. | Sound but not complete (NOT-GUARANTEED); if a negation sits above the cut chain, the status is UNKNOWN (rule N1 of report 11, scenario V6b of report 12). Safety net only. | [completeness statuses](../concepts/completeness-statuses.md) |
| **B, pattern detection and blocking** (a simple case of GBTS algorithms) | Detect a recursive pattern attached only to invented individuals; when a new link has the same type as an earlier one, stop and keep a back-link: a finite representation of the infinite model. Queries are evaluated on the chain unfolded up to the query size. The "type" must include negated predicates (easy if data-only). | Complete if the blocking condition is correct [U-own]. General GBTS stays out of scope (E10); blocking simple chains *could* enter F2 (to discuss; potentially publishable). | [blocking of recursive chains](../algorithms/blocking-of-recursive-chains.md), [decidability classes](../concepts/decidability-classes.md) (FDNC, finitary) |
| **C, diagnostics to the modeller** (owner: "high value") | Detect the pattern and tell the human or LLM modeller: "your rules imply an infinite chain of managers starting from Tom; did you mean it to stop at the company director?". Candidate first-class output of the analyser, to be specified. | No change to semantics. | [modeller diagnostics](../concepts/modeller-diagnostics.md) |

<a id="e6-language"></a>
## Language choice (E6)

Kotlin or Rust, decided by a time-boxed spike. See [language choice](../engineering/language-choice-kotlin-vs-rust.md). Note the spike duration inconsistency below.

## Open points of the F2 draft (report 11 §11)

Report 11 is a DRAFT under owner review. Each point has a recommended default in the report; **none is decided**. Summary (see the report for options):

| # | Topic | Recommended default in report 11 | Notes |
|---|---|---|---|
| <a id="op-1"></a>OP-1 | Functional terms in answers | mode per query, default `all` | |
| <a id="op-2"></a>OP-2 | Lookup source | arbitrary conjunction + `p@db` sugar | report 12 proposes a generalised `@base` view |
| <a id="op-3"></a>OP-3 | Several recorded values (D2 conflict) | use all values and report a violation | |
| <a id="op-4"></a>OP-4 | Functional terms in facts | forbidden in v1 | keeps critical-instance property |
| <a id="op-5"></a>OP-5 | Numeric identity | `integer ⊂ decimal`, `2 = 2.00` | presentation scale later |
| <a id="op-6"></a>OP-6 | Division | partial exact `/` + explicit `div(…, s, mode)` | mode names to align with D7 |
| <a id="op-7"></a>OP-7 | Default rounding mode | `half_up` | **superseded by [D7](decisions.md#d7)** |
| <a id="op-8"></a>OP-8 | Numeric limit | `digits = 1000` budget | |
| <a id="op-9"></a>OP-9 | Empty groups | grounded vs implicit groups, chosen syntactically | |
| <a id="op-10"></a>OP-10 | Granularity of the soundness rule | unit level in v1 | answer-level via provenance later |
| <a id="op-11"></a>OP-11 | What UNKNOWN returns | nothing + blocking units; opt-in preview | |
| <a id="op-12"></a>OP-12 | Typing | soft typing | |
| <a id="op-13"></a>OP-13 | Labelled nulls in F2 input | reject | |
| <a id="op-14"></a>OP-14 | Constant-answer pruning analysis | none in v1 | research item |
| <a id="op-15"></a>OP-15 | Goal-directed decidability (FDNC, finitary) | dynamic tabling completion only | relates to Q1 lead B |
| <a id="op-16"></a>OP-16 | Scope of the D5 guard | negation + aggregates + lookup-induced | report 12 T4: exempt EDB-only negation |
| <a id="op-17"></a>OP-17 | Hybrid rewriting in v1 | defer to v1.1 | |
| <a id="op-18"></a>OP-18 | Invalidation granularity | any rule change | |
| <a id="op-19"></a>OP-19 | Default budgets | `depth = 16`, `rounds = 10⁴`, `time` as safety net | |
| <a id="op-20"></a>OP-20 | General integrity constraints | include | |
| <a id="op-21"></a>OP-21 | `@invent-unless-exists` sugar (proposed by report 12 §7.4) | reject self-defeating invention in v1; sugar defined by rewrite later | built on the `hasBoss` misreading, still valid as scenario |
| <a id="op-22"></a>OP-22 | Generalise report 11 Prop. 1 to "negation through invention", self/cross classification (report 12 §7.2) | adopt | diagnostics |

Interactions flagged by report 11 for validation: D2 + D5 (every query touching a lookup-declared function loses stored rewriting); D2 + E3-ex (the natural `@lookup manager ← hasManager` is the case D2 rejects); E1 + negation (programs with a non-certified recursive SCC below a negation return UNKNOWN under materialisation).

## Other open items

- Where the new code lives in this repository, and its licence ([licensing](../legal/licensing-and-provenance.md)).
- The concrete syntax (report 11's is provisional).
- Equality between invented terms and constants (D8 caveat, Q1), and its impact on counting.
- Open research gaps (README key theory points): incremental restricted/core chase; breaking GRD SCCs under logical equivalence.
- Report 09 §7 lists ten research questions (publication candidates).

<a id="known-inconsistencies"></a>
## Known inconsistencies between sources

Reported, **not resolved**. The higher source in the [hierarchy](../main.md#source-of-truth-hierarchy) prevails until the owner decides.

| # | Sources | Inconsistency | Prevailing source for now |
|---|---|---|---|
| I1 | README D7 vs report 11 §1.3, §4.3, OP-7 | D7 names three modes `floor`, `round` (half-up), `bank_round` (half-even) and lets the modeller choose; report 11 still defines modes `half_up, half_even, down, up, floor, ceiling` with default `half_up`. Names, the set of modes and the existence of a default differ; behaviour on negatives (half-up vs half away from zero; floor vs down) is unspecified in D7. | D7 |
| I2 | README "Key theory points" vs report 11 §6.3 (and README Q1 lead A) | The README key points list **three** completeness statuses (static, dynamic, not guaranteed); report 11 has **four** (adds UNKNOWN for exposed units), and README Q1 lead A itself uses UNKNOWN. | report 11 (more specific, cited by README Q1), pending validation |
| I3 | README Corrections vs reports 09 and 11 | The corrected E3-ex is `employee(x) ∧ ¬isCompanyDirector(x) → ∃y managerOf(y,x)`. Report 09 uses `employee(X) -> hasManager(X, manager(X))` with no negation, and report 11 §10.3-10.4 uses `hasManager` with lookup-before-invent (`recordedManager`). The Corrections only mention report 12. Reports 09/11 examples remain valid as lookup scenarios but are not the owner's rule. | README Corrections |
| I4 | README E6 vs report 08 §8 | Spike duration: README says 2 weeks Kotlin + 3 weeks Rust; report 08 Stage 1 says 3 weeks Kotlin + 4 weeks Rust. | README |
| I5 | README "Key theory points" vs D1 | "Default semantics candidate: negation and aggregation only on null-free positions (Choice A); Skolem perfect-model semantics as opt-in" predates D1, which made F2 (Skolem perfect-model semantics) the v1 framework. | D1 |
| I6 | README "Scope" line vs D1/D6 | "Existential rules first, then stratified negation, then aggregation" predates D1 (no existential variables in F2) and D6 (v0 positive Datalog). | D1, D6 |
| I7 | D8 vs report 11 §1.3, §4.1 | D8 accepts that distinct Skolem terms may denote the same individual; report 11 reads ground terms as free constructors (distinct terms, distinct objects). D8 is a direction with an explicit caveat. | open (owner) |
| I8 | Report 05 vs report 08 | Report 05 recommends a Kotlin core; report 08 recommends Rust conditional on a spike. Already acknowledged in report 08 §0. | README E6 (open, spike) |

## Related pages

- [decisions](decisions.md), [requirements](requirements.md), [roadmap](roadmap.md), [running examples](running-examples.md).

## References

- [README, Open questions and solution leads](../../preliminary-analysis/README.md#open-questions-and-solution-leads).
- [Report 11 §11](../../preliminary-analysis/11-f2-framework-definition.md); [report 12 §7-§8](../../preliminary-analysis/12-invention-under-negation.md); [report 09 §7](../../preliminary-analysis/09-skolem-function-frameworks.md).
