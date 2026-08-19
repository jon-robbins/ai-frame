# AI Frame — Jira Project Tracker Design Spec

**Date:** 2026-08-19
**Status:** Draft — revised with 10 user adjustments; ready for implementation planning
**Path:** docs/pm/ (delivery location)

---

## 1. Classification

**Path: Architectural.** This creates a new project-management subsystem — 12 deliverable files, 11 Jira-defined sections, ~80–120 atomic tasks — with no existing flow in the repo. The input is the exhaustive spec in `docs/JIRA.md`; the output is a complete, novice-executable, dependency-aware backlog plus its 10 companion analysis documents.

## 2. Scope & Success Criteria

### In scope (all 11 deliverables from `docs/JIRA.md` §Required Output, plus navigation):

1. Repository Audit — what exists, empty, branch-only, stale
2. Requirements Matrix — product requirements → defining source
3. Uncertainty / Decision Register — question, evidence, resolving task, downstream blocks
4. Milestone Dependency Graph — hardware→power→Nano→M1→M2→decision→M3→M4→app→frame with parallel WF4/ESP32/WF2 branches
5. Ordered Atomic Backlog — every task, all 20 fields, execution order, parallelism marked, explicit READY/BLOCKED distinction
6. Critical Path — shortest safe path: 1-panel Nano-driven text → 2-panel → 4-panel → 6-panel, with post-decision reclassification rule for losing controller path
7. Existing Experiment Coverage — EXP-001…EXP-016 each mapped to covering AF-### tasks; EXP-017 (new, 4-panel) proposed
8. ADR Coverage — ADR-001…ADR-024 each with constraint/decision/evidence-tasks/resolution-milestone
9. Jira Import Table — normalized markdown table (derived from canonical backlog)
10. Jira CSV Draft — same columns, CSV-escaped, import-ready (derived from canonical backlog)
11. Coverage Audit — self-audit answering all 9 questions from JIRA.md §Coverage Audit, INCLUDING an explicit 3-way cross-file consistency check between backlog.md / jira-import-table.md / jira-import.csv

### Explicitly OUT of scope:

- Any calendar estimates, duration guesses, or invented timelines. (JIRA.md forbids them; confirmed by user adjustment #8.)
- Actually importing anything into Jira
- Executing any hardware, software, or firmware work
- Purchasing components
- Rewriting any ADR, EXP, BOM, or STATUS facts (the tracker *references*, never mutates, source-of-truth docs)

### Success criteria:

- A person new to electronics and PCB development can execute the backlog top-to-bottom without discovering hidden subtasks midway.
- No JIRA.md-required field is missing on any task.
- Every contradiction between source docs is either resolved with evidence or spawns an explicit verification task.
- Every purchased BOM component is accounted for: used/tested / explicitly optional / explicitly rejected.
- Shortest critical path genuinely reaches "Nano renders arbitrary text, transports it, physical panel displays it."
- Backlog is canonical; import table and CSV are perfect mirrors. Coverage audit includes an explicit 3-way mechanical consistency check that passes.
- Task IDs (AF-001, AF-002, …) are assigned once during backlog generation and remain stable forever. No renumbering.

## 3. Architecture: Deliverable Files

All 12 files under `docs/pm/`:

| # | File | Corresponds to | Canonical? | Size estimate |
|---|------|----------------|------------|---------------|
| 1 | `README.md` | Navigation index | — | ~50 lines |
| 2 | `01-repository-audit.md` | JIRA.md #1 | Yes | ~150 lines |
| 3 | `02-requirements-matrix.md` | JIRA.md #2 | Yes | ~150 lines |
| 4 | `03-uncertainty-register.md` | JIRA.md #3 | Yes | ~200 lines |
| 5 | `04-milestone-graph.md` | JIRA.md #4 | Yes | ~150 lines (mermaid + text) |
| 6 | `05-backlog.md` | JIRA.md #5 | **CANONICAL** | 3000–5000 lines (core deliverable, source of truth) |
| 7 | `06-critical-path.md` | JIRA.md #6 | Derived from #6 | ~200 lines |
| 8 | `07-experiment-coverage.md` | JIRA.md #7 | Yes (cross-references #6) | ~350 lines (16+1 EXPs × mapping) |
| 9 | `08-adr-coverage.md` | JIRA.md #8 | Yes (cross-references #6) | ~250 lines (18 ADRs × status) |
| 10 | `09-jira-import-table.md` | JIRA.md #9 | **DERIVED** from #6 | ~600 lines |
| 11 | `10-jira-import.csv` | JIRA.md #10 | **DERIVED** from #6 | ~same rows as #10, CSV format |
| 12 | `11-coverage-audit.md` | JIRA.md #11 | Audit report | ~200 lines |

**Canonical-source rule (user adjustment #10):** `05-backlog.md` is the single source of truth. Files #09 and #10 are mechanically derived views — never edited independently. The Coverage Audit (#12) must include an explicit, mechanical cross-file consistency check (see §11) that verifies #09 and #10 match #06 field-by-field for every AF-### ID. A mismatch = Coverage Audit fails; regenerate #09 and #10 by re-extracting from #06.

Numbered prefixes enforce the JIRA.md-intended reading order.

## 4. Task Granularity & Execution Model

Approach C (Milestone-Gated, Outcome-Bound), as chosen — revised by user adjustments #1 and #2.

### Task boundary rule (user adjustment #1):

**One task = one independently verifiable outcome.**

That outcome must produce one concrete:
- artifact (photo, log, measurement table, commit, schematic file)
- OR one pass/fail-verified condition (continuity verified on all 14 HUB75 signals, voltage within tolerance, panel passes standard test suite)
- OR one recorded decision with documented evidence (ADR-016 = WF4 selected, scoring matrix attached)

No task is defined by "how long it takes." No arbitrary time-range optimization. The sole criterion is: can the task pass or fail on its own, independent of adjacent tasks, without trusting the operator's memory of what happened inside it? If yes, it's a valid task boundary. If not, split.

### Explicit extra-fine granularity for high-risk/error-prone work (user adjustment #2):

The following areas deliberately split into finer task boundaries than the general rule, because mistakes here are either safety-critical, hard to recover from, or maskable by adjacent-passing tests:

#### (a) Mains AC wiring
Rather than one "wire C14→PSU" task, split into individually-verified atomic steps:
- Verify C14 rear-tab L/N/PE routing (mains disconnected, use continuity on the module)
- Wire C14 L-terminal → PSU L-terminal (single conductor, verify continuity afterwards)
- Wire C14 N-terminal → PSU N-terminal (single conductor, verify continuity afterwards)
- Wire C14 PE-terminal → PSU FG/earth (single conductor, verify continuity afterwards; PE must NOT be switched)
- Visual + continuity inspection of all three AC conductors BEFORE any energize
- PSU no-load energize (EXP-002) — separate, after all the above pass

#### (b) Polarity/orientation verification
Every "apply power" task is PRECEDED by its own explicit polarity-verification task that stands alone (never folded into the "apply power" task):
- Panel power branch polarity verify (multimeter, PSU OFF — then PSU ON is next task)
- HUB75 cable orientation verify (key/notch + printed labels vs panel IN markings — then plug in is next task)
- C14 fuse verify (correct rating × present × seated) — separate from wiring tasks

#### (c) ESP32 → HCT245 → HUB75 adapter construction & continuity testing
The HCT245 adapter build (previously ~1 task) splits into these individually-verified stages, each with its own continuity/DoD check:
1. Perfboard prep: mark pin rows for U1, U2, 5V rail, GND rail, HUB75 header footprint
2. Install U1 power & control: U1 VCC(pin20)→+5V, U1 GND(pin10)→GND, U1 DIR(pin1)→+5V, U1 /OE(pin19)→GND. Verify continuity on all 4 pins.
3. Install U1 decoupling: 100 nF directly across U1 pin20↔pin10. Verify no solder bridges.
4. Install U2 power & control: U2 VCC(pin20)→+5V, U2 GND(pin10)→GND, U2 DIR(pin1)→+5V, U2 /OE(pin19)→GND. Verify continuity on all 4 pins.
5. Install U2 decoupling: 100 nF directly across U2 pin20↔pin10. Verify no solder bridges.
6. Wire U1 A-side inputs (ESP32 GPIOs IO5,IO4,IO6,IO15,IO7,IO17,IO8,IO18 → U1 pins 2,3,4,5,6,7,8,9). Continuity verify each of 8.
7. Wire U1 B-side outputs (U1 pins 18,17,16,15,14,13,12,11 → HUB75 header positions R1,G1,B1,R2,G2,B2,A,B). Continuity verify each of 8.
8. Wire U2 A-side inputs (ESP32 GPIOs IO10,IO9,IO16,IO12,IO11,IO13 → U2 pins 2,3,4,5,6,7). Continuity verify each of 6 (U2 pins 8,9 unused confirmed unconnected).
9. Wire U2 B-side outputs (U2 pins 18,17,16,15,14,13 → HUB75 header positions C,D,E,CLK,LAT,OE). Continuity verify each of 6.
10. Wire HUB75 header GND positions (2 positions) → common GND rail. Continuity verify both.
11. Optional bulk cap: install 1000 µF electrolytic across 5V/GND near adapter entry. Polarity verified.
12. Full adapter continuity test: 14 HUB75 signals × end-to-end (ESP32 pin-header → HUB75 header position), both HCT245 VCC/GND pins, no-shorts check between adjacent pins and VCC↔GND.

### Expected task count: 80–120

Breakdown by epic (epics defined in §5, adjusted per user point 5):
- M0 Hardware Receipt & Safe Power: ~15 tasks (extra-fine split on mains + polarity)
- M1 One Nano-driven panel:
  - Nano software bootstrap subtrack: ~12 tasks
  - WF4 controller subtrack: ~14 tasks (incl M1 reach)
  - ESP32 controller subtrack: ~25 tasks (extra-fine on HCT245 build per §4c + M1 reach)
  - WF2 experimental subtrack: ~5 tasks
- M2 Two-panel 256×64: ~10 tasks (split WF4 and ESP32 paths)
- MG Architecture Decision Gate: ~4 tasks
- M3 Four-panel 256×128 (EXP-017, new): ~8 tasks
- M4 Six-panel 256×192: ~10 tasks (EXP-015 thermal, EXP-016 reboot)
- MA Application Software Completion: ~10 tasks
- MF Mounted Frame Optimization: ~10 tasks
- Conditional ESP32 PCB: ~18 tasks (all conditional, post-decision)

Total: ~121 max, minus ~40 of overlap (controller tracks compete, losers drop post-decision, conditional PCB skipped if WF4 wins) → ~80–120 tasks in the canonical backlog with all conditional tracks represented; post-decision ~50–70 remain active.

### Stable IDs (user adjustment #3):

Task IDs (AF-001, AF-002, AF-003, …) are assigned in sequential order on the first backlog-generation pass and are frozen thereafter. Epics claim the first N IDs (as defined in §5). Tasks follow. IDs are NEVER renumbered, never reused, never swapped. If a task is later split into two: keep the original AF-### for the first sub-task and append new AF-### at the end of the sequence for the new sub-tasks. If a task is obsoleted by a decision: mark it `Conditional: yes` + `Skip condition: (decision predicate)`, do NOT delete its AF-###.

## 5. Epics (Milestones & Execution Phases) — user adjustments #4 + #5

Epics PRIMARILY represent execution phases and the explicit gated milestones listed by user (adjustment #5). Epics are NOT EXP mirrors. Existing EXP-001…EXP-016 map to relevant tasks **only through `07-experiment-coverage.md`**, never through epic naming or IDs.

Epic block: AF-001…AF-010. Tasks start at AF-011 onward.

| ID | Epic Name | Explicit Gate? | Contents |
|----|-----------|---------------|----------|
| AF-001 | M0 — Hardware Receipt & Safe Power Infrastructure | No (enabling phase) | EXP-001 inventory & identify; extra-fine C14 inlet wiring + polarity/continuity per conductor; fused switched inlet assembly; ferrules + crimping practice; PSU no-load energize (EXP-002); 1-panel power (EXP-003) + per-branch polarity verify |
| AF-002 | M1 — One Nano-Driven Arbitrary-Text Panel | YES, first gate | Contains parallel subtracks (not separate epics): Nano software bootstrap, WF4 controller path, ESP32 controller path, WF2 experimental reference. Gate = user-supplied arbitrary text rendered on Nano, transported programmatically, displayed correctly on one physical panel. No manual vendor-software per-update operations allowed for the gate-pass. |
| AF-003 | M2 — Two-Panel 256×64 Logical Canvas | YES, second gate | Extend surviving controller candidate(s) to two chained panels = 256×64. Parallel power to both; signal chaining; left/right order; text crossing the physical seam. Gate = arbitrary Nano-generated content seamlessly crosses both panels with no controller-caused seam artifact. |
| AF-004 | MG — Architecture Decision Gate | YES (transition gate) | EXP-014 scoring matrix (WF4 vs multi-ESP32, 13 criteria). Produce ADR-016 final controller, ADR-017 final transport. Apply post-decision reclassification: losing-controller downstream M3+ tasks become conditional/skippable. |
| AF-005 | M3 — Four-Panel 256×128 (2×2) | YES, third gate | *New proposed experiment EXP-017.* 4-panel topology = 2 rows × 2 columns. WF4 path: X1→top-row (2 panels), X2→bottom-row (2 panels). ESP32 path: ESP32#1→top-row (2), ESP32#2→bottom-row (2). Gate = Nano-rendered 256×128 content crosses both horizontal AND vertical physical seams correctly; row updates synchronized enough; sustained stability. EXP-017 is a first-class experiment (adjustment #6): full procedure, measurements, success criteria, result fields. |
| AF-006 | M4 — Six-Panel 256×192 Final Prototype | YES, fourth gate | Full 2×3. WF4: X1→top-row, X2→mid-row, X3→bot-row. ESP32: three controllers, one per row. Gate = 256×192 arbitrary Nano-driven content anywhere on display, every seam crossed correctly, acceptable refresh, stable transport. |
| AF-007 | MR — Reliability, Thermal, Recovery | No (validates M4 gates) | EXP-015 PSU loaded thermal test @ 25/50/75/100% brightness, 6 panels. EXP-016 full-system unattended reboot ×5 cycles. Wi-Fi recovery. Repeated boot/recovery testing. Gate outputs feed directly into MF readiness. |
| AF-008 | MA — Application Software Completion | No | Time/date, weather, Google Calendar, Spotify now-playing + album artwork, arbitrary artwork, caching layer, offline fallback status indicator + last-known data, structured logs, systemd service supervision, boot-time automatic startup. |
| AF-009 | MF — Mounted Frame Optimization | YES, fifth & final gate | Exact component dims → internal layout → cable routing → strain relief → PE bonding → ventilation → passive cooling → thermal testing → brightness ceiling → nighttime brightness → Wi-Fi perf inside enclosure → panel mechanical mount → prototype backplate → final frame → extended unattended. |
| AF-010 | Cond-X — Conditional ESP32 Custom PCB | No (conditional phase) | Trigger = ADR-016 selects multi-ESP32. Freeze footprint → freeze GPIO → KiCad schematic → 2×HCT245 → decoupling → HUB75 keyed → power/GND → ERC → layout → DRC → orientation review → Gerber/drill/BOM/CPL → manufacture → continuity → 1-row validation → replace perfboards. All 18 tasks: `Conditional: yes`, `Skip condition: ADR-016 ≠ multi-ESP32 architecture`. |

### Parallel controller tracks within M1/M2:

WF4 and ESP32 paths live INSIDE AF-002 (M1) and AF-003 (M2) as parallel-labeled task groups (not separate epics), labeled with `controller-wf4` / `controller-esp32` / `controller-wf2` tags. This aligns with user adjustment #4 (epics = milestones not controller mirrors).

### Post-decision reclassification (user adjustment #7):

Every task under M3/MR/MA/MF that is specific to a single controller architecture carries a "Reclassification rule" in its Known uncertainties or Conditional field. Example:

> If ADR-016 selects WF4: this task (ESP32-specific M3 task) becomes `Conditional: yes` with `Skip condition: ADR-016 selects WF4 rather than multi-ESP32`.
> If ADR-016 selects multi-ESP32: all WF4-specific M3+ tasks become `Conditional: yes` with `Skip condition: ADR-016 selects multi-ESP32 rather than WF4`.

The MG epic (AF-004) includes as its final step a mechanical sweep through the backlog: apply reclassification to every losing-controller downstream task, set `Labels: blocked conditional` on losers, confirm winning-controller tasks retain or gain `critical-path: yes`.

## 6. Task Field Schema (20 fields, 1:1 with JIRA.md §Required Fields)

Every non-Epic task includes these fields, in this order, as markdown sub-headers. Fields may reference sub-fields (e.g., Conditional carries Skip condition).

1. **Task ID** — `AF-###` sequential. AF-001…AF-010 are Epics. Tasks begin at AF-011. IDs are stable forever per §4.
2. **Epic** — Parent Epic's AF-### ID + name. (e.g., `AF-002 | M1 — One Nano-Driven Arbitrary-Text Panel`)
3. **Summary** — Imperative single line. Verb-object-outcome.
4. **Description** — What the task covers. What is OUT of scope for this task (to prevent mid-task scope creep).
5. **Why this task exists** — Consequence of skipping. Why it can't be merged into adjacent tasks (calls out the independently-verifiable-outcome criterion specifically).
6. **Prerequisites** — Physical items / information / delivered work that must exist before the task can start (even if no other AF-### blocks it).
7. **Blocked by** — Comma-separated AF-### IDs of tasks that must be COMPLETED before this task starts. `—` if unblocked by other tasks. Together with Labels: blocked, this determines the task's READY status (see §6a below).
8. **Required hardware** — BOM items by name + quantities. Reference BOM line numbers when useful.
9. **Required tools** — Multimeter (specify its model if relevant), soldering iron, crimper, etc.
10. **Required software** — OS images, Pillow, Huidu HD-Windows software, esptool, Python packages, editor, etc.
11. **Exact execution steps** — Numbered list (8–30 steps depending on task risk). Hardware steps: name the probe points, expected multimeter reading range, acceptable pass threshold. Protocol steps: name exact commands (or expected script), expected payload size range, expected success response.
12. **Expected result** — Qualitative description of correct behavior BEFORE formal DoD checks.
13. **Acceptance criteria / Definition of Done** — All objective, pass/fail. Every statement measurable without trusting operator memory. Multi-part criteria are individually enumerated (not a paragraph).
14. **Evidence to save** — Photos (path: `hardware/photos/AF-###/`), screenshots, logs, measurement tables, commit hashes, filled-in EXP result sections. Names the exact repo location so nothing is saved ad-hoc.
15. **Safety considerations** — Explicit list, not empty for any task touching mains, panels, 5 V high-current, HUB75, or CH340 power. Mains tasks: full 8-item JIRA.md checklist (power-off wiring, L/N/PE verify, PE bonded, fuse verified, insulated, strain relief, inspection before energize). Panel tasks: HUB75 not hot-plugged. CH340 tasks: CH340 5 V/3.3 V NOT connected to independently-powered target.
16. **Known uncertainties** — Anything VERIFY ON RECEIPT that could change the steps. If an uncertainty is unresolved on arrival, this field tells the operator exactly WHERE to pause (e.g., "Pause before step 3 if WF4 5 V connector is not a barrel jack; record photos and proceed only after identifying the mating connector.").
17. **Failure response / what to do if it fails** — Step-by-step: identify which DoD criterion failed → check the top 3 most likely causes (cited to earlier tasks or continuity points) → which earlier task to re-verify → which EXP or ADR to update if the finding changes architecture assumptions.
18. **Source references** — Repo file + ID + line ranges per §7 format.
19. **Labels** — From §8 set.
20. **Status flags** — Three sub-fields (Critical path + Conditional + Skip condition + Reclassification rule where applicable):
    - `Critical path:` yes / candidate-yes / no. (See §9 for pre/post decision semantics.)
    - `Conditional:` yes / no.
    - If `Conditional: yes` → `Skip condition:` exact predicate (e.g., "Skip if ADR-016 selects WF4 rather than multi-ESP32 architecture.").
    - If controller-specific and downstream of M2 decision gate → `Reclassification rule:` exact post-MG predicate per §5.

### 6a. READY vs BLOCKED distinction (user adjustment #9):

JIRA.md's 20-field schema does not include a "Status" column. We compute it explicitly using the existing fields, never inventing new stored fields:

```
Task READY  ↔  (Labels does NOT contain "blocked")
               AND
               (Blocked By list is empty
                OR every AF-### in Blocked By is marked COMPLETED in tracking)

Task BLOCKED ↔  otherwise.
```

Additionally, we use three semantic blocker categories that appear as comments in the Prerequisites field (so humans can distinguish WHY without a separate field):
- `BLOCKED: DELIVERY` — Hardware component not yet physically received. Clears after EXP-001 confirms receipt of that item.
- `BLOCKED: EXP-XXX` — Waiting for an experiment's evidence.
- `BLOCKED: ADR-XXX` — Waiting for a pending ADR to be resolved (applies to Conditional: yes tasks before the decision gate).

A task with `Labels: blocked` and `BLOCKED: DELIVERY` in Prerequisites is semantically different from one with `Labels: blocked` and `BLOCKED: ADR-016` — the first is a shipping-delay blocker, the second is an architecture-decision blocker.

## 7. Cross-Reference Format

Text-search and grep-friendly. Each reference includes the stable ID, filename, and line range:

```
ADR-016 (DECISIONS.md#L220-L232)
EXP-007 Phase C (EXPERIMENTS.md#L223-L245)
EXP-017 (proposed; covered by Epic AF-005 | M3)
SIG-01 + BUF-03 (PROTOTYPE_WIRING.md#L245-L270)
BOM: SN74HCT245N (BOM.md#L42-L43)
JIRA Safety §Mains (JIRA.md#L346-L367)
PIN_LEVEL_APPENDIX U1 allocation (PIN_LEVEL_APPENDIX.md#L93-L135)
```

Reverse-lookup files `07-experiment-coverage.md` and `08-adr-coverage.md`:
- `07`: one row per EXP-001…EXP-016 + EXP-017 (proposed). Columns: Experiment ID, Status, Covering AF-### tasks, Uncovered fraction (if any), New tasks spawned.
- `08`: one row per ADR-001…ADR-024. Columns: ADR ID, Status (ACCEPTED/PENDING/DEFERRED/REJECTED), Accepted constraint if ACCEPTED, Pending decision predicate if PENDING/DEFERRED, Evidence-producing AF-### tasks, Milestone at which it resolves.

## 8. Label Taxonomy

Base set from JIRA.md §Issue Types (unchanged):
`spike`, `decision`, `hardware`, `software`, `firmware`, `power`, `safety`, `controller-wf4`, `controller-esp32`, `controller-wf2`, `mechanical`, `critical-path`, `blocked`, `conditional`

Practical additions (needed because base set would leave tasks unfindable):
`nano` (Nano-specific: OS, RAM-sensitive software, UART, USB-C),
`pcb` (KiCad/PCB tasks under AF-010 Cond-X),
`validation` (experiment-result / measurement / DoD-check tasks, vs build/wire tasks),
`delivery` (blocked pending hardware arrival)

## 9. Critical Path Definition

A task is `critical-path: yes` if removing or delaying it strictly increases the earliest possible date of the next milestone gate.

### Pre-decision (before MG/ADR-016):

The critical path is deliberately TWO-CANDIDATE (not an ambiguity, a known structural fork). Both controller tracks have their relevant tasks marked `critical-path: candidate-yes`. Neither is marked plain `yes` because we cannot yet know which will survive.

Both candidates share the SAME upstream critical path:
```
AF-001 (M0: Hardware Receipt)
  → [C14 L wire] → [C14 N wire] → [C14 PE wire] → [AC inspection]
  → PSU no-load
  → Panel 1 polarity verify → 1-panel power (EXP-003)
  → [parallel starts]
     WF4 track candidate: WF4 1-panel HW (EXP-004)
                         + Nano→WF4 programmatic (EXP-007)
                         + Nano: Pillow text renderer + framebuffer + transport adapter
       OR
     ESP32 track candidate: ESP32 board ID (EXP-010)
                            + HCT245 extra-fine build (12 stages, §4c)
                            + ESP32→HCT→1-panel (EXP-011)
                            + Nano→ESP32 transport (EXP-013)
                            + Nano: Pillow text renderer + framebuffer + transport adapter
  → M1 GATE: end-to-end arbitrary Nano text → physical panel
```

The Nano software subtask group (Pillow, framebuffer, transport interface) is marked `critical-path: yes` unconditionally — it's required regardless of which controller wins.

### Decision (MG/AF-004):

ADR-016 selects a controller. ADR-017 selects the transport. AFTER this decision:
1. All winning-controller M3/M4/MR tasks are promoted from `candidate-yes` → `critical-path: yes`.
2. All losing-controller M3/M4/MR tasks become `Conditional: yes` with explicit Skip condition = "ADR-016 selected (winner) rather than (loser)"; their `critical-path` flips to `no`; they gain `Labels: blocked conditional`.
3. WF2 path never reaches M3 regardless — WF2 was always reference/fallback, so M3/WF2 tasks don't exist in the backlog (preventing a category error of planning WF2 for a geometry it can't cleanly serve).

### Post-decision critical path through the milestones (5 gates per user adjustment #5):

```
M1 GATE: 1-panel Nano-driven text (already passed)
  → M2 GATE: 2-panel 256×64, content crosses seam
  → MG: ADR-016 + ADR-017 (decision gate)
  → M3 GATE: 4-panel 256×128 (EXP-017, new first-class experiment)
  → M4 GATE: 6-panel 256×192
  → MR: EXP-015 (thermal) + EXP-016 (reboot ×5)
  → MA: Application software completion
  → MF GATE: Mounted frame optimization
```

Parallel (non-critical, even after decision):
- Nano image creation and Wi-Fi setup (can begin BEFORE hardware receipt, useful but not the literal choke chain — marked `critical-path: no` unless it actually blocks M1 in practice)
- Spotify, Google Calendar, and artwork widgets (only require 6-panel working, not reliability-certified; can overlap with MR)
- Mounted-frame dimensioning/cabling sketches (details can iterate while MA work runs; the actual cutting/building is gated)

## 10. Jira Import Table & CSV Schema

### Canonical-source rule (user adjustment #10, part 1):

`05-backlog.md` is the single source of truth. Both `09-jira-import-table.md` and `10-jira-import.csv` are mechanical derivations. NEVER edit the table or CSV by hand. If a mismatch is found, fix `05-backlog.md` and regenerate both derived files.

### Table (09-jira-import-table.md) columns:

`Issue Type | Epic Name (AF-###) | Task ID | Summary | Description | Blocked By | Labels | Critical Path | Conditional | Acceptance Criteria (abbreviated) | Source References`

One row per Epic (AF-001…AF-010, using `Issue Type = Epic`) plus one row per Task (AF-011 onward, `Issue Type = Task`).

Epic Name column = the task's parent Epic ID. For Epic rows themselves, Epic Name = the Epic's own ID (Jira imports Epics as their own parents automatically).

`Acceptance Criteria (abbreviated)` column: the task's full 20-field DoD is in `05-backlog.md` only. The import table uses a condensed 1-line bullet form that fits import tooling. Users needing full DoD click-through to the backlog file.

Sort: Epics first by ID, then tasks by ID. NEVER sort by any other field, to preserve the execution-order reading alignment.

### CSV (10-jira-import.csv):

Same 11 columns. Standard RFC-4180 CSV rules:
- Fields containing commas, quotes, or newlines are wrapped in `"`.
- Internal double-quote characters inside a quoted field are doubled (`""`).
- `\r\n` line endings for Jira's import tooling compatibility.
- `Blocked By` = comma-separated AF-### IDs inside one field, empty string if none.
- `Labels` = space-separated inside one field (matches Jira CSV import convention).
- Header row = identical column names to the import table.

### Cross-file consistency check procedure (explicit for audit):

The Coverage Audit MUST run this mechanical 4-part check and record its result:

1. **Row counts:** Count of (Epics+Tasks) listed in `05-backlog.md` MUST equal the number of rows (excluding header) in `09-jira-import-table.md` MUST equal the number of rows (excluding header) in `10-jira-import.csv`. Report exact numbers; any mismatch = ❌.
2. **ID set equality:** The set of AF-### IDs present in the backlog MUST equal the set in the table MUST equal the set in the CSV. Report any orphan IDs on either side; any mismatch = ❌.
3. **Field-by-field for every ID:** For each AF-###, verify the stored values of Epic, Summary, Blocked By, Labels, Critical Path, Conditional match between backlog fields and import-table columns AND between import-table columns and CSV cells (after proper CSV unescaping). Check at least 10% of IDs randomly, plus ALL Epics, plus ALL Conditional=yes IDs. Any mismatch = ❌.
4. **Sort order:** Order of rows in table MUST equal order of rows in CSV MUST equal the order of Epics then tasks in the backlog. Any reordering = ❌.

## 11. Coverage Audit Framework

Directly answers the 9 questions from JIRA.md §Coverage Audit, PLUS the 3-way cross-file consistency check from §10 above (total = 10 audit items):

1. **Every purchased component:** Row per BOM item → Status column = In-use (covering AF-###) / Explicitly-optional (AF-010 / Cond-X) / Explicitly-rejected (ADR-### rationale + REJECTED label). Zero items left unaccounted.
2. **Every EXP maps to backlog work:** Row per EXP-001…EXP-016 + EXP-017 → mapping to covering AF-### tasks. If any EXP procedure step is not covered by any task's Exact execution steps, create a new AF-### to cover it. Record the diff.
3. **Every PENDING ADR has evidence tasks:** ADR-016, ADR-017, plus deferred ADR-024 → each lists ≥1 AF-### producing evidence for the decision, recorded with milestone at which evidence arrives.
4. **Every milestone has objective acceptance criteria:** The 6 gated milestones (M1 one panel, M2 two panel, MG decision, M3 four panel, M4 six panel, MF mounted frame) each get explicit enumerated DoD gates. No "looks good" subjective passes.
5. **Hidden assumptions → tasks:** Assumptions flagged in the uncertainty register (connector polarities, ESP32 headers soldered vs not, WF4 power connector type, C14 tab routing, Nano installed-power header availability, panel harness gauge/thermal, HUB75 keyed orientation, controller protocol reverse-engineering status) each spawn an explicit VERIFY-ON-RECEIPT task (spike) with its own DoD.
6. **No task too large:** Red-flag any task whose Exact execution steps length exceeds ~30 steps OR whose DoD has more than 8 pass/fail criteria. Such tasks are split (per §4 rules) and the Coverage Audit records the split.
7. **Novice polarity safety:** Walk the task order top-to-bottom from "box arrives" to "first AC energize" to "first panel energize" to "first HUB75 signal." VERIFY that for every energize/connect-with-power step: the IMMEDIATELY PRECEDING task is the corresponding polarity/continuity/orientation VERIFY task with power OFF. Any out-of-order (energize step before its verify step) = ❌, reorder.
8. **No work scheduled before its decision:** Every AF-010 (Cond-X ESP32 PCB) task has `Conditional: yes` + `Skip condition: ADR-016 ≠ multi-ESP32`. Every MF detail task (ADR-024 input-dependent) has `Blocked by: MR completion` or equivalent precondition; no frame-cutting before controller count, PCB dims, and PSU thermals are known.
9. **Shortest critical path validity:** Manual walk of the critical-path candidate chains (both WF4 and ESP32) from "EXP-001 inventory completes" to "M1 gate: Nano text on panel visible." Confirm every intermediate DoD node is satisfied, no dependency skipped, no controller path is assumed to have passed experiments that haven't been run.
10. **3-way cross-file consistency:** Run the explicit procedure from §10 above (row counts, ID sets, field-by-field sample, sort order). Record pass/fail.

Any ❌ on items 1–10 triggers a fix: add or modify or reorder tasks in `05-backlog.md`, regenerate the derived files #09/#10, re-run the audit. Repeat until all 10 audit items pass ✅. Final file records the passing audit timestamp, the person who ran it, and the list of fix commits if any were needed.

## 12. Source-of-Truth Rules Applied

These rules from JIRA.md §Source-of-Truth are encoded into the uncertainty register and backlog tasks:

- **Never silently resolve contradictions.** Example: `agent/connection-diagrams` branch still contains consolidated `CONNECTIONS.md` (291 lines, older) while main has moved to detailed `PROTOTYPE_WIRING.md` (793 lines) + `PIN_LEVEL_APPENDIX.md` (264 lines). The audit classifies this as: stale branch, not an active contradiction — the wiring branches are proposal evidence, not accepted decisions. Consistent with JIRA.md §Repo Audit.
- **Accepted ADRs constrain.** 14 ACCEPTED ADRs are hard constraints on all tasks. Tasks violating an accepted ADR cannot exist in the backlog without first triggering an ADR-amendment spike task.
- **PENDING ADRs do NOT constrain.** ADR-016, ADR-017: all downstream tasks carry explicit Conditional + Skip condition rather than being written to assume one outcome.
- **DEFERRED stays deferred.** ADR-024. Mounted-frame detail tasks carry `Blocked by: MR completion` (thermal/controller inputs known); MF epic gates details after M4/MR. No cutting decisions before the inputs exist.
- **Experiments produce evidence, not decisions.** EXP-014 outputs the scoring matrix; AF-004/MG epic then produces the ADR-016/017 commit with rationale.
- **Nothing is invented (8 categories from JIRA.md §Known Areas):** connector polarity / pin numbering / PCB revisions / GPIO availability / USB functionality / communication protocols / scan configuration / current capacity / firmware support. Each of the 8 maps to explicit `spike` tasks or `VERIFY ON RECEIPT` steps in the uncertainty register, not assumptions.

## 13. Assumptions Encoded (None Are Silent)

Every assumption is Accepted (cited to an ACCEPTED ADR), Unverified (cited to a specific AF-### VERIFY task that resolves it), or Rejected (cited to a REJECTED ADR). None are implicit.

| Assumption | Status | Cited to |
|------------|--------|----------|
| Six P2 panels 2×3 layout 256×192 | Accepted | ADR-001 |
| Canonical framebuffer + transport abstraction | Accepted | ADR-002 |
| App compute separate from HUB75 refresh | Accepted | ADR-003 |
| Nano-W Linux/Python | Accepted | ADR-004 |
| Centralized 5 V PSU | Accepted | ADR-006 |
| Parallel panel power distribution | Accepted | ADR-007 |
| Fused/switched/earthed AC input | Accepted | ADR-008 |
| Prefer wired Nano→controller link | Accepted | ADR-009 |
| Test 3 controllers before selection | Accepted | ADR-010 |
| WF4 = primary stock candidate | Accepted | ADR-011 |
| WF2 = experimental/reference | Accepted | ADR-012 |
| ESP32 with perfboard first, PCB post-decision | Accepted | ADR-013 |
| HCT245 2-stage buffering (not direct drive) | Accepted | ADR-014 |
| ESP32 GPIO mapping = Seengreat reference | Unverified | VERIFY task under EXP-010/AF-002 (ESP32 board ID); mapping re-confirmed before adapter build |
| Panel HUB75 IN connector polarity + key orientation | Unverified | Separate polarity VERIFY task before ANY HUB75 plug-in (M0) |
| WF4 5 V power input connector mating type | Unverified | VERIFY task on receipt; WF4 power-wire task gated until connector identified |
| C14 inlet rear-tab routing (L/N/PE through switch + fuse) | Unverified | Separate continuity VERIFY task (mains disconnected) BEFORE any wire crimp |
| Generic ESP32 board: headers pre-soldered or not? | Unverified | EXP-001 inspection; if unsoldered: new solder-headers task inserted before EXP-010 |
| Nano USB-C power path acceptable for installed use | Unverified | PWR-06 deferred: bench-test USB-C first; decision after PSU integration |
| Panel power harness actual conductor gauge + thermal | Unverified | EXP-003 + EXP-015 measure; replacement task inserted if harness warms |
| Panel 4-pin power connector polarity (red/black to V+/V-) | Unverified | Separate polarity VERIFY per panel before PSU-on |
| Full display current exceeds multimeter 10 A shunt | Accepted (Do-not-measure) | BOM.md Safety Notes, repeated in every current-measure task safety section |
| Controller choice WF4 vs multi-ESP32 | Unverified → PENDING ADR-016 | MG epic AF-004; post-decision reclassification applied per §5 |
| Transport choice (protocol/USB/UART/TCP) | Unverified → PENDING ADR-017 | MG epic AF-004; part of EXP-007 + EXP-013 evidence |
| Final enclosure design | DEFERRED → ADR-024 | MF epic AF-009; gated on MR thermal + controller architecture + PCB dims |
| Raspberry Pi as default compute | Rejected | ADR-004 |
| Expensive integrated ESP32-HUB75 boards | Rejected | ADR-021 |
| Custom low-level firmware as V1 dependency | Rejected | ADR-022 |
| TXS0108E / MOSFET level shifters for HUB75 | Rejected | ADR-014 |
| Cascaded Nano → ESP32 → WF4 → panel | Rejected | ADR-016 rejected alternatives section |

## 14. Safety Encoding

Safety is not a single epic or single label. It's encoded per-task, and every task that touches ANY of the 4 hazard categories must have a non-empty Safety considerations field that verbatim repeats the relevant items from the JIRA.md rule set:

### Mains energize tasks (highest risk):

Inline 8-point checklist in Safety considerations:
1. Power DISCONNECTED before any wiring work.
2. L / N / PE routing VERIFIED with mains disconnected (continuity + visual tab labels).
3. Protective earth connected to BOTH PSU FG/earth AND any exposed conductive enclosure parts (PE is never switched).
4. Fuse present, correct rating (T2A 5×20 slow-blow per BOM), seated correctly.
5. All terminals fully insulated; no bare copper strands visible after crimp; heat-shrink applied where needed.
6. Strain relief applied to AC cable at both the PSU end and the C14 end.
7. Full visual + continuity inspection passed immediately before energizing (documented as the preceding VERIFY task's DoD).
8. Mains never routed through breadboard, Dupont, or unenclosed terminals.

### HUB75 connection tasks:

Safety considerations reads:
> Do not hot-plug HUB75 ribbon cables. Before connecting or disconnecting any HUB75 cable, ensure the PSU is fully de-energized and any panel capacitors are discharged. Verify cable orientation and keying against the panel IN label (not OUT) before mating.

### CH340 and serial debug tasks:

Safety considerations reads:
> Do not connect the CH340 5 V or 3.3 V power output pin to any board that is already receiving independent 5 V power. Use signal (TX/RX) and GND only. Verify both boards share a common GND reference before signaling.

### 5 V high-current panel power tasks:

Safety considerations reads:
> Panel power must arrive through the 1-to-4 parallel distribution harness branches — never daisy-chained through another panel, never through Dupont jumpers, never routed through a development board's screw terminals. Total display current (~27.6 A worst case) must NOT be measured in series through a standard multimeter's 10 A input.

## 15. Decomposition: Implementation Execution Steps

The implementation pass (after writing-plans skill produces the detailed plan) roughly follows this order:

1. Create `docs/pm/` directory.
2. **File #02** `01-repository-audit.md` from gathered context + explicit stale/empty/branch-only/contradiction checks.
3. **File #03** `02-requirements-matrix.md` by scanning PROJECT.md subsystems, all ADRs, and STATUS.md milestone gates.
4. **File #04** `03-uncertainty-register.md` from JIRA.md's 5 uncertainty areas (WF4/ESP32/WF2/Power/Mechanical) + all VERIFY ON RECEIPT items from PROTOTYPE_WIRING.md + PIN_LEVEL_APPENDIX.md + BOM inspection checklist.
5. **File #05** `04-milestone-graph.md` with mermaid flowcharts matching the dependency chain AND parallel controller tracks. Include both pre-decision and post-decision views.
6. **File #06** `05-backlog.md` CANONICAL GENERATION:
   a. Assign AF-001…AF-010 to the 10 Epics (per §5 revised). STABLE IDs from this point on.
   b. Draft all 10 Epic definitions (Epic schema fields as applicable).
   c. M0 tasks: inventory + extra-fine mains wiring (split L/N/PE/inspection) + per-conductor polarity/continuity + PSU no-load + panel power polarity verify + EXP-003 1-panel power. Sequential order.
   d. M1 tasks in parallel groups:
      - Nano bootstrap (independent, can start before hardware).
      - WF4 track (EXP-004, EXP-007 phases A/B/C).
      - ESP32 track (EXP-010 + extra-fine HCT245 12-stage build per §4c + EXP-011 + EXP-012 prereq + EXP-013).
      - WF2 experimental (EXP-008, EXP-009).
      - M1 gate-pass task: end-to-end arbitrary text (aggregates whichever track first passes).
   e. M2 tasks (WF4 256×64 physical + ESP32 256×64 + seam-crossing + stability).
   f. MG tasks (EXP-014 scoring matrix completion → ADR-016 draft → ADR-017 draft → post-decision reclassification sweep of losing-controller downstream tasks).
   g. M3 tasks (EXP-017, new: 4-panel 2×2 proposal + procedure + execution for both WF4 and ESP32 architectures → seam-crossing both axes).
   h. M4 tasks (6-panel full build → EXP-015 loaded thermal → EXP-016 full reboot & Wi-Fi recovery).
   i. MR tasks (reliability, thermal, recovery — may merge with M4 if the scope fits).
   j. MA Application software tasks (JIRA.md §Software Planning ordered list: 1 OS → 2 Wi-Fi/SSH → 3 Python env → 4 Pillow text → 5 test patterns → 6 framebuffer abstraction → 7 transport interface → 8 transport impl → 9 end-to-end → 10 scaling → 11 recovery → 12 higher widgets).
   k. MF mounted frame tasks (ordered per JIRA.md §Mounted Frame Phase).
   l. Cond-X ESP32 PCB tasks (all 18 conditional, explicit skip condition = ADR-016 ≠ multi-ESP32).
   m. Global review pass: every task has Blocked By filled correctly; Labels set; Status flags set (critical-path candidate-yes on both tracks pre-decision; Conditional + Skip on all losing downstream; READY/BLOCKED computable); stable IDs; no renumber.
7. **File #07** `06-critical-path.md` extracted from backlog. Include the pre-decision two-candidate view, post-decision single-path view, and the reclassification rule verbatim. Walk both chains to M1 gate.
8. **File #08** `07-experiment-coverage.md`: one row per EXP-001…EXP-016 + EXP-017. For each, list all covering AF-### tasks with step-level cross-references. EXP-017 is a proposed experiment — write its full template entry (goal, procedure, measurements, success criteria per EXPERIMENTS.md template) in this file as the proposed definition, linked to Epic AF-005/M3.
9. **File #09** `08-adr-coverage.md`: one row per ADR-001…ADR-024. Accepted constraint, pending predicate, evidence AF-###, resolution milestone.
10. **File #10** `09-jira-import-table.md`: MECHANICALLY DERIVED from backlog. Build table by walking Epics then tasks, extracting the 11 columns. Do NOT hand-edit; copy from backlog fields.
11. **File #11** `10-jira-import.csv`: MECHANICALLY DERIVED from the same extraction as #10, with CSV escaping applied.
12. **File #12** `11-coverage-audit.md`: Run the 10-item audit from §11 + §10 cross-file consistency. Fix any failures by editing #06 backlog + regenerating #10/#11, then re-run audit. Record passes.
13. **File #01** `docs/pm/README.md` index: file map, epic list, quick start ("Begin by reading 01-repository-audit.md, then 04-milestone-graph.md, then execute 05-backlog.md top-to-bottom."), stable ID legend, how to mark a task completed, how to apply the post-decision reclassification sweep.
14. Final review pass:
    a. All cross-references resolve to actual existing files (and plausible line ranges; line numbers don't need to be exact if documents are new, but file paths MUST exist).
    b. All Blocked By IDs refer to AF-### that exist.
    c. All Conditional=yes tasks have a non-empty Skip condition.
    d. All tasks touching mains/panels/HUB75 have a non-empty Safety section.
    e. Zero invented calendar dates or durations anywhere (grep for "hour", "day", "week", "Jan", "Feb", "March" — only legitimate safety/measurement occurrences allowed, e.g., "leave energized 10 min" from EXP-002 procedure is OK, that's a test-duration not a project estimate).
    f. Stable ID rule: every AF-### is unique, no duplicates, no gaps (gaps OK if a planned task was removed; document the gap in README if any).
