# Equivalence notions for rule sets

'Same meaning' for two rule sets has several precise readings: logical equivalence, conservative extension, query (answer) equivalence, and, for programs with negation, strong and uniform equivalence. Which notion an optimisation preserves determines which transformations are allowed. This page defines the notions and how they can be checked.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Logical equivalence; model-conservative and deductive-conservative extensions; query equivalence relative to input and output schemas.
- [ ] Rule entailment by freezing: `Σ ⊨ B → H` iff the frozen body plus `Σ` entails the frozen head; decidability inherited from decidable classes.
- [ ] Datalog: uniform equivalence decidable, equivalence w.r.t. EDB/IDB separation undecidable (Shmueli).
- [ ] Programs with negation: strong equivalence (here-and-there logic), uniform equivalence.
- [ ] Equivalence under Skolemisation and with function symbols.
- [ ] Which transformations are equivalences and which are only conservative extensions (normalisation, auxiliary predicates).

## Related pages

- [rule-set transformations and equivalence](../algorithms/rule-set-transformations-and-equivalence.md): transformations.
- [conjunctive queries and UCQ](conjunctive-queries-and-ucq.md): query containment.
- [logic programming and ASP](logic-programming-and-asp.md): strong equivalence.
- [Skolemisation and function-graph translations](skolemisation-and-function-graph-translations.md): conservative translations.

## Key references

- Y. Sagiv. *Optimizing Datalog programs*. In *Foundations of Deductive Databases and Logic Programming*, 1988. [U]
- O. Shmueli. *Equivalence of Datalog queries is undecidable*. JLP 15(3), 1993.
- V. Lifschitz, D. Pearce, A. Valverde. *Strongly equivalent logic programs*. ACM TOCL 2(4), 2001.
- T. Eiter, M. Fink. *Uniform equivalence of logic programs under the stable model semantics*. ICLP 2003.
- C. Lutz, F. Wolter. *Deciding inseparability and conservative extensions in the description logic EL*. JSC 45(2), 2010. [U]
