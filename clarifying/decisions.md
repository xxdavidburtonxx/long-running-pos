# Decision Log

This document tracks all decisions made during clarification and execution, with rationale.

## Decision Format

```
## D[number]: [Decision Title]

**Date**: YYYY-MM-DD
**Phase**: Clarifying | Execution | Learnings
**Status**: Proposed | Accepted | Rejected | Superseded by D[x]

### Context
What led to this decision?

### Decision
What was decided?

### Rationale
Why this choice over alternatives?

### Implications
What does this mean for other decisions/work?

### Alternatives Considered
What else was considered and why not?
```

---

## Decisions

### D001: Use libSQL over standard SQLite

**Date**: 2026-01-15
**Phase**: Clarifying
**Status**: Accepted

#### Context
Need a database layer for structured knowledge storage.

#### Decision
Use [libSQL](https://github.com/tursodatabase/libsql) instead of standard SQLite.

#### Rationale
- Open source with open contributions (unlike SQLite)
- Embedded replicas for local-first with sync capability
- Active development by Turso
- Compatible with SQLite file format and API

#### Implications
- Need to learn libSQL-specific features
- Can use Beekeeper Studio for visualization
- Future sync/replication is possible

#### Alternatives Considered
- **Standard SQLite**: More stable, but closed contributions, no replication
- **PostgreSQL**: More powerful, but not embedded, requires server
- **DuckDB**: Good for analytics, but less mature ecosystem

---

### D002: Fork VSCode for UI layer

**Date**: 2026-01-15
**Phase**: Clarifying
**Status**: Proposed

#### Context
Need a way to view and edit files with full control over the experience.

#### Decision
Fork VSCode rather than use it as-is or build from scratch.

#### Rationale
- Full control over UI and behavior
- Massive existing ecosystem
- File editing is a solved problem
- Can integrate custom panels for POS-specific features

#### Implications
- Significant maintenance burden
- Need to track upstream changes
- Custom features won't work with extensions expecting vanilla VSCode

#### Alternatives Considered
- **Use VSCode as-is with extensions**: Less control, extension limitations
- **Build from scratch**: Too much work for file editing
- **Zed/Helix/other editors**: Smaller ecosystems, less extensible

---

### D003: TypeScript for Logseq-like component

**Date**: 2026-01-15
**Phase**: Clarifying
**Status**: Accepted

#### Context
Want a knowledge graph UI like Logseq but with different technology choices.

#### Decision
Build the knowledge UI in TypeScript, not ClojureScript (Logseq's language).

#### Rationale
- TypeScript has larger talent pool
- Better tooling and IDE support
- Easier integration with VSCode fork
- More familiar for most developers

#### Implications
- Can't directly port Logseq code
- Need to understand Logseq's concepts and reimplement
- May lose some of ClojureScript's immutability guarantees

#### Alternatives Considered
- **Use Logseq as-is**: Difficult to deeply integrate, ClojureScript barrier
- **Port Logseq to TypeScript**: Large effort, need to understand both languages
- **Build from scratch in TypeScript**: What we're doing

---

(More decisions will be added as clarification progresses)

