# Resource: New API Endpoint (`FEATURE/backend/api_endpoint.md`)

Summary: Concise resource for proposing new backend API endpoints, ingress routes, and request/response contracts wrapped in the detected tech stack.

---

## 1. When to Use

Apply when introducing new API ingress capabilities:
- New REST/RPC routes, WebSocket handlers, or webhook receivers under `routes/`, `controllers/`, or `api/`.

---

## 2. Analysis Checklist

Inspect workspace files before proposing:
- [ ] **Routing & Conventions**: Existing framework router (e.g., FastAPI `APIRouter`, Express router).
- [ ] **Ingress Models**: Active schema library (e.g., Pydantic `BaseModel`, Zod, Dataclasses).
- [ ] **Auth & Dependencies**: Security middleware, dependency injection, and permissions.

---

## 3. Proposal Content & Schemas

Express all schemas inside the specific code tag of the detected tech stack (e.g., ```` ```python ```` or ```` ```typescript ````).

### Endpoint Definition
- Route: `<METHOD> <path>`
- Controller File: `<target_file_path>`
- Logic Summary:
  - Simple logic: `<one-line description with few words>`
  - Complex logic: Split into abstract simpler steps (1. Validate -> 2. Process -> 3. Return).
- Auth & Security: `<Bearer token | API key | None>`

### Endpoint Contract Schema
Wrap in the code tag of the confirmed tech stack:

````markdown
```<tech_stack>
# Request & Response Contract
Method: <HTTP_METHOD>
Path: <path>

Headers:
  <Header-Key>: <Type>

Request Body:
  <field_name>: <Type> (<constraints, e.g. min_length, default>)

Response Success (<200|201>):
  <field_name>: <Type>

Error Responses:
  <400|404|422|500>: <condition> -> {"error": "<code_string>", "message": "<safe_msg>"}
```
````

---

## 4. Verification (Code Execution Test Only)

> [!NOTE]
> Define **only a code execution test** to verify execution readiness. Detailed unit/integration suites are created separately during implementation hardening.


---

## 5. Pitfalls to Avoid

- Exposing internal database entities directly in response payloads instead of dedicated schemas.
- Missing input validation on query parameters and request bodies.
- Performing synchronous blocking calls inside async route handlers.
