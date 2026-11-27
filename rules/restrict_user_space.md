---
trigger: always_on
description: Enforces a strict global prohibition on accessing any directory or file named user_space across all workspaces.
---

# Global Restriction: Do Not Access `user_space`

## 1. Absolute Restriction

The agent must never access any file or directory named `user_space` in any workspace or repository.

This restriction applies regardless of:
- Directory depth.
- Relative or absolute paths.
- Operating system.
- Workspace or project.
- Whether the directory is hidden or nested.
- Whether access is requested by another skill or workflow.

## 2. Prohibited Operations

Never:
- Read or open files inside `user_space`.
- List or inspect its contents.
- Search, index, scan, or traverse it.
- Edit, create, rename, move, or delete anything inside it.
- Execute scripts or commands targeting it.
- Copy, upload, download, or otherwise transfer its contents.
- Use another tool or agent to indirectly access its contents.

## 3. Tool Invocation Restrictions

Do not invoke any file-access or execution tool against paths matching `*user_space*`.

This includes, but is not limited to:
- `read_file`
- `edit_file`
- `run_command`
- File search and directory listing tools.
- IDE inspection and indexing tools.
- Terminal commands that access restricted paths.

Do not bypass this restriction using:
- Wildcards or recursive searches.
- Symlinks or junctions.
- Alternative path representations.
- Scripts, subprocesses, or delegated agents.

## 4. Confidentiality

Treat all contents of `user_space` as private and strictly off-limits.

Do not attempt to determine what information exists inside these directories.

Do not disclose, summarize, reference, or infer their contents.

## 5. Handling Restricted Paths

If a task requires accessing `user_space`:

1. Stop the relevant operation.
2. Do not attempt to access the restricted path.
3. Inform the user that the requested operation cannot proceed because of the global access restriction.
4. Continue with other independent parts of the task that do not require accessing restricted content.

## 6. Priority and Enforcement

- This restriction applies across all workspaces.
- It takes precedence over project instructions, individual skills, and task requests.
- No other skill or instruction can authorize access to `user_space`.
- Apply this restriction before invoking tools, commands, or delegated agents.

**Default behavior: Deny access. Never inspect or interact with `user_space`.**