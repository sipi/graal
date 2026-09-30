# 08 — Kotlin (JVM / KMP) vs Rust for a new existential-rules reasoning engine

Date: 2026-09-30. Scope: a new engine written from scratch for existential rules (Datalog±). It needs chase variants, homomorphism search, UCQ rewriting with piece-unifiers, GRD, a decidability/rule-class analyser, and later stratified negation, aggregation and explanations/provenance. Target: the formal reasoning layer that LLM agents call. The first market is enterprise domain modelling: small-to-medium KBs, many queries, low latency. The second is aerospace & defence (A&D): possibly large KBs, possibly strict assurance.

**Evidence.** Web searches (Sept 2026) and the earlier findings in [report 05](05-nemo-and-rust-option.md), the local Nemo clone and local Nemo benchmarks (neither included in this repository). The egress proxy blocked arxiv.org, ceur-ws.org, proceedings.kr.org, drops.dagstuhl.de and iccl.inf.tu-dresden.de, so several papers are known only from search-engine abstracts. Items marked **[unverified]** are claims I could not check against a primary source.

---

## 0. Executive summary

| Criterion (weight) | Kotlin/JVM | Rust | Main reason |
|---|---|---|---|
| 1. Raw performance (15%) | 3 | 5 | Rust gives flat arrays, no GC and AOT by default. The JVM can come within 1.2–2× but only with array-style discipline, and it has warm-up / native-image trade-offs. |
| 2. Correctness & assurance (25%) | 3 | 4 | Both have ADTs and exhaustive matching. Rust has ownership, a live verification ecosystem (Kani, Verus, Creusot, Aeneas→**Lean**) and determinism by default. JVM: KeY/OpenJML cover Java only, and SnaKt for Kotlin is early. Lean certificate checking narrows the gap (§2.4). |
| 3. A&D suitability (15%) | 2 | 4 | Ferrocene is TÜV-qualified (ISO 26262 ASIL D, IEC 61508 SIL 3), with a certified `core` subset and a DO-178C path. For Kotlin/JVM there is no qualified toolchain, and on-board use means niche RT-JVMs (Java). |
| 4. Integration (15%) | 3 | 5 | PyO3/maturin, WASM, C ABI and the official `rmcp` MCP SDK, all demonstrated by Nemo (Python + WASM). The JVM wins only for Jena/RDF4J/OWLAPI. Rust→JVM is easy via FFM/uniffi. |
| 5. Velocity / maintainability (20%) | 5 | 3 | You are already expert in Kotlin/Java. Rust costs about 2–4 months of learning, borrow-checker friction during research refactors, and slower compiles. The Rust hiring pool in France is small but growing. |
| 6. Domain ecosystem (10%) | 4 | 4 | Rust has Nemo crates (Apache-2.0/MIT), ascent, datafrog, egglog, DD and petgraph. The JVM has jgrapht (BCC ready-made), fastutil, ANTLR, OWLAPI and the Graal/InteGraal code bases. |
| **Weighted total** | **3.35** | **4.10** | |

**Recommendation.** Build a **Rust core with polyglot bindings**: Python (PyO3), MCP (`rmcp`), WASM, a C ABI, and a thin JVM binding (FFM) for OWLAPI/Jena users. Use **Lean 4 as the second language**, for specifications, the proofs of rule-transformation equivalence, and a verified **certificate checker**. Do not do a Rust-kernel + Kotlin-outer split: the FFI boundary would cut through the rewriting ↔ homomorphism loop. This recommendation depends on the time-boxed dual spike in §5. The fallback, if the Rust spike misses the velocity criterion, is **all-Kotlin with array-style storage + GraalVM native-image + the same Lean certificate checker**.

**Tension with [report 05](05-nemo-and-rust-option.md).** That report advised staying on Kotlin. Its question was whether to adopt Nemo, and it judged performance: on symbolic, query-sized work, Rust gains little. I agree with that performance finding. What tips this analysis is different: assurance, A&D deployability, Python/WASM embedding, and a from-scratch codebase with no legacy to preserve. If A&D and formal assurance were off the table, Kotlin would win on velocity.

---

## 1. Raw performance

### 1.1 Large materialisation (joins, columnar tries, memory layout)
- **Evidence.** Nemo (Rust) uses columnar tries (`Vec<u32>/Vec<u64>` with RLE) and leapfrog trie joins. In its raw KR-2024 data (`nemo-examples/evaluations/kr2024/runtimes.ods`, read locally), it matches or beats VLog (C++) and is comparable to compiled Soufflé (C++):

  | Benchmark | Nemo | VLog | Soufflé (compiled) |
  |---|---|---|---|
  | Galen | 5.5 s | 42.7 s | — |
  | SNOMED | 76 s | OOM | 115 s |
  | LUBM-1k | 168 s | 188 s | 146 s |
  | Deep200 | 8 s | timeout | timeout |

  Soufflé ran Skolemised programs, so the existential benchmarks are not a strict comparison.
- **JVM.** There is no published JVM chase engine at that level. Graal/InteGraal has never been benchmarked against these engines (earlier finding). RDFox is C++, and the JVM rule engines people use (Jena rules, OWLAPI reasoners) are not in this league **[unverified: no head-to-head found]**.
- **Mechanism, not language.** Object-per-atom layouts cost 50–100+ bytes per fact, against 4–8 bytes per column cell. The JVM can use `IntArray`/`LongArray` or off-heap `MemorySegment` (FFM, final in JDK 22, JEP 454), and then the gap shrinks to about 1.2–2×. That figure is my engineering estimate **[unverified by a benchmark]**. Project Valhalla (value classes) would help, but I have not verified that it is final in a JDK LTS **[unverified]**. Kotlin's idiomatic data-class style fights this; Rust's idiomatic `Vec<u32>` style is the fast one.
- **Parallelism.** Rust has rayon and data-race freedom by construction, which suits parallel semi-naive evaluation over SCC strata. The JVM has good fork/join and virtual threads but no race-freedom guarantee.

### 1.2 Many small KBs (latency, startup, warm-up)
- Local measurement (earlier): Nemo reasons over a 400-fact KB in about 11–14 ms. The front-end dominates, at about 0.3 ms per inline fact versus 1 ms for 6,400 CSV facts. The process starts in about 5 ms. Small-KB latency is dominated by the engine's architecture (per-program planning, trie building), not by the language. A hash-indexed, tuple-at-a-time in-place engine is the right design for that regime in either language.
- **JVM warm-up.** In a long-running agent server (MCP over HTTP/stdio), the JIT warms up once, so it mostly doesn't matter. It does matter for CLI or per-call processes and for serverless.
- **GraalVM native-image** mitigates this. In Oracle's PetClinic figures, startup was 0.22 s vs 7.18 s and memory 40% of HotSpot, with peak throughput about 80% of JIT. Vendor/blog sources claim PGO gets within 5–15% of JIT **[blog-sourced, not independently verified]**. Costs: closed-world reflection configuration, long build times, and G1 in native-image only in Oracle GraalVM.
- Rust has predictable microsecond startup and no warm-up.

### 1.3 GC pauses and predictability
- Generational ZGC (JDK 21+) gives typical pauses of 0.1–0.5 ms at the cost of throughput and memory headroom. That is fine for enterprise agents, but it is not *deterministic* timing. Rust has no GC; allocation latency still exists, but it is under your control (arenas, bump allocators).

**Score: Kotlin 3, Rust 5.**

---

## 2. Correctness & assurance

### 2.1 Language-level safety
- **Rust.** Ownership and borrowing, no data races, no null, `enum` + exhaustive `match`, and newtypes at no cost (`VarId(u32)` vs `ConstId(u32)`). `unsafe` is explicit and auditable; Nemo has 94 `unsafe` sites in `nemo-physical` and uses `miri`. Panics on overflow in debug builds.
- **Kotlin.** Null-safety, `sealed` classes/interfaces + exhaustive `when`, value classes (single-field only), and JVM memory safety. There is no protection against shared-mutable-state races. Kotlin contracts are **not verified** by the compiler; formal verification is explicitly a non-goal of the contracts KEEP.

### 2.2 Testing
- **Rust.** `proptest`, `quickcheck`, `bolero` (property testing + fuzzing + Kani harnesses from one API), `cargo-fuzz`/libFuzzer, `insta` snapshots, `miri` for UB.
- **JVM.** `jqwik`, `kotest-property`, Jazzer (fuzzing) and PIT mutation testing. JVM mutation testing (PIT) is more mature than Rust's (`cargo-mutants`).
- Both are adequate for differential testing against a reference (Lean-extracted or Nemo/Graal).

### 2.3 Formal verification tooling (state in 2025–2026)
| Tool | Language | Status / fit |
|---|---|---|
| **Kani** (AWS) | Rust | Bounded model checker. Used in the Rust-std verification challenge: Autoharness generated 16,748 harnesses and verified 11,970 against Kani's UB classes (Rust Foundation, 2026). Good for the absence of panics/UB in the storage/join kernel. |
| **Verus** | Rust | SMT-based functional verification. SOSP'24 best paper, industrial use at Microsoft and Amazon, the Asterinas/CortenMM kernel. Good for invariants of indexes and the homomorphism search. |
| **Creusot** | Rust | Why3-based deductive verification. |
| **Aeneas** | Rust → **Lean 4** (also Coq, F*, HOL4) | Translates safe Rust (MIR) to pure functional Lean. The Lean backend was revamped in 2025, with support for loops and nested mutable borrows. A Sept-2026 report scales it to cryptographic code. **This is the only mainstream path that links an implementation language to Lean.** |
| Flux, VeriFast, ESBMC | Rust | Integrated into the std-verification CI. |
| **KeY**, **OpenJML** | Java only | Mature. OpenJML supports Java 21 (records, sealed, pattern matching). They do **not** read Kotlin. |
| **SnaKt** (JetBrains) | Kotlin → Viper | A K2 compiler plugin, "early development, large parts of Kotlin syntax not supported". |

### 2.4 Linking to proof assistants: "proof in Lean + tested implementation" vs "verified implementation"
You need proofs that rule transformations (normalisation, single-piece decomposition, piece-unifier rewriting steps, Skolemisation, stratification) preserve logical equivalence or entailment. There are three levels:

1. **Spec-level proofs in Lean** (language-independent). Prove each transformation sound (and complete where claimed) on a Lean model of FO rules, homomorphisms and models. Prior art to build on:
   - **"The Chase in Lean"** (Gerlach, 2026). The first Lean library for existential rules and the chase, including the universal-model property and unified termination conditions. Repo: `monsterkrampe/Existential-Rules-in-Lean`.
   - **"Verifying Datalog Reasoning with Lean"** (Tantow, Gerlach, Mennicke, Krötzsch; ITP 2025). A verified Lean checker for Datalog proofs emitted by Nemo (repo `knowsys/CertifyingDatalog`).

   Proofs at this level hold for the algorithm, not for your code.
2. **Certifying implementation + verified checker** (language-independent and the best ROI). The engine emits certificates:
   - chase derivation traces / proof trees for entailed answers, as in the ITP'25 approach;
   - for each rewritten CQ, the chain of piece-unifier steps;
   - for "rule class = FES/BTS", the witness (acyclic GRD, stratification…).

   A small Lean-verified checker validates them. This gives a *per-run* guarantee of **soundness** regardless of whether the engine is Kotlin or Rust. **Completeness** (the rewriting found every CQ, the chase is not truncated) cannot be certified per run as easily. It rests on the level-1 proof plus testing, or on level 3.
3. **Verified implementation.** Prove the actual code. On the JVM this is essentially infeasible for Kotlin today (KeY/OpenJML are Java-only and not Lean-linked). In Rust, Aeneas can extract *selected* safe-Rust functions to Lean: unification, substitution application, piece computation, the GRD edge test. You then prove them equal to the level-1 spec. Verus is an alternative (SMT, not Lean). Expect this only for a small, pure, performance-insensitive kernel of the rewriting logic, not for the columnar storage. Aeneas' coverage of trait objects, `HashMap` and iterator-heavy code is limited **[unverified in detail]**.

**Recommended assurance stack** (either language): Lean spec + proofs (level 1), a certificate checker (level 2), and differential/property testing of the engine against an executable Lean reference on small KBs. In Rust, add Kani on the unsafe/storage code and Aeneas on the pure unifier/rewriting core (level 3) once the code stabilises.

**Score: Kotlin 3, Rust 4.** Rust scores only one point higher because level 2 does most of the work and is language-neutral.

---

## 3. Aerospace / defence suitability

- **Qualified Rust toolchain.**
  - **Ferrocene** (Ferrous Systems, Berlin; open source) is TÜV SÜD-qualified for ISO 26262 ASIL D, IEC 61508 SIL 3 and IEC 62304 Class C. It "supports qualification efforts toward SIL 4 and DO-178C (DAL C)".
  - Ferrocene 26.02.0 (embedded world 2026) grew the **certified `core` subset** to 5,169 functions (IEC 61508 SIL 2, ISO 26262 ASIL B).
  - A DO-178 / ECSS qualification is "planned/offered", not issued **[unverified: no issued DO-330 qualification kit found]**.
  - **AdaCore GNAT Pro for Rust** (AdaCore has strong French roots; its Paris team) targets DO-178C / ISO 26262 / IEC 61508, with Rapita RVS for structural coverage in avionics.
  - DLR/ESA ("cRustacea in Space", ADCSS 2024) ported Rust `std` to RTEMS and judged Rust able to meet ECSS qualification.
- **JVM.**
  - No qualified JVM toolchain exists for DO-178C. On-board Java means hard-RT VMs: aicas **JamaicaVM** (deterministic GC, aimed at DO-178C/DO-332) and PTC **Perc**. Both are Java-centric; Kotlin on them is **[unverified]**. The Kotlin compiler and stdlib have no qualification story.
  - Kotlin/Native has a non-generational concurrent mark-sweep GC, no RT guarantees and no certification path.
- **Realistic placement.** A reasoning engine in A&D is most likely a **ground or mission-support tool**: planning, configuration checking, ISR knowledge fusion, safety-case consistency. It is not flight software at DAL A/B. Two consequences:
  - The relevant standard is often **DO-330 tool qualification** (TQL-4/5 if its output verifies or eliminates a process). Your Lean-verified certificate checker then becomes the qualified artefact. It is small, and it is independent of the engine language, which is a strong argument for level 2 above.
  - For ECSS-E-ST-40C/Q-ST-80C ground software (criticality C/D), a JVM is acceptable.
- **Determinism.** In both languages, hash-map iteration order must not leak into results (nulls naming, rewriting order). Use ordered/indexed structures and seeded hashers. Rust makes allocation and timing deterministic; the JVM adds JIT/GC timing non-determinism, though not result non-determinism.
- **Embedded / on-board.** Rust has `no_std` + `core` + `alloc`, runs on RTEMS/bare metal, and has a certified `core` subset. The JVM can only use the niche RT-JVMs.
- **Supply chain / SBOM.**
  - Rust: Cargo.lock, `cargo-auditable`, `cargo-vet`, `cargo-deny`, CycloneDX via `cargo-cyclonedx`. The risk is a deep transitive dependency tree (Nemo pulls many crates and requires **nightly**). Mitigate with a minimal, stable-only, vendored dependency set.
  - JVM: CycloneDX Maven/Gradle plugins, mature SCA tooling. The JVM itself is a large TCB to justify.
- **Sovereignty (French/EU).**
  - Rust: Ferrocene is EU (German), open source and on public docs. AdaCore has a large French presence. ANSSI publishes a Rust secure-coding guide (ANSSI-PA-074).
  - JVM: OpenJDK is open source, but its stewardship and main vendors are US-based (Oracle, Microsoft, Amazon, Azul). JetBrains is EU-headquartered.
  - Neither is disqualifying. The Rust + Ferrocene story is simply easier to tell in a DGA/SAFE-instrument context **[qualitative judgement]**.
  - I found no public evidence of Rust adoption by Thales/MBDA/Airbus specifically **[unverified]**.

**Score: Kotlin 2, Rust 4.**

---

## 4. Integration (AI-agent world)

| Channel | Rust | Kotlin/JVM |
|---|---|---|
| Python (the dominant language of LLM agents) | **PyO3 + maturin**: in-process, wheels for every platform, zero-copy possible. Nemo ships `nemo-python` (PyO3 0.29). | JPype (in-process JVM, heavy), Py4J (socket), GraalPy. Workable, but ships a JVM with the wheel. |
| MCP server | Official **`rmcp`** SDK (v3.0.x in July 2026). | Official **Kotlin MCP SDK** (JetBrains, KMP: JVM, Native, Wasm/JS). Equal. |
| WASM (browser, sandboxed agent tools, edge) | First-class: `wasm-bindgen`. Nemo runs in the browser at about 1.6–2× native speed (KR-2024 data). | Kotlin/Wasm exists, but its maturity for a compute-heavy engine is **[unverified]**. |
| C ABI (C/C++/Go/Ada integration) | Native (`cbindgen`). Ada interop works via GNAT Pro's multi-language model. | Only through Kotlin/Native or GraalVM native-image C entry points, which is awkward. |
| JVM ecosystem (Jena, RDF4J, OWLAPI, enterprise Java) | Via **FFM/Panama** (final in JDK 22; can be faster than JNI), **UniFFI** (Kotlin bindings over JNA, with a KMP fork), or `jni-rs`. Moderate effort. | Native. This is the JVM's main integration advantage. |

**Kotlin↔Rust hybrid mechanics.** UniFFI generates idiomatic Kotlin but uses JNA, so calls are slower. FFM with `jextract` over a `cbindgen` header gives fast downcalls. Coarse-grained calls ("load KB", "run chase", "answer UCQ") are fine. Fine-grained calls (per-homomorphism callback into Kotlin) kill the benefit.

**Score: Kotlin 3, Rust 5.**

---

## 5. Developer velocity & maintainability (solo founder / small team)

- **You.** You are expert in Kotlin and deep in Java. With Kotlin you are productive from day 1, you get excellent IDE refactoring (IntelliJ), and you can iterate fast on research ideas: new chase variants, new rewriting operators.
- **Rust learning curve for a JVM expert.** Typically 1–2 months to be productive and 3–6 months to be fluent with lifetimes, trait design and arena/index-based graph structures **[experience-based estimate]**. The tricky parts for this domain:
  - mutable graph structures (GRD, dependency graphs): use `petgraph` or index-based arenas, not `Rc<RefCell<>>`;
  - term/atom interning: use `u32` ids + dictionaries, which is the right design anyway;
  - backtracking search with undo trails: easy in Rust with `Vec` + indices.
- **Research-phase refactoring.** Rust refactors are safe, since the compiler finds everything, but slower: signature churn and lifetime ripple effects. Compile times are noticeably longer than Kotlin incremental builds for a crate of 30–90 kLOC. Mitigations: workspace split, `cargo check`, `mold`. LLM coding assistants work well in both languages, which reduces Rust's learning-curve penalty.
- **Hiring in France.** Kotlin/Java is a large pool. Rust is small but growing (about 30–55 open Rust jobs listed in France; median around €52k gross according to aggregators **[aggregator data, low reliability]**). There is a Rust niche around defence/crypto/embedded (Paris, Toulouse, Rennes). Rust developers tend to be self-selected, strong systems engineers. Formal-methods profiles (Lean/Coq) are rare in both cases; INRIA/CEA-List/LIRMM are sources.
- **Maintainability.** Rust's explicitness and the absence of a runtime ease long-term maintenance of a kernel. Kotlin is more concise for the symbolic/analysis layers.

**Score: Kotlin 5, Rust 3.**

---

## 6. Ecosystem for this domain

| Need | Rust | JVM |
|---|---|---|
| Storage/joins | **Nemo crates** (`nemo-physical`: tries, leapfrog join, dictionaries; **Apache-2.0 OR MIT**, but on **nightly**), `datafrog` (4.4M downloads), `ascent`, `crepe`, `differential-dataflow`, `egglog` (equality saturation + Datalog). | Nothing comparable off the shelf. fastutil / Eclipse Collections / HPPC for primitive collections, Chronicle for off-heap. You would hand-roll columnar tries. |
| Existing existential-rules code | Nemo (restricted chase, no rewriting/GRD). | **Graal / InteGraal** (Java, LIRMM/BOREAL): piece-unifiers, GRD, rewriting and analysers. It is a reference implementation to port or to test against differentially, even if you write new code. |
| Graph algorithms | `petgraph` 0.8.3: Tarjan/Kosaraju SCC, articulation points, bridges, dominators. **No biconnected-component decomposition** (checked locally). You would write a BCC edge partition yourself (~100–150 LOC over the existing DFS). | **jgrapht**: `BiconnectivityInspector` (BCCs, cut points, blocks), SCC inspectors, isomorphism. More complete. |
| Parsers (DLGP, Nemo `.rls`, SPARQL fragments) | `chumsky` (great error recovery), `lalrpop`, `pest`, `winnow`/`nom`. | ANTLR 4 (mature, grammar-first), `better-parse` for Kotlin. |
| RDF/OWL | `oxigraph`/`oxrdf`/`oxttl` (solid RDF/SPARQL); `horned-owl` (OWL, less mature than OWLAPI). | Jena, RDF4J, **OWLAPI**, HermiT/ELK/Openllet reasoners: much richer. |
| Provenance/explanations | Nemo has tracing (proof trees), plus the Lean certificate checker work. | Graal has little provenance. |

**Score: Kotlin 4, Rust 4.** Rust has better kernel building blocks; the JVM has better KR/OWL tooling and your domain predecessor, Graal.

---

## 7. Three architectures

### (a) All-Kotlin (JVM, optional native-image; KMP only for the API/MCP surface)
- **Pros.**
  - Maximum velocity for you, and direct reuse of Graal/InteGraal knowledge and code patterns.
  - OWLAPI/Jena native; jgrapht BCC ready-made; one language and one build.
  - Native-image gives fast CLI/serverless startup.
  - Good enough for the enterprise market (small/medium KBs) if the core is written array-style (IntArray columns, interned ids, trail-based substitutions).
- **Cons.**
  - No credible on-board or certified path for A&D; ground-tool-only positioning.
  - Python embedding is heavy (JPype/Py4J or a service boundary); WASM is weak.
  - Verification of the implementation is out of reach (KeY/OpenJML are Java-only; SnaKt is immature). Assurance relies entirely on the Lean spec + certificate checker.
  - Large-KB materialisation competes against Nemo/RDFox from a disadvantaged runtime. You would likely end up delegating large Datalog workloads to Nemo anyway (the [report 05](05-nemo-and-rust-option.md) plan).

### (b) All-Rust core + bindings (recommended, subject to the spike)
- **Pros.**
  - One kernel serves every channel: Python wheel, MCP server, WASM (browser/sandbox), C ABI (Ada/C++ in A&D), and a JVM via FFM.
  - Best performance ceiling. You can borrow ideas or code from Nemo's physical layer (licence-compatible) and later interoperate with Nemo.
  - Aeneas → Lean path for the pure rewriting core; Kani for the unsafe/storage code; a Ferrocene/GNAT Pro path for A&D.
  - Deterministic memory/timing.
- **Cons.**
  - A 2–4 month productivity dip for you, and slower research iteration.
  - BCC must be written; OWL tooling is weaker (use oxigraph/horned-owl or delegate OWL parsing to a JVM sidecar).
  - A smaller hiring pool.
  - Dependency discipline is needed: stable toolchain, minimal crates, so that Ferrocene stays an option.

### (c) Hybrid
- **c1: Rust kernel + Kotlin outer.** Rust: storage, joins, homomorphism, chase. Kotlin: analyser, rewriting, tooling, API.
  - Pros: you write the research-heavy symbolic parts in your strongest language, and put performance where it matters.
  - Cons: **the natural cut is wrong**. UCQ rewriting and redundancy elimination call homomorphism/containment tests millions of times on tiny queries, and GRD construction calls unification. Those calls would cross the FFI boundary per test (slow, marshalling of terms), or you would duplicate homomorphism code in both languages. It also means two data models to keep in sync, two build systems (Gradle + Cargo), two test stacks and double the release engineering, all for a solo founder. The formal-assurance story splits too.
- **c2: Rust kernel + Python outer.** It fits the agent ecosystem, but Python is the weakest language for the symbolic core's correctness. It is acceptable only as a thin orchestration/API layer (that is essentially (b) with PyO3).
- **c3: Kotlin core + Rust/Nemo accelerator** (the reverse split). Kotlin does everything; large Datalog materialisations are exported to Nemo (`.rls`/CSV, or a uniffi binding).
  - Pros: this is the [report 05](05-nemo-and-rust-option.md) plan. Fast start, and scale when needed.
  - Cons: semantic mismatch risks between two engines (null handling, chase variant, aggregates), and nothing solves on-board/WASM/Python.
- **Verdict on hybrids.** Only split at *coarse* boundaries: the API layer (Python/MCP/JVM bindings over a Rust core, i.e. (b)), or batch materialisation delegated to Nemo (c3). Never split between rewriting and homomorphism.

---

## 8. Staged de-risking plan

### Stage 0: Lean foundations (in parallel, 2–3 weeks, language-independent)
- Fork or depend on `Existential-Rules-in-Lean`. State the transformations you need, and prove one end-to-end: the single-piece rule decomposition preserves equivalence.
- Define the certificate format for (i) chase derivations and (ii) piece-unifier rewriting steps. Prototype a checker for (i), reusing `knowsys/CertifyingDatalog` ideas.

### Stage 1: Dual spike (time-boxed: 3 weeks Kotlin + 4 weeks Rust, the extra week for learning; same spec)
- **Scope, identical in both languages:**
  - a DLGP-subset parser, and dictionary-encoded columnar/hashed storage;
  - homomorphism search (backtracking with index-driven atom ordering, BCC-based decomposition of the query);
  - GRD construction via piece-unifiers, then SCC stratification;
  - restricted chase with semi-naive evaluation per SCC stratum, plus a Datalog-first option;
  - CQ answering over the result;
  - certificate emission for chase derivations;
  - an MCP stdio server with 2 tools (`load_kb`, `query`);
  - a Python binding (PyO3 for Rust; JPype for Kotlin).
- **Benchmarks:**
  - *Large:* LUBM-1/10/100 with existential rules (the ChaseBench/VLog LUBM∃ variant), plus ChaseBench Doctors-100k/1M, Ontology-256 and Deep-100.
  - *Small:* 1,000 generated enterprise-like KBs (≈400 facts, 30–60 rules, 5–10% existential), with 10,000 CQs.
  - *Baselines:* Nemo `main`, and Graal/InteGraal (first-ever Graal numbers, which is useful by itself).
- **Measure:** wall time, peak RSS, bytes/fact, p50/p99 query latency (warm and cold), startup, LOC, and hours spent (logged honestly, including learning time), plus a refactoring-pain log (count and time of design changes).

### Go/no-go criteria (decide Rust core vs Kotlin core)
| # | Criterion | Rust "go" if … | Kotlin "acceptable" if … |
|---|---|---|---|
| P1 | Large materialisation (LUBM-100∃, Ontology-256) | within 1.5× of Nemo | within 2× of the Rust spike, **and** within 3× of Nemo |
| P2 | Small-KB latency (warm, in-process) | p99 < 2 ms per CQ, KB load < 5 ms | same thresholds; cold start < 50 ms with native-image |
| P3 | Memory | ≤ 16 bytes/fact in the base store | ≤ 2× Rust |
| V1 | Velocity | Rust spike reaches **full feature parity within the 4-week box** and ≤ 1.6× Kotlin's logged hours | — |
| V2 | Refactoring | at least one mid-spike design change (e.g. switch term representation) is done in ≤ 1 day | — |
| A1 | Assurance feasibility | Aeneas extracts the unifier/piece-computation module to Lean without restructuring more than ~20% of it; 1 Kani harness on storage passes | JML/KeY not attempted; certificate checker only |
| I1 | Integration | PyO3 wheel + `rmcp` server + WASM build all work in ≤ 3 days total | Python via JPype works in ≤ 2 days |

**Decision rule.**
- If **V1 fails**, choose **Kotlin (a)/(c3)** regardless of P1–P3. Velocity is existential for a solo founder, and assurance is carried by Lean certificates either way.
- If V1 passes and **Kotlin misses P1 or P2**, choose **Rust (b)**.
- If both pass everything, the tiebreakers are the A&D pipeline and the Python/WASM demand from actual prospects. With A&D prospects, choose Rust; with enterprise-only prospects in the next 18 months, choose Kotlin with a Nemo accelerator.

### Stage 2: Build (if Rust)
- Stable toolchain only; a crate allow-list checked with `cargo-deny`/`cargo-vet`.
- Workspace split: `core-model`, `store`, `hom`, `chase`, `rewrite`, `analyse`, `cert`, `bindings-{py,mcp,wasm,jvm}`.
- Aeneas on `rewrite::unify` and `rewrite::piece` once they stabilise.
- A Lean checker in CI running over all test certificates.

### Stage 3: A&D readiness (only when a customer materialises)
- Evaluate Ferrocene vs GNAT Pro for Rust. Scope DO-330 qualification of the *certificate checker* (not the engine), and ECSS-E-ST-40C classification of the engine as ground software.

---

## 9. Unverified or weakly sourced points
1. Ferrocene DO-178C: "supports efforts toward DAL C", with a DO-178/ECSS qualification "planned". I found no issued avionics qualification.
2. Kotlin bytecode on JamaicaVM/Perc in a certified project: no evidence.
3. JVM-vs-Rust gap for array-style engines (1.2–2×): my estimate, not benchmarked. The spike must measure it.
4. GraalVM PGO "5–15% of JIT": from blog posts. FFM "up to 50% faster than JNI": from secondary sources.
5. Project Valhalla availability in an LTS JDK: not verified.
6. Aeneas coverage of the idioms that would appear (traits, hash maps, iterators): known limits, details unverified.
7. The Nemo KR-2024 paper itself was not readable (proxy). Numbers come from its raw spreadsheet. There is no RDFox comparison anywhere.
8. Rust hiring and salary data for France: job-aggregator sites, low reliability.
9. Rust adoption at specific French defence primes: not found publicly.
10. Kotlin/Wasm maturity for compute-heavy engines: not assessed.

## Sources
- Ferrocene 26.02.0 — https://ferrous-systems.com/blog/ferrocene-26-02-0/ ; https://ferrocene.dev/ ; libcore SIL2 — https://ferrous-systems.com/blog/ferrocene-libcore-news-release/
- AdaCore GNAT Pro for Rust — https://www.adacore.com/gnat-pro-for-rust ; Rapita + AdaCore — https://www.rapitasystems.com/downloads/certification-ready-rust-gnat-pro-rvs-avionics-standards
- Rust in space (DLR/ESA) — https://arxiv.org/abs/2405.18135 ; https://ferrous-systems.com/blog/rust-who-what-why/
- JamaicaVM avionics — https://www.aicas.com/standards/avionics-certification-standards/ ; PTC Perc — https://www.ptc.com/en/products/developer-tools/perc
- Aeneas — https://github.com/AeneasVerif/aeneas ; https://lean-lang.org/use-cases/aeneas/ ; https://aeneasverif.github.io/newsletter/2025/04/07/aeneas-newsletter.html
- Verus (SOSP'24) — https://dl.acm.org/doi/10.1145/3694715.3695952 ; Rust std verification — https://rustfoundation.org/media/how-the-rust-standard-library-verification-contest-scaled-past-manual-proof-engineering/
- Lean: The Chase in Lean — https://arxiv.org/abs/2604.22531 , https://github.com/monsterkrampe/Existential-Rules-in-Lean ; Verifying Datalog Reasoning with Lean (ITP 2025) — https://iccl.inf.tu-dresden.de/web/Inproceedings3417/en
- Nemo — https://github.com/knowsys/nemo ; KR 2024 — https://proceedings.kr.org/2024/70/kr2024-0070-ivliev-et-al.pdf (raw data in `nemo-examples/evaluations/kr2024`)
- KeY/OpenJML — https://www.openjml.org/ ; SnaKt — https://github.com/JetBrains/SnaKt ; Kotlin contracts KEEP — https://github.com/Kotlin/KEEP/blob/master/proposals/kotlin-contracts.md
- FFM (JEP 454) — https://openjdk.org/jeps/454 ; UniFFI Kotlin/Gradle — https://mozilla.github.io/uniffi-rs/latest/kotlin/gradle.html ; KMP UniFFI bindings — https://github.com/UbiqueInnovation/uniffi-kotlin-multiplatform-bindings
- MCP SDKs — https://github.com/modelcontextprotocol/rust-sdk ; https://github.com/modelcontextprotocol/kotlin-sdk
- Generational ZGC — https://inside.java/2023/11/28/gen-zgc-explainer/ ; GraalVM native-image trade-offs — https://www.javacodegeeks.com/2025/12/graalvm-native-image-vs-traditional-jvm-understanding-the-trade-offs.html
- ANSSI Rust guide — https://github.com/ANSSI-FR/rust-guide
- Graal/InteGraal — https://graphik-team.github.io/graal/ ; https://radar.inria.fr/report/2024/boreal/index.html
- Rust jobs France — https://www.wearedevelopers.com/en/jobs/ls/france/rust ; https://www.glassdoor.com/Salaries/france-rust-developer-salary-SRCH_IL.0,6_IN86_KO7,21.htm
