# All-in-SDD — Codebase Management

## Plugin Change Workflow

When the user requests any change to this plugin, follow this workflow strictly:

### 1. Create version branch

```bash
git checkout main
git checkout -b release/v<new-version>
```

Bump version in both files:
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`

Version rules:
- **Patch** (0.2.0 → 0.2.1): bug fixes, typo corrections
- **Minor** (0.2.0 → 0.3.0): new features, new skills, behavior changes
- **Major** (0.x → 1.0): breaking changes, fundamental workflow redesign

### 2. Discuss and implement

- Discuss the request with the user. Ask questions, raise concerns, propose alternatives.
- Do not proceed until alignment is reached.
- Implement the agreed changes on the version branch.

### 3. Update documentation

After implementation, update these files as needed:
- **CHANGELOG.md** — Add entry under the new version with what changed
- **README.md** — Update if skills were added/removed/renamed, or if installation/usage steps changed

### 4. User confirmation

Present a summary of all changes to the user:
- Files modified and what changed
- Version number
- CHANGELOG entry
- README changes (if any)

**STOP.** Wait for explicit user confirmation before proceeding.

### 5. Commit and push

After user confirms:

```bash
git add <changed-files>
git commit -m "<type>: <description>"
git push -u origin release/v<version>
```

### 6. Merge to main

```bash
git checkout main
git merge release/v<version>
git push origin main
```

## File Structure

```
All-in-SDD/
├── .claude-plugin/        # Plugin and marketplace config (version lives here)
├── skills/                # All SDD skill definitions
│   ├── sdd/               # Core reference (pipeline, standards, structure, review)
│   └── sdd-*/             # Individual skill commands
├── CLAUDE.md              # This file — codebase management rules
├── CHANGELOG.md           # Version history
└── README.md              # Installation and usage guide
```

## Rules

- Never push directly to `main`. Always use a version branch.
- Every change set gets its own version bump — no batching multiple sessions into one version.
- CHANGELOG.md is mandatory for every version.
- README.md updates are mandatory when user-facing behavior changes.

## Writing Standards

- **Checklists over bullets.** Any item that can be verified (done/not done, pass/fail) must be written as a checklist (`- [ ]`), not a plain bullet (`-`). This applies to acceptance criteria, done definitions, verification steps, sync checks, and any other list where items are meant to be confirmed.
