# GE Verona Throughput Diagnostic — Board Recommendation

Interactive board-level deliverable for the GE Verona training case: a constraint diagnosis and recommendation for lifting effective throughput 30% in 30 months, built through a four-persona ("BMAD") roundtable.

## What's in this repo

```
ge-verona-case-brief/
├── site/
│   └── index.html          ← the deliverable. Single self-contained file — open it in any browser.
├── docs/
│   ├── bmad-roundtable.md   ← the four-persona roundtable (Business/Manufacturing/Analytics/Decision) + red-team pass
│   └── data-model.md        ← every number in the interactive simulator, traced back to an assumption
└── data/
    └── ge-verona-scenario.html   ← the original scenario file this brief is built from (reference copy)
```

## Opening it

**Quickest:** just open `site/index.html` in a browser. It's fully self-contained — no build step, no server, no dependencies except a Google Fonts import.

**In VSCode:** open this folder, install the **Live Server** extension, right-click `site/index.html` → "Open with Live Server" for auto-reload while you edit.

## The two interactive centerpieces

**Section 03 — "Explore the constraint chain"**: an expandable tree (front-office stages / plants) where every row breaks into value-added vs. friction, with toggleable legend chips and live search. Click a branch open, isolate "friction" to see where the losses concentrate, or search a plant name. (The interaction pattern — expand-in-place tree, stacked composition bar, isolate-on-click legend chips — was adapted from an unrelated internal org-exploration tool; none of that tool's data is used here.)

**Section 04 — "The interactive throughput bridge"**: a live simulator — toggle any of the four recommended levers off and the projected throughput, capital spend, quality rate, and unit-cost index all recompute in real time. This is meant to be used live in the board meeting: if someone asks "what if we don't fund Plant 3 staffing," toggle it off and show them.

Both are fully documented in `docs/data-model.md` — nothing in either is a black box.

## Pushing to GitHub

This folder is already a git repo with an initial commit (see below). There's no GitHub connector wired up in this session, so push it yourself:

```bash
cd ge-verona-case-brief
git remote add origin https://github.com/<your-username>/<repo-name>.git
git branch -M main
git push -u origin main
```

(Create the empty repo on GitHub first — no README/license, since this repo already has one — then run the commands above.)

## Editing the analysis

- Change a lever's assumptions (capital, timing, produced-unit impact) in the `LEVERS` array inside the `<script>` tag at the bottom of `site/index.html` — the simulator, gauges, and waterfall all read from that one array.
- Update the corresponding row in `docs/data-model.md` so the workpaper stays in sync with what the page actually computes.
- The `test_model.js`-style checks used to validate this model (nameplate ceilings, quality/cost blends, capital sums) are described in `docs/data-model.md` — re-run the arithmetic there if you change an assumption.
