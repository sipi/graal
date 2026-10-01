# Logic-based knowledge representation and rule-based reasoning: a reference

This is a reference on logic-based knowledge representation and rule-based reasoning: definitions, notation, results, algorithms, systems and benchmarks, with pointers to the primary literature. It is meant to be the shared source of truth on the domain, so that readers use one vocabulary and one notation. It is not final: any page can be enriched or challenged (see [conventions](conventions.md#8-contribution-and-challenge-process) and the [disputes log](disputes.md)).

## The domain in a few paragraphs

**Knowledge representation and reasoning (KR&R).** KR&R studies how to write knowledge in a formal language that a machine can reason with, and which conclusions follow. Logic-based KR uses fragments of first-order logic. The fragments are chosen so that reasoning is decidable, or efficient, or both. This reference covers the rule-based family: knowledge is written as facts and if-then rules. See [foundations](concepts/foundations.md).

**Facts, rules, queries.** A **fact** is a ground atom (`employee(tom)`). A **rule** says that whenever its body holds, its head holds (`managerOf(y, x) → superiorOf(y, x)`). A set of facts is a **database** or **instance**. A rule set is variously called an ontology, a program, a theory or a set of dependencies, depending on the community. Together they form a **knowledge base** `K = (D, Σ)`. Users ask **queries**, typically conjunctive queries ("which `x` have a superior who is a director?"). See [Datalog](concepts/datalog.md) and [conjunctive queries](concepts/conjunctive-queries-and-ucq.md). The symbols are fixed in [notation](notation.md); pedagogical examples are in [examples](examples.md).

**Open world and closed world.** Under first-order semantics, what is not entailed is *unknown* (open-world assumption). Answers are then the **certain answers**, true in every model. Under the closed-world assumption, what is not derivable is *false*. Logic programming adopts this view through **default negation** (negation as failure) and a designated model (least, perfect, stable or well-founded). The two readings agree on positive rules and conjunctive queries. They diverge with negation, aggregation, equality and value invention. See [foundations](concepts/foundations.md#open-world-closed-world).

**Rule-language families.** Several communities developed related languages:
- **Datalog** (databases): function-free Horn rules, least-model semantics, always terminating ([Datalog](concepts/datalog.md)). Its extensions add stratified negation, aggregation and arithmetic ([stratified negation](concepts/stratified-negation.md), [perfect-model semantics](concepts/perfect-model-semantics.md), [aggregation](concepts/aggregation.md), [exact decimals and rounding](concepts/exact-decimals-and-rounding.md)).
- **Existential rules** (tuple-generating dependencies, Datalog±): heads may assert that some individual *exists*. Reasoning then creates **labelled nulls** ([existential rules](concepts/existential-rules.md), [labelled nulls](concepts/labelled-nulls.md)).
- **Logic programming and answer set programming**: rules with function symbols, default negation, disjunction and aggregates, under stable-model or well-founded semantics ([logic programming and ASP](concepts/logic-programming-and-asp.md)).
- **Description logics and OWL**: variable-free concept languages for ontologies. Their Horn fragments are closely related to existential rules ([description logics and OWL](adjacent/description-logics-and-owl.md)).
- **Semantic-web rule languages** over RDF graphs ([RDF and SPARQL](adjacent/rdf-and-sparql.md)).

**Value invention.** "Every employee has a manager" asserts the existence of an individual without naming it. There are two ways to write it. An **existential variable** creates an anonymous labelled null. A **Skolem function** creates a term such as `manager(tom)`. The choice changes identity, termination, and how negation and counting behave. See [Skolem functions and terms](concepts/skolem-functions-and-terms.md), [Skolemisation and function-graph translations](concepts/skolemisation-and-function-graph-translations.md), [value-invention strategies](concepts/value-invention-strategies.md) and [equality and UNA](concepts/equality-and-una.md).

**Reasoning tasks.** The main tasks are:
- fact entailment and query answering (certain answers or answers in a designated model);
- model computation (materialisation, answer sets);
- consistency checking (constraints, `⊥`);
- query containment and rule-set equivalence ([equivalence notions](concepts/equivalence-notions.md));
- explanation of answers ([provenance](concepts/provenance.md), [explanations and diagnostics](concepts/explanations-and-diagnostics.md));
- static analysis of rule sets (termination, class membership: [rule-set analysis tools](algorithms/rule-set-analysis-tools.md)).

**Decidability and complexity.** With value invention, query answering is undecidable in general: the chase may run forever. Research identified **abstract decidable classes** (finite expansion sets, finite unification sets, bounded treewidth sets) and recognisable **sufficient conditions** (weak and joint acyclicity, MFA, guardedness, stickiness, wardedness, ...). Membership in the abstract classes is itself undecidable. Complexity is measured as data complexity (rules fixed) and combined complexity. See [decidability classes](concepts/decidability-classes.md) and [chase termination](concepts/chase-termination.md). When termination is not guaranteed, a computation stopped early is still sound for positive consequences. It can become unsound with negation or aggregation ([soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md)).

**Algorithm families.** There are three families:
- **Forward chaining** applies rules to data until a fixpoint: [semi-naive evaluation](algorithms/semi-naive-evaluation.md), [chase variants](algorithms/chase-variants.md), [SCC-driven chase](algorithms/scc-driven-chase.md), [incremental maintenance](algorithms/incremental-maintenance.md).
- **Backward chaining** starts from the query: [query rewriting](algorithms/query-rewriting.md) with [piece-unifiers](algorithms/piece-unifiers.md), [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md), magic sets.
- **Hybrid strategies** combine both ([hybrid strategies](algorithms/hybrid-strategies.md)).

Underneath sit [homomorphism search](algorithms/homomorphism-search.md) and [join algorithms](algorithms/worst-case-optimal-joins.md). Above sit dependency analysis ([GRD](algorithms/graph-of-rule-dependencies-grd.md)), [finite representations of infinite models](algorithms/blocking-and-finite-representations.md) and [rule-set transformations](algorithms/rule-set-transformations-and-equivalence.md).

**Systems landscape.** The systems covered here are:
- existential-rule and chase engines: [Graal](systems/graal.md), [InteGraal](systems/integraal.md), [VLog and Rulewerk](systems/vlog-rulewerk.md), [Nemo](systems/nemo.md), [Vadalog](systems/vadalog.md);
- Datalog engines: [RDFox](systems/rdfox.md), [Soufflé](systems/souffle.md);
- ASP systems: [clingo and DLV](systems/clingo-and-dlv.md);
- an RDF rule engine: [Jena rules](systems/jena-rules.md);
- [others](systems/others.md) (egglog, Scallop, ascent, DDlog, ELK, Ontop, LogicBlox, data-exchange chase engines).

Benchmarks and the use of systems as test oracles are in [evaluation](evaluation/benchmarks-and-test-oracles.md).

<a id="scope"></a>
## Scope

| Circle | Topics | Treatment |
|---|---|---|
| **Core** | Datalog and extensions; existential rules and the chase; logic programming and ASP; negation; aggregation; equality; Skolemisation and value invention; decidability; algorithms; systems; benchmarks | in depth |
| **Adjacent** | description logics and OWL; RDF and SPARQL; Prolog and tabling (covered in [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md)); ontology-based data access | light: key notions and connections with the core |
| **Out for now** | probabilistic reasoning, temporal reasoning, uncertainty | not covered |

## Reading paths

| Topic | Pages, in order |
|---|---|
| **First contact** | this page → [notation](notation.md) → [foundations](concepts/foundations.md) → [examples](examples.md) → [Datalog](concepts/datalog.md) → [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) → [glossary](glossary.md) |
| **Datalog evaluation** | [Datalog](concepts/datalog.md) → [semi-naive evaluation](algorithms/semi-naive-evaluation.md) → [homomorphism search](algorithms/homomorphism-search.md) → [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md) → [GRD](algorithms/graph-of-rule-dependencies-grd.md) → [SCC-driven chase](algorithms/scc-driven-chase.md) → [incremental maintenance](algorithms/incremental-maintenance.md) → [Soufflé](systems/souffle.md), [RDFox](systems/rdfox.md) |
| **Negation and aggregation** | [foundations](concepts/foundations.md#open-world-closed-world) → [stratified negation](concepts/stratified-negation.md) → [perfect-model semantics](concepts/perfect-model-semantics.md) → [logic programming and ASP](concepts/logic-programming-and-asp.md) → [aggregation](concepts/aggregation.md) → [exact decimals and rounding](concepts/exact-decimals-and-rounding.md) → [clingo and DLV](systems/clingo-and-dlv.md) |
| **Existential rules and the chase** | [existential rules](concepts/existential-rules.md) → [labelled nulls](concepts/labelled-nulls.md) → [chase variants](algorithms/chase-variants.md) → [chase termination](concepts/chase-termination.md) → [decidability classes](concepts/decidability-classes.md) → [piece-unifiers](algorithms/piece-unifiers.md) → [query rewriting](algorithms/query-rewriting.md) → [hybrid strategies](algorithms/hybrid-strategies.md) |
| **Value invention** | [existential rules](concepts/existential-rules.md) → [Skolem functions and terms](concepts/skolem-functions-and-terms.md) → [Skolemisation and function-graph translations](concepts/skolemisation-and-function-graph-translations.md) → [value-invention strategies](concepts/value-invention-strategies.md) → [equality and UNA](concepts/equality-and-una.md) → [blocking and finite representations](algorithms/blocking-and-finite-representations.md) |
| **Guarantees and analysis** | [chase termination](concepts/chase-termination.md) → [decidability classes](concepts/decidability-classes.md) → [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) → [rule-set analysis tools](algorithms/rule-set-analysis-tools.md) → [explanations and diagnostics](concepts/explanations-and-diagnostics.md) |
| **Rule-set optimisation** | [equivalence notions](concepts/equivalence-notions.md) → [rule-set transformations and equivalence](algorithms/rule-set-transformations-and-equivalence.md) → [GRD](algorithms/graph-of-rule-dependencies-grd.md) → [provenance](concepts/provenance.md) |
| **Goal-directed reasoning** | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) → [piece-unifiers](algorithms/piece-unifiers.md) → [query rewriting](algorithms/query-rewriting.md) → [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) → [hybrid strategies](algorithms/hybrid-strategies.md) → [ontology-based data access](adjacent/ontology-based-data-access.md) |
| **Testing and benchmarking reasoners** | [benchmarks and test oracles](evaluation/benchmarks-and-test-oracles.md) → [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) → system pages |

<a id="page-index"></a>
## Page index

### Reference apparatus
- [conventions](conventions.md): purpose, page template, naming, linking, claim tags, contribution and challenge process.
- [notation](notation.md): canonical symbols and their mapping to DLGP, Datalog±, ASP and DL notations.
- [glossary](glossary.md): every term, one line each, with French equivalents.
- [disputes](disputes.md): log of challenged claims and their resolution.
- [examples](examples.md): pedagogical examples used across pages.

### Concepts
- [foundations](concepts/foundations.md): terms, atoms, substitutions, homomorphisms, models, entailment, certain answers, OWA/CWA, UNA.
- [Datalog](concepts/datalog.md): function-free Horn rules, least model, complexity.
- [existential rules](concepts/existential-rules.md): TGDs, Datalog±, certain answers, universal models.
- [logic programming and ASP](concepts/logic-programming-and-asp.md): normal and disjunctive programs, function symbols, stable models, well-founded semantics.
- [Skolem functions and terms](concepts/skolem-functions-and-terms.md): function terms as invented values; Herbrand vs first-order readings.
- [Skolemisation and function-graph translations](concepts/skolemisation-and-function-graph-translations.md): from existential variables to function terms and back.
- [labelled nulls](concepts/labelled-nulls.md): terms for unknown individuals, renaming, answers with nulls.
- [value-invention strategies](concepts/value-invention-strategies.md): Skolem terms, labelled nulls, lookup/constructor patterns in practice.
- [conjunctive queries and UCQ](concepts/conjunctive-queries-and-ucq.md): CQ, UCQ, queries with negation, containment.
- [perfect-model semantics](concepts/perfect-model-semantics.md): stratum-wise least fixpoints.
- [stratified negation](concepts/stratified-negation.md): negation as failure, strata, closed world.
- [aggregation](concepts/aggregation.md): count, sum, min, max; groups; stratified and recursive aggregation.
- [equality and UNA](concepts/equality-and-una.md): EGDs, functional dependencies, unique names, equality reasoning.
- [exact decimals and rounding](concepts/exact-decimals-and-rounding.md): numeric datatypes, exact arithmetic, rounding modes.
- [decidability classes](concepts/decidability-classes.md): FES, FUS, BTS, GBTS; acyclicity, guardedness, stickiness, wardedness; LP classes with functions.
- [chase termination](concepts/chase-termination.md): all-instance vs per-instance, critical instance, undecidability.
- [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md): what an interrupted or budgeted computation guarantees.
- [equivalence notions](concepts/equivalence-notions.md): logical, strong, uniform and query equivalence; conservative extensions.
- [provenance](concepts/provenance.md): semiring provenance, why- and how-provenance, proof trees.
- [explanations and diagnostics](concepts/explanations-and-diagnostics.md): explaining answers and non-answers; diagnosing rule sets.

### Algorithms
- [homomorphism search](algorithms/homomorphism-search.md): CQ evaluation by backtracking, decompositions, backjumping.
- [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md): generic join, leapfrog triejoin.
- [semi-naive evaluation](algorithms/semi-naive-evaluation.md): delta-driven fixpoint computation.
- [SCC-driven chase](algorithms/scc-driven-chase.md): saturating component by component.
- [chase variants](algorithms/chase-variants.md): oblivious, semi-oblivious/Skolem, restricted, Datalog-first, core, parsimonious.
- [piece-unifiers](algorithms/piece-unifiers.md): unification for existential rules.
- [graph of rule dependencies (GRD)](algorithms/graph-of-rule-dependencies-grd.md): dependency edges, SCCs, refined dependencies.
- [query rewriting](algorithms/query-rewriting.md): UCQ and Datalog rewritings; rewriting known queries in advance.
- [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md): SLD, SLG, magic sets; Prolog systems with tabling.
- [hybrid strategies](algorithms/hybrid-strategies.md): materialise below, rewrite above; combined approach.
- [incremental maintenance](algorithms/incremental-maintenance.md): DRed, FBF, B/F, counting.
- [blocking and finite representations](algorithms/blocking-and-finite-representations.md): blocking, finite representations of infinite models.
- [rule-set analysis tools](algorithms/rule-set-analysis-tools.md): recognising classes, certifying termination, selecting algorithms.
- [rule-set transformations and equivalence](algorithms/rule-set-transformations-and-equivalence.md): equivalence-preserving optimisation of rule sets.

### Systems
- [Graal](systems/graal.md), [InteGraal](systems/integraal.md), [Nemo](systems/nemo.md), [VLog and Rulewerk](systems/vlog-rulewerk.md), [RDFox](systems/rdfox.md), [Vadalog](systems/vadalog.md), [Jena rules](systems/jena-rules.md), [Soufflé](systems/souffle.md), [clingo and DLV](systems/clingo-and-dlv.md), [others](systems/others.md).

### Evaluation
- [benchmarks and test oracles](evaluation/benchmarks-and-test-oracles.md): benchmark suites, differential testing, reference systems as oracles.

### Adjacent topics
- [description logics and OWL](adjacent/description-logics-and-owl.md): DL families, OWL 2 profiles, relation to existential rules.
- [RDF and SPARQL](adjacent/rdf-and-sparql.md): graph data, blank nodes, entailment regimes, rule languages over RDF.
- [ontology-based data access](adjacent/ontology-based-data-access.md): mappings, rewriting to SQL, virtual knowledge graphs.

## References (entry points)

- S. Abiteboul, R. Hull, V. Vianu. *Foundations of Databases*. Addison-Wesley, 1995.
- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- M.-L. Mugnier, M. Thomazo. *An introduction to ontology-based query answering with existential rules*. Reasoning Web 2014, LNCS 8714.
- E. Dantsin, T. Eiter, G. Gottlob, A. Voronkov. *Complexity and expressive power of logic programming*. ACM Computing Surveys 33(3), 2001.
- G. Brewka, T. Eiter, M. Truszczyński. *Answer set programming at a glance*. CACM 54(12), 2011.
- F. Baader, I. Horrocks, C. Lutz, U. Sattler. *An Introduction to Description Logic*. Cambridge University Press, 2017.
- F. van Harmelen, V. Lifschitz, B. Porter (eds.). *Handbook of Knowledge Representation*. Elsevier, 2008.
