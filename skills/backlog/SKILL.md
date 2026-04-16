---
name: backlog
description: Add, view, promote, or drop items in docs/BACKLOG.md
user_invocable: true
arg_description: "[add <description>] | [promote <description>] | [drop <description>] — omit args to view"
---

# /sdd:backlog

Manage the project backlog — a lightweight collection point for ideas,
bugs, polish items, and tech debt that haven't entered the SDD spec pipeline.

See ref/pipeline.md §2.9 for category and priority definitions.

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
3. Remove the item from BACKLOG.md.
4. Create the empty feature directory `specs/<version>/<feature-name>/` (if not already present).
5. Tell the user: "Backlog item promoted to feature `<version>/<feature-name>`. What's next?
   - Start grooming this feature now (research + spec)
   - Promote another backlog item
   - Something else"

   Based on the user's choice:
   - **Start grooming** → Read and follow `groom/SKILL.md` with the new feature's version and name.
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
