# Changelog

All notable changes to this project will be documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/).

## [0.8.0] - 2026-04-16

### Added
- **`planned` feature lifecycle state.** New features registered in `docs/VERSION_PLAN.md` now start as `planned`, not `in_progress`. The state flips to `in_progress` only when `skills/groom/SKILL.md` writes the first `research.md`. Rationale: if a feature is just a directory with no work done, calling it "in progress" is misleading — `/sdd:status` now correctly distinguishes "registered but not started" from "actually being worked on".
- **New state transition:** `planned → in_progress → accepted → closed` (plus `abandoned` from any state).
- **`[PLANNED]` marker** in `/sdd:status` output (📭).

### Changed
- `commands/new-version.md` now creates features with status `planned`.
- `skills/groom/SKILL.md` now flips `planned → in_progress` when writing `research.md` (step 6). If the feature row is missing from VERSION_PLAN.md, groom creates it.
- `ref/pipeline.md` §2.6 Feature Status table updated with the new `planned` row.
- `/sdd:status` next-action table distinguishes `planned` features from legacy empty directories.
- `skills/{accept-version, close-version}/SKILL.md` gate checks extended to also block when features are `planned` (previously only blocked `in_progress` and `accepted`).
- `skills/accept-feature/SKILL.md` status update rule extended to handle `planned → accepted` (rare edge case, included for consistency).

### Fixed
- **`/sdd:backlog promote` now registers the promoted feature in `docs/VERSION_PLAN.md`.** Previously it only created the empty feature directory — the Feature Status table was left unchanged, so `/sdd:status` could not see the new feature. Promote now adds a `planned` row and appends the feature name to the Roadmap row for that version.
- **`/sdd:backlog promote` validates the target version before mutating any state.** Previously the empty feature directory was created before checking whether the version existed in Version Status, leaving a half-state on disk if the version was missing. The check now runs first.
- **`/sdd:status` next-action for legacy empty directories now backfills the missing `planned` status row** before grooming, instead of silently leaving the inconsistency.

## [0.7.0] - 2026-04-16

### Changed
- **User-facing commands moved to `commands/` directory.** Previously all 13 skills lived under `skills/`, which caused two problems: (1) the palette showed bare names like `/status` instead of `/sdd:status` — Claude Code only renders the plugin namespace prefix for items under `commands/`, not `skills/`; (2) internal skills were still appearing in the palette because the frontmatter field to hide them was named incorrectly (see Fixed below). Moved the 5 user-facing entry points (`status`, `init`, `new-version`, `backlog`, `abandon`) from `skills/<name>/SKILL.md` to `commands/<name>.md`. The 7 internal executable skills (`groom`, `plan`, `exec`, `accept-feature`, `accept-version`, `close-feature`, `close-version`) remain in `skills/` and are loaded on demand by `commands/status.md`.
- Command frontmatter format: commands use `description` + `argument-hint` (no `name` field — the filename is the name; no `user-invocable` field — commands are always user-invokable).
- **`ref/` promoted to top-level reference library.** Previously `skills/ref/` held reference docs (pipeline, standards, structure, review) wrapped in a fake skill (only `SKILL.md` was an empty index, the real content was in sibling markdown files). Moved to root `ref/`, renamed `SKILL.md` → `README.md`, stripped skill frontmatter. Rationale: `ref/` has no executable logic, so presenting it as a skill was misleading; separating docs from executable skills clarifies the plugin's architecture (`commands/` = user entry, `skills/` = executable stages, `ref/` = documentation).

### Fixed
- **Internal skills were appearing in the `/` command palette.** All skills were using frontmatter field `user_invocable` (underscore) instead of the Claude Code plugin spec field `user-invocable` (hyphen). The field was silently ignored. After the commands/skills split above, this is no longer the primary gate — the palette only lists files under `commands/` — but the field was renamed in all remaining skill files for correctness.

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
