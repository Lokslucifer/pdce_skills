# Resource: New Backend Tests (`TESTING/NEW_TESTS/backend.md`)

Summary: Standardized architectural resource for proposing new backend test suites through bottom-up module classification, classical testing patterns, targeted refactoring of complex modules, and dependency isolation.

## 1. When to Use

Application Criteria:
- Designing brand new test suites for untested backend components, services, or APIs.
- Establishing test proposals during new feature development or post-implementation test stabilization.
- Structuring unit and integration test boundaries across domain logic and external infrastructure collaborators.

## 2. Analysis Checklist & Module Traversal

### Traversal Strategy
Bottom-Up Module Ordering:
- Construct dependency directed acyclic graph (DAG) of the target backend system.
- Begin analysis bottom-up from leaf modules (pure utilities, domain models, zero downstream dependencies).
- Progress upward through intermediary domain services to top-level controllers, workers, and API endpoints.

### Tech Stack & Test Runner Discovery
Workspace Inspection:
- Inspect workspace configuration files (`pyproject.toml`, `package.json`, `go.mod`, `pom.xml`, `Cargo.toml`).
- Auto-detect existing test framework and runner (e.g., `pytest`, `jest`, `vitest`, `go test`).
- Prompt User on Absence:
    - If no test runner or configuration is detected, prompt user to confirm the preferred test runner and execution environment before finalizing proposal.

### Existing Test Suite Audit & Complexity Gate
Audit Flow:
- Inspect if any test files currently target the module.
- If existing tests are absent or in a clean/simple state:
    - Capture existing coverage baseline and proceed directly to test case mapping.
- If existing tests are in a complex or fragile state (e.g., heavy over-mocking, inter-test state leaks, mixed orchestration):
    - Halt automatic generation.
    - Raise an interactive prompt to the user detailing:
        - Current suite complexity, maintenance risks, and failure modes.
        - Recommendation to restructure into clean Domain/Controller separation.
    - Decision Branching:
        - User approves restructuring: Add the module and its tests to refactoring scope; recursively analyze its dependencies.
        - User rejects restructuring: Skip module test redesign and preserve existing suite as-is.

## 3. Module Typology & Classification Matrix

### Typology Definitions
Classification Dimensions (Complexity vs Collaborators):
- `Domain`:
    - Metrics: High business complexity, high domain significance, low/immutable collaborators.
    - Contents: Pure business logic, entity validation, invariant rules, domain calculations.
    - Collaborators: Strictly immutable objects or in-memory domain entities; zero out-of-process I/O.
- `Controller`:
    - Metrics: Low business complexity, low domain significance, high collaborators.
    - Contents: Flow orchestration, input translation, protocol adaptation, transaction management.
    - Collaborators: Coordinates domain modules, repositories, database clients, and external gateways.
- `Trivial`:
    - Metrics: Low business complexity, low domain significance, zero or minimal collaborators.
    - Contents: Simple data transfer objects (DTOs), static constants, property getters/setters, basic mappings.
- `Complex`:
    - Metrics: High business complexity, high domain significance, high collaborators.
    - Anti-pattern: Business decisions tightly coupled with out-of-process I/O and orchestration.
    - Remediation: Must be refactored by splitting into pure `Domain` modules and lean `Controller` modules.

## 4. Architectural Refactoring Directives

### Structural Separation Rules
Depth vs Breadth Invariant:
- Modules must exhibit either depth (high domain complexity) or breadth (many collaborators), never both.
- Split all `Complex` modules into:
    - One or more functional `Domain` modules capturing business logic.
    - A thin `Controller` coordinating domain calls and I/O collaborators.

Visibility & Encapsulation:
- Place all observable system behavior behind the module's Public API.
- Place internal algorithms, helper state, and implementation details strictly behind Private APIs.
- Tests must assert exclusively against the Public API; never test private implementation details.

### Functional Domain Core & Testing Order
Domain Design Strategy:
- Make domain modules functional and immutable wherever possible.
- Design domain APIs to favor test styles in strict priority order:
    1. `Output-based testing`: Invoke method with inputs and assert return values (pure functions; highest test value and lowest maintenance).
    2. `State-based testing`: Invoke method and assert resulting state changes on the entity or value object.
    3. `Communication-based testing`: Assert interactions between collaborators (lowest priority; reserved only for unmanaged boundaries).
- Refactor code proactively to convert stateful and interaction-heavy logic into output-based functional logic.

## 5. Classical Testing & Test Generation Rules

### Classical Testing Paradigm
Core Paradigm:
- Adopt Classical (Detroit / State-oriented) testing:
    - Prioritize real instances over test doubles.
    - Verify final observable outcomes and state rather than intermediate collaborator function calls.
    - Reserve mocking strictly for external, unmanaged boundaries.

### AAA Pattern & SUT Act Invariant
Arrange-Act-Assert Structure:
- `Arrange`: Instantiate real SUT and dependencies; set up initial test state.
- `Act`: Execute the single public method under test.
- `Assert`: Verify return values, resulting entity state, or boundary side-effects.

Single Behavior & Single-Line Act Invariant:
- Each test case must verify exactly one observable behavior.
- SUT Act Single-Line Rule:
    - The `Act` section invoking the System Under Test (`sut`) must consist of exactly one line of code.
    - Multiple lines in the Act section indicates an encapsulation flaw or leaky abstraction in the SUT's public API.

### Human-Centric Test Naming Policy
Naming Rules:
- Test names must be readable by non-programmers as a plain English description of expected behavior.
- Separate words strictly with underscores (`snake_case`).
- Prohibited pattern:
    - Do NOT use `[MethodUnderTest]_[Scenario]_[ExpectedResult]` (e.g., `calculateTotal_EmptyCart_ReturnsZero`).
- Required pattern:
    - Describe the business scenario and expected outcome directly.
    - Valid example: `cart_total_is_zero_when_no_items_are_added`.
    - Valid example: `user_cannot_withdraw_amount_exceeding_account_balance`.

### Typology-Specific Test Strategy
Domain Modules:
- Generate pure unit tests.
- Strictly zero mocks allowed; execute all domain logic with real in-memory inputs.
- Exercise all boundary conditions, mathematical formulas, and business invariants.

Controller Modules:
- Generate integration tests.
- Execute integration suites sequentially to prevent state collision.
- Clean up managed dependencies at the start of each test suite or test case to ensure deterministic isolation.
- Paths to Cover:
    - All primary happy paths end-to-end through the controller and domain.
    - Critical explicit edge cases and infrastructure failure paths not covered by unit tests.

## 6. Dependency Isolation & Management

### Dependency Classification
Managed Dependencies:
- Definition: Out-of-process resources completely owned, controlled, and accessed solely by the application under test (e.g., dedicated application database, local Redis cache).
- Testing Strategy:
    - Do NOT mock managed dependencies.
    - Execute integration tests against real containerized or test-dedicated instances.
- State Lifecycle:
    - `Stateful`: Databases, persisted storage queues. Requires automated pre-test cleanup (table truncation, database reset).
    - `Stateless`: Ephemeral caches or in-memory registries requiring simple connection flushes.

Unmanaged Dependencies:
- Definition: External systems shared with other applications, third parties, or outside services (e.g., payment gateways, email delivery APIs, shared message buses).
- Testing Strategy:
    - Mock strictly at the application's outbound adapter / boundary client (the very edge of the system).
    - Do not allow test executions to perform real network calls to unmanaged dependencies.

## 7. Proposal Content & Schemas

Wrap all signatures and code examples in the markdown code tag of the detected tech stack (e.g., ```` ```python ```` or ```` ```typescript ````).

### Abstract Content Blocks

Base Content Schema:
- Module Name: `<module_or_class_name>`
- Target Path: `<source_file_path>`
- Short Behaviour Description: `<1-2 sentence summary of purpose and responsibility>`
- Type: `Domain` | `Controller` | `Complex` | `Trivial`

Public API Content Schema:
- API Signature:
    ````markdown
    ```<tech_stack>
    def <method_name>(self, <param>: <Type>) -> <ReturnType>: ...
    ```
    ````
- Behaviour: `<observable functional contract and invariant guarantees>`
- Test Cases:
    - Unit of Behaviour: `<scenario description>`
        - Test Name: `<non_programmer_scenario_description>`
        - Verification Style: `Output-based` | `State-based`
        - Assertion Focus: `<expected return value or state property>`

Refactor Content Schema (for `Complex` modules):
- Refactoring Justification: `<explanation of mixed domain logic and collaborator orchestration>`
- Code Change:
    - Before (Legacy Implementation):
        ````markdown
        ```<tech_stack>
        # Before: Mixed business logic with database / network calls
        class <LegacyService>:
            def process(self, ...): ...
        ```
        ````
    - After (Decomposed Architecture):
        ````markdown
        ```<tech_stack>
        # After: Pure Domain Engine
        class <DomainEngine>:
            def compute_decision(self, ...) -> <DomainResult>: ...

        # After: Lean Orchestration Controller
        class <ControllerService>:
            def execute(self, ...) -> <Response>: ...
        ```
        ````

Dependency Content Schema (for `Controller` modules):
- Managed Dependencies:
    - `<DependencyName>`:
        - Type: `Stateful` | `Stateless`
        - Cleanup Method: `<pre-test execution script or SQL truncation hook>`
- Unmanaged Dependencies:
    - `<DependencyName>`:
        - Boundary Adapter: `<client_or_gateway_class>`
        - Mock Strategy: `<edge-level mock contract replacing external call>`

### Type-Specific Synthesis Compositions

Domain Proposal Synthesis:
- Base Content
- Public API Content (all public methods, output/state-based tests, zero mocks)

Controller Proposal Synthesis:
- Base Content
- Public API Content (orchestration flows, integration test scenarios)
- Dependency Content (managed instance cleanup hooks, unmanaged edge mocks)

Trivial Proposal Synthesis:
- Base Content (classification note explaining why dedicated tests are omitted or limited to smoke verification)

Complex Proposal Synthesis:
- Base Content (marked as `Complex`)
- Refactor Content (before and after architectural decomposition)
- Refactored Sub-Modules:
    - Domain Sub-Module: Full Domain Content synthesis
    - Controller Sub-Module: Full Controller Content synthesis
    - Trivial Sub-Module (if any): Full Trivial Content synthesis

## 8. Verification & Execution Commands

### Execution Directives
- Auto-detected Test Runner: `<test_runner_command>` (e.g., `pytest`, `npm test`)
- Unit Test Execution (Domain):
    - Command: `<test_runner> <path_to_domain_tests>`
    - Invariant: Zero network or database connections; sub-second completion.
- Integration Test Execution (Controller):
    - Pre-test Cleanup Hook: `<cleanup_script_or_fixture>`
    - Command: `<test_runner> <path_to_controller_tests> --run-sequentially`
    - Invariant: Runs sequentially against isolated managed test containers.

## 9. Pitfalls to Avoid

- Introducing mocks or stubs into Domain module unit tests.
- Allowing multiple statements in the Act phase of an AAA test, masking API design issues.
- Naming tests with `Method_Scenario_ExpectedResult` rather than readable behavioral sentences.
- Executing integration tests in parallel against shared stateful managed dependencies.
- Mocking internal domain classes rather than external unmanaged boundary adapters.
- Testing private methods or internal state variables rather than observable public API behavior.

