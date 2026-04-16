---
name: close-feature
description: After feature verification passes, sync documents, merge the feature branch, and formally close the feature
user-invocable: false
---

# /sdd:close-feature

After feature verification passes, execute document sync, merge the feature branch back into the version branch, and formally close the feature.

## Parameters

- `feature-path` (required): e.g., `specs/v0.4/001-ai-qa`

## Pre-checks

1. Verify tasks.md exists and all tickets have `done` status. If any non-done tickets → report and stop.
2. Verify this feature's row in the Feature Status table of `docs/VERSION_PLAN.md` is `accepted`. If not → stop, tell the user: "Feature has not been verified yet. Next step is to run verification first. Continue?"

## Steps

### Document Sync

3. **Spec sync:** Check if spec.md was revised during implementation. If so, confirm the revisions are captured in spec.md.

4. **SCHEMA.md sync:** Check if the implementation includes data model changes (new tables, new columns, migration files). If so:
   - Compare SCHEMA.md with actual migrations, confirm consistency.
   - If inconsistent → update SCHEMA.md.

5. **DECISIONS.md sync:** Check if new project-level architectural decisions were made during implementation (ADRs referenced in plan.md, new tech choices made during implementation). If undocumented decisions exist → append to DECISIONS.md.

6. **README.md sync:** Check if the feature introduced any setup-related changes (new env vars, new dependencies, new services, new database setup, new build steps). If so:
   - Verify that README.md documents all setup steps needed to use the new functionality.
   - Check that environment variable tables are complete (variable name, purpose, how to obtain, which file).
   - Check that new prerequisites are listed with version requirements.
   - If README.md is missing or incomplete → update it before closing.

### Branch Merge

7. **Merge feature branch into version branch.**
   - Ensure the current working tree is clean (all document sync changes committed on the feature branch).
   - Switch to `release/<version>`.
   - Merge `feat/<version>/<feature-name>` into `release/<version>`. Use a merge commit (not squash) to preserve ticket-level history.
   - If conflicts arise → pause, report to the user, wait for resolution guidance. Do not auto-resolve.
   - After a successful merge, ask the user whether to delete the feature branch locally (the branch is preserved in history via the merge commit).

### Status Update

8. **VERSION_PLAN.md sync:** Update this feature's row in the Feature Status table from `accepted` to `closed`.

### Status Confirmation

9. Present the close report to the user:

```
Feature Close Report — [Feature Name]

Document Sync:
  [OK] spec.md — No revisions / Revisions synced
  [OK] SCHEMA.md — No changes / Updated
  [OK] DECISIONS.md — No new ADRs / Appended ADR-XXX
  [OK] README.md — No setup changes / Updated (new env vars, dependencies, etc.)
  [OK] VERSION_PLAN.md — Status: closed

Tickets: X/X done
Branch: feat/<version>/<feature-name> → release/<version> (merged)
```

10. **Tell the user what's next:**
    - If the version has other features not yet done: "Feature closed. Next step is to continue with the next feature in this version. Continue?"
    - If all features in the version are closed: "Feature closed. All features in <version> are now closed. Next step is version-level verification. Continue?"
