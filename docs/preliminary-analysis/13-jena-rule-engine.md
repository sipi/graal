# 13: Apache Jena rule engine (hybrid forward/backward reasoning, `makeSkolem`, `noValue`)

**Status: study, input for owner validation.** The project owner asked to add Apache Jena's rule engine to the engines taken into account ([report 06](06-sota-engines.md)), noting that it has a builtin that creates "Skolem constants". This report describes the engine's architecture and rule language and documents the value-invention and negation builtins precisely. It then runs the running examples (E1-ex, E2-ex, E3-ex, and Q1) on Jena 6.2.0, lists what is worth borrowing, and adds Jena rows to the comparison matrix of report 06. Nothing here changes an earlier report or decision.

Status tags:
- **[S]**: checked in the Jena source code this session (`apache/jena`, `main` at commit `00b22ca`, 2026-10-01; package `org.apache.jena.reasoner.rulesys` and the rule files `jena-core/src/main/resources/etc/*.rules`).
- **[D]**: from the official documentation, "Reasoners and rule engines: Jena inference support". jena.apache.org was blocked by the network proxy, so it was read from its source in `apache/jena-site`, `source/documentation/inference/__index.md`.
- **[E]**: checked empirically this session with `jena-core` 6.2.0 from Maven Central on OpenJDK 21. The program and full outputs are summarised in the appendix.
- **[U]**: from memory, not re-checked.

Version note: the request mentioned Jena 5.x. The current release is **6.2.0** (2026-07-27); 6.0.0 came out on 2026-01-27 and the last 5.x, 5.6.0, on 2025-10-10 [S, git tags]. The rule engine is functionally the same across 5.x and 6.x. Since 2023 the package has only received refactorings plus one bug fix (§6) [S]. Everything below therefore applies to both.

---

## 0. Summary

1. **Architecture.** `GenericRuleReasoner` has four modes over two engines: a forward RETE engine (incremental), a legacy forward engine, a backward Prolog-like LP engine with SLG-style tabling (XSB-like), and the default **hybrid** mode. In hybrid mode, forward rules are materialised first and may *generate instantiated backward rules*. Queries are then answered by the tabled backward engine over the data plus the forward deductions. The layering is strict: forward rules never see backward results [D, S]. The RDFS and OWL reasoners are rule files run on this machinery. A hard-wired transitive reasoner (TGC) handles `subClassOf`/`subPropertyOf` [D, S].
2. **`makeSkolem(?x, v1..vn)` is a hash-based, deterministic blank node.** The label is Base64(MD5(the serialised argument terms)) [S].
   - It is stable across rules, runs and JVMs [E].
   - There is **no function name**: `makeSkolem(?m, ?x)` in a "manager" rule and in an "address" rule returns the *same* node [E]. The modeller must add a tag constant by hand (`makeSkolem(?m, 'manager', ?x)`).
   - Hashing is on lexical forms, not values: `'1'^^xsd:int`, `'01'^^xsd:int`, `'1'^^xsd:integer` and `'1'^^xsd:decimal` give four different nodes, although `equal()` says they are equal [E].
   - The result is an opaque blank node, so the nesting `manager(manager(x))` is invisible to the engine.
   - `makeTemp` creates a fresh blank node per call. `makeInstance(x, p[, t], ?v)` is *memoised* per (x, p, t), and only in backward/hybrid rules [S, E].
3. **`noValue(s, p[, o])` is operational negation-as-failure with no stratification.** It succeeds iff no matching triple is *currently* visible [S, D]:
   - in forward rules, data plus the forward deductions so far;
   - in backward rules, data plus forward deductions only, never backward-derived triples.

   Results depend on firing order, and Jena does not check stratification:
   - **E1-ex gives a wrong answer in pure forward mode in all four permutations tried**: the default applies to a contract whose specific condition is derived [E].
   - A stratifiable 2-rule program gives the perfect model or a wrong model depending on rule order [E].
   - The **hybrid layering** (specific-condition rule forward, default rule backward) gives the right answer and stays right under updates [E]. This is the only principled way to use `noValue` in Jena, and it is exactly the D5 pattern (negation evaluated against a lower, materialised layer).
4. **E3-ex / Q1.** `employee(x), not isCompanyDirector(x) → managerOf(manager(x), x)` alone works in all modes [E]. With "every manager is an employee":
   - forward materialisation never terminates (no safeguard) [E];
   - backward mode with tabling answers the *bound* goal `(?m managerOf Tom)` in 0.06 s, because ground subgoals are cut after the first answer [S, E];
   - backward mode without tabling, or with an open goal `(?e type Employee)`, does not terminate [E].

   Jena has no termination analysis, depth guard or blocking.
5. **E2-ex.** There is **no aggregation** [D, S]. Worse, the arithmetic builtins silently **truncate `xsd:decimal` to an integer** (`Number.longValue()`): `120.50 + 80.25 = 200` [S, E]. A procedural running-total workaround with `remove` computes 200 instead of 200.85, so "basket > 200 € ⇒ free delivery" gives the **wrong decision** [E].
6. **To borrow**:
   - the hybrid architecture, extended so that the analyser chooses the layering instead of the rule author (E10, D5);
   - variant tabling plus ground-goal cut-off for goal-directed answering over infinite models (Q1, T12-13);
   - forward rules that compile specialised backward rules (rule specialisation, E8/E10);
   - derivation records (E8);
   - deterministic hash-based identifiers for *output* (D8), but with the function name and canonical value forms in the key, and with symbolic terms kept internally;
   - `makeInstance` + `noValue` as an existing lookup-before-invent precedent (D2).
7. **Not to borrow**: operational negation without a stratification check, untyped decimal arithmetic, untagged Skolem keys, no termination analysis. Jena is not a suitable oracle beyond positive Datalog, plus `noValue` used in hybrid layering.

---

## 1. Architecture

### 1.1 `GenericRuleReasoner` and its modes [D, S]

`GenericRuleReasoner` (`rulesys/GenericRuleReasoner.java`) takes a `List<Rule>` and a `RuleMode`:

| Mode | Inference graph | Engine | Notes |
|---|---|---|---|
| `forward` | `BasicForwardRuleInfGraph` | legacy non-RETE forward engine (`FRuleEngine`) | "identical external semantics", kept for performance in some cases [D] |
| `forwardRETE` | `RETERuleInfGraph` | `RETEEngine` (Forgy 1982) | Incremental: adding or removing a triple propagates only its consequences. All rules are run forward even if written `<-` [D]. Head terms that assert backward rules are ignored |
| `backward` | `LPBackwardRuleInfGraph` | `LPBRuleEngine` / `LPInterpreter` | Prolog-like SLD with backtracking, top-to-bottom and left-to-right, plus tabling. All rules are run backward. Backward rules have a single head triple [D] |
| `hybrid` (default) | `FBRuleInfGraph` | RETE forward, then LP backward | Forward rules (`->`) are materialised into a deductions graph. A forward rule whose head contains `[ head <- body ]` instantiates that backward rule with its bindings and adds it to the LP rule store. Queries go to the LP engine over raw data, forward deductions and the generated backward rules [D, S: `RETEConflictSet.execute` calls `infGraph.addBRule(r.instantiate(env))`] |

**Interaction between the modes** [D]:
- **The data flow has no loops.** "The backward rules are not employed when searching for matches to forward rule terms." Forward rules therefore form a lower layer and backward rules an upper layer. The documentation states the consequence: a relation used by forward rules must be derivable *forward* to be complete.
- **Updates.** Forward rules stay incremental, including the incremental assertion and retraction of generated backward rules. All LP tables are discarded on any update (`reset()`), and later queries rebuild them.

**Tabling** [D, S]:
- Tabling is **variant tabling** keyed on triple patterns (`Cache<TriplePattern, Generator>`, default capacity 524,288 goals, system property `jena.rulesys.lp.max_cached_tabled_goals`).
- It is declared per predicate by `table(P)` or globally by `tableAll()`. Any goal with a variable predicate is tabled as soon as one predicate is.
- A suspended consumer resumes when the producer generates new answers, "essentially the same as XSB's SLG".
- Generators for **ground goals are singletons** (`Generator.isSingleton = goal.isGround()`): they stop after the first answer. This matters for termination (§3.3).
- The documentation lists *subsumption* tabling as a future improvement. It has not been done.

**Other engine features**:
- **Derivations.** `setDerivationLogging(true)` records, for each forward-derived triple, the rule and the matched body triples (`RuleDerivation`). They are retrievable via `InfGraph.getDerivation(t)` and trace back to data [D, S].
- **Validation rules.** Rules derive `(?x rb:violation error('summary','description', args))`, which `validate()` collects into reports [D].
- **Functor filtering and `hide(p)`** keep internal control triples out of query results [D].
- **Preprocessing hooks.** Procedural passes run at `prepare()` [D].

### 1.2 Transitive reasoner and RDFS/OWL rule sets [D, S]

- **`TransitiveReasoner`** (package `reasoner.transitiveReasoner`) is hard-wired Java code for the `rdfs:subClassOf` and `rdfs:subPropertyOf` lattices. It stores them as graphs and answers both the closed relation and the *direct* (transitive reduction) relation. `GenericRuleReasoner.setTransitiveClosureCaching(true)` plugs it into hybrid mode as the "TGC".
- **RDFS**: `RDFSRuleReasoner` runs `etc/rdfs-fb-tgc*.rules` in hybrid mode with the TGC, at three levels (full, default, simple). `RDFSForwardRuleReasoner` uses `etc/rdfs.rules`.
- **OWL**: `OWLFBRuleReasoner` (`etc/owl-fb.rules`, 786 lines), `OWLMiniReasoner` and `OWLMicroReasoner` (`owl-fb-mini`/`-micro`). They are hybrid rule sets, plus an "OWL translation" hook that compiles intersection/union class definitions into rules.
  - They are instance-based, incomplete for OWL (e.g. no comprehension axioms) [D].
  - Validation is done by rules.
- **`owl:someValuesFrom` is a lookup-before-invent pattern** (`owl-fb.rules` l. 316–319) [S]:
  ```
  [some1: (?C rdfs:subClassOf some(?P, ?D)), noValue(?D rdfs:subClassOf ?C) ->
      [some1b: (?X ?P ?T) <- (?X rdf:type ?C), noValue(?X, ?P), makeInstance(?X, ?P, ?D, ?T)]
      [some1b2: (?T rdf:type ?D) <- (?X rdf:type ?C), makeInstance(?X, ?P, ?D, ?T)] ]
  ```
  An existential witness is created only when the individual has *no* recorded `?P` value: a hand-written D2 / restricted-chase-like check, evaluated against the lower layer. It is also a forward rule that compiles one backward rule per `someValuesFrom` axiom.
- **Class prototypes.** One prototype blank node per OWL class is created with `makeTemp` under a `noValue` guard (`prototype1`, l. 212) and hidden from results.

---

## 2. Rule language

### 2.1 Syntax [D]

```
Rule      := bare-rule .  |  [ bare-rule ]  |  [ ruleName : bare-rule ]
bare-rule := term, ... term -> hterm, ... hterm      // forward rule
          |  bhterm <- term, ... term                // backward rule
hterm     := term | [ bare-rule ]                    // a forward head may contain a backward rule
term      := (node, node, node) | (node, node, functor) | builtin(node, ... node)
functor   := functorName(node, ... node)             // structured literal
node      := uri-ref | prefix:localname | <uri> | ?var | 'literal' | 'lex'^^type | number
```

- **Data model: RDF triples only.** All predicates are binary, an n-ary predicate needs reification or functors, and variables may stand in predicate position. Semantically, every rule is a Datalog rule over a hidden `triple/3` predicate [D].
- **Rule files.** They accept `#` and `//` comments, `@prefix`, and `@include <url>` (including the keywords `RDFS`, `OWL`, `OWLMini`, `OWLMicro`).
- **Functors** are *structured literals*, not function symbols that build recursive terms. The documentation calls them flat ("not recursive"), "syntactic sugar for datalog" [D]. They are used to bundle OWL restriction components (`some(?P, ?D)`) and validation reports.
- **Builtins** are Java objects in a `BuiltinRegistry`. A builtin can act in the body, where it binds variables or tests, or in the head, where it performs an action. Users can register new builtins [D].

### 2.2 Value-invention builtins [S, E]

| Builtin | Where | Semantics (from source) | Deterministic? | Scope of identity |
|---|---|---|---|---|
| `makeSkolem(?x, v1, …, vn)` | body, all modes | Builds a key by concatenating, for each `vi`, a kind prefix and a lexical serialisation. URIs give `U<uri>`, blank nodes `B<label>`, literals `L<lex>[@lang][^^dtURI]`, anything else `O<toString>`. The key is hashed with **MD5**, and `?x` is bound to the blank node labelled `Base64(digest)` (`MakeSkolem.bodyCall`) | **Yes**: a pure function of the argument terms; the same label across rules, inference graphs and JVM runs [E: `_:UAxMYh23kvPg5OahC9TsCQ==` for `('manager', ex:Tom)` in every run] | **Global**: the key contains neither the rule nor a function name. Two rules calling `makeSkolem(?m, ?x)` for different purposes get the same node [E] |
| `makeTemp(?x)` | body, all modes (head: error) | `NodeFactory.createBlankNode()`: a fresh blank node per call | No: the backward mode gives a new node per query [E] | none |
| `makeInstance(?x, ?p, [?t,] ?v)` | body, **backward/hybrid only** (forward: `BuiltinException` [E]) | Looks up `TempNodeCache` keyed by (x, p) and, if given, class t; creates and caches a fresh blank node if absent | Within one inference graph: yes, even across `reset()` [E]. Across runs: no (random labels) | per (instance, property, class), per inference graph |

Consequences of the `makeSkolem` design:
- **It is a named Skolem function only by convention.** The "name" is whatever constant the modeller passes first (`'manager'`). Experiment `skolem-scope`: `makeSkolem(?m, ?x)` used in a manager rule and an address rule produced `(_:35fO… managerOf Tom)` and `(Tom hasAddress _:35fO…)`, i.e. Tom's manager *is* Tom's address [E].
- **Lexical, not value, identity.** Experiment `skolem-lit`: the four numerically equal literals `'1'^^xsd:int`, `'01'^^xsd:int`, `'1'^^xsd:integer` and `'1'^^xsd:decimal` give four distinct Skolem nodes. On the same data, `equal()` reports all six pairs equal [E]. The engine is therefore not consistent with itself on whether these are "the same argument".
- **Nesting is hidden.** `makeSkolem(?k2, 'manager', ?k)` with `?k` a Skolem node hashes `B<label of k>`. The result is again a flat blank node, so the engine cannot see the depth or cyclicity of `manager(manager(x))`. A termination analysis or a Q1 lead B blocking pass would need the symbolic term.
- **Collision risk.** MD5 is 128 bits. Accidental collisions are negligible, but deliberately crafted collisions are feasible. This matters only if keys come from untrusted data [U].
- **Precedent for D8.** Jena already does what D8 wants at the output boundary: an identifier that is a deterministic function of (function name, arguments). This makes it independent of evaluation order and of the run, and stable under incremental updates. Jena does it *at invention time* and loses the term. D8 keeps the term until output.

### 2.3 Negation-like and non-monotonic builtins [S, D, E]

| Builtin | Semantics | Monotonic flag |
|---|---|---|
| `noValue(s, p)` / `noValue(s, p, o)` | `!context.contains(s, p, o)`. A variable in subject or predicate position is treated as a wildcard. The context sees: **forward rules**: the raw graph plus the forward deductions *so far* [D: "in the model or the explicit forward deductions so far"]; **backward rules**: `findDataMatches`, i.e. raw data, schema and forward deductions, *not* backward-derived triples [S: `BBRuleContext.contains` → `graph.findDataMatches`] | `isMonotonic() = false` |
| `notEqual(x, y)` | Semantic inequality of the two bound terms (typed-literal comparison when comparable, otherwise `!sameValueAs`). This is a test, not negation over the data, so it is harmless | monotonic |
| `listNotContains`, `notLiteral`, `notBNode`, `notDType`, `unbound`, … | Tests on bound terms | monotonic |
| `remove(n, …)` (head, forward only) | Deletes the triple matched by body clause n. Deletions propagate through RETE and retract consequences of rules that used it [D] | non-monotonic |
| `drop(n, …)` | Same, silently: deletes from the raw and deduction graphs without firing rules | non-monotonic |
| `hide(p)` | Hides triples of predicate p from queries | — |

**How the forward engine handles non-monotonicity** [S]:
- **Which rules count as non-monotonic.** A rule is flagged non-monotonic only if a **head** builtin is non-monotonic (`Rule.allMonotonic(head)`; the source comment says "Future support for negation would affect this"). A rule set whose only non-monotonic builtin is `noValue` in bodies is therefore treated as **monotonic**.
- **Monotonic rule sets.** Every RETE activation fires immediately, and `noValue` is evaluated against whatever has been derived at that instant.
- **Non-monotonic rule sets** (some head uses `remove`/`drop`): activations go to a conflict set.
  - Pending triple insertions and deletions are processed first. Then one activation is fired, **LIFO** (last added first).
  - Just before firing, `shouldStillFire()` re-evaluates the body's non-monotonic builtins, e.g. `noValue`.
  - A `+`/`−` pair for the same rule and binding cancels out.
- **No retraction when a `noValue` becomes false later.** RETE retracts only when a *body triple* is deleted (via `remove`). A conclusion drawn while `noValue` was true is never withdrawn when the triple appears afterwards (experiment `e1-more`, incremental part).
- **No stratification analysis and no well-founded or stable-model semantics.** The documentation's only warning concerns `remove`/`drop`: "the behaviour of a rule set in which different rules both drop and create the same triple(s) is undefined" [D]. Forward firing order is explicitly unspecified: "There is no guarantee of the order in which matching rules will fire" [D].

**In backward and hybrid mode**, `noValue` in a backward rule looks only at the lower (forward) layer. This is sound stratified negation **by construction**, *provided* every negated predicate is fully derived by forward rules. If the negated predicate is derived by backward rules, `noValue` silently ignores those derivations (experiment `e1`, mode `bwd`).

### 2.4 Arithmetic, comparisons, other builtins [D, S, E]

- **Arithmetic.** `sum`, `difference`, `product`, `quotient`, `min`, `max` and `addOne` work one way only: the inputs must be bound, otherwise the call fails. The result is `double` if either input is `Float`/`Double`, **otherwise `long`, via `Number.longValue()`** (`Sum.java` etc.). `xsd:decimal` (a `BigDecimal`) is therefore **truncated** [S]. Experiment `e2` [E]:
  - `120.50 + 80.25` → `'200'^^xsd:int`;
  - `120.50 × 80.25` → `9600` (the exact value is 9670.125);
  - `120.50 / 3` → `40`.
- **Comparisons are correct on decimals.** `lessThan`, `greaterThan`, `le`, `ge`, `equal` and `notEqual` use typed-literal comparison: `greaterThan(120.50, 120.4)` is true [E]. Arithmetic and comparison are therefore inconsistent with each other.
- **Strings and other builtins**: `strConcat`, `uriConcat` (also a value-invention route: a URI built from data), `regex`, `now`, list builtins, `isDType`, `print`, `table`, `tableAll`.
- **No aggregation at all.** There is no count, sum, min, max or group-by over a set of bindings. The only "counting" builtin, `countLiteralValues`, counts distinct literal values of `(x, p, *)` and is not documented in the builtins table [S].

### 2.5 Termination safeguards

There are none in the rule engine itself [S, D]:
- No depth or term-size bound, no firing budget, no cycle detection, no acyclicity check on rule sets.
- The documentation: "It is perfectly possible, though not a good idea, to write rules that will loop infinitely".
- The only bound is the LP table *cache* size, an LRU-style cache that evicts and does not stop anything.
- Applications typically wrap queries in timeouts [U].

---

## 3. The running examples on Jena 6.2.0

Setup [E]:
- `jena-core` 6.2.0 with its dependencies, from Maven Central, on OpenJDK 21.0.10, 4 cores.
- One Java program (`Exp.java`, kept outside the repository) builds `GenericRuleReasoner`s from rule strings, loads data as raw triples, and prints sorted query results.
- Non-terminating runs are stopped after 8 s. A counting builtin `tick()` reports rule firings.

### 3.1 E3-ex: `employee(x), not isCompanyDirector(x) → ∃y managerOf(y, x)`

Data: `Tom`, `Anna` and `Dir` are employees, and `Dir` is the company director.

```
[r1: (?x rdf:type ex:Employee), noValue(?x, rdf:type, ex:CompanyDirector),
     makeSkolem(?m, 'manager', ?x) -> (?m ex:managerOf ?x)]
```

| Mode | Result |
|---|---|
| forwardRETE, backward, hybrid | `(_:UAxM… managerOf Tom)`, `(_:A/Kb… managerOf Anna)`; nothing for `Dir`. The **same labels in every mode and every run** [E] |

Here `noValue` reads a data predicate, so it is stratified and order-independent, and Jena matches the F2 result (Skolem terms `manager(Tom)` and `manager(Anna)`).

**With `r2: (?m ex:managerOf ?x) -> (?m rdf:type ex:Employee)` (the Q1 program):**

| Mode / query | Result |
|---|---|
| forwardRETE, `prepare()` | **Does not terminate**: after 8 s, about 1.1 M firings of r1 and 0.75 GB of heap, growing [E]. Each new Skolem node is a non-director employee, as predicted in the README (Q1) |
| backward, `tableAll()`, goal `(?m managerOf ex:Tom)` | **Terminates in 0.06 s** with the single answer `(_:UAxM… managerOf Tom)` and 1 firing of r1 [E]. The subgoal `(Tom rdf:type Employee)` is ground, and its generator is a singleton that stops after the data match. The r2 → r1 → … chain is never explored |
| backward, `tableAll()`, goal `(?e rdf:type ex:Employee)` | **Does not terminate**: about 1.1 M firings in 8 s [E] |
| backward **without** tabling, goal `(?m managerOf ex:Tom)` | **Does not terminate**: about 10 M firings in 8 s, because SLD backtracking explores the r2/r1 recursion when enumerating all answers [E] |

Reading:
- Jena behaves like the Skolem chase forward. It has no restricted check (apart from the hand-written `noValue`) and no blocking.
- Goal-directed, tabled evaluation answers bound queries on the infinite model. This is a cheap, effective partial answer to Q1, and an instance of T12-13 (COMPLETE-DYNAMIC by goal-directed evaluation).
- Jena itself gives no completeness status and no diagnostic (Q1 lead C).

**Lookup-before-invent ([report 12](12-invention-under-negation.md) V1).** The rules are r0 `(?x hasBoss ?b) -> (?b managerOf ?x)` forward, plus R `(?m managerOf ?x) <- (?x type Employee), noValue(?x, hasBoss), makeSkolem(?m,'manager',?x)` backward, in hybrid mode. The result is `alice managerOf bob`, `m(alice) managerOf alice` and `m(carol) managerOf carol`, exactly T12-01 [E].

### 3.2 E1-ex: default via `noValue`

Data: contracts `c1` and `c2`, and `c2 category Premium`. Rules:
```
[s: (?c ex:category ex:Premium) -> (?c ex:hasSpecificCondition ex:PremiumCond)]
[d: (?c rdf:type ex:Contract), noValue(?c, ex:hasSpecificCondition) -> (?c ex:applies ex:GeneralConditions)]
```
Expected (perfect model): only `c1` gets the general conditions.

| Configuration | `applies GeneralConditions` | Correct? |
|---|---|---|
| forwardRETE / forward / hybrid with both rules forward; both rule orders; both fact orders | `c1`, **`c2`** | **No** (all 4 permutations in RETE, all tried in the legacy engine) [E] |
| backward (both rules backward) | `c1`, **`c2`** | **No**: `noValue` does not see the backward-derived `hasSpecificCondition` [E] |
| forwardRETE + a never-firing rule with `drop` in its head (switches RETE to deferred, conflict-set mode) | `c1` | yes, here [E] |
| **hybrid: `s` forward, `d` backward** | `c1` | **yes** [E] |
| `hasSpecificCondition` asserted as data (no `s`), forward | none for c2 | yes |
| incremental, forwardRETE: materialise with c1 only, then add `c1 category Premium` | `c1` keeps `applies GeneralConditions` *and* gets `hasSpecificCondition` | **No** (no retraction) [E] |
| incremental, hybrid (`s` forward, `d` backward): same update | `applies` disappears for c1 | yes (tables reset) [E] |

Why: the rule set has no non-monotonic *head*, so RETE treats it as monotonic. `d` fires as soon as `(c2 type Contract)` is injected, before `s` has derived `hasSpecificCondition` (§2.3).

**A stratifiable chain.** The rules are `s1: T(x), noValue(x,a) → b(x)` and `s2: T(x), noValue(x,c) → a(x)`. The perfect model is `{a}` (s2 is in a lower stratum than s1).

| Engine | Order s1, s2 | Order s2, s1 |
|---|---|---|
| forwardRETE (immediate mode) | `{a, b}` **wrong** | `{a}` |
| legacy forward | `{a, b}` **wrong** | `{a}` |
| forwardRETE deferred mode (LIFO + re-check) | `{a}` | `{a, b}` **wrong** |

The result is order-dependent in every forward configuration [E]. Deferral only changes *which* order is wrong.

**A cross-defeating pair** (`r1: noValue(x,p) → q(x)`, `r2: noValue(x,q) → p(x)`; two stable models) gives `{q}` or `{p}` depending on rule order, with no warning [E]. This is the V2x "genuine choice" of [report 12](12-invention-under-negation.md), resolved silently by firing order.

### 3.3 E2-ex: "basket > 200 € ⇒ free delivery"

Data: basket `b1` with items priced `120.50`, `80.25` and `0.10` (`xsd:decimal`); the exact total is 200.85.

- **No aggregate exists**, so the sum over a basket cannot be written declaratively. A pairwise rule (`sum(?x, ?y, ?s)` over pairs of distinct items) yields `120`, `200` and `80`: the decimals are truncated *before* adding [E].
- A procedural running total in forward mode:
  ```
  init: item(b,i), noValue(b,total) -> total(b,0)
  step: item(b,i), price(i,p), noValue(i,counted), total(b,t), sum(t,p,t2) -> remove(3), total(b,t2), counted(i,'true')
  free: total(b,t), greaterThan(t,200) -> delivery(b,Free)
  ```
  This computes `total = 200` and **no free delivery**. The correct decision is "free" (200.85 > 200) [E].
  - The workaround is order-sensitive by construction (`remove` plus `noValue`).
  - It is only correct with integer prices.
  - It contradicts D7, which requires exact decimals and modeller-chosen rounding.

---

## 4. Strengths worth borrowing

| # | Idea | Jena evidence | Relevance | Adaptation for our core |
|---|---|---|---|---|
| J1 | **Hybrid layering: materialise a lower layer forward, answer an upper layer by tabled backward chaining** | `FBRuleInfGraph`: forward deductions feed `findDataMatches` used by LP rules and by `noValue` [S]. E1 is correct only in this configuration [E] | E10(d) combinations; D5 (negated literals evaluated against lower strata materialised by the chase) is exactly this data flow | The analyser, not the rule author, decides the cut: per stratum or SCC, chosen from the GRD and the query workload. The cut must be *closed downward*: everything a lower-layer rule or a negation reads is derived in a lower layer. Jena leaves this to the author, and completeness silently fails otherwise |
| J2 | **Variant tabling (SLG) plus ground-goal cut-off** | `LPBRuleEngine` table of `Generator`s; singleton generators for ground goals [S]; bound E3 query terminates on the infinite model [E] | E10(b) dynamic backward chaining; Q1 (finite answers for bound queries on infinite models); T12-13 COMPLETE-DYNAMIC | Tabling over *symbolic* Skolem terms, so that the analyser can also detect cyclic term patterns. Consider subsumption tabling, listed as a future improvement in Jena's own docs |
| J3 | **Forward rules that generate specialised backward rules** (`(?p subPropertyOf ?q) -> [(?a ?q ?b) <- (?a ?p ?b)]`) | documentation example; OWL `some1`/`some1b` [D, S] | Rule specialisation by the schema: a form of partial evaluation. Relevant to E8 (optimisation pass with traceability) and E10(c) (pre-computed rewritings invalidated on change: Jena retracts generated rules incrementally) | Treat as an optimisation that the analyser applies to rules with "schema" body atoms, with an E9 proof sheet (instantiation with ground schema bindings is an equivalence on the given schema) |
| J4 | **Deterministic identifiers from (function, arguments)** | `makeSkolem` = MD5 of the argument serialisation [S]; same label in every mode and run [E] | D8 late materialisation: output identifiers that are order- and run-independent and stable under incremental updates | Include the **function name** and **canonical value forms** in the key, and use a longer hash if identifiers leave the system. Keep the symbolic term internally (Jena's flat blank node hides nesting, which termination analysis and Q1 blocking need) |
| J5 | **Memoised invention per (individual, property, class)**: `makeInstance` with `noValue` guard | OWL `someValuesFrom` rules [S]; V1 reproduced [E] | D2 lookup-before-invent has a 20-year-old industrial precedent | Already designed in report 11; Jena confirms the pattern is usable by modellers |
| J6 | **Derivation records** (rule + matched triples) | `RuleDerivation`, `getDerivation` [D, S] | E8 explanations cite original rules | Report 06 idea 5 (cheap annotations, proof trees on demand) covers it more efficiently |
| J7 | **Validation rules producing structured violation reports** | `rb:violation error(...)` [D] | Q1 lead C (diagnostics as first-class output); integrity constraints of D2 | Diagnostics as a separate output relation with structured payloads |
| J8 | **Special-cased transitive closure with direct (reduced) relation** | `TransitiveReasoner` / TGC [D, S] | Report 06 idea 10 (modular materialisation) | Analyser recognises transitive predicates |

## 5. Weaknesses

- **Semantics guarantees.**
  - `noValue` has an operational, order-dependent meaning, with no stratification check, no well-founded or stable semantics, and no warning. It gives wrong answers on E1-ex and on a stratifiable 2-rule program, and it resolves cross-defeating rules arbitrarily [E].
  - Correct use requires hand-placing rules in the right forward/backward layer.
  - `remove`/`drop` are explicitly "undefined" when rules conflict [D].
- **No termination analysis or safeguard.** Q1 runs forever in forward mode [E].
- **Skolem functions are untyped and unnamed** (§2.2). Identity is lexical, while `equal()` is value-based [E].
- **No aggregation. Decimal arithmetic silently truncates to integers** [S, E]. This is disqualifying for E2-ex and D7.
- **RDF-only data model**: binary predicates. N-ary business relations need reification or functors, which complicates rule-level reasoning such as GRD and piece-unifiers.
- **No query rewriting**, no decidability classes, no chase variants, no completeness status (E1, E2, E7).
- **Performance** [U]:
  - in-memory and single-threaded;
  - RETE memory grows with non-ground patterns (the documentation's "Futures" asks for tuning with "highly non-ground patterns");
  - the full OWL rule set is known not to scale to large ABoxes.
  - No benchmark was run this session. The only data points are the Q1 runs: about 140 k firings/s of r1 forward, heap-bound.
- **Maintenance status** [S]:
  - The rule engine is in *maintenance mode*. In 2023–2026, commits touching `rulesys` were 8, 10, 9 and 3 per year, almost all API refactorings (node/triple API changes, licence headers, deprecations).
  - The only functional change since 2024 is a bug fix (GH-1361, 2026-06: respect `filterFunctors` in backward chaining).
  - The "Futures" list (custom equality reasoner, subsumption tabling, RETE tuning, database integration) is unchanged.
  - Jena as a whole is very active (Apache, releases every 2–4 months; 6.2.0 in July 2026), and its licence is Apache-2.0.
- **As a test oracle**, Jena is usable on positive Datalog with binary predicates (v0, D6), with `makeSkolem` tagged by hand. It also covers stratified negation only when the negated predicates are placed in the forward layer and the negations in the backward layer. It is weaker than clingo/DLV for negation and useless for aggregation.

---

## 6. Comparison rows (to read with [report 06](06-sota-engines.md) §2)

Same columns as the report 06 matrix:

| System | Lang. | Licence | Status 2024-26 | ∃-rules | Chase variant / termination | Strat. neg. | Aggreg. | Equality | Incremental | Query rewriting | Explanations | Key idea to borrow |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Jena rules: forward RETE | Java | Apache-2.0 | Jena very active (6.2.0, 2026-07); rule engine in maintenance mode | ~ (manual: `makeSkolem` hash blank node, `makeTemp`) | Skolem-like, user-controlled; **no termination check or guard** | ✗ (`noValue` operational, order-dependent; wrong on E1-ex [E]) | ✗ (arithmetic truncates decimals) | ~ by rules only (`owl:sameAs` rules); no union-find | ✓ RETE add/remove (no retraction when a `noValue` turns false) | ✗ | ✓ derivation records | incremental RETE, validation rules |
| Jena rules: backward LP | Java | Apache-2.0 | as above | ~ (`makeSkolem`, `makeInstance` memoised) | goal-directed; terminates on bound goals via ground-goal cut-off [E]; open goals may loop | ~ (`noValue` sees only data + forward layer) | ✗ | ~ rules | ✗ (tables reset on update) | ✗ (dynamic backward chaining instead) | ✗ [U] | SLG variant tabling, singleton ground goals |
| Jena rules: hybrid (default) | Java | Apache-2.0 | as above | ~ (OWL `someValuesFrom` = `noValue` + `makeInstance`: lookup-before-invent) | forward layer as above; backward layer goal-directed | ✓ **by layering only** (negation in backward rules over forward-materialised predicates); not checked | ✗ | ~ rules; TGC for subClass/subProperty | forward incremental, backward recomputed | ✗ (forward rules *generate* specialised backward rules) | ✓ forward part | **hybrid forward/backward cut (E10, D5)**, rules that compile rules |

Additions to the prioritised list of report 06 §5 (to be merged there if the owner agrees):
- **J1 → extends idea 1 (analyser-driven).** The analyser chooses the forward/backward cut per SCC, with the invariant "a negated or forward-read predicate lies entirely below the cut".
- **J2 → new idea.** Tabled backward evaluation with ground-goal cut-off as the E10(b) engine, which also gives finite answers to bound queries on infinite Skolem models (Q1).
- **J4 → refines D8.** Output identifiers = hash(function name, canonical arguments). Jena shows both the benefit (stability) and the pitfalls (no name, lexical keys).

---

## 7. Open points raised by this study (not decisions)

1. **Q1 interaction.** Ground-goal cut-off plus tabling gives termination only for bound queries. Should F2's backward strategy give these answers the status COMPLETE-DYNAMIC (as T12-13), and the open-goal enumeration NOT-GUARANTEED under a budget?
2. **D8 hash key.** Should the output identifier of a Skolem term be a hash (stable, compact, opaque, collision risk) or a readable serialisation such as `manager(Tom)` (explainable, unbounded length)? Jena chose the hash and lost explainability.
3. **Value canonicalisation of Skolem arguments.** Under D7's exact decimals, `manager(1.0)` and `manager(1.00)` must be the same term. Jena gets this wrong (§2.2).

---

## Appendix: experiment programs and outputs [E]

- Environment: `jena-core` 6.2.0 plus `jena-base`, `jena-iri3986`, `jena-langtag`, `slf4j-nop` and transitive dependencies, all via `mvn dependency:copy-dependencies`; OpenJDK 21.0.10.
- The files are kept in the session scratchpad, not in the repository.
- Each experiment builds `new GenericRuleReasoner(Rule.parseRules(rules))`, sets the mode, binds a raw graph, and lists triples with `InfGraph.find`.

Key outputs (labels abbreviated):

```
##### e3-fwd   (r1 only; same output for modes fwd, bwd, hyb)
   (_:A/Kb2vnOawnr1W+oFwLLcg== ex:managerOf ex:Anna)
   (_:UAxMYh23kvPg5OahC9TsCQ== ex:managerOf ex:Tom)
##### skolem-scope
   (_:35fOfeefYPOvUb5wrcU68Q== ex:managerOf ex:Tom)        <- makeSkolem(?m, ?x) in rule ra
   (ex:Tom ex:hasAddress _:35fOfeefYPOvUb5wrcU68Q==)       <- makeSkolem(?a, ?x) in rule rb: same node
   (ex:Tom ex:managerTagged _:UAxMYh23kvPg5OahC9TsCQ==)    <- makeSkolem(?m, 'manager', ?x): same as e3-fwd
   (ex:Tom ex:addressTagged _:AI+muuZ6gbuzzo88jXpHXQ==)
##### skolem-lit   ('1'^^int, '01'^^int, '1'^^integer, '1'^^decimal, '1')
   five distinct Skolem nodes; equal() true on all 6 pairs of the four numerals
##### invent-bwd
   makeTemp: different node on each query; makeSkolem: same node; makeInstance: same node (also after reset())
   forward makeInstance: BuiltinException "only usable in backward/hybrid rule sets"
##### lookup (report 12 V1, hybrid)
   (_:EL+F… managerOf alice) (_:KtmO… managerOf carol) (alice managerOf bob)
##### e3rec-fwd
   forward RETE prepare(): NOT TERMINATED after 8.0s; tick count=1131776; heap used=751 MB
##### e3rec-bwd  (tableAll)
   goal (?m managerOf ex:Tom): (_:UAxM… managerOf Tom); finished in 0.06s; tick count=1
   goal (?e rdf:type ex:Employee): NOT TERMINATED after 8.0s; tick count=1133146
##### e3rec-bwd-notable
   goal (?m managerOf ex:Tom): NOT TERMINATED after 8.0s; tick count=10348013
##### e1   (both rules forward, any order; or both backward)
   (ex:c1 applies GeneralConditions) (ex:c2 applies GeneralConditions)      <- c2 wrong
   hybrid, s forward, d backward: (ex:c1 applies GeneralConditions)         <- correct
##### e1-more
   forward incremental, after adding (c1 category Premium): c1 keeps applies GeneralConditions
   hybrid incremental, after the same update: (none)
##### strata   (perfect model {a})
   fwd s1,s2: {a, b}   fwd s2,s1: {a}   fwdold s1,s2: {a, b}   fwdold s2,s1: {a}
##### deferred (never-firing drop rule added)
   E1: c1 only (correct, both orders, also with a 3-step specific chain)
   strata s1,s2: {a}   strata s2,s1: {a, b}
##### choice
   r1,r2: {q}   r2,r1: {p}
##### e2
   pairwise sum over 120.50 / 80.25 / 0.10: 120, 200, 80 (xsd:int)
   quotient(120.50,3)=40  product(120.50,80.25)=9600  difference=40  greaterThan(120.50,120.4)=true
   running total with remove(): total 200 (exact 200.85); no free delivery derived
```

## Sources

- Apache Jena documentation, "Reasoners and rule engines: Jena inference support": sections "The general purpose rule engine", "Builtin primitives", "Hybrid rule engine", "Tabling", "Futures". Read from `https://raw.githubusercontent.com/apache/jena-site/main/source/documentation/inference/__index.md`; jena.apache.org itself was blocked by the proxy [D].
- Apache Jena source, `https://github.com/apache/jena`, commit `00b22ca` (2026-10-01) [S]:
  - `jena-core/src/main/java/org/apache/jena/reasoner/rulesys/`: `GenericRuleReasoner`, `FBRuleInfGraph`, `Rule`, `builtins/{MakeSkolem, MakeTemp, MakeInstance, NoValue, NotEqual, Remove, Drop, Sum}`, `impl/{RETEEngine, RETEConflictSet, RETERuleContext, BBRuleContext, LPBRuleEngine, Generator, TempNodeCache}`;
  - `jena-core/src/main/resources/etc/{owl-fb, rdfs-fb-tgc}.rules`;
  - git tags `jena-5.6.0`, `jena-6.0.0` and `jena-6.2.0`, and the commit log of `rulesys` since 2022.
- Maven Central `org.apache.jena:jena-core` metadata (latest 6.2.0) [S].
- Forgy, C. L., "Rete: a fast algorithm for the many pattern/many object pattern match problem", *Artificial Intelligence* 19, 1982 (cited by the Jena docs) [U].
