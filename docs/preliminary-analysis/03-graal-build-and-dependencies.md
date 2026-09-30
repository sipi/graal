# Graal: build and dependency health audit

- Repository: local clone of Graal (HEAD `3a9598f`, 2019-03-11), version `1.3.2-SNAPSHOT`
- Audit date: 2026-09-30
- Environment: OpenJDK **21.0.10** (the only JDK installed, so JDK 8/11/17 runtimes could not be tested), Apache Maven **3.9.11**, network through the agent proxy (Maven Central reachable).
- Method: every build ran in throwaway copies made with `git archive` in two throwaway copies (one unmodified, one patched). The repository itself was not modified. Build logs are not included in this repository.

---

## 1. Inventory

### 1.1 Project structure

The parent POM is `fr.lirmm.graphik:graal:1.3.2-SNAPSHOT` (packaging `pom`). It has no external parent: it does not inherit `oss-parent` or any other POM. The reactor has 32 projects:

```
graal (root)
├─ graal-util, graal-api, graal-core, graal-homomorphism, graal-grd
├─ graal-io (pom): graal-io-dlgp, graal-io-owl, graal-io-rdf, graal-io-ruleml, graal-io-sparql
├─ graal-kb, graal-forward-chaining, graal-backward-chaining, graal-rules-analyser
├─ graal-store (pom)
│   ├─ graal-store-rdbms (pom): rdbms-common, rdbms-adhoc, rdbms-natural, rdbms-test
│   ├─ graal-store-neo4j, graal-store-rdf4j, graal-store-dictionary
├─ rdf4j-common, graal-test, graal-coverage, graal-builtin-predicates
├─ graal-sparql-homomorphism
└─ graal-keyvalue (pom): graal-key-value-core (artifactId graal-keyvalue-core)
```

Size: 603 Java files, about 69k LOC in total (main and test), of which about 16k LOC are tests in 69 test files.

Cosmetic defect: `graal-sparql-homomorphism/pom.xml` has `<name>fr.lirmm.graphik:graal-rules-analyser</name>` because it was copy-pasted. As a result the reactor shows "graal-rules-analyser" twice.

### 1.2 Java level

`<jdk.version>1.8</jdk.version>` feeds `maven-compiler-plugin` 3.1 through `source`/`target`. The build does not use `--release`, so nothing checks that the code only uses the Java 8 API when compiling on a newer JDK.

### 1.3 Build plugins (root POM)

| Plugin | Declared | Latest stable (Central, 2026-09) | Notes |
|---|---|---|---|
| maven-compiler-plugin | 3.1 (2013) | 3.16.0 | 3.1 does not support `<release>`. It still works on JDK 21. |
| maven-surefire-plugin | 3.0.0-M3 | 3.6.0 | Its hard-coded `<argLine>-Xmx4G</argLine>` **overrides the JaCoCo agent**, so no coverage is collected (see below). |
| maven-javadoc-plugin | 2.9.1 (+2.10.1 in `<reporting>`) | 3.12.0 | It works on JDK 21 but logs harmless `Error fetching link ... package-list` messages (JDK 11+ produces `element-list`). |
| maven-source-plugin | 2.2.1 | 3.4.0 | OK |
| maven-release-plugin | 2.5.2 (2.1 in 2 submodules) | 3.3.1 | |
| maven-gpg-plugin | no version (release profile) | 3.2.8 | The version is unpinned. |
| maven-pmd-plugin | 3.3 | 3.28.0 | Not bound to a phase. `pmd:pmd` works on JDK 21 for Java 8 sources. |
| findbugs-maven-plugin | 3.0.0 | 3.0.5 (dead since 2017) | Not bound to a phase. **`findbugs:findbugs` fails on JDK 21** (`module java.base does not "opens java.io"`). Replace it with spotbugs-maven-plugin 4.10.x. |
| maven-site-plugin | 3.4 | 3.22.0 | |
| jacoco-maven-plugin | 0.7.7.201606060606 | 0.8.15 | 0.7.7 cannot instrument or analyse class files newer than Java 8. It currently "works" only because the agent is never attached and the classes are Java 8 bytecode. |
| maven-antrun-plugin | 1.6 (graal-coverage) | 3.2.0 | Runs the `org.jacoco.ant` 0.7.7 report. With no `.exec` files the report is empty. |
| maven-dependency-plugin | no version (graal-coverage) | – | |

The root POM has no `<pluginManagement>` or `<dependencyManagement>` section. It uses no enforcer, no Maven wrapper and no BOMs.

### 1.4 Repositories

- **No `<repositories>` or `<pluginRepositories>` are declared anywhere.** Everything resolves from Maven Central, so the build depends on no dead LIRMM or Nexus repository.
- `distributionManagement` points to `oss.sonatype.org` (OSSRH), which **Sonatype shut down on 2025-06-30**. Snapshot deploys and releases through this configuration are dead. They must move to the Central Portal (`central-publishing-maven-plugin`).
- Legacy Ant path: `prepare_ant.sh` and `scripts/wget-dep.sh` download jars listed in `scripts/dep-list` from `repo1.maven.org`. The list is stale (for example it names jackson 2.3, jsonld-java 0.5.1 and `concurrentlinkedhashmap` 1.3.1 where Maven resolves 1.4.2). It is not part of the Maven build and is a candidate for deletion.

### 1.5 Internal and "unpublished" dependencies

- `fr.lirmm.graphik:dlgp2-parser:2.1.1` is the team's own DLGP parser. It **is published on Central** (last release 2017-05-12, and 2.1.1 is the latest). It was not rebuilt from source, so it is a frozen binary with no newer upstream.
- The team's own libraries are all reactor-internal (`graal-*`, `rdbms-*`, `rdf4j-common`). Central holds graal releases up to 1.3.1 (2018-03-15).
- No system-scoped or local-file jars are used, and nothing needs to be installed by hand.

### 1.6 Direct third-party dependencies

| Dependency | Version(s) declared | Used in | Latest stable | Upgrade nature |
|---|---|---|---|---|
| org.slf4j:slf4j-api | 1.7.7 (root, io-rdf) | all | 2.0.20 | Easy. The 2.x API is compatible, but providers use ServiceLoader, and there is no binding at all today (NOP logger). |
| junit:junit | 4.12 (root), **4.11** (graal-test overrides) | tests | 4.13.2 (JUnit 4 is final). Jupiter 6.1.3 | 4.13.2 is a drop-in. Moving to JUnit 5/6 is a rewrite because `@Theories` has no direct equivalent (use `@ParameterizedTest`/`@MethodSource`). |
| org.apache.commons:commons-lang3 | **3.3.2, 3.4, 3.5, 3.8.1** (it diverges per module) | 11 modules | 3.21.0 | Drop-in |
| org.apache.commons:commons-collections4 | 4.1, 4.2 | 6 modules | 4.6.0 | Drop-in |
| commons-io:commons-io | 2.5, 2.6 (test) | tests | 2.22.0 | Drop-in |
| commons-logging | 1.2 | store-rdf4j | 1.4.0 | Drop-in |
| org.jgrapht:jgrapht-core | 0.9.0 | util, grd, rules-analyser (3 files) | 1.5.3 | **Breaking but small.** `DirectedGraph` was removed (use `Graph`), `StrongConnectivityInspector` became `KosarajuStrongConnectivityInspector`, and `CycleDetector` moved to `alg.cycle`. 1.5 requires Java 11+. |
| fr.lirmm.graphik:dlgp2-parser | 2.1.1 | io-dlgp | 2.1.1 | None available |
| org.eclipse.rdf4j:* (13 artifacts incl. transitive) | **1.0.3** (2016, first Eclipse release, still Sesame-like API) | rdf4j-common, io-rdf, store-rdf4j, sparql-homomorphism, graal-test (17 files, 58 import lines) | 5.3.2 (Java 11) / 6.1.0 | **Breaking.** Removals across 2.x to 5.x include `ValueFactoryImpl`, Iteration/`CloseableIteration` generics, the `Repository.initialize()` to `init()` rename, SPARQL repository and MemoryStore construction, and Rio handler changes. 4.x+ requires Java 11 and 6.x likely Java 17. |
| org.apache.jena:jena-core, jena-arq | 2.13.0 (2015) | io-sparql (5 files) | 5.6.0 / 6.2.0 | **Breaking.** Packages were renamed `com.hp.hpl.jena.*` → `org.apache.jena.*` (3.0), `ElementVisitor` gained methods (for example `ElementLateral` in 4.x), and Java 17+ is required for 5.x. It is used only to parse SPARQL into the internal query model. |
| net.sourceforge.owlapi:owlapi-apibinding | 4.0.1 (2014) | io-owl (10 files, 164 import lines, including internal `uk.ac.manchester.cs.owl.owlapi` classes) | 5.5.1 | **Breaking, and the biggest API surface.** OWLAPI 5 replaces `Set` getters with `Stream`s, reworks the visitors and changes the Guice/Guava deps. OWLAPI 4.5.x is a softer intermediate step. 4.0.1 **fails at runtime on JDK 17+** (Guice 4.0-beta cglib). |
| org.neo4j:neo4j-kernel, neo4j-cypher | 2.3.11 (EOL 2017, Scala 2.11) | store-neo4j (1 file) | 5.26 LTS / 2025.x–2026.x | **Rewrite of the store.** `GraphDatabaseFactory`/`ExecutionEngine` were removed in 3.x+, the new API is `DatabaseManagementService` with `tx.execute`, Cypher syntax changed, and 5.x/2025.x require Java 17/21. 2.3.11 **fails at runtime on JDK 17+** without `--add-opens`. |
| mysql:mysql-connector-java | 5.1.6 (2008) | rdbms-common | com.mysql:mysql-connector-j 9.x / 26.7.0 | New coordinates and driver class `com.mysql.cj.jdbc.Driver`. Code does `Class.forName("com.mysql.jdbc.Driver")`. |
| postgresql:postgresql | 9.1-901-1.jdbc4 (2011) | rdbms-common | org.postgresql:postgresql 42.7.13 | New coordinates. The API is compatible. |
| org.xerial:sqlite-jdbc | 3.7.2 (2010) | rdbms-common | 3.53.4.0 | Drop-in (JDBC) |
| org.hsqldb:hsqldb | 2.3.4 | rdbms-common, rdbms-test, graal-test | 2.7.4 (Java 11+ jar, plus a `jdk8` classifier) | Mostly drop-in. Some SQL-dialect strictness changes need re-testing (the rdbms stores generate SQL). |
| org.jacoco:org.jacoco.ant | 0.7.7 | graal-coverage | 0.8.15 | Build-only |

Notable transitive dependencies (from `dependency:list`, 105 distinct GAVs in total):

- Jackson 2.3.x through `jsonld-java` 0.5.1 (via rdf4j and jena)
- Guava 17/18 and Guice 4.0-beta through OWLAPI
- trove4j 3.0.3 through OWLAPI
- httpclient 4.2.6 and httpcore 4.2.5
- xercesImpl 2.11.0
- libthrift 0.9.2 through jena-arq
- lucene-core 3.6.2 and scala-library 2.11.7 through neo4j
- mapdb 1.0.8
- asm 5.x and parboiled 1.1.7

Version convergence is poor. Four commons-lang3 versions, two junit versions, three commons-io versions and two guava versions all reach different module classpaths, because nothing is managed centrally.

---

## 2. Build as-is (JDK 21, unmodified POMs)

Command: `mvn -B test -fae` (cold `~/.m2`). **Wall time 1 min 47 s**, including downloads.

- **Dependency resolution: OK.** Every artifact and plugin resolved from Central through the proxy. No TLS or proxy problems occurred.
- **Compilation: OK.** All 32 projects compiled with `-source/-target 1.8` on javac 21.
- **Tests: BUILD FAILURE in 2 modules.**

| Module | Tests | Failures | Errors |
|---|---|---|---|
| graal-util | 4 | 0 | 0 |
| graal-core | 91 | 0 | 0 |
| graal-io-dlgp | 65 | 0 | 0 |
| graal-homomorphism | 33 | 0 | 0 |
| graal-forward-chaining | 3 | 0 | 0 |
| graal-grd | 30 | 0 | 0 |
| **graal-io-owl** | 74 | **2** | **71** |
| graal-io-sparql | 13 | 0 | 0 |
| graal-backward-chaining | 15 | 0 | 0 |
| graal-rules-analyser | 28 | 0 | 0 |
| graal-kb | 35 | 0 | 0 |
| rdbms-test | 3 | 0 | 0 |
| graal-store-dictionary | 4 | 0 | 0 |
| **graal-test** | 89 | **74** | 0 |
| graal-builtin-predicates | 3 | 0 | 0 |
| graal-sparql-homomorphism | 1 | 0 | 0 |
| graal-keyvalue-core | 1 | 0 | 0 |
| **Total** | **492** | **76** | **71** (345 pass) |

These modules have no tests: graal-api, graal-io-rdf, graal-io-ruleml, rdf4j-common, rdbms-common/adhoc/natural, store-neo4j, store-rdf4j and graal-coverage.

### Root causes (both come from JDK 16+ strong encapsulation, not from Graal code)

1. **graal-io-owl.** OWLAPI 4.0.1 builds `OWLManager` through Guice 4.0-beta, whose cglib calls `ClassLoader.defineClass` reflectively:
   `InaccessibleObjectException: Unable to make protected final java.lang.Class java.lang.ClassLoader.defineClass(...) accessible: module java.base does not "opens java.lang" to unnamed module`.
   The first test fails with `ExceptionInInitializerError` and the other 73 fail with `NoClassDefFoundError: Could not initialize class OWLManager`.

2. **graal-test.** Every JUnit `@Theories` class that uses `TestUtil.getAtomSet()` fails with the misleading message `Never found parameters that satisfied method assumptions. Violated assumptions: []`. That message comes from JUnit 4 Theories swallowing exceptions thrown from `@DataPoints` methods. I reproduced the real cause with a standalone probe on the test classpath: Neo4j 2.3.11 embedded fails in two ways.
   - `org.neo4j.helpers.Exceptions.<clinit>`: `LinkageError: Could not get Throwable message field` (reflection on `Throwable.detailMessage`, which needs `--add-opens java.base/java.lang`).
   - After that is opened: `IllegalAccessError: SingleFilePageSwapper cannot access class sun.nio.ch.FileChannelImpl` (needs `--add-exports/--add-opens java.base/sun.nio.ch`).

   Because one data point (the Neo4j store) cannot be built, the whole data-point array fails, and the RDBMS, RDF4J and in-memory stores are never exercised either.

### Minimal workaround (verified in the scratch copy)

The root POM surefire `argLine` was changed to:

```
-Xmx4G --add-opens java.base/java.lang=ALL-UNNAMED
       --add-opens java.base/sun.nio.ch=ALL-UNNAMED --add-exports java.base/sun.nio.ch=ALL-UNNAMED
       --add-opens java.base/java.nio=ALL-UNNAMED --add-opens java.base/java.io=ALL-UNNAMED
       --add-opens java.base/java.util=ALL-UNNAMED --add-opens java.base/java.lang.reflect=ALL-UNNAMED
```

(The first three flags are the ones proven necessary. The rest are defensive and can be trimmed.)

Results with this change:

- `mvn -B test`: **BUILD SUCCESS, 492 tests, 0 failures, 0 errors**, 2 min 49 s with a warm cache. graal-test now takes about 60 s because the Neo4j and HSQLDB theories actually run.
- `mvn -B verify` (javadoc jars, source jars and the graal-coverage antrun report): **BUILD SUCCESS**, 3 min 32 s.

`-DargLine=...` on the command line does **not** work as a workaround, because the POM hard-codes `<argLine>`. The POM itself has to change, or the flags go into `JDK_JAVA_OPTIONS` for the forked JVM.

### Hidden issue: coverage is silently broken

`jacoco:prepare-agent` sets the `argLine` property, but the surefire `<argLine>-Xmx4G</argLine>` replaces it. No `jacoco.exec` file is produced anywhere, and graal-coverage writes a report "with 430 classes" that is 0% covered. The fix is `<argLine>@{argLine} -Xmx4G ...</argLine>` together with JaCoCo ≥ 0.8.11 (0.7.7 would crash instrumenting on JDK 21, or when analysing Java 17+ bytecode).

---

## 3. Compiling for release 17 or 21

JDK 21 is available. `mvn -B test -Djdk.version=17` and `-Djdk.version=21` were both run on the add-opens copy:

- **Both: BUILD SUCCESS, all 492 tests green** (2 min 35 s and 2 min 39 s). The bytecode was verified as major version 65 for 21.
- A clean `test-compile` with `-Djdk.version=21 -Dmaven.compiler.showDeprecation=true` produced only these warnings:
  - 11 × `Object.finalize()` overridden, which is **deprecated for removal** (in graal-util, graal-homomorphism, graal-io-dlgp, graal-kb, rdbms and store-rdf4j). Replace with `Cleaner`/try-with-resources before finalization is disabled by default in a future JDK.
  - 5 × `Class.newInstance()`, for example `Class.forName("org.hsqldb.jdbc.JDBCDriver").newInstance()` in the JDBC drivers and in `AbstractMapper`.
  - 1 × `new Integer(...)` (deprecated for removal) and 1 × `new URL(String)`.
  - Several uses of Graal's own deprecated `Term`/`Rule` API.
- No code uses `sun.misc`, `javax.xml.bind`, `javax.annotation`, CORBA or other removed JDK modules. **The Graal sources themselves need essentially no changes for Java 21.**

What breaks on 17 and 21 is therefore:

- **Runtime reflection in two old dependencies** (OWLAPI 4.0.1/Guice 4.0-beta and Neo4j 2.3.11).
- **Build tooling:**
  - findbugs-maven-plugin fails.
  - JaCoCo 0.7.7 is incompatible, and so is the antrun report once bytecode is newer than 8.
  - compiler-plugin 3.1 cannot use `<release>`.
  - javadoc 2.9.1 logs link errors.

Maven 3.9.11 raised no model or plugin-validation warnings.

Not tested: JDK 8/11 runtimes (not installed), and Maven 4.

---

## 4. Outdated and vulnerable dependencies

The CVE lookup against OSV.dev was **blocked by the egress proxy (403)**, and the `gh` CLI is not installed. The CVE list below therefore comes from well-known advisories and has **not been tool-verified**. Run `mvn org.owasp:dependency-check-maven:check` (needs an NVD API key) or a GitHub Dependabot/OSV scan to confirm it. The "Latest" values were read live from Maven Central `maven-metadata.xml`.

| Artifact (resolved) | Scope | Known advisories (non-exhaustive) | Exposure in Graal |
|---|---|---|---|
| com.fasterxml.jackson.core:jackson-databind 2.3.3 (transitive via jsonld-java) | compile | Dozens of polymorphic-deserialization CVEs (CVE-2017-7525, CVE-2017-17485, CVE-2018-7489, CVE-2019-12384, ...), plus CVE-2020-36518 and CVE-2022-42003/42004 (DoS) | Low: default typing is not enabled. It is still flagged by every scanner. It goes away when rdf4j and jena are upgraded. |
| mysql:mysql-connector-java 5.1.6 | compile | CVE-2017-3523 (RCE via `autoDeserialize`), CVE-2017-3586/3589, CVE-2018-3258, CVE-2019-2692 | Medium when the MySQL store is used. |
| postgresql 9.1-901 | compile | CVE-2020-13692 (XXE), CVE-2022-21724 (arbitrary class instantiation via URL properties), CVE-2022-31197 (SQL injection in `refreshRow`), CVE-2024-1597 (SQLi in simple query mode) | Medium |
| org.hsqldb:hsqldb 2.3.4 | compile | CVE-2022-41853 (RCE via Java routine calls; fixed in 2.7.1) | Medium: rdbms-common exposes it on the compile classpath. |
| org.xerial:sqlite-jdbc 3.7.2 | compile | CVE-2023-32697 (RCE via JDBC URL; affects ≤ 3.41.2.1), plus many bundled-SQLite CVEs | Low to medium |
| org.apache.jena:jena-core/arq 2.13.0 | compile | CVE-2021-39239 (XXE in RDF/XML parsing), CVE-2023-22665 and CVE-2023-32200 (script execution in SPARQL functions) | Low: Graal only parses SPARQL syntax. |
| org.apache.thrift:libthrift 0.9.2 (via jena-arq) | compile | CVE-2018-1320 (SASL bypass), CVE-2019-0205 (DoS) | Low (unused) |
| org.eclipse.rdf4j 1.0.3 | compile | CVE-2018-1000644 (XXE in RDF parsers, < 2.4) | Medium when parsing untrusted RDF/XML. |
| org.apache.httpcomponents:httpclient 4.2.6 | compile | CVE-2014-3577 (hostname verification), CVE-2015-5262, CVE-2020-13956 | Low to medium (SPARQL repository over HTTP) |
| xerces:xercesImpl 2.11.0 | compile | CVE-2012-0881, CVE-2013-4002, CVE-2022-23437 (DoS) | Low |
| com.google.guava 17/18 (via OWLAPI) | compile | CVE-2018-10237, CVE-2020-8908, CVE-2023-2976 | Low |
| commons-io 2.4–2.6 | compile/test | CVE-2021-29425 (path traversal, fixed in 2.7), CVE-2024-47554 (DoS, fixed in 2.14) | Low |
| commons-lang3 3.3.2–3.8.1 | compile | CVE-2025-48924 (`ClassUtils.getClass` recursion, fixed in 3.18.0) | Low |
| junit 4.11/4.12 | test | CVE-2020-15250 (TemporaryFolder permissions) | Test-only |
| neo4j 2.3.11 (+scala 2.11.7, lucene 3.6.2) | compile | EOL since 2017, with no security fixes since. | Medium: it is an unsupported database engine. |

Clean or low-risk: slf4j-api 1.7.7 (CVE-2018-8088 affects `slf4j-ext` only), commons-collections4 (the known gadget CVE is in 3.x), trove4j, jgrapht, dlgp2-parser.

### Upgrade difficulty per dependency

| Group | Target | Breaking? | Estimate |
|---|---|---|---|
| commons-lang3 / collections4 / io / logging, slf4j 2.0, junit 4.13.2, hsqldb 2.7.x, sqlite-jdbc, postgresql 42.x, mysql-connector-j | latest | No, or trivial (driver class name, coordinates). Re-run the rdbms tests against HSQLDB 2.7. | 1–2 d |
| jgrapht 0.9 → 1.5.3 | | Yes, in 3 files | 0.5 d |
| Jena 2.13 → 5.x | | Package rename, plus the `ElementVisitor` interface grew | 1–2 d |
| rdf4j 1.0.3 → 5.x | | Yes: construction, iteration and Rio APIs, 17 files. Also fixes the Jackson and httpclient transitive CVEs. | 2–4 d |
| OWLAPI 4.0.1 → 5.1.x | | Yes: streams, visitors and use of internal impl classes, 10 files and about 1.8k LOC in io-owl. Alternative: 4.5.x as a low-risk step that already fixes the Java 17+ Guice issue. | 3–6 d (5.x) / 0.5–1 d (4.5.x) |
| Neo4j 2.3 → 5.26 LTS / 2025.x | | Full rewrite of `Neo4jStore` (removed `ExecutionEngine`/`GraphDatabaseFactory`, new transaction API, Cypher syntax). 5.x+ requires Java 17/21 and pulls in a very large dependency tree. **Alternatively, drop or deprecate the module.** | 2–4 d (or 0.5 d to remove) |
| JUnit 4 → JUnit 5/6 (optional) | | 69 test files. `@Theories` has to be rewritten as `@ParameterizedTest`. The JUnit Vintage engine allows a gradual move. | 3–5 d (optional) |

---

## 5. Effort estimates (person-days, including verification)

### (a) Build and test green on Java 21, keeping the current dependencies: **0.5–2 d**

- Add the `--add-opens/--add-exports` flags to the surefire `argLine`. This is verified to be sufficient: 0.1 d.
- Set `maven.compiler.release` to 21 (or keep 8 with `<release>8</release>`) and bump compiler-plugin to 3.13+, surefire to 3.5+, and javadoc/source/release/site/gpg to current versions: 0.25 d.
- Replace findbugs with spotbugs, move JaCoCo to 0.8.13+, set `argLine` to `@{argLine} ...` so coverage works again, and update or retire the antrun coverage module (JaCoCo's `report-aggregate` goal can replace it): 0.5 d.
- Add a `dependencyManagement` section to converge commons-lang3, junit and commons-io, plus the Maven Wrapper and the enforcer plugin: 0.25–0.5 d.
- Publishing: move from OSSRH to the Central Portal. This is only needed to release: 0.25–0.5 d.
- The upper end of the range covers CI setup and running on a non-Linux runner.

The code changes for the Java 21 bytecode target are nil. Removing `finalize()` is optional hygiene (0.5 d).

### (b) Upgrade every dependency to its latest version: **10–20 d**

This covers the table above: rdf4j, OWLAPI 5, Jena 5, Neo4j 5 or 2025, jgrapht 1.5 and the JDBC drivers, plus regression testing. The test suite is thin in some modules: io-rdf, store-rdf4j, store-neo4j and rdf4j-common have no tests of their own and are only exercised through graal-test. Budget about 20% extra for writing tests before the upgrades.

Including a JUnit 5 migration adds 3–5 d. Dropping graal-store-neo4j saves 2–4 d and removes the heaviest and most vulnerable dependency tree. Doing it in two phases works well: first OWLAPI 4.5 plus the drop-in updates (2–3 d), then the major rdf4j, Jena and OWLAPI 5 rewrites.

Note that rdf4j 5, Jena 5 and Neo4j 5+ require Java 11 or 17+, so (b) implies dropping Java 8 support.

### (c) Migrate the build to Gradle Kotlin DSL: **3–6 d. Not recommended.**

- The build is a plain multi-module Maven reactor: 32 projects, no code generation, no custom Mojos, and a single antrun coverage step. Once (a) is done it works on JDK 21 with current plugins.
- Gradle would bring faster incremental and cached builds and version catalogs. However, total build time is about 2.5 min and is dominated by graal-test (about 60 s) and javadoc, which a build tool does not change.
- Costs:
  - rewriting publishing and signing for Central;
  - re-creating the release-plugin flow;
  - re-creating JaCoCo aggregation;
  - retraining contributors used to Maven, and the ongoing cost of keeping Gradle and plugins upgraded;
  - downstream users consume the artifacts through Maven POMs anyway.
- Recommendation: stay on Maven and modernise it. Add parent `pluginManagement`/`dependencyManagement` (optionally a `graal-bom`), the Maven Wrapper, the enforcer plugin (with `dependencyConvergence`), the versions-maven-plugin or Renovate/Dependabot, and the Central Portal publishing plugin. Revisit Gradle only if a clear need appears, such as heavy code generation, multi-language modules or remote build cache requirements.

---

## Appendix: commands and logs (not included in this repository)

- `build-asis.log`: unmodified `mvn -B test -fae` (FAIL, 492 run / 76 F / 71 E, 1:47)
- `build-addopens.log`: with add-opens (SUCCESS, 492 / 0 / 0, 2:49)
- `build-j17.log`, `build-j21.log`: `-Djdk.version=17/21` (SUCCESS)
- `build-verify.log`: `mvn verify` with add-opens (SUCCESS, 3:32)
- `build-lint.log`: deprecation lint at release 21
- `fb.log` (findbugs failure) and `pmd.log` (PMD OK)
- `alldeps.txt`: 105 resolved third-party GAVs. `latest.txt`: latest versions from Central.
- `probe/Probe.java`: standalone reproduction of the Neo4j JDK 21 failure
