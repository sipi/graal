# Conjunctive queries and unions (CQ, UCQ)

Conjunctive queries (select-project-join queries, existentially quantified conjunctions of atoms) are the query language of the engine; unions of CQs (UCQs) are what query rewriting produces. This page covers CQs, UCQs, CQs with negation and aggregates (NCQ) as defined for F2, containment and minimisation.

> **Status in this project:** `v0` (CQ, UCQ open) `F2` (NCQ, pre-registered queries) — [E4](../project/requirements.md#e4), [E10](../project/requirements.md#e10); report 11 §1.6 (DRAFT).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definitions: CQ, Boolean CQ, answer variables, UCQ, NCQ/UNCQ (report 11 §1.6), answer modes `all`/`constants` (OP-1).
- [ ] Evaluation by homomorphism; certain answers vs perfect-model answers.
- [ ] Containment and equivalence (Chandra-Merlin), minimisation (cores), UCQ subsumption used by rewriting.
- [ ] Complexity: NP-complete combined, AC0 data; tractable classes (acyclic, bounded hypertree width) [U].
- [ ] Pre-registered queries (`@query`) and why they matter for a-priori rewriting.
- [ ] Examples from E1-ex, E2-ex, v0-ex.

## Related pages

- [foundations](../concepts/foundations.md)
- [homomorphism search](../algorithms/homomorphism-search.md)
- [query rewriting pure](../algorithms/query-rewriting-pure.md)
- [precomputed rewriting](../algorithms/precomputed-rewriting.md)

## Key references

- [report 11 §1.6, §5](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 07 §2](../../preliminary-analysis/07-sota-theory.md)
- Chandra, Merlin. STOC 1977 [U].
- Abiteboul, Hull, Vianu. *Foundations of Databases*, ch. 4-6 [U].
