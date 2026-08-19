# Milestone 0 (M0) Refactor Map & Assertion Audit

**Document:** `docs/pm/refactor-map.md`  
**Purpose:** Authoritative migration matrix and formal factual assertion audit for Milestone 0 (M0) tasks (`AF-011` through `AF-024`, plus `AF-171`), establishing the exact decomposition, file destinations, and ground-truth corrections required for Pass 4A backlog restructuring.

---

## 1. Executive Summary & Ground Truth Baseline

During the initial draft of the monolithic backlog (`docs/pm/05-backlog.md`), Milestone 0 tasks inherited several factual hallucinations, ungrounded hardware assumptions, and oversized task definitions. This document establishes the migration mapping and factual audit necessary to restructure M0 into focused, single-outcome tasks adhering to the compact task format across two dedicated phase files:
- `docs/pm/backlog/00-hardware-receipt.md` — Delivery intake, physical inspection, and inventory verification (covers/executes EXP-001).
- `docs/pm/backlog/01-power-bringup.md` — Safe AC mains wiring, insulation/continuity checks, PSU no-load validation (EXP-002), and first-panel DC cold power bringup (EXP-003).

### Ground Truth Authority Reference

| Subsystem / Topic | Ground Truth Specification | Primary Authority Source |
|---|---|---|
| **LED Matrix Panels** | 6 × P2 indoor RGB LED modules, 128×64 pixels, 2.0 mm pitch (256×128 mm), 1/32 scan, 5 V, ~23 W max, HUB75E interface, SMD1515. Seller ref: P2-32S. (Not P1.53). | [`docs/PROJECT.md`](../PROJECT.md) §1.1, §3.1; [`docs/BOM.md`](../BOM.md) line 17 |
| **Power Supply Unit (PSU)** | Single-output switching power supply model **A-200-5** (5 V DC / 40 A / 200 W nominal). Three V+ terminals, three COM terminals. No 12 V rail. (Not RD-65A). | [`docs/PROJECT.md`](../PROJECT.md) §3.4; [`docs/BOM.md`](../BOM.md) line 53; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 |
| **AC Mains Inlet & Fuse** | IEC C14 fused/switched inlet module with illuminated 2-pole rocker switch and 5×20 mm fuse drawer. Candidate fuse: **T2A 5×20 mm slow-blow glass fuse** (2 A time-delay). Verify physical fit and rating upon delivery receipt. (Not 5 A fast-acting). | [`docs/BOM.md`](../BOM.md) lines 56–57; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 |
| **DC Power Distribution** | 2 × 1-to-4 LED panel power harnesses (pure copper wiring, 4 output branches each = 8 branches total for 6 panels, parallel distribution). Harness branches are **unfused**. | [`docs/BOM.md`](../BOM.md) line 54; [`docs/PROJECT.md`](../PROJECT.md) §3.4 |
| **Purchased Item Count** | Exactly **27 purchased BOM line items**. No unpurchased frame screws, M3 nuts, washers, Z-clips, standoffs, or 3D-printed PLA brackets in prototype intake (frame design and mechanical mounting are deferred to Milestone MF per ADR-024). | [`docs/BOM.md`](../BOM.md); [`docs/DECISIONS.md`](../DECISIONS.md) ADR-024 |
| **Application Host SBC** | Sipeed LicheeRV Nano-W (Sophgo SG2002 SoC, 256 MB DDR3, onboard Wi-Fi, microSD, USB 2 OTG Type-C, Linux). | [`docs/PROJECT.md`](../PROJECT.md) §3.2; [`docs/BOM.md`](../BOM.md) line 24 |
| **Display Controllers** | HD-WF4 (4× HUB75 outputs), HD-WF2 (2× HUB75 outputs), ESP32-S3 DevKitC-1 N16R8 (16 MB flash, 8 MB octal PSRAM) with 2× SN74HCT245N DIP-20 buffers. | [`docs/PROJECT.md`](../PROJECT.md) §3.3; [`docs/BOM.md`](../BOM.md) lines 33–42 |
| **HUB75 Signal Cables** | 6 × 16-pin HUB75 IDC ribbon cables (~40 cm length). | [`docs/BOM.md`](../BOM.md) line 18 |

---

## 2. M0 Migration Matrix

The table below catalogs all 15 initial M0 tasks (`AF-011` through `AF-024`, plus `AF-171`), defining their refactoring classification, target destination phase file, replacement IDs, and specific actions.

**Classification Taxonomy:**
- `KEEP`: Retain task scope with minor schema/formatting alignment.
- `SPLIT`: Decompose oversized or multi-outcome task into focused, single-outcome tasks.
- `CORRECT`: Amend task description, steps, acceptance criteria, or safety bounds to eliminate hallucinations and match ground truth.
- `MERGE`: Combine redundant or overlapping task steps into another canonical task.
- `REMOVE`: Deprecate/obsolete task if entirely superseded or invalid.

| Old ID | Title | Classification | Destination File | Replacement IDs | Action Summary |
|---|---|---|---|---|---|
| **AF-011** | EXP-001 Inventory all boards — photo every, record PCB revisions, confirm markings | `SPLIT` | `00-hardware-receipt.md` | `AF-011`, `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179`, `AF-180` | Decompose monolithic 11-step unboxing/inventory task into 8 single-outcome tasks structured by BOM hardware domain. Eliminate all hallucinations (P1.53 → P2, RD-65A → A-200-5, 5A fuse → T2A 5×20mm slow-blow, fused harness → unfused 1-to-4 harness, frame screws/Z-clips/brackets/PLA → remove). |
| **AF-012** | VERIFY C14 rear tab routing — L/N/PE pass through fuse and switch before reaching PSU (power OFF, no power anywhere) | `CORRECT` | `01-power-bringup.md` | `AF-012` | Retain dedicated unpowered C14 continuity and tab routing verification task. Correct fuse rating reference from "5 A" to candidate T2A 5×20mm slow-blow fuse. Clarify 6.3mm spade terminal interfaces on rear tabs. |
| **AF-013** | Crimp practice + visual sample test — ferrule visual sample test + spade crimp practice | `CORRECT` | `01-power-bringup.md` | `AF-013` | Retain novice practice crimp task (5 ferrule + 5 spade samples on 18 AWG offcuts) prior to AC mains assembly. Correct terminal specifications: 6.3mm female insulated spade for C14 tabs, bootlace ferrules for PSU screw barrier terminals. |
| **AF-014** | Wire C14 L → PSU L (18 AWG; crimp spade + ferrule each end; verify each end continuity) | `CORRECT` | `01-power-bringup.md` | `AF-014` | Retain dedicated Live (L) conductor installation task (C14 6.3mm spade tab → 3-core 0.75mm² / 18 AWG brown wire with bootlace ferrule → PSU L screw terminal). Verify continuity (<1 Ω) and wiggle stability under power-OFF conditions. |
| **AF-015** | Wire C14 N → PSU N (18 AWG; crimp spade + ferrule; verify continuity + wiggle) | `CORRECT` | `01-power-bringup.md` | `AF-015` | Retain dedicated Neutral (N) conductor installation task (C14 6.3mm spade tab → 3-core 0.75mm² / 18 AWG blue wire with bootlace ferrule → PSU N screw terminal). Verify continuity and wiggle stability under power-OFF conditions. |
| **AF-016** | Wire C14 PE → PSU FG / Earth (18 AWG; verify PE continuous, NOT switched, at all switch positions) | `CORRECT` | `01-power-bringup.md` | `AF-016` | Retain dedicated Protective Earth (PE) conductor installation task (C14 6.3mm spade tab → 3-core 0.75mm² green/yellow wire with bootlace ferrule → PSU FG terminal). Verify PE is 100% continuous and unswitched across both switch positions. |
| **AF-017** | Visual + continuity inspection of all three AC wires — no stray strands, insulation, strain relief candidates | `CORRECT` | `01-power-bringup.md` | `AF-017` | Retain pre-energization visual and cross-continuity isolation inspection of all 3 AC lines (L/N, L/PE, N/PE isolation matrix in both switch positions). Remove hallucinated reference to 12 V PSU output. |
| **AF-018** | PSU no-load energize EXP-002 (wall plug in, switch ON, ALL OUTPUTS DISCONNECTED — measure 5 V, 12 V, 10 min hold) | `CORRECT` | `01-power-bringup.md` | `AF-018` | Correct and retain EXP-002 PSU no-load energization task. **REMOVE hallucinated 12 V rail** and all 12 V measurement steps/tolerances (A-200-5 is single-output 5 V DC / 40 A). Enforce 5 V no-load measurement (nominal ~5.0 V, range 4.80–5.25 V, calibration via V-ADJ potentiometer if needed), 10-minute hold stability (drift ≤ 0.05 V), and case thermal verification. |
| **AF-019** | VERIFY panel power harness branch #1 polarity — PSU OFF (confirm: red wire = V+ to 5 V+ post contact; black/other = V-; continuity on correct pins; reversed = FAIL) | `CORRECT` | `01-power-bringup.md` | `AF-019` | Retain unpowered harness branch #1 polarity and continuity verification before connection. Remove hallucinated branch fuse references (harnesses are unfused 1-to-4 splitters). Verify connector keying or apply index marking. |
| **AF-020** | VERIFY panel #1 4-pin power connector polarity at panel end (multimeter PSU OFF — confirm markings align with actual cap polarity on PCB V+/V- pins) | `CORRECT` | `01-power-bringup.md` | `AF-020` | Retain dedicated unpowered verification of panel #1 PCB power terminals using onboard capacitor polarity vs silkscreen markings. Align hardware references with P2 128×64 modules. |
| **AF-021** | Panel 1 power test EXP-003 (PSU → harness #1 → panel #1. PSU energize. Measure PSU voltage, AT PANEL voltage, temps. 10-15 min. Panel LEDs off: expected, no data) | `CORRECT` | `01-power-bringup.md` | `AF-021` | Retain EXP-003 single-panel cold DC power test. Energize panel #1 via harness branch #1 without HUB75 data connected. Measure voltage at PSU terminals (5.0 V) and panel connector (≥4.85 V), monitor 10–15 min thermal stability. Confirm panel LEDs remain dark (expected with no refresh data). |
| **AF-022** | VERIFY HUB75 cable + panel #1 IN connector orientation (keyed notch, printed IN label vs OUT, continuity on GND positions — PSU OFF, no power) | `CORRECT` | `01-power-bringup.md` | `AF-022` | Retain unpowered HUB75 ribbon cable and connector orientation verification (keyed notch, IN vs OUT receptacle identification, GND pin continuity). Correct cable spec to 16-pin ~40cm IDC cable. |
| **AF-023** | Panel polarity verify batch (panels #2 through #6 — 5 more polarities, all PSU OFF) | `CORRECT` | `01-power-bringup.md` | `AF-023` | Retain batch unpowered polarity verification for remaining panels #2 through #6 and remaining harness branches. Remove hallucinated branch fuse references. Enforce individual per-panel logging in acceptance criteria. |
| **AF-024** | (M0 summary review) — EXP-001/EXP-002/EXP-003 evidence review + BOM.md status update commit | `CORRECT` | `01-power-bringup.md` | `AF-024` | Refocus task as M0 Power Bringup & Exit Gate Aggregator (review EXP-002 and EXP-003 evidence logs, verify M0 exit criteria satisfaction). BOM status updating for hardware receipt is delegated to `AF-180` in Phase 00. |
| **AF-171** | EXP-001 Step 5 coverage — Mirror inventory photos into hardware/photos/ directory path as specified by EXP-001 Procedure | `CORRECT` | `00-hardware-receipt.md` | `AF-171` | Retain task to mirror/store hardware inventory photographs into canonical `hardware/photos/` directory tree to strictly satisfy EXP-001 Step 5. Link with `AF-011`..`AF-179` photo generation. |

---

## 3. Phase File Allocation & Task Breakdown

### 3.1 Phase 00 — Hardware Receipt & Inventory (`00-hardware-receipt.md`)

Covers physical package intake, unpacking, unboxing verification, item-by-item photography, marking and revision documentation against `BOM.md`, and execution of EXP-001.

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-011` | Reconcile delivered shipment against BOM | Inspect outer shipping boxes, unpack all items onto clean bench, perform line-by-line item count against the 27 purchased BOM items, and document any short-ships or shipping damage. | — | `hardware` `intake` `inventory` |
| `AF-174` | Identify delivered LED panels | Inspect all 6 × P2 128×64 RGB LED panels (model P2-32S, SMD1515); photograph front/back; inspect HUB75E 16-pin shrouded headers, keyed notches, and power connector markings. | `AF-011` | `hardware` `panel` `inventory` |
| `AF-175` | Identify delivered WF4 and WF2 controller boards | Inspect Huidu HD-WF4 (4× HUB75) and HD-WF2 (2× HUB75) controller cards; photograph front/back; record PCB revision silk, IC markings, terminal layouts, and Wi-Fi antenna types. | `AF-011` | `hardware` `controller` `inventory` |
| `AF-176` | Identify delivered LicheeRV Nano-W board | Inspect Sipeed LicheeRV Nano-W (SG2002, 256MB DDR3); confirm Wi-Fi module presence, microSD card slot, USB-C port, and pin headers; photograph front/back. | `AF-011` | `hardware` `nano` `inventory` |
| `AF-177` | Identify delivered ESP32-S3 boards and logic ICs | Inspect ESP32-S3 DevKitC-1 N16R8 board, measure board width/length and pin spacing, record N16R8 module markings; inspect 2× SN74HCT245N DIP-20 ICs, 100nF and 1000µF capacitors, and 5×7cm perfboard. | `AF-011` | `hardware` `esp32` `inventory` |
| `AF-178` | Identify delivered A-200-5 PSU and C14 inlet assembly | Inspect A-200-5 switching power supply (5V/40A/200W single output) and C14 fused/switched inlet module; verify fuse drawer contains candidate T2A 5×20mm slow-blow fuse; photograph terminal markings. | `AF-011` | `hardware` `power` `safety` `inventory` |
| `AF-179` | Inspect delivered DC power cables and 1-to-4 harnesses | Inspect 2 × 1-to-4 LED panel power harnesses (confirm 4 branches each, unfused, 4-pin panel connectors), 6 × 16-pin HUB75 ribbon cables (~40cm), 18 AWG silicone wire, AC power cord, spade terminals, and ferrules. | `AF-011` | `hardware` `wiring` `inventory` |
| `AF-180` | Update BOM component receipt status | Update status column of all verified received items in `docs/BOM.md` from `ORDERED` to `RECEIVED`, recording specific physical observations, part markings, and revision numbers. | `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179` | `docs` `bom` `inventory` |
| `AF-171` | Mirror EXP-001 inventory photos to hardware/photos | Copy/symlink all component inventory photographs taken during AF-011..AF-179 into the canonical `hardware/photos/` directory tree per EXP-001 Step 5 specification. | `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179` | `docs` `photos` `inventory` |

### 3.2 Phase 01 — Safe Power Bringup (`01-power-bringup.md`)

Covers C14 mains inlet wiring, ferrule/spade crimping, unpowered AC isolation and continuity verification, switching power supply no-load energization (EXP-002), and single-panel cold DC power bringup (EXP-003).

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-012` | Verify C14 rear tab routing through fuse and switch | Conduct 9-step unpowered continuity and isolation test across C14 prongs, fuse holder, 2-pole switch, and rear spade tabs to confirm L is fused and switched, N is switched, and PE is unswitched. | `AF-178` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-013` | Ferrule and spade crimp practice and inspection | Produce 5 sample ferrule crimps (18 AWG) and 5 sample 6.3mm spade crimps on wire offcuts; inspect each against 4-point visual criteria and pull test before real wiring. | `AF-178`, `AF-179` | `hardware` `wiring` `practice` |
| `AF-014` | Wire C14 Live conductor to PSU L terminal | Install Live (L) wire (3-core 0.75mm² / 18 AWG brown): crimp 6.3mm female spade onto C14 Live tab, crimp bootlace ferrule onto PSU L end; verify continuity (<1 Ω) and wiggle test. | `AF-012`, `AF-013` | `hardware` `safety-review` `power` `mains` |
| `AF-015` | Wire C14 Neutral conductor to PSU N terminal | Install Neutral (N) wire (3-core 0.75mm² / 18 AWG blue): crimp 6.3mm female spade onto C14 Neutral tab, crimp bootlace ferrule onto PSU N end; verify continuity and wiggle test. | `AF-014` | `hardware` `safety-review` `power` `mains` |
| `AF-016` | Wire C14 Protective Earth conductor to PSU FG terminal | Install Protective Earth (PE) wire (3-core 0.75mm² / 18 AWG green/yellow): crimp 6.3mm female spade onto C14 PE tab, crimp bootlace ferrule onto PSU FG terminal; verify continuous unswitched path. | `AF-015` | `hardware` `safety-review` `power` `mains` `pe-bonding` |
| `AF-017` | Visual and cross-continuity inspection of AC mains wiring | Perform 7-point visual inspection of all 6 crimp joints and 6-point electrical cross-continuity matrix (L↔N, L↔PE, N↔PE open in both switch states) prior to mains energization. | `AF-016` | `hardware` `safety-review` `power` `isolation` |
| `AF-018` | PSU no-load energization and voltage hold test (EXP-002) | Energize A-200-5 PSU with all DC outputs disconnected; verify single 5V DC output (~5.00 V, 4.80–5.25 V range), calibrate V-ADJ if needed, record 10-min hold stability (drift ≤ 0.05 V) and case temperature. | `AF-017` | `hardware` `safety-review` `power` `validation` |
| `AF-019` | Verify panel power harness branch #1 polarity | Conduct unpowered continuity test on harness branch #1: confirm red wire continuity to 5V+ and black wire to COM/GND; verify no cross-short; mark connector orientation index. | `AF-018`, `AF-179` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-020` | Verify panel #1 PCB power terminal polarity against capacitor | Verify panel #1 4-pin power input pins against negative stripe of onboard decoupling electrolytic capacitor; confirm PCB silkscreen matching; mark panel orientation with index tag. | `AF-174` | `hardware` `safety-review` `panel` `polarity-verify` |
| `AF-021` | Single-panel cold DC power test (EXP-003) | Connect panel #1 to A-200-5 via harness branch #1 (no HUB75 data connected); energize PSU; measure PSU terminal voltage (5.0V) and panel connector voltage (≥4.85V); monitor 10–15 min thermal stability. | `AF-018`, `AF-019`, `AF-020` | `hardware` `safety-review` `power` `validation` |
| `AF-022` | Verify HUB75 cable and panel #1 IN header orientation | Verify panel #1 HUB75 IN header label; confirm ~40cm ribbon cable keyed notches engage properly; verify GND pin continuity end-to-end under power-OFF conditions. | `AF-174`, `AF-179` | `hardware` `safety-review` `hub75` `polarity-verify` |
| `AF-023` | Batch verify polarity for panels #2–#6 and harness branches | Execute unpowered polarity verification for remaining 5 panels (#2 through #6) and harness branches #2 through #6; record individual pass/fail results. | `AF-019`, `AF-020`, `AF-022` | `hardware` `safety-review` `panel` `polarity-verify` |
| `AF-024` | M0 power bringup review and milestone exit verification | Review evidence files for EXP-002 and EXP-003; verify all M0 safety and power exit gate criteria are fully satisfied prior to initiating Milestone 1. | `AF-021`, `AF-023`, `AF-180` | `docs` `validation` `milestone-gate` |

---

## 4. Formal M0 Assertion Audit

The following table documents every factual error, hallucinated component, invalid model number, ungrounded specification, and unsupported quantitative tolerance identified across the legacy M0 tasks (`AF-011` through `AF-024`, and `AF-171`), establishing the authoritative correction for each.

| Assertion / Claim | Old Task | Classification | Authority Source | Corrected Value / Disposition |
|---|---|---|---|---|
| **Panel Model P1.53-128×64:** Task text asserts panels are "P1.53-128×64 form factor" | `AF-011`, `AF-020`, `AF-023` | Hallucinated Part Model / Pitch | [`docs/PROJECT.md`](../PROJECT.md) §1.1, §3.1; [`docs/BOM.md`](../BOM.md) line 17 | **Corrected to P2 128×64.** All panels are P2 indoor RGB LED matrix modules (2.0 mm pitch, 128×64 pixels, 256×128 mm module dimensions, 1/32 scan, HUB75E interface, seller model ref P2-32S / SMD1515). |
| **PSU Model RD-65A:** Task text asserts PSU is "RD-65A" with dual 5V/12V ratings | `AF-011`, `AF-018` | Hallucinated Part Model | [`docs/PROJECT.md`](../PROJECT.md) §3.4; [`docs/BOM.md`](../BOM.md) line 53; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 | **Corrected to A-200-5.** The purchased centralized power supply is an A-200-5 switching power supply rated for 5 V DC / 40 A / 200 W nominal (single output). |
| **12 V PSU Output Rail:** Task text asserts PSU has a 12 V rail requiring measurement (12.00–12.40 V) at t=0, 1, 5, 10 min | `AF-017`, `AF-018` | Severe Hallucination (Dual-Rail PSU) | [`docs/PROJECT.md`](../PROJECT.md) §3.4; [`docs/BOM.md`](../BOM.md) line 53; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 | **Removed 12 V rail entirely.** The A-200-5 has no 12 V output terminals or circuitry. All DC output terminals are 5 V (3× V+, 3× COM). Eliminate all 12 V measurement steps, logging tables, and acceptance criteria. |
| **5 A C14 Mains Fuse:** Task asserts C14 inlet includes a "5 A × 20 mm" fast fuse (part P-03c) | `AF-011`, `AF-012` | Hallucinated / Unverified Fuse Rating | [`docs/BOM.md`](../BOM.md) line 57; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 line 98 | **Corrected to candidate T2A 5×20 mm slow-blow glass fuse.** The BOM specifies a 2 A time-delay (T2A) 5×20 mm fuse. Task instructions require physically checking and verifying the fuse marking and physical size upon receipt. |
| **Fused Panel Harness Branches:** Task asserts panel power harnesses contain "fused branches" and "8 branch fuses" | `AF-011`, `AF-019`, `AF-023` | Hallucinated Harness In-Line Fuses | [`docs/BOM.md`](../BOM.md) line 54; [`docs/PROJECT.md`](../PROJECT.md) §3.4 | **Corrected to ordinary unfused 1-to-4 harnesses.** The delivered power harnesses are standard 1-to-4 DC splitters with pure copper wiring and unfused 4-pin Molex/VH connectors for parallel panel power distribution. |
| **Unpurchased Mechanical Frame Items:** Task asserts intake inventory includes "frame screws, M3 nuts, washers, z-clips, standoffs, brackets PLA" | `AF-011` | Hallucinated Unpurchased Mechanical Inventory | [`docs/BOM.md`](../BOM.md) lines 12–88; [`docs/PROJECT.md`](../PROJECT.md) §3.5; [`docs/DECISIONS.md`](../DECISIONS.md) ADR-024 | **Removed from prototype receipt inventory.** No mechanical frame screws, Z-clips, or 3D-printed brackets were purchased in the initial 27 BOM items. Frame design, mounting brackets, and enclosure materials are deferred to Milestone MF per ADR-024. |
| **Synthetic Alphanumeric BOM Codes:** Task text uses synthetic catalog IDs (`C-04a`, `H-03`, `H-06`, `H-07`, `H-09`, `H-15`, `H-16`, `H-18`, `H-19`, `P-01`, `P-02`, `P-03c`) | `AF-011`, `AF-012`, `AF-013`, `AF-014`, `AF-019` | Synthetic Catalog Notation | [`docs/BOM.md`](../BOM.md) | **Replaced with canonical BOM item names and specifications.** BOM.md identifies items by English/Chinese item names, specifications, and vendor links rather than arbitrary alphanumeric codes. |
| **Mixed Ribbon Cable Lengths:** Task asserts shipment includes a mix of 0.5 m (`H-18`) and 1.0 m (`H-19`) HUB75 cables | `AF-011`, `AF-022` | Hallucinated Cable Lengths | [`docs/BOM.md`](../BOM.md) line 18 | **Corrected to 6 × ~40 cm HUB75 ribbon cables.** All ordered ribbon cables are standard 16-pin IDC ribbon cables of ~40 cm length suitable for 2-panel row chaining. |
| **Overly Constrained No-Load Voltage Range:** Task asserts 5 V rail must measure strictly "5.00–5.20 V" without calibration guidance | `AF-018` | Incomplete Specification | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002; [`docs/pm/02-requirements-matrix.md`](02-requirements-matrix.md) R044 | **Corrected to nominal ~5.0 V with V-ADJ calibration.** Acceptable initial no-load reading is 4.80–5.25 V. If reading deviates from 5.00–5.15 V, calibrate using the PSU onboard V-ADJ potentiometer. Stability tolerance: drift ≤ 0.05 V over 10 minutes. |
| **Arbitrary Voltage Drop Rationale:** Task justifies panel voltage drop threshold (≥4.85 V) by referencing "USB spec" | `AF-021` | Ungrounded Specification Rationale | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-003; [`docs/PROJECT.md`](../PROJECT.md) §3.4 | **Corrected to direct DC distribution drop.** The single-panel quiescent test requires ~5.0 V at PSU terminals and ≥4.85 V at the panel connector (maximum acceptable IR drop across 18 AWG harness is <0.15 V under quiescent/light load). |
| **Inaccurate HUB75 Ground Pin Count:** Task asserts HUB75 connector has "usually pin 16 and pin something = 2 GND positions total" | `AF-022` | Inaccurate Pinout Assertion | HUB75E Standard Specification; [`docs/PROJECT.md`](../PROJECT.md) §3.1 | **Corrected to standard HUB75E 16-pin pinout.** HUB75E provides multiple ground pins (typically pins 4, 8, 12, 16 depending on manufacturer / E-line configuration). Keyed notch engagement and pin 1 index orientation are verified. |
| **C14 Terminal Type Ambiguity:** Task speculates C14 rear terminals may be screw-clamp | `AF-013`, `AF-014`, `AF-015`, `AF-016` | Ambiguous Mechanical Spec | [`docs/BOM.md`](../BOM.md) lines 56, 59 | **Clarified as 6.3 mm male spade tabs.** C14 fused/switched inlet module uses 6.3 mm quick-disconnect tabs. Internal AC wiring must terminate in 6.3 mm insulated female spade crimp terminals. |
| **BOM Status Update Ownership:** Task AF-024 includes updating BOM status from ORDERED to RECEIVED in addition to milestone review | `AF-024` | Redundant Responsibility | Refactor Plan; Phase 00 Scope | **Delegated to AF-180 in Phase 00.** `AF-180` owns BOM status updates following receipt inspection; `AF-024` in Phase 01 acts exclusively as the M0 Power Bringup & Exit Gate Aggregator. |

---

## 5. Verification & Traceability Coverage

### 5.1 Experiment Coverage (M0 EXPs)

| Experiment ID | Title | Covering Phase File | Covering Task IDs |
|---|---|---|---|
| **EXP-001** | Hardware inventory and identification | `00-hardware-receipt.md` | `AF-011`, `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179`, `AF-180`, `AF-171` |
| **EXP-002** | PSU no-load bring-up | `01-power-bringup.md` | `AF-012`, `AF-013`, `AF-014`, `AF-015`, `AF-016`, `AF-017`, `AF-018` |
| **EXP-003** | Single-panel power test | `01-power-bringup.md` | `AF-019`, `AF-020`, `AF-021`, `AF-022`, `AF-023`, `AF-024` |

### 5.2 Uncertainty Register Resolution (M0 Uncertainties)

| Uncertainty ID | Description | Resolving Task IDs |
|---|---|---|
| `U-001` | AC mains wiring safety, C14 tab routing, fuse/switch placement | `AF-012`, `AF-014`, `AF-015`, `AF-016`, `AF-017` |
| `U-002` | Panel and harness DC polarity mapping and keying | `AF-019`, `AF-020`, `AF-021`, `AF-023` |
| `U-003` | 5V high-current wiring, crimp quality, ferrule termination | `AF-013`, `AF-014`, `AF-015`, `AF-016`, `AF-019`, `AF-021` |
| `U-004` | Protective Earth (PE) chassis and PSU ground bonding | `AF-016`, `AF-017` |
| `U-006` | Delivery receipt confirmation and component inspection | `AF-011`, `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179`, `AF-180` |
| `U-014` | ESP32-S3 DevKitC header pin soldering status | `AF-177` |
| `U-015` | ESP32-S3 module markings and N16R8 flash/PSRAM confirmation | `AF-177` |
| `U-021` | A-200-5 PSU terminal type, dimensions, and terminal cover | `AF-178` |
| `U-022` | PSU cold-start stability, no-load voltage, inrush behavior | `AF-018` |
| `U-026` | C14 inlet fuse physical dimensions and current rating | `AF-178`, `AF-012` |
| `U-031` | HUB75 ribbon cable keying, pin 1 orientation, and IN/OUT header labels | `AF-174`, `AF-179`, `AF-022` |

---

*This refactor map serves as the authoritative specification for populating `docs/pm/backlog/00-hardware-receipt.md` and `docs/pm/backlog/01-power-bringup.md` during Pass 4A.*
