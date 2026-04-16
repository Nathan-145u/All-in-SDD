---
name: plan
description: Generate technical plan (plan.md) and work packages (tasks.md) from a spec
user_invocable: false
---

# /sdd:plan

Based on an approved spec, generate a technical implementation plan and atomic work packages.

## Parameters

- `feature-path` (required): e.g., `specs/v0.4/001-ai-qa`

## Pre-checks (Gate)

1. Verify `spec.md` exists.
2. Validate the spec against `ref/standards.md` §3.2 completeness checklist.
3. If any items fail (including remaining `[NEED CLARIFICATION]` tags) → **stop**, tell the user: "Spec is incomplete (<N> issues: <list>). Planning is blocked until the spec passes the completeness checklist. Next step is to revise the spec — want to start that now?"
4. If the spec declares prerequisite feature dependencies, verify all tickets in the depended-upon feature are `done`. Not complete → **stop**, report the blocking reason.

## Steps

### Generate plan.md

5. Read spec.md + research.md + SCHEMA.md + DECISIONS.md.
6. Generate `plan.md`:

```markdown
# Plan — [Feature Name]

## Prerequisites
- [If cross-feature dependencies exist, list the dependent feature paths and their current status]
- No cross-feature dependencies

## Technical Approach
- [approach description]

## File Change List
- Add: [file path]
- Modify: [file path] — [reason for modification]
```

7. If a new project-level architectural decision is made, write it to `DECISIONS.md` and reference the ADR number in plan.md.
8. Request user approval for the plan.

### Generate tasks.md

9. Read `docs/COUNTERS.md` to get the current T-number N.
10. Break the plan into atomic work packages, numbering from T(N+1).
11. For each ticket, determine if manual intervention is needed. Common `[HUMAN REQUIRED]` scenarios:
    - Environment variable / key configuration
    - Third-party service registration or approval
    - App Store / platform submission
    - Manual testing requiring physical devices or external systems
12. Generate `tasks.md`:

```markdown
# Tasks — [Feature Name]

## T(N+1): [task name]
- Status: planned
- Files: [file list]
- Done Definition: [verifiable completion condition]
- Dependencies: none
- Manual Intervention: none

## T(N+2): [task name]
- Status: planned
- Files: [file list]
- Done Definition: [verifiable completion condition]
- Dependencies: T(N+1)
- Manual Intervention: [HUMAN REQUIRED] [specific action description]
```

13. Present the complete task list. If there are `[HUMAN REQUIRED]` tickets, **specifically highlight them to the user**.
14. Request user confirmation.
15. Update `docs/COUNTERS.md` with the new maximum T-number.
16. **Commit grooming artifacts.** Commit plan.md and tasks.md on the feature branch with message `chore: grooming for <feature-name>`. Always create a new commit — do not amend the previous grooming commit (amending can break history if it has already been pushed).
17. After completion, tell the user: "Plan and tasks are ready. Next step is to execute the first ticket (TDD: write tests, implement, review). Continue?"
