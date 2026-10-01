# Foundations: first-order logic for rule-based reasoning

The minimal first-order logic needed to read every other page: terms, atoms, substitutions, homomorphisms, interpretations and models, entailment, certain answers, the open- and closed-world assumptions, and the unique-name assumption. It also fixes the notation used throughout the wiki. Everything here is textbook material; the project-specific choices are flagged in "In this project".

> **Status in this project:** `background` `v0` `F2` — notation reference for all pages; term kinds fixed by [D1](../project/decisions.md#d1) and [E5](../project/requirements.md#e5); closed-world semantics of F2 by [D1](../project/decisions.md#d1) and [report 11 §4-§5](../../preliminary-analysis/11-f2-framework-definition.md).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

## Intuition

Take the [v0 example](../project/running-examples.md#v0-ex): the facts `managerOf(anna, tom)` and `managerOf(dir, anna)`, and the rule "a manager of X is a superior of X; a manager of a superior of X is a superior of X". Reasoning answers "who are the superiors of tom?" with `{anna, dir}`. To say precisely *why* `dir` is an answer, and why nobody else is, we need: a language (terms, atoms, rules), a notion of world in which the statements are true (models), and a notion of consequence (entailment). Queries are answered by finding the facts' **images** of the query pattern, which is a **homomorphism**: this one operation underlies query evaluation, rule application, query containment and rewriting.

## Formal definition

### Vocabulary and terms

- A **signature** fixes disjoint sets of **predicates** `p, q, ...`, each with an arity `ar(p) ≥ 0`; **constants** `a, b, tom, 200.00, "text"`; **function symbols** `f, manager, ...` with arity `≥ 1`.
- **Variables** `x, y, z` (in program syntax: upper case `X, Y`).
- **Terms**: `t ::= x | c | f(t1, ..., tn)`. A term is **ground** if it contains no variable.
- **Labelled nulls** `n1, n2, ...` (also written `_:b1`, `ν1`) are a separate set of symbols standing for unknown individuals, produced by existential rules. They behave like constants in an instance but may be renamed or mapped to other terms by homomorphisms. See [labelled nulls](labelled-nulls.md).
- In this project, **ground terms have three kinds**: constants (symbols and typed literals), named functional terms such as `manager(tom)`, and labelled nulls ([D1](../project/decisions.md#d1)).

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
  `cert(q, D, Σ) = { ā ∈ Const^{|x̄|} : D ∪ Σ ⊨ q(ā) }`.
- **Key fact.** If `U` is a universal model, `ā ∈ cert(q, D, Σ)` iff there is a homomorphism `h : q → U` with `h(x̄) = ā` and `ā` made of constants. So entailment of a CQ reduces to homomorphism search in one model. Answers mapping an answer variable to a labelled null are **not** certain answers (the null's identity is unknown).
- For plain [Datalog](datalog.md), the least Herbrand model is universal, and the certain answers are exactly the query's answers on it.

### Open world, closed world

- **Open-world assumption (OWA)**: a fact not entailed is *unknown*, not false. First-order entailment and certain answers are open-world: with only `employee(tom)`, the KB entails neither `isCompanyDirector(tom)` nor its negation.
- **Closed-world assumption (CWA)**: a fact not derivable is false. Negation as failure (`not p(X)`) and aggregation (`#count`, `#sum`) are only meaningful under a closed-world reading of the predicates they range over: counting "all" items requires knowing there are no others.
- **Positive programs and CQs do not see the difference**: the least model answers coincide with certain answers. The difference appears with negation, aggregation, and equality.

### Unique-name assumption (UNA)

- **UNA**: distinct constants denote distinct individuals (`tom ≠ anna` in every model considered).
- Under **Herbrand / free-constructor** semantics (logic programming), UNA extends to function terms: distinct ground terms denote distinct objects, so `manager(tom) ≠ anna` and `manager(tom) ≠ manager(anna)`.
- Without UNA (pure FOL), `manager(tom) = anna` is consistent; equality reasoning (EGDs, functional dependencies used as rules) can then merge terms. See [equality and UNA](equality-and-una.md).

## Key properties and results

| Result | Statement | Reference |
|---|---|---|
| Homomorphism theorem | CQ `q1` is contained in CQ `q2` iff there is a homomorphism `q2 → q1` mapping answer variables to answer variables in order (canonical database argument). | Chandra & Merlin, STOC 1977 |
| Least model of Horn programs | A set of definite (positive) rules plus facts has a least Herbrand model, the least fixpoint of the immediate-consequence operator, and it entails exactly the ground atoms true in all models. | van Emden & Kowalski, JACM 1976 |
| Universal model by the chase | For existential rules, the (possibly infinite) chase result is a universal model; CQ certain answers are its homomorphic images over constants. | Fagin, Kolaitis, Miller, Popa, TCS 2005; Deutsch, Nash, Remmel, PODS 2008 ([report 07 §1](../../preliminary-analysis/07-sota-theory.md)) |
| Undecidability | CQ entailment under arbitrary existential rules is undecidable (but semi-decidable). | Beeri & Vardi, ICALP 1981 ([report 07 §1.1](../../preliminary-analysis/07-sota-theory.md)) |
| Skolemisation | Replacing `∃z` by fresh rule-local function terms preserves entailment of sentences over the original signature (conservative). | classical; [report 09 §1.1](../../preliminary-analysis/09-skolem-function-frameworks.md) [U] |
| Herbrand vs FO reading | For positive programs and CQs over constants, Herbrand (free-constructor) and first-order readings agree; they diverge with negation, aggregation or equality. | [report 09 §1.1](../../preliminary-analysis/09-skolem-function-frameworks.md) [U, classical] |

## In this project

- **Term model**: three term kinds from day one (constant, named functional term, labelled null), consistent across all stores ([D1](../project/decisions.md#d1), [E5](../project/requirements.md#e5)). v0 populates only constants ([D6](../project/decisions.md#d6)).
- **v0 semantics**: least Herbrand model of a positive Datalog program; certain answers and model answers coincide.
- **F2 semantics**: answers are **truth in the perfect model** (closed world, Herbrand reading with unique names on ground terms), not certain answers over all FO models ([report 11 §5.1](../../preliminary-analysis/11-f2-framework-definition.md), DRAFT). On the positive fragment with constant answers both coincide (report 11 Prop. 5 [U-own]).
- **UNA and Skolem terms**: report 11 compares ground terms syntactically (`manager(paul) ≠ alice`); [D8](../project/decisions.md#d8)'s caveat (distinct Skolem terms may denote the same individual) and the E3-ex correction show this is a live design tension ([Q1](../project/open-questions.md#q1), [I7](../project/open-questions.md#known-inconsistencies)).
- **Homomorphism** is the core operation of query evaluation, rule application, the restricted chase, subsumption in rewriting, and core computation: it gets its own algorithm page with bi-connected-component decomposition ([E4](../project/requirements.md#e4)).

## Pitfalls

- **Answer variables bound to nulls are not certain answers.** Returning them as if they were individuals is unsound w.r.t. FO semantics. (F2 named terms are different: they are legitimate answers in mode `all`, report 11 OP-1 `[choice]`.)
- **Closed-world negation on an incomplete computation is unsound**, not just incomplete: `not q(a)` may succeed because `q(a)` was not derived *yet*. See [completeness statuses](completeness-statuses.md).
- **Homomorphisms must fix constants**, and in F2 must fix functional terms built from constants; only variables (and, for existential reasoning, nulls) may be mapped.
- **Unification is not homomorphism**: unification maps variables on both sides; homomorphism maps one side into a fixed instance.
- **`2`, `2.0` and `2.00`** are the same term in report 11 (value identity of decimals, OP-5 `[choice]`); comparing literals as strings is a bug.
- **Mixing up UNA with equality reasoning**: declaring an FD does not make the engine merge terms in F2; FDs are checked as constraints ([D2](../project/decisions.md#d2)).

## Related pages

- [Datalog](datalog.md), [existential rules](existential-rules.md): the two rule languages built on these foundations.
- [conjunctive queries and UCQ](conjunctive-queries-and-ucq.md): queries, containment.
- [homomorphism search](../algorithms/homomorphism-search.md): computing homomorphisms efficiently.
- [labelled nulls](labelled-nulls.md), [Skolem functions and terms](skolem-functions-and-terms.md): the non-constant term kinds.
- [equality and UNA](equality-and-una.md), [stratified negation](stratified-negation.md), [perfect-model semantics](perfect-model-semantics.md): where closed-world and UNA matter.

## References

- [Report 07 §1](../../preliminary-analysis/07-sota-theory.md) (decidability baseline, chase), [report 09 §1](../../preliminary-analysis/09-skolem-function-frameworks.md) (readings of Skolem terms), [report 11 §1, §4, §5](../../preliminary-analysis/11-f2-framework-definition.md) (F2 syntax and semantics, DRAFT).
- S. Abiteboul, R. Hull, V. Vianu. *Foundations of Databases*. Addison-Wesley, 1995 (chapters on CQs, Datalog, negation). [U]
- A. K. Chandra, P. M. Merlin. *Optimal implementation of conjunctive queries in relational data bases*. STOC 1977. [U]
- M. H. van Emden, R. A. Kowalski. *The semantics of predicate logic as a programming language*. JACM 23(4), 1976. [U]
- R. Fagin, P. G. Kolaitis, R. J. Miller, L. Popa. *Data exchange: semantics and query answering*. TCS 336, 2005. [U]
- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. AIJ 175(9-10), 2011 (cited in report 07).
- M.-L. Mugnier, M. Thomazo. *An introduction to ontology-based query answering with existential rules*. Reasoning Web 2014. [U]
