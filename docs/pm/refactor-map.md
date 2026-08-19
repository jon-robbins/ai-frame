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
| **Power Supply Unit (PSU)** | Single-output switching power supply model **A-200-5** (5 V DC / 40 A / 200 W nominal). Verify delivered terminal block arrangement on receipt. No 12 V rail. (Not RD-65A). | [`docs/PROJECT.md`](../PROJECT.md) §3.4; [`docs/BOM.md`](../BOM.md) line 53; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 |
| **AC Mains Inlet & Fuse** | IEC C14 fused/switched inlet module with illuminated rocker switch (determine whether L and N are both switched during AF-012 continuity testing) and 5×20 mm fuse drawer. Candidate fuse: **T2A 5×20 mm slow-blow glass fuse** (2 A time-delay). Verify physical fit and rating upon delivery receipt. (Not 5 A fast-acting). | [`docs/BOM.md`](../BOM.md) lines 56–57; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 |
| **DC Power Distribution** | 2 × 1-to-4 LED panel power harnesses (pure copper wiring, 4 output branches each = 8 branches total for 6 panels, parallel distribution). Harness construction: **UNKNOWN / verify delivered harness construction on receipt** (check wire gauge, check if fused or unfused, verify polarity). | [`docs/BOM.md`](../BOM.md) line 54; [`docs/PROJECT.md`](../PROJECT.md) §3.4 |
| **Purchased Item Count** | Reconcile delivered shipment against BOM rows expected in receipt state. No unpurchased frame screws, M3 nuts, washers, Z-clips, standoffs, or 3D-printed PLA brackets in prototype intake (frame design and mechanical mounting are deferred to Milestone MF per ADR-024). | [`docs/BOM.md`](../BOM.md); [`docs/DECISIONS.md`](../DECISIONS.md) ADR-024 |
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
| **AF-011** | EXP-001 Inventory all boards — photo every, record PCB revisions, confirm markings | `SPLIT` | `00-hardware-receipt.md` | `AF-011`, `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179`, `AF-180` | Decompose monolithic 11-step unboxing/inventory task into 8 single-outcome tasks structured by BOM hardware domain. Reconcile delivered shipment against BOM rows expected in receipt state. Eliminate all hallucinations (P1.53 → P2, RD-65A → A-200-5, 5A fuse → T2A 5×20mm slow-blow, fused harness assertion → UNKNOWN / verify delivered harness construction on receipt, frame screws/Z-clips/brackets/PLA → remove). |
| **AF-012** | VERIFY C14 rear tab routing — L/N/PE pass through fuse and switch before reaching PSU (power OFF, no power anywhere) | `CORRECT` | `01-power-bringup.md` | `AF-012` | Retain dedicated unpowered C14 continuity and tab routing verification task. Correct fuse rating reference from "5 A" to candidate T2A 5×20mm slow-blow fuse. Test and record whether C14 switch is single-pole or double-pole; do not presume 2-pole. Clarify 6.3mm spade terminal interfaces on rear tabs. |
| **AF-013** | Crimp practice + visual sample test — ferrule visual sample test + spade crimp practice | `CORRECT` | `01-power-bringup.md` | `AF-013` | Retain novice practice crimp task (5 ferrule + 5 spade samples on conductors from the purchased 3-core 0.75 mm² AC cable or wire offcuts; verify actual conductor colors on receipt) prior to AC mains assembly. Correct terminal specifications: 6.3mm female insulated spade for C14 tabs, bootlace ferrules for PSU screw barrier terminals. |
| **AF-014** | Wire C14 L → PSU L (18 AWG; crimp spade + ferrule each end; verify each end continuity) | `CORRECT` | `01-power-bringup.md` | `AF-014` | Retain dedicated Live (L) conductor installation task (C14 6.3mm spade tab → Live conductor from the purchased 3-core 0.75 mm² AC cable; verify actual conductor colors on receipt, typically brown, with bootlace ferrule → PSU L screw terminal). Verify continuity (<1 Ω) and wiggle stability under power-OFF conditions. |
| **AF-015** | Wire C14 N → PSU N (18 AWG; crimp spade + ferrule; verify continuity + wiggle) | `CORRECT` | `01-power-bringup.md` | `AF-015` | Retain dedicated Neutral (N) conductor installation task (C14 6.3mm spade tab → Neutral conductor from the purchased 3-core 0.75 mm² AC cable; verify actual conductor colors on receipt, typically blue, with bootlace ferrule → PSU N screw terminal). Verify continuity and wiggle stability under power-OFF conditions. |
| **AF-016** | Wire C14 PE → PSU FG / Earth (18 AWG; verify PE continuous, NOT switched, at all switch positions) | `CORRECT` | `01-power-bringup.md` | `AF-016` | Retain dedicated Protective Earth (PE) conductor installation task (C14 6.3mm spade tab → Earth conductor from the purchased 3-core 0.75 mm² AC cable; verify actual conductor colors on receipt, typically green/yellow, with bootlace ferrule → PSU FG terminal). Verify PE is 100% continuous and unswitched across both switch positions. |
| **AF-017** | Visual + continuity inspection of all three AC wires — no stray strands, insulation, strain relief candidates | `CORRECT` | `01-power-bringup.md` | `AF-017` | Retain pre-energization visual and cross-continuity isolation inspection of all 3 AC lines (L/N, L/PE, N/PE isolation matrix in both switch positions). Remove hallucinated reference to 12 V PSU output. |
| **AF-018** | PSU no-load energize EXP-002 (wall plug in, switch ON, ALL OUTPUTS DISCONNECTED — measure 5 V, 12 V, 10 min hold) | `CORRECT` | `01-power-bringup.md` | `AF-018` | Correct and retain EXP-002 PSU no-load energization task. **REMOVE hallucinated 12 V rail** and all 12 V measurement steps/tolerances (A-200-5 is single-output 5 V DC / 40 A). Remove invented electrical limits (`4.80–5.25 V`, `5.00–5.15 V`, drift `≤0.05 V`). Reference EXP-002 ground truth: verify correctly polarized, approximately 5 V, stable output with no abnormal behavior; record measured values. |
| **AF-019** | VERIFY panel power harness branch #1 polarity — PSU OFF (confirm: red wire = V+ to 5 V+ post contact; black/other = V-; continuity on correct pins; reversed = FAIL) | `CORRECT` | `01-power-bringup.md` | `AF-019` | Retain unpowered harness branch #1 polarity and continuity verification before connection. Verify delivered harness construction on receipt (check wire gauge, check if fused or unfused, verify polarity). Verify connector keying or apply index marking. |
| **AF-020** | VERIFY panel #1 4-pin power connector polarity at panel end (multimeter PSU OFF — confirm markings align with actual cap polarity on PCB V+/V- pins) | `CORRECT` | `01-power-bringup.md` | `AF-020` | Retain dedicated unpowered verification of panel #1 PCB power terminals using onboard capacitor polarity vs silkscreen markings. Align hardware references with P2 128×64 modules. |
| **AF-021** | Panel 1 power test EXP-003 (PSU → harness #1 → panel #1. PSU energize. Measure PSU voltage, AT PANEL voltage, temps. 10-15 min. Panel LEDs off: expected, no data) | `CORRECT` | `01-power-bringup.md` | `AF-021` | Retain EXP-003 single-panel cold DC power test. Energize panel #1 via harness branch #1 without HUB75 data connected. Remove invented `≥4.85 V` and `<0.15 V` drop. Reference EXP-003: measure PSU voltage and panel terminal voltage, record both and delta; check for abnormal heating or voltage drop; monitor 10–15 min stability. Confirm panel LEDs remain dark (expected with no refresh data). |
| **AF-022** | VERIFY HUB75 cable + panel #1 IN connector orientation (keyed notch, printed IN label vs OUT, continuity on GND positions — PSU OFF, no power) | `CORRECT` | `01-power-bringup.md` | `AF-022` | Retain unpowered HUB75 ribbon cable and connector orientation verification. Remove generic pin-number claims; verify pin 1, keying, and orientation on delivered hardware. Correct cable spec to 16-pin ~40cm IDC cable. |
| **AF-023** | Panel polarity verify batch (panels #2 through #6 — 5 more polarities, all PSU OFF) | `CORRECT` | `01-power-bringup.md` | `AF-023` | Retain batch unpowered polarity verification for remaining panels #2 through #6 and remaining harness branches. Verify delivered harness construction on receipt (check wire gauge, check if fused or unfused, verify polarity). Enforce individual per-panel logging in acceptance criteria. |
| **AF-024** | (M0 summary review) — EXP-001/EXP-002/EXP-003 evidence review + BOM.md status update commit | `CORRECT` | `01-power-bringup.md` | `AF-024` | Refocus task as M0 Power Bringup & Exit Gate Aggregator (review EXP-002 and EXP-003 evidence logs, verify M0 exit criteria satisfaction). BOM status updating for hardware receipt is delegated to `AF-180` in Phase 00. |
| **AF-171** | EXP-001 Step 5 coverage — Mirror inventory photos into hardware/photos/ directory path as specified by EXP-001 Procedure | `SUPERSEDED` | `00-hardware-receipt.md` | `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179` | Mark `SUPERSEDED` (replaced by direct photo recording into `hardware/photos/` in tasks AF-174 through AF-179). |

---

## 3. Phase File Allocation & Task Breakdown

### 3.1 Phase 00 — Hardware Receipt & Inventory (`00-hardware-receipt.md`)

Covers physical package intake, unpacking, unboxing verification, item-by-item photography, marking and revision documentation against `BOM.md`, and execution of EXP-001.

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-011` | Reconcile delivered shipment against expected BOM line items | Inspect outer shipping boxes, unpack all items onto clean bench, reconcile delivered shipment against BOM rows expected in receipt state, and document any short-ships or shipping damage. | — | `hardware` `delivery-review` |
| `AF-174` | Identify delivered LED panels | Inspect all 6 × P2 128×64 RGB LED panels (model P2-32S, SMD1515); photograph front/back directly into `hardware/photos/`; inspect HUB75E 16-pin shrouded headers, keyed notches, and power connector markings. | `AF-011` | `hardware` `delivery-review` |
| `AF-175` | Identify delivered WF4 and WF2 controller boards | Inspect Huidu HD-WF4 (4× HUB75) and HD-WF2 (2× HUB75) controller cards; photograph front/back directly into `hardware/photos/`; record PCB revision silk, IC markings, terminal layouts, and Wi-Fi antenna types. | `AF-011` | `hardware` `delivery-review` `controller-wf4` `controller-wf2` |
| `AF-176` | Identify delivered LicheeRV Nano-W board | Inspect Sipeed LicheeRV Nano-W (SG2002, 256MB DDR3); confirm Wi-Fi module presence, microSD card slot, USB-C port, and pin headers; inspect Lenovo 64GB microSD card per BOM.md; photograph front/back directly into `hardware/photos/`. | `AF-011` | `hardware` `delivery-review` `nano` |
| `AF-177` | Identify delivered ESP32-S3 boards and logic ICs | Inspect ESP32-S3 DevKitC-1 N16R8 board (1× prototype BOM), measure board width/length and pin spacing, record N16R8 module markings; inspect 2× SN74HCT245N DIP-20 ICs per BOM.md, 100nF and 1000µF capacitors, and 5×7cm perfboard; photograph directly into `hardware/photos/`. | `AF-011` | `hardware` `delivery-review` `controller-esp32` |
| `AF-178` | Identify delivered A-200-5 PSU and C14 inlet assembly | Inspect A-200-5 switching power supply (5V/40A/200W single output) and C14 fused/switched inlet module; verify delivered terminal block arrangement on receipt; verify fuse drawer contains candidate T2A 5×20mm slow-blow fuse; photograph directly into `hardware/photos/`. | `AF-011` | `hardware` `delivery-review` `power` `safety-review` |
| `AF-179` | Inspect delivered DC power cables and 1-to-4 harnesses | Inspect 2 × 1-to-4 LED panel power harnesses (confirm 4 branches each, verify delivered harness construction: wire gauge, fused/unfused status, 4-pin panel connectors), 6 × 16-pin HUB75 ribbon cables (~40cm), 18 AWG silicone wire, AC power cord, spade terminals, and ferrules; photograph directly into `hardware/photos/`. | `AF-011` | `hardware` `delivery-review` `power` |
| `AF-180` | Update BOM component receipt status | Update status column of all verified received items in `docs/BOM.md` from `ORDERED` to `RECEIVED`, recording specific physical observations, part markings, and revision numbers. | `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179` | `docs` `delivery-review` |
| `AF-171` | SUPERSEDED | SUPERSEDED (replaced by direct photo recording into `hardware/photos/` in tasks AF-174 through AF-179; do not export to Jira). | `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179` | `docs` `delivery-review` |

### 3.2 Phase 01 — Safe Power Bringup (`01-power-bringup.md`)

Covers C14 mains inlet wiring, ferrule/spade crimping, unpowered AC isolation and continuity verification, switching power supply no-load energization (EXP-002), and single-panel cold DC power bringup (EXP-003).

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-012` | Verify C14 rear tab routing through fuse and switch | Conduct unpowered continuity and isolation test across C14 prongs, fuse holder, switch, and rear spade tabs to confirm L is fused and switched, test and record whether switch is single-pole or double-pole, and confirm PE is unswitched. | `AF-178` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-013` | Ferrule and spade crimp practice and inspection | Produce 5 sample ferrule crimps and 5 sample 6.3mm spade crimps on conductors from the purchased 3-core 0.75 mm² AC cable or wire offcuts (verify actual conductor colors on receipt); inspect each against 4-point visual criteria and pull test before real wiring. | `AF-178`, `AF-179` | `hardware` `safety-review` `power` |
| `AF-014` | Wire C14 Live conductor to PSU L terminal | Install Live (L) conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically brown): crimp 6.3mm female spade onto C14 Live tab, crimp bootlace ferrule onto PSU L end; verify continuity (<1 Ω) and wiggle test. | `AF-012`, `AF-013` | `hardware` `safety-review` `power` |
| `AF-015` | Wire C14 Neutral conductor to PSU N terminal | Install Neutral (N) conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically blue): crimp 6.3mm female spade onto C14 Neutral tab, crimp bootlace ferrule onto PSU N end; verify continuity and wiggle test. | `AF-014` | `hardware` `safety-review` `power` |
| `AF-016` | Wire C14 Protective Earth conductor to PSU FG terminal | Install Protective Earth (PE) conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically green/yellow): crimp 6.3mm female spade onto C14 PE tab, crimp bootlace ferrule onto PSU FG terminal; verify continuous unswitched path. | `AF-015` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-017` | Visual and cross-continuity inspection of AC mains wiring | Perform 7-point visual inspection of all crimp joints and cross-continuity electrical isolation matrix (L↔N, L↔PE, N↔PE open in both switch states) prior to mains energization. | `AF-016` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-018` | PSU no-load energization and voltage hold test (EXP-002) | Energize A-200-5 PSU with all DC outputs disconnected; verify single 5V DC output (~5.0 V nominal across +V and COM), adjust V-ADJ if needed to achieve ~5V stable output, run 10-min hold, record measured values, and verify no abnormal behavior. | `AF-017` | `hardware` `safety-review` `power` `validation` |
| `AF-019` | Verify panel power harness branch #1 polarity | Conduct unpowered continuity test on harness branch #1: verify delivered harness construction (wire gauge, fused/unfused status), confirm red wire continuity to 5V+ and black wire to COM/GND; verify no cross-short; mark connector orientation index. | `AF-018`, `AF-179` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-020` | Verify panel #1 PCB power terminal polarity against capacitor | Verify panel #1 4-pin power input pins against negative stripe of onboard decoupling electrolytic capacitor; confirm PCB silkscreen matching; mark panel orientation with index tag. | `AF-174` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-021` | Single-panel cold DC power test (EXP-003) | Connect panel #1 to A-200-5 via harness branch #1 (no HUB75 data connected); energize PSU; measure PSU terminal voltage and panel connector voltage, record both and delta per EXP-003; monitor 10–15 min thermal stability. | `AF-018`, `AF-019`, `AF-020` | `hardware` `safety-review` `power` `validation` |
| `AF-022` | Verify HUB75 cable and panel #1 IN header orientation | Verify panel #1 HUB75 IN header label; verify pin 1, keying, and orientation on delivered hardware; confirm ~40cm ribbon cable keyed notches engage properly; verify GND and signal pin continuity end-to-end under power-OFF conditions. | `AF-174`, `AF-179` | `hardware` `safety-review` `polarity-verify` |
| `AF-023` | Batch verify polarity for panels #2–#6 and harness branches | Execute unpowered polarity verification for remaining 5 panels (#2 through #6) and harness branches #2 through #6; verify delivered harness construction; record individual pass/fail results. | `AF-019`, `AF-020`, `AF-022` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-024` | M0 power bringup review and milestone exit verification | Review evidence files for EXP-002 and EXP-003; verify all M0 safety and power exit gate criteria are fully satisfied prior to initiating Milestone 1. | `AF-021`, `AF-023`, `AF-180` | `docs` `validation` |

---

## 4. Formal M0 Assertion Audit

The following table documents every factual error, hallucinated component, invalid model number, ungrounded specification, and unsupported quantitative tolerance identified across the legacy M0 tasks (`AF-011` through `AF-024`, and `AF-171`), establishing the authoritative correction for each.

| Assertion / Claim | Old Task | Classification | Authority Source | Corrected Value / Disposition |
|---|---|---|---|---|
| **Panel Model P1.53-128×64:** Task text asserts panels are "P1.53-128×64 form factor" | `AF-011`, `AF-020`, `AF-023` | Hallucinated Part Model / Pitch | [`docs/PROJECT.md`](../PROJECT.md) §1.1, §3.1; [`docs/BOM.md`](../BOM.md) line 17 | **Corrected to P2 128×64.** All panels are P2 indoor RGB LED matrix modules (2.0 mm pitch, 128×64 pixels, 256×128 mm module dimensions, 1/32 scan, HUB75E interface, seller model ref P2-32S / SMD1515). |
| **PSU Model RD-65A:** Task text asserts PSU is "RD-65A" with dual 5V/12V ratings | `AF-011`, `AF-018` | Hallucinated Part Model | [`docs/PROJECT.md`](../PROJECT.md) §3.4; [`docs/BOM.md`](../BOM.md) line 53; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 | **Corrected to A-200-5.** The purchased centralized power supply is an A-200-5 switching power supply rated for 5 V DC / 40 A / 200 W nominal (single output). |
| **12 V PSU Output Rail:** Task text asserts PSU has a 12 V rail requiring measurement (12.00–12.40 V) at t=0, 1, 5, 10 min | `AF-017`, `AF-018` | Severe Hallucination (Dual-Rail PSU) | [`docs/PROJECT.md`](../PROJECT.md) §3.4; [`docs/BOM.md`](../BOM.md) line 53; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 | **Removed 12 V rail entirely.** The A-200-5 has no 12 V output terminals or circuitry. All DC output terminals are 5 V (verify delivered terminal block arrangement on receipt). Eliminate all 12 V measurement steps, logging tables, and acceptance criteria. |
| **5 A C14 Mains Fuse:** Task asserts C14 inlet includes a "5 A × 20 mm" fast fuse (part P-03c) | `AF-011`, `AF-012` | Hallucinated / Unverified Fuse Rating | [`docs/BOM.md`](../BOM.md) line 57; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002 line 98 | **Corrected to candidate T2A 5×20 mm slow-blow glass fuse.** The BOM specifies a 2 A time-delay (T2A) 5×20 mm fuse. Task instructions require physically checking and verifying the fuse marking and physical size upon receipt. |
| **Fused Panel Harness Branches:** Task asserts panel power harnesses contain "fused branches" and "8 branch fuses" | `AF-011`, `AF-019`, `AF-023` | Hallucinated Harness In-Line Fuses | [`docs/BOM.md`](../BOM.md) line 54; [`docs/PROJECT.md`](../PROJECT.md) §3.4 | **Changed assertion to UNKNOWN / verify delivered harness construction on receipt.** Verify wire gauge, check whether branches are fused or unfused, and verify polarity upon receipt of delivered harnesses. |
| **Unpurchased Mechanical Frame Items:** Task asserts intake inventory includes "frame screws, M3 nuts, washers, z-clips, standoffs, brackets PLA" | `AF-011` | Hallucinated Unpurchased Mechanical Inventory | [`docs/BOM.md`](../BOM.md) lines 12–88; [`docs/PROJECT.md`](../PROJECT.md) §3.5; [`docs/DECISIONS.md`](../DECISIONS.md) ADR-024 | **Removed from prototype receipt inventory.** No mechanical frame screws, Z-clips, or 3D-printed brackets were purchased in the initial BOM items. Reconcile delivered shipment against BOM rows expected in receipt state. Frame design, mounting brackets, and enclosure materials are deferred to Milestone MF per ADR-024. |
| **Synthetic Alphanumeric BOM Codes:** Task text uses synthetic catalog IDs (`C-04a`, `H-03`, `H-06`, `H-07`, `H-09`, `H-15`, `H-16`, `H-18`, `H-19`, `P-01`, `P-02`, `P-03c`) | `AF-011`, `AF-012`, `AF-013`, `AF-014`, `AF-019` | Synthetic Catalog Notation | [`docs/BOM.md`](../BOM.md) | **Replaced with canonical BOM item names and specifications.** BOM.md identifies items by English/Chinese item names, specifications, and vendor links rather than arbitrary alphanumeric codes. |
| **Mixed Ribbon Cable Lengths:** Task asserts shipment includes a mix of 0.5 m (`H-18`) and 1.0 m (`H-19`) HUB75 cables | `AF-011`, `AF-022` | Hallucinated Cable Lengths | [`docs/BOM.md`](../BOM.md) line 18 | **Corrected to 6 × ~40 cm HUB75 ribbon cables.** All ordered ribbon cables are standard 16-pin IDC ribbon cables of ~40 cm length suitable for 2-panel row chaining. |
| **Overly Constrained No-Load Voltage Range:** Task asserts 5 V rail must measure strictly "5.00–5.20 V" without calibration guidance | `AF-018` | Incomplete Specification / Invented Limits | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002; [`docs/pm/02-requirements-matrix.md`](02-requirements-matrix.md) R044 | **Removed invented electrical limits (`4.80–5.25 V`, `5.00–5.15 V`, drift `≤0.05 V`).** Reference EXP-002 ground truth: verify correctly polarized, approximately 5 V, adjust V-ADJ if needed to achieve ~5 V stable output; record measured values (initial voltage, after 10 min, ambient temp, observations) and check for abnormal behavior (heat, smell, buzzing, or unstable voltage). |
| **Arbitrary Voltage Drop Rationale:** Task justifies panel voltage drop threshold (≥4.85 V) by referencing "USB spec" | `AF-021` | Ungrounded Specification / Invented Limits | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-003; [`docs/PROJECT.md`](../PROJECT.md) §3.4 | **Removed invented `≥4.85 V` and `<0.15 V` drop.** Reference EXP-003 ground truth: measure PSU output voltage and panel terminal voltage, record both and delta; check for abnormal heating, smell, noise, or voltage drop. |
| **Inaccurate HUB75 Ground Pin Count:** Task asserts HUB75 connector has "usually pin 16 and pin something = 2 GND positions total" | `AF-022` | Inaccurate Pinout Assertion | HUB75E Standard Specification; [`docs/PROJECT.md`](../PROJECT.md) §3.1 | **Remove generic pin-number claims; verify actual connector/panel mapping on receipt.** Verify pin 1, keying notch, and orientation on delivered hardware. |
| **C14 Terminal Type Ambiguity:** Task speculates C14 rear terminals may be screw-clamp | `AF-013`, `AF-014`, `AF-015`, `AF-016` | Ambiguous Mechanical Spec | [`docs/BOM.md`](../BOM.md) lines 56, 59 | **Clarified as 6.3 mm male spade tabs.** C14 fused/switched inlet module uses 6.3 mm quick-disconnect tabs. Internal AC wiring from the purchased 3-core 0.75 mm² AC cable must terminate in 6.3 mm insulated female spade crimp terminals. |
| **BOM Status Update Ownership:** Task AF-024 includes updating BOM status from ORDERED to RECEIVED in addition to milestone review | `AF-024` | Redundant Responsibility | Refactor Plan; Phase 00 Scope | **Delegated to AF-180 in Phase 00.** `AF-180` owns BOM status updates following receipt inspection; `AF-024` in Phase 01 acts exclusively as the M0 Power Bringup & Exit Gate Aggregator. |

---

## 5. Verification & Traceability Coverage

### 5.1 Experiment Coverage (M0 EXPs)

| Experiment ID | Title | Covering Phase File | Covering Task IDs |
|---|---|---|---|
| **EXP-001** | Hardware inventory and identification | `00-hardware-receipt.md` | `AF-011`, `AF-174`, `AF-175`, `AF-176`, `AF-177`, `AF-178`, `AF-179`, `AF-180` (`AF-171` superseded) |
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
