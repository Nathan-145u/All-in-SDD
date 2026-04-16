---
description: Add, view, promote, or drop items in docs/BACKLOG.md
argument-hint: '[add|promote|drop] [description]'
---

# /sdd:backlog

Manage the project backlog — a lightweight collection point for ideas,
bugs, polish items, and tech debt that haven't entered the SDD spec pipeline.

See `ref/pipeline.md` §2.9 for category and priority definitions.

## Commands

### View (no args)

Show the current backlog grouped by category.
Within each category, sort by priority: 🔴 high → 🟡 mid → 🟢 low.

### add <description>

1. Read `docs/BACKLOG.md`.
2. Discuss the item with the user to determine:
   - **Category**: Ideas / Bug / Polish / Tech Debt (see `ref/pipeline.md` §2.9 for definitions)
   - **Priority**: 🔴 high / 🟡 mid / 🟢 low
3. If both are obvious from context, confirm with the user and place directly.
   If ambiguous, ask one clarifying question.
4. Append the item under the correct category with priority tag and today's date.

Format: `- 🔴 high: description (2026-04-13)`

### promote <description>

1. Find the item by description keyword match.
2. Ask the user which version and feature name to use (e.g., `v0.5 001-feature-name`).
3. **Validate the target version exists** in the Version Status table of `docs/VERSION_PLAN.md`. If it does not exist, stop and tell the user: "Version `<version>` is not registered. Please run `/sdd:new-version <version>` first." Do not modify any files until the version exists.
4. Remove the item from BACKLOG.md.
5. Create the empty feature directory `specs/<version>/<feature-name>/` (if not already present).
6. **Register the feature in `docs/VERSION_PLAN.md`.** Add a new row to the Feature Status table with status `planned` (e.g., `| v0.5/001-feature-name | planned |`). If the Roadmap table has a row for this version, append `, <feature-name>` to that row's Features column (comma + space separator, matching the format in `commands/new-version.md`).
7. Tell the user: "Backlog item promoted to feature `<version>/<feature-name>`. What's next?
   - Start grooming this feature now (research + spec)
   - Promote another backlog item
   - Something else"

   Based on the user's choice:
   - **Start grooming** → Read and follow `skills/groom/SKILL.md` with the new feature's version and name.
   - **Promote another** → Return to the promote flow for the next item.
   - **Something else** → Follow the user's request.

### drop <description>

1. Find the item by description keyword match.
2. Remove the item from BACKLOG.md.

## Rules

- Items have no ID — matched by description content.
- Every item must have a priority tag (🔴 high / 🟡 mid / 🟢 low).
- Promoted and dropped items are deleted from the file.
- BACKLOG.md lives at `docs/BACKLOG.md`.
