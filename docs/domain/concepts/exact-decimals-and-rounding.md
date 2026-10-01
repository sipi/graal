# Exact decimals and rounding

Rules over money, quantities and thresholds need exact arithmetic: binary floating point cannot represent most decimal fractions, so comparisons such as `200.00 > 200.00` or sums of prices can give wrong results. This page covers numeric datatypes in rule languages, exact decimal arithmetic, division, and the catalogue of rounding modes with their behaviour on ties and negative numbers.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Value spaces: integers, exact decimals (`m·10^-s`), rationals, binary floats; lexical vs value space (XSD); `2 = 2.00` value identity.
- [ ] Exact `+`, `−`, `×`; division as a partial operation or with an explicit scale and rounding mode; overflow and precision limits.
- [ ] Rounding modes catalogue with exact definitions: floor (towards −∞), ceiling (towards +∞), truncate (towards 0), half-up (two conventions: towards +∞ or away from zero), half-down, half-even (banker's rounding), half away from zero; IEEE 754 and Java/Python names.
- [ ] Behaviour on negative numbers and ties; worked table (basket threshold example).
- [ ] Comparisons across datatypes; ill-typed built-ins.
- [ ] Support in systems: integer-only aggregates in ASP (scaling), decimals in RDF stores, floats in Datalog engines.
- [ ] Pitfalls: string comparison of literals, implicit float conversion, rounding inside aggregates vs after.

## Related pages

- [aggregation](aggregation.md): sums and thresholds.
- [examples](../examples.md): basket threshold.
- [RDF and SPARQL](../adjacent/rdf-and-sparql.md): XSD datatypes.

## Key references

- IEEE Std 754-2019, *IEEE Standard for Floating-Point Arithmetic* (decimal formats and rounding-direction attributes).
- W3C. *XML Schema Definition Language (XSD) 1.1 Part 2: Datatypes*. 2012 (`xsd:decimal`, value vs lexical space).
- M. F. Cowlishaw. *Decimal floating-point: algorism for computers*. ARITH 2003.
- Java SE documentation, `java.math.RoundingMode`; Python documentation, `decimal` module (rounding constants). [U]
