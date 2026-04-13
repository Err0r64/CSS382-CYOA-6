# Brainstorm – Choose Your Own Adventure Authoring Tool

## Project Summary

Build a web-based tool with two modes:

1. **Author Mode** – Create and manage branching CYOA stories (nodes, choices, graph visualization)
2. **Reader Mode** – Play through a CYOA story by making choices at each branch point

The existing repo already has "The Cave of Time" extracted into text pages + a Mermaid story graph. We need to turn that into a web experience and generalize it so authors can create new stories.

---

## Key Design Decisions

### Tech Stack (Recommended)

- **React + Vite** – fast setup, component-based, easy to split work across team members
- **React Flow or Cytoscape.js** – interactive graph visualization for the story map
- **Tailwind CSS** – rapid styling without writing custom CSS
- **Deployment**: Netlify (drag-and-drop deploy) or GitHub Pages (via `gh-pages` branch)

### Data Format

- All story data stored as **JSON** — no backend required
- Each story is a JSON file with nodes (story segments) and edges (choices)
- Example structure:

```json
{
  "title": "The Cave of Time",
  "startNode": "page-2",
  "nodes": {
    "page-2": {
      "text": "You've hiked through Snake Canyon once before...",
      "choices": [
        { "text": "Start back home", "target": "page-4" },
        { "text": "Wait until morning", "target": "page-5" }
      ]
    }
  }
}
```

- The existing `cot-pages-ocr-v2/` text files + `cot-story-graph.mmd` can be converted to this JSON format with a script

### No Backend Needed

- Author mode: edit story JSON in browser, export/import as `.json` files
- Reader mode: load JSON and navigate through it
- This keeps deployment dead simple (static site)

---

## Feature Ideas

### MVP (Must Have)

- [ ] Reader: display story text, show choice buttons, navigate the story
- [ ] Reader: "restart" and "go back" buttons
- [ ] Author: add/edit/delete story nodes (text + choices)
- [ ] Author: visual story graph showing all nodes and connections
- [ ] Author: click a node on the graph to edit it
- [ ] Import/export story as JSON
- [ ] Pre-load "The Cave of Time" as the demo story
- [ ] Deploy to public URL

### Nice to Have (If Time Allows)

- [ ] Author: highlight unfinished nodes (nodes with no choices = dead ends)
- [ ] Author: highlight orphaned nodes (not reachable from start)
- [ ] Author: drag-and-drop graph layout
- [ ] Reader: story progress tracker / breadcrumbs
- [ ] Reader: "show all endings" summary
- [ ] Multiple stories / story selector
- [ ] Author: color-code terminal vs. branch vs. merge nodes
- [ ] Dark mode toggle

### Stretch Goals

- [ ] AI-assisted story generation (use Claude API to suggest continuations)
- [ ] Collaborative editing (would need a backend)
- [ ] Audio narration
- [ ] Animated transitions between story pages

---

## How to Split Work (3 Team Members)

### Option A – By Feature

- **Person 1**: Reader mode (story display, navigation, UI)
- **Person 2**: Author mode (node editor, CRUD operations, import/export)
- **Person 3**: Graph visualization + data conversion script + deployment

### Option B – By Layer

- **Person 1**: Data layer (JSON schema, conversion script, state management)
- **Person 2**: UI/UX (components, styling, layout, responsive design)
- **Person 3**: Graph visualization + integration + deployment

### Key Coordination Rule

Everyone works on separate files/components to minimize merge conflicts. Use feature branches and pull requests.

---

## Architecture Overview

```
src/
├── components/
│   ├── Reader/
│   │   ├── StoryReader.jsx       # Main reader view
│   │   ├── StoryNode.jsx         # Displays text + choices
│   │   └── ChoiceButton.jsx      # Individual choice button
│   ├── Author/
│   │   ├── StoryEditor.jsx       # Main editor view
│   │   ├── NodeEditor.jsx        # Edit a single node
│   │   └── ImportExport.jsx      # Load/save JSON
│   ├── Graph/
│   │   └── StoryGraph.jsx        # Interactive graph visualization
│   └── Layout/
│       ├── Navbar.jsx            # Top nav with mode switcher
│       └── App.jsx               # Root component
├── data/
│   └── cave-of-time.json         # Pre-loaded demo story
├── utils/
│   └── storyHelpers.js           # Validation, graph analysis helpers
└── main.jsx
```

---

## Open Questions

- Do we want the author to be able to create stories from scratch, or only edit existing ones?
-> from scratch
- Should the reader show a mini-map of where they are in the story graph?
-> let's not to make things easier
- How polished does the Cave of Time data need to be (OCR has some noise)?
-> this is already done for us
