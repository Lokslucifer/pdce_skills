---
trigger: always_on
description: Resolve relative resource paths from the directory of the file containing the reference, while treating absolute paths as workspace-level paths.
---

# Path Resolution Rules

## 1. Relative Paths

For any relative filesystem path referenced in a file, resolve it relative to the directory containing that file.

This applies to:
- Skills and `SKILL.md` files
- Skill resources and nested references
- Scripts and executables
- Configuration files
- Templates and supporting documents

Never resolve a relative resource path against the current working directory by default.

## 2. Absolute Paths

Paths explicitly provided as absolute paths are workspace-level paths.

- Use the provided absolute path directly.
- Do not prepend the current file's directory.
- Do not reinterpret it as a skill-relative path.

## 3. Nested Resource Resolution

Always use the file containing the reference as the resolution base.

### Example Hierarchy

```text
workspace/
├── project/
│   └── main.md
└── skills/
    └── my-skill/
        ├── SKILL.md
        ├── resources/
        │   ├── resource1.md
        │   └── nested/
        │       └── resource2.md
        └── scripts/
            └── hello.py
```

### Resolution Rules in Practice

- `SKILL.md` references `resources/resource1.md` → resolved relative to `skills/my-skill/`
- `resource1.md` references `nested/resource2.md` → resolved relative to `skills/my-skill/resources/`
- `resource1.md` references `../scripts/hello.py` → resolved relative to `skills/my-skill/resources/` (resolves to `skills/my-skill/scripts/hello.py`)

Each relative path is resolved from the directory of the file containing that reference.

## 4. Path Resolution Procedure

Before accessing a referenced file:

1. **Identify Source**: Identify the file containing the path reference.
2. **Determine Base Directory**: Determine that file's absolute directory.
3. **Resolve Relative Path**: Resolve the relative path against that directory.
4. **Normalize**: Normalize the resulting path (canonicalizing separators, `.` and `..`).
5. **Verify**: Verify that the target exists before accessing or executing it.

## 5. Important Constraints

- Do not assume the current working directory is the reference directory.
- Do not assume all skill resources are inside the workspace.
- Preserve absolute paths as provided.
- Do not guess missing paths.
- If the containing file's location cannot be determined, report the limitation rather than guessing.

## 6. Core Principle

> [!IMPORTANT]
> **Relative resource paths belong to the file that references them. Absolute paths belong to the workspace-level filesystem context and must be used directly.**
