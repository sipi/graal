# Skolem functions and terms

Skolem functions replace existential variables by function terms. Rule-local Skolem functions give a conservative reading of existential rules; named Skolem functions, shared across rules, data and queries, give invented objects a stable identity (`manager(tom)`). Named functions are the core of F2 and the reason it was chosen.

> **Status in this project:** `F2` — named functions are the v1 primitive ([D1](../project/decisions.md#d1)); default lookup-before-invent ([D2](../project/decisions.md#d2)); late materialisation direction ([D8](../project/decisions.md#d8)); not in v0 ([D6](../project/decisions.md#d6)).
> **Page maturity:** stub · checked against README 2026-10-01

## TODO (stage 2)

Fill every section of the [page template](../conventions.md#2-page-template) (intuition with a running example, formal definition, key properties, in this project, pitfalls, related pages, references). Cover at least:

- [ ] Skolemisation: rule-local functions, frontier vs all-variables arguments (semi-oblivious vs oblivious chase).
- [ ] Named functions: declaration (`@function f/n`), sharing across rules, why it is strictly stronger than existential rules (report 09 §1.1 example).
- [ ] Herbrand / free-constructor reading vs FO reading; when they agree (positive CQs over constants) and diverge.
- [ ] Term model: hash-consing, depth, three term kinds (D1); functional terms in answers (OP-1).
- [ ] D8 late materialisation: symbolic Skolem terms with creation context; the owner's caveat (distinct terms may denote the same individual; UNA vs equality; counting).
- [ ] Sharing can break termination (report 09 Prop. 2).
- [ ] Industrial precedents: Vadalog injective Skolem functions, RDFox SKOLEM, LogicBlox constructors.
- [ ] E3-ex in named form and the infinite chain; link Q1.

## Related pages

- [existential rules](../concepts/existential-rules.md)
- [lookup before invent](../concepts/lookup-before-invent.md)
- [function graph translation tp](../concepts/function-graph-translation-tp.md)
- [equality and una](../concepts/equality-and-una.md)
- [labelled nulls](../concepts/labelled-nulls.md)
- [chase variants](../algorithms/chase-variants.md)

## Key references

- [report 09 §1, §3, §6](../../preliminary-analysis/09-skolem-function-frameworks.md)
- [report 11 §1.1, §1.3, §4.1, §5.2](../../preliminary-analysis/11-f2-framework-definition.md)
- [README D1, D8, Corrections](../../preliminary-analysis/README.md)
- Marnette. *Generalized schema-mappings: from termination to tractability*. PODS 2009.
- Fagin, Kolaitis, Popa, Tan. *Composing schema mappings: second-order dependencies to the rescue*. TODS 2005.
