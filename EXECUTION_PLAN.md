# Execution Plan: Personal Operating System (POS)

## Overview

This document defines the execution plan for building POS over ~1 week.

**Phase**: Transitioning from Clarifying → Execution

---

## Workstreams

| # | Workstream | Priority | Dependencies | Est. Time |
|---|------------|----------|--------------|-----------|
| 1 | Foundation | P0 | None | 1 day |
| 2 | Shell | P1 | Foundation | 1 day |
| 3 | Knowledge Graph | P1 | Foundation, Shell | 2 days |
| 4 | Review UI | P1 | Foundation, Shell | 1 day |
| 5 | Workbench | P2 | Foundation, Shell | 1 day |

**Total**: ~6 days of focused execution

---

## Dependency Graph

```
                    ┌──────────────┐
                    │  Foundation  │
                    │     (P0)     │
                    └──────┬───────┘
                           │
                    ┌──────▼───────┐
                    │    Shell     │
                    │     (P1)     │
                    └──────┬───────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
   ┌──────▼──────┐  ┌──────▼──────┐  ┌──────▼──────┐
   │  Knowledge  │  │  Review UI  │  │  Workbench  │
   │    Graph    │  │    (P1)     │  │    (P2)     │
   │    (P1)     │  └─────────────┘  └─────────────┘
   └─────────────┘
```

---

## Day-by-Day Plan

### Day 1: Foundation
**Goal**: Database + project scaffolding

- [ ] Create new repo: `pos` (the actual application)
- [ ] Initialize TypeScript/Node project
- [ ] Set up Electron shell (minimal)
- [ ] Install and configure libSQL
- [ ] Create full database schema
- [ ] Seed with test data
- [ ] Verify Beekeeper Studio connection
- [ ] Basic CRUD operations working

**Exit Criteria**: `npm run dev` works, database queryable

### Day 2: Shell
**Goal**: Main application structure with mode switching

- [ ] Electron app with window management
- [ ] Three-mode layout (Human/Handoff/Agent)
- [ ] Tab key switching works
- [ ] Sidebar (placeholder content)
- [ ] Status bar (placeholder data)
- [ ] Navigation between modes
- [ ] Panel system for embedding workstreams

**Exit Criteria**: Can press Tab to switch modes, see different placeholders

### Day 3-4: Knowledge Graph
**Goal**: Obsidian-like note editing with backlinks

- [ ] Markdown editor integration (TipTap or CodeMirror)
- [ ] File tree showing notes directory
- [ ] Create/edit/delete notes
- [ ] `[[backlink]]` syntax parsing
- [ ] Clickable backlinks
- [ ] Backlinks panel (what links here)
- [ ] Basic search
- [ ] Sync to database (notes table)
- [ ] Graph visualization (basic)

**Exit Criteria**: Can create notes with working backlinks, see graph

### Day 5: Review UI
**Goal**: Batch review interface for agent decisions

- [ ] Review queue showing pending items
- [ ] Checkbox list interface
- [ ] Agent reasoning display
- [ ] Select/deselect individual items
- [ ] Batch approve/reject
- [ ] Log decisions to review_events
- [ ] Mock agent data for testing

**Exit Criteria**: Can review and approve mock agent decisions

### Day 6: Workbench
**Goal**: Agent observation and control

- [ ] Agent status list
- [ ] Progress indicators
- [ ] Activity feed (from agent_events)
- [ ] Pause/resume controls (UI only, agent integration later)
- [ ] Basic trace viewer
- [ ] Learnings display (placeholder)

**Exit Criteria**: Can see agent status and activity

### Day 7: Integration & Polish
**Goal**: Everything working together

- [ ] All workstreams integrated in shell
- [ ] Mode switching with real content
- [ ] Cross-workstream navigation (note → task → review)
- [ ] Bug fixes and polish
- [ ] Documentation
- [ ] Demo walkthrough

**Exit Criteria**: Full demo of human → agent → review workflow

---

## Technical Decisions

### Stack
- **Runtime**: Node.js + Electron
- **Language**: TypeScript
- **UI Framework**: React
- **State**: Zustand
- **Styling**: Tailwind CSS
- **Database**: libSQL (@libsql/client)
- **Editor**: TipTap or CodeMirror 6
- **Graph**: D3.js or Cytoscape

### Project Structure
```
pos/
├── package.json
├── tsconfig.json
├── electron/
│   ├── main.ts          # Electron main process
│   └── preload.ts       # Preload script
├── src/
│   ├── main.tsx         # React entry
│   ├── App.tsx          # Root component
│   ├── core/
│   │   ├── database.ts  # libSQL client
│   │   ├── types.ts     # Shared types
│   │   └── schema.sql   # Database schema
│   ├── components/
│   │   ├── Shell/       # Main app shell
│   │   ├── Sidebar/     # Context-aware sidebar
│   │   └── StatusBar/   # Bottom status
│   ├── features/
│   │   ├── knowledge/   # Knowledge graph workstream
│   │   ├── review/      # Review UI workstream
│   │   └── workbench/   # Workbench workstream
│   └── hooks/
│       └── useDatabase.ts
├── data/
│   ├── pos.db           # libSQL database
│   └── notes/           # Markdown files
└── scripts/
    ├── init-db.ts       # Database initialization
    └── seed.ts          # Seed data
```

---

## Success Criteria (End of Week)

### Must Have
- [ ] App runs on desktop (Electron)
- [ ] Can create/edit notes with backlinks
- [ ] Can see/approve review requests
- [ ] Can see agent status
- [ ] Tab toggles between modes
- [ ] Data persists in libSQL

### Nice to Have
- [ ] Graph visualization
- [ ] Import existing Obsidian vault
- [ ] Real agent integration
- [ ] Keyboard shortcuts throughout
- [ ] Beautiful UI

### Future (v2)
- [ ] AI-suggested connections
- [ ] Real agent execution
- [ ] Task management (Plane integration)
- [ ] File browser (VSCode-like)
- [ ] Mobile companion

---

## How to Start

1. **Confirm this plan looks right** — Any changes before we begin?
2. **I'll create the `pos` repo** — Fresh start for the actual application
3. **Begin Day 1: Foundation** — Database and scaffolding

---

## Communication During Execution

- **Daily**: I'll commit progress and update `progress/sessions/`
- **Blockers**: I'll ask specific questions when stuck
- **Reviews**: I'll request review at end of each day/milestone
- **Async**: You don't need to watch — check in when convenient

---

Ready to begin? Say "go" and I'll start Day 1.

