# Workstream: Foundation (Database + Core Infrastructure)

**Priority**: P0 — Everything else depends on this

---

## The Job

Set up the foundational infrastructure that all other workstreams build upon:
1. libSQL database with schema
2. Project structure for the POS application
3. Core TypeScript/Node tooling
4. File system conventions

---

## Success Criteria

- [ ] libSQL database running locally with full schema
- [ ] Can insert/query all entity types (agents, tools, notes, tasks, events)
- [ ] Project scaffolded with TypeScript, build system working
- [ ] Can run `npm run dev` and see something
- [ ] Database viewable in Beekeeper Studio

---

## Tasks

### 1. Project Setup
- Create new repo for the actual POS application
- Initialize with TypeScript, Node.js
- Set up build tooling (Vite or similar for frontend)
- Configure ESLint, Prettier

### 2. libSQL Integration
- Install libSQL client (`@libsql/client`)
- Create database initialization script
- Implement full schema from spec
- Create seed data for testing
- Verify Beekeeper Studio can connect

### 3. Core Data Layer
- Create TypeScript types matching SQL schema
- Build basic CRUD operations for each entity
- Implement event emission pattern
- Add database migrations system

### 4. File System Conventions
```
pos/
├── packages/
│   ├── core/           # Shared types, database, utilities
│   ├── knowledge-ui/   # Human cognition (knowledge graph)
│   ├── review-ui/      # Human-AI handoff
│   ├── workbench/      # Agent observation
│   └── shell/          # Main app shell
├── data/
│   ├── pos.db          # libSQL database
│   └── notes/          # Markdown files
├── scripts/            # CLI tools, migrations
└── docs/               # Documentation
```

---

## Definition of Done

This workstream is complete when:
- A developer can clone the repo, run `npm install && npm run dev`
- The database schema exists and is queryable
- Basic TypeScript infrastructure is in place
- Other workstreams can start building on top

