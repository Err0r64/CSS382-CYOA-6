# Choose Your Own Adventure — CSS 382 Group 6

**Live Site:** https://idyllic-sunshine-7221b8.netlify.app/

**GitHub Repository:** https://github.com/Err0r64/CSS382-CYOA-6

**Team Members:** Alec Situ, Hyobin Yook 

Created for the "Choose Your Own Adventure Maker" assignment in CSS 382 A *Introduction To Artificial Intelligence*, with Dr. Yusuf Pisan, Spring 2026.

---

## About

A web-based Choose Your Own Adventure tool with two modes:

- **Author Mode** — Create and edit branching stories using an interactive graph editor
- **Reader Mode** — Browse and play through available stories

Built with React + Vite, React Flow, and Dagre. No backend — all data stored in the browser.

See `Codebase.md` for full architecture documentation.

---

## Running Locally

The latest version of the web app is in `CYOA-Src-Files-v3/`.

```bash
cd CYOA-Src-Files-v3
npm install
npm run dev
```

Then open http://localhost:5173 in your browser.

**Other commands:**

```bash
npm run build      # build for production (outputs to dist/)
npm run preview    # preview the production build locally
npm run lint       # run ESLint
```

---

## Build Story Graph

Run:

```bash
python3 scripts/build_story_graph.py \
	--pages-dir output/cot-pages-ocr-v2 \
	--output output/cot-story-graph.mmd
```

Generated output:

- `output/cot-story-graph.mmd`: Mermaid graph of branching story transitions from the corrected OCR v2 page set

## Generate All Story Variants

Run:

```bash
python3 scripts/write_all_stories.py
```

Generated outputs:

- `output/cot-stories/story-0001.txt` (and additional numbered files): one complete path per file
- `output/cot-stories/manifest.json`: index of generated story files and page paths

Optional flags:

```bash
python3 scripts/write_all_stories.py \
	--graph output/cot-story-graph.mmd \
	--pages-dir output/cot-pages-ocr-v2 \
	--output-dir output/cot-stories \
	--start-page 2 \
	--max-decisions 20
```

## Re-Extract From Spread-Scanned PDF

The book scan is a two-page spread layout. The story starts on the left side of PDF page 8:

- PDF page 8 -> story pages 2 and 3
- PDF page 9 -> story pages 4 and 5

Run:

```bash
python3 scripts/reextract_cot_ocr_split.py \
	--pdf samples/the-cave-of-time.pdf \
	--pdf-start-page 8 \
	--pdf-end-page 66 \
	--story-start-page 2 \
	--output-dir output/cot-pages-ocr-v2
```

Generated output:

- `output/cot-pages-ocr-v2/*.txt`: OCR re-extraction using left/right half-page splitting
