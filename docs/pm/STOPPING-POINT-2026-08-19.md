# Checkpoint — Stopping Point (2026-08-19)

> Frozen snapshot. Next session resumes from "Next actions" at the bottom.

---

## What is committed on this branch

Branch: `agent/pm-tracker` (forked from `main` at `7342271`)

### Files committed (deliverables + task input)

| File | Status | Line count | Notes |
|------|--------|-----------|-------|
| [docs/JIRA.md](file:///Users/jon.robbins/GitHub/ai-frame/docs/JIRA.md) | ✅ Complete (task input) | — | The original 11-deliverable + safety + planning spec. Written before this session; included as the binding authority for all work below. |
| [docs/pm/01-repository-audit.md](file:///Users/jon.robbins/GitHub/ai-frame/docs/pm/01-repository-audit.md) | ✅ Complete | — | File inventory, empty dirs catalog, branch divergence notes (agent/connection-diagrams vs main), contradictions C-01/C-02, stale-content assessment, what's-NOT-here-yet list, untracked files note. Validation passed: ≥5 core docs referenced; ≥6 placeholder dirs listed; contradictions section non-empty. |
| [docs/pm/02-requirements-matrix.md](file:///Users/jon.robbins/GitHub/ai-frame/docs/pm/02-requirements-matrix.md) | ✅ Complete | — | 70+ requirement rows drawn from PROJECT.md §1/§3/§5, all ACCEPTED ADRs, JIRA.md Milestones 1–4, BOM.md safety notes. Columns: #, Requirement statement, Source (doc#L-L), Type (F/S/A/M), Enforcing ADR. Validation passed: ≥65 rows; uniform pipe-cell count. |
| [docs/pm/03-uncertainty-register.md](file:///Users/jon.robbins/GitHub/ai-frame/docs/pm/03-uncertainty-register.md) | ⚠️ PARTIAL — Resolving AF-### = `TASK-TO-BE-ASSIGNED` for all 47 U-rows | — | 47 uncertainties (U-001..U-047) covering all JIRA §Known Areas of Uncertainty (WF4 4, ESP32 9, WF2 1, Power 7, Mechanical 8) + all VERIFY-ON-RECEIPT items from PROTOTYPE_WIRING / PIN_LEVEL_APPENDIX / BOM receipt checklist. Severities: safety-critical / architecture-blocking / integration-risk / cosmetic. **INCOMPLETE:** "Resolving AF-###" column still says `TASK-TO-BE-ASSIGNED` on every row — must be filled AFTER full backlog is ID-stable (after Tasks 5+6 complete). |
| [docs/pm/04-milestone-graph.md](file:///Users/jon.robbins/GitHub/ai-frame/docs/pm/04-milestone-graph.md) | ✅ Complete | — | Legend for 6 milestone gates + parallel branches + conditional phase. TWO mermaid flowcharts: (A) pre-decision view showing dual WF4/ESP32 candidate tracks diverging after EXP-003, Nano SW dashed independent line; (B) post-decision single-winner track → M3→M4→MR→MA→MF with Cond-X PCB as dashed ESP32 branch. Per-gate one-sentence objective DoD. Parallelism legend. Validation passed: 2 mermaid blocks present; all 6 milestones (M1/M2/MG/M3/M4/MF) named; both "WF4" and "ESP32" present. |
| [docs/pm/05-backlog.md](file:///Users/jon.robbins/GitHub/ai-frame/docs/pm/05-backlog.md) | ⚠️ PARTIAL — Epics AF-001..AF-010 done. Tasks AF-011..AF-081 done. **Not yet started: M2 bulk, MG, M3, M4, MR, MA, MF, Cond-X.** | 3231 lines, 71 tasks, 10 epics | Stable ID registry FROZEN header: AF-001..AF-010 = 10 epics; tasks begin AF-011; never renumber/reuse/delete. 20-field task schema enforced (fields 1–20 in exact order per §Task schema header). Epic defs complete for all 10 phases (M0/M1/M2/MG/M3/M4/MR/MA/MF/Cond-X). Tasks: AF-011..AF-024 = M0 Hardware Receipt & Safe Power (14 tasks, mains 6-stage extra-fine, per-panel polarity, EXP-001/002/003). AF-025..AF-037 = M1 Nano SW Bootstrap subtrack (13 tasks, independent/unblocked by hardware delivery). AF-038..AF-047 = M1 WF4 Controller subtrack (10 tasks, EXP-004 stock firmware, EXP-007 A/B/C, E2E arbitrary text). AF-048..AF-075 = M1 ESP32 Controller subtrack (28 tasks, EXP-010 board ID, 12-stage HCT245 extra-fine build with per-point continuity DoD per stage, EXP-011 1-panel, EXP-013 Nano transport, E2E text). AF-076..AF-079 = M1 WF2 Experimental reference subtrack (4 tasks, spike, not on critical path, EXP-008/009). AF-080 = M1 GATE-PASS aggregator (1 task, 7 DoD from JIRA.md Milestone 1, WF4 OR ESP32 pass semantics). AF-081 = first M2 task seed (panel #2 polarity/orientation re-verify — may be redundant vs plan, review at resume). All hardware-touching tasks carry verbatim JIRA.md Safety Rules: mains 8-point, HUB75 no-hot-plug + power order, CH340 3-pin, 5 V high-current no-Dupont. Post-decision reclassification rules NOT yet applied (because M3+ controller-specific tasks not yet written). READY/BLOCKED labels encoded: Nano SW tasks = partially READY (no BLOCKED:DELIVERY), all M0+hardware tasks = Labels: blocked blocked:delivery. |

### Explicitly NOT committed (process artifacts, per project memory)

| Path | Why excluded |
|------|--------------|
| `docs/superpowers/plans/2026-08-19-jira-project-tracker.md` | Plan file. Project memory hard constraint: plan files kept untracked to prevent leakage into commits. |
| `docs/superpowers/specs/2026-08-19-jira-project-tracker-design.md` | Spec/process artifact. Same convention as plan files — process, not deliverable. Deliverables live under `docs/pm/`. |
| `.superpowers-installer/` | Installer scratch dir. Runtime artifact, not repo content. |

---

## ID freeze / Stable ID registry state (non-negotiable going forward)

✅ **AF-001 … AF-010 = Epics (frozen at Task 3).** Must never be renumbered or reused.

✅ **AF-011 … AF-081 = Tasks assigned (71 tasks).** These IDs are burned. Going forward:
- New tasks are **APPENDED AT THE END** with new IDs after the current highest (AF-082 and up).
- No ID is ever reassigned.
- If a task is split: keep the original AF-### as-is (mark it partial or obsolete), append NEW tasks at the end with new IDs.
- If a task is obsoleted/skipped: DO NOT DELETE IT. Set `Conditional: yes` + write `Skip condition:` + add `Labels: blocked conditional`.

The backlog's Stable ID Registry block (lines 7–18 of `05-backlog.md`) is the authoritative source for this rule. Any violation = spec violation requiring a backlog audit re-run.

---

## What is NOT yet done (resumption roadmap)

Ordered by the plan's Task numbering. Resume at **Task 5**:

### ⏸️ Stopped here — next: Task 5 (resume point #1)

**Task 5: Populate M2, MG, M3, M4, MR tasks in backlog** (append after AF-081; IDs continue AF-082+):

- **M2 Two-Panel 256×64** (~10 tasks): WF4 EXP-005 (2 chained panels, horizontal gradient, seam-crossing text, numbered regions, ≥30 min); ESP32 EXP-012 (2 chained panels, scrolling + full-screen color change + brightness config + refresh measure, ≥1 hr); M2 GATE-PASS task (seam-crossing verified on both tracks or survivors).
- **MG Architecture Decision Gate** (4 tasks): EXP-014 scoring matrix (13 criteria × 2 candidates, raw measurements only); Draft ADR-016 Choose WF4 vs multi-ESP32 (DECISIONS.md ADR schema, cite matrix, rule = simplest that reliably satisfies V1); Draft ADR-017 Choose transport (Huidu/UART/USB/TCP, cite EXP-007C / EXP-013, preference = wired if practical); Post-decision reclassification SWEEP task (Execution Steps = walk every remaining task with controller-wf4 OR controller-esp32 label: if WON → keep critical-path:yes Conditional:no; if LOST → flip to Conditional:yes + Skip condition "Skip if ADR-016 selected WINNER" + critical-path:no + Labels:blocked conditional; WF2 always reclassify to conditional).
- **M3 Four-Panel 256×128 / EXP-017 first-class milestone** (~8 tasks): Both WF4 topology (X1→top-row 2 panels, X2→bottom-row 2 panels, 4 parallel power branches) AND ESP32 multi-controller topology (controller#1→top-row 2 chained, controller#2→bottom-row 2 chained — conditional 2nd ESP32 purchase/build tasks with skip for WF4 path); Nano 256×128 framebuffer with row crop split; content crossing BOTH seams; row sync sanity; coordinate grid full-display; ≥30 min stability; M3 GATE-PASS. Every controller-paired task needs `critical-path: candidate-yes` + `Reclassification rule:` + pre-decision `Conditional: yes`.
- **M4 Six-Panel 256×192 Final Prototype** (~10 tasks): Full 2×3 wiring (WF4 X1/X2/X3 or ESP32 #1/#2/#3 top/mid/bot); 6 parallel power branches; 256×192 framebuffer; coordinate grid + all seams; brightness sweep (25/50/75/100%); M4 GATE-PASS; EXP-015 PSU loaded thermal (4 levels × measurements table); EXP-016 power recovery ×5 cycles (timing fields); Wi-Fi recovery; API failure simulation.
- **MR Reliability Thermal Recovery** (2–3 tasks): Aggregate thermal → brightness ceiling; ≥24 hr extended stability at dashboard-normal content.

Validation after Task 5: Total tasks ≥ 100. `grep -c "Reclassification rule" docs/pm/05-backlog.md` ≥ 12. `grep -c "EXP-017"` ≥ 5. Both ADR-016 and ADR-017 appear many times.

**Task 6: Populate MA, MF, Cond-X; global review pass; fill uncertainty register AF refs**

- **MA Application Software Completion** (~10–12 tasks, strict order from JIRA.md §Software Planning): OS baseline, Wi-Fi/SSH harden, env frozen + requirements.txt, Pillow production fonts + alignment, test-pattern module, framebuffer API docs+unit tests, transport interface formalized (exceptions/reconnect/backoff), transport implementation of ADR-017-selected protocol (with reclassification for pre-decision), E2E harden, scaling framebuffer 256×192 + 3-row crop, reliability/watchdog/logs, higher-level widgets (time/weather/calendar/Spotify/artwork/caching/offline-fallback/systemd/auto-start).
- **MF Mounted Frame Optimization** (~18 tasks from JIRA.md §Mounted Frame Phase, ALL BLOCKED: MR completion): exact component dims, internal layout sketch with coords, min frame depth (PSU 50mm constraint), PSU airflow holes, controller/Nano placement, mains/low-voltage separation + creepage/clearance, cable routing plan, strain relief (C13, harnesses, HUB75, Nano USB), PE bonding, ventilation/passive cooling/fans, closed-box thermal testing at brightness ceiling, brightness ceiling + nighttime dim mode, Wi-Fi signal inside frame, panel mechanical mounting (6 panels → backplate fastener + alignment pins), prototype backplate build, final frame finish/wall-mount bracket, 72 hr wall-mounted unattended test, MF GATE-PASS (all 18 checkpoints from JIRA). Gated on MR EXP-015/016 completion + ADR-016 + Cond-X if ESP32 wins. ADR-024 deferral noted.
- **Cond-X ESP32 Custom PCB** (18–19 tasks, ALL `Conditional: yes`, ALL `Skip condition: ADR-016 does not select multi-ESP32 architecture`, Labels: hardware pcb conditional blocked blocked:adr-016. Order from JIRA.md §Conditional PCB Work steps 1–19): Freeze ESP32 footprint from EXP-010; Freeze validated GPIO map (perfboard-proven, not provisional); KiCad schematic ESP32-S3 + 2 × HCT245 stages (U1/U2 validated allocations); decoupling (2×100nF per IC + optional 1000µF footprint); HUB75 keyed connector (pinout orientation verified); power entry thick traces/pours + ferrules/screw terminals; ERC fix all errors + fix critical warnings + document waived; PCB layout (board outline, mounting holes, trace width rules: 5V≥40mil, signals≥8mil, ground pour, decoupling close); DRC fix all errors + critical warnings; physical/connector-orientation review; Gerber X2 (or RS-274X acceptable); Excellon drill + legend; PCB BOM (ref des/val/footprint aligned with JLCPCB/Jiepei/JDBPCB libs); CPL if assembled; 5-board prototype manufacture order (2-layer FR-4 1.6mm 1oz green/white HASL <100×100mm per BOM fabrication note); PCB receipt + every-net continuity via multimeter; 1-row hardware validation (populated adapter → 256×64 row → Standard Test Pattern Suite → stable ≥1hr → no perfboard); Perfboard replacement swap + confirm identical behavior + update wiring doc refs.
- **Global review pass** on entire `05-backlog.md`: Blocked By (no nonexistent AF-###); Labels taxonomy match; Status flags (pre-decision candidate-yes semantics correct, Nano SW unconditional yes, reclassification rules attached to M3+ controller tasks); Conditional + Skip condition 1:1 for every controller M3+ downstream + every Cond-X + every MF gated task; Stable IDs (no dup AF-###, no reuse); READY/BLOCKED label computation consistent (blocked:delivery / blocked:adr-xxx / blocked:exp-xxx all correctly tagged); Safety sections non-empty every hardware touch.
- **Fill Uncertainty Register Resolving AF-###** sweep. Return to `03-uncertainty-register.md` and replace every `TASK-TO-BE-ASSIGNED` with the actual AF-### that resolves U-###. Validation: `grep -c TASK-TO-BE-ASSIGNED docs/pm/03-uncertainty-register.md` should equal 0.

**Task 7: Critical-path analysis → 06-critical-path.md**

Legend (yes/candidate-yes/no semantics). View A: walk both candidate chains M0→M1 (WF4 fork + ESP32 fork node-by-node with AF-### + summary). View B: M1→M2 critical path with winning-controller continuation note. View C: post-decision full chain as mermaid flowchart. Reclassification rule VERBATIM text. Shortest-path sanity check (each gate DoD verbatim from JIRA.md). Validate: both "WF4 candidate" and "ESP32 candidate" strings; Reclassification keyword present; All 6 milestones present.

**Task 8: Experiment coverage + ADR coverage → 07-experiment-coverage.md + 08-adr-coverage.md + EXP-017 appendix**

EXP coverage table (17 rows: EXP-001..EXP-016 + EXP-017). Columns: Experiment ID, Status, Status justification, Covering AF-### task IDs, Uncovered steps list, New tasks created if any. For EXISTING EXPs: manually walk each EXPERIMENTS.md EXP's numbered procedure lines, check each step appears in ≥1 backlog task's Exact execution steps. If uncovered → append NEW tiny AF-### at end of backlog (no renumber) and record here. APPENDIX: Proposed EXP-017 full definition using EXPERIMENTS.md blank template verbatim (Goal, Status=PLANNED, Depends-On=M2+ADR-016, Hardware list, Hypothesis, Preconditions, Procedure, Measurements table, Success Criteria, Result/Conclusion/Follow-Up=TBD). EXP-017 must include BOTH WF4 topology (X1→top-row X2→bottom-row) AND ESP32 multi-controller topology (#1→top-row #2→bottom-row) per JIRA.md §Milestone 3.

ADR coverage table (24 rows: ADR-001..ADR-024). Columns: ADR ID, Status (ACCEPTED/PENDING/DEFERRED/REJECTED), Accepted constraint if ACCEPTED, Pending predicate if PENDING/DEFERRED, Evidence-producing AF-### tasks, Milestone at which it resolves. For PENDING ADR-016/017 list ALL evidence tasks. For DEFERRED ADR-024 list MR inputs. APPENDIX: ADR status summary bar counts. Validate: 17 EXP rows; EXP-017 appendix has ≥10 template section headers; 24 ADR rows; ACCEPTED/PENDING/DEFERRED/REJECTED all appear.

**Task 9: Derive Jira import markdown table → 09-jira-import-table.md** (DERIVED — NEVER hand-edit after generation. Future edits go to 05-backlog.md ONLY then regenerate.) 11 columns exactly: `Issue Type | Epic Name (AF-###) | Task ID | Summary | Description | Blocked By | Labels | Critical Path | Conditional | Acceptance Criteria (abbreviated) | Source References`. Sort order: Epics AF-001→AF-010, then Tasks AF-011→end ascending. Epic rows: Issue Type=Epic, abbreviated scope summary, Epic Exit/Gate criteria. Task rows: Issue Type=Task, description first 1-2 sentences ~80 chars truncate, DoD compact <4 lines per cell. Validate: row count matches epic+task count from 05 exactly. Spot-check 7 IDs summary match between files.

**Task 10: Derive Jira import CSV → 10-jira-import.csv** (SAME data as Task 9, same row order). RFC-4180 compliant: CRLF `\r\n` line endings; field quoting when it contains commas/quotes/newlines; `"` inside quoted → `""`; header row matches 11 column names exactly from Task 9 headers; `Blocked By` empty = CSV empty string (not `—`). Validate with python3 csv.reader: header match True; every row has exactly 11 columns; Blocked By with commas still reads as single column.

**Task 11: 10-item coverage audit → 11-coverage-audit.md**. Run ALL 10 audit items from design spec §11: (1) every BOM purchased component accounted for; (2) every EXP (16+1) → zero uncovered procedure steps; (3) every PENDING/DEFERRED ADR (016/017/024) → ≥1 evidence task each; (4) 6 gated milestones → each has objective enumerated DoD gates; (5) every hidden assumption from uncertainty register → explicit VERIFY task exists (U-### vs backlog cross-check); (6) no task too large (steps ≤30 AND DoD ≤8 bullets per task); (7) novice polarity safety (every energize/power-connect PRECEDED by corresponding verify-power-OFF task, walk top-to-bottom order); (8) no work before decision (Cond-X skip-gated ADR-016; MF detail gated MR completion); (9) shortest critical path validity (walk both chains EXP-001 complete → "Nano text visible on panel" no node skipped); (10) 3-way cross-file consistency (§10 4-part: row counts match, ID sets identical between 05/09/10, field-by-field sample match, sort order identical). For any ❌: fix source file (most often #06; if derivation mismatch regenerate #09/#10). Record methodology/findings/result ✅/❌/fix-log per item. Final file: ALL 10 ✅. Validate: 10 PASS/✅ markers; 3-way consistency results explicit.

**Task 12: README index + final comprehensive review → docs/pm/README.md + docs/pm/.final-review.log (hidden, untracked)**. README contents: (1) title+1-sentence purpose; (2) 12-file map table; (3) novice executor quick-start Step 1→5 (read audit, read graph, work backlog top-to-bottom, if hardware not shipped start READY Nano SW tasks, at MG run reclassification sweep before M3); (4) Stable ID legend (frozen, split=append new end, obsolete=Conditional:yes+Skip never delete); (5) READY/BLOCKED legend (computation rule from design spec §6a; blocker categories DELIVERY/EXP/ADR); (6) post-decision sweep HOW-TO per AF-004 checklist; (7) label legend all 18 explained; (8) how to mark task completed (insert Status line into Evidence section); (9) link to coverage audit "passed 2026-08-XX, modify → re-run 1-10". Final comprehensive review: run ALL structural validations from Tasks 1–11 in one shell block → save output to `.final-review.log` (hidden, untracked). Check specifically: 12 files exist non-empty; all cross-reference `(filename#Lx-Ly)` filenames exist; stable ID count matches backlog total; zero duplicate AF-### IDs; zero false-positive calendar estimate hits; 20-field schema spot-check 10 random tasks; 3-way row counts match final time.

---

## Resumption checklist (copy-paste into next session's starting prompt)

1. `git checkout agent/pm-tracker`
2. Re-read this file: `docs/pm/STOPPING-POINT-2026-08-19.md` + plan file `docs/superpowers/plans/2026-08-19-jira-project-tracker.md` (untracked) + design spec `docs/superpowers/specs/2026-08-19-jira-project-tracker-design.md` (untracked)
3. Resume at **Task 5**: append M2/MG/M3/M4/MR tasks to `docs/pm/05-backlog.md`, continuing IDs at **AF-082** (confirm — grep for highest AF-### present in backlog at resume time, do not assume, increment from there)
4. After Task 6 completes: sweep `docs/pm/03-uncertainty-register.md` to replace all `TASK-TO-BE-ASSIGNED`
5. Work Tasks 7 → 12 order
6. At Task 11 audit item 10: run explicit 3-way consistency; regenerate #09/#10 if any mismatch
7. Before session-end commit: re-verify 0 duplicate AF-### IDs, 0 TASK-TO-BE-ASSIGNED, 0 calendar-estimate keywords outside legitimate EXP test durations

---

_Generated 2026-08-19. Next session: resume at Task 5._
