# Skolemisation and function-graph translations

Two translations connect existential rules and rules with function terms. Skolemisation replaces each existential variable by a function term; it preserves certain answers over the original signature. The function-graph translation goes the other way: each function `f` becomes a predicate `F_f(x̄, y)` holding its graph, with existential rules creating values. Together they let results and tools from one world be used in the other.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Skolemisation of existential rules: rule-local functions, frontier vs full Skolemisation; conservative extension; relation with the semi-oblivious and oblivious chases.
- [ ] Second-order dependencies (SO tgds) as the logical home of shared Skolem functions.
- [ ] Function-graph translation: graph predicate per function, flattening of nested terms, existence and use rules; the implicit functionality (key) of `F_f` and when it is harmless.
- [ ] Correctness: same certain answers over constants under stated conditions; what breaks with negation, aggregation, equality.
- [ ] Transfer of decidability classes and termination criteria through the translations.
- [ ] Using the translations to compare systems (existential-rule engines vs logic-programming engines) and as test oracles.
- [ ] Example: every employee has a manager in both forms.

## Related pages

- [Skolem functions and terms](skolem-functions-and-terms.md): the function terms.
- [existential rules](existential-rules.md): the existential side.
- [decidability classes](decidability-classes.md): class transfer.
- [equivalence notions](equivalence-notions.md): conservative extension.
- [benchmarks and test oracles](../evaluation/benchmarks-and-test-oracles.md): cross-system testing.

## Key references

- R. Fagin, P. G. Kolaitis, L. Popa, W.-C. Tan. *Composing schema mappings: second-order dependencies to the rescue*. ACM TODS 30(4), 2005.
- B. Marnette. *Generalized schema-mappings: from termination to tractability*. PODS 2009.
- M. Arenas, J. Pérez, J. Reutter, C. Riveros. *The language of plain SO-tgds: composition, inversion and structural properties*. JCSS 79(6), 2013.
- A. Calì, G. Gottlob, A. Pieris. *Towards more expressive ontology languages: the query answering problem*. Artificial Intelligence 193, 2012.
- B. Cuenca Grau, I. Horrocks, M. Krötzsch, C. Kupke, D. Magka, B. Motik, Z. Wang. *Acyclicity notions for existential rules and their application to query answering in ontologies*. JAIR 47, 2013. https://doi.org/10.1613/jair.3964
- M. Marx, M. Krötzsch. *TGDs capture complex values*. ICDT 2022.
