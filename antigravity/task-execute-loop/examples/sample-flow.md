# Sample Execution Flow (`task-execute-loop`)

This document demonstrates an end-to-end trace of `task-execute-loop` executing task `T1` from `proposals/storage-migration/proposal.md`. It illustrates tracking progress via `status.md`, git separation, handling human edits, stale test diagnosis, non-persistent impact list artifact generation, two-stage approval, and the task boundary commit.

---

## 1. Start: Ingest Proposal & Baseline Checkpoint

```markdown
User: Let's begin implementing the tasks in proposals/storage-migration/proposal.md.

Agent:
1. Ingesting proposal blueprint: `proposals/storage-migration/proposal.md`.
2. Initializing tracker: created `proposals/storage-migration/status.md` with tasks T1, T2, T3 set to [TODO].
3. Verifying workspace git status:
   $ git status --porcelain
   (Clean working tree)
4. Creating empty baseline commit for the proposal:
   $ git commit --allow-empty -m "start: Pluggable Storage and Ingestion"
   [master abc1234] start: Pluggable Storage and Ingestion

Tracker initialized. Ready to begin. Picking next incomplete task.
```

---

## 2. Step 1: Display Available Tasks & Prompt User

```markdown
Agent:
### Available Tasks from proposals/storage-migration/status.md

| Task ID | Task Title | Status |
| :--- | :--- | :--- |
| `T1` | Pluggable Storage Abstraction Layer | `[TODO]` |
| `T2` | Document Service Orchestration | `[TODO]` |
| `T3` | Ingestion Pipeline Migration | `[TODO]` |

Which task would you like to execute?
1. (Recommended) T1: Pluggable Storage Abstraction Layer
2. T2: Document Service Orchestration
3. T3: Ingestion Pipeline Migration

User: Let's do T1.

Agent:
- Selected Task: T1 (Pluggable Storage Abstraction Layer)
- Status in `proposals/storage-migration/status.md` updated: [TODO] -> [IN_PROGRESS]
- Objective: Provide an abstract storage provider interface with native async Amazon S3 and local filesystem implementations.

Applying implementation patches via patch tool:
- Created: `code/storage/interface.py`
- Created: `code/storage/local.py`
- Created: `code/storage/s3.py`
- Created: `code/storage/factory.py`
- Created: `code/loop_tests/storage/test_local_storage.py`

Staging agent changes and tracker immediately:
$ git add -N code/storage code/loop_tests/storage
$ git add .
```

---

## 3. Step 3: Show for Review & Stop

```markdown
Agent:
### Task T1: Implementation Staged for Review [IN_REVIEW]

Status Tracker: Updated `proposals/storage-migration/status.md` to `[IN_REVIEW]`.

Summary of Changes:
- `code/storage/interface.py`: Defined abstract base class `StorageProvider` with async methods `save_stream`, `read_stream`, `delete`.
- `code/storage/local.py`: Implemented `LocalStorageProvider` with chunked non-blocking streaming.
- `code/storage/s3.py`: Implemented `S3StorageProvider` using `aioboto3.Session`.
- `code/storage/factory.py`: Factory function `get_storage_provider()` reading from `AppConfig`.
- `code/loop_tests/storage/test_local_storage.py`: 4 unit tests verifying local file streaming and deletion (all passing).

Stopping here for your review. Please review the patches in your editor. You may accept, reject, or edit the files directly.
```

---

## 4. Step 4 & 5: Collect Human Changes & Check Against Objective

*(Human edits `code/storage/interface.py` to add `overwrite: bool = True` parameter to `save_stream`, and updates `code/storage/local.py`)*

```markdown
User: I added an `overwrite` parameter to `save_stream` so callers don't accidentally wipe existing files unless they specify it.

Agent:
1. Detecting human changes:
   $ git status --porcelain
    M code/storage/interface.py
    M code/storage/local.py
   $ git diff
   diff --git a/code/storage/interface.py b/code/storage/interface.py
   - async def save_stream(self, key: str, stream: AsyncIterator[bytes]) -> StorageObjectMetadata:
   + async def save_stream(self, key: str, stream: AsyncIterator[bytes], overwrite: bool = True) -> StorageObjectMetadata:

2. Review against Objective & Acceptance Criteria:
   - Objective: Abstract storage provider interface with async local and S3 backends.
   - Evaluation: Adding an `overwrite` parameter enhances safety and does not violate any acceptance criteria.
   - Syntax & logic check: Clean; no missing imports or type errors.
```

---

## 5. Step 6: Test & Diagnose Failure (Stale Test vs Bug)

```markdown
Agent:
Running loop tests:
$ pytest code/loop_tests/storage/test_local_storage.py
FAILED: test_local_storage.py::test_save_stream_creates_file - TypeError: save_stream() got unexpected keyword argument in mock call

Diagnosis:
- Failure Type: **Case A (Stale Test)**
- Explanation: The unit test mock in `test_local_storage.py` was authored against the previous signature. The human's addition of `overwrite: bool = True` is an intentional contract change.
- Proposed Action: Update the test call in `loop_tests/` to match the new signature (preserving test assertions).

Applying test update via patch tool:
$ git add code/loop_tests/storage/test_local_storage.py
$ pytest code/loop_tests/storage/test_local_storage.py
4 passed in 0.28s.
$ pytest code/tests/
All 42 regression tests pass.
```

---

## 6. Step 7: Impact List Artifact & Two-Stage Approval Gate

```markdown
Agent:
A behavioral change was introduced (`StorageProvider.save_stream()` signature modified).

Generating the Impact List analysis as a conversation artifact structured via hierarchical-outline:
Artifact: `impact-list-T1.md` (RequestFeedback: true, UserFacing: true)

Hierarchical Outline Preview:
- Triggering Modification:
  - Source: Human edit on `code/storage/interface.py`
  - Contract: Added `overwrite: bool = True` to `StorageProvider.save_stream()`
- Codebase Ripple Effect:
  - `code/storage/s3.py`: Needs matching signature and overwrite check in `S3StorageProvider.save_stream()`
- Downstream Task Alignment:
  - `T3`: Caller `DocumentService.persist_document` must pass `overwrite=True`
  - Cascade Metric: 1 upcoming task affected ($\le 2$ -> Safe to proceed)
- Proposed Diff for `proposals/storage-migration/proposal.md`:
```diff
--- a/proposals/storage-migration/proposal.md
+++ b/proposals/storage-migration/proposal.md
@@ -75,3 +75,3 @@
-    async def save_stream(self, key: str, stream: AsyncIterator[bytes]) -> StorageObjectMetadata:
+    async def save_stream(self, key: str, stream: AsyncIterator[bytes], overwrite: bool = True) -> StorageObjectMetadata:
```

Please review the impact list artifact. Do you approve proceeding with this change?
```

### Stage 1: User Approves Impact List Artifact

```markdown
User: Approved, update the proposal.

Agent:
Applying proposed diff to `proposals/storage-migration/proposal.md` via patch tool.
Staging updated proposal and status tracker:
$ git add proposals/storage-migration/proposal.md proposals/storage-migration/status.md

### Stage 2 Approval Gate: Updated Proposal Verification

Proposed update applied to `proposals/storage-migration/proposal.md`:
```diff
--- a/proposals/storage-migration/proposal.md
+++ b/proposals/storage-migration/proposal.md
@@ -75,3 +75,3 @@
-    async def save_stream(self, key: str, stream: AsyncIterator[bytes]) -> StorageObjectMetadata:
+    async def save_stream(self, key: str, stream: AsyncIterator[bytes], overwrite: bool = True) -> StorageObjectMetadata:
```

Please review and approve the updated proposal.md before I implement changes in downstream modules (`s3.py` and `T3`).
```

### Stage 2: User Approves Updated Proposal

```markdown
User: Proposal looks great. Proceed with updating the modules.

Agent:
Applying patch to `code/storage/s3.py` to match the updated contract.
$ git add code/storage/s3.py
```

---

## 7. Pre-Commit Walkthrough

```markdown
Agent:
### Pre-Commit Source Control Walkthrough (T1)

$ git diff HEAD --stat
 code/loop_tests/storage/test_local_storage.py   | 12 ++++++++++++
 code/storage/factory.py                         | 28 ++++++++++++++++++++++++++++
 code/storage/interface.py                       | 34 ++++++++++++++++++++++++++++++++++
 code/storage/local.py                           | 82 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 code/storage/s3.py                              | 94 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
 proposals/storage-migration/proposal.md         |  2 +-
 proposals/storage-migration/status.md           | 25 +++++++++++++++++++++++++
 7 files changed, 276 insertions(+), 1 deletion(-)

Verification Checklist:
- [x] All T1 acceptance criteria met in proposal.md.
- [x] `code/loop_tests/storage/` pass (4/4 passed).
- [x] Curated test suite passes (42/42 passed).
- [x] Human explicit approval granted.

Have you made any further changes, or are we ready to commit Task T1 and move to T2?
```

---

## 8. Commit & Transition

```markdown
User: Ready to commit and move on.

Agent:
1. Updating `proposals/storage-migration/status.md`:
   - Marked T1 as `[DONE]`.
   - Appended Decisions Log entry:
     ```markdown
     ### Decisions Log
     - Task T1 (2026-10-30):
       - Decision: Added `overwrite: bool = True` to `StorageProvider.save_stream()`.
       - Rationale: Human review identified risk of accidental blob overwrites.
       - Affected Modules: `s3.py`, `local.py`, `interface.py`, and downstream task T3.
       - Proposal Updated: Yes
       - Approved By: User
     ```
2. Committing task checkpoint:
   $ git add .
   $ git commit -m "T1: Pluggable Storage Abstraction Layer"
   [master def5678] T1: Pluggable Storage Abstraction Layer
   7 files changed, 277 insertions(+), 1 deletion(-)

Task T1 is complete. Moving to Task T2: Service Layer Orchestration.
```
