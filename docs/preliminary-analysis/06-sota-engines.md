# 06 — State of the art of rule / Datalog / chase engines: what to borrow

Date: 2026-09-30. Scope: identify, for a new sound-by-construction existential-rules engine (Kotlin or Rust) with a decidability/complexity analyser driving algorithm selection, the 1–3 strongest ideas of each existing system, with references.

Method: web search (search-engine snippets; several primary sites — arxiv.org, ijcai.org, docs.oxfordsemantic.tech, api.github.com — were blocked by the egress proxy, so some facts rely on search snippets and on prior knowledge). Local clones of Nemo (v0.10.2-dev, last commit 2026-08-20) and InteGraal (Maven poms), not included in this repository, were inspected directly. Every point that could not be confirmed against a primary source in this session is tagged **[unverified]**.

---

## 0. Two clarifications first

### 0.1 Does RDFox support existential rules?

**Not natively, only through explicit Skolemisation.** RDFox's rule language is Datalog plus stratified negation (`NOT`, `NOT EXISTS … IN`), aggregation (`AGGREGATE(...)`), `BIND`/`FILTER` built-ins and equality (`owl:sameAs`). It has no `∃` quantifier in rule heads. To create new objects, a rule author must *call* the built-in tuple table `rdfox:SKOLEM(arg1, …, argn, ?v)`. It binds `?v` to a blank node uniquely determined by the arguments. In older versions this was a `SKOLEM` built-in function; it was replaced by the `rdfox:SKOLEM` tuple table ([RDFox docs – tuple tables](https://docs.oxfordsemantic.tech/tuple-tables.html), [release notes 6.2](https://docs.oxfordsemantic.tech/6.2/release-notes.html), [release notes 4.1](https://docs.oxfordsemantic.tech/4.1/release-notes.html)). Consequences:

* Semantically this is the **Skolem (semi-oblivious) chase**, and the user controls it by hand. The user chooses which variables go into the Skolem term, and so decides the frontier and the chase variant. RDFox does no restricted-chase check (it does not test whether the head is already satisfied) and no termination analysis. If the Skolem terms nest, materialisation can diverge, and preventing that is the user's job **[the absence of any termination check was not confirmed in the docs, which were blocked]**.
* OWL axioms outside OWL 2 RL (e.g. `SubClassOf(A, ObjectSomeValuesFrom(p, B))` on the right-hand side) are not turned into existential rules **[unverified; from prior knowledge of the RDFox docs]**.
* **RDFox does not use Warded Datalog±.** Wardedness plays no role in RDFox's design, its papers or its docs. The confusion probably comes from the shared Oxford origin (see 0.2).

### 0.2 Who invented Warded Datalog±?

* **Marcelo Arenas, Georg Gottlob, Andreas Pieris**, *Expressive Languages for Querying the Semantic Web*, PODS 2014, pp. 14–26 ([Edinburgh research explorer](https://www.research.ed.ac.uk/en/publications/expressive-languages-for-querying-the-semantic-web/), [ResearchGate](https://www.researchgate.net/publication/266657756_Expressive_languages_for_querying_the_Semantic_Web)). The paper introduced the warded fragment (as TriQ-Lite) to capture SPARQL under OWL 2 QL entailment with stratified negation. It was then developed in Gottlob & Pieris, *Beyond SPARQL under OWL 2 QL entailment regime: rules to the rescue*, IJCAI 2015, and in Gottlob et al. *Ontology Querying: Datalog Strikes Back* (Reasoning Web 2017, [Springer](https://link.springer.com/chapter/10.1007/978-3-319-61033-7_3)). Its space-efficient core is in Berger, Gottlob, Pieris, Sallinger, *The Space-Efficient Core of Vadalog*, PODS 2019 / TODS 2022 ([ACM](https://dl.acm.org/doi/10.1145/3488720)).
* The **Vadalog system** (Bellomarini, Sallinger, Gottlob, VLDB 2018) implements it. Gottlob moved from Oxford to Calabria; Sallinger is at TU Wien; the commercial spin-off is **Prometheux**. Oxford Semantic Technologies (RDFox) was founded by Horrocks, Motik and Cuenca Grau. These are two separate lines of work that happen to share Oxford.
* Warded ⊇ (in expressive terms, with some caveats) linear and guarded-without-joins-on-nulls. It captures PTIME data complexity. Its relationship with Shy (DLV∃) is analysed in Baldazzi, Bellomarini, Favorito, Sallinger, *On the Relationship between Shy and Warded Datalog±*, KR 2022 ([pdf](https://proceedings.kr.org/2022/39/kr2022-0039-baldazzi-et-al.pdf)).

---

## 1. System-by-system analysis

### 1.1 RDFox (Oxford Semantic Technologies → Samsung)
* **Origin / status:** University of Oxford (Motik, Horrocks, Nenov, Piro, Olteanu). Spin-off OST was founded in 2017. **Samsung Electronics announced its acquisition of OST on 16 July 2024** ([Oxford news](https://www.ox.ac.uk/news/2024-07-16-university-spin-out-oxford-semantic-technologies-acquired-samsung-electronics), [TechCrunch](https://techcrunch.com/2024/07/18/samsung-to-acquire-uk-based-knowledge-graph-startup-oxford-semantic-technologies)). C++, commercial and closed source (free academic licence). Actively released (v7.x).
* **Formalism:** RDF triples plus named graphs and tuple tables; Datalog with stratified negation, aggregation, built-ins, `owl:sameAs`, OWL 2 RL, SWRL, Skolem (see §0.1); SPARQL 1.1.
* **Core ideas:**
  1. **Parallel, mostly lock-free materialisation.** Threads pull facts from the shared table itself, which acts as the work queue (no central scheduler), and each fact is matched against the rule bodies. The indexes support "mostly" lock-free concurrent insertion. The result was up to 13.9× speed-up on 16 cores. Motik, Nenov, Piro, Horrocks, Olteanu, AAAI 2014 ([AAAI](https://ojs.aaai.org/index.php/AAAI/article/view/8730)).
  2. **Incremental maintenance: DRed → Backward/Forward (B/F) → Forward/Backward/Forward (FBF).** Deletions are checked for alternative derivations by backward chaining instead of over-deleting. FBF combines DRed-style over-deletion with B/F-style proving. Motik, Nenov, Piro, Horrocks, *Incremental update of datalog materialisation: the B/F algorithm*, AAAI 2015. *Maintenance of datalog materialisations revisited*, AIJ 269 (2019) ([pdf](http://www.cs.ox.ac.uk/people/boris.motik/pubs/mnph19maintenance-revisited.pdf)). Hu, Motik, Horrocks, *Optimised maintenance of datalog materialisations*, AAAI 2018 ([arXiv](https://arxiv.org/abs/1711.03987)) adds module-level plug-in algorithms.
  3. **Equality by rewriting.** `owl:sameAs` is handled by choosing a representative per equivalence class (union-find) and rewriting facts, instead of axiomatising equality. It is combined with incremental maintenance: *Combining rewriting and incremental materialisation maintenance for datalog programs with equality*, IJCAI 2015 ([pdf](https://www.cs.ox.ac.uk/people/boris.motik/pubs/mnph15incremental-BF-sameAs.pdf)).
  4. (Secondary) **Modular materialisation.** Rule "modules" with specialised algorithms, e.g. transitive closure or symmetric properties. Hu, Motik, Horrocks, *Modular materialisation of datalog programs*, AAAI 2019 ([arXiv](https://arxiv.org/abs/1811.02304)). **Hypertree decompositions for complex rule bodies** (both materialisation and incremental): Zhang, Hu, Nenov, Horrocks, IJCAI 2023 ([IJCAI](https://www.ijcai.org/proceedings/2023/377)).
* **Borrow:** FBF for incremental maintenance of the Datalog part; equality-by-representative with rewriting; the modular "plug-in algorithm per rule module" architecture, which fits an analyser-driven engine very well; hypertree/GHD-based join plans for large rule bodies (a generalisation of the bi-connected-component idea). Note: RDFox's incremental algorithms are for Datalog; with existentials/Skolem they apply to the Skolem chase only.

### 1.2 Vadalog (Oxford/TU Wien → Prometheux)
* **Origin / status:** Bellomarini, Sallinger, Gottlob, *The Vadalog System: Datalog-based Reasoning for Knowledge Graphs*, PVLDB 11(9) 2018 ([pdf](https://www.vldb.org/pvldb/vol11/p975-bellomarini.pdf), [arXiv](https://arxiv.org/abs/1807.08709)). Java, proprietary; commercial through **Prometheux** ([research](https://prometheux.ai/research.html)). Active: *Vadalog Parallel* (Spark, GPU through RAPIDS), PVLDB 17 (2024) ([pdf](https://www.vldb.org/pvldb/vol17/p4614-benedetto.pdf)); *Temporal Vadalog* (TIME 2025, [DROPS](https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.TIME.2025.15)); *A Datalog Rewriting Algorithm for Warded Ontologies*, Benedetto, Calautti, Hammad, Sallinger, Vlad-Starrabba, IJCAI 2025 ([IJCAI](https://www.ijcai.org/proceedings/2025/485)).
* **Formalism:** Warded Datalog± plus stratified negation, EGDs (harmless), monotonic aggregation, arithmetic, external sources; temporal DatalogMTL extension.
* **Core ideas:**
  1. **A termination strategy specific to warded programs.** The chase builds a *warded forest* (derivation trees along ward edges) and a *lifted linear forest*. A new fact is not produced when an **isomorphic** fact already exists in the relevant sub-tree (local isomorphism check). The lifted linear forest caches "patterns" that were already shown to be redundant. For warded rules this is sound and complete for query answering, and it avoids a global core computation.
  2. **A streaming, pipelined "pull" execution** (volcano-style) over a rule-graph query plan, with specialised dynamic indexes and **non-blocking monotonic aggregation** (`msum`, `mcount`, `mmax` …). Aggregates are computed incrementally as monotone contributions, so they can appear *inside* recursion. This is the key idea for recursion plus aggregation.
  3. **Harmful-join elimination** — a rewriting that turns programs with "harmful" joins (joins on affected positions) into warded form where possible. Also recent **Datalog rewriting of warded ontologies** (IJCAI 2025).
* **Borrow:** isomorphism-based pruning as the chase-termination strategy for classes proven *warded/shy* by the analyser; monotonic aggregation semantics (with a proof obligation in the analyser that aggregation is monotone); the idea that *the analyser's class drives the chase variant*, which Vadalog already does implicitly.

### 1.3 VLog, Rulewerk and Nemo (TU Dresden / VU Amsterdam, Krötzsch group)
* **VLog** (C++, Apache-2.0): Urbani, Jacobs, Krötzsch, *Column-oriented Datalog materialization for large knowledge graphs*, AAAI 2016 ([anthology](https://mlanthology.org/aaai/2016/urbani2016aaai-column/), [TR](https://arxiv.org/abs/1511.08915)). Existential support: Urbani, Krötzsch, Jacobs, Dragoste, Carral, *Efficient model construction for Horn logic with VLog*, IJCAR 2018 ([Springer](https://link.springer.com/chapter/10.1007/978-3-319-94205-6_44)) — skolem and **restricted chase** implemented column-wise. Superseded by Nemo; maintenance only **[current activity not verified; GitHub API blocked]**.
* **Rulewerk** (formerly VLog4j; Java, Apache-2.0): Java API and parser on top of VLog ([GitHub](https://github.com/knowsys/rulewerk)). Effectively in maintenance mode **[unverified]**.
* **Nemo** (Rust, dual MIT/Apache-2.0, verified in local clone; version 0.10.2-dev, commit 2026-08-20): Ivliev, Ellmauthaler, Gerlach, Marx, Meißner, Meusel, Krötzsch, *Nemo: First Glimpse of a New Rule Engine*, ICLP 2023 TC ([arXiv](https://arxiv.org/abs/2308.15897)); *Nemo: Your Friendly and Versatile Rule Reasoning Toolkit*, KR 2024 ([pdf](https://proceedings.kr.org/2024/70/kr2024-0070-ivliev-et-al.pdf)); [GitHub](https://github.com/knowsys/nemo). Supports Datalog, existential rules (restricted chase), stratified negation, aggregates, datatypes, SPARQL/CSV/RDF import, a web app, an LSP and Python/WASM bindings.
* **Core ideas:**
  1. **Columnar storage of sorted, deduplicated tables ("tries" of columns)** with interval/RLE compression, so memory stays small. The unit of work is a whole table per rule application (set-at-a-time), not a tuple.
  2. **Worst-case-optimal multiway joins (leapfrog triejoin)** over sorted column tries. Variable-order planning makes performance independent of atom order in rules ("syntax-independent performance").
  3. **Restricted chase done table-wise, "Datalog-first":** Datalog rules are saturated before existential rules are applied, which reduces the number of nulls and improves restricted-chase termination in practice (VLog IJCAR 2018; Carral et al.). Active triggers are computed as a set difference/anti-join of body matches against head matches.
  4. **Tracing without logging.** Nemo does not record derivations during the chase. To explain a fact it *partially grounds a rule head with the fact* and re-evaluates the body (see `nemo/src/execution/execution_engine/tracing/tree_query.rs`, "partial_grounding_for_rule_head_and_fact"). It returns proof trees, supports **tree queries** (a shape-based query over proofs), and pagination. The visual front-ends are EvonNemo ([XLoKR 2024](https://imld.de/cnt/uploads/2024-XLoKR-EvonNemo.pdf)) and *nev* ([EuroVis 2026 poster](https://imld.de/cnt/uploads/nev-poster-paper.pdf)).
  5. Nemo's "incremental" transformation (`rule_model/pipeline/transformations/incremental.rs`) is **demand-driven import of SPARQL sources**, not materialisation maintenance. Nemo has **no DRed/FBF**.
  6. Related: *Column-Oriented Datalog on the GPU* (2025, [arXiv](https://arxiv.org/abs/2501.13051)).
* **Borrow:** columnar sorted tables with LFTJ for the chase kernel (Rust makes this natural); Datalog-first scheduling of the restricted chase; post-hoc tracing by re-evaluation (zero overhead at materialisation time); the rule-model pipeline of transformations, each with `Origin` tracking, which is the base for traceability of rule-set rewritings.

### 1.4 DLV / DLV2 / DLV∃ (University of Calabria)
* **DLV∃:** Leone, Manna, Terracina, Veltri, *Efficiently computable Datalog∃ programs*, KR 2012 ([pdf](https://www.mat.unical.it/kr2012/shy.pdf)) introduced the **Shy** class. Shy strictly extends Datalog and linear Datalog∃ and keeps Datalog complexity. It is evaluated bottom-up by a **parsimonious chase**: a trigger fires only if no *homomorphic/isomorphic* image of the new atom already exists. For Shy this is complete for (atomic/CQ) query answering. Also *Magic-Sets for Datalog with existential quantifiers* (Datalog 2.0, 2012, [Springer](https://link.springer.com/chapter/10.1007/978-3-642-32925-8_5)). *Reasoning over ontologies with DLV* (2020, [Springer](https://link.springer.com/chapter/10.1007/978-3-030-49559-6_6)).
* **DLV2** (C++): Alviano et al., *The ASP system DLV2*, LPNMR 2017; grounder **I-DLV** (with magic sets) plus solver **wasp**. Free for academic and non-commercial use; commercial through DLVSystem **[licence terms not re-verified]**.
* **Core ideas:** (1) *parsimonious* chase as a sound and complete decision procedure for a syntactic class; (2) *magic sets for existential rules*, i.e. query-driven bottom-up evaluation; (3) ASP stable-model semantics for full non-stratified negation and disjunction.
* **Borrow:** magic-set rewriting for existential programs as an alternative to UCQ rewriting when the UCQ blows up; parsimonious/isomorphism pruning for Shy.

### 1.5 clingo / Potassco (University of Potsdam)
* C++, MIT, very active. Gebser, Kaminski, Kaufmann, Schaub, *Multi-shot ASP solving with clingo*, TPLP 2019.
* **Core ideas:** (1) semi-naive **grounding** (gringo) separated from **CDCL-based solving** (clasp); (2) **multi-shot** incremental solving (add, ground and solve in a loop); (3) theory extensions (clingo-dl, clingo-lpx).
* **Borrow:** a clear boundary between the "grounding/chase" layer and the "search" layer. If the analyser detects non-stratified negation or disjunction, *delegate to an ASP backend* instead of reimplementing search. Multi-shot is a model for an incremental API.

### 1.6 Soufflé (Oracle Labs / University of Sydney)
* C++ (compiles Datalog to C++), UPL-1.0, active ([site](https://souffle-lang.github.io)). Jordan, Scholz, Subotić, *Soufflé: On synthesis of program analyzers*, CAV 2016.
* **Core ideas:**
  1. **Staged compilation** (Futamura-style) from Datalog → RAM IR → specialised C++, and an interpreter for the same IR.
  2. **Automatic index selection** by minimum chain cover, so a relation needs few indexes (Subotić, Jordan, Chang, Fekete, Scholz, *Automatic index selection for large-scale Datalog computation*, PVLDB 2018). **Specialised concurrent data structures**: concurrent B-tree, *Brie* (a trie for dense data), and **eqrel** (a parallel union-find relation for equivalence relations; Nappa et al., PACT 2019).
  3. **Provenance with minimal overhead.** Each tuple is annotated with (rule id, height); minimal-height proof trees are reconstructed on demand. Average overhead is 1.31× on DOOP. Zhao, Subotić, Scholz, *Debugging large-scale Datalog: a scalable provenance evaluation strategy*, TOPLAS 2020 ([arXiv](https://arxiv.org/abs/1907.05045), [docs](https://souffle-lang.github.io/provenance2)).
* **Borrow:** the index-selection algorithm; eqrel/union-find as the equality substrate; *(rule, height) provenance annotations* as the cheap, always-on traceability layer. No existentials, so Soufflé is a model for the Datalog core, not for the chase.

### 1.7 Graal and InteGraal (Inria/LIRMM GraphIK → BOREAL)
* **Graal** (Java, CeCILL **[not re-verified]**): Baget, Leclère, Mugnier, Rocher, Sipieter, *Graal: a toolkit for query answering with existential rules*, RuleML 2015. It covers the DLGP format, GRD via piece-unifiers, the Kiabora analyser (Leclère, Mugnier, Rocher, RR 2013), chase variants, and UCQ rewriting. That rewriting is sound, complete and minimal, with a compiled pre-order (König, Leclère, Mugnier, Thomazo, SWJ 2015).
* **InteGraal** (Java, **Apache-2.0**, verified in poms): Baget, Bisquert, Leclère, Mugnier, Pérution-Kihli, Tornil, Ulliana, BDA 2023 ([GitLab releases](https://gitlab.inria.fr/rules/integraal/-/releases), [site](https://rules.gitlabpages.inria.fr/integraal-website/features/dlgp)). The Maven modules show: `grd`, `unifiers`, `forward-chaining`, `backward-chaining`, `graal-ruleset-analysis`, `redundancy`, `forgetting`, `views` (federated sources), `explanation`, `storage`, `query-evaluation`. It builds a federated fact base over heterogeneous sources through views/mappings. Benchmarking is done through **B-Runner** (RuleML+RR 2024, [Springer](https://link.springer.com/chapter/10.1007/978-3-031-72407-7_3)).
* **Core ideas:** (1) the **piece** as the unit of unification (GRD, rewriting); (2) combining GRD SCCs with per-SCC decidability classes (Baget, Leclère, Mugnier, Salvat, *On rules with existential variables: walking the decidability line*, AIJ 2011); (3) federated sources with rules as mappings. Recent theory: Buron, Mugnier, Thomazo, *Parallelisable existential rules: a story of pieces*, KR 2021 ([arXiv](https://arxiv.org/abs/2107.06054)); *Connected components and disjunctive existential rules* (2023, [arXiv](https://arxiv.org/abs/2310.12884)).
* **Borrow (keep):** everything the user already designed. In addition, InteGraal's `redundancy`, `forgetting` and `explanation` modules are direct prior art for the "rule-set optimisation with proven equivalence" and "explanations" phases.

### 1.8 Llunatic, ChaseFUN, PDQ (data-exchange / data-integration chase engines)
* **Llunatic** (Java, on PostgreSQL; open source on [GitHub](https://github.com/donatellosantoro/Llunatic), licence **[unverified]**): Geerts, Mecca, Papotti, Santoro, *The LLUNATIC data-cleaning framework*, PVLDB 2013; *That's all folks! LLUNATIC goes open source*, PVLDB 7(13) 2014 ([ACM](https://dl.acm.org/doi/abs/10.14778/2733004.2733031)); *Cleaning data with Llunatic*, VLDBJ 2020 ([Springer](https://link.springer.com/article/10.1007/s00778-019-00586-5)). Idea: **the chase runs as SQL inside the DBMS**, with TGDs *and* EGDs, a **chase tree** of repair alternatives and *cost managers* that prune it, and user interaction for conflicting EGDs. Useful for large KBs that live in an RDBMS (aerospace/defence).
* **ChaseFUN** (Java): Bonifati, Ileana, Linardi, *Functional dependencies unleashed for scalable data exchange*, SSDBM 2016 **[venue from memory, not re-verified]**. Idea: **stratify TGDs and EGDs/FDs** so that EGDs are applied in a partitioned, parallel way without interleaving costs. Useful for the "equality handling" phase.
* **PDQ** (Java; Benedikt, Leblay, Tsamoura, *PDQ: Proof-driven query answering over web-based data*, PVLDB 2014; *Querying with access patterns and integrity constraints*, PVLDB 2015; *Generating plans from proofs*, book 2016). Idea: **a chase proof of query entailment is compiled into an executable query plan** (reformulation under access restrictions and views), and cost-based search runs over proofs. Useful for rewriting under source constraints (InteGraal views) and for *proof-producing* equivalence checks.
* **ChaseBench** (Benedikt, Konstantinidis, Mecca, Motik, Papotti, Santoro, Tsamoura, *Benchmarking the chase*, PODS 2017, [pdf](https://www.cs.ox.ac.uk/boris.motik/pubs/bkmmpst17becnhmarking-chase.pdf), [site](https://dbunibas.github.io/chasebench/)) compared all of these.

### 1.9 Rust/Kotlin embedded Datalog libraries
* **crepe** (Rust, MIT/Apache): a procedural macro compiling Datalog to Rust at compile time with semi-naive evaluation and stratified negation. Idea: zero-cost embedding and type-checked relations.
* **ascent** (Rust, MIT): Sahebolamri, Gilray, Micinski, *Seamless deductive inference via macros*, CC 2022; *Bring your own data structures to Datalog*, OOPSLA 2023. Ideas: **lattices** (Flix-style, Madsen et al. PLDI 2016) for aggregation and fixpoints; **pluggable relation data structures** (e.g. union-find for equivalence); **parallel** evaluation with rayon.
* **datafrog** (Rust, rust-lang, used by Polonius): a minimal engine with no parser. `Variable`s are semi-naive deltas, and `leapjoin` is a leapfrog-style multiway join through `Leaper`s. Idea: a tiny, auditable semi-naive kernel, a good model for a trusted core.
* **Differential Datalog (DDlog)** (Rust codegen over McSherry's *differential dataflow*; MIT): Ryzhyk & Budiu, Datalog 2.0 2019. The repository is **archived** under `vmware-archive` ([GitHub](https://github.com/vmware-archive/differential-datalog)); the search snippet says archived 13 July 2026 **[archive date unverified; likely earlier]**. Idea: **fully incremental evaluation (insertions and deletions) through differential dataflow**, with multiset weights and timestamps. It is a lesson: very powerful, but heavy and hard to maintain. The underlying crates `timely`/`differential-dataflow` are still maintained **[unverified]**.
* **Kotlin/JVM:** there is no JVM Datalog/chase engine with performance comparable to Nemo/Soufflé. Rulewerk (Java wrapper over VLog through JNI), Graal/InteGraal and Jena rules are the references. This supports choosing Rust for the core (and JVM/Kotlin bindings if needed).

### 1.10 egglog (UW / UCSD)
* Rust, MIT, active. Zhang, Wang, Flatt, Cao, Zucker, Rosenthal, Tatlock, Willsey, *Better Together: Unifying Datalog and Equality Saturation*, PLDI 2023 ([arXiv](https://arxiv.org/abs/2304.04332)); PLDI 2025 tutorial.
* **Core ideas:** (1) **e-graph = relational database + union-find**; congruence closure is a "rebuild" step that canonicalises tables after merges, i.e. equality handled *by representative* inside a relational engine (same spirit as RDFox sameAs rewriting, but with functional dependencies/congruence); (2) **lattice-valued functions (`:merge`)** for aggregation-like reasoning; (3) semi-naive evaluation over e-graphs and **extraction** of the optimal term.
* **Borrow:** the egglog rebuild algorithm as the implementation of EGDs/`owl:sameAs` together with functional dependencies (nulls merged by EGDs); possibly equality saturation over *rule sets* for rule-set optimisation (finding equivalent, cheaper rule sets, with certificates).

### 1.11 Scallop (UPenn)
* Rust, MIT, 45 KLoC; Li, Huang, Naik, *Scallop: A Language for Neurosymbolic Programming*, PLDI 2023 ([ACM](https://dl.acm.org/doi/10.1145/3591280), [pdf](https://www.cis.upenn.edu/~mhnaik/papers/pldi23.pdf)). Follow-up: Lobster (GPU, 2025, [arXiv](https://arxiv.org/abs/2503.21937)).
* **Core idea:** evaluation is **parametric in a provenance semiring**. The same engine computes plain sets, why-provenance, top-k proofs, probabilities or gradients by plugging in a semiring implementation (Green, Karvounarakis, Tannen, *Provenance semirings*, PODS 2007).
* **Borrow:** a `Provenance` trait/type parameter on the evaluation kernel. Unit (Boolean) is used for speed, and *top-k proofs* / *why* are used for small enterprise KBs where full explanation is wanted.

### 1.12 Ontology reasoners with transferable ideas
* **ELK** (Java, Apache-2.0; Kazakov, Krötzsch, Simančík, *The incredible ELK*, JAR 2014). Ideas: (1) **consequence-based saturation, parallelised by "contexts"** (each concept a work unit, lock-free job queues); (2) **incremental reasoning without bookkeeping** (Kazakov & Klinov, ISWC 2013); (3) **goal-directed tracing of inferences**: proofs are recomputed on demand by re-running the relevant context instead of storing all inferences (Kazakov & Klinov, ISWC 2014). Point (3) is the same principle as Nemo tracing.
* **Konclude** (C++, LGPL; Steigmiller, Liebig, Glimm, JWS 2014). Idea: a **hybrid** of tableau and saturation — cheap saturation handles most of the work, and full tableau is used only where needed; parallel; aggressive caching. This is the same "analyser picks the cheapest complete procedure per part" philosophy.
* **Ontop** (Java, Apache-2.0; Calvanese et al., *Ontop: answering SPARQL queries over relational databases*, SWJ 2017; Rodríguez-Muro, Kontchakov, Zakharyaschev, ISWC 2013). Ideas: (1) **T-mappings**: the ontology (OWL 2 QL/Datalog part) is compiled *into the mappings* once, offline, so that query rewriting at run time only handles the part that cannot be compiled (tree witnesses); (2) **tree-witness rewriting** (Kikot, Kontchakov, Zakharyaschev, KR 2012), which avoids the exponential UCQ blow-up by producing non-UCQ (NDL/positive existential) rewritings; (3) **semantic query optimisation using database constraints** (keys, FKs) to eliminate redundant self-joins and unions.
* Other rewriting engines worth citing: Nyaya (Gottlob, Orsi, Pieris, ICDE 2011 / TODS 2014, *Query rewriting and optimization for ontological databases*, [ACM](https://dl.acm.org/doi/10.1145/2638546)), Rapid, Clipper, Requiem, and Graal's own piece-based rewriter.

---

## 2. Comparison matrix

Legend: ✓ supported, ~ partial/limited, ✗ absent, ? unverified.

| System | Lang. | Licence | Status 2024-26 | ∃-rules | Chase variant / termination | Strat. neg. | Aggreg. | Equality | Incremental | Query rewriting | Explanations | Key idea to borrow |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| RDFox | C++ | Commercial | Active (Samsung, 2024) | ~ (manual `rdfox:SKOLEM`) | Skolem, user-controlled; no check? | ✓ | ✓ | ✓ rewriting (union-find) | ✓ DRed/B-F/FBF | ✗ | ✓ explain **[?]** | parallel materialisation, FBF, sameAs rewriting, modular/hypertree |
| Vadalog | Java | Proprietary (Prometheux) | Active (Parallel, Temporal, 2025) | ✓ warded | isomorphism pruning over warded/linear forests | ✓ | ✓ monotonic, in recursion | ✓ harmless EGDs | ? | ✓ warded→Datalog (IJCAI 25) | ✓ claimed | warded termination, monotonic aggregation |
| Nemo | Rust | MIT/Apache | Very active (v0.10, 2026) | ✓ | restricted, Datalog-first | ✓ | ✓ | ~ | ✗ (incremental *imports* only) | ✗ | ✓ tracing, tree queries | columnar + LFTJ, post-hoc tracing |
| VLog | C++ | Apache-2.0 | Maintenance **[?]** | ✓ | skolem / restricted | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | column-oriented chase |
| Rulewerk | Java | Apache-2.0 | Maintenance **[?]** | ✓ (via VLog) | as VLog | ✓ | ✗ | ✗ | ✗ | ✗ | ~ | Java API over native core |
| DLV∃ / DLV2 | C++ | Academic free / commercial **[?]** | DLV2 active; DLV∃ research prototype | ✓ Shy | parsimonious chase | ✓ (+ASP) | ✓ | ~ | ✗ | magic sets | ✗ | Shy + parsimonious, magic sets for ∃ |
| clingo | C++ | MIT | Very active | ✗ (grounded) | n/a | ✓ full ASP | ✓ | ✗ | multi-shot | ✗ | ~ (unsat cores) | grounding/solving split, multi-shot |
| Soufflé | C++ | UPL-1.0 | Active | ✗ | n/a | ✓ | ✓ | ✓ eqrel | ✗ | ✗ | ✓ (rule,height) provenance | compilation, index selection, provenance |
| Graal | Java | CeCILL **[?]** | Superseded by InteGraal | ✓ | oblivious/semi-obl./restricted/core | ~ | ✗ | ✗ | ✗ | ✓ UCQ (pieces) | ✗ | pieces, GRD, Kiabora |
| InteGraal | Java | Apache-2.0 | Active (Inria BOREAL) | ✓ | several; GRD-driven | ✓ | ~ **[?]** | ~ | ✗ **[?]** | ✓ UCQ + views | ✓ module | federation, redundancy, forgetting |
| Llunatic | Java/SQL | open source **[?]** | Dormant **[?]** | ✓ TGD+EGD | chase tree + cost manager | ✗ | ✗ | ✓ EGDs | ✗ | ✗ | ~ (repair lineage) | chase inside RDBMS |
| ChaseFUN | Java | ? | Dormant | ✓ | TGD/EGD stratified | ✗ | ✗ | ✓ FDs | ✗ | ✗ | ✗ | FD/EGD stratification |
| PDQ | Java | ? | Dormant **[?]** | ✓ | proof-driven | ✗ | ✗ | ✓ | ✗ | ✓ plans from proofs | ✓ proofs | proofs → plans |
| DDlog | Rust | MIT | **Archived** | ✗ | n/a | ✓ | ✓ | ✗ | ✓ full (differential) | ✗ | ✗ | differential dataflow |
| crepe / ascent / datafrog | Rust | MIT/Apache | Active / active / stable | ✗ | n/a | ✓ / ✓ / manual | ✗ / lattices / manual | ✗ / BYODS union-find / ✗ | ✗ | ✗ | ✗ | macros, lattices, leapjoin kernel |
| egglog | Rust | MIT | Very active | ~ (function symbols) | saturation + rebuild | ✓ | lattices (merge) | ✓ congruence closure | ✓ semi-naive | ✗ | ~ proofs (experimental) **[?]** | DB + union-find rebuild |
| Scallop | Rust | MIT | Active | ✗ (value creation limited) | n/a | ✓ | ✓ | ✗ | ✗ | ✗ | ✓ semiring-parametric | provenance semirings |
| ELK | Java | Apache-2.0 | Maintained | n/a (EL) | consequence-based | n/a | n/a | n/a | ✓ no bookkeeping | n/a | ✓ goal-directed tracing | contexts, on-demand tracing |
| Konclude | C++ | LGPL | Maintained **[?]** | n/a (SROIQ) | tableau + saturation | n/a | n/a | ✓ | ~ | n/a | ✗ | hybrid cheap/complete |
| Ontop | Java | Apache-2.0 | Very active | OWL 2 QL | rewriting only | ~ | ✓ (SPARQL) | ~ | virtual | ✓ T-mappings, tree witness | ~ | compile ontology into mappings |

---

## 3. Benchmarks — what is standard for existential rules today

| Benchmark | Content | Used by | Status |
|---|---|---|---|
| **ChaseBench** (PODS 2017, [site](https://dbunibas.github.io/chasebench/)) | Scenarios with s-t TGDs, target TGDs, EGDs, queries: *STB-128*, *ONT-256* (generated with iBench), *Doctors* / *Doctors-FD* (data exchange), *LUBM*, **Deep** (deep100/200/300: deep chases with many nulls). A common format and tools. | Graal, RDFox, Llunatic, PDQ, VLog, Nemo, InteGraal (through B-Runner) | **De facto standard** for the chase |
| **iBench** (Arocena, Glavic, Ciucanu, Miller, PVLDB 2015) | A *generator* of schema mappings/metadata; ChaseBench's STB/ONT were made with it | data-exchange community | generator, not a fixed set |
| **LUBM** (Guo, Pan, Heflin 2005) / **UOBM** (Ma et al. 2006) | University ontologies with a data generator; UOBM adds OWL DL/Lite features harder than LUBM | RDFox, VLog, Nemo, all RDF stores | standard for Datalog/OWL RL scale; weak on ∃ |
| Oxford/VLog "real ontology" suites | Reactome, UniProt, ChEMBL, Claros, DBpedia, UOBM translated into ∃-rules (Carral et al., *VLog: a rule engine for knowledge graphs*, ISWC 2019; IJCAR 2018) | VLog, Nemo, Graal | common in KR papers **[exact list not re-verified]** |
| **iWarded** (Baldazzi et al., [arXiv 2103.08588](https://arxiv.org/abs/2103.08588)) | Generator of warded programs with controllable recursion/harmful joins | Vadalog | standard for warded |
| **B-Runner** (RuleML+RR 2024) | Infrastructure for repeatable benchmarking of rule reasoners | InteGraal team | recommended harness |
| DOOP/DaCapo | Program-analysis Datalog | Soufflé, ascent | Datalog only |

Recommendation: ChaseBench (including Deep and Doctors) plus LUBM/UOBM plus iWarded, and a set of enterprise-modelling KBs of your own, all run through B-Runner. Add chase-termination suites from the acyclicity literature (MFA/RMFA test ontologies: Cuenca Grau et al., JAIR 2013; Carral, Dragoste, Krötzsch, IJCAI 2017) to exercise the analyser.

---

## 4. Explanation and provenance — best approach for traceability

| Approach | Reference | Cost | Fits |
|---|---|---|---|
| **Full semiring provenance** (N[X], why, lineage) | Green, Karvounarakis, Tannen, PODS 2007; Scallop | Exponential in the worst case; heavy in memory | small KBs, "all explanations" |
| **Top-k proofs semiring** | Scallop | bounded (k) | small/medium KBs |
| **(rule id, height) annotations + on-demand minimal proof tree** | Soufflé, TOPLAS 2020 | ~1.3× time, 2 ints per fact | large KBs, always on |
| **No logging; re-derive by partial grounding of the head** | Nemo tracing; ELK goal-directed tracing | zero at materialisation, cost at query time | large KBs, rare explanation queries |
| **Why-provenance / minimal sub-KB (justifications)** | Calautti, Livshits, Pieris, Schneider, *The complexity of why-provenance for Datalog queries* (2023/24) **[venue not re-verified]**; OWL justifications | hard in general (NP-hard for Datalog), computed on demand | "which input facts/rules matter" |

**Recommended design:** make provenance a **type parameter of the evaluation kernel** (Scallop). The default is Boolean plus Soufflé-style (rule, height) annotations, which are cheap and always on; they also record the **trigger that created each null** (rule id plus frontier image), which is essential for explaining existential facts. Proof trees are rebuilt on demand (Soufflé/Nemo/ELK). Switch to top-k or why semirings for enterprise-scale KBs. Keep `Origin` links from every rewritten or optimised rule back to the source rule (the Nemo rule-model pipeline and InteGraal `explanation`), so that an explanation over an optimised rule set maps back to the user's rules.

---

## 5. Prioritised "ideas to borrow"

Phases: DM = data model/storage, HOM = homomorphism/joins, CH = chase, RW = rewriting, AN = analyser, NA = negation/aggregation, INC = incremental, EX = explanations, OPT = rule-set optimisation.

| # | Idea | Source (system / paper) | Benefit | Cost | Phase |
|---|---|---|---|---|---|
| 1 | **Analyser picks a certified procedure per GRD SCC**: SCC classes (FES/BTS/FUS: acyclicity, MFA/RMFA, warded, shy, linear, sticky) combined over the SCC order; the result is "complete (proof: class X)" or "completeness not guaranteed" | Graal/Kiabora (Baget et al. AIJ 2011; Leclère et al. RR 2013); Vadalog; DLV∃; Cuenca Grau et al. JAIR 2013; Carral et al. IJCAI 2017 | the core promise of the engine | medium (already largely designed) | AN |
| 2 | **Columnar sorted tables + worst-case-optimal multiway join (LFTJ)**, variable-order planning independent of atom order; hypertree/GHD plans for large bodies (bi-connected components are a special case) | Nemo (ICLP 2023, KR 2024); datafrog leapjoin; Zhang et al. IJCAI 2023 | robust performance on large KBs; fits Rust | high | DM, HOM |
| 3 | **Restricted chase, table-at-a-time, Datalog-first scheduling** (per stratum/SCC) | VLog IJCAR 2018; Nemo | fewer nulls, better termination in practice | medium | CH |
| 4 | **Class-specific termination by isomorphism/parsimony** when the analyser proves warded/shy, instead of a general core computation | Vadalog (VLDB 2018, TODS 2022); DLV∃ (KR 2012) | complete and terminating on classes where the restricted chase may not terminate | medium | CH, AN |
| 5 | **Provenance-parametric kernel**: cheap (rule, height, trigger) annotations always on; proof trees on demand; top-k/why semirings as options | Soufflé TOPLAS 2020; Scallop PLDI 2023; Nemo tracing; ELK tracing | traceability at controlled cost | medium | EX |
| 6 | **Equality by representative (union-find) plus rebuild**, for `sameAs`, EGDs and functional dependencies on nulls | RDFox IJCAI 2015; egglog PLDI 2023; Soufflé eqrel; ChaseFUN (EGD stratification) | sound EGD support, no quadratic blow-up of equality axioms | medium | DM, CH |
| 7 | **FBF (or DRed) incremental maintenance for Datalog strata**; recompute existential strata or use the Skolem chase for them | RDFox AIJ 2019, AAAI 2018 | essential for aerospace/defence update workloads | high | INC |
| 8 | **Monotonic aggregation inside recursion**, with the analyser checking monotonicity; otherwise stratified aggregation; lattices as the general mechanism | Vadalog; ascent (OOPSLA 2023); Flix (PLDI 2016); egglog `:merge` | expressive aggregates without losing soundness | medium | NA |
| 9 | **Compile the ontology offline (T-mappings) and avoid UCQ blow-up** with tree-witness/NDL rewritings or magic sets for existential rules when the UCQ explodes | Ontop (ISWC 2013, SWJ 2017); Kikot et al. KR 2012; DLV∃ magic sets; Nyaya; Graal minimal UCQ | scalable rewriting and OBDA over enterprise RDBMS | medium-high | RW |
| 10 | **Modular materialisation**: specialised algorithms for recognised rule patterns (transitive closure, symmetry, equivalence) chosen by the analyser | RDFox AAAI 2019; Soufflé eqrel; ELK | big constant-factor wins; natural in an analyser-driven design | low-medium | AN, CH |
| 11 | **Rule-model pipeline with `Origin` provenance for each transformation** + certificates of equivalence (chase-based containment proofs à la PDQ) + redundancy/forgetting modules | Nemo pipeline; InteGraal `redundancy`/`forgetting`; PDQ (PVLDB 2014) | proven-equivalent optimisation with explanations mapped back to source | medium | OPT, EX |
| 12 | **Automatic index selection (minimum chain cover)** when using B-tree style indexes | Soufflé PVLDB 2018 | fewer indexes, less memory | low | DM |
| 13 | **Parallel semi-naive with lock-free insertion and work-stealing by fact** | RDFox AAAI 2014; ascent (rayon); ELK contexts | multi-core scaling | medium-high | CH |
| 14 | **Delegate non-stratified negation / disjunction to ASP (clingo)** instead of reimplementing search; multi-shot as the incremental API model | clingo TPLP 2019; DLV2 | future expressiveness at low cost; the soundness boundary stays clear | low | NA |
| 15 | **Chase in the RDBMS (SQL generation)** for KBs that do not fit in memory | Llunatic (PVLDB 2013/14) | out-of-core option for defence data | high | CH, DM |
| 16 | **Equality saturation over rule sets** to search equivalent cheaper programs (with extraction by cost) | egglog | research-grade OPT | high / research | OPT |
| 17 | **Staged compilation (Datalog → IR → native)** for fixed rule sets deployed repeatedly | Soufflé CAV 2016; crepe | top speed for stable ontologies | high | CH |

Not recommended: re-implementing DDlog-style differential dataflow. DDlog is archived and heavy; FBF is a better fit for set-based rules.

---

## 6. Unverified points / caveats
* RDFox: that there is no termination check on Skolem chains, and that `explain` returns proof trees — docs site blocked; based on snippets and prior knowledge.
* DDlog archive date ("13 July 2026" in a search snippet) — probably an artefact; the repository has been under `vmware-archive` since around 2023.
* VLog/Rulewerk activity, Graal licence (CeCILL), Llunatic/ChaseFUN/PDQ licences, DLV2 licence terms, Konclude status — GitHub API blocked.
* ChaseFUN venue (SSDBM 2016), Calautti et al. why-provenance venue, and the exact "real ontology" benchmark list of VLog/Nemo papers — from memory.
* egglog proof/explanation support maturity.
* InteGraal aggregation and incremental support — only module names were inspected, not the code.
