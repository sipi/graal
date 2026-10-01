# Provenance

Provenance records how a fact was derived: which input facts and which rules contributed. Formal models range from lineage and why-provenance to how-provenance in provenance semirings; practical systems store compact annotations and rebuild derivations on demand. Provenance underlies explanations, debugging, trust and incremental maintenance.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Lineage, why-provenance (witness sets), how-provenance (provenance polynomials, semiring `N[X]`), where-provenance.
- [ ] Provenance for Datalog and recursion: proof trees, infinite derivations, absorptive semirings.
- [ ] Compact annotations: (rule, height) per fact and on-demand reconstruction; top-k proofs.
- [ ] Provenance of invented values (which trigger created a null or term).
- [ ] Provenance through rule transformations: origin links from rewritten rules to source rules.
- [ ] Complexity of computing why-provenance for Datalog.
- [ ] Costs and trade-offs (memory, time) per design.

## Related pages

- [explanations and diagnostics](explanations-and-diagnostics.md): user-facing use of provenance.
- [incremental maintenance](../algorithms/incremental-maintenance.md): counting and derivation-based maintenance.
- [Soufflé](../systems/souffle.md): provenance annotations.
- [others (Scallop)](../systems/others.md): semiring-parameterised evaluation.

## Key references

- T. J. Green, G. Karvounarakis, V. Tannen. *Provenance semirings*. PODS 2007.
- P. Buneman, S. Khanna, W.-C. Tan. *Why and where: a characterization of data provenance*. ICDT 2001.
- D. Zhao, P. Subotić, B. Scholz. *Debugging large-scale Datalog: a scalable provenance evaluation strategy*. ACM TOPLAS 42(2), 2020. [U]
- M. Calautti, E. Livshits, A. Pieris, M. Schneider. *The complexity of why-provenance for Datalog queries*. 2023-2024. [U venue]
- Z. Li, J. Huang, M. Naik. *Scallop: a language for neurosymbolic programming*. PLDI 2023. [U]
