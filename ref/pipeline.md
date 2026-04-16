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

**Ticket numbering rule:** Globally incrementing, not reset per feature. Before creating a new tasks.md, read `docs/COUNTERS.md` to get the current T-number, then start from the next number. After creating all tickets, update COUNTERS.md with the new maximum.

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

- `for_review` means: **Waiting for the user (human) to review and confirm the ticket's done definition is satisfied.** By this point, AI code review (§2.5) has already passed and any issues have been resolved. This is the human review gate.

**AI execution rules:**
- Start with the first ticket in `planned` status whose dependencies are all `done`
- Change status to `in_progress` before starting
- After implementation: commit, run code review (§2.5), fix any issues, then change status to `for_review` and wait for user confirmation
- Change status to `done` after user confirms
- Execute one ticket at a time, then pick the next
- If backtracking is needed (spec gap discovered), revert the current ticket to `planned` and fix the upstream file first
- `[HUMAN REQUIRED]` ticket: change status to `in_progress`, immediately notify the user of the manual steps needed, list specific actions, wait for user to complete and confirm

| Exit Criteria | Who Approves |
|---------------|--------------|
| Each ticket has a done definition and dependency declaration, independently executable | AI self-check |

## 2.4 Implementation

### Branch Strategy

```
main
 └── release/v0.4                                ← version integration branch
      └── feat/v0.4/001-episode-list             ← feature branch (grooming + integration)
           ├── feat/v0.4/T005-add-chat           ← ticket branch → merge back to feature branch
           ├── feat/v0.4/T006-db-migration       ← ticket branch → merge back to feature branch
           └── feat/v0.4/T007-auth               ← ticket branch → merge back to feature branch
                                                     ↓
                                       feature branch → merge back to release/v0.4
                                                                ↓
                                                      release/v0.4 → merge back to main
```

- **Version branch:** When a version starts, create `release/<version>` from `main` (e.g., `release/v0.4`). This branch is the integration point for all features in the version.
- **Feature branch:** When grooming begins for a feature, create `feat/<version>/<feature-name>` from the version branch (e.g., `feat/v0.4/001-episode-list`). Grooming artifacts (research.md, spec.md, plan.md, tasks.md) are committed on this branch.
- **Ticket branch:** For each ticket, create `feat/<version>/<ticket>-<short-name>` from the feature branch (e.g., `feat/v0.4/T005-add-chat`).
- **Ticket merge flow:** After a ticket is done and confirmed, squash-merge its ticket branch back into the feature branch. Then create the next ticket branch from the updated feature branch.
- **Feature close flow:** After `/sdd:accept-feature` passes, `/sdd:close-feature` performs document sync and merges the feature branch back into the version branch (merge commit, not squash, to preserve ticket history).
- **Release flow:** After `/sdd:accept-version` passes, `/sdd:close-version` merges the version branch back into `main` (merge commit, to preserve feature history).

### Grooming Commit Rules

Grooming artifacts (research.md, spec.md, plan.md, tasks.md) must be committed on the feature branch:

- **Mandatory commit:** After all four files are complete and approved, commit them together. Commit message: `chore: grooming for <feature-name>`.
- **Optional mid-progress commits:** You may commit at any time to save progress (e.g., before closing a session). Commit message: `wip: grooming for <feature-name>`.
- **Late modifications:** If grooming files are modified after implementation has started (e.g., spec revision during a ticket), commit to the current branch (ticket branch or feature branch).

### Commit and Ticket Cadence

- **One ticket at a time.** Complete the full cycle (implement → commit → review → confirm → merge) before starting the next ticket.
- **One commit per ticket** (target). Commit message must include the ticket number: `feat(T005): add chat edge function`. If code review produces fixes, commit them separately during development, then squash into one commit before merge.
- **Stop after each ticket.** Do not auto-continue to the next ticket. Present the result, wait for user confirmation, merge, then tell the user the next ticket number and ask whether to continue.

### Implementation Rules

- Use TDD: write failing tests from acceptance criteria, then implement until tests pass.
- Execute tickets sequentially as ordered in tasks.md.
- If implementation reveals a situation the spec didn't anticipate → **pause, propose spec revision, wait for user confirmation before continuing**.

## 2.5 AI Code Review

AI code review is a **mandatory gate** in the ticket lifecycle, not an optional step. It happens after commit and before the ticket enters `for_review` (human review).

- Use a dedicated review agent (e.g., `code-reviewer`). If running in the same session, the agent must review against the spec, not from memory of implementation.
- Load only: original spec + code diff (`git diff <feature-branch>...<ticket-branch>`).
- Check strictly against acceptance criteria item by item.
- If issues are found: fix, commit the fix separately. Squash into the original commit when ticket is confirmed done and ready to merge.

## 2.6 Sync and Close

### Feature and Version Lifecycle States

Features and versions have persistent states tracked in `docs/VERSION_PLAN.md`. The file has two status tables that skills read and write:

```markdown
## Feature Status

| Feature | Status |
|---------|--------|
| v0.4/001-ai-qa | closed |
| v0.5/001-subtitle-export | in_progress |

## Version Status

| Version | Status |
|---------|--------|
| v0.4 | closed |
| v0.5 | in_progress |
```

| Feature Status | Meaning | Set By |
|----------------|---------|--------|
| `in_progress` (default) | Grooming or implementation underway | new-version (initial) |
| `accepted` | `/sdd:accept-feature` passed | accept-feature |
| `closed` | Document sync done, feature branch merged to version branch | close-feature |
| `abandoned` | No longer being pursued | abandon |

| Version Status | Meaning | Set By |
|----------------|---------|--------|
| `in_progress` (default) | Features still being delivered | new-version (initial) |
| `accepted` | `/sdd:accept-version` passed, all features closed | accept-version |
| `closed` | Version branch merged to main | close-version |

These persistent states let `/sdd:status` distinguish verified-but-not-closed from not-yet-verified, avoiding re-triggering verification.

### Sync Operations

`/sdd:close-feature` performs:
- Update spec (if revised during implementation).
- Update `SCHEMA.md` (if data model changed).
- Update `DECISIONS.md` (if new project-level architectural decisions were made).
- Update `README.md` (if setup-related changes were introduced — see §2.6.1).
- Update `VERSION_PLAN.md` — feature status: `accepted` → `closed`.
- Merge the feature branch back into the version branch.

`/sdd:close-version` performs:
- Final cross-feature SCHEMA.md / DECISIONS.md / VERSION_PLAN.md consistency check.
- Merge the version branch into `main`.
- Update `VERSION_PLAN.md` — version status: `accepted` → `closed`.

### 2.6.1 README Maintenance Rule

**`README.md` is the onboarding contract.** A new developer should be able to clone the repo and get a running environment by following README.md alone — no Slack messages, no tribal knowledge.

Any ticket that introduces setup-related changes **must** update README.md in the same ticket. Setup-related changes include:

| Change Type | README Section to Update |
|-------------|-------------------------|
| New environment variable or secret | "Environment setup" table — variable name, purpose, how to obtain, which file |
| New database or migration | "Database Setup" — connection steps, seed data, migration commands |
| New third-party service | "Third-Party Services" — what it is, how to get credentials, where to store them |
| New runtime, SDK, or tool dependency | "Prerequisites" — tool name, minimum version, install command |
| New build/run command | "Getting Started" — updated step-by-step instructions |
| New simulator, emulator, or device requirement | "Prerequisites" — setup instructions and download links |

**Sensitive credentials** should never be in README.md. Instead, document:
- What the credential is for
- Who to ask or where to find it (e.g., "ask team lead", "1Password vault: Project X", "Supabase Dashboard → Settings → API")
- Which file to put it in (e.g., `.env.local`)

## 2.7 Abandonment

When a feature is no longer being pursued, use `/sdd:abandon`:
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

## 2.9 Backlog

`docs/BACKLOG.md` is the upstream collection point for the SDD pipeline — items that haven't entered the formal spec process.

### Categories

| Category | Definition | Who Perceives the Problem | Example |
|----------|-----------|--------------------------|---------|
| Ideas | New functionality that may or may not be built | — | "A-B loop auto-suggest difficult sentences" |
| Bug | Something is broken or not working as designed; not urgent enough to fix immediately | User | "Offline mode shows stale episode count" |
| Polish | Small UX improvements; not doing them doesn't break core functionality | User | "Progress bar contrast too low in dark mode" |
| Tech Debt | Code/architecture issues; currently working but should be improved | Developer only | "Two services have duplicated retry logic" |

### Priority

Every item must have a priority tag:

| Priority | Tag | When to Pick Up |
|----------|-----|----------------|
| High | 🔴 high | Next gap between features |
| Mid | 🟡 mid | When there's time after a version |
| Low | 🟢 low | Someday, no active plan |

### Relationship to Pipeline

- Backlog items are NOT subject to the "spec before code" rule.
- An item enters the formal pipeline only when promoted (via `/sdd:backlog promote`).
- Promotion triggers `/sdd:groom` to create the feature spec.
