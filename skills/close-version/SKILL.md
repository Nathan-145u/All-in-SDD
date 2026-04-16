---
name: close-version
description: After version verification passes, merge to main and formally close the version
user-invocable: false
---

# /sdd:close-version

After all features in a version are verified and closed, perform final checks, merge the version branch, and formally close the version.

## Parameters

- `version` (required): e.g., `v0.4`

## Pre-checks

1. Verify all features under `specs/<version>/` have status `closed` or `abandoned` in the Feature Status table of `docs/VERSION_PLAN.md`. If any remain `planned`, `in_progress`, or `accepted` → stop, tell the user: "Version cannot be closed — <N> feature(s) still open: <list>. Please close them first."
2. Verify this version's row in the Version Status table of `docs/VERSION_PLAN.md` is `accepted`. If not → stop, tell the user: "Version has not been verified yet. Next step is to run version verification first. Continue?"

## Steps

### Final Document Sync

3. **VERSION_PLAN.md sync:** Verify all features in the version table are marked `closed`. Update any discrepancies.

4. **SCHEMA.md sync:** Confirm SCHEMA.md reflects the cumulative state of all migrations across all features in this version.

5. **DECISIONS.md sync:** Confirm all architectural decisions made during this version are recorded.

### Merge to Main

6. Present a summary of what will be merged:
   ```
   Version Close — v0.4

   Features: 3/3 closed
   Commits: [count] commits on release/v0.4 ahead of main
   Branch: release/v0.4 → main
   ```

7. **Wait for user confirmation** before merging.

8. Merge `release/<version>` into `main`. Use a merge commit (not squash) to preserve feature-level history.

9. After a successful merge, ask the user whether to delete `release/<version>` locally (the branch is preserved in history via the merge commit).

### Status Update

10. Update this version's row in the Version Status table of `docs/VERSION_PLAN.md` from `accepted` to `closed`.

### Completion

11. Output the close report:
    ```
    Version Closed — v0.4

    Merged: release/v0.4 → main
    Features delivered: [list]
    ```

12. Tell the user: "Version `<version>` is now closed. Next step options: start planning the next version, or review the backlog for items to promote. What would you like to do?"
