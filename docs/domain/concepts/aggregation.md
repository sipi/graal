# Aggregation

Aggregates (`#count`, `#sum`, `#min`, `#max`) compute a value from a collection of tuples. In stratified programs they read a fully computed lower stratum under a closed-world reading; recursive aggregation needs monotonicity or a dedicated semantics. Grouping, empty groups, duplicate handling and the datatype of the result are frequent sources of divergence between systems.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Syntax: aggregate atoms with tuple keys (set of tuples, ASP-Core-2), local vs group-by variables; SQL-style grouping as a contrast.
- [ ] Semantics in stratified programs; empty collections and empty groups (ASP grounded groups vs SQL GROUP BY).
- [ ] Set vs bag semantics and the role of the tuple key.
- [ ] Recursive aggregation: monotonic aggregation (Ross-Sagiv), limit Datalog, FLP and other ASP semantics, lattice-based Datalog.
- [ ] Aggregates over invented individuals (nulls, function terms): what counting means when identity is uncertain.
- [ ] Aggregates on incomplete computations: partial sums are wrong, not partial.
- [ ] Example: basket threshold.

## Related pages

- [stratified negation](stratified-negation.md): stratification with strict edges.
- [exact decimals and rounding](exact-decimals-and-rounding.md): datatypes of results.
- [logic programming and ASP](logic-programming-and-asp.md): ASP aggregates.
- [soundness and completeness of partial results](soundness-and-completeness-of-partial-results.md): aggregates on partial results.

## Key references

- K. A. Ross, Y. Sagiv. *Monotonic aggregation in deductive databases*. JCSS 54(1), 1997.
- M. Kaminski, B. Cuenca Grau, E. V. Kostylev, B. Motik, I. Horrocks. *Foundations of declarative data analysis using limit Datalog programs*. IJCAI 2017.
- W. Faber, G. Pfeifer, N. Leone. *Semantics and complexity of recursive aggregates in answer set programming*. Artificial Intelligence 175(1), 2011.
- F. Calimeri et al. *ASP-Core-2 input language format*. TPLP 20(2), 2020.
- I. S. Mumick, H. Pirahesh, R. Ramakrishnan. *The magic of duplicates and aggregates*. VLDB 1990. [U]
