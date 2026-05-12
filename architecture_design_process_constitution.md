# Requirements-Constrained Architecture Design Process

## 1. Purpose

This document defines a formal process for deriving, evaluating, justifying, and governing software architecture from requirements.

The purpose of the process is to reduce reliance on intuition alone by making architectural reasoning explicit, traceable, repeatable, reviewable, and justifiable.

The process does not assume that requirements uniquely determine a single correct architecture. Instead, it treats architecture as a requirements-constrained decision process in which valid candidate architectures are generated, compared, selected, recorded, and governed.

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

**Category-first generation.** When the candidate space is large or unfamiliar, generate candidates at the *category* or *class* level first (file-tree approaches, graph databases, relational with polymorphic associations, RDF triplestores, etc.) rather than jumping to specific products or implementations. Evaluate categories against the most-constraining requirements; only after a category is selected, drill down to specific products or implementations within it. This is complementary to rejection-driven refinement: where rejection-driven refinement uses a rejected proposal to surface missing constraints, category-first generation reduces evaluation cost by eliminating whole classes of options before investing in product-specific detail.

**Research investigation handoff.** Research investigations (glossary §15B) may be opened from any point in the design — DECIDE evaluation when the catalog lacks needed information, an open question that requires research to resolve, an assumption that needs validation, a contract definition that needs further information, or as a standalone task. Research investigations are *not* exclusive to DECIDE; they are a general mechanism for gathering information whenever the design needs it. The artifact that triggered the investigation waits on the findings, or proceeds under a recorded assumption (glossary §15) if waiting would block productive work. Findings typically update or create catalog entries (constitution §5.2) and feed back into the triggering artifact. Investigations are themselves of two kinds (glossary §15B.1): *survey investigations* search what already exists, while *constructive investigations* develop something new.

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

**The forcing function in summary.** Decomposition is a *means*; *fulfillment of requirements* is the *end*. The end is enforced by implementation-done at the leaves, by the driving question's repeated check, and by the parent's responsibility to verify progress. Decomposition that does not lead toward implementation is decomposition that is failing its purpose.

**Practical guidance.** When in doubt about whether to decompose further, prefer implementer mode. Decomposition produces sub-contracts that must be authored, approved, delegated, designed, implemented, and verified — substantial overhead. Implementer mode produces implementation directly. The cost of unnecessary decomposition is real and visible (delayed implementation, more artifacts to maintain, more coordination); the cost of "I'll just build it" at a slightly-large scope is usually less than the cost of unnecessary decomposition.

Related industry practice: this principle parallels several established techniques — YAGNI ("you aren't gonna need it") from XP/Agile, the "walking skeleton" pattern that forces end-to-end implementation early, and INCOSE's design-review gates (SDR, PDR, CDR) that require demonstrated implementation progress at each phase. See RI-0050 for the broader systems-engineering context.

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

