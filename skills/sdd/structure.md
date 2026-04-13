# 1. Spec File Structure

The project's specs follow a four-layer structure. You need to know each layer's responsibility and when to read it.

```
project-root/
├── CLAUDE.md                          # Trampoline file (references CONSTITUTION.md)
├── docs/
│   ├── CONSTITUTION.md                # Layer 1: Project constitution (global constraints + principles)
│   ├── DECISIONS.md                   # Layer 1: Architecture Decision Records + deviation log
│   ├── SCHEMA.md                      # Layer 1: Database schema (SSOT)
│   ├── DESIGN.md                      # Layer 1: Design language
│   └── VERSION_PLAN.md                # Layer 4: Roadmap and version overview
├── specs/                             # Layers 2+3: Organized by version/feature
│   ├── v0.1/
│   │   └── 001-rss-playback/
│   │       ├── research.md            # Research artifact (generated during spec writing)
│   │       ├── spec.md                # WHAT: Requirements + acceptance criteria
│   │       ├── plan.md                # HOW: Technical approach
│   │       └── tasks.md               # EXECUTION: Work packages + status tracking
│   ├── v0.2/
│   │   ├── 001-subtitle-display/
│   │   └── 002-transcription-admin/
│   └── v0.4/
│       ├── 001-ai-qa/
│       ├── 002-batch-download/
│       └── 003-ipad-mac-layout/
└── .claude/rules/                     # Layer 1: AI behavior rules
```

## Loading Rules

| Layer | File | When to Read |
|-------|------|--------------|
| Layer 1 Constitution | `CONSTITUTION.md` | Auto-loaded each session (via `CLAUDE.md` reference) |
| Layer 1 Reference | `SCHEMA.md`, `DESIGN.md`, `DECISIONS.md` | On demand when data model / UI / architecture decisions are involved |
| Layer 2 Feature Spec | `specs/v0.x/xxx/spec.md` | When implementing that feature |
| Layer 2 Technical Plan | `specs/v0.x/xxx/plan.md` | When understanding the technical approach |
| Layer 3 Execution Tasks | `specs/v0.x/xxx/tasks.md` | When executing specific work packages |
| Layer 4 Roadmap | `VERSION_PLAN.md` | When understanding long-term direction or judging if something is over-engineered |
