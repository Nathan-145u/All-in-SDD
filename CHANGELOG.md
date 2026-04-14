# Changelog

All notable changes to this project will be documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/).

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
