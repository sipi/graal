# Rule-set analysis tools

Static analysis of a rule set before reasoning: computing dependency graphs and strata, recognising decidable classes, certifying chase termination or providing non-termination evidence, detecting ill-formed programs, and selecting a reasoning strategy per component. Since membership in the abstract classes is undecidable, tools run portfolios of sufficient tests of increasing cost.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Pipeline: well-formedness (safety, typing), dependency graphs, stratification, per-SCC classification, strategy selection.
- [ ] Portfolios: costs and order of tests (aGRD, WA, JA, SWA, MSA, MFA, restricted variants, guardedness, stickiness, wardedness, AR, finitely ground).
- [ ] Outputs: class labels, termination certificates, non-termination witnesses, diagnostics.
- [ ] Tools: Kiabora, analysers in rule engines (Graal, VLog/Nemo reliance analysis), MFA/MSA checkers, ASP termination checkers.
- [ ] Algorithm selection procedures from the literature.
- [ ] Example: managers are employees (fails WA and MFA, with the witness).

## Related pages

- [decidability classes](../concepts/decidability-classes.md): what is recognised.
- [chase termination](../concepts/chase-termination.md): certificates.
- [graph of rule dependencies](graph-of-rule-dependencies-grd.md): dependency analysis.
- [explanations and diagnostics](../concepts/explanations-and-diagnostics.md): reporting.
- [hybrid strategies](hybrid-strategies.md): strategy selection.

## Key references

- M. Leclère, M.-L. Mugnier, S. Rocher. *Kiabora: an analyzer of existential rule bases*. RR 2013.
- J.-F. Baget, M. Leclère, M.-L. Mugnier, E. Salvat. *On rules with existential variables: Walking the decidability line*. Artificial Intelligence 175(9-10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- B. Cuenca Grau, I. Horrocks, M. Krötzsch, C. Kupke, D. Magka, B. Motik, Z. Wang. *Acyclicity notions for existential rules and their application to query answering in ontologies*. JAIR 47, 2013. https://doi.org/10.1613/jair.3964
- L. González, A. Ivliev, M. Krötzsch, S. Mennicke. *Efficient dependency analysis for rule-based ontologies*. ISWC 2022. https://arxiv.org/abs/2207.09669
- D. Carral, I. Dragoste, M. Krötzsch. *Restricted chase (non)termination for existential rules with disjunctions*. IJCAI 2017. https://doi.org/10.24963/ijcai.2017/128
- M. Calautti, S. Greco, F. Spezzano, I. Trubitsyna. *Checking termination of bottom-up evaluation of logic programs with function symbols*. TPLP 15(6), 2015.
