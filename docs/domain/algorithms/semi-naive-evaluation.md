# Semi-naive evaluation

Semi-naive evaluation computes the least fixpoint of a recursive rule set by joining, in each round, only the facts that are new since the previous round with the rest, avoiding most re-derivations. It is the standard bottom-up algorithm of Datalog engines and the basis of chase implementations. It is fair (breadth-first), which matters for completeness arguments.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Naive vs semi-naive evaluation; delta rules; rules with several recursive atoms.
- [ ] Per-SCC and per-stratum evaluation; termination detection.
- [ ] Fairness and determinism; round-based budgets.
- [ ] Join strategies and index maintenance (link homomorphism search, WCOJ).
- [ ] Duplicate elimination, set semantics, dictionary encoding of constants.
- [ ] Parallel and distributed semi-naive evaluation.
- [ ] Function terms and value invention inside semi-naive loops.
- [ ] Example: chain of command trace.

## Related pages

- [Datalog](../concepts/datalog.md): the language.
- [SCC-driven chase](scc-driven-chase.md): component scheduling.
- [worst-case-optimal joins](worst-case-optimal-joins.md): join algorithms.
- [incremental maintenance](incremental-maintenance.md): updates.

## Key references

- F. Bancilhon. *Naive evaluation of recursively defined relations*. In *On Knowledge Base Management Systems*, Springer, 1986. [U]
- S. Abiteboul, R. Hull, V. Vianu. *Foundations of Databases*, chapters 4-6 (conjunctive queries), 12-15 (Datalog, negation). Addison-Wesley, 1995.
- F. Bancilhon, R. Ramakrishnan. *An amateur's introduction to recursive query processing strategies*. SIGMOD 1986.
- B. Motik, Y. Nenov, R. Piro, I. Horrocks, D. Olteanu. *Parallel materialisation of Datalog programs in centralised, main-memory RDF systems*. AAAI 2014.
