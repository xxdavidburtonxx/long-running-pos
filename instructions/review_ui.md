# Workstream: Review UI (Human-AI Handoff)

**Priority**: P1 — Critical for agent autonomy

---

## The Job

Build the interface where humans efficiently review AI decisions:
- Batch review with checkboxes
- Agent reasoning visible
- Approve/reject/modify actions
- Unblock agent to continue

---

## The Core Interaction

```
┌────────────────────────────────────────────────────────────────┐
│ Review: Decision Makers for Acme Corp                    [100] │
├────────────────────────────────────────────────────────────────┤
│ Filter: [All ▾] [Agent Recommends ▾]          Search: [____]  │
├────────────────────────────────────────────────────────────────┤
│ ☑️ John Smith - VP Engineering                                 │
│    Agent: "Strong fit - 10 yrs experience, active on LinkedIn" │
│                                                                │
│ ☑️ Jane Doe - CTO                                              │
│    Agent: "Decision maker, spoke at relevant conference"       │
│                                                                │
│ ☐ Bob Wilson - Senior Dev (Agent: Skip)                       │
│    Agent: "Not a decision maker, individual contributor"       │
│    [Click to override and include]                             │
│                                                                │
│ ☐ Alice Chen - Intern (Agent: Skip)                           │
│    Agent: "Too junior for this outreach"                       │
├────────────────────────────────────────────────────────────────┤
│                    [Approve Selected (2)] [Approve All (2)]    │
└────────────────────────────────────────────────────────────────┘
```

---

## Success Criteria

- [ ] Can view pending review requests from agents
- [ ] Checkbox interface for batch approve/reject
- [ ] Agent reasoning displayed for each item
- [ ] Can override agent recommendations (select skipped, deselect recommended)
- [ ] Approval triggers agent to continue
- [ ] Review decisions logged to `review_events` table

---

## Key Features

### Review Queue
- List of pending `review_request` events
- Grouped by task/agent
- Priority ordering
- Count of items awaiting review

### Batch Interface
- Select all / deselect all
- Keyboard navigation (j/k to move, space to toggle)
- Quick filters (agent recommends, agent skips, all)
- Search within items

### Decision Capture
- Each toggle creates a `review_event` record
- Tracks: original agent opinion, human decision, timestamp
- Notes field for human reasoning (optional)

### Agent Continuation
- On "Approve", mark review_request as resolved
- Agent workflow continues with approved items
- Rejected items excluded from downstream processing

---

## Data Model

```typescript
interface ReviewRequest {
  id: string;
  agent_id: string;
  task_id: string;
  items: ReviewItem[];
  created_at: Date;
  status: 'pending' | 'approved' | 'rejected';
}

interface ReviewItem {
  id: string;
  data: any; // The thing being reviewed (person, company, etc.)
  agent_recommendation: 'include' | 'exclude';
  agent_reasoning: string;
  human_decision?: 'include' | 'exclude' | null;
  human_notes?: string;
}
```

---

## Technical Approach

### Stack
- React components
- Keyboard shortcuts (react-hotkeys-hook)
- Optimistic UI updates
- WebSocket or polling for new review requests

### Agent Integration
```typescript
// Agent creates review request
await db.execute(`
  INSERT INTO agent_events (id, agent_id, event_type, input, timestamp)
  VALUES (?, ?, 'review_request', ?, datetime('now'))
`, [eventId, agentId, JSON.stringify({ items, task_id })]);

// UI polls for pending reviews
const pending = await db.execute(`
  SELECT * FROM agent_events 
  WHERE event_type = 'review_request' 
  AND id NOT IN (SELECT agent_event_id FROM review_events)
`);
```

---

## Relationship to Other Workstreams

- **Foundation**: Uses agent_events and review_events tables
- **Workbench**: Review requests visible in agent activity
- **Knowledge Graph**: Review decisions may inform future notes/learnings

---

## Definition of Done

This workstream is complete when:
- User can see all pending review requests
- Can approve/reject items in batch
- Agent can continue after approval
- All decisions are logged for learning

