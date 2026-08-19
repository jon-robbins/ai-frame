# Phase 00 — Hardware Receipt & Inventory (M0)

Covers delivery package intake, physical inspection of all received components against BOM.md, component marking and photography, silkscreen recording, and inventory verification (covers/executes EXP-001).

---

### AF-011 — Reconcile delivered shipment against expected BOM line items

**Milestone:** M0
**Depends on:** —
**Labels:** hardware, delivery-review
**Procedure:** EXP-001
**Resolves:** U-006

#### Do
1. Check outer shipping boxes and package condition for transit damage.
2. Unbox all delivered components onto an ESD-safe workspace and reconcile the delivered shipment against expected BOM line items.
3. Match packing slips line-by-line against `docs/BOM.md`.
4. Log any missing items, damaged components, or physical discrepancies in the intake manifest.

#### Done when
- Delivered shipment reconciled against expected BOM line items.
- Damaged or missing components (if any) are logged in the intake manifest.
- Physical intake manifest is complete and ready for component identification.

#### If it fails
Quarantine damaged packages, photograph packing slips, and escalate missing items to the supplier.

---

### AF-174 — Identify and record delivered P2 LED panels

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, delivery-review
**Procedure:** EXP-001
**Resolves:** U-006, U-031

#### Do
1. Inspect all 6 delivered LED panels.
2. Verify 128×64 matrix resolution and 2.0 mm pitch (SMD1515, 256×128 mm).
3. Record silkscreen model markings (P2-32S) and exact visible driver IC markings.
4. Inspect HUB75E port markings, pin 1 orientation, keyed notch, and IN/OUT header labels.
5. Capture macro photos directly into `hardware/photos/`.

#### Done when
- Silkscreen markings and exact driver IC part numbers recorded for all 6 panels in evidence log.
- 1/32 scan rate verification recorded (check board markings if printed, or note confirmation deferred to functional testing).
- HUB75E pin 1, keyed notch, and DATA_IN / DATA_OUT orientations verified on all panels.
- Photos saved to `hardware/photos/`.

#### If it fails
Log mismatched panel models in the intake discrepancy log; quarantine any panel if pitch, dimensions, or scan rate differ from P2 128×64.

---

### AF-175 — Identify delivered HD-WF4 and HD-WF2 controller boards

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, delivery-review, controller-wf4, controller-wf2
**Procedure:** EXP-001
**Resolves:** U-006

#### Do
1. Inspect HD-WF4 and HD-WF2 controller boards.
2. Record PCB revisions, SoC markings, and onboard flash/RAM ICs.
3. Inspect HUB75 output ports (4× HUB75 on WF4, 2× HUB75 on WF2).
4. Inspect 5V terminal blocks, polarity markings, and USB ports.
5. Capture macro photos directly into `hardware/photos/`.

#### Done when
- Controller PCB photos captured and saved with legible IC markings in `hardware/photos/`.
- Connector orientations, port counts, and pinouts documented in evidence log.
- 5V power terminal block polarity markings verified.

#### If it fails
Note physical damage, bent pins, or PCB revision discrepancies in the intake log.

---

### AF-176 — Identify delivered LicheeRV Nano-W board and accessories

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, delivery-review, nano
**Procedure:** EXP-001
**Resolves:** U-006

#### Do
1. Inspect Sipeed LicheeRV Nano-W board and confirm SG2002 SoC and onboard 256 MB DDR3 memory.
2. Inspect Wi-Fi antenna connector, Type-C USB ports, and onboard LEDs.
3. Check pin header straightness and soldering quality.
4. Inspect Lenovo 64GB microSD card per BOM.md.
5. Capture macro photos directly into `hardware/photos/`.

#### Done when
- Board revision, SoC markings, and RAM configuration recorded in evidence log.
- Lenovo 64GB microSD card per BOM.md capacity and model markings verified.
- Pin header alignment and physical condition inspected with zero damage.
- Photos saved to `hardware/photos/`.

#### If it fails
Replace or quarantine damaged microSD card or LicheeRV Nano board.

---

### AF-177 — Identify delivered ESP32-S3 boards and logic ICs

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, delivery-review, controller-esp32
**Procedure:** EXP-001
**Resolves:** U-006, U-014, U-015

#### Do
1. Inspect ESP32-S3 DevKitC-1 N16R8 board (1× prototype BOM), verifying 16 MB flash, 8 MB octal PSRAM markings.
2. Inspect 2× SN74HCT245N DIP-20 buffer ICs per BOM.md, confirming exact HCT logic series (not HC, LS, or AHCT).
3. Inspect 5×7 cm perfboards, 100 nF and 1000 µF capacitors, and DuPont jumper leads.
4. Inspect IC lead straightness and pin header condition.
5. Capture macro photos directly into `hardware/photos/`.

#### Done when
- ESP32-S3 DevKitC-1 N16R8 board (1× prototype BOM) markings verified and logged.
- 2× SN74HCT245N DIP-20 buffer ICs per BOM.md confirmed (HCT series verified for 3.3V→5V logic level shifting).
- Lead straightness and pin headers checked with no bent pins.
- Photos saved to `hardware/photos/`.

#### If it fails
Reject non-HCT logic ICs (HCT required for 3.3V→5V level shifting); replace damaged ICs or bent pin headers.

---

### AF-178 — Identify delivered A-200-5 PSU and C14 inlet assembly

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, delivery-review, power, safety-review
**Procedure:** EXP-001
**Resolves:** U-006, U-021, U-026

#### Do
1. Inspect A-200-5 switching power supply label to confirm single 5V/40A/200W output rating.
2. Inspect terminal screw arrangement (verify delivered terminal block arrangement on receipt) and clear plastic safety barrier.
3. Inspect IEC C14 fused switched inlet module and 6.3 mm rear spade tabs.
4. Examine candidate T2A 5×20mm slow-blow fuse and fuse carriage fit.
5. Capture macro photos directly into `hardware/photos/`.

#### Done when
- Single 5V DC output rail confirmed on PSU rating plate (no secondary voltage rails).
- Terminal barrier, screw threads, and terminal markings photographed and verified.
- C14 inlet 6.3 mm spade terminal dimensions confirmed.
- Candidate T2A 5×20mm slow-blow fuse seating and carriage fit confirmed.
- Photos saved to `hardware/photos/`.

#### If it fails
Do NOT energize if voltage rating is not 5V or if FG terminal is missing.

---

### AF-179 — Inspect delivered DC power cables and 1-to-4 harnesses

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, delivery-review, power
**Procedure:** EXP-001
**Resolves:** U-006, U-031

#### Do
1. Inspect both 1-to-4 DC power harnesses and count branches (expecting 8 branches total).
2. Record conductor colors, wire gauge markings if present, connector/end-terminal types, fused/unfused construction, keying, and visible crimp quality.
3. Verify delivered harness construction and insulation integrity.
4. Perform unpowered continuity check between positive and negative terminals on all harness branches.
5. Capture photos directly into `hardware/photos/`.

#### Done when
- Both 1-to-4 harnesses inspected, branch count confirmed (8 total), and construction details recorded (wire gauge, termination types, fused/unfused status, polarity consistency).
- Harness terminal crimps pull-tested gently with zero loose wires.
- Multimeter confirms zero short circuits between positive and negative terminals.
- Photos saved to `hardware/photos/`.

#### If it fails
Replace or re-crimp any harness branch with loose connections, damaged insulation, or reversed polarity.

---

### AF-180 — Update BOM receipt status and component inventory

**Milestone:** M0
**Depends on:** AF-174, AF-175, AF-176, AF-177, AF-178, AF-179
**Labels:** docs, delivery-review
**Resolves:** U-006

#### Do
1. Review all intake findings and physical inspection notes from AF-174 through AF-179.
2. Update `docs/BOM.md` Status column to `RECEIVED` for all verified line items.
3. Record physical observations, exact part markings, and revision numbers in BOM notes.
4. Document any missing or quarantined components.

#### Done when
- `docs/BOM.md` Status column updated to `RECEIVED` for all verified received line items.
- Unresolved discrepancies or quarantined items logged in BOM notes.

#### If it fails
Flag unreceived or quarantined components in BOM notes and update delivery tracking.

---

### AF-171 — SUPERSEDED

Replaced by: AF-174, AF-175, AF-176, AF-177, AF-178, AF-179

(Do not export to Jira)

