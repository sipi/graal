# Description logics and OWL

Description logics (DLs) are decidable fragments of first-order logic built from concepts (unary predicates), roles (binary predicates) and individuals; they are the formal basis of the W3C OWL 2 ontology language. Horn DLs (DL-Lite, EL, Horn-SHIQ) translate into existential rules, and the OWL 2 profiles QL, EL and RL correspond to rewriting, polynomial materialisation and rule-based reasoning respectively.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template) (adjacent-page variant: keep it short), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Syntax and semantics: concepts, roles, TBox, ABox; open world, no UNA by default.
- [ ] Families: ALC, SHIQ, SROIQ (OWL 2 DL), DL-Lite, EL; complexity overview.
- [ ] OWL 2 profiles QL, EL, RL and their rule-based implementations.
- [ ] Translation of Horn DLs into existential rules and Datalog; description logic programs.
- [ ] Reasoning procedures: tableaux with blocking, consequence-based reasoning, rewriting.
- [ ] Justifications as explanations.

## Related pages

- [existential rules](../concepts/existential-rules.md): rule counterpart.
- [ontology-based data access](ontology-based-data-access.md): DL-Lite and QL.
- [blocking and finite representations](../algorithms/blocking-and-finite-representations.md): tableau blocking.
- [RDF and SPARQL](rdf-and-sparql.md): OWL in the semantic web stack.

## Key references

- F. Baader, I. Horrocks, C. Lutz, U. Sattler. *An Introduction to Description Logic*. Cambridge University Press, 2017.
- W3C. *OWL 2 Web Ontology Language Profiles (Second Edition)*. W3C Recommendation, 2012.
- D. Calvanese, G. De Giacomo, D. Lembo, M. Lenzerini, R. Rosati. *Tractable reasoning and efficient query answering in description logics: the DL-Lite family*. JAR 39(3), 2007.
- B. N. Grosof, I. Horrocks, R. Volz, S. Decker. *Description logic programs: combining logic programs with description logic*. WWW 2003.
- F. Baader, S. Brandt, C. Lutz. *Pushing the EL envelope*. IJCAI 2005.
