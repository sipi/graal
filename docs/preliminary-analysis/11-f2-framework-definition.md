# 11: Definition of the F2 framework (v1 reference contract)

**Status: DRAFT, awaiting owner validation.** This document is the reference contract that conformance tests and the implementation must follow. Where a choice had to be made that the owner has not yet taken, it is marked **[choice]** and repeated in §11 with its options and a recommended default.

Scope: F2 = stratified Datalog with **named** Skolem functions under perfect-model semantics, with exact decimals (decision D1). It encodes decisions D1, D2, D3 and D5 (see [README](README.md)) and requirements E1, E3, E5 and E10. Background: [report 09](09-skolem-function-frameworks.md) (named functions, `T(P)`, termination criteria) and [report 07](07-sota-theory.md) §7 (checklist of specification decisions).

Conventions:
- Propositions marked **(sketch)** have a proof sketch only. Like the [U-own] items of report 09, each needs a careful proof before it is relied on (E9).
- The concrete syntax is **provisional**. It is close to DLGP and Datalog and is used only to fix ideas; the abstract syntax in §1 is normative.
- Running examples: E1-ex (default/exception), E2-ex (basket threshold), E3-ex (line manager), as in the README.

---

## 1. Syntax

### 1.1 Signature

A signature `S = (Pred, Fun, Const)` consists of three sets.

- **Predicates** `Pred`: each `p ∈ Pred` has an arity `ar(p) ≥ 0`.
  - Names starting with `$` are reserved for predicates introduced by translations (`$L_f`, `$D_f`, `$viol_c`, …).
- **Named function symbols** `Fun`: each `f ∈ Fun` has an arity `ar(f) ≥ 1`.
  - Function symbols must be declared (`@function manager/1.`). An undeclared functional term is a syntax error: this catches typos that would otherwise silently invent objects.
  - Nullary functions are not allowed: they would be constants.
  - Function symbols, predicates and constants live in separate namespaces.
- **Constants** `Const` = `Sym ⊎ Lit`:
  - `Sym`: symbolic constants (identifiers or IRIs), e.g. `paul`, `<http://ex.org/paul>`;
  - `Lit`: typed literals whose value lies in one of the datatype value spaces of §1.2.

In addition, the term model has a set `Null` of **labelled nulls**, disjoint from everything above (D1, E5). F2 has no syntax that creates or mentions nulls. Nulls exist in the data model only so that F3 can be added without changing term identity. **[choice]** An F2 knowledge base whose input contains a null is rejected at load time (§11, OP-13).

### 1.2 Datatypes

| Datatype | Value space | Literal forms (provisional) | Order |
|---|---|---|---|
| `string` | finite sequences of Unicode code points | `"text"` | lexicographic by code point |
| `decimal` | `𝔻 = { m·10^(-s) : m ∈ ℤ, s ∈ ℕ }` (finite decimal fractions) | `75.50`, `-3.2`, `"75.50"^^xsd:decimal` | numeric |
| `integer` | `ℤ ⊂ 𝔻` | `42`, `-3`, `"42"^^xsd:integer` | numeric |
| `boolean` | `{false, true}` | `true`, `false` | `false < true` |

Justification:
- **Exact decimals, no binary floats** (D1): business rules compare money amounts exactly (E2-ex: `200.00`). `𝔻` is closed under `+`, `−` and `×`, which is all that sums and prices need.
- **`integer` is a subtype of `decimal`**, and literals denote values, not strings. So `2`, `2.0` and `2.00` are the **same term** (value identity, as in the XSD value space). The scale of a literal is not significant. Output uses a canonical form with trailing fractional zeros removed; formatting to a fixed scale is a presentation concern **[choice]** (OP-5).
- **Strings and symbols are distinct**: `"paul" ≠ paul`. Symbols carry identity (individuals); strings carry text.
- **Booleans** are needed for flags coming from enterprise data. They are plain constants; there is no three-valued logic.
- Not in v1: floats, dates and durations, language-tagged strings. Each can be added later as a new value space with its own order.

### 1.3 Terms, atoms, literals

- **Variables** `Var`: identifiers starting with an upper-case letter, and `_` (anonymous; every occurrence is a fresh variable).
- **Terms**: `t ::= X | c | f(t1,…,tn)` with `X ∈ Var`, `c ∈ Const`, `f ∈ Fun`, `n = ar(f)`. A term is **ground** if it has no variables. Ground terms are compared **syntactically** (free-constructor or Herbrand reading, [09 §1.1](09-skolem-function-frameworks.md)). For example `manager(paul) ≠ alice` and `manager(paul) ≠ manager(alice)`.
- **Depth**: `depth(c) = 0` and `depth(f(t̄)) = 1 + max depth(tᵢ)`.
- **Relational atom**: `p(t1,…,tn)` with `n = ar(p)`.
- **Built-in atoms**:
  - *comparisons* `t1 op t2` with `op ∈ {=, !=, <, <=, >, >=}`;
  - *assignments* `X = e`, where `X` is a variable and `e` an arithmetic expression built from terms with `+`, `−`, `*`, `/` and the functions `abs(e)`, `round(e,s)`, `round(e,s,mode)`, `div(e1,e2,s)`, `div(e1,e2,s,mode)`. Here `s` is an integer literal (the scale) and `mode ∈ {half_up, half_even, down, up, floor, ceiling}`.
- **Aggregate atom**: `X = #op{ e, Y1,…,Yk : C }` with `op ∈ {count, sum, min, max}`:
  - `e` is a term or arithmetic expression (the aggregated value);
  - `Y1..Yk` are the **tuple keys**; the aggregated collection is a set of tuples `(e, Y1..Yk)`, so two items with the same price are not collapsed (ASP-style, [09 §5.2](09-skolem-function-frameworks.md));
  - `C` is a conjunction of positive relational atoms, negated relational atoms and built-ins;
  - for `#count`, `e` may be omitted: `#count{ Y1..Yk : C }`.
- **Literals**: a relational atom `a` (positive), a negated relational atom `not a` (negative), a built-in atom, or an aggregate atom. Negation is **only** on relational atoms.

Provisional syntax: `not p(X)`; `S > 200.00`; `T = P * Q`; `S = #sum{ P*Q, I : inBasket(B,I,Q), price(I,P) }`.

### 1.4 Rules, facts, constraints

- **Rule**: `H1, …, Hm :- B1, …, Bk.` with `m ≥ 1`, where the `Hᵢ` are relational atoms and the `Bⱼ` literals.
  - A head is a **conjunction**. With no existential variables this is pure sugar for `m` rules sharing the body. Named terms are identical across them, because they are the same terms.
  - Head terms may contain named functional terms, but **no arithmetic**: `p(X+1)` must be written `p(Y) :- …, Y = X+1`.
- **Fact**: a rule with an empty body and ground head.
  - **[choice]** In v1, facts are **function-free** (constants only, OP-4). Functional terms enter the model only through rules.
  - The **data** `D` is the set of facts. A predicate may have both facts and rules (no strict EDB/IDB split).
- **Integrity constraint (negative constraint)**: `! :- B1, …, Bk.`, optionally named `@constraint c: ! :- … .` Its answers are *violations*. Constraints never change the model (D2).
- **Functional dependency**: `@fd p: A -> B.` with `A, B ⊆ {1..ar(p)}` disjoint position sets.
  - It is sugar for one constraint per position `j ∈ B`: `! :- p(x̄), p(ȳ), x_A = y_A, x_j != y_j`, where `!=` is syntactic term identity.
  - FDs are **integrity constraints only**: they are checked and violations are reported. They are never used to equate terms (D2, option O1 of [09 §4](09-skolem-function-frameworks.md)).

### 1.5 Lookup-before-invent declarations

A **lookup declaration** for a function `f/n` has the form

```
@lookup f(X1,…,Xn) = Y :- λ.
```

where `X1..Xn, Y` are distinct variables and `λ` is a conjunction of positive relational atoms and built-ins that is safe for `X1..Xn, Y` (§2). Example: `@lookup manager(X) = Y :- recordedManager(X, Y).`

Intended meaning (D2): the value of `f(t̄)` is `v` whenever `λ` records `v` for `t̄`. Only when nothing is recorded for `t̄` is the fresh object `f(t̄)` invented. At most one lookup declaration per function symbol is allowed. A function without a lookup declaration is a pure constructor (option O0 of report 09).

Each lookup declaration **implicitly declares** the integrity constraint "the recording is functional": `! :- λ[Y↦Y1], λ[Y↦Y2], Y1 != Y2` (the renaming applies to the non-argument variables). A violation is reported as a D2 conflict (OP-3).

**[choice]** The lookup source is an arbitrary conjunction, subject to stratification (§3). In the natural modelling of E3-ex, the recorded relation (`recordedManager`) is distinct from the derived one (`hasManager`). Using `hasManager` itself as the source would create a cycle, which is detected and rejected (§3.3). A sugar `p@db` ("the facts of `p` in `D` only") is proposed in OP-2 to make the common pattern one line.

### 1.6 Queries and pre-registered queries

- A **conjunctive query with negation** (NCQ) is `?(X1,…,Xk) :- B1,…,Bm.`: a rule whose head is the answer tuple. Its body may contain negated atoms, built-ins, aggregates and functional terms. A CQ is an NCQ without negation or aggregates. A **union** (UCQ, UNCQ) is a finite set of such queries with the same answer arity. Boolean queries have `k = 0`.
- **Answer mode** `mode ∈ {all, constants}`: whether answer tuples containing functional terms are returned (§5.2). The default is `all` **[choice]** (OP-1).
- A **pre-registered query** is `@query name [mode] ?(X̄) :- … .`; several declarations with the same name form a union. Pre-registered queries are the only candidates for a-priori rewriting (§8.3).

A **knowledge base** is `K = (S, P, D, Λ, C)`: signature, rules, facts, lookup declarations, and constraints (including FDs). Queries are posed against `K`.

---

## 2. Well-formedness

### 2.1 Safety

A variable is **bound** in a body if it satisfies one of these:
1. it occurs in a positive relational atom of the body, at any depth: `q(manager(X))` binds `X` by matching;
2. it is the left-hand side of an assignment `X = e` all of whose variables are bound;
3. it is the result variable of an aggregate atom;
4. it is equated by `X = Y` to a bound `Y`.

The binding order must be acyclic.

A rule, query or constraint is **safe** iff:
- every variable of the head is bound;
- every variable of a negated atom is bound, except anonymous `_` variables. These are local to the negation: `not hasManager(X,_)` means `¬∃Y hasManager(X,Y)`.
- every variable of a comparison, and of the right-hand side of an assignment, is bound;
- for an aggregate atom `X = #op{ e, Ȳ : C }`:
  - *local* variables (occurring only inside the braces) must be safe with respect to `C` alone;
  - *global* variables (also occurring outside) are the **group-by variables** (§4.4).

A lookup body `λ` must bind `X1..Xn` and `Y`. Unsafe programs are rejected.

### 2.2 Typing (soft typing)

**[choice]** v1 uses *soft typing* (OP-12):
- Optional declarations `@type p(τ1,…,τn).` with `τ ∈ {any, symbol, string, integer, decimal, boolean, term}`. `term` means "a functional term".
- **Static check**: type inference propagates declared types through rules. A rule is rejected when an arithmetic operand, a `<`-comparison argument, or a `#sum` value is **certainly** non-numeric (e.g. bound to a `symbol`-typed position, or to a functional term).
- **Dynamic check**: every declared `@type` is also an integrity constraint on the model. A fact violating it is reported as a violation; it is not rejected.
- **Runtime semantics of ill-typed built-ins**: arithmetic on non-numbers, or `<` between values of different datatypes, is **false** (the built-in relation does not hold) and produces a diagnostic. Equality `=`/`!=` is defined on all terms.

---

## 3. Stratification

### 3.1 The lookup translation `lb(K)`

Stratification and semantics are defined on the translated program `lb(K)`, which eliminates lookup declarations. For every `f` with a lookup declaration `f(x̄) = y :- λ`, add:

```
$L_f(x̄, y) :- λ.            % recorded graph of f
$D_f(x̄)    :- $L_f(x̄, _).   % "a value of f(x̄) is recorded"
```

For every rule, query and constraint `r`, repeat until no unresolved lookup term remains:
1. choose an **innermost unresolved** term `f(s̄)` in `r`, with `f` lookup-declared and no unresolved lookup term inside `s̄`;
2. replace `r` by two rules:
   - `r_look`: every occurrence of `f(s̄)` in `r` is replaced by a fresh variable `Y`, and `$L_f(s̄, Y)` is added to the body;
   - `r_inv`: `r` unchanged, with `not $D_f(s̄)` added to the body, and `f(s̄)` marked resolved.

Remarks:
- All occurrences of the *same* term `f(s̄)` in a rule are resolved together, since they denote the same object.
- A rule with `k` distinct lookup terms yields at most `2^k` rules. Each is safe if `r` is: `s̄` is bound wherever `f(s̄)` may occur (§2.1).
- Constraints (FDs, `@type`, lookup functionality) become rules `$viol_c(x̄) :- body`.

### 3.2 Dependency graph and stratifiability

The **dependency graph** `G(K)` has one node per predicate of `lb(K)`, one node per query, and edges `h → q` for every rule of `lb(K)` with head predicate `h` (or query node `h`) and a body literal on `q`:
- **positive** edge when `q` occurs in a positive relational atom;
- **negative** edge when `q` occurs in a negated atom;
- **aggregate** edge when `q` occurs inside an aggregate atom, whatever the polarity inside it.

Negative and aggregate edges are called **strict** edges. Through `r_inv`, every predicate whose rules use a lookup-declared `f` gets a strict edge to `$D_f`, and through `$D_f` it depends on the predicates of `λ_f`. These are the **lookup-induced edges** of D2.

**Definition (stratifiable).** `K` is stratifiable iff no cycle of `G(K)` contains a strict edge. A **stratification** is a map `σ` from nodes to ℕ with:
- `σ(h) ≥ σ(q)` for every positive edge `h → q`;
- `σ(h) > σ(q)` for every strict edge `h → q`.

The canonical stratification assigns strongly connected components (SCCs) in topological order. Constraint and query nodes are sinks (top strata).

**Aggregates are non-recursive in v1**: an aggregate edge must not lie on a cycle, which the definition already enforces. Recursive monotonic aggregation (Ross & Sagiv 1997; limit Datalog, Kaminski et al. 2017) is future work.

Stratification is **predicate-level** ([07 §7, item 14](07-sota-theory.md)). A finer dependency relation, using term unification between heads and bodies (the GRD of [09 §3](09-skolem-function-frameworks.md)), is used for termination analysis and scheduling (§7) but never to accept a program that is not predicate-stratifiable.

### 3.3 Non-stratifiability created by lookup (D2)

**Proposition 1 (sketch).** Suppose `K` without its lookup declarations is stratifiable. Then `K` is non-stratifiable iff some lookup-declared `f` has a predicate of `λ_f` that reaches in `G(K)` the head of a rule of `P` that contains `f`. Informally: *the recorded value of `f` depends on a value derived through `f`*.

*Sketch.* The only strict edges added by `lb` are `h → $D_f`, and `$D_f → $L_f → λ_f` are positive. A new cycle through a strict edge therefore closes exactly when `λ_f` reaches `h`.

The analyser must report such cycles with:
- the function `f`;
- the cycle as a chain of original rules;
- a remedy: record the source in a separate predicate, or use `p@db` (OP-2).

Example: `@lookup manager(X)=Y :- hasManager(X,Y).` with the rule `hasManager(X, manager(X)) :- employee(X).` is rejected with the cycle `hasManager →(lookup) $D_manager → hasManager`.

---

## 4. Semantics

### 4.1 Universe, interpretations

- The **Herbrand universe** `U` is the least set containing `Const` and closed under `f(t̄)` for `f ∈ Fun`. Nulls are not in `U` for F2.
- The **Herbrand base** `B` is the set of ground relational atoms over `U`.
- Built-in atoms have a **fixed interpretation** (§4.3). An interpretation is a subset `I ⊆ B`. Distinct ground terms denote distinct objects (unique names on terms).

### 4.2 Perfect model

Let `lb(K)` be stratified by `σ` with strata `P_0, …, P_n`. Let `M_{-1} = D`. For each stratum `i`:
- `T_i(I)` = `I` ∪ the heads `Hθ` of all ground instances `θ` of rules of `P_i` such that:
  - every positive atom `aθ ∈ I`;
  - every negated atom `aθ ∉ M_{i-1}`;
  - every built-in holds;
  - every aggregate holds with its collection computed on `M_{i-1}` (§4.4).
- `M_i = ⋃_{k∈ℕ} T_i^k(M_{i-1})`, the least fixpoint above `M_{i-1}`. `T_i` is monotone and continuous, because negation and aggregates read only the fixed `M_{i-1}`.
- The **perfect model** is `PM(K) = M_n`, restricted to the predicates of `K` (reserved `$` predicates hidden, except violations).

Known facts (Apt, Blair & Walker 1988; Przymusinski 1988 [U, classical]):
- `PM(K)` does not depend on the chosen stratification;
- it is well defined even when infinite: function symbols do not affect stratification;
- it is a minimal model of the rules.

`PM(K)` may be infinite (E3-ex trap). Violations of a constraint `c` are the tuples `x̄` with `$viol_c(x̄) ∈ M_n`.

### 4.3 Built-ins and exact arithmetic

Every built-in is a fixed relation on `U`:
- `=` and `!=` are identity and non-identity of ground terms (numeric literals are compared by value, since they are the same term).
- `<`, `<=`, `>`, `>=` hold only between two numbers, two strings or two booleans, using the order of §1.2. They are false between different datatypes and on symbols or functional terms (with a diagnostic).
- `+`, `−`, `*` are the exact operations on `𝔻`. There is **no overflow** in the semantics (unbounded precision).
- **Division** `e1 / e2` **[choice]** (OP-6) is a *partial* function:
  - defined iff `e2 ≠ 0` and the exact rational quotient lies in `𝔻`, i.e. its reduced denominator has only the prime factors 2 and 5;
  - otherwise the assignment does not hold for that instance (the rule does not fire) and a diagnostic `arithmetic-undefined` is attached to the result.
  - `div(e1, e2, s[, mode])` is total for `e2 ≠ 0`: it is the exact quotient rounded to scale `s`.
- **Rounding** `round(e, s[, mode])` rounds to `s` fractional digits. **[choice]** The default mode is `half_up` (half away from zero, commercial rounding; OP-7). `half_even` (banker's rounding) and the directed modes are available explicitly.
- **Implementation limit.** Implementations bound the number of significant digits (a resource budget, §6.4). Exceeding it is *not* a semantic value: it is treated as budget exhaustion, so the affected evaluation unit becomes incomplete. The model is never silently altered.

Because built-ins are relations, an undefined operation makes an atom false. That is a definition, not an approximation, so it never threatens soundness.

### 4.4 Aggregates

Let `r` contain `X = #op{ e, Ȳ : C }`, let `ḡ` be its global (group-by) variables, and let `θ` be a ground substitution for `ḡ`. The **collection** of the group is

  `Coll(θ) = { (eθρ, Ȳθρ) : ρ ground, M_{i-1} ⊨ Cθρ, eθρ defined }`,

a **set of tuples**. The value is computed on the first components, counted with multiplicity over distinct tuples:
- `#count` is `|Coll(θ)|`;
- `#sum` is the exact sum in `𝔻`. It is defined only if every value is numeric; otherwise it has no value and a diagnostic is raised;
- `#min` and `#max` are defined only if `Coll(θ)` is non-empty and its values all belong to one ordered datatype (numbers, or strings). Collections containing functional terms, symbols or mixed datatypes have no `#min`/`#max`.

**Groups** **[choice]** (OP-9). Two cases arise, depending on where the group-by variables are bound.
- **Grounded groups**: every group-by variable is also bound outside the aggregate, as in `total(B,S) :- basket(B), S = #sum{…}`. The group exists for every such binding, even if `Coll(θ)` is empty. On an empty collection, `#count = 0`, `#sum = 0`, and `#min`/`#max` have no value, so the rule does not fire.
- **Implicit groups** (SQL `GROUP BY` reading): some group-by variable occurs only inside the aggregate, as in `total(B,S) :- S = #sum{ P*Q, I : inBasket(B,I,Q), price(I,P) }`. The groups are the bindings of `ḡ` for which `C` has at least one solution. Empty groups do not exist.

Further rules:
- **Functional terms in collections** are ordinary distinct values: `#count{ M : hasManager(_,M) }` counts `alice` and `manager(dave)` as two objects. This is correct by the free-constructor reading, and it is D2's purpose that recorded managers are *not* also invented.
- **Infinite collections** (possible when a lower stratum is infinite) have no value. The engine can never establish this, so any answer depending on it has status UNKNOWN (§6).
- **Non-recursive in v1** (§3.2).

### 4.5 Correctness of the lookup translation

For a model `M ⊇ M_{σ($D_f)}` and ground `s̄`, define the **value set** `val_M(f(s̄)) = { v : $L_f(s̄, v) ∈ M }` if non-empty, and `{ f(s̄) }` otherwise.

**Proposition 2 (sketch).** Let `M = PM(K)`, computed on `lb(K)`. Then:
1. **Invention only when unrecorded.** A term `f(s̄)` with `f` lookup-declared occurs in a fact of `M` introduced by a rule of `P` only if `$D_f(s̄) ∉ M`.
2. **Value semantics.** `M` restricted to the predicates of `K` is the perfect model of the program obtained from `P` by reading every ground lookup term `f(s̄)` in a rule instance as *any* element of `val_M(f(s̄))`, innermost first. When every `$L_f` is functional (no D2 conflict), this reading is deterministic: `f(s̄)` is the recorded value if one exists, and `f(s̄)` itself otherwise.
3. **Datalog-first correspondence.** When `λ_f` does not depend on `f`, this coincides with the Datalog-first restricted chase on `T(P)` with the bridge rule `λ_f → F_f(x̄, y)` ([09 §4, O2](09-skolem-function-frameworks.md); §9 below).

*Sketch.* By stratification, `$D_f` lies strictly below every rule using `f`, so its extension is fixed when those rules are evaluated. A ground instance of an original rule with `k` lookup terms corresponds to the instances of its `2^k` variants whose look/invent pattern agrees with `$D_f` in `M`. `r_inv` fires exactly for unrecorded arguments, and `r_look` fires once per recorded value. Point 1 follows because only `r_inv` variants keep `f(s̄)`. Point 2 follows by unfolding, which preserves perfect models for stratified programs [U, classical].

Caveat (only under a D2 conflict): if `$L_f(s̄,·)` has several values, two distinct terms `f(s̄)` and `f(s̄')` that coincide at run time (`s̄ = s̄'`) may be resolved to different recorded values in the same rule instance. The functionality constraint reports the conflict.

---

## 5. Reasoning tasks and answers

### 5.1 Tasks

| Task | Input | Output |
|---|---|---|
| **Materialisation** | `K` | `PM(K)` (or a sound part of it), violations, status per SCC (§6) |
| **Query answering** | `K`, (U)NCQ `Q`, mode | `Ans(Q,K)`, or a sound part of it, with a status (§6) |
| **Entailment** | `K`, ground atom or Boolean (U)NCQ | `true` / `false` / `unknown`, with a status |
| **Constraint checking** | `K` | violations, each with an explanation; the status of the claim "no (other) violation" |
| **Explanation** | `K`, a returned fact or answer | a proof tree over the *original* rules (below) |

`Ans(Q, K) = { t̄ ∈ U^k : PM(K ∪ {ans_Q}) ⊨ ans_Q(t̄) }`, where `ans_Q` is `Q` read as a top-stratum rule. Answers are **truth in the perfect model** (closed world), not certain answers over all first-order models. On the positive fragment with constant answers, the two coincide (Proposition 5, §9).

`Ans(Q,K)` may be **infinite** (E3-ex trap). Only a finite part can then be returned, and the status says so.

### 5.2 Functional terms in answers **[choice]** (OP-1)

Recommended: in mode `all` (default), answers **may contain named functional terms**, returned as structured ground terms (`manager(dave)`). The presentation marks the term kind: constant, typed literal, or functional term, plus labelled null in F3. Named terms are stable, meaningful identifiers (the reason for choosing F2). In mode `constants`, tuples containing a functional term are dropped. This mode is used for Graal-oracle comparison (§9) and enables query-specific pruning (§8.3).

Output order is deterministic. A fixed total order on ground terms is used for presentation only:
- by kind: numbers < strings < booleans < symbols < functional terms;
- then by value;
- functional terms by symbol, then arguments, lexicographically.

### 5.3 Explanations

Every fact of `PM(K)` has a finite derivation, because it is in a least fixpoint. An **explanation** is a finite proof tree whose internal nodes are instances of original rules (traced through `lb`, E8), with these leaves:
- a data fact;
- a built-in that holds;
- `not a`, justified as "`a ∉ PM`, `a`'s evaluation unit is complete (§6)";
- an aggregate, justified by its full collection from a complete unit;
- `lookup f(s̄) = v` (the `$L_f` fact and its own explanation);
- `invented f(s̄)`, justified as "no recorded value: `$D_f(s̄)` absent from the complete unit of `$D_f`".

Minimal explanations are not required in v1.

---

## 6. Soundness under non-termination (E1)

### 6.1 The problem

With stratified negation, a higher stratum evaluated over an **incomplete** lower stratum can derive facts that are **false** in `PM(K)`. `not q(a)` succeeds on the partial extension although `q(a) ∈ PM(K)`. Aggregates are also non-monotone: a partial `#sum` is simply a different number. Non-termination and budgets must therefore be tracked **per evaluation unit** and propagated along strict edges.

### 6.2 Evaluation units and their state

An **evaluation unit** is:
- for materialisation: an SCC of `G(K)`;
- for goal-directed strategies: a tabled subgoal, or an SCC of mutually dependent subgoals (§8.2).

An engine run assigns to each evaluated unit `C`:
- the computed set `E(C)` of facts over its predicates (or subgoal instances);
- a flag `fix(C)`: `reached` iff the unit's fixpoint was reached **and** no derivation inside it was cut by a budget (§6.4); `not reached` otherwise.

Let `Inc = { C : fix(C) = not reached }`, and let *reaches* be reachability in `G(K)`.

**Definition (unit labels).** For an evaluated unit `C`:
- `C` is **complete** iff `C ∉ Inc` and `C` reaches no unit of `Inc`.
- `C` is **exposed** iff some path from `C` to a unit of `Inc` traverses a strict (negative or aggregate) edge.
- `C` is **sound-partial** iff it is neither complete nor exposed, i.e. it reaches `Inc` only through positive edges.

**Normative rule (N1).** The engine **must not** return, as answers or entailed facts, facts of an exposed unit. It may evaluate exposed units internally, for example for a preview (OP-11), but their facts must never be mixed with sound results.

**Proposition 3 (soundness and completeness; sketch).** Suppose each unit is evaluated by a **fair** monotone procedure. Suppose further that negated atoms and aggregates over a lower unit `C'` are evaluated on `E(C')`. Then:
1. `E(C) ⊆ PM(K)` for every non-exposed unit `C`;
2. `E(C) = PM(K)|C` for every complete unit `C`.

*Sketch.* Induction in topological order.
- Let `C` be non-exposed. Every unit reached from `C` by a strict edge must be complete: otherwise it is in `Inc` or reaches `Inc`, and `C` would be exposed. Its `E` therefore equals `PM`.
- Every unit reached positively is itself non-exposed, since an exposing path from it would extend to one from `C`. By induction it is sound.
- Every rule instance fired in `C` thus uses positive facts that are true in `PM` and negative or aggregate conditions that agree with `PM`. Since `PM` is closed under the rules, the head is in `PM`.
- For a complete unit, all inputs equal `PM`, and a reached fixpoint of a fair evaluation is the least fixpoint `M_i` of §4.2 restricted to `C`.

Refinement, not required in v1 (OP-10): a positive `#count{…} >= k` or a `#sum` of non-negative values `>= k` is monotone, and could be treated as a positive edge.

### 6.3 Statuses

The status of a result (query answer set, entailment, materialised unit or constraint check) is computed from the **cone** of its node, i.e. the units it reaches:

| Status | Condition | Meaning for returned answers | Meaning for absent answers |
|---|---|---|---|
| **COMPLETE-STATIC** | node complete, and every unit of the cone statically certified terminating (§7), or answered by a stored bounded rewriting (§8.3) | all true | all false |
| **COMPLETE-DYNAMIC** | node complete, not all units certified | all true | all false |
| **NOT-GUARANTEED** | node sound-partial | all true (sound) | unknown ("completeness not guaranteed") |
| **UNKNOWN** | node exposed | none returned (N1) | unknown ("soundness cannot be guaranteed") |

Additional rules:
- For a Boolean query or entailment: `true` under NOT-GUARANTEED is certain, and `false` under NOT-GUARANTEED is reported as `unknown`.
- A **query containing negation** or an aggregate is itself a node with strict edges. It is therefore UNKNOWN whenever a negated or aggregated predicate is incomplete, even if the query's positive part is fine.
- **Constraint checking**: a reported violation is certain iff its constraint node is not exposed. The claim "no violation" requires a complete constraint node.
- An UNKNOWN result must name the blocking units (the incomplete units at the end of the exposing paths) and the strict edge involved. This makes the diagnosis explainable, e.g. "`externalContact` negates `employee`, whose SCC hit the term-depth budget".
- COMPLETE-STATIC implies that a fixpoint was actually reached. A statically certified unit that still exhausts a wall-clock budget is not complete.

### 6.4 Budgets

A **budget** is a tuple of limits, each checked per unit:
- `rounds`: semi-naive rounds per unit;
- `depth`: maximal term depth of derived facts;
- `facts`: number of derived facts;
- `digits`: significant digits of a decimal (§4.3);
- `time`: wall-clock time;
- for goal-directed strategies, `subgoals` (number of tables).

Semantics:
- **Cutting.** A derivation whose result would exceed `depth` or `digits` is **not performed**. The unit is then marked `fix = not reached`, even if saturation is observed afterwards. Exhausting `rounds`, `facts`, `time` or `subgoals` stops the unit with `fix = not reached`.
- **Continuation.** After a unit stops, evaluation of **other** units continues:
  - non-exposed units are evaluated normally;
  - exposed units are skipped, or evaluated only for preview (N1).
- **Soundness.** Every budget only removes derivations, so Proposition 3 applies unchanged.
- **Determinism.** With only `rounds`, `depth`, `facts` and `digits` as budgets, and breadth-first (fair) semi-naive evaluation, results are deterministic. `time` is a safety net: results remain sound, but the returned set may vary between runs. Conformance tests must use deterministic budgets.
- **Fairness requirement.** Within a unit, evaluation must be fair: every fact of `PM|C` is derived after finitely many rounds, if no budget cuts it. Semi-naive breadth-first evaluation is fair. This gives "complete in the limit".

---

## 7. Termination analysis

### 7.1 What is certified

A unit `C` is **statically certified** if its **cone program** terminates for every finite function-free `D`. The cone program consists of the rules of `lb(K)` whose heads are in `C` or in units reached from `C`. Certification is done on an **abstraction**, because the critical-instance argument fails with negation and arithmetic ([09 §2](09-skolem-function-frameworks.md), last row).

**Definition (abstraction `P^abs`).** From the cone program:
1. delete all negated atoms and all comparisons;
2. replace every assignment `Z = e` by substituting `Z` with a fresh **free** term `op_e(x̄)`, where `x̄` are the variables of `e`. This treats arithmetic as value invention.
3. replace every aggregate atom by a positive atom over a fresh predicate that holds the group key and the result. Its rule is non-recursive by §3.2, and its result position is fed by a free term over the group key.

**Proposition 4 (sketch).** If `LHM(P^abs ∪ D)` is finite for every finite function-free `D`, then every unit of the cone reaches its fixpoint in finitely many rounds, for every finite function-free `D`.

*Sketch.*
- Deleting negative literals and filters only enables more rule instances ([09 §5.1](09-skolem-function-frameworks.md)).
- Evaluating the free arithmetic terms to their values is a map from the terms of `P^abs` onto the values of `K`, preserving derivations. The evaluated model is the image of a finite set, restricted further by partiality.
- Each unit's computation over finite inputs is thus bounded by the finite abstract model.

### 7.2 Portfolio

The criteria are run on `P^abs` of each unit's cone, **cheapest first**, stopping at the first success.

| Order | Criterion | Adaptation to F2 | Cost |
|---|---|---|---|
| 0 | **Term-free unit**: no rule in `C` has a head term that is not a variable or constant occurring in the body (no invention, no assignment in `C`) | Finite given finite inputs (plain Datalog) | linear |
| 1 | **Weak acyclicity (WA)** on positions | Special edges from the positions of the argument variables of `f(t̄)` or `op_e(x̄)` to the head position holding it. Position-based, so no pooling is needed | PTIME |
| 2 | **Joint acyclicity (JA)** | One pseudo-existential per **function symbol** (pooled over all its occurrences), not per rule ([09 §3](09-skolem-function-frameworks.md), Prop. 2) | PTIME |
| 3 | **Argument-restricted (AR)** | Native (LP with functions) | PTIME [U] |
| 4 | **Γ-acyclicity** (Calautti et al. 2015) | Native | PTIME [U] |
| 5 | **MSA** | One abstraction constant per function symbol | EXPTIME, under an analysis budget |
| 6 | **MFA** | Skolem chase of `P^abs` on the critical instance; fails iff a cyclic term appears. Named symbols are used as-is | 2EXPTIME, under an analysis budget |

Notes:
- The critical-instance property holds for `P^abs`: it is positive, built-in-free and has function-free data ([09 §2](09-skolem-function-frameworks.md), [U-own]). This is why the abstraction is applied first, and why OP-4 (function-free facts) matters.
- The inclusions WA ⊆ JA ⊆ MSA ⊆ MFA are expected to hold for pooled named functions as for existential rules [U-own]. AR and Γ-acyclicity are incomparable to them in general, so they are tried as well.
- A successful MFA run yields a **checkable certificate**: the finite critical-instance chase ([09 §7, item 10](09-skolem-function-frameworks.md)).
- **Not in the v1 static portfolio**:
  - RJA/RMFA: different chase semantics, applicable only via `T(P)` under lookup ([09 §3](09-skolem-function-frameworks.md));
  - FDNC and finitary or finitely recursive recognition: goal-directed decidability. Recognition of finitary programs is undecidable, and FDNC recognition is a research item (OP-15).

  For goal-directed cases, v1 relies on **dynamic** completion of tabled evaluation (§8.2).
- **Data-dependent termination** (E3-ex under lookup when all chains close) is not certified statically. It surfaces as COMPLETE-DYNAMIC. Conditional certificates ("terminates whenever `D` satisfies constraint χ") are future work ([09 §7, item 4](09-skolem-function-frameworks.md)).

### 7.3 Labelling and output

Each unit gets one of two labels:
- `TERMINATES(criterion, certificate?)`;
- `NOT-CERTIFIED(criteria tried, witness)`. The witness is the offending cycle, given as the special-edge cycle of positions and the chain of **original** rules, plus the cyclic term found by MFA if one was found (e.g. `manager(manager(X))`).

A cyclic MFA term is **evidence, not proof**, of non-termination. The label says "may not terminate on some data".

Labels are computed per SCC in topological order and aggregated per stratum and per program. They are recomputed whenever rules or declarations change. The analyser output is part of every result trace (E2/E7).

---

## 8. Strategies (E10, D5)

All strategies compute answers of the same `PM(K)` and obey §6: units, statuses and rule N1.

### 8.1 Materialisation (forward chaining)

- **Definition**: stratum-by-stratum, SCC-by-SCC semi-naive evaluation of `lb(K)` (§4.2), with functional terms hash-consed. It is incremental-friendly (FBF/DRed on Datalog with terms, [07 §7, item 18](07-sota-theory.md)).
- **Applicability**: always.
- **Complete** on a unit iff its fixpoint is reached (COMPLETE-DYNAMIC). This is predicted when the cone is certified (COMPLETE-STATIC).

### 8.2 Dynamic backward chaining

- **Definition**: goal-directed tabled evaluation of `lb(K)` for the query's goals, using SLD resolution with syntactic term unification (occurs check), SLG-style tables and a sideways information passing strategy.
- **Negation**: a negated subgoal `not q(t̄)` may be resolved only after the table of `q(t̄)` is **completed** (stratified completion). Otherwise the goal's unit is exposed.
- **Complete** for a query iff all tables it depends on are completed. This can happen even when `PM(K)` is infinite, because only the relevant part is explored. This is the dynamic counterpart of the finitary and finitely recursive classes (§10, E3-ex).
- **Magic-set rewriting** may be used instead, within a stratum, as long as the transformed program stays stratified.

### 8.3 A-priori rewriting of pre-registered queries

- **Definition**: for a pre-registered query `Q`, compute a **rewriting** `R_Q`. This is a finite UNCQ over **base predicates** such that `Ans(Q, K) = eval(R_Q, D ∪ B)` for every `D`, where `B` is the materialised extension of the base predicates.
  - Base predicates are those with facts only, plus, in hybrid mode, materialised predicates.
  - The rewriting is computed by exhaustive unfolding with SLD resolution on `lb(K)`. Function terms unify structurally, and no piece-unifiers are needed because F2 has no existential variables. It saturates up to CQ subsumption (homomorphism check).
- **Pruning**: a CQ is dropped when:
  - it contains a functional term in an atom over a predicate that has no rules, since facts are function-free (OP-4);
  - in mode `constants`, it binds an answer variable to a functional term.
- **Useful only if bounded.** If saturation does not finish within the analysis budget, no rewriting is stored and the query falls back to §8.1/§8.2. A bounded rewriting is a static proof, hence COMPLETE-STATIC.

### 8.4 Rewriting under negation: the hybrid strategy (D5)

**Definition.** Let `Q` be a query, and let `N(Q)` be the set of predicates that `Q` reaches through a strict edge (negated, aggregated, or `$D_f` through lookup).
1. Every predicate in `N(Q)`, together with its cone, is **materialised** by the chase (§8.1).
2. `Q` is rewritten **stratum by stratum**:
   - positive atoms over predicates outside the materialised part are unfolded;
   - negated atoms and aggregates are kept as literals, to be evaluated against the materialised lower strata;
   - positive atoms over materialised predicates may be kept as base atoms.

**Proposition 6 (sketch).** If every unit in the cones of `N(Q)` is complete, the hybrid rewriting evaluated on `D ∪ E(materialised part)` returns `Ans(Q,K)`. If some are incomplete, §6 applies unchanged: materialised units are units, and `Q` is exposed.

*Sketch.* Materialised strict inputs equal `PM`, so negation and aggregates are fixed relations. The remaining unfolding is a positive-Datalog rewriting relative to them, which is correct by the correctness of SLD resolution for definite programs.

**v1 guard (D5).**
- *Pre-computed* (stored) rewritings are allowed **only for queries whose node reaches no strict edge** in `G(K)`, directly or transitively.
- **[choice]** The guard counts aggregate edges and lookup-induced edges as "negation", since both are non-monotone (OP-16).
- Queries outside the guard use §8.1, §8.2 or the hybrid strategy computed at query time.

**Invalidation.**
- Each stored `R_Q` carries a fingerprint of the rules and declarations (functions and lookups) in `Q`'s cone in `lb(K)`. Any change to them invalidates `R_Q`. v1 may simply invalidate on **any** rule or declaration change.
- Changes to data, FDs, `@type` or other constraints do not invalidate `R_Q`: they do not change `PM` on non-`$viol` predicates. In hybrid mode, the materialised part is maintained incrementally instead.

### 8.5 Combinations and default selection policy

Selection is **per query and per unit**, recorded in the trace. The default policy is owner-tunable:
1. If `Q` has a valid stored bounded rewriting, evaluate it: COMPLETE-STATIC.
2. Otherwise, if the cone of `Q` is certified, use incremental materialisation: COMPLETE-STATIC.
3. Otherwise:
   - materialise the certified units of the cone;
   - for the rest, run tabled backward chaining under budget;
   - if all tables complete, the status is COMPLETE-DYNAMIC;
   - otherwise, materialise under budget and return NOT-GUARANTEED or UNKNOWN per §6.
4. Any strategy may be run in parallel as a cross-check in test mode. On a complete unit, all strategies must agree.

---

## 9. Relationship with existential rules (D3)

### 9.1 The function-graph translation `T(P)`

This is defined on the positive part of `lb(K)`: rules without negation, aggregates or arithmetic. For each `f ∈ Fun`, a fresh predicate `F_f/(ar(f)+1)` is introduced. Each rule is flattened innermost-first ([09 §1.2](09-skolem-function-frameworks.md)):
- a head term `f(t̄)` yields the existence rule `B → ∃Y F_f(t̄, Y)` and the use rule `B ∧ F_f(t̄, Y) → H[Y]`;
- a body term `f(t̄)` becomes `Y` plus the body atom `F_f(t̄, Y)`.

For a lookup-declared `f`, the lookup variant `r_look` is kept as is (it has no `f` term), `r_inv` is translated without its `not $D_f` literal, and a **bridge rule** `λ_f → F_f(x̄, y)` is added. The implicit key `κ_f` on `F_f` is harmless ([09 §1.2](09-skolem-function-frameworks.md), Prop. 1).

**Proposition 5 (sketch).** Let `K` be such that:
- `lb(K)` has no negation other than the `not $D_f` literals introduced by lookup;
- there are no aggregates and no arithmetic;
- facts and queries are function-free.

Then for every (U)CQ `Q` in mode `constants`, `Ans(Q, K)` equals the certain answers of `Q` over `T(K) ∪ D`.

*Sketch.*
- Without lookup, this is Proposition 1 of report 09.
- With lookup, the Datalog-first restricted chase of `T(K) ∪ D` fires the bridge first. The existence rule then fires only for unrecorded tuples, which reproduces `PM(K)` up to renaming `f(s̄) ↦` null (Prop. 2.3).
- Any other chase result `J` (e.g. oblivious, with extra null witnesses for recorded tuples) maps homomorphically into this model while fixing constants: map each extra witness to the recorded value, which is available because `F_f(s̄, v)` holds by the bridge. Hence constant answers coincide.
- FDs and other constraints do not change `PM`, so they do not affect this statement. Only their violations differ, and they are checked by the second oracle.

### 9.2 Graal as oracle

| Fragment | Graal status | Procedure |
|---|---|---|
| **EXACT**: the conditions of Prop. 5, mode `constants`. Negation or aggregation are allowed only in strata *above* the tested query's cone | exact oracle, where Graal terminates | feed `T(K)` with bridge rules (shared functions), or the plain existential reading (rule-local functions); compare constant answers |
| **LOWER-BOUND**: positive, with shared functions or lookup, where `T(K)` is not used | Graal answers ⊆ engine answers | per-rule existential reading `Σ_local` ([09 §1.4](09-skolem-function-frameworks.md)) |
| **NONE**: negation or aggregates in the query's cone over invented terms, arithmetic, decimals, answers containing functional terms, function terms in facts, violations | no Graal oracle | clingo / DLV with native function symbols; decimals scaled to integers; `lb(K)` translated verbatim |

---

## 10. Worked examples

Provisional syntax. `%` starts a comment.

### 10.1 E1-ex: default and exception

```
contract(c1). contract(c2). contract(c3).
customerOf(c2, k7). frameworkAgreement(k7).
derogation(c3).

specificConditionApplies(C) :- contract(C), customerOf(C, K), frameworkAgreement(K).
specificConditionApplies(C) :- derogation(C).
generalConditionsApply(C)   :- contract(C), not specificConditionApplies(C).

% variant creating an object in the default branch
@function generalTerms/1.
appliedTerms(C, generalTerms(C)) :- generalConditionsApply(C).
```

- **Strata**:
  - 0: facts;
  - 1: `specificConditionApplies`;
  - 2: `generalConditionsApply` (strict edge to stratum 1);
  - 3: `appliedTerms`.
- **Perfect model**: `specificConditionApplies(c2)`, `specificConditionApplies(c3)`, `generalConditionsApply(c1)`, `appliedTerms(c1, generalTerms(c1))`.
- **Analyser**: all units are term-free (criterion 0), except `appliedTerms`, which is non-recursive and WA. Certified.
- **Status**: `?(C) :- generalConditionsApply(C).` → `{c1}`, COMPLETE-STATIC. `?(C,T) :- appliedTerms(C,T).` → `{(c1, generalTerms(c1))}` in mode `all`, and `{}` in mode `constants`.
- **Rewriting**: `generalConditionsApply` reaches a negative edge, so no stored rewriting (D5 guard). The hybrid strategy materialises `specificConditionApplies` and rewrites the query to `contract(C), not specificConditionApplies(C)`.

### 10.2 E2-ex: threshold on a sum, exact decimals

```
@type price(symbol, decimal).  @type inBasket(symbol, symbol, integer).
basket(b1). basket(b2). basket(b3). basket(b4). basket(b5).
price(i1, 75.50). price(i2, 60.00). price(i3, 66.67). price(i4, 40.00).
inBasket(b1, i1, 2). inBasket(b1, i2, 1).     % 151.00 + 60.00 = 211.00
inBasket(b2, i1, 1).                          % 75.50
inBasket(b4, i3, 3).                          % 200.01
inBasket(b5, i4, 5).                          % 200.00, boundary
                                              % b3: empty basket

total(B, S)     :- basket(B), S = #sum{ P*Q, I : inBasket(B, I, Q), price(I, P) }.
freeDelivery(B) :- total(B, S), S > 200.00.
```

- **Strata**:
  - 0: facts;
  - 1: `total` (aggregate edge; grounded groups through `basket(B)`);
  - 2: `freeDelivery`.
- **Perfect model**: `total(b1, 211)`, `total(b2, 75.5)`, `total(b3, 0)`, `total(b4, 200.01)`, `total(b5, 200)`; `freeDelivery(b1)`, `freeDelivery(b4)`.
  - `b5` is not free: `200.00 > 200.00` is false exactly.
  - `b3` has sum `0`, because the group is grounded.
  - `b1` shows why tuple keys are needed: were two items of `b1` both at `P*Q = 60.00`, keying on `I` keeps both.
- **Analyser**: no recursion, so all units certified (criterion 0). The assignment inside the aggregate lies in a non-recursive unit.
- **Status**: `?(B) :- freeDelivery(B).` → `{b1, b4}`, COMPLETE-STATIC. No stored rewriting (aggregate edge, OP-16).
- **Oracle**: clingo with prices in cents (7550, …).

### 10.3 E3-ex: the line manager, with lookup-before-invent

```
@function manager/1.
@lookup manager(X) = Y :- recordedManager(X, Y).
@fd hasManager: 1 -> 2.

employee(paul). employee(alice). employee(dave).
recordedManager(paul, alice).

hasManager(X, manager(X)) :- employee(X).                          % r1
```

- **`lb(K)`**:
  - `$L_manager(X,Y) :- recordedManager(X,Y).`
  - `$D_manager(X) :- $L_manager(X,_).`
  - `r1.look: hasManager(X,Y) :- employee(X), $L_manager(X,Y).`
  - `r1.inv:  hasManager(X, manager(X)) :- employee(X), not $D_manager(X).`
- **Perfect model**: `hasManager(paul, alice)`, `hasManager(alice, manager(alice))`, `hasManager(dave, manager(dave))`. No `manager(paul)` is invented (Prop. 2.1). There are no violations.
- **Analyser**: `hasManager` is non-recursive, WA. Certified. `?(X,M) :- hasManager(X,M).` → the 3 tuples above, COMPLETE-STATIC.
- **D2 conflict**: add `recordedManager(paul, bob).` Then `hasManager(paul, alice)` and `hasManager(paul, bob)` both hold. Two violations are reported: of the lookup functionality constraint, and of `@fd hasManager: 1 -> 2` with witness `(paul, alice, bob)`. There is no merging and no inconsistency explosion.
- **Rejected variant**: `@lookup manager(X) = Y :- hasManager(X, Y).` is non-stratifiable (§3.3). The error names the cycle `hasManager —lookup→ $D_manager → hasManager` and suggests `recordedManager` or `hasManager@db`.
- **O0 contrast** (no `@lookup`, data fact `hasManager(paul, alice)`): the model contains both `hasManager(paul, alice)` and `hasManager(paul, manager(paul))`, and the FD reports the conflict.
- **Stored rewriting** (O0 contrast only): `@query mgrOfPaul ?(M) :- hasManager(paul, M).` passes the guard, because O0 introduces no strict edge. It yields the bounded rewriting `{ ?(M) :- hasManager(paul,M) [facts] ; ?(manager(paul)) :- employee(paul) }`. The lookup version is excluded by the guard, since it reaches `$D_manager` negatively.

### 10.4 E3-ex trap: "every manager is an employee"

Add to 10.3 (with lookup, without `bob`):

```
recordedManager(alice, ceo). recordedManager(ceo, ceo).
person(bob). person(paul).

employee(Y) :- hasManager(X, Y).                                   % r2
externalContact(P) :- person(P), not employee(P).                  % r3
headcount(N) :- N = #count{ X : employee(X) }.                      % r4
```

**Strata**:
- 0: facts, `$L_manager`, `$D_manager`;
- 1: SCC `{employee, hasManager}`, positive cycle `r1.* ↔ r2`, strict edge only to `$D_manager`;
- 2: `externalContact` (negative edge to 1), `headcount` (aggregate edge to 1), FD constraints.

The program is stratifiable: the lookup source `recordedManager` does not depend on `manager`.

**Analyser output (provisional format):**

```
unit S1 = {employee, hasManager}   NOT-CERTIFIED
  tried: term-free ✗, WA ✗, JA ✗, AR ✗, Γ-acyclic ✗, MSA ✗, MFA ✗ (cyclic term)
  witness: special-edge cycle hasManager[2] -r2-> employee[1] -r1.inv-> hasManager[2]
           rules r1 (via lookup-invent branch), r2; MFA cyclic term manager(manager(*))
  note: static analysis ignores 'not $D_manager' (abstraction); termination is data-dependent
unit S2a = {externalContact}   term-free, but cone contains S1  -> not certified
unit S2b = {headcount}         term-free, but cone contains S1  -> not certified
```

**Run 1: data where every chain closes** (without `employee(dave)`):
- `S1` reaches its fixpoint: `hasManager(paul,alice)`, `hasManager(alice,ceo)`, `hasManager(ceo,ceo)`, `employee ⊇ {paul, alice, ceo}`.
- All queries are COMPLETE-DYNAMIC: `externalContact = {bob}`, `headcount(3)`.

**Run 2: with `employee(dave)`**, materialisation with budget `depth = 3`:
- `S1` derives:
  - `hasManager(dave, manager(dave))`, `employee(manager(dave))`;
  - `hasManager(manager(dave), manager²(dave))`, `employee(manager²(dave))`;
  - `hasManager(manager²(dave), manager³(dave))`, `employee(manager³(dave))`;
  - plus the closed chain from paul.
- The next derivation (depth 4) is cut, so `fix(S1) = not reached` and `S1 ∈ Inc`.
- Results:

| Query | Strategy | Returned | Status |
|---|---|---|---|
| `?(X,Y) :- hasManager(X,Y).` | materialisation | 6 tuples (3 closed-chain + 3 dave-chain) | NOT-GUARANTEED (sound-partial: positive path to `S1`) |
| `?(X) :- employee(X).` mode `constants` | materialisation | `{paul, alice, ceo, dave}` | NOT-GUARANTEED (in fact complete; see OP-14) |
| `?(P) :- externalContact(P).` | materialisation | none | UNKNOWN: negates `employee`, whose unit `S1` hit budget `depth` |
| same | backward chaining (§8.2) | `{bob}` | COMPLETE-DYNAMIC |
| `?(N) :- headcount(N).` | any | none | UNKNOWN (aggregate over `S1`; in fact the collection is infinite, so there is no value) |
| constraint `@fd hasManager` | materialisation | no violation found | NOT-GUARANTEED ("no violation" not established) |

Why backward chaining completes `externalContact`:
- the goal `employee(bob)` calls `hasManager(X, bob)`;
- `r1.inv` fails, because `manager(X)` does not unify with `bob`;
- `r1.look` with `recordedManager(X, bob)` bound first has no solution;
- so the table of `employee(bob)` is completed with no answer, and `not employee(bob)` is decided;
- `employee(paul)` is a fact.

This illustrates the combination policy (§8.5): the materialisation status is UNKNOWN, while the goal-directed status is complete although `PM(K)` is infinite.

**Oracles**:
- Graal on `T(K)` plus the bridge (EXACT fragment for `hasManager`/`employee` in mode `constants`). It agrees on Run 1. On Run 2 its restricted chase does not terminate either, so only the lower-bound check on budgeted outputs is possible.
- clingo does not terminate on Run 2 (infinite grounding), as expected.

---

## 11. Open points requiring owner validation

| # | Point | Options | Recommended default |
|---|---|---|---|
| OP-1 | Functional terms in answers (§5.2) | (a) returned as structured terms; (b) constants only; (c) mode per query | **(c)**, default mode `all` (a); `constants` for oracle tests and pruning |
| OP-2 | Lookup source (§1.5) | (a) arbitrary conjunction, stratification-checked; (b) data only; (c) (a) plus `p@db` sugar (facts of `p` in `D`) | **(c)**: lets E3-ex write `@lookup manager(X)=Y :- hasManager@db(X,Y)` without a second predicate |
| OP-3 | Several recorded values for `f(s̄)` (D2 conflict) | (a) use all values and report; (b) use none (invent) and report; (c) reject the KB | **(a)**: sound w.r.t. the data, visible through violations, no explosion |
| OP-4 | Functional terms in facts | (a) forbidden in v1; (b) allowed for functions without lookup | **(a)**: keeps the critical-instance property (§7), rewriting pruning (§8.3) and Graal comparison simple |
| OP-5 | Numeric identity and presentation | (a) `integer ⊂ decimal`, value identity (`2 = 2.00`), canonical output; (b) scale-preserving terms; (c) (a) plus a declared presentation scale `@type p(decimal(2))` | **(a)** now, **(c)** later |
| OP-6 | Division | (a) partial exact `/` plus explicit `div(…, s, mode)`; (b) rationals as value space; (c) evaluation error aborts the run | **(a)**: exact, never silently rounded, with a diagnostic on undefined instances |
| OP-7 | Default rounding mode | `half_up` (away from zero) / `half_even` | **`half_up`**: matches business expectations; `half_even` available explicitly |
| OP-8 | Numeric limit | digits budget; default value | budget `digits = 1000` significant digits, overflow = budget exhaustion (never a value) |
| OP-9 | Empty groups (§4.4) | grounded groups exist (`count = sum = 0`, no min/max) vs SQL-only groups | **both readings**, chosen syntactically by where group-by variables are bound |
| OP-10 | Granularity of the soundness rule (§6) | (a) unit-level (predicate/SCC, table); (b) answer-level via provenance (an answer is sound if some derivation avoids exposed checks); (c) plus monotone-aggregate edges treated as positive | **(a)** in v1; (b) and (c) as later refinements |
| OP-11 | What UNKNOWN returns | (a) nothing, plus blocking units; (b) a separately flagged "unverified preview" on explicit request | **(a)**, with (b) as an opt-in debug mode, never mixed with sound results |
| OP-12 | Typing | soft typing (static rejection of certain errors, `@type` as constraints) / strict typing | **soft typing** |
| OP-13 | Labelled nulls in F2 input | reject at load / accept as opaque constants | **reject** (they have no F2 semantics; kept for F3) |
| OP-14 | Constant-answer pruning analysis (E3-ex Run 2: the constant part is finite and computed, but not known to be) | v1 none / term-flow analysis proving that function terms cannot flow back into constant positions | **none in v1**; research item |
| OP-15 | Goal-directed decidability classes (FDNC, finitary, finitely recursive) | static recognition in v1 / dynamic tabling completion only | **dynamic only** in v1; FDNC-shape recogniser later |
| OP-16 | Scope of the D5 guard | strict edges = negation only / negation + aggregates / + lookup-induced negation | **all three** (conservative). Consequence: no stored rewriting for queries touching a lookup-declared function; they use the hybrid strategy at query time |
| OP-17 | Hybrid rewriting (§8.4) in v1 | implement / defer (materialisation + backward chaining cover these queries) | **defer to v1.1**; the definition is fixed now so that tests can be written |
| OP-18 | Invalidation granularity | any rule change / cone fingerprint | **any rule or declaration change** in v1; cone fingerprint later |
| OP-19 | Default budgets | values for `rounds`, `depth`, `facts`, `time` | deterministic defaults `depth = 16` and `rounds = 10⁴`, plus `time` as a safety net; tests set budgets explicitly |
| OP-20 | General integrity constraints `! :- body` beyond FDs | include / FDs only | **include** (same machinery; FDs are sugar) |

**Points where the owner decisions interact non-trivially** (for validation):
- **D2 + D5**: lookup-before-invent is *defined* by a translation into stratified negation. Under the literal D5 guard, every query touching a lookup-declared function is excluded from stored rewriting, even when, as in §10.3, the negation is harmless for constant answers (Prop. 5). A refinement that excludes lookup-induced edges from the guard is possible, but needs its own proof (OP-16).
- **D2 + E3-ex**: the natural modelling `@lookup manager ← hasManager` is exactly the non-stratifiable case D2 asks to reject. Hence the `recordedManager` / `@db` pattern (OP-2).
- **E1 + negation**: soundness is guaranteed only through rule N1. The consequence is that, in practice, programs with a non-certified recursive SCC below a negation return UNKNOWN under materialisation, unless goal-directed evaluation completes.
