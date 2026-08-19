# 05 — Canonical Backlog

> **SINGLE SOURCE OF TRUTH.** This file (#05) is the canonical backlog. NEVER hand-edit the Jira import markdown table (09-jira-import-table.md) or CSV (10-jira-import.csv). Those two files are MECHANICAL DERIVATIONS produced by walking the rows of THIS file. Any change to a task, Epic, field, label, status, ordering, or any other property — edit it HERE. Then regenerate the derivation files. The coverage audit's 3-way consistency check will enforce alignment.

---

# 🔐 Stable ID Registry — FROZEN

**Assigned once on 2026-08-19. Effective immediately.**

- **AF-001 … AF-010** = 10 Epics (defined in this document, §1 below).
- **Tasks begin at AF-011.**
- **Never renumber.** Never reuse. Never delete.
- **To mark a task obsolete/skipped:** DO NOT remove it. Set `Conditional: yes` + write a `Skip condition:` explaining why, AND add `Labels: blocked conditional`.
- **To split a task that turns out too large during execution:** keep the old AF-###, split the remaining sub-work into NEW tasks appended at the END of this backlog with NEW unique AF-### IDs after the current last ID. Do not renumber.
- **To add a new task discovered later (EXP uncovered step, new safety check, new VERIFY):** append at the END with a new AF-###.

**This registry is non-negotiable. Any modification after this document is first baselined is a spec violation requiring a backlog audit re-run.**

---

## Task schema (20 fields per non-Epic task)

Every task in §2 has ALL 20 fields in THIS EXACT ORDER:

| # | Field name | Definition / meaning |
|---|-----------|----------------------|
| 1 | Task ID | Stable AF-### ID (links to everything) |
| 2 | Epic | Parent Epic ID (AF-001 … AF-010) |
| 3 | Summary | 1-sentence human-readable outcome |
| 4 | Description | 2–4 sentence context; may include Skip condition (if Conditional=yes) and Reclassification rule (post-decision task) |
| 5 | Why this task exists | 1–3 sentences on which requirement/uncertainty/EXP this resolves (links back to matrix and uncertainty register) |
| 6 | Prerequisites | What must be TRUE before starting. Includes semantic blocker comments: `BLOCKED: DELIVERY` (hardware not yet received), `BLOCKED: EXP-XXX` (needs experiment result), `BLOCKED: ADR-XXX` (needs decision), `COMPLETED: AF-###` (one specific task done), `COMPLETED: AF-### OR AF-###` (either one sufficient for OR-semantics gates) |
| 7 | Blocked by | Comma-separated AF-### IDs, or `—` |
| 8 | Required hardware | Specific BOM C-/H-/P- codes, quantities, any additional consumables |
| 9 | Required tools | Multimeter, crimp tool, ferrule crimper, soldering iron, etc., with BOM codes where applicable |
| 10 | Required software | Software + version floors, download URLs if specific |
| 11 | Exact execution steps | Numbered 1,2,3,… every single discrete action the executor does. **This list IS the task.** No hidden steps. For multi-point solder tasks, list each individual solder joint as its own step if it has an individual continuity DoD. |
| 12 | Expected result | 2–3 sentences of what the executor observes if everything goes right. May include a measurement table to fill in (with empty cells). |
| 13 | Acceptance criteria / DoD | Bullet list. Every bullet MUST be a pass/fail-observable condition. No "looks good". No "feels right". Use numbers, colors, beeps, voltage ranges, beep-counts, photo comparisons. ≤ 8 bullets. |
| 14 | Evidence to save | Exact list: filenames, photo subjects and angles, log excerpts, git commit hashes. The executor MUST attach or commit every item. |
| 15 | Safety considerations | Non-empty on every hardware-touching task. Verbatim 8-point mains checklist for any task that touches C14/AC-side wiring. Verbatim "No HUB75 hot-plug" + power order when touching HUB75. Verbatim CH340 3-pin rule when using CH340. Verbatim no-Dupont/no-series-multimeter for 5 V high-current tasks. For software-only tasks it may say "N/A — software only" but must still be present. |
| 16 | Known uncertainties | 1–5 lines from uncertainty register (U-### IDs) that still apply even when prerequisites are met. If task PRODUCES the answer, say "Resolves U-###." |
| 17 | Failure response | Step-by-step what to do on failure: which part to swap first, power-off sequence, escalate-to-VERIFY-task-flag, never "just try again blindly". |
| 18 | Source references | `STABLE-ID (FILENAME#Lx-Ly)` format. EXPs, ADRs, BOM items, JIRA safety rules, wiring docs. |
| 19 | Labels | Space-separated lowercase tags. Taxonomy: `software`, `hardware`, `firmware`, `mechanical`, `docs`, `validation`, `spike`, `safety-review`, `delivery-review`, `decision`, `critical-path`, `blocked`, `blocked:delivery`, `blocked:exp`, `blocked:adr`, `controller-wf4`, `controller-esp32`, `controller-wf2`, `nano`, `pcb`, `conditional`, `power`, `purchasing`, `thermal`, `thermal-review`, `polarity-verify` |
| 20 | Status flags | One line each with explicit values: `critical-path: candidate-yes` (pre-decision dual-track), `critical-path: yes` (unconditional on path), `critical-path: no`. `Conditional: yes/no`. If Conditional=yes, a companion `Skip condition: …` line describing when to skip. If post-decision task, a `Reclassification rule: …` line describing how to flip this task's class based on ADR outcome. |

---

# §1. Epics (AF-001 … AF-010) — 10 execution phases / milestones

---

## Epic AF-001 — M0: Hardware Receipt & Safe Power

- **Gate status:** NOT an explicit gated milestone; it is the prerequisite to M1.
- **Scope summary:** Inventory receipt (EXP-001), C14/AC mains wiring (extra-fine 6-stage split per design spec §4a: verify routing, wire L, wire N, wire PE, visual+continuity inspection, PSU no-load energize EXP-002), all per-panel power & polarity & orientation verify tasks, plus the first-panel live power test EXP-003. Everything the executor does to go from "hardware in a box on desk" to "one panel safely sits on bench receiving 5 V without any data" lives here.
- **Explicitly NOT in scope:** Controller builds (WF4 / ESP32 / WF2), controller-to-panel data, Nano software. Those are in M1 epics / parallel Nano sw subtrack.
- **Entry criteria:** `BLOCKED: DELIVERY`. Physical hardware delivery box received, opened, items on desk. The entire epic is blocked by delivery; all inside tasks have `Labels: blocked blocked:delivery`.
- **Exit / gate criteria (epic completion):** EXP-001 inventory done; C14/AC wiring passes 8-point safety checklist + visual + continuity; EXP-002 PSU no-load voltages pass tolerance + 10 min hold; EXP-003 first panel cold power test passes (panel receives 5 V, no LEDs on, no damage). The entire M0 epic is complete ONLY when BOTH the AC-side AND the DC first-panel side have been verified.
- **Parallelism notes:** Inside the mains wiring block, ordering is STRICTLY sequential (verify routing → crimp practice → L → N → PE → inspect → energize). NO PARALLEL inside mains wiring block for safety. Panel 2…panel 6 batch polarity verify tasks MAY run in parallel with each other AFTER PSU no-load completes and PSU is again OFF.
- **Labels applicable to most tasks inside:** `hardware`, `safety-review`, `polarity-verify`, `power`, `blocked:delivery`
- **Source references:**
  - BOM.md §Inventory + safety notes (BOM.md#L40-L144)
  - PROTOTYPE_WIRING.md §1–§6 inventories + VERIFY-on-receipt checklists
  - JIRA.md §Safety Rules (1) mains 8-point, (2) HUB75, (4) no-Dupont-5V
  - EXPERIMENTS.md EXP-001, EXP-002, EXP-003
  - Requirements matrix R001-R007, R017-R022, R044-R047, R064
  - Uncertainties U-001, U-002, U-003, U-021, U-025, U-026

**Tasks in this Epic:** Begin at AF-011. Inserted below.

---

## Epic AF-002 — M1: One Nano-Driven Arbitrary-Text Panel — 🔒 EXPLICIT GATED MILESTONE

- **Gate status:** 🔒 EXPLICIT GATED MILESTONE (milestone #1 of 6).
- **Scope summary:** Everything required to get "user types an arbitrary string → Nano Pillow renders it → transport delivers it → physical ONE panel displays the correct text, no manual vendor clicks during update, 10 minute stable hold." Contains FOUR FULLY PARALLEL subtracks with an aggregator GATE-PASS task at the end:
  - (a) Nano SW Bootstrap subtrack (flash SD, Python, Pillow, framebuffer, test patterns, transport abstraction) — **fully independent, may run BEFORE hardware delivery.**
  - (b) WF4 Controller subtrack (identify, power, stock firmware 1-panel EXP-004, Huidu software EXP-007A, protocol inspection EXP-007B, Nano integration EXP-007C, end-to-end arbitrary text)
  - (c) ESP32 Controller subtrack (identify EXP-010, if needed solder headers, GPIO map confirmation, **HCT245 perfboard 12-stage build with per-point continuity DoD on every step**, flash firmware, 1-panel EXP-011, Nano transport EXP-013, end-to-end arbitrary text)
  - (d) WF2 Experimental reference subtrack (EXP-008 stock, EXP-009 alt firmware — spike only, not on critical path)
- **Explicitly NOT in scope:** 2+ panels (in M2); architecture decision (in MG); production hardening of transport or renderer (in MA).
- **Entry criteria:** AF-001 EXIT criteria passed = M0 done. Nano SW may begin BEFORE delivery.
- **Exit / gate criteria (M1 Objective DoD):** AT LEAST ONE of (WF4 controller subtrack passes) OR (ESP32 controller subtrack passes). Exact 7 conditions from JIRA.md Milestone 1 requirements enumerated in GATE-PASS AF-### task's DoD. The other track may continue running in parallel for data collection purposes; it does NOT block the gate.
- **Parallelism notes:** FOUR-WAY parallel subtracks. Nano SW subtrack can even run days before M0 completes. No ordering between WF4 / ESP32 / WF2 / Nano.
- **Labels applicable to most tasks inside:**
  - Nano SW subtrack: `software nano critical-path yes` (this subtrack is ALWAYS unconditional on critical path because regardless of controller choice, we need Nano SW)
  - WF4 subtrack: `hardware controller-wf4 power validation critical-path candidate-yes blocked:exp-003`
  - ESP32 subtrack: `hardware controller-esp32 power validation critical-path candidate-yes blocked:exp-003`
  - WF2 subtrack: `firmware controller-wf2 spike critical-path no`
- **Source references:**
  - JIRA.md Milestone 1 7-item requirements list
  - EXPERIMENTS.md EXP-004, EXP-007 A/B/C, EXP-008, EXP-009, EXP-010, EXP-011, EXP-013
  - PIN_LEVEL_APPENDIX.md §1-§5 (for ESP32 HCT build)
  - DECISIONS.md ADR-004, ADR-005, ADR-008, ADR-009
  - Design spec §4c 12-stage HCT build with per-point continuity
  - Requirements matrix R001, R003, R004, R013–R016, R022, R023, R056, R057, R059, R060, R063

**Tasks in this Epic:** Start at AF-0?? (inserted after AF-001 tasks).

---

## Epic AF-003 — M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE

- **Gate status:** 🔒 EXPLICIT GATED MILESTONE (milestone #2 of 6).
- **Scope summary:** Scale from 1 → 2 chained panels per row → logical canvas 256×64. For each controller track (WF4 and ESP32): 2-panel physical chain, 2-panel parallel power branches (2 of 8 used), horizontal seam-crossing verification, left→right data flow ordering, numbered region test to confirm panel order, Standard Test Pattern Suite extended to 256-wide, Standard Defect Checklist applied to both panels, ≥ 30 min stable run. WF4 runs EXP-005 + EXP-006; ESP32 runs EXP-012. GATE-PASS aggregator at the end: either track passing unblocks the gate.
- **Explicitly NOT in scope:** 4 panels, architecture decision, production hardening, frame.
- **Entry criteria:** M1 gate passed. Both per-row controller tracks must have passed their individual 1-panel basic tasks (not gate-level, just individual working).
- **Exit / gate criteria (Objective DoD):** As per JIRA.md Milestone 2 requirements enumerated in GATE-PASS task: 256×64 logical, horizontal gradient, text crosses the seam vertically, vertical lines every 16, numbered regions, left/right order correct, ≥30 min sustained, checklist pass on both panels.
- **Parallelism notes:** WF4 M2 + ESP32 M2 tracks run in parallel (both gathering data for eventual decision). Nano SW framebuffer extension to 256×64 may run IN PARALLEL with them (it's independent; already covered in Nano SW M1 subtask "framebuffer scaling test").
- **Labels applicable to most tasks inside:** Per-track `controller-wf4` / `controller-esp32`, `validation`, `critical-path candidate-yes`, `blocked:exp-exp-004` / `blocked:exp-exp-011`
- **Source references:**
  - JIRA.md Milestone 2 requirements
  - EXPERIMENTS.md EXP-005, EXP-006, EXP-012
  - ADR-003 (2×3 layout baseline), ADR-022 (parallel power)
  - Requirements matrix R054 (ordering + seams)

**Tasks in this Epic:** After M1 tasks.

---

## Epic AF-004 — MG: Architecture Decision Gate — 🔒 DECISION GATE (milestone #3 of 6)

- **Gate status:** 🔒 EXPLICIT GATED MILESTONE (milestone #3 of 6). Decision point, not build work.
- **Scope summary:** (1) Complete EXP-014 scoring matrix with MEASURED inputs, (2) Draft and record ADR-016 (WF4 vs multi-ESP32) using the "simplest architecture that reliably satisfies V1" rule, (3) Draft and record ADR-017 (Nano → controllers transport selection — wired preferred if practical, preference order from ADR-017 record), (4) **POST-DECISION RECLASSIFICATION SWEEP** of every M3+ task in backlog: flip winning controller → unconditional critical-path yes; flip losing → conditional/skip/not-critical; always mark WF2 downstream conditional; Cond-X PCB conditional on ESP32 win; MA transport task narrowed to ONE transport implementation.
- **Explicitly NOT in scope:** Any physical build.
- **Entry criteria:** M2 gate passed; EXP-007C (WF4 protocol+latency) complete; EXP-013 (ESP32 transport+latency) complete; EXP-014 scoring matrix drafting complete.
- **Exit / gate criteria (Objective DoD):**
  1. EXP-014 scoring matrix: every one of 13 criteria × 2 candidates cells filled. Every cell cites a specific measurement source (EXP-004 pass, or EXP-011 temp, etc.). No blank cells. No "gut feel" fills.
  2. ADR-016 record written with DECISIONS.md ADR schema, status ACCEPTED, decision rule cited verbatim, rejected alternatives documented.
  3. ADR-017 record written ACCEPTED, citing the preference order: wired preferred, specific chosen transport named, rejected transports named.
  4. Post-decision sweep APPLIED: 100% of M3+ controller-specific tasks reclassified.
  5. Files updated: DECISIONS.md, 05-backlog.md reclassification fields, 06-critical-path.md post-decision single-path view updated to reflect winner, 09/10 derivation files regenerated from updated #06.
- **Parallelism notes:** No parallel inside this epic — all steps are sequential (matrix complete → ADRs written → sweep applied → derivations regenerated).
- **Labels applicable to most tasks inside:** `decision`, `docs`, `blocked:exp-exp-005`, `blocked:exp-exp-012`, `blocked:exp-exp-007c`, `blocked:exp-exp-013`
- **Source references:**
  - EXPERIMENTS.md EXP-014 §Scoring Matrix §Criteria §Weights §Decision Rule
  - DECISIONS.md ADR-004 history, ADR-017 PENDING record
  - Design spec §5 Post-decision subsection (reclassification rules VERBATIM)
  - Requirements matrix R037 (decision rule)

**Tasks in this Epic:** After M2 tasks.

---

## Epic AF-005 — M3: Four-Panel 256×128 (2×2) EXP-017 — 🔒 EXPLICIT GATED MILESTONE (milestone #4 of 6)

- **Gate status:** 🔒 EXPLICIT GATED MILESTONE (milestone #4 of 6). **FIRST-CLASS EXP-017 milestone added per user adjustment #6.**
- **Scope summary:** Full 4-panel 2×2 (256×128 logical) on the WINNING controller architecture only. EXP-017 (proposed experiment, does not yet exist in EXPERIMENTS.md) is the defining procedure for this epic. Topology per architecture:
  - WF4 wins → X1 → top-row 2-panel chain; X2 → bottom-row 2-panel chain.
  - multi-ESP32 wins → Controller #1 → top-row chain; Controller #2 → bottom-row chain. NOTE: If multi-ESP32 wins, a SECOND ESP32 controller board + SECOND HCT245 adapter MUST be purchased (if not already purchased) and built BEFORE this epic. Purchase/build tasks for #2 ESP32 are marked conditional-pre-decision in M2/MG boundary.
  Nano framebuffer = 256×128, split into TWO row crops (top 256×64, bottom 256×64), sent to respective row controllers. Tests: (a) arbitrary content crossing BOTH horizontal seam (row boundary) AND vertical seam (panel-chain boundary); (b) row synchronization sanity — both rows update within acceptable time delta; no "tearing worse than typical mid-range commercial LED sign"; (c) coordinate grid full-display + corner labels + panel boundary labels; (d) sustained ≥ 30 min; (e) full EXP-017 measurements + success criteria logged.
- **Explicitly NOT in scope:** Six panels (in M4); mechanical frame (MF). Controller build for the non-winning architecture: SKIPPED.
- **Entry criteria:** MG gate passed → winner known → reclassification sweep done → 2nd ESP32 controller (if ESP32 path) fully built and M2-equivalent 2-panel pass on BOTH rows.
- **Exit / gate criteria (M3 Objective DoD):** JIRA.md Milestone 3-like: 2×2 physical wiring, 256×128 logical, content crosses both seam types, seam-crossing text verified, row sync acceptable, coordinate test pass, ≥30 min stable, EXP-017 success criteria met.
- **Parallelism notes:** Single winning architecture → single sequential task chain. Nano MA application-level widget work may run IN PARALLEL (software does not depend on 4-panel hardware wiring completion).
- **Labels applicable to most tasks inside:** WINNING controller tag, `validation`, `critical-path yes`. For the conditional-while-pre-decision tasks: `Conditional: yes` + Reclassification rule attached.
- **Source references:**
  - EXP-017 proposed template (full, in 07-experiment-coverage.md appendix)
  - JIRA.md Milestone 3 description paragraphs for WF4 vs multi-ESP32 topology
  - ADR-006 (6 max), ADR-003 (2×3 layout baseline, M3 is 2×2 within it)

**Tasks in this Epic:** After MG tasks.

---

## Epic AF-006 — M4: Six-Panel 256×192 Final Prototype — 🔒 EXPLICIT GATED MILESTONE (milestone #5 of 6)

- **Gate status:** 🔒 EXPLICIT GATED MILESTONE (milestone #5 of 6).
- **Scope summary:** Six panels, 2×3 layout = 256 wide × 192 tall. 3 rows, each with two chained panels. WINNING controller architecture only:
  - WF4 wins → X1 → top row; X2 → mid row; X3 → bot row.
  - multi-ESP32 wins → Controller #1/#2/#3 each → one row.
  - Nano canonical framebuffer: 256×192 = 147,456 pixels RGB888. Controller-agnostic canonical render. Split into 3 row crops (each 256×64). Transport sends each row crop to the correct row controller. Tests: arbitrary content ANYWHERE on canvas; ALL 6 seams (2 vertical × 3 rows + 2 horizontal between rows) correctly crossed; corner labels + full coordinate grid + every panel boundary label; brightness sweep 25/50/75/100%; GATE-PASS aggregation; PLUS EXP-015 PSU loaded thermal (4 brightness levels × PSU voltage / farthest-panel voltage / PSU-housing temp / wiring-harness hotspots / controller temps / visual stability); PLUS EXP-016 5× power cycles with time-to-first-image + time-to-network tracking each; PLUS Wi-Fi 5-minute AP-off recovery test; PLUS API failure simulation (3P endpoint disabled → cached fallback rendered → app stays alive → no blank).
- **Entry criteria:** M3 gate passed. 3rd row controller (if ESP32 path) fully built. All harnesses built with all 6 branches + Nano + controllers + 2 spares on 2 harnesses; 8 total.
- **Exit / gate criteria (M4 Objective DoD):** JIRA.md Milestone 4 requirements 1–10 verbatim in GATE-PASS task DoD.
- **Parallelism notes:** Single winning controller path, sequential. Nano MA widget work parallel.
- **Labels applicable:** WINNING controller tag, `validation critical-path yes`, `thermal`, `thermal-review`.
- **Source references:**
  - JIRA.md Milestone 4 (10 requirements)
  - EXPERIMENTS.md EXP-015, EXP-016
  - Requirements matrix R005, R006, R007, R044, R048

**Tasks in this Epic:** After M3.

---

## Epic AF-007 — MR: Reliability & Thermal Recovery Completion

- **Gate status:** NOT an explicit gated milestone; it is an aggregator/consolidation epic.
- **Scope summary:** Aggregate all thermal data from EXP-015 (4 brightness levels, 4 measurement points each); determine if passive ventilation alone is sufficient (if not, select quiet fans in MF); log the final safe max-brightness ceiling (software value) and nighttime dimming baseline; confirm PSU test pass; run the extended ≥24 hr dashboard-normal-content stability test on 6-panel bench setup. Critical output: feeds MF design tasks (#3 depth, #10 ventilation, #12 brightness ceiling, #11 closed-box thermal). BLOCKED: MR blocks ALL MF detail work (user adjustment #9 — READY vs BLOCKED).
- **Entry criteria:** M4 gate passed; EXP-015 complete; EXP-016 complete; Wi-Fi recovery done; API failure simulation done.
- **Exit criteria (non-gated aggregator):** Brightness ceiling number documented; thermal baseline documented; fan requirement flag (yes/no + quietness requirement if yes); 24 hr extended stability pass log committed.
- **Labels applicable:** `thermal thermal-review validation docs`
- **Source references:**
  - JIRA.md §Mounted Frame (MF tasks require MR thermal inputs)
  - EXPERIMENTS.md EXP-015/016 analysis
  - Uncertainties U-022, U-033

**Tasks in this Epic:** After M4.

---

## Epic AF-008 — MA: Application Software Completion

- **Gate status:** NOT a gated milestone.
- **Scope summary:** All 12 JIRA §Software Planning stages, in strict order. OS baseline, network harden, env freeze → production font selection + multi-line text renderer → reusable test pattern module → framebuffer formal API + unit tests → transport interface exceptions/reconnect/backoff → concrete SINGLE transport implementation (selected by ADR-017 result — NOT a hybrid) → E2E pipeline hardened → scaling/row crops without renderer changes → reliability recovery (logs, watchdog, controller restart detection) → higher-level widgets: time/date, weather, Google Calendar, Spotify album art + arbitrary artwork, caching, offline fallback status + last-known data, systemd service supervision + boot auto-start.
- **Entry criteria:** M1 Nano subtask basic complete. Transport stage 8 is BLOCKED: ADR until MG. Widgets stage 12 BLOCKED: delivery until APIs creds provided by user (.env).
- **Exit criteria:** All 12 stages done; 24 hr dashboard-normal content stability; every widget works offline; app survives 3P API failures with grace.
- **Parallelism notes:** MA widget work runs IN PARALLEL with most M3/M4/MF work once M1 Nano basic is done. Only stage 8 depends on MG, and only stage 12 depends on user-provided API keys.
- **Labels applicable:** `software nano critical-path yes docs` (stages 1–7 unconditional; stage 8 blocked:adr-017; stage 12 blocked:delivery user-action)
- **Source references:**
  - JIRA.md §Software Planning 12-stage ordered list
  - DECISIONS.md ADR-009, ADR-010, ADR-011, ADR-012, ADR-023, ADR-017

**Tasks in this Epic:** After M1 Nano subtrack (can overlap with much of hardware work).

---

## Epic AF-009 — MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE (milestone #6 of 6)

- **Gate status:** 🔒 EXPLICIT GATED MILESTONE (milestone #6 of 6).
- **Scope summary:** All 18 JIRA §Mounted Frame Phase items in order. All tasks are BLOCKED: EXP-exp-015 MR completion until MR delivers thermal ceiling + component dims + PCB dimensions if ESP32-PCB path chosen. Items 1–18: exact component dims, internal layout sketch with X/Y coords + clearance + airflow, minimum frame depth, PSU location + air holes, controller + Nano location, physical mains/low-voltage separation barrier, cable routing plan with bundle ties + service loops, strain relief ALL 4 categories (C13 + panel harnesses + HUB75 + Nano USB-C), PE bonding ALL 3 categories (PSU FG → chassis → panel frames), ventilation + passive/quiet-fan decision, closed-box thermal test at brightness ceiling, Wi-Fi inside frame, panel mechanical mounting + alignment, prototype backplate build, final aesthetic + wall bracket hardware, 72 hr wall test, GATE-PASS.
- **Entry criteria:** MR complete; exact component dimensions measured (WF4 or 3× ESP32/HCT depending on ADR-016); Cond-X PCB (if applicable) has exact board outline + dimensions.
- **Exit / gate criteria (Objective DoD):** All 18 JIRA MF items pass. Exact 18 enumeration in GATE-PASS task DoD.
- **Parallelism notes:** MF items run nearly sequentially (layout sketch → placement → cutout → build → test); some measurement sub-steps parallel within item 1.
- **Labels applicable:** `mechanical blocked blocked:exp-exp-015 power safety-review docs thermal-review`
- **Source references:**
  - JIRA.md §Mounted Frame 18 items
  - DECISIONS.md ADR-015 (docs-first), ADR-024 (deferred, NO CUTTING before dims/thermal/schematic complete), ADR-020 (wall only, not desktop)
  - Requirements matrix R009, R028-R033, R065-R066

**Tasks in this Epic:** After MR.

---

## Epic AF-010 — Cond-X: Conditional ESP32 Custom PCB

- **Gate status:** NOT a gated milestone. Entire epic is CONDITIONAL.
- **Scope summary:** 19 JIRA §Conditional PCB Work steps (1=freeze ESP32 footprint, 2=freeze validated GPIO map, 3=schematic ESP32 socket + rails, 4=two HCT245 stages U1/U2, 5=decouple, 6=HUB75 keyed 2×8 connector pinout matches perfboard, 7=power/GND entry + thick traces/pours, 8=ERC pass, 9=PCB layout + trace width rules + ground pour, 10=DRC pass, 11=connector/orientation review, 12=Gerber X2, 13=Excellon drill + legend, 14=PCB BOM ref des+values+footprints, 15=CPL if assembled, 16=5 board manufacture via JLCPCB/Jiepei/JDBPCB, 17=receipt continuity every net, 18=one-row validation on hardware, 19=perfboard removal + PCB install + identical behavior + wiring docs references updated).
- **Entry criteria:**
  - HARD CONDITION: `Skip condition: ADL-016 does NOT select multi-ESP32 architecture` — if WF4 wins, entire epic skipped.
  - If multi-ESP32 wins: EXP-010 (footprint dims) complete; validated (not provisional) GPIO map from working perfboard EXP-011/012 passes; Cond-X PCB = perfboard replacement, NOT first-ESP32-HCT build. ADR-014 strictly enforced.
- **Exit criteria (non-gated):** If skipped, note added to each task "Skip — WF4 architecture selected". If executed: 1-19 all complete, perfboards removed, PCB adapters in their place, identical behavior verified, docs updated.
- **Parallelism notes:** Sequential, schematic → layout → manufacture → validation.
- **Labels applicable:** ALL tasks have `hardware pcb conditional blocked blocked:adr-016 docs`
- **Source references:**
  - JIRA.md §Conditional PCB Work (19 steps)
  - BOM.md §8 Fabrication services researched (3 vendors: JLCPCB, Jiepei, JDBPCB)
  - PIN_LEVEL_APPENDIX.md §1–§5 validated pin allocations (use working perfboard map, not the initial provisional one)
  - DECISIONS.md ADR-014 (NO PCB before perfboard validated), ADR-015 (docs-first)

**Tasks in this Epic:** After MR / concurrent with MF (no blocking either way; Cond-X PCB feeds MF layout sketch item 1 if ESP32 wins).

---

---

# §2. Tasks (AF-011+)

---

# §2. Tasks (AF-011+)

Inserted in sequential AF-### ID order. Never renumbered. Append only at tail.

---

## M0 Epic AF-001: Hardware Receipt & Safe Power

---

### AF-011: EXP-001 Inventory all boards — photo every, record PCB revisions, confirm markings

**Task ID:** AF-011
**Epic:** AF-001 (M0: Hardware Receipt & Safe Power)
**Summary:** Execute EXP-001 — inventory every purchased component.
**Description:** Physical hardware delivery arrival. Open the shipping package(s), lay every BOM item on a clean workbench, and execute EXP-001 procedure line-by-line. This is the first hardware-touching task of the entire project; all downstream hardware tasks depend on its results.
**Why this task exists:** Resolves uncertainty U-006 (BOM items ORDERED → RECEIVED). Produces the PCB revision, WF4 markings, ESP32 dimensions, and other identification facts used by ALL downstream build tasks. Implements requirements R005 (physical 6 panels), R039 (PCB data-sheet facts later), R046 (fuse rating verify-on-receipt). Produces evidence for every single VERIFY-ON-RECEIPT checklist in PROTOTYPE_WIRING.md §1–§6.
**Prerequisites:** BLOCKED: DELIVERY. Physical package delivered to work location. Workbench cleared with space for all items and a plain photo backdrop.
**Blocked by:** —
**Required hardware:** All items in BOM.md shipping boxes as delivered. No extra hardware.
**Required tools:** Smartphone or camera for photos; permanent marker for labelling bags; paper labels or Post-its for per-item tags.
**Required software:** EXPERIMENTS.md open for step-by-step walkthrough of EXP-001 Procedure 1-11.
**Exact execution steps:**
1. Open shipping boxes. Photograph the UNOPENED outer boxes (shipping-label side + opposite side = 2 photos).
2. Unpack every item onto the workbench. Keep bags/boxes with their items until identification complete.
3. For EVERY PCB item (panels × 6, WF4, ESP32, Nano, PSU, C14 inlet):
   a. Photograph FRONT at close range with board labels facing camera (sharp focus, good lighting).
   b. Photograph BACK at close range.
   c. Record: PCB revision marking (e.g., "Rev 1.2" silk), any serial number sticker, manufacturer name (if silk-screened).
   d. For panels: count the modules in the 2×8 HUB75 header receptacle on the rear, verify 16 positions visible, record keyed notch PRESENT or ABSENT.
   e. For WF4: count HUB75 ports (X1, X2, X3?); record port labels exactly as silk-screened.
   f. For ESP32: record silkscreen "ESP32-S3-DevKitC-1-N16R8" or any deviation. Look for USB-C port; BOOT + RESET buttons; antenna type (PCB antenna or U.FL?). Measure dimensions mm: length × width (record). Measure header spacing: 1×40 pins each side = pitch 2.54 mm (standard). Record if headers already soldered.
   g. For Nano: record "LicheeRV Nano-W" or variant; USB-C visible; microSD slot visible; antenna; any BOOT switches.
   h. For PSU (RD-65A): photograph model label; record output ratings (match to spec).
   i. For C14 inlet: photograph fuse holder + switch side-by-side on rear tab; confirm fuse P-03c (5 A × 20 mm) present in holder; record fuse rating printed on fuse body.
4. For cable/harness items (C13, HUB75 cables, panel power harnesses × 2):
   a. Photographs both ends of each cable.
   b. Panel power harnesses: count fused branches on each harness → confirm 4+4 = 8 total branches, 2 spares per ADR-022. Record fuse rating printed on each branch fuse (if visible).
5. For mechanical: frame screws, M3 nuts, washers, z-clips, standoffs, brackets PLA — count quantity, compare to BOM counts, flag short-ships.
6. For consumables: ferrules (2 sizes red + blue), spade terminals, solder, HCT245 ICs × 2 (or × 2 + spares), 100nF caps, 1000µF cap — count, compare, flag short-ships. Confirm HCT245 packaging = DIP-20 (through-hole, not SMD).
7. For tools (crimp, ferrule crimper, wire cutters, multimeter leads): confirm all match BOM descriptions.
8. Compare each item's actual appearance to BOM.md "Notes/Description" field. For any deviation, write it down (e.g., "panel power harness has 5 branches, not 4" → order correction needed before panel build).
9. Bag/tag every identified item with a sticky label = BOM line code (C-04a panel-1, C-04b panel-2, … C-04f panel-6, H-18 HUB75 cable 0.5 m-1, etc.). Put items back in bags with labels.
10. Set aside items in categories per PROTOTYPE_WIRING §1-§6 groupings (C14 group, Panel Power group, HUB75 group, WF4 group, ESP32 group, Nano group, Debug group).
11. Flip BOM.md STATUS column: every item from "ORDERED" to "RECEIVED" and commit the change to a new WIP branch or local save of BOM.md (final commit later with other docs, but save the change locally).
**Expected result:** Every BOM line item physically present, identified, photographed, labeled, categorized. Short-ships or deviations documented. 6 panels × 2 photos each = 12 panel photos; WF4/ESP32/Nano/PSU/C14 each have 2+ photos; cables have end photos.
**Acceptance criteria / DoD:**
- All panels ×6 physically present on bench, confirmed P1.53-128×64 form factor, 16-pin HUB75 header receptacle present with keyed notch confirmed
- WF4 physically present, X1/X2/X3 port count noted
- ESP32 present, dimensions mm recorded, headers solder status recorded, N16R8 marking photo
- Nano present, USB-C + microSD confirmed
- PSU RD-65A present, ratings label matches BOM P-01
- C14 present, fuse holder+switch rear-tab routing matches PROTOTYPE_WIRING PWR-01 description, fuse rating 5 A × 20 mm physically read off fuse body
- Cable counts: H-03 C13 cord, H-18 0.5m HUB75 × n, H-19 1m HUB75 × n, panel harnesses × 2, 8 fused branches total (verify by counting fuse holders on harnesses)
- No deviation (OR deviations documented with "order correction action" plan for any short-ship)
**Evidence to save:**
- `inventory/` subfolder committed to docs (outside repo? no — commit to `docs/photos/inventory/YYYY-MM-DD/` if git LFS or size OK; otherwise, list filenames + store locally per `.gitignore` rules): outer-box.jpg, pcb-front/*.jpg (6 panels + 4 controllers/PSU/C14), pcb-back/*.jpg, cable-ends/*.jpg, consumables-count.jpg (single group photo showing spread of ferrules/crimps/HCT ICs), labels-on-bags group photo
- `docs/pm/evidence/AF-011-exp-001-inventory-notes.md` = text notes: per-PCB revision strings, ESP32 dims mm, headers soldered Y/N, fuse ratings recorded, deviations + short-ship list with resolution plan
- git diff of BOM.md (STATUS changes ORDERED → RECEIVED)
**Safety considerations:**
N/A — unboxing and identification only, no power.
NOTE: Do NOT discard anti-static bags; we re-use them for ESD-sensitive storage of ESP32/WF4/Nano when not in use.
**Known uncertainties:**
- Resolves U-006 (delivery status → now COMPLETED for what arrived; short-ships remain partial).
- Resolves, via physical observation + photo evidence: U-014 (ESP32 headers soldered?), U-015 (N16R8 markings?), U-007 (WF4 hardware present, markings identified → WF4 q1 serviceability partial answer), U-021 (PSU terminal layout, photo captured).
- U-026 (mains fuse rating): partially resolved IF fuse body clearly readable; if illegible → escalate to dedicated VERIFY task.
**Failure response:**
If any panel missing / fuse wrong rating / harness not 8 branches → DO NOT proceed to wire L/N/PE. Stop. Photograph the discrepancy. Open a correction-ticket action: contact seller for replacement. Log time estimate. Leave all other tasks in M0 blocked until corrected (except Nano SW work, which is independent). Re-run this task's DoD after correction arrives.
**Source references:**
- EXPERIMENTS.md EXP-001 Procedure 1–11 + Measurements table columns (EXPERIMENTS.md#L36-L70 approximately)
- BOM.md all 48 STATUS=ORDERED lines (BOM.md#L40-L144)
- PROTOTYPE_WIRING.md §1–§6 VERIFY ON RECEIPT checklists (PROTOTYPE_WIRING.md#L1-L80)
- Uncertainty register U-006, U-014, U-015, U-021, U-026
**Labels:** hardware safety-review delivery-review blocked blocked:delivery purchasing docs
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-012: VERIFY C14 rear tab routing — L/N/PE pass through fuse and switch before reaching PSU (power OFF, no power anywhere)

**Task ID:** AF-012
**Epic:** AF-001 (M0)
**Summary:** Before any mains wiring, confirm C14 L/N/PE physical routing through fuse + 2-pole switch correct.
**Description:** With the C14 inlet fully UNCONNECTED from PSU (no wires attached yet) and with 100% of the workbench unpowered (not just PSU off — wall plug OUT), use the multimeter in continuity mode (beep) to walk the internal conductive path from the C14 prongs through the rear-tab metal stamping, through the fuse holder (fuse inserted), through the 2-pole switch (in ON position), and out to the bare wire terminal posts. This is a NOVICE-CRITICAL pre-check because if fuse or switch is on the wrong line (e.g., PE is routed through switch) that is a safety hazard. Do NOT solder/crimp anything to the C14 inlet posts until THIS verification passes.
**Why this task exists:** Resolves U-001 (is mains routing correct?). JIRA safety rule (1) mains 8-point checklist pre-check #1 "tab routing verified". Also enforces R017 (mains fuse L only, 2-pole switch both L+N, PE continuous not switched).
**Prerequisites:** COMPLETED: AF-011. C14 inlet physically on bench, rear-tab accessible, fuse inserted into C14, switch toggleable. All power OFF: wall plug removed from socket, 3 m from any energized object if possible.
**Blocked by:** AF-011
**Required hardware:** C14 inlet P-02 (rear-tab with integrated fuse + 2-pole switch) as delivered; fuse P-03c 5A inserted; 3 short Dupont male pins (only for multimeter probe tips to reach tight posts — NOT used for current carrying).
**Required tools:** Multimeter in continuity mode. Probes tips clean.
**Required software:** PROTOTYPE_WIRING.md §1 open.
**Exact execution steps:**
1. Confirm wall plug removed. Confirm multimeter set to continuity mode (beep on < 1 Ω). Test probes together → should beep.
2. Locate the C14 3-prong MALE side (wall-plug insertion side, called LINE SIDE prongs): prong L = Live (fused), prong N = Neutral, prong PE = Protective Earth (middle).
3. Locate C14 3 posts on the REAR TAB (WIRE SIDE where we crimp). They should have labels embossed L, N, PE/E.
4. **Test 1: Line (L) path through fuse.** Switch OFF. Probe LINE-SIDE prong L → WIRE-SIDE post labeled L (through fuse path). Expected: NO continuity (switch OFF breaks the path). Good.
5. **Test 2: Switch ON, Line through fuse+switch.** Switch ON now. Probe LINE-SIDE L prong → WIRE-SIDE L post. Expected: BEEP = continuity. Record: fuse is IN the L conductor path, switch IN the L conductor path.
6. **Test 3: Neutral (N) path through switch only.** Switch OFF. Probe LINE-SIDE prong N → WIRE-SIDE post labeled N. Expected: NO continuity (switch OFF breaks 2nd pole).
7. **Test 4: Switch ON, Neutral path through switch only.** Switch ON. Probe LINE-SIDE N prong → WIRE-SIDE N post. Expected: BEEP. Record: switch ALSO breaks the N pole = 2-pole switching confirmed.
8. **Test 5: PE path, switch OFF → NO break.** Switch OFF. Probe LINE-SIDE PE prong → WIRE-SIDE PE post. Expected: BEEP (PE continuous, NOT switched).
9. **Test 6: PE path, switch ON → still continuous.** Switch ON. Probe same as step 8. Expected: BEEP still. Record: PE NEVER goes through the switch. Correct.
10. **Test 7: L↔N no internal short.** Switch ON. Probe L-wire-post ↔ N-wire-post. Expected: NO beep (no internal short in C14/switch/fuse assembly).
11. **Test 8: L↔PE no internal short.** Switch ON. Probe L-wire-post ↔ PE-wire-post. Expected: NO beep.
12. **Test 9: N↔PE no internal short.** Switch ON. Probe N-wire-post ↔ PE-wire-post. Expected: NO beep.
13. Write down results: every test step 4–12 has a pass/fail (beep Y/N) on a single sheet of paper taped to C14 for reference.
**Expected result:** All 9 continuity tests match the expected behavior exactly. L goes through both fuse and switch. N goes through switch only (not fuse). PE goes through neither (continuous). All 3 pairs no shorts.
**Acceptance criteria / DoD:**
- Tests 4,6 match: L passes through fuse+switch (switch off=no, switch on=beep). If not → stop, C14 routing wrong → DO NOT USE.
- Tests 7,8 match: N passes through switch (2-pole confirmed). If not → review.
- Tests 9,10 match: PE never switched (both switch states → beep). If PE switches → major safety issue.
- Tests 11,12,13: all 3 pairs (L↔N, L↔PE, N↔PE) → NO beep, no internal shorts. If any beep → C14 defective.
- All 9 recorded pass/fail lines written & physically attached to C14 inlet for later audit.
**Evidence to save:**
- Photo of multimeter + C14 being tested (probe positions visible + beep visible on multimeter display if beep-icon or continuity-symbol shown)
- `docs/pm/evidence/AF-012-c14-routing-continuity-notes.md` — step-by-step pass/fail logged + photo filenames
**Safety considerations:**
⚠️ Mains wiring task safety checklist (verbatim JIRA.md Safety Rules 1/8-point, applicable to every mains-touch task):
  1. Wall plug REMOVED from socket for entire task (no energized workbench at any point).
  2. Multimeter probes properly insulated; no exposed copper on probes.
  3. All work on wooden bench; tools non-conductive handles.
  4. C14 is isolated from PSU for this test — only the standalone C14 on bench.
  5. If any doubt on reading, stop and redo — never guess continuity result.
  6. No jewelry on hands; short hair tied back.
  7. Fire extinguisher class ABC visible if work area allows (even though unpowered — habit).
  8. Every individual continuity result written DOWN — not memorized.
⚠️ Polarity-verify only: at NO point does mains power connect during this task.
**Known uncertainties:** Resolves U-001 (mains routing correctness). U-026 C14 fuse correct rating partially here (fuse IN holder; rating confirmed in AF-011).
**Failure response:** If any DoD condition fails, STOP ALL MAINS WIRING. Return the C14 assembly if routing does not match spec. Do NOT guess. Do NOT "fix with a wire rework" — replace the inlet if it's mis-wired internally. If internal shorts found (tests 11-13 fail), discard inlet (or use only after replacement of fuse/switch components by a licensed electrician — novice recommended: replace inlet).
**Source references:**
- JIRA.md §Safety Rules (1/4) mains 8-point checklist verbatim
- PROTOTYPE_WIRING.md §1.1 PWR-01 C14 inventory "Tab routing confirmed through fuse/switch before PSU"
- Requirements matrix R017 (mains routing), R019 (dedicated verify OFF task before energize)
- Uncertainty U-001
**Labels:** hardware safety-review power polarity-verify blocked blocked:exp-af-011
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-013: Crimp practice + visual sample test — ferrule visual sample test + spade crimp practice

**Task ID:** AF-013
**Epic:** AF-001
**Summary:** Produce 5 good ferrule crimps (18 AWG) and 5 good spade crimps before any real wiring crimp.
**Description:** Novice crimp practice task. Produce a set of sacrificial crimp samples (5 ferrules on 18 AWG red wire offcuts, 5 spade terminals on the same). Each sample passes visual inspection and optional pull test. No real crimp onto a component (C14 L-wire, etc.) happens BEFORE this task produces its 10 good samples and passes DoD. Rationale per JIRA.md novice rules: every "crimp" preceded by "practice crimps" task.
**Why this task exists:** Novice skill building. Catches badly-angled crimps, insulation-too-far-in issues, missing ferrules on stranded ends BEFORE a real crimp ruins a PSU post or creates a bad connection. Addresses U-003 (5V high-current wiring adequate? — crimp quality is a component). Crimp tool learning.
**Prerequisites:** COMPLETED: AF-011. Tools + consumables on hand. No power required.
**Blocked by:** AF-011
**Required hardware:** BOM H-06 18 AWG red wire 10 short offcuts ~5cm each (stranded), H-07 ferrules (18 AWG red × 5, matching to 18 AWG gauge color code), H-09 spade terminals (M3 or M4 as required by PSU post — match to PSU posts' actual size, TBD by visual identification of PSU posts now; use size consistent with PSU posts).
**Required tools:** BOM H-15 crimp tool (ratchet), BOM H-16 ferrule crimper (ratchet), wire stripper/cutter.
**Required software:** None.
**Exact execution steps:**
1. Strip ~6 mm insulation off one end of a 18 AWG red offcut. No nicks on strands visible (check each of the 7 strands under light; if nicked, cut end off and re-strip).
2. Insert ferrule: stripped strands fully inside ferrule metal tube; 0.5 mm insulation edge visible just outside ferrule lip (insulation NOT inside metal crimp zone).
3. Ferrule crimper: select correct 18 AWG nest. Insert ferrule squarely. Squeeze ratchet crimper handles until ratchet releases (one click = full cycle). Do NOT half-squeeze.
4. Remove. Visual inspection checklist (AF-014 inspection uses the SAME checklist so this memorizes it):
   (a) Strands fully captured (no stray strands outside ferrule).
   (b) Ferrule crimped squarely (not angled).
   (c) 0.5–1 mm insulation visible at edge (not crimped inside metal), metal does not bite insulation.
   (d) No cracks in ferrule metal (visual).
   (e) If you tug the wire firmly (<5 kg = hand pull hard), wire does not come out of ferrule.
5. Repeat steps 1-4 for ferrule samples #2, #3, #4, #5.
6. Spade crimp: strip ~6mm off new offcut. Insert stripped strands into spade wire-entry barrel. Insulation edge 0.5mm outside barrel's insulation-grip wing (the wing crimps over INSULATION, not metal). Two-stage crimp: first crimp metal-crimp barrel over strands; second crimp insulation-wings over insulation.
7. Remove. Spade visual:
   (a) All strands captured (no stray).
   (b) Spade tongue not deformed.
   (c) Insulation crimp wing grips insulation firmly (tug → stays on).
   (d) No exposed copper strands between insulation and spade.
8. Repeat spade × 5 → 5 samples.
9. Arrange 5 ferrules + 5 spades in a row on a white background. Label them F1…F5 and S1…S5.
**Expected result:** 10 good crimp samples, all 10 pass the individual visual rules above. No samples fail. If one fails, discard and redo to bring count back to 5 per category.
**Acceptance criteria / DoD:**
- 5 ferrule samples. For each: 4 visual sub-checks (strands full, square, insulation visible edge, no cracks) + 1 tug (no pullout). 5/5 per sample × 5 samples = 25 sub-checks pass.
- 5 spade samples. For each: strands full + tongue undeformed + insulation crimp OK + no copper gap + tug no pullout = 25 sub-checks pass.
- Total 50 sub-checks: 100% pass.
- F1-F5 and S1-S5 labeled and arranged in 1 group photo so inspection by a later reviewer is possible.
**Evidence to save:**
- Photo: `docs/photos/crimp-practice-2026-08-19.jpg` (group of 10 labeled samples)
- `docs/pm/evidence/AF-013-crimp-practice-notes.md` = per-sample pass/fail for each sub-check, any samples discarded for failures noted
**Safety considerations:**
No electrical. Mechanical only: keep fingers away from crimp tool ratchet release during squeeze (pinch point). Ferrule crimper works similarly.
**Known uncertainties:** Resolves the "novice crimp skill sufficient?" part of U-003 (only a component; wiring task will test real crimps).
**Failure response:** If more than 1 sample per 10 attempts fails → tool or technique issue. Review tool nest selection (correct AWG?); review strip length (6mm?); maybe wire stripper damaged. Adjust; re-do until 10/10 first-try pass.
**Source references:**
- BOM H-06, H-07, H-09, H-15, H-16
- JIRA.md novice crimp rule (R049: "every crimp preceded by practice crimps task")
- R064 requirements matrix
**Labels:** hardware docs spike blocked:exp-af-011
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-014: Wire C14 L → PSU L (18 AWG; crimp spade + ferrule each end; verify each end continuity)

**Task ID:** AF-014
**Epic:** AF-001
**Summary:** Install the Live (L) wire between C14 wire-side L post and PSU L input screw post. Single conductor only.
**Description:** Wire the L conductor. One end: crimp appropriate spade terminal onto 18 AWG brown (or black per local convention, BROWN for L EU, BLACK for L US — use the color coded for L in BOM wiring; brown is EU convention; note actual color used) to attach to C14 wire-side L post OR (if C14 posts are screw-clamp type — identified by visual from PSU photos U-021 already gathered in AF-011) use stripped + ferrule only for screw clamp. The other end: 18 AWG stranded → ferrule → PSU L input screw post. Cut length: generous service loop (~15 cm longer than straight-line distance C14 → PSU) so later frame service doesn't pull tight. Do only L wire in this task; do not do N or PE in same task = extra-fine split per design spec §4a.
**Why this task exists:** Extra-fine split (design spec §4a): one wire = one task. Allows isolation of L-wire mistakes from N/PE. Implements mains wiring step 1 of the physical build. Addresses U-001 sub-step.
**Prerequisites:** COMPLETED: AF-012 (C14 routing verified correct). COMPLETED: AF-013 (good crimps mastered). All power OFF, wall plug out. Workbench clear.
**Blocked by:** AF-012, AF-013
**Required hardware:** 18 AWG wire length L-color (brown or black per convention, cut for C14→PSU + 15 cm slack); 1× spade terminal (correct size for C14 wire-side L post screw); 2× ferrules (one at PSU end + one spare if redo needed).
**Required tools:** Wire stripper, cutter, ferrule crimper, crimp tool (spade), multimeter continuity mode.
**Required software:** None.
**Exact execution steps:**
1. Measure C14 wire-side L post → PSU L screw post distance on layout. Add 15 cm service slack. Cut wire to that length.
2. Strip one end ~6 mm. Ferrule crimp to this end: ferrule metal → PSU end. Use AF-013 technique. Visual pass + tug.
3. Strip other end ~6 mm. If C14 L post = screw post with spade: crimp a spade terminal (two-stage crimp, strands + insulation both captured). If C14 L post = screw clamp: ferrule only. Visual + tug.
4. Route wire along planned path. No sharp bends < 2× wire diameter.
5. Attach the PSU end FIRST (easy access; no strain yet): unscrew PSU L post (counter-clockwise), insert ferrule into the screw-clamp gap, tighten screw firmly (finger-tight + 1/4 to 1/2 turn with a small screwdriver — do not strip plastic threads — if plastic post stop at firm).
6. Attach C14 wire-side L post end: if spade, place spade under washer or directly under screw head; tighten firmly. If clamp: insert ferrule; tighten clamp.
7. Multimeter continuity: one probe touches C14 LINE-SIDE (male prong) L pin; other probe touches PSU L post metal OR PSU L input screw head. Expected: BEEP (solid continuity, no intermittent when wiggling).
8. Wiggle-test continuity: wiggle both ends of wire, 10 cm from each connection, 3 times each direction. Continuity beep never drops. No breaks.
9. Visual inspection of the two crimps: no stray strands (ferrule side), spade not deformed.
**Expected result:** One L wire installed, both ends tight, continuity solid < 1 Ω at all times (even wiggle), no visual defects.
**Acceptance criteria / DoD:**
- L wire connects correct post pairs: C14 LINE-SIDE L prong ↔ PSU L input post. Single conductor only.
- Continuity step 7: BEEP solid.
- Wiggle test step 8: NO drop in continuity (beep uninterrupted throughout). If intermittent → bad crimp → redo.
- Both crimps visual pass (ferrules: 4-point checklist; spade: 4-point checklist per AF-013).
- Length slack present (≥ 8 cm service loop at both ends, not taught).
**Evidence to save:**
- Photo: `docs/photos/mains/L-wire-both-ends.jpg`
- Continuity reading: multimeter close-up of beep-indicator or ohms reading (< 0.5 Ω)
- `docs/pm/evidence/AF-014-L-wire-install.md` = wire color used, length cut, visual+wiggle test outcomes logged
**Safety considerations:**
JIRA.md Safety Rules (1) mains 8-point verbatim checklist applicable:
1. Wall plug REMOVED from socket.
2. Multimeter probes insulated.
3. Wooden/non-conductive bench.
4. L wire only in this task. Do not install N or PE now.
5. If wiggle test shows intermittent → undo, redo. Do not proceed.
6. No jewelry.
7. Fire extinguisher visible.
8. Result written down, not memorized.
**Known uncertainties:** U-021 (PSU terminal type = spade vs screw clamp): resolved by AF-011 photos. U-001 (mains routing through fuse/switch): already resolved by AF-012 routing verify; L wire only attaches to correct posts.
**Failure response:** If wiggle intermittent → undo both ends. Inspect each crimp visually. Most likely cause: ferrule strands not fully captured or screw not tight. Redo the faulty end and re-test. NEVER proceed with intermittent connections on L wire.
**Source references:**
- JIRA.md Safety Rules (1/4).
- Design spec §4a extra-fine splits for mains wiring.
- PROTOTYPE_WIRING §1.1 PWR-01.
- BOM H-06 wire, H-07 ferrules, H-09 spades.
- Uncertainties U-001, U-021.
**Labels:** hardware safety-review power blocked blocked:exp-af-012 blocked:exp-af-013
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-015: Wire C14 N → PSU N (18 AWG; crimp spade + ferrule; verify continuity + wiggle)

**Task ID:** AF-015
**Epic:** AF-001
**Summary:** Install Neutral (N) wire. Single conductor. Identical procedure to L wire AF-014 but for N post only.
**Description:** Parallel to AF-014, for the N conductor only. One wire = one task (extra-fine split). Use BLUE wire color (EU convention for N) or WHITE color for US convention. Cut length same as L wire, +/-2 cm for routing differences. Continuity verify: LINE-SIDE N prong ↔ PSU N post. Wiggle test.
**Why this task exists:** Extra-fine split. If a mistake occurs in N wire, it is isolated from L and PE mistakes.
**Prerequisites:** COMPLETED: AF-014 (methods validated). COMPLETED: AF-012 routing verified. COMPLETED: AF-013.
**Blocked by:** AF-014, AF-012, AF-013
**Required hardware:** 18 AWG BLUE or WHITE wire + 2 ferrules + 1 spade (as appropriate for C14 post type).
**Required tools:** Identical to AF-014.
**Required software:** None — hardware-only task.
**Exact execution steps:**
Identical to AF-014 steps 1-9, replacing "L" → "N", "brown/black" → "blue/white".
1. Measure + cut with slack.
2. Ferrule crimp (PSU end).
3. Spade or ferrule crimp (C14 end).
4. Route wire away from L wire (routing: maintain physical separation where possible).
5. PSU end attach.
6. C14 end attach.
7. Continuity: LINE-SIDE N prong → PSU N post = BEEP.
8. Wiggle both ends. No interruption.
9. Visual inspection.
**Expected result:** N wire solid, < 1 Ω, wiggle pass.
**Acceptance criteria / DoD:** Identical to L-wire DoD with N post pairs.
**Evidence to save:** Analogous to AF-014 (N-wire photos + notes).
**Safety considerations:** JIRA Safety Rules (1) mains 8-point checklist verbatim applicable. Single wire only in this task. Do NOT connect PE yet.
**Known uncertainties:** U-001 component.
**Failure response:** Intermittent → redo crimps.
**Source references:** Same refs as AF-014. Design spec §4a.
**Labels:** hardware safety-review power blocked blocked:exp-af-014
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-016: Wire C14 PE → PSU FG / Earth (18 AWG; verify PE continuous, NOT switched, at all switch positions)

**Task ID:** AF-016
**Epic:** AF-001
**Summary:** Install Protective Earth (PE) / Frame Ground (FG) conductor. NOT switched. Continuity verify at both switch positions.
**Description:** PE wire. Green-yellow stripe wire color (international standard mandatory for PE if purchased; confirm color in BOM wire spools). ONE wire only (extra-fine split). C14 LINE-SIDE PE prong → WIRE-SIDE PE post → PSU FG/Earth input screw post. CRITICAL CHECKS: (1) PE is NEVER switched (verify at both switch OFF and switch ON → always continuity); (2) PE NEVER goes through fuse; (3) no electrical separation between the two (solid metal path throughout).
**Why this task exists:** Extra-fine split; PE is safety-critical (U-004 PE bonding). U-001 PE component of routing resolved in practice.
**Prerequisites:** COMPLETED: AF-015 (L and N installed; do PE last so routing doesn't confuse with L/N); COMPLETED: AF-012 PE routing test passes at component level.
**Blocked by:** AF-015, AF-012
**Required hardware:** Green-yellow stripe 18 AWG wire + ferrules × 2 ends (both ends likely screw clamp; use spade only if C14 PE post requires — typically PE post is screw clamp directly into C14 body metal so ferrule is correct).
**Required tools:** Stripper/cutter, ferrule crimper, multimeter continuity mode.
**Exact execution steps:**
1. Cut length. Same slack as L/N.
2. Ferrule both ends (C14 PE clamp + PSU FG screw clamp).
3. Route wire.
4. Attach PSU FG post (usually labeled "FG" or earth symbol on PSU label).
5. Attach C14 wire-side PE clamp post.
6. Continuity checks:
   a. LINE-SIDE PE prong → PSU FG post → BEEP always.
   b. Switch position OFF → re-test PE continuity → BEEP still.
   c. Switch position ON → re-test → BEEP still.
   d. Wiggle both ends → BEEP uninterrupted.
7. Cross-check: PE to L no continuity (BLOCKED: separate task for full cross-check later; spot-check PE ↔ L now: NO beep, PE ↔ N now: NO beep).
8. Visual both ferrules.
**Expected result:** PE continuous in both switch states, wiggle pass, PE isolated from L/N (no cross).
**Acceptance criteria / DoD:**
- PE beep at both OFF and ON switch position (6a, 6b, 6c all 3 BEEP).
- Wiggle uninterrupted.
- Cross-check spot: PE↔L: NO beep. PE↔N: NO beep. If beep → cross-shorted → undo immediately.
- Green-yellow color mandatory (if not, mark wire with green-yellow tape to make unambiguous; flag if BOM shipped wrong color).
**Evidence to save:** PE-wire photos + notes. Multimeter continuity at both switch positions shown.
**Safety considerations:** JIRA Safety Rules (1) mains 8-point verbatim checklist. PE = most safety critical wire in the box. No compromise.
**Known uncertainties:** U-004 (PE bonding in whole frame later: this task is PSU-side PE bonding only; chassis side PE done in MF #9).
**Failure response:** If PE continuity depends on switch position → routing WRONG. Immediately undo all three wires (L/N/PE) and re-verify physical C14 routing again (AF-012 procedure). If correct → re-attach in correct posts. If cross PE↔L or PE↔N → separate at once.
**Source references:** JIRA Safety (1), PROTOTYPE_WIRING §1, BOM.md Safety #5.
**Labels:** hardware safety-review power blocked blocked:exp-af-015 polarity-verify
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-017: Visual + continuity inspection of all three AC wires — no stray strands, insulation, strain relief candidates

**Task ID:** AF-017
**Epic:** AF-001
**Summary:** Inspect all 3 AC wires visually; confirm no stray strands; full cross-continuity matrix (L/N, L/PE, N/PE all OPEN).
**Description:** After L/N/PE all three wired, do a full inspector's walk before any thought of energizing. This is JIRA.md Safety Rules (1) checklist pre-energize visual step. Two phases: (A) Visual inspection of every crimp, every joint, every insulation edge, every screw tightened, every bare-metal exposure area. (B) Electrical cross-continuity check matrix of all 3 pairs, all in both switch positions. No energizing.
**Why this task exists:** Novice final check before PSU no-load. Prevents shorts, fire, electrocution. Addresses U-001's "completed with inspection" clause.
**Prerequisites:** COMPLETED: AF-014 (L), AF-015 (N), AF-016 (PE). All 3 wires attached to both ends. PSU OFF switch, wall plug OUT. No HUB75, no panels, no Nano plugged in at this moment (PSU side 5 V/12 V outputs COMPLETELY UNCONNECTED).
**Blocked by:** AF-014, AF-015, AF-016
**Required hardware:** All 3 AC wires as installed. Nothing else connected to PSU outputs.
**Required tools:** Bright flashlight or lamp; magnifying glass if available; multimeter continuity.
**Exact execution steps:**
VISUAL (check off each):
1. L crimps both ends: no stray strands? (look under light at each ferrule barrel exit + spade exit. A single strand touching adjacent post = L→N short).
2. N crimps both ends: same check.
3. PE crimps both ends: same check.
4. All 3 screws tight? (PSU 3 screws, C14 3 posts = 6 total screws; re-check each for slight turn resistance with screwdriver — should NOT turn easily).
5. Insulation intact: no nicks, no cuts, no crimp-tool damage to insulation near crimps.
6. No exposed copper anywhere except INSIDE the screw clamp (i.e., no bare copper between clamp and insulation).
7. Strain relief candidates: wires route so pulling from outside won't yank screws directly (leave service loop slack that would absorb a tug before tug reaches the clamp).
ELECTRICAL cross-continuity (6 tests):
8. Switch OFF. Multimeter continuity. Test L↔N. Expected: NO beep.
9. Switch OFF. Test L↔PE. Expected: NO beep.
10. Switch OFF. Test N↔PE. Expected: NO beep.
11. Switch ON. Test L↔N. Expected: NO beep (switch ON connects both lines through fuse but only through PSU primary which is high-impedance primary winding; usually no continuity at multimeter low-voltage; if it beeps → SHORT → serious problem).
12. Switch ON. Test L↔PE. Expected: NO beep.
13. Switch ON. Test N↔PE. Expected: NO beep.
14. Wiggle all 3 wires vigorously during the 6 cross-tests (one at a time). No intermittent beep during wiggle on any pair.
15. Write 6 results + wiggle notes on checklist sheet.
**Expected result:** All 7 visual + 13 electrical sub-checks (6 cross, 6 wiggled + per-pair result = 6 checks each side of switch = 12 + wiggle detail) pass.
**Acceptance criteria / DoD:**
- VISUAL: 7 items all PASS. No stray strands anywhere (magnifier-aided).
- ELECTRICAL: All 6 cross-continuity tests → NO beep at both OFF and ON switch positions (6 tests × 2 positions = 12; if any beep → FAIL).
- Wiggle test during cross-checks: no spurious beeps appear during tugs.
- Checklist sheet with 19 items (7V + 12E) ticked & dated.
**Evidence to save:** Checklist sheet photo. Close-up photos of each of the 6 crimps (L-end-1, L-end-2, N-end-1, N-end-2, PE-end-1, PE-end-2 = 6 macro photos).
**Safety considerations:** JIRA Safety Rules (1) 8-point checklist VERBATIM applies. Pre-energize audit. No energizing occurs here.
**Known uncertainties:** U-001 (fully resolved here if all pass).
**Failure response:** ANY visual or electrical FAIL → DO NOT energize (EXP-002 must wait). Identify the bad crimp or post. Undo, re-crimp, re-do AF-017 from scratch. Do not guess "maybe OK".
**Source references:** JIRA.md Safety Rules (1/4) 8-point pre-energize; PROTOTYPE_WIRING §1.1-§1.3.
**Labels:** hardware safety-review power blocked blocked:exp-af-016
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-018: PSU no-load energize EXP-002 (wall plug in, switch ON, ALL OUTPUTS DISCONNECTED — measure 5 V, 12 V, 10 min hold)

**Task ID:** AF-018
**Epic:** AF-001
**Summary:** First mains energize ever. PSU outputs fully disconnected. EXP-002 full procedure.
**Description:** Execute EXP-002 PSU no-load. Wall plug in. All 5 V and 12 V outputs on PSU remain UNCONNECTED (no panels, no Nano, no controllers). Verify 5 V = 5.00–5.20 V; 12 V = 12.00–12.40 V; stable ±0.05 V for 10 minutes; PSU housing ≤ 40°C; no hum, no smell, no smoke. This is the FIRST time the wall plug goes IN. Maximum caution.
**Why this task exists:** R044 PSU voltage tolerance check; EXP-002 procedure; U-022 cold start unknowns (inrush → no-load first so inrush limited to PSU internal); proves PSU works before panels/electronics are attached.
**Prerequisites:** COMPLETED: AF-017 (visual + cross-check all PASS). PSU 5 V / 12 V outputs: completely naked (no spade connectors installed on 5 V output yet). Work area non-conductive. Fire extinguisher close. Someone else within earshot if possible (buddy system preferred but solo OK with documented steps).
**Blocked by:** AF-017
**Required hardware:** All AC wiring done as-is. No DC loads attached whatsoever.
**Required tools:** Multimeter DC voltage mode. IR thermometer if available (otherwise finger-back-of-hand 2-second test; IR preferred). Stopwatch or phone timer.
**Exact execution steps (EXP-002 verbatim):**
1. Confirm: 5 V output posts bare. 12 V output posts bare. Not a single DC wire connected. (If any DC wire connected → STOP, remove it now, go back to AF-017.)
2. Confirm PSU switch (integrated into C14 assembly) = OFF position.
3. Plug C13 cord into wall socket first. Then plug other end into C14 inlet. (Order: wall first so C13 ground pin makes contact before any live prongs.)
4. Stand to the side of the PSU (not face-on over it, 30 cm back). With one finger, flip C14 switch to ON. — — — Observe immediately: any pop? Any spark? Any smoke? Any burning smell?
5. If any pop/spark/smoke/smell IMMEDIATELY: flip switch OFF, yank wall plug out. Proceed to Failure response.
6. If no pop/spark/smoke/smell: wait 30 seconds. Observe: hum acceptable? (faint 50/60 Hz hum normal; loud buzz = abnormal).
7. Multimeter DC volts mode. Red probe → PSU 5 V output + terminal (labeled 5V+ or V1+). Black probe → PSU COM or 5V- terminal. Record reading in table.
8. Multimeter: red → 12 V + terminal (labeled 12V+ or V2+). Black → COM. Record reading.
9. Set stopwatch = 10 minutes. Sit near setup but do NOT touch anything. Do not leave the room. Do not get distracted.
10. At t = 1 minute: re-measure 5 V and 12 V. Log.
11. At t = 5 minutes: re-measure. Log.
12. At t = 10 minutes: re-measure. Log. PSU housing top: IR temp (or back of hand test 2 seconds). Record.
13. End of test. Switch = OFF. Yank wall plug out.
**Expected result:** 5 V stays between 5.00–5.20 V; 12 V = 12.00–12.40 V; all 4 time-points (0,1,5,10 min) within tolerance and delta from 0→10 ≤ 0.05 V. PSU temp ≤ 40°C (or back of hand: warm to touch, NOT hot). No smells, no smoke, no sparks.
**Acceptance criteria / DoD:**
- 5 V tolerance: 4 measurements all in 5.00–5.20 V range.
- 12 V tolerance: 4 measurements all in 12.00–12.40 V range.
- Stability: max 5 V drift ≤ 0.05 V over 10 min.
- No sparks/smoke/smell/hot at any point.
- Final PSU temp ≤ 40°C (or warm, not hot).
**Evidence to save:**
- `docs/pm/evidence/AF-018-exp-002-psu-no-load.md` = measurement table (t=0/1/5/10 for both rails), PSU temp, observations
- Photo of multimeter showing each of the 4×2 = 8 voltage readings (or 1 composite if 8 screenshots impractical; take at least t=0 + t=10 for both rails = 4 photos)
- PSU case top IR temp photo if IR thermometer available
**Safety considerations:**
JIRA Safety Rules (1/4) mains checklist VERBATIM:
1. Wall plug accessible for quick yank-out throughout (taped extension or clear path — no tripping).
2. Buddy system: another human in earshot.
3. No face-over-PSU at switch-on.
4. Load-less: NO DC outputs connected.
5. If ANY anomaly → OFF + yank plug within 1 second.
6. All prior inspections (AF-017) already passed.
7. Fire extinguisher visible and operator knows how to use.
8. Every measurement logged in real time (not memory).
⚠️ Also applies: HUB75/PSU power ordering is not relevant here (no electronics attached yet), but keep the discipline: energize PSU first, then later electronics/controller, then panels = for all FUTURE power-ups, memorize this order.
**Known uncertainties:** U-022 (PSU cold start, inrush, cold stability). If any anomaly → escalates U-022 severity to safety-critical.
**Failure response:**
Immediate anomaly → OFF + yank plug.
Smoke/smell → do NOT re-energize. PSU likely defective. RMA to BOM vendor.
5 V out of spec >5.3 V = overvoltage → PSU regulator suspect. RMA. <4.9 V at no-load = already sagging = PSU suspect. RMA.
Loud buzz → mechanical resonance. May be OK after mounting (case dampens), but if combined with temp >50°C = suspect. Record anomaly; proceed with caution; monitor during EXP-015 loaded.
**Source references:**
- EXPERIMENTS.md EXP-002 §Procedure, §Measurements table (4 time-points × 2 rails = 8 cells), §Success criteria (2 rail ranges + stability + temp + no smell)
- Requirements matrix R044 (no-load + loaded)
- Uncertainty U-022
**Labels:** hardware safety-review power validation blocked blocked:exp-af-017
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-019: VERIFY panel power harness branch #1 polarity — PSU OFF (confirm: red wire = V+ to 5 V+ post contact; black/other = V-; continuity on correct pins; reversed = FAIL)

**Task ID:** AF-019
**Epic:** AF-001
**Summary:** Before any panel power connection: verify harness #1 end-to-end polarity & pin positions with PSU OFF.
**Description:** This is a dedicated power-OFF polarity verify task for panel harness #1 → panel #1 4-pin Molex END → mating to panel. 100% PSU disconnected. Multimeter on continuity. End to end: PSU 5 V+ screw post (which the harness's red pin will connect to) ↔ panel-connector's V+ pin. Expected continuity. Similarly PSU COM/5V- ↔ panel-connector V-. Cross (V+ of harness ↔ V- of connector): NO continuity. Connector gender: confirm the harness mating connector cannot plug in backwards (keyed shroud). If NOT keyed → add an extra physical "pin 1 index mark" with a Sharpie before ANY plug-in event. Rationale: design spec §4b extra-fine on polarity.
**Why this task exists:** Safety-critical polarity check BEFORE first plug-in of panel. Prevents panel damage (reverse polarity → instant death = $/panel expensive). Resolves U-002 (harness/panel connector).
**Prerequisites:** COMPLETED: AF-018. PSU OFF + wall plug out. Panel #1 on bench, rear accessible. Harness #1 in hand.
**Blocked by:** AF-018
**Required hardware:** Panel power harness × 1 (BOM H-04a or H-04b first harness, first branch), panel #1 4-pin power connector visual identification, PSU.
**Required tools:** Multimeter continuity mode. Sharpie marker.
**Exact execution steps:**
1. Wall plug out. PSU switch OFF. Ensure no residual voltage in PSU (wait 60 s).
2. Locate harness branch #1 (the branch intended for panel #1; label it now with Sharpie "PANEL-1" on both 4-pin connector and wire bundle).
3. Harness wire colors: look at wires. Normally: RED = V+, BLACK = V- (GND). Confirm: are there two extra wires? (No — 4-pin connector on panels typically has 2 power, 2 spares or 2 parallel for current capacity.)
4. Identify harness's 4-pin panel-side connector: which pin position (pin 1 = corner, marked with raised index on connector body) corresponds to which wire?
5. Identify panel #1's 4-pin power input: silk screen should show "+5V" and "GND" labels near the pins. If NO labels → flag = Uncertainty (log this; continue test by deducing from diode-protection or cap polarity: electrolytic cap near connector has stripe on negative → GND).
6. Multimeter continuity. Touch probe A to harness's wire-end terminal that will attach to PSU 5 V+ screw post. Touch probe B to harness panel connector's PIN that will mate to panel V+ (by marking from step 4). Expected: BEEP (V+ goes straight through).
7. Probe A = harness wire for PSU COM/5V-; probe B = panel connector GND pin. Expected: BEEP.
8. Cross-test: probe A = 5 V+ wire end; probe B = panel connector GND pin. Expected: NO beep (no cross short).
9. Cross-test reverse: probe A = 5 V- wire end; probe B = panel connector V+ pin. Expected: NO beep.
10. Check connector SHROUD: is panel connector keyed (plastic shroud prevents reverse insertion)? Is harness connector keyed matching?
    a. If both keyed and match → OK.
    b. If un-keyed → add Sharpie alignment mark: a big thick arrow on top side of BOTH panel connector and harness connector bodies so alignment is obvious during plug-in.
11. (Advanced, if possible with pins): Plug the harness connector ONTO panel #1 now, but PSU side NOT connected to PSU. Panel side connector plugged. Then do a post-plug continuity from harness wire end → panel body's actual power input pins (on rear of panel PCB: V+ cap +, GND cap -): confirm continuity correct for 2 correct paths, cross no continuity.
12. Write results down on harness #1 bag label.
**Expected result:** Correct 2 paths beep; 2 cross paths silent; keyed OR Sharpie-marked for unambiguous orientation.
**Acceptance criteria / DoD:**
- V+ path (PSU 5V+ wire end → panel V+ pin): beep.
- V- path (PSU 5V- wire end → panel V- pin): beep.
- Cross paths: no beep.
- Panel connector orientation unambiguous (keyed or marked).
- Post-plug optional step 11 confirms same result.
**Evidence to save:**
- Photo: harness 4-pin connector marked orientation, panel connector with matching mark
- Notes: wire color mapping, step 6-9 pass/fail
**Safety considerations:**
PSU unplugged. No energized parts.
⚠️ Future plug-in note: BEFORE you ever connect PSU 5 V+ screw post to harness wire end, you will repeat the cross-continuity one more time. This task does NOT install the harness to PSU. It verifies the harness's internal correctness and orientation.
**Known uncertainties:**
- Resolves U-002 (panel #1 polarity mapping).
- U-025 (fuse rating per branch): if harness fuses are visible, record fuse rating of branch #1 now; may resolve U-025 partially.
**Failure response:**
If any cross beep → harness has internal short → DO NOT USE THIS BRANCH. Use next available spare branch (spares per ADR-022) and re-verify. If all harness branches bad → return harness to maker.
If orientation ambiguous → always Sharpie mark.
**Source references:**
- PROTOTYPE_WIRING.md §3 PanelPolarity: "Verify both harness-end AND panel-end markings match expected V+/V- color code"
- Requirements matrix R019 (polarity verify OFF before energize), R052
- Uncertainty U-002
- Design spec §4b extra-fine polarity splits
**Labels:** hardware safety-review polarity-verify power blocked blocked:exp-af-018
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-020: VERIFY panel #1 4-pin power connector polarity at panel end (multimeter PSU OFF — confirm markings align with actual cap polarity on PCB V+/V- pins)

**Task ID:** AF-020
**Epic:** AF-001
**Summary:** Verify panel #1 PCB-side 4-pin input V+/V- labels actually correspond to PCB's bulk cap +/-. Confirm before first power.
**Description:** Parallel to AF-019 but on the panel's PCB side. AF-019 verifies the harness internal. This verifies the panel's own power input matches its silk labels. Novice-level: assume silk labels might be WRONG (cheap manufactured = sometimes silks reversed). Use the electrolytic capacitors near the power connector. Electrolytics have a WHITE/COLORED STRIPE on the NEGATIVE side. So: the pin connected to the NEGATIVE side of a big cap = GND/V-. The pin connected to the OTHER side (no stripe, positive lead of cap) = V+. Compare that to the silk label. Do they match?
**Why this task exists:** Safety/sanity check. If reversed = instant panel dead on first power. U-002 (panel side polarity).
**Prerequisites:** COMPLETED: AF-011 (panel identified).
**Blocked by:** AF-011
**Required hardware:** Panel #1 only.
**Required tools:** Multimeter continuity.
**Exact execution steps:**
1. Panel #1 on bench, rear up. Find 4-pin Molex input connector.
2. Locate the nearest large electrolytic capacitor. Usually 100 µF 10 V or 220 µF 16 V.
3. Identify cap's WHITE STRIPE = negative side.
4. Multimeter continuity. Probe A = cap negative leg (the side with the stripe). Probe B = each of the 4 panel power connector pins, one by one.
5. The pin that beeps is V-/GND. That pin should correspond to "GND" / "V-" / "-" silk label near the connector.
6. Probe A = cap positive leg (opposite stripe). Probe B = other 3 pins one by one. The beep is V+/5V. Should say "5V" / "+" on silk.
7. Write mapping: pin # (if pin numbers are visible, by corner index) → V+ and V-.
8. Compare to silk labels. Do they agree?
**Expected result:** V- beep = silk V-; V+ beep = silk V+.
**Acceptance criteria / DoD:**
- Electrolytic-cap derived V+ and V- pins AGREE with silk labels on panel PCB. If disagree → panel silks wrong = label carefully with Sharpie CORRECT polarity on panel rear = the actual truth (not the silk).
- No pins beep on both (would mean short on PCB → panel defective).
- The correct mapping is documented with Sharpie on panel rear for all future plug-in events.
**Evidence to save:** Photo of panel rear PCB showing cap stripe + connector, with annotated arrows to correct pins + Sharpie mark after labeling. Notes file AF-020.
**Safety considerations:** No power. Mechanical only.
**Known uncertainties:** Resolves panel-side portion of U-002.
**Failure response:** If cap-short (V+ beep and V- beep on same pin = shorted cap or PCB) → DO NOT POWER. Return panel. If silk disagrees → Sharpie the truth. Mark panel clearly so any future human reads the Sharpie, not silk.
**Source references:** R019, U-002, R052.
**Labels:** hardware polarity-verify safety-review blocked blocked:exp-af-011
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-021: Panel 1 power test EXP-003 (PSU → harness #1 → panel #1. PSU energize. Measure PSU voltage, AT PANEL voltage, temps. 10-15 min. Panel LEDs off: expected, no data)

**Task ID:** AF-021
**Epic:** AF-001
**Summary:** Connect panel #1 only to 5 V power (no data). EXP-003. No HUB75 data. First panel power ever.
**Description:** Execute EXP-003 verbatim. PSU OFF + wall plug out. Attach harness #1 branch #1 from PSU 5 V + / COM outputs → panel #1 4-pin (with correct orientation confirmed by AF-019 + AF-020). Double-check polarity ONE MORE TIME just before plugging PSU side. Then PSU power sequence ON. 5 V measure at PSU output and AT PANEL CONNECTOR SOCKET (back-probe if possible to avoid disconnection; or measure after 1 min when opening case if panel allows). Wait 10-15 min. Temperatures. Panel LEDs all OFF = expected (no HUB75 data → panel chips do nothing). No damage.
**Why this task exists:** EXP-003. Proves panel 1 + harness branch + PSU load (1 panel light load) works. R044 light load baseline.
**Prerequisites:** COMPLETED: AF-018 (no-load OK). COMPLETED: AF-019 (harness polarity). COMPLETED: AF-020 (panel polarity). COMPLETED: HUB75 side NOT connected (leave it off — only POWER connected now).
**Blocked by:** AF-018, AF-019, AF-020
**Required hardware:** PSU, harness #1, panel #1.
**Required tools:** Multimeter DC voltage, IR thermometer or hand.
**Exact execution steps (EXP-003 verbatim):**
1. Wall plug OUT. C14 switch OFF.
2. Harness panel-side connector → panel #1 4-pin. Check Sharpie marks aligned. Plug-in.
3. Harness PSU-side wire ends → PSU: RED → 5 V+ screw post. BLACK → PSU COM/5V- post. Tighten screws.
4. Double-check polarity: RED=5V+, BLACK=V- on PSU; confirmed by prior tasks.
5. HUB75: NOT connected. Confirm no HUB75 anywhere near panel at this moment.
6. Power-up ORDER (per JIRA.md HUB75 rule #2, applied to power):
   a. Plug wall cord in.
   b. Stand aside. Flip C14 switch ON.
7. Watch 10 seconds: any smoke? Panel LED glow = abnormal? No panel LEDs should be on.
8. If no smoke: wait 1 min.
9. Measure PSU 5 V at posts. Log.
10. Measure panel-side: if 4-pin connector accessible for back-probing (use fine probe tips on wires), record V+ / V- across panel input terminals. Should be ≥ 4.85 V (allowing wire drop for light load).
11. Run 10–15 minutes.
12. At end: measure PSU 5 V again, panel input V again, PSU housing temp, wire-to-panel connector plastic temp (hand test).
13. POWER DOWN ORDER: Switch OFF → wall plug OUT → wait 30 s before handling connections.
**Expected result:** Voltages within limits. No smoke. Panel LEDs OFF (no data = expected blank). All temps ambient or slightly warm (< 40°C).
**Acceptance criteria / DoD:**
- PSU 5 V at all times in 4.90–5.20 V under this load.
- Panel input ≥ 4.85 V (≥ 4.75 V is USB spec; panel needs similar; use 4.85 as tighter no-load/light-load).
- No smoke, no pop, no smell.
- Panel LEDs: OFF / no random pixels on.
- Temps: PSU case warm; connector plastic ambient = OK.
**Evidence to save:** EXP-003 log. Voltage photos. Panel photo during test (dark = no pixels).
**Safety considerations:**
JIRA mains 8-point verbatim: wall plug accessible, buddy system.
JIRA HUB75 power order rule: PSU ON → then later controller/data → then panels. (Today: NO data, only power; panels are powered before any data, which is acceptable because no HUB75 = no hot plug.)
⚠️ FUTURE: NEVER plug/unplug HUB75 while PSU ON (no hot plug). Today, no HUB75 = fine.
5 V high-current rules JIRA Safety (4): No Dupont connectors used for 5 V here (harness uses correct 4-pin). No full-current in series through multimeter probes during this task (we measured at PSU posts and back-probed panel socket with near-zero current draw on probes = OK).
**Known uncertainties:** U-022 (cold start inrush: monitored). U-027 (contribution in current draw measured indirectly by voltage drop).
**Failure response:** Smoke → OFF + yank. Reverse polarity suspected → disconnect harness, check markings. Dead panel → return.
**Source references:**
- EXPERIMENTS.md EXP-003 §Procedure 1-8, §Success criteria
- Requirements matrix R044, R017
- JIRA Safety (1), (2), (4)
**Labels:** hardware safety-review power validation blocked blocked:exp-af-020
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-022: VERIFY HUB75 cable + panel #1 IN connector orientation (keyed notch, printed IN label vs OUT, continuity on GND positions — PSU OFF, no power)

**Task ID:** AF-022
**Epic:** AF-001
**Summary:** Verify panel #1 HUB75 input data direction; HUB75 cable keyed notch both ends; GND pin continuity between cable ends. PSU OFF.
**Description:** PSU OFF, no power. Dedicated polarity-verify for HUB75. Three sub-checks: (A) Panel HUB75 receptacle — which side is "IN"? Should have printed "IN" label next to one receptacle, "OUT" next to the other (for daisy-chain). If NO labels → test data direction by plugging into one then the other on a later task; this task flags the missing label. (B) HUB75 0.5 m cable × 1: both ends have the keyed notch? Plug into the "IN" only, make sure the keyed shroud fits and the notch aligns. (C) Cable continuity: using multimeter through cable, GND pins on PIN_LEVEL_APPENDIX §5 layout position GND (positions: usually pin 16 and pin something = 2 GND positions total; see §5 for exact). Touch probes to the corresponding positions at both ends of cable. Beep = good.
**Why this task exists:** Extra-fine polarity verify for HUB75. Hot-plug rule + mis-wired data direction = garbled display or dead panel. Resolves U-002 data-direction component + U-031 exact panel pinout orientation.
**Prerequisites:** Panel #1 available, HUB75 cable 0.5 m, PIN_LEVEL_APPENDIX §5 for exact GND positions, PSU OFF wall plug out.
**Blocked by:** AF-011 (cables present).
**Required hardware:** Panel #1, HUB75 cable 0.5 m 1 pc.
**Required tools:** Multimeter continuity.
**Exact execution steps:**
1. Find panel rear 2 HUB75 headers. Look next to each for silk "IN" / "OUT". Record. If no labels → flag in Known uncertainties.
2. Take HUB75 0.5 m cable. Inspect both plugs: keyed plastic ridge (the notch that prevents 180° reversal) is present on both ends? Y/N. If NOTCH MISSING → file bug: mark cable with Sharpie "top-side" arrow BOTH ENDS so orientation is unambiguous.
3. Plug cable (loosely, hand-held) into "IN" header of panel. Ensure keyed notch engages. If it doesn't engage → wrong direction or cable reversed.
4. Unplug cable. Continuity check cable: PIN_LEVEL_APPENDIX §5 lists pin positions of the 2 GNDs. Hold cable plug A and plug B side by side, same orientation. Probe pin A-GND-position → plug B same GND-position → beep.
5. Do both GND positions.
6. Also verify a non-GND pair (say R1 position at plug A → R1 at plug B = beep also; but NOT GND → R1 = no beep). This proves the cable isn't shorted internally.
**Expected result:** IN label present on panel 1 header; cable notch both ends; 2 GND positions beep correctly; non-GND correct not cross-GND.
**Acceptance criteria / DoD:**
- "IN" header identified (labeled OR by convention: controller-side always goes to panel's IN, not chain-OUT between panels).
- Cable notches 2/2 present. If missing → Sharpie marked.
- 2/2 GNDs continuity.
- 1 cross-check no-short pass.
**Evidence to save:** Photos of labels, notch, Sharpie marks. Continuity notes.
**Safety considerations:** PSU OFF. No power anywhere. Pre-requisite for every future HUB75 plug-in = operator is aware of IN vs OUT + notch.
**Known uncertainties:** U-031 (exact pin positions: we only verified GNDs here; full 14-signal verification done during HCT build stage 12 for ESP32; WF4 built-in HUB75 output pins: verified in EXP-004).
**Failure response:** Cable internal short → throw cable away, use next HUB75 cable of same length. Missing IN label → mark panel rear with Sharpie "IN" on the correct header (choose: header closest to power connector usually IN).
**Source references:**
- PROTOTYPE_WIRING.md §5 HUB75 inventory: "Verify 'IN' label on panel input; verify data-flow left→right each row"
- PIN_LEVEL_APPENDIX.md §5 HUB75 2×8 keyed 16 pin layout
- Requirements matrix R052 (orientation verify task before every first plug)
- JIRA.md Safety Rules (2/4): "Never hot-plug panels/controller/PSU" → prerequisite for correct future behavior is orientation knowledge established now.
**Labels:** hardware polarity-verify safety-review blocked blocked:exp-af-011
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-023: Panel polarity verify batch (panels #2 through #6 — 5 more polarities, all PSU OFF)

**Task ID:** AF-023
**Epic:** AF-001
**Summary:** Apply AF-019 + AF-020 procedure to panels #2, #3, #4, #5, #6. Batch verify all 5 panels. All PSU OFF.
**Description:** Repeat AF-019 (harness-end polarity) for harness branches #2…#6 AND AF-020 (panel-side PCB cap polarity) for each of the 5 remaining panels. Each result is logged per-panel. Do NOT energize any of these yet. This task = verification only; power tests for these panels come in M2/M3/M4 when their controller chain actually drives them.
**Why this task exists:** Saves time by batching 5 polarity verifies. The DoD still ENUMERATES each panel individually per JIRA.md novice rule. Safety-critical per U-002.
**Prerequisites:** COMPLETED: AF-019, AF-020 (method validated on panel #1). 5 harnesses available (or re-use the same harness per panel for test = OK since we only probe).
**Blocked by:** AF-019, AF-020
**Required hardware:** Panels #2-#6. Harness (one is enough for all 6 off-line tests; if 6 branches each have their own connector then test each branch to its own mating panel).
**Required tools:** Multimeter continuity + Sharpie.
**Exact execution steps:**
For each panel i = 2…6:
1. Panel i PCB polarity verify (AF-020 procedure: V- = cap stripe negative, V+ = opposite; compare to silk; Sharpie mark if wrong).
2. If a dedicated harness branch #i exists: harness branch i internal polarity (AF-019 procedure, including cross-test no-short, Sharpie orientation arrow if un-keyed on 4-pin power connector).
3. Panel i HUB75 "IN" header label (AF-022 step 1 procedure) → confirm label exists and note which header.
4. Log each.
**Expected result:** 5 panels × 3 checks each = 15 sub-results. All pass.
**Acceptance criteria / DoD:**
- For panel 2: panel polarity pass, harness branch pass, IN header identified.
- For panel 3: same 3 pass.
- For panel 4: same.
- For panel 5: same.
- For panel 6: same.
- All 15 enumerated in evidence; no aggregation.
**Evidence to save:** Per-panel 5 rows of pass/fail in AF-023 notes file. Photos of any required Sharpie corrections.
**Safety considerations:** No power. All OFF. This is batch verification.
**Known uncertainties:** U-002 (resolved in full for all panels if all pass).
**Failure response:** Any per-panel fail → handle individually the same as AF-019/AF-020 failure response. Does not block the other 5 panels from verification.
**Source references:** Same refs as AF-019, AF-020, AF-022. Design spec §4b.
**Labels:** hardware polarity-verify safety-review blocked blocked:exp-af-022
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-024: (M0 summary review) — EXP-001/EXP-002/EXP-003 evidence review + BOM.md status update commit

**Task ID:** AF-024
**Epic:** AF-001
**Summary:** M0 aggregator. Review all M0 evidence. Commit BOM.md status updates. Confirm M0 exit criteria met so M1 can start.
**Description:** Review all AF-011 through AF-023 outputs. Check all evidence files committed. Flip BOM.md statuses ORDERED→RECEIVED (from AF-011). Commit the BOM update + any doc edits made in M0 with a single commit message `docs(m0): M0 exit — inventory + c14 routing + polarity prechecks + exp-002/003 pass`. Moves M0 exit criteria from "tasks done" to "evidence committed, exit gate passed."
**Why this task exists:** Epic-completion documentation & commit discipline.
**Prerequisites:** COMPLETED: AF-023.
**Blocked by:** AF-023
**Required hardware:** None.
**Required tools:** Git.
**Exact execution steps:**
1. Review checklist of AF-011 through AF-023 Evidence-to-save for each task.
2. For each task, confirm the evidence file exists in the appropriate directories.
3. Edit BOM.md: all rows STATUS=ORDERED → RECEIVED (except items that were NOT in shipment per AF-011 short-ship list; those stay ORDERED and are annotated "SHORT-SHIP RESOLUTION PENDING").
4. `git add BOM.md docs/pm/*.md docs/photos/*` as needed.
5. Commit.
**Expected result:** One git commit that captures all M0-produced work.
**Acceptance criteria / DoD:**
- All 14 tasks (AF-011…AF-024) have evidence save directories non-empty (or documented elsewhere).
- BOM.md statuses updated to reflect reality.
- One commit hash generated.
- M0 exit criteria satisfied (per AF-001 Epic exit criteria): EXP-001 done; C14 + safety OK; EXP-002 pass; EXP-003 pass.
**Evidence to save:** Commit hash.
**Safety considerations:** N/A.
**Known uncertainties:** None new.
**Failure response:** Missing evidence → go back to the source task and produce it. Do not mark M0 done without evidence.
**Source references:** AF-001 exit criteria. M0 Gantt context.
**Labels:** docs validation delivery-review blocked blocked:exp-af-023
**Status flags:**
critical-path: yes
Conditional: no

---

## End of M0. Start of M1 Epic AF-002: One Nano-Driven Arbitrary-Text Panel — 🔒 GATED MILESTONE

Parallel subtracks inside M1:
- Subtrack (a): Nano SW Bootstrap (fully independent, software only, no hardware needed)
- Subtrack (b): WF4 Controller
- Subtrack (c): ESP32 Controller (including 12-stage HCT perfboard build)
- Subtrack (d): WF2 Experimental Reference (spike, non-critical)
- Aggregator: M1 GATE-PASS task (at LEAST one of subtrack b or c passes)

---

## M1 Subtrack (a): Nano SW Bootstrap (software only, independent, may run before hardware)

---

### AF-025: Flash LicheeRV Nano-W microSD card (download image, write, verify)

**Task ID:** AF-025
**Epic:** AF-002
**Summary:** Flash Nano's Debian 12 (Bookworm) headless image onto 32 GB min microSD.
**Description:** Download latest official LicheeRV Nano-W Debian Bookworm headless image from Sipeed/LicheeRV download page. Verify checksum. Flash to class-A2 32 GB microSD (BOM S-01) via balenaEtcher or `dd if= of=/dev/mmcblkX bs=4M status=progress conv=fsync`. Verify: eject + re-insert, check partitions visible (boot + root). Store microSD in Nano's slot. This task is software-only + a PC. FULLY INDEPENDENT OF M0 HARDWARE DELIVERY — YOU CAN DO THIS BEFORE THE BOX ARRIVES.
**Why this task exists:** R012 (Nano compute subsystem), ADR-008, ADR-010 (microSD min 32 GB), ADR-011 (Bookworm headless).
**Prerequisites:** A Linux/Mac/Windows PC with microSD writer + internet. MicroSD card on hand (BOM S-01).
**Blocked by:** — (delivery independent; only a microSD + a PC is needed; not BLOCKED: DELIVERY because Nano box includes microSD; microSD ordered with BOM, but user could use a spare if needed)
**Required hardware:** BOM S-01 microSD (or any 32 GB+ Class A2), microSD reader/writer, a separate host PC.
**Required tools:** balenaEtcher GUI or raw `dd`. sha256sum tool.
**Required software:** Exact Nano image URL: from Sipeed official docs. LicheeRV Nano-W Debian 12 Bookworm headless.
**Exact execution steps:**
1. Visit LicheeRV Nano-W doc page → downloads → "Debian" → headless/server variant.
2. Download .img.xz + sha256sum.txt.
3. `sha256sum -c sha256sum.txt image.img.xz` or equivalent → OK.
4. Insert microSD into writer. Determine device path `/dev/diskN` (macOS) or `/dev/mmcblkX` (Linux) or `\\.\PhysicalDriveN` (Windows). NO mounted partitions.
5. Etcher GUI: select image, select card, flash. Or xzcat + dd.
6. Flash complete. Eject safely. Remove + re-insert.
7. Confirm 2 partitions visible: small FAT boot partition (~100 MB) + ext4 rootfs partition (~several GB).
8. Insert microSD into Nano's microSD slot.
**Expected result:** Image boots (we verify boot in AF-026). Flash verified by Etcher checksum (or dd + fsync). Card snaps into Nano slot.
**Acceptance criteria / DoD:**
- Download checksum verified.
- Flash complete; verify re-insert sees 2 partitions.
- microSD in Nano.
**Evidence to save:**
- Screenshot of Etcher "Flash Successful" or dd status.
- Screenshot of re-inserted microSD showing 2 partitions in Disk Utility / `lsblk`.
- Filename + URL of the image downloaded + sha256sum of image recorded in AF-025 notes.
**Safety considerations:** N/A — software only on host PC. Do NOT have Nano connected to CH340 for power during this task (CH340 power NEVER connected anyway, per JIRA Safety 3).
**Known uncertainties:** None. Download link may 404 if docs move → search alternative: use the direct Sipeed dl.sipeed.com bucket.
**Failure response:** Flash fails → try new microSD. If 2nd card fails → replace SD writer.
**Source references:**
- DECISIONS.md ADR-008 (LicheeRV Nano-W selected), ADR-010 (microSD ≥32 GB), ADR-011 (Bookworm headless — docker mentioned but ADR-023 says no for V1)
- Requirements matrix R012, R025
- BOM.md S-01 microSD
**Labels:** software nano docs blocked blocked:delivery (needs PC + microSD; not blocked by full BOM shipment) critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-026: Nano first boot + serial debug via CH340 (TX/RX/GND 3 pins only — NO 5V/3.3V power from CH340 to Nano. Nano powered from own 5 V fused branch.)

**Task ID:** AF-026
**Epic:** AF-002
**Summary:** Serial console into Nano, boot Bookworm, confirm kernel boots, login prompt appears. CH340 power lines NOT connected.
**Description:** Plug CH340 into host PC USB-A. Use 3 Dupont female-female wires ONLY: CH340 TX → Nano UART RX; CH340 RX → Nano UART TX; CH340 GND → Nano GND. IMPORTANT: DO NOT CONNECT CH340 5 V OR 3.3 V HEADER PINS TO NANO AT ALL. Leave them unpopulated in the jumper wire. Nano's 5 V comes from its own fused 5 V branch via USB-C power supply (use a 5 V 3 A capable USB-C wall adapter during bench work — a phone charger is sufficient for this software-only phase; later Nano moves onto its assigned fused harness branch). Open PuTTY/minicom/screen at 115200 baud 8N1 no-flow-control. Plug Nano power → observe boot. Login with default credentials (sipeed docs).
**Why this task exists:** R012 compute, R022 CH340 safety (3-wire rule), ADR-008.
**Prerequisites:** COMPLETED: AF-025 (card flashed). Nano + CH340 + USB-C power supply + USB-A to USB-C CH340 cable (or CH340 has USB plug). 5 V 3 A phone charger capable for Nano. (NOT using PSU harness yet — we use bench charger first.)
**Blocked by:** AF-025
**Required hardware:** Nano, microSD (from AF-025), CH340 USB-UART adapter, 3× Dupont F-F jumper, USB-C cable + 5 V 3 A USB-C power (charger), host PC.
**Required tools:** Terminal program on host PC.
**Required software:** minicom/screen/PuTTY at 115200 8N1.
**Exact execution steps:**
1. Nano OFF. CH340 NOT in PC USB yet.
2. Wire TX/RX/GND only: CH340 TX → NANO RX; CH340 RX → NANO TX; CH340 GND → NANO GND. Cross-check: CH340 has a 4-pin header block VCC/TX/RX/GND — PHYSICALLY REMOVE the VCC pin from the 4-pin block if possible (pull it out with pliers), or use a 3-pin cable that doesn't reach VCC. Zero tolerance for VCC line connected accidentally.
3. CH340 into PC USB.
4. Terminal program: open `/dev/ttyUSB0` (Linux) or `/dev/tty.usbserial-XXX` (mac) or COM3 etc. at 115200 8N1 no hardware flow no software flow.
5. Plug Nano USB-C power.
6. Watch terminal. Kernel messages should scroll. After ~30-60 s: login prompt.
7. Default user: sipeed / password from docs (usually "licheepi" or "sipeed"). Record actual in notes.
8. Run `uname -a`, `cat /etc/os-release`, `free -h` (confirm 2 GB total LPDDR4).
9. Proper shutdown: `sudo poweroff`. Wait for LED stop. Unplug USB-C power. Unplug CH340.
**Expected result:** Full boot, login works, 2 GB RAM seen, Bookworm OS ID correct.
**Acceptance criteria / DoD:**
- NO CH340 VCC pin connected → inspect wiring BEFORE and AFTER power. Visual inspection passes.
- Nano boots → kernel boot messages visible.
- Login prompt appears.
- Credentials work.
- 2 GB RAM (± some reserved; 1.7 GB+ OK).
- Bookworm string present in /etc/os-release.
**Evidence to save:**
- Screenshot: terminal session from boot → login → `uname -a` → `free -h` → `cat /etc/os-release` → shutdown.
- Photo: physical wiring showing ONLY 3 wires connected (CH340 VCC pin visible empty = unpopulated). CRITICAL PHOTO.
- Boot log saved as text if possible: copy/paste into AF-026 notes.
**Safety considerations:**
VERBATIM JIRA Safety Rules (3/4) CH340 rules:
1. Only connect TX/RX/GND (3 pins). ALWAYS.
2. NEVER connect 5 V or 3.3 V power from CH340 to independently powered target.
3. Disconnect CH340 before powering target from PSU (we used bench charger; rule is similar).
4. If debugging multiple ESP32s later (not this task): need separate isolated USB-UART per board.
⚠️ Critical enforcement: the "VCC pin empty" photo is REQUIRED evidence. No photo = task fails audit later.
**Known uncertainties:** None.
**Failure response:** No boot → re-seat microSD; try re-flash (AF-025); check TX/RX crossed (swap TX/RX = common fix). Garbage characters → wrong baud (use 115200).
**Source references:**
- JIRA.md Safety (3/4)
- BOM C-01 Nano, C-07 CH340
- ADR-008, ADR-011
**Labels:** software nano safety-review blocked blocked:exp-af-025
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-027: Nano Wi-Fi configuration + SSH connectivity test (set SSID/password via nmtui or nmcli or /etc/NetworkManager/system-connections; mDNS hostname ai-frame.local; SSH from host PC works)

**Task ID:** AF-027
**Epic:** AF-002
**Summary:** Connect Nano to home/office Wi-Fi. Enable SSH. Test mDNS host resolution ai-frame.local from host.
**Description:** Wi-Fi config. Static IP (not DHCP random) preferred, or DHCP reservation in router (whichever is easier for executor's home router). mDNS (avahi) so host can `ssh sipeed@ai-frame.local`. Change default password to executor's own password. Set up SSH keys from host so password not needed every time. R025 network harden step 2 of SW plan.
**Why this task exists:** R025 network harden; Nano SSH access needed for all later dev steps.
**Prerequisites:** COMPLETED: AF-026 (Nano boots, login shell works).
**Blocked by:** AF-026
**Required hardware:** Nano with USB-C power, CH340 serial connected one last time during Wi-Fi setup.
**Exact execution steps:**
1. Boot Nano, login via serial.
2. Change default user password immediately: `passwd`.
3. Wi-Fi: `nmcli device wifi list` → confirm Nano-W sees the target SSID.
4. Connect: `nmcli device wifi connect SSID password WIFI_PASSWORD`.
5. Verify `ip addr show wlan0` → IPv4 assigned.
6. Router: set static IP or DHCP reservation for this MAC so address never changes.
7. Install openssh-server: `sudo apt update && sudo apt install -y openssh-server avahi-daemon`.
8. Set hostname: `sudo hostnamectl set-hostname ai-frame`.
9. mDNS: avahi should advertise ai-frame.local now.
10. Host PC: `ssh sipeed@ai-frame.local`. Should work. If not, `ssh sipeed@<static IP>`.
11. SSH key: from host PC `ssh-copy-id sipeed@ai-frame.local`.
12. Now you can disconnect CH340 serial. All further work via SSH.
**Expected result:** SSH key-based login works from host PC at ai-frame.local. Serial no longer required.
**Acceptance criteria / DoD:** 7. **SSH key-based login successful without password.** Wi-Fi auto-connects on reboot. Default password changed.
**Evidence to save:** Screenshots: `ssh` command with fingerprint + prompt, `ip addr`, `hostname`.
**Safety considerations:** N/A.
**Source references:** JIRA.md §Software Planning stage 2. Requirements R025.
**Labels:** software nano blocked blocked:exp-af-026
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-028: Minimal Python environment frozen (python3, pip, venv; requirements.txt for Pillow only so far; venv activation path recorded for systemd later)

**Task ID:** AF-028
**Epic:** AF-002
**Summary:** Create Python 3 venv with pip. Freeze base packages. Record venv path for future systemd.
**Description:** ADR-009: Python + Pillow. Install python3-venv via apt. Create `/home/sipeed/ai-frame-venv`. Activate. `pip install --upgrade pip`. Verify Pillow installable (we do it in the next task; this task sets up venv). Write requirements-base.txt with just pip-tools for now.
**Why this task exists:** Stage 3 of 12 SW plan.
**Prerequisites:** SSH works (AF-027).
**Blocked by:** AF-027
**Exact execution steps:**
1. `sudo apt update && sudo apt install -y python3-venv python3-dev build-essential libjpeg-dev zlib1g-dev libfreetype6-dev` (Pillow build dependencies).
2. `python3 -m venv /home/sipeed/ai-frame-venv`.
3. `source /home/sipeed/ai-frame-venv/bin/activate && pip install --upgrade pip setuptools wheel`.
4. `pip freeze > /home/sipeed/ai-frame/requirements-base.txt` (create repo dir first).
5. Deactivate. Record venv activation path: `/home/sipeed/ai-frame-venv/bin/activate`. This path will be used in systemd unit files later.
**Acceptance criteria / DoD:**
- Venv directory exists.
- Python version inside venv: `python --version` → 3.11+ (Bookworm default).
- `pip list` inside venv → non-empty.
- requirements-base.txt committed in the repo.
**Evidence to save:** Output of `pip list`, venv path documented in AF-028 notes.
**Safety considerations:** N/A.
**Source references:** JIRA.md SW 3. ADR-009.
**Labels:** software nano blocked blocked:exp-af-027
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-029: Pillow install + basic smoke test: generate 128×64 solid red PNG → file exists, correct dims (Pillow 10+)

**Task ID:** AF-029
**Epic:** AF-002
**Summary:** Install Pillow. Generate a 128×64 pure-red test image. Verify correct.
**Description:** Inside venv, `pip install Pillow`. Then write a 5-line script:

```python
from PIL import Image
img = Image.new("RGB", (128, 64), (255, 0, 0))
img.save("test-128x64-red.png")
print("size:", Image.open("test-128x64-red.png").size)
```

Run it. Assert size = (128, 64).
**Why this task exists:** Stage 4 first half: Pillow rendering library validated.
**Prerequisites:** AF-028.
**Blocked by:** AF-028
**Acceptance criteria / DoD:** Pillow imports OK. PNG created. Image size (128, 64). Pixel (0,0) RGB = (255,0,0) verified via `img.getpixel((0,0))`.
**Evidence to save:** PNG committed or linked in docs/pm/evidence; script output.
**Source references:** JIRA.md SW 4. ADR-009.
**Labels:** software nano blocked blocked:exp-af-028
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-030: Pillow text renderer: render arbitrary string "Hello AI Frame" onto 128×64 RGB canvas → saved PNG, text visibly readable (no font yet, default PIL bitmap font acceptable for stage 4)

**Task ID:** AF-030
**Epic:** AF-002
**Summary:** Text render smoke test. Arbitrary string → 128×64 PNG.
**Description:** Use PIL ImageDraw.Draw.text with default font. Later we select production multi-line-capable TTF font in MA stage 4. Draw "Hello AI Frame" at top-left. Save PNG.
**Why this task exists:** R001 "display any typed text content" lowest-level precursor. JIRA.md M1 "arbitrary user-supplied string" requirement at software level.
**Prerequisites:** AF-029.
**Blocked by:** AF-029
**Acceptance criteria / DoD:** PNG produced; text not clipped; visible text "Hello AI Frame" appears in image if opened.
**Evidence to save:** PNG.
**Source references:** JIRA M1 requirement 2, R001.
**Labels:** software nano blocked blocked:exp-af-029
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-031: Standard test-pattern renderer (solid fills ×3 colors, 8×8 checkerboard, diagonal lines, horizontal gradient vertical-gradient, (x,y) coordinate labels every 32 px)

**Task ID:** AF-031
**Epic:** AF-002
**Summary:** Implement 6 standard test patterns as Python functions. Each returns an Image.
**Description:** Functions:
- solid(width,height,r,g,b)
- checkerboard(w,h,size=8)
- lines(w,h, orientation="diagonal")
- gradient(w,h, direction="horizontal")
- coordinate_labels(w,h, step=32)
- test_suite(w=128,h=64) → save all 6 PNGs
**Why this task exists:** R056 every panel-up test uses standard pattern suite; Nano SW must be able to produce them. JIRA M1 standard test patterns.
**Prerequisites:** AF-030.
**Blocked by:** AF-030
**Acceptance criteria / DoD:** 6 PNGs produced for 128×64. Visually correct (checkerboard alternates, gradient has smooth slope, coords readable at step).
**Evidence to save:** 6 PNGs + code committed to repo software/ path.
**Source references:** EXPERIMENTS.md Standard Test Pattern Suite. R056.
**Labels:** software nano blocked blocked:exp-af-030
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-032: Canonical framebuffer abstraction class/methods: Framebuffer.new(w,h), set_pixel(x,y,r,g,b), get_region(x,y,w,h), export_raw_bytes()

**Task ID:** AF-032
**Epic:** AF-002
**Summary:** Define canonical framebuffer API used everywhere.
**Description:** Class Framebuffer:
```python
class Framebuffer:
    @staticmethod
    def new(w, h): ...
    def set_pixel(self, x, y, rgb): ...  # rgb = (r,g,b)
    def get_region(self, x, y, w, h): -> Framebuffer  # crop
    def export_raw_bytes(self): -> bytes  # RGBRGBRGB row-major order, 3bpp
    @property
    def size(self): -> (w,h)
```
Backed by PIL Image internally. `export_raw_bytes()` returns raw RGB24 row-major bytes ready for controller transport.
**Why this task exists:** R004 programmatic 24-bit framebuffer; JIRA SW 6 canonical abstraction.
**Prerequisites:** AF-031.
**Blocked by:** AF-031
**Required hardware:** None — software-only task. LicheeRV Nano-W (AF-025 flashed) or local PC for pytest-only testing.
**Required tools:** None beyond Nano board or a PC with Python 3.10+.
**Required software:** Python ≥ 3.10. Pillow ≥ 10.0 (installed AF-027 or on local dev PC). pytest optional if using it for unit tests, else bare assert-based smoke script.
**Exact execution steps:**
1. Create file `software/nano/app/framebuffer.py` (or appropriate V1 repo path) and module `__init__.py` export.
2. Implement class `Framebuffer` with the exact 5-method API shown in Description (new / set_pixel / get_region / export_raw_bytes / size property).
3. Back storage: use `PIL.Image.new("RGB", (w, h))` as internal buffer.
4. `export_raw_bytes()`: return `Image.tobytes("raw", "RGB")` to guarantee RGB24 row-major order 3 bpp (no stride padding, no alpha).
5. Write smoke tests: create 128×64 FB → set pixel (5,5, (10,20,30)) → crop (0,0,128,64) → verify length = 128*64*3 = 24576 bytes.
6. Run smoke tests on Nano (if available) or on dev PC via Python venv.
**Expected result:** All 4 smoke assertions pass; `export_raw_bytes()` length equals w*h*3 for any size; pixel value round-trips correctly through set_pixel + get_region.
**Acceptance criteria / DoD:**
- Constructor works for 128×64.
- set_pixel(5,5,(1,2,3)) → get_pixel (or region crop read → find pixel at 5,5) returns (1,2,3).
- get_region(0,0,128,64).size == (128,64).
- export_raw_bytes returns w*h*3 bytes length for any (w,h).
**Evidence to save:** Python module committed. Unit tests: pytest or small python asserts.
**Safety considerations:** N/A — software-only task; no hardware touched.
**Known uncertainties:** Pillow RGB24 byte order on Nano (should match standard; if not, swap via `Image.tobytes(..., 'BGR')` or reorder).
**Failure response:** If Pillow version incompatibility → pin Pillow ≥ 10 in requirements.txt per AF-027 method. If export_raw_bytes format mismatch vs controller expectations → add a byte-order flag or post-processing transpose step.
**Source references:** JIRA SW 6. R004, R023, R024.
**Labels:** software nano blocked blocked:exp-af-031
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-033: Transport interface spec: abstract class DisplayTransport.send_frame(buffer: bytes, width: int, height: int)

**Task ID:** AF-033
**Epic:** AF-002
**Summary:** Define the transport interface, no implementation yet.
**Description:**
```python
from abc import ABC, abstractmethod
class DisplayTransport(ABC):
    @abstractmethod
    def send_frame(self, buffer: bytes, width: int, height: int) -> None:
        """Send a frame. buffer = RGB24 row-major, width*height*3 bytes.
        Raises TransportError on failure. Reconnects internally if needed;
        after 3 retries raises. Implementation-specific timeouts.
        """
class TransportError(Exception): pass
```
Implementation classes are NOT in this task (they come after MG/ADR-017). A stub implementation for testing comes next task.
**Why this task exists:** JIRA SW 7 interface formalization. R023 pluggable transport.
**Prerequisites:** AF-032.
**Blocked by:** AF-032
**Acceptance criteria / DoD:**
- You cannot instantiate DisplayTransport abstract directly (raises TypeError).
- TransportError exists.
- Subclass with send_frame defined: instantiable.
**Evidence to save:** Code file `transport.py` with ABC.
**Source references:** JIRA SW 7, ADR-017 (PENDING). R023.
**Labels:** software nano blocked blocked:exp-af-032
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-034: Candidate transport implementation skeleton (stubbed raises NotImplementedError for WF4-protocol variant + UART variant)

**Task ID:** AF-034
**Epic:** AF-002
**Summary:** Create 2 stub transport subclasses. No actual protocol yet. Framework for implementing either after ADR-017.
**Description:** `StubTransport` prints the buffer length and dimensions. Aids end-to-end smoke next task. Also create empty subclasses:
- `Wf4HuiduProtocolTransport(DisplayTransport)` — body `raise NotImplementedError`
- `Esp32UartTransport(DisplayTransport)` — body `raise NotImplementedError`
**Why this task exists:** Placeholder so architecture switch after MG is just filling in one implementation's body. ADR-017 will pick ONE; the other is left NotImplemented for V1 (never filled).
**Prerequisites:** AF-033.
**Blocked by:** AF-033
**Acceptance criteria / DoD:** 2 NotImplementedError subclasses defined + 1 StubTransport works.
**Evidence to save:** Committed transport skeleton code.
**Source references:** JIRA SW 7 + 8.
**Labels:** software nano blocked blocked:exp-af-033
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-035: End-to-end smoke "arbitrary input text → Pillow framebuffer → transport stub → success printed"

**Task ID:** AF-035
**Epic:** AF-002
**Summary:** End-to-end smoke on the software-only path.
**Description:**
```python
text = arbitrary_cmdline_input()  # user enters e.g. "arbitrary string" via sys.argv[1]
fb = Framebuffer.new(128,64)
Pillow_draw_text(fb, text, x=0, y=0)
transport = StubTransport()
transport.send_frame(fb.export_raw_bytes(), 128, 64)
print("OK")
```
Run it with 3 different strings. Success.
**Why this task exists:** JIRA M1 requirement "arbitrary text → Nano → display" software-side proof without a physical display yet.
**Prerequisites:** AF-034.
**Blocked by:** AF-034
**Acceptance criteria / DoD:** 3 different strings passed via argv → script prints OK each time. No crashes. No assumptions about string length (it wraps or truncates gracefully if too long for 128×64 — truncate is OK in smoke).
**Evidence to save:** Terminal output of 3 runs. Script committed to repo.
**Source references:** R001, JIRA M1 requirements.
**Labels:** software nano critical-path yes validation blocked blocked:exp-af-034
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-036: Framebuffer scaling test: generate 256×64 then 256×128 then 256×192 test patterns without renderer changes

**Task ID:** AF-036
**Epic:** AF-002
**Summary:** Verify rendering code is fully resolution-agnostic. R024.
**Description:** For each size in [128×64, 256×64, 256×128, 256×192]:
1. Call Standard Test Pattern suite on the size.
2. Call Framebuffer.new(w, h). export_raw_bytes len == w*h*3.
3. get_region crops for 256×192 → row crops (0,0,256,64), (0,64,256,64), (0,128,256,64) each == correct (256,64).
No changes to pattern-rendering or framebuffer code allowed during this test (i.e., bugs found during this test are fixed by patching the code and re-running, but the code must work across all 4 sizes without renderer code architecture changes like "if size == 256: special case foo").
**Why this task exists:** JIRA SW 10 (renderer NEVER changes between sizes). R024.
**Prerequisites:** AF-035.
**Blocked by:** AF-035
**Acceptance criteria / DoD:**
- All 4 sizes pattern generation succeeds, PNGs written for each size.
- Each size row-major byte count correct.
- Row crop operations on 256×192 produce 3 sub-crops of 256×64 each.
- No size-based special cases in pattern-render or framebuffer source code after any bug fix patch (grep for `if.*256|if.*128|if.*192` → count 0 except for crop coordinates).
**Evidence to save:** 4 sizes × 6 patterns (some subset representative to save storage). grep output showing no size conditionals.
**Source references:** JIRA SW 10, R024.
**Labels:** software nano blocked blocked:exp-af-035 validation
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-037: (M1 Nano SW subtrack summary) — commit all Nano software so far to repo branch

**Task ID:** AF-037
**Epic:** AF-002
**Summary:** Commit Nano SW to repository.
**Description:** Place all Nano code into `/home/sipeed/ai-frame/` (on Nano) AND push a copy of the source to the Git repository under `software/app/nano/` or similar (exact location determined by project conventions).
**Prerequisites:** AF-036.
**Blocked by:** AF-036
**Acceptance criteria / DoD:** `git log --oneline` shows a commit.
**Evidence to save:** commit hash.
**Labels:** software nano docs
**Status flags:**
critical-path: yes
Conditional: no

---

## End of M1 Subtrack (a). Start Subtrack (b): WF4 Controller

---

### AF-038: VERIFY WF4 PCB revision, markings, photos (EXP-001 / WF4 subtask): identify front/rear, port count X1/X2/X3, power connector type

**Task ID:** AF-038
**Epic:** AF-002
**Summary:** EXP-001 WF4-specific identification.
**Description:** WF4 only: photos front/rear; count X1/X2/X3 ports; ID power input connector style (screw terminal? 5.5/2.1 DC barrel? Molex?); silk revision, serial if any. Compare to DECISIONS.md ADR-004 WF4 description (3 HUB75 ports, multi-port sender).
**Why this task exists:** WF4 serviceability U-009 partial answer, U-007 hardware form, U-027 power draw connector.
**Prerequisites:** COMPLETED: AF-024 (M0 exit, inventory done) OR at minimum AF-011 inventory identifying WF4 exists.
**Blocked by:** AF-024 (or AF-011)
**Acceptance criteria / DoD:**
- Front + back photos captured.
- Port count = 3 (X1, X2, X3) or different → record different.
- Power connector type clearly stated + photo.
**Evidence to save:** 2 photos + notes.
**Safety considerations:** No power.
**Source references:** EXP-001. ADR-004 WF4.
**Labels:** hardware controller-wf4 docs blocked blocked:exp-af-024
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-039: VERIFY WF4 5 V power input connector mating — identify correct mating connector, crimp practice on connector if new type

**Task ID:** AF-039
**Epic:** AF-002
**Summary:** Identify WF4 power connector. Crimp one sample with 18 AWG red/black.
**Description:** From AF-038 photos, determine mating connector (if screw terminal, no mating needed: bare ferrule OK). If DC barrel: need 5.5 mm OD / 2.1 mm ID or 2.5 mm ID; crimp barrel onto 18 AWG. Practice sample (similar to AF-013).
**Prerequisites:** AF-038.
**Blocked by:** AF-038
**Acceptance criteria / DoD:** Mating connector type identified; sample crimp pass if crimping needed.
**Evidence to save:** Connector photos side-by-side with WF4 jack.
**Labels:** hardware controller-wf4 power blocked blocked:exp-af-038
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-040: WF4 power wiring to PSU (18 AWG, correct connector, ferrules at PSU end, branch assigned. Branch #X of 8)

**Task ID:** AF-040
**Epic:** AF-002
**Summary:** Physically wire WF4 power from PSU 5 V fused panel harness (one branch, or branch 7 reserved for Nano + controllers combined).
**Description:** Follow safety rules: PSU OFF, wall out. 18 AWG. Ferrule at PSU end. WF4 end: mating connector installed (screw clamp → ferrule, or barrel). Connect one fused 5 V branch (NOT Nano's branch). Label wires "WF4 power" on both ends.
**Prerequisites:** AF-039, COMPLETED AF-024 (PSU has been safety-verified).
**Blocked by:** AF-039, AF-024
**Safety considerations:** JIRA Safety (1) mains, (4) no Dupont for 5 V high-current, (2) no HUB75 hot plug. WF4 power-only; no HUB75 plugged in yet.
**Acceptance criteria / DoD:**
- Continuity: PSU 5V+ → WF4 power V+ = beep.
- Wiggle pass.
- Cross WF4 V+ → V- = no beep.
**Evidence to save:** Continuity + installation photos.
**Source references:** R017, R018.
**Labels:** hardware controller-wf4 power safety-review polarity-verify blocked blocked:exp-af-039
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-041: WF4 X1 → panel IN HUB75 (signal cable, verify orientation per polarity AF-022, plug in)

**Task ID:** AF-041
**Epic:** AF-002
**Summary:** Plug HUB75 cable WF4 X1 → panel #1 IN. Orient with notch. PSU OFF.
**Description:** PSU OFF, wall plug out. HUB75 0.5 m cable: WF4 X1 port → panel #1 "IN" port (confirmed in AF-022). Keyed notch aligned. Label cable "WF4-X1 → panel-1-IN".
**Prerequisites:** AF-040 WF4 powered physically (but off), AF-022 HUB75 orientation panel 1.
**Blocked by:** AF-040, AF-022
**Acceptance criteria / DoD:** Cable plugged, notch engaged, both ends labeled.
**Evidence to save:** Photo of ends.
**Safety considerations:** JIRA Safety (2): HUB75 connected WHEN EVERYTHING OFF = OK. Later never hot-plug.
**Labels:** hardware controller-wf4 polarity-verify blocked blocked:exp-af-040
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-042: WF4 stock firmware → 1 panel EXP-004: configure 128×64 / 1/32 scan / color-order. Run Standard Test Pattern Suite. Check Standard Defect Checklist

**Task ID:** AF-042
**Epic:** AF-002
**Summary:** EXP-004. WF4 stock firmware drives panel #1 correctly.
**Description:** EXP-004 verbatim: WF4 + 1 panel, config parameters, pattern suite, defect checklist, stability 1 hr if possible; at least 10 min.
**Prerequisites:** AF-041.
**Blocked by:** AF-041
**Exact execution steps:**
1. Power up ORDER (JIRA Safety 2): PSU ON first → WF4 ON → then panel powered. Wait.
2. Configure WF4: access WF4 configuration (Huidu app or on-board pushbuttons if available). Set panel count = 1, width = 128, height = 64, scan = 1/32, chip = default or ICND2065 if known; color order = RGB (try until colors correct).
3. Save config.
4. Run Standard Test Pattern Suite (from AF-031 software patterns): use WF4's built-in test patterns or upload equivalent via vendor software. 6 patterns:
   4.1 Solid RED full panel. Every pixel red.
   4.2 Solid GREEN full panel.
   4.3 Solid BLUE full panel.
   4.4 8×8 checkerboard.
   4.5 Diagonal lines.
   4.6 Horizontal gradient black→white or R→G→B.
5. For each pattern, take 1 photo.
6. Standard Defect Checklist 10 items:
   6.1 Stuck-on pixel (any pixel always on when it shouldn't be).
   6.2 Stuck-off pixel.
   6.3 Color-cast row/column (one row has tint vs others).
   6.4 Scan line offset (every other row shifted by 1+ pixels).
   6.5 Ghosting/glitch on movement (when moving from pattern A to B).
   6.6 Flickering visible at seating distance > 1 m (flicker obvious, not sub-1000 Hz imperceptible).
   6.7 Missing pixel row: entire row gone; or extra row.
   6.8 Missing pixel column: entire column gone or extra.
   6.9 Power-up artifacts: bright flash or all-white for > 1 s during power on.
   6.10 Discoloration at edge/corner.
7. Run at least 10 min. If stable, extend to 1 hr to gather stability data now (saves time later; optional).
8. Power down ORDER: panels OFF path first? Actually the data says: panels = powered from 5 V rail so can't data-separate. But JIRA says: "disconnect power to panels first → controller → PSU last". So: switch OFF C14. Done.
**Acceptance criteria / DoD:**
- Panel displays correct 128×64 content. Colors order correct in solid tests (red = red, green = green, blue = blue).
- Pattern 4.1–4.6 all render, each captured in photo.
- Standard Defect Checklist: 10 items ALL PASS or at most 1 minor stuck pixel ≤ 2 and considered acceptable but noted.
**Evidence to save:** 6 pattern photos + 10 checklist item results saved in EXP-004 format.
**Safety considerations:**
Power ORDER obeyed. HUB75 NOT unplugged while powered.
**Source references:**
- EXPERIMENTS.md EXP-004 procedure.
- Standard Test Pattern Suite + Standard Defect Checklist referenced in EXPERIMENTS.md.
- Requirements R056, R057, R011 (panel spec), R004.
**Labels:** hardware controller-wf4 power validation blocked blocked:exp-af-041
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-043: Huidu software Phase A EXP-007A: send known content via vendor software, map UI: config screen, static upload screen, live update screen

**Task ID:** AF-043
**Epic:** AF-002
**Summary:** EXP-007A. Explore Huidu vendor software. Understand 3 UI surfaces: CONFIG, STATIC FILE UPLOAD, LIVE UPDATE.
**Description:** EXP-007A verbatim: Install Huidu vendor software (HD2018 or latest version) on a Windows PC or under Wine. Connect WF4 to PC via USB or Wi-Fi (WF4 default Wi-Fi? Or USB? — try the method WF4 manual says). Send a known test pattern PNG (from AF-031's patterns) as static content. Confirm it appears on panel correctly and matches the PNG byte-for-byte visually. Record screenshots of the 3 UI surfaces (config, upload, live if any). Time each operation.
**Prerequisites:** AF-042.
**Blocked by:** AF-042
**Acceptance criteria / DoD:**
- Screenshots of 3 UI surfaces.
- Static PNG upload to panel works, matches.
- Timing: config time X sec, static upload Y sec.
**Evidence to save:** Screenshots + log of steps.
**Source references:** EXP-007A. U-010 (documentation availability partially).
**Labels:** software controller-wf4 validation blocked blocked:exp-af-042
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-044: Protocol inspection Phase B EXP-007B: inspect documented/reverse-engineered Huidu libraries + static image from PC then from Nano; measure transfer time, latency, max update freq

**Task ID:** AF-044
**Epic:** AF-002
**Summary:** EXP-007B. Explore open-source reverse-engineered Huidu libs. Measure transfer metrics from PC then Nano.
**Description:** EXP-007B: find libraries (GitHub e.g., "huidu led" or reverse-engineer by packet capturing EXP-007A actions). First: from PC, send a known static PNG via the library (not vendor software) → confirm it appears. Measure time = call start to display complete. Then: do the same from Nano over USB (WF4 USB slave → Nano USB host). Then: do it repeatedly, measure max update frequency per second. Latency = from call to first visible change on panel (phone camera 240 FPS if possible, or stopwatch).
**Prerequisites:** AF-043.
**Blocked by:** AF-043
**Acceptance criteria / DoD:**
- Static image from PC via open library works.
- Static image from Nano via USB works.
- Transfer time per 128×64 frame measured in ms.
- Latency ms measured.
- Max update frequency = frames per second sustainable for 30 seconds.
**Evidence to save:** Library link, code snippet, measurements table (transfer time, latency, max freq).
**Source references:** EXP-007B, ADR-017 transport decision inputs, U-010.
**Labels:** software controller-wf4 spike blocked blocked:exp-af-043
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-045: Python integration Phase C EXP-007C: Pillow render → transport → automate repeated updates → run ≥ 1 hour

**Task ID:** AF-045
**Epic:** AF-002
**Summary:** EXP-007C. Nano → WF4 programmatic updates ≥ 1 hour stable.
**Description:** EXP-007C verbatim: Python code that (a) Pillow renders arbitrary text, (b) exports raw bytes or PNG, (c) uses the open library to send frame via WF4 transport. Repeat: update the frame every N ms (max sustainable freq). Run for ≥ 1 hour. At end: no crashes, panel content correct, WF4 controller not hot to touch, panel no new defects. Fill in EXP-007 success criteria: Preferred = programmatic Nano update without per-update vendor software clicks, one-script, <1 min latency, 1 hr continuous stable.
**Prerequisites:** AF-044, AF-032 (Pillow/FB abstraction).
**Blocked by:** AF-044, AF-032
**Acceptance criteria / DoD:**
- Preferred success: script on Nano, no manual clicks, runs 1 hr stable.
- Strong preference: latency < 1 min per full update (the faster the better).
- Acceptable fallback: vendor software for initial config only + programmatic subsequent updates.
- Failure: if every update still needs the vendor software open → FAIL.
**Evidence to save:** EXP-007 results (Preferred/Strong pref/Acceptable/Failure outcome recorded). 1-hour run screenshot with uptime; graph of update times over the hour if possible.
**Source references:**
- EXP-007C §Success criteria Preferred / Strong pref / Acceptable / Failure
- R001, R003, ADR-016 criterion 1
**Labels:** software controller-wf4 power thermal-review validation blocked blocked:exp-af-044 blocked:exp-af-032
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-046: Pillow text renderer → WF4 end-to-end test (arbitrary user-supplied string, NOT hard-coded)

**Task ID:** AF-046
**Epic:** AF-002
**Summary:** WF4 candidate chain passes "user types arbitrary string → Nano → panel". This is the WF4 subtrack terminal pass for M1 gate.
**Description:** Run the E2E flow with a string that the executor TYPES IN at runtime, not hard-coded. E.g., enter "The quick brown fox jumps over the lazy dog 1234567890!@#$%". Confirm it appears on panel #1 correctly. Do 10-minute stable hold. Take video or series of photos.
**Prerequisites:** AF-045, AF-030 (text renderer), AF-035 (smoke).
**Blocked by:** AF-045, AF-030
**Acceptance criteria / DoD:**
- Arbitrary user-entered string (NOT "Hello AI Frame" from AF-030) appears correctly on panel #1.
- No manual vendor-software clicks during this update (only programmatic).
- 10 min stable hold: no blank, no freeze, no garble.
- WF4 controller temp < 45°C (hand test or IR).
**Evidence to save:** Photo of panel with displayed string matching the user-entered input string photo. Start and end timestamp. 10-min stable hold.
**Source references:** JIRA M1 requirements 1-7.
**Labels:** validation controller-wf4 critical-path candidate-yes blocked blocked:exp-af-045
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-047: WF4 M1 subtrack evidence commit + subtrack pass log

**Task ID:** AF-047
**Epic:** AF-002
**Summary:** WF4 subtrack aggregator: commit all evidence, mark subtrack = WF4 candidate pass (if AF-046 passes).
**Description:** If AF-046 DoD all pass, WF4 subtrack = candidate-pass. Commit the EXP-004/007 results as a single commit to docs evidence folder.
**Prerequisites:** AF-046.
**Blocked by:** AF-046
**Acceptance criteria / DoD:** 1 commit hash, log says "WF4 subtrack: candidate pass / fail".
**Labels:** docs controller-wf4 validation
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

## End of M1 Subtrack (b): WF4 Controller. Start Subtrack (c): ESP32 Controller

---

### AF-048: VERIFY ESP32-S3 board rev/dims/module markings/header spacing/USB/BOOT/RESET/antenna (EXP-010). Fill EXP-010 measurement table.

**Task ID:** AF-048
**Epic:** AF-002 (M1: One Nano-Driven Arbitrary-Text Panel)
**Summary:** Execute EXP-010 ESP32-S3 board identification. Fill every row of EXP-010 Measurements table.
**Description:** EXP-010 verbatim. ESP32 only: photograph both sides; record PCB revision and module silk; measure length/width mm; measure header pin pitch and pins per side; locate USB-C, BOOT button, RESET button, antenna type (PCB or U.FL); compare to known DevKitC-1 layout; identify N16R8 marking photo. This is the baseline physical identification that feeds Cond-X PCB footprint later.
**Why this task exists:** Resolves U-014 (headers soldered?), U-015 (N16R8 markings?), U-028 (ESP32 physical geometry), U-029 (GPIO availability preliminary answer). EXP-010 procedure is the defining source. All downstream HCT build tasks depend on these measurements matching reality.
**Prerequisites:** COMPLETED: AF-024 (M0 exit, inventory done) OR at minimum AF-011 inventory identifying ESP32 exists on bench.
**Blocked by:** AF-024 (or AF-011)
**Required hardware:** ESP32-S3 N16R8 dev board as delivered. No wiring.
**Required tools:** Ruler or digital calipers (mm precision), smartphone/camera for close-up photos of module markings and PCB rev silk.
**Required software:** EXPERIMENTS.md EXP-010 procedure + Measurements table columns open for fill-in.
**Exact execution steps:**
1. Photograph ESP32 FRONT close-up: board silk labels, USB-C port location, BOOT button, RESET button, antenna region.
2. Photograph ESP32 BACK close-up: any silk, module underside, solder joints.
3. Module marking macro: photograph the shielded metal can marking that should read "ESP32-S3" and "N16R8" or variant close enough to read text.
4. Measure board length mm and width mm with caliper or ruler. Record in EXP-010 table.
5. Header spacing: confirm 1×40 pins each side; pitch = 2.54 mm (measure pin1 to pin2 center distance). Count pins. Record.
6. USB connector: confirm USB-C (not micro-USB). Record.
7. BOOT button: present/absent? Labeled "BOOT"? Press test (click). Record.
8. RESET button: present/absent? Labeled "EN" or "RST" or "RESET"? Press test. Record.
9. Antenna: PCB trace antenna on right edge? Or U.FL connector for external? Record with photo.
10. Fill EXP-010 Measurements table every empty row.
11. Compare pin labels with known ESP32-S3 DevKitC-1 layouts online (if internet available) OR proceed to AF-051 (software GPIO list) without comparison.
**Expected result:** EXP-010 Measurements table every row filled. 5+ photos (front, back, module-marking macro close-up, USB/buttons close-up, antenna close-up). Dimensions in mm. Pin pitch confirmed 2.54 mm if standard.
**Acceptance criteria / DoD:**
- EXP-010 table all 7 rows filled: Board length/width, Header spacing/pitch/pins per side, USB location, BOOT/RESET, Antenna, Module marking, Flash/PSRAM (flash/PSRAM = software step AF-050, so mark as "pending AF-050").
- Photos: front + back + module marking legible (can read the marking text or state it's illegible → escalation).
- N16R8 marking physically observed and photographed = pass.
**Evidence to save:**
- `docs/photos/esp32-identify/esp32-front.jpg`
- `docs/photos/esp32-identify/esp32-back.jpg`
- `docs/photos/esp32-identify/esp32-module-marking.jpg`
- `docs/photos/esp32-identify/esp32-usb-buttons-antenna.jpg`
- `docs/pm/evidence/AF-048-exp-010-notes.md` = filled EXP-010 measurements table + notes
**Safety considerations:**
N/A — identification only, no power, no wiring.
NOTE: Do not discard anti-static bag; re-use for ESP32 storage between work sessions.
**Known uncertainties:**
- Resolves U-014 (headers soldered? — visual answer here).
- Resolves U-015 (N16R8 marking photo captured).
- Resolves U-028 (board dimensions, header pitch).
- U-029 (GPIO availability): partial answer from board labels; fully resolved in AF-051 (software list).
**Failure response:**
If marking illegible → proceed to AF-050 software flash/PSRAM check; N16R8 will be confirmed there. If headers missing → proceed to AF-049 conditional solder task. If board physically damaged (cracked, pins bent) → flag + RMA.
**Source references:**
- EXPERIMENTS.md EXP-010 §Procedure 1–4, §Measurements table (7 empty rows) (EXPERIMENTS.md#L291-L319)
- PIN_LEVEL_APPENDIX.md §7 (ESP32 parts inventory, 1×40 header strip note)
- Uncertainties U-014, U-015, U-028, U-029
**Labels:** hardware controller-esp32 docs validation blocked blocked:exp-af-024
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-049: (Conditional) Solder 1×40 male headers both sides if unsoldered. Visual + continuity every pin.

**Task ID:** AF-049
**Epic:** AF-002 (M1)
**Summary:** If AF-048 found headers UNSOLDERED: solder 2× 1×40 male header strips both sides of ESP32. Visual + continuity every pin.
**Description:** Skip condition: AF-048 determined headers already factory-soldered → skip this task entirely. If unsoldered: solder 1×40 male pin headers onto BOTH sides of ESP32-S3 dev board (80 pins total). Use 60/40 solder + flux. After soldering: (1) visual check every joint — no cold joints, no bridges; (2) multimeter continuity every individual pin (header pin to ESP32 module pad or adjacent via/trace = beep). Novice solder task.
**Why this task exists:** U-014 resolved as "no headers" needs this step. All downstream HCT wiring (AF-059 onward) requires male header pins on ESP32 for female Dupont jumper or direct solder connection. Without headers = no perfboard wiring possible.
**Prerequisites:** COMPLETED: AF-048. AND (CONDITION: headers found unsoldered in AF-048).
**Blocked by:** AF-048
**Required hardware:** ESP32-S3 dev board (unsoldered), 2× 1×40 male pin header strips 2.54 mm pitch (BOM H-17 or similar), solder (rosin core 60/40), flux (liquid or paste).
**Required tools:** Soldering iron (350–380°C), brass sponge tip cleaner, fume extractor or well-ventilated area, multimeter continuity mode, good lighting, magnifying glass if available.
**Required software:** None.
**Exact execution steps:**
1. Break header strips to exactly 40 pins each. Trim excess if purchased longer.
2. Insert one 1×40 strip into ESP32 side row of through-holes. Press from top so pins extend uniformly below the board. Use tape or a helping hand to hold strip flush to PCB top so pins sit perpendicular.
3. Solder ONE pin at each END of the strip (pin 1 and pin 40) to tack it in place. Check alignment → if strip crooked, re-melt one tack pin + reposition BEFORE soldering all 38 middle pins.
4. Solder remaining 38 pins of side #1. 1 pin at a time: heat pad + pin for 1 sec; add solder to opposite side of iron tip to form concave fillet; remove iron; wait to cool. Do NOT apply iron for >3 s per pin (can lift pad).
5. Flip board. Repeat steps 2–4 for side #2 (second 1×40 strip).
6. Visual inspection all 80 pins:
   6a. Every joint has concave fillet, not ball-shape or grainy cold joint.
   6b. NO bridges (solder touching two adjacent pins) — use solder wick or iron tip drag to remove.
   6c. NO pins lifted from PCB (no gap between header plastic and PCB).
7. Multimeter continuity every single pin. Method: probe on pin end + the nearest visible via or pad connected to that pin on PCB (or use PIN_LEVEL_APPENDIX §3/§4 to find known ground pin = beep to GND rail). Count: 80 pins → 80 continuity checks. Each pin: beep = good. NO beep = bad joint → re-solder.
**Expected result:** 2× 1×40 strips soldered both sides; 80/80 visual passes; 80/80 continuity beeps.
**Acceptance criteria / DoD:**
- Skip condition: if AF-048 headers Y → skip. Otherwise:
- 2 header strips flush to PCB, perpendicular, no skew.
- 80/80 visual joints pass (no bridges, no cold, no lifted).
- 80/80 continuity beeps (0 fails).
**Evidence to save:**
- `docs/photos/esp32-headers/side1-soldered.jpg` (all 40 pins in frame)
- `docs/photos/esp32-headers/side2-soldered.jpg`
- `docs/photos/esp32-headers/closeup-no-bridges.jpg` (2 photos, each side at pin 20 area, magnified)
- `docs/pm/evidence/AF-049-header-solder-notes.md` = 80/80 continuity checksum, any redone joints documented
**Safety considerations:**
Soldering safety:
1. Fume extractor or cross-ventilation — do not inhale solder fumes.
2. Iron stand when not in use; hot tip away from flammables.
3. No jewelry on soldering hand.
4. 3 s max per pin.
5. Wash hands after handling solder (lead).
No electrical power applied during this task.
**Known uncertainties:**
- Resolves U-014 (if unsoldered → resolves to "now soldered").
- If a pad is lifted during soldering → that GPIO unavailable → list in AF-051 unavailable-GPIO list + adjust provisional map AF-052.
**Failure response:**
Any bridge → wick off, re-solder. Any lifted pad → mark GPIO as unavailable in AF-051 list; if pin is in provisional map (IO4/5/6/7/8/9/10/11/12/13/15/16/17/18 = U1/U2 mapped pins) → must remap that signal in AF-052 to a free GPIO and note deviation from PIN_LEVEL_APPENDIX §3/§4. More than 2 lifted pads → stop; consider using a different dev board (spare) or using adjacent pins only.
**Source references:**
- JIRA.md novice solder guideline (no explicit rule, but by analogy to crimp rule R049: every solder preceded by understanding of method — task self-documents)
- PIN_LEVEL_APPENDIX.md §7 (1×40 header strip inventory)
- Uncertainty U-014
**Labels:** hardware controller-esp32 blocked blocked:exp-af-048 conditional
**Status flags:**
critical-path: candidate-yes
Conditional: yes
Skip condition: AF-048 determines ESP32 headers are already factory-soldered (both sides).

---

### AF-050: Confirm N16R8 flash/PSRAM in SW via esptool or info sketch.

**Task ID:** AF-050
**Epic:** AF-002 (M1)
**Summary:** Software-confirm N16R8 = 16 MB flash + 8 MB PSRAM (octal SPI). Run esptool flash_id + Arduino info sketch.
**Description:** Connect ESP32 via USB-C to PC. Run `esptool.py flash_id` to read chip + flash size. Then flash a minimal Arduino-ESP32 or ESP-IDF "chip info" sketch that prints: Chip model, Flash size, PSRAM size, PSRAM type (octal or quad). Confirm N16R8 = 16 MB flash + 8 MB octal PSRAM. If not N16R8 (e.g., N8R2) → flag.
**Why this task exists:** EXP-010 step 4 (verify N16R8 in software). R057 (PSRAM/DMA behavior — needs ≥8 MB PSRAM for HUB75 framebuffers). U-015 final confirmation. U-030 PSRAM buffer sufficiency first data point.
**Prerequisites:** COMPLETED: AF-048. COMPLETED: AF-049 IF Conditional=solder-needed; else if headers present → skip AF-049 so AF-048 suffices.
**Blocked by:** AF-048, AF-049 (if applicable)
**Required hardware:** ESP32-S3 board, USB-C data cable (NOT charge-only; data cable confirmed), PC or laptop.
**Required tools:** None physical.
**Required software:** esptool.py (latest pip install), Arduino IDE 2.x with ESP32 board package (2.0.x or 3.x latest stable) OR ESP-IDF v5.x.
**Exact execution steps:**
1. Plug USB-C cable into ESP32 USB-C port. Plug other end into PC USB port.
2. Confirm OS enumerates serial port (Windows: COMx; Linux: /dev/ttyACM0 or /dev/ttyUSB0; macOS: /dev/cu.usbmodem*). If no port → try different cable (charge-only cables don't work). Record port name.
3. `pip install esptool` if not already.
4. Run: `esptool.py --port <PORT> flash_id`. Copy output. Record flash size (e.g., 16 MB = 0x1000000).
5. Run: `esptool.py --port <PORT> chip_id`. Copy output. Confirm chip = ESP32-S3.
6. In Arduino IDE: File → Examples → ESP32 → ChipID → or write minimal sketch with: `Serial.begin(115200);` then: `ESP.getFlashChipSize()`, `ESP.getPsramSize()` (or `psramFound() && ESP.psramSize()` for newer cores). If octal PSRAM API exists: `ESP.getPsramType()` or similar. Compile for board = "ESP32S3 Dev Module". Flash via USB.
7. Open Serial Monitor 115200 baud. Press RESET. Copy full serial output.
8. Record: Flash size = X MB; PSRAM size = Y MB; PSRAM type = octal/quad.
**Expected result:** Flash = 16 MB (16,777,216 bytes). PSRAM = 8 MB (8,388,608 bytes). PSRAM type = octal SPI. All printed in serial output.
**Acceptance criteria / DoD:**
- esptool flash_id output shows ESP32-S3 chip + flash size matches N16 (≥ 16 MB).
- Serial monitor output shows PSRAM detected (psramFound() = true), PSRAM size = 8 MB.
- Octal PSRAM = bonus confirm; if API unavailable → at minimum PSRAM size confirmed 8 MB.
**Evidence to save:**
- Terminal screenshot or text: `esptool flash_id` output
- Serial monitor screenshot: info sketch output with flash/PSRAM sizes
- `docs/pm/evidence/AF-050-n16r8-software-confirm.md` = text copies of both outputs + port name used
**Safety considerations:**
⚠️ CH340 3-pin rule if using an external CH340/USB-to-serial adapter (NOT applicable if using ESP32 native USB-C, which is self-powered from USB 5 V — no external adapter used here). If you later add CH340: "Connect ONLY TX/RX/GND. NEVER connect 5 V or 3.3 V from CH340 adapter to an independently powered target board — risk of back-powering via protection diodes, or ground loop destroying USB port."
USB 5 V is safe low voltage. No HUB75, no panel connections = no high current.
**Known uncertainties:**
- Resolves U-015 (N16R8 configuration final confirmation).
- Resolves U-030 (PSRAM 8 MB present → buffer capacity known as ≥ 8 MB for DMA framebuffers).
- Octal PSRAM = bonus; if quad → still proceed but note for later DMA bandwidth.
**Failure response:**
Flash < 16 MB or PSRAM ≠ 8 MB → flag. If no PSRAM detected (0 bytes) → check board config in Arduino IDE ("ESP32S3 Dev Module" usually has PSRAM enabled by default via QIO/OCTAL; if disabled → enable and reflash). If still no PSRAM → board is not N16R8 → escalate (wrong board shipped).
**Source references:**
- EXPERIMENTS.md EXP-010 §Procedure step 4: "Verify the N16R8 configuration in software" (EXPERIMENTS.md#L313-L315)
- Uncertainties U-015, U-030
**Labels:** firmware controller-esp32 validation blocked blocked:exp-af-048
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-051: Identify unavailable GPIOs (flash, octal PSRAM, USB occupied). List them.

**Task ID:** AF-051
**Epic:** AF-002 (M1)
**Summary:** Enumerate ESP32-S3 GPIOs that CANNOT be used for HCT245 inputs. Flash/PSRAM/USB/straps. Produce explicit list.
**Description:** ESP32-S3 has GPIO 0…48, but many are consumed: (a) SPI flash + octal PSRAM pins (GPIO26–32 and GPIO33–37 for octal — varies by board; check board schematic reference), (b) USB D+/D- usually GPIO19/20, (c) BOOT strap pin GPIO0, (d) other board-specific consumed pins. List: every GPIO number and whether it's: FREE, OCCUPIED (why), STRAP (safe-after-boot or must-not-use), STRAP-OK (usable if boot value correct). Cross-reference with ESP32-S3 DevKitC-1 official pinout (online if available) and software `pinMode()` test sketch that tries each free candidate.
**Why this task exists:** Resolves U-029 (GPIO availability final answer). Prevents accidentally using a flash/PSRAM GPIO that would crash the ESP32 when soldered. Feeds AF-052 provisional GPIO map final confirmation against PIN_LEVEL_APPENDIX §3/§4.
**Prerequisites:** COMPLETED: AF-050 (software toolchain works = can run test sketch).
**Blocked by:** AF-050
**Required hardware:** ESP32-S3 + USB-C cable + PC.
**Required tools:** None.
**Required software:** Arduino IDE or ESP-IDF (same toolchain as AF-050); official ESP32-S3 DevKitC-1 N16R8 schematic PDF if internet accessible.
**Exact execution steps:**
1. From ESP32-S3 data sheet (or Espressif docs online): mark all known reserved/strapped GPIO: GPIO0 (BOOT), GPIO3 (strap), GPIO45/46 (strap), GPIO47/48 (often USB/JTAG alternate).
2. Octal PSRAM note: if PSRAM is octal, GPIOs for PSRAM second bank are typically: GPIO33,34,35,36,37 (PSRAM SPICS1/SPICLK_F/SPID/SPIQ/SPICS0 alternate). Mark these = OCCUPIED: octal PSRAM.
3. Flash SPI: GPIO26–32 usually = internal flash SPI (8-line or 4-line). Mark OCCUPIED: flash SPI.
4. USB: GPIO19/20 = USB_D+/D- on native USB-C. Mark OCCUPIED: USB.
5. Software test sketch: for each candidate GPIO in PIN_LEVEL_APPENDIX §3/§4 (IO4,5,6,7,8,9,10,11,12,13,15,16,17,18): try `pinMode(pin, INPUT_PULLUP)` then `digitalRead(pin)`. If returns 1 (pullup works) and sketch does not crash → free. If sketch crashes or never responds → that GPIO is connected to internal flash/PSRAM → mark OCCUPIED.
6. Produce final list: all GPIO 0–48, each tagged FREE / OCCUPIED / STRAP / STRAP-OK. Highlight the 14 GPIO actually used in §3/§4 (IO4,5,6,7,8,9,10,11,12,13,15,16,17,18) = all should be FREE on DevKitC-1 N16R8.
**Expected result:** A clean list of 49 GPIOs with each status. The 14 GPIO needed for HCT245 inputs all = FREE.
**Acceptance criteria / DoD:**
- 14 HCT GPIO (from §3/§4: 4,5,6,7,8,9,10,11,12,13,15,16,17,18) all pass software test = free.
- Complete GPIO list (0–48) with each tagged status.
- OCCUPIED list enumerates flash, PSRAM, USB pins.
**Evidence to save:**
- `docs/pm/evidence/AF-051-gpio-unavailable-list.md` = full 49-row table: GPIO, Status, Reason, Notes
- Serial monitor output of test sketch run showing all 14 free GPIO reads OK
**Safety considerations:**
N/A — low-voltage USB only, no external wiring.
**Known uncertainties:**
- Resolves U-029 (GPIO availability final).
- If any of 14 mapped GPIO is occupied → triggers AF-052 adjustment step.
**Failure response:**
If any of the 14 target GPIO (4,5,6,7,8,9,10,11,12,13,15,16,17,18) = OCCUPIED → go to AF-052 with adjustment flag. Pick a replacement free GPIO from the list. Update the cross-ref note that PIN_LEVEL_APPENDIX §3/§4 map was provisional and a new GPIO was substituted.
**Source references:**
- PIN_LEVEL_APPENDIX.md §3 U1 GPIO list, §4 U2 GPIO list (PIN_LEVEL_APPENDIX.md#L93-L177)
- EXPERIMENTS.md EXP-010 §Procedure step 3: "Identify GPIOs unavailable because of flash, octal PSRAM, USB" (EXPERIMENTS.md#L311-L313)
- Uncertainty U-029
**Labels:** firmware controller-esp32 docs blocked blocked:exp-af-050
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-052: Confirm provisional GPIO mapping against actual board silkscreen; adjust if needed. Cross-ref PIN_LEVEL_APPENDIX.md §3/§4.

**Task ID:** AF-052
**Epic:** AF-002 (M1)
**Summary:** Confirm the 14 GPIO in §3/§4 match board silk labels. Adjust for any AF-051 conflicts. Produce final validated per-solder-step GPIO→pin map.
**Description:** Walk through every row of PIN_LEVEL_APPENDIX §3 table (8 rows R1/G1/B1/R2/G2/B2/A/B → ESP32 IO5,IO4,IO6,IO15,IO7,IO17,IO8,IO18) and §4 table (6 rows C/D/E/CLK/LAT/OE → IO10,IO9,IO16,IO12,IO11,IO13). For each: find the silk label on the ESP32 dev board next to the physical header pin. Confirm silk label matches the table number. If AF-051 found any occupied GPIO → substitute a free GPIO now and document the change. Write a substitute table if any change.
**Why this task exists:** Prevents soldering wrong GPIO pin to U1/U2 A-side inputs. A single wrong GPIO = display completely broken and hard to debug. Resolves the "provisional" flag in PIN_LEVEL_APPENDIX.md warning (the header says provisional).
**Prerequisites:** COMPLETED: AF-048 (board present, silk readable). COMPLETED: AF-051 (GPIO availability final, conflicts if any known).
**Blocked by:** AF-048, AF-051
**Required hardware:** ESP32 board, PIN_LEVEL_APPENDIX §3/§4 tables open.
**Required tools:** Sharpie marker for label correction on paper map; magnifying glass if silk tiny.
**Required software:** PIN_LEVEL_APPENDIX.md open.
**Exact execution steps:**
1. For each of the 8 §3 rows (U1 A-side inputs):
   1.1 Read ESP32 GPIO # from §3 Function→ESP32 GPIO column.
   1.2 Find that GPIO's silk label on actual ESP32 dev board header. Confirm label text = exact number (e.g., "IO5" or just "5").
   1.3 Confirm AF-051 list said this GPIO = FREE. Record: pass.
2. For each of the 6 §4 rows (U2 A-side inputs): same 1.1→1.3.
3. If ANY §3/§4 GPIO says OCCUPIED in AF-051 OR silk label on board DOES NOT MATCH §3/§4 GPIO number (e.g., board silk says "GPIO21" where table expected "IO5" — very unlikely but check):
   3.1 Pick a replacement free GPIO from AF-051 list.
   3.2 Rewrite the affected table row.
4. Write the FINAL MAP (§3' and §4' adjusted if needed) as a committed notes file. This FINAL MAP is the one used by ALL 12 HCT build stages.
**Expected result:** All 14 GPIO confirmed matching silk + free, OR adjusted map written with reason. No discrepancy between paper and reality.
**Acceptance criteria / DoD:**
- If no conflict: 14/14 GPIO pass silk-label match + AF-051 FREE.
- If conflict: explicit adjusted map in notes + documented "reason changed: silk or AF-051 said X, so replaced GPIO Y with Z".
- Final map referenced by all HCT build stages.
**Evidence to save:**
- `docs/pm/evidence/AF-052-final-gpio-map.md` = final 8 + 6 = 14 row table (Function, ESP32 GPIO [FINAL], U pin, notes); annotated photo of ESP32 silk with 14 pins circled if possible
**Safety considerations:**
No power, no wiring. Paper/silk verification only.
**Known uncertainties:**
- Resolves the "provisional" status of §3/§4 GPIO map → now validated/final.
- Any substitution propagates to AF-059 and AF-061 step outputs.
**Failure response:**
Silk mismatch or occupied GPIO → substitution documented. If more than 2 substitutions needed → re-check for a fundamental board variant (not DevKitC-1).
**Source references:**
- PIN_LEVEL_APPENDIX.md §3 U1 allocation, §4 U2 allocation (PIN_LEVEL_APPENDIX.md#L93-L177)
- PIN_LEVEL_APPENDIX.md warning: "provisional reference layout… confirm the delivered P2 panel's printed labels… before soldering" (PIN_LEVEL_APPENDIX.md#L5-L8)
- Uncertainty U-029
**Labels:** hardware controller-esp32 docs validation blocked blocked:exp-af-048 blocked:exp-af-051
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-053: Gather ESP32 build materials: 2× SN74HCT245N (DIP-20), perfboard, 2×8 HUB75 header, wire (22-26 AWG), 2× 100nF ceramic caps, optional 1000µF electrolytic, solder, flux, heatshrink.

**Task ID:** AF-053
**Epic:** AF-002 (M1)
**Summary:** Gather all HCT build materials from BOM inventory. Confirm package types (DIP-20, not SMD). Count + photograph before starting 12-stage build.
**Description:** Before any HCT soldering (AF-054+): lay out every material item on bench. Confirm each item is correct BOM item, correct package type, correct quantity. Photograph the spread. Any short-ship → use spare BOM items or correct BEFORE build starts. Reference PIN_LEVEL_APPENDIX §7 inventory (1 ESP32 adapter = 2 HCT, 2 caps, 0–1 electrolytic, 1 HUB75 header, 1 perfboard, wire).
**Why this task exists:** Ensures build can complete in one continuous session without "ran out of solder mid-stage 7" interruptions. Reduces the risk of cold joints from rushed resumptions. Aligns with JIRA.md novice: gather before build.
**Prerequisites:** COMPLETED: AF-011 inventory (items present somewhere). COMPLETED: AF-052 final GPIO map in hand.
**Blocked by:** AF-011, AF-052
**Required hardware:** Gather these items onto a clean paper-covered workbench area:
- 2× SN74HCT245N — package: DIP-20 (through-hole PDIP with 20 pins). Verify pin count = 20, notch present at one end. NOT SMD/SOIC.
- 1× perfboard 5×7 cm or larger (grid holes 2.54 mm pitch).
- 1× 2×8 keyed IDC header male (for HUB75 cable mating). Confirm 16 pins total, plastic keyed ridge present.
- Wire: 22–26 AWG solid or stranded (prefer stranded 24 AWG for signal). Red for +5 V, black for GND, various colors for 14 signals if available.
- 2× 100 nF ceramic capacitor (monolithic/disc; leaded, through-hole). Any voltage rating ≥ 10 V.
- 1× 1000 µF / ≥10 V electrolytic capacitor (OPTIONAL bulk). Leads, through-hole. Polarity stripe visible.
- Solder: 60/40 rosin-core thin diameter (0.5–0.8 mm).
- Flux: liquid or paste rosin flux.
- Optional heatshrink 3 mm diameter (~30 cm cuts) for wire insulation if exposed strands.
**Required tools:** Soldering iron (already on hand from AF-049 if applicable), fume extractor, multimeter (continuity mode).
**Required software:** PIN_LEVEL_APPENDIX §7 parts inventory open for cross-check.
**Exact execution steps:**
1. Spread clean white paper over workbench area.
2. Place each item listed above on paper. Label each with Sharpie on paper: "U1/U2 HCT245N DIP-20 ×2".
3. Inspect HCT ICs: notch present (the orientation marker at one end). Count pins each side: 10 each side = 20 total. Read part number silk on chip body: "SN74HCT245N" (or variant like "SN74HCT245AN" = OK; the N suffix = DIP-20). Count: 2 pcs.
4. Count perfboard grid holes per side: should have enough for 2 DIP-20 sockets (or direct solder) + a 2×8 header + 2 caps + rails.
5. Inspect 2×8 HUB75 header: 16 pins, keyed plastic ridge (prevents reverse cable insertion). Count: 1.
6. Count 100 nF caps: 2. Check leads are straight.
7. Electrolytic 1000 µF: 1 if present (0 OK). Check polarity stripe on can (negative = stripe side). Record: present (Y/N).
8. Solder: spool present. Diameter noted.
9. Flux: present Y/N.
10. Wire: enough length for ~30 individual wires each 10 cm = ~3 m total; confirm colors if available.
11. Group photo of all items laid out labeled.
**Expected result:** Every item in §7 inventory physically present (except electrolytic optional), correct package types, correct quantities.
**Acceptance criteria / DoD:**
- HCT245N ×2: DIP-20 package, 20 pins, notch, part number matches.
- Perfboard present.
- 2×8 keyed HUB75 header ×1 present.
- 100 nF caps ×2 present.
- Optional electrolytic noted Y/N.
- Solder + flux + wire present.
**Evidence to save:**
- `docs/photos/hct-build/materials-spread.jpg` = single overhead photo of all items labeled
- `docs/pm/evidence/AF-053-materials-gathered.md` = quantity checklist Y/N for each item
**Safety considerations:**
No power. Materials-only.
**Known uncertainties:**
- Short-ship resolution = known before build (not discovered mid-stage).
**Failure response:**
Any missing item (HCT ×1 missing, etc.) → use BOM spare if available. If no spare → DO NOT start the 12-stage build. Wait for replacement item. Half-built HCT boards are unreliable.
**Source references:**
- PIN_LEVEL_APPENDIX.md §7 Parts inventory for one complete ESP32 signal adapter (PIN_LEVEL_APPENDIX.md#L234-L250)
- BOM.md HCT245N line, capacitor lines, perfboard, wire lines.
**Labels:** hardware controller-esp32 docs blocked blocked:exp-af-011 blocked:exp-af-052
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-054: Stage 1 perfboard prep. Mark rows for U1 U2, 5V/GND rails, HUB75 footprint. Mount DIP-20 sockets if purchased; direct solder otherwise. Visual check.

**Task ID:** AF-054
**Epic:** AF-002 (M1)
**Summary:** HCT build stage 1. Layout-mark perfboard. Mount DIP-20 sockets ×2 OR direct solder HCT ICs (notch aligned). Visual check.
**Description:** Stage 1 of 12. Layout the perfboard: choose locations for U1 (HCT #1), U2 (HCT #2), HUB75 2×8 header, 5 V and GND bus rails. Draw with pencil. Rule: U1 notch and U2 notch both oriented SAME WAY (e.g., notch at top = pin 1 top-left of each DIP-20), mounted side by side with ~3–5 holes spacing between for wire routing. If DIP-20 sockets purchased: solder sockets first (easy to replace IC later). If no sockets: direct solder HCT ICs in place (be careful of ESD). Visual check: no pins bent under, all pins through holes, notches aligned.
**Why this task exists:** 12-stage build per design spec. Stage 1 layout. Wrong orientation = pins 1/20 swapped = dead IC. The physical layout choice affects routing distance for all subsequent stages.
**Prerequisites:** COMPLETED: AF-053 (all materials gathered).
**Blocked by:** AF-053
**Required hardware:** Perfboard, 2× HCT245N (or 2× DIP-20 sockets if purchased), pencil for marking perfboard copper side, double-sided foam tape or helping hand to hold sockets during soldering.
**Required tools:** Soldering iron, solder, flux, magnifying glass, bright light.
**Required software:** PIN_LEVEL_APPENDIX §2 HCT245N pinout diagram (notch orientation reference).
**Exact execution steps:**
1. Place perfboard copper-side DOWN (component side UP) on workbench. Identify row/column grid.
2. Decide layout: leave 2 empty rows at top for 5 V rail, 2 empty rows at bottom for GND rail. Place U1 (socket or HCT) in rows 3–12 (10 pin rows = 20 pins for DIP-20). Place U2 in rows 3–12 too, offset 10+ columns to the right. Space for wires between U1 and U2 = at least 3 empty columns.
3. Place 2×8 HUB75 header footprint: choose a location near the perfboard EDGE (so HUB75 ribbon can extend off-board) — rows X…X+1 on right side, columns Y…Y+1. Leave 2 columns of margin.
4. Draw outline with pencil on component side for all 3. Label "U1" "U2" "HUB75".
5. If using DIP-20 sockets:
   5a. Insert U1 socket into holes. Ensure notch on socket matches your pencil layout (notch at top). Insert U2 socket same orientation notch at top.
   5b. Tape sockets down from component side so they don't fall out when flipping board.
   5c. Flip perfboard copper-side UP now. Solder 2 diagonal corner pins on U1 socket first. Check alignment → socket flush to board. If crooked → re-melt tack + reposition.
   5d. Solder remaining 18 pins of U1 socket.
   5e. Solder U2 socket same (2 tack → 18 remaining).
6. If NO sockets (direct solder HCT):
   6a. ESD-precaution: touch ground first. Hold HCT by body, not pins. Insert U1 pins through perfboard holes. Notch at top. Ensure no pins bent under.
   6b. Tape U1 down component-side. Flip copper-up. Solder 2 corner pins of U1 first → alignment check → remaining 18.
   6c. Repeat for U2: same notch orientation.
7. Visual inspection component-side: both ICs/sockets seated flat, not tilted. Notches facing same direction (both up). All 40 pins visible (no bent-under).
8. Magnifier check copper-side for any accidental solder bridges between adjacent perfboard pads of socket pins.
**Expected result:** 2 DIP-20 footprints soldered (or 2 HCTs direct-soldered), same notch orientation, flat, no bridges.
**Acceptance criteria / DoD:**
- U1 and U2 (or sockets) mounted: same notch orientation, flat against perfboard, tilted < 3°, all 20+20=40 pins visible through holes.
- Copper-side: no visible solder bridges between adjacent socket/HCT pins.
- HUB75 header area marked with pencil.
**Evidence to save:**
- `docs/photos/hct-build/stage1-layout.jpg` = component-side: both footprints + labels visible
- `docs/photos/hct-build/stage1-copper-side.jpg` = copper-side, no bridges
**Safety considerations:**
Soldering safety (as AF-049): fume extractor/ventilation, iron stand, lead-wash after.
No electrical power applied. HCT ICs ESD-sensitive: touch ground before handling if direct soldering (no sockets).
**Known uncertainties:**
- Uncertainty U-032 (HCT245 perfboard build reliability): Stage 1 sets up geometry; full resolution in Stage 12.
**Failure response:**
If any socket/HCT pin bent under → de-solder, straighten pin, reinsert. If lifted perfboard pad → use an adjacent pad (same row/col shift 1) if routing still works. If orientation wrong (one notch up, one down) → de-solder and flip before any other stage.
**Source references:**
- PIN_LEVEL_APPENDIX.md §2 SN74HCT245N PDIP pinout (notch orientation) (PIN_LEVEL_APPENDIX.md#L56-L91)
- Design spec §4c 12-stage extra-fine HCT build with per-stage continuity
**Labels:** hardware controller-esp32 blocked blocked:exp-af-053
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-055: Stage 2 U1 power & control. U1 VCC(pin20)→+5V, GND(pin10)→GND, DIR(pin1)→+5V, /OE(pin19)→GND. 4 individual continuity verifications.

**Task ID:** AF-055
**Epic:** AF-002 (M1)
**Summary:** HCT stage 2. U1 power + control pins only (4 pins). Solder then 4 individual continuity verifications each.
**Description:** Stage 2 of 12. U1 only. Four control/power pins (per PIN_LEVEL_APPENDIX §3 bottom rows): U1 pin 20 VCC → +5 V rail; U1 pin 10 GND → GND rail; U1 pin 1 DIR → +5 V (A→B direction per §2); U1 pin 19 /OE → GND (outputs always enabled). One solder joint per pin. After soldering EACH pin, verify continuity from that IC pin (or socket pin hole) to its destination rail. No other pins touched in this stage.
**Why this task exists:** Extra-fine split. Per-pin continuity before moving on. The 4 control pins must be correct BEFORE any signal wires — if DIR or /OE is wrong, all 8 signals pass through incorrectly and the issue is masked by later signal work.
**Prerequisites:** COMPLETED: AF-054 (U1 footprint mounted).
**Blocked by:** AF-054
**Required hardware:** Short pieces of 22–26 AWG wire (4 pieces, ~3–5 cm). Red for +5 V (pin 20, pin 1 DIR), black for GND (pin 10, pin 19 /OE).
**Required tools:** Soldering iron, solder, flux, wire stripper/cutter, multimeter continuity mode, magnifier.
**Required software:** PIN_LEVEL_APPENDIX §2 pinout (pin 1/10/19/20 positions).
**Exact execution steps:**
1. Cut + strip 4 short jumper wires: 2× red (for +5 V pins), 2× black (for GND pins). Strip ~3 mm each end.
2. **Joint 1: U1 pin 20 VCC → +5 V rail.** On perfboard copper side: solder one end of red wire to U1 pin 20 pad. Solder other end to the perfboard +5 V rail pad (a point in the top 2 rows you reserved for 5 V). Visual: good fillet, no bridge. Then: multimeter continuity: probe U1 pin 20 (at socket hole top, or HCT pin) → the +5 V rail point → BEEP (pass).
3. **Joint 2: U1 pin 10 GND → GND rail.** Black wire. Solder both ends. Continuity: U1 pin 10 → GND rail → BEEP.
4. **Joint 3: U1 pin 1 DIR → +5 V.** Red wire. (This makes A→B direction = ESP32 side A to HUB75 side B.) Solder both ends. Continuity: U1 pin 1 → +5 V rail → BEEP.
5. **Joint 4: U1 pin 19 /OE → GND.** Black wire. (/OE = Active Low; tied to GND = permanently enabled.) Solder both ends. Continuity: U1 pin 19 → GND rail → BEEP.
6. Cross-check: U1 pin 20 (+5 V rail) ↔ U1 pin 10 (GND rail) = NO beep (no short VCC↔GND through U1 body yet — should be high-impedance; if beep → short = bad).
**Expected result:** All 4 pins soldered correctly. 4/4 continuity beeps. Cross VCC↔GND no beep.
**Acceptance criteria / DoD:**
- 4 individual continuity beeps: pin 20→+5V, pin 10→GND, pin 1→+5V, pin 19→GND.
- Cross VCC↔GND no beep.
- No visual bridges to adjacent U1 pins (pins 2/3/4… or 18/17/16…).
**Evidence to save:**
- `docs/photos/hct-build/stage2-u1-power.jpg` = component side showing 4 wires routed
- 4 continuity multimeter photos or consolidated notes: each joint # + pass
- `docs/pm/evidence/AF-055-stage2-notes.md` = 4 continuity + cross-check results
**Safety considerations:**
Soldering safety. No energized parts.
⚠️ Future note: U1 DIR=+5V and /OE=GND are hard ties in this prototype. If a later design needs output-disable, /OE would be driven by GPIO, but for V1 prototype this is simplified.
**Known uncertainties:**
- None specific; this stage is mechanical solder + basic continuity.
**Failure response:**
Any of 4 continuity fails → re-solder that joint. Check for solder bridge to adjacent pin. If cross VCC↔GND beeped → short! Visually inspect for solder bridge between U1 pin 20 VCC and any nearby GND via. Wick if found. If no bridge found and still short → U1 defective (internal short — unlikely but possible) → replace IC/socket.
**Source references:**
- PIN_LEVEL_APPENDIX.md §2 (pin 1=DIR, pin 10=GND, pin 19=/OE, pin 20=VCC) (PIN_LEVEL_APPENDIX.md#L56-L91)
- PIN_LEVEL_APPENDIX.md §3 bottom rows "Direction / Enable / Ground / Supply" (PIN_LEVEL_APPENDIX.md#L107-L110)
- Design spec §4c 12-stage
**Labels:** hardware controller-esp32 power blocked blocked:exp-af-054
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-056: Stage 3 U1 decoupling. 100nF directly across U1 pin20↔pin10. Visual bridge check.

**Task ID:** AF-056
**Epic:** AF-002 (M1)
**Summary:** HCT stage 3. U1 decoupling: 100 nF ceramic cap across U1 VCC(pin20) ↔ GND(pin10), closest possible to IC. Visual bridge check.
**Description:** Stage 3 of 12. Decoupling cap for U1. 100 nF ceramic (non-polarized). Bend leads to shortest possible length. Solder one lead to the same pad row as U1 pin 20 (VCC side). Solder other lead to the same pad row as U1 pin 10 (GND side). Capacitor body should sit directly across the U1 footprint's short axis (end-to-end near the IC) for shortest leads = best decoupling. After soldering: magnifier visual check → no bridges. Optional: multimeter continuity across cap leads (ceramic = no beep — if beep → cap shorted → replace).
**Why this task exists:** Required for HCT IC stability — without close decoupling, high-speed HUB75 switching can cause glitches, reset, or noise. PIN_LEVEL_APPENDIX §2 bottom: "100 nF capacitor directly between U1 pin 20 and U1 pin 10".
**Prerequisites:** COMPLETED: AF-055 (U1 power pins accessible/soldered).
**Blocked by:** AF-055
**Required hardware:** 1× 100 nF ceramic capacitor (leaded).
**Required tools:** Soldering iron, solder, flux, pliers/dykes to bend/trim leads, magnifier, multimeter continuity (optional cap check).
**Required software:** None.
**Exact execution steps:**
1. Take one 100 nF ceramic cap. Note: non-polarized (no stripe) = either direction OK.
2. Bend leads 90° outward so the cap sits low-profile across U1. Leads length from cap body to pad = ~3 mm or less (short as possible). Trim if too long.
3. Solder lead A → U1 pin 20 pad (or the same perfboard hole immediately adjacent to pin 20).
4. Solder lead B → U1 pin 10 pad (adjacent perfboard hole to pin 10).
5. Trim excess lead length flush to solder joint.
6. Magnifier visual: no solder bridge between leads of cap; no bridge to adjacent U1 pins 19/18 or pin 1/2.
7. (Optional test: multimeter continuity across the 2 cap leads. Ceramic = should NOT beep. If beep → cap shorted internal. Remove + replace.)
**Expected result:** 100 nF cap soldered directly across U1 VCC/GND, short leads, no bridges.
**Acceptance criteria / DoD:**
- Cap physically present across U1 pin 20 ↔ pin 10.
- Visual no bridges (magnifier).
- Optional test: cap not shorted (no continuity beep across leads).
**Evidence to save:**
- `docs/photos/hct-build/stage3-u1-decouple.jpg` = close-up of U1 area with cap visible (short leads)
- `docs/pm/evidence/AF-056-stage3-notes.md` = visual pass confirmation + optional short-test result
**Safety considerations:**
Soldering safety. No power.
**Known uncertainties:**
- None; standard decoupling practice.
**Failure response:**
Bridge → wick. If cap shorted → remove, throw away, use second 100 nF cap (the one intended for U2; re-procure a spare for U2 later; or use a different-value ceramic from BOM spare if any available).
**Source references:**
- PIN_LEVEL_APPENDIX.md §3 smallest physical connections item 21: "100 nF capacitor directly between U1 pin 20 and U1 pin 10" (PIN_LEVEL_APPENDIX.md#L134-L134)
- Design spec §4c stage 3.
**Labels:** hardware controller-esp32 blocked blocked:exp-af-055
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-057: Stage 4 U2 power & control. Same 4 pins as U1. 4 continuity verifies.

**Task ID:** AF-057
**Epic:** AF-002 (M1)
**Summary:** HCT stage 4. U2 power + control (4 pins: pin20→VCC, pin10→GND, pin1→DIR→+5V, pin19→/OE→GND). 4 continuity each.
**Description:** Stage 4 of 12. Identical procedure to AF-055 but for U2 (second HCT245N). Four pins: U2 pin 20→+5 V, pin 10→GND, pin 1→+5 V (DIR A→B same as U1), pin 19→GND (outputs enabled). Four continuity per-pin verifications. Cross-check VCC↔GND no short.
**Why this task exists:** Extra-fine split per-stage. U2 power control = identical to U1 — any wiring mistake here affects C/D/E/CLK/LAT/OE (row address + control signals) → entire display broken.
**Prerequisites:** COMPLETED: AF-055 (method validated with U1) + COMPLETED: AF-054 (U2 footprint mounted).
**Blocked by:** AF-055, AF-054
**Required hardware:** 4 short wires (2 red: +5 V, 2 black: GND), same as AF-055.
**Required tools:** Same as AF-055.
**Required software:** PIN_LEVEL_APPENDIX §2 pinout.
**Exact execution steps:**
Identical to AF-055 steps 1–6 replacing "U1" → "U2":
1. Cut/strip 4 wires.
2. Joint 1: U2 pin 20 (VCC) → +5 V rail. Continuity beep.
3. Joint 2: U2 pin 10 (GND) → GND rail. Continuity beep.
4. Joint 3: U2 pin 1 (DIR) → +5 V. Continuity beep.
5. Joint 4: U2 pin 19 (/OE) → GND. Continuity beep.
6. Cross: U2 pin 20 ↔ U2 pin 10 → NO beep.
**Expected result:** 4/4 continuity, no short, no bridges.
**Acceptance criteria / DoD:** Same as AF-055 for U2 pins.
**Evidence to save:**
- `docs/photos/hct-build/stage4-u2-power.jpg`
- `docs/pm/evidence/AF-057-stage4-notes.md` = 4 continuity + cross-check
**Safety considerations:**
Same as AF-055.
**Known uncertainties:** None.
**Failure response:** Same as AF-055.
**Source references:**
- PIN_LEVEL_APPENDIX.md §2 pinout, §4 bottom rows Direction/Enable/Ground/Supply (PIN_LEVEL_APPENDIX.md#L152-L155)
- AF-055 procedure (analogous).
**Labels:** hardware controller-esp32 power blocked blocked:exp-af-056
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-058: Stage 5 U2 decoupling. 100nF U2 pin20↔pin10. Bridge check.

**Task ID:** AF-058
**Epic:** AF-002 (M1)
**Summary:** HCT stage 5. U2 100 nF decoupling cap directly across pin20↔pin10. Visual bridge check.
**Description:** Stage 5 of 12. Identical to AF-056 but for U2. 100 nF cap shortest leads across U2 VCC/GND. Visual magnifier bridge check. Optional short-test.
**Why this task exists:** Same decoupling reason as U1 for HCT stability. PIN_LEVEL_APPENDIX §4 smallest physical connections item 17.
**Prerequisites:** COMPLETED: AF-057.
**Blocked by:** AF-057
**Required hardware:** 1× 100 nF ceramic cap.
**Required tools:** Same as AF-056.
**Exact execution steps:** Same as AF-056 1–7, U2.
**Expected result:** Cap soldered, short leads, no bridges.
**Acceptance criteria / DoD:** Same as AF-056 for U2.
**Evidence to save:**
- `docs/photos/hct-build/stage5-u2-decouple.jpg`
- `docs/pm/evidence/AF-058-stage5-notes.md`
**Safety considerations:** Same as AF-056.
**Known uncertainties:** None.
**Failure response:** Same as AF-056.
**Source references:**
- PIN_LEVEL_APPENDIX.md §4 smallest connections item 17: "100 nF capacitor directly between U2 pin 20 and U2 pin 10" (PIN_LEVEL_APPENDIX.md#L175-L175)
- AF-056 (analogous).
**Labels:** hardware controller-esp32 blocked blocked:exp-af-057
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-059: Stage 6 U1 A-side inputs (ESP32 → U1 pins 2,3,4,5,6,7,8,9). 8 wires. Use EXACT GPIO numbers from PIN_LEVEL_APPENDIX §3 (ESP32 IO5,IO4,IO6,IO15,IO7,IO17,IO8,IO18). 8 individual continuity DoD per wire.

**Task ID:** AF-059
**Epic:** AF-002 (M1)
**Summary:** HCT stage 6. U1 A-side 8 input wires from ESP32 GPIO. EXACT GPIO per §3. 8 individual continuity DoD per wire.
**Description:** Stage 6 of 12. 8 wires from ESP32 dev board header → U1 A input pins (pins 2,3,4,5,6,7,8,9). USE EXACT NUMBERS from AF-052 final map (based on PIN_LEVEL_APPENDIX §3):
R1: ESP32 IO5 → U1 pin 2
G1: ESP32 IO4 → U1 pin 3
B1: ESP32 IO6 → U1 pin 4
R2: ESP32 IO15 → U1 pin 5
G2: ESP32 IO7 → U1 pin 6
B2: ESP32 IO17 → U1 pin 7
A: ESP32 IO8 → U1 pin 8
B: ESP32 IO18 → U1 pin 9
One wire = one solder joint at each end. After EACH wire: continuity from ESP32 header pin → U1 A-pin = BEEP. 8 wires → 8 individual continuity checks.
**Why this task exists:** RGB data + A/B row address are the highest-bandwidth 8 signals. Any mis-wire here = completely wrong colors / half-panel / wrong rows. Extra-fine split: 8 individual continuity DoD (not a group check).
**Prerequisites:** COMPLETED: AF-052 (final GPIO map), COMPLETED: AF-056 (U1 ready), COMPLETED: AF-049 (ESP32 headers soldered if needed).
**Blocked by:** AF-052, AF-056, AF-049 (if applicable)
**Required hardware:** 8× wires 22–26 AWG (~10–15 cm each), different colors if available per signal function (R/G/B rows = colored wires helpful for debug).
**Required tools:** Wire stripper/cutter, soldering iron, solder, flux, multimeter continuity, magnifier.
**Required software:** AF-052 final map open + PIN_LEVEL_APPENDIX §3 table open for cross-reference each wire.
**Exact execution steps:**
For each of the 8 rows (R1, G1, B1, R2, G2, B2, A, B):
1. Cut wire ~15 cm (leave service slack). Strip ~3 mm both ends.
2. ESP32 end: Find ESP32 header pin labeled with the exact GPIO # (e.g., IO5). Solder wire directly to the header pin (or use a female Dupont jumper crimped onto ESP32 side if using that method — solder perfboard side; Dupont only on ESP32 side is acceptable).
3. Perfboard U1 end: Solder wire to the exact U1 pin # A-input (2–9 as per list).
4. Visual check both ends: no bridge to adjacent pins.
5. **Continuity verification (MANDATORY for each wire):**
   Probe A = ESP32 GPIO header pin metal. Probe B = U1 A-input pin (perfboard pad or socket). Expected: BEEP. If NO beep → bad solder joint → re-do this wire step 2–4.
6. Wiggle test (optional but recommended): wiggle both ends. Continuity uninterrupted.
Do all 8 wires this way (R1→wire1, G1→wire2, B1→wire3, R2→wire4, G2→wire5, B2→wire6, A→wire7, B→wire8). Write a table: wire# function / ESP32 GPIO / U1 pin / continuity-pass.
7. Visual cross-check: no adjacent wires shorted (magnifier perfboard side U1 pins 2–9 row).
**Expected result:** 8/8 individual continuity beeps. All wires correctly routed per §3 map. No bridges.
**Acceptance criteria / DoD:**
- 8 continuity checks: ALL 8 beep individually.
- GPIO mapping exactly matches §3 (R1=IO5→pin2, G1=IO4→pin3, B1=IO6→pin4, R2=IO15→pin5, G2=IO7→pin6, B2=IO17→pin7, A=IO8→pin8, B=IO18→pin9).
- No solder bridges to adjacent U1 pins.
**Evidence to save:**
- `docs/photos/hct-build/stage6-u1-a-side.jpg` = perfboard U1 side wires visible, ideally color-coded
- `docs/pm/evidence/AF-059-stage6-continuity-table.md` = 8-row table: function, GPIO, U1 pin, pass/fail (all pass)
- 8 continuity multimeter photos consolidated or noted
**Safety considerations:**
Soldering safety. No power.
⚠️ Future: ESP32 GPIO outputs are 3.3 V level. HCT245 powered by 5 V with DIR=+5V translates 3.3 V A-input → 5 V B-output for HUB75. Correct direction critical.
**Known uncertainties:**
- If any GPIO from §3 was substituted in AF-052 → use the substituted number here, not the §3 original.
**Failure response:**
Single wire no continuity → re-solder both ends, most common issue = cold joint. If short between 2 adjacent U1 A-pins → wick and re-do 2 joints. If continuity goes to wrong U1 pin (e.g., R1 wire soldered to U1 pin 3 instead of 2) → desolder both ends and swap.
**Source references:**
- PIN_LEVEL_APPENDIX.md §3 Exact U1 pin allocation — 8 function rows + §3 smallest connections items 1–8 (PIN_LEVEL_APPENDIX.md#L97-L134)
- Design spec §4c stage 6
- AF-052 final map (supercedes §3 if substituted)
**Labels:** hardware controller-esp32 blocked blocked:exp-af-052 blocked:exp-af-056
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-060: Stage 7 U1 B-side outputs (U1 pins 18,17,16,15,14,13,12,11 → HUB75 R1,G1,B1,R2,G2,B2,A,B). 8 wires. 8 continuity DoD.

**Task ID:** AF-060
**Epic:** AF-002 (M1)
**Summary:** HCT stage 7. U1 B-side 8 output wires → HUB75 header exact positions per §5. 8 individual continuity DoD each wire.
**Description:** Stage 7 of 12. 8 wires from U1 B outputs (pins 18,17,16,15,14,13,12,11) → HUB75 2×8 header (PIN_LEVEL_APPENDIX §5 layout). Mapping (from §3 B-output column + §5 connector positions):
U1 pin 18 (B1 output) → HUB75 R1 position (§5 row 1, col 1)
U1 pin 17 (B2) → HUB75 G1 (row 1, col 2)
U1 pin 16 (B3) → HUB75 B1 (row 2, col 1)
U1 pin 15 (B4) → HUB75 R2 (row 3, col 1)
U1 pin 14 (B5) → HUB75 G2 (row 3, col 2)
U1 pin 13 (B6) → HUB75 B2 (row 4, col 1)
U1 pin 12 (B7) → HUB75 A (row 5, col 1)
U1 pin 11 (B8) → HUB75 B (row 5, col 2)
First: Solder the 2×8 keyed HUB75 male header to its footprint location on perfboard (stage 1 marked it; 16 pins). Solder it flat, 2 tack pins first. Then solder 8 B-wires. After each wire: continuity U1 B-pin → HUB75 pin = BEEP.
**Why this task exists:** U1 outputs go directly to HUB75 RGB + A/B. Wrong wiring → wrong colors / wrong row. 8 individual continuity per §3 smallest connections items 9–16.
**Prerequisites:** COMPLETED: AF-059. COMPLETED: AF-054 (HUB75 footprint pre-marked).
**Blocked by:** AF-059, AF-054
**Required hardware:** 2×8 keyed HUB75 male header ×1, 8× wires 22–26 AWG (~8 cm shorter — short distance within perfboard).
**Required tools:** Soldering iron, solder, flux, multimeter continuity, magnifier.
**Required software:** PIN_LEVEL_APPENDIX §5 layout (R1/G1/B1/GND/R2/G2/B2/E/A/B/C/D/CLK/LAT/OE/GND positions) open; §3 B-output mapping open.
**Exact execution steps:**
0. **Mount HUB75 header first:** Insert 2×8 male header into perfboard at marked location. Keyed ridge should face outwards (towards perfboard edge, so HUB75 ribbon cable can mate). Tape. Solder 2 diagonal pins tack → alignment (header perpendicular, flat) → solder remaining 14 pins. Visual no bridges between adjacent 16 pins (8 rows × 2 cols = adjacent pins are close). Continuity spot: probe 2 GND positions later (stage 10). Now:
For each of 8 B-side wires:
1. Wire ~8 cm. Strip ends.
2. Perfboard U1 end: solder to exact U1 B-pin (18,17,16,15,14,13,12,11 as listed per function).
3. HUB75 header end: solder to the exact HUB75 header PIN position matching the function label (§5). IMPORTANT: use the LABELS (R1, G1, etc.) to find positions on the 2×8 header you just mounted; §5 gives the column/row mapping. Confirm your header orientation matches §5 view (mating face view = same orientation as pins you solder).
4. Visual each joint.
5. Continuity: probe U1 B-pin → HUB75 pin = BEEP.
6. Wiggle test.
Write 8-row table: function, U1 B-pin, HUB75 position (row/col or label), continuity pass.
7. Visual cross-check 8 HUB75 header pin solder joints: no adjacent pin bridges.
**Expected result:** 8/8 continuity. HUB75 header mounted. No bridges.
**Acceptance criteria / DoD:**
- HUB75 2×8 header soldered flat, perpendicular, 16/16 pins soldered no bridges.
- 8 continuity: ALL beep (U1 pin 18→R1, 17→G1, 16→B1, 15→R2, 14→G2, 13→B2, 12→A, 11→B).
- All wires go to correct HUB75 LABEL positions (§5 map).
**Evidence to save:**
- `docs/photos/hct-build/stage7-hub75-header.jpg` = header mounted (mating face view showing keyed ridge out)
- `docs/photos/hct-build/stage7-u1-b-side.jpg` = 8 wires
- `docs/pm/evidence/AF-060-stage7-table.md` = 8-row continuity table
**Safety considerations:**
Soldering safety. No power.
**Known uncertainties:**
- Header orientation vs §5: always double-check by reading keyed ridge position relative to row/col; if you end up with row order swapped vs panel → fixable by software config later (row-swap) but prefer correct now.
**Failure response:**
Single continuity fail → re-solder. Wrong HUB75 position → de-solder both ends and move. If HUB75 header adjacent pin bridge → wick carefully between pins (use fine-tip wick).
**Source references:**
- PIN_LEVEL_APPENDIX.md §3 B-output column (PIN_LEVEL_APPENDIX.md#L97-L107) + smallest connections items 9–16 (PIN_LEVEL_APPENDIX.md#L122-L129)
- PIN_LEVEL_APPENDIX.md §5 provisional 2×8 HUB75 header layout (PIN_LEVEL_APPENDIX.md#L180-L208)
- PIN_LEVEL_APPENDIX.md §6 smallest connection list U1→HUB75 R1,G1,B1,R2,G2,B2,A,B (PIN_LEVEL_APPENDIX.md#L214-L221)
**Labels:** hardware controller-esp32 blocked blocked:exp-af-059
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-061: Stage 8 U2 A-side inputs (ESP32 IO10,IO9,IO16,IO12,IO11,IO13 → U2 pins 2,3,4,5,6,7). Pins 8,9 UNCONNECTED (confirm). 6 continuity DoD.

**Task ID:** AF-061
**Epic:** AF-002 (M1)
**Summary:** HCT stage 8. U2 A-side 6 input wires from ESP32 GPIO per §4. Pins 8+9 of U2 confirmed UNCONNECTED. 6 individual continuity DoD.
**Description:** Stage 8 of 12. 6 wires from ESP32 → U2 A-side inputs (pins 2–7). Pins 8 and 9 of U2 (A7/A8) ARE NOT WIRED — mark and confirm visually. Mapping per §4 (or AF-052 final map if adjusted):
C: ESP32 IO10 → U2 pin 2
D: ESP32 IO9 → U2 pin 3
E: ESP32 IO16 → U2 pin 4
CLK: ESP32 IO12 → U2 pin 5
LAT: ESP32 IO11 → U2 pin 6
OE: ESP32 IO13 → U2 pin 7
U2 pins 8, 9 = UNCONNECTED (leave empty; no solder). 6 wires → 6 individual continuity checks.
**Why this task exists:** U2 handles row address C/D/E (for 1/32 scan = need E) plus control CLK/LAT/OE. These are critical timing signals. Wrong wiring = panel doesn't scan or never latches properly. 2 unused channels confirmed unconnected (prevents accidental stray short later).
**Prerequisites:** COMPLETED: AF-058 (U2 ready), COMPLETED: AF-052 (final GPIO map).
**Blocked by:** AF-058, AF-052
**Required hardware:** 6× wires (~12–15 cm).
**Required tools:** Stripper, soldering iron, solder, flux, multimeter continuity, magnifier.
**Required software:** PIN_LEVEL_APPENDIX §4 table open, AF-052 map open.
**Exact execution steps:**
1. **Pre-check U2 pins 8 and 9:** visually confirm NO solder and NO wire attached to U2 pins 8 (A7) or 9 (A8). Leave empty. Write photo note later showing empty.
2. For each of 6 wires (C, D, E, CLK, LAT, OE):
   a. Cut/strip wire.
   b. ESP32 end: solder to correct GPIO header pin (IO10, IO9, IO16, IO12, IO11, IO13 respectively).
   c. U2 A-side end: solder to U2 pin 2, 3, 4, 5, 6, 7 respectively.
   d. Visual.
   e. Continuity: ESP32 GPIO pin → U2 A-pin = BEEP.
   f. Wiggle.
3. Write 6-row table: function, GPIO, U2 pin, pass.
4. Photo of U2 pins 8+9 = empty (unconnected).
5. Visual cross: no bridges between U2 pins 2–7 or 8/9.
**Expected result:** 6/6 continuity. Pins 8+9 empty. No bridges.
**Acceptance criteria / DoD:**
- 6 continuity: all 6 beep.
- U2 pins 8 and 9: visually confirmed NO wire, NO solder joint.
- Mapping exactly §4 or adjusted AF-052 map.
**Evidence to save:**
- `docs/photos/hct-build/stage8-u2-a-side.jpg` = U2 A-side showing 6 wires + pins 8/9 empty
- `docs/pm/evidence/AF-061-stage8-table.md` = 6-row table + pins-8-9-unconfirmed
**Safety considerations:**
Soldering safety. No power.
Unused pins 8, 9 left floating — acceptable for 74HCT unused inputs that are not 5 V tolerant on A side? No: per 74HCT245 datasheet, unused A-input channels are OK floating (but B-outputs will be undefined). In our design we don't connect B outputs of A7/A8 either, so floating A7/A8 is harmless.
**Known uncertainties:** None.
**Failure response:**
Any continuity fail → re-solder. If pins 8/9 accidentally have solder blob → wick clean.
**Source references:**
- PIN_LEVEL_APPENDIX.md §4 Exact U2 pin allocation 6 function rows (C,D,E,CLK,LAT,O→pins 2–7), note "pins 8,9 unconnected", smallest connections items 1–6 (PIN_LEVEL_APPENDIX.md#L142-L177)
- PIN_LEVEL_APPENDIX.md §4 row "unused: pin 8, 9 UNCONNECTED" (PIN_LEVEL_APPENDIX.md#L150-L151)
**Labels:** hardware controller-esp32 blocked blocked:exp-af-058 blocked:exp-af-052
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-062: Stage 9 U2 B-side outputs (U2 pins 18,17,16,15,14,13 → HUB75 C,D,E,CLK,LAT,OE). 6 wires. 6 continuity DoD.

**Task ID:** AF-062
**Epic:** AF-002 (M1)
**Summary:** HCT stage 9. U2 B-side 6 output wires → HUB75 positions (C,D,E,CLK,LAT,OE). 6 individual continuity DoD. U2 pins 11,12 UNCONNECTED (confirm).
**Description:** Stage 9 of 12. 6 wires U2 B outputs (18,17,16,15,14,13) → HUB75 header. Mapping:
U2 pin 18 (B1) → HUB75 C (§5 row 6, col 1)
U2 pin 17 (B2) → HUB75 D (row 6, col 2)
U2 pin 16 (B3) → HUB75 E (row 4, col 2)
U2 pin 15 (B4) → HUB75 CLK (row 7, col 1)
U2 pin 14 (B5) → HUB75 LAT (row 7, col 2)
U2 pin 13 (B6) → HUB75 OE (row 8, col 1)
U2 pins 11 and 12 (B8/B7 corresponding to unused A8/A7) = CONFIRMED UNCONNECTED (empty). 6 continuity per wire.
**Why this task exists:** C/D/E = full row address range needed for 1/32 scan (128×64 panel). CLK/LAT/OE = the 3 control signals that actually drive the shift registers. If any of these wrong → no image.
**Prerequisites:** COMPLETED: AF-060 (HUB75 header mounted), COMPLETED: AF-061.
**Blocked by:** AF-060, AF-061
**Required hardware:** 6× wires (~8 cm).
**Required tools:** Soldering, multimeter continuity, magnifier.
**Required software:** PIN_LEVEL_APPENDIX §4 B-output column + §5 HUB75 positions + §6 C,D,E,CLK,LAT,OE list.
**Exact execution steps:**
1. Confirm U2 pins 11, 12 = NO wire, NO solder. Visually inspect. Photo.
2. For each of 6 wires (C, D, E, CLK, LAT, OE):
   a. Wire 8 cm. Strip.
   b. U2 B-pin solder (18,17,16,15,14,13 respectively).
   c. HUB75 header correct position.
   d. Visual.
   e. Continuity: U2 B-pin → HUB75 pin = BEEP.
3. 6-row table.
4. Visual cross: no HUB75 adjacent bridges, no U2 bridges.
**Expected result:** 6/6 beep. Pins 11, 12 empty. No bridges.
**Acceptance criteria / DoD:**
- 6 continuity: all 6 beep.
- U2 pins 11, 12 empty (photo).
- HUB75 positions match §5 labels.
**Evidence to save:**
- `docs/photos/hct-build/stage9-u2-b-side.jpg`
- `docs/pm/evidence/AF-062-stage9-table.md` = 6-row table + pins-11-12-empty confirm
**Safety considerations:** Soldering safety. No power.
**Known uncertainties:** None.
**Failure response:** As AF-060.
**Source references:**
- PIN_LEVEL_APPENDIX.md §4 B-output column (PIN_LEVEL_APPENDIX.md#L142-L149) + smallest connections items 7–12 (PIN_LEVEL_APPENDIX.md#L165-L170) + pins 11/12 note unconnected (PIN_LEVEL_APPENDIX.md#L176)
- PIN_LEVEL_APPENDIX.md §6 U2→HUB75 C,D,E,CLK,LAT,OE (PIN_LEVEL_APPENDIX.md#L222-L227)
**Labels:** hardware controller-esp32 blocked blocked:exp-af-061
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-063: Stage 10 HUB75 header GND positions (2 pins) → common GND rail. 2 continuity DoD.

**Task ID:** AF-063
**Epic:** AF-002 (M1)
**Summary:** HCT stage 10. HUB75 2×8 header has 2 GND positions (§5 row 2 col 2 = GND; row 8 col 2 = GND). Solder both to common GND rail. 2 continuity DoD.
**Description:** Stage 10 of 12. HUB75 §5 layout shows 2 GND positions. Both MUST be connected to common GND rail (used by U1 pin 10, U2 pin 10, and later ESP32 GND). 2 wires (black preferred). After each: continuity GND position → common GND rail = BEEP.
**Why this task exists:** HUB75 panel has 2 GND pins on its connector. Both must be connected for signal return path and noise immunity. If only one GND connected → possible crosstalk or ground bounce glitches.
**Prerequisites:** COMPLETED: AF-062.
**Blocked by:** AF-062
**Required hardware:** 2× short black wires (~5 cm).
**Required tools:** Soldering, multimeter continuity.
**Exact execution steps:**
1. First GND position (§5 row 2 col 2, between B1 and E): solder short black wire → common GND rail (perfboard row reserved for GND, same as U1 pin 10 GND rail point). Continuity: HUB75 GND1 → GND rail = BEEP.
2. Second GND position (§5 row 8 col 2, below LAT/OE row): solder second short black wire → common GND rail point. Continuity: HUB75 GND2 → GND rail = BEEP.
3. Cross-check: HUB75 GND1 ↔ HUB75 GND2 = BEEP (they should be connected via rail now).
**Expected result:** 2 GNDs → GND rail both beep. GND1↔GND2 beep.
**Acceptance criteria / DoD:**
- 2/2 continuity: each GND → GND rail beep.
- Cross GND1↔GND2 beep.
**Evidence to save:**
- `docs/photos/hct-build/stage10-gnds.jpg`
- `docs/pm/evidence/AF-063-stage10-notes.md` = 3 checks all pass.
**Safety considerations:** Soldering safety.
**Known uncertainties:** None.
**Failure response:** Continuity fail → re-solder.
**Source references:**
- PIN_LEVEL_APPENDIX.md §5 GND at row 2 col 2 and row 8 col 2 (PIN_LEVEL_APPENDIX.md#L184-L198)
- PIN_LEVEL_APPENDIX.md §6 "common GND → both HUB75 GND positions" (PIN_LEVEL_APPENDIX.md#L228)
**Labels:** hardware controller-esp32 blocked blocked:exp-af-062
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-064: Stage 11 Optional 1000µF electrolytic across 5V/GND. Polarity verified. Visual.

**Task ID:** AF-064
**Epic:** AF-002 (M1)
**Summary:** HCT stage 11. OPTIONAL. If 1000 µF electrolytic present: solder across 5 V/GND. POLARITY VERIFIED. Visual check.
**Description:** Stage 11 of 12. OPTIONAL (skip if electrolytic not available). 1000 µF electrolytic capacitor. STRIPE on can = NEGATIVE. Negative lead → GND rail. Positive lead (other side, no stripe, longer leg usually) → +5 V rail. Mount near perfboard +5 V/GND input point. Visual check: stripe → GND.
**Why this task exists:** Bulk capacitance: absorbs momentary current spikes during full-white panel draws, preventing 5 V rail sag. Not strictly required for single-panel test (panel power has its own PSU bulk), but best practice. PIN_LEVEL_APPENDIX §7: 0–1 "optional bulk 5 V capacitance".
**Prerequisites:** COMPLETED: AF-063. AND (electrolytic present in inventory per AF-053).
**Blocked by:** AF-063
**Required hardware:** 1× 1000 µF / ≥10 V electrolytic capacitor, leads.
**Required tools:** Soldering iron, solder, flux, pliers, magnifier.
**Exact execution steps:**
1. Skip condition: if AF-053 noted electrolytic = No / absent → skip this task (write: "Skip — no optional electrolytic in inventory").
2. Identify polarity: can has a WHITE (or colored) STRIPE running vertically = NEGATIVE side. The leg on stripe side is typically shorter = NEG. Confirm: compare leg length (long leg = POS, short leg = NEG). Mark with Sharpie if unsure.
3. Bend leads. Length: mount cap near +5 V/GND input edge of perfboard. Trim to fit.
4. **NEG lead (stripe side, short leg) → GND rail solder.**
5. **POS lead (no stripe, long leg) → +5 V rail solder.**
6. Visual magnifier: stripe side clearly connected to GND. Legs not swapped. No bridges.
7. (Optional continuity test across cap leads: electrolytic = should NOT beep DC continuity immediately — may briefly beep as multimeter charges the cap then silence. If steady beep → shorted → remove and don't use; throw away.)
**Expected result:** Electrolytic mounted if present, stripe→GND, no short.
**Acceptance criteria / DoD:**
- If skip: documented reason "no electrolytic in inventory" or similar.
- If mounted: stripe (NEG) → GND. POS → +5 V. Visual pass. No short.
**Evidence to save:**
- `docs/photos/hct-build/stage11-bulk-cap.jpg` (close-up of stripe marking + wire connections) OR skip note.
- `docs/pm/evidence/AF-064-stage11-notes.md`
**Safety considerations:**
Soldering safety.
⚠️ Electrolytic POLARITY WARNING: Reverse polarity on aluminum electrolytic = capacitor may bulge, leak, or pop violently. Always confirm stripe→GND. Never reverse.
**Known uncertainties:** None.
**Failure response:**
If polarity accidentally reversed → DE-SOLDER IMMEDIATELY. Throw away the electrolytic. Do not re-use (may be damaged internally). If no spare → proceed without bulk cap (single-panel test should still work; we'll monitor voltage in EXP-011).
**Source references:**
- PIN_LEVEL_APPENDIX.md §7 inventory line "0–1 optional bulk 5 V capacitance" (PIN_LEVEL_APPENDIX.md#L241)
**Labels:** hardware controller-esp32 power conditional blocked blocked:exp-af-063
**Status flags:**
critical-path: candidate-yes
Conditional: yes
Skip condition: 1000 µF electrolytic capacitor not present in BOM inventory (per AF-053 check).

---

### AF-065: Stage 12 Full adapter end-to-end continuity + no-shorts check. 14 HUB75 signals (ESP32 header pin → HUB75 header position). Both IC VCC/GND. No shorts: adjacent pins, VCC↔GND.

**Task ID:** AF-065
**Epic:** AF-002 (M1)
**Summary:** HCT stage 12. FINAL adapter validation. 14 end-to-end continuity checks (ESP32 GPIO → HUB75 position via U1/U2). Both IC VCC/GND. Full no-shorts scan.
**Description:** Stage 12 of 12 = final build validation before any firmware flash/panel connection. Three check groups:
(A) 14 HUB75 signals END-TO-END: start at ESP32 GPIO header pin, traverse wire→U1/U2 A-pin→B-pin→wire→HUB75 position. Continuity each = BEEP. 14 signals: R1,G1,B1,R2,G2,B2,A,B,C,D,E,CLK,LAT,OE.
(B) Both IC power pins: U1 VCC→+5V, U1 GND→GND; U2 VCC→+5V, U2 GND→GND. Continuity each.
(C) NO-SHORTS: (i) Every adjacent pin pair on HUB75 header = NO beep between different signals. (ii) VCC ↔ GND across the perfboard = NO beep. (iii) Random other signal-to-signal pairs = NO beep.
**Why this task exists:** This is the definitive "adapter built correctly" check before powering anything. Catches any mis-wire, cold joint, or solder bridge. Every soldered joint verified in one comprehensive audit.
**Prerequisites:** COMPLETED: AF-064 (or skipped). All 11 prior stages done.
**Blocked by:** AF-064
**Required hardware:** Fully built HCT perfboard adapter + ESP32 wires attached. Still UNPOWERED.
**Required tools:** Multimeter continuity mode (beep), magnifier, notepad.
**Required software:** Complete continuity checklist table prepared: 14 + 4 = 18 "must beep" rows; several "must NOT beep" rows.
**Exact execution steps:**
**Group A — 14 end-to-end:**
For each of 14 signals (R1, G1, B1, R2, G2, B2, A, B, C, D, E, CLK, LAT, OE):
1. Probe A = ESP32 GPIO header pin (the one soldered/connected per §3/§4 map).
2. Probe B = HUB75 header pin at the position corresponding to that signal's label (§5 map).
3. Record: BEEP (pass) or NO beep (fail).
Group A count: 14 tests.
**Group B — IC power (4 tests):**
4. U1 pin 20 → +5 V rail point = beep.
5. U1 pin 10 → GND rail point = beep.
6. U2 pin 20 → +5 V = beep.
7. U2 pin 10 → GND = beep.
**Group C — NO SHORTS (≥20 tests):**
8. HUB75 header: every adjacent pin pair (row 1 col1↔col2, row 2 col1↔col2, … row 8 col1↔col2) = NO beep.
9. HUB75 header: every vertical neighbor pair (row 1 col1↔row 2 col1, etc. within each column) = NO beep.
10. VCC ↔ GND rail across perfboard = NO beep.
11. VCC ↔ any 14 signal pins = NO beep.
12. GND ↔ any 14 signal pins (except 2 GNDs on HUB75 which are GND, so exclude those) = NO beep.
13. Random cross: R1 (ESP32 side) ↔ G1 (HUB75 side) NO beep (different signals shouldn't short).
Write every result down. Photo of multimeter during any "fail" result.
**Expected result:** Group A 14/14 beep; Group B 4/4 beep; Group C all tests = NO beep (0 shorts).
**Acceptance criteria / DoD:**
- Group A: 14/14 end-to-end beep (0 fails).
- Group B: 4/4 power beep.
- Group C: 0 shorts (all no-beep tests = silent). If 1 short found → repair and RE-RUN entire Group C after fix.
**Evidence to save:**
- `docs/photos/hct-build/stage12-overview.jpg` = entire completed adapter overview photo
- `docs/pm/evidence/AF-065-stage12-full-audit.md` = Group A (14 rows), Group B (4 rows), Group C (≥20 rows) all pass/fail filled
- If any short found and fixed: photo of short location + after-fix re-audit
**Safety considerations:**
No power. Full audit OFF-line.
⚠️ Post-stage 12 note: once this passes, the adapter is "ready to power" but no HUB75 panel connection yet (that's EXP-011 with power order respected).
**Known uncertainties:**
- Resolves U-032 (HCT245 perfboard build reliability) → adapter continuity-wise sound.
**Failure response:**
ANY Group A no-beep → trace back. Find which segment breaks: ESP32 wire (solder re-do), U1/U2 A-side, or B-side wire. Re-do bad segment. Re-run that line.
ANY Group C short (beep where shouldn't): magnify area, wick bridge, re-test. If intermittent short → wiggle wires to find it.
**Source references:**
- PIN_LEVEL_APPENDIX.md §3/§4 complete mapping + §5 HUB75 positions
- PIN_LEVEL_APPENDIX.md §6 complete 14-wire HCT→HUB75 list + 2 GND (PIN_LEVEL_APPENDIX.md#L214-L228)
- Design spec §4c 12-stage final end-to-end
**Labels:** hardware controller-esp32 validation blocked blocked:exp-af-064
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-066: Flash known ESP32 HUB75 firmware (ESP32-HUB75-MatrixPanel-DMA or WLED-MM). Document version + board config.

**Task ID:** AF-066
**Epic:** AF-002 (M1)
**Summary:** Flash ESP32 with known working HUB75 library. Start with ESP32-HUB75-MatrixPanel-DMA. Document version + exact config.
**Description:** Choose firmware: (1) Preferred: `ESP32-HUB75-MatrixPanel-DMA` by mrfaptastic — most active ESP32 S3 DMA HUB75 lib. (2) Fallback: WLED or WLED-MM with HUB75 driver. Install Arduino IDE or PlatformIO. Add the library. Use the validated GPIO map from AF-052 (passing IO4,5,6,7,8,9,10,11,12,13,15,16,17,18). Configure: panel width=128, height=64, scan=1/32, chip=ICND2065 or default, color order RGB. Set DMA mode, enable PSRAM framebuffer. Flash via USB-C. Verify serial output = library version + panel configured. Document: library GitHub URL + version/commit hash; exact GPIO struct in code; compile flags; flash size/PSRAM enabled.
**Why this task exists:** EXP-011 firmware step: "start with existing library… avoid custom low-level firmware". Provides the display driver for EXP-011.
**Prerequisites:** COMPLETED: AF-050 (toolchain works), COMPLETED: AF-065 (adapter hardware verified).
**Blocked by:** AF-050, AF-065
**Required hardware:** ESP32-S3, USB-C data cable, PC. Perfboard adapter connected to ESP32 (the 14 GPIO wires attached already = part of stage 6/8).
**Required tools:** None physical.
**Required software:** Arduino IDE 2.x + ESP32 board package latest stable; `ESP32-HUB75-MatrixPanel-DMA` library (Library Manager search or GitHub master). WLED/WLED-MM as fallback.
**Exact execution steps:**
1. Clone or Library Manager install ESP32-HUB75-MatrixPanel-DMA. Record version (e.g., v2.0.8 or commit hash).
2. Open the basic example for ESP32 S3 or the "Pattern Test" example.
3. Edit the GPIO configuration struct in the example to use the AF-052 final 14 GPIO map exactly:
   R1=IO5, G1=IO4, B1=IO6, R2=IO15, G2=IO7, B2=IO17, A=IO8, B=IO18, C=IO10, D=IO9, E=IO16, CLK=IO12, LAT=IO11, OE=IO13.
4. Panel config: kPanelWidth=128, kPanelHeight=64, kScan=kScan32 (or 1/32).
5. PSRAM: enabled in board config menu ("ESP32S3 Dev Module" → PSRAM: "OPI PSRAM" or "Octal").
6. Flash size: 16 MB.
7. Compile. Fix any errors (lib config mismatch → adjust).
8. Flash to ESP32 via USB-C.
9. Open serial monitor 115200. Press RESET. Copy serial output (should print: library banner, panel init message, "PSRAM allocated X bytes", "DMA started").
10. If ESP32-HUB75-MatrixPanel-DMA fails compile/init → fallback try WLED-MM latest build with HUB75 ESP32-S3 config. Record which you used.
11. Document all in notes: library chosen, version, exact GPIO code lines, compile flags, serial output.
**Expected result:** ESP32 flashed, serial confirms library init, panel configured 128×64, PSRAM framebuffer allocated.
**Acceptance criteria / DoD:**
- Firmware flashed successfully (no flash error).
- Serial output shows library banner + panel dimensions = 128×64 + PSRAM in use.
- Exact GPIO map matches AF-052 (documented in notes with code snippet).
**Evidence to save:**
- `docs/pm/evidence/AF-066-firmware-config.md` = library name + URL + version, GPIO config code snippet, panel config, compile flags, full serial output
- Arduino IDE screenshot of Tools menu (PSRAM / flash size shown)
**Safety considerations:**
USB low voltage only. ESP32 powered from USB PC (5 V), no HUB75 panel, no PSU 5 V = low current safe.
⚠️ CH340 3-pin rule: NOT applicable here (using ESP32 native USB-C, not external CH340 adapter). If you ever use CH340 with ESP32 externally: TX/RX/GND only.
**Known uncertainties:**
- U-033 (library compatibility with 128×64 S3 + PSRAM DMA): preliminary data point here (init works).
- Fallback firmware choice: if first lib fails, second choice recorded.
**Failure response:**
Lib fails compile → check ESP32 board package version (may need 2.0.14+ or 3.0.2+). If lib crashes during init → check GPIO config for conflicting pin (e.g., wrong number). If no serial output → double-click BOOT then re-flash (download mode). If persistent fallback to WLED-MM.
**Source references:**
- EXPERIMENTS.md EXP-011 §Firmware: "start with existing library (ESP32-HUB75-MatrixPanel-DMA, or WLED/WLED-MM)" (EXPERIMENTS.md#L336-L337)
- PIN_LEVEL_APPENDIX §3/§4 GPIO map
- AF-052 final map
**Labels:** firmware controller-esp32 blocked blocked:exp-af-050 blocked:exp-af-065
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-067: ESP32 + HCT + 1 panel physical test (EXP-011). Standard Test Pattern Suite. Record refresh rate, color depth, PSRAM usage, stability, IC temp, flicker. DoD: correct 128×64, ≥1 hour stable.

**Task ID:** AF-067
**Epic:** AF-002 (M1)
**Summary:** EXP-011 full procedure. ESP32→HCT→panel 128×64. Standard Test Pattern Suite. Metrics. ≥1 hour stable.
**Description:** Execute EXP-011 verbatim. Power order strict: PSU ON → 5 V to HCT adapter (from PSU 5 V rail branch: adapter power) AND ESP32 powered via USB or PSU 5 V → wait → THEN panel power (already on PSU rail) → THEN HUB75 data already plugged (was plugged while OFF per HUB75 no-hot-plug rule). Actually the correct procedure: EVERYTHING OFF, HUB75 plugged (panel IN ↔ HCT adapter HUB75 header), then PSU ON → panel + HCT adapter power → controller (ESP32) powered → displays patterns. Run Standard Test Pattern Suite (EXPERIMENTS.md Shared): 1 solid R/G/B/W/B; 2 checkerboard; 3 lines; 4 gradient; 5 moving; 6 coordinate grid. For each: photo + Standard Defect Checklist. Record metrics: library-reported refresh rate Hz, color depth bits, PSRAM used bytes, stability pass/fail, HCT ICs temp (hand test or IR), ESP32 temp, visible flicker subjective rating (1–5). Run ≥ 1 hour continuous (alternating patterns or static full white stress at end).
**Why this task exists:** EXP-011 is the one-panel success criteria. U-033 (refresh quality, DMA/PSRAM behavior) resolved here. Feeds ADR-016 WF4 vs ESP32 scoring matrix EXP-014 cells.
**Prerequisites:** COMPLETED: AF-066 (firmware flashed), COMPLETED: AF-065 (adapter continuity pass), COMPLETED: AF-021 (panel 1 power verified), COMPLETED: AF-022 (HUB75 cable 1 orientation).
**Blocked by:** AF-066, AF-065, AF-021, AF-022
**Required hardware:** ESP32 dev board + HCT perfboard adapter fully wired + HUB75 0.5 m cable + panel #1 powered via harness branch + PSU + PC (for USB to ESP32 OR power ESP32 from PSU 5 V if already wired).
**Required tools:** Multimeter (DC V check at adapter 5 V rail), IR thermometer if available (hand back-of-2s OK), stopwatch/timer (1 hour), phone/camera.
**Required software:** Serial monitor open (for refresh rate/PSRAM metric reads).
**Exact execution steps (EXP-011 verbatim + safety):**
⚠️ First: confirm ALL POWER OFF (PSU OFF, wall out), HUB75 cable plugged in (ESP32 HCT adapter header → panel #1 IN). No HUB75 hot-plug.
1. Power OFF everywhere. HUB75 cable between adapter and panel #1 IN already plugged (keyed notch engaged, per AF-022). Labeled.
2. Also: connect HCT adapter's +5 V rail and GND rail to PSU 5 V fused branch (separate branch, not panel's; or use ESP32's 5 V input: common 5 V rail). Adapter 5 V rail → PSU 5 V+; adapter GND rail → PSU COM. **5 V high-current rule: if any wire carries ≥2 A continuous, use screw terminals or ferrules. Adapter current is tiny (mA range) → Dupont acceptable here; but panel power must NOT use Dupont.** Use ferrules at PSU screw posts for adapter power wire ends. Continuity before energize: adapter +5 V→PSU 5 V+ = beep, adapter GND→PSU COM = beep, no cross.
3. ESP32 power: either via USB-C to PSU/PC OR connect ESP32 5 V pin (VIN or 5 V) to same 5 V rail (prefer USB now for serial console during test).
4. Panel power: harness branch #1 → panel #1 4-pin (already AF-019/020/021 verified).
5. Cross-check all GNDs COMMON: PSU COM ↔ adapter GND ↔ ESP32 GND ↔ panel power GND = all beep continuity. (If grounds not common = level shifting won't work; panel won't read signals correctly.)
6. POWER UP ORDER (strict):
   a. Plug wall cord in.
   b. Stand aside. Flip C14/PSU switch ON. (Now: PSU ON, panel receives 5 V, HCT adapter receives 5 V, ESP32 if powered from rail boots. If ESP32 USB to PC → PC USB also provides 5 V boot; common GND OK.)
   c. Wait 10 s for ESP32 boot + firmware init.
7. Check serial: see "panel init done" or library message.
8. Run Standard Test Pattern Suite:
   8.1 Solid RED → photo. Check: every pixel red. Standard Defect Checklist.
   8.2 Solid GREEN → photo.
   8.3 Solid BLUE → photo.
   8.4 Solid WHITE → photo. (Run 10 min at full white later during stability.)
   8.5 Solid BLACK → photo (off).
   8.6 Checkerboard 8×8 or full → photo.
   8.7 Horizontal lines every row + vertical lines every 16 px → photo.
   8.8 Linear gradient → photo.
   8.9 Moving line or scrolling text (if example sketch has it) → 30 s watch. No flicker? Photo or 5 s video.
   8.10 Coordinate grid with corner labels → photo.
9. For each pattern: Standard Defect Checklist 10 items (AF-042 checklist applies same).
10. Read metrics from serial or library API:
    10.1 Reported refresh rate (Hz). Record.
    10.2 Color depth setting (bits/pixel). Record.
    10.3 PSRAM allocated / free bytes. Record.
11. Stability run: set display to rotating patterns or full-white stress. Start 1 hour timer. Sit nearby. Every 15 min:
    11.1 HCT U1 IC temp: back of hand 2 s (warm ≤ 40°C OK; too hot to touch > 50°C = bad).
    11.2 HCT U2 IC temp: same.
    11.3 ESP32 module temp: same.
    11.4 Visual: any blank, freeze, garble, flicker change?
12. At 1 hr mark: check all good. Take final photo of display.
13. POWER DOWN ORDER (strict, for HUB75 no-hot-plug):
    a. Switch OFF C14/PSU → wait 30 s → then wall plug out → only THEN any cable handling if needed later (do not unplug HUB75 while PSU ON ever).
**Expected result:** All 6 Standard patterns render correctly. 128×64 full pixels addressed. Refresh rate ≥ ~60 Hz (or whatever library reports; anything ≥30 Hz visually stable acceptable). No severe flicker. HCT ICs warm, not hot. 1 hour stable: no blank, no freeze, no garble.
**Acceptance criteria / DoD:**
- Correct 128×64 panel addressed (no missing rows, no repeated half, correct RGB color order).
- Standard Test Pattern Suite: all 6 pattern categories render, each photographed.
- Standard Defect Checklist 10 items ALL PASS (or ≤ 2 minor stuck pixels noted).
- Serial metrics recorded: refresh rate, color depth, PSRAM.
- 1 hour continuous stable: NO blank, NO freeze, NO garble, NO controller reset.
- HCT ICs temp: warm, not hot (hand touch OK = ≤ ~50°C subjective).
**Evidence to save:**
- 10 pattern photos (8.1 through 8.10)
- 1-hour run: 4 interval temp notes + final display photo
- `docs/pm/evidence/AF-067-exp-011-results.md` = full EXP-011 report: metrics table (refresh Hz, color depth bits, PSRAM bytes, stability, temps, flicker rating) + Standard Defect Checklist 10 passes
- Serial output excerpt of metrics
**Safety considerations:**
JIRA Safety Rules verbatim:
⚠️ No HUB75 hot-plug. Always power OFF controller + panel before connecting/disconnecting HUB75. Power order: PSU 5V → controller → panel data ON. Reverse to disconnect. (Fully complied: HUB75 plugged in step 1 with everything OFF. Power order PSU→controller→panel ON followed. Power OFF then wait before handling.)
⚠️ 5 V high-current rule. NO Dupont connectors for any continuous ≥2 A path. Use screw terminals, ferrule crimps, or polarized locking connectors. NO full-current series-through-multimeter — use clamp meter or measure voltage drop. (Panel power uses fused harness ferrule/barrel/screw = correct. HCT adapter current < 200 mA → Dupont wires OK. PSU end of adapter power: ferrules on screw posts.)
5 V HCT/ESP32 low voltage safe. Mains side: PSU OFF+wall out before any wiring changes.
**Known uncertainties:**
- Resolves U-033 (refresh quality, PSRAM/DMA behavior): actual measured data.
- Resolves U-027 (current draw contribution: indirectly from adapter temps vs 1 panel load).
**Failure response:**
No image at all → power off immediately. Check: all GNDs common? (Step 5). HCT DIR=+5V? (U1 U2 pin 1). /OE=GND? (pin 19). Firmware GPIO config matches physical? (AF-066 vs AF-052). Serial says init OK? Fix root cause before re-power.
Wrong colors → R/G/B swap: adjust color order parameter in firmware, reflash.
Severe flicker / garbled rows → check E pin wired correctly (U2 pin 16 → HUB75 E position §5 row 4 col 2; 1/32 scan needs E).
1 hour stability fails (blank/freezes/crashes) → serial after reboot: look for crash dump. PSRAM allocation failed? → increase PSRAM config or reduce framebuffer double-buffering.
HCT IC too hot to touch (> 60°C) → likely 5 V short somewhere on B-side outputs → power off, continuity check outputs, check for 5 V → signal line shorts.
**Source references:**
- EXPERIMENTS.md EXP-011 §Procedure §Electrical rules §Firmware §Success criteria (EXPERIMENTS.md#L322-L347)
- EXPERIMENTS.md §Shared Test References: Standard Test Pattern Suite + Standard Defect Checklist (EXPERIMENTS.md#L42-L65)
- PIN_LEVEL_APPENDIX.md Electrical rules recap (§2)
- JIRA.md Safety Rules (2) HUB75, (4) 5 V high-current
- Uncertainties U-033, U-027
**Labels:** hardware controller-esp32 power validation blocked blocked:exp-af-066 blocked:exp-af-065 blocked:exp-af-021 blocked:exp-af-022
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-068: Nano ↔ ESP32 wired transport (EXP-013). UART first. Small payload → framing (magic, length, frame#, checksum) → full 256×64 (49,152 B) → sequential frames. Measure transfer time, latency, drop/corrupt rate. Run ≥1 hour. Test native USB fallback candidate if UART inadequate.

**Task ID:** AF-068
**Epic:** AF-002 (M1)
**Summary:** EXP-013 full. Nano → ESP32 wired transport. UART first, then native USB fallback. Metrics. ≥1 hour.
**Description:** Execute EXP-013 verbatim. Candidate transports priority: (1) UART, (2) Native USB serial/JTAG if UART inadequate. On ESP32: write a small transport listener sketch (use the already working HUB75 framebuffer — extend firmware to accept incoming frame bytes over UART and write them directly to DMA buffer). On Nano: write Python transport sender (framing per §Procedure: magic 4 bytes, payload length u16, frame number u32, optional CRC32 checksum, then N payload bytes). Test phases: (a) small payload 1 KB → confirm frame reception; (b) full 256×64 RGB frame = 49,152 bytes → verify every pixel checksum correct; (c) sequential frames every N ms → sustained; (d) ≥ 1 hour run. Metrics: transfer time per 49,152 B frame (ms), end-to-end latency (ms — trigger on Nano → visible change on panel timed), dropped frames count, corrupted frames count. If UART < 1 fps → escalate to native USB fallback (task AF-070 optional exploration).
**Why this task exists:** EXP-013 = Nano→ESP32 transport experiment. Feeds ADR-017 (transport choice) + M1 gate-pass "programmatic" + ADR-016 criterion 6 (wired transport).
**Prerequisites:** COMPLETED: AF-067 (one-panel EXP-011 passes = framebuffer driver works), COMPLETED: AF-037 (Nano SW bootstrap committed, Python env ready), Nano physically available on bench, 3× jumper wires for UART (TX, RX, GND) OR USB-C cable for native USB between Nano USB host/device TBD.
**Blocked by:** AF-067, AF-037
**Required hardware:** Nano, ESP32 (on bench), jumper wires 22–26 AWG ×3 (UART) OR USB-C to USB-C or USB-A to USB-C cable (native USB). Common GND mandatory.
**Required tools:** Multimeter continuity (UART wire verify).
**Required software:**
Nano side: Python 3.x, pyserial library (`pip install pyserial`), optionally `crcmod`.
ESP32 side: Arduino or ESP-IDF sketch extending AF-066 firmware with UART listener or USB serial listener.
Both: `screen` or serial monitor for debug.
**Exact execution steps (EXP-013 verbatim):**
PHASE 1 — UART:
1. Wiring UART (PSU OFF, all off, no HUB75 changes): Nano GPIO for UART TX → ESP32 GPIO for UART RX. Nano GPIO for UART RX → ESP32 GPIO for UART TX. Nano GND → ESP32 GND (common ground — CRITICAL). Do NOT connect 3.3 V or 5 V between them (Nano independently powered from its own USB-C; ESP32 independently powered from PSU rail or its USB-C).
   ⚠️ CH340 3-pin rule (applied to general 3-board serial): "Connect ONLY TX/RX/GND. NEVER connect 5 V or 3.3 V from any adapter/board to an independently powered target board — risk of back-powering via protection diodes, or ground loop destroying USB port." (Follow strictly. 3 wires only.)
2. Continuity on 3 UART wires: Nano TX → ESP32 RX = beep. Nano RX → ESP32 TX = beep. Nano GND ↔ ESP32 GND = beep. No cross wires.
3. On ESP32: flash firmware = HUB75 driver + UART transport listener (baud rate: start 921600, optionally try 1500000 or 2000000 if both sides support). Serial2 typically on ESP32 for non-USB UART pins. Protocol:
   - Wait for magic bytes (4 bytes: e.g., 0xA5 0x5A 0x3C 0xC3).
   - Then read length (2 bytes, little-endian).
   - Then frame# (4 bytes, little-endian).
   - Then checksum CRC32 (4 bytes) of the frame payload.
   - Then read exactly "length" bytes into a buffer.
   - Verify CRC32 matches. If yes → write buffer to HUB75 DMA framebuffer + respond ACK byte. If no → respond NAK.
4. On Nano: write Python sender function implementing same framing.
5. Small payload test: send a 1024-byte payload. Check ACK.
6. Full frame test: send a 49,152-byte frame (256×64 RGB, one row of 2-panel logical = 49,152 B — for M1 single-panel we can test with 128×64×3=24,576 B as a simpler first, then do full 256×64 to gather M2-relevant data now). Measure transfer time (start of serial write → ACK received). Record. Verify pixel checksum correct on ESP32 side (read back framebuffer via debug serial, compare).
7. Latency measurement: From "Nano sender calls write()" to "first visible pixel change on panel". Use phone camera 240 fps video or stopwatch method (average 10 frames). Record ms.
8. Sequential frames: send 1 frame every T ms continuously. T chosen as the smallest that gives no drops in 1 minute.
9. 1-hour stability: run the sequential sender for 1 hour. Count dropped frames (NAK or timeout) and corrupted frames (CRC fail).
10. If UART transfer time for one 49,152 B frame > 1000 ms (i.e., < 1 fps) → escalate: go to native USB Phase 2 or task AF-070 optional exploration.
PHASE 2 — Native USB (only if Phase 1 UART < 1 fps or unreliable):
11. ESP32 native USB-C: Arduino config: "USB Mode: USB-OTG (TinyUSB) CDC". Serial class.
12. Nano USB-C host port → plug ESP32 native USB-C into Nano. Confirm Nano enumerates `/dev/ttyACM*`.
13. Repeat steps 5–9 at higher baud effectively (USB serial is full-speed USB 12 Mbps → should achieve >1 fps easily).
**Expected result:** UART preferred gives reliable 1+ fps for 49,152 B frames, 0 drops/corruptions in 1 hour, latency < 500 ms. If UART insufficient → native USB fallback works.
**Acceptance criteria / DoD:**
- UART Phase 1: full frame received correctly on ESP32 side (checksum pass).
- Transfer time per 49,152 B frame measured in ms.
- Latency ms measured.
- 1-hour run: drop rate < 1 frame per 1000, corruption rate = 0.
- If native USB fallback used: same metrics collected + pass.
**Evidence to save:**
- `docs/pm/evidence/AF-068-exp-013-results.md` = EXP-013 report: transport selected (UART/native USB), framing protocol spec, measurements table: transfer time ms, latency ms, fps achieved, drop count, corrupt count, 1-hour summary
- Nano Python sender code snippet (committed to repo)
- ESP32 listener code snippet (committed to repo)
- Serial monitor screenshot showing 1 hour run stats (if available)
**Safety considerations:**
⚠️ CH340 3-pin rule (by analogy to general serial inter-board): CONNECT ONLY TX/RX/GND between Nano and ESP32. NEVER connect 5 V or 3.3 V power rails between two independently powered boards. Both boards have their own 5 V power (Nano: USB-C from PSU or wall; ESP32: already on PSU 5 V rail via HCT adapter). Back-powering via GPIO protection diodes = destroy USB port / Nano / ESP32 chip. Strictly 3 wires only. Cross-check: Nano 5 V ↔ ESP32 5 V = NO continuity after wiring (they should be isolated except GND common).
5 V low-voltage safe. No HUB75 disconnection during this task (stays plugged from EXP-011).
**Known uncertainties:**
- Resolves U-034 (UART bandwidth adequacy) and U-035 (USB native firmware support).
- Feeds ADR-017 Nano→controller transport decision inputs.
**Failure response:**
UART < 1 fps → proceed Phase 2 native USB now or AF-070. Frequent CRC errors → lower baud rate, add hardware flow control if available (RTS/CTS pins), check cable length (shorter = better), add 100 Ω series resistors (slow edges). GND not common → add GND wire (MANDATORY). ESP32 crash during reception → check for stack overflow (increase stack size of UART task). 1-hour drops → retransmission logic: NAK → resend last frame.
**Source references:**
- EXPERIMENTS.md EXP-013 §Candidate transports, §Procedure 1–4, §Success criteria (EXPERIMENTS.md#L369-L391)
- EXPERIMENTS.md EXP-013 numbers: "256 × 64 × 3 = 49,152 bytes ≈ 48 KiB; at ~1.5 Mbps… acceptable for mostly-static dashboard" (EXPERIMENTS.md#L377-L378)
- JIRA.md Safety (3) CH340 3-pin rule (by analogy for any inter-board serial)
- Uncertainties U-034, U-035
**Labels:** firmware controller-esp32 nano spike validation blocked blocked:exp-af-067 blocked:exp-af-037
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-069: Pillow text renderer → ESP32 end-to-end test (arbitrary user-supplied string, NOT hard-coded). 10-min stable hold.

**Task ID:** AF-069
**Epic:** AF-002 (M1)
**Summary:** ESP32 candidate chain: user types arbitrary string → Nano Pillow renders → transport → ESP32 → panel. 10-min stable hold. This is the ESP32 subtrack terminal M1 pass.
**Description:** E2E chain: Nano side — write a small interactive script that: (a) prompts the human to type any string at runtime (NOT "Hello AI Frame" hardcoded — the executor TYPES IT IN when the script says "Enter text to display: "); (b) Pillow renders it onto a 128×64 framebuffer; (c) uses the AF-068 transport to send the frame to ESP32. ESP32 side: receives frame → writes to working HUB75 DMA buffer → panel displays it. Verify the string appears correctly. Hold: 10 minutes continuous. No blank, no freeze, no garble.
**Why this task exists:** This is the M1 terminal pass condition for the ESP32 subtrack (analogous to AF-046 for WF4). The 7-point M1 gate criteria from JIRA.md are all exercised here. AF-080 GATE-PASS aggregator checks: (WF4 AF-046 passes) OR (ESP32 AF-069 passes) → M1 gate passes.
**Prerequisites:** COMPLETED: AF-068 (transport works), COMPLETED: AF-030 (Pillow text renderer), COMPLETED: AF-035 (smoke test).
**Blocked by:** AF-068, AF-030, AF-035
**Required hardware:** Full chain on bench: Nano (USB-C powered), ESP32+HCT adapter (PSU 5 V), panel #1, transport wiring (3-wire UART or USB), PSU.
**Required tools:** None.
**Required software:** Nano: Python script (AF-030 Pillow renderer + AF-068 transport combined into one CLI tool). ESP32: AF-068 transport+display firmware.
**Exact execution steps:**
1. Power up order: PSU ON → panel + HCT adapter → ESP32 boots → Nano boots.
2. On Nano: run `python3 display_text_esp32.py` (or whatever filename). Script prompts: "Enter text to display: ".
3. The executor TYPES IN a string that was NOT previously hardcoded anywhere. Suggested test string: "The quick brown fox jumps over the lazy dog 1234567890!@#$%^&*()". (This exact string is fine — the point is it wasn't hardcoded into the script — executor typed it at runtime.)
4. Press Enter. Script renders → sends frame.
5. Observe panel: text appears. Compare character-by-character with what was typed. Match?
6. Start 10-minute timer. Do not touch anything. Observe periodically.
7. At 10 minutes: verify text still displayed correctly. No garble. No blank. No freeze.
8. Power down order strict: PSU OFF → wait 30 s → wall out.
**Expected result:** Typed arbitrary string appears correctly on panel #1, 10 minutes continuous, correct characters throughout, no anomalies.
**Acceptance criteria / DoD:**
- Arbitrary user-entered string (NOT hard-coded, NOT vendor software entry) appears correctly on panel #1.
- Source of render = Nano (Pillow), NOT direct from PC (confirmed by script running on Nano).
- Controller path used = ESP32 subtrack (documented).
- No manual vendor-software clicks DURING this update (setup/config OK; none were needed for ESP32 subtrack since open firmware).
- Text visibly correct on 1 physical panel (128×64). Every character matches.
- 10-minute stable hold: NO blank, NO freeze, NO garble.
- Safety checklists completed for every task in this path (AF-048 through AF-069: all hardware-touch tasks' safety items passed).
**Evidence to save:**
- Photo of Nano terminal prompt showing "Enter text: " + the typed string visible in terminal history
- Photo of panel #1 displaying the same string (2 photos side-by-side when compiled)
- Start timestamp + end timestamp = 10 min difference documented
- 10-min stable hold confirmation note
- `docs/pm/evidence/AF-069-esp32-e2e-pass.md` = evidence summary
**Safety considerations:**
JIRA Safety Rules verbatim:
⚠️ No HUB75 hot-plug. Always power OFF controller + panel before connecting/disconnecting HUB75. Power order: PSU 5V → controller → panel data ON. Reverse to disconnect. (Complied: no HUB75 changes this task.)
⚠️ 5 V high-current rule. Panel power = fused harness + ferrules. Correct.
⚠️ CH340 3-pin rule (inter-board transport): 3 wires only (TX/RX/GND). Correct.
All prior tasks safety checklists = passed by prerequisite chain.
**Known uncertainties:**
- Resolves U-036 (end-to-end text pipeline works on ESP32 chain).
- Final input to GATE-PASS AF-080.
**Failure response:**
Text not matching → check Pillow font rendering vs expected string; check transport frame bytes; check pixel swap (RGB vs BGR); debug in stages. Blank during hold → transport watchdog? Add heartbeat. Freeze → ESP32 crash → check serial for crash dump after reset. Garble → transport corruption → retransmission in AF-068 fix.
**Source references:**
- JIRA.md Milestone 1 requirements 1–7 (7-point gate)
- AF-046 (WF4 analog: same DoD pattern)
- Uncertainty U-036
**Labels:** validation controller-esp32 nano critical-path candidate-yes blocked blocked:exp-af-068 blocked:exp-af-030 blocked:exp-af-035
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-070: (Optional) Native USB transport fallback exploration — if UART bandwidth < 1 fps for 256×64, try USB serial/JTAG.

**Task ID:** AF-070
**Epic:** AF-002 (M1)
**Summary:** Skip unless AF-068 found UART < 1 fps. If so: ESP32 native USB OTG CDC, test same framing, re-measure metrics.
**Description:** Conditional skip: if AF-068 UART achieved ≥ 1 fps reliable → SKIP. If < 1 fps: proceed. Explore ESP32-S3 native USB-C OTG (TinyUSB CDC). Wire Nano USB host → ESP32 USB-C device. Nano enumerates `/dev/ttyACM0` at high speed. Same framing protocol. Repeat AF-068 measurements: transfer time, latency, 1-hour drops.
**Why this task exists:** U-035 fallback path if UART inadequate. EXP-013 candidate transport 2 "Native USB — much higher bandwidth and direct wired; unknowns are firmware support, complexity, and robustness" explored here only if first choice UART fails speed.
**Prerequisites:** COMPLETED: AF-068. AND CONDITION: UART < 1 fps for 49,152 B frame OR drop rate > acceptable.
**Blocked by:** AF-068
**Required hardware:** USB-C to USB-C cable (or USB-A to USB-C depending on Nano USB host port type). Nano, ESP32.
**Required tools:** None.
**Required software:** ESP32 Arduino: USB CDC mode enabled ("USB Mode: Hardware CDC and JTAG" or "TinyUSB CDC"). Nano: libusb or cdc_acm kernel driver.
**Exact execution steps:**
1. Skip condition check: AF-068 UART ≥ 1 fps → skip.
2. Else: reconfigure ESP32 firmware Tools→USB Mode to CDC. Flash.
3. Nano USB host port → ESP32 USB-C.
4. `ls /dev/ttyACM*` → find port.
5. Repeat AF-068 steps 5–9: small payload, full frame 49,152 B, transfer time, latency, drops, 1-hour run.
6. Document metrics comparison UART vs USB.
**Expected result:** Native USB ≥ 5 fps. 0 drops 1 hour.
**Acceptance criteria / DoD:**
- Skip documented OR:
- USB metrics recorded; transfer time < 200 ms for 49,152 B.
- Latency ms recorded.
**Evidence to save:**
- Skip note OR `docs/pm/evidence/AF-070-native-usb-results.md`
**Safety considerations:**
⚠️ USB 5 V: ESP32 side gets 5 V from USB; but ESP32 already powered from PSU rail → this creates dual 5 V sources unless Nano USB-C is a data-only connection with power pin disconnected. Data-only cable (cut 5 V line, or use power-blocking adapter) PREFERRED. Never connect two 5 V rails together. If in doubt: power the ESP32 ONLY from USB during this test (disconnect HCT adapter's 5 V rail from PSU → but then panel must be powered too — better: keep panel on PSU, disconnect ESP32 5 V rail wire, power ESP32 solely from USB data cable 5 V). Common GND still maintained via USB cable.
**Known uncertainties:** Resolves U-035 (USB native firmware support + robustness).
**Failure response:** USB CDC not enumerating → check ESP32 Arduino USB config mode (Hardware CDC vs TinyUSB; sometimes one works when other doesn't). If persistent → UART acceptable at < 1 fps for dashboard mostly-static content (M1 doesn't require video).
**Source references:**
- EXPERIMENTS.md EXP-013 §Candidate transports Native USB entry (EXPERIMENTS.md#L378-L379)
- Uncertainty U-035
**Labels:** firmware controller-esp32 spike conditional blocked blocked:exp-af-068
**Status flags:**
critical-path: candidate-yes
Conditional: yes
Skip condition: AF-068 UART transport achieves ≥ 1 fps for 49,152 B (256×64 RGB) frames with 0 drop/corruption over 1 hour.

---

### AF-071: EXP-011/EXP-013 result data commit — raw measurement tables.

**Task ID:** AF-071
**Epic:** AF-002 (M1)
**Summary:** Commit EXP-011 (AF-067) and EXP-013 (AF-068) raw measurement tables and evidence to repo evidence folder.
**Description:** Consolidate AF-067 EXP-011 results + AF-068 EXP-013 results into one commit under `docs/pm/evidence/`. Include all markdown tables, photos (referenced via paths), serial output excerpts. Photo binary files committed if git LFS available or stored locally per `.gitignore` with path reference.
**Prerequisites:** COMPLETED: AF-069.
**Blocked by:** AF-069
**Acceptance criteria / DoD:** 1 commit hash. EXP-011 + EXP-013 data all in evidence directory.
**Evidence to save:** git commit hash.
**Labels:** docs controller-esp32 validation
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-072: ESP32 M1 subtrack evidence commit + subtrack pass log.

**Task ID:** AF-072
**Epic:** AF-002 (M1)
**Summary:** ESP32 subtrack aggregator: commit all evidence, log subtrack = ESP32 candidate-pass (if AF-069 passes).
**Description:** If AF-069 DoD all pass → ESP32 subtrack = candidate-pass. Commit all 12-stage photos, firmware config, EXP-010, EXP-011, EXP-013 results, E2E pass log as a single commit. Write a subtrack-pass.md summarizing outcome for ADR-016 scoring.
**Prerequisites:** AF-071, AF-069 pass.
**Blocked by:** AF-071
**Acceptance criteria / DoD:** 1 commit hash, log says "ESP32 subtrack: candidate pass / fail".
**Evidence to save:** commit hash, pass log.
**Source references:** ADR-016 scoring matrix input (EXP-014).
**Labels:** docs controller-esp32 validation
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-073: (If needed) ESP32 alternative firmware evaluation — WLED-MM vs custom sketch comparison.

**Task ID:** AF-073
**Epic:** AF-002 (M1)
**Summary:** If ESP32-HUB75-MatrixPanel-DMA unsatisfactory (AF-066/AF-067 issues): compare WLED-MM. Document pros/cons.
**Description:** Skip condition: AF-066 and AF-067 pass with MatrixPanel-DMA → SKIP. If any issues, try WLED-MM latest. Same 1-panel test. Compare: refresh rate, flicker, PSRAM usage, API/protocol ease, compile/flash difficulty, project activity (GitHub stars/commits/year). Document which is better for V1.
**Prerequisites:** COMPLETED: AF-067 AND condition: AF-067 had DMA lib issues.
**Blocked by:** AF-067
**Acceptance criteria / DoD:** Skip documented OR 5-dimension comparison table + recommendation.
**Evidence to save:** Comparison table.
**Labels:** firmware controller-esp32 spike conditional
**Status flags:**
critical-path: candidate-yes
Conditional: yes
Skip condition: AF-067 and AF-066 both pass using ESP32-HUB75-MatrixPanel-DMA (no significant issues; single-panel 128×64 correct).

---

### AF-074: ESP32 thermal check — full-white 100% brightness, 15 min, HCT ICs + ESP32 temps hand-test or IR.

**Task ID:** AF-074
**Epic:** AF-002 (M1)
**Summary:** ESP32 full-white stress thermal. 15 min, 100% brightness. HCT U1/U2 + ESP32 temps.
**Description:** EXP-015-style for ESP32+HCT chain single-panel. Set display solid full-white, 100% brightness. Run 15 minutes. At 15 min: IR thermometer or back-of-hand on HCT U1, HCT U2, ESP32 module metal can. Record temps. Threshold: < 60°C = pass; 60–70 = warning; > 70°C = stop immediately and investigate (likely short).
**Why this task exists:** Thermal baseline for single-panel → feeds M2/M3 6-panel scaling expectations. ADR-016 criterion 8 "Reliability" input.
**Prerequisites:** COMPLETED: AF-067 (panel works), COMPLETED: AF-069 (optional, can do parallel).
**Blocked by:** AF-067
**Required hardware:** Full chain. IR thermometer if available (preferred); hand 2-second back test acceptable.
**Exact execution steps:**
1. Power up strict order. Display: solid white, 100% brightness (API call or constant set).
2. Timer = 15 min.
3. t=0, 5, 10, 15 min: temps:
   3a. HCT U1 top °C
   3b. HCT U2 top °C
   3c. ESP32 module top °C
   3d. Visual: any flicker change, color shift?
4. At 15 min: if any > 70°C → power OFF immediately. Else: continue 1 more min = finish.
5. Power down strict.
**Expected result:** Temps < 60°C all components. No visual change.
**Acceptance criteria / DoD:**
- Temps recorded 4 time-points × 3 components.
- All < 60°C = pass. 60–70 = warning noted.
**Evidence to save:**
- `docs/pm/evidence/AF-074-esp32-thermal.md` = temps table + visual observations.
- IR photos if available.
**Safety considerations:**
HUB75 no-hot-plug + power order rules apply.
High brightness = high panel current. Watch PSU and harness too (same as AF-021/EXP-003 extended).
**Known uncertainties:** Thermal baseline for U-022 contribution.
**Failure response:** > 70°C → OFF immediately. Check HCT outputs for short to GND (bridge on B-side to GND). Check VCC/GND near IC for decoupling cap contact. If ESP32 hot → maybe flash/PSRAM IC internal → less concern if within data sheet (ESP32 S3 data sheet says –40 to +85 °C operating; module top = 60°C still safe).
**Source references:**
- Analogous to EXPERIMENTS.md EXP-015 loaded PSU thermal (but single-panel mini version)
- Uncertainty U-022
**Labels:** hardware controller-esp32 thermal thermal-review validation blocked blocked:exp-af-067
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

### AF-075: ESP32 controller summary — candidate pass/fail determination, notes for ADR-016 scoring.

**Task ID:** AF-075
**Epic:** AF-002 (M1)
**Summary:** ESP32 controller M1 subtrack summary. Candidate pass/fail decision + 13-dimension notes for EXP-014 / ADR-016 scoring.
**Description:** Aggregate all ESP32 subtask results. Write a single-page summary answering ADR-016 scoring matrix 13 criteria (WF4 vs ESP32 rows): Hardware cost, Wiring complexity, Software complexity, Open protocol, Python integration, Wired transport, Refresh quality, Reliability, Boot/recovery, Maintainability, Custom firmware required, Mechanical size, Future flexibility. For each criterion, assign a preliminary ESP32 score (1–5 or qualitative) + cite measurement source (AF-0XX pass/fail, measurement Y). End with overall: ESP32 subtrack = CANDIDATE PASS or CANDIDATE FAIL.
**Why this task exists:** Feeds ADR-016 decision matrix EXP-014 directly. If M2 continues both tracks, these 13 criterion notes save rework.
**Prerequisites:** COMPLETED: AF-072, COMPLETED: AF-074.
**Blocked by:** AF-072, AF-074
**Acceptance criteria / DoD:** 13-criterion table filled for ESP32 column; each cell cites a specific measurement source (EXP-011 temp, EXP-013 latency, etc.). Overall candidate pass/fail determination.
**Evidence to save:**
- `docs/pm/evidence/AF-075-esp32-adr016-scoring-notes.md` = 13-criterion table + determination
**Source references:**
- EXPERIMENTS.md EXP-014 §Scoring matrix 13 criteria (EXPERIMENTS.md#L407-L424)
- ADR-016 (DECISIONS.md pending record)
**Labels:** docs controller-esp32 decision validation blocked blocked:exp-af-072 blocked:exp-af-074
**Status flags:**
critical-path: candidate-yes
Conditional: no

---

## End of M1 Subtrack (c): ESP32 Controller. Start Subtrack (d): WF2 Experimental Reference

---

### AF-076: VERIFY WF2 PCB rev, markings, photos. Identify port count, power connector.

**Task ID:** AF-076
**Epic:** AF-002 (M1)
**Summary:** EXP-001 WF2-specific identification. Photos front/back, PCB rev, markings, port count, power connector type.
**Description:** WF2 only (spike, not critical path): front/back photos; silk rev; count HUB75 ports (should be X1 + X2 = 2 outputs per §1 HD-WF2 test path overview); power input connector style (screw terminal? barrel?); compare to BOM description and PIN_LEVEL_APPENDIX §1 HD-WF2 test path (2× HUB75 outputs). This is WF2's equivalent of AF-038 (WF4) / AF-048 (ESP32).
**Why this task exists:** EXP-008 procedure step 1: "Identify the exact PCB revision; do not flash alternative firmware yet." Resolves WF2 form factor U-008 partial. U-008: WF2 q2 stock-firmware programmability first data needed.
**Prerequisites:** COMPLETED: AF-024 or AF-011 inventory identifying WF2 present on bench.
**Blocked by:** AF-024 (or AF-011)
**Required hardware:** HD-WF2 board as delivered.
**Required tools:** Camera, ruler/visual inspection.
**Required software:** PIN_LEVEL_APPENDIX §1 open for cross-reference.
**Exact execution steps:**
1. Photograph WF2 FRONT close-up: silk labels, HUB75 ports X1 X2, power connector, USB port if any, any buttons/LEDs.
2. Photograph WF2 BACK close-up: any silk, solder side.
3. Record PCB revision silk.
4. Count HUB75 ports: count = 2 (X1, X2) per §1? Record actual number + labels silk.
5. Power connector: identify mating connector type (barrel 5.5/2.1? Screw terminal block? Molex?). Photo.
6. USB port: present (Y/N)? USB-C / micro-USB / Type-A?
7. Any other connectivity: Wi-Fi antenna location? Ethernet?
8. Record all.
**Expected result:** 2 photos + clear descriptions of rev, 2 ports confirmed, connector types.
**Acceptance criteria / DoD:**
- 2 photos.
- PCB rev recorded.
- Port count = 2 or actual noted.
- Power connector type + USB type stated.
**Evidence to save:**
- 2 photos + `docs/pm/evidence/AF-076-wf2-identify.md`
**Safety considerations:** No power, identification only.
**Known uncertainties:** Resolves WF2 side of U-007/U-008.
**Failure response:** Missing marking → note "unknown". Wrong port count (e.g., 1 not 2) → flag in EXP-008.
**Source references:**
- PIN_LEVEL_APPENDIX.md §1 HD-WF2 test path overview + WF2-PWR-01 / WF2-SIG-01 notes (PIN_LEVEL_APPENDIX.md#L10-L53)
- EXPERIMENTS.md EXP-008 §Procedure step 1 (EXPERIMENTS.md#L256-L261)
- Uncertainties U-007, U-008
**Labels:** firmware controller-wf2 docs spike blocked blocked:exp-af-024
**Status flags:**
critical-path: no
Conditional: no

---

### AF-077: Stock firmware EXP-008: 1-panel basic tests, max layout, available communication methods. Result notes.

**Task ID:** AF-077
**Epic:** AF-002 (M1)
**Summary:** EXP-008 verbatim. WF2 stock firmware → 1 panel basic tests, max layout, available comm methods. Informational result notes.
**Description:** EXP-008 procedure. Repeat EXP-004's basic tests on WF2: connect 1 panel to X1 (and optionally X2 separately), use stock firmware interface (pushbuttons on-board? Huidu app? USB config?), set 128×64, run Standard Test Pattern Suite basic patterns (R/G/B/W/B), determine max practical stock layout (1 panel 128×64? 256×64 chain? 2 rows? record what works). Record available communication methods for future programmatic control: Wi-Fi (default SSID? AP mode?), USB (serial? mass storage?), any TCP/IP port found by nmap scan, any serial port detected.
**Why this task exists:** EXP-008 = stock WF2 characterization. JIRA scope: WF2 is "spike only, not on critical path" (M1 epic labels note). Produces the baseline against which alt firmware EXP-009 improvement is measured.
**Prerequisites:** COMPLETED: AF-076. (WF2 power connector wiring analogous to WF4 AF-039/040 done now as sub-steps of AF-077 if not already — but no separate tasks to keep spike small; include power wiring as step 1.)
**Blocked by:** AF-076
**Required hardware:** WF2, panel #1 or panel #2 available spare, PSU, HUB75 cable 0.5 m, power wire 18 AWG + mating WF2 power connector + ferrules at PSU.
**Required tools:** Multimeter, soldering/crimp, nmap on PC or Nano for network scan.
**Exact execution steps (EXP-008 verbatim):**
1. WF2 power wiring (PSU OFF, wall out): identify WF2 power connector from AF-076. Crimp/install mating connector. 18 AWG red/black. Ferrule at PSU 5 V+ branch / COM. Wires labeled "WF2 power" both ends. Continuity: correct V+/V-, no cross.
2. HUB75 cable WF2 X1 → panel #2 IN (use panel #2 so panel #1 still dedicated to WF4/ESP32 chain; orientation per AF-022 general rule; confirm notch). PSU OFF when plugging.
3. Power up order: PSU → WF2 → panel.
4. Access stock WF2 config: on-board pushbuttons if available, or vendor software (HD2018 same as WF4 family?), or USB. Set panel config = 1 panel, 128×64, 1/32 scan, color order trial.
5. Run stock built-in test patterns (whatever the firmware provides). Equivalent to: solid fills R/G/B/W/B, checkerboard, lines if available. Photo each.
6. Determine max practical stock layout: try 2-panel chain on X1 OUT → IN panel 2nd. Try X2 driving a separate 1-panel. Record max resolution achievable before display breaks up or controller refuses.
7. Available communication methods inventory:
   7a. USB: plug WF2 USB into PC. Does OS enumerate a serial port? Mass storage? Ethernet-over-USB? Record.
   7b. Wi-Fi: does WF2 boot up as AP (Wi-Fi scan shows "HD-WF2-XXXX" SSID?) Or does it try to STA connect? Record.
   7c. Network: if Wi-Fi connected, `nmap -p- WF2-IP` → find open ports.
   7d. Any other: TFTP server on WF2? HTTP server? Record.
8. Power down strict.
**Expected result:** Stock WF2 drives 1 panel correctly at 128×64 (or not). Max layout recorded. Comms methods inventory complete.
**Acceptance criteria / DoD:**
- Informational only (EXP-008 success criteria = "record supported panel behavior, output behavior, scan compatibility, controller limits, and interface options"). No hard pass/fail; record what works and what doesn't.
- 3 categories of facts written down: panel behavior, max layout, comms.
**Evidence to save:**
- 5 pattern photos (whatever built-in patterns available).
- `docs/pm/evidence/AF-077-exp-008-stock-results.md` = panel behavior notes, max layout, comms inventory
**Safety considerations:**
JIRA Safety Rules verbatim:
⚠️ No HUB75 hot-plug. Always power OFF controller + panel before connecting/disconnecting HUB75. Power order: PSU 5V → controller → panel data ON. Reverse to disconnect.
⚠️ 5 V high-current rule. WF2 draws less current than 6 panels but 5 V power wiring still ferrules at PSU; no Dupont for high-current; WF2 power connector itself is low current (~500 mA WF2 internal = acceptable with Dupont at board if that's the connector type).
**Known uncertainties:**
- Resolves U-008 (WF2 stock firmware programmability: comm methods inventory).
- Resolves U-009 (WF2 q2 serviceability partially: how many panels can it stock-drive?).
**Failure response:**
Panel won't drive with stock config → try color order swap, scan type 1/16 then 1/32 (some WF2 variants might be 1/16 default). If still no → note "stock WF4 firmware compatibility = unknown chip variant; proceed to EXP-009 alt firmware try".
**Source references:**
- EXPERIMENTS.md EXP-008 §Procedure, §Success criteria (EXPERIMENTS.md#L248-L266)
- PIN_LEVEL_APPENDIX.md §1 WF2-SIG topology reference
- Uncertainties U-008, U-009
**Labels:** firmware controller-wf2 validation spike blocked blocked:exp-af-076
**Status flags:**
critical-path: no
Conditional: no

---

### AF-078: Alt/open firmware EXP-009: backup stock → flash known WLED-MM / community build → 1-panel tests, Wi-Fi/serial control. Is alt firmware materially better for programmability/live-update/open-protocol?

**Task ID:** AF-078
**Epic:** AF-002 (M1)
**Summary:** EXP-009 verbatim. Backup WF2 stock firmware/config → flash known WLED-MM or community build → 1-panel tests. Answer: materially better?
**Description:** EXP-009 procedure. Preconditions met (AF-077 WF2 rev confirmed; recovery/reflash procedure understood; original firmware restorable OR loss acceptable per user OK). Procedure: (1) back up where possible (read flash via `esptool.py read_flash` if WF2 uses ESP inside — likely yes, WF2 typically = ESP32-based board like HD-WF2 = ESP32 + HUB75 driver on same PCB). (2) Select best-supported existing open firmware: WLED-MM with WF2 config if available, or ESP32-HUB75-MatrixPanel-DMA example adapted for known WF2 GPIO pinout (research community GitHub — "huidu wf2 wled" search). (3) Flash known existing image/build only (NO custom firmware first). (4) Test 1 panel: measure stable resolution 128×64, refresh behavior, Wi-Fi AP connect, serial/network control API availability. (5) Test whether live frame input is practical. Conclusion: is alt firmware MATERIALLY better for programmability/live-update/open-protocol than stock? If yes → keep alt firmware installed for reference. If no → revert stock.
**Why this task exists:** EXP-009 success criteria verbatim: "Alternative firmware is useful only if it materially improves programmability, live updates, open protocol support, or reliability without substantial custom firmware maintenance." Records WF2's open-firmware upside as a comparison point for ADR-016 (though WF2 not a direct candidate for 3-row architecture).
**Prerequisites:** COMPLETED: AF-077. AND: (a) exact WF2 rev confirmed, (b) recovery/reflash procedure understood, (c) original firmware restorable (or user accepts loss of stock firmware).
**Blocked by:** AF-077
**Required hardware:** WF2, USB cable for flashing (micro-USB or USB-C per AF-076), PC, panel.
**Required tools:** Multimeter, any special WF2 boot-mode pin if needed.
**Required software:** esptool.py (backup), WLED-MM firmware binary or ESP32-HUB75 DMA compiled for WF2 pinout (community build found), WF2 firmware flashing guide / community docs.
**Exact execution steps (EXP-009 verbatim):**
1. Back up: if WF2 is ESP32 based → hold BOOT button (or short GPIO0 to GND) at power up → download mode. `esptool.py --port PORT read_flash 0x0 0x1000000 wf2-stock-backup.bin` (adjust flash size to actual). Save backup file in a safe offline location (not just repo; cloud storage OK).
2. Find a known-working WF2 open firmware build: search GitHub / Discord / LED Matrix communities for "HD-WF2 WLED" or "HD-WF2 ESP32 HUB75 pinout". Record source URL + build date + commit.
3. Flash the known image: `esptool.py write_flash 0x0 wled-mm-wf2.bin` (or whatever offset per WLED standard).
4. Reset WF2. Wi-Fi AP should appear (default WLED AP: WLED-AP). Connect.
5. Configure WLED: LED preferences → Hue/Saturation or specifically WLED-MM Matrix setup: 128×64, HUB75 pins = WF2's actual pinout (community doc says what pins HD-WF2 routes). Apply.
6. Test 1 panel on X1: WLED effects → solid R/G/B → check colors. Measure max stable resolution (128×64 stable? Try 256×64 chain).
7. Wi-Fi control: from Nano or PC → `curl` WLED JSON API / XML API / UDP realtime (WARLS, DDP, TPM2.net?) → can you push a live frame?
8. Serial control: USB serial (if WLED exposes it) → serial API frame push.
9. Live frame practicality test: push a 128×64 frame over best available API. Measure round-trip. Repeat 100 times. Any drops?
10. Decision: materially better than stock for (1) programmability (can push arbitrary frame without vendor sw?), (2) live update (API available? 1+ fps?), (3) open protocol (standard DDP/TPM2/WLED-API vs closed). 3 yes/improvement vs stock = "materially better". Else: no.
11. If decision = materially better → keep alt firmware. Else: revert to backup (flash back `wf2-stock-backup.bin`).
**Expected result:** Alt firmware flashed; tests done; documented answer "materially better Y/N with 3 sub-reasons".
**Acceptance criteria / DoD:**
- Stock firmware backup file saved (checksummed: SHA256 stored).
- Alt firmware flashed successfully (WF2 boots, AP visible, WLED interface reachable).
- 1 panel driven correctly: colors/patterns match AF-067 or better.
- Decision documented: materially better? Y / N. Each Y/N cites evidence for 3 sub-dimensions.
**Evidence to save:**
- `docs/pm/evidence/AF-078-exp-009-alt-fw.md` = backup file SHA256, firmware source URL, config pinout, 3-dimension decision (Y/N + reasons), revert note if stock restored
- 2 pattern photos under alt firmware (red, gradient)
**Safety considerations:**
⚠️ Firmware flashing WF2: always have a backup. If flash fails midway → WF2 could be bricked (need to reflash via GPIO0 download mode repeatedly). ESP download mode recovery = usually always possible as long as chip is ESP-based → no permanent risk. Still: backup first.
HUB75 no-hot-plug + 5 V high-current rules apply same as AF-077.
**Known uncertainties:**
- Resolves U-008 q2 (WF2 open firmware programmability upside).
**Failure response:**
Flash fails / does not boot → revert backup. If backup won't restore → WF2 stuck in download mode → re-try different baud rate (lower 115200), different USB cable, press BOOT button during reset. If WF2 NOT ESP-based (unknown chip) → no esptool → skip alt flash; mark EXP-009 "inapplicable — WF2 chip non-ESP".
**Source references:**
- EXPERIMENTS.md EXP-009 §Preconditions, §Procedure 1–4, §Success criteria (EXPERIMENTS.md#L268-L289)
- EXPERIMENTS.md EXP-009 §Candidate software: "WLED-MM WF2 builds; ESP32-HUB75-MatrixPanel-DMA WF1/WF2 work" (EXPERIMENTS.md#L274-L275)
- Uncertainty U-008
**Labels:** firmware controller-wf2 spike blocked blocked:exp-af-077
**Status flags:**
critical-path: no
Conditional: no

---

### AF-079: WF2 reference subtrack summary. Commit evidence.

**Task ID:** AF-079
**Epic:** AF-002 (M1)
**Summary:** WF2 experimental reference subtrack aggregator: summary + evidence commit.
**Description:** Summarize WF2 subtrack outcome: EXP-008 stock + EXP-009 alt. Write 1-page summary: key facts learned, whether WF2 showed any surprise advantage that should make it a candidate for re-consideration (per JIRA scope: "WF2 is primarily a reference path unless testing produces an unexpected advantage"). If surprising advantage → flag to ADR-016. If no → note: "WF2: reference only, not candidate; WF4/ESP32 preferred candidates". Commit photos and summary.
**Prerequisites:** COMPLETED: AF-078.
**Blocked by:** AF-078
**Acceptance criteria / DoD:** 1 commit hash. 1-page summary. Surprise advantage Y/N with reason.
**Evidence to save:** commit hash, `docs/pm/evidence/AF-079-wf2-subtrack-summary.md`.
**Source references:**
- M1 epic scope: "WF2 Experimental reference subtrack (EXP-008 stock, EXP-009 alt firmware — spike only, not on critical path)"
- JIRA.md WF2 Known Areas: "Treat WF2 primarily as an experimental/reference/fallback path unless actual evidence gives it a clear advantage"
**Labels:** docs controller-wf2 spike
**Status flags:**
critical-path: no
Conditional: no

---

## End of M1 Subtrack (d): WF2 Reference. M1 GATE-PASS Aggregator

---

### AF-080: M1 One-Panel Gate. Verify AT LEAST ONE of (WF4 subtrack passes AF-046) OR (ESP32 subtrack passes AF-069). Document which track passed. DoD enumerates EXACT 7 conditions from JIRA.md Milestone 1:
1. Arbitrary text (not fixed, not vendor UI entry) sent to panel
2. Source = Nano (Pillow rendered, not direct from PC)
3. Controller path used (document which)
4. No manual vendor-software clicks DURING this update (setup OK)
5. Text visibly correct on 1 physical panel (128×64)
6. 10-minute stable hold (no blank/freeze/garble)
7. All safety checklists completed for every task used in this path

**Task ID:** AF-080
**Epic:** AF-002 (M1: One Nano-Driven Arbitrary-Text Panel — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** M1 GATE-PASS aggregator. Verify WF4 or ESP32 subtrack passed. Enumerate JIRA Milestone 1 EXACT 7 conditions. Pass M1.
**Description:** M1 = first gated milestone (JIRA.md Milestone 1 section, 7 requirements verbatim). At least one candidate controller path must have its terminal E2E pass task pass: WF4 path = AF-046 passed DoD? OR ESP32 path = AF-069 passed DoD? Both may pass (good — both candidates still alive for M2). Document WHICH path(s) passed. Walk the 7 conditions EXACTLY from JIRA.md Milestone 1, citing evidence for each. If both pass → M1 gate = DOUBLE PASS (best possible outcome; data for ADR-016 richer). If only one passes → M1 gate = SINGLE PASS. If zero → M1 gate = NOT PASSED → escalate (go back to failed subtrack terminal tasks, find why they failed, fix, re-run until one passes).
**Why this task exists:** This is the OFFICIAL M1 GATE (milestone #1 of 6). Epic AF-002 exit criteria. Entry to M2. Per JIRA.md gates: explicit gated milestones require enumerating every requirement. Per ADR-013/ADR-014 rules: no architecture decision before M2 data — but both candidates may still move forward to M2 (dual-track M2) if both pass here.
**Prerequisites:** COMPLETED: AF-046 OR COMPLETED: AF-069 (OR-semantics gate: either is sufficient. Both passing = bonus).
**Blocked by:** AF-046 OR AF-069
**Required hardware:** Any hardware remaining on bench from passed path. Physical evidence files present.
**Required tools:** None.
**Required software:** 7-condition checklist open. Evidence folders open.
**Exact execution steps:**
1. Check WF4 subtrack terminal: AF-046 DoD all 4 bullets pass? Y/N. Evidence present?
2. Check ESP32 subtrack terminal: AF-069 DoD all 7 bullets pass? Y/N. Evidence present?
3. Classify pass situation: BOTH-PASS / WF4-ONLY / ESP32-ONLY / NONE-PASS.
4. For EACH passing path (WF4 and/or ESP32): Enumerate JIRA Milestone 1 EXACT 7 conditions:
   Condition 1. Arbitrary text (not fixed, not vendor UI entry) sent to panel.
   Evidence: cite AF-046 photo of user-entered string OR AF-069 same. Confirm the string was NOT hard-coded.
   Condition 2. Source = Nano (Pillow rendered, not direct from PC).
   Evidence: AF-030 Pillow renderer commit; AF-045 (WF4) or AF-068 (ESP32) code runs on Nano.
   Condition 3. Controller path used.
   Document: "WF4 path (X1 → panel 1)" or "ESP32 path (U1/U2 HCT245 adapter → panel 1)" or both if both pass.
   Condition 4. No manual vendor-software clicks DURING this update (initial setup/configuration clicks OK, none during the actual update that displayed the arbitrary string).
   Evidence: WF4 path: AF-045/AF-046 scripts ran without vendor app open during update; or ESP32 path: no vendor software used at all (open firmware).
   Condition 5. Text visibly correct on 1 physical panel (128×64).
   Evidence: photo of panel with string vs terminal input.
   Condition 6. 10-minute stable hold (no blank/freeze/garble).
   Evidence: start+end timestamps, 10 min stable notes in AF-046/AF-069.
   Condition 7. All safety checklists completed for every task used in this path.
   Evidence: Walk every task in the chosen path's chain:
   - If WF4: AF-038 through AF-046 → each task's Safety considerations section completed by executor, no safety checklist skipped (visual pass, mains pass, HUB75 no-hot-plug pass, 5 V high-current pass per evidence notes).
   - If ESP32: AF-048 through AF-069 → each Safety considerations section executed. All applicable HUB75 no-hot-plug, 5 V high-current, CH340 3-pin rules followed per evidence.
5. If BOTH WF4 AND ESP32 passed → note: "M1 gate = DOUBLE PASS. Both controller candidates progress to M2 dual-track. EXP-014 matrix enriched."
6. If SINGLE passed → note: "M1 gate = SINGLE PASS via [path]. Other path may continue running for data collection but does not block M2 start per M1 exit OR-semantics."
7. If NONE passed → STOP. M1 gate NOT PASSED. Write remediation plan. Do NOT proceed to M2 tasks until at least one path's terminal E2E passes.
**Expected result:** M1 gate PASSED (at least one path). 7 conditions × 1 or 2 paths = 7 or 14 evidence citations all satisfied.
**Acceptance criteria / DoD:**
- At least 1 of 2 paths (WF4 AF-046 passes OR ESP32 AF-069 passes) = TRUE.
- For EACH passing path: 7 conditions enumerated EXACTLY as JIRA.md Milestone 1 listed (wording matches), each condition has a specific evidence citation (AF-###, file, photo, commit hash).
- Condition 7 explicitly audits every individual task's safety checklist in the chain of the chosen path (3–20 tasks, all accounted for Y/N passed).
- Final line: "M1 gate: PASSED via [WF4 / ESP32 / BOTH]".
**Evidence to save:**
- `docs/pm/evidence/AF-080-m1-gate-pass.md` = full 7-condition audit (7×1 or 7×2 = 7 or 14 rows) + final pass determination line + chain-of-path safety audit per condition 7
**Safety considerations:**
Condition 7 itself audits all safety rules for every hardware task in the path. JIRA.md Safety Rules (1) mains 8-point, (2) HUB75 no-hot-plug, (3) CH340 3-pin, (4) no-Dupont-5V were all verbatim included in every applicable task (C14 wiring tasks, WF4/ESP32 power tasks, HUB75 plug tasks, inter-board transport tasks). Condition 7 = they all passed.
No new hardware work in this gate task; audit only.
**Known uncertainties:**
- None. Gate task resolves state.
- Pass count (single or double) determines M2 track count.
**Failure response:**
If 0/2 paths pass → identify nearest failing task. Example: WF4 AF-045 programmatic updates fail due to Huidu protocol library unmaintained → fall back to ESP32 path AF-069 terminal → debug that. If both near-success (missing 10 min hold by 1 garble) → extend test with root-cause fix (e.g., add CRC retransmission on transport). NEVER advance to M2 tasks before M1 gate = PASSED.
**Source references:**
- JIRA.md Milestone 1 requirements 1–7 VERBATIM (JIRA.md#milestone-1-one-physical-panel paragraph)
- Epic AF-002 scope + Exit / gate criteria: "AT LEAST ONE of (WF4 controller subtrack passes) OR (ESP32 controller subtrack passes). Exact 7 conditions from JIRA.md Milestone 1 requirements enumerated in GATE-PASS AF-### task's DoD."
- M1 epic Parallelism notes: WF4 / ESP32 / WF2 / Nano = 4-way parallel. Nano SW subtrack always unconditional because needed regardless of controller choice (covered by AF-030/035/037 already done prerequisites chain).
- Requirements matrix R001, R003, R004 (V1 = Nano-driven arbitrary text end-to-end)
**Labels:** validation decision critical-path yes blocked blocked:exp-af-046 blocked:exp-af-069
**Status flags:**
critical-path: yes
Conditional: no

---

## EPIC AF-003: M2 Two-Panel 256×64 Logical Canvas — GATED

### AF-081: VERIFY Panel #2 polarity (power OFF) + HUB75 orientation check — independent of panel 1's earlier batch verify (re-verify since we're now about to chain)

**Task ID:** AF-081
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** Re-verify panel #2 power polarity marking and HUB75 IN/OUT orientation (power OFF) since panel now leaves batch storage for active chaining.
**Description:** Panel #2 was batch-verified in M0 (AF-023/025 style). Before actively wiring into M2 chain, re-confirm: (a) 5 V input terminal labels match V+/V- silk on panel back, (b) HUB75 IN vs OUT port labels correct and visible, (c) orientation label (top/bottom or dot mark) matches panel #1's convention so both panels mount same way in eventual frame. This is NOT a repeat of the full M0 batch verify — just a targeted re-check because the panel is about to be physically moved and plugged in.
**Why this task exists:** JIRA Safety Rules. Panel orientation mix-up (panel flipped 180°) would cause left/right order inversion or color/row errors in M2 chain. Moving panels from storage to bench increases risk of accidental reverse orientation.
**Prerequisites:** COMPLETED: AF-080 (M1 gate passed). Panel #2 retrieved from storage onto bench.
**Blocked by:** AF-080
**Required hardware:** Panel #2 (physical unit). AF-022/AF-023 orientation reference photos (from M0 batch).
**Required tools:** Camera. Multimeter (beep continuity mode, optional: for re-checking V+/V- labels match internal silk).
**Required software:** None.
**Exact execution steps:**
1. PSU OFF. Wall plug removed. No power anywhere on bench.
2. Place panel #2 on ESD mat face UP (LED side visible). Compare orientation to panel #1's face-up position: same "top" edge direction? Note.
3. Flip panel #2. Inspect 5 V input terminal block: labels (V+, GND/V-) silk visible. Match to M0 AF-023 photo of panel #2. Confirm same.
4. Inspect HUB75 ports: find IN and OUT labels silk on PCB. Confirm they exist and match M0 photo.
5. Optional continuity: multimeter beep between 5 V input V+ screw → any large bulk capacitor + terminal on PCB. Beep = correct label. Similarly V- → chassis GND plane beep.
6. Take 1 photo of panel #2 back side (5 V block + HUB75 ports with labels visible).
7. Result: orientation PASS / FAIL / NOTES.
**Expected result:** Panel #2 polarity labels match M0 record. HUB75 IN/OUT labeled. Orientation same convention as panel #1.
**Acceptance criteria / DoD:**
- 1 re-verify photo of panel #2 back.
- Polarity labels confirmed = match M0.
- HUB75 IN/OUT ports confirmed labeled.
- Physical orientation (top edge) convention matches panel #1.
**Evidence to save:**
- `docs/pm/evidence/AF-081-panel2-reverify.jpg` (back with labels)
- 1-line pass note in evidence folder.
**Safety considerations:**
Power OFF for entire task — no safety issues. Safety value = prevents reverse polarity in step AF-082 (prevents panel damage from reversed 5 V).
**Known uncertainties:**
- None. Straightforward re-verify.
- If panel #2 orientation DOESN'T match panel #1 → this is the first discovery, handled in Failure response below.
**Failure response:**
If V+/V- silk mismatch with M0 photo → STOP. Do NOT wire. Reverse at PSU harness end (swap red/black at panel #2 end ferrule) OR note the panel's V+/V- are internally flipped vs panel #1 and wire accordingly (but document prominently in wiring diagram). If HUB75 IN/OUT labels missing → use continuity: Panel1 OUT → Panel2 pin 1 (red wire) should NOT connect to Panel2 shift register outputs (test with meter to known shift-register pin if traceable). If orientation flipped → label panel #2 top edge with tape so it mounts flipped in chain (will cause content rotation per panel; fix in software config by rotating that panel's output).
**Source references:**
- JIRA.md §Safety Rules (4) no-Dupont-5V (panel power polarity relates to this rule's sibling concerns).
- Epic AF-003 scope + Exit criteria paragraph.
- M0 analog: AF-022 (panel 1 HUB75 orientation), AF-023 (panel 1 polarity).
**Labels:** hardware polarity-verify validation blocked blocked:exp-af-080
**Status flags:**
critical-path: yes
Conditional: no

---


### AF-082: VERIFY Panel #2 power connector at PSU harness branch end (PSU OFF, confirm V+/V- correct, continuity match). Extra-fine polarity verify (separate from energize, per JIRA safety rule #2 ordering).

**Task ID:** AF-082
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** Panel #2 power branch #2 extra-fine polarity check at PSU harness end — power OFF, continuity match V+/V- pairs match panel #2 labels.
**Description:** PSU OFF, wall plug OUT. Probe harness branch #2 (the one that will feed panel #2). Confirm: (a) harness branch #2 red wire → panel #2 V+ pin beeps; (b) harness branch #2 black wire → panel #2 V- pin beeps; (c) NO cross-pair beep (red harness ↔ black panel = NO beep; black harness ↔ red panel = NO beep). This is explicitly SEPARATE from the energize task, per JIRA safety rule ordering: polarity verify OFF → then energize ON in a DIFFERENT task (AF-083).
**Why this task exists:** JIRA Safety Rules ordering principle: every energize task MUST be preceded by a power-OFF-only dedicated polarity verify task. Resolves U-002 (polarity) for panel #2's active harness branch. Prevents reverse-5V panel damage at AF-083 energize.
**Prerequisites:** COMPLETED: AF-081 (panel #2 polarity/orientation re-verified). PSU OFF, wall plug REMOVED from socket. Harness branch #2 loose ends visible at both PSU side and panel #2 side (not yet screwed into PSU terminals, not yet plugged into panel #2 power block).
**Blocked by:** AF-081
**Required hardware:** Panel #2 (on bench), PSU harness branch #2 (2 of 8 branches from BOM H-07 2-harnesses = 8 total; we use branch #2 from harness #1 or #2 as labeled during M0 AF-011), ferrule-crimped ends if not yet crimped.
**Required tools:** Multimeter in continuity (beep) mode. Probe tips clean.
**Required software:** None.
**Exact execution steps:**
1. Wall plug REMOVED from socket. PSU switch OFF. Multimeter in continuity beep mode. Probes touch test → beep.
2. Locate PSU harness branch #2 (labeled "branch #2" on harness — if unlabeled → choose 2nd branch of 4 on the 1-to-4 harness closest to panel #2's physical location and label it with tape "BR-2 PANEL-2" now).
3. At panel #2's rear 5 V power terminal block (not yet plugged in): identify V+ and V- silk labels (confirmed in AF-081).
4. Test 1 (correct V+): Probe harness branch #2 RED wire ferrule end ↔ panel #2 V+ terminal screw (or contact pin of power connector matching V+). Expected: BEEP.
5. Test 2 (correct V-): Probe harness branch #2 BLACK wire ferrule end ↔ panel #2 V- (GND) terminal. Expected: BEEP.
6. Test 3 (cross-no-short V+): Probe harness branch #2 RED ↔ panel #2 V-. Expected: NO BEEP.
7. Test 4 (cross-no-short V-): Probe harness branch #2 BLACK ↔ panel #2 V+. Expected: NO BEEP.
8. Test 5 (no harness internal short): Probe harness branch #2 RED ↔ harness branch #2 BLACK (both ferrule ends, no panel connected). Expected: NO BEEP.
9. Write all 5 results on paper taped to harness.
**Expected result:** 3 beeps (tests 1,2) + 2 no-beeps (tests 3,4) + no harness short (test 5) = all 5 pass.
**Acceptance criteria / DoD:**
- Test 1 BEEP (red harness ↔ V+ panel).
- Test 2 BEEP (black harness ↔ V- panel).
- Tests 3,4 NO beep (no cross).
- Test 5 NO beep (no harness internal short).
- All 5 results written and taped.
**Evidence to save:**
- `docs/pm/evidence/AF-082-panel2-harness2-continuity.jpg` (probe positions visible + multimeter display beep-icon where applicable)
- `docs/pm/evidence/AF-082-panel2-harness2-notes.md` (5 rows pass/fail)
**Safety considerations:**
⚠️ Polarity verification is power-OFF only. PSU switch OFF AND wall plug REMOVED before probing. Multimeter in continuity mode (beep). Correct polarity = beep only on matching pairs (red-wire-harness-end ↔ panel-V+-pin, black-wire-harness-end ↔ panel-V--pin). Cross-pair continuity = V+ ↔ V- MUST NOT beep — beep means short, DO NOT energize, fix first.
**Known uncertainties:**
- If harness branch #2 ferrules are NOT YET CRIMPED → do NOT proceed; stop and insert AF-### ferrule crimp task for branches #2 (and #3,#4,#5,#6 for later use). This is an implicit precondition checked at runtime.
**Failure response:**
Any cross-pair beep (tests 3,4,5) → STOP. DO NOT proceed to AF-083 energize. Investigate: swap red/black ferrule positions at the harness end if needed (de-crimp or re-crimp). Re-test all 5 before advancing. If ferrules missing → batch crimp all remaining harness branches (#2 through #6) now and re-run this task.
**Source references:**
- JIRA.md §Safety Rules (1/4) mains 8-point "polarity verified before every energize" + (2/4) "separate polarity verify task from energize task" ordering principle.
- BOM.md H-07 1-to-4 panel power harnesses × 2 = 8 branches.
- AF-081 panel #2 re-verify reference.
**Labels:** hardware polarity-verify safety-review blocked blocked:exp-af-081
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-083: WF4 256×64 row wiring (panel1 OUT → panel2 IN HUB75, panel#2 power branch #2 connect to PSU). Power order: PSU OFF → plug HUB75 both ends → power ON.

**Task ID:** AF-083
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** WF4 EXP-005 physical wiring: 2-panel HUB75 chain (panel1 OUT→panel2 IN), panel#2 on power branch #2. Strict power order, no hot-plug.
**Description:** Wire and energize the 256×64 row for the WF4 candidate path. HUB75 chain: WF4 X1 → panel#1 IN → panel#1 OUT → panel#2 IN. Panel power: panel#1 → branch #1, panel#2 → branch #2 (parallel, NOT daisy-chained). Strict no-hot-plug power order observed per JIRA safety rule.
**Why this task exists:** Executes EXP-005 §Procedure step 1-2 physical layer. Validates the 2-panel WF4 topology end-to-end wiring before firmware config (AF-084). Resolves U-034 (chain wiring correctness).
**Prerequisites:** COMPLETED: AF-082 (panel #2 harness branch #2 polarity pass). COMPLETED: WF4 EXP-004 1-panel chain already exists (M1 subtrack b — carries forward: WF4 X1 ↔ panel#1 wiring).
**Blocked by:** AF-082
**Required hardware:** WF4 controller, Panel #1 (already wired from M1), Panel #2 (on bench), HUB75 cable 0.5 m (1 pc for panel1 OUT → panel2 IN), PSU harness branch #2 labeled BR-2-PANEL2, PSU A-200-5, C14 mains harness with switch.
**Required tools:** Ferrule crimper (HS-202D if ferrules pending), screwdriver for PSU screw terminals, multimeter (standby for quick voltage check after power-on).
**Required software:** None (wiring only).
**Exact execution steps:**
1. PSU switch OFF. Wall plug REMOVED. Confirm full OFF.
2. HUB75 chain step 1: Take 0.5 m HUB75 cable. Plug one end FIRMLY into Panel #1 HUB75 OUT port (label confirmed in M0). Keyed notch engages.
3. HUB75 chain step 2: Plug other end of same cable FIRMLY into Panel #2 HUB75 IN port. Keyed notch engages. Both panels' power ports still UNCONNECTED.
4. Power branch step 1: Confirm Panel #1 power is still connected to PSU harness branch #1 from M1. Ferrule crimped. Screw terminals tight.
5. Power branch step 2: Take PSU harness branch #2 (polarity pass at AF-082). Ferrules on both wires. Route to Panel #2 5 V power block. Red ferrule → Panel #2 V+ screw terminal. Black ferrule → Panel #2 V- screw terminal. Tighten screws to ~0.6 Nm (firm, no wobble; no need to overtighten strip threads).
6. PSU terminal side: Branch #2 ferrule ends (red + black) are screwed into PSU V+ and V- output posts respectively, parallel to branch #1 (same V+ post = parallel distribution per ADR-007). Tighten.
7. Visual inspection all power screws: tight. HUB75 cables: seated and notch-engaged. No loose strands.
8. Multimeter standby (voltage DC 20 V range).
9. Power ON sequence, STRICT ORDER:
   a. C14 wall plug → insert into socket.
   b. C14 switch → ON.
   c. Wait 3 s → PSU fan should start (or silent; listen for click).
   d. Measure PSU V+ output post ↔ V- post with multimeter. Expected: 5.00–5.10 V DC. Record.
   e. IF voltage in range: STOP, do not touch wiring. Task proceeds to next step.
   f. IF voltage out of range (>5.20 or <4.85) → power OFF immediately, diagnose PSU trim pot or harness.
10. Observe Panel #1 and Panel #2: both show boot LEDs or static pattern (whatever WF4 shows at boot). Panel #2 not dark = power connected.
**Expected result:** PSU output = 5.00–5.10 V DC. Both panels #1 and #2 receive power visibly (not dark). HUB75 chain physically seated.
**Acceptance criteria / DoD:**
- PSU voltage at terminals = 5.00–5.10 V DC.
- Panel #1 lights up at boot (same as M1 EXP-004).
- Panel #2 lights up at boot (not dark = power OK).
- HUB75 chain plugged both ends correctly (notch visible seated, not forced).
- Power order followed (no hot-plug; HUB75 connected only while PSU OFF).
**Evidence to save:**
- `docs/photos/wf4-m2/wiring-overview.jpg` (both panels + WF4 + harness visible)
- `docs/photos/wf4-m2/voltage-at-power-on.jpg` (multimeter display = 5.0X V visible)
- `docs/pm/evidence/AF-083-wf4-m2-wiring-notes.md` (voltage reading, timestamp)
**Safety considerations:**
⚠️ No HUB75 hot-plug. Always power OFF controller + panels before connecting/disconnecting HUB75. Power ON order: 5V PSU → controller power → panel data. Power OFF order reverse: panels/controller sign OFF in software or idle → PSU switch OFF → unplug HUB75 only after full OFF. No hot-plug exceptions, not even for a quick test — hot-plug can destroy HUB75 shift-register outputs, HCT245 buffers, or ESP32/WF4 GPIO pins via transient overvoltage on data lines.
⚠️ 5 V high-current rule (JIRA Safety Rules 4/4). NO Dupont connectors or breadboard jumper wires for any continuous ≥2 A (40 W) 5 V path. Use screw terminals, ferrule crimps (18 AWG), or polarized locking Molex-style connectors. NEVER run full load current in series through a multimeter ammeter input — risk of burning out multimeter fuses or input protection and giving false readings from series voltage drop. Use clamp meter (if available) or measure voltage drop across known trace/connector resistance, or measure voltage at both ends of path under load and compute drop < 0.25 V per branch as pass criterion.
**Known uncertainties:**
- U-034 (panel chain data direction): resolved positively if Panel #2 not dark and WF4 config later passes.
- Panel #2 power connector mechanical fit: if ferrules too thick for terminal, trim ferrules to exact length.
**Failure response:**
Panel #2 stays dark → PSU OFF immediately (C14 switch OFF → wall plug OUT). Check: (a) branch #2 screws at both ends (panel and PSU) tight; (b) harness red/black not swapped (go back to AF-082 re-verify). Voltage out of range → C14 switch OFF, inspect PSU V-trim potentiometer (small blue screw on output side) — adjust slightly if available. If no trim pot → PSU may be defective, swap PSU and escalate.
**Source references:**
- EXPERIMENTS.md EXP-005 §Procedure steps 1-2 (chain + parallel power)
- ADR-007 parallel power distribution (6 parallel branches)
- JIRA.md §Safety Rules 2/4 HUB75 no-hot-plug + 4/4 5V high-current no-Dupont
- BOM.md H-07 harnesses × 2 = 8 branches total
**Labels:** hardware controller-wf4 wiring safety-review critical-path candidate-yes blocked blocked:exp-af-082
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-084: WF4 EXP-005 2-panel configuration (Huidu software or firmware): set 256×64 total resolution, scan=1/32, panel order left→right, chain topology 2-chained-horizontal.

**Task ID:** AF-084
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** Configure WF4 controller firmware for 256×64 total 1/32 scan, panel order left→right, 2 chained horizontal on X1 output.
**Description:** Using Huidu vendor software (or WF4 on-board config if accessible), set the WF4 X1 output to logical resolution 256×64, scan rate = 1/32, panel ordering = left-to-right (panel#1 = x=0..127, panel#2 = x=128..255), chain topology = 2-chained-horizontal. Save config and reboot controller. This is EXP-005 §Procedure step 3.
**Why this task exists:** Without explicit config, WF4 drives each panel independently or at wrong resolution. Produces the configuration that EXP-005 §Success Criteria requires. Resolves U-035 (WF4 supports chained 256×64 config?).
**Prerequisites:** COMPLETED: AF-083 (wiring done, power on, both panels not dark). Huidu software installed (from M1 EXP-004 software setup). USB cable or Wi-Fi connection from PC to WF4 working (from EXP-004 carry-forward).
**Blocked by:** AF-083
**Required hardware:** WF4 powered, PC (Windows if Huidu software requires it), USB-WF4 cable OR WF4 Wi-Fi connection.
**Required tools:** None (software configuration).
**Required software:** Huidu vendor control software (HD2020 / HDPlayer or equivalent used in M1 EXP-004).
**Exact execution steps:**
1. Open Huidu control software. Connect to WF4 controller (USB or Wi-Fi; method same as M1 EXP-004).
2. Navigate to Screen / Display config panel.
3. Set Resolution Width = 256, Height = 64.
4. Set Scan type = 1/32 scan (P2 panels standard).
5. Set Panel layout / chain topology: X1 output = "2 panels chained horizontally" or equivalent option. If options are (rows × cols), set rows=1, cols=2, chain-per-row=2.
6. Set Panel ordering: left→right = panel#1 → panel#2. If software shows physical layout diagram, drag panels to correct order: physical panel on left of bench = logical x=0..127 (panel 1), physical panel on right = logical x=128..255 (panel 2).
7. Set Color order = RGB (same as M1 EXP-004 1-panel — should be default, confirm matches).
8. Save configuration.
9. Reboot WF4 controller (software reboot button or power cycle via C14 switch).
10. After reboot, confirm config persists (re-open display config panel, re-read values).
**Expected result:** WF4 controller reboots with 256×64 resolution. Huidu software confirms 256×64 config saved.
**Acceptance criteria / DoD:**
- Display config = 256 × 64 confirmed in software.
- Scan type = 1/32 confirmed.
- Chain topology = 2 horizontal panels confirmed in layout dialog.
- Panel order = left→right matches physical bench layout.
- Config persists after reboot (re-read matches save).
**Evidence to save:**
- `docs/pm/evidence/AF-084-wf4-config-screenshot.png` (software display config dialog with all values visible)
- `docs/pm/evidence/AF-084-wf4-config-after-reboot.png` (screenshot re-opened after reboot to show persistence)
**Safety considerations:**
N/A — software configuration only. WF4 already powered per AF-083 power order. Do NOT disconnect HUB75 or power while saving config.
**Known uncertainties:**
- Huidu software dialog exact wording varies by version; adapt names accordingly (e.g., "screen config", "scan mode", "cascade").
- If WF4 requires reboot for config, document the reboot method.
**Failure response:**
Config fails to save → check USB/Wi-Fi connection; reconnect; retry. If panels behave incorrectly after reboot → revert config to 128×64 and confirm each panel still works individually; re-apply 256×64 and iterate. If 1/32 scan option missing → verify correct P2 panel profile selected in Huidu software panel type dropdown.
**Source references:**
- EXPERIMENTS.md EXP-005 §Procedure step 3 ("Configure logical dimensions as 256 × 64")
- JIRA.md M2 Milestone requirements: "correct left/right ordering"
- AF-043 WF4 EXP-004 config (1-panel baseline)
**Labels:** firmware controller-wf4 configuration critical-path candidate-yes blocked blocked:exp-af-083
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-085: WF4 EXP-005 Standard Test Pattern Suite for 256×64 (solid fills, checkerboard full-width, horizontal gradient from 0→255 across full 256, vertical gradient, 1px vertical lines every 16px across BOTH panels, numbered regions 1..8 each 32px wide, left/right edge boundary labels "PANEL1 LEFT EDGE px0" and "PANEL2 RIGHT EDGE px255"). Run ≥30 min.

**Task ID:** AF-085
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** Run the Standard Test Pattern Suite EXP-005 end-to-end on 256×64 WF4. ≥30 min continuous. No visual artifacts.
**Description:** EXPERIMENTS.md §Shared Test References Standard Test Pattern Suite verbatim, customized for 256×64 width. Execute each pattern in order, observe, photo each. Then continuous 30-min hold with alternating patterns auto-advance to confirm long-run stability.
**Why this task exists:** EXP-005 §Procedure steps 4-5 + §Success Criteria. Produces the WF4 candidate baseline measurement for 256×64 stable refresh. Feeds seam verification (AF-086) and M2 gate-pass aggregator (AF-091).
**Prerequisites:** COMPLETED: AF-084 (WF4 256×64 config saved and rebooted, config persists). Both panels visibly powered.
**Blocked by:** AF-084
**Required hardware:** WF4 + 2-panel chain + PSU (already powered per AF-083). PC connected for pattern sending.
**Required tools:** Camera / smartphone for photos. Timer or stopwatch (30 min).
**Required software:** Huidu control software (pattern library or custom image upload). Pillow on Nano or PC for custom pattern PNG generation if needed.
**Exact execution steps:**
1. In Huidu software, prepare and send each pattern in order. After each send, wait 5 s for render settle, take 1 photo.
2. Pattern 1 — Solid fills (5 patterns, 5 photos): solid red, solid green, solid blue, solid white, solid black. (RGB channel isolation + full/zero load.)
3. Pattern 2 — Checkerboard full-width 256×64, 8×8 checker tiles. (Pixel-level addressing across both panels.)
4. Pattern 3 — Horizontal gradient: x=0 → R=G=B=0 (black), x=255 → R=G=B=255 (white). Linear ramp across 256 px. Verify gradient on both panels.
5. Pattern 4 — Vertical gradient: y=0 → black, y=63 → white. (Vertical row mapping.)
6. Pattern 5 — 1 px vertical white lines every 16 px, across full 256 width (16 lines total at x=0,16,32,240). Verify lines appear on BOTH panels at correct positions.
7. Pattern 6 — Numbered regions 1..8, each 32 px wide × 64 px tall. Region N = number N rendered as big bold text centered in its 32-px column. Verify region 1-4 appear on panel #1, region 5-8 appear on panel #2.
8. Pattern 7 — Edge boundary labels: white text "PANEL1 LEFT EDGE px0" at x=0-80 left-justified y=25-40; white text "PANEL2 RIGHT EDGE px255" at x=176-255 right-justified y=25-40. Verify each label appears on its correct physical panel edge.
9. Sustained stability run: set up Huidu software or auto-script to cycle between patterns every 30 s. Start timer. Leave system unattended for 30 minutes.
10. At 30 min mark: return. Observe for any blank/freeze/garble/flicker. Take final state photo. Record timestamp.
**Expected result:** All 8 patterns render correctly across both panels. No duplicated half, no reversed panel order, no shifted rows. 30-min hold: no blank, no freeze, no garble, no controller reset.
**Acceptance criteria / DoD:**
- Solid fills: all 5 (R,G,B,W,black) render uniformly across BOTH panels (one panel not dimmer than the other).
- Checkerboard: correct on both panels; no seam banding or double-px at x=127/128.
- Horizontal gradient: visible smooth gradient from left edge to right edge across entire 256.
- Vertical lines every 16 px: 16 lines visible; line at x=112 on panel #1, line at x=128 on panel #2 = visible and correctly spaced.
- Numbered regions 1-4 on physical panel #1, 5-8 on physical panel #2 (correct order).
- Edge labels "PANEL1 LEFT EDGE px0" visible on leftmost physical panel, "PANEL2 RIGHT EDGE px255" on rightmost.
- 30-min stability: no blank, no freeze, no garble. Timestamp start + end documented.
**Evidence to save:**
- `docs/photos/wf4-m2-patterns/solid-red.jpg` through `solid-black.jpg` (5 photos)
- `docs/photos/wf4-m2-patterns/checkerboard.jpg`
- `docs/photos/wf4-m2-patterns/horizontal-gradient.jpg`
- `docs/photos/wf4-m2-patterns/vertical-gradient.jpg`
- `docs/photos/wf4-m2-patterns/vertical-lines-16px.jpg`
- `docs/photos/wf4-m2-patterns/numbered-regions-1-8.jpg`
- `docs/photos/wf4-m2-patterns/edge-labels.jpg`
- `docs/photos/wf4-m2-patterns/stability-30min-end.jpg`
- `docs/pm/evidence/AF-085-wf4-exp005-suite-notes.md` (per-pattern pass/fail, start/end timestamps, any artifacts observed)
**Safety considerations:**
⚠️ No HUB75 hot-plug. Always power OFF controller + panels before connecting/disconnecting HUB75. Power ON order: 5V PSU → controller power → panel data. Power OFF order reverse: panels/controller sign OFF in software or idle → PSU switch OFF → unplug HUB75 only after full OFF. No hot-plug exceptions, not even for a quick test — hot-plug can destroy HUB75 shift-register outputs, HCT245 buffers, or ESP32/WF4 GPIO pins via transient overvoltage on data lines.
NOTE: During 30-min hold, do NOT leave flammable items near PSU or panels. Monitor periodically or at least at 10-min intervals for thermal anomalies.
**Known uncertainties:**
- Seam-crossing artifacts: if observed, dedicated verification at AF-086.
- 30-min thermal: WF4 or panel temp; no explicit ceiling but hand-test at end.
**Failure response:**
Any pattern fails → document photo + exact description. IF panel order reversed → go back to AF-084 config and swap panel order (drag panels in layout dialog). IF duplicated half → check chain length config = 2 panels. IF garbled rows → reseat HUB75 cables with POWER OFF, re-test. 30-min hold fails (blank/freeze) → reboot WF4 and reduce to 10-min test to capture first-failure time.
**Source references:**
- EXPERIMENTS.md §Shared Test References → Standard Test Pattern Suite (6 patterns verbatim)
- EXPERIMENTS.md §Standard Defect Checklist (6 defects to observe)
- EXPERIMENTS.md EXP-005 §Procedure steps 4-5 + §Success Criteria
**Labels:** validation controller-wf4 critical-path candidate-yes blocked blocked:exp-af-084
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-086: WF4 EXP-005 seam-crossing verification: (a) horizontal gradient smooth across seam (no banding at px127→px128); (b) vertical seam text "SEAM CROSSING TEST" centered at x=120-136 with letters straddling both panels; (c) 1px vertical line at x=128 continuous across both panel outputs (no gap, no double-line). Evidence: close-up photo of seam region for each check.

**Task ID:** AF-086
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** WF4 256×64 dedicated seam-crossing verification (3 checks) at the panel#1 ↔ panel#2 physical seam. Close-up photos each check.
**Description:** EXP-005 §Success Criteria dedicated verification. Physical seam location = between right edge of panel #1 (px127) and left edge of panel #2 (px128). Render 3 specific test contents and verify content crosses the seam cleanly. Each check gets a close-up photo.
**Why this task exists:** M2 Milestone JIRA requirement: "arbitrary text crossing the physical seam" + "correct left/right ordering" dedicated proof. Passing this task = WF4 candidate meets M2's core visual requirement. Feeds directly into M2 GATE-PASS (AF-091 DoD items b, c, d).
**Prerequisites:** COMPLETED: AF-085 (test suite baseline renders). WF4 config = 256×64 active.
**Blocked by:** AF-085
**Required hardware:** WF4 2-panel setup (powered and rendering per AF-085).
**Required tools:** Camera with close-up / macro mode (or smartphone) for seam region shots. Ruler or reference card for scale (optional).
**Required software:** Pattern generator (Huidu custom image upload or Pillow PNG generator for the 3 specific seam patterns).
**Exact execution steps:**
1. **Check (a) — Horizontal gradient across seam:** Re-send Pattern 3 horizontal gradient (x=0→0, x=255→255 grayscale). Position camera 10–15 cm from the physical seam region (where panel #1 right edge meets panel #2 left edge). Ensure px127 (panel1 last) and px128 (panel2 first) both visible in frame. Take close-up photo. Observe: any banding, brightness step, or color jump at the seam? Record Y/N.
2. **Check (b) — Seam text straddling:** Generate and send a 256×64 image with bold white 20-px-tall text "SEAM CROSSING TEST" on black background. Text is horizontally CENTERED at x=128, so letters straddle px127 and px128 boundary. Expected: "SEAM" left of center, "CROSSING" partially across, "TEST" right of center. Observe: each letter is intact across the seam, not split with a gap or doubled. Take close-up photo of seam region with text.
3. **Check (c) — 1 px vertical line at x=128:** Send pattern with exactly 1 vertical white pixel column at x=128 (panel #2 leftmost column), full height y=0..63, on black background. (Alternative if x=128 edge too hard to see: also send x=127 line as cross-check on panel #1 rightmost.) Observe: line is SINGLE continuous line (no gap between panel physical edges, no double-width line). Take close-up photo of seam region.
**Expected result:** (a) Gradient smooth at seam — no visible step/banding. (b) Text "SEAM CROSSING TEST" straddles seam cleanly, no split letters. (c) 1 px vertical line at x=128 is single and continuous.
**Acceptance criteria / DoD:**
- (a) Gradient: no banding at px127→128 (pass/fail based on photo).
- (b) Text: each letter of "SEAM CROSSING TEST" visible on correct side of seam, letters crossing seam are NOT split by physical gap (photo proof).
- (c) 1 px line at x=128: single continuous line, no gap, no double-line (photo proof).
- 3 close-up seam photos submitted, one per check (a,b,c).
**Evidence to save:**
- `docs/photos/wf4-m2-seam/seam-a-gradient-closeup.jpg`
- `docs/photos/wf4-m2-seam/seam-b-text-straddle-closeup.jpg`
- `docs/photos/wf4-m2-seam/seam-c-1px-line-closeup.jpg`
- `docs/pm/evidence/AF-086-wf4-seam-notes.md` (per-check pass/fail, observations)
**Safety considerations:**
No safety concerns during observation. Do not touch HUB75 or power while energized.
**Known uncertainties:**
- Physical panel-to-panel gap: if the bench gap is large due to poor alignment, (c) may show visual gap but content mapping can still be correct. The DoD is about content mapping, not mechanical gap. Mechanical gap is resolved in the frame/mounting phase.
**Failure response:**
Any check fails → debug WF4 config: (a) banding = check panel order, check chain length=2; (b) text split or off-center = check text x-position in generator; (c) double-line or gap = check that 1 px line really is one column (not 2 pixels wide due to rendering bug), check HUB75 cable seating at panel #1 OUT port (reseat with POWER OFF). Re-photo after fix.
**Source references:**
- EXPERIMENTS.md EXP-005 §Success Criteria: "Correct 256×64 mapping; no duplicated half or panel-order reversal; stable refresh with no controller-caused seam"
- JIRA.md M2 Milestone requirements: "arbitrary text crossing the physical seam" + "correct left/right ordering"
- Standard Test Pattern Suite custom seam variants (applied per-project seam locations)
**Labels:** validation controller-wf4 critical-path candidate-yes blocked blocked:exp-af-085
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-087: ESP32 256×64 row wiring EXP-012 prep (panel1→panel2 chain, panel#2 on power branch #2, ESP32 HCT adapter X HUB75 out → panel1 IN). Same no-hot-plug power order.

**Task ID:** AF-087
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** ESP32 EXP-012 256×64 row wiring: ESP32 HCT adapter → panel1 IN → panel1 OUT → panel2 IN. Power: panel#2 on branch #2 (branch #1 for panel#1). No hot-plug.
**Description:** Wire the ESP32 candidate 2-panel chain per EXP-012 §Procedure steps 1-2 physical layer. ESP32 HCT245 perfboard adapter (from M1 Subtrack c) connects to panel #1 IN. Panel #1 OUT chains to panel #2 IN via 0.5 m HUB75. Both panels receive parallel power: panel #1 on harness branch #1, panel #2 on harness branch #2. Strict power order observed.
**Why this task exists:** EXP-012 §Procedure steps 1-2 physical implementation. Produces the ESP32-candidate 2-panel chain baseline for firmware config (AF-088) and test patterns (AF-089, AF-090). Dual-track parallel to WF4 AF-083. Resolves U-037 (ESP32 2-panel chain feasibility).
**Prerequisites:** COMPLETED: M1 Subtrack c: AF-048 through AF-071 = ESP32 HCT245 perfboard adapter BUILT and 1-panel EXP-011 PASS. COMPLETED: AF-082 (panel #2 branch #2 polarity pass). PSU OFF.
**Blocked by:** AF-082 (power polarity prereq) + (AF-071 or whichever M1 EXP-011 terminal task = ESP32 1-panel pass)
**Required hardware:** ESP32-S3 + HCT245 perfboard adapter (M1-built), Panel #1 and #2 on bench, HUB75 cable 0.5 m for chain panel1→panel2, HUB75 cable from HCT245 HUB75 E header → panel1 IN, PSU harness branches #1 and #2, PSU A-200-5, C14 mains.
**Required tools:** Screwdriver for PSU terminals, multimeter (standby voltage check).
**Required software:** None (wiring only). Firmware loaded in next task AF-088.
**Exact execution steps:**
1. PSU switch OFF. Wall plug OUT. Full OFF confirmed.
2. HUB75 chain: HCT245 perfboard HUB75 2×8 header → HUB75 cable → Panel #1 IN port. Notch keyed. Firmly seated.
3. Panel #1 OUT port → HUB75 0.5 m cable → Panel #2 IN port. Notch engaged both ends.
4. Panel power #1: harness branch #1 → Panel #1 V+/V- block (re-use from M1 ESP32 1-panel setup if WF4 setup was swapped in). Ferrule crimped. Screws tight.
5. Panel power #2: harness branch #2 (AF-082 polarity pass) → Panel #2 V+/V- block. Red ferrule → V+. Black ferrule → V-. Screws tight.
6. PSU terminal side: branches #1 and #2 both V+ → PSU V+ post (parallel shared). Both V- → PSU V- post. Screws tight.
7. ESP32 + HCT board power: 5 V from PSU V+/V- posts to HCT 5 V rail and ESP32 5 V or USB power. Ground common with panel ground. (Same power method as M1 EXP-011.)
8. Visual: all screws tight. No loose wires. No stray strands.
9. Power ON STRICT ORDER:
   a. Wall plug IN.
   b. C14 switch ON.
   c. Wait 3 s. Multimeter check PSU output = 5.00–5.10 V DC. Record.
   d. ESP32 boot: USB serial or on-board LED indicates boot.
10. Panels #1 and #2 both not dark = power OK.
**Expected result:** 5.00–5.10 V at PSU. Both panels receive power (not dark). ESP32 boots. HUB75 chain seated 3 connections (HCT→p1 IN, p1 OUT→p2 IN).
**Acceptance criteria / DoD:**
- PSU output voltage 5.00–5.10 V DC.
- Panel #1 and Panel #2 both powered (visible LEDs at boot — color irrelevant; not dark = power OK).
- ESP32 boots (LED blink or USB serial enumerates).
- 3 HUB75 connectors seated notch-engaged.
- Power order observed (no hot-plug).
**Evidence to save:**
- `docs/photos/esp32-m2/wiring-overview.jpg`
- `docs/photos/esp32-m2/psu-voltage-on.jpg`
- `docs/pm/evidence/AF-087-esp32-m2-wiring-notes.md`
**Safety considerations:**
⚠️ No HUB75 hot-plug. Always power OFF controller + panels before connecting/disconnecting HUB75. Power ON order: 5V PSU → controller power → panel data. Power OFF order reverse: panels/controller sign OFF in software or idle → PSU switch OFF → unplug HUB75 only after full OFF. No hot-plug exceptions, not even for a quick test — hot-plug can destroy HUB75 shift-register outputs, HCT245 buffers, or ESP32/WF4 GPIO pins via transient overvoltage on data lines.
⚠️ 5 V high-current rule (JIRA Safety Rules 4/4). NO Dupont connectors or breadboard jumper wires for any continuous ≥2 A (40 W) 5 V path. Use screw terminals, ferrule crimps (18 AWG), or polarized locking Molex-style connectors. NEVER run full load current in series through a multimeter ammeter input — risk of burning out multimeter fuses or input protection and giving false readings from series voltage drop. Use clamp meter (if available) or measure voltage drop across known trace/connector resistance, or measure voltage at both ends of path under load and compute drop < 0.25 V per branch as pass criterion.
**Known uncertainties:**
- U-037 (ESP32 DMA/PSRAM at 256×64): test at runtime (AF-089).
- ESP32 5 V power feeding: if powered via USB only, panel current does not pass through ESP32 (correct). If powered via Vin pin, confirm 5 V tolerance.
**Failure response:**
Panel #2 dark → PSU OFF immediately. Check harness branch #2 screws, polarity. Voltage out of range → trim or PSU swap. ESP32 no boot → USB-serial check: boot mode = flash download? (If yes, check BOOT button not stuck; press RESET.)
**Source references:**
- EXPERIMENTS.md EXP-012 §Procedure steps 1-2 (chain second panel, parallel power)
- JIRA.md §Safety Rules 2/4 + 4/4
- M1 Subtrack c: AF-054..AF-065 HCT245 build + AF-066..AF-071 EXP-011 validation
**Labels:** hardware controller-esp32 wiring safety-review critical-path candidate-yes blocked blocked:exp-af-082
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-088: ESP32 EXP-012 2-panel firmware config (256×64 chain, panel ordering, brightness/color-depth/buffer config; record refresh rate via serial or lib output).

**Task ID:** AF-088
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** Flash/configure ESP32 firmware for 256×64 2-panel chain. Document GPIO mapping, panel chain length, color depth, buffer config. Record refresh rate.
**Description:** Re-use the M1 EXP-011 firmware/library (ESP32-HUB75-MatrixPanel-DMA or WLED-MM) and expand config: chain=2 panels horizontal, total width=256, height=64, scan=1/32. GPIO mapping same as M1 EXP-011 (validated there). Serial out enabled → log refresh rate and PSRAM/DMA usage. Save config values.
**Why this task exists:** EXP-012 §Procedure step 3 verbatim. Establishes baseline refresh rate and PSRAM usage for 256×64 — critical measurements for EXP-014 scoring matrix (AF-093).
**Prerequisites:** COMPLETED: AF-087 (wiring + power up OK). M1 EXP-011 firmware source and flashing tools available.
**Blocked by:** AF-087
**Required hardware:** ESP32 HCT setup powered (per AF-087). USB-C data cable → PC serial monitor.
**Required tools:** Serial monitor (screen / minicom / Arduino IDE Serial Monitor).
**Required software:** Arduino IDE or PlatformIO; ESP32-HUB75-MatrixPanel-DMA or WLED/WLED-MM library (same lib used in M1 EXP-011). Python esptool for flashing.
**Exact execution steps:**
1. Open M1 EXP-011 firmware sketch in Arduino IDE / PlatformIO.
2. Locate config section. Change:
   a. MATRIX_WIDTH or equivalent = 256
   b. MATRIX_HEIGHT = 64
   c. CHAIN_LENGTH or PANELS_PER_ROW = 2
   d. SCAN = 1/32 or kPanelScan_32Scan or as per library enum
   e. Keep GPIO pins SAME as EXP-011 (already validated)
   f. Brightness = 64 (mid-range for testing; we'll sweep later)
   g. Color depth = 8-bit per channel (or library default)
   h. Buffer / PSRAM: enable double-buffering if available; PSRAM framebuffer if supported
3. Compile. Address any compile errors.
4. Flash firmware to ESP32: USB-C connected; put into download mode if needed (BOOT+RESET or automatic via esptool).
5. Open serial monitor at 115200 baud. Press ESP32 RESET.
6. Observe boot log: confirm width=256, height=64, chain=2. Any DMA/PSRAM allocation messages.
7. After boot, record reported REFRESH RATE (if library prints it — if not, modify sketch to print every 5 s).
8. Record PSRAM usage % or bytes free.
9. Save firmware config section as text in evidence file (copy-paste all #define lines).
10. Confirm both panels show default boot pattern (not dark, not garbage).
**Expected result:** Compile + flash succeeds. Boot log: 256×64 chain=2 confirmed. Refresh rate: some positive number (typical 60–200 Hz depending on library/config). No DMA allocation errors.
**Acceptance criteria / DoD:**
- Config: width=256, height=64, chain=2, scan=1/32. Saved to evidence.
- Compile + flash = success.
- Boot serial log confirms 256×64 config.
- Refresh rate recorded (numerical value, Hz).
- PSRAM / memory status recorded.
- Both panels not dark after flash.
**Evidence to save:**
- `docs/pm/evidence/AF-088-esp32-exp012-config.h` (copy of all config #define lines)
- `docs/pm/evidence/AF-088-esp32-exp012-serial-boot.log` (serial capture, refresh rate line highlighted)
- `docs/pm/evidence/AF-088-esp32-exp012-notes.md` (refresh rate = X Hz, PSRAM usage notes)
**Safety considerations:**
N/A — firmware configuration only. ESP32 already powered per correct order in AF-087.
**Known uncertainties:**
- Refresh rate range: expected 60+ Hz. If < 40 Hz, visible flicker → escalate as DoD concern for EXP-014 scoring.
- PSRAM availability: N16R8 has 8 MB octal PSRAM; if library does not use it, framebuffer stays in internal SRAM = may limit color depth.
**Failure response:**
Compile error → check library version; if library requires chain=1 maximum → research multi-panel API of that library; switch libraries if needed (WLED-MM explicitly supports chains). DMA allocation fail → reduce color depth or disable double-buffering and re-test. Refresh rate < 30 Hz → document and proceed for comparison purposes (may fail EXP-014 scoring).
**Source references:**
- EXPERIMENTS.md EXP-012 §Procedure steps 2-3 ("Configure logical dimensions as 256×64; test brightness, color-depth, buffer configs; measure reported refresh rate")
- M1 Subtrack c: AF-066 (firmware flash), AF-069 (refresh rate measurement for 1-panel)
**Labels:** firmware controller-esp32 configuration critical-path candidate-yes blocked blocked:exp-af-087
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-089: ESP32 EXP-012 Standard Test Pattern Suite + runtime metrics: static + scrolling text + full-screen color change every 5s. Record refresh rate, PSRAM if used, no visual artifacts. Run ≥1 hour.

**Task ID:** AF-089
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** ESP32 EXP-012 full Standard Test Pattern Suite + 1-hr stability. Metrics: refresh rate, PSRAM, stability.
**Description:** EXPERIMENTS.md Standard Test Pattern Suite on ESP32 256×64. Plus EXP-012 §Procedure scrolling content and changing full-screen colors. Plus 1-hr continuous runtime. Record all metrics from serial log. Observe Standard Defect Checklist items.
**Why this task exists:** EXP-012 §Success Criteria + §Procedure steps 2-3. Produces ESP32 candidate 256×64 evidence for EXP-014 scoring matrix. Baseline measurements for V1 text reliability, refresh quality, and stability.
**Prerequisites:** COMPLETED: AF-088 (firmware config, refresh rate measured, panels not dark).
**Blocked by:** AF-088
**Required hardware:** ESP32 2-panel setup running. USB serial connection for logging.
**Required tools:** Camera for photos. Timer (1 hr).
**Required software:** Modified firmware sketch implementing the test pattern sequence, scroll, color change, and serial metric printout. Serial log capture to file.
**Exact execution steps:**
1. Flash test-pattern firmware (or load pattern via Nano transport if EXP-013 already supports it; fall-back: hard-coded sequence in ESP32 sketch for now).
2. Pattern sequence (photo each, same WF4 suite for comparability):
   a. Solid R, G, B, white, black fills (5 photos).
   b. Checkerboard full-width.
   c. Horizontal gradient 0→255.
   d. Vertical gradient.
   e. 1 px vertical lines every 16 px.
   f. Numbered regions 1..8 (32 px wide).
   g. Edge boundary labels: "PANEL1 LEFT EDGE px0", "PANEL2 RIGHT EDGE px255".
3. Dynamic content test 1: Scrolling white text "ESP32 EXP-012 256×64 STABILITY TEST" left-to-right across full width. 3 passes. Observe: scroll smooth? No garbled rows? Photo one frame.
4. Dynamic content test 2: Full-screen color change every 5 seconds between R→G→B→W→R cycle. 10 cycles (50 s). Observe: no flicker on change, no reset.
5. Stability run 1 hr: let firmware run scrolling + color cycle continuously. Serial log on, capture to file (every 30 s: refresh rate, PSRAM bytes free, uptime seconds).
6. At 1 hr mark: stop timer. Observe: no blank, no freeze, no garble, no controller reset. Take final photo.
7. Aggregate metrics: avg refresh rate, min refresh rate, max refresh rate, PSRAM trend (stable? growing leak?).
**Expected result:** All patterns correct. Dynamic content smooth. 1-hr stable hold (no blank/freeze/reset). Refresh rate stable within ±10%. PSRAM free stable (no leak).
**Acceptance criteria / DoD:**
- Standard suite patterns 3-7 (above) render correctly across both panels (compare to WF4 AF-085 subjectively).
- Scroll test: 3 passes, no garbled rows, scroll direction correct.
- Color cycle 10×: no controller reset, no garbled output after color-change.
- ≥1 hour (3600 s) uptime: serial log shows continuous time from t=0 to t≥3600.
- No blank, no freeze, no garble, no watchdog reset during hour.
- Refresh rate: avg value recorded; if avg < 30 Hz → flag but continue (will affect EXP-014 scoring).
- PSRAM: stable (final free value within 10% of initial).
**Evidence to save:**
- `docs/photos/esp32-m2-patterns/` — same 7 pattern filenames as WF4 AF-085
- `docs/photos/esp32-m2-patterns/scrolling-text.jpg`
- `docs/photos/esp32-m2-patterns/stability-1hr-end.jpg`
- `docs/pm/evidence/AF-089-esp32-exp012-serial.log` (full 1-hr capture)
- `docs/pm/evidence/AF-089-esp32-exp012-notes.md` (per-pattern pass/fail, avg/min/max refresh Hz, PSRAM trend, stability timestamp start/end)
**Safety considerations:**
⚠️ No HUB75 hot-plug (during 1-hr run: panels are energized, do NOT reach behind and unplug).
⚠️ PSU thermal: at 30-min and 60-min marks, do hand-touch test on PSU chassis (<60C = OK, no burn pain).
**Known uncertainties:**
- Thermal: if WF4 was tested and run, ESP32 may be different temperature; monitor.
- Watchdog: if ESP32 crashes with wdt reset, investigate memory leak or DMA overflow.
**Failure response:**
Crash / blank → read serial crash dump (Guru Meditation Error or WDT). Reduce color depth or disable double-buffer. Re-run. If patterns wrong (panel order reversed) → config chain order or swap HUB75 cable direction. Refresh rate too low → documented, passed on to scoring matrix as-is.
**Source references:**
- EXPERIMENTS.md §Standard Test Pattern Suite + §Standard Defect Checklist
- EXPERIMENTS.md EXP-012 §Procedure steps 2-3 + §Success Criteria
- JIRA.md M2 Milestone: "sustained stability"
**Labels:** validation controller-esp32 critical-path candidate-yes blocked blocked:exp-af-088
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-090: ESP32 EXP-012 seam-crossing verification (same 3 checks as WF4 AF-086). Close-up seam photos.

**Task ID:** AF-090
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** ESP32 256×64 seam verification: gradient, seam text straddle, 1 px line at x=128. 3 close-up photos.
**Description:** Same seam-crossing verification as AF-086 but on ESP32 candidate path. (a) horizontal gradient smooth; (b) "SEAM CROSSING TEST" text straddling; (c) 1 px vertical line at x=128 continuous. Each gets a close-up photo.
**Why this task exists:** Direct comparability with WF4 AF-086 result in EXP-014 matrix. M2 GATE-PASS (AF-091) DoD items (b,c,d) for the ESP32 path.
**Prerequisites:** COMPLETED: AF-089 (ESP32 patterns rendering).
**Blocked by:** AF-089
**Required hardware:** ESP32 2-panel chain powered.
**Required tools:** Camera with close-up / macro capability.
**Required software:** Test pattern generator for 3 seam patterns (same image assets as AF-086, or ESP32 firmware modes).
**Exact execution steps:**
1. Check (a): Send horizontal gradient 0→255 across 256×64. Close-up photo at seam. Observe: banding? Y/N.
2. Check (b): Send "SEAM CROSSING TEST" centered at x=120-136, letters straddle px127/128 seam. Photo close-up at seam. Letters intact? Y/N.
3. Check (c): Send 1 px vertical white line at x=128, y=0..63. Photo close-up. Line single continuous? Y/N.
**Expected result:** Same as WF4: (a) no banding, (b) text straddles clean, (c) line continuous.
**Acceptance criteria / DoD:**
- (a) Gradient: no banding at seam.
- (b) Text: straddles seam, letters not split, not gapped by physical seam beyond mapping.
- (c) 1 px line: single, continuous, no gap, no double-width.
- 3 close-up photos.
**Evidence to save:**
- `docs/photos/esp32-m2-seam/seam-a-gradient-closeup.jpg`
- `docs/photos/esp32-m2-seam/seam-b-text-straddle-closeup.jpg`
- `docs/photos/esp32-m2-seam/seam-c-1px-line-closeup.jpg`
- `docs/pm/evidence/AF-090-esp32-seam-notes.md`
**Safety considerations:**
Energized observation only. Do NOT touch wiring.
**Known uncertainties:**
ESP32 library chain offset handling: some libraries add a per-panel column gap (known bug). If observed, check library issue tracker for known fix or workaround patch.
**Failure response:**
Same as AF-086: check panel order, chain length config, HUB75 reseat (POWER OFF first), re-send patterns. If library-specific chain bug → document workaround and apply.
**Source references:**
- AF-086 WF4 procedure (same 3 checks for direct comparison)
- EXPERIMENTS.md EXP-012 §Success Criteria
**Labels:** validation controller-esp32 critical-path candidate-yes blocked blocked:exp-af-089
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-091: M2 GATE-PASS 256×64 aggregator. DoD: (a) 256×64 canvas WORKS on at least one path (WF4 AF-086 OR ESP32 AF-090); (b) horizontal gradient smooth across seam; (c) vertical seam text straddles correctly; (d) 1px vertical line at x=128 continuous across both panels; (e) ≥30 min stable hold (WF4) or ≥1 hr stable hold (ESP32). Document which path passed.

**Task ID:** AF-091
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** M2 2-panel GATE-PASS aggregator: confirm at least one controller path passes all 5 DoD items. Document which path(s).
**Description:** Review WF4 AF-086 and ESP32 AF-090 evidence side-by-side. Confirm at least one path satisfies the 5 DoD bullets. M2 passes IF either (not necessarily both) controller passes. If BOTH pass → mark both as candidate for ADR-016 comparison. Document explicitly which controller(s) passed + evidence checklist hash list.
**Why this task exists:** M2 is a 🔒 GATED MILESTONE per JIRA.md Milestone 2 definition. You CANNOT proceed to Architecture Decision Gate (Epic AF-004) or M3 (Epic AF-005) until this aggregator's DoD is signed off. This task is the M2 exit gate.
**Prerequisites:** COMPLETED: AF-086 (WF4 seam) OR AF-090 (ESP32 seam), each with their own stability prereqs met.
**Blocked by:** AF-086 OR AF-090
**Required hardware:** None (doc review).
**Required tools:** None.
**Required software:** Evidence folder open.
**Exact execution steps:**
1. Open WF4 AF-085 evidence + AF-086 evidence. Tick:
   (a) 256×64 canvas works? (patterns rendered?)
   (b) Gradient seam smooth?
   (c) Seam text straddle?
   (d) 1 px line at x=128 continuous?
   (e) ≥30 min stable?
2. Open ESP32 AF-089 evidence + AF-090 evidence. Tick same (a)-(d) and (e) = ≥1 hr stable?
3. Result: WF4 pass Y/N, ESP32 pass Y/N.
4. IF at least one Y → M2 GATE = PASS. Write down which path(s).
5. IF both Y → both advance to ADR-016 scoring (ideal case).
6. IF neither Y → M2 GATE = FAIL (see Failure response).
7. Commit all M2 evidence (WF4 + ESP32 photos + notes) in a single docs commit.
**Expected result:** At least one controller path ticks all 5. Both paths if all experiments succeeded.
**Acceptance criteria / DoD:**
- (a) 256×64 canvas WORKS on at least one path (WF4 OR ESP32).
- (b) Horizontal gradient smooth across seam on the passing path.
- (c) Vertical seam text "SEAM CROSSING TEST" straddles correctly on the passing path.
- (d) 1 px vertical line at x=128 continuous across both panels on the passing path.
- (e) WF4: ≥30 min stable hold documented OR ESP32: ≥1 hr stable hold documented (match the path's runtime rule).
- Passing path name explicitly documented.
**Evidence to save:**
- `docs/pm/evidence/AF-091-m2-gate-pass.md` (WF4 5-tick checklist, ESP32 5-tick checklist, PASS statement with passing path(s) named, evidence file hash list or commit hash)
- Git commit hash of the M2 evidence bulk commit.
**Safety considerations:**
N/A — doc review gate.
**Known uncertainties:**
None new. Resolves the M2 stage uncertainties entirely for at least one path.
**Failure response:**
M2 GATE FAIL (neither path ticks all 5) → triage: diagnose which DoD item failed on each path. Go back to the source task and fix. E.g., seam banding → fix WF4 chain order config; 1 hr ESP32 crash → lower color depth and re-run stability. Re-run this gate after fixes. DO NOT advance to ADR-016 or M3 without gate pass.
**Source references:**
- JIRA.md Milestone 2 DoD bullet points (parallel power, HUB75 chaining, left/right order, arbitrary text crossing seam, Nano updates end-to-end, sustained stability)
- Epic AF-003 Exit criteria header in backlog §1
- EXP-005 and EXP-012 success criteria (EXPERIMENTS.md)
**Labels:** validation decision gate-pass blocked blocked:exp-af-086 blocked:exp-af-090
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-092: M2 subtrack evidence commit + subtrack pass log

**Task ID:** AF-092
**Epic:** AF-003 (M2: Two-Panel 256×64 Logical Canvas — 🔒 EXPLICIT GATED MILESTONE)
**Summary:** M2 subtrack aggregator commit: commit all WF4 (AF-083..AF-086) and ESP32 (AF-087..AF-090) evidence. Log subtrack status.
**Description:** Analogous to AF-047 (WF4 subtrack) but for M2's dual subtracks. Commit all evidence files generated in AF-082..AF-091 in a single docs commit with explicit message. Update docs evidence index if maintained. Log each subtrack (WF4 M2, ESP32 M2) as candidate-pass or fail with short note.
**Why this task exists:** Discipline: close out each subtrack before moving on. Ensures all evidence is committed before ADR-016 scoring (AF-093) requires looking at raw measurement data — no evidence lost.
**Prerequisites:** COMPLETED: AF-091 (gate pass).
**Blocked by:** AF-091
**Required hardware:** None.
**Required tools:** Git.
**Required software:** None.
**Exact execution steps:**
1. List all evidence files from AF-082 through AF-091 (photos, notes, serial logs, config headers).
2. Confirm each file physically exists in its evidence path (cross-check against each task's Evidence to save list).
3. `git add` all new/changed evidence files.
4. `git status` review: no unexpected files staged; no evidence missing.
5. Commit with message: `docs(m2): M2 subtrack evidence commit — WF4 AF-083..AF-086 + ESP32 AF-087..AF-090 + gate AF-091`.
6. Write subtrack log: 2 rows (WF4, ESP32), each row = status (candidate-pass / fail) + short note (why).
**Expected result:** 1 commit hash. Subtrack log 2 rows.
**Acceptance criteria / DoD:**
- All AF-082..AF-091 evidence files committed.
- 1 commit hash generated.
- Subtrack log WF4 row + ESP32 row each with status.
**Evidence to save:**
- Commit hash.
- `docs/pm/evidence/AF-092-m2-subtrack-log.md` (2 subtrack rows).
**Safety considerations:**
N/A.
**Known uncertainties:**
None.
**Failure response:**
Missing evidence files → go back to the task and produce / re-commit. Do not proceed without all evidence.
**Source references:**
- AF-047 (WF4 subtrack commit precedent)
- AF-024 (M0 summary commit precedent)
**Labels:** docs validation blocked blocked:exp-af-091
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-093: EXP-014 Controller comparison scoring matrix COMPLETION. Fill EVERY cell of 13-criteria × 2-candidate table (table copied from EXPERIMENTS.md EXP-014 §Scoring Matrix schema verbatim). 13 criteria rows from EXPERIMENTS: V1 text reliability, 6-panel feasibility, update latency/fps, ease of 6-panel wiring, open-protocol degree, power consumption, thermal, cost delta vs BOM baseline, available docs/community support, controller availability (risk of stock-out), transport protocol obsolescence risk, bus/cable count, code maintainability (Python glue vs embedded C++). Use RAW MEASUREMENT DATA from prior tasks, NOT gut feel. Score 0-5 per cell with explicit evidence cite (AF-### + measurement). Add weighting column from ADR-016's decision rule.

**Task ID:** AF-093
**Epic:** AF-004 (MG: Architecture Decision Gate — 🔒 GATED MILESTONE)
**Summary:** Complete EXP-014 Scoring Matrix. 13 criteria × 2 candidates (WF4 vs multi-ESP32). Every cell: score 0-5 + explicit AF-### evidence cite. Add ADR-016 weighting.
**Description:** Architecture Decision Gate input #1. Copy the EXP-014 §Scoring Matrix empty schema from EXPERIMENTS.md into a filled table. For EACH cell (13×2=26 cells): score 0 = poor / 5 = excellent. Each score MUST include a parenthetical evidence cite = (AF-###, measurement-value). E.g.: WF4 cell = "4 (AF-086: seam gradient smooth, 30 min stable)". ESP32 cell = "4 (AF-090: seam clean, 1 hr stable @ avg 85 Hz)". Add a 3rd column = Weight, derived from ADR-016 decision rule: "simplest architecture that reliably satisfies V1 proof of concept at acceptable cost and without introducing novel manufacturing risk" — weights per criterion reflect this rule (higher weight for reliability, manufacturing risk, cost; lower for openness/coolness).
**Why this task exists:** ADR-016 draft (next task AF-094) needs quantitative evidence-backed comparison, NOT gut feel. EXP-014 §Decision Rule verbatim says "simplest architecture that satisfies V1 requirements". The 26 cells + weighting = the DECISION INPUT for the entire architecture.
**Prerequisites:** COMPLETED: AF-092 (M2 subtrack evidence committed — all raw measurement data available). COMPLETED: Nano transport experiments (AF-045 WF4 EXP-007 Phase C, AF-068 ESP32 EXP-013 UART framing results). All evidence files present and committed.
**Blocked by:** AF-092, AF-045, AF-068
**Required hardware:** None (document task).
**Required tools:** None.
**Required software:** Spreadsheet (Numbers/Excel/Google Sheets) or Markdown table editor. EXPERIMENTS.md open for criteria row text.
**Exact execution steps:**
1. Open EXPERIMENTS.md EXP-014 §Scoring Matrix. Note: existing empty table has rows: Hardware cost, Wiring complexity, Software complexity, Open protocol, Python integration, Wired transport, Refresh quality, Reliability, Boot/recovery, Maintainability, Custom firmware required, Mechanical size, Future flexibility. These 13 rows = baseline; ADAPT row labels to the 13 criteria from THIS task's description (reorder / relabel if needed to match the 13 in task summary: V1 text reliability, 6-panel feasibility, update latency/fps, ease of 6-panel wiring, open-protocol degree, power consumption, thermal, cost delta vs BOM baseline, available docs/community, controller availability risk, transport protocol obsolescence risk, bus/cable count, code maintainability Python vs embedded C++).
2. Create a filled table with columns: [Criterion | Weight | WF4 score | WF4 evidence cite | ESP32 score | ESP32 evidence cite].
3. Weight column: assign 1..5 based on ADR-016 decision rule priority. E.g.: Reliability = weight 5; Manufacturing / 6-panel feasibility = weight 5; Cost delta = weight 4; Code maintainability = weight 4; Open-protocol degree = weight 2; Future flexibility = weight 1. (Adjust per your best reading of the decision rule; document the weighting rationale in a footnote.)
4. WF4 column: for each of 13 rows, score 0..5 and write a short evidence cite = (AF-###, specific measurement). Examples of evidence mapping:
   - V1 text reliability: WF4 → (AF-046: Nano arbitrary text end-to-end, 10 min stable; AF-085: patterns 30 min stable) → score
   - 6-panel feasibility: WF4 → (ADR-011: 3 X-ports X1 X2 X3 map to 3 rows of 2 naturally; no extra controllers to buy) → score
   - Update latency/fps: WF4 → (AF-045: EXP-007 Phase C measured values for transfer time + update freq) → score
   - Ease of 6-panel wiring: WF4 → (3 HUB75 outputs, 3 separate top/mid/bot rows, each panel power = BOM harness 6 of 8 branches parallel) → score
   - Etc. for all 13 rows.
5. ESP32 column: same 13 rows, score 0..5 + cite (AF-###, measurement). Pull from AF-069, AF-071, AF-089 (refresh rate, PSRAM, stability), AF-068 (UART transport results), BOM.md H-02 quantity (needs 3× ESP32s + 3× HCT adapters = cost delta, etc).
6. Compute weighted total each candidate: Σ (WF4_score × weight) vs Σ (ESP32_score × weight). Higher total = numerical winner.
7. Save table.
**Expected result:** 13×2 = 26 cells filled, all with scores + evidence cites. Weighted totals computed. Numerical winner identified (may be tie; tiebreak goes to the decision rule tiebreaker = "simplest").
**Acceptance criteria / DoD:**
- Table has 13 rows × 6 columns (Criterion | Weight | WF4 score | WF4 evidence | ESP32 score | ESP32 evidence).
- Every row has a Weight value (1..5).
- All 26 score cells have integer 0..5.
- All 26 evidence cells have (AF-###, measurement-text) cite format. NOT "gut feel", "seems", "I think" — ONLY AF-### links.
- Weighted totals computed and displayed below the table.
- Numerical winner identified OR explicit tie (with tiebreak note = simplest = fewer unique parts = WF4 if 1 controller vs 3).
**Evidence to save:**
- `docs/pm/evidence/AF-093-exp-014-scoring-matrix.md` (full filled Markdown table + totals + winner line)
- Optional: spreadsheet export CSV in same folder.
**Safety considerations:**
N/A — document/decision task.
**Known uncertainties:**
- If some cell's evidence is missing (e.g., 6-panel feasibility raw data doesn't exist yet because we're at 2-panel stage), score conservatively based on topology-extrapolation reasoning BUT EXPLICITLY flag the score as "(extrapolated: AF-### 2-panel passes; 6-panel is same topology ×3 rows)". Do NOT leave cells empty.
**Failure response:**
Empty cells → go back to the AF-### cited and extract the specific measurement; do NOT invent data. If data truly missing, mark the cell "0 (NO EVIDENCE YET — extrapolated score = risk)" and flag for future task to produce it before final ADR.
**Source references:**
- EXPERIMENTS.md EXP-014 §Scoring Matrix (13 rows baseline) + §Decision Rule
- DECISIONS.md ADR-016 (decision criteria list: "reliability, simple software integration, acceptable refresh quality, low maintenance, low unit cost, wired control where practical, existing stable firmware, reasonable openness, reliable boot/recovery, mechanical simplicity")
- DECISIONS.md ADR-016 Context statement + Decision Rule verbatim = source for Weight column
- BOM.md cost lines (H-01 WF4 ~¥57 ×1 vs H-02 ESP32 ~¥30 ×3 + HCT adapters ×3)
- EXP-007 Phase C (AF-045) + EXP-013 (AF-068) measurements
**Labels:** decision blocked blocked:adr-blocked blocked:exp-af-091 docs validation
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-094: Draft ADR-016 Choose WF4 vs multi-ESP32 Architecture. Follow DECISIONS.md ADR schema VERBATIM (Title, Status=PENDING → ACCEPTED after review, Context, Decision, Consequences, Alternatives considered, Rejected alternatives with why). Decision rule VERBATIM: "simplest architecture that reliably satisfies V1 proof of concept at acceptable cost and without introducing novel manufacturing risk." DO NOT pick based on more-interesting tech. Cite EXP-014 scoring matrix (AF-093). Context must include current 6-panel WF4 X1/X2/X3 topology feasibility evidence AND ESP32 multi 3× controller HCT adapter cost/manufacturing evidence.

**Task ID:** AF-094
**Epic:** AF-004 (MG: Architecture Decision Gate — 🔒 GATED MILESTONE)
**Summary:** Draft ADR-016. DECISIONS.md schema verbatim. Choose: WF4 (single controller, 3 X-ports) vs multi-ESP32 (3× ESP32 + 3× HCT adapters). Decision rule = simplest reliable V1.
**Description:** Write the ADR-016 document entry directly into DECISIONS.md following the existing ADR schema exactly. Use the AF-093 scoring matrix as the ONLY quantitative input. Do NOT re-litigate the scores; accept the matrix. Apply the decision rule VERBATIM. The decision must be one of: "ACCEPTED: HD-WF4" or "ACCEPTED: Multi-ESP32 (3× controller)". Status starts as PENDING during this drafting task; flips to ACCEPTED after final review within this task.
**Why this task exists:** ADR-016 is the 🔒 Architecture Decision Gate. It is the canonical, committed decision. EVERY downstream M3/M4 task that is controller-specific will be reclassified based on THIS decision (AF-096 reclassification sweep executes the class changes). Without ADR-016 ACCEPTED, M3 cannot proceed on the critical path.
**Prerequisites:** COMPLETED: AF-093 (scoring matrix filled, winner identified).
**Blocked by:** AF-093
**Required hardware:** None (doc edit).
**Required tools:** None.
**Required software:** Text editor. DECISIONS.md file open.
**Exact execution steps:**
1. Open DECISIONS.md. Locate the ADR-016 existing stub (currently says PENDING, short context). Replace / augment it to the FULL ADR schema.
2. ADR-016 sections, verbatim in order:
   a. **Title:** ADR-016 — Choose Final Display-Controller Architecture (WF4 vs Multi-ESP32)
   b. **Status:** PENDING → set to ACCEPTED after writing + reviewing.
   c. **Context:** 3–5 sentences. Must cite: (i) WF4 topology for 6 panels = X1→top-row-2, X2→mid-row-2, X3→bot-row-2 — evidence cite = ADR-011 ACCEPTED + AF-085 2-panel passes. (ii) Multi-ESP32 topology = 3× ESP32-S3 devkits × 3× HCT245 perfboard adapters → one row each. Evidence cite = BOM H-02 qty needed ×3 (currently ordered qty=1, need 2 more if wins) + AF-089/AF-090 2-panel passes. (iii) Both candidates passed M2 GATE-PASS (AF-091). (iv) Why this decision is needed now: M3 4-panel and M4 6-panel depend on knowing controller count to buy/build remaining 2× ESP32 or proceed with WF4 X2/X3 config.
   d. **Decision:** One sentence. "The selected V1 display-controller architecture is [WINNER]."
   e. **Rationale:** 3–5 sentences. EXPLICITLY reference AF-093 weighted total. Quote the decision rule VERBATIM in the rationale. Explain how the winner satisfies "simplest / reliable / acceptable cost / no novel manufacturing risk" in light of the scores.
   f. **Consequences:** 3–5 bullet points. E.g., if WF4 wins: "Proceed with single WF4 card (already on hand); no additional controller purchases required; no 2nd/3rd HCT adapter build; transport is Huidu protocol (or fall-back per ADR-017)." If ESP32 wins: "Purchase 2× more ESP32-S3 (BOM H-02 qty 1→3); build 2× more HCT245 adapters (reuse AF-054..AF-065 procedure); transport per ADR-017 between Nano and 3× ESP32s."
   g. **Alternatives considered:** Bullet list: (A) WF4, (B) Multi-ESP32. (C) WF2 (rejected per ADR-012 experimental status).
   h. **Rejected alternatives with why:** Explicitly reject the LOSING architecture with 2–3 sentences referencing its AF-093 scores and how it loses on the decision rule dimensions. Also reject WF2 (ADR-012) and the cascade Nano→ESP32→WF4→panel (already rejected in current ADR-016 stub with why).
3. Re-read the ADR for alignment with DECISIONS.md voice and format.
4. Set Status = ACCEPTED.
5. Save DECISIONS.md.
**Expected result:** DECISIONS.md contains a full, schema-compliant ADR-016 entry. Status = ACCEPTED. A clear winner named.
**Acceptance criteria / DoD:**
- All 8 sections present and populated (Title, Status, Context, Decision, Rationale, Consequences, Alternatives considered, Rejected alternatives with why).
- Status = ACCEPTED.
- Decision rule quoted VERBATIM in Rationale.
- AF-093 scoring matrix cited explicitly (with AF-ID).
- Context mentions BOTH WF4 6-panel topology evidence AND ESP32 multi-3× controller cost/manufacturing evidence.
- Winner architecture named unambiguously in the Decision line.
- Rejected alternatives line explicitly names the LOSER and explains why it loses.
**Evidence to save:**
- `git diff of DECISIONS.md` (the ADR-016 delta). Commit the DECISIONS.md change with message: `docs(adr-016): ACCEPTED — final display-controller architecture = [WINNER]`.
**Safety considerations:**
N/A — document/decision task.
**Known uncertainties:**
- If M2 only had ONE path pass (not both), the decision is trivial — but still write the full ADR and explicitly note why the other path failed (reject it with evidence).
**Failure response:**
If ADR draft is inconsistent with scores → revise. Do NOT override the matrix with a different decision unless you EXPLICITLY call out why the scores are misleading (e.g., a single criterion the scores underweighted is a showstopper — but then the matrix should be corrected in AF-093 first, not overridden here). The decision rule is the ultimate tiebreak.
**Source references:**
- DECISIONS.md ADR schema (see ADR-001 through ADR-014 for exact section ordering + prose style)
- DECISIONS.md §Decision-Making Principle VERBATIM text (top of file) — must be reflected in the Rationale.
- EXPERIMENTS.md EXP-014 §Decision Rule VERBATIM
- AF-093 filled scoring matrix
- ADR-011, ADR-012, ADR-013, ADR-014 (constraining this decision)
**Labels:** decision blocked blocked:adr-blocked blocked:exp-af-093 docs
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-095: Draft ADR-017 Choose Transport Protocol. Follow DECISIONS.md ADR schema. Options: Huidu proprietary protocol (if WF4 wins), USB-serial framing, UART-hardware framing, TCP-over-Wi-Fi. Cite EXP-007 Phase C (AF-045 latency/bandwidth/update-freq results) AND EXP-013 (AF-068 UART framing results). Preference VERBATIM: "wired transport if practical and sufficient bandwidth for full-frame 256×192 at ≥1 fps." Wi-Fi TCP fallback only if wired inadequate.

**Task ID:** AF-095
**Epic:** AF-004 (MG: Architecture Decision Gate — 🔒 GATED MILESTONE)
**Summary:** Draft ADR-017. DECISIONS.md schema. Choose Nano→controller transport. Cite AF-045 (EXP-007 Phase C) + AF-068 (EXP-013 UART). Wired preference verbatim.
**Description:** Write DECISIONS.md ADR-017 full entry. Decision is conditional on ADR-016 winner's available transport options: (WF4 winner → options = Huidu protocol (USB/Wi-Fi/LAN) or WF4 USB-serial if exposed). (ESP32 winner → options = UART hardware framing, native USB, TCP-over-Wi-Fi). In all cases apply the wired preference rule VERBATIM. Cite AF-045 (Nano→WF4 bandwidth/latency/update freq) and AF-068 (Nano→ESP32 UART framing throughput). Choose ONE transport. Status = ACCEPTED after draft.
**Why this task exists:** ADR-009 (ACCEPTED: Prefer wired Nano→controller link) final resolution. M3/M4 transport code needs a SINGLE canonical transport; without this decision, dual-transport code doubles the software work.
**Prerequisites:** COMPLETED: AF-094 (ADR-016 ACCEPTED — winner controller known, because available transports DEPEND on the controller). COMPLETED: AF-045 (EXP-007 Phase C results), AF-068 (EXP-013 UART framing results).
**Blocked by:** AF-094, AF-045, AF-068
**Required hardware:** None.
**Required tools:** None.
**Required software:** DECISIONS.md editor.
**Exact execution steps:**
1. Read ADR-016 WINNER (just written at AF-094). Note which controller won.
2. Open DECISIONS.md. Edit ADR-017 stub (currently says PENDING, short context). Fill full schema:
   a. **Title:** ADR-017 — Choose Final Nano→Controller Transport Protocol.
   b. **Status:** PENDING → ACCEPTED after draft.
   c. **Context:** 3–5 sentences. State the ADR-016 winner (controller = X), note controller-specific transport options in play. Cite AF-045 and AF-068 measurement summaries (values) verbatim. Quote ADR-009 wired preference verbatim.
   d. **Decision:** "The selected Nano→controller transport protocol is [CHOSEN_TRANSPORT]."
   e. **Rationale:** Quote the wired preference VERBATIM: "wired transport if practical and sufficient bandwidth for full-frame 256×192 at ≥1 fps." Show that [CHOSEN_TRANSPORT] meets or fails this rule, with explicit AF-045/AF-068 measurement values showing bandwidth ≥ (256×192×3) bytes = 147,456 B/frame. 1 fps = ~150 KB/s. 10 fps = ~1.5 MB/s. Compare measured throughput from AF-045/AF-068 to this threshold.
   f. **Consequences:** 3–5 bullets. E.g., "Use pyserial + custom framing for UART transport"; "Huidu library (or custom) if WF4 wins"; "TCP socket server on each ESP32 if Wi-Fi TCP selected"; etc.
   g. **Alternatives considered:** Bullet list ALL options in task description: Huidu proprietary (if WF4 wins), USB-serial framing, UART-hardware framing, TCP-over-Wi-Fi.
   h. **Rejected alternatives with why:** Explicitly reject each losing alternative with a 1-sentence reason. E.g., "TCP-over-Wi-Fi: rejected because wired UART (AF-068) achieved sufficient throughput per the preference rule; Wi-Fi adds AP-dependency and recovery complexity."
3. Save DECISIONS.md. Set Status = ACCEPTED.
**Expected result:** DECISIONS.md ADR-017 full entry, Status ACCEPTED, transport named, wired-preference rule explicitly quoted in rationale with measurement-based justification.
**Acceptance criteria / DoD:**
- Full DECISIONS.md ADR schema 8 sections present.
- Status = ACCEPTED.
- Decision line explicitly names the chosen transport.
- Rationale includes the preference rule VERBATIM.
- Rationale cites BOTH AF-045 (EXP-007 Phase C) AND AF-068 (EXP-013) with specific measured values.
- Rationale includes the math: 256×192×3 = 147,456 B/frame. Measured throughput compared to 1 fps minimum.
- Rejected alternatives: each losing option named with 1-sentence why-rejected.
**Evidence to save:**
- `git diff of DECISIONS.md` (ADR-017 delta). Commit message: `docs(adr-017): ACCEPTED — final transport protocol = [CHOSEN_TRANSPORT]`.
**Safety considerations:**
N/A.
**Known uncertainties:**
- If WF4 wins: Nano→WF4 protocol is not fully documented (proprietary). If AF-045 measurement shows insufficient throughput on proprietary, we may fall-back to Wi-Fi-TCP on WF4 local net. Document this fall-back path clearly if it becomes the decision.
**Failure response:**
If no single wired transport achieves ≥1 fps on the winning controller → explicitly document this in the Rationale, THEN choose TCP-over-Wi-Fi as fall-back with a "known deviation from ADR-009 preference" note in the Consequences section. This is the ONLY acceptable case for selecting Wi-Fi.
**Source references:**
- DECISIONS.md ADR schema
- DECISIONS.md ADR-009 (Prefer wired link, ACCEPTED) — constrains this ADR
- DECISIONS.md ADR-017 existing stub
- EXPERIMENTS.md EXP-007 Phase C (Nano→WF4 measurements at AF-045)
- EXPERIMENTS.md EXP-013 (UART framing at AF-068)
- DECISIONS.md ADR-016 winner (determines the candidate transport set)
**Labels:** decision blocked blocked:adr-blocked blocked:exp-af-094 docs
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-096: Post-decision Reclassification SWEEP. Execution Steps MUST literally walk EVERY remaining task in backlog (M3 onward, i.e., tasks not yet written but the rule must be specified as the actual checklist the executor will run AFTER Task 5/6 populate them — so write the VERBATIM 4-step procedure the executor applies):
  1. Walk backlog from first M3 Epic AF-005 task to end. For every task with Label `controller-wf4` OR `controller-esp32`:
  2. If task's controller WON ADR-016: change critical-path from `candidate-yes` to `yes`. Keep Conditional=no. Remove any `blocked:adr-016` label.
  3. If task's controller LOST ADR-016: change Conditional from `no` to `yes`. Add Skip condition line: `Skip condition: ADR-016 selected [WINNER_CONTROLLER_NAME] architecture; this task uses the LOSING controller and is not needed on the critical path. May still be executed for reference data if desired but does not block progress.` Change critical-path from `candidate-yes` to `no`. Add Labels: `blocked conditional`. Remove `blocked:adr-016` if present.
  4. Any M3+ task with Label `controller-wf2`: ALWAYS reclassify to Conditional=yes + Skip="WF2 is reference/fallback track only. Skip for V1 build." + critical-path=no + Labels blocked conditional regardless of ADR-016 outcome.
  5. Any task in Epic AF-010 Cond-X ESP32 Custom PCB: IF ADR-016 = multi-ESP32 → mark all Cond-X tasks READY (remove `blocked:adr-016`, keep Conditional=no since now actual requirement). IF ADR-016 = WF4 → keep Conditional=yes and Skip condition unchanged.
  6. Save file and commit with message noting sweep applied + which controller won.

**Task ID:** AF-096
**Epic:** AF-004 (MG: Architecture Decision Gate — 🔒 GATED MILESTONE)
**Summary:** Post-ADR-016/ADR-017 backlog reclassification sweep on ALL future M3+ tasks. Execute the 6-step VERBATIM procedure. Commit.
**Description:** Architecture Decision Gate final output action. Once ADR-016 and ADR-017 are both ACCEPTED, every controller-specific future task (M3=AF-005, M4=AF-006, M5=AF-007, MR=AF-008, MF=AF-009, Cond-X=AF-010) that carries dual-track `critical-path: candidate-yes` status MUST be reclassified. The 6-step procedure is the executor checklist written VERBATIM from this task's description. Apply EVERY step.
**Why this task exists:** After a decision gate, there is NO dual track. One controller is on the critical path; the other is conditional reference. Without this sweep, downstream executor confusion is guaranteed (wasting time on both paths or skipping the wrong one). This is the formal mechanism that converts "candidate-yes" dual-path tasks into a single critical path.
**Prerequisites:** COMPLETED: AF-094 (ADR-016 = ACCEPTED with winner known). COMPLETED: AF-095 (ADR-017 = ACCEPTED). M3..M6+ tasks already exist in backlog (this sweep applies to tasks already written in AF-005..AF-010 by this point — which by backlog state of this current task are AF-097..AF-119 and any AF-120+).
**Blocked by:** AF-094, AF-095
**Required hardware:** None.
**Required tools:** None.
**Required software:** Text editor for 05-backlog.md. Git for commit.
**Exact execution steps:**
1. Walk backlog from first M3 Epic AF-005 task (ID ≥ AF-097) to end of file. For EVERY task whose Labels field contains BOTH `critical-path` AND (`controller-wf4` OR `controller-esp32`):
2. If the task's controller label = WINNER of ADR-016:
   a. In Status flags, change `critical-path: candidate-yes` → `critical-path: yes`.
   b. Keep `Conditional: no` unchanged.
   c. In Labels, REMOVE any occurrence of `blocked:adr-016` (if present — may not be on all, but if present → remove).
   d. Do NOT add any Skip condition.
3. If the task's controller label = LOSER of ADR-016:
   a. In Status flags, change `Conditional: no` → `Conditional: yes`.
   b. ADD a new line to Status flags, verbatim: `Skip condition: ADR-016 selected [WINNER_CONTROLLER_NAME] architecture; this task uses the LOSING controller and is not needed on the critical path. May still be executed for reference data if desired but does not block progress.` (Replace [WINNER_CONTROLLER_NAME] with "HD-WF4" or "multi-ESP32" based on the actual winner.)
   c. In Status flags, change `critical-path: candidate-yes` → `critical-path: no`.
   d. In Labels, ADD the 2 tags: `blocked conditional` (note: space-separated single tags → so `blocked` + `conditional`).
   e. In Labels, REMOVE `blocked:adr-016` if present.
4. Any M3+ task with Labels containing `controller-wf2`:
   a. Status flags: set `Conditional: yes` (regardless of current value).
   b. ADD Skip condition line: `Skip condition: WF2 is reference/fallback track only. Skip for V1 build.`
   c. Status flags: `critical-path: no`.
   d. Labels: add `blocked conditional`.
5. Any task in Epic AF-010 (Cond-X ESP32 Custom PCB):
   a. IF ADR-016 winner = multi-ESP32:
      - Labels: REMOVE any `blocked:adr-016`.
      - Status flags: Keep `Conditional: no` (it becomes a real requirement, no longer Conditional).
   b. IF ADR-016 winner = WF4:
      - Keep Conditional=yes as-is. Keep existing Skip condition unchanged. Do NOT change it.
6. Save the modified 05-backlog.md file. Commit with message: `docs(backlog): post-ADR-016 reclassification sweep applied. ADR-016 winner = [WINNER_CONTROLLER_NAME]. ADR-017 transport = [TRANSPORT].`
**Expected result:** Every controller-specific dual-track task in M3+ has been updated. Winner tasks → critical-path=yes. Loser tasks → Conditional=yes, Skip added, critical-path=no, labels blocked conditional. WF2 reference tasks → Conditional skip. AF-010 Cond-X PCB tasks conditionally flipped based on winner. Commit exists.
**Acceptance criteria / DoD:**
- Step 1: Walk-through from AF-097 to end completed. All candidate tasks touched.
- Step 2 (winner): ≥1 task now has `critical-path: yes` where it previously had `candidate-yes`.
- Step 3 (loser): ≥1 task now has `Conditional: yes` + Skip condition line + `critical-path: no` + labels `blocked conditional`.
- Step 4 (WF2): Any WF2-labeled tasks have Conditional=yes + skip.
- Step 5 (Cond-X PCB): Correct state based on winner.
- Step 6: Single commit with specified message format (winner name + transport name embedded in commit message).
- Post-sweep: grep for `critical-path: candidate-yes` on M3+ should return ZERO results — every candidate was promoted YES or demoted NO.
**Evidence to save:**
- Commit hash.
- `docs/pm/evidence/AF-096-reclass-sweep-diff.md` = diff of backlog showing before/after snippets for at least 1 winner task + 1 loser task.
**Safety considerations:**
N/A — doc edit task.
**Known uncertainties:**
- The M3+ tasks are populated by the AF-097..AF-119 generator tasks that follow this sweep spec; so when this sweep actually executes, the target tasks exist. This task's DoD explicitly says "AF-097 to end" so the executor does not try to pre-walk empty content.
**Failure response:**
If a task is missed in the walk-through → post-sweep grep of `candidate-yes` will find it (DoD check). Re-run the sweep for any stragglers. Re-commit. If commit message is wrong → amend the commit message.
**Source references:**
- JIRA.md §Architecture Decision Gate (section between Milestone 2 and Milestone 3)
- Backlog schema §Status flags explanation of `critical-path: candidate-yes` semantics
- DECISIONS.md ADR-016 + ADR-017 ACCEPTED entries
- Epic AF-010 (Cond-X PCB) header notes on conditionality
**Labels:** decision docs blocked blocked:exp-af-094 blocked blocked:exp-af-095
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-097: VERIFY Panels #3 and #4 polarity and HUB75 orientation (power OFF, re-verify on retrieval from storage). Epic AF-005.

**Task ID:** AF-097
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** Re-verify panels #3 and #4 polarity, HUB75 IN/OUT, orientation (power OFF) as they leave storage for M3 active use.
**Description:** Analogous to AF-081 (panel #2 re-verify) but now for panels #3 and #4. Both panels are retrieved from anti-static storage bags and placed on bench. For EACH panel, re-confirm 5 V V+/V- silk labels match M0 batch record, HUB75 IN/OUT labeled, and top-edge orientation matches panels #1 and #2 convention. This is a precondition before ANY wiring of the 2×2 = 4-panel grid.
**Why this task exists:** JIRA Safety Rules + M3 scope. Panels #3 and #4 have been in storage since M0 batch verify (AF-023); moving them risks orientation mix-up. Polarity error on the NEW branches #3 and #4 would cause panel damage.
**Prerequisites:** M2 GATE-PASS and reclassification sweep done (AF-091 and AF-096) so the winning controller path is known. Panels #3 and #4 physically retrieved onto ESD mat.
**Blocked by:** AF-096
**Required hardware:** Panels #3 and #4, M0 AF-023 photos reference.
**Required tools:** Multimeter (beep continuity mode — optional polarity confirm), camera.
**Required software:** None.
**Exact execution steps:**
1. PSU OFF. Wall plug out. Full OFF.
2. Panel #3 from bag. Place face up. Compare top-edge orientation to panel #1/2 (known good). Record match Y/N.
3. Flip #3. Check 5 V terminal block labels (V+/V-) silk visible and match M0 AF-023 photo of panel #3.
4. Check HUB75 ports IN/OUT labels #3. Record.
5. Optional multimeter: #3 V+ ferrule ↔ bulk cap pin beep, V- ↔ GND plane beep (same method AF-081).
6. Repeat steps 2–5 identically for Panel #4.
7. Take 1 back-photo of panel #3 + 1 back-photo of panel #4 (each with labels readable).
**Expected result:** Both panels #3 and #4: polarity labels match M0, HUB75 IN/OUT labeled, orientation matches 1/2 convention.
**Acceptance criteria / DoD:**
- Panel #3: polarity match, HUB75 labeled, orientation match. Back photo.
- Panel #4: polarity match, HUB75 labeled, orientation match. Back photo.
- Any mismatch → explicitly handled per Failure response BEFORE proceeding.
**Evidence to save:**
- `docs/photos/m3-panels/panel3-back-reverify.jpg`
- `docs/photos/m3-panels/panel4-back-reverify.jpg`
- `docs/pm/evidence/AF-097-panels3-4-reverify.md` (2 rows, each: match Y/N / notes)
**Safety considerations:**
⚠️ Polarity verification is power-OFF only. PSU switch OFF AND wall plug REMOVED before probing. Multimeter in continuity mode (beep). Correct polarity = beep only on matching pairs (red-wire-harness-end ↔ panel-V+-pin, black-wire-harness-end ↔ panel-V--pin). Cross-pair continuity = V+ ↔ V- MUST NOT beep — beep means short, DO NOT energize, fix first.
**Known uncertainties:**
If #3 or #4 orientation flipped vs 1/2 → will cause bottom-row content to render upside-down or reversed; handled by flagging + marking in Failure response.
**Failure response:**
Any mismatch → STOP. Do NOT wire. If polarity flipped vs M0 → swap branch-end ferrules for that panel's harness. If orientation flipped → label top edge of the affected panel with tape "FLIP TOP ↓" (bottom-row panel flips are handled by mounting flipped OR software row-rotation per controller config).
**Source references:**
- AF-081 (panel #2 re-verify precedent)
- AF-023 (M0 batch verify, reference photos)
- JIRA.md §Safety Rules (polarity + re-verify before energize)
**Labels:** hardware polarity-verify validation blocked blocked:exp-af-096
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-098: WF4 M3 topology setup: (a) Panels #3 and #4 bottom row physical placement on bench aligned with top row; (b) Bottom-row chain: panel#3 IN → panel#3 OUT → panel#4 IN; (c) Power: 4 parallel branches (top-row panel#1, top-row panel#2, bottom-row panel#3, bottom-row panel#4) → PSU. Branches #3 and #4 new ferrules. (d) WF4 X1 → panel#1 IN (carry forward from M2). WF4 X2 → panel#3 IN (bottom-row first panel). Labels: controller-wf4 critical-path candidate-yes + Reclassification rule.

**Task ID:** AF-098
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** WF4 EXP-017 4-panel topology wiring: 2 rows × 2 panels. X1=top-row (panel#1→#2). X2=bottom-row (panel#3→#4). 4 parallel power branches. No hot-plug.
**Description:** WF4 candidate M3 physical wiring. JIRA Milestone 3 topology: WF4 X1→top-left→top-right, X2→bottom-left→bottom-right. Top-row chains carry forward from M2. NEW: Bottom-row HUB75 chain (panel#3→panel#4) + 2 new power branches #3 and #4 (parallel → PSU). WF4 X2 output connects to panel#3 IN. Power order: PSU OFF → all HUB75 connections → power ON. Harness allocation: BOM has 2× 4-branch harnesses = 8 branches total; M3 uses branches #1 and #2 (harness 1) + #3 and #4 (harness 1 remaining or harness 2 first two — executor decides but label them BR-1-P1, BR-2-P2, BR-3-P3, BR-4-P4 explicitly with tape).
**Why this task exists:** EXP-017 topology bring-up WF4 path. 4 parallel branches (ADR-007). Prepares for WF4 EXP-017 config (AF-102) and first-light.
**Prerequisites:** COMPLETED: AF-097 (panels 3/4 re-verify pass). Panels 1/2 still present on bench from M2. BR-3 and BR-4 ferrules crimped (if not already → insert mini-task now: ferrule-crimp both wires, both ends, for branches #3 and #4).
**Blocked by:** AF-097
**Required hardware:** Panels #1,#2 (carry-over), panels #3,#4 (new), WF4 controller, HUB75 cables: 2× (panel#3 IN ← WF4 X2, panel#3 OUT → panel#4 IN) + existing top-row cables, PSU harness branches #3,#4 with new 18 AWG red/black ferrules crimped both ends, PSU A-200-5, C14 harness.
**Required tools:** Ferrule crimper (if BR-3/4 ferrules pending), screwdriver PSU terminals, multimeter (voltage standby).
**Required software:** None (wiring only).
**Exact execution steps:**
1. PSU OFF, wall plug OUT. Full OFF.
2. Physical placement: Arrange 2×2 bench grid. Top row = panel#1 (left), panel#2 (right) — carry from M2. Bottom row directly below = panel#3 (left, aligned below #1), panel#4 (right, aligned below #2). Equal spacing. Same gap between rows as between columns (target ~5 mm gap for eventual frame; 10 mm OK for bench).
3. Bottom-row HUB75 chain:
   a. WF4 X2 HUB75 OUT → HUB75 cable → Panel #3 IN. Notch seated.
   b. Panel #3 OUT → HUB75 cable → Panel #4 IN. Notch seated both ends.
4. Top-row HUB75 chain carry-forward check: WF4 X1 → panel#1 IN → panel#2 IN still seated. Reseat if loose (only while OFF).
5. Panel power 4 branches parallel:
   a. BR-1 P1 → panel#1 V+/V- (carry).
   b. BR-2 P2 → panel#2 V+/V- (carry).
   c. BR-3 P3 → panel#3 V+/V- (NEW). Red=V+, Black=V-. Screws tight.
   d. BR-4 P4 → panel#4 V+/V- (NEW). Same. Screws tight.
6. PSU side: BR-1/2/3/4 all V- ferrules → shared PSU V- post. BR-1/2/3/4 all V+ ferrules → shared PSU V+ post. Screws tight. 4-way parallel screw-down OK (ADR-007).
7. Tape labels on each branch: "BR-1-P1", "BR-2-P2", "BR-3-P3", "BR-4-P4".
8. Visual: all screws tight. No stray wire strands. 4 HUB75 connectors seated (X1→P1, P1→P2, X2→P3, P3→P4).
9. Power ON STRICT ORDER:
   a. Wall plug IN.
   b. C14 switch ON.
   c. Wait 3 s. Multimeter at PSU posts: 5.00–5.10 V DC. Record.
10. All 4 panels not dark → power OK. WF4 boot.
**Expected result:** PSU 5.00–5.10 V. All 4 panels powered (not dark). HUB75: 2 rows × 2 chain.
**Acceptance criteria / DoD:**
- Physical 2×2 grid alignment (#1 above #3, #2 above #4).
- HUB75: X1→#1→#2 chain (top), X2→#3→#4 chain (bottom). 4 plugs seated.
- 4 parallel power branches (#1,#2,#3,#4) → PSU V+/V- shared. Screws tight.
- PSU voltage = 5.00–5.10 V DC.
- Panels 1,2,3,4 ALL not dark after power on.
- Labels on branches: "BR-1-P1" through "BR-4-P4".
**Evidence to save:**
- `docs/photos/wf4-m3/2x2-grid-overview.jpg`
- `docs/photos/wf4-m3/psu-4-branches-tight.jpg`
- `docs/photos/wf4-m3/psu-voltage-4panel-on.jpg`
- `docs/pm/evidence/AF-098-wf4-m3-topology-notes.md` (voltage reading, timestamp)
**Safety considerations:**
⚠️ No HUB75 hot-plug. Always power OFF controller + panels before connecting/disconnecting HUB75. Power ON order: 5V PSU → controller power → panel data. Power OFF order reverse: panels/controller sign OFF in software or idle → PSU switch OFF → unplug HUB75 only after full OFF. No hot-plug exceptions, not even for a quick test — hot-plug can destroy HUB75 shift-register outputs, HCT245 buffers, or ESP32/WF4 GPIO pins via transient overvoltage on data lines.
⚠️ 5 V high-current rule (JIRA Safety Rules 4/4). NO Dupont connectors or breadboard jumper wires for any continuous ≥2 A (40 W) 5 V path. Use screw terminals, ferrule crimps (18 AWG), or polarized locking Molex-style connectors. NEVER run full load current in series through a multimeter ammeter input — risk of burning out multimeter fuses or input protection and giving false readings from series voltage drop. Use clamp meter (if available) or measure voltage drop across known trace/connector resistance, or measure voltage at both ends of path under load and compute drop < 0.25 V per branch as pass criterion.
**Known uncertainties:**
- WF4 X2 output mapping: some WF4 cards label ports differently (e.g., X1/X2/X3 in non-top-to-bottom order). If bottom-row content ends up on wrong physical row, swap config in AF-102 (software port→row assignment).
**Failure response:**
Panel #3 or #4 dark → PSU OFF, check screws, check harness ferrules seated, polarity. Voltage out of range → PSU trim or swap. Chain wrong → reseat HUB75 with POWER OFF.
**Source references:**
- EXPERIMENTS.md EXP-017 (M3 4-panel first-class milestone experiment, defined in JIRA Milestone 3 spec)
- JIRA.md Milestone 3 topology spec: WF4 X1→top-left→top-right, X2→bottom-left→bottom-right
- ADR-007 parallel distribution (4 parallel branches for M3)
- BOM.md H-07: 2 harnesses × 4 branches = 8 total branches; M3 uses 4 of 8
- AF-083 WF4 M2 topology (analogous single-row wiring)
**Labels:** hardware controller-wf4 wiring safety-review critical-path candidate-yes blocked blocked:exp-af-097
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-099: ESP32 M3 multi-controller prep CONDITIONAL (Conditional: yes pre-decision, Skip="Skip if ADR-016 selects WF4"). (a) Verify 2nd ESP32-S3 board on hand (if still ONLY 1 ESP32 → BLOCKED: needs 2nd purchase from BOM H-02 quanity≥2 update, insert purchase task). (b) Build 2nd HCT245 perfboard adapter: REPEAT stages 1-12 from AF-054..AF-065 (you don't need to write separate 12 tasks here, this ONE task references AF-054..AF-065 as the procedure and the executor must produce the same 12 DoD evidence checklist). Continuity 14 HUB75 signals end-to-end confirmed. Labels: controller-esp32 critical-path candidate-yes + Reclassification rule.

**Task ID:** AF-099
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** ESP32 M3 multi-controller prep (CONDITIONAL). Verify 2nd ESP32 on hand. Build 2nd HCT245 perfboard adapter = AF-054..AF-065 full procedure repeat. 14-signal continuity.
**Description:** Multi-ESP32 architecture needs 1 controller per row. M2 used 1 controller; M3 needs 2 controllers (top-row + bottom-row). Therefore: need a 2nd ESP32 board and a 2nd HCT245 perfboard adapter. CONDITIONAL: if ADR-016 selected WF4 → skip this task entirely via Skip condition. If ESP32 won (or pre-decision dual-track), execute. Step (a): BOM H-02 currently qty=1 (generic ESP32-S3 N16R8). If we still only have 1 → M3 is BLOCKED until purchase of qty 2 more (for M4 6-panel = 3 controllers total). Step (b): build 2nd HCT adapter EXACTLY reusing AF-054 through AF-065 (the 12-stage HCT build procedure that was used for controller #1). The executor must produce EVERY evidence item from those 12 tasks for the 2nd adapter, and the 12 DoD checklists must be ticked per adapter #2. This is ONE Jira task but its sub-workload = 12 stages. Final output of stage 12 = continuity 14 HUB75 signals end-to-end verified on adapter #2.
**Why this task exists:** JIRA Milestone 3 topology: ESP32 #1→top-row, ESP32 #2→bottom-row. Both rows need a fully-built HCT-buffered adapter. Scaling from 1→2 controllers is non-trivial hardware work. Resolves U-041 (2nd/3rd ESP32 build feasibility + build-hours estimate).
**Prerequisites:** CONDITIONAL: ADR-016 NOT YET DONE (dual-track mode) OR ADR-016 DONE = multi-ESP32 winner. COMPLETED: AF-097 (panels 3/4 re-verify).
**Blocked by:** AF-097
**Required hardware:** (a) 2nd ESP32-S3 N16R8 (BOM H-02 qty update: at least 2 on hand → need 3 eventually for M4; if only 1 → BLOCKED PURCHASE). (b) HCT components (SN74HCT245N ×1, 100 nF cap ×1, 2×8 box header ×1, perfboard 5×7 cm ×1, male headers 1×40 bits, solder, flux, 18 AWG 5V/GND wire, ferrules for 5V/GND if screw-tempinals method). HCT components qty = BOM originally had HCT ×2 for prototype; now building adapter #2 uses the 2nd set.
**Required tools:** Soldering iron, ferrule crimper, multimeter continuity, 12-stage checklist printed or open from AF-054..AF-065.
**Required software:** None.
**Exact execution steps:**
1. CONDITION CHECK first: Open DECISIONS.md ADR-016 Status. If ADR-016 = ACCEPTED and winner = "HD-WF4" → EXECUTE SKIP: apply Skip condition and mark task done (no hardware work). If winner = "multi-ESP32" OR still PENDING (dual-track) → continue.
2. Sub-task (a) — 2nd ESP32 on-hand check:
   a. Inventory ESP32-S3 boards physically present. Count = ?
   b. If count < 2 → BLOCKED. Insert a BOM purchase task: update BOM.md H-02 qty from 1→3 (M3+M4 need 3 total). Place order for 2× more ESP32-S3-N16R8 boards matching the AF-048 measurement spec. Wait for delivery + re-run AF-048 identification for the new boards (silk check, measurement check).
   c. If count ≥ 2 → proceed.
3. Sub-task (b) — Build 2nd HCT245 perfboard adapter:
   a. Open AF-054 through AF-065 in 05-backlog.md. These 12 tasks define the 12 stages of the HCT adapter build.
   b. FOR EACH stage 1–12 (AF-054 → AF-065):
      i. Read the Exact execution steps of that stage.
      ii. Repeat them IDENTICALLY for the new adapter #2. Same pin mapping, same component orientation, same 100 nF cap placement beside each HCT245, same HUB75 keyed header orientation.
      iii. Tick off that stage's Acceptance criteria / DoD items for adapter #2.
      iv. Produce the stage's evidence (photo, continuity row) and save under `esp32-hct-adapter-2/` folder mirroring the adapter-1 evidence names.
   c. After stage 12 (AF-065 equivalent): run the full 14 HUB75 signal end-to-end continuity check (ESP32 GPIO side → HUB75 header pin side). 14 beeps. All signals match. NO cross-shorts.
**Expected result:** (ESP32-path only) 2nd ESP32 board identified. 2nd HCT adapter fully built with all 12 stages done, 14/14 continuity.
**Acceptance criteria / DoD:**
- Skip applied IF ADR-016 winner = WF4 (with Skip condition line present in Status flags after task execution).
- ELSE (ESP32 path active):
   - Count of ESP32-S3 boards physically on hand ≥ 2. (If purchase required, BLOCKED flag set and purchase task inserted before this task completes.)
   - 2nd HCT adapter: 12 stages × DoD items ALL ticked (same checklist as adapter #1, adapted to adapter #2).
   - 2nd HCT adapter: 14 HUB75 signal continuity 14/14 = beeps. No cross-shorts.
   - Adapter #2 evidence photos folder populated with 12+ analogous photos to adapter #1.
**Evidence to save:**
- `docs/pm/evidence/AF-099-esp32-multiprep.md` (12-stage checklist for adapter #2 — each stage DoD pass/fail ticked).
- `docs/photos/esp32-hct-adapter-2/` — photo set mirroring the adapter #1 set (AF-054..AF-065 photo names but adapter2).
- `docs/pm/evidence/AF-099-adapter2-14-continuity.md` (14 rows beep Y/N, cross-short N/N).
- If BLOCKED PURCHASE → blocked note + BOM H-02 diff + order screenshot.
**Safety considerations:**
⚠️ Solder task safety (from AF-054..AF-065 stages): soldering iron hot tip, fume extraction or well-ventilated area. No stray solder balls.
**Known uncertainties:**
- ESP32 board revisions of new boards ordered may differ slightly from the first; re-run AF-048 identification for any new board.
- HCT component lead time if spares insufficient; check BOM P-08/P-09 before soldering.
**Failure response:**
12-stage fail at any stage → same failure response as the corresponding AF-054..AF-065 task (re-solder, re-seat, continuity re-check). Continuity fail → debug solder bridge, dry joint, wrong wire. BLOCKED PURCHASE → stop, create purchasing task, wait delivery.
**Source references:**
- AF-054 through AF-065 procedure VERBATIM for 12-stage HCT build (executor copies these steps)
- JIRA Milestone 3 topology spec: Multi-ESP32 #1→top-row, #2→bottom-row
- BOM.md H-02 ESP32 qty; H-08 HCT×2; H-10 perfboard
**Labels:** hardware controller-esp32 soldering safety-review conditional critical-path candidate-yes blocked blocked:exp-af-097 blocked:adr-016
**Status flags:**
critical-path: candidate-yes
Conditional: yes
Skip condition: Skip if ADR-016 selects WF4.
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-100: ESP32 M3 topology setup CONDITIONAL (skip for WF4). (a) Panels #3,#4 bottom-row placement same as WF4; (b) Bottom-row chain panel#3→panel#4 HUB75; (c) 4 parallel power branches same as WF4; (d) ESP32 controller #1 HCT adapter → top-row panel#1 IN (carry forward from M2). ESP32 controller #2 HCT adapter → bottom-row panel#3 IN.

**Task ID:** AF-100
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** ESP32 M3 4-panel topology wiring CONDITIONAL. 2 controllers: #1→top-row, #2→bottom-row. 4 parallel power branches. Skip for WF4.
**Description:** ESP32 candidate M3 physical wiring. Mirrors AF-098 (WF4) but with 2 ESP32 + 2 HCT adapters instead of 1 WF4 with 2 X-ports. Panels and power branches identical to AF-098 (same 4-parallel, same panel placement, same panel chain per row). Only difference: 2 controllers instead of 1 WF4. Strict no-hot-plug power order.
**Why this task exists:** EXP-017 ESP32 dual-controller topology setup. Mirrors WF4 AF-098 for comparability.
**Prerequisites:** CONDITIONAL (skip WF4 win). COMPLETED: AF-099 (adapter #2 built + 2nd ESP32 on hand). COMPLETED: AF-097.
**Blocked by:** AF-099
**Required hardware:** 2 × ESP32-S3 + 2 × HCT245 perfboard adapters (#1 built in M1, #2 built AF-099), panels 1/2/3/4, HUB75 cables (same count AF-098 + 1 extra for controller#2→panel3), 4 power branches, PSU, C14.
**Required tools:** Screwdriver PSU terminals, multimeter standby voltage.
**Required software:** None.
**Exact execution steps:**
1. CONDITION CHECK: ADR-016 winner = WF4 → APPLY SKIP; mark done. Else continue.
2. PSU OFF, wall plug OUT. Full OFF.
3. Panel 2×2 physical placement: identical to AF-098 step 2.
4. HUB75 row chains:
   a. Controller #1 (top) HCT HUB75 out → Panel #1 IN → Panel #1 OUT → Panel #2 IN. (Carry from M2; reseat only if loose while OFF.)
   b. Controller #2 (bottom) HCT HUB75 out → Panel #3 IN → Panel #3 OUT → Panel #4 IN. (New chain, notch engaged all ends.)
5. Power: 4 parallel branches BR-1..BR-4 identical to AF-098.
6. Controllers power: Controller #1 5 V power carry from M2. Controller #2 5 V power same method as #1 (PSU 5 V branch or USB; ground common).
7. Power ON STRICT ORDER (same AF-098 step 9). Voltage 5.00–5.10 V. Both controllers boot. All 4 panels not dark.
**Expected result:** 2×2 grid. 2 ESP32 boot. All 4 panels powered. Panel chain: controller#1→top-row 2 panels, controller#2→bottom-row 2 panels.
**Acceptance criteria / DoD:**
- Skip condition applied if WF4 wins. Else:
- Physical 2×2 grid same as AF-098.
- 2 ESP32 controllers both boot (LEDs / serial enumerate).
- 4 HUB75 connections seated: ctrl#1→p1, p1→p2, ctrl#2→p3, p3→p4.
- 4 parallel power branches (BR-1..BR-4) all tight.
- PSU voltage 5.00–5.10 V DC.
- 4 panels not dark.
**Evidence to save:**
- `docs/photos/esp32-m3/2x2-grid-overview.jpg`
- `docs/photos/esp32-m3/psu-voltage-4panel-on.jpg`
- `docs/pm/evidence/AF-100-esp32-m3-topology-notes.md`
**Safety considerations:**
⚠️ No HUB75 hot-plug. Always power OFF controller + panels before connecting/disconnecting HUB75. Power ON order: 5V PSU → controller power → panel data. Power OFF order reverse: panels/controller sign OFF in software or idle → PSU switch OFF → unplug HUB75 only after full OFF. No hot-plug exceptions, not even for a quick test — hot-plug can destroy HUB75 shift-register outputs, HCT245 buffers, or ESP32/WF4 GPIO pins via transient overvoltage on data lines.
⚠️ 5 V high-current rule (JIRA Safety Rules 4/4). NO Dupont connectors or breadboard jumper wires for any continuous ≥2 A (40 W) 5 V path. Use screw terminals, ferrule crimps (18 AWG), or polarized locking Molex-style connectors. NEVER run full load current in series through a multimeter ammeter input — risk of burning out multimeter fuses or input protection and giving false readings from series voltage drop. Use clamp meter (if available) or measure voltage drop across known trace/connector resistance, or measure voltage at both ends of path under load and compute drop < 0.25 V per branch as pass criterion.
**Known uncertainties:**
U-041 (ESP32-to-ESP32 mechanical fit on bench): both need USB cables for serial + 5 V. May need a USB hub.
**Failure response:**
Same as AF-098 (panel dark → power OFF check wiring). Controller #2 no boot → same as #1 M1 boot debug. Power order strictly followed.
**Source references:**
- AF-098 WF4 M3 topology (analogous procedure; compare wiring photos)
- JIRA Milestone 3 topology: ESP32 #1→top, #2→bottom
- ADR-007 parallel 4 branches
- BOM.md harness allocation (4 of 8 used)
**Labels:** hardware controller-esp32 wiring safety-review conditional critical-path candidate-yes blocked blocked:exp-af-099 blocked:adr-016
**Status flags:**
critical-path: candidate-yes
Conditional: yes
Skip condition: Skip if ADR-016 selects WF4.
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-101: Nano framebuffer 256×128 (4-panel) software preparation. (a) Framebuffer.new(256,128); (b) Pillow render arbitrary 4-line content fitting 256×128; (c) Split framebuffer into 2 row-crops: top-row crop = 256×64 y=0..63 (panels 1+2), bottom-row crop = 256×64 y=64..127 (panels 3+4). (d) Transport send_frame for each crop to respective controller (X1 vs X2 on WF4 or controller#1 vs controller#2 on ESP32). Labels: software nano critical-path yes Conditional no.

**Task ID:** AF-101
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** Nano software upgrade from 256×64 → 256×128 framebuffer. Pillow render + row-crop split into top-256×64 and bottom-256×64. Transport send to each row's controller.
**Description:** ADR-002 canonical framebuffer scaling step. M1/M2 used 256×64 (or 128×64). M3 introduces 256×128 = 256×192 / 1.5. Implement (a) new framebuffer dims; (b) Pillow multi-line render to 256×128; (c) 2-row horizontal crop split (top-crop and bottom-crop); (d) transport abstraction updated to send each crop to the correct controller port. The transport layer is polymorphic per ADR-017 winner (Nano→WF4 uses Huidu lib / Nano→multi-ESP32 uses per-controller UART or USB endpoint). Code must call the correct transport send per row.
**Why this task exists:** ADR-002 canonical framebuffer scaling. JIRA Milestone 3 requirement: "Nano rendering a 256×128 logical framebuffer" + transport split. Without this, 4-panel content is just 2 independent 256×64; we need the single 256×128 abstraction so renderer code is controller-agnostic.
**Prerequisites:** COMPLETED: AF-096 (ADR-017 transport selected, so transport code path is known). COMPLETED: M2 Nano 256×64 framebuffer code already working (carried from AF-085/AF-089 controller-side Nano integration terminal tasks).
**Blocked by:** AF-096
**Required hardware:** Nano on bench powered (SSH reachable), controllers powered (per AF-098 or AF-100 depending on path).
**Required tools:** SSH client. Text editor on Nano.
**Required software:** Python on Nano (Pillow, `asyncio`), transport library implementing the ADR-017-selected protocol. Existing 256×64 framebuffer code (from M1/M2).
**Exact execution steps:**
1. Nano SSH login. Navigate to display app source.
2. Framebuffer config: Change width=256 (keep), height from 64 → 128. Framebuffer byte buffer grows to 256×128×3 = 98,304 bytes (≈96 KB). This fits comfortably in Nano's 256 MB DDR3.
3. Pillow render: Write test helper function that renders 4 lines of text into the 256×128 canvas at y positions = 8, 32, 72, 96 (top-row: y 0-63, bottom-row: y 64-127). Use DejaVu Sans or system CJK font.
4. Row-crop split: Implement `split_rows(frame, row_height=64)` → returns list of row crops:
   - row[0] = frame.crop((0, 0, 256, 64)) → top-row (panels #1 and #2)
   - row[1] = frame.crop((0, 64, 256, 128)) → bottom-row (panels #3 and #4)
5. Transport dispatch: For each row index i (0..1):
   - IF WF4 path (ADR-016=WF4): `transport.send_to_port(port=('X1' if i==0 else 'X2'), image=row[i])`
   - IF multi-ESP32 path (ADR-016=ESP32): `transport.send_to_controller(id=i, image=row[i])`
6. Unit test: Generate a 256×128 frame. Assert row crops' shapes = (256, 64) each. Assert row[1] pixel at (0,0) == frame pixel at (0,64).
7. End-to-end smoke: Send 4-line test content. Observe top 2 lines render on physical top-row panels. Bottom 2 lines render on physical bottom-row panels.
**Expected result:** 256×128 framebuffer instantiates. 4-line Pillow render succeeds. Row crops correct. Transport dispatch sends each crop to its correct controller → physical rows correct.
**Acceptance criteria / DoD:**
- Framebuffer height = 128. Size = 98,304 bytes (verified in code or runtime print).
- Unit test: row crops correct shape and pixel carry-over.
- E2E smoke: 4-line content visible — lines 1,2 on top-row panels, lines 3,4 on bottom-row panels.
- Transport abstraction remains controller-agnostic (render code does not contain `if WF4` branch — transport layer encapsulates).
**Evidence to save:**
- `docs/pm/evidence/AF-101-framebuffer-256x128-code.diff` (git diff of the framebuffer + split + transport changes)
- `docs/photos/m3-software/4line-test-top-bottom.jpg` (photo of physical display showing lines split correctly between rows)
- `docs/pm/evidence/AF-101-nano-render-notes.md` (crop test assertions, timestamps)
**Safety considerations:**
N/A — software only.
**Known uncertainties:**
- Crop off-by-one: ensure the crop upper bound is exclusive (Pillow convention) so row[0] covers y=0..63 (64 rows) and row[1] covers y=64..127. Photo verification catches this.
**Failure response:**
Wrong content on wrong row → debug transport port/id mapping (WF4: swap X1/X2 string name mapping). Row crops overlap or gap → debug crop tuple (box) parameters. Transport fails entirely → drop back to 256×64 single-row test to confirm per-row transport still works, then re-add split.
**Source references:**
- DECISIONS.md ADR-002 (Canonical 256×192 framebuffer with transport abstraction) → partial implement now at 256×128, extendable later to 256×192
- JIRA Milestone 3 requirement: "Nano rendering a 256×128 logical framebuffer"
- M1 AF-030 (Pillow text renderer), AF-046 (Nano→WF4 E2E)
- M2 Nano integration tasks (transport baseline)
**Labels:** software nano critical-path yes blocked blocked:exp-af-096
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-102: WF4 EXP-017 4-panel configuration + first-light. Set X1=256×64 chain top-row, X2=256×64 chain bottom-row. 256×128 total. Verify top-row and bottom-row panels receive their correct crop. Standard test patterns.

**Task ID:** AF-102
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** WF4 EXP-017 config: X1=256×64 chain=2 top-row, X2=256×64 chain=2 bottom-row. Total resolution 256×128. First-light standard patterns.
**Description:** Analogous to AF-084 (WF4 256×64 single-row config) but extended to X2 as second independent 256×64 row. In Huidu software, set controller as multi-output (or "two screen" if available): Screen 1 (output X1) = 256×64 chain 2 horizontal top-row; Screen 2 (output X2) = 256×64 chain 2 horizontal bottom-row. Total logical canvas 256×128 or 2 screens that we compose via Nano crop dispatch (AF-101). Send standard patterns to each row via Huidu software or Nano AF-101 split, confirm each row lights correctly.
**Why this task exists:** WF4 candidate M3 config. EXP-017 §Procedure step 1-3 WF4-side. Validates that WF4 X2 output independently drives a 2nd 256×64 row without cross-talk.
**Prerequisites:** COMPLETED: AF-098 (WF4 topology wiring, 4 panels powered). COMPLETED: AF-101 (Nano split works, or fall-back to Huidu direct per-row pattern send).
**Blocked by:** AF-098
**Required hardware:** WF4 4-panel setup (per AF-098). PC or Nano connected.
**Required tools:** Camera.
**Required software:** Huidu control software (same AF-084) OR Nano AF-101 framebuffer app.
**Exact execution steps:**
1. Open Huidu software. Navigate to WF4 controller multi-output config.
2. Configure Output/X1 (Screen 1): Width=256, Height=64, Chain=2 horizontal, Scan=1/32, Panel order left→right (top-row panels #1→#2).
3. Configure Output/X2 (Screen 2): Width=256, Height=64, Chain=2 horizontal, Scan=1/32, Panel order left→right (bottom-row panels #3→#4).
4. If Huidu supports a combined 256×128 screen (2 rows of 256×64 stitched): enable combined canvas mode = 256×128 with X1 as row0 and X2 as row1.
5. Save config, reboot WF4.
6. After reboot: Send solid red to X1/top-row → confirm panels #1 and #2 red. Send solid green to X2/bottom-row → confirm panels #3 and #4 green.
7. Send checkerboard to both rows → check panel order per row (left/right).
8. Send full 256×128 test image via Nano AF-101 (if available) or Huidu combined canvas. Confirm top-half vs bottom-half correct physical placement.
**Expected result:** Top-row panels receive X1 content exclusively. Bottom-row receives X2 content exclusively. Combined 256×128 works.
**Acceptance criteria / DoD:**
- X1 config = 256×64 chain 2 panels saved.
- X2 config = 256×64 chain 2 panels saved.
- Config persists after reboot.
- Solid red top (#1,#2), solid green bottom (#3,#4) — no cross-row leakage.
- Checkerboard both rows: left/right panel order correct in each row.
- Full 256×128 content from Nano displays correct top/bottom halves on correct physical rows.
**Evidence to save:**
- `docs/photos/wf4-m3/config-screenshot.png`
- `docs/photos/wf4-m3/red-top-green-bottom.jpg`
- `docs/photos/wf4-m3/full-256x128-first-light.jpg`
- `docs/pm/evidence/AF-102-wf4-exp017-config-notes.md`
**Safety considerations:**
N/A — software config only. WF4 powered per AF-098 power order.
**Known uncertainties:**
WF4 screen-combine mode naming varies by software version. If 256×128 combined canvas not available, use Nano side to always send independent X1 and X2 256×64 frames (AF-101 split) — this achieves the same logical 256×128 result.
**Failure response:**
Cross-row leakage (X2 content on X1 panels) → check port mapping in software; X1/X2 physical cable might be swapped. Bottom-row inverted top/bottom content → panel orientation flipped from top-row; enable per-screen 180° rotation in Huidu if available.
**Source references:**
- AF-084 WF4 M2 config (analogous 1-row config procedure)
- JIRA Milestone 3 requirements: "two 256×64 signal rows", "arbitrary content crossing both horizontal and vertical physical seams"
- EXP-017 milestone definition (M3 4-panel)
**Labels:** firmware controller-wf4 configuration critical-path candidate-yes blocked blocked:exp-af-098
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-103: ESP32 EXP-017 4-panel first-light CONDITIONAL. controller#1 top-row 256×64, controller#2 bottom-row 256×64. Send each crop, verify independent correct render.

**Task ID:** AF-103
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** ESP32 EXP-017 4-panel first-light CONDITIONAL. Controller#1→top-row 256×64, controller#2→bottom-row 256×64. Verify renders.
**Description:** ESP32 candidate M3 first-light. Mirrors AF-102 WF4. Flash BOTH controllers with AF-088 style 256×64 chain=2 firmware (controller #2 independent compile+flash). Use Nano AF-101 split to send top-crop to #1 and bottom-crop to #2 via transport ADR-017 protocol. Observe top-row and bottom-row independently correct.
**Why this task exists:** EXP-017 ESP32 first-light baseline. Validates 2-controller independent rendering before seam verification (AF-104). Mirrors WF4 AF-102.
**Prerequisites:** CONDITIONAL (skip WF4 win). COMPLETED: AF-100 (topology) + AF-101 (Nano split + transport).
**Blocked by:** AF-100
**Required hardware:** 2 ESP32 controllers + 4 panels (per AF-100). Nano connected (transport working to controller #1 and #2 addresses).
**Required tools:** Camera. Serial monitor for each controller if separate USB cables.
**Required software:** ESP32 firmware AF-088 style recompiled for controller#2. Transport code supports controller id=0 and id=1 with unique device paths or addresses.
**Exact execution steps:**
1. Skip condition check. If WF4 win → skip. Else continue.
2. Flash controller #1 firmware (top) = AF-088 config (256×64 chain=2). Same id=0 address.
3. Flash controller #2 firmware (bottom) = same 256×64 chain=2 firmware but with address/id=1 (or different UART port or different TCP IP).
4. Boot both. Confirm each individually responds to transport ping.
5. Via Nano AF-101: send top-crop (256×64 red) to id=0. Bottom-crop (256×64 green) to id=1.
6. Observe: panels #1,#2 red; panels #3,#4 green. No cross.
7. Send checkerboard to both rows. Verify panel order correct left/right per row.
8. Send full 256×128 image via Nano split; top/bottom rows correct.
**Expected result:** Controller#1 drives top-row, controller#2 drives bottom-row independently. No cross-row leakage.
**Acceptance criteria / DoD:**
- Skip if WF4 win.
- Else: red top (#1,#2), green bottom (#3,#4) correct.
- Checkerboard row-wise correct.
- Full 256×128 content from Nano split correct.
**Evidence to save:**
- `docs/photos/esp32-m3/red-top-green-bottom.jpg`
- `docs/photos/esp32-m3/full-256x128-first-light.jpg`
- `docs/pm/evidence/AF-103-esp32-exp017-firstlight-notes.md`
**Safety considerations:**
Energized observation only. No wiring touches.
**Known uncertainties:**
2-controller address differentiation in transport: serial requires 2 USB ports (each controller has its own /dev/ttyUSBx); TCP requires 2 IPs. Ensure Nano transport code maps id→port/IP correctly.
**Failure response:**
Controller #2 no response → USB cable test; flash verification test; boot mode check. Cross-row leakage → transport id map swapped (top→id=1, bottom→id=0). Row panel order wrong in #2 → re-flash #2 with inverted chain order flag.
**Source references:**
- AF-102 WF4 first-light (analogous)
- AF-088 ESP32 M2 firmware (compile baseline for both controllers)
- AF-101 Nano split + transport dispatch
**Labels:** validation firmware controller-esp32 conditional critical-path candidate-yes blocked blocked:exp-af-100 blocked:adr-016
**Status flags:**
critical-path: candidate-yes
Conditional: yes
Skip condition: Skip if ADR-016 selects WF4.
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-104: EXP-017 seam-crossing VERIFY both seams. (a) VERTICAL seam at x=128: BOTH rows independently → 1px line continuous; gradient smooth; text straddles. (b) HORIZONTAL seam at y=64: top-row bottom edge ↔ bottom-row top edge → 1px horizontal line at y=64 continuous across all 256px; vertical gradient at y=63→64 smooth both rows; text straddling horizontal seam with 10px above and below. (c) Content crossing BOTH SEAMS SIMULTANEOUSLY: diagonal line from (120,60)→(136,68); or circular shape centered at (128,64). Evidence: 4 close-up photos = vert seam top row, vert seam bot row, horiz seam all panels, both-simultaneous.

**Task ID:** AF-104
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** EXP-017 M3 4-panel seam verification. 3 major checks = (a) 2 vertical seams (top row x=128, bottom row x=128), (b) 1 horizontal seam (y=64), (c) both seams simultaneous. 4 close-up photos.
**Description:** JIRA Milestone 3: "arbitrary content crossing both horizontal and vertical physical seams". This task = dedicated seam verification for the 2×2 = 4-panel layout. (a) Two vertical seams (one per row, same x=128, M2 style check each row). (b) New: horizontal seam at y=64 between top-row (y=0..63) and bottom-row (y=64..127) — check with 1px horizontal line at y=64 spanning all 256 px, vertical gradient crossing y=63→64, and a text label straddling. (c) New: content crosses BOTH seams at once — tests the single canonical framebuffer truly maps to all 4 panels' quadrants. Each check gets a dedicated close-up photo.
**Why this task exists:** M3 GATE-PASS (AF-108 DoD) explicit input. Validates that the 256×128 canonical framebuffer (ADR-002) correctly maps across all 4 physical seam boundaries. Defect found here = Nano crop split bug OR controller port/row mapping bug.
**Prerequisites:** COMPLETED: AF-102 (WF4 first-light) OR AF-103 (ESP32 first-light), plus AF-101 Nano split working.
**Blocked by:** AF-102 (WF4) OR AF-103 (ESP32)
**Required hardware:** Passing controller path (WF4 4-panel or ESP32 4-panel) running, Nano connected.
**Required tools:** Camera with close-up mode.
**Required software:** Nano test pattern generator: produces the 4 specific seam test frames 256×128.
**Exact execution steps:**
1. **(a) VERTICAL seam x=128, both rows independently:**
   a. Top-row vertical seam (panels #1↔#2): gradient across 256 → close-up photo top-row seam region. Check smooth. Then 1px vertical line at x=128 → close-up top-row. Then "TOP SEAM X-128" text straddling → close-up top-row.
   b. Bottom-row vertical seam (panels #3↔#4): same 3 sub-tests (gradient, 1px line, text) → close-up photo of bottom-row seam region for each.
   Result photo: 1 combined close-up of vert seam top row + 1 combined of vert seam bottom row (2 photos total for check (a), or 6 individual; executor's discretion but 2 combined = minimum).
2. **(b) HORIZONTAL seam at y=64 (top-row bottom edge ↔ bottom-row top edge):**
   a. Render 1px HORIZONTAL white line at y=64, all x=0..255, black background. It sits exactly on the boundary: top-row panels show bottom pixel row (y=63+1? no — at y=64, this line is physically rendered by BOTTOM-ROW panels as y=0 of bottom-row crop). So line appears on bottom edge of top-row physical panels' LAST row (if Nano crop is correct: top-crop up to y=64 exclusive, line y=64 is in bottom-crop at y=0). Close-up of horizontal seam full width, confirm the 1px line is visible and runs CONTINUOUSLY straight across all 4 panels (top-row bottom / bottom-row top physical seam region).
   b. Render vertical gradient: y=0 black → y=127 white. Close-up at y=64 seam center. Should be mid-gray continuous across horizontal physical seam (top ↔ bottom row gap = mechanical only, content mapping smooth).
   c. Render large bold white text "HORIZ SEAM Y-64" positioned so its bounding box spans y=54..y=74 (10 px above, 10 px below the seam). Close-up: top half of letters on top-row panels, bottom half on bottom-row panels — no visible discontinuity or split beyond mechanical gap.
   Result photo: 1 close-up composite of horizontal seam (line OR gradient OR text — executor picks the most diagnostic one; all 3 tested).
3. **(c) BOTH SEAMS SIMULTANEOUS (cross check at center point (128,64)):**
   a. Pattern: White diagonal line from pixel (120,60) to pixel (136,68), thickness 1 px, on black background. This line: (1) crosses vertical x=128, (2) crosses horizontal y=64, (3) enters at panel #2 top-right quadrant area → exits at panel #3 bottom-left area (panels #2 and #3 are opposite corners of the 2×2 grid) — all 4 panels get a segment of the line.
   b. Alternative: Draw a white circle r=16 px centered at (128,64) on black background. The circle crosses all 4 quadrants and both seams.
   Result photo: 1 close-up photo at the display center (all 4 panel corners in frame) showing the diagonal or circle continuous across all 4 quadrants / 2 seams.
**Expected result:** (a) 2 vertical seams: gradients smooth, 1px lines continuous, text straddles. (b) Horizontal seam: 1px line continuous across 256 px, gradient mid-gray smooth across seam, text split clean. (c) Diagonal or circle at center crosses all 4 panels + both seams without discontinuity beyond mechanical gaps.
**Acceptance criteria / DoD:**
- (a) Vertical seam top-row (x=128): gradient smooth, 1px line continuous, text straddles.
- (a) Vertical seam bottom-row (x=128): gradient smooth, 1px line continuous, text straddles.
- (b) Horizontal seam (y=64): 1px horizontal line continuous across 256 px (full width), vertical gradient smooth at seam, text straddles.
- (c) Both seams simultaneous (center): diagonal line or circle = continuous mapping across all 4 quadrants, no discontinuity.
- 4 close-up photos saved (as per Evidence list): vert-top, vert-bot, horiz, both-simultaneous.
**Evidence to save:**
- `docs/photos/m3-seams/seam-vert-toprow-closeup.jpg`
- `docs/photos/m3-seams/seam-vert-botrow-closeup.jpg`
- `docs/photos/m3-seams/seam-horiz-y64-closeup.jpg`
- `docs/photos/m3-seams/seam-both-center-closeup.jpg`
- `docs/pm/evidence/AF-104-m3-seams-notes.md` (per-check pass/fail, observations, notes on mechanical vs content gaps)
**Safety considerations:**
Energized display observation only. Do NOT move panels or touch wiring during the test.
**Known uncertainties:**
The horizontal 1px line at y=64 is particularly sensitive to crop convention (inclusive/exclusive). Pillow `crop(box)` upper bound is exclusive; top-crop (0,0,256,64) → y=64 not included → line goes to bottom-crop at y=0 of bottom row. If panels are physically aligned, the line will appear just below the midpoint gap between top-row bottom and bottom-row top. This is CORRECT behavior; verify mapping, not mechanical zero-gap.
**Failure response:**
Any seam fail → check: (1) Nano crop coordinates (print crop tuple values to log and compare with pixel positions). (2) Controller row/port mapping: is X1/controller#1 truly the physical TOP row? (swap if inverted). (3) Per-row panel chain order (AF-102/103 config). Fix config, re-send patterns, re-photo.
**Source references:**
- JIRA Milestone 3: "arbitrary content crossing both horizontal and vertical physical seams"
- AF-086/AF-090 (M2 vertical seam check procedure, 1 seam style baseline)
- ADR-002 canonical framebuffer (crop split correctness validated here)
**Labels:** validation critical-path yes blocked blocked:exp-af-102 blocked:exp-af-103
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-105: EXP-017 Row synchronization sanity test. Update both rows as fast as possible. Take video recording of 10 updates of high-contrast full-frame flip. Measure max inter-row delta: acceptable if "no tearing worse than dashboard-tolerable" which means any 2 consecutive row updates are within 2 seconds. We're not doing video wall sync; just "not noticeably misaligned when you look at it."

**Task ID:** AF-105
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** EXP-017 row sync sanity: 10 full-frame flips, video record, max inter-row time-delta ≤ 2 s dashboard-tolerable.
**Description:** M3 Milestone JIRA: "synchronized-enough row updates". Dashboard use case: content changes infrequently (clock minute-tick, weather update, album art change). We do NOT need genlock. We need: top row and bottom row update within 2 seconds of each other so viewer doesn't see the top half flip while bottom half is still stale. Test: send 10 alternating high-contrast frames (full-frame black ↔ full-frame checkerboard 32-px tile). Video record the process. Frame-by-frame the video: max time delta between top-row flip and bottom-row flip for any of 10 iterations. Pass if max delta ≤ 2 s.
**Why this task exists:** M3 GATE-PASS (AF-108) DoD line: "Row sync within dashboard tolerance." Validates multi-controller (or multi-X-port WF4) row update ordering is acceptable for the actual use case. Avoids spending engineering time on true sync when it's unnecessary.
**Prerequisites:** COMPLETED: AF-101 (Nano transport dispatch to both rows works, AF-102 or AF-103 first-light passes).
**Blocked by:** AF-101
**Required hardware:** Display running, Nano controlling both rows.
**Required tools:** Camera video mode (smartphone OK), timer / video playback frame-step.
**Required software:** Nano test script: loop 10 iterations: send frame A, sleep 2 s, send frame B, sleep 2 s. (Frame A = all 256×128 black. Frame B = 32-px checkerboard with sharp white/black tiles.) Transport send for each frame = top-crop + bottom-crop dispatched as fast as possible sequentially (minimize gap; script calls back-to-back without intervening sleeps).
**Exact execution steps:**
1. Start camera video recording (30 fps or 60 fps preferred for timing resolution).
2. Run Nano test script (10 iter A→B→A→B→…).
3. Stop recording after all 10 iterations complete + 5 s.
4. Play back video. For each of the 10 frame transitions (t1..t10):
   a. Frame where TOP ROW first shows new pattern = timestamp T_top.
   b. Frame where BOTTOM ROW first shows new pattern = timestamp T_bot.
   c. Delta = abs(T_top - T_bot). Record delta for each of 10 transitions.
5. max_delta = max(d1..d10).
**Expected result:** max_delta ≤ 2.0 s for all 10 transitions. Typical expected: well under 0.5 s if transport is near-simultaneous.
**Acceptance criteria / DoD:**
- 10 transitions completed.
- For EACH transition: row update time delta ≤ 2.0 s (dashboard-tolerable). Do NOT average; the max is the bottleneck.
- max_delta value explicitly recorded ≤ 2.0 s.
- No visible case where user could say "wow the two halves updated way apart" — video evidence for review.
**Evidence to save:**
- `docs/videos/m3-row-sync-10flips.mp4` (or local path if git LFS not used; at minimum record filename + location).
- `docs/pm/evidence/AF-105-row-sync-deltas.md` (10-row table T_top, T_bot, delta; max_delta line; pass/fail line).
**Safety considerations:**
N/A.
**Known uncertainties:**
WF4 internal 2-port send: if X1 and X2 are dispatched by Huidu internal controller, delta may be sub-100 ms (excellent). Multi-ESP32 UART: 2 serial writes back-to-back = delta ~ tens of ms. Either should pass easily. Wi-Fi TCP transports may have higher jitter — if ADR-017 selected Wi-Fi TCP, verify max still ≤ 2 s.
**Failure response:**
max_delta > 2 s → investigate: (1) Is the Nano script sending crops sequentially with blocking operations? If so, dispatch the 2 transport sends in parallel (asyncio.gather on 2 coroutines) instead of sequential await. (2) Is one controller dropping frames? Retransmission logic adding delay? Add send confirmation timeouts. Re-run after optimizations.
**Source references:**
- JIRA Milestone 3 requirement: "synchronized-enough row updates" (explicit wording of "no tearing worse than dashboard-tolerable" — same definition here)
- ADR-002 transport layer (row dispatch performance)
- DECISIONS.md ADR-009 + ADR-017 transport selection (may determine delta magnitudes)
**Labels:** validation software critical-path yes blocked blocked:exp-af-101
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-106: EXP-017 Coordinate grid + labels. Render full 256×128 black canvas with: (a) 1px white grid every 32px; (b) corner labels TL=(0,0), TR=(255,0), BL=(0,127), BR=(255,127); (c) panel boundary labels: "Panel1 Left x=0" "Seam x=128" "Panel2 Right x=255" repeated on both rows; (d) horizontal seam label "Seam y=64". Verify all labels are on the correct physical panel corners/edges.

**Task ID:** AF-106
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** EXP-017 256×128 coordinate grid + corner + boundary label rendering. Verify each label lands on correct physical panel edge/corner.
**Description:** Full coordinate system validation. Render a single 256×128 frame containing: 32 px grid, 4 corner labels with (x,y) coordinates, vertical seam labels (per row), and horizontal seam label. Then WALK AROUND the physical display and verify each label appears on the CORRECT physical panel corner/edge. E.g., label "TL=(0,0)" must appear on physical panel #1 top-left LED pixel. Label "Panel2 Right x=255" on top row must appear on panel #2 right edge; same label on bottom row on panel #4 right edge.
**Why this task exists:** Ultimate "is the framebuffer mapping right?" test. Combines all 4 seam validations and corner mapping into one visual reference image. This is the canonical reference frame you'd use for frame mechanical alignment later. M3 GATE-PASS (AF-108) references these labels indirectly via seam requirements, but this task explicitly confirms every panel boundary.
**Prerequisites:** COMPLETED: AF-104 (seams pass — mapping mostly correct; this task catches any remaining corner / edge off-by-ones).
**Blocked by:** AF-104
**Required hardware:** Display running, Nano with pattern generator.
**Required tools:** Camera (overview photo + close-ups of each corner).
**Required software:** Nano render script producing the coordinate grid frame.
**Exact execution steps:**
1. Render the 256×128 frame:
   a. Background = solid black.
   b. Grid: 1 px white lines every 32 px. Vertical lines at x ∈ {0,32,64,96,128,160,192,224,255}. Horizontal lines at y ∈ {0,32,64,96,127}. (Note: x=0 and x=255 and y=0 and y=127 are edges so they may appear as 1-px edge only.)
   c. Corner labels (white 12-px text, corner-aligned inward from corner by 2 px):
      - TL: text "(0,0)" aligned top-left at (2,2)
      - TR: text "(255,0)" aligned top-right at (255-text_w-2, 2)
      - BL: text "(0,127)" aligned bottom-left at (2, 127-text_h-2)
      - BR: text "(255,127)" aligned bottom-right at (255-text_w-2, 127-text_h-2)
   d. Vertical seam boundary labels (top-row = y ∈ [26,40], bottom-row = y ∈ [26+64, 40+64] = [90,104]):
      - On each row, 3 labels: "Panel1 Left x=0" (left-justified near x=0..50), "Seam x=128" (centered at x=108..148, straddling seam), "Panel2 Right x=255" (right-justified near x=205..255).
   e. Horizontal seam label "Seam y=64": bold text centered both horizontally and vertically across the seam. Bounding box y ∈ [54,74] so text straddles.
2. Send frame to display.
3. Verify each label physically:
   a. "(0,0)" → appears on Panel #1 top-left corner LED pixels? Y/N.
   b. "(255,0)" → Panel #2 top-right? Y/N.
   c. "(0,127)" → Panel #3 bottom-left? Y/N.
   d. "(255,127)" → Panel #4 bottom-right? Y/N.
   e. Vertical "Seam x=128" top-row → on the seam between panel#1 and #2? Y/N.
   f. Vertical "Seam x=128" bottom-row → on seam between #3 and #4? Y/N.
   g. Horizontal "Seam y=64" → on seam between top-row (#1/#2) and bottom-row (#3/#4)? Y/N.
4. Take overview photo + per-corner 4 close-ups.
**Expected result:** All 7 label placement checks pass. Every label is on the correct physical panel edge/corner. Grid lines visible.
**Acceptance criteria / DoD:**
- 4 corner labels all on correct physical panel corners.
- 2 vertical seam labels (top-row x=128, bottom-row x=128) correct.
- 1 horizontal seam label (y=64) straddles correct seam.
- Overview photo of full 2×2 grid + corner 4 photos saved.
**Evidence to save:**
- `docs/photos/m3-grid/grid-overview-4panel.jpg`
- `docs/photos/m3-grid/corner-tl-0-0.jpg`
- `docs/photos/m3-grid/corner-tr-255-0.jpg`
- `docs/photos/m3-grid/corner-bl-0-127.jpg`
- `docs/photos/m3-grid/corner-br-255-127.jpg`
- `docs/pm/evidence/AF-106-grid-label-checklist.md` (7 checks Y/N each)
**Safety considerations:**
N/A — observation only.
**Known uncertainties:**
Panel mechanical alignment on bench: labels may look slightly "off center" relative to physical plastic edges because panels have a bezel / pixel border. The DoD is pixel mapping correctness (label appears on correct panel not correct plastic-center).
**Failure response:**
Corner labels wrong panel → root cause = row OR column inverted. Corner (0,0) wrong = check panel#1 is the Nano (x=0,y=0) target (panel#1 = top-left). If "(0,0)" is on panel#4 (bottom-right) → BOTH rows AND columns reversed globally: invert the render (rotate 180 or flip axes) or swap all chain/panel orders. Fix mapping and re-verify.
**Source references:**
- Standard Test Pattern Suite (coordinate grid item #6)
- ADR-002 canonical framebuffer mapping
- M3 GATE-PASS coordinate alignment baseline
**Labels:** validation software critical-path yes blocked blocked:exp-af-104
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-107: EXP-017 Sustained stability ≥30 min. Full-screen alternating checkerboard 8×8 updates every 2 seconds. Document: CPU usage on Nano, controller temps (hand/IR), no blank/freeze/garble, row sync stays within tolerance.

**Task ID:** AF-107
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** EXP-017 sustained stability ≥30 min. Alternating checkerboard every 2 s. Document CPU, temps, no blank/freeze/garble, row sync OK.
**Description:** EXP-017 §Procedure long-run baseline for 4-panel. Full-screen stress pattern (8×8 checkerboard tile = high contrast, all pixel rows toggling frequently). Auto-update every 2 s (more aggressive than dashboard normal use). Run continuously ≥30 min. At intervals: record Nano CPU%, hand-test controller temps (WF4 case, or 2× ESP32), verify no visual anomalies, spot-check row sync delta (re-use AF-105 method for 1 spot check mid-run).
**Why this task exists:** M3 GATE-PASS (AF-108 DoD): "≥30 min stability." Stress-testing the 4-panel setup at higher-than-dashboard update rate catches thermal or memory issues before M4 scales to 6 panels.
**Prerequisites:** COMPLETED: AF-106 (mapping/grid verified; system working end-to-end).
**Blocked by:** AF-106
**Required hardware:** Full 4-panel system + Nano.
**Required tools:** Timer, camera (start/end photos), SSH to Nano for top/CPU readings, IR thermometer or hand, notepad.
**Required software:** Nano stability-script: loop checkerboard invert pattern every 2 s for ≥1800 s (30 min). Transport working.
**Exact execution steps:**
1. Start timestamp (T0). Record wall-clock.
2. Start script: checkerboard pattern (alternating black/white 8×8 tiles over 256×128), send full frame, invert all pixels every 2 s.
3. T0 + 10 min (T10):
   a. Nano CPU%: `top -bn1 | head -n 20` → record Python process CPU%.
   b. Controller temps hand test (WF4 plastic case / or both ESP32 metal cans): < 50C? (<60C pass, no burn-pain). Record.
   c. Visual: no blank, no freeze (pattern still inverts), no garbled rows. Record.
   d. Spot row sync (as AF-105, 1 iteration): delta ≤ 2 s. Record.
4. T0 + 20 min (T20): repeat T10 checks.
5. T0 + 30 min (T30): repeat T10 checks + take end-state photo.
6. Stop script. Compute: total uptime ≥ 1800 s.
**Expected result:** ≥30 min (1800 s) continuous runtime. No blank, no freeze, no garbled rows. Nano CPU not at 100% sustained (typically 5–30%). Temps within pass. Row sync delta still ≤ 2 s at T30.
**Acceptance criteria / DoD:**
- Total runtime ≥ 1800 s (wall-clock Tstart → Tend difference recorded).
- At T10, T20, T30: visual check = no blank, no freeze, no garble.
- Nano CPU% recorded at 3 points; no 100% sustained runaway.
- Controller temps 3 checks all < 60C (no hand burn).
- Row sync spot check at T30: delta ≤ 2 s (dashboard tolerance).
- Start photo + end photo saved.
**Evidence to save:**
- `docs/photos/m3-stability/start-t0.jpg`
- `docs/photos/m3-stability/end-t30.jpg`
- `docs/pm/evidence/AF-107-m3-stability-notes.md` (3 data points T10/T20/T30 each: CPU%, temp pass Y/N, visual status, row-sync delta; total runtime seconds; pass/fail line)
**Safety considerations:**
⚠️ PSU thermal at 30 min mark: hand-touch PSU chassis. If too hot to hold for 5 s (<60C = hold OK), monitor. IR thermometer preferred if available.
**Known uncertainties:**
- Nano CPU% depends on transport encoding; WF4 proprietary lib may do heavy processing (higher CPU%), ESP32 raw-framer UART may be lighter (lower CPU%). Either is fine as long as not 100% pinned.
**Failure response:**
Freeze / blank during run → immediately review serial logs or crash dump. Common causes: Nano OOM, controller watchdog reset, transport hang. Reduce update freq (every 5 s instead of 2) and re-run; if that passes, document "stable at dashboard rate, stress rate borderline" with note. Temps > 60C → stop run, monitor, check airflow / add a fan for M4 test (fan not required for V1 but for bench useful).
**Source references:**
- EXPERIMENTS.md EXP-017 §Procedure stability requirement (30 min M3; 1 hr M4)
- AF-085 (WF4 M2 30 min stability baseline)
- AF-089 (ESP32 M2 1 hr stability baseline for method)
- AF-105 row sync test method (re-used for spot checks)
**Labels:** validation thermal-review critical-path yes blocked blocked:exp-af-106
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-108: M3 GATE-PASS EXP-017 aggregator. DoD: 256×128 2×2 = 4 panels. Both vertical seams (x=128 in top and bottom rows) and horizontal seam (y=64) crossed correctly by test content. Row sync within dashboard tolerance. ≥30 min stability. Evidence: 4 seam photos + stability timestamp start/end.

**Task ID:** AF-108
**Epic:** AF-005 (M3: Four-Panel 256×128 EXP-017 — 🔒 GATED MILESTONE #4 of 6)
**Summary:** M3 GATE-PASS aggregator. DoD 5 bullets: 2×2 4 panels, both vertical seams correct, horizontal seam correct, row sync dashboard-tolerant, ≥30 min stable.
**Description:** M3 is gated milestone #4 of 6 per JIRA.md gated sequence. Review all AF-097..AF-107 evidence for the winning controller path (single critical path after reclassification sweep). Tick the 5 DoD bullets. If passing → M3 GATE = PASS. Commit evidence as a unit. M4 6-panel CANNOT start before this gate signs off.
**Why this task exists:** Formal gate exit. You CANNOT proceed to M4 6-panel work (Epic AF-006) without explicit gate sign-off.
**Prerequisites:** COMPLETED: AF-107 (stability) for the winning controller path. Reclassification sweep (AF-096) already applied so the critical path is single.
**Blocked by:** AF-107
**Required hardware:** None (doc review).
**Required tools:** None.
**Required software:** Evidence folders open.
**Exact execution steps:**
1. Gather evidence for the critical-path controller (winner ADR-016):
   (a) 4 seam photos from AF-104: vert-top, vert-bot, horiz, both-center.
   (b) Row sync deltas from AF-105 (max_delta ≤ 2 s recorded).
   (c) Stability timestamp from AF-107 (start/end ≥1800 s).
   (d) Grid from AF-106 (mapping correct).
2. Tick each DoD bullet:
   - 256×128 2×2 = 4 panels, works (first-light AF-102/AF-103 confirmed)?
   - Both vertical seams crossed correctly?
   - Horizontal seam crossed correctly?
   - Row sync dashboard-tolerable (max_delta ≤2 s)?
   - ≥30 min stable?
3. 5/5 ticks = PASS. Commit evidence + gate log.
4. 4/5 or less = FAIL → go back to source task.
**Expected result:** 5/5 pass. Gate pass log written + committed.
**Acceptance criteria / DoD:**
- 256×128 2×2 = 4 panels on critical-path controller.
- Vertical seams x=128 (top and bottom rows): gradient smooth / 1px line / text straddle all correct (per AF-104 photos evidence).
- Horizontal seam y=64 (top ↔ bottom rows): 1px line continuous across 256 px, gradient smooth, text straddles (per AF-104).
- Row sync max delta ≤ 2 s (AF-105 table).
- ≥30 min stability hold (AF-107 timestamp start/end documented).
**Evidence to save:**
- `docs/pm/evidence/AF-108-m3-gate-pass.md` (5 ticks, evidence hash list, commit hash, PASS statement)
- Git commit: `docs(m3): M3 GATE-PASS PASS — EXP-017 4-panel 256×128 on [controller].`
**Safety considerations:**
N/A — doc gate.
**Known uncertainties:**
None. Gate is binary pass/fail on documented evidence.
**Failure response:**
Any bullet fails → route back to specific failing source task, fix, re-run evidence, re-gate. DO NOT advance to M4.
**Source references:**
- JIRA Milestone 3 gated definition + DoD bullet list
- Epic AF-005 header exit criteria
- AF-091 (M2 gate) analogous procedure
**Labels:** validation gate-pass decision blocked blocked:exp-af-107
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-109: VERIFY Panels #5 and #6 polarity and HUB75 orientation (power OFF, retrieve from storage).

**Task ID:** AF-109
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** Re-verify panels #5 and #6 polarity, HUB75 IN/OUT, top-edge orientation (power OFF) as they leave storage for M4.
**Description:** Analogous to AF-081 (#2) and AF-097 (#3/#4) — now for the last 2 panels that complete the 2×3 = 6 grid. Retrieve panels #5 and #6 from anti-static storage bags onto ESD mat. Re-confirm each panel: 5 V V+/V- silk labels match M0 batch record, HUB75 IN/OUT labels present, orientation matches panels #1–#4 convention. Panels #5 and #6 will be the NEW 3rd bottom-row (#5 left, #6 right) in the 2 cols × 3 rows layout.
**Why this task exists:** JIRA Safety Rules: every panel's polarity re-verified immediately before it leaves storage for active wiring. 6-panel full-build is the maximum-current state; a polarity error on panel #5 or #6 at 6-parallel-branch would be the highest-current polarity error in the whole project.
**Prerequisites:** COMPLETED: M3 GATE-PASS (AF-108) — 4-panel system validated and signed off. Reclassification sweep (AF-096) = critical path single.
**Blocked by:** AF-108
**Required hardware:** Panels #5, #6; M0 AF-023 photos reference; anti-static mats.
**Required tools:** Multimeter continuity optional, camera.
**Required software:** None.
**Exact execution steps:**
1. PSU OFF, wall plug out → full OFF.
2. Panel #5 out of bag. Place face-up. Check top-edge orientation vs #1/3 (should match). Record.
3. Flip #5. Check 5 V V+/V- silk labels visible. Match to M0 photo. Check HUB75 IN/OUT labels. Optional continuity beep #5 V+ ↔ cap + pin, V- ↔ GND plane.
4. Repeat steps 2–3 identically for Panel #6.
5. Take 1 back photo each (#5, #6).
**Expected result:** #5 and #6: polarity match, HUB75 labeled, orientation match 1–4.
**Acceptance criteria / DoD:**
- Panel #5: polarity match, HUB75 labeled, orientation match. Back photo.
- Panel #6: polarity match, HUB75 labeled, orientation match. Back photo.
**Evidence to save:**
- `docs/photos/m4-panels/panel5-back.jpg`
- `docs/photos/m4-panels/panel6-back.jpg`
- `docs/pm/evidence/AF-109-panels5-6-reverify.md` (2 rows)
**Safety considerations:**
⚠️ Polarity verification is power-OFF only. PSU switch OFF AND wall plug REMOVED before probing. Multimeter in continuity mode (beep). Correct polarity = beep only on matching pairs (red-wire-harness-end ↔ panel-V+-pin, black-wire-harness-end ↔ panel-V--pin). Cross-pair continuity = V+ ↔ V- MUST NOT beep — beep means short, DO NOT energize, fix first.
**Known uncertainties:**
None; standard re-verify.
**Failure response:**
Mismatch → STOP, do NOT wire. Polarity flipped → swap BR-5/BR-6 end ferrules (red/black). Orientation flipped → label panel top edge and plan for mount-time rotation or software row rotation.
**Source references:**
- AF-081 (panel #2 re-verify pattern)
- AF-097 (panels #3/#4 re-verify pattern)
- AF-023 (M0 batch verify reference)
**Labels:** hardware polarity-verify validation blocked blocked:exp-af-108
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-110: WF4 M4 2×3 topology setup: X1→top-row (panes 1,2 chained), X2→mid-row (panes 3,4 chained — was bottom-row in M3), X3→NEW bottom-row (panes 5,6 chained). 6 parallel power branches (2 harnesses: 6 of 8 branches used; 2 spare labeled). Panels 5+6 chain + power branches #5 and #6.

**Task ID:** AF-110
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** WF4 M4 2×3 topology wiring: X1 top, X2 mid, X3 bottom — each 2 chained panels. 6 parallel power branches (BR-1..BR-6 = 6 of 8 BOM harnesses used; 2 spare).
**Description:** WF4 candidate final 6-panel 2×3 layout per ADR-011 (WF4 = 3 X-ports naturally map to 3 rows of 2). M3's "bottom-row" becomes M4's "mid-row" (panels #3,#4 shift logically). NEW 3rd bottom-row = panels #5,#6 chained on WF4 X3. All 3 rows chain = panel-odd (1,3,5) IN from controller X-port → OUT → panel-even (2,4,6) IN. Power: 6 parallel branches BR-1..BR-6, each to one panel V+/V- block (ADR-007 parallel distribution). BOM H-07 = 2 × 4-branch harnesses = 8 branches total. M4 uses 6 (BR-1..BR-6); the 2 unused branches (7 and 8 on whichever harness) are physically labeled "SPARE-1" and "SPARE-2" with tape, left unconnected at the panel end (their ferrule ends may remain screwed into the PSU V+/V- posts unused at the far end, or leave them unscrewed + cap the ferrule ends with heat-shrink to prevent accidental short against the chassis/bench). 6 of 8 branches used, 2 spare labeled. Power order strict.
**Why this task exists:** Final WF4 6-panel 2×3 topology. Implements EXP-006 §Procedure steps 1-2 and JIRA Milestone 4 "six independent parallel 5V panel power branches" + 3 256×64 rows. Sets up WF4 for M4 first-light (AF-113).
**Prerequisites:** COMPLETED: AF-109 (panels #5,#6 re-verify). COMPLETED: M3 WF4 system (AF-098/AF-102/AF-108) as starting point. BR-5 and BR-6 ferrules crimped both ends (if not → mini-task: ferrule crimp).
**Blocked by:** AF-109
**Required hardware:** WF4 controller, 6 panels (#1,#2 = top-row; #3,#4 = mid-row; #5,#6 = bottom-row), HUB75 cables (carry from M3 + 2 more: X3→panel#5, panel#5 OUT→panel#6 IN), BR-5 + BR-6 (new ferrule crimped), 2 harnesses total 8 branches, PSU A-200-5, C14.
**Required tools:** Ferrule crimper (if BR-5/6 pending), screwdriver PSU, multimeter, heat-shrink tubing + lighter or heat gun for spare ferrule capping.
**Required software:** None (wiring only).
**Exact execution steps:**
1. PSU OFF, wall plug OUT. Full OFF.
2. Physical 2×3 bench placement:
   - Row 1 (top):    Panel #1 (left)  Panel #2 (right)   ← was M2/M3 top-row.
   - Row 2 (mid):    Panel #3 (left)  Panel #4 (right)   ← was M3 bottom-row, now promoted mid.
   - Row 3 (bot):    Panel #5 (left)  Panel #6 (right)   ← NEW 3rd row.
   Equal row gaps and column gaps. Grid alignment check.
3. HUB75 chains:
   a. Row1: WF4 X1 → #1 IN → #1 OUT → #2 IN   (carry from M2/M3; reseat only if loose OFF).
   b. Row2: WF4 X2 → #3 IN → #3 OUT → #4 IN   (carry from M3 "X2=bottom"; now logically mid-row).
   c. Row3 (NEW): WF4 X3 → #5 IN → #5 OUT → #6 IN   (new HUB75 cables, notch engaged all).
4. Power branches 6 parallel (ADR-007):
   BR-1 → Panel #1 V+/V-  (carry)
   BR-2 → Panel #2 V+/V-  (carry)
   BR-3 → Panel #3 V+/V-  (carry M3 BR-3, was panel#3 bottom; now mid, same panel)
   BR-4 → Panel #4 V+/V-  (carry M3 BR-4)
   BR-5 → Panel #5 V+/V-  (NEW, Red=V+, Black=V-)
   BR-6 → Panel #6 V+/V-  (NEW)
   All 6 branches' V- ferrule ends → shared PSU V- post.
   All 6 branches' V+ ferrule ends → shared PSU V+ post.
   Screws all tight. 6-way parallel screw-down is OK (ADR-007: max parallel branches = 6 in V1).
5. Spare branches: identify which harness has the unused 7th and 8th branches (usually harness #2 branches 3+4 if we used harness #1 fully and harness #2 first 2 = #5,#6). Label each of 2 unused panel-end ferrules: tape "SPARE-1" and "SPARE-2". If the ferrule ends are exposed, slip heat-shrink sleeve over each + shrink to cap to prevent accidental bench short. PSU-side spare ferrules MAY remain in shared posts since they are parallel with other ferrules there and only one end is "hot" (panel end is capped).
6. Tape branch labels for #1..#6 if not already.
7. Visual inspection all screws tight, no stray strands, all 9 HUB75 plugs seated notch-engaged (3 rows × 3 connections each = Xp→odd + odd→even = 2 per row × 3 rows = 6 + 3 controllers to first panel = 9 total).
8. Power ON STRICT ORDER:
   a. Wall plug IN.
   b. C14 switch ON.
   c. Wait 3 s. Multimeter PSU posts: 5.00–5.10 V DC. Record.
   d. Panels 1..6 ALL not dark → power OK. WF4 boot.
**Expected result:** 2×3 layout correct, 3 WF4 X-ports drive 3 256×64 rows, 6 parallel branches used + 2 spares labeled, PSU 5.00–5.10 V, 6 panels not dark.
**Acceptance criteria / DoD:**
- Physical 2×3 alignment (2 cols × 3 rows). 6 panels.
- HUB75: 3 rows × 2-panel chain each; X1→row1, X2→row2, X3→row3. 9 plugs seated.
- 6 power branches (#1..#6) → 6 panels, parallel → PSU. Screws tight.
- 2 spare branches labeled "SPARE-1" / "SPARE-2". Exposed ends capped (if they remain in PSU posts).
- PSU voltage 5.00–5.10 V DC.
- 6/6 panels not dark.
**Evidence to save:**
- `docs/photos/wf4-m4/2x3-6panel-overview.jpg`
- `docs/photos/wf4-m4/psu-6branches-spares-labeled.jpg`
- `docs/photos/wf4-m4/psu-voltage-6panel.jpg`
- `docs/pm/evidence/AF-110-wf4-m4-topology-notes.md`
**Safety considerations:**
⚠️ No HUB75 hot-plug. Always power OFF controller + panels before connecting/disconnecting HUB75. Power ON order: 5V PSU → controller power → panel data. Power OFF order reverse: panels/controller sign OFF in software or idle → PSU switch OFF → unplug HUB75 only after full OFF. No hot-plug exceptions, not even for a quick test — hot-plug can destroy HUB75 shift-register outputs, HCT245 buffers, or ESP32/WF4 GPIO pins via transient overvoltage on data lines.
⚠️ 5 V high-current rule (JIRA Safety Rules 4/4). NO Dupont connectors or breadboard jumper wires for any continuous ≥2 A (40 W) 5 V path. Use screw terminals, ferrule crimps (18 AWG), or polarized locking Molex-style connectors. NEVER run full load current in series through a multimeter ammeter input — risk of burning out multimeter fuses or input protection and giving false readings from series voltage drop. Use clamp meter (if available) or measure voltage drop across known trace/connector resistance, or measure voltage at both ends of path under load and compute drop < 0.25 V per branch as pass criterion.
⚠️ 6-panel is MAX CURRENT state. Spare branches capped: yes, 2 spares labeled + ends capped.
**Known uncertainties:**
6-way parallel on single PSU post: if screw terminal can't physically clamp 6 ferrules + spares (8 total) → move spares to a 2nd set of V+/V- posts if PSU has multiple (many PSUs do). Otherwise, install a mini bus-bar or short Y-pigtail adaptor with ferrules. Max 4 ferrules per post → spares move to alternate posts if needed. Adapt at runtime.
**Failure response:**
Panels #5 or #6 dark → PSU OFF, check BR-5/6 screws and ferrules. Voltage out of range → trim / swap PSU. Spares hard to clamp → move spares as described. Chain wrong → reseat OFF.
**Source references:**
- EXPERIMENTS.md EXP-006 (WF4 full 6-panel 256×192) §Topology block diagram WF4 X1→top, X2→mid, X3→bot, X4 unused
- DECISIONS.md ADR-011 (WF4 primary candidate, X1→top, X2→mid, X3→bot)
- ADR-007 (parallel distribution; 6 parallel is max in V1)
- BOM.md H-07 2×4 harness = 8 branches total; M4 uses 6 of 8 + 2 spares labeled
- JIRA Milestone 4: six parallel 5 V panel power branches
**Labels:** hardware controller-wf4 wiring safety-review critical-path candidate-yes blocked blocked:exp-af-109
**Status flags:**
critical-path: candidate-yes
Conditional: no
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-111: ESP32 M4 2×3 multi-controller setup CONDITIONAL. Need 3rd ESP32 + HCT adapter build (reference AF-054..AF-065 procedure). Panels 5+6 bottom-row chain + power. controller#1 top, controller#2 mid, controller#3 bottom rows.

**Task ID:** AF-111
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** ESP32 M4 6-panel 2×3 setup CONDITIONAL. Need 3rd ESP32 + 3rd HCT adapter. Ctrl#1→top, ctrl#2→mid, ctrl#3→bottom. 6 parallel power. Skip for WF4.
**Description:** ESP32 candidate final 6-panel 2×3 (CONDITIONAL). Mirrors WF4 AF-110 but with 3 ESP32 + 3 HCT adapters (1 per row). Requirements: (a) Need 3rd ESP32 board on hand (BOM H-02 need qty=3 total). If qty still only 1 or 2 → BLOCKED purchase 3rd. (b) Need 3rd HCT245 perfboard adapter built = use AF-054..AF-065 12-stage procedure AGAIN (identical to AF-099 for adapter #2; adapter #3 repeat of same 12 stages, continuity 14 signals end-to-end confirmed). (c) Panel placement 2×3 same as AF-110. (d) Power: 6 parallel branches BR-1..BR-6, 2 spares labeled — same as AF-110. (e) Controllers: ctrl#1 HCT→row1, ctrl#2 HCT→row2 (carry from M3; was "bottom" in M3, now promoted to "mid"), NEW ctrl#3 HCT→row3 bottom = panel#5 IN→panel#5 OUT→panel#6 IN. Strict power order.
**Why this task exists:** Multi-ESP32 path scaling to 3 controllers × 3 rows = 6 panels final. EXP-014 feasibility measurement for ESP32 6-panel.
**Prerequisites:** CONDITIONAL (skip WF4 win). COMPLETED: AF-109 (panels 5/6 verify). COMPLETED: M3 ESP32 path (AF-100/AF-103/AF-108) — controller #1 and #2 built.
**Blocked by:** AF-109 + AF-100 (ESP32 adapter #2)
**Required hardware:** 3rd ESP32-S3 N16R8 board (BOM H-02 qty ≥ 3; if still <3 → BLOCKED purchase H-02 update), 3rd HCT adapter parts set (1× HCT245, 1× 100 nF, 1× box header, 1× perfboard, solder/flux), HUB75 cables for row3 (2 new: ctrl#3→#5, #5→#6), BR-5 + BR-6 power branches w/ ferrules, 6 panels and ctrls #1 and #2 carry from M3.
**Required tools:** Ferrule crimper, soldering iron, multimeter, screwdriver PSU, heat-shrink for spares.
**Required software:** None.
**Exact execution steps:**
1. CONDITION CHECK: ADR-016 winner = WF4 → APPLY SKIP. Else continue.
2. Sub-step (a): 3rd ESP32 count = ? If count <3 → BLOCKED. Update BOM.md H-02 qty from current (1 or 2) to 3. Order the missing quantity. Delivery → re-run AF-048 style identification for the new board (rev/dims/markings).
3. Sub-step (b): Build 3rd HCT245 adapter. OPEN AF-054..AF-065. REPEAT stages 1-12 IDENTICALLY for adapter #3. Tick each stage's DoD for adapter #3. Save evidence photos under `esp32-hct-adapter-3/` mirroring #1 and #2. After stage 12: continuity 14 HUB75 signals end-to-end 14/14 beeps, no cross-shorts.
4. Sub-step (c/d/e): Physical 2×3 placement IDENTICAL to AF-110 step 2. BR-5/6 + spares labeled/capped same as AF-110. Controller chain per row:
   a. Ctrl #1 HCT → row1 #1 IN → #1 OUT → #2 IN   (carry from M3 top-row).
   b. Ctrl #2 HCT → row2 #3 IN → #3 OUT → #4 IN   (carry M3 bottom-row, now mid; verify address/id mapping now mid = id=1).
   c. NEW Ctrl #3 HCT → row3 #5 IN → #5 OUT → #6 IN   (HUB75 + power).
5. Controllers power: #3 5 V same method #1 and #2 (PSU 5 V / USB). Ground common.
6. Power ON STRICT ORDER (same AF-110 step 8). PSU voltage: 5.00–5.10 V. 3 controllers boot. 6 panels not dark.
**Expected result:** 2×3, 3 ESP32 boot, 6 panels not dark, 6 power branches, 2 spares labeled capped.
**Acceptance criteria / DoD:**
- Skip if WF4 wins.
- Else 3 ESP32 boards on hand (count verified ≥ 3).
- Adapter #3: 12 stages DoD ticked + 14/14 HUB75 continuity.
- 6 panels powered, 6 branches, 2 spares labeled capped.
- 3 controllers boot.
- 6/6 panels not dark.
**Evidence to save:**
- `docs/pm/evidence/AF-111-esp32-adapter3-12stage.md` (adapter 3 12-stage checklist)
- `docs/photos/esp32-hct-adapter-3/` — mirror adapter 1/2 photos
- `docs/pm/evidence/AF-111-adapter3-14-continuity.md` (14 rows)
- `docs/photos/esp32-m4/2x3-6panel-overview.jpg`
- `docs/pm/evidence/AF-111-esp32-m4-notes.md` (voltage, 6 panels check)
- BLOCKED PURCHASE doc if applicable.
**Safety considerations:**
⚠️ No HUB75 hot-plug. Always power OFF controller + panels before connecting/disconnecting HUB75. Power ON order: 5V PSU → controller power → panel data. Power OFF order reverse: panels/controller sign OFF in software or idle → PSU switch OFF → unplug HUB75 only after full OFF. No hot-plug exceptions, not even for a quick test — hot-plug can destroy HUB75 shift-register outputs, HCT245 buffers, or ESP32/WF4 GPIO pins via transient overvoltage on data lines.
⚠️ 5 V high-current rule (JIRA Safety Rules 4/4). NO Dupont connectors or breadboard jumper wires for any continuous ≥2 A (40 W) 5 V path. Use screw terminals, ferrule crimps (18 AWG), or polarized locking Molex-style connectors. NEVER run full load current in series through a multimeter ammeter input — risk of burning out multimeter fuses or input protection and giving false readings from series voltage drop. Use clamp meter (if available) or measure voltage drop across known trace/connector resistance, or measure voltage at both ends of path under load and compute drop < 0.25 V per branch as pass criterion.
Solder task for adapter #3: hot iron, ventilation.
**Known uncertainties:**
3 controllers USB crowding. Need a 3-port USB hub or 3 separate USB cables. For transport: 3 distinct serial devices or 3 IPs.
**Failure response:**
BLOCKED purchase → insert purchasing task. Adapter #3 fails → re-solder / debug. 6 panels dark → power off, check BR-5/6, chain.
**Source references:**
- AF-054..AF-065 (12-stage HCT build procedure)
- AF-099 (adapter #2 build — analogous repeat for adapter #3)
- AF-110 WF4 6-panel topology (same panel/power, different controller)
- JIRA Milestone 4: six parallel 5 V branches
**Labels:** hardware controller-esp32 wiring soldering safety-review conditional critical-path candidate-yes blocked blocked:exp-af-109 blocked:exp-af-100 blocked:adr-016
**Status flags:**
critical-path: candidate-yes
Conditional: yes
Skip condition: Skip if ADR-016 selects WF4.
Reclassification rule: After ADR-016 Architecture Decision Gate completes, IF this task's controller LOST the decision → set Conditional=yes + add Skip condition='Skip: ADR-016 selected [other controller]' + critical-path=no + Labels add 'blocked conditional'. IF this task's controller WON → set critical-path=yes (from candidate-yes) + keep Conditional=no.

---

### AF-112: Nano full 256×192 framebuffer + 3-row crop split. top=256×64 y=0..63, mid=256×64 y=64..127, bot=256×64 y=128..191. Send to X1/X2/X3 (WF4) or ctrl#1/#2/#3 (ESP32). Software: extend AF-101's 2-row split to 3 rows.

**Task ID:** AF-112
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** Nano full 256×192 framebuffer (ADR-002 canonical). 3-row crop split: top/mid/bot each 256×64. Transport dispatch to 3 row controllers.
**Description:** ADR-002's FINAL canonical framebuffer resolution = 256×192. Upgrade Nano framebuffer from M3's 256×128 → 256×192. Extend AF-101's 2-row split to 3 rows. Pillow render to 256×192. Crop to top (y=0..63), mid (y=64..127), bot (y=128..191). Transport dispatch to the 3 respective row controllers (ports X1/X2/X3 on WF4; ids 0/1/2 on ESP32). Unit test crop pixel carry-over. E2E smoke send a 6-line text frame: lines 1-2 → top row, lines 3-4 → mid row, lines 5-6 → bottom row. Verify physical placement correct.
**Why this task exists:** ADR-002 complete implementation (256×192). JIRA Milestone 4: "Logical framebuffer: 256×192" and "Nano rendering arbitrary content anywhere on the complete display". All downstream M4 tasks depend on this 3-row split working.
**Prerequisites:** COMPLETED: AF-110 (WF4) OR AF-111 (ESP32) 6-panel topology + controllers configured. COMPLETED: AF-101 256×128 2-row split baseline.
**Blocked by:** AF-110 OR AF-111
**Required hardware:** Nano SSH reachable, 6-panel system powered.
**Required tools:** SSH.
**Required software:** Python (Pillow, transport library per ADR-017). AF-101's framebuffer code as starting point.
**Exact execution steps:**
1. Nano SSH. Edit display app.
2. Framebuffer: height 128 → 192. Buffer 256×192×3 = 147,456 B (≈144 KB). Fits Nano DDR3.
3. Row-split function refactor: change from row count = 2 to row count = 3, with row_height = 64. Generalize to N rows (so M3 code would still work with N=2; add optional param). For frame → crops:
   crops[0] = frame.crop((0, 0, 256, 64))      # top (y=0..63)
   crops[1] = frame.crop((0, 64, 256, 128))    # mid (y=64..127)
   crops[2] = frame.crop((0, 128, 256, 192))   # bot (y=128..191)
4. Unit test: crop shapes (256,64) each. Pixel carry: frame@(0,0)==crop0@(0,0); frame@(0,64)==crop1@(0,0); frame@(0,128)==crop2@(0,0).
5. Transport dispatch generalized to 3 rows:
   - WF4: port map crops[0]→X1, crops[1]→X2, crops[2]→X3.
   - ESP32: controller id crops[0]→id=0 (top), crops[1]→id=1 (mid), crops[2]→id=2 (bot).
6. E2E smoke: 6-line text frame at y positions [8,28, 72,92, 136,156] → physical 3 rows each get 2 lines. Observe on display.
**Expected result:** 256×192 framebuffer, 3 crops correct, transport dispatch 3-ways works, text on correct physical rows.
**Acceptance criteria / DoD:**
- Framebuffer height = 192. Buffer size = 147,456 bytes.
- 3 crops unit tests pass (shape + pixel carry).
- E2E smoke: lines 1-2 on physical top-row (#1,#2); lines 3-4 on mid-row (#3,#4); lines 5-6 on bot-row (#5,#6).
- Render code still controller-agnostic (no if WF4 / if ESP32 in renderer — transport layer encapsulates).
**Evidence to save:**
- `docs/pm/evidence/AF-112-framebuffer-256x192-code.diff`
- `docs/photos/m4-software/6line-test-3rows.jpg`
- `docs/pm/evidence/AF-112-3row-crop-test.md` (unit test assertions)
**Safety considerations:**
N/A — software.
**Known uncertainties:**
Crop off-by-one y=128 (Pillow exclusive upper-bound). E2E photo catches inversion.
**Failure response:**
Wrong content on wrong row → transport port/id mapping (WF4 X1/X2/X3 string order, or ESP32 ctrl ids 0/1/2 mapping). Crop overlap → debug crop tuples.
**Source references:**
- DECISIONS.md ADR-002 (Canonical 256×192 framebuffer with transport abstraction) — FINAL implementation now
- AF-101 (256×128 2-row split baseline; extend to 3 rows)
- JIRA Milestone 4: "Logical framebuffer: 256×192" + "arbitrary text anywhere on the complete display"
**Labels:** software nano critical-path yes blocked blocked:exp-af-110 blocked:exp-af-111
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-113: First-light 256×192 all 6 panels. Standard test patterns full resolution.

**Task ID:** AF-113
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** M4 first-light full 256×192 6-panel. Standard Test Pattern Suite end-to-end.
**Description:** EXPERIMENTS.md §Standard Test Pattern Suite executed on the COMPLETE 256×192 2×3 = 6-panel system (critical-path controller). Patterns: solid fills (R/G/B/W/black), checkerboard full canvas, horizontal+vertical gradients, vertical lines every 16 px, coordinate grid + corner labels. Compare against §Standard Defect Checklist. Observe: 6 panels respond, all 3 rows update, color correct, no repeated rows, no row duplicated half, WF4 X3 / ESP32 controller #3 bottom row works.
**Why this task exists:** EXP-006 §Success Criteria baseline (WF4 6-panel) or EXP-012 6-row equivalent (ESP32). First end-to-end proof the full 256×192 canvas renders. Feeds directly into full seam verification (AF-114) and M4 GATE-PASS (AF-119).
**Prerequisites:** COMPLETED: AF-112 (3-row framebuffer + transport working). COMPLETED: AF-110 OR AF-111 topology.
**Blocked by:** AF-112
**Required hardware:** Full 6-panel critical-path system.
**Required tools:** Camera.
**Required software:** Nano pattern generator (Standard Test Pattern Suite adapted to full 256×192) OR controller-side Huidu software (WF4) sending full 256×192 image.
**Exact execution steps:**
1. Send each pattern from the Standard Test Pattern Suite (scaled to full 256×192):
   a. Solid fills: red, green, blue, white, black. 5 photos.
   b. Checkerboard 8×8 tiles full 256×192. Photo.
   c. Horizontal gradient x=0→255, 0→255 full width.
   d. Vertical gradient y=0→191 full height.
   e. 1 px vertical lines every 16 px (16 lines total across full 256 width × 192 height).
   f. Coordinate grid with corner labels: TL=(0,0), TR=(255,0), BL=(0,191), BR=(255,191).
2. After each pattern: wait 5 s settle, take photo, check against §Standard Defect Checklist items: wrong color order? repeated/missing rows? duplicated half/reversed order? ghosting/flicker? missing pixels/stuck rows? refresh instability/controller reset? Record defects per pattern if any.
**Expected result:** All patterns render correctly across all 6 panels. No defects in checklist.
**Acceptance criteria / DoD:**
- Solid fills all 5 uniform across 6 panels.
- Checkerboard correct 2×3 layout.
- Horizontal gradient smooth all width; vertical gradient smooth all 192 rows (top→bottom).
- Vertical lines every 16 px visible.
- Grid + corner labels: (0,0) on #1 TL; (255,0) on #2 TR; (0,191) on #5 BL; (255,191) on #6 BR.
- Standard Defect Checklist: zero observations (no wrong color, no missing rows, no duplicated half, no ghosting severe, no missing pixels, no reset).
**Evidence to save:**
- `docs/photos/m4-firstlight/` directory = solid-*.jpg (5), checkerboard.jpg, gradient-horiz.jpg, gradient-vert.jpg, vert-lines-16px.jpg, grid-corners.jpg
- `docs/pm/evidence/AF-113-m4-firstlight-defect-checklist.md` (6 checklist rows all clear, or defect list if any)
**Safety considerations:**
Full-6-panel white is MAX CURRENT state. After white pattern test, return to black or lower brightness before proceeding (reduce load).
**Known uncertainties:**
Bottom row (row3, panels #5,#6) = newest chain; most likely to show chain order / config errors if any.
**Failure response:**
Defects: row duplicated half → chain length config (WF4: X3 set to chain 2; ESP32 ctrl#3 firmware chain=2). Wrong corner mapping → transport row/port map swap (swap X2/X3 string, or ctrl ids 1/2). Color wrong → color order setting. Reset → firmware watchdog / thermal.
**Source references:**
- EXPERIMENTS.md §Standard Test Pattern Suite, §Standard Defect Checklist
- EXPERIMENTS.md EXP-006 (WF4 6-panel §Procedure steps 2-3, §Success Criteria)
- AF-102 / AF-103 (M3 4-panel first-light analogous)
**Labels:** validation critical-path yes blocked blocked:exp-af-112
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-114: M4 full seam verification. 5 seams total: (a) vertical in top row x=128; (b) vertical in mid row x=128; (c) vertical in bot row x=128; (d) horizontal seam y=64 (top↔mid); (e) horizontal seam y=128 (mid↔bot). All 5: gradient smooth; 1px line continuous; content straddling test passes each seam. Evidence: 1 close-up photo per seam (5 total).

**Task ID:** AF-114
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** M4 6-panel seam verification: 3 vertical (rows 1/2/3 × x=128) + 2 horizontal (y=64, y=128) = 5 seams total. Each: gradient, 1px line, text straddle. 5 close-up photos (1 per seam).
**Description:** Full seam coverage for 2×3 = 6-panel layout. 3 vertical seams (one per row at x=128) and 2 horizontal seams (top↔mid at y=64, mid↔bot at y=128). Each seam gets the 3-part seam test: (i) gradient smooth across seam; (ii) 1px line continuous at seam boundary (vertical seam → vertical line at x=128; horizontal seam → horizontal line at y=boundary); (iii) text straddling. 5 seams × 3 sub-tests = 15 checks. Evidence: 1 close-up photo per seam = 5 photos, each showing seam-region view with all 3 sub-tests visible (composite or best diagnostic — executor's choice).
**Why this task exists:** M4 GATE-PASS (AF-119) DoD line: "All 5 seams correct (3 vert + 2 horiz)." JIRA Milestone 4: "correct crossing of every physical seam." Validates every panel-to-panel boundary of the final 6-panel layout.
**Prerequisites:** COMPLETED: AF-113 (full 256×192 first-light passes, all panels mapping correctly).
**Blocked by:** AF-113
**Required hardware:** Full 6-panel display running.
**Required tools:** Camera close-up mode.
**Required software:** Nano pattern generator producing per-seam test frames.
**Exact execution steps:**
1. Seam (a) TOP ROW vertical x=128 (panel #1 ↔ #2): gradient across 256 → close-up; 1px vertical line at x=128 → close-up; "TOP-SEAM-X128" text straddle → close-up. Composite seam (a) photo saved.
2. Seam (b) MID ROW vertical x=128 (#3↔#4): same 3 sub-tests. Photo.
3. Seam (c) BOT ROW vertical x=128 (#5↔#6): same 3 sub-tests. Photo.
4. Seam (d) HORIZONTAL y=64 (top-row ↔ mid-row): vertical gradient (full 0→191) close-up at y=64 region; 1px horizontal line at y=64 full width (appears at bottom of top-crop / top of mid-crop boundary) close-up; text "HORIZ-SEAM-Y64" straddling y=54..74. Photo.
5. Seam (e) HORIZONTAL y=128 (mid-row ↔ bot-row): same 3 sub-tests (mid vs bot; line at y=128). Photo.
6. Each seam's result: pass (all 3 sub-tests) or flag.
**Expected result:** All 5 seams clean. 5 photos. 15 sub-tests pass.
**Acceptance criteria / DoD:**
- 3 vertical seams (rows 1,2,3 × x=128): all 3 have gradient smooth, 1px vertical line continuous, text straddling clean.
- 2 horizontal seams (y=64 and y=128): gradient smooth across row boundary, 1px horizontal line continuous across 256 width, text straddling.
- 5 total seam close-up photos saved.
- 15/15 sub-tests pass.
**Evidence to save:**
- `docs/photos/m4-seams/seam-a-vert-toprow-x128.jpg`
- `docs/photos/m4-seams/seam-b-vert-midrow-x128.jpg`
- `docs/photos/m4-seams/seam-c-vert-botrow-x128.jpg`
- `docs/photos/m4-seams/seam-d-horiz-y64-top-mid.jpg`
- `docs/photos/m4-seams/seam-e-horiz-y128-mid-bot.jpg`
- `docs/pm/evidence/AF-114-m4-5seams-checklist.md` (15 rows: 5 seams × 3 sub-tests each: pass/fail)
**Safety considerations:**
Energized observation only. No wiring touches.
**Known uncertainties:**
Horizontal lines y=64 and y=128: Pillow crop exclusive upper-bound → y=64 is in crop[1] at y=0; y=128 is in crop[2] at y=0. Visually they appear just below the mid row gap. This is the correct mapping (ADR-002 verified).
**Failure response:**
Any seam fail → check crop tuple and controller row/port mapping. Fix, re-send patterns, re-photograph. Vertical seam chain fail → panel chain per row config (AF-110/AF-111 firmware).
**Source references:**
- AF-104 (M3 4-panel seam procedure — analog; scale from 3 seams to 5 seams)
- AF-086 / AF-090 (M2 single vertical seam style baseline)
- JIRA Milestone 4: "correct crossing of every physical seam"
- M4 GATE-PASS (AF-119) DoD "All 5 seams correct"
**Labels:** validation critical-path yes blocked blocked:exp-af-113
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-115: M4 Brightness sweep. Full-white content at 25%, 50%, 75%, 100%. At each level record: visual acceptable? any flicker or panel unevenness? Note behavior at 100%.

**Task ID:** AF-115
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** M4 4-step brightness sweep (25%/50%/75%/100%) full-white content. Visual check flicker, panel unevenness, 100% behavior note.
**Description:** Pre-thermal sweep visual check. Step the full-6-panel content to solid uniform white at 4 brightness levels. Brightness defined at controller-side (WF4 brightness setting, or ESP32 OCR/PWM library setting 25% = 64 / 256 etc). At each level: let it stabilize 30 s, then visually inspect 6 panels for: (1) all panels visually look the same brightness (no one panel dimmer than others); (2) no flicker visible to naked eye; (3) color still pure white or slight warm (no unexpected color cast). At 100% specifically: note if panel edges or module power-connector area shows visible discoloration (very unlikely but check).
**Why this task exists:** EXP-015 thermal (AF-116) is the quantitative follow-up. This task is the qualitative pass-fail pre-check: if 100% brightness already causes visible flicker or severe panel unevenness, we note it before running thermal measurements. Feeds into AF-120 MR brightness ceiling (AF-120 rule: 100% OK → ceiling=100, else 75).
**Prerequisites:** COMPLETED: AF-113 full first-light patterns.
**Blocked by:** AF-113
**Required hardware:** Full 6-panel running, controller brightness configurable.
**Required tools:** Camera (photo each brightness level for later comparison).
**Required software:** Brightness control API (controller-specific) or Huidu brightness slider (WF4) or library setBrightness() (ESP32).
**Exact execution steps:**
1. Set full white content (R=G=B = 255 × brightness).
2. Brightness level 25% (OCR ~64/256 if 8-bit):
   a. Set. Wait 30 s.
   b. Visual: 6 panels uniform brightness? Y/N.
   c. Visual: flicker? Y/N.
   d. Photo.
3. Level 50%: repeat a-d.
4. Level 75%: repeat a-d.
5. Level 100%: repeat a-d. At 100% also: (e) check each panel near power connector for discoloration / hot-spot visual (1 min observation).
6. Back to 0% / black to cool.
**Expected result:** All 4 levels: uniform across 6 panels, no flicker (100% subtle PWM flicker may be visible to some; note but not necessarily fail). 100% no visual panel anomaly.
**Acceptance criteria / DoD:**
- 4 levels tested. 4 photos saved.
- Each level: 6-panel brightness uniform (no obviously dim panel).
- Flicker note: if present, level and severity recorded (not necessarily fail).
- 100% level: no visual panel anomaly.
**Evidence to save:**
- `docs/photos/m4-brightness/b-25pct.jpg`
- `docs/photos/m4-brightness/b-50pct.jpg`
- `docs/photos/m4-brightness/b-75pct.jpg`
- `docs/photos/m4-brightness/b-100pct.jpg`
- `docs/pm/evidence/AF-115-brightness-sweep-notes.md` (4 rows: uniformity Y/N, flicker severity, 100% anomaly Y/N, overall visual acceptability line)
**Safety considerations:**
⚠️ 100% white = ~138 W (6 × 23 W seller-rated max). PSU + wiring is at full design load. If anything smells / buzzes loudly → abort sweep and note in Failure response. Run 100% no more than strictly necessary for this sweep and AF-116 thermal.
**Known uncertainties:**
WF4 brightness setting location varies by firmware version / model. ESP32 library brightness API name varies.
**Failure response:**
Obvious dim panel at any level → check power branch #N screw tightness / ferrule (power OFF first, then re-tighten). Flicker severe at 100% → reduces brightness ceiling in AF-120 to 75% if thermal otherwise OK. Smell / buzz → abort, skip 100% thermal, ceiling cap 75%.
**Source references:**
- EXPERIMENTS.md EXP-015 §Procedure brightness levels (25/50/75/100%)
- AF-116 EXP-015 (quantitative thermal next task)
- AF-120 MR ceiling analysis (uses this sweep + AF-116 data)
**Labels:** validation visual-check critical-path yes blocked blocked:exp-af-113
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-116: EXP-015 PSU Loaded Thermal (4 brightness levels × measurements). Embed the MEASUREMENT TABLE directly in Exact execution steps (4 levels × 4 measurements each). Levels: 25%, 50%, 75%, 100%. Measurements per level: (a) PSU 5V output voltage AT PSU terminals (should be 5.00-5.10V); (b) 5V voltage AT FARTHEST PANEL connector (panel #6 power branch — should be ≥4.75V); (c) PSU chassis temp (hand test or IR — should be <60C); (d) wiring/connector temp at PSU 5V output posts and at panel #6 connector (hand test <50C no pain); (e) Controller stability (no blank/restart); (f) Display visual (no brown/dim region, no flicker). Run 5 min per brightness level, record readings in table.

**Task ID:** AF-116
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** EXP-015 PSU Loaded Thermal test 4-brightness × 6 measurements table. 5 min per level. Full PASS → all voltages within range, temps < limits.
**Description:** EXPERIMENTS.md EXP-015 §Procedure VERBATIM quantitative implementation. Representative content per level = uniform full white at 25% / 50% / 75% / 100% (staircase up, 5 min each). Per-brightness-level SIX measurements (a–f) as defined in the task summary. The measurement table is embedded in both the execution steps (fill-in) and the Evidence (filled copy). The table is the primary output of this task. Voltage drops: drop = PSU V − panel#6 V. Drop per single branch should be < 0.25 V at 100% (6-panel parallel drop is shared at PSU, but the farthest panel is worst case).
**Why this task exists:** M4 GATE-PASS (AF-119) DoD line: "EXP-015 thermal all 4 levels PASSED (all voltages within tolerance, temps within limits)". ADR-006 centralized 200 W PSU acceptance test (§Consequences: "PSU must still pass loaded-voltage, thermal, extended-runtime testing"). Produces the AF-120 MR brightness ceiling data.
**Prerequisites:** COMPLETED: AF-115 (qualitative sweep passed or at least 4 levels testable). Multimeter ready. IR thermometer optional; hand test acceptable.
**Blocked by:** AF-115
**Required hardware:** Full 6-panel energized. Panel #6 power branch accessible for voltage probing at the connector end.
**Required tools:** Multimeter (DC V 20 V range). IR thermometer optional (or hand). Stopwatch 5 min. Notebook or tablet for table fill-in.
**Required software:** Brightness control (WF4 slider or ESP32 library setBrightness).
**Exact execution steps:**
1. Prepare the EXP-015 measurement table in a notes file (6 columns × 5 rows = 4 levels + header + totals). Table template:
```
| Brightness Level | (a) PSU V (terminals) V | (b) Panel#6 V (far end) V | (c) PSU chassis °C (hand/IR) | (d) Wiring temp PSU+#6 (hand) | (e) Ctrl stability Y/N | (f) Display visual Y/N |
|------------------|-------------------------|---------------------------|-------------------------------|-------------------------------|------------------------|------------------------|
| 25%              |                         |                           |                               |                               |                        |                        |
| 50%              |                         |                           |                               |                               |                        |                        |
| 75%              |                         |                           |                               |                               |                        |                        |
| 100%             |                         |                           |                               |                               |                        |                        |
```
2. Set display = full white. Brightness = 25%. Start timer = 5 min.
3. After 5 min at 25%, QUICKLY take all 6 readings (a)–(f) and fill row 1:
   (a) Multimeter DC 20V: probes at PSU V+ and V- output posts directly. Record V.
   (b) Multimeter DC 20V: probes at panel #6's 5 V connector (V+ and V- screws at panel end — the FARTHEST panel from PSU = worst drop). Record V.
   (c) PSU chassis temp: hand touch metal 5 s → "<60C (hold OK)" / "≥60C (too hot to hold)" — or IR thermometer °C if available.
   (d) Wiring temp: PSU output posts (plastic/metal near screws) hand test <50C = "no pain" / "yes pain", then panel #6 connector area same hand test. Record.
   (e) Controller stability: no blank, no watchdog reset, no garble in last minute? Y/N.
   (f) Display visual: no brown/dim regions, no flicker beyond AF-115 baseline? Y/N.
4. Repeat step 2–3 for brightness = 50% → row 2.
5. Repeat step 2–3 for 75% → row 3.
6. Repeat step 2–3 for 100% → row 4. At 100% WATCH FOR: (c)/(d) heat rising. If PSU chassis temp gets >60C (pain hold) → abort at that reading, note abort, proceed to AF-120 analysis with partial data.
7. Return display to black / 0% brightness for cool-down.
**Expected result:** All 4 rows filled. All voltages: (a) 5.00–5.10 V, (b) ≥4.75 V. All temps: PSU <60C, connectors <50C (no pain). Stability Y, Visual Y for all 4.
**Acceptance criteria / DoD:**
- 4×6 = 24 measurement cells all filled.
- Every row (a) = 5.00–5.10 V.
- Every row (b) ≥ 4.75 V (i.e., worst-case voltage drop max 0.35 V total; drop is acceptable as long as panel side ≥ 4.75 V).
- Every row (c): PSU chassis <60C (hand-hold OK) OR IR <60C.
- Every row (d): wiring PSU-side + panel#6-side <50C (no hand pain).
- Every row (e): stability Y (no blank/reset).
- Every row (f): visual Y (no brown/dim/flicker severe).
**Evidence to save:**
- `docs/pm/evidence/AF-116-exp015-thermal-table.md` (filled table, all 24 cells non-empty)
- `docs/photos/m4-thermal/meter-psu-100pct.jpg` (at 100% level, PSU voltage reading photo with meter display visible)
- `docs/photos/m4-thermal/meter-panel6-100pct.jpg` (panel#6 far-end voltage at 100% photo)
**Safety considerations:**
⚠️ 5 V high-current rule (JIRA Safety Rules 4/4). NO Dupont connectors or breadboard jumper wires for any continuous ≥2 A (40 W) 5 V path. Use screw terminals, ferrule crimps (18 AWG), or polarized locking Molex-style connectors. NEVER run full load current in series through a multimeter ammeter input — risk of burning out multimeter fuses or input protection and giving false readings from series voltage drop. Use clamp meter (if available) or measure voltage drop across known trace/connector resistance, or measure voltage at both ends of path under load and compute drop < 0.25 V per branch as pass criterion.
⚠️ Thermal at 100%: if PSU too hot to hold > 5 s, abort and record abort. Do NOT continue past pain threshold. Have a fire extinguisher nearby (habits).
**Known uncertainties:**
Hand-test temp thresholds: "<50C = no pain" and "<60C = hold OK 5 s" are well-established human skin-contact approximate thresholds. IR thermometer if available removes uncertainty.
**Failure response:**
100% level voltage (b) < 4.75 V → significant drop. Mitigations: (a) re-crimp ferrules on panel #6 branch BR-6 with larger ferrules / cleaner crimp; (b) shorten panel #6 branch wire length if possible. If still fails → ceiling to 75% + escalate. Temp exceed → ceiling to 75% in AF-120; consider fan or vent holes in eventual enclosure.
**Source references:**
- EXPERIMENTS.md EXP-015 §Procedure + §Success Criteria verbatim ("Stable voltage; no dangerous conductor/connector heating; no PSU shutdown or controller resets; acceptable PSU thermal behavior. If necessary, establish a software brightness ceiling below 100%.")
- DECISIONS.md ADR-006 §Consequences (PSU must pass loaded tests before FINAL acceptance)
- JIRA Milestone 4: "PSU load/thermal validation"
- M4 GATE-PASS (AF-119) DoD "EXP-015 thermal all 4 levels PASSED"
**Labels:** validation thermal thermal-review power critical-path yes blocked blocked:exp-af-115
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-117: EXP-016 Power Recovery ×5 cycles. Procedure: (1) Full dashboard content on all 6 panels. (2) PSU switch OFF → wait 10 seconds. (3) PSU switch ON. (4) Record Time-to-first-image (seconds from ON switch flip to first visible pixels on any panel). (5) Record Time-to-network-data (seconds from ON to Nano reconnects Wi-Fi and sends new content — indicated by e.g., clock widget tick or cached-fallback replaced). (6) Confirm content intact (no blank panels, no garbled rows). Repeat 5 cycles. Embed the 5-cycle × 2-timing-fields table in Evidence to save.

**Task ID:** AF-117
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** EXP-016 Power Recovery × 5 cycles. TTFI + TTND per cycle. Table: 5 rows × 2 timings. All 5 cycles: no blank panels, content intact.
**Description:** EXPERIMENTS.md EXP-016 §Procedure "Power recovery" section VERBATIM ×5 cycles. Per cycle: load dashboard-like content on full 6 panels (so content is non-trivial and we can detect garbled rows / missing panels). Hard PSU switch OFF. Wait 10 s for PSU caps discharge. PSU switch ON. Stopwatch: T0 = ON switch flip. Measure 2 timings: TTFI = seconds from T0 → first visible pixels light up on any panel (controller boot + power-up). TTND = seconds from T0 → Nano reconnects Wi-Fi / fetches new network content → the display updates past its cached fallback image (showing e.g., a new clock minute or fresh widget). After each cycle, inspect all 6 panels: content intact? No blank? No garbled rows? Y/N. Repeat 5. Table: 5 cycles × 2 timings.
**Why this task exists:** M4 GATE-PASS (AF-119) DoD: "EXP-016 recovery ×5 cycles all PASSED." JIRA Milestone 4: "repeated boot/recovery testing" + "unattended operation". MR (AF-120) computes worst-case TTFI and TTND = operational SLAs for the product.
**Prerequisites:** COMPLETED: AF-113 (first-light all panels work). COMPLETED: M4 Nano app shows dashboard-like content (clock widget or cached/fallback indicator — not a solid test pattern; we need network-data update indication).
**Blocked by:** AF-113
**Required hardware:** Full 6-panel system. Wi-Fi AP reachable (for TTND). PSU switch accessible for rapid flip.
**Required tools:** Stopwatch or timer app with ms or 0.1 s resolution. Notebook for table fill-in. Camera per cycle optional (or final cycle photo).
**Required software:** Nano app running dashboard-mode content with a network-updating widget (clock ticking per minute is fine; or weather widget). A clearly-different cached fallback image (e.g., grayscale or "CACHED" banner) so TTND is visible when cached fallback replaced with live content.
**Exact execution steps:**
1. Prepare the EXP-016 timing table (5 rows × 2 cols + notes):
```
| Cycle # | TTFI (s) | TTND (s) | Content intact after ON? Y/N | Notes |
|---------|----------|----------|------------------------------|-------|
| 1       |          |          |                              |       |
| 2       |          |          |                              |       |
| 3       |          |          |                              |       |
| 4       |          |          |                              |       |
| 5       |          |          |                              |       |
| worst   |          |          | —                            |       |
```
2. Display dashboard content on all 6 panels. Confirm visible network-dependent content (e.g., clock shows correct minute).
3. **Cycle 1:**
   a. PSU switch OFF. Start 10 s wait timer.
   b. After 10 s, PSU switch ON + START stopwatch T0 simultaneously (same hand action if possible).
   c. Watch display: when FIRST PIXELS illuminate (any panel lights up, even boot pattern) → stopwatch value = TTFI1, record row 1 TTFI.
   d. Keep watching: display initially shows boot/cached image; when Wi-Fi connects + Nano updates content (clock widget changed minute, or CACHED banner disappears, or known new content appears) → stopwatch value = TTND1, record row 1 TTND.
   e. Final check cycle 1: 6 panels all show content? No blank? No garbled rows? Intact Y/N → row 1.
   f. If system stable for ≥30 s after TTND → proceed to cycle 2.
4. **Cycles 2, 3, 4, 5:** repeat cycle 1 steps a-f identically. Each new cycle = PSU OFF → 10 s wait → ON → timings → intact check.
5. After 5 cycles, compute worst row: worst TTFI = max(1..5), worst TTND = max(1..5). Fill "worst" row.
**Expected result:** 5 cycles complete. All intact=Y. TTFI typically 5–30 s. TTND typically 15–60 s depending on Wi-Fi reconnect time.
**Acceptance criteria / DoD:**
- 5 cycles × (TTFI, TTND, intact) = 15 data cells all filled.
- All 5 cycles: Content intact = Y (6 panels all non-blank, no garbled rows).
- Worst-case TTFI recorded (numerical seconds).
- Worst-case TTND recorded (numerical seconds).
**Evidence to save:**
- `docs/pm/evidence/AF-117-exp016-power-recovery-table.md` (filled 5-row table + worst row; notes column)
- `docs/photos/m4-recovery/cycle-5-intact.jpg` (cycle 5 end-state: all 6 panels visible, optional photo)
**Safety considerations:**
⚠️ PSU ON/OFF cycling: 5 cycles. Each cycle PSU OFF for 10 s — allows PSU bulk caps discharge and panel caps discharge. Do NOT skip the 10 s wait (otherwise partial discharge can produce weird boot states). PSU switch OFF → wait → ON. Do NOT yank the wall plug (mechanical wear on C14 blades) — use the C14 integrated switch on the inlet per design.
**Known uncertainties:**
Wi-Fi AP behavior: reconnect times vary. If AP does DHCP lease renew slow, TTND may spike; this is OK, recorded as worst-case.
**Failure response:**
Cycle content-intact = N → diagnose: which panel(s) failed? If blank → that panel's branch loose? If garbled rows → controller flash / boot issue. Re-run the offending cycle (don't count failed cycles; count 5 successful ones). If EVERY cycle has an intact failure → escalate to dedicated debug task. If TTND > 2 minutes consistently → check Nano Wi-Fi config + AP signal strength inside bench location.
**Source references:**
- EXPERIMENTS.md EXP-016 §Procedure "Power recovery" VERBATIM ("disconnect mains 30 s → restore → record Nano boot time, controller boot, time to first useful image, time to network data. Repeat ≥5 times.")
- JIRA Milestone 4: "repeated boot/recovery testing", "unattended operation"
- M4 GATE-PASS (AF-119) DoD "EXP-016 recovery ×5 cycles all PASSED"
- MR AF-120 (uses worst TTFI/TTND as operational SLAs)
**Labels:** validation recovery-test power critical-path yes blocked blocked:exp-af-113
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-118: Wi-Fi Recovery + API Fail Simulation. (a) Wi-Fi: disable AP for 5 minutes (unplug router or change SSID on Nano). Wait. Re-enable AP. Verify: Nano auto-reconnects SSH; display eventually updates to current content (no manual reboot). (b) API fail: (simulate by unplugging router OR blocking outbound) → verify: app stays alive (no crash/segfault); display shows cached/fallback content (not blank); cached status indicator visible. Restore network. Verify: live content resumes without restart.

**Task ID:** AF-118
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** EXP-016 Wi-Fi Recovery + API Fail Simulation. Pass = Nano auto-reconnects, app survives API fail, cached fallback visible, live resume.
**Description:** EXPERIMENTS.md EXP-016 §"Wi-Fi recovery" + §"API failure" VERBATIM. Two independent tests (a) then (b). (a) Wi-Fi recovery: disable AP (unplug power to Wi-Fi router, or Nano-side ifconfig down). Leave disabled 5 min. Re-enable AP (router back on, or ifconfig up). Passive observation for 5 min. Verify: Nano SSH reachable (auto-reconnected). Display eventually transitions from cached fallback to current network content. No manual reboot. (b) API fail simulation: while display running, KILL network outbound (unplug router, or use Nano iptables/nftables to block destination of the API call). Wait 2 min. Expected behavior: (1) Nano app does NOT crash (no segfault / exception bubbled to top); (2) display is NOT blank — shows cached fallback content (last known good frame, or a static "Offline" template); (3) a visible "cached / offline" status indicator is present (e.g., banner at top-left, or icon). Restore network. Verify: within 30 s, display transitions from cached → live content with NO restart of the app.
**Why this task exists:** M4 GATE-PASS (AF-119) DoD: "Wi-Fi recovery PASSED. API fail simulation PASSED." JIRA Milestone 4: "Wi-Fi recovery." EXPERIMENTS.md EXP-016 §Success Criteria: "No manual intervention; recovers after power loss; Wi-Fi reconnects automatically; application restarts automatically; transient API errors do not blank or crash the display."
**Prerequisites:** COMPLETED: Nano app has a network API call (e.g., weather, calendar, clock NTP — even NTP or simple HTTPS to a known host is sufficient) AND has a cached-fallback renderer code path + visible status banner. COMPLETED: AF-117 (power recovery).
**Blocked by:** AF-117
**Required hardware:** Full 6-panel system. Wi-Fi router on the same network (access to its power plug for test (a)). Nano SSH reachable from a secondary non-Wi-Fi debug channel if needed (USB serial CH340 as backup — recommended).
**Required tools:** Stopwatch. Secondary debug access (USB serial to Nano) useful.
**Required software:** Nano app with: transport + render loop, network-client code with at least 1 API call, error-handler catch-all path that renders cached-fallback content, visible status-indicator overlay (banner or icon).
**Exact execution steps:**
1. **(a) Wi-Fi Recovery:**
   a. Baseline: confirm Nano SSH reachable via Wi-Fi IP. Display showing current network content. T0.
   b. Disable AP: unplug Wi-Fi router power plug. (Alternative method on Nano: `ifconfig wlan0 down` but router-side is more realistic of real-world outage).
   c. Wait 5 min (300 s). During wait: observe display → should show cached fallback content with offline/cached indicator visible. No blank.
   d. Re-enable AP: plug router back in.
   e. Passive observe for up to 5 min:
      i. From secondary USB-serial (if available): ping router gateway, watch wpa_supplicant + DHCP re-association log lines.
      ii. From Nano SSH: try to reconnect from another machine; when SSH works → mark T1 = auto-reconnect success time (seconds from re-enable).
      iii. On display: when cached-fallback banner disappears + live content visible → mark T2 = content-updated time.
   f. Outcome (a) pass if: SSH works without reboot, display eventually updates, no manual Nano power cycle needed. Total recovery time = router-on to content = usually 30–120 s.
2. **(b) API Fail Simulation:**
   a. Baseline: display live, SSH reachable to Nano.
   b. Simulate API fail: unplug router power (total network loss is easiest simulation; alternative = Nano iptables `-A OUTPUT -p tcp --dport 443 -j DROP` to block HTTPS outbound only).
   c. Wait 2 min (120 s). During this 2 min:
      i. Nano app PID still alive (check via `ps aux | grep python` from USB serial or secondary access — should be same PID).
      ii. Display NOT blank (shows cached/fallback frame).
      iii. Cached/offline status indicator visible on display.
   d. Restore network: plug router back in (or `iptables -D OUTPUT ...` remove the drop rule).
   e. Observe 30 s: display transitions to LIVE content (status indicator gone, widget updated). No app restart required (PID still same).
   f. Outcome (b) pass if all 4 sub-checks of step c + step e hold.
**Expected result:** (a) Nano Wi-Fi auto-reconnects, display updates without reboot. (b) App survives no crash, cached fallback visible, live content resumes without restart.
**Acceptance criteria / DoD:**
- (a) Wi-Fi: after disable + 5 min + re-enable → Nano SSH auto-reconnects within 5 min. Display updates to live content without manual reboot.
- (b) API fail 2 min:
  i. Nano app PID unchanged (no crash/restart).
  ii. Display NOT blank during outage (cached/fallback visible).
  iii. Cached/offline status indicator visible on display.
  iv. Network restore: live content resumes within 30 s, no app restart needed.
**Evidence to save:**
- `docs/pm/evidence/AF-118-wifi-apirecovery-notes.md` (test (a) narrative: T1 SSH reconnect seconds, T2 content-update seconds, outcome pass; test (b) narrative: PID unchanged Y/N, cached fallback Y/N, indicator visible Y/N, live resume within 30 s Y/N, outcome pass)
- `docs/photos/m4-recovery/wifi-outage-cached-fallback.jpg` (display showing cached banner during outage in test (a) or (b))
- `docs/photos/m4-recovery/network-restored-live.jpg` (live content after recovery)
**Safety considerations:**
⚠️ Do not yank the Nano's power to "simulate" Wi-Fi failure. Use the router-side disable method or Nano-side ifconfig. Power cycling the Nano during M4 tests is only for EXP-016 power cycles (AF-117).
**Known uncertainties:**
Wi-Fi reconnect time depends on router boot time (30–90 s) + Nano wpa_supplicant roam time. 5 min window should always be enough.
**Failure response:**
(a) Nano does not auto-reconnect SSH within 5 min → check: Wi-Fi interface state (`ip link wlan0`), `wpa_supplicant` running, systemd-networkd or NetworkManager enabled for auto-connect. Rebuild Nano network config. (b) App crashes on API fail → the error-handler code path is missing or incomplete. Wrap the network call in try/except. Add a global exception handler that renders cached frame. Re-test.
**Source references:**
- EXPERIMENTS.md EXP-016 §Wi-Fi recovery + §API failure VERBATIM
- JIRA Milestone 4: "Wi-Fi recovery"
- M4 GATE-PASS (AF-119) DoD "Wi-Fi recovery PASSED. API fail simulation PASSED."
**Labels:** validation recovery-test networking nano critical-path yes blocked blocked:exp-af-117
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-119: M4 GATE-PASS aggregator. DoD: 6 panels = 256×192. All 5 seams correct (3 vert + 2 horiz). Nano-driven arbitrary content anywhere on canvas. Brightness acceptable at dashboard level. EXP-015 thermal all 4 levels PASSED (all voltages within tolerance, temps within limits). EXP-016 recovery ×5 cycles all PASSED. Wi-Fi recovery PASSED. API fail simulation PASSED.

**Task ID:** AF-119
**Epic:** AF-006 (M4: Six-Panel 256×192 Final Prototype — 🔒 GATED MILESTONE #5 of 6)
**Summary:** M4 GATE-PASS final aggregator. 7 DoD bullets: 6 panels works, all 5 seams correct, arbitrary content, brightness OK, thermal ×4 pass, recovery ×5 pass, Wi-Fi+API pass.
**Description:** M4 is gated milestone #5 of 6 per JIRA sequence. Review all AF-109..AF-118 evidence on the critical-path controller. Tick EVERY DoD bullet. 7/7 = PASS. M4 is the FINAL HARDWARE PROTOTYPE gate. After M4 passes, the project moves to reliability / thermal / extended stability / packaging phase (Epic AF-007 MR, then MF mechanical frame). This gate confirms the hardware+software prototype is functionally complete.
**Why this task exists:** Formal M4 exit gate. You CANNOT proceed to AF-120 (MR reliability thermal brightness ceiling) or enclosure work (AF-024 deferred eventually) without explicit sign-off.
**Prerequisites:** COMPLETED: AF-118 (Wi-Fi+API recovery pass). All evidence committed.
**Blocked by:** AF-118
**Required hardware:** None (document review).
**Required tools:** None.
**Required software:** All evidence folders open.
**Exact execution steps:**
1. Gather evidence list:
   - 6 panels 256×192: AF-113 first-light overview photo.
   - 5 seams correct (3V+2H): AF-114 5-photo set + 15-checklist pass.
   - Nano-driven arbitrary content: AF-112 framebuffer + AF-113 patterns proof.
   - Brightness acceptable: AF-115 visual acceptability line.
   - EXP-015 thermal: AF-116 filled table (all 24 cells, voltages in range, temps within limits).
   - EXP-016 power recovery ×5: AF-117 filled 5-row table, 5 intact = Y.
   - Wi-Fi recovery + API fail: AF-118 notes both pass.
2. Tick each DoD bullet.
3. 7/7 = PASS. Commit all M4 evidence as a unit. Write gate-pass log.
4. <7/7 = FAIL → route back.
**Expected result:** 7/7 ticks. M4 gate pass log + commit.
**Acceptance criteria / DoD:**
- 6 panels = 256×192 fully driven on critical-path controller.
- All 5 seams correct (3 vert + 2 horiz) per AF-114 15-checklist.
- Nano-driven arbitrary content anywhere on full canvas (ADR-002 256×192 implemented and validated).
- Brightness acceptable at dashboard level (AF-115 sweep overall visual acceptability = Y).
- EXP-015 thermal all 4 levels PASSED (all 24 cells in-range per AF-116 DoD rules).
- EXP-016 recovery ×5 cycles all PASSED (5× intact = Y, timings recorded AF-117).
- Wi-Fi recovery PASSED. API fail simulation PASSED (AF-118 both tests = pass).
**Evidence to save:**
- `docs/pm/evidence/AF-119-m4-gate-pass.md` (7 ticks, evidence hash list, PASS statement, 1-line summary of any issues/deviations)
- Git commit: `docs(m4): M4 GATE-PASS PASS — 6-panel 256×192 prototype on [controller].`
**Safety considerations:**
N/A.
**Known uncertainties:**
None at gate level.
**Failure response:**
Any bullet fails → specific task re-route. E.g., thermal 100% out of spec → lower ceiling in AF-120 and re-verify 75% max load pass in AF-116 (run 75% level longer as new max; do not gate-fail if you can cap to 75% and still meet dashboard brightness). Recovery ×5 cycle has 1 fail → fix root cause, re-run 5 cycles. DO NOT advance to MR/MF phases.
**Source references:**
- JIRA Milestone 4 full DoD bullet list (6 panels = 256×192, arbitrary text anywhere, all seams correct, acceptable refresh, stable transport, PSU thermal validated, repeated boot recovery tested, Wi-Fi recovery, unattended operation)
- Epic AF-006 header exit criteria
- AF-091 (M2 gate), AF-108 (M3 gate) analogous procedure
**Labels:** validation gate-pass decision blocked blocked:exp-af-118
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-120: MR Aggregate thermal + brightness ceiling analysis. Aggregate EXP-015 table data from AF-116. Rule: if at 100% brightness all readings within spec → brightness ceiling = 100%. If at 100% any reading out of spec → ceiling = 75%. Write the ceiling value explicitly. Also from EXP-016 recovery cycle data: document worst-case time-to-first-image and time-to-network-data; these become operational SLAs for the dashboard.

**Task ID:** AF-120
**Epic:** AF-007 (MR: Reliability, Thermal & Recovery Aggregation)
**Summary:** MR aggregate AF-116 thermal → brightness ceiling rule applied (100% or 75%). Aggregate AF-117 recovery worst → operational TTFI/TTND SLAs documented.
**Description:** Milestone Reliability (MR) aggregation task. Two major outputs from M4's quantitative measurements. (1) Brightness ceiling: open AF-116's filled EXP-015 table. Apply the rule VERBATIM: "At 100% brightness all readings within spec → ceiling=100%. If at 100% any reading out of spec → ceiling=75%." "Readings within spec" = all 6 DoD cells for the 100% row of AF-116 table pass per AF-116 DoD thresholds: (a) PSU V in [5.00,5.10], (b) panel#6 V ≥4.75, (c) PSU chassis <60C, (d) connectors <50C/no-pain, (e) stability Y, (f) visual Y. If ALL 6 pass at 100% → ceiling 100%. If ANY fail → cap at 75%. Write the explicit number (100 or 75). (2) Operational SLAs: open AF-117 filled EXP-016 5-cycle table. worst-case TTFI = max(row1..row5 TTFI column). worst-case TTND = max(row1..row5 TTND column). Document both values as "operational SLAs" with the semantics: "After power restoration, users will see pixels within [worst_TTFI] seconds, and live network dashboard content within [worst_TTND] seconds." Add a note that these are SLAs for the dashboard UX (not contractual; internal product use).
**Why this task exists:** MR (Milestone Reliability) is the post-M4 analysis that produces NUMBERS used by every downstream task: AF-121 (extended stability runs at the ceiling brightness not at 100% if capped), MF mechanical enclosure (vent sizing depends on ceiling & thermal pass/fail), final product operational documentation, future ADR-024 enclosure design.
**Prerequisites:** COMPLETED: AF-119 (M4 gate pass; all measurements taken and valid). COMPLETED: AF-116 thermal table filled. COMPLETED: AF-117 recovery table filled.
**Blocked by:** AF-119
**Required hardware:** None (doc review / analysis).
**Required tools:** None.
**Required software:** None.
**Exact execution steps:**
1. Brightness ceiling analysis:
   a. Pull AF-116 100%-brightness row: 6 cells (a)–(f).
   b. Apply 6 DoD thresholds from AF-116:
      i. (a) PSU V ∈ [5.00,5.10]?
      ii. (b) Panel#6 V ≥ 4.75?
      iii. (c) PSU chassis <60C / hold OK?
      iv. (d) Connectors <50C / no-pain?
      v. (e) Stability Y?
      vi. (f) Visual Y?
   c. Count: all 6 pass → ceiling = 100%. Any 1+ fail → ceiling = 75%.
   d. Write EXPLICIT value: "V1 software brightness ceiling = XX%".
   e. Rationale: 1 sentence stating which cell(s) if any failed forcing 75% cap.
2. Operational SLA analysis:
   a. Pull AF-117 5-cycle table TTFI column. Compute worst_TTFI = max of the 5.
   b. Pull AF-117 TTND column. Compute worst_TTND = max of the 5.
   c. Write EXPLICIT lines:
      - "Operational SLA — Time to first image (TTFI): ≤ XX s worst-case (from PSU ON flip)."
      - "Operational SLA — Time to network data (TTND): ≤ YY s worst-case (from PSU ON flip)."
   d. Semantics note 1 sentence: "These values define the dashboard's power-restore user experience baseline; no manual intervention is expected within these windows."
3. Commit MR analysis doc.
**Expected result:** 1 explicit brightness ceiling number (100% or 75%). 2 explicit worst-case SLA numbers (TTFI s, TTND s). All values sourced from AF-116 / AF-117 tables with no inventing.
**Acceptance criteria / DoD:**
- Brightness ceiling explicitly stated as a single % = 100 or 75.
- Ceiling rule applied correctly: all 6 100%-row cells pass → 100; else → 75.
- Ceiling rationale line: explicit which (a)–(f) cells if any failed, forcing 75 cap.
- Worst-case TTFI explicitly stated in seconds.
- Worst-case TTND explicitly stated in seconds.
- SLA semantics note present.
**Evidence to save:**
- `docs/pm/evidence/AF-120-mr-brightness-ceiling-and-slas.md` (contains: ceiling line, ceiling 6-cell rule-application checklist, ceiling rationale, TTFI worst + TTND worst with SLA lines, semantics note)
- Optional: graph or simple reference back to AF-116/AF-117 table rows.
**Safety considerations:**
N/A.
**Known uncertainties:**
If 100% passes but is uncomfortably close to spec limits (e.g., temp = 59C hold barely OK), the executor may RECOMMEND 75% as operational best-practice even if the strict rule returns 100%. In that case, state BOTH: "By strict rule = 100% pass" AND "MR recommendation: cap at 75% for thermal margin", and let downstream decide. The DoD for this task requires ONLY the strict rule output; the recommendation is advisory.
**Failure response:**
Not applicable (analysis task; no failure beyond missing data, which would route back to AF-116 or AF-117 to re-measure missing cells).
**Source references:**
- AF-116 EXP-015 filled thermal table (source)
- AF-117 EXP-016 filled power recovery table (source)
- EXPERIMENTS.md EXP-015 §Success Criteria: "If necessary, establish a software brightness ceiling below 100%" (this task IS the ceiling establishment)
- JIRA Milestone 4 output → MR phase analysis
**Labels:** docs thermal-review analysis blocked blocked:exp-af-119
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-121: MR Extended Stability ≥24 hours at dashboard-normal content (mixed: time widget + weather + rotating artwork — not full-white). Verify at end: no blank, no garbled rows, no frozen Nano (SSH still works), no thermal issue (hand test temps). Snapshot photo at start, every 8 hours, at end. 4 snapshots minimum.

**Task ID:** AF-121
**Epic:** AF-007 (MR: Reliability, Thermal & Recovery Aggregation)
**Summary:** MR Extended ≥24 hr stability at dashboard-normal content. Snapshot every 8 hr + start+end = min 4. End: no blank, no garble, SSH works, temps OK.
**Description:** Extended duration burn-in at the brightness ceiling determined by AF-120. Content = real dashboard mixed (not full-white stress). Content examples: time/date widget (auto-updating per minute), weather (updates hourly), rotating artwork (rotates every 30 min). The goal = 24-hour actual-dashboard simulation. Take snapshots at T0 start, T8h, T16h, T24h end = minimum 4 photos. At T24h end: physical inspection checklist. (a) No blank panels. (b) No garbled rows (any row still shows content correctly). (c) Nano SSH reachable + process PID same (no segfault restart). (d) Controller / PSU / connector temps hand-test still within thresholds (<60C PSU, <50C connectors — same as thermal test). (e) Optionally: Nano CPU% trend stable, no memory leak (free RAM at end not substantially worse than start — if monitor script runs).
**Why this task exists:** Long-run sanity check before final packaging. 24 hours is 1 day of real dashboard life. If a memory leak, watchdog reset, thermal drift, or silent controller crash mode exists, it will likely show up in 24 h. Short tests (30 min, 1 hr) are insufficient for cumulative-failure modes. This closes MR phase (AF-007).
**Prerequisites:** COMPLETED: AF-120 (brightness ceiling known; run at that ceiling value). COMPLETED: Nano app renders dashboard-normal mixed content (time + weather + artwork widgets available). System left powered ON in a stable location (low traffic, no accidental cable kick).
**Blocked by:** AF-120
**Required hardware:** Full 6-panel system. Wi-Fi AP up for 24 h (for network widgets to update). Power strip not accidentally switched off.
**Required tools:** Timer/alarm reminders at T8h, T16h, T24h. Camera or scripted auto-snap. Optional: Nano cron or script to log free RAM / CPU% hourly to a log file.
**Required software:** Dashboard app running (time, weather, artwork widgets). Optional monitoring script on Nano: free+CPU hourly to `~/24h-stability.log`.
**Exact execution steps:**
1. Set Nano app content = dashboard normal (time + weather + rotating artwork as available). Set brightness = ceiling (AF-120 value).
2. T0 start: timestamp + snapshot photo #1. If monitoring: start log script (`date; free; ps aux | head -5` appended hourly). Leave system running undisturbed.
3. T8h: return. Snapshot photo #2. Quick visual scan: all 6 panels show something? Y/N. If any blank → note in evidence, but continue test to see if auto-recovers from watchdog.
4. T16h: return. Snapshot photo #3. Same quick visual scan.
5. T24h (end):
   a. Snapshot photo #4.
   b. End-checks checklist:
      i. Blank panels 0/6?
      ii. Garbled rows 0? (Compare to T0: same content structure, content looks valid, no rows of random LEDs.)
      iii. Nano SSH: connect from another machine. Works? (Y/N)
      iv. Nano app PID same as start? (ps aux | grep python → PID number same as T0 if logged; or just "app running" = acceptable.)
      v. Temps hand test: PSU chassis <60C? Connectors <50C? Controllers WF4/ESP32s <55C?
      vi. If monitoring log: tail RAM at end vs start — no 2x memory growth.
   c. All end-checks pass → PASS.
6. Tear down (or leave running for next phase).
**Expected result:** ≥24 h (86,400 s) total runtime. 4 snapshots. 6 end-checks all Y/PASS.
**Acceptance criteria / DoD:**
- Total runtime ≥ 86,400 s (wall-clock documented T0→T24h delta ≥ 24 h 0 min).
- 4 snapshots saved (T0, T8h, T16h, T24h).
- End check (i): 0 blank panels.
- End check (ii): 0 garbled rows.
- End check (iii): Nano SSH still works.
- End check (iv): App still running (PID same or alive).
- End check (v): Temps hand test all within limits.
- End check (vi, if monitored): no extreme memory growth.
**Evidence to save:**
- `docs/photos/mr-24h/snapshot-t0-start.jpg`
- `docs/photos/mr-24h/snapshot-t8h.jpg`
- `docs/photos/mr-24h/snapshot-t16h.jpg`
- `docs/photos/mr-24h/snapshot-t24h-end.jpg`
- `docs/pm/evidence/AF-121-mr-24h-stability.md` (T0/T8h/T16h/T24h timestamps; 6 end-check rows; overall pass/fail line; notes on any anomalies or auto-recoveries observed)
- Optional: `docs/pm/evidence/AF-121-nano-monitor.log` (hourly free/CPU if script ran)
**Safety considerations:**
⚠️ 24 h unattended run: ensure the bench is in a low-fire-risk area. No flammables stacked against PSU/panels. Prefer a room with a smoke detector. If the executor has a smart plug / remote power strip, they may set up a temperature alarm (if IR / smart meter available) or auto-cutoff, but it's not required. Bench should still be checked at 8 h and 16 h windows per DoD.
**Known uncertainties:**
Widget content depends on API stability: transient API failures are OK as long as the app's cached fallback (validated AF-118) keeps display non-blank.
**Failure response:**
Panels blank at T8h or T16h → note and continue (the app may have watchdog-recovered; end check still counts total garbled/blank at T24h final). End fails any check → inspect serial logs for crash reasons, check thermal, check transport stability, apply fix, re-run 24 h from T0 (do NOT accept partial 8 h runs as the 24 h result). Temps rising over 24 h → already accounted for by brightness ceiling; if still exceeded, lower ceiling from 100 → 75 or 75 → 50 and re-run.
**Source references:**
- JIRA Milestone 4 + MR (post-M4 reliability): "extended unattended testing"
- AF-085 (30 min M2 WF4), AF-089 (1 hr M2 ESP32), AF-107 (30 min M3) — shorter-duration stability baselines, now scaled to 24 h actual dashboard.
- AF-120 brightness ceiling (value used here)
**Labels:** validation stability reliability critical-path yes blocked blocked:exp-af-120
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-122: Stage 1 Nano OS finalize + update baseline. apt full-upgrade, remove bloat packages, set timezone/locale, disable unused services (Bluetooth, audio if headless, etc.), reboot, confirm boot time. Baseline snapshot.

**Task ID:** AF-122
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 1 Nano OS finalize baseline. apt full-upgrade, de-bloat, timezone/locale, disable unused services, reboot. Confirm boot time. Baseline snapshot.
**Description:** Productionize the Nano OS from "dev image" to "appliance image". Full system upgrade first. Remove packages not needed for headless dashboard appliance: GUI libraries, Bluetooth stack (if no BT peripherals used), audio server (PulseAudio/ALSA utils unless HDMI audio needed), desktop man-db cache triggers, etc. Set correct timezone to the physical frame location (not UTC). Set locale to en_US.UTF-8 or equivalent user locale. Disable unused systemd services: bluetooth.service, avahi-daemon.service (unless mDNS handled another way; if we use mDNS keep avahi), cups, ModemManager, etc. Reboot. Measure boot time from power-on to systemd multi-user.target reached (systemd-analyze or journalctl --list-boots timing). Take a baseline snapshot: list of installed packages (dpkg -l), running services (systemctl list-units --state=running), boot time seconds. This is the "known good OS baseline" that all future MA software tasks assume.
**Why this task exists:** MA stages 2-12 all depend on a stable, minimal, productionized Nano OS. Bloat packages slow boot, waste RAM, create security surface, and can conflict (e.g., PulseAudio grabbing CPU idle poll). Boot time matters because AF-117/EXP-016 measured TTFI includes OS boot; shaving seconds here directly improves the user-visible recovery SLA. A baseline snapshot lets us know if anything accidentally drifts later.
**Prerequisites:** Nano bootable from SD card (M0 subtrack). Nano console access via serial or HDMI/keyboard or SSH (if network already up). Internet access via Wi-Fi for apt.
**Blocked by:** AF-037 (Nano SSH basic shell works — if network already up; if doing Stage 1 offline via serial/HDMI, no prior software task needed).
**Required hardware:** Nano, SD card with OS, Wi-Fi AP with internet.
**Required tools:** None (native apt/systemd).
**Required software:** Stock OS image already flashed (per ADR-010). Root/sudo access on Nano.
**Exact execution steps:**
1. Boot Nano, log in as root or sudo-capable user. Check `sudo apt update` works (apt lists update).
2. Run `sudo apt full-upgrade -y` and let it complete. Answer any config prompts with "keep existing" unless new config is clearly required.
3. Debloat: run `sudo apt purge -y` with a list of unnecessary packages. Minimum purge list: libqt5gui5, libgtk-3-common, bluez, blueman, pulseaudio, pavucontrol, cups, cups-daemon, modemmanager, libreoffice-*, scratch, minecraft-pi, wolfram-engine, hplip. Adjust list if some aren't installed; purge only what exists.
4. Autoremove: `sudo apt autoremove -y && sudo apt clean`.
5. Timezone: `sudo timedatectl set-timezone <TZ>` where TZ = user's location (e.g., America/Los_Angeles, Europe/Stockholm). Verify: `timedatectl` shows correct zone.
6. Locale: `sudo localectl set-locale LANG=en_US.UTF-8` or user's locale. Generate locale if missing: `sudo locale-gen en_US.UTF-8`. Verify: `localectl status`.
7. Disable unused services: `sudo systemctl disable --now bluetooth avahi-daemon cups cups-browsed ModemManager`. (If mDNS name resolution is needed for `ai-frame.local`, KEEP avahi-daemon; only disable it if we use static IP + /etc/hosts or DNS.)
8. Reboot: `sudo reboot`.
9. After reboot, log in. Run `systemd-analyze` → record "Startup finished in Xs (kernel) + Ys (userspace) = Zs total". Record the Z total.
10. Baseline snapshot commands: `dpkg -l > /tmp/nano-baseline-packages.txt`, `systemctl list-units --state=running --no-pager > /tmp/nano-baseline-services.txt`, `uname -a > /tmp/nano-baseline-kernel.txt`.
**Expected result:** Boot time Z total ≤ 45 seconds for a minimal image. Purge step removes ≥ 50 MB of packages. timezone/locale correct. No error messages from disabling services.
**Acceptance criteria / DoD:**
- apt full-upgrade completed with no errors.
- ≥ 10 packages purged + autoremove ran clean.
- timedatectl shows correct timezone; localectl shows correct locale.
- systemctl list-units running: bluetooth/cups/ModemManager NOT in list; avahi present only if mDNS needed.
- Boot time Z ≤ 45 s recorded.
- 3 baseline snapshot files non-empty.
**Evidence to save:**
- `docs/pm/evidence/AF-122-os-baseline.md` (boot time value; list of purged packages count; timezone+locale output lines)
- `docs/pm/evidence/AF-122-packages.txt` (copy of dpkg -l baseline)
- `docs/pm/evidence/AF-122-services.txt` (copy of running services baseline)
**Safety considerations:**
N/A — software configuration only.
**Known uncertainties:**
Exact purge package list depends on the specific distro image shipped. Some packages may not be installed; purge commands tolerate missing packages with --ignore-missing or just skip. If image is already minimal, boot time may be <30 s.
**Failure response:**
If apt fails → check network (ping 8.8.8.8), check /etc/resolv.conf DNS, re-run apt update then retry. If boot time > 60 s → inspect `systemd-analyze blame` and disable top slow services (NetworkManager-wait-online if using static IP, man-db.timer trigger, etc.). If locale fails → re-run locale-gen and restart login shell.
**Source references:**
- JIRA.md §Software Planning Stage 1 "OS baseline harden"
- DECISIONS.md ADR-010 Nano SD card OS
**Labels:** software nano critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-123: Stage 2 Wi-Fi/SSH harden. Static IP reservation on router OR static config on Nano via nmcli. mDNS hostname ai-frame.local confirmed resolves. SSH key-based auth only (disable password auth). Fail2ban or equivalent ssh brute-force protection. Confirm reconnect on reboot without monitor/keyboard.

**Task ID:** AF-123
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 2 Wi-Fi/SSH harden. Static IP (router reservation OR Nano nmcli). mDNS ai-frame.local resolves. SSH key-only auth, disable password. Fail2ban. Reboot reconnect confirmed headless.
**Description:** Make the Nano reliably reachable on the LAN without a monitor after every reboot. Two static IP approaches are valid: (A) preferred = DHCP reservation on Wi-Fi router (bind Nano's wlan0 MAC to a fixed IP via router admin page); (B) fallback = static IP configured on Nano via nmcli (IPv4 method=manual, with gateway + DNS matching router). Set system hostname to `ai-frame`. Confirm mDNS name `ai-frame.local` resolves from another machine on same LAN (ping ai-frame.local or avahi-resolve). SSH harden: generate or copy an SSH public key (from the user's laptop/desktop that will administer the frame) into Nano's `~/.ssh/authorized_keys` (correct perms 600). Then modify /etc/ssh/sshd_config: `PasswordAuthentication no`, `ChallengeResponseAuthentication no`, `PubkeyAuthentication yes`, `PermitRootLogin prohibit-password` or `no`. Install fail2ban: apt install fail2ban, enable default sshd jail (bans IPs after 5 failed auths for 10 min default). Final headless verification: SSH in from laptop, type `sudo reboot`, wait, confirm `ssh user@ai-frame.local` reconnects within 60 s without any monitor/keyboard attached to Nano.
**Why this task exists:** Every subsequent MA task (and M3/M4/MF remote access) requires reliable headless SSH. If Wi-Fi or SSH is flaky, we can't update software or debug remotely. Password auth disabled = brute-force attack surface zero on a LAN-exposed device. Static IP avoids "Nano moved IPs, can't find it" frustration.
**Prerequisites:** AF-122 (OS baseline complete, apt works). Wi-Fi AP known SSID + password. User has an SSH keypair on their admin machine (id_ed25519 or id_rsa.pub).
**Blocked by:** AF-122
**Required hardware:** Nano. Wi-Fi AP with internet. User's admin machine (laptop/desktop) on same LAN.
**Required tools:** Router admin access (for option A static reservation).
**Required software:** openssh-server on Nano (usually preinstalled). fail2ban (new apt install). avahi-daemon on Nano if not already (for mDNS — keep if we disabled in AF-122, we need to re-enable for this task).
**Exact execution steps:**
1. **Hostname + mDNS:** `sudo hostnamectl set-hostname ai-frame`. Edit /etc/hosts if needed to add 127.0.1.1 ai-frame. Ensure avahi-daemon is running: `sudo systemctl enable --now avahi-daemon`. (Re-enable if disabled in AF-122.)
2. **Static IP approach (choose A or B):**
   A (router reservation): Get Nano's wlan0 MAC from `ip link show wlan0 | grep link/ether`. Log into router admin → DHCP reservation / Static lease → bind that MAC to e.g., 192.168.1.50. Save, restart Nano networking. Verify `ip addr show wlan0` shows the reserved IP after renew.
   B (Nano-side static): `sudo nmcli con mod <wificonname> ipv4.method manual ipv4.addresses 192.168.1.50/24 ipv4.gateway 192.168.1.1 ipv4.dns "192.168.1.1,8.8.8.8"`. `sudo nmcli con up <wificonname>`. Verify IP.
3. **mDNS test from ADMIN MACHINE:** On laptop, `ping -c 2 ai-frame.local`. Should resolve to Nano's static IP and reply. If fails, troubleshoot avahi or try direct `.local` resolver on admin machine.
4. **SSH key copy FROM ADMIN MACHINE:** On laptop, `ssh-copy-id user@ai-frame.local` (or manually append laptop's ~/.ssh/id_*.pub to Nano ~/.ssh/authorized_keys, chmod 600). Verify key-based login works: `ssh user@ai-frame.local` should NOT prompt for password.
5. **SSH password auth DISABLE:** On Nano, edit /etc/ssh/sshd_config. Uncomment or set: `PubkeyAuthentication yes`, `PasswordAuthentication no`, `ChallengeResponseAuthentication no`, `KbdInteractiveAuthentication no`, `PermitRootLogin prohibit-password`. Save. `sudo sshd -t` (syntax check OK). `sudo systemctl restart ssh`.
6. **Verify password auth BLOCKED:** From admin machine (with NO key for this test, e.g., from a different test account), `ssh -o PreferredAuthentications=password user@ai-frame.local`. Should get "Permission denied (publickey)" immediately with no password prompt.
7. **Fail2ban:** `sudo apt install -y fail2ban`. `sudo systemctl enable --now fail2ban`. Verify sshd jail active: `sudo fail2ban-client status sshd`. Defaults (5 failures → 10 min ban) are acceptable for V1.
8. **Headless reboot reconnect TEST:** From admin machine SSH session: `sudo reboot`. Admin session drops. Start stopwatch. Run `ssh user@ai-frame.local` repeatedly until it reconnects. Record seconds from reboot command to successful login. Confirm ≤ 60 s.
**Expected result:** ai-frame.local resolves from admin machine. SSH password auth disabled. Fail2ban sshd jail active. Headless reboot reconnect ≤ 60 s.
**Acceptance criteria / DoD:**
- Static IP confirmed (either router reservation page screenshot OR nmcli con show output with method=manual + static IP).
- `ping ai-frame.local` from admin machine: 2/2 replies, correct IP.
- SSH key-only login works; password login attempt gets "Permission denied (publickey)" — NO password prompt.
- `sudo fail2ban-client status sshd` shows jail count 1.
- Headless reboot reconnect time ≤ 60 s documented.
**Evidence to save:**
- `docs/pm/evidence/AF-123-network-harden.md` (static IP method chosen + IP value; mDNS ping output; ssh reconnect time; fail2ban status line)
- Optional screenshot of router DHCP reservation page (if approach A)
**Safety considerations:**
N/A — software network configuration only.
**Known uncertainties:**
Some cheap Wi-Fi routers don't support mDNS forwarding across WLAN ↔ LAN, or some Windows machines lack Bonjour/mDNS client. If ai-frame.local doesn't resolve on Windows, admin can use direct static IP instead. Fail2ban ban-duration default may be 1h in some distros; 10 min or 1h both fine for V1.
**Failure response:**
SSH lockout after step 5 (can't login) → ATTACH HDMI/keyboard directly to Nano, revert sshd_config. ALWAYS verify key-based login works BEFORE disabling password auth (step 4 confirmed key login works, step 5 is just closing the door). Static IP conflicts → pick a different IP outside DHCP pool range.
**Source references:**
- JIRA.md §Software Planning Stage 2 "Wi-Fi + SSH harden"
- DECISIONS.md ADR-009 network architecture
**Labels:** software nano critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-124: Stage 3 Minimal Python env frozen. venv at /opt/ai-frame/venv or ~/ai-frame-venv. requirements.txt: pin Pillow 10+ version + any transport deps (pyserial for UART, requests for API calls). venv activation PATH recorded for later systemd ExecStart line. `pip freeze > requirements.txt` committed.

**Task ID:** AF-124
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 3 Minimal Python venv frozen. /opt/ai-frame/venv or ~/ai-frame-venv. Pillow 10+ pinned, pyserial, requests. requirements.txt from pip freeze committed. venv activation PATH recorded for systemd.
**Description:** Create a dedicated Python virtual environment for the ai-frame application. Two location choices: (A) system-wide `/opt/ai-frame/venv` owned by ai-frame service user (more "appliance" style); (B) user-local `~/ai-frame-venv` (simpler for single-user prototype). V1 prototype can use either; document which. Inside venv, install minimum required packages with pinned major versions: `Pillow>=10.0,<11` (Pillow 10+ for current text rendering APIs), `pyserial` (UART transport candidate), `requests` (HTTP API calls for weather/calendar widgets). These three are the baseline; nothing else extra (no numpy/pandas unless genuinely needed later). After install, run `pip freeze > requirements.txt` which pins every transitive dep to exact version (e.g., Pillow==10.4.0, pyserial==3.5, requests==2.32.3, certifi==..., charset-normalizer==..., etc.). Record the absolute PATH to the venv's python interpreter: `/opt/ai-frame/venv/bin/python3` or `/home/user/ai-frame-venv/bin/python3`. This exact PATH will be the ExecStart line prefix in the systemd unit file (AF-133).
**Why this task exists:** Reproducible Python environment. Without venv + freeze, we get "works on my machine" drift; system Python package upgrades via apt can break Pillow or other deps randomly. requirements.txt committed = any rebuild of Nano SD card installs exact same versions. The venv interpreter path is required input for systemd service file in Stage 12.
**Prerequisites:** AF-122 (apt works, OS baseline). Python3-venv package installed (apt install python3-venv).
**Blocked by:** AF-122
**Required hardware:** Nano with SD card.
**Required tools:** None.
**Required software:** python3-venv, pip.
**Exact execution steps:**
1. Install venv support: `sudo apt install -y python3-venv python3-pip`.
2. Create venv (choose A or B, document choice):
   A (system-wide): `sudo mkdir -p /opt/ai-frame && sudo chown $USER:$USER /opt/ai-frame && python3 -m venv /opt/ai-frame/venv`
   B (user-local): `python3 -m venv ~/ai-frame-venv`
3. Activate venv: `source <path>/bin/activate`. Verify `which python3` points inside the venv.
4. Install pinned minimum deps: `pip install "Pillow>=10.0,<11" pyserial requests`.
5. Verify Pillow version: `python3 -c "from PIL import Image; print(Image.__version__)"` — should show 10.x.y.
6. Verify pyserial: `python3 -c "import serial; print(serial.__version__)"`.
7. Verify requests: `python3 -c "import requests; print(requests.__version__)"`.
8. Freeze: `cd /opt/ai-frame` (or project dir) and `pip freeze > requirements.txt`.
9. Verify requirements.txt contains all three + transitive deps: `wc -l requirements.txt` should show ≥ 8 lines (Pillow, pyserial, requests, certifi, charset-normalizer, idna, urllib3, etc.).
10. Record the venv python interpreter absolute PATH (e.g., `/opt/ai-frame/venv/bin/python3`) into a note for AF-133.
**Expected result:** venv activated. Pillow 10.x + pyserial + requests import correctly. requirements.txt ≥ 8 lines with exact pinned versions. venv python PATH recorded.
**Acceptance criteria / DoD:**
- venv created. `ls <venvpath>/bin/python3` exists.
- Inside venv, Pillow 10.x version printed.
- pyserial and requests import OK.
- requirements.txt file contains `Pillow==10.x.y` line, `pyserial==x.y`, `requests==x.y.z` + transitive deps. ≥ 8 lines total.
- venv interpreter absolute PATH documented.
**Evidence to save:**
- `requirements.txt` committed to repo root or `/opt/ai-frame/requirements.txt` (the actual committed file is the evidence; also copy a reference snapshot to docs/pm/evidence/AF-124-requirements.txt)
- `docs/pm/evidence/AF-124-python-env.md` (venv location choice A/B; Pillow/pyserial/requests version output lines; venv python PATH line)
**Safety considerations:**
N/A — Python environment setup.
**Known uncertainties:**
Exact transitive dep versions vary by release date; `pip freeze` captures whatever is current at install time. That's correct — we want the snapshot. Pillow 10+ is required because some text-rendering APIs changed in 9.x → 10.x.
**Failure response:**
pip install fails → check internet, check pypi.org reachable. If Pillow fails to compile on some arch → `sudo apt install -y libjpeg-dev zlib1g-dev libfreetype6-dev liblcms2-dev` then retry. venv create fails → `apt install python3-venv` or install the matching python version package.
**Source references:**
- JIRA.md §Software Planning Stage 3 "Python venv freeze"
- ADR-010 Nano SD card OS
**Labels:** software nano critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-125: Stage 4 Pillow text renderer production. Font selection: pick a TTF font (Noto Sans or DejaVu Sans — present by default or apt install). Multi-line rendering (newlines in input string → wrap). Alignment options: left/center/right horizontal, top/middle/bottom vertical. API: render_text(text, width, height, font_size=?, align=?, valign=?, color=?, bgcolor=?) → returns Framebuffer.

**Task ID:** AF-125
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 4 Pillow text renderer production. TTF font (Noto/DejaVu Sans). Multi-line wrap on newlines. 3×3 align options. API render_text(...) returns Framebuffer.
**Description:** Build the canonical production text renderer on top of Pillow ImageDraw.text. Font selection: pick a high-quality TTF font that's either (a) preinstalled on Nano OS (DejaVu Sans — usually at /usr/share/fonts/truetype/dejavu/DejaVuSans.ttf and DejaVuSans-Bold.ttf) OR (b) installed via apt: fonts-noto-core → Noto Sans (better Unicode coverage for emoji if we ever want them). Confirm font file path exists. Rendering features: (1) Multi-line rendering: if input `text` contains `\n` characters, split into lines and render each line sequentially. (2) Word wrap (optional for V1; at minimum handle explicit \n). (3) Horizontal alignment: `align='left'` → x=0 anchor; `align='center'` → x=w/2 center anchor; `align='right'` → x=w right anchor. (4) Vertical alignment: `valign='top'` → y=0 start; `valign='middle'` → total text block centered vertically in canvas; `valign='bottom'` → text block anchored to bottom. (5) Color: RGB tuple default (255,255,255) white text; bgcolor default (0,0,0) black background. (6) Font_size parameter in pixels. The function MUST return a Framebuffer object (or PIL Image, if framebuffer class not yet formalized — accept PIL Image temporarily but note the transition to AF-127's Framebuffer class after Stage 6).
**Why this task exists:** The whole point of the frame = text display. M1/M2 used ad-hoc Pillow snippets; MA Stage 4 makes it a reusable production API. Widgets in Stage 12 (clock, weather, calendar, Spotify) all call render_text() directly. 3×3 align covers all widget layout cases (clock top-right = right/top; weather card centered = center/middle; scrolling bottom banner = center/bottom, etc.).
**Prerequisites:** AF-124 (Pillow 10+ installed in venv). Venv activated.
**Blocked by:** AF-124
**Required hardware:** Nano (or any dev machine with Pillow — can be developed on laptop first, then deploy to Nano).
**Required tools:** None.
**Required software:** Pillow 10+ in venv. A TTF font file present.
**Exact execution steps:**
1. **Font selection + install (if needed):** Check `ls /usr/share/fonts/truetype/dejavu/DejaVuSans*.ttf`. If present → use DejaVu Sans (apt install fonts-dejavu if missing). ELSE install Noto: `sudo apt install -y fonts-noto-core` → use NotoSans-Regular.ttf. Record the absolute path to the TTF file chosen.
2. Write `text_renderer.py` module with function:
```
def render_text(text: str, width: int, height: int,
                font_size: int = 24,
                align: str = 'left',      # left | center | right
                valign: str = 'top',      # top | middle | bottom
                color: tuple = (255,255,255),
                bgcolor: tuple = (0,0,0),
                font_path: str = <ttf_abspath>) -> <Framebuffer_or_PIL_Image>:
```
3. Implementation steps inside function:
   a. Create PIL Image RGB (width, height), fill bgcolor.
   b. Load ImageFont.truetype(font_path, font_size).
   c. Split `text` by `\n` → lines list.
   d. For each line, compute its bounding box with draw.textbbox((0,0), line, font) → get line_w, line_h.
   e. Compute total block height = sum(line_heights for all lines) + line_spacing*(n-1) (line_spacing = 2-4 px).
   f. Compute y_start based on valign: top→0, middle→(height-block_height)//2, bottom→height-block_height.
   g. For each line, compute x based on align: left→0, center→(width-line_w)//2, right→width-line_w. Draw at (x, y_curr). y_curr += line_h + spacing.
   h. Return PIL Image. (After AF-127, change return type to Framebuffer; API stays same.)
4. Unit test by hand (or pytest if installed): Call render_text with 9 combinations: align × valign = 3×3 = 9 calls. Pass a 3-line string, font_size=16, w=256, h=64. Export each as PNG and visually inspect: text positions match the align names.
5. Test edge case: single long line without \n (V1 word-wrap optional — just let it clip if too long is OK; document the behavior).
**Expected result:** 9 alignment renderings all correctly positioned. Multi-line with \n works (lines drawn sequentially below each other). Font renders without artifacts (glyphs present, no broken Unicode for basic ASCII).
**Acceptance criteria / DoD:**
- TTF font file absolute path documented. Font renders basic ASCII a-z, A-Z, 0-9 correctly.
- Multi-line test (3 lines via \n): 3 lines rendered, correct vertical spacing.
- 3×3 align tests (9 images generated): each image visually matches its align+valign label.
- Function signature matches spec above (8 parameters with defaults).
**Evidence to save:**
- `text_renderer.py` source committed to repo.
- `docs/pm/evidence/AF-125-text-render/align-<align>-<valign>.png` — 9 PNG files covering all 3×3 combos
- `docs/pm/evidence/AF-125-text-render/multiline-test.png` — 3-line rendering
- `docs/pm/evidence/AF-125-renderer.md` (font path; font name chosen; list of 9 generated files + visual confirmation line)
**Safety considerations:**
N/A — Pillow rendering code.
**Known uncertainties:**
Exact line-height calculation varies by font (ascender/descender metrics). Pillow's textbbox() in Pillow 10+ handles this better than deprecated textsize(). V1 does not need word-wrapping (no break-on-space); explicit \n in widget strings is acceptable.
**Failure response:**
Font path wrong → locate font with `fc-list | grep -i "dejavu\|noto sans" | head -5`. PIL unidentifiable image errors → upgrade to Pillow 10+ or ensure the Image.new mode is "RGB" consistently. Alignment visibly off → adjust the textbbox-based calculation to use anchor parameters or add per-font baseline offsets.
**Source references:**
- JIRA.md §Software Planning Stage 4 "Pillow text renderer production"
- AF-031 (M1 Nano basic Pillow text) — ad-hoc predecessor that this task productionizes
- Pillow 10 docs: ImageDraw.textbbox(), ImageFont.truetype()
**Labels:** software nano critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-126: Stage 5 Standard test-pattern renderer moved to reusable module `testpatterns.py` with function `generate(name, w, h)` accepting pattern names from the Standard Test Pattern Suite list. Unit-tested output dimensions correct.

**Task ID:** AF-126
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 5 Standard test-pattern module testpatterns.py. generate(name, w, h) accepts pattern suite names. Output dimensions unit-tested.
**Description:** Migrate the ad-hoc test pattern drawing code from M1/M2/M3 EXP procedure scripts into a single reusable module. The module exposes one canonical function: `generate(name: str, w: int, h: int) -> <Image_or_Framebuffer>`. The accepted `name` values MUST be the exact names from the Standard Test Pattern Suite (as used in EXP-005/006/012/017 — e.g., 'horizontal-gradient', 'vertical-gradient', 'full-white', 'full-black', 'red', 'green', 'blue', 'vertical-lines-16', 'seam-cross-text', 'numbered-regions', 'checkerboard-8x8', 'corner-labels-px', 'framebuffer-canary', etc.). List every supported name in a module-level constant `SUPPORTED_PATTERNS: list[str]`. Unknown pattern name → raise ValueError. Unit test: for every pattern in SUPPORTED_PATTERNS, call generate(name, 256, 64) and assert the returned image size is (256, 64). Additional quality spot-check: for 'full-white' pattern, sample pixel at center → assert RGB (255,255,255); for 'full-black' → (0,0,0); for 'vertical-lines-16' at x=16 → non-black, at x=8 → black (checks line spacing).
**Why this task exists:** Validation tasks in M3/M4/MF all need to generate named patterns at arbitrary sizes. Ad-hoc copy-paste from EXP scripts leads to drift (gradient slightly different in each script, numbered regions count wrong). A single module = one canonical implementation, used by every EXP re-run and every GATE-PASS seam/visual test.
**Prerequisites:** AF-124 (Pillow in venv). AF-125 (Pillow rendering basic confirmed).
**Blocked by:** AF-124, AF-125
**Required hardware:** None (can be developed on laptop).
**Required tools:** None.
**Required software:** Pillow 10+ in venv. pytest optional (asserts by hand acceptable).
**Exact execution steps:**
1. Create `testpatterns.py` module.
2. Define SUPPORTED_PATTERNS list = [
   'full-white', 'full-black', 'red', 'green', 'blue',
   'horizontal-gradient', 'vertical-gradient',
   'vertical-lines-16', 'horizontal-lines-16',
   'checkerboard-8x8',
   'numbered-regions',
   'seam-cross-text',
   'corner-labels-px',
   'framebuffer-canary'
] (Add more if M2/M3 tests reference additional names — inspect AF-086/AF-090/AF-104 outputs.)
3. Implement generate(name, w, h):
   a. Validate name in SUPPORTED_PATTERNS; else raise ValueError(f"Unknown pattern: {name}")
   b. Create img = Image.new('RGB', (w, h), (0,0,0))
   c. draw = ImageDraw.Draw(img)
   d. Switch on name → implement each pattern:
      - full-white: fill (255,255,255)
      - red/green/blue: fill (255,0,0) etc.
      - horizontal-gradient: for x in 0..w-1: draw line at x with color (int(255*x/(w-1)), 0, int(255*(w-1-x)/(w-1)))
      - vertical-gradient: similar for y
      - vertical-lines-16: for x in 0,16,32,... < w: draw 1px vertical line white
      - numbered-regions: draw w/4 × h/4 grid of rectangles, each with its index number centered
      - etc. for every other named pattern
   e. Return img
4. Write test script `test_testpatterns.py` with assertions:
   a. For every name in SUPPORTED_PATTERNS: img = generate(name, 256, 64); assert img.size == (256, 64)
   b. img = generate('full-white', 16, 16); assert img.getpixel((8,8)) == (255,255,255)
   c. img = generate('full-black', 16, 16); assert img.getpixel((8,8)) == (0,0,0)
5. Run test script. All assertions pass.
**Expected result:** test_testpatterns.py runs with 0 assertion failures. Every pattern renders at correct dimensions. Corner-cases (full-white pixel, full-black pixel) match.
**Acceptance criteria / DoD:**
- testpatterns.py has generate(name, w, h) function + SUPPORTED_PATTERNS list with ≥ 14 entries.
- Unknown name raises ValueError (tested manually).
- test_testpatterns.py: all per-pattern dimension assertions pass (≥ 14 patterns × (256,64) OK).
- full-white + full-black pixel assertions pass.
**Evidence to save:**
- `testpatterns.py` committed.
- `test_testpatterns.py` committed (or pytest equivalent).
- `docs/pm/evidence/AF-126-testpatterns.md` (list of supported pattern names count; test pass result line; sample PNG of 'horizontal-gradient' at 256×64)
- `docs/pm/evidence/AF-126-sample-gradient.png`
**Safety considerations:**
N/A — code module.
**Known uncertainties:**
Exact pattern list may grow if M2/M3 use additional named patterns not listed above; add them as needed by searching AF-086/AF-090/AF-104 content.
**Failure response:**
Pattern dimensions wrong → inspect for off-by-one in gradient loops or ImageDraw.rectangle coordinate bugs (Pillow rectangle is inclusive/exclusive corner depending on version — add +1 to end coord if last pixel missing). ValueError on legit name → add it to SUPPORTED_PATTERNS.
**Source references:**
- JIRA.md §Software Planning Stage 5 "Test patterns module"
- M1/M2/M3 EXP procedure pattern generation snippets (AF-033, AF-086, AF-090, AF-104, AF-105)
**Labels:** software nano critical-path yes validation
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-127: Stage 6 Canonical framebuffer abstraction production API docs + unit tests. Framebuffer class methods: new(w,h), set_pixel(x,y,r,g,b), get_pixel(x,y), get_region(x,y,w,h) → returns new Framebuffer, paste_region(src_fb, dst_x, dst_y), export_raw_bytes() → bytes, export_png(path). Unit tests: dimensions, set→get round-trip, region crop edge cases, raw bytes length = w*h*3.

**Task ID:** AF-127
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 6 Framebuffer class production API. Methods: new, set_pixel, get_pixel, get_region, paste_region, export_raw_bytes, export_png. Unit tests for all. Raw bytes length = w*h*3.
**Description:** Formalize the framebuffer concept from ADR-002 (canonical 256×192 for V1 6-panel, but class is generic w × h) into a single `framebuffer.py` module with a clean, well-tested Framebuffer class. Internal storage: flat bytes or bytearray of length w*h*3 (RGB interleaved, row-major order). Every method has clear semantics + docstring. Unit tests: pytest-style or assert-script style. Test cases cover: (1) Constructor: Framebuffer(w, h) → internal buf length = w*h*3; get_width() return w; get_height() return h. (2) set_pixel(x,y,r,g,b) at (0,0) then get_pixel(0,0) → same RGB. At (w-1, h-1) corner → same. Out of bounds (x=w,y=0) → raise IndexError or clamped (document behavior; prefer RAISE IndexError to catch bugs early). (3) get_region(0,0,w,h) → new Framebuffer identical to original (pixel-for-pixel compare). get_region(w-10, h-5, 10, 5) at edge → 10×5 OK. get_region out of bounds → error. (4) paste_region: create 10×10 red Framebuffer; paste into 100×100 at (50,50); get_pixel(55,55) → red; get_pixel(0,0) → black (default). (5) export_raw_bytes() → len == w*h*3, first 3 bytes == get_pixel(0,0) RGB. (6) export_png(path) → file exists, PIL.open(path) returns (w, h) size.
**Why this task exists:** Framebuffer is the canonical inter-module data type. Renderers (text, patterns, widgets) → produce Framebuffer. Transport → consumes Framebuffer (or raw bytes). Row crop/split in Stage 10 operates on Framebuffer regions. Without a shared abstraction, each module passes PIL Images or raw bytes with ad-hoc conventions, leading to "transposed rows" or "BGR vs RGB" bugs. ADR-002 requires this abstraction for the 3-row crop split to be testable.
**Prerequisites:** AF-124 (venv + Pillow).
**Blocked by:** AF-124
**Required hardware:** None.
**Required tools:** None.
**Required software:** Pillow (for PNG export). pytest optional.
**Exact execution steps:**
1. Create `framebuffer.py`.
2. Implement Framebuffer class with methods:
   a. `__init__(self, w: int, h: int)` → self.w, self.h, self._buf = bytearray(w*h*3)
   b. `width(self) -> int` → return w
   c. `height(self) -> int` → return h
   d. `_idx(self, x, y)` → y*w*3 + x*3 (row-major RGB). Raises IndexError if x/y out of [0,w-1]/[0,h-1].
   e. `set_pixel(self, x, y, r, g, b)` → write 3 bytes at _idx
   f. `get_pixel(self, x, y) -> tuple[int,int,int]` → read 3 bytes, return tuple
   g. `get_region(self, x, y, rw, rh) -> 'Framebuffer'` → allocate new Framebuffer(rw,rh); for each j in 0..rh-1: for i in 0..rw-1: copy (x+i,y+j) → (i,j)
   h. `paste_region(self, src: 'Framebuffer', dx: int, dy: int)` → for j in 0..src.h-1: for i in 0..src.w-1: src (i,j) → self (dx+i, dy+j)
   i. `export_raw_bytes(self) -> bytes` → return bytes(self._buf)
   j. `export_png(self, path: str)` → use Pillow: create Image.new('RGB', (w,h)); pixels from buf; Image.save(path)
   k. Add `@classmethod from_pil(cls, pil_img) -> 'Framebuffer'` helper for Stage 4/5 transition (converts PIL Image to Framebuffer).
3. Write `test_framebuffer.py` with assertions for all 6 categories above.
4. Run tests: all pass.
**Expected result:** Every unit test passes. No IndexError false positives. Raw bytes length exact. export_png produces valid PNG of correct size.
**Acceptance criteria / DoD:**
- Framebuffer class: 10 methods/constructors listed above all present with correct signatures.
- Dimensions test: Framebuffer(256, 192)._buf → len 147,456 (256*192*3).
- set→get round-trip: 3 pixel positions (0,0), (w-1,h-1), (w//2,h//2) all match. IndexError on x=w.
- get_region full-frame: new fb pixel-identical to source. Edge crop 10×5 at corner: dimensions OK.
- paste_region: red 10×10 at 50,50 → (55,55) is red; (0,0) is default black.
- export_raw_bytes(256,192) → len 147,456; first 3 bytes == (0,0) pixel RGB.
- export_png → PIL Image.open size (w,h).
**Evidence to save:**
- `framebuffer.py` committed.
- `test_framebuffer.py` committed.
- `docs/pm/evidence/AF-127-framebuffer.md` (test results summary: all N assertions passed; counts of each test category)
- Optional: generated PNG from export_png test.
**Safety considerations:**
N/A — data structure + unit tests.
**Known uncertainties:**
Out-of-bounds behavior: raise IndexError vs clamp. Choice = RAISE (catches bugs). If a use case legitimately wants clamping, add a clamp=True flag later. Internal ordering: RGB row-major (confirmed matches ADR-002; HUB75 panels also expect row-major RGB at 3.3V logic).
**Failure response:**
Raw bytes length off by factor → check _idx formula (y*w*3 + x*3 is correct; if swapped x*h*3 → transposed). set→get mismatch → check color ordering in buf (R then G then B, not BGR). PNG wrong size → check PIL Image.new dimensions.
**Source references:**
- JIRA.md §Software Planning Stage 6 "Framebuffer abstraction formalize"
- DECISIONS.md ADR-002 Canonical Framebuffer (256×192 V1, row-major RGB)
- AF-104/AF-105/AF-106 (M3 seam test renderers — informal predecessors that motivated formalization)
**Labels:** software nano critical-path yes validation docs
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-128: Stage 7 Transport interface formalized. Exception classes: TransportError, TransportTimeout, TransportDisconnected, SerializationError. Reconnect semantics: connect() with retry + exponential backoff config (max_backoff, max_attempts). send_frame(buffer, w, h) with internal retry loop. Status query method is_connected() → bool.

**Task ID:** AF-128
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 7 Transport interface formalized. 4 Exception classes. connect() with retry + exponential backoff (max_backoff, max_attempts). send_frame(buf,w,h) internal retry. is_connected() -> bool.
**Description:** Write the abstract Transport base class (interface) that all concrete transport implementations (AF-129 winner) will inherit from. This is ONLY the interface contract — no network/socket/serial code yet. Content: (1) TransportException base class. Subclasses: TransportError (general send/receive fail), TransportTimeout (ack not received within deadline), TransportDisconnected (socket closed, serial cable unplugged), SerializationError (frame bytes can't be framed or header CRC wrong). (2) Abstract class BaseTransport with ABCMeta: `__init__(self, config: dict)` where config contains backoff parameters. `connect(self) -> None` — attempt connection, honor max_attempts + exponential backoff: attempt 1 delay = base_delay_s (e.g., 1 s), attempt 2 = min(2*base, max_backoff), attempt 3 = min(4*base, max_backoff), etc. After max_attempts fails → raise TransportError. `send_frame(self, framebuffer: Framebuffer) -> None` or `send_frame(self, buf: bytes, w: int, h: int) -> None` — send one frame; internal retry loop on transient failures; persistent failure → raise. `is_connected(self) -> bool` — status. Close/disconnect optional. (3) Config dataclass or dict keys: `base_delay_s: float = 1.0`, `max_backoff_s: float = 30.0`, `max_attempts: int = 5`, `send_timeout_s: float = 5.0`, `frame_retry_count: int = 3`. (4) ONE dummy test: NoopTransport(BaseTransport) implementation that drops frames or succeeds based on a test flag — used for unit testing Stage 8+ without real hardware.
**Why this task exists:** Without a shared interface, Stage 8's concrete transport would be hard-coded throughout the app, making ADR-017 swaps or future transport additions a massive refactor. Exponential backoff in connect() = no "Nano spams reconnect during power loss, crashes router's DHCP" bugs. Exception hierarchy lets Stage 11's reliability layer catch specific failure types (Timeout → retry; Disconnected → reconnect loop; Serialization → log and skip).
**Prerequisites:** AF-127 (Framebuffer class defined, used in type hints).
**Blocked by:** AF-127
**Required hardware:** None.
**Required tools:** None.
**Required software:** None beyond Python stdlib + typing.
**Exact execution steps:**
1. Create `transport/` package (directory) with `__init__.py` and `base.py`.
2. In `transport/base.py`:
   a. `class TransportError(Exception): pass`
   b. `class TransportTimeout(TransportError): pass`
   c. `class TransportDisconnected(TransportError): pass`
   d. `class SerializationError(TransportError): pass`
   e. `from abc import ABC, abstractmethod`
   f. `class BaseTransport(ABC):`
      - `def __init__(self, config: dict | None = None):` → merge config with defaults, store self._config
      - `def connect(self) -> None:` → loop with attempts = 0; while attempts < max_attempts: try self._connect_impl(); return on success; except TransportError: sleep exponential_backoff(attempts++); raise after loop
      - `@abstractmethod def _connect_impl(self) -> None:` → override in concrete
      - `def send_frame(self, fb: Framebuffer) -> None:` → attempt frame_retry_count times; call self._send_frame_impl(fb); return. On transient error → retry; permanent → raise
      - `@abstractmethod def _send_frame_impl(self, fb: Framebuffer) -> None:` → concrete override
      - `@abstractmethod def is_connected(self) -> bool:`
      - `def _backoff_s(self, attempt: int) -> float:` → return min(base_delay * (2 ** attempt), max_backoff). (Optional jitter: ±10% random to avoid thundering herd.)
3. In `transport/__init__.py`: export all 4 exceptions + BaseTransport.
4. In `transport/noop.py`: create NoopTransport(BaseTransport) for unit test use. _connect_impl passes. _send_frame_impl no-ops (success) or raises based on class attr `_fail_next: bool`. is_connected returns True if connect() ran.
5. Test script: instantiate NoopTransport; connect() succeeds. send_frame(dummy_fb) succeeds. Set _fail_next=True → send_frame retries 3x then raises TransportError (verifies retry logic).
**Expected result:** Exception hierarchy 4 levels deep. connect() retries with correct exponential backoff. send_frame retries N times before raising. is_connected correctly reflects state.
**Acceptance criteria / DoD:**
- 4 Exception classes defined (TransportError base, 3 subclasses). Subclasses are isinstance() of TransportError.
- BaseTransport: connect, send_frame, is_connected, _backoff_s methods.
- _backoff_s(0)=1, _backoff_s(1)=2, _backoff_s(2)=4, _backoff_s(5)=30 (if max_backoff=30, base=1). Correct exponential.
- NoopTransport tests: connect OK, send OK, retry-count verified on forced fail.
**Evidence to save:**
- `transport/__init__.py`, `transport/base.py`, `transport/noop.py` committed.
- `docs/pm/evidence/AF-128-transport-iface.md` (exception class count; backoff values at attempts 0-5; Noop test pass result)
**Safety considerations:**
N/A — interface code.
**Known uncertainties:**
send_frame signature: Framebuffer vs raw bytes + w/h. Use Framebuffer (richer, type-safer); concrete impl can call fb.export_raw_bytes() internally. Jitter addition: optional but good practice; add if easy.
**Failure response:**
ABC abstract enforcement not working → ensure `class BaseTransport(ABC, metaclass=ABCMeta)` or Python 3.4+ style `class BaseTransport(ABC)` is used. Retry count not triggering → check for break statement inside retry loop.
**Source references:**
- JIRA.md §Software Planning Stage 7 "Transport interface formalize"
- ADR-017 Transport decision (interface exists to accept ADR-017's winner)
- EXP-007 (WF4 transport) + EXP-013 (ESP32 UART) raw measurement data (motivated interface parameters)
**Labels:** software nano critical-path yes docs
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-129: Stage 8 Transport IMPLEMENTATION — select ADR-017 result protocol (with Reclassification rule for pre-decision). Pre-decision state: Conditional task that reclassifies. Reclassification rule VERBATIM: "Reclassification rule: After ADR-017 transport decision. Activate implementation branch matching ADR-017 selection: (a) Huidu-protocol → build concrete HuiduTransport (uses lib from EXP-007C, WF4 path); (b) UART → concrete UartTransport from EXP-013 framing; (c) USB → concrete UsbSerialTransport; (d) TCP → concrete TcpTransport. DESELECT branches: mark as Conditional=yes Skip='ADR-017 selected [other]; skip'. Implement ONLY the ADR-017 winner; do NOT implement the others for V1 (reject scope creep)."

**Task ID:** AF-129
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 8 Transport IMPLEMENTATION (Conditional — ADR-017 winner only). Pre-decision: Conditional=yes. Post ADR-017: reclassify per rule. Implement ONE concrete transport subclass. Skip others.
**Description:** Implement the SINGLE concrete transport subclass matching ADR-017's result. Four possible branches (only ONE built for V1):
(a) **HuiduTransport:** If ADR-017 = Huidu-protocol (WF4 path). Wraps the reverse-engineered library code from EXP-007C (Nano → WF4 via USB or Ethernet depending on WF4 port). Reuses the framing/command bytes documented in EXP-007B/C evidence files. Concrete impl: _connect_impl opens USB device (pyusb) or TCP socket (if WF4 Ethernet port). send_frame_impl sends frame buffer bytes in Huidu command format (header, X-port target for 3 rows, payload, checksum if any).
(b) **UartTransport:** If ADR-017 = UART (multi-ESP32 path). Uses pyserial, opens /dev/ttyUSB0 or /dev/ttyS0 at the agreed baud rate (EXP-013 result: e.g., 921600 or 1500000). Framing format from EXP-013: e.g., magic byte, target row ID byte, length uint16, payload, CRC8/16. For 256×192 canvas: caller splits into 3 rows per Stage 10, UartTransport may handle one row at a time (send to row 0, row 1, row 2 in sequence) OR transport layer routes rows internally based on config.
(c) **UsbSerialTransport:** If ADR-017 = USB native (similar to UART but different driver path; some ESP32 USB-C ports present as /dev/ttyACM0). Nearly identical code to UartTransport but with USB ACM device path.
(d) **TcpTransport:** If ADR-017 = TCP/IP (Wi-Fi or wired Nano → controller(s) reachable via IP:port). Open socket to each controller IP (or single WF4 IP). Send frame in length-prefixed TCP packets.

After selecting the winner branch: code the concrete module under `transport/huidu.py`, `transport/uart.py`, `transport/usbserial.py`, or `transport/tcp.py`. Implement `_connect_impl`, `_send_frame_impl`, `is_connected`. Unit test with loopback or dummy endpoint where possible. The three NON-winner branches: DO NOT IMPLEMENT. Leave only the module-level docstring + Skip placeholder.
**Why this task exists:** Transport is the bridge between Nano software and the controller hardware. Every frame that reaches a panel goes through this code. Implementing ONLY the ADR winner prevents scope creep ("well, TCP would be nice to have too even though we chose UART"). Reclassification rule ensures Conditional flag is flipped OFF post-ADR so the task becomes critical-path=yes for the chosen branch.
**Prerequisites:** BEFORE ADR-017: none. AFTER ADR-017: AF-128 (interface done) AND ADR-017 ACCEPTED with specific transport named. If branch (a) Huidu: EXP-007C protocol data. If (b) UART: EXP-013 framing data.
**Blocked by:** AF-128, ADR-017 decision (blocked:adr-017)
**Required hardware:** AFTER decision: the relevant hardware (WF4 for a, ESP32 with USB/UART for b/c, network for d).
**Required tools:** AFTER decision: any relevant tools from EXP-007/EXP-013.
**Required software:** For Huidu: pyusb. For UART/USB: pyserial. For TCP: stdlib socket.
**Exact execution steps:**
1. **PRE-DECISION (this step on FIRST execution of task before ADR-017 is written):** Confirm ADR-017 status in DECISIONS.md. If still PENDING → STOP. Document that task is blocked pending ADR-017; do not write any transport code yet.
2. **POST-DECISION (ADR-017 ACCEPTED with winner known):**
   a. Read ADR-017 Decision line → note winner (a)/(b)/(c)/(d).
   b. Apply reclassification rule VERBATIM to this task: update task Conditional=no, remove blocked:adr-017 label, set critical-path=yes. For the 3 NON-selected transport branches (e.g., if winner=UART, then Huidu/USB/TCP branches), create placeholder skip-notes or no additional tasks needed (we don't have per-branch tasks, we just don't code them).
   c. Create transport module file for winner branch (huidu.py / uart.py / usbserial.py / tcp.py).
   d. Class WinnerTransport(BaseTransport): implement __init__, _connect_impl, _send_frame_impl, is_connected. Use the protocol data from EXP evidence: EXP-007C for Huidu; EXP-013 for UART framing; etc.
   e. Live smoke test: transport connect → send test 256×192 frame → panels display something. Verify at least one frame visually correct.
   f. Document the non-winning branches: in a short note in evidence, state "ADR-017 selected [WINNER]; branches Huidu/UART/USB/TCP as appropriate were SKIPPED per reclassification rule. Their modules (if empty) may be deleted or left with a SKIP comment."
**Expected result:** ONE concrete transport class implemented. Live smoke test: 1 frame sent → panels display it. Non-winner branches explicitly SKIPPED in documentation with reason.
**Acceptance criteria / DoD:**
**PRE-DECISION (ADR-017 still PENDING) DoD:** Task marked Conditional=yes, blocked:adr-017 label present, reclassification rule text copied exactly into Status flags section. NO transport code written.
**POST-DECISION (ADR-017 ACCEPTED) DoD:**
- Reclassification executed: this task Conditional=no, critical-path=yes, blocked:adr-017 removed from Labels.
- Winner transport module file exists (1 of huidu/uart/usbserial/tcp.py). Class inherits BaseTransport.
- Live smoke test: send_frame(256×192 FB) → panels display frame. Photo evidence.
- Non-winner 3 branches: explicit SKIP documented per rule. NO non-winner transport code files committed (or if file exists, only comment: "SKIP — ADR-017 selected [X]").
**Evidence to save:**
- Transport module file committed (winner only).
- `docs/pm/evidence/AF-129-transport-impl.md` (winner branch name; ADR-017 decision citation; non-winner 3 branches SKIP list; smoke test pass note)
- `docs/photos/ma-transport/smoke-test-frame.jpg` (live smoke test photo if post-decision)
**Safety considerations:**
⚠️ UART/USB-serial only: ensure correct voltage level. ESP32 UART pins are 3.3 V; Nano USB-serial 5 V would fry ESP32. Use proper level shifters if going via TTL; via USB-C cable only → OK (USB is 5 V but data is differential; ESP32 onboard CH340 handles 5 V→3.3 V).
**Known uncertainties:**
PRE-DECISION uncertainty: winner unknown. Resolved by ADR-017. POST-DECISION: exact framing parameters may differ from EXP evidence by 1 byte (header length, CRC polynomial, magic bytes). Test with loopback first.
**Failure response:**
PRE-DECISION → stop work, do not code. POST-DECISION, send_frame fails: (a) double-check EXP framing evidence byte-by-byte; (b) use logic analyzer or print debug first 16 bytes both sides; (c) baud rate / parity / stop bit mismatches for UART → match EXP-013 values exactly. If one row works but others don't → check row target ID routing.
**Source references:**
- JIRA.md §Software Planning Stage 8 "Transport SINGLE implementation"
- DECISIONS.md ADR-017 (the actual decision). Read this BEFORE writing any concrete code.
- EXP-007 evidence (Huidu bytes), EXP-013 evidence (UART framing bytes)
- AF-128 (BaseTransport interface — implement this)
**Labels:** software nano blocked blocked:adr-017 conditional
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-017 per reclassification rule)
Conditional: yes
Skip condition: ADR-017 not yet decided. Activate only after ADR-017 ACCEPTED.
Reclassification rule: After ADR-017 transport decision. Activate implementation branch matching ADR-017 selection: (a) Huidu-protocol → build concrete HuiduTransport (uses lib from EXP-007C, WF4 path); (b) UART → concrete UartTransport from EXP-013 framing; (c) USB → concrete UsbSerialTransport; (d) TCP → concrete TcpTransport. DESELECT branches: mark as Conditional=yes Skip='ADR-017 selected [other]; skip'. Implement ONLY the ADR-017 winner; do NOT implement the others for V1 (reject scope creep).

---

### AF-130: Stage 9 Physical end-to-end productionized. Run the Nano → transport → 6-panel E2E with arbitrary multi-line content, alignment combinations, font sizes. Should work from M1/M2/M3/M4 gates already; this task = harden pipeline, remove debug prints, add structured logging. E2E stress test: 500 sequential frame sends with no errors.

**Task ID:** AF-130
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 9 E2E pipeline hardened production. Multi-line content × align combos × font sizes. Remove debug prints. Add structured logging. Stress test: 500 sequential frame sends 0 errors.
**Description:** Wire all prior stages together into ONE production E2E pipeline: Nano app code calls render_text() → returns Framebuffer → transport.send_frame(fb) → panels display. The M1/M2/M3/M4 gates already proved the individual parts; this task removes the "development scaffolding": delete ad-hoc print("DEBUG: sending packet len=49152") statements. Replace them with a structured logger (Python logging module with JSON formatter or at minimum timestamp-level-message format). Logger config: INFO level for normal events (frame_sent, transport_connected, reconnect_started), WARNING level for retries, ERROR level for send failures after retries, CRITICAL for watchdog escalations. Then run the E2E stress test: a loop that renders 500 different frames (vary content, align, font_size per iteration) and sends each one sequentially. Count: total_attempts, successful_sends, retries_triggered, errors. Success criterion: successful_sends == 500, errors == 0. Retries_triggered ≤ 5 (minor transient recovery OK). Visual spot-check at start, middle (frame #250), end (#500): panels show correct content each time.
**Why this task exists:** M4 proved the system works once or for 30 min. MA Stage 9 = productionize the pipeline. Debug prints spam logs and slow the event loop. Structured logs mean when something fails in 72h MF wall test (AF-150), we can grep the log file for CRITICAL/ERROR and see what happened instead of scrolling 72h of DEBUG prints. 500-frame stress = catches intermittent framing/timeout bugs that 1 frame misses.
**Prerequisites:** POST-DECISION prerequisite: AF-129 (concrete transport implemented). Also AF-125 (render_text), AF-127 (Framebuffer), AF-128/AF-129 (transport). M4 GATE-PASS (AF-119) confirmed physical 6-panel works.
**Blocked by:** AF-129 (concrete transport impl), AF-125, AF-127, AF-119 (M4 6-panel hardware works)
**Required hardware:** Full 6-panel system energized. Nano connected to controllers via chosen transport.
**Required tools:** None.
**Required software:** transport impl, framebuffer, text_renderer modules all committed.
**Exact execution steps:**
1. Create the pipeline glue module `pipeline.py`:
   a. Imports: text_renderer.render_text, framebuffer.Framebuffer, transport.<winner>.
   b. Logger setup: basicConfig level=INFO, format='%(asctime)s %(levelname)s %(name)s: %(message)s' (or JSON format if json-logging installed).
   c. Function `run_one(text, font_size, align, valign)`: call render_text → convert PIL→Framebuffer → transport.send_frame. Try/except around send: catch TransportError → log WARNING + retry once per config. Log INFO frame_sent event with fields (text_len, font_size, align, valign, send_time_ms).
2. Remove ALL debug print() statements from transport module, framebuffer, text_renderer, pipeline. (Replace any useful ones with logger.debug calls that are silenced at INFO level.)
3. Manual E2E sanity check: call run_one() with 9 combos (3×3 align/valign) from render_text Stage 4 test. Visually confirm each frame displays on panels.
4. **Stress test loop (500 iterations):**
   a. Initialize counters: total=0, ok=0, retries=0, errors=0.
   b. For i in 0..499:
      - Randomize: text = 1-3 lines, font_size ∈ {12,16,20,24,32}, align random of 3, valign random of 3.
      - total += 1.
      - try: run_one(text,...); ok += 1; except TransportError as e: errors += 1; log.error(e). (Count retries inside run_one; add to retries counter.)
   c. After loop: print(f"TOTAL={total} OK={ok} RETRIES={retries} ERRORS={errors}").
5. **Visual spot checks:** at iteration 0, 250, 499: take photo of panels showing the frame.
**Expected result:** TOTAL=500. OK=500. ERRORS=0. RETRIES ≤ 5. All 3 spot-check photos show correctly rendered+positioned content. Logger has ~500 INFO frame_sent lines with timing data.
**Acceptance criteria / DoD:**
- 0 debug print() statements remaining in codebase modules (grep -rn "print(" over framebuffer.py, text_renderer.py, testpatterns.py, transport/, pipeline.py — should return 0 matches, or only print in test scripts).
- Structured logger configured: at least timestamp + level present in every log line.
- Manual 9× combo E2E: all 9 combos display correctly on panels.
- Stress test: OK=500, ERRORS=0, RETRIES ≤ 5.
- 3 spot-check photos saved (start, mid, end).
**Evidence to save:**
- `pipeline.py` committed.
- `docs/pm/evidence/AF-130-e2e-stress.log` (log file from stress run — first 50 lines + last 50 lines OK)
- `docs/pm/evidence/AF-130-e2e-summary.md` (stress counters: TOTAL/OK/RETRIES/ERRORS values; logger config description)
- `docs/photos/ma-e2e/spot-000-start.jpg`, `spot-250-mid.jpg`, `spot-499-end.jpg`
**Safety considerations:**
N/A — software pipeline stress.
**Known uncertainties:**
Retries ≤ 5 threshold: if network/UART transient is higher (e.g., cheap USB-serial cable causing 20 retries), investigate cable before accepting. 500 frames may take 1-5 min depending on FPS; if too slow, reduce to 200 and note in evidence.
**Failure response:**
ERRORS > 0 → inspect error log lines; if same SerializationError every time → framing bug in transport (go back to AF-129, compare EXP bytes). If random TransportTimeout → increase send_timeout_s in transport config by 2×. RETRIES > 5 → check physical connections (HUB75 seat, UART TX/RX wiggle test, network cable). Visual fail (frame blank) → 0x00 byte bug in render or transport; export FB to PNG locally and compare to panel.
**Source references:**
- JIRA.md §Software Planning Stage 9 "E2E pipeline harden + 500 frame stress"
- AF-119 (M4 GATE-PASS: 6-panel hardware baseline proved)
- AF-085/AF-090/AF-107 (30 min stability baselines; now stress is quick 500 frames not timed)
**Labels:** software nano critical-path yes validation
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-131: Stage 10 Scaling framebuffer dimensions. Canonical dimensions = 256×192 for V1 6-panel. 3-row crop split: top (y=0..63→ctrl#1/X1), mid (y=64..127→ctrl#2/X2), bot (y=128..191→ctrl#3/X3). Renderer code ZERO changes (no dimension constants in renderer). All dimension constants in ONE config file config.py with CANVAS_W=256, CANVAS_H=192, ROW_HEIGHT=64, NUM_ROWS=3, PANEL_PER_ROW=2. Verify: change CANVAS_H to 64 in config → renderer still works (M1 1-panel case).

**Task ID:** AF-131
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 10 Dimension scaling + 3-row crop split. Renderers ZERO dimension constants. All constants in config.py (CANVAS_W=256, CANVAS_H=192, ROW_H=64, NUM_ROWS=3, PANEL_PER_ROW=2). Config change H=64 → M1 1-panel works unchanged.
**Description:** Centralize ALL dimension-related constants into a SINGLE `config.py` file. No renderer, transport, or widget code may hardcode numbers like 256, 192, 64, 3, 2. EVERYTHING reads from config.py. Then implement the canonical 3-row crop split function for V1 6-panel: given a single full-canvas Framebuffer (W×H from config), produce NUM_ROWS sub-Framebuffers where row i = fb.get_region(0, i*ROW_HEIGHT, CANVAS_W, ROW_HEIGHT). For V1 6-panel: row_0 = 0..63, row_1 = 64..127, row_2 = 128..191. Each row goes to its controller (WF4: X1/X2/X3 ports; ESP32: ctrl-0 / ctrl-1 / ctrl-2). ZERO renderer changes requirement: text_renderer.py, testpatterns.py take width/height as function parameters already. Renderer modules must NOT `import config` (they are generic). Only pipeline glue (AF-130) and transport wrappers import config and pass the values down. **Verification test:** edit config.py CANVAS_H=64 (M1 1-panel 128×64 or 256×64), NUM_ROWS=1, ROW_HEIGHT=64. Restart app. Render 1 line of text → 1 row of panels displays correctly (crop split returns 1 row = whole canvas). Then revert config to V1 defaults.
**Why this task exists:** Dimension drift = a huge source of bugs in multi-panel systems. If renderer says 256×192, crop split says 256×190, transport says 256×192 → bottom 2 rows blank or misaligned. Centralizing in ONE config.py ensures single source of truth. The "edit config → test M1 case" check is a meta-verification that NO hardcoded constants slipped in (if someone hardcoded 192, changing CANVAS_H=64 would break). ADR-002 requires "framebuffer dims agnostic to renderer code" — this task is the enforcement.
**Prerequisites:** AF-127 (Framebuffer with get_region). AF-130 (pipeline works end-to-end). Text renderer (AF-125) and test patterns (AF-126) already parameterized on w/h.
**Blocked by:** AF-127, AF-130
**Required hardware:** 6-panel system (for V1 test) AND ability to disconnect rows temporarily (for M1 verification: wire only 1 row = 2 panels). If rewiring is expensive, the M1 verification CAN be done on paper (code inspection + PNG-only render, no physical display — but physical preferred).
**Required tools:** None.
**Required software:** All MA modules to date.
**Exact execution steps:**
1. Create `config.py` with constants:
```
CANVAS_W = 256
CANVAS_H = 192
ROW_HEIGHT = 64
NUM_ROWS = 3
PANELS_PER_ROW = 2
PANEL_W = 128
PANEL_H = 64
```
(Add any other useful derived constants like ROW_PIXELS = CANVAS_W * ROW_HEIGHT * 3.)
2. **Grep audit:** Run `grep -rn "256\|192\|ROW_HEIGHT\|NUM_ROWS" framebuffer.py text_renderer.py testpatterns.py transport/ pipeline.py`. EXPECTED: ZERO matches in framebuffer.py (it's generic), ZERO in text_renderer.py, ZERO in testpatterns.py. In pipeline.py / transport: matches should ONLY appear as `config.CANVAS_W` etc. (not bare numbers). Fix any bare numbers → import config + use attribute.
3. **Row split function:** In pipeline.py (or new `rowsplit.py` module):
```
import config
from framebuffer import Framebuffer
def split_rows(canvas_fb: Framebuffer) -> list[Framebuffer]:
    rows = []
    for i in range(config.NUM_ROWS):
        y0 = i * config.ROW_HEIGHT
        row_fb = canvas_fb.get_region(0, y0, config.CANVAS_W, config.ROW_HEIGHT)
        rows.append(row_fb)
    return rows
```
4. **Integration test V1 defaults:** Create 256×192 full canvas filled with horizontal-gradient (testpatterns.generate). Call split_rows. Get 3 row FBs, each (256, 64) dimensions. Export each to PNG: row0.png (y=0..63 gradient top), row1.png (y=64..127), row2.png (y=128..191). Visually check row0 shows top of gradient, row2 shows bottom.
5. **M1 1-row verification (dimension change test):**
   a. Edit config.py: CANVAS_H=64, NUM_ROWS=1, ROW_HEIGHT=64. Save.
   b. Run render_text(text="M1 TEST", w=256, h=64). Pass through split_rows → 1 row FB (256×64). Send to physical 1-row display (2 panels) if available. If hardware unavailable, export PNG + verify dimensions (256, 64) and text is centered in a 64-px-tall image.
   c. **PASS if:** NO code edits needed outside config.py. The render/rowsplit/transport code runs as-is on both V1 6-panel config and M1 1-panel config. The only change was config.py numbers.
6. **Revert config.py back to V1 defaults (CANVAS_H=192 etc.).**
**Expected result:** ZERO bare-number dimension constants in renderer/transport modules (only config.py has them). split_rows returns correctly sized rows. M1 config change test: 0 code changes outside config.py → pipeline works unchanged.
**Acceptance criteria / DoD:**
- config.py exists with all 6+ constants.
- grep audit: renderer modules (text_renderer, testpatterns, framebuffer) have 0 bare 256/192/64 matches. pipeline/transport import config only.
- split_rows on 256×192 → 3 rows each 256×64, gradient visually continuous from row0→row1→row2.
- M1 1-row test: edit ONLY config.py (CANVAS_H=64, NUM_ROWS=1, ROW_H=64) → pipeline works, 0 module source edits required. Hardware OR PNG proof.
- config.py reverted to V1 defaults after test.
**Evidence to save:**
- `config.py` committed with V1 defaults.
- `rowsplit.py` or pipeline split_rows function committed.
- `docs/pm/evidence/AF-131-dim-scale.md` (grep audit zero-match result line; row split dimension check pass; M1 1-row test pass note "0 code edits outside config.py")
- `docs/pm/evidence/AF-131-row0.png`, `row1.png`, `row2.png` (V1 split gradient rows)
- `docs/pm/evidence/AF-131-m1-canvas.png` (M1 256×64 render after config change, no renderer edits)
**Safety considerations:**
N/A — dimension config refactor.
**Known uncertainties:**
If transport modules accept w, h params but were tested only with 256×192, they may have implicit assumptions (e.g., "row length is 49152 bytes" → 256*64*3). Fix those to use len(fb.export_raw_bytes()) instead of constants.
**Failure response:**
Bare numbers found in renderer files → refactor to function parameters. split_rows dimension off-by-one: y0 should be i*ROW_HEIGHT not i*(ROW_HEIGHT+1). Verify row0 covers y=0..63 inclusive, row1 y=64..127, row2 y=128..191. M1 test breaks (after config change transport errors) → transport had hardcoded "192÷64=3 rows" loop; refactor to config.NUM_ROWS.
**Source references:**
- JIRA.md §Software Planning Stage 10 "Dimension scaling"
- DECISIONS.md ADR-002 Canonical Framebuffer (256×192 V1, but renderer code agnostic)
- AF-104/AF-105/AF-106 (M3 4-panel seam tests — manually cropped rows; this task automates the crop correctly for 6-panel V1 case)
**Labels:** software nano critical-path yes validation
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-132: Stage 11 Reliability/recovery layer. Structured JSON logs (logger with structured output: timestamp, level, event, fields). Transport reconnect with exponential backoff on failure. Controller restart detection (if transport supports heartbeat or echo — detect dead, reconnect, flush frame). Watchdog: if no frame sent successfully for > 5 min → escalate (log CRITICAL + optionally reboot Nano via sudo reboot — configurable).

**Task ID:** AF-132
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 11 Reliability layer. Structured JSON logs. Transport auto-reconnect exponential backoff. Controller heartbeat/restart detect. Watchdog 5 min no-frame → CRITICAL + optional reboot.
**Description:** Production resilience layer. Three components:
1. **Structured JSON logging:** Upgrade AF-130's basic logger to JSON output. Every log line is a single-line JSON object: `{"ts": "2026-08-19T12:34:56.789Z", "level": "INFO", "event": "frame_sent", "fields": {"took_ms": 123, "ok": true}}`. Write to rotating log file `/var/log/ai-frame/app.log` (max size 10 MB, keep 5 rotated logs) + stderr for systemd journal. Python package: use stdlib logging + `python-json-logger` if installed, else hand-rolled JSON formatter.
2. **Transport auto-reconnect manager:** Wrap transport in a ReconnectingTransport decorator class (same BaseTransport interface). If send_frame raises TransportDisconnected: call reconnect with exponential backoff (AF-128 base class already provides connect() retry loop). After reconnect, re-send the failed frame ONCE. If re-send fails → propagate error. For **controller restart detection:** heartbeat or echo. If transport supports echo command (e.g., send 1-byte PING → get 1-byte PONG back; or if TCP just check socket alive): try ping every 30 seconds. If ping fails 3x → reconnect. After reconnect, send the current frame buffer once to refresh the panels (controllers boot to blank).
3. **Watchdog:** A watchdog thread tracks `last_successful_frame_ts` (epoch seconds). Every successful frame_sent → update timestamp. Watchdog wakes every 10 seconds: check (now() - last_successful_frame_ts > 300 seconds [5 min configurable]). If EXPIRED: (a) log CRITICAL level event `watchdog_expired` with fields (gap_seconds, last_frame_ts). (b) Configurable escalation: `WATCHDOG_ESCALATE = 'log_only'` (default for testing) OR `'reboot'` (production: run `sudo reboot` via subprocess). The escalation setting is in config.py.
**Why this task exists:** 72-hour MF wall test (AF-150) and 24h MR stability (AF-121) need resilience. If controller reboots silently (power glitch, watchdog inside controller), panels go blank and stay blank until someone SSHes in. Heartbeat detect + auto-reconnect + flush-frame means panels recover within ~30 s of controller reset without human intervention. The 5-min watchdog catches deadlock/segfault. Structured JSON logs let you `grep CRITICAL /var/log/ai-frame/app.log | jq` after the fact to see exactly when things died.
**Prerequisites:** AF-128 (BaseTransport interface for decorator). AF-130 (pipeline with send_frame calls). Logger infrastructure exists from Stage 9.
**Blocked by:** AF-128, AF-130
**Required hardware:** Full 6-panel system (for live watchdog/reconnect integration test: simulate a controller reboot by unplugging/replugging its power).
**Required tools:** None.
**Required software:** python-json-logger optional (stdlib fallback OK). sudo for reboot test (careful — test log_only first!).
**Exact execution steps:**
1. **JSON logger:** Create `logging_config.py` module. Setup function: creates logger with JsonFormatter. FileHandler at /var/log/ai-frame/app.log (RotatingFileHandler 10 MB × 5 backups) + StreamHandler to stderr. Level INFO default.
2. **ReconnectingTransport decorator:** `class ReconnectingTransport(BaseTransport):` wraps inner concrete transport. Override send_frame(fb):
```
def send_frame(self, fb):
    try:
        self._inner.send_frame(fb)
        record_success_ts()
    except TransportDisconnected:
        log.warning("event=transport_disconnected action=reconnect")
        self._inner.connect()  # exponential backoff inside connect() per AF-128
        try:
            self._inner.send_frame(fb)  # re-send
            record_success_ts()
        except TransportError:
            raise
```
3. **Heartbeat (if supported):** Add a background thread every 30 s: try self._inner.ping() (implement ping in concrete transport if possible — echo byte or socket keepalive). Failures_count → after 3 fails → reconnect(). After reconnect: send latest cached frame (keep a copy of last FB) so panels aren't blank.
4. **Watchdog thread:** Background thread. Loop: sleep 10 s. Check (time.time() - last_success > config.WATCHDOG_TIMEOUT_S [default 300]). If expired: log.critical with event='watchdog_expired'. Then: if config.WATCHDOG_ESCALATE == 'reboot': subprocess.run(['sudo', 'reboot']). (Test with 'log_only' first!)
5. **Integration test:**
   a. Run pipeline with ReconnectingTransport + watchdog + JSON logging. Send frames every 2 s for 2 min → verify app.log has JSON lines, watchdog not firing.
   b. Simulate disconnect: for UART → physically unplug USB-serial cable for 15 s → plug back. Observe logs: WARNING transport_disconnected, reconnect attempt (backoff), reconnect success, re-send OK. Panels recover.
   c. Watchdog test: set WATCHDOG_TIMEOUT_S = 20 (test value), disable transport (don't call send), wait 30 s → log CRITICAL watchdog_expired appears. ESCALATE = 'log_only' → no reboot. Good.
**Expected result:** JSON logs valid (each line parses as JSON via `jq .`). Simulated disconnect → auto-reconnect within backoff time + frame re-sent. Watchdog fires correctly (log CRITICAL after timeout + optional reboot — test with log_only!).
**Acceptance criteria / DoD:**
- app.log contains valid JSON lines. `jq type < /var/log/ai-frame/app.log | head -3` returns "object" 3x.
- Structured fields: every line has ts, level, event keys.
- Disconnect simulation test: log WARNING "transport_disconnected" followed by reconnect events; panels display again within 60 s of plug-back.
- Watchdog test with short timeout: CRITICAL line appears at ~timeout_s + 10 s. No reboot when ESCALATE='log_only'.
**Evidence to save:**
- `logging_config.py`, `reconnecting_transport.py` (or decorator in transport/__init__.py), watchdog code committed.
- config.py updated with WATCHDOG_TIMEOUT_S=300, WATCHDOG_ESCALATE='log_only' defaults.
- `docs/pm/evidence/AF-132-reliability.md` (JSON validation sample 3 lines; disconnect test result; watchdog test result)
- `docs/pm/evidence/AF-132-sample-app.log` (first 20 lines of JSON log)
- Optional screenshot of log during disconnect/reconnect sequence.
**Safety considerations:**
⚠️ Watchdog reboot: NEVER test with ESCALATE='reboot' on a machine that you can't physically access or that hosts other services (SSH, containers, etc.). Test exclusively with 'log_only' first. Verify sudo reboot works only when you're physically present to power-cycle if it gets stuck in a boot loop.
**Known uncertainties:**
Heartbeat ping: some transports don't have an explicit echo. For TCP, socket SO_KEEPALIVE is sufficient (dead peer detection via TCP keepalive probes). For UART, no echo → heartbeat not possible; fall back to send_frame failure as disconnect detector. That's acceptable (panel blank 1 cycle → reconnect next cycle).
**Failure response:**
JSON log parsing fails (jq errors) → hand-rolled formatter missing escape for quotes/newlines; switch to python-json-logger package via pip. Reconnect loop infinite (thundering herd) → check _backoff_s caps at max_backoff (AF-128). Watchdog fires immediately after start → initialize last_success_ts = time.time() at startup (not 0 epoch).
**Source references:**
- JIRA.md §Software Planning Stage 11 "Reliability / recovery layer"
- AF-117 EXP-016 (power recovery — the app-level counterpart to PSU power loss; this task handles silent crashes/controller resets at runtime)
- AF-121 (24h stability — this layer is why 24h pass expected without SSH intervention)
**Labels:** software nano critical-path yes validation
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-133: Stage 12 Higher-level widgets + supervision. Widgets: (a) Time/date clock widget; (b) Weather widget (OpenWeatherMap API, cache 10 min); (c) Google Calendar next-event widget (OAuth or ics URL); (d) Spotify now-playing + album artwork widget (Spotify Web API or DBus MPRIS); (e) Arbitrary artwork/photo rotation (local dir walk, shuffle, interval). Caching layer: all HTTP API results cached on disk. Offline fallback status indicator: "Data stale as of HH:MM" banner when cache stale. Systemd service file ai-frame.service with Type=simple, Restart=on-failure, RestartSec=5, WantedBy=multi-user.target. Boot-time auto-startup: `systemctl enable ai-frame`. Verify: reboot Nano → service starts automatically → dashboard visible within 60 seconds.

**Task ID:** AF-133
**Epic:** AF-008 (MA: Application Software Completion)
**Summary:** Stage 12 Widgets + systemd supervision. 5 widgets (clock, weather, calendar, Spotify, artwork rotation). HTTP cache to disk. Offline stale-data banner. systemd ai-frame.service auto-start. Reboot → dashboard ≤60 s.
**Description:** Final MA stage. Build 5 widgets, then wrap the whole app in a systemd service for unattended boot.
**Widgets (each widget renders into a Framebuffer):**
(a) **Clock widget:** every 1 s (or every 10 s, minute-change is enough) render current date+time. Uses render_text(). Layout: big time HH:MM, smaller date YYYY-MM-DD Weekday below. Alignment: top-left or top-right (configurable).
(b) **Weather widget:** OpenWeatherMap API. Calls `https://api.openweathermap.org/data/2.5/weather?q=CITY&appid=KEY&units=metric`. Cache result on disk to `/var/cache/ai-frame/weather.json` with timestamp. Skip HTTP call if cache age < 600 s (10 min). Render: city name, temp °C, condition icon (text emoji or simple icon drawing if Pillow draw supports), humidity %. If HTTP fails → use cache file regardless of age; show "stale as of HH:MM" small banner in corner of widget.
(c) **Calendar next-event widget:** Google Calendar. Two methods supported: (i) .ics file URL (public share URL, no auth — simplest for V1): download iCal, parse next event with `icalendar` package; OR (ii) Google OAuth with google-api-python-client (more setup, private calendar). Choice = whichever user provides creds for. Cache next event time + title to disk. Stale fallback: show last cached event with stale banner.
(d) **Spotify now-playing widget:** Two paths: (i) Spotify Web API (OAuth, requires user Spotify Premium account) → GET https://api.spotify.com/v1/me/player/currently-playing → track name, artist, album cover image URL → download cover, Pillow paste. OR (ii) DBus MPRIS on Linux desktop (if Nano runs desktop player locally — less likely for headless; support if easy). Cache: last track info + cover art file. Stale fallback: "Last played: Track - Artist" banner.
(e) **Artwork/photo rotation widget:** Local directory walk (configurable path, e.g., ~/Pictures/ai-frame-art or /opt/ai-frame/art). Find all files *.jpg, *.jpeg, *.png, *.webp. Shuffle order at boot. Every N seconds (configurable, default 1800 s = 30 min) → pick next file → Pillow open → resize (fit within canvas, preserve aspect ratio, letterbox background black) → convert to Framebuffer.
**Caching:** Standardized cache helper `disk_cache.py` — functions get(key, max_age_s) → value_or_None, set(key, value). Storage dir `/var/cache/ai-frame/<key>.json` or `.blob`. Mtime checked for age.
**Systemd service file:** Write `/etc/systemd/system/ai-frame.service` (or `ai-frame.service` committed to repo + install instructions for user to copy to /etc). Contents:
```
[Unit]
Description=AI-Frame Display Service
After=network.target

[Service]
Type=simple
User=ai-frame-user (or current user)
WorkingDirectory=/opt/ai-frame
ExecStart=<venv_python_path_from_AF-124> /opt/ai-frame/main.py
Restart=on-failure
RestartSec=5
Environment="PYTHONUNBUFFERED=1"

[Install]
WantedBy=multi-user.target
```
Install: `sudo systemctl daemon-reload && sudo systemctl enable ai-frame`. **Boot verify:** `sudo reboot`. From admin machine, stopwatch: T0 = power-on. SSH check: ai-frame.local reachable by ping within ~30 s; systemctl status ai-frame shows active (running); visually confirm panels display dashboard content. Total time from reboot to panels visible = ≤ 60 s.
**Why this task exists:** Widgets = the actual end-user functionality. Without them it's just a test-pattern generator. Caching + offline banner = "data staleness is visible, not silently wrong". Systemd supervision = the app comes up automatically after every power loss (EXP-016's TTND metric directly depends on systemd Restart=on-failure). Boot auto-start verify = "unplug frame from wall after 6 months, plug back in — it works without SSH".
**Prerequisites:** AF-125 (render_text). AF-127 (Framebuffer). AF-128/AF-129 (transport). AF-131 (config.py for canvas dims). AF-132 (watchdog/logging). User-provided API keys (.env file) for (b)(c)(d) — these widgets are BLOCKED:DELIVERY if no keys. Clock widget (a) and artwork rotation (e) need NO external keys → do those first.
**Blocked by:** AF-125, AF-127, AF-129, AF-131, AF-132. Widgets (b)(c)(d): blocked:delivery (user must provide API keys before integration test).
**Required hardware:** Nano + full 6-panel system.
**Required tools:** None.
**Required software:** requests (already in venv), icalendar (pip install for widget c if .ics method), Pillow (already there). Spotify: spotipy or requests+OAuth.
**Exact execution steps:**
1. **disk_cache.py helper module:** Implement get/set with mtime-based expiry. Storage dir: /var/cache/ai-frame (mkdir -p, chown).
2. **Widget (a) Clock:** Write `widgets/clock.py`. No external deps, use datetime.now(). Call render_text for time (big font, e.g., 36 px) and date (smaller, 16 px). Align top-left. Unit test by generating PNG.
3. **Widget (e) Artwork rotation:** Write `widgets/art_rotation.py`. Local dir walk, shuffle list, round-robin index. Every interval_s: next image → resize fit → letterbox. Generate PNG test.
4. **Widget (b) Weather:** Write `widgets/weather.py` + config.py OWM_API_KEY, OWM_CITY. If key is None/unset → widget returns "Weather API key not configured" placeholder + skip HTTP. Test with key if available; verify cache get skips HTTP when <10 min old. Test stale case: edit cache mtime to 24 h old → force offline → widget renders stale banner.
5. **Widget (c) Calendar:** `widgets/calendar.py`. If ics config URL set → download .ics, parse, find next event after now(). If OAuth → skip if creds not provided; document setup steps for user in README. Stale banner test same as weather.
6. **Widget (d) Spotify:** `widgets/spotify.py`. If creds not set → placeholder. Cache cover art JPEG on disk. Stale banner.
7. **Dashboard layout composer:** `dashboard.py` — combines all 5 widgets into one canvas (configurable layout: e.g., clock top-left, weather top-right, calendar middle-left, Spotify middle-right, artwork bottom-half or background). Uses Framebuffer paste_region.
8. **main.py entry point:** import dashboard + pipeline + watchdog loop. Main loop: every REFRESH_INTERVAL_S (default 10 s for clock ticking; widgets cached for longer per their max_age). Render dashboard → send_frame.
9. **Systemd unit file:** Write `ai-frame.service` with spec above. Substitute <venv_python_path> with the path recorded in AF-124 evidence. Copy to /etc/systemd/system/. daemon-reload, enable.
10. **Reboot verify:** sudo reboot. Start stopwatch at "BIOS POST done" or power-on. Check: ping ai-frame.local responds at ~20 s. SSH + systemctl status: active. Visual: dashboard on panels. Record time from reboot to dashboard content visible. ≤ 60 s = PASS.
**Expected result:** 5 widget modules present (clock, art, weather, calendar, spotify). Weather/calendar/spotify have no-key placeholder AND stale-banner behavior. Dashboard composer produces one coherent 256×192 Framebuffer. Systemd service enabled. Reboot → dashboard visible within 60 s.
**Acceptance criteria / DoD:**
- Clock (a) widget: renders current HH:MM + date. PNG test saved.
- Art rotation (e) widget: local dir images fit + letterbox correct. PNG test.
- Weather (b): cache logic works. Key missing → placeholder. Stale → banner. If key present → real data.
- Calendar (c): ics URL path implemented (test with public iCal URL like a holiday calendar if user not provided).
- Spotify (d): API or placeholder code present, stale banner logic implemented.
- disk_cache: set+get works; max_age respected.
- systemd ai-frame.service: file present in /etc/systemd/system, enable ran, WantedBy=multi-user.target.
- Reboot verify: dashboard visible on panels at ≤ 60 s from power-on. Documented time.
**Evidence to save:**
- `widgets/` package (5 modules), `disk_cache.py`, `dashboard.py`, `main.py`, `ai-frame.service` all committed.
- .env.example file committed with placeholder keys for OWM/CAL/SPOTIFY.
- `docs/pm/evidence/AF-133-widgets.md` (5 widgets present status; cache logic test results; stale-banner test results; reboot dashboard time = N s ≤ 60)
- `docs/photos/ma-widgets/dashboard-boot.jpg` (photo at boot verify, panels show dashboard)
- PNG tests: `clock-test.png`, `art-fit-test.png` saved in evidence dir.
**Safety considerations:**
⚠️ API keys: NEVER commit real API keys to git. The .env file must be in .gitignore. Only commit .env.example with placeholder strings. OAuth token files similarly gitignored.
**Known uncertainties:**
Widgets (b)(c)(d) blocked:delivery (user provides keys). It's acceptable to commit placeholder versions (render placeholder text if key not set). The boot verify still works with clock + art rotation. Stale-banner format: "Data stale as of HH:MM" — the exact banner position and font size are design choices; small bottom banner is fine.
**Failure response:**
Systemd service fails to start: `journalctl -u ai-frame.service -n 50` → inspect Python traceback. Missing venv path → fix ExecStart to match AF-124 recorded path. Reboot time > 60 s → check systemd-analyze blame (AF-122); if network-online delay → change After=network.target to not wait for full-online (dash starts with cached data first).
**Source references:**
- JIRA.md §Software Planning Stage 12 "Widgets + supervision"
- AF-117 EXP-016 (power recovery TTND metric — systemd Restart=on-failure is the enabler)
- AF-118 (cached fallback for network API) — this task implements the caching layer
- AF-124 (venv python path input to systemd ExecStart)
**Labels:** software nano critical-path yes docs blocked blocked:delivery
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-134: Item 1 Exact component dimensions measured. PSU (BOM P-01 HD-120V5A: stated 199×110×50 mm, VERIFY physical with calipers), controller(s) dims (IF WF4 wins: WF4 board actual W×H×D; IF ESP32 wins: 3×(ESP32-S3 + HCT perfboard/PCB dims), Nano dims, HUB75 cable slack needed per row, power harness branch slack per panel. Record every dim in a table with photo.

**Task ID:** AF-134
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 1 Exact component dimensions measured with calipers. PSU 199×110×50 mm (verify). Controller dims (WF4 or 3×ESP32+HCT per ADR-016). Nano dims. HUB75/power cable slack per row/panel. Dims table + photo.
**Description:** MF design cannot begin until we know the EXACT physical size of every component going inside the frame. Stated BOM dimensions are marketing numbers — actual measured dims matter because 1 mm error per component × 10 components = 1 cm gap or interference. Use digital calipers (resolution ±0.1 mm, ideally) or a metric ruler. Measure EVERY component. For components that vary per ADR-016 (controller), measure BOTH WF4 AND the 3× ESP32+HCT stacks (even if one will be unused) so the layout sketch (Item 2) can be done for either topology. Component list:
- **PSU (RD-65A or HD-120V5A, P-01 in BOM):** Width (left→right), Height (top→bottom, chassis including mounting feet), Depth (front→back, including AC receptacle protrusion at rear AND 5 V binding posts protrusion at front — max envelope, not just metal box). Stated: 199×110×50 mm. VERIFY. Photo next to ruler.
- **WF4 controller (IF ADR-016 = WF4 or just for the dims table completeness):** Board W×H. Component height (tallest thing on board: usually the electrolytic capacitor near 5 V input, or HUB75 connector plastic, or header pins). Board edge to USB-C connector protrusion distance.
- **3× ESP32-S3 devkits + HCT adapters (IF ADR-016 = ESP32 or just for dims):** (a) single ESP32-S3 board W×H×max-component-H. (b) single HCT perfboard OR populated Cond-X PCB (if PCB already done: its W×H×max-component-H) W×H×H. (c) Combined stack height if both boards are mounted parallel (standoff gap needed). (d) With HUB75 cable plugged into HCT connector: cable bend radius + cable exit dimension (how far does the connector stick out past the board edge?).
- **Nano (LicheeRV Nano-W or C-01 equivalent):** Board W×H. USB-C connector protrusion from edge. SD card slot protrusion. Antenna region no-go zone (antenna clearance 5 mm minimum, no metal over antenna).
- **Cable slack budgets per row:** For each of 3 rows (top, mid, bot): measure physical distance from controller location (Item 5 will place them, estimate from a rough mental sketch) to row's HUB75 panel input, PLUS add 50 mm service loop at each end. Total cable length per row. Confirm BOM H-18 (0.5 m) / H-19 (1 m) are adequate or if we need longer cables.
- **Power harness slack per panel branch:** Distance from PSU 5 V posts to each panel's 4-pin power connector, plus 40 mm service loop at PSU post end + 40 mm at panel end. Verify BOM harness branches (H-04a/b) are long enough.

Record all values in a Markdown table: Columns = Component, Measured W mm, Measured H mm, Measured D/Max-Z mm, Notes (protrusions, photo ref). Attach photos of each component next to a metric ruler/calipers in the photo.
**Why this task exists:** MF Items 2-14 (layout sketch, depth, cutouts, placement, cable routing) ALL depend on knowing exact dimensions. If PSU is actually 205 mm wide instead of 199, and the frame internal width was designed at 200, the PSU won't fit and we scrap the backplate. Calipers-measured dims eliminate this class of failure.
**Prerequisites:** COMPLETED: MR phase (AF-120 brightness ceiling thermal done; components not in active use so can be placed on bench to measure). COMPLETED: ADR-016 (to know WHICH controller to install, but measure BOTH anyway for the layout sketch contingency). Hardware on bench: PSU, WF4 controller, ESP32 + HCT perfboard stack (1 of each, multiply count for 3), Nano.
**Blocked by:** AF-120 (MR thermal done → system can be disassembled for component measurement), ADR-016 (controller topology known for installation dims — even though we measure both)
**Required hardware:** All components listed above. Digital calipers (±0.1 mm) OR metric ruler (mm marks, ±1 mm acceptable if calipers not available). Digital camera or phone camera.
**Required tools:** Calipers (preferred) or metric ruler. Notebook for dims.
**Required software:** None (measurements are physical).
**Exact execution steps:**
1. **Bench prep:** Clear ESD-safe work area. Lay metric ruler or calipers on bench as photo size reference.
2. **PSU dims (3 measurements + photo):**
   a. W (longest side): calipers from left edge metal to right edge metal. Include any side mounting flanges that stick out.
   b. H (top to bottom): calipers from bottom rubber feet base to top of metal box. Include any feet protrusion.
   c. D (front to back MAX envelope): measure from the FARTHEST forward 5 V post screw tip to the FARTHEST rear protrusion (C14 rear tab + any AC cord connector when mated? No — measure PSU alone, but note the additional "C13 cord bend radius needed" in a separate row).
   d. Photo: PSU on its side next to calipers/ruler showing each axis.
3. **WF4 dims:** W, H of PCB board rectangle. Max Z height = tallest component (capacitor/connector/header pin tips from PCB bottom to highest peak). USB-C protrusion past board edge. Photo.
4. **ESP32 + HCT perfboard dims (×1 stack):**
   a. ESP32 standalone W×H×Z.
   b. HCT perfboard standalone W×H×Z.
   c. Combined stack mounted (standoff gap): measure total Z height including standoffs. ESP32 stacked above or beside HCT? If beside: total footprint W + H of combined side-by-side layout.
   d. HUB75 cable plugged in: how far does the cable + plug stick out past the HUB75 connector board edge? (Bend radius measurement.)
   e. Multiply by 3 for total 3-row controller volume in notes.
5. **Nano dims:** W×H. USB-C protrusion from edge. SD card protrusion. Antenna region (typically a no-metal zone on one corner of WiFi boards — mark as 5 mm minimum clearance from any metal standoffs/chassis). Photo.
6. **Cable slack budgets (mental/sketch estimate):**
   a. Controller-to-panel HUB75 length per row: estimate controller location ~150 mm below its row (controllers stacked at bottom of frame) or near middle of each row. Distance = 300 mm typical. Add 50 mm each end service loop = 400 mm. Compare to BOM H-18 (500 mm) / H-19 (1000 mm). Document adequacy.
   b. Panel power branch length: PSU in bottom-left → panel #6 (top-right): 1500 mm worst-case diagonal. Add 40+40 mm loops = 1580 mm. BOM harness length? Check BOM H-04a/b length field; if shorter, note "H-04 branch #6 too short, need custom wire" as finding.
7. **Compile Markdown table with all rows.** Attach photo references per row.
**Expected result:** Dims table with ≥ 12 rows (PSU, WF4, ESP32, HCT, Nano × W/H/D each + cable slack rows). Photos attached per component. Stated vs measured deviation recorded (e.g., PSU stated 199 mm, measured 201.3 mm → +2.3 mm).
**Acceptance criteria / DoD:**
- Markdown table: Component / W mm / H mm / D mm / Notes ≥ 12 data rows.
- Every measured dimension: actual value recorded (not repeated from BOM stated). If stated ≠ measured, explicit "stated=X, measured=Y, delta=±Z" note.
- Calipers/ruler reference in every component photo.
- Cable slack budgets: BOM adequacy noted (OK/too short/too long).
**Evidence to save:**
- `docs/pm/evidence/AF-134-component-dims.md` (full table)
- `docs/photos/mf-dims/psu-w.jpg, psu-h.jpg, psu-d.jpg`
- `docs/photos/mf-dims/wf4-board.jpg, esp32-board.jpg, hct-perfboard.jpg, nano-board.jpg` (each with ruler/calipers in frame)
- `docs/photos/mf-dims/cable-slack-sketch.jpg` (hand sketch with length arrows if drawn)
**Safety considerations:**
⚠️ Calipers around PCB edges: sharp PCB corners can scratch. Slide slowly, don't force. If measuring live components (not recommended — bench measure with all power OFF), calipers metal tips near 5 V posts → PSUs CAPACITORS CAN HOLD CHARGE EVEN AFTER UNPLUGGED. Ensure PSU discharged: wait ≥ 10 min after unplug, then (optional) discharge 5 V output caps with 1 kΩ resistor across posts before measuring.
**Known uncertainties:**
Cable slack budget dimensions rely on controller placement estimates from Item 2 (which itself uses these dims). Chicken-and-egg: do a rough estimate here, then refine after Item 2 layout sketch. The first pass is just "BOM cables long enough in principle." Combined ESP32+HCT Z-height depends on whether we use 5 mm or 10 mm standoffs. Measure both options and take the max for clearance planning.
**Failure response:**
Dims critical mismatch (PSU > stated by 10+ mm) → re-do Item 3 (frame depth minimum) with the larger measured dim before any cutting. BOM cables too short → create a purchasing task (or use spare H-19 1 m cables for all rows if needed).
**Source references:**
- JIRA.md §Mounted Frame Phase Item 1 "Exact component dimensions measured"
- BOM.md P-01 PSU, C-03 WF4, C-05a ESP32, H-05 HCT245, C-01 Nano, H-18/H-19 cables (stated dims / lengths to verify against)
- DECISIONS.md ADR-016 (controller choice: know which stack goes inside — 1 WF4 or 3× ESP32+HCT)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-135: Item 2 Internal layout sketch (component placement with X/Y coordinates on grid paper or digital drawing app, mm or cm scale). Clearance distances: ≥10 mm between any 5 V high-current path and any mains AC conductor path. Airflow channels marked from PSU intake fan to exhaust vents. Nano accessible for USB service. Service loops ≥50 mm at each connector.

**Task ID:** AF-135
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 2 Internal layout sketch with X/Y coords (mm/cm scale). 10 mm AC/5V clearance min. Airflow channels PSU intake→exhaust. Nano USB service access. 50 mm service loops every connector.
**Description:** Design the internal layout BEFORE cutting anything. This is the "CAD on paper" pass that prevents the #1 MF mistake: "I'll just place things by eye, then find the PSU doesn't fit." Medium choice: (a) traditional grid paper (1 mm or 5 mm grid), sketch with pencil, scale 1:5 (1 cm = 50 mm real) or 1:2. (b) digital: FreeCAD Sketcher, LibreCAD, Figma frames with mm grid, Inkscape with mm page size. Any tool that lets you draw rectangles to exact measured dims (AF-134 values). Layout rules, VERBATIM from JIRA MF Item 2:
- **Scale and coordinates:** Choose origin (0,0) = bottom-left corner of internal frame area (inside the chassis walls, backplate surface X/Y plane). Place every component as a rectangle using its EXACT AF-134 measured W/H dimensions, not rounded. Rectangle position = (X_bottom_left_mm, Y_bottom_left_mm) in internal coordinates.
- **AC/DC separation ≥10 mm:** ANY conductor that carries mains AC (L/N/PE wires in AC harness, PSU AC input side, C14 inlet body, fuse holder, switch) must have a 10 mm CLEARANCE ZONE (no overlap) from ANY conductor that carries 5 V DC (panel power harness, 5 V posts, controller 5 V pins, USB 5 V). This includes the ROUTING PATHS of cables, not just components. Draw the cable paths as lines; ensure 10 mm perpendicular gap between any AC-cable-line and any 5V-cable-line.
- **Airflow channels:** Identify PSU intake fan location (from Item 4's design later, but sketch direction now). Draw a shaded channel from intake vents → across PSU fins → to exhaust vents. Ensure NO large components or cable bundles block this channel (cables crossing airflow channel must be perpendicular and thin, not parallel bundles along the channel).
- **Nano USB service access:** The Nano's USB-C port must be within 20 mm of a frame side panel that is removable (or a dedicated USB cutout hole in back/bottom panel). Draw a "USB service access zone" arrow from Nano USB port to the nearest panel edge with a cutout or removable panel.
- **Service loops ≥50 mm at each connector:** For EVERY cable end (PSU AC-in, PSU 5V-posts, each panel power connector, each HUB75 at both panel end and controller end, Nano USB, every branch split) draw a loop of diameter ~10 mm (cable 50 mm extra length = 2× semicircle) at the connector location. This slack is required for disassembly without re-crimping.
- **Component list to place:** PSU (AF-134 W×H), controller(s): WF4 single or 3× ESP32+HCT stacks, Nano, optional future fan (40×40 or 60×60 mm placeholder), cable routing lines for 6× panel power branches, cable lines for 3× HUB75 rows, AC compartment region (if physical barrier chosen in Item 6 — draw the barrier line here too).

Save the sketch as PDF/photo with scale legend. Cross-reference with Item 3 (frame depth minimum = max Z-component + clearance both sides) to confirm sketch fits in the chosen depth.
**Why this task exists:** ADR-024 explicitly DEFERs cutting until ALL dims/thermal/schematic inputs known. Item 2 layout sketch is THE integration document that all subsequent Items 3-15 derive from. If Item 2 is wrong, Items 4 (holes in wrong place), 5 (controllers where HUB75 can't reach), 7 (cables too short by 10 mm), 14 (mounting holes collide with Nano SD card) all fail. The 10 mm AC/DC clearance is a SAFETY requirement for the closed frame.
**Prerequisites:** AF-134 (exact dims measured for every component that goes in the sketch).
**Blocked by:** AF-134
**Required hardware:** Grid paper + pencil + ruler (option A), OR computer with drawing app (option B). AF-134 photos/dims table open for reference.
**Required tools:** Ruler (mm). Compass or circle template for service loops.
**Required software:** Optional: CAD/drawing app (LibreCAD, FreeCAD Sketcher, Inkscape, Figma). If none, pen/paper is acceptable.
**Exact execution steps:**
1. Choose medium (paper/digital). Choose scale (e.g., 1 grid square = 10 mm → easy to count).
2. Draw outer boundary: internal usable area. Outer = total frame outer dims (from Item 3 later: approx 1536 mm wide + bezel, 1152 mm tall + bezel — but use placeholder internal = 6-panel active area minus 10 mm bezel each side for first pass; refine later).
3. Place each component rectangle EXACT size from AF-134:
   a. **PSU location candidate:** bottom-left or left side (intake fan facing bottom/left, exhaust facing opposite). Draw rectangle.
   b. **Controller(s):** WF4 → near middle bottom, close to all 3 rows. 3×ESP32 → one per row (top/mid/bot) on right side near their rows.
   c. **Nano:** near bottom-right corner, USB-C port facing RIGHT edge (15 mm to edge) for rear-panel USB cutout access.
   d. **Optional fan placeholder:** 60×60 mm near PSU exhaust if thermal needs it.
4. Draw AC region: a dashed box around C14 inlet, fuse, switch, PSU AC-input. Label "AC SIDE". Draw DC region: dashed box around 5 V posts, controllers, Nano, signal cables. Label "DC SIDE". CHECK 10 mm clearance between any AC rectangle edge and any DC component rectangle edge. If violating → move components.
5. Draw CABLE ROUTES as lines:
   a. 6× panel power branches: from PSU 5 V posts → each panel corner (6 destinations). Lines must not enter AC zone. Service loop (small circle) at post end and at each panel end.
   b. 3× HUB75 rows: from each controller row port → top row panels, mid row panels, bot row panels. Lines cross 90° to power branches if they must cross (not parallel).
   c. AC lines: from C14 → fuse → switch → PSU AC input. Keep within AC zone. Do NOT exit AC zone.
   d. Nano USB cable: from Nano USB port → rear panel cutout.
   CHECK 10 mm between any AC-cable-line and any 5V-power-cable-line. If too close → reroute.
6. Airflow: shade PSU intake side → PSU box → exhaust side. Ensure no cable bundles parallel to airflow. Cables crossing airflow must be perpendicular and single.
7. Service access: draw arrow from Nano USB to nearest panel edge. Label "USB SERVICE CUTOUT".
8. Service loops: at each connector endpoint, draw a circle diameter ~10 mm (cable 50 mm extra length). Label loops "50 mm loop".
9. Save sketch with scale bar. Double-check all rules.
**Expected result:** Sketch file (PDF/PNG/JPG). All components placed at exact AF-134 dims. 10 mm AC/DC clearance measured on sketch between any AC/DC pair (use ruler). Airflow channel unobstructed. Nano USB service arrow present. ≥ 16 service loops visible (6 panel power × 2 ends = 12, + 3 HUB75 × 2 ends = 6 → actually ≥ 18 loops total in whole sketch).
**Acceptance criteria / DoD:**
- Sketch saved with scale legend. Every component labeled with its X/Y bottom-left coordinate + W/H.
- 10 mm clearance check: measure 3 random pairs (AC cable corner vs 5 V cable corner) — all pass. Measure AC box edge vs DC controller edge — pass.
- Airflow channel: no large component or 2+ cables parallel inside shaded airflow channel.
- Nano service: USB port rectangle edge ≤ 20 mm from frame panel edge; cutout arrow drawn.
- Service loops: count ≥ 10 loops (each connector end has at least one; 6 panels × 2 + 3 rows × 2 + PSU AC + PSU DC = many).
**Evidence to save:**
- `docs/mf-layout-sketch.pdf` or `.png` (the sketch file)
- `docs/pm/evidence/AF-135-layout-notes.md` (scale chosen; component coordinates table; 10 mm clearance spot-check results; any rule-violations-fixed notes)
- If paper: `docs/photos/mf-layout/layout-sketch-photo.jpg` (photo of paper sketch with ruler overlay)
**Safety considerations:**
⚠️ 10 mm AC/DC clearance IS A SAFETY REQUIREMENT, not a guideline. If the sketch cannot achieve 10 mm everywhere (e.g., tight frame), YOU MUST REARRANGE. Acceptable fallbacks: (a) increase frame inner size by 10 mm in that axis; (b) add a physical plastic barrier BETWEEN them (Item 6) which reduces the clearance requirement but the barrier itself takes space. NEVER accept < 10 mm without a barrier.
**Known uncertainties:**
Exact outer frame dims depend on Item 3 (depth) and Item 14 (panel layout + bezel). Iterate: do a first-pass sketch with estimated outers now; after Item 3 and Item 14 are known, update sketch coordinates once. The first pass validates topology (left/right AC/DC separation works, Nano reaches USB edge, etc.).
**Failure response:**
Can't fit everything with 10 mm clearance → increase internal frame size in the tight axis. If width is the issue, shift controllers 10 mm right. If depth is the issue, see Item 3 (depth can be increased from 80 mm baseline to 90 mm easily). Sketch topology completely infeasible → start over with PSU on opposite side.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 2 "Internal layout sketch X/Y coords"
- AF-134 (component dims input)
- AF-139 Item 6 later (physical barrier — may be needed if clearance sketch borderline even after rearrangement)
- ADR-024 DEFER cutting rule (sketch first)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 docs safety-review critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-136: Item 3 Minimum frame depth determined. PSU 50mm is hard lower bound; add PCB/cable clearance min 15mm each side = 80mm baseline; finalize depth number with justification. If fans needed add their depth.

**Task ID:** AF-136
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 3 Minimum frame depth determined. PSU 50 mm hard lower bound. +15 mm PCB clearance each side = 80 mm baseline. Final depth justified. If AF-143 fan needed, add fan depth.
**Description:** Compute the FRAME INTERNAL DEPTH (Z-axis, from panel rear PCB surface to backplate inner surface). This number drives: backplate standoff length, side panel extrusion depth, all cutout depths for C14/USB/C13. Components stacked along Z (worst-case cross-section somewhere in the frame), list ALL layers with their Z-thickness:
1. **Panel rear side PCB standoff layer:** panels attach to backplate via standoffs (≥5 mm clearance from backplate to panel PCB components — Item 14/15). Layer 1 = standoff height (≥5 mm). This is BETWEEN backplate and panel PCB backside.
2. **Component side of panel PCB:** if any tall component on panel rear (capacitors, connectors) → but our standoff already covers this if standoff > component height.
3. **Wiring/cable layer between panel PCB back and PSU/controller boards:** typical flat harness + HUB75 ribbon bundle = 5-10 mm. Assume worst = 10 mm.
4. **PSU depth (or max-Z controller board, whichever is LARGER):** PSU D-measurement from AF-134 (stated 50 mm; actual measured maybe 50-53 mm). Take the MAXIMUM Z component across all boards (PSU vs WF4 vs ESP32+HCT stack vs Nano + tallest connector). Layer 4 = max_component_Z_mm.
5. **Clearance between component top face and backplate (or between component top face and panels if they face the other way):** MINIMUM 15 mm for air circulation + cable bend radius on the "back side" of the tallest component (opposite where the wiring is). If clearance <15 mm, cables can't bend, no air.
6. **Optional cooling fan depth:** if AF-143 decides fan required (40 mm or 60 mm axial fan depth = 10-25 mm typical depending on model), we need to mount the fan somewhere in the stack so add its full depth to the required internal Z.

SUM the layers for the total required internal depth. Round UP to nearest 5 mm for material convenience (aluminum extrusion slots come in standard depths: 80 mm, 90 mm, 100 mm are all standard). Write justification paragraph: why we chose that number, listing each layer's contribution. If we round up from 82 to 90, the extra 8 mm = bonus airflow margin.
**Why this task exists:** Depth is the hardest dimension to change after cutting: if side panels are 3D printed 80 mm and later find PSU + cables need 85, we reprint all 4 sides. Explicit layer-by-layer sum makes the decision quantitative not guesswork.
**Prerequisites:** AF-134 (max-Z dimensions of every component measured). Item 2 layout sketch (cross-section knowledge: which spot in the layout has the worst-case stacked Z?).
**Blocked by:** AF-134, AF-135 (partial — layout cross-section location known)
**Required hardware:** None yet (calculation only for now; material purchase after depth locked).
**Required tools:** Calculator.
**Required software:** None.
**Exact execution steps:**
1. From AF-134 dims table, extract max-Z values:
   - PSU_D_mm = ___ (e.g., 50.0)
   - WF4_Z_mm = ___
   - ESP32_HCT_STACK_Z_mm = ___ (for ADR-016 winner)
   - Nano_Z_mm = ___
2. max_component_Z = max(PSU_D, winner_controller_Z, Nano_Z) = ____ mm.
3. Layer sum, worst case:
   - Layer A: panel rear standoff = ≥ 5 mm. Use 5 mm.
   - Layer B: cable/wiring run between panels + component face = 10 mm (conservative).
   - Layer C: max_component_Z = ____ mm (step 2).
   - Layer D: clearance opposite side = 15 mm minimum.
   - Subtotal WITHOUT fan = A + B + C + D = ____ mm.
4. Fan check: inspect AF-143 plan (even though it's later, we can assume worst case now). If Item 10/14 thermal analysis CLOSED BOX PASSIVE COOLING is questionable → proactively add fan depth buffer. Axial fan typical: 40×40×10 mm (depth 10 mm), 60×60×25 mm (depth 25 mm). Use the larger 25 mm as planning buffer. Fan subtotal = 25 mm. Placeholder note: "Fan depth 25 mm reserved. Actual fan decided in Item 11; if no fan needed, we just get extra 25 mm airflow margin at no cost."
   - Subtotal WITH fan planning buffer = Subtotal_no_fan + 25 mm = ____ mm.
5. Round UP final internal depth to nearest 5 mm or standard extrusion slot:
   - If Subtotal ~80 mm → final 80 mm (baseline).
   - If Subtotal 83-85 mm → 90 mm.
   - If Subtotal 92-95 mm → 100 mm.
   Final depth = ____ mm.
6. Write justification paragraph enumerating each layer. Note the fan placeholder: "25 mm reserved as fan buffer per Item 11 decision; if passive cooling confirmed sufficient at Item 11, buffer remains as extra airflow margin (acceptable, no rework needed)."
**Expected result:** Final internal depth number (mm, 5 mm granularity). Layer-by-layer arithmetic. Justification paragraph. Fan buffer note.
**Acceptance criteria / DoD:**
- max_component_Z computed from AF-134 actual measurements (not BOM stated).
- 4-layer sum (A/B/C/D) computed. Values per layer documented.
- Fan buffer 25 mm explicitly considered: either "added to sum" or "thermal expects passive OK but still reserved 25 mm as safety margin".
- Final internal depth rounded UP to standard size (80/90/100 mm). Justification paragraph references all layers.
**Evidence to save:**
- `docs/pm/evidence/AF-136-frame-depth.md` (layer table with numbers; sum; final depth value; justification paragraph; fan buffer note)
**Safety considerations:**
N/A — dimension calculation. Thermal risk later if depth is TOO SHALLOW → that's why we add 15 mm clearance + optional 25 mm fan buffer.
**Known uncertainties:**
Actual standoff heights from backplate to panels: Item 15 will install standoffs. We assumed standoff ≥5 mm; if panel tall components require 8 mm standoffs, Layer A becomes 8 → total depth +3 mm (absorbed by round-up buffer).
**Failure response:**
After Item 11 (thermal testing), we discover depth too shallow (airflow choked, temps exceed limits). Mitigation: if side panels are aluminum extrusion, extrusions are modular and can be replaced with deeper profile. If wooden, the backplate cutouts are still compatible; side rails are re-cut. Mitigation cost = moderate. This is why we round UP to the next standard size — avoids this scenario.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 3 "Minimum frame depth determined"
- AF-134 (PSU 50 mm, component Z dims input)
- AF-143 Item 11 later (fan decision; 25 mm reserved now to avoid depth-change rework)
- Item 15 standoff installation (standoff height ≥5 mm input to Layer A)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-137: Item 4 PSU location + air intake/exhaust holes design. PSU intake fan = left side or bottom; exhaust = opposite side. Verify NO power cables or signal cables routed DIRECTLY in front of PSU fan grille (would block airflow → overheat → PSU shutdown). Hole pattern: array of 5mm diameter holes 8mm pitch or equivalent.

**Task ID:** AF-137
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 4 PSU location + intake/exhaust hole pattern. Intake left or bottom, exhaust opposite side. NO cables in front of fan grille. Array 5 mm holes × 8 mm pitch or equivalent.
**Description:** Finalize PSU location from Item 2 sketch and design the ventilation hole pattern on the frame side panels/backplate where the PSU intake and exhaust align. Steps:
1. **PSU location confirmation on layout:** From Item 2 sketch, read PSU rectangle coordinates (X,Y,W,H). Confirm intake fan side (usually PSU has a sticker "airflow direction arrow" — arrow points OUT from exhaust side). Rule: intake from low-velocity side (bottom or left panel, away from user's face if wall-mounted); exhaust to opposite side (top or right).
2. **Cable block verification:** Overlay Item 2 cable routes on PSU rectangle's fan grille area. PSU fan grille is typically ~40% of one face. Draw a rectangle "FAN ZONE" representing the active grille area. CHECK Item 2's cable routes: any power branch cable or HUB75 cable line passing through the FAN ZONE rectangle? → IF YES → MOVE those cables in Item 2 sketch (reroute around PSU side, not across the fan face). PSU fan blocked by cable bundle = PSU overheats at 100% brightness → PSU thermal shutdown → panels blank. This is a common failure.
3. **Hole pattern design (intake panel AND exhaust panel):** On whichever frame panel aligns with PSU intake (e.g., left side panel if PSU intake faces left), AND on the opposite panel (exhaust), design a ventilation hole array. Requirements:
   - Hole diameter: 5 mm (easy drill bit size, small enough that no finger can touch live parts inside, complies with IP2X minimum if panel is metal).
   - Hole pitch (center-to-center distance): 8 mm minimum (so 3 mm web between holes, structural integrity preserved for MDF/acrylic).
   - Hole area coverage: total open area of the hole array should be ≥ the PSU fan grille's open area. PSU grille area = π × (fan_radius)^2 × (1 - obstruction). Typical 40 mm fan grille = ~1000 mm² open. Number of 5 mm holes needed: each hole area = π × 2.5² = ~19.6 mm². Count N_holes = ceil(1000 / 19.6) = 52 holes minimum. Round up to 60 = 10 rows × 6 cols or 8×8.
   - Layout: rectangular grid pattern aligned to PSU grille location. Draw the hole positions on the layout sketch or a dedicated cutout diagram with coordinates so Item 15 can drill accurately.
4. **Mark hole centers with coordinates on the panel cutout drawing:** for each panel (intake/exhaust side), list each hole's (X_from_panel_edge_mm, Y_from_panel_edge_mm).
**Why this task exists:** PSU thermal shutdown at 100% brightness = user complaint "my frame randomly goes blank after an hour." #1 cause = blocked intake/exhaust (cables or insufficient hole area). Item 4's explicit cable-block check + hole-count math guarantees adequate cooling at design time.
**Prerequisites:** AF-135 (layout sketch with PSU rectangle + cable routes). AF-136 (frame depth known → panel dimensions for hole placement relative to edges).
**Blocked by:** AF-135, AF-136
**Required hardware:** PSU in hand to verify which side is intake (fan visible on that side) vs exhaust (arrow sticker direction).
**Required tools:** None (hole design is paper/CAD; drilling happens Item 15).
**Required software:** Drawing app if digital sketch, else pencil + compass on paper.
**Exact execution steps:**
1. **PSU airflow direction check physically:** Examine PSU. Find the fan: one side has visible fan blades = INTAKE. Opposite side has sticker with airflow arrow (usually pointing OUT) = EXHAUST. Confirm: hold a strip of paper near fan side when PSU is on (bench energized safely with no load for 5 s) → paper pulled toward fan = intake; pushed = exhaust. (Skip energizing if unsafe; just trust the arrow sticker.)
2. **Map to layout:** On Item 2 sketch, label PSU rectangle: "INTAKE → LEFT EDGE, EXHAUST → RIGHT EDGE" (example).
3. **Cable-block check:** Zoom to FAN ZONE rectangle (approximate the grille size, e.g., 40×40 mm, centered on intake side face). Scan Item 2 cable lines (panel power branches + HUB75 rows + USB cable). Does any cable line pass through FAN ZONE? If yes → reroute. If cable must pass nearby, pass at 90° along the EDGE of the FAN ZONE (not through center 50%). After reroute: 0 cables inside FAN ZONE inner 80% area.
4. **Hole count math:**
   - Fan grille open area estimate: ____ mm² (measure grille, or use typical: 40 mm fan = ~1000 mm²; 60 mm = ~2000 mm²).
   - 5 mm hole area = 19.6 mm² each.
   - Minimum holes N_min = ceil(open_area / 19.6) = ____.
   - Design count N_design = max(N_min, 48) (floor at 48 for good measure).
   - Arrange N_design as grid: e.g., 8 holes wide × 6 holes tall = 48 (pitch 8 mm → total pattern width 56 mm, height 40 mm — fits 40×56 over fan).
5. **Hole coordinates:** For intake panel: origin = bottom-left corner of panel. Hole grid: start_x = 20 mm (20 mm from panel edge), start_y = (panel_height - pattern_height)/2 (centered vertically). Grid i × j: hole (i,j) = (start_x + i*8, start_y + j*8). Same for exhaust panel on opposite side (grid mirrored).
6. Update layout sketch: draw the hole grid dots on the panel sides at the intake/exhaust positions. Save updated file.
**Expected result:** 0 cables inside PSU FAN ZONE inner 80%. N_design ≥ N_min holes, 5 mm diameter × 8 mm pitch. Hole coordinate table for both panels. Updated layout sketch with hole dots.
**Acceptance criteria / DoD:**
- PSU intake/exhaust sides: physically verified (not assumed) and documented.
- Cable-block check: Item 2 cable routes rerouted if needed. Result = 0 cables pass through FAN ZONE central area. Reroute evidence in updated sketch.
- Hole math documented: open_area, N_min, N_design, grid dimensions.
- Hole coordinate list for both panels (intake and exhaust): start_x, start_y, pitch, rows × cols.
- Updated layout sketch file with hole dots drawn.
**Evidence to save:**
- `docs/pm/evidence/AF-137-psu-ventilation.md` (intake/exhaust sides; cable-block check pass/fail before vs after reroute; hole math table; hole coordinates per panel)
- Updated layout sketch file (`docs/mf-layout-sketch-v2.png`) with hole dots and updated cable routes
- Optional: `docs/photos/mf-layout/psu-fan-side.jpg` (photo of PSU fan side for reference)
**Safety considerations:**
⚠️ Thermal: blocked PSU fan is a SAFETY issue at high load. PSU overheats → thermal cutoff should engage (good, designed-in) but worst case (cheap PSU no cutoff) → component degradation or capacitor swelling. Hole diameter 5 mm is finger-safe for IP20 (child finger can't reach through 5 mm round hole to live parts). Do NOT use > 12 mm holes for ventilation without adding an internal finger guard mesh.
**Known uncertainties:**
Exact fan grille open area: if unknown, overdesign by 1.5× (60 holes instead of 40) — cost is zero (a few extra drill strokes), downside is minor structural weakening only if we go to 500+ holes. Hole pitch 8 mm minimum: for 12 mm MDF backplate, 5 mm hole at 8 mm pitch leaves 3 mm web between holes = structurally fine for <100 holes in a 60×80 area.
**Failure response:**
After Item 14 build (closed-box thermal test), PSU temp >60C → add more holes (drill additional 20 holes in the same grid area), or install axial fan (Item 11 plan) — the 25 mm depth buffer in Item 3 leaves room.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 4 "PSU location + intake/exhaust holes"
- AF-135 (layout sketch: PSU rectangle, cable routes, updated v2 with holes)
- EXP-015 / AF-116 (PSU thermal baseline: at 100% brightness, PSU chassis temp goal <60C)
- AF-144 Item 12 closed-box thermal (validates this design was adequate)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 thermal docs safety-review critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-138: Item 5 Controller location + Nano location. Controller(s) near their respective rows to minimize HUB75 cable length. Nano near edge for USB-C access (debug cable without opening frame). USB cable service loop.

**Task ID:** AF-138
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 5 Controller + Nano placement finalized. Controllers near respective rows (min HUB75 length). Nano near edge with USB-C cutout access. USB service loop.
**Description:** Lock the EXACT positions (X,Y bottom-left coords) of every controller board and the Nano on the layout sketch. Two placement regimes based on ADR-016 winner:
**Regime A — WF4 wins (1 controller board):**
- Place WF4 board VERTICALLY centered, near the BOTTOM EDGE of the frame, slightly to left or right of the PSU (avoid blocking PSU exhaust). WF4 drives 3 rows via 3 X-ports (X1/X2/X3): HUB75 cables from WF4 go UP to top row (longest cable), mid row (medium), bot row (short). Acceptable HUB75 length budget per row: top ≤ 1000 mm (BOM H-19 1m works), mid ≤ 700 mm, bot ≤ 400 mm. Draw arrows from WF4 X1→top row panels, X2→mid, X3→bot. Cable routing: all three HUB75 cables exit WF4 on the same side facing the panels (not the backplate), travel vertically upward together in a single bundle for the first section, then split at their row's Y coordinate.
**Regime B — 3× ESP32 wins (3 controller boards):**
- Place one ESP32+HCT board stack DIRECTLY BEHIND each row's physical center. Top row controller at Y ≈ canvas_top - 30 mm (behind top row panels center). Mid row controller at Y ≈ canvas_mid. Bot row controller at Y ≈ canvas_bot + 30 mm. X coordinate = right side of frame, so HUB75 cables travel LEFT a short distance (~300-500 mm) into the 2 panels of each row. HUB75 per row cable length ≤ 600 mm → BOM H-18 (0.5 m) nearly fits, may need H-19 (1 m) for top row if routing around obstacles.
**Nano placement (BOTH regimes):**
- X: near RIGHT frame edge, within 20 mm of the removable side panel OR rear-panel USB cutout. Y: bottom 1/4 of frame, near the controllers (short UART/USB transport cable to WF4/ESP32). USB-C port on Nano MUST FACETOWARD the nearest panel edge/cutout. Draw the USB cutout rectangle on that panel edge (8 mm × 12 mm minimum to fit USB-C plug housing). Service loop: USB cable from Nano to cutout = 50 mm extra slack, drawn as a loop between Nano and cutout.
Update Item 2 layout sketch with: exact controller X/Y coords per regime, Nano X/Y, USB cutout rectangle on relevant panel edge, HUB75 cable arrows with labeled lengths, transport connection arrow between Nano and controllers.
**Why this task exists:** Minimizing HUB75 cable length = minimizing EMI and signal integrity problems (HUB75 is unbalanced 3.3 V logic, long cables cause crosstalk → garbled rows). USB service access = "user can plug in USB debug cable without removing 6 panels from the wall." This is a JIRA MF requirement #5 explicitly.
**Prerequisites:** AF-135 (layout sketch base exists, Item 2). AF-136 (depth OK). ADR-016 (winner known → Regime A or B).
**Blocked by:** AF-135, ADR-016
**Required hardware:** Controllers and Nano in hand (from bench) to verify connector side matches sketch connector orientation (USB-C on Nano should actually face the panel edge we placed it near).
**Required tools:** Ruler.
**Required software:** Drawing app if digital.
**Exact execution steps:**
1. **Regime select:** Read ADR-016 Decision. Regime = A if WF4, B if ESP32.
2. **Controller placement:**
   **A (WF4 single):** Choose (X, Y) = (PSU_X + PSU_W + 20 mm, 30 mm from bottom). 20 mm gap from PSU = AC/DC clearance (Item 2 rule). Check HUB75 reach: X1 to top row panels distance = approx canvas_height - 30 mm ≈ 1120 mm. BOM H-19 = 1000 mm → close. Either: (a) move WF4 UP to Y = (canvas_height/2 - WF4_H/2) center position, so top cable shorter at ~600 mm and bottom cable ~600 mm (equal lengths, both fit H-19 1m). Better for uniformity. Document X/Y coords. Draw 3 arrows: X1 → top row input, X2 → mid, X3 → bot. Label arrow lengths in mm.
   **B (3× ESP32):** For each of 3 rows, controller X = frame_internal_width - ESP_stack_W - 10 mm (right side, 10 mm clearance from edge). Y positions:
     - Top ctrl Y = top_row_panels_bottom_Y - ESP_stack_H - 10 mm (behind top row, slightly below).
     - Mid ctrl Y = mid_row_center_Y - ESP_stack_H/2.
     - Bot ctrl Y = bot_row_panels_top_Y + 10 mm (behind bot row, slightly above).
   Write 3 (X,Y) pairs. Draw arrow from each HCT HUB75 output → leftward into its row panels. Label each arrow length. Each ≤ 600 mm.
3. **Nano placement (both regimes):**
   - X = if USB cutout on RIGHT panel → X = internal_width - Nano_W - 5 mm (port facing right edge). If cutout on REAR panel → any X, but within 20 mm of rear edge on Z-axis (depth direction). Rear USB cutout easier than side.
   - Y = 30 mm from bottom, next to WF4 or next to bot-row ESP32 controller. Keep Nano transport cable to controller(s) short (<300 mm UART/USB).
   - USB-C connector on Nano FACES the cutout panel. If connector actually faces wrong direction in real life → rotate Nano coords 180° before finalizing.
   - Draw USB cutout rectangle on the chosen panel (rear/side): size 10 mm × 14 mm (fits USB-C plug with clearance). Center it aligned with Nano USB port.
   - Service loop: draw 50 mm loop between Nano USB and cutout on the internal side.
4. Transport wiring arrow: Nano → each controller (UART cable or USB cable or TCP Ethernet). Label length (Nano next to bot controller = ~150 mm to bot, ~600 mm to top controller if regime B). Acceptable for UART at 921600 baud.
5. Save updated sketch (v3) with all coordinates, arrows, cutouts.
**Expected result:** 1 or 3 controller positions with exact (X,Y), 1 Nano position (X,Y). HUB75 per-row cable lengths: all ≤ H-19 (1 m). USB cutout drawn aligned to Nano connector. Service loops. Updated sketch file.
**Acceptance criteria / DoD:**
- Regime documented (A/B per ADR-016). Coordinates table for each controller and Nano.
- HUB75 per-row lengths all documented. Max ≤ 1000 mm (H-19 length). If any > 1000 → move controller, note adjustment.
- Nano USB-C port FACING panel edge with cutout rectangle. Cutout rectangle on sketch. USB service loop drawn.
- Transport cable Nano→controllers drawn with length.
- Updated layout v3 sketch saved with annotations.
**Evidence to save:**
- `docs/pm/evidence/AF-138-placement.md` (regime; controller X/Y table; Nano X/Y; HUB75 length table per row; USB cutout location/size)
- Updated sketch file `docs/mf-layout-sketch-v3.png` with controllers/Nano placed, arrows, cutout
**Safety considerations:**
N/A — placement on sketch. Later electrical routing follows this.
**Known uncertainties:**
USB cutout on REAR panel = easier for machining (drill 2 holes + file rectangular) vs side panel extrusion. Rear cutout means user reaches BEHIND the wall-mounted frame to plug in USB → may need to lift frame slightly off French cleat top hook. Side cutout: user plugs in from the side, no frame lift, but frame side rail must have cutout. Evaluate access; document choice.
**Failure response:**
HUB75 length exceeds 1000 mm for a row → (a) move controller closer to that row (preferred); (b) purchase longer HUB75 cable (1.5 m, AliExpress easy) if can't move controller. Nano USB faces wrong direction → rotate Nano on sketch 180°, accept that SD card slot now faces different direction (SD card still accessible if Nano has rear cutout).
**Source references:**
- JIRA.md §Mounted Frame Phase Item 5 "Controller location + Nano location"
- ADR-016 (winner → Regime A vs B)
- BOM H-18 (0.5 m HUB75), H-19 (1 m HUB75)
- AF-135 layout sketch (updated to v3)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-139: Item 6 Mains / low-voltage separation PHYSICAL BARRIER. Separate AC compartment (C14 inlet, fuse, switch, AC wires, PSU AC input) from DC compartment (5 V output posts, panel power branches, controllers, Nano, signal cables). Barrier = plastic sheet or double-wall construction. Creepage distance ≥8 mm, clearance ≥4 mm between any AC conductor and any accessible DC part/chassis. NO shared routing paths: AC harness enters AC side only, DC harness exits DC side only.

**Task ID:** AF-139
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 6 AC/DC physical barrier installed. AC compartment (C14/fuse/switch/PSU-AC-in) separated from DC compartment (5 V posts/branches/controllers/Nano/signal). Plastic sheet or double-wall. Creepage ≥8 mm, clearance ≥4 mm. NO shared cable paths — AC only AC-side, DC only DC-side.
**Description:** MF Item 6 is a SAFETY-CRITICAL requirement. Even if Item 2's sketch achieved 10 mm air clearance, we add a PHYSICAL BARRIER because in real life cables shift during assembly, vibration, and service. A barrier means "a loose AC strand can't physically reach a DC conductor even if both zip ties break." Barrier design choices, ordered by preference:
1. **Double-wall construction (BEST, zero extra parts):** Build internal frame with a vertical or horizontal middle divider wall (same material as side panels — acrylic/MDF/extrusion). The divider wall splits the internal volume into two sub-chambers. Left/top chamber = AC. Right/bottom chamber = DC.
2. **Plastic sheet barrier (GOOD):** Cut a 1 mm or 2 mm ABS/PETG/Acrylic sheet to fit the internal cross-section, glue or screw it into place. Leave cable pass-through slits ONLY on the AC side for AC wires and ONLY on the DC side for DC wires (no shared slit!). Seal unused area with hot glue if needed.
3. **Heat-shrink tubing over entire AC harness (ACCEPTABLE FALLBACK if barrier doesn't fit):** Entire AC wiring L/N/PE bundle inside 2:1 adhesive-lined heat-shrink tube wall-thick after shrink. Still NOT as good as a physical wall (heat-shrink can be nicked by sharp edges). Use only as last resort.

**Enforcement rules after barrier installed:**
- **Creepage ≥8 mm:** Surface distance along the barrier/plastic/chassis between any exposed AC conductor (switch solder tab, fuse cap metal) and any exposed DC conductor/chassis screw. Walk the surface path with calipers. ≥ 8 mm.
- **Clearance ≥4 mm:** Air-line distance (shortest straight line through air) between any AC conductor point and any DC/chassis point. ≥ 4 mm when barrier is present (clearance reduced from 10 mm Item 2 rule ONLY because barrier now blocks physical contact).
- **NO shared routing paths:** AC harness (L/N/PE wires from C14 to fuse to switch to PSU AC-IN) does NOT pass through any opening that a DC cable also uses. AC cables have their own dedicated cable gland/slit on AC-side. DC cables exit DC-side through their own openings. You should be able to REMOVE the entire DC-side wire bundle without touching any AC wire, and vice versa.

Design the barrier on the layout sketch (Item 2 v4 update). If Cond-X PCB ESP32 wins and barrier is problematic for board placement → shift ESP32 boards DC-side only.
**Why this task exists:** Mains wiring failure inside an enclosure = fire hazard. Safety Rule #1 in JIRA Safety section: "Any failure mode that exposes AC to a user-touchable part is a STOP work hazard." The physical barrier eliminates the most common failure mode: a loose ferrule on an AC wire comes out, wire falls onto a 5 V post that's connected to panel grounds connected to chassis screws user touches on the outside.
**Prerequisites:** AF-135 (layout sketch with AC/DC dashed zones), AF-138 (controllers/Nano placed DC-side, PSU AC-in side known).
**Blocked by:** AF-135, AF-138
**Required hardware:** Barrier material TBD after design (acrylic sheet or MDF divider; material purchased at Item 15 build time). Design task only now.
**Required tools:** None.
**Required software:** Drawing app or sketch update.
**Exact execution steps:**
1. **Barrier type select:** From layout topology: if PSU is in bottom-left (AC) and controllers in right/center (DC) → VERTICAL BARRIER at X = PSU_X + PSU_W + 5 mm (5 mm from PSU box edge). Barrier runs full internal height from bottom panel to top panel.
2. **On layout sketch, draw thick vertical line = BARRIER.** Label "AC/DC PHYSICAL BARRIER — acrylic 2 mm or MDF 5 mm".
3. **Left of barrier = AC CHAMBER:** Contains only: C14 inlet, fuse holder, power switch, AC L/N/PE wires between them, PSU AC-IN connector block. Mark AC chamber hatched.
4. **Right of barrier = DC CHAMBER:** Contains only: PSU 5 V output posts, 6× panel power branches from posts, controllers (WF4 or ESP32 stacks), Nano, HUB75 cables, Nano USB cable, transport cables. Mark DC chamber dotted.
5. **Pass-through slits in barrier:** The PSU itself STRADDLES the barrier line (PSU box has AC-IN side [left] and 5 V output side [right] — this is OK because the PSU's metal case is the UL-listed internal barrier). Draw the PSU rectangle overlapping the barrier line. AC-in of PSU stays left chamber. 5 V posts stay right chamber. NO other wires pass through the barrier. (Exception: if the PSU has a separate FG screw on outside — bonding wire goes through barrier with a dedicated grommet-sealed hole.)
6. **Creepage/clearance verification on sketch:**
   - Pick 3 AC conductor exposure points: (a) switch solder tab on inside; (b) fuse end-cap metal; (c) PSU AC-IN L-screw.
   - Pick 3 DC/chassis points nearest to barrier: (a) 5 V post nearest barrier; (b) chassis assembly screw 20 mm from barrier; (c) WF4 metal shield nearest barrier.
   - For each (AC, DC) pair (9 pairs):
     - Clearance = straight-line mm distance (if no barrier between line path, the barrier blocks so clearance effectively infinite; if the line goes AROUND barrier end, measure AROUND distance).
     - Creepage = surface path along barrier walls + panels mm.
   - All 9 pairs: clearance ≥ 4 mm (usually way more because barrier blocks direct line), creepage ≥ 8 mm. PASS.
7. **Routing path check:** Trace C14→fuse→switch→PSU-AC-IN: entire wire path LEFT of barrier. Trace PSU 5 V posts → 6 panel branches → panels: entire path RIGHT of barrier. No wires cross the barrier line except inside PSU body. PASS.
8. Save sketch v4 with barrier, hatched/dotted chambers, pass-through slits, verification pass notes.
**Expected result:** Barrier line drawn on sketch. 2 labeled chambers. 9 creepage/clearance pairs all ≥ limits. Shared routing check = 0 crossings except PSU body. Sketch v4 saved.
**Acceptance criteria / DoD:**
- Barrier type + material + location documented (vertical/horizontal, 2 mm acrylic or MDF 5 mm, X/Y coords).
- AC chamber hatched: contents list correct (only AC parts). DC chamber dotted: only DC parts.
- 9 creepage/clearance pair table: each pair's clearance ≥4 mm AND creepage ≥8 mm. All pass.
- Routing check: AC wires never leave AC chamber (except inside PSU metal case). DC never in AC. 0 unauthorized barrier crossings.
**Evidence to save:**
- `docs/pm/evidence/AF-139-acdc-barrier.md` (barrier design choice; chamber contents; 9-pair clearance/creepage table with values; routing pass result)
- Sketch v4 file `docs/mf-layout-sketch-v4.png` with barrier line, chamber hatching, pass-through slits
**Safety considerations:**
⚠️ SAFETY-CRITICAL TASK. Barrier design MUST NOT be skipped with "I'll be careful with routing during assembly." Physical barrier = defense in depth. IF ANY clearance/creepage pair fails the limit → redesign the barrier position or extend barrier height/width BEFORE next task. Do NOT proceed to Item 7/15 build with a failed creepage pair.
**Known uncertainties:**
PSU straddles barrier: need to ensure PSU metal case touches chassis on BOTH sides of barrier for PE bonding continuity (Item 9 PE bonding). PSU FG screw — if located on AC side, we route FG bonding wire through a dedicated barrier grommet hole (sealed).
**Failure response:**
Clearance/creepage pair fails → (a) move barrier 5 mm further into DC side (gain clearance), (b) add a 10 mm "flange" extension to barrier top/bottom (increases creepage surface path), (c) heat-shrink the AC switch/fuse metal tabs individually to increase creepage. All 3 mitigations valid.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 6 "Mains/low-voltage PHYSICAL BARRIER"
- JIRA Safety Rules §1 "AC/DC separation no shared routing"
- IEC 60950-1 (informative reference): creepage 8 mm / clearance 4 mm basic insulation for 250 V mains pollution degree 2 (typical home/office)
- AF-134/AF-135 (layout sketch updated to v4)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 safety-review power critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-140: Item 7 Cable routing plan. Draw every power branch on layout sketch with length. Mark bundle tie points (zip tie anchors). Mark service loops at each connector end (≥ loop diameter = 4× cable diameter). HUB75 cables routed AWAY from AC paths and AWAY from power branch bundles (avoid EMI coupling between 5V high-current switching and HUB75 3.3V logic signals). If HUB75 and power must cross → cross at 90 degrees, not parallel.

**Task ID:** AF-140
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 7 Cable routing plan on sketch v5. All 6 power branches drawn with length. Zip tie anchor points. Service loops ≥ 4× cable diameter. HUB75 AWAY from AC and AWAY from 5V bundles. Crossing HUB75/power = 90° NOT parallel.
**Description:** Detailed cable routing design = Item 7. Up to now we had placeholder lines; now we specify exact routes, lengths, tie points, EMI avoidance rules.
**Power branches (6):** PSU 5 V+ post → splitter → 6 separate wires → 6 panels. Panel order: #1 = top-left, #2 = top-right, #3 = mid-left, #4 = mid-right, #5 = bot-left, #6 = bot-right. Draw each branch as thick colored line (e.g., RED = 5 V branches). Label each with branch ID, exact length in mm (from PSU post, through splitter if any, to panel connector + 50 mm each end service loop). Bundle tie points: where multiple power branches share the same route, mark zip tie anchor points (small circles on sketch) every 100-150 mm along the bundle.
**HUB75 rows (3):** 3 cables (regime B WF4 single → 3 from WF4; regime A ESP32 → 1 per controller). Different color (e.g., BLUE = HUB75 signal). Route each from its controller X-port to the row's LEFT panel IN connector.
**EMI RULES (violation = garbled rows at 100% brightness):**
1. HUB75 cables NEVER run in the same bundle as 5 V high-current power branches. The 5 V branches carry pulsed 2 A+ current (each row's LEDs switch in sync = big current transients → strong magnetic field). HUB75 carries 3.3 V logic with ~0.5 V noise margin → easily corrupted. Minimum perpendicular gap when crossing = 10 mm, but parallel is FORBIDDEN.
2. HUB75 cables routed AWAY from AC chamber side entirely (stay in DC chamber far from AC wires).
3. If HUB75 and power MUST cross paths (layout constraint): cross at EXACTLY 90° (perpendicular). 90° intersect minimizes mutual inductive coupling (parallel = maximum coupling). Draw the crossing with a small "90°" label.
4. HUB75 zip ties separate from power zip ties (never the same anchor holding both a HUB75 and a 5 V branch).
**Service loop rule per connector end:** Every panel connector end (6× power + 6× HUB75 × 2 ends = actually each HUB75 has 2 ends [controller and panel], 3 rows = 6, so total 12 HUB75 ends), every PSU post end (6 ends), every controller transport end → service loop drawn as circle with DIAMETER = 4× the cable's outer diameter (thick 18 AWG power wire ≈ 3 mm OD × 4 = 12 mm loop diameter; thin HUB75 ribbon ≈ 2 mm OD × 4 = 8 mm loop). This gives enough slack to unplug/replug by pulling the connector straight back without tugging on crimps.
Save sketch v5 with colors/labels/tie points/cross angles.
**Why this task exists:** #1 field failure after 1 week = "random rows turn garbled when display shows bright content." 90% of the time = HUB75 routed parallel to 5 V power branches (a 2-hour rework to reroute HUB75). Explicit EMI rules in design = no rework. Service loops = "panel #4 dies, I want to swap it from spare stock without re-crimping 2 cables" — loops let you pull panel 50 mm out, reach in, disconnect.
**Prerequisites:** AF-138 (controllers/nano placed), AF-139 (AC/DC barrier defined → HUB75 only DC side).
**Blocked by:** AF-138, AF-139
**Required hardware:** Optional: sample cable pieces on bench to verify bend radius (cable OD × 4 bend rule).
**Required tools:** Colored pencils (paper) or layer colors (digital).
**Required software:** None beyond drawing.
**Exact execution steps:**
1. Copy sketch v4 → v5.
2. **6 Power branches (RED lines):**
   a. Start point: PSU 5 V posts (right chamber DC side, near barrier).
   b. If BOM uses 8-branch fused parallel harness: branches go out from harness block.
   c. Route #1 (top-left panel) branch upward along left side of DC chamber (parallel to barrier, 20 mm right of barrier). Tie points every 120 mm. End at panel #1 power conn location.
   d. Route #2 (top-right) upward then right. Tie points.
   e. #3/#4 (mid row) similar mid height.
   f. #5/#6 (bot row) short runs.
   g. For each branch: measure line length on sketch (convert with scale) + 50 mm service loop at PSU post END + 50 mm service loop at panel END. Write total length next to line: "PWR-#1 620 mm".
3. **3 HUB75 rows (BLUE lines):**
   a. From each controller port (WF4 X1/X2/X3, or 3× ESP32 HCT outputs).
   b. Route each to its row's left-panel HUB75 IN connector.
   c. EMI CHECK 1: scan each HUB75 line vs nearby 5 V branches — any parallel run longer than 40 mm? → REROUTE HUB75 to opposite side of frame. Move to right side if power is on left side.
   d. EMI CHECK 2: HUB75 lines cross AC barrier line? → NO (all HUB75 DC side). Fix if yes.
   e. EMI CHECK 3: any HUB75 line touches the same zip tie anchor circle as a power line? → Split into two anchors 10 mm apart.
   f. EMI CHECK 4: if HUB75 line and power line MUST share a crossing region → draw the intersection angle = 90°. Label "90° CROSS".
   g. For each HUB75 cable: length = line length + service loop controller end (50 mm) + service loop panel end (50 mm). Write length: "HUB-TOP 580 mm".
4. **Service loop circles:** Every connector endpoint (count them):
   - 6 × panel power IN ends.
   - 6 × panel power OUT ends (or PSU post ends). Actually: branches = PSU post (6 branch-ends) + panel 6 (6 ends) = 12 ends.
   - HUB75: 3 × controller ends + 3 × panel row ends = 6 ends.
   - Transport cables: Nano → 3 controllers = 3 ends.
   - USB cable: Nano → cutout = 2 ends.
   - PE bonding: 3 bonding points = no flexible cables = no loop needed.
   Total ~23 ends. Draw small circle at each end with diameter = ~10 mm, label "LOOP 4×OD".
5. **Bundle tie points (small circles on trunk lines):**
   - Power trunk near PSU: 2-3 anchors.
   - HUB75 bundle if 3 cables run close together: anchors every 150 mm.
   Label each anchor "ZIP #1", etc.
6. Save sketch v5.
**Expected result:** Sketch v5 with colored lines, labels, lengths per cable, 90° cross labels where needed, ~20+ service loop circles, zip anchors. EMI checks 1-4 all pass (0 violations).
**Acceptance criteria / DoD:**
- 6 power branches: each labeled with ID, total length (including loops).
- 3 HUB75 rows: each labeled total length.
- EMI audit results: 4 checks all documented PASS (0 parallel runs >40 mm; no AC crossing; no shared ties; crossings at 90°).
- Service loop count ≥ 18 circles on sketch.
- Zip tie anchor points ≥ 8 anchors marked.
- Sketch v5 file saved.
**Evidence to save:**
- `docs/pm/evidence/AF-140-cable-routing.md` (EMI 4-check pass results; power branch length table; HUB75 length table; loop count + zip count)
- Sketch v5 file `docs/mf-layout-sketch-v5.png` with colors/labels
**Safety considerations:**
EMI is not a safety issue (it's a signal-integrity/reliability issue), BUT it masks a real reliability failure that users interpret as "broken product." Good EMI routing = far fewer "it just stopped displaying right" support cases.
**Known uncertainties:**
Cable lengths on sketch vs real life: add ~10% contingency to each length (real cables don't follow perfect straight lines; 90° bends have radius). If BOM H-19 (1 m) was borderline, 10% contingency = 1.1 m → buy 1.5 m spares for 2 rows.
**Failure response:**
EMI parallel run found after build (garbled rows at full brightness): fix = reroute HUB75 cable(s) along the opposite wall, which takes 30 min. This task's goal is to catch it before any drilling/building.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 7 "Cable routing plan (lengths, ties, loops, EMI rules)"
- AF-139 (AC/DC barrier → HUB75 all DC side)
- BOM H-18/H-19 cable lengths (check plan vs BOM: lengths OK or need reorder)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-141: Item 8 Strain relief for every external/heavy cable. (a) C13 power cable entering frame: cable gland PG-13 or rubber grommet + internal zip tie anchor BEFORE spade terminals. (b) Panel power harnesses at PSU post side: ferrule crimp + zip tie bundle anchor before branches split. (c) HUB75 cables at controller end: small zip tie loop through HUB75 connector latch hole (if present) or around cable jacket anchored to frame. (d) Nano USB-C external access port: cable secured before entering frame.

**Task ID:** AF-141
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 8 Strain relief 4 categories. (a) C13 cable entry: PG-13 gland/grommet + internal zip tie BEFORE terminals. (b) Harnesses at PSU post: ferrule + bundle tie before split. (c) HUB75 controller end: zip tie through latch hole or around jacket anchored to frame. (d) Nano USB-C external port: cable secured pre-entry.
**Description:** Strain relief = the #1 overlooked but critical mechanical detail. Without it, someone trips over the power cord → the spade terminal pulls out and flops → live AC exposed. Strain relief means "the cable jacket is clamped to the chassis BEFORE any electrical connection point bears any mechanical load." 4 categories per JIRA MF Item 8 verbatim. Design each on sketch v6. Specify EXACT hardware parts to use (gland size, zip tie size, anchor locations).
(a) **C13 power cord (mains cable entering frame from rear panel):**
- **Rear panel cutout:** Hole for C13 cord. Use either:
  1. Cable gland (strain relief by compression) — PG-13 size is standard for 3×0.75 mm² C13 cord outer diameter ~8-10 mm. PG-13 = 13 mm mounting hole diameter, fits panel thickness up to 6 mm.
  2. Rubber grommet (cheap alternative) — 10 mm ID × 15 mm OD, fits in 15 mm hole, cord passes through grommet, grommet compresses against cord.
- **INTERNAL anchor (MANDATORY even with gland):** Even with a gland, add a heavy-duty zip tie (5 mm wide × 200 mm long) around the C13 cord INSIDE the frame immediately after the gland/grommet, anchored to a nearby chassis screw or a dedicated L-shaped bracket. The zip tie anchor is the PRIMARY strain relief (prevents cord from being yanked OUT through the gland even if gland fails). Gland/grommet is secondary seal against dust. Distance from anchor to AC spade terminals = ≥ 50 mm slack (cable won't pull on terminals even if fully stretched).
(b) **Panel power harnesses (6 branches) at PSU post / splitter side:**
- At the point where 6 separate 18 AWG branch wires emerge from the main 5 V bundle (or from a fused distribution block), bundle all 6 wires together with two heavy-duty zip ties 10 mm apart. Anchor this bundle to a chassis standoff/screw BEFORE any ferrules bear tension. If a branch wire is pulled at the panel end, the ferrule crimp does NOT feel the force (the zip tie bundle stops it).
(c) **HUB75 cables at controller end (3 cables):**
- Each HUB75 ribbon/IDC connector (2×8 keyed) at the WF4 X-port or at the ESP32 HCT board output: inspect connector body. Many have a small plastic latch tab with a hole, or a "strain relief bump" on the connector back near the cable exit.
- Pass a small 2.5 mm × 100 mm zip tie THROUGH the connector latch hole (if present) or loop it TIGHT around the cable jacket 5 mm behind the connector body, then anchor the free end of the zip tie to the controller board's mounting standoff or a nearby chassis hole.
- Result: yank on HUB75 cable → zip tie bears force, not the IDC solder joints on the board. (HCT adapter IDC joints failing from strain = a common perfboard fail mode in EXP.)
(d) **Nano USB-C external access cable (if we use a captive pigtail instead of user plugging directly into Nano):**
- If rear panel USB cutout accepts a USB-C plug directly → no cable, skip (d).
- If we route a short 150 mm USB-C pigtail cable from Nano USB-C port inside frame OUT through a cutout and the user plugs their debug cable into this pigtail's panel-mounted receptacle: secure the pigtail cable with a zip tie anchor inside immediately after the panel receptacle, before reaching Nano.
Draw each strain relief method on sketch v6: gland + internal tie symbol for (a), bundle tie symbol for (b), connector-loop-tie symbol for (c)×3, optional pigtail tie for (d). Specify BOM part numbers: PG-13 gland (or grommet), zip ties sizes.
**Why this task exists:** Strain relief failure mode #1: "Cleaner accidentally kicked power cord → frame went blank, electrician found loose AC wire inside." #2: "Tried to move frame 6 inches to the right, HUB75 connector ripped off perfboard trace." #3: "Took frame off wall, panel #6 power connector pulled off ferrule." Every one of these is a "user broke it on day 2" support nightmare that strain relief eliminates for $0.20 in zip ties.
**Prerequisites:** AF-140 (cable routes drawn → know where cable entry/exit points are). Sketch v5 available.
**Blocked by:** AF-140
**Required hardware:** None yet. Spec only. Purchase at Item 15 (glands/grommets + zip ties).
**Required tools:** None.
**Required software:** Sketch update v6.
**Exact execution steps:**
1. Copy sketch v5 → v6.
2. **(a) C13 rear entry:**
   a. On rear panel, mark hole location: X = 30 mm from AC chamber side, Y = 50 mm from bottom (near PSU AC-in). Hole: Ø13 mm (PG-13 gland mounting hole) or Ø15 mm (rubber grommet). Spec choice: PG-13 gland (superior). Write part: "PG-13 cable gland, nylon, IP40, panel thickness 1-6 mm".
   b. Inside frame, immediately behind hole: draw a chassis anchor circle (screw hole). Draw heavy zip tie around cord path near anchor, connect to anchor. Label: "ZIP 5×200 mm anchor — C13 strain relief, slack 50 mm to terminals".
3. **(b) Power harness splitter bundle tie:**
   a. On sketch, locate point where 6 branches split from main 5 V bundle (right after PSU 5 V posts or splitter block).
   b. Draw two parallel zip tie loops around the bundle, 10 mm apart, anchored to a nearby standoff. Label: "ZIP 5×200 ×2 — 6-branch bundle anchor pre-split, ferrules not load-bearing".
4. **(c) HUB75 at controllers (3):**
   a. For each controller HUB75 output connector (1 WF4 = 3 ports; 3 ESP32 = 3 total):
   b. Draw a small loop at connector body (latch hole or cable jacket). Connect loop via small zip tie to controller board standoff. Label each: "ZIP 2.5×100 through HUB75 latch → standoff #N; no board solder joint load". 3 instances.
5. **(d) Nano USB pigtail (if applicable):**
   a. If USB external = direct plug into Nano (rear cutout aligns): draw small note: "USB direct-plug, no pigtail, (d) N/A".
   b. If pigtail + panel receptacle: anchor pigtail near receptacle as in (a). Label: "USB pigtail ZIP anchor pre-Nano".
6. Compile BOM additions (if not already in BOM): PG-13 gland ×1, rubber grommet backup ×1 (if gland out of stock), zip ties sizes: 5 mm × 200 mm × 10 pcs (heavy), 2.5 mm × 100 mm × 20 pcs (small signal).
7. Save sketch v6 with reliefs drawn.
**Expected result:** Sketch v6 with strain relief drawings for all 4 categories. BOM additions list. Each relief type has specific part + size + anchor location.
**Acceptance criteria / DoD:**
- (a) C13: gland/grommet specified, hole size marked, internal zip anchor + slack to terminals noted.
- (b) Harness bundle: 2× heavy zip anchors drawn pre-split; note "ferrules not load-bearing".
- (c) HUB75 ×3: each has small zip through latch/jacket to standoff; note "solder joints no load".
- (d) USB pigtail anchor drawn OR documented N/A if direct-plug.
- BOM parts list for strain relief hardware compiled.
- Sketch v6 saved.
**Evidence to save:**
- `docs/pm/evidence/AF-141-strain-relief.md` (4 categories detailed specs; BOM strain-relief parts list)
- Sketch v6 file `docs/mf-layout-sketch-v6.png` with relief symbols and anchors
**Safety considerations:**
⚠️ (a) C13 strain relief is SAFETY-CRITICAL. Without internal zip tie anchor, a cord yank CAN pull spade terminals out (even if ferrules are good). PG-13 gland alone holds ~30-50 N pull force; with internal zip tie it's >200 N (zip tie tensile strength). In tripping scenario (90 kg human tripping), the cord should pull the frame slightly or unplug from wall socket end, NOT pull the AC wires free inside. The anchor is the PRIMARY protection; gland = secondary.
**Known uncertainties:**
HUB75 latch holes: not all cheap IDC connectors have them. If the HUB75 cable connectors purchased (BOM H-18/H-19) have NO latch hole hole → fall back to "zip-tie around cable jacket 5 mm behind connector, heat-shrink the zip-tie grip area to prevent slippage" (zip around jacket alone can slip). Heat-shrink + zip = still acceptable.
**Failure response:**
Zip tie slips on jacket (test pull: cable slides through zip) → add a 20 mm piece of 3:1 adhesive heat-shrink over the jacket at the grip point, let cool fully, then apply zip tie OVER the heat-shrink area. Heat-shrink adhesive bites into jacket → slip impossible.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 8 "Strain relief every external/heavy cable"
- BOM H-21 zip ties (check quantities/sizes; add heavy/small if missing)
- BOM H-18/H-19 HUB75 cables — inspect connectors for latch holes on receipt
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 safety-review docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-142: Item 9 PE (Protective Earth) bonding. Connect: (1) PSU FG (Frame Ground) screw → (2) frame chassis metal structure → (3) any exposed conductive panel frames if panel frames are metal. Use 18 AWG green/yellow wire. Continuity verified: multimeter between every bonded point (3 points min → 3 continuity pairs). No paint or anodizing under bonding screws — scrape to bare metal, add star washer if needed.

**Task ID:** AF-142
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 9 PE bonding 3 points (PSU FG → chassis metal → exposed conductive panel frames if metal). 18 AWG green/yellow wire. 3-pair continuity verified. No paint/anodizing under screws — scrape bare, star washer.
**Description:** Protective Earth (PE, also called "Safety Ground", "Frame Ground", "Chassis Ground") bonding = the #1 electrical safety requirement inside a metal-cased product with mains input. PE ensures that if an internal fault shorts L (live) to any exposed metal part, the fault current flows DIRECTLY back to PE through a low-resistance path, tripping the household circuit breaker BEFORE a user touching the frame gets shocked. Three-point daisy chain, every user-facing metal part bonded. Wire: 18 AWG green/yellow jacketed copper (standard PE color code per IEC, green with yellow stripe). DO NOT use green-only or yellow-only, and NEVER use black/red.
Bonding points in order, daisy chain (1 connected to 2, 2 connected to 3; no branching necessary — serial daisy chain is fine for 3 points):
1. **PSU FG screw:** Every enclosed mains PSU has a dedicated "FG" (Frame Ground) screw terminal on the case, separate from AC-in L/N/PE. It's a #6 or M3 screw tapped into PSU metal chassis. This is the PRIMARY PE tie-in (it's already connected to the PE pin of the C14 inlet INSIDE the PSU, per UL listing). We add an EXTERNAL bonding wire from PSU FG to our frame chassis for redundancy and to bond OUR metal chassis parts that are NOT part of the PSU case.
2. **Frame chassis metal structure:** If backplate = metal (aluminum composite) OR side rails = aluminum extrusion → any M3 screw hole in the metal chassis. If entire chassis = wood/plastic (non-conductive), bonding point #2 is N/A (skip, only need points 1 + 3 if panels are metal).
3. **Exposed conductive panel frames (if metal):** Most P1.53-128×64 panels have a black metal (steel) retainer frame around the PCB holding the LED matrix in place. If these panel frames are METAL (magnet sticks = steel; aluminum also metal) AND exposed (not fully covered by plastic bezel), they MUST be bonded. Bond one point per row (3 rows = 3 panel frame points minimum), or at minimum bond the top-left, top-right, and bottom-right frame corners. Use a separate 18 AWG green/yellow wire from each panel frame screw to chassis or daisy chain panels in series.

**Continuity verification (MANDATORY after wiring, BEFORE closing frame):**
Multimeter in continuity / low-resistance mode (Ω, beep when < 10 Ω). Probe pair list:
- Pair 1: PSU FG screw ↔ chassis (point 2). Expected: BEEP, resistance < 0.5 Ω.
- Pair 2: Chassis ↔ panel frame #1 (metal corner). Beep < 1 Ω.
- Pair 3: PSU FG screw ↔ panel frame #1 (metal corner). Beep < 1 Ω.
(Also test all metal panel frames if 3+ panels bonded → more pairs.)

**Mechanical prep per bonding screw (CRITICAL):**
Paint, anodizing, and powder-coating are INSULATORS. For EVERY screw that attaches a PE wire to metal:
1. Use a scraper/file/sandpaper to REMOVE all paint/anodizing around the screw hole, on BOTH sides of the metal, 5 mm radius from hole center. Shiny bare metal visible.
2. Place a STAR WASHER (toothed lock washer, external type #6 or M3) between the PE ring terminal and the bare metal. Star washer teeth bite into metal when screw tightened → guaranteed electrical contact even if small paint flecks remain.
3. PE wire termination: RING TERMINAL (not spade, not bare wrap) on green/yellow wire, crimped with proper ratchet crimper (AF-013 crimp practice quality). Ring terminal size matches screw (M3/#6).

Design the bonding wire routes on sketch v7. Specify the 3 bonding point coordinates. Note: panel-to-panel metal frames may be electrically continuous via screw-attached backplate; test assumption during build.
**Why this task exists:** U-004 (SAFETY-CRITICAL uncertainty: GND/PE bonding common ground) is resolved by this task. JIRA Safety Rules #5 explicitly requires PE bonding. Without it: a PSU internal winding-to-case fault (1 in 10,000 units manufacturing defect) makes the entire frame chassis LIVE at 220 V/110 V. User touches frame → shock. PE bonding → fault current < 10 ms breaker trip → safe.
**Prerequisites:** Sketch v6, backplate material known (Item 14/15: metal or wood/plastic). Panel frames conductive? (Quick test before this task: magnet to panel frame. Magnet sticks = steel = conductive = bond needed; no stick + plastic look = OK.)
**Blocked by:** AF-141 (sketch v6)
**Required hardware:** (for build later, spec now): 18 AWG green/yellow wire 1 m length, M3 ring terminals insulated × 5, star washers M3 external-tooth × 5 (one per bonding screw + one spare).
**Required tools:** Multimeter continuity (build-time), scraper/sandpaper (build-time).
**Required software:** Sketch v7 update.
**Exact execution steps:**
1. **Backplate conductivity test:** Determine backplate/sides material from Item 14 decision (acrylic = non-conductive, MDF = non-conductive, plywood = non-conductive, aluminum extrusion = conductive, aluminum composite = conductive).
   - Chassis point #2 needed = conductive YES; not needed = NO.
2. **Panel frame conductivity test (Item 1 bench test now before finalizing design):** Take one panel from BOM. Put fridge magnet on its side edge frame. If it sticks = steel = bond required. If not: scrape a tiny spot of edge, use multimeter continuity between two far panel frame corners → beep = aluminum conductive = bond; no beep = fully plastic = skip point 3 bonding.
3. **Sketch v7 bonding design (copy v6→v7):**
   a. **Point 1 (PSU FG):** Label PSU chassis FG screw location (look at PSU rear/sides — typically a screw with green/yellow marking nearby, or an unpopulated screw in metal chassis). Mark X/Y on sketch. Label "PE BOND #1 PSU FG".
   b. **Point 2 (Chassis if conductive):** Mark a screw hole in backplate near point #1. Label "PE BOND #2 CHASSIS".
   c. **Point 3 (Panel frames if metal):** Mark 3 panel frame corner screw locations (top-left #1, mid-right #4, bot-right #6). Label "PE BOND #3 PANEL FRAMES ×3".
   d. **Wire routes:** Draw PE green/yellow wire lines: #1 → #2 → #3a (panel-1 frame) → #3b (panel-4 frame) → #3c (panel-6 frame). Daisy chain topology (single wire from #1, successive taps). Or star from #1 if simpler. Either fine. Wire gauge label: "18 AWG green/yellow".
   e. **Per-bonding-point mechanical note:** Next to each #1/#2/#3 symbol: "NOTE: scrape paint 5 mm radius bare; star washer M3; ring terminal crimped."
4. **Continuity pair test plan list:** Compile the N pairs table for build-time: Pair ID, Point A, Point B, Max R (<1 Ω / <0.5 Ω).
5. Save sketch v7.
**Expected result:** Sketch v7 with PE points, wire routes, per-point mechanical notes. Continuity test pair list compiled. 3-point minimum (1→2→3) if all conductive; 2-point (1→3 only) if chassis non-conductive; 1-point only if panels also non-conductive (rare — still bond PSU FG to chassis anyway for any future metal bracket additions).
**Acceptance criteria / DoD:**
- Bonding point count determined and marked on sketch: PSU FG (ALWAYS present) + chassis (if metal) + panel frames (if metal) = 1 to 6 total points.
- Wire routes drawn. Gauge and color specified (18 AWG G/Y).
- Per-point scraping + star washer + ring terminal note present next to EACH bonding point on sketch.
- Continuity pair table: every pair (N choose 2 pairs) with pass criterion R < 1 Ω (or <0.5 Ω for short pairs).
- Sketch v7 saved.
**Evidence to save:**
- `docs/pm/evidence/AF-142-pe-bonding-design.md` (conductivity test results [chassis Y/N, panels Y/N]; bonding point list; daisy-chain vs star topology; continuity pair test table; per-point mechanical rules)
- Sketch v7 file `docs/mf-layout-sketch-v7.png` with PE points, wires, notes
**Safety considerations:**
⚠️ SAFETY-CRITICAL TASK (U-004). Do NOT skip any bonding point just because "chassis is wood so non-conductive". Panel FRAMES may still be metal even if chassis is wood. Do NOT use wire thinner than 18 AWG. Fault current on a dead short can be 100+ A for milliseconds; a 22 AWG wire could fuse/vaporize before breaker trips, leaving the fault present. Star washers ARE required: paint fleck = 20 Ω resistance = no breaker trip + live metal = HAZARD.
**Known uncertainties:**
Panel frame composition: 3rd-party AliExpress panels sometimes have plastic trim that LOOKS like metal. Magnet test + multimeter continuity test are the ground truth. If uncertain, bond anyway (cost = 2 minutes + $0.02 in wire, zero downside).
**Failure response:**
Continuity test at build fails (no beep between PSU FG and panel frame): (a) Did we scrape paint/anodizing? Check all bonding screws for bare metal spot + star washer. (b) Panel frames daisy-chain: are panels touching each other's metal frame at seam? If gapped by 0.5 mm plastic shim, add a jumper wire between them. (c) Wire crimp bad: re-crimp ring terminal (pull test: terminal should NOT pull off 18 AWG wire with 5 kg force).
**Source references:**
- JIRA.md §Mounted Frame Phase Item 9 "PE bonding 3 points"
- JIRA Safety Rules §5 "PE bonding all exposed conductive surfaces"
- U-004 uncertainty (SAFETY-CRITICAL: PE continuity)
- IEC 60364-5-54 PE bonding requirements (informative): max loop resistance < 2 Ω for breaker trip; 0.5 Ω easily achievable with 18 AWG short wires
- AF-017 (AC wiring visual/inspection — PE continuity task inside M0 done for AC inlet side; now do the CHASSIS side)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 safety-review power critical-path yes polarity-verify
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-143: Item 10 Ventilation design + cooling assessment. Passive cooling first: verify with EXP-015 thermal data INSIDE closed frame at brightness ceiling. If PSU or controllers exceed temp limits INSIDE frame: add quiet 40 mm or 60 mm axial fans (NOT high-RPM whiny ones — choose <25 dB at rated voltage). If adding fans: add to BOM as post-decision OPTIONAL purchase.

**Task ID:** AF-143
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 10 Ventilation + cooling assessment. Passive first (EXP-015 closed-box extrapolation). If exceed limits: add 40/60 mm quiet axial fan <25 dB. Add to BOM as OPTIONAL if fans needed.
**Description:** Predict closed-frame thermal behavior BEFORE building the frame. If passive cooling is borderline, we install fans NOW (during build) instead of discovering after wall-mount that it thermal-cycles off every 40 minutes. Two stages: PASSIVE CALCULATION (this task's work) → PASSIVE TESTING (Item 12's closed-box 60 min test). If calculation says passive OK with large margin → Item 12 confirms, fans SKIP. If calculation says passive MARGINAL or FAIL → design fan into the build now, include in cutouts, and Item 12 verifies it's adequate.
**Passive cooling calculation steps:**
1. **Input data from EXP-015 (AF-116):** At brightness ceiling (e.g., 75% or 100%), what was PSU chassis temp on the OPEN BENCH at thermal steady state? Open bench = excellent convection. Temp_open = ___°C (from AF-116 row 4 PSU col c).
2. **Closed-box derate:** Enclose the system, airflow is restricted. Typical ΔT_rise (closed box minus open bench) for a vented closed box with 5 mm holes Item 4 designed = +10..15 °C. Rule of thumb: multiply open PSU temp rise over ambient by 1.6 for closed box. If T_ambient = 25 °C, PSU open = 50 °C → rise = 25 K. Closed rise = 25 × 1.6 = 40 K → T_closed_est = 25 + 40 = 65 °C.
3. **Limit check:** PSU chassis temp MAX limit = 60 °C (hand-hold OK threshold from AF-116). Controller IC temp MAX = 55 °C (WF4 SoC / ESP32 datasheet typical max junction = 85 °C but we want case <55 °C for reliability). Wiring connector max = 50 °C (hand no pain).
4. **Verdict:** If T_closed_est < limit MINUS 5 °C safety margin → PASSIVE ACCEPTABLE. If T_closed_est within 5 °C of limit OR over limit → ADD FAN.
**Fan selection (if verdict = ADD FAN):**
- **Fan size:** PSU is 50 mm deep, frame depth 80-90 mm. 40 mm × 40 mm × 10 mm fan (4010) OR 60 mm × 60 mm × 25 mm fan (6025). 4010 fits the 25 mm buffer reserved in Item 3.
- **Noise requirement:** NO whiny 7000 RPM fans. Manufacturer spec: sound pressure level < 25 dB(A) at rated voltage. Typical: Noctua NF-A4x10 5V = 14.9 dB(A) (premium, ~$15); budget: Gdstime 4010 5V 5000 RPM = 22-24 dB (acceptable). If 5 V fan available (power from PSU 5 V post spare branch) → easiest. If only 12 V fans → need 12 V from PSU 12 V output (if RD-65A has 12 V tap — check BOM PSU). 5 V preferred for simplicity.
- **Placement:** Fan mounted on EXHAUST side panel (pushes hot air OUT; intake via Item 4 holes = natural). Blows in direction parallel to airflow channel arrow from Item 4 sketch.
- **Mount:** 4 screws M3 × 10 mm through panel into fan corners, fan rubber anti-vibration gaskets between fan and panel (prevents panel buzzing = silent).
- **Add to BOM as OPTIONAL post-decision line:** Fan unit, 4 screws + gaskets, if 5V OK or 12V variant.
**Update Item 4 hole pattern if fan added:** near fan location, add the 4 fan screw holes (M3, 39 mm × 39 mm square for 4010 fan) and a large circular vent cutout (Ø38 mm) centered behind the fan blades (fan blows through this hole).
**Why this task exists:** Thermal throttling/shutdown of PSU/controller in closed wall frame = "my display randomly blanks for 20 minutes then comes back" — hard-to-debug, user thinks it's broken. Calculation-based fan decision at design time avoids re-machining the frame. <25 dB noise requirement: the frame is in a home; a whiny fan makes it unusable. Silence = V1 proof of concept acceptability.
**Prerequisites:** AF-116 (EXP-015 open-bench thermal data, PSU temp at ceiling brightness). AF-137 (vent hole pattern area known). AF-136 (frame depth → fan buffer available).
**Blocked by:** AF-116, AF-137, AF-136
**Required hardware:** None yet (calculation + BOM update optional). Fan test if we purchase one for noise sample.
**Required tools:** Calculator.
**Required software:** None.
**Exact execution steps:**
1. **Extract AF-116 data:** Open AF-116 evidence file. Read row corresponding to BRIGHTNESS CEILING value (from AF-120 result). Get: (c) PSU chassis temp °C = T_open. Ambient room temp during EXP-015 T_amb = ___ (recorded or assume 25 °C).
2. **Compute:**
   - Rise_open = T_open - T_amb.
   - Rise_closed_est = Rise_open × 1.6.
   - T_closed_est = T_amb + Rise_closed_est.
3. **Limit check:**
   - PSU limit = 60 °C. Margin_PSU = 60 - T_closed_est.
   - Controller limit = 55 °C (case). Rise for controller typically similar to PSU; Margin_Ctrl = 55 - (T_amb + Rise_open × 1.4).
   - VERDICT: If Margin_PSU ≥ 5 °C AND Margin_Ctrl ≥ 5 °C → **PASSIVE OK** (no fan needed; Item 12 closed-box test will confirm). If EITHER margin <5 °C OR negative → **ADD FAN** (design now, build in).
4. **If VERDICT = ADD FAN:**
   a. Fan size: 4010 (40×40×10 mm) fits Item 3's 25 mm buffer with 15 mm clearance.
   b. Voltage: 5 V preferred → power from spare 5 V fused branch (BOM H-04b 8 branches; 6 used by panels, 1 by Nano+ctrls, 1 spare = available).
   c. Noise target: <25 dB(A). Search AliExpress/Amazon for "4010 5V silent fan <25 dB". Select model.
   d. Mount location: exhaust side of frame, aligned with PSU exhaust. Add fan to Item 4 sketch v8: 4 mounting holes + central vent cutout.
   e. Add to BOM as OPTIONAL line: "MF-FAN-OPT: 4010 5V <25 dB fan ×1, M3 screws ×4, rubber gaskets ×4".
5. **If VERDICT = PASSIVE OK:** Write: "No fan required by calculation. Item 12 closed-box test will confirm; if Item 12 shows T > limits, install fan post-hoc using the Item 3 25 mm reserved buffer and drill 4 screw holes then (remedial work moderate)."
6. Save cooling assessment document.
**Expected result:** Verdict (PASSIVE OK or ADD FAN). Rise calculation documented. If ADD FAN: model selected (<25 dB), size/voltage, BOM line addition, updated sketch v8 with mounting. If PASSIVE: clear margins above threshold.
**Acceptance criteria / DoD:**
- T_open and T_amb values from EXP-015 copied in.
- Rise_closed_est calculated (×1.6 factor shown).
- PSU margin and controller margin numbers. Both margins ≥ 5 °C = PASSIVE documented. Either <5 °C = ADD FAN documented.
- If ADD FAN: fan model/size/voltage/noise spec written, BOM optional line added, mount location drawn on sketch v8.
**Evidence to save:**
- `docs/pm/evidence/AF-143-cooling-assessment.md` (input temps; rise math; margins; verdict; if fan → BOM line + spec)
- If fan: sketch v8 file `docs/mf-layout-sketch-v8.png` with fan cutouts/screw holes
**Safety considerations:**
⚠️ Thermal. PSU exceeding 60 °C at closed box = electrolytic capacitor lifespan halves every 10 °C rise. Capacitor swelling / bulging after 1-2 years = PSU fails. Also: PSU internal overtemperature may disable 5 V output (thermal protection) → panels blank. If thermal protection FAILS to engage (cheap PSU), sustained overheat = fire risk. Fan or adequate passive design is NOT optional.
**Known uncertainties:**
×1.6 closed-box derate factor is a rule of thumb for electronics enclosures with 40-60 vent holes. If Item 4 designed 80+ vent holes, derate factor drops to 1.4 (better). If only 30 holes, factor is 1.8-2.0 (worse). Adjust accordingly based on Item 4's N_design hole count. Controller thermal not measured open-bench in EXP-015 (only PSU). Estimate controller runs ~5 °C hotter inside closed box than PSU (WF4/ESP32 small-form-factor dissipates ~1-2 W each into stagnant air).
**Failure response:**
After Item 12 closed-box test, T exceeds limit despite "PASSIVE OK" verdict here: (a) Drill 20 more vent holes (5 min) and retest. (b) If still hot → install 4010 fan in reserved 25 mm buffer (drill 4 screw holes + center vent; 30 min work). Pre-wire the spare 5 V branch during harness install in case fan is needed later (even if passive verdict: leave the wire coiled near exhaust location = 2 minutes during harness build, saves 30 min later).
**Source references:**
- JIRA.md §Mounted Frame Phase Item 10 "Ventilation design + cooling assessment"
- AF-116 EXP-015 (open-bench PSU chassis temp data input)
- AF-120 (brightness ceiling value = which EXP-015 row to use)
- AF-137 (vent hole count N_design → derate factor refine)
- AF-144 Item 12 (closed-box 60 min verification: confirms this design)
**Labels:** mechanical thermal blocked blocked:exp-exp-015 blocked:exp-exp-016 thermal-review docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-144: Item 11 Closed-box thermal testing at brightness ceiling. Close all frame panels, run full dashboard-normal content (not full-white) at determined ceiling brightness for ≥60 min. Measure at end: (a) PSU case temp IR; (b) controller IC temps hand/IR; (c) wiring temp at high-current points; (d) internal frame air temp (thermometer probe if available). All within same limits as EXP-015 open-bench. Also display still stable, no garbled rows, no blank.

**Task ID:** AF-144
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 11 Closed-box thermal test ≥60 min at ceiling brightness dashboard content. 4 measurements: PSU IR, controller IC temp, wiring temp, internal air temp. All within EXP-015 limits. Display stable no garble/blank.
**Description:** After full frame assembly (Item 15 partially assembled: panels mounted, backplate on, all side panels attached except one service panel that we can still open for probing), run the FIRST closed-internal-volume thermal test. This validates Item 4 vent holes + Item 10 cooling verdict. Procedure mirrors EXP-015 AF-116 but content = dashboard-normal (not full-white stress) + environment = closed chassis.
**Test setup:**
- Panels all attached per Item 15 (6 panels). Backplate + top/bottom side rails on. LEFT service side panel OFF (we need to probe inside at end; leave the PSU intake/exhaust side panels INTACT so airflow is realistic — leave the opposite side panel removable for probing).
- Wires all routed as Item 7 plan. Strain reliefs installed.
- Content running: real dashboard (AF-133 main.py default) with clock, weather placeholder, art rotation. Mixed content not all-white. Brightness = CEILING VALUE from AF-120 (the production value).
- Duration: ≥60 MINUTES continuous. Stopwatch started at T0 when content switches on.

**End-of-test measurements (minute 60, do not turn off):**
Open the service access panel quickly. Take readings within 60 seconds of panel removal (don't let air in cool things down too much):
(a) **PSU chassis temp:** IR thermometer aimed at PSU metal case midpoint (not the fan grille, the solid metal). Record °C. (If no IR: hand-touch 5 s → "<60C hold OK" / ">60C pain" qualitative as last resort AF-116 method.)
(b) **Controller IC temp:** IR aimed at WF4 SoC metal can (or ESP32 module metal shield top). If no IR: hand touch top of WF4/ESP32 chip shield 3 s → "<55C cool-warm" / ">55C hot-to-touch" (quick finger withdrawal = too hot). Don't burn yourself.
(c) **Wiring/connector temp at high-current points:** (i) PSU 5 V output posts (plastic near screws) — hand test, "<50C no pain" / ">50C pain". (ii) Panel #6 (farthest, top-right) power connector area — hand test same.
(d) **Internal frame air temp (if probe available):** Digital thermometer K-type probe or indoor hygrometer placed inside middle of DC chamber, away from direct component heat. Record °C.
(e) **Stability visual (run the whole 60 min, not just end):** Display stable at minute 5, minute 30, minute 60: no blank panels, no garbled rows. Any row dropout in the 60 min = fail even if temps OK.

**Limits (same as open-bench AF-116, with the understanding closed box will be hotter):**
- (a) PSU chassis < 60 °C. (Hand: hold OK 5 s.)
- (b) Controller < 55 °C case. (Hand: barely warm, not hot-to-touch instant withdrawal.)
- (c) Wiring < 50 °C. (Hand: no pain.)
- (e) Display stable all 60 min. (0 blanks, 0 garbles.)

**If FAIL:** Mitigate per Item 10 failure response: (a) drill 20 extra vent holes; (b) install 4010 silent fan in reserved buffer. Then re-run 60 min. MUST PASS before wall-mount.
**Why this task exists:** Item 10 was calculation/prediction. Item 11 is experimental truth inside the actual closed chassis. If Item 11 passes, the 72 h wall test won't thermal-crash mid-run. If Item 11 fails, we catch and fix before the frame is on the wall.
**Prerequisites:** COMPLETED: Item 15 prototype backplate + panels mounted (at least semi-assembled, all panels on). Side panels available for assembly. APP SOFTWARE running from Stage 12 (AF-133) with dashboard content at ceiling brightness (AF-120).
**Blocked by:** AF-137 (vent holes cut), AF-143 (cooling plan: with or without fan — if fan verdict ADD FAN, install fan before this test), AF-148/AF-149 (backplate + panels mounted), AF-133 (dashboard app, ceiling brightness config).
**Required hardware:** Fully assembled 6-panel closed frame (service panel removable). IR thermometer (preferred) or hand. Stopwatch. Optional: digital temperature probe.
**Required tools:** IR thermometer (preferred, $8 AliExpress, or multimeter temp probe if DMM has temp mode).
**Required software:** main.py dashboard app with ceiling brightness setting.
**Exact execution steps:**
1. **Assemble closed frame for test:** Mount all 6 panels (Item 15/16). Attach backplate. Attach 3 of 4 side panels (intake side + exhaust side + top = closed; leave one service side = opposite intake/exhaust, removable with 4 screws). Ensure NO large gaps on intake/exhaust sides (those must be airtight so air flows through the vent hole pattern, not through gaps).
2. **Start test:** Turn PSU ON. Start dashboard app running at ceiling brightness. T0 = timestamp. Take T=0 photo of frame running.
3. **Mid check T=30 min:** Visual check only: 6 panels all displaying? No garbled rows? OK → continue. Photo optional.
4. **End test T=60 min:**
   a. Keep PSU & display ON. Remove service panel screws (≤ 4 screws), set panel aside. Start 60-second timer for readings.
   b. Within 60 s:
      - (a) IR thermometer: point at PSU metal case solid (not grille). Record °C: ___.
      - (b) IR: WF4 SoC top or ESP32 module top ×3 (record hottest of 3 if ESP32). °C: ___.
      - (c) Hand: PSU posts 3 s → pain? Y/N (no pain = <50C OK). Panel #6 connector 3 s → pain? Y/N.
      - (d) If probe: internal air temp °C: ___.
   c. Replace service panel loosely.
5. **Stability check:** At T=60 min right after temp read: visual scan panels 1-6. Any blank? Any garbled random rows? (Garbled = rows with wrong colors or flickering noise; transient glitch OK for 1 s; sustained = fail.)
6. **Result:** Compare measurements to limits:
   - (a) PSU <60C → PASS. Else FAIL.
   - (b) Ctrl <55C → PASS. Else FAIL.
   - (c) Both wiring hand tests: no pain → PASS. Pain → FAIL.
   - (e) 60 min 0 garbled/blanks sustained → PASS. Any sustained → FAIL.
7. **FAIL → mitigate:** Drill 20 extra vent holes (10 min). If still FAIL → install 4010 fan in reserved buffer (30 min, wiring from pre-staged spare 5 V branch). Re-run T60 after fix.
**Expected result:** All 4 temperature measurements within limits. 60 min stability perfect.
**Acceptance criteria / DoD:**
- PSU temp <60 °C (or hand-hold-OK 5 s).
- Controller temp <55 °C (or hand cool-warm no instant withdrawal).
- Wiring: PSU posts + panel #6 connector: both hand test no pain.
- Visual stability 60 min: 0 sustained blank, 0 sustained garble.
- If probe: internal air temp documented (no hard limit, but <45 °C target).
- If FAIL mitigated: evidence of mitigation (extra holes drilled photo, fan installed photo) + re-run pass data.
**Evidence to save:**
- `docs/pm/evidence/AF-144-closedbox-thermal.md` (T0 timestamp; T60 4 measurements; stability pass/fail line; mitigation if any with photo refs)
- `docs/photos/mf-thermal/t0-start.jpg` (frame at start, closed)
- `docs/photos/mf-thermal/t60-end.jpg` (end test still running, or temp IR photo if IR camera available)
- If mitigation: `docs/photos/mf-thermal/extra-vent-holes.jpg` or `fan-installed.jpg`
**Safety considerations:**
⚠️ Thermal at 60 min closed box. If PSU or wiring hot to touch (>pain threshold), DO NOT continue running for the full test. Turn PSU off after measurement; ventilate 10 min cool, then apply mitigation, then re-run from T0 with shorter 30 min watch. Do NOT leave a frame that registered FAIL (hot wiring/PSU) unattended.
**Known uncertainties:**
Service panel removal time: if we fumble with screws and take 2+ minutes, internal temps drop 3-5 °C (false pass). Mitigation: practice removing service panel screws once before test; target <30 s removal. IR thermometer takes 1 s per reading; all 4 readings <20 s total.
**Failure response:**
Temps exceed limits → (1) extra 20 vent holes first (cheap, quick) → retest. (2) If still failing → fan. If display garbled rows only at T60 (not T0) = thermal drift of controller HCT245 buffers (hot → timing marginal). Fix with cooling.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 11 "Closed-box thermal at brightness ceiling"
- AF-116 EXP-015 (open-bench baseline limits/procedure — same limits, closed env test here)
- AF-120 (brightness ceiling value used)
- AF-143 (cooling assessment — validates here)
**Labels:** validation mechanical thermal blocked blocked:exp-exp-015 blocked:exp-exp-016 thermal-review critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-145: Item 12 Brightness ceiling (software) + nighttime dim mode. Config BRIGHTNESS_CEILING_PERCENT constant in config.py. Optional: ambient light sensor (BH1750 I2C if purchased later, OPTIONAL not V1-required) or scheduled nighttime dim (e.g., 22:00→06:00 brightness=40%). Document current method.

**Task ID:** AF-145
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 12 Brightness ceiling config.py constant + nighttime dim mode. BRIGHTNESS_CEILING_PERCENT in config.py. Dim: schedule 22:00→06:00 40% (default). BH1750 ALS OPTIONAL not V1. Document method.
**Description:** The MR phase (AF-120) already determined the BRIGHTNESS CEILING value. This task: (a) bake that ceiling value into config.py as a HARD constant (user can lower it, but cannot accidentally exceed ceiling which would overheat), (b) implement nighttime dimming (either time-based scheduled for V1 or optional ambient sensor for future).
(a) **Ceiling config constant:** Add to config.py: `BRIGHTNESS_CEILING_PERCENT = X` where X = the ceiling value from AF-120 MR (e.g., 75 if 100% was too hot, or 100 if passed). The app code NEVER sets display brightness ABOVE this value (even if a widget tries). The brightness setter function first clamps: `actual_brightness = min(requested_brightness, BRIGHTNESS_CEILING_PERCENT)`. Document this clamp in code comment and in user README.
(b) **Nighttime dim mode (V1 default method: TIME-BASED SCHEDULE):** Add two more constants:
```
NIGHT_DIM_ENABLED = True
NIGHT_DIM_START_HOUR = 22   # 22:00 local time, 10 PM
NIGHT_DIM_END_HOUR = 6      # 06:00, 6 AM
NIGHT_DIM_BRIGHTNESS_PERCENT = 40   # dimmed to 40% of ceiling = 0.4 * X% absolute
```
Runtime logic: in dashboard main loop, every tick check current local time hour. If hour ∈ [NIGHT_DIM_START_HOUR, 24) ∪ [0, NIGHT_DIM_END_HOUR) → brightness = BRIGHTNESS_CEILING_PERCENT * NIGHT_DIM_BRIGHTNESS_PERCENT / 100. ELSE → full BRIGHTNESS_CEILING_PERCENT. Transition is immediate on hour boundary.
(c) **Ambient light sensor BH1750 (FUTURE / OPTIONAL, not required for V1 GATE-PASS):** Purchase BH1750 I2C digital light sensor module (~$2), connect VCC=3.3V, GND, SDA, SCL to Nano I2C pins. Library: pip install smbus2 or adafruit-circuitpython-bh1750. Read lux every 30 s. Map lux → brightness: 0 lux (night) → 20%, 50 lux (room dim) → 40%, 200 lux (room normal) → 70%, 500+ lux (daylight near window) → 100%. ALS is a post-V1 nice-to-have; for this task, just leave TODO comment + code skeleton, NOT required.

Document the current method in README: "V1 brightness = two modes: daytime [CEILING%] and nighttime [NIGHT%] on 22:00–06:00 schedule. Edit config.py to change hours or disable."
**Why this task exists:** 24/7 wall frame at 100% brightness 100 W LEDs in bedroom = user complaint "too bright at night, like a nightlight from hell". Time-based dimming is 20 lines of code, zero new hardware, works perfectly. It's the default V1 feature. The software clamp on ceiling = "even if some widget accidentally requests 100% after AF-120 found ceiling = 75%, display stays safe (no thermal runaway in closed box)."
**Prerequisites:** AF-120 (MR brightness ceiling value X% known). AF-131 (config.py exists). AF-133 (main loop with dashboard).
**Blocked by:** AF-120, AF-131, AF-133
**Required hardware:** None (time-based). Optional BH1750 sensor if doing ALS.
**Required tools:** None.
**Required software:** All MA modules (config, main loop, brightness setter in transport/widgets).
**Exact execution steps:**
1. **Read ceiling X%:** Open AF-120 evidence → get MR brightness ceiling percent value (e.g., 75).
2. **config.py edits:** Append:
```
# Brightness control
BRIGHTNESS_CEILING_PERCENT = X   # from AF-120 MR ceiling, DO NOT EXCEED without Item 11 retest
# Nighttime dim schedule (V1 default method: time-based)
NIGHT_DIM_ENABLED = True
NIGHT_DIM_START_HOUR = 22   # inclusive, 24h format
NIGHT_DIM_END_HOUR = 6      # exclusive
NIGHT_DIM_BRIGHTNESS_PERCENT = 40  # 40% of CEILING (not 40% absolute)
```
3. **Brightness clamp function:** In transport or pipeline module, write helper:
```
import config
import datetime
def effective_brightness_pct(requested_pct: int = None) -> int:
    # 1. Never exceed ceiling (safety clamp)
    ceiling = config.BRIGHTNESS_CEILING_PERCENT
    if requested_pct is None:
        pct = ceiling
    else:
        pct = min(requested_pct, ceiling)
    # 2. Nighttime dim
    if config.NIGHT_DIM_ENABLED:
        now = datetime.datetime.now().hour
        start = config.NIGHT_DIM_START_HOUR
        end = config.NIGHT_DIM_END_HOUR
        is_night = (now >= start) or (now < end)
        if is_night:
            pct = pct * config.NIGHT_DIM_BRIGHTNESS_PERCENT // 100
    return max(0, min(100, pct))  # final clamp 0-100
```
4. **Wire function into main loop:** Every frame send before rendering or before transport.set_brightness(%) call, use effective_brightness_pct().
5. **Test daytime mode:** Set system time to 12:00 (noon). effective_brightness_pct() returns X%. Test.
6. **Test nighttime mode:** Set system time to 23:00 (11 PM). effective_brightness_pct() returns floor(X * 40 / 100)%. Test.
7. **ALS (OPTIONAL NOT REQUIRED for V1):** If sensor purchased and connected: write skeleton `read_als_lux()` function with TODO comment, return None, document in README as "Future V1.1 feature". Do not require for DoD.
8. **README update:** Short paragraph on brightness controls: ceiling clamp, schedule dim, how to edit constants.
**Expected result:** Config constants written. effective_brightness_pct() returns X% at 12 PM, 0.4·X% at 11 PM. Clamp test: request 100%, ceiling =75 → returns 75% max. Night time: returns 30% (if ceiling 75 × 40%).
**Acceptance criteria / DoD:**
- config.py: BRIGHTNESS_CEILING_PERCENT set to actual AF-120 X% value. Night 4 dim constants present.
- effective_brightness_pct() function with ceiling clamp + schedule logic.
- Daytime test: noon → X% correct.
- Nighttime test: 23:00 → floor(X*0.4) % correct.
- Over-ceiling test: request 100 when ceiling=75 → 75 returned.
- README user-facing paragraph written.
- ALS OPTIONAL: NOT required. Skeleton TODO OK.
**Evidence to save:**
- `config.py` updated (new constants).
- Pipeline/transport brightness clamp function committed.
- `docs/pm/evidence/AF-145-brightness.md` (X% ceiling value; daytime/nighttime return values; clamp test result)
- README.md (or docs/user-manual.md) updated with brightness section.
**Safety considerations:**
N/A — software configuration. (Thermal safety already enforced by ceiling value from AF-120.)
**Known uncertainties:**
Exact NIGHT_START/END hours: 22-6 is default. User in early time zone or night owl may want 23-7. Documenting in config.py + README lets user edit without code changes.
**Failure response:**
Brightness stuck at 40% during day → hour check logic bug. Check midnight wraparound (now >= 22 OR now < 6 = correct for 24h wrap). If always night → check datetime imports (datetime.now() vs datetime.datetime.now()).
**Source references:**
- JIRA.md §Mounted Frame Phase Item 12 "Brightness ceiling + nighttime dim"
- AF-120 MR brightness ceiling (X% value input)
- AF-144 closed-box thermal (re-validates at ceiling value after dim implementation)
**Labels:** software nano mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-146: Item 13 Wi-Fi signal strength inside closed frame. Nano inside frame, all panels on, frame closed. Run `iw dev wlan0 link | grep signal` or equivalent. Signal should be ≥ -67 dBm for reliable connection. If weaker (<-75 dBm): relocate Nano to frame edge closer to Wi-Fi AP; or add external antenna pigtail if ESP32 is used (note: LicheeRV Nano-W has onboard antenna); or add Wi-Fi range extender near frame. Document which fix applied.

**Task ID:** AF-146
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 13 Wi-Fi signal strength closed-frame test. Nano inside frame closed, panels on. iw wlan0 signal ≥ -67 dBm PASS. If < -75 dBm → relocate Nano to edge, add ESP32 ext antenna, or Wi-Fi extender near frame. Document fix.
**Description:** Metal panels, metal backplate (if aluminum), metal PSU all attenuate 2.4 GHz Wi-Fi signal. The Nano is now INSIDE the closed box; we need to make sure Wi-Fi RSSI (received signal strength) is still strong enough for reliable widget API calls, SSH, OTA. RSSI scale: -30 dBm = excellent (next to AP), -50 dBm = good, -67 dBm = minimum reliable threshold (1% packet loss acceptable for TCP), -75 dBm = problematic (5%+ loss, SSH lag, widget API timeouts), -85 dBm = unusable.
**Test procedure:**
- Nano installed in its final Item 5 location inside frame. All 6 panels attached, all metal frame sides/backplate attached (fully CLOSED). Frame sitting on bench in its final room location, before wall-mounting.
- Wi-Fi AP in its normal home location (not moved next to frame for test — simulate real conditions).
- SSH into Nano via Wi-Fi.
- Command (Linux Nano with standard iw): `iw dev wlan0 link | grep signal`. Output line like `signal: -62 dBm`. Record dBm value. Repeat 3 times at 10 s intervals; take worst (most-negative) reading = actual RSSI.
- **Alternative for ESP32 controller's Wi-Fi (if regime B ESP32 and controllers use Wi-Fi):** On ESP32 console, `WiFi.RSSI()` function. Record 3× per controller, take worst.

**Interpretation + Fixes:**
- **BEST ≥ -67 dBm:** PASS. No fix needed.
- **MARGINAL -68 to -74 dBm:** Borderline. Relocate Nano if possible (see Fix A) to a corner closer to AP; retest. If still -70+ → PASS with note "Monitor SSH latency for 72 h test".
- **FAIL ≤ -75 dBm:** Apply fixes in priority order, retest after each until ≥ -67.
  - **Fix A (Nano relocation, preferred, $0):** On Item 5 layout sketch, move Nano from its current internal location to the frame CORNER that is CLOSEST TO THE WI-FI AP (e.g., if AP is in the living room northwest of frame, place Nano in frame's northwest internal corner, 10 mm from frame edge — as long as USB service access rules still met). This reduces the amount of panel metal between Nano antenna and the AP by 30-50%.
  - **Fix B (LicheeRV Nano-W onboard antenna improvement):** Most Nano-W boards have a ceramic chip antenna on one corner. Ensure (during Item 5 re-layout) this antenna corner has NO METAL (backplate, panel frame, PSU) within 10 mm of the antenna zone. Keep that corner 15 mm away from any metal (Item 5 called out antenna clearance; enforce it).
  - **Fix C (ESP32 only, if regime B and ESP32 Wi-Fi used):** Some ESP32-S3 boards have an IPEX/U.FL external antenna connector + a ceramic antenna + 0 Ω resistor jumper. If signal FAIL: solder 0 Ω resistor from onboard antenna to IPEX, add 2.4 GHz U.FL pigtail antenna (150 mm wire, 3 dBi gain) to the controller, route pigtail to the nearest non-metal frame edge, stick antenna pad to frame inside non-metal surface.
  - **Fix D (range extender / mesh node, cost $15-30):** Purchase cheap Wi-Fi extender or mesh node, place it within 3 m of frame (same room, line of sight). This boosts local RSSI by 20-30 dB. Configure extender to same SSID. Re-test Nano RSSI from inside frame. Should now ≥ -55 dBm.

Document the fix path applied (if any). Add purchased items (pigtail, extender) to BOM as post-MF OPTIONAL items.
**Why this task exists:** Item 17 (72 h wall test) requires Wi-Fi sustained. If RSSI = -80 dBm inside closed frame, 72 h test will have periodic widget-data failures, SSH reconnects. The test MUST be done in FINAL CLOSED frame, FINAL room location, not on bench with frame half-open (on bench it's -55 dBm, misleading).
**Prerequisites:** Item 5 (Nano/controller placement finalized, can apply Fix A to location). Item 15 (frame fully closable with panels). Wi-Fi AP working normally.
**Blocked by:** AF-138, AF-148/AF-149 (frame closable)
**Required hardware:** Closed frame, Nano inside, AP in final location. Optional fix hardware (U.FL pigtail, Wi-Fi extender).
**Required tools:** None (Linux `iw` built-in).
**Required software:** SSH to Nano.
**Exact execution steps:**
1. **Frame setup:** Assemble frame in FINAL CLOSED configuration (all 6 panels on, backplate, sides). Place frame on a table in its INTENDED FINAL ROOM LOCATION (not on bench in lab). Wi-Fi AP in its normal location, no temporary placement.
2. **SSH to Nano:** Either via `ssh user@ai-frame.local` (if mDNS reaches inside frame) or static IP. If SSH fails at first (signal too weak), temporarily open service panel, SSH, THEN close panel for measurement — ensure measurement is with closed frame.
3. **Read RSSI × 3:**
   ```
   for i in 1 2 3; do iw dev wlan0 link | grep signal; sleep 10; done
   ```
   Record three values. Take the worst (most negative, e.g., -71 vs -68 = -71 is worst). Worst = RSSI_actual.
4. **Threshold apply:**
   - RSSI_actual ≥ -67 → PASS. Document. Task ends.
   - -68 ≥ RSSI_actual ≥ -74 → MARGINAL. Try Fix A first (relocate Nano 20 mm toward AP corner). Retest ×3. If now ≥ -67 → PASS. If still marginal → PASS with note, monitor in 72 h test.
   - RSSI_actual ≤ -75 → FAIL. Apply Fix A → retest. If still FAIL → Fix B (antenna clearance check). If still FAIL → Fix C (ESP32) or Fix D (range extender). Retest after each. Continue until ≥ -67.
5. **Fix documentation:** For each fix applied, write what was done, before-RSSI, after-RSSI.
**Expected result:** Final RSSI after any fixes ≥ -67 dBm. Fix path documented with before/after values.
**Acceptance criteria / DoD:**
- 3 RSSI readings, worst value recorded, closed frame, real room location.
- Final RSSI ≥ -67 dBm (PASS after 0+ fixes).
- If fixes applied: Fix A/B/C/D type described, before-RSSI, after-RSSI per fix.
- If hardware purchased (pigtail, extender): BOM update line added.
**Evidence to save:**
- `docs/pm/evidence/AF-146-wifi-rssi.md` (3 raw readings, worst; fix steps with before/after dBm; final pass dBm)
- Optional: `docs/photos/mf-wifi/nano-placement-fix.jpg` (if Fix A relocation applied)
**Safety considerations:**
N/A — RF signal measurement.
**Known uncertainties:**
If wall material between frame location and AP is thick brick/concrete, Fix D (range extender) may be the only feasible fix regardless of Nano placement. If frame is within 2 m of AP with line of sight, RSSI ≥ -50 dBm with no fixes needed.
**Failure response:**
Fix D (range extender) still fails → check if AP is on 5 GHz (Nano-W may only have 2.4 GHz radio). Switch AP to 2.4 GHz channel 1/6/11 (non-overlapping) or enable 2.4 GHz band. 2.4 GHz penetrates walls 2× better than 5 GHz.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 13 "Wi-Fi signal strength inside closed frame"
- AF-123 (Stage 2 Wi-Fi/SSH: Nano static IP, baseline connection now tested inside metal)
- AF-150 Item 17 (72 h test: Wi-Fi sustained requirement)
- U-040 uncertainty (Nano service access) + Wi-Fi reliability linked
**Labels:** validation mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-147: Item 14 Panel mechanical mounting: 6 panels → backplate fastener layout. Use panel mounting holes (M3 or M2.5) in each panel corner, 4 panels × 4 holes + alignment pins at pixel pitch 6 mm consistency intervals. Alignment pins prevent seam offset from screw-hole tolerance. Backplate material = 5 mm acrylic OR 12 mm MDF OR 9 mm plywood. Confirm flatness: lay straightedge across backplate, no >0.5 mm gap.

**Task ID:** AF-147
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 14 Panel fastener layout on backplate. 6 panels × 4 corner holes (M3/M2.5). Alignment pins at 6 mm pixel pitch intervals to prevent screw-hole-tolerance seam offset. Material: 5 mm acrylic/12 mm MDF/9 mm plywood. Flatness <0.5 mm with straightedge.
**Description:** Design the exact hole pattern on the backplate for mounting all 6 panels. ADR-019 (assumed: wall mount only) backplate = rigid sheet that holds panels flat + aligned. Poor fastener layout = panels misaligned by 1 mm at seams = visible pixel jump (1 pixel = 6 mm for P1.5? Actually P1.53 = pixel pitch 1.53 mm, so 1 mm panel offset = ~2/3 of a pixel = VISIBLE seam defect). Layout needs TWO things: (1) 4 corner mounting screws per panel for mechanical hold; (2) 2 ALIGNMENT PINS per panel (press-fit metal or plastic dowel pins) that locate panels EXACTLY at pixel-pitch grid positions BEFORE tightening screws. Alignment pins correct for screw-hole ±0.2 mm tolerance in drilled holes.
**Inputs:**
- **Panel pitch:** P1.53-128×64, each panel W_mm = 128 pix × 1.53 mm/pix = 195.84 mm; panel H_mm = 64 × 1.53 = 97.92 mm. (MEASURE actual physical W/H of one panel with calipers — stated pitch may be 1.53 ±0.01 mm; actual assembled W may be 196.0 ±0.2 mm. Use ACTUAL measured W/H from AF-134 if measured there.)
- **Layout: 6 panels = 3 rows × 2 cols per row.** Total canvas W_mm = 2 × panel_W; H_mm = 3 × panel_H. Add bezel margin on each side of backplate: extra 20 mm left/right/top/bottom beyond outermost panel edges. Backplate total W_total = 2*panel_W + 40 mm; H_total = 3*panel_H + 40 mm. (Large; panel array ~392 mm × 294 mm, backplate ~432 × 334 mm. Stated in prompt 1536 mm wide? — likely refers to pixel count × pitch with 6mm. Re-check: prompt says "256 panels × 6 mm pitch" — that suggests user assumed P6 not P1.53; actual panel P1.53. Use actual panel dims MEASURED, not guessed.)
- **Backplate material choice (3 valid options, pick one):**
  1. **5 mm cast acrylic (PMMA):** Transparent or black. Rigid, light, laser-cuttable easily, 5 mm thick = adequate flatness for <0.4 m span. $ cost medium.
  2. **12 mm MDF (medium-density fiberboard):** Cheap, flat sheet, very rigid at 12 mm. Heavy (~2× acrylic). Easy drill/CNC. Paintable. $ cost low.
  3. **9 mm birch plywood:** Lightest, sustainable, screw-holds well. 9 mm rigid enough. Nice grain if finished naturally. $ cost medium.

**Hole pattern specification:**
Define coordinate system: (0,0) = backplate bottom-left corner (back side view; panels attach to front side). Panel numbering: #1 = top-left, #2 = top-right, #3 = mid-left, #4 = mid-right, #5 = bot-left, #6 = bot-right. Each panel n has:
- Bottom-left corner of panel's PCB (on backplate front face): (panel_n_x0, panel_n_y0). Compute from grid: panel_W × col + bezel_left; panel_H × row_index_from_bottom + bezel_bottom. (Row index from bottom: bot = 0, mid = 1, top = 2.)
- 4 corner MOUNTING HOLES per panel: panel holes are at PCB corners, 4 mm inset from each PCB edge. Hole diameter on backplate: M3 clearance 3.3 mm or M2.5 clearance 2.7 mm (USE whichever matches panel's actual threaded hole — verify on one panel: screw test M3 in panel hole. If M3 binds → M2.5).
- 2 ALIGNMENT PINS per panel: locate pins at two opposite corners of each panel (top-left + bot-right of PCB). Pin diameter Ø = 2.0 mm (tolerance +0/-0.02 mm precision dowel pins if purchased, or 2 mm drill with M2 screw shank as pin). Pin holes on backplate = 2.0 mm press-fit (drill 1.95 mm for acrylic/MDF, hammer pin in). Pin holes on panel = check if panel PCB already has unused tooling holes at corners (usually Ø2 mm); if yes, use them. If no tooling holes, skip alignment pins for those specific panels and use 2-sided tape between panels as backup (but pins preferred).

**Flatness check (after backplate cut in Item 15, before any drilling):**
Lay a long metal straightedge (≥ 0.5 m) diagonally across backplate, both diagonals. Hold up to light: NO gap >0.5 mm between straightedge and backplate anywhere along the line. >0.5 mm → backplate warped; reject sheet and buy another (acrylic warps less than MDF/plywood stored vertically).
**Why this task exists:** "Why not just drill holes by eye?" — 1 panel drilled 0.2 mm offset at 6 positions in the grid accumulates to 1.2 mm error at the far corner = 1 mm VISIBLE seam. Alignment pins locate panels at EXACT positions before screws are tightened; they are the secret to a perfect-looking seam. Screw-hole tolerance drill wander is ±0.2 mm; pins are ±0.01 mm. The JIRA MF Item explicitly requires alignment pins.
**Prerequisites:** AF-134 (if panel physical W/H measured). If not: measure panel dims NOW as part of this task (Item 1 dims task also asks for it; cross-reference). Screw test M3 vs M2.5 in panel mounting hole.
**Blocked by:** AF-134 (partial — dims), Item 1 backplate design.
**Required hardware:** 1 physical panel (for M3 vs M2.5 screw test), calipers for panel W/H.
**Required tools:** Calipers. Metal straightedge ≥ 500 mm (for flatness check procedure description now, actual flatness check performed Item 15 after cut).
**Required software:** None. Design on sketch v9 or spreadsheet with coordinate calculation.
**Exact execution steps:**
1. **Measure panel W/H actual (AF-134 might have it; if not, measure now):**
   - Take one panel PCB. Calipers measure PCB edge-to-edge long side (128 pixel side). Record panel_W_mm = ____ (target ~195.8 mm for P1.53). Short side = panel_H_mm = ____ (~97.9 mm). Measure all 6 panels? No — they're all same batch; assume W/H identical within ±0.1 mm. (Optional: check #1 and #6 to confirm.)
2. **Screw size test:** Find the 4 panel mounting holes on panel rear corner PCB. Take M3×6 mm screw. Try thread into hole. If screws in smoothly with 2 finger turns (tight fit but no cracking) → M3. If binds hard or won't start → M2.5. Document panel fastener = M3 or M2.5. If neither fits → measure hole ID with calipers, get tap/screw accordingly.
3. **Backplate outer dims calculation (3 material options):**
   - Bezel margin per side = 20 mm (min 15, choose 20).
   - W_total = 2 × panel_W + 40 mm. H_total = 3 × panel_H + 40 mm.
   - Pick material from 3 options. Document material choice with rationale (e.g., "12 mm MDF: cheapest, most rigid at this span, local hardware store has 430×340 sheet").
4. **Coordinate list (Excel, Markdown table, or CAD file):**
   For each panel n (1-6):
   - Determine row (0=bot,1=mid,2=top) and col (0=left,1=right).
   - panel_n_x0 = 20 mm + col * panel_W.
   - panel_n_y0 = 20 mm + row * panel_H.
   - 4 mounting holes: each inset 4 mm from PCB corner. So TL = (x0+4, y0+panel_H-4). TR = (x0+panel_W-4, y0+panel_H-4). BL = (x0+4, y0+4). BR = (x0+panel_W-4, y0+4). 4 holes per panel × 6 panels = 24 mounting holes.
   - 2 alignment pins per panel (top-left and bot-right corners of PCB, tooling hole location if panel has them, else 4 mm inset same as screw hole but diameter = Ø2.0 mm). 12 pin holes.
   Total holes: 36. List them all with (X,Y, diameter, purpose: M3_screw / Ø2_pin).
5. **Flatness check procedure description:** "Step 1 Item 15 build: acquire backplate sheet at chosen dims. Lay straightedge along diag1 and diag2. Hold up to light. Any gap >0.5 mm along line = REJECT sheet; swap for flatter one."
6. Save coordinate table + sketch v9 with hole pattern.
**Expected result:** Material choice. 24 mounting hole + 12 pin hole coordinate list. Diameters match screw test. Flatness check procedure written.
**Acceptance criteria / DoD:**
- panel_W and panel_H: PHYSICALLY measured (not datasheet assumed). Values recorded.
- Screw size (M3 or M2.5): physical screw-turn test confirmed. Value recorded.
- Backplate material + thickness chosen, rationale written. W_total/H_total computed.
- Hole coordinate table: 6 panels × 4 screws = 24 screw holes (X, Y, Ø clear_M3/M2.5). 12 pin holes (X, Y, Ø2.0). 36 hole rows.
- Flatness check procedure with 0.5 mm gap limit.
- Sketch v9 with 36 hole positions (or spreadsheet CSV).
**Evidence to save:**
- `docs/pm/evidence/AF-147-panel-mount-layout.md` (panel_W/H measured; screw size; backplate material+choice rationale; W_total/H_total; 36-row hole table; flatness procedure)
- Optional spreadsheet CSV `docs/pm/evidence/AF-147-backplate-holes.csv` (columns: X_mm, Y_mm, Dia_mm, Purpose)
- Sketch v9 file `docs/mf-layout-sketch-v9.png` (hole pattern overlay on backplate)
**Safety considerations:**
N/A — mechanical dimensioning. (Safety: panel heavy? 6 panels × 0.2 kg each = 1.2 kg; backplate ~2 kg; total ~3-4 kg — Z-clips rated for 20 kg handle it; see Item 16.)
**Known uncertainties:**
Prompt mentioned 1536 mm wide (256 × 6 mm pitch) — that was a placeholder for P6 64×32 panels. Actual P1.53-128×64 panels are much smaller. The MEASURED values rule; ignore placeholder estimates.
**Failure response:**
Mounting holes drilled Item 15 in wrong place → if error >1 mm, alignment pins won't fit; drill new hole 2 mm away, fill old hole with wood filler / epoxy (MDF/plywood easy, acrylic use acrylic cement patch). Alignment pins fail (no tooling hole on panel PCB) → use 2-sided thin foam tape between panel PCB and backplate at the two alignment positions (still adds lateral friction to prevent shift; acceptable fallback).
**Source references:**
- JIRA.md §Mounted Frame Phase Item 14 "Panel mechanical mounting 6 panels × 4 holes + alignment pins"
- AF-134 Item 1 (component dims: panel dims if measured there)
- BOM.md H-24 3D printed PLA brackets reference; H-13 Z-clips for wall
- ADR-024 DEFER cutting (this task = design; NO cutting yet. Item 15 = actual cut/drill)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-148: Item 15 Prototype backplate BUILD. Cut to outer dimensions (256 panels × 6mm pitch = 1536mm wide + left/right bezel; 192 pix × 6mm = 1152mm tall + top/bot bezel). Drill fastener holes at panel positions. Install standoffs between panels and backplate (≥5 mm clearance for PCB components on panel rear). Mount all 6 panels, verify pixel alignment across seams (seams <0.5 mm visible gap, no pixel row offset by >1 px which would be 6 mm).

**Task ID:** AF-148
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 15 Backplate PROTOTYPE BUILD (actual cut/drill/standoffs/mount 6 panels). Cut to Item 14 dims. Drill 36 holes per Item 14. Standoffs ≥5 mm panel PCB clearance. Mount 6 panels. Verify seams <0.5 mm visible gap; no pixel row offset >1 px (1.53 mm for P1.53 per prompt? = 1.5 mm approx).
**Description:** First ACTUAL BUILD step of the frame (Items 1-14 were design only; Item 15 = physical construction begins). This is the prototype backplate (it's also the production backplate if alignment passes; we call it prototype because we verify alignment before committing to finish material). Dimensions: Item 14 had panel_W × 2 + bezel_left+right and panel_H × 3 + bezel_top+bot = actual measured dims (they may be ~400 mm × 330 mm, not the 1536 mm placeholder in prompt — use Item 14 calculated numbers). "256×6=1536" was an old placeholder; actual dims from Item 14 rule.
Build steps VERBATIM from JIRA Item 15:
1. **Cut backplate to outer dims.** Use Item 14's W_total × H_total. Material: acrylic / 12 mm MDF / 9 mm plywood chosen in Item 14. Tools: circular saw + straightedge guide (MDF/plywood) OR laser cutter (acrylic, at maker space) OR jigsaw (fine tooth blade, slow). Cut clean edges, deburr with sandpaper 220 grit.
2. **Flatness check (Item 14 procedure, actual run now):** Lay metal straightedge diagonally 2 ways. Light gap check. Any >0.5 mm gap = reject, use different sheet OR shim high spots later with panel standoffs of slightly different height.
3. **Drill 36 holes per Item 14 coordinate list.** Mark centers with center punch (metal) or scribe+dot (acrylic). Drill with correct clearance bit (M3 = 3.3 mm, M2.5 = 2.7 mm, Ø2 = 2.0 mm drill). Drill speed slow for acrylic/mdf, chip-clear every 1 mm to avoid melting/wandering. Deburr both sides of each hole.
4. **Install standoffs (per panel, ≥5 mm clearance between backplate front face and panel PCB rear components).** Standoff choice: (a) brass M3 female-female threaded hex standoff 6 mm length (common, cheap — gives 6 mm clearance, exceeds 5 mm min). (b) 3D printed spacer ring 6 mm tall, slide screw through it. (c) Nylon spacer. Mount: pass M3 screw through BACK of backplate from rear → through standoff spacer → into panel PCB corner threaded hole. Tighten with screwdriver on backplate side; panel PCB gets just-firm pressure (don't crack PCB). Do NOT overtorque M3 into plastic — 0.2 Nm is enough; hand tight + 1/8 turn.
5. **Install alignment pins if using.** Ø2.0 mm dowel pins (or M2 screw shanks cut short): press-fit into backplate Ø1.95 mm holes with small hammer. Height = standoff height (6 mm) + panel PCB thickness (1.6 mm) = 7.6 mm pin protrudes from backplate surface; panel tooling hole slips over pin → panel seated.
6. **Mount all 6 panels in order #5 (bot-left), #6 (bot-right) first, then #3/#4 mid, then #1/#2 top.** Seat each panel on its 2 alignment pins (or onto standoffs if no pins). Start all 4 corner screws LOOSE first for all 6 panels. Do NOT tighten any until ALL 24 screws are started (allows shift for alignment).
7. **Alignment verification (LOOSE screws, before tightening ANY):**
   Check EVERY SEAM (5 seams total: 2 vertical between cols, 2 horizontal between rows × 3 rows = 2 horiz plus 1 mid-horiz actually total horiz seams = 2, total vertical seams = 2, so 4 internal seams? 3 rows × 2 cols → vertical seams 2 (between col 0-1 at rows top/mid/bot) = 3 vertical physical seam lines, each spanning 2-panel height. Horizontal seams: between rows 0-1 and rows 1-2 = 2 horizontal physical seam lines, each spanning 2-panel width. Total 5 internal seams (correct per M4 GATE-PASS: 3 vertical + 2 horizontal = 5 seams). At every seam:
   a. **Seam gap width:** Visually inspect between the two panel PCB edges at the closest point. Should be ≤ 0.5 mm (can't fit a fingernail into the gap; fingernail thickness ~0.4 mm). >0.5 mm visible gap → loosen both panels' screws, shift together, re-loosen → retest.
   b. **Pixel row offset at seam (most critical):** Hold head at 45° to panel surface, sight along the seam horizontally and vertically. Check that pixel rows CONTINUE across the seam without stepping. Example: a horizontal red line of pixels enters the seam at y=32 from panel left edge → it CONTINUES at y=32 on the right panel. No "step" by more than 1 pixel. 1 pixel offset for P1.53 = 1.53 mm physical step = VISIBLE (tolerate ≤ 1 pixel max, prefer 0). >1 px offset → loosen, shift panel up/down on standoffs, retighten loose.
8. **After ALL seams pass a+b with LOOSE screws:** Tighten 24 screws in CRISS-CROSS pattern: top-left #1 → bot-right #6 → top-right #2 → bot-left #5 → mid-left #3 → mid-right #4, repeat for inner screws. Each tightened to "finger tight + 1/8 turn" (no power tools). Retightening may shift seams; after all tight, RE-CHECK all 5 seams a+b one more time. If any seam now fails, loosen 4 screws of the 2 offending panels, shift, re-tighten.
9. **Final visual check:** Full frame view from 2 m distance. Seams should be almost invisible (no bright/dark pattern at seams indicating alignment error).
**Why this task exists:** Alignment is make-or-break for the product's visual quality. A user's first impression is "that looks like one big screen" (0.3 mm perfect seams → awesome) vs "6 panels taped together" (1 mm gaps, pixel steps → cheap). Build quality of seams = perceived quality of the whole product.
**Prerequisites:** COMPLETED: Item 14 (backplate dims, hole table, material choice, standoff spec). Hardware on hand: backplate sheet, M3/M2.5 screws × 24 + spare, standoffs × 24 (6 panels × 4 corners), alignment pins × 12 (if using), tools.
**Blocked by:** AF-147
**Required hardware:** Backplate material (Item 14 choice) cut to size, 24 standoffs 6 mm (M3 or M2.5), 24 screws matching standoff thread and panel hole thread, Ø2.0 mm dowel pins ×12 (optional but recommended), deburr sandpaper.
**Required tools:** Saw (circular/jigsaw/laser). Drill with bits: 3.3 mm, 2.7 mm, 2.0 mm. Center punch (metal backplate) or scribe. Screw driver (Phillips #1 M2.5 or #2 M3). Mallet (for dowel pins). Metal straightedge ≥ 500 mm. Light source for gap check. Digital calipers (measure seam gap if borderline 0.4 vs 0.6 mm).
**Required software:** None.
**Exact execution steps:** As 1-9 above (verbatim from JIRA Item 15).
**Expected result:** 6 panels mounted. 5 seams: gap <0.5 mm every seam, pixel row offset ≤ 1 px every seam (0 preferred). Backplate flat. Standoffs all ≥5 mm clearance (no PCB component touch backplate).
**Acceptance criteria / DoD:**
- Backplate cut to Item 14 W_total × H_total, edges clean, deburred.
- Flatness check: 2 diagonals, 0 gaps >0.5 mm documented (photo or pass line).
- 36 holes drilled per coordinate table; deburred both sides.
- Standoffs installed: ALL 24 standoffs give ≥5 mm clearance. Visually inspect PCB rear near standoff: no component touches backplate.
- Alignment pins (if used): 12 pins press-fit, panels seat over pins.
- 5 seams × 2 checks = 10 sub-checks. Pass = 10/10: (a) gap <0.5 mm ALL 5; (b) pixel row offset ≤ 1 px ALL 5. (Tolerate 1 seam at 0.6 mm gap OR 1 seam at 2 px offset; both = FAIL, redo alignment.)
- 2 m full view: seams nearly invisible.
**Evidence to save:**
- `docs/pm/evidence/AF-148-backplate-build.md` (flatness 2 diagonals pass; 5 seams × 2 checks = 10 pass results table; photo refs)
- `docs/photos/mf-backplate/backplate-drilled.jpg` (holes drilled, no panels)
- `docs/photos/mf-backplate/standoffs-installed.jpg` (before panels)
- `docs/photos/mf-backplate/seam-closeup-top-vert.jpg` (3 vertical seams + 2 horizontal seams: close-up photos of each = 5 seam close-ups; each seam with ruler reference edge for gap reading)
- `docs/photos/mf-backplate/full-mount-2m-view.jpg` (photo from 2 m back at eye level, straight-on view; overall frame)
**Safety considerations:**
⚠️ Power tools (saw, drill). Eye protection (safety glasses) mandatory for saw/drill. Ear protection for circular saw ≥ 85 dB. MDF dust = respiratory hazard; use dust mask + vacuum dust collection. Acrylic chips = eye hazard, also cracked acrylic edges sharp. Deburr ALL cut edges before handling barehanded. Over-tightening M3 into panel PCB plastic: screw strips PCB thread (panel ruined). Hand-tight + 1/8 turn MAX.
**Known uncertainties:**
Standoff 6 mm may be short if panel rear has a tall electrolytic capacitor at a corner. Solution: after step 4 (standoffs loose), visually inspect each panel corner rear for tall components; if any cap >5 mm at that corner, swap standoff to 8 mm or 10 mm (buy mixed standoff kit: 6/8/10/12 mm × 2 each, use as needed).
**Failure response:**
Seam gap >0.5 mm after re-loosening screws: the 2 panels' mutual corner standoff heights are uneven (one standoff 5.8 mm, adjacent 6.2 mm → panel tilts, opens gap). Fix: measure actual standoff stack heights, add a thin 0.3 mm cardboard washer under the short standoff. Pixel row offset >1 px despite alignment pins: panel hole-to-edge tolerance varies by 0.5 mm (manufacturing). Fix: without pins, you can shift 0.5 mm; with pins, you can't (pin locks location). Use "loose all screws, push panels together against alignment jig" method: 2 long straightedges clamped along the top and bottom edges, 6 panels squeezed between → tightens all seams.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 15 "Prototype backplate build, cut/drill/mount 6 panels, verify seams"
- AF-147 (coordinate list, material choice, flatness procedure)
- AF-114 (M4 GATE-PASS: 5 seams correct — validated at bench level; now mechanical mount locks in those seams permanently)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 safety-review critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-149: Item 16 Final frame: finish material + aesthetics + wall-mount bracket hardware selection. Frame sides: aluminum extrusion or wood molding or 3D printed. Wall mount: French cleat (2-part, plywood or aluminum) rated ≥ 20 kg load. BOM update if new items purchased post-MF design.

**Task ID:** AF-149
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 16 Final frame finish + wall hardware. Sides: aluminum extrusion OR wood molding OR 3D printed. Wall mount: French cleat 2-part rated ≥ 20 kg. BOM update new items.
**Description:** Choose the aesthetic finish materials (frame side rails/bezel) and the wall-mounting bracket hardware. User-facing choices = taste-based, so we give 3 valid side rail options with pros/cons; executor picks one. Wall mount choice is fixed (French cleat) per JIRA MF.
**Side rails / bezel (4 pieces: top/bottom/left/right):**
- **Option 1: Aluminum extrusion profile (BEST structural finish).** Standard V-slot or T-slot 20×20 mm aluminum profile, cut to frame backplate outer dimensions. 4 miter cuts 45° at corners, connect with L-brackets inside. Silver anodized = industrial look. Black powder-coated = modern. Pros: rigid, lightweight, screw anywhere in slot for brackets. Cons: cost ~$20 for 2 m × 4 profiles, miter cut tool needed (hacksaw OK with miter box).
- **Option 2: Solid wood molding / picture frame stock (GOOD, warm look).** 20 mm × 20 mm hardwood molding (pine, oak, maple) or MDF wrapped with wood veneer tape. Miter 45°, glue + brad nails. Stain or paint. Pros: warm look, cheap at hardware store, easy cut. Cons: wood expands/contracts with humidity; not as rigid as aluminum for long spans >0.5 m (add internal corner gussets).
- **Option 3: 3D printed corner brackets + thin wood/MDF strips (GOOD budget custom).** 4 corner bracket pieces 3D printed (PLA+ 20% infill, 3 walls) at each corner, connecting vertical and horizontal side strips. Strips = 6 mm MDF or plywood ripped to 20 mm width, painted black. Pros: fully custom dimensions, no metalworking tools, cost $5. Cons: 3D printer time ~4 h for corners.

**Wall mount bracket (FRENCH CLEAT, one recommended method, not alternatives):**
JIRA MF Item 16 specifies: "Wall mount: French cleat (2-part, plywood or aluminum) rated ≥ 20 kg load." French cleat = two interlocking 45° beveled strips. One strip screws to WALL (studs, NOT drywall alone). The mating strip screws to BACKPLATE rear face, top 100 mm area. You lift the frame, hook the cleat over the wall cleat, lower → gravity locks it. Cannot fall off unless lifted straight up 20 mm.
- **Cleat material choices:**
  1. **Plywood 12 mm (CHEAP, strong, classic):** Cut two strips of 12 mm birch plywood, each 50 mm tall × (backplate_W - 40 mm) long. Table saw or circular saw with 45° bevel rip cut. Sand smooth. Load rating: 12 mm plywood cleat spanning 0.4 m = holds >50 kg easily (exceeds 20 kg requirement by 2.5×).
  2. **Aluminum 6061-T6 3 mm sheet bent to 45° or extruded cleat profile (PREMIUM):** Buy ready-made "French cleat hanger" 1 m aluminum, or cut and bend 3 mm Al. Load rating ≥ 30 kg. Thinner profile than wood.
- **Installation requirements (verbatim):**
  - WALL cleat: 4 wood screws × #10 × 2.5" (60 mm) length. Screws MUST go into WOOD WALL STUDS. Use stud finder. If studs are 400 mm apart and cleat is 380 mm long, mount cleat with 1 end into stud A and the other end into stud B (hit 2 studs minimum). Drywall anchors alone = FAIL (pull-out ~10 kg, frame + backplate ~5-8 kg safety factor 2.5 needs 20 kg).
  - BACKPLATE cleat: 4 wood screws (plywood cleat) or machine screws + T-nuts (aluminum backplate) into backplate top 100 mm area. Counterbore screw heads so cleat sits flush against backplate rear.
  - TEST: after mounting wall cleat, hang 15 kg sandbag or water bucket from the backplate cleat for 1 minute. No bend, no slip → rated ≥20 kg.

**BOM update:** Any newly purchased items (aluminum extrusion lengths, wood molding, #10 wood screws, plywood for cleat, 3D printer filament if option 3) → add as OPTIONAL POST-MF lines in BOM, category Mechanical. Many may already be in BOM H-13 (Z-clips researched, now we use French cleat actual).
**Why this task exists:** French cleat is the gold standard for heavy wall art/mirror mounting. Z-clips from BOM H-13 are easier to disengage (risk of frame falling if bumped from below). French cleat requires LIFTING UP 20 mm to remove = safe. Load rating ≥ 20 kg = "a child hangs on the frame, it doesn't fall" safety factor. Side rail finish = aesthetic, user's home decor.
**Prerequisites:** Item 15 (backplate outer dims known W_total, H_total → side rail cut lengths). Hardware: stud finder, screws, saw for cleat, material chosen for rails + cleat.
**Blocked by:** AF-148 (backplate W_total, H_total known).
**Required hardware:** Side rail material (aluminum extrusion / wood / 3D print filament), French cleat material (12 mm plywood 1 m length × 100 mm strip, or aluminum), #10 × 2.5" wood screws × 8 (4 wall, 4 backplate). Stud finder.
**Required tools:** Miter saw / miter box + hacksaw (45° cuts). Table saw or circular saw with bevel (French cleat 45° rip). Drill + #10 countersink bit. Stud finder. Tape measure.
**Required software:** Optional: 3D modeling for corner brackets if Option 3.
**Exact execution steps:**
1. **Side rail selection + build:**
   a. Choose 1 of 3 options, document rationale.
   b. Cut 4 rails: top = bottom = W_total + rail_overlap (aluminum extrusion = miter, total length per rail = outer dimension; wood molding = outer length with 45° return). Vertical rails left/right = H_total + overlap.
   c. Fasten 4 rails together at corners: aluminum = L-brackets + slot nuts; wood = glue + brads; 3D print = corner brackets + screws into strips.
   d. Finish: if wood → stain/paint + 2 coats clear polyurethane. If aluminum → leave anodized/powder-coated; if raw, wipe with acetone.
2. **French cleat build (plywood method described; adapt for aluminum):**
   a. Rip 12 mm plywood at 45° bevel into two 50 mm tall strips, length L = W_total - 40 mm (cleat inset 20 mm from each backplate edge).
   b. Label 2 strips: WALL cleat (bevel faces UPWARD AND INWARD — "wall side hook up") and BACKPLATE cleat (bevel faces DOWNWARD AND OUTWARD — interlocks when lowered onto wall cleat).
   c. WALL cleat: 4 countersunk holes, 100 mm from each end + 2 evenly between = 4 holes. Locate 2 holes to hit studs. Mark wall with stud finder. Mark stud centers. Drill pilot holes in studs. Mount wall cleat with #10 × 2.5" wood screws, 4 screws. Use level for horizontal.
   d. BACKPLATE cleat: mount to backplate REAR face, top edge of backplate 10 mm below top of backplate (so frame top hides cleat). 4 screws, countersunk, spaced as wall cleat.
   e. Load test: mount backplate cleat (temporarily held in bench vise). Hang 15 kg bucket. 1 min hold. No deflection visible at cleat joint. PASS.
3. **Assemble rails onto backplate:** Screw or glue side rails to backplate edges. Clamp 30 min if glued.
4. **Frame total weight:** Place completed frame (backplate + panels + rails) on bathroom scale. Record kg. Target ≤ 8 kg. (French cleat 20 kg rating / 8 kg actual = SF 2.5, safe.)
5. Update BOM with any new purchases.
**Expected result:** Side rails attached. French cleat both halves built and load-tested 15 kg for 1 min. Frame total weight measured.
**Acceptance criteria / DoD:**
- Side rails: option chosen documented. 4 rails attached to backplate, corners mitered or bracketed. Finished (painted/stained/clear if wood).
- French cleat: 2 plywood strips 45° bevel, correct wall vs backplate orientation. Wall cleat mounted level, #10 screws hit studs (2 studs minimum). Backplate cleat mounted top rear.
- Load test: 15 kg bucket × 1 min on backplate cleat → no deflection, no slip, no screw pullout. PASS.
- Frame total weight documented, ≤ 8 kg typical.
- BOM.md updated: new items added to Mechanical section as OPTIONAL POST-MF.
**Evidence to save:**
- `docs/pm/evidence/AF-149-frame-finish-hardware.md` (side rail option; cleat build details; stud hit count; load test duration + result; total weight; BOM update lines)
- `docs/photos/mf-finish/side-rails-front.jpg` (frame with rails, front view)
- `docs/photos/mf-finish/cleat-wall-mounted-level.jpg` (cleat on wall, bubble level visible in photo)
- `docs/photos/mf-finish/cleat-load-test.jpg` (15 kg bucket hanging from cleat during test)
- `docs/photos/mf-finish/frame-weigh-in.jpg` (frame on scale, scale display readable)
**Safety considerations:**
⚠️ Wall mount LOAD RATING ≥ 20 kg IS SAFETY. A frame falling off wall = injury risk. Do NOT use drywall anchors alone (even "toggle bolts" rated 50 kg each — they can crack drywall under sustained load over years). STUD SCREWS ONLY for wall cleat (wood-to-wood screw shear strength = 100+ kg per #10 screw). If studs don't align → mount 19 mm plywood backer board to studs FIRST (3 screws per stud, 600 mm × 150 mm board), THEN mount cleat to the backer board. 45° cleat bevel cut: if bevel reversed (30° instead of 45°), cleat slips under vibration. Verify angle with bevel gauge or protractor: EXACTLY 45° ±2°.
**Known uncertainties:**
Side rail option taste-based: executor picks what matches home decor. All three valid for DoD. Total weight may exceed 8 kg if using thick MDF backplate + aluminum rails (10 kg still fine for 20 kg cleat rating; 2.0 safety factor still good).
**Failure response:**
Cleat bevel wrong angle (slipped during load test): re-rip the 2 strips with saw fence set correctly at 45°. Studs impossible to hit (steel studs in apartment): use 1/4" toggle bolts rated 50 kg × 6 (exceeds 20 kg by 15×) into steel studs or drywall — document the switch.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 16 "Frame finish + French cleat wall hardware ≥ 20 kg"
- BOM H-13 (Z-clips researched originally; we use French cleat now — more robust)
- ADR-020 (wall mount only, not desktop; cleat is for wall)
**Labels:** mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 safety-review docs critical-path yes purchasing
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-150: Item 17 Extended unattended testing (72 hours, wall-mounted, closed frame, normal home environment — not lab bench). Dashboard content running normally. Check at least every 12 hours via SSH + photo. Verify: no crashes, no panel dropout, no thermal issues (hand test backplate), Wi-Fi connection sustained, widgets updated with fresh data, clock accurate to <1 min. Take photo evidence at start, 24h, 48h, 72h (4 photos).

**Task ID:** AF-150
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF Item 17 72 h EXTENDED WALL-MOUNT UNATTENDED TEST. Closed frame, real home location (not lab bench). Dashboard content. Check every 12 h via SSH + photo: 0 crashes, 0 panel dropout, thermal OK hand test, Wi-Fi sustained, widgets fresh data, clock <1 min drift. 4 photos: start, 24h, 48h, 72h.
**Description:** The ultimate V1 validation. Mount the completed frame on its ACTUAL WALL in the user's home, in its final location. Run the dashboard (clock + weather + calendar + Spotify + art rotation widgets) continuously for 72 HOURS (3 days). This is a real-life soak test — catches failure modes that 24-hour bench tests and 1-hour closed-box tests miss: thermal drift over a day-night cycle (ambient temp drops 5 °C at night, PSU warms day), Wi-Fi router nightly reboot causes transient dropout, widget HTTP 503 errors at 3 AM when API maintenance window hits, daytime 72-hour memory leak in widget HTTP client that builds to OOM crash after 50 h, art rotation directory walk hits a corrupted JPEG after 200 rotations → Pillow crash. Check every 12 hours (T0 start, T12, T24, T36, T48, T60, T72 end — minimum checks every 12 h = 7 checkpoints, but user can do more frequent).
**Checklist at each 12 h checkpoint:**
1. **Frame visual (photo + visual):** Take a photo. Scan all 6 panels: (a) 0 panels blank? (b) No garbled rows (any row shows correct widget content, not random LED noise)? (c) Seams still look correct (no panel physically shifted off alignment pins after 3 days of thermal cycling)?
2. **SSH reachable + process alive:** SSH from user laptop to Nano. `ps aux | grep python` → the main.py process PID should exist (if PID changes = watchdog restarted it → acceptable with log note; if process MISSING = CRASH → FAIL). `free -h` → available RAM not critically low (<100 MB free on 512 MB Nano = OK; <20 MB = OOM risk). Record PID + free RAM.
3. **Thermal backplate hand test:** Touch backplate metal/wood surface at center 5 s. Should be cool-warm, no more than 35-40 °C (body temp 37 °C = slightly above hand temp; if too hot to hold 5 s → Item 11 closed-box test may have missed ambient drift → consider fan).
4. **Wi-Fi sustained:** During SSH session, `ping -c 2 8.8.8.8` → 0% packet loss. `iw dev wlan0 link | grep signal` → RSSI still ≥ -67 dBm (Item 13 fix value). Document dBm at each check.
5. **Widgets data fresh:** Compare displayed clock time to SSH `date` command on Nano. Clock widget display time vs system time difference < 60 seconds (1 min). Look at weather widget: temperature value? "stale as of HH:MM" banner visible? If banner present → acceptable (offline fallback works) but note it. Look at calendar/Spotify: data present or placeholder?
**Evidence:** 4 required photos (T0, T24, T48, T72). Additional checkpoints optional. Log sheet for each checkpoint: time, visual Y/N, PID Y/N, RAM free MB, backplate feel, Wi-Fi dBm, clock diff seconds, widget notes.
**Why this task exists:** JIRA MF explicitly requires 72 h wall test. It's the product release gate. The AF-121 MR 24 h bench test was shorter. 72 h wall test = "I'd be happy with this installed in my living room." Any bug that shows up ONLY after 48+ hours (memory leak, widget HTTP client connection leak, slow thermal drift) — this test catches it.
**Prerequisites:** COMPLETED: Item 16 (frame on wall, French cleat installed). APP: dashboard widgets running (AF-133 main.py) with real or placeholder data. Wi-Fi OK at wall location (Item 13 pass). Thermal OK closed box (Item 11 pass).
**Blocked by:** AF-149 (frame on wall), AF-133 (dashboard app), AF-146 (Wi-Fi at wall location OK), AF-144 (closed-box thermal passed)
**Required hardware:** Frame WALL-MOUNTED in final home location. Wi-Fi AP active 72 h. User's laptop/phone on home LAN for SSH checks.
**Required tools:** None (phone camera for photos).
**Required software:** SSH client on laptop/phone (Termius, OpenSSH).
**Exact execution steps:**
1. **T0 (Day 1, Hour 0):**
   - Frame mounted on wall. PSU switched ON. Dashboard app auto-started by systemd (AF-133 verified auto-start on reboot).
   - Take PHOTO-0: straight-on view, full frame.
   - Fill checkpoint sheet row T0:
     * Clock diff (widget clock vs `date`): __ s.
     * Visual: 0 blank? Y/N. 0 garble? Y/N.
     * `ps aux | grep main.py` PID: ___. Process exists? Y.
     * `free -h` available RAM: ___ MB.
     * Backplate feel: cool / warm / hot.
     * Wi-Fi RSSI: ___ dBm.
     * Widgets: weather present? Y/N; calendar present? Y/N; Spotify present? Y/N. Stale banner visible? Y/N.
2. **T12 (Day 1, Hour 12):** Same checkpoint list. Photo optional (not required).
3. **T24 (Day 2, Hour 24):** PHOTO-1 mandatory. Full checkpoint list.
4. **T36 (Day 2, Hour 36):** Checkpoint. Photo optional.
5. **T48 (Day 3, Hour 48):** PHOTO-2 mandatory. Checkpoint.
6. **T60 (Day 3, Hour 60):** Checkpoint. Photo optional.
7. **T72 (Day 4, Hour 72 - END):** PHOTO-3 mandatory. Full checkpoint. Final verdict: PASS / FAIL.
**Verdict PASS criteria (ALL must be true at EVERY non-optional checkpoint T0/T24/T48/T72):**
- Visual: 0 blank panels, 0 garbled rows, seams unchanged (no shift). Every checkpoint.
- Process: main.py PID exists (crash = FAIL; watchdog restart OK with log note but if 3+ restarts = FAIL).
- Thermal: backplate cool-warm, no "too hot to hold 5 s". Every checkpoint.
- Wi-Fi: 100% ping to 8.8.8.8 (2/2) every checkpoint. RSSI ≥ -67 dBm at least once in T0/T24/T48/T72 (minor dip at 1 checkpoint to -70 OK).
- Clock drift: widget clock vs system time diff < 60 s (1 min). Every checkpoint. (Absolute system time vs NTP drift < 2 s — systemd-timesyncd handles that.)
- Widgets: offline fallback (stale banner) is OK, but data should be fresh if network is up.

**Verdict FAIL → mitigate:** If any criterion fails at any checkpoint, diagnose, apply fix, restart from T0 (do NOT accept "it failed at T48 but passed all others" — 72 h requires PASS for full 72 h). Common fixes: memory leak → add cron hourly restart; OOM → increase swap 512 MB; Wi-Fi drops → Fix D range extender; thermal hot → add fan; widget stale banner after 72 h despite network → HTTP client connection leak fix.
**Expected result:** 4 photos (T0/T24/T48/T72), ≥ 7 checkpoint rows. Final PASS verdict, all criteria all checkpoints.
**Acceptance criteria / DoD:**
- Frame wall-mounted 72 h continuous (wall clock time: T0 timestamp to T72 timestamp ≥ 72 h 0 min documented).
- 4 mandatory photos saved.
- Checkpoint log: ≥ 7 rows (T0 through T72). Each row has all 6 checklist fields filled.
- ALL visual/process/thermal/Wi-Fi/clock PASS at T0, T24, T48, T72 (4 mandatory checkpoints; T12/T36/T60 can FAIL with mitigating notes if T+12 recovers — but better all pass).
- 0 process crashes (PID same throughout OR 1 watchdog restart with explanation; ≥2 = FAIL).
- Clock drift < 60 s all mandatory checkpoints.
**Evidence to save:**
- `docs/pm/evidence/AF-150-72h-log.md` (7+ checkpoint rows with all fields; final verdict PASS line; any mitigations applied if fail-restart occurred)
- `docs/photos/mf-72h/photo-t0.jpg, photo-t24.jpg, photo-t48.jpg, photo-t72.jpg` (4 mandatory)
- Optional: T12/T36/T60 extra photos (proves consistency, nice to have)
**Safety considerations:**
⚠️ 72 h unattended electrical equipment on wall: follow standard home safety. Do NOT run near flammable materials (curtains, papers stacked behind frame). Ensure that the 72 h test room has a working smoke detector (basic home safety). If user leaves town for the 3 days, they should have a smart plug with energy monitoring that alerts on power anomaly, or have a neighbor check in per the 12 h schedule (the schedule requires checks, so it's not fully unsupervised — user does the checks).
**Known uncertainties:**
Widget data freshness depends on 3rd-party API uptime. Weather Underground / OpenWeatherMap have 99.9% uptime but 0.1% = 43 min/30 days of downtime; 72 h test has a ~1% chance of hitting a transient 5xx. Stale banner displaying during that time is PASS per rules (the offline fallback is working correctly).
**Failure response:**
T48 fails (process crashed after 48 h): ssh in, `journalctl -u ai-frame.service --since "48 hours ago"` → find traceback. Typical: Pillow OSError corrupted JPEG in art rotation (filter corrupted files in directory walk, skip bad images). HTTP client connection pool exhaustion (close sessions after each request). Apply fix, systemctl restart, start 72 h test from new T0. Clock drift >1 min after 72 h: systemd-timesyncd config wrong; enable NTP or force `timedatectl set-ntp true`.
**Source references:**
- JIRA.md §Mounted Frame Phase Item 17 "Extended unattended testing 72 hours wall-mounted"
- AF-121 MR 24 h bench stability (shorter predecessor, bench not wall)
- AF-132 (watchdog/reconnect layer: why process crashes are auto-recovered or detected)
- AF-117 EXP-016 (TTND metric, boot-recovery; here we test long-haul uptime of the running app)
**Labels:** validation mechanical blocked blocked:exp-exp-015 blocked:exp-exp-016 reliability docs critical-path yes
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-151: MF GATE-PASS aggregator (all 18 checkpoint items from JIRA §Mounted Frame Phase). DoD checklist: Frame on wall. All 6 panels displaying correct content with all seams correct. Brightness acceptable for daytime viewing, nighttime dim mode functional if implemented. Wi-Fi signal ≥-67 dBm. Thermal inside closed frame within limits per Item 11. Service access documented (which panel opens, USB access point, mains disconnect point). PE bonding continuity confirmed. Mains/low-voltage physical barrier installed and documented. Cable routing plan + strain relief all implemented. 72 hr extended test passed.

**Task ID:** AF-151
**Epic:** AF-009 (MF: Mounted Frame Optimization — 🔒 EXPLICIT GATED MILESTONE #6 of 6)
**Summary:** MF GATE-PASS aggregator, final milestone #6 of 6. DoD checklist: 18 JIRA MF items. Frame on wall. 6 panels correct content + seams OK. Brightness/daytime OK, dim mode functional. Wi-Fi ≥-67 dBm. Thermal within Item 11 limits. Service access doc (panel opens, USB, mains disconnect). PE bonding 3-pair continuity pass. AC/DC barrier installed + doc. Cable routing + strain relief implemented. 72 h test pass.
**Description:** The FINAL GATED MILESTONE of the whole project. Execute a formal PASS/FAIL review against EVERY SINGLE JIRA §Mounted Frame Phase item (Item 1 through Item 18 = 18 items, plus GATE-PASS Item 19 aggregator checklist = 18 checkpoint items listed in DoD). This task is the aggregator: it lists all items, references the AF-### task that completed each, collects evidence links, and signs off the milestone.
**DoD checklist VERBATIM (match these 12 aggregate bullets + each of the 18 JIRA MF items explicitly marked):**
**JIRA MF Item-by-Item 18 Check-off:**
1. Item 1 (AF-134): Exact component dims measured → PASS (evidence: AF-134 table).
2. Item 2 (AF-135): Internal layout sketch with X/Y coords → PASS (evidence: sketch v5-v9 final).
3. Item 3 (AF-136): Min frame depth determined → PASS (evidence: AF-136 layer sum).
4. Item 4 (AF-137): PSU location + intake/exhaust hole pattern → PASS (evidence: sketch v4+ holes, drill photos).
5. Item 5 (AF-138): Controller location + Nano location finalized → PASS (evidence: sketch v5 coords).
6. Item 6 (AF-139): AC/DC physical barrier installed → PASS (evidence: photo of installed barrier, chamber hatched sketch).
7. Item 7 (AF-140): Cable routing plan implemented → PASS (evidence: photo of actual wired internals, matches sketch v5; EMI 90° cross photos).
8. Item 8 (AF-141): Strain relief 4 categories → PASS (evidence: per-category installed photos, C13 anchor, bundle tie, HUB75 loop-tie).
9. Item 9 (AF-142): PE bonding 3-point continuity → PASS (evidence: 3-pair multimeter continuity reading beep photos or log, R < 1 Ω each pair).
10. Item 10 (AF-143): Ventilation/cooling assessment → PASS (evidence: AF-143 calculation table, fan verdict or passive OK).
11. Item 11 (AF-144): Closed-box thermal test 60 min → PASS (evidence: AF-144 measurements table, all within limits).
12. Item 12 (AF-145): Brightness ceiling + night dim mode → PASS (evidence: config.py constants, daytime=X%/nighttime=Y% test).
13. Item 13 (AF-146): Wi-Fi closed frame RSSI ≥ -67 dBm → PASS (evidence: AF-146 dBm value after fixes).
14. Item 14 (AF-147): Panel mounting layout + alignment pins design → PASS (evidence: AF-147 coordinate table 36 holes).
15. Item 15 (AF-148): Backplate built + 6 panels mounted seams <0.5 mm → PASS (evidence: 5 seam close-up photos, 2 m full view).
16. Item 16 (AF-149): Frame finish + French cleat ≥ 20 kg → PASS (evidence: cleat load 15 kg test photo, weigh-in total kg).
17. Item 17 (AF-150): 72 h wall test PASS → PASS (evidence: AF-150 log, 4 photos).
18. Item 18 (GATE-PASS aggregator self): Checklist this task all 18 items + the DoD bullets below.

**Additional Aggregate DoD bullets (beyond the 18 Items — some overlap, re-verify):**
- (a) Frame actually HANGING ON WALL in final location (not on bench). Photo proof.
- (b) All 6 panels displaying correct dashboard content (not test pattern). Seams <0.5 mm gap and ≤1 px offset everywhere. Photo proof.
- (c) Brightness acceptable for daytime viewing: stand 3 m away in daylight → text readable (not washed out by sun). Nighttime dim mode triggers correctly at 22:00 (test by setting system clock forward 2 minutes to 22:00, wait, confirm brightness drops to 40%).
- (d) Wi-Fi signal ≥ -67 dBm at wall location (repeat Item 13 once more after wall-mount to confirm cleat metal didn't attenuate more).
- (e) Thermal inside closed frame within limits (re-run Item 11 spot-check once at wall location, ambient home temp; quick hand check + optional IR; confirm same limits).
- (f) Service access DOCUMENTED in user-facing README: (i) WHICH panel/side opens for internal service (left service side? rear backplate lift-off French cleat?): "To open: lift frame STRAIGHT UP 20 mm off wall cleat → lower onto table → remove 4 screws on left service side panel → access internals". (ii) USB access point: "Nano debug USB-C port: reach via rear top-right cutout (no panel removal needed for SSH/debug cable). If full Nano access required → open per (i)". (iii) MAINS DISCONNECT POINT: "Always: first unplug C13 power cord from wall socket. Before opening frame additionally: confirm wall unplugged 30 s, then touch PSU metal case to discharge residual. Never open with PSU plugged in."
- (g) PE bonding continuity RE-CONFIRMED after frame on wall (if wall install disturbed any bonding wires — unlikely but verify): multimeter between PSU FG and panel frame corner, still <1 Ω beep.
- (h) AC/DC physical barrier: visually re-check service panel side photo that barrier is intact (no unauthorized cuts through barrier during cable routing changes).
- (i) Cable routing + strain relief: compare installed photos (Items 7/8) against sketch v6 — differences ≤ 20 mm, strain reliefs not removed during build.
- (j) 72 h extended test: final verdict line explicitly "PASS" in AF-150 log.

This task = compile all 18 + 10 aggregate bullets into a SINGLE Markdown checklist document, with evidence links per line. Every line = PASS/FAIL + link. If any line FAIL, this task blocks until that AF-### item re-passes. Final milestone line: "MF GATE-PASS = PASS. Project V1 proof of concept DONE." Git commit message: `docs(mf): MF GATE-PASS PASS — 6-panel frame wall-mounted in home, 72h soak OK. V1 PROOF OF CONCEPT COMPLETE.`
**Why this task exists:** It's the 🔒 milestone #6 of 6 final deliverable. Without it, we have a bunch of individual tasks done but no formal sign-off that the MOUNTED FRAME product actually works as a whole.
**Prerequisites:** COMPLETED: Items 1-17 of MF phase (AF-134 through AF-150 all PASS).
**Blocked by:** AF-134, AF-135, AF-136, AF-137, AF-138, AF-139, AF-140, AF-141, AF-142, AF-143, AF-144, AF-145, AF-146, AF-147, AF-148, AF-149, AF-150
**Required hardware:** Completed wall-mounted frame (in situ).
**Required tools:** None for this task (all tools used in prior items; just compile evidence).
**Required software:** None. Markdown editor.
**Exact execution steps:**
1. Create new evidence document `AF-151-mf-gatepass.md`.
2. Top line: "MF GATE-PASS (Milestone #6 of 6) — Final Sign-off Checklist".
3. Date. Executor name.
4. Section 1: JIRA 18 Items (1-18 above). For each:
   - N. Item X title.
   - Completed by AF-###.
   - Evidence link: `docs/pm/evidence/AF-###-filename.md` OR photo path.
   - PASS / FAIL line. All 18 PASS.
5. Section 2: Aggregate DoD bullets (a)-(j). For each:
   - (a) Bulb text.
   - Evidence.
   - PASS / FAIL. All 10 PASS.
6. Section 3: Summary line. "All 28 checkpoints (18 items + 10 aggregate bullets) PASS. MF GATE-PASS = PASS."
7. Add milestone summary: "MF GATE-PASS PASS. Project V1 proof of concept: wall-mounted 256×192 LED pixel frame, 6 P1.53 panels, Nano dashboard software (clock/weather/calendar/Spotify/art), 72 h unattended soak test PASSED. DONE."
8. Git commit with milestone message.
**Expected result:** Document with 28 checkpoints (18 JIRA + 10 DoD bullets), 28 PASS lines, 0 FAIL. Git commit milestone message.
**Acceptance criteria / DoD:**
- 18 JIRA MF Items: each linked to its AF-### task, each PASS.
- 10 aggregate DoD bullets (a)-(j): each with evidence link, each PASS.
- Document contains: explicit "MF GATE-PASS = PASS" final verdict line.
- Git commit pushed: message contains "MF GATE-PASS PASS", "V1 PROOF OF CONCEPT COMPLETE".
**Evidence to save:**
- `docs/pm/evidence/AF-151-mf-gatepass.md` (full 28-point checklist with links and PASS/Fail)
- `docs/photos/mf-gatepass/wall-mount-on-site.jpg` (proof frame is actually on wall, in home environment, not bench — aggregate bullet a)
- `docs/photos/mf-gatepass/dashboard-content-seams.jpg` (aggregate bullet b: correct content + seams)
**Safety considerations:**
⚠️ SAFETY-AUDIT FINAL: This task re-reviews Items 6 (barrier), 8 (strain), 9 (PE bonding), 11 (thermal). If ANY of those have a FAIL during re-check → DO NOT PASS milestone. Fix first.
**Known uncertainties:**
None (this task aggregates prior evidence).
**Failure response:**
1 checkpoint FAIL → go to that AF-### task, re-run the specific step that failed, apply fix, get new evidence, mark PASS before signing off.
**Source references:**
- JIRA.md §Mounted Frame Phase (18 items verbatim; this task = completion aggregator)
- AF-001 through AF-150: all prior tasks' evidence feeds into this.
- DECISIONS.md ADR-024 (docs-first; deferred cutting no longer deferred since this is MF complete)
**Labels:** mechanical docs blocked blocked:exp-exp-015 blocked:exp-exp-016 safety-review delivery-review critical-path yes validation GATE-PASS
**Status flags:**
critical-path: yes
Conditional: no

---

### AF-152: Step 1 Freeze exact ESP32 footprint. Use EXP-010 AF-048 physical measurements (W×L in mm, header pitch 2.54mm, pin count, mounting hole positions/diameter, USB-C connector location from edge, BOOT/RESET button positions, antenna position/no-go zone).

**Task ID:** AF-152
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 1 Freeze ESP32 footprint from EXP-010 AF-048 measurements. W×L, header pitch 2.54 mm, pin count, mounting holes Ø+pos, USB-C edge location, BOOT/RESET button pos, antenna no-go zone.
**Description:** First step in Conditional PCB work (19 steps per JIRA). ESP32-S3-N16R8 devkit is the component we are socketing or header-mounting on our custom PCB (the HCT adapter replaces the perfboard; ESP32 devkit plugs into the adapter via headers OR we socket it). The PCB designer needs the EXACT mechanical footprint of the ESP32 board so that: (1) headers on our PCB align EXACTLY with ESP32 pins; (2) mounting holes on PCB line up with ESP32's existing mounting holes (if any); (3) our board edge is 1 mm clear of USB-C protrusion; (4) BOOT/RESET buttons are NOT covered by our PCB standoffs; (5) antenna no-go zone is respected (no copper pour, no metal standoffs, no HCT chip within 5 mm of ESP32 antenna ceramic/PCB trace).
Pull the data from EXP-010 AF-048's physical measurement evidence (W×L dims, pin photo, header count). If AF-048 data is incomplete, RE-MEASURE now with calipers. Compile a KiCad footprint file OR a detailed mechanical drawing with:
- **Board outline W_mm × L_mm:** the ESP32 PCB extents (excluding USB-C connector protrusion).
- **Header positions:** 2 headers (typically 2×20 pins, 40 pins total, on DevKitC-1 style boards). Pitch: 2.54 mm standard. Header-to-header spacing: measure distance between header centerlines (typically 22.86 mm = 0.9" for standard DevKitC-1 footprint). Pin 1 marker position on each header (square pad, silk dot, or notch).
- **Mounting holes on ESP32 PCB:** Count them (2 or 4), Ø, (X,Y) from bottom-left ESP32 board origin.
- **USB-C protrusion:** How many mm does USB-C metal housing stick out past the board W/L edge?
- **BOOT/RESET button tops:** Height in mm above PCB surface, (X,Y) center positions from origin.
- **Antenna no-go zone polygon:** Typically a rectangle in one corner of the ESP32 board (ceramic antenna or inverted-F PCB trace antenna). Draw 5 mm buffer around antenna; mark "NO COPPER, NO METAL STANDOFFS, NO COMPONENTS" zone.

Document footprint. Save as `pcb/footprints/esp32-s3-n16r8-devkitc.pretty/` KiCad footprint OR a PDF mechanical drawing. Future PCB layout (AF-160) uses this footprint.
**Why this task exists:** 90% of PCB-to-component mechanical fit failures are "header pitch was 2.50 mm instead of 2.54 mm, didn't fit" or "USB-C connector banged against the PCB edge because the protrusion was 1 mm longer than we measured." Freezing the footprint before any schematic/layout prevents scrapping $30 worth of manufactured boards.
**Prerequisites:** CONDITION CHECK first: this epic is SKIP = ADR-016 = WF4 (single controller). IF WF4 → mark this task Conditional=yes, blocked, Skip condition active, no work. WORK only if ADR-016 = MULTI-ESP32 (3× controller wins). Also EXP-010 AF-048 done (or re-measure now).
**Blocked by:** ADR-016 (Conditional blocker; Cond-X epic blocked:adr-016)
**Required hardware:** ESP32-S3-N16R8 devkit ×1 (in hand from BOM H-02 ordered qty=1; need physical for re-measure).
**Required tools:** Digital calipers (±0.05 mm ideal for 2.54 mm pitch verification). 300 mm ruler.
**Required software:** Optional: KiCad Footprint Editor for footprint file; else Inkscape/FreeCAD for mechanical drawing.
**Exact execution steps:**
1. **PRE-WORK CONDITION CHECK (MANDATORY first):** Open DECISIONS.md ADR-016. Read Decision line.
   - If "ACCEPTED: HD-WF4 (single WF4)" → SKIP this entire epic. Mark task: Conditional=yes (unchanged), Labels add extra `blocked`, Skip condition text present, Reclassification rule text present. Write 1 line evidence: "SKIP — ADR-016 = WF4 architecture; no Cond-X PCB needed". STOP, no footprint work.
   - If "ACCEPTED: Multi-ESP32 (3× controller)" → PROCEED. APPLY reclassification rule VERBATIM (see Status flags section). After reclassification, this task Conditional=no, critical-path=yes, remove Labels blocked blocked:adr-016, keep hardware pcb labels. CONTINUE steps below.
2. **Open AF-048 EXP-010 evidence file.** Copy all measurements: W_mm, L_mm, pin count, mounting hole count, etc.
3. **Verify/calibrate with calipers (if data uncertain or missing any field):**
   a. W/L × 2 board rectangle edges.
   b. Header pins: count pins per header. Pitch: measure center-to-center distance between pin 1 and pin 11 on a 20-pin header (should be 10 × 2.54 mm = 25.40 mm). Measure, record actual pitch = (measured distance)/10. Pitch MUST be 2.54 mm (0.1") for perfboard compatibility — if non-standard, document it.
   c. Header spacing (distance between the two 20-pin headers, center-to-center, along the width axis). Typical DevKitC-1: 22.86 mm = 0.9".
   d. Mounting holes: find each through-hole on ESP32 PCB corners. Measure each hole ID with calipers. Measure (X,Y) from bottom-left board corner.
   e. USB-C protrusion past board edge: board edge to metal USB shell end = mm.
   f. BOOT/RESET buttons: (X,Y) center from bottom-left, height above PCB top surface.
   g. Antenna region: identify the corner with the antenna (no copper fill in KiCad board view, or ceramic sticker). Measure zone rectangle W×H, mark 5 mm buffer.
4. **Compile footprint:**
   a. KiCad footprint: add to project library, 2×20 pin headers with pitch, correct spacing, board outline on Silk layer, mounting holes, USB protrusion keep-out, button keep-out, antenna no-copper zone.
   b. Drawing: mechanical PDF (1:1 scale if possible) with (X,Y) coordinates of every feature.
5. **VERIFY footprint vs physical ESP32 board (sanity check):** Print the footprint 1:1 scale on paper. Place ESP32 board on top of paper. Align headers with paper pin marks. Perfect alignment = pins center directly over paper marks. Misalignment >0.2 mm → correct drawing.
**Expected result:** **PRE-DECISION (ADR-016 PENDING or WF4):** Reclassification rule present, Skip condition present, no footprint work yet. **POST-DECISION MULTI-ESP32 WIN:** ESP32 footprint compiled (KiCad or PDF), all 7 categories (board dims, headers, mounting holes, USB protrusion, buttons, antenna zone) documented. 1:1 paper-print alignment check PASS (pins <0.2 mm error).
**Acceptance criteria / DoD:**
**PRE/ADR-016 = WF4:**
- Skip condition text present. Reclassification rule text copied VERBATIM. Labels: blocked + blocked:adr-016. 0 PCB/footprint work. Evidence: 1-line SKIP note.
**POST/ADR-016 = MULTI-ESP32:**
- Reclassification executed (Conditional=no, critical-path=yes, blocked labels removed per rule).
- Footprint file saved (KiCad .kicad_mod in project lib or PDF).
- Footprint fields: W×L dims, 2 headers pin count + 2.54 mm pitch verified + spacing, mounting holes Ø+pos, USB protrusion mm, button X/Y+H, antenna zone rect.
- 1:1 print alignment check: ESP32 board pins match paper marks within <0.2 mm. Photo evidence.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-152-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `pcb/footprints/esp32s3-devkitc.pretty/ESP32-S3-DevKitC-1.kicad_mod` (KiCad footprint) OR `pcb/mech/esp32-footprint-drawing.pdf`
- `docs/pm/evidence/AF-152-esp32-footprint.md` (7-field dimensions summary; 1:1 print pass result)
- `docs/photos/condx-pcb/footprint-1to1-print-alignment.jpg` (photo of ESP32 sitting on paper print, pins aligned to marks)
**Safety considerations:**
N/A — footprint measurement/drawing.
**Known uncertainties:**
PRE: unknown winner until ADR-016. Resolved by step 1 check. POST: exact mounting hole count varies by manufacturer (some DevKitC clones have 2 holes, some 4). Measure the actual ESP32 you have, not a reference photo.
**Failure response:**
**PRE/WRONG WINNER:** If ADR-016 = WF4, properly mark SKIP and stop — do NOT waste time on PCB. **POST:** Header pitch off by 0.1 mm → adjust footprint, reprint, re-align. If pitch is truly non-2.54 mm (unlikely), we may not socket and instead solder ESP32 directly (undesired; return/exchange ESP32 board for proper 2.54 mm clone).
**Source references:**
- JIRA.md §Conditional PCB Work Step 1 "Freeze exact ESP32 footprint"
- EXP-010 / AF-048 (physical measurements source data)
- PIN_LEVEL_APPENDIX.md §1-§4 (provisional GPIO, to be validated Step 2)
- DECISIONS.md ADR-014 (PCB ONLY after perfboard validated = prerequisite for epic)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-153: Step 2 Freeze validated GPIO mapping — use the mapping that ACTUALLY PASSED EXP-011/EXP-012 with perfboard build (AF-067/AF-089), NOT the provisional mapping from PIN_LEVEL_APPENDIX §3/§4 if any differences were found. Document any changes from provisional → validated in a small mapping-diff table.

**Task ID:** AF-153
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 2 Freeze validated GPIO mapping from EXP-011/012 perfboard WORKING build (AF-067/AF-089). NOT provisional PIN_LEVEL_APPENDIX §3/§4. Provisional→Validated diff table if any changes.
**Description:** The perfboard build (12-stage AF-054..AF-065) was wired with a GPIO map, then tested in EXP-011/012. Some GPIOs that "looked fine on paper" (PIN_LEVEL_APPENDIX provisional) may have been swapped during perfboard build because they conflicted with flash/PSRAM strapping pins, or a GPIO had an unexpected pull-up on boot causing a garble, or during AF-067 wiring we used a different pin than planned and it worked. The RULE: the PCB uses the VALIDATED GPIO MAP — the EXACT wiring that made EXP-012 1-row stability pass (1 hr at 85 Hz or whatever metrics). We do NOT re-copy PIN_LEVEL_APPENDIX on faith. "Works in practice" beats "works in theory" every time for a PCB.
Diff the two: (a) Open PIN_LEVEL_APPENDIX §3 (U1) and §4 (U2) tables (provisional columns). (b) Open EXP-011/012/AF-067/AF-089 wiring notes — what ACTUALLY got wired on perfboard for each signal. For each HUB75 signal (R1, G1, B1, R2, G2, B2, A, B, C, D, CLK, LAT, OE) list: Signal, Provisional GPIO (from appendix), Validated GPIO (from working perfboard), DIFF (SAME or SWAPPED: GPIO_X→GPIO_Y). If all same → diff table has column "All SAME. No changes." If any swapped → note the reason if known (e.g., "GPIO 12 was MTDI strapping pin, caused boot into download mode; swapped to GPIO 5 on perfboard; stable since"). Freeze the validated GPIO map as CSV file `pcb/gpio-map-validated.csv` with columns: Signal Name, ESP32 GPIO Number, HCT245 IC Pin (U1 pin xx / U2 pin xx), HUB75 Connector Pin (P1..P16 per PIN_LEVEL_APPENDIX §5), Direction (ESP32 → HCT input, HCT output → HUB75).
**Why this task exists:** "PCB copied the provisional GPIO map instead of the working perfboard map" = $30 PCB revision + 2 week lead time waste. PCB = expensive, 2 week turnaround, no hot-air rework station required. Validated GPIO map = pin-perfect identical to perfboard that passed 1 hr stability. No surprises.
**Prerequisites:** **SKIP if ADR-016 = WF4.** PROCEED only if multi-ESP32. ACCESS to: PIN_LEVEL_APPENDIX §3/§4 provisional tables; AF-067 12-stage build wiring records; AF-089/AF-090 EXP-012 validation working-mapping evidence.
**Blocked by:** ADR-016 (Conditional blocker). EXP-012 perfboard validation done (AF-089/AF-090) for validated map source. AF-152 (footprint frozen, so we know which physical header pin corresponds to ESP32 GPIO).
**Required hardware:** Working perfboard + ESP32 (the exact one from EXP-012 pass; use for cross-check if needed).
**Required tools:** None. Software diff.
**Required software:** Spreadsheet or Markdown table editor.
**Exact execution steps:**
1. **CONDITION CHECK same as AF-152 Step 1:** If ADR-016 = WF4 → SKIP, mark per reclassification rule, 0 work. If multi-ESP32 → reclassify per rule (Conditional=no, critical-path=yes, unblock labels). Proceed.
2. **Collect 2 inputs:**
   a. PROVISIONAL: Copy PIN_LEVEL_APPENDIX §3 and §4 tables into a temp working table. Per signal: R1, G1, B1 (top-half row pixels), R2, G2, B2 (bottom-half), A/B/C/D (row address 4-bit for 1/32 scan), CLK (clock), LAT (latch/strobe), OE (output enable). For each signal note provisional GPIO#.
   b. VALIDATED: Open AF-067 wiring notes (12-stage build — at stage SOLDER step you recorded which ESP32 pin went to which HCT input). Also open AF-089 (EXP-012 final working setup). Cross-reference: the setup that passed 1-hr stability used these GPIOs. For each signal note validated GPIO#.
3. **Build diff table (Markdown):**
   | Signal | Provisional GPIO | Validated GPIO (perf working) | Diff | Reason if diff |
   |--------|------------------|--------------------------------|------|----------------|
   | R1     | 4                | 4                              | SAME |                |
   | CLK    | 12               | 5                              | SWAP 12→5 | GPIO 12 = MTDI strapping pin, pulled high boot → ESP32 into download mode; swapped during AF-067 per note |
   ... (15 signals total: 6 RGB + 4 ABCD + CLK/LAT/OE + any GND not needed)
   Total rows = 14-15 signals.
4. **If diff count > 0:** Double-check "Reason if diff" against AF-067 build journal. Are the reasons consistent with EXP-010 strapping pin discoveries? (GPIO 0, 2, 5, 12, 15 = common strapping pins that must be correct logic level on boot; any of these used as outputs → ensure they don't cause boot issues.)
5. **Save validated GPIO map CSV:** `pcb/gpio-map-validated.csv` columns = [Signal, ESP32_GPIO, HCT_Device (U1/U2), HCT_Pin_Number, HUB75_Pin_Number, Direction_ESP32_to_HCT_or_HCT_to_HUB75]. (Rows: each signal row.)
6. **Confirm U1/U2 channel allocations match PIN_LEVEL_APPENDIX:** U1 drives which signals? U2 drives which? VALIDATED should match APPENDIX §3/§4 IC assignments (if IC allocation was also swapped during perf build → document). IC allocation change = rare but possible; our perf board passed, so we copy it exactly.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: 14-15 row diff table. 0 diffs or N diffs each with a reason. Validated CSV saved, 14 rows, all fields filled.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP noted per rule, no diff work.
**POST/ESP32:**
- Reclassification applied (Conditional=no, critical-path=yes, unblock).
- Diff table: 14+ rows, each signal provisional vs validated. Diff count documented. Every diff has a reason.
- Validated CSV `pcb/gpio-map-validated.csv` saved with 6 columns, 14 rows.
- U1/U2 allocation matches working perfboard (verified against AF-067 build records). CSV confirms U1 pins / U2 pins match working.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-153-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `pcb/gpio-map-validated.csv` (committed)
- `docs/pm/evidence/AF-153-gpio-diff.md` (full 14-row diff table; diff count + per-diff reason; U1/U2 allocation confirmation line)
**Safety considerations:**
N/A — GPIO mapping. (GPIO wrong = non-functional board, not safety hazard.)
**Known uncertainties:**
If AF-067 build journal wasn't detailed (we forgot to record a swap mid-build), we may need to REVERSE-ENGINEER the working perfboard mapping: desolder ESP32 header, use multimeter continuity from each ESP32 GPIO pin through HCT245 to HUB75 pin. Takes 30 min but gives ground truth.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Diff count >5 signals (large diff from appendix) → likely we misread the validated source. Cross-reference with AF-090 working test again and if needed do multimeter continuity reverse-engineering on perfboard. CSV wrong format → fix columns.
**Source references:**
- JIRA.md §Conditional PCB Work Step 2 "Freeze validated GPIO map (working perf, NOT provisional appendix)"
- PIN_LEVEL_APPENDIX.md §3 (U1 allocation provisional), §4 (U2 allocation provisional), §5 (HUB75 pinout)
- AF-067 (12-stage perfboard build — wiring records; working map)
- AF-089/AF-090 (EXP-012 2-panel stability pass → validates the working mapping)
- EXP-010 AF-048/AF-049 (GPIO availability / strapping pin discoveries — reason-for-diff source)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs firmware
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-154: Step 3 KiCad schematic: ESP32-S3 socket or 2×20 header footprints. Power/GND rails symbols. 3-pin or 2-pin 5 V power entry header/screw terminal.

**Task ID:** AF-154
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 3 KiCad schematic start. ESP32-S3 socket or 2×20 headers footprint. Power/GND rails symbols. 5 V power entry: 3-pin or 2-pin header/screw terminal.
**Description:** Create the KiCad schematic sheet (`.kicad_sch`) for the Cond-X PCB — the ESP32 HCT245 adapter that replaces the perfboard. Step 3 = foundation components only (before HCT ICs in Step 4). Schematic content:
- **ESP32-S3 devkit interface:** We have two mounting options (choose ONE, document decision):
  (a) **2×20 female header sockets on our PCB (PREFERRED):** We solder two 2×20 pin 2.54 mm pitch FEMALE sockets to our custom PCB. The ESP32 DevKitC male headers PLUG INTO these sockets (piggyback style). Pros: ESP32 can be removed/reflashed/programmed easily; no solder rework needed to swap ESP32. Cons: Z-height = ESP32 + socket stack = ~18 mm (fits in frame). Footprint for socket = 2 rows × 20 pins, pitch 2.54 mm, row spacing same as ESP32 (AF-152 measurement).
  (b) **2×20 male headers (ESP32 soldered DOWN to our PCB):** ESP32 headers solder directly into our PCB (same as perfboard). Lower Z-height (~10 mm). Cons: ESP32 not swappable without desolder 40 pins; reflash requires USB-C accessible from combo stack.
  Option (a) preferred. Use female socket footprint.
- **Pin numbering on sockets:** ESP32 pin 1 (GPIO 0 or 3V3 depending on board) maps to Socket Pin 1. Confirm with AF-152 footprint pin-1 marker (silk dot). On schematic: create a hierarchical symbol block "ESP32-S3-DEVKIT" with 40 pins named: GPIO0, GPIO1, ... GPIO48 (as actually broken out), plus 3V3, 5V, GND, EN. We will later wire only the pins used per validated GPIO map (AF-153). The other pins are No-Connect (NC marker on schematic).
- **Power rails:** Create global net labels on schematic: `+5V` (5 volt power input from PSU), `GND` (ground — 0 V reference). Add power symbols: arrow for +5V pointing INTO net, ground symbol for GND.
- **5 V power entry connector (2 or 3 pins):** How does 5 V power come onto the PCB from PSU harness? Choose:
  (a) **Screw terminal 2-pin 5.08 mm pitch (PREFERRED for high current):** Phoenix-style screw terminal block, 2 positions, pitch 5.08 mm, current rating ≥ 10 A per pin (we have 2 panels per row = max 40 W / 5 V = 8 A, 10 A-rated gives 25% margin).
  (b) **2-pin XT30 male/female connector (good for high current):** XT30 is standard RC LiPo connector, 15 A rating, polarization prevents reverse plug (SAFETY WIN).
  (c) **2-pin 0.2" header (NOT RECOMMENDED for 8 A):** Dupont-style only good for 2-3 A continuous. Reject for 5 V high current.
  Choice = (a) 5.08 mm 2-pin screw terminal OR (b) XT30. Document choice. Schematic symbol: Terminal 2 or Header 2, pins named +5V and GND. Wire pin 1 → +5V net, pin 2 → GND net.

Save KiCad schematic (start of file; continue adding in Steps 4/5/6). Run ERC early (Step 8 later is full ERC; now just check for obvious errors like unconnected power pins on ESP32 symbol: 3V3 pin on ESP32 symbol connects to... wait: ESP32 is powered ITS OWN WAY via its USB-C or its 5V pin. If we plug ESP32 into sockets, do we power ESP32 from our PCB's 5V through the socket? YES, we should (shared 5 V rail). So schematic: +5V net → ESP32 5V pin, GND → ESP32 GND pins. That way ESP32 gets power from PSU along with the panel power (no extra USB cable needed once flashed). USB-C on ESP32 is for reflash/debug only after that.
**Why this task exists:** Formal schematic foundation. Before HCT wiring (the complex part), locking down the ESP32 socket footprint and 5 V entry ensures we don't power the thing wrong. Reverse 5 V = instant smoke for ESP32 and HCT; polarized XT30 or correct screw terminal labeling prevents that.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: AF-152 (ESP32 footprint, pin spacing, header count), AF-153 (validated GPIO map — so we know which pins to wire in future steps). KiCad installed (8.0+ recommended).
**Blocked by:** ADR-016, AF-152, AF-153
**Required hardware:** ESP32 devkit + footprint, power connector sample if choosing XT30 (confirm pinout with multimeter continuity).
**Required tools:** None (KiCad schematic entry only).
**Required software:** KiCad 7 or 8 (Eeschema).
**Exact execution steps:**
1. **CONDITION CHECK:** If ADR-016 = WF4 → SKIP. If multi-ESP32 → reclassify per rule. Proceed.
2. **Create KiCad project:** `pcb/condx-hct-adapter.kicad_pro`. New project.
3. **Schematic sheet `condx-hct-adapter.kicad_sch`:**
   a. **ESP32 socket choice + symbol:** Create or import ESP32 DevKit symbol (40 pins). Place on schematic. Add socket footprint from AF-152 library (or standard 2×20 socket female footprint). Document choice: "Socket option = female 2×20 header; ESP32 piggybacks".
   b. **Power rails:** Place `PWR_FLAG +5V` and `PWR_FLAG GND` symbols (KiCad ERC needs these to know that nets are power-driven). Place global label `+5V` on the +5V power symbol net; `GND` on GND net.
   c. **5 V entry connector choice:** Decide (a) screw terminal 5.08 mm 2P or (b) XT30. Document choice in schematic comment block. Place connector symbol. Wire pin1 → +5V, pin2 → GND. Polarization mark (pin1 = +5V indicated on silk later).
   d. **POWER ESP32 THROUGH SOCKET:** On ESP32 symbol, find the "5V / VIN" pin that supplies the devkit onboard regulator. Wire schematic: our PCB +5V net → ESP32 5V pin. Find ALL GND pins on ESP32 symbol → wire each to GND net.
   e. **3V3 rail generation note:** Do we need to generate 3V3 on our PCB for HCT inputs? HCT245 inputs are 5 V tolerant (TTL-level compatible; HCT = "High-speed CMOS TTL-compatible inputs"). HCT inputs accept 3.3 V from ESP32 as HIGH correctly (HCT V_IH(min) = 2 V at VCC=5 V). So ESP32 3.3 V outputs → HCT inputs = OK directly. No 3V3 regulator on our PCB needed. We only need +5 V and GND. Comment on schematic: "HCT245 VCC = +5 V. ESP32 3V3 outputs → HCT inputs directly (HCT TTL-compatible). No level shifter needed." This confirms why no extra regulator.
4. Save schematic. Annotate schematics (assign component designators: J1 = ESP32 sockets, J2 = 5 V entry terminal). First pass ERC: red crosses expected (unused GPIO pins no-connect). Mark unused pins with NC flag.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: KiCad project created. Schematic has ESP32 socket symbol with 40 pins, power rails + flags, 5 V entry connector polarized, ESP32 5V/GND pins powered from PCB rail, 3V3-HCT comment. ERC 0 critical errors (warnings allowed).
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified: Conditional=no, critical-path=yes, unblocked.
- KiCad project files exist: .kicad_pro, .kicad_sch.
- ESP32 socket: (a) or (b) documented; footprint assigned; 40-pin symbol placed.
- Power rails: +5V and GND with PWR_FLAGs, global labels.
- 5 V entry: (a) screw 2P 5.08mm OR (b) XT30, documented, polarized, wired to +5V/GND correctly.
- ESP32 powered through socket: 5V pin and all GND pins on ESP32 symbol connected to PCB nets.
- HCT power compatibility comment: no 3V3 regulator needed on PCB.
- ERC run: 0 CRITICAL errors; unconnected GPIO flagged NC only.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-154-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `pcb/condx-hct-adapter.kicad_pro` + `pcb/condx-hct-adapter.kicad_sch` (committed)
- `docs/pm/evidence/AF-154-schematic-foundation.md` (socket choice a/b, 5V entry a/b, ERC result 0 critical)
**Safety considerations:**
⚠️ 5 V entry polarization. If XT30: ensure male/female direction prevents reverse (PCB side = female XT30 with pin1 = +5V, PSU harness side = male XT30 with red = +5V to correct pin). If screw terminal: silk LABEL "+5V" and "GND" next to each pin, plus pin1 triangle marker. Reverse 5 V/GND into HCT245 = IC destroyed instantly (up to 3 ICs dead, 40¢ each, but PCB still functional if you replace them).
**Known uncertainties:**
The exact ESP32 "5V/VIN" pin name varies by DevKitC clone. Some call it VIN, some 5V, some VBUS. Use the AF-152 footprint pin-out (measure which ESP32 physical header pin connects to USB 5 V via diode) — multimeter continuity from USB-C 5 V pin to header pin = confirms the +5 V input pin name. Do NOT guess.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** ERC critical errors: (a) Unconnected power pins on ESP32 symbol → forgot to wire 5V/GND, wire them. (b) "Net not driven" → forgot PWR_FLAG, add it. Footprint missing → go back to AF-152 and add socket footprint to library.
**Source references:**
- JIRA.md §Conditional PCB Work Step 3 "KiCad schematic ESP32 socket + power rails + 5V entry connector"
- AF-152 (ESP32 footprint socket + pin map)
- AF-153 (validated GPIO map — used starting next step for HCT wiring)
- BOM.md §8 Fabrication services (3 vendors researched, used later Step 16 manufacture order)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-155: Step 4 Two SN74HCT245N stages (U1/U2 validated allocations). Schematic matches PIN_LEVEL_APPENDIX §3/§4 channel assignments (use VALIDATED from AF-153, not provisional). DIR pins tied HIGH, /OE pins tied LOW on both ICs per perfboard-proven wiring.

**Task ID:** AF-155
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 4 Two HCT245 stages schematic U1/U2. Channel assignments = VALIDATED from AF-153 (not provisional appendix). DIR pins HIGH, /OE pins LOW on both ICs (perf proven).
**Description:** Add the two SN74HCT245N octal bus transceiver ICs to the schematic. HCT245 wiring was validated on perfboard (the 12-stage build procedure has explicit DIR/OE tie instructions from AF-055/AF-059). Copy it EXACTLY onto the schematic. Do NOT "improve" the DIR or /OE wiring — if perf works, PCB copies verbatim.
HCT245 pin basics (DIP-20, SOIC-20 pin 1 = notch marker):
- Pins 2-9 = A-side (A1..A8): ESP32 GPIO side (TTL 3.3 V inputs OK for HCT "TTL-compatible inputs"). Input direction for our use case: ESP32 → HCT A → HCT B → HUB75. Data flows A → B.
- Pins 18-11 = B-side (B1..B8): HUB75 side, 5 V swing outputs (B1/B2/../B8 ordered pin 18 B1 down to pin 11 B8).
- Pin 1 = DIR (direction): HIGH = A→B (correct for our use). TIE DIRECTLY TO +5 V.
- Pin 19 = /OE (output enable active LOW): LOW = outputs enabled. TIE DIRECTLY TO GND. We never tri-state outputs (panel always driven). No need for GPIO control of /OE (ESP32 can control brightness via panel clock blanking timing instead; perfboard did same).
- Pin 20 = VCC (+5 V). Pin 10 = GND.
Place U1 and U2 on schematic (two separate HCT245 symbols). Each DIP-20 or SOIC-20 footprint assigned (SN74HCT245N DIP-20 = through-hole; easier for prototype hand-solder — footprint = DIP-20 300 mil width 0.1" pitch; SOIC-20 if later doing SMT assembly). Assign validated signals per AF-153 CSV to U1 channels and U2 channels:
**Wiring on schematic:**
- Each HCT input An pin (n=1..8 on IC) → one ESP32 GPIO from the validated CSV, Signal in column, listed as U1 or U2 in CSV HCT_Device column.
- Each HCT output Bn pin → corresponding HUB75 connector pin (from CSV HUB75_Pin_Number). In schematic, HUB75 connector symbol we will add Step 6; for now, use net labels like `HUB75_R1` at each Bn output.
- U1 DIR (pin 1) → +5V. U1 /OE (pin 19) → GND.
- U2 DIR (pin 1) → +5V. U2 /OE (pin 19) → GND.
- U1 VCC (pin 20) → +5V. U1 GND (pin 10) → GND. Same for U2 VCC/GND.
Save schematic. Annotate designators: U1 = first HCT245, U2 = second (order from CSV). Check perfboard notes: DIR/OE ties confirmed match AF-065 (HCT wiring continuity check stage).
**Why this task exists:** HCT245 channel miswiring = signals routed wrong rows or wrong colors = "PCB panel is blue where it should be red". DIR pin accidentally tied LOW = B→A data flow = HUB75 signals trying to drive ESP32 GPIO outputs (contention, risk of permanent GPIO damage). Copying the validated perf map 1:1 eliminates both failure modes.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: AF-153 (validated GPIO CSV with U1/U2 column, pin numbers). KiCad schematic from AF-154 open.
**Blocked by:** ADR-016, AF-153, AF-154
**Required hardware:** Perfboard HCT adapter nearby to double-check DIR/OE wiring (if in doubt, multimeter continuity DIR pin 1 to +5 V on perf → should beep).
**Required tools:** None. KiCad Eeschema.
**Required software:** KiCad Eeschema.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP (mark task per rule). Multi-ESP32 → reclassify per VERBATIM rule, proceed.
2. Open KiCad schematic condx-hct-adapter.kicad_sch from AF-154.
3. **Add U1 HCT245 symbol:** Use generic 74xx245 symbol from KiCad library (SN74HCT245 or 74HC245). Assign footprint: DIP-20 300mil (through-hole). Reference designator U1.
4. **U1 wiring using AF-153 CSV rows where HCT_Device = "U1":**
   For each row (8 channels): ESP32 GPIO → wire to U1 An pin where n = CSV HCT_Pin channel. U1 Bn pin → net label HUB75_SignalName (Signal column).
5. **U1 power/control pins:** U1 pin1 DIR → +5V net. U1 pin19 /OE → GND net. U1 pin20 VCC → +5V. U1 pin10 GND → GND.
6. **Add U2 HCT245 symbol:** Same library, footprint DIP-20. Designator U2.
7. **U2 wiring using CSV rows where HCT_Device = "U2":** Same procedure as U1. 8 channels.
8. **U2 power/control pins:** DIR→+5V, /OE→GND, VCC+20→+5V, GND-10→GND. Exactly as U1.
9. **VERIFY perf DIR/OE ties (sanity):** Hold perfboard in hand. Multimeter continuity: HCT U1 pin 1 (DIR notch end) ↔ 5V rail? Beep = YES. HCT U1 pin 19 (opposite side from pin 1 near pin 20 VCC) ↔ GND? Beep = YES. Same for U2. Schematic matches perfboard. If any mismatch = change schematic to match perf (per perf's ACTUAL wiring, not what appendix said).
10. Save schematic. Annotate. ERC spot check: unconnected pins → add NC flag to spare unused ESP32 GPIO. No critical errors.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Schematic now has 2 HCT245 ICs (U1/U2). 8 channels per IC wired per validated CSV, 16 signals total. DIR→+5V both, /OE→GND both. Perfboard continuity sanity confirms DIR/OE ties match.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified per rule.
- 2 HCT245 on schematic: U1 footprint DIP-20, U2 same. 16 channels wired.
- Per-channel: CSV row (Signal, GPIO, U1/U2, HCT pin, HUB75 pin) → schematic wire matches (verify 3 random channels manually, record pass).
- DIR pins: U1 pin1=+5V, U2 pin1=+5V. Correct.
- /OE pins: U1 pin19=GND, U2 pin19=GND. Correct.
- Perfboard sanity: DIR/OE 2×2 = 4 continuity measurements all beep correctly. Documented.
- ERC 0 critical errors.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-155-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- Updated schematic file committed (pcb/condx-hct-adapter.kicad_sch).
- `docs/pm/evidence/AF-155-hct-schematic.md` (3-channel spot-check verification pass list; perfboard DIR/OE 4-continuity-beep confirmation line)
**Safety considerations:**
⚠️ GPIO bus contention. If DIR = LOW accidentally, B outputs of HCT are connected to HUB75 panel inputs. But panel inputs are inputs (high impedance). The real risk is if we DIR = LOW during a transient test where HUB75 cable is accidentally a loopback (unlikely). The DIR→+5V hard-tie is safe. Verify solder joint on PCB: DIR pin is soldered to +5V (cold solder joint = floating DIR pin = undefined direction = intermittent data corruption). Hard tie on PCB is as safe as it gets.
**Known uncertainties:**
KiCad 74xx245 symbol pin numbering: pin 1 = DIR, pin 19 = /OE. Verify symbol against Texas Instruments SN74HCT245N datasheet PDF (pin 1 DIR, pin 10 GND, pin 20 VCC, pins 2-9 A1-A8 ascending, pins 18-11 B1-B8 descending). Some symbols have B1 at pin 11, not pin 18. Cross-check datasheet. If wrong, swap B net labels to match actual physical B-pin order (18→B1, 17→B2, ..., 11→B8).
**Failure response:**
**PRE/WF4:** SKIP. **POST:** ERC "outputs connected together" error → 2 signals wired to same HCT pin by mistake. Fix: check CSV HCT_Pin uniqueness. DIR/OE wrong (conflicting with perf sanity) → move wire on schematic. Wrong pin B-order (symbol bug): swap net labels so B1..B8 match datasheet physical pins 18→B1 down to 11→B8.
**Source references:**
- JIRA.md §Conditional PCB Work Step 4 "Two SN74HCT245N stages U1/U2 validated channels. DIR HIGH, /OE LOW."
- PIN_LEVEL_APPENDIX §3/§4 (U1/U2 channel tables — reference only; USE AF-153 VALIDATED CSV instead)
- AF-153 CSV (actual ground truth for wiring)
- AF-054..AF-065 (12-stage perf build — DIR/OE tie instructions Step 3/4/7)
- Texas Instruments SN74HCT245 datasheet (pinout verification)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs firmware
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-156: Step 5 Decoupling. 2× 100nF ceramic (0603 or 0805 SMD or through-hole) BESIDE each HCT245 — U1 decoupling across U1 pin20↔pin10; U2 decoupling across U2 pin20↔pin10. Optional 1000µF 10V+ aluminum electrolytic footprint across 5V/GND input (through-hole, polarized footprint with polarity marking on silkscreen).

**Task ID:** AF-156
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 5 Decoupling caps schematic + footprint placement rules. 2× 100 nF ceramic (TH or SMD 0603/0805): U1 decap between U1 pin20 (VCC) and pin10 (GND); U2 decap between U2 pins 20↔10. Optional 1000 µF 10 V+ aluminum electrolytic TH polarized across 5V/GND at input, silkscreen polarity.
**Description:** Add decoupling capacitors to the schematic + establish PCB LAYOUT RULES for their placement (placement rules are executed in Step 9 layout; schematic captures intent now). Decoupling prevents 5 V rail dips during HCT output simultaneous switching (8 outputs switch at once = 8×20 mA = 160 mA transient; 100 nF local reservoir supplies the transient, preventing rail glitches that cause flip-flop misclocking = garbled rows). Without decoupling, perfboard might pass (wiring has stray inductance and capacitance acting as accidental filters), but PCB with controlled-impedance traces could fail. Standard practice.
Decoupling count = 2 (minimum 1 per IC):
- **C1: U1 decoupling cap.** Value 100 nF (0.1 µF) ceramic, X7R dielectric, rated voltage ≥10 V (25 V X7R 0805 is fine). Schematic: place next to U1 symbol on schematic. One lead → +5 V net (connect near U1 pin20 VCC). Other lead → GND net (near U1 pin10 GND). Designator C1. Footprint choice: (a) Through-hole: 2.54 mm pitch radial ceramic (common yellow/brown "multilayer ceramic" through-hole). OR (b) SMD 0603 or 0805 (smaller, but hand-solder requires fine tip). Schematic is footprint-agnostic; assign footprint now based on assembly plan (prototype through-hole preferred for novice hand-solder).
- **C2: U2 decoupling cap.** Identical value 100 nF, same specs. Next to U2 symbol. +5V/GND. Designator C2. Same footprint.
- **(OPTIONAL but recommended) C3: Bulk input reservoir 1000 µF aluminum electrolytic.** Value 1000 µF, rated 10 V minimum (16 V safer, standard size). Through-hole polarized: anode (+) lead longer, marked with stripe on can body = CATHODE (-). Schematic: polarized capacitor symbol, + lead → +5V near the 5 V entry connector (J2 in AF-154), - lead → GND. Designator C3. Footprint: TH radial polarized (2 pin, 5.08 mm pitch, silkscreen will show + mark near anode pin, or half-circle mark on cathode side). Purpose: supplies larger current transients (entire row LEDs turn ON simultaneously) from the 5V input before the harness delivers it; prevents rail dip >0.2 V at the panel. Recommended even if perf board survived without (PCB cleaner power planes).

Silk polarity rule for C3: footprint silk will show "+" symbol at the anode pin on the copper silkscreen top layer. DO NOT REVERSE electrolytics (reverse voltage > 1 V on aluminum electrolytic = venting / bulging / eventual smoke / PCB damage after prolonged time).
Save schematic. Annotate C1, C2, C3. ERC.
**Why this task exists:** HCT245 simultaneous-switch noise. Standard 1 cap per IC decoupling is mandatory for all digital PCBs. Novice mistake: "perf worked, why add caps?" Perf has wires with 5-10 nH inductance acting as low-pass filters; PCB traces have 1-2 nH. The digital switching noise spectrum shifts up on PCB; caps provide local charge at the high frequencies where trace inductance blocks current flow.
**Prerequisites:** PRE/WF4 → SKIP. POST only: schematic from AF-155 (U1/U2 present).
**Blocked by:** ADR-016, AF-155
**Required hardware:** Optional: hold perfboard near ear/scope if you had noise issues (you didn't because it passed; this is proactive design).
**Required tools:** None. Schematic entry only.
**Required software:** KiCad Eeschema.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per rule. Proceed.
2. Open schematic from AF-155.
3. **Add C1:** Symbol = "Capacitor" (generic non-polarized). Value = "100nF" or "0.1uF". Designator = C1. Place on schematic near U1. Wire one terminal → +5V net (near U1 pin20 symbol VCC pin). Wire other terminal → GND net (near U1 pin10 GND). Assign footprint: C_0805_SMD (or through-hole C_Radial_2.54mm Pitch TH).
4. **Add C2:** Same symbol, same value. Designator = C2. Place near U2. Wire same: one to +5V (U2 pin20), other to GND (U2 pin10). Same footprint as C1.
5. **Add C3 (optional RECOMMENDED):** Symbol = "Capacitor Polarized" or "Electrolytic Capacitor" (has plus sign). Value = "1000uF". Voltage rating comment "≥10V, use 16V". Designator = C3. Place near 5 V entry connector (J2 from AF-154). Wire (+) → +5V (connector +5V pin). Wire (-) → GND (connector GND pin). Assign footprint: CP_Radial_D10mm_P5.08mm (10 mm can diameter, 5.08 mm pitch TH). Add schematic text note next to C3: "POLARIZED: anode = +5V; cathode stripe = GND. SILKSCREEN + MARK REQUIRED."
6. **Decoupling placement rule note on schematic text block:** "PCB LAYOUT (Step 9) RULE: C1 MUST be placed WITHIN 5 mm of U1 pin20 (VCC). C2 WITHIN 5 mm of U2 pin20. Short traces to vias → VCC/GND. Do NOT place caps on far side of board." (Rule enforced in Step 9 layout; record here for reference.)
7. Save schematic. Annotate. ERC: 0 critical.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Schematic has C1/C2 100 nF per HCT. Optional C3 1000 µF polarized at input. Placement-within-5-mm rule documented for Step 9.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- C1 100 nF: present, +5V/GND wired to U1 VCC/GND pins area, footprint assigned.
- C2 100 nF: same for U2.
- C3 1000 µF (if skipped → explicit reason "bulk reservoir deemed unnecessary by designer; accept risk"; OTHERWISE present): polarized symbol, + wired to +5V, - to GND, silkscreen + mark note.
- Placement rule (5 mm proximity) in schematic notes.
- ERC 0 critical errors (polarized cap correct polarity check: ERC flags if swapped).
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-156-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- Updated schematic committed.
- `docs/pm/evidence/AF-156-decoupling.md` (C1/C2 present; C3 present with polarized note; 5 mm placement rule recorded. ERC 0 critical.)
**Safety considerations:**
⚠️ C3 electrolytic polarity. If reversed (anode → GND, cathode → +5V) for hours at 5 V: capacitor heats up, electrolyte pressure builds, pressure relief vent on top of can ruptures (you will see a "pimple" on aluminum top). Worst case: small amount of electrolyte vapor released (slight odor, mild corrosive to copper traces). No fire hazard at 5 V/10 W. Still: silk polarity mark prevents reverse. Double-check footprint: cathode band on cap body → silk "-" or half-circle.
**Known uncertainties:**
Through-hole vs SMD caps: novice hand soldering = TH easier. 0805 SMD requires steady hand and fine tip iron. If executor is comfortable with SMD, use 0805 (smaller PCB, higher fab yield for fine traces). Either acceptable for DoD.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** ERC "reverse biased polarized capacitor" → swapped +/- pins on C3 symbol. Fix: rotate symbol or swap wires. No 100 nF symbol found → use generic "C" symbol, change value.
**Source references:**
- JIRA.md §Conditional PCB Work Step 5 "Decoupling 2×100nF beside each HCT + optional 1000uF alumin electrolytic input footprint"
- SN74HCT245 datasheet app note: "Decoupling capacitors 0.1 µF ceramic per package from VCC to GND" (TI app report SLVA610 — industry standard)
- AF-054..AF-065 (12-stage perf build — may or may not have had decoupling; on PCB we enforce 1 per IC minimum)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-157: Step 6 HUB75 keyed 2×8 connector (panel-orientation verified pinout). Pinout MUST match the direction validated in EXP-011/EXP-012 perfboard — Pin 1 orientation: RED stripe on ribbon cable / keyed notch position corresponds to PIN_LEVEL_APPENDIX §5. Add silkscreen "PANEL IN →" arrow next to HUB75 connector pointing AWAY from board toward panel.

**Task ID:** AF-157
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 6 HUB75 2×8 keyed connector schematic. Pinout matches EXP-011/012 validated perfboard direction (NOT reversed). Pin 1 = red stripe / notch per PIN_LEVEL_APPENDIX §5. Silkscreen arrow "PANEL IN →" pointing AWAY from board toward panel.
**Description:** Add the output connector — the 2×8 (16-pin) keyed IDC or shrouded header that the HUB75 ribbon cable plugs into. This step is CRITICAL because the #1 HCT adapter failure mode on PCB is "Pin 1 reversed" (connector rotated 180° on silk/footprint) — if you plug the HUB75 cable wrong, signals R1 go to G2, ROW A goes to OE, etc. = panel completely garbled. Even a keyed connector can be inserted wrong if you force it. The silkscreen direction arrow + validated orientation against perf board removes all doubt.
Schematic steps:
1. **HUB75 connector symbol on schematic:** Place 2×8 header (16 pins) symbol. Designator J3 (after J1=ESP32 socket, J2=5V entry). Signal pin net assignments from PIN_LEVEL_APPENDIX §5 (16 pins total: A, B, C, D, R1, G1, B1, R2, G2, B2, CLK/LAT/OE, 2×GND, 2 spare usually GND or no-connect). Pin order on 2×8 shrouded header: standard is "pin 1 = top-left of the keyed notch side, pins 1-2 row 1 odd, pins 2-16 even row 2 zig-zag". Use VALIDATED direction from EXP-011 perf board: physically take the working HUB75 ribbon cable, look at PIN 1 end (RED stripe on flat ribbon, or embossed "1" or triangle) → which end of perf board did you plug it into when EXP-012 passed? → that end = "PANEL" side. Our PCB connector PANEL side = the side where the cable goes OUT from our board to the PANEL HUB75 IN.
2. **Wire nets from HCT outputs (Step 4 schematic net labels HUB75_R1 etc.) to the CORRECT HUB75 connector pins (using AF-153 CSV HUB75_Pin_Number column).** Pin 1 of J3 = whatever signal PIN_LEVEL_APPENDIX §5 Pin 1 says (usually ROW A, double-check).
3. **GND pins on connector:** At minimum the 2 GND pins in §5 → wire to GND net. If appendix marks "spare" pins (pin 15, 16), leave NC or wire to GND (shielding). Wire GND to GND.

Physical orientation rule: on the PCB LAYOUT (Step 9), we will place the HUB75 shrouded header with its keyed notch facing the PANEL DIRECTION (away from the ESP32/HCT chips, toward the board edge that faces the panels). The SILKSCREEN next to the header will have a LARGE ARROW "PANEL IN →" pointing AWAY from the board center, toward the edge, to the PANEL. The arrow MUST be visible when the ESP32/HCT side of the board is facing the assembler (so they know which way the header notch faces: cable goes to panels from the arrow side).
Save schematic. Annotate J3. ERC.
**Why this task exists:** Pin 1 reverse = non-functional board (because HUB75 signals are permuted). Arrow silkscreen = 0 cost, eliminates 50% of assembly direction mistakes. Validated orientation = copies perfboard proven direction (no guessing).
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: PIN_LEVEL_APPENDIX §5 (HUB75 pin layout) + AF-153 CSV (signals to pins). Working perfboard for orientation cross-check.
**Blocked by:** ADR-016, AF-153, PIN_LEVEL_APPENDIX §5
**Required hardware:** Working perfboard + HUB75 cable (the one used in EXP-012 pass) for physical orientation check of RED STRIPE end.
**Required tools:** None. Schematic.
**Required software:** KiCad Eeschema.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. Open schematic from AF-156.
3. **Physical orientation confirmation (HOLD perfboard + HUB75 ribbon cable in hand):**
   a. Find the RED STRIPE or TRIANGLE/1 MARK on one end of the HUB75 ribbon cable. That end = Pin 1 end.
   b. Look at EXP-012 working setup: when the system passes, the Pin 1 end of the ribbon cable was plugged into the HCT perfboard adapter? Or was Pin 1 end plugged into the PANEL? Answer by inspecting both connectors.
   c. Rule: On our PCB, the HUB75 header (J3) shall face the cable direction that feeds the CABLE AWAY from our board TOWARD the panel, and the PIN-1 END of the cable plugs into either the board or panel depending on perf setup. Either way, our PCB receives a signal from ESP32/HCT and DRIVES the panel → so our header is the OUTPUT going to the panel. Draw the direction: signal flow on PCB = ESP32 → HCT → J3 → cable → PANEL. The arrow "PANEL IN →" points in the direction away from HCT chips, along the signal flow direction toward the connector that exits to the PANEL. Record this orientation note.
4. **Schematic add J3:** Symbol = Conn_02x08_Odd_Even or 2×8 shrouded header. Designator J3. Footprint = PinHeader_2x08_P2.54mm_Vertical_Shrouded (keyed, shrouded, 2.54 mm pitch vertical).
5. **Wire J3 to signal nets:** Use PIN_LEVEL_APPENDIX §5 column "Pin #" (1 through 16) and "Signal". For each pin: wire to corresponding HUB75_* net from U1/U2 B-outputs (CSV lookup). 16 pins × 1 wire. GND pins → GND net. Spare pins → GND or NC.
6. **Schematic text arrow annotation:** Next to J3 symbol, add text: "PANEL IN → [direction signal flow]". (Step 9 adds the actual PCB silkscreen arrow; this reminds us during layout.)
7. Save schematic. Annotate J3. ERC: 0 critical.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Schematic has 2×8 shrouded J3, 16 pins wired per appendix + validated CSV, direction annotation matches working perfboard cable flow (arrow to panel).
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- 2×8 J3 present, shrouded footprint, 2.54 mm pitch.
- 16 pins wired per appendix §5 / validated CSV. GND pins → GND. 14 signals correct per 3 random spot-checks.
- Physical orientation check performed (perf + cable in hand). Pin-1 end documented (to board or to panel). Arrow direction to PANEL written on schematic.
- ERC 0 critical.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-157-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- Updated schematic committed.
- `docs/pm/evidence/AF-157-hub75-orientation.md` (3-pin spot-check pass; pin-1 perf direction documented; arrow-to-panel note; ERC 0 critical)
**Safety considerations:**
⚠️ Shrouded header force: if the user tries to plug a non-keyed cable into a shrouded header backwards, the shroud plastic prevents full insertion (key tab blocks it) — GOOD. But if user forces it with pliers, pins bend. Not electrical safety. Do not force connectors.
**Known uncertainties:**
Shrouded vs non-shrouded 2×8 header: JLCPCB assembly library has both. Keyed shrouded (with polarization tab) = strongly preferred for idiot-proofing. 2×8 2.54 mm shrouded header vertical = standard part.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Pin mapping wrong (3-spot check fails): swap 2 nets in CSV lookup, rewire. Orientation unclear (perf set up and taken down already): do a 2-minute perf bench test of cable direction: unplug HUB75 from perf, plug Pin-1 end into perf, run test pattern → works = direction correct. If Pin-1 end to panel works = direction confirmed.
**Source references:**
- JIRA.md §Conditional PCB Work Step 6 "HUB75 keyed 2×8 connector pinout matches perf validated; silkscreen 'PANEL IN →' arrow away from board to panel"
- PIN_LEVEL_APPENDIX.md §5 (HUB75 pinout pin 1..16 signal names)
- AF-067/AF-089 (perf working setup; physical cable orientation)
- EXP-011/EXP-012 (perf validation → connector direction known)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs firmware polarity-verify
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-158: Step 7 Power/GND entry. Screw terminal 2-pin 5.08mm pitch or 2× XT30 or 2× ferrule-compatible connector. 5 V high-current path TRACE WIDTH RULE: ≥40 mil (1.0 mm) minimum for 5 V traces. Use copper pours (top and bottom) for GND and 5 V if possible. Vias stitching between top and bottom pour along current path.

**Task ID:** AF-158
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 7 Power/GND entry final connector spec (confirmed Step 3 choice) + PCB LAYOUT RULES to enforce in Step 9. 5 V traces ≥ 40 mil (1.0 mm) width MINIMUM. Copper pour GND top + bottom + 5 V pour partial or bottom. Via stitching top/bottom along current path.
**Description:** Step 3 picked the 5 V entry connector type (screw terminal 5.08mm OR XT30). Confirm that choice here (write it into schematic explicitly with confirmed footprint). Then, define the PCB LAYOUT POWER RULES that will be enforced in Step 9 (layout). Schematic power connections are already made; layout is where trace width determines whether 8 A @5 V (40 W) through our tiny PCB causes the trace to melt or voltage to drop.
PCB layout power rules (VERBATIM — these become Step 9 Pcbnew DRC constraints):
1. **5 V entry connector trace width MINIMUM = 40 mil (1.0 mm) = 1 mm copper width.** This includes ALL traces from 5 V entry pin → HCT VCC pins (pin 20 U1/U2) → ESP32 socket 5 V pin → (optional) C3 + pin. 1 mm copper trace on 1 oz Cu PCB has ~50 mΩ per 100 mm length, carrying 8 A drops 0.4 V → acceptable (4.6 V at HCT VCC min). Narrower 0.5 mm = 100 mΩ/100 mm = 0.8 V drop → 4.2 V, below HCT 4.5 V VCC minimum = NON-FUNCTIONAL at max load. 40 mil is floor; 60 mil (1.5 mm) preferred where space allows.
2. **GND path trace width minimum = 40 mil (1.0 mm) SAME as 5 V.** Current flows in a loop; GND carries same 8 A. Same reasoning.
3. **Copper pours (planes) preferred over individual traces:**
   - **TOP layer = continuous GND copper pour (ground plane):** Entire top layer (unless ESP32/HCT signal routing requires gaps) filled with GND net copper. Benefits: (a) massive GND cross-section → near-zero voltage drop, (b) EMI shielding for signal traces below, (c) easier via stitching to bottom GND pour.
   - **BOTTOM layer = partial 5 V copper pour + continuous GND copper pour everywhere else:** 5 V pour under the 5 V entry connector → up to U1/U2 VCC pins region. All other bottom copper = GND. This gives 5 V a 10 mm-wide "bus" instead of a 1 mm trace (ultra-low resistance).
4. **Via stitching between top GND and bottom GND pours:** Along the 5 V return path (from 5 V entry GND pin, along HCT ICs GND pins), place a grid of VIAS (metal-filled holes) connecting top GND pour to bottom GND pour, every 5 mm, for the entire length of the board. Vias stitch = both layers act as one thick plane (further reduces resistance and inductance). Vias size: 0.8 mm drill / 1.4 mm annular ring (standard cheap PCB fab spec).
5. **(If C3 1000 µF installed):** Its + lead connects DIRECTLY to 5 V entry pin via the 5 V pour (short as possible). - lead connects to GND pour. Keep loop area between 5V+ and GND for C3 as small as possible (< 20 mm² loop) to maximize high-frequency decoupling effectiveness.

Document these rules. If connector choice is still ambiguous (Step 3 was undecided between a/b), finalize now. Schematic update: confirm J2 footprint matches final choice. Save schematic. Annotate. This completes the schematic (Steps 3-7 done; Step 8 is ERC review of the whole schematic).
**Why this task exists:** 1 oz copper trace width-current table: 1 mm (40 mil) trace = 1.6 A max for 10 °C rise in free air. But we use copper pour instead = 10+ mm width = 10 A safely. Without explicit ≥40 mil rule, a novice Pcbnew user routes +5 V with a default 0.25 mm (10 mil) signal trace = 0.6 A rating = melts at 8 A. Copper pours are the #1 technique for high-current PCBs.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: connector chosen AF-154.
**Blocked by:** ADR-016, AF-154 (connector choice confirmation).
**Required hardware:** Connector sample (check current rating of actual chosen screw terminal or XT30 — XT30 = 15 A good; 5.08 mm screw = typically 10 A/pin good; both exceed our 8 A max).
**Required tools:** None.
**Required software:** KiCad Eeschema.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify. Proceed.
2. Open schematic from AF-157.
3. **Confirm 5 V entry connector choice FINAL:**
   a. Screw 2-pin 5.08 mm pitch (PHOENIX-style) OR XT30 (polarized). Make decision. If Step 3 already picked one, confirm rating.
   b. Update J2 footprint FINAL in schematic: e.g., `TerminalBlock_02x05.08mm_1x02_P5.08mm_Horizontal` (screw horizontal) or `Connector_XT30PW-F_Vertical` (XT30 female PCB mount).
   c. Add schematic note: "ENTRY CONNECTOR FINAL: [type]. Current rating ≥ 10 A per pin verified."
4. **Write PCB power layout rules VERBATIM (5 rules 1-5 above) as a schematic text block titled "PCB LAYOUT POWER RULES (for Step 9)".** These will be Step 9's checklist.
5. Save schematic. Final full schematic annotation pass (J1=ESP32 sock, J2=5V entry, J3=HUB75, U1/U2 HCT, C1/C2/C3 caps — all designators unique and assigned).
6. Schematic DONE (Steps 3-7 = all components on schematic). Summary comment block: all nets named, all components placed, ready for Step 8 ERC.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Schematic COMPLETE (all parts wired). J2 connector finalized with footprint. 5 power rules written as Step 9 layout checklist.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- J2 connector FINAL choice documented with current rating ≥10 A/pin. Footprint assigned and matches.
- PCB LAYOUT POWER RULES block present in schematic notes: all 5 rules written VERBATIM (trace ≥40 mil, GND same, top GND pour + bottom GND/5V pours, via stitching 5 mm grid, C3 loop <20 mm² if applicable).
- Schematic declared COMPLETE: components J1-J3, U1-U2, C1-C3. All pins of all ICs wired or NC.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-158-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- Final schematic file (pcb/condx-hct-adapter.kicad_sch) committed with final connector.
- `docs/pm/evidence/AF-158-power-entry-rules.md` (connector final choice + rating; 5 rules verbatim copied for Step 9 layout checklist)
**Safety considerations:**
⚠️ High-current trace melting. If Step 9 violates the 40 mil minimum (by mistake), and we run 2 panels full-white = 8 A flows, trace temperature rises: 10 mil (0.25 mm) copper at 8 A → ~400 °C in seconds → trace vaporizes → PCB delaminates, fire risk near the harness. This is why 40 mil is MINIMUM (1 oz). Copper pours preferred. Step 10 DRC explicitly checks.
**Known uncertainties:**
JLCPCB $2 5-board price tier = 1 oz finished copper weight standard. 2 oz = extra cost, 1 oz is fine with 1 mm trace + 5 V pour. We assume 1 oz and design pour generously.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Connector rating unknown (cheap screw terminal from AliExpress has no datasheet): assume 10 A worst case, use copper pour; XT30 always 15 A per RC hobbyist standard (universal).
**Source references:**
- JIRA.md §Conditional PCB Work Step 7 "Power/GND entry connector finalized. 5V trace width rule ≥40mil (1mm). Copper pours GND top+bottom, via stitching."
- UL 1280 / IPC-2221A trace width vs current nomograph (informative: 40 mil 1oz 10°C rise = 1.5 A in free air, but plane effectively adds width)
- BOM.md §8 Fabrication services (JLCPCB default copper weight 1 oz = reference)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs power
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-159: Step 8 ERC (Electrical Rules Check) in KiCad Eeschema. Run full ERC. Fix ALL errors. Fix ALL critical warnings. Non-critical warnings: document which are WAIVED and why (max 3 waived).

**Task ID:** AF-159
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 8 KiCad ERC full run on schematic. Fix ALL errors. Fix ALL critical warnings. Non-critical warnings: WAIVE max 3 with documented reasons.
**Description:** Run KiCad's Electrical Rules Checker (ERC) on the completed schematic (Steps 3-7). ERC is the schematic compiler. It catches: (1) Output pins shorted together, (2) inputs unconnected, (3) power pins not driven, (4) reversed polarized capacitor, (5) multiple net labels on different pins, (6) mismatched pin types (output→output driver conflict). These are exactly the classes of errors that make $30 boards non-functional scrap. We do NOT proceed to layout (Step 9) with ANY ERC error or critical warning. Non-critical warnings (cosmetic net naming, known unused pins) can be waived with a note, MAX 3 waived (to prevent waive-all abuse).
Procedure:
1. Open schematic condx-hct-adapter.kicad_sch. Eeschema → Inspect → Run ERC. Use default rules (or stricter if desired). Generate ERC report file `pcb/erc-report.txt`.
2. **Errors (red X, severity ERROR):** Count N_error. ALL MUST BE FIXED. Zero tolerance. Typical fixes:
   - "Unconnected power pin" → forgot GND/VCC on U1/U2/ESP32 → wire them.
   - "Net not driven" → add PWR_FLAG to +5V/GND (already done, but if missed here).
   - "Output pin connected to output pin" → two outputs accidentally shorted. Find the two signals on the same net, separate.
   - "Global label not connected anywhere" → typo in label name. Fix.
3. **Critical warnings (orange, severity WARNING CRITICAL):** Examples: "Unconnected input pin (HCT input)", "Polarized capacitor reversed", "Differing net names in same net". Fix ALL. Zero tolerance. Typical fix reversed C3: swap anode/cathode wires (polarized cap + symbol to +5V now to wrong side). Unconnected HCT input: that's a bug (all 8 inputs per HCT should be connected to ESP32 per 16-signal map; if CSV is 14 signals because R2/G2/B2 are only 6 RGB + 9 = 15? HUB75 has up to 13 signals total typically, so 2 NC pins on 16 pin HCT pairs. If an input pin is truly unused on HCT → tie to GND (not floating input!) or mark NC with explicit flag and wave on schematic.
4. **Non-critical warnings (yellow light, severities WARNING / INFO):** Examples: "NC marker placed on unused pin", "Net has only one pin (test point)", "Different value for same reference designator in different sheet". These are informational or correct-by-design. WAIVE a maximum of 3 non-critical warnings only. For each waived warning, write 1-sentence reason: e.g., (1) "ESP32 GPIO 34-39 are input-only and not used in our design, marked NC." (2) "HCT pin 7 input unused because only 13 HUB75 signals; tied to GND to prevent floating input logic oscillation." (3) "Net label `GND` aliased to global label `GND` — same net intentional." Warnings beyond 3 → must fix instead of waive.
5. After fixes + waives, RE-RUN ERC. Verify N_error = 0, N_critical_warning = 0. Total waived = ≤3. Record counts.
**Why this task exists:** 2 weeks and $30 are wasted if you layout a schematic that had ERC errors. ERC catches 90% of stupid schematic mistakes (which every designer makes regardless of experience). 0-error rule = boards come back functional on first spin 80% of the time vs 30%.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: COMPLETE schematic (Steps 3-7 done). KiCad Eeschema.
**Blocked by:** ADR-016, AF-154 through AF-158 (schematic done)
**Required hardware:** None. Software only.
**Required tools:** None.
**Required software:** KiCad Eeschema with ERC.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. Open schematic from AF-158 final version.
3. Run ERC: Inspect → Electrical Rules Checker. Checkboxes: all default (report unconnected pins, report similar net names, check reversed pins, check pin conflicts, etc.). Save report to pcb/erc-report.txt.
4. Iterate ERROR-fix loop:
   a. Count errors in ERC panel. For each error row: click → jump to schematic location. Understand. Fix wiring/net/flag.
   b. Re-run ERC. Repeat until N_error = 0.
5. Iterate CRITICAL WARNING fix loop:
   a. For each CRITICAL warning row: click → fix. Reversed C3? Swap wires. Unconnected input? Connect or tie to GND explicitly. Re-run.
   b. Repeat until N_critical = 0.
6. Count remaining NON-CRITICAL warnings (yellow/info only).
   a. If > 3: choose the 3 most innocuous to waive; FIX the rest (even if they seem OK, it's faster than documenting >3).
   b. For each waived warning (max 3): compile 1-line reason in a waived-warnings table in evidence.
7. Re-run ERC final. Screenshot or copy ERC summary line: "Errors: 0, Warnings critical: 0, Warnings: N, Info: M. Waived: 3."
**Expected result:** PRE/WF4: SKIP. POST/ESP32: ERC Errors 0. Critical Warnings 0. Non-critical waived ≤ 3 with reasons.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- ERC report final run shows: Errors = 0 (hard requirement).
- Critical warnings = 0.
- Non-critical warnings waived: count ≤ 3, each with 1-line why documented.
- pcb/erc-report.txt committed.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-159-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `pcb/erc-report.txt` (ERC report saved)
- `docs/pm/evidence/AF-159-erc-summary.md` (errors=0; critical=0; waived-warning table count ≤ 3 rows with reasons; final ERC summary line)
**Safety considerations:**
N/A — schematic review.
**Known uncertainties:**
KiCad 7 vs 8 default ERC rules differ slightly. Use default set; if custom project has relaxed rules, stick to default (tighter = better for prototype).
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Can't get error count to 0 after 10 iterations → ask for peer review of schematic (send Kicad_sch file), or start a fresh schematic copy-paste in case of file corruption (rare). Warnings > 3 after fixes: waive only the 3 most clearly-cosmetic, fix/improve the rest. E.g., "NC marker on pin" → if it's unused GPIO, keep as waive (it's correct-by-design).
**Source references:**
- JIRA.md §Conditional PCB Work Step 8 "ERC in KiCad Eeschema. 0 errors, 0 critical warnings, max 3 non-critical waived with reason."
- KiCad Eeschema manual / docs: ERC feature (standard tool)
- Schematic files AF-154 to AF-158 (the schematic under test)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs validation
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-160: Step 9 PCB layout in Pcbnew. Board outline rectangle (target <100×100 mm per BOM fabrication note — that's the JLCPCB $2 / 5 boards price tier; IF it doesn't fit at <100×100 document the smallest dimension and accept slightly higher fab cost). 4 mounting holes M3 at corners. Trace width RULE ENFORCED in design rules: 5 V traces ≥40 mil (1.0 mm), signal traces (HCT245→ESP32, HCT→HUB75) ≥8 mil (0.2 mm). Ground pour on top layer, 5 V pour or partial pour on bottom layer (or vice versa, but continuous GND plane preferred). Component placement: 100nF decoupling caps WITHIN 5 mm of each HCT245 VCC pin. ESP32 headers aligned to board edge if USB-C must protrude.

**Task ID:** AF-160
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 9 Pcbnew layout. Board outline <100×100 mm (JLC $2 tier) if possible; else document smallest size and pay slight premium. 4×M3 corner mount holes. DRC rules: 5V≥40mil, signals≥8mil. TOP=GND pour, BOT=5V partial pour + GND pour. Decoupling C1/C2 WITHIN 5 mm of U1/U2 pin20 VCC. ESP32 header USB edge alignment.
**Description:** Perform the PCB physical layout (Pcbnew). This is the design that goes to fabrication. Takes 1-4 hours depending on experience. Follow every VERBATIM rule below. Check off each.
Layout rules CHECKLIST VERBATIM (execute in order):
1. **Import netlist from schematic into Pcbnew:** Schematic edits → update PCB from schematic (F8). Footprints loaded onto canvas as unratted ratsnest.
2. **Board outline rectangle (Edge.Cuts layer):** Start with target 90 × 90 mm. If all components + keep-outs fit within, shrink to smallest rectangle with 5 mm clearance to components all sides. Goal: <100 × 100 mm = JLCPCB 5 boards for $2 base price (amazing deal). If we can't fit at 100×100 (esp. with ESP32 + 2× DIP-20 + connectors), the 100×150 mm tier is $4-$6 (still cheap). Document final outline dimensions W_mm × H_mm. If ≥100 mm either axis: note "Slight premium fab cost, accepted".
3. **4 M3 mounting holes at corners:** Place 4 non-plated through-holes (NPTH if PCB supports; or plated through-hole OK for M3 clearance 3.3 mm drill / annular ring 5 mm). Position holes 5 mm inward from each board corner. Center-to-corner distance = 5 mm minimum.
4. **Setup Design Rules (DRC parameters) BEFORE routing any traces:**
   - **5 V power class net:** Set custom net class for 5 V = trace width minimum 1.0 mm = 40 mil. Clearance from 5 V to any other net ≥ 0.3 mm (12 mil).
   - **GND net class:** Same as 5 V: 1.0 mm min trace width.
   - **Default signal class (all other nets: GPIO / HCT signals):** min trace width = 0.2 mm = 8 mil. Clearance ≥ 0.15 mm (6 mil).
   - **Via size:** default = 0.8 mm drill / 1.4 mm annular ring (standard cheap fab). Save DRC preset.
5. **FLOORPLAN (components placed, no traces yet):** Place components within board outline.
   a. **J2 (5 V entry, screw/XT30):** Place on LEFT edge of board, connector opening facing OFF the board to left (cable enters from left, no need to go around ESP32).
   b. **J3 (HUB75 2×8 shrouded header):** Place on RIGHT edge of board, shroud notch facing OFF the board to RIGHT (cable exits right toward panel; silkscreen arrow PANEL IN → pointing right, matches J3 direction).
   c. **U1 and U2 (two HCT245 DIP-20):** Place side-by-side in CENTER area of board, pin 1 notches facing same direction (e.g., both left). Spacing between them = 10 mm minimum (room for decoupling caps between them).
   d. **ESP32 socket (J1: 2×20 female headers × 2):** Place ALONG the TOP EDGE or BOTTOM EDGE of board. Ensure USB-C connector on the piggybacked ESP32 DEVKIT protrudes PAST the PCB outline by at least 2 mm (so USB-C cable can plug in without bumping our PCB edge). Check: ESP32 board outline + USB protrusion = no overlap with HCT ICs. If needed, move ESP32 socket to bottom edge instead of top. Antenna zone (AF-152): if ESP32 antenna corner is near the board edge, ensure NO copper pour within 5 mm of that corner (keep-out zone).
   e. **C1 (100 nF U1 decoupling):** PLACE WITHIN 5 MM OF U1 PIN 20 (VCC). Use a ruler in Pcbnew: distance from C1 pad center to U1 pin-20 pad center ≤ 5 mm.
   f. **C2 (100 nF U2 decoupling):** PLACE WITHIN 5 MM OF U2 PIN 20. Same rule.
   g. **C3 (1000 µF bulk if present):** Place NEAR J2 5 V entry connector, its + pad adjacent to J2 +5 V pin. Loop area (rectangle from J2 + to C3 + to C3 - to J2 -) < 20 mm². Small as possible.
6. **ADD COPPER POURS (Filled Zones) BEFORE auto-routing or manual routing:**
   - **TOP layer = GND filled zone:** Draw polygon on F.Cu layer covering entire board interior, 0.5 mm inset from Edge.Cuts. Assign net = GND. Remove polygon from ESP32 antenna no-go 5 mm zone (subtract keep-out).
   - **BOTTOM layer = GND filled zone (main) + partial 5 V filled zone near J2/U1/U2 VCC area:** Two polygons on B.Cu: (a) Large GND zone covering entire board inset 0.5 mm; (b) Smaller 5 V zone covering J2 + pin20 U1 + pin20 U2 region (size ~20 × 40 mm). Assign nets accordingly.
   - **VIA STITCHING grid:** Add 15-20 vias in a 5 mm grid pattern throughout the board, connecting GND TOP zone ↔ GND BOT zone. Use default via size.
7. **ROUTE TRACES (manual, 1-2 h):**
   - Start with 5 V / GND connections first: J2 → C3 (+/-) → U1 pin20/10, U2 pin20/10, ESP32 5V/GND pins. Use 1.0 mm width (set net class auto applies). Use direct short routes.
   - Then signal traces: ESP32 socket → U1/U2 A-pin inputs (8 per IC, 16 traces total; 0.2 mm width). U1/U2 B-pin outputs → J3 HUB75 pins (16 traces total 0.2 mm).
   - DIR pin 1 → +5 V jumper (very short, 1 mm wide, near U1/U2 pin1). /OE pin 19 → GND jumper short.
   - Use vias sparingly to swap layers only when needed (avoid multiple signal layer transitions on same net if possible).
   - Keep signal traces away from board edges (≥0.4 mm clearance to Edge.Cuts = standard fab requirement).
8. **FINAL CHECKLIST before declaring layout done:**
   - 5 V net traces ≥ 1 mm everywhere? (Scan visually; no skinny 5 V traces.)
   - C1/C2 within 5 mm of U1/U2 pin20? (Re-measure in Pcbnew with dimension tool; move caps if needed.)
   - GND top + bottom continuous? Vias stitched?
   - 4 M3 holes plated or non-plated correct size?
   - Board outline closed rectangle (no open edges on Edge.Cuts)?
   - HUB75 header faces to PANEL direction (arrow check Step 6).
   - ESP32 USB protrudes past edge? (Mount ESP32 on footprint mentally.)
   - No components closer than 1 mm to board edge?

Save layout as pcb/condx-hct-adapter.kicad_pcb.
**Why this task exists:** Layout = the physical part that costs $2-6 and 2 weeks lead time. Every rule above prevents a known PCB failure: decoupling too far = noise; 5 V too narrow = voltage drop/melt; GND plane not stitched = EMI; mounting holes missing = can't attach to chassis. 100×100 mm target = cheapest fab.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: schematic ERC 0 errors (AF-159 PASS). Footprints assigned in schematic (AF-154 through AF-158). KiCad Pcbnew.
**Blocked by:** ADR-016, AF-159 (ERC done = schematic golden).
**Required hardware:** None.
**Required tools:** None. Pcbnew.
**Required software:** KiCad Pcbnew.
**Exact execution steps:** 1-8 VERBATIM as checklist above. Save .kicad_pcb.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Layout completed. 8-point final checklist each item PASS. Final dims documented (W × H mm). <100 mm or ≥100 mm premium accepted note.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- PCB layout file pcb/condx-hct-adapter.kicad_pcb saved with all nets routed, 0 unconnected ratsnest lines remaining.
- Board dims: W × H mm recorded. <100 mm both axes = JLC $2 tier confirmed; if either axis ≥100 mm = cost premium accepted note.
- 4 × M3 mounting holes at corners, 5 mm in from edge.
- DRC rules set: 5 V ≥ 40 mil, signals ≥ 8 mil. GND via size standard.
- Copper pours: TOP = GND, BOT = GND + 5 V partial. Vias stitched (≥ 10 GND vias count).
- Decoupling placement: C1 pad to U1 pin20 center distance ≤ 5 mm. C2 to U2 pin20 ≤ 5 mm. Distance tool verified.
- ESP32 USB protrusion: ≥ 2 mm past board edge (USB accessible). HUB75 arrow direction correct (J3 to panel).
- 8-point FINAL checklist: all 8 items PASS (no violations).
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-160-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `pcb/condx-hct-adapter.kicad_pcb` (committed)
- `docs/pm/evidence/AF-160-layout-report.md` (board W×H dims + cost tier; 4 holes documented; DRC rule values; pour summary; C1/C2 5 mm distance measurement values; USB protrusion mm; 8-point checklist 8/8 PASS)
- `docs/photos/condx-pcb/pcb-layout-screenshot.png` (Pcbnew screenshot showing full routed board with pours visible; for evidence records)
**Safety considerations:**
5 V trace width = safety-relevant (melting). Top GND plane = also touch-safety (if user contacts board through a vent hole, they hit GND not 5 V — GND plane is safer than random 5 V traces).
**Known uncertainties:**
Placement 90×90 mm feasibility: with 2 DIP-20 + 2×20 headers + 2 connectors + C3 10 mm diameter can, 90×90 may be tight. 100×100 should fit comfortably. If 100×100 still not, go 100×120 ($4 slight premium — still fine).
**Failure response:**
Can't fit components within 100 mm → expand to 100 mm × 110 mm. Accept slight cost increase. Can't meet 5 mm decoupling rule: HCT U1 VCC at pin20, cap C1 at opposite side of U1 → flip C1 to pin20 side, re-route. USB not protruding: move ESP32 socket 2 mm toward the edge (shrink outline the opposite side to keep total size same; or allow ESP32 to overhang the PCB edge — ESP32 is mounted ON TOP of our PCB; overhang is OK as long as mounting screws don't collide with ESP32 components).
**Source references:**
- JIRA.md §Conditional PCB Work Step 9 "PCB layout Pcbnew. Target <100×100 mm JLC tier. 4×M3 holes. DRC: 5V≥40mil, signals≥8mil. GND pour top + 5V/GND bottom. Decoupling caps WITHIN 5mm HCT VCC. ESP32 USB edge alignment."
- BOM.md §8 Fabrication services (JLCPCB/Jiepei/JDBPCB tier pricing for ≤100×100 mm = $2 reference)
- AF-155 (HCT schematics, U1/U2 pin numbers pin20 = VCC)
- AF-156 (decoupling placement rule, executed here)
- AF-158 (PCB Power Rules VERBATIM source)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs purchasing
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-161: Step 10 DRC (Design Rules Check) in Pcbnew. Run full DRC. Fix ALL errors. Fix ALL critical warnings (clearance violations, trace-to-edge, annular rings). Non-critical waived warnings documented max 3.

**Task ID:** AF-161
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 10 Pcbnew DRC full run. Fix ALL errors. Fix ALL critical warnings (clearance, trace-to-edge, annular ring). Non-critical max 3 waived with reasons.
**Description:** PCB equivalent of ERC. DRC catches mechanical/clearance/sizing errors that ERC can't: (1) 5 V trace 0.2 mm somewhere because you accidentally routed 5 V with default signal class (common mistake). (2) Trace 0.1 mm from board edge = fab will mill it off. (3) Via annular ring 0.05 mm = drill breaks the ring = open via. (4) Solder mask bridge between pins 0.05 mm apart = solder bridge short. (5) Courtyard overlap of two components (can't physically solder U1 next to J1 with 0 mm gap). All are common, all break the board. Procedure mirrors ERC step:
1. Open Pcbnew layout from AF-160. Inspect → Design Rules Checker. Use default DRC from rules defined Step 9 (5 V 40 mil, signal 8 mil, clearances). Generate DRC report to pcb/drc-report.txt.
2. **ERRORS (red):** N_error = count. Fix ALL. Zero tolerance. Example fixes: "Trace too narrow (net 5V, width 0.25 mm, expected 1.0 mm)" → select that track segment, change its width property to 1.0 mm (or reassign to 5 V net class if it lost the class). "Clearance violation: 0.09 mm between U1 pin and via" → move via 0.2 mm away.
3. **CRITICAL WARNINGS (orange critical):** Typical: "Trace to board edge < 0.4 mm" → move trace away from edge. "Annular ring < 0.15 mm for via" → change via size to standard 0.8/1.4 or accept if just barely under and fab says OK (better to enlarge). "Courtyards overlap U1/C1" → move C1 1 mm. Fix ALL critical.
4. **NON-CRITICAL WARNINGS (yellow/info):** "Courtyard close to courtyard", "Silkscreen clipped by board edge", "Unconnected pad (if pad has no net on purpose?)". Waive max 3, 1-line reason each.
5. Re-run DRC. Final: Errors 0, Critical 0, Waived ≤ 3.
**Why this task exists:** DRC catches the "stupid little mistake" that $2 can't fix after fabrication. Experience shows 50% of first-spin boards have ≥ 1 DRC-caught defect that would render it non-functional. 0-error = dramatically higher first-spin success rate.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: layout from AF-160, DRC rules set Step 9.
**Blocked by:** ADR-016, AF-160
**Required hardware:** None.
**Required tools:** None.
**Required software:** KiCad Pcbnew DRC.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. Open Pcbnew from AF-160 .kicad_pcb.
3. Run DRC with full rules. Save pcb/drc-report.txt.
4. Error-fix iteration loop: for each error, inspect, fix. Re-run until errors = 0.
5. Critical warning fix iteration: for each critical warning, inspect, fix. Re-run until critical = 0.
6. Non-critical count. If ≤ 3: waive with reasons table. If > 3: fix most trivial.
7. Final DRC run: confirm Errors 0, Critical 0, Waived ≤ 3.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: DRC Errors 0, Critical 0, Waived ≤ 3 documented.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- DRC final: Errors = 0. Critical warnings = 0.
- Waived warnings ≤ 3 rows, each with 1-line reason.
- pcb/drc-report.txt committed.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-161-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `pcb/drc-report.txt` committed.
- `docs/pm/evidence/AF-161-drc-summary.md` (errors=0; critical=0; waived table ≤ 3 rows)
**Safety considerations:**
N/A — PCB mechanical review.
**Known uncertainties:**
JLCPCB/Jiepei/JDBPCB actual DRC capabilities may be slightly different from KiCad defaults. When we generate Gerbers (Step 12), we'll also run the fab house's online free DRC (JLCPCB has a "Check Gerber DRC" web tool that checks their factory-specific rules). That's an extra safety net in addition to KiCad DRC.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Persistent annular ring violation on tiny via: increase via drill size from 0.6 mm to 0.8 mm standard. Trace-to-edge can't fix (routing too dense): increase board outline 2 mm in that axis (tiny 100→102 mm increases cost by pennies).
**Source references:**
- JIRA.md §Conditional PCB Work Step 10 "DRC Pcbnew. 0 errors, 0 critical, max 3 waived."
- KiCad Pcbnew DRC docs (standard tool)
- AF-160 layout + DRC rule setup (layout under test)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs validation
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-162: Step 11 Physical/connector-orientation review. Hold perfboard build next to PCB layout on screen. Visualize: board installed in chassis → HUB75 cable reaches panel without strain (cable exit direction = toward panels). USB on ESP32 accessible = USB connector on PCB edge accessible, not blocked by standoffs. GPIO silkscreen labels match PIN_LEVEL_APPENDIX.

**Task ID:** AF-162
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 11 Physical/orientation review: hold real perfboard build next to PCB layout on screen side-by-side. Visualize install in chassis: (1) HUB75 cable exits board toward panels (no strain; direction matches). (2) ESP32 USB-C on PCB edge ACCESSIBLE; not blocked by chassis standoffs when mounted. (3) GPIO silkscreen labels printed on PCB match PIN_LEVEL_APPENDIX §3/§4 validated.
**Description:** Final design sanity check BEFORE generating Gerbers. "If you can't visualize the board working in the chassis, the board won't work in the chassis." Hold the physical working perfboard + ESP32 stack in your left hand. Display the PCB layout screen shot with the board outline visible in your right hand (or same monitor, split screen). Visualize 3 checks. Fix any orientation issues NOW (free, 2 minutes). After Gerbers → $30 and 2 weeks to fix.
3 checks VERBATIM:
1. **HUB75 cable exit direction NO STRAIN:** On screen layout, locate J3 HUB75 shrouded header on RIGHT edge of PCB (as placed Step 9, facing right to PANEL). Mentally install our PCB inside the frame DC chamber: the HUB75 0.5 m cable plugs into J3 facing RIGHT → goes to the 2 panels of one row. The cable travel distance from PCB edge to panel HUB75-IN is ~300 mm. Cable route is clean (no 180° U-turn immediately). Compare to perfboard: on perf, HUB75 plugged in and traveled 300 mm to panels without strain. Same orientation on PCB? YES/NO. If NO (e.g., header faces left instead of right → cable must do a 180° strain loop → fail). Fix: rotate header 180° in layout, reassign pin 1 location, rewire nets.
2. **ESP32 USB-C ACCESSIBLE:** Visualize ESP32 devkit piggybacked onto PCB socket (headers). The ESP32 USB-C connector is on the edge of the ESP32, overhanging our PCB edge by 2 mm (per Step 9 placement rule). When the assembly is mounted inside chassis on standoffs, is the USB-C port reachable with a standard USB-C cable (not blocked by chassis M3 screws or standoffs)? Rule: USB-C center of connector must be ≥ 10 mm from any M3 standoff center. Measure on layout. If a standoff is 8 mm from USB → fail. Fix: move a corner mounting hole 5 mm away from that corner (still 5 mm from edge, just shifted).
3. **GPIO SILKSCREEN LABELS printed on PCB match PIN_LEVEL_APPENDIX validated:** Go to Pcbnew Silk screen layer (F.SilkS). Next to ESP32 socket footprint, do we have silk labels for the key GPIO pins? E.g., next to the pin that is "GPIO 5 = CLK (validated)" we should print "CLK" or "GPIO5" or similar. Minimum: label the HCT signal input pins (U1 A1 = ESP32 GPIO4 = R1 signal; print a tiny "R1" label next to that ESP32 socket pin silk if possible; or a table on back silk layer). Purpose: when we hand-solder the socket or debug a bad solder joint, we can easily tell which physical pin is signal R1 without holding up the schematic. Print at minimum 0.8 mm font (readable). MATCH labels to validated CSV, not appendix.
Fix any issues. Save updated layout (silk labels or hole moves or header rotations). Re-run DRC if changes (clearance/hole moves may trigger new warnings).
**Why this task exists:** Orientation mistakes are the #1 most expensive "stupid" PCB mistake. A 180° HUB75 header = cable does a U turn inside tight chassis = intermittent garble from cable strain (looks like a mysterious EMI bug that takes weeks to diagnose). Silk labels save 30 minutes per debug session with multimeter.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: layout from AF-161 (DRC golden), physical perfboard + ESP32 in hand to visualize.
**Blocked by:** ADR-016, AF-160 (layout), AF-161 (DRC done before small adjustments; adjust then re-run DRC small).
**Required hardware:** Working perfboard build (EXP-012 setup) in hand: ESP32 + HCT perfboard + HUB75 cable plugged in the direction that worked. Also 1 M3 standoff + screw to visualize standoff clearance.
**Required tools:** None (visualization).
**Required software:** Pcbnew open (layout + silk screen layer visible).
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. **Set up visualization:** Split screen or two monitors: LEFT = physical perf board held up, HUB75 cable plugged in, cable direction toward PANELS noted with a finger arrow. RIGHT = Pcbnew layout, all layers visible (copper + silk + edge cuts). Zoom to board outline.
3. **CHECK 1 (HUB75 exit):** On layout, J3 on right edge, cable exits to RIGHT (toward panel). Hold perf board HUB75 exit side next to screen. Is exit side same? If exit is LEFT on layout but perf was RIGHT → rotate footprint 180°: in Pcbnew, select J3, Rotate 180°, flip on layer if needed. Re-assign pin 1 net mapping (pin1↔pin16 swap row). Re-check ratsnest closed (no unrouted nets after rotate; re-route any broken traces locally).
4. **CHECK 2 (USB accessibility):**
   a. On layout, find ESP32 USB connector position (overhang 2 mm from board edge per Step 9). Measure distance from USB center to nearest M3 mounting hole center. ≥ 10 mm? PASS.
   b. < 10 mm → fix: take the nearest corner mounting hole, shift its (X,Y) position diagonally 5 mm away from USB (still stays 5 mm minimum from corner outline). Update hole position.
5. **CHECK 3 (GPIO silk labels):**
   a. Show F.SilkS layer (top silk). Zoom to ESP32 socket footprint area.
   b. Add text labels next to ESP32 header pin rows (on 0.8 mm grid between pins if room): for the 15 signals from validated CSV, print signal name in small 1 mm text next to the corresponding ESP32 socket pin. E.g., near socket pin that goes to R1 U1 input → print "R1". Or if too crowded, print on B.SilkS (bottom silk); or print a reference table in empty PCB corner area: "R1→GPIO4 CLK→GPIO5 OE→GPIO13 ..." table format.
   c. Confirm CSV signal names (not appendix provisional) used in labels.
6. **Layout changes? If any fixes in step 3/4/5:** Save updated .kicad_pcb. Re-run DRC (AF-161 procedure quick pass): should still be Errors 0 / Critical 0. If new DRC errors from hole move/silk overlap → fix.
7. Document 3 check results: 3/3 PASS.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: 3/3 orientation checks PASS, any needed fixes applied, DRC re-run still 0 errors, silk labels present (15 signal names near socket or corner table).
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- HUB75 exit direction (Check 1): MATCH perf working direction. No U-turn strain. If fix applied → header rotated, re-wired. PASS.
- USB-standoff distance (Check 2): USB center to nearest M3 hole center ≥ 10 mm. Measured value recorded (e.g., 11.4 mm). PASS.
- GPIO silk labels (Check 3): 15 key signal names present (either next to pins OR corner table), names match validated CSV (not provisional). PASS.
- Any layout changes: DRC re-run → still Errors 0 / Critical 0.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-162-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- Updated PCB layout file (if any changes) committed.
- `docs/pm/evidence/AF-162-orientation-review.md` (Check1 direction match result; Check2 distance mm; Check3 label count = 15 names + match CSV; DRC post-fix 0/0.)
- Optional: `docs/photos/condx-pcb/orientation-perf-vs-layout.jpg` (photo of perf held next to screen layout, for evidence of direction match — nice to have).
**Safety considerations:**
N/A — visualization.
**Known uncertainties:**
Silk 0.8 mm font legibility: on real PCB, 0.8 mm text is readable with a magnifying glass or phone camera zoom. 1 mm = easier to read. If room, use 1 mm.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Rotating J3 180° swaps pins, broke nets: re-route from pin 1 ↔ pin 16 per row (easy in Pcbnew, swap track endpoints). Standoff distance too close: moving mounting hole may cause copper pour clearance DRC error → re-pour GND zones (Refill All Zones button) after hole move.
**Source references:**
- JIRA.md §Conditional PCB Work Step 11 "Physical/orientation review: hold perf next to PCB layout screen. Check HUB75 cable exit no strain, USB accessible, GPIO silk labels match validated appendix."
- AF-067/AF-089 (working perfboard, used for direction visualization)
- PIN_LEVEL_APPENDIX §3/§4 (labels; actual labels MATCH AF-153 CSV)
- AF-152 (ESP32 footprint, USB protrusion position)
- AF-157 (HUB75 direction pin-1 orientation)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs validation
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-163: Step 12 Gerber files generated. Preferred: Gerber X2 format. Acceptable: RS-274X. File naming per JLCPCB convention: top layer .GTL, bottom layer .GBL, top silks .GTO, top mask .GTS, bottom mask .GBS, drill .TXT, drill map .DRD, board outline .GKO, inner layers if any, paste layers if SMD assembly.

**Task ID:** AF-163
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 12 Generate Gerber files (X2 preferred; RS-274X acceptable). Naming JLCPCB convention: .GTL top copper, .GBL bottom, .GTO top silk, .GTS top mask, .GBS bottom mask, .TXT drill, .DRD drill map, .GKO outline, paste if SMD.
**Description:** Export fabrication outputs from Pcbnew → Gerber + drill + maps = industry standard files that PCB factories understand. File naming convention is CRITICAL because factories auto-import by extension. Wrong extension confuses JLCPCB's automated system and can cause wrong layer rotations. JLCPCB convention (also accepted by Jiepei/JDBPCB per BOM research) is .GTL/.GBL/etc (not the older .pho/.gbr generic extensions). Follow JLC naming EXACTLY.
Procedure Pcbnew → File → Fabrication Outputs → Gerbers (.gbr):
- **Output directory:** pcb/gerbers/ (clean empty folder).
- **Format:** GERBER X2 (preferred checkbox "Use extended X2 format"). If fab doesn't support X2 yet, RS-274X is fallback (works everywhere, slightly fewer metadata per aperture).
- **Layers to plot (2-layer board, standard):**
  1. F.Cu → .GTL (Top copper)
  2. B.Cu → .GBL (Bottom copper)
  3. F.SilkS → .GTO (Top silkscreen)
  4. B.SilkS → .GBO (Bottom silkscreen; optional if bottom has no silk, but include anyway)
  5. F.Mask → .GTS (Top solder mask)
  6. B.Mask → .GBS (Bottom solder mask)
  7. Edge.Cuts → .GKO (Board outline, mechanical)
  8. F.Paste → .GTP (Top paste, for SMD stencil if doing SMD assembly later; include for completeness even if TH prototype)
  9. B.Paste → .GBP (Bottom paste, optional include)
- **Options:** Check "Check zone fills before plotting" (auto-refills copper pours). Check "Use proper filename extensions" (auto applies GTL/GBL). Disable "Plot sheet reference on all layers" (we don't want schematic title block on PCB copper). Click PLOT. Generates all Gerber files in pcb/gerbers/.
- **Verify file names after plot in pcb/gerbers/:**
  condx-hct-adapter.GTL (F.Cu)
  condx-hct-adapter.GBL (B.Cu)
  condx-hct-adapter.GTO (F.SilkS)
  condx-hct-adapter.GBO (B.SilkS)
  condx-hct-adapter.GTS (F.Mask)
  condx-hct-adapter.GBS (B.Mask)
  condx-hct-adapter.GKO (Edge.Cuts)
  condx-hct-adapter.GTP (F.Paste) [optional include]
  condx-hct-adapter.GBP (B.Paste) [optional include]
Good naming = 8-10 files with correct extensions. Next: run Gerber Viewer (Pcbnew → View → Gerber Viewer) load all layers. Inspect visually:
- Copper layers top and bottom look correct (no traces chopped at edges, GND pours continuous).
- Edge.Cuts (.GKO) form a single closed rectangle (no gaps, no extra mechanical pieces inside).
- Solder mask (.GTS/.GBS): does NOT cover solder pads (pads appear clear on mask layer visual). Silkscreen text readable.
If any layer visually WRONG → fix layout, regenerate. If OK → proceed to drill files next step (AF-164).
**Why this task exists:** Gerbers = the handoff to manufacturing. Wrong naming = wrong layer rotation = top/bottom swapped = board comes out backwards (copper on wrong side, components can't be mounted). 2 weeks + $30 down the drain for wrong naming. Visual check in Gerber Viewer catches 90% of output issues before sending to fab.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: layout AF-160 + DRC pass (AF-161) + orientation review pass (AF-162). Pcbnew Gerber exporter.
**Blocked by:** ADR-016, AF-160/161/162.
**Required hardware:** None.
**Required tools:** None.
**Required software:** KiCad Pcbnew Fabrication Outputs → Gerbers; Gerber Viewer (gerbview).
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. Open Pcbnew final layout from AF-162.
3. **Gerber export:** File → Fabrication Outputs → Gerbers. Settings: dir pcb/gerbers/, X2 format ON, 8-10 layers per list above, proper extensions ON, zone refill ON, plot.
4. **Verify file names:** `ls -la pcb/gerbers/` → confirm 8+ files have correct JLC extensions .GTL/.GBL/.GTO/.GBO/.GTS/.GBS/.GKO + pastes.
5. **Gerber Viewer visual check:** Pcbnew → Tools → Gerber Viewer. Load all 8-10 files from pcb/gerbers/. Toggle layers:
   a. F.Cu only: does all copper look like routed layout? Traces look continuous, no gaps.
   b. B.Cu only: 5 V pour region + GND pour look correct.
   c. Edge.Cuts only: closed rectangle, no extra lines.
   d. Overlay F.SilkS + F.Cu: silk labels align near pins. All readable.
   e. Overlay F.Mask + F.Cu: all component pads show through mask (black circles = mask opening on pads). No pads covered by mask accidentally.
6. All visual checks pass. Gerbers ready.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Gerber folder populated with 8-10 correctly-named files. Gerber view visual checks a-e pass.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- 8+ Gerber files exist in pcb/gerbers/ with JLCPCB extensions: GTL, GBL, GTO, GBO, GTS, GBS, GKO (minimum 7 required; pastes extra).
- X2 format preferred, RS-274X acceptable.
- Visual inspection 5 checks (Cu top/bot OK, Edge closed rectangle, Silk readable, Mask exposes pads): all PASS.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-163-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- pcb/gerbers/ directory committed (all Gerber files).
- `docs/pm/evidence/AF-163-gerber-summary.md` (file count = N; extensions = list correct; GerberViewer 5 visual checks PASS; X2 or RS-274X format noted)
- Optional: screenshot of Gerber Viewer with all layers loaded, saved as `docs/photos/condx-pcb/gerber-view-all-layers.png`
**Safety considerations:**
N/A — file export.
**Known uncertainties:**
Jiepei/JDBPCB Gerber name conventions: they accept JLCPCB naming (according to BOM research). If any variation needed, rename files with a short shell script (5 min). Always run the fab house's FREE online Gerber checker tool before submitting order (JLCPCB has "Quote Now → Add your Gerber files → auto checks DRC/naming"). Do this as part of Step 16 order.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Gerber Viewer shows traces chopped at edges (missing copper within 0.4 mm of board edge): Edge.Cuts too close to components / traces. Expand board outline 0.5 mm outward all around, regenerate. Solder mask covers pads: reversed F.Mask / B.Mask (renamed GTS/GBS wrong), swap file names or re-export with correct layer-to-extension mapping.
**Source references:**
- JIRA.md §Conditional PCB Work Step 12 "Gerber X2/RS274X + JLCPCB naming convention GTL/GBL/GTO/GTS/GBS/TXT/DRD/GKO + paste layers"
- JLCPCB Gerber naming guide (public help page: JLCPCB Preferred File Extension Names) — referenced in BOM research
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs purchasing
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-164: Step 13 Drill files + drill legend. Excellon format .DRL or .TXT. Drill legend PDF showing drill sizes and counts.

**Task ID:** AF-164
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 13 Drill files (Excellon .DRL or .TXT) + drill legend PDF (drill sizes × counts). Export from Pcbnew with matching tool numbers to Gerbers.
**Description:** Drill file tells the CNC drill machine in the fab what size holes to drill at each coordinate. Typical drills on our board: (1) 0.8 mm = standard signal/PTH vias (many, 20-50 count). (2) 3.3 mm = M3 clearance holes for corner mounting screws (4 count, NPTH or PTH). (3) 0.9 mm = DIP-20 component holes for HCT through-hole pins (U1/U2 × 40 = 40 count, PTH). (4) 1.0 mm = 2×20 header through-hole pins (80 count). (5) 1.5 mm = XT30 connector pins or screw terminal block pins (2 count). (6) 1.2 mm = decoupling C1/C2 TH ceramic if using through-hole (2 count) or C3 1000 uF pins (0.8 mm or 1.0 mm typical). Pcbnew generates all this automatically from footprint hole sizes.
Procedure Pcbnew → File → Fabrication Outputs → Drill Files (.drl):
- Output directory: same pcb/gerbers/ (alongside Gerbers; the factory expects all files in one zip).
- **Format:** Excellon (decimal format, 2:4 leading zeros or suppress leading zeros as per fab default; KiCad default "Millimeters / absolute / Format 3:3" = works for all Chinese fabs).
- **Drill origin:** Absolute or Auxiliary axis origin = board lower-left (same as Gerber origin; ALIGN with Gerber origin else drilled holes offset from pads).
- **Mirror:** No mirror.
- **Merge NPTH and PTH into single drill file: YES (simpler for fab, all holes one file).**
- Options: "Generate map file (PDF, SVG, POST, DXF)" → PDF preferred. Click "Generate Drill File".
- Also "Generate Report File" → drill_report.txt lists each tool size, count, plated/non-plated.
Verify pcb/gerbers/ now contains:
- `condx-hct-adapter.drl` (Excellon drill file; or .TXT extension, both fine)
- `condx-hct-adapter-drl_map.pdf` (drill map legend PDF: shows board outline, each drill location marked with symbol like Ø0.8, Ø3.3, and a legend box listing tool #, size, count)
- `condx-hct-adapter-drill_report.txt` (tool table: Tool #1 0.80mm PTH count 34, etc.)

Open drill map PDF. Inspect: 4 large Ø3.3 holes at corners = mounting. 2×40 + 2×80 TH component holes in the middle area. Via cluster 0.8 mm. All looks correct. Zip the entire pcb/gerbers/ folder (Gerbers + drill + map) into condx-hct-adapter-gerbers-v1.zip (ready for order Step 16; also uploaded to JLCPCB free DRC checker Step 16 before purchase).
**Why this task exists:** 90% of "I got my boards and the holes don't line up" = drill/Gerber origin misalignment. We merge NPTH/PTH, align origin, generate PDF map to verify hole locations/sizes before submitting. Drill report also lets us count the holes for sanity (should be ~80 PTH TH pins + 4 mounting + 30 vias ≈ 114 holes total).
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: Gerbers from AF-163 in pcb/gerbers/. Layout has all holes (vias + component + mounting).
**Blocked by:** ADR-016, AF-163.
**Required hardware:** None.
**Required tools:** None.
**Required software:** KiCad Pcbnew Drill Export, PDF viewer.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. Open Pcbnew final layout (from AF-162 / Gerbers exported AF-163).
3. File → Fabrication Outputs → Drill File. Output dir = pcb/gerbers/. Settings: mm, absolute, 3:3 format, merge NPTH/PTH YES, map format = PDF, generate drill report = YES. Generate.
4. Verify output files in pcb/gerbers/:
   - .drl or .TXT drill file: size non-zero (≥ 10 KB for 100 holes).
   - -drl_map.pdf opens.
   - -drill_report.txt opens, list of tools with sizes/counts.
5. **Inspect drill map PDF visually:**
   a. 4 × large circles (Ø3.3 mm) at corners = mounting holes. Correct positions.
   b. Medium clusters (Ø0.9 mm) in DIP-20 and header areas (40+80 pins = 120 holes). Correct.
   c. Small Ø0.8 mm via clusters. Correct.
   d. No holes outside Edge.Cuts board outline (except maybe test points not relevant; no outside).
6. **Zip Gerbers + drill:** `cd pcb/gerbers && zip ../condx-hct-adapter-gerbers-v1.zip *` (1 zip file for upload).
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Drill file (.drl/.TXT), drill map PDF, drill report TXT all present. Map shows correct 4 corner mount holes + TH component pattern. Zip ready.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- pcb/gerbers/condx-hct-adapter.drl (or .TXT) exists (Excellon drill, merged PTH+NPTH).
- pcb/gerbers/condx-hct-adapter-drl_map.pdf (drill legend, readable).
- pcb/gerbers/condx-hct-adapter-drill_report.txt (tool size/count table).
- Map inspection: 4 × M3 holes at corners, TH pattern matches DIP/header footprints, no off-board holes.
- Zip: pcb/condx-hct-adapter-gerbers-v1.zip exists (contains all Gerber + drill files).
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-164-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- Drill/map/report files committed in pcb/gerbers/.
- Zip file committed or available for upload.
- `docs/pm/evidence/AF-164-drill-summary.md` (files present; map inspection 4-point pass; tool count table e.g., "Total drills = 118, Ø3.3×4, Ø1.0×86, Ø0.8×24, Ø1.5×4"); zip file location.
**Safety considerations:**
N/A — file export.
**Known uncertainties:**
KiCad drill origin alignment: default auxiliary axis origin = same as page center in some legacy projects → holes offset 100 mm from Gerber → useless boards. Fix: in Pcbnew, set auxiliary axis origin to bottom-left of board outline (Edit → Grid and Origin → Set Auxiliary Axis Origin → click the board bottom-left corner) BEFORE exporting Gerber + Drill. Always re-set origin, then export BOTH Gerber and Drill in same session so origins match.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Origin mismatch obvious in drill map (board outline in PDF does NOT contain the holes; holes are at top-right of the PDF page instead of inside outline). Fix: set auxiliary axis origin correctly (bottom-left corner of Edge.Cuts outline), re-export BOTH Gerbers AND Drill (must re-export both together, not just drill), verify new map alignment. Missing drill file size tiny (0 holes): layout has zero vias / connectors with holes → forgot to place TH footprints (all SMD?), but HCT are DIP-20 so should be present; check footprint assignments.
**Source references:**
- JIRA.md §Conditional PCB Work Step 13 "Excellon drill .DRL/.TXT + drill legend PDF sizes/counts"
- JLCPCB help (how to submit Gerbers: Gerbers + drill in one zip, drill Excellon RS-274X compatible)
- AF-163 (Gerber export, shared folder with drill output)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs purchasing
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-165: Step 14 PCB BOM for JLCPCB/assembly house. CSV or XLSX with columns: Reference Designator, Value, Footprint, Manufacturer Part Number, LCSC Part Number (if JLCPCB SMT used) or JIEPEI equivalent. Align part selection with chosen fab house's in-stock library to avoid custom part fee. HCT245 = SN74HCT245N (DIP-20 through-hole is fine for prototype 5 boards; SMD SOIC-20 if doing SMD assembly to reduce hand-solder).

**Task ID:** AF-165
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 14 PCB BOM (CSV/XLSX). RefDes, Value, Footprint, MPN, LCSC/JIEPEI P/N. Align with fab in-stock library to skip custom part fee. HCT245 = SN74HCT245N DIP-20 TH or SOIC-20 SMD.
**Description:** The PCB BOM tells the assembly house (or you, if hand-soldering) exactly which parts go where. Columns: Reference Designator (U1, U2, C1, C2, C3, J1, J2, J3, H1, H2…), Value (SN74HCT245N, 100nF, 1000µF/10V, 2x8 HUB75, 2pin 5.08mm, ESP32-S3 headers 2x20), Footprint (DIP-20, 0805/0603 SMD or TH ceramic, Radial Polarized 10x12mm, HUB75 2x8 shrouded/keyed, 2pin 5.08mm screw, 2x20 header 2.54mm), Manufacturer Part Number (TI SN74HCT245N, etc.), LCSC Part Number (JLCPCB: search lcsc.com for "SN74HCT245N DIP-20" → copy LCSC# e.g. Cxxxxxx). If NOT using JLC SMT assembly (hand-solder all TH), the LCSC column is optional, but still useful for your own purchase. Key decision: HCT245 package = DIP-20 through-hole (easier hand-solder, prototype = fine, all 5 boards you solder yourself) OR SOIC-20 SMD (if you want JLC to assemble SMD parts, you hand-solder only TH ESP32 headers and HUB75). DIP-20 recommended for prototype unless you have prior SOIC solder experience.
**Why this task exists:** No fab order goes through without a BOM. LCSC part number match = JLC can pull the part from their own warehouse (no custom import fee, 1–3 day turn vs 1–2 week). Part alignment with their library avoids surprise fees.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: Schematic finalized from AF-154/155, all components placed AF-160 layout.
**Blocked by:** ADR-016, AF-155, AF-160.
**Required hardware:** None (doc task).
**Required tools:** None.
**Required software:** Spreadsheet app, KiCad schematic open (BOM export from Eeschema). LCSC.com or JIEPEI web for part number lookup.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. In KiCad Eeschema: Tools → Generate BOM. Use default "csv" plugin or "bom2grouped_csv" plugin. Output = pcb/condx-hct-adapter-bom-raw.csv.
3. Open raw CSV. Rename/reorder columns to target schema: RefDes, Value, Footprint, MPN, LCSC_PN.
4. **For every row, fill MPN + LCSC_PN (if using JLC):**
   a. U1, U2: Value = SN74HCT245N. MPN = Texas Instruments SN74HCT245N (DIP-20) OR SN74HCT245NSR (SOIC-20). LCSC: search lcsc.com → copy LCSC#.
   b. C1, C2 (100nF decoupling): MPN = generic 0805/0603 SMD 100nF X7R 50V OR TH ceramic MLCC 100nF 2.54mm pitch. LCSC = pick in-stock basic part (LCSC has thousands).
   c. C3 (1000µF 10V+ if placed): MPN = radial polarized aluminum e.g., Rubycon or generic Chinese. LCSC = pick in-stock 10x12mm or 8x11mm.
   d. J1 (HUB75 2x8 keyed shrouded header): MPN = standard 2x8 2.54mm shrouded with key / polarization bump. LCSC = search "2x8 shrouded header keyed".
   e. J2 (5V power 2pin 5.08mm screw terminal): MPN = standard 2-pin 5.08mm pitch rising-clamp screw. LCSC = pick in-stock.
   f. J3 (ESP32 headers 2x20): MPN = 2x20 2.54mm straight female header socket (for ESP32 plug-in) OR male headers (if you solder ESP32 directly). LCSC = standard.
   g. H1-H4 (M3 mounting holes): no BOM line (mechanical only).
5. **Save final BOM:** pcb/condx-hct-adapter-bom.csv. If JLC SMT assembly ordered: also save as XLSX per JLC upload template.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: BOM file (CSV/XLSX) with all rows filled, LCSC part numbers present if using JLC assembly. Parts match schematic RefDes.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- pcb/condx-hct-adapter-bom.csv exists with columns RefDes, Value, Footprint, MPN, LCSC_PN (or blank LCSC if hand-solder only).
- U1, U2 rows present with HCT245 package choice explicitly stated (DIP-20 or SOIC-20).
- C1, C2 decoupling rows present (100nF each).
- C3 row present (or explicit "NOT POPULATED per Step 5" with reason).
- J1 HUB75, J2 power, J3 ESP32-header rows present.
- LCSC PN cells (if used) = valid LCSC format numbers (C + digits).
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-165-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- pcb/condx-hct-adapter-bom.csv committed.
- Optional: pcb/condx-hct-adapter-bom.xlsx (JLC format).
- `docs/pm/evidence/AF-165-bom-summary.md` (HCT package choice stated; total unique part count; hand-solder vs SMT-assembly decision; LCSC availability verified).
**Safety considerations:**
N/A — document/BOM task.
**Known uncertainties:**
JLC SMT assembly fee minimum: they charge a setup fee (~¥50–¥150) for SMT service even on 5 boards + cost of parts. DIP-20 hand-solder avoids SMT fee entirely (you solder U1/U2/connectors — 2×20 pins + TH = ~100 solder joints, ~2 hours, acceptable for prototype 5 boards). Document your make/buy choice.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** LCSC part not in stock → search equivalent part on LCSC with same footprint + value (generic 0805 100nF = hundreds of alternates). HCT245 DIP-20 OOS → fall back to SOIC-20 + JLC SMT or find alternate supplier.
**Source references:**
- JIRA.md §Conditional PCB Work Step 14 "PCB BOM CSV/XLSX RefDes/Value/Footprint/MPN/LCSC"
- BOM.md §Fabrication services researched (JLCPCB/Jiepei/JDBPCB library coverage)
- PIN_LEVEL_APPENDIX §2 HCT245 pinout (U1/U2 package type)
- AF-155, AF-156 (component types to list)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs purchasing
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-166: Step 15 CPL (Component Placement List) if ordering assembled boards via SMT. CSV with RefDes, X (mm), Y (mm), Rotation (deg), Side (Top/Bottom). Generated from Pcbnew via File → Fabrication Outputs → Component Placement (.pos).

**Task ID:** AF-166
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 15 CPL (.pos) export from Pcbnew. CSV: RefDes, Xmm, Ymm, Rot deg, Side (Top/Bot). Only if ordering JLC/Jiepei SMT assembled boards; hand-solder TH → SKIP this step (but still generate empty-skipped evidence).
**Description:** SMT assembly = the fab house places SMD components (SOIC-20 HCTs, 0805/0603 caps) on your board using a pick-and-place robot. That robot needs X/Y coordinates, rotation angle, and top/bottom side for every SMD RefDes. This is the CPL = Component Placement List = .pos file. If you are hand-soldering ALL parts (DIP-20 TH recommended prototype), you don't need CPL; but to keep consistency, this task still runs and produces evidence that SMT-was-not-chosen.
**Why this task exists:** JLCPCB SMT assembly order page requires 3 uploads: (1) Gerber zip, (2) BOM (CSV/XLSX), (3) CPL (CSV). Missing CPL = cannot complete SMT order.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: IF AND ONLY IF AF-165 chose SMD package (SOIC-20 HCTs + SMD decoupling caps). IF hand-solder DIP-20 ALL TH → this task produces skip-evidence and no .pos.
**Blocked by:** ADR-016, AF-160 (final layout), AF-165 (BOM package decision known).
**Required hardware:** None.
**Required tools:** None.
**Required software:** KiCad Pcbnew (CPL export). Spreadsheet if reformatting.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule.
2. **SUB-DECISION within this task:** Check AF-165 BOM summary: are ANY parts SMD?
   a. **IF ALL parts TH (DIP-20, TH ceramics, TH connectors):** → SMT = NOT USED. Write skip-evidence file; this task DONE (no .pos export needed). Go to Step 5 (evidence save).
   b. **IF ANY parts SMD (SOIC-20 HCTs, 0805/0603 caps, etc.):** → proceed to export CPL.
3. Open Pcbnew final layout. File → Fabrication Outputs → Component Placement (.pos). Settings:
   - Format: CSV (comma separated, NOT ASCII).
   - Units: Millimeters.
   - Files: "Single file for board" (not separate top/bottom).
   - Include footprints with "virtual" attribute: no.
   - Output directory: pcb/gerbers/ (alongside Gerber/Drill — one upload zip).
   - Click "Generate Position File".
4. Open generated pcb/gerbers/condx-hct-adapter-pos.csv (or .pos). Verify columns match JLC CPL format header: "Designator,Val,Package,Mid X,Mid Y,Rotation,Layer". If different columns (KiCad default "Ref,Val,Package,PosX,PosY,Rot,Side"), rename header row to JLC naming: Ref→Designator, PosX→Mid X, PosY→Mid Y, Rot→Rotation, Side→Layer. Side values: KiCad writes "top"/"bottom" → JLC wants "Top Layer"/"Bottom Layer" — do the string replacement.
5. Save final: pcb/gerbers/condx-hct-adapter-cpl.csv.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Either (a) ALL-TH → skip-evidence with stated reason, or (b) SMD → CPL CSV in gerbers/ dir, columns JLC-compatible, all SMD RefDes present.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32 — Case A (ALL TH hand-solder):**
- Reclassified.
- Skip evidence file states: "AF-165 BOM confirmed ALL parts through-hole. No SMD components. SMT assembly not used. CPL Step 15 SKIPPED."
**POST/ESP32 — Case B (SMT used):**
- Reclassified.
- pcb/gerbers/condx-hct-adapter-cpl.csv exists.
- Header row: Designator,Val,Package,Mid X,Mid Y,Rotation,Layer (JLC naming).
- All SMD RefDes (at minimum: U1, U2, C1, C2 if SMD) present as rows.
- Mid X / Mid Y values = plausible (0–100 mm range, inside board outline 0..<100).
- Layer column values = "Top Layer" (all our parts top-side; no bottom-side parts expected).
- Rotation values = 0 or multiples of 90; SOIC-20 pin 1 orientation matches silkscreen.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-166-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32 Case A (ALL TH):**
- `docs/pm/evidence/AF-166-cpl-skip-th.md` (reason = all TH; no SMT).
**POST/ESP32 Case B (SMT):**
- pcb/gerbers/condx-hct-adapter-cpl.csv committed.
- `docs/pm/evidence/AF-166-cpl-summary.md` (SMD part count listed; JLC column header match confirmed).
**Safety considerations:**
N/A — file export.
**Known uncertainties:**
KiCad .pos default layer naming "top" vs JLC "Top Layer". Easy string replace. If your board has both SMD and TH parts: CPL ONLY lists SMD (TH are inserted by hand post-fab or wave-solder, not pick-place). So if only HCTs are SOIC (SMD) but connectors TH, CPL has only U1, U2, C1, C2 rows. That's correct.
**Failure response:**
**PRE/WF4:** SKIP. **POST SMT CASE:** .pos file empty → layout has 0 SMD footprints (all TH) → actually you should be in Case A. Wrong columns → rename in spreadsheet. Missing RefDes in CPL → footprint has attribute "Virtual" in Pcbnew footprint properties → uncheck Virtual.
**Source references:**
- JIRA.md §Conditional PCB Work Step 15 "CPL Component Placement List .pos from Pcbnew if SMT"
- JLCPCB SMT assembly help (3-file upload: Gerbers + BOM + CPL; CPL column specs)
- AF-165 (package decision: ALL-TH vs any-SMD)
**Labels:** hardware pcb conditional blocked blocked:adr-016 docs purchasing
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-167: Step 16 Prototype manufacture ORDER. 5 boards minimum. Specs per BOM note: 2-layer FR-4, 1.6 mm board thickness, 1 oz Cu finished weight, green solder mask, white silkscreen, HASL lead-free finish, <100×100 mm (if possible — else minimum size that fits). Service chosen from BOM.md research (JLCPCB / Jiepei / JDBPCB). Order confirmation screenshot saved.

**Task ID:** AF-167
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 16 ORDER 5 prototype boards. 2-layer FR-4, 1.6mm, 1oz Cu, green mask, white silk, HASL lead-free. Fab from BOM research list. Confirmation screenshot saved.
**Description:** Place the PCB fabrication order. 5-board minimum (the JLCPCB classic $2 / 5 boards price tier applies IF board <100×100 mm). If your board >100×100 mm (AF-160 documented), accept the higher cost (still <¥100 for 5 boards 2-layer). Specs per BOM §Fabrication services researched notes: 2-layer, FR-4, 1.6 mm standard thickness, 1 oz copper (finished weight, not base foil), green solder mask, white silkscreen, HASL lead-free surface finish. No gold fingers, no impedance control, no blind/buried vias, no castellated holes — all standard. Upload zip from AF-163 (plus drill files AF-164). If SMT assembly also ordered (Case B AF-165/166), upload BOM + CPL in SMT step. Service: pick one of JLCPCB (Shenzhen Jialichuang) / Jiepei (JIEPEI) / JDBPCB per BOM.md research entry (Jiepei typically cheapest for small prototype 5-boards without account coupon; JLC best if you also do SMT assembly same factory).
**Why this task exists:** We need physical PCB boards for Step 17. Without this purchase, Cond-X cannot move past design to validation.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: All files from AF-163 (Gerbers), AF-164 (Drill), AF-165 (BOM), AF-166 (CPL if SMT) present and committed.
**Blocked by:** ADR-016, AF-163, AF-164, AF-165, AF-166.
**Required hardware:** Payment method for fab order.
**Required tools:** None.
**Required software:** Web browser. Upload zip. BOM/CPL attachments if SMT.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. Choose fab house from BOM.md §Fabrication services researched (3 options). Open their website. Register/login if needed.
3. Start new PCB order:
   a. Upload condx-hct-adapter-gerbers-v1.zip (Gerbers + drill from AF-164).
   b. Wait for their online Gerber viewer to render the board. Verify in viewer: 4 corner mounting holes, board outline <100×100 (or documented larger size), no missing copper layers, drill holes visible. If viewer shows errors → download error report, fix in AF-160/163, re-zip, re-upload.
   c. **Select order parameters VERBATIM:**
      - Layers: 2
      - Base material: FR-4 TG130-140 (standard / default)
      - Board thickness: 1.6 mm
      - Copper weight: 1 oz (finished)
      - Solder mask color: Green
      - Silkscreen color: White
      - Surface finish: HASL (lead-free)
      - Gold fingers: No
      - Impedance control: No
      - Blind/buried vias: No
      - Castellated holes: No
      - Flying probe test: Yes (default; adds tiny cost, catches open/short factory defects)
      - Quantity: 5 (minimum)
      - Build time: Standard 48h or 24h (cheapest option; no rush needed for prototype)
   d. Add SMT assembly service IF Case B AF-165: select "SMT assembly" option, # assembled boards = 2 or 5 (your choice; minimum 2), both sides = top-side only, upload BOM CSV + CPL CSV. Match parts in their BOM validator (approve substitutions if LCSC out of stock, or pick in-stock alternate from list).
4. Add to cart. Checkout. Pay.
5. **Save order confirmation:** Screenshot the payment-success / order-detail page showing: order number, date, fab house, qty=5, specs as above, total price, status=Paid/Processing. Save PNG. Also save the order email (PDF).
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Order placed. Order #, confirmation screenshot, email saved. ETA from fab displayed.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- Fab order successfully placed (status: Paid or Processing in fab portal).
- Order parameters match all VERBATIM specs (2L, FR-4, 1.6mm, 1oz, Green mask, White silk, HASL lead-free).
- Quantity ≥ 5 boards.
- Order confirmation screenshot file exists (shows order # + specs + price + paid status).
- Order confirmation email saved (PDF or screenshot).
- If SMT assembly added: BOM/CPL validated by fab portal; approved part substitutions documented.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-167-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `docs/pm/evidence/AF-167-order-confirmation.png` (screenshot of order page with all details visible)
- `docs/pm/evidence/AF-167-order-summary.md` (fab house name, order #, date paid, total price, qty=5, specs verbatim confirmed, SMT yes/no + # boards assembled if yes, expected ship date from portal)
- Optional: order email PDF in same folder.
**Safety considerations:**
Purchasing / payment. Standard online purchase PCI risks: use credit card or trusted payment method (fab houses are reputable Chinese payment integrators Alipay/WeChat or international Stripe/PayPal; no issues reported in BOM research). Do not wire money to personal accounts.
**Known uncertainties:**
Chinese fab holiday closures (Chinese New Year ~Feb, National Day ~Oct) add 1–2 weeks. Portal will display actual build + ship ETA; record that. Border/customs delay for international shipping if not using DHL/FedEx expedited.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Gerber viewer flags errors → read error message, fix layout (usually unconnected copper pour, missing drill legend, annular ring too small for via), re-export Gerber+Drill (AF-163+164), re-upload. Payment fails → try different card or PayPal. Out of stock at fab #1 → try fab #2 or #3 from BOM list.
**Source references:**
- JIRA.md §Conditional PCB Work Step 16 "Prototype manufacture ORDER 5 boards, specs: 2L FR-4 1.6mm 1oz green white HASL lead-free, JLC/Jiepei/JDBPCB"
- BOM.md §Fabrication services researched (JLCPCB / Jiepei / JDBPCB links, pricing, min qty, SMT capability)
- AF-163 (Gerber zip), AF-164 (drill), AF-165 (BOM), AF-166 (CPL if SMT)
**Labels:** hardware pcb conditional blocked blocked:adr-016 purchasing
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-168: Step 17 PCB receipt + every-net continuity test. Physically received 5 PCBs. Take 1 PCB sample. Verify with multimeter continuity mode EVERY net in schematic. List each net by name; confirm end-to-end beep. VCC↔GND MUST NOT beep (no power-plane short). Any open net or short = reject that PCB; if multiple boards fail same net = design issue → return to Step 9/10 fix layout, reorder. At least 3 of 5 PCBs pass all continuity = batch acceptable.

**Task ID:** AF-168
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 17 RECEIVED + every-net continuity. 1 PCB sample, multimter continuity on EVERY net. VCC↔GND NO-BEEP. ≥3/5 boards pass = batch OK. Fail same net on multi = design bug → reorder.
**Description:** Physical PCBs arrive from fab. Before ANY soldering or population, we verify one sample board (Board #1 of 5) end-to-end continuity. This catches factory defects: broken traces, drill misregistration that broke a trace, solder mask splash short, internal layer short. Schematic net list: enumerate ALL unique net names from KiCad Eeschema (use Eeschema Tools → Edit Symbol Fields → list, or read netlist file directly). Typical nets for our board: +5V, GND, ESP32_GPIO_xx_to_HCT_U1_A1, HCT_U1_Y1_to_HUB75_R1, HUB75_ADDR_A, HUB75_CLK, HUB75_LAT, HUB75_OE, etc. List each; probe both ENDPOINTS (not mid-trace; both component pad sides). Expect beep. Then special: VCC (+5V net) to GND net → NO BEEP (short would destroy everything). If Board #1 passes, do a 10-net spot-check on boards #2 and #3 (skip exhaustive repeat if #1 fully passes). Count pass boards: need ≥3 of 5.
**Why this task exists:** 3-5% of cheap Chinese prototype PCBs have a factory defect. You DON'T discover it until after soldering, at which point debugging is misery ("did I solder bad or is the board broken?"). Pre-population continuity = 10 minutes of work = eliminates entire class of debugging pain.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: Physical delivery of 5 PCBs from AF-167 order. Package opened, all 5 boards visually inspected (no obvious shipping damage, no broken corners).
**Blocked by:** ADR-016, AF-167 (physical PCB delivery — BLOCKED: DELIVERY of PCB parcel).
**Required hardware:** 5 bare PCBs received. Multimeter. Schematic printout or 2nd screen with net list.
**Required tools:** Multimeter with continuity/beep mode (200Ω or diode mode with beep). Fine-tip probes preferred (0.8mm tip or smaller) to land on 0.8mm SMD pads if present; DIP pads are easy with any probe.
**Required software:** KiCad schematic open on 2nd screen for net name lookups.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed upon physical PCB delivery.
2. **Short test FIRST (before any other):** Multimeter continuity mode. Touch one probe to ANY +5V pad (power connector J2 VCC pin, or HCT245 U1 pin 20 VCC pad, or ESP32 header 5V pin pad — they are the SAME net). Touch other probe to ANY GND pad (J2 GND pin, U1 pin 10 GND, ESP32 header GND pin). Result: **NO BEEP**. If beeps → SHORT → this board BAD. Go to step 6 (reject).
3. **Enumerate all nets:** In KiCad Eeschema: Inspect → Netlist Browser. Copy all net names into a table template (3 columns: Net name, Pass/Fail, Notes).
4. **For each net in list:** Locate its two ENDPOINTS (component pads). Probe endpoint A with red probe, endpoint B with black probe. **BEEP = PASS.** (If net connects 3+ pads (e.g., GND plane, +5V pour): probe 3+ representative pads, all should beep to each other.) Mark Pass in table.
5. **Board #1 exhaustive done.** Boards #2, #3: do spot-check of 10 critical nets (VCC to U1 pin20, GND to U1 pin10, U1 outputs → HUB75 pins, ESP32 GPIO → U1 inputs). All pass = those boards OK. Boards #4, #5: visual only unless #1/#2/#3 had issues.
6. Evaluate batch: Count PASS boards. **≥ 3 of 5 pass = batch ACCEPTABLE.** Use passing boards for AF-169 (1 board) and spares. **< 3 pass** OR any multi-board fails on the same net → DESIGN BUG, not factory defect. Return to AF-160 (layout) / AF-154 (schematic) to find the bug. Fix. Re-export Gerber/Drill. Reorder (AF-167 again, new cost).
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Short test NO-BEEP. ≥ 3 of 5 bare PCBs pass all their continuity tests (exhaustive for #1, spot for #2/#3).
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- 5 bare PCBs physically in hand (photo evidence package opened).
- Board #1: VCC↔GND continuity = NO BEEP (short test PASSED).
- Board #1: Every schematic net (exhaustive list) = BEEP from end-to-end. No opens.
- Boards #2, #3: 10-net spot-check all pass.
- Batch pass count ≥ 3 / 5.
- If ANY multi-board identical-failure net: design bug documented, escalate path to AF-160 fix + reorder started.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-168-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `docs/pm/evidence/AF-168-pcb-delivery-photo.jpg` (all 5 boards on desk, fab packaging visible)
- `docs/pm/evidence/AF-168-continuity-table.md` (filled table: each net name × board #1 result Pass/Fail; VCC-GND-short-test line explicit "NO-BEEP PASS"; boards #2/#3 spot lines)
- `docs/pm/evidence/AF-168-batch-verdict.md` (pass count = X / 5, verdict = ACCEPT or REJECT + if REJECT: design bug net name + reorder plan)
**Safety considerations:**
⚠️ Bare PCB handling: ESD precaution. Touch a grounded metal object before handling (PCBs have no semis yet = low risk, but habit). PCB edges have FR-4 glass fiber = wash hands after extended handling to remove glass dust. No copper splinters in fingers.
**Known uncertainties:**
"Every net" for a 2-layer board with pours: GND net has HUNDREDS of connected pads (the entire pour). Don't test every via; test representative corners + each component GND pin (U1 pin10, U2 pin10, J2 GND, HUB75 GND pin, ESP32 headers GND pins — 6 probes is enough for GND plane). Same for +5V pour if used.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** VCC↔GND BEEP on Board #1 → visually inspect for solder bridges/copper whiskers between adjacent VCC/GND pads (look with magnifier at J2 power connector first; sometimes fab leaves tiny copper sliver). Bridge found → scrape off with hobby knife, re-test. If NO visible bridge → internal short from factory. Board #1 BAD. Same short on Board #2 → DESIGN BUG: schematic accidentally connected VCC-GND or layout has overlapping pour → fix schematic/layout, reorder. Open net (no beep) on Board #1 but #2 passes → factory drill defect (Board #1 only → throw it away, use #2-#5, count still ≥3/5). Open net same on #1+#2 → layout trace broken → fix AF-160, re-export AF-163/164, reorder AF-167.
**Source references:**
- JIRA.md §Conditional PCB Work Step 17 "PCB receipt + every-net continuity, VCC-GND NO BEEP, ≥3/5 pass"
- AF-159 (ERC) + AF-161 (DRC) should have caught schematic/layout errors; this catches what ERC/DRC missed
- AF-167 (order — BLOCKED until physical delivery)
**Labels:** hardware pcb conditional blocked blocked:adr-016 blocked:delivery validation
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-169: Step 18 Populated one-row hardware validation. Populate 1 PCB (U1, U2, decoupling, HUB75 connector, power connector, ESP32 headers if socket). Solder per steps from perfboard. Same 12-stage build verification (AF-054..AF-065 procedure referenced). Then connect to 256×64 row (2 panels) as EXP-012. Run Standard Test Pattern Suite. Stable ≥1 hour. No perfboard in signal path anywhere.

**Task ID:** AF-169
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 18 POPULATE + VALIDATE 1 PCB. Solder components (U1/U2/C1/C2/J1/J2/J3). 12-stage build-verify AF-054..AF-065. Connect 256×64 2-panel row. Standard Test Pattern Suite. Stable ≥ 1 hour. No perfboard anywhere in signal path.
**Description:** Take one passing bare PCB from AF-168. Populate it with all components exactly as the perfboard build was done, but using the PCB instead. Component list: (1) U1 = SN74HCT245N (DIP-20 or SOIC-20 per AF-165 choice), (2) U2 = SN74HCT245N, (3) C1 = 100nF decoupling (across U1 pin20/pin10, within 5mm), (4) C2 = 100nF decoupling (across U2 pin20/pin10, within 5mm), (5) C3 = 1000µF/10V+ bulk cap if footprint placed, (6) J1 = HUB75 2×8 keyed shrouded header (PANEL-IN arrow silkscreen direction matches verified perfboard), (7) J2 = 5V power 2-pin 5.08mm screw terminal, (8) J3 = ESP32 2×20 female header socket (so ESP32 plugs in) OR male headers (direct solder). Solder in build stages with per-stage continuity verification EXACTLY LIKE the perfboard 12-stage AF-054..AF-065 procedure (reuse that 12-step solder→continuity→next pattern; do NOT skip per-stage verification). After fully populated: plug in ESP32-S3 devkit (same one validated in EXP-011/EXP-012 perfboard setup). Disconnect the perfboard HCT adapter from your EXP-012 2-panel test rig. Connect the new PCB HCT adapter in its EXACT place: same HUB75 cable, same 5V power connection, same ESP32 (reflash firmware if needed — should be identical firmware). Run the entire Standard Test Pattern Suite (the same suite from AF-090/AF-085). Stable hold ≥ 1 hour. CRITICAL: perfboard is NOT in the signal path at all during this test (not daisy-chained, not parallel — remove perfboard completely from the bench to prove the PCB works standalone).
**Why this task exists:** Proves the PCB design is functionally identical to the validated perfboard design (same signal routing, no introduction of new artifacts). This is the populated-board-validation step that the Reclassification rule in every Cond-X task references as the critical-path cutoff.
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: ≥1 passing bare PCB from AF-168, all BOM components in hand (U1/U2 ICs, caps, connectors, headers — may need to purchase if BOM parts not already in inventory). ESP32-S3 from EXP-011 available. Same 2-panel 256×64 EXP-012 test rig intact.
**Blocked by:** ADR-016, AF-168, AF-065 (perfboard 12-stage validation procedure exists as the reference to follow), AF-090 (Standard Test Pattern Suite defined, 2-panel stable baseline exists to compare against).
**Required hardware:** 1 passing bare PCB. Components: 2× SN74HCT245N (U1/U2), 2× 100nF C1/C2, C3 if used, J1 HUB75 keyed 2×8, J2 2-pin 5.08mm, J3 2×20 ESP32 socket. ESP32-S3 devkit. Solder (63/37 or lead-free 0.8mm). 2× HUB75 cables (same ones used in EXP-012). 2 panels (P2.5-128×64) from EXP-012. 5V PSU + panel harnesses.
**Required tools:** Soldering iron (temperature-controlled, 320–360°C). Desoldering pump + wick (if mistakes). Multimeter continuity mode. Same 12-stage build continuity checklists from AF-054..AF-065. Stopwatch 1 hr.
**Required software:** ESP32 firmware from EXP-011/EXP-012 (same .bin or Arduino sketch). Nano transport from EXP-013 to drive patterns. Standard Test Pattern Suite generator from AF-080/AF-090.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. **REMOVE PERFBOARD FIRST:** In your EXP-012 2-panel test rig: disconnect perfboard HCT adapter completely. Place it in a different room or at least in its anti-static bag on the far side of the bench (physical proof it is not in signal path).
3. **Populate PCB per 12-stage procedure AF-054..AF-065:** Copy the EXACT stage order from those tasks (Stage 1 = solder U1 socket or pins, continuity per pin; Stage 2 = U2; Stage 3 = C1, C2; Stage 4 = C3; Stage 5 = J2 power; Stage 6 = J3 ESP32 headers; Stage 7 = J1 HUB75; Stages 8–12 = full per-net continuity, VCC-GND no-short, power-up 5V check, no-ESD, ESP32 plug-in check, firmware flash check). Do NOT compress stages. Do NOT skip per-stage continuity.
4. After full population: Plug ESP32-S3 into J3 socket (or press headers into breadboard if direct solder not used).
5. Connect: J2 5V to PSU 5V output posts (same branch). J1 HUB75 out → panel #1 IN → panel #1 OUT → panel #2 IN (same 2-panel chain from EXP-012, exactly same cables, same orientation).
6. PSU ON. Verify 5V at J2 posts = 5.00–5.10 V. No magic smoke.
7. Flash ESP32 with EXP-012 firmware. Start Nano test pattern driver.
8. Run EVERY pattern in the Standard Test Pattern Suite. Each pattern: visual compare against EXP-012 perfboard baseline photos (from AF-089/AF-090). Expected: IDENTICAL behavior (no new artifacts, no missing rows, no color shifts).
9. Set a stable pattern (e.g., full-white or gradient). Start 1-hour timer. Monitor every 10 minutes. No garble, no blank, no reset.
**Expected result:** PRE/WF4: SKIP. POST/ESP32: PCB fully populated, all 12 build stages pass continuity, Standard Test Pattern Suite all visually match perfboard baseline, 1-hour stable hold with zero issues.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- 12-stage build checklist (AF-054..AF-065 procedure) fully executed with all continuity beeps and DoD per stage.
- VCC↔GND short test = NO-BEEP at all stages.
- Post-population: PSU 5V at PCB = 5.00–5.10 V.
- Perfboard NOT connected / NOT in signal path (photo evidence: perfboard in bag visible far from test rig).
- Standard Test Pattern Suite ALL patterns run (same list AF-090).
- Visual comparison: every pattern matches EXP-012 perfboard baseline photos (no new glitches/artifacts).
- 1-hour stable hold: no blank, no garbled row, no ESP32 reset.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-169-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `docs/pm/evidence/AF-169-12stage-checklist.md` (copy of AF-054..AF-065 checklist with all PASS/FILL filled for this PCB build)
- `docs/pm/evidence/AF-169-populated-photo-top.jpg` (PCB top, all components visible, soldering OK)
- `docs/pm/evidence/AF-169-populated-photo-bottom.jpg` (solder joints visible)
- `docs/pm/evidence/AF-169-perfboard-off-bench.jpg` (perfboard in bag on far side, test rig running on PCB only — proof no perfboard in path)
- `docs/pm/evidence/AF-169-test-patterns-comparison.md` (each pattern line: baseline perfboard photo URL → PCB photo URL, verdict = MATCH)
- `docs/pm/evidence/AF-169-1hr-stable-photo.jpg` (final pattern photo after 60 min timestamp visible)
**Safety considerations:**
⚠️ Soldering safety: fume extraction (fan or hood), eye protection, hot iron never rest on bench. ⚠️ 5 V high-current rule (JIRA Safety Rules 4/4). NO Dupont for 5 V power path. J2 = screw terminal + ferrule crimped wires. ⚠️ No HUB75 hot-plug (JIRA Safety Rules 2/2). Power order when changing HUB75: PSU OFF → wait 10 s → plug/unplug HUB75 → PSU ON. Do NOT yank HUB75 with power on.
**Known uncertainties:**
If DIP-20 socket used for HCT ICs (instead of directly soldering IC): ensure socket pins fully seated before soldering socket to PCB, then press IC into socket with pin-1 alignment. SOIC-20 SMD solder: if SMD assembly by fab, skip hand-solder U1/U2 but still verify continuity across their pins before applying power.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Build-stage continuity fail → cold solder joint / solder bridge → reheat / wick bridge / resolder joint. Pattern comparison shows NEW artifact (not in perfboard baseline) → layout design bug: trace too long, missing via, stiching missing, U1/U2 channel swapped compared to perfboard validated mapping. Compare PCB net by net against perfboard wiring schematic (AF-153 validated mapping). Find discrepancy. If layout bug: fix AF-160, reorder (AF-163/164/167 again), v1.1 board. 1-hour stable fails (reset mid-run) → thermal on PCB or 5V drop: check U1/U2 case temp (hand), check 5V at J2 under full pattern load (≥4.75V).
**Source references:**
- JIRA.md §Conditional PCB Work Step 18 "Populated 1-row hardware validation, 12-stage AF-054..AF-065, Standard Test Pattern Suite, stable 1hr"
- AF-054..AF-065 (12-stage perfboard procedure — reused VERBATIM for PCB build stages)
- AF-090 (Standard Test Pattern Suite list + 2-panel baseline photos to compare)
- AF-153 (validated GPIO mapping: PCB must MATCH this, no deviations from validated perfboard wiring)
**Labels:** hardware firmware pcb conditional blocked blocked:adr-016 validation controller-esp32 power
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

### AF-170: Step 19 Perfboard REPLACEMENT swap. Remove old perfboard HCT adapter from EXP-012/EXP-013 test setup. Install PCB adapter in EXACT same physical location (same HUB75 cable same lengths, same power connection). Re-run Standard Test Pattern Suite on 256×64 row. Confirm IDENTICAL behavior to perfboard baseline (same patterns, same refresh, no new artifacts). Update all wiring docs references from "perfboard HCT adapter" → "v1.0 PCB HCT adapter", add photo of PCB in-situ.

**Task ID:** AF-170
**Epic:** AF-010 (Cond-X: Conditional ESP32 Custom PCB)
**Summary:** Cond-X PCB Step 19 SWAP: perfboard OUT, PCB IN at same location, same cables. Standard Test Pattern Suite re-run = IDENTICAL to perfboard baseline. All docs rename "perfboard HCT" → "v1.0 PCB HCT". In-situ photo added.
**Description:** The final Cond-X step: the PCB adapter graduates from "validation item on the side" to "the actual HCT adapter in our test rig". Physically remove the perfboard HCT adapter that has been used since EXP-011. Install the new v1.0 PCB HCT adapter in its EXACT same physical location: use the same HUB75 cables (do not swap for different-length cables; same lengths = same signal timing). Same 5V power branch (same wire length to PSU posts). Same ESP32 devkit (if socket works with same devkit). Re-run the full Standard Test Pattern Suite on 256×64 2-panel row. Confirm IDENTICAL behavior to the perfboard baseline recorded in AF-089/AF-090: same visual patterns, same measured refresh rate (if logged), same stability, no new artifacts of any kind. Then: documentation sweep — find every place in PROTOTYPE_WIRING.md, PIN_LEVEL_APPENDIX.md, EXPERIMENTS.md, BOM.md, and task evidence files that says "perfboard HCT adapter" or "perfboard build" or equivalent — replace with "v1.0 PCB HCT adapter (Cond-X AF-170)". Add a photo of the PCB installed in the test rig to docs/photos/ with a clear caption linking to AF-170.
**Why this task exists:** Formalizes the PCB as the canonical HCT adapter. After this step, the perfboard is RETIRED (can keep as backup/reference but no longer the primary). The documentation matches current hardware state (prevents future confusion: "but wiring docs still say perfboard!").
**Prerequisites:** PRE/WF4 → SKIP. POST/ESP32 only: AF-169 passed (populated PCB validated, stable 1 hr). EXP-012 test rig with perfboard still in place (perfboard baseline accessible, photos from AF-090).
**Blocked by:** ADR-016, AF-169.
**Required hardware:** EXP-012 test rig (2 panels, PSU, Nano, ESP32). Validated PCB from AF-169. The exact HUB75 cables and power wires currently connected to perfboard.
**Required tools:** Screwdriver for HUB75 screws (if panel connectors screw-lock). Multimeter (quick continuity after swap if issues).
**Required software:** Standard Test Pattern Suite driver (same as AF-169 / AF-090). All documentation markdown files open for edits.
**Exact execution steps:**
1. **CONDITION CHECK:** WF4 → SKIP. Multi-ESP32 → reclassify per VERBATIM rule. Proceed.
2. PSU OFF. Wait 10 s (caps discharge).
3. **REMOVE perfboard:** Disconnect: (a) ESP32 from perfboard HCT, (b) HUB75 cables from perfboard J1, (c) 5V power wires from perfboard power connector. Set perfboard aside on anti-static mat (do not throw away — keep as backup reference. Label it "RETIRED: Perfboard HCT v0.1, replaced by PCB v1.0 AF-170").
4. **INSTALL PCB v1.0 IN EXACT SAME LOCATION:** Physically place the PCB where the perfboard was.
   a. Connect SAME HUB75 cables: the cable that used to go to perfboard J1 → now goes to PCB J1. Same orientation (red stripe pin 1 = same).
   b. Connect SAME 5V power wires: wires from PSU that went to perfboard power connector → now connect to PCB J2 (same V+/V- polarity, verify before tightening).
   c. Plug SAME ESP32 devkit into PCB J3 socket (same one).
5. Double-check: HUB75 cable lengths same as before (no shorter/longer). Power branch wire length identical. No new components.
6. PSU ON. 5V at J2 posts = 5.00–5.10 V. No magic smoke.
7. Run FULL Standard Test Pattern Suite. For each pattern: compare side-by-side with AF-090 perfboard baseline photo. Expected: IDENTICAL (pixel-for-pixel same, no new artifacts).
8. Optional: run 10 min extended stability (already passed 1 hr in AF-169; short confirm OK).
9. **DOCUMENTATION SWEEP:** Search-replace across docs:
   a. PROTOTYPE_WIRING.md: every "perfboard HCT" → "v1.0 PCB HCT adapter (AF-170)"
   b. PIN_LEVEL_APPENDIX.md: if references perfboard wiring → update.
   c. EXPERIMENTS.md EXP-011/EXP-012/EXP-013 sections: add a note "Post-AF-170: perfboard adapter replaced by v1.0 PCB; all behavior identical."
   d. BOM.md: if perfboard listed → mark perfboard adapter "(RETIRED after AF-170; replaced by PCB v1.0, Qty needed 3 for 3-row build)".
10. Add in-situ photo to docs/photos/: `condx-pcb-v1-insitu-2panel-row.jpg` (caption: "AF-170: v1.0 PCB HCT adapter installed in 256×64 2-panel row test rig, replacing perfboard.")
**Expected result:** PRE/WF4: SKIP. POST/ESP32: Swap successful. Standard Test Pattern Suite all patterns IDENTICAL to perfboard baseline. Documentation sweep complete. Perfboard retired.
**Acceptance criteria / DoD:**
**PRE/WF4:** SKIP.
**POST/ESP32:**
- Reclassified.
- Perfboard physically removed from test rig; photo proof it is on anti-static mat with "RETIRED" label.
- PCB v1.0 installed: same HUB75 cables, same 5V power wires, same ESP32 devkit.
- PSU ON: 5V at J2 = 5.00–5.10 V.
- Standard Test Pattern Suite: every pattern PASS / MATCH vs perfboard baseline (no new artifacts).
- Documentation: grep for "perfboard HCT" across docs/ (excluding evidence/archive dirs) → 0 remaining hits in PROTOTYPE_WIRING.md, PIN_LEVEL_APPENDIX.md, EXPERIMENTS.md, BOM.md (all replaced).
- In-situ photo committed: docs/photos/condx-pcb-v1-insitu-2panel-row.jpg.
**Evidence to save:**
**PRE/WF4:** `docs/pm/evidence/AF-170-skip.md` ("SKIP: ADR-016 = WF4").
**POST/ESP32:**
- `docs/pm/evidence/AF-170-perfboard-retired.jpg` (perfboard on mat, handwritten "RETIRED: Perfboard HCT v0.1, AF-170 PCB v1.0 replaces it" label visible)
- `docs/photos/condx-pcb-v1-insitu-2panel-row.jpg` (committed; PCB in test rig, panels running pattern visible)
- `docs/pm/evidence/AF-170-pattern-match-verdict.md` (all patterns vs baseline = MATCH, count patterns/total)
- `docs/pm/evidence/AF-170-doc-sweep-grep.md` (grep command run across main docs, showing 0 remaining "perfboard HCT" hits in the canonical files — copy-paste grep output)
- git diff or commit hash showing documentation rename edits.
**Safety considerations:**
⚠️ No HUB75 hot-plug (JIRA Safety Rules 2/2). Swap step: PSU OFF → wait 10 s → remove perfboard → install PCB → PSU ON. Never swap HUB75 with PSU powered. ⚠️ 5V polarity verify before tightening J2 screws. Red wire = V+ = +5V = J2 pin 1 (or marked + on silkscreen). Black = V- = GND = J2 pin 2. Wrong polarity = instant ESP32 + HCT death.
**Known uncertainties:**
Documentation grep may find hits in old evidence photos or archived EXP notes with "perfboard" caption text. That's fine — do NOT edit old evidence files (they are historical records). Only edit the CANONICAL docs: PROTOTYPE_WIRING.md, PIN_LEVEL_APPENDIX.md, EXPERIMENTS.md, BOM.md.
**Failure response:**
**PRE/WF4:** SKIP. **POST:** Post-swap pattern shows NEW artifact (not in AF-169 either) → physical installation issue: HUB75 cable not fully seated → reseat. Or 5V polarity reversed → immediate PSU OFF → check wiring. If pattern matches AF-169 but DIFFERS from original perfboard baseline → unlikely but possible: different ESP32 devkit was accidentally used → re-flash original firmware from EXP-012, use original ESP32. Documentation grep still returns perfboard hits in canonical files → re-run replace.
**Source references:**
- JIRA.md §Conditional PCB Work Step 19 "Perfboard replacement swap, identical location/cables, Standard Test Pattern re-run IDENTICAL, docs rename perfboard→PCB"
- AF-169 (populated PCB baseline)
- AF-090 (2-panel perfboard baseline photos to compare against)
- PROTOTYPE_WIRING.md, PIN_LEVEL_APPENDIX.md, EXPERIMENTS.md, BOM.md (canonical docs needing rename sweep)
**Labels:** hardware firmware pcb conditional blocked blocked:adr-016 validation controller-esp32 docs
**Status flags:**
critical-path: no (pre-decision; becomes yes after ADR-016 per reclassification rule)
Conditional: yes
Skip condition: ADR-016 does NOT select multi-ESP32 architecture. This entire epic is required ONLY if 3× ESP32-S3 + HCT adapter perfboard builds are the chosen V1 controller path. If ADR-016 = WF4 architecture, mark all tasks in this epic as blocked/skipped — do NOT build PCBs.
Reclassification rule: After ADR-016. IF multi-ESP32 selected → change Conditional=no (this epic becomes mandatory), remove Labels 'blocked blocked:adr-016', set critical-path=yes for tasks up to and including populated board validation. IF WF4 selected → keep Conditional=yes unchanged, add Labels 'blocked', Skip condition unchanged, epic fully skipped.

---

## EXP Coverage Gap Tasks (appended by 07-experiment-coverage.md; IDs AF-171+)

---

### AF-171: EXP-001 Step 5 coverage — Mirror inventory photos into hardware/photos/ directory path as specified by EXP-001 Procedure

**Task ID:** AF-171
**Epic:** AF-001 (M0: Hardware Receipt & Safe Power)
**Summary:** EXP-001 Procedure Step 5 explicit path coverage. Mirror/copy all AF-011 inventory photos from docs/photos/inventory/ into the hardware/photos/ directory tree per EXPERIMENTS.md literal specification.
**Description:** EXP-001 Procedure Step 5 reads verbatim: "Save photos under hardware/photos/." AF-011 stores photos under docs/photos/inventory/ per its Evidence-to-save spec and docs repo conventions. To ensure the EXP-001 procedure specification is strictly honored at the canonical path it names, create an identical set of photos (file copies or symlinks as appropriate) under hardware/photos/inventory-YYYY-MM-DD/ with the same filenames. This closes the coverage gap: both paths have the inventory photo set.
**Why this task exists:** EXP-001 coverage gap discovered in 07-experiment-coverage.md audit. AF-011's Evidence-to-save specifies docs/photos/inventory/ but the EXP procedure specifies hardware/photos/. Both paths should be populated so any reader looking at EXPERIMENTS.md can find its files where the procedure says they are.
**Prerequisites:** COMPLETED: AF-011 (inventory photos generated in docs/photos/inventory/).
**Blocked by:** AF-011
**Required hardware:** None (file copy only).
**Required tools:** File manager or shell cp command.
**Required software:** None.
**Exact execution steps:**
1. Create directory `hardware/photos/inventory-YYYY-MM-DD/` if it does not already exist (replace YYYY-MM-DD with the date AF-011 ran, or today).
2. Locate all files generated by AF-011 Evidence-to-save: outer-box.jpg, pcb-front/*.jpg (6 panels + 4 controllers/PSU/C14), pcb-back/*.jpg, cable-ends/*.jpg, consumables-count.jpg, labels-on-bags group photo.
3. Copy each file 1:1 to the matching hardware/photos/ sub-path using `cp -R` or equivalent; preserve file timestamps.
4. Produce a directory listing `ls -R hardware/photos/` confirming every AF-011 photo file has its counterpart in the hardware/ tree.
5. (Optional, space-permitting): If git LFS or storage limits prevent actual byte-for-byte copies, instead write a README.md under hardware/photos/ explaining the mirror location points to docs/photos/inventory/, with SHA-256 checksums of each file so readers can verify they can reconstruct the expected tree from docs/photos.
**Expected result:** Every AF-011 inventory photo file exists BOTH under docs/photos/inventory/ AND under hardware/photos/inventory-YYYY-MM-DD/ with identical filenames.
**Acceptance criteria / DoD:**
- Directory hardware/photos/inventory-YYYY-MM-DD/ exists and is non-empty.
- Count of files in hardware/photos/ inventory folder equals count produced by AF-011 evidence set (or README + checksums if copy impractical).
- SHA-256 of any file in hardware/photos path matches its docs/photos counterpart (sampled check of at least 3 files).
**Evidence to save:**
- Directory listing output committed in `docs/pm/evidence/AF-171-hardware-photos-mirror.md`.
- 3 sampled SHA-256 checksums (docs file vs hardware file identical).
**Safety considerations:** N/A — file operation only, no hardware/electrical.
**Known uncertainties:** None.
**Failure response:** If copy fails due to permission or storage → fall back to README + checksums option (step 5). Re-run DoD against the checksum fallback.
**Source references:**
- EXPERIMENTS.md EXP-001 Procedure Step 5 VERBATIM.
- AF-011 Evidence-to-save listing.
- 07-experiment-coverage.md EXP-001 uncovered step row.
**Labels:** docs blocked blocked:exp-af-011
**Status flags:**
critical-path: no
Conditional: no

---

### AF-172: EXP-008 Step 2 coverage — WF2 stock firmware explicit "both HUB75 outputs" test. Output-1 then Output-2 independently each with Standard Test Pattern Suite + behavior compare.

**Task ID:** AF-172
**Epic:** AF-002 (M1: One Nano-Driven Arbitrary-Text Panel — WF2 experimental reference subtrack)
**Summary:** EXP-008 Procedure Step 2 gap: run 1-panel basic tests explicitly on BOTH HUB75 outputs of the WF2 (output-1 and output-2 independently), compare behavior, document any asymmetry.
**Description:** EXP-008 Procedure Step 2 reads: "Connect one panel; repeat EXP-004's basic tests on both HUB75 outputs." AF-077 runs the basic tests but does NOT explicitly enumerate the "both HUB75 outputs" sub-component. This task fills that gap: physically move the HUB75 cable from WF2 output-1 → output-2, re-run the full Standard Test Pattern Suite + Standard Defect Checklist for each output independently, record whether any difference exists between output-1 and output-2 behavior (color order, scan offset, max layout, refresh anomaly). If WF2 has only 1 usable output due to hardware bug → that is important reference data for EXP-008 conclusion ("max practical stock layout" note).
**Why this task exists:** EXP-008 coverage gap discovered in 07-experiment-coverage.md audit. WF2 has 2 HUB75 outputs; the EXP-008 procedure explicitly calls them both out. Skipping output-2 means we never discover a dead output or asymmetry that would affect whether WF2 could theoretically drive a 2-panel row (reference data, not on critical path since WF2 is non-critical).
**Prerequisites:** COMPLETED: AF-077 (WF2 stock firmware EXP-008 basic tests already ran on at least one output). WF2 still wired and accessible.
**Blocked by:** AF-077
**Required hardware:** WF2 HD-WF2 board, 1 P2 panel already on bench, HUB75 0.5 m cable, PSU and harness branch (carry forward from AF-077; leave connected).
**Required tools:** Camera. Multimeter continuity optional.
**Required software:** Standard Test Pattern Suite generator (or WF2 built-in patterns as used in AF-077).
**Exact execution steps:**
1. PSU OFF, wall plug out. Confirm WF2 powered from same harness branch as AF-077.
2. Label WF2 HUB75 output connectors with a Sharpie now: "OUTPUT-1" = the connector that AF-077 already drove successfully; "OUTPUT-2" = the other HUB75 output on the WF2 body.
3. Run FULL Standard Test Pattern Suite on OUTPUT-1 (re-use AF-077 results as "OUTPUT-1" results; if evidence was not labeled per-output, re-run the 6 patterns now explicitly on OUTPUT-1 and label photos "WF2-output1-patternX.jpg").
4. 4a. PSU OFF, wall plug out. HUB75 no hot plug.
5. 4b. Unplug HUB75 cable from WF2 OUTPUT-1 header. Plug the SAME cable, SAME panel, same end into WF2 OUTPUT-2 header.
6. 4c. Power back up per correct order (PSU ON → WF4/WF2 ON → panel on).
7. Run the SAME 6 Standard Test Pattern Suite patterns (solid R/G/B, checkerboard 8×8, diagonal lines, horizontal gradient, coordinate labels at 32 px, 1px vertical lines every 16) EXPLICITLY on OUTPUT-2. Label each photo "WF2-output2-patternX.jpg".
8. For OUTPUT-2, run Standard Defect Checklist 10 items (same as AF-077). Document each Y/N.
9. Compare OUTPUT-1 vs OUTPUT-2 side by side: colors match? row order match? any difference in max practical layout configuration? any output shows stuck row / missing pixels vs the other? Write a 3-sentence comparison.
10. Power down.
**Expected result:** Both outputs functional OR one output explicitly documented as failed/different. Side-by-side comparison written.
**Acceptance criteria / DoD:**
- OUTPUT-1: 6 pattern photos labeled output1, 10-item defect checklist filled.
- OUTPUT-2: 6 pattern photos labeled output2, 10-item defect checklist filled.
- Side-by-side 3-sentence comparison produced. Note written whether outputs are IDENTICAL or ASYMMETRIC (and in which way: color, mapping, dead output, etc.).
**Evidence to save:**
- WF2 outputs labeled photo (Sharpie "OUTPUT-1"/"OUTPUT-2" on board or adjacent tape).
- `docs/photos/wf2-stock/wf2-output1-pattern[1-6].jpg` (6)
- `docs/photos/wf2-stock/wf2-output2-pattern[1-6].jpg` (6)
- `docs/pm/evidence/AF-172-wf2-both-outputs.md` = output1 defect checklist 10 rows, output2 defect checklist 10 rows, 3-sentence comparison, final verdict "outputs: IDENTICAL / ASYMMETRIC (reason) / ONE DEAD (which one)".
**Safety considerations:**
⚠️ JIRA Safety Rules (2): NEVER hot-plug HUB75. Steps 4a-4c: always PSU OFF + wall plug OUT before physically moving the HUB75 cable between outputs. Do NOT pull the cable while WF2 is powered.
Power order when re-energizing: PSU ON → WF2 ON → data.
**Known uncertainties:** None new. WF2 output count / behavior was partially known; this resolves both-output asymmetry.
**Failure response:** If OUTPUT-2 shows no image (fully dead) → log verdict "1 DEAD (OUTPUT-2)" and proceed — this is valid reference data, not a failure of the experiment/task (it's what we came to find). If patterns differ drastically between outputs → document. No need to "fix" WF2 (reference path only).
**Source references:**
- EXPERIMENTS.md EXP-008 Procedure Step 2 VERBATIM: "repeat EXP-004's basic tests on both HUB75 outputs."
- AF-077 (already did one output, re-used as OUTPUT-1 data or re-run).
- 07-experiment-coverage.md EXP-008 uncovered step row.
**Labels:** hardware firmware controller-wf2 validation spike blocked blocked:exp-af-077 docs
**Status flags:**
critical-path: no
Conditional: no

---

### AF-173: EXP-009 Step 1 coverage — Concrete WF2 stock firmware backup procedure. Read full SPI flash via esptool or equivalent; write .bin; sha256; store firmware/wf2/stock-backup/; restore-read-back verify hash-match DoD.

**Task ID:** AF-173
**Epic:** AF-002 (M1: One Nano-Driven Arbitrary-Text Panel — WF2 experimental reference subtrack)
**Summary:** EXP-009 Procedure Step 1 explicit backup coverage: backup WF2 configuration/firmware with concrete step-by-step. Before ANY alternative firmware flashing, produce a verified restorable stock backup.
**Description:** EXP-009 Procedure Step 1 reads: "Back up configuration/firmware where possible." EXP-009 Preconditions also state "original firmware restorable (or loss acceptable)". AF-078 mentions the backup concept but does not enumerate concrete actions. This task provides the concrete step-by-step: identify WF2 flash chip (ESP-based? or proprietary?); choose correct flash-read tool (esptool.py if ESP, or vendor backup utility if proprietary); read full chip; compute sha256; store in repo firmware/wf2/stock-backup/ folder; perform a read-back verify by re-reading the SAME chip a second time and comparing hashes (so the first read is known not to be corrupt); document recovery address map / procedure so later executor knows how to restore.
**Why this task exists:** EXP-009 coverage gap discovered in 07-experiment-coverage.md audit. The EXP precondition says "original firmware restorable" — without a concrete backup step, we don't know if restore is possible. This task closes the gap with actionable read + verify + store steps.
**Prerequisites:** COMPLETED: AF-076 (WF2 physically identified; know what chip / MCU WF2 is using = know what tool to use). COMPLETED: AF-077 (stock firmware already identified as functional = good candidate baseline to capture). WF2 powered + USB/serial header or flash-reading wiring accessible.
**Blocked by:** AF-076, AF-077
**Required hardware:** WF2 board, appropriate programmer for its MCU (if WF2 = ESP-family: CH340 USB-UART, or direct USB-C if it has one; if proprietary SPI flash: a CH340 + SOIC8 test clip). Same 3-pin CH340 rule if using CH340 (TX/RX/GND only, no VCC from CH340).
**Required tools:** Multimeter continuity for identifying WF2 test-point pins if no built-in USB; CH340 USB-UART (per JIRA 3-pin rule) or SOIC8 clip + CH340.
**Required software:** esptool.py latest (if ESP-based); flashrom or equivalent (if generic SPI flash). sha256sum / shasum.
**Exact execution steps:**
1. Determine WF2's MCU type: from AF-076 photos / PCB rear inspection, confirm whether WF2 uses an ESP-family chip (most likely) or another MCU. If ESP → use esptool. If unknown generic SPI flash chip → use flashrom + SOIC8 clip approach.
2. If ESP-based via USB/serial: follow CH340 3-pin rule (TX/RX/GND only; NO VCC from CH340). Wire CH340 → WF2 UART header debug port (or GPIO0/BOOT strapping pin if known). Plug CH340 into PC USB. WF2 powered from its OWN PSU 5 V fused branch (not from CH340).
3. Determine flash size from AF-076 markings (if known). Typical WF2 = 1 MB, 2 MB, or 4 MB SPI flash. Start with a 4 MB read to be safe: `esptool.py --port <PORT> read_flash 0x0 0x400000 firmware/wf2/stock-backup/wf2-stock-read1.bin`.
4. Compute sha256: `sha256sum firmware/wf2/stock-backup/wf2-stock-read1.bin > firmware/wf2/stock-backup/wf2-stock-read1.bin.sha256`. Store output.
5. WITHOUT changing any wiring, perform the SAME read a SECOND time to a DIFFERENT filename: `esptool.py --port <PORT> read_flash 0x0 0x400000 firmware/wf2/stock-backup/wf2-stock-read2.bin`.
6. Compute sha256 of read2: `sha256sum firmware/wf2/stock-backup/wf2-stock-read2.bin`.
7. COMPARE hashes. They MUST match exactly (byte-for-byte identical dumps). If they DIFFER → re-seat wires / improve boot-strapping / retry step 3. Do not proceed until read1 hash == read2 hash.
8. Once matching: rename the canonical backup to `wf2-stock-backup-YYYY-MM-DD.bin` and its hash file. Write a README.md in firmware/wf2/stock-backup/ stating: chip type, flash size, tool used, exact restore command (e.g., `esptool.py --port PORT write_flash 0x0 wf2-stock-backup-YYYY-MM-DD.bin`), BOOT/strapping pin state needed for restore, warning "NO CH340 VCC connected ever during restore — WF2 always powered from its own PSU 5 V branch".
9. If WF2 uses proprietary non-ESP approach and cannot find a read tool → document "backup not achievable via standard tools; accept loss per EXP-009 preconditions OR-loss-acceptable clause" and record why (unknown chip, no exposed header). Proceed to step 10 with the documented decision.
**Expected result:** Read1 hash == Read2 hash; bin + hash + README stored. OR documented acceptable-loss decision with reasoning.
**Acceptance criteria / DoD:**
- File `firmware/wf2/stock-backup/wf2-stock-backup-YYYY-MM-DD.bin` exists.
- Its corresponding `.sha256` file exists, AND a second independent read produced the IDENTICAL sha256 hash (hash1 == hash2 documented side by side).
- README.md in firmware/wf2/stock-backup/ contains: chip type, flash size, tool used, exact restore command line, BOOT strap state, CH340 3-pin warning for any future restore attempt.
- OR: if non-ESP proprietary and no read tool found, a documented "loss accepted" decision with 2-sentence reasoning (chip unidentifiable; EXP-009 precondition allows this; flashing alt firmware irreversible but acceptable since WF2 = reference only).
**Evidence to save:**
- Terminals screenshots: read1 done, read2 done, sha256 of both compared matching.
- README.md committed in firmware/wf2/stock-backup/.
- Bin + sha256 committed or stored locally per git LFS / .gitignore rules (if too large for git, record SHA + file path in the README so it can be found on disk).
- If "loss accepted" path: decision document `docs/pm/evidence/AF-173-wf2-backup-loss-accepted.md` with reasoning.
**Safety considerations:**
VERBATIM JIRA Safety Rules (3/4) CH340 rules if using CH340 USB-UART adapter with ESP-family WF2:
1. Only connect TX/RX/GND (3 pins). ALWAYS.
2. NEVER connect 5 V or 3.3 V from CH340 to independently powered WF2. WF2 gets its 5 V from its own assigned fused PSU harness branch (same as used in AF-077/AF-078). Never back-power via CH340.
3. Disconnect CH340 before powering WF2 from PSU / re-powering cycles.
SPI flash clip work: do NOT use high voltage programmers; 3.3 V SPI only. Clip orientation correct (pin 1 dot on clip matches chip pin 1 silk dot on board).
**Known uncertainties:** WF2 MCU family was provisionally identified in AF-076 but may not have been; this task's step 1 forces a definitive answer.
**Failure response:** Read1 hash ≠ read2 hash → common causes: (a) boot strapping pin not held correctly during esptool read → re-check BOOT/EN pins; (b) loose serial wires → re-seat; (c) baud rate too high → lower baud 115200. If proprietary chip and genuinely no read tool available → fall back to "loss accepted" DoD clause (WF2 = reference, not V1 path). NEVER proceed to AF-078 flashing WITHOUT EITHER: hash-match verified backup OR documented loss-acceptance.
**Source references:**
- EXPERIMENTS.md EXP-009 Procedure Step 1 VERBATIM: "Back up configuration/firmware where possible." And EXP-009 Preconditions: "exact WF2 revision confirmed; recovery/reflash procedure understood; original firmware restorable (or loss acceptable)."
- AF-078 (flashing step — must NOT occur before this task produces a backup or acceptable loss).
- 07-experiment-coverage.md EXP-009 uncovered step row.
- JIRA.md Safety Rules (3/4) CH340 VERBATIM 3-pin rule.
**Labels:** firmware controller-wf2 docs safety-review blocked blocked:exp-af-076 blocked blocked:exp-af-077
**Status flags:**
critical-path: no
Conditional: no
