# Task-Type Resource Registry (`task-propose`)

Summary: Central registry indexing all supported task types and domain-specific proposal resources for the `task-propose` skill, with explicit instructions for registering new types and specializations.

> [!IMPORTANT]
> **Proposal Organization Directive**:
> When generating proposals, the agent must **always use these resource references** to correctly structure and organize the proposal. Follow each resource's concise outline, wrap all interfaces and schemas in the code tag of the detected tech stack, and define only **code execution tests** for verification.

---

## 1. Supported Task Types

The skill categorizes engineering work into primary domains and task types:

### Implementation Domain (`IMPLEMENTATION/`)
| Task Type | Definition | Scope |
| :--- | :--- | :--- |
| **`FEATURE`** | Introduce new functionality | Brand new endpoints, new domain modules, new entities, or new background workflows. |
| **`MODIFICATION`** | Change existing functionality | Refactoring, driver migrations, signature changes, behavioral updates, or optimizations. |

### Testing Domain (`TESTING/`)
| Task Type | Definition | Scope |
| :--- | :--- | :--- |
| **`NEW_TESTS`** | Propose new test suites | Greenfield unit and integration test suites, bottom-up module classification, and classical testing design. |
| **`MODIFICATION`** | Update or refactor test suites | Updating existing suites, repairing fragile/broken tests, adding coverage deltas, or migrating test runners. |

---

## 2. Active Resource Index

Resources are organized under `resources/<DOMAIN>/<TASK_TYPE>/`:

| Domain / Task Type | Domain Target | Specific Resource | File Path | Scope & Focus |
| :--- | :--- | :--- | :--- | :--- |
| `IMPLEMENTATION/FEATURE` | `backend` | `api_endpoint` | [`IMPLEMENTATION/FEATURE/api_endpoint.md`](./IMPLEMENTATION/FEATURE/api_endpoint.md) | New HTTP/RPC ingress routes, controllers, request validation schemas, authentication, and status codes. |
| `IMPLEMENTATION/FEATURE` | `backend` | `module` | [`IMPLEMENTATION/FEATURE/module.md`](./IMPLEMENTATION/FEATURE/module.md) | New domain services, business logic components, repositories, database models, and internal pipelines. |
| `IMPLEMENTATION/FEATURE` | `fallback` | `generic` | [`IMPLEMENTATION/FEATURE/generic.md`](./IMPLEMENTATION/FEATURE/generic.md) | Generic fallback for new features when no domain-specific template matches. |
| `IMPLEMENTATION/MODIFICATION` | `backend` | `module` | [`IMPLEMENTATION/MODIFICATION/module.md`](./IMPLEMENTATION/MODIFICATION/module.md) | Overall backend modifications (routes, services, repositories, driver migrations) with backward compatibility check and ripple-effect file lists. |
| `IMPLEMENTATION/MODIFICATION` | `fallback` | `generic` | [`IMPLEMENTATION/MODIFICATION/generic.md`](./IMPLEMENTATION/MODIFICATION/generic.md) | Generic fallback for modifications when no domain-specific template matches. |
| `TESTING/NEW_TESTS` | `backend` | `backend` | [`TESTING/NEW_TESTS/backend.md`](./TESTING/NEW_TESTS/backend.md) | New backend test suite proposals: bottom-up traversal, Domain/Controller/Trivial/Complex typology, classical AAA rules, managed/unmanaged dependency isolation. |
| `TESTING/NEW_TESTS` | `fallback` | `generic` | [`TESTING/NEW_TESTS/generic.md`](./TESTING/NEW_TESTS/generic.md) | Generic fallback for new test suites when no domain-specific template matches. |
| `TESTING/MODIFICATION` | `backend` | `backend` | [`TESTING/MODIFICATION/backend.md`](./TESTING/MODIFICATION/backend.md) | Refactoring and updating existing test suites, audit of brittle/broken tests, and regression invariants. |
| `TESTING/MODIFICATION` | `fallback` | `generic` | [`TESTING/MODIFICATION/generic.md`](./TESTING/MODIFICATION/generic.md) | Generic fallback for test modifications when no domain-specific template matches. |

---

## 3. How to Add a New Task Type or Specific Resource

The `task-propose` core (`SKILL.md`) is completely decoupled from domain templates. Adding new resources requires **zero changes to `SKILL.md`**:

### Adding a New Specific Resource (e.g., UI Component)
1. Determine domain and task type: `IMPLEMENTATION` or `TESTING`, and `FEATURE` or `MODIFICATION`.
2. Create markdown file under the matching directory: `resources/<DOMAIN>/<TASK_TYPE>/<resource>.md` (e.g., `resources/IMPLEMENTATION/FEATURE/ui_component.md`).
3. Implement standard resource sections:
   - When to use (signals and triggers).
   - Targeted analysis checklist.
   - Proposal content outline and type schemas.
   - Verification guidance.
   - Common pitfalls.
4. Register the new resource in the table above.

### Adding a New Task Type (e.g., `BUG_FIX`)
1. Create directory `resources/<DOMAIN>/<NEW_TASK_TYPE>/` (e.g., `resources/IMPLEMENTATION/BUG_FIX/`).
2. Add domain-specific resources and generic fallback inside (e.g., `generic.md`).
3. Update the **Supported Task Types** and **Active Resource Index** tables above.

