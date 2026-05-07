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

**Three exits per node.** For each unfulfilled requirement, choose one of three exits according to the requirement's current state:

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

**Recursion at delegated scopes.** When DECIDE produces a contract (§10), the contract's **delegate** (glossary §27A) treats their assigned shell as a fresh root and runs the loop inside it. The contract owner retains authority over the shell; the delegate has freedom inside the shell but cannot alter it. Each contract therefore creates a fresh mini-project at its delegation level. A delegate may itself author sub-contracts, becoming the contract owner of those sub-contracts.

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

## 11. Architecture Tree

The architecture tree decomposes the system into contracts, components, modules, subsystems, and submodules.

Each child contract is constrained by its parent contract. A delegated owner may further decompose their assigned contract, but may not change the outer contract without approval from the owner of that contract.

The approval path for boundary changes follows ownership of the affected contract.

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

