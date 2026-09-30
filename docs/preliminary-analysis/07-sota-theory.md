# 07 — State of the art (theory) for a new existential-rule reasoning engine

Scope: theoretical background for the formal specification of an engine that is (i) always sound, (ii) complete when decidability is established (otherwise it reports "completeness not guaranteed"), (iii) driven by a decidability/complexity analyser that selects the algorithm, (iv) supports stratified negation then aggregation, (v) has an optimisation pass producing a **logically equivalent** rule set with proofs and traceability.

Method: literature knowledge cross-checked by web search (arXiv/publisher pages were partly blocked; DBLP/Dagstuhl/KR proceedings/ICCL pages were used). Items marked **[U]** were not re-verified during this survey (exact bound, venue, or author list may need checking before citing). Items marked **[V]** were explicitly verified in this session.

Notation: a rule (TGD) is `R = ∀x∀y (B[x,y] → ∃z H[x,z])`; `x` is the frontier. `Σ` is a rule set, `D` an instance (database), `q` a (U)CQ. `fr(R)` = frontier. `freeze(B)` = the instance obtained by replacing every variable of `B` by a fresh constant.

---

## 1. Decidability landscape for existential rules

### 1.1 Baseline
- BCQ entailment `Σ ∪ D ⊨ q` is undecidable for arbitrary TGDs (Beeri & Vardi, ICALP 1981; Chandra, Lewis, Makowsky, STOC 1981). It is RE (semi-decidable) because the chase is a complete procedure: every fact derived by any chase is entailed, and every CQ entailed is witnessed by a finite chase prefix. **Consequence for the spec: a (breadth-first / fair) chase is always sound, and complete "in the limit"; completeness is only ever lost by stopping early.**

### 1.2 Abstract (semantic) classes: FES / FUS / BTS
Baget, Leclère, Mugnier, Salvat. *On rules with existential variables: Walking the decidability line.* Artificial Intelligence 175(9–10), 2011. https://doi.org/10.1016/j.artint.2011.03.002
- **FES** (finite expansion set): for every `D`, `Σ ∪ D` has a finite universal model. Equivalent to termination of the **core chase** on every instance (Deutsch, Nash, Remmel, *The chase revisited*, PODS 2008, https://doi.org/10.1145/1376916.1376938: the core chase terminates on `D` iff a finite universal model exists).
- **FUS** (finite unification set): every CQ has a finite UCQ rewriting (≡ first-order rewritability for all CQs).
- **BTS** (bounded treewidth set): for every `D` there is a universal model of bounded treewidth (bound may depend on `D`). FES ⊆ BTS; FUS is incomparable with BTS.
- Membership in FES, FUS, BTS is **undecidable** (Baget et al. 2011). Recent strengthening **[V]**: Larroque & Manière, *Will my favorite chases terminate if evaluating conjunctive queries does? One does not simply decide this*, IJCAI 2026 (https://arxiv.org/abs/2605.12349): membership in the standard/restricted/core chase termination classes and in BTS remains undecidable even when the input is restricted to rule sets for which CQ entailment is known to be decidable.

### 1.3 Concrete (recognisable) classes and complexity of BCQ entailment

| Class | Key reference | Abstract class | Data compl. | Combined compl. | Recognition |
|---|---|---|---|---|---|
| Datalog (no ∃) | Dantsin, Eiter, Gottlob, Voronkov, ACM CSUR 2001 | FES | PTIME-c | EXPTIME-c | syntactic |
| Linear (single body atom) | Calì, Gottlob, Lukasiewicz, JWS 2012; Calì, Gottlob, Kifer JAIR 2013 | FUS (+BTS) | AC0 | PSPACE-c (NP-c bounded arity) | syntactic |
| Guarded | Calì, Gottlob, Kifer, KR 2008 / JAIR 48, 2013 | BTS | PTIME-c | 2EXPTIME-c (EXPTIME-c bounded arity) | syntactic |
| Frontier-guarded | Baget, Leclère, Mugnier, Salvat IJCAI 2011; Baget, Mugnier, Rudolph, Thomazo IJCAI 2011 (*Walking the complexity lines for generalized guarded existential rules*) | BTS | PTIME-c | 2EXPTIME-c | syntactic |
| Weakly guarded / weakly frontier-guarded | same | BTS | EXPTIME-c | 2EXPTIME-c | PTIME (affected positions) |
| Sticky | Calì, Gottlob, Pieris, AIJ 193, 2012 | FUS | AC0 | EXPTIME-c (NP-c bounded arity) | PTIME (marking procedure) |
| Weakly sticky | same | — (neither FUS nor FES in general) | PTIME | 2EXPTIME-c | PTIME |
| Sticky-join, tame (guarded+sticky "glue") | Calì, Gottlob, Pieris; Gottlob, Manna, Pieris, *Combining decidability paradigms for existential rules*, TPLP 13(4–5), 2013 | — | AC0 / PTIME | EXPTIME/2EXPTIME **[U]** | PTIME |
| Warded Datalog± | Arenas, Gottlob, Pieris PODS 2014; Gottlob & Pieris IJCAI 2015 | — (captured by Vadalog termination control) | PTIME-c | EXPTIME-c | PTIME |
| Piece-wise linear warded | Berger, Gottlob, Pieris, Sallinger, *The space-efficient core of Vadalog*, PODS 2019 / TODS 2022 (https://arxiv.org/abs/1809.05951) | — | NLOGSPACE-c | PSPACE-c **[U]** | PTIME |
| Shy | Leone, Manna, Terracina, Veltri, KR 2012; *Fast query answering over existential rules*, ACM TOCL 20(2), 2019 | (parsimonious chase complete) | PTIME-c | **[U]** (EXPTIME-ish) | PTIME |
| Dyadic existential rules | Gottlob, Manna, Marte, TPLP 2023 (arXiv 2307.12051) | generalises combinations | depends on components | — | depends |
| Weakly acyclic (WA) | Fagin, Kolaitis, Miller, Popa, TCS 336, 2005 | FES | PTIME-c | 2EXPTIME-c | PTIME (NL) |
| Acyclic GRD (aGRD) | Baget et al. 2011 | FES ∩ FUS | AC0 | NEXPTIME-ish **[U]** (non-recursive) | GRD construction: deciding one edge = existence of a piece-unifier, NP-complete in general, PTIME for atomic heads **[U]** |

Acyclicity notions (sufficient conditions for **all-instance** chase termination):
- **WA** (Fagin et al. 2005): position dependency graph without a cycle through a special edge → semi-oblivious/Skolem chase terminates on all instances.
- **Super-weak acyclicity (SWA)**: Marnette, *Generalized schema-mappings: from termination to tractability*, PODS 2009. Also shows oblivious/semi-oblivious all-instance termination = termination on the **critical instance**.
- **Joint acyclicity (JA)**: Krötzsch & Rudolph, *Extending decidable existential rules by joining acyclicity and guardedness*, IJCAI 2011. PTIME-checkable.
- **MFA / MSA** (model-faithful / model-summarising acyclicity): Cuenca Grau, Horrocks, Krötzsch, Kupke, Magka, Motik, Wang, *Acyclicity notions for existential rules and their application to query answering in ontologies*, JAIR 47, 2013 (https://doi.org/10.1613/jair.3964). MFA: run the Skolem chase on the critical instance, fail if a cyclic Skolem term appears; checking MFA is 2EXPTIME-complete, MSA EXPTIME-complete (lower for bounded arity **[U]**). Inclusions: WA ⊆ SWA ⊆ MSA ⊆ MFA and WA ⊆ JA ⊆ MSA (relation JA vs SWA: see Fig. 1 of the JAIR paper **[U]**). MFA-rule sets: BCQ entailment 2EXPTIME-complete combined, PTIME data.
- **Restricted-chase acyclicity**: RMFA / RJA / RMFC (restricted MFA, restricted JA, and a *cyclicity* criterion proving non-termination): Carral, Dragoste, Krötzsch, *Restricted chase (non)termination for existential rules with disjunctions*, IJCAI 2017 (https://doi.org/10.24963/ijcai.2017/128). They also introduce the **Datalog-first restricted chase** (apply Datalog rules to fixpoint before any existential rule), which terminates strictly more often.
- **Graph of position dependencies combined with GRD**: Baget, Garreau, Mugnier, Rocher, *Extending acyclicity notions for existential rules*, ECAI 2014 — a family of acyclicity notions that unifies WA/JA-like position graphs with rule dependencies, strictly generalising both.
- **Hierarchical restricted-chase termination**: Karimi, Zhang, You, *Restricted chase termination for existential rules: a hierarchical approach and experimentation*, TPLP 2021 (arXiv 2005.05423).
- **Beyond PTIME data complexity**: Hanisch & Krötzsch, *Chase termination beyond polynomial time*, PODS 2024 (arXiv 2403.16712) **[U authors]** — acyclicity-like classes with k-EXPTIME data complexity.

Combinations along GRD strata (Baget et al. 2011; implemented in **Kiabora**: Leclère, Mugnier, Rocher, *Kiabora: an analyzer of existential rule bases*, RR 2013): compute the SCCs of the GRD; if the SCC DAG can be layered so that each layer belongs to a class and the sequence is "compatible", decidability follows:
- sequence of FES layers is FES; sequence of FUS layers is FUS;
- FES layers (below) then FUS layers (above): decidable — materialise the lower part, rewrite the query with the upper part;
- BTS then FUS: decidable; FUS *below* FES/BTS: not in general (needs the combined notions such as Gottlob–Manna–Pieris "tameness").
Kiabora labels each SCC by the concrete classes it satisfies (aGRD, WA/JA-like, linear, guarded, frontier-guarded, sticky, …). **This is exactly why shrinking SCCs (the LUBM observation) matters**: smaller SCCs → more SCCs are individually in a recognisable class and more rules become non-recursive (trivially FES∩FUS).

Refined dependency relations (fewer edges than the classical GRD):
- Positive reliances and restraints (restricted-chase aware): Krötzsch, *Computing cores for existential rules with the standard chase and ASP*, KR 2020; González, Ivliev, Krötzsch, Mennicke, *Efficient dependency analysis for rule-based ontologies*, ISWC 2022 (https://arxiv.org/abs/2207.09669) **[V]**, scaling to >100k rules.

### 1.4 Chase variants and termination

Variants (Onet, *The chase procedure and its applications in data exchange*, Dagstuhl Follow-Ups 5, 2013; Grahne & Onet, *Anatomy of the chase*, Fundamenta Informaticae 157(3), 2018; Benedikt et al., *Benchmarking the chase*, PODS 2017):
- **Oblivious**: fires every trigger once.
- **Semi-oblivious / Skolem**: fires once per frontier mapping; equivalent (up to null renaming) to the Skolem chase (Marnette 2009).
- **Restricted / standard**: fires only active triggers (head not already satisfied); result depends on order.
- **Datalog-first restricted** (Carral et al. 2017).
- **Parallel / breadth-first** variants (apply all active triggers of a round simultaneously).
- **Core chase** (Deutsch et al. 2008): parallel restricted step + core computation; terminates iff a finite universal model exists (complete for FES).
- **Equivalent chase** (Rocher, PhD thesis, Montpellier 2016): stops as soon as a step produces an instance homomorphically equivalent to the previous one; terminates on exactly the FES instances, without computing cores at each step **[U — characterisation to check]**.
- **Parsimonious chase** (Leone et al. 2012/2019): does not fire a trigger whose result is homomorphic to existing facts; complete for query answering on shy programs, not a universal model in general.
- **Vadalog termination control** (Bellomarini, Sallinger, Gottlob, *The Vadalog system*, PVLDB 11(9), 2018, https://www.vldb.org/pvldb/vol11/p975-bellomarini.pdf): isomorphism/"warded forest" based pruning, complete for warded rules.

Termination notions: for a fixed `D` vs. for all `D` (**all-instance / uniform**); for the restricted chase, **all fair sequences** (∀∀) vs. **some sequence** (∀∃, "sometimes termination").

Key results:
- Oblivious and semi-oblivious: all-instance termination ⇔ termination on the critical instance (Marnette 2009). Still undecidable for arbitrary TGDs.
- Termination on a given instance: undecidable for all variants (Deutsch et al. 2008).
- All-instance termination: undecidable (Gogacz & Marcinkowski, *All-instances termination of chase is undecidable*, ICALP 2014; Grahne & Onet 2018 for the systematic picture). Even for single-head binary TGDs for the oblivious chase: Bednarczyk, Ferens, Ostropolski-Nalewaja, IJCAI 2020 **[U title]**.
- Universal restricted-chase termination is **not even recursively enumerable**: Carral, Gerlach, Larroque, Thomazo, *Restricted chase termination: you want more than fairness*, PODS 2025 / PACMMOD 3(2) art. 109 (https://doi.org/10.1145/3725246) **[V]** — places it in the analytical hierarchy (Π¹₁-hard **[U level]**); the extra hardness stems from the fairness condition, and they propose an alternative to fairness that lowers it.
- Decidable cases: guarded/linear oblivious & semi-oblivious termination (Calautti, Gottlob, Pieris, *Chase termination for guarded existential rules*, PODS 2015 — 2EXPTIME-c guarded, PSPACE-c linear **[U bounds]**); sticky semi-oblivious (Calautti & Pieris, ICDT 2019 / ToCS 2021 **[V]**); linear rules, several variants with one technique (Leclère, Mugnier, Thomazo, Ulliana, *A single approach to decide chase termination on linear existential rules*, ICDT 2019 **[V]**); semi-oblivious linear complexity (Calautti, Milani, Pieris, PVLDB 16, 2023 **[V]**); restricted chase, single-head guarded and sticky (Gogacz, Marcinkowski, Pieris, PODS 2020 / LICS 2020 guarded case; *Uniform restricted chase termination*, SIAM J. Comput. 52(3), 2023 **[V]**); multi-head linear restricted (Gerlach, Larroque, Marcinkowski, Ostropolski-Nalewaja, KR 2025, arXiv 2509.19400 **[V]**).
- Normalisation changes termination: Carral, Larroque, Mugnier, Thomazo, *Normalisations of existential rules: not so innocuous!*, KR 2022 (https://proceedings.kr.org/2022/11/) **[V]** — see §3.
- Expressive power of terminating classes: Krötzsch, Marx, Rudolph, *The power of the terminating chase*, ICDT 2019 invited (https://drops.dagstuhl.de/entities/document/10.4230/LIPIcs.ICDT.2019.3) **[V]**.

**Consequence for the analyser**: never rely on deciding termination; use a portfolio of *sufficient* conditions (cheap first: aGRD, WA, JA, SWA; then MSA/MFA with budget; RJA/RMFA for restricted; Kiabora-style stratum combination) and treat the class as "unknown" otherwise. Also observe that **completeness can be established dynamically**: if a restricted/core chase actually terminates on the given `D` (or a UCQ rewriting reaches a fixpoint for the given `q`), the result is complete for that input regardless of the static class.

---

## 2. Algorithm selection

Paradigms:
1. **Materialisation (forward chaining)**: chase variants above. Needs FES (per instance). Best when many queries, stable data.
2. **Query rewriting (backward chaining)**: UCQ rewriting with piece-unifiers — König, Leclère, Mugnier, Thomazo, *Sound, complete and minimal UCQ-rewriting for existential rules*, Semantic Web J. 6(5), 2015 (Graal's PURE). Needs FUS. Rewriting into Datalog (not UCQ) extends applicability: Gottlob & Schwentick KR 2012 (polynomial non-recursive Datalog rewritings for linear/sticky); Ahmetaj, Ortiz, Šimkus, *Rewriting guarded existential rules into small datalog programs*, ICDT 2018; Benedikt, Buron, Germano, Kappelmann, Motik, *Rewriting the infinite chase*, PVLDB 15(11), 2022 and *Rewriting the infinite chase for guarded TGDs*, ACM TODS 2024 **[V]** — saturation-based Datalog rewriting of guarded TGDs, then any Datalog engine.
3. **Combined approach**: materialise a (polynomial, query-independent) finite model that may be unsound, then rewrite the query to filter spurious answers — Lutz, Toman, Wolter, IJCAI 2009 (EL); Kontchakov, Lutz, Toman, Wolter, Zakharyaschev, KR 2010 (DL-Lite); Lutz, Seylan, Toman, Wolter, ISWC 2013 (filtering). For guarded/warded existential rules, related "finite-model-plus-filter" ideas appear in Gottlob, Manna, Pieris **[U]**. Note: the intermediate model is not sound by itself; only the filtered answers are. The spec must state soundness at the level of **returned answers**, not of internal structures.
4. **Hybrid by GRD strata** (Baget et al. 2011; Kiabora; Graal): materialise FES strata, rewrite FUS strata above them.
5. **Goal-directed materialisation**: magic sets for existential rules — Alviano, Leone, Manna, Terracina, Veltri, *Magic-sets for Datalog with existential quantifiers*, Datalog 2.0, 2012 (LNCS 7494) — sound and complete for shy/finitely-ground settings **[U scope]**; parsimonious chase (shy).
6. **Termination-controlled chase**: Vadalog (warded, isomorphism-based pruning; PTIME data complexity); **Datalog-first restricted chase** with restraint/reliance-based scheduling (VLog: Urbani, Krötzsch, Jacobs, Dragoste, Carral, *Efficient model construction for Horn logic with VLog*, IJCAR 2018; Nemo: Ivliev, Gerlach, Meusel, Steinberg, Krötzsch, *Nemo: your friendly and versatile rule reasoning toolkit*, KR 2024 **[V]**).
7. **Chase with a budget** (fallback): any fair chase prefix; answers are sound; status "completeness not guaranteed".

Decision procedure used in practice (synthesis, to be formalised in the spec):
1. Stratify w.r.t. negation/aggregation (§4).
2. Inside each stratum, compute the (refined) GRD and its SCC DAG; label each SCC with recognised classes.
3. If the whole stratum is (statically) FES → materialise with the cheapest variant whose termination is guaranteed by the certificate found (WA/JA/MFA ⇒ Skolem chase; RJA/RMFA ⇒ Datalog-first restricted chase; aGRD ⇒ any).
4. Else if FUS (linear, sticky, aGRD, FUS-layers) → rewrite (UCQ, or Datalog rewriting for size).
5. Else if FES-below/FUS-above decomposition exists → hybrid.
6. Else if guarded/warded/shy → specific complete procedures (guarded saturation to Datalog; warded termination control; parsimonious chase).
7. Else → budgeted chase, report "completeness not guaranteed" unless termination is observed at run time.
Benchmarks to calibrate costs: Benedikt, Konstantinidis, Mecca, Motik, Papotti, Santoro, Tsamoura, *Benchmarking the chase*, PODS 2017.

---

## 3. Equivalence-preserving rule transformations

### 3.1 Three notions — keep them separate in the spec
Let sig(Σ) ⊆ S.
- **Logical equivalence** `Σ ≡ Σ'` (same signature): `Mod(Σ) = Mod(Σ')`, i.e. `Σ ⊨ Σ'` and `Σ' ⊨ Σ`.
- **Conservative extension** (fresh predicates allowed, sig(Σ') ⊇ sig(Σ)): `Σ' ⊨ Σ` and (model-conservative) every model of `Σ` expands to a model of `Σ'`; deductive-conservative = same consequences over sig(Σ). Model-conservative ⇒ deductive-conservative. **Not** logical equivalence (different signatures; a database containing a fresh-predicate fact breaks it).
- **Query (UCQ-answer) equivalence** relative to a data schema `E` and query schema `Q`: for all `D` over `E` and all UCQs `q` over `Q`, `Σ ∪ D ⊨ q` iff `Σ' ∪ D ⊨ q`.

Useful facts:
- **Proposition (freezing).** For positive existential rules, `Σ ⊨ (B → ∃z H)` iff `chase(Σ, freeze(B)) ⊨ ∃z freeze_fr(H)` (frontier variables frozen consistently). Proof: universal-model property of the chase + the fact that a rule is falsified exactly by a model containing an image of `B` with no extension to `H`. Classical for Datalog as **uniform containment** (Sagiv, *Optimizing datalog programs*, in Foundations of Deductive Databases and Logic Programming, 1988) and for TGD implication (Beeri & Vardi, JACM 1984).
- **Corollary 1.** Logical entailment `Σ ⊨ Σ'` reduces to |Σ'| BCQ-entailment tests with `Σ` over frozen bodies (instances with constants). It is decidable whenever BCQ entailment under `Σ` is decidable **over arbitrary instances** (FES, FUS, BTS, and every concrete class of §1.3), with the same complexity as combined-complexity BCQ entailment. Equivalence needs both directions, so both `Σ` and `Σ'` must be in decidable classes (or the certificate must be found by a terminating chase run on the frozen body — which is instance-level, hence often available even when the class is unknown).
- **Corollary 2.** For positive existential rules, *CQ-equivalence over all instances over the full signature* ⇔ *logical equivalence* (take `D = freeze(B)`). Query equivalence becomes strictly weaker only when data or queries are restricted to sub-signatures (EDB/IDB separation). For Datalog, query equivalence with EDB/IDB separation is **undecidable** (Shmueli, *Equivalence of Datalog queries is undecidable*, J. Logic Programming 15, 1993), boundedness is undecidable (Gaifman, Mairson, Sagiv, Vardi, JACM 40(3), 1993), containment of Datalog in UCQ is decidable, 2EXPTIME-complete (Chaudhuri & Vardi, JCSS 54, 1997), while uniform containment/equivalence is decidable (Sagiv 1988; EXPTIME-complete **[U]**).
- For schema mappings (st-TGDs) the hierarchy logical equivalence ⊊ data-exchange equivalence ⊊ CQ-equivalence was established by Fagin, Kolaitis, Nash, Popa, *Towards a theory of schema-mapping optimization*, PODS 2008; relaxed notions: Pichler, Sallinger, Savenkov, *Relaxed notions of schema mapping equivalence revisited*, ToCS 53, 2013 **[V]**.
- With negation/aggregation, logical equivalence of the positive reading is meaningless; the analogue is **strong equivalence** (Lifschitz, Pearce, Valverde, ACM TOCL 2(4), 2001) or **uniform equivalence** (Eiter & Fink, ICLP 2003): same canonical model(s) for every added set of facts. For stratified programs, the practical requirement is: *for every input `D` over the full signature, the canonical model coincides (up to null renaming / homomorphic equivalence)*.

### 3.2 Catalogue of transformations and their status

| Transformation | Status | Remarks / reference |
|---|---|---|
| Renaming variables, reordering atoms, removing duplicate atoms | Logical equivalence | trivial |
| **Head piece decomposition**: split `B → ∃z(H1 ∧ H2)` when `H1`, `H2` share no existential variable into `B → ∃z1 H1`, `B → ∃z2 H2` | **Logical equivalence** | ∃ distributes over ∧ on disjoint variables; used as normal form by Gottlob, Pichler, Savenkov, *Normalization and optimization of schema mappings*, PVLDB 2009 / VLDBJ 20, 2011 **[V]**. Changes chase behaviour (number of triggers, restricted-chase applicability) but not semantics. |
| Datalog head splitting (no ∃) into single-atom heads | **Logical equivalence** | special case of the above |
| **Atomic-head decomposition with fresh predicate** `B → ∃z p(x,z)`, `p(x,z) → Hi` | **Conservative extension only** | Carral, Larroque, Mugnier, Thomazo, KR 2022 **[V]**: the "one-way" atomic decomposition and single-piece decomposition may alter chase termination and other properties for some chase variants (restricted/core); a "two-way" variant (adding `H → p(x,z)`) behaves better for the restricted chase **[U exact statements]**. |
| Body normalisation with fresh predicates (binarisation, sub-query factorisation) | Conservative extension | same caveats |
| **Redundant rule elimination**: remove `R` if `Σ \ {R} ⊨ R` | **Logical equivalence** | decidable by Corollary 1 when `Σ\{R}` is in a decidable class, or certified by a terminating chase on `freeze(body(R))`. Order-dependent; minimum-cardinality equivalent subsets are hard (Gottlob et al. 2011 for st-TGDs). |
| **Rule-local minimisation**: body core (remove body atom `a` if the rule without `a` is equivalent *as a single rule*: CQ-minimisation fixing frontier) and head core (core of `H` relative to `B`, fixing frontier) | **Logical equivalence** | Chandra–Merlin minimisation; GPS 2011 normal form: for st-TGDs the result is unique up to isomorphism **[U uniqueness for recursive TGDs — likely false in general]** |
| **Σ-relative body minimisation**: replace `R` by `R' = (B\{a} → ∃z H)` if `Σ ⊨ R'` | **Logical equivalence** (since `R' ⊨ R`) | Sagiv-style uniform minimisation. |
| **Σ-relative head reduction**: replace `R` by `R'` with smaller head if `(Σ\{R}) ∪ {R'} ⊨ R` | **Logical equivalence** | removes outgoing GRD edges from `R` — the most likely explanation of the LUBM observation (heads repeating consequences derivable through hierarchies). |
| Adding an entailed rule (e.g. an unfolding `B1 ∧ C → H` of `B1 → p`, `p ∧ C → H`) | **Logical equivalence** | safe but may create edges. |
| **Unfolding + deletion** of the unfolded rule / predicate elimination | **Query equivalence only** (w.r.t. data not containing the eliminated predicate) | Tamaki & Sato, *Unfold/fold transformation of logic programs*, ICLP 1984 (least Herbrand model); for existential rules one unfolding step = one piece-unifier rewriting step (König et al. 2015). |
| Folding (introducing a new predicate for a shared subquery) | Conservative extension | |
| Collapsing equivalent predicates (`A ↔ B`) into one | Neither (changes signature) unless kept as a view; conservative/query equivalence at best | |
| Skolemisation | Satisfiability/CQ-answer preserving, not logical equivalence (different signature) | |

### 3.3 Transformations that shrink GRD SCCs
- A GRD edge `R1 → R2` exists iff some piece-unifier of `body(R2)` with `head(R1)` exists (Baget et al. 2011); refined variants remove "useless" unifiers (e.g. ones leading to already-satisfied triggers: positive reliances, González et al. 2022).
- **Monotonicity caveats (important for the spec)**: (a) removing rules can only remove edges (node deletion); (b) head reduction and piece decomposition can only remove outgoing edges (fewer or smaller head pieces to unify with) **[claim to be proved in the spec; follows since every piece-unifier with a sub-head restricts to one with the original head]**; (c) **body minimisation is not monotone**: removing a body atom can make a previously invalid piece-unifier valid (the "existential variable must not occur outside the unified part" condition relaxes), thus adding edges; (d) adding entailed rules adds nodes/edges. The optimiser must therefore recompute the GRD and accept a transformation only if its objective improves (e.g. lexicographic: size of largest SCC, number of cyclic SCCs, number of rules in non-recognised SCCs).
- **Semantic lower bound**: an SCC may be *inherent*, e.g. `A(x) → B(x)`, `B(x) → A(x)` cannot be made acyclic under logical equivalence. A useful spec notion: an SCC is *reducible* if some logically equivalent Σ' has a finer SCC decomposition; deciding reducibility is open (no reference found) — treat the optimiser as a heuristic search with certified steps.
- Traceability of transformed rule sets: Ivliev, Krötzsch, Marx, *Recovering explanations from transformed rule-based ontologies*, ISWC 2026 (arXiv 2608.06399) **[V]** — reconstructing proofs w.r.t. the original rules from proofs w.r.t. rewritten Datalog rules; complexity results and two languages to specify proof transformations. Directly relevant to "keep the original set and full traceability".

---

## 4. Stratified negation and aggregation with existential rules

### 4.1 The core difficulty
With existential rules, negation is evaluated over a *particular* universal model, and universal models differ (Skolem vs restricted vs core chase; strategy-dependent restricted chase). Answers to `¬` can differ between homomorphically equivalent models. Therefore **logically equivalent positive strata can yield different results after negation** — which directly conflicts with an optimiser that only guarantees logical equivalence.

Key literature:
- Calì, Gottlob, Lukasiewicz, *A general Datalog-based framework for tractable query answering over ontologies*, JWS 14, 2012 — stratified negation for guarded Datalog± evaluated over the (oblivious) chase; semantics tied to a specific chase **[U — check which variant and known issues]**.
- Magka, Krötzsch, Horrocks, *Computing stable models for nonmonotonic existential rules*, IJCAI 2013 — stable models of the **Skolemised** program; R-acyclicity / R-stratification guarantee finiteness/uniqueness.
- Baget, Garreau, Mugnier, Rocher, *Revisiting chase termination for existential rules and their extension to nonmonotonic negation*, NMR 2014 (arXiv 1405.1071) — shows the dependence on Skolem vs restricted chase and defines stable-model-like semantics based on chase variants.
- Gottlob, Hernich, Kupke, Lukasiewicz, *Stable model semantics for guarded existential rules and description logics*, KR 2014 (non-Skolem, unnamed individuals); Hernich, Kupke, Lukasiewicz, Gottlob, *Well-founded semantics for extended datalog and ontological reasoning*, PODS 2013; Gottlob et al., *Equality-friendly well-founded semantics*, AAAI 2012.
- Alviano & Pieris, *Default negation for non-guarded existential rules*, PODS 2015; Alviano, Morak, Pieris, *Stable model semantics for tuple-generating dependencies revisited*, PODS 2017.
- Ellmauthaler, Krötzsch, Mennicke, *Answering queries with negation over existential rules*, AAAI 2022 (https://arxiv.org/abs/2112.07376) **[V]** — proposes **universal core models** as the reference semantics for queries with negation, and syntactic query fragments that can be answered equivalently over other (chase) models.
- Küchenmeister, Ivliev, Arndt, Krötzsch, *Stratified negation in RDF rules: a correct approach*, 2026 (arXiv 2607.28778) **[V]** — "chain stratification" guaranteeing a unique, lean (core) and justified result regardless of application order, with blank nodes in heads.
- Implementations: Nemo, VLog/Rulewerk, RDFox, Vadalog all use Skolem-style (deterministic) nulls with stratified negation (Vadalog: deterministic, injective, range-disjoint Skolem functions **[V]**).

### 4.2 Aggregation
- Datalog aggregation semantics: stratified aggregation (Mumick, Pirahesh, Ramakrishnan, VLDB 1990); monotonic aggregation over lattices (Ross & Sagiv, PODS 1992 / JCSS 54, 1997); Van Gelder, *Foundations of aggregation in deductive databases*, DOOD 1993; Mazuran, Serra, Zaniolo, *Extending the power of datalog recursion*, VLDBJ 22, 2013 (mcount/msum); Kaminski, Cuenca Grau, Kostylev, Motik, Horrocks, *Foundations of declarative data analysis using limit Datalog programs*, IJCAI 2017, and *Stratified negation in limit Datalog programs*, IJCAI 2018 (arXiv 1804.09473); ASP aggregates: Faber, Pfeifer, Leone, AIJ 175, 2011.
- With existential rules: Vadalog supports monotonic aggregation in recursion (Bellomarini et al. 2018; Temporal Vadalog, TPLP 2025 **[V]**); Nemo supports stratified aggregates (KR 2024) **[V]** — treatment of nulls in aggregates not verified **[U]**.
- **Nulls in aggregates are semantically unstable**: `count` of existential witnesses differs across universal models (two nulls may denote the same element). Certain-answer semantics for aggregates over incomplete data: range semantics [glb, lub] of Afrati & Kolaitis, *Answering aggregate queries in data exchange*, PODS 2008; counting over incomplete databases: Arenas, Barceló, Monet, PODS 2020 **[U]**.

### 4.3 Proposed semantic choices (defensible defaults)

**Choice A (recommended default) — null-insensitive stratified semantics.**
- Syntax restriction ("constant-guarded" negation/aggregation): every variable occurring in a negated atom, in a group-by key or in an aggregated argument must be bound only to constants, i.e. appear in the positive body at a position that is *not affected* (affected positions: Calì, Gottlob, Kifer 2008 — positions that may carry nulls), or be explicitly guarded by a built-in `isConstant(v)` filter.
- Semantics: strata evaluated bottom-up; each stratum's positive part by any chase producing a universal model; negation/aggregation evaluated on the constant-only facts.
- **Theorem sketch (to include in the spec)**: if `M1`, `M2` are homomorphically equivalent models then they contain exactly the same null-free facts; hence for a constant-guarded rule, a match in `M1` composed with `h : M1 → M2` is a match in `M2` with identical negated/aggregated tuples; by induction on strata, the models obtained are homomorphically equivalent and the null-free facts (hence all certain answers) are independent of the chase variant. **Corollary**: replacing any stratum's positive part by a logically equivalent one preserves all answers — the optimiser's logical-equivalence guarantee is then sufficient.
- Aggregates are set-based over distinct constant tuples; `count` over existential witnesses is forbidden (or only `exists`-style `count ≥ 1`).

**Choice B (opt-in) — Skolem perfect-model semantics.**
- Semantics: the perfect (= unique stable) model of the Skolemised stratified program (Magka et al. 2013), i.e. what Nemo/VLog/Vadalog compute; negation and aggregation may range over nulls (Skolem terms).
- Well-defined and deterministic, but **syntax-dependent**: logically equivalent rewritings (even head-piece splitting) change Skolem terms and therefore negation/aggregation results. Under Choice B the optimiser must only apply transformations preserving the Skolem model up to term renaming (e.g. transformations restricted to Datalog rules, or proven "Skolem-equivalent"), and the engine must fix Skolem-function naming in the trace.
- Alternative reference semantics for research mode: core-model semantics (Ellmauthaler et al. 2022), costly.

---

## 5. Mechanised proofs

Existing formalisations:
- **Coq**: DatalogCert — Benzaken, Contejean, Dumbrava, *Certifying standard and stratified Datalog inference engines in SSReflect*, ITP 2017 **[V]** (model-theoretic + fixpoint semantics, bottom-up and stratified evaluation, soundness/completeness/termination/minimality). Bonifati, Dumbrava, Arias, *Certified graph view maintenance with regular Datalog*, TPLP 18(3–4), 2018 (arXiv 1804.10565) **[V]** (incremental maintenance, extracted engine). *Developing and certifying Datalog optimizations in Coq/MathComp*, CPP 2021 (https://doi.org/10.1145/3437992.3439913) **[V title; authors U — believed Bégay, Crégut, Monin]**. Benzaken & Contejean, *A Coq mechanised formal semantics for realistic SQL queries*, CPP 2019.
- **Isabelle**: AFP entry *Stratified Datalog and Program Analysis* (https://www.isa-afp.org/entries/Stratified_Datalog.html) and SAC 2024 paper *Isabelle-verified correctness of Datalog programs for program analysis* (Schlichtkrull, Hansen, Nielson **[U authors]**) **[V]**. Also Isabelle formalisations of first-order resolution/superposition (IsaFoR/IsaFoL) usable for FO entailment reasoning.
- **Lean 4**: Tantow, Gerlach, Mennicke, Krötzsch, *Verifying Datalog reasoning with Lean*, ITP 2025, LIPIcs 352:36 **[V]** — certifying approach: the engine emits proof trees, a verified Lean checker validates them. *The Chase in Lean — crafting a formal library for existential rule research*, 2026 (https://arxiv.org/abs/2604.22531; code: https://github.com/monsterkrampe/Existential-Rules-in-Lean) **[V; authors U, likely Gerlach et al.]** — ~19k lines; disjunctive existential rules; generic chase parameterised by an "obsolescence condition" covering Skolem and restricted chase; MFA-like termination criteria unified; chase result is a universal model; core result without alternative matches outlined.
- No mechanisation found of GRD/piece-unifier theory, FUS rewriting completeness, or of equivalence-preserving rule-set transformations for existential rules **[gap]**.

Feasibility assessment for this engine:
- *Per-transformation schema proofs* (piece decomposition, variable renaming, rule-local minimisation via a homomorphism, redundant-rule removal given an entailment witness) are short first-order arguments — easily mechanised in Lean on top of the 2026 chase library (definitions of models/entailment exist).
- *The freezing proposition* (entailment ⇔ chase on frozen body) needs the universal-model theorem, already proved in the Lean library — so it is within reach.
- Recommended architecture: **certifying transformations** rather than a verified optimiser. Each optimisation step emits a certificate: for `Σ ⊨ R`, a finite chase derivation from `freeze(body R)` plus the homomorphism from `freeze(head R)`; for local minimisation, the homomorphism. A small verified checker (Lean, reusing Tantow et al.'s approach) validates certificates. This decouples proof burden from optimiser heuristics and yields traceability for free (certificates are the trace).
- Semantic theorems of §4.3 (Choice A invariance) are moderate-size mechanisation targets.

---

## 6. Incremental maintenance

- **Counting** (Gupta, Mumick, Subrahmanian, *Maintaining views incrementally*, SIGMOD 1993): exact for non-recursive programs; recursive counting requires derivation-count bookkeeping and can diverge on cycles.
- **DRed** (delete and re-derive; same paper; Staudt & Jarke 1996 variant): over-delete then re-derive; works for recursive Datalog and stratified negation; over-deletion can be costly.
- **B/F** (backward/forward): Motik, Nenov, Piro, Horrocks, *Incremental update of Datalog materialisation: the backward/forward algorithm*, AAAI 2015 — checks alternative derivations backwards before deleting.
- **FBF and recursive counting**: Motik, Nenov, Piro, Horrocks, *Maintenance of Datalog materialisations revisited*, AIJ 269, 2019 — unifying framework, correctness proofs, comparison of DRed/FBF/counting.
- **With equality (owl:sameAs)**: Motik, Nenov, Piro, Horrocks, *Combining rewriting and incremental materialisation maintenance for Datalog programs with equality*, IJCAI 2015 **[V]**.
- **Modular materialisation**: Hu, Motik, Horrocks, *Modular materialisation of Datalog programs*, AIJ 308, 2022 — plug specialised modules (e.g. transitivity) into DRed/FBF.
- **Differential dataflow / DBSP**: McSherry, Murray, Isaacs, Isard, CIDR 2013; Budiu et al., *DBSP: automatic incremental view maintenance for rich query languages*, PVLDB 16, 2023 — algebraic incrementalisation for recursive queries with stratified negation and aggregation.
- Certified: Bonifati, Dumbrava, Arias 2018 (regular Datalog, Coq).
- **Existential rules**: little dedicated theory **[gap]**. Under the **Skolem chase** the program is Datalog with function terms (finite when an acyclicity certificate holds), so DRed/B-F/FBF apply verbatim — a strong argument for the Skolem chase as materialisation back-end even when Choice A is the declared semantics. Under the **restricted chase**, deletions are non-monotone at the level of the computed model (a deleted fact may re-enable a previously blocked trigger, requiring fresh nulls), and insertions may make existing nulls redundant; incremental restricted/core chase requires either recomputation per affected stratum or a core-maintenance step (no established algorithm found **[U]**).

---

## 7. Specification decisions to make (checklist)

| # | Decision | Options | Recommended default | Reference |
|---|---|---|---|---|
| 1 | Reference semantics of positive rules | FO models / certain answers; specific chase result | FO models; answers = certain answers (null-free tuples) | Baget et al. 2011; Deutsch et al. 2008 |
| 2 | Soundness statement | per-answer vs per-internal-structure | per returned answer (allows combined approach) | Lutz et al. 2009 |
| 3 | Completeness status taxonomy | static only / static + dynamic | three statuses: COMPLETE-STATIC(class, certificate), COMPLETE-DYNAMIC(chase/rewriting reached fixpoint on this input), NOT-GUARANTEED(budget hit) | §1.4 |
| 4 | Analyser: which sufficient conditions, in which order | aGRD, WA, JA, SWA, MSA, MFA, RJA, RMFA, linear, guarded, sticky, warded, shy, stratum combination | cheap PTIME checks first (aGRD on refined GRD, WA, JA, linear/guarded/sticky/warded), then MFA/RMFA under time budget; Kiabora-style SCC labelling | Cuenca Grau et al. 2013; Carral et al. 2017; Leclère et al. 2013 |
| 5 | Dependency graph flavour | classical GRD / positive reliances | classical GRD for decidability claims (well-studied), reliances for scheduling | Baget et al. 2011; González et al. 2022 |
| 6 | Default materialisation chase | oblivious / Skolem / restricted / Datalog-first restricted / core | Skolem (deterministic, incremental-friendly, matches MFA certificates); Datalog-first restricted when only RJA/RMFA certify termination | Marnette 2009; Carral et al. 2017 |
| 7 | Algorithm selection | fixed / analyser-driven portfolio | portfolio driven by §2 decision procedure, recorded in the trace | §2 |
| 8 | Notion of rule-set equivalence for the optimiser | logical equivalence / conservative extension / query equivalence | logical equivalence only (as required); conservative-extension transformations (fresh predicates) disallowed or put in a separate, explicitly labelled mode | Fagin et al. 2008; GPS 2011; Carral et al. 2022 |
| 9 | Allowed transformations | see §3.2 | renaming, head-piece split, rule-local core, redundant-rule removal, Σ-relative body/head reduction, adding entailed rules — each with a certificate | §3.2 |
| 10 | Optimiser objective | #rules, largest SCC, #cyclic SCCs, class membership | lexicographic: (all strata in decidable class) > largest SCC size > #cyclic SCCs > #rules; recompute GRD after each step (non-monotone) | §3.3 |
| 11 | Proof obligations for transformations | verified optimiser / certificates + verified checker / paper proofs | schema theorems on paper + Lean; per-step certificates checked by a small verified checker | Tantow et al. 2025; Chase in Lean 2026 |
| 12 | Traceability | keep original only / keep both + mapping / proof translation | keep original Σ, transformed Σ', per-step certificate chain; explanation back-translation to Σ | Ivliev, Krötzsch, Marx 2026 |
| 13 | Negation semantics | constant-guarded stratified (A) / Skolem perfect model (B) / core-model / well-founded / stable | **A** by default; **B** as opt-in mode with "syntax-dependent" label and restricted optimiser | §4.3; Magka et al. 2013; Ellmauthaler et al. 2022 |
| 14 | Stratification test | predicate-level / reliance-based / chain stratification | predicate-level (simple, standard); consider reliance-based later | Küchenmeister et al. 2026 |
| 15 | Aggregation semantics | stratified set-based / monotonic recursive / range semantics over nulls | stratified, set-based, keys and aggregated values null-free (Choice A); monotonic recursive aggregates as later extension | Mumick et al. 1990; Ross & Sagiv 1997; Afrati & Kolaitis 2008 |
| 16 | Nulls in answers | return / hide / mark | never returned as certain answers; optional "model view" output with explicit null marking | standard certain-answer semantics |
| 17 | Normal form of rules internally | original / head-piece normal form / atomic heads with fresh predicates | head-piece normal form (logical equivalence); no atomic-head normalisation with fresh predicates | GPS 2011; Carral et al. 2022 |
| 18 | Incremental maintenance | recompute / DRed / B/F / FBF / counting | FBF (or B/F) over Skolem materialisation per stratum; recompute strata using restricted/core chase | Motik et al. 2015, 2019 |
| 19 | Equality / constants in rules | none / UNA / equality rules | out of scope for v1 (flag); if added, logical equivalence proofs must include equality axioms | Motik et al. IJCAI 2015 |
| 20 | Budget semantics for non-terminating cases | time / depth / #nulls | chase depth (breadth-first rounds) + wall clock; answers sound, status NOT-GUARANTEED | §1.1 |

---

## Unverified points (to check before citing)
- Exact combined complexities marked [U] (piece-wise linear warded, shy, tame, aGRD, uniform containment of Datalog, MFA/MSA with bounded arity).
- JA vs SWA inclusion relation in the acyclicity hierarchy.
- Exact results of Carral et al. KR 2022 per chase variant and per normalisation (one-way vs two-way atomic decomposition).
- Exact analytical-hierarchy level in Carral et al. PODS 2025; bounds in Calautti–Gottlob–Pieris PODS 2015.
- Authors of *The Chase in Lean* (2026), of the CPP 2021 Coq Datalog optimisation paper, and of the Isabelle SAC 2024 paper.
- Precise characterisation of Rocher's "equivalent chase"; scope of magic sets for existential rules (Alviano et al. 2012).
- Which chase variant Calì–Gottlob–Lukasiewicz 2012 use for stratified negation; Nemo's treatment of nulls in aggregates.
- Absence of dedicated incremental restricted/core chase algorithms, and absence of results on "SCC reducibility under logical equivalence" (literature gap, not a verified negative).
