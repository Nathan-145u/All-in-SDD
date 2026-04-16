---
description: Create a new version directory and update the version plan
argument-hint: '<version>'
---

# /sdd:new-version

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

4. **Update `docs/VERSION_PLAN.md`.** Ensure it has these three sections (create any that don't exist):

   **Roadmap:** Add a new row summarizing this version.
   ```markdown
   ## Roadmap

   | Version | Theme | Features |
   |---------|-------|----------|
   | v0.5 | [theme] | 001-feature-name, 002-feature-name, 003-fix-known-bug |
   ```

   **Version Status:** Add a row for this version with status `in_progress`.
   ```markdown
   ## Version Status

   | Version | Status |
   |---------|--------|
   | v0.5 | in_progress |
   ```

   **Feature Status:** Add a row per feature with status `planned`. Features are `planned` when the directory exists but no work has started (no research/spec/plan/tasks yet). The first time `skills/groom/SKILL.md` writes `research.md`, it flips the status to `in_progress`.
   ```markdown
   ## Feature Status

   | Feature | Status |
   |---------|--------|
   | v0.5/001-feature-name | planned |
   | v0.5/002-feature-name | planned |
   | v0.5/003-fix-known-bug | planned |
   ```

   Status values: `planned` → `in_progress` → `accepted` → `closed` (set by groom / accept-* / close-* skills) or `abandoned` (set by abandon).

5. Pre-create directories for each feature the user listed (empty directories):

```
specs/v0.5/
├── 001-feature-name/
├── 002-feature-name/
└── 003-fix-known-bug/
```

6. For any backlog items included in this version, remove them from BACKLOG.md.

7. **Create version branch.** Create `release/<version>` from `main` (e.g., `release/v0.5`). This branch serves as the integration point for all tickets in this version.

8. After completion, tell the user: "Version `<version>` is set up with <N> feature(s) planned. Next step is to start grooming the first feature (`001-<name>`) — research the codebase and write its spec. Continue?"
