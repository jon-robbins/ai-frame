# Phase 05 — HD-WF2 Reference Controller Investigation (M1)

Tasks are maintained as a compact, dependency-driven M1 phase catalog. Hardware facts must be verified against received evidence and the referenced wiring appendix.

---

### AF-076 — Identify HD-WF2 PCB revision, markings, and 2-port layout

**Milestone:** M1
**Depends on:** AF-024
**Labels:** firmware controller-wf2 docs spike
**Context:** Inspect HD-WF2 board; confirm 2 × HUB75 outputs (X1, X2); document PCB revision silk, power terminal type, and USB interface; photograph directly into hardware/photos/.

#### Do
1. Photograph the WF2 and record every revision, power connector, USB interface, and HUB75 label.
2. Count and label the observed outputs.
3. Save the identification table and photos.

#### Done when
- Board identity and physical output labels are recorded.
- Any mismatch remains explicit.

#### If it fails
Do not power an unidentified board; preserve evidence and resolve variant ambiguity.

### AF-077 — Characterize HD-WF2 stock firmware capabilities on single panel (EXP-008)

**Milestone:** M1
**Depends on:** AF-076
**Labels:** firmware controller-wf2 validation spike
**Procedure:** EXP-008
**Context:** Execute basic test patterns on single panel using WF2 stock firmware; determine maximum practical layout; inventory Wi-Fi/USB/network communication methods.

#### Do
1. Configure stock WF2 for one delivered panel and record settings.
2. Run the six standard patterns and inventory available Wi-Fi/USB/network controls.
3. Record maximum observed layout and all visible defects.

#### Done when
- Pattern and communication results are recorded.
- Maximum practical layout is evidence-backed or unknown.

#### If it fails
Power down before cabling changes; preserve settings and isolate panel, cable, and firmware.

### AF-172 — Verify HD-WF2 stock firmware on both HUB75 output ports

**Milestone:** M1
**Depends on:** AF-077
**Labels:** hardware firmware controller-wf2 validation spike docs
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before changing HUB75 wiring or ribbon cables.
**Procedure:** EXP-008
**Context:** Execute Standard Test Pattern Suite independently on Output-1 and Output-2; compare color mapping, scan offset, and output symmetry side-by-side.

#### Do
1. With power off, test Output-1 and Output-2 independently using the same panel/cable.
2. Run the standard patterns on each and compare mapping, offset, and symmetry.
3. Save side-by-side results.

#### Done when
- Each output has a complete pass/fail/unknown record.
- Comparison uses identical inputs.

#### If it fails
Disconnect power before moving cables; label the failing port and return to AF-077.

### AF-173 — Backup HD-WF2 stock firmware via SPI flash dump

**Milestone:** M1
**Depends on:** AF-076, AF-077
**Labels:** firmware controller-wf2 docs safety-review
**Safety:** CH340
**Stop condition:** Do not connect CH340 VCC or 3.3 V; disconnect the adapter before changing target power.
**Procedure:** EXP-009
**Context:** Execute concrete dual-read SPI flash backup using esptool.py read_flash following CH340 3-pin rule; verify SHA256 hash match between read1 and read2; commit backup README.

#### Do
1. Apply the CH340 three-wire rule and identify the WF2 flash interface from evidence.
2. Perform two `esptool.py read_flash` reads with recorded parameters.
3. Hash both files and write the backup README.

#### Done when
- Both dumps exist and SHA256 hashes match or the mismatch is recorded.
- No CH340 power output is connected.

#### If it fails
Disconnect CH340 and target power before rewiring; preserve both dumps and diagnose interface/parameters.

### AF-078 — Evaluate HD-WF2 alternative/open firmware (EXP-009)

**Milestone:** M1
**Depends on:** AF-077, AF-173
**Labels:** firmware controller-wf2 spike
**Procedure:** EXP-009
**Context:** Flash community open firmware (WLED-MM / DMA build); test single panel driving; test live frame API; evaluate if alternative firmware is materially better than stock.

#### Do
1. Flash the documented community firmware only after the stock backup is retained.
2. Test one panel with standard patterns and the available live-frame API.
3. Compare observed behavior with stock without inventing thresholds.

#### Done when
- Firmware version, panel result, API result, and defects are recorded.
- Benefits and regressions are evidence-backed or unknown.

#### If it fails
Restore the verified stock image if boot/display fails; preserve binaries and logs.

### AF-079 — Collate and commit HD-WF2 reference subtrack summary

**Milestone:** M1
**Depends on:** AF-078
**Labels:** docs controller-wf2 spike
**Context:** Summarize EXP-008 and EXP-009 findings; commit evidence; record conclusions regarding WF2 suitability for ADR-012/ADR-016.

#### Do
1. Index EXP-008/009 logs, firmware hashes, photos, and test tables.
2. Summarize stock/open firmware findings and suitability for ADR-012/016.
3. Commit the reviewed reference report.

#### Done when
- Every conclusion cites evidence or is marked unknown.
- Commit hash and artifact index are recorded.

#### If it fails
Return unsupported conclusions to AF-077/078; do not infer suitability from an incomplete run.
