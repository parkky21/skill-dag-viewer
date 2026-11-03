# Skill DAG Viewer

**Live:** https://parkky21.github.io/skill-dag-viewer/ (once GitHub Pages is enabled)

Interactive skill DAGs (directed acyclic graphs) that run entirely in the browser. Ratings on the leaf skills roll up through the hierarchy as weighted Beta distributions.

| Page | What it does |
| --- | --- |
| [`index.html`](index.html) | **Excel → Skill DAG builder.** Upload any `.xlsx`, `.xls`, `.ods` or `.csv`, choose which column labels form the hierarchy (top to bottom), set edge weights, and build the graph. |
| [`ai-skill-dag.html`](ai-skill-dag.html) | Prebuilt DAG of the AI Skill Inventory v0.2 draft: AI → 7 clusters → 37 sub-clusters → 140 skills. |

No build step and no server: each page is a single HTML file. Spreadsheets are parsed locally and never uploaded anywhere.

## Run it

Open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

To publish with **GitHub Pages**: open the repository's Settings → Pages, set the source to *Deploy from a branch*, and choose `main` / root.

## Using the builder

1. Drop a spreadsheet on the page (or try the built-in sample from `sample/AI_Skill_Inventory_DRAFT_v0_2.xlsx`).
2. Check the sheet and header row; both are detected automatically.
3. Pick the hierarchy columns from top to bottom. The last level is the leaf you rate.
4. Optionally choose:
   - a leaf description column;
   - a starting-rating column (0–1, 0–5, 0–10 or 0–100 scales are detected);
   - an edge-weight column (numeric, or text categories with a weight per value);
   - which other columns to show in the inspector.
5. Review the preview (node counts per level, sample paths, warnings), then **Build DAG**.

Cleanup options:

- **Fill down** blank or merged cells. A cell is filled only when the row above has the same parents.
- **Merge identical labels** on the same level, which turns repeated labels into shared nodes.
- **Strip leading numbers** such as `1. `.
- **Split** cells on `,` `;` `|` so one row can feed several parents.

After building you can:

- switch between organic and tree layouts;
- search;
- adjust each leaf's rating and confidence;
- export scores to Excel (leaf ratings, node scores and edges);
- export or import ratings as JSON.

The last DAG and its ratings are kept in the browser's local storage.

## How scores propagate

- **Leaf:** rating *V* and confidence *C* define a Beta distribution with mean *V* and concentration *K* = 2 + 98·C².
- **Parent:**
  - **Belief *B*:** the weighted mean of its children.
  - **Neural *N*:** a sigmoid of the weighted sum.
  - **Confidence:** derived from the weighted variance, moment-matched back to a Beta distribution.
- **Edge weights:** normalised per parent.

## Credits

The graph engine, layout and Beta-propagation math are adapted from [parkky21/algojobdag](https://github.com/parkky21/algojobdag). Libraries load from CDNs:

- [vis-network](https://visjs.github.io/vis-network/) 9.1.9
- [SheetJS](https://sheetjs.com/) 0.18.5
- [Lucide](https://lucide.dev/) 0.469.0
