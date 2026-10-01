# Graal

Graal is the Java existential-rules platform (Inria/LIRMM GraphIK, upstream abandoned since 2019, CeCILL 2.1) whose code base is in this repository and whose original author is the project owner. The project builds a new core guided by Graal and uses Graal as a test oracle on the fragment where it is valid.

> **Status in this project:** `background` — option B (new core, Graal as oracle) chosen; not refurbished (option A rejected); oracle boundaries in report 09 §1.4 and report 11 §9.2; licence: [licensing](../legal/licensing-and-provenance.md).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] Module map, key abstractions, algorithms (homomorphism with BCC, chase drivers, PURE, GRD, Kiabora analyser).
- [ ] Known defects: null representation per store, Skolem naming collisions, equals/hashCode, global state, timeouts (report 02).
- [ ] Build status on JDK 21 and how to run it as an oracle (report 03: `--add-opens`, 492 tests).
- [ ] Oracle fragments: EXACT / LOWER-BOUND / NONE (report 11 §9.2).
- [ ] What to keep as design inspiration vs what to avoid.

## Related pages

- [integraal](../systems/integraal.md)
- [benchmarks and test oracles](../engineering/benchmarks-and-test-oracles.md)
- [function graph translation tp](../concepts/function-graph-translation-tp.md)
- [licensing](../legal/licensing-and-provenance.md)

## Key references

- [report 02](../../preliminary-analysis/02-graal-architecture-audit.md)
- [report 03](../../preliminary-analysis/03-graal-build-and-dependencies.md)
- [report 01 §1](../../preliminary-analysis/01-ecosystem-and-alternatives.md)
- [report 09 §1.4](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 11 §9.2](../../preliminary-analysis/11-f2-framework-definition.md)
