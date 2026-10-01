# Incremental maintenance

Incremental maintenance updates a materialisation when facts (or rules) are added or removed, without recomputing from scratch. Insertions are handled by continuing semi-naive evaluation; deletions need algorithms such as delete-and-rederive (DRed), forward/backward/forward (FBF), backward/forward (B/F) or counting. Value invention, negation and aggregation complicate all of them.

## TODO (coverage)

Stub. Fill the [page template](../conventions.md#2-page-template), using the symbols of [notation](../notation.md) and, where possible, the shared [examples](../examples.md). Cite primary sources. Cover at least:

- [ ] Insertions; deletions: DRed, B/F, FBF, counting; trade-offs.
- [ ] Maintenance with stratified negation and aggregation.
- [ ] Maintenance with function terms and labelled nulls; incremental restricted and core chase (open research).
- [ ] Rule changes vs data changes; invalidating derived artefacts (stored rewritings, indexes).
- [ ] Differential dataflow as an alternative model.

## Related pages

- [semi-naive evaluation](semi-naive-evaluation.md): insertion is continued evaluation.
- [provenance](../concepts/provenance.md): counting and derivation records.
- [RDFox](../systems/rdfox.md): industrial implementation.
- [others (DDlog)](../systems/others.md): differential dataflow.

## Key references

- A. Gupta, I. S. Mumick, V. S. Subrahmanian. *Maintaining views incrementally*. SIGMOD 1993.
- B. Motik, Y. Nenov, R. Piro, I. Horrocks. *Maintenance of Datalog materialisations revisited*. Artificial Intelligence 269, 2019. [U]
- B. Motik, Y. Nenov, R. Piro, I. Horrocks. *Incremental update of Datalog materialisation: the backward/forward algorithm*. AAAI 2015.
- F. McSherry, D. G. Murray, R. Isaacs, M. Isard. *Differential dataflow*. CIDR 2013.
