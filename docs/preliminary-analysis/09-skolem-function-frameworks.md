# 09: Named Skolem functions as the modelling primitive (theory survey)

> **Note (2026-10-01).** The example `employee(X) -> hasManager(X, manager(X))` used in this report is not the owner's actual E3-ex rule, which is `employee(x), not isCompanyDirector(x) -> exists y managerOf(y, x)`. See [README Corrections](README.md#corrections). The example remains a valid illustration of named Skolem functions.

Scope: the theoretical consequences of replacing existential variables by **explicit, named Skolem functions** shared across rules, for example

```
employee(X) -> hasManager(X, manager(X)).        % manager/1 is a named function
```

where two different derivations of `manager(Paul)` denote the same object. This report complements [07](07-sota-theory.md), which covers existential rules. The final section proposes candidate frameworks and open research questions.

Method: literature knowledge cross-checked by web search. arxiv.org, dblp.org, jair.org and vldb.org were blocked for direct fetching in this session, so verification relies on search-engine snippets of publisher, proceedings, DBLP and arXiv pages.

Status tags:
- **[V]**: bibliographic facts or statements checked in this session. Usually these are the title, authors, venue and a headline result.
- **[U]**: from memory, not re-checked.
- **[U-own]**: propositions formulated in this report. Each comes with a proof sketch, and needs a careful proof (and ideally a Lean check, see [07 §5](07-sota-theory.md)) before it is relied on.

Running examples:
- **E1 (default / exception).** `generalConditionsApply(C) :- contract(C), not specificConditionApplies(C).` This uses stratified negation as failure.
- **E2 (threshold on an aggregate).** `freeDelivery(B) :- total(B,S), S > 200.00.` with `total(B,S) :- S = #sum{ P*Q, I : inBasket(B,I,Q), price(I,P) }`. It combines aggregation, arithmetic and exact decimals.
- **E3 (every employee has a line manager).** `employee(X) -> hasManager(X, manager(X))`. The trap: add `hasManager(X,Y) -> employee(Y)` ("every manager is an employee").

Notation:
- `P` is a program with named functions.
- `Σ` is a set of existential rules (TGDs).
- `D` is a database: ground, function-free facts over constants unless stated otherwise.
- `LHM(P ∪ D)` is the least Herbrand model of a positive program.
- `sk(Σ)` is the Skolemisation of `Σ` with *rule-local* fresh function symbols whose arguments are the frontier.

---

## 1. Semantic relationships

### 1.1 Three readings of "every employee has a manager"

| Reading | Rule | Meaning |
|---|---|---|
| (a) existential | `employee(X) → ∃Y hasManager(X,Y)` | at least one manager, identity unknown |
| (b) rule-local Skolem | `employee(X) → hasManager(X, f_R(X))`, `f_R` fresh and private to rule R | a chosen manager, private to this rule |
| (c) named Skolem | `employee(X) → hasManager(X, manager(X))`, `manager/1` usable in other rules, data and queries | *the* manager: a term with a stable identity |

**(a) vs (b): Skolemisation is conservative [U, classical].**
- `sk(Σ) ⊨ Σ`, and every model of `Σ` can be expanded (by choosing witnesses) into a model of `sk(Σ)`. Hence `Σ` and `sk(Σ)` entail exactly the same sentences over sig(Σ).
- In particular, they have the same certain answers to (U)CQs over constants, for every `D` over sig(Σ).
- `sk(Σ)` is Horn with function symbols. `LHM(sk(Σ) ∪ D)` is exactly the result of the semi-oblivious (Skolem) chase, which is a universal model of `Σ ∪ D` (Marnette, PODS 2009 [U statement; cited as in 07]).
- The choice of Skolem arguments (frontier vs. all body variables) does not change certain answers: both are conservative. It does change chase size and termination: the frontier choice gives the semi-oblivious chase, the all-variables choice gives the oblivious chase.
- The equivalence needs `f_R` to be **fresh**: not in `D`, not in queries, not in any other rule.

**(b) vs (c): named functions are a strictly stronger theory [U-own, elementary].**
Take P = { r1: `a(X) → p(X, f(X))`, r2: `a(X) → q(X, f(X))` }. Its existential reading Σ = { `a(X) → ∃Y p(X,Y)`, `a(X) → ∃Y q(X,Y)` }.
- `P ⊨ Σ`.
- `P ∪ {a(c)} ⊨ ∃Y (p(c,Y) ∧ q(c,Y))`, but `Σ ∪ {a(c)}` does not entail it.

So **sharing a function symbol between rules equates existential witnesses**. It acts exactly like a key (functional dependency) on a hidden relation `F_f(args, value)`; see §1.2.

**Herbrand vs first-order reading of (c).** In classical FO, function symbols denote *total* functions that are not necessarily injective, and `manager(Paul) = Alice` is consistent. Under logic-programming (Herbrand) semantics, distinct ground terms denote distinct objects (Clark's equality theory: free constructors, unique names for terms) [U, classical]. For **positive** programs and CQs over constants the two readings agree, because `LHM` is a universal model for Horn clauses [U, classical]. They diverge as soon as negation, aggregation (counting) or equality is involved. §4 turns this into a design choice.

### 1.2 Compiling named functions into existential rules: the function-graph translation `T(P)`

For every named function `f/n`, introduce a fresh predicate `F_f/(n+1)`, the graph of `f`. Flatten every head term `f(t)` bottom-up:

```
rule  B(x) → H(x, f(t))               (t ⊆ vars(B), possibly nested terms flattened first)
T:    B(x) → ∃Y F_f(t, Y)             ("existence rule", identical head for every use of f)
      B(x) ∧ F_f(t, Y) → H(x, Y)      ("use rule", a full TGD)
```

Body occurrences `p(f(X))` become `p(Y) ∧ F_f(X,Y)`. The implicit functional dependency is the key EGD `κ_f: F_f(t,Y1) ∧ F_f(t,Y2) → Y1 = Y2`.

**Proposition 1 [U-own].** Let `P` be a safe positive program with named functions such that `D` and queries contain no function terms and no `F_f` atoms. Then:
1. Every fair restricted chase of `T(P) ∪ D` fires the existence rule of `f` at most once per argument tuple `t`, because only existence rules produce `F_f` atoms and once `F_f(t,·)` exists the head is satisfied. The result is therefore isomorphic to `LHM(P ∪ D)`, mapping `f(t) ↦` the unique `F_f(t,·)` witness.
2. Consequently `LHM(P ∪ D)` is a universal model of `T(P) ∪ D`. It satisfies every key `κ_f`. Since `Mod(T(P) ∪ κ ∪ D) ⊆ Mod(T(P) ∪ D)`, it is also universal for `T(P) ∪ κ ∪ D`.
3. Hence **for every CQ/UCQ over constants, `P`, `T(P)` and `T(P) ∪ κ` have the same certain answers**. The key is *harmless*: it is never needed for positive CQ answering.

Proof sketch: the use rules quantify over *all* `F_f`-successors. In any model of `T(P)`, pick one successor per tuple; the derivation of `P` replays on it. This is the separability intuition of Calì, Gottlob and Pieris (below) instantiated to single-head existence rules whose universal positions coincide with the key positions: their "non-conflicting" case (U = K) [U: exact statement to be checked against the AIJ 2012 definition].

**Consequences.**
- Named functions add no semantic power *beyond existential rules* for positive CQ answering. They add *identity management* (sharing) that existential rules can express only via the extra `F_f` predicates. This gives a clean bridge to all existential-rule theory (§3) and to Graal as an oracle (§1.4).
- The equivalence breaks when:
  1. `D` contains `F_f` facts or function terms (e.g. "manager(Paul) = Alice");
  2. users declare their own FDs/EGDs;
  3. negation or aggregation range over invented terms;
  4. equality is queried.

**Proposition 2: sharing can break termination [U-own].** Take P = r1, r2 above plus r3: `p(X,Y) ∧ q(X,Y) → a(Y)`.
- With named `f`: `a(c) ⇒ a(f(c)) ⇒ a(f(f(c))) ⇒ …`, which is infinite.
- With rule-local `f1`, `f2`: `p(c,f1(c))` and `q(c,f2(c))` do not join, so evaluation terminates.

Conversely, merging `f1, f2 ↦ f` is a term homomorphism that preserves every local derivation and term depth. So **termination of the named program implies termination of its rule-local version, but not the reverse**. Termination analyses must be run on the named program, or on a sound abstraction of it (§2, §3).

### 1.3 Relation to FDs, EGDs, keys, UNA and second-order dependencies

- A named function is a **key on its graph**: `F_f[1..n] → F_f[n+1]`. If users additionally declare `hasManager` functional, i.e. the FD `hasManager: 1 → 2`, then `hasManager(Paul,Alice)` from data and the derived `hasManager(Paul, manager(Paul))` force `manager(Paul) = Alice`. That is a genuine TGD+EGD interaction (§4).
- Implication for FDs + inclusion dependencies is undecidable (Mitchell, Information and Control 56(3), 1983; Chandra & Vardi, SIAM J. Comput. 14(3), 1985) [V]. Query answering under arbitrary TGDs + EGDs is undecidable, and EGDs break the decidability of guarded/warded/shy fragments in general [V: Bellomarini et al. 2022 abstract].
- **Unique Name Assumption (UNA).** Constants are pairwise distinct. An EGD equating two constants is a *hard violation* (inconsistency), as in the standard EGD chase which fails on constant clashes [U, classical: Fagin, Kolaitis, Miller, Popa 2005]. Under Herbrand semantics, UNA extends to function terms (`manager(Paul) ≠ Alice`). A declared FD then contradicts the invention unless the design says "lookup before invent" (§4, option O2).
- **Second-order tgds (SO tgds)** are the database-theory home of *named, shared Skolem functions with equalities between terms*. Fagin, Kolaitis, Popa and Tan, *Composing schema mappings: second-order dependencies to the rescue*, PODS 2004 / ACM TODS 30(4), 2005 [V]. Plain SO-tgds are studied by Arenas, Pérez, Reutter and Riveros, JCSS 79(6), 2013 [V]. SO tgds allow `∃f ∀x (φ → ψ)` with `f` shared across clauses and equalities `f(x) = g(y)` in bodies. They were studied for source-to-target mappings (non-recursive), where the chase trivially terminates.
- Recursive SO dependencies with equality are handled goal-directedly by Tsamoura & Motik, *Goal-driven query answering over first- and second-order dependencies with equality*, arXiv 2412.09125 [V title/authors; content U]. **This is the closest existing formal framework to "named functions + declared functional dependencies" and should be read in full before fixing the spec.**
- Industrial precedents for named or deterministic Skolem functions:
  - **Vadalog** Skolem functions are deterministic, injective and range-disjoint (different functions never produce the same null) [V: Vadalog handbook snippet]. This is exactly the free-constructor (Herbrand) reading.
  - **RDFox** `SKOLEM` built-in maps a tuple to a unique blank node; the docs recommend putting a type tag, e.g. `"Employment"`, as the first argument [V]. A type tag makes it a named function by convention.
  - **LogicBlox / LogiQL constructor predicates** are one-to-one functions (injections) from a key tuple to an entity. A value is created only if none exists for the key [V]. That is "lookup before invent" (option O2 in §4).
  - **Nemo** Skolem function symbols are identified per rule (rule, disjunct, existential variable, arity) [U: search snippet, attribution unclear], i.e. rule-local.

### 1.4 When Graal remains a valid test oracle

Graal reasons with existential rules (certain answers, chase/rewriting) and, to our knowledge, has no function terms in DLGP and no EGDs [U]. Its answers must coincide with the engine's on the fragment **ORACLE-EXACT** below.

**ORACLE-EXACT (by Prop. 1) [U-own].** A program qualifies when all of the following hold:
1. Rules are positive (no negation or aggregation), or negation and aggregation touch only constant (null-free) positions; this is Choice A of [07 §4.3], applied stratum-wise.
2. Named functions appear in heads. They may appear in bodies too, provided rules are safe.
3. `D` and queries are function-free, and contain no `F_f` predicates.
4. There are no user-declared FDs/EGDs and no equality atoms.
5. There is no arithmetic producing values in recursive positions. Non-recursive built-in filters over constants are fine if Graal supports them, which is [U].
6. Queries are CQs/UCQs, and answers are compared on **constant tuples only**; invented terms and nulls are filtered.

Test procedure:
- **(i) Rule-local functions** (each symbol in exactly one rule): feed the plain existential reading to Graal. If Graal's Skolem chase uses frontier arguments, even the *materialised models* coincide up to renaming `f_R(t) ↔ null`.
- **(ii) Shared functions**: feed `T(P)` to Graal. Certain answers must coincide. Any chase variant or rewriting in Graal is acceptable, since certain answers are chase-independent.

Outside ORACLE-EXACT:
- **Lower-bound oracle only.** If `P` shares functions, then `P ⊨ Σ_local`, where `Σ_local` is the naïve per-rule existential reading. Graal's answers on `Σ_local` must be a *subset* of the engine's answers. This is a useful completeness smoke test even when `T(P)` is not used.
- **No Graal oracle.** Negation or aggregation over invented terms, user FDs/equality, function terms in data, decimal aggregation, and queries returning invented terms. For these, use a **second oracle with native function symbols**: clingo, or DLV2 (which checks for finite groundedness). Both compute the perfect/stable model of stratified programs with uninterpreted function terms [U]. clingo aggregates are integer-only, so E2 must be tested with scaled integers (cents) [U]. Where `T(P)` plus Choice A applies, Nemo can serve as an oracle for the negation layer.

---

## 2. Logic programs and chase with function symbols: decidability and termination analyses

General fact: Horn programs with function symbols are Turing-complete. Finiteness of `LHM` and termination of bottom-up evaluation are undecidable. All usable classes are **sufficient conditions** [U, classical].

| Notion | Reference | Guarantees | Cost of check | Shared named functions? |
|---|---|---|---|---|
| Finitely ground (FG) | Calimeri, Cozza, Ianni, Leone, *Computable functions in ASP: theory and implementation*, ICLP 2008, LNCS 5366 [V] | Finite "intelligent" instantiation, hence finitely many finite answer sets, computable by DLV-style grounding | **Membership undecidable** [V] | Yes: arbitrary function symbols |
| Finitary programs | Bonatti, *Reasoning with infinite stable models*, AIJ 156(1), 2004 (+ erratum 2008); disjunctive extension [V] | Ground queries decidable, non-ground semi-decidable; handles *infinite* stable models; compactness [V] | Recognition undecidable [U] | Yes |
| Finitely recursive queries + magic sets | Alviano, Faber, Leone, *Disjunctive ASP with functions: decidable queries and effective computation*, TPLP 10(4–6), 2010 [V] | Query answering decidable for finitely recursive queries on disjunctive programs with stratified negation and functions, via magic sets [V] | query-dependent [U] | Yes |
| ω-restricted | Syrjänen, LPNMR 2001, LNCS 2173 [V] | Every variable bound by a domain predicate of a lower stratum, which gives finite grounding [V] | PTIME syntactic [U] | Yes, but very restrictive: function terms only over domain predicates |
| λ-restricted | Gebser, Schaub, Thiele (gringo), LPNMR 2007 [U] | Finite grounding via level mapping of predicates [U] | PTIME [U] | Yes |
| Argument-restricted (AR) | Lierler & Lifschitz, *One more decidable class of finitely ground programs*, ICLP 2009, LNCS 5649 [V] | Subclass of FG; contains finite-domain, ω- and λ-restricted programs [V]. Term depth per argument bounded via an argument ranking | polynomial [U] | Yes |
| FDNC | Eiter & Šimkus, LPAR 2007; ACM TOCL 11(2), 2010 [V] | Decidable reasoning with **infinite** models: unary/binary predicates, unary functions, forest-shaped rules; consistency and brave/cautious reasoning EXPTIME-complete [V] | syntactic | Yes; tailored to "successor-like" unary functions such as `manager(X)`. E3 is essentially FDNC-shaped [U-own] |
| Bidirectional / BD programs | Eiter & Šimkus, IJCAI 2009 [U] | Decidable, with functions used forward and backward [U] | syntactic | Yes |
| Stratification-based and local stratification (LS) for chase | Greco, Spezzano, Trubitsyna, PVLDB 4(11), 2011 [V] | Chase termination; LS generalises super-weak acyclicity and stratification criteria [V] | polynomial [U] | Via Skolemised reading [U] |
| Γ-acyclicity, safety | Calautti, Greco, Spezzano, Trubitsyna, *Checking termination of bottom-up evaluation of logic programs with function symbols*, TPLP 2015 (arXiv 1407.2106) [V] | Termination of bottom-up evaluation. The propagation graph of complex terms gives Γ-acyclicity, which generalises most earlier criteria; the safety function analyses rule activation [V] | polynomial [U] | **Yes, native**: function symbols are ordinary LP functions |
| Adornment-based | *Logic programming with function symbols: checking termination of bottom-up evaluation through program adornments*, TPLP 2013 [V title; authors Greco, Molinaro, Trubitsyna U] | Termination via adorned program rewriting [V] | can be exponential [U] | Yes |
| Bounded programs | Greco, Molinaro, Trubitsyna, TPLP 13(4–5), 2013 / IJCAI 2013 [V] | Finite stable models of finite size [V] | decidable [U] | Yes |
| Size-based: atom sizes, linear constraints, polynomially bounded | Calautti, Greco, Molinaro, Trubitsyna, IJCAI 2015 (*Logic program termination analysis using atom sizes*) [V]; *Checking termination … through linear constraints*, 2014 [V title]; *Polynomially bounded logic programs with function symbols*, AAAI [V title; year U] | Head atom size bounded by body sizes via linear inequalities; the last also gives a polynomial bound on model size [V/U] | LP solving, PTIME [U] | Yes |
| Size-change termination (SCT) | Lee, Jones, Ben-Amram, POPL 2001 [V]; for TRS, e.g. Thiemann & Giesl [U] | No infinite descent, for top-down or functional termination [V]. **Not directly** for bottom-up growth: needs dualising (term *growth* along cycles) | **PSPACE-complete**, PTIME approximations [V] | Yes; term-rewriting/Prolog tools (AProVE, dependency pairs) address top-down termination [U] |
| WA / SWA / JA | Fagin et al. 2005; Marnette 2009; Krötzsch & Rudolph 2011 (see 07) | All-instance Skolem chase termination | PTIME | WA: yes (position-based, insensitive to function identity). JA/SWA: yes after merging the "movement" sets of all occurrences of the same function [U-own] |
| MSA / MFA | Cuenca Grau, Horrocks, Krötzsch, Kupke, Magka, Motik, Wang, JAIR 47:741–808, 2013 [V] | Skolem chase on the critical instance; MFA fails iff a cyclic term appears. Treats equality via singularisation [U] | MFA 2EXPTIME-c, MSA EXPTIME-c (07) [U] | **Yes, native**: MFA is defined on Skolemised programs, so shared symbols can be used as-is. Run on *named* P (Prop. 2) [U-own] |
| DMFA / RMFA / RJA, restricted cyclicity (RMFC) | Carral, Dragoste, Krötzsch, IJCAI 2017, pp. 922–928 [V]; Karimi, Zhang, You, TPLP 2021; DMFA [U] | Restricted / Datalog-first chase termination; RMFC proves non-termination [V] | 2EXPTIME-ish [U] | **Not directly**: named semantics has no "head already satisfied" check. They apply to the O2 "lookup-before-invent" semantics via `T(P)` [U-own] |
| EGD-aware termination | Calautti, Greco, Molinaro, Trubitsyna, *Exploiting equality generating dependencies in checking chase termination*, PVLDB 9(5), 2016 [V] | Uses EGDs to prove termination (merges stop growth) [V] | [U] | Relevant for O3 (§4) |
| R-acyclicity / R-stratification | Magka, Krötzsch, Horrocks, IJCAI 2013 [V] | Finiteness and uniqueness of stable models of Skolemised programs with negation [V/U] | [U] | Yes, via Skolemised reading |
| Semantic acyclicity (description graphs + LP) | Magka, Motik, Horrocks, ESWC 2012 [V] | Finite models for structured objects, with stratified negation [V] | [U] | Yes |
| Critical-instance characterisation | Marnette 2009 (see 07) | Skolem chase terminates on all instances iff it terminates on the critical instance | — | **Holds for named functions** in positive, function-free-data, built-in-free programs [U-own: any `D` maps to the critical instance and term homomorphisms preserve derivations and depth]. **Fails** with arithmetic built-ins, equality, or negation |

**Key take-away.** The LP-with-functions line (Calabria group: FG, AR, Γ-acyclicity, bounded, atom sizes) was designed for *arbitrary shared function symbols*. It is directly applicable to named functions, unlike some existential-rule notions tied to per-rule existential variables. MFA is the natural bridge: it is literally the Skolem chase on the critical instance, and it works unchanged with named symbols.

---

## 3. Which decidability classes transfer to named Skolem functions

Principle: via `T(P)` (Prop. 1), a named-function program is an existential rule set plus harmless keys. A class **transfers** when `T(P)` lies in it, or when its defining criterion can be restated on `P` directly. The flattening introduces `F_f` body atoms, so syntactic classes based on body shape (linear, guarded, sticky) may be lost.

| Class | Transfers? | Conditions | Reference |
|---|---|---|---|
| Datalog (no functions) | trivially | — | — |
| Non-recursive / acyclic GRD | **yes** | Compute the GRD on `P` with **term unification** (function terms unify only with the same symbol), or on `T(P)`. Sharing adds edges (joins on `f(t)` become possible, Prop. 2) | Baget et al. 2011 [U-own adaptation] |
| WA | **yes** | Special edges from argument positions of `f(t)` to the head position holding `f(t)`, pooled over all rules | Fagin et al. 2005 [U-own] |
| JA, SWA | **yes, with adaptation** | Pool the "movement" of all occurrences of the same named function (one pseudo-existential per symbol, not per rule) | Krötzsch & Rudolph 2011; Marnette 2009 [U-own] |
| MSA / MFA | **yes, native** | Run on named `P`: MSA uses one abstraction constant per function symbol, MFA uses cyclic-term detection. Not with arithmetic or EGDs; MFA+equality via singularisation is [U] | JAIR 2013 [V] |
| Γ-acyclic / AR / bounded / atom-size | **yes, native** | Designed for LP with functions; built-ins need care | §2 [V] |
| RJA / RMFA (restricted chase) | **no (different semantics)** | Apply to `T(P)` only under the O2 "lookup-before-invent" semantics | Carral et al. 2017 [V]; [U-own] |
| FES (finite universal model) | **replaced** | The relevant notion is finiteness of `LHM(P ∪ D)` for all D. It is equivalent to termination on the critical instance (positive, built-in-free), still undecidable | [U-own] |
| Linear | **partially** | Rule-local functions: yes (plain existential reading). Shared: `T(P)` use rules have bodies `B ∧ F_f`, so not linear. But the key is harmless (Prop. 1), so rewriting with piece-unifiers on `T(P)` remains complete. **FUS of `T(P)` is not guaranteed** [U-own; open, §7] | Calì, Gottlob, Lukasiewicz 2012 |
| Guarded / frontier-guarded | **partially** | Rule-local: yes. Shared: the use rule is guarded if some atom of `B` contains `t` and `x`. `F_f(t,Y)` frontier-guards it iff `x ⊆ t` ("argument-guarded functions": the function takes *all* frontier variables as arguments) | Calì, Gottlob, Kifer 2008/2013 [U-own condition] |
| Sticky / weakly sticky | **unclear** | The join on `Y` through `F_f` may violate stickiness marking; needs case analysis | Calì, Gottlob, Pieris AIJ 193, 2012 [U] |
| Warded | **likely yes** | `F_f(t,Y)` is the ward carrying the "dangerous" variable `Y` when `t` contains the frontier's harmful variables. Vadalog already implements deterministic Skolem functions over warded programs | Arenas, Gottlob, Pieris 2014; Bellomarini et al. 2018 [U-own] |
| Shy | [U] | Parsimonious chase relies on homomorphism checks, which conflicts with named identity | Leone et al. 2012/2019 |
| FDNC | **direct analogue** | Unary named functions, binary/unary predicates, forest shape. EXPTIME-complete even with infinite models | Eiter & Šimkus 2010 [V] |
| Finitary / finitely recursive | **direct analogue** | Ground or goal-directed queries on infinite models (E3) | Bonatti 2004; Alviano et al. 2010 [V] |
| TGDs + **user** FDs/keys | **breaks in general** | Undecidable (FD+IND). Decidable sub-cases: non-conflicting / separable keys (Calì, Gottlob, Pieris, VLDB 2010 and RR 2010; AIJ 2012 [V/U]); **harmless EGDs** for warded, PTIME (Bellomarini, Benedetto, Brandetti, Sallinger, PVLDB 15(13), 2022 [V]) | §4 |

Why sharing sometimes breaks classes: a shared function is a key on `F_f`. This key is harmless by construction (Prop. 1), so **decidability of CQ answering never breaks by sharing alone**: it is still decidable whenever `T(P)` lies in a decidable class.

What breaks are the *syntactic recognisers*. Flattening adds a join `B ∧ F_f(t,Y)`, and sharing adds GRD edges between rules that were previously independent (Prop. 2). The truly undecidable interaction arises only with **user-declared** FDs, or with data asserting function values.

---

## 4. Equality: do named functions force equality reasoning?

Yes, as soon as a named function's value can also be given by data or constrained by a declared FD. Example: `hasManager(Paul, Alice)` is in `D`, and the user declares "each employee has one line manager". Four design options:

**O0: no FDs on function-valued relations.** Named functions are free constructors, with Vadalog-like injectivity and range-disjointness [V]. `hasManager(Paul,Alice)` and `hasManager(Paul, manager(Paul))` coexist.
- Cost: none (hash-consed term interning).
- Oracle: Graal via `T(P)` (§1.4).
- Downside: counter-intuitive for "the manager".

**O1: O0 plus integrity constraints.** Declared FDs are checked, not enforced: `hasManager(X,Y) ∧ hasManager(X,Z) ∧ Y≠Z → ⊥`, evaluated in a top stratum. A violation reports "invented manager conflicts with recorded manager Alice". This is the typical enterprise-validation behaviour.
- Cost: one stratified check.
- Recommended default together with O2.

**O2: lookup-before-invent (definitional / defaulted functions).** Declare a function's graph partly from data or Datalog: `manager(X) := Y if hasManager_rec(X,Y)`. Invention happens only when no defined value exists. The semantics is **stratified default negation**, i.e. an instance of E1:

```
definedMgr(X)                  :- hasManager_rec(X,_).
F_manager(X,Y)                 :- hasManager_rec(X,Y).
F_manager(X, manager(X))       :- employee(X), not definedMgr(X).
```

- This is exactly LogicBlox constructor behaviour [V]. It coincides with the Datalog-first restricted chase on `T(P)` when the definition stratum is below the invention stratum [U-own].
- Requirement: definitions must not depend (positively or negatively) on invented values of the same function. Check this by stratification of `definedMgr` below its own invention rule.
- **E3 benefit.** With complete data (every recorded employee has a recorded manager, the top manager records itself or `isTop`), evaluation terminates although plain Skolemisation does not (§5.3).

**O3: full equality (EGD chase with union-find and congruence).** Merge `manager(Paul) ≡ Alice`.
- Congruence: `a ≡ b ⇒ f(a) ≡ f(b)`, via congruence closure in O(n log n) (Downey, Sethi, Tarjan 1980; Nelson & Oppen 1980 [U]).
- Representative rewriting, as RDFox does for `owl:sameAs` (Motik, Nenov, Piro, Horrocks, AAAI 2015 [U title]; incremental version IJCAI 2015 [V in 07]).
- Singularisation for goal-directed answering (Benedikt, Motik, Tsamoura, AAAI 2018 [V]).
- **Constant clash under UNA** (`Alice ≡ Bob`): hard inconsistency. Alternatively, inconsistency-tolerant reporting with the conflicting derivations as explanation.
- **Injectivity** (free-constructor reading): `manager(a) ≡ manager(b) ⇒ a ≡ b`. This gives backward propagation of merges and extra clashes; it must be an explicit declaration.
- Complexity: data complexity stays PTIME when the chase terminates. Termination and decidability are lost in general (FD+IND, §1.3). Termination tools: Calautti et al. PVLDB 2016 [V]. Decidable fragment: warded + harmless EGDs, PTIME [V].
- Interaction with negation: merges are non-monotone for `not` and for aggregates (counts shrink). **Equality must be saturated within the positive part of a stratum before any negation or aggregation reads it.**

**Recommendation.**
- v1: **O0 + O1 + O2**. No equality reasoning is needed. Everything is expressible as stratified Datalog with function terms, and every construct has a known semantics.
- O3 is opt-in, restricted to declared keys, and flagged "decidability: EGD fragment". Report 07's decision 19 ("equality out of scope for v1") stays valid.

---

## 5. Negation (E1) and aggregation (E2) under Skolem-function semantics

### 5.1 Negation
- **Perfect-model semantics** of stratified programs with function symbols is well defined, even for infinite Herbrand models (Apt, Blair, Walker 1988; Przymusinski 1988) [U, classical]. Stratification is predicate-level, so function symbols do not affect it.
- Unlike existential rules ([07 §4.1](07-sota-theory.md): negation answers depend on the chosen universal model), **named functions give a canonical model**. Negation over invented objects is deterministic: `not hasManager(manager(Paul), _)` is a closed-world statement about the invented object.
- This is Choice B of report 07 made *principled*: the function names are part of the user's syntax, so "syntax dependence" is intended. The optimiser must then preserve the named terms themselves: it may not merge or split named functions.
- **E1** is plain stratified NAF. It only becomes interesting when the default creates objects, e.g. `appliedConditions(C, generalConditions(C)) :- contract(C), not specificApplies(C)`. That is fine: the invention stratum sits above the tested predicate.
- **Termination with negation (sufficient condition).** Deleting all negative literals gives `P⁺`. The perfect model of `P ∪ D` is contained in `LHM(P⁺ ∪ D)`, since deleting negative literals only enables rules. So **any termination certificate for `P⁺` (MFA, Γ-acyclicity, …) certifies `P`** [U-own, easy]. R-stratification/R-acyclicity (Magka et al. 2013 [V]) is finer.
- **Graal oracle.** Valid only if negation touches constant positions (Choice A), and then only for the positive strata.

### 5.2 Aggregation
- Stratified aggregation: Mumick, Pirahesh, Ramakrishnan 1990; ASP aggregates: Faber, Pfeifer, Leone AIJ 2011 (see 07).
- Aggregating over **sets containing function terms** is well defined under the free-constructor reading: `count{ M : hasManager(_,M) }` counts distinct terms. This contrasts with nulls under existential semantics ([07 §4.2](07-sota-theory.md)), where counts differ across universal models.
- Caveat: the count is correct only if the terms really denote distinct objects. Under O0/O1 they do by fiat. Under O3, count over equivalence classes, after saturation.
- **E2**:
  - Use tuple-keyed aggregation `#sum{ P*Q, I : … }` (ASP style), so that two items with equal price are not collapsed by set semantics [U: clingo semantics].
  - Totals are exact decimals, so `200.00` compares exactly. Use arbitrary-precision decimals, or rationals if division is allowed; ban binary floats in the core. Division needs an explicit rounding function with a stated scale and mode, because decimals are not closed under `/`.
  - E2 is non-recursive, hence trivially terminating.
- Recursive aggregates: stay stratified in v1. Monotonic aggregates (Ross & Sagiv 1997) and limit Datalog (Kaminski et al. IJCAI 2017/2018) are later extensions (07).

### 5.3 Arithmetic and value invention; E3 trap by framework
- Arithmetic built-ins are **value invention**: `p(X+1) :- p(X)` is the successor function. Engines with arithmetic functors are Turing-complete and may not terminate; Soufflé documents exactly this [V].
- Termination analyses must treat arithmetic outputs as terms of unbounded depth. Treat `+`, `*` and aggregate outputs as function symbols in the position graph, i.e. **special edges**. This is WA-style "arithmetic acyclicity": no cycle through an arithmetic-output position [U-own]. It is sound but coarse.
- The critical-instance argument fails with arithmetic, because homomorphisms do not commute with `+`. MFA must then be run with arithmetic abstracted as fresh function symbols [U-own].

**E3 across the approaches** (rules: `employee(X) → hasManager(X, manager(X))`, `hasManager(X,Y) → employee(Y)`):

| Approach | Behaviour | Detection / remedy |
|---|---|---|
| Existential + Skolem/semi-oblivious chase | infinite chain `f(f(…Paul))` | Not WA (special cycle), not MFA (cyclic term `f(f(x))`) [U-own check] |
| Existential + restricted chase | Terminates on data where every employee's manager is recorded and the chain closes, e.g. top manager records `hasManager(ceo,ceo)`. Otherwise infinite | RMFA on the critical instance: likely yes, since the critical instance satisfies the head [U]; data-dependent in general |
| Existential, any chase | The rule set is **linear**, hence FUS: rewrite queries; answers over constants are finite and decidable | Graal / PURE rewriting works [U-own: rules are linear] |
| Existential, core chase | No finite universal model (every finite model has a manager cycle, which cannot map into the acyclic infinite chain model) | Not FES [U-own] |
| Named functions (Herbrand) | `LHM` infinite: `employee(manager^k(Paul))` for all k | MFA/Γ-acyclicity fail. Program is FDNC-shaped and finitely recursive, so ground/goal-directed queries are decidable (Bonatti; Alviano et al.) [U-own classification] |
| Named + O2 lookup-before-invent | Terminates iff every chain reaches a recorded manager that closes the chain | Dynamic: completes when the fixpoint is reached; static only with data assumptions |
| Named + O3 FD merging | Same as O2 if merging happens before inventing; otherwise invent-then-merge | EGD-aware termination (PVLDB 2016) |
| Modelling fixes | (a) Restrict "every manager is an employee" to recorded persons: `hasManager(X,Y), person_rec(Y) → employee(Y)`, which makes it WA. (b) Top manager: `employee(X), not isTop(X) → …` does **not** help, since invented managers are never `isTop`; combine with O2. (c) Depth bound: sound, status NOT-GUARANTEED | Analyser should *explain* the cycle (show the Skolem term cycle `manager(manager(x))` and the rule path) |

---

## 6. Candidate frameworks

### F1: Existential rules + constant-guarded stratified negation + stratified aggregation (report 07's Choice A)
- **Syntax:** TGDs; stratified `not`; aggregates and built-ins; negated, aggregated and grouped variables bound to non-affected (null-free) positions.
- **Semantics:** FO models, certain answers; negation and aggregation evaluated over constants, so independent of the chase variant (07 §4.3).
- **Tasks:** CQ answering, entailment, rule-set equivalence (optimiser).
- **Decidable fragments:** the full existential-rule zoo (07 §1.3), plus all termination notions.
- **Open:** negation over nulls (core-model semantics), incremental restricted chase.
- **Examples:** E1 ✓. E2 ✓ (on constants). E3 natural (∃), trap solved by rewriting (linear/FUS), but "the manager" identity cannot be expressed.
- **Verdict:** most mature, Graal a direct oracle, but it does not express the user's intended identity semantics.

### F2: Stratified Datalog with named functions ("Datalog^F"), perfect-model semantics
- **Syntax:**
  - Safe rules with function terms over declared function symbols (signature, arity, optional type tag), stratified `not`, stratified aggregates, exact-decimal built-ins.
  - Declared function graphs with lookup-before-invent (O2).
  - Integrity constraints (O1).
  - No EGD enforcement.
- **Semantics:** the unique perfect model under free-constructor (Herbrand) reading; answers may contain named terms (`manager(Paul)`), which are meaningful identifiers.
- **Tasks:** model computation; (ground) query answering; constraint checking; explanation.
- **Decidable fragments:**
  - Static certificates (§2): WA, JA, MSA, MFA on `P⁺`, Γ-acyclicity, AR, bounded, atom-size, arithmetic acyclicity.
  - Goal-directed for infinite models: finitely recursive / FDNC with magic sets.
  - Otherwise budgeted and flagged.
- **Theoretical maturity:** high for the LP semantics (perfect models, ASP with functions); medium for combined analyses with arithmetic and O2.
- **Oracles:** clingo/DLV (exact, integer arithmetic) [U]; Graal via `T(P)` on ORACLE-EXACT (positive, constant answers).
- **Implementation complexity:** lowest. It is a semi-naive Datalog engine with interned terms. It is incremental-friendly (DRed/FBF apply to Datalog with function terms, 07 §6) and needs no chase variants or homomorphism checks for materialisation.
- **Weak spot:** CQ rewriting (FUS-style backward chaining) is not native. Use magic sets / tabling instead, or `T(P)` + piece-unifier rewriting.
- **Verdict:** best fit for E1–E3 as the user reads them (identity, determinism, closed world over invented objects), cheapest to build and test. Termination is by certificate, not by class.

### F3: Hybrid (existentials by default, named functions opt-in)
- **Syntax:** F1 ∪ F2. `∃Y` produces anonymous nulls. `f(t)` produces named terms. Named functions may **not** take anonymous-null positions as arguments ("named-guardedness", checked via affected positions) [U-own condition].
- **Semantics:** named rules are applied as Datalog with terms, anonymous existentials by any chase variant. Negation and aggregation are allowed over constants **and named terms**, but not over anonymous nulls.
- **Proposition 3 [U-own].** Under named-guardedness, the facts over constants ∪ named terms are identical in all universal models produced by any chase variant. So Choice A's variant-independence theorem (07 §4.3) extends, with named terms treated as constants.
  - Proof idea: named terms are built only from constants and named terms; homomorphisms between universal models fix them.
- **Maturity:** low (new combination). The components are mature.
- **Oracle:** Graal for the ∃ part plus `T(P)`; clingo for the named/negation part; the cross-check is not exact where the two interact.
- **Implementation:** highest (both machineries, plus two notions of term identity in every store; conflicts with 07 decision E5 unless the term model has three kinds: constant, named term, null).
- **Verdict:** the most expressive and the best long-term story (aerospace/defense ontologies need true ∃), but it should be F2 grown by adding ∃, not built first.

### Comparison

| Criterion | F1 existential | F2 named functions | F3 hybrid |
|---|---|---|---|
| E1 default/exception | ✓ (constants) | ✓ (also over invented objects) | ✓ |
| E2 sum + decimals | ✓ (constants) | ✓ | ✓ |
| E3 identity ("the manager") | ✗ (only "some") | ✓ | ✓ (opt-in) |
| E3 trap | FUS rewriting finds answers; chase diverges | diverges unless O2/data; goal-directed decidable (FDNC/finitary) | per chosen construct |
| Theoretical maturity | high | high (LP), medium (combination) | low |
| Termination/decidability analyses | full zoo | LP-with-functions + MFA/WA/JA adapted + arithmetic acyclicity | union, plus the named-guardedness condition |
| Test oracle | Graal (exact positive), Nemo | clingo/DLV exact; Graal via `T(P)` on ORACLE-EXACT | partial |
| Rule-set equivalence for the optimiser | logical equivalence (07) | must preserve named terms ("Skolem equivalence"; strong equivalence with functions) | both |
| Implementation complexity | high | low–medium | highest |

**Recommendation.**
1. Specify **F2** as the v1 core: named functions + O0/O1/O2 + stratified negation + stratified aggregation + exact decimals.
2. Specify `T(P)` in the spec as the *reference bridge* to existential rules. It makes Graal an oracle and imports existential-rule classes.
3. Leave the design open for **F3** by making the term model three-kinded from day one: constant / named term / anonymous null.

---

## 7. Open research questions (publication candidates)

1. **Function-graph translation theorem.** Prove Prop. 1 in full generality (nested terms, body function terms, data containing function graphs under O2), and mechanise it in Lean on the Chase-in-Lean library. Characterise exactly when `T(P)` stays linear, guarded, sticky or warded. Conjecture: "argument-guarded functions" (functions take all frontier variables) preserve frontier-guardedness.
2. **FUS under sharing.** Does sharing a function preserve first-order rewritability for linear programs? Harmless keys suggest yes for CQs, but piece-unifier rewriting over `T(P)` joins may not terminate. Find a direct rewriting calculus on terms (SLD with piece-unifiers modulo free constructors).
3. **Acyclicity for named functions.** Formal JA/MSA/RMFA variants with pooled function symbols. Prove soundness and the critical-instance property for named programs (Prop. 2 shows naïve reuse of rule-local results is unsound). Compare with Γ-acyclicity and AR on real enterprise KBs.
4. **Lookup-before-invent semantics (O2).** Characterise it as a Datalog-first restricted chase. Give termination criteria depending on *data completeness assumptions* (E3: "every recorded chain closes"). This suggests "conditional termination certificates" (terminates whenever D satisfies constraint C). C is checkable at load time.
5. **Harmless user keys with named functions and injectivity.** Extend Bellomarini et al.'s harmless EGDs to programs with injective named constructors and UNA on terms; complexity of consistency.
6. **Arithmetic-aware termination.** Unify WA-style arithmetic acyclicity, size-change (dualised for bottom-up growth), and limit Datalog. Give decidable criteria for recursive decimal arithmetic with aggregates.
7. **Equivalence for the optimiser under named semantics.** "Skolem-equivalence" and strong/uniform equivalence of stratified programs with function symbols. Find which 07 transformations remain valid, and their proof sheets.
8. **Incremental maintenance with named terms and O3 equality.** FBF over union-find with congruence closure and injectivity; retraction of merges.
9. **Explanations of invented objects.** Provenance of `manager(Paul)` across multiple rules and sharing; minimal explanations; the link to Ivliev–Krötzsch–Marx proof transformation (07).
10. **Certificates for assurance** (defense domain). Checkable termination certificates: an MFA certificate is a finite Skolem-chase run on the critical instance, which a small verified checker can validate.

---

## Sources verified this session
- Calimeri, Cozza, Ianni, Leone, ICLP 2008: https://link.springer.com/chapter/10.1007/978-3-540-89982-2_37
- Lierler & Lifschitz, ICLP 2009: https://link.springer.com/chapter/10.1007/978-3-642-02846-5_40
- Eiter & Šimkus, FDNC, ACM TOCL 11(2): https://dl.acm.org/doi/10.1145/1656242.1656249
- Alviano, Faber, Leone, TPLP 2010: https://arxiv.org/abs/1007.4028
- Bonatti, AIJ 2004: https://www.sciencedirect.com/science/article/pii/S0004370204000256
- Syrjänen, LPNMR 2001: https://link.springer.com/content/pdf/10.1007/3-540-45402-0_20.pdf
- Calautti, Greco, Spezzano, Trubitsyna, TPLP 2015: https://arxiv.org/abs/1407.2106
- Greco, Molinaro, Trubitsyna, Bounded programs: https://www.ijcai.org/Proceedings/13/Papers/142.pdf
- Calautti et al., atom sizes, IJCAI 2015: https://www.ijcai.org/Abstract/15/401
- Greco, Spezzano, Trubitsyna, PVLDB 2011: https://dl.acm.org/doi/abs/10.14778/3402707.3402750
- Calautti, Greco, Molinaro, Trubitsyna, PVLDB 9(5) 2016: https://dl.acm.org/doi/10.14778/2876473.2876475
- Cuenca Grau et al., JAIR 47, 2013: https://jair.org/index.php/jair/article/view/10830
- Carral, Dragoste, Krötzsch, IJCAI 2017: https://www.ijcai.org/proceedings/2017/0128.pdf
- Magka, Krötzsch, Horrocks, IJCAI 2013: https://www.ijcai.org/Abstract/13/157
- Magka, Motik, Horrocks, ESWC 2012: https://link.springer.com/chapter/10.1007/978-3-642-30284-8_29
- Bellomarini, Benedetto, Brandetti, Sallinger, PVLDB 15(13) 2022: https://dl.acm.org/doi/abs/10.14778/3565838.3565850
- Calì, Gottlob, Pieris, RR 2010: https://link.springer.com/chapter/10.1007/978-3-642-15918-3_1
- Mitchell 1983 / Chandra & Vardi 1985 (FD+IND undecidable): https://dl.acm.org/doi/abs/10.1137/0214049
- Fagin, Kolaitis, Popa, Tan, SO tgds: https://dl.acm.org/doi/10.1145/1114244.1114249
- Arenas, Pérez, Reutter, Riveros, plain SO-tgds, JCSS 2013: https://www.sciencedirect.com/science/article/pii/S0022000013000123
- Tsamoura & Motik, arXiv 2412.09125: https://arxiv.org/abs/2412.09125
- Benedikt, Motik, Tsamoura, AAAI 2018: https://ojs.aaai.org/index.php/AAAI/article/view/11563
- Marnette, value invention and equality: https://arxiv.org/abs/1212.0254
- Marx & Krötzsch, TGDs capture complex values, ICDT 2022: https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.ICDT.2022.13
- Lee, Jones, Ben-Amram, POPL 2001: https://www.semanticscholar.org/paper/The-size-change-principle-for-program-termination-Lee-Jones/ab8e798b8cf4b6ddc2bc80e5cdabbf6b9df14b0d
- Vadalog Skolem functions: https://vadalog.org/vadalog-handbook/latest/expressions-skolem.html
- RDFox reasoning (SKOLEM): https://docs.oxfordsemantic.tech/reasoning.html
- LogicBlox constructor predicates: https://developer.logicblox.com/content/docs/core-reference/webhelp/constructor-predicates.html
- Soufflé (arithmetic and non-termination): https://souffle-lang.github.io/tutorial

## Unverified points (check before citing)
- All [U-own] propositions: Prop. 1 (harmless keys / `T(P)`), Prop. 2 (sharing breaks termination; named-program termination implies local), Prop. 3 (named-guardedness), the critical-instance property for named programs, JA/MSA pooling, the E3 classifications.
- The exact non-conflicting key definition (Calì, Gottlob, Pieris, AIJ 2012) and whether Prop. 1 is literally an instance of it.
- Complexity of recognising AR, λ-restricted, bounded and atom-size programs; the λ-restricted reference (Gebser, Schaub, Thiele 2007); authors of the TPLP 2013 adornment paper; the year of the AAAI polynomially-bounded paper.
- The MFA treatment of equality (singularisation); RMFA behaviour on E3.
- The Nemo Skolem-naming snippet; Graal having no function terms or EGDs; clingo/DLV aggregate and decimal support.
- The content of Tsamoura & Motik (2412.09125) beyond title and authors.
