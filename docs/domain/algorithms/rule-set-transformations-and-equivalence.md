# Rule-set transformations and equivalence

Rule sets can be rewritten into equivalent, cheaper ones: removing redundant rules or atoms, simplifying premises using other rules, splitting or merging rules, normalising heads, breaking recursive components. Each transformation preserves some notion of equivalence (logical equivalence, conservative extension, query equivalence), and keeping a link from transformed to original rules preserves explanations.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Catalogue of transformations and the equivalence each preserves.
- [ ] Premise simplification using other rules (e.g. `{a → b, a ∧ b → c} ≡ {a → b, a → c}`) and termination of simplification (decreasing measures).
- [ ] Redundancy elimination (rule subsumption, core of rule bodies).
- [ ] Normalisation (single-head, atomic heads) and its effect on termination.
- [ ] Transformations that shrink GRD components.
- [ ] Proof obligations: statement, proof in both directions, counter-example for a close variant; mechanised proofs.
- [ ] Traceability: origin links; explanations over original rules.
- [ ] Equality saturation over rule sets (research).

## Related pages

- [equivalence notions](../concepts/equivalence-notions.md): the notions preserved.
- [graph of rule dependencies](graph-of-rule-dependencies-grd.md): SCC effects.
- [provenance](../concepts/provenance.md): origin links.
- [chase termination](../concepts/chase-termination.md): normalisation changes termination.

## Key references

- Y. Sagiv. *Optimizing Datalog programs*. In *Foundations of Deductive Databases and Logic Programming*, 1988. [U]
- D. Carral, L. Larroque, M.-L. Mugnier, M. Thomazo. *Normalisations of existential rules: not so innocuous!* KR 2022.
- V. Lifschitz, D. Pearce, A. Valverde. *Strongly equivalent logic programs*. ACM TOCL 2(4), 2001.
- M. Willsey et al. *egg: fast and extensible equality saturation*. POPL 2021.
