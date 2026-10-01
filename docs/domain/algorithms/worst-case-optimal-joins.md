# Worst-case-optimal joins

Worst-case-optimal join algorithms (generic join, leapfrog triejoin) evaluate multiway joins variable by variable over sorted or trie-indexed relations, with running time bounded by the AGM bound on the output size. They outperform pairwise join plans on cyclic queries and are used by several modern rule engines.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] AGM bound; fractional edge covers.
- [ ] Generic join, NPRR, leapfrog triejoin; variable ordering.
- [ ] Data structures: sorted columns, tries, hash tries.
- [ ] When WCOJ beats binary joins and when not (acyclic queries, small inputs).
- [ ] Combination with semi-naive deltas; index maintenance under insertion.
- [ ] Free join and hybrid plans [U]; relation with hypertree decompositions.

## Related pages

- [homomorphism search](homomorphism-search.md): backtracking view of the same problem.
- [semi-naive evaluation](semi-naive-evaluation.md): use in fixpoint computation.
- [Nemo](../systems/nemo.md): a WCOJ-based rule engine.

## Key references

- A. Atserias, M. Grohe, D. Marx. *Size bounds and query plans for relational joins*. FOCS 2008; SIAM J. Comput. 42(4), 2013.
- H. Q. Ngo, E. Porat, C. Ré, A. Rudra. *Worst-case optimal join algorithms*. PODS 2012; JACM 65(3), 2018.
- T. L. Veldhuizen. *Triejoin: a simple, worst-case optimal join algorithm*. ICDT 2014.
- Y. R. Wang, M. Willsey, D. Suciu. *Free join: unifying worst-case optimal and traditional joins*. SIGMOD 2023. [U]
