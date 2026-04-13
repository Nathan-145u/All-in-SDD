---
name: sdd-close-feature
description: After feature verification passes, sync documents and formally close the feature
user_invocable: true
arg_description: "<feature-path> — e.g., specs/v0.4/001-ai-qa"
---

# /sdd-close-feature

After feature verification passes, execute document sync, update status, and formally close the feature.

## Parameters

- `feature-path` (required): e.g., `specs/v0.4/001-ai-qa`

## Pre-checks

1. Verify tasks.md exists and all tickets have `done` status. If any non-done tickets → report and stop, suggest running `/sdd-do` or `/sdd-verify-feature` first.
2. Recommend the user run `/sdd-verify-feature` first to confirm all acceptance criteria pass (if not already run).

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

7. **VERSION_PLAN.md sync:** Update the feature's status to complete in the version table.

### Status Confirmation

8. Present the sync report to the user:

```
Feature Close Report — [Feature Name]

Document Sync:
  [OK] spec.md — No revisions / Revisions synced
  [OK] SCHEMA.md — No changes / Updated
  [OK] DECISIONS.md — No new ADRs / Appended ADR-XXX
  [OK] README.md — No setup changes / Updated (new env vars, dependencies, etc.)
  [OK] VERSION_PLAN.md — Status updated

Tickets: X/X done
Branch: feat/<version>/<feature-name> — Ready to merge to main
```

9. Prompt the next step:
   - If the version has more features → continue to the next feature
   - If all features in the version are closed → prompt `/sdd-verify-version <version>` for version-level verification
