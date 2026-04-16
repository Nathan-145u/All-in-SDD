# 4. Conflict Resolution & Spec Review

## 4.1 Document Conflict Priority

When multiple documents disagree:

`CONSTITUTION.md` > `SCHEMA.md` > `DECISIONS.md` > `spec.md` > `plan.md` > `tasks.md`

Two specs at the same level conflict → pause, report to the user, wait for a ruling.

## 4.2 Spec Review Checklist

- [ ] Can a brand-new AI session with no prior context implement the feature using only this spec?
- [ ] Are there any unstated implicit assumptions?
- [ ] Does the error handling table cover all realistic failure modes?
- [ ] Are the acceptance criteria truly binary (pass/fail)?
- [ ] Does it conflict with any existing spec or ADR?
- [ ] Are there any remaining `[NEED CLARIFICATION]` tags?

## 4.3 Over-Specification Warning

If the user requests a full spec for any of the following scenarios, proactively suggest simplification:
- Single-file, <50-line changes
- One-off scripts or internal tools
- Single-line bug fixes with obvious cause

Ceremony should match risk. The cost of the process should not exceed the cost of the task itself.
