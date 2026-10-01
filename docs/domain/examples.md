# Examples

Small pedagogical examples reused across the reference, so that pages illustrate notions on shared, well-understood cases instead of inventing new ones each time. Each example states its language fragment, its intended reading and its expected results under the relevant semantics. Program syntax follows [notation §8](notation.md#8-program-syntax-in-examples) (Datalog/ASP style; DLGP style when marked).

| Example | Fragment | Illustrates |
|---|---|---|
| [Chain of command](#ex-chain) | positive Datalog, recursion | least model, semi-naive evaluation, SCCs, CQ answering |
| [Default conditions](#ex-default) | stratified negation | closed world, strata, perfect model |
| [Basket threshold](#ex-basket) | aggregation, exact decimals | grouping, empty groups, exact comparison, rounding |
| [Every employee has a manager](#ex-manager) | value invention | existential variables vs Skolem terms, identity of invented individuals |
| [Managers are employees](#ex-manager-chain) | value invention + recursion | non-terminating chase, infinite models, why negation on data does not stop invention |
| [Teaching ontology](#ex-teaching) | existential rules, DL-style | certain answers, query rewriting, universal models |

<a id="ex-chain"></a>
## Chain of command (positive Datalog)

```prolog
managerOf(anna, tom).  managerOf(dir, anna).  managerOf(dir, bob).

superiorOf(Y, X) :- managerOf(Y, X).
superiorOf(Z, X) :- superiorOf(Y, X), managerOf(Z, Y).
sameHead(X1, X2) :- superiorOf(Y, X1), superiorOf(Y, X2).
```

Query: `q(s) = superiorOf(s, tom)`.

- **Least model**: `superiorOf = {(anna, tom), (dir, anna), (dir, bob), (dir, tom)}`; `sameHead` relates all of `tom, anna, bob` pairwise (including reflexive pairs), since they share `dir`.
- **Answers**: `{anna, dir}`. For positive Datalog, the answers on the least model are exactly the certain answers.
- **Illustrates**: one recursive component (`superiorOf`) below a non-recursive rule (`sameHead`); semi-naive evaluation needs two rounds of new `superiorOf` facts; the query is evaluated by homomorphism search.

<a id="ex-default"></a>
## Default conditions (stratified negation)

"If no specific condition applies to a contract, the general conditions apply."

```prolog
contract(c1). contract(c2). contract(c3).
customerOf(c2, k7). frameworkAgreement(k7).
derogation(c3).

specificConditionApplies(C) :- contract(C), customerOf(C, K), frameworkAgreement(K).
specificConditionApplies(C) :- derogation(C).
generalConditionsApply(C)   :- contract(C), not specificConditionApplies(C).
```

- **Strata**: (1) facts; (2) `specificConditionApplies`; (3) `generalConditionsApply`, which depends negatively on (2).
- **Perfect model**: `generalConditionsApply(c1)` only. Stable-model and well-founded semantics give the same result (the program is stratified).
- **Open world**: read as first-order formulas with `¬` instead of `not`, the knowledge base entails nothing about `c1`. The conclusion relies on the closed-world reading of `specificConditionApplies`.
- **Pitfall**: if `specificConditionApplies` were computed only partially (interrupted computation), `generalConditionsApply` could contain wrong answers, not just miss some.

<a id="ex-basket"></a>
## Basket threshold (aggregation, exact decimals)

"A basket whose total exceeds 200.00 gets free delivery."

```prolog
basket(b1). basket(b2). basket(b3). basket(b4). basket(b5).
price(i1, 75.50). price(i2, 60.00). price(i3, 66.67). price(i4, 40.00).
inBasket(b1, i1, 2). inBasket(b1, i2, 1).     % 151.00 + 60.00 = 211.00
inBasket(b2, i1, 1).                          % 75.50
inBasket(b4, i3, 3).                          % 200.01
inBasket(b5, i4, 5).                          % 200.00 (boundary)
                                              % b3: empty basket
total(B, S)     :- basket(B), S = #sum{ P*Q, I : inBasket(B, I, Q), price(I, P) }.
freeDelivery(B) :- total(B, S), S > 200.00.
```

- **Answers**: `freeDelivery = {b1, b4}`. `b5` is excluded because `200.00 > 200.00` is false under exact arithmetic; with binary floating point, `3 * 66.67` and similar sums may not be represented exactly.
- **Empty group**: whether `total(b3, 0)` exists depends on the aggregate semantics. In ASP-Core-2, the group-by variable `B` is bound by `basket(B)` and the sum over an empty set is `0`; under SQL `GROUP BY` semantics there is no row for `b3`.
- **Tuple keys**: the collection is a set of tuples `(P*Q, I)`; without the key `I`, two items with the same amount would be collapsed into one.
- **Rounding**: a 5% discount `S * 0.95` rounded to two decimals shows why the rounding mode must be stated:

  | Basket | Exact `S * 0.95` | floor | ceiling | truncate | half-up | half-even |
  |---|---|---|---|---|---|---|
  | b2 | 71.725 | 71.72 | 71.73 | 71.72 | 71.73 | 71.72 |
  | b5 | 190.00 | 190.00 | 190.00 | 190.00 | 190.00 | 190.00 |
  | b4 | 190.0095 | 190.00 | 190.01 | 190.00 | 190.01 | 190.01 |

  On negative values, floor and truncate differ, and "half-up" is ambiguous (towards `+∞` or away from zero): see [exact decimals and rounding](concepts/exact-decimals-and-rounding.md).
- **Running it in systems with integer aggregates only**: scale amounts to cents.

<a id="ex-manager"></a>
## Every employee has a manager (value invention)

"Every employee who is not the company director has a line manager."

```
employee(x) ∧ not isCompanyDirector(x) → ∃y managerOf(y, x)
```

with data `employee(tom). employee(anna). employee(dir). isCompanyDirector(dir).`

Two standard readings:

```prolog
% (a) existential rule (DLGP style; Y existential), default negation on a data predicate
managerOf(Y, X) :- employee(X), not isCompanyDirector(X).      % Y existential

% (b) Skolem function: the manager of X is the term manager(X)
managerOf(manager(X), X) :- employee(X), not isCompanyDirector(X).
```

- **(a)** The chase creates labelled nulls: `managerOf(n1, tom)`, `managerOf(n2, anna)`. Certain answers to `q(y) = managerOf(y, tom)` are empty: the manager exists but is unknown. The Boolean query `∃y managerOf(y, tom)` is entailed.
- **(b)** The model contains `managerOf(manager(tom), tom)` and `managerOf(manager(anna), anna)`. Under the Herbrand reading, `manager(tom)` is an answer to `q(y)`, and it is a different individual from `manager(anna)` and from every constant.
- **Data already recording a manager**: with `managerOf(dir, anna)` given, the restricted chase does not invent a manager for `anna` in reading (a) (the head is already satisfied). Reading (b) still produces `manager(anna)`. Avoiding this needs an explicit pattern, see [value-invention strategies](concepts/value-invention-strategies.md).

<a id="ex-manager-chain"></a>
## Managers are employees (non-terminating invention)

Add to the previous example:

```prolog
employee(Y) :- managerOf(Y, X).
```

- Reading (b): `employee(manager(tom))` holds; `isCompanyDirector(manager(tom))` is false (the invented term is not the constant `dir`), so the first rule fires again and produces `manager(manager(tom))`, and so on. The least (perfect) model is **infinite**.
- Reading (a): every chase variant also diverges, because the invented nulls are never `dir`. The restricted chase does not help: no existing individual satisfies the head for the new employee.
- **Intended meaning versus formal meaning**: a modeller usually means "the chain stops at the director". Expressing it requires identifying some invented individual with the constant `dir`, i.e. equality reasoning ([equality and UNA](concepts/equality-and-una.md)), or a different modelling.
- **Static analysis**: the rule set is not weakly acyclic and not MFA (the MFA check produces the cyclic term `manager(manager(x))`). See [chase termination](concepts/chase-termination.md), [decidability classes](concepts/decidability-classes.md), [blocking and finite representations](algorithms/blocking-and-finite-representations.md).
- **Partial results**: a computation stopped after `k` levels is sound for positive queries but may be incomplete. A query that negates `employee` or `managerOf` may get wrong answers. See [soundness and completeness of partial results](concepts/soundness-and-completeness-of-partial-results.md).

<a id="ex-teaching"></a>
## Teaching ontology (existential rules, rewriting)

```
professor(x) → ∃z teaches(x, z) ∧ course(z)
teaches(x, z) → teacher(x)
teacher(x) → person(x)
```

Data: `professor(alice). teaches(bob, logic101).`

- **Chase**: adds `teaches(alice, n1)`, `course(n1)`, `teacher(alice)`, `teacher(bob)`, `person(alice)`, `person(bob)`. It terminates (weakly acyclic).
- **Certain answers** of `q(x) = person(x)`: `{alice, bob}`. Of `q(x, z) = teaches(x, z)`: `{(bob, logic101)}` only; `(alice, n1)` is not a certain answer.
- **Rewriting** `q(x) = ∃z teaches(x, z)` gives the UCQ `teaches(x, z) ∨ professor(x)`, evaluated directly on the data without materialisation.
- **DL reading**: `Professor ⊑ ∃teaches.Course`, `∃teaches.⊤ ⊑ Teacher`, `Teacher ⊑ Person` (see [description logics and OWL](adjacent/description-logics-and-owl.md)).

## Related pages

- [foundations](concepts/foundations.md), [notation](notation.md), [Datalog](concepts/datalog.md), [stratified negation](concepts/stratified-negation.md), [aggregation](concepts/aggregation.md), [existential rules](concepts/existential-rules.md), [Skolem functions and terms](concepts/skolem-functions-and-terms.md).

## References

- Default negation and stratification: K. R. Apt, H. A. Blair, A. Walker. *Towards a theory of declarative knowledge*. In *Foundations of Deductive Databases and Logic Programming*, Morgan Kaufmann, 1988.
- Aggregates in ASP: ASP-Core-2 input language format, TPLP 20(2), 2020.
- Existential rules and the chase: R. Fagin, P. G. Kolaitis, R. J. Miller, L. Popa. *Data exchange: semantics and query answering*. TCS 336(1), 2005.
- MFA: B. Cuenca Grau, I. Horrocks, M. Krötzsch, C. Kupke, D. Magka, B. Motik, Z. Wang. *Acyclicity notions for existential rules and their application to query answering in ontologies*. JAIR 47, 2013.
