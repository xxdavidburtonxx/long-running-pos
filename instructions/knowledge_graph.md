# Workstream: Knowledge Graph (Human Cognition)

**Priority**: P1 — Core human interface

---

## The Job

Build an Obsidian-like knowledge graph interface in TypeScript:
- Markdown editor with `[[backlink]]` support
- Graph visualization of note connections
- Search, tags, navigation
- Sync notes to/from libSQL database

---

## Source Inspiration

- **Logseq** (https://github.com/logseq/logseq) — Concepts, not code (ClojureScript → TypeScript)
- **Obsidian** — UX reference (user's current tool)
- User's vault: `/Users/davidburton/Library/Mobile Documents/iCloud~md~obsidian/Documents/Life Headquarters/HUMAN`

---

## Success Criteria

- [ ] Can create/edit markdown notes
- [ ] `[[backlinks]]` are clickable and create connections
- [ ] Graph view shows note relationships
- [ ] Search finds notes by content
- [ ] Tags work (`#tag` syntax)
- [ ] Notes sync to libSQL (content_blob, metadata)
- [ ] Can import existing Obsidian vault

---

## Key Features

### Editor
- Markdown with live preview or WYSIWYG
- `[[wikilink]]` syntax with autocomplete
- Code blocks with syntax highlighting
- Drag-drop images/files

### Navigation
- File tree (folder structure)
- Backlinks panel (what links here?)
- Graph visualization (force-directed or similar)
- Quick search (Cmd+O style)
- Tag browser

### Database Sync
- Notes stored as files on disk (source of truth)
- Metadata + content_blob synced to libSQL
- Backlinks extracted and stored in `note_links` table
- Chunks created for semantic search

---

## Technical Approach

### Stack
- **Editor**: TipTap, ProseMirror, or CodeMirror
- **Graph**: D3.js, Sigma.js, or Cytoscape
- **Framework**: React (for VSCode compatibility later)
- **State**: Zustand or similar

### File ↔ Database Sync
```typescript
// On file save
async function syncNoteToDatabase(filePath: string) {
  const content = await fs.readFile(filePath, 'utf-8');
  const { title, tags, backlinks } = parseMarkdown(content);
  
  await db.execute(`
    INSERT OR REPLACE INTO notes (id, title, content_blob, content_text, file_path, word_count, updated_at)
    VALUES (?, ?, ?, ?, ?, ?, datetime('now'))
  `, [generateId(filePath), title, content, content, filePath, countWords(content)]);
  
  // Update backlinks
  await syncBacklinks(noteId, backlinks);
  
  // Update tags
  await syncTags(noteId, tags);
}
```

---

## Relationship to Other Workstreams

- **Foundation**: Depends on database schema and core types
- **Review UI**: Knowledge can spawn tasks that need review
- **Workbench**: Agent actions may reference notes for context

---

## Definition of Done

This workstream is complete when:
- User can write atomic notes (200-500 words)
- Notes link to each other with clickable backlinks
- Graph view shows the knowledge structure
- Notes are queryable in the database
- Existing Obsidian vault can be imported

