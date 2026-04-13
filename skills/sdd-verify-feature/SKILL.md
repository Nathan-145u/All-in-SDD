---
name: sdd-verify-feature
description: Check feature completeness, verify against acceptance criteria
user_invocable: true
arg_description: "<feature-path> — e.g., specs/v0.4/001-ai-qa"
---

# /sdd-verify-feature

Perform completeness verification for a feature, checking whether implementation meets the spec's acceptance criteria.

## Parameters

- `feature-path` (required): e.g., `specs/v0.4/001-ai-qa`

## Steps

### Document Completeness Check

1. Verify spec.md exists (required). Check if plan.md and tasks.md exist (optional for tailored features — see pipeline.md §2.8).
2. If tasks.md exists, verify all tickets have `done` status. If any non-done tickets → report and stop.
3. If tasks.md does not exist (tailored feature), skip ticket verification and proceed directly to acceptance criteria check.

### Spec Acceptance Criteria Check

3. Read the "Acceptance Criteria" section of spec.md.
4. Verify each checklist item:
   - Read relevant code files to confirm implementation matches.
   - If automatically verifiable (e.g., file exists, function signature matches) → auto-mark.
   - If requiring human judgment (e.g., UI behavior) → list as pending user confirmation.

5. Output the verification report:

```
Verification Report — 001-ai-qa

Acceptance Criteria: 7/7
  [PASS] Long-press subtitle → "Ask AI" opens chat sheet
  [PASS] Chat shows quoted context, supports follow-up
  [PASS] Preset templates (Explain / Vocab / Context) work
  [PASS] Streaming AI responses display token-by-token
  [PASS] Chat clears when sheet dismissed
  [PASS] User API key entry with Keychain storage
  [PASS] User key used for Q&A when configured

Document Sync:
  [OK]   SCHEMA.md matches actual migration
  [OK]   DECISIONS.md has no unrecorded architectural decisions
  [WARN] spec.md §3.2 description has minor discrepancy with implementation (see below)

Issues:
  1. Spec requires "Chat history clears on episode change"
     but ChatService.swift does not listen for episode switch events.
```

### Critical Path Check

6. Read the "Critical Path" section of spec.md.
7. If E2E test files exist, run the tests and report results.
8. If no E2E tests exist, list critical path items as pending manual verification.

### Completion

9. All acceptance criteria pass → report feature verification complete, prompt the next step: `/sdd-close-feature <feature-path>` to sync and close.
10. If items fail → list issues, suggest fixes or suggest running `/sdd-propose` to revise the spec.
