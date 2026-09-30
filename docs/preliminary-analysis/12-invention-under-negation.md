# 12: Value invention correlated with negation ("employees without a boss get a manager")

**Status: study, input for owner validation.** It checks the business rule stated by the project owner against the F2 contract of [report 11](11-f2-framework-definition.md) and against four other approaches, and it turns the results into phase-2 test scenarios. Nothing here changes report 11. Proposed changes are listed in §8 as candidate open points.

The rule under study:

```
R:  ∀x  employee(x) ∧ ¬hasBoss(x) → ∃y managerOf(y, x)
```

Here `hasBoss(x)` means "x has some recorded boss". In the examples, `hasBoss` is binary (`hasBoss(bob, alice)`: alice is bob's boss), and R reads `¬∃b hasBoss(x, b)`, written `not hasBoss(X, _)`.

Status tags, as in reports 09 and 11:
- **[V]**: bibliographic facts checked in this session. arxiv.org, KR proceedings, HAL and Semantic Scholar PDFs were blocked by the network proxy, so these checks rely on search-engine snippets of publisher and institutional pages.
- **[U]**: from memory, not re-checked.
- **[U-own]**: propositions formulated here, with a proof sketch only. Each needs a careful proof (E9) before it is relied on.
- **[E]**: checked empirically in this session. The programs and outputs are in the appendices.

---

## 0. Summary

1. **In Skolem form, R is lookup-before-invent written by hand.** Add the rule "a recorded boss is a manager", `managerOf(B,X) :- hasBoss(X,B)`, and R becomes `managerOf(manager(X), X) :- employee(X), not hasBoss(X,_)`. This is exactly `lb(K)` (report 11 §3.1) for `@lookup manager(X) = Y :- hasBoss(X,Y)` (§1).
2. **"The restricted chase is an implicit negation on the rule's own head": correct operationally, incomplete as an explanation of order dependence** (§3).
   - A restricted-chase trigger for `employee(x) → ∃y managerOf(y,x)` is active iff `¬∃y managerOf(y,x)` holds *now*. So the restricted chase is the **nondeterministic inflationary** evaluation of the Skolemised rule with that negation added. That rule negates its own head, which is a self-loop through negation and never stratifiable.
   - The self-loop **alone** does not cause order dependence: if only the rule itself can produce `managerOf(·, a)`, every restricted order gives the same result (the semi-oblivious one).
   - Order dependence needs a **second producer** of head atoms for the same frontier: a Datalog rule (removed by Datalog-first) or another invention (not removed by Datalog-first).
   - Under **stable-model** semantics, the naive self-negating program has **no** stable model as soon as one invention is needed (`p ← not p`). Under the **well-founded** semantics, every invention is *undefined*. Neither reproduces the chase.
   - The declarative counterpart of the restricted chase is a **self-exempt** encoding ("not ∃ a witness *other than my own invention*"). Its stable models are exactly the non-redundant fair restricted-chase results (Prop. D, [U-own], checked [E] on V1, V2, V2x and V5).
3. **V2 is literally the program that report 11 §3.3 rejects.** V2 is R plus `managerOf(y,x) → hasBoss(x,y)`. It equals `lb(K)` of the rejected `@lookup manager(X)=Y :- hasManager(X,Y)` example, and it also equals the restricted-chase reading of the plain existential rule.
   - F2 must reject it: it is non-stratifiable (plain stable models: 0; WFS: undefined).
   - Its *intended* meaning, "invent unless a boss exists", has a clean remedy that is already in F2's toolbox: a lookup whose source is the part of `hasBoss` that does not depend on the invention (`hasBoss@db`, OP-2).
4. **Recommendation.**
   - Accept V1, V3, V5 and V6 under the existing contract. V3 and V6b get the §6 statuses NOT-CERTIFIED / NOT-GUARANTEED / UNKNOWN.
   - Reject V2, V4 and V2x with a dedicated diagnostic class, **negation through value invention**, split into *self-defeating* (V2, V4: a mechanical rewrite to lookup exists) and *cross-defeating* (V2x: a genuine choice, 2 stable models, order-dependent chase).
   - Do not give V2 a native non-stratified semantics in v1 (D1). Propose it as an optional, checkable sugar (OP-21).

---

## 1. The rule explained simply, then formally

### 1.1 In plain words

"Every employee who has no boss on record gets a manager; if we do not know who that manager is, we create a placeholder for them."

There are three ways to write this.

| Form | Rule | Where the "unless" lives |
|---|---|---|
| **Skolem with explicit negation (F2)** | `managerOf(manager(X), X) :- employee(X), not hasBoss(X,_).` | explicit: `not hasBoss(X,_)` |
| **Existential, restricted chase** | `employee(x) → ∃y managerOf(y, x)` | implicit: the engine fires the rule only if no `managerOf(_, x)` exists yet |
| **Skolem without negation (semi-oblivious chase)** | `managerOf(manager(X), X) :- employee(X).` | nowhere: everyone gets `manager(X)`, even bob, who already has alice |

The owner's rule R is the first form. It makes explicit what the restricted chase does implicitly, but it tests a **different predicate**:
- R tests `hasBoss`;
- the restricted chase tests the rule's own head, `managerOf`.

The two coincide only when `hasBoss` and `managerOf` are linked by rules. How they are linked is exactly what the variants of §4 vary.

### 1.2 R as lookup-before-invent

Take the lookup declaration `@lookup manager(X) = Y :- hasBoss(X, Y).` and the rule `managerOf(manager(X), X) :- employee(X).` The translation `lb(K)` (report 11 §3.1) produces:

```
$L_manager(X,Y) :- hasBoss(X,Y).
$D_manager(X)   :- $L_manager(X,_).
managerOf(Y, X)            :- employee(X), $L_manager(X, Y).       % look
managerOf(manager(X), X)   :- employee(X), not $D_manager(X).      % invent
```

Up to the names `$L`/`$D` (and the extra `employee(X)` guard on the look branch), this is the owner's R plus the lookup rule `r0: managerOf(B,X) :- hasBoss(X,B)`. **R is the invent branch of lookup-before-invent, written by hand.** Consequences:
- everything report 11 proves about `lb(K)` (Prop. 2, invention only when unrecorded; the Datalog-first correspondence) applies to R when `hasBoss` does not depend on `managerOf`;
- when it does (V2), we are in the non-stratifiable case of report 11 §3.3.

### 1.3 Formal setting

Fix a single-head rule with one existential variable:

`r: B(x̄, z̄) → ∃y H(x̄, y)` (`x̄` is the frontier).

From it we build three programs:

| Program | Rule(s) | Name |
|---|---|---|
| `sk(r)` | `H(x̄, f_r(x̄)) ← B(x̄, z̄)` | Skolemisation; its least model is the semi-oblivious chase ([09 §1.1](09-skolem-function-frameworks.md)) |
| `neg(r)` | `H(x̄, f_r(x̄)) ← B(x̄, z̄), not sat_r(x̄)` and `sat_r(x̄) ← H(x̄, y)` | "invent unless a witness exists": the restricted check made explicit |
| `se(r)` | `H(x̄, f_r(x̄)) ← B(x̄, z̄), not other_r(x̄)` and `other_r(x̄) ← H(x̄, y), y ≠ f_r(x̄)` | **self-exempt**: "invent unless a witness *other than my own invention* exists" |

For a program `Σ`, `neg(Σ)` and `se(Σ)` apply the construction to every existential rule and keep the Datalog rules. The null created by a restricted-chase trigger with frontier image `ā` is named `f_r(ā)`. This naming is sound because the restricted chase applies `r` at most once per frontier tuple: after one application, the head is satisfied for `ā`. With this naming, results of different chase orders can be compared syntactically, without renaming nulls. The chase simulator of Appendix B does exactly this.

---

## 2. Approaches compared

| Label | Approach | Semantics used here |
|---|---|---|
| (a) | **F2** (report 11) | predicate-level stratification of `lb(K)`; perfect model; accept/reject; §6 unit statuses under budgets |
| (b1) | **Restricted chase**, existential form, *no* negation (`R∃: employee(x) → ∃y managerOf(y,x)`) | all fair sequences; Datalog-first (DF) sequences; existential-first; Nemo-like parallel DF (a rule fires all its active triggers at once) |
| (b2) | **Semi-oblivious / Skolem chase** of `R∃` | least model of `sk(Σ)` |
| (b3) | Chase with R's negation evaluated **eagerly** (inflationary, no strata) | all sequences; shows what a procedural engine without stratification does |
| (c) | **Stable models** with function symbols (ASP; "existential ASP" by Skolemisation, Baget et al. 2018 [V]) | plain Skolem encoding of R, and the self-exempt encoding `se`; number of models, brave and cautious answers |
| (d) | **Well-founded semantics** | alternating fixpoint on the ground program |
| (e) | **Empirical** | clingo 5.8.2 (Python API), a purpose-written chase simulator, and Nemo 0.10.2-dev built from source (commit `e578c28`, about 5 minutes) |

---

## 3. Is the restricted chase an implicit negation? What the literature supports, what holds

### 3.1 Literature

- **Order dependence of the restricted chase** (result and termination), and the **Datalog-first** strategy:
  - Krötzsch, Marx & Rudolph, *The Power of the Terminating Chase*, ICDT 2019 [V]. It discusses a Datalog-first standard chase that prioritises rules without existentials.
  - Carral, Dragoste & Krötzsch, *Restricted Chase (Non)Termination for Existential Rules with Disjunctions*, IJCAI 2017 [V]. Its Datalog-first acyclicity notions were already cited in [09 §2](09-skolem-function-frameworks.md).
  - Gerlach & Carral, *Do Repeat Yourself: Understanding Sufficient Conditions for Restricted Chase Non-Termination*, KR 2023 [V title].
  - Carral, Gerlach, Larroque & Thomazo, *Restricted Chase Termination: You Want More than Fairness*, PODS 2025 / PACMMOD [V]. It shows that universal restricted-chase termination is hard *because of fairness*, and proposes an alternative condition.

  These results are stated for the chase itself. They do not phrase it as negation.
- **Existential rules with negation under stable models via Skolemisation**:
  - Magka, Krötzsch & Horrocks, *Computing Stable Models for Nonmonotonic Existential Rules*, IJCAI 2013 [V]: R-acyclicity and R-stratification.
  - Baget, Garcia, Garreau, Lefèvre, Rocher & Stéphan, *Bringing existential variables in answer set programming and bringing non-monotony in existential rules: two sides of the same coin*, AMAI 82, 2018 [V]: ENM-rules, semantics by Skolemisation, chase termination in the non-monotonic case.

  In both, negation is *user-written*. The existential head is Skolemised *without* a restricted check. This is our column (c) "plain".
- **Queries with negation over chase results**:
  - Ellmauthaler, Krötzsch & Mennicke, *Answering Queries with Negation over Existential Rules*, AAAI 2022 [V]. It proposes universal **core** models as the semantics and identifies query fragments that are safe on other chase results. This is exactly the V6 issue.
  - Krötzsch, *Computing Cores for Existential Rules with the Standard Chase and ASP*, KR 2020 [V title and venue]. It uses ASP to compute cores and restricted-chase results. That its encoding uses a negated head-satisfaction check is [U]: the PDF was not reachable.
- **Nondeterministic inflationary Datalog¬**: Abiteboul & Vianu, *Datalog extensions for database queries and updates*, JCSS 43, 1991 [V]. Firing one rule instance at a time with negation evaluated on the current instance gives a nondeterministic language whose results depend on the order. The restricted chase is an instance of this (Prop. A).
- **Implementation evidence (Nemo)** [E, source read in this session]:
  - `nemo/src/execution/selection_strategy/strategy_stratified_negation.rs` builds a rule graph with three edge labels: `Positive`, `Negative` and **`Restrain`**. A `Restrain` edge goes from any rule producing predicate `p` to every *other* rule that has `p` in an existential head. It is a *weak* edge: a scheduling preference, not a stratification constraint.
  - The restricted check is implemented with a generated `_SATISFIED_n` helper table (`planning/strategy/forward/restricted.rs`).

  So Nemo treats an existential head as an implicitly negated predicate, but only for *ordering*, which amounts to a component-level Datalog-first preference.

I found no paper that states the claim in the owner's words, "restricted chase = Skolem rule negating its own head, hence non-stratifiable". Props. A to D below make it precise. They are [U-own].

### 3.2 What holds

**Prop. A (operational identity) [U-own, immediate].** For `Σ` with rules of the form of §1.3, the fair restricted-chase sequences of `Σ` on `D` are exactly the fair sequences of `neg(Σ)` on `D` in which one rule instance fires at a time, with `not sat_r` evaluated on the *current* instance and facts never retracted. This is the nondeterministic inflationary semantics of Abiteboul & Vianu, with Skolem-named nulls.

*Sketch.* A restricted trigger `(r, h)` is active iff `B h` holds and `∃y H(h(x̄), y)` does not. That is precisely "the body of the `neg(r)` instance holds now". Applying it adds `H(ā, f_r(ā))`, the head of that instance.

**Prop. B (non-stratifiability) [U-own, immediate].** Every existential rule `r` yields the cycle `H →(−) sat_r →(+) H` in the predicate graph of `neg(Σ)`. So `neg(Σ)` is never stratifiable, as the owner stated.

Its declarative semantics is degenerate: a ground instance with body true gives the pair `H(ā,f) ← not sat(ā)` and `sat(ā) ← H(ā,f)`, an odd loop (`p ← not p`).
- **Stable models**: none, as soon as some trigger needs an invention.
- **WFS**: `H(ā, f_r(ā))` and `sat_r(ā)` are undefined.

[E]: V2 is exactly this program (§4), and clingo reports 0 stable models, with the inventions undefined in the WFS.

**Prop. C (where order dependence comes from) [U-own, sketch].** Call `r` **head-exclusive** on `D` if, in every fair restricted sequence, the only rule application that produces an atom `H(ā, ·)` is `r`'s own trigger for `ā`.
1. If every existential rule is head-exclusive, all fair restricted sequences produce the same result, and it equals the semi-oblivious result.
2. Order dependence therefore requires a *second producer* of `H(ā, ·)`.
3. If every second producer is a Datalog rule whose derivation of `H(ā, ·)` does not depend on invented terms, every Datalog-first fair sequence produces the same result. By report 11 Prop. 2.3, it equals the F2 perfect model of the lookup reading.
4. If a second producer depends on inventions (V2x: "colleagues share their manager"), even Datalog-first sequences may differ.

*Sketch.*
1. With head-exclusivity, a trigger can only be deactivated by its own application, so every active trigger eventually fires by fairness. The fired set is order-independent and equals the semi-oblivious trigger set.
2. This is the contrapositive of 1.
3. Datalog-first saturates the invention-independent Datalog producers before any existential trigger. By the independence hypothesis, those producers would give the same atoms `H(ā, ·)` at any later point. So the set of deactivated triggers is fixed before the first invention.
4. By example, [E] V2x: 2 Datalog-first results.

So the self-loop of Prop. B explains non-stratifiability, but **not** order dependence. Order dependence comes from negative cycles involving *two* rule instances, like an even loop in ASP. In rule-graph terms, these are exactly the cases where Nemo's `Restrain` edges join two different rules. Nemo omits the self-edge (`head_index != existential_index`).

**Prop. D (declarative counterpart of the restricted chase) [U-own, sketch].** Assume the rule shape of §1.3 (single head atom, one existential variable). For every interpretation `M`, the following are equivalent:
1. `M` is a stable model of `se(Σ) ∪ D`;
2. `M` is the result of a fair restricted-chase sequence of `Σ` on `D` that is **non-redundant**: no invented atom `H(ā, f_r(ā))` coexists in the result with another atom `H(ā, b)`, `b ≠ f_r(ā)`.

*Sketch.*
- (1 ⇒ 2): Order the atoms of `M` by their stage in the least model of the reduct `se(Σ)^M`, and fire in that order. When `H(ā, f_r(ā))` is fired, `other_r(ā) ∉ M`, so no other witness exists, then or later. `f_r(ā)` itself is not yet present, so the trigger is active. `M ⊨ Σ`, because a body-true instance either has `other_r(ā)` true (satisfied) or fires in the reduct. Fairness comes from dovetailing the ω stages.
- (2 ⇒ 1): Take the result `M` of a non-redundant fair sequence. The reduct keeps the invention instance exactly for the `ā` without another witness, which includes every fired trigger by non-redundancy. So the least model of the reduct re-derives `M`. It derives nothing more, because a kept instance with a true body has no witness other than `f_r(ā)` in `M`, so by fairness it fired and its head is in `M`.

Corollaries:
- Redundant chase results, such as the existential-first result of V1 where bob gets a null *and* alice, are not stable models of `se(Σ)`.
- On V1 and V5, the unique stable model of `se` is the Datalog-first result, which equals F2 ([E]).
- On V2x, `se` has 2 stable models, which are the 2 Datalog-first results ([E]).
- `se(Σ)` can be inconsistent where the chase terminates. This happens when an invention always creates a *different* witness for its own frontier, e.g. `H(x,y) → H(x,c)`: every result is redundant.

**Answer to the owner's claim.**
- *Correct*: the restricted chase behaves as if the Skolemised rule carried `not ∃y H(x̄,y)` (Prop. A), and written as a rule this is a self-dependency through negation, never stratifiable (Prop. B).
- *To be refined*: the self-dependency is not what makes results order-dependent. Alone it is harmless for the chase and fatal for stable and well-founded semantics. Order dependence comes from **other** producers of the head (Prop. C). Datalog-first removes those that do not depend on inventions, and nothing removes those that do.
- The faithful declarative reading is `se` (Prop. D), not `neg`.

---

## 4. The variants

Common data (all variants), in F2 provisional syntax:

```
@function manager/1.
person(alice). person(bob). person(carol).
employee(alice). employee(bob). employee(carol).
hasBoss(bob, alice).

r0:  managerOf(B, X) :- hasBoss(X, B).                                  % a recorded boss is a manager
R:   managerOf(manager(X), X) :- employee(X), not hasBoss(X, _).        % the owner's rule
```

`r0` is included in every variant, so that `managerOf` is the single "manager" relation. Without it, bob would have a boss but no `managerOf` fact. For the existential approaches (b), R is replaced by `R∃: employee(x) → ∃y managerOf(y, x)`.

| Variant | Extra rules / data | What it tests |
|---|---|---|
| **V1** | none (`hasBoss` from data only) | stratified base case |
| **V2** | `r2: hasBoss(X, Y) :- managerOf(Y, X).` | R negates what it derives (`p ← not p`) |
| **V2x** | V2 + `colleague(alice,carol). colleague(carol,alice).` + `rc: managerOf(Y, Z) :- managerOf(Y, X), colleague(X, Z).` | cross-defeating invention: inventing for one employee gives the other a boss |
| **V3** | V1 + `r3: employee(Y) :- managerOf(Y, X).` | infinite chain; termination and §6 statuses |
| **V4** | V2 + r3 | both |
| **V5** | data `employee(dave;erin)`, `worksIn(carol,sales)`, `worksIn(dave,sales)`, `head(dave,sales)`, `subDept(sales,hq)`, `head(erin,hq)`, `worksIn(erin,hq)`, and rules `w1: hasBoss(X,H) :- worksIn(X,D), head(H,D), X != H.` and `w2: hasBoss(H,H2) :- head(H,D), subDept(D,P), head(H2,P).` | stratified, multi-level derivation of `hasBoss` |
| **V6** | queries on V1 (V6a) and on V3 (V6b): `Q6a ?(X) :- managerOf(Y,X), not person(Y).` ("has a manager who is not a known person", i.e. an invented one); `Q6b ?(X) :- employee(X), not knownManager(X).` with `knownManager(X) :- managerOf(Y,X), person(Y).`; `Q6c ?(X) :- employee(X), not hasMgr(X).` with `hasMgr(X) :- managerOf(_,X).` | negation *above* R |

---

## 5. Results per variant

In the chase columns, `n(x)` denotes the null of the `R∃` trigger with frontier `x`, and `m(x)` abbreviates `manager(x)`.

### 5.1 V1: `hasBoss` from data only

- **(a) F2.**
  - Strata: `hasBoss` (data) < `managerOf`. Accepted.
  - Unit `{managerOf}` is non-recursive and WA, hence certified.
  - `PM`: `managerOf(alice,bob)`, `managerOf(m(alice),alice)`, `managerOf(m(carol),carol)`. **COMPLETE-STATIC.** No `m(bob)` is invented.
- **(b1) Restricted chase.**
  - Over all fair orders there are **2** results: the above (with `n` for `m`), or the same plus a redundant `managerOf(n(bob), bob)` when `R∃` fires for bob before `r0`.
  - Datalog-first gives **1** result, equal to F2.
  - Nemo gives the Datalog-first result even with `R∃` written first in the file: its `Restrain` edge schedules `r0` first.
- **(b2) Semi-oblivious**: 3 nulls, including the redundant `n(bob)`.
- **(b3) Eager negation, no strata**: 2 results. If R fires for bob before the projection `hb(bob) :- hasBoss(bob,_)` is derived, bob gets a null. Stratification is what prevents this.
- **(c) Stable models**: plain encoding 1 model and `se` 1 model, both equal to F2. Brave = cautious.
- **(d) WFS**: total, equal to F2.

### 5.2 V2: `hasBoss(X,Y) :- managerOf(Y,X)`, R negates what it derives

- **(a) F2: rejected.**
  - Cycle `managerOf →(−) hasBoss →(+) managerOf` (R, then r2).
  - Up to renaming (`hasBoss` ≡ `managerOf⁻¹` through r0/r2), this is exactly `lb(K)` of report 11 §3.3's rejected program, and exactly `neg(Σ)` for `Σ = {r0, R∃}` (Prop. B).
- **(b1) Restricted chase** (existential form, r2 kept):
  - all orders: 2 results; Datalog-first: 1 (bob gets no null);
  - **Nemo: bob gets a redundant null**, whatever the rule order. `R∃` and the cycle `{r0, r2}` are separate components, and the positive edge `R∃ → r2` (strong) overrides the `Restrain` preference `r0 ⇢ R∃` (weak). So Nemo is Datalog-first only at component level, not per trigger.
- **(b2) Semi-oblivious**: 3 nulls.
- **(c) Stable models.**
  - Plain: **0** (odd loop for alice and carol).
  - `se`: **1**. This is the intended model: `m(alice)`, `m(carol)` invented, bob keeps alice, and `hasBoss` mirrors `managerOf`.
- **(d) WFS (plain)**: `hasBoss(bob,alice)` and `managerOf(alice,bob)` are true. `managerOf(m(alice),alice)`, `managerOf(m(carol),carol)` and their `hasBoss` mirrors are **undefined**.
- **Intended meaning.** "Invent unless a boss exists." With the remedy `@lookup manager(X) = Y :- hasBoss@db(X,Y)`, R becomes the positive rule `managerOf(manager(X), X) :- employee(X)`, and the program is stratified. Its `PM` equals the unique `se` stable model and the Datalog-first chase result.

### 5.3 V2x: cross-defeating invention

- **(a) F2: rejected** (same cycle as V2, plus `rc`).
- **(b1) Restricted chase.**
  - All orders: **6** results.
  - Datalog-first: **2** results, one where alice's invented manager also manages carol, and the symmetric one. **Up to renaming of nulls they are isomorphic**, so with anonymous nulls the order dependence is invisible. With named Skolem terms, `m(alice)` ≠ `m(carol)` reveals which trigger fired first.
  - Nemo (parallel Datalog-first): fires alice's and carol's triggers in the same step, giving both managers plus a redundant null for bob. This is a non-core result.
- **(c) Stable models.**
  - Plain: 0.
  - `se`: **2**, one per Datalog-first result.
  - Brave: every invented atom. Cautious: none of them. In mode `constants`, both models agree.
- **(d) WFS**: all inventions undefined, in both encodings.

### 5.4 V3: every manager is an employee (infinite)

- **(a) F2.**
  - Stratifiable: the SCC `{employee, managerOf}` is recursive through r3, and its only strict edge goes to `hasBoss` (data). Accepted.
  - The unit is **NOT-CERTIFIED**: term-free, WA, JA, AR and Γ fail, and MFA finds the cyclic term `manager(manager(X))`.
  - `PM` is infinite: `m^n(alice)` and `m^n(carol)` for all `n ≥ 1`. bob has no invented manager, but his boss alice starts a chain.
  - Budget `depth = 3`: the depth-4 derivations are cut, so the unit is in `Inc`.
    - `?(X,Y) :- managerOf(X,Y)` → 7 tuples, **NOT-GUARANTEED**.
    - `?(X) :- employee(X)` in mode `constants` → `{alice, bob, carol}`, NOT-GUARANTEED. The answer is in fact complete; this is OP-14.
- **(b1) Restricted chase**: non-terminating in **every** order.
  - Datalog-first avoids bob's redundant null, and with it a whole redundant infinite chain `n(bob)`, `n(n(bob))`, …. Existential-first produces that chain.
  - [E] Nemo was killed after 15 s. The simulator shows 1 Datalog-first result and 2 results over all orders, up to depth 3.
- **(b2) Semi-oblivious**: non-terminating, including bob's chain.
- **(c)/(d)**: one (infinite) stable model in both encodings. The WFS is total and equals `PM`.
  - [E] Unbounded clingo grounding was killed after 20 s.
  - With the depth guard `k = 3`, clingo gives 1 model, whose `cut/1` atoms mark `m³(alice)` and `m³(carol)`.

### 5.5 V4: V2 + V3

- **(a) F2: rejected** (V2's cycle). After the V2 remedy, it behaves as V3 plus the `hasBoss` mirror.
- **(b1)/(b2)**: as V3 (non-terminating, with the same order effect on bob).
- **(c)**: plain 0; `se` 1 (infinite, the V3 model plus mirror). With `k = 3`, clingo gives 0 and 1.
- **(d)**: plain: every invented atom, at every depth, is undefined. `se`: total.

### 5.6 V5: `hasBoss` derived from other data

- **(a) F2.**
  - Strata: `hasBoss` (w1, w2 over data) < `managerOf`. Accepted.
  - Everything is non-recursive, hence certified.
  - `PM`: `hasBoss` = {bob→alice, carol→dave, dave→erin}. `managerOf` = {alice→bob, dave→carol, erin→dave, `m(alice)`→alice, `m(erin)`→erin}. **COMPLETE-STATIC.**
- **(b1) Restricted chase**:
  - all orders: **8** results, one redundant-null choice for each of bob, carol and dave;
  - Datalog-first: 1 result, equal to F2;
  - Nemo: the Datalog-first result.
- **(b2) Semi-oblivious**: 5 nulls, 3 of them redundant.
- **(c)/(d)**: 1 stable model in both encodings, WFS total, all equal to F2.

### 5.7 V6: negation above R

- **V6a (on V1).**
  - F2: `Q6a = Q6b = {alice, carol}`, COMPLETE-STATIC. The query negates `person` (data) and `knownManager` (complete).
  - Restricted chase: `Q6a` is **order-dependent**. It gives `{alice, carol}` under Datalog-first and Nemo, and `{alice, bob, carol}` under existential-first and semi-oblivious, because bob's redundant null "is not a person". `Q6b` is the same in every order.
  - This is the Ellmauthaler, Krötzsch & Mennicke (2022) phenomenon: `Q6a` must be evaluated on the core, which here is the Datalog-first result. `Q6b` belongs to a fragment that is safe on any universal model.
  - F2 has no such issue: named terms plus lookup-before-invent give a canonical model.
  - Stable models and WFS: 1 model, total; equal to F2.
- **V6b (on V3, budget `depth = 3`).**
  - `Q6a`: positive in `managerOf` (incomplete unit), negative only on `person` (data). The query is **sound-partial**, so the status is **NOT-GUARANTEED**. The returned answers are sound; `q6a(m³(alice))` is missing because of the cut.
  - `Q6b`: negates `knownManager`, which reaches the incomplete unit. It is **exposed**: **UNKNOWN**, and nothing is returned (N1).
  - `Q6c`: exposed, **UNKNOWN**. [E] The budgeted clingo model *does* contain `q6c(m³(alice))` and `q6c(m³(carol))`. These are **false** in `PM`, where every employee has a manager. This is a concrete instance of report 11 §6.1: returning them would be unsound. It is the empirical justification for rule N1.
  - Goal-directed evaluation (§8.2): the ground query `?() :- q6b(alice)` completes. The table of `knownManager(alice)` resolves `managerOf(Y, alice)` to `m(alice)` (R: `hasBoss(alice,_)` has no answers, and its table is complete) and to no `r0` answer, and `m(alice)` is not a `person`. So the status is COMPLETE-DYNAMIC, with answer true. The open query `?(X) :- q6b(X)` does not complete, since `employee` is infinite.

---

## 6. Comparison table

`=F2` means the result equals the F2 perfect model, or the model obtained after the recommended remedy for rejected variants. `DF` means Datalog-first. `+n(bob)` marks a redundant null. `∞` means infinite or non-terminating.

| Variant | (a) F2 | (b1) Restricted chase: all orders / DF / Nemo | (b2) Semi-oblivious | (c) Stable models: plain / `se` | (d) WFS (plain) | (e) Verified with |
|---|---|---|---|---|---|---|
| **V1** | accept; COMPLETE-STATIC; invents for alice, carol | 2 / 1 =F2 / =F2 | `+n(bob)` | 1 =F2 / 1 =F2 | total =F2 | clingo, simulator, Nemo |
| **V2** | **reject** (self-defeating); remedy `@lookup … hasBoss@db` | 2 / 1 / `+n(bob)` (not DF) | `+n(bob)` | **0** / 1 (intended) | inventions **undefined** | clingo, simulator, Nemo (negation form rejected "not stratified") |
| **V2x** | **reject** (cross-defeating) | 6 / **2** (isomorphic) / both + `n(bob)` | 3 nulls | 0 / **2** (brave ≠ cautious) | inventions undefined | clingo, simulator, Nemo |
| **V3** | accept; NOT-CERTIFIED; ∞; NOT-GUARANTEED under budget | ∞ in all orders (DF avoids bob's chain) / Nemo ∞ | ∞ | 1 ∞ / 1 ∞ | total, ∞ | clingo (bounded; unbounded times out), simulator (bounded), Nemo timeout |
| **V4** | **reject**; remedy → V3 plus mirror | ∞ | ∞ | **0** / 1 ∞ | all inventions undefined | clingo (bounded), simulator (bounded), Nemo timeout |
| **V5** | accept; COMPLETE-STATIC; invents for alice, erin | 8 / 1 =F2 / =F2 | 3 redundant nulls | 1 / 1 =F2 | total | clingo, simulator, Nemo |
| **V6a** | `Q6a = Q6b = {alice, carol}` COMPLETE-STATIC | `Q6a` order-dependent (adds bob off-DF) | `Q6a` adds bob | 1 / 1 | total | all three |
| **V6b** | `Q6a` NOT-GUARANTEED; `Q6b`, `Q6c` UNKNOWN | ∞ | ∞ | 1 ∞ | total, ∞ | clingo bounded: spurious `q6c` confirms N1 |

---

## 7. Recommendations for F2

### 7.1 Accept, reject, flag

| Variant | Decision | Status / diagnostic |
|---|---|---|
| V1, V5 | **accept** | COMPLETE-STATIC. Optionally emit an info message: "`r0` + R form the lookup-before-invent pattern for `manager`" (§7.2, item 4) |
| V3 | **accept, flag** | NOT-CERTIFIED with an MFA witness. Under budget: NOT-GUARANTEED for positive queries, UNKNOWN for strict dependants (§6, unchanged) |
| V6 | **accept** | per-query statuses as in §5.7. No change to §6 is needed; V6b is a regression test for N1 |
| V2, V4 | **reject** | new diagnostic `self-defeating-invention` with an automatic remedy suggestion (lookup from the invention-independent part) |
| V2x | **reject** | new diagnostic `cross-defeating-invention`: "the result would depend on the order of inventions; 2 incompatible choices". No automatic remedy; the user must decide (e.g. pick a deterministic rule such as "the manager of the alphabetically first colleague") |

### 7.2 What the analyser must detect

1. **Negation through value invention.** A non-stratifiability cycle that contains a rule with a functional head term `f(t̄)` is a *negation through invention* cycle, whether the negation comes from `@lookup` (report 11 §3.3) or is hand-written (V2).
   - Report 11 Proposition 1 covers only the lookup-induced case. It should be generalised: *a strict cycle is reported as invention-related iff it passes through the head of a rule that creates a functional term*.
   - The message names the function, the negated literal and the cycle as original rules, as §3.3 already requires.
2. **Self versus cross classification** (a sufficient syntactic test, [U-own]). A negation-through-invention cycle through rule `r` (head `H(x̄, f(x̄))`, negated literal `not q(x̄', _)`) is **self-defeating** when every rule path from `H` to `q` inside the cycle is *frontier-preserving*:
   - each rule on the path has a single body atom from the cycle;
   - the frontier variables `x̄` flow unchanged into the positions of `q` that `r`'s negated literal binds.

   Examples:
   - `r2: hasBoss(X,Y) :- managerOf(Y,X)` is frontier-preserving;
   - `rc: managerOf(Y,Z) :- managerOf(Y,X), colleague(X,Z)` is not: the frontier changes from `X` to `Z`.

   Under this condition, every derivation of `q(ā, ·)` that uses an invention uses `r`'s own invention for `ā`. `se` then coincides with "negate only the invention-independent part of `q`", and all Datalog-first orders agree (Props. C and D). Otherwise the cycle is classified **cross-defeating**.
3. **Remedy synthesis** for self-defeating cycles. Split `q` into:
   - `q_base`: the rules of `q` that do not reach `r`;
   - the rest.

   Then suggest `@lookup f(x̄) = Y :- q_base(…)`, or `q@db` when `q_base` is data only. This is OP-2's sugar, generalised to an `@base` view. The rewritten program is stratified by construction, and its perfect model is the unique `se` stable model [U-own].
4. **Pattern recognition "invent unless recorded".** A pair `{r0: p(B,X) :- q(X,B).  R: p(f(X),X) :- body, not q(X,_).}` should be recognised and normalised to the `@lookup` form. This keeps report 11's guarantees available: Prop. 2, explanations "invented because unrecorded", and the Graal oracle of §9 (see §8, tension T3).
5. **Termination (unchanged).** V3 and V4 must produce the §7.3 witness `manager(manager(X))` through r3 and the invent branch.

### 7.3 §6 statuses

§6 applies unchanged, and V3/V6b exercise every row:
- positive queries on V3: NOT-GUARANTEED;
- `Q6a` (negation on data only): NOT-GUARANTEED, not UNKNOWN, because the exposure rule looks only at strict edges into incomplete units;
- `Q6b` and `Q6c`: UNKNOWN;
- ground `Q6b(alice)` by goal-directed evaluation: COMPLETE-DYNAMIC.

The spurious budgeted answer `q6c(m³(alice))` ([E]) is the test that N1 exists for.

### 7.4 V2: reject, or give it a semantics?

| Option | Meaning | Cost |
|---|---|---|
| (a) reject, with remedy suggestion | F2 stays stratified; the user writes `@lookup … hasBoss@db` (or `@base`) | none; consistent with D1/D2 |
| (b) native "invent unless exists" semantics = `se` stable model / Datalog-first restricted chase, only when the analyser proves the cycle self-defeating (§7.2, item 2) | V2 and V4 accepted as written | new semantic layer (non-stratified). It needs Props. C and D proved (E9), a new explanation leaf ("not invented because another witness exists") and a new termination abstraction. It is equivalent to (a) after the automatic rewrite, so it adds syntax, not expressiveness |
| (c) inflationary / chase-order semantics | whatever the engine's order gives | order-dependent (V2x, and V1 without strata): violates the determinism of report 11 §6.4 |

**Recommendation: (a) in v1**, with the remedy computed automatically, so that the user sees the rewritten rule. Record (b) as **OP-21**: an optional sugar `@invent-unless-exists` whose meaning is *defined as* the (a) rewrite. It is accepted only for self-defeating cycles. Its order-independence condition is the frontier-preservation test of §7.2, item 2, a statically checkable condition, not a user declaration. Reject (c).

---

## 8. Tensions with decisions D1–D5 and report 11

| # | With | Tension | Proposed resolution |
|---|---|---|---|
| T1 | **D2**, report 11 §3.3 Prop. 1 | Prop. 1 detects only *lookup-induced* cycles. V2 is the same cycle, hand-written, and would be reported as a generic non-stratifiability without the D2 explanation or remedy | generalise Prop. 1 to "negation through invention" (§7.2, item 1). Same proof idea: the new strict edges are those whose target reaches an inventing head |
| T2 | **D1** | Accepting V2 natively (option (b)) would leave the stratified perfect-model framework | keep D1; add OP-21 as sugar defined by rewrite |
| T3 | report 11 §9 (Prop. 5, Graal EXACT fragment) | V1 written with explicit `not hasBoss` is semantically the lookup form, but falls outside Prop. 5 (negation below the query), so the Graal oracle is classified NONE | pattern normalisation (§7.2, item 4) brings it back to EXACT. Otherwise use clingo as the oracle |
| T4 | **D5** / OP-16 | V1's `managerOf` reaches a negative edge to `hasBoss`, which has **no rules** (data only). The D5 guard forbids a stored rewriting, although `not hasBoss(X,_)` is a plain base literal | refine the guard: strict edges into predicates without rules (pure EDB) do not block stored rewriting. The negated literal is kept as a base literal (§8.4 already evaluates it on `D`). To be added to OP-16 |
| T5 | report 11 §4.5 Prop. 2.3 and §9 (Datalog-first correspondence), [09 §4 O2](09-skolem-function-frameworks.md) | Confirmed [E] on V1 and V5. But **Nemo is not a per-trigger Datalog-first chase** on cyclic programs ([E] V2 existential: redundant null for bob), and fires existential rules in parallel ([E] V2x) | when Nemo is used as an oracle, compare only constant answers of positive queries (Prop. 5 already requires this). Never compare queries that negate invented positions (V6a `Q6a`) |
| T6 | report 11 §6.4 determinism | none for F2. Chase-based oracles are order-dependent on V1, V2, V5 and V6a | conformance tests fix the F2 expected output; chase oracles are checked "up to redundant nulls" (homomorphic equivalence) only |
| T7 | report 11 §5.3 explanations | For R with explicit negation, the justification leaf is `not hasBoss(alice,_)`. For the lookup form it is `invented manager(alice)`. Same fact, two explanation shapes | acceptable; pattern normalisation (§7.2, item 4) can unify them |

Candidate new open points for report 11 §11:
- **OP-21**: `@invent-unless-exists` sugar (§7.4).
- **OP-22**: generalise §3.3 Proposition 1 and add the self/cross classification (§7.2).
- **OP-16 addendum**: EDB-only strict edges (T4).

---

## 9. Test scenarios for phase 2

Conventions:
- the data is §4's unless stated otherwise;
- all expected outputs are for F2;
- `m(x)` stands for `manager(x)`;
- budgets are explicit (report 11 §6.4);
- mode `all` unless stated.

| Id | Input | Query / task | Expected output | Expected status / diagnostic |
|---|---|---|---|---|
| T12-01 | V1 | `?(Y,X) :- managerOf(Y,X).` | `(alice,bob)`, `(m(alice),alice)`, `(m(carol),carol)` | COMPLETE-STATIC; no `m(bob)` anywhere in the model |
| T12-02 | V1 rewritten as `@lookup manager(X)=Y :- hasBoss(X,Y).` + `managerOf(manager(X),X) :- employee(X).` | same | same as T12-01 | COMPLETE-STATIC (differential test: explicit negation versus lookup) |
| T12-03 | V2 | load | rejected | `self-defeating-invention`: cycle `R →(not hasBoss) r2 → R`, function `manager`; remedy `@lookup manager(X)=Y :- hasBoss@db(X,Y)` |
| T12-04 | V2 after the suggested remedy | `?(Y,X) :- managerOf(Y,X).` and `?(X,Y) :- hasBoss(X,Y).` | managerOf as T12-01; hasBoss = `(bob,alice)`, `(alice,m(alice))`, `(carol,m(carol))` | COMPLETE-STATIC: the unit `{managerOf, hasBoss}` (positive r0/r2 cycle plus the invent branch) is WA, since `manager(X)` takes `X` from `employee` (data) and no special edge lies on the cycle |
| T12-05 | V2x | load | rejected | `cross-defeating-invention`: rule `rc` changes the frontier (`X` to `Z`); no automatic remedy; message mentions "2 incompatible choices" |
| T12-06 | V3, budget `depth=3` | `?(Y,X) :- managerOf(Y,X).` | `(alice,bob)`, `(m^k(alice), m^(k-1)(alice))` and `(m^k(carol), m^(k-1)(carol))` for k = 1..3 (7 tuples) | NOT-GUARANTEED; analyser NOT-CERTIFIED with witness `manager(manager(X))` via `r3` and R |
| T12-07 | V3, budget `depth=3`, mode `constants` | `?(X) :- employee(X).` | `alice`, `bob`, `carol` | NOT-GUARANTEED (in fact complete, OP-14) |
| T12-08 | V4 | load | rejected | `self-defeating-invention` (as T12-03); analyser *also* reports the r3 termination witness for the remedied program |
| T12-09 | V5 | `?(Y,X) :- managerOf(Y,X).` | `(alice,bob)`, `(dave,carol)`, `(erin,dave)`, `(m(alice),alice)`, `(m(erin),erin)` | COMPLETE-STATIC; `carol` and `dave` get no invented manager |
| T12-10 | V6a | `Q6a`, `Q6b` | both `{alice, carol}` | COMPLETE-STATIC |
| T12-11 | V6b, budget `depth=3` | `Q6a` | sound subset of `{alice, carol, m(alice), m(carol), m²(alice), m²(carol), …}`, at least `{alice, carol, m(alice), m(carol), m²(alice), m²(carol)}` | NOT-GUARANTEED |
| T12-12 | V6b, budget `depth=3` | `Q6c` | **nothing returned** (the budgeted evaluation would wrongly yield `m³(alice)`, `m³(carol)`) | UNKNOWN; blocking unit `{employee, managerOf}`, strict edge `q6c → hasMgr` |
| T12-13 | V6b | `?() :- q6b(alice).` by goal-directed evaluation | `true` | COMPLETE-DYNAMIC |
| T12-14 | V1 + `r0` removed | `?(Y,X) :- managerOf(Y,X).` | `(m(alice),alice)`, `(m(carol),carol)` (bob has a boss but no `managerOf`) | COMPLETE-STATIC; documents why `r0` matters |

Oracle notes:
- T12-01, T12-09 and T12-10: clingo, plain encoding, and Nemo, constant answers only.
- T12-03, T12-04, T12-05 and T12-08: clingo `se` encoding for the intended model.
- T12-11 and T12-12: clingo with the depth guard shows the budget effect (Appendix A).

---

## 10. Sources

Verified this session through search snippets ([V]):
- Krötzsch, Marx, Rudolph. *The Power of the Terminating Chase.* ICDT 2019. [iccl.inf.tu-dresden.de/web/Inproceedings3203](https://iccl.inf.tu-dresden.de/web/Inproceedings3203/en)
- Carral, Dragoste, Krötzsch. *Restricted Chase (Non)Termination for Existential Rules with Disjunctions.* IJCAI 2017, 922–928. [ijcai.org/proceedings/2017/0128.pdf](https://www.ijcai.org/proceedings/2017/0128.pdf)
- Gerlach, Carral. *Do Repeat Yourself: Understanding Sufficient Conditions for Restricted Chase Non-Termination.* KR 2023 (arXiv 2309.12710). Title only.
- Carral, Gerlach, Larroque, Thomazo. *Restricted Chase Termination: You Want More than Fairness.* PACMMOD / PODS 2025. [dl.acm.org/doi/10.1145/3725246](https://dl.acm.org/doi/10.1145/3725246)
- Magka, Krötzsch, Horrocks. *Computing Stable Models for Nonmonotonic Existential Rules.* IJCAI 2013. [ijcai.org/Abstract/13/157](https://www.ijcai.org/Abstract/13/157)
- Baget, Garcia, Garreau, Lefèvre, Rocher, Stéphan. *Bringing existential variables in answer set programming and bringing non-monotony in existential rules: two sides of the same coin.* AMAI 82:3–41, 2018. [link.springer.com/article/10.1007/s10472-017-9563-9](https://link.springer.com/article/10.1007/s10472-017-9563-9)
- Ellmauthaler, Krötzsch, Mennicke. *Answering Queries with Negation over Existential Rules.* AAAI 2022, 36(5):5626–5633. [ojs.aaai.org/index.php/AAAI/article/view/20503](https://ojs.aaai.org/index.php/AAAI/article/view/20503)
- Krötzsch. *Computing Cores for Existential Rules with the Standard Chase and ASP.* KR 2020, 603–613. [iccl.inf.tu-dresden.de/web/Inproceedings3249](https://iccl.inf.tu-dresden.de/web/Inproceedings3249/en). Content about the chase encoding is [U].
- Abiteboul, Vianu. *Datalog extensions for database queries and updates.* JCSS 43:62–124, 1991. [sciencedirect.com/science/article/pii/002200009190032Z](https://www.sciencedirect.com/science/article/pii/002200009190032Z)
- Nemo source, [github.com/knowsys/nemo](https://github.com/knowsys/nemo), commit `e578c283` (2026-08-20). Files `nemo/src/execution/selection_strategy/strategy_stratified_negation.rs`, `nemo/src/util/labeled_graph.rs`, `nemo/src/execution.rs` (`DefaultExecutionStrategy = StrategyStratifiedNegation<StrategyRoundRobin>`).

Not re-checked ([U]):
- Alviano, Morak, Pieris, *Stable model semantics for tuple-generating dependencies revisited*, PODS 2017. Under its first-order stable-model semantics, V2 should also have no stable model, by the same minimality argument [U-own].
- Gottlob, Hernich, Kupke, Lukasiewicz: well-founded semantics for guarded existential rules with negation (AAAI 2012, KR 2014).

---

## Appendix A: clingo (ASP) programs and outputs

Environment: clingo 5.8.2 (`pip install clingo`), driven by `run.py`. `run.py`:
- loads `base.lp`, one of `R_plain.lp` / `R_selfexempt.lp`, and the variant files;
- enumerates all stable models;
- computes the WFS by the alternating fixpoint on the ground program, obtained through clingo's ground-program observer.

The directory is `scratchpad/negation/asp/`; it is not committed.

```
% base.lp
person(alice;bob;carol). employee(alice;bob;carol).
hasBoss(bob,alice).
managerOf(B,X) :- hasBoss(X,B).
hb(X) :- hasBoss(X,_).

% R_plain.lp        (F2 reading of R)
managerOf(manager(X),X) :- employee(X), not hb(X), ok(X).

% R_selfexempt.lp   (se encoding, restricted-chase reading)
managerOf(manager(X),X) :- employee(X), not hbOther(X), ok(X).
hbOther(X) :- hasBoss(X,Y), Y != manager(X).

% nobound.lp
ok(X) :- employee(X).

% bound.lp          (depth budget, V3/V4/V6b)
#const k=3.
depth(C,0) :- person(C).
depth(manager(X),D+1) :- depth(X,D), D < k.
ok(X) :- employee(X), depth(X,D), D < k.
cut(X) :- employee(X), depth(X,k).

% v2.lp
hasBoss(X,Y) :- managerOf(Y,X).
% v3.lp
employee(Y) :- managerOf(Y,X).
% v2x.lp
colleague(alice,carol). colleague(carol,alice).
managerOf(Y,Z) :- managerOf(Y,X), colleague(X,Z).
% v5.lp
person(dave;erin). employee(dave;erin).
worksIn(carol,sales). worksIn(dave,sales). head(dave,sales).
subDept(sales,hq). head(erin,hq). worksIn(erin,hq).
hasBoss(X,H)  :- worksIn(X,D), head(H,D), X != H.
hasBoss(H,H2) :- head(H,D), subDept(D,P), head(H2,P).
% v6.lp
q6a(X) :- managerOf(Y,X), not person(Y).
knownManager(X) :- managerOf(Y,X), person(Y).
q6b(X) :- employee(X), not knownManager(X).
hasMgr(X) :- managerOf(_,X).
q6c(X) :- employee(X), not hasMgr(X).
```

Variant composition:

| Variant | Files |
|---|---|
| V1 | base + R + nobound |
| V2 | + v2 |
| V2x | + v2 + v2x |
| V3 | base + R + bound + v3 |
| V4 | + v2 + v3 (bound) |
| V5 | + nobound + v5 |
| V6a | V1 + v6 |
| V6b | V3 + v6 |

Output, condensed: `employee/1` atoms are dropped, and `m` = `manager`. `SM` lists the stable models; `WFS-U` lists the undefined atoms (`-` means the WFS is total and equals the unique stable model).

```
V1  plain: SM=1 {hasBoss(bob,alice) managerOf(alice,bob) managerOf(m(alice),alice) managerOf(m(carol),carol)}  WFS-U: -
V1  se   : SM=1 (same)                                                                                         WFS-U: -
V2  plain: SM=0   WFS true: hasBoss(bob,alice) managerOf(alice,bob)
                  WFS-U: hasBoss(alice,m(alice)) hasBoss(carol,m(carol)) managerOf(m(alice),alice) managerOf(m(carol),carol)
V2  se   : SM=1 {hasBoss(alice,m(alice)) hasBoss(bob,alice) hasBoss(carol,m(carol))
                 managerOf(alice,bob) managerOf(m(alice),alice) managerOf(m(carol),carol)}                     WFS-U: -
V2x plain: SM=0   WFS-U: all 8 atoms managerOf(m(a|c), a|c), hasBoss(a|c, m(a|c))
V2x se   : SM=2 SM1 {… managerOf(m(alice),alice) managerOf(m(alice),carol) hasBoss(alice,m(alice)) hasBoss(carol,m(alice))}
                SM2 {… managerOf(m(carol),alice) managerOf(m(carol),carol) hasBoss(alice,m(carol)) hasBoss(carol,m(carol))}
                cautious ∩ = {hasBoss(bob,alice) managerOf(alice,bob)}; WFS-U: the same 8 atoms
V3  plain: SM=1 {hasBoss(bob,alice) managerOf(alice,bob) managerOf(m^k(x), m^(k-1)(x)) for x∈{alice,carol}, k=1..3
                 cut(m³(alice)) cut(m³(carol))}                                                                WFS-U: -
V3  se   : SM=1 (same)                                                                                         WFS-U: -
V3  plain, unbounded (nobound.lp): grounding killed after 20 s (exit 124)
V4  plain: SM=0   WFS true: hasBoss(bob,alice) managerOf(alice,bob); WFS-U: every invented atom at every depth + cut/1
V4  se   : SM=1 {V3 model + hasBoss mirror of every managerOf}                                                 WFS-U: -
V5  plain: SM=1 {hasBoss(bob,alice) hasBoss(carol,dave) hasBoss(dave,erin) managerOf(alice,bob) managerOf(dave,carol)
                 managerOf(erin,dave) managerOf(m(alice),alice) managerOf(m(erin),erin)}                       WFS-U: -
V5  se   : SM=1 (same)                                                                                         WFS-U: -
V6a both : SM=1 {V1 model + q6a(alice) q6a(carol) q6b(alice) q6b(carol)}                                     WFS-U: -
V6b both : SM=1 {V3 model + q6a(x), x∈{alice,carol,m(alice),m(carol),m²(alice),m²(carol)}
                 + q6b(x), x∈{alice,carol,m^1..3(alice),m^1..3(carol)}
                 + q6c(m³(alice)) q6c(m³(carol))   <- spurious, artefact of the budget}                        WFS-U: -
```

## Appendix B: chase simulator

`scratchpad/negation/chase/chase.py` (about 120 lines, not committed) implements:
- restricted and semi-oblivious chase over atoms, with Skolem-named nulls `n_R(x)`;
- negated body atoms evaluated on the current instance, for form (b3);
- a depth budget `K`;
- five exploration modes:
  - `all`: every sequence of single-trigger applications, memoised on states;
  - `all-df`: every sequence in which Datalog triggers have priority;
  - `datalog-first` and `exist-first`: one deterministic sequence each;
  - `parallel-df`: Nemo-like; the first rule with active triggers, Datalog first, fires all of them at once.

Rules, with `R` listed first on purpose:

```
r0: managerOf(B,X) :- hasBoss(X,B)        hb: hb(X) :- hasBoss(X,B)
R∃: managerOf(Y,X) :- employee(X)                        (Y existential)
R¬: managerOf(Y,X) :- employee(X), not hb(X)             (form b3)
r2: hasBoss(X,Y) :- managerOf(Y,X)        r3: employee(Y) :- managerOf(Y,X)
rc: managerOf(Y,Z) :- managerOf(Y,X), colleague(X,Z)
w1: hasBoss(X,H) :- worksIn(X,D), head(H,D), X != H
w2: hasBoss(H,H2) :- head(H,D), subDept(D,P), head(H2,P)
```

Output, condensed: number of distinct final instances per mode. `K=3`, except `all`/`all-df` on V4, where `K=2` avoids a state explosion.

```
            all  all-df  DF  exist-first  parallel-df  semi-obl.   notes
V1  R∃        2     1     1   +n(bob)      =DF          +n(bob)     Q6a: DF {alice,carol}; exist-first/semi-obl. {alice,bob,carol}
V1  R¬        2     1     1   +n(bob)      =DF          =DF         eager negation: bob invented if R fires before hb(bob)
V2  R∃        2     1     1   +n(bob)      =DF          +n(bob)
V2x R∃        6     2     1   all nulls    n(a),n(c)    3 nulls     all-df results: {n(alice)→alice,carol} and {n(carol)→alice,carol}
V3  R∃        2     1     1   +bob chain   =DF          +bob chain  budget cut at depth 3 (2 cuts for DF, 3 otherwise)
V4  R∃        2     1     1   +bob chain   =DF          +bob chain  (K=2 for all/all-df)
V5  R∃        8     1     1   5 nulls      =DF          5 nulls     DF: nulls only for alice, erin
```

`R¬` rows for V2 to V5 have the same counts as `R∃`. The full output is in `chase/results_chase.txt`.

## Appendix C: Nemo

Build: `git clone --depth 1 https://github.com/knowsys/nemo` (commit `e578c283`, version 0.10.2-dev), then `cargo build --release -p nemo-cli`. The build took about 5 minutes and nothing was installed or committed. Command: `nmo -e none --print-facts idb <file>.rls`, with a 15 s timeout.

Programs (Nemo syntax; `!Y` is existential and `~` is negation). All share:

```
employee(alice). employee(bob). employee(carol). person(alice). person(bob). person(carol).
hasBoss(bob, alice).
```

| File | Rules added (in this order) |
|---|---|
| V1_ex | `managerOf(!Y, ?X) :- employee(?X).` `managerOf(?B, ?X) :- hasBoss(?X, ?B).` |
| V1_neg | `managerOf(!Y, ?X) :- employee(?X), ~hb(?X).` `hb(?X) :- hasBoss(?X, ?B).` + r0 |
| V2_ex / V2_neg | V1_ex / V1_neg + `hasBoss(?X, ?Y) :- managerOf(?Y, ?X).` |
| V2_ex_Rlast | V2_ex with the existential rule moved last |
| V2x_ex | V2_ex + `colleague(alice, carol). colleague(carol, alice).` `managerOf(?Y, ?Z) :- managerOf(?Y, ?X), colleague(?X, ?Z).` |
| V3_ex / V3_neg | V1_ex / V1_neg + `employee(?Y) :- managerOf(?Y, ?X).` |
| V4_ex | V2_ex + r3 |
| V5_ex / V5_neg | + V5 data and `hasBoss(?X, ?H) :- worksIn(?X, ?D), head(?H, ?D), ?X != ?H.` `hasBoss(?H, ?H2) :- head(?H, ?D), subDept(?D, ?P), head(?H2, ?P).` |
| V6a_ex / V6a_neg | + `q6a(?X) :- managerOf(?Y, ?X), ~person(?Y).` `knownManager(?X) :- managerOf(?Y, ?X), person(?Y).` `q6b(?X) :- employee(?X), ~knownManager(?X).` |

Output (`managerOf` facts; `_:n` are Nemo nulls):

```
V1_ex        managerOf(alice,bob) managerOf(_:0,alice) managerOf(_:1,carol)                      (= DF = F2)
V1_neg       same; hb(bob)
V2_ex        managerOf(alice,bob) managerOf(_:0,alice) managerOf(_:1,bob) managerOf(_:2,carol)   (redundant null for bob)
V2_ex_Rlast  same as V2_ex (textual order irrelevant)
V2_neg       ERROR nmo: The rules of the program are not stratified.   (exit 1)
V2x_ex       managerOf(alice,bob) _:0→{alice,carol} _:1→bob _:2→{alice,carol}                  (parallel firing, non-core)
V3_ex, V3_neg, V4_ex   killed after 15 s (exit 124): non-termination
V5_ex/_neg   managerOf(alice,bob) (dave,carol) (erin,dave) (_:0,alice) (_:1,erin)                (= DF = F2)
V6a_ex/_neg  q6a = {alice, carol}, q6b = {alice, carol}, knownManager = {bob}                    (= F2)
```

Every Nemo run prints `generated predicate: _SATISFIED_0`: this is the helper table of the restricted check, i.e. the implicit negation of the existential head.
