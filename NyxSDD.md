# NyxSDD — Workflow Specification

## 1. Objective

Define a structured, traceable, and verifiable process for AI-assisted software development.

## 2. Workflow

The initial workflow consists of six stages:

1. Discovery
2. Specify
3. Design
4. Tasks
5. Implement
6. Verify

## 3. Stage Definitions

### Discovery
Defines the context of the product and problems to solve.

### Specify

Define the functional and non-functional requirements of a feature.

Output:

* spec.md

The specification must include:

* Feature objective
* Scope
* Functional requirements
* Non-functional requirements, when applicable
* Business rules
* Acceptance criteria
* Out-of-scope items
* Open questions, if any

### Design

Define the technical solution based on the approved specification.

Output:

* design.md

The design must include, when applicable:

* Architectural decisions
* Components affected
* Interfaces and contracts
* Data model changes
* Security considerations
* Testing strategy
* Relevant technical constraints

### Tasks

Decompose the approved design into implementation tasks.

Output:

* tasks.md

Each task must include:

* Unique identifier
* Description
* Related requirements
* Dependencies
* Expected outcome
* Completion criteria

### Implement

Execute the implementation tasks according to the approved specification and design.

Expected outputs:

* Source code changes
* Automated tests
* Updated task statuses
* Execution evidence

The implementation must not silently change approved requirements or architectural decisions.

### Verify

Evaluate whether the implementation satisfies the specification and design.

Output:

* verification.md

Verification must consider:

* Acceptance criteria
* Build results
* Automated test results
* Relevant static analysis
* Requirement-to-test traceability
* Deviations from the approved design
* Remaining issues

## 4. Workflow Rules

1. Each feature must have a specification before implementation begins.
2. Design must reference the requirements it addresses.
3. Tasks must reference the requirements or technical decisions they implement.
4. Implementation must follow the approved design.
5. Completed tasks must have appropriate completion evidence.
6. Verification must distinguish verified, failed, and unverified criteria.
7. Changes to approved requirements must be recorded.
8. Changes affecting downstream artifacts must trigger an impact review.
9. A feature cannot be considered complete while required acceptance criteria remain unverified or failed.
10. The workflow must support returning to previous stages when necessary.

## 5. Human Approval

The framework must support human review and approval of specifications, designs, and task plans.

Approval requirements may be configurable.

## 6. Completion

A feature is complete when:

* Required acceptance criteria have been verified.
* Required automated checks have passed.
* No unresolved blocking issues remain.
* Verification results have been recorded.
