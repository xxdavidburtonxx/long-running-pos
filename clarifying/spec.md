# Personal Operating System (POS) - Specification

## The Product in One Sentence

A local-first Personal Operating System where human cognition (knowledge graph) and AI cognition (autonomous agents) seamlessly integrate, with work building on itself over time.

---

## Core Problem

All work today happens in two disconnected places:
1. **Obsidian** — Human thinking, note-taking, idea connection
2. **Claude Code** — AI execution of clarified tasks

**Missing pieces:**
- No seamless handoff between human clarity and AI execution
- No efficient UI for human review of AI decisions
- No workbench to observe/control long-running agents
- No accumulated learnings from agent executions
- No unified data model tying it all together

---

## The Three Worlds (Toggle with Tab)

```
┌─────────────────────────────────────────────────────────────────┐
│                    PERSONAL OPERATING SYSTEM                     │
├─────────────────┬─────────────────┬─────────────────────────────┤
│  HUMAN WORLD    │   HANDOFF UI    │      AGENT WORLD            │
│                 │                 │                             │
│  Knowledge      │  Review Panel   │  Terminal (Claude Code)     │
│  Graph          │  (checkboxes)   │  + Workbench                │
│  (Obsidian-     │                 │                             │
│   like)         │  Learnings      │  File Browser               │
│                 │  Dashboard      │  (VSCode-like)              │
│                 │                 │                             │
│  Atomic Notes   │  Task Board     │  Agent Logs                 │
│  200-500 words  │  (Plane/Linear) │  & Traces                   │
│                 │                 │                             │
└─────────────────┴─────────────────┴─────────────────────────────┘
                          ↕
                    libSQL Database
                  (local, on-disk)
```

---

## Component Architecture

### 1. Knowledge Graph (Human Cognition)
**Purpose**: Where humans gain clarity through connected ideas

**Source**: Fork/reimplement Logseq concepts in TypeScript
**Reference**: Obsidian folder at `/Users/davidburton/Library/Mobile Documents/iCloud~md~obsidian/Documents/Life Headquarters/HUMAN`

**Features**:
- Markdown files that link to each other (`[[backlinks]]`)
- Atomic notes (200-500 word learning units)
- Graph visualization of connections
- Tags, search, backlinks navigation
- AI-suggested connections and cleanup
- Adjacent possible: connecting unrelated ideas → eureka moments

**Data**:
- `.md` files stored as BLOBs in libSQL
- Chunked representations for linking to specific sections
- Metadata: tags, backlinks, last modified, etc.

### 2. Review Panel (Human-AI Handoff)
**Purpose**: Efficient human review of AI decisions

**Example Use Case**: CRM data entry
- Agent finds 100 decision makers
- Agent marks each as "include" or "exclude" with reasoning
- Human sees list with checkboxes:
  - ☑️ = Agent likes, human agrees (default selected)
  - ☐ = Agent likes, human disagrees (deselect to override)
  - ☐ = Agent doesn't like but surfaces anyway, human can select to override
- Human reviews in bulk, approves, agent continues

**Features**:
- Batch review UI (not one-by-one approval)
- Agent reasoning visible for each decision
- Quick keyboard navigation
- Filters: "Show only agent recommendations" / "Show all"
- Approve and continue workflow

### 3. Workbench (Agent Observation & Control)
**Purpose**: Instruments to understand and control long-running agents

**Features**:
- Agent status: running / paused / completed / stuck
- Progress indicators: what stage? how far?
- Resource usage: tokens, time, API calls
- Pause/resume/cancel controls
- Redirect: give new direction mid-execution
- Trace viewer: what did the agent do and why?
- Learning extraction: what patterns emerged?

### 4. Terminal (AI Cognition)
**Purpose**: Where agents execute clarified tasks

**Tool**: Claude Code (staying with it unless we need to augment)
**Alternative**: opencode if we need open source modifications

**Features**:
- Standard AI coding terminal
- Integration with POS database
- Access to knowledge graph for context
- Event emission to database (actions, decisions, outcomes)

### 5. File Browser (IDE View)
**Purpose**: See and edit files created by agents

**Source**: VSCode fork or embed
**Why fork**: Deep integration with POS, not just extensions

**Features**:
- Standard file tree navigation
- Syntax highlighting for all file types
- Diff view for agent changes
- Direct editing when needed
- Integration with knowledge graph (link files to notes)

### 6. Task Management
**Purpose**: Turn clarified knowledge into executable tasks

**Source**: Plane (https://github.com/makeplane/plane) or Linear API

**Features**:
- Tasks derived from atomic notes
- Agent assignment to tasks
- Progress tracking
- Completion → learnings extraction

### 7. Learnings Dashboard
**Purpose**: Accumulated wisdom from agent executions

**Features**:
- What worked? What didn't?
- Pattern detection across executions
- Suggestions for process improvement
- Feed historical context to future agents
- Database schema insights (what to change?)

---

## Data Model (libSQL)

### Entities (Nouns)
```sql
-- Agents that execute work
CREATE TABLE agents (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT, -- 'main', 'sub', 'specialist'
    parent_agent_id TEXT REFERENCES agents(id),
    status TEXT, -- 'active', 'paused', 'completed', 'failed'
    config JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Scripts/Tools that agents call
CREATE TABLE tools (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    description TEXT,
    code_path TEXT, -- path to script file
    input_schema JSON,
    output_schema JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Knowledge notes (atomic units)
CREATE TABLE notes (
    id TEXT PRIMARY KEY,
    title TEXT NOT NULL,
    content_blob BLOB, -- full .md file
    content_text TEXT, -- searchable text
    file_path TEXT, -- where on disk
    word_count INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP
);

-- Chunks of notes for granular linking
CREATE TABLE note_chunks (
    id TEXT PRIMARY KEY,
    note_id TEXT REFERENCES notes(id),
    chunk_index INTEGER,
    content TEXT,
    embedding BLOB, -- for semantic search
    start_line INTEGER,
    end_line INTEGER
);

-- Tasks derived from notes
CREATE TABLE tasks (
    id TEXT PRIMARY KEY,
    title TEXT NOT NULL,
    description TEXT,
    source_note_id TEXT REFERENCES notes(id),
    assigned_agent_id TEXT REFERENCES agents(id),
    status TEXT, -- 'pending', 'in_progress', 'review', 'done'
    priority INTEGER,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Events (Verbs)
```sql
-- Agent actions (trace log)
CREATE TABLE agent_events (
    id TEXT PRIMARY KEY,
    agent_id TEXT REFERENCES agents(id),
    event_type TEXT, -- 'tool_call', 'decision', 'output', 'error', 'review_request'
    tool_id TEXT REFERENCES tools(id),
    input JSON,
    output JSON,
    success BOOLEAN,
    duration_ms INTEGER,
    tokens_used INTEGER,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Human review events
CREATE TABLE review_events (
    id TEXT PRIMARY KEY,
    agent_event_id TEXT REFERENCES agent_events(id),
    reviewer TEXT, -- 'human', 'auto'
    decision TEXT, -- 'approve', 'reject', 'modify'
    original_value JSON,
    final_value JSON,
    notes TEXT,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Learnings extracted from executions
CREATE TABLE learnings (
    id TEXT PRIMARY KEY,
    source_type TEXT, -- 'agent_execution', 'human_insight', 'pattern_detection'
    source_id TEXT,
    learning TEXT,
    confidence REAL, -- 0-1
    applied_count INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Relationships (Adjectives)
```sql
-- Note-to-note links (backlinks)
CREATE TABLE note_links (
    from_note_id TEXT REFERENCES notes(id),
    to_note_id TEXT REFERENCES notes(id),
    link_type TEXT, -- 'backlink', 'tag', 'reference'
    context TEXT, -- surrounding text
    PRIMARY KEY (from_note_id, to_note_id, link_type)
);

-- Agent hierarchies
CREATE TABLE agent_relationships (
    parent_agent_id TEXT REFERENCES agents(id),
    child_agent_id TEXT REFERENCES agents(id),
    relationship_type TEXT, -- 'controls', 'delegates_to', 'monitors'
    PRIMARY KEY (parent_agent_id, child_agent_id)
);

-- Tags
CREATE TABLE tags (
    id TEXT PRIMARY KEY,
    name TEXT UNIQUE NOT NULL
);

CREATE TABLE note_tags (
    note_id TEXT REFERENCES notes(id),
    tag_id TEXT REFERENCES tags(id),
    PRIMARY KEY (note_id, tag_id)
);
```

---

## Example Workflow: Sales Pipeline

### Stage 1: ICP Identification
```
Human: Writes atomic note "What makes a good ICP for [product]"
       → Connects to notes about successful customers
       → Clarity emerges: ICP criteria defined
       → Creates task: "Build ICP scoring script"

Agent: Receives task with context from linked notes
       → Builds script that queries public data
       → Scores companies against ICP criteria
       → Outputs ranked list to database
       → Emits events to agent_events table
```

### Stage 2: Decision Maker Identification
```
Agent: For each ICP company, finds decision makers
       → Researches each person (LinkedIn, company site, news)
       → Forms opinion: "reach out" or "skip" with reasoning
       → Creates review_request event