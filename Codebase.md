# Codebase.md — CYOA Project Architecture

**Team:** Alec Situ & Hyobin Yook  
**Stack:** React + Vite, React Flow (@xyflow/react), Dagre, Tailwind CSS

---

## Overview

This is a fully client-side, no-backend web application. All story data is stored in the browser's `localStorage`. The app has two modes — **Author** (create and edit stories) and **Reader** (play through stories) — served from a single-page React app with hash-based routing.

---

## Entry Point

```
main.jsx
```

Bootstraps the React app inside a `HashRouter` (enables GitHub Pages deployment without a server). Wraps everything in `App`.

```
App.jsx
```

The root component. Wraps the entire app in `SettingsProvider` for global state, renders `Navbar`, and defines all routes via React Router v7.

---

## Routing

| Path | Component | Description |
|---|---|---|
| `/` | `LandingPage` | Home screen with Author / Reader buttons |
| `/author` | `AuthorHome` | Create or upload a story |
| `/edit/:storyId` | `GraphEditor` | Interactive graph editor for a story |
| `/reader` | `StoryList` | Browse available stories |
| `/read/:storyId` | `StoryReader` | Play through a story |

---

## File Structure

```
src/
├── App.jsx                          # Root component, routing, navbar
├── SettingsContext.jsx              # Global settings state (theme, edge style)
├── main.jsx                         # App bootstrap
├── index.css                        # CSS variables, theme overrides, ReactFlow overrides
│
├── components/
│   ├── Landing/
│   │   └── LandingPage.jsx          # Home screen
│   ├── Author/
│   │   ├── AuthorHome.jsx           # Story list + create/upload options
│   │   ├── GraphEditor.jsx          # Main graph editor (ReactFlow + dagre)
│   │   └── NodeEditor.jsx           # Side panel: edit node title, text, choices
│   └── Reader/
│       ├── StoryList.jsx            # Browse stories
│       └── StoryReader.jsx          # Play a story + visited path graph
│
├── data/
│   └── cave-of-time.json            # Bundled demo story, pre-loaded on first visit
│
└── utils/
    ├── storage.js                   # localStorage helpers
    └── storyHelpers.js              # Graph analysis (reachable, orphans, endings)
```

---

## Key Components

### `SettingsContext.jsx`
Global React Context wrapping the entire app. Stores:
- `theme` — `'dark'` (default) or `'light'`, applied as `data-theme` on `<html>`
- `edgeStyle` — `'default'` (curve), `'straight'`, or `'smoothstep'`

Both values are persisted to `localStorage` so they survive page reloads.

---

### `GraphEditor.jsx`
The author's main workspace. Built on `@xyflow/react` with `dagre` for auto-layout.

**Key responsibilities:**
- Converts story JSON nodes into ReactFlow nodes and edges via `layoutGraph()`
- Custom `StoryNode` component with explicit `Handle` positions (left/right or top/bottom depending on layout direction)
- Toolbar: Add Node, Export JSON, Back, Layout Toggle (Vertical/Horizontal), Align, Edge Style, Help
- Node selection opens `NodeEditor` panel; selected node highlighted with gold border
- Clicking an edge label opens the source node's editor
- Drag-to-connect creates a new choice between nodes
- `handleAlign()` re-runs dagre and calls `fitView` to reset positions
- Reads `edgeStyle` from `SettingsContext` and passes it to `layoutGraph`

**Layout:**
- Dagre `rankdir: 'TB'` (vertical) or `'LR'` (horizontal)
- `screenToFlowPosition()` used to place new nodes at the current viewport center

---

### `NodeEditor.jsx`
A right-side panel that opens when a node is selected in the graph editor.

**Features:**
- Edit node title, story text, ending flag
- Manage choices: add, remove, set label and target node
- Target dropdown includes a "Create a new node" option — creates the node and wires it as a target in one step
- Node ID is intentionally hidden from the UI

---

### `StoryReader.jsx`
Handles the full reading experience plus the visited-path graph.

**Reading flow:**
- Loads story from `localStorage` via `storyId` URL param
- Maintains a `history` stack for Go Back
- Detects ending nodes (`isEnding: true` or no choices)
- On reaching an ending, saves visited nodes via `mergeVisitedNodes()` to persist across sessions

**Visited path graph (`VisitedGraphFlow`):**
- Built with its own `ReactFlowProvider` (isolated to avoid shared state bugs)
- Uses `buildVisitedGraph()` with dagre `LR` layout by default
- Custom `VisitedNode` component with dynamic handle positions
- Color-coded: green = current turn, red = ending reached, grey = previous turns
- Toolbar: Align, Vertical/Horizontal toggle
- Enlarge button opens it in a full-screen modal
- Clicking any node jumps the reader back to that point in the story

**Previous Path modal:**
- Always-visible button during reading (when past visit data exists)
- Opens the same `VisitedGraphInner` in a full-screen modal

---

### `storage.js`

All localStorage interaction goes through this file.

| Function | Description |
|---|---|
| `getAllStories()` | Returns all stories from localStorage |
| `getStory(id)` | Returns a single story by ID |
| `saveStory(id, story)` | Saves/overwrites a story |
| `deleteStory(id)` | Removes a story |
| `generateId()` | Generates a unique story ID |
| `generateNodeId()` | Generates a unique node ID |
| `getVisitedNodes(storyId)` | Returns all historically visited node IDs for a story |
| `mergeVisitedNodes(storyId, newIds)` | Merges new node IDs into the persistent visited set |

Stories are stored under the key `cyoa-stories`. Visited nodes under `cyoa-visited`. Settings under `cyoa-theme` and `cyoa-edge-style`.

---

### `storyHelpers.js`

Pure functions for graph analysis. No side effects.

| Function | Description |
|---|---|
| `findReachable(story)` | BFS from startNode; returns Set of reachable node IDs |
| `findEndings(story)` | Returns IDs of ending nodes (isEnding or no choices) |
| `findOrphans(story)` | Returns IDs of nodes not reachable from start |
| `findDeadEnds(story)` | Returns non-ending nodes with no outgoing choices |
| `validateStory(story)` | Returns array of warning strings |
| `createEmptyStory(title)` | Returns a new story object with a single start node |

---

### `index.css`

Defines all design tokens as CSS custom properties on `:root` (dark mode) and overrides them under `[data-theme="light"]`.

**Key variables:** `--color-bg`, `--color-surface`, `--color-surface-2`, `--color-border`, `--color-text`, `--color-text-dim`, `--color-accent`, `--font-display`, `--font-ui`

Also contains all ReactFlow overrides:
- `.react-flow__controls-button` — styled for both dark and light mode
- `.react-flow__minimap` — theme-aware background
- `.react-flow__edge-text` — readable in light mode
- `.cyoa-toolbtn` — author toolbar button class, theme-aware

---

## Data Format

Every story is a plain JSON object:

```json
{
  "title": "Story Title",
  "author": "Author Name",
  "startNode": "node-id",
  "nodes": {
    "node-id": {
      "title": "Page Title",
      "text": "The story text for this page...",
      "choices": [
        { "text": "Choice label", "target": "another-node-id" }
      ],
      "isEnding": false
    }
  }
}
```

- `startNode` — the ID of the first node to display
- `isEnding: true` — marks a node as a terminal ending (no choices shown)
- Nodes with no choices and `isEnding: false` are treated as dead ends (flagged in graph)

---

## Theming Approach

Theme is applied by setting `data-theme="light"` on `document.documentElement`. All components use CSS variables exclusively — no hardcoded colors in component styles. ReactFlow's built-in styles are overridden in `index.css` using `!important` selectors scoped to `[data-theme="light"]` and the global scope for dark mode defaults.

---

## Graph Layout

Both the author graph and the reader path graph use **dagre** for automatic node positioning:

1. Story nodes are registered with dagre using fixed width/height
2. Edges are added based on story choices
3. `dagre.layout()` computes `(x, y)` for each node
4. Positions are passed directly to ReactFlow nodes

The **Align** button re-runs this process at any time, snapping manually moved nodes back to the computed layout.

---

## Deployment Notes

- Uses `HashRouter` so all routing works on static hosts (GitHub Pages, Netlify)
- `vite.config.js` sets `base: './'` for relative asset paths
- Build: `npm run build` → outputs to `dist/`
- No server, no API, no database — pure static site
