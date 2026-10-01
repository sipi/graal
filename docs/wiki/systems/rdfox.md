# RDFox

RDFox (Oxford Semantic Technologies, now Samsung; C++, commercial) is a high-performance in-memory Datalog engine for RDF with parallel materialisation, incremental maintenance and equality handling. It has no existential rule heads: value invention uses the `SKOLEM` built-in, i.e. a hand-controlled Skolem chase.

> **Status in this project:** `background` — inspiration (incremental maintenance, equality, parallelism); not usable as dependency (commercial).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Use the system-page variant of the [page template](../conventions.md#2-page-template). Cover at least:

- [ ] SKOLEM built-in and its relation to named functions (report 09 §1.3).
- [ ] Parallel materialisation, FBF/DRed, equality by representative.
- [ ] Explanation features [U].

## Related pages

- [incremental maintenance](../algorithms/incremental-maintenance.md)
- [skolem functions and terms](../concepts/skolem-functions-and-terms.md)
- [equality and una](../concepts/equality-and-una.md)

## Key references

- [report 06 §0.1, §1.1](../../preliminary-analysis/06-sota-engines.md)
- [README Clarifications](../../preliminary-analysis/README.md)
- [report 09 §1.3](../../preliminary-analysis/09-skolem-function-frameworks.md)
