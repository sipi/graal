# Hybrid strategies

Hybrid strategies combine materialisation and rewriting: materialise the part of the rule set where the chase terminates (or the strata below a negation), and rewrite the query with the rest. The combined approach materialises a compact, possibly unsound model and filters answers with a rewritten query. Algorithm selection policies choose among strategies per query or per component.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Decomposition along GRD strata: FES below FUS, BTS below FUS.
- [ ] Stratum-by-stratum rewriting with negated or aggregated predicates evaluated on materialised lower strata.
- [ ] Combined approach (DL-Lite, EL): unsound intermediate model plus filtering; soundness stated on returned answers.
- [ ] Selection policies and cost models; cross-checking strategies against each other.
- [ ] Example: default conditions with rewriting above the negated stratum.

## Related pages

- [query rewriting](query-rewriting.md): the rewriting part.
- [SCC-driven chase](scc-driven-chase.md): the materialisation part.
- [decidability classes](../concepts/decidability-classes.md): class combination.
- [rule-set analysis tools](rule-set-analysis-tools.md): selection.

## Key references

- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- M. Leclère, M.-L. Mugnier, S. Rocher. *Kiabora: an analyzer of existential rule bases*. RR 2013.
- C. Lutz, D. Toman, F. Wolter. *Conjunctive query answering in the description logic EL using a relational database system*. IJCAI 2009.
- R. Kontchakov, C. Lutz, D. Toman, F. Wolter, M. Zakharyaschev. *The combined approach to query answering in DL-Lite*. KR 2010.
- C. Lutz, İ. Seylan, D. Toman, F. Wolter. *The combined approach to OBDA: taming role hierarchies using filters*. ISWC 2013.
