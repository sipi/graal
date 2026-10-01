# Project documentation: start here

Every agent working on this project starts with this page. It gives the reading order, the dependency rule between the project documents and the domain reference, the source-of-truth hierarchy, and the conventions of the project pages.

**The project in one paragraph.** We are building a new, homemade but solid logical reasoning engine. It is a new core guided by the design of Graal (the legacy Java code base in this repository), which serves as a test oracle on the fragment where it is valid. The engine is meant as a formal reasoning layer for AI agents and for enterprise business-domain modelling, and later for aerospace and defense. Its differentiator is a decidability and complexity analyser that drives the choice of algorithm. It is always sound, and it is complete whenever completeness can be proved or observed. Otherwise it says so explicitly. The v1 framework is **F2**: stratified Datalog with named Skolem functions under perfect-model semantics, with exact decimals ([D1](decisions.md#d1)). The first engine, **v0**, is plain positive Datalog ([D6](decisions.md#d6)).

## Reading order

| # | Page | Why |
|---|---|---|
| 1 | this file | orientation, rules, hierarchy |
| 2 | [vision and scope](vision-and-scope.md) | what we build, for whom, what is in and out at each phase |
| 3 | [requirements](requirements.md) | E1-E13 explained |
| 4 | [decisions](decisions.md) | D1-D19 with rationale and consequences |
| 5 | [open questions](open-questions.md) | what is not decided (Q1, OP-3, OP-23, language, ...) |
| 6 | [roadmap](roadmap.md) | phases, v0 / F2 (v1) / later |
| 7 | [how agents work here](how-agents-work-here.md) | development model, rules of conduct, commits, where things live |

Then, as needed: [running examples](running-examples.md), [architecture principles](architecture-principles.md), [test strategy](test-strategy.md), [language choice](language-choice-kotlin-vs-rust.md), [licensing and provenance](licensing-and-provenance.md), [domain stage-2 plan](domain-stage2-plan.md).

<a id="domain-introduction"></a>
## Domain introduction focused on the project's theoretical framework

*To be written.* A short introduction to the domain notions that F2 relies on, with links to the domain pages. It will be injected into every agent's context. Until it exists, read the [domain reference entry page](../domain/main.md) and its "first contact" reading path.

## The domain reference: `docs/domain/`

- [`docs/domain/`](../domain/main.md) is the **source of truth on the domain**: definitions, [notation](../domain/notation.md), results, algorithms, systems and benchmarks of logic-based knowledge representation and rule-based reasoning. Use its notation and terminology. Do not rebuild your own definitions.
- Consult it **as needed**, from the links in project pages. You do not need to read it end to end.
- It is **project-agnostic** and can always be challenged and enriched. To enrich a page, or to dispute a claim, follow its [contribution and challenge process](../domain/conventions.md#8-contribution-and-challenge-process) and its [disputes log](../domain/disputes.md). Never silently rewrite a definition.

### Dependency rule

- Project documents (this folder, `docs/preliminary-analysis/`, code, tests) **may link to** `docs/domain/`, and should, rather than re-explaining domain notions.
- `docs/domain/` **never links to or mentions** the project: no D/E/Q/OP identifiers, no F2, no project status, no links to project folders. Project-specific choices, such as which semantics or algorithm we adopt, belong here.
- A notion coined by the project (for example lookup-before-invent with `@lookup`, the four completeness statuses, rule N1) is defined in project documents. The general notion behind it is described in the domain reference: [value-invention strategies](../domain/concepts/value-invention-strategies.md), [soundness and completeness of partial results](../domain/concepts/soundness-and-completeness-of-partial-results.md).

## Source-of-truth hierarchy (project)

1. **README decisions and requirements** ([`../preliminary-analysis/README.md`](../preliminary-analysis/README.md)): D1-D19, E1-E13, Corrections, open questions Q*. **Authoritative.**
2. **F2 framework definition**, [report 11](../preliminary-analysis/11-f2-framework-definition.md). It was **validated by the owner on 2026-10-01, except OP-3**, which waits for [report 14](../preliminary-analysis/14-uniqueness-and-functionality.md). OP-23 was raised by the validation and is open. It is the reference contract for conformance tests and the implementation.
3. **Other reports** in [`../preliminary-analysis/`](../preliminary-analysis/README.md) (01-09, 12, 13; 14 in progress).
4. **Project pages** (this folder): they summarise and link; they never override a decision.

For domain knowledge (definitions, results), [`docs/domain/`](../domain/main.md) is the reference. If a report and the domain reference disagree on a domain fact, record a dispute in the [domain disputes log](../domain/disputes.md) and tell the owner. If a project page and a higher project source disagree, the higher source wins. Report the conflict in [open questions](open-questions.md#known-inconsistencies) and to the owner. Never resolve it silently.

## Conventions for project pages

- Same language rules as the domain reference: English, short sentences, no emojis. The [domain glossary](../domain/glossary.md) gives French equivalents.
- Project pages carry a status box:

  ```markdown
  > **Status in this project:** `<scope labels>` — <one line, with links to D*/E*/Q*/OP-*>.
  > **Page maturity:** stub | draft | reviewed-by-architect | validated-by-owner · checked against README <date>
  ```

  | Label | Meaning |
  |---|---|
  | `v0` | In the first engine: plain positive Datalog ([D6](decisions.md#d6)). |
  | `F2` | In the v1 framework F2 ([D1](decisions.md#d1)), after v0. |
  | `later` | Expected after v1 (e.g. rewriting and backward chaining per [D16](decisions.md#d16), F3 with labelled nulls, co-reference option of [D10](decisions.md#d10)). |
  | `out-of-scope` | Explicitly excluded (e.g. general GBTS algorithms, [E10](requirements.md#e10); uncertainty and time). |
  | `open` | Not decided; link to the open question. |

  Only the owner, or an agent quoting an explicit owner message, may set `validated-by-owner`.
- Claim tags in project pages: `[U]` unverified, `[U-own]` a proposition formulated in our reports with a sketch only (needs a proof sheet, [E9](requirements.md#e9)), `[E]` checked empirically, with the run described.
- Anchors are stable: `decisions.md#d6`, `requirements.md#e4`, `open-questions.md#q1`, `open-questions.md#op-3`.
- Link reports with section numbers: `[report 11 §6.3](../preliminary-analysis/11-f2-framework-definition.md)`. Link domain notions to their domain page.
- Terminology (owner decision, README): a **labelled null** is an object produced in facts for an unknown individual; **existential variable** is used only for rule syntax; "existential witness" is only an explanatory gloss.

## Index of project pages

- [vision and scope](vision-and-scope.md), [requirements](requirements.md), [decisions](decisions.md), [open questions](open-questions.md), [roadmap](roadmap.md), [how agents work here](how-agents-work-here.md).
- [running examples](running-examples.md): E1-ex, E2-ex, E3-ex (corrected) and v0-ex in the provisional syntax.
- [architecture principles](architecture-principles.md), [test strategy](test-strategy.md): the engine's architecture and conformance suite.
- [language choice: Kotlin vs Rust](language-choice-kotlin-vs-rust.md) (open), [licensing and provenance](licensing-and-provenance.md).
- [domain stage-2 plan](domain-stage2-plan.md): work plan for the writers who fill the domain stubs.
