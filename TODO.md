# CollabMark / DocuFlow — Development Roadmap & Prioritized Checklist

## Legend
- **P0:** Absolutely required core features for MVP
- **P1:** Important features for competition readiness & user experience
- **P2:** Polish, bonus features, and optional enhancements

---

## [P0] Core MVP Requirements
- [x] **Split-Screen Interface:** Left Monaco editor, Right live rendered Markdown preview.
- [x] **Monaco Editor Integration:** Syntax highlighting, line numbers, word wrap, smooth cursor.
- [x] **Markdown Elements Rendering:** Headings, bold/italic, lists, blockquotes, code blocks, tables, links.
- [x] **Live Typing Update:** Immediate update of rendered Markdown preview without lagging.
- [x] **Real-Time Collaboration (CRDT):** Yjs + `y-monaco` + `y-websocket` binary protocol.
- [x] **Multiplayer Room Support:** Dynamic room routing via URL hash (`#/room-name`).
- [x] **Zero-Conflict Synchronization:** Concurrent edits from 5+ users merge seamlessly.
- [x] **Connected Users List & Presence:** Visual avatar badges showing active collaborator count.
- [x] **Connection Status Indicator:** Clear connection badge (Connected / Connecting / Disconnected).
- [x] **Reconnect Handling:** Automatic retry and manual 1-click reconnect button.
- [x] **Mermaid Diagram Support:** Native rendering of ````mermaid code blocks inside Markdown.
- [x] **Mermaid Syntax Error Tolerance:** Resilient error handling that does not crash preview while typing.

---

## [P1] Important Hackathon Features
- [x] **Remote Cursors & Selection:** Live remote cursor indicators with collaborator names and custom colors in Monaco.
- [x] **Version History Checkpoints:** Create manual snapshots with custom titles and timestamps.
- [x] **Visual Diff Comparison:** Line-by-line diff view highlighting additions and removals against current document.
- [x] **Snapshot Rollback / Restore:** 1-click restoration of any historical version to the live CRDT doc.
- [x] **Sharing Permissions:**
  - [x] Instant copy collaborator edit link.
  - [x] Audience "View-Only" link (`?mode=view`) locking the editor.
  - [x] Room-level global read-only lock toggle.
- [x] **Multi-Format Export:**
  - [x] Download as raw Markdown (`.md`).
  - [x] Download as standalone HTML document with embedded CSS and rendered SVG diagrams.
  - [x] Print / Save to PDF with optimized `@media print` layout.
- [x] **Interactive Mermaid Tools:**
  - [x] "Download Diagram as SVG" button on each rendered diagram.
  - [x] Diagram inspector & fullscreen zoom modal (50% to 300% zoom controls).
  - [x] "Insert Mermaid Diagram" toolbar dropdown with 5 pre-built templates (Flowchart, Sequence, Class, ERD, Git).
- [x] **Document Hub & Starter Templates:**
  - [x] Welcome & Features Tour template.
  - [x] System Architecture & Sequence Diagram template.
  - [x] Sprint Planning & State Diagram template.
  - [x] Room switcher drawer with room discovery and random room generator.
- [x] **Markdown Toolbar:** Bold, Italic, Strikethrough, Headings, Lists, Checklist, Quote, Code, Table, Link.

---

## [P2] Polish, UX & Optional Enhancements
- [x] **Dark / Light Theme Toggle:** Synchronized Monaco editor theme and Mermaid diagram theme (`dark` vs `default`).
- [x] **View Modes:** Split (50/50), Editor Only (focus mode), Preview Only (presentation mode).
- [x] **Custom User Multiplayer Profile:** Editable display name and custom cursor color picker.
- [x] **Document Metrics Bar:** Live word count, character count, line count, reading time estimate, and cursor line/column.
- [x] **Keyboard Shortcuts Cheatsheet Modal:** Quick reference popup accessible via `?` key.
- [x] **Confetti Celebrations:** Delightful confetti effect on link copy.
- [ ] **Sync Scrolling:** Bi-directional proportional scroll-lock between editor and preview.
- [ ] **Room Passcode Prompt Modal:** Frontend modal verifying passcode before allowing room entrance when password is set.
- [ ] **Offline IndexedDB Caching:** Offline editing fallback with `y-indexeddb` when disconnected from network.
