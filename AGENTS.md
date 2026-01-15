# Long-Running Agent Architecture

This repository defines the architecture for AI agents working on long-running tasks (days to weeks).

## The Three Phases of Work

Work follows this progression:

```
CLARIFYING → EXECUTION → LEARNINGS
   ↑                         |
   └─────────────────────────┘
```

### Phase 1: Clarifying (Human-led, AI-assisted)
- **Owner**: Human
- **AI Role**: Ask questions, synthesize, propose structures
- **Artifact**: Clear specification with measurable outcomes
- **Exit Criteria**: Human says "I have given you enough clarity to execute"

### Phase 2: Execution (AI-led, Human-supervised)
- **Owner**: AI Agent
- **Human Role**: Approve decisions, unblock, course-correct
- **Artifact**: Working code with regression tests
- **Exit Criteria**: All workstream success criteria met

### Phase 3: Learnings (Joint reflection)
- **Owner**: Both
- **Artifact**: Updated philosophy docs, new patterns, anti-patterns
- **Exit Criteria**: Learnings committed to repo for future sessions

## Current Project: Personal Operating System (POS)

**Status**: Phase 1 (Clarifying)

### Core Principle
> Work builds on itself over time.

### Components (Proposed)
1. **Database Layer**: libSQL + Beekeeper Studio
2. **Terminal/AI Layer**: opencode + VSCode fork
3. **Knowledge UI**: Logseq-like (TypeScript, not ClojureScript)
4. **Agent Task UI**: Locally-hosted human-in-the-loop interface

### Clarification Artifacts
- `clarifying/questions.md` — Questions asked and answered
- `clarifying/decisions.md` — Decisions made and rationale
- `clarifying/spec.md` — Emerging specification

### Workstreams (TBD after clarification)
Will be defined in `instructions/` once Phase 1 completes.

## Non-Negotiables (Inherited from FastRender patterns)

- **Measurable progress**: Every session produces committed artifacts
- **No vanity work**: Changes must move toward defined outcomes
- **Regression philosophy**: Fixes come with tests
- **Safety constraints**: Timeouts on all commands, scoped operations
- **Definition of "done"**: Must be observable and measurable

## Session Protocol

### Starting a session
1. Read this file and relevant `instructions/*.md`
2. Check `progress/` for current state
3. Identify the highest priority work
4. Execute until blocked or done

### Ending a session
1. Commit all progress artifacts
2. Update `progress/` with current state
3. Document blockers for next session
4. Push to remote

## Progress Tracking

```
progress/
├── sessions/           # Per-session logs
│   └── YYYY-MM-DD.md   # What was accomplished
├── decisions/          # Decision log with rationale
└── status.json         # Machine-readable current state
```

## Success Criteria (Meta)

This agent architecture is successful when:
- [ ] An AI agent can resume work after days/weeks with full context
- [ ] Progress is visible, measurable, and undeniable
- [ ] Learnings accumulate and improve future sessions
- [ ] Human time is spent on clarification, not babysitting execution

