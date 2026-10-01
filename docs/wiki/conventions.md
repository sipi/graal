# Wiki conventions

Rules that every wiki page and every wiki writer (human or AI agent) must follow: page template, file naming, notation, linking, tagging of unverified claims, language, and the procedure to propose changes. A page that does not follow these rules is not finished.

> **Status in this project:** `v0` `F2` `later` (applies to all pages) — governed by [E11](project/requirements.md#e11) (development model) and the source-of-truth hierarchy of [main](main.md#source-of-truth-hierarchy).
> **Page maturity:** reviewed-by-architect · checked against README 2026-10-01

## 1. Source of truth (reminder)

The wiki **summarises and links**; it never decides. Order of authority:

1. Decisions `D*`, requirements `E*`, corrections and open questions `Q*` in [`../preliminary-analysis/README.md`](../preliminary-analysis/README.md).
2. The F2 framework definition, [report 11](../preliminary-analysis/11-f2-framework-definition.md), **once validated by the owner** (it is a DRAFT today: cite it, but label its `[choice]` items as proposals).
3. The other reports in [`../preliminary-analysis/`](../preliminary-analysis/README.md).
4. Wiki pages.

If a wiki page disagrees with a higher source, the higher source wins and the conflict is **reported** (see §8), never silently resolved.

## 2. Page template

Every page (concept, algorithm, system, engineering, legal) uses these sections, in this order. Section titles are fixed so that agents can grep them. Omit a section only if it is truly empty, and then write "None." rather than deleting the heading in concept and algorithm pages.

```markdown
# <Title in sentence case>

<One paragraph (3-6 sentences): what this is, why it matters for the engine.>

> **Status in this project:** `<scope labels>` — <one line, with links to D*/E*/Q*/OP-*>.
> **Page maturity:** stub | draft | reviewed-by-architect | validated-by-owner · checked against README <date>

## Intuition
<Plain words + a tiny example, preferably a running example (E1-ex, E2-ex, E3-ex, v0-ex).>

## Formal definition
<Precise definitions, consistent with concepts/foundations.md notation.>

## Key properties and results
<Theorems, complexity, decidability, with a reference for each; [U] where unverified.>

## In this project
<How the engine uses it, which decision governs it, what is still open. Optional for systems pages.>

## Pitfalls
<Classic mistakes, especially semantic traps an implementer could fall into.>

## Related pages
<Relative links to other wiki pages, one line each saying why.>

## References
<Reports first (with section), then papers / docs. Each external reference: authors, title, venue, year; [V]/[U] tag.>
```

Variants:
- **System pages** (`systems/`) replace *Intuition / Formal definition / Key properties* by *Overview* (language, licence, maintenance, scope), *Strong ideas to borrow*, *Weaknesses and gaps*, *Use in this project* (oracle, backend, inspiration, nothing).
- **Project pages** (`project/`) and **engineering/legal pages** use free sections, but keep the title, summary paragraph, status box, *Related pages* and *References*.

### 2.1 Scope labels for the status box

| Label | Meaning |
|---|---|
| `v0` | In the first engine: plain positive Datalog ([D6](project/decisions.md#d6)). |
| `F2` | In the v1 framework F2 (stratified Datalog with named Skolem functions, [D1](project/decisions.md#d1)), after v0. |
| `later` | Expected after F2 (e.g. F3 hybrid with labelled nulls, recursive aggregation, Lean mechanisation). |
| `out-of-scope` | Explicitly excluded (e.g. general GBTS algorithms, [E10](project/requirements.md#e10); uncertainty and time). |
| `open` | Not decided. Must link to the open question (`Q*`, `OP-*`, or the [open questions page](project/open-questions.md)). |
| `background` | Theory or ecosystem knowledge needed to understand the project, with no implementation commitment. |

Several labels may be combined, e.g. `v0` (Datalog part) + `F2` (function terms). Never write "in scope" without saying which phase.

### 2.2 Page maturity

- `stub`: title, summary, status box, TODO list, key references. Written in stage 1.
- `draft`: all template sections filled by a writer agent; not yet cross-checked.
- `reviewed-by-architect`: cross-checked for consistency with README, report 11 and neighbouring pages.
- `validated-by-owner`: the project owner has validated the theory on the page. Only the owner (or an agent quoting an explicit owner message) may set this.

## 3. Naming and layout

- File names: **kebab-case**, English, `.md`, no numbers, no dates: `semi-naive-evaluation.md`.
- One concept per page. If a page exceeds about 400 lines, split it and link the parts.
- Sections (folders): `project/`, `concepts/`, `algorithms/`, `systems/`, `engineering/`, `legal/`. New folders need an entry in [main](main.md) and in this file.
- Page title = the concept name in sentence case (`# Semi-naive evaluation`), not a file name.
- Every new page must be added to the index in [main.md](main.md#page-index) and its terms to the [glossary](glossary.md).

## 4. Notation

The reference notation is defined in [concepts/foundations.md](concepts/foundations.md). Summary:

### 4.1 Logic notation (in prose and formal definitions)

- Predicates `p, q, employee`; constants `a, b, tom, c1`; variables `x, y, z` (lower case in logic formulas); function symbols `f, g, manager`.
- Tuples with a bar or bold: `x̄`. Substitutions and homomorphisms `σ, θ, h`; application written `σ(t)` or `tσ`.
- Existential rule (TGD): `∀x̄ ∀ȳ (B[x̄, ȳ] → ∃z̄ H[x̄, z̄])`; `x̄` is the frontier. Quantifier `∀` may be left implicit.
- Negated literal: `¬p(x)` in logic, `not p(X)` in program syntax (negation as failure under stratified semantics, see [stratified negation](concepts/stratified-negation.md)).
- Database / instance `D` (or `I`), rule set `Σ` (existential rules) or `P` (programs with functions), query `q` or `Q`, knowledge base `K`.
- Entailment `⊨`; certain answers `cert(q, Σ, D)`; perfect model `PM(K)`; least Herbrand model `LHM(P ∪ D)`.

### 4.2 Program syntax (provisional, DLGP-like)

The concrete syntax is **provisional** and not decided ([report 11](../preliminary-analysis/11-f2-framework-definition.md) §1: the abstract syntax is normative). Use the syntax of report 11:

```prolog
% comment
@function manager/1.                                % named function declaration (F2)
@lookup manager(X) = Y :- recordedManager(X, Y).    % lookup-before-invent (F2, D2)
employee(tom).                                       % fact
superiorOf(Y, X) :- managerOf(Y, X).                 % rule: head :- body.
generalConditionsApply(C) :- contract(C), not specificConditionApplies(C).
total(B, S) :- basket(B), S = #sum{ P*Q, I : inBasket(B, I, Q), price(I, P) }.
! :- managerOf(X, X).                                % integrity constraint
?(X) :- employee(X).                                 % query
```

- Variables start with an upper-case letter (or `_`, anonymous); constants and predicates start with a lower-case letter.
- **Existential variables** are not part of F2. When a page must show an existential rule in code form, use the DLGP convention (a head variable that does not occur in the body is existential) **and** add a comment `% Y existential`, or prefer logic notation.
- Decimal literals as in report 11 (`200.00`). Rounding mode names follow [D7](project/decisions.md#d7) (`floor`, `round`, `bank_round`); the names in report 11 §1.3 are superseded (see [exact decimals](concepts/exact-decimals-and-rounding.md)).

## 5. Linking rules

- Always **relative links**: `[semi-naive evaluation](../algorithms/semi-naive-evaluation.md)`. Never absolute GitHub URLs to files of this repository.
- Requirements, decisions and open questions have stable anchors: `project/requirements.md#e4`, `project/decisions.md#d6`, `project/open-questions.md#q1`. Report 11 open points: `project/open-questions.md#op-7` (summary) or the report itself, §11.
- Link reports with section numbers: `[report 11 §6.3](../../preliminary-analysis/11-f2-framework-definition.md)`. Do not copy long passages from reports: summarise in a few lines and link.
- The first mention of a glossary term on a page links to its page (not to the glossary).
- Report 13 (Apache Jena rule engine) lives at `docs/preliminary-analysis/13-jena-rule-engine.md`.

## 6. Tagging claims

| Tag | Meaning |
|---|---|
| `[V]` | Verified against the primary source (paper, documentation, code) by the agent who wrote it, or tagged `[V]` in a report. |
| `[U]` | **Unverified**: from memory or secondary source. Mandatory for every bibliographic detail, complexity bound or claim about another system that was not checked. Inherit `[U]` / `[U-own]` from reports. |
| `[U-own]` | A proposition formulated within this project (reports 09, 11, 12) with a proof sketch only; needs a proof sheet (E9) before being relied upon. |
| `[E]` | Checked empirically (a run of clingo, Nemo, Graal, a simulator), with the run described or linked. |
| `[choice]` | A proposal of report 11 awaiting owner validation. Never present it as decided. |

A claim with no tag is a claim the writer stands behind as standard textbook material. When in doubt, tag `[U]`.

## 7. Language rules

- English everywhere: pages, code, identifiers, comments, commit messages. The [glossary](glossary.md) gives French equivalents for the owner.
- Short sentences, active voice, no marketing tone. Define a term before using it, or link to it.
- Prefer tables and lists to long paragraphs. Keep pages dense: a page is a map to the literature and the reports, not a textbook.
- No emojis. Use "must / must not" only for normative statements backed by a decision, requirement or report 11 rule; otherwise "should" or "we propose".

## 8. Proposing changes

| Situation | What to do |
|---|---|
| You found a contradiction between sources | Do **not** pick a side. Add an entry to the *Known inconsistencies* section of [open questions](project/open-questions.md#known-inconsistencies) with both citations, and mention it in your report to the owner. |
| You need a choice that no decision covers | Write it as **open** on the page and add it to [open questions](project/open-questions.md) with options and, if you wish, a recommended default. |
| You think a decision is wrong or incomplete | Same as above, labelled "challenge to D*". The decision stays in force until the owner changes the README. |
| The owner takes a decision | It is recorded in the README first (D*, with a date), then [decisions](project/decisions.md) and affected pages are updated with a link. |
| A wiki page is wrong w.r.t. its sources | Fix it directly and say so in the commit message. |

Never change the meaning of a decision, requirement or semantic definition while "editing for clarity". Semantic changes go through the owner.
