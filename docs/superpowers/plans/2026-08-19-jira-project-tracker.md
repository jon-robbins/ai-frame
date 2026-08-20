# Jira Project Tracker Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use subagent-driven-development (recommended) or executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Generate all 12 files under `docs/pm/` containing the exhaustive AI Frame project-management backlog per `docs/JIRA.md` spec and `docs/superpowers/specs/2026-08-19-jira-project-tracker-design.md` design — stable AF-### IDs, 20 fields per task, 6 gated milestones, extra-fine high-risk granularity, canonical backlog with mechanically derived table/CSV, and a passing 10-item coverage audit.

**Architecture:** Build files in dependency order. Audit/analysis files first (#02–#05). Then the canonical backlog (#06, the single source of truth, largest deliverable). Then derived files extracted from the backlog (#07, #08, #09, #10). Then the coverage audit (#12, which validates everything else). Finally a README index (#01) as the last human-facing step. Every file has a structural validation step after writing. No file is edited by hand after being generated as a derivation (backlog edits only; table/CSV regenerated from it).

**Tech Stack:** Markdown (CommonMark with GitHub-flavored tables + mermaid diagrams), CSV (RFC-4180 compliant). No runtime code. No external dependencies beyond a text editor and standard shell utilities for structural validation (grep, wc, diff, sort).

**Spec:** [2026-08-19-jira-project-tracker-design.md](file:///Users/jon.robbins/GitHub/ai-frame/docs/superpowers/specs/2026-08-19-jira-project-tracker-design.md)

## Global Constraints

(copied verbatim from the design spec — every task's requirements implicitly include these)

1. **Stable IDs:** AF-001…AF-010 = 10 Epics. Tasks begin at AF-011. Assigned once sequentially on first backlog pass. NEVER renumbered, never reused, never deleted (obsoleted tasks get `Conditional: yes` + Skip condition).
2. **Granularity rule:** One task = one independently verifiable outcome. NO arbitrary time-range optimization.
3. **Extra-fine splits mandatory for:**
   - Mains: separate VERIFY-tab-routing → wire-L → wire-N → wire-PE → inspection → energize
   - Polarity: EVERY "apply power" preceded by its own standalone "verify polarity (power OFF)" task
   - ESP32→HCT245→HUB75: 12-stage split per design spec §4c (perfboard prep → U1 power+control → U1 decoupling → U2 power+control → U2 decoupling → U1 A-side 8 wires → U1 B-side 8 wires → U2 A-side 6 wires → U2 B-side 6 wires → HUB75 GND positions → optional bulk cap → full end-to-end continuity + no-shorts)
4. **Epics = 6 execution milestones + supporting phases** (M0/M1/M2/MG/M3/M4/MR/MA/MF/Cond-X), NOT EXP mirrors. AF-001…AF-010 per design spec §5.
5. **6 explicit gated milestones with objective DoD:** M1 1-panel, M2 2-panel, MG decision, M3 4-panel EXP-017, M4 6-panel, MF mounted frame.
6. **Post-decision reclassification:** Every controller-specific M3+ task carries Reclassification rule = if its controller loses ADR-016, flip to `Conditional: yes` / `critical-path: no` / `Labels: blocked conditional`.
7. **READY/BLOCKED computable from stored fields:** No Status column. READY = Labels lacks "blocked" AND (Blocked By empty OR all pre-requisites COMPLETED). Semantic blocker comments in Prerequisites: `BLOCKED: DELIVERY` / `BLOCKED: EXP-XXX` / `BLOCKED: ADR-XXX`.
8. **EXP-017 is first-class proposed experiment:** Full procedure, measurements, success criteria per EXPERIMENTS.md template, located in Epic AF-005 / M3.
9. **Canonical-source rule:** `05-backlog.md` = single source of truth. `09-jira-import-table.md` and `10-jira-import.csv` = mechanical derivations. NEVER hand-edit the derived files.
10. **3-way cross-file consistency:** Coverage audit 10-item #10 = explicit 4-part check (row counts, ID sets, field-by-field sample, sort order). Any mismatch = regenerate derived files.
11. **NO calendar estimates, NO duration guesses, NO invented timelines.** Test-duration phrases ("leave energized 10 min", "run ≥1 hour stability test") drawn directly from EXP procedures are the ONLY time references allowed.
12. **Every non-Epic task has ALL 20 fields from design spec §6.** Order matters: Task ID → Epic → Summary → Description → Why this task exists → Prerequisites → Blocked by → Required hardware → Required tools → Required software → Exact execution steps → Expected result → Acceptance criteria / DoD → Evidence to save → Safety considerations → Known uncertainties → Failure response → Source references → Labels → Status flags.
13. **Safety sections non-empty and verbatim JIRA.md rules:** Mains tasks → 8-point checklist. HUB75 tasks → no hot-plug. CH340 tasks → no 5 V/3.3 V power to independently-powered target. 5 V high-current → no Dupont/daisy-chain, no full-current-in-series-through-multimeter.
14. **Cross-reference format:** `STABLE-ID (filename#Lstart-Lend)`. ADRs, EXPs, BOM items, JIRA safety rules, wiring docs.
15. **No silent contradictions / no invented facts:** Contradictions between source docs → recorded in audit + VERIFY task. All 8 "never invent" classes from JIRA.md §Source-of-Truth → explicit VERIFY/spike tasks, never assumptions.

---

## File Structure

| # | Path | Action | Responsibility | Canonical? |
|---|------|--------|----------------|------------|
| 01 | `docs/pm/README.md` | Create | Navigation index, epic list, ID legend, READY/BLOCKED legend, how to apply post-decision sweep | n/a |
| 02 | `docs/pm/01-repository-audit.md` | Create | What exists/empty/branch-only/stale; contradictions catalog | Yes |
| 03 | `docs/pm/02-requirements-matrix.md` | Create | Product requirements → defining source doc | Yes |
| 04 | `docs/pm/03-uncertainty-register.md` | Create | Question / evidence now / resolving AF-### / downstream blocks | Yes |
| 05 | `docs/pm/04-milestone-graph.md` | Create | ASCII + mermaid dep graph: pre-decision 2-candidate, post-decision single path | Yes |
| 06 | `docs/pm/05-backlog.md` | Create | **CANONICAL:** 10 Epic defs + all Tasks, 20 fields each, ordered execution, stable IDs | **CANONICAL** |
| 07 | `docs/pm/06-critical-path.md` | Create | Extracted from #06: 2-candidate view → post-decision view → walk both to M1 gate | Derived |
| 08 | `docs/pm/07-experiment-coverage.md` | Create | EXP-001…EXP-016 + EXP-017 → covering AF-###; EXP-017 full proposed template | Yes (cross-refs #06) |
| 09 | `docs/pm/08-adr-coverage.md` | Create | ADR-001…ADR-024 rows: constraint / predicate / evidence AF-### / resolution milestone | Yes (cross-refs #06) |
| 10 | `docs/pm/09-jira-import-table.md` | Create | **DERIVED** 11-column table (Epic rows + Task rows), sorted Epics→tasks | **Derived only** |
| 11 | `docs/pm/10-jira-import.csv` | Create | **DERIVED** same 11 columns, RFC-4180 CSV, `\r\n` line endings | **Derived only** |
| 12 | `docs/pm/11-coverage-audit.md` | Create | 10-item audit (9 JIRA.md + 3-way consistency), all ✅ before deliver | Audit report |

---

### Task 1: Create docs/pm directory + scaffolding (audit + matrix + uncertainty)

**Files:**
- Create: `docs/pm/01-repository-audit.md`
- Create: `docs/pm/02-requirements-matrix.md`
- Create: `docs/pm/03-uncertainty-register.md`

**Interfaces:**
- Consumes: `README.md`, `docs/PROJECT.md#L1-L217`, `docs/STATUS.md#L1-L51`, `docs/BOM.md#L1-L144`, `docs/EXPERIMENTS.md#L1-L533`, `docs/DECISIONS.md#L1-L292`, `docs/JIRA.md#L128-L197`, `hardware/`, `software/`, `firmware/`, git branch list, git status (stale/untracked/branch-only)
- Produces: Three audit/analysis files that Tasks 5/6/7/8 reference directly for uncertainty IDs, contradiction references, and requirement provenance.

**Repository audit content checklist:**
1. File inventory: every dir + key files listed.
2. Empty directories: explicitly list `firmware/`, `hardware/pcb/`, `hardware/schematics/schematics-files`, `software/app/`, `software/display/` — note status = "placeholder, no content yet."
3. Branch-only work: `agent/connection-diagrams` vs main diff noted (CONNECTIONS.md exists there but main has PROTOTYPE_WIRING + PIN_LEVEL_APPENDIX). Classify: stale branch, treated as proposed design not accepted decision. `agent/prototype-wiring-diagrams` diff (none now, appears merged).
4. Stale content candidates: BOM.md STATUS values mostly ORDERED — not stale, expected. Photos are pre-delivery reference — not stale, note replacement rule on receipt.
5. Contradictions found: (a) STATUS.md has 2 milestones; JIRA.md has 4 hardware milestones + intermediate M3 = new EXP-017. Not contradiction; JIRA.md extends STATUS.md — note this explicitly. (b) agent/connection-diagrams CONNECTIONS.md vs main PROTOTYPE_WIRING — not contradiction, branch is older proposal.
6. What's NOT here yet: all software/firmware code, PCB KiCad files, actual experiment result data.
7. Untracked files noted: `docs/JIRA.md`, `docs/superpowers/`, `.superpowers-installer/` — expected.

**Requirements matrix structure:**
- Table with columns: #, Requirement statement, Source (doc#L-L), Type (functional / safety / architectural / mechanical), Enforcing ADR if any.
- Rows drawn from: PROJECT.md §1 (V1 functionality/scope/non-goals), PROJECT.md §3 (subsystem specs), all ADRs (accepted constraints), JIRA.md milestones 1–4, BOM.md safety notes.
- ~60 rows minimum. Must cover the 10 V1 proof items from PROJECT.md §1.4 verbatim.

**Uncertainty register structure:**
- Table with columns: ID (U-001…), Question, Evidence now, Resolving AF-### task (leave TBD if backlog not yet built — revisit after Task 6), Downstream blocks, Severity (safety-critical / architecture-blocking / integration-risk / cosmetic).
- Populate from: JIRA.md §Known Areas of Uncertainty (WF4 4 questions, ESP32 9 questions, WF2 1 note, Power 7 items, Mechanical 8 items) + VERIFY ON RECEIPT items from PROTOTYPE_WIRING.md + PIN_LEVEL_APPENDIX.md + BOM receipt checklist.
- Target: ~30–40 uncertainty rows.

- [ ] **Step 1: Structural skeleton for all 3 files**

Write each file's front matter: title, last updated date, purpose statement, legend of status values used, table headers with columns defined. Do not fill content yet.

Run:
```bash
# Validate: 3 files created, non-empty
ls -la docs/pm/01-repository-audit.md docs/pm/02-requirements-matrix.md docs/pm/03-uncertainty-register.md
wc -l docs/pm/0[123]-*.md
```
Expected: 3 files exist; total lines > 30 (skeletons have headers/legends/footers); no file is 0 bytes.

- [ ] **Step 2: Fill 01-repository-audit.md using the 7-point content checklist above**

Write every section. For every "empty directory" claim, verify the dir with `ls` and note that ".gitkeep exists but no other files." For branch-only work, cite actual `git diff --stat` output from the session context (we already ran this: connection-diagrams branch has CONNECTIONS.md consolidated vs main's detailed 2 docs). Save the contradiction list explicitly as "Contradictions & Discrepancies Identified" subsection.

Run validation:
```bash
# Audit must mention all 5 core docs + both branches
grep -c "README\|PROJECT\|STATUS\|BOM\|EXPERIMENTS\|DECISIONS" docs/pm/01-repository-audit.md
grep -c "connection-diagrams\|prototype-wiring-diagrams" docs/pm/01-repository-audit.md
# Audit must explicitly list the 6 placeholder dirs
grep -c "firmware/\|hardware/pcb/\|software/app/\|software/display/" docs/pm/01-repository-audit.md
# Contradictions section exists, non-empty
grep -A 5 "ontradiction" docs/pm/01-repository-audit.md | head -20
```
Expected: Each grep returns ≥1 and the "Contradictions" grep returns multiple lines with actual content.

- [ ] **Step 3: Fill 02-requirements-matrix.md**

Scrape systematically: PROJECT.md §1.2 (functionality list), §1.4 (V1 proof 10 items), §1.5 (non-goals — use "Explicitly not required" rows), §3 (Display / Compute / Controller / Power / Enclosure subsystems requirements), every ACCEPTED ADR in DECISIONS.md (14 items), every Safety requirement from PROJECT.md §5 and JIRA.md §Safety Rules, BOM.md Safety Notes.

Run validation:
```bash
# Count requirement rows. Expect ≥60 rows (grep table separators)
grep -c "^|" docs/pm/02-requirements-matrix.md
# Verify column format: every row has exactly 5 pipe-separated cells (adjust count if your table has more columns)
awk -F'|' '/^\|/ {print NF}' docs/pm/02-requirements-matrix.md | sort -u
```
Expected: Row count ≥65 lines with pipes. All data rows have the same number of fields; awk sort shows 1 consistent value for all data rows.

- [ ] **Step 4: Fill 03-uncertainty-register.md**

Use the 5 JIRA uncertainty areas + VERIFY ON RECEIPT items. Number sequentially U-001 upward. Leave "Resolving AF-###" column as `TASK-TO-BE-ASSIGNED` — after building the backlog in Task 6 we do a sweep to fill these in.

Run validation:
```bash
# Count uncertainty rows. Expect ≥30
grep -c "^| U-\|^|U-" docs/pm/03-uncertainty-register.md
# All severity values present
grep -oE "safety-critical|architecture-blocking|integration-risk|cosmetic" docs/pm/03-uncertainty-register.md | sort -u
```
Expected: ≥30 rows. At least "safety-critical" and "architecture-blocking" severities are present (mains polarity = safety; controller architecture = blocking).

- [ ] **Step 5: Commit**

```bash
git add docs/pm/01-repository-audit.md docs/pm/02-requirements-matrix.md docs/pm/03-uncertainty-register.md
git commit -m "docs(pm): add repo audit, requirements matrix, uncertainty register"
```

---

### Task 2: Build Milestone Dependency Graph

**Files:**
- Create: `docs/pm/04-milestone-graph.md`

**Interfaces:**
- Consumes: Design spec §5 (10 epics / 6 gates), §9 (critical path pre/post-decision), JIRA.md §Milestone Dependency Graph
- Produces: The visual map that tells a novice which order to read the backlog in. Referenced by README, critical-path file, and coverage audit milestone DoD sections.

Content structure:
1. Text legend: 6 milestone gates (M1…MF) + parallel branches + conditional phase.
2. Pre-decision view (mermaid flowchart):
   - Hardware receipt → safe power → M1 (parallel: Nano SW + WF4 track + ESP32 track + WF2 ref)
   - → M2 → MG decision
   - Show explicitly 2 parallel controller candidates diverging after EXP-003
   - Show dashed line: Nano SW can begin before hardware (independent)
3. Post-decision view: single winning controller → M3 → M4 → MR → MA → MF
   - Conditional ESP32 PCB as dashed branch off MG only if ESP32 wins
4. Text description: for each gate, one sentence on the objective DoD.
5. Parallelism legend: which task groups can run simultaneously, which are strictly serialized.

- [ ] **Step 1: Write the file.** Include two mermaid diagrams + legends + gate descriptions.

- [ ] **Step 2: Structural validation**

```bash
# 2 mermaid blocks present
grep -c "```mermaid" docs/pm/04-milestone-graph.md
# All 6 milestone names appear
grep -oE "M1|M2|MG|M3|M4|MF" docs/pm/04-milestone-graph.md | sort -u
# Both controller candidates named in pre-decision
grep -oE "WF4|ESP32" docs/pm/04-milestone-graph.md | sort -u
```
Expected: 2 mermaid blocks. All 6 milestones present. Both "WF4" and "ESP32" strings present.

- [ ] **Step 3: Commit**

```bash
git add docs/pm/04-milestone-graph.md
git commit -m "docs(pm): add milestone dependency graph"
```

---

### Task 3: Build Epic definitions + assign and freeze stable IDs in canonical backlog

**Files:**
- Create: `docs/pm/05-backlog.md` (initial — only Epics section; tasks arrive in Tasks 4,5,6)

**Interfaces:**
- Consumes: Design spec §5 Epic definitions table, §6 20-field schema, design spec §4 Stable ID rules (AF-001…AF-010 = Epics).
- Produces: The canonical backlog file's header, Epic schema section, and AF-001 through AF-010 Epic entries with ALL Epic-applicable fields filled. Tasks section exists but is empty, with a comment "Tasks begin at AF-011."

Critical action: STABILIZE THE IDS NOW. Write at the top of the file:
> **Stable ID Registry — FROZEN.** AF-001…AF-010 = Epics (defined below). Tasks begin at AF-011. Any renumbering, reuse, or deletion after this point is a spec violation — obsoleted tasks get Conditional:yes + Skip condition, not removal.

Epic field set (abbreviated compared to tasks, since Epics are containers):
- Epic ID
- Epic Name + Gate status (explicit gate? Y/N)
- Scope summary (what's inside, what's NOT inside)
- Entry criteria (tasks that must pass BEFORE any task inside this epic can start — these inform the first task's Blocked By)
- Exit / Gate criteria (objective DoD if this epic is a gate; or completion summary if not)
- Parallelism notes (what inside is safe to run concurrently with adjacent epics?)
- Labels applicable to most tasks inside (so executors can filter)
- Source references (which ADRs/EXPs/sections feed the epic)

- [ ] **Step 1: Initialize backlog with header, ID registry block, and 10 Epic entries.**

Epics in exact order:
AF-001 M0 Hardware Receipt & Safe Power
AF-002 M1 One Nano-Driven Arbitrary-Text Panel
AF-003 M2 Two-Panel 256×64 Logical Canvas
AF-004 MG Architecture Decision Gate
AF-005 M3 Four-Panel 256×128 (2×2 / EXP-017)
AF-006 M4 Six-Panel 256×192 Final Prototype
AF-007 MR Reliability Thermal Recovery
AF-008 MA Application Software Completion
AF-009 MF Mounted Frame Optimization
AF-010 Cond-X Conditional ESP32 Custom PCB

For each, fill all Epic-applicable fields above. Explicitly label which are gates per design spec §5. Leave "Tasks in this Epic:" placeholder sections for each.

- [ ] **Step 2: Structural validation**

```bash
# ID registry phrase "FROZEN" exists
grep "FROZEN" docs/pm/05-backlog.md
# All 10 Epic IDs appear exactly once
grep -oE "AF-00[1-9]|AF-010" docs/pm/05-backlog.md | sort | uniq -c
# Epic Name list contains all 10 names
grep -c "Hardware Receipt\|One Nano\|Two-Panel\|Architecture Decision\|Four-Panel\|Six-Panel\|Reliability\|Application Software\|Mounted Frame\|Conditional ESP32" docs/pm/05-backlog.md
# All 6 gated-milestone keywords appear with "GATE" marker
grep -c "GATE" docs/pm/05-backlog.md
```
Expected: "FROZEN" line present. 10 unique IDs, count per ID = 1. All 10 Epic names present. GATE count ≥ 6.

- [ ] **Step 3: Commit**

```bash
git add docs/pm/05-backlog.md
git commit -m "docs(pm): freeze epic IDs and definitions in canonical backlog"
```

---

### Task 4: Populate M0 + M1 parallel task groups in the backlog (first half of tasks)

**Files:**
- Modify: `docs/pm/05-backlog.md` (append tasks starting at AF-011)

**Interfaces:**
- Consumes: Design spec §4 extra-fine splits (mains/polarity/HCT245 12-stage), §6 20-field per task, §6a READY/BLOCKED labels, §14 safety verbatim rules; EXPERIMENTS.md EXP-001…EXP-013; PROTOTYPE_WIRING.md connection inventories PWR-01…DBG-03; PIN_LEVEL_APPENDIX.md §3/§4 U1/U2 exact pins; BOM.md receipt checklist + safety notes.

ID block for this task: AF-011 through AF-0??. Assign sequentially. **Critical: once assigned, no swaps.**

Task families to insert (rough order, with parallel label groups):

M0 Hardware Receipt & Safe Power (~15 tasks):
- EXP-001 inventory: photo every board, record PCB revisions, confirm markings → BOM inspection items (ferrules, connectors)
- VERIFY: C14 rear tab routing (L/N/PE through fuse+switch) — mains disconnected, continuity
- C14 fuse verify (correct rating, present, seated)
- Crimp practice + ferrule visual sample test (learn tool, produce 5 good crimps)
- Wire C14 L → PSU L — single conductor, crimp spade + ferrule, continuity verify
- Wire C14 N → PSU N — same
- Wire C14 PE → PSU FG/earth — same, PE NOT switched
- Visual + continuity inspection: all 3 AC wires, no stray strands, insulation, strain relief
- PSU no-load energize (EXP-002) — step-by-step with voltage table to fill
- VERIFY panel power harness #1 polarity (multimeter, PSU OFF — confirm red=V+, black=V- on both ends of harness branch)
- VERIFY panel #1 4-pin power connector polarity (panel markings match harness connector gender/reverse-pin check)
- Panel 1 power test (EXP-003) — 1 panel only, PSU ON, voltage at both PSU terminals AND panel connector, 10–15 min thermal
- VERIFY HUB75 cable + panel IN connector orientation (keyed notch position, printed IN label vs OUT, continuity GND positions)
- Panel 2+…+6 polarity verify (single task covering panels 2–6 each: 5 more polarities, all PSU OFF — save time by batching these verifications; the DoD enumerates all 5 individually)

Then M1 epic, split into PARALLEL TRACKS (labeled within the file with clear "=== PARALLEL GROUP ===" separators and `Labels:` with the group tag so humans can filter):

M1 Nano Software Bootstrap subtrack (~12 tasks):
- Flash LicheeRV Nano-W SD card (download image, write, verify)
- Nano first boot + serial debug via CH340 (TX/RX/GND only, no CH340 power)
- Nano Wi-Fi config + SSH connectivity test
- Minimal Python environment install (python3, pip, venv)
- Pillow install + basic smoke test: generate 128×64 red solid PNG → file exists, correct dims
- Pillow text renderer: render arbitrary string "Hello AI Frame" onto 128×64 RGB canvas → saved image, text visibly readable
- Standard test-pattern renderer (solid fills, checkerboard, lines, gradient, coordinate labels)
- Canonical framebuffer abstraction (class/methods: new(width,height), set_pixel(x,y,rgb), get_region(x,y,w,h), export_raw_bytes)
- Transport interface spec: DisplayTransport.send_frame(buffer) abstract, no implementation yet
- Candidate transport implementation skeleton (stubbed, raises NotImplementedError — for WF4-protocol and UART/USB)
- End-to-end smoke: "arbitrary input text → Pillow framebuffer → transport stub → print success"
- (Optional) Framebuffer scaling test: generate 256×64 then 256×128 then 256×192 test patterns without renderer changes

M1 WF4 Controller subtrack (~14 tasks):
- VERIFY WF4 PCB revision, photo front/rear, record markings (EXP-001/WF4 subtask)
- VERIFY WF4 5 V power input connector type — identify mating connector
- WF4 power wiring to PSU (18 AWG, correct connector, ferrules at PSU end)
- WF4 X1 → panel IN HUB75 (signal, verify orientation per polarity task above)
- WF4 stock firmware → 1 panel (EXP-004): configure 128×64 / scan / color-order → run Standard Test Pattern Suite → check Standard Defect Checklist
- Huidu software Phase A (EXP-007A): send known content, map interfaces (config vs file vs live)
- Protocol inspection Phase B (EXP-007B): documented/reverse-engineered libs, static image from PC, then from Nano, measure transfer time + latency + max update freq
- Python integration Phase C (EXP-007C): Pillow render, transport, automate repeated updates, run ≥1 hour
- EXP-007 success: programmatic Nano updates, no manual vendor software per-update, acceptable latency, stable
- Pillow text renderer → WF4 end-to-end test (arbitrary string chosen by user, not hard-coded)

M1 ESP32 Controller subtrack (~25 tasks, HCT245 12-stage extra-fine per design spec §4c):
- VERIFY ESP32-S3 board revision + dimensions + module marking + header spacing + USB location + BOOT/RESET buttons + antenna (EXP-010 measurements) — fill the EXPERIMENTS.md EXP-010 table
- (Conditional) if ESP32 headers unsoldered: solder 1×40 male pin headers to both sides of the board; visual + continuity check
- Confirm N16R8 flash/PSRAM in software via esptool or Arduino info sketch
- Identify unavailable GPIOs (flash, octal PSRAM, USB occupied)
- Confirm provisional GPIO mapping against actual board silkscreen; adjust if needed
- === HCT245 extra-fine build (12 stages, design spec §4c, each task has its own continuity DoD) ===
  - 1. Perfboard prep: mark rows for U1, U2, 5V/GND rails, HUB75 header footprint. Drill/mount IC sockets if DIP sockets purchased (BOM says direct DIP-20, confirm).
  - 2. Install U1 power & control: U1 VCC(pin20)→+5V, U1 GND(pin10)→GND, U1 DIR(pin1)→+5V, U1 /OE(pin19)→GND. Continuity verify all 4 pins.
  - 3. Install U1 decoupling: 100nF directly across U1 pin20↔pin10. Visual check for bridges.
  - 4. Install U2 power & control: 4 pins, continuity verify.
  - 5. Install U2 decoupling: 100nF U2 pin20↔pin10. Bridges check.
  - 6. Wire U1 A-side inputs: ESP32 IO5,IO4,IO6,IO15,IO7,IO17,IO8,IO18 → U1 pins 2,3,4,5,6,7,8,9. 8 continuity verifies.
  - 7. Wire U1 B-side outputs: U1 pins 18,17,16,15,14,13,12,11 → HUB75 R1,G1,B1,R2,G2,B2,A,B. 8 continuity verifies.
  - 8. Wire U2 A-side inputs: ESP32 IO10,IO9,IO16,IO12,IO11,IO13 → U2 pins 2,3,4,5,6,7. 6 continuity verifies. Confirm pins 8,9 unconnected.
  - 9. Wire U2 B-side outputs: U2 pins 18,17,16,15,14,13 → HUB75 C,D,E,CLK,LAT,OE. 6 continuity verifies.
  - 10. Wire HUB75 header GND positions (2) → common GND rail. 2 continuity verifies.
  - 11. Optional: install 1000µF electrolytic across 5V/GND. Polarity verified.
  - 12. Full adapter continuity test. 14 HUB75 signals end-to-end (ESP32 pin header → HUB75 header position). Both IC VCC/GND pins. No shorts: adjacent pins, VCC↔GND.
- === END HCT245 extra-fine build ===
- Flash known ESP32 HUB75 firmware (existing library, not custom. ESP32-HUB75-MatrixPanel-DMA or WLED-MM. Document version + board config)
- ESP32 + HCT + 1 panel physical test (EXP-011): Standard Test Pattern Suite, record refresh rate (if reported), color depth, PSRAM usage, stability, IC temp, flicker. DoD: correct 128×64, ≥1 hour stable.
- Nano ↔ ESP32 wired transport (EXP-013): UART first. Small payload → framing (magic, length, frame# checksum) → full 256×64 (49,152 B) → sequential frames → measure transfer time, latency, drop/corrupt rate. Run ≥1 hour. Test native USB fallback candidate if UART bandwidth inadequate.
- Pillow text renderer → ESP32 end-to-end test (arbitrary user-supplied string, not hard-coded)

M1 WF2 Experimental subtrack (~5 tasks):
- VERIFY WF2 PCB rev, markings
- Stock firmware EXP-008: 1 panel basic tests, max layout, available communication methods
- Alt/open firmware EXP-009: back up → flash known WLED-MM / community build → 1 panel tests, Wi-Fi/serial control. Result: is alt firmware materially better than stock for programmability/live-update/open-protocol?

M1 GATE-PASS aggregator task (1 task):
- M1 One-Panel Gate: Verify at least ONE controller track (WF4 OR ESP32) passes: "user types arbitrary string → Nano renders → transports → correct text visible on 1 physical panel → no manual vendor-software clicks during update → stable 10 minute hold." Document which track passed. The other track may continue in parallel but is not required for the gate.

This is ~15+12+14+25+5+1 = 72 tasks in this Task 4. ID range: AF-011…AF-082 approximately.

- [ ] **Step 1: Insert M0 tasks (AF-011 onward) with all 20 fields per task.** Follow the 15-item family list above. For each mains polarity/wire task, include the full 8-point safety verbatim from design spec §14. For each "energize" task, confirm the PREVIOUS task is the corresponding "verify (power OFF)" task — this is the safety audit check later.

- [ ] **Step 2: Insert M1 Nano Bootstrap subtrack tasks.** Label each: `Labels: software nano critical-path yes` (Nano group is unconditional yes on critical path per design spec §9). In Blocked By, mark as independent where possible (can begin BEFORE hardware: Flash SD card, Python env, Pillow, etc. have `BLOCKED: DELIVERY` blocker only, not other AF-tasks).

- [ ] **Step 3: Insert M1 WF4 subtrack tasks.** Label: `Labels: hardware controller-wf4 power validation critical-path candidate-yes`. For EXP-004: embed the full Standard Test Pattern Suite as execution steps 1-6; embed Standard Defect Checklist in DoD. For EXP-007 phases A/B/C: use the EXPERIMENTS.md procedure verbatim as execution steps, expected result as stated there, success criteria split into Preferred / Strong preference / Acceptable fallback / Failure.

- [ ] **Step 4: Insert M1 ESP32 subtrack tasks INCLUDING the 12 HCT245 stages.** For each of the 12 adapter build tasks, pull the exact pin numbers from PIN_LEVEL_APPENDIX.md §3 for U1 and §4 for U2 (not approximate). Execution steps must enumerate EACH individual solder point (e.g., "1. ESP32 IO5 → U1 pin 2 (A1) via perfboard wire; cut to length; strip; solder both joints; trim"). DoD: "Continuity verified between ESP32 IO5 header pin and U1 pin 2 with multimeter in continuity mode — beep < 1 Ω." For the EXP-013 transport task, reference the candidate transports + framing from EXPERIMENTS.md EXP-013 §Candidate Transports and §Procedure 1-4.

- [ ] **Step 5: Insert M1 WF2 experimental subtrack tasks.** Label: `Labels: firmware controller-wf2 spike critical-path no`. For EXP-009, include the explicit Preconditions from EXPERIMENTS.md: exact WF2 rev confirmed; reflash procedure understood; original firmware restorable.

- [ ] **Step 6: Insert M1 GATE-PASS aggregator task.** Label: `Labels: validation decision critical-path yes`. Blocked By: [M1 WF4 end-to-end task OR M1 ESP32 end-to-end task] (document the "OR" semantics: either one passing unblocks the gate). DoD enumerates the 6 exact conditions from JIRA.md Milestone 1 requirements 1-7 verbatim.

- [ ] **Step 7: Structural validation**

```bash
# Count tasks. Expect ≥60 in this insertion (72 approximate)
grep -c "^### AF-\|^## AF-" docs/pm/05-backlog.md  # or whatever heading style you use for tasks
# Every Epic mentioned has child tasks
grep -c "Epic: AF-00[1-9]\|Epic: AF-010" docs/pm/05-backlog.md
# 20-field schema: check for presence of all field headers in at least one task
for FIELD in "Task ID" "Epic" "Summary" "Description" "Why this task exists" "Prerequisites" "Blocked by" "Required hardware" "Required tools" "Required software" "Exact execution steps" "Expected result" "Acceptance criteria" "Evidence to save" "Safety considerations" "Known uncertainties" "Failure response" "Source references" "Labels" "Status flags"; do
  count=$(grep -c "$FIELD" docs/pm/05-backlog.md)
  echo "$FIELD: $count occurrences (expect ≥ number of non-Epic tasks)"
done
# Safety sections NON-EMPTY on every task whose Labels contain "power" or "safety" or "controller" (i.e., touches hardware)
grep -A 2 "Labels:" docs/pm/05-backlog.md | grep -B 1 -E "power|safety|controller" | grep "Labels" | wc -l
# vs same tasks whose "Safety considerations" section actually has bullet/checklist content (non-empty)
# count "Safety considerations:" occurrences whose following section is non-trivial
```
Expected: Task count ≥70. All 20 field headers appear ≥70 times (once per task). Every hardware-touching task has a non-empty Safety section. Grep is approximate — if counts look low, manually spot-check 3 power tasks and 3 HUB75 tasks for the verbatim 8-point or no-hot-plug safety text.

- [ ] **Step 8: Commit**

```bash
git add docs/pm/05-backlog.md
git commit -m "docs(pm): add M0 + M1 backlog tasks (AF-011..AF-0xx)"
```

---

### Task 5: Populate M2, MG, M3, M4, MR tasks (controller-architecture-dependent phases)

**Files:**
- Modify: `docs/pm/05-backlog.md`

**Interfaces:**
- Consumes: Design spec §5 M2/MG/M3/M4/MR epics; §9 post-decision reclassification rules; §5 EXP-017 definition; EXPERIMENTS.md EXP-005,EXP-006,EXP-012,EXP-014,EXP-015,EXP-016.

Continue ID sequence from where Task 4 ended.

M2 (~10 tasks, split WF4 + ESP32):
- WF4: 2 panels chained (EXP-005): 256×64, both panels parallel power, horizontal gradient, seam-crossing text, vertical lines every 16px, numbered regions, left/right order, ≥30 min run.
- ESP32: 256×64 row (EXP-012): 2 chained panels, static + scrolling + full-screen color change, brightness/color-depth/buffer config, refresh rate measure, ≥1 hr.
- M2 GATE-PASS task: seam-crossing verified on both controller tracks (or whichever survive post-M1 if one already fails).

MG (4 tasks):
- EXP-014 Controller comparison scoring matrix completion: fill every cell of the 13-criteria × 2-candidate table from EXPERIMENTS.md EXP-014 §Scoring Matrix. Use raw measurement data from prior tasks, not gut feel.
- Draft ADR-016: Choose WF4 vs multi-ESP32. Follow DECISIONS.md ADR schema. Cite scoring matrix. Follow decision rule: "simplest architecture that reliably satisfies V1." Do NOT pick based on more-interesting tech.
- Draft ADR-017: Choose transport (Huidu protocol/USB/UART/native-USB/TCP). Cite EXP-007 Phase C / EXP-013 bandwidth/latency results. Follow preference: wired if practical.
- **Post-decision reclassification SWEEP:** Walk EVERY remaining task in the backlog (M3 onward). For each task with Labels: `controller-wf4` or `controller-esp32`:
  - If task's controller WON → keep `critical-path: yes` (from candidate-yes), keep Conditional=no
  - If task's controller LOST → flip to `Conditional: yes`, write Skip condition "Skip if ADR-016 selected WINNER_NAME", flip critical-path to `no`, add Labels: `blocked conditional`
  - If task is generic (no controller label) → no change
  - WF2 M3+ tasks: if any exist, always reclassify to conditional regardless of winner since WF2 is reference/fallback
  - Document this entire sweep: create a new task "Apply post-ADR-016/017 reclassification to backlog" whose Execution Steps ARE the above rules.

M3 (8 tasks, EXP-017 first-class):
- EXP-017 proposed experiment definition inserted into 07-experiment-coverage.md later; backlog now contains execution tasks:
  - WF4 M3 topology: X1→top-row 2 panels, X2→bottom-row 2 panels. 4 parallel power branches (2 harnesses: 4 of 8 branches used). Top-row: Top-L→Top-R chain; Bottom-row: Bot-L→Bot-R chain.
  - ESP32 M3 topology: controller #1 → top-row (2 chained panels); controller #2 → bottom-row (2 chained panels). Note: 2nd ESP32 must be purchased + received + HCT adapter built BEFORE this — if not, insert the conditional purchase/build tasks with skip for WF4 path.
  - Nano framebuffer 256×128 render; split into row crops; send to controllers
  - Arbitrary content crossing BOTH horizontal seam (row boundary) AND vertical seam (panel chain boundary)
  - Row synchronization sanity test (both rows update within an acceptable time delta; no "tearing" worse than dashboard-tolerable)
  - Coordinate grid full-display test, corner labels, boundary labels
  - Sustained stability run ≥30 min full display
  - M3 GATE-PASS task: 4-panel, 256×128, both seams crossed, both row-updates synchronized-enough.

M4 (10 tasks):
- Full 2×3 = 6 panel wiring: WF4 X1/X2/X3 or ESP32 #1/#2/#3 top/mid/bot rows
- 6 parallel power branches: 2 harnesses, 6 of 8 branches used, 2 spare
- Full 256×192 framebuffer: Nano composes, split into 3 row crops, send to controllers
- Coordinate grid + corner labels + panel boundary labels (all seams)
- Brightness sweep: 25%, 50%, 75%, 100%, note behavior
- M4 GATE-PASS task: arbitrary Nano-driven content anywhere on display, every seam crossed correctly, acceptable refresh, stable transport
- EXP-015 PSU loaded thermal: 4 brightness levels × 4 measurements each: PSU voltage, farthest-panel voltage, PSU/wire/connector temp, controller stability, display visual
- EXP-016 power recovery ×5 cycles: document time-to-first-image, time-to-network-data each cycle
- Wi-Fi recovery test: disable AP 5 min → restore → verify auto reconnection
- API failure simulation: failed request → confirm app stays alive → cached/fallback content still visible

MR (~merged with M4 if space permits; otherwise 2-3 tasks on their own):
- MR summary task: aggregate thermal data → establish brightness ceiling if needed; confirm PSU passes; log the final safe max-brightness value
- Extended stability ≥24 hrs at dashboard-normal content

- [ ] **Step 1: Insert M2 WF4 + ESP32 tasks.** For each, embed seam-crossing and ordering verification steps explicitly in DoD (not vague). Reference EXPERIMENTS.md EXP-005 / EXP-012 procedure lines.

- [ ] **Step 2: Insert M2 GATE-PASS task.** Label: `validation decision critical-path candidate-yes` on both tracks.

- [ ] **Step 3: Insert MG 4 tasks (matrix + ADR-016 + ADR-017 + reclassification sweep).** The reclassification sweep task must have execution steps that literally walk the losing-controller M3+ list and apply the flip rules. Label all 4: `Labels: decision blocked blocked: ADR-016` (the MG epic is BLOCKED: EXP from M2 completion).

- [ ] **Step 4: Insert M3 8 tasks with EXP-017 full procedure.** For every WF4-vs-ESP32 task pair, mark the ESP32-multi tasks as `Conditional: yes` pre-decision with reclassification rule = skip if WF4 wins; mark WF4 tasks similarly. Label: `critical-path candidate-yes`. After MG sweep, one becomes plain `yes`, other flips to `blocked conditional`.

- [ ] **Step 5: Insert M4 10 tasks + MR 2-3 tasks.** Again, apply controller-specific conditional + reclassification rules for the 3-controller parallel wiring tasks. For EXP-015, embed the 4 brightness levels × measurements table directly into Execution Steps with empty cells to fill (or as sub-table in Evidence to save). For EXP-016, embed the 5-cycle × 2 timing fields per cycle.

- [ ] **Step 6: Structural validation**

```bash
# Total Epic + Task entries now. Expect ≥ 72 + ~30 = 100 entries
grep -c "^### AF-\|^## AF-" docs/pm/05-backlog.md
# Count explicit "Reclassification rule:" occurrences = number of controller-specific M3+ tasks. Expect ≥ 12
grep -c "Reclassification rule" docs/pm/05-backlog.md
# EXP-017 referenced by name ≥ several times
grep -c "EXP-017" docs/pm/05-backlog.md
# Both ADR-016 and ADR-017 appear in tasks
grep -c "ADR-016\|ADR-017" docs/pm/05-backlog.md
```
Expected: Total tasks ≥ 100. Reclassification count ≥ 12. EXP-017 count ≥ 5. Both ADR IDs appear many times.

- [ ] **Step 7: Commit**

```bash
git add docs/pm/05-backlog.md
git commit -m "docs(pm): add M2-M3-M4-MR backlog tasks; EXP-017 defined"
```

---

### Task 6: Populate MA software, MF mounted frame, and Cond-X ESP32 PCB tasks; global review pass

**Files:**
- Modify: `docs/pm/05-backlog.md`

**Interfaces:**
- Consumes: JIRA.md §Software Planning ordered list (12 stages), §Mounted Frame Phase (18 items), §Conditional PCB Work (19 steps), ADR-024 deferral, design spec §5 AF-008 MA, AF-009 MF, AF-010 Cond-X.

MA Application Software (~10 tasks, order strictly follows JIRA.md §Software Planning):
1. Nano OS finalize + update baseline
2. Wi-Fi/SSH harden + static IP / mDNS / failover config
3. Minimal Python env frozen (requirements.txt, venv activation path on boot)
4. Pillow text renderer production font selection + multi-line + alignment options
5. Standard test-pattern renderer moved to reusable module
6. Canonical framebuffer abstraction production API docs + tests (unit: dimensions, region crops, raw export)
7. Transport interface: formalized exceptions, reconnect semantics, retry loop with backoff
8. Transport implementation: concrete implementation of ADR-017-selected transport (Huidu protocol / UART / USB / TCP — not both! This task is after ADR-017, so reference only the chosen one. In the backlog before decision, keep this task with reclassification rules to pick the right code path based on ADR-017)
9. Physical end-to-end: arbitrary text → Pillow → transport → physical panel (at this point, should already work from M1/M2 gates; this task hardens the pipeline)
10. Scaling framebuffer dimensions: 256×192 canonical; 3-row crop for multi-controller; per-controller send code; NO renderer changes
11. Reliability/recovery: structured logs, transport reconnect, controller restart detection, watchdog
12. Higher-level widgets: time/date, weather, Google Calendar, Spotify now-playing + album artwork, arbitrary artwork, caching layer, offline fallback status indicator + last-known data, systemd service supervision, boot-time auto-startup

MF Mounted Frame Optimization (~10 tasks, order per JIRA.md §Mounted Frame Phase; all BLOCKED: MR completion because of thermal/PCB-dim inputs):
1. Exact component dimensions measured: PSU (199×110×50), controller(s) (WF4 dims or 3×ESP32+HCT adapter dims), Nano, harness slack
2. Internal layout sketch (component placement with X/Y coords, clearance distances, airflow channels)
3. Minimum frame depth determined (PSU 50mm is likely the major constraint; add PCB/cable clearance, finalize depth number)
4. PSU location + air intake/exhaust holes; verify no cable in airflow path
5. Controller location + Nano location; USB/wiring access for service
6. Mains / low-voltage separation: physical barrier, distance, creepage/clearance; no shared routing paths
7. Cable routing plan: every power branch, signal cable lengths, bundle ties, service loops
8. Strain relief: C13 cable, panel power harnesses, HUB75 cables, Nano USB-C — each clamp method specified
9. PE (protective earth) bonding: PSU FG → frame chassis → any exposed conductive panel frames
10. Ventilation design → passive cooling feasibility → if fans required, add them (quiet, not whiny)
11. Thermal testing (inside-frame closed-box) at brightness ceiling → confirm PSU, controllers, wiring all within limits
12. Brightness ceiling (software) + nighttime brightness level / dim mode
13. Wi-Fi signal strength measured inside closed frame → antenna relocation if needed
14. Panel mechanical mounting: 6 panels → backplate fastener layout, alignment pins for pixel pitch consistency
15. Prototype backplate build (wood, MDF, laser-cut acrylic — whatever is accessible; document materials)
16. Final frame: finish material, aesthetics, wall-mount bracket hardware selection
17. Extended unattended testing (72 hrs, wall-mounted, closed frame)
18. MF GATE-PASS: frame on wall, all panels working, all seams correct, brightness acceptable at night, Wi-Fi stable, thermal within limits, service access documented

Cond-X ESP32 Custom PCB (18 tasks, ALL `Conditional: yes`, Skip condition: `ADR-016 ≠ multi-ESP32 architecture`) — order from JIRA.md §Conditional PCB Work 1-19:
1. Freeze exact ESP32 footprint (dimensions, header pitch, pin count, mounting hole positions, USB location) — based on physical measurement from EXP-010
2. Freeze validated GPIO mapping (the mapping that actually passed EXP-011/012 with perfboard, not the provisional one)
3. KiCad schematic: ESP32-S3 socket or headers, power/GND rails
4. Two SN74HCT245N stages (per U1/U2 allocations from PIN_LEVEL_APPENDIX.md §3/§4 with verified pinouts)
5. Decoupling: 2× 100nF beside each HCT245; optional 1000µF footprint
6. HUB75 keyed 2×8 connector (panel-orientation verified pinout)
7. Power/GND: entry connector, thick traces or copper pours for 5 V high-current path, ferrules/screw-terminal choice
8. ERC (Electrical Rules Check) — run, fix all errors, fix all warnings that matter, document waived warnings
9. PCB layout: board outline, mounting holes, trace width rules (5 V ≥ 40 mil, signals ≥ 8 mil), ground pour, component placement decoupling close to ICs
10. DRC (Design Rules Check) — run, fix all errors, fix critical warnings
11. Physical/connector-orientation review: board plugged in → HUB75 cable reaches panel without strain, USB accessible on ESP32, GPIO labels match silk
12. Gerber files generated (Gerber X2 preferred, RS-274X acceptable)
13. Drill files (Excellon), drill legend
14. BOM for PCB (reference designators + values + footprints), aligned with JLCPCB/Jiepei/JDBPCB part libraries if applicable
15. CPL (Component Placement List) if ordering assembled boards
16. Prototype manufacture order (5 boards, 2-layer FR-4, 1.6 mm, 1 oz Cu, green mask white silk HASL, <100×100 mm per BOM note)
17. PCB receipt + continuity test: every net from schematic verified on real board via multimeter continuity
18. One-row hardware validation: populated adapter → HUB75 → 256×64 row → Standard Test Pattern Suite → stable ≥1hr → no perfboard used anymore
19. Perfboard replacement: remove perfboard build from test setup, install PCB adapter in its place, confirm identical behavior, update wiring docs references

Global review pass (last step of this task, on entire #06 backlog):
- Blocked By filled correctly for every task (no references to nonexistent AF-###)
- Labels set according to taxonomy
- Status flags: critical-path semantics match design spec §9 (pre-decision = candidate-yes on both tracks; Nano SW unconditional yes; post-decision reclassification rules attached)
- Conditional + Skip condition: every controller-specific M3+ downstream; every Cond-X PCB task; every MF detail task gated on MR inputs
- Stable IDs: no duplicate AF-###, no accidental reuse of same number for two different tasks
- READY/BLOCKED: every task either has `Labels: blocked delivery` (hardware not shipped yet) OR `Labels: blocked adr-xxx` OR `Labels: blocked exp-xxx` OR computable READY = no blocked label AND Blocked By=— or all COMPLETED.
- Safety sections non-empty on every hardware touch task.

- [ ] **Step 1: Insert MA 10-12 tasks.** Strict order per JIRA.md §Software Planning. Every task references the JIRA.md §Software Planning line range for its stage. For task 8 (transport impl), add explicit Reclassification rule: use Huidu-protocol code path if ADR-017=Huidu; UART if ADR-017=UART; USB if ADR-017=USB; etc.

- [ ] **Step 2: Insert MF 10-18 tasks.** Every task has `Labels: mechanical blocked blocked: exp-exp-015` or equivalent. Blocked By: explicitly reference EXP-015 thermal completion + EXP-016 recovery completion + ADR-016 (controller count known) + Cond-X-PCB-completion if ESP32 path wins. ADR-024 deferral noted in Known uncertainties: "No cutting before all thermal/dimensions/schematic inputs are known." GATE-PASS task enumerates all 18 checkpoints from JIRA.md.

- [ ] **Step 3: Insert Cond-X ESP32 PCB 18-19 tasks.** ALL have `Conditional: yes` with identical `Skip condition: ADR-016 does not select multi-ESP32 architecture`. Labels: `hardware pcb conditional blocked blocked: adr-016`. For steps 3-7 KiCad schematic sections, reference the validated (not provisional) U1/U2 channel allocations from PIN_LEVEL_APPENDIX §3/§4 tables, the HUB75 layout from PIN_LEVEL_APPENDIX §5, and the HCT245 pinout from §2. For step 16 manufacture, reference the 3 researched fabrication services from BOM.md §Fabrication services researched.

- [ ] **Step 4: Run global review pass as its own explicit checklist in the task.** Correct any issues found inline.

- [ ] **Step 5: Structural validation**

```bash
# Total tasks now. Expect 100 + ~12 MA + ~15 MF + ~19 Cond-X = ~146 total Epic+Task entries (approximate)
grep -c "^### AF-\|^## AF-" docs/pm/05-backlog.md
# Count Conditional=yes tasks. Expect ~19 PCB + ~20 losing-controller M3+ = ~40
grep -c "Conditional: yes" docs/pm/05-backlog.md
# Count explicit "Skip condition:" = every Conditional=yes task must have one
grep -c "Skip condition:" docs/pm/05-backlog.md
# All 6 gated milestones mentioned + MF gate
grep -c "GATE-PASS" docs/pm/05-backlog.md
# Zero calendar-duration placeholders (excluding legitimate EXP procedure test-durations)
# Search for suspicious date/month words that aren't test-duration:
grep -ni "Jan\|Feb\|March\|April\|May\|June\|July\|Aug\|Sept\|Oct\|Nov\|Dec\|today\|tomorrow\|next week\|estimate\|duration" docs/pm/05-backlog.md || echo "No calendar/duration keywords found — GOOD"
# Filter out legitimate EXP procedure durations from the scan
```
Expected: Total tasks ≥ 130. Conditional count ≈ Skip condition count (1:1). GATE-PASS count ≥ 6 (M1…MF). Calendar grep: NO matches — or only matches inside legitimate EXP procedure phrases like "run ≥1 hour" or "leave energized 10 min" which are test durations NOT project estimates.

- [ ] **Step 6: Fill Uncertainty Register Resolving AF-### columns**

Return to `docs/pm/03-uncertainty-register.md` and fill the `Resolving AF-###` column for every U-### row, using the actual AF-### task numbers now assigned in the backlog. Re-validate:

```bash
# Every U-### has a non-empty, non-"TASK-TO-BE-ASSIGNED" resolving column
count_unresolved=$(grep -c "TASK-TO-BE-ASSIGNED" docs/pm/03-uncertainty-register.md)
echo "Unresolved uncertainty references: $count_unresolved (expect 0)"
```

- [ ] **Step 7: Commit**

```bash
git add docs/pm/05-backlog.md docs/pm/03-uncertainty-register.md
git commit -m "docs(pm): add MA software, MF frame, Cond-X PCB tasks; fill uncertainty AF references"
```

---

### Task 7: Extract critical-path analysis file

**Files:**
- Create: `docs/pm/06-critical-path.md`

**Interfaces:**
- Consumes: `docs/pm/05-backlog.md` (Status flags field values, critical-path: candidate-yes / yes / no)
- Produces: Human-readable document with 3 views: (A) shortest safe path to "Nano-driven arbitrary text on 1 physical panel"; (B) critical path from there to M2; (C) post-decision critical path: M2 → MG → M3 → M4 → MR → MA → MF, including which controller track is the winning one.

Content sections:
1. Legend: critical-path semantics (yes / candidate-yes / no)
2. View A: Walk the two candidate chains node-by-node. For each node, give AF-### + summary. Example:
   `AF-011 [M0] EXP-001 Inventory + Identify → AF-0?? (C14 tab routing verify) → AF-0?? → ... → AF-0?? [EXP-003 1-panel power] → [fork] → either WF4 candidate chain OR ESP32 candidate chain → → → M1 GATE-PASS AF-0??`
3. View B: M1→M2 critical path (just the winning-controller-continuation; note that post-decision the losing fork drops)
4. View C: Full post-decision chain. Show as mermaid flowchart.
5. Post-decision reclassification rule VERBATIM from design spec §5/§9. Include note: after MG completes, a human MUST walk the reclassification steps per that task's checklist.
6. Shortest-path objective sanity check: list each milestone gate DoD verbatim to prove that reaching the gate actually satisfies what JIRA.md Milestone 1–4 require.

- [ ] **Step 1: Write file with 6 content sections.** Populate View A/B/C by reading Status flags from #06 backlog.
- [ ] **Step 2: Validate**

```bash
# 2 candidate track names both appear
grep -c "WF4 candidate\|ESP32 candidate" docs/pm/06-critical-path.md
# Reclassification rule phrase present
grep -c "Reclassification" docs/pm/06-critical-path.md
# All 6 milestone gate names appear
grep -oE "M1|M2|MG|M3|M4|MF" docs/pm/06-critical-path.md | sort -u
```

- [ ] **Step 3: Commit**

```bash
git add docs/pm/06-critical-path.md
git commit -m "docs(pm): extract critical-path document"
```

---

### Task 8: Build experiment coverage + ADR coverage documents

**Files:**
- Create: `docs/pm/07-experiment-coverage.md`
- Create: `docs/pm/08-adr-coverage.md`

**Interfaces:**
- Consumes: `docs/EXPERIMENTS.md` (EXP-001…EXP-016 full content), `docs/DECISIONS.md` (ADR-001…ADR-024), `docs/pm/05-backlog.md` (task execution steps, Source references field, task IDs assigned)
- Produces: Two reverse-lookup documents. EXP coverage document MUST also contain the full proposed EXP-017 template since EXP-017 does NOT yet exist in EXPERIMENTS.md.

EXP-coverage structure:
1. Table: columns = Experiment ID, Status (PLANNED/READY/IN-PROGRESS/PASSED/FAILED/BLOCKED), Status justification, Covering AF-### task IDs, Uncovered steps list, New tasks created if any.
2. One row per EXP-001…EXP-016 + EXP-017.
3. APPENDIX — Proposed EXP-017 Full Definition. Write this section using the EXPERIMENTS.md blank template verbatim: Goal, Status=PLANNED, Depends-On=M2 completion + ADR-016, Hardware list, Hypothesis, Preconditions, Procedure 1-…, Measurements table, Success Criteria, Result=TBD, Conclusion=TBD, Follow-Up=TBD. Include EXACT topology for both WF4 and ESP32 cases (WF4: X1→top-row, X2→bottom-row; ESP32: controller#1→top-row, controller#2→bottom-row) per JIRA.md Milestone 3 WF4: vs Multi-ESP32 topology paragraphs.

ADR-coverage structure:
1. Table: columns = ADR ID, Status (ACCEPTED/PENDING/DEFERRED/REJECTED), Accepted constraint if ACCEPTED, Pending decision predicate if PENDING/DEFERRED, Evidence-producing AF-### tasks, Milestone at which it resolves.
2. One row per ADR-001…ADR-024.
3. APPENDIX — ADR status summary bar counts (14 ACCEPTED currently, 2 PENDING, 1 DEFERRED, plus REJECTED records that exist inline within accepted ADR rejected alternatives).

- [ ] **Step 1: Write 07-experiment-coverage.md with table + EXP-017 proposed appendix template.**

For the "Uncovered steps list" per existing EXP: manually walk the procedure lines inside each EXPERIMENTS.md EXP and check whether every numbered step appears inside at least one backlog task's Exact execution steps (check by content match). Where a step is NOT covered, add it as a new tiny task to #06 backlog and record it here. This may add 2–5 additional tasks to the backlog in this step — since IDs are stable, append them at the end, do NOT renumber.

- [ ] **Step 2: Write 08-adr-coverage.md table + status summary.**

For PENDING ADR-016/017, list ALL evidence tasks that feed them (all EXP-004→EXP-007 + EXP-011→EXP-013 + their DoD outputs). For DEFERRED ADR-024, list the MR tasks that produce the required inputs.

- [ ] **Step 3: Validation**

```bash
# EXP coverage: 17 rows (16 existing + EXP-017)
grep -c "^| EXP-" docs/pm/07-experiment-coverage.md
# EXP-017 appendix template has all 10 template sections (Goal, Status, Depends, Hardware, Hypothesis, Preconditions, Procedure, Measurements, Success, Result)
grep -E "^\*\*(Goal|Status|Depends|Hardware|Hypothesis|Preconditions|Procedure|Measurements|Success|Result|Conclusion|Follow-Up)" docs/pm/07-experiment-coverage.md | wc -l
# ADR coverage: 24 rows (ADR-001…ADR-024, accounting for gaps)
grep -c "^| ADR-" docs/pm/08-adr-coverage.md
# Statuses ACCEPTED/PENDING/DEFERRED all appear
grep -oE "ACCEPTED|PENDING|DEFERRED|REJECTED" docs/pm/08-adr-coverage.md | sort -u
```

- [ ] **Step 4: If any EXP uncovered steps found, append new tasks to backlog**

Create new AF-### at the END of the backlog (stable IDs, not renumbered). Regenerate the validation counts from Task 6 if needed.

- [ ] **Step 5: Commit**

```bash
git add docs/pm/07-experiment-coverage.md docs/pm/08-adr-coverage.md docs/pm/05-backlog.md
git commit -m "docs(pm): experiment and ADR reverse-lookup coverage docs; EXP-017 proposed template"
```

---

### Task 9: Mechanically derive Jira import table from canonical backlog

**Files:**
- Create: `docs/pm/09-jira-import-table.md`

**Interfaces:**
- Consumes: `docs/pm/05-backlog.md` (each Epic + Task's fields: Task ID, Epic parent, Summary, Description, Blocked By, Labels, Critical Path, Conditional, Acceptance Criteria, Source References)
- Produces: 11-column markdown table, sorted Epics first then tasks, NEVER hand-edited after this point, future edits to #06 only.

11 columns exactly:
`Issue Type | Epic Name (AF-###) | Task ID | Summary | Description | Blocked By | Labels | Critical Path | Conditional | Acceptance Criteria (abbreviated) | Source References`

Derivation rules per row:
- **For Epics (AF-001…AF-010):**
  - Issue Type = Epic
  - Epic Name = own AF-### ID
  - Task ID = own AF-### ID
  - Summary = Epic name string from backlog Epic header
  - Description = Epic scope summary (1–2 sentences, abbreviated)
  - Blocked By = —
  - Labels = labels-most-applicable from epic scope
  - Critical Path = N/A (epics are containers)
  - Conditional = no unless AF-010 (which is conditional)
  - Acceptance Criteria = Epic Exit/Gate criteria (1-sentence abbreviation)
  - Source References = epic's source docs refs

- **For Tasks (AF-011 onward):**
  - Issue Type = Task
  - Epic Name = parent Epic ID string "AF-00X"
  - Task ID = own AF-###
  - Summary = task Summary field verbatim
  - Description = Description first 1-2 sentences or ~80 chars, truncation marker `…` if too long (full description is in backlog)
  - Blocked By = comma-separated list, `—` if empty
  - Labels = space-separated, copied verbatim from task's Labels field
  - Critical Path = task's Status flags value ("yes" / "candidate-yes" / "no")
  - Conditional = "yes" / "no"
  - Acceptance Criteria (abbreviated) = DoD criteria enumerated as compact bulleted 1-liners; keep < 4 lines per cell; long DoD lives in backlog
  - Source References = references copied verbatim (abbreviate long line ranges if needed)

- [ ] **Step 1: Build the table row-by-row.** Use exact sort order: Epics AF-001→AF-010 ascending; Tasks AF-011→end ascending. NEVER sort by any other column.

- [ ] **Step 2: Validation (row consistency against #06)**

```bash
# Count rows in table (excluding header). Must equal count of Epics+Tasks in #06.
rows_table=$(( $(grep -c "^|" docs/pm/09-jira-import-table.md) - 2 ))  # minus header + separator
rows_backlog=$(grep -c "^### AF-\|^## AF-" docs/pm/05-backlog.md)
echo "Table rows: $rows_table ; Backlog entries: $rows_backlog"
[ "$rows_table" -eq "$rows_backlog" ] && echo "MATCH" || echo "MISMATCH"
# Sample 5 random Task IDs + 2 Epic IDs and check Summary matches
for id in AF-003 AF-010 AF-020 AF-050 AF-080 AF-100 AF-120; do
  backlog_summary=$(grep -A 1 "$id" docs/pm/05-backlog.md | grep "Summary" | head -1 | cut -d: -f2-)
  table_summary=$(grep "| $id " docs/pm/09-jira-import-table.md | cut -d'|' -f5)
  echo "$id backlog: $backlog_summary"
  echo "$id table:   $table_summary"
done
```

- [ ] **Step 3: Commit**

```bash
git add docs/pm/09-jira-import-table.md
git commit -m "docs(pm): derive Jira import markdown table"
```

---

### Task 10: Mechanically derive Jira import CSV + document the derivation script/log

**Files:**
- Create: `docs/pm/10-jira-import.csv`

**Interfaces:**
- Consumes: EXACT same data as Task 9's output — same 11 columns, same row order, same values
- Produces: RFC-4180 compliant CSV with `\r\n` line endings, proper quoting, column header matching the import table column names EXACTLY.

CSV rules (implemented exactly):
1. Header row = 11 column names in order: `Issue Type,Epic Name (AF-###),Task ID,Summary,Description,Blocked By,Labels,Critical Path,Conditional,Acceptance Criteria (abbreviated),Source References`
2. Line endings: `\r\n` (not `\n`). On Unix, convert with `sed` or write a Python one-liner.
3. A field is wrapped in `"` if it contains commas, quotes, OR newlines.
4. A `"` character inside a quoted field is doubled to `""`.
5. The order of rows is IDENTICAL to Task 9's table. No reordering, no filtering, no additional rows.
6. `Blocked By` empty = empty string (not "—" which is the markdown convention; CSV uses empty).

Best implementation: a small Python (or shell) helper that reads the markdown table and emits CSV. The helper should be quick and throwaway (delete after use), or the mapping can be done manually for ~130 rows with careful copy/paste using the same extraction. Either way, document in the commit how the CSV was produced so a future regenerator can replicate it.

- [ ] **Step 1: Produce CSV with 11 columns + correct escaping.**

- [ ] **Step 2: Validation (byte-level correctness)**

```bash
# Line endings are CRLF (should be 0x0D 0x0A between rows)
file docs/pm/10-jira-import.csv
head -c 500 docs/pm/10-jira-import.csv | od -c | head -20 | grep "\\\\r"
# Header column names exact match (use python to diff first line vs expected)
python3 <<'PY'
import csv
expected = ["Issue Type","Epic Name (AF-###)","Task ID","Summary","Description","Blocked By","Labels","Critical Path","Conditional","Acceptance Criteria (abbreviated)","Source References"]
with open("docs/pm/10-jira-import.csv", newline="") as f:
    reader = csv.reader(f)
    header = next(reader)
    print("Header match:", header == expected)
    if header != expected:
        print("Got header:", header)
        print("Expected:", expected)
    rows = list(reader)
    print("Data rows:", len(rows))
    # Spot-check 2 rows for CSV parsing correctness (no broken quoting)
    for i in [0, len(rows)//2, len(rows)-1]:
        print(f"Row {i}: Task ID={rows[i][2] if len(rows[i])>2 else 'SHORT'}, cols={len(rows[i])}")
PY
# Row count data rows should equal the table's data rows from Task 9 validation
# Also spot-check a Blocked By field with commas: if a task has Blocked By "AF-020, AF-021", it must parse as one column not two.
```

Expected: Header match = True. Every row has exactly 11 columns. The Blocked By column, when containing commas, is still read as a single column by the CSV parser.

- [ ] **Step 3: Commit**

```bash
git add docs/pm/10-jira-import.csv
git commit -m "docs(pm): derive Jira import CSV"
```

---

### Task 11: Run the 10-item coverage audit + fix any failures

**Files:**
- Create: `docs/pm/11-coverage-audit.md`
- May modify: `docs/pm/05-backlog.md`, `docs/pm/09-jira-import-table.md`, `docs/pm/10-jira-import.csv` (to fix audit failures before recording passes)

**Interfaces:**
- Consumes: ENTIRE docs/pm/ file set + BOM.md (component inventory), EXPERIMENTS.md (EXP step coverage check), DECISIONS.md (ADR evidence check), design spec §11 10-item audit framework.

Procedure: go through the 10 audit items from design spec §11 + §10 4-part 3-way consistency. For each, record methodology, findings, and result ✅ or ❌. For ❌, record the fix (file changed, commit hash) then re-run to get ✅. The final file shows ALL 10 items as ✅.

10 audit items (repeat exact list from spec):
1. Every purchased component (use BOM.md as roster) → accounted for
2. Every EXP maps (16 existing + EXP-017) → no uncovered procedure steps
3. Every PENDING/DEFERRED ADR (ADR-016, ADR-017, ADR-024) → ≥1 evidence task each
4. 6 gated milestones → each has objective, enumerated DoD gates
5. Hidden assumptions from uncertainty register → each spawns explicit VERIFY task (check U-### vs backlog)
6. No task too large (steps ≤ 30 AND DoD criteria ≤ 8 per task)
7. Novice polarity safety: every energize/power-connect task preceded by corresponding verify-power-off task (walk top-to-bottom, verify order)
8. No work before decision: Cond-X PCB tasks skip-gated on ADR-016; MF detail tasks gated on MR completion
9. Shortest critical path validity: walk both chains M0→M1 from EXP-001 inventory complete → "Nano text visible on panel"; no node skipped, no assumed result
10. 3-way cross-file consistency (§10 4-part procedure: row counts, ID sets, field-by-field sample, sort order)

- [ ] **Step 1: Run all 10 audits. For each, record methodology, findings, result ✅/❌.** If ❌: go fix the source file (most often #06 backlog; if table/CSV mismatch, regenerate #09/#10 from fixed #06). Re-run until all 10 ✅.

- [ ] **Step 2: Write the audit file with all 10 passing, fix log, and timestamp.**

- [ ] **Step 3: Validation**

```bash
# 10 explicit audit items, each marked PASS/✅
passes=$(grep -cE "✅|PASS" docs/pm/11-coverage-audit.md)
echo "Audit PASS markers: $passes (expect ≥ 10, one per item + sub-checks)"
# 3-way consistency check results explicitly recorded
grep -A 10 "cross-file consistency" docs/pm/11-coverage-audit.md | head -15
```

- [ ] **Step 4: Commit**

```bash
git add docs/pm/11-coverage-audit.md docs/pm/05-backlog.md docs/pm/09-jira-import-table.md docs/pm/10-jira-import.csv
git commit -m "docs(pm): coverage audit (10/10 pass); fix any audit-discovered issues"
```

---

### Task 12: Write README index; final comprehensive review pass

**Files:**
- Create: `docs/pm/README.md`

**Interfaces:**
- Consumes: All 11 other files.
- Produces: Landing page for the tracker, navigation, operational instructions for humans using it.

Contents:
1. Title + 1-sentence purpose.
2. File map table (12 files with descriptions).
3. Quick start for a novice executor: "Step 1: Read `01-repository-audit.md` to understand what exists. Step 2: Read `04-milestone-graph.md` for the big picture. Step 3: Work `05-backlog.md` top-to-bottom. Step 4: If hardware hasn't shipped, start with M1 Nano software bootstrap tasks labeled READY + BLOCKED: DELIVERY = NO. Step 5: When MG is reached, run the post-decision reclassification sweep before continuing to M3."
4. Stable ID legend: where IDs are frozen, how to split (keep old, append new at end), how to obsolete (Conditional:yes + Skip condition, never delete).
5. READY/BLOCKED legend: the computation rule from design spec §6a; blocker comment categories DELIVERY/EXP/ADR.
6. Post-decision sweep: step-by-step HOW to apply the reclassification rule per AF-004's checklist.
7. Label legend: all 18 labels (14 JIRA.md base + 4 practical additions) explained.
8. How to mark a task completed in the backlog (insert a `- Status: COMPLETED YYYY-MM-DD by [person]; Evidence committed: <hash>` line into the task's Evidence section).
9. Link to the Coverage Audit (#12) — "Audit passed on 2026-08-19. If you modify anything, re-run items 1–10."

**Final comprehensive review pass (across all 12 files):**
Run ALL structural validations from Tasks 1–11 in ONE shell session; capture output as a verification log. Check specifically:
- All 12 files exist and non-empty.
- All cross-reference filenames exist (grep all `(filename#Lx-Ly)` patterns, confirm file is there).
- Stable IDs: `grep -oh "AF-[0-9]\+" docs/pm/05-backlog.md | sort -u | wc -l` equals backlog total task+epic count.
- No duplicate AF-### IDs: `grep -oh "AF-[0-9]\+" docs/pm/05-backlog.md | sort | uniq -d` returns nothing.
- No calendar estimates: zero false-positive hits on the duration grep.
- 20-field schema count: spot-check 10 random tasks for all 20 field headers present.
- 3-way consistency row counts match one final time.

- [ ] **Step 1: Write README.md.**

- [ ] **Step 2: Run comprehensive final review shell block above. Save output to `docs/pm/.final-review.log` (hidden file, untracked, for record).**

- [ ] **Step 3: Fix any final review issues.** Re-commit fixes.

- [ ] **Step 4: Commit README + any final fixes.**

```bash
git add docs/pm/README.md
git commit -m "docs(pm): add README index; final comprehensive review"
```

---

## Plan Self-Review (writing-plans skill checklist)

### 1. Spec coverage

Skim spec §2–15 and map each requirement → plan task:
- §2 all 11 JIRA deliverables + README → 12 files explicitly created, Task 12 writes final index. ✅
- §3 canonical backlog + derived files → Tasks 9/10 explicitly derive, never hand-edit derived. ✅
- §4 task boundary rule + extra-fine splits → M0/ESP32 tasks in Task 4 explicitly enumerated 12-stage + mains polarity splits. ✅
- §5 epics = 6 milestones (not EXP mirrors) → Task 3 epic definitions use M0-MF naming. ✅
- §6 20-field schema → every task in Tasks 4/5/6 includes all 20. ✅
- §6a READY/BLOCKED → computed from Labels + Blocked By, legend in README. ✅
- §9 critical path semantics → Task 7 legend + 3 views + reclassification rule verbatim. ✅
- §10 canonical rule + 4-part consistency check → Task 11 audit item 10 explicit. ✅
- §11 10-item audit → Task 11 procedure verbatim from spec. ✅
- §12 source-of-truth rules → encoded via uncertainty register + contradictions in Task 1 / Audit item 5. ✅
- §13 assumptions table → all Unverified assumptions map to VERIFY tasks in Uncertainty Register; Audit item 5 double-checks. ✅
- §14 safety rules → every mains/HUB75/CH340/high-current task has verbatim 8-point / no-hot-plug / no-CH340-power / no-Dupont-for-current safety text. ✅
- User 10 adjustments → all explicitly encoded into the plan's granularity / ID stability / epic restructure / etc. ✅

No gaps found.

### 2. Placeholder scan

Search plan for red flags: TBD? → Only the TASK-TO-BE-ASSIGNED placeholder in Task 1's uncertainty register, and Task 6 removes it. Not a plan failure, it's a deliberate intermediate state with explicit later resolution. "Write appropriate tests"? → Not applicable (documentation, no code tests). "Similar to Task N"? → Every task's content is self-contained; Task 9/10 share the "same 11 columns" language and both specify them explicitly, not "similar." No placeholders.

### 3. Type consistency

AF-### IDs: epics AF-001…AF-010 consistently. Uncertainty register references U-001 numbering consistently. EXP-001…EXP-016 + EXP-017 consistently. ADR-001…ADR-024 consistently. File references: docs/pm/01-... through 11-... + README ordering consistent. Task ordering: all 12 tasks reference backlog append operations that are sequential; no step assumes an AF-ID number assigned in a later task. No naming mismatches.

---

## Execution Handoff

Plan complete and saved to [2026-08-19-jira-project-tracker.md](file:///Users/jon.robbins/GitHub/ai-frame/docs/superpowers/plans/2026-08-19-jira-project-tracker.md). Two execution options:

**1. Subagent-Driven (recommended)** - I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** - Execute tasks in this session using executing-plans, batch execution with checkpoints

Which approach?
