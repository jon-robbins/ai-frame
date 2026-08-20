# Milestone 0 (M0), Milestone 1 (M1), Milestone 2 (M2) & Milestone 3 (M3) Refactor Map & Assertion Audit

**Document:** `docs/pm/refactor-map.md`  
**Purpose:** Authoritative migration matrix and formal factual assertion audit for Milestone 0 (M0) tasks (`AF-011` through `AF-024`, plus `AF-171`), Milestone 1 (M1) tasks (`AF-025` through `AF-080`, plus `AF-172`, `AF-173`), Milestone 2 (M2) tasks (`AF-081` through `AF-092`), the Architecture Decision Gate (MG) tasks (`AF-093` through `AF-096`), and the Milestone 3 (M3) tasks (`AF-097` through `AF-108`, plus the newly defined `AF-187`), establishing the exact decompositions, assembly merges, target file destinations, and ground-truth corrections required for backlog restructuring.

---

# PART I — Milestone 0 (M0) Refactor Map & Audit

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
| **AF-014** | Wire C14 L → PSU L (18 AWG; crimp spade + ferrule each end; verify each end continuity) | `CORRECT` | `01-power-bringup.md` | `AF-014` | Retain dedicated Live (L) conductor installation task (C14 6.3mm spade tab → Live conductor from the purchased 3-core 0.75 mm² AC cable; verify actual conductor colors on receipt, typically brown, with bootlace ferrule → PSU L screw terminal). Verify continuity under power-OFF conditions, record measured resistance, and check wiggle stability. |
| **AF-015** | Wire C14 N → PSU N (18 AWG; crimp spade + ferrule; verify continuity + wiggle) | `CORRECT` | `01-power-bringup.md` | `AF-015` | Retain dedicated Neutral (N) conductor installation task (C14 6.3mm spade tab → Neutral conductor from the purchased 3-core 0.75 mm² AC cable; verify actual conductor colors on receipt, typically blue, with bootlace ferrule → PSU N screw terminal). Verify continuity and wiggle stability under power-OFF conditions. |
| **AF-016** | Wire C14 PE → PSU FG / Earth (18 AWG; verify PE continuous, NOT switched, at all switch positions) | `CORRECT` | `01-power-bringup.md` | `AF-016` | Retain dedicated Protective Earth (PE) conductor installation task (C14 6.3mm spade tab → Earth conductor from the purchased 3-core 0.75 mm² AC cable; verify actual conductor colors on receipt, typically green/yellow, with bootlace ferrule → PSU FG terminal). Verify PE is 100% continuous and unswitched across both switch positions. |
| **AF-017** | Visual + continuity inspection of all three AC wires — no stray strands, insulation, strain relief candidates | `CORRECT` | `01-power-bringup.md` | `AF-017` | Retain pre-energization visual inspection of all 3 AC lines; verify direct L/N/PE circuit continuity as applicable, and electrical isolation between L↔PE and N↔PE in both switch states. Do not require L↔N to be open through the connected PSU. Remove hallucinated reference to 12 V PSU output. |
| **AF-018** | PSU no-load energize EXP-002 (wall plug in, switch ON, ALL OUTPUTS DISCONNECTED — measure 5 V, 12 V, 10 min hold) | `CORRECT` | `01-power-bringup.md` | `AF-018` | Correct and retain EXP-002 PSU no-load energization task. **REMOVE hallucinated 12 V rail** and all 12 V measurement steps/tolerances (A-200-5 is single-output 5 V DC / 40 A). Remove invented electrical limits (`4.80–5.25 V`, `5.00–5.15 V`, drift `≤0.05 V`). Reference EXP-002 ground truth: verify correctly polarized, approximately 5 V, stable output with no abnormal behavior; record measured values. Any adjustment toward ~5.0 V is conditional on AF-178 confirming a supported voltage-adjust control and procedure; otherwise record the measured result and stop/escalate. |
| **AF-019** | VERIFY panel power harness branch #1 polarity — PSU OFF (confirm verified positive termination and verified negative/COM termination from AF-179/receipt evidence; continuity on correct pins; reversed = FAIL) | `CORRECT` | `01-power-bringup.md` | `AF-019` | Retain unpowered harness branch #1 polarity and continuity verification before connection. Use the verified positive termination and verified negative/COM termination identified from AF-179/receipt evidence. Verify delivered harness construction on receipt (check wire gauge, check if fused or unfused), verify polarity, and verify connector keying or apply index marking. |
| **AF-020** | VERIFY panel #1 4-pin power connector polarity at panel end (multimeter PSU OFF — confirm markings align with actual cap polarity on PCB V+/V- pins) | `CORRECT` | `01-power-bringup.md` | `AF-020` | Retain dedicated unpowered verification of panel #1 PCB power terminals using onboard capacitor polarity vs silkscreen markings. Align hardware references with P2 128×64 modules. |
| **AF-021** | Panel 1 power test EXP-003 (PSU → harness #1 → panel #1. PSU energize. Measure PSU voltage, panel voltage, and temps. 10-15 min. No data connected) | `CORRECT` | `01-power-bringup.md` | `AF-021` | Retain EXP-003 single-panel cold DC power test. Energize panel #1 via harness branch #1 without HUB75 data connected. Remove invented `≥4.85 V` and `<0.15 V` drop. Reference EXP-003: measure PSU voltage and panel terminal voltage, record both and delta; check for no significant heating, smell, or noise; monitor 10–15 min stability. |
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
| `AF-014` | Wire C14 Live conductor to PSU L terminal | Install Live (L) conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically brown): crimp 6.3mm female spade onto C14 Live tab, crimp bootlace ferrule onto PSU L end; verify continuity under power-OFF conditions, record measured resistance, and perform a wiggle test. | `AF-012`, `AF-013` | `hardware` `safety-review` `power` |
| `AF-015` | Wire C14 Neutral conductor to PSU N terminal | Install Neutral (N) conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically blue): crimp 6.3mm female spade onto C14 Neutral tab, crimp bootlace ferrule onto PSU N end; verify continuity and wiggle test. | `AF-014` | `hardware` `safety-review` `power` |
| `AF-016` | Wire C14 Protective Earth conductor to PSU FG terminal | Install Protective Earth (PE) conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically green/yellow): crimp 6.3mm female spade onto C14 PE tab, crimp bootlace ferrule onto PSU FG terminal; verify continuous unswitched path. | `AF-015` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-017` | Visual and cross-continuity inspection of AC mains wiring | Perform 7-point visual inspection of all crimp joints; verify direct L/N/PE circuit continuity as applicable, and check isolation between L↔PE and N↔PE in both switch states prior to mains energization. Do not require L↔N to be open through the connected PSU. | `AF-016` | `hardware` `safety-review` `power` `polarity-verify` |
| `AF-018` | PSU no-load energization and voltage hold test (EXP-002) | Energize A-200-5 PSU with all DC outputs disconnected; verify single 5V DC output (~5.0 V nominal across +V and COM), and, only if AF-178 confirms a supported voltage-adjust control and procedure, adjust V-ADJ if needed to achieve ~5V stable output; otherwise record the measured result and stop/escalate. Run the 10-min hold, record measured values, and verify no abnormal behavior. | `AF-017` | `hardware` `safety-review` `power` `validation` |
| `AF-019` | Verify panel power harness branch #1 polarity | Conduct an unpowered continuity test on harness branch #1: verify the delivered harness construction from AF-179/receipt evidence, identify and verify the positive termination and negative/COM termination, confirm continuity to 5V+ and COM/GND, verify no cross-short, and mark the connector orientation index. | `AF-018`, `AF-179` | `hardware` `safety-review` `power` `polarity-verify` |
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
| **Overly Constrained No-Load Voltage Range:** Task asserts 5 V rail must measure strictly "5.00–5.20 V" without calibration guidance | `AF-018` | Incomplete Specification / Invented Limits | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-002; [`docs/PROJECT.md`](../PROJECT.md) §3.4 | **Removed invented electrical limits (`4.80–5.25 V`, `5.00–5.15 V`, drift `≤0.05 V`).** Reference EXP-002 ground truth: verify correctly polarized, approximately 5 V, and adjust V-ADJ toward ~5 V only if AF-178 confirms a supported voltage-adjust control and procedure; otherwise record the measured result and stop/escalate. Record measured values (initial voltage, after 10 min, ambient temp, observations) and check for abnormal behavior (heat, smell, buzzing, or unstable voltage). |
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

# PART II — Milestone 1 (M1) Refactor Map & Audit

## 6. Milestone 1 (M1) Executive Summary & Ground Truth Baseline

Milestone 1 establishes the first end-to-end display pipeline driving dynamically rendered arbitrary text onto a single physical 128×64 P2 HUB75E panel from the LicheeRV Nano-W application computer.

During the legacy monolithic backlog draft (`docs/pm/05-backlog.md`), Milestone 1 tasks (`AF-025` through `AF-080`, plus `AF-172`, `AF-173`) suffered from substantial specification drift:
1. **Severe Memory Hallucinations:** Tasks in the Nano software bootstrap track hallucinated a "2 GB LPDDR4 RAM" configuration for the LicheeRV Nano-W (actual: 256 MB DDR3).
2. **Oversized & Monolithic Tasks:** Complex multi-stage protocol exploration tasks (e.g., `AF-044`) bundled five distinct engineering outcomes into single tasks.
3. **Extreme Micro-Step Fragmentation:** The ESP32 level shifter assembly was fragmented into 12 micro-tasks (single pin solder steps), obscuring functional verification.
4. **Mechanical & Hardware Discrepancies:** DevKitC header counts were hallucinated as 80 pins (actual: 44 pins or 40/48 pins), and WF4 port counts were misstated.

This document establishes the authoritative migration matrix, safe assembly consolidations, task decompositions, and ground-truth corrections across five dedicated Milestone 1 phase files:
- `docs/pm/backlog/02-nano-bootstrap.md` — Application host software bootstrap, Python environment, Pillow text rendering, framebuffer abstraction, transport ABC, and resolution agnosticism.
- `docs/pm/backlog/03-wf4-single-panel.md` — Primary stock controller bringup (HD-WF4), single-panel HUB75 driving (EXP-004), vendor software mapping (EXP-007A), open protocol reverse engineering / frame transport (EXP-007B/C), and end-to-end arbitrary text display.
- `docs/pm/backlog/04-esp32-single-panel.md` — Secondary open controller candidate bringup (ESP32-S3 N16R8), SN74HCT245N level buffer assembly (5 consolidated stages), HUB75 DMA driver (EXP-011), Nano-to-ESP32 UART/USB frame transport (EXP-013), thermal checks, and end-to-end arbitrary text display.
- `docs/pm/backlog/05-wf2-investigation.md` — Reference controller investigation (HD-WF2), stock dual-port characterization (EXP-008), SPI flash backup, and community open firmware evaluation (EXP-009).
- `docs/pm/backlog/06-single-panel-gate.md` — Milestone 1 Single-Panel Exit Gate aggregator (`AF-080`: 7-point JIRA exit criteria evaluation across WF4 and ESP32 tracks).

### Ground Truth Authority Reference for M1

| Subsystem / Topic | Ground Truth Specification | Primary Authority Source |
|---|---|---|
| **Application Host SBC** | Sipeed LicheeRV Nano-W (Sophgo SG2002 SoC, 256 MB DDR3 SIP, onboard Wi-Fi, microSD, USB 2 OTG Type-C, headless Linux). (Not 2 GB LPDDR4). | [`docs/PROJECT.md`](../PROJECT.md) §3.2; [`docs/BOM.md`](../BOM.md) line 24 |
| **Primary Controller (WF4)** | Huidu HD-WF4 controller card featuring **4 × HUB75 outputs (X1, X2, X3, X4)**. Three outputs map to three 256×64 display rows in the 2×3 topology; X4 remains unused in V1. | [`docs/PROJECT.md`](../PROJECT.md) §3.3; [`docs/BOM.md`](../BOM.md) line 33; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-004, EXP-006 |
| **Secondary Controller (ESP32)** | ESP32-S3 DevKitC-1 N16R8 (16 MB flash, 8 MB octal PSRAM) paired with **2 × SN74HCT245N DIP-20** logic level shifters on perfboard. Header pin count: standard 2× 22-pin (44 pins total) or 2× 20-pin / 2× 24-pin on generic dev boards. (Not 80 pins). | [`docs/PROJECT.md`](../PROJECT.md) §3.3; [`docs/BOM.md`](../BOM.md) lines 35–42; [`hardware/schematics/PIN_LEVEL_APPENDIX.md`](../../hardware/schematics/PIN_LEVEL_APPENDIX.md) |
| **Reference Controller (WF2)** | Huidu HD-WF2 controller card with 2 × HUB75 outputs (X1, X2). Reference, reverse-engineering, and open-firmware comparison platform only (never deployed in production V1 per ADR-012). | [`docs/PROJECT.md`](../PROJECT.md) §3.3; [`docs/DECISIONS.md`](../DECISIONS.md) ADR-012; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-008, EXP-009 |
| **LED Matrix Panels** | 6 × P2 indoor RGB LED modules, 128×64 pixels, 2.0 mm pitch (256×128 mm), 1/32 scan, 5 V, ~23 W max, HUB75E interface, SMD1515. Seller ref: P2-32S. | [`docs/PROJECT.md`](../PROJECT.md) §1.1, §3.1; [`docs/BOM.md`](../BOM.md) line 17 |
| **Framebuffer Architecture** | Canonical 256×192 logical canvas composed in Python via Pillow, exporting raw RGB24 row-major byte arrays (`w * h * 3` bytes). Decoupled from physical transport. Resolution-agnostic rendering. | [`docs/PROJECT.md`](../PROJECT.md) §2.2, §4.1, §4.2; [`docs/DECISIONS.md`](../DECISIONS.md) ADR-002 |
| **Serial / Debug Interface Safety** | CH340 USB-UART debug connection strictly follows the **3-pin rule**: connect ONLY TX, RX, and common GND. NEVER connect 5 V or 3.3 V power from USB adapters to independently powered boards. | [`docs/pm/runbooks/safety.md`](runbooks/safety.md) CH340; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-013 |
| **HUB75 Signal Safety** | **NO HUB75 hot-plugging.** De-energize panel and controller power before connecting or disconnecting HUB75 ribbon cables; do not invent power-up sequencing. | [`docs/pm/runbooks/safety.md`](runbooks/safety.md) HUB75; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-004, EXP-011 |
| **DC Power Distribution** | Centralized A-200-5 PSU (5 V / 40 A / 200 W nominal). Parallel DC distribution to panels and controllers. Harness construction: **UNKNOWN / verify delivered harness construction on receipt**. | [`docs/PROJECT.md`](../PROJECT.md) §3.4; [`docs/BOM.md`](../BOM.md) line 54 |

---

## 7. M1 Migration Matrix

The table below catalogs all 58 legacy Milestone 1 tasks (`AF-025` through `AF-080`, plus `AF-172`, `AF-173`), defining their refactoring classification, target destination phase file, replacement IDs, and specific actions.

**Classification Taxonomy:**
- `KEEP`: Retain task scope with minor schema/formatting alignment.
- `SPLIT`: Decompose oversized or multi-outcome task into focused, single-outcome tasks.
- `CORRECT`: Amend task description, steps, acceptance criteria, or safety bounds to eliminate hallucinations and match ground truth.
- `MERGE`: Combine redundant or micro-fragmented task steps into a safe, verifiable canonical assembly stage.
- `MERGED`: Step absorbed into a consolidated parent task.
- `SUPERSEDED`: Replaced by a more comprehensive or directly targeted task structure.
- `REMOVE`: Deprecate/obsolete task if entirely superseded or invalid.

| Old ID | Title | Classification | Destination File | Replacement IDs | Action Summary |
|---|---|---|---|---|---|
| **AF-025** | Flash LicheeRV Nano-W microSD card (download image, write, verify) | `CORRECT` | `02-nano-bootstrap.md` | `AF-025` | Retain microSD flashing task. Ground against official Sipeed SG2002 Linux image (Debian/Buildroot lightweight headless for 256 MB DDR3). Verify download checksum and partition structure. Eliminate synthetic catalog codes (`S-01` → Lenovo 64GB microSD). |
| **AF-026** | Nano first boot + serial debug via CH340 | `SPLIT` | `02-nano-bootstrap.md` | `AF-026`, `AF-181`, `AF-182` | **CORRECT & SPLIT.** Eliminate severe hallucination ("2 GB total LPDDR4" → 256 MB DDR3). Split monolithic boot task into 3 single-outcome tasks: `AF-026` (serial console connection & CH340 3-pin verification), `AF-181` (first boot log capture & hardware initialization check), `AF-182` (OS environment and memory/CPU resource audit via `free -m` and `/proc/cpuinfo`). |
| **AF-027** | Nano Wi-Fi configuration + SSH connectivity test | `CORRECT` | `02-nano-bootstrap.md` | `AF-027` | Retain Wi-Fi configuration, static IP / DHCP reservation, mDNS hostname resolution (`ai-frame.local`), and passwordless SSH public key authentication setup. |
| **AF-028** | Minimal Python environment frozen (python3, pip, venv) | `CORRECT` | `02-nano-bootstrap.md` | `AF-028` | Retain Python 3 virtual environment creation (`python3 -m venv`), core package management upgrade, and `requirements-base.txt` dependency baseline generation. |
| **AF-029** | Pillow install + basic smoke test: generate 128×64 solid red PNG | `CORRECT` | `02-nano-bootstrap.md` | `AF-029` | Retain Pillow library installation and 128×64 RGB solid color test image rendering smoke test. |
| **AF-030** | Pillow text renderer: render arbitrary string onto 128×64 RGB canvas | `CORRECT` | `02-nano-bootstrap.md` | `AF-030` | Retain dynamic text rendering test onto 128×64 canvas using Pillow default font. Verify text legibility without clipping. |
| **AF-031** | Standard test-pattern renderer (solid fills, checkerboard, lines, gradient, coords) | `CORRECT` | `02-nano-bootstrap.md` | `AF-031` | Retain Python test pattern generator module implementing the 6 standard test patterns (solid fills, 8×8 checkerboard, diagonal lines, horizontal gradient, vertical gradient, coordinate grid). |
| **AF-032** | Canonical framebuffer abstraction class (`Framebuffer` API) | `CORRECT` | `02-nano-bootstrap.md` | `AF-032` | Retain canonical `Framebuffer` abstraction class implementation (`new`, `set_pixel`, `get_region`, `export_raw_bytes` in RGB24 row-major order, `size`). |
| **AF-033** | Transport interface spec: abstract class `DisplayTransport` | `CORRECT` | `02-nano-bootstrap.md` | `AF-033` | Retain abstract base class definition `DisplayTransport.send_frame(buffer, width, height)` and custom `TransportError` exception hierarchy. |
| **AF-034** | Candidate transport implementation skeleton (stubbed classes) | `CORRECT` | `02-nano-bootstrap.md` | `AF-034` | Retain candidate transport skeleton defining `StubTransport` and placeholder classes for candidate display transports. |
| **AF-035** | End-to-end smoke: arbitrary input text → Pillow → framebuffer → stub | `CORRECT` | `02-nano-bootstrap.md` | `AF-035` | Retain software-only pipeline CLI smoke test connecting runtime user text input to Pillow rendering, framebuffer byte export, and stub transport transmission. |
| **AF-036** | Framebuffer scaling test: 128×64, 256×64, 256×128, 256×192 | `CORRECT` | `02-nano-bootstrap.md` | `AF-036` | Retain multi-resolution scaling verification confirming rendering pipeline is resolution-agnostic across 4 target dimensions without renderer source modifications. |
| **AF-037** | Nano software bootstrap summary and commit | `CORRECT` | `02-nano-bootstrap.md` | `AF-037` | Retain Nano software bootstrap subtrack aggregator and git repository baseline commit. |
| **AF-038** | VERIFY WF4 PCB revision, markings, photos, port count, power connector | `CORRECT` | `03-wf4-single-panel.md` | `AF-038` | Retain WF4 physical identification task. Correct port count specification to **4 HUB75 outputs (X1, X2, X3, X4)** per ground truth. Document power input connector type. |
| **AF-039** | VERIFY WF4 5 V power input connector mating and crimp sample | `CORRECT` | `03-wf4-single-panel.md` | `AF-039` | Retain WF4 power input connector verification and crimp practice task (screw terminal ferrule or DC barrel). |
| **AF-040** | WF4 power wiring to PSU (18 AWG, ferrule, branch assigned) | `CORRECT` | `03-wf4-single-panel.md` | `AF-040` | Retain unpowered WF4 DC power wiring to PSU 5 V rail. Verify delivered harness construction on receipt (check wire gauge, fused/unfused status, polarity). |
| **AF-041** | WF4 X1 → panel IN HUB75 signal cable orientation and connection | `CORRECT` | `03-wf4-single-panel.md` | `AF-041` | Retain unpowered HUB75 signal ribbon connection from WF4 X1 port to panel #1 IN header. Verify keyed notch orientation and index labeling. |
| **AF-042** | WF4 stock firmware → 1 panel EXP-004 (128×64 / 1/32 scan / patterns) | `CORRECT` | `03-wf4-single-panel.md` | `AF-042` | Retain EXP-004 single-panel stock firmware verification (128×64, 1/32 scan, standard test pattern suite, 10-point defect checklist, ≥10 min hold). |
| **AF-043** | Huidu software Phase A EXP-007A: map UI (config, upload, live) | `CORRECT` | `03-wf4-single-panel.md` | `AF-043` | Retain EXP-007A vendor software workflow exploration using the tool/version appropriate for the delivered WF4: configuration, static file upload, and live update interfaces. |
| **AF-044** | Protocol inspection Phase B EXP-007B: reverse-engineered libs & metrics | `SPLIT` | `03-wf4-single-panel.md` | `AF-044`, `AF-183`, `AF-184`, `AF-185`, `AF-186` | **SPLIT.** Decompose 5-outcome monolithic task into single-outcome tasks: `AF-044` (reverse-engineer / identify open-source Huidu protocol library), `AF-183` (transmit test frame from host PC to WF4 via open library), `AF-184` (implement Python frame sender on Nano), `AF-185` (measure Nano-to-WF4 frame latency), `AF-186` (benchmark maximum update frequency and throughput). |
| **AF-045** | Python integration Phase C EXP-007C: automated repeated updates (≥1 hr) | `CORRECT` | `03-wf4-single-panel.md` | `AF-045` | Retain EXP-007C automated continuous update integration (Pillow render → transport → ≥1 hour stability run, controller temperature check). |
| **AF-046** | Pillow text renderer → WF4 end-to-end test (arbitrary user text) | `CORRECT` | `03-wf4-single-panel.md` | `AF-046` | Retain WF4 subtrack terminal pass test (runtime typed text input on Nano → Pillow render → WF4 transport → panel #1, 10-min stable hold, no manual vendor UI clicks). |
| **AF-047** | WF4 M1 subtrack evidence commit + subtrack pass log | `CORRECT` | `03-wf4-single-panel.md` | `AF-047` | Retain WF4 subtrack evidence aggregation and milestone candidate pass logging for ADR-016. |
| **AF-048** | VERIFY ESP32-S3 board rev/dims/module markings/header spacing (EXP-010) | `CORRECT` | `04-esp32-single-panel.md` | `AF-048` | Retain EXP-010 physical board identification. Correct header count hallucination (**2× 22-pin / 44 pins total** or 2× 20-pin / 40 pins; not 80 pins). Verify N16R8 module markings, dimensions, and peripheral locations. |
| **AF-049** | (Conditional) Solder male headers on ESP32-S3 dev board if unsoldered | `CORRECT` | `04-esp32-single-panel.md` | `AF-049` | Retain conditional male header soldering task. Verify visual solder fillet quality and continuity on all soldered pins. |
| **AF-050** | Confirm N16R8 flash/PSRAM in SW via esptool or info sketch | `CORRECT` | `04-esp32-single-panel.md` | `AF-050` | Retain software flash/PSRAM verification (`esptool.py flash_id` and Arduino/ESP-IDF chip info sketch confirming 16 MB flash + 8 MB octal PSRAM). |
| **AF-051** | Identify unavailable GPIOs (flash, octal PSRAM, USB occupied) | `CORRECT` | `04-esp32-single-panel.md` | `AF-051` | Retain GPIO availability analysis (exclude flash, octal PSRAM GPIO33–37, native USB GPIO19/20, strapping pins). |
| **AF-052** | Confirm provisional GPIO mapping against actual board silkscreen | `CORRECT` | `04-esp32-single-panel.md` | `AF-052` | Retain cross-verification of 14 target GPIOs against physical silkscreen and `PIN_LEVEL_APPENDIX.md` §3/§4. Produce final validated pin map for adapter assembly. |
| **AF-053** | Gather ESP32 build materials: 2× SN74HCT245N, perfboard, header, caps, and inspected hookup-wire stock | `CORRECT` | `04-esp32-single-panel.md` | `AF-053` | Retain pre-assembly materials staging for the 2× SN74HCT245N ICs, perfboard, 2×8 keyed HUB75 header, and capacitors. No wire type or gauge is predeclared: inspect received stock for suitable low-current hookup wire; if none is suitable, record a purchase requirement before assembly. |
| **AF-054** | Stage 1 perfboard prep: direct-mount ICs and header, with optional sockets | `CORRECT` | `04-esp32-single-panel.md` | `AF-054` | Retain as **Stage 1 (Perfboard Layout & IC/Header Mounting)** of consolidated 5-stage build. Direct-soldering the SN74HCT245N ICs is the default available path. DIP-20 sockets are optional only if separately obtained and recorded, never a required part; mount the 2×8 keyed HUB75 header (notch facing outward). Verify alignment and pad isolation. |
| **AF-055** | Stage 2 U1 power & control wiring (pins 20, 10, 1, 19) | `MERGE` | `04-esp32-single-panel.md` | `AF-055` | Retain as **Stage 2 (Power, Ground, Decoupling & Direction Control Wiring)**. Consolidate U1/U2 VCC/GND/DIR/OE wiring, 100nF decoupling capacitors directly across pin 20↔10, both HUB75 GND positions, and optional bulk electrolytic into a unified power/control wiring stage. Perform 8 continuity and cross-isolation checks. |
| **AF-056** | Stage 3 U1 decoupling: 100nF across pin20↔pin10 | `MERGED` | `04-esp32-single-panel.md` | `AF-055` | Merged into Stage 2 (`AF-055`). |
| **AF-057** | Stage 4 U2 power & control wiring | `MERGED` | `04-esp32-single-panel.md` | `AF-055` | Merged into Stage 2 (`AF-055`). |
| **AF-058** | Stage 5 U2 decoupling: 100nF across pin20↔pin10 | `MERGED` | `04-esp32-single-panel.md` | `AF-055` | Merged into Stage 2 (`AF-055`). |
| **AF-059** | Stage 6 U1 A-side inputs (ESP32 → U1 pins 2–9) | `MERGE` | `04-esp32-single-panel.md` | `AF-059` | Retain as **Stage 3 (RGB Data & Row Address A/B Signal Wiring)**. Consolidate U1 A-side inputs (ESP32 GPIOs IO5, IO4, IO6, IO15, IO7, IO17, IO8, IO18 → U1 pins 2–9) and U1 B-side outputs (U1 pins 18–11 → HUB75 R1, G1, B1, R2, G2, B2, A, B) into a unified RGB/address wiring stage with 16 individual continuity checks. |
| **AF-060** | Stage 7 U1 B-side outputs (U1 pins 18–11 → HUB75 R1..B) | `MERGED` | `04-esp32-single-panel.md` | `AF-059` | Merged into Stage 3 (`AF-059`). |
| **AF-061** | Stage 8 U2 A-side inputs (ESP32 → U2 pins 2–7) | `MERGE` | `04-esp32-single-panel.md` | `AF-061` | Retain as **Stage 4 (Row Address C/D/E & Control Timing Signal Wiring)**. Consolidate U2 A-side inputs (ESP32 GPIOs IO10, IO9, IO16, IO12, IO11, IO13 → U2 pins 2–7) and U2 B-side outputs (U2 pins 18–13 → HUB75 C, D, E, CLK, LAT, OE) into a unified timing/address wiring stage. Confirm unused pins 8, 9, 11, 12 remain unconnected. |
| **AF-062** | Stage 9 U2 B-side outputs (U2 pins 18–13 → HUB75 C..OE) | `MERGED` | `04-esp32-single-panel.md` | `AF-061` | Merged into Stage 4 (`AF-061`). |
| **AF-063** | Stage 10 HUB75 header GND positions → common GND | `MERGED` | `04-esp32-single-panel.md` | `AF-055` | Merged into Stage 2 (`AF-055`). |
| **AF-064** | Stage 11 Optional 1000µF electrolytic across 5V/GND | `MERGED` | `04-esp32-single-panel.md` | `AF-055` | Merged into Stage 2 (`AF-055`). |
| **AF-065** | Stage 12 Full adapter end-to-end continuity + no-shorts check | `CORRECT` | `04-esp32-single-panel.md` | `AF-065` | Retain as **Stage 5 (Full Adapter Unpowered Continuity & Isolation Audit)**. Verify every intended end-to-end signal path (ESP32 header pin → HUB75 pin) and IC power path, and cover all unintended adjacent-pad/net connections, VCC↔GND isolation, and relevant signal-to-ground isolation before energizing. |
| **AF-066** | Flash known ESP32 HUB75 firmware (MatrixPanel-DMA / WLED-MM) | `CORRECT` | `04-esp32-single-panel.md` | `AF-066` | Retain firmware flashing task (`ESP32-HUB75-MatrixPanel-DMA` or WLED-MM) with validated GPIO struct, 128×64 panel config, 1/32 scan, and octal PSRAM DMA framebuffer allocation. |
| **AF-067** | ESP32 + HCT + 1 panel physical test (EXP-011) | `CORRECT` | `04-esp32-single-panel.md` | `AF-067` | Retain EXP-011 single-panel validation (all HUB75/panel connections de-energized, no hot-plugging, safe energization procedure from experiment/runbook, standard test pattern suite, 10-point defect checklist, refresh rate/PSRAM logging, ≥1 hour stability). |
| **AF-068** | Nano ↔ ESP32 wired transport (EXP-013 UART framing protocol) | `CORRECT` | `04-esp32-single-panel.md` | `AF-068` | Retain EXP-013 wired frame transport (3-wire UART: TX, RX, GND following CH340 safety rule; framing protocol: magic, length, frame#, CRC32; latency and frame transfer time measurement, ≥1 hour continuous transmission). |
| **AF-069** | Pillow text renderer → ESP32 end-to-end test (arbitrary user text) | `CORRECT` | `04-esp32-single-panel.md` | `AF-069` | Retain ESP32 subtrack terminal pass test (runtime typed text input on Nano → Pillow render → UART transport → ESP32 → panel #1, 10-min stable hold, character verification). |
| **AF-070** | (Optional / Spike) Native USB transport fallback exploration | `CORRECT` | `04-esp32-single-panel.md` | `AF-070` | Retain conditional native USB CDC fallback exploration if UART bandwidth proves insufficient for target dashboard refresh. |
| **AF-071** | EXP-011 / EXP-013 result data commit — raw measurement tables | `CORRECT` | `04-esp32-single-panel.md` | `AF-071` | Retain evidence consolidation and repository commit for ESP32 experimental data. |
| **AF-072** | ESP32 M1 subtrack evidence commit + subtrack pass log | `CORRECT` | `04-esp32-single-panel.md` | `AF-072` | Retain ESP32 subtrack aggregator and candidate pass logging for ADR-016. |
| **AF-073** | (Conditional / Spike) ESP32 alternative firmware evaluation | `CORRECT` | `04-esp32-single-panel.md` | `AF-073` | Retain conditional alternative firmware comparison (WLED-MM vs custom sketch) if MatrixPanel-DMA exhibits defects. |
| **AF-074** | ESP32 thermal check — full-white 100% brightness, 15 min | `CORRECT` | `04-esp32-single-panel.md` | `AF-074` | Retain thermal stress test (100% white brightness, 15-min hold, temperature logging of HCT245 U1/U2 ICs and ESP32 module). |
| **AF-075** | ESP32 controller summary — candidate pass/fail notes for ADR-016 | `CORRECT` | `04-esp32-single-panel.md` | `AF-075` | Retain ESP32 candidate synthesis scoring across 13 evaluation criteria feeding ADR-016 architecture decision. |
| **AF-076** | VERIFY WF2 PCB rev, markings, photos, port count, power connector | `CORRECT` | `05-wf2-investigation.md` | `AF-076` | Retain WF2 physical identification task. Confirm 2 × HUB75 ports (X1, X2), record PCB revision silk, power connector type, and USB port. |
| **AF-077** | Stock firmware EXP-008: 1-panel basic tests, max layout, comms | `CORRECT` | `05-wf2-investigation.md` | `AF-077` | Retain EXP-008 stock firmware investigation (1-panel basic patterns, maximum practical layout, inventory of Wi-Fi/USB/network communication methods). |
| **AF-172** | EXP-008 Step 2 coverage: WF2 stock firmware dual HUB75 outputs test | `CORRECT` | `05-wf2-investigation.md` | `AF-172` | Retain independent test pattern suite execution across Output-1 and Output-2 to evaluate symmetry and port health. |
| **AF-173** | EXP-009 Step 1 coverage: WF2 stock firmware backup | `CORRECT` | `05-wf2-investigation.md` | `AF-173` | Retain revision-dependent backup: identify delivered WF2 revision, SoC, flash size, and accessible boot/interface first; use esptool read-flash only when supported, otherwise use the appropriate documented method; require two matching reads or an explicitly documented recovery path before alternative firmware. |
| **AF-078** | Alt/open firmware EXP-009: flash WLED-MM / community build | `CORRECT` | `05-wf2-investigation.md` | `AF-078` | Retain EXP-009 community firmware evaluation (WLED-MM / DMA build flashing, 1-panel driving, live frame API test, evaluation of material benefits vs stock). |
| **AF-079** | WF2 reference subtrack summary — commit evidence | `CORRECT` | `05-wf2-investigation.md` | `AF-079` | Retain WF2 reference subtrack aggregation and synthesis report for ADR-012/ADR-016. |
| **AF-080** | M1 One-Panel Gate (7-point JIRA exit criteria verification) | `CORRECT` | `06-single-panel-gate.md` | `AF-080` | Retain M1 official gate aggregator. Formally audit the 7 Milestone 1 exit criteria from JIRA.md across WF4 (`AF-046`) and/or ESP32 (`AF-069`) candidate tracks (arbitrary text, Nano rendering, controller path, no runtime clicks, 128×64 display, 10-min hold, safety compliance audit). |

---

## 8. M1 Phase File Allocation & Task Breakdown

### 8.1 Phase 02 — Nano Software Bootstrap (`02-nano-bootstrap.md`)

Covers host SBC operating system preparation, serial console verification, network and SSH configuration, Python virtual environment setup, Pillow rendering, canonical framebuffer abstraction, transport ABC specification, and resolution agnosticism.

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-025` | Flash LicheeRV Nano-W microSD card | Download official Sipeed SG2002 Linux image (Debian/Buildroot lightweight headless), verify SHA256, write to Lenovo 64GB microSD via Etcher/dd, and verify dual partitions. | — | `software` `nano` `docs` |
| `AF-026` | Verify Nano first boot and serial console connection | Connect CH340 to Nano following the strict 3-pin rule (TX, RX, GND only; zero VCC connection); power Nano via dedicated 5V USB-C; verify kernel boot output and reach login prompt over 115200 8N1 serial. | `AF-025` | `software` `nano` `safety-review` |
| `AF-181` | Capture and document first boot kernel log | Capture full `dmesg` kernel ring buffer and serial console boot log; document hardware initialization status and peripheral device enumeration. | `AF-026` | `software` `nano` `docs` |
| `AF-182` | Verify OS environment and hardware resources | Execute system resource audit (`cat /etc/os-release`, `free -m` confirming 256 MB DDR3 RAM baseline, `/proc/cpuinfo` confirming SG2002 RISC-V/ARM core). | `AF-026` | `software` `nano` `validation` |
| `AF-027` | Configure Nano Wi-Fi and passwordless SSH access | Connect Nano to local Wi-Fi via NetworkManager (`nmcli`), configure DHCP reservation / static IP, establish mDNS (`ai-frame.local`), and configure passwordless SSH public key authentication. | `AF-182` | `software` `nano` |
| `AF-028` | Create minimal Python 3 virtual environment | Provision Python 3 venv (`~/ai-frame-venv` in the application user's home directory), upgrade pip/setuptools/wheel, install C build headers (`libjpeg-dev`, `zlib1g-dev`, `libfreetype6-dev`), and generate `requirements-base.txt`. | `AF-027` | `software` `nano` |
| `AF-029` | Install Pillow and execute solid color smoke test | Install Pillow 10+ inside venv and generate a 128×64 pure red (`#FF0000`) test image; verify dimensions and pixel values programmatically. | `AF-028` | `software` `nano` |
| `AF-030` | Render dynamic text onto 128×64 RGB canvas | Render arbitrary runtime text string onto a 128×64 RGB canvas using Pillow; export PNG and verify character rendering without clipping. | `AF-029` | `software` `nano` |
| `AF-031` | Implement 6 standard test pattern generator functions | Implement Python test pattern library generating 6 standard patterns (solid fills, 8×8 checkerboard, diagonal lines, horizontal/vertical gradients, coordinate grid) for 128×64. | `AF-030` | `software` `nano` |
| `AF-032` | Implement canonical Framebuffer abstraction class | Implement canonical `Framebuffer` class with exact API (`new`, `set_pixel`, `get_region`, `export_raw_bytes` exporting RGB24 row-major byte arrays, `size`); verify unit test suite. | `AF-031` | `software` `nano` |
| `AF-033` | Define pluggable display transport abstract base interface | Implement abstract base class `DisplayTransport.send_frame(buffer, width, height)` and custom `TransportError` exception hierarchy. | `AF-032` | `software` `nano` |
| `AF-034` | Implement candidate transport skeleton and stub | Implement `StubTransport` class logging frame payload length and dimensions, plus stubbed candidate transport subclasses. | `AF-033` | `software` `nano` |
| `AF-035` | Execute end-to-end software pipeline smoke test | Execute CLI smoke test passing arbitrary command-line text into Pillow renderer, loading into `Framebuffer`, and dispatching to `StubTransport`. | `AF-034` | `software` `nano` `validation` |
| `AF-036` | Validate framebuffer scaling across 4 resolutions | Generate test patterns and verify byte lengths across 128×64, 256×64, 256×128, and 256×192 without renderer code modifications; verify sub-region row cropping. | `AF-035` | `software` `nano` `validation` |
| `AF-037` | Collate and commit Nano software bootstrap codebase | Commit complete Nano application software framework, venv setup documentation, and test scripts to repository under `software/nano/`. | `AF-036` | `software` `nano` `docs` |

### 8.2 Phase 03 — HD-WF4 Single-Panel Bringup (`03-wf4-single-panel.md`)

Covers primary stock controller physical inspection, safe DC power wiring, unpowered HUB75 connection, single-panel driving with stock firmware (EXP-004), vendor software mapping (EXP-007A), open protocol reverse engineering / frame transport (EXP-007B/C), and end-to-end arbitrary text display.

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-038` | Identify HD-WF4 PCB revision, markings, and 4-port layout | Inspect HD-WF4 board; confirm **4 × HUB75 outputs (X1, X2, X3, X4)**; document PCB revision silk, power terminal type, and Wi-Fi antenna; photograph front/rear directly into `hardware/photos/`. | `AF-024` | `hardware` `controller-wf4` `docs` |
| `AF-039` | Verify HD-WF4 5V DC power input connector mating | Inspect WF4 power connector (screw terminal or DC barrel); fabricate and test sample 18 AWG power lead with bootlace ferrules. | `AF-038` | `hardware` `controller-wf4` `power` |
| `AF-040` | Wire HD-WF4 DC power to PSU 5V rail | Install 18 AWG power leads from PSU 5V rail to WF4 power inputs; verify delivered harness construction; record the measured resistance and verify polarity and continuity under power-OFF conditions. | `AF-039`, `AF-024` | `hardware` `controller-wf4` `power` `safety-review` `polarity-verify` |
| `AF-041` | Connect HUB75 signal ribbon from HD-WF4 X1 to panel #1 IN header | Install 16-pin ~40cm HUB75 ribbon cable between WF4 port X1 and panel #1 IN connector with power OFF; verify keyed notch engagement and label cable. | `AF-040`, `AF-022` | `hardware` `controller-wf4` `polarity-verify` |
| `AF-042` | Drive single P2 panel using HD-WF4 stock firmware (EXP-004) | Configure WF4 for 128×64 1/32 scan; execute Standard Test Pattern Suite across panel #1; evaluate against 10-point Standard Defect Checklist; monitor 10–15 min stability. | `AF-041` | `hardware` `controller-wf4` `power` `validation` |
| `AF-043` | Characterize Huidu vendor software workflow (EXP-007 Phase A) | Identify and test the vendor tool/version appropriate for the delivered WF4; document configuration, static file upload, and live update UI surfaces; record transfer times. | `AF-042` | `software` `controller-wf4` `validation` |
| `AF-044` | Reverse-engineer open-source Huidu protocol library for WF4 | Identify and inspect open-source Huidu protocol libraries; document packet framing, command structure, checksums, and network/USB payload formats. | `AF-043` | `software` `controller-wf4` `spike` |
| `AF-183` | Transmit test frame from host PC to WF4 via open protocol library | Transmit a static 128×64 test frame from host PC to WF4 using open protocol library without vendor software; verify correct panel rendering. | `AF-044` | `software` `controller-wf4` `validation` |
| `AF-184` | Implement Python frame sender on Nano communicating with WF4 | Port open protocol transport to Python on Nano; send static 128×64 framebuffer from Nano to WF4 over USB/network link. | `AF-183`, `AF-037` | `software` `controller-wf4` `nano` |
| `AF-185` | Measure Nano-to-WF4 frame transmission latency and update duration | Measure end-to-end latency from Nano frame dispatch to visible panel pixel update; measure raw byte transfer time per frame. | `AF-184` | `software` `controller-wf4` `validation` |
| `AF-186` | Benchmark maximum sustainable update frequency to WF4 | Benchmark continuous sequential frame updates over 30 seconds; determine maximum sustainable frame rate (FPS) without frame drops or controller reset. | `AF-185` | `software` `controller-wf4` `validation` |
| `AF-045` | Automated continuous Python rendering and transmission (EXP-007 Phase C) | Run automated continuous update loop from Nano (render → transport → WF4) for ≥1 hour; verify thermal stability, zero crashes, and zero required vendor software clicks. | `AF-186`, `AF-032` | `software` `controller-wf4` `power` `thermal-review` `validation` |
| `AF-046` | End-to-end arbitrary user text rendering to panel #1 via WF4 | Execute full E2E test with arbitrary runtime user string typed on Nano terminal; verify exact character rendering on panel #1; perform 10-min stable hold. | `AF-045`, `AF-030`, `AF-035` | `validation` `controller-wf4` `critical-path` |
| `AF-047` | Collate and commit WF4 M1 subtrack evidence and candidate pass log | Collate all EXP-004 and EXP-007 measurements, photos, and logs; commit evidence; record preliminary WF4 scores for ADR-016 matrix. | `AF-046` | `docs` `controller-wf4` `validation` |

### 8.3 Phase 04 — ESP32 Single-Panel & Level Shifter (`04-esp32-single-panel.md`)

Covers secondary open controller physical inspection, software flash/PSRAM verification, GPIO mapping validation, 5-stage consolidated SN74HCT245N level buffer assembly and unpowered electrical audit, HUB75 DMA driver bringup (EXP-011), Nano-to-ESP32 UART/USB transport (EXP-013), thermal validation, and end-to-end arbitrary text display.

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-048` | Identify ESP32-S3 DevKitC-1 N16R8 board geometry and markings (EXP-010) | Measure board dimensions; count physical header pins (**2× 22-pin / 44 pins total** or 2× 20/24-pin); confirm N16R8 module markings; photograph directly into `hardware/photos/`. | `AF-024` | `hardware` `controller-esp32` `docs` `validation` |
| `AF-049` | Solder male pin headers to ESP32-S3 dev board if unsoldered | (Conditional) Solder 2.54mm male header strips onto ESP32-S3 board; perform visual inspection and continuity check across all pins. | `AF-048` | `hardware` `controller-esp32` `conditional` |
| `AF-050` | Confirm ESP32-S3 N16R8 16 MB flash and 8 MB octal PSRAM in software | Connect ESP32 via USB; run `esptool.py flash_id` and execute Arduino/ESP-IDF chip info sketch confirming 16 MB flash and 8 MB octal PSRAM. | `AF-048`, `AF-049` | `firmware` `controller-esp32` `validation` |
| `AF-051` | Identify unavailable ESP32-S3 GPIOs | Enumerate all 49 GPIOs; identify pins occupied by flash, octal PSRAM (GPIO33–37), native USB (GPIO19/20), and boot strapping pins; confirm free GPIO list. | `AF-050` | `firmware` `controller-esp32` `docs` |
| `AF-052` | Validate provisional GPIO mapping against physical board silkscreen | Cross-verify 14 target GPIOs against physical board silkscreen and `PIN_LEVEL_APPENDIX.md` §3/§4; generate final validated pin allocation table. | `AF-048`, `AF-051` | `hardware` `controller-esp32` `docs` `validation` |
| `AF-053` | Gather and inspect ESP32 adapter build materials | Stage 2× SN74HCT245N ICs, perfboard, 2×8 keyed HUB75 header, 2× 100nF ceramic caps, and optional 1000µF bulk cap; no wire type or gauge is predeclared, so inspect received stock for suitable low-current hookup wire and record a purchase requirement if none is suitable before assembly. | `AF-011`, `AF-052` | `hardware` `controller-esp32` `docs` |
| `AF-054` | Stage 1: Perfboard Layout & IC/Header Mounting | Layout component positions on perfboard; direct-soldering the ICs is the default available path. Use DIP-20 sockets only if separately obtained and recorded, never as a required part; mount the 2×8 keyed HUB75 header (notch facing outward) and check pad isolation. | `AF-053` | `hardware` `controller-esp32` |
| `AF-055` | Stage 2: Power, Ground, Decoupling & DIR/OE Control Wiring | Wire and solder U1/U2 VCC (pin 20) → +5V, GND (pin 10) → GND, DIR (pin 1) → +5V (A→B), /OE (pin 19) → GND; solder 2× 100nF decoupling caps directly across pin 20↔10; wire both HUB75 GND positions to GND rail; solder optional 1000µF bulk cap; verify 8 continuity paths and VCC↔GND isolation. *(Absorbs legacy AF-056, AF-057, AF-058, AF-063, AF-064).* | `AF-054` | `hardware` `controller-esp32` `power` `safety-review` `polarity-verify` |
| `AF-059` | Stage 3: RGB Data & Row Address A/B Signal Wiring | Wire and solder the 8 high-speed RGB and lower address lines: ESP32 GPIOs (IO5, IO4, IO6, IO15, IO7, IO17, IO8, IO18) → U1 A-inputs (pins 2–9) → U1 B-outputs (pins 18–11) → HUB75 header (R1, G1, B1, R2, G2, B2, A, B); perform 16 individual continuity tests. *(Absorbs legacy AF-060).* | `AF-055`, `AF-052` | `hardware` `controller-esp32` |
| `AF-061` | Stage 4: Row Address C/D/E & Control Timing Signal Wiring | Wire and solder the 6 row address and control timing lines: ESP32 GPIOs (IO10, IO9, IO16, IO12, IO11, IO13) → U2 A-inputs (pins 2–7) → U2 B-outputs (pins 18–13) → HUB75 header (C, D, E, CLK, LAT, OE); confirm unused pins 8, 9, 11, 12 remain unconnected; perform 12 continuity tests. *(Absorbs legacy AF-062).* | `AF-059` | `hardware` `controller-esp32` |
| `AF-065` | Stage 5: Full Adapter Unpowered Continuity & Isolation Audit | Perform a complete unpowered electrical audit: verify every intended end-to-end signal path (ESP32 header pin → HUB75 pin) and IC power path, and cover all unintended adjacent-pad/net connections, VCC↔GND isolation, and relevant signal-to-ground isolation before energizing. | `AF-061` | `hardware` `controller-esp32` `validation` `safety-review` |
| `AF-066` | Flash known ESP32 HUB75 driver firmware | Flash ESP32 with `ESP32-HUB75-MatrixPanel-DMA` library using validated 14-GPIO mapping, 128×64 panel config, 1/32 scan, and octal PSRAM DMA framebuffer allocation; verify serial initialization output. | `AF-050`, `AF-065` | `firmware` `controller-esp32` |
| `AF-067` | Single panel physical display test via ESP32+HCT245 (EXP-011) | Connect panel #1 while all HUB75/panel connections are de-energized; prohibit hot-plugging and follow only the safe energization procedure documented by EXP-011/runbook; execute Standard Test Pattern Suite; evaluate 10-point defect checklist; record refresh rate and PSRAM metrics; run ≥1 hour. | `AF-066`, `AF-065`, `AF-021`, `AF-022` | `hardware` `controller-esp32` `power` `validation` |
| `AF-068` | Nano-to-ESP32 wired frame transport bringup (EXP-013) | Establish 3-wire UART link (TX, RX, GND strictly following CH340 safety rule); implement packet framing (magic, length, frame#, CRC32); measure transfer time, latency, and drop rate over ≥1 hour. | `AF-067`, `AF-037` | `firmware` `controller-esp32` `nano` `spike` `validation` |
| `AF-069` | End-to-end arbitrary user text rendering via ESP32 pipeline | Execute full E2E test with arbitrary runtime user string typed on Nano terminal; verify exact character rendering on panel #1 via ESP32 transport; perform 10-min stable hold. | `AF-068`, `AF-030`, `AF-035` | `validation` `controller-esp32` `nano` `critical-path` |
| `AF-070` | Native USB CDC transport fallback exploration for ESP32 | (Conditional / Spike) Evaluate ESP32-S3 native USB CDC transport if UART bandwidth is insufficient; measure frame transfer time and latency over USB. | `AF-068` | `firmware` `controller-esp32` `spike` `conditional` |
| `AF-071` | Collate and commit measurement tables for EXP-011 and EXP-013 | Consolidate EXP-011 and EXP-013 markdown tables, metrics, photos, and serial logs; commit evidence under `docs/pm/evidence/`. | `AF-069` | `docs` `controller-esp32` `validation` |
| `AF-072` | Collate and commit ESP32 M1 subtrack evidence and candidate pass log | Collate complete ESP32 candidate evidence; commit subtrack pass log; prepare preliminary scoring inputs for ADR-016. | `AF-071` | `docs` `controller-esp32` `validation` |
| `AF-073` | Evaluate alternative ESP32 firmware (WLED-MM vs custom sketch) | (Conditional / Spike) Compare WLED-MM against custom DMA driver if MatrixPanel-DMA exhibits defects; document 5-dimension comparison. | `AF-067` | `firmware` `controller-esp32` `spike` `conditional` |
| `AF-074` | Execute ESP32 full-white thermal stress test | Run full-white 100% brightness stress test on ESP32 + HCT adapter for 15 minutes; log temperatures of U1, U2, and ESP32 module at 0, 5, 10, 15 min. | `AF-067` | `hardware` `controller-esp32` `thermal` `thermal-review` `validation` |
| `AF-075` | Synthesize ESP32 controller M1 candidate evaluation for ADR-016 | Aggregate all ESP32 subtask data; fill preliminary 13-criterion scoring matrix for EXP-014 / ADR-016; record candidate pass/fail determination. | `AF-072`, `AF-074` | `docs` `controller-esp32` `decision` `validation` |

### 8.4 Phase 05 — HD-WF2 Reference Investigation (`05-wf2-investigation.md`)

Covers reference-only controller physical inspection, stock dual-output characterization (EXP-008), concrete SPI flash backup, and community open firmware evaluation (EXP-009).

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-076` | Identify HD-WF2 PCB revision, markings, and 2-port layout | Inspect HD-WF2 board; confirm 2 × HUB75 outputs (X1, X2); document PCB revision silk, power terminal type, and USB interface; photograph directly into `hardware/photos/`. | `AF-024` | `firmware` `controller-wf2` `docs` `spike` |
| `AF-077` | Characterize HD-WF2 stock firmware capabilities on single panel (EXP-008) | Execute basic test patterns on single panel using WF2 stock firmware; determine maximum practical layout; inventory Wi-Fi/USB/network communication methods. | `AF-076` | `firmware` `controller-wf2` `validation` `spike` |
| `AF-172` | Verify HD-WF2 stock firmware on both HUB75 output ports | Execute Standard Test Pattern Suite independently on Output-1 and Output-2; compare color mapping, scan offset, and output symmetry side-by-side. | `AF-077` | `hardware` `firmware` `controller-wf2` `validation` `spike` `docs` |
| `AF-173` | Backup HD-WF2 stock firmware via revision-appropriate method | Identify the delivered WF2 revision, SoC, flash size, and accessible boot/interface; use esptool read-flash only if supported, otherwise use the appropriate documented backup method; require two matching reads or an explicitly documented recovery path before alternative firmware. | `AF-076`, `AF-077` | `firmware` `controller-wf2` `docs` `safety-review` |
| `AF-078` | Evaluate HD-WF2 alternative/open firmware (EXP-009) | Flash community open firmware (WLED-MM / DMA build); test single panel driving; test live frame API; evaluate if alternative firmware is materially better than stock. | `AF-077`, `AF-173` | `firmware` `controller-wf2` `spike` |
| `AF-079` | Collate and commit HD-WF2 reference subtrack summary | Summarize EXP-008 and EXP-009 findings; commit evidence; record conclusions regarding WF2 suitability for ADR-012/ADR-016. | `AF-078` | `docs` `controller-wf2` `spike` |

### 8.5 Phase 06 — Single-Panel Exit Gate (`06-single-panel-gate.md`)

Official Milestone 1 milestone aggregator evaluating the 7-point exit criteria from JIRA.md across the candidate controller tracks.

| Task ID | Title | Summary / Single Outcome | Depends On | Labels |
|---|---|---|---|---|
| `AF-080` | Milestone 1 Single-Panel Exit Gate Aggregator | Formally audit all 7 Milestone 1 exit conditions across WF4 (`AF-046`) and/or ESP32 (`AF-069`) candidate tracks; verify arbitrary runtime text rendering from Nano, zero manual runtime UI clicks, 128×64 panel display, 10-min stable hold, and complete safety checklist compliance. | `AF-046` OR `AF-069` | `validation` `decision` `critical-path` |

---

## 9. Formal M1 Assertion Audit

The following table documents every factual error, hallucinated component, invalid model number, ungrounded specification, and unsupported quantitative tolerance identified across the legacy M1 tasks (`AF-025` through `AF-080`, plus `AF-172`, `AF-173`), establishing the authoritative correction for each.

| Assertion / Claim | Old Task | Classification | Authority Source | Corrected Value / Disposition |
|---|---|---|---|---|
| **Nano 2 GB LPDDR4 RAM Hallucination:** Task asserts `free -h` should confirm "2 GB total LPDDR4" / "2 GB RAM (± some reserved; 1.7 GB+ OK)" | `AF-026` | Severe Hallucination (Compute Specs) | [`docs/PROJECT.md`](../PROJECT.md) §3.2; [`docs/BOM.md`](../BOM.md) line 24 | **Corrected to 256 MB integrated DDR3 RAM.** The Sipeed LicheeRV Nano-W utilizes the Sophgo SG2002 SoC with 256 MB SIP DDR3 memory. Software budget is lightweight Linux with Python/Pillow (no heavy desktop/Docker). Split `AF-026` into `AF-026`, `AF-181`, and `AF-182` to audit actual memory resources via `free -m`. |
| **Nano Heavy Linux / Desktop Expectations:** Tasks assume standard heavy Debian Bookworm desktop environment with arbitrary background services | `AF-025`, `AF-026`, `AF-027` | Inaccurate OS Architecture Assumption | [`docs/PROJECT.md`](../PROJECT.md) §3.2; [`docs/DECISIONS.md`](../DECISIONS.md) ADR-011, ADR-023 | **Clarified as lightweight headless Linux.** The 256 MB RAM budget requires a lean headless image (Buildroot or minimal Debian server) with lightweight Python/asyncio service architecture. |
| **HD-WF4 3-Port Assertion:** Task summary asserts "port count X1/X2/X3" | `AF-038` | Inaccurate Hardware Specification | [`docs/PROJECT.md`](../PROJECT.md) §3.3; [`docs/BOM.md`](../BOM.md) line 33 | **Corrected to 4 HUB75 outputs (X1, X2, X3, X4).** The HD-WF4 board physically provides 4 output ports. V1 utilizes 3 outputs (X1, X2, X3) to drive the three 256×64 display rows, leaving X4 unused. |
| **ESP32-S3 DevKitC-1 80-Pin Header Hallucination:** Tasks assert the board has "1×40 pins each side; 80 pins total" | `AF-048`, `AF-049` | Hallucinated Mechanical / Pinout Spec | Espressif ESP32-S3-DevKitC-1 Schematic & Pinout; [`docs/PROJECT.md`](../PROJECT.md) §3.3 | **Corrected to 2× 22-pin headers (44 pins total)** for official DevKitC-1, or 2× 20-pin / 2× 24-pin for generic dev boards. Task instructions require measuring and counting physical pins on delivered hardware. |
| **Fused Controller Power Harness Branches Assertion:** Tasks assert controllers are powered from an assigned "5 V fused branch" or "branch #X of 8 branch fuses" | `AF-026`, `AF-040`, `AF-067`, `AF-173` | Hallucinated Harness In-Line Fuses | [`docs/BOM.md`](../BOM.md) line 54; [`docs/PROJECT.md`](../PROJECT.md) §3.4 | **Changed assertion to UNKNOWN / verify delivered harness construction on receipt.** Verify wire gauge, whether branches are fused or unfused, and polarity upon receipt of delivered harnesses. |
| **Monolithic Protocol Inspection Bundling (AF-044):** Legacy task combined 5 independent engineering outcomes into one monolithic task | `AF-044` | Oversized Task / Multi-Outcome Bundling | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-007 Phase B; Single-Outcome Decomposition Policy | **Split into 5 single-outcome tasks:** `AF-044` (reverse-engineer library), `AF-183` (PC transmission test), `AF-184` (Nano Python sender), `AF-185` (latency measurement), and `AF-186` (update frequency benchmark). |
| **12-Stage Micro-Step Fragmentation (AF-054..AF-065):** Legacy tasks fragmented perfboard soldering into 12 individual single-wire/single-pin steps | `AF-054`..`AF-065` | Over-Fragmented Micro-Steps | [`hardware/schematics/PIN_LEVEL_APPENDIX.md`](../../hardware/schematics/PIN_LEVEL_APPENDIX.md) §3–§7; Single-Outcome Assembly Standard | **Merged into 5 safe, verifiable assembly stages:** `AF-054` (Stage 1: Layout & IC/Header), `AF-055` (Stage 2: Power, GND, Decoupling & DIR/OE), `AF-059` (Stage 3: RGB & Address A/B), `AF-061` (Stage 4: Address C/D/E & Timing), `AF-065` (Stage 5: Full Continuity & Isolation Audit). |
| **Synthetic Alphanumeric BOM Codes in M1:** Legacy tasks used synthetic catalog codes (`S-01`, `C-01`, `C-07`, `H-17`) | `AF-025`, `AF-026`, `AF-048`, `AF-049`, `AF-053` | Synthetic Catalog Notation | [`docs/BOM.md`](../BOM.md) | **Replaced with canonical component names and specifications** (Sipeed LicheeRV Nano-W, Lenovo 64GB microSD, CH340 USB-UART, 2.54mm pin header strips). |
| **Invented Frame Latency & Bandwidth Thresholds:** Legacy tasks asserted arbitrary timing cutoffs ("latency < 1 min", "UART < 1 fps escalate to USB", "USB transfer < 200 ms") | `AF-044`, `AF-045`, `AF-068`, `AF-070` | Ungrounded Quantitative Thresholds | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-007, EXP-013 | **Removed invented limits.** Reference EXP-007 / EXP-013 ground truth: measure and record actual transfer time, latency, and update frequency; evaluate against ambient dashboard requirements (mostly-static information), not high-speed video. |
| **Rigid Provisional Pin Mapping Assumptions:** Legacy tasks assumed fixed numeric pin assignments without verifying physical panel silkscreen or connector keying | `AF-052`, `AF-059`, `AF-060`, `AF-061`, `AF-062` | Unverified Pinout Assumption | [`hardware/schematics/PIN_LEVEL_APPENDIX.md`](../../hardware/schematics/PIN_LEVEL_APPENDIX.md) §5 Warning | **Reinforced mandatory pre-solder verification.** Verify delivered panel connector keying and silkscreen labels against provisional map in `AF-052` before soldering adapter leads. |

---

## 10. M1 Verification & Traceability Coverage

### 10.1 Experiment Coverage (M1 EXPs)

| Experiment ID | Title | Covering Phase File | Covering Task IDs |
|---|---|---|---|
| **EXP-004** | HD-WF4 → One Panel | `03-wf4-single-panel.md` | `AF-038`, `AF-039`, `AF-040`, `AF-041`, `AF-042` |
| **EXP-007** | Nano → WF4 Programmatic Control | `03-wf4-single-panel.md` | `AF-043`, `AF-044`, `AF-183`, `AF-184`, `AF-185`, `AF-186`, `AF-045`, `AF-046`, `AF-047` |
| **EXP-008** | HD-WF2 Stock Firmware | `05-wf2-investigation.md` | `AF-076`, `AF-077`, `AF-172` |
| **EXP-009** | HD-WF2 Alternative/Open Firmware | `05-wf2-investigation.md` | `AF-173`, `AF-078`, `AF-079` |
| **EXP-010** | ESP32-S3 Board Identification | `04-esp32-single-panel.md` | `AF-048`, `AF-049`, `AF-050`, `AF-051`, `AF-052` |
| **EXP-011** | ESP32-S3 → HCT245 → One Panel | `04-esp32-single-panel.md` | `AF-053`, `AF-054`, `AF-055`, `AF-059`, `AF-061`, `AF-065`, `AF-066`, `AF-067`, `AF-071`, `AF-074` |
| **EXP-013** | Nano → ESP32 Wired Frame Transport | `04-esp32-single-panel.md` | `AF-068`, `AF-069`, `AF-070`, `AF-071`, `AF-072`, `AF-075` |

### 10.2 Uncertainty Register Resolution (M1 Uncertainties)

| Uncertainty ID | Description | Resolving Task IDs |
|---|---|---|
| `U-007` | HD-WF4 / HD-WF2 physical dimensions, mounting points, connector positions | `AF-038`, `AF-076` |
| `U-008` | HD-WF2 stock firmware vs open firmware programmability | `AF-077`, `AF-172`, `AF-173`, `AF-078`, `AF-079` |
| `U-009` | Controller board serviceability & mounting constraints | `AF-038`, `AF-076` |
| `U-010` | Huidu protocol documentation & software control API availability | `AF-043`, `AF-044`, `AF-183`, `AF-184` |
| `U-014` | ESP32-S3 DevKitC header pin soldering status | `AF-048`, `AF-049` |
| `U-015` | ESP32-S3 module markings & N16R8 flash/PSRAM confirmation | `AF-048`, `AF-050` |
| `U-027` | Display controller DC current consumption | `AF-040`, `AF-055`, `AF-067`, `AF-074` |
| `U-028` | ESP32-S3 DevKitC-1 physical geometry & pin pitch | `AF-048` |
| `U-029` | ESP32-S3 GPIO availability for HUB75 level shifting | `AF-051`, `AF-052` |
| `U-030` | ESP32-S3 PSRAM buffer sufficiency for HUB75 DMA | `AF-050`, `AF-066`, `AF-067` |
| `U-032` | SN74HCT245N level buffer perfboard build reliability | `AF-054`, `AF-055`, `AF-059`, `AF-061`, `AF-065` |
| `U-033` | ESP32-HUB75-MatrixPanel-DMA library compatibility & refresh quality | `AF-066`, `AF-067`, `AF-073` |
| `U-034` | Nano-to-ESP32 UART bandwidth & frame update latency | `AF-068`, `AF-069` |
| `U-035` | ESP32-S3 native USB CDC transport viability | `AF-068`, `AF-070` |
| `U-036` | End-to-end text rendering & controller pipeline integration | `AF-030`, `AF-035`, `AF-046`, `AF-069`, `AF-080` |

### 10.3 Architecture Decision Record (ADR) Coverage (M1 ADRs)

| ADR ID | Title | Relation to Milestone 1 | Evidence-Producing Tasks |
|---|---|---|---|
| `ADR-002` | Canonical 256×192 framebuffer with transport abstraction | Nano rendering, framebuffer, and candidate transport boundary | `AF-029`, `AF-030`, `AF-032`, `AF-033`, `AF-034`, `AF-035`, `AF-036` |
| `ADR-003` | Separate application compute from HUB75 refresh | Separates Nano application work from WF4 and ESP32 refresh candidates | `AF-035`, `AF-046`, `AF-069` |
| `ADR-004` | Linux + Python on the LicheeRV Nano-W | Host bootstrap, OS/resource audit, and Python application environment | `AF-025`, `AF-026`, `AF-181`, `AF-182`, `AF-028`, `AF-029`, `AF-030`, `AF-035` |
| `ADR-008` | Fused, switched, earthed AC input | No direct M1 evidence; M0 mains evidence remains the authority for this current ADR | — |
| `ADR-009` | Prefer a wired computer-to-controller link | Wired transport exploration for WF4 and ESP32 candidates | `AF-044`, `AF-068`, `AF-070`, `AF-183`, `AF-184`, `AF-185`, `AF-186` |
| `ADR-010` | Test multiple controllers before selection | Comparative physical and software evidence across WF4, WF2, and ESP32 | `AF-042`, `AF-046`, `AF-067`, `AF-069`, `AF-077`, `AF-172`, `AF-080` |
| `ADR-011` | HD-WF4 as primary stock-controller candidate | WF4 geometry, vendor workflow, protocol, and end-to-end evidence | `AF-038`, `AF-042`, `AF-043`, `AF-044`, `AF-045`, `AF-046`, `AF-047` |
| `ADR-012` | HD-WF2 as experimental controller | Reference characterization, backup, and alternative-firmware investigation | `AF-076`, `AF-077`, `AF-172`, `AF-173`, `AF-078`, `AF-079` |
| `ADR-013` | ESP32 evaluation and adapter PCB strategy | ESP32 board audit, adapter assembly, refresh, transport, and thermal evidence | `AF-048`, `AF-050`, `AF-052`, `AF-053`, `AF-054`, `AF-065`, `AF-067`, `AF-069`, `AF-074`, `AF-075` |
| `ADR-014` | HCT buffering for the ESP32→HUB75 interface | SN74HCT245N adapter electrical and display validation | `AF-053`, `AF-054`, `AF-055`, `AF-059`, `AF-061`, `AF-065`, `AF-067` |
| `ADR-016` | Final display-controller architecture | Candidate scoring and final controller decision evidence | `AF-047`, `AF-072`, `AF-075`, `AF-080` |
| `ADR-017` | Final Nano→controller transport | Protocol and transport evidence feeding final selection | `AF-033`, `AF-034`, `AF-044`, `AF-068`, `AF-070`, `AF-183`, `AF-184`, `AF-185`, `AF-186` |

---

# PART III — Milestone 2 (M2) Refactor Map & Audit

## 11. M2 Migration Matrix

M2 is authoritative in `docs/pm/backlog/07-dual-panel.md`. The historical M2 entries are restored as permanent IDs and normalized to the compact task schema; AF-081–AF-090 retain their intended individual outcomes, while AF-091 and AF-092 are the M2 gate and evidence/outcome aggregators. No M0/M1 task is redesigned by this pass.

| Historical ID | Current task / destination | Classification | Migration and lineage disposition |
|---|---|---|---|
| **AF-081** | `07-dual-panel.md` / AF-081 | `KEEP` | Verify panel #2 power polarity and HUB75 orientation unpowered; labels normalized. |
| **AF-082** | `07-dual-panel.md` / AF-082 | `KEEP` | Verify the panel #2 power branch mapping and isolation before energization; retain the no-assumed-branch-count rule. |
| **AF-083** | `07-dual-panel.md` / AF-083 | `KEEP` | Wire the WF4 two-panel chain and separate panel power branches under EXP-005; topology unchanged. |
| **AF-084** | `07-dual-panel.md` / AF-084 | `KEEP` | Configure the WF4 256×64 left-to-right row and record ordering evidence under EXP-005. |
| **AF-085** | `07-dual-panel.md` / AF-085 | `KEEP` | Run the WF4 shared patterns, defect checklist, and EXP-005 stability record. |
| **AF-086** | `07-dual-panel.md` / AF-086 | `CORRECT` | Verify the WF4 x=128 seam with dependency on M1 Nano E2E `AF-046`; seam-crossing gradient, text, and test content must be Nano-rendered and sent through the proven WF4 programmatic path, with Nano-origin evidence under EXP-005. |
| **AF-087** | `07-dual-panel.md` / AF-087 | `KEEP` | Prepare the ESP32 two-panel row and separate panel power branches under EXP-012. |
| **AF-088** | `07-dual-panel.md` / AF-088 | `CORRECT` | Retain the ESP32 256×64 outcome, but require supported variations to be exercised and exactly record tested settings/results without inventing a finite set or threshold. |
| **AF-089** | `07-dual-panel.md` / AF-089 | `KEEP` | Run the ESP32 shared patterns, defect checklist, dynamic tests, and EXP-012 stability record. |
| **AF-090** | `07-dual-panel.md` / AF-090 | `CORRECT` | Verify the ESP32 x=128 seam with dependency on M1 Nano E2E `AF-069`; seam-crossing gradient, text, and test content must be Nano-rendered and sent through the proven Nano→ESP32 transport, with Nano-origin evidence under EXP-012. |
| **AF-091** | `07-dual-panel.md` / AF-091 | `CORRECT / AGGREGATE` | Restore the permanent M2 gate aggregator. It passes M2 when either branch has a complete 256×64 dual-panel evidence set and the passing candidate demonstrates Nano-controlled 256×64 output, including content crossing x=128, through the branch-specific proven transport; both paths are evaluated, but both are not required to pass. |
| **AF-092** | `07-dual-panel.md` / AF-092 | `RESTORE / AGGREGATE` | Restore the permanent M2 subtrack aggregator with concrete output `docs/pm/evidence/AF-092-m2-summary.md`. It records the passing path or paths’ linked evidence and the other candidate path’s linked result or missing-input record, including non-passing or incomplete status when applicable; ADR-016 architecture selection happens later. |

## 12. M2 Factual Assertion Audit

The audit below uses current authoritative sources only. `docs/pm/02-requirements-matrix.md` is stale and is explicitly excluded from M2 lineage or factual authority.

| Assertion / claim | Current authority | Audit disposition |
|---|---|---|
| M2 is a gated two-panel 256×64 canvas milestone; at least one controller path must pass the dual-panel criteria. | [`docs/pm/backlog/README.md`](backlog/README.md) §4, M2; [`docs/JIRA.md`](../JIRA.md) Milestone 2 | AF-091 is the gate aggregator and uses the README gate rule: WF4 AF-086 **or** ESP32 AF-090 may satisfy the gate when the complete criteria evidence passes. |
| The WF4 two-panel experiment chains panels, powers them in parallel, tests 256×64 mapping/order/seam patterns, and runs at least 30 minutes. | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-005 | AF-083–AF-086 preserve this procedure and evidence lineage; no additional electrical threshold is introduced. |
| The ESP32 two-panel experiment chains panels, powers them in parallel, exercises supported display/configuration variations, measures reported refresh, and runs the documented stability observation. | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-012 | AF-087–AF-090 preserve the experiment. AF-088 records exactly which supported variations were tested and their results rather than asserting an invented exhaustive list. |
| JIRA’s Milestone 2 narrative requires Nano-controlled end-to-end updates in addition to the two-panel hardware requirements. | [`docs/JIRA.md`](../JIRA.md) Milestone 2; [`docs/pm/backlog/README.md`](backlog/README.md) §4; [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-005, EXP-007, EXP-012, and EXP-013; M1 [`AF-046`](backlog/03-wf4-single-panel.md) and [`AF-069`](backlog/04-esp32-single-panel.md) | The Nano-controlled E2E requirement is preserved, not qualified away: AF-086 depends on and extends the proven WF4 Nano path from AF-046, AF-090 depends on and extends the proven Nano→ESP32 path from AF-069, and AF-091 requires the passing controller candidate to provide Nano-originated 256×64 seam evidence through its branch-specific transport. |
| The panels are P2 128×64 modules and project power distribution is parallel. | [`docs/PROJECT.md`](../PROJECT.md) §§1.1, 3.1, 3.4; [`docs/BOM.md`](../BOM.md) | AF-081–AF-090 retain the current panel/power context and require delivered-hardware markings and evidence where physical details must be verified. |

## 13. M2 Verification & Traceability Coverage

| Source / evidence domain | Covering task IDs |
|---|---|
| EXP-005 WF4 two-panel row | `AF-081`–`AF-086`, `AF-091`, `AF-092` |
| EXP-012 ESP32 two-panel row | `AF-081`, `AF-082`, `AF-087`–`AF-090`, `AF-091`, `AF-092` |
| M2 gate outcome and architecture-decision inputs | `AF-091`, `AF-092` |
| Current M2 gate definition and cross-document requirements | `docs/pm/backlog/README.md`, `docs/JIRA.md`, `docs/EXPERIMENTS.md` |

The stale `docs/pm/02-requirements-matrix.md` is not used as a source, replacement map, or acceptance authority for this M2 pass.

# PART IV — Architecture Decision Gate (MG) Migration Matrix & Factual Audit

## 14. MG Migration Matrix

MG restores the historical AF-093–AF-096 prerequisite in [`08-architecture-decision.md`](backlog/08-architecture-decision.md). The tasks use the compact schema and current sources: [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-014, [`docs/DECISIONS.md`](../DECISIONS.md) ADR-016/ADR-017, [`docs/JIRA.md`](../JIRA.md), [`README.md`](../../README.md), and the PM runbooks. The stale [`02-requirements-matrix.md`](02-requirements-matrix.md) is excluded. AF-093 completes the measured matrix for WF4 versus the three-controller ESP32 architecture (Nano + three ESP32 controllers); AF-094 records ADR-016 and, after evidence review, sets it `ACCEPTED` naming the winning architecture; AF-095 then records ADR-017 using the accepted controller and sets it `ACCEPTED` naming the transport — each draft remains `PENDING` only while its review is incomplete; AF-096 then performs the registry-driven post-decision sweep only after both decisions are `ACCEPTED`, without choosing a winner in this pre-decision source file, and M3 remains blocked until that sweep completes.

| **ID** | **Destination / lineage** | **Disposition** | **Migration / factual correction** |
|---|---|---|---|
| **AF-093** | `08-architecture-decision.md` / AF-093 | **RESTORE / CORRECT** | Complete all 13 EXP-014 criteria for WF4 versus the three-controller ESP32 architecture (Nano + three ESP32 controllers) from measured, linked evidence; preserve explicit unknowns and do not select a controller. |
| **AF-094** | `08-architecture-decision.md` / AF-094 | **RESTORE / CORRECT** | Record the evidence-linked ADR-016 decision from the completed EXP-014 matrix and, after review, set it `ACCEPTED` naming exactly one architecture; `PENDING` is an interim draft state only, and no winner is asserted before acceptance. |
| **AF-095** | `08-architecture-decision.md` / AF-095 | **RESTORE / CORRECT** | Using the accepted ADR-016 architecture, record the evidence-linked ADR-017 transport decision and, after review, set it `ACCEPTED` naming exactly one transport; `PENDING` is an interim draft state only. |
| **AF-096** | `08-architecture-decision.md` / AF-096 | **RESTORE / CORRECT / GATE** | After both ADR-016 and ADR-017 are reviewed/accepted, execute the ADR-016 post-decision reclassification registry-driven: walk every M3+ controller-specific/ADR-016-conditional entry that exists at execution time (current known M3 targets AF-098, AF-099, AF-187, AF-100), not a fixed task list. AF-096 does not execute while either ADR is `PENDING`, and M3 cannot proceed until the sweep completes. The winner loses `conditional blocked blocked:adr-016` and `Applies if`, gains `critical-path`; the loser gets the exact resolved skip `Skip — ADR-016 selected WF4` or `Skip — ADR-016 selected multi-ESP32`, retains `conditional blocked blocked:adr-016`, and has no `critical-path`. |

## 15. MG Factual Assertion Audit

| Assertion | Current source(s) | Required treatment |
|---|---|---|
| EXP-014 compares WF4 and the three-controller ESP32 architecture (Nano + three ESP32 controllers) across 13 criteria. | [`docs/EXPERIMENTS.md`](../EXPERIMENTS.md) EXP-014; [`docs/JIRA.md`](../JIRA.md) architecture gate | AF-093 fills the matrix from measured evidence and marks unsupported cells unknown rather than inventing scores. |
| ADR-016 and ADR-017 are unresolved decisions. | [`docs/DECISIONS.md`](../DECISIONS.md) index and ADR-016/017 entries | AF-094/AF-095 each record and then accept their decision after evidence review; an ADR remains `PENDING` only while its review is incomplete, and the acceptance names the winner. |
| MG must reclassify downstream conditional branches after both ADR reviews/acceptances. | [`docs/pm/backlog/README.md`](backlog/README.md) MG gate; [`docs/JIRA.md`](../JIRA.md) required fields and label taxonomy; [`docs/pm/runbooks/task-format.md`](runbooks/task-format.md) | AF-096 performs exact machine-readable label and `Applies if` mutations only after both ADRs are accepted, records the before/after result, and gates M3. |
| The stale requirements matrix is not an authority for MG. | [`docs/pm/02-requirements-matrix.md`](02-requirements-matrix.md) is stale; current sources above | Exclude it from MG lineage, acceptance, scoring, and decision rationale. |

---

# PART V — Milestone 3 (M3) Migration Matrix, Factual Audit & Traceability

## 16. M3 Migration Matrix

M3 preserves the historical AF-097–AF-108 lineage and the added AF-187 in [`09-quad-panel.md`](backlog/09-quad-panel.md). Task 6 applies seven live project-level outcomes: AF-097, AF-098, AF-099, AF-187, AF-100, AF-101, and AF-104. AF-102 and AF-103 are historical `SUPERSEDED` first-light lineage absorbed by AF-098 and AF-100; AF-105 through AF-108 are historical `SUPERSEDED` validation/gate lineage absorbed by AF-104. Before ADR-016, the active conditional entries are AF-098, AF-099, AF-187, and AF-100; each has `conditional blocked blocked:adr-016`, its applicable controller label, and `Applies if`. AF-096 then normalizes the registry only after both ADR-016 and ADR-017 are reviewed/accepted: the winner loses those three labels and `Applies if`, gains `critical-path`, and retains its controller label; the loser retains or adds the three labels, loses `critical-path`, and receives exactly `Skip — ADR-016 selected WF4` when multi-ESP32 loses or `Skip — ADR-016 selected multi-ESP32` when WF4 loses. No `[other controller]` placeholder may remain. Only the selected branch executes after AF-096; the pre-decision map does not claim a winner.

| Historical ID | Destination / disposition | Classification | Factual migration and lineage disposition |
|---|---|---|---|
| **AF-097** | `09-quad-panel.md` / AF-097 | `KEEP / CORRECT` | Common unpowered verification of panels #3/#4; depends on AF-096 and records polarity, HUB75 orientation, and physical orientation without assuming markings. |
| **AF-098** | `09-quad-panel.md` / AF-098 | `KEEP / CORRECT / MERGE` | Active pre-decision registry entry for WF4 X1/X2 two-row topology, four separate parallel panel-power branches, 256×128 configuration, and Nano-originated first light; absorbs AF-102 and follows the exact AF-096 winner/loser mutations. |
| **AF-099** | `09-quad-panel.md` / AF-099 | `KEEP / CORRECT` | Pre-decision registry entry for the multi-ESP32 second-controller readiness audit; labels include `conditional blocked blocked:adr-016` and `controller-esp32`, with `Applies if` for the branch condition; after AF-096, apply the exact winner/loser label and line mutations in the shared rule. It completes with exactly one recorded outcome — `READY` with identified hardware or `MISSING` with the exact required items — and does not wait for procurement; no silent purchase or hardware assumption. |
| **AF-187** | `09-quad-panel.md` / AF-187 | `ADD` | Newly defined outcome resolving AF-099's audit into verified second-controller hardware: `READY` hardware is verified in place; `MISSING` hardware is purchased per ADR-013, received, identified, and verified de-energized. Depends on AF-099, and AF-100 depends on it, giving one unambiguous DAG path `AF-099 audit → AF-187 ensure hardware ready → AF-100 assemble`; carries the `purchasing` taxonomy label and the same winner/loser mutations as the other branch entries. |
| **AF-100** | `09-quad-panel.md` / AF-100 | `KEEP / CORRECT / MERGE` | Active pre-decision registry entry for the multi-ESP32 two-row topology, per-controller configuration, dual Nano-row dispatch, and first light; absorbs AF-103 and follows the exact AF-096 winner/loser mutations. |
| **AF-101** | `09-quad-panel.md` / AF-101 | `KEEP / CORRECT` | Nano 256×128 framebuffer extension with two 256×64 row crops and dispatch; preserves the canonical framebuffer/transport boundary. |
| **AF-102** | `09-quad-panel.md` / AF-102 | `SUPERSEDED -> AF-098` | Historical WF4 configuration/first-light outcome absorbed by live AF-098; it is not an AF-096 target or active dependency. |
| **AF-103** | `09-quad-panel.md` / AF-103 | `SUPERSEDED -> AF-100` | Historical multi-ESP32 configuration/first-light outcome absorbed by live AF-100; it is not an AF-096 target or active dependency. |
| **AF-104** | `09-quad-panel.md` / AF-104 | `KEEP / CORRECT / MERGE` | Live EXP-017 validation and M3 gate after AF-098 (WF4) or AF-100 (multi-ESP32). It validates both x=128 seams, the y=64 seam, all-seams-simultaneous content, row updates, coordinates, >=30-minute stability, and PASS/FAIL gate evidence; absorbs AF-105 through AF-108. |
| **AF-105** | `09-quad-panel.md` / AF-105 | `SUPERSEDED -> AF-104` | Historical row synchronization observation absorbed by AF-104. |
| **AF-106** | `09-quad-panel.md` / AF-106 | `SUPERSEDED -> AF-104` | Historical coordinate-grid and boundary-label observation absorbed by AF-104. |
| **AF-107** | `09-quad-panel.md` / AF-107 | `SUPERSEDED -> AF-104` | Historical >=30-minute stability observation absorbed by AF-104. |
| **AF-108** | `09-quad-panel.md` / AF-108 | `SUPERSEDED -> AF-104` | Historical M3 gate aggregation absorbed by AF-104. |

## 17. M3 Factual Assertion Audit

The audit uses current sources in `docs/pm/backlog/README.md`, `docs/JIRA.md`, `docs/PROJECT.md`, `docs/BOM.md`, `docs/DECISIONS.md`, received-hardware evidence, and the shared PM runbooks. `docs/pm/02-requirements-matrix.md` is stale and is expressly excluded from M3 authority, lineage, and acceptance criteria.

| Assertion / claim | Current authority | Audit disposition |
|---|---|---|
| M3 is a gated four-panel 2×2 / 256×128 milestone after architecture selection. | [`docs/pm/backlog/README.md`](backlog/README.md) M3 gate; [`docs/JIRA.md`](../JIRA.md) Milestone 3 | AF-096 precedes M3; live AF-104 checks the selected path and blocks M4 unless the complete gate passes. |
| The M3 physical topology is two 256×64 rows, with four independent panel power branches. | [`docs/JIRA.md`](../JIRA.md) Milestone 3; [`docs/PROJECT.md`](../PROJECT.md) §§3.1, 3.4; [`docs/BOM.md`](../BOM.md) | AF-098 and AF-100 retain separate WF4/multi-ESP32 topologies and parallel power; no panel-power daisy-chain is introduced. |
| M3 uses the applicable winning row topology: WF4 maps X1 to the top row and X2 to the bottom row; the three-controller ESP32 architecture uses the applicable ESP32 row controller(s). | [`docs/JIRA.md`](../JIRA.md) Milestone 3; [`docs/PROJECT.md`](../PROJECT.md) §3.3; [`docs/DECISIONS.md`](../DECISIONS.md) ADR-016 | Before ADR-016, active AF-098, AF-099, AF-187, and AF-100 carry `conditional blocked blocked:adr-016`, their controller labels, and explicit `Applies if` conditions. After AF-096, the winner loses those labels and `Applies if`, gains `critical-path`; the loser retains/adds those labels, loses `critical-path`, and receives exactly `Skip — ADR-016 selected WF4` when the three-controller ESP32 architecture loses or `Skip — ADR-016 selected multi-ESP32` when WF4 loses. |
| The Nano owns the logical framebuffer and row split, while the controller owns HUB75 refresh. | [`docs/PROJECT.md`](../PROJECT.md) §§3.1–3.3; [`docs/DECISIONS.md`](../DECISIONS.md) ADR-002 and ADR-016 | AF-101 supplies Nano-originated 256×128 rows; AF-098 or AF-100 consumes them for first light; AF-104 validates the resulting physical canvas while preserving the transport boundary. |
| EXP-017 is a new M3 experiment, not historical evidence. | [`docs/JIRA.md`](../JIRA.md) Milestone 3 explicitly requests a new experiment ID; current `docs/EXPERIMENTS.md` now defines EXP-017 | The experiment record is marked “Newly defined for M3”; historical AF-097–108 is used only for lineage. |
| Row synchronization is judged from observed deltas against dashboard use, not an invented universal number. | [`docs/pm/backlog/README.md`](backlog/README.md) M3 gate; [`docs/JIRA.md`](../JIRA.md) “synchronized-enough row updates” | AF-104 records method, deltas, conditions, and an explicit dashboard-tolerable assessment without a numeric cutoff. |
| Stability requires at least 30 minutes, with available measurements recorded but no unsupported thermal/CPU thresholds. | [`docs/pm/backlog/README.md`](backlog/README.md) M3 gate; [`docs/JIRA.md`](../JIRA.md) Milestone 3; evidence runbook | AF-104 requires runtime, pattern, logs, and measured temperatures/CPU where available; it does not convert unknown limits into pass/fail numbers. |

The stale requirements matrix is not used to justify its obsolete P1.53, RD-65A, memory, mains, or threshold claims. Where received-hardware evidence is unavailable, M3 tasks require verification or record a blocked/unknown state rather than inventing a specification.

## 18. M3 Verification & Traceability Coverage

| Source / evidence domain | Covering task IDs |
|---|---|
| MG prerequisite and M3 pre-decision branch registry / AF-096 post-ADR-016 single-winning-path normalization | `AF-093`, `AF-094`, `AF-095`, `AF-096`, `AF-098`, `AF-099`, `AF-187`, `AF-100`, `AF-104` |
| Panels #3/#4 unpowered verification and received-hardware evidence | `AF-097` |
| WF4 2×2 topology, first light, and four parallel branches | `AF-098` |
| Multi-ESP32 second-controller readiness, topology, and first light | `AF-099`, `AF-187`, `AF-100` |
| Nano 256×128 framebuffer, two row crops, and transport dispatch | `AF-101`, `AF-098`, `AF-100`, `AF-104` |
| EXP-017 vertical, horizontal, and simultaneous seam crossing | `AF-104` |
| Row synchronization observation and dashboard assessment | `AF-104` |
| Coordinate grid, corner labels, and panel-boundary labels | `AF-104` |
| EXP-017 >=30-minute stability and complete evidence review | `AF-104` |

`docs/EXPERIMENTS.md` EXP-017 is the planned experiment definition; active evidence destinations are `docs/pm/evidence/AF-097-...`, `AF-098-...`, `AF-099-...`, `AF-187-...`, `AF-100-...`, `AF-101-...`, and `AF-104-...`, plus the standard hardware-photo/log paths. The MG prerequisite is restored in `08-architecture-decision.md`; no derived Jira file, M0/M1 file, M2 file, or M4+ file is part of this migration.

*This refactor map serves as the authoritative specification for populating the M0, M1, M2, MG, and M3 phase files during the refactor passes; PART IV covers the restored MG prerequisite and PART V covers the included M3 section for `docs/pm/backlog/09-quad-panel.md`.*

---

## M0 Merge Lineage (Task 3 Apply)

| Absorbed ID | Surviving ID |
|---|---|
| AF-174 | AF-011 |
| AF-175 | AF-011 |
| AF-176 | AF-011 |
| AF-177 | AF-011 |
| AF-178 | AF-011 |
| AF-179 | AF-011 |
| AF-171 | AF-011 |
| AF-012 | AF-014 |
| AF-015 | AF-014 |
| AF-016 | AF-014 |
| AF-017 | AF-014 |
| AF-019 | AF-021 |
| AF-020 | AF-021 |
| AF-022 | AF-021 |
| AF-023 | AF-021 |

---

## M1 Merge Lineage (Task 4 Apply)

This applied-state table is authoritative for M1 compression. It supersedes the earlier M1 decomposition proposals in this map wherever they describe now-absorbed cards as active. Each absorbed ID remains in its phase file as a `SUPERSEDED` stub with the listed replacement; active dependency edges across the backlog registry use the surviving ID only.

| Absorbed ID | Surviving ID |
|---|---|
| AF-181 | AF-026 |
| AF-182 | AF-026 |
| AF-028 | AF-027 |
| AF-031 | AF-029 |
| AF-033 | AF-032 |
| AF-034 | AF-032 |
| AF-036 | AF-032 |
| AF-037 | AF-035 |
| AF-039 | AF-040 |
| AF-041 | AF-042 |
| AF-043 | AF-183 |
| AF-044 | AF-183 |
| AF-184 | AF-183 |
| AF-185 | AF-045 |
| AF-186 | AF-045 |
| AF-047 | AF-045 |
| AF-049 | AF-048 |
| AF-051 | AF-050 |
| AF-053 | AF-052 |
| AF-055 | AF-054 |
| AF-059 | AF-054 |
| AF-061 | AF-054 |
| AF-065 | AF-054 |
| AF-074 | AF-067 |
| AF-070 | AF-068 |
| AF-071 | AF-069 |
| AF-072 | AF-069 |
| AF-073 | AF-069 |
| AF-075 | AF-069 |
| AF-172 | AF-077 |
| AF-079 | AF-078 |

### Applied M1 Survivor Scope

| Survivor | Absorbed project-level coverage retained in the live card |
|---|---|
| AF-026 | Nano serial first boot, raw kernel/boot evidence, and measured OS/resource audit under the CH340 3-pin rule. |
| AF-027 | Nano Wi-Fi, SSH, virtual environment, and reproducible base dependency state. |
| AF-029 | Pillow smoke test and all six standard pattern generators. |
| AF-032 | Framebuffer API, transport ABC/stub boundary, and four-resolution scaling/row-crop validation. |
| AF-035 | End-to-end Nano software smoke test, clean-shell verification, and bootstrap evidence commit. |
| AF-040 | WF4 input identification/mating and power-off 5 V wiring validation. |
| AF-042 | Unpowered HUB75 connection plus stock-WF4 single-panel EXP-004 validation. |
| AF-183 | Vendor workflow characterization, evidence-backed protocol investigation, host sender, and Nano sender. |
| AF-045 | WF4 latency/throughput measurement, one-hour update loop, and cited candidate evidence index. |
| AF-048 | ESP32 board identification and conditionally required header installation. |
| AF-050 | Measured ESP32 flash/PSRAM confirmation and GPIO inventory. |
| AF-052 | Validated 14-signal GPIO map and adapter-material staging. |
| AF-054 | One ESP32 HUB75 adapter with the documented eight-step build, 14-signal continuity, and pre-power isolation audit. |
| AF-067 | ESP32 physical single-panel validation, one-hour observation, and full-white thermal log. |
| AF-068 | Nano-to-ESP32 UART transport plus conditional USB-CDC fallback comparison. |
| AF-069 | ESP32 arbitrary-text E2E proof, conditional firmware comparison, cited scoring, and candidate evidence commit. |
| AF-077 | WF2 stock-firmware characterization on both outputs. |
| AF-078 | WF2 alternative-firmware evaluation and cited reference-subtrack summary. |

---

## M2 + MG Merge Lineage (Task 5 Apply)

This applied-state table is authoritative for M2 compression. Each absorbed M2 ID remains in `07-dual-panel.md` as a `SUPERSEDED` stub with the listed replacement; active dependency edges across the backlog registry use the surviving ID only. MG retains its four audit-approved project-level cards, with AF-093 consuming the consolidated AF-091 M2 evidence.

| Absorbed ID | Surviving ID |
|---|---|
| AF-082 | AF-081 |
| AF-084 | AF-083 |
| AF-085 | AF-083 |
| AF-086 | AF-083 |
| AF-088 | AF-087 |
| AF-089 | AF-087 |
| AF-090 | AF-087 |
| AF-092 | AF-091 |

### Applied M2 Survivor Scope

| Survivor | Absorbed project-level coverage retained in the live card |
|---|---|
| AF-081 | Panel #2 unpowered polarity, HUB75 orientation, isolated branch continuity, cross-pair/short checks, and evidence. |
| AF-083 | WF4 two-panel physical topology, parallel power, 256×64 order/configuration, pattern and defect validation, ≥30-minute stability, and Nano-originated x=128 seam proof. |
| AF-087 | ESP32 two-panel physical topology, parallel power, 256×64 configuration/variations/metrics, pattern and defect validation, ≥1-hour stability, and Nano-originated x=128 seam proof. |
| AF-091 | M2 OR-gate criteria, both candidate subtrack rows, explicit PASS/FAIL and missing-input record, and MG evidence handoff. |

### Applied MG Identity Lineage

| Audit-approved card | Applied live ID | Dependency/registry state |
|---|---|---|
| AF-093 | AF-093 | Depends on consolidated M2 survivor AF-091. |
| AF-094 | AF-094 | Unchanged live architecture-decision card. |
| AF-095 | AF-095 | Unchanged live transport-decision card. |
| AF-096 | AF-096 | Registry-wide sweep semantics retained; current M3 targets are normalized to AF-098, AF-099, AF-187, and AF-100. |

---

## M3 Merge Lineage (Task 6 Apply)

This applied-state table is authoritative for M3 compression. Each absorbed M3 ID remains in `09-quad-panel.md` as a `SUPERSEDED` stub with the listed replacement; active dependency edges use only live IDs. The applied seven-card shape deliberately retains AF-187 as the independently visible readiness/procurement resolution outcome, preserving the executable multi-ESP32 chain `AF-099 audit -> AF-187 resolve hardware -> AF-100 assemble and first light`.

| Absorbed ID | Surviving ID |
|---|---|
| AF-102 | AF-098 |
| AF-103 | AF-100 |
| AF-105 | AF-104 |
| AF-106 | AF-104 |
| AF-107 | AF-104 |
| AF-108 | AF-104 |

### Applied M3 Survivor Scope

| Survivor | Absorbed project-level coverage retained in the live card |
|---|---|
| AF-097 | Panels #3/#4 unpowered polarity, HUB75 IN/OUT/key, physical orientation, and received-hardware evidence. |
| AF-098 | Conditional WF4 2×2 topology, four parallel panel-power branches, 256×128 configuration, and Nano-originated first light formerly covered by AF-102. |
| AF-099 | Conditional second ESP32-path audit with one explicit `READY` or `MISSING` outcome and no hidden procurement wait. |
| AF-187 | Conditional readiness/procurement resolution between AF-099 and AF-100: verify in-place hardware or order, receive, verify, and document the exact hardware under ADR-013. |
| AF-100 | Conditional ESP32 2×2 topology, four parallel branches, per-controller 256×64 configuration, dual-row Nano dispatch, and first light formerly covered by AF-103. |
| AF-101 | Nano 256×128 framebuffer, two exact row crops, selected-transport dispatch, and source/crop/log evidence. |
| AF-104 | EXP-017 all-seam proof, row-update observation and dashboard assessment, coordinate-grid evidence, >=30-minute stability evidence, and the PASS/FAIL M3 gate formerly covered by AF-105 through AF-108. |

### Applied M3 Conditional Registry

The current M3 entries subject to AF-096's registry-wide two-class normalization are AF-098 (WF4), AF-099 and AF-187 (ESP32 readiness), and AF-100 (ESP32 assembly). Before ADR-016 each uses `conditional blocked blocked:adr-016` plus its controller label and an `Applies if` condition. The selected class becomes executable and `critical-path`; the losing class remains blocked with the exact resolved skip line. AF-101 and AF-104 are architecture-independent convergence cards, and no pre-decision file claims a winner.
