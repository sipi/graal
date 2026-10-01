# Running examples

The business examples used throughout the project (E1-ex, E2-ex, E3-ex), written in the provisional syntax, with their expected behaviour, plus one positive-Datalog example for v0. Every wiki page should illustrate its topic with one of these before inventing a new example. The README descriptions are authoritative; the code below is illustrative and uses the **provisional** syntax of [report 11](../../preliminary-analysis/11-f2-framework-definition.md).

> **Status in this project:** `v0` (v0-ex only) `F2` (E1-ex, E2-ex, E3-ex) `open` (E3-ex intent, [Q1](open-questions.md#q1)) — README *Running business examples* and *Corrections*.
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

Naming: `E1-ex`..`E3-ex` are examples; `E1`..`E12` are requirements ([requirements](requirements.md)).

<a id="v0-ex"></a>
## v0-ex: chain of command (positive Datalog)

Introduced by the wiki as the v0 illustration (not an owner example). It is the positive, recorded-data part of E3-ex.

```prolog
managerOf(anna, tom).  managerOf(dir, anna).  managerOf(dir, bob).

superiorOf(Y, X) :- managerOf(Y, X).
superiorOf(Z, X) :- superiorOf(Y, X), managerOf(Z, Y).
sameDepartmentHead(X1, X2) :- superiorOf(Y, X1), superiorOf(Y, X2).

?(S) :- superiorOf(S, tom).
```

- **Least model:** `superiorOf` = `{(anna,tom), (dir,anna), (dir,bob), (dir,tom)}`; `sameDepartmentHead` pairs all of `tom, anna, bob` sharing `dir`, including reflexive pairs.
- **Answers:** `?(S)` → `{anna, dir}`, status COMPLETE-STATIC (Datalog always terminates).
- **Exercises:** semi-naive evaluation (recursive SCC `{superiorOf}`), joins, GRD with one recursive SCC and one non-recursive rule above it, homomorphism search for the query.

<a id="e1-ex"></a>
## E1-ex: default and exception (stratified negation, closed world)

README: "If no specific condition applies then general conditions apply."

```prolog
contract(c1). contract(c2). contract(c3).
customerOf(c2, k7). frameworkAgreement(k7).
derogation(c3).

specificConditionApplies(C) :- contract(C), customerOf(C, K), frameworkAgreement(K).
specificConditionApplies(C) :- derogation(C).
generalConditionsApply(C)   :- contract(C), not specificConditionApplies(C).

?(C) :- generalConditionsApply(C).
```

- **Strata:** facts; `specificConditionApplies`; `generalConditionsApply` (negative edge).
- **Perfect model / answer:** `generalConditionsApply(c1)` only; COMPLETE-STATIC.
- **Why it matters:** the negation is read under the **closed world** (`c1` has no recorded specific condition, so none applies). Under first-order (open-world) semantics this rule would not let us conclude anything about `c1`. See [stratified negation](../concepts/stratified-negation.md).
- **F2 variant** (report 11 §10.1): `appliedTerms(C, generalTerms(C)) :- generalConditionsApply(C).` creates a named object in the default branch.
- **D5 note:** `generalConditionsApply` depends on negation, so no stored rewriting; the hybrid strategy materialises `specificConditionApplies` and evaluates `contract(C), not specificConditionApplies(C)`.

<a id="e2-ex"></a>
## E2-ex: basket > 200€ gives free delivery (aggregation, exact decimals)

```prolog
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

- **Answers:** `freeDelivery` = `{b1, b4}`. `b5` is excluded because `200.00 > 200.00` is false **exactly** (no binary floats); `b3` has total `0` (grounded group, report 11 §4.4, OP-9 `[choice]`).
- **Tuple keys:** the collection is a set of tuples `(P*Q, I)`, so two items with the same amount are not collapsed.
- **Rounding (D7).** Rounding is chosen by the modeller among `floor`, `round` (half-up), `bank_round` (half-even); the concrete syntax is open. Illustration with a 5% discount, `D = S * 0.95` rounded to 2 decimals:

  | Basket | Exact `S * 0.95` | `floor` | `round` | `bank_round` |
  |---|---|---|---|---|
  | b2 | 71.725 | 71.72 | 71.73 | 71.72 |
  | b5 | 190.00 | 190.00 | 190.00 | 190.00 |
  | b4 | 190.0095 | 190.00 | 190.01 | 190.01 |

  (Behaviour on negative amounts is not fixed by D7; see [exact decimals](../concepts/exact-decimals-and-rounding.md).)
- **Oracle:** clingo with prices scaled to cents (integer aggregates).

<a id="e3-ex"></a>
## E3-ex: every employee has a line manager (value invention)

### Corrected reading (README Corrections, 2026-10-01)

The owner's rule:

```
∀x  employee(x) ∧ ¬isCompanyDirector(x) → ∃y managerOf(y, x)
```

Negation is on a **data** predicate; it is meant to stop the manager chain at the company director, the only employee without a boss. Two readings:

```prolog
% (a) existential reading (F1/F3 style; not F2 syntax). Y is existential.
managerOf(Y, X) :- employee(X), not isCompanyDirector(X).   % Y existential

% (b) named Skolem reading (F2, D1)
@function manager/1.
managerOf(manager(X), X) :- employee(X), not isCompanyDirector(X).   % r1
```

Data:

```prolog
employee(tom). employee(anna). employee(dir).
isCompanyDirector(dir).
```

Without further rules, F2 gives `managerOf(manager(tom), tom)` and `managerOf(manager(anna), anna)`: finite, stratifiable (`isCompanyDirector` is data only), COMPLETE-STATIC.

### The trap: "every manager is an employee"

```prolog
employee(Y) :- managerOf(Y, X).                                         % r2
```

- `employee(manager(tom))` holds, and `isCompanyDirector(manager(tom))` is false (an invented term is never the constant `dir`), so r1 fires again: `manager(manager(tom))`, and so on. **The model is infinite**; the chain never reaches the director.
- The same happens with existential variables (any chase variant): invented nulls are never `dir`.
- Recorded managers do not help in reading (b): with a fact `managerOf(dir, anna)`, r1 still invents `manager(anna)` (no lookup). The restricted chase in reading (a) would skip anna's invention, but tom's chain still diverges.
- **Analyser expectation:** the SCC `{employee, managerOf}` is stratifiable but NOT-CERTIFIED (WA/JA/MFA fail; MFA witness `manager(manager(X))`). This is **the first test case for the analyser** (README).
- **Statuses under a depth budget** (report 11 §6): positive queries NOT-GUARANTEED; queries negating or aggregating over `employee` or `managerOf` UNKNOWN (rule N1). Goal-directed evaluation may still complete some ground queries (report 11 §10.4 shows this on the lookup variant).
- **Intended meaning is not expressible** without equality between invented terms and constants (D8 caveat). This is open question [Q1](open-questions.md#q1) (leads: depth guard, blocking, modeller diagnostics).

### Related variants in the reports (not the owner's rule)

- **Lookup-before-invent variant** ([report 11 §10.3-10.4](../../preliminary-analysis/11-f2-framework-definition.md)): `@lookup manager(X) = Y :- recordedManager(X, Y).` with `hasManager(X, manager(X)) :- employee(X).` Chains close when recorded data closes them (COMPLETE-DYNAMIC), otherwise the same trap.
- **`hasBoss` variants V1-V6** ([report 12](../../preliminary-analysis/12-invention-under-negation.md)): built on a misreading (`not hasBoss(X,_)`), kept as test scenarios T12-01..14.
- Report 09 uses `employee(X) -> hasManager(X, manager(X))` without negation. See [known inconsistencies](open-questions.md#known-inconsistencies) (I3).

## Related pages

- [Q1](open-questions.md#q1), [Skolem functions](../concepts/skolem-functions-and-terms.md), [lookup-before-invent](../concepts/lookup-before-invent.md), [chase termination](../concepts/chase-termination.md), [completeness statuses](../concepts/completeness-statuses.md), [test strategy](../engineering/test-strategy.md).

## References

- [README, Running business examples, Corrections, Q1](../../preliminary-analysis/README.md#running-business-examples).
- [Report 11 §10](../../preliminary-analysis/11-f2-framework-definition.md) (worked examples), [report 12](../../preliminary-analysis/12-invention-under-negation.md), [report 09](../../preliminary-analysis/09-skolem-function-frameworks.md) (running examples section).
