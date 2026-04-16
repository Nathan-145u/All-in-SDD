# Changelog

All notable changes to this project will be documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/).

## [0.6.0] - 2026-04-16

### Added
- **`/sdd:close-version` skill**: Separates version close (merge `release/<version>` → `main` + document sync) from version verification. Previously `sdd-verify-version` tried to do both.
- **Feature/version lifecycle states** in `docs/VERSION_PLAN.md`: `in_progress` → `accepted` → `closed`. New-version creates `## Feature Status` and `## Version Status` tables; `accept-feature` / `accept-version` write the `accepted` marker; `close-feature` / `close-version` write `closed`; `abandon` writes `abandoned`. This lets `status` distinguish verified-but-not-closed from not-yet-verified (prevents status loop from re-triggering verification).
- **Feature branch merge step** in `close-feature`: explicitly merges `feat/<version>/<feature-name>` into `release/<version>` (merge commit, preserving ticket history). Previously this merge was described in pipeline.md but never implemented.

### Changed
- **Status-driven workflow**: `/sdd:status` is now the primary entry point. It shows progress, determines the next action, and auto-continues when the user confirms. No need to remember individual command names.
- **Internal skills**: `sdd:ref`, `sdd:groom`, `sdd:plan`, `sdd:exec`, `sdd:accept-feature`, `sdd:close-feature`, `sdd:accept-version`, and `sdd:close-version` are now internal modules (`user_invocable: false`). They are auto-triggered by `/sdd:status` based on project state.
- **User-facing skills reduced to 5**: `/sdd:status`, `/sdd:init`, `/sdd:new-version`, `/sdd:backlog`, `/sdd:abandon`.
- **Natural-language next-step prompts**: Internal skills now tell users what the next step is (e.g., "Next step is to generate the plan. Continue?") instead of exposing internal command names. Flow control is centralized in `/sdd:status`.
- **New status markers**: Added `[ACCEPTED]`, `[DONE]`, `[PLAN]`, `[RESEARCH]`, `[CLOSED]` markers for finer-grained progress tracking.
- **`backlog promote`** now offers to either start grooming the promoted feature immediately or continue promoting other backlog items in a batch.

### Renamed
- `/sdd-new-proj` → `/sdd:init`
- `/sdd-propose` → `sdd:groom` (now internal)
- `/sdd-do` → `sdd:exec` (now internal)
- `/sdd-verify-feature` → `sdd:accept-feature` (now internal)
- `/sdd-verify-version` → `sdd:accept-version` (now internal)
- `/sdd-close-feature` → `sdd:close-feature` (now internal)
- `/sdd-abandon-feature` → `/sdd:abandon`
- `/sdd-plan` → `sdd:plan` (now internal)
- `/sdd` → `sdd:ref` (now internal reference manual)
- All remaining user commands moved to the `sdd:` namespace (`/sdd:status`, `/sdd:new-version`, `/sdd:backlog`).

### Fixed
- `accept-feature`: Fixed duplicate step-3 numbering. Status update is now conditional — only promotes `in_progress` → `accepted`, never regresses `closed` back to `accepted` during `accept-version` re-verification.
- `accept-version`: Moved the "all features closed" gate check to the front of the workflow so it fails fast instead of running the full per-feature verification first. The gate now accepts both `closed` and `abandoned` features.
- `status`: Scanning order explicitly documented (version ascending, then feature number ascending; abandoned features skipped). "Verify version" rule now triggers when all features are `closed` **or** `abandoned`.
- `plan`: Grooming commit no longer offers an amend option — always creates a new commit to avoid rewriting pushed history.
- `groom`: Revision Mode explicitly does not commit — revised grooming files travel with the user's ongoing work (usually absorbed into the next ticket commit).
- Ref-file path references: all skills now use the `ref/` prefix consistently (was mixed `pipeline.md` / `ref/pipeline.md`).
- `ref`: Added missing `user_invocable: false` marker — was documented as internal but not declared in frontmatter, so it still showed up as a user command.
- `plan`: Reworded the spec-incomplete gate failure message. Previous wording ended with "Continue?" which ambiguously suggested continuing to plan with a broken spec; new wording makes clear that planning is blocked and the next action is to revise the spec.

## [0.5.0] - 2026-04-14

### Changed
- **Branch strategy**: Grooming artifacts (research/spec/plan/tasks) are now committed on a feature branch (`feat/<version>/<feature-name>`), not the version branch. Ticket branches are created from the feature branch and merge back to it. Feature branches merge back to the version branch on completion.
- **Grooming commit rules**: Mandatory commit after all four grooming files are approved. Optional mid-progress commits allowed. Late modifications commit to the current branch.
- **Code review gate**: Code review is now a mandatory step in the ticket lifecycle. Flow: implement → commit → AI code review → fix → user confirm → squash → merge. Previously review was an optional standalone step that was often skipped.
- **`/sdd-propose`**: Now creates the feature branch at the start of grooming (Phase 0). Commits research.md + spec.md on completion.
- **`/sdd-plan`**: Commits plan.md + tasks.md on the feature branch on completion.
- **`/sdd-do`**: Ticket branches now branch from and merge back to the feature branch (not version branch). Added code review step (step 10) between commit and user confirmation. Squash-and-merge enforced at merge time.

## [0.4.0] - 2026-04-13

### Added
- **Full test suite in `/sdd-do`**: After a ticket's own tests pass, run the full project test suite (unit + integration) to catch regressions before proceeding
- **Checklist writing standard** in CLAUDE.md: All verifiable items must use `- [ ]` checklist format, not plain bullets

## [0.3.0] - 2026-04-13

### Added
- **CLAUDE.md**: Codebase management rules — defines the version branch workflow for all future plugin changes
- Enforces: version branch → discuss → implement → update docs → confirm → commit → merge to main

## [0.2.0] - 2026-04-13

### Added
- **README onboarding enforcement**: `/sdd-new-proj` now scaffolds a project README with setup sections (Prerequisites, Environment setup, Database Setup, Third-Party Services, Troubleshooting)
- **README sync in pipeline**: `/sdd-do` checks for setup-related changes after each ticket and updates README accordingly
- **README verification**: `/sdd-verify-feature` and `/sdd-close-feature` verify README completeness before closing
- **README maintenance rule** (pipeline.md §2.6.1): defines which changes require README updates and how to handle sensitive credentials
- **Version/ticket branch strategy**: `release/<version>` as integration branch, `feat/<version>/<ticket>-<name>` per ticket
- **One-ticket-at-a-time cadence**: `/sdd-do` now commits per ticket, stops for user confirmation, and merges back to version branch before continuing
- `/sdd-new-version` automatically creates the `release/<version>` branch
- `/sdd-verify-version` prompts merge of version branch back to main

## [0.1.0] - 2026-04-12

### Added
- Initial release with core SDD skills: `/sdd`, `/sdd-new-proj`, `/sdd-new-version`, `/sdd-propose`, `/sdd-plan`, `/sdd-do`, `/sdd-status`, `/sdd-backlog`, `/sdd-verify-feature`, `/sdd-verify-version`, `/sdd-close-feature`, `/sdd-abandon-feature`
- SDD reference manual (pipeline, standards, structure, review)
- Plugin marketplace configuration
