# BMAD Roundtable — GE Verona Throughput Diagnostic

**Engagement:** GE Verona Throughput Diagnostic · Board Recommendation
**Method:** Four-persona roundtable (Business → Manufacturing/Ops → Analytics → Decision), followed by an adversarial red-team pass before the recommendation was locked.
**Status:** Complete — feeds `site/index.html`

This document is the workpaper behind the board deliverable. It exists so anyone on the team (or the board, if asked) can trace every number in the interactive brief back to a stated assumption. Nothing in the deliverable should be un-auditable.

---

## Why BMAD, adapted

BMAD normally orchestrates a software build through specialist agents that hand off in sequence. There's no code being shipped here, but the same discipline — don't let one voice (or one camp) own the diagnosis — is exactly what this case demands. The board is split into Camp A ("build") and Camp B ("fix"); the brief explicitly warns against "a vote for whichever camp is louder." So the roundtable is run as four distinct lenses, each required to hand off a specific artifact to the next:

| Persona | Owns | Hands off |
|---|---|---|
| **B — Business** | Frames the economic stakes: is 30% actually the right target, and against what demand? | A validated problem statement and demand ceiling |
| **M — Manufacturing / Ops (constraint diagnosis)** | Runs Theory of Constraints + OEE decomposition across the three plants and the front office | A ranked list of where throughput is actually lost |
| **A — Analytics** | Turns the diagnosis into a quantified throughput bridge, capital sizing, and guardrail math | A numbers-checked model, stress-tested against nameplate ceilings |
| **D — Decision** | Red-teams both camps against the model, sequences the path, and writes the board recommendation | The final memo + risk/KRI list |

---

## B — Business: is 30% the right target, against real demand?

Order intake is up ~9% YoY, backlog has grown from 6 to 11 weeks, and lost-order analysis pins ~$40M/yr in revenue on lead-time misses. Two framework customers (~18% of revenue) have tied renewal to delivery recovery — this is not a hypothetical growth story, it's retention risk on committed revenue.

Critically, the CEO's target is **effective** throughput (on-time-in-full units), not raw production. Today: 48,000 units produced, 81% OTIF → ~39,000 effective. Target: ~50,700 effective (+30%). The demand side checks out too — "demand at competitive lead time" is estimated at ~52,000 units, comfortably above the 50,700 target, so this isn't a case of manufacturing more than the market wants.

**Handoff to M:** the gap to close is 11,700 effective units. Don't assume all of it has to come from producing more — some of it may already be sitting in the 81%→94% OTIF gap.

---

## M — Manufacturing/Ops: where is the system actually constrained?

Applying Theory of Constraints (identify → exploit → subordinate → elevate) and OEE decomposition (Availability × Performance × Quality) across the value stream:

**Front office (7.3 of 14.2 weeks, ~51% of lead time).** Touch time across order entry, engineering, and scheduling is only ~16 hours total. Elapsed time is 7.3 weeks (~292 working hours at a 40-hr week). That means roughly **95% of front-office lead time is queueing, not work** — driven by a shared 11-engineer pool with no triage between standard and custom orders, a weekly batch scheduling cutoff, and 6+ handoffs with no single order owner. This is a policy constraint, not a capacity wall — cheap and fast to fix.

**Plant 1 (Verona) — 90% utilized, CNC bottleneck at 71% OEE.** Near nameplate (23,400 of 26,000), so there's little slack in raw hours — but a 29-point OEE gap at the bottleneck cell means real throughput is being lost to changeovers (95 min, 6/day) and downtime (8%) before any new equipment is needed.

**Plant 2 (Brescia) — reported 95% utilized, but this is the case's central trap.** Worst OEE (64%), worst downtime (15%), longest changeovers (140 min), worst quality (2.4% — already breaching the 1.8% ceiling on its own), worst unit cost (108). "95% utilized" is measuring gross time, not availability-adjusted capacity. **Camp A's instinct to expand this plant would pour $28–34M into the least-capable, highest-risk asset in the network.** The data says fix it before you scale it.

**Plant 3 (Padua) — 58% utilized, but the constraint is people, not machines.** 7,200 units of nameplate headroom sit behind a 22% skilled-labor vacancy rate, and the plant is configured mostly for custom/mixed work rather than the standard-product backlog that's actually backed up. The lever here is staffing and mix, not new floor space.

**Finding handed to A:** there is no single physical bottleneck. There's a chain of four fixable friction points — one policy (front office), two OEE gaps (Plant 1, Plant 2), and one staffing/mix mismatch (Plant 3) — and a real risk that fixing the front office alone just relocates the queue onto Plant 2 if Plant 2 isn't fixed in parallel.

---

## A — Analytics: the quantified throughput bridge

All figures below are derived from the scenario file's own numbers and cross-checked against nameplate ceilings so nothing implies conjuring capacity that isn't physically there. Full arithmetic in `data-model.md`; summary:

| Step | Lever | Produced units | Effective throughput (after OTIF) | Running total |
|---|---|---|---|---|
| 0 | Baseline | 48,000 | 39,000 | 39,000 |
| 1 | Front-office / OTIF recovery (81%→94%) | 48,000 (unchanged) | 45,120 | **+6,120** |
| 2 | Plant 1 CNC debottleneck (OEE 71%→~82%) | +2,300 → 25,700 | 47,282 | **+2,160** |
| 3 | Plant 2 changeover/downtime/quality fix (OEE 64%→~70%, no expansion) | +1,100 → 15,900 | 48,316 | **+1,030** |
| 4 | Plant 3 staffing & mix ramp (vacancy 22%→~10%) | +3,500 → 13,300 | 51,606 | **+3,290** |
| **Total** | | 54,900 (vs. 58,500 nameplate — fits) | **51,606** | **vs. 50,700 target: +900 buffer** |

**Nameplate check:** Plant 1 lands at 25,700 of 26,000 nameplate (tight, as expected — the case flags "little obvious slack unless OEE is the lever"). Plant 2 lands at 15,900 against a stated 15,500 nameplate — a ~2.5% stretch, which we treat as a testable assumption (today's nameplate figure likely already embeds current changeover/downtime losses; a real SMED and reliability program should lift the practical ceiling by roughly that much). This is flagged as the single assumption in the model most worth validating with a rapid time-and-motion study before committing capital. Plant 3 lands at 13,300 of 17,000 — comfortable headroom remains even after the ramp.

**Capital:** the four levers total **$14.5–24M** of the $48M envelope — leaving **$24–33.5M in reserve**, unused by either Camp A's $60–75M greenfield plant (which exceeds the envelope on its own) or the $28–34M Plant 2 expansion.

**Quality:** blended field-return rate improves from 1.80% today to ~1.59% under the plan (Plant 2's quality problem is fixed rather than scaled up) — comfortably inside the ≤1.8% ceiling, with margin.

**Unit cost:** blended cost index improves from ~100.2 today to ~97.1 — better utilization spreads fixed cost across more units, and Plant 2's cost problem is addressed directly rather than diluted across a bigger footprint.

**Handoff to D:** the model shows the +30% target is achievable through recoverable throughput and targeted staffing — without a new plant and without expanding Plant 2 — with a ~1.8% buffer and a large unused capital reserve.

---

## D — Decision: red-team, then recommend

Before locking the recommendation, we stress-tested it from both camps' point of view.

**Camp A's challenge:** "You're assuming Plant 2's OEE recovery is achievable and that the nameplate stretch is real. If it isn't, you're short on capacity with no plant and no time left to build one."
**Response:** This is exactly why the plan sequences Plant 2's fix early (Wave 1–2, not Wave 4) with a month-6 checkpoint on changeover time and downtime — if the OEE recovery isn't tracking, there are 18+ months left to redirect a portion of the $24–33.5M reserve into a smaller, targeted capital add (not a full greenfield plant) rather than discovering the shortfall at month 28.

**Camp B's challenge:** "You're relying on Plant 3 retraining and mix conversion — the case flags that Plant 3's configuration doesn't match the backed-up standard-product demand. What if you can't convert it fast enough?"
**Response:** Correct, and this is the single largest single-lever contribution (+3,290 effective units) — the highest-risk element of the plan. It's sequenced with a 9–15 month runway and its own leading indicator (vacancy rate trend), and the plan still clears the target with only a ~900-unit buffer if it lands, so this is the lever the board should ask about most at month 6.

**Both camps agree, once shown the model:** the board's binary framing (build vs. fix) was a false choice. The data supports neither pure camp — it supports a sequenced, mostly-process path with staffing investment, funded well inside the envelope, with the reserve held for contingency rather than committed to a plant the constraint analysis doesn't support.

### Final recommendation

**Do not build a fourth plant. Do not expand Plant 2.** Pursue a four-lever, 30-month operational path — front-office/OTIF recovery, Plant 1 debottleneck, Plant 2 friction fix, Plant 3 staffing and mix ramp — using $14.5–24M of the $48M envelope, holding $24–33.5M in reserve for contingency. This clears the +30% effective throughput target with a small buffer, improves quality and unit cost rather than merely holding them, and is fully reversible/adjustable at the month-6 checkpoint if any single lever underperforms.

Full sequencing, risks, and month-6 KRIs are in the interactive brief (`site/index.html`, sections 06–08).

---

## Round 2 — closing the gaps (post-launch review)

After the board page shipped, a review against the original scenario brief found one substantive gap and two copy bugs: the demand-side business case above (§B) never made it onto the actual board page; the "~2% buffer" language (here and on the page) was a rounding error — 906/50,700 is 1.8%, not 2%; and the hero line's "five-step" wording named only four items. A second pass — the same four personas, run independently, plus an adversarial red-team ruling — closed these out before touching `site/index.html`.

**B — Business (re-pass):** the $40M/yr revenue-at-risk and the two framework-customer renewals tied to OTIF recovery are real stakes that belong on the page itself, not just in this workpaper — a board approving $14.5–24M should see what's at risk in dollars, not just OEE points. Also flagged: the mix/margin tension (38% custom work re-clogs the same queue the front-office fix clears, absent a routing rule favoring standard orders through recovered capacity) and a timing risk (if either customer's renewal decision lands before month 6–9, the fix won't have proven out yet).

**M — Ops (re-pass):** two findings worth keeping. First, Plant 2's nameplate isn't a hard physical ceiling — it was rated under today's 140-minute-changeover, 15%-downtime regime, so a modest stretch above it is a fair assumption once those inputs change, not a red flag on its own (validate with the Wave 0 time-motion study as already planned). Second, and more important: Plant 3's ramp is gated by hiring/training, which has a longer lead time than any capital fix in this plan — it should start immediately, in parallel with the front-office rollout, not wait for Wave 2. Waiting costs the plan its longest lever the most time.

**A — Analytics (re-pass, critical finding):** re-ran the full model. The 1.59% quality blend and 97.1 cost index both check out exactly. But the "~2% buffer" framing hid something: if Plant 3's lever alone fails to land — the other three succeed — produced total is 51,400 and effective throughput is 51,400 × 0.94 = **48,316, a 4.7% miss on the 50,700 target**, not a buffer erosion. Plant 3 supplies 3,500 of the plan's 6,900-unit gross lift (over half) and carries the longest time-to-effect (tied with Plant 2 at up to 15 months) — it is the plan's real single point of failure, even though it was narratively framed as "comfortable headroom" (78% utilization even after the ramp) while the risk-flag language went to Plant 2's smaller nameplate stretch instead. The board page understated this.

**D — Decision (re-pass):** the deliverable ended on a Methodology section — an audit footnote, not something a board votes on. There was no explicit statement of what's being approved *today* versus deferred to month 6. Separately: toggling Plant 3 off in the live simulator produced generic copy ("this is the size of the gap a capital option would need to close") that hands Camp A a talking point mid-meeting with no rebuttal on the page.

### Red-team ruling

**Camp A's steelman, reheard:** "If 51% of the lift rides on one lever — hiring and mix conversion — where's the hedge?" **Ruling: the recommendation holds. No plant, no expansion.** A new plant takes years and capital far beyond the $24–33.5M reserve; it cannot close a gap that opens at month 6–9. The real hedge already exists — redirect reserve capital toward accelerating Plant 1/Plant 2 further if Plant 3 slips. Camp A's actual ask isn't a plant, it's that the plan stop understating Plant 3's risk. Granted.

**Camp B, overruled on scope:** several proposed additions were cut as too granular for a one-sitting board page — a two-tranche split of the Plant 2 lever, a request for actual customer renewal dates and a full account-level KRI system (real, but a data-gathering action item, not page content), and a standalone quality-hold-gate callout (folded into the existing Plant 2 risk line instead).

**Locked changes made to `site/index.html`:** (1) corrected the buffer stat to 1.8%; (2) reordered §08's risk list so Plant 3's quantified downside (48,316 / −4.7%) leads, with Plant 2's nameplate stretch second; (3) moved Plant 3 hiring to start in Wave 1, parallel with the front office, in the §06 roadmap; (4) added a "Decision requested today" closing section so the page ends on an explicit ask, not methodology; (5) added a counter-banner in the live simulator that appears only when Plant 3 is toggled off, naming the gap as people-and-mix, not space. Plus three free edits: the recommendation's column is now labeled "Fix, verify, then decide"; the OEE glossary entry now clarifies it's a diagnostic ratio, not a multiplier that derives produced units; and a compact demand/business-case callout (the $40M revenue risk, the two customer renewals, and the ~52,000-unit demand ceiling vs. the 50,700 target) now sits near the top of §01.
