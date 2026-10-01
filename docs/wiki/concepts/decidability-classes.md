# Decidability classes

The map of decidable fragments of existential rules and logic programs with functions: abstract classes (FES, FUS, BTS, GBTS), recognisable classes (weakly acyclic, jointly acyclic, MFA, MSA, guarded, sticky, warded, linear, shy) and logic-programming classes (finitely ground, argument-restricted, Γ-acyclic, FDNC, finitary). The analyser runs a portfolio of sufficient tests drawn from this map.

> **Status in this project:** `background` `v0` (Datalog) `F2` (portfolio) `out-of-scope` (GBTS algorithms, [E10](../project/requirements.md#e10)) — [E2](../project/requirements.md#e2)/[E7](../project/requirements.md#e7); report 11 §7.2 portfolio (DRAFT); FDNC/finitary relate to [Q1](../project/open-questions.md#q1) lead B and [OP-15](../project/open-questions.md#op-15).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Abstract classes FES, FUS, BTS, GBTS: definitions, inclusions, undecidability of membership.
- [ ] Concrete classes with complexity table (report 07 §1.3): Datalog, linear, guarded, frontier-guarded, weakly (frontier-)guarded, sticky, weakly sticky, warded, shy, aGRD.
- [ ] Acyclicity notions: WA, SWA, JA, MSA, MFA, RJA/RMFA, with inclusions.
- [ ] LP-with-functions classes (report 09 §2): FG, AR, Γ-acyclic, bounded, FDNC, finitary, finitely recursive.
- [ ] Which classes transfer to named functions (report 09 §3 table).
- [ ] Combination along GRD strata (Kiabora): FES below FUS, etc.
- [ ] What the F2 portfolio keeps (report 11 §7.2) and what it leaves out; GBTS out of scope (E10) and the Q1 lead B nuance.
- [ ] Pitfalls: membership in the abstract classes is undecidable; normalisation changes classes.

## Related pages

- [chase termination](../concepts/chase-termination.md)
- [decidability analyser](../algorithms/decidability-analyser.md)
- [graph of rule dependencies grd](../algorithms/graph-of-rule-dependencies-grd.md)
- [existential rules](../concepts/existential-rules.md)
- [blocking of recursive chains](../algorithms/blocking-of-recursive-chains.md)

## Key references

- [report 07 §1](../../preliminary-analysis/07-sota-theory.md)
- [report 09 §2-§3](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 11 §7](../../preliminary-analysis/11-f2-framework-definition.md)
- Baget, Leclère, Mugnier, Salvat. AIJ 2011.
- Cuenca Grau et al. *Acyclicity notions for existential rules*. JAIR 47, 2013.
- Eiter, Šimkus. *FDNC: decidable nonmonotonic disjunctive logic programs with function symbols*. ACM TOCL 2010 [V in report 09].
