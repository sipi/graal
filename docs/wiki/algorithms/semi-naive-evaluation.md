# Semi-naive evaluation

Semi-naive evaluation computes the least fixpoint of a recursive rule set by joining only the facts that are new since the previous round with the rest, avoiding re-derivations. It is the core algorithm of the v0 engine and of F2 materialisation, and is fair (breadth-first), which the status machinery requires.

> **Status in this project:** `v0` `F2` — [D6](../project/decisions.md#d6); report 11 §6.4 (fairness, deterministic budgets), §8.1.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Naive vs semi-naive; delta rules; rule rewriting for multiple recursive atoms.
- [ ] Per-SCC and per-stratum evaluation; termination detection.
- [ ] Fairness and determinism (report 11 §6.4); budgets (rounds, facts, depth).
- [ ] Join strategies (link homomorphism search, WCOJ) and index maintenance.
- [ ] Duplicate elimination, set semantics, dictionary encoding.
- [ ] Parallel semi-naive (RDFox, ascent) [U].
- [ ] Function terms in F2: hash-consing, depth budget.
- [ ] v0-ex worked trace.

## Related pages

- [datalog](../concepts/datalog.md)
- [worst case optimal joins](../algorithms/worst-case-optimal-joins.md)
- [homomorphism search](../algorithms/homomorphism-search.md)
- [scc driven chase](../algorithms/scc-driven-chase.md)
- [incremental maintenance](../algorithms/incremental-maintenance.md)

## Key references

- [report 11 §6.4, §8.1](../../preliminary-analysis/11-f2-framework-definition.md)
- [report 06 §5 ideas 2, 13](../../preliminary-analysis/06-sota-engines.md)
- [report 05 §1.3](../../preliminary-analysis/05-nemo-and-rust-option.md)
- Bancilhon. *Naive evaluation of recursively defined relations*. 1986 [U].
- Abiteboul, Hull, Vianu. *Foundations of Databases*, ch. 13 [U].
