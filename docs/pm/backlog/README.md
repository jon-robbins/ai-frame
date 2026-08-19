# Project Backlog & Task Registry

> [!WARNING]
> PM tracker currently undergoing schema migration. Derived Jira files 09/10 and coverage audit are stale until regeneration.

**Location:** `docs/pm/backlog/`  
**Purpose:** Authoritative technical registry and milestone task catalog for the AI-Frame hardware/software project.

---

## 1. ID Allocation & Registry Rules

Every task across the project backlog is assigned a permanent, unique identifier:

- **Stable Project IDs:** Tasks are identified by immutable IDs in the format `AF-XXX` (e.g., `AF-011`, `AF-174`).
- **Non-Contiguous Milestone Ranges:** Initial task IDs originated in numeric blocks during initial backlog creation (`AF-001` through `AF-010` for Epics, `AF-011` through `AF-173` for initial tasks). During refactoring, splitting, or expanding tasks, new IDs are allocated sequentially starting from `AF-174+` and placed directly into whichever milestone/phase file they belong.
- **Authoritative Mapping:** The single source of truth for task location and milestone association is the mapping `AF-ID → phase file / milestone`. Do not assume task IDs within a phase file are contiguous.
- **Next Available ID:** `AF-187` (updated monotonically as new tasks or splits are introduced).
- **Never Renumber or Delete:** Task IDs are never reused or deleted once assigned.
- **SUPERSEDED Conventions:** If a task is decomposed or obsoleted such that no natural core task remains after a split, the entry is marked:
  ```markdown
  ### AF-XXX — SUPERSEDED
  
  Replaced by: AF-YYY, AF-ZZZ
  
  (Do not export to Jira)
  ```
  Superseded tasks are retained in the phase files for auditability and lineage tracking, but are **NEVER exported to Jira**.

---

## 2. Execution Model: Dependency Graph

The project executes as a **dependency-driven directed acyclic graph (DAG)**, rather than a rigid linear checklist. Key execution characteristics include:

- **Independent Parallel Tracks:** Subtracks proceed concurrently whenever physical or logical dependencies permit. For instance, the **LicheeRV Nano Software Bootstrap** (`02-nano-bootstrap.md`) is independent of the main display-hardware bring-up once Nano/microSD are available, executing in parallel with shipping, delivery receipt (`00-hardware-receipt.md`), and safe AC power bringup (`01-power-bringup.md`).
- **Parallel Candidate Spikes:** During Milestone 1 (M1), both the **HD-WF4** (`03-wf4-single-panel.md`) and **ESP32-S3** (`04-esp32-single-panel.md`) controller tracks operate in parallel alongside the HD-WF2 reference track (`05-wf2-investigation.md`).
- **Convergence at Gated Milestones:** Parallel tracks converge at explicit quality gates:
  - **M1 Single-Panel Gate (`AF-080` in `06-single-panel-gate.md`):** Requires at least one controller track (WF4 or ESP32) to achieve end-to-end arbitrary text rendering from the Nano before advancing to M2 dual-panel testing.
  - **Architecture Decision Gate (MG in `08-architecture-decision.md`):** Evaluates measured data from both candidates (EXP-014), commits ADR-016 and ADR-017, and executes a backlog reclassification sweep to prune/skip non-selected controller tasks.
- **Single Winning Path:** From Milestone 3 (M3) onward, work proceeds along the single winning controller architecture, with conditional tasks (e.g., custom ESP32 PCB `13-esp32-pcb.md`) activating only if triggered by ADR decisions.

---

## 3. Task Schema Reference

All tasks adhere to the compact schema specified in [`../runbooks/task-format.md`](../runbooks/task-format.md).

### Metadata Fields Summary

| Field | Type | Description |
|---|---|---|
| `Milestone:` | **Required** | Target milestone (`M0`, `M1`, `M2`, `MG`, `M3`, `M4`, `MR`, `MA`, `Cond-X`, `MF`). |
| `Depends on:` | **Required** | Comma-separated list of prerequisite stable task IDs (or `—` if none). |
| `Labels:` | **Required** | Space-separated lowercase taxonomy tags (e.g., `hardware`, `software`, `safety-review`). |
| `Applies if:` | Optional | Architectural condition required for task execution (e.g., `ADR-016 selects ESP32`). |
| `Safety:` | Optional | Named safety profile from [`../runbooks/safety.md`](../runbooks/safety.md) (`MAINS`, `HUB75`, `CH340`, `5V-HIGH-CURRENT`). |
| `Stop condition:` | **Required with Safety** | Immediate physical action or shutdown threshold before proceeding or upon anomaly. |
| `Resolves:` | Optional | Uncertainty IDs from `docs/pm/03-uncertainty-register.md` (e.g., `U-001`). |
| `Procedure:` | Optional | Experiment reference from `docs/EXPERIMENTS.md` (e.g., `EXP-003`). |
| `Wiring:` | Optional | Section link in `hardware/schematics/PROTOTYPE_WIRING.md` or `PIN_LEVEL_APPENDIX.md`. |
| `Parts:` | Optional | Specialized components or consumables needed for the task. |
| `Context:` | Optional | Single-sentence explanation of task rationale. |

### Task Body Sections

- **`#### Do`**: Numbered, step-by-step physical, command-line, or code instructions.
- **`#### Done when`**: Bulleted list of pass/fail verifiable criteria (measurements, exit codes, observable states; max 8 bullets).
- **`#### If it fails`**: Concrete fault isolation and safe recovery procedure (what to de-energize, what to inspect/swap).

---

## 4. Milestone Definitions & Exit Gates

| Milestone | Gate Status | Exit Gate Definition (Objective DoD) |
|---|---|---|
| **M0: Safe Power & Receipt** | Prerequisite | EXP-001 inventory complete; C14/AC mains wiring passes visual + continuity + 8-point safety checks; EXP-002 PSU no-load energization measured ~5 V and stable per EXP-002 & 10 min hold; EXP-003 first-panel DC cold power test passes (5 V verified, no damage/abnormal draw). |
| **M1: Single-Panel Prototype** | 🔒 Gated | At least ONE controller track passes: arbitrary user-supplied string → Nano Pillow render → transport → correct text visible on 1 physical panel → no manual vendor-software clicks during update → stable 10 min hold (`AF-080`). |
| **M2: Dual-Panel Canvas** | 🔒 Gated | At least ONE controller track passes EXP-005/EXP-012: 2 chained panels = 256×64; horizontal seam crossing verified; left→right panel order verified via numbered regions; Standard Test Pattern Suite + Standard Defect Checklist pass on both panels; ≥ 30 min stable run. |
| **MG: Architecture Decision** | 🔒 Decision Gate | EXP-014 scoring matrix (13 criteria × 2 candidates) fully filled from measured data; ADR-016 (WF4 vs multi-ESP32) documented using "simplest that reliably satisfies V1" rule; ADR-017 transport documented; post-decision reclassification sweep applied to all downstream backlog tasks. |
| **M3: Four-Panel Prototype** | 🔒 Gated | Winning controller only; 2×2 topology (256×128); both horizontal seam (row boundary) and vertical seam (panel chain boundary) crossed by arbitrary content; row updates synchronized; coordinate grid + boundary labels correct; sustained stability ≥ 30 min; EXP-017 procedure logged. |
| **M4: Six-Panel Full Prototype** | 🔒 Gated | 2×3 = 6 panels on winning controller architecture (256×192); arbitrary Nano-driven content anywhere on canvas; all seams correctly crossed; acceptable refresh rate; EXP-015 4-brightness thermal pass; EXP-016 ×5 power-cycle recovery pass; Wi-Fi recovery pass; API failure/offline fallback behavior pass. |
| **MR: Reliability & Burn-In** | Review Gate | Display brightness ceiling calibrated and logged; 24-hour continuous unattended stability test at dashboard-normal content without crash, thermal runaway, or visual artifacting. |
| **MA: Application Software** | Review Gate | All 12 JIRA software stages complete; production dashboard runtime with widgets (time/date, weather, Google Calendar, Spotify now-playing/artwork, decorative graphics/icons, and other API-driven dashboards) and offline-fallback supervision operational on Nano. |
| **Cond-X: Custom ESP32 PCB** | Conditional Gate | Skip if ADR-016 selects WF4. If active: 18 KiCad steps (schematic ERC, layout DRC, Gerber/BOM/CPL generation, fabrication, continuity testing, row validation, swap out perfboard for PCB). |
| **MF: Mounted Frame Optimization** | 🔒 Gated | All 18 MF items satisfied: exact dimensions, layout sketch, PSU location/airflow, controller & Nano mounting, cable routing & strain relief across all 4 categories, PE bonding across all 3 categories, ventilation/fan specs, closed-box thermal test at brightness ceiling, closed-box Wi-Fi test, mechanical panel mounting/alignment, backplate/materials documented, aesthetic finish/mounting hardware selected, 72h unattended wall-mounted test pass. |

---

## 5. File Ownership Table

The backlog is organized into 15 focused phase files:

| File | Milestone | Scope |
|---|---|---|
| [`00-hardware-receipt.md`](00-hardware-receipt.md) | M0 | Delivery receipt, physical component inspection, inventory verification (covers/executes EXP-001) |
| [`01-power-bringup.md`](01-power-bringup.md) | M0 | C14 mains wiring, AC safety checks, PSU no-load validation (EXP-002), first-panel power (EXP-003) |
| [`02-nano-bootstrap.md`](02-nano-bootstrap.md) | M0 / M1 | LicheeRV Nano OS flashing, serial debug, Python environment, Pillow text/graphics rendering foundation |
| [`03-wf4-single-panel.md`](03-wf4-single-panel.md) | M1 | HD-WF4 controller bringup, stock firmware, Huidu protocol reverse engineering (EXP-007 A/B/C), Nano control |
| [`04-esp32-single-panel.md`](04-esp32-single-panel.md) | M1 | ESP32-S3 pinout confirmation, buffer adapter assembly and continuity validation, ESP32 HUB75 driver firmware (EXP-011), Nano transport (EXP-013) |
| [`05-wf2-investigation.md`](05-wf2-investigation.md) | M1 | HD-WF2 reference controller stock testing (EXP-008) and alternative firmware exploration (EXP-009) |
| [`06-single-panel-gate.md`](06-single-panel-gate.md) | M1 | Milestone 1 exit gate aggregator task (`AF-080`) verifying end-to-end arbitrary text display from Nano |
| [`07-dual-panel.md`](07-dual-panel.md) | M2 | Two-panel 256×64 logical canvas chaining, seam crossing, test patterns, defect checklist (EXP-005, EXP-012) |
| [`08-architecture-decision.md`](08-architecture-decision.md) | MG | EXP-014 scoring matrix, ADR-016 controller decision, ADR-017 transport decision, post-decision backlog sweep |
| [`09-quad-panel.md`](09-quad-panel.md) | M3 | Four-panel 256×128 (2×2) multi-controller sync, row & chain seam crossing, EXP-017 validation |
| [`10-six-panel.md`](10-six-panel.md) | M4 | Six-panel 256×192 full prototype assembly, PSU loaded thermal (EXP-015), power recovery cycles (EXP-016) |
| [`11-reliability.md`](11-reliability.md) | MR | Display brightness ceiling calibration, 24-hour continuous burn-in stability test |
| [`12-application.md`](12-application.md) | MA | Software stages 1–12, widget framework, live data fetching, offline fallback supervisor |
| [`13-esp32-pcb.md`](13-esp32-pcb.md) | Cond-X | Conditional custom ESP32 HUB75 adapter PCB design in KiCad (schematic ERC, layout DRC, fabrication) |
| [`14-mounted-frame.md`](14-mounted-frame.md) | MF | Physical frame fabrication, panel mounting, electrical safety/PE bonding, closed-frame 72h validation |
