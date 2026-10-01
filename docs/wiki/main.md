# Project wiki: a sound, analyser-driven logical reasoning engine

This wiki is the reference document for every AI agent working on this project, and a map for the project owner who validates its theory and architecture. It explains what we are building, the logical "universe" it lives in, the decisions already taken, and where each piece of knowledge is sourced. It summarises and links the [preliminary analysis](../preliminary-analysis/README.md); it never overrides it.

> **Status in this project:** `v0` `F2` `later` — entry point; governed by the [source-of-truth hierarchy](#source-of-truth-hierarchy) below.
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

## What the project is

We are building a **new, homemade but solid logical reasoning engine**. The repository still contains the legacy Graal code base (Java, 2019, CeCILL 2.1), but the decision is to write a **new core**, guided by Graal's design and using Graal as a test oracle on the fragment where it is valid (see [vision and scope](project/vision-and-scope.md) and [decisions](project/decisions.md)).

Intended uses:
- a **formal reasoning layer for AI agents**: an LLM agent formalises a domain into rules and facts; the engine derives consequences, answers queries, checks constraints and explains its answers, with an explicit guarantee status;
- **enterprise and complex business-domain modelling** (small to medium knowledge bases: contracts, pricing, organisations);
- later, **aerospace and defense** (potentially large knowledge bases, assurance requirements).

The differentiator: no existing system combines **decidability analysis**, **automatic algorithm selection** driven by that analysis, and **high performance** (README, Clarifications). The engine is always sound, complete whenever completeness can be proved or observed, and says so explicitly otherwise ([E1](project/requirements.md#e1)).

## The universe in a few paragraphs

**Knowledge representation with rules.** Knowledge is written as **facts** (`employee(tom).`) and **rules** (`superiorOf(Y, X) :- managerOf(Y, X).`), a fragment of first-order logic. A **knowledge base** (KB) is a set of facts (the data) plus a set of rules (the ontology or business logic). Users ask **queries**, typically conjunctive queries ("which X have a superior who is a director?"). See [foundations](concepts/foundations.md), [Datalog](concepts/datalog.md), [conjunctive queries](concepts/conjunctive-queries-and-ucq.md).

**Reasoning.** The engine computes what *follows* from the KB. Two families of algorithms: **forward chaining** (apply rules to the data until nothing new appears: materialisation, the chase) and **backward chaining** (start from the query and rewrite it with the rules until it can be evaluated directly on the data: query rewriting, tabling). Real engines combine both ([E10](project/requirements.md#e10)). See [semi-naive evaluation](algorithms/semi-naive-evaluation.md), [chase variants](algorithms/chase-variants.md), [query rewriting](algorithms/query-rewriting-pure.md), [hybrid strategies](algorithms/hybrid-strategies.md).

**Value invention.** Business rules often say "every employee *has* a manager" without naming the manager. Logic offers two ways to express this: an **existential variable** (`∃y managerOf(y, x)`, an anonymous **labelled null**) or a **named Skolem function** (`managerOf(manager(x), x)`, a term with a stable identity). This project chose named functions for v1 (framework **F2**, [D1](project/decisions.md#d1)), while keeping labelled nulls in the data model for later. See [existential rules](concepts/existential-rules.md), [Skolem functions](concepts/skolem-functions-and-terms.md), [labelled nulls](concepts/labelled-nulls.md).

**Decidability.** With value invention, reasoning is undecidable in general: the chase may run forever (E3-ex, "every manager is an employee"). Decades of research identified **decidable classes** (finite expansion sets, finite unification sets, bounded treewidth sets) and cheap **sufficient tests** (weak acyclicity, joint acyclicity, MFA, guardedness, stickiness...). Membership in the abstract classes is itself undecidable, so the engine runs a **portfolio** of sufficient tests per strongly connected component of the rule dependency graph. See [decidability classes](concepts/decidability-classes.md), [chase termination](concepts/chase-termination.md), [the analyser](algorithms/decidability-analyser.md).

**Why soundness and completeness statuses matter.** A sound engine never returns a wrong answer; a complete engine returns all answers. When termination is not guaranteed, a budgeted computation is still sound for positive consequences but may miss answers; with negation or aggregation, an incomplete lower layer can even make upper-layer answers *wrong*. The engine therefore attaches a **status** to every result: complete by static proof, complete because a fixpoint was observed, sound but not guaranteed complete, or unknown ([E1](project/requirements.md#e1), [completeness statuses](concepts/completeness-statuses.md)). For an AI agent, this status is the difference between a fact and a guess.

**Non-monotonic features.** Business rules need defaults and exceptions ("if no specific condition applies, general conditions apply": **stratified negation**, E1-ex) and thresholds on sums ("basket > 200€ gives free delivery": **aggregation** with **exact decimals**, E2-ex). These require a closed-world reading layered by **strata**. See [stratified negation](concepts/stratified-negation.md), [aggregation](concepts/aggregation.md), [exact decimals](concepts/exact-decimals-and-rounding.md).

**Where LLM agents fit.** Agents play two roles. (1) As **users**: they write rules and queries and need precise answers, statuses and **diagnostics** ("your rules imply an infinite chain of managers; did you mean it to stop at the director?", [modeller diagnostics](concepts/modeller-diagnostics.md)). (2) As **developers**: AI agents write 100% of the engine's code, against a conformance test suite built independently of the implementation, while the owner validates theory and architecture ([E11](project/requirements.md#e11), [how agents work here](project/how-agents-work-here.md)).

## Current phase in one paragraph

Phase 1 (theoretical framework) is under way: F2 is chosen ([D1](project/decisions.md#d1)), its definition is drafted in [report 11](../preliminary-analysis/11-f2-framework-definition.md) (**DRAFT, under owner review**). The **first engine, v0, is plain positive Datalog** ([D6](project/decisions.md#d6)): no existential variables, no functions, no negation, but an architecture that anticipates F2 (three term kinds, strata). The implementation language (Kotlin or Rust) is **open** ([E6](project/requirements.md#e6)). See the [roadmap](project/roadmap.md).

## How to read this wiki

| Reading path | Pages, in order |
|---|---|
| **New agent in 30 minutes** | this page → [how agents work here](project/how-agents-work-here.md) → [conventions](conventions.md) → [decisions](project/decisions.md) → [requirements](project/requirements.md) → [running examples](project/running-examples.md) → [foundations](concepts/foundations.md) → [roadmap](project/roadmap.md) → [open questions](project/open-questions.md) |
| **Working on v0 (positive Datalog engine)** | [Datalog](concepts/datalog.md) → [semi-naive evaluation](algorithms/semi-naive-evaluation.md) → [homomorphism search](algorithms/homomorphism-search.md) → [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md) → [GRD](algorithms/graph-of-rule-dependencies-grd.md) → [architecture principles](engineering/architecture-principles.md) → [test strategy](engineering/test-strategy.md) |
| **Working on the analyser** | [decidability classes](concepts/decidability-classes.md) → [chase termination](concepts/chase-termination.md) → [GRD](algorithms/graph-of-rule-dependencies-grd.md) → [piece-unifiers](algorithms/piece-unifiers.md) → [decidability analyser](algorithms/decidability-analyser.md) → [completeness statuses](concepts/completeness-statuses.md) → [modeller diagnostics](concepts/modeller-diagnostics.md) |
| **Working on storage / joins** | [foundations](concepts/foundations.md) (terms) → [labelled nulls](concepts/labelled-nulls.md) → [Skolem terms](concepts/skolem-functions-and-terms.md) → [architecture principles](engineering/architecture-principles.md) → [homomorphism search](algorithms/homomorphism-search.md) → [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md) → [Nemo](systems/nemo.md) → [Soufflé](systems/souffle.md) |
| **Working on F2 semantics** | [report 11](../preliminary-analysis/11-f2-framework-definition.md) → [Skolem terms](concepts/skolem-functions-and-terms.md) → [lookup-before-invent](concepts/lookup-before-invent.md) → [perfect-model semantics](concepts/perfect-model-semantics.md) → [stratified negation](concepts/stratified-negation.md) → [aggregation](concepts/aggregation.md) → [T(P)](concepts/function-graph-translation-tp.md) → [Q1](project/open-questions.md#q1) |
| **Working on tests / benchmarks** | [test strategy](engineering/test-strategy.md) → [benchmarks and oracles](engineering/benchmarks-and-test-oracles.md) → [running examples](project/running-examples.md) → [Graal](systems/graal.md) → [clingo and DLV](systems/clingo-and-dlv.md) |
| **Working on rewriting / backward chaining** | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) → [piece-unifiers](algorithms/piece-unifiers.md) → [query rewriting](algorithms/query-rewriting-pure.md) → [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) → [precomputed rewriting](algorithms/precomputed-rewriting.md) → [hybrid strategies](algorithms/hybrid-strategies.md) |

## Page index

### Entry and conventions
- [main](main.md): this page.
- [conventions](conventions.md): page template, naming, notation, linking, tags, how to propose changes.
- [glossary](glossary.md): every project term, one line each, with French equivalents.
- [STAGE2-PLAN](STAGE2-PLAN.md): work plan for the writer agents filling the stub pages.

### Project
- [vision and scope](project/vision-and-scope.md): goals, target domains, differentiator, what is in and out.
- [requirements](project/requirements.md): E1-E12 explained.
- [decisions](project/decisions.md): D1-D8 with rationale (README is authoritative).
- [open questions](project/open-questions.md): Q1, leads A-C, report 11 open points, known inconsistencies.
- [roadmap](project/roadmap.md): phases and the v0 / F2 / later split.
- [running examples](project/running-examples.md): E1-ex, E2-ex, E3-ex (corrected) and a v0 example, in provisional syntax.
- [how agents work here](project/how-agents-work-here.md): development model, rules of conduct, commits, where things live.

### Concepts
- [foundations](concepts/foundations.md): terms, atoms, substitutions, homomorphisms, models, entailment, certain answers, OWA/CWA, UNA.
- [Datalog](concepts/datalog.md): function-free Horn rules, least model; the v0 fragment.
- [existential rules](concepts/existential-rules.md): TGDs, Datalog+/-, certain answers.
- [Skolem functions and terms](concepts/skolem-functions-and-terms.md): rule-local vs named functions, Herbrand reading.
- [labelled nulls](concepts/labelled-nulls.md): the third term kind, store consistency (E5).
- [conjunctive queries and UCQ](concepts/conjunctive-queries-and-ucq.md): CQ, UCQ, NCQ, containment.
- [perfect-model semantics](concepts/perfect-model-semantics.md): stratum-wise least fixpoints.
- [stratified negation](concepts/stratified-negation.md): negation as failure, strata, closed world.
- [aggregation](concepts/aggregation.md): count/sum/min/max, groups, stratified aggregates.
- [equality and UNA](concepts/equality-and-una.md): EGDs, FDs as constraints, D8 caveat.
- [exact decimals and rounding](concepts/exact-decimals-and-rounding.md): decimal value space, D7 rounding modes.
- [decidability classes](concepts/decidability-classes.md): FES/FUS/BTS/GBTS, WA, JA, MFA, MSA, guarded, sticky, warded, FDNC.
- [chase termination](concepts/chase-termination.md): all-instance vs per-instance, critical instance, undecidability.
- [completeness statuses](concepts/completeness-statuses.md): COMPLETE-STATIC/DYNAMIC, NOT-GUARANTEED, UNKNOWN; rule N1.
- [equivalence notions](concepts/equivalence-notions.md): logical equivalence, conservative extension, query equivalence (E8).
- [provenance and explanations](concepts/provenance-and-explanations.md): proof trees over original rules.
- [modeller diagnostics](concepts/modeller-diagnostics.md): analyser output aimed at the human or LLM modeller (Q1 lead C).
- [function-graph translation T(P)](concepts/function-graph-translation-tp.md): bridge from named functions to existential rules (D3).
- [lookup-before-invent](concepts/lookup-before-invent.md): default for named functions (D2).

### Algorithms
- [homomorphism search](algorithms/homomorphism-search.md): CQ evaluation, bi-connected components, backjumping (E4).
- [piece-unifiers](algorithms/piece-unifiers.md): unification for existential rules (E4).
- [graph of rule dependencies (GRD)](algorithms/graph-of-rule-dependencies-grd.md): dependency edges, SCCs (E4).
- [SCC-driven chase](algorithms/scc-driven-chase.md): scheduling saturation by SCC order (E4).
- [chase variants](algorithms/chase-variants.md): oblivious, semi-oblivious/Skolem, restricted, Datalog-first, core.
- [semi-naive evaluation](algorithms/semi-naive-evaluation.md): delta-driven fixpoint computation (v0 core).
- [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md): leapfrog triejoin, generic join.
- [query rewriting (PURE)](algorithms/query-rewriting-pure.md): UCQ rewriting with piece-unifiers (E4).
- [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md): SLD/SLG, magic sets.
- [precomputed rewriting](algorithms/precomputed-rewriting.md): rewritings of pre-registered queries, invalidation (E10, D5).
- [hybrid strategies](algorithms/hybrid-strategies.md): materialise below, rewrite above; D5 under negation.
- [decidability analyser](algorithms/decidability-analyser.md): portfolio per SCC, algorithm selection (E2/E7).
- [rule-set simplification](algorithms/rule-set-simplification.md): equivalence-preserving optimisation (E8, E12, D4).
- [incremental maintenance](algorithms/incremental-maintenance.md): DRed, FBF, B/F.
- [blocking of recursive chains](algorithms/blocking-of-recursive-chains.md): finite representation of infinite chains (Q1 lead B).

### Systems
- [Graal](systems/graal.md), [InteGraal](systems/integraal.md), [Nemo](systems/nemo.md), [VLog and Rulewerk](systems/vlog-rulewerk.md), [RDFox](systems/rdfox.md), [Vadalog](systems/vadalog.md), [Jena rules](systems/jena-rules.md), [Soufflé](systems/souffle.md), [clingo and DLV](systems/clingo-and-dlv.md), [others](systems/others.md) (egglog, Scallop, ascent, DDlog, ELK, Ontop).

### Engineering
- [language choice: Kotlin vs Rust](engineering/language-choice-kotlin-vs-rust.md) (open).
- [benchmarks and test oracles](engineering/benchmarks-and-test-oracles.md).
- [test strategy](engineering/test-strategy.md).
- [architecture principles](engineering/architecture-principles.md).

### Legal
- [licensing and provenance](legal/licensing-and-provenance.md): CeCILL vs re-implementation; licences of other engines.

## Source-of-truth hierarchy

1. **README decisions and requirements** ([`../preliminary-analysis/README.md`](../preliminary-analysis/README.md)): D*, E*, Corrections, Q*. Authoritative.
2. **Framework definition**, [report 11](../preliminary-analysis/11-f2-framework-definition.md), **once validated** by the owner. Until then it is a DRAFT: its `[choice]` items are proposals.
3. **Reports** 01-13 in [`../preliminary-analysis/`](../preliminary-analysis/README.md) (report 13 on the Apache Jena rule engine: [`../preliminary-analysis/13-jena-rule-engine.md`](../preliminary-analysis/13-jena-rule-engine.md)).
4. **Wiki pages.**

The wiki summarises and links; **it never overrides a decision**. Any conflict between levels, or between two sources of the same level, must be **reported** (in [open questions, known inconsistencies](project/open-questions.md#known-inconsistencies) and to the owner), never silently resolved. See [conventions §8](conventions.md#8-proposing-changes).
