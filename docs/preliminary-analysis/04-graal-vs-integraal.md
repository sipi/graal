# InteGraal vs Graal: gap analysis for a "SOLID" reasoning core

Date: 2026-09-30. Graal = local fork (last commit 3a9598f, 2019-03-11).
InteGraal = `fr.lirmm.graphik:integraal-*` **2.0.7** sources jars from Maven Central
(extracted locally from the published Maven Central artifacts; not included here).
Paths below marked `IG:` are relative to the root of the extracted InteGraal artifacts, and paths marked `G:` are relative to the Graal repository root.

## 0. Sources and what could not be checked

- **gitlab.inria.fr is blocked**: the proxy refused the CONNECT (403 connect_rejected) on `api/v4/projects/rules%2Fintegraal`. So the README, the git history, the issues, the CI and **the test sources were not seen**.
- **Maven Central works.** Artifacts on Central: integraal, -all, -api, -backward-chaining, -component, -configuration, -core, -explanation, -forgetting, -forward-chaining, -graal-ruleset-analysis, -grd, -io, -model, -query-evaluation, -redundancy, -storage, -unifiers, -util, -views (plus `brunner-integraal` 1.0.0–1.0.5, a benchmark runner).
  - Versions go from 1.0.0 (2023-11-10) to **2.0.7 (2025-06-11, latest; metadata lastUpdated 20250611)**.
  - There is **no 3.x** on Central, and no GitHub mirror (GitHub search for "integraal" finds only unrelated repositories).
  - Nothing has been published on Central for about 15 months.
- **Tests are not published.** Sources jars contain only `src/main`, and there is no test-jar. The InteGraal test count is **unknown**.
- Everything below comes from reading code. Nothing was compiled or run.

## 1. Feature matrix

| # | Feature | Graal (fork) | InteGraal 2.0.7 |
|---|---|---|---|
| 1 | GRD via piece-unifiers + dependency filters | **Yes.** `G:graal-grd/.../core/grd/DefaultGraphOfRuleDependencies.java`, checkers `ProductivityChecker`, `RestrictedProductivityChecker` (graal-grd/.../core/unifier/checker/) | **Yes, rewritten.** `IG:integraal-grd/fr/boreal/grd/impl/GRDImpl.java` (jgrapht `DirectedPseudograph`, parallel computation, positive and **negative** edges). Unifiers: `IG:integraal-unifiers/fr/boreal/unifier/QueryUnifierAlgorithm.java` (König thesis Alg. 2–5, most-general single-piece unifiers). Checkers: `ProductivityChecker` (7 conditions, negation-aware) and `RestrictedProductivityChecker`. **Porting bug:** the restricted checker adds `B+(r1)` twice and never `B+(r2)` (compare with Graal's version, which adds b1, h1, b2). This costs precision (extra edges), not soundness. `getCompressedAncestorRules` contains a stray `System.out.println`. |
| 2 | SCC-stratified chase | **Yes.** `G:graal-grd/.../forward_chaining/SccChase.java` (layers of the SCC graph, `graal-util/.../graph/scc/StronglyConnectedComponentsGraph.java`) and `StaticChase`, `ChaseWithGRD` | **Yes, opt-in.** `GRDImpl.getBySccStratification()` (Gabow SCC, reversed) + `IG:integraal-forward-chaining/.../metachase/stratified/StratifiedChase{,Builder}.java` (`useStratifiedChase().useStratification()`). It also offers `getMinimalStratification` (Bellman-Ford, negative-cycle = not stratifiable) and single-evaluation stratification. Within a stratum the scheduler is GRD-driven (`GRDScheduler`, which is **the default scheduler**). Caveat: the SCC order relies on Gabow output being reverse-topological; this was not verified by test. |
| 3 | PURE UCQ rewriting + minimization | **Yes.** `G:graal-backward-chaining/.../pure/PureRewriter.java`, 3 operators (`AggregSingleRuleOperator`, `AggregAllRulesOperator`, `BasicAggregAllRulesOperator`), cover in `Utils.computeCover` | **Yes.** `IG:integraal-backward-chaining/fr/boreal/backward_chaining/pure/PureRewriter.java`: breadth-first, cover (`cover/QueryCover.java`), **per-query core** (`core/QueryCoreProcessorImpl.java`), and subsumption pruning in both directions. Only one operator (`SingleRuleAggregator`). Compilation-aware (`RuleCompilation`, `unfolding/UCQUnfolder.java`). Extra: `source_target/SourceTargetRewriter` (DLX exact-cover, for mappings). Accepts only CQ/UCQ with no negation. No termination guard other than thread interrupt (FUS is assumed, as in Graal). |
| 4 | Homomorphism: BCC, backjumping, forward checking, pluggable schedulers | **Yes (strongest part of Graal).** `G:graal-homomorphism/.../bbc/BCC.java`, `BCCScheduler`, `BCCBackJumping`, `backjumping/GraphBaseBackJumping`, `forward_checking/*`, `scheduler/*`; `BacktrackHomomorphism` default = BCC + backjumping + forward checking | **Not in the reasoning path.** `IG:integraal-query-evaluation/.../conjunction/backtrack/BacktrackEvaluator.java` is plain backtracking with a pluggable `Scheduler` (`NaiveDynamicScheduler` default, `SimpleScheduler`, `NoScheduler`). There is **no BCC, no backjumping and no forward checking** outside the vendored Graal copy (below). Other additions: delegation of atoms/conjunctions to the DBMS (`generic/FOQueryEvaluatorWithDBMSDelegation`), computed atoms, and an isomorphism checker. The BCC code does exist as a **verbatim copy** of Graal (`IG:integraal-graal-ruleset-analysis/fr/lirmm/graphik/integraal/homomorphism/bbc/BCC.java`, 0 diff lines after package rename), but it is used only inside the rule analyser. |
| 5 | Rule analyser / decidability (WA, MFA, MSA, sticky, guarded, FES/FUS/BTS, Kiabora combination) | **Yes.** `G:graal-rules-analyser/.../rulesetanalyser/{Analyser,RuleSetPropertyHierarchy}.java` + 21 properties | **Yes, but it is Graal's code.** `IG:integraal-graal-ruleset-analysis` ("Rule base analysis for InteGraal. This is imported from Graal", POM) holds 419 files / 50k lines, a repackaged copy of Graal's core, homomorphism, forward chaining and analyser. It **depends on Graal 1.3.1 jars** (`graal-core`, `graal-api`, `graal-util`). Rules are converted with `IG:integraal-util/.../converter/*Converter.java`. The 21 property files are **identical** to Graal's apart from the jgrapht 1.5 API and removed logging (MFA/MSA drop the LOGGER.warn on chase errors). Exposed as `EndUserAPI.isFes/isFus/isDecidable/hybridize`. |
| 6 | Stratified negation | **No** user-level negation. Negated parts are used only internally for the restricted-chase check (`G:graal-forward-chaining/.../RestrictedChaseRuleApplier.java`, `graal-core/.../RuleWrapper2ConjunctiveQueryWithNegatedParts.java`). Negative constraints are parsed. | **Partial.** Rule bodies may contain `FONegation`, evaluated as negation-as-failure (`IG:integraal-query-evaluation/.../negation/NegationFOQueryEvaluator.java`, "exists a homomorphism" on the current fact base). The GRD has negative edges, `isStratifiable()` exists, and the stratified chase exists. **Soundness gaps:** (a) the default chase (`ChaseBuilder.defaultChase`, `EndUserAPI.saturate`, `IComponentBuilder.buildAndGetChase`) never stratifies; the REPL itself warns "stratification is emulated from within the interactive tool; eventually, InteGraal should do it itself" (`IG:integraal-api/.../integraal_repl/IGCommands.java:346`). (b) The DEFAULT (SCC) stratification path does not call `isStratifiable()`, so a non-stratifiable program runs silently. (c) `Rules.computeSafeNegation` (`IG:integraal-util/fr/lirmm/boreal/util/Rules.java:130`) is buggy: with k negated parts it emits k rules each keeping only one negation, and a rule with no negation returns the empty set. It has no callers in the published code. (d) Semantics under nulls: nulls are variables in the data and negation is checked by homomorphism against them. The result depends on the chase variant (restricted vs semi-oblivious), and no documented semantics was seen (the README could not be reached). |
| 7 | Aggregation | No | **No group-by aggregation.** Only scalar "evaluable functions" over the arguments of one tuple (`IG:integraal-model/fr/boreal/model/functions/IntegraalInvokers.java`: sum, min, max, average, median, weightedAverage, comparisons, string ops, isPrime, and others) and computed atoms. There is no count/sum over a group and no monotonic-aggregate semantics. |
| 8a | Chase variants | Breadth-first, static, GRD-driven and SCC chases. Appliers: default, exhaustive, restricted; frontier-restricted halting (`G:graal-forward-chaining/...`) | **Richer and pluggable** (`IG:integraal-forward-chaining/.../chase/ChaseBuilder.java`). Checkers: always-true (oblivious without memory), oblivious, **semi-oblivious (default)**, restricted, "equivalent" (piece-local homomorphism check). Namers: fresh (default), body-Skolem, frontier-Skolem, frontier-by-piece-Skolem. Trigger computers: naive (**default**), semi-naive, two-steps, restricted. Appliers: parallel/breadth-first (default), multi-thread, source-delegated Datalog (push down to SQL). Halting conditions: timeout, step or atom limits, external interrupt. Lineage tracking is included. |
| 8b | Equality / EGDs | No (only `=` in queries: `G:graal-homomorphism/.../utils/EqualityUtils.java`) | **No EGDs.** Only query-level variable equalities (`FOQuery.getVariableEqualities`). `Predicate.BOTTOM` exists for constraints. |
| 8c | Core computation | No (nothing in the fork) | **Yes.** `IG:integraal-core/fr/boreal/core/{Naive,ByPiece,ByPieceAndVariable,MultiThreadsByPiece}CoreProcessor.java`, used as a chase end-of-step treatment (`treatment/ComputeCore`, `ComputeLocalCore`), which gives a core chase. Queries also get cores during rewriting. |
| 9 | Data-model soundness | Known issues: nulls created as constants in `LinkedListAtomSet` (:76 `DefaultConstantGenerator("EE")`) but as variables in `DefaultInMemoryGraphStore` (:87 `DefaultVariableGenerator("EE")`); `AbstractRule.equals` compares label, body and head while `hashCode` is identity-based (AbstractRule.java:62–120, so the equals/hashCode contract is broken); static unsynchronized counters (`GraalConstant._constant_count`, `Rules.auxIndex`, `DefaultGraphOfRuleDependencies.ruleIndex`) | **Mostly fixed, with new edge cases.** See §2. |
| 10 | Positioning | Generic existential-rule toolkit (2014–2019) | **Data integration** ("Knowledge-Representation and Reasoning for Data Integration", root POM): SQL/RDF/SPARQL/Mongo/Web-API sources, mappings/views, federated query answering, explanations, forgetting, a REPL. See §3. |
| 11 | Size, tests, activity | 603 Java files, 85k lines total (69k main); 69 test classes / **384 `@Test`** | 792 files / 109k lines published, of which **50k are the vendored Graal copy**, leaving about 59k "native". Core reasoning modules (non-blank, non-comment lines): model 6.5k, forward-chaining 3.6k, query-eval 1.7k, backward 0.9k, core 0.65k, grd 0.4k, unifiers 0.3k. Tests: **unknown**. Releases: 24 versions in 19 months, then silence since 2025-06. |

## 2. Data model in detail (item 9)

**Fixed compared with Graal**

- **Rule identity.** `FORuleImpl.equals` compares body and head structurally (label excluded), and `hashCode = Objects.hash(body, head)`, cached. The contract holds (`IG:integraal-model/fr/boreal/model/rule/impl/FORuleImpl.java`).
- **Fresh names.** `SameObjectTermFactory.createOrGetFreshVariable()` is `synchronized`, and its counter is an instance field on the singleton, so there are no static racy counters (`IG:integraal-model/.../factory/impl/SameObjectTermFactory.java`). Fresh predicates come from `SameObjectPredicateFactory`.
- **Nulls in the chase.** All in-memory chase naming produces `IdentityFreshVariableImpl("Graal:EE<n>")`, so nulls are consistently variables. There is no longer a store that turns nulls into constants in memory.
- **Term equality.** Terms are hash-consed (`WeakHashMap<label, SoftReference<Term>>`) and compared by identity (`IdentityTermImpl.equals` is `==`, with `identityHashCode`).

**Remaining or new issues**

1. **No distinct null type.** Labelled nulls are `Variable`s. Facts can contain variables, and the only mark of a null is the `IdentityFreshVariableImpl` subclass plus the "Graal:EE" label prefix, which the parser does not reserve.
2. **Nulls do not round-trip through external stores.**
   - `TripleStoreStore` writes a null as an **IRI** `"_:"+label` (`IG:integraal-storage/.../triplestore/TripleStoreStore.java:427-428`). `RDF4JValueConverter` reads IRIs back as **constants** (`.../rdf4j/value/converter/RDF4JValueConverter.java:31-34`). A null therefore becomes a constant, which is the same class of bug as Graal's LinkedListAtomSet vs graph store.
   - The SQL layouts store type "V" and read it back via `createOrGetVariable(label)` (`.../rdbms/layout/AdHocSQLLayout.java:183`). That returns a plain `IdentityVariableImpl`, and fresh variables are never registered in the factory map, so it is a **different object** from the in-memory fresh variable with the same label. Identity equality then treats one null as two terms whenever results from in-memory and SQL stores are mixed.
3. **Mixed term implementations break equality.** `VariableImpl.equals` is label-based and accepts any `Variable`, while `IdentityVariableImpl.equals` is `==`. The relation is asymmetric, and the hashCodes are inconsistent (label hash vs identity hash).
4. **`FOConjunctionImpl` equals/hashCode.** `equals` is set-like (`containsAll` in both directions), but `hashCode` sums `hash*size*17` over the list. With duplicate atoms, equal conjunctions can have different hashes. It is also O(n²).
5. **Global singletons and static mutable state.**
   - Singletons: the term, predicate, formula and query factories, plus `NoRuleCompilation` (lazily set, non-final static).
   - `SameObjectTermFactory.forgetConstant` is not synchronized.
   - Several static mutable fields sit in explanation and REPL code (`Sat4JSolver.lists`, `StatsUtil.*`, `AbstractIncrementalGRIBasedExplainer_KBGRI.grd`).
   - `GRDImpl.getMinimalStratification()` mutates the graph itself by adding and removing a fictive vertex, so it is not safe to call concurrently.
6. **Library hygiene.** `EndUserAPI.saturate` prints the chase description JSON to stdout on every call. Duplicate `xxxOld` / `xxx` API pairs exist throughout `EndUserAPI`. The published code has 99 TODO/FIXME markers outside the vendored module. The explanation module ships benchmark code with hard-coded dataset paths (`pipeline_with_timer/compareExplainers.java`, "AAAI_datasets").

## 3. Stated goals and positioning (item 10)

- **Root POM name:** "InteGraal : Knowledge-Representation and Reasoning for Data Integration". Its description lists:
  1. internal storage in SQL/RDF (Postgres, MySQL, HSQL, SQLite, SPARQL endpoints, in-memory triplestores) plus native in-memory storage;
  2. "data-integration capabilities for exploiting federated heterogeneous data-sources through mappings able to target systems such as SQL, RDF, and black-box (e.g. Web-APIs)";
  3. query answering over heterogeneous, federated data by rewriting and/or chase.
- **Design stance:** "designed in a modular way … easy to test new scenarios and techniques, in particular by combining algorithms". That is a **research workbench**, with every chase dimension a pluggable strategy.
- **Module mix confirms the integration focus:**
  - `views` ("relational views over heterogeneous data");
  - `storage` (RDBMS, triplestore, Mongo/JSON dependencies in the root POM);
  - DBMS-delegated evaluation and source-delegated Datalog application;
  - `explanation` (why-provenance, GMUS/SAT4J, AAAI experiments);
  - `forgetting`, `redundancy`;
  - REPL / commander.
- **What this means for a reasoning core:**
  - Graal's homomorphism engineering (BCC, backjumping, forward checking) was **not carried over** to the main evaluator.
  - Rule analysis is **not reimplemented**; it is the Graal code bolted on through a model converter and Graal 1.3.1 jars.
  - Effort went into data-source abstraction, configurability, explanations and core computation.
- The README and the papers could not be read (GitLab blocked). The positioning above rests on POM texts and code only.

## 4. What Graal has that InteGraal lacks

1. **BCC-based homomorphism with backjumping and forward checking** in the main query and chase path. InteGraal keeps it only inside the vendored analyser copy.
2. **Multiple PURE rewriting operators** (all-rules aggregation variants). InteGraal has a single operator.
3. **A native (non-vendored) rule analyser.** InteGraal still depends on Graal 1.3.1 jars and model conversion for it.
4. Test coverage: Graal's 384 tests are visible. InteGraal's could not be verified.

## 5. What InteGraal adds over Graal

- Stratified negation (opt-in; see the caveats in row 6).
- Core computation and a core chase.
- Skolem naming variants and a semi-oblivious chase.
- Semi-naive and two-step trigger computation.
- Multi-thread appliers.
- Lineage and explanations.
- Evaluable functions.
- A negation-aware GRD with minimal stratification.
- Query cores in rewriting.
- DBMS delegation and federated views.
- A cleaner rule equality contract and synchronized fresh naming.
- Java 21 with JPMS modules.

## 6. Judgement: "lean, sound, fast reasoning core for an AI agent"

**InteGraal's objectives do not match this goal.**

It is optimised for **data integration and research experimentation**: many pluggable strategies, external stores, mappings and explanations. It is not a small, verified, fast kernel.

- **For it:** the algorithmic coverage is broad. It has the GRD, SCC or minimal stratification, PURE with cores, core chase, and several chase variants. It is Apache-2.0 and modular.
- **Against it (sound):**
  - Negation is not stratified by default.
  - A non-stratifiable program is not rejected on the SCC path.
  - Nulls are variables and do not round-trip through RDF and SQL stores.
  - Equality contracts are broken when term implementations are mixed.
  - `computeSafeNegation` is buggy.
- **Against it (fast):**
  - The main evaluator is plain backtracking.
  - Graal's BCC, backjumping and forward checking were dropped from it.
  - The default trigger computer is *naive*.
- **Against it (lean):** 59k native lines plus a 50k-line vendored Graal copy and Graal 1.3.1 jars. Heavy dependencies: rdf4j, jena, JDBC drivers, mongodb, sat4j.
- **Against it (maintainable):** stdout printing in the API, `Old` duplicate APIs, unpublished tests, and no release since 2025-06.

A reasonable path is to **harvest ideas and algorithms** from InteGraal rather than adopt it as the core:
- König's unifier algorithm, `ProductivityChecker` with negation, Bellman-Ford stratification, by-piece core processors, and the Skolem namer variants;
- Graal's BCC, backjumping and forward-checking homomorphism;
- a proper `Null` term type, rule-level stratification enforced by default, and immutable, thread-confined factories.
