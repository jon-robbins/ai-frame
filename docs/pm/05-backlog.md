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
**Acceptance criteria / DoD:**
- Constructor works for 128×64.
- set_pixel(5,5,(1,2,3)) → get_pixel (or region crop read → find pixel at 5,5) returns (1,2,3).
- get_region(0,0,128,64).size == (128,64).
- export_raw_bytes returns w*h*3 bytes length for any (w,h).
**Evidence to save:** Python module committed. Unit tests: pytest or small python asserts.
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

