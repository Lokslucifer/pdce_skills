# Task-Type Resource Registry (`task-propose`)

Summary: Central registry indexing all supported task types and domain-specific proposal resources for the `task-propose` skill, with explicit instructions for registering new types and specializations.

> [!IMPORTANT]
> **Proposal Organization Directive**:
> When generating proposals, the agent must **always use these resource references** to correctly structure and organize the proposal. Follow each resource's concise outline, wrap all interfaces and schemas in the code tag of the detected tech stack, and define only **code execution tests** for verification.

---

## 1. Supported Task Types

The skill categorizes engineering work into primary task types:

| Task Type | Definition | Scope |
| :--- | :--- | :--- |
| **`FEATURE`** | Introduce new functionality | Brand new endpoints, new domain modules, new entities, or new background workflows. |
| **`MODIFICATION`** | Change existing functionality | Refactoring, driver migrations, signature changes, behavioral updates, or optimizations. |

---

## 2. Active Resource Index

Resources are located under `resources/<TASK_TYPE>/`:

| Task Type | Domain | Specific Resource | File Path | Scope & Focus |
| :--- | :--- | :--- | :--- | :--- |
| `FEATURE` | `backend` | `api_endpoint` | [`FEATURE/backend/api_endpoint.md`](./FEATURE/backend/api_endpoint.md) | New HTTP/RPC ingress routes, controllers, request validation schemas, authentication, and status codes. |
| `FEATURE` | `backend` | `module` | [`FEATURE/backend/module.md`](./FEATURE/backend/module.md) | New domain services, business logic components, repositories, database models, and internal pipelines. |
| `FEATURE` | `fallback` | `generic` | [`FEATURE/generic.md`](./FEATURE/generic.md) | Generic fallback for new features when no domain-specific template matches. |
| `MODIFICATION` | `backend` | `backend` | [`MODIFICATION/backend.md`](./MODIFICATION/backend.md) | Overall backend modifications (routes, services, repositories, driver migrations) with backward compatibility check and ripple-effect file lists. |
| `MODIFICATION` | `fallback` | `generic` | [`MODIFICATION/generic.md`](./MODIFICATION/generic.md) | Generic fallback for modifications when no domain-specific template matches. |

---

## 3. How to Add a New Task Type or Specific Resource

The `task-propose` core (`SKILL.md`) is completely decoupled from domain templates. Adding new resources requires **zero changes to `SKILL.md`**:

### Adding a New Specific Resource (e.g., UI Component)
1. Determine task type: `FEATURE` or `MODIFICATION`.
2. Create directory and markdown file: `resources/<TASK_TYPE>/<domain>/<resource>.md` (e.g., `resources/FEATURE/frontend/ui_component.md`).
3. Implement standard resource sections:
   - When to use (signals and triggers).
   - Targeted analysis checklist.
   - Proposal content outline and type schemas.
   - Verification guidance.
   - Common pitfalls.
4. Register the new resource in the table above.

### Adding a New Task Type (e.g., `BUG_FIX`)
1. Create directory `resources/<NEW_TASK_TYPE>/` (e.g., `resources/BUG_FIX/`).
2. Add domain-specific resources inside (e.g., `resources/BUG_FIX/backend/module.md`).
3. Update the **Supported Task Types** and **Active Resource Index** tables above.

