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

## The one assumption most worth pressure-testing

Plant 2's post-fix produced figure (15,900) sits ~2.5% above its stated nameplate (15,500). The model treats this as legitimate because nameplate figures in the scenario data are almost certainly derived from *observed* output under today's changeover/downtime regime — not a theoretical engineering maximum. A real SMED and reliability program should lift the practical ceiling by roughly the amount of time it recovers. This is flagged in the roundtable doc as the top candidate for a rapid time-and-motion study in Wave 0, before capital is committed to Wave 2.

## Simulator behavior when a lever is toggled off

- **OTIF lever off:** OTIF reverts to 81%, applied to whatever produced total is active.
- **Any plant lever off:** that plant's produced units, quality, and cost index revert to baseline; its capital and time-to-effect drop out of the totals.
- Capital, quality, cost, and the waterfall bar all recompute live — this is what lets the board ask "what if we skip Plant 3" and see the consequence immediately, in the room.
