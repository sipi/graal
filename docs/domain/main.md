# Logic-based knowledge representation and rule-based reasoning: a reference

This is a reference on logic-based knowledge representation and rule-based reasoning: definitions, notation, results, algorithms, systems and benchmarks, with pointers to the primary literature. It is meant to become the shared source of truth on the domain, so that readers use one vocabulary and one notation. It is not final: any page can be enriched or challenged (see [conventions](conventions.md#8-contribution-and-challenge-process) and the [disputes log](disputes.md)).

<a id="status-and-authority"></a>
**Status and authority.** Most pages are currently **stubs**: a summary and a coverage checklist (`## TODO (coverage)`, [conventions §2.1](conventions.md#21-stubs)). Every entry of the [page index](#page-index) is marked *written* or *stub*. A stub's summary is orientation, not a definition. What is authoritative:
- for symbols, [notation](notation.md) is normative;
- for a notion whose page is written, that page's *Formal definitions* and *Key results* sections;
- for a notion whose page is a stub, the **primary literature** that the stub cites (and, failing that, the entry references at the end of this page), not the stub's summary and not the reader's own recollection;
- the [glossary](glossary.md) is a reminder and defers to all of the above.

Claims tagged `[U]` are unverified and claims tagged `[D]` are disputed ([conventions §7](conventions.md#7-claim-tags)). If two pages disagree, or a page disagrees with its cited source, do not pick one silently: log the disagreement in [disputes](disputes.md) ([conventions §8](conventions.md#8-contribution-and-challenge-process)). In particular, if this overview and a written page disagree, the written page is presumed right and the overview is the bug.

## The domain in a few paragraphs

**Knowledge representation and reasoning (KR&R).** KR&R studies how to write knowledge in a formal language that a machine can reason with, and which conclusions follow. Logic-based KR uses fragments of first-order logic and nonmonotonic extensions of them (default negation, stable models). The fragments are chosen so that reasoning is decidable, or efficient, or both. This reference covers the rule-based family: knowledge is written as facts and if-then rules. See [foundations](concepts/foundations.md).

**Facts, rules, queries.** A **fact** is a ground atom (`employee(tom)`). A **rule** says that whenever its body holds, its head holds (`managerOf(y, x) → superiorOf(y, x)`); rules are implicitly universally quantified over their body variables. A set of facts is an **instance**; the input instance is the **database** `D` (any instance is written `I`). A rule set is variously called an ontology, a program, a theory or a set of dependencies, depending on the community. Together they form a **knowledge base** `K = (D, Σ)`. Users ask **queries**, typically conjunctive queries such as `q(x) = ∃y superiorOf(y, x) ∧ isCompanyDirector(y)` ("which `x` have a superior who is the company director?"). See [Datalog](concepts/datalog.md) and [conjunctive queries](concepts/conjunctive-queries-and-ucq.md). The symbols are fixed in [notation](notation.md); pedagogical examples are in [examples](examples.md).

**Open world and closed world.** Under first-order semantics (databases with incomplete information, description logics, existential rules), a fact that is not entailed is *unknown* (open-world assumption). Query answers are the **certain answers**: tuples of constants true in every model. Logic programming reads a program differently:
- over **Herbrand interpretations**, which build in the unique-name assumption (distinct ground terms denote distinct objects) and domain closure (every object is denoted by a ground term);
- with **default negation** (`not`, negation as failure): `not p` holds when `p` is not derived;
- under a semantics that selects particular models: the least model (positive programs), the perfect model (stratified programs), the **well-founded model** (three-valued: an atom may be true, false or *undefined*), or the **stable models** (zero, one or many; reasoning is *cautious*, true in all stable models, or *brave*, true in some).

This is related to, but not the same as, Reiter's **closed-world assumption** (CWA, 1978), which adds the negation of every ground atom not entailed and is inconsistent with disjunctive knowledge. The world can also be closed for some predicates only (negation over a lower stratum, closed predicates in description logics). The two readings agree on function-free positive rules (Datalog) and unions of conjunctive queries; with function symbols or existential variables they still agree on answers made of constants. They diverge with default negation, aggregation, equality (where the unique-name assumption matters) and answers containing invented values. See [foundations](concepts/foundations.md#open-world-closed-world) and [equality and UNA](concepts/equality-and-una.md).

**Rule-language families.** Several communities developed related languages:
- **Datalog** (deductive databases, at the junction of logic programming and databases): function-free Horn rules whose head variables occur in the body, under least-model semantics. Bottom-up evaluation always terminates on finite data. Top-down SLD resolution (Prolog) may loop on recursive rules unless it uses tabling. Extensions add stratified negation and stratified aggregation, which preserve termination ([Datalog](concepts/datalog.md), [stratified negation](concepts/stratified-negation.md), [perfect-model semantics](concepts/perfect-model-semantics.md), [aggregation](concepts/aggregation.md)). Arithmetic that computes new values (`y = x + 1`) and recursion through aggregates can destroy termination and decidability. "Datalog" in system names often means such an extended language: check the fragment.
- **Datalog over lattices and semirings**: recursion with monotone aggregates (`#min`, `#max`) or values in a lattice or semiring (Datalog°, Flix-style lattices), relevant to [aggregation](concepts/aggregation.md) and [provenance](concepts/provenance.md).
- **Existential rules**: also called tuple-generating dependencies (TGDs) in databases, ∀∃-rules, and conceptual-graph rules in KR, where the name "existential rules" originates. Heads may assert that some individual *exists*. Datalog± is not a synonym: it names a family of decidable fragments of existential rules (linear, guarded, sticky, warded, ...), usually with equality-generating dependencies and negative constraints ([existential rules](concepts/existential-rules.md), [labelled nulls](concepts/labelled-nulls.md)).
- **Nonmonotonic and disjunctive existential rules**: existential rules with default negation under stable-model or well-founded semantics, and with disjunctive heads ([existential rules](concepts/existential-rules.md), [logic programming and ASP](concepts/logic-programming-and-asp.md)).
- **Logic programming and answer set programming**: rules with function symbols, default negation, disjunction and aggregates, under stable-model or well-founded semantics; Prolog adds an operational reading (SLDNF resolution), whose declarative counterpart is Clark's completion ([logic programming and ASP](concepts/logic-programming-and-asp.md)).
- **Description logics and OWL**: variable-free concept languages for ontologies. DL-Lite_R and EL axioms translate into linear and guarded existential rules; Horn DLs with number restrictions also need equality-generating dependencies; expressive DLs need disjunction and classical negation. Conversely, existential rules allow bodies that are not tree-shaped and predicates of any arity, which DLs lack ([description logics and OWL](adjacent/description-logics-and-owl.md)).
- **Semantic-web rule languages** over RDF graphs: SWRL, RIF, N3, OWL 2 RL written as rules, SHACL rules ([RDF and SPARQL](adjacent/rdf-and-sparql.md)).

**Datatypes and built-ins.** Rule languages also need value spaces (integers, decimals, strings, dates), comparisons and arithmetic built-ins, and functions such as rounding. They raise their own questions (typing, exactness, partiality of division) and affect termination when they compute new values. See [exact decimals and rounding](concepts/exact-decimals-and-rounding.md).

**Value invention.** "Every employee has a manager" asserts the existence of an individual without naming it. In first-order logic this is an **existential variable**: `employee(x) → ∃y managerOf(y, x)`. Semantically, the rule only asserts existence; it creates nothing. Procedures differ in how they represent the unnamed individual:
- forward-chaining procedures (the [chase](algorithms/chase-variants.md)) introduce a fresh **labelled null**; query rewriting introduces none;
- **Skolemisation** replaces `∃y` by a term `f^ρ_y(x)` over a fresh, rule-local **Skolem function** symbol. This preserves the entailment of sentences of the original signature, so certain answers over constants are unchanged.

Many languages also let users write **shared function symbols** (`managerOf(manager(x), x)`), used in several rules, in data and in queries. That is *not* Skolemisation: it identifies individuals across rules and is strictly more expressive. Other mechanisms include identifier-minting built-ins, constructor predicates and arithmetic. The choices differ on termination (which chase variant, see below), on the identity of invented individuals, on whether they may be returned as answers, and on how negation, counting and equality treat them. See [Skolem functions and terms](concepts/skolem-functions-and-terms.md), [Skolemisation and function-graph translations](concepts/skolemisation-and-function-graph-translations.md), [value-invention strategies](concepts/value-invention-strategies.md) and [equality and UNA](concepts/equality-and-una.md).

**Reasoning tasks.** The main tasks are:
- fact entailment and query answering: certain answers, or answers in a designated model, or brave and cautious answers over stable models;
- model computation (materialisation) and answer-set enumeration;
- consistency checking. The meaning of a constraint depends on the semantics: under first-order semantics a violated negative constraint `B → ⊥` makes the knowledge base inconsistent (it then entails everything); in ASP a constraint `:- B.` eliminates candidate answer sets;
- query containment, also under constraints, and rule-set equivalence ([equivalence notions](concepts/equivalence-notions.md));
- rewritability: deciding whether a query and rule set admit a first-order (UCQ) or Datalog rewriting ([query rewriting](algorithms/query-rewriting.md));
- explanation of answers and non-answers ([provenance](concepts/provenance.md), [explanations and diagnostics](concepts/explanations-and-diagnostics.md));
- static analysis of rule sets: termination, class membership ([rule-set analysis tools](algorithms/rule-set-analysis-tools.md)).

**Decidability.** With existential variables (or function symbols), conjunctive-query entailment is undecidable in general (Beeri and Vardi 1981; Chandra, Lewis and Makowsky 1981). The cause is expressive power, the ability to encode a Turing machine, not chase non-termination as such. Undecidability holds even for a fixed rule set, and even for a single rule (Baget et al. 2011) `[U]`. The problem is semi-decidable: if the answer is yes, a finite prefix of the chase proves it. Decidability does **not** require a finite chase. A rule set belongs to an abstract decidable class if it has, for every database:
- a finite universal model (**FES**, finite expansion sets; some chase variant terminates);
- a finite UCQ rewriting of every CQ (**FUS**, finite unification sets);
- a universal model of bounded treewidth (**BTS**, bounded treewidth sets; **GBTS** when the chase builds the tree decomposition greedily).

Membership in these abstract classes is undecidable. Recognisable **sufficient conditions** map to them: acyclicity notions (weak and joint acyclicity, MFA) ensure FES; linear rules, sticky rules and an acyclic graph of rule dependencies (aGRD) ensure FUS; guarded and frontier-guarded rules ensure (G)BTS, with infinite chases. Warded and shy rules are decidable classes with their own arguments `[U]`. Guarded, linear, sticky and warded rule sets all have decidable query answering although their chase may be infinite. Whether the chase terminates depends on its **variant**: termination of the oblivious chase implies termination of the semi-oblivious (Skolem) chase, which implies termination of the restricted chase on every fair sequence, which implies the existence of a finite universal model (core chase) `[U]`. See [decidability classes](concepts/decidability-classes.md), [chase termination](concepts/chase-termination.md) and [chase variants](algorithms/chase-variants.md).

**Finite and unrestricted entailment.** Certain answers are defined over all models, finite or infinite. Entailment over finite models only (finite entailment) can differ. A class where the two coincide is **finitely controllable**; guarded and sticky rules are known examples `[U]`. Do not equate "true in all finite models" with a certain answer without such a result.

**Complexity.** **Data complexity** fixes the rules *and* the query and measures the cost in the size of the database only. **Combined complexity** takes data, rules and query as input. Landmark results for (Boolean) query answering, all `[U]` until checked against Dantsin et al. 2001, Calì, Gottlob and Pieris 2012, and the decidability-classes page:

| Language | Data complexity | Combined complexity |
|---|---|---|
| Datalog | PTIME-complete | EXPTIME-complete |
| Weakly acyclic existential rules | PTIME-complete | 2EXPTIME-complete |
| Linear existential rules | in AC0 (FO-rewritable) | PSPACE-complete |
| Sticky existential rules | in AC0 (FO-rewritable) | EXPTIME-complete |
| Guarded existential rules | PTIME-complete | 2EXPTIME-complete |
| Warded existential rules | PTIME-complete | EXPTIME-complete |
| Normal logic programs, stable models (brave / cautious) | NP-complete / coNP-complete | NEXPTIME-complete / coNEXPTIME-complete |
| Disjunctive logic programs, stable models (brave / cautious) | Σ2P-complete / Π2P-complete | NEXPTIME^NP-complete / coNEXPTIME^NP-complete |

**Partial results.** When termination is not guaranteed, a computation may be stopped early. Facts derived by any finite prefix of a chase sequence are entailed, so the answers to unions of conjunctive queries made of constants that it returns are **sound** (certain), but they may be **incomplete**: completeness needs a fair chase run to its end, or a proof that the computation reached a fixpoint. An early stop can neither conclude that a fact is false nor certify that the knowledge base is **consistent**: a constraint violation may appear later. With default negation, or with non-monotone aggregates (such as `#count` or `#sum` compared against a threshold) evaluated over an incomplete lower layer, even returned answers may be wrong. Monotone aggregates over a lattice (`#min`, `#max` in recursion) give intermediate values that are sound bounds in the lattice order, not final values. See [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md).

**Algorithm families.** The main families are:
- **Forward chaining** (bottom-up) applies rules to data until a fixpoint: [semi-naive evaluation](algorithms/semi-naive-evaluation.md), [chase variants](algorithms/chase-variants.md), [SCC-driven chase](algorithms/scc-driven-chase.md), [incremental maintenance](algorithms/incremental-maintenance.md).
- **Backward chaining** (top-down) starts from the query: [query rewriting](algorithms/query-rewriting.md) with [piece-unifiers](algorithms/piece-unifiers.md); SLD and SLG resolution with tabling ([backward chaining and tabling](algorithms/backward-chaining-and-tabling.md)).
- **Goal-directed bottom-up evaluation and hybrids**: magic sets and demand transformations rewrite a program so that bottom-up evaluation only derives facts relevant to the query (covered in [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md)); [hybrid strategies](algorithms/hybrid-strategies.md) materialise part of the knowledge base and rewrite the query over the rest.
- **Ground-and-solve** for stable models: grounding (finite for finitely ground programs) followed by conflict-driven search with nogood learning, as in clingo and DLV; also lazy grounding and goal-directed ASP ([logic programming and ASP](concepts/logic-programming-and-asp.md), [clingo and DLV](systems/clingo-and-dlv.md)).
- **Model construction** in description logics (adjacent): tableaux and consequence-based reasoning ([description logics and OWL](adjacent/description-logics-and-owl.md)); related to [blocking and finite representations](algorithms/blocking-and-finite-representations.md).

Building blocks: [homomorphism search](algorithms/homomorphism-search.md) and join and matching algorithms (hash joins, [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md), RETE-style incremental matching). Rule-set level analyses and transformations: dependency analysis ([GRD](algorithms/graph-of-rule-dependencies-grd.md)), [rule-set analysis tools](algorithms/rule-set-analysis-tools.md), [rule-set transformations](algorithms/rule-set-transformations-and-equivalence.md).

**Systems landscape.** The systems covered here, alphabetically within each group (version, licence and maintenance status are given with an "as of" date on each system page):
- existential-rule and chase engines: [Graal](systems/graal.md), [InteGraal](systems/integraal.md), [Nemo](systems/nemo.md), [Vadalog](systems/vadalog.md), [VLog and Rulewerk](systems/vlog-rulewerk.md);
- Datalog engines: [RDFox](systems/rdfox.md) (also equality reasoning and Skolem-based value invention), [Soufflé](systems/souffle.md);
- ASP systems: [clingo and DLV](systems/clingo-and-dlv.md);
- an RDF rule engine: [Jena rules](systems/jena-rules.md);
- [others](systems/others.md): ascent, DDlog, egglog, ELK, LogicBlox, Ontop, Scallop (for its semiring-provenance idea), and the data-exchange chase engines ChaseFUN, Llunatic and PDQ.

Not yet covered: DLV2 and DLV∃, Alpha, s(CASP), XSB and SWI-Prolog tabling (see [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md)), rewriting engines (Clipper, Iqaros, Rapid), RDF stores with OWL RL reasoning (GraphDB, Stardog), DL reasoners (HermiT, Konclude). Benchmarks and the use of systems as test oracles are in [evaluation](evaluation/benchmarks-and-test-oracles.md).

<a id="words"></a>
## Words that mean different things in different communities

| Word | Readings to distinguish |
|---|---|
| chase | a family of procedures (oblivious, semi-oblivious/Skolem, restricted, Datalog-first, core) with different termination behaviour; always name the variant |
| null | a labelled null (a term standing for an unknown individual, comparable by identity) is not SQL `NULL` (three-valued comparisons) |
| negation | default negation `not` (negation as failure), classical negation `¬`, negation in queries, negative constraints `B → ⊥` |
| completeness | of an answer set (every correct answer returned), refutation completeness of a proof procedure, or "X-complete" in complexity theory |
| model | any first-order structure satisfying the theory, or a Herbrand model in logic programming |
| ontology | a rule set, a DL TBox, or an OWL document (TBox and ABox) |
| Datalog | pure positive Datalog, Datalog with stratified negation and aggregation, or the extended language of a "Datalog engine" |

The [glossary](glossary.md) gives one line per term.

<a id="scope"></a>
## Scope

| Circle | Topics | Treatment | Why |
|---|---|---|---|
| **Core** | Datalog and extensions; existential rules and the chase; logic programming and ASP (including tabling as an algorithm); negation; aggregation; equality; Skolemisation and value invention; datatypes and built-ins; decidability and complexity; algorithms; systems; benchmarks | in depth | the model-theoretic rule languages and their reasoning procedures |
| **Adjacent** | description logics and OWL; RDF and SPARQL; Prolog as a language (its tabled resolution is core, in [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md)); ontology-based data access | light: key notions and connections with the core | close relatives with their own literature; covered where they meet rules |
| **Out for now** | probabilistic and uncertain reasoning (probabilistic logic programming, probabilistic databases); temporal reasoning; production rules (OPS5, CLIPS, Drools); inconsistency-tolerant semantics (AR, IAR); argumentation; belief revision; constraint programming and SMT; higher-order logic | not covered | each has a large separate literature; production rules have an operational semantics (conflict resolution, retraction) rather than a model-theoretic one, although their RETE matching is mentioned as an algorithm |

## Reading paths

Most pages on these paths are stubs (see [Status and authority](#status-and-authority)): until a page is written, read the primary references that its stub cites.

| Topic | Pages, in order |
|---|---|
| **First contact** | this page → [notation](notation.md) → [foundations](concepts/foundations.md) → [examples](examples.md) → [Datalog](concepts/datalog.md) → [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) → [glossary](glossary.md) |
| **Datalog evaluation** | [Datalog](concepts/datalog.md) → [semi-naive evaluation](algorithms/semi-naive-evaluation.md) → [homomorphism search](algorithms/homomorphism-search.md) → [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md) → [GRD](algorithms/graph-of-rule-dependencies-grd.md) → [SCC-driven chase](algorithms/scc-driven-chase.md) → [incremental maintenance](algorithms/incremental-maintenance.md) → [Soufflé](systems/souffle.md), [RDFox](systems/rdfox.md) |
| **Negation and aggregation** | [foundations](concepts/foundations.md#open-world-closed-world) → [stratified negation](concepts/stratified-negation.md) → [perfect-model semantics](concepts/perfect-model-semantics.md) → [aggregation](concepts/aggregation.md) → [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) |
| **Logic programming and ASP** | [foundations](concepts/foundations.md#open-world-closed-world) → [stratified negation](concepts/stratified-negation.md) → [logic programming and ASP](concepts/logic-programming-and-asp.md) → [decidability classes](concepts/decidability-classes.md) (classes with function symbols) → [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) → [clingo and DLV](systems/clingo-and-dlv.md) |
| **Existential rules and the chase** | [existential rules](concepts/existential-rules.md) → [labelled nulls](concepts/labelled-nulls.md) → [chase variants](algorithms/chase-variants.md) → [chase termination](concepts/chase-termination.md) → [decidability classes](concepts/decidability-classes.md) → [piece-unifiers](algorithms/piece-unifiers.md) → [query rewriting](algorithms/query-rewriting.md) → [hybrid strategies](algorithms/hybrid-strategies.md) |
| **Value invention** | [existential rules](concepts/existential-rules.md) → [Skolem functions and terms](concepts/skolem-functions-and-terms.md) → [Skolemisation and function-graph translations](concepts/skolemisation-and-function-graph-translations.md) → [value-invention strategies](concepts/value-invention-strategies.md) → [equality and UNA](concepts/equality-and-una.md) → [blocking and finite representations](algorithms/blocking-and-finite-representations.md) |
| **Equality** | [foundations](concepts/foundations.md) (UNA) → [equality and UNA](concepts/equality-and-una.md) → [labelled nulls](concepts/labelled-nulls.md) → [chase variants](algorithms/chase-variants.md) → [others](systems/others.md) (egglog) |
| **Guarantees and analysis** | [chase termination](concepts/chase-termination.md) → [decidability classes](concepts/decidability-classes.md) → [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) → [rule-set analysis tools](algorithms/rule-set-analysis-tools.md) → [explanations and diagnostics](concepts/explanations-and-diagnostics.md) |
| **Rule-set optimisation** | [equivalence notions](concepts/equivalence-notions.md) → [rule-set transformations and equivalence](algorithms/rule-set-transformations-and-equivalence.md) → [GRD](algorithms/graph-of-rule-dependencies-grd.md) → [incremental maintenance](algorithms/incremental-maintenance.md) |
| **Goal-directed reasoning** | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) → [piece-unifiers](algorithms/piece-unifiers.md) → [query rewriting](algorithms/query-rewriting.md) → [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) → [hybrid strategies](algorithms/hybrid-strategies.md) → [ontology-based data access](adjacent/ontology-based-data-access.md) |
| **Description logics and RDF** | [existential rules](concepts/existential-rules.md) → [description logics and OWL](adjacent/description-logics-and-owl.md) → [RDF and SPARQL](adjacent/rdf-and-sparql.md) → [ontology-based data access](adjacent/ontology-based-data-access.md) → [query rewriting](algorithms/query-rewriting.md) |
| **Testing and benchmarking reasoners** | [benchmarks and test oracles](evaluation/benchmarks-and-test-oracles.md) → [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) → system pages |

<a id="page-index"></a>
## Page index

Status: *written* (template filled; can still be enriched or challenged) or *stub* (summary and coverage checklist only; see [Status and authority](#status-and-authority)).

### Reference apparatus
- [conventions](conventions.md) — *written*: purpose, page template, naming, linking, claim tags, contribution and challenge process.
- [notation](notation.md) — *written*: canonical symbols and their mapping to DLGP, Datalog±, ASP and DL notations (mapping partly unverified).
- [glossary](glossary.md) — *written*: every term, one line each, with French equivalents.
- [disputes](disputes.md) — *written*: log of challenged claims and their resolution.
- [examples](examples.md) — *written*: pedagogical examples used across pages.

### Concepts
- [foundations](concepts/foundations.md) — *written*: terms, atoms, substitutions, homomorphisms, models, entailment, certain answers, OWA/CWA, UNA.
- [Datalog](concepts/datalog.md) — *stub*: function-free Horn rules, least model, complexity.
- [existential rules](concepts/existential-rules.md) — *stub*: TGDs, Datalog± fragments, certain answers, universal models.
- [logic programming and ASP](concepts/logic-programming-and-asp.md) — *stub*: normal and disjunctive programs, function symbols, stable models, well-founded semantics.
- [Skolem functions and terms](concepts/skolem-functions-and-terms.md) — *stub*: function terms as invented values; Herbrand vs first-order readings.
- [Skolemisation and function-graph translations](concepts/skolemisation-and-function-graph-translations.md) — *stub*: from existential variables to function terms and back.
- [labelled nulls](concepts/labelled-nulls.md) — *stub*: terms for unknown individuals, renaming, answers with nulls.
- [value-invention strategies](concepts/value-invention-strategies.md) — *stub*: labelled nulls, Skolem terms, shared function symbols, identifier-minting built-ins, constructor predicates.
- [conjunctive queries and UCQ](concepts/conjunctive-queries-and-ucq.md) — *stub*: CQ, UCQ, queries with negation, containment.
- [stratified negation](concepts/stratified-negation.md) — *stub*: negation as failure, strata, closed world.
- [perfect-model semantics](concepts/perfect-model-semantics.md) — *stub*: stratum-wise least fixpoints.
- [aggregation](concepts/aggregation.md) — *stub*: count, sum, min, max; groups; stratified and recursive aggregation.
- [equality and UNA](concepts/equality-and-una.md) — *stub*: EGDs, functional dependencies, unique names, equality reasoning.
- [decidability classes](concepts/decidability-classes.md) — *stub*: FES, FUS, BTS, GBTS; acyclicity, linearity, guardedness, stickiness, wardedness; LP classes with functions; complexity.
- [chase termination](concepts/chase-termination.md) — *stub*: all-instance vs per-instance, critical instance, undecidability.
- [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) — *stub*: what an interrupted or budgeted computation guarantees.
- [equivalence notions](concepts/equivalence-notions.md) — *stub*: logical, strong, uniform and query equivalence; conservative extensions.
- [provenance](concepts/provenance.md) — *stub*: semiring provenance, why- and how-provenance, proof trees.
- [explanations and diagnostics](concepts/explanations-and-diagnostics.md) — *stub*: explaining answers and non-answers; diagnosing rule sets.
- Datatypes and built-ins: [exact decimals and rounding](concepts/exact-decimals-and-rounding.md) — *stub* (one section written): numeric datatypes, exact arithmetic, rounding modes.

### Algorithms
- [homomorphism search](algorithms/homomorphism-search.md) — *stub*: CQ evaluation by backtracking, decompositions, backjumping.
- [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md) — *stub*: generic join, leapfrog triejoin.
- [semi-naive evaluation](algorithms/semi-naive-evaluation.md) — *stub*: delta-driven fixpoint computation.
- [SCC-driven chase](algorithms/scc-driven-chase.md) — *stub*: saturating component by component.
- [chase variants](algorithms/chase-variants.md) — *stub*: oblivious, semi-oblivious/Skolem, restricted, Datalog-first, core, parsimonious.
- [piece-unifiers](algorithms/piece-unifiers.md) — *stub*: unification for existential rules.
- [graph of rule dependencies (GRD)](algorithms/graph-of-rule-dependencies-grd.md) — *stub*: dependency edges, SCCs, refined dependencies.
- [query rewriting](algorithms/query-rewriting.md) — *stub*: UCQ and Datalog rewritings, rewritability.
- [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) — *stub*: SLD, SLG, magic sets; Prolog systems with tabling.
- [hybrid strategies](algorithms/hybrid-strategies.md) — *stub*: materialise below, rewrite above; combined approach.
- [incremental maintenance](algorithms/incremental-maintenance.md) — *stub*: DRed, FBF, B/F, counting.
- [blocking and finite representations](algorithms/blocking-and-finite-representations.md) — *stub*: blocking, finite representations of infinite models.
- [rule-set analysis tools](algorithms/rule-set-analysis-tools.md) — *stub*: recognising classes, certifying termination, selecting algorithms.
- [rule-set transformations and equivalence](algorithms/rule-set-transformations-and-equivalence.md) — *stub*: equivalence-preserving optimisation of rule sets.

### Systems
All *stub*: [clingo and DLV](systems/clingo-and-dlv.md), [Graal](systems/graal.md), [InteGraal](systems/integraal.md), [Jena rules](systems/jena-rules.md), [Nemo](systems/nemo.md), [RDFox](systems/rdfox.md), [Soufflé](systems/souffle.md), [Vadalog](systems/vadalog.md), [VLog and Rulewerk](systems/vlog-rulewerk.md), [others](systems/others.md).

### Evaluation
- [benchmarks and test oracles](evaluation/benchmarks-and-test-oracles.md) — *stub*: benchmark suites, differential testing, reference systems as oracles.

### Adjacent topics
- [description logics and OWL](adjacent/description-logics-and-owl.md) — *stub*: DL families, OWL 2 profiles, relation to existential rules.
- [RDF and SPARQL](adjacent/rdf-and-sparql.md) — *stub*: graph data, blank nodes, entailment regimes, rule languages over RDF.
- [ontology-based data access](adjacent/ontology-based-data-access.md) — *stub*: mappings, rewriting to SQL, virtual knowledge graphs.

## References

### Entry points

- Databases and Datalog: S. Abiteboul, R. Hull, V. Vianu. *Foundations of Databases*. Addison-Wesley, 1995. S. Ceri, G. Gottlob, L. Tanca. *What you always wanted to know about Datalog (and never dared to ask)*. IEEE TKDE 1(1), 1989.
- Existential rules: J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002 . M.-L. Mugnier, M. Thomazo. *An introduction to ontology-based query answering with existential rules*. Reasoning Web 2014, LNCS 8714.
- Datalog±: A. Calì, G. Gottlob, T. Lukasiewicz. *A general Datalog-based framework for tractable query answering over ontologies*. Journal of Web Semantics 14, 2012.
- Data exchange and the chase: R. Fagin, P. G. Kolaitis, R. J. Miller, L. Popa. *Data exchange: semantics and query answering*. TCS 336(1), 2005.
- Conceptual graphs: M. Chein, M.-L. Mugnier. *Graph-based Knowledge Representation*. Springer, 2009.
- Logic programming and ASP: M. Gelfond, V. Lifschitz. *The stable model semantics for logic programming*. ICLP/SLP 1988. G. Brewka, T. Eiter, M. Truszczyński. *Answer set programming at a glance*. CACM 54(12), 2011. M. Gebser, R. Kaminski, B. Kaufmann, T. Schaub. *Answer Set Solving in Practice*. Morgan & Claypool, 2012.
- Complexity: E. Dantsin, T. Eiter, G. Gottlob, A. Voronkov. *Complexity and expressive power of logic programming*. ACM Computing Surveys 33(3), 2001.
- Description logics: F. Baader, I. Horrocks, C. Lutz, U. Sattler. *An Introduction to Description Logic*. Cambridge University Press, 2017.
- General: F. van Harmelen, V. Lifschitz, B. Porter (eds.). *Handbook of Knowledge Representation*. Elsevier, 2008.

### Results cited on this page

- C. Beeri, M. Y. Vardi. *The implication problem for data dependencies*. ICALP 1981, LNCS 115. [U]
- A. K. Chandra, H. R. Lewis, J. A. Makowsky. *Embedded implicational dependencies and their inference problem*. STOC 1981. [U]
- R. Reiter. *On closed world data bases*. In H. Gallaire, J. Minker (eds.), *Logic and Data Bases*, Plenum, 1978.
- K. L. Clark. *Negation as failure*. In H. Gallaire, J. Minker (eds.), *Logic and Data Bases*, Plenum, 1978.
- A. Van Gelder, K. A. Ross, J. S. Schlipf. *The well-founded semantics for general logic programs*. JACM 38(3), 1991.
- A. Calì, G. Gottlob, A. Pieris. *Towards more expressive ontology languages: The query answering problem*. Artificial Intelligence 193, 2012. [U]
- D. Magka, M. Krötzsch, I. Horrocks. *Computing stable models for nonmonotonic existential rules*. IJCAI 2013. [U]
- V. Bárány, G. Gottlob, M. Otto. *Querying the guarded fragment*. LICS 2010 (finite controllability of guarded rules). [U]
