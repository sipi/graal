# InteGraal

InteGraal is Graal's successor from the same team (BOREAL), Apache-2.0, positioned for data integration. It was evaluated and rejected as the core (option E): it reuses Graal's analyser verbatim, lacks bi-connected-component homomorphism in its main path, has partial stratified negation, no group-by aggregation, and nulls still do not round-trip across stores.

> **Status in this project:** `background` — option E rejected; possible source of ideas (Apache-2.0).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] Feature matrix vs Graal (report 04 §1).
- [ ] Data model and null handling (report 04 §2).
- [ ] What it adds (data integration, redundancy/forgetting modules, B-Runner benchmarking).
- [ ] Licence implications (Apache-2.0: reuse possible with attribution).

## Related pages

- [graal](../systems/graal.md)
- [labelled nulls](../concepts/labelled-nulls.md)
- [benchmarks and test oracles](../engineering/benchmarks-and-test-oracles.md)

## Key references

- [report 04](../../preliminary-analysis/04-graal-vs-integraal.md)
- [report 01 §2](../../preliminary-analysis/01-ecosystem-and-alternatives.md)
- [report 06 §1.7](../../preliminary-analysis/06-sota-engines.md)
