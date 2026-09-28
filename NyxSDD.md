# NyxSDD — Workflow Specification

## 1. Objective

Define a structured, traceable, and verifiable process for AI-assisted software development.

The process is optimized for work where an AI agent produces most of the artifacts and a human reviews and approves them. Every artifact is therefore machine-readable, individually addressable, and explicit about what remains unresolved.

## 2. Artifact Model

NyxSDD distinguishes two levels of artifacts.

**Project-level** artifacts are written once and maintained over the life of the product. They constrain every feature.

| Artifact | Purpose | Stage |
| --- | --- | --- |
| `principles.md` | Stack, conventions, and standing constraints | Set up before the first feature |
| `product.md` | Product context and problem space | Discovery |

**Feature-level** artifacts are produced per feature and describe a single unit of delivered change.

| Artifact | Purpose | Stage |
| --- | --- | --- |
| `spec.md` | Requirements | Specify |
| `design.md` | Technical solution | Design |
| `tasks.md` | Implementation plan and task status | Tasks |
| `verification.md` | Evidence that the feature satisfies its spec | Verify |
| `changes.md` | Record of changes to approved artifacts | Any stage after approval |

### Directory layout

```
.nyx/
  principles.md
  product.md
  features/
    F-001-user-authentication/
      spec.md
      design.md
      tasks.md
      verification.md
      changes.md
```

A feature directory is named `<feature-id>-<slug>`. The slug is descriptive and has no semantic meaning; the identifier is authoritative.

## 3. Identifiers

Every referenceable element carries an identifier. Traceability rules depend on these identifiers, so they are mandatory, not conventional.

| Prefix | Element | Defined in |
| --- | --- | --- |
| `F-` | Feature | directory name |
| `REQ-` | Functional requirement | `spec.md` |
| `NFR-` | Non-functional requirement | `spec.md` |
| `BR-` | Business rule | `spec.md` |
| `AC-` | Acceptance criterion | `spec.md` |
| `Q-` | Open question | any artifact |
| `AD-` | Architectural decision | `design.md` |
| `T-` | Task | `tasks.md` |
| `CH-` | Change record | `changes.md` |
| `CON-` | Standing constraint | `principles.md` |

Rules:

1. Identifiers are numeric, zero-padded to three digits, and assigned sequentially within their artifact: `REQ-001`, `REQ-002`.
2. Identifiers are scoped to their feature. Within a feature, `REQ-001` is unambiguous. Across features, the qualified form is used: `F-001/REQ-001`.
3. Identifiers are never reused and never renumbered. A removed requirement is marked removed and its identifier is retired.
4. A reference to a non-existent identifier is a workflow error.

## 4. Workflow

The workflow consists of six stages:

1. Discovery
2. Specify
3. Design
4. Tasks
5. Implement
6. Verify

Discovery is a project-level stage and runs once, revisited as the product evolves. Stages 2 through 6 run per feature.

## 5. Stage Definitions

### Discovery

> Status: draft. This stage is deliberately expressed as questions rather than as a template. It is being written collaboratively and will be expanded.

Define the context of the product and the problems to solve, before any feature is specified.

Output:

* `product.md`

Discovery is conducted as a set of questions. The agent asks; the human answers; the agent records the answers in `product.md`. The intended question areas are:

**Context**

* What is this product, described in one paragraph?
* Who uses it, and in what situation?
* What exists today, and what is being replaced or extended?

**Problems**

* Which problems does this product solve?
* For each problem: who experiences it, and what does it cost them today?
* Which problems is this product explicitly not solving?

**Value**

* How is this product useful, and to whom?
* What would change for a user if it worked perfectly?
* How would we know it is working?

**Boundaries**

* Which constraints are fixed and not open to negotiation?
* Which assumptions are we making that we have not verified?

Unanswered questions are recorded as `Q-` entries rather than guessed at.

### Specify

Define the functional and non-functional requirements of a feature.

Output:

* `spec.md`

The specification must include:

* Feature objective
* Scope
* Functional requirements, each with a `REQ-` identifier
* Non-functional requirements, each with an `NFR-` identifier, when applicable
* Business rules, each with a `BR-` identifier
* Acceptance criteria, each with an `AC-` identifier and the requirements it covers
* Out-of-scope items
* Open questions, each with a `Q-` identifier, if any

Acceptance criteria are written so that they can be evaluated without interpretation:

```
AC-001 — Covers: REQ-001, BR-002
Given a user with an expired session
When the user submits a request to a protected endpoint
Then the response status is 401 and no state is modified
```

Every functional requirement must be covered by at least one acceptance criterion.

### Design

Define the technical solution based on the approved specification.

Output:

* `design.md`

The design must include, when applicable:

* Architectural decisions, each with an `AD-` identifier and the requirements it addresses
* Components affected
* Interfaces and contracts
* Data model changes
* Security considerations
* Testing strategy, stating how each `AC-` will be verified
* Relevant technical constraints, referencing `CON-` entries from `principles.md` where they apply

The design must not introduce requirements. A solution that requires behavior absent from the specification is a change to the specification and follows section 8.

### Tasks

Decompose the approved design into implementation tasks.

Output:

* `tasks.md`

Each task must include:

* Unique identifier (`T-`)
* Description
* Related requirements or architectural decisions
* Dependencies, as task identifiers
* Expected outcome
* Completion criteria
* Status

Task status is one of:

| Status | Meaning |
| --- | --- |
| `todo` | Not started |
| `doing` | In progress |
| `blocked` | Cannot proceed; the blocker is recorded |
| `done` | Completion criteria met and evidence recorded |
| `cancelled` | No longer required; the reason is recorded |

### Implement

Execute the implementation tasks according to the approved specification and design.

Expected outputs:

* Source code changes
* Automated tests
* Updated task statuses
* Execution evidence

The implementation must not silently change approved requirements or architectural decisions.

**Execution evidence** is the record that a task's completion criteria were met. For a task to be `done`, its evidence must include:

* The commit or change reference containing the work
* The names of the automated tests covering it, and their result
* The command that was run and the relevant portion of its output
* Any artifact produced, such as a migration or a generated file

Evidence is a record of something that was observed. An assertion that a task is complete is not evidence.

### Verify

Evaluate whether the implementation satisfies the specification and design.

Output:

* `verification.md`

Verification must consider:

* Acceptance criteria, each with a verdict
* Build results
* Automated test results
* Relevant static analysis
* Requirement-to-test traceability
* Deviations from the approved design
* Remaining issues

Each acceptance criterion receives exactly one verdict:

| Verdict | Meaning |
| --- | --- |
| `verified` | Evaluated and satisfied, with evidence |
| `failed` | Evaluated and not satisfied |
| `unverified` | Not evaluated, or evidence insufficient |
| `n/a` | Out of scope for this feature, with justification |

Absence of a verdict is treated as `unverified`. Verification never infers a verdict from the absence of a failure.

## 6. Project Principles

`principles.md` records the decisions that apply to every feature, so that they are not re-litigated in each specification and design.

It must include:

* Technology stack and versions
* Code conventions and project structure
* Testing requirements, including what must be covered and by which kind of test
* Standing constraints, each with a `CON-` identifier
* Decisions that are settled and not open for reconsideration per feature

Specifications and designs must comply with `principles.md`. A feature that requires an exception states it explicitly and references the `CON-` entry it departs from.

## 7. Artifact Lifecycle and Approval

Each artifact carries front matter recording its state:

```yaml
---
id: F-001
artifact: spec
status: approved
version: 2
approved_by: <name>
approved_at: <date>
---
```

Status is one of:

| Status | Meaning |
| --- | --- |
| `draft` | Being written; downstream stages must not start |
| `review` | Submitted for human review |
| `approved` | Approved; downstream stages may proceed |
| `superseded` | Replaced by a later version |

### Gates

A gate is a point at which the workflow stops until an artifact is approved.

| Gate | Requires | Permits |
| --- | --- | --- |
| G1 | `spec.md` approved | Design |
| G2 | `design.md` approved | Tasks |
| G3 | `tasks.md` approved | Implement |
| G4 | `verification.md` recorded | Completion |

The framework must support human review and approval at each gate. Which gates require explicit human approval is configurable; a gate that is not configured for human approval is still recorded as passed, with the approver identified as the agent.

An artifact in `draft` or `review` never satisfies a gate.

## 8. Change Management

A change to an approved artifact is recorded in `changes.md`. Each record includes:

* Identifier (`CH-`)
* Date
* The artifact and the identifiers affected
* What changed, stated as before and after
* Why it changed
* The impact review result
* Who approved it

The artifact's version is incremented and its previous version marked `superseded`.

### Impact review

An impact review determines what downstream work is invalidated by a change. It is performed whenever an approved artifact changes, and it produces:

1. The list of downstream identifiers that reference the changed element, found by following identifier references.
2. For each one, a verdict: unaffected, requires update, or requires removal.
3. For each affected task already `done`, whether its evidence remains valid. Evidence that no longer demonstrates the current criteria is invalidated and the task returns to `todo`.
4. For each affected acceptance criterion already `verified`, whether its verdict stands. A verdict whose basis changed returns to `unverified`.

A change whose impact review is incomplete does not permit implementation to continue.

## 9. Flow Scale

Running six stages for a one-line fix is disproportionate, and a process that is disproportionate is abandoned. NyxSDD defines two paths.

**Full path.** All stages and all artifacts. Required when the change introduces or alters behavior visible to a user, changes an interface or data model, affects security or data integrity, or touches more than one component.

**Lite path.** A single `spec.md` containing the objective, the affected requirement or defect, acceptance criteria, and the verification verdicts. No separate `design.md` or `tasks.md`. Permitted when the change meets all of these:

* It does not alter approved requirements.
* It does not change an interface, contract, or data model.
* It is confined to one component.
* Its acceptance criteria can be stated in three criteria or fewer.

The path is chosen and recorded before Specify begins. A lite-path change that turns out to violate any condition above is promoted to the full path, and the promotion is recorded as a `CH-` entry.

All traceability, evidence, and verdict rules apply to both paths. The lite path reduces the number of artifacts, never the standard of evidence.

## 10. Agent Responsibilities

The agent produces artifacts; the human decides. This section defines the boundary.

### What the agent does without asking

* Draft any artifact, and revise it in response to review.
* Perform the impact review and report its result.
* Implement tasks that are `todo` in an approved `tasks.md`, and record their evidence.
* Run builds, tests, and static analysis, and record the results.
* Record a verdict of `failed` or `unverified`, including against its own work.

### What requires a human decision

* Approval at any gate configured to require it.
* Any change to an approved artifact.
* Any departure from a `CON-` entry in `principles.md`.
* Choosing between alternatives where the specification is silent and the choice affects behavior the specification describes.
* Promotion from the lite path to the full path.

### Open questions

An open question is recorded as a `Q-` entry, never resolved by assumption.

* A `Q-` entry that affects the current stage's output blocks that stage. The agent stops and asks.
* A `Q-` entry that affects only a later stage does not block. It is carried forward and must be resolved before the gate preceding that stage.
* A `Q-` entry is closed by recording the answer and its source. An unanswered `Q-` entry attached to an acceptance criterion forces that criterion to `unverified`.

The agent may state a recommendation when raising a question. It may not act on its own recommendation in place of an answer.

### Context

At each stage the agent reads `principles.md`, `product.md`, and every approved artifact of the feature produced by preceding stages. It does not rely on context from outside those artifacts; anything it learns elsewhere that affects the work is recorded in an artifact before being acted on.

## 11. Workflow Rules

1. Each feature must have an approved specification before implementation begins.
2. Design must reference the requirements it addresses, by identifier.
3. Tasks must reference the requirements or architectural decisions they implement, by identifier.
4. Implementation must follow the approved design.
5. A task marked `done` must have execution evidence as defined in section 5.
6. Verification must assign every acceptance criterion exactly one verdict.
7. Changes to approved artifacts must be recorded as `CH-` entries.
8. Changes affecting downstream artifacts must trigger an impact review.
9. A feature cannot be considered complete while a required acceptance criterion is `failed` or `unverified`.
10. The workflow must support returning to previous stages, by way of a `CH-` entry and its impact review.
11. Specifications and designs must comply with `principles.md`, or declare their exception.
12. Every identifier referenced must exist.

## 12. Completion

A feature is complete when:

* Every required acceptance criterion is `verified`.
* Required automated checks have passed.
* Every task is `done` or `cancelled` with a recorded reason.
* No `Q-` entry remains open against a required acceptance criterion.
* No unresolved blocking issue remains.
* `verification.md` records the above.
