---
name: sdd-abandon-feature
description: Abandon a feature, mark all incomplete tickets as abandoned
user_invocable: true
arg_description: "<feature-path> — e.g., specs/v0.4/003-old-feature"
---

# /sdd-abandon-feature

Abandon a feature. Preserve all files as historical record but mark as no longer in progress.

## Parameters

- `feature-path` (required): e.g., `specs/v0.4/003-old-feature`

## Steps

1. Verify the feature path exists. Read all files in the feature directory.

2. Present the current status to the user:
   - List of existing files
   - Ticket status in tasks.md (if exists)
   - Amount of work already completed

3. **Require user confirmation to abandon.** Show the impact:
   - All incomplete tickets will be marked `abandoned`
   - Completed (`done`) tickets remain unchanged
   - Files will not be deleted (preserved as historical record)
   - VERSION_PLAN.md will be updated

4. After user confirms, execute:
   - Change all non-`done` tickets in tasks.md to `abandoned`
   - Mark the feature as `[ABANDONED]` in VERSION_PLAN.md
   - If a feature branch `feat/<version>/<feature-name>` exists, ask the user if they want to delete it

5. Ask the user if they want to record the abandonment reason. If yes, create or append to `research.md` in the feature directory:

```markdown
## Abandonment Record
- Date: [current date]
- Reason: [user-provided reason]
```

6. After completion, prompt `/sdd-status` to view the updated project status.
