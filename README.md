# All-in-SDD

A Claude Code plugin for **Spec-Driven Development** — a structured workflow where specs are the single source of truth, and code is the artifact.

## What is SDD?

SDD enforces a disciplined pipeline: **write spec → plan → break into tasks → implement (TDD) → accept → close**. The spec is the contract; if code and spec disagree, the spec wins.

### Three Laws

1. **Spec before code.** Never start implementation without a spec.
2. **Spec is the single source of truth.** When code and spec disagree, the spec wins.
3. **Fresh context per task.** Each feature uses an isolated context.

## Skills

### User Commands

| Skill | Description |
|-------|-------------|
| `/sdd:status` | **Primary entry point.** Shows project progress, suggests the next action, and auto-continues the workflow when you confirm |
| `/sdd:init` | Scaffold a new SDD project structure |
| `/sdd:new-version` | Create a new version directory and update the version plan |
| `/sdd:backlog` | Add, view, promote, or drop items in docs/BACKLOG.md |
| `/sdd:abandon` | Abandon a feature, mark all incomplete tickets as abandoned |

### Internal Skills (auto-triggered by `/sdd:status`)

| Skill | Description |
|-------|-------------|
| `sdd:groom` | Create or revise a feature's research.md and spec.md |
| `sdd:plan` | Generate technical plan (plan.md) and work packages (tasks.md) from a spec |
| `sdd:exec` | Execute the next (or a specified) ticket |
| `sdd:accept-feature` | Check feature completeness and verify against acceptance criteria |
| `sdd:close-feature` | Sync documents, merge feature branch into version branch, formally close the feature |
| `sdd:accept-version` | Check version completeness, run cross-feature integration tests |
| `sdd:close-version` | Final sync, merge version branch into main, formally close the version |

### Typical Workflow

```
/sdd:init              → bootstrap project structure
/sdd:new-version v0.1  → create version plan
/sdd:status            → shows progress, suggests next action
  "next"               → auto-runs groom (research + spec)
  "next"               → auto-runs plan
  "next"               → auto-runs exec (one ticket at a time)
  ... repeat until all tickets in feature done ...
  "next"               → auto-runs accept-feature
  "next"               → auto-runs close-feature (merges feature → version branch)
  ... repeat for remaining features in the version ...
  "next"               → auto-runs accept-version
  "next"               → auto-runs close-version (merges version → main)
```

## Installation

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed

### Step 1: Add marketplace

```bash
claude plugin marketplace add Nathan-145u/All-in-SDD
```

### Step 2: Install plugin

```bash
claude plugin install sdd@claude-sdd
```

### Step 3: Restart Claude Code

Restart your Claude Code session for the skills to take effect.

### Step 4: Clean up old local skills (if applicable)

If you previously had SDD skills installed locally in `~/.claude/skills/`, remove them to avoid duplicates:

```bash
rm -rf ~/.claude/skills/sdd ~/.claude/skills/sdd-*/
```

## Updating

After the plugin is updated on GitHub:

```bash
# Pull latest marketplace index
claude plugin marketplace update claude-sdd

# Update the plugin
claude plugin update sdd@claude-sdd

# Restart Claude Code to apply
```

## Uninstalling

```bash
# Remove the plugin
claude plugin uninstall sdd@claude-sdd

# Optionally remove the marketplace
claude plugin marketplace remove claude-sdd
```

## Local Development

If you want to test changes before pushing:

```bash
# Load plugin directly from local directory
claude --plugin-dir ~/path/to/All-in-SDD
```

Changes to skill files take effect on the next Claude Code restart. No need to go through the marketplace update flow during development.

## Plugin Structure

```
All-in-SDD/
├── .claude-plugin/
│   ├── marketplace.json
│   └── plugin.json
├── commands/                   # User-facing slash commands (shown as /sdd:<name>)
│   ├── status.md
│   ├── init.md
│   ├── new-version.md
│   ├── backlog.md
│   └── abandon.md
├── skills/                     # Internal executable skills (auto-loaded by status.md)
│   ├── groom/
│   ├── plan/
│   ├── exec/
│   ├── accept-feature/
│   ├── close-feature/
│   ├── accept-version/
│   └── close-version/
├── ref/                        # Reference library (pipeline, standards, structure, review)
└── README.md
```

## License

MIT
