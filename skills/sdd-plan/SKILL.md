---
name: sdd-plan
description: Generate technical plan (plan.md) and work packages (tasks.md) from a spec
user_invocable: true
arg_description: "<feature-path> — e.g., specs/v0.4/001-ai-qa"
---

# /sdd-plan

Based on an approved spec, generate a technical implementation plan and atomic work packages.

## Parameters

- `feature-path` (required): e.g., `specs/v0.4/001-ai-qa`

## Pre-checks (Gate)

1. Verify `spec.md` exists.
2. Validate the spec against `sdd/standards.md` §3.2 completeness checklist.
3. If any items fail (including remaining `[NEED CLARIFICATION]` tags) → **stop**, prompt the user to run `/sdd-propose` to complete the spec first.
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

9. Scan all existing tasks.md files under `specs/` to find the maximum ticket number N.
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
15. After completion, prompt the next step: `/sdd-do` to start execution.
