# Generic Feature Resource (`FEATURE/generic.md`)

Summary: Generic proposal template and checklist for new feature tasks when no domain-specific template matches.

---

## 1. When to Use

Apply when introducing new functionality where no specialized resource (e.g. `api_endpoint.md`, `module.md`) matches the task domain.

---

## 2. Analysis Checklist

Inspect workspace files before proposing:
- [ ] **Existing Conventions**: Module organization, file layouts, and naming patterns.
- [ ] **Interfaces & Types**: Shared schemas, base classes, or data models.
- [ ] **Dependencies**: Existing libraries and configurations in scope.

---

## 3. Proposal Content & Schemas

Structure concisely via `hierarchical-outline`. Wrap all code blocks, signatures, and contracts in the detected tech stack syntax.

### Component Definition
- Component: `<name>`
- Action: `new`
- Target Path: `<file_or_module_path>`
- Logic Summary:
  - Simple logic: `<one-line description with few words>`
  - Complex logic: Split into abstract simpler steps (1. Step A -> 2. Step B -> 3. Step C).

### Signatures & Contracts
Wrap in the code tag of the confirmed tech stack:

````markdown
```<tech_stack>
# Proposed signature / interface / schema
<signature_definition>
```
````

---

## 4. Verification (Code Execution Test Only)

> [!NOTE]
> Define **only a code execution test** to verify execution readiness. Comprehensive unit test suites will be written when the implementation is hardened.

- **Execution Command / Script**:
  - Command: `<minimal CLI command or invocation script>`
  - Expected Observable Result: `<expected output, return code, or artifact>`
  - Verification Checklist:
    - [ ] Execution script executes without unhandled errors.

---

## 5. Pitfalls to Avoid

- Introducing unnecessary dependencies when existing codebase utilities suffice.
- Violating established project conventions or architectural layering.

