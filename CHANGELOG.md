# Changelog

All notable changes to this project will be documented in this file.

Format follows [Keep a Changelog](https://keepachangelog.com/).

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
