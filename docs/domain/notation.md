# Notation

The canonical notation of this reference. Every domain page uses these symbols, so that definitions written by different authors compose without silent translation. The last section maps them to the notations of the main communities (DLGP, the Datalog± literature, answer set programming, description logics). When a source uses another notation, translate it into this one and, if the translation is not obvious, say so on the page.

## Summary

| Object | Symbol | Sets | Example |
|---|---|---|---|
| Constant | `a, b, c`, or a lower-case name | `𝐂` | `tom`, `200.00`, `"text"` |
| Variable | `x, y, z`, `u, v, w` | `𝐕` | `x` |
| Labelled null | `n, n1, n2, ...` | `𝐍` | `n1` |
| Function symbol | `f, g, h`, or a lower-case name | `𝐅` | `manager` |
| Predicate | `p, q, r`, or a lower-case name | `𝐏` | `employee` |
| Term | `t, s` | `𝐓` | `manager(tom)` |
| Tuple of terms or variables | `t̄`, `x̄` (bar) | | `x̄ = (x1, x2)` |
| Atom | `α, β`, or `p(t̄)` | | `managerOf(x, y)` |
| Conjunction / set of atoms | `A, B, H` | | `employee(x) ∧ worksIn(x, d)` |
| Instance, database | `I`, `J` (instances), `D` (input database) | | `D = {employee(tom)}` |
| Substitution | `σ, θ` | | `σ = {x ↦ tom}` |
| Homomorphism | `h` | | `h : A → I` |
| Rule | `ρ` | | `ρ = B → H` |
| Rule set | `Σ` (rules), `P` (logic program) | | |
| Knowledge base | `K = (D, Σ)` | | |
| Query | `q(x̄)` (CQ), `Q` (UCQ or general query) | | |
| Interpretation, model | `M`, `𝓜` | `Mod(K)` | |

## 1. Signature and terms

- A **signature** fixes pairwise disjoint sets of **constants** `𝐂`, **function symbols** `𝐅` (each with an arity `ar(f) ≥ 1`) and **predicates** `𝐏` (each with an arity `ar(p) ≥ 0`). Variables `𝐕` and labelled nulls `𝐍` are disjoint from all of them and from each other.
- **Constants** include symbolic names and typed literals (numbers, strings, dates). In prose, write them in lower case or as literals: `tom`, `c1`, `200.00`, `"Paris"`.
- **Variables** are lower-case `x, y, z` in logic notation. Program syntax follows the convention of the language shown (upper-case `X, Y` in Datalog, Prolog, ASP and DLGP); see §8.
- **Labelled nulls** are written `n1, n2, ...` in prose and `_:n1` in program-like listings (the RDF blank-node convention). They name unknown individuals in instances; they are never written in rules.
- **Terms**: `t ::= x | a | n | f(t1, ..., tk)`. A **functional term** is a term `f(t̄)`. A term is **ground** if it contains no variable. `vars(·)` denotes the variables of a term, atom, conjunction or rule; `terms(·)` its terms; `adom(I)` the active domain (all terms) of an instance `I`.
- **Tuples**: `x̄`, `t̄` (bar). `|x̄|` is the length. A tuple used as a set means the set of its elements.

## 2. Atoms, instances, conjunctions

- An **atom** is `p(t1, ..., tk)` with `k = ar(p)`. Greek letters `α, β` denote atoms when the predicate is irrelevant.
- A **fact** is a ground atom. An **instance** `I` is a set of facts (finite unless stated). `D` denotes the input database (the facts given by the user). The **critical instance** for a rule set is written `I*`.
- A **conjunction** of atoms is identified with the set of its atoms: `B = {α1, ..., αm}` and `α1 ∧ ... ∧ αm` are interchangeable.
- **Built-in atoms** (comparisons, arithmetic) are written infix: `x > 200.00`, `z = x * y`.
- **Equality atoms**: `t1 = t2`; **falsity**: `⊥`.

## 3. Substitutions, unifiers, homomorphisms

- A **substitution** `σ` is a finite mapping from variables to terms, written `{x ↦ tom, y ↦ f(z)}`. Application is written **prefix**: `σ(t)`, `σ(α)`, `σ(B)`. Composition `σ ∘ θ` applies `θ` first. `σ|_X` is the restriction to the variables `X`.
- A **unifier** of atoms `α, β` is a substitution with `σ(α) = σ(β)`; `mgu(α, β)` is a most general unifier.
- A **homomorphism** `h : A → B` from a set of atoms `A` to a set of atoms `B` maps the variables (and, when stated, the labelled nulls) of `A` to terms of `B`, is the identity on constants, and satisfies `h(A) ⊆ B`. Write `A → B` for "there is a homomorphism from `A` to `B`" and `A ↔ B` for homomorphic equivalence.
- An **isomorphism** is a bijective homomorphism whose inverse is a homomorphism; instances equal up to null renaming are **isomorphic**.

## 4. Rules

All rules are implicitly universally quantified over their body variables; the quantifier `∀` may be omitted.

| Rule kind | Canonical form | Notes |
|---|---|---|
| Datalog rule | `B → H` with `vars(H) ⊆ vars(B)` | Usually `H` is a single atom. |
| Existential rule (TGD) | `B[x̄, ȳ] → ∃z̄ H[x̄, z̄]` | `x̄` is the **frontier** `fr(ρ) = vars(B) ∩ vars(H)`; `z̄` are the **existential variables**. |
| Rule with function terms | `B → H` where `H` contains terms `f(t̄)` | Either the result of Skolemisation (each function symbol is a fresh, rule-local **Skolem function** `f^ρ_z`, see §7) or a rule using **shared function symbols** written by the user and possibly also used in other rules, data and queries. The two are not equivalent: shared symbols identify individuals across rules. |
| Normal rule (default negation) | `B⁺ ∧ not B⁻ → H` | `not` is default negation (negation as failure); `B⁺` positive body, `B⁻` negated atoms. |
| Equality-generating dependency (EGD) | `B → t1 = t2` | |
| Negative constraint | `B → ⊥` | Integrity constraint; in program syntax `:- B.` or `! :- B.` |
| Disjunctive rule | `B → H1 ∨ ... ∨ Hk` | ASP, disjunctive Datalog. |

- `body(ρ)`, `head(ρ)`, `fr(ρ)` denote the body, head and frontier of `ρ`.
- A **trigger** for `ρ` on `I` is a pair `(ρ, h)` with `h : body(ρ) → I`. It is **active** if `h` does not extend to a homomorphism `h' : head(ρ) → I`.
- **Default negation** is always written `not` (also in logic notation). The symbol `¬` is reserved for **classical (strong) negation**. Many papers on stratified Datalog write `¬p(x)` for default negation; when quoting them, translate to `not p(x)` and say so.

## 5. Negation, aggregates, built-ins

- **Default negation**: `not p(t̄)`. Safety: every variable of `not p(t̄)` occurs in a positive body atom (unless explicitly anonymous).
- **Aggregates**, in the ASP-Core-2 style: `#agg{ e, ȳ : C }` where `#agg ∈ {#count, #sum, #min, #max}`, `e` is the aggregated term, `ȳ` the **tuple key** (the collection is a set of tuples `(e, ȳ)`), and `C` a conjunction. Example: `s = #sum{ p * k, i : inBasket(b, i, k), price(i, p) }`. Group-by variables are the variables of `C` that also occur outside the aggregate.
- **Comparison and arithmetic** built-ins: `=, ≠, <, ≤, >, ≥, +, −, *, /`. Rounding functions are written `round_m(x, s)` with an explicit mode `m` and scale `s` (see [exact decimals and rounding](concepts/exact-decimals-and-rounding.md)).

## 6. Queries and answers

- **Conjunctive query (CQ)**: `q(x̄) = ∃ȳ B[x̄, ȳ]`; `x̄` are the **answer variables**. Boolean CQ: `x̄` empty, written `q()`.
- **Union of CQs (UCQ)**: `Q = q1 ∨ ... ∨ qk` (same answer variables), also written as a set `{q1, ..., qk}`.
- **Evaluation** on an instance: `ans(q, I) = { h(x̄) | h : B → I }`.
- **Certain answers**: `cert(q, K) = { ā ∈ 𝐂^{|x̄|} | K ⊨ q(ā) }`. Certain answers contain constants only.
- **Answers in a designated model** (least, perfect, well-founded): `ans(q, M)`, with `M` named explicitly.

## 7. Semantics, entailment, chase

| Notion | Notation |
|---|---|
| Satisfaction, entailment | `M ⊨ φ`; `K ⊨ φ`; `Σ ⊨ ρ` (rule entailment) |
| Models of `K` | `Mod(K)` |
| Logical equivalence of rule sets | `Σ1 ≡ Σ2` |
| Immediate-consequence operator | `T_P(I)`; powers `T_P↑i`; least fixpoint `lfp(T_P)` |
| Least (Herbrand) model | `LM(P)` |
| Perfect model (stratified programs) | `PM(P)` |
| Stable models / answer sets | `SM(P)` |
| Well-founded model | `WF(P)` |
| Gelfond-Lifschitz reduct | `P^I` |
| Stratification | `P = P1 ∪ ... ∪ Pk` (strata, lowest first); `str(p)` the stratum of predicate `p` |
| Chase result | `chase(D, Σ)`; a variant is subscripted: `chase_o` (oblivious), `chase_so` (semi-oblivious), `chase_sk` (Skolem), `chase_r` (restricted), `chase_df` (Datalog-first restricted), `chase_c` (core) |
| Chase sequence | `I0 = D, I1, I2, ...`; `Ii+1 = Ii ∪ σ(head(ρ))` for a trigger |
| Universal model | `U` |
| Graph of rule dependencies | `GRD(Σ)` |
| Predicate dependency graph | `PDG(P)` |
| Skolemisation | `sk(Σ)`; Skolem function for existential variable `z` of rule `ρ`: `f^ρ_z` |
| Size of a structure | `|·|` |
| Complexity classes | `PTIME`, `NP`, `EXPTIME`, `2EXPTIME`, `AC0`; always state which measure: **data complexity** (rules and query fixed, only `D` varies) or **combined complexity** (`D`, `Σ` and the query all part of the input) |

## 8. Program syntax in examples

Logic notation (above) is canonical for definitions and results. Examples may also use a **program syntax** so that they can be run in existing systems. Pages state which convention they use:

- **Datalog / Prolog / ASP style** (default for examples): `head :- body.`, variables start with an upper-case letter or `_`, constants and predicates with a lower-case letter, `not` for default negation, `:- body.` for constraints, ASP-Core-2 aggregates.
- **DLGP style** for existential rules: a head variable not occurring in the body is existential. Because this is easy to miss, always add a comment `% Z existential` or prefer logic notation.

```prolog
superiorOf(Y, X) :- managerOf(Y, X).                        % Datalog rule
generalConditionsApply(C) :- contract(C), not specificConditionApplies(C).
total(B, S) :- basket(B), S = #sum{ P*Q, I : inBasket(B, I, Q), price(I, P) }.
managerOf(Y, X) :- employee(X).                             % DLGP: Y existential
:- managerOf(X, X).                                         % constraint
```

## 9. Correspondence with other notations

| Concept | This reference | DLGP | Datalog± literature | ASP (ASP-Core-2, clingo, DLV) | Description logics |
|---|---|---|---|---|---|
| Variable | `x, y` | `X, Y` | `X, Y` (bold for tuples) | `X, Y` | not explicit (concept language is variable-free) |
| Constant | `a, tom` | `a`, `<iri>`, literals | `a, b, c` | `a`, numbers, strings | individual name `a` |
| Labelled null | `n1`, `_:n1` | not in input syntax [U] | `ζ1, ζ2` or `⊥1` [U] | none (function terms instead) | anonymous individual |
| Functional term | `f(t̄)` | not supported [U] | Skolem term `f(X)` | `f(X)` (uninterpreted) | none |
| Atom | `p(t̄)` | `p(t1,...,tk)` | `p(X)` | `p(X)` | concept assertion `A(a)`, role assertion `r(a, b)` |
| Rule body → head | `B → H` | `H :- B.` | `φ(X, Y) → ∃Z ψ(X, Z)` | `H :- B.` | `C ⊑ D` (axiom) |
| Existential variable | `∃z` in the head | head variable absent from the body | `∃Z` | not expressible (use `f(X)`) | `∃r.C` on the right of `⊑` |
| Default negation | `not p(t̄)` | `not p(t̄)` in extensions only [U] | often `¬p(X)` (stratified) | `not p(X)` | none (open world) |
| Classical negation | `¬p(t̄)` | none | `¬` | `-p(X)` | `¬C` |
| Negative constraint | `B → ⊥` | `! :- B.` | `φ(X) → ⊥` | `:- B.` | `C ⊓ D ⊑ ⊥` |
| Equality (EGD) | `B → x = y` | none [U] | `φ(X) → X1 = X2` | `X = Y` in constraints only | functional roles, `≤1 r` |
| Query | `q(x̄) = ∃ȳ B` | `?(X) :- B.` | `q(X) ← ∃Y φ(X, Y)` | `#show` / brave or cautious reasoning | instance query, conjunctive query |
| Fact base | `D` | facts `p(a).` | `D` (database) | facts | ABox `𝒜` |
| Rule set | `Σ` | rules | `Σ` | program `P` | TBox `𝒯` |
| Entailment | `K ⊨ φ` | | `D ∪ Σ ⊨ q` | cautious consequence | `𝒦 ⊨ α` |
| Model of choice | stated explicitly | | all FO models (certain answers) | stable models | all FO models |

Entries tagged `[U]` must be checked against the cited specification before being relied upon (see [disputes](disputes.md) for contested entries).

## References

- DLGP: J.-F. Baget, A. Gutierrez, M. Leclère, M.-L. Mugnier, S. Rocher, C. Sipieter. *DLGP: An extended Datalog syntax for existential rules and Datalog± — Version 2.0*. GraphIK technical report, 2015. [U]
- ASP-Core-2: F. Calimeri, W. Faber, M. Gebser, G. Ianni, R. Kaminski, T. Krennwallner, N. Leone, M. Maratea, F. Ricca, T. Schaub. *ASP-Core-2 input language format*. TPLP 20(2), 2020.
- Datalog±: A. Calì, G. Gottlob, T. Lukasiewicz. *A general Datalog-based framework for tractable query answering over ontologies*. Journal of Web Semantics 14, 2012.
- Description logics: F. Baader, I. Horrocks, C. Lutz, U. Sattler. *An Introduction to Description Logic*. Cambridge University Press, 2017.
- Existential rules: M.-L. Mugnier, M. Thomazo. *An introduction to ontology-based query answering with existential rules*. Reasoning Web 2014, LNCS 8714.
