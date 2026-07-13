# Data model & math behind the interactive brief

This documents every number the simulator in `site/index.html` uses, so it's auditable and easy to update in VSCode if the team wants to change an assumption. The live JS model lives in `<script>` at the bottom of `index.html` under `const LEVERS` and `function computeState()` — the numbers below match it exactly.

## Baseline (from the scenario file, unchanged)

| Metric | Value |
|---|---|
| Units produced / yr | 48,000 |
| OTIF (on-time-in-full) today | 81% |
| Effective throughput today | 39,000 (case's own stated figure) |
| +30% target (effective) | 50,700 |
| Gap to close | 11,700 |
| Capital envelope | $48M |
| Quality ceiling | ≤1.8% field-return |
| Cost ceiling | ≤100 unit-cost index |

Per-plant baseline:

| Plant | Produced | Nameplate | OEE | Quality | Cost index |
|---|---|---|---|---|---|
| Plant 1 (Verona) | 23,400 | 26,000 | 71% | 1.5% | 94 |
| Plant 2 (Brescia) | 14,800 | 15,500 | 64% | 2.4% | 108 |
| Plant 3 (Padua) | 9,800 | 17,000 | 52% | 1.6% | 103 |

## The four levers (each independently toggleable in the simulator)

**1. Front-office / OTIF recovery** — order-management redesign + engineering triage + faster batch cadence.
- Effect: raises OTIF from 81% → 94% (the case's own Table 7.1 figure). Does not change produced units on its own.
- Capital: $4.5–8M (order-mgmt system $4–7M + engineering triage $0.5–1M). Time to effect: 3–14 months.

**2. Plant 1 — CNC debottleneck**
- Effect: OEE 71% → ~82% translates to +2,300 produced units/yr (25,700 of 26,000 nameplate — tight but fits).
- Quality/cost shift: 1.5%→1.4%, cost index 94→92 (fewer changeovers = lower cost per unit).
- Capital: $6–9M. Time to effect: 6–10 months.

**3. Plant 2 — changeover/downtime/quality fix (explicitly NOT an expansion)**
- Effect: OEE 64% → ~70% via SMED changeover reduction and downtime reduction translates to +1,100 produced units/yr (15,900 vs. 15,500 nameplate — a ~2.5% stretch; see assumption note below).
- Quality/cost shift: 2.4%→1.9%, cost index 108→102.
- Capital: $1–2M (company-wide SMED program, majority weighted to Plant 2). Time to effect: 9–15 months.

**4. Plant 3 — staffing & mix ramp**
- Effect: reducing the 22% skilled-labor vacancy toward ~10% and converting part of the plant's configuration toward standard-product work adds +3,500 produced units/yr (13,300 of 17,000 nameplate — comfortable headroom remains).
- Quality/cost shift: held flat (1.6%, cost index 103→101 from better fixed-cost absorption).
- Capital: $3–5M. Time to effect: 9–15 months.

## Combined effect (all four levers on — the recommended path)

```
produced_total = 48,000 + 2,300 + 1,100 + 3,500 = 54,900   (nameplate sum = 58,500 → fits)
effective      = 54,900 × 0.94 = 51,606
vs. target 50,700 → +906 buffer (~1.8%)

capital        = $14.5M (low case) to $24M (high case) of $48M envelope
reserve        = $24–33.5M unused

quality blend  = (25,700×1.4 + 15,900×1.9 + 13,300×1.6) / 54,900 = 1.59%   (ceiling ≤1.8% ✓)
cost blend     = (25,700×92 + 15,900×102 + 13,300×101) / 54,900 = 97.1     (ceiling ≤100 ✓)
```

## Downside case: what if Plant 3 alone misses its ramp?

The headline buffer (+906 units, ~1.8%) looks like margin against the 50,700 target — but it isn't evenly distributed risk. Plant 3 supplies 3,500 of the plan's 6,900-unit gross lift (over half) and has the longest time-to-effect of any lever (9–15 months, tied with Plant 2). If the other three levers land on time but Plant 3's staffing/mix ramp does not:

```
produced_total = 25,700 (P1, on) + 15,900 (P2, on) + 9,800 (P3, baseline — unchanged) = 51,400
effective      = 51,400 × 0.94 = 48,316
vs. target 50,700 → −2,384 units (−4.7% miss, not a buffer erosion)

quality blend  = (25,700×1.4 + 15,900×1.9 + 9,800×1.6) / 51,400 = 1.59%   (essentially unchanged — P3's quality effect was flat)
cost blend     = (25,700×92 + 15,900×102 + 9,800×103) / 51,400 = 97.19    (slightly worse — P3's cost improvement is lost)
```

Plant 3 is narratively framed as "comfortable headroom" (78% utilized even after the ramp) while the risk-flag language in the roundtable went to Plant 2's smaller nameplate stretch — but on the numbers, Plant 3 is the plan's actual single point of failure. This is why Plant 3's hiring now starts in Wave 1, parallel with the front-office rollout, rather than waiting for Wave 2: labor lead times are the longest pole in the plan, so the riskiest lever gets the longest runway to recover if it slips.

## The one assumption most worth pressure-testing

Plant 2's post-fix produced figure (15,900) sits ~2.5% above its stated nameplate (15,500). The model treats this as legitimate because nameplate figures in the scenario data are almost certainly derived from *observed* output under today's changeover/downtime regime — not a theoretical engineering maximum. A real SMED and reliability program should lift the practical ceiling by roughly the amount of time it recovers. This is flagged in the roundtable doc as the top candidate for a rapid time-and-motion study in Wave 0, before capital is committed to Wave 2.

## Simulator behavior when a lever is toggled off

- **OTIF lever off:** OTIF reverts to 81%, applied to whatever produced total is active.
- **Any plant lever off:** that plant's produced units, quality, and cost index revert to baseline; its capital and time-to-effect drop out of the totals.
- Capital, quality, cost, and the waterfall bar all recompute live — this is what lets the board ask "what if we skip Plant 3" and see the consequence immediately, in the room.

## Section 03 — the drill-down "value-added vs. friction" explorer

A separate, exploratory view of the same evidence in §02, styled as an expandable tree (front-office stages under one branch, plants under another). Every node reduces to two numbers that sum to 100%: **value-added** (real work / effective output) and **friction** (queue, rework, downtime, changeover, quality loss).

**Front-office branch** — friction = the scenario's own cited "% in queue/rework" per stage; value-added = the remainder:

| Stage | Elapsed | Value-added | Friction |
|---|---|---|---|
| Order entry & configuration | 3.5 wk | 4% | 96% |
| Engineering & drawing release | 2.0 wk | 11% | 89% |
| Material & scheduling | 1.8 wk | 8% | 92% |
| **Front office (weighted)** | **7.3 wk** | **6.9%** | **93.1%** |

**Shop-floor branch** — value-added = OEE at the plant's bottleneck cell (used as a proxy for the whole plant's effectiveness); friction = 100 − OEE:

| Plant | Units/yr | Value-added (OEE) | Friction |
|---|---|---|---|
| Plant 1 — Verona (CNC) | 23,400 | 71% | 29% |
| Plant 2 — Brescia (Final test) | 14,800 | 64% | 36% |
| Plant 3 — Padua (Assembly) | 9,800 | 52% | 48% |
| **Shop floor (weighted by units)** | **48,000** | **65.0%** | **35.0%** |

Front office (7.3 wk) + shop floor (6.9 wk, from the value-stream flow: machining 3.2 + assembly 2.4 + ship 1.3) = 14.2 wk, matching the case's stated average lead time exactly — a useful internal consistency check.

This section's interaction pattern (expand-in-place tree, stacked composition bar, toggleable legend chips, live search) was adapted from an unrelated internal tool a colleague built for org/headcount exploration — only the UI pattern was reused; none of that tool's data or domain is part of this brief.
