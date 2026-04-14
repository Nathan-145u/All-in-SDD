---
name: sdd-propose
description: Create or revise a feature's research.md and spec.md
user_invocable: true
arg_description: "<version> <feature-name> — e.g., v0.4 001-ai-qa"
---

# /sdd-propose

Create a new spec or revise an existing one. Automatically detects whether the feature already has files and routes to the appropriate mode.

## Parameters

- `version` (required): e.g., `v0.4`
- `feature-name` (required): e.g., `001-ai-qa`

## Mode Detection

Check if `specs/<version>/<feature-name>/spec.md` exists:
- **Does not exist** → New Spec mode (Phase 1–3)
- **Exists** → Revision mode (Phase 4)

---

## New Spec Mode

### Phase 0: Branch Setup

0. Ensure the feature branch exists. Create `feat/<version>/<feature-name>` from `release/<version>` (e.g., `feat/v0.1/001-episode-list`). If the version branch does not exist, create it from `main` first. Switch to the feature branch before proceeding.

### Phase 1: Research

1. Read Layer 1 documents: `CONSTITUTION.md`, `SCHEMA.md`, `DESIGN.md`, `VERSION_PLAN.md`.
2. Read all existing `spec.md` files under `specs/<version>/` to check for scope overlap or conflicts.
3. Scan the codebase, identify files, existing patterns, and potential conflicts relevant to this feature.
4. Generate `specs/<version>/<feature-name>/research.md`:

```markdown
# Research — [Feature Name]

## Relevant Files
- path/to/file — Why it's relevant to this feature

## Existing Patterns
- How similar functionality is implemented in the project

## Potential Conflicts
- What existing code this feature might conflict with

## Dependencies
- Schema/ADR/third-party APIs to understand first

## Technical Approach Options
- Option A: [description] — pros / cons
- Option B: [description] — pros / cons
- Recommended: [Option X], rationale: ...

## Risk Assessment
- Security: [Does it involve authentication, data access, user input?]
- Performance: [Impact on existing system performance]
- Compatibility: [Impact on existing features]
```

5. Present the research results to the user, confirm nothing is missing.

### Phase 2: Write the Spec

6. Generate `specs/<version>/<feature-name>/spec.md` template, pre-filling known information from research. Use `[NEED CLARIFICATION]` for content that cannot be determined independently:

```markdown
# Spec — [Feature Name]

## 1. Scope
**Included:**
- ...

**Excluded:**
- ...

## 2. Technical Context & Constraints
- Prerequisite features: [NEED CLARIFICATION] Does this depend on other features?
- ...

## 3. Functional Requirements
- ...

## 4. Error Handling Table
| Scenario | Behavior | Component |
|----------|----------|-----------|
| ... | ... | ... |

## 5. Acceptance Criteria
- [ ] ...
- [ ] ...

## 6. Critical Path
1. ...
```

7. Ask the user structured questions about `[NEED CLARIFICATION]` items and other blanks. Prioritize: scope boundaries, error scenarios, acceptance criteria.

8. After user input, perform **adversarial review**: analyze missing edge cases and security risks, add findings as constraints.

### Phase 3: Completeness Check

9. Verify against the `sdd/standards.md` §3.2 completeness checklist item by item.

10. If any items fail (including remaining `[NEED CLARIFICATION]` tags), point them out to the user and assist with completion.

11. When all items pass, request user's final approval.

12. **Commit grooming artifacts.** Commit research.md and spec.md on the feature branch. If this is the first grooming commit, use `chore: grooming for <feature-name>`. If previous grooming commits exist (e.g., from mid-progress saves), this commit updates them.

13. After completion, prompt the next step: `/sdd-plan <version> <feature-name>` to enter the planning stage.

---

## Revision Mode

When spec.md already exists, enter revision mode.

### Phase 4: Revise

1. Read all files in the feature directory (research.md, spec.md, and plan.md/tasks.md if they exist).

2. Present the current status to the user:
   - research.md: exists / does not exist
   - spec.md: completeness check results (including `[NEED CLARIFICATION]` count)
   - plan.md: exists / does not exist
   - tasks.md: exists / does not exist; if exists, show ticket status summary

3. Ask the user what they want to do:
   - Add to / modify research
   - Modify a specific section of the spec
   - Add missing error handling
   - Modify acceptance criteria
   - **Run a full review** (check against `sdd/review.md` §4.2 review checklist item by item)
   - Other

4. Execute the modification or review.

5. If the spec underwent substantial changes and plan.md/tasks.md already exist:
   - Warn the user: spec changes may require regenerating plan and tasks.
   - Ask if they want to run `/sdd-plan` to re-plan.

6. After modification, re-run the completeness checklist and present results (including whether `[NEED CLARIFICATION]` tags remain).
