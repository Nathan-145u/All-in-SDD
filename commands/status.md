---
description: Show current SDD project progress and drive the next step
argument-hint: '[version]'
---

# /sdd:status

The primary entry point for the SDD workflow. Shows project progress and offers to continue to the next step.

## Parameters

- `version` (optional): e.g., `v0.4`. Omit to show an overview of all versions.

## Steps

### 1. Scan and Report

1. Scan the `specs/` directory structure.

2. For each version/feature, gather:
   - Which files exist in the feature directory (research / spec / plan / tasks)
   - Ticket status distribution in tasks.md
   - Feature status from the Feature Status table in `docs/VERSION_PLAN.md` (`in_progress` / `accepted` / `closed` / `abandoned`)
   - Version status from the Version Status table in `docs/VERSION_PLAN.md` (`in_progress` / `accepted` / `closed`)

3. Output format:

```
SDD Project Status

v0.4 [ACCEPTED] — 3/3 features closed, awaiting version close
  [CLOSED]    001-ai-qa            T001-T003  3/3 done        ✅
  [CLOSED]    002-batch-download   T004-T005  2/2 done        ✅
  [CLOSED]    003-ipad-mac-layout  T006-T008  3/3 done        ✅

v0.5 — 0/2 features done
  [SPEC]      001-subtitle-export  spec only (no plan/tasks yet)  📝
  [EMPTY]     002-fix-timeout      empty (no files yet)           ⬜
  [ABANDONED] 003-old-feature      abandoned                      🚫

Next action: → Close version v0.4 (merge release/v0.4 → main)
```

4. If `docs/BACKLOG.md` exists, append a backlog summary line showing total item count and priority breakdown. If the file does not exist, skip this line.

   Example: `Backlog: 10 items (2 🔴, 4 🟡, 4 🟢)`

5. Feature status markers:
   - `[CLOSED]` ✅ — Feature verified and closed (status in VERSION_PLAN.md is `closed`)
   - `[ACCEPTED]` ☑️ — Feature verified, not yet closed (status is `accepted`)
   - `[DONE]` 🟢 — All tickets done, not yet verified
   - `[WIP]` 🔄 — Has ticket(s) in_progress or for_review
   - `[SPEC]` 📝 — Has spec but no plan/tasks
   - `[PLAN]` 📋 — Has spec + plan but no tasks yet
   - `[RESEARCH]` 🔍 — Has research.md but no spec yet
   - `[EMPTY]` ⬜ — Empty directory
   - `[ABANDONED]` 🚫 — Abandoned

6. Version status markers:
   - `[CLOSED]` — Merged to main
   - `[ACCEPTED]` — All features closed, version verified, awaiting merge to main
   - (no marker) — version in progress

### 2. Determine Next Action

7. Identify the next action by scanning features in order. The first match wins.

   **Scanning order:** iterate versions in ascending order (v0.1 → v0.2 → ...). Within each version, iterate features in ascending number order (001 → 002 → ...). Skip `abandoned` features. The first feature/version state that matches a rule wins.

| Feature/Version State | Next Action |
|-----------------------|-------------|
| Empty feature directory (no files) | Start grooming: create research.md + spec.md |
| Has research.md but no spec.md | Continue grooming: finish spec.md |
| Has spec.md with `[NEED CLARIFICATION]` tags | Resolve open questions in spec |
| Has spec.md but no plan.md | Generate plan.md + tasks.md |
| Has plan.md but no tasks.md | Generate tasks.md |
| Has tasks.md with `planned` tickets (dependencies met) | Execute next ticket |
| All tickets `done`, feature status is not `accepted`/`closed` | Verify feature |
| Feature status is `accepted` | Close feature |
| All features in version `closed` or `abandoned`, version status is not `accepted`/`closed` | Verify version |
| Version status is `accepted` | Close version |
| Version status is `closed` and no other active work | Suggest starting a new version or promoting from backlog |

8. Present the next action to the user:
   ```
   Next action: → [description of what will happen next]
   Continue? (or tell me what you'd like to do instead)
   ```

### 3. Auto-Continue

9. When the user confirms (e.g., "next", "continue", "yes"), load and execute the appropriate internal skill:

| Next Action | Internal Skill to Load |
|-------------|----------------------|
| Start/continue grooming | Read and follow `skills/groom/SKILL.md` |
| Generate plan + tasks | Read and follow `skills/plan/SKILL.md` |
| Execute next ticket | Read and follow `skills/exec/SKILL.md` |
| Verify feature | Read and follow `skills/accept-feature/SKILL.md` |
| Close feature | Read and follow `skills/close-feature/SKILL.md` |
| Verify version | Read and follow `skills/accept-version/SKILL.md` |
| Close version | Read and follow `skills/close-version/SKILL.md` |

To load an internal skill: read its `SKILL.md` file and follow its instructions step by step, as if the user had invoked it directly.

10. If the user wants to do something else (e.g., "I want to revise the spec", "abandon this feature", "check the backlog"), follow their request instead. The internal skills are still accessible — just read the appropriate SKILL.md and follow it.

### 4. After Each Step Completes

11. After any internal skill finishes its work, return to step 1: re-scan, re-report, and suggest the next action. This creates a continuous loop:

```
/sdd:status → report → suggest next → user confirms → execute → report → suggest next → ...
```

The user stays in this loop until they stop or switch to a different task.
