# Resource: New Backend Module (`FEATURE/backend/module.md`)

Summary: Concise resource for proposing new backend domain services, business logic modules, repositories, and data models wrapped in the detected tech stack.

---

## 1. When to Use

Apply when introducing new internal backend components:
- New service classes, business logic modules, repository implementations, or entity models under `services/`, `repository/`, `models/`, or `pipelines/`.

---

## 2. Analysis Checklist

Inspect workspace files before proposing:
- [ ] **Architecture Layering**: Abstract interfaces, dependency injection conventions, and service layers.
- [ ] **Data Access**: Active database drivers (e.g., Motor, SQLAlchemy, PyMongo) and connection management.
- [ ] **Concurrency Runtime**: Asynchronous event loop (`async def`) vs synchronous threading (`def`).

---

## 3. Proposal Content & Schemas

Express all interfaces, signatures, and models inside the specific code tag of the detected tech stack (e.g., ```` ```python ```` or ```` ```typescript ````).

### Component Definition
- Component: `<ServiceName | RepositoryName | ModelName>`
- Action: `new`
- Target Path: `<file_or_module_path>`
- Logic Summary:
  - Simple logic: `<one-line description with few words>`
  - Complex logic: Split into abstract simpler steps (1. Ingest -> 2. Transform -> 3. Persist -> 4. Emit).

### Service & Function Signatures
Wrap signatures in the code tag of the confirmed tech stack:

````markdown
```<tech_stack>
class <ClassName>:
    async def <function_name>(self, <param>: <Type>) -> <ReturnType>:
        """
        Responsibility: <one-line summary>
        Raises: <DomainException> on <condition>
        """
        ...
```
````

### Data Models & Schemas
Wrap entities in the code tag of the confirmed tech stack:

````markdown
```<tech_stack>
class <EntityName>(BaseModel):
    <field_name>: <Type> = Field(..., description="<short_desc>")
    # Indexes: <index_name> on (<field>)
```
````

---

## 4. Verification (Code Execution Test Only)

> [!NOTE]
> Define **only a code execution test** to verify execution readiness. Comprehensive unit test suites will be written when the module or implementation is hardened.

- **Execution Command / Script**:
  - Command: `<minimal python -c / node -e script invoking the method with mock data>`
  - Expected Observable Result: `<successful invocation, expected return value or written record>`
  - Verification Checklist:
    - [ ] Execution script executes without unhandled exceptions.
    - [ ] Operation yields expected return data type.

---

## 5. Pitfalls to Avoid

- Synchronous I/O or sleep calls inside async methods blocking the event loop.
- Unbounded database queries without pagination or limit constraints.
- Circular imports between models and services.
