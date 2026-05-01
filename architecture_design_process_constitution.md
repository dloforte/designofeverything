# Requirements-Constrained Architecture Design Process

## 1. Purpose

This document defines a formal process for deriving, evaluating, justifying, and governing software architecture from requirements.

The purpose of the process is to reduce reliance on intuition alone by making architectural reasoning explicit, traceable, repeatable, reviewable, and justifiable.

The process does not assume that requirements uniquely determine a single correct architecture. Instead, it treats architecture as a requirements-constrained decision process in which valid candidate architectures are generated, compared, selected, recorded, and governed.

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

### 3.1 Hard Requirements

Hard requirements are pass/fail constraints. A candidate architecture that violates a hard requirement is invalid unless the requirement is changed by its owner.

### 3.2 Optimization Objectives

Optimization objectives define qualities to maximize or minimize, such as latency, cost, reliability, maintainability, commonness of technology, development speed, or implementation risk.

Candidate architectures that satisfy all hard requirements are compared against these optimization objectives.

## 4. Capability Derivation

Requirements are translated into required system capabilities.

A capability is something the system must be able to do in order to satisfy one or more requirements.

Capabilities should be derived before modules are named. This prevents premature architectural decomposition and helps distinguish what the system must accomplish from how responsibility should be divided.

Each capability should reference the requirement or requirements that justify its existence.

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

A contract defines the boundary, responsibility, authority, and constraints of a component, module, subsystem, or service.

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

