---
description: Scaffold a new Spec-Driven Development project
argument-hint: '[project-name]'
---

# /sdd:init

Bootstrap a new SDD project structure.

## Steps

1. Determine the project name (from argument or current directory name).

2. Create the following file structure:

```
project-root/
├── README.md
├── CLAUDE.md
├── docs/
│   ├── CONSTITUTION.md
│   ├── DECISIONS.md
│   ├── SCHEMA.md
│   ├── DESIGN.md
│   ├── BACKLOG.md
│   ├── COUNTERS.md
│   └── VERSION_PLAN.md
└── specs/
```

3. Initial content for each file:

**CLAUDE.md (trampoline file):**
```markdown
See [docs/CONSTITUTION.md](docs/CONSTITUTION.md) for project constitution.
See [docs/DECISIONS.md](docs/DECISIONS.md) for architecture decisions.
```

**README.md:** Project onboarding guide. Generate with the following sections:
```markdown
# [Project Name]

Brief description of the project.

## Prerequisites

- [ ] List required runtimes and versions (e.g., Node.js >= 18, Python 3.11+)
- [ ] List required global tools (e.g., pnpm, Docker, specific CLI tools)

## Getting Started

### 1. Clone the repository
### 2. Install dependencies
### 3. Environment setup

List every environment variable needed, where to get the value, and where to put it:

| Variable | Purpose | How to Obtain | File |
|----------|---------|---------------|------|
| `DATABASE_URL` | Database connection | See [Database Setup](#database-setup) | `.env` |

### 4. Run the app

## Database Setup

(If applicable) Step-by-step instructions for setting up the database.

## Third-Party Services

(If applicable) For each external service:
- What it is and why we use it
- How to get credentials
- Where credentials are stored

## Troubleshooting

Common issues and how to resolve them.
```

Leave sections empty with `(Not yet configured)` if not yet known — they will be filled in as features are implemented.

**CONSTITUTION.md:** Ask the user to populate — project purpose, tech stack, global constraints, absolute red lines. Generate template, leave blanks where user decisions are needed.

**DECISIONS.md:** Empty template with ADR format instructions.

**SCHEMA.md:** Empty template with table format instructions. If the user already knows the data model, assist with filling it in.

**DESIGN.md:** Empty template with color/font/component format instructions.

**BACKLOG.md:** Empty template with four categories (Ideas, Bug, Polish, Tech Debt).

**COUNTERS.md:** Initialize with `T-number: 0`.

**VERSION_PLAN.md:** Start with an architecture overview and tech stack table from the user. Leave the three status-tracking sections (`## Roadmap`, `## Version Status`, `## Feature Status`) as empty tables — `/sdd:new-version` will populate them as versions and features are added. Example empty state:

```markdown
# Version Plan

## Roadmap

| Version | Theme | Features |
|---------|-------|----------|

## Version Status

| Version | Status |
|---------|--------|

## Feature Status

| Feature | Status |
|---------|--------|
```

4. After completion, show the file tree and tell the user: "Project scaffolding is complete. Next step is to create your first version (e.g., `v0.1`) and plan its initial features. What version number should we start with?"
