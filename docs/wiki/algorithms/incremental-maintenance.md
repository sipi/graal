# Incremental maintenance

Updating a materialised model when facts (or rules) are added or removed, without recomputing from scratch: DRed (delete and re-derive), FBF (forward/backward/forward), B/F, counting. Essential for update-heavy workloads; the Skolem-chase style of F2 materialisation makes Datalog techniques applicable.

> **Status in this project:** `F2` `open` — README key theory points (Skolem chase enables FBF / B-F); report 11 §8.1, §8.4 (materialised part maintained incrementally); v0 inclusion open.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] DRed, FBF, B/F, counting-based methods; costs and trade-offs.
- [ ] Applicability with function terms (Datalog with terms) and with stratified negation and aggregation.
- [ ] Rule changes vs data changes; interaction with stored rewritings (invalidation).
- [ ] Open research gaps: incremental restricted/core chase (README); named terms with equality (report 09 §7 item 8).
- [ ] Prior art: RDFox (AIJ 2019, AAAI 2018), differential dataflow / DDlog (not recommended).

## Related pages

- [semi naive evaluation](../algorithms/semi-naive-evaluation.md)
- [precomputed rewriting](../algorithms/precomputed-rewriting.md)
- [rdfox](../systems/rdfox.md)
- [others](../systems/others.md)

## Key references

- [report 07 §6](../../preliminary-analysis/07-sota-theory.md)
- [report 06 §5 idea 7](../../preliminary-analysis/06-sota-engines.md)
- [README Key theory points](../../preliminary-analysis/README.md)
- Motik, Nenov, Piro, Horrocks. *Maintenance of Datalog materialisations revisited*. AIJ 2019 [U].
- Gupta, Mumick, Subrahmanian. *Maintaining views incrementally*. SIGMOD 1993 [U].
