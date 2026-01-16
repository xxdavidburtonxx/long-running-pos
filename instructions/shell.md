# Workstream: Shell (Main Application)

**Priority**: P1 — Ties everything together

---

## The Job

Build the main application shell that:
- Hosts all UI panels (knowledge, review, workbench)
- Provides navigation between worlds (Tab to toggle)
- Integrates file browser (VSCode-like)
- Manages window/panel layout

---

## The Layout

```
┌─────────────────────────────────────────────────────────────────────┐
│ [🧠 Human] [🔄 Handoff] [🤖 Agent]              Tab to toggle       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────────────┬───────────────────────────────────────┐   │
│  │                     │                                       │   │
│  │   SIDEBAR           │          MAIN PANEL                   │   │
│  │                     │                                       │   │
│  │   Files / Notes     │   (Content changes based on mode)     │   │
│  │   Navigation        │                                       │   │
│  │   Quick actions     │   Human: Knowledge Graph Editor       │   │
│  │                     │   Handoff: Review Panel + Tasks       │   │
│  │                     │   Agent: Workbench + File Browser     │   │
│  │                     │                                       │   │
│  │                     │                                       │   │
│  └─────────────────────┴───────────────────────────────────────┘   │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│ Status: 2 agents running | 5 reviews pending | DB: 1.2MB          │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Success Criteria

- [ ] Single application window with all panels
- [ ] Tab key toggles between Human/Handoff/Agent modes
- [ ] Sidebar shows relevant navigation for current mode
- [ ] Status bar shows system state
- [ ] All workstream UIs integrated and functional
- [ ] Runs as desktop app (Electron or Tauri)

---

## Key Features

### Mode Switching
- **Tab key**: Cycles through Human → Handoff → Agent → Human
- **Cmd+1/2/3**: Jump directly to mode
- Visual indicator of current mode
- State preserved when switching

### Sidebar (Context-Aware)
| Mode | Sidebar Shows |
|------|---------------|
| Human | Note tree, recent notes, tags, graph mini-view |
| Handoff | Review queue, task list, pending items count |
| Agent | Agent list, active processes, quick controls |

### Main Panel
- Full-width content area
- Loads appropriate workstream UI
- Supports split views (future)

### Status Bar
- Agent count and status
- Pending reviews count
- Database size
- Sync status (if applicable)
- Quick actions

### Window Management
- Resizable panels
- Remember layout preferences
- Full screen support
- Multiple windows (future)

---

## Technical Approach

### Stack Options

**Option A: Electron**
- Pros: Mature, VSCode uses it, full Node.js access
- Cons: Heavy, memory usage

**Option B: Tauri**
- Pros: Lighter, Rust backend, better performance
- Cons: Less mature, smaller ecosystem

**Recommendation**: Start with Electron for faster development, consider Tauri later.

### Framework
- React for UI (consistent with other workstreams)
- Zustand for state management
- React Router for navigation
- Tailwind CSS for styling

### Mode State
```typescript
type Mode = 'human' | 'handoff' | 'agent';

interface AppState {
  currentMode: Mode;
  setMode: (mode: Mode) => void;
  cycleMode: () => void;
  
  // Each mode has its own preserved state
  humanState: KnowledgeGraphState;
  handoffState: ReviewState;
  agentState: WorkbenchState;
}

// Tab key handler
useHotkeys('tab', () => {
  const modes: Mode[] = ['human', 'handoff', 'agent'];
  const current = modes.indexOf(appState.currentMode);
  const next = modes[(current + 1) % modes.length];
  appState.setMode(next);
});
```

---

## Relationship to Other Workstreams

- **Foundation**: Uses database for all state
- **Knowledge Graph**: Embedded as Human mode panel
- **Review UI**: Embedded as Handoff mode panel
- **Workbench**: Embedded as Agent mode panel

---

## Definition of Done

This workstream is complete when:
- App launches as a desktop application
- Tab toggles between three modes seamlessly
- All workstream UIs are integrated
- Status bar shows live system state
- Layout is responsive and usable

