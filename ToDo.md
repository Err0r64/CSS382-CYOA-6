# ToDo – Choose Your Own Adventure Project

## Phase 0: Setup (Do First)
- [ ] Fork the repo and add all team members as contributors
- [ ] Set up React + Vite project: `npm create vite@latest cyoa-app -- --template react`
- [ ] Install dependencies: `npm install reactflow tailwindcss @tailwindcss/vite`
- [ ] Verify local dev server runs: `npm run dev`
- [ ] Write a conversion script to turn `cot-pages-ocr-v2/*.txt` + `cot-story-graph.mmd` into a single `cave-of-time.json`
- [ ] Commit and push initial project scaffold

## Phase 1: Reader Mode (Core MVP)
- [ ] Create `StoryReader` component – displays current node's text
- [ ] Create `ChoiceButton` components – one per choice, navigates to target node on click
- [ ] Add "Restart Story" button
- [ ] Add "Go Back" button (maintain a history stack)
- [ ] Handle terminal nodes (no choices = "The End" message with restart option)
- [ ] Load `cave-of-time.json` as the default story
- [ ] Basic styling – readable text, clear buttons, centered layout

## Phase 2: Author Mode (Core MVP)
- [ ] Create `StoryEditor` component – lists all story nodes
- [ ] Create `NodeEditor` component – edit node text and choices (add/remove/modify)
- [ ] Add "Create New Node" functionality
- [ ] Add "Delete Node" with confirmation
- [ ] Import story from JSON file (file picker)
- [ ] Export current story to JSON file (download)
- [ ] Add "New Story" button to start from scratch

## Phase 3: Graph Visualization
- [ ] Integrate React Flow (or Cytoscape.js) to render story graph
- [ ] Nodes display page ID, edges show choice text
- [ ] Click a node to select it for editing (in author mode)
- [ ] Click a node to jump to it (in reader mode)
- [ ] Color-code: start node (green), terminal/ending nodes (red), normal nodes (default)
- [ ] Highlight current path in reader mode

## Phase 4: Integration & Polish
- [ ] Add Navbar with mode switcher (Reader / Author / Graph)
- [ ] Connect all three views through shared state (React context or prop drilling)
- [ ] Responsive layout (works on mobile for reader, desktop for author)
- [ ] Error handling for malformed JSON imports
- [ ] Clean up OCR noise in the Cave of Time demo data

## Phase 5: Deploy
- [ ] Build production bundle: `npm run build`
- [ ] Deploy to Netlify (connect GitHub repo) or GitHub Pages (`gh-pages` branch)
- [ ] Verify deployed URL works
- [ ] Update README.md with:
  - [ ] Deployed website URL
  - [ ] GitHub repository URL
  - [ ] Team member names (optional)

## Phase 6: Documentation
- [ ] Update Codebase.md to describe the web app architecture
- [ ] Ensure all team members have commits visible on GitHub
- [ ] Final review and cleanup

---

## Assignment Checklist (from instructions)
- [ ] All team members added to Canvas CYOA group
- [ ] Repo forked, all members are contributors
- [ ] Read Fork-Instructions.md ✅
- [ ] Read AI-Instructions.md ✅
- [ ] Read Codebase.md ✅
- [ ] Brainstorm.md created ✅
- [ ] ToDo.md created ✅
- [ ] Project extended and deployed on public website
- [ ] All members have commits on GitHub
- [ ] README.md includes deployed URL + repo URL
