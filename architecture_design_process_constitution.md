# Requirements-Constrained Architecture Design Process

## 1. Purpose

This document defines a formal process for deriving, evaluating, justifying, and governing software architecture from requirements.

The purpose of the process is to reduce reliance on intuition alone by making architectural reasoning explicit, traceable, repeatable, reviewable, and justifiable.

The process does not assume that requirements uniquely determine a single correct architecture. Instead, it treats architecture as a requirements-constrained decision process in which valid candidate architectures are generated, compared, selected, recorded, and governed.

**Operational note (added 2026-05-24 per DEC-0690 / PO-31).** This document is authoritative *reference*; the disciplines encoded herein are made actively-checked at agent draft-time via an operational *cadence specifier* maintained per project (canonical name: `tool/AGENT_PRIMING.md`). Agent sessions working with this constitution shall load the project's priming document at session start, before any artifact-touching work. The priming document specifies *when* each constitutional discipline is to be actively checked during artifact generation; this document specifies *what* the disciplines are and *why* they apply. If the priming document and this document disagree on substance, this document is authoritative and the priming document is updated to reconcile. The priming document is a cadence specifier, not a re-statement; it must not duplicate the substance of this document — it points into it. Projects adopting this constitution maintain their own per-project priming document; the canonical example in the *designofeverything* project is `tool/AGENT_PRIMING.md`.

### 1.1 Motivation

The process exists to address three concerns that recur in real projects:

**Drift prevention.** In a sufficiently large project, individual changes accumulate into deviations from the original intent. The process resists this by requiring every authoritative artifact — requirement, decision, contract — to be traceable back to the requirements that justify its existence, so that drift can be detected and reasoned about rather than silently absorbed.

**Bounded scope of change.** Developers and AI assistants given a task in a complex system may change more than the task requires, creating unintended interdependencies between modules or revising interfaces and objectives that other parts of the project depend on. The process responds with the contract concept: a contract proscribes the *shell* — the boundaries — of a design space. Inside the boundaries, the delegate has full freedom. The boundaries themselves are owned and may not be altered without governed change.

**Freedom within boundaries.** The contract concept is intended to give delegates real authority over implementation. Compare a skyscraper-floor designer who is given fixed structural supports and electrical, mechanical, and plumbing connection points: within those, the floor designer chooses freely. The process aims to make that division of authority explicit at every level of decomposition.

These motivations apply beyond software. The process is intended to be amenable to the design of anything where requirements, contracts, and bounded scopes are useful concepts.

### 1.2 Intellectual Provenance

The process described in this document is not novel in spirit; several established methodologies cover adjacent ground. Two are worth acknowledging explicitly because their structures are visible in the process here:

**IBIS — Issue-Based Information System.** Introduced by Horst Rittel in the 1970s for collaborative work on *wicked problems*, IBIS models a design dialogue as a graph of *issues*, *positions* (proposed responses to issues), and *arguments* (supporting or opposing positions). It is the direct intellectual ancestor of this process's open-question, clarification, and decision lifecycle (glossary §15A, §9, §20). The Compendium tool (and its continuation as CompendiumNG) operationalized IBIS in software form.

**QOC — Questions, Options, Criteria (Design Space Analysis).** Developed in the early 1990s by MacLean, Young, Bellotti, and Moran. Represents reasoning as a graph of *questions* about the design, *options* responding to each question, and *criteria* used to evaluate options against one another. QOC explicitly names the *design space* — the set of options considered for a question — a term this document arrived at independently (glossary §11A) before the QOC parallel was identified.

This process draws on the IBIS and QOC traditions and extends them with explicit machinery for contracts (§10), catalogs of mechanisms (§5), change governance (§13), polymorphic references, forward migration, and AI-first multi-user operation. The intent is not to replace these methodologies but to integrate their insights into a tool-mediated system that meets modern operational expectations.

## 2. Core Principle

Architecture is a governed chain of reasoning from requirements to contracts.

A valid architectural decision should be traceable through the following chain:

```text
Requirement → Capability → Candidate Mechanisms → Tradeoffs → Decision → Contract → Implementation Authority
```

The selected architecture does not need to be the only architecture that could work. It must be a justified architecture under the stated requirements, assumptions, constraints, and priorities.

## 2A. The Generate-Compare-Act Pattern

Many process steps in this constitution share a common three-stage structure:

```text
1. Generate — enumerate candidate options, approaches, or
              possibilities for the matter at hand.

2. Compare  — evaluate the candidates against the dimensions
              that matter for the current context.

3. Act      — select / decide / commit / record, based on the
              comparison + recorded rationale.
```

The pattern applies to decisions, investigations, contract drafting, change request evaluation, and other process steps where candidates need to be honestly surveyed before one is acted upon. Sections that apply the pattern do so in their own specific forms; this section names the unified discipline + the risks each stage addresses.

**Stage 1 (Generate)** addresses the **coverage risk**: options missed; only the obvious or familiar candidates considered. Mitigations established elsewhere in this constitution: §3.2 *category-first generation*; §5 catalog consultation + §5.3 bootstrapping; §15B Research Investigations (Survey kind) for unfamiliar option spaces.

**Stage 2 (Compare)** addresses the **bias risk**: implementation-bias in scoring (a poor-implementation pick unfairly representing an option); analyst-bias in criterion framing (criteria invented to favor a predetermined option); criterion-source bias (ungrounded analyst judgment treated as decision criterion). Mitigations: PO-14 anti-gaming; PO-17 per-decision criterion-relevance; PO-30 criterion-grounding (constitution §9.1 sub-rule per DEC-0690); PO-32 matrix-row abstraction homogeneity + RI-informed scoring; §9.2 swing-weight when criteria differ materially in importance.

**Stage 3 (Act)** addresses the **completeness risk**: silent assumption that the comparison's recommendation is trustworthy; matrix-recommendation gap not surfaced; rationale not recorded for future revisitation; review triggers not stated. Mitigations: §9.3 completeness check; LB · §9.3 verdict vocabulary; §3.2 tie-breaking questions when the matrix doesn't discriminate; §3.2 insulation by abstraction when the act's commitment may need to be revisited.

Application specifics live in the sections that apply the pattern (§3.2's three exits SPLIT/DECIDE/ACT; §5's catalog; §9 decision records; §10A.2 decomposition workflow; §10A.14 approach selection; §15B research investigations). When opening a new process step that involves selecting among candidates, the designer should:

```text
(a) Recognize whether the three-stage pattern applies (trivial-
    derivation per §10.4 collapses to obvious-Stage-3 with Stage 1
    + Stage 2 implicit when alternatives are obvious or non-
    existent); and

(b) Apply the relevant stage-specific mitigations from this
    section's references.
```

**Nested recursion**: when Stage 2 scoring for some candidate option depends on un-investigated implementation details, open a brainstorming RI (Stage 1 of a nested three-stage cycle) followed by a comparative RI (Stage 2 of the nested cycle); both feed back into the parent step's Stage 2. The pattern is recursive — nested three-stage cycles handle finer-grained sub-investigations that the parent stage cannot honestly resolve without them. Per PO-32 + PO-33.

**Anti-gaming**: this pattern is NOT a license to add process ceremony to every micro-choice. For trivial-derivation cases per §10.4 (no plausible alternatives; choice obvious), the pattern collapses to "obvious Stage 3" with Stages 1 + 2 implicit. The pattern applies when there are genuinely multiple options + the choice has non-trivial consequences.

**Scope**: open-ended. The pattern is not pre-limited to decision-making processes. It applies to any process where multiple candidates need to be generated + compared + acted upon. Sections beyond those listed above may also apply it; future POs may identify additional applications.

*Added 2026-05-26 per DEC-0740 + PO-33; specific applications throughout this constitution cross-reference this section.*

## 3. Requirements Tree

The process begins with a requirements tree.

The requirements tree decomposes high-level project objectives into more specific requirements. Requirements may be functional, non-functional, operational, regulatory, organizational, or strategic.

Each requirement should have a unique identifier, owner, status, version, and relationship to other requirements.

Requirements may include both hard constraints and optimization objectives.

### 3.1 Starting a Project

A new project is started by identifying the external authority and recording the root-level requirement(s) authored by that authority.

The first action in the process is therefore:

```text
1. Identify the external authority for this project
   (project owner, customer, mission statement, regulation, etc.).
2. Elicit the root-level requirement(s) from that authority.
3. Record each root-level requirement with its origin and owner.
```

Subsequent requirements are derived from root-level requirements as the project proceeds. Root-level requirements may also be added later if new external authority is discovered; root-level does not mean earliest-known. (See glossary §5.)

### 3.2 Driving the Design Forward

Once root-level requirements exist, the design progresses through an iterative loop. Each iteration begins with a single driving question; if more work remains, the iteration takes one of three exits at each unfulfilled requirement.

**The driving question.** Each iteration starts by asking:

```text
Are all requirements fulfilled?
```

The question is evaluated against the design and all available artifacts. The evaluation is recorded as a lightweight check: at minimum, that it was performed, the number of requirements considered, and the number not yet fulfilled. The check produces an answer (yes / no), a rationale, and links to whatever the evaluation triggered.

If the answer is *yes*, the design (or sub-design under a contract) is complete; see *Termination* below. If *no*, the loop continues.

**Two modes — implementer and decomposer.** At each iteration, the designer of the current scope is in one of two modes:

- **Implementer mode** — the current scope is small enough and well-understood enough that a single worker (human or AI) is producing the design and implementation directly. The three exits below — SPLIT, DECIDE, ACT — operate within this mode.
- **Decomposer mode** — the current scope is large or admits independent sub-areas. The work is to partition the scope into bounded sub-contracts and delegate each to a separate worker. The detailed workflow is **§10A *Decomposition and Delegation Workflow*** — the *primary mode* of this process for any scope large enough to admit it.

The two modes interleave in any non-trivial project: upper levels in decomposer mode, lower levels in implementer mode. The choice of mode at any iteration is itself an instance of DECIDE, weighted toward the *independent-development* criterion of §7. The remainder of this section (the three exits and the named heuristics) is the implementer-mode toolkit. The decomposer-mode toolkit is §10A.

**Three exits per node.** For each unfulfilled requirement, choose one of three exits. The exits are not strictly ordered in priority — they trigger on different conditions of the requirement's current state. The designer's first task is to triage: which condition does this requirement currently match?

```text
Triage questions for choosing the exit:

  Q1. Is the requirement specific enough to be acted on directly
      — does an existing solution map onto it, can a contract
      be drawn around it, can a verification be defined for it?
      If YES -> ACT.

  Q2. Are there multiple plausible solutions worth weighing —
      does the catalog or the design space contain alternatives
      that satisfy the requirement, and does the choice among
      them have consequences worth recording?
      If YES -> DECIDE.

  Q3. Does the requirement bundle multiple obligations, or
      contain ambiguity in scope or intent, or rest on an
      uncertainty about what the project even needs?
      If YES -> SPLIT.
```

A rebuttable preference applies: when the same requirement could plausibly be ACT (existing fitting solution available) or DECIDE (multiple options to weigh), prefer ACT — *reuse beats reinvention* (see §10A.9 on shared modules). The preference is rebutted when the apparent fit is superficial or when a comparison would surface useful information.

For each unfulfilled requirement, the chosen exit is one of the following:

```text
SPLIT   — the requirement bundles multiple obligations or contains
          a scope or intent ambiguity. Resolve by splitting into
          derived child requirements, raising an open question
          (glossary §15A) for clarification, or recording an
          assumption (glossary §15) and routing it for owner action.

DECIDE  — the requirement is specific enough to consider mechanisms
          or candidate architectures. Consult the catalog (§5),
          generate candidates, evaluate against requirements and
          tradeoffs (§8), and record a decision (§9). Decisions
          typically yield one or more contracts (§10) and/or new
          derived requirements.

ACT     — the requirement is actionable directly: select an
          available solution from the catalog, draw a contract, or
          hand off to an implementer. Define the verification
          method (glossary §2).
```

**Rejection-driven refinement.** When the right exit is unclear, a useful heuristic is to propose any plausible solution that could fulfill the requirement (treat as ACT even when underspecified). The owner or relevant authority will usually reject the proposal because something specific is missing — and the rejection itself surfaces the missing constraint or specificity, which drives the next SPLIT or DECIDE. The heuristic uses rejection as a generator of design information rather than a setback.

**DECIDE applies the Generate-Compare-Act pattern (§2A).** Category-first generation + hybrid synthesis (below) + tie-breaking questions (below) are Stage 1 + Stage 1.5 + Stage 2.5 variants; the §9.1 option-criterion matrix is Stage 2; §9.3 completeness check + parent acceptance is Stage 3. See §2A for the unified discipline + per-stage risk mitigations.

**Category-first generation.** When the candidate space is large or unfamiliar, generate candidates at the *category* or *class* level first (file-tree approaches, graph databases, relational with polymorphic associations, RDF triplestores, etc.) rather than jumping to specific products or implementations. Evaluate categories against the most-constraining requirements; only after a category is selected, drill down to specific products or implementations within it. This is complementary to rejection-driven refinement: where rejection-driven refinement uses a rejected proposal to surface missing constraints, category-first generation reduces evaluation cost by eliminating whole classes of options before investing in product-specific detail. Per §2A, this is a Stage 1 (Generate) coverage-risk mitigation.

**Research investigation handoff.** Research investigations (glossary §15B) may be opened from any point in the design — DECIDE evaluation when the catalog lacks needed information, an open question that requires research to resolve, an assumption that needs validation, a contract definition that needs further information, or as a standalone task. Research investigations are *not* exclusive to DECIDE; they are a general mechanism for gathering information whenever the design needs it. The artifact that triggered the investigation waits on the findings, or proceeds under a recorded assumption (glossary §15) if waiting would block productive work. Findings typically update or create catalog entries (constitution §5.2) and feed back into the triggering artifact. Investigations are themselves of two kinds (glossary §15B.1): *survey investigations* search what already exists, while *constructive investigations* develop something new. Per §2A: RIs are the constitutional vehicle for deeper Stage 1 (brainstorming RIs surveying candidates) + Stage 2 (comparative RIs investigating candidate properties); findings feed back into the triggering step's stages.

**Requirement reconsideration.** At any point in the loop, the project may discover that holding a requirement as currently stated makes the design infeasible, sub-optimal, or excessively expensive given what has been learned. The response in such cases is not always "fulfill the requirement at any cost" — it may be to **revise the requirement**. Revision is itself a decision with tradeoffs: *what is given up by holding the requirement as it stands* versus *what is gained by relaxing, redefining, narrowing, or otherwise altering it*. The structured option-criterion evaluation (§9.1) is the right vehicle for the tradeoff analysis. The change itself is governed by §13 (change classification per §30A — a revision if the essence of the requirement is preserved with adjusted bounds, a replacement if the new requirement is materially different). The requirement's owner approves the change.

Requirement reconsideration is not an escape hatch from process discipline; it is a recognition that requirements are themselves design artifacts, subject to revision when better information arrives during the loop's iterations. *Holding a requirement is itself a tradeoff* — even when the requirement is simply restated unchanged, that restatement is an implicit decision to keep paying the cost the requirement imposes. Making that implicit decision explicit when the cost has grown is the move this paragraph names.

**Gap analysis.** When evaluating an *existing solution* — a tool, mechanism, framework, or third-party offering — as a candidate to satisfy some or all of the project's requirements, perform a **gap analysis**: enumerate the requirements relevant to the solution and, for each, determine whether the solution satisfies it as-is, partially satisfies it (with what remaining gap), or does not satisfy it at all.

The gap analysis is the requirements-as-criteria specialization of the option-criterion evaluation (§9.1): the *option* is the existing solution being considered, and the *criteria* are the project's requirements. The output is *the gap* — the set of requirements not met by the solution, along with the cost characterization of closing each one.

```text
Steps:
1. Identify the existing solution under consideration.
2. List the requirements relevant to the scope of consideration
   (often a subset; rarely the entire requirement set is relevant
   to a single existing solution).
3. For each requirement, assess one of:
     - SATISFIED — the solution meets the requirement as-is
     - PARTIAL  — the solution meets part; record what's missing
                  and the cost to close it
     - UNMET    — the solution does not meet the requirement;
                  record cost to add it (extension, integration,
                  or workaround)
     - N/A      — the requirement is outside the solution's scope
                  and is expected to be met elsewhere
4. Sum the gap: total cost of closing all PARTIAL and UNMET items
   under the option.
5. Compare against alternatives — most importantly, against
   building (or against another existing solution).
6. Decide: adopt-and-extend, build, or relax requirements (if
   specific gaps point at requirements that may be over-strict —
   see Requirement reconsideration above).
```

Gap analysis is the disciplined alternative to "this looks similar enough" or "let's build, surely" judgment. The constitution does not require it for every option considered — it is a tool that becomes load-bearing when the option being considered is substantial enough that getting the build/adopt decision wrong would be costly. When applied, the gap analysis output becomes part of the relevant decision record (§9), informing the option-criterion evaluation.

**Hybrid synthesis.** When the option-criterion matrix (§9.1) leaves two or more contenders that are strong on *different* dimensions, ask — before invoking tie-breaking questions — whether a **hybrid** candidate can be constructed that takes the best of each contender. Examine the matrix cell by cell: where contender X scores well and Y poorly, take X's approach for that dimension; where Y scores well and X poorly, take Y's approach; where both score equally well, pick whichever fits the project's constraints better.

A hybrid is not always available. Some contender approaches are *mutually exclusive* — a system cannot be both service-oriented and monolithic for the same component, for example. But often the contenders differ on multiple dimensions and only some of those dimensions are interlocked. The hybrid candidate inhabits the dimensions that don't interlock and takes the better of each.

When a viable hybrid emerges, **evaluate it as a new candidate in the matrix**. If it dominates the original contenders — wins on every cell where one of them won, ties or wins everywhere else — it becomes the selected option, and tie-breaking questions are not needed. If it merely matches the contenders on different cells without dominating, the original tie-breaking dimensions still apply and tie-breaking questions are the next move.

Apply with care: a poorly-constructed hybrid can combine *weaknesses* rather than strengths if the synthesis is sloppy. The matrix is the discipline that prevents this — every hybrid claim is checkable cell-by-cell against the criteria, and the hybrid must be added to the matrix as a real evaluated candidate, not asserted as superior.

Hybrid synthesis is complementary to the other techniques in this section. Category-first generation produces the initial candidate set; hybrid synthesis combines candidates within or across categories when their strengths are partially separable; tie-breaking questions handle the case where synthesis is not possible or not sufficient; insulation by abstraction preserves optionality after a decision is made. Hybrid synthesis comes *before* tie-breaking in the workflow — it is the move that may obviate the need for tie-breaking entirely.

**Tie-breaking questions.** An option-criterion evaluation (§9.1) sometimes does not produce a dominant option even after hybrid synthesis (above) has been attempted. Two or more options survive with comparable overall strengths, each strong on different dimensions. When this happens, the next move is **not** for the analyst to implicitly choose — it is to formulate **tie-breaking questions** for the decision's owner, targeted at the specific dimensions where the surviving options differ, and ask which dimension matters most for the project's objectives.

```text
A tie-breaking question is:
- short and targeted at one differentiating dimension;
- expressed in terms of project objectives, not implementation
  preferences;
- paired with a concrete consequence ("if X matters more, we
  prefer option A; if Y matters more, we prefer option B").
```

Common tie-breaking dimensions include long-term strategic commitments (technology platform, ecosystem reach), risk tolerance, deployment preferences, team or operational expertise, and the relative weight of distinct project goals. The owner's answers to the tie-breaking questions become part of the decision record's reasoning so that the basis for the tie-breaker is preserved alongside the matrix.

Tie-breaking questions are not abdication of analysis. They are a recognition that some calls genuinely depend on owner judgment about priorities the matrix cannot encode — and that surfacing those judgments explicitly is more honest than letting the analyst's defaults stand in for them silently.

**Insulation by abstraction.** When a decision has been made — an option-criterion evaluation completed, tie-breaking questions answered, a choice settled with reasonable confidence — but rejected alternatives remain *viable* and the choice has long-term consequences, the *tool's architecture* can be designed to **insulate** dependent components from the chosen option. A *boundary abstraction* is placed between the chosen option and the components that depend on it. Dependents interact with the abstraction's interface rather than with the option's idioms directly.

The benefit is **reversibility**. If the chosen option's risks later materialize, or a substantially-better alternative emerges, or the requirements shift, the option can be replaced by writing a new implementation against the same abstraction. Dependent components do not change.

The cost is real but bounded:

```text
- Design work to shape the abstraction's interface to the
  project's domain rather than to a specific option's idioms.
- A layer of indirection between dependents and the option.
- Discipline to prevent option-specific features from leaking
  through the abstraction.
- Some abstraction-defying capabilities of the chosen option
  may be hidden, duplicated, or surfaced only via escape hatches.
```

This technique is most useful when (a) multiple options remain viable after the option-criterion matrix and tie-breaking, (b) the long-term consequences of the choice are uncertain, or (c) the requirements may evolve in ways that affect the choice. It is **not** a default — premature abstraction has its own costs, and an interface shaped before the project understands its real needs is usually wrong. Apply when the value of preserving optionality is concrete (a real second option exists; a real reason to prefer flexibility exists), not as a default architectural posture.

The technique generalizes the contract concept of §10 — a contract is itself a kind of abstraction, defining the shell within which a delegate has freedom. Insulation by abstraction applies the same idea specifically at decision boundaries where the decision could plausibly need to be revisited.

**AI agents must not make assumptions.** AI agents operating under this process face the same epistemic discipline as human delegates: when a decision or specification is needed, the agent **must not** fill gaps with implicit assumptions.

```text
- If existing artifacts (constitution, glossary, requirements,
  decisions, contracts, etc.) provide a definitive answer, the
  agent uses them and cites the basis.
- If they do not, the agent raises a question — an open question
  per §15A, a clarification per §9, or a change request per §13 —
  to the appropriate authority (the contract owner, the project
  owner, or the relevant artifact's owner).
- The agent records an explicit assumption per glossary §15 only
  when waiting for owner action would block productive work, and
  the assumption remains unverified until the owner accepts it
  per §15.1.
```

What is forbidden is *silent assumption* — proceeding as if a decision had been made when it has not. AI agents are particularly prone to this because they can reasonably infer "what the user probably wants" from training-data priors; this process explicitly prohibits substituting inference-from-priors for design discipline. **Only requirements and the artifacts that derive from them have authority to drive the design.**

This rule applies equally to humans, but is stated explicitly here because the inference-from-priors failure mode is more available to AI agents.

**Recursion at delegated scopes.** When DECIDE (or decomposer mode) produces a contract (§10), the contract's **delegate** (glossary §27A) treats their assigned shell as a fresh root and runs this loop inside it — including the choice between implementer and decomposer modes for their own scope. The contract owner retains authority over the shell; the delegate has freedom inside the shell but cannot alter it. The full handoff and handback workflow connecting parent to delegate iterations is **§10A *Decomposition and Delegation Workflow***.

**Choosing which requirement to address next.** Among unfulfilled requirements, prefer the one whose **design space** (glossary §11A) is smallest — the most-constrained requirement, with the fewest viable solutions. Committing to a wide-design-space requirement before a constrained one risks eliminating the overlap with the constrained requirement's viable solutions. This is a feasibility-risk heuristic; it parallels the minimum-remaining-values strategy used in constraint-satisfaction problem solvers. When design spaces are comparable, break ties by (in order): what unblocks the most other work, what reduces the most project uncertainty, owner priority.

**Termination.** The design (or sub-design under a contract) is complete when the driving question answers *yes* with rationale, AND:

```text
- every assumption has been accepted, rejected, or converted
  (glossary §15.1);
- every open question is resolved (glossary §15A);
- every load-bearing decision is recorded (§9);
- every requirement has a defined verification method or a
  recorded waiver (glossary §2).
```

### 3.3 Hard Requirements

Hard requirements are pass/fail constraints. A candidate architecture that violates a hard requirement is invalid unless the requirement is changed by its owner.

### 3.4 Optimization Objectives

Optimization objectives define qualities to maximize or minimize, such as latency, cost, reliability, maintainability, commonness of technology, development speed, or implementation risk.

Candidate architectures that satisfy all hard requirements are compared against these optimization objectives.

## 4. Capability Reasoning

Capability is a reasoning concept rather than a separate authoritative artifact layer. Within this process:

```text
- Requirements drive design. Anything that drives design must be a requirement.
- Anything that does not drive design is context, not authority.
```

When useful, requirements may be translated into capability statements as a way of describing what the system must be able to do before deciding how that ability should be implemented or allocated. This is helpful in distinguishing *what the system must accomplish* from *how responsibility should be divided* — and in resisting premature architectural decomposition.

A capability statement that is purely descriptive — used to organize reasoning, label discussions, or aid decomposition — remains a context artifact and does not carry design authority on its own.

A capability that turns out to actually drive design (e.g., it dictates a contract boundary, an interface obligation, or a constraint on candidate mechanisms) should be captured as a requirement, with the same fields and traceability obligations as any other requirement.

The capability layer is therefore optional. Use it where it clarifies reasoning. Skip it where requirements can be derived directly without an intermediate capability vocabulary. (See glossary §10.)

## 5. Catalog-Guided Mechanism Selection

For each required capability, the process consults catalogs of known mechanisms, technologies, patterns, and architectural options.

Catalogs exist to preserve prior reasoning, reduce repeated brainstorming, expose known tradeoffs, and generate decision-relevant questions.

Each catalog category should include:

```text
- Candidate options
- Strengths and weaknesses
- Known tradeoffs
- Commonness and maturity
- Risks and failure modes
- Relevant constraints
- Discriminating properties
- Clarifying questions
```

The purpose of catalog questions is not to ask every possible question. The purpose is to ask questions whose answers could change the preferred option or alter the architecture.

### 5.1 The Catalog as a Snapshot

The catalog is a *snapshot* of what the project currently knows is possible — a cache of the real-world space of solutions, not an authoritative limit on it. Novel mechanisms, recent technologies, and project-specific options may exist outside the catalog.

The intended workflow is:

```text
1. Consult the catalog first when looking for candidate mechanisms
   for a requirement.
2. If no catalog entry fits, search outside the catalog (the real
   world of possible solutions) for candidates.
3. When a useful new mechanism is found outside the catalog,
   capture it as a new catalog entry so future iterations and
   future projects benefit.
4. Each catalog entry records the date of its last update so
   designers can judge how stale the entry may be (see glossary
   §17).
```

Treating the catalog as a cache rather than a limit ensures the project benefits from accumulated knowledge without being trapped by it.

### 5.2 Catalog Scope

Catalog entries should be **project-agnostic** where possible: they capture the relevant general aspects of the technology, mechanism, or pattern, without limiting their description to the current project's potential use of them. The catalog is intended for reuse across projects.

Project-specific considerations — *how* a particular catalog option fits the current project's specific requirements, constraints, or tradeoffs — belong in decision records, research investigations (glossary §15B), or tradeoff-analysis artifacts, not in the catalog entry itself.

When adding or updating a catalog entry, capture only the information needed for the current decision. Comprehensive coverage is not required; entries grow incrementally as additional projects encounter additional aspects. What is added should remain generic to the option, not specific to the current project's use of it.

**Catalog entry kinds.** The catalog admits two structurally-distinct kinds of entry: **mechanism entries (CAT-NNNN)** — options for satisfying capabilities or requirements per the scope above (mechanisms, technologies, patterns, architectural options); and **development-approach entries (APP-NNNN)** — options for governing the design-to-implementation transition (development approaches per §10A.14). Both kinds follow the same entry schema (status, owner, last-updated, description, strengths, weaknesses, tradeoffs, commonness/maturity, discriminating questions, sources). The distinction is the *selection context*: mechanisms are selected by capability-decomposition decisions (§4, §10); approaches are selected by approach-selection decisions at design-done milestones (§10A.14).

A project may extend the catalog with additional entry kinds; the entry-kind set is itself a project catalog-scope choice. Adding a new entry kind is a §13-governed decision per §9. The two kinds above are the constitution's defaults.

### 5.3 Catalog Bootstrapping

A project's first DECIDE encounter with the catalog will frequently find no relevant entries — particularly early in the project's life or when the catalog is shared across only a small set of projects. In that case, the project **bootstraps** the catalog as part of opening the decision: candidate mechanisms are recorded as new catalog entries with the information available, then evaluated as part of the decision. The bootstrapped entries remain in the catalog after the decision is made, available for reuse by future iterations of this project or by other projects.

Bootstrapping is a normal part of the DECIDE step, not an exception. The same project-agnostic scope rule (§5.2) applies to bootstrap entries: they record the option's general character, not its fit to the current project.

### 5.4 Considered Options Become Catalog Entries

Any option that the project considers as a candidate to satisfy a requirement — whether or not it is investigated in depth, and whether or not it is selected — should result in a catalog entry. This applies whether the option is selected, rejected, deferred, or considered and dismissed.

The catalog therefore grows with the project's reasoning history. Options that have been *considered and rejected* are valuable to keep recorded — both for the rejecting decision (so the rationale is traceable) and for future projects (which may face the same candidate set and benefit from prior consideration).

Rejection rationale belongs in the **decision record** that rejected the option (§9), not in the catalog entry. The catalog entry remains project-agnostic and captures only the option's general character (per §5.2).

When a topic comes up for consideration without prior catalog presence, add a catalog entry — minimal is acceptable — before the consideration is resolved. The entry records the option's character; the decision records why it was selected, rejected, or deferred. A research investigation (glossary §15B) may or may not be opened to fill in the entry further; the existence of the entry does not require the investigation.

## 6. Handling Underspecified Requirements

Requirements are often underspecified. The process does not require all ambiguities to be identified upfront.

Instead, ambiguities are discovered when they affect a design decision.

When the process reaches a decision point and the preferred option depends on an unknown interpretation of a requirement, the architect or system must either:

```text
1. Ask the owner of the requirement for clarification; or
2. Make an explicit assumption and record it for verification.
```

Clarifications may result in new requirements, revised requirements, assumptions, constraints, non-goals, or priority changes.

## 7. Candidate Architecture Generation

Capabilities are grouped into candidate architectural decompositions.

A candidate decomposition should be evaluated using criteria such as:

```text
- Cohesion
- Coupling
- Volatility
- Ownership clarity
- Contract clarity
- Independent development
- Testability
- Replaceability
- Operational risk
- Performance requirements
- Security and privacy constraints
- Expected future change
```

A capability should become a separate module or contract when doing so reduces coupling, hides volatility, enables independent work, clarifies ownership, protects higher-level objectives, or makes the system easier to reason about.

## 8. Candidate Evaluation and Optimization

Candidate architectures are evaluated in two stages.

First, candidates that violate hard requirements are rejected.

Second, remaining candidates are scored against optimization objectives according to explicit weights, priorities, or decision rules.

The selected candidate is the architecture that best satisfies the stated decision criteria under the known requirements, assumptions, constraints, and priorities.

If multiple candidates are meaningfully equivalent, an arbitrary decision may be made, but the basis for treating them as equivalent should be recorded.

### 8.1 Justification Quality

Every selection — of an architecture, a mechanism, a contract assignment — must be justified by reference to authoritative artifacts (requirements, constraints, tradeoffs, recorded assumptions).

Typicality, convention, common practice, and "what is usually done" are **not** by themselves sufficient justifications. A typical option may be selected, but only when its selection is justified by fit to the project's stated objectives. Commonness or maturity may appear as one input to a tradeoff analysis, but commonness alone never carries the decision.

The standard is not that every decision be inevitable. The standard is that every decision be justifiable in terms the project has explicitly accepted as authoritative.

## 9. Decision Records

Decision records capture the Stage 3 (Act) output of the Generate-Compare-Act pattern (§2A). The §9.1 option-criterion matrix is the primary Stage 2 mechanism; §9.2 swing-weight refines it when criteria differ materially in importance; §9.3 completeness check is Stage 3 verification.

Every major architectural decision should be recorded in a versioned decision record.

A decision record should include:

```text
- Decision ID
- Title
- Status
- Version
- Owner
- Date
- Related requirements
- Related capabilities
- Related assumptions
- Options considered
- Selection criteria
- Decision
- Reasoning
- Tradeoffs
- Rejected alternatives
- Affected contracts
- Risks
- Review triggers
- Superseded decisions
- Superseding decisions
```

Decision records should not be overwritten. When a decision changes, the previous decision is preserved and marked as superseded. The new decision records why the prior reasoning no longer governs.

**Superseded / Superseding semantics (broadened per DEC-0620).** The *Superseded decisions* and *Superseding decisions* fields cover any governance artifact whose authoritative claim is being reversed or replaced — not only DECs. Examples include handback artifacts (HB-NNNN) reversed by a subsequent audit DEC; fulfillment-check claims (FC-NNNN) reversed by re-evaluation; or contract versions (CON-NNNN vN.M) superseded by class-1 replacement per §30A. The field names are preserved for backward-compatibility with the existing artifact corpus; the semantics extend to "Superseded artifacts" / "Superseding artifacts" in current reading. When listing a superseded artifact, include the supersession relationship's rationale.

### 9.1 Option-Criterion Evaluation

When a decision involves choosing among multiple options against multiple criteria, the *Tradeoffs* and *Selection criteria* fields above should be structured as an **option-criterion evaluation** rather than free prose. Each (option, criterion) pair is recorded as:

```text
- option: <option ID or short name>
- criterion: <criterion ID or short name>
- sign: positive | negative | neutral
- weight or note: (optional) magnitude or short rationale
```

Reading the matrix, an evaluator can see immediately which options satisfy which criteria, where each option's strengths and weaknesses lie, and which criteria are decisive. The structured form supports the §8.1 justification-quality rule (no typicality-only justifications): every cell either has a justification or its absence is itself the rationale for the cell's verdict.

This pattern is borrowed from QOC (Questions, Options, Criteria; MacLean et al., 1991), where signed edges between options and criteria are central to the design-rationale notation. See glossary §1.2 for the lineage and glossary §20 for the decision-record artifact.

The form is not mandatory for trivial decisions (one option, no real comparison), but is the recommended default whenever options are genuinely being compared.

**Criterion selection happens first** (per PO-17). Before constructing the matrix or evaluating options, the analyst identifies **what matters for this decision** — the criteria the options should be graded against. Then weights are assigned (if criteria differ materially in importance), pre-committed before scoring. Only then are options scored.

The criterion-selection step is a distinct phase that has been underweighted in practice; the discipline is explicit:

```text
1. What matters for this decision (criteria) is drawn from:
   - The requirements + objectives the decision must fulfill.
   - Load-bearing artifacts (§10.5) — but only when they
     discriminate for THIS decision. Load-bearing status does
     not automatically include the artifact as a criterion.
   - Decision-local concerns specific to this decision.

2. Criteria are NOT carried forward from prior decisions by
   default. Each decision audits its own criteria. Criteria
   that mattered for a previous decision may or may not matter
   for this one; the analyst checks.

3. Weights are pre-committed (per §9.2 when criteria differ in
   importance; otherwise treated as equal).

4. THEN options are scored against the criteria.

5. If no clear winner emerges, examine what differentiates
   contenders — surface latent criteria honestly (per the
   matrix-readability paragraph below), invoke tie-breaking
   questions (§3.2), or apply §9.2 weighted evaluation.
```

The discipline distinguishes two structurally-different concerns:

```text
LOAD-BEARING ARTIFACTS (§10.5):
  Ever-present constraints. Audited at every decision — but
  only INCLUDED as criteria if they discriminate for the
  decision at hand.

CRITERIA FOR THIS DECISION (§9.1):
  Dimensions chosen because they matter for THIS decision's
  grading. Drawn from requirements + objectives + (relevant)
  load-bearing items + decision-local concerns. NOT carried
  forward by default.
```

A useful analogy: criteria for "how many bolts on a wheel" are not the same as criteria for "interior color offerings." Some criteria do transfer when they apply broadly (e.g., "must support the luxury-vehicle positioning") — but most criteria are decision-local and should not be padded into unrelated decisions. The criteria for each decision should be chosen based on what will help differentiate the available options relative to the requirements and objectives that the choice is attempting to fulfill.

**Matrix readability.** The recommendation produced by an option-criterion evaluation should be **readable from the matrix on its own**. Cell scores should make the recommendation visible: the recommended candidate either strictly dominates (per §3.2 hybrid-dominance check) or its advantage is captured by the criteria the matrix records. If the recommendation does not follow visibly from the cell scores and the analyst is bridging the gap with prose synthesis, that gap is a signal — *not* that the recommendation is right despite the matrix, but that the matrix is missing or mis-defining a criterion.

When the analyst sees this gap, the disciplined moves are, in order:

```text
1. Check whether an existing criterion is MIS-FRAMED rather
   than missing. Many "missing criteria" are actually existing
   criteria with imprecise definitions that conflate distinct
   costs or capabilities. Reframing the criterion (e.g.,
   splitting "coordination cost" into "wasted coordination"
   vs. "necessary coordination") often resolves the apparent
   gap without adding criteria.

2. If a genuinely latent criterion exists — a dimension the
   analyst was implicitly weighing but had not yet made
   explicit — surface it as a new criterion. Define it
   independently of the candidates; score every candidate
   against it. The matrix is re-read with the new criterion in
   place.

3. If no honest reframe or new criterion resolves the gap,
   invoke §3.2 tie-breaking questions explicitly. Surface the
   trade-off to the owner rather than burying it in prose. A
   matrix that does not produce a visible recommendation, and
   for which no legitimate criterion revision resolves the
   case, is exactly the case §3.2 tie-breaking is designed
   for.
```

**Guarding against criterion-gaming.** The latitude to revise criteria mid-evaluation invites a failure mode: *manufacturing* a criterion whose definition exists only to promote a predetermined recommendation. This must be guarded against, because a "rule of revision" without discipline collapses the matrix's purpose entirely.

The distinction is between **surfacing latent criteria that were not fully recognized before** (legitimate) and **creating criteria such that a specific option is promoted** (illegitimate). Three heuristics for distinguishing the two when revising criteria during evaluation:

```text
1. Pre-commitment test. Write the criterion's definition and
   the scoring rubric BEFORE re-scoring any candidate. If the
   definition is hard to express without naming the favored
   option ("the property X has and Y doesn't"), the criterion
   is probably gamed.

2. Hypothetical-alternative test. Imagine a candidate not yet
   on the table. Would it score consistently against the new
   criterion using the same definition? If the criterion has
   clear non-trivial scoring outcomes for unseen candidates,
   it is probably genuine. If it only resolves the case for
   the favored candidate, it is probably gamed.

3. Reframe-vs-add preference. Before adding a new criterion,
   check whether an existing one is mis-framed. Reframing
   exposes hidden distinctions in dimensions already
   identified; adding introduces new dimensions. Reframing is
   the cleaner first move when the apparent gap traces to
   imprecise definition. Adding is justified when a genuinely
   distinct dimension is involved.
```

A revision that meets these tests — a criterion definable without naming the favored candidate, applying with clear scoring to alternatives the analyst had not considered, and either reframing an existing criterion honestly or introducing a genuinely new dimension — is legitimate. A revision that fails them is gaming and should be rejected.

The discipline ensures that the matrix remains a *test* of whether a recommendation is well-founded, not a *theater* in which any recommendation can be justified by clever criterion framing.

**Escalation to §9.2 when criteria differ in importance.** The reframe-vs-add discipline above covers one class of matrix-recommendation gap: the matrix is wrong because of criterion *definitions*. There is a second class: the matrix is wrong because of criterion *weights*. These have distinct responses.

```text
DIAGNOSTIC: when the §9.1 matrix's visible cell-count diverges
from the analyst's argued recommendation, ask:

  Is the divergence because the matrix is missing or mis-
  defining a criterion? (Definition problem.)

  OR is it because the matrix's +/~/− cell scoring treats all
  criteria as equally important, when in fact one or more
  criteria carry qualitatively more weight than others?
  (Weight problem.)
```

The two classes have distinct responses:

```text
DEFINITION PROBLEM → reframe or add per the discipline above.

WEIGHT PROBLEM → escalate to §9.2 swing-weight matrix.
                 Apply importance × variation weighting; let
                 the weighted scores produce the
                 recommendation. Do NOT bridge the gap with
                 prose synthesis.
```

The case for §9.2 escalation is recognizable: several criteria align in one direction (the cell-count winner), but those criteria are bounded *cost* dimensions (small absolute variation; enabling rather than critical importance), while a single criterion in the other direction is a *capability* dimension (wide variation; critical importance). The cell-count is misleading because it weights all criteria equally; the swing-weight matrix correctly reflects that not all criteria carry equal load.

A useful litmus: if removing the apparently-decisive criteria from the matrix would NOT change the recommendation, those criteria are likely enabling-with-narrow-variation and a swing-weight pass will down-weight them appropriately. If the recommendation depends on the apparently-decisive criteria, they're likely critical-with-wide-variation and the matrix is reading correctly.

**Four-way disposition of matrix-recommendation gaps**:

```text
1. Reframe an existing criterion (PO-14)
   — when an existing criterion conflates distinct dimensions or
   is imprecisely defined.

2. Add a new criterion with anti-gaming guardrails (PO-14)
   — when a genuinely latent dimension is missing.

3. Escalate to §9.2 swing-weight matrix
   — when criteria differ materially in importance and +/~/−
   cell scoring is insufficient.

4. Invoke §3.2 tie-breaking questions
   — when criteria are correctly framed and weighted, and
   candidates genuinely split on which they favor. The
   trade-off is surfaced to the owner as an explicit question.
```

These four responses are not interchangeable. Each addresses a different failure mode of the §9.1 matrix. Choosing the wrong response — for example, escalating to §9.2 when the actual problem is a missing criterion — buries the issue rather than resolving it.

**Criterion-grounding sub-rule (added 2026-05-24 per DEC-0690 / PO-30).** Each criterion in a §9.1 option-criterion matrix shall trace to an authoritative artifact. PO-17's *decision-local concerns* admission, taken alone, is insufficient — it admits the failure mode in which analyst judgment is dressed as a criterion. Acceptable grounding forms for any criterion are:

```text
(a) Direct trace to a REQ or DEC that motivates this
    criterion.

(b) Trace to a project-context fact that itself traces to
    a REQ or DEC (e.g., a DV5 "implementer experience"
    criterion traces to the project's recorded implementer
    identity + an approach-selection DEC such as the
    designofeverything project's DEC-0570).

(c) An explicit decision-local concern with project-
    context justification per §8.1 — NOT analyst judgment
    alone; the justification must demonstrate the concern
    is real for THIS project (not generic-best-practice).
    Per PO-14 anti-gaming, the (c) justification must pass
    the pre-commitment + hypothetical-alternative tests:
    a "decision-local concern" that exists only to move
    the matrix toward a predetermined option is gaming,
    regardless of grounding line.

(d) When (a)-(c) are not possible AND the analyst believes
    the criterion is substantive, a new REQ is opened per
    §10.4 BEFORE the criterion is used in the matrix.
    §10.4's trivial-vs-non-trivial-derivation rule governs
    whether a full DEC is needed for the new REQ.
```

The §9.3 completeness check shall verify each criterion in any §9.1 matrix has one of (a)-(d) recorded. A criterion with no grounding line is FAIL-MUST-REVISE.

The sub-rule does NOT mean "every criterion needs a new REQ written before it can be used." (a)-(c) typically cover all legitimate criteria; (d) is the exception. The point is to make the grounding EXPLICIT and AUDITABLE, closing the PO-17 loophole through which ungrounded analyst-bias has historically reached §9.1 matrices (see PO-30 for the empirical evidence that motivated this sub-rule).

### 9.2 Weighted Evaluation via Swing-Weight Matrix (optional refinement)

When the option-criterion evaluation in §9.1 involves criteria of genuinely different importance, and the positive/negative/neutral shorthand is insufficient to distinguish strong from weak signals, the project may use **weighted evaluation**. INCOSE practice for this is the **swing-weight matrix** ([Parnell & Trainor 2009, INCOSE International Symposium](https://www.incose.org/resource/2-3-1-using-the-swing-weight-matrix-to-weight-multiple-objectives/)).

The swing-weight matrix assigns weights to criteria along **two axes**:

```text
- Importance — whether the criterion measures a *defining*,
               *critical*, or *enabling* function. Defining
               criteria distinguish the option set from
               non-options; critical criteria are essential to
               acceptable performance; enabling criteria support
               but do not define.

- Variation  — the gap between the current capability of the
               worst-acceptable option and the best-available
               option on this criterion. Wider variation gets
               more weight, because the criterion discriminates
               more among the options being compared.
```

The highest-importance × highest-variation criterion is the **anchor**: it receives an un-normalized weight of 100 (placed in the upper-left corner of the swing-weight matrix). Other criteria are placed in the matrix and assigned weights relative to the anchor. The weights are then normalized so they sum to 1 (or 100%).

Once weights are assigned, each option's score on each criterion (converted from +/−/~ to a numeric scale — e.g., +1 / −1 / 0, or a finer 5- or 7-point scale) is multiplied by the criterion's weight. The sum is the option's overall score. The option with the highest score is the matrix-recommended choice.

When swing-weight evaluation is used, both the swing-weight matrix (showing how weights were assigned) and the resulting scored matrix (showing each option's overall score) should be recorded in the decision (§9). The verbal-style §9.1 matrix may still be recorded alongside as a readability aid.

**When to use swing-weight rather than plain §9.1**: when the choice is contested, when the criteria genuinely vary in importance, or when the decision must be defended to external reviewers (regulatory, customer, audit). The plain §9.1 matrix is sufficient for most ordinary decisions; swing-weight is the formal escalation when the case requires it.

Insight from INCOSE practice: weights depend on **both importance and variation**. A criterion that all options score the same on does not discriminate even if it is very important; a criterion with wide variation discriminates strongly even if its absolute importance is modest. The swing-weight technique forces this duality to be considered explicitly rather than implicitly conflating the two.

### 9.3 Phase 1 Completeness Check

Before a decision artifact's Phase 1 (proposal) is offered for parent acceptance, the artifact's author **shall** perform an explicit completeness check. The check is gating: the parent **shall not** accept Phase 1 unless the check has been performed **and** every field passes.

The check verifies, for each field, **both**:

```text
(a) PRESENCE — the field exists in the artifact OR is
    explicitly marked N/A with a one-sentence rationale
    explaining why the field is not applicable to this
    decision.

(b) SUBSTANCE — when present, the field carries
    substantive content: content that adds information
    beyond the field label, that contributes to the
    parent's or future reader's understanding of the
    decision, and that traces to authoritative artifacts
    per §8.1.

    The following do NOT constitute substantive content
    and shall cause the check to FAIL:
      - placeholder text ("TBD", "to be determined",
        "[fill in]")
      - tautologies that restate the field's label
        without adding information ("the decision is to
        make the decision")
      - vague non-traceable assertions ("seems
        reasonable", "industry standard", "best
        practice") without trace to authoritative
        artifacts per §8.1
      - duplicate cross-references that point to
        another section without adding context ("see
        above" without specifying what)
      - rubber-stamped sign-offs that merely assert
        compliance without demonstrating it

A field that fails (b) is treated identically to a
missing field: the decision artifact shall be revised
before Phase 1 acceptance can be attempted.
```

The §9 template fields covered are: Decision ID; Title; Status; Version; Owner; Date; Related requirements; Related capabilities (if applicable); Related assumptions; Options considered; Selection criteria; Decision; Reasoning; Tradeoffs; Rejected alternatives; Affected contracts; Risks; Review triggers; Superseded decisions; Superseding decisions.

**Additional checks:**

```text
1. If the decision involves choosing among multiple
   options against multiple criteria, the §9.1
   option-criterion evaluation shall be constructed:
   - Criteria selected first per PO-17 (criteria for
     THIS decision, not carried forward by default).
   - Weights pre-committed before scoring options.
   - Cells scored (+/~/− or extended convention).
   - Recommendation visible from cell scores per
     matrix-readability discipline.
   - Anti-gaming guardrails per PO-14 applied to any
     criterion-revision performed mid-evaluation.
   - For decisions where criteria differ materially
     in importance, the §9.2 swing-weight matrix may
     be invoked per PO-15.

2. For trivial decisions (single option, no real
   comparison), the §9.1 matrix is not mandatory;
   §8.1 justification quality discipline shall be
   applied instead — the recommendation must trace
   to logical fit to authoritative artifacts, not
   typicality.

3. The completeness check itself shall be recorded
   in the decision artifact (typically as a
   "Completeness check" section), enumerating each
   §9 template field and its verdict (PRESENT-AND-
   SUBSTANTIVE / N/A-WITH-RATIONALE / FAIL-MUST-
   REVISE).
```

The completeness check is the responsibility of the decision artifact's author. The parent owner **shall not** accept Phase 1 unless the check has been performed **and** every field has verdict PRESENT-AND-SUBSTANTIVE or N/A-WITH-RATIONALE. Any FAIL-MUST-REVISE verdict requires the artifact to be revised; the check is re-performed on the revision; Phase 1 acceptance can be attempted only after every field passes.

This rule applies to all decision artifacts (DEC-NNNN) regardless of scope, decomposition level, or constitutional level (including constitutional refinements such as this §9.3 itself, which was recorded in DEC-0610 with a self-applied completeness check demonstrating the discipline).

**Sub-rules added by DEC-0620 (2026-05-21):**

*N/A-WITH-RATIONALE requires explicit rationale.* When a §9 template field is marked N/A, the rationale shall explicitly state why the field is not applicable to this decision — even if the rationale is *"we considered each dimension below and could not identify an applicable aspect."* Silent absence of a field (no entry, no N/A marker, no rationale) constitutes FAIL-MUST-REVISE per §9.3(a). Empty fields signal either that the author did not consider the dimension (the failure §9.3 was built to catch) or considered it without recording the reasoning (leaving future readers unable to verify). Both fail.

*Substantive vs ergonomic options.* When the *Options considered* field contains more than one entry, the author shall mark each option as either **substantive** — independently meeting the decision's objectives differently; requires §9.1 matrix construction or §8.1 justification — or **ergonomic** — variations in how a substantive choice is realized (e.g., placement choices for a constitutional addition; bundling choices for related refinements; presentation choices). Ergonomic alternatives do not require matrix scoring; they are recorded with rationale only. A decision with one substantive choice plus several ergonomic alternatives may invoke the §9.1 trivial-decision exception for the substantive choice while still enumerating the ergonomic alternatives.

*Deferred decisions as N/A-WITH-RATIONALE.* When a decision is deferred per §10A.10 (last responsible moment), the *Decision* field is recorded as N/A-WITH-RATIONALE with the rationale stating the trigger conditions under which the decision will be resolved. The Decision field is not FAIL-MUST-REVISE in this case — the deferral itself is the recorded answer, and the trigger conditions are the substance.

*Blanket-template recording.* The completeness check is by default recorded **per-DEC** (a Completeness check section within each decision artifact). A blanket-template approach (recording the check for multiple DECs via a single appendix declaration) is permitted **only when explicit justification for the blanket approach is provided** in lieu of per-DEC sections — e.g., the project is in active development and per-DEC boilerplate would not earn its keep relative to the appendix's coverage. Without such justification, per-DEC recording is required.

### 9.3.1 Deferred-Decision Recording

Decisions deferred per §10A.10 *last responsible moment* are recorded as a normal DEC artifact with the *Decision* field marked N/A-WITH-RATIONALE per §9.3 above. The rationale states the trigger conditions for resolution. The deferred DEC remains open in the project's tracking; when the trigger conditions are met, the deferred DEC is revised (the *Decision* field becomes PRESENT-AND-SUBSTANTIVE; version increments per §30A class-2). Examples in the designofeverything project: DEC-0070 (artifact identifier encoding) and DEC-0160 (shared cross-cutting store backend).

### 9.3.2 Change-Acceptance Compact Template

A **change-acceptance DEC** is a DEC that accepts a previously-proposed change request (CHG) and transitions a source DEC from Phase 1 to Phase 2 (or applies a class-2 revision to an existing contract per §10A.11). Change-acceptance DECs follow a compact template that inherits §9 template fields from the source DEC being accepted.

The compact template requires the following fields to be present in the change-acceptance DEC itself:

```text
- Decision ID
- Title (typically "Acceptance of CHG-NNNN" or similar)
- Status
- Version
- Owner
- Date
- Decision (states the acceptance + the resulting artifact version)
- Reasoning (states why the change is accepted)
- References (cites the source DEC + CHG)
```

The following fields **may** be marked N/A-WITH-RATIONALE with the form *"Inherits from DEC-NNNN [the source DEC]"*:

```text
- Related requirements
- Related capabilities
- Related assumptions
- Options considered
- Selection criteria
- Tradeoffs
- Rejected alternatives
- Affected contracts (if the acceptance doesn't add new affected artifacts)
- Risks (if the acceptance doesn't surface new risks)
- Review triggers (if inherited from source)
- Superseded decisions
- Superseding decisions
```

Inheritance applies only when the field's substance is materially unchanged from the source DEC. If the change-acceptance surfaces new affected artifacts, new risks, or new review triggers, those fields must be PRESENT-AND-SUBSTANTIVE rather than inherited.

## 10. Architecture Contracts

Architecture is governed through contracts.

A contract defines the *shell* of a design space — its boundaries — for a component, module, subsystem, service, or any other delegated scope of work. Inside that shell the delegate has full freedom to choose any implementation that meets the contract. The shell itself is owned and may not be altered without governed change.

The skyscraper analogy is useful: the designer of a single floor is given the floor's outer dimensions, the locations of structural supports, and the connection points for electrical, mechanical, and plumbing systems. Within those, the floor designer chooses freely. Outside those, the floor designer has no authority — those boundaries belong to the design of the building as a whole.

In software terms, a contract names what crosses the boundary (objective, inputs, outputs, constraints, externally visible behavior) and what stays inside it (implementation, internal structure, internal state, internal interfaces).

The core contract template is:

```text
Contract ID:
Name:
Owner:
Status:
Version:

Objective:
Inputs:
Outputs:
Owns:
Does Not Own:
Constraints:
```

Extended contract fields may include:

```text
Assumptions:
Dependencies:
Provided Interface:
Required Interface:
Quality Requirements:
Related Requirements:
Related Decisions:
Change Authority:
Open Issues:
```

The basic rule of a contract is:

```text
As long as you meet these requirements, you may choose the implementation inside this boundary.
```

The delegate owns implementation decisions inside the contract. The contract owner retains authority over changes to the contract boundary, objective, inputs, outputs, constraints, and externally visible behavior.

### 10.1 Reference Stability in Contract Prose

Contracts and other artifacts at higher levels of the architecture tree should describe their scope in terms of *stable referential anchors* — the requirements that justify them and the decisions that constrain them — rather than by embedding specific lower-level implementation choices in prose.

When a contract's scope depends on a decision recorded elsewhere (e.g., "the data-model backend is X"), the contract should cite the decision (e.g., *"the data-model backend specified in DEC-XXXX"*) rather than name the specific choice directly. This:

```text
- Improves traceability — impact analysis can find every contract
  affected by a decision change.
- Reduces churn when the underlying decision is revisited or
  superseded.
- Keeps the contract at the correct level of abstraction for its
  position in the tree; specific implementation choices remain at
  the level where they are decided.
```

Specific implementation choices may still appear directly in lower-level contracts whose scope is exactly that decision. A contract whose objective is "implement the persistence layer using X" may name X in its prose, because at *that* level, X is the relevant referent.

This principle is closely related to *Last responsible moment* (§10A.10): higher-level contracts are necessarily more abstract; concrete implementation choices belong at the lowest level where the choice is binding, and references in higher-level contracts should reflect that.

**Reference stability extends to concept stability.** The principle stated above applies not only to *references* (citing decisions rather than naming choices) but to the *vocabulary and concepts* used in upper-layer artifacts. An upper-layer contract, decision, or change request must not name specific backend constructs, data-store-specific syntax, or implementation-level terms in prose — these belong at the implementation layer. Upper-layer artifacts use the abstraction's vocabulary; backend-specific terms appear only in the artifacts whose scope is the backend itself.

```text
Two failure modes the discipline guards against:

1. REFERENCE drift — upper-layer contract names "Datomic" or
   "PostgreSQL" directly rather than citing the data-model
   decision. If the data-model choice is later revised, every
   such reference must be hunted down.

2. CONCEPT drift — upper-layer contract describes behavior using
   backend-specific vocabulary (specific attribute namespaces,
   transaction primitives, query languages, etc.). Even with
   correct references, the surrounding prose embeds
   implementation choices that should be at the implementation
   layer.

The discipline mitigates both: cite decisions for references;
use abstraction-level vocabulary in concepts; let implementation
specifics live in the layer whose scope is the implementation.
```

When a decision spans layers — for example, a higher-level
delegate decision whose recommendation is informed by a
backend's specific characteristics — record the recommendation
at the abstraction level (the pattern, expressed in domain
terms), and present the backend-specific mapping as a separate,
explicitly-labeled section. If the underlying decision (e.g.,
the data-model backend) is later revised, the abstraction-level
recommendation typically still holds; the mapping changes.

This discipline is operationalized via the **load-bearing
artifacts** mechanism (§10.5).

### 10.2 The Contract Is the Parent's Specification of the Delegate's Scope

The contract is fully the **parent's** specification of the delegate's scope. The delegate **cannot** modify the contract; only the parent can. The contract defines the boundary within which the delegate has freedom and outside which they have none.

A parent-authored contract specifies what the parent has determined about the sub-scope: its objective, what it owns, what it does not own, what constraints it must honor, what requirements it must satisfy, and (to whatever level of detail the parent has chosen to specify) its inputs, outputs, and exposed interface.

The contract does **not** specify the delegate's *internal* structure — implementation, internal interfaces, internal data structures, file layout, etc. Those remain the delegate's freedom inside the contract's boundary.

A contract may be deliberately abstract in places when the parent has not yet determined the specifics. The *Provided Interface* and *Required Interface* fields of the contract template, in particular, are often left abstract or constraint-level at decomposition time — the parent specifies what the interface must accomplish ("must support persisting artifacts under transactional ACID guarantees per REQ-XXXX") without committing to specific signatures.

**Two distinct cases of unspecified contract sections.** Not every unspecified section follows the same trajectory:

```text
- "Eventually concrete here" sections. The section's specifics
  are consumed by a sibling contract, an external party, or a
  cross-cutting concern. The specifics MUST eventually become
  concrete at this contract's level (initially abstract; refined
  as the delegate's design surfaces them; the parent updates the
  contract via change governance per §10A.11). Typical examples:
  *Provided Interface* (what siblings call), *Required Interface*
  (what this contract calls on shared modules).

- "May remain light or empty at this level" sections. The
  section's specifics are entirely internal to the sub-tree below
  this contract. No sibling, no external party, no cross-cutting
  concern needs them at this level. They are resolved entirely in
  the delegate's own sub-contracts. Typical examples: internal
  data structures, internal interfaces between this contract's
  sub-contracts, implementation strategies.
```

When drafting or refining a contract, the parent asks for each potentially-unspecified section:

```text
1. Does any sibling contract or external consumer need this
   specified at this level to coordinate with the delegate's
   work?
2. Does the project as a whole (cross-cutting concerns, audit,
   integration requirements) need this specified at this level?

If YES to either:
   The section is "eventually concrete here." Mark abstract
   initially; refine as the delegate's design surfaces specifics.

If NO to both:
   The section "may remain light at this level." The specifics
   live entirely in the delegate's sub-contracts and need not
   surface at this contract's level. The section may stay light,
   empty, or carry a brief note saying so.
```

The default for higher-level contracts is to **leave sections light unless a concrete reason requires specifying them**. This is *last responsible moment* (§10A.10) applied at the section level.

When the delegate's work surfaces a need for greater specificity in the contract — for example, the delegate has designed concrete interface signatures and the dependents need to know them, or the delegate has discovered a requirement the contract did not anticipate — the delegate raises a **change request** to the parent (per §13 and §10A.11). The change request typically *proposes* the specifics. The parent reviews the proposal and decides; if the parent accepts, the parent **updates the contract** to reflect the new specifics. The change is recorded as a Decision per §9 and the contract's version increments.

The asymmetry — *parent updates contract; delegate proposes via change request* — is a fundamental rule of this process. The delegate's design output is real engineering work, but it does not become *part of the contract* until the parent has accepted and recorded the update. This preserves the contract's role as the boundary of authority: the parent owns the contract; the delegate works within it.

In the typical lifecycle, contract specificity *increases over time*: the contract starts abstract; the delegate's work surfaces specifics; the parent refines the contract via change requests; eventually the contract is concrete enough for implementation against. By the implementation-done milestone (§10A.5), the contract reflects the actual interfaces the delegate has built, having been refined through this loop.

### 10.3 Contract Content Traces to Requirements

Every item recorded in a contract — anything in *Owns*, *Inputs*, *Outputs*, *Constraints*, *Provided Interface*, *Required Interface*, or any other field that asserts what the delegate is responsible for or governed by — must trace to one or more **requirements** (or to a decision that itself traces to requirements). The contract is a vehicle for *allocating requirements to a scope*, not for inventing responsibilities.

If a contract author finds an item they want to record but cannot trace to a requirement, the response is **not** to add the item on the contract's authority. The response is one of:

```text
1. Stop and consider whether the item indicates a need for a
   derived requirement the project has not yet recognized.

2. If yes, either:
   (a) Pause contract drafting to go through the requirement-
       addition process (§10.4); when the new requirement is
       accepted, add the contract item with explicit trace to it.

   (b) Mark the item in the contract with a "pending decision"
       annotation referencing what the open requirement question
       is, and proceed with the rest of the contract drafting.
       Pending items must be resolved before the contract reaches
       the approved lifecycle state. When the resolution comes,
       the pending annotation is replaced with either a trace to
       the new requirement (if accepted), or the item is removed
       from the contract (if the consideration concluded against
       adding a requirement).

3. If consideration concludes immediately that no requirement is
   justified, the item is simply not added.
```

Option (b) is the typical workflow for contract authors who want to keep momentum: pending decisions surface as a tracked to-do list, and the contract is finalized when they are resolved. The constitution does not require contract authors to halt mid-draft for every item that surfaces a requirement question.

This rule preserves the constitution's foundational principle: **requirements drive design**. Contract content that does not trace to requirements is design authority that has bypassed the requirements gate.

### 10.4 Adding Derived Requirements Is Itself a Decision

When the design process surfaces a need that justifies a new derived requirement — whether through a delegate's change request, a contract author's discovery, a decision's downstream consequence, a research finding, or a clarification from the owner — **adding the requirement is itself a decision** recorded per §9.

The decision evaluates:
- Whether a new requirement is justified, or whether the need is already covered by an existing requirement (interpreted appropriately).
- The proposed statement of the requirement.
- The parent requirement(s) from which it derives.
- Alternatives considered (if any) for how the requirement could have been stated, narrowed, or broadened.
- The justification per §8.1 (no typicality-only; logical fit to project objectives).

For **trivial derivations** — where the derived requirement is obviously implied by the parent and no alternatives plausibly exist (e.g., REQ-0010 "the tool shall represent a requirements tree" derived from REQ-0000 "provide a software tool that supports a requirements-constrained design process") — the decision may be abbreviated. The requirement's `Origin` field records the parent and a brief justification; no separate decision artifact is needed.

For **non-trivial derivations** — where alternatives exist for what the requirement should be, where the requirement constrains design in non-obvious ways, or where the requirement is contested — a full §9 decision record is created, with §9.1 option-criterion evaluation if alternatives were genuinely considered. The requirement's `Origin` field references the decision.

The rule: requirements are not exempt from §8.1's justification-quality discipline. Requirements *are* the artifacts §8.1 is designed to ensure are well-grounded. Adding a requirement without justification is the same error as adding any other design choice without justification.

### 10.5 Load-Bearing Artifacts Remain Ever-Present

Some artifacts in a project have **cross-cutting, structural impact** — every contract, decision, and iteration of the design loop must honor them. Cross-cutting requirements, foundational decisions, meta-disciplines surfaced by process observations, and load-bearing constitution sections all fit this description. Without explicit recurring visibility, these artifacts are easily drifted from: the project commits to a discipline, then quietly violates it iterations later when the discipline has faded from active consideration.

The process recognizes a class of **load-bearing artifacts** that:

```text
- Have cross-cutting, structural impact (every contract / many
  decisions are constrained by them).

- Are easy to drift from in practice (drift produces
  cumulative violations, not single-point errors).

- Benefit from explicit recurring visibility during every
  iteration rather than relying on memory.
```

The project maintains a curated **load-bearing artifacts index** (typically a single file in the project's working area; for projects implementing this process via the tool the canonical location is `tool/load_bearing.md` or equivalent). The index lists each load-bearing artifact with:

```text
- Reference — the canonical artifact (REQ-NNNN, DEC-NNNN,
  §X.Y of constitution / glossary, PO-NN).
- Statement — one-sentence summary.
- Why load-bearing — the cross-cutting impact warranting
  recurring visibility.
- Audit guidance — what to check at each iteration / decision /
  contract draft. The audit guidance is what makes the entry
  actionable.
```

**Visibility obligations**. The load-bearing artifacts index is:

- Included in every delegate's handoff package per §10A.3.
- Consulted at every fulfillment check (§3.2) before a new
  decision is opened.
- Consulted at every contract draft / refinement before
  approval is sought.
- Consulted at every change-request evaluation per §13.

**Adding / removing load-bearing status**. Adding an artifact to the load-bearing index is itself a Decision per §9 — load-bearing status is a governance choice. Removal likewise. The list grows as the project discovers which commitments warrant recurring visibility; it does not start exhaustive.

**Relationship to the cross-cutting-requirements concept (§7, PO-12)**. Cross-cutting requirements apply to many sub-contracts. Load-bearing artifacts are similar but broader: they include cross-cutting requirements, but also load-bearing decisions, meta-disciplines, and constitution / glossary sections. The load-bearing index is the operational mechanism for keeping all such artifacts ever-present.

**Anti-failure-mode**. PO-16 surfaced the failure mode this section addresses: the project committed to backend-abstraction discipline (REQ-0270 + §10.1) early, then quietly violated it in three subsequent decisions because the discipline had faded from active consideration. The load-bearing artifacts mechanism is the project's structural response — recurring visibility prevents the drift.

## 10A. Decomposition and Delegation Workflow

The contract concept of §10 enables this process's *primary mode* of operation: **decomposing a scope into bounded sub-scopes, drafting a contract for each, and delegating the work — to separate workers (human or AI), in parallel where possible — under contracts that prevent design drift**. This section specifies the workflow connecting §10 (contracts as boundary objects) to §11 (the architecture tree as structure).

### 10A.1 Implementer mode vs. decomposer mode

At any iteration of the design loop (§3.2), the designer of the current scope is in one of two modes:

**Implementer mode.** The current scope is small enough and well-understood enough that a single worker is producing the design and implementation directly. The three exits of §3.2 — SPLIT, DECIDE, ACT — operate within this mode.

**Decomposer mode.** The current scope is large, multi-faceted, or contains sub-areas that admit independent development. The work is to partition the scope into sub-contracts and delegate each. This section is the workflow.

The two modes are not mutually exclusive in time. A non-trivial project's loop alternates between them: upper levels typically in decomposer mode (carving the work into sub-systems), lower levels in implementer mode (building leaf functionality). The choice between modes is itself an instance of DECIDE — among the §7 criteria (cohesion, coupling, ownership clarity, contract clarity, independent development, testability, etc.), the criterion most relevant to mode selection is **independent development**: if sub-areas can be worked on without continuous coordination, decomposition is favored; if coordination would be so tight that workers would block each other constantly, implementation in one scope is favored.

This document treats decomposer mode as the default for any scope large enough to admit it. Implementation-as-a-single-worker is appropriate at the *leaves* of the contract tree; everywhere else, decomposition into bounded sub-contracts is the preferred move. The contract concept (§10) is what makes parallel delegated work safe: each sub-contract proscribes a shell of design space within which the delegate has full freedom and outside which they have none.

### 10A.2 The decomposition workflow

Decomposition follows the Generate-Compare-Act pattern (§2A): Step 1 (identify candidate decompositions) is Stage 1; Step 2 (evaluate against §7 criteria via §9.1 matrix) is Stage 2; Steps 3-5 (select + draft + approve) are Stage 3.

When decomposer mode is selected, the designer produces sub-contracts as follows:

```text
1. Identify candidate decompositions.
   Generate two or more ways the scope could be partitioned. Use
   category-first generation (§3.2) to keep candidates at the
   architectural level (e.g., "by capability layer", "by user-
   facing function", "by data domain") rather than committing to
   product-level details prematurely.

2. Evaluate candidates against §7's criteria.
   Apply the option-criterion matrix (§9.1). Cohesion, coupling,
   independent-development, contract clarity, and ownership
   clarity are the most discriminating criteria for decomposition.

3. Select a decomposition.
   Record the selection as a decision record (§9). The
   decomposition itself becomes a structural artifact of the
   project.

4. Draft a contract for each sub-scope.
   Per §10's structure: objective, inputs, outputs, owns, does
   not own, constraints, related requirements, related decisions,
   change authority, etc. The contracts together must cover the
   parent scope's requirements without overlap or gap. Where
   overlap or gap exists, the decomposition is not yet correct
   and step 1 should be revisited.

   *Tools implementing this constitution may support an automated
   check that the union of sub-contracts' owns covers the parent's
   scope without overlap. The check is helpful but not infallible —
   semantic overlap (two contracts that nominally own different
   areas but in practice need the same artifact) escapes
   syntactic checks. Treat the check as an aid, not as a guarantee.*

5. The parent owner approves each contract.
   Approval is itself a Decision recorded per §9: the parent
   verifies that each sub-contract's role, scope, and constraints
   are consistent with the parent's own scope, with related
   requirements, and with the project's broader context. Approval
   transitions the contract from drafted to approved (per the
   project's lifecycle states; see REQ-0290 in this project's
   tracking). Rejection — or approval with required modifications —
   is also recorded as a Decision with rationale per §9.

6. Assign each contract to a delegate.
   The delegate may be a human or an AI agent. Assignment may
   precede or follow approval depending on project conventions.
```

### 10A.3 The handoff package

When a contract is assigned and approved, the delegate receives a **handoff package** — the bounded context within which they will operate. The package comprises:

```text
- The contract itself (full §10 / glossary §21 fields).
- The current version of the constitution and glossary.
- The project's load-bearing artifacts index (§10.5). These
  artifacts must remain ever-present during the delegate's
  iteration; the handoff package surfaces them explicitly so
  they are not relied on by memory.
- The parent's artifacts that the contract references, plus those
  the delegate must read to understand the contract's context
  (transitively reachable via reference roles per glossary §1A
  and the project's role vocabulary).
- Read-only awareness of in-flight sibling contracts under the
  same parent.
- A clearly-stated "definition of done" for the contract — the
  conditions under which the delegate's iteration of the §3.2
  loop terminates (typically: all requirements within the
  contract's scope are fulfilled per §3.2's termination criteria,
  plus any contract-specific deliverables).
```

The delegate's writes are scoped to their contract per §10 and the project's REQ-0080 / REQ-0300 enforcement. Reads of artifacts outside the contract's scope are permitted (the delegate must understand the surrounding project) but writes are not. Tool-level support for assembling and presenting the handoff package is required (REQ-0280 in the project tracking this constitution).

### 10A.4 The delegate's iteration

Within their contract's scope, the delegate runs their own iteration of the §3.2 loop. The driving question becomes scoped: *Are all requirements within this contract fulfilled?* The three exits SPLIT / DECIDE / ACT operate on the contract's scope. The delegate may further decompose if their scope is itself large enough — they then become a contract owner for those sub-contracts, applying §10A.2 recursively.

The delegate is **not** authorized to modify the contract itself. If the delegate determines the contract is wrong (insufficient, infeasible, internally contradictory, or in conflict with a discovered constraint), they raise a change request to the contract's owner per §13 and the project's REQ-0310 — they do not unilaterally amend the contract. While the change request is pending, the delegate may pause or continue under a recorded provisional assumption (per glossary §15.1).

### 10A.5 The handback (design-done and implementation-done milestones)

A delegate's iteration of the loop terminates in **two distinct milestones**, each with its own handback to the parent.

**Design-done milestone.** The design within the contract's scope is complete: the *shape* of the work has been determined, even though no implementation work has been performed yet. The criteria are:

```text
- All design decisions in scope have been made and recorded (§9).
- Sub-contracts (if the delegate further decomposed) are authored,
  approved, and assigned.
- Verification methods (per glossary §2's IATD set: Inspection,
  Analysis, Test, Demonstration) have been assigned to every
  requirement in scope.
- All open questions in scope have been resolved.
- All assumptions in scope have been accepted, rejected, or
  converted (per glossary §15.1).
- Concrete interface specifications produced by the delegate's
  design work have been *proposed to the parent* (typically as
  part of the design-done handback) and the parent has refined
  the contract's *Provided Interface* and *Required Interface*
  fields accordingly via change governance (per §10.2 and §10A.11).
  The delegate does not modify the contract; the parent's
  acceptance is what makes the proposed specifics canonical.
```

At design-done, the contract may transition (per the project's lifecycle states) from `in-design` to `implementing`. The delegate produces a **design-done handback** containing the design decisions, sub-contracts, verification methods, and the proposed concrete interfaces. The parent reviews the handback, decides whether to accept, and on acceptance updates the contract to reflect the now-concrete specifics. The contract evolves under §10A.11's normal revision discipline.

**Implementation-done milestone.** The implementation work the design specifies has been produced and verified:

```text
- Every requirement in scope has been verified by its assigned
  method. For software, this is typically automated test (per
  glossary §2's note on the software primary tool); other
  methods apply where appropriate.
- Failure modes have been analyzed and either mitigated or
  accepted with rationale.
- The driving question (§3.2) for the contract's scope answers
  YES.
```

At implementation-done, the contract transitions to `fulfilled`. The delegate produces an **implementation-done handback** containing the implementation, the verification evidence, and any decisions made during implementation that affect the parent.

The parent reviews each handback. Acceptance at design-done allows the delegate to proceed to implementation; acceptance at implementation-done closes the contract. Rejection — or required revision — is recorded as a Decision per §9 with rationale, and returns the contract to `in-design` or `implementing` as appropriate.

The two-milestone separation matters because the *design* of a sub-scope is generally a prerequisite for any implementation work to begin coherently, and parent review at design-done can catch scope misunderstandings before substantial implementation effort is invested. Some projects may collapse the two milestones (a delegate proceeds straight through both with one combined handback) when the design is small or trivial; the constitution treats them as distinct milestones by default.

### 10A.6 Coordination among parallel delegates

Multiple delegates working on sibling contracts under a common parent will sometimes discover dependencies that were not captured when the contracts were drafted. The constitution's discipline:

```text
- Direct delegate-to-delegate writes are NOT permitted. Each
  delegate's writes are scoped to their own contract per §10
  and the project's REQ-0080.

- The tool makes each delegate aware of sibling contracts'
  current state read-only, so cross-delegate dependencies surface
  early.

- When a delegate's work would create or rely on a dependency
  not documented in the contracts, the delegate raises a change
  request to the common parent. The parent decides how to
  resolve: amend one or both contracts, absorb the dependency
  into the parent's scope, or reject the request.

- In the meantime, the delegate may proceed under a recorded
  assumption (per glossary §15.1) if waiting would block work.
```

The principle: **coordination flows through the common parent, not laterally between delegates**. This preserves the contract concept's integrity. Each delegate is bound to their contract; only the parent has authority to alter it.

### 10A.7 Integration and re-evaluation at the parent

When the parent accepts a delegate's handback, the parent integrates the result into their own scope. The parent's next iteration of the §3.2 loop re-asks the driving question with the delegate's contribution included. As more sibling contracts complete and are accepted, the parent's unfulfilled-requirements count decreases. When the parent's scope's driving question answers YES — all requirements fulfilled, all sub-contracts in the fulfilled state, all assumptions accepted, all open questions resolved, all decisions recorded, all verification methods defined — the parent's contract itself is fulfilled. The parent then hands back to *their* parent, and so on, until the recursion bottoms out at the project root.

### 10A.8 Project initiation under decomposition-as-primary

A project applying decomposition-as-primary starts as follows:

```text
1. Identify the external authority and elicit the root-level
   requirement(s) — §3.1.
2. Run an initial fulfillment check against the root (§3.2). For
   any non-trivial project this answers: not fulfilled.
3. At the root, choose mode (§10A.1). Almost always decomposer
   mode for a project at scale.
4. Apply the §10A.2 decomposition workflow. Produce the top-level
   sub-contracts — typically a small number of major sub-systems.
5. Approve each top-level contract; assign delegates.
6. Each top-level delegate runs §3.2 inside their contract,
   typically entering their own decomposer mode at first and
   transitioning to implementer mode at lower levels.
7. The project becomes a tree of contracts and delegates working
   in parallel where independent and serializing through change
   requests where they must coordinate.
8. Driving questions answer YES at every level over time; the
   project terminates when the root's question answers YES.
```

The depth of the contract tree is determined by the project's scale and the complexity of its sub-areas. Some projects are flat — a single contract; most non-trivial projects have multiple levels of nested sub-contracts.

### 10A.9 Reuse and shared modules

A specialized but important case in decomposer mode: when a candidate sub-scope, capability, or module is needed by **more than one** consuming contract — sibling contracts under the same parent, or contracts at different levels of the tree — the project should prefer **reuse** of a single shared sub-contract over redundant parallel implementations. Reuse-first is the default when the same capability would otherwise be implemented multiple times.

```text
Benefits of reuse:
- Less work overall.
- Consistent behavior across the project (one implementation,
  one verification record, one canonical contract).
- A single set of design decisions for the module rather than
  several with risk of divergence.
- Lower long-term maintenance burden.

Costs of reuse:
- Coordination — multiple consumers must agree on the module's
  contract; changes must be negotiated.
- Coupling — consumers depend on the shared module; the
  module's failures or breaking changes ripple outward.
- Ownership — the module needs an explicit owner accountable
  for it, distinct from the consumers.
```

When the workflow encounters a candidate module that could be shared:

```text
1. Search before building.
   The first question a delegate should ask, when identifying a
   needed sub-capability, is NOT "how do I build this?" but
   "does this already exist or is anyone else planning it?".
   The tool (per project requirements supporting this
   constitution) shall make existing and planned modules
   discoverable across the project.

2. Identify or assign an owner for the shared module.
   The owner is responsible for the module's contract, its
   evolution under change requests, and its fulfillment.

3. Each consumer references the shared module's contract from
   their own contract — typically with a "depends-on" reference
   role (per the project's reference vocabulary).

4. The shared module participates in the architecture tree as
   a node referenced by multiple parents — strictly speaking,
   the architecture is no longer a tree but a directed acyclic
   graph (DAG). See §11.

5. Changes to the shared module's contract are governed by §13,
   with the consumers explicitly notified — the consumers'
   designs depend on the module's contract.
```

**Primary parenthood — most-significant dependence rules ownership.** A shared module has a **primary parent**: the parent contract whose dependence on the module is most significant (highest impact, most relevant context for evaluating module changes). The primary parent owns the module's contract and arbitrates change requests from other dependents.

The most-significant-dependence rule is a recommendation, not a hard procedure. It says: among the contracts that depend on the shared module, the one most affected by the module's behavior — the one whose own scope would be most disrupted if the module changed — is the right place for ownership authority to sit. That parent has the strongest context for deciding what changes are acceptable.

**Transferability of primary parenthood.** Primary parenthood may transfer from one parent contract to another over a project's life. As the project evolves, the most-significant-dependent may change; the transfer is recorded as a Decision per §9 with rationale (e.g., *"primary parenthood of M-XXXX transfers from F-A to F-B because B's scope now exercises M-XXXX in ways A's does not"*).

**Ownership transfer is always parent-to-parent — never to the module's own delegate.** Ownership remains with a contract role at a level of the architecture tree that has the proper scope and context to make decisions about the module *in light of the other elements at that level*. A module's delegate has authority over the module's *implementation*, never over its *contract*. Transferring contract ownership downward to the delegate would dissolve the boundary between contract authority and implementation authority that the contract concept exists to maintain.

The eligible recipients of an ownership transfer are other parent contracts in the architecture tree — typically, sibling parents at the same level as the current owner (when dependence has shifted between siblings), or the parent of the current owner (when the module's relevance has expanded beyond the current owner's scope). In rare cases ownership may transfer further up to a grandparent or higher.

**Validation rights of dependents.** When any dependent requests a change to a shared module, **all** dependents have validation rights. Each must respond — accepting the change, raising their own change request, or pursuing an alternative:

```text
- Accept. The change is applied; the dependent updates its own
  contract or implementation as needed.

- Fork. The dependent severs from the shared module and creates
  its own version (a §30A class-1 change to the dependent's
  reference: the relationship to the original is replaced rather
  than revised). Forking is a strong move; it removes the
  consistency benefit of sharing and doubles maintenance burden,
  but is appropriate when the dependent's needs have genuinely
  diverged from the shared module's primary use cases.

- Recommend splitting. If the change request reveals that the
  shared module is taking on multiple distinct roles that should
  be separated — i.e., the module's cohesion has decayed — the
  dependent recommends splitting the module into separate
  modules with their own contracts and primary parents. Splitting
  is itself a decomposition decision per §10A.2 applied to the
  shared module's existing scope.
```

The validation right ensures that no single consumer's needs override the others' silently. Reuse depends on consensus among consumers; the right to fork or recommend splitting is the safety valve when consensus cannot be reached.

**Detection of role accumulation.** A shared module that accumulates roles becomes a "kitchen sink" — a single module with several distinct purposes that should have been separate. The detection criterion is the same as §7's *cohesion* criterion: if the module's role can no longer be described in one cohesive sentence, splitting is a candidate. Periodic review of shared modules for cohesion is good hygiene.

**Sub-contracts within a shared module.** A shared module's primary parent may further decompose the shared module's scope per §10A.2. The module's *external interface* (what dependents see) is the primary-parent-level concern; *internal structure* is the delegate's. Internal sub-contracts of the shared module are not visible to the module's dependents.

**Ownership-implementation models.** Within the primary-parent rule above, the *implementation* arrangement may follow one of several models; the choice is itself a decision (§9) and should be recorded in the module's contract:

```text
- Single delegate. The primary parent assigns one delegate. The
  most common case for stable, well-understood modules.

- Coordinator delegate. The primary parent assigns one delegate
  who serves as a coordinator among contributors (which may
  include personnel from other dependents' teams). Change
  requests still flow to the primary parent for governance, but
  the coordinator drives alignment among contributors.

- Multiple co-delegates. Two or more delegates share
  implementation responsibility under the same primary parent's
  governance. Change governance applies — major changes need
  consent of all co-delegates. Most coordination cost; reserved
  for modules where implementation authority must genuinely be
  shared (rare).
```

**The duplication-avoidance discipline.** Symmetrically: a delegate considering implementation of a module that *might* be useful elsewhere in the project should announce it as a candidate shared module (in a form discoverable by the tool's project-wide search) before building. The cost is a brief moment of broadcasting; the benefit is preventing parallel duplicate implementations that have to be reconciled later.

This discipline reframes the delegate's mental sequence:

```text
Old (build-first):    "What do I need? -> I'll build it."
New (reuse-first):    "What do I need? -> Does it exist? Is it
                       planned? -> Reuse if so; build only if
                       not, and announce what I'm building so
                       others can find it."
```

### 10A.10 Last Responsible Moment

Decisions about implementation should be deferred to the **latest level and latest time** at which the decision is actually needed. Higher-level contracts and architecture should describe scope and constraint *without committing* to lower-level implementation choices that should be made by the delegate at the appropriate level.

The benefit is **maximum flexibility and minimum premature constraint**. When a decision must be made, the maximum amount of information is available, and earlier decisions have not pre-committed the lower level to specific approaches that may later prove suboptimal. The detail gradient of the architecture tree naturally follows: project-root contracts are abstract; leaf contracts are concrete; the gradient sharpens with depth.

This is **not procrastination**. The decision is made *when its time comes* — driven by the design loop's progression — not before. The principle is "do not make decisions before they are needed," not "do not make decisions."

The principle interacts directly with two others:

- *AI agents must not make assumptions* (§3.2). When a parent's contract is silent on a lower-level concern, the delegate does not invent an answer. The delegate either applies the loop in their scope to derive one through proper artifacts, or raises the question if it depends on the parent's authority.
- *Reference stability in contract prose* (§10.1). Higher-level contracts cite decisions and requirements rather than embedding specific implementation choices, so deferred decisions remain deferable until they are actually made.

A practical consequence: a parent-authored contract typically *does not* specify the delegate's interface methods, internal structure, or implementation choices — those are the delegate's design work. The parent specifies the *role*, the *scope*, the *requirements that must be satisfied*, and the *constraints that must be honored*. The delegate produces the rest, going through the §3.2 loop within their scope.

### 10A.11 Contracts Evolve

A contract authored at decomposition time is a *draft* that captures the parent's understanding of the sub-scope's role at the moment of decomposition. As the delegate (and any further delegates of theirs) proceed through their iterations, they will surface obstacles, needs, and constraints that the original contract did not anticipate. **The contract is expected to be revised — sometimes many times — as the design progresses.**

Contract revisions follow §13 change governance and §30A classification:

```text
- Minor refinements preserving the contract's role and objective
  are *revisions* (class-2). The contract retains its identifier;
  its version increments.

- Material changes to the contract's role or objective are
  *replacements* (class-1). A new contract with a new identifier
  replaces the original; the original is preserved as superseded.
```

Repeated revision is **normal, not pathological**. A contract may go through many drafts during its scope's design — that is the design process working as intended.

Revisions may also be triggered by *change requests from the delegate*. When a delegate determines that fulfilling their contract requires an amendment to the contract itself, they raise a change request to the parent rather than proceeding under an unstated assumption (per §3.2 *AI agents must not make assumptions*). The parent's response is itself a Decision recorded per §9: accept, reject, or modify the requested change with rationale (per §10A.2 step 5).

The expected pattern: an early contract is sparse and abstract; subsequent revisions add concreteness as lower-level work surfaces what is needed. By the time implementation-done is reached (per §10A.5), the contract's *Provided Interface* and *Required Interface* fields have been populated by the delegate's own design work and accepted by the parent.

### 10A.12 Delegate Reporting

Delegates report up to the parent **when there is something to report**. Reporting is **event-driven, not time-based**:

```text
- A question that requires the parent's answer (open question per
  §15A or clarification per §9).
- A change request that affects the contract's boundary or
  another contract's scope (per §13 and §10A.11).
- A handback at the design-done or implementation-done milestone
  (per §10A.5).
- A risk or assumption that escalates to the parent's level (per
  §15.1 — owner-of-scope addresses every assumption).
```

Between these events, the delegate proceeds without reporting. Continuous status reporting (e.g., daily standups regardless of need) is **not** a requirement of this process; individual projects may add it as a practice, but it is not part of the constitutional discipline.

When a delegate produces **artifacts that may be reusable across the project** — modules, decisions, contracts, catalog entries, role vocabulary additions — these become available to the project's search and discovery layer (per the project's REQ-0330-style discoverability requirements). Other delegates can find and consider them for reuse rather than re-deriving the same work. The reporting cadence here is automatic — the act of recording the artifact in the project tooling makes it discoverable; the delegate does not need to *announce* it explicitly.

### 10A.13 Decomposition Is Bounded by Fitness for Implementation

A risk in any decomposition-as-primary process: each level may defer to a lower level, and the lower level to a still-lower level, producing an unbounded recursion in which no level ever reaches implementation. *Last responsible moment* (§10A.10) and *contracts evolve* (§10A.11) both encourage deferring — without counter-pressure, deferring forever is a real failure mode.

The constitution prevents this through several reinforcing mechanisms:

**1. The mode-selection question gates every decomposition.** §10A.1 says decomposer mode is selected when sub-areas admit *independent development*. The converse is the bound: when the current scope is small enough and well-understood enough that a single worker (human or AI) can implement it directly, **implementer mode is the right choice, not decomposer mode**. The mode-selection question at every iteration is itself a check against unnecessary decomposition.

**2. The fitness-for-implementation check.** Before electing decomposer mode at any iteration, the parent asks:

```text
Could this scope be implemented now by a single worker if we
just assigned it directly?
```

If yes, decomposer mode is over-engineering. Implementer mode is the right choice. Decomposing for the sake of decomposing produces accidental complexity and delays implementation. When in doubt, prefer implementer mode.

**3. Implementation-done milestone forces actual artifact production.** §10A.5 requires that implementation-done produce verified implementation (per the IATD set in glossary §2 and the project's REQ-0160-style verification recording). A contract that never reaches implementation-done has not satisfied its parent's requirements. This applies at every level, including leaves. Decomposing without producing implementation at the leaves is a failure to fulfill requirements, not a successful decomposition.

**4. The driving question keeps asking.** §3.2's driving question — *Are all requirements fulfilled?* — keeps repeating each iteration. A project that decomposes without progressing toward fulfillment never gets a YES from the driving question. The driving question is the recurring alarm that the strategy is wrong if no progress is being made.

**5. Periodic architecture-tree review.** The project owner periodically reviews the architecture tree for depth and progress. Contracts that have been in `in-design` for an extended period without progressing to `implementing` (per the project's REQ-0290-style lifecycle) are candidates for review: is the contract's scope unnecessarily large? Is the delegate stuck on a sub-decision that should be elevated? Is decomposition into smaller and smaller sub-scopes happening without corresponding implementation?

**6. The decompose-vs-leaf rubric (DC1–DC7).** When the mode-selection question (mechanism 1) and the fitness-for-implementation check (mechanism 2) leave the analyst uncertain whether to decompose or stay leaf, a structured rubric supports the decision. The rubric is a §9.1 option-criterion evaluation with criteria pre-committed per PO-17.

The rubric has seven criteria with default weights:

```text
DC1  Cohesion + scope breadth                          weight 18
     Can the contract's role be expressed in one
     coherent sentence? Wider scope or heterogeneous
     responsibilities → favor decompose.

DC2  Sub-area independence                             weight 16
     Could sub-areas be designed and implemented by
     separate workers without continuous cross-
     coordination? Independent → favor decompose.

DC3  Heterogeneous change profile                      weight 12
     Do different parts of the contract's scope evolve
     at materially different rates? Heterogeneous →
     favor decompose.

DC4  Contract clarity at sub-boundaries                weight 14
     If sub-decomposed, would each sub-contract have a
     crisp interface (clean inputs, outputs, role)?
     Yes → favor decompose.

DC5  Verification heterogeneity                        weight 10
     Do sub-areas have materially different verification
     approaches (per glossary §2 IATD)? Heterogeneous
     → favor decompose.

DC6  Boundary value (replaceability + reuse)           weight 12
     Would a sub-boundary enable reuse across the
     project (per §10A.9) or facilitate replaceability
     (per §3.2 insulation-by-abstraction)? Yes → favor
     decompose.

DC7  Single-implementer tractability                   weight 18
     §10A.13.2's fitness-for-implementation check
     expressed as a positive criterion. Can a single
     implementer handle the scope directly within
     reasonable cognitive load? Yes → favor leaf.
```

**Scoring.** Each criterion's contribution is `+` (favors decompose), `−` (favors leaf), or `~` (neutral; does not discriminate). The contribution magnitude is the criterion's weight; the sign is the direction. Sum to get the weighted total.

**Thresholds.**

```text
≥ +20  → DECOMPOSE.    The scope has structural support
                       for sub-contracts.

≤ −20  → LEAF.         The scope is correctly a single
                       implementer's work.

−20 < total < +20      → JUDGMENT CALL. No structural
                       recommendation; invoke §3.2
                       tie-breaking questions to the
                       owner, or escalate to §9.2
                       swing-weight evaluation per
                       PO-15.
```

**Per-contract criterion augmentations.** The DC1–DC7 set is the default. Each contract being audited may have decision-local concerns that materially discriminate beyond DC1–DC7. The recommended pattern is to add 1–3 *augmented criteria* (label them DC-FN-a, DC-FN-b, etc., where FN identifies the contract being audited) capturing concerns specific to that contract. Augmented criteria supplement DC1–DC7 within the same matrix; their weights are pre-committed per PO-17; honest augmentations meet PO-14's anti-gaming heuristics (pre-commitment test; hypothetical-alternative test; reframe-vs-add preference).

**Relationship to per-iteration discipline (PO-19).** DC1–DC7 is the *tool* that operationalizes PO-19's mode-selection-explicit-at-every-iteration discipline. At each iteration, the delegate runs the rubric (or affirms a prior application still holds); the rubric's output is the recorded rationale for the mode-selection answer. "Leaf-mode by inheritance from the parent's decomposition" is **not** sufficient rationale per PO-19; the rubric (or an equivalent recorded analysis) is the proper artifact.

**Rubric stability.** The DC1–DC7 set is recommended as the default but is not constitutionally frozen. A project may add criteria (with anti-gaming guardrails per PO-14), revise weights based on experience, or replace the rubric entirely if it develops a better tool. Changes are §13 governed; the rubric's content is part of the constitution and its evolution follows the constitution's revision discipline.

**Provenance.** The rubric was developed during the project that first exercised this constitution (the *designofeverything* tool project) through retroactive audits of seven top-level sub-contracts (DEC-0410..DEC-0470). It was subsequently exercised in two F-level sub-decompositions (F3 via DEC-0480; F6 via DEC-0500) where the +66 and +98 weighted scores respectively produced unambiguous DECOMPOSE recommendations, and in the four retroactive LEAF affirmations (−58 to −100) where the negative scores produced unambiguous LEAF affirmations. The threshold of ±20 was calibrated against these exercises.

**The forcing function in summary.** Decomposition is a *means*; *fulfillment of requirements* is the *end*. The end is enforced by implementation-done at the leaves, by the driving question's repeated check, and by the parent's responsibility to verify progress. Decomposition that does not lead toward implementation is decomposition that is failing its purpose.

**Practical guidance.** When in doubt about whether to decompose further, prefer implementer mode. Decomposition produces sub-contracts that must be authored, approved, delegated, designed, implemented, and verified — substantial overhead. Implementer mode produces implementation directly. The cost of unnecessary decomposition is real and visible (delayed implementation, more artifacts to maintain, more coordination); the cost of "I'll just build it" at a slightly-large scope is usually less than the cost of unnecessary decomposition.

Related industry practice: this principle parallels several established techniques — YAGNI ("you aren't gonna need it") from XP/Agile, the "walking skeleton" pattern that forces end-to-end implementation early, and INCOSE's design-review gates (SDR, PDR, CDR) that require demonstrated implementation progress at each phase. See RI-0050 for the broader systems-engineering context.

### 10A.14 Approach Selection at Design-Done

Approach selection applies the Generate-Compare-Act pattern (§2A) at two levels: the project-wide approach-selection DEC executes the pattern once (catalog APP-NNNN entries are Stage 1; the framework matrix is Stage 2; the DEC is Stage 3); the per-contract audit verdict at each design-done re-runs the pattern at contract scope.

At every design-done milestone (§10A.5), the parent records an **approach-selection decision** governing the transition from design-done to implementation-done for the contract's scope. The decision is recorded as a §9.1 option-criterion evaluation against the project's adopted approach-selection criteria framework (see §5 catalog for available approach options — APP-NNNN namespace; the project-specific criteria framework is itself a project artifact).

Weights are pre-committed per PO-17 before scoring; anti-gaming guardrails per PO-14 apply. The selected approach is part of the design-done handback package per §10A.5. The parent's acceptance of the handback binds the delegate to the selected approach during implementation. Changing the approach mid-implementation is itself a §13 change requiring rationale.

**Approach selection is recorded at two scope levels** (per DEC-0640 v0.2, 2026-05-24):

```text
1. PROJECT-WIDE approach-selection DEC. At the
   project's first design-done milestone (typically
   the project root), the parent records a project-
   wide approach-selection DEC scoring the framework
   (DV1–DV12 or equivalent) against the project's
   overall characterization. This DEC is the project's
   binding default; sibling contracts inherit unless
   explicitly refined per (2).

2. PER-CONTRACT audit verdict at each subsequent
   design-done. At every subsequent design-done
   milestone, the parent performs a per-contract
   audit verdict that EITHER:

   (a) Confirms the project-wide approach applies
       to this contract — recorded as a brief cite
       of the project-wide DEC + verdict (a few
       sentences within the handback package; §9.3
       verdict vocabulary applies). The verdict
       demonstrates that the framework was actually
       run against this contract's specifics, not
       merely asserted. This avoids opening a per-
       contract DEC when the project-wide default
       holds substantively.

   (b) Refines or replaces the project-wide approach
       for this contract — recorded as a full per-
       contract approach-selection DEC with §9.1
       matrix per the framework. The refinement
       may select a different APP-NNNN, hybridize
       components from multiple, or otherwise
       deviate from the project-wide.
```

The audit verdict at (2) is **mandatory** — silent inheritance from the project-wide DEC into a per-contract design-done is the failure mode this rule prevents, analogous to PO-19's *leaf-mode-by-inheritance* prohibition for mode-selection (§10A.13.6). A handback package without the verdict fails the design-done acceptance check.

Verdict vocabulary follows §9.3: the verdict is either PRESENT-AND-SUBSTANTIVE (the framework was run against this contract's specifics and the project-wide choice applies, with cell-scoring evidence sufficient to demonstrate this), N/A-WITH-RATIONALE (only when a per-contract refinement DEC is opened per (b), at which point the project-wide-inherits verdict is superseded by the per-contract DEC), or FAIL-MUST-REVISE (the verdict's substance is insufficient — the framework was not actually run; the verdict is rubber-stamped).

The constitution does not bind to a specific criteria framework. A project may adopt the DV1–DV12 framework recorded in the designofeverything project's catalog (per RI-0170 and applied in DEC-0570), or refine its own. The framework adopted is itself a recorded decision per §9.

**Relationship to §10A.13.6 (mode-selection rubric).** §10A.14 v0.2's two-level structure (project-wide + per-contract verdict) is the constitutional analogue of §10A.13.6 + PO-19's mode-selection-at-every-iteration discipline. Both make a governance choice explicit at every milestone via a structured framework, with inheritance recorded rather than silently assumed. Together, mode-selection (§10A.13.6) and approach-selection (§10A.14) form the parent's two governance choices at each design milestone.

**§10A.14 version history.** v0.1 (2026-05-21 per DEC-0590): per-contract DEC required at every design-done. v0.2 (2026-05-24 per DEC-0640): two-level structure — project-wide default + per-contract audit verdict — per DEC-0640 O4 hybrid recommendation. The v0.1 framing produced administrative cost the §10A.10 LRM principle warned against; the v0.2 framing preserves rigor (no silent inheritance) while avoiding busy-work DECs for the uniform-default case.

## 11. Architecture Tree

The **architecture tree** decomposes the system into contracts, components, modules, subsystems, and submodules. It is the structural artifact produced by repeated application of the decomposition workflow (§10A) at successive levels of detail.

The architecture tree is **one** of several tree-shaped or graph-shaped structures in a project — not the only one. The project also has:

- The **requirements tree** (§3) — root-level requirements at the top, derived requirements branching downward via derivation links.
- The **artifact-derivation graph** — the broader set of references and dependencies among artifacts (decisions reference requirements; contracts reference decisions and requirements; sub-contracts reference parent contracts; catalog entries are referenced by decisions; etc.). Strictly a directed acyclic graph (DAG) because shared modules (§10A.9) and cross-cutting requirements create multiple incoming references.

When this constitution uses the phrase *the tree* without qualifier, the **architecture tree** is meant. Other structures are named explicitly (*requirements tree*, *artifact graph*, etc.).

### 11.1 Structure

A contract appears in the tree at the level its delegate operates. Each child contract is constrained by its parent contract: the child's objective, inputs, outputs, owns, does not own, and constraints must be consistent with the parent's contract. A delegate may further decompose their assigned scope into sub-contracts, becoming the contract owner for those sub-contracts (§27A). The recursion bottoms out at *leaf contracts*, where the delegate is in implementer mode (§27B) and is producing the design or implementation directly rather than delegating further.

The tree's depth and breadth are project-dependent. Some projects are flat — a single contract; most non-trivial projects have multiple levels.

### 11.2 The tree is generally a tree, but may be a DAG

Strictly, the architecture is a tree only when every sub-contract has exactly one parent. **Shared modules** (§10A.9) introduce nodes that are referenced by multiple parents — making the architecture a *directed acyclic graph* (DAG) rather than a strict tree. The terminology "architecture tree" is retained for clarity in the common case; in projects with shared modules, the structure should be understood as a tree-with-shared-leaves.

Visually:

```text
                    Root contract
                   /      |      \
                  /       |       \
              Sub-A    Sub-B    Sub-C
              /  \      |       /
             /    \     |      /
          Leaf-1  Shared-Leaf-X    Leaf-3
                  (used by Sub-A and Sub-B)
```

The DAG structure is not a violation of the contract concept — each consumer of a shared module references the module's contract in their own contract, and the shared module has an explicit owner per §10A.9.

### 11.3 Authority and approval paths

The approval path for changes follows ownership of the affected contract:

```text
- A delegate may modify artifacts within their own contract's
  scope (subject to REQ-0080 enforcement on the boundary).

- A delegate may NOT modify their own contract — that requires
  a change request to the contract's owner (the parent).

- A delegate may NOT modify a sibling's contract or any
  contract outside their own scope — coordination flows
  through the common parent (§10A.6).

- For a shared module, change governance follows the module's
  declared ownership model (§10A.9) — single owner, coordinator
  owner, or co-owners. Consumers of the shared module are
  notified of proposed changes that may affect them.
```

### 11.4 The tree is a designed artifact, not a found one

The architecture tree does not pre-exist the project; it is produced *by* the project's iterations of the §3.2 loop in decomposer mode. Each pass of decomposition adds nodes (sub-contracts) and edges (parent-child references, depends-on references for shared modules). The tree at any point reflects the project's current understanding of how the system is being structured. Like any design artifact, the tree is subject to revision — sub-contracts may be merged, split, replaced, or relocated as understanding evolves, all under change governance (§13 and glossary §30A).

A key implication: the tree is *queryable* — designers can ask "what is the current architecture tree?" and "what would the tree look like as of baseline X?" (per glossary §30B). The tool implementing this constitution must support these queries (per the project's REQ-0060 traceability and REQ-0220 search).

### 11.5 Functional vs. Physical Aspects of the Architecture Tree

Following INCOSE systems-engineering practice ([Functional Analysis and Allocation](https://acqnotes.com/acqnote/careerfields/functional-analysis-and-allocation); INCOSE Systems Engineering Handbook v5.0), the architecture tree expresses both **functional** and **physical** aspects of the design:

```text
- Functional architecture  — what the system does. Functions,
                             roles, responsibilities, capabilities,
                             and the requirements allocated to
                             each. The "what" view.

- Physical architecture    — how the system is realized.
                             Components, modules, products,
                             services, and the interfaces between
                             them. The "how" view.
```

The two views are **not separate trees**; they are different aspects of *the same architecture tree*. A given contract may emphasize functional content (especially at higher levels) or physical content (especially at lower levels). The distinction is a useful lens for reading and designing contracts.

**Where functional and physical sit in a contract.** A contract whose objective is *"provide storage and persistence"* is primarily functional — it describes the role this scope plays in the larger system. A contract whose objective is *"implement the Datomic schema and on-disk layout"* is primarily physical — it commits to specific implementations. The transition from functional to physical happens through decomposition: each sub-contract is typically more physical than its parent.

**The decomposition gradient.** Per §10A.10 (last responsible moment) and §10.2 (contract is the parent's specification), higher-level contracts stay functional — they describe role and constraint without committing to specific implementations. The physical specifics emerge at lower levels, as delegate design work surfaces them and the contract is refined via change governance (§10A.11).

```text
Project-root contract:    Highly functional. What the system
                          does at the top level.

Intermediate contracts:   Mixed. Functional role with some
                          structural commitments (e.g., "Storage
                          and Repository").

Leaf contracts:           Highly physical. Specific implementation:
                          which technology, which file layout,
                          which method signatures.
```

**Allocation discipline.** Per INCOSE practice, every requirement of the higher-level scope must be allocated to one or more lower-level functions, and each function must trace to one or more requirements. This is what §10A.2 step 4's *"no overlap, no gap"* rule (clarified by PO-12 to allow cross-cutting requirements applying to multiple sub-contracts) enforces in our process.

**When to make the functional/physical distinction explicit in a contract.** Most contracts do not need a *Functional aspect* / *Physical aspect* header; the distinction is implicit in the contract's level in the tree. For projects with strong systems-engineering practice (regulated industries, complex hardware-software systems), the contracts may explicitly label which fields are functional and which are physical. For software-tool projects, the implicit distinction is usually sufficient.

## 12. Traceability Graph

The requirements tree and architecture tree are connected by a traceability graph.

The traceability graph records relationships among requirements, capabilities, assumptions, decisions, contracts, risks, open questions, and change requests.

This graph should allow the design system to answer questions such as:

```text
- Which requirements justify this contract?
- Which decisions depend on this assumption?
- Which contracts are affected by this requirement change?
- Which requirements are not yet covered by architecture?
- Which components exist without clear justification?
- Which owners must approve a proposed boundary change?
```

## 13. Change Governance

Requirements, decisions, and contracts may change, but changes must be governed.

A change request should identify:

```text
- What is proposed to change
- Why the change is needed
- Which requirements, decisions, contracts, and assumptions are affected
- Who owns the affected artifacts
- What review level is required
- Whether the change is approved, rejected, deferred, or superseded
```

Simple changes may be approved immediately. Complex changes may require analysis, negotiation, or coordination across affected contracts.

The level of review is determined by the owner of the affected contract, subject to higher-level contracts and requirements.

### 13.1 Change Classification

Every change to an artifact is classified as a **revision** (preserves identity, version increments, references continue) or a **replacement** (creates a new artifact with a new identifier, original is marked superseded, references are individually evaluated). See glossary §30A for the definitions and §30A.1 for a known open concern: the artifact's owner currently classifies the change, but referencing artifacts may legitimately disagree about whether a particular change is a revision or a replacement. This is recorded as a future-work concern rather than resolved at this stage.

## 14. Automation Objective

The process is intended to support automated and AI-assisted architecture design.

Automation may assist with:

```text
- Deriving capabilities from requirements
- Searching catalogs for candidate mechanisms
- Generating decision-relevant questions
- Recording assumptions
- Comparing options
- Drafting decision records
- Drafting contracts
- Detecting uncovered requirements
- Detecting unjustified components
- Detecting conflicting contracts
- Identifying affected artifacts after a change
```

The long-term objective is to make architectural design faster, more consistent, more reviewable, and potentially increasingly automatable.

Human owners remain responsible for requirements, priorities, approvals, and any decisions that require judgment beyond the current capability of the system.

## 15. Governing Summary

This process treats architecture as a structured, versioned, traceable, and governed decision system.

The process is successful when:

```text
- Requirements are decomposed and uniquely identified.
- Capabilities are derived from requirements.
- Candidate mechanisms are selected from known catalogs.
- Tradeoffs and ambiguities are made explicit when they matter.
- Decisions are justified and versioned.
- Contracts define authority and boundaries.
- Changes follow ownership.
- The architecture remains traceable back to requirements.
```

The standard is not that every architectural decision be inevitable.

The standard is that every important architectural decision be justifiable.

