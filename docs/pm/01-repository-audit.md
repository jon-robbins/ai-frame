# 01 — Repository Audit

**Last updated:** 2026-08-19
**Purpose:** Inventory of what exists, what is empty / placeholder-only, what lives on unmerged branches, what is stale, and any contradictions or discrepancies discovered. Feeds the uncertainty register and defines the starting state for the backlog.

---

## 1. Branch state at audit time

- **Active branch:** `main`
- **Branches scanned for diff content:**
  - `agent/connection-diagrams` — one consolidated CONNECTIONS.md document vs. main's two detailed docs (PROTOTYPE_WIRING + PIN_LEVEL_APPENDIX)
  - `agent/prototype-wiring-diagrams` — no net diff vs main at audit time (appears merged)
- **Ruling:** Branch-only `agent/connection-diagrams` CONNECTIONS.md is treated as an **older proposed design draft**, not an accepted decision. Main's PROTOTYPE_WIRING.md + PIN_LEVEL_APPENDIX.md are the canonical connection references for the start of execution.

## 2. Top-level repository layout

```
/Users/jon.robbins/GitHub/ai-frame/
├── README.md                          — present, project overview + 2 photos
├── docs/
│   ├── PROJECT.md                     — present, V1 functional + scope + non-goals + subsystems + safety
│   ├── STATUS.md                      — present, 2 stated milestones, mostly ORDERED/PENDING
│   ├── BOM.md                         — present, 48 BOM lines + safety notes + 3 researched fab services
│   ├── EXPERIMENTS.md                 — present, 16 experiments (EXP-001…EXP-016), blank template
│   ├── DECISIONS.md                   — present, 24 ADR records (14 ACCEPTED + 2 PENDING + 1 DEFERRED + inline REJECTs)
│   ├── JIRA.md                        — present, project planning process spec, 11 deliverables, 4 milestones, 18 uncertainties, safety rules, planning ordered lists, cond PCB 19 steps
│   ├── PROTOTYPE_WIRING.md            — present, §1-7 inventories PWR-01…DBG-03, §C14/PanelPwr/HUB75 VERIFYs, §EXP refs
│   ├── PIN_LEVEL_APPENDIX.md          — present, §1 HCT245 DIP-20 pinout, §2 14 HCT245 nets, §3 U1 channels, §4 U2 channels, §5 HUB75 layout (2×8 keyed, 16 pins: color pins + row pins + control + GND)
│   ├── superpowers/                   — present, skills + spec for this planning work
│   └── (pm/)                          — NOT YET CREATED — this is the deliverable directory
├── firmware/                          — PRESENT BUT EMPTY PLACEHOLDER. No source code.
├── hardware/
│   ├── pcb/                           — PRESENT BUT EMPTY PLACEHOLDER. No KiCad files.
│   └── schematics/
│       └── schematics-files/          — PRESENT BUT EMPTY PLACEHOLDER. No schematic source files.
├── software/
│   ├── app/                           — PRESENT BUT EMPTY PLACEHOLDER. No app source.
│   └── display/                       — PRESENT BUT EMPTY PLACEHOLDER. No display driver source.
├── .superpowers-installer/            — present, untracked
├── .vscode/                           — present, editor settings
└── .gitignore                         — present
```

## 3. Expected-but-absent items

The following items are **not yet present** and are expected to be produced by later workstreams:

| Expected item | Category | Producing epic / mechanism |
|---------------|----------|----------------------------|
| Nano firmware / python display code | Software | AF-008 MA Application Software |
| ESP32 HUB75 firmware (flash binary or sketch) | Software | AF-002 M1 / AF-010 Cond-X downstream |
| WF4 programmatic protocol library | Software | AF-002 M1 / EXP-007 |
| KiCad schematic for ESP32 HCT adapter PCB | Hardware (schematic) | AF-010 Cond-X |
| KiCad PCB layout + Gerbers + drill files + BOM+CPL | Hardware (PCB) | AF-010 Cond-X |
| Experiment result data (pass/fail, photos, logs) | Documentation | Each EXP task's Evidence output |
| KiCad schematic for 6-panel + PSU + C14 wiring harness | Hardware (schematic) | ADR-024 DEFERRED → candidate for V1.1; not required for V1 |

## 4. Stale / aging content candidates

| File or section | What makes it look stale | Ruling at this time |
|-----------------|--------------------------|----------------------|
| `BOM.md` STATUS column — 39 lines = "ORDERED" | BOM items not yet physically received | **NOT stale.** Hardware was ordered recently; "ORDERED" is correct. On receipt, these flip to "RECEIVED" with EXP-001 inventory tasks. |
| Photos in README.md (USB-C PSU, HUB75 panels, C14 inlet, LicheeRV Nano-W) | Photos are pre-delivery catalog photos | **NOT stale;** they are reference photos. Rule: replace with actual receipt photos during EXP-001. |
| `STATUS.md` Milestone 1 = "Hardware delivery, inventory + safe power" vs `docs/JIRA.md` 4 hardware milestones + intermediate M3 = new EXP-017 | Milestone count difference | **NOT a contradiction.** JIRA.md's 4 HW milestones + M3 EXP-017 are an EXPANSION of STATUS.md's two coarse milestones. Treat JIRA.md as superseding STATUS.md granularity, not contradicting scope. |
| PIN_LEVEL_APPENDIX.md §1 HCT245 pins table uses exact DIP-20 pin numbers | Not verified against physical IC on hand yet | **NOT stale;** standard SN74HCT245N datasheet pinout. Verify-on-receipt in EXP-001. |

## 5. Contradictions & discrepancies identified

These are recorded explicitly so the uncertainty register and backlog can spawn VERIFY or resolve tasks.

| ID | Finding | Left source | Right source | Nature | Severity |
|----|---------|-------------|--------------|--------|----------|
| C-01 | Two sets of connection documentation exist, one on a non-merged branch | main: `PROTOTYPE_WIRING.md` + `PIN_LEVEL_APPENDIX.md` (two files, highly detailed, with explicit VERIFY checklists) | branch `agent/connection-diagrams`: `CONNECTIONS.md` (single consolidated doc, without the VERIFY-on-receipt sections) | **Branch divergence, not true contradiction** | Informational; treat main-branch pair as canonical |
| C-02 | Milestone framing differs in level of detail | `STATUS.md`: 2 coarse milestones | `docs/JIRA.md`: 4 hardware milestones + mid-flight EXP-017 as first-class milestone | **Granularity extension, same scope** | Informational; JIRA.md drives this PM plan |
| C-03 | Numbering gap in ADR register (001–024 but some records appear to use same ADR ID for rejected-alternatives within) | `DECISIONS.md` ADR-004 block contains both "Controller = Huidu WF-…" ACCEPTED record and WF4/WF2/Multi-ESP32 alternatives with REJECTED inline | Typical DECISIONS.md formatting style (alternatives recorded inside same-numbered record with ACCEPTED + inline REJECT flags) | **Not a true numbering gap.** Coverage table in 08-adr-coverage.md will list one row per ADR-### header line, with inline REJECTs noted in the row notes. | Informational |

## 6. Untracked / new files on disk

These files are present and expected additions (not a signal of repo corruption):

```
docs/JIRA.md
docs/superpowers/
.superpowers-installer/
docs/pm/               → will be created during this plan execution
```

## 7. Source-of-truth principle — what never to invent

Per JIRA.md §Source-of-Truth Rules 1-8, the following **must never be assumed**: panel manufacturer part numbers (manufacturer unknown), exact driver IC, exact pixel-chip pinout, exact controller chip-on-board flash size, WF4 firmware version/protocol, exact WF2 stock firmware, exact multi-ESP32 controller count, exact KiCad schematic files. All 8 spawn explicit VERIFY or spike tasks in the backlog; see uncertainty register rows U-001…U-008.
