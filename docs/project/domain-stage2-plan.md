# Domain stage-2 plan: filling the domain stubs

Work plan for the writer agents of stage 2. The 47 stub pages of the domain reference ([`docs/domain/`](../domain/main.md)) are split into four balanced batches that can be written in parallel. For each batch the plan gives the pages, the primary sources to read, the cross-links to respect and the consistency risks. It also fixes the writing and review process ([below](#writing-and-review-process)). This plan is a project document. The pages it produces are project-agnostic and must not mention it.

> **Status in this project:** `v0` `F2` `later` — work plan; governed by the [domain conventions](../domain/conventions.md) and the [dependency rule](README.md#dependency-rule).
> **Page maturity:** reviewed-by-architect · 2026-10-01

<a id="writing-and-review-process"></a>
## Writing and review process

1. **Isolation of domain writers.** Domain writers do **not** read `docs/project/` or `docs/preliminary-analysis/`. They work only from the primary literature and from `docs/domain/` (in particular [notation](../domain/notation.md), [conventions](../domain/conventions.md) and [glossary](../domain/glossary.md)). This prevents project vocabulary, priorities and decisions from leaking into the domain reference. The architect hands each writer its batch section of this plan as its brief (pages, primary references, cross-links, consistency risks); the brief contains no project content, and the writer reads nothing else from the project.
2. **Adversarial review of every batch.** Each written batch is reviewed by a separate agent acting as an adversarial reviewer (findings graded blocker / major / minor / nit, each with a suggested fix). The review is saved under [`reviews/`](reviews/) (`YYYY-MM-DD-<target>-adversarial-review.md`, header with date, target, reviewer and status), and its blockers and majors are fixed before the batch counts as done. Minors and nits are fixed or listed as open in the saved review.
3. **Final cross-page consistency review**, after all batches are written and reviewed ([After stage 2](#after-stage-2)).
4. **Owner's expert review** comes after the agents' reviews, on pages already corrected, so that the owner's time goes to substance rather than to defects an agent can find.
5. **Revision of `main.md`** at the end of stage 2: the entry page is revised again once the pages are written (status markers, overview paragraphs aligned with the written pages, reading paths), and reviewed like a batch.

First application: [adversarial review of `main.md`](reviews/2026-10-01-domain-main-adversarial-review.md) (2026-10-01).

## Rules common to all batches

1. **Read first** (domain only, see [isolation](#writing-and-review-process)):
   - [domain main](../domain/main.md), [conventions](../domain/conventions.md), [notation](../domain/notation.md), [glossary](../domain/glossary.md), [examples](../domain/examples.md);
   - [foundations](../domain/concepts/foundations.md), the model page;
   - the primary references listed for the batch, and those cited by the stubs.
2. **Template.** Fill the [page template](../domain/conventions.md#2-page-template) (system-page, evaluation or adjacent variant where relevant). Replace the `## TODO (coverage)` section by the template sections. Every item of the TODO list must be covered, or explicitly moved to *Disputes and open issues* with a reason.
3. **Notation is mandatory.** Use the symbols of [notation.md](../domain/notation.md): `σ(t)` prefix application, `K = (D, Σ)`, `cert(q, K)`, `not` for default negation and `¬` for classical negation, `n1` for labelled nulls, `chase_r` and friends for variants. If a needed symbol is missing, do not invent one locally. Report it, and the architect adds it to `notation.md`.
4. **Terminology.** A "labelled null" is a term in an instance. An "existential variable" is a variable of a rule head. Translate the notation of the sources and explain the differences in *Variants and terminology across communities*.
5. **Project-agnostic.** No project identifiers (D*, E*, Q*, OP-*, F1-F3, v0), no "this project", no project status, no links to `docs/project/` or `docs/preliminary-analysis/`. Before committing, run:
   `grep -rnE "\b(D[0-9]+|E[0-9]+|OP-[0-9]+|Q[0-9]+|F[123])\b|preliminary-analysis|docs/project|this project|our engine|owner|lookup-before-invent|named function" docs/domain`
   It must return nothing meaningful.
6. **Sources.** Cite primary literature and official documentation in *References*. The references listed per batch below and those cited by the stubs are the starting point; find further primary sources yourself. Tag `[U]` every bibliographic detail, complexity bound or system claim not checked against the primary source. If a domain page tags a claim `[U]`, keep the tag unless you checked the source.
7. **Examples.** Illustrate with the shared [examples](../domain/examples.md) before inventing new ones. A new example that several pages would use is proposed for `examples.md` in your final report.
8. **Disputes.** If two sources disagree, or a domain page contradicts the literature, present both and open an entry in the [disputes log](../domain/disputes.md) with a `[D]` tag ([conventions §8](../domain/conventions.md#8-contribution-and-challenge-process)).
9. **Files.** Edit only the pages of your batch, plus new entries appended to `disputes.md`. Do not edit `main.md`, `conventions.md`, `notation.md`, `glossary.md`, `examples.md` or `foundations.md`: list the rows and changes you propose in your final report (including the *written* status marker for the page index of `main.md`), and the architect merges them, to avoid conflicts.
10. **Size.** Keep pages dense: typically 80-250 lines. Link instead of repeating.
11. **Commits.** One commit per batch; `git pull --rebase` before pushing.

## Single-place definitions (to avoid divergent copies)

| Notion | Defined only in | Others link to it |
|---|---|---|
| terms, homomorphism, entailment, certain answers, OWA/CWA, UNA | [foundations](../domain/concepts/foundations.md) | all |
| chase variants, triggers, active triggers | [chase variants](../domain/algorithms/chase-variants.md) | chase termination, existential rules, systems |
| FES/FUS/BTS/GBTS and all recognisable classes | [decidability classes](../domain/concepts/decidability-classes.md) | chase termination, rule-set analysis tools, systems |
| stable and well-founded semantics | [logic programming and ASP](../domain/concepts/logic-programming-and-asp.md) | stratified negation, perfect-model semantics |
| guarantees of interrupted or budgeted computations | [soundness and completeness of partial results](../domain/concepts/soundness-and-completeness-of-partial-results.md) | stratified negation, aggregation, chase termination |
| GRD and refined dependencies | [GRD](../domain/algorithms/graph-of-rule-dependencies-grd.md) | SCC-driven chase, analysis tools, hybrid strategies |
| rounding modes | [exact decimals and rounding](../domain/concepts/exact-decimals-and-rounding.md) | aggregation, examples |

## Batch A: semantics and languages (12 pages)

| Page | Primary sources to cite (non-exhaustive) |
|---|---|
| [concepts/datalog](../domain/concepts/datalog.md) | Abiteboul-Hull-Vianu 1995; Ceri-Gottlob-Tanca 1989; Dantsin et al. 2001; van Emden-Kowalski 1976 |
| [concepts/existential-rules](../domain/concepts/existential-rules.md) | Baget et al. AIJ 2011; Calì-Gottlob-Lukasiewicz JWS 2012; Fagin et al. TCS 2005; Mugnier-Thomazo 2014 |
| [concepts/logic-programming-and-asp](../domain/concepts/logic-programming-and-asp.md) | Lloyd 1987; Gelfond-Lifschitz 1988, 1991; Van Gelder-Ross-Schlipf 1991; ASP-Core-2; Gebser et al. 2012 |
| [concepts/conjunctive-queries-and-ucq](../domain/concepts/conjunctive-queries-and-ucq.md) | Chandra-Merlin 1977; Sagiv-Yannakakis 1980; Yannakakis 1981; Gottlob-Leone-Scarcello 2002 |
| [concepts/perfect-model-semantics](../domain/concepts/perfect-model-semantics.md) | Apt-Blair-Walker 1988; Przymusinski 1988 |
| [concepts/stratified-negation](../domain/concepts/stratified-negation.md) | Apt-Blair-Walker 1988; Chandra-Harel 1985; Apt-Bol 1994; Magka-Krötzsch-Horrocks 2013 |
| [concepts/aggregation](../domain/concepts/aggregation.md) | Ross-Sagiv 1997; Kaminski et al. 2017; Faber-Pfeifer-Leone 2011; ASP-Core-2 |
| [concepts/exact-decimals-and-rounding](../domain/concepts/exact-decimals-and-rounding.md) | IEEE 754-2019; XSD 1.1 Part 2; Java `RoundingMode`; Python `decimal` |
| [concepts/equality-and-una](../domain/concepts/equality-and-una.md) | Mitchell 1983; Chandra-Vardi 1985; Calì-Gottlob-Pieris AIJ 2012; Bellomarini et al. PVLDB 2022; Motik et al. AAAI 2015; egg (POPL 2021) |
| [concepts/labelled-nulls](../domain/concepts/labelled-nulls.md) | Imieliński-Lipski 1984; Codd 1979; Fagin et al. 2005; Hogan 2015 |
| [concepts/equivalence-notions](../domain/concepts/equivalence-notions.md) | Sagiv 1988; Shmueli 1993; Lifschitz-Pearce-Valverde 2001; Eiter-Fink 2003 |
| [adjacent/description-logics-and-owl](../domain/adjacent/description-logics-and-owl.md) | Baader et al. 2017; OWL 2 Profiles; Calvanese et al. JAR 2007; Grosof et al. 2003 |

- **Cross-links:** foundations (notation), logic programming and ASP (stable and well-founded semantics, single place), soundness and completeness of partial results (batch B) for anything about incomplete strata, decidability classes (batch B) for complexity tables.
- **Consistency risks:**
  - `not` vs `¬`: many stratified-Datalog papers write `¬` for default negation; translate and say so.
  - Rounding: the half-up convention differs (towards `+∞` vs away from zero) between standards and libraries; give both, with sources. Do not pick one as "the" half-up.
  - Empty groups in aggregation differ between ASP and SQL; present both.
  - Do not present perfect-model answers and certain answers as the same thing; state the conditions under which they coincide.

## Batch B: value invention, decidability and guarantees (12 pages)

| Page | Primary sources to cite (non-exhaustive) |
|---|---|
| [concepts/skolem-functions-and-terms](../domain/concepts/skolem-functions-and-terms.md) | Marnette 2009; Fagin-Kolaitis-Popa-Tan 2005; Calimeri et al. ICLP 2008; Vadalog handbook; RDFox docs; LogicBlox docs |
| [concepts/skolemisation-and-function-graph-translations](../domain/concepts/skolemisation-and-function-graph-translations.md) | Fagin et al. 2005 (SO tgds); Arenas et al. JCSS 2013; Marx-Krötzsch ICDT 2022; Calì-Gottlob-Pieris AIJ 2012 |
| [concepts/value-invention-strategies](../domain/concepts/value-invention-strategies.md) | LogicBlox constructors (SIGMOD 2015, reference manual); Jena inference docs (`makeSkolem`, `makeInstance`, `makeTemp`); RDFox `SKOLEM`; Vadalog; Carral et al. IJCAI 2017 |
| [concepts/decidability-classes](../domain/concepts/decidability-classes.md) | Baget et al. AIJ 2011; Cuenca Grau et al. JAIR 2013; Krötzsch-Rudolph IJCAI 2011; Calì-Gottlob-Kifer JAIR 2013; Calì-Gottlob-Pieris AIJ 2012; Arenas-Gottlob-Pieris 2014; Leone et al. TOCL 2019; Syrjänen 2001; Alviano-Faber-Leone 2010; Eiter-Šimkus 2010; Lierler-Lifschitz 2009 |
| [concepts/chase-termination](../domain/concepts/chase-termination.md) | Deutsch-Nash-Remmel 2008; Marnette 2009; Gogacz-Marcinkowski 2014; Grahne-Onet 2018; Carral et al. PODS 2025; Carral-Dragoste-Krötzsch 2017; Leclère et al. ICDT 2019; Carral et al. KR 2022; Calautti-Gottlob-Pieris 2015 |
| [concepts/soundness-and-completeness-of-partial-results](../domain/concepts/soundness-and-completeness-of-partial-results.md) | Beeri-Vardi 1984 (chase as proof procedure); Deutsch et al. 2008; Apt-Blair-Walker 1988; approximate/anytime reasoning literature |
| [concepts/provenance](../domain/concepts/provenance.md) | Green-Karvounarakis-Tannen 2007; Buneman-Khanna-Tan 2001; Zhao-Subotić-Scholz 2020; Calautti et al. (why-provenance for Datalog); Scallop |
| [concepts/explanations-and-diagnostics](../domain/concepts/explanations-and-diagnostics.md) | Kalyanpur et al. ISWC 2007; Huang et al. PVLDB 2008; Kazakov-Klinov 2014; Cuenca Grau et al. 2013 (cyclic terms as witnesses) |
| [algorithms/chase-variants](../domain/algorithms/chase-variants.md) | Onet 2013; Grahne-Onet 2018; Benedikt et al. PODS 2017; Deutsch et al. 2008; Marnette 2009; Carral et al. 2017; Leone et al. 2019; Bellomarini et al. 2018; Rocher thesis 2016 |
| [algorithms/blocking-and-finite-representations](../domain/algorithms/blocking-and-finite-representations.md) | Thomazo et al. KR 2012; Thomazo thesis 2013; DL tableau blocking (Horrocks-Sattler); Bellomarini et al. 2018; Eiter-Šimkus 2010 |
| [algorithms/rule-set-analysis-tools](../domain/algorithms/rule-set-analysis-tools.md) | Leclère-Mugnier-Rocher RR 2013 (Kiabora); González et al. ISWC 2022; Cuenca Grau et al. 2013; Calautti et al. TPLP 2015 |
| [algorithms/graph-of-rule-dependencies-grd](../domain/algorithms/graph-of-rule-dependencies-grd.md) | Baget et al. AIJ 2011; Kiabora; González et al. 2022; Krötzsch KR 2020; Baget et al. ECAI 2014 |

- **Cross-links:** decidability classes is the single place for class definitions; chase variants for variants; soundness and completeness of partial results for guarantees (other pages link there); explanations and diagnostics links to provenance for the bookkeeping.
- **Consistency risks:**
  - Complexity bounds tagged `[U]` (for instance in the table of `main.md`) keep the tag unless checked against the primary source.
  - Guarantees of partial results: describe the notions found in the literature (soundness, completeness, anytime and approximate reasoning) with their sources; do not present any status taxonomy as standard unless a source establishes it.
  - Value-invention strategies: describe LogicBlox, Jena, RDFox and Vadalog mechanisms from their documentation, with dates. Present the generic mechanisms (existential variables, rule-local Skolem functions, shared function symbols, identifier-minting built-ins, constructor predicates, conditional invention); do not coin pattern names.
  - Blocking: describe the general technique and its known completeness conditions; mark any simplified variant without a published proof as `[U]`.

## Batch C: evaluation algorithms and adjacent query topics (12 pages)

| Page | Primary sources to cite (non-exhaustive) |
|---|---|
| [algorithms/homomorphism-search](../domain/algorithms/homomorphism-search.md) | Chandra-Merlin 1977; Gottlob-Leone-Scarcello 2002; Dechter 2003; Prosser 1993; Baget (BCC-based projection) |
| [algorithms/worst-case-optimal-joins](../domain/algorithms/worst-case-optimal-joins.md) | Atserias-Grohe-Marx 2008/2013; Ngo et al. 2012/2018; Veldhuizen 2014; Wang et al. SIGMOD 2023 |
| [algorithms/semi-naive-evaluation](../domain/algorithms/semi-naive-evaluation.md) | Bancilhon 1986; Bancilhon-Ramakrishnan 1986; Abiteboul-Hull-Vianu ch. 13; Motik et al. AAAI 2014 |
| [algorithms/scc-driven-chase](../domain/algorithms/scc-driven-chase.md) | Baget et al. 2011; Kiabora 2013; Graal (RuleML 2015) |
| [algorithms/piece-unifiers](../domain/algorithms/piece-unifiers.md) | König et al. SWJ 2015, RR 2012; Baget et al. 2011 |
| [algorithms/query-rewriting](../domain/algorithms/query-rewriting.md) | König et al. 2015; Calvanese et al. 2007; Gottlob-Schwentick 2012; Benedikt et al. PVLDB 2022, TODS 2024; Kikot et al. KR 2012 |
| [algorithms/backward-chaining-and-tabling](../domain/algorithms/backward-chaining-and-tabling.md) | Tamaki-Sato 1986; Chen-Warren 1996; Bancilhon et al. 1986 (magic sets); Alviano et al. 2012; Swift-Warren 2012 (XSB); Wielemaker et al. 2012 (SWI-Prolog); Bonatti 2004 |
| [algorithms/hybrid-strategies](../domain/algorithms/hybrid-strategies.md) | Baget et al. 2011; Kiabora; Lutz-Toman-Wolter 2009; Kontchakov et al. 2010; Lutz et al. 2013 |
| [algorithms/incremental-maintenance](../domain/algorithms/incremental-maintenance.md) | Gupta-Mumick-Subrahmanian 1993; Motik et al. AIJ 2019, AAAI 2015; McSherry et al. 2013 |
| [algorithms/rule-set-transformations-and-equivalence](../domain/algorithms/rule-set-transformations-and-equivalence.md) | Sagiv 1988; Carral et al. KR 2022; Lifschitz-Pearce-Valverde 2001; egg (POPL 2021) |
| [adjacent/ontology-based-data-access](../domain/adjacent/ontology-based-data-access.md) | Poggi et al. 2008; Xiao et al. IJCAI 2018; Calvanese et al. SWJ 2017 (Ontop); R2RML; Lenzerini 2002 |
| [adjacent/rdf-and-sparql](../domain/adjacent/rdf-and-sparql.md) | RDF 1.1 Concepts and Semantics; SPARQL 1.1 and Entailment Regimes; SWRL; RIF Core; SHACL; Hogan 2015 |

- **Cross-links:** piece-unifiers and GRD (batch B) for rewriting foundations; soundness and completeness of partial results (batch B) for every guarantee; system pages (batch D) for implementations.
- **Consistency risks:**
  - Keep algorithms independent of any particular system's design choices; describe trade-offs, not recommendations.
  - Compiling rewritings for a fixed query workload, if covered, is a section of query rewriting, not a separate page.
  - Prolog and tabling are covered inside backward chaining and tabling (adjacent depth for Prolog features).

## Batch D: systems and evaluation (11 pages)

| Page | Primary sources to cite (non-exhaustive) |
|---|---|
| [systems/graal](../domain/systems/graal.md) | Baget et al. RuleML 2015; König et al. 2015; Kiabora 2013; Graal site and repository |
| [systems/integraal](../domain/systems/integraal.md) | InteGraal repository and papers; B-Runner |
| [systems/nemo](../domain/systems/nemo.md) | Ivliev et al. KR 2024; Nemo repository and docs |
| [systems/vlog-rulewerk](../domain/systems/vlog-rulewerk.md) | Urbani et al. IJCAR 2018; Carral et al. ISWC 2019 |
| [systems/rdfox](../domain/systems/rdfox.md) | Nenov et al. ISWC 2015; Motik et al. AIJ 2019, AAAI 2015; RDFox documentation |
| [systems/vadalog](../domain/systems/vadalog.md) | Bellomarini et al. PVLDB 2018; Arenas-Gottlob-Pieris 2014; Gottlob-Pieris 2015; iWarded |
| [systems/jena-rules](../domain/systems/jena-rules.md) | Jena inference documentation; Jena repository; Forgy 1982 |
| [systems/souffle](../domain/systems/souffle.md) | Jordan et al. CAV 2016; Subotić et al. PVLDB 2018; Zhao et al. TOPLAS 2020; Soufflé docs |
| [systems/clingo-and-dlv](../domain/systems/clingo-and-dlv.md) | Gebser et al. TPLP 2019; Alviano et al. LPNMR 2017; Leone et al. TOCL 2006; Leone et al. TOCL 2019 (DLV∃) |
| [systems/others](../domain/systems/others.md) | egglog (PLDI 2023); Scallop (PLDI 2023); ELK (JAR 2014); Ontop (SWJ 2017); LogicBlox (SIGMOD 2015); Llunatic; PDQ |
| [evaluation/benchmarks-and-test-oracles](../domain/evaluation/benchmarks-and-test-oracles.md) | Benedikt et al. PODS 2017 (ChaseBench); iBench; LUBM; UOBM; iWarded; Cuenca Grau et al. 2013; Carral et al. 2017 |

- **Cross-links:** each system page links to the concept and algorithm pages of the techniques it implements, and to [benchmarks and test oracles](../domain/evaluation/benchmarks-and-test-oracles.md).
- **Consistency risks:**
  - System facts change. Every version, licence and maintenance claim carries "as of <date>" and a source; licences not checked against the repository are `[U]`.
  - Describe each system from its own documentation and papers. Do not describe it through any project's evaluation or choices: "rejected as core", "used as oracle by ..." and similar phrases are forbidden.
  - Graal page: describe the system, its algorithms and documented limitations. No statements about this repository or its authors' roles in any project.
  - Oracle boundaries in the evaluation page are stated generically: which fragment and semantics two systems share, and how to compare results.

## Project stubs (not part of this plan)

The project pages [architecture principles](architecture-principles.md), [test strategy](test-strategy.md), [language choice](language-choice-kotlin-vs-rust.md) and [licensing and provenance](licensing-and-provenance.md) are also stubs. They follow the [project conventions](README.md#conventions-for-project-pages) and are scheduled with phase 3 (architecture) and phase 2 (tests) of the [roadmap](roadmap.md).

<a id="after-stage-2"></a>
## After stage 2

Once every batch is written, adversarially reviewed and fixed ([process](#writing-and-review-process)), the architect, or a reviewer agent, cross-checks all domain pages:
- merges the proposed glossary rows, notation additions and examples;
- reviews the disputes opened by writers;
- runs a final cross-page consistency review (definitions, notation, claims and tags agree across pages), saved under [`reviews/`](reviews/);
- revises [`main.md`](../domain/main.md) (status markers, overview, reading paths) and has the revision reviewed;
- reruns the project-reference grep and the relative-link check.

Then the owner reviews the reference as a domain expert. Only after that does the architect write the [domain introduction focused on the project's theoretical framework](README.md#domain-introduction).
