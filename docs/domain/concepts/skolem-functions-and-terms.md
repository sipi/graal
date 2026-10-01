# Skolem functions and terms

A Skolem function replaces an existential variable by a function term of the variables it depends on, so that invented individuals become terms such as `manager(tom)`. Under a Herbrand reading these terms are distinct, stable objects that can be joined, compared, counted and returned. Function symbols may be local to one rule (classical Skolemisation) or shared across rules, data and queries, which changes the semantics.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Function terms as invented values: depth, nesting, ground terms; term representation (hash-consing).
- [ ] Rule-local Skolem functions (one per existential variable and rule) vs shared ('named') function symbols used in several rules, in data and in queries; why sharing is strictly more expressive and can break termination.
- [ ] Arguments of the Skolem function: frontier variables (semi-oblivious chase) vs all body variables (oblivious chase).
- [ ] Herbrand (free-constructor) reading vs first-order reading; agreement on positive CQs over constants; divergence with negation, aggregation, equality.
- [ ] Functional terms in answers, in input data, and their serialisation (identifiers, round-trip).
- [ ] Co-reference: when two distinct terms may denote the same individual (equality, link equality and UNA).
- [ ] Systems: Vadalog Skolem functions, RDFox SKOLEM, LogicBlox constructors, ASP function terms (link value-invention strategies).
- [ ] Example: every employee has a manager, reading (b); managers are employees (infinite terms).

## Related pages

- [Skolemisation and function-graph translations](skolemisation-and-function-graph-translations.md): from existential variables to functions and back.
- [value-invention strategies](value-invention-strategies.md): invention patterns in practice.
- [labelled nulls](labelled-nulls.md): the anonymous alternative.
- [equality and UNA](equality-and-una.md): identity of terms.
- [chase termination](chase-termination.md): termination with function terms.

## Key references

- B. Marnette. *Generalized schema-mappings: from termination to tractability*. PODS 2009.
- R. Fagin, P. G. Kolaitis, L. Popa, W.-C. Tan. *Composing schema mappings: second-order dependencies to the rescue*. ACM TODS 30(4), 2005.
- F. Calimeri, S. Cozza, G. Ianni, N. Leone. *Computable functions in ASP: theory and implementation*. ICLP 2008.
- L. Bellomarini, E. Sallinger, G. Gottlob. *The Vadalog system: Datalog-based reasoning for knowledge graphs*. PVLDB 11(9), 2018.
- Vadalog handbook, *Skolem functions*. https://vadalog.org/vadalog-handbook/latest/expressions-skolem.html [U]
- RDFox documentation, *Reasoning* (`SKOLEM` built-in). https://docs.oxfordsemantic.tech/reasoning.html
- LogicBlox reference manual, *Constructor predicates*. https://developer.logicblox.com/content/docs/core-reference/webhelp/constructor-predicates.html
