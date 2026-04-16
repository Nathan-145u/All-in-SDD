---
name: accept-version
description: Check version completeness, verify all features meet acceptance criteria, run integration tests
user-invocable: false
---

# /sdd:accept-version

Perform completeness verification for all features under a version, including cross-feature integration testing.

## Parameters

- `version` (required): e.g., `v0.4`

## Pre-checks (Gate)

1. Scan all feature directories under `specs/<version>/`.
2. Check the Feature Status table in `docs/VERSION_PLAN.md`: all features for this version must have status `closed` or `abandoned`. If any are still `planned`, `in_progress`, or `accepted` → stop, tell the user: "Version cannot be verified — <N> feature(s) still need closing: <list>. Please close them first before running version verification."

## Steps

### Per-Feature Verification

3. For each feature, execute the full `/sdd:accept-feature` check workflow as a double-check (features were already verified individually, but this catches any regressions introduced by later features).

### Cross-Feature Integration Test

4. Run the full test suite (unit + integration + E2E) across the entire codebase to detect cross-feature conflicts:
   - Shared schema field conflicts
   - API route collisions
   - UI state interference between features
   - Dependency version conflicts

5. If no test suite exists or tests are incomplete:
   - Scan all specs in this version for overlapping file changes (compare file change lists across plan.md files)
   - Identify shared resources (same DB tables, same API endpoints, same UI components modified by multiple features)
   - List potential conflict points and present to the user for manual verification

### Version Report

6. Output a version-level summary report:

```
Version Verification Report — v0.4

Feature Completion: 3/3
  [PASS] 001-ai-qa            7/7 acceptance criteria passed
  [PASS] 002-batch-download   5/5 acceptance criteria passed
  [WARN] 003-ipad-mac-layout  4/5 acceptance criteria passed (1 failed)

Integration Tests:
  [PASS] Unit tests: 142/142 passed
  [PASS] Integration tests: 28/28 passed
  [WARN] E2E tests: 11/12 passed (1 failed — see below)

Cross-Feature Conflicts:
  [OK]   No shared schema conflicts detected
  [WARN] 001-ai-qa and 003-ipad-mac-layout both modify SettingsView.swift
         — verify no UI state interference

Failed Items:
  003-ipad-mac-layout:
    [FAIL] "macOS sidebar navigation with placeholder when no episode selected"
           MacLayout.swift missing placeholder view

Document Sync:
  [OK] VERSION_PLAN.md v0.4 description matches actual features
  [OK] SCHEMA.md matches all migrations
  [OK] DECISIONS.md has no gaps
```

### Completion

7. **All checks pass:**
   - Update `docs/VERSION_PLAN.md` → Version Status table: set this version's row to `accepted`. This persistent marker lets `status` distinguish verified versions from unverified ones.
   - Report version verification complete.
   - Tell the user: "Version verification passed. Next step is to merge `release/<version>` into `main` and formally close the version. Continue?"

8. **If items fail:**
   - Do NOT update VERSION_PLAN.md.
   - List all issues grouped by category (feature failures, test failures, cross-feature conflicts).
   - Tell the user: "Version verification failed. Suggested fix order: <list>. What would you like to do?"
