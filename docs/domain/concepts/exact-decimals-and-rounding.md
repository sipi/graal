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

## Cross-language comparison of rounding modes

Languages disagree on what "round" means for ties (values exactly halfway between two candidates) and, above all, for **negative** ties. Four tie rules are in use:

- **half away from zero** (`-2.5 → -3`, `2.5 → 3`): the "school" or commercial rule;
- **half towards +∞** (`-2.5 → -2`, `2.5 → 3`), also called half-up in the strict sense of "towards the larger value";
- **half to even** (`-2.5 → -2`, `2.5 → 2`, `3.5 → 4`), also called banker's rounding or round-half-even, the IEEE 754 default;
- the directed modes floor (towards −∞), ceiling (towards +∞) and truncation (towards 0), which have no ties.

The name "half-up" is ambiguous: it means away from zero in PHP, C `round` and Java `BigDecimal.RoundingMode.HALF_UP`, but towards +∞ in the specification of Java `Math.round` and JavaScript `Math.round`.

### Results at scale 0

| Input | 2.5 | 3.5 | -2.5 | -3.5 | 2.4 | -2.6 |
|---|---|---|---|---|---|---|
| floor (towards −∞) | 2 | 3 | -3 | -4 | 2 | -3 |
| ceiling (towards +∞) | 3 | 4 | -2 | -3 | 3 | -2 |
| truncate (towards 0) | 2 | 3 | -2 | -3 | 2 | -2 |
| half away from zero | 3 | 4 | -3 | -4 | 2 | -3 |
| half towards +∞ | 3 | 4 | -2 | -3 | 2 | -3 |
| half to even | 2 | 4 | -2 | -4 | 2 | -3 |

### Function in each language and its tie behaviour

Status: verified = confirmed against the official documentation or a page quoting it during a check on 2026-10-01; [U] = not verified.

| Language | Function | Tie behaviour | Status |
|---|---|---|---|
| C | `round`, `lround`, `llround` | half away from zero, whatever the current rounding mode | verified (cppreference, C standard 7.12.9) |
| C | `rint`, `nearbyint` | current rounding mode, half to even by default | [U] |
| C | `floor`, `ceil`, `trunc` | directed | [U] |
| Java | `Math.round(double)` | half towards +∞ (`floor(a + 1/2)`): `Math.round(-2.5) = -2` | verified (Javadoc, as quoted) |
| Java | `Math.rint` | half to even | [U] |
| Java | `BigDecimal` with `RoundingMode.HALF_UP` | half away from zero (`UP` means away from zero) | [U] (Javadoc not re-fetched) |
| Java | `RoundingMode.HALF_EVEN`, `FLOOR`, `CEILING`, `DOWN` | half to even; towards −∞; towards +∞; towards 0 | [U] |
| JavaScript | `Math.round` | half towards +∞: `Math.round(-2.5) = -2`, `Math.round(-20.5) = -20` | verified (ECMAScript, MDN) |
| JavaScript | `Math.floor`, `Math.ceil`, `Math.trunc` | directed | [U] |
| Python 3 | `round(x[, n])` | half to even: `round(2.5) = 2`, `round(3.5) = 4` | verified (docs for built-in `round`) |
| Python 3 | `decimal.ROUND_HALF_UP`, `ROUND_HALF_EVEN`, `ROUND_FLOOR`, `ROUND_CEILING`, `ROUND_DOWN` | half away from zero; half to even; towards −∞; towards +∞; towards 0 | [U] |
| PHP | `round($x, $p, PHP_ROUND_HALF_UP)` (default) | half away from zero: 1.5 → 2, -1.5 → -2 | verified (PHP manual) |
| PHP | `PHP_ROUND_HALF_EVEN`, `HALF_DOWN`, `HALF_ODD` | half to even; half towards zero; half to odd | [U] |
| Excel | `ROUND` | half away from zero: `ROUND(-2.5, 0) = -3` | verified (several tutorials; Microsoft page not fetched) |
| COBOL | `ROUNDED` (default mode `NEAREST-AWAY-FROM-ZERO`) | half away from zero | verified (GnuCOBOL mapping to HALF_UP; ISO 2014 text not fetched) |
| COBOL | `ROUNDED MODE IS NEAREST-EVEN` | half to even: 2.5 → 2 | verified (same sources) |
| COBOL | `TRUNCATION`, `TOWARD-GREATER`, `TOWARD-LESSER` | directed | [U] |
| SQL, PostgreSQL | `ROUND(numeric)` | half away from zero | verified (PostgreSQL manual, section 8.1) |
| SQL, PostgreSQL | `ROUND(double precision)` | half to even on most platforms, platform dependent | verified (PostgreSQL manual, section 8.1) |
| SQL, MySQL | `ROUND` on exact values (`DECIMAL`) | half away from zero | verified (MySQL manual, precision math) |
| SQL, MySQL | `ROUND` on approximate values (`DOUBLE`) | depends on the C library, typically half to even | verified (MySQL manual) |
| SQL, Oracle, SQL Server | `ROUND` | half away from zero | [U] |
| IEEE 754-2019 | `roundTiesToEven` (default), `roundTiesToAway`, `roundTowardZero`, `roundTowardPositive`, `roundTowardNegative` | as named | verified (standard, section 4.3) |

Notes. Several systems choose the tie rule by operand type (exact decimal versus binary floating point). Binary floating-point values such as 2.675 are rarely exact ties, so decimal and binary rounding can differ in practice even for the same rule.

### References

- ISO/IEC 9899, C standard, 7.12.9 (rounding and remainder functions); [cppreference, `round`](https://en.cppreference.com/w/c/numeric/math/round).
- Java SE API: [`java.lang.Math.round`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/Math.html#round(double)); [`java.math.RoundingMode`](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/math/RoundingMode.html).
- ECMAScript language specification, `Math.round`; [MDN, `Math.round`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/round).
- Python documentation: [built-in `round`](https://docs.python.org/3/library/functions.html#round); [`decimal` rounding modes](https://docs.python.org/3/library/decimal.html#rounding-modes).
- PHP manual: [`round`](https://www.php.net/manual/en/function.round.php).
- Microsoft, [ROUND function](https://support.microsoft.com/en-us/office/round-function-c018c5d8-40fb-4053-90b1-b3e7f61a213c).
- ISO/IEC 1989:2023 (COBOL), `ROUNDED` phrase; [GnuCOBOL documentation](https://gnucobol.sourceforge.io/).
- PostgreSQL manual, [numeric types](https://www.postgresql.org/docs/current/datatype-numeric.html); MySQL manual, [rounding behavior](https://dev.mysql.com/doc/refman/8.4/en/precision-math-rounding.html).
- IEEE Std 754-2019, section 4.3 (rounding-direction attributes).

## Related pages

- [aggregation](aggregation.md): sums and thresholds.
- [examples](../examples.md): basket threshold.
- [RDF and SPARQL](../adjacent/rdf-and-sparql.md): XSD datatypes.

## Key references

- IEEE Std 754-2019, *IEEE Standard for Floating-Point Arithmetic* (decimal formats and rounding-direction attributes).
- W3C. *XML Schema Definition Language (XSD) 1.1 Part 2: Datatypes*. 2012 (`xsd:decimal`, value vs lexical space).
- M. F. Cowlishaw. *Decimal floating-point: algorism for computers*. ARITH 2003.
- Java SE documentation, `java.math.RoundingMode`; Python documentation, `decimal` module (rounding constants). [U]
