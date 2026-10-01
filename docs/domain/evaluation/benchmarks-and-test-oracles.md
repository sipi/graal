# Benchmarks and test oracles

How reasoners are evaluated: benchmark suites for correctness and performance (ChaseBench, LUBM and UOBM, real-world ontologies, iWarded, termination test suites, program-analysis Datalog), benchmarking infrastructure, and the use of independent reference systems as test oracles on the fragments where their semantics coincide. Differential testing between systems requires care with chase variants, null naming and datatypes.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (free sections, keeping summary, pitfalls, disputes, related pages and references), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Benchmark catalogue: ChaseBench (STB, ONT, Doctors, LUBM, Deep), iBench, LUBM/UOBM, real ontologies translated to rules, iWarded, termination test suites (MFA/RMFA), DOOP/DaCapo for Datalog.
- [ ] Workload shapes: few large knowledge bases vs many small ones; latency vs throughput.
- [ ] Benchmarking infrastructure and methodology (warm-up, timeouts, reporting).
- [ ] Test oracles: which system can serve as reference for which fragment and semantics (Datalog engines, ASP systems on stratified programs, chase engines up to homomorphic equivalence, translations between existential and functional forms).
- [ ] Comparing results: equality up to null renaming, homomorphic equivalence, datatype normalisation, determinism of budgets.
- [ ] Conformance test design: expected outputs derived from a specification, not from an implementation; property-based and differential tests.

## Related pages

- [chase variants](../algorithms/chase-variants.md): what results to compare.
- [Skolemisation and function-graph translations](../concepts/skolemisation-and-function-graph-translations.md): cross-form testing.
- [clingo and DLV](../systems/clingo-and-dlv.md): reference for stratified semantics.
- [soundness and completeness of partial results](../concepts/soundness-and-completeness-of-partial-results.md): budgeted results.

## Key references

- M. Benedikt, G. Konstantinidis, G. Mecca, B. Motik, P. Papotti, D. Santoro, E. Tsamoura. *Benchmarking the chase*. PODS 2017.
- ChaseBench site. https://dbunibas.github.io/chasebench/
- P. C. Arocena, B. Glavic, R. Ciucanu, R. J. Miller. *The iBench integration metadata generator*. PVLDB 9(3), 2015.
- Y. Guo, Z. Pan, J. Heflin. *LUBM: a benchmark for OWL knowledge base systems*. Journal of Web Semantics 3(2-3), 2005.
- L. Ma, Y. Yang, Z. Qiu, G. Xie, Y. Pan, S. Liu. *Towards a complete OWL ontology benchmark*. ESWC 2006.
- T. Baldazzi et al. *iWarded*. RuleML+RR 2021.
- B. Cuenca Grau, I. Horrocks, M. Krötzsch, C. Kupke, D. Magka, B. Motik, Z. Wang. *Acyclicity notions for existential rules and their application to query answering in ontologies*. JAIR 47, 2013. https://doi.org/10.1613/jair.3964
- D. Carral, I. Dragoste, M. Krötzsch. *Restricted chase (non)termination for existential rules with disjunctions*. IJCAI 2017. https://doi.org/10.24963/ijcai.2017/128
