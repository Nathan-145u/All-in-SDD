---
name: accept-feature
description: Check feature completeness, verify against acceptance criteria
user_invocable: false
---

# /sdd:accept-feature

Perform completeness verification for a feature, checking whether implementation meets the spec's acceptance criteria.

## Parameters

- `feature-path` (required): e.g., `specs/v0.4/001-ai-qa`

## Steps

### Document Completeness Check

1. Verify spec.md exists (required). Check if plan.md and tasks.md exist (optional for tailored features — see `ref/pipeline.md` §2.8).
2. If tasks.md exists, verify all tickets have `done` status. If any non-done tickets → report and stop.
3. If tasks.md does not exist (tailored feature), skip ticket verification and proceed directly to acceptance criteria check.

### Spec Acceptance Criteria Check

4. Read the "Acceptance Criteria" section of spec.md.
5. Verify each checklist item:
   - Read relevant code files to confirm implementation matches.
   - If automatically verifiable (e.g., file exists, function signature matches) → auto-mark.
   - If requiring human judgment (e.g., UI behavior) → list as pending user confirmation.

6. Output the verification report:

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

### README Onboarding Check

7. Check if the feature introduced setup-related changes (new env vars, dependencies, services, database, build steps). If so, verify that README.md:
   - Lists all required environment variables with how to obtain them
   - Documents any new prerequisites (runtimes, tools, SDKs)
   - Includes step-by-step database setup instructions (if applicable)
   - Explains how to get credentials for new third-party services
   - Has accurate build/run commands
   - If README.md is missing or incomplete → report as `[FAIL]` in the verification report

### Critical Path Check

8. Read the "Critical Path" section of spec.md.
9. If E2E test files exist, run the tests and report results.
10. If no E2E tests exist, list critical path items as pending manual verification.

### Completion

11. **All acceptance criteria pass:**
    - **Status update rule** (write to `docs/VERSION_PLAN.md` → Feature Status table):
      - `in_progress` → `accepted`
      - `accepted` → no change (already verified)
      - `closed` → no change (re-verification during `accept-version`; do not regress closed features)
    - Report verification complete.
    - Tell the user:
      - If status was just changed to `accepted`: "Feature verification passed. Next step is to sync documents and formally close this feature. Continue?"
      - If status was already `accepted` or `closed` (re-verification case): "Feature re-verified, status unchanged."

12. **If items fail:**
    - Do NOT update VERSION_PLAN.md (status stays whatever it was).
    - List all issues.
    - Tell the user: "Verification failed with <N> issues. Next step options: fix the issues, or revise the spec if the spec itself is wrong. What would you like to do?"
