# Open questions

Everything that is **not decided**: the owner's open question Q1 and its solution leads, the open points of the F2 definition that remain open after the owner's validation (OP-3, OP-23), the points proposed by report 12, the language choice, and other open items. It also records the status of every report 11 open point and the resolution of the known inconsistencies I1-I8. Nothing open on this page is a decision; leads and recommended defaults are proposals awaiting the owner.

> **Status in this project:** `open` — sources: [README Q1](../preliminary-analysis/README.md#open-questions-and-solution-leads), [report 11 §11](../preliminary-analysis/11-f2-framework-definition.md), [report 12 §8](../preliminary-analysis/12-invention-under-negation.md).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

<a id="q1"></a>
## Q1 Infinite value-invention chains despite a "stopping" negation

**Problem.** E3-ex (corrected, see [running examples](running-examples.md#e3-ex)):

```prolog
managerOf(Y, X) :- employee(X), not isCompanyDirector(X).   % Y existential, or Y = manager(X) in F2
employee(Y)     :- managerOf(Y, X).                          % every manager is an employee
```

recurses forever, with existential variables as well as with Skolem terms: invented individuals are never the director (a constant), so the negation stops nothing. The modeller's intent ("stop at the director") cannot be expressed without equality between invented terms and constants (the co-reference option of [D10](decisions.md#d10), formerly the D8 caveat). **Not relevant to v0** ([D6](decisions.md#d6)); to be revisited with F2.

| Lead | Idea | Guarantee | Pages |
|---|---|---|---|
| **A, depth guard** | Cap the length of such recursions. | Sound but not complete (NOT-GUARANTEED, [D12](decisions.md#d12)); if a negation sits above the cut chain, the status is UNKNOWN (rule N1 of report 11, scenario V6b of report 12). Safety net only. | [D12](decisions.md#d12); domain: [soundness and completeness of partial results](../domain/concepts/soundness-and-completeness-of-partial-results.md) |
| **B, pattern detection and blocking** (a simple case of GBTS algorithms) | Detect a recursive pattern attached only to invented individuals; when a new link has the same type as an earlier one, stop and keep a back-link: a finite representation of the infinite model. Queries are evaluated on the chain unfolded up to the query size. The "type" must include negated predicates (easy if data-only). | Complete if the blocking condition is correct [U-own]. General GBTS stays out of scope (E10); blocking simple chains *could* enter F2 (to discuss; potentially publishable). | domain: [blocking and finite representations](../domain/algorithms/blocking-and-finite-representations.md), [decidability classes](../domain/concepts/decidability-classes.md) (FDNC, finitary) |
| **C, diagnostics to the modeller** (owner: "high value") | Detect the pattern and tell the human or LLM modeller: "your rules imply an infinite chain of managers starting from Tom; did you mean it to stop at the company director?". Candidate first-class output of the analyser, to be specified. | No change to semantics. | domain: [explanations and diagnostics](../domain/concepts/explanations-and-diagnostics.md) |

<a id="e6-language"></a>
## Language choice (E6)

Kotlin or Rust, decided by a time-boxed spike (2 weeks Kotlin + 3 weeks Rust, README). See [language choice](language-choice-kotlin-vs-rust.md).

## Report 11 open points: status after the owner's validation (2026-10-01)

[Report 11](../preliminary-analysis/11-f2-framework-definition.md) was **validated by the owner on 2026-10-01, except OP-3** (pending [report 14](../preliminary-analysis/14-uniqueness-and-functionality.md)). Its §11 is the authoritative table; summary:

| # | Topic | Status |
|---|---|---|
| <a id="op-1"></a>OP-1 | Functional terms in answers | default validated: mode per query, default `all`; output encoding per [D11](decisions.md#d11) |
| <a id="op-2"></a>OP-2 | Lookup source | [D14](decisions.md#d14): any predicate if the rule set stays stratifiable; `p@db` sugar postponed |
| <a id="op-3"></a>OP-3 | Several recorded values for one argument (D2 conflict) | **OPEN**: uniqueness problem, [report 14](../preliminary-analysis/14-uniqueness-and-functionality.md) in progress; "use all values and report a violation" (optional strict mode) is a provisional proposal only |
| <a id="op-4"></a>OP-4 | Functional terms in facts | [D11](decisions.md#d11): allowed; output encoding parsed back; termination analysis accounts for them |
| <a id="op-5"></a>OP-5 | Numeric identity | default validated: `integer ⊂ decimal`, `2 = 2.00`; presentation scale later |
| <a id="op-6"></a>OP-6 | Division | validated: partial exact `/` + explicit `div(…, s, mode)` with a mandatory mode ([D13](decisions.md#d13)) |
| <a id="op-7"></a>OP-7 | Default rounding mode | [D13](decisions.md#d13): five modes, no default; tie direction of `round` on negatives to confirm |
| <a id="op-8"></a>OP-8 | Numeric limit | default validated: `digits = 1000` budget |
| <a id="op-9"></a>OP-9 | Empty groups | default validated: grounded vs implicit groups, chosen syntactically |
| <a id="op-10"></a>OP-10 | Granularity of the soundness rule | default validated: unit level in v1; answer-level later |
| <a id="op-11"></a>OP-11 | What UNKNOWN returns | default validated: nothing + blocking units; opt-in preview never mixed with sound results |
| <a id="op-12"></a>OP-12 | Typing | default validated: soft typing |
| <a id="op-13"></a>OP-13 | Labelled nulls in F2 input | [D9](decisions.md#d9): reject |
| <a id="op-14"></a>OP-14 | Constant-answer pruning analysis | default validated: none in v1; research item |
| <a id="op-15"></a>OP-15 | Goal-directed decidability (FDNC, finitary) | default validated: dynamic only, once backward chaining exists ([D16](decisions.md#d16)); relates to Q1 lead B |
| <a id="op-16"></a>OP-16 | Scope of the D5 guard | [D15](decisions.md#d15): strict (negation + aggregates + lookup-induced edges) |
| <a id="op-17"></a>OP-17 | Hybrid rewriting in v1 | [D16](decisions.md#d16): v0/v1 pure chase only |
| <a id="op-18"></a>OP-18 | Invalidation granularity | default validated: any rule or declaration change (once stored rewriting exists) |
| <a id="op-19"></a>OP-19 | Default budgets | default validated: `depth = 16`, `rounds = 10⁴`, `time` as safety net; tests set budgets explicitly |
| <a id="op-20"></a>OP-20 | General integrity constraints | default validated: include |
| <a id="op-23"></a>OP-23 | Articulation of the D10 co-reference option with `@lookup` and with functional terms sent back in data (D11) | **OPEN** (raised by the validation) |

### Points proposed by report 12 (not part of report 11)

| # | Topic | Status |
|---|---|---|
| <a id="op-21"></a>OP-21 | `@invent-unless-exists` sugar; reject self-defeating invention in v1 (report 12 §7.4) | not decided; built on the `hasBoss` misreading, still valid as scenario |
| <a id="op-22"></a>OP-22 | Generalise report 11 Prop. 1 to "negation through invention", self/cross classification (report 12 §7.2) | not decided; feeds diagnostics |

Interactions noted at validation (report 11 §11): D2 + D5/D15 (every query touching a lookup-declared function loses stored rewriting); D2 + E3-ex (the natural `@lookup manager ← hasManager` is the case D2 rejects; hence a separate source predicate such as `recordedManager`); E1 + negation (with materialisation only in v1, programs with a non-certified recursive SCC below a negation return UNKNOWN); D10 + D2 + D11 (OP-23).

## Other open items

- Where the new code lives in this repository, and its licence ([licensing](licensing-and-provenance.md)).
- The concrete syntax (report 11's is provisional), including the output encoding of Skolem terms ([D11](decisions.md#d11)).
- Equality between invented terms and constants (Q1, co-reference option of [D10](decisions.md#d10)), and its impact on counting.
- Open research gaps (README key theory points): incremental restricted/core chase; breaking GRD SCCs under logical equivalence.
- Report 09 §7 lists ten research questions (publication candidates).

<a id="known-inconsistencies"></a>
## Known inconsistencies between sources

All eight inconsistencies recorded so far are **resolved**: three by owner decisions, five by editorial fixes in the README (2026-10-01). New ones are added here with the next free number and reported to the owner; they are never resolved silently.

| # | Sources | Inconsistency | Resolution |
|---|---|---|---|
| I1 | README D7 vs report 11 §1.3, §4.3, OP-7 | Rounding mode names, set of modes and existence of a default differed. | **Resolved by [D13](decisions.md#d13)**: five modes, no default; report 11 updated. |
| I2 | README key theory points vs report 11 §6.3 | Three vs four completeness statuses. | **Resolved by [D12](decisions.md#d12)**: four statuses. |
| I3 | README Corrections vs reports 09 and 11 | E3-ex illustrated with `hasManager` / `recordedManager` instead of the owner's rule. | **Editorial fix in README** (Corrections now cover reports 09 and 11; report 11 carries a note). |
| I4 | README E6 vs report 08 §8 | Spike duration 2 + 3 weeks vs 3 + 4 weeks. | **Editorial fix in README**: 2 + 3 weeks authoritative. |
| I5 | README key theory points vs D1 | "Default semantics candidate: null-free positions; Skolem semantics opt-in" predates D1. | **Editorial fix in README**: marked superseded by D1. |
| I6 | README scope line vs D1/D6 | "Existential rules first ..." predates D1 and D6. | **Editorial fix in README**: marked superseded by D1 and D6. |
| I7 | D8 vs report 11 §1.3, §4.1 | Distinct Skolem terms may co-refer (D8) vs free-constructor reading (report 11). | **Resolved by [D10](decisions.md#d10)**: distinct terms denote distinct individuals in v1; co-reference is a later option. |
| I8 | Report 05 vs report 08 | Kotlin core vs Rust core recommendation. | **Editorial fix in README**: report 05 historical, report 08 most recent; E6 stays open. |

## Related pages

- [decisions](decisions.md), [requirements](requirements.md), [roadmap](roadmap.md), [running examples](running-examples.md).

## References

- [README, Open questions and solution leads](../preliminary-analysis/README.md#open-questions-and-solution-leads).
- [Report 11 §11](../preliminary-analysis/11-f2-framework-definition.md); [report 12 §7-§8](../preliminary-analysis/12-invention-under-negation.md); [report 09 §7](../preliminary-analysis/09-skolem-function-frameworks.md).
