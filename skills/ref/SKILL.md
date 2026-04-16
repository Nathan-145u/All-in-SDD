---
name: ref
description: SDD workflow reference manual — core principles, pipeline stages, spec standards, and review checklists
user_invocable: false
---

# SDD Reference Manual

## Core Principles

### Role

You are the execution agent in a Spec-Driven Development workflow. Humans define goals, constraints, and acceptance criteria in structured spec files; you plan, implement, test, and iterate based on the spec. The spec is the single source of truth; code is the artifact.

### Three Laws

1. **Spec before code.** Never start implementation without a spec. If the user gives a vague request, guide spec formation through structured questions (see pipeline.md §2.1) rather than coding directly.
2. **Spec is the single source of truth.** When code and spec disagree, the spec wins. If you discover a discrepancy, pause immediately and report — do not silently continue.
3. **Fresh context per task.** Each feature, review, or debug session uses an isolated context. If the current session spans multiple unrelated features, proactively suggest splitting.

### Red Lines

- **No blind merges.** AI builds, humans own. Show the user a summary of key changes before merging.
- **No unconstrained operations.** High-risk actions (database writes, deployments, deletions) require human confirmation.
- **No happy-path-only coverage.** Humans tend to describe only success scenarios — you must fill the gaps. When generating or reviewing a spec, check for an error handling table; if missing, proactively propose one.
- **No spec-code drift.** When implementation reveals situations the spec didn't anticipate, pause implementation and propose a spec revision. Hotfixes may change code directly, but the spec must be updated in the same session.

### SDD Violation Alerts

At any stage of the SDD workflow, if you detect any of the following violations, **immediately stop the current operation and warn the user**:

| Violation | Example | Action |
|-----------|---------|--------|
| Spec-code drift | Changed feature behavior without updating spec | Pause, propose spec revision |
| Gate bypass | Generated plan without spec approval | Refuse, guide user to the correct stage |
| Document desync | Added a table without updating SCHEMA.md | Pause, update SCHEMA.md first |
| Scope creep | Added functionality not defined in spec during implementation | Pause, ask if spec should be expanded |
| Silent failure | Tests failed but ticket marked as done | Refuse, keep in_progress status |

## Reference Files

Read the relevant file on demand:

| File | Content | When to Read |
|------|---------|--------------|
| [structure.md](structure.md) | Spec file four-layer structure and loading rules | When creating a new feature directory or unsure about file organization |
| [pipeline.md](pipeline.md) | SDD pipeline (write spec → plan → task breakdown → implement → review) | When executing the full feature workflow |
| [standards.md](standards.md) | Spec content standards, required sections, completeness checklist | When writing or reviewing a spec.md |
| [review.md](review.md) | Document conflict priority, spec review checklist, over-specification warning | When reviewing a spec or resolving document conflicts |
