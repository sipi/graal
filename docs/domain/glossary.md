# Glossary

Every term used in this reference, with a one-line definition, the page that explains it, and its French equivalent. Alphabetical; abbreviations are listed under their short form. Definitions here are reminders; the linked page and [notation](notation.md) are authoritative. When you introduce a new term on any page, add it here ([conventions §8](conventions.md#8-contribution-and-challenge-process)).

| Term | Definition | Page | French |
|---|---|---|---|
| Active domain (`adom`) | The set of terms occurring in an instance. | [foundations](concepts/foundations.md) | domaine actif |
| Active trigger | A trigger whose head is not already satisfied by an extension of its homomorphism; the restricted chase fires only these. | [chase variants](algorithms/chase-variants.md) | déclencheur actif |
| Aggregate (`#count`, `#sum`, `#min`, `#max`) | A body literal computing a value from a collection of tuples. | [aggregation](concepts/aggregation.md) | agrégat |
| aGRD | Acyclic graph of rule dependencies; implies FES and FUS. | [decidability classes](concepts/decidability-classes.md) | GRD acyclique |
| Answer set | Stable model of a logic program, in ASP terminology. | [logic programming and ASP](concepts/logic-programming-and-asp.md) | ensemble réponse |
| Answer set programming (ASP) | Declarative problem solving with logic programs under stable-model semantics. | [logic programming and ASP](concepts/logic-programming-and-asp.md) | programmation par ensembles réponses |
| Answer variable | A free variable of a query; its bindings form the answers. | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) | variable réponse |
| AR (argument-restricted) | Termination criterion for logic programs with functions bounding term depth per argument. | [decidability classes](concepts/decidability-classes.md) | à arguments restreints |
| Atom | `p(t1, ..., tk)`: a predicate applied to terms. | [foundations](concepts/foundations.md) | atome |
| Backjumping | Backtracking that jumps back to the cause of a conflict rather than to the previous choice. | [homomorphism search](algorithms/homomorphism-search.md) | retour arrière intelligent |
| Backward chaining | Goal-directed reasoning from the query towards the data. | [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) | chaînage arrière |
| Banker's rounding | Synonym of rounding half to even. | [exact decimals and rounding](concepts/exact-decimals-and-rounding.md) | arrondi bancaire |
| Bi-connected component (BCC) | Maximal sub-graph that stays connected after removing any single vertex; used to decompose queries. | [homomorphism search](algorithms/homomorphism-search.md) | composante biconnexe |
| Blank node | RDF term denoting an unnamed resource; the RDF counterpart of a labelled null. | [RDF and SPARQL](adjacent/rdf-and-sparql.md) | nœud anonyme (nœud blanc) |
| Blocking | Stopping the expansion of an individual whose type repeats that of an earlier one, to obtain a finite representation. | [blocking and finite representations](algorithms/blocking-and-finite-representations.md) | blocage |
| Body | The premise of a rule. | [foundations](concepts/foundations.md) | corps (prémisse) |
| BTS | Bounded treewidth set: rule sets having, for every database, a universal model of bounded treewidth. | [decidability classes](concepts/decidability-classes.md) | ensemble à largeur arborescente bornée |
| Budget | A limit on rounds, depth, facts, time or memory after which a computation is stopped. | [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) | budget (limite de ressources) |
| Certain answer | A tuple of constants that is an answer in every model of the knowledge base. | [foundations](concepts/foundations.md) | réponse certaine |
| Certificate | Checkable evidence of a property, e.g. a terminating MFA run proving chase termination. | [rule-set analysis tools](algorithms/rule-set-analysis-tools.md) | certificat |
| Chase | Forward chaining with value invention; produces a universal model (possibly infinite). | [chase variants](algorithms/chase-variants.md) | chase (saturation) |
| Classical (strong) negation (`¬`) | Negation of first-order logic: `¬p(a)` holds only if it is entailed. | [logic programming and ASP](concepts/logic-programming-and-asp.md) | négation classique (forte) |
| Closed-world assumption (CWA) | What is not derivable is false. | [foundations](concepts/foundations.md) | hypothèse du monde clos |
| Combined complexity | Complexity measured in the size of the data, the rules and the query together. | [decidability classes](concepts/decidability-classes.md) | complexité combinée |
| Completeness | Every correct answer is returned. | [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) | complétude |
| Conjunctive query (CQ) | Existentially quantified conjunction of atoms with answer variables. | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) | requête conjonctive |
| Conservative extension | A theory over a larger signature with the same consequences over the original signature. | [equivalence notions](concepts/equivalence-notions.md) | extension conservative |
| Constant | A term denoting a fixed individual or value (symbol or typed literal). | [foundations](concepts/foundations.md) | constante |
| Constructor (predicate) | A declared function-like predicate that creates or retrieves an entity for each key (LogicBlox style). | [value-invention strategies](concepts/value-invention-strategies.md) | constructeur |
| Core | An instance with no proper endomorphism; the smallest instance homomorphically equivalent to a given one. | [foundations](concepts/foundations.md) | cœur |
| Core chase | Chase variant computing cores; terminates iff a finite universal model exists. | [chase variants](algorithms/chase-variants.md) | chase cœur |
| Critical instance | Instance with one constant and all atoms over it; termination on it implies all-instance termination for (semi-)oblivious chases. | [chase termination](concepts/chase-termination.md) | instance critique |
| Data complexity | Complexity measured in the size of the data, rules and query fixed. | [decidability classes](concepts/decidability-classes.md) | complexité en données |
| Datalog | Function-free Horn rules whose head variables occur in the body. | [Datalog](concepts/datalog.md) | Datalog |
| Datalog± | Family of existential-rule languages (linear, guarded, sticky, warded, ...). | [existential rules](concepts/existential-rules.md) | Datalog± |
| Datalog-first chase | Restricted chase applying Datalog rules to fixpoint before any existential rule. | [chase variants](algorithms/chase-variants.md) | chase Datalog d'abord |
| Decidability | Existence of an algorithm that always terminates with the correct yes/no answer. | [decidability classes](concepts/decidability-classes.md) | décidabilité |
| Decimal (exact) | Finite decimal fraction `m·10^-s` with exact arithmetic (no binary floats). | [exact decimals and rounding](concepts/exact-decimals-and-rounding.md) | décimal exact |
| Default negation (`not`) | `not p` holds if `p` is not derived (negation as failure). | [stratified negation](concepts/stratified-negation.md) | négation par défaut (par échec) |
| Description logic (DL) | Decidable fragment of first-order logic with concepts and roles, the basis of OWL. | [description logics and OWL](adjacent/description-logics-and-owl.md) | logique de description |
| Diagnostic | A message explaining a problem of a rule set (non-stratifiable cycle, non-termination witness, constraint violation, ...). | [explanations and diagnostics](concepts/explanations-and-diagnostics.md) | diagnostic |
| DRed | Delete-and-rederive incremental maintenance algorithm. | [incremental maintenance](algorithms/incremental-maintenance.md) | DRed (suppression et re-dérivation) |
| EDB / IDB | Extensional (stored) vs intensional (derived) predicates. | [Datalog](concepts/datalog.md) | prédicats extensionnels / intensionnels |
| EGD | Equality-generating dependency: a rule whose head equates terms. | [equality and UNA](concepts/equality-and-una.md) | dépendance génératrice d'égalités |
| Entailment (`⊨`) | `K ⊨ φ` iff every model of `K` satisfies `φ`. | [foundations](concepts/foundations.md) | conséquence logique |
| Existential rule (TGD) | Rule whose head may contain existentially quantified variables. | [existential rules](concepts/existential-rules.md) | règle existentielle |
| Existential variable | A variable quantified by `∃` in a rule head. Not to be confused with a labelled null. | [existential rules](concepts/existential-rules.md) | variable existentielle |
| Explanation | A justification of an answer, typically a proof tree over the rules and facts. | [explanations and diagnostics](concepts/explanations-and-diagnostics.md) | explication |
| Fact | A ground atom. | [foundations](concepts/foundations.md) | fait |
| Fairness | Every applicable rule application is eventually performed (or made inactive). | [chase termination](concepts/chase-termination.md) | équité |
| FBF, B/F | Forward/backward/forward and backward/forward incremental maintenance algorithms. | [incremental maintenance](algorithms/incremental-maintenance.md) | FBF, B/F |
| FDNC | Decidable class of logic programs with unary functions and forest-shaped rules, with possibly infinite models. | [decidability classes](concepts/decidability-classes.md) | FDNC |
| FES | Finite expansion set: a finite universal model exists for every database. | [decidability classes](concepts/decidability-classes.md) | ensemble à expansion finie |
| Finitely ground program | Logic program whose relevant grounding is finite, so that grounders terminate. | [logic programming and ASP](concepts/logic-programming-and-asp.md) | programme finiment instanciable |
| Forward chaining | Applying rules to data until fixpoint (materialisation). | [semi-naive evaluation](algorithms/semi-naive-evaluation.md) | chaînage avant |
| Frontier | Variables shared by the body and the head of a rule. | [foundations](concepts/foundations.md) | frontière |
| Function-graph translation | Compilation of function terms into existential rules through graph predicates `F_f(x̄, y)`. | [Skolemisation and function-graph translations](concepts/skolemisation-and-function-graph-translations.md) | traduction par graphe de fonction |
| Functional dependency (FD) | Constraint that some positions of a relation determine others. | [equality and UNA](concepts/equality-and-una.md) | dépendance fonctionnelle |
| Functional term | Term `f(t̄)` built with a function symbol, e.g. `manager(tom)`. | [Skolem functions and terms](concepts/skolem-functions-and-terms.md) | terme fonctionnel |
| FUS | Finite unification set: every CQ has a finite UCQ rewriting. | [decidability classes](concepts/decidability-classes.md) | ensemble à unification finie |
| GBTS | Greedy bounded treewidth set: BTS rule sets whose chase builds a tree decomposition greedily. | [decidability classes](concepts/decidability-classes.md) | ensemble à largeur arborescente bornée gloutonne |
| GRD | Graph of rule dependencies: edge `ρ1 → ρ2` if applying `ρ1` may trigger `ρ2`. | [GRD](algorithms/graph-of-rule-dependencies-grd.md) | graphe de dépendance des règles |
| Ground | Containing no variables. | [foundations](concepts/foundations.md) | clos (sans variable) |
| Grounding | Replacing variables of a program by terms, as done by ASP grounders. | [logic programming and ASP](concepts/logic-programming-and-asp.md) | instanciation (grounding) |
| Guarded rule | Rule whose body has an atom containing all body variables. | [decidability classes](concepts/decidability-classes.md) | règle gardée |
| Head | The conclusion of a rule. | [foundations](concepts/foundations.md) | tête (conclusion) |
| Herbrand (free-constructor) reading | Distinct ground terms denote distinct objects. | [foundations](concepts/foundations.md) | lecture de Herbrand (constructeurs libres) |
| Homomorphism | Mapping of variables (and possibly nulls) to terms, identity on constants, sending atoms into atoms. | [foundations](concepts/foundations.md) | homomorphisme |
| Hybrid strategy | Materialise part of the knowledge base and rewrite the query over the rest. | [hybrid strategies](algorithms/hybrid-strategies.md) | stratégie hybride |
| Incremental maintenance | Updating a materialisation after data or rule changes without full recomputation. | [incremental maintenance](algorithms/incremental-maintenance.md) | maintenance incrémentale |
| Instance (database, fact base) | A set of facts. | [foundations](concepts/foundations.md) | instance (base de faits) |
| Integrity (negative) constraint | Rule with head `⊥`; a match signals a violation. | [equality and UNA](concepts/equality-and-una.md) | contrainte d'intégrité (négative) |
| JA (joint acyclicity) | Acyclicity criterion ensuring Skolem chase termination, finer than WA. | [decidability classes](concepts/decidability-classes.md) | acyclicité jointe |
| Knowledge base (KB) | Facts plus rules, `K = (D, Σ)`. | [foundations](concepts/foundations.md) | base de connaissances |
| Labelled null | Term occurring in instances that stands for an unknown individual, typically created by the chase. | [labelled nulls](concepts/labelled-nulls.md) | null étiqueté (valeur nulle étiquetée) |
| Least Herbrand model | Smallest model of a positive program; least fixpoint of the immediate-consequence operator. | [Datalog](concepts/datalog.md) | plus petit modèle de Herbrand |
| Literal | An atom, a negated atom, a built-in or an aggregate in a rule body. | [stratified negation](concepts/stratified-negation.md) | littéral |
| Lookup-before-invent | Value-invention pattern: reuse a recorded value for a key, invent a new one only if none is recorded. | [value-invention strategies](concepts/value-invention-strategies.md) | consulter avant d'inventer |
| Magic sets | Program transformation making bottom-up evaluation goal-directed. | [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) | ensembles magiques |
| Materialisation | Computing and storing all derivable facts. | [semi-naive evaluation](algorithms/semi-naive-evaluation.md) | matérialisation (saturation) |
| MFA / MSA | Model-faithful / model-summarising acyclicity: termination tests by a Skolem chase on the critical instance. | [decidability classes](concepts/decidability-classes.md) | acyclicité fidèle au modèle / résumant le modèle |
| Model | Interpretation satisfying all formulas of the knowledge base. | [foundations](concepts/foundations.md) | modèle |
| OBDA | Ontology-based data access: querying data sources through an ontology and mappings. | [ontology-based data access](adjacent/ontology-based-data-access.md) | accès aux données fondé sur une ontologie |
| Oblivious / semi-oblivious chase | Chase firing every trigger / once per frontier mapping (the latter equals the Skolem chase). | [chase variants](algorithms/chase-variants.md) | chase oblivious / semi-oblivious |
| Open-world assumption (OWA) | What is not entailed is unknown. | [foundations](concepts/foundations.md) | hypothèse du monde ouvert |
| Oracle (test) | Reference system whose answers serve as expected results on a fragment. | [benchmarks and test oracles](evaluation/benchmarks-and-test-oracles.md) | oracle (de test) |
| OWL 2 | W3C ontology language based on description logics, with profiles EL, QL, RL. | [description logics and OWL](adjacent/description-logics-and-owl.md) | OWL 2 |
| Perfect model | Stratum-by-stratum least-fixpoint model of a stratified program. | [perfect-model semantics](concepts/perfect-model-semantics.md) | modèle parfait |
| Piece-unifier | Unifier between a query piece and a rule head respecting existential variables. | [piece-unifiers](algorithms/piece-unifiers.md) | unificateur par morceaux |
| Predicate dependency graph | Graph over predicates with positive and negative (or aggregate) edges, used for stratification. | [stratified negation](concepts/stratified-negation.md) | graphe de dépendance des prédicats |
| Proof tree | Tree of rule applications deriving a fact from input facts. | [provenance](concepts/provenance.md) | arbre de preuve |
| Provenance | Record of how facts were derived (which facts and rules contributed). | [provenance](concepts/provenance.md) | provenance (traçabilité) |
| PURE | Piece-unifier-based UCQ rewriting algorithm (sound, complete, minimal). | [query rewriting](algorithms/query-rewriting.md) | PURE |
| Query rewriting | Reformulating a query with the rules so it can be evaluated directly on the data. | [query rewriting](algorithms/query-rewriting.md) | réécriture de requêtes |
| RDF | W3C graph data model of subject-predicate-object triples. | [RDF and SPARQL](adjacent/rdf-and-sparql.md) | RDF |
| Restricted (standard) chase | Chase firing only active triggers; order-dependent. | [chase variants](algorithms/chase-variants.md) | chase restreint (standard) |
| Rounding mode | Rule mapping an exact value to a value at a given scale: floor, ceiling, truncation, half-up, half-even, half away from zero, ... | [exact decimals and rounding](concepts/exact-decimals-and-rounding.md) | mode d'arrondi |
| Rule | `B → H`, a universally quantified implication. | [foundations](concepts/foundations.md) | règle |
| Safety | Every variable of the head, of negated atoms and of built-ins is bound by a positive body atom. | [stratified negation](concepts/stratified-negation.md) | sûreté (règle saine) |
| SCC | Strongly connected component of a dependency graph. | [GRD](algorithms/graph-of-rule-dependencies-grd.md) | composante fortement connexe |
| Semi-naive evaluation | Fixpoint computation joining only new facts with old ones. | [semi-naive evaluation](algorithms/semi-naive-evaluation.md) | évaluation semi-naïve |
| Skolem chase | Chase where existential variables are replaced by Skolem terms; equivalent to the semi-oblivious chase. | [chase variants](algorithms/chase-variants.md) | chase de Skolem |
| Skolem function | Function symbol replacing an existential variable. | [Skolem functions and terms](concepts/skolem-functions-and-terms.md) | fonction de Skolem |
| Skolemisation | Replacing existential variables by Skolem terms. | [Skolemisation and function-graph translations](concepts/skolemisation-and-function-graph-translations.md) | skolémisation |
| SLD / SLG resolution | Prolog's resolution strategy / its tabled extension with completion. | [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) | résolution SLD / SLG |
| Soundness | Every returned answer is correct. | [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md) | correction (adéquation) |
| SPARQL | W3C query language for RDF. | [RDF and SPARQL](adjacent/rdf-and-sparql.md) | SPARQL |
| Stable model | Model of a normal program that is the least model of its reduct; coincides with the perfect model on stratified programs. | [logic programming and ASP](concepts/logic-programming-and-asp.md) | modèle stable |
| Sticky | Existential-rule class (FUS) defined by a variable-marking procedure. | [decidability classes](concepts/decidability-classes.md) | collant (sticky) |
| Stratification | Assignment of predicates to layers so that negation and aggregation only look at lower layers. | [stratified negation](concepts/stratified-negation.md) | stratification |
| Substitution | Mapping from variables to terms. | [foundations](concepts/foundations.md) | substitution |
| Tabling | Memoising subgoals and their answers during backward chaining. | [backward chaining and tabling](algorithms/backward-chaining-and-tabling.md) | tabulation |
| Term | Variable, constant, functional term or labelled null. | [foundations](concepts/foundations.md) | terme |
| Trigger | A rule together with a homomorphism of its body into the instance. | [chase variants](algorithms/chase-variants.md) | déclencheur |
| UCQ | Union of conjunctive queries. | [conjunctive queries](concepts/conjunctive-queries-and-ucq.md) | union de requêtes conjonctives |
| Unifier (mgu) | Substitution making two atoms equal; most general unifier. | [foundations](concepts/foundations.md) | unificateur (le plus général) |
| Unique-name assumption (UNA) | Distinct names denote distinct individuals. | [equality and UNA](concepts/equality-and-una.md) | hypothèse des noms uniques |
| Universal model | Model that maps homomorphically into every model; answers CQs. | [foundations](concepts/foundations.md) | modèle universel |
| Value invention | Creating new individuals during reasoning (labelled nulls, Skolem terms, constructors). | [value-invention strategies](concepts/value-invention-strategies.md) | invention de valeurs |
| WA (weak acyclicity) | No cycle through a special edge in the position graph; ensures Skolem chase termination. | [decidability classes](concepts/decidability-classes.md) | acyclicité faible |
| Warded | Datalog± class (Arenas, Gottlob, Pieris) implemented by Vadalog. | [decidability classes](concepts/decidability-classes.md) | gardé par pupille (warded) |
| Well-founded semantics | Three-valued semantics of normal programs; total and equal to the perfect model on stratified programs. | [logic programming and ASP](concepts/logic-programming-and-asp.md) | sémantique bien fondée |
| Worst-case-optimal join | Multiway join meeting the AGM output bound (e.g. leapfrog triejoin). | [worst-case-optimal joins](algorithms/worst-case-optimal-joins.md) | jointure optimale dans le pire cas |
