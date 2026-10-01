# Adversarial review of `docs/domain/main.md` (2026-10-01)

| Field | Value |
|---|---|
| **Date** | 2026-10-01 |
| **Target** | [`docs/domain/main.md`](../../domain/main.md), with `conventions.md`, `notation.md`, `glossary.md`, `examples.md`, `disputes.md` and the stubs it links to |
| **Reviewer** | AI agent (adversarial) |
| **Status** | Blocker (#1) and all majors (#2-#10) addressed in the commit that added this file (`git log --diff-filter=A -- docs/project/reviews/2026-10-01-domain-main-adversarial-review.md`). Minors and nits: see the table below. |

This file is a project record of a review of the domain reference; it is saved here, not in `docs/domain/`, because the domain reference never links to project documents. Process: [domain stage-2 plan](../domain-stage2-plan.md#writing-and-review-process).

## Resolution of the findings

| # | Severity | Status | What was done |
|---|---|---|---|
| 1 | blocker | addressed | "Status and authority" paragraph (stub summaries are orientation; authority = `notation.md`, written pages, else the primary literature cited; disagreements go to `disputes.md`); *written*/*stub* marker on every index entry; note on the reading paths; `conventions.md` §2.1 aligned. Deviation: a disagreement between the overview and a *written* page presumes the written page right; a stub never wins. |
| 2 | major | addressed | Undecidability attributed to expressive power (Beeri-Vardi 1981; Chandra-Lewis-Makowsky 1981), single-rule claim tagged `[U]`, semi-decidability, decidable classes with infinite chases, termination depends on the chase variant. |
| 3 | major | addressed | Rewritten: Herbrand interpretations (UNA, domain closure), default negation, least/perfect/well-founded (three-valued)/stable (0, 1 or many; brave/cautious); Reiter's CWA distinguished; partial closure; agreement restricted to Datalog/UCQs and to answers over constants. |
| 4 | major | addressed | Semantics (existential variable) vs procedure (chase nulls, rewriting creates none); rule-local Skolem functions `f^ρ_z` vs shared function symbols; other mechanisms listed (aligned with the value-invention-strategies stub); consequences hedged. `notation.md` §4, `examples.md` (b) and glossary aligned. |
| 5 | major | addressed | Datalog± no longer a synonym; conceptual-graph rules, nonmonotonic and disjunctive existential rules, lattice/semiring Datalog added; semantic-web languages named; SLDNF and Clark completion; production rules and inconsistency-tolerant semantics placed out of scope. Existential-rules stub summary aligned. |
| 6 | major | addressed | Datalog bullet: bottom-up termination on finite data, SLD looping without tabling, arithmetic and recursive aggregates break termination, "Datalog" in system names. |
| 7 | major | addressed (one item deferred) | Rounding moved out of the Datalog bullet into a "Datatypes and built-ins" paragraph and index line; "lookup/constructor patterns" and "lookup-before-invent" removed (glossary entry deleted, value-invention stub reworded to generic conditional invention); "rewriting known queries in advance" removed from index and query-rewriting summary; four-status taxonomy removed from the soundness stub; "named function" removed from `notation.md`; empty `docs/domain/project/` deleted. **Deferred:** renaming `exact-decimals-and-rounding.md` to a broader datatypes-and-built-ins page (a stage-2 decision; it would break incoming links). |
| 8 | major | addressed | Ground-and-solve (ASP, lazy grounding) and DL model construction added; magic sets moved to goal-directed bottom-up evaluation; "join and matching algorithms" mentions RETE. Resolution-based FO proving not added (out of the rule-based core). |
| 9 | major | addressed | Data complexity = rules and query fixed; class-to-condition mapping; linear rules, frontier-guarded, aGRD, shy, GBTS; landmark complexity table, all `[U]`; finite vs unrestricted entailment and finite controllability. |
| 10 | major | addressed (with a nuance) | Soundness defined (UCQ answers over constants from any chase prefix), consistency cannot be certified by an early stop, non-monotone aggregates. Disagreement: monotone lattice aggregates do not "stay sound" as facts; intermediate values are sound only as bounds in the lattice order. Soundness stub aligned. |
| 11 | minor | addressed | "fragments of first-order logic and nonmonotonic extensions of them". |
| 12 | minor | addressed | Formal CQ over example predicates; universal quantification; `D` vs `I`. |
| 13 | minor | addressed | DL bullet made precise (DL-Lite_R/EL, EGDs for number restrictions, non-tree bodies and arity). |
| 14 | minor | addressed | Brave/cautious, answer-set enumeration, containment under constraints, rewritability, constraint meaning per semantics. |
| 15 | minor | partly addressed | As-of note, alphabetical order, RDFox remark, missing systems listed as "not yet covered". **Open:** per-system maintenance status (e.g. superseded or archived systems) belongs on the system pages (stage 2, batch D). |
| 16 | minor | addressed | "Why" column, named out-of-scope neighbours, Prolog vs tabling clarified, Scallop note, `conventions.md` §3 aligned. |
| 17 | minor | addressed | Query-rewriting summary (per-query vs FUS, `[U]`); glossary "Classical negation" and "Skolem chase" (frontier vs all body variables, `[U]`). |
| 18 | minor | addressed | LP/ASP, Equality and DL/RDF paths added; optimisation path ends with incremental maintenance; index order stratified negation before perfect model. |
| 19 | minor | partly addressed | "Words that mean different things" table added. **Open:** marking community-specific readings in the glossary entries themselves. |
| 20 | minor | partly addressed | References grouped by family; Calì-Gottlob-Lukasiewicz, Fagin et al., Gelfond-Lifschitz, Ceri-Gottlob-Tanca, Gebser et al., Chein-Mugnier added. **Open:** Arenas-Barceló-Libkin-Martens-Pieris textbook (bibliographic details not verified); the list exceeds ten entries. |
| 21 | minor | open | Inline worked example not added (pedagogy choice for the end-of-stage-2 revision of `main.md`); the words table and glossary partly mitigate define-before-use. |
| 22 | nit | addressed | "Building blocks" / "rule-set level analyses and transformations". |
| 23 | nit | addressed | ChaseFUN, Llunatic, PDQ named. |
| 24 | nit | addressed | Glossary "Core": no homomorphism into a proper subset (no proper retraction). |
| 25 | nit | addressed | Index line for notation: "mapping partly unverified". |

---

## Original review

Reviewed: `docs/domain/main.md` (138 lines), with `conventions.md`, `notation.md`, `glossary.md`, `examples.md`, `disputes.md`, `concepts/foundations.md`, and every stub (TODO lists and summaries). Read-only: nothing in the repo was modified.

What I checked mechanically:
- Every relative link and anchor in `main.md` resolves. That includes `foundations.md#open-world-closed-world`, `conventions.md#8-…`, and `#scope`/`#page-index`.
- The page index is complete: 20 concepts, 14 algorithms, 10 systems, 1 evaluation page and 3 adjacent pages, matching the files on disk.
- Status of the linked pages: 46 of the 48 content pages are stubs with a `## TODO (coverage)` section. Only `concepts/foundations.md` is written. `concepts/exact-decimals-and-rounding.md` is a stub that already has a long written section on rounding.
- Bibliographic details of the seven entry references (venues, volumes, years) are correct as far as I know. I did not run a web search. The claims I marked "from memory" below should be checked before anyone removes a `[U]`.

Severity scale:
- **blocker**: an agent relying on the page will be misled about the whole reference.
- **major**: a wrong or misleading technical or structural claim.
- **minor**: imprecise or incomplete.
- **nit**: style.

---

## Findings

### 1. [blocker] Page index and reading paths: the "source of truth" claim hides that almost every linked page is a stub
- **Location:** intro (l. 3), Reading paths (l. 57-69), Page index (l. 71-128).
- **Problem:**
  - The intro calls the reference "the shared source of truth" and only adds "it is not final".
  - 46 of the 48 linked pages contain only a summary and a TODO checklist, and nothing on `main.md` says so.
  - The reading paths ("First contact", "Existential rules and the chase", …) therefore send an agent through chains of empty pages.
- **Why it matters:** an agent that follows a link and finds a three-sentence summary plus a checklist will either treat the summary as the authoritative definition, or fill the gap from its own priors. Both defeat the purpose of a shared source of truth. The stub summaries also contain claims that are not hedged and not tagged (see 8, 17), and these would be read as authoritative.
- **Fix:**
  - Add a status marker to every index entry, e.g. `— *stub*` or a `Status` column (written / stub / partial).
  - Add a paragraph after the intro:
    > **Status and authority.** Most pages are currently stubs (summary + coverage checklist). A stub's summary is orientation, not a definition. Until a page is written, the authority on its topic is the primary literature it cites. `notation.md` is normative for symbols. A page's *Formal definitions* section is normative for its notion. The glossary is a reminder and defers to both. Claims tagged `[U]` are unverified and `[D]` disputed ([conventions §7](../../domain/conventions.md#7-claim-tags)). If this page and a linked page disagree, the linked page wins, and the disagreement is a bug to log in [disputes](../../domain/disputes.md).

### 2. [major] Decidability and complexity (l. 30): the cause of undecidability is wrong
- **Location:** "With value invention, query answering is undecidable in general: the chase may run forever."
- **Problem:**
  - Undecidability does not come from chase non-termination. Many decidable classes have infinite chases: guarded, linear, sticky, warded. Conversely, a terminating chase only gives decidability if termination is guaranteed for every input.
  - The actual result is that CQ entailment under TGDs is undecidable because the language can encode Turing machines (Beeri & Vardi 1981; Chandra, Lewis & Makowsky 1981). It stays undecidable for a fixed rule set, and even for a single rule (Baget et al. 2011; from memory, to verify). The problem is recursively enumerable (semi-decidable).
  - "The chase" is also used without naming a variant, although termination depends on the variant: oblivious ⊂ semi-oblivious ⊂ restricted ⊂ core.
- **Why it matters:** an agent will infer "infinite chase ⇒ undecidable" or "need termination for decidability". With that belief it will discard the BTS/FUS classes and query rewriting, which are exactly the non-terminating-but-decidable cases this domain is known for.
- **Fix:** replace with:
  > With existential variables (or function symbols), CQ entailment is undecidable in general, even for a fixed rule set (Beeri & Vardi 1981; Chandra, Lewis & Makowsky 1981), though it remains semi-decidable: if the answer is yes, a finite prefix of the chase proves it. Decidability does not require a finite chase. A class is decidable if it has a finite universal model (FES, so some chase variant terminates), a finite UCQ rewriting for every query (FUS), or a universal model of bounded treewidth (BTS) (Baget et al. 2011). Whether the chase terminates depends on the variant (oblivious, semi-oblivious/Skolem, restricted, core): see [chase variants](../../domain/algorithms/chase-variants.md).

### 3. [major] Open world and closed world (l. 11): CWA, negation as failure, designated model and UNA are conflated, and the "agree" claim is too broad
- **Problems:**
  1. "Logic programming adopts this view through default negation and a designated model (least, perfect, stable or well-founded)" is not precise enough:
     - stable-model semantics has zero, one or many models, and reasoning is brave or cautious, so there is no single designated model;
     - well-founded semantics is three-valued, so "not derivable ⇒ false" does not hold (some atoms are *undefined*);
     - Reiter's CWA (1978), negation as failure, and Clark completion are distinct notions. Reiter's CWA is inconsistent with disjunctive knowledge.
  2. "The two readings agree on positive rules and conjunctive queries" is false as stated when the positive rules contain function symbols or existential variables. In `examples.md` §ex-manager, reading (b) returns `manager(tom)` as an answer under the Herbrand reading, but `manager(tom)` is not a certain answer. The claim holds only for (i) answers restricted to tuples of constants and (ii) monotone queries (UCQs). The paragraph's last sentence, "they diverge with … value invention", partly contradicts the same claim.
  3. The unique-name assumption and domain closure are not mentioned. The divergence on equality comes from UNA, not from CWA. LP builds in UNA and domain closure through Herbrand interpretations, and FO does not.
  4. The closed world is presented as a global property, but a reading can close some predicates only. Examples: negation on a lower stratum only, DBoxes or closed predicates in DLs. `examples.md` itself speaks of "the closed-world reading of `specificConditionApplies`".
- **Why it matters:** this paragraph is the main bridge between the database/KR world and the LP/ASP world. An agent will repeat "LP has a designated model" and "the readings agree on positive rules", and will mis-implement Skolem-term answers or ASP reasoning.
- **Fix:**
  > **Open world and closed world.** Under first-order semantics (databases with incomplete information, DL, existential rules) a fact that is not entailed is *unknown*; query answers are the **certain answers**: tuples of constants true in every model. Logic programming instead reads a program under **Herbrand interpretations**, which build in the unique-name assumption and domain closure, and under **default negation** (`not`, negation as failure): `not p` holds when `p` is not derived. Its semantics select particular models: the least model (positive programs), the perfect model (stratified), the well-founded model (three-valued: true, false, undefined) or the stable models (zero, one or many; reasoning is *cautious*, i.e. true in all, or *brave*, i.e. true in some). This is related to, but not the same as, Reiter's closed-world assumption. The readings agree for function-free positive rules (Datalog) and UCQs; with function symbols they still agree on answers made of constants. They diverge with default negation, aggregation, equality (UNA) and answers containing invented values.

### 4. [major] Value invention (l. 20): "two ways", Skolem function vs named function symbol, and semantics vs procedure
- **Problems:**
  1. "There are two ways to write it" is contradicted by [value-invention strategies](../../domain/concepts/value-invention-strategies.md), which lists five mechanisms: existential variables, rule-local Skolem terms, shared function symbols, identifier-minting built-ins (Jena `makeSkolem`, RDFox `SKOLEM`, SPARQL `BNODE()`), and constructor predicates. Arithmetic (`y = x + 1`) also invents values.
  2. "A **Skolem function** creates a term such as `manager(tom)`" mixes up two things:
     - Skolemisation, which introduces a *fresh, rule-local* symbol per existential variable (`notation.md` §7: `f^ρ_z`);
     - a *user-named function symbol shared across rules, data and queries* (`manager`).

     These are not semantically equivalent. Shared symbols identify individuals across rules, and they are strictly more expressive than existential rules, as the Skolem stub itself says. Calling `manager(X)` a "Skolem function" is exactly the slippage that `notation.md` §4 ("Skolemised or 'named function' rules") and `examples.md` (b) propagate.
  3. "An existential variable creates an anonymous labelled null" mixes semantics and procedure. Semantically, an existential variable only asserts existence. The *chase* creates nulls, while query rewriting creates none. `conventions.md` §5 insists on keeping these two apart.
  4. "The choice changes identity, termination, and how negation and counting behave" needs hedging. For positive rules and CQ answers over constants, rule-local Skolemisation preserves certain answers. The choice matters for termination (the Skolem chase is the semi-oblivious one, and the restricted chase can stop where it does not), for answers containing invented values, and for non-monotone constructs.
- **Why it matters:** this paragraph decides which notion of "Skolem" every agent will use. The domain literature reserves "Skolem function" for the outcome of Skolemisation.
- **Fix:**
  > **Value invention.** "Every employee has a manager" asserts the existence of an individual without naming it. In first-order logic this is an **existential variable**: `employee(x) → ∃y managerOf(y, x)`. Forward-chaining procedures (the chase) represent the unnamed individual by a fresh **labelled null**. **Skolemisation** replaces `∃y` by a term `f^ρ_y(x)` over a fresh, rule-local function symbol. This preserves entailment of sentences of the original signature, so certain answers over constants are unchanged. Many languages also let users write **shared, named function symbols** (`manager(x)`) used in several rules, in data and in queries. That is *not* Skolemisation and is strictly more expressive. Other mechanisms include identifier-minting built-ins, constructor predicates and arithmetic. The choices differ on termination (which chase variant), on the identity of invented individuals, on whether they may be returned as answers, and on how negation, counting and equality treat them.

### 5. [major] Rule-language families (l. 13-18): missing and conflated families
- **Problems:**
  - "Existential rules (tuple-generating dependencies, Datalog±)" presents Datalog± as a synonym. Datalog± is the Calì–Gottlob–Lukasiewicz/Pieris *family of decidable fragments* (with EGDs and negative constraints). The glossary says this correctly.
  - The following are missing:
    - **Conceptual-graph rules**: the historical root of the "existential rules" name and of the Montpellier/Graal line (Chein & Mugnier 2009; Salvat & Mugnier 1996). This is ironic given the expert validator.
    - **Nonmonotonic existential rules**: stable or well-founded semantics with existential variables (Magka, Krötzsch & Horrocks 2013; Gottlob, Hernich, Kupke & Lukasiewicz 2012/2014). This is exactly the intersection of two core circles.
    - **Disjunctive existential rules / disjunctive Datalog.**
    - **Production rules** (OPS5, CLIPS, Drools, RETE). This is the largest industrial "rule-based reasoning" family. It has operational semantics, and it should at least appear in the scope table as out of scope, with a reason.
    - **Datalog over lattices/semirings** (Datalog°, Flix, ascent), whose systems are listed under "others".
  - The "Semantic-web rule languages" bullet names no language. It should mention SWRL, RIF, N3, OWL 2 RL as rules, and SHACL rules.
  - The LP bullet omits Prolog's operational SLDNF semantics and Clark completion.
- **Fix:** rewrite the existential-rules bullet as:
  > **Existential rules** (also called tuple-generating dependencies, TGDs, in databases, ∀∃-rules, and conceptual-graph rules in KR; Datalog± names a family of decidable fragments of them, with EGDs and negative constraints): …

  Add bullets for nonmonotonic and disjunctive existential rules. In the scope table, list production rules and inconsistency-tolerant semantics, each placed explicitly as adjacent or out of scope.

### 6. [major] Datalog bullet (l. 14): "always terminating" and "extensions add … arithmetic"
- **Problems:**
  - "Always terminating" holds for bottom-up evaluation on finite data. SLD resolution (Prolog) loops on left-recursive Datalog, and the backward-chaining page covers exactly that case.
  - Arithmetic built-ins that compute new values (`y = x + 1`) break termination and decidability. A recursive `#sum` can do the same. Listing arithmetic as an extension right after "always terminating" invites the wrong inference.
  - "Datalog" is used throughout without qualification, for pure positive Datalog, for Datalog with negation, and for industrial "Datalog engines" (Soufflé has ADTs and arithmetic). The ambiguity is never flagged.
  - Attribution: Datalog came from deductive databases, at the intersection of LP and databases, not "databases" alone.
- **Fix:**
  > **Datalog** (deductive databases): function-free Horn rules whose head variables occur in the body. Least-model semantics; bottom-up evaluation always terminates on finite data (data complexity PTIME-complete, combined EXPTIME-complete). Top-down SLD resolution may not terminate without tabling. Extensions add stratified negation and aggregation, which preserve termination. Arithmetic that computes new values, and recursion through aggregates, can destroy it. "Datalog" in system names often means such an extended language: check the fragment.

### 7. [major] Project leakage: topic weighting and wording mirror project decisions
- **Evidence** (compare with `docs/project/decisions.md`):
  - "Exact decimals and rounding" is a *core concept*, in the Datalog bullet (l. 14), the index and the negation-and-aggregation reading path. It is the only page apart from foundations with substantial written content: a cross-language rounding table that matches decisions D7/D13/D18. In the KR&R literature, rounding modes are an engineering detail, not a core concept.
  - "lookup/constructor patterns" (l. 89) and the glossary entry "Lookup-before-invent" match D2/D14. The phrase is a coinage, not established terminology.
  - "rewriting known queries in advance" (l. 111) and the query-rewriting stub ("computed once and reused until the rules change") match D5 (pre-computed rewriting).
  - The soundness stub lists a "status taxonomy (complete by proof, complete by observation, sound but possibly incomplete, unknown)". This is D12's four completeness statuses, presented as "typical in tools and papers" with no source.
  - `notation.md` §4 says "Skolemised or 'named function' rules". "Named Skolem functions" is the F2 vocabulary of D1.
  - An empty `docs/domain/project/` directory exists inside the domain tree.
  - The running examples (contract conditions, basket free delivery at 200.00) are business-rule scenarios. That is acceptable in itself, but together with the above it gives the reference a project-shaped centre of gravity.
- **Why it matters:** `conventions.md` §1 forbids this. An agent will take the project's priorities and terms as the domain's ("lookup-before-invent" as a standard pattern, rounding as a core KR topic, a four-status taxonomy as established). An expert validator will notice the skew.
- **Fix:**
  - Rename `exact-decimals-and-rounding.md` to a broader **"Datatypes and built-ins"** page: XSD value spaces, arithmetic, comparisons and string functions, with rounding as one section. Move it out of the Datalog bullet into a "Built-ins and datatypes" line.
  - In the index, replace "lookup/constructor patterns" with "constructor predicates, identifier-minting built-ins, conditional invention".
  - Drop "rewriting known queries in advance" from the index line.
  - Either cite a source for the status taxonomy or remove it.
  - Delete `docs/domain/project/`.
  - Remove "named function" from `notation.md` or define it neutrally as "shared function symbol".

### 8. [major] Algorithm families (l. 32-37): ASP solving and model building are missing, and magic sets is misfiled
- **Problems:**
  - "There are three families: forward, backward, hybrid" leaves out:
    - **ground-and-solve** (grounding plus conflict-driven nogood learning) for ASP, even though LP/ASP is declared core;
    - **tableaux / model building and consequence-based** reasoning (the DL side, ELK);
    - **lazy grounding** and goal-directed ASP (s(CASP));
    - resolution-based FO reasoning.
  - Magic sets is listed under backward chaining. It is a rewriting that makes *bottom-up* evaluation goal-directed, and is usually classed as a hybrid.
  - "Join algorithms" links only to worst-case-optimal joins. RETE/TREAT (incremental matching), used by Jena and production systems, is absent.
- **Fix:** add a fourth bullet:
  > **Ground-and-solve** for stable models: grounding (with bounded or finitely-ground programs) followed by conflict-driven search (clingo, DLV); also lazy-grounding and goal-directed ASP.

  Add a line for model building (tableaux, consequence-based) in the adjacent DL circle. Move magic sets to hybrids, or describe it as "goal-directed bottom-up". Change "join algorithms" to "join and matching algorithms (hash and worst-case-optimal joins, RETE)".

### 9. [major] Decidability paragraph: classes listed without mapping, linear rules missing, data complexity defined wrong
- **Problems:**
  - "Complexity is measured as data complexity (rules fixed)" is wrong: data complexity fixes the rules *and the query*. The glossary has it right, so the two pages are inconsistent.
  - The sufficient conditions are not mapped to the abstract classes: acyclicity notions ⇒ FES, guarded ⇒ BTS, linear/sticky ⇒ FUS, warded ⇒ … Linear rules (DL-Lite, the most used FUS class), frontier-guarded rules, shy programs and aGRD are missing. GBTS appears in the index but not in the text.
  - There are no landmark complexity numbers. An entry page for agents should give a few anchors, e.g.:

    | Language | Data complexity | Combined complexity |
    |---|---|---|
    | Datalog | P-complete | EXPTIME-complete |
    | Linear | AC0 | PSPACE-complete |
    | Guarded | P-complete | 2EXPTIME-complete |
    | Sticky | AC0 | EXPTIME-complete |
    | Normal ASP (ground) | brave reasoning NP-complete | — |
    | Disjunctive ASP (ground) | Σ2P-complete | — |

    (From memory; tag `[U]` until checked against Calì–Gottlob–Pieris 2012 and Dantsin et al. 2001.)
  - Finite versus unrestricted entailment (finite controllability) is never mentioned. Agents will conflate "true in all finite models" with certain answers.
- **Fix:** add the mapping in one sentence, the table above tagged `[U]`, fix "rules and query fixed", and add one sentence on finite controllability.

### 10. [major] Partial results (end of l. 30): "sound for positive consequences" is undefined, and inconsistency is a hole
- **Problems:**
  - "Positive consequences" should be stated precisely: answers to monotone queries (UCQs) over monotone rules, read as constants-only answers.
  - With negative constraints, an early stop cannot certify *consistency*. Under FO an inconsistent KB entails everything, so "no ⊥ found yet" says nothing.
  - Recursive aggregation over monotone lattices (`#min`, `#max` in Soufflé, Datalog°) stays sound. "Aggregation ⇒ unsound" is too coarse.
- **Fix:**
  > If termination is not guaranteed, facts derived by any finite prefix of a fair chase are entailed, so answers to UCQs over constants are sound but may be incomplete; a computation stopped early can neither conclude that a fact is false nor that the knowledge base is consistent. With default negation or non-monotone aggregates evaluated over an incomplete lower layer, even returned answers may be wrong.

### 11. [minor] KR&R paragraph (l. 7): "Logic-based KR uses fragments of first-order logic"
- **Problem:** LP/ASP semantics (stable models, NAF) are nonmonotonic and not first-order. Datalog's least-model semantics expresses transitive closure, which is not first-order definable as a query.
- **Fix:** "Logic-based KR uses fragments of first-order logic and nonmonotonic extensions of them".

### 12. [minor] Facts, rules, queries (l. 9): the CQ example is informal and does not match the shared examples
- **Problems:**
  - "Which `x` have a superior who is a director?" uses a predicate (`director`) that appears in no example.
  - "Database or instance" is used without noting that `notation.md` reserves `D` for the input and `I` for any instance.
  - Rules are not said to be universally quantified.
- **Fix:** write the CQ formally, `q(x) = ∃y superiorOf(y, x) ∧ isCompanyDirector(y)`, and add "rules are implicitly universally quantified".

### 13. [minor] Description logics bullet (l. 17): "Horn fragments are closely related to existential rules" is vague
- **Problem:** DL-Lite_R and EL translate into linear and guarded existential rules. Horn-SHIQ needs EGDs for number restrictions. DLs in general also need disjunction and negation, which plain existential rules do not have. In the other direction, existential rules allow cyclic bodies and higher arity, which DLs do not.
- **Fix:** "DL-Lite_R and EL axioms are linear/guarded existential rules; Horn DLs with number restrictions also need EGDs; existential rules in turn allow non-tree-shaped bodies and arbitrary arity, which DLs lack."

### 14. [minor] Reasoning tasks (l. 22-28): incomplete
- **Missing:**
  - brave and cautious reasoning, and answer-set enumeration;
  - query containment *under constraints*, and FO/Datalog rewritability as a task;
  - (in)consistency handling.
- **Inaccurate:** "consistency checking (constraints, `⊥`)" does not say that in ASP a constraint *eliminates candidate models* rather than making the KB inconsistent.
- **Fix:** add these items and a clause on the meaning of constraints under each semantics.

### 15. [minor] Systems landscape (l. 39-44): no status, and coverage is skewed
- **Problems:**
  - No "as of" dates or maintenance status, which `conventions.md` §2 requires for systems. For example, Graal is superseded by InteGraal, and DDlog is archived.
  - Missing systems:
    - DLV2 / DLV± (existential rules);
    - Alpha and s(CASP) (ASP);
    - XSB and SWI-Prolog tabling (named in the backward page, but not here);
    - the rewriting engines Rapid, Clipper and Iqaros;
    - the RDF/OWL RL stores GraphDB and Stardog;
    - the DL reasoners HermiT and Konclude (adjacent).
  - The list order (Graal, InteGraal first) suggests one school's centre.
  - "Datalog engines: RDFox": RDFox also does equality (`owl:sameAs` rewriting) and SKOLEM-based invention, and the bullet does not say so.
- **Fix:** add the as-of note "(status as of <date> on each system page)" and the missing names, at least in `others`. Order alphabetically within each bullet.

### 16. [minor] Scope table: inconsistent with the folders and unjustified
- **Problems:**
  - Prolog and tabling are in the *adjacent* circle but covered by an `algorithms/` (core) page.
  - Scallop (probabilistic, neurosymbolic) is listed in `systems/others`, although probabilistic reasoning is out of scope.
  - "Uncertainty" overlaps with "probabilistic".
  - No reason is given for any exclusion.
  - Important out-of-scope neighbours are not named: production rules, inconsistency-tolerant semantics (AR/IAR), argumentation, belief revision, constraint programming/SMT, higher-order logic.
  - `conventions.md` says "short pages" where `main.md` says "light". This is a trivial difference.
- **Fix:** add a "why" column. Name the out-of-scope neighbours. Note that Scallop is listed only for its semiring-provenance idea.

### 17. [minor] Stub summaries reachable from the index make claims that are not hedged
These are not in `main.md` itself, but `main.md` routes agents to them, and the stubs carry no `[U]` tags.
- **query-rewriting:** "terminates exactly on finite unification sets". For a given query, rewriting terminates if and only if that query has a finite UCQ rewriting. FUS means this holds for all queries.
- **glossary, Classical negation:** "`¬p(a)` holds only if it is entailed" conflates truth in a model with entailment.
- **glossary, Skolem chase:** "equivalent to semi-oblivious" holds only for frontier-based Skolemisation. With Skolem terms over all body variables, the Skolem chase corresponds to the oblivious chase.
- **Fix:** tag these `[U]` or fix them now, and link the dispute process.

### 18. [minor] Reading paths: gaps and odd orderings
- **Missing paths:**
  - "LP/ASP" (stable models, grounding, solving, clingo/DLV);
  - "Equality" (EGDs, UNA, FDs, Rete/egglog);
  - "DL/OWL and RDF connection".
- **Odd ordering:**
  - "Rule-set optimisation" ends with provenance, which is not an optimisation topic.
  - In the index, perfect-model semantics comes before stratified negation, which reverses the dependency.
- **Fix:** add the three paths, reorder the index entries, and end the optimisation path with incremental maintenance.

### 19. [minor] Ambiguous terms used on the entry page without the needed qualification
- **Terms:**
  - "the chase" (variant unstated);
  - "null" (no warning about SQL `NULL`);
  - "negation" (default vs classical vs negation in queries vs negative constraints);
  - "completeness" (of answers vs refutation-completeness vs "X-complete" in complexity);
  - "model" (Herbrand model in LP vs any FO structure);
  - "ontology";
  - "Datalog" (see 6).
- **Fix:** add a short "Words that mean different things in different communities" box after the domain overview, with one line per term and a link to the glossary. Mark these glossary entries with community-specific readings.

### 20. [minor] References (entry points): the Datalog± and ASP primaries are missing, and the list leans toward one school
- **Missing:**
  - Calì, Gottlob & Lukasiewicz 2012 (JWS), the Datalog± reference, which is already cited in `notation.md`;
  - Fagin, Kolaitis, Miller & Popa 2005 (data exchange, TCS);
  - Gelfond & Lifschitz 1988 (stable models);
  - Ceri, Gottlob & Tanca 1989 ("What you always wanted to know about Datalog");
  - Gebser, Kaminski, Kaufmann & Schaub 2012, *Answer Set Solving in Practice*;
  - Arenas, Barceló, Libkin, Martens & Pieris, *Database Theory* (open textbook);
  - Chein & Mugnier 2009, *Graph-based Knowledge Representation*.
- **Fix:** add them. Keep at most about 10 entries, grouped by family.

### 21. [minor] Pedagogy: dense jargon before definition
- **Problem:** the overview uses "least, perfect, stable or well-founded", "MFA", "wardedness" and "piece-unifiers" before defining them. `conventions.md` §9 says "define before use". There is no inline worked example: the reader is sent to `examples.md`.
- **Fix:** keep the overview but add one five-line worked example inline: chain of command, one fact derived, one CQ answer, and then the teaching ontology with one null and why `(alice, n1)` is not a certain answer. In the decidability paragraph, link the class names to the glossary.

### 22. [nit] Line 37: the "Underneath / Above" metaphor is vague
- **Fix:** "Building blocks: …; rule-set level analyses and transformations: …".

### 23. [nit] Line 44: "data-exchange chase engines" is unnamed
- **Fix:** name them (Llunatic, ChaseFUN, PDQ), as in `others.md`.

### 24. [nit] Glossary "Core": "no proper endomorphism" is ambiguous
- **Problem:** `foundations.md` says "every endomorphism is injective".
- **Fix:** "no homomorphism into a proper subset of itself (no proper retraction)".

### 25. [nit] Index line for `notation`: claims a mapping to "Datalog±" notation, but most DLGP cells are tagged `[U]`
- **Fix:** add "(partly unverified)".

---

## Overall verdict

The page is well structured. The links are clean, the index is complete and the scope circles are coherent. It is a reasonable skeleton. It is **not yet fit to serve as "the source of truth"** for three reasons:

1. It hides the fact that almost every page it routes to is a stub, and it does not say what is authoritative when pages are empty or disagree.
2. Several headline technical statements are wrong or over-broad: the cause of undecidability, "LP has a designated model", "readings agree on positive rules", "two ways to invent", and Skolem function vs named function symbol. These are exactly the points an existential-rules expert will check first.
3. Its topic weighting and some terms (exact decimals as core, lookup-before-invent, pre-computed rewriting, the completeness-status taxonomy, "named function") visibly mirror the project's decisions, against `conventions.md` §1.

Coverage is skewed. ASP solving, nonmonotonic and disjunctive existential rules, conceptual-graph roots, linear rules, and production rules (even as out of scope) are missing.

## Top 5 fixes

1. Add a **Status and authority** paragraph and per-page stub markers in the index and reading paths (finding 1).
2. Rewrite the **decidability** sentence: undecidability comes from expressive power, decidable ≠ terminating chase, variant-dependent termination, data complexity fixes rules *and* query, and a small landmark complexity table tagged `[U]` (findings 2, 9).
3. Rewrite **Open world and closed world**: separate Herbrand/UNA/domain closure, NAF, Reiter's CWA, brave and cautious reasoning over many stable models, three-valued WFS, and restrict "agree" to Datalog/UCQ or to answers over constants (finding 3).
4. Rewrite **Value invention**: semantics vs chase, rule-local Skolemisation `f^ρ_z` vs shared named function symbols, the full list of mechanisms, and hedged consequences. Fix `notation.md` §4 and `examples.md` (b) wording to match (finding 4).
5. **Remove the project-shaped weighting**: fold rounding into a "Datatypes and built-ins" page, drop "lookup-before-invent", "rewriting known queries in advance" and the unsourced status taxonomy, and delete `docs/domain/project/`. Rebalance coverage with ASP ground-and-solve, nonmonotonic and disjunctive existential rules, conceptual-graph rules, linear rules and the Datalog± primary reference (findings 5, 7, 8, 20).
