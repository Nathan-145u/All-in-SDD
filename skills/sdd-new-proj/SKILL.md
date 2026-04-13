---
name: sdd-new-proj
description: Scaffold a new Spec-Driven Development project
user_invocable: true
arg_description: "[project-name] — optional, defaults to current directory name"
---

# /sdd-new-proj

Bootstrap a new SDD project structure.

## Steps

1. Determine the project name (from argument or current directory name).

2. Create the following file structure:

```
project-root/
├── CLAUDE.md
├── docs/
│   ├── CONSTITUTION.md
│   ├── DECISIONS.md
│   ├── SCHEMA.md
│   ├── DESIGN.md
│   └── VERSION_PLAN.md
└── specs/
```

3. Initial content for each file:

**CLAUDE.md (trampoline file):**
```markdown
See [docs/CONSTITUTION.md](docs/CONSTITUTION.md) for project constitution.
See [docs/DECISIONS.md](docs/DECISIONS.md) for architecture decisions.
```

**CONSTITUTION.md:** Ask the user to populate — project purpose, tech stack, global constraints, absolute red lines. Generate template, leave blanks where user decisions are needed.

**DECISIONS.md:** Empty template with ADR format instructions.

**SCHEMA.md:** Empty template with table format instructions. If the user already knows the data model, assist with filling it in.

**DESIGN.md:** Empty template with color/font/component format instructions.

**VERSION_PLAN.md:** Ask the user — architecture overview, tech stack table, goals for the first version.

4. After completion, show the file tree and prompt the user for the next step: `/sdd-new-version <version>`.
