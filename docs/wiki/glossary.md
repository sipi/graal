# Glossary

Every term used in the project, with a one-line definition, the page that explains it, and its French equivalent for the project owner. Alphabetical. Abbreviations are listed under their short form. When you introduce a new term on any page, add it here.

> **Status in this project:** `v0` `F2` `later` `background` — terminology reference; definitions defer to the linked pages and to the [README](../preliminary-analysis/README.md).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

| Term | Definition | Page | French |
|---|---|---|---|
| Active trigger | A trigger whose rule head is not already satisfied; the restricted chase fires only these. | [chase variants](algorithms/chase-variants.md) | déclencheur actif |
| Aggregate (`#count`, `#sum`, `#min`, `#max`) | A literal computing a value from a collection of tuples of a lower stratum. | [aggregation](concepts/aggregation.md) | agrégat |
| aGRD | Acyclic graph of rule dependencies; implies FES and FUS. | [decidability classes](concepts/decidability-classes.md) | GRD acyclique |
| Analyser | The static component that classifies the rule set and selects algorithms (E2/E7). | [decidability analyser](algorithms/decidability-analyser.md) | analyseur (de décidabilité) |
| Answer mode (`all`, `constants`) | Whether answers containing functional terms are returned (report 11 OP-1, proposal). | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) | mode de réponse |
| Answer variable | A free variable of a query, whose bindings form the answers. | [foundations](concepts/foundations.md) | variable réponse |
| AR (argument-restricted) | Termination criterion for logic programs with functions bounding term depth per argument. | [decidability classes](concepts/decidability-classes.md) | à arguments restreints |
| Atom | `p(t1,...,tn)`: a predicate applied to terms. | [foundations](concepts/foundations.md) | atome |
| Backjumping | Backtracking that jumps back to the cause of a conflict rather than to the previous choice. | [homomorphism search](algorithms/homomorphism-search.md) | retour arrière intelligent |
| Backward chaining | Goal-directed reasoning from the query towards the data. | [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) | chaînage arrière |
| `bank_round` | Rounding half to even (banker's rounding), one of the D7 modes. | [exact decimals](concepts/exact-decimals-and-rounding.md) | arrondi bancaire (au pair) |
| Bi-connected component (BCC) | Maximal sub-graph that stays connected after removing any single vertex; used to decompose queries (E4). | [homomorphism search](algorithms/homomorphism-search.md) | composante biconnexe |
| Blocking | Stopping the expansion of an invented individual whose type repeats an earlier one (Q1 lead B). | [blocking of recursive chains](algorithms/blocking-of-recursive-chains.md) | blocage |
| Body | The premise (right part, after `:-`) of a rule. | [foundations](concepts/foundations.md) | corps (prémisse) |
| BTS | Bounded treewidth set: rule sets with a universal model of bounded treewidth for every database. | [decidability classes](concepts/decidability-classes.md) | ensemble à largeur arborescente bornée |
| Budget | Limits on rounds, depth, facts, digits, time, subgoals; exceeding one makes a unit incomplete. | [completeness statuses](concepts/completeness-statuses.md) | budget (limite de ressources) |
| Certain answer | A tuple of constants that is an answer in every model of the KB. | [foundations](concepts/foundations.md) | réponse certaine |
| Certificate | Checkable evidence of a property, e.g. a finite MFA chase run proving termination. | [decidability analyser](algorithms/decidability-analyser.md) | certificat |
| Chase | Forward chaining with value invention; produces a universal model. | [chase variants](algorithms/chase-variants.md) | chase (saturation) |
| Closed-world assumption (CWA) | What is not derivable is false. | [foundations](concepts/foundations.md) | hypothèse du monde clos |
| Completeness | Every correct answer is returned. | [completeness statuses](concepts/completeness-statuses.md) | complétude |
| COMPLETE-STATIC / COMPLETE-DYNAMIC | Status of a result proved complete by static analysis / by observing the fixpoint. | [completeness statuses](concepts/completeness-statuses.md) | complet (statique / dynamique) |
| Conjunctive query (CQ) | Existentially quantified conjunction of atoms with answer variables. | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) | requête conjonctive |
| Conservative extension | A rule set over a larger signature with the same consequences over the original signature. | [equivalence notions](concepts/equivalence-notions.md) | extension conservative |
| Constant | A term denoting a fixed individual or value (symbol or typed literal). | [foundations](concepts/foundations.md) | constante |
| Core | An instance with no proper endomorphism; the smallest homomorphically equivalent instance. | [foundations](concepts/foundations.md) | cœur |
| Core chase | Chase variant computing cores; terminates iff a finite universal model exists. | [chase variants](algorithms/chase-variants.md) | chase cœur |
| Critical instance | Instance with one constant and all atoms over it; termination on it implies all-instance termination for (semi-)oblivious chases. | [chase termination](concepts/chase-termination.md) | instance critique |
| D1-D8 | Owner decisions recorded in the README. | [decisions](project/decisions.md) | décisions |
| Datalog | Function-free Horn rules without existential variables; the v0 fragment. | [Datalog](concepts/datalog.md) | Datalog |
| Datalog+/- | Family of existential-rule languages (linear, guarded, sticky, warded...). | [existential rules](concepts/existential-rules.md) | Datalog+/- |
| Datalog-first chase | Restricted chase applying Datalog rules to fixpoint before any existential rule. | [chase variants](algorithms/chase-variants.md) | chase Datalog d'abord |
| Decidability | Existence of an algorithm that always terminates with the correct yes/no answer. | [decidability classes](concepts/decidability-classes.md) | décidabilité |
| Decimal (exact) | Finite decimal fraction `m·10^-s`, with exact arithmetic (no binary floats). | [exact decimals](concepts/exact-decimals-and-rounding.md) | décimal exact |
| DRed | Delete-and-rederive incremental maintenance algorithm. | [incremental maintenance](algorithms/incremental-maintenance.md) | DRed (suppression et re-dérivation) |
| E1-E12 | Requirements agreed with the owner. | [requirements](project/requirements.md) | exigences |
| E1-ex, E2-ex, E3-ex | Running business examples (default conditions, basket > 200€, line manager). | [running examples](project/running-examples.md) | exemples fil rouge |
| EDB / IDB | Extensional (stored) vs intensional (derived) predicates. | [Datalog](concepts/datalog.md) | prédicats extensionnels / intensionnels |
| EGD | Equality-generating dependency: a rule whose head equates terms. | [equality and UNA](concepts/equality-and-una.md) | dépendance génératrice d'égalités |
| Entailment (`⊨`) | `K ⊨ φ` iff every model of `K` satisfies `φ`. | [foundations](concepts/foundations.md) | conséquence logique |
| Evaluation unit | SCC (materialisation) or tabled subgoal group whose completeness is tracked. | [completeness statuses](concepts/completeness-statuses.md) | unité d'évaluation |
| Existential rule (TGD) | Rule whose head may contain existentially quantified variables. | [existential rules](concepts/existential-rules.md) | règle existentielle |
| Explanation | Finite proof tree over original rules justifying a fact or answer. | [provenance and explanations](concepts/provenance-and-explanations.md) | explication |
| Exposed unit | A unit reaching an incomplete unit through a negative or aggregate edge; its facts must not be returned (N1). | [completeness statuses](concepts/completeness-statuses.md) | unité exposée |
| F1, F2, F3 | Candidate frameworks: existential rules + guarded negation; stratified Datalog with named functions (chosen, D1); hybrid. | [decisions](project/decisions.md) | cadres F1, F2, F3 |
| Fact | A ground atom. | [foundations](concepts/foundations.md) | fait |
| Fairness | Every applicable rule application is eventually performed. | [chase termination](concepts/chase-termination.md) | équité |
| FBF, B/F | Forward/backward/forward and backward/forward incremental maintenance algorithms. | [incremental maintenance](algorithms/incremental-maintenance.md) | FBF, B/F |
| FDNC | Decidable class of logic programs with unary functions and forest-shaped rules, with infinite models. | [decidability classes](concepts/decidability-classes.md) | FDNC |
| FES | Finite expansion set: a finite universal model exists for every database. | [decidability classes](concepts/decidability-classes.md) | ensemble à expansion finie |
| `floor` | Rounding towards negative infinity, one of the D7 modes (behaviour on negatives to confirm). | [exact decimals](concepts/exact-decimals-and-rounding.md) | arrondi par défaut (partie entière) |
| Forward chaining | Applying rules to data until fixpoint (materialisation). | [semi-naive evaluation](algorithms/semi-naive-evaluation.md) | chaînage avant |
| Frontier | Variables shared by the body and the head of a rule. | [foundations](concepts/foundations.md) | frontière |
| Function-graph translation `T(P)` | Compilation of named functions into existential rules via graph predicates `F_f` (D3). | [T(P)](concepts/function-graph-translation-tp.md) | traduction par graphe de fonction |
| Functional dependency (FD) | Constraint that some positions of a relation determine others; in F2 checked, never used for equality (D2). | [equality and UNA](concepts/equality-and-una.md) | dépendance fonctionnelle |
| Functional term (named) | Ground term `f(t̄)` built with a declared named function, e.g. `manager(tom)`. | [Skolem functions](concepts/skolem-functions-and-terms.md) | terme fonctionnel (nommé) |
| FUS | Finite unification set: every CQ has a finite UCQ rewriting. | [decidability classes](concepts/decidability-classes.md) | ensemble à unification finie |
| GBTS | Greedy bounded treewidth set; its specific algorithms are out of scope (E10). | [decidability classes](concepts/decidability-classes.md) | GBTS (largeur arborescente bornée gloutonne) |
| GRD | Graph of rule dependencies: edge r1 → r2 if r1 may trigger r2. | [GRD](algorithms/graph-of-rule-dependencies-grd.md) | graphe de dépendance des règles |
| Ground | Containing no variables. | [foundations](concepts/foundations.md) | clos (sans variable) |
| Guarded rule | Rule whose body has an atom containing all body variables. | [decidability classes](concepts/decidability-classes.md) | règle gardée |
| Head | The conclusion (left part, before `:-`) of a rule. | [foundations](concepts/foundations.md) | tête (conclusion) |
| Herbrand (free-constructor) reading | Distinct ground terms denote distinct objects. | [foundations](concepts/foundations.md) | lecture de Herbrand (constructeurs libres) |
| Homomorphism | Mapping of variables (and nulls) to terms, identity on constants, sending atoms into atoms. | [foundations](concepts/foundations.md) | homomorphisme |
| Hybrid strategy | Materialise part of the KB and rewrite the query over the rest. | [hybrid strategies](algorithms/hybrid-strategies.md) | stratégie hybride |
| Incremental maintenance | Updating a materialisation after data or rule changes without recomputation. | [incremental maintenance](algorithms/incremental-maintenance.md) | maintenance incrémentale |
| Instance (database, fact base) | A set of facts. | [foundations](concepts/foundations.md) | instance (base de faits) |
| Integrity constraint | Rule with head `!` (falsity); its matches are reported violations. | [equality and UNA](concepts/equality-and-una.md) | contrainte d'intégrité |
| JA (joint acyclicity) | Acyclicity criterion ensuring Skolem chase termination, finer than WA. | [decidability classes](concepts/decidability-classes.md) | acyclicité jointe |
| Knowledge base (KB) | Facts plus rules (plus declarations and constraints in F2). | [foundations](concepts/foundations.md) | base de connaissances |
| Labelled null | Term standing for an unknown individual, created by existential rules; third term kind (E5). | [labelled nulls](concepts/labelled-nulls.md) | null étiqueté (valeur nulle étiquetée) |
| Late materialisation (D8) | Keeping Skolem terms symbolic during reasoning; output identifiers only at the end. | [Skolem functions](concepts/skolem-functions-and-terms.md) | matérialisation tardive |
| Least Herbrand model | Smallest model of a positive program; fixpoint of the immediate-consequence operator. | [Datalog](concepts/datalog.md) | plus petit modèle de Herbrand |
| Literal | An atom, a negated atom, a built-in or an aggregate in a rule body. | [stratified negation](concepts/stratified-negation.md) | littéral |
| Lookup-before-invent (D2) | Take a function value from data when recorded; invent only otherwise. | [lookup-before-invent](concepts/lookup-before-invent.md) | consulter avant d'inventer |
| `lb(K)` | Report 11's translation eliminating lookup declarations into stratified negation. | [lookup-before-invent](concepts/lookup-before-invent.md) | traduction `lb(K)` |
| Magic sets | Program transformation making bottom-up evaluation goal-directed. | [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) | ensembles magiques |
| Materialisation | Computing and storing all derivable facts. | [semi-naive evaluation](algorithms/semi-naive-evaluation.md) | matérialisation (saturation) |
| MFA / MSA | Model-faithful / model-summarising acyclicity: termination tests by a Skolem chase on the critical instance. | [decidability classes](concepts/decidability-classes.md) | acyclicité fidèle au modèle / résumant le modèle |
| Model | Interpretation satisfying all formulas of the KB. | [foundations](concepts/foundations.md) | modèle |
| Modeller diagnostic | Analyser message to the human or LLM modeller about a suspicious pattern (Q1 lead C). | [modeller diagnostics](concepts/modeller-diagnostics.md) | diagnostic pour le modélisateur |
| N1 (rule) | Report 11 normative rule: never return facts of an exposed unit. | [completeness statuses](concepts/completeness-statuses.md) | règle N1 |
| Negation as failure (`not`) | `not p` holds if `p` is not derivable (closed world). | [stratified negation](concepts/stratified-negation.md) | négation par échec |
| NOT-GUARANTEED | Status: returned answers are sound, absent answers unknown. | [completeness statuses](concepts/completeness-statuses.md) | complétude non garantie |
| Oblivious / semi-oblivious chase | Chase firing every trigger / once per frontier mapping (= Skolem chase). | [chase variants](algorithms/chase-variants.md) | chase oblivious / semi-oblivious |
| OP-1..OP-22 | Open points of report 11 (and proposed by report 12). | [open questions](project/open-questions.md) | points ouverts |
| Open-world assumption (OWA) | What is not entailed is unknown. | [foundations](concepts/foundations.md) | hypothèse du monde ouvert |
| Oracle | External system whose answers serve as expected results on a fragment. | [benchmarks and oracles](engineering/benchmarks-and-test-oracles.md) | oracle (de test) |
| Perfect model | Stratum-by-stratum least-fixpoint model of a stratified program. | [perfect-model semantics](concepts/perfect-model-semantics.md) | modèle parfait |
| Piece-unifier | Unifier between a query piece and a rule head respecting existential variables. | [piece-unifiers](algorithms/piece-unifiers.md) | unificateur par morceaux |
| Pre-registered query | Query declared in advance (`@query`), candidate for stored rewriting. | [precomputed rewriting](algorithms/precomputed-rewriting.md) | requête pré-enregistrée |
| Proof sheet | E9 standard proof format for a transformation. | [rule-set simplification](algorithms/rule-set-simplification.md) | fiche de preuve |
| Provenance | Record of how facts were derived. | [provenance and explanations](concepts/provenance-and-explanations.md) | provenance (traçabilité) |
| PURE | Graal's piece-unifier-based UCQ rewriting algorithm. | [query rewriting](algorithms/query-rewriting-pure.md) | PURE |
| Q1 | Owner's open question on infinite invention chains despite a stopping negation. | [open questions](project/open-questions.md#q1) | question ouverte Q1 |
| Query rewriting | Reformulating a query with the rules so it can be evaluated directly on the data. | [query rewriting](algorithms/query-rewriting-pure.md) | réécriture de requêtes |
| Restricted (standard) chase | Chase firing only active triggers; order-dependent. | [chase variants](algorithms/chase-variants.md) | chase restreint (standard) |
| `round` | Rounding half-up, one of the D7 modes (tie behaviour on negatives to confirm). | [exact decimals](concepts/exact-decimals-and-rounding.md) | arrondi (au plus proche, demi vers le haut) |
| Rule | `body → head`, universally quantified implication. | [foundations](concepts/foundations.md) | règle |
| Safety | Every variable of the head, of negated atoms and of built-ins is bound by a positive body atom. | [stratified negation](concepts/stratified-negation.md) | sûreté (règle saine) |
| SCC | Strongly connected component of a dependency graph. | [GRD](algorithms/graph-of-rule-dependencies-grd.md) | composante fortement connexe |
| Semi-naive evaluation | Fixpoint computation joining only new facts with old ones. | [semi-naive evaluation](algorithms/semi-naive-evaluation.md) | évaluation semi-naïve |
| Skolem chase | Chase where existential variables are replaced by Skolem terms; equivalent to semi-oblivious. | [chase variants](algorithms/chase-variants.md) | chase de Skolem |
| Skolem function (named / rule-local) | Function symbol replacing an existential variable; named ones are shared across rules (F2). | [Skolem functions](concepts/skolem-functions-and-terms.md) | fonction de Skolem (nommée / locale) |
| Soundness | Every returned answer is correct. | [completeness statuses](concepts/completeness-statuses.md) | correction (adéquation) |
| Stable model | Answer-set semantics of logic programs with negation; coincides with the perfect model on stratified programs. | [perfect-model semantics](concepts/perfect-model-semantics.md) | modèle stable |
| Sticky | Existential-rule class (FUS) defined by a variable-marking procedure. | [decidability classes](concepts/decidability-classes.md) | collant (sticky) |
| Stratification | Assignment of predicates to layers so that negation and aggregation only look at lower layers. | [stratified negation](concepts/stratified-negation.md) | stratification |
| Strict edge | Negative or aggregate edge of the dependency graph. | [stratified negation](concepts/stratified-negation.md) | arc strict |
| Substitution | Mapping from variables to terms. | [foundations](concepts/foundations.md) | substitution |
| Tabling | Memoising subgoals and their answers during backward chaining (SLG). | [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) | tabulation |
| Term | Variable, constant, functional term or labelled null. | [foundations](concepts/foundations.md) | terme |
| Trigger | A rule together with a homomorphism of its body into the instance. | [chase variants](algorithms/chase-variants.md) | déclencheur |
| UCQ | Union of conjunctive queries. | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) | union de requêtes conjonctives |
| Unifier (mgu) | Substitution making two atoms equal; most general unifier. | [foundations](concepts/foundations.md) | unificateur (le plus général) |
| Unique-name assumption (UNA) | Distinct names denote distinct individuals. | [equality and UNA](concepts/equality-and-una.md) | hypothèse des noms uniques |
| Universal model | Model that maps homomorphically into every model; answers CQs. | [foundations](concepts/foundations.md) | modèle universel |
| UNKNOWN | Status of an exposed result: nothing returned, soundness cannot be guaranteed. | [completeness statuses](concepts/completeness-statuses.md) | inconnu |
| v0 | First engine: plain positive Datalog (D6). | [roadmap](project/roadmap.md) | v0 |
| Warded | Datalog+/- class (Arenas, Gottlob, Pieris) implemented by Vadalog. | [decidability classes](concepts/decidability-classes.md) | gardé par pupille (warded) |
| WA (weak acyclicity) | No cycle through a special edge in the position graph; ensures Skolem chase termination. | [decidability classes](concepts/decidability-classes.md) | acyclicité faible |
| Worst-case-optimal join | Multiway join algorithm meeting the AGM output bound (e.g. leapfrog triejoin). | [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md) | jointure optimale dans le pire cas |
