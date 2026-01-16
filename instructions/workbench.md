# Workstream: Workbench (Agent Observation & Control)

**Priority**: P2 — Important for long-running agents

---

## The Job

Build instruments to observe and control long-running agents:
- See what agents are doing
- Understand progress and status
- Pause, resume, redirect agents
- Extract learnings from executions

---

## The Interface

```
┌────────────────────────────────────────────────────────────────┐
│ WORKBENCH                                                      │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│ ACTIVE AGENTS                                                  │
│ ┌────────────────────────────────────────────────────────────┐ │
│ │ 🟢 Sales Pipeline Agent                    [Pause] [Stop]  │ │
│ │    Task: Find decision makers for 50 companies             │ │
│ │    Progress: 32/50 companies (64%)                         │ │
│ │    Runtime: 2h 14m | Tokens: 45,230 | API calls: 128       │ │
│ │    Last action: Researching "Acme Corp" contacts           │ │
│ └────────────────────────────────────────────────────────────┘ │
│                                                                │
│ ┌────────────────────────────────────────────────────────────┐ │
│ │ 🟡 Content Writer Agent                    [Resume] [Stop] │ │
│ │    Task: Write blog posts from notes                       │ │
│ │    Status: PAUSED - Waiting for human review               │ │
│ │    Progress: 3/10 posts                                    │ │
│ └────────────────────────────────────────────────────────────┘ │
│                                                                │
│ RECENT ACTIVITY                               [View All →]     │
│ ───────────────────────────────────────────────────────────── │
│ 14:32 Sales Agent called linkedin_lookup("John Smith")        │
│ 14:31 Sales Agent decided: "include" for Jane Doe             │
│ 14:30 Content Agent created draft: "AI in 2026"               │
│ 14:28 Sales Agent completed company: "TechCorp"               │
│                                                                │
│ LEARNINGS                                     [Extract →]      │
│ ───────────────────────────────────────────────────────────── │
│ • Sales Agent: LinkedIn lookups succeed 78% of the time       │
│ • Sales Agent: "VP" titles convert better than "Director"     │
│ • Content Agent: Posts with examples get 2x engagement        │
│                                                                │
└────────────────────────────────────────────────────────────────┘
```

---

## Success Criteria

- [ ] Can see all active/paused/completed agents
- [ ] Progress indicators show stage and completion %
- [ ] Can pause/resume/stop agents
- [ ] Activity log shows recent agent actions
- [ ] Can drill into specific agent's trace
- [ ] Learnings extracted and displayed

---

## Key Features

### Agent Status Panel
- List of all agents with status (🟢 running, 🟡 paused, 🔴 stopped, ✅ complete)
- Current task description
- Progress bar or percentage
- Resource usage (tokens, time, API calls)
- Control buttons (pause/resume/stop/redirect)

### Activity Feed
- Real-time (or near-real-time) stream of agent events
- Filterable by agent, event type
- Expandable details for each event
- Link to full trace view

### Trace Viewer
- Complete history of agent's decisions and actions
- Tree view of tool calls
- Input/output for each step
- Timing information
- Error highlighting

### Learnings Extraction
- Patterns detected across executions
- Success/failure rates by action type
- Suggestions for improvement
- Export to notes (knowledge graph)

---

## Data Model

```typescript
interface AgentStatus {
  agent_id: string;
  name: string;
  status: 'running' | 'paused' | 'stopped' | 'completed' | 'failed';
  current_task: string;
  progress: {
    current: number;
    total: number;
    percentage: number;
  };
  resources: {
    runtime_ms: number;
    tokens_used: number;
    api_calls: number;
  };
  last_event: AgentEvent;
  started_at: Date;
  updated_at: Date;
}

interface AgentTrace {
  agent_id: string;
  events: AgentEvent[];
  summary: {
    total_events: number;
    tool_calls: number;
    decisions: number;
    errors: number;
    reviews_requested: number;
  };
}
```

---

## Technical Approach

### Stack
- React for UI
- Polling or WebSocket for live updates
- Charts: Recharts or similar for resource usage
- Virtual scrolling for long activity lists

### Agent Control
```typescript
// Pause agent
async function pauseAgent(agentId: string) {
  await db.execute(`
    UPDATE agents SET status = 'paused' WHERE id = ?
  `, [agentId]);
  
  // Agent checks status before each action
  // If paused, it stops and waits
}

// Resume agent
async function resumeAgent(agentId: string) {
  await db.execute(`
    UPDATE agents SET status = 'active' WHERE id = ?
  `, [agentId]);
  
  // Trigger agent to continue (notification or polling)
}
```

### Progress Tracking
- Agents emit progress events: `{ current: 32, total: 50, stage: "researching" }`
- UI aggregates events to show current progress
- Estimated time remaining based on rate

---

## Relationship to Other Workstreams

- **Foundation**: Reads agent_events table
- **Review UI**: Review requests visible here too
- **Knowledge Graph**: Learnings can become notes

---

## Definition of Done

This workstream is complete when:
- User can see all agents and their status
- Can pause/resume agents mid-execution
- Activity feed shows what agents are doing
- Can extract learnings from completed executions

