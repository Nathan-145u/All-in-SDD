# All-in-SDD

A Claude Code plugin for **Spec-Driven Development** — a structured workflow where specs are the single source of truth, and code is the artifact.

## What is SDD?

SDD enforces a disciplined pipeline: **write spec → plan → break into tasks → implement (TDD) → verify → close**. The spec is the contract; if code and spec disagree, the spec wins.

### Three Laws

1. **Spec before code.** Never start implementation without a spec.
2. **Spec is the single source of truth.** When code and spec disagree, the spec wins.
3. **Fresh context per task.** Each feature uses an isolated context.

## Included Skills

| Skill | Description |
|-------|-------------|
| `/sdd` | Workflow reference manual — core principles, pipeline, and red lines |
| `/sdd-new-proj` | Scaffold a new SDD project structure |
| `/sdd-new-version` | Create a new version directory and update the version plan |
| `/sdd-propose` | Create or revise a feature's research.md and spec.md |
| `/sdd-plan` | Generate technical plan (plan.md) and work packages (tasks.md) from a spec |
| `/sdd-do` | Execute the next (or a specified) ticket |
| `/sdd-status` | Show current SDD project progress overview |
| `/sdd-backlog` | Add, view, promote, or drop items in docs/BACKLOG.md |
| `/sdd-verify-feature` | Check feature completeness and verify against acceptance criteria |
| `/sdd-verify-version` | Check version completeness, run integration tests |
| `/sdd-close-feature` | Sync documents and formally close a completed feature |
| `/sdd-abandon-feature` | Abandon a feature, mark all incomplete tickets as abandoned |

## Typical Workflow

```
/sdd-new-proj          → bootstrap project structure
/sdd-new-version v0.1  → create version plan
/sdd-propose v0.1 001-feature-name
                       → research codebase, write spec
/sdd-plan specs/v0.1/001-feature-name
                       → generate plan.md + tasks.md
/sdd-do                → execute tickets one by one (TDD)
/sdd-verify-feature specs/v0.1/001-feature-name
                       → verify against acceptance criteria
/sdd-close-feature specs/v0.1/001-feature-name
                       → sync docs, close feature
/sdd-status            → check overall progress
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
├── skills/
│   ├── sdd/                    # Core reference (pipeline, standards, structure, review)
│   ├── sdd-new-proj/
│   ├── sdd-new-version/
│   ├── sdd-propose/
│   ├── sdd-plan/
│   ├── sdd-do/
│   ├── sdd-status/
│   ├── sdd-backlog/
│   ├── sdd-verify-feature/
│   ├── sdd-verify-version/
│   ├── sdd-close-feature/
│   └── sdd-abandon-feature/
└── README.md
```

## License

MIT
