# Nemo (knowsys/nemo) as engine or blueprint for a solid existential-rules reasoner

Evaluation date: 2026-09-30. Nemo checkout: `main` @ `e578c28` (2026-08-20), version `0.10.2-dev`.
Local clone of Nemo (not included here). Other material read: the Nemo wiki, the Nemo docs and the raw benchmark spreadsheets of the ICLP 2023, KR 2024 and ESWC 2026 papers (nemo-examples).

**Access limits.** The egress proxy blocked iccl.inf.tu-dresden.de, proceedings.kr.org, arxiv.org and ceur-ws.org, so I could not read the paper PDFs. All performance numbers below come from the authors' own raw result files in `knowsys/nemo-examples/evaluations/*`. Qualitative claims come from the code and the official docs.

---

## 0. Verdict

**Stay on the JVM (Kotlin) for the reasoner core. Treat Nemo as architectural inspiration and as an optional back-end for large Datalog workloads. Do not adopt it as the core, and do not plan a Rust rewrite now.**

The reasoning, in short:

* Nemo is an excellent engine for **bottom-up, set-at-a-time materialisation**: columnar tries, leapfrog-style joins, semi-naive evaluation and the Datalog-first restricted chase. On its home turf it beats VLog and matches Soufflé.
* It has none of your signature techniques: no piece-unifiers, no GRD, no query rewriting, no backward chaining, and no homomorphism engine for small conjunctive queries. Its engine design (immutable sorted tries, whole-program materialisation, one-shot `initialize → execute`) does not suit the "many small KBs, answer queries fast, sometimes rewrite" workload of an agent's reasoning layer.
* Your added value (GRD/SCC, PURE rewriting, BCC-based homomorphisms, rule-class analysis) is mostly **symbolic, query-sized computation**. There, Rust's performance edge over a well-written JVM is small, and it is dwarfed by algorithmic choices.

Suggested plan:

1. Build the Kotlin core with a Nemo-inspired physical layer: dictionary-encoded `IntArray`/`LongArray` columns, sorted tries and a WCOJ join for saturation.
2. Keep an **optional "Nemo back-end" exporter**: emit `.rls` and call `nmo` or a uniffi/JNI binding for huge, Datalog-heavy materialisations. That covers the "RDFox-scale" need without RDFox.
3. Upstream the pieces Nemo lacks and you know well: GRD-based scheduling and chase-termination analyses. Nemo already has an unmerged `static_checks` branch you could collaborate on.

Revisit Rust only if profiling of the Kotlin engine on the real agent workload shows memory or throughput limits that off-heap columns cannot fix.

---

## 1. Architecture

### 1.1 Crates

Source: `Cargo.toml` plus a line count over `*.rs`, about 90 kLOC in total.

| crate | LOC | role |
|---|---|---|
| `nemo-physical` | ~39.7k | Storage and execution. Dictionaries, columns, tries, trie-scan operators (join, union, subtract, null creation, aggregate, functions), execution plans, the database/table manager. |
| `nemo` | ~46k | The logical layer. Parser, rule model and transformation pipeline, normalisation, planning (per-rule plans), rule-selection strategies, I/O formats (DSV, RDF formats, JSON, SPARQL), tracing. |
| `nemo-cli` | ~0.9k | The `nmo` binary. |
| `nemo-python` | ~0.5k | PyO3 bindings: load, reason, iterate results, trace. |
| `nemo-wasm` | ~1.4k | wasm-bindgen bindings; powers the in-browser demo. |
| `nemo-language-server` | ~1.1k | LSP for editors. |

The project requires a **nightly** toolchain (`rust-toolchain.toml`). It uses `#![feature(...)]` in `nemo/src/lib.rs` and `nemo-physical/src/lib.rs`, for example `iter_intersperse`, `str_from_raw_parts` and `slice_swap_unchecked`. The public API is `async`, via `tokio` and `async-trait` (`nemo/src/api.rs`, `execution_engine.rs`).

### 1.2 Data model

* **Dictionary encoding.** `nemo-physical/src/dictionary/` contains a *meta dictionary* (`meta_dv_dict.rs`) that dispatches to per-kind dictionaries: IRIs, strings, lang-strings, "other" typed literals, tuples and maps (`tuple_dv_dict.rs`), and nulls.
* **Storage types.** Integers and floating-point numbers are stored unboxed and are not dictionary-encoded. `datatypes/storage_type_name.rs` defines `Id32`, `Id64`, `Int64`, `Float` and `Double`, and a column can mix storage types.
* **Labelled nulls.** Nulls are plain dictionary ids from `NullDvDictionary` (`dictionary/null_dv_dict.rs`), a counter `unused_ids: 0..` with `fresh_null()`. The trie operator `TrieScanNull` (`tabular/operations/null.rs`) appends fresh-null columns to a trie while it is scanned. Each existential either gets a `FreshNull` or a `RepeatNull` (the same null repeated). So nulls cost the same as constants in joins, which is ideal.
* **Tables.** Tables are immutable **column-oriented tries** (`tabular/trie.rs`, `columnar/intervalcolumn*.rs`). One layer is stored per attribute, with interval columns for child ranges. Columns are adaptive, either `vector` or **RLE** (`columnar/column/rle.rs`, `columnbuilder/adaptive.rs`).
* **Orderings.** Each predicate can hold several sorted permutations (orders) on demand (`management/database/order.rs`, `permutator.rs`).
* **Fragmentation.** Each rule application produces a new sub-table ("step") per predicate. Fragments are periodically unioned (`MAX_FRAGMENTATION`, `ExecutionEngine::defrag` in `nemo/src/execution/execution_engine.rs`).

### 1.3 Execution model

* **Joins.** Joins run as **leapfrog/trie joins over a global variable order**. `columnar/operations/join.rs` (`ColumnScanJoin`) is a leapfrog intersection of sorted column scans, and `tabular/operations/join.rs` (`TrieScanJoin`) lifts it to tries, layer by layer. This is the Leapfrog-Triejoin family, i.e. worst-case-optimal in spirit.
* **Variable order.** Variable orders are computed heuristically in `execution/planning/analysis/variable_order.rs`. It was recently simplified to "only produce one variable order" for speed (commit #797, 2026-08).
* **Semi-naive evaluation, one rule at a time.** Each *step* applies exactly one rule (`ExecutionEngine::step`). Semi-naive deltas are the step ranges since that rule's `step_last_applied`. See `planning/operations/join_seminaive.rs`, `join_cartesian.rs` and `union.rs` with `UnionRange`.
* **Single-threaded.** I found no `rayon` or thread use in the engine. The papers' numbers are single-core.
* **Rule scheduling.** Scheduling uses `StrategyStratifiedNegation<StrategyRoundRobin>` (`execution/selection_strategy/*`, alias `DefaultExecutionStrategy` in `execution.rs`):
  * A **rule-level dependency graph** is built from *predicate names only*. It has three edge labels: `Positive`, `Negative`, and `Restrain` (from Datalog rules to existential rules that share a head predicate).
  * The graph is split into **SCC "topological components"**, with negative edges treated as strict and `Restrain` edges as weak. Components run in order; inside a component, rules run round-robin until a fixpoint.
  * **So Nemo already has SCC-driven scheduling, but on a predicate graph, not a piece-unifier GRD.**
  * The scheduler sits behind a trait (`RuleSelectionStrategy::new(Vec<&NormalizedRule>)` / `next_rule(new_derivations)`), so it is pluggable.

### 1.4 Chase variant and termination

* **Chase variant.** Nemo uses the **Datalog-first restricted (standard) chase** (`nemo-doc/.../intro/tour.mdx`, "Existential rules").
  * Implementation: `planning/strategy/forward/restricted.rs`, `operations/restricted_head.rs`, `restricted_frontier.rs` and `restricted_null.rs`.
  * The satisfaction check is *set-at-a-time*. Nemo joins the head atoms against existing tables, projects to the frontier ("satisfied matches"), subtracts from the new body matches, and only then creates nulls.
  * There are regression tests in `resources/testcases/regression/restricted_chase/*`, for example `multipieces`, `multinulls` and `block*`.
* **Skolem chase.** There is only a program transformation, `rule_model/pipeline/transformations/skolem.rs`, which rewrites existentials to function terms `_SKOLEM_n(frontier)` stored as tuple values. It is not the default and is not referenced by the engine or the CLI in `main`. It appears to serve the static-checks work (MSA).
* **Oblivious, core and parallel chase.** None of these exist.
* **Termination.** **No termination guarantee or detection in `main`.** There are no class checks, no depth or size limits and no timeouts (`nemo-cli/src/cli.rs` has no such option). A non-terminating program runs until it runs out of memory.
  * Termination analysis lives on **unmerged branches**: `feature/static_checks` (last commit 2026-08-23), `feature/static_checks-mfa` and `feature/static_checks-msa`. They add a crate `nemo-static-checks` with `is_weakly_acyclic`, `is_jointly_acyclic`, `is_mfa`, `is_msa`, `is_dmfa`, `is_rmfa`, `is_mfc`, `is_drpc`, `is_guarded`, `is_sticky`, `is_weakly_sticky`, `is_shy` and others (`nemo-static-checks/src/static_checks/rules_properties.rs`), plus a test corpus in `resources/testcases_static_checks/`.
  * Those branches were written by a student (Louis Gröger). Merge status is unknown.
* **No equality or EGDs.** There is no `owl:sameAs`-style equality reasoning (grep for egd/sameAs/equality finds nothing). RDFox has this.

---

## 2. Features

| Feature | Status in Nemo | Evidence |
|---|---|---|
| Existential rules | Yes, Datalog-first restricted chase | `tour.mdx`; `planning/strategy/forward/restricted.rs` |
| Stratified negation | Yes, `~atom`; stratification over the rule graph; error `NonStratifiedProgram` | `strategy_stratified_negation.rs` |
| Negation with nulls | Nulls are ordinary values: `~p(x)` checks absence in the computed restricted chase, stratum by stratum. Existential rules are ordered by `Restrain` edges, so the result is only as well defined as the (Datalog-first, stratified) chase order. There is no formal "negation over all models" semantics. | same file, `EdgeLabel::Restrain` |
| Aggregation | `#count` (distinct tuples), `#sum` (distinct, with optional distinguishing vars), `#min`, `#max`. At most one aggregate per rule, in the head. Stratified: aggregate bodies are treated as negative edges. | `nemo-doc/.../reference/aggregates.mdx`; `rule_model/components/term/aggregate.rs`; `strategy_stratified_negation.rs` |
| Datatypes and built-ins | RDF-compatible values: IRIs, strings, lang strings, xsd numerics, booleans, tuples, maps. Arithmetic, string and comparison built-ins. | `nemo-physical/src/datavalues/*`, `nemo-doc/.../reference/builtins.md` |
| Import/export | CSV/TSV/DSV, N-Triples, Turtle, RDF/XML, N-Quads, TriG, JSON, **SPARQL endpoints** (with semi-naive, binding-aware SPARQL, ESWC 2026). Gzip. | README; `nemo/src/io/formats/*` |
| Tracing / explanations | Yes, a strong point. Fact-level traces (`--trace`, `--trace-all-idb-facts`), tree and node **proof queries** (`execution_engine/tracing/{simple,tree_query,node_query}.rs`) that re-derive using the recorded rule history. Traces feed the Lean certifier (ITP 2025) and proof visualisation (XLoKR 2024, TGD/EDBT-WS 2026). | code plus `intro/research.md` |
| Query answering vs materialisation | **Full materialisation only.** Output predicates restrict what is computed (`TransformationActive` drops rules irrelevant to outputs), but there is no goal-directed evaluation, no magic sets and no top-down evaluation. | `rule_model/pipeline/transformations/active.rs` |
| Query rewriting / backward chaining | **None** (grep for magic/backward/rewrit finds only unrelated hits) | — |
| GRD via piece-unifiers | **None.** Only the predicate-level rule graph above. | — |
| Rule-class analysis (acyclicity etc.) | Unmerged branches only (see 1.4) | branch `feature/static_checks` |
| Incremental maintenance (add/delete facts) | No. An engine is `initialize` then `execute` once. The "incremental" transformation is about SPARQL import inlining, not DRed. | `execution_engine.rs`; `transformations/incremental.rs` |
| Equality / EGDs | No | — |

---

## 3. Performance evidence

### 3.1 KR 2024

Paper: "Nemo: Your Friendly and Versatile Rule Reasoning Toolkit", KR 2024, pp. 743–754.

* PDF: https://proceedings.kr.org/2024/70/kr2024-0070-ivliev-et-al.pdf (could not be fetched here).
* Raw data: `nemo-examples/evaluations/kr2024/runtimes.ods`.
* Setup: wall-clock seconds, averaged over 3 runs, loading included. Machine not recorded in the spreadsheet.

| Benchmark | Nemo 0.5.1 | Nemo WASM (Firefox) | VLog 1.3.6 | Soufflé 2.4.1 compiled | Soufflé interp. | Gringo 5.7.1 |
|---|---|---|---|---|---|---|
| Galen (EL) | **5.5** | 8.8 | 42.7 | 10.4 | 13.7 | 22.0 |
| SNOMED (EL) | **76.4** | 129.6 | OOM | 114.9 | 157.5 | 95.4 |
| Doctors 1M (ChaseBench) | 3.3 | 5.8 | 2.9 | **2.0** | 2.4 | 7.1 |
| Ontology-256 (ChaseBench) | **14.2** | 29.2 | 22.2 | 14.9 | 19.2 | 23.9 |
| LUBM 1k (ChaseBench) | 167.6 | 4 GB WASM limit | 188.4 | **145.7** | 170.4 | OOM |
| Deep 100 (ChaseBench) | 3.1 | 5.4 | 23.2 | timeout | 27.7 | **0.2** |
| Deep 200 (ChaseBench) | **8.0** | 13.9 | timeout | timeout | timeout | timeout |

How to read this table:

* Soufflé and Gringo have no native existentials, so they ran translated programs. The different fact counts in the sheet (for example LUBM: 239M vs 187M facts) show they computed a different, Skolem-like result. The comparison is therefore not apples to apples on existential benchmarks.
* **RDFox does not appear** in any of the three evaluation folders (ICLP 2023, KR 2024, ESWC 2026). Most likely this is because of its licence. I found **no published head-to-head of Nemo vs RDFox**.

### 3.2 ICLP 2023

Paper: "Nemo: First Glimpse of a New Rule Engine", EPTCS 385. arXiv: https://arxiv.org/abs/2308.15897. Raw data: `evaluations/iclp2023/results.ods`.

Nemo 0.2.0 vs VLog, in ms:

| Benchmark | Nemo 0.2.0 | VLog |
|---|---|---|
| Doctors 1M | 3,227 | 2,510 |
| Ontology-256 | 13,419 | 22,489 |
| Deep 200 | 5,135 | OOM |
| Galen | 3,622 | 45,186 |
| SNOMED | 62,080 | OOM |
| LUBM 1k | 163,310 | 199,407 |

### 3.3 ESWC 2026

Paper: "SPARQLing Datalog for Rule-Based Reasoning over Large Knowledge Graphs". Raw data: `evaluations/eswc2026/results.ods`.

This paper measures SPARQL-sourced reasoning against Wikidata, comparing current Nemo with Nemo 0.8 and Rulewerk/VLog. Examples (median seconds): "anc" 23.7 vs 235.2 for Rulewerk; "dsj" 32.5 vs 89.0. This is about network/federated import, not core chase speed.

### 3.4 Performance takeaways

* Nemo is **state of the art among open-source chase engines**: at least as fast as VLog and much more robust (no OOM on SNOMED or Deep 200).
* It is comparable to compiled Soufflé on plain Datalog, single-threaded.
* Its WASM build is only about 1.6–2× slower than native.
* **No evidence either way against RDFox.** RDFox is multi-threaded and incremental, so expect RDFox to win on multi-core machines and on updates.

### 3.5 Small-KB latency (own measurement)

See section 7: about 10 ms of reasoning on a 400-fact KB. Inline program facts cost about 0.3 ms each at load time; CSV import is about 1000× cheaper.

---

## 4. Maturity

* **Licence:** Apache-2.0 OR MIT. This is ideal: embeddable and forkable with no copyleft.
* **Releases:** v0.5.0 (2024) … v0.8.0 (2025-07-15), v0.9.0 (2025-10-21), v0.9.1 (2025-10-23), v0.10.0 (2026-03-20), v0.10.1 (2026-08-01). That is roughly 3 releases a year.
* **Stability claim:** the README still says *"Nemo is in heavy development and the current releases should still be considered unstable."* The pre-1.0 API and file syntax have changed across releases.
* **Activity, last 12 months (2025-09-30 → 2026-09-30):**
  * 303 commits on `main`, including merge commits.
  * About 12 distinct authors, but concentrated: Maximilian Marx 168, Alex Ivliev 101, then single digits (Krötzsch 6, Gerlach 3, ...).
  * **Bus factor of about 2**, which is typical for an academic project (TU Dresden, Krötzsch's group).
* **GitHub:** about 300 stars, 21 forks, 70 open issues, 87 remote branches (many experimental), GitHub Actions CI (`build.yml`, `pr.yml`, `release.yml`, nix/docker).
* **Tests:** about 385 `#[test]` functions plus 84 end-to-end `.rls` test programs with expected outputs (`resources/testcases/**`). Reasonable, but there is no systematic chase-correctness corpus beyond ChaseBench-style examples, and no fuzzing. `miri` is listed in the toolchain components; 94 `unsafe` occurrences in `nemo-physical`.
* **Toolchain risk:** a nightly Rust toolchain is required.
* **APIs:**
  * Rust library: `nemo::api::{load, load_string, reason, output_predicates}`, plus `ExecutionEngine::{initialize, execute, predicate_rows, trace_*}`. It is async.
  * Python: PyO3, `nemo-python`, with load/reason/results/trace.
  * WASM: `nemo-wasm`, wasm-bindgen.
  * LSP.
  * **No JVM binding.**
* **JVM/Kotlin binding feasibility:**
  * **uniffi** (Mozilla) generates Kotlin bindings over JNA directly from annotated Rust. It is the lowest-effort path: wrap a sync facade (`block_on` over the async API) exposing `load_string`, `reason`, `rows(predicate)` and `trace(fact)`.
  * **Panama FFM** (stable since Java 22) plus `cbindgen`-generated C ABI is the fastest path for bulk row transfer, because rows can be read as `MemorySegment` without copies.
  * JNI via the `jni` crate also works.
  * In every case you ship a native library per platform and get one engine instance per KB. The per-call FFI overhead is negligible next to reasoning, but converting results to JVM objects is not free for large answers.

---

## 5. Fit with your must-have techniques

| Technique | In Nemo? | How it could be added | Difficulty |
|---|---|---|---|
| **GRD via piece-unifiers** | No; a predicate-level graph only | Implement piece-unifier dependency (Baget et al.) over `NormalizedRule`, then a new `RuleSelectionStrategy` that builds components from the GRD instead of predicate edges. The trait is clean. Details below the table. | **Moderate.** About 1.5–3 kLOC Rust: unifier, GRD, strategy. Upstream acceptance plausible if benchmarked. |
| **SCC-driven chase** | **Yes, already** (predicate graph) | Comes almost for free once the GRD above exists. Also possible: Datalog-first inside components, and skipping rules whose GRD predecessors produced no delta. Semi-naive already makes such rules cheap. | Low, after the GRD |
| **UCQ query rewriting (PURE)** | No | Orthogonal to Nemo's engine. Write the rewriter separately (in Kotlin or Rust). Each rewritten CQ becomes a Datalog rule `ans(x̄) :- body` evaluated by Nemo over the base facts. Nemo becomes the "SQL engine" of the OBDA-style pipeline. Rewriting *inside* Nemo (a `ProgramTransformation`) is possible but unnatural. | Low to integrate, high to build (which you already know) |
| **Backward chaining / goal-directed** | No | The engine is bottom-up, set-at-a-time, with immutable tries. Top-down (SLD/QSQ) does not fit. A **magic-sets `ProgramTransformation`** is conceivable for Datalog, but magic sets plus existentials plus restricted chase is semantically delicate: the restricted chase is order-sensitive. | High; a research project |
| **Homomorphism search exploiting bi-connected components** | No; joins are WCOJ over one variable order | Nemo's *materialisation* does not need it: triggers are found set-at-a-time. You need BCC-based backtracking for **CQ containment and UCQ minimisation in rewriting**, for **core computation**, and for **tuple-at-a-time entailment checks on small KBs**. Those live outside Nemo's physical layer. In a WCOJ engine the analogue is decomposition-guided variable ordering (GHD/Yannakakis). | Separate component; not a Nemo change |
| **Termination / class analysis** | Unmerged branches (WA, JA, MFA, MSA, guardedness, stickiness...) | Collaborate on `feature/static_checks`; add GRD-based criteria (e.g., GRD acyclic ⇒ FES∩BDD; per-SCC class combination as in Graal/Kiabora). | Moderate; a good contribution target |
| **Stratified negation, then aggregation** | Yes / yes (limited) | Already there. Aggregates: one per rule, distinct semantics. | — |

On the GRD strategy row, two details matter:

* **Normalised rules.** The GRD must be computed on the *normalised* rules, which may be split and rewritten. Build it on the original `ProgramHandle` and map through `RuleIdTranslation`, or compute directly on `NormalizedRule`.
* **Hard-coded default.** `DefaultExecutionStrategy` is a type alias, so either make it configurable via `ExecutionParameters` or change the alias.

**Overall:** Nemo could host GRD/SCC scheduling and termination checks. The rewriting and homomorphism parts of your design would sit **beside** Nemo, not inside it. Contributing is therefore partial: the upstream-able part is about 20–30% of your vision.

---

## 6. Rust vs Kotlin/JVM for this engine

### 6.1 Where Rust wins, concretely

* **Memory layout.** Nemo's tries are `Vec<u32>`/`Vec<u64>` columns with RLE, cache-friendly and without per-object headers. On the JVM you get the same with `IntArray`/`LongArray` (or off-heap `MemorySegment`), and you **must** code that way: `data class Atom(val pred: Predicate, val terms: List<Term>)` costs 50–100+ bytes per fact versus 4–8 bytes per column cell. The gap is not Rust vs JVM, it is objects vs arrays. Rust makes the array style the default; Kotlin makes the object style the default.
* **Specialisation.** Monomorphised generics (`ColumnScanJoin<T: ColumnDataType>`) give specialised inner loops for u32/u64/i64/f64. On the JVM, generics over primitives box. You would hand-specialise, or use one `LongArray` encoding for everything, which is simple and good enough.
* **Predictable latency.** There are no GC pauses. For huge materialisations (tens of GB) the JVM needs tuning (ZGC/Shenandoah make pauses sub-ms, at a throughput cost), or off-heap storage.
* **Deployment.** A single static binary plus WASM (in-browser at about 1.7× native, per the KR 2024 sheet) plus Python. On the JVM, GraalVM native-image is the counterpart, with caveats.

### 6.2 Where the JVM is fine or better

* **Homomorphism search on small queries.** This is backtracking over a few dozen atoms, dominated by index lookups and the quality of the search order (BCC, ordering heuristics). With escape analysis plus reusable substitution arrays (`IntArray` indexed by variable id, undo trail), the JIT gets within about 1.2–2× of Rust. With careless allocation (a new `Map<Var, Term>` per step) you lose 5–20×. Discipline matters more than language.
* **Rewriting (PURE).** Rewriting explodes combinatorially. The cost is in the number of CQs generated and in containment checks. Algorithmic pruning (piece-unifier restrictions, subsumption checks with good indexing) matters by orders of magnitude; the language by maybe 2×.
* **Dev velocity and maintainability.**
  * Kotlin: you already have Graal's design knowledge and the JVM ecosystem (JUnit/Kotest, property testing, JMH, async-profiler, JFR).
  * Rust: the borrow checker is a real tax on graph-heavy, mutable, cyclic structures (rule graphs, unifier structures, substitutions with sharing). Nemo itself uses `RefCell`/`UnsafeCell` inside trie scans (`tabular/operations/join.rs`, `null.rs`), requires nightly, and has 94 `unsafe` sites in `nemo-physical`.
  * A solo Rust rewrite of Graal-level scope (chase variants, GRD, rewriting, negation, aggregation, I/O) is realistically **1–2 person-years**.
* **Agent integration.** Most agent stacks are Python/TS/JVM services. A JVM library integrates in-process with Kotlin or Java hosts. Rust needs FFI or a sidecar in every host except Python, where PyO3 is excellent.

### 6.3 The "many small KBs" workload changes the argument

For an AI agent's formalisation layer, a typical call looks like this: 10–10,000 facts, 5–200 rules, repeated many times, often with **queries** rather than full materialisation, and needing **explanations**.

* **Throughput per call.** Throughput is dominated by fixed costs: parsing, normalisation, planning (variable orders, plan construction per rule), dictionary set-up, allocation of tries and permutations. Nemo optimises for large tables. Each rule application builds sorted immutable tries, which is overkill for 1k facts.
  * For small KBs, a hash-indexed, tuple-at-a-time, in-place engine (Graal-like, with good indexes and BCC homomorphisms) is often **faster** than a columnar WCOJ engine, regardless of language.
* **JVM warm-up.** Warm-up is the real JVM cost for small KBs, but only per process, not per KB. In a long-lived agent service the JIT is warm after the first few hundred calls, and GC pressure from short-lived per-KB structures is exactly what generational GC handles best.
  * For CLI-style one-shot use, use GraalVM native-image or CRaC.
* **Goal-directed reasoning pays off more than raw speed.** Query rewriting (no materialisation at all) or magic-sets-style relevance filtering beats any materialisation engine on small KBs with few queries. Your technique set is well chosen for this regime; Nemo's is not.
* **Isolation and many tenants.** Many concurrent small KBs favour a thread-safe library with cheap engine instances. Nemo's engine is single-threaded per instance and async-API, so you would run N instances in parallel, which works. On the JVM, same story.

**Bottom line:** Rust's advantage is decisive for **big-data materialisation** (Nemo's niche, which you can simply delegate to Nemo). It is marginal for the **small-KB, query-driven, explanation-heavy agent workload**, where algorithmic design, engineering discipline (primitive arrays, no per-step allocation) and velocity dominate.

---

## 7. Local measurement (small-KB latency)

**Setup.** Built `nmo` from `main` with `cargo +nightly build -r -p nemo-cli`: 3m37s on 4 cores. Machine: a 4-vCPU cloud container. Benchmark programs are not included here.

**Results:**

| Program | Nemo's own timing | Process wall time |
|---|---|---|
| `small.rls`: 400 inline facts; 7 rules with an existential, 2 recursive closures, negation and `#count`; 5.1k derived facts | "Data import" 95–98 ms, "Reasoning" 11–14 ms | about 115 ms |
| `tiny.rls`: 1 fact, 1 rule | — | about 5–6 ms (process start is cheap) |

**Surprise: inline facts are slow.** Facts written inside the program text cost about **0.3 ms per fact**, linearly:

| Inline facts | "Data import" |
|---|---|
| 100 | 31 ms |
| 400 | 138 ms |
| 1,600 | 515 ms |
| 6,400 | 1,905 ms |

The same 6,400 facts loaded via `@import ... csv` take **1 ms**. The cost is in the logical front-end: the rule-model pipeline, validation and normalisation, per fact statement. The physical engine is not the bottleneck.

**Consequence for agent use.** Pass facts as CSV/in-memory tables, not as generated rule text. The core reasoning on small KBs is about 10 ms, which is fine but not "microseconds". A tuned in-process JVM engine over a few hundred facts should be in the same range or better once warm.

## 8. What I could not verify

* The paper PDFs (KR 2024, ICLP 2023, ESWC 2026, ITP 2025, XLoKR 2024) were blocked by the egress proxy. Benchmark numbers come from the authors' raw spreadsheets in `knowsys/nemo-examples`, not the papers' tables. Hardware and exact configuration were not checked.
* **No RDFox comparison exists** in any Nemo evaluation folder. Any Nemo-vs-RDFox statement is conjecture.
* Whether `feature/static_checks` will be merged, and its correctness, was not reviewed beyond the function list.
* Formal semantics of negation combined with the Datalog-first restricted chase (order dependence) was inferred from code and docs. It is not a documented guarantee.
* uniffi/Panama binding feasibility is an assessment; no binding was built.
* JVM vs Rust performance ratios in section 6 are engineering estimates from general experience, not measurements on this workload.

## Sources

* Repo: https://github.com/knowsys/nemo (commit `e578c28`); wiki https://github.com/knowsys/nemo/wiki; docs https://knowsys.github.io/nemo-doc (source: https://github.com/knowsys/nemo-doc)
* Benchmarks: https://github.com/knowsys/nemo-examples/tree/main/evaluations (`kr2024/runtimes.ods`, `iclp2023/results.ods`, `eswc2026/results.ods`)
* KR 2024: https://proceedings.kr.org/2024/70/kr2024-0070-ivliev-et-al.pdf
* ICLP 2023 (EPTCS 385): https://arxiv.org/abs/2308.15897
* ESWC 2026: https://link.springer.com/chapter/10.1007/978-3-032-25156-5_27
* Publications list: `nemo.wiki/Publications.md`; `nemo-doc/src/content/docs/intro/research.md`
* Static checks: branch `feature/static_checks`, crate `nemo-static-checks/`
