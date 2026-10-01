# Labelled nulls

Labelled nulls are the terms created by existential rules to stand for unknown individuals. They are the third term kind of the data model and must be represented identically in every store, a lesson from Graal and InteGraal where they were not. F2 has no syntax that creates them, but the data model carries them from day one.

> **Status in this project:** `v0` (data model only) `later` (F3 semantics) — [E5](../project/requirements.md#e5), three term kinds [D1](../project/decisions.md#d1); rejection of nulls in F2 input proposed ([OP-13](../project/open-questions.md#op-13)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definition: nulls vs constants vs variables; renaming, homomorphisms that move nulls.
- [ ] Why answers containing nulls are not certain answers.
- [ ] Store consistency requirement (E5): identity, serialisation, round-trip through every store, no name collisions (Graal `EE<n>` prefix bug).
- [ ] Graal and InteGraal inconsistencies (reports 02, 04) as the cautionary tale.
- [ ] Relation with Skolem terms: a null is a Skolem term whose identity is forgotten (and D8).
- [ ] Negation and aggregation over nulls: why report 07 Choice A restricted them to null-free positions.
- [ ] Design notes for the term encoding (tagging bits, dictionary) — link architecture principles.

## Related pages

- [foundations](../concepts/foundations.md)
- [existential rules](../concepts/existential-rules.md)
- [skolem functions and terms](../concepts/skolem-functions-and-terms.md)
- [architecture principles](../engineering/architecture-principles.md)
- [graal](../systems/graal.md)
- [integraal](../systems/integraal.md)

## Key references

- [report 02 §3 forward chaining](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 04 §2](../../preliminary-analysis/04-graal-vs-integraal.md)
- [report 07 §4](../../preliminary-analysis/07-sota-theory.md)
- [report 11 §1.1, OP-13](../../preliminary-analysis/11-f2-framework-definition.md)
- [README E5, D1](../../preliminary-analysis/README.md)
