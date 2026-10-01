# Chase variants

The chase is forward chaining with value invention. Its variants (oblivious, semi-oblivious / Skolem, restricted, Datalog-first restricted, parallel, core, equivalent, parsimonious, Vadalog termination control) differ in when a rule application is considered redundant, which changes result size, termination and order dependence. E3 requires the variant to be explicit.

> **Status in this project:** `background` `F2` (Skolem-style evaluation of named functions) `later` (F3 chase on nulls) — [E3](../project/requirements.md#e3); README key theory point "Skolem chase as materialisation backend"; [D8](../project/decisions.md#d8) order independence.
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definitions of each variant with triggers, active triggers, rounds; equivalence semi-oblivious = Skolem chase.
- [ ] Termination differences and the hierarchy of terminating classes; order dependence of the restricted chase (report 12 §3).
- [ ] Core chase and FES; equivalent chase (Rocher) [U].
- [ ] Which variant F2 materialisation corresponds to (semi-naive evaluation of lb(K) with hash-consed terms; Datalog-first correspondence for lookup).
- [ ] Graal's variants and their bugs (report 02 §3: Skolem naming collisions).
- [ ] Nemo/VLog Datalog-first restricted chase; Vadalog isomorphism pruning.
- [ ] E3-ex trap under each variant.

## Related pages

- [chase termination](../concepts/chase-termination.md)
- [existential rules](../concepts/existential-rules.md)
- [scc driven chase](../algorithms/scc-driven-chase.md)
- [semi naive evaluation](../algorithms/semi-naive-evaluation.md)
- [nemo](../systems/nemo.md)
- [vadalog](../systems/vadalog.md)

## Key references

- [report 07 §1.4](../../preliminary-analysis/07-sota-theory.md)
- [report 12 §3](../../preliminary-analysis/12-invention-under-negation.md)
- [report 02 §3](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 05 §1.4](../../preliminary-analysis/05-nemo-and-rust-option.md)
- Onet. *The chase procedure and its applications in data exchange*. 2013.
- Benedikt et al. *Benchmarking the chase*. PODS 2017.
