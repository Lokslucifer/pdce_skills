# Generic Modification Resource (`MODIFICATION/generic.md`)

Summary: Generic proposal template and checklist for modification, refactoring, or optimization tasks when no domain-specific template matches.

---

## 1. When to Use

Apply when modifying or refactoring existing code where no specialized resource matches the task domain.

---

## 2. Analysis Checklist

Inspect workspace files before proposing:
- [ ] **Current Baseline**: Existing implementation, method signatures, and behavior.
- [ ] **Call Sites**: All upstream files and functions that consume the target code.
- [ ] **Ripple Radius**: External contracts or consumers affected by the change.

---

## 3. Proposal Content & Schemas

Structure concisely via `hierarchical-outline`. Wrap all code blocks, signatures, and contracts in the detected tech stack syntax.

### Proposed Component Modifications
- Component: `<name>`
- Action: `modify` | `remove`
- Target Path: `<file_or_module_path>`
- Logic Summary:
  - Simple logic: `<one-line description with few words>`
  - Complex logic: Split into abstract simpler steps.

### Updated Signatures & Interface Diffs
Wrap in the code tag of the confirmed tech stack:

````markdown
```<tech_stack>
# Diff / Signature update
# Before:
<legacy_signature_or_behavior>

# After:
<updated_signature_or_behavior>
```
````

### Backward Compatibility & Ripple Effect
- Backward Compatibility Check:
  - Is backward compatibility needed?: `<Yes | No>`
  - If YES:
    - Compatibility Mechanism: `<how compatibility is preserved>`
  - If NO:
    - **Other Files Required to Change**:
      - `<path/to/caller1>`: `<specific update needed>`
      - `<path/to/caller2>`: `<specific update needed>`

---

## 4. Verification (Code Execution Test Only)

> [!NOTE]
> Define **only a code execution test** to verify execution readiness. Comprehensive unit test suites will be written when the implementation is hardened.

- **Execution Command / Script**:
  - Command: `<minimal CLI command or invocation script>`
  - Expected Observable Result: `<expected output, return code, or artifact>`
  - Verification Checklist:
    - [ ] Modified component executes without unhandled errors.
    - [ ] If backward compatibility was NOT preserved, all ripple callers execute cleanly.

---

## 5. Pitfalls to Avoid

- Breaking existing callers without listing all dependent files that must be updated.
- Leaving dead code or zombie parameters.

