# Equality and the unique-name assumption

How the engine treats equality between terms: unique names for constants, free-constructor reading of named terms, functional dependencies as checked constraints rather than equality rules (D2), and the open tension raised by D8 and by the corrected E3-ex, where the modeller's intent requires an invented term to equal a constant.

> **Status in this project:** `F2` `open` — [D2](../project/decisions.md#d2) (FDs are integrity constraints), [D8](../project/decisions.md#d8) caveat, E3-ex correction and [Q1](../project/open-questions.md#q1); report 09 §4 options O0-O3.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] UNA for constants; Herbrand UNA for function terms; FO reading without UNA.
- [ ] EGDs and FDs: undecidability with TGDs (FD + IND), harmless EGDs, separable keys.
- [ ] Report 09 §4 options O0 (free constructors), O1 (constraints), O2 (lookup-before-invent), O3 (equality reasoning, union-find).
- [ ] D2: FDs checked, never used for equality; violations reported.
- [ ] D8 caveat: symbolic Skolem terms that may denote the same individual; effect on counting and aggregates.
- [ ] E3-ex correction: the chain could stop at the director only with equality between invented terms and constants (Q1).
- [ ] Implementation techniques if equality is ever added: union-find with rebuild (RDFox, egglog), singularisation [U].

## Related pages

- [foundations](../concepts/foundations.md)
- [lookup before invent](../concepts/lookup-before-invent.md)
- [skolem functions and terms](../concepts/skolem-functions-and-terms.md)
- [aggregation](../concepts/aggregation.md)
- [open questions](../project/open-questions.md)

## Key references

- [report 09 §1.3, §4](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 11 §1.4, §4.3](../../preliminary-analysis/11-f2-framework-definition.md)
- [README D2, D8, Corrections, Q1](../../preliminary-analysis/README.md)
- [report 06 §5 idea 6](../../preliminary-analysis/06-sota-engines.md)
- Bellomarini, Benedetto, Brandetti, Sallinger. *Exploiting the power of equality-generating dependencies in ontological reasoning*. PVLDB 2022 [V in report 09].
