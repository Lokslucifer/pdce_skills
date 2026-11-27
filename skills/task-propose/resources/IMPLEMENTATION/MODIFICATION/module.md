# Resource: Modified Backend (`MODIFICATION/backend.md`)

Summary: Concise resource for proposing modifications to existing backend routes, services, driver migrations, and repositories with explicit backward compatibility and ripple-effect file mapping.

---

## 1. When to Use

Apply when modifying, refactoring, or migrating existing backend code:
- Endpoint parameter/response adjustments, driver migrations (e.g., PyMongo -> Motor), service refactoring, repository updates, or performance tuning.

---

## 2. Analysis Checklist

Inspect workspace files before proposing:
- [ ] **Current Baseline**: Ground existing signatures, blocking calls, and query semantics in code.
- [ ] **Call Sites & Upstream Consumers**: Trace all files and callers that invoke the target component.
- [ ] **Driver / Library Parity**: Check compatibility differences between old and new drivers or libraries.

---

## 3. Proposal Content & Schemas

Express all signatures and contracts inside the specific code tag of the detected tech stack (e.g., ```` ```python ```` or ```` ```typescript ````).

### Proposed Component Modifications
- Component: `<name>`
- Action: `modify` | `remove`
- Target Path: `<file_or_module_path>`
- Logic Summary:
  - Simple logic: `<one-line description with few words>`
  - Complex logic: Split into abstract simpler steps (1. Migrate -> 2. Adapt -> 3. Rewire).

### Updated Signatures & Interface Diffs
Wrap in the code tag of the confirmed tech stack:

````markdown
```<tech_stack>
# Signature Diff
# Before:
def <function_name>(self, <params>) -> <OldType>: ...

# After:
async def <function_name>(self, <params>) -> <NewType>:
    """Responsibility: <one-line summary>"""
    ...
```
````

### Backward Compatibility & Ripple Effect
- Backward Compatibility Check:
  - Is backward compatibility needed?: `<Yes | No>`
  - If YES:
    - Compatibility Mechanism: `<default parameter values, backward-compatible shims, or adapters>`
  - If NO:
    - **Other Files Required to Change**:
      - `<path/to/caller1.py>`: `<specific update needed, e.g. add await or update payload>`
      - `<path/to/caller2.py>`: `<specific update needed>`

---

## 4. Verification (Code Execution Test Only)

> [!NOTE]
> Define **only a code execution test** to verify execution readiness and also add some behavioural tests. Comprehensive unit test suites will be written when the module or implementation is hardened.

---

## 5. Pitfalls to Avoid

- Breaking existing callers without identifying all downstream files that must be updated.
- Leaving unawaited coroutines when converting synchronous functions to `async def`.
- Subtly changing database query semantics or projection keys during driver migrations.
