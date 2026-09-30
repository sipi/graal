# Graal fork: is it the right starting point? Ecosystem research

Date of research: 2026-09-30. Method: web search, GitHub metadata, Maven Central (repo1.maven.org) directory listings and POMs, and the local clone at /home/user/graal. Some Inria hosts (gitlab.inria.fr, rules.gitlabpages.inria.fr, team.inria.fr, radar.inria.fr) and mvnrepository.com / proceedings.kr.org were **blocked by the egress proxy**, so facts from those sites come only from search-result snippets, and are marked as such.

---

## 1. Graal upstream status

| Item | Finding | Source |
|---|---|---|
| Official repo | `graphik-team/graal` on GitHub, default branch `develop`, 51 stars, 13 forks, not archived | https://github.com/graphik-team/graal |
| Last push | GitHub `pushed_at` 2022-06-21 (the same value as the sipi/graal fork, which suggests a branch sync or bot push, not new development). Local clone: last real commit **2019-03-11** (Clément Sipieter), first in the clone 2017-06 | GitHub API; `git log` in the local clone |
| Last release | No GitHub releases. The last Maven Central release is **`graal-core` 1.3.1, published 2018-03-15**. The clone is at `1.3.2-SNAPSHOT` | https://repo1.maven.org/maven2/fr/lirmm/graphik/graal-core/maven-metadata.xml , https://github.com/graphik-team/graal/releases |
| License | **CeCILL v2.1** (GPL-compatible copyleft; LICENSE and LICENSE-fr files) | https://github.com/graphik-team/graal , local LICENSE |
| Code size | About 603 Java files, about 85k lines (local clone) | local |
| Tech debt signals | Java 7/8-era Maven build, JUnit 4.12, slf4j 1.7.7, FindBugs/PMD. Storage modules target old RDF4J/OpenRDF, Neo4j and Blueprints versions (see the Maven artifact list) | local pom.xml; https://repo1.maven.org/maven2/fr/lirmm/graphik/ |
| Docs / papers | Homepage and papers: DLGP v2.0 spec, framework overview, and the Graal paper (Baget et al., RuleML 2015) | https://graphik-team.github.io/graal/ , https://graphik-team.github.io/graal/papers/datalog+_v2.0_en.pdf , https://graphik-team.github.io/graal/papers/framework_en.pdf |

**Verdict:** Graal has been effectively abandoned upstream since about 2019. The team moved on (see section 2).

---

## 2. Successor from the same team: InteGraal (team BOREAL, formerly GraphIK)

GraphIK became the Inria/LIRMM/Université de Montpellier team **BOREAL** (Knowledge and Rules for Reasoning on Heterogeneous Data). BOREAL is the team name, not a separate software product.

| Item | Finding | Source |
|---|---|---|
| Positioning | Inria reports call InteGraal "the result of a complete re-engineering of the Graal tool". Development began in late 2020 "with the goal of providing a major version of the former Graal software". Since 2021 it has been "the main Java platform developed by the team" | search snippets of https://radar.inria.fr/report/2022/boreal , https://radar.inria.fr/report/2023/boreal/index.html , https://radar.inria.fr/report/2025/boreal/index.html (the pages themselves were blocked) |
| Code hosting | `https://gitlab.inria.fr/rules/integraal` (Inria GitLab, not GitHub). Website: `https://rules.gitlabpages.inria.fr/integraal-website/` | POM `<url>`/`<scm>`; search results |
| License | **Apache License 2.0** (declared in the POM). This is far more permissive than Graal's CeCILL | https://repo1.maven.org/maven2/fr/lirmm/graphik/integraal/2.0.7/integraal-2.0.7.pom |
| Language / toolchain | Java, `maven.compiler.release` **21**, JPMS (`module-info.java`), Guava 33, JGraphT 1.5.2, picocli, recent Maven plugins | same POM |
| Releases on Maven Central | `fr.lirmm.graphik:integraal-*` versions 1.4.0 through **2.0.7**, last updated **2025-06-11** | https://repo1.maven.org/maven2/fr/lirmm/graphik/integraal/maven-metadata.xml |
| Newer versions? | A search result showed a Javadoc page titled "InteGraal ... **3.2.2** API" with package `fr.lirmm.graphik.integraal.api.core`. This suggests 3.x releases exist outside Maven Central (for example in the GitLab package registry). **UNVERIFIED**: the host was blocked | https://rules.gitlabpages.inria.fr/integraal/fr.lirmm.integraal.rule_analysis/fr/lirmm/graphik/integraal/api/core/class-use/Atom.html |
| Modules (2.0.7) | model, util, storage, io, query-evaluation, unifiers, grd, forward-chaining, backward-chaining, graal-ruleset-analysis, redundancy, forgetting, views, explanation, configuration, component, api, core, all | POM `<modules>` |
| Chase (verified in the 2.0.7 sources jar) | Oblivious, semi-oblivious and restricted trigger checkers; Skolem variants (body, frontier, frontier-by-piece); breadth-first, parallel and multi-thread rule appliers; semi-naive; GRD-based scheduler; **stratified chase** (stratified negation); core and local-core computation; lineage tracking (federated); timeouts and step or atom limits | https://repo1.maven.org/maven2/fr/lirmm/graphik/integraal-forward-chaining/2.0.7/ |
| Storage backends (verified in the storage POM) | In-memory, SQL (SQLite, PostgreSQL, HSQLDB, MySQL), RDF4J (in-memory, SPARQL endpoint), MongoDB. Mappings and views over federated sources, including Web APIs | https://repo1.maven.org/maven2/fr/lirmm/graphik/integraal-storage/2.0.7/integraal-storage-2.0.7.pom , parent POM description |
| Other features | Query rewriting (backward chaining, with disjunctive-rule and mapping work from KR 2023), rule-set analysis (ported from Graal's rules-analyser / Kiabora), redundancy, forgetting, **explanation** module, DLGP / DLGPE formats | POM; KR 2023: https://proceedings.kr.org/2023/42/kr2023-0042-leclere-et-al.pdf |
| Satellites (2025) | Py4Graal (Python bindings), DLGPE (extended DLGP), NanoParse, IRIRef | search snippet of the 2025 BOREAL report |
| Related tooling | `brunner-*` (benchmark runner, with `brunner-integraal` and `brunner-vlog`), last updated 2024-09 | https://repo1.maven.org/maven2/fr/lirmm/graphik/ |
| Paper | Baget, Bisquert, Leclère, Mugnier, Pérution-Kihli, Tornil, Ulliana, "InteGraal: a Tool for Data-Integration and Reasoning" (venue not verified; listed in the BOREAL reports) | https://www.lirmm.fr/~mugnier/CV/pub-mlmugnier.html |
| Maturity caveats | A research-lab tool with a small core team (the POM lists one developer, Florent Tornil, an Inria engineer). Community outside the lab is small. It is hosted on Inria GitLab, where external issues and PRs are harder to file. The API changed across 1.x, 2.x and 3.x | inferred from the above |

**Verdict:** InteGraal is exactly the "refurbished Graal" the founder wants to build: modern Java 21, Apache-2.0, more chase variants, stratified negation, explanations, federated storage. It was active through at least mid-2025, with signs of 3.x releases after that.

---

## 3. Alternatives

GitHub data (stars, last push, license) comes from the GitHub search API as of 2026-09-30. Latest tags come from `git ls-remote`.

| Engine | Lang | License | Activity / maturity | Existentials | Negation | Aggregates | Perf. reputation | JVM interop |
|---|---|---|---|---|---|---|---|---|
| **Graal** (fork) | Java 7/8 | CeCILL 2.1 | Dead since 2019. Last release 1.3.1 (2018) | Yes (several chases, rewriting) | Limited | No | Research-grade, not fast | Native |
| **InteGraal** | Java 21 | Apache-2.0 | Active research tool. 2.0.7 (06/2025), 3.x likely | Yes (oblivious, semi-oblivious, restricted, core; rewriting) | Stratified | Unverified | Research-grade. Benchmarked vs VLog via brunner | Native (Maven Central) |
| **Nemo** (TU Dresden, knowsys) | Rust | Apache-2.0 | 300 stars, pushed 2026-09. Last tag v0.10.1 (08/2024). README says "heavy development, unstable". KR 2024 paper | Yes (TGDs; restricted chase to my knowledge, not verified in docs) | Stratified | Yes | Fast, scalable in-memory. Successor of VLog | **No Java binding** (Python, WASM, CLI). Would need JNI/FFM or a subprocess |
| **VLog** | C++ | Apache-2.0 | Superseded by Nemo. Last push 2023-07 | Yes (restricted / Skolem chase) | Stratified | No | Historically among the fastest chase engines | Via Rulewerk |
| **Rulewerk** (Java wrapper for VLog) | Java | Apache-2.0 | Last push 2025-07, last tag v0.9.0. Maintenance mode | Yes (via VLog) | Stratified | No | That of VLog | Native Java API, but needs VLog native binaries |
| **RDFox** (Oxford Semantic / **Samsung** since 2024) | C++ | Commercial (free licences on request) | Mature, production-grade | Limited (Skolem-style via built-ins; not a general existential-rule engine) | Stratified | Stratified | Very fast, incremental maintenance | Official Java API, REST, SPARQL |
| **Vadalog** (Oxford / Prometheux) | Java/Scala (not verified) | Commercial licence only | Industrial (Bank of Italy). Parallel version (VLDB) | Yes: **Warded Datalog±** | Yes | Yes | Good, scalable | JVM-based, but closed |
| **Soufflé** | C++ (Datalog to C++) | UPL-1.0 | 1.2k stars, 2.5 release, pushed 2026-07 | No | Stratified | Yes | Excellent for static analysis | Weak (subprocess, SWIG) |
| **clingo** (Potassco, ASP) | C++ | MIT | 838 stars, v5.8.2, very active | No, only via grounding and function symbols; no existentials | Yes (stable models, non-stratified) | Yes | Mature; grounding bottleneck on large data | C API; Python first-class; Java via community bindings |
| **Scallop** (UPenn) | Rust | MIT | 516 stars, 0.2.4, pushed 2026-06 | No | Stratified | Yes | Good. **Probabilistic / differentiable** Datalog; has an LLM plugin interface | Python-first |
| **egglog** | Rust | MIT | 851 stars, v3.0.0, very active | No (e-graphs + Datalog, equality saturation) | No | Merge functions | Excellent for rewriting | None (Rust, Python) |
| **AbcDatalog** (Harvard) | Java 21 | BSD | Small, still maintained | No | Stratified | No | Modest, multithreaded bottom-up and magic sets | Native |
| **XTDB** (ex-Crux) / Datomic-style | Clojure | MPL-2.0 | Very active, but v2 is now SQL-first | No | Limited | Yes | Database, not a reasoner | Native JVM |
| **2P-Kt** (tuProlog) | **Kotlin MPP** | Apache-2.0 | 1.4.x, still released in 2026 | No (Prolog SLD resolution) | NAF | Via Prolog | Modest | Native Kotlin/JVM/JS |

Sources:
- Nemo: https://github.com/knowsys/nemo , https://github.com/knowsys/nemo/releases , KR 2024 paper https://proceedings.kr.org/2024/70/kr2024-0070-ivliev-et-al.pdf , https://iccl.inf.tu-dresden.de/web/Nemo/en , https://ceur-ws.org/Vol-3801/short3.pdf
- VLog: https://github.com/karmaresearch/vlog
- Rulewerk: https://github.com/knowsys/rulewerk
- RDFox: https://docs.oxfordsemantic.tech/reasoning.html , https://www.oxfordsemantic.tech/rdfox , Samsung acquisition: https://news.samsung.com/global/samsung-electronics-announces-acquisition-of-oxford-semantic-technologies-uk-based-knowledge-graph-startup , https://techcrunch.com/2024/07/18/samsung-to-acquire-uk-based-knowledge-graph-startup-oxford-semantic-technologies/
- Vadalog: https://www.vldb.org/pvldb/vol11/p975-bellomarini.pdf , https://github.com/prometheuxresearch/VadalogParallel-Experiments (production licence by contacting Prometheux)
- Soufflé: https://github.com/souffle-lang/souffle
- clingo: https://github.com/potassco/clingo
- Scallop: https://github.com/scallop-lang/scallop
- egglog: https://github.com/egraphs-good/egglog
- AbcDatalog: https://github.com/HarvardPL/AbcDatalog
- XTDB: https://github.com/xtdb/xtdb
- 2P-Kt: https://github.com/tuProlog/2p-kt , https://www.sciencedirect.com/science/article/pii/S2352711021001126

Notes and caveats:
- Nemo's chase variant, Vadalog's implementation language, RDFox's exact existential support, and InteGraal's aggregation support were **not verified** against primary docs in this session.
- Only three candidates cover general existential rules and have an open licence: **InteGraal** (JVM), **Nemo** (Rust) and VLog/Rulewerk (legacy).

---

## 4. Kotlin angle

- **Kotlin logic libraries:** the only serious one found is **2P-Kt** (tuProlog reboot: Kotlin Multiplatform, Apache-2.0, modules for terms, unification, theory indexing and a solver; release 1.4.1 in September 2026 per its releases page). It does Prolog, not existential rules or a chase, but its term, unification and indexing modules show that Kotlin fits this domain. No Kotlin Datalog± / chase engine was found.
  - https://github.com/tuProlog/2p-kt
  - https://github.com/tuProlog/2p-kt/releases
- **Partial Java-to-Kotlin conversion, evidence:**
  - **Benefits.** Kotlin and Java interoperate at the bytecode level, so file-by-file migration is feasible. Meta migrated about 10M lines incrementally and reported fewer NPEs and more concise code.
    - https://engineering.fb.com/2022/10/24/android/android-java-kotlin-migration/
    - https://kotlinlang.org/docs/java-interop.html
  - **Costs.**
    - Platform types (`T!`) leak unknown nullability at every Java/Kotlin boundary.
    - The J2K converter output needs manual rework.
    - A library consumed from Java needs `@JvmStatic`, `@JvmOverloads` and `@JvmName` hygiene, and must avoid Kotlin-only idioms in its public API (companion objects, default args, `suspend`).
    - The build then needs mixed Maven/Gradle Kotlin compilation.
    - Sources: https://developer.android.com/kotlin/interop , https://kt.academy/article/ak-java-interop-1
  - **Implication for this project.** A *partial* conversion of a Java codebase that is 85k lines and seven years stale doubles the toolchain surface without adding capability. It pays off only if new code is written in Kotlin, for example a Kotlin DSL or facade over a Java core. Rewriting the internals does not pay off.

---

## 5. LLM + symbolic reasoning (brief)

1. **Logic-LM** (Pan et al., Findings of EMNLP 2023): the LLM translates NL into a symbolic program (Prolog/FOL/CSP/SAT), a solver runs it, and solver errors feed self-refinement. It reports +39% over standard prompting. https://arxiv.org/abs/2305.12295
2. **Scallop / VIEIRA** (Li et al., AAAI 2024): Datalog with foundation models (GPT, CLIP, SAM) as relational foreign predicates. This is the closest existing design to "LLMs inside a rule engine". https://arxiv.org/abs/2412.14515 , https://www.scallop-lang.org/papers/aaai24.pdf
3. **"Current Practices for Building LLM-Powered Reasoning Tools Are Ad Hoc -- and We Can Do Better"** (2025) argues for principled neurosymbolic tool integration. https://arxiv.org/pdf/2507.05886
4. **NeuSymMS** (2026) couples LLM fact extraction with a CLIPS rule engine as agent memory. **ATA** decouples LLM agents into offline symbolic KB ingestion and deterministic online execution. https://arxiv.org/html/2605.17596v2 (ATA URL not captured)
5. **Ontology-constrained enterprise agents** (2026). https://arxiv.org/pdf/2604.00555

**Takeaway:** the dominant pattern is that the LLM acts as a translator or fact extractor, and a deterministic engine does inference, supplies provenance and explanations, and gives error feedback. The features that matter most for that backend are:
- explanations and provenance
- incremental updates
- a robust parser with good error messages
- termination guarantees or chase limits
- an embeddable API or service

Those matter more than raw peak throughput. InteGraal already has lineage and explanation modules and timeouts; Graal has none of these.

---

## 6. Assessment: fork Graal vs adopt InteGraal vs other engine vs from scratch

| Option | Pros | Cons |
|---|---|---|
| **Fork Graal + refurbish** | Familiar code (the founder co-authored it); JVM | The original authors already did this refurbishment: it is called InteGraal. It is copyleft CeCILL, 85k lines of Java-8-era code, and would take months of dependency and modernization work before any new capability. No community, and it misses stratified negation, explanations and modern chase work |
| **Adopt InteGraal** (depend on it or fork it) | Apache-2.0, Java 21, on Maven Central, active research team, and a superset of Graal features. Same formalism and DLGP syntax. Kotlin can wrap it cleanly | Research-grade stability and API churn (1.x to 3.x), small team, GitLab-hosted. The 3.x state and roadmap are unverified. Performance is not in the league of Nemo or RDFox |
| **Adopt Nemo** | Fastest open existential-rule engine, active, Apache-2.0, with aggregates, negation and tracing | Rust with no JVM binding; self-declared "unstable" releases; last tagged release is 2024 |
| **RDFox / Vadalog** | Production-grade and fast | Commercial and closed; RDFox has only limited existentials |
| **Write from scratch** (Kotlin) | Full control, clean design for agent use cases | Chase termination, homomorphism optimisation, rewriting and rule analysis are hard and well-studied. It would re-derive years of GraphIK/BOREAL work |

**Recommendation.** Do not refurbish the 2019 Graal fork. Start from **InteGraal**: evaluate the latest 3.x on Inria GitLab, and contact the BOREAL team, since the founder's history with Graal makes collaboration natural. Build a Kotlin facade or service layer for multi-agent/LLM use on top of it: a tool-call API, explanation output, and DLGP generation and validation for LLM output.

Keep **Nemo** behind the same interface as a second backend, run as a subprocess or via Python/FFI, for workloads where performance matters. Fork InteGraal only if upstream collaboration fails; its Apache-2.0 licence makes that legally easy.
