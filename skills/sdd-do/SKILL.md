---
name: sdd-do
description: Execute the next (or a specified) ticket
user_invocable: true
arg_description: "[feature-path | ticket-number] — e.g., specs/v0.4/001-ai-qa or T005. Omit to auto-select the next executable ticket"
---

# /sdd-do

Execute a ticket and update its status.

## Parameters

- `feature-path` (optional): e.g., `specs/v0.4/001-ai-qa`. When specified, only select tickets within that feature.
- `ticket-number` (optional): e.g., `T005`. Directly specify the ticket to execute.
- Omit all parameters: scan all tasks.md files, auto-select the first ticket in `planned` status whose dependencies are all `done`.

**Priority:** specified ticket number > specified feature path > auto-select.

## Steps

1. **Locate the ticket.**
   - If a number was specified: search all tasks.md files under `specs/` for that ticket.
   - If a feature path was specified: find the first `planned` ticket with all dependencies `done` in that feature's tasks.md.
   - If nothing was specified: scan all tasks.md files, find the first `planned` ticket with all dependencies `done`.
   - If no executable ticket is found → inform the user that all tickets are complete or blocked.

2. **Load context.** Read spec.md and plan.md from the ticket's feature directory.

3. **Check isolation.** Create or switch to the ticket branch `feat/<version>/<ticket>-<short-name>` (e.g., `feat/v0.4/T005-add-chat`) from the feature branch `feat/<version>/<feature-name>`. If the feature branch does not exist, create it from the version branch `release/<version>`. If the version branch does not exist, create it from `main`.

4. **Check `[HUMAN REQUIRED]`.** If the ticket's "Manual Intervention" field contains `[HUMAN REQUIRED]`:
   - Change ticket status to `in_progress`
   - **Immediately notify the user**: list the specific steps the user must complete
   - Wait for user to confirm completion
   - Change status to `for_review`
   - Skip to step 8

5. **Change ticket status to `in_progress`.** Write to tasks.md.

6. **Execute implementation.**
   - Use TDD: write failing tests from acceptance criteria, then implement until tests pass.
   - After the ticket's own tests pass, run the full project test suite (unit + integration) to catch regressions. If any existing test fails, fix before proceeding.
   - Primarily modify files declared in the ticket. If changes to undeclared files are needed → pause, report to the user, request confirmation, then update the ticket's file list.

7. **Exception handling.**
   - If implementation reveals a situation the spec didn't anticipate → **pause**, revert ticket status to `planned`, report the issue to the user, suggest running `/sdd-propose` to revise the spec.
   - If spec-code drift is detected (implementation deviates from spec definition) → **immediately warn**, handle as an SDD violation.

8. **README sync check.** After implementation, check if the ticket introduced any setup-related changes. If YES, update the project's `README.md` accordingly. Setup-related changes include:
   - New environment variables or secrets → add to the "Environment setup" table with purpose, how to obtain, and which file
   - New database or database migrations → update "Database Setup" section with connection steps
   - New third-party service integrations → update "Third-Party Services" section with credential instructions
   - New runtime/tool dependencies (e.g., a new SDK, simulator, CLI tool) → update "Prerequisites" section
   - New build steps or run commands → update "Getting Started" section
   - Changes to ports, URLs, or connection strings → update relevant sections

   If README.md does not exist yet, create it following the template from `/sdd-new-proj`.

9. **Commit.** Create a commit on the ticket branch. Commit message must include the ticket number:
   ```
   feat(T005): add chat edge function
   ```

10. **AI code review.** Run a code review using a dedicated review agent (e.g., `code-reviewer`). The review must check:
    - Code diff against the spec's acceptance criteria
    - Code quality, security, and correctness
    - If issues are found → fix them, commit the fixes separately on the ticket branch. Do NOT squash yet.
    - If no issues → proceed.

11. **Present to user.** Change ticket status to `for_review`, present:
    - Change summary (which files were modified, key changes)
    - Code review results (passed, or issues found and fixed)
    - Whether the ticket's done definition is satisfied
    - **STOP here.** Wait for user confirmation. Do NOT auto-continue to the next ticket.

12. **After user confirms**, change status to `done`.

13. **Squash and merge.** If there are multiple commits on the ticket branch (implementation + review fixes), squash them into one commit. Merge the ticket branch back into the feature branch (`feat/<version>/<feature-name>`).

14. **Prompt next step:**
    - If more tickets remain → prompt `/sdd-do` to continue
    - If all tickets for this feature are done → prompt `/sdd-verify-feature <feature-path>` for verification
