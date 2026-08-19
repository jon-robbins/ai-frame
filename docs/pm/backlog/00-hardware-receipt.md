# Phase 00 — Hardware Receipt & Inventory (M0)

Covers delivery package intake, physical inspection of all received components against BOM.md, component marking and photography, silkscreen recording, and inventory verification (covers/executes EXP-001).

---

### AF-011 — Reconcile delivered shipment against BOM inventory

**Milestone:** M0
**Depends on:** —
**Labels:** hardware, intake, inventory
**Procedure:** EXP-001
**Resolves:** U-006

#### Do
1. Check outer shipping boxes and package condition for transit damage.
2. Unbox all delivered components (27 purchased BOM line items) onto an ESD-safe workspace.
3. Match packing slips line-by-line against `docs/BOM.md`.
4. Log any missing items, damaged components, or physical discrepancies in the intake manifest.

#### Done when
- Item counts match `docs/BOM.md` across all 27 purchased line items.
- Damaged or missing components (if any) are logged in the intake manifest.
- Physical intake manifest is complete and ready for component identification.

#### If it fails
Quarantine damaged packages, photograph packing slips, and escalate missing items to the supplier.

---

### AF-174 — Identify and record delivered P2 LED panels

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, panel, inventory
**Procedure:** EXP-001
**Resolves:** U-006, U-031

#### Do
1. Inspect all 6 delivered LED panels.
2. Verify 128×64 matrix resolution and 2.0 mm pitch (SMD1515, 256×128 mm).
3. Record silkscreen model markings (P2-32S) and driver ICs (e.g., ICN2037BP / FM6124).
4. Inspect HUB75E port markings, pin 1 orientation, keyed notch, and IN/OUT header labels.

#### Done when
- Silkscreen markings and driver IC part numbers recorded for all 6 panels in evidence log.
- 1/32 scan rate confirmed from board markings.
- HUB75E pin 1, keyed notch, and DATA_IN / DATA_OUT orientations verified on all panels.

#### If it fails
Log mismatched panel models in the intake discrepancy log; quarantine any panel if pitch, dimensions, or scan rate differ from P2 128×64.

---

### AF-175 — Identify delivered HD-WF4 and HD-WF2 controller boards

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, controller, inventory
**Procedure:** EXP-001
**Resolves:** U-006

#### Do
1. Inspect HD-WF4 and HD-WF2 controller boards.
2. Record PCB revisions, SoC markings, and onboard flash/RAM ICs.
3. Inspect HUB75 output ports (4× HUB75 on WF4, 2× HUB75 on WF2).
4. Inspect 5V terminal blocks, polarity markings, and USB ports.

#### Done when
- Controller PCB photos captured and saved with legible IC markings.
- Connector orientations, port counts, and pinouts documented in evidence log.
- 5V power terminal block polarity markings verified.

#### If it fails
Note physical damage, bent pins, or PCB revision discrepancies in the intake log.

---

### AF-176 — Identify delivered LicheeRV Nano-W board and accessories

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, nano, inventory
**Procedure:** EXP-001
**Resolves:** U-006

#### Do
1. Inspect Sipeed LicheeRV Nano-W board and confirm SG2002 SoC and onboard 256 MB DDR3 memory.
2. Inspect Wi-Fi antenna connector, Type-C USB ports, and onboard LEDs.
3. Check pin header straightness and soldering quality.
4. Inspect SanDisk 32GB microSD card and card reader packaging.

#### Done when
- Board revision, SoC markings, and RAM configuration recorded in evidence log.
- SanDisk 32GB microSD card capacity and model markings verified.
- Pin header alignment and physical condition inspected with zero damage.

#### If it fails
Replace or quarantine damaged microSD card or LicheeRV Nano board.

---

### AF-177 — Identify delivered ESP32-S3 boards and logic ICs

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, esp32, inventory
**Procedure:** EXP-001
**Resolves:** U-006, U-014, U-015

#### Do
1. Inspect 2× ESP32-S3 DevKitC-1 N16R8 boards (verify 16 MB flash, 8 MB octal PSRAM markings).
2. Inspect 5× SN74HCT245N DIP-20 buffer ICs, confirming exact HCT logic series (not HC, LS, or AHCT).
3. Inspect 5×7 cm perfboards, 100 nF and 1000 µF capacitors, and DuPont jumper leads.
4. Inspect IC lead straightness and pin header condition.

#### Done when
- ESP32-S3 N16R8 markings verified and logged.
- SN74HCT245N DIP-20 packages confirmed (HCT series verified for 3.3V→5V logic level shifting).
- Lead straightness and pin headers checked with no bent pins.

#### If it fails
Reject non-HCT logic ICs (HCT required for 3.3V→5V level shifting); replace damaged ICs or bent pin headers.

---

### AF-178 — Identify delivered A-200-5 PSU and C14 inlet assembly

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, power, safety, inventory
**Procedure:** EXP-001
**Resolves:** U-006, U-021, U-026

#### Do
1. Inspect A-200-5 switching power supply label to confirm single 5V/40A/200W output rating.
2. Inspect 7 terminal screws (L, N, FG, 3× COM, 3× +V) and clear plastic safety barrier.
3. Inspect IEC C14 fused switched inlet module and 6.3 mm rear spade tabs.
4. Examine candidate T2A 5×20mm slow-blow fuse and fuse carriage fit.

#### Done when
- Single 5V DC output rail confirmed on PSU rating plate (no secondary voltage rails).
- Terminal barrier, screw threads, and terminal markings photographed and verified.
- C14 inlet 6.3 mm spade terminal dimensions confirmed.
- Candidate T2A 5×20mm slow-blow fuse seating and carriage fit confirmed.

#### If it fails
Do NOT energize if voltage rating is not 5V or if FG terminal is missing.

---

### AF-179 — Inspect delivered DC power cables and 1-to-4 harnesses

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware, wiring, inventory
**Procedure:** EXP-001
**Resolves:** U-006, U-031

#### Do
1. Inspect 2× 1-to-4 DC power splitters and 6× panel power leads.
2. Confirm fork/spade terminal crimps and 4-pin DC panel connectors.
3. Verify conductor gauge (18 AWG pure copper) and insulation integrity.
4. Perform unpowered continuity check between red and black forks on all harness branches.

#### Done when
- Red (+5V) and black (GND) polarity consistency confirmed across all 8 harness branches.
- Fork terminal crimps pull-tested gently with zero loose wires.
- Multimeter confirms zero short circuits between red and black fork terminals.

#### If it fails
Replace or re-crimp any harness branch with loose connections, damaged insulation, or reversed polarity.

---

### AF-180 — Update BOM receipt status and component inventory

**Milestone:** M0
**Depends on:** AF-174, AF-175, AF-176, AF-177, AF-178, AF-179
**Labels:** docs, bom, inventory
**Resolves:** U-006

#### Do
1. Review all intake findings and physical inspection notes from AF-174 through AF-179.
2. Update `docs/BOM.md` Status column to `Received` for all verified line items.
3. Record physical observations, exact part markings, and revision numbers in BOM notes.
4. Document any missing or quarantined components.

#### Done when
- `docs/BOM.md` status updated for all 27 purchased line items.
- Unresolved discrepancies or quarantined items logged in BOM notes.

#### If it fails
Flag unreceived or quarantined components in BOM notes and update delivery tracking.

---

### AF-171 — Record component identification photos for inventory verification

**Milestone:** M0
**Depends on:** AF-174, AF-175, AF-176, AF-177, AF-178, AF-179
**Labels:** docs, photos, inventory
**Procedure:** EXP-001

#### Do
1. Capture clear, high-resolution macro photos of all received modules, IC markings, and PCB silkscreens.
2. Verify all photos have legible labels and meet lighting/focus standards in `docs/pm/runbooks/evidence.md`.
3. Save photos into `hardware/photos/` using standard naming conventions (`AF-XXX-<view>.jpg`).
4. Link photo references in the EXP-001 intake evidence log.

#### Done when
- High-resolution photos saved with standard naming convention in `hardware/photos/`.
- All component photos referenced and cross-linked in the intake evidence log.

#### If it fails
Re-take blurry, poorly lit, or out-of-focus photos before closing intake.
