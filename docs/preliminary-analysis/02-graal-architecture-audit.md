# Graal: architecture and quality audit (static inspection)

Repo: local clone of Graal, version `1.3.2-SNAPSHOT`, Java 8 (`pom.xml:55`), last commit 2019-03-11 (`3a9598f`). Visible history: 171 commits from 2017-06-26 (earlier history is not in this clone). Main authors: O. Rodriguez (119 commits) and C. Sipieter (51). CI: `.gitlab-ci.yml` runs `mvn install` on `maven:3-jdk-8`. License: **CeCILL 2.1** (`LICENSE`), a GPL-compatible copyleft licence, so any derived engine inherits copyleft obligations.

Method: static reading only; nothing was built or run. SLOC means non-blank, non-comment Java lines, computed with a small Python script (`cloc` is not installed). Every file carries a CeCILL header of about 40 lines, so raw `wc -l` is about 2x SLOC.

---

## 1. Module map

32 `pom.xml` files and 27 modules with code. Totals: **30.9k SLOC main (534 files)** and **9.7k SLOC test (69 files)**. The test/main ratio is about 0.31.

| Module | Purpose | Main SLOC (files) | Test SLOC (files) | @Test/@Theory |
|---|---|---|---|---|
| graal-util | Generic utilities: CloseableIterator "stream" framework, profiler, Partition, graph SCC/biconnected components, Trie, URI/prefix | 2726 (87) | 83 (2) | 4 |
| graal-api | All interfaces (Term, Atom, AtomSet, Store, Rule, Substitution, Query*, Homomorphism*, Chase, RuleApplier, QueryRewriter, KnowledgeBase, Parser/Writer) plus a few abstract bases | 1599 (98) | 0 | 0 |
| graal-core | Default model implementations (terms, atoms, rules, substitutions, queries), in-memory stores (`LinkedListAtomSet`, `DefaultInMemoryGraphStore`), piece-unifiers, rule compilations (ID/Hierarchical), rule sets, mappers, factories, `Rules` utilities (critical instance, decomposition) | 7432 (116) | 1390 (14) | 91 |
| graal-homomorphism | CQ/UCQ evaluation: backtracking CSP engine with pluggable scheduler, bootstrapper, forward checking (NFC0/NFC2/SimpleFC), back-jumping, BCC; atomic and fully-instantiated fast paths; negated parts; `SmartHomomorphism` dispatcher; `PureHomomorphism` for atom-set containment | 4067 (58) | 770 (8) | 33 |
| graal-forward-chaining | Chase drivers (`BasicChase`, `BreadthFirstChase`), rule appliers, halting conditions (restricted, frontier-restricted/skolem) | 742 (11) | 101 (2) | 3 |
| graal-grd | Graph of Rule Dependencies (jgrapht), productivity checkers, GRD-driven chases (`ChaseWithGRD`, `ChaseWithGRDAndUnfiers`, `SccChase`, `StaticChase`) | 746 (7) | 653 (5) | 30 |
| graal-backward-chaining | PURE query rewriting (piece-unifier based UCQ rewriting, with or without compilation) | 856 (11) | 534 (4) | 15 |
| graal-rules-analyser | "Kiabora": 20 decidability-class properties, class hierarchy, and FES/FUS/BTS combination over SCCs of the GRD | 2281 (30) | 391 (2) | 28 |
| graal-kb | `DefaultKnowledgeBase` and `KBBuilder`: picks saturation, rewriting or a mix from the analyser | 558 (3) | 412 (3) | 35 |
| graal-builtin-predicates | Built-in comparison predicates in queries and rules | 485 (13) | 154 (1) | 3 |
| graal-io/graal-io-dlgp | DLGP parser and writer (the grammar lives in the external artifact `dlgp2-parser:2.1.1`) | 1013 (10) | 669 (2) | 65 |
| graal-io/graal-io-owl | OWL 2 to existential rules (OWLAPI 4.0.1) | 1867 (11) | 1875 (1) | 74 |
| graal-io/graal-io-rdf | RDF parser and writer (rdf4j 1.0.3) | 218 (5) | 0 | 0 |
| graal-io/graal-io-ruleml | RuleML writer | 297 (1) | 0 | 0 |
| graal-io/graal-io-sparql | SPARQL parser and writer (Jena 2.13, `com.hp.hpl.jena`) | 580 (7) | 341 (3) | 13 |
| graal-store/rdbms-common | JDBC store base, SQL homomorphism, drivers (MySQL, Postgres, SQLite, HSQLDB) | 1884 (29) | 0 | 0 |
| graal-store/rdbms-adhoc | Generic "one table per predicate" RDBMS store | 778 (7) | 0 | 0 |
| graal-store/rdbms-natural | Store over existing relational tables | 522 (6) | 0 | 0 |
| graal-store/rdbms-test | Tests for the RDBMS stores (HSQLDB) | 0 | 157 (3) | 3 |
| graal-store/graal-store-neo4j | Neo4j 2.3 embedded store | 500 (1) | 0 | 0 |
| graal-store/graal-store-rdf4j | RDF4J triple store | 590 (9) | 0 | 0 |
| graal-store/graal-store-dictionary | Dictionary-encoded (integer id) stores; package `org.graal.*` | 782 (9) | 122 (1) | 4 |
| graal-sparql-homomorphism | Evaluates CQs as SPARQL against RDF4J. Source path is misplaced: `src/main/java/src/fr/...` | 115 (1) | 69 (1) | 1 |
| graal-keyvalue/key-value-core | DLGP to key-value store converter (experimental) | 141 (1) | 59 (1) | 1 |
| rdf4j-common | RDF4J helper utilities | 158 (3) | 0 | 0 |
| graal-test | Cross-module theory tests: stores x homomorphism variants x chases | 0 | 1902 (16) | 104 |
| graal-coverage | JaCoCo aggregate (no code) | 0 | 0 | 0 |

### Dependency graph (compile scope; "(t)" = test scope only)

```
graal-util  <-- graal-api  <-- graal-core
                                  |
                        graal-homomorphism  (t: io-dlgp)
                          /          \
   graal-forward-chaining            graal-backward-chaining (t: io-dlgp)
          (t: io-dlgp)  \
                   graal-grd  (jgrapht 0.9; also depends on homomorphism)
                        |
               graal-rules-analyser  (fc + grd + jgrapht)
                        |
graal-kb  = api + util + fc + bc + homomorphism + rules-analyser  (t: io-dlgp)

graal-io-{dlgp,owl,ruleml,sparql,rdf} --> api + core + util
rdf4j-common --> core + util ;  graal-io-rdf --> rdf4j-common
rdbms-common --> api, util, core, forward-chaining, homomorphism (+ JDBC drivers)
rdbms-adhoc / rdbms-natural --> rdbms-common ; store-dictionary --> rdbms-adhoc
graal-store-rdf4j --> core + io-sparql + rdf4j-common
graal-store-neo4j --> core + api
graal-builtin-predicates --> core + forward-chaining + kb (+ io-dlgp)
graal-sparql-homomorphism --> homomorphism + store-rdf4j + kb
graal-test (tests only) --> everything, including all stores
```

The module graph has no cycles. The layering api -> core -> homomorphism -> {fc, bc} -> grd -> analyser -> kb is clean at the Maven level.

**Core for a reasoning engine:** util, api, core, homomorphism, forward-chaining, backward-chaining, grd, rules-analyser, kb, plus io-dlgp. That is about **22k SLOC main**. Everything else (RDBMS, Neo4j, RDF4J, SPARQL, OWL, RuleML, key-value, dictionary) is peripheral, about 9k SLOC, and runs on 2014–2016-era dependencies.

---

## 2. Architecture

### Key abstractions (`graal-api/src/main/java/fr/lirmm/graphik/graal/api/`)
- **Term** (`core/Term.java`): interface with sub-interfaces `Constant`, `Variable` and `Literal`. `AbstractTerm` decides the kind with `instanceof`. There is also a deprecated `Term.Type` enum that `compareTo`, `equals` and `hashCode` still use (`core/AbstractTerm.java`). Terms are identified by an arbitrary `Object` identifier. They are not interned; `DefaultTermFactory` allocates a new object on every call.
- **Predicate** (`core/Predicate.java`): a concrete, immutable class (identifier plus arity). It has special constants `EQUALITY`, `BOTTOM` and `TOP`.
- **Atom** (`core/Atom.java`): **mutable** (`setPredicate` and `setTerm`), yet `AbstractAtom` computes `hashCode`/`equals` from its values. Mutating an atom that sits in a hash set or map corrupts that collection.
- **AtomSet** (`core/AtomSet.java`, 30 methods, all `throws AtomSetException`). It is duplicated as **InMemoryAtomSet**, which re-declares the same methods without checked exceptions and returns `CloseableIteratorWithoutException`. **Store** extends AtomSet with batching, `size(p)` and `close()`. The "WithoutException" twin pattern appears 379 times across 80 main files.
- **Rule** (`core/Rule.java`): the body and head are exposed as mutable `InMemoryAtomSet`s, and the rule label is mutable.
- **Substitution** (`core/Substitution.java`): mutable map operations (`put`, `aggregate`, `compose`) plus image creation.
- **Query hierarchy**: `Query`, `ConjunctiveQuery`, `UnionOfConjunctiveQueries`, `ConjunctiveQueryWithNegatedParts`, `EffectiveQuery`/`EffectiveConjunctiveQuery` (a query plus a substitution), and `NegativeConstraint` (a rule whose head is BOTTOM).
- **Homomorphism<T1, T2 extends AtomSet>** (`homomorphism/Homomorphism.java`) together with `ExistentialHomomorphism`, `...WithCompilation`, `Prepared...`, `...Checker` and `...Pattern`. That makes 13 interfaces for one concept.
- **Chase** (`forward_chaining/Chase.java`): `next`/`hasNext`/`execute`. Rules are applied through `RuleApplier` and `DirectRuleApplier`, and the variant is chosen by a `ChaseHaltingCondition`. The `AbstractChase` and `AbstractDirectChase` implementations live in the API module.
- **QueryRewriter**, **RulesCompilation** (pre-compilation of rules with atomic body and atomic head into the unification and homomorphism steps), **GraphOfRuleDependencies**, **KnowledgeBase** with **Approach** (REWRITING_FIRST/ONLY, SATURATION_FIRST/ONLY).
- Iteration uses a home-grown `fr.lirmm.graphik.util.stream.CloseableIterator` (`graal-util/.../stream/CloseableIterator.java`). It does **not** extend `java.util.Iterator`, and `hasNext` throws a checked `IteratorException`.

### Extension points (a real strength)
- The homomorphism engine is a genuine strategy composition: `BacktrackHomomorphism(Scheduler, Bootstrapper, ForwardChecking, BackJumping)` (`graal-homomorphism/.../BacktrackHomomorphism.java`). `graal-test/.../test/TestUtil.java:119-150` wires about 15 combinations and cross-checks them.
- `SmartHomomorphism` dispatches to the first `HomomorphismChecker` that accepts the query type. It is extensible through `addChecker`, and the builtin-predicates and SQL modules plug in this way.
- Chase variants are pluggable through `ChaseHaltingCondition` and `RuleApplier`. Rewriting strategies are pluggable through `RewritingOperator` (`AggregSingleRuleOperator`, `AggregAllRulesOperator`, `BasicAggregAllRulesOperator`). Stores can be swapped through `AtomSet`/`Store`, and index structures through `CurrentIndexFactory`.

### Design quality
- **Interfaces versus implementations:** there are 109 public interfaces for 305 public classes. The split is good in principle. However, the API module also contains implementation (`AbstractAtom`, `AbstractTerm`, `AbstractChase`, `AbstractDirectChase`, `AbstractGraalWriter`).
- **Singletons and global mutable state:** 78 `instance()` singletons. Most are stateless strategies, with these exceptions:
  - `SmartHomomorphism.instance()` holds a mutable `TreeSet` of checkers and is called directly from inside algorithms (`RestrictedChaseHaltingCondition`, `RestrictedChaseRuleApplier`, `AbstractRuleApplier` default constructors, `MFAProperty`). Injected solvers are bypassed in those places.
  - `CurrentIndexFactory.setInstance(...)` (`graal-core/.../atomset/graph/CurrentIndexFactory.java`) is a process-wide switch.
  - `GraalConstant.freshPredicate/freshConstant` use unsynchronised static counters (`graal-core/.../GraalConstant.java`).
  - There are static `DefaultVariableGenerator`s in `backward_chaining/pure/Utils.java:95`, `core/compilation/IDCompilation.java:78`, `core/unifier/UnifierUtils.java:410` and `io/dlp/DlgpParser.java:93` (`freeVarGen`). Their prefixes are built from `Class.hashCode()`, so variable names depend on the process history, and the counters are not thread-safe.
- **Thread-safety:** not designed for concurrency. There are no `volatile` fields and no concurrent collections in the core. The only `synchronized` blocks are on singleton getters and in `BacktrackIterator:98`.
- **Mutability:** Atom, Rule, Substitution, query atom sets and `DefaultLiteral` (commented "not immutable" in `DefaultTermFactory`) are all mutable. `DefaultRule` caches frontier and existentials lazily (`graal-core/.../core/DefaultRule.java:77-78,193-208`) and never invalidates the caches on `setBody`/`setHead` or on mutation of the exposed body and head, so stale-cache bugs are possible.
- **equals/hashCode/compareTo contract violations** (`graal-core/.../core/AbstractRule.java:62-120`):
  - `equals` is structural (label, body, head), but `hashCode` is identity-based. Two equal rules usually get different hashes, which breaks the contract.
  - `compareTo` compares identity hash codes, so it is non-deterministic across runs, and two distinct rules can collide and compare as 0. This ordering keys `TreeMap<Rule, …>` in `BreadthFirstChase.java:154,169` and `FrontierRestrictedChaseHaltingCondition.java:81`. A collision silently merges two rules. Even without a collision, rule application order, and therefore fresh-null naming, is not reproducible between runs.
- **Generics abuse:**
  - `SmartHomomorphism implements HomomorphismWithCompilation<Object, AtomSet>` and dispatches on `Object` with unchecked casts.
  - Parameters such as `Homomorphism<? super Query, ? super T>` and `DirectRuleApplier<? super Rule, ? super T>` appear throughout.
  - `AbstractChase<T1 extends Rule, T2 extends AtomSet>` has type parameters that add little value.
  - Main code has 26 `unchecked` and 4 `rawtypes` suppressions.
- **Telescoping constructors:** `DefaultGraphOfRuleDependencies` has 13 constructors (`graal-grd/.../grd/DefaultGraphOfRuleDependencies.java`), and `BasicChase` and `BreadthFirstChase` have about 10 each.
- **Cross-cutting profiling:** `Profilable` and `getProfiler().isProfilingEnabled()` checks are interleaved in every algorithm loop.
- **God classes:** there are no truly huge ones. The largest are `Rules.java` (634 lines, a static utility grab-bag), `DefaultKnowledgeBase.java` (578 lines, which mixes analysis, strategy selection, saturation, rewriting and unfolding) and `UnifierUtils.java` (427 lines).
- **Split packages** will block JPMS or Kotlin module hygiene:
  - `fr.lirmm.graphik.graal.api.core` and `...graal.core` are shared between graal-api/graal-core and graal-builtin-predicates.
  - `...forward_chaining` is shared between graal-forward-chaining and graal-grd.
  - `...homomorphism` is shared with builtin-predicates.
  - `...store.rdbms.adhoc` is shared between rdbms-adhoc and store-dictionary.
  - graal-grd also puts GRD code in the package `fr.lirmm.graphik.graal.core.grd`.

---

## 3. Algorithms implemented

### Homomorphism / CQ answering (graal-homomorphism)
- A backtracking CSP solver (`BacktrackIterator`, 402 lines) with:
  - variable ordering: `DefaultScheduler`, `ComparableOrderScheduler`, `FixedOrderScheduler`, and BCC (bi-connected component) scheduling (`bbc/BCCScheduler.java`, 483 lines);
  - domain bootstrapping: `Star`, `Stat`, `AllDomain`, `Default`;
  - forward checking: `NFC0`, `NFC2`, `NFC2WithLimit`, `SimpleFC`;
  - graph-based back-jumping.
- Special-case solvers: atomic queries, fully instantiated queries, UCQ, negated parts, and queries with compilation.
- `PureHomomorphismImpl` handles query containment for rewriting.
- **Known defect: `SmartHomomorphism` drops the initial substitution when no compilation is given.**
  - `SmartHomomorphism.java:219-220`: `execute(q, a, compilation, s)` calls `this.execute(query, atomSet)` and loses `s`.
  - `SmartHomomorphism.java:264-265`: `exist(q, a, compilation, s)` calls `this.exist(query, atomSet)`, with the same loss.
  - Reachable from `UnionConjunctiveQueriesSubstitutionIterator.java:132` whenever no explicit homomorphism is set and the compilation is `NoCompilation`. Each sub-query then runs without its partial substitution, which can produce wrong (too many) answers.
- An open FIXME records a known bug: `forward_checking/AbstractNFC.java:170` ("bug with p(X,Y,Z) -> q(X,Y) in the compilation").

### Forward chaining (graal-forward-chaining and graal-grd)
- **Restricted (standard) chase** is the default. `RestrictedChaseHaltingCondition` checks whether the head is already satisfied under the trigger. `RestrictedChaseRuleApplier` encodes the rule as "body AND NOT head" (`RuleWrapper2ConjunctiveQueryWithNegatedParts`). The breadth-first and GRD variants apply triggers in parallel within a round, so redundant atoms can appear inside a round.
- **Semi-oblivious / skolem chase** (`FrontierRestrictedChaseHaltingCondition`):
  - Skolem terms are created as **Constants** whose names are built by string concatenation, `"f_"+ruleIndex+"_"+var+"_X-a_Y-b..."` (`FrontierRestrictedChaseHaltingCondition.java:95-105`).
  - Labels that contain `_` or `-` (common in URIs) can produce equal names for two different frontier mappings. The rule is then not applied, and the chase is incomplete.
  - The rule index comes from the identity-hash `TreeMap` described above.
- **Oblivious chase:** there is no dedicated halting condition; the name appears only in a comment.
- **Core chase:** absent. It existed once, and the tests are commented out (`graal-test/.../forward_chaining/ChaseTest.java:175-210`, `CoreChaseStopCondition`).
- **Equality / EGDs:** not supported. `KBBuilder.add` rejects rules containing `=` (`graal-kb/.../KBBuilder.java:71,143`), and the chase has no term-merging.
- **Chase drivers:**
  - `BasicChase`: naive, all rules each round, with direct in-place application.
  - `BreadthFirstChase`: semi-naive delta only for rules with a single body atom (`linearRuleCheck`).
  - `ChaseWithGRD`: only re-fires rules that depend on the rules fired last round.
  - `ChaseWithGRDAndUnfiers`: uses the GRD unifiers as partial substitutions.
  - `SccChase`: SCC-by-SCC in topological order.
  - `StaticChase`: static helpers.
- **Hazards in the drivers:**
  - `ChaseWithGRD` sets a **hard-coded 1-minute timeout** by default (`graal-grd/.../ChaseWithGRD.java`, `setTimeout(Duration.ofMinutes(1))`) and throws `ChaseException` when it expires. `DefaultKnowledgeBase.saturate()` uses it without overriding the timeout (`graal-kb/.../DefaultKnowledgeBase.java:229`), so saturating a large KB fails after 60 seconds.
  - `AbstractRuleApplier.apply` (used by `BasicChase`) adds atoms to the atom set while it is still iterating lazily over homomorphism results computed on that same set (`rule_applier/AbstractRuleApplier.java`). `DefaultInMemoryGraphStore.atomsByPredicate` iterates live index sets (`core/atomset/graph/DefaultInMemoryGraphStore.java:176-185`), so a ConcurrentModificationException or missed or duplicated triggers are likely. This was not verified by execution.
  - `DefaultInMemoryGraphStore.remove` throws `MethodNotImplementedError` (`:126-129`), so the default store is append-only.
- **Labelled nulls are inconsistent across stores:**
  - `DefaultInMemoryGraphStore` and `Neo4jStore` generate fresh **Variables** named `EE<n>`.
  - `LinkedListAtomSet`, `RDF4jStore` and `NaturalRDBMSStore` generate fresh **Constants** named `EE<n>` (`LinkedListAtomSet.java:76`, `RDF4jStore.java:379`, `NaturalRDBMSStore.java:99`).
  - As a result, null semantics, and therefore query answers, depend on which store is used. None of the generators checks for collisions with user terms that already use the `EE` prefix.

### Query rewriting (graal-backward-chaining)
- **PURE** (piece-unifier based UCQ rewriting, König, Leclère, Mugnier and Thomazo): a breadth-first exploration that keeps only the most general queries (`pure/RewritingAlgorithm.java`), with three aggregation operators and optional ID compilation. Unfolding of compiled rewritings is done by `PureRewriter.unfold`.
- There is no depth or size bound. It loops until the queue empties or the thread is interrupted (`RewritingAlgorithm.java`, `while (!Thread.currentThread().isInterrupted() …)`), so it terminates only for FUS rule sets.
- `Utils.rewrite` returns `null` under a bare `// FIXME` (`pure/Utils.java:146`). Another comment reads "TODO: take account the Substitution?" (`pure/Utils.java:304`).
- The piece-unifier machinery lives in graal-core (`core/unifier/*`, about 1.3k raw lines) and is shared by GRD and rewriting. It has only **9 unit tests** (`graal-core/src/test/.../UnifierTest.java`).

### GRD and termination / decidability analysis
- **GRD** (`DefaultGraphOfRuleDependencies`): pairwise piece-unifier dependency checks, O(n²) unifications, with optional `ProductivityChecker`/`RestrictedProductivityChecker` filters. It uses jgrapht 0.9 `DefaultDirectedGraph<Rule,Integer>`, so it inherits the broken Rule equals/hashCode contract. SCC decomposition is done in `graal-util/.../graph/scc/StronglyConnectedComponentsGraph.java`.
- **Rules analyser (Kiabora)**, `graal-rules-analyser/.../property/`, has 20 properties:
  - syntactic: Linear (atomic body), Disconnected, DomainRestricted, RangeRestricted, FrontierOne, Guarded, FrontierGuarded, WeaklyGuardedSet, WeaklyFrontierGuardedSet, JointlyFrontierGuardedSet, Sticky, WeaklySticky;
  - graph-based: WeaklyAcyclic (position graph), AGRD (acyclic GRD);
  - chase-based: MSA and MFA, each running a skolem chase on the critical instance, which is where the default 1-minute timeout applies;
  - abstract classes: FES, FUS, BTS, GBTS.
- `RuleSetPropertyHierarchy` propagates the properties, and `Analyser` combines FES/FUS/BTS over GRD SCCs (`Analyser.COMBINE_FES/FUS`). `DefaultKnowledgeBase` uses this to decide which rules to saturate and which to rewrite.
- `MFAProperty.check` returns 0 ("unknown") on any chase or homomorphism error and only logs a warning (`MFAProperty.java:125-135`, "TODO throw Error").
- `Rules.criticalInstance` uses only predicates that appear in rule bodies, and its comment doubts the definition (`Rules.java:610`). This is fine for MFA and MSA triggering but is undocumented.

### Correctness-critical code with weak coverage
| Area | Main code | Tests |
|---|---|---|
| Chase drivers and halting conditions | about 740 SLOC fc + 750 SLOC grd | 3 tests in fc, about 7 theories in `graal-test/.../ChaseTest.java`, about 22 in grd (mostly with compilation). `BasicChase` is tested only in the RDBMS and SPARQL tests. No test of the semi-oblivious chase on non-trivial rule sets, and no termination tests. |
| Piece-unifiers (`core/unifier`) | about 1.3k lines | 9 tests |
| PURE rewriting | 856 SLOC | 15 regression-style theories (issue22, issue34, …), no cross-check against the chase |
| MFA/MSA/Sticky/… | 2.3k SLOC | 28 tests; FES, FUS, BTS, AGRD and JointlyFG have no dedicated test |
| KB approach selection | 558 SLOC | 35 tests |
| RDBMS, Neo4j and RDF4J stores | about 4.3k SLOC | covered only through the graal-test theories and 3 RDBMS tests |

---

## 4. Tests
- **Counts:** about 513 annotated tests (`@Test` about 380, `@Theory` 134) in 69 test files. There is no `@Ignore`, but several are commented out (for example the core-chase tests and a `/*@Test FIXME` in DLGP). JUnit 4.12 is used throughout.
- **Kinds:**
  - Unit tests: core (91), dlgp (65), owl (74, all in one file of 1875 SLOC), sparql (13).
  - Theory-based integration: `graal-test` (104 theories). They run every query and chase test over all stores (in-memory graph, linked list, AdHoc RDBMS/HSQLDB, Natural RDBMS, Neo4j, RDF4J) and about 15 homomorphism configurations (`graal-test/.../test/TestUtil.java:119-150`). This combinatorial differential testing is the most valuable asset in the test suite.
  - Regression tests named after issue numbers (backward chaining, `ConjunctiveQueryFixedBugTest`).
- **Not tested at all:** graal-api (interfaces only), io-rdf, io-ruleml, store-rdf4j, store-neo4j (only indirectly), rdf4j-common, and graal-util streams and profiler (4 tests total).
- **Missing kinds:**
  - property-based or randomised tests, such as "chase then query equals rewrite then evaluate" or termination checks on known classes;
  - benchmark suites (no JMH, and no ChaseBench or iBench datasets);
  - explicit tests of chase variant semantics (for example, the restricted chase must not fire on a satisfied trigger, beyond 2–3 tiny cases in `FrontierRestrictedChaseHaltingConditionTest`).
- **Apparent quality:** the assertions are meaningful (they compare expected sets of atoms or queries). Heavy reliance on `SmartHomomorphism` inside the tests hides which solver actually runs. The Neo4j and temporary-file setup in `TestUtil` static initialisers makes the tests environment-dependent.

---

## 5. Code smells and technical debt
- **Markers in main code:** TODO 34, FIXME 13, XXX 1. Test code: FIXME 2. The notable ones are listed below.
  - `SqlUCQHomomorphism.java:135`: "FIXME manage the substitution part".
  - `AdHocConjunctiveQueryTranslator.java:224` and `DictionaryAdHocConjectiveQueryTranslator.java:82`: "if arity = 0 -> crash".
  - `RdbmsConstantGenenrator.java:93` and `RdbmsVariableGenenrator.java:93`: `value = 0; // FIXME`.
  - `AbstractNFC.java:170`: a known compilation bug.
  - Three stubs in `graal-sparql-homomorphism` marked "TODO Auto-generated method stub".
- **Unimplemented methods:** `MethodNotImplementedError` in `DefaultInMemoryGraphStore.remove`, `Neo4jStore:238`, `PostgreSQLDriver:199`, `RuleWrapper2ConjunctiveQueryWithNegatedParts:86` and `IDConditionImpl:349`.
- **Deprecated APIs:** 38 `@Deprecated` in main code. The main case is the `Term.Type` enum, still used by `AbstractTerm.equals/hashCode/compareTo` (20 `getType()` calls), and `AtomSet.getTerms(Type)`/`isSubSetOf`.
- **Error handling:**
  - 30 `catch (Exception` in main code, which wrap everything into `ChaseException`, often with an empty message: `new ChaseException("", e)` in `SccChase.java:167-179` and `RuleApplicationException("", e)` in `RestrictedChaseRuleApplier`.
  - 37 `throw new Error(...)` in main code (for example `NegFilter.java:75-76` prints the stack trace and then throws `Error`).
  - 14 `printStackTrace` calls and 20 `System.out` calls in main code, including 9 in `BCCScheduler`.
  - `QueryUnifier.toString` can return `null`.
  - 278 `throws AtomSetException` declarations. The checked-exception design forced the duplicate `*WithoutException` API.
- **Logging:** SLF4J 1.7.7 is used in 25 classes. The SPARQL I/O module pulls in `log4j`/`slf4j-log4j12` 1.x (`graal-io-sparql/pom.xml:49-68`) and RDF4J pulls in `commons-logging`. Log4j 1.x is EOL and has known CVEs.
- **Security:** the AdHoc RDBMS translator builds SQL by string concatenation with constant identifiers (`AdHocConjunctiveQueryTranslator.java:128`, `" = '" + … + "'"`). This allows SQL injection and breaks on labels that contain a quote.
- **Duplicated code:** 199 duplicated 12-line windows. Duplication overall is moderate. The main pairs are:
  - `DictionaryAdHocConjectiveQueryTranslator` and `AdHocConjunctiveQueryTranslator` (46 windows);
  - `MFAProperty` and `MSAProperty` (30);
  - `IndexedByBody` and `IndexedByHeadPredicatesRuleSet` (20);
  - `DlgpWriter` and `AbstractSparqlWriter` (19, admitted at `AbstractSparqlWriter.java:188`);
  - `DefaultAtom` and `AtomEdge` (18);
  - `AbstractChase` and `AbstractDirectChase` (13).
  - `SmartHomomorphism` repeats the same dispatch loop 8 times.
- **Dead or low-value modules:** `graal-keyvalue` (experimental, 141 SLOC), `graal-sparql-homomorphism` (misplaced source root, stubs), `graal-io-ruleml` (writer only, untested), `graal-coverage` (tooling). The Neo4j 2.3 embedded store is obsolete and will not survive a dependency upgrade.
- **Obsolete dependencies:**
  - jgrapht 0.9.0 (current 1.5.x, with API breaks);
  - rdf4j 1.0.3 (current 4.x/5.x);
  - Jena 2.13 (`com.hp.hpl.jena`; current 5.x under `org.apache.jena`, a full rewrite);
  - OWLAPI 4.0.1;
  - Neo4j 2.3.11;
  - MySQL connector 5.1.6, Postgres 9.1 JDBC, HSQLDB 2.3.4;
  - commons-lang3 3.5, JUnit 4.12, jacoco 0.7.7, and a findbugs plugin (dead project).
  - `dlgp2-parser` is an external LIRMM artifact; the grammar is not in this repository.
- **Documentation:** `docs/` contains only `docs/dev/RELEASE-PROCESS.txt`, and the README covers only building. Javadoc coverage: about 132 of 452 public types (29%) have a non-trivial class-level javadoc, and only about 15% of public methods (399 of 2631) are preceded by one. Algorithmic references (papers) are rarely cited in the code.
- **Other smells:**
  - Misspelled class names are part of the API: `ChaseWithGRDAndUnfiers`, `RdbmsConstantGenenrator`, `DictionaryAdHocConjectiveQueryTranslator`, `DictionnaryMappingException`.
  - The package `org.graal.store.dictionary` breaks the `fr.lirmm.graphik` convention.
  - `prepare_ant.sh` and `scripts/*.xml.prefix` provide an alternative Ant build.

---

## 6. Relevance of a Kotlin conversion

### Modules that would benefit most
- **graal-api and the graal-core model** (Term, Constant, Variable, Literal, Predicate, Atom, Rule, Substitution, the query types):
  - Terms can become a **sealed interface** with data or value classes, replacing the `instanceof`-plus-deprecated-`Type` enum mix and fixing the equals, hashCode and compareTo issues.
  - Queries (CQ, UCQ, CQ with negation, effective query) can become a sealed hierarchy, which removes `SmartHomomorphism`'s dispatch on `Object` and the unchecked casts in favour of an exhaustive `when`.
  - Immutable `Atom`/`Rule` data classes remove the stale `DefaultRule` caches (use `by lazy` on immutable data) and the hazard of mutable hash keys.
- **Checked-exception duplication:** Kotlin has no checked exceptions, so `AtomSet` and `InMemoryAtomSet` and the `CloseableIterator` / `...WithoutException` pairs can collapse into one API using `Sequence` or `Iterator` plus `AutoCloseable`/`use {}`. This removes a large share of the graal-util stream code.
- **graal-kb, graal-rules-analyser, graal-io-dlgp writer, builders and factories:**
  - Named and default arguments replace the 10–13 telescoping constructors.
  - An `object` for true singletons, with explicit dependency injection for stateful ones.
  - The 20 analyser properties fit an enum or sealed-object hierarchy well.
- **Null-safety** fixes existing null returns (`Utils.rewrite` returning null, `QueryUnifier.toString`, `DefaultTermFactory.createTerm` returning null, and the `null` Rule in `ChaseWithGRD` queues).

### Modules that should stay in Java, or be ported with care
- **graal-homomorphism's backtracking inner loops** (`BacktrackIterator`, `BacktrackUtils`, `AbstractNFC`/`NFC2`, `BCCScheduler`) and the index structures in `DefaultInMemoryGraphStore`/`AbstractTermVertex`. These are allocation- and boxing-sensitive: `Var[]` arrays, `Map<Variable,Integer>`, candidate sets. A Kotlin port is fine if it avoids lambda-heavy collection chains, `Pair` allocations and boxed `Int` in generics, but it brings no semantic gain. Keeping it in Java behind a clean Kotlin-facing interface is reasonable.
- **Peripheral stores** (RDBMS, Neo4j, RDF4J): convert them only if they are kept at all. They mainly need a dependency upgrade, or should be dropped.

### Risks
- **Java interop during a partial conversion:**
  - Kotlin-to-Java calls lose checked-exception declarations unless `@Throws` is added.
  - Platform types (`Term!`) weaken null-safety at the boundary.
  - Sealed hierarchies cannot be extended from Java.
  - `object` singletons need `@JvmStatic`/`INSTANCE` for Java callers.
- **Generics:** declaration-site variance (`in`/`out`) has to be designed rather than transliterated from `? super Query, ? super T`. The `Homomorphism<Object, AtomSet>` dispatch pattern should be redesigned, not ported.
- **Equality semantics:** data-class `equals`/`hashCode` changes behaviour relative to the current identity-hash Rules. Tests that depend on duplicate rules being distinct, or on the TreeMap order, will change.
- **Performance:** Kotlin's default immutable collections are Java collections behind read-only views, so there is no cost there. The risk is idiomatic code that allocates per candidate in the homomorphism loop. Benchmarks must be set up before converting, and there are none today.
- **Build:** the multi-module Maven build plus kotlin-maven-plugin, mixed-source compilation order, and the split packages need to be resolved first.

---

## 7. Overall assessment

**Strengths:**
- A rich, theory-grounded algorithm set: PURE rewriting, piece-unifiers, GRD, an extensive decidability analyser with FES/FUS/BTS combination, and a configurable CSP-style homomorphism engine.
- Sensible module layering with no Maven cycles.
- An interface-first API.
- A useful combinatorial theory test harness.

**Weaknesses:**
- Semantic inconsistencies at the heart of the model: nulls are Constants in some stores and Variables in others; skolem naming can collide; the Rule equals, hashCode and compareTo are broken.
- Global mutable singletons and no thread-safety.
- Hidden defaults with correctness impact: a 1-minute chase timeout, the substitution dropped in `SmartHomomorphism`, and a default store that does not support `remove`.
- Thin tests on the chase and the unifiers.
- No core chase and no equality support.
- Dependencies are 7–10 years stale across the whole periphery.
- Copyleft CeCILL licence.

**Score as a foundation: 5/10.**
- The core (about 22k SLOC across util, api, core, homomorphism, fc, bc, grd, analyser, kb and dlgp) is worth keeping as an algorithmic reference and as a starting codebase.
- The data model (terms, atoms, rules, nulls, equality contracts) should be redesigned rather than refurbished.
- The storage periphery should be dropped or rewritten.
