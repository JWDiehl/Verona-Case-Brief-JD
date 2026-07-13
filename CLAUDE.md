# Project context for Claude Code

Read this first, then `docs/bmad-roundtable.md` and `docs/data-model.md` for full depth before making any changes.

## What this project is

A board-level deliverable for a Slide3 internal training exercise: the "GE Verona" case (a fictional precision-pump/compressor manufacturer). The scenario file (`data/ge-verona-scenario.html`) gave a CEO mandate — lift **effective** (on-time-in-full) throughput 30% in 30 months, inside a $48M capital envelope, without breaching a 1.8% quality ceiling or a unit-cost index of 100 — plus a board split between two camps: "build a new plant" vs. "fix what we have." Slide3's job was to diagnose the true constraint and recommend a path, not referee the two camps.

This was built by running a structured four-persona ("BMAD": Business → Manufacturing/Ops → Analytics → Decision) roundtable, with an adversarial red-team pass against both camps, then packaging the result as an interactive HTML board brief with a constraint-chain drill-down explorer and a live lever simulator. A second BMAD pass (see `docs/bmad-roundtable.md`, "Round 2") later closed gaps found by reviewing the page against the original scenario brief — see below.

## The recommendation (already locked — don't re-litigate without new evidence)

Don't build a new plant, don't expand Plant 2. Pursue four sequenced levers — front-office/OTIF recovery, Plant 1 CNC debottleneck, Plant 2 changeover/downtime/quality fix, Plant 3 staffing & mix ramp — using $14.5–24M of the $48M envelope. This clears the 50,700-unit effective-throughput target with a ~906-unit (~1.8%) buffer, and improves both quality (1.80%→1.59%) and unit cost (~100.2→97.1) rather than merely holding them. Full reasoning and the red-team exchange are in `docs/bmad-roundtable.md`. Every number is traced to an assumption in `docs/data-model.md`.

**Known fragility, don't paper over it:** the ~1.8% buffer is not evenly distributed risk. Plant 3's staffing/mix ramp alone supplies over half the gross unit lift; if it alone fails to land, effective throughput is ~48,316 — a 4.7% miss on target, not a buffer erosion (see `docs/data-model.md`, "Downside case"). This is why Plant 3's hiring now starts in Wave 1, parallel with the front office, rather than waiting for Wave 2.

## Repo layout

```
site/index.html      ← the deliverable. Single self-contained HTML file, Slide3 light-theme brand
                        (Ivory/Olive/Periwinkle — see the :root CSS vars). Three interactive/dynamic
                        pieces: Section 03 is an expandable value-added-vs-friction drill-down tree
                        (DZ_DATA in the bottom <script>); Section 04 is the lever simulator (LEVERS
                        in the same <script>) that recomputes the throughput bridge, capital, quality,
                        and cost gauges live as levers are toggled — including a counter-banner
                        (#p3Note) that appears only when the Plant 3 lever is off, so the presenter
                        has a ready rebuttal if the room asks "why not just build a plant instead?";
                        one-click scenario presets (Today / Front-office-only / If-Plant-3-slips /
                        Recommended, in `#presetBtns`) sit above the simulator and flip all four
                        levers at once. Section 10 closes with a plain-English "bottom line" pull-quote
                        followed by an explicit "decision requested today" ask, rather than ending on
                        methodology. The page also has two presentation-polish touches: sections
                        fade/slide in on scroll (`.pre-reveal`/`.revealed`, progressive enhancement —
                        JS opts sections INTO the hidden state, so a JS failure leaves everything
                        visible) and the two headline throughput numbers in §01 count up on scroll
                        into view (`[data-countup]`). Both respect `prefers-reduced-motion`.
docs/bmad-roundtable.md  ← the 4-persona roundtable + red-team pass (the "why"), plus a "Round 2"
                           section documenting the later gap-closing pass and red-team ruling
docs/data-model.md       ← every number, traced to the scenario file (the "how"), including a
                           "Downside case" section for the Plant-3-fails stress test
data/ge-verona-scenario.html ← the original case prompt this brief answers (read-only reference)
```

## State / open items

- Pushed to GitHub: `https://github.com/JWDiehl/Verona-Case-Brief-JD` (`main` branch).
- The scenario email referenced a July 6 board meeting; that date had already passed as of when this was built. **The real presentation date is still unconfirmed** — `site/index.html` does not hardcode a date; check before presenting.
- Browser QA (Playwright/Chromium) has been run four times: original 8-section page; after the Section 03 drill-down explorer; after the "Round 2" gap-closing pass (business-case callout, corrected 1.8% buffer stat, reordered risk list, resequenced roadmap, §10 decision-ask, Plant-3-off counter-banner); and after the scenario-preset buttons, count-up numbers, and scroll-reveal animation were added — each time confirming render, gauge math, and interactivity with no console errors. The "If Plant 3 slips" preset was independently verified to reproduce the hand-calculated downside case exactly (48,316 units, "2384 short of target") in one click.
- If you change a lever assumption (capital, timing, produced-unit impact), edit the `LEVERS` array in `site/index.html`'s `<script>` tag and update the matching row in `docs/data-model.md` so the workpaper stays in sync with what the page computes. If you add a new lever combination worth demoing live, add it to `PRESETS` in the same `<script>`, not just as a manual toggle combo.
- Deferred to a future pass, not currently in the deck (see `docs/bmad-roundtable.md` Round 2 "overruled on scope"): a two-tranche split of the Plant 2 lever, actual framework-customer renewal dates + a customer-level KRI, and a standalone quality-hold-gate callout (folded into the existing Plant 2 risk line instead).

## Conventions

- Design system: Slide3 branded HTML (light theme) — see the `slide3-branded-html-v2` skill if you have it available; otherwise just match the existing CSS custom properties in `site/index.html`'s `:root`.
- Keep `site/index.html` a single self-contained file (no build step) — that's a deliberate constraint so it opens in any browser with zero setup.
