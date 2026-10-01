# Homomorphism search

Finding homomorphisms from a conjunction of atoms into a fact base is the inner loop of query answering, rule application, restricted-chase checks and rewriting subsumption. E4 requires exploiting the bi-connected components of the query; this page covers backtracking search, variable ordering, backjumping, decomposition and indexing.

> **Status in this project:** `v0` `F2` — [E4](../project/requirements.md#e4) (bi-connected components); spike scope ([E6](../project/requirements.md#e6)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Problem statement and complexity (NP-complete; tractable for bounded (hyper)treewidth).
- [ ] Backtracking with variable/atom ordering heuristics, forward checking, backjumping (conflict-directed).
- [ ] Bi-connected component decomposition of the query (Graal) and its generalisation to hypertree/GHD plans [U].
- [ ] Index structures for small KBs (many small KBs workload) vs WCOJ for large ones.
- [ ] Variants needed: all answers, existence check, homomorphism between two CQs (subsumption), with fixed terms.
- [ ] Graal and InteGraal state (report 02 §3, report 04: BCC missing in InteGraal main path).
- [ ] Tests and benchmarks (spike: LUBM, 1,000 small KBs).

## Related pages

- [foundations](../concepts/foundations.md)
- [worst case optimal joins](../algorithms/worst-case-optimal-joins.md)
- [semi naive evaluation](../algorithms/semi-naive-evaluation.md)
- [query rewriting pure](../algorithms/query-rewriting-pure.md)
- [graal](../systems/graal.md)

## Key references

- [report 02 §3](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 04 §1](../../preliminary-analysis/04-graal-vs-integraal.md)
- [report 06 §5 idea 2](../../preliminary-analysis/06-sota-engines.md)
- [README E4, E6](../../preliminary-analysis/README.md)
- Baget, Mugnier et al. Graal homomorphism with bi-connected components [U: exact paper to identify].
- Gottlob, Leone, Scarcello. *Hypertree decompositions and tractable queries*. JCSS 2002 [U].
