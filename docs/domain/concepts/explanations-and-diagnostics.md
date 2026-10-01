# Explanations and diagnostics

Explanations justify an answer (why does this fact hold?) or a non-answer (why not?); diagnostics report problems of the rule set itself: non-stratifiable cycles, non-termination witnesses, constraint violations, unintended infinite structures, ill-typed built-ins. For users who write rules, including automated ones, good explanations and diagnostics are as important as answers.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Explaining answers: proof trees, minimal explanations, justifications (minimal sub-knowledge-bases) in DL reasoners.
- [ ] Explaining non-answers (why-not provenance), and explaining absent results due to negation.
- [ ] Explaining invented individuals: which rule and which data created them.
- [ ] Static diagnostics: unsafe rules, non-stratifiable negation or aggregation (with the cycle), non-termination evidence (cyclic Skolem terms, special-edge cycles), invention that defeats its own condition.
- [ ] Dynamic diagnostics: budgets reached, constraint violations, inconsistency.
- [ ] Detecting likely modelling errors (e.g. an infinite chain of invented individuals where the modeller expected the chain to stop) and phrasing them as questions.
- [ ] Explanations over transformed rule sets: mapping back to source rules.
- [ ] Output formats: human-readable and machine-readable.

## Related pages

- [provenance](provenance.md): the bookkeeping behind explanations.
- [rule-set analysis tools](../algorithms/rule-set-analysis-tools.md): static analysis producing diagnostics.
- [stratified negation](stratified-negation.md): stratification errors.
- [chase termination](chase-termination.md): non-termination witnesses.
- [soundness and completeness of partial results](soundness-and-completeness-of-partial-results.md): reporting guarantees.

## Key references

- T. J. Green, G. Karvounarakis, V. Tannen. *Provenance semirings*. PODS 2007.
- A. Kalyanpur, B. Parsia, M. Horridge, E. Sirin. *Finding all justifications of OWL DL entailments*. ISWC 2007.
- J. Huang, T. Chen, A. Doan, J. F. Naughton. *On the provenance of non-answers to queries over extracted data*. PVLDB 1(1), 2008.
- Y. Kazakov, P. Klinov. *Goal-directed tracing of inferences in EL ontologies*. ISWC 2014. [U]
- B. Cuenca Grau, I. Horrocks, M. Krötzsch, C. Kupke, D. Magka, B. Motik, Z. Wang. *Acyclicity notions for existential rules and their application to query answering in ontologies*. JAIR 47, 2013. https://doi.org/10.1613/jair.3964
