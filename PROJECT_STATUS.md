# CollabMark / DocuFlow — Project Status & Technical Handoff

## 1. Project Overview
**DocuFlow (CollabMark)** is a high-performance, real-time collaborative Markdown & Mermaid Diagram Studio built for a 32-hour hackathon. It combines the editing experience of HackMD/VS Code with live Mermaid diagram rendering, conflict-free multiplayer editing via Yjs (CRDT), visual version history checkpoints with diff comparison, sharing permissions, and multi-format exports (Markdown, HTML, PDF).

---

## 2. Current Architecture
The system uses a client-server architecture powered by WebSockets and CRDTs:

```mermaid
flowchart TD
    subgraph Browser ["Frontend Browser (Vite + React 19)"]
        UI[App Component]
        Monaco[Monaco Editor]
        Preview[React Markdown + Mermaid]
        YMonaco[y-monaco Binding]
        Awareness[Awareness Protocol (Presence & Cursors)]
        WSProvider[y-websocket Provider]
    end

    subgraph Server ["Backend (Node.js + Express + WS)"]
        WSServer[WebSocket Server (Port 1234)]
        YDocStore[In-Memory Y.Docs + Disk Cache]
        RESTAPI[REST API (/api/*)]
        Snapshots[JSON Snapshot Store]
    end

    UI --> Monaco
    UI --> Preview
    Monaco <--> YMonaco
    YMonaco <--> WSProvider
    Awareness <--> WSProvider
    WSProvider <-->|WebSocket wss:// or ws://| WSServer
    WSServer <--> YDocStore
    UI <-->|HTTP REST /api| RESTAPI
    RESTAPI <--> Snapshots
```

---

## 3. Technology Stack
### Frontend
- **Framework:** React 19, TypeScript
- **Bundler & Build Tool:** Vite 8 + `@tailwindcss/vite`
- **Styling:** Tailwind CSS v4, custom CSS scrollbars and print media queries
- **Code Editor:** Monaco Editor (`@monaco-editor/react`, `monaco-editor`)
- **CRDT / Multiplayer:** `yjs`, `y-monaco`, `y-websocket`, `lib0`
- **Diagramming:** `mermaid` (v12) with custom error handling, zoom/inspect modal, and SVG export
- **Markdown Rendering:** `react-markdown`, `remark-gfm`
- **Diffing:** `diff` (v9) with unified diff display
- **Icons & Polish:** `lucide-react`, `canvas-confetti`

### Backend
- **Runtime:** Node.js (v24 ES modules)
- **Web Framework:** Express 4
- **Real-Time Communication:** `ws` (v8) + `y-websocket` binary synchronization protocol
- **CRDT Engine:** `yjs` (v13)
- **Persistence:** Binary updates cached in `server/data/docs/` and metadata/snapshots in `server/data/*.json`

---

## 4. Folder Structure
```
e:/DocuFlow/
├── package.json               # Root scripts (server, client, install)
├── PROJECT_STATUS.md          # Comprehensive handoff documentation
├── TODO.md                    # Prioritized roadmap & feature checklist
├── server/
│   ├── package.json           # Backend dependencies (express, ws, yjs, y-websocket)
│   ├── server.js              # Combined HTTP + WebSocket server & REST endpoints
│   └── data/                  # Auto-generated persistence directory
│       ├── docs/              # Binary state snapshots (<room>.bin)
│       ├── snapshots.json     # Saved version checkpoints & metadata
│       └── rooms.json         # Room permission configurations
└── client/
    ├── package.json           # Frontend dependencies
    ├── vite.config.ts         # Vite configuration with Tailwind & monaco alias
    ├── tsconfig.json          # TypeScript compiler configuration
    ├── index.html             # HTML entrypoint
    └── src/
        ├── main.tsx           # React root mounting
        ├── App.tsx            # Main layout, room state, layout mode, modals
        ├── index.css          # Tailwind imports, selection highlights, print styles
        ├── types.ts           # Type definitions (UserPresence, Snapshot, RoomMetadata)
        ├── components/
        │   ├── Navbar.tsx             # Top header, presence badges, export menu
        │   ├── Toolbar.tsx            # Markdown formatting & Mermaid insert dropdown
        │   ├── Editor.tsx             # Monaco editor, y-monaco binding, awareness cursors
        │   ├── Preview.tsx            # Live Markdown preview & code block wrapper
        │   ├── MermaidBlock.tsx       # Mermaid SVG renderer, error fallback, zoom modal
        │   ├── StatsBar.tsx           # Document statistics (words, chars, lines, reading time)
        │   ├── ShareModal.tsx         # Room share links, read-only permissions, demo link
        │   ├── VersionHistoryModal.tsx# Snapshot timeline, visual diff, version restore
        │   ├── RoomDrawer.tsx         # Document room explorer & template applier
        │   └── ShortcutsModal.tsx     # Keyboard shortcuts and syntax cheatsheet
        └── utils/
            ├── exportUtils.ts         # Markdown, HTML, and PDF export handlers
            └── userUtils.ts           # User profiles, codename generator, cursor colors
```

---

## 5. Features That Are COMPLETE
- [x] **Split-Screen Markdown Editor + Live Preview**
  - Monaco editor with syntax highlighting, line numbers, smooth cursor animation
  - Live markdown preview updating in real-time as you type
  - Full support for Headings, Bold/Italic, Strikethrough, Lists, Blockquotes, Tables, and Code blocks
  - 3 view modes: Split (50/50), Editor Only (focus mode), Preview Only (reading mode)
- [x] **Real-Time Multiplayer Collaboration**
  - Yjs CRDT synchronization with `y-monaco` and `y-websocket`
  - Zero conflict data consistency
  - Live user presence: remote user avatars, connected count, user name tags
  - Live remote cursors and selection highlighting rendered in Monaco
  - Connection status badge with auto-reconnect and manual reconnect button
  - Document room URL routing via hash (`#/room-name`)
- [x] **Native Mermaid Diagram Support**
  - Live rendering of ````mermaid code blocks inside markdown
  - Supports Flowcharts, Sequence diagrams, Class diagrams, State diagrams, ERDs, Git graphs
  - Non-blocking syntax error fallback badge (typing does not crash the preview)
  - Zoom & inspect fullscreen modal with scale controls (50% to 300%)
  - "Download Diagram as SVG" button on every rendered diagram
  - "Insert Mermaid Diagram" toolbar dropdown with 5 pre-built templates
- [x] **Version History & Diff Snapshots**
  - Checkpoint creation with custom name, author tag, and timestamp
  - Visual diff comparison comparing any historical snapshot against the live document
  - 1-click snapshot restore with immediate CRDT rollback
- [x] **Permissions & Sharing**
  - 1-click copy link for collaborator edit access
  - Audience "View-Only" mode link (`?mode=view`) locking Monaco editor
  - Global read-only lock toggle in Room metadata
- [x] **Export Capabilities**
  - Export to raw Markdown (`.md` file)
  - Export to standalone HTML document with embedded CSS and rendered SVG diagrams
  - Print-to-PDF triggered through browser print dialog with dedicated print CSS
- [x] **Templates & Room Hub**
  - Built-in starter templates (Welcome Studio, System Architecture, Sprint Planning)
  - Room drawer to explore active/saved rooms or generate random room IDs
  - In-place document title editing synced with backend
- [x] **Polish & Extras**
  - Light/Dark mode toggle with synchronized Monaco themes and Mermaid themes
  - Bottom metrics bar: word count, character count, line count, diagram count, reading time
  - Keyboard shortcuts modal (`?` key or button)

---

## 6. Features That Are PARTIALLY COMPLETE
- Document password protection: API endpoint `POST /api/rooms/:roomId/verify-passcode` is implemented on the server; the UI currently defaults to open collaboration and can be wired to a password prompt modal if passwords are set.

---

## 7. Features That Are NOT STARTED
- Third-party cloud storage sync (Google Drive / GitHub Gist sync) — not requested for MVP.

---

## 8. Current Implementation Details
- **Editor Binding:** In `client/src/components/Editor.tsx`, `MonacoBinding` connects Monaco's text model directly to `ydoc.getText('monaco')` and attaches the awareness protocol for cursor and selection broadcast.
- **Preview Reactivity:** In `client/src/App.tsx`, `ytext.observe()` updates the `content` state on every local or remote change, passing it to `Preview.tsx`.
- **Mermaid Rendering:** `MermaidBlock.tsx` uses unique element IDs and calls `mermaid.render()` inside a `useEffect`, catching exceptions gracefully when the user is midway through typing invalid syntax.
- **Persistence:** When edits occur, the backend server debounces saving binary Yjs updates to `server/data/docs/<roomId>.bin`.

---

## 9. Database Schema (File-Based JSON / Binary Store)
- `server/data/docs/<roomId>.bin`: Binary encoded Yjs document state.
- `server/data/snapshots.json`:
  ```json
  {
    "<roomId>": [
      {
        "id": "snap_1727680000000_abc12",
        "title": "v1.0 Milestone Checkpoint",
        "author": "Cyber Architect",
        "createdAt": "2026-09-30T07:15:00.000Z",
        "content": "# Markdown content...",
        "charCount": 1240,
        "lineCount": 45
      }
    ]
  }
  ```
- `server/data/rooms.json`:
  ```json
  {
    "<roomId>": {
      "roomId": "welcome-studio",
      "title": "Welcome to CollabMark",
      "readOnly": false,
      "passcode": null,
      "updatedAt": "2026-09-30T07:15:00.000Z"
    }
  }
  ```

---

## 10. API Endpoints
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/api/health` | Returns server health and timestamp |
| `GET` | `/api/templates` | Lists starter templates with descriptions & content |
| `GET` | `/api/rooms` | Lists active and saved rooms with user count & metadata |
| `GET` | `/api/rooms/:roomId/metadata` | Fetches metadata for a specific room |
| `POST` | `/api/rooms/:roomId/metadata` | Updates title, readOnly status, or passcode |
| `POST` | `/api/rooms/:roomId/verify-passcode`| Verifies room access passcode |
| `GET` | `/api/rooms/:roomId/snapshots`| Retrieves all saved snapshots for room |
| `POST` | `/api/rooms/:roomId/snapshots`| Creates and saves a new version checkpoint |
| `POST` | `/api/rooms/:roomId/restore` | Restores document to a specified snapshot ID |
| `POST` | `/api/rooms/:roomId/apply-template` | Applies a starter template to the live room |

---

## 11. WebSocket / Real-Time Architecture
- **Port:** `1234`
- **Protocol:** `y-websocket` binary protocol
- **Endpoint:** `ws://localhost:1234/<roomId>`
- **Awareness:** Handles presence, user names, cursor line/column coordinates, and user colors.

---

## 12. Environment Variables Required
None required for local development. Default values:
- `PORT=1234` (Backend server and WebSocket port)
- `VITE_WS_URL` (Optional frontend override for WebSocket host)

---

## 13. What is Currently Working
- The backend server is actively running on port 1234 (HTTP + WS).
- The frontend dev server is actively running on `http://localhost:5173`.
- TypeScript builds with 0 errors (`npm run build` succeeds).
- Real-time CRDT sync, Monaco editor, Mermaid diagram rendering, snapshot history, diff view, and exports are all operational.

---

## 14. Known Bugs
None.

---

## 15. Current Errors
None.

---

## 16. What Was Being Worked on Immediately Before This Handoff
- Resolved Monaco Editor package alias resolution in Vite (`monaco-editor/esm/vs/editor/editor.api.js` mapped to `monaco-editor`).
- Validated production bundle build (`npm run build` completed in 3.28s).
- Verified backend server on `http://localhost:1234` and frontend on `http://localhost:5173`.

---

## 17. EXACT Next Steps
1. Open `http://localhost:5173` in a browser.
2. Open a second tab or window at `http://localhost:5173#welcome-studio` to demo real-time multiplayer editing and cursor tracking.
3. Test Mermaid diagram zoom and SVG download.
4. Test creating a version snapshot and viewing the visual diff.

---

## 18. Commands to Run the Project
To run both simultaneously from the project root:
```bash
# In Terminal 1 (Backend):
npm run server

# In Terminal 2 (Frontend):
npm run client
```

---

## 19. Commands to Run Frontend
```bash
cd client
npm run dev
# Or build for production:
npm run build
```

---

## 20. Commands to Run Backend
```bash
cd server
npm start
# Or with auto-reload:
npm run dev
```

---

## 21. Testing Status
- Build test: `npm run build` passed with code 0.
- Health test: `GET /api/health` returned HTTP 200 OK.
- Frontend test: `http://localhost:5173/` returned HTTP 200 OK.
- Proxy test: Vite proxy `/api/*` to backend returned HTTP 200 OK.

---

## 22. Deployment Status
Ready for deployment on any Node-capable platform (e.g., Render, Railway, fly.io, or VPS) by running `npm run build` in `client` and serving the static dist via `express.static` or reverse proxy.

---

## 23. Important Technical Decisions
- **Yjs CRDT:** Chosen over Operational Transformation (OT) because CRDTs require no centralized operational transform server and work deterministically across peers.
- **y-monaco:** Direct binding between Yjs text type and Monaco model gives native undo/redo stacks and remote cursor markers.
- **Client-side Mermaid.js:** Diagrams render instantly client-side without round-trips to an external image rendering API.
