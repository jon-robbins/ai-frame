# PM Backlog Migration Completion & Audit Re-Run Implementation Plan

> **⚠️ FROZEN 2026-08-19 — DO NOT EXECUTE.** Superseded by the compression-first refactor
> (`2026-08-19-compression-first-refactor.md`, spec: `reframe-plan.md`). Retained as reference
> for: historical ID inventory, verified factual corrections (HUB75 OUT→IN notation, EXP-006 vs
> EXP-012 scope, 19 KiCad Cond-X steps, AF-052 identity, MA identifier grounding), Jira generator
> requirements (for when export is eventually un-deferred), safety findings, and source-of-truth
> cleanup. Its task-granularity strategy — including the AF-188..AF-195 chain and Jira
> regeneration as a workstream — is superseded.

> **For agentic workers:** REQUIRED SUB-SKILL: Use subagent-driven-development (recommended) or executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Finish the PM tracker migration — migrate the last 62 historical backlog tasks into phase files, canonicalize the epics, reconcile support docs, regenerate the Jira artifacts from the new registry, re-run the 10-item coverage audit, and finalize the migration state.

**Architecture:** The canonical backlog now lives in 15 phase files under `docs/pm/backlog/` (compact task schema) plus an epic registry to be added to `backlog/README.md`. The monolith `docs/pm/05-backlog.md` becomes historical/superseded. `09-jira-import-table.md` and `10-jira-import.csv` are mechanical derivations regenerated from the phase registry by script. `refactor-map.md` records per-phase migration lineage; `11-coverage-audit.md` re-runs against the new registry.

**Tech Stack:** Markdown docs, grep/awk verification, one Python 3 script (`scripts/generate_jira_import.py`, stdlib only) for derivation and RFC-4180 validation.

**Spec:** This plan (the user's cleaned inventory of 2026-08-19 is fully incorporated, plus the 2026-08-19 plan review: seven blockers and one factual correction, all applied). Baseline: `agent/pm-tracker` pushed through `13e0f4b` on origin (MG+M3 refactor approved); executors start from that branch state.

## Global Constraints

- **Plan file stays untracked** (project memory hard constraint: never `git add docs/superpowers/`).
- **Stable IDs are frozen:** never renumber, reuse, or delete AF-### IDs. Obsolete tasks become `### AF-XXX — SUPERSEDED` (never exported to Jira). New IDs append from current max; next available at plan start = **AF-188**. Task 1 allocates AF-188..AF-195 (eight-task M4 third-controller chain), leaving next free = **AF-196**.
- **Compact task schema** (from `docs/pm/backlog/README.md` §3 and `docs/pm/runbooks/task-format.md`): metadata fields `Milestone:` `Depends on:` `Labels:` (Required); `Applies if:` `Safety:` `Stop condition:` `Resolves:` `Procedure:` `Wiring:` `Parts:` `Context:` (Optional); body sections `#### Do` (numbered), `#### Done when` (≤8 bullets), `#### If it fails`.
- **One independently verifiable outcome per task.** No bundled procurement+assembly outcomes (AF-099→AF-187→AF-100 precedent).
- **OR dependencies are machine-visible:** e.g. `Depends on: AF-110 OR AF-111` (precedent: AF-091, AF-104/105). Never bury prerequisites only in `Context:`.
- **Controller-conditional pre-decision representation** (M4/Cond-X): both branches defined; entries carry `Labels: ... conditional blocked blocked:adr-016` + controller label + `Applies if:`. The AF-096 sweep follows the **approved two-class winner/loser model verbatim** (AF-096 Do steps 3–4; do NOT amend AF-096 or invent a third class): winner — remove `conditional`, `blocked`, `blocked:adr-016`, remove `Applies if`, retain controller label, add `critical-path`; loser — exact skip line `Skip — ADR-016 selected WF4` (multi-ESP32 loses) or `Skip — ADR-016 selected multi-ESP32` (WF4 loses). No `[other controller]` placeholders. ADR-013's PCB prerequisites (hardware validated, architecture selected, measured board, GPIO-map validated) are **dependencies, not a second decision gate**: Cond-X carries the sole clause `Applies if: ADR-016 selects multi-ESP32` (per JIRA.md:459 "if and only if") with the prerequisites encoded in `Depends on` — so on an ESP32 win, Cond-X activates once its dependencies complete.
- **Safety encoding:** every energize/connect task names a `Safety:` profile plus a task-specific `Stop condition:`. Tasks **reference** the named profiles in `docs/pm/runbooks/safety.md` (MAINS, HUB75, CH340, 5V-HIGH-CURRENT) — never repeat verbose checklists (`task-format.md` line 84 forbids them). HUB75 no-hot-plug; CH340 no-power-routing; 5V-HIGH-CURRENT no-Dupont-for-panel-current; a polarity-verify task precedes every energize.
- **No calendar estimates, no invented thresholds** (no sync delta cutoffs, no invented temp limits), **no unverified hardware facts** (no unpurchased-hardware assertions).
- **Derived files:** never hand-edit `09-jira-import-table.md`/`10-jira-import.csv`; regenerate both atomically (Task 8). Do not edit `05-backlog.md` content except its title, source-of-truth paragraph, and supersession banner (Task 10).
- **Counts (authoritative, from the monolith-era derived 09 table's Epic column):** remaining = 62 tasks — M4: 11 (AF-109..AF-119), MR: 2 (AF-120, AF-121), MA: 12 (AF-122..AF-133), MF: 18 (AF-134..AF-151), Cond-X: 19 (AF-152..AF-170). The earlier "65 vs 68" discrepancy resolves to 62. Task 1 additionally allocates AF-188..AF-195 (eight third-controller tasks mirroring the M1 adapter-stage decomposition), so M4 lands as 19 tasks.
- **Jira artifact schema is owned by `docs/JIRA.md`** (11 columns: `Issue Type,Epic,Task ID,Summary,Milestone,Depends On,Labels,Applies If,Context,Acceptance Criteria,Procedure / References` — JIRA.md:614). The existing `09`/`10` files are stale monolith-era derivatives with a different column set; Task 8 replaces both to the JIRA.md schema.
- **Experiment scope facts:** EXP-006 is the WF4 six-panel 256×192 M4 experiment. EXP-012 is the ESP32 two-panel 256×64 **row** experiment — per-row evidence only, never cited as an M4 procedure; the ESP32 M4 architecture integrates three proven rows under the AF-119 gate.
- **Do not regenerate Jira until Tasks 1–6 are complete** (user's gate).
- Commits go on branch `agent/pm-tracker`, message prefix `docs(pm):`, one commit per plan task.

## Transformation Recipe (applies to Tasks 1–5)

For each historical task `### AF-NNN: Summary` in `docs/pm/05-backlog.md`:

1. Extract the 20-field monolith block (Summary, Description, Why-this-task-exists, Prerequisites/Depends, Labels, Required hardware/tools/software, Exact steps, Expected result/DoD, Safety, Uncertainties, Failure response, Source refs).
2. Rewrite into the compact schema. Field mapping:
   - `Milestone:` ← target milestone (M4/MR/MA/MF/Cond-X).
   - `Depends on:` ← monolith Prerequisites, keeping AF-### IDs stable; render alternatives as `X OR Y`; replace prose with IDs only.
   - `Labels:` ← monolith Labels filtered to the taxonomy in `05-backlog.md` line 46 (`software hardware firmware mechanical docs validation spike safety-review delivery-review decision critical-path blocked blocked:delivery blocked:exp blocked:adr controller-wf4 controller-esp32 controller-wf2 nano pcb conditional power purchasing thermal thermal-review polarity-verify`). Use `purchasing` (never `procurement`). Controller-conditional entries additionally get `conditional blocked blocked:adr-016`.
   - `Safety:`/`Stop condition:` ← from monolith Safety; mandatory whenever the task energizes or connects power/HUB75.
   - `Procedure:` ← EXP reference (EXP-006 for WF4 six-panel M4; EXP-015/016 where applicable). EXP-012 is ESP32 per-row evidence — cite it as such in `Context:`, never as an M4 `Procedure:`.
   - `Resolves:` ← U-### from `03-uncertainty-register.md` if the monolith block cites one.
   - `Context:` ← one sentence from Why-this-task-exists.
   - `Do` ← Exact steps (≤30 steps; split if more — but prefer folding, historical steps are already ≤30).
   - `Done when` ← Expected result/DoD (≤8 bullets; consolidate without losing verifiability).
   - `If it fails` ← Failure response.
3. Ground facts against current sources, correcting stale claims: `docs/JIRA.md` (Milestone 4, §Software Planning, §Mounted Frame Phase), `docs/EXPERIMENTS.md` (EXP-006/012/015/016 + Standard Test Pattern Suite/Defect Checklist), `docs/DECISIONS.md` (ADR-002, ADR-006, ADR-007, ADR-011, ADR-013, ADR-014, ADR-024), `docs/PROJECT.md`, `docs/BOM.md`. The stale `docs/pm/02-requirements-matrix.md` is excluded as authority.
4. If the monolith task bundles audit+procurement+assembly for missing hardware (M4 third controller), decompose per the approved M3 chain: audit (READY/MISSING) → resolve (verify/purchase/build) → assemble.

**Worked example** (pattern only; do all fields for every task):

```markdown
### AF-110 — Assemble WF4 2×3 six-panel topology

**Milestone:** M4
**Depends on:** AF-109, AF-108
**Labels:** hardware controller-wf4 power validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects WF4; this task supplies the selected controller's three-row topology.
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** De-energize the PSU, controller, and panels before any HUB75 or panel-power connection change; never hot-plug.
**Procedure:** EXP-006

#### Do
1. (from monolith AF-110 Exact steps, with X1/X2/X3 row chains written in explicit OUT→IN hops)
...

#### Done when
- (from monolith Expected result, ≤8 verifiable bullets)
...

#### If it fails
- (from monolith Failure response)
```

---

### Task 1: Migrate M4 into `10-six-panel.md` (+ AF-188..AF-195 decomposition)

**Files:**
- Modify: `docs/pm/backlog/10-six-panel.md` (currently a stub)
- Modify: `docs/pm/refactor-map.md` (append PART VI)
- Modify: `docs/pm/backlog/README.md` line 18 (next available ID → AF-196)

**Interfaces:**
- Consumes: monolith `05-backlog.md` blocks `### AF-109:` .. `### AF-119:`; approved M3 semantics from `09-quad-panel.md` (AF-099/AF-187/AF-100 chain, AF-096 sweep); M1 adapter-stage outcomes AF-054/AF-055/AF-059/AF-061/AF-065/AF-066 (the mirroring source for the third-controller chain).
- Produces: 19 compact-schema tasks (AF-109..AF-119 + new AF-188..AF-195); refactor-map PART VI (`## 18.` migration matrix + `## 19.` factual audit); registry next-ID update. Later tasks rely on: M4 gate AF-119, framebuffer task AF-112, thermal AF-116, recovery AF-117 (referenced by MR/MF/support docs), and the third-controller chain AF-188→...→AF-195→AF-111 (referenced by Task 8/9 checks).

- [ ] **Step 1: Verify the ID inventory before writing**

Run: `awk -F'|' '/^\| Task \| AF-006/{print $4}' docs/pm/09-jira-import-table.md | tr -d ' '`
Expected: AF-109..AF-119 (11 IDs). If different, stop and reconcile before proceeding.

- [ ] **Step 2: Write the phase-file header + pre-decision note**

Model on `09-quad-panel.md` lines 1–5: phase intro sentence, then the AF-096 normalization paragraph listing this file's conditional entries — the branch-specific work only: **AF-110, AF-111, and the ESP32-only third-controller chain AF-188..AF-195** — and stating the sweep is registry-wide with these as current known M4 targets. AF-112 and AF-113 are controller-independent convergence tasks (AF-112 depends on `AF-110 OR AF-111`; AF-113 depends on AF-112) and are explicitly **outside** the sweep. Milestone: M4; gate: AF-119 (six-panel 256×192, all 5 seams, arbitrary content, brightness, EXP-015 ×4 levels, EXP-016 ×5 cycles, Wi-Fi/API recovery — from `backlog/README.md` §4 M4 row and JIRA.md Milestone 4).

- [ ] **Step 3: Migrate the controller-independent tasks**

AF-109 (retrieve/verify panels #5/#6 unpowered — polarity-verify before any energize), AF-112 (Nano 256×192 framebuffer + 3-row crop; `Depends on: AF-110 OR AF-111` machine-visible), AF-113 (first-light full test-pattern suite), AF-114 (5-seam verification: 3 vertical + 2 horizontal), AF-115 (brightness sweep 25/50/75/100 feeding MR ceiling), AF-116 (EXP-015 loaded thermal ×4 levels), AF-117 (EXP-016 ×5 power-cycle recovery), AF-118 (Wi-Fi recovery + API-failure fallback), AF-119 (M4 gate aggregator with explicit convergence `Depends on: AF-114, AF-116, AF-118` — seam validation, brightness/thermal, and recovery/Wi-Fi/API paths are machine-visible prerequisites; explicitly does not begin MR).

- [ ] **Step 4: Migrate controller branches + decompose the third-controller bundle**

Atomicity per JIRA.md Task Granularity Rules: the M1 build deliberately separated the adapter into independently verifiable stages (AF-054 mounting, AF-055 power/decoupling, AF-059 RGB/A/B, AF-061 C/D/E/timing, AF-065 continuity, AF-066 flash), so adapter #3 gets the same decomposition — not one bundled build task. All eight new tasks carry `conditional blocked blocked:adr-016` + `controller-esp32`. Chain: `AF-188 audit → AF-189 acquire → AF-190 mount → AF-191 power/control → AF-192 RGB/A/B → AF-193 C/D/E/timing → AF-194 continuity → AF-195 flash/configure → AF-111 assemble`.

- AF-110 (WF4 X1/X2/X3 three-row topology; `Depends on: AF-109, AF-108`; 6 parallel power branches; chains in explicit OUT→IN hops like `WF4 X1 → panel #1 IN and panel #1 OUT → panel #2 IN`).
- AF-111 becomes the ESP32 2×3 assembly only (ctrl#1/#2/#3 → one row each; `Depends on: AF-195, AF-109`).
- New **AF-188 — Audit third ESP32 controller-path readiness** (mirrors AF-099): `Depends on: AF-108`; audit ESP32/HCT inventory vs the two proven controllers + needed third; complete with exactly one outcome — `READY` (identified board + adapter components) or `MISSING` (exact items recorded) in `docs/pm/evidence/AF-188-third-controller-readiness.md`; never waits for procurement.
- New **AF-189 — Acquire and verify third ESP32 controller-path hardware** (mirrors AF-187's hardware scope): `Depends on: AF-188`; Labels `... purchasing`; on `READY` verify the identified items in place; on `MISSING` order per BOM/ADR-013 staged-purchase rule, receive, verify de-energized (board identity, adapter component identity, no damage), photograph, update inventory; document board identity, firmware baseline, and intended bottom-row role in `docs/pm/evidence/AF-189-third-controller-hardware.md`. No adapter build, no wiring, no flashing, no energizing.
- New **AF-190 — Adapter #3 Stage 1: perfboard layout & IC/header mounting** (mirrors AF-054): `Depends on: AF-189`; Labels `... hardware`; layout + mount the HCT245 ICs and HUB75 connector on perfboard per the ADR-014 contents; unpowered mechanical outcome only.
- New **AF-191 — Adapter #3 Stage 2: power, ground, decoupling & DIR/OE control wiring** (mirrors AF-055): `Depends on: AF-190`; `Safety: 5V-HIGH-CURRENT` + stop condition (mirroring AF-055's: stop and disconnect immediately on abnormal heat/smell/sound); solder power/ground rails, 100 nF decoupling per IC, DIR/OE control wiring; unpowered.
- New **AF-192 — Adapter #3 Stage 3: RGB data & row-address A/B wiring** (mirrors AF-059): `Depends on: AF-191`; `Safety: HUB75` + stop condition (disconnect panel/controller power before changing HUB75 wiring); R1/R2/G1/G2/B1/B2 + A/B signal wiring; unpowered.
- New **AF-193 — Adapter #3 Stage 4: row-address C/D/E & control timing wiring** (mirrors AF-061): `Depends on: AF-192`; `Safety: HUB75` + stop condition (same as AF-192); C/D/E, LAT, OE, CLK wiring; unpowered.
- New **AF-194 — Adapter #3 Stage 5: full unpowered continuity & isolation audit** (mirrors AF-065): `Depends on: AF-193`; `Safety: HUB75` + stop condition (same as AF-192); the 14-signal HUB75 continuity check plus short/isolation verification; Done when the audit passes and the adapter is labeled/photographed.
- New **AF-195 — Flash and configure ESP32 controller #3**: `Depends on: AF-194, AF-108` (AF-108 transitively proves the selected M3 implementation). Flash controller #3 with the **exact proven firmware/library version, validated GPIO mapping, 256×64 row configuration, and relevant settings used by the passing ESP32 M3 path recorded by AF-103/AF-108** — AF-066 is lineage/baseline evidence only, not a permanent firmware constraint; if M3 testing changed the configuration, the M3-proven one wins. Flash through the ESP32's normal USB interface; capture serial initialization; record identity/firmware/config in the AF-189 evidence file. No panel connection, no `Safety: CH340` for the flash itself. If a CH340 adapter is separately attached to capture UART logs on the USB-powered board, apply the CH340 profile with VCC/5V/3.3V disconnected — TX/RX/GND only.

- [ ] **Step 5: Add refactor-map PART VI**

Append after PART V: `# PART VI — Milestone 4 (M4) Migration Matrix, Factual Audit & Traceability` with `## 18. M4 Migration Matrix` (intro paragraph + per-ID table rows for AF-109..AF-119 with `KEEP / CORRECT` dispositions, `ADD` for AF-188..AF-195 — noting they mirror the surviving M1 adapter-stage outcomes AF-054/055/059/061/065/066 per JIRA.md task-granularity rules — and the OR-dependency note on AF-112) and `## 19. M4 Factual Assertion Audit` (assertions: 2×3 topology + 6 parallel branches per JIRA Milestone 4/ADR-007; ADR-002 canonical 256×192 framebuffer final implementation in AF-112; EXP-006 is the WF4 M4 procedure while EXP-012 is ESP32 per-row evidence only — the ESP32 M4 architecture integrates three proven rows, it does not re-run EXP-012 as an M4 procedure; EXP-015 belongs to M4 with MR consuming results; EXP-016 ×5 cycles; Wi-Fi/API recovery criteria from EXP-016 Success Criteria; the approved two-class winner/loser sweep applies to AF-110, AF-111, and AF-188..AF-195; AF-112/AF-113 are convergence tasks outside the sweep).

- [ ] **Step 6: Update registry + verify**

Update `docs/pm/backlog/README.md` line 18: `**Next Available ID:** \`AF-196\``.

Verification (all must pass):

```bash
test "$(grep -c '^### AF-' docs/pm/backlog/10-six-panel.md)" -eq 19
for i in 109 110 111 112 113 114 115 116 117 118 119 188 189 190 191 192 193 194 195; do grep -q "^### AF-$i " docs/pm/backlog/10-six-panel.md || echo "MISSING AF-$i"; done
grep -n 'Depends on: AF-110 OR AF-111' docs/pm/backlog/10-six-panel.md
grep -n 'READY.*MISSING\|MISSING.*READY' docs/pm/backlog/10-six-panel.md
grep -n '^# PART VI' docs/pm/refactor-map.md
# every energize/connect task carries Safety + Stop condition (profiles, not checklists):
test "$(grep -c 'Safety:' docs/pm/backlog/10-six-panel.md)" -le "$(grep -c 'Stop condition' docs/pm/backlog/10-six-panel.md)"
```

Expected: no MISSING output; OR dependency present exactly once; PART VI present; `Stop condition` count ≥ `Safety:` count.

- [ ] **Step 7: Commit**

```bash
git add docs/pm/backlog/10-six-panel.md docs/pm/refactor-map.md docs/pm/backlog/README.md
git commit -m "docs(pm): populate M4 six-panel backlog phase with AF-188..195 third-controller decomposition"
```

### Task 2: Migrate Cond-X into `13-esp32-pcb.md` (+ README 19-step fix)

**Files:**
- Modify: `docs/pm/backlog/13-esp32-pcb.md` (stub)
- Modify: `docs/pm/backlog/README.md` §4 Cond-X row (18 → 19 KiCad steps)
- Modify: `docs/pm/refactor-map.md` (append PART VII)

**Interfaces:**
- Consumes: monolith blocks `### AF-152:` .. `### AF-170:` (19 tasks); ADR-013 (PCB promotion prerequisites), ADR-014 (HCT buffering), ADR-021/022; JIRA.md:459 "if and only if the ESP32 architecture is selected" + its enumerated 19 PCB steps.
- Produces: 19 compact tasks; refactor-map PART VII (`## 20.`, `## 21.`); corrected Cond-X gate row (consumed by Task 7's 06-critical-path rewrite and Task 9's audit item #8).

- [ ] **Step 1: Fix the Cond-X gate row in `backlog/README.md`**

`backlog/README.md` §4 line 85 currently says "18 KiCad steps"; JIRA.md §Conditional PCB Work explicitly enumerates **19**. Change `18 KiCad steps` → `19 KiCad steps` (content of the parenthetical stays).

- [ ] **Step 2: Write the phase header with whole-phase conditional semantics**

Intro + a pre-decision note distinct from M3/M4's per-task branching: **every** Cond-X task carries `conditional blocked blocked:adr-016 pcb` + the sole clause `Applies if: ADR-016 selects multi-ESP32` (JIRA.md:459 "if and only if"). The ADR-013 promotion prerequisites (first ESP32 proven, architecture selected, board measured, GPIO map validated) are **explicit dependencies, not part of the Applies-if condition and not a second decision gate**: the phase entry task gets `Depends on: AF-096, AF-108, AF-052` (AF-052 — Validate provisional GPIO mapping against physical board silkscreen; cite it in `Context:` against ADR-013's prerequisites). Resolution follows the **approved two-class AF-096 model unchanged**: WF4 wins → every entry gets the loser skip line; multi-ESP32 wins → standard winner mutation per AF-096 Do step 3, and Cond-X becomes active once its dependency prerequisites complete. Gate definition from `backlog/README.md` §4 Cond-X row (19 KiCad steps: schematic ERC, layout DRC, Gerber/BOM/CPL, fabrication, continuity, row validation, perfboard swap).

- [ ] **Step 3: Migrate the 19 tasks**

Preserve AF-152..AF-170 in monolith order (schematic symbol/footprint choices → schematic capture → ERC → layout → DRC → Gerber/BOM/CPL → fab order → receipt/continuity → row validation → swap-out). Add `Parts:`/`Wiring:` fields where the monolith cites KiCad artifacts or `hardware/schematics/` targets. Keep `pcb` label. Mains/5V tasks keep Safety encoding. Dependencies stay within-phase except entry tasks depending on ADR-016-gated predecessors (per monolith Prerequisites).

- [ ] **Step 4: Add refactor-map PART VII**

`# PART VII — Cond-X (Conditional ESP32 PCB) Migration Matrix & Factual Audit`, `## 20. Cond-X Migration Matrix` (19 `KEEP / CORRECT` rows; whole-phase resolution semantics paragraph: loser skip line on WF4 win, standard winner mutation + dependency-gated activation on ESP32 win, per the approved AF-096 text — no third sweep class; note the AF-096 sweep must enumerate every Cond-X entry as M3+ ADR-016-conditional work — registry-driven enumeration already covers it), `## 21. Cond-X Factual Assertion Audit` (assertions: JIRA.md's "if and only if ESP32 selected" + 19 enumerated steps, matching the corrected README gate row; ADR-013 promotion prerequisites encoded as dependencies, not an Applies-if compound; ADR-014 HCT contents 2× SN74HCT245N + 2× 100 nF + keyed HUB75E + bulk cap optional; ADR-021 rejected integrated boards; perfboard swap-out is the completion outcome).

- [ ] **Step 5: Verify + commit**

```bash
test "$(grep -c '^### AF-' docs/pm/backlog/13-esp32-pcb.md)" -eq 19
for i in $(seq 152 170); do grep -q "^### AF-$i " docs/pm/backlog/13-esp32-pcb.md || echo "MISSING AF-$i"; done
grep -c 'conditional blocked blocked:adr-016' docs/pm/backlog/13-esp32-pcb.md   # = 19
grep -c 'Applies if: ADR-016 selects multi-ESP32' docs/pm/backlog/13-esp32-pcb.md   # = 19 (sole clause)
grep -n '19 KiCad steps' docs/pm/backlog/README.md
grep -n '18 KiCad' docs/pm/backlog/README.md   # expect no match
grep -n '^# PART VII' docs/pm/refactor-map.md
```

```bash
git add docs/pm/backlog/13-esp32-pcb.md docs/pm/backlog/README.md docs/pm/refactor-map.md
git commit -m "docs(pm): populate Cond-X ESP32 PCB backlog phase; fix gate to 19 KiCad steps"
```

### Task 3: Migrate MR into `11-reliability.md`

**Files:**
- Modify: `docs/pm/backlog/11-reliability.md` (stub)
- Modify: `docs/pm/refactor-map.md` (append PART VIII)

**Interfaces:**
- Consumes: monolith blocks `### AF-120:` and `### AF-121:`; EXP-015/016 results (produced by M4 AF-116/AF-117); ADR-024 (DEFERRED frame decision).
- Produces: 2 compact tasks; PART VIII (`## 22.`, `## 23.`). MF tasks (Task 5) depend on MR completion; ADR-024 inputs resolve here.

- [ ] **Step 1: Migrate the 2 tasks**

AF-120 — brightness ceiling calibration: derive dashboard-acceptable ceiling from AF-115 sweep + EXP-015 thermal data; record the ceiling value and its evidence; `Depends on: AF-119`. AF-121 — 24-hour continuous unattended burn-in at dashboard-normal content at the calibrated ceiling; record crash/thermal/artifact outcome; `Depends on: AF-120`. Both: no invented thermal thresholds — pass criteria reference the recorded ceiling and observed stability only. Add `Resolves:` U-### if `03-uncertainty-register.md` cites AF-120/121.

- [ ] **Step 2: Add refactor-map PART VIII**

`## 22. MR Migration Matrix` (2 rows; MR is a Review Gate not a gated milestone — brightness ceiling + 24h burn-in per `backlog/README.md` §4), `## 23. MR Factual Assertion Audit` (assertions: ceiling is calibrated from measured data not asserted; ADR-024 DEFERRED consumes MR thermal + dimension inputs; MF remains blocked until MR passes).

- [ ] **Step 3: Verify + commit**

```bash
test "$(grep -c '^### AF-' docs/pm/backlog/11-reliability.md)" -eq 2
grep -n 'Depends on: AF-119' docs/pm/backlog/11-reliability.md
grep -n 'Depends on: AF-120' docs/pm/backlog/11-reliability.md
grep -n '^# PART VIII' docs/pm/refactor-map.md
```

```bash
git add docs/pm/backlog/11-reliability.md docs/pm/refactor-map.md
git commit -m "docs(pm): populate MR reliability backlog phase"
```

### Task 4: Migrate MA into `12-application.md`

**Files:**
- Modify: `docs/pm/backlog/12-application.md` (stub)
- Modify: `docs/pm/refactor-map.md` (append PART IX)

**Interfaces:**
- Consumes: monolith blocks `### AF-122:` .. `### AF-133:` (12 tasks = JIRA §Software Planning stages 1–12); AF-119 physical baseline.
- Produces: 12 compact tasks; PART IX (`## 24.`, `## 25.`). Task 7's 07-experiment-coverage reconciliation references stage tasks; MA gate row in §4.

- [ ] **Step 1: Migrate the 12 stage tasks**

Map AF-122..AF-133 to stages 1–12 preserving monolith order and dependencies (notably AF-129-style concrete-transport task gating on the M4 winner; unit-test tasks keep their test commands in `Do`). All `Labels:` include `software nano` as applicable; no hardware Safety fields except where a stage energizes the display (then HUB75 encoding). **Monolith code identifiers (e.g. `testpatterns.generate(...)`, `Framebuffer`, `config.py`) are historical proposals, not contracts** — `software/app` currently contains only `.gitkeep` and JIRA.md says software is immature. Keep an identifier only where a canonical source (JIRA.md §Software Planning or existing code) independently verifies it; otherwise describe the outcome and mark the concrete naming as a decision for the implementing task.

- [ ] **Step 2: Add refactor-map PART IX**

`## 24. MA Migration Matrix` (12 rows, stage mapping column), `## 25. MA Factual Assertion Audit` (assertions: 12 JIRA stages complete = MA gate; offline-fallback supervision operational; renderer stays controller-agnostic per ADR-002 — no `if wf4/esp32` in renderer code, transport owns dispatch).

- [ ] **Step 3: Verify + commit**

```bash
test "$(grep -c '^### AF-' docs/pm/backlog/12-application.md)" -eq 12
for i in $(seq 122 133); do grep -q "^### AF-$i " docs/pm/backlog/12-application.md || echo "MISSING AF-$i"; done
grep -n '^# PART IX' docs/pm/refactor-map.md
```

```bash
git add docs/pm/backlog/12-application.md docs/pm/refactor-map.md
git commit -m "docs(pm): populate MA application-software backlog phase"
```

### Task 5: Migrate MF into `14-mounted-frame.md`

**Files:**
- Modify: `docs/pm/backlog/14-mounted-frame.md` (stub)
- Modify: `docs/pm/refactor-map.md` (append PART X)

**Interfaces:**
- Consumes: monolith blocks `### AF-134:` .. `### AF-151:` (18 tasks = JIRA §Mounted Frame Phase items 1–18 + gate); MR completion; ADR-024.
- Produces: 18 compact tasks; PART X (`## 26.`, `## 27.`). Task 9 audit item #8 (MF gated on MR) consumes this.

- [ ] **Step 1: Migrate the 18 tasks**

Preserve items 1–18 in order: component dimensions, layout sketch X/Y/clearance/airflow, minimum frame depth, PSU location/air holes, controller/Nano mounting, mains/low-voltage separation barrier, cable routing + service loops, strain relief ×4 categories, PE bonding ×3 categories, ventilation/fan decision, closed-box thermal at ceiling, closed-box Wi-Fi, mechanical mounting/alignment, backplate build, aesthetic/wall hardware, 72h wall test, gate AF-151 aggregator. **Every mains task gets `Safety: MAINS` + a task-specific `Stop condition:` (e.g. exact de-energize/unplug state before mechanical work on live-side parts) — reference the runbook profile, never repeat the verbose 8-point checklist** (per `docs/pm/runbooks/safety.md` purpose statement and `task-format.md` line 84; precedent: `01-power-bringup.md` profile style). Experiment prerequisites are expressed in the **dependency graph** (`Depends on:` the MR tasks AF-120/AF-121 where the monolith gates on ceiling/dims delivery) using only the normalized `blocked:exp` label — never monolith-era experiment-specific labels like `blocked:exp-exp-015`. AF-151 gate explicitly ends V1 proof-of-concept; `Depends on:` chain ends at the 72h task.

- [ ] **Step 2: Add refactor-map PART X**

`## 26. MF Migration Matrix` (18 rows; note PE bonding and strain-relief category coverage per ADR-006/safety runbooks), `## 27. MF Factual Assertion Audit` (assertions: all 18 JIRA MF items have tasks; mains safety encoding present on every AC task; 72h unattended test is the final gate; ADR-024 inputs — dims/thermal/ventilation — resolve here).

- [ ] **Step 3: Verify + commit**

```bash
test "$(grep -c '^### AF-' docs/pm/backlog/14-mounted-frame.md)" -eq 18
for i in $(seq 134 151); do grep -q "^### AF-$i " docs/pm/backlog/14-mounted-frame.md || echo "MISSING AF-$i"; done
grep -c 'Safety: MAINS' docs/pm/backlog/14-mounted-frame.md   # every AC-side task
grep -n '^# PART X' docs/pm/refactor-map.md
```

```bash
git add docs/pm/backlog/14-mounted-frame.md docs/pm/refactor-map.md
git commit -m "docs(pm): populate MF mounted-frame backlog phase with mains safety encoding"
```

### Task 6: Canonicalize the epic registry in `backlog/README.md`

**Files:**
- Modify: `docs/pm/backlog/README.md` (insert §1A after §1)

**Interfaces:**
- Consumes: monolith `05-backlog.md` lines 55–270 (`## Epic AF-001 — ...` .. `## Epic AF-010 — ...` blocks with Scope summary + Exit/gate criteria).
- Produces: canonical epic registry. Task 8's script parses it as the epic source; the final derivation no longer depends on the monolith.

- [ ] **Step 1: Insert §1A Epic Registry**

New section `## 1A. Epic Registry (AF-001..AF-010 — Canonical)` immediately after §1 (numbering "1A" avoids breaking existing §2–§9 cross-references in other documents). One row per epic in a markdown table with columns exactly: `| Epic ID | Name | Milestone | Scope summary | Exit / gate criteria |`. Content: condensed from the monolith blocks, facts unchanged (scope summaries trimmed to essentials; gate criteria keep their enumerated DoD references, e.g. M1's "at least one controller subtrack passes AF-080", AF-010's "Skip if ADR-016 selects WF4"). Add one sentence above the table: “This section is the canonical home of the epic definitions; `docs/pm/05-backlog.md` §1 is historical.”

- [ ] **Step 2: Update §1 ID rules**

Amend the "Authoritative Mapping" bullet: task location lives in the phase files; epic definitions live in §1A; the monolith `05-backlog.md` is historical lineage only.

- [ ] **Step 3: Verify + commit**

```bash
grep -n '^## 1A. Epic Registry' docs/pm/backlog/README.md
test "$(grep -cE '^\| AF-[0-9]{3} \|' docs/pm/backlog/README.md)" -eq 10
for i in $(seq 1 10); do grep -qE "^\| AF-$(printf '%03d' $i) \|" docs/pm/backlog/README.md || echo "MISSING epic AF-$(printf '%03d' $i)"; done
```

```bash
git add docs/pm/backlog/README.md
git commit -m "docs(pm): canonicalize epic registry AF-001..AF-010 in backlog README"
```

### Task 7: Reconcile PM support documents

**Files:**
- Modify: `docs/pm/06-critical-path.md`
- Modify: `docs/pm/07-experiment-coverage.md`
- Modify: `docs/pm/08-adr-coverage.md`
- Modify: `docs/pm/03-uncertainty-register.md`
- Modify: `docs/pm/04-milestone-graph.md`
- Modify: `docs/pm/README.md`

**Interfaces:**
- Consumes: completed phase registry (Tasks 1–6).
- Produces: support docs consistent with the registry. Task 9's audit consumes these; Task 8 does not depend on them (order chosen so reconciliation is reviewable before regeneration).

- [ ] **Step 1: `06-critical-path.md`**

Replace §6's monolith-line references (`05-backlog.md:4007-4031`) and the old six-step text with the registry-driven AF-096 semantics (approved two-class winner/loser model; quote `08-architecture-decision.md` AF-096 Do steps 1–5; ADR-013 prerequisites for Cond-X encoded as dependencies). Update View C gate nodes to phase-file locations. Keep the "human must apply the sweep" warning. Replace every "walk the rows of 05-backlog.md" instruction with "walk every M3+ controller-specific/ADR-016-conditional entry in `docs/pm/backlog/` phase files".

- [ ] **Step 2: `07-experiment-coverage.md`**

Re-point every `Covering AF-###` cell to the phase-file location (add a Location column: `backlog/NN-*.md`). Add EXP-017 covering tasks AF-097..AF-108 + AF-187. Add rows with **architecture-correct mapping**: EXP-006 (HD-WF4 → Full 256×192) procedure steps → the WF4/M4 tasks (AF-109, AF-110, AF-112..AF-119 as applicable to its procedure); the ESP32 M4 path (AF-188..AF-195 + AF-111) is **M4 integration work informed by EXP-011/012/013 and the accepted architecture — not EXP-006 coverage**. EXP-012's direct procedure coverage remains with the M2 ESP32 256×64 tasks; AF-188..AF-195 and AF-111 consume/reuse that proven per-row evidence during M4 integration but are **not additional EXP-012 procedure coverage**. EXP-015/016 → AF-116/AF-117/AF-120/AF-121. Keep "0 uncovered procedure steps" claim true — verify each EXP's procedure steps map to a task without assigning an experiment to the wrong architecture.

- [ ] **Step 3: `08-adr-coverage.md`**

ADR-016 row: evidence tasks = AF-093 (matrix), AF-094 (decide/record/accept), resolves at MG. ADR-017 row: AF-095. ADR-024 row: evidence = MR thermal + MF dimension tasks (AF-120/AF-121, AF-134+). ADR-013/014 rows: Cond-X tasks AF-152..AF-170 + the AF-188..AF-195 third-controller chain (adapter stages AF-190..AF-194, flash AF-195). Update "Milestone at which it resolves" column against the phase files.

- [ ] **Step 4: `03-uncertainty-register.md`**

For each `Resolving AF-###`: confirm the ID exists in exactly one phase file (SUPERSEDED entries must re-point to their replacement). Re-point any resolver that landed in a merged/decomposed task (e.g. monolith AF-013-era receipt tasks → AF-174..AF-180; second-controller unknowns → AF-099/AF-187). Zero `TASK-TO-BE-ASSIGNED` strings remain.

- [ ] **Step 5: `04-milestone-graph.md` + `docs/pm/README.md`**

04: verify both mermaid views match the migrated DAG (gate IDs unchanged: AF-080/091/AF-096-MG/AF-108/AF-119/AF-151; Cond-X dashed conditional). README: rewrite File Map so `docs/pm/backlog/` (15 phase files + README) is canonical for tasks and epics; `05-backlog.md` marked historical; §9 drift warning now keys on phase-file edits → Task 8 regeneration; quick-start step "work backlog top-to-bottom" re-points to phase files.

- [ ] **Step 6: Verify + commit**

```bash
grep -c '05-backlog.md:' docs/pm/06-critical-path.md          # = 0 (line refs gone)
grep -n 'registry-driven\|phase file' docs/pm/06-critical-path.md | head -3
grep -c 'TASK-TO-BE-ASSIGNED' docs/pm/03-uncertainty-register.md   # = 0
grep -n 'AF-187' docs/pm/07-experiment-coverage.md
grep -n 'AF-094' docs/pm/08-adr-coverage.md
grep -n 'historical' docs/pm/README.md
```

```bash
git add docs/pm/06-critical-path.md docs/pm/07-experiment-coverage.md docs/pm/08-adr-coverage.md docs/pm/03-uncertainty-register.md docs/pm/04-milestone-graph.md docs/pm/README.md
git commit -m "docs(pm): reconcile support docs with phase-file registry"
```

### Task 8: Regenerate Jira artifacts from the phase registry

**Files:**
- Create: `scripts/generate_jira_import.py`
- Modify (script-written): `docs/pm/09-jira-import-table.md`, `docs/pm/10-jira-import.csv`

**Interfaces:**
- Consumes: `docs/pm/backlog/README.md` §1A (epics) + `docs/pm/backlog/*.md` (tasks, excluding SUPERSEDED).
- Produces: 09 (11-column markdown) + 10 (RFC-4180 CSV) from one in-memory collection. Task 9's audit item #10 consumes both.

- [ ] **Step 1: Write the generator script**

```python
#!/usr/bin/env python3
"""Regenerate 09-jira-import-table.md and 10-jira-import.csv from docs/pm/backlog/.

Column schema is owned by docs/JIRA.md (the Jira Import Table):
Issue Type,Epic,Task ID,Summary,Milestone,Depends On,Labels,Applies If,Context,Acceptance Criteria,Procedure / References
"""
import csv, io, re
from pathlib import Path

ROOT = Path(__file__).resolve().parents[1]
BACKLOG = ROOT / "docs/pm/backlog"
README = (BACKLOG / "README.md").read_text(encoding="utf-8")
PHASES = sorted(p for p in BACKLOG.glob("*.md") if p.name != "README.md")

# --- epics from README §1A table: | AF-ID | Name | Milestone | Scope | Exit gate |
epics = []
sec = README.split("## 1A. Epic Registry", 1)[1].split("\n## ", 1)[0]
for m in re.finditer(r"^\| (AF-\d{3}) \| (.+?) \| (.+?) \| (.+?) \| (.+?) \|$", sec, re.M):
    epics.append({"id": m.group(1), "name": m.group(2), "ms": m.group(3),
                  "scope": m.group(4), "gate": m.group(5)})

# --- tasks from phase files (compact schema) ---
META = re.compile(r"^\*\*(Milestone|Depends on|Labels|Applies if|Safety|Stop condition|Resolves|Procedure|Wiring|Parts|Context):\*\* (.*)$", re.M)
tasks = []
for f in PHASES:
    text = f.read_text(encoding="utf-8")
    for blk in re.split(r"^### ", text, flags=re.M)[1:]:
        header = blk.splitlines()[0]
        if "SUPERSEDED" in header:   # e.g. "AF-171 — SUPERSEDED" — excluded from export
            continue
        tid, title = header.split("—", 1)
        meta = {k: v for k, v in META.findall("\n".join(blk.splitlines()[1:]))}
        labels = meta.get("Labels", "")
        done = re.search(r"#### Done when\n((?:- .*\n?)+)", blk)
        acc = " ".join(l.lstrip("- ") for l in done.group(1).strip().splitlines()) if done else ""
        tasks.append({
            "id": tid.strip(), "title": title.strip(), "file": f.name,
            "ms": meta.get("Milestone", ""), "dep": meta.get("Depends on", "—"),
            "labels": meta.get("Labels", ""), "applies": meta.get("Applies if", "—"),
            "acc": acc, "ctx": meta.get("Context", ""),
            "refs": " ".join(x for x in (meta.get("Procedure", ""), meta.get("Resolves", ""),
                                         meta.get("Wiring", ""), meta.get("Parts", "")) if x) or "—",
        })

# map task -> owning epic via phase-file milestone -> epic Milestone column
assert len(epics) == 10, f"expected 10 epics, got {len(epics)}"   # malformed §1A parse fails immediately
ms2epic = {e["ms"]: e["id"] for e in epics}   # unmapped milestone must fail loudly

# --- JIRA.md-owned column schema ---
COLS = ["Issue Type", "Epic", "Task ID", "Summary", "Milestone", "Depends On",
        "Labels", "Applies If", "Context", "Acceptance Criteria", "Procedure / References"]

def epic_row(e):
    return ["Epic", e["id"], e["id"], e["name"], e["ms"], "—", "epic", "—",
            e["scope"], e["gate"], "—"]

def task_row(t):
    return ["Task", ms2epic[t["ms"]], t["id"], t["title"], t["ms"], t["dep"],
            t["labels"], t["applies"], t["ctx"], t["acc"], t["refs"]]

rows = [epic_row(e) for e in epics] + [task_row(t) for t in tasks]

# --- uniqueness guard ---
ids = [r[2] for r in rows]
assert len(ids) == len(set(ids)), "duplicate AF id"

# --- 09 markdown ---
def esc(s): return s.replace("|", "\\|")
md = ["# 09 — Jira Import Table (DERIVED — DO NOT HAND-EDIT)", "",
      "> **MECHANICAL DERIVATION** from `docs/pm/backlog/` phase files + epic registry, "
      "following the column schema defined in `docs/JIRA.md`. NEVER edit manually. "
      "Regenerate with `python3 scripts/generate_jira_import.py`. `Procedure / References` = "
      "the task's Procedure (EXP-###), Resolves (U-###), Wiring, and Parts fields; broader "
      "ADR/JIRA/BOM attribution lives canonically in `refactor-map.md` and the 07/08 coverage docs.", ""]
md.append("| " + " | ".join(COLS) + " |")
md.append("|" + "---|" * len(COLS))
for r in rows:
    md.append("| " + " | ".join(esc(c) for c in r) + " |")
(ROOT / "docs/pm/09-jira-import-table.md").write_text("\n".join(md) + "\n", encoding="utf-8")

# --- 10 CSV (RFC-4180: CRLF, quote only when needed) ---
buf = io.StringIO()
w = csv.writer(buf, lineterminator="\r\n", quoting=csv.QUOTE_MINIMAL)
w.writerow(COLS)
for r in rows:
    w.writerow(r)
(ROOT / "docs/pm/10-jira-import.csv").write_text(buf.getvalue(), encoding="utf-8")
print(f"epics={len(epics)} tasks={len(tasks)} total_rows={len(rows)}")
```

- [ ] **Step 2: Run the generator**

Run: `python3 scripts/generate_jira_import.py`
Expected output: `epics=10 tasks=<N> total_rows=<10+N>` where N = active task count. Expected N: 177 = (108 existing headers − 1 SUPERSEDED) + 62 migrated + 8 new (AF-188..AF-195). If N differs, investigate before continuing — do not force it. Also verify the header row matches JIRA.md:614 exactly (`Issue Type,Epic,Task ID,Summary,Milestone,Depends On,Labels,Applies If,Context,Acceptance Criteria,Procedure / References`).

- [ ] **Step 3: Validate the outputs**

```bash
python3 - <<'EOF'
import csv
from pathlib import Path
csv_rows = list(csv.reader(open("docs/pm/10-jira-import.csv", newline="", encoding="utf-8")))
md = Path("docs/pm/09-jira-import-table.md").read_text().splitlines()
md_rows = [l for l in md if l.startswith("| Task |") or l.startswith("| Epic |")]
assert len(csv_rows) == len(md_rows) + 1, (len(csv_rows), len(md_rows))  # csv has header
assert all(len(r) == 11 for r in csv_rows), "bad column count"
assert csv_rows[0] == ["Issue Type", "Epic", "Task ID", "Summary", "Milestone", "Depends On",
                       "Labels", "Applies If", "Context", "Acceptance Criteria",
                       "Procedure / References"], "schema drift vs docs/JIRA.md"
raw = Path("docs/pm/10-jira-import.csv").read_bytes()
assert raw.count(b"\r\n") >= len(csv_rows), "CRLF missing"
ids = [r[2] for r in csv_rows[1:]]
assert len(ids) == len(set(ids)), "duplicate IDs"
assert "AF-171" not in ids, "SUPERSEDED exported"
print("csv/md rows:", len(csv_rows) - 1, "unique ids:", len(set(ids)))
EOF
```

Expected: `csv/md rows: <10+N> unique ids: <10+N>`; no assertion failures.

- [ ] **Step 4: Commit**

```bash
git add scripts/generate_jira_import.py docs/pm/09-jira-import-table.md docs/pm/10-jira-import.csv
git commit -m "docs(pm): regenerate Jira import table and CSV from phase-file registry"
```

### Task 9: Re-run the coverage audit against the new registry

**Files:**
- Modify: `docs/pm/11-coverage-audit.md`

**Interfaces:**
- Consumes: phase registry + epic registry, regenerated 09/10, reconciled support docs (Tasks 1–8).
- Produces: dated audit report — all 10 items PASS against the new source of truth, plus the 7 added checks.

- [ ] **Step 1: Re-run and record items 1–9**

Re-run each existing item with new methodology text (targets = phase files, not 05). Item #2 must show 0 uncovered steps including EXP-017, EXP-006 procedure steps mapped to the WF4/M4 tasks, and the ESP32 M4 path (AF-188..AF-195 + AF-111) attributed to EXP-011/012/013-informed integration — not EXP-006 coverage. Item #3: ADR-016/017 evidence = AF-093..AF-096. Item #6: task size across all 15 phase files (steps ≤30, DoD ≤8). Item #7: polarity-before-energize across all phases incl. MF mains tasks. Item #8: Cond-X skip-gated per the approved two-class AF-096 winner/loser sweep; MF gated on MR. Item #9: critical-path chains per 06-critical-path.

- [ ] **Step 2: Redefine and run item #10 + added checks**

Item #10 becomes: `phase registry + epic registry ↔ 09 table ↔ 10 CSV` (drop 05 from the comparison; note the change). Add these checks, each with methodology + findings + result:

```bash
# (a) every stable ID exactly once in canonical registry
grep -rhoE '^### AF-[0-9]+' docs/pm/backlog/*.md | sort | uniq -d   # expect empty
# (b) every new ID (AF-174+) has refactor-map lineage
for i in $(seq 174 195); do grep -rq "AF-$i" docs/pm/refactor-map.md || echo "NO LINEAGE AF-$i"; done
# (c) no SUPERSEDED task exports to Jira
grep -c 'AF-171' docs/pm/10-jira-import.csv   # expect 0
# (d) all dependencies reference existing IDs (OR-aware, cycle-free)
python3 - <<'EOF'
import re, glob
ids, deps = set(), {}
for f in glob.glob("docs/pm/backlog/*.md"):
    for blk in re.split(r"^### ", open(f).read(), flags=re.M)[1:]:
        tid = "AF-" + re.match(r"(\d+)", blk).group(1)
        ids.add(tid)
        m = re.search(r"^\*\*Depends on:\*\* (.*)$", blk, re.M)
        if m and m.group(1) != "—":
            groups = []
            for part in m.group(1).split(","):          # AND separates comma groups
                alts = set(re.findall(r"AF-\d+", part))  # OR lives inside one group
                if alts:
                    groups.append(alts)
            deps[tid] = groups
# so "AF-096, AF-101, AF-102 OR AF-103" parses as AF-096 AND AF-101 AND (AF-102 OR AF-103)
missing = {t: a for t, alts in deps.items() for a in alts if not a <= ids}
assert not missing, missing
# fixed-point solvability: a task is solvable when every AND group has at least
# one already-solvable member; iterate until no progress. Catches OR-deadlocks
# (all alternatives loop back) that a hard-edge-only DFS would miss.
solvable = set(ids) - set(deps)
progress = True
while progress:
    progress = False
    for t, gs in deps.items():
        if t not in solvable and all(any(d in solvable for d in g) for g in gs):
            solvable.add(t)
            progress = True
unsolved = sorted(set(deps) - solvable)
assert not unsolved, f"CYCLIC/UNSATISFIABLE: {unsolved}"
print("DAG OK:", len(solvable), "solvable of", len(ids), "tasks;",
      len(deps), "dependency-bearing")
EOF
# (e) every ADR-016-conditional task has an Applies-if clause or a resolved skip line
python3 - <<'EOF'
import re, glob
bad = []
for f in glob.glob("docs/pm/backlog/*.md"):
    for blk in re.split(r"^### ", open(f).read(), flags=re.M)[1:]:
        head = blk.splitlines()[0]
        if re.search(r"\*\*Labels:\*\*.*conditional.*blocked:adr-016", blk) or \
           re.search(r"\*\*Labels:\*\*.*blocked:adr-016.*conditional", blk):
            has_applies = re.search(r"^\*\*Applies if:\*\* \S", blk, re.M)
            resolved_skip = head.startswith("AF-") and "Skip — ADR-016 selected" in blk
            if not (has_applies or resolved_skip):
                bad.append(f"{f}: {head}")
assert not bad, f"conditional tasks missing Applies-if/skip: {bad}"
print("check (e) OK")
EOF
# (f) no stale safety thresholds / unsupported hardware facts
grep -rniE '≤ ?[0-9]+ ?s.*(cutoff|threshold)|under ?60 ?C' docs/pm/backlog/*.md | grep -v 'measured\|observed\|recorded'   # expect empty
```

Record every check's result; any FAIL gets a Fix Log + re-run (existing audit format).

- [ ] **Step 3: Commit**

```bash
git add docs/pm/11-coverage-audit.md
git commit -m "docs(pm): re-run 10-item coverage audit against phase-file registry, 10/10 PASS"
```

### Task 10: Finalize the migration state

**Files:**
- Modify: `docs/pm/05-backlog.md` (top banner only)
- Modify: `docs/pm/backlog/README.md` (remove migration warning)
- Create: `docs/pm/STOPPING-POINT-2026-08-19-PM-MIGRATION.md`

**Interfaces:**
- Consumes: passing audit (Task 9).
- Produces: final migration state. Nothing downstream.

- [ ] **Step 1: Mark 05 historical — title and source-of-truth claims included**

The file currently opens with `# 05 — Canonical Backlog` followed by an active `> **SINGLE SOURCE OF TRUTH.** ...` blockquote claiming canonicity. Leaving those while adding a warning would be self-contradictory. Replace the title line and the entire SINGLE-SOURCE-OF-TRUTH blockquote (and nothing else) with:

```markdown
# 05 — Canonical Backlog (HISTORICAL — SUPERSEDED 2026-08-19)

> **⚠️ HISTORICAL / SUPERSEDED (2026-08-19).** The canonical backlog now lives in
> [`docs/pm/backlog/`](backlog/) (15 phase files + epic registry in
> [`backlog/README.md`](backlog/README.md) §1A). This monolith is retained for lineage
> and audit history only. Do not add tasks here. Jira derivations regenerate from the
> phase registry via `scripts/generate_jira_import.py`.
```

All task content below the header stays byte-identical.

- [ ] **Step 2: Remove the migration banner from `backlog/README.md`**

Delete the `> [!WARNING]` block at the top (schema migration / stale derived files notice). Update the stale-derivative language anywhere else in the file to point at the Task-8 regeneration flow.

- [ ] **Step 3: Create the new stopping point**

`docs/pm/STOPPING-POINT-2026-08-19-PM-MIGRATION.md`: snapshot of the completed migration — final counts (epics 10; tasks N with breakdown per phase file; next available ID = AF-196), the new audit PASS date, the new derivation command, pointer to the old `STOPPING-POINT-2026-08-19.md` for pre-migration history. Do not rewrite the old checkpoint.

- [ ] **Step 4: Final verification sweep**

```bash
# all remaining 05-backlog mentions must be historical/lineage statements —
# classify hits manually; the assertion below enforces the authoritative-free claim:
grep -rn '05-backlog' docs/pm/README.md docs/pm/backlog/README.md   # every hit must contain historical/superseded/lineage context
! grep -rniE '05-backlog.*(canonical|source of truth)|(canonical|source of truth).*05-backlog' docs/pm/README.md docs/pm/backlog/README.md
grep -n 'SINGLE SOURCE OF TRUTH' docs/pm/05-backlog.md              # expect 0 matches (blockquote replaced in Step 1)
git status                                                          # working tree clean except docs/superpowers/ (untracked, never committed)
```

- [ ] **Step 5: Final commit**

```bash
git add docs/pm/05-backlog.md docs/pm/backlog/README.md docs/pm/STOPPING-POINT-2026-08-19-PM-MIGRATION.md
git commit -m "docs(pm): finalize migration — 05-backlog superseded, registry canonical, audit PASS"
```

---

## Self-Review (completed during planning; revised after four 2026-08-19 plan reviews)

- **Review-round-4 fixes incorporated (4 corrections + 2 cleanups + 1 hardening):** (1) AF-119 gets explicit convergence `Depends on: AF-114, AF-116, AF-118` (seam / brightness-thermal / recovery paths machine-visible); (2) AF-195 flashes the **exact proven M3-path firmware/library/GPIO/row configuration recorded by AF-103/AF-108** — AF-066 is lineage/baseline evidence only, not a permanent constraint; (3) adapter stages carry explicit mirrored Safety profiles (AF-191 `5V-HIGH-CURRENT`, AF-192/193/194 `HUB75`, each + stop condition, verified against the M1 originals); (4) Task 7 wording tightened — EXP-012's direct coverage stays with the M2 tasks; AF-188..AF-195 + AF-111 consume/reuse the proven per-row evidence, not additional coverage; (5) Task 10 final sweep now asserts no `05-backlog` + canonical/source-of-truth co-occurrence (manual classification note kept); (6) generator asserts `len(epics) == 10` before ms2epic. Not-applicable note: the plan's Task 10 heading is already a plain H1 (line 606); the reviewer's bold-wrapped rendering does not occur in the file.

- **Spec coverage:** inventory §1 → Tasks 1–5 (62 tasks: 11+19+2+12+18); §2 epic home → Task 6; §3 six support docs → Task 7; §4 regeneration with single in-memory collection, SUPERSEDED exclusion, RFC-4180, uniqueness → Task 8; §5 ten items re-pointed + item #10 redefined + seven added checks → Task 9; §6 supersession (title + source-of-truth blockquote replaced, not just bannered), banner removal, new stopping point, next-ID record, stale grep, final commit → Task 10. Execution order matches the user's recommendation (M4 → Cond-X → MR → MA → MF → epics → support docs → Jira → audit → bookkeeping). Count discrepancy resolved to 62 with per-epic authority (09-table Epic column).
- **Review-round-3 fixes incorporated (2 blockers + 3 structural + cleanups):** (1) Task 7/Task 9 now map experiments architecture-correctly — EXP-006 procedure steps → WF4/M4 tasks only (AF-109, AF-110, AF-112..AF-119 as applicable); the ESP32 M4 path (AF-188..AF-195 + AF-111) is EXP-011/012/013-informed integration, explicitly **not** EXP-006 coverage; (2) AF-195 mirrors AF-066 exactly — USB-interface flash, no `Safety: CH340` for the flash; CH340 profile applies only to a separately attached UART-log adapter with VCC/5V/3.3V disconnected (TX/RX/GND only); (3) the Cond-X entry dependency uses the stable ID directly — `Depends on: AF-096, AF-108, AF-052` (AF-052 — Validate provisional GPIO mapping against physical board silkscreen); (4) Task 9 item #8 re-worded to the approved two-class sweep; (5) MF gating uses the dependency graph (MR tasks AF-120/AF-121) + normalized `blocked:exp` only — no `blocked:exp-exp-015`-style legacy labels; (6) the AF-110 worked example dependency corrected to `AF-109, AF-108` matching Task 1; (7) audit check (e) upgraded from two grep counts to a block-level Python assertion (every `blocked:adr-016`-conditional task has an `Applies if:` clause or a resolved skip line).
- **Review-round-2 fixes retained:** JIRA.md-owned 11-column generator schema with drift assertion; two-class AF-096 semantics (no third class); 19-step Cond-X gate; eight-task third-controller chain mirroring M1 stages (next free AF-196, expected count 177); MA identifiers grounded; fixed-point DAG solvability check.
- **Review-round-1 fixes retained:** pushed `13e0f4b` baseline; `SUPERSEDED in header` detection; fail-loud epic mapping; printf-padded epic verification; `Procedure / References` composition with attribution rehomed; MF `Safety: MAINS` profiles + specific Stop conditions; Task 10 replaces 05's title and SINGLE-SOURCE-OF-TRUTH blockquote; EXP-012 scoped as ESP32 per-row evidence.
- **Placeholder scan:** no TBD/TODO and no unresolved ID placeholders; every code step has full code (script, fixed-point checker, block-level check (e)); the worked example's dependency matches its task spec.
- **Consistency:** next-available sequence AF-188..AF-195 (Task 1) → AF-196 (registry update in Task 1 Step 6, recorded again in Task 10); expected active count 177 in Task 8 = 107 pre-existing active + 62 migrated + 8 new; M4 = 19 tasks consistent across Task 1 interfaces/verify and the counts constraint; OR-dependency syntax uniform with AF-104/105 precedent; two-class sweep wording identical across Global Constraints, Task 1 Step 2, Task 2, and Task 9 item #8; SUPERSEDED handling consistent between README §1 rules, script, and audit check (c); lineage check (b) covers AF-174..AF-195.
