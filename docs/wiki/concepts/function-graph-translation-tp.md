# Function-graph translation T(P)

T(P) compiles a program with named functions into existential rules by introducing, for each function f, a predicate F_f holding its graph. It is the accepted bridge between Skolem and existential readings (D3): it imports existential-rule decidability results and makes Graal an exact test oracle on a fragment.

> **Status in this project:** `F2` — accepted bridge ([D3](../project/decisions.md#d3)); report 09 §1.2 (Prop. 1 [U-own]), report 11 §9 (DRAFT).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Definition: existence rule and use rule per head term; body terms flattened; implicit key κ_f; bridge rule for lookup-declared functions.
- [ ] Proposition 1 (report 09): same certain answers over constants; harmless key; conditions under which it breaks.
- [ ] Proposition 5 (report 11): Ans in mode `constants` = certain answers of T(K) ∪ D under stated conditions.
- [ ] Transfer of decidability classes through T(P) (report 09 §3).
- [ ] Use for Graal oracle: EXACT / LOWER-BOUND / NONE fragments (report 11 §9.2).
- [ ] Open research: proof in full generality, Lean mechanisation (report 09 §7 item 1).

## Related pages

- [skolem functions and terms](../concepts/skolem-functions-and-terms.md)
- [existential rules](../concepts/existential-rules.md)
- [lookup before invent](../concepts/lookup-before-invent.md)
- [benchmarks and test oracles](../engineering/benchmarks-and-test-oracles.md)
- [graal](../systems/graal.md)

## Key references

- [report 09 §1.2, §1.4, §3, §7](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 11 §9](../../preliminary-analysis/11-f2-framework-definition.md)
- [README D3](../../preliminary-analysis/README.md)
