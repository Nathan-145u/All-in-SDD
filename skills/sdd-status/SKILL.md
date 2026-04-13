---
name: sdd-status
description: Show current SDD project progress overview
user_invocable: true
arg_description: "[version] — optional, e.g., v0.4. Omit to show all versions"
---

# /sdd-status

Scan the specs/ directory and display version, feature, and ticket status.

## Parameters

- `version` (optional): e.g., `v0.4`. Omit to show an overview of all versions.

## Steps

1. Scan the `specs/` directory structure.

2. For each version/feature, gather:
   - Which files exist in the feature directory (research / spec / plan / tasks)
   - Ticket status distribution in tasks.md

3. Output format:

```
SDD Project Status

v0.4 — 2/3 features done
  [DONE]      001-ai-qa            T001-T003  3/3 done        ✅
  [DONE]      002-batch-download   T004-T005  2/2 done        ✅
  [WIP]       003-ipad-mac-layout  T006-T008  1/3 done (T007 in_progress) 🔄

v0.5 — 0/2 features done
  [SPEC]      001-subtitle-export  spec only (no plan/tasks yet)  📝
  [EMPTY]     002-fix-timeout      empty (no files yet)           ⬜
  [ABANDONED] 003-old-feature      abandoned                      🚫

Next executable ticket: T007 — iPad two-column layout NavigationSplitView
```

4. Status markers:
   - `[DONE]` ✅ — All tickets done
   - `[WIP]` 🔄 — Has ticket(s) in_progress or for_review
   - `[SPEC]` 📝 — Has spec but no tasks
   - `[EMPTY]` ⬜ — Empty directory
   - `[ABANDONED]` 🚫 — Abandoned

5. If a version was specified, show a detailed view (each ticket listed with its status).
