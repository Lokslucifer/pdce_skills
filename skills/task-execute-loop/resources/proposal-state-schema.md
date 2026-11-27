# Execution Status & Decisions Schema (`status.md`)

Summary: Governs how execution progress, task statuses, acceptance verification, and decisions are tracked during `task-execute-loop`. Supports both single-task and multi-task batch execution. Progress is tracked in a dedicated `status.md` alongside `proposal.md` (e.g. `proposals/<name>/status.md` or `plan/<name>/status.md`), keeping `proposal.md` clean as the architectural specification.

---

## File Location & Relationship

- **Blueprint**: `proposals/<name>/proposal.md` (or `plan/<name>/proposal.md`) defines the architectural contracts, schemas, and task definitions.
- **Tracker**: `status.md` is initialized and maintained in the same directory as `proposal.md` (e.g., `proposals/<name>/status.md`).
- **Rule**: `proposal.md` is updated ONLY when architectural designs, contracts, or schemas intentionally change via an approved impact diff. Routine task status transitions (`[TODO]` -> `[IN_PROGRESS]` -> `[IN_REVIEW]` -> `[DONE]`) are recorded in `status.md`.

---

## Standard `status.md` Format

```markdown
# Execution Status: <Proposal Title>

- Proposal: `proposals/<name>/proposal.md`
- Tech Stack: <Language, framework, runtime>
- Baseline Checkpoint: `<commit-hash>` ("start: <proposal name>")
- Current State: `In Progress` | `Complete`

## Task Overview

| Task ID | Task Title | Status | Checkpoint Commit |
| :--- | :--- | :--- | :--- |
| `T1` | <Task Title 1> | `[DONE]` | `abc1234` |
| `T2` | <Task Title 2> | `[IN_PROGRESS]` | Pending |
| `T3` | <Task Title 3> | `[IN_PROGRESS]` | Pending |
| `T4` | <Task Title 4> | `[TODO]` | - |

---

## Active Tasks: T2, T3 [`[IN_PROGRESS]`]

- Status: `[TODO]` | `[IN_PROGRESS]` | `[IN_REVIEW]` | `[DONE]`

### T2: <Task Title 2>
- Objective: <Task objective from proposal.md>
- Verification Criteria:
  - [x] <Criterion 1 verified by loop test>
  - [ ] <Criterion 2 in progress>

### T3: <Task Title 3>
- Objective: <Task objective from proposal.md>
- Verification Criteria:
  - [x] <Criterion 1 verified by loop test>
  - [ ] <Criterion 2 in progress>

---

## Decisions Log

Record all architectural deviations, contract alterations, and approved user decisions across tasks or batches:

### Task Batch T2, T3 (<YYYY-MM-DD>)
- Decision: <Concise summary of what was altered from initial proposal>
- Rationale: <Why this change was made>
- Affected Modules: <List of files/modules updated>
- Proposal Updated: [Yes|No]
- Approved By: User (via interactive review)
```

---

## Task Lifecycle States

| State Badge | Meaning | Next Permitted Transition |
| :--- | :--- | :--- |
| `[TODO]` | Task has not been started. | `[IN_PROGRESS]` |
| `[IN_PROGRESS]` | Agent is actively implementing code or `loop_tests/` for the task or batch. | `[IN_REVIEW]` |
| `[IN_REVIEW]` | Agent patches are staged (`git add .`); awaiting human review or edits. | `[IN_PROGRESS]` (revisions) or `[DONE]` (approved) |
| `[DONE]` | Criteria met across batch, tests pass, human approved, boundary commit executed. | Terminal for this task |

---

## Synchronization Rules

1. **Initialize at Start**: When `task-execute-loop` starts, read `proposal.md` and create `status.md` if it does not already exist, listing all tasks as `[TODO]`.
2. **Update on Transitions**: Update `status.md` whenever tasks transition state (`[TODO]` -> `[IN_PROGRESS]` -> `[IN_REVIEW]` -> `[DONE]`). In multi-task execution, all tasks in the active batch transition together.
3. **Stage with Code**: `status.md` is staged with agent patches (`git add status.md`) so that human edits remain clearly visible as unstaged diffs.
4. **Batch Checkpoint Commits**: When completing a multi-task batch, update all batch tasks to `[DONE]` and record the single checkpoint commit hash in the Task Overview.
