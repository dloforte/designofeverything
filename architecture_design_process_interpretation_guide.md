# Architecture Design Process — Interpretation Guide

Copyright © 2026 Dan Lo Forte. All rights reserved.
Spec version: v1.2.3
Last updated: 2026-08-25

## 1. Purpose and status

This document is the **interpretive companion** to the two authoritative L1
documents of this process — the **constitution** and the **glossary**. Those
documents are kept deliberately concise; this one holds the depth that would
bloat them: the **intent** behind a terse rule, the **resolved reading** of an
ambiguity, and **worked examples**.

**It is interpretive, never authoritative.** This Guide introduces **no new
rule**. Where it and the constitution or glossary disagree on substance, the
authoritative document wins and this Guide is corrected (§13-governed). Its job
is to make the authoritative rules *easier to apply correctly*, not to add to
them.

**It is consulted on-demand.** Session start loads the constitution and glossary
(or their minimum load). This Guide is opened **when a reader perceives
ambiguity in a section** — it is not read every session. A constitution or
glossary section with a known interpretive seam carries a terse
`(see Interpretation Guide §X.Y)` pointer back here.

**It grows by governed resolution.** A new entry is added when an ambiguity is
surfaced and resolved — recorded as a decision (§9) and cited in the entry.
Entries are keyed by the authoritative section they interpret. Resolved entries
are retained (the glossary §15A resolved-question lifecycle, applied to
constitutional interpretation).

**It is distinct from the decision log.** Decisions (§9) are the chronological
*amendments*; this Guide is the *current, curated annotation* of the two
authoritative documents. When they conflict, the decision log and the
authoritative text govern; this Guide is reconciled.

## 2. How to use an entry

Each entry names the authoritative **section** it interprets, states the
**question or seam**, gives the **resolved reading** (and *why*), often a
**worked example**, and cites the **governing decision**. Read the authoritative
section first; consult the entry only when its plain reading leaves you
uncertain.

---

## Entries

### §10A.13.6 — reading the decompose-vs-leaf rubric (why there is no number)

**Governing decision:** DEC-1610 (Decision A). **See also:** §9.1
(matrix-readability + top-priority sub-rule), §10A.10 (last responsible moment),
DEC-0750, DEC-0520 (the rubric's origin).

**The question.** The rubric scores DC1–DC7. Earlier drafts applied a fixed
**±20** cutoff. What is that threshold *for*, and how should a project that adds
or drops criteria read it?

**The resolved reading.** The threshold's *purpose* was only ever a
**decisiveness gate** — *is one direction the clear winner (separated from the
alternative), or a near-tie within the noise?* That is a **readability
judgment**, the same one §9.1 already makes for every other matrix in the
process ("the recommendation should be readable from the cells"). So:

- The weights are a **coarse importance ordering** (which criteria count more),
  not an arithmetic to a cutoff. The higher-importance criteria (cohesion +
  single-implementer tractability highest; sub-area independence and
  contract-clarity next; the rest lower) carry more of the read.
- **Clear winner → that verdict.** One direction plainly predominates among the
  important criteria; the other has no important criterion strongly favouring it.
- **Near-tie → judgment call → implementer default.** When the important
  criteria are split, or only low-importance ones discriminate, the structural
  signal is within the noise. A near-tie is *low-stakes* (§9.1 top-priority
  sub-rule / DEC-0750: weak discrimination ⇒ the choice matters less than its
  prominence suggests ⇒ take the most-reversible option). The most-reversible
  option here is **implementer mode** — a too-big leaf can be decomposed later,
  but un-decomposing is costly (§10A.10). Escalate to a §3.2 owner tie-break
  only when the scope is genuinely high-consequence.

**Why no number — the scale-invariance argument.** An absolute cutoff (±20) is a
fraction of the *maximum possible* signed sum, which depends on the criteria
set. §10A.13.6 explicitly invites **augmented criteria** (DC-FN-a…); adding them
changes the weight sum, so the *same* ±20 silently represents *weaker* consensus
(and dropping criteria, *stronger*). The number's precision was therefore false.
"Clear winner vs near-tie" is invariant to the criteria count because it is
*read from the pattern*, not computed against a fixed line.

**Why words, not a percentage.** A normalized fraction (net lean ÷ the weight of
the criteria that took a side — e.g. "a clear majority of the deciding weight
points one way") *is* scale-invariant, but it merely relocates the arbitrariness
from ±20 to some ⅔. The thing being detected ("obvious, or a toss-up?") is
inherently a readability judgment the process already trusts everywhere else, so
a qualitative read is the honest instrument. Keep a fraction only as an optional
auditability aid, never as the rule.

**Worked example.** DC1 (cohesion, high) and DC7 (tractability, high) can point
opposite: a scope with one coherent role (favours leaf) that is nonetheless too
large for a single worker (favours decompose). If the remaining important
criteria (DC2 sub-area independence, DC4 contract-clarity) clearly favour
decompose, decompose. If they are themselves split, it is a near-tie →
implementer (build it; if it proves too big, decompose *then* — little is lost).
This is exactly the case the old ±20 band left ambiguous.

### §10A.1 ↔ §10A.13 — the two "defaults" are one continuum, not a contradiction

**Governing decision:** DEC-1610 (Decision B, seed entry 2). **See also:**
§10A.1, §10A.13.2, §10A.13.6.

**The seam.** §10A.1 says decomposer mode is the default *"for any scope large
enough to admit it."* §10A.13 says *"prefer implementer mode when in doubt."*
Read in isolation, they look like opposite global defaults.

**The resolved reading.** They govern **different points on one continuum**, and
the qualifiers already say so:

- §10A.1's default is conditioned on *"large enough to admit it"* — Mechanism 2
  (§10A.13.2) defines that as *not* single-worker-implementable-now. For a
  **clearly-large** scope (independent sub-areas; no one worker could hold it),
  decompose — that is §10A.1.
- §10A.13's *"prefer implementer when in doubt"* governs the **near-tie** — a
  borderline scope. That is the §10A.13.6 near-tie default (entry above).

So: clearly-large → decompose; clearly-small → leaf; **borderline →
implementer** (the reversible default). There is no scope for which both
instructions fire and disagree — the apparent contradiction is the two ends of
one reading, stated in two places.

### §10A.13.6 / PO-19 — what "at each iteration" means

**Governing decision:** DEC-1610 (Decision B, seed entry 3). **See also:** PO-19,
§3.2 (the design loop).

**The seam.** PO-19 requires explicit mode-selection *"at every iteration,"* but
§3.2's loop iterates by asking the driving question across a scope's unfulfilled
requirements — so is a scope with twelve unfulfilled requirements one iteration
or twelve?

**The resolved reading.** Mode-selection is **per-scope-entry**, not
per-requirement. When a delegate takes up a scope, they decide (and record)
decomposer vs implementer for *that scope* via the rubric. As they work the
scope's requirements, they **re-affirm** that the prior mode-selection still
holds (§10A.13.6's *"or affirms a prior application still holds"*); a fresh
rubric run is needed only when something material changes the scope's shape (a
discovered sub-area, a requirement that enlarges the scope). "At each iteration"
therefore means *"whenever you take up or materially re-shape a scope,"* not
"once per requirement."

### §10A.13.6 — DC1/DC7 audit "the scope under consideration," not necessarily a contract

**Governing decision:** DEC-1610 (Decision B, seed entry 4). **See also:**
§10A.8 (root mode-selection), §10.2 (the contract as the parent's spec).

**The seam.** DC1 asks about *"the contract's role,"* DC7 about *"the contract's
scope"* — but §10A.8 puts the first mode-selection at the **root**, before any
contract artifact exists.

**The resolved reading.** Read "contract" in DC1–DC7 as **"the scope under
consideration."** That scope always exists — at the root it is the project's
overall scope (the root requirement's span); at any node it is the span the
delegate is deciding whether to decompose. The rubric audits a *scope*; a CON
artifact is how a scope's boundary is *recorded once it is delegated*, not a
precondition for auditing whether to decompose it. At the root, run the rubric
against the root scope, record the decomposition decision (§9), then create the
contracts it implies.

### §10A.13.6 — when to recalibrate the rubric

**Governing decision:** DEC-1610 (Decision B, seed entry 5). **See also:**
§10A.13.6 "Rubric stability," "Provenance."

**The seam.** The DC criteria and their importance ordering were developed on a
single project (the *designofeverything* tool). "Rubric stability" permits a
project to revise or replace the rubric (§13-governed), but names no *trigger*
for when recalibration is warranted.

**The resolved reading.** Recalibrate — re-weight the importance ordering, or
add/replace criteria (with PO-14 anti-gaming guardrails) — when either holds:

- **Empirical divergence:** recorded decompose-vs-leaf outcomes repeatedly
  diverge from the rubric's reading (the project keeps overriding the rubric's
  clear-winner verdicts) — a sign the criteria or their ordering no longer fit
  the work.
- **Domain shift:** the constitution is adopted in a materially different domain
  from the one the rubric was calibrated on (e.g. a numerical-engineering
  desktop application vs a design-methodology tool), where the relative
  importance of cohesion, independence, verification-heterogeneity, etc. may
  differ.

Absent a trigger, the default ordering stands. Recalibration is a §13 change
recorded as a decision (§9).

### §13.3 — the pending window: why pull-not-push, and forced-ruling-not-auto-accept

**Governing decision:** DEC-1620 (grounded in RI-0360; PO-46 is the surfaced gap). **See also:** §13.2 (the landed window), §10A.6 (sequencing), §8 / §8.1 (Stage-1 blind; never carries a decision alone), §15.1 (provisional adoption), §26 (owner authority).

**The seam.** §13.2 surfaces a *landed* change's referrers and gates new work through an un-reviewed changed dependency. But a change request spends time **pending** — issued, not yet ruled — and during it a worker several hops downstream can keep building against requirements a known change will soon revise or remove, because §13.2's cascade only reaches them once the change *lands*. §13.3 adds the pending-window provisions; two of its choices are easy to misread.

**Why the forward mark is PULL, not push.** The mark is a *state on the affected artifacts*, met when a dependent reads the artifact or picks up its work — not a notification pushed to a channel. For a context-limited AI-agent delegate this is the reliable form: an agent does not durably watch an inbox, and broadcast notice degrades to noise at scale (notification fatigue). It is the **same graph traversal** as §13.2's referrer-surfacing, fired on the *proposed* rather than the *applied* state — reuse, not a parallel mechanism. The one thing it must do that the landed gate never does is **retract**: a landed change never un-lands, but a pending change can be rejected or modified, so its mark is cleared when the affected set changes.

**Why the impact estimate is a suggestion, never a gate.** It is the change-governance sibling of the commonness rule (§8.1) and the Aim (§3.2 / §8): a citable, fallible input that tilts *scheduling* (how affected work proceeds; how the change is prioritized) but never a candidate's *validity*. "A guess beats no guess" — a fallible estimate lets downstream work avoid wasted effort — but a "low impact" guess is recorded, not asserted, and never proves the absence of impact (the DEC-1410 presence-not-truth parallel). Where the tool can compute the true downstream fan-out from the traceability graph, that count is a sanity-check on the guess, not a replacement for it.

**Why the aging terminal FORCES A RULING, not an auto-accept.** The owner-ruled reading (DEC-1620, OQ-1620-c): a submitted change request was submitted for a reason, so it must be **addressed** — but "addressed" means the affected owner **rules** it (accept / reject / modify), not that silence becomes consent. Apache-style *lazy consensus* (silence → auto-accept) was **rejected**: it resolves by the very *neglect* the backstop exists to prevent, and it erodes the owner's veto-by-inaction. The owner keeps full reject authority (§13, §26); the aging backstop removes only the option of *never deciding*. This preserves "the owner rules the change" as **rules, not rules-by-neglect**.

**Worked example.** A change request at a high level will, on resolution, remove a requirement a subsystem three hops down depends on. Landed-window only: the subsystem keeps building until the change lands and the cascade reaches it — wasted work. Under §13.3: at CR-issue the subsystem's governing requirement is marked pending (forward notice); the issuer's "high impact" estimate + the graph fan-out tell the subsystem to pause or proceed only under a §15.1 provisional assumption; §10A.6 has the common parent sequence the change ahead of the subsystem work; and if the owner sits on the change, its age escalates until the owner is *forced to rule* it — never until it auto-accepts.

### §9.1 — cell correspondence: why a per-cell mark, and what a VERIFIED mark does not buy

**Governing decision:** DEC-1650 (Decision A; grounded in PO-47, which promotes and strengthens PO-32's implementation-dependence limb). **See also:** §9.1 (criterion-grounding sub-rule; matrix readability; the anti-gaming heuristics; the symmetric-correction sub-rule below), §9.3 (where the check gates), §2A Stage 3 (the risk it closes), §3.2 (no silent assumptions; hybrid synthesis), §15.1 (the exposure route), §10A.10.

**The seam.** Every other discipline §9.1 attaches to a matrix polices the matrix's *structure*, its criteria's *provenance*, or the analyst's *motive*. The cell-correspondence sub-rule polices something else — whether a cell is *true* — and two things about it read oddly at first: why demand a mark on **every** cell rather than simply telling analysts to check their facts, and how much a VERIFIED mark actually certifies.

**The intent — marking replaces noticing.** The rule this one strengthens (PO-32) already asked whether a cell's score depended on something not yet investigated. That is a **noticing** check: it requires the analyst to recognize that a claim they hold as settled is in fact unexamined. The recorded failures are exactly the cases where nobody recognized it — the cell read to its author as a settled fact, inside an entry that simultaneously attested that every load-bearing fact had been checked. A per-cell mark removes the need to notice: the question *"did I check this, or infer it?"* is put to every cell unconditionally, instead of being raised only when the analyst already suspects a gap. The dependence test then converts *"which unverified cells matter?"* from a judgment about importance into arithmetic the matrix already supports. This is the same preference the process applies elsewhere — a structural trigger beats a behavioural one, because a reader who has read every authoritative document still fails to recall a discipline at drafting time.

**What bounds the cost.** The obligation to *act* attaches only to **depended-on inferred** cells. Marking is cheap and universal; verification is expensive and confined. An inferred cell that the recommendation does not turn on carries its mark and nothing else — and most cells in most matrices are exactly that. A rule demanding every cell be verified would be strictly more expensive with no compensating gain, and would stall ordinary decisions; that option was considered and rejected.

**The recorded high-risk locus — the candidate you built yourself.** §3.2 invites the analyst to *synthesize a hybrid* from the strongest parts of the options, and warns that a poorly-constructed hybrid can combine weaknesses rather than strengths, adding that *"the matrix is the discipline that prevents this."* That presupposes the cells are true. When a hybrid's entire advantage **is** a cell, the discipline is circular: nothing attacks what the analyst just built. In the recorded cross-project cases, both overturned recommendations were hybrids constructed during the decision's own synthesis pass. Mark those cells with particular care; they are the ones least likely to be examined and most likely to be decisive.

**Worked example.** A matrix compares three options against a criterion "meets the response-time budget." The cell for the recommended option reads `+`, on the strength of a named mechanism the analyst has heard performs well but has not examined. Marked honestly, that cell is **INFERRED**. Dependence test: set it to its worst plausible value (`−`) and re-read — if the recommendation still holds, mark and move on; if the recommendation flips, this cell is **depended on**, and before ratification it must either be checked against the mechanism itself (with the check cited) or recorded as an explicit exposure: *"the recommendation assumes mechanism M meets the budget; not measured; if it does not, option 2 wins"* — routed to the owner per §15.1. Either outcome is acceptable. Silence is not.

**Domain-general readings.** "Checked against the thing it describes" means whatever counts as evidence in the project's domain; only the *form* of the evidence changes.

- **Curriculum design.** A matrix compares three ways to sequence a degree programme. Criterion: *prerequisite coverage*. Cell: *"Option B's second-year module is fully prepared by the existing first-year course X."* That is **INFERRED** until course X's syllabus has been read or its instructor asked. Worst plausible value: X covers half the assumed topics. If B was winning on that cell, read the syllabus — or record the exposure ("B's sequencing assumes X covers topic set T; not confirmed with X's owner") and route it to the programme owner.
- **Hardware.** A matrix compares three enclosure designs. Criterion: *survives the service vibration envelope*. Cell: *"Option C's connector is rated for the envelope."* **INFERRED** until the datasheet, the qualification report, or a shake-table run says so. Worst plausible value: it is rated for a narrower envelope than assumed. Depended-on ⇒ consult the datasheet and cite it, or carry the exposure into the decision's Risks.
- **Software.** A cell asserting that a named mechanism provides a property is **INFERRED** until that mechanism has been examined — and citing a source location that supports the claim while never reading the point at which the claim stops holding is a *shallow* check, not a verified one.

**The staleness hazard — a VERIFIED mark is a frozen fact with an unmarked expiry date.** *(A Guide note, not a rule — see the next paragraph.)* The mark records that a check was performed at a moment in time. **The thing checked can change after the check**: a supplier revises a component, a course is re-written, a standard is amended, a mechanism is reworked. A bare `VERIFIED` says nothing about *when* the check was taken or *what state* it was taken against — so a matrix re-read months later, or a later decision citing this one, can inherit a VERIFIED cell that is no longer true. The badge makes that cell *less* likely to be questioned than an INFERRED one would be, which is the hazard: the mark's whole value is that it signals "someone looked," and that signal does not decay visibly even though its subject does. **Practical advice: make the mark cite what was checked, precisely enough that staleness is detectable** — the document *and its version or date*, the measurement *and when it was taken*, the person asked *and when* — rather than the bare word. A dated citation is self-invalidating in a way a bare mark is not: a reader can see that the thing cited has since moved. This is also what the sub-rule already asks for ("the check is CITABLE"); the point here is that the citation should be specific enough to *age*.

**Why the constitution carries no re-check obligation.** The obvious rule would be *"re-verify a VERIFIED cell when its premises change."* That trigger fails the promotion gate at its second question — *is the check mechanical, performable without spontaneously noticing anything?* It is not: it requires the reader to notice that a premise has moved, which is **exactly the noticing dependency this sub-rule exists to remove**. A rule that reintroduces the failure mode of the rule it extends is worse than no rule, because it would carry constitutional weight while firing only for readers who already suspected the problem. So the clause deliberately stops at the ratification moment, and **no structural trigger covers the staleness case in either direction** — the citation advice above is the mitigation, and it is advice.

**Correction (2026-08-25).** An earlier revision of this paragraph stated that the staleness case *is* handled where a structural trigger already exists: that where the thing a cell asserts a property of is itself a **project artifact**, §13.2's change-propagation gate blocks new authoritative work through a dependency unreviewed since its target changed, with §13.3 giving the pending-window equivalent. **That was wrong, and is corrected here rather than carried.** The §13.2 / DEC-0800 consumption gate is a predicate over *stored dependency-bearing edges*; a §9.1 matrix-cell citation of a project artifact mints **no edge at all**, so the gate has nothing to act on and never fires on such a cell. The project-artifact case is therefore uncovered too — not for the external case's reason (that no governed revision event exists to fire on) but for a different one: a governed revision event **does** exist (§30A), and nothing connects it to the cell. A candidate remedy for that half — minting a dependency-bearing edge from a decision to the artifacts its **depended-on** verified cells cite, bounded by this sub-rule's own dependence test — would be a mechanical trigger of exactly the kind the promotion gate asks for. It is **not adopted here**: it would be a new rule, and this Guide introduces none. The correction is recorded in the adopting project's decision log rather than argued here, per this Guide's non-authoritative role.

The paragraph is left standing with its error named rather than silently rewritten, because the error is itself the strongest available illustration of what this very entry teaches: it asserted an unexamined property of a **named mechanism** — the §13.2 gate — inside the Guide entry for the rule against doing exactly that.

**The honest boundary — presence, not truth.** The sub-rule enforces that the question was asked of every cell and that the depended-on unverified ones were surfaced. It does not certify that a VERIFIED mark is correct: a shallow check can be mistaken for an adequate one, and "worst plausible value" is itself a judgment about plausibility. It reduces the rate at which a decisive false cell reaches ratification silently; it does not eliminate it. This is the same limit §10.4's requirement-contradiction pass carries, and it should be stated rather than oversold.

### §9.1 — symmetric correction: a finding is about the world, not about the option it was found on

**Governing decision:** DEC-1650 (Decision B; grounded in PO-48). **See also:** §9.1 (the anti-gaming heuristics it joins; matrix readability; the cell-correspondence sub-rule above), §9.3 (where the check gates), §13.2 (the same principle one level up in the artifact graph).

**The seam.** Read quickly, the sub-rule can look like it says *"findings against the leading option are suspect."* It says nothing of the kind. It makes no claim about whether a finding is right, and it protects no option. It asks one question of every late correction — **which options does this touch?** — and requires the answer to be written down before the matrix is re-read.

**The intent — the asymmetry is structural, not accidental.** Attention is never distributed evenly across a matrix's options. The recommended option attracts scrutiny, because it is the one anyone has a reason to examine closely; the runner-up is not re-examined at all. So a late correction — from any source — arrives already attached to the leader. Applying it only there penalizes the examined option *for having been examined*, and the resulting flip is nearly undetectable after the fact, because it looks exactly like the discipline working: the cells moved, and the recommendation followed the cells. Nothing in the record shows that the runner-up was simply never looked at. The touches-list is what makes the omission visible.

**Why it needed its own clause.** The existing anti-gaming heuristics guard the *criterion* channel and turn on **motive** — a criterion manufactured to promote a predetermined option. This failure needs no motive at all: an analyst acting in complete good faith, applying a correct finding to the option on which it was correctly found, produces the inversion. A discipline that fires only on bad intent cannot catch it. One level up, §13.2 already holds the analogous principle for the artifact graph — a change is dispositioned against **every** referrer it touches, not only the one that surfaced it. This sub-rule is that principle applied *inside* a matrix.

**Worked example.** An independent review of a decision produces **nine** findings against the recommended option. Exactly **one** turns out to be option-exclusive; the other eight concern a substrate, an assumption, or a cost that every option shares. Applying all nine to the leader alone inverts the recommendation. Applying them with a touches-list — eight to all options, one to the leader — leaves the leader ahead. The recorded near-miss behind this rule is precisely that shape, and its own project's phrase for it is the right one: the recommendation would have inverted *"on an asymmetry of scrutiny rather than of merit."*

**Domain-general readings.**

- **Curriculum design.** A review of the recommended course sequence reports: *"students arrive without the mathematics prerequisite."* Touches-list: is that a property of the **incoming cohort** (then it touches all three sequences, and each is re-scored under it), or of **this sequence's own ordering**, which defers the prerequisite a year later than the rivals do (then it is exclusive — and the entry names the ordering as the property that makes it so)?
- **Hardware.** A design review of the leading enclosure finds that the sealing gasket degrades at the upper service temperature. If that gasket material is common to all three enclosures, the finding touches all three. If the leading option uses it because of its geometry and the others do not, it is exclusive — and the record names the geometry, not merely the conclusion.
- **Software.** A late finding that a mechanism has a throughput ceiling touches every option built on that mechanism, not only the one someone happened to profile.

**Both failure directions.** The rule is symmetric on purpose. **Over-exclusive** — applying a shared finding to one option — is the failure it was written for. **Over-inclusive** is its mirror: declaring that a finding "applies to everything" without saying how each option scores under it dilutes a real finding into nothing. The touches-list is *per option and produces cells*. And per the pre-commitment test in the same family, it is stated **before** the corrected matrix is re-read, not fitted to the outcome.

**The honest boundary — presence, not truth.** The gate enforces that a scope was **stated** and **applied**, not that the scope is **right**. A finding can be mis-scoped in good faith, and a wrong touches-list produces a wrong matrix. The discipline makes the omission governance-visible; it does not certify the judgment.

### §9.1 — Scenario consultation: reading the recommendation against the usage the project already wrote down

**Governing decision:** DEC-1660 (grounded in PO-49; the option ladders and the external survey are RI-0370). **See also:** §9.1 (Scenario-alignment as a criterion — the permissive paragraph this sub-rule attaches to; the cell-correspondence and symmetric-correction sub-rules above), §9.3 (where the check gates), §3.2 (interpretive authority; the T1/T2/T3 guardrail; *Termination*'s enactment coverage), §8 / §3.3 (Stage 1 is Scenario-blind), §15.1 (the residual route), glossary §12A (the Scenario type, its *Lifecycle*, and the non-gating firewall), §26, §29.

**The seam.** Three things about this sub-rule read oddly at first. It obliges a walk of Scenarios the analyst may be certain are irrelevant. It demands as much writing for an affirmative reading as for a negative one. And it produces a verdict — WORSE-ALIGNED — that decides nothing: the recommendation may be ratified carrying it. Each of the three is deliberate, and the reasons are different.

**The intent — the failure is mechanical, not attitudinal.** Before this sub-rule the process handed a decision its Scenarios for one job — grounding a criterion — and had no step that handed them over for the other: testing the recommendation. Nobody ignored them, disbelieved them, or ranked them low; they were routed to one use, and there being no route to the second, they went nowhere else. That is why a permissive paragraph (*"Scenario-alignment **may** appear as a criterion"*) cannot close it: a permissive rule fires only for the analyst who already reached for it, and the analyst who would reach for it is not the one the rule is for. So the walked set is a **flat enumeration resolved structurally** — every Active Scenario, handed over — rather than a relevance judgment. Letting the author decide which Scenarios matter would rebuild the noticing dependency the sub-rule exists to remove, and the record is unambiguous that the noticing does not happen.

**Where this sits in the Generate-Compare-Act pattern (§2A) — and why §2A does not name it.** The sub-rule *fires* at Stage 3's moment, alongside the cell-correspondence sub-rule's verify-or-expose obligation, but the gap it closes belongs to **Stage 2**: a condition the project had already written down was never made a column, so the comparison was complete on its own terms and its terms were short. §2A's Stage-3 list therefore does not point here — it names the cell-correspondence sub-rule against the *silent-assumption-of-trustworthiness* risk that sub-rule matches exactly — and no Stage-2 pointer was added either, because Stage 2's risk is stated as *bias* and a dimension nobody selected engages no motive. §2A's per-stage mitigation lists are illustrative rather than exhaustive, so the absence is not a judgment about where this belongs. (DEC-1670.)

**Why the affirmative reading is generative too.** The obvious economy is to let BETTER-ALIGNED be a bare tick and reserve the writing for misalignments. Active-design-review practice says that is precisely the verdict which fails: reviews are structured to avoid yes/no questions, because a reviewer will give a carelessly considered answer to one, and the published remedy is to replace the question with a **production** instruction — not *"are the exceptions defined?"* but *"write down the exceptions."* The affirmative is also the verdict a **self-reviewing author** — offering their own recommendation, with no second party present — reaches for most. So a better-aligned reading must name the steps carried, the mechanism that carries them, and what the reading depends on. The dependency clause is what makes the reading re-readable later: it is the sentence a revision of the Scenario can be checked against.

**What the rule does not do — the non-gating firewall, in interpretive form.** This is the part most easily misread, so state it flatly. The object of the obligation is the **decision record**, never the candidate. What can fail at §9.3 is *a recommendation offered with an in-scope Scenario unread* — an incomplete record — exactly as a matrix offered with an ungrounded criterion fails today. A candidate is never invalidated by a Scenario, at Stage 1 or anywhere else: hard-requirement rejection is Scenario-blind, a candidate is only better- or worse-**aligned** with a Scenario (never *satisfies* / *violates* it), and where something genuinely gates on a narrative it does so through a **Demonstration**, which passes or fails the **requirement** — the Scenario being only that Demonstration's narrative basis. A WORSE-ALIGNED reading is therefore *information that routes*, not a veto: to a Scenario-alignment criterion if it discriminates among the options, to §3.2 requirement reconsideration if the narrative and a governing requirement genuinely clash, and otherwise to a §15.1 residual for the Scenario's owner, who may accept the misalignment, revise or retire the Scenario, or ask for a different option. Reading a recorded misalignment as a rejection is the single most likely misapplication of this sub-rule, and it is a T2 violation.

**Worked example.** A decision selects among three options for how a project's work is organized, and the project holds three Active Scenarios. The walk produces:

- **SCN-0010 — NOT-ENGAGED-WITH-RATIONALE.** The narrative concerns how a result is reviewed after it is produced; this decision selects how it is produced. The verdict carries that sentence — *"not relevant"* alone would be the rubber-stamp §9.3 already classes as a substance failure.
- **SCN-0020 — BETTER-ALIGNED.** *"The recommended option carries steps 2 and 4 of the narrative — the hand-off and the correction round — because it keeps both inside one owner's scope; the reading depends on the hand-off remaining a single step, which is what the narrative describes today."* Note what makes this a reading rather than an assertion: two named steps, one named mechanism, one named dependency.
- **SCN-0030 — WORSE-ALIGNED.** *"The narrative's third sentence has the work returned once and finished; the recommended option returns it twice, because its check happens after the hand-off rather than before."* This does **not** reject the option. The author asks whether it discriminates: if a rival option carries that sentence and the recommended one does not, it becomes a Scenario-alignment criterion, grounded and scored *aligned*; if the narrative and the governing requirement genuinely clash, it routes to requirement reconsideration; otherwise it is a residual routed to the Scenario's owner. The recommendation may be ratified with the misalignment on the record — and the record is the point.

Later, SCN-0020 is revised so the hand-off becomes two steps. Because the reading named its dependency, glossary §12A *Lifecycle* now brings that reading into the re-evaluation set along with the enacting requirements, and the reading is re-affirmed, revised, or retired rather than silently inherited.

**Domain-general readings.** The instrument is a question — *does the option we are recommending carry the usage we already wrote down?* — and it reads identically in any domain; only what a "step" is changes.

- **Curriculum design.** A matrix compares three ways to sequence a programme. A Scenario narrates *"the learner completes the assessment without re-reading the module."* BETTER-ALIGNED reads: *"the recommended sequence carries that step because the assessment sits immediately after the practical session rather than a term later; the reading depends on the practical session keeping its current length."* WORSE-ALIGNED reads: *"the narrative has the learner assessed once; this sequence assesses twice, because it splits the module across two terms."* Neither verdict passes or fails a sequence — the second one routes.
- **Hardware.** A matrix compares three enclosure designs. A Scenario narrates *"the operator swaps the cartridge one-handed, without setting the unit down."* BETTER-ALIGNED reads: *"the recommended design carries the swap and the re-seat, by the latch geometry it specifies; the reading depends on the cartridge staying within its present mass."* WORSE-ALIGNED reads: *"the narrative has the swap done one-handed; this design requires the cover to be held open, so the second hand is occupied."* That is recorded, and routed — it does not disqualify the design, which may still be recommended for reasons the matrix does record.

**What the precedent does and does not support — recorded because it is easy to overclaim.** The **act** — reading a candidate against an authored usage narrative — has precedent in more than one tradition: a systems-engineering handbook recommends walking an operational scenario as a plausibility screen on a candidate, and the misuse-case literature suggests paper-testing chosen defences against the narrated misuse. Both are **optional heuristics**. Making the act an **obligation**, with an exhaustive walk and a recorded verdict per Scenario, has **no precedent found anywhere in the surveyed literature** — that is the sharper claim, and the one to make. Two further honesties. First, the closest structural precedent, **SAAM** (1994), matches this sub-rule on three axes at once — it walks every scenario with no cutoff, it does so explicitly to compare candidate options, and its per-scenario verdict is two-valued plus a generative obligation — but its scenario results are **decisive**: it favours the architecture with the fewest scenario conflicts and produces a ranking. SAAM uses scenarios as the selection instrument, so it is **not** precedent for the non-gating stance taken here; the non-gating stance comes from this process's own §12A firewall, not from the family. Second, the design-rationale ancestry named in constitution §1.2 does not supply this discipline either — it is not a recovered instrument, it is a new obligation.

**The evidence is domain-narrow even though the instrument is domain-general.** Every source surveyed for this sub-rule is software-architecture-specific or systems-engineering-specific. The wording above carries no domain vocabulary and the curriculum and hardware readings show the question transferring intact — but a reader adopting this process for a non-software project should know that the *empirical* support for the instrument's yield was gathered in one family of projects, and treat its cost and benefit claims accordingly.

**The size threshold — what crossing 10 does, and what it does not.** At **10** Active Scenarios the full walk stops being assumed affordable, and the crossing **obliges a §9 decision** scoping the consultation. It does **not** silently narrow the walk, and it engages no prioritization scheme of its own: an automatic conversion at a count would hand the author back the selection discretion the sub-rule removes, and would do so without any owner ever ruling on it. Until that decision is ruled, the full walk stands. Note also what that decision's mandate covers: the **total effort per decision** — the size of the set *and* the cost of each reading — not the set size alone, since the two multiply. And the number itself is honest about its status: it is **bracketed, not derived** (above the size a full walk is known to be affordable here; materially below the size at which an industrial method needed a convened multi-day team to walk one), no source measures the per-decision, single-author cost of a scenario reading, and **8 or 12 would be about as defensible**. What the recorded number buys is a forced decision at a legible point, which a right-but-unrecorded intuition does not.

**The two coverage holes, named.** Both are limits of the instrument, recorded so a reader inherits them with the rule rather than discovering them.

- **A usage nobody narrated surfaces nothing.** The walk is only as good as the Scenarios the project actually authored, and this sub-rule adds no obligation to author any. The only thing in the process pushing on the Scenario set's completeness is §3.2 *Termination*'s enactment coverage, and it pushes from the other end and binds only at design-done.
- **An irreversibly foreclosed *deferred* usage falls outside an Active-only walk.** The walked set is **Active** Scenarios only, because walking the parked set manufactures misalignment readings — and routed residuals — for work nobody intends to do. The price is real: a decision that irreversibly forecloses a usage narrated by a **Deferred** Scenario is not surfaced by the walk, and §3.2 *Termination*'s re-disposition of that Scenario arrives after the foreclosing decision, by which point the only honest disposition left is to retire it. This is a named limit with a recorded review trigger, not an oversight.

**The honest boundary — presence, not truth.** Like the requirement-consistency pass and the two cell-level sub-rules, this one enforces that the question was **asked of every in-scope Scenario** and that the answer was **recorded**. It does not certify that the reading is correct: an author can read a narrative against an option and conclude, in good faith and wrongly, that the option carries it. And a walk that fires reliably while never surfacing a misalignment that routes anywhere would be an instrument pointing nowhere — worth noticing if the record ever reads that way, rather than treating the discipline's own regularity as evidence that it works.

### §30A — the unit of classification is the limb, not the artifact

**Governing decision:** DEC-1710. **See also:** §30A (revision / replacement), §30A.1 (the classification-authority concern this compounds), §13.1 (who classifies), §13.2 (per-referrer disposition, which is what a classification proposes), §9 (superseded / superseding semantics).

**The seam.** §30A states its test over a **whole artifact**: a *revision* "preserves the essence of the artifact," a *replacement* means "the result is, in essence, a different artifact." Read literally, that asks for **one verdict per change**. But an artifact is rarely a single proposition, and a change to it rarely touches every part. A ruling that settles three things at once can be **refined on two of them and reversed on the third by the same change** — and a reader obliged to produce one verdict will produce it by weighing, which means the reversed part is either outvoted by the refined parts or made to outvote them. Both outcomes lose information the record needed.

**The intent — the test is sound; the unit it is applied to is what has to be chosen.** Nothing here weakens §30A. Its question — *is this still the same artifact?* — is exactly right. What DEC-1710 records is that the question must be asked of **each thing the artifact separately decided**, wherever those things can move independently of one another. Where an artifact decides one thing, the limb and the artifact coincide and nothing changes. Where it decides several, a whole-artifact verdict is an average, and an average of *refinement* and *reversal* is a number that describes neither.

**How this reconciles two classifications that look opposed.** A corpus applying §30A will accumulate precedents that appear to conflict: one holding that reversing part of a decision is a **revision**, because it carries the decision further in its own adopted direction; another holding that reversing a decision's stated rule is **replacement** territory, because that sentence was the whole of what the decision decided. Read as rival tests they are irreconcilable, and a later classifier must distinguish one away. Read limb-wise they are **answers to different questions about different limbs**, and both survive: the first asks *does this carry the limb further in its own direction?*, the second asks *does this invert the limb's stated rule?* Applied to one change, both can fire — on different limbs — and both be right.

**Worked example.** A recorded ruling settles where a tool finds a governing document. It has three limbs: **(1)** a known default path so a bare run works; **(2)** an environment-variable override; **(3)** *"error clearly if the files are absent — do not silently proceed."* A proposal replaces the lookup with a multi-tier resolver that searches several locations in a stated order.

- On limbs (1) and (2) the resolver is a strict **superset**: the override becomes its highest tier, the default its lowest, and tiers are inserted between. That strictly widens the set of situations in which a bare run works, which carries the ruling further in its own adopted direction — **revision**.
- On limb (3) the resolver's own behaviour is the **opposite** of the rule: it omits a document it cannot find rather than raising. That inverts the stated sentence — **replacement territory**.

The whole-artifact readings both fail, and fail in instructive ways. *"It is a superset, therefore a revision"* is true of the limbs it weighs and **silent on the one it does not** — a superset relation is a claim about a set of behaviours, and it can only settle a question about essence if every limb is inside the set being extended. *"Reverse anything stated, therefore a replacement"* is the cautious-looking error: it forces a new identifier and a full referrer re-evaluation for a change that is, on two limbs of three, a refinement. **Over-classifying is not free** — it spends the referrer attention that §13.2's machinery exists to conserve.

The useful output is neither verdict but the **pair**: the change may proceed as a revision **provided** the inverted limb is not carried with it. That is a sentence a whole-artifact verdict cannot express.

**Domain-general readings.** The instrument is a question — *which of the things this artifact decided does this change touch, and in which direction?* — and it reads identically in any domain.

- **Curriculum design.** An approved module specification fixes the assessment's **format**, its **weighting**, and a rule that *"no assessment may be re-sat more than once."* A proposal changes the format and, in passing, permits unlimited re-sits. Format is a revision; the re-sit rule is inverted, and the record should say so separately rather than approving "a change to the assessment."
- **Hardware.** A released part drawing specifies a **material**, a **tolerance**, and a note that *"this surface shall not be painted."* A change to a compatible material with a tighter tolerance is a revision on both; a change permitting a coating inverts the note. One drawing, two classes.
- **Software.** An interface decision fixes a transport, a retry policy, and a rule that a request is never silently dropped. Swapping the transport for a superset is a revision; making the failure path drop rather than surface is not.

**What this does NOT license — the abuse to refuse.** Limb-wise classification is not permission to **subdivide an artifact until every part is small enough to look like a revision**. The limbs must be ones the artifact itself decided **separately** — recognizable in its own recorded text, not carved out of it after the fact to reach a preferred class. A limb invented at classification time to make a reversal look local is the §30A analogue of manufacturing a criterion to move a matrix, and it fails for the same reason. The test for a genuine limb is whether the artifact would still have decided it had the others gone the other way.

**The relationship to §30A.1's recorded concern.** §30A.1 notes that an artifact's owner and its referrers may legitimately disagree about a change's class. Limb-wise classification **narrows** that disagreement without dissolving it: much apparent disagreement is a referrer weighting one limb that the owner weighted differently, and separating the limbs lets each side see which limb the other is answering about. It does not settle who is right — §13.2's per-referrer disposition still does that — but it changes the dispute from a clash of verdicts into a comparison of scopes, which is a smaller thing to resolve.

**The honest boundary — presence, not truth.** Like the disciplines it sits beside, this reading enforces that the limbs were **enumerated** and each **classified**, not that the enumeration is complete or the classifications correct. An author can miss a limb, or split one that was really indivisible, in perfect good faith. What the reading buys is that a change carrying both a refinement and a reversal can no longer be recorded as though it carried only one — the omission becomes governance-visible. It does not certify the judgment.

---

## Adding to this Guide

A new entry is added when a constitutional or glossary ambiguity is surfaced and
resolved. The resolution is recorded as a decision (§9); the entry keys itself to
the authoritative section, states the seam and the resolved reading, and cites
the decision. A terse `(see Interpretation Guide §X.Y)` pointer is added to the
authoritative section only where the seam is likely to be hit. Entries are
retained, not deleted, when superseded — a later entry supersedes an earlier one
with a note, mirroring the §9 decision-record discipline.
