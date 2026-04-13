---
name: sdd-new-version
description: Create a new version directory and update the version plan
user_invocable: true
arg_description: "<version> — e.g., v0.5, v1.0, v2.3"
---

# /sdd-new-version

Create a new version directory and register it in VERSION_PLAN.md.

## Parameters

- `version` (required): format `v<major>.<minor>`, e.g., `v0.5`, `v1.0`, `v2.3`

## Steps

1. Validate parameters: version matches `v\d+\.\d+` format, `specs/<version>/` directory does not exist.

2. Create `specs/<version>/` directory.

3. Read `docs/BACKLOG.md` (if it exists). Present relevant backlog items to the user grouped by priority (🔴 → 🟡 → 🟢), then ask:
   - What is this version's theme/goal?
   - Which backlog items (if any) should be included in this version?
   - Any new features not in the backlog? (rough list is fine)
   - Are there known bugs to fix in this version?
   - Prerequisites: which version must be completed first?

4. Add a new row to the version table in `VERSION_PLAN.md`:

```markdown
| v0.5 | [theme] | [feature summary] | [feature summary] | [feature summary] |
```

5. Pre-create directories for each feature the user listed (empty directories):

```
specs/v0.5/
├── 001-feature-name/
├── 002-feature-name/
└── 003-fix-known-bug/
```

6. For any backlog items included in this version, remove them from BACKLOG.md.

7. After completion, prompt the next step: `/sdd-propose v0.5 001-feature-name` to start spec writing for the first feature.
