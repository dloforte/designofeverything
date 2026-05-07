# Architecture Design Process Glossary

## 1. Purpose

This glossary defines key terminology for the requirements-constrained architecture design process.

Within this process, these terms should be used consistently so that requirements, decisions, contracts, ownership, and traceability can be recorded and reasoned about precisely.

## 1A. Artifact

An **artifact** is any governed item recorded under this process — a requirement, decision, contract, catalog option, capability statement, assumption, clarification, open question, traceability link, change request, fulfillment check, invalidation history entry, or any other type the process recognizes. Every artifact is uniquely addressable, owned, versioned, and traceable.

The term *artifact* is the umbrella under which the process's specific types live. An artifact's type is one of its properties, not its immutable identity: over time, an artifact may shift type if its primary purpose evolves (a convention promoted to a requirement, for example). The artifact's unique global identifier remains constant across such shifts.

A reference from one artifact to another is an **artifact reference**. An artifact reference points at its target by the target's eternal global identifier; any human-readable type decoration accompanying the reference (e.g., the `REQ-` in `REQ-90`) is a display convention and not the basis of resolution. References therefore continue to resolve correctly even when the target's type decoration changes.

An artifact reference is distinct from a **traceability link** (§28), which is a labeled, typed edge derived from artifact references for the purpose of querying the design.

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
- One or more verification methods (or a recorded justification if intentionally unverified)
```

A good design expects every requirement to be verified at some level. Common verification methods are **analysis**, **testing**, **inspection**, and **demonstration**. The method appropriate to a given requirement depends on its character: behavioral requirements are typically tested; structural or interface requirements are inspected; mathematical or property-based requirements are analyzed; end-to-end objectives are demonstrated. A requirement that is intentionally not verified should record a justification for the waiver, so the absence of verification is itself an explicit decision.

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

## 11A. Design Space

A **design space** is the set of solutions or implementations that could satisfy a requirement, or a set of requirements considered jointly.

A constrained requirement has a small design space — few viable solutions. A broad or weakly specified requirement has a large design space.

The size and shape of the design space affect design ordering. When multiple requirements are unfulfilled, the design loop (constitution §3.2) prefers to address requirements with the smallest design space first. Committing to a wide-design-space requirement before a constrained one risks eliminating the overlap with the constrained requirement's viable solutions, leaving no joint-feasible design.

Design spaces are not always known precisely. Consulting the catalog (§17) and exploring outside it gives the designer evidence about how large or small a particular design space is.

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

REQ-091 deserves clarification. It originates in a context such as choosing a visual display technology where *a monitor* is the common, mature, low-cost option, and the project has reason to prefer such options for cost, ecosystem, and operational simplicity. The phrasing is intentional but easy to misread: it is **not** an instruction to select an option *because* it is common. Selecting an option on the sole basis of commonness, popularity, or "what is usually done" is never an adequate justification under this process (§19).

The correct reading of an optimization requirement like REQ-091 is: when comparing otherwise-valid candidates, prefer the one whose maturity, ecosystem, or operational characteristics best fit the stated objectives. If those objectives align with commonness — and they often do — the common option will tend to win. The decision is still justified by fit to objectives, not by commonness as a heuristic.

Optimization requirements may require weights, priorities, thresholds, or scoring rules when multiple optimization requirements compete.

## 15. Assumption

An **assumption** is a proposition treated as true for design, reasoning, planning, or evaluation purposes.

An assumption may be temporary, pending confirmation, or it may be durable if the project intentionally chooses to rely on it. The important point is that the assumption is explicitly recorded so that decisions and derived requirements depending on it can be reviewed if the assumption changes or is invalidated.

Assumptions are not requirements by themselves. If an assumption is hardened into an authoritative design obligation, it should be converted into or reflected by a requirement.

Assumptions should be traceable to the decisions, requirements, contracts, or other artifacts that depend on them.

### 15.1 Status, Routing, and Resolution

Assumptions are inherently uncertain. Each one is a proposition the project is treating as true *without verification*. The handling rule reflects that:

```text
1. Every newly-recorded assumption is marked with an explicit
   "unverified" status.

2. The assumption is SENT to the owner of the scope in which it was
   recorded — explicitly, for their evaluation. This is not
   optional visibility; every assumption is routed to its scope
   owner.

3. The owner MUST address every assumption by explicitly accepting,
   rejecting, or converting it (typically into a requirement).
   Leaving an assumption indefinitely unaddressed is not a
   permitted disposition.

4. The preferred path is to address an assumption WITHOUT
   provisional adoption — that is, to obtain owner action before
   proceeding. If an assumption can be addressed without blocking,
   that is the preferred path from the beginning.

5. Provisional adoption exists ONLY to prevent blocking when
   waiting for owner action would stall productive work.
   Provisional adoption does not change the unverified status; the
   assumption remains routed to the owner for action.

6. A completed design contains zero unresolved assumptions. Every
   assumption has been accepted, rejected, or converted before the
   design is regarded as complete.
```

Two emphases are intentional. First, *routing to owners is mandatory* — every assumption is sent for evaluation; this is not a courtesy. Second, *provisional adoption exists only to avoid blocking* — it is not a default, not a convenience, and not a substitute for owner action.

## 15A. Open Question

An **open question** is a recorded prompt for clarification, decision, or further information whose answer is not yet known and is needed (now or later) to make progress.

An open question is not authoritative. It is a context artifact whose purpose is to ensure that pending uncertainties are tracked rather than forgotten.

The lifecycle of an open question typically ends in one of:

```text
- A clarification (which may itself revise or create a requirement)
- A recorded assumption
- A new or revised requirement
- A decision
- A catalog or contract update
```

Open questions should be tracked in a way appropriate to the project's working style. The format is intentionally flexible — for example, a single `open_questions.md` file is acceptable, as is per-question entries in a database, ticketing system, or tool. What matters is that:

```text
- The question is recorded with a unique, short reference.
- The author and the audience for the answer are identifiable.
- The resolution is captured and links to whatever artifact (clarification, assumption, requirement, decision) absorbed the question.
- Resolved questions are kept rather than deleted, for traceability.
```

If an open question is never resolved but its underlying need disappears (because the design moved past it, or another decision rendered it moot), this should be recorded as the resolution.

#### Recommended lifecycle pattern

The following pattern has worked well in practice and is recommended (not mandated) for projects that do not yet have tooling that imposes a different structure:

```text
1. New questions are appended in order, each with a short
   incrementing ID (e.g., Q1, Q2, Q3, ...).

2. While unresolved, the entry contains the question, any context,
   and the audience.

3. When the question is answered, the entry is updated in place:
   marked Resolved, with the answer captured directly under the
   question, and links to whatever artifacts the resolution
   produced (a new requirement, a recorded assumption, a decision,
   etc.).

4. Resolved entries are kept, not deleted, so the document grows
   into a record of what the project considered, discussed, and
   decided.
```

The same lifecycle pattern is appropriate for other commentary-style artifacts that surface during design — for example, observations about gaps or refinements in the process documents themselves. A project may use a parallel file (such as a `process_observations.md` with `PO-1`, `PO-2`, ... entries) following the same incrementing-ID and inline-resolution rules.

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

A catalog option should include its strengths, weaknesses, tradeoffs, risks, commonness, maturity, constraints, and discriminating questions, together with a **last-updated date** so designers can judge how stale the entry may be.

The catalog as a whole is treated as a *snapshot* of what the project currently knows is possible — a cache of the real-world space of solutions, not an authoritative limit on it. See constitution §5.1.

## 18. Tradeoff

A **tradeoff** is a difference between options in which improving one criterion tends to worsen another criterion or impose a cost elsewhere.

Tradeoffs are used to generate decision-relevant questions and compare candidate options.

## 19. Decision

A **decision** is a selected option among alternatives.

A decision is not itself a requirement. It is a reasoning outcome that may lead to new or revised requirements, contracts, allocations, catalog updates, or change requests.

A decision should be recorded when it materially affects architecture, requirements, contracts, implementation authority, cost, risk, or future flexibility.

A decision must be **justifiable** in terms of authoritative inputs — requirements, constraints, tradeoffs, recorded assumptions. Typicality, convention, common practice, or "what is usually done" is not by itself a sufficient justification. A common option may be selected, but only when its selection is justified by fit to the project's stated objectives. Commonness or maturity may appear as one input to a tradeoff analysis, but commonness alone never carries a decision.

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

A contract proscribes the *shell* of a design space — the boundaries within which the delegate works. Inside the shell, the delegate has full freedom to choose any implementation that meets the contract. The shell itself is owned and may not be altered without governed change.

The skyscraper analogy is useful: the designer of a single floor is given the floor's outer dimensions, structural supports, and connection points for electrical, mechanical, and plumbing systems. Within those, the floor designer chooses freely. The boundaries themselves belong to a higher-level designer.

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

## 27A. Delegate

A **delegate** is the person, role, group, or AI agent responsible for working *within* a contract's boundary.

The contract owner has authority over the *shell* — the contract's objective, inputs, outputs, and constraints. The **delegate** has authority over implementation choices *inside* the shell. The delegate does not have authority to modify the shell itself; any change to the shell must be routed back to the contract owner via change governance (§13 of the constitution).

When the design loop recurses inside a contract (constitution §3.2), the role running the loop within that scope is the delegate, not the contract owner. A delegate may itself produce sub-contracts in the course of decomposing the work — and becomes the contract owner for those sub-contracts, delegating further. Roles therefore flip at each delegation level: the delegate of an outer contract is the contract owner of any inner contracts they author.

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

## 30A. Change Classification

Changes to artifacts are classified by their impact on artifact identity:

- **Revision** (*class 2 change*) — the change preserves the essence of the artifact. The artifact retains its global identifier; its version increments. References to the artifact continue to point to the same identifier and are migrated forward (REQ-0140). Revisions cover minor edits, clarifications, refinements, corrections, and additions that do not alter the artifact's load-bearing meaning.

- **Replacement** (*class 1 change*) — the change is large enough that the result is, in essence, a different artifact. The original retains its identifier and is marked **superseded**; a new artifact with a new global identifier is created. References to the old identifier are individually evaluated under change governance (§13 of the constitution): each may be redirected to the new artifact (if still valid in spirit), or recorded with an invalidation history entry (REQ-0170) and remediated.

Replacement is a deliberate exception to REQ-0140's "do not preserve historical versions wholesale" rule: the superseded artifact is preserved precisely because a distinct new artifact replaced it, and the fact of the replacement is part of the project's reasoning history.

The terms **revision** and **replacement** are preferred in everyday use; the parenthetical *class 1 / class 2* tags are retained for cross-reference with configuration-management practice, where the same distinction governs review and authority requirements.

### 30A.1 Open concern: classification authority

The classification of a change (revision vs. replacement) is currently the responsibility of the artifact's owner. This is the simplest rule but has a known weakness: a small textual edit that the owner of artifact A views as a *revision* may, when read by the owner of an artifact B that references A, change enough of the meaning to be a *replacement* from B's perspective. A and B may legitimately disagree on the class of the same change.

This concern is recorded as a known limitation. A more conservative mechanism — for example, requiring every artifact that references A to evaluate any revision of A and either accept the unchanged reference or raise a concern to A's owner — would be safer but more costly. The process does not yet require this; it is captured here so that the issue is not silently absorbed and can be revisited as the process gains experience.

## 31. Change Request

A **change request** is a proposed modification to a requirement, decision, contract, assumption, catalog entry, or other governed artifact.

A change request should identify what is proposed, why it is needed, what artifacts are affected, who owns those artifacts, and what approval is required.

