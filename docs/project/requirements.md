# Requirements E1-E13

The requirements agreed between the project owner and the analysis phase, explained one by one: what each means, why it exists, how it constrains the design, and which pages implement it. The wording of the [README](../preliminary-analysis/README.md#requirements-agreed-so-far) is authoritative; this page only explains it.

> **Status in this project:** `v0` `F2` `later` — all requirements are in force; later decisions (D1-D17) refine them, see [decisions](decisions.md).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

Note on numbering: `E1`-`E13` are **requirements**; `E1-ex`, `E2-ex`, `E3-ex` are **running examples** (see [running examples](running-examples.md)). Do not confuse them.

<a id="e1"></a>
## E1 Always sound; complete when decidability is proven; otherwise an explicit status

- **Meaning.** Every returned answer must be a true consequence of the knowledge base (soundness). When termination / decidability is established, all answers are returned (completeness). Otherwise the result carries an explicit "completeness not guaranteed" status.
- **Why.** An AI agent or a business user must be able to trust each answer, and know when absence of an answer means "false" versus "not found".
- **Consequences.**
  - Completeness can be proved **statically** (analyser certificate) or observed **dynamically** (the fixpoint was reached), see the README key theory points.
  - With negation or aggregation, an incomplete lower stratum can make upper answers *unsound*. Hence the four statuses of [D12](decisions.md#d12): COMPLETE-STATIC, COMPLETE-DYNAMIC, NOT-GUARANTEED, UNKNOWN, and the normative rule N1 of [report 11 §6](../preliminary-analysis/11-f2-framework-definition.md) (never return facts of an "exposed" unit). Domain background: [soundness and completeness of partial results](../domain/concepts/soundness-and-completeness-of-partial-results.md).
- **Pages:** [decisions D12](decisions.md#d12); domain: [chase termination](../domain/concepts/chase-termination.md), [rule-set analysis tools](../domain/algorithms/rule-set-analysis-tools.md).

<a id="e2"></a><a id="e7"></a>
## E2 / E7 The decidability and complexity analyser is central

- **Meaning.** A static analyser classifies the rule set (per strongly connected component of the dependency graph) and **drives algorithm selection**: materialisation, rewriting, or hybrid.
- **Why.** This is the project's differentiator (README, Clarifications): no existing system combines decidability analysis, automatic selection and performance.
- **Consequences.** Membership in FES/FUS/BTS is undecidable, so the analyser runs a portfolio of sufficient tests per SCC; its output (labels, certificates, witnesses) is part of every result trace (report 11 §7.3). In v0 its first job is to recognise the plain Datalog fragment ([D6](decisions.md#d6)). In v0/v1 the only strategy is the chase ([D16](decisions.md#d16)): the analyser certifies termination and statuses; strategy selection grows with later strategies.
- **Pages:** domain: [rule-set analysis tools](../domain/algorithms/rule-set-analysis-tools.md), [decidability classes](../domain/concepts/decidability-classes.md), [GRD](../domain/algorithms/graph-of-rule-dependencies-grd.md); diagnostics: [explanations and diagnostics](../domain/concepts/explanations-and-diagnostics.md).

<a id="e3"></a>
## E3 Explicit, controlled semantics

- **Meaning.** The semantics is stated explicitly, not implied by an implementation: which chase variant, stratified negation, then aggregation, and how labelled nulls are treated.
- **Consequences.** [D1](decisions.md#d1) fixes the v1 semantics: F2 perfect-model semantics. [Report 11](../preliminary-analysis/11-f2-framework-definition.md) is the reference contract (validated on 2026-10-01 except OP-3). Any behaviour not covered by a written semantics is a specification bug.
- **Pages:** [perfect-model semantics](../domain/concepts/perfect-model-semantics.md), [chase variants](../domain/algorithms/chase-variants.md), [stratified negation](../domain/concepts/stratified-negation.md), [aggregation](../domain/concepts/aggregation.md).

<a id="e4"></a>
## E4 Must-have techniques

- **Meaning.** The engine must include: the graph of rule dependencies (GRD) computed with piece-unifiers; an SCC-driven chase; UCQ query rewriting in the style of PURE; homomorphism search exploiting the bi-connected components of the query.
- **Why.** These are Graal's signature techniques, known to the owner, and absent or partial in the alternatives (InteGraal lacks BCC homomorphism in its main path; Nemo lacks rewriting and GRD; reports 04, 05).
- **Note.** With F2 (no existential variables), piece-unifiers reduce to classical unification of terms (report 11 §8.3); they remain needed for the existential reading (T(P), F3) and for Graal-style analysis. UCQ rewriting comes after v1 ([D16](decisions.md#d16)).
- **Pages:** [piece-unifiers](../domain/algorithms/piece-unifiers.md), [GRD](../domain/algorithms/graph-of-rule-dependencies-grd.md), [SCC-driven chase](../domain/algorithms/scc-driven-chase.md), [query rewriting](../domain/algorithms/query-rewriting.md), [homomorphism search](../domain/algorithms/homomorphism-search.md).

<a id="e5"></a>
## E5 Labelled nulls are a distinct term kind, consistent across all stores

- **Meaning.** The term model distinguishes labelled nulls from constants from day one, and every store (in-memory, persistent, external) represents and round-trips them identically.
- **Why.** Graal and InteGraal represent nulls inconsistently across stores (README key findings; reports 02, 04). This was named the first design decision.
- **Consequences.** [D1](decisions.md#d1) extends it to **three term kinds**: constant, named functional term, labelled null. F2 has no syntax creating nulls, and an input containing a labelled null is rejected ([D9](decisions.md#d9)). Functional terms may appear in input and must round-trip through the output encoding ([D11](decisions.md#d11)).
- **Pages:** [architecture principles](architecture-principles.md); domain: [labelled nulls](../domain/concepts/labelled-nulls.md).

<a id="e6"></a>
## E6 Language: Kotlin or Rust (open)

- **Meaning.** The implementation language is not decided.
- **Status.** Report 08 scores Rust 4.10 vs Kotlin 3.35 (sensitivity: 4.0 vs 3.55); report 05 recommended staying on Kotlin. Decision by a time-boxed spike, **2 weeks Kotlin + 3 weeks Rust** (README; report 08's 3 + 4 weeks are superseded by an editorial note), with go/no-go criteria (README E6). A hybrid Rust kernel + Kotlin outer layer is to be avoided. Report 05's Kotlin recommendation is historical; report 08 is the most recent analysis (README editorial note).
- **Pages:** [language choice](language-choice-kotlin-vs-rust.md).

<a id="e8"></a>
## E8 Rule-set optimisation pass

- **Meaning.** An optional pass rewrites the rule set to a more efficient one, with four constraints: the original set is always kept; the rewritten set is **logically equivalent** (query-answer equivalence may come later, low priority); each transformation has a formal proof; explanations trace back to the original rules.
- **Status.** The deliverable on transformations is postponed ([D4](decisions.md#d4)).
- **Pages:** domain: [rule-set transformations and equivalence](../domain/algorithms/rule-set-transformations-and-equivalence.md), [equivalence notions](../domain/concepts/equivalence-notions.md), [provenance](../domain/concepts/provenance.md).

<a id="e9"></a>
## E9 Proof sheets for transformations

- **Meaning.** Transformation proofs are first pen-and-paper proofs of logical equivalence (level a), each written as a standard sheet: statement and applicability condition; proof in both directions; counter-example for a close non-equivalent variant; associated differential test. Later mechanisation in Lean (level b), e.g. a small verified certificate checker. A verified implementation (level c) is out of scope.
- **Extension by practice.** Reports 09, 11 and 12 mark their own propositions `[U-own]` (sketch only); each needs such a proof before being relied upon.
- **Pages:** [test strategy](test-strategy.md); domain: [rule-set transformations and equivalence](../domain/algorithms/rule-set-transformations-and-equivalence.md).

<a id="e10"></a>
## E10 Reasoning strategies selected by the analyser

- **Meaning.** (a) forward chaining (chase, materialisation); (b) backward chaining at query time; (c) query rewriting, possibly a priori for pre-registered queries, which saves run time when the rewriting is bounded; (d) combinations. Pre-computed rewritings must be invalidated when rules change. **GBTS-specific algorithms are out of scope.**
- **Refinements.** [D16](decisions.md#d16): v0/v1 = pure chase only; then rewriting or backward chaining; then hybrid. [D5](decisions.md#d5) and [D15](decisions.md#d15) define rewriting under negation and the strict guard on pre-computed rewriting. [Q1](open-questions.md#q1) lead B discusses whether blocking of *simple* recursive chains could enter F2 (not decided).
- **Pages:** domain: [semi-naive evaluation](../domain/algorithms/semi-naive-evaluation.md), [backward chaining and tabling](../domain/algorithms/backward-chaining-and-tabling.md), [query rewriting](../domain/algorithms/query-rewriting.md) (including rewriting of queries known in advance), [hybrid strategies](../domain/algorithms/hybrid-strategies.md).

<a id="e11"></a>
## E11 Development model

- **Meaning.** 100% of the code is written by AI agents. Theoretical choices and architecture are validated by the project owner. The conformance and quality test suite and the benchmarks are built **independently of, and before**, the implementation.
- **Consequences.** Tests encode the specification (report 11 and decisions), not the behaviour of an implementation. Agents never silently change semantics. See [how agents work here](how-agents-work-here.md) and [test strategy](test-strategy.md).

<a id="e12"></a>
## E12 Premise simplification with termination measure

- **Meaning.** Rule-set simplification includes simplifying premises using other rules, e.g. `{a → b, a ∧ b → c} ≡ {a → b, a → c}`. Termination of the simplification is guaranteed by a strictly decreasing measure (e.g. total body size).
- **Pages:** domain: [rule-set transformations and equivalence](../domain/algorithms/rule-set-transformations-and-equivalence.md).

<a id="e13"></a>
## E13 Working principle: define the cap, then small verifiable increments

- **Meaning.** First define the target (the "cap"); then a plan that reaches it in small, iterative, incremental steps, each verifiable and validated. Same as [D17](decisions.md#d17).
- **Consequences.** Every phase of the [roadmap](roadmap.md) starts by writing its target (specification, expected behaviour, tests) before implementation; each step ends with a verification (tests, review) and an owner validation where theory or architecture is concerned. See [how agents work here](how-agents-work-here.md).

## Scope and target domains (README)

- **Scope:** v0 is plain positive Datalog ([D6](decisions.md#d6)), then F2 with named functions, stratified negation and aggregation ([D1](decisions.md#d1)). The older README line "existential rules first, then stratified negation, then aggregation" is marked as superseded in the README (former inconsistency I6, resolved). Uncertainty and time are out of scope for now.
- **Target domains:** enterprise / complex business-domain modelling (small-to-medium KBs), later aerospace/defense (potentially large KBs).

## Related pages

- [decisions](decisions.md), [open questions](open-questions.md), [vision and scope](vision-and-scope.md), [roadmap](roadmap.md).

## References

- [README, Requirements agreed so far](../preliminary-analysis/README.md#requirements-agreed-so-far) (authoritative).
- [Report 07](../preliminary-analysis/07-sota-theory.md) (theory behind E1, E2/E7, E8), [report 08](../preliminary-analysis/08-kotlin-vs-rust.md) (E6), [report 11](../preliminary-analysis/11-f2-framework-definition.md) §6-§8 (E1, E10 for F2).
