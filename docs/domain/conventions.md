# Conventions of the domain reference

Rules for every page of this reference and for every contributor, human or AI agent: purpose and scope, page template, naming, notation, linking, claim tags, language, and the process to propose, enrich and challenge content. A page that does not follow these rules is not finished.

## 1. Purpose and dependency rule

- This reference is an **encyclopedia of logic-based knowledge representation and rule-based reasoning**: definitions, notation, results, algorithms, systems and benchmarks. It aims to be the single source of truth on the domain, so that readers share one vocabulary and one notation instead of rebuilding partial, inconsistent understandings.
- It is **project-agnostic**. Pages describe the domain as found in the literature and in system documentation. They never mention a particular project, its decisions, plans, requirements or internal identifiers, and never link outside this folder except to primary sources (papers, standards, official documentation, source repositories).
- Other documents may link here; this reference never links back to them.
- Nothing here is final. Every statement can be challenged and revisited through the process of §8.

## 2. Page template

Concept and algorithm pages use these sections, in this order. Section titles are fixed so that they can be searched. If a section is truly empty, keep the heading and write "None known."

```markdown
# <Title in sentence case>

<Summary: one paragraph (3-6 sentences): what this is and why it matters.>

## Intuition
<Plain words and a small example, preferably from examples.md.>

## Formal definitions
<Precise definitions in the notation of notation.md.>

## Key results
<Theorems, complexity, decidability; each with a reference; [U] where unverified.>

## Variants and terminology across communities
<Same notion under other names (databases, logic programming, KR, description logics, semantic web); notational differences; near-synonyms that are NOT the same notion.>

## Pitfalls
<Classic mistakes and semantic traps.>

## Disputes and open issues
<Open research questions, contested claims, and entries of disputes.md that concern this page.>

## Related pages
<Relative links, one line each saying why.>

## References
<Primary literature and official documentation: authors, title, venue, year, DOI or URL when known.>
```

Variants:
- **System pages** (`systems/`) replace *Intuition*, *Formal definitions* and *Key results* by *Overview* (purpose, implementation language, licence, maintenance status with an "as of" date), *Language and semantics* (supported fragment, chase variant, negation, aggregation, equality, value invention), *Algorithms and notable techniques*, *Limitations*. Every version, licence or maintenance claim carries an "as of <date>" and a source.
- **Evaluation pages** (`evaluation/`) use free sections but keep the summary, *Pitfalls*, *Disputes and open issues*, *Related pages* and *References*.
- **Adjacent pages** (`adjacent/`) are deliberately short: summary, key notions, connection with the core topics, pointers to the literature.

### 2.1 Stubs

A **stub** has: title, summary, a `## TODO (coverage)` checklist, *Related pages*, and *Key references*. A writer filling a stub replaces the TODO list by the template sections. A page without a TODO section is considered written; it can still be enriched or challenged. A stub's summary is orientation, not a definition: until the page is written, the primary literature it cites is authoritative on its topic ([main](main.md#status-and-authority)). The page index of [main](main.md#page-index) marks each page *written* or *stub*; update the marker when a page is filled.

## 3. Scope

The reference is organised in three circles (see [main](main.md#scope)):

| Circle | Topics | Depth |
|---|---|---|
| Core | Datalog and extensions, existential rules and the chase, logic programming and ASP, negation, aggregation, equality, Skolemisation and value invention, datatypes and built-ins, decidability and complexity, algorithms, systems, benchmarks | in depth |
| Adjacent | description logics and OWL, RDF and SPARQL, Prolog as a language (tabling is core), ontology-based data access | light: short pages, focused on the connection with the core |
| Out (for now) | probabilistic and uncertain reasoning, temporal reasoning, production rules, inconsistency-tolerant semantics, argumentation, belief revision, constraint programming and SMT, higher-order logic (reasons in [main](main.md#scope)) | not covered; propose a page through §8 if needed |

## 4. Naming and layout

- File names: kebab-case, English, `.md`, no numbers, no dates (`semi-naive-evaluation.md`).
- Folders: `concepts/`, `algorithms/`, `systems/`, `evaluation/`, `adjacent/`. A new folder needs an entry in [main](main.md) and in this file.
- One notion per page; split pages longer than about 400 lines.
- Page title: the notion in sentence case (`# Semi-naive evaluation`).
- Every new page is added to the index of [main](main.md#page-index); every new term to the [glossary](glossary.md).

## 5. Notation

- [notation.md](notation.md) is normative. Do not introduce a new symbol for a notion that already has one; if a notion is missing, add it to `notation.md` first (§8).
- When a source uses another notation, translate it; if the translation loses something, say so in *Variants and terminology*.
- Default negation is `not`; classical negation is `¬` ([notation §4](notation.md#4-rules)).
- Program-syntax examples state their convention (Datalog/ASP style or DLGP style, [notation §8](notation.md#8-program-syntax-in-examples)).
- Terminology: a **labelled null** is a term occurring in instances that stands for an unknown individual; an **existential variable** is a variable quantified by `∃` in a rule head. Do not use "null" for the variable nor "existential" for the term.

## 6. Linking and references

- Internal links are **relative** (`[chase variants](../algorithms/chase-variants.md)`). The first mention of a notion that has its own page links to that page.
- External references point to **primary sources**: the paper that introduced or proved a result, a standard, the official documentation or repository of a system. Surveys and textbooks are welcome as entry points, in addition to primary sources.
- Format: authors, *title*, venue, year, and a DOI or URL when known. Tag `[U]` any bibliographic detail that was not checked against the source.
- Do not copy long passages; summarise and cite.

## 7. Claim tags

| Tag | Meaning |
|---|---|
| (none) | Standard material the writer stands behind, with a reference in *Key results* or *References*. |
| `[U]` | **Unverified**: from memory or a secondary source; bibliographic details, complexity bounds and claims about systems not checked against the primary source. Must be removed only by someone who checked the source, naming it. |
| `[D]` | **Disputed**: a claim challenged through §8 and not yet settled. Always followed by a link to its entry in [disputes](disputes.md). |

When in doubt, tag `[U]`.

## 8. Contribution and challenge process

| Situation | What to do |
|---|---|
| **Propose a page** | Create a stub (§2.1) in the right folder, add it to the index of [main](main.md#page-index), and add its terms to the [glossary](glossary.md). Check first that the notion is not covered under another name (glossary, *Variants and terminology* sections). |
| **Enrich a page** | Add content, examples, results or references that do not change existing definitions. Keep the template; keep tags; add new terms to the glossary. |
| **Challenge a claim** | Do not rewrite it. Tag the claim `[D]`, add an entry to the page's *Disputes and open issues* section, and log it in [disputes](disputes.md) with the claim, the objection, and the evidence (references, counter-examples, runs). |
| **Resolve a dispute** | Once the evidence settles it, edit the claim, remove the `[D]` tag, and close the entry in [disputes](disputes.md) with the resolution and its evidence. Keep the closed entry: the log is append-only. |
| **Two sources disagree** | Present both, with references, in *Variants and terminology* (if it is a convention) or *Disputes and open issues* (if it is a factual or mathematical disagreement). Do not pick one silently. |
| **Change notation or a definition** | Never silently. Open a dispute (or a notation proposal) in [disputes](disputes.md), update [notation](notation.md) and every page using the old form in the same change, and record the change in the log. |
| **Fix a typo, a broken link, a formatting issue** | Fix it directly. |

Never change the meaning of a definition or result while "editing for clarity".

## 9. Language

- English everywhere. The [glossary](glossary.md) gives French equivalents.
- Short sentences, active voice, no marketing tone, no emojis. Define a term before using it, or link to it.
- Prefer tables and lists to long paragraphs. A page is a dense map to the literature, not a textbook chapter.
- "Must" is reserved for definitions and theorems (what a notion requires); avoid recommendations: the reference describes the domain, it does not prescribe design choices.
