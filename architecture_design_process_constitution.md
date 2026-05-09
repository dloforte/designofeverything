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
   Approval transitions the contract from drafted to approved
   (per REQ-0290's lifecycle states).

6. Assign each contract to a delegate.
   The delegate may be a human or an AI agent. Assignment may
   precede or follow approval depending on project conventions.
```

### 10A.3 The handoff package

When a contract is assigned and approved, the delegate receives a **handoff package** — the bounded context within which they will operate. The package comprises:

```text
- The contract itself (full §10 / glossary §21 fields).
- The current version of the constitution and glossary.
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

### 10A.5 The handback

When the delegate's scoped driving question answers YES with rationale, the delegate produces a **handback** to the parent:

```text
- The implementation or design output the contract called for.
- A verification record demonstrating the contract's requirements
  have been met (per the verification expectation in glossary §2).
- The decisions made internally during the delegate's iteration —
  these decisions inherit visibility to the parent for review and
  remain part of the project's reasoning history.
- Any sub-contracts the delegate authored, in their final state.
- The contract's lifecycle state transition: in-design or
  implementing → fulfilled (per REQ-0290).
```

The parent reviews the handback. If the parent is satisfied, the parent accepts and updates their own state — the requirements the contract covered are now fulfilled at the parent level. If not satisfied, the parent either negotiates revisions (the contract returns to in-design) or rejects (the contract transitions to rejected; a new contract may be drafted to cover the same scope).

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

**Ownership models for shared modules.** A shared module's ownership may be structured in one of several ways; the choice is itself a decision (§9) and should be recorded in the module's contract.

```text
- Single owner. One delegate owns the shared module's contract
  and implementation. Consumers raise change requests to the
  owner if they need amendments. Simplest model; appropriate
  when the module's design is stable and one delegate has the
  expertise.

- Coordinator owner. One delegate has nominal ownership but acts
  as a coordinator among multiple stakeholders (consumers and
  contributors). Change requests go to the coordinator who runs
  alignment with stakeholders before approving. Appropriate
  when the module's contract is still evolving and consumer
  needs vary.

- Multiple co-owners. Two or more delegates share ownership.
  Change governance applies — major changes require the consent
  of all co-owners. Most coordination cost; reserved for modules
  where governance authority must genuinely be shared.
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

## 11. Architecture Tree

The **architecture tree** decomposes the system into contracts, components, modules, subsystems, and submodules. It is the structural artifact produced by repeated application of the decomposition workflow (§10A) at successive levels of detail.

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

