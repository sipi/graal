# Foundations: first-order logic for rule-based reasoning

The minimal first-order logic needed to read every other page: terms, atoms, substitutions, homomorphisms, interpretations and models, entailment, certain answers, the open- and closed-world assumptions, and the unique-name assumption. Everything here is textbook material. Symbols follow [notation](../notation.md), which is normative.

## Intuition

Take the [chain-of-command example](../examples.md#ex-chain): the facts `managerOf(anna, tom)` and `managerOf(dir, anna)`, and the rule "a manager of X is a superior of X; a manager of a superior of X is a superior of X". Reasoning answers "who are the superiors of tom?" with `{anna, dir}`. To say precisely *why* `dir` is an answer, and why nobody else is, we need: a language (terms, atoms, rules), a notion of world in which the statements are true (models), and a notion of consequence (entailment). Queries are answered by finding the facts' **images** of the query pattern, which is a **homomorphism**: this one operation underlies query evaluation, rule application, query containment and rewriting.

## Formal definitions

### Vocabulary and terms

- A **signature** fixes disjoint sets of **predicates** `p, q, ...`, each with an arity `ar(p) ≥ 0`; **constants** `a, b, tom, 200.00, "text"`; **function symbols** `f, manager, ...` with arity `≥ 1`.
- **Variables** `x, y, z` (in program syntax: upper case `X, Y`).
- **Terms**: `t ::= x | a | n | f(t1, ..., tk)` (variable, constant, labelled null, functional term). A term is **ground** if it contains no variable.
- **Labelled nulls** `n1, n2, ...` (written `_:n1` in program-like listings) are a separate set of symbols standing for unknown individuals, produced by existential rules. They behave like constants in an instance but may be renamed or mapped to other terms by homomorphisms. See [labelled nulls](labelled-nulls.md).
- Ground terms can thus be of three kinds: constants (symbols and typed literals), ground functional terms such as `manager(tom)`, and labelled nulls. Which kinds a language admits in its input, in its rules and in its results varies (see [value-invention strategies](value-invention-strategies.md)).

### Atoms, instances, rules, queries

- An **atom** is `p(t1, ..., tn)` with `n = ar(p)`. A **fact** is a ground atom (over constants, functional terms or nulls).
- An **instance** (database, fact base) `I` or `D` is a set of facts, usually finite. The **active domain** `adom(I)` is the set of terms occurring in `I`.
- A **conjunction** of atoms is identified with the set of its atoms.
- A **rule** is `B → H` with `B` (body) and `H` (head) conjunctions, implicitly universally quantified: `∀x̄ (B → H)`. Variants: [Datalog](datalog.md) (`vars(H) ⊆ vars(B)`), [existential rules](existential-rules.md) (`∀x̄∀ȳ (B[x̄,ȳ] → ∃z̄ H[x̄,z̄])`; `x̄`, the variables shared by body and head, is the **frontier**), rules with function terms ([Skolem functions](skolem-functions-and-terms.md)), rules with negated body literals ([stratified negation](stratified-negation.md)).
- A **conjunctive query** (CQ) is `q(x̄) = ∃ȳ B[x̄, ȳ]`; `x̄` are the **answer variables**. Boolean if `x̄` is empty. See [conjunctive queries and UCQ](conjunctive-queries-and-ucq.md).

### Substitutions and homomorphisms

- A **substitution** `σ` is a finite mapping from variables to terms, extended to terms, atoms and sets by replacing each variable `x` by `σ(x)`. `σ` is **ground** if all images are ground.
- A **unifier** of atoms `a1, a2` is a substitution with `σ(a1) = σ(a2)`; a **most general unifier** (mgu) is one from which every unifier is obtained by further substitution.
- A **homomorphism** from a set of atoms `A` to a set of atoms `B` is a substitution-like mapping `h` from the variables (and, depending on context, labelled nulls) of `A` to terms of `B`, which is the **identity on constants**, such that `h(A) ⊆ B`. Notation `h : A → B`.
- Two instances are **homomorphically equivalent** if there are homomorphisms both ways. An instance is a **core** if every homomorphism from it to itself is injective; every finite instance has a unique core up to isomorphism.
- Deciding whether a homomorphism `A → B` exists is NP-complete in general (it contains graph colouring); it is polynomial when `A` has bounded treewidth / hypertree width. See [homomorphism search](../algorithms/homomorphism-search.md).

### Interpretations, models, entailment

- A first-order **interpretation** `M` has a non-empty domain `Δ`, maps each constant to an element of `Δ`, each function symbol to a total function, each predicate `p` to a relation `p^M ⊆ Δ^{ar(p)}`.
- `M` **satisfies** a ground atom, a conjunction, a rule `∀x̄ (B → ∃z̄ H)` (every assignment satisfying `B` extends to one satisfying `H`), as usual. `M` is a **model** of a set of formulas if it satisfies each of them.
- A knowledge base `K = (D, Σ)` **entails** a formula `φ`, written `D ∪ Σ ⊨ φ`, if every model of `D ∪ Σ` satisfies `φ`.
- An instance `I` can be read as an interpretation whose domain is its terms (a **Herbrand-style** interpretation); an instance `U` is a **universal model** of `D ∪ Σ` if it is a model and it maps homomorphically (fixing constants) into every model. Universal models exist for existential rules and are produced by the [chase](../algorithms/chase-variants.md) (possibly infinite).

### Certain answers

- For a CQ `q(x̄)` and a KB, the **certain answers** are the tuples of constants `ā` such that `D ∪ Σ ⊨ q(ā)`:
  `cert(q, K) = { ā ∈ 𝐂^{|x̄|} : K ⊨ q(ā) }` with `K = (D, Σ)`.
- **Key fact.** If `U` is a universal model, `ā ∈ cert(q, K)` iff there is a homomorphism `h : q → U` with `h(x̄) = ā` and `ā` made of constants. So entailment of a CQ reduces to homomorphism search in one model. Answers mapping an answer variable to a labelled null are **not** certain answers (the null's identity is unknown).
- For plain [Datalog](datalog.md), the least Herbrand model is universal, and the certain answers are exactly the query's answers on it.

### Open world, closed world

- **Open-world assumption (OWA)**: a fact not entailed is *unknown*, not false. First-order entailment and certain answers are open-world: with only `employee(tom)`, the KB entails neither `isCompanyDirector(tom)` nor its negation.
- **Closed-world assumption (CWA)**: a fact not derivable is false. Negation as failure (`not p(X)`) and aggregation (`#count`, `#sum`) are only meaningful under a closed-world reading of the predicates they range over: counting "all" items requires knowing there are no others.
- **Positive programs and CQs do not see the difference**: the least model answers coincide with certain answers. The difference appears with negation, aggregation, and equality.

### Unique-name assumption (UNA)

- **UNA**: distinct constants denote distinct individuals (`tom ≠ anna` in every model considered).
- Under **Herbrand / free-constructor** semantics (logic programming), UNA extends to function terms: distinct ground terms denote distinct objects, so `manager(tom) ≠ anna` and `manager(tom) ≠ manager(anna)`.
- Without UNA (pure FOL), `manager(tom) = anna` is consistent; equality reasoning (EGDs, functional dependencies used as rules) can then merge terms. See [equality and UNA](equality-and-una.md).

## Key results

| Result | Statement | Reference |
|---|---|---|
| Homomorphism theorem | CQ `q1` is contained in CQ `q2` iff there is a homomorphism `q2 → q1` mapping answer variables to answer variables in order (canonical database argument). | Chandra & Merlin, STOC 1977 |
| Least model of Horn programs | A set of definite (positive) rules plus facts has a least Herbrand model, the least fixpoint of the immediate-consequence operator, and it entails exactly the ground atoms true in all models. | van Emden & Kowalski, JACM 1976 |
| Universal model by the chase | For existential rules, the (possibly infinite) chase result is a universal model; CQ certain answers are its homomorphic images over constants. | Fagin, Kolaitis, Miller, Popa, TCS 2005; Deutsch, Nash, Remmel, PODS 2008 |
| Undecidability | CQ entailment under arbitrary existential rules is undecidable (but semi-decidable). | Beeri & Vardi, ICALP 1981; Chandra, Lewis, Makowsky, STOC 1981 |
| Skolemisation | Replacing `∃z` by fresh rule-local function terms preserves entailment of sentences over the original signature (conservative). | classical (Skolem 1920); see e.g. Fagin, Kolaitis, Popa, Tan, TODS 2005 for the dependency setting [U] |
| Herbrand vs FO reading | For positive programs and CQs over constants, Herbrand (free-constructor) and first-order readings agree; they diverge with negation, aggregation or equality. | classical [U] |

## Variants and terminology across communities

| Notion here | Other names | Community |
|---|---|---|
| instance, database | fact base, ABox (DL), extensional database (EDB), RDF graph | databases, KR, DL, semantic web |
| rule set | ontology, program, theory, TBox (DL), set of dependencies | KR, LP, DL, databases |
| existential rule | tuple-generating dependency (TGD), Datalog± rule | databases, KR |
| labelled null | marked null, existential witness (gloss), anonymous individual (DL), blank node (RDF) | databases, DL, semantic web |
| certain answers | cautious consequences (ASP, on a single designated model they coincide with model answers) | databases, LP |
| homomorphism | containment mapping, match, projection (conceptual graphs) | databases, KR |

- Logic programming reads programs under a **designated model** (least, perfect, stable, well-founded), with Herbrand interpretations and implicit closed-world and unique-name assumptions; first-order KR reads them under **all models** (certain answers). See [logic programming and ASP](logic-programming-and-asp.md).
- "Ground" means variable-free in LP and logic; some database texts say "constant-only", excluding nulls. State which is meant.

## Pitfalls

- **Answer variables bound to nulls are not certain answers.** Returning them as if they were individuals is unsound w.r.t. FO semantics. Functional terms under a Herbrand reading are different: they denote specific objects of the designated model and may be returned as answers.
- **Closed-world negation on an incomplete computation is unsound**, not just incomplete: `not q(a)` may succeed because `q(a)` was not derived *yet*. See [soundness and completeness of partial results](soundness-and-completeness-of-partial-results.md).
- **Homomorphisms must fix constants**, and ground functional terms built from constants under a Herbrand reading; only variables (and, for existential reasoning, nulls) may be mapped.
- **Unification is not homomorphism**: unification maps variables on both sides; homomorphism maps one side into a fixed instance.
- **Lexical form vs value of literals**: `2`, `2.0` and `2.00` have the same numeric value; whether they are the same term depends on the datatype semantics (in XSD, `xsd:decimal` compares by value). Comparing literals as strings is a classic bug. See [exact decimals and rounding](exact-decimals-and-rounding.md).
- **Mixing up UNA with equality reasoning**: a functional dependency can be read as a constraint (violations are errors) or as an EGD (it merges terms); the two readings give different results. See [equality and UNA](equality-and-una.md).
- **`not` versus `¬`**: default negation and classical negation are different operators ([notation §4](../notation.md#4-rules)).

## Disputes and open issues

None known.

## Related pages

- [Datalog](datalog.md), [existential rules](existential-rules.md): the two rule languages built on these foundations.
- [conjunctive queries and UCQ](conjunctive-queries-and-ucq.md): queries, containment.
- [homomorphism search](../algorithms/homomorphism-search.md): computing homomorphisms efficiently.
- [labelled nulls](labelled-nulls.md), [Skolem functions and terms](skolem-functions-and-terms.md): the non-constant term kinds.
- [notation](../notation.md): the canonical symbols; [examples](../examples.md): the shared examples.
- [equality and UNA](equality-and-una.md), [stratified negation](stratified-negation.md), [perfect-model semantics](perfect-model-semantics.md): where closed-world and UNA matter.

## References

- C. Beeri, M. Y. Vardi. *The implication problem for data dependencies*. ICALP 1981. [U]
- A. K. Chandra, H. R. Lewis, J. A. Makowsky. *Embedded implicational dependencies and their inference problem*. STOC 1981. [U]
- A. Deutsch, A. Nash, J. Remmel. *The chase revisited*. PODS 2008. https://doi.org/10.1145/1376916.1376938
- S. Abiteboul, R. Hull, V. Vianu. *Foundations of Databases*. Addison-Wesley, 1995 (chapters on CQs, Datalog, negation). [U]
- A. K. Chandra, P. M. Merlin. *Optimal implementation of conjunctive queries in relational data bases*. STOC 1977. [U]
- M. H. van Emden, R. A. Kowalski. *The semantics of predicate logic as a programming language*. JACM 23(4), 1976. [U]
- R. Fagin, P. G. Kolaitis, R. J. Miller, L. Popa. *Data exchange: semantics and query answering*. TCS 336, 2005. [U]
- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. AIJ 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- M.-L. Mugnier, M. Thomazo. *An introduction to ontology-based query answering with existential rules*. Reasoning Web 2014. [U]
