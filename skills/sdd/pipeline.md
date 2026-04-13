# 2. SDD Pipeline

When the user requests a feature implementation, follow these steps.

## Overview: Stage Gates and Backtracking

```
spec.md ──[gate]──► plan.md ──► tasks.md ──► implementation
(includes research)
   ▲                  ▲            ▲
   └──────────────────┴────────────┘
          Backtrack on issues
```

- **The spec → plan gate is the critical gate.** The spec is the contract; plan and tasks can be redone, but spec rework means the entire chain is reworked.
- **Gate blocking condition:** A spec containing `[NEED CLARIFICATION]` tags cannot proceed to the next stage.
- **Backtrack rule:** When downstream problems are found, update the upstream file — do not patch downstream.
- **Each stage can be skipped as needed** (see §2.8).

## 2.1 Writing the Spec

Spec writing is a complete process that includes research:

```
User gives vague requirement
       ↓
AI researches codebase ──► Discovers constraints and patterns ──► Generates research.md
       ↓                                                              ↓
User decides ◄────────────────────────────────────────────────────────┘
       ↓
AI scaffolds + probes + adversarial review
       ↓
Produces spec.md
```

**Steps:**

1. **Research.** Scan the codebase, identify relevant files, existing patterns, and potential conflicts. Read existing specs in the same version to check for scope overlap. Generate `research.md`.

**research.md template:**

```markdown
# Research — [Feature Name]

## Relevant Files
- path/to/file.swift — Why it's relevant to this feature

## Existing Patterns
- How similar functionality is implemented in the project

## Potential Conflicts
- What existing code this feature might conflict with

## Dependencies
- Schema/ADR/third-party APIs to understand first

## Technical Approach Options
- Option A: [description] — pros / cons
- Option B: [description] — pros / cons
- Recommended: [Option X], rationale: ...

## Risk Assessment
- Security: [Does it involve authentication, data access, user input?]
- Performance: [Impact on existing system performance]
- Compatibility: [Impact on existing features]
```

2. **Scaffold.** Generate spec template (see standards.md §3.1), pre-fill known information (from research results), leave blanks where user decisions are needed. Use `[NEED CLARIFICATION]` to tag items that cannot be determined independently.
3. **Probe.** Ask the user structured questions about `[NEED CLARIFICATION]` items and other blanks. Prioritize: scope boundaries, error scenarios, acceptance criteria.
4. **Adversarial review.** After the draft is complete, proactively analyze missing edge cases and security risks, and add findings as constraints.

| Exit Criteria | Who Approves |
|---------------|--------------|
| Passes standards.md §3.2 completeness checklist with no `[NEED CLARIFICATION]` tags remaining | **Human must approve** |

## 2.2 Planning

Generate `plan.md`: the technical implementation approach for the feature.

**plan.md template:**

```markdown
# Plan — [Feature Name]

## Prerequisites
- [If cross-feature dependencies exist, list the dependent feature paths and their current status]
- No cross-feature dependencies

## Technical Approach
- Chat uses stateless agent pattern, Edge Function does not write to DB
- iOS chat history maintained in-memory only, cleared on close
- Streaming response uses SSE format

## File Change List
- Add: supabase/functions/chat/index.ts
- Add: Views/ChatPanelView.swift, Services/ChatService.swift
- Modify: docs/SCHEMA.md
- Modify: SettingsView.swift (add AI settings section)
```

**Rules:**
- If a new project-level architectural decision is made, write it to `DECISIONS.md` and reference the ADR number in plan.md.
- If cross-feature dependencies exist, verify that all tickets in the depended-upon feature are `done`; otherwise block.

| Exit Criteria | Who Approves |
|---------------|--------------|
| Technical approach is clear, file change list is complete, prerequisites met or no dependencies | Human approves |

## 2.3 Task Breakdown

Generate `tasks.md`: break the plan into atomic work packages.

**Ticket numbering rule:** Globally incrementing, not reset per feature. Before creating a new tasks.md, scan all existing tasks.md files under `specs/` to find the maximum ticket number, then start from the next number.

**tasks.md template:**

```markdown
# Tasks — [Feature Name]

## T001: Database Migration
- Status: planned
- Files: supabase/migrations/003_add_chat.sql, docs/SCHEMA.md
- Done Definition: Migration executes without errors, SCHEMA.md updated
- Dependencies: none
- Manual Intervention: none

## T002: Edge Function — chat
- Status: planned
- Files: supabase/functions/chat/index.ts
- Done Definition: SSE streaming response works, user key + fallback logic works
- Dependencies: T001
- Manual Intervention: none

## T003: Configure Third-Party API Keys
- Status: planned
- Files: none (environment configuration)
- Done Definition: API keys configured in Supabase Secrets
- Dependencies: T001
- Manual Intervention: [HUMAN REQUIRED] User must manually configure keys in Supabase Dashboard
```

**`[HUMAN REQUIRED]` rules:**
- During the plan stage, identify all steps requiring manual intervention and mark the "Manual Intervention" field in tasks.md with `[HUMAN REQUIRED]` plus a description of the specific action.
- Common scenarios: environment variable configuration, third-party service registration, App Store submission, manual testing, key configuration.
- Even if not anticipated during the plan stage, **immediately notify the user** when manual intervention is discovered during the exec stage — do not skip or attempt to handle it yourself.

**Status transitions:** `planned → in_progress → for_review → done`

- `for_review` means: **Implementation is complete, waiting for the user to confirm the ticket's done definition is satisfied.** This is not code review (code review is in §2.5); it is user acceptance of a single ticket's deliverable.

**AI execution rules:**
- Start with the first ticket in `planned` status whose dependencies are all `done`
- Change status to `in_progress` before starting
- Change status to `for_review` after implementation, wait for user confirmation
- Change status to `done` after user confirms
- Execute one ticket at a time, then pick the next
- If backtracking is needed (spec gap discovered), revert the current ticket to `planned` and fix the upstream file first
- `[HUMAN REQUIRED]` ticket: change status to `in_progress`, immediately notify the user of the manual steps needed, list specific actions, wait for user to complete and confirm

| Exit Criteria | Who Approves |
|---------------|--------------|
| Each ticket has a done definition and dependency declaration, independently executable | AI self-check |

## 2.4 Implementation

- **Isolation:** Create a feature branch `feat/<version>/<feature-name>` (e.g., `feat/v0.4/001-ai-qa`).
- Use TDD: write failing tests from acceptance criteria, then implement until tests pass.
- Execute tickets sequentially as ordered in tasks.md.
- If implementation reveals a situation the spec didn't anticipate → **pause, propose spec revision, wait for user confirmation before continuing**.

## 2.5 Review

- Review should be done in a new session (or using a dedicated review agent).
- Load only: original spec + code diff.
- Check strictly against acceptance criteria item by item.

## 2.6 Sync and Close

After feature completion, use `/sdd-close-feature` to perform these sync operations:
- Update spec (if revised during implementation).
- Update `SCHEMA.md` (if data model changed).
- Update `DECISIONS.md` (if new project-level architectural decisions were made).
- Update `VERSION_PLAN.md` with the feature's status.
- Mark all tickets in tasks.md as `done`.

## 2.7 Abandonment

When a feature is no longer being pursued, use `/sdd-abandon-feature`:
- Mark all incomplete tickets as `abandoned`
- Mark the feature as abandoned in VERSION_PLAN.md
- Preserve existing files as historical record — do not delete

## 2.8 Tailoring

Not every feature needs all files:

| Scenario | Files Needed |
|----------|-------------|
| Brand new complex feature | research + spec + plan + tasks |
| Enhancement to existing module | spec + plan + tasks |
| Clear bug fix | spec + tasks (tasks derived directly from spec, skip plan) |
| Single-file small change | spec only |
