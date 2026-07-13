# Project context for Claude Code

Read this first, then `docs/bmad-roundtable.md` and `docs/data-model.md` for full depth before making any changes.

## What this project is

A board-level deliverable for a Slide3 internal training exercise: the "GE Verona" case (a fictional precision-pump/compressor manufacturer). The scenario file (`data/ge-verona-scenario.html`) gave a CEO mandate — lift **effective** (on-time-in-full) throughput 30% in 30 months, inside a $48M capital envelope, without breaching a 1.8% quality ceiling or a unit-cost index of 100 — plus a board split between two camps: "build a new plant" vs. "fix what we have." Slide3's job was to diagnose the true constraint and recommend a path, not referee the two camps.

This was built by running a structured four-persona ("BMAD": Business → Manufacturing/Ops → Analytics → Decision) roundtable, with an adversarial red-team pass against both camps, then packaging the result as an interactive HTML board brief with a constraint-chain drill-down explorer and a live lever simulator.

## The recommendation (already locked — don't re-litigate without new evidence)

Don't build a new plant, don't expand Plant 2. Pursue four sequenced levers — front-office/OTIF recovery, Plant 1 CNC debottleneck, Plant 2 changeover/downtime/quality fix, Plant 3 staffing & mix ramp — using $14.5–24M of the $48M envelope. This clears the 50,700-unit effective-throughput target with a ~900-unit buffer, and improves both quality (1.80%→1.59%) and unit cost (100.2→97.1) rather than merely holding them. Full reasoning and the red-team exchange are in `docs/bmad-roundtable.md`. Every number is traced to an assumption in `docs/data-model.md`.

## Repo layout

```
site/index.html      ← the deliverable. Single self-contained HTML file, Slide3 light-theme brand
                        (Ivory/Olive/Periwinkle — see the :root CSS vars). Two interactive pieces:
                        Section 03 is an expandable value-added-vs-friction drill-down tree (DZ_DATA
                        in the bottom <script>); Section 04 is the lever simulator (LEVERS in the
                        same <script>) that recomputes the throughput bridge, capital, quality, and
                        cost gauges live as levers are toggled.
docs/bmad-roundtable.md  ← the 4-persona roundtable + red-team pass (the "why")
docs/data-model.md       ← every number, traced to the scenario file (the "how")
data/ge-verona-scenario.html ← the original case prompt this brief answers (read-only reference)
```

## State / open items

- Pushed to GitHub: `https://github.com/JWDiehl/Verona-Case-Brief-JD` (`main` branch).
- The scenario email referenced a July 6 board meeting; that date had already passed as of when this was built. **The real presentation date is still unconfirmed** — `site/index.html` does not hardcode a date; check before presenting.
- Browser QA (Playwright/Chromium) has been run twice: once on the original 8-section page (theme, layout, and the lever simulator all checked out — flipping all 4 LEVERS off correctly dropped the gauges to baseline 38,880 units/$0 capital/1.80% quality/100.2 cost index with the ceiling-breach warning firing, and back to 51,606/$14.5–24.0M/1.59%/97.1 with no console errors) and again after the Section 03 drill-down explorer was added, to confirm the new section renders and the renumbered sections (04-09) and existing simulator still work post-merge.
- If you change a lever assumption (capital, timing, produced-unit impact), edit the `LEVERS` array in `site/index.html`'s `<script>` tag and update the matching row in `docs/data-model.md` so the workpaper stays in sync with what the page computes.

## Conventions

- Design system: Slide3 branded HTML (light theme) — see the `slide3-branded-html-v2` skill if you have it available; otherwise just match the existing CSS custom properties in `site/index.html`'s `:root`.
- Keep `site/index.html` a single self-contained file (no build step) — that's a deliberate constraint so it opens in any browser with zero setup.
