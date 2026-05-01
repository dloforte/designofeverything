# Architecture Design Process Glossary

## 1. Purpose

This glossary defines key terminology for the requirements-constrained architecture design process.

Within this process, these terms should be used consistently so that requirements, decisions, contracts, ownership, and traceability can be recorded and reasoned about precisely.

## 2. Requirement

A **requirement** is a normative statement about what must be true of the project, system, process, architecture, component, contract, implementation, operation, or outcome.

A requirement creates an obligation. It may describe a function, constraint, quality, interface, behavior, policy, performance target, legal obligation, organizational rule, verification need, or architectural condition.

Every requirement should have:

```text
- A unique identifier
- An origin
- An owner
- A status
- A version
- A scope
- A rationale or justification
- Links to related requirements, decisions, contracts, assumptions, and verification methods
```

## 3. Requirement Classification

Requirement types are not always mutually exclusive. A requirement may have multiple characteristics, such as being root-level, derived, allocated, performance-related, interface-related, constraint-related, or risk-related at the same time.

To avoid unnecessary complexity, this process distinguishes between a requirement's primary type and its secondary labels.

The **primary type** identifies the most important role the requirement plays for interpretation, review, ownership, and verification. The primary type should usually be the characteristic that most affects how the requirement is managed.

Secondary labels may be used to record additional characteristics without forcing the requirement into only one conceptual category.

A requirement should be split into multiple requirements when it contains multiple independently meaningful obligations, when different owners are responsible for different parts, when different verification methods are needed, or when one part could change without changing the other.

The purpose of classification is to support reasoning and governance, not to create unnecessary administrative burden.

## 4. Requirement Authority and Origin

Within this process, requirements are the authoritative artifacts that drive the design.

A requirement has one of two authority origins:

```text
1. Root authority
   The requirement comes from an external authority outside the internal requirements tree.

2. Derived authority
   The requirement is derived from one or more existing requirements.
```

External authorities may include a project owner, customer, law, regulation, contract, business objective, mission statement, safety standard, or organizational policy.

Internal context artifacts such as decisions, clarifications, assumptions, catalog options, tradeoff analyses, and risk analyses may explain why a requirement was created, changed, derived, or revised. However, they do not replace requirement authority. They provide reasoning context for how existing requirements led to new or revised requirements.

A requirement without an origin and authority is not sufficiently justified within this process.

## 5. Root-Level Requirement

A **root-level requirement** is a requirement that does not depend on any other requirement for its authority within the current project.

A root-level requirement may come directly from the project owner, customer, mission statement, business objective, law, regulation, or other external source of authority.

Root-level requirements may be discovered after the project begins. Root-level does not mean earliest-known. It means not derived from another requirement inside the current requirement structure.

## 6. Top-Level Requirement

A **top-level requirement** is a high-authority requirement near the top of the requirements structure.

In many projects, top-level requirements and root-level requirements may be the same. However, a top-level requirement may also be a major child requirement beneath a broader root-level requirement.

Example:

```text
REQ-000: Provide a tool that allows users to interact with maps.
  REQ-010: Users shall be able to view maps.
  REQ-020: Users shall be able to search for places.
  REQ-030: Users shall be able to get directions.
```

Here, `REQ-000` is root-level. `REQ-010`, `REQ-020`, and `REQ-030` may be treated as top-level functional requirements under that root.

## 7. Derived Requirement

A **derived requirement** is a requirement whose authority comes from one or more existing requirements.

A derived requirement exists because it is necessary or useful to satisfy, preserve, clarify, allocate, verify, or operationalize another requirement.

A derived requirement may be explained by context artifacts such as decisions, assumptions, clarifications, catalog options, tradeoff analyses, physical facts, technology choices, decomposition logic, or risk analysis. These artifacts help explain the reasoning, but the authority of the derived requirement still flows from requirements.

A derived requirement must reference the requirement or requirements from which it is derived.

## 8. Parent and Child Requirements

**Parent** and **child** describe a relationship between requirements, not separate artifact types.

When one requirement is derived from another requirement, the originating requirement may be called the **parent requirement**, and the derived requirement may be called the **child requirement**.

In the simplest case:

```text
Parent requirement → Child requirement
Existing requirement → Derived requirement
```

A child requirement is usually a derived requirement. A derived requirement may depend on multiple parent requirements. In that case, it has multiple derivation links rather than a single parent.

The parent-child relationship should explain why the child requirement exists in relation to the parent requirement.

Example:

```text
REQ-000: Transport 4 people from point A to point B within time T.
  REQ-010: The system shall carry at least 4 people.
  REQ-020: The system shall move between point A and point B.
  REQ-030: The system shall complete the trip within time T.
```

Here, `REQ-000` is the parent requirement. `REQ-010`, `REQ-020`, and `REQ-030` are child requirements derived from it.

## 9. Clarification

A **clarification** is an answer, interpretation, or explanation that resolves uncertainty in the meaning, scope, priority, or intent of a requirement.

A clarification is usually a context or history artifact rather than an authoritative requirement by itself.

A clarification may result in:

```text
- A revised requirement
- A new derived requirement
- A new root-level requirement from an external authority
- An assumption being confirmed or rejected
- A decision record update
- A non-goal
- A priority change
```

Clarifications should be recorded when they materially affect requirements, decisions, contracts, or design reasoning.

## 10. Capability

A **capability** is an ability the system or one of its parts must possess in order to satisfy one or more requirements.

Within this process, capability is not a primary authoritative artifact type. It is a reasoning concept, requirement label, or descriptive style used to identify what the system must be able to do before deciding how that ability should be implemented, allocated, or governed.

If a capability drives the design authoritatively, it should be captured as a requirement.

Example:

```text
Informal capability statement:
The system needs the ability to produce visual map output.

Derived capability requirement:
REQ-021: The system shall produce visual map output.
Origin: Derived from REQ-010.
```

Capabilities may still be discussed inside decision records, catalog analyses, contracts, and design rationale. However, they should not become an untracked layer between requirements and architecture.

## 11. Constraint

A **constraint** is a requirement that limits the solution space.

Constraints may come from law, platform, budget, schedule, hardware, standards, architecture decisions, organizational policy, compatibility needs, or physical reality.

Example:

```text
The application shall run on commodity mobile devices.
```

## 12. Objective

An **objective** is a statement of intent, purpose, or desired direction.

Within this process, an objective is not necessarily a separate artifact type. If an objective drives the design authoritatively, it should be represented as a requirement. If it merely explains intent, it may appear as context, clarification, rationale, or a contract objective field.

Examples:

```text
Root-level objective requirement:
REQ-000: Provide a tool that allows users to interact with maps for common map-related tasks.

Optimization objective requirement:
REQ-090: Minimize map interaction latency while satisfying all mandatory requirements.

Contract objective field:
The renderer's objective is to produce visual map output from map scene state.
```

The term objective should therefore be interpreted by where it appears:

```text
- In the requirements tree: an authoritative requirement.
- In a contract: the purpose of that contract boundary.
- In a decision record or clarification: explanatory context or intent.
```

## 13. Mandatory Requirement

A **mandatory requirement** is a pass/fail requirement. A candidate that violates a mandatory requirement is invalid unless the requirement is changed by its owner.

In ordinary usage within this process, the word **requirement** normally implies a mandatory requirement unless the requirement is explicitly classified as an optimization requirement, preference, objective, or other non-pass/fail form.

The term **mandatory requirement** is useful when it is necessary to distinguish pass/fail requirements from optimization requirements.

## 14. Optimization Requirement

An **optimization requirement** is an authoritative requirement that defines a direction to maximize, minimize, improve, reduce, or optimize rather than a simple pass/fail condition.

Optimization requirements are used to compare otherwise-valid candidates.

Examples:

```text
REQ-090: Minimize map interaction latency while satisfying all mandatory requirements.
REQ-091: Maximize use of common, mature technologies unless another requirement justifies a less common choice.
REQ-092: Minimize implementation risk for the first release.
```

Optimization requirements may require weights, priorities, thresholds, or scoring rules when multiple optimization requirements compete.

## 15. Assumption

An **assumption** is a proposition treated as true for design, reasoning, planning, or evaluation purposes.

An assumption may be temporary, pending confirmation, or it may be durable if the project intentionally chooses to rely on it. The important point is that the assumption is explicitly recorded so that decisions and derived requirements depending on it can be reviewed if the assumption changes or is invalidated.

Assumptions are not requirements by themselves. If an assumption is hardened into an authoritative design obligation, it should be converted into or reflected by a requirement.

Assumptions should be traceable to the decisions, requirements, contracts, or other artifacts that depend on them.

## 16. Mechanism

A **mechanism** is a known way to provide a capability or produce a required effect.

Examples:

```text
- Electronic display
- Projector
- Local database
- Remote API
- Client-side renderer
- Server-rendered tiles
- Message queue
```

## 17. Catalog Option

A **catalog option** is a recorded mechanism, pattern, technology, or architectural choice that may be selected to satisfy a capability or requirement.

A catalog option should include its strengths, weaknesses, tradeoffs, risks, commonness, maturity, constraints, and discriminating questions.

## 18. Tradeoff

A **tradeoff** is a difference between options in which improving one criterion tends to worsen another criterion or impose a cost elsewhere.

Tradeoffs are used to generate decision-relevant questions and compare candidate options.

## 19. Decision

A **decision** is a selected option among alternatives.

A decision is not itself a requirement. It is a reasoning outcome that may lead to new or revised requirements, contracts, allocations, catalog updates, or change requests.

A decision should be recorded when it materially affects architecture, requirements, contracts, implementation authority, cost, risk, or future flexibility.

## 20. Decision Record

A **decision record** is a versioned reasoning artifact that captures how a decision was made.

A decision record acts like a black box with recorded inputs and outputs.

Typical inputs include:

```text
- Relevant requirements
- Assumptions
- Catalog options
- Clarifications
- Risk analysis
- Constraints
- Evaluation criteria
- Tradeoffs considered
```

Typical outputs may include:

```text
- The selected decision
- Rejected alternatives
- New or revised requirements
- Contract implications
- Architecture tree changes
- Allocation changes
- Risks accepted
- Review triggers
- Follow-up questions or change requests
```

The purpose of a decision record is to preserve reasoning that would otherwise be lost. When requirements, assumptions, costs, technologies, or project priorities change, the decision record allows the project to review whether the logic that produced the current design is still valid.

Decision records should not be overwritten. If a decision changes, the old decision should be preserved and marked as superseded.

## 21. Contract

A **contract** is a boundary definition for a component, module, subsystem, service, person, team, or delegated scope of work.

A contract defines objective, inputs, outputs, ownership, exclusions, constraints, authority, and change rules.

The core contract principle is:

```text
As long as you meet these requirements, you may choose the implementation inside this boundary.
```

## 22. Component

A **component** is an architectural unit that performs responsibilities within a contract boundary.

A component may be implemented as a module, service, library, process, database, external dependency, or other technical unit.

## 23. Module

A **module** is a component with a defined internal boundary, interface, and responsibility set, usually within a software codebase.

## 24. Subsystem

A **subsystem** is a larger architectural unit composed of multiple components, modules, services, contracts, or internal subcontracts.

## 25. Allocation

**Allocation** is the act of assigning a requirement, capability, responsibility, constraint, or decision to a contract, component, module, subsystem, person, or team.

Allocation does not erase the origin of the requirement. It records where responsibility for satisfying it is placed.

## 26. Owner

An **owner** is the person, role, group, or authority responsible for an artifact, requirement, decision, contract, or scope.

Ownership includes authority to approve, reject, revise, or delegate changes, subject to higher-level requirements and contracts.

## 27. Authority

**Authority** is the right to make or approve changes to an artifact or scope.

Authority follows ownership unless otherwise specified by a higher-level contract or governance rule.

## 28. Traceability Link

A **traceability link** is a recorded relationship between artifacts.

Traceability links allow the process to answer questions such as:

```text
- Which requirements justify this contract?
- Which decisions depend on this assumption?
- Which contracts are affected by this requirement change?
- Which requirements remain uncovered?
```

## 29. Status

A **status** indicates the current governance state of an artifact.

Common statuses include:

```text
- Draft
- Proposed
- Active
- Rejected
- Superseded
- Deprecated
- Under Review
```

## 30. Version

A **version** identifies a specific historical state of an artifact.

Versioning preserves the reasoning history of requirements, decisions, contracts, and other artifacts.

## 31. Change Request

A **change request** is a proposed modification to a requirement, decision, contract, assumption, catalog entry, or other governed artifact.

A change request should identify what is proposed, why it is needed, what artifacts are affected, who owns those artifacts, and what approval is required.

