---
trigger: always_on
description: Enforces test placement conventions across all projects.
---

# Test Placement

Tests written while implementing a task or patch (to verify work,
reproduce bugs, or iterate in a loop) must go in `loop_tests/`,
NOT in `tests/` or `test_suite/`.

## Why

These tests are often brittle, tightly coupled to the current
implementation, or exploratory. Keeping them separate protects
the curated test suite from noise and flaky tests.

## Rules

1. Default location for any test you create: `loop_tests/`,
   mirroring the source path where practical.

   Example: `loop_tests/auth/test_login.py`

2. Do not add or modify files in `test_suite/` unless the task
   explicitly asks for tests to be added or changed there.

3. Do not move or promote tests from `loop_tests/` into
   `test_suite/`. The human decides what gets promoted.

4. Existing tests in `test_suite/` should still be run to check
   for regressions. Do not edit them to make your change pass
   unless the task requires it.

5. Leave `loop_tests/` contents in place when you finish.
   Do not delete them.

## Scope and Exceptions

- Apply this rule to all repositories unless a repository has
  an explicit, conflicting test-placement policy.
- If `loop_tests/` does not exist, create it.
- Before creating tests, inspect the existing project structure
  and follow the source-directory mirroring convention.
- If the project uses a different test framework or directory
  structure, ask before introducing a new convention.