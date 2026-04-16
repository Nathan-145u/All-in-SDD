# 3. Spec Content Standards

When generating or reviewing a spec, follow these standards.

## 3.1 Required Sections in spec.md

| Section | Content |
|---------|---------|
| Scope | What's included, what's explicitly excluded |
| Technical Context & Constraints | Tech stack, file boundaries, Schema references, hard limits, prerequisite feature dependencies |
| Functional Requirements | User stories + input/output signatures |
| Error Handling Table | Each failure scenario + expected behavior |
| Acceptance Criteria | Checklist format (`- [ ]`), each item independently pass/fail |
| Critical Path | Testable E2E user journey |

These six sections ensure the spec covers three content layers: technical context, functional requirements, and edge cases/errors. Missing any layer means the spec is incomplete.

## 3.2 Spec Completeness Checklist

All items must pass before submitting to the user for approval:

- [ ] Scope defines what's included and what's excluded
- [ ] Data model changes reference `SCHEMA.md` (if applicable)
- [ ] API endpoints have request/response format + error codes (if applicable)
- [ ] UI screens have layout, states, and transition definitions (if applicable)
- [ ] Error handling table covers all failure scenarios
- [ ] Acceptance criteria use checklist format (`- [ ]`), each item independently pass/fail
- [ ] Critical path defined as a testable E2E journey
- [ ] No `[NEED CLARIFICATION]` tags remaining

## 3.3 Standard Annotations

| Tag | Meaning | Handling |
|-----|---------|----------|
| `[NEED CLARIFICATION]` | The spec contains content that cannot be determined independently and requires user decision | Ask the user structured questions, replace with actual content after receiving answers. A spec with this tag **cannot pass the completeness checklist** and is blocked from proceeding to the next stage |
| `[HUMAN REQUIRED]` | Marks a ticket in tasks.md that requires manual intervention | When execution reaches this ticket, immediately notify the user, list specific steps, and wait for user completion |

## 3.4 Prohibited Content

| Anti-pattern | Your response |
|--------------|--------------|
| Vague adjectives ("efficient", "modern") | Ask the user for measurable criteria |
| Implementation micromanagement ("use a for loop") | Ignore, choose implementation approach autonomously based on spec goals and constraints |
| Multiple unrelated features in one spec | Suggest the user split into separate specs |
| Duplicated information (schema written in both spec and SCHEMA.md) | Remove duplication, keep reference to the SSOT |

## 3.5 Ticket Numbering

- Ticket numbers are **immutable** once assigned. Never renumber, reuse, or fill gaps.
- If a ticket is deleted, its number is retired permanently.
- New tickets always receive the next available number (global max + 1).
- This ensures stable references across conversations, commits, and PRs.
