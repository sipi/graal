# Worst-case-optimal joins

Worst-case-optimal join algorithms (generic join, leapfrog triejoin) evaluate multiway joins variable by variable over sorted or trie-indexed relations, with running time bounded by the AGM bound. They are the backbone of Nemo-style large-scale materialisation and a candidate for large KBs.

> **Status in this project:** `v0` `later` `open` — performance for large KBs (aerospace/defense); choice of join engine is part of phase 3 and the spike ([E6](../project/requirements.md#e6)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] AGM bound, generic join, leapfrog triejoin; variable ordering.
- [ ] Columnar sorted tables and tries (Nemo), datafrog leapjoin.
- [ ] When WCOJ beats binary joins and when not (small queries, many small KBs).
- [ ] Combination with semi-naive deltas; index maintenance under insertion.
- [ ] Free join and hybrid plans [U].
- [ ] Relation to hypertree/GHD plans and bi-connected components.

## Related pages

- [homomorphism search](../algorithms/homomorphism-search.md)
- [semi naive evaluation](../algorithms/semi-naive-evaluation.md)
- [nemo](../systems/nemo.md)
- [souffle](../systems/souffle.md)
- [architecture principles](../engineering/architecture-principles.md)

## Key references

- [report 06 §5 idea 2](../../preliminary-analysis/06-sota-engines.md)
- [report 05 §1, §3](../../preliminary-analysis/05-nemo-and-rust-option.md)
- [report 08 §1.1](../../preliminary-analysis/08-kotlin-vs-rust.md)
- Veldhuizen. *Leapfrog triejoin*. ICDT 2014 [U].
- Ngo, Porat, Ré, Rudra. *Worst-case optimal join algorithms*. PODS 2012 / JACM 2018 [U].
