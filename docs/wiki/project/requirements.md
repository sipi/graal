# Requirements E1-E12

The requirements agreed between the project owner and the analysis phase, explained one by one: what each means, why it exists, how it constrains the design, and which pages implement it. The wording of the [README](../../preliminary-analysis/README.md#requirements-agreed-so-far) is authoritative; this page only explains it.

> **Status in this project:** `v0` `F2` `later` — all requirements are in force; later decisions (D1-D8) refine them, see [decisions](decisions.md).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

Note on numbering: `E1`-`E12` are **requirements**; `E1-ex`, `E2-ex`, `E3-ex` are **running examples** (see [running examples](running-examples.md)). Do not confuse them.

<a id="e1"></a>
## E1 Always sound; complete when decidability is proven; otherwise an explicit status

- **Meaning.** Every returned answer must be a true consequence of the knowledge base (soundness). When termination / decidability is established, all answers are returned (completeness). Otherwise the result carries an explicit "completeness not guaranteed" status.
- **Why.** An AI agent or a business user must be able to trust each answer, and know when absence of an answer means "false" versus "not found".
- **Consequences.**
  - Completeness can be proved **statically** (analyser certificate) or observed **dynamically** (the fixpoint was reached), see the README key theory points.
  - With negation or aggregation, an incomplete lower stratum can make upper answers *unsound*. Report 11 §6 therefore adds a fourth status, UNKNOWN, and the normative rule N1 (never return facts of an "exposed" unit). See [completeness statuses](../concepts/completeness-statuses.md) and the inconsistency note in [open questions](open-questions.md#known-inconsistencies).
- **Pages:** [completeness statuses](../concepts/completeness-statuses.md), [chase termination](../concepts/chase-termination.md), [decidability analyser](../algorithms/decidability-analyser.md).

<a id="e2"></a><a id="e7"></a>
## E2 / E7 The decidability and complexity analyser is central

- **Meaning.** A static analyser classifies the rule set (per strongly connected component of the dependency graph) and **drives algorithm selection**: materialisation, rewriting, or hybrid.
- **Why.** This is the project's differentiator (README, Clarifications): no existing system combines decidability analysis, automatic selection and performance.
- **Consequences.** Membership in FES/FUS/BTS is undecidable, so the analyser runs a portfolio of sufficient tests per SCC; its output (labels, certificates, witnesses) is part of every result trace (report 11 §7.3). In v0 its first job is to recognise the plain Datalog fragment ([D6](decisions.md#d6)).
- **Pages:** [decidability analyser](../algorithms/decidability-analyser.md), [decidability classes](../concepts/decidability-classes.md), [GRD](../algorithms/graph-of-rule-dependencies-grd.md).

<a id="e3"></a>
## E3 Explicit, controlled semantics

- **Meaning.** The semantics is stated explicitly, not implied by an implementation: which chase variant, stratified negation, then aggregation, and how labelled nulls are treated.
- **Consequences.** [D1](decisions.md#d1) fixes the v1 semantics: F2 perfect-model semantics. Report 11 is the draft contract. Any behaviour not covered by a written semantics is a specification bug.
- **Pages:** [perfect-model semantics](../concepts/perfect-model-semantics.md), [chase variants](../algorithms/chase-variants.md), [stratified negation](../concepts/stratified-negation.md), [aggregation](../concepts/aggregation.md).

<a id="e4"></a>
## E4 Must-have techniques

- **Meaning.** The engine must include: the graph of rule dependencies (GRD) computed with piece-unifiers; an SCC-driven chase; UCQ query rewriting in the style of PURE; homomorphism search exploiting the bi-connected components of the query.
- **Why.** These are Graal's signature techniques, known to the owner, and absent or partial in the alternatives (InteGraal lacks BCC homomorphism in its main path; Nemo lacks rewriting and GRD; reports 04, 05).
- **Note.** With F2 (no existential variables), piece-unifiers reduce to classical unification of terms (report 11 §8.3); they remain needed for the existential reading (T(P), F3) and for Graal-style analysis.
- **Pages:** [piece-unifiers](../algorithms/piece-unifiers.md), [GRD](../algorithms/graph-of-rule-dependencies-grd.md), [SCC-driven chase](../algorithms/scc-driven-chase.md), [query rewriting](../algorithms/query-rewriting-pure.md), [homomorphism search](../algorithms/homomorphism-search.md).

<a id="e5"></a>
## E5 Labelled nulls are a distinct term kind, consistent across all stores

- **Meaning.** The term model distinguishes labelled nulls from constants from day one, and every store (in-memory, persistent, external) represents and round-trips them identically.
- **Why.** Graal and InteGraal represent nulls inconsistently across stores (README key findings; reports 02, 04). This was named the first design decision.
- **Consequences.** [D1](decisions.md#d1) extends it to **three term kinds**: constant, named functional term, labelled null. F2 has no syntax creating nulls; report 11 proposes to reject nulls in F2 input (OP-13, `[choice]`).
- **Pages:** [labelled nulls](../concepts/labelled-nulls.md), [architecture principles](../engineering/architecture-principles.md).

<a id="e6"></a>
## E6 Language: Kotlin or Rust (open)

- **Meaning.** The implementation language is not decided.
- **Status.** Report 08 scores Rust 4.10 vs Kotlin 3.35 (sensitivity: 4.0 vs 3.55); report 05 recommended staying on Kotlin. Decision by a time-boxed spike with go/no-go criteria (README E6). A hybrid Rust kernel + Kotlin outer layer is to be avoided.
- **Pages:** [language choice](../engineering/language-choice-kotlin-vs-rust.md).

<a id="e8"></a>
## E8 Rule-set optimisation pass

- **Meaning.** An optional pass rewrites the rule set to a more efficient one, with four constraints: the original set is always kept; the rewritten set is **logically equivalent** (query-answer equivalence may come later, low priority); each transformation has a formal proof; explanations trace back to the original rules.
- **Status.** The deliverable on transformations is postponed ([D4](decisions.md#d4)).
- **Pages:** [rule-set simplification](../algorithms/rule-set-simplification.md), [equivalence notions](../concepts/equivalence-notions.md), [provenance and explanations](../concepts/provenance-and-explanations.md).

<a id="e9"></a>
## E9 Proof sheets for transformations

- **Meaning.** Transformation proofs are first pen-and-paper proofs of logical equivalence (level a), each written as a standard sheet: statement and applicability condition; proof in both directions; counter-example for a close non-equivalent variant; associated differential test. Later mechanisation in Lean (level b), e.g. a small verified certificate checker. A verified implementation (level c) is out of scope.
- **Extension by practice.** Reports 09, 11 and 12 mark their own propositions `[U-own]` (sketch only); each needs such a proof before being relied upon.
- **Pages:** [rule-set simplification](../algorithms/rule-set-simplification.md), [test strategy](../engineering/test-strategy.md).

<a id="e10"></a>
## E10 Reasoning strategies selected by the analyser

- **Meaning.** (a) forward chaining (chase, materialisation); (b) backward chaining at query time; (c) query rewriting, possibly a priori for pre-registered queries, which saves run time when the rewriting is bounded; (d) combinations. Pre-computed rewritings must be invalidated when rules change. **GBTS-specific algorithms are out of scope.**
- **Refinements.** [D5](decisions.md#d5) (rewriting under negation, hybrid); [Q1](open-questions.md#q1) lead B discusses whether blocking of *simple* recursive chains could enter F2 (not decided).
- **Pages:** [semi-naive evaluation](../algorithms/semi-naive-evaluation.md), [backward chaining and tabling](../algorithms/backward-chaining-and-tabling.md), [query rewriting](../algorithms/query-rewriting-pure.md), [precomputed rewriting](../algorithms/precomputed-rewriting.md), [hybrid strategies](../algorithms/hybrid-strategies.md).

<a id="e11"></a>
## E11 Development model

- **Meaning.** 100% of the code is written by AI agents. Theoretical choices and architecture are validated by the project owner. The conformance and quality test suite and the benchmarks are built **independently of, and before**, the implementation.
- **Consequences.** Tests encode the specification (report 11 and decisions), not the behaviour of an implementation. Agents never silently change semantics. See [how agents work here](how-agents-work-here.md) and [test strategy](../engineering/test-strategy.md).

<a id="e12"></a>
## E12 Premise simplification with termination measure

- **Meaning.** Rule-set simplification includes simplifying premises using other rules, e.g. `{a → b, a ∧ b → c} ≡ {a → b, a → c}`. Termination of the simplification is guaranteed by a strictly decreasing measure (e.g. total body size).
- **Pages:** [rule-set simplification](../algorithms/rule-set-simplification.md).

## Scope and target domains (README)

- **Scope order in the README:** existential rules first, then stratified negation (an existing external module to integrate), then aggregation. Uncertainty and time are out of scope for now. This line predates [D1](decisions.md#d1) and [D6](decisions.md#d6), which reorder the work (v0 positive Datalog, then F2 with named functions); see [known inconsistencies](open-questions.md#known-inconsistencies).
- **Target domains:** enterprise / complex business-domain modelling (small-to-medium KBs), later aerospace/defense (potentially large KBs).

## Related pages

- [decisions](decisions.md), [open questions](open-questions.md), [vision and scope](vision-and-scope.md), [roadmap](roadmap.md).

## References

- [README, Requirements agreed so far](../../preliminary-analysis/README.md#requirements-agreed-so-far) (authoritative).
- [Report 07](../../preliminary-analysis/07-sota-theory.md) (theory behind E1, E2/E7, E8), [report 08](../../preliminary-analysis/08-kotlin-vs-rust.md) (E6), [report 11](../../preliminary-analysis/11-f2-framework-definition.md) §6-§8 (E1, E10 for F2).
