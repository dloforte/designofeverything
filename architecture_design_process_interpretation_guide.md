# Architecture Design Process — Interpretation Guide

Copyright © 2026 Dan Lo Forte. All rights reserved.
Revision: 0.1
Date: 2026-08-02

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

---

## Adding to this Guide

A new entry is added when a constitutional or glossary ambiguity is surfaced and
resolved. The resolution is recorded as a decision (§9); the entry keys itself to
the authoritative section, states the seam and the resolved reading, and cites
the decision. A terse `(see Interpretation Guide §X.Y)` pointer is added to the
authoritative section only where the seam is likely to be hit. Entries are
retained, not deleted, when superseded — a later entry supersedes an earlier one
with a note, mirroring the §9 decision-record discipline.
