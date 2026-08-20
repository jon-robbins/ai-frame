# Phase 04 - ESP32 Single-Panel Bringup & Level Shifter (M1)

Tasks are maintained as a compact, dependency-driven M1 phase catalog. Hardware facts must be verified against received evidence and the referenced wiring appendix.

---

### AF-048 - Identify ESP32-S3 DevKitC-1 board geometry and prepare headers (EXP-010)

**Milestone:** M1
**Depends on:** AF-024
**Labels:** hardware controller-esp32 docs validation
**Procedure:** EXP-010
**Context:** Confirm the exact delivered ESP32 variant and ensure headers are installed only when needed.

#### Do
1. Photograph and measure the delivered board; count physical header positions and record PCB/module markings, including the observed N16R8 evidence.
2. Compare observations with project/manufacturer authority and list discrepancies as unknowns rather than selecting an assumed variant.
3. If AF-048 shows unsoldered headers or the adapter requires installation, solder the delivered header strips, inspect all joints, continuity-check installed pads, and photograph the result. Otherwise record the header task as not applicable.

#### Done when
- Front/rear photos, dimensions, header count, and module markings are saved without asserting an unobserved variant.
- Header applicability is recorded; every installed header pin passes visual and continuity inspection.
- Any mismatch is marked for GPIO-map review.

#### If it fails
Do not design or solder from an assumed board variant. Unpower USB before rework, repair bridges/loose joints, and resolve board discrepancies before GPIO mapping.

---

### AF-049 - SUPERSEDED

Replaced by: AF-048

(Do not export to Jira)

---

### AF-050 - Confirm ESP32-S3 flash/PSRAM and identify usable GPIOs

**Milestone:** M1
**Depends on:** AF-048
**Labels:** firmware controller-esp32 validation docs
**Context:** Establish measured memory resources and the board-specific usable GPIO inventory before assigning HUB75 signals.

#### Do
1. Run `esptool.py flash_id` over USB and a chip-info sketch; save raw flash/PSRAM reports and compare them with received module markings.
2. Inventory GPIO labels from board silkscreen, received evidence, manufacturer documentation, and the appendix; mark occupied/reserved pins with a source for each.
3. Publish usable and unknown GPIO sets without asserting fixed ranges or a total pin count not evidenced by the delivered board.

#### Done when
- Raw reports and board identity record flash/PSRAM as confirmed, conflicting, or unknown.
- The GPIO inventory identifies authoritative sources and each actual reservation.
- The usable/unknown set provides a factual basis for AF-052.

#### If it fails
Preserve contradictory reports; check cable and boot mode before repeating. Do not solder from an incomplete inventory or replace measured results with a BOM expectation.

---

### AF-051 - SUPERSEDED

Replaced by: AF-050

(Do not export to Jira)

---

### AF-052 - Validate provisional GPIO mapping against physical board silkscreen and stage adapter materials

**Milestone:** M1
**Depends on:** AF-050, AF-011
**Labels:** hardware controller-esp32 docs validation
**Context:** Produce the evidenced 14-signal allocation and ensure every adapter component is physically ready before assembly.

#### Do
1. Cross-check each target signal against the delivered board silkscreen and `hardware/schematics/PIN_LEVEL_APPENDIX.md` sections 3-4. Record a 14-row table with signal, ESP32 GPIO, HCT input/output channel, HUB75 destination, source, and status (confirmed/conflicting/unknown).
2. Verify delivered panel labels/keying and stage two SN74HCT245N ICs, perfboard, keyed 2x8 HUB75 header, two 100 nF ceramics, optional 1000 uF bulk capacitor, and suitable received low-current hookup wire.
3. Record quantities, markings, pitch, substitutions, shortages, and a photograph of staged parts. A conflict or missing suitable wire blocks assembly.

#### Done when
- Every assignment has evidence and conflicts/unknowns are explicit.
- Required parts and optional/substitute status are recorded and photographed.
- No adapter assembly begins with a mapping conflict or undocumented material shortage.

#### If it fails
Stop on mapping conflicts, quarantine mismatched materials, and obtain an authority-backed correction before AF-054.

---

### AF-053 - SUPERSEDED

Replaced by: AF-052

(Do not export to Jira)

---

### AF-054 - Build and electrically validate one ESP32 HUB75 adapter

**Milestone:** M1
**Depends on:** AF-052
**Labels:** hardware controller-esp32 power validation safety-review
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** Keep panel/controller power disconnected throughout this card. Stop for any uncertain map, bridge, damaged conductor, or failed continuity/isolation result; do not energize to diagnose it.
**Context:** Build one perfboard adapter that drives one HUB75 panel row through two SN74HCT245N 3.3 V-to-5 V buffers, using the validated AF-052 map and ADR-014 rationale.

#### Do
1. Copy the validated AF-052 14-signal table onto the build record, lay out U1, U2, ESP32 header, and keyed HUB75 header on perfboard, then mount the ICs directly or use separately obtained/recorded sockets. Record pin-1 and header-notch orientation in a clear top-side photo/diagram.
2. Wire U1/U2 pin 20 to +5 V and pin 10 to GND; tie pin 1 `DIR` to +5 V for A-to-B direction and pin 19 `/OE` to GND. Place one 100 nF ceramic directly near each IC's VCC/GND pair, wire the observed HUB75 ground positions to the ground rail, and record optional bulk-electrolytic polarity/presence separately.
3. Wire and label the U1 RGB/A/B paths exactly as validated: IO5 -> U1 A2/B18 -> R1; IO4 -> A3/B17 -> G1; IO6 -> A4/B16 -> B1; IO15 -> A5/B15 -> R2; IO7 -> A6/B14 -> G2; IO17 -> A7/B13 -> B2; IO8 -> A8/B12 -> A; IO18 -> A9/B11 -> B.
4. Wire and label the U2 paths exactly as validated: IO10 -> U2 A2/B18 -> C; IO9 -> A3/B17 -> D; IO16 -> A4/B16 -> E; IO12 -> A5/B15 -> CLK; IO11 -> A6/B14 -> LAT; IO13 -> A7/B13 -> OE. Leave unused U2 channels 8, 9, 11, and 12 unconnected and record that state.
5. Inspect every solder joint, IC/header orientation, wire routing, adjacent pads, decoupling placement, and exposed conductor under good light; correct bridges/cold joints while unpowered and update the photo/diagram if the route changes.
6. Complete the 14-signal continuity checklist end-to-end from each ESP32 header GPIO through the named HCT A/B channel to the named HUB75 destination; separately verify both IC VCC/GND paths, DIR/OE ties, and HUB75 grounds.
7. Complete the isolation checklist: no VCC-to-GND short, no unintended adjacent-pad/net bridge, and no relevant signal-to-ground short. Record every measurement/observation against the map rather than relying on a single audible beep.
8. Photograph top and bottom sides plus the labeled map, sign the unpowered pre-power checklist, and retain the continuity/isolation record before any firmware flashing or panel connection.

#### Done when
- The board has a clear photo/diagram, all 14 named signal routes match AF-052, and unused U2 channels remain unconnected.
- Every intended signal/power/control path passes the recorded continuity checklist.
- VCC/GND, adjacent-net, and relevant signal-to-ground isolation checks pass while unpowered; the signed pre-power record authorizes AF-066, not an ad hoc energization.

#### If it fails
Do not energize. Label the failed net, repair one defect at a time, and repeat the complete relevant continuity/isolation audit; return to AF-052 for any mapping contradiction.

---

### AF-055 - SUPERSEDED

Replaced by: AF-054

(Do not export to Jira)

---

### AF-059 - SUPERSEDED

Replaced by: AF-054

(Do not export to Jira)

---

### AF-061 - SUPERSEDED

Replaced by: AF-054

(Do not export to Jira)

---

### AF-065 - SUPERSEDED

Replaced by: AF-054

(Do not export to Jira)

---

### AF-066 - Flash known ESP32 HUB75 driver firmware

**Milestone:** M1
**Depends on:** AF-050, AF-054
**Labels:** firmware controller-esp32
**Context:** Load a known driver configuration using the validated map and observed panel settings.

#### Do
1. Build the selected ESP32 HUB75 driver using the AF-052 map, observed 128x64/scan settings, and documented PSRAM DMA configuration.
2. Flash the ESP32 and capture serial initialization output.
3. Record build/flash command, library/version, binary hash, serial result, and map revision.

#### Done when
- The recorded binary boots and serial initialization is retained.
- Firmware, map, panel configuration, and command evidence can be reproduced for AF-067.

#### If it fails
Preserve logs and correct one build, map, or configuration issue before reflashing; do not bypass AF-054's pre-power result.

---

### AF-067 - Single-panel physical display test via ESP32+HCT245 and full-white thermal stress observation (EXP-011)

**Milestone:** M1
**Depends on:** AF-066, AF-054, AF-021
**Labels:** hardware controller-esp32 power validation thermal-review
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize panel and controller before changing HUB75 wiring. Stop and disconnect immediately for polarity/wiring errors, smoke, abnormal smell, heat, or sound.
**Procedure:** EXP-011
**Context:** Prove the completed adapter and firmware drive one panel safely, then collect physical and thermal evidence for the ESP32 candidate.

#### Do
1. With panel, HUB75, and controller de-energized, make and verify every connection; follow the HUB75 safety runbook and do not hot-plug.
2. Energize only under EXP-011/runbook controls. Run the six standard patterns and standard defect checklist; record refresh and PSRAM metrics with method/units.
3. Hold the display for at least one hour, retaining start/end, pattern, resets, visible defects, and available power/temperature observations.
4. Run a 15-minute full-white stress observation using a named temperature instrument; record U1, U2, and ESP32 temperatures at 0, 5, 10, and 15 minutes, plus any instrument limitation or stop event.

#### Done when
- Pattern, defect, refresh, PSRAM, and one-hour evidence are retained.
- The thermal log contains the four scheduled readings or an explicit instrument limitation, and all anomalies/stop events are recorded.

#### If it fails
Remove power before rewiring. Preserve the first artifact/log, then isolate power, cable, adapter, and firmware in that order; inspect unpowered before any repeat.

---

### AF-068 - Nano-to-ESP32 wired frame transport bringup with USB-CDC fallback exploration (EXP-013)

**Milestone:** M1
**Depends on:** AF-067, AF-035
**Labels:** firmware controller-esp32 nano validation
**Safety:** CH340, HUB75
**Stop condition:** Use only TX/RX/GND for UART/CH340; disconnect panel and controller power before changing HUB75 wiring.
**Procedure:** EXP-013
**Wiring:** `hardware/schematics/PROTOTYPE_WIRING.md` §6
**Context:** Establish reliable Nano-to-ESP32 frame transport and test native USB CDC only when measured UART capacity is insufficient for the target workload.

#### Do
1. Connect the three-wire UART using native 3.3 V logic: Nano A16 TX -> ESP32 selected UART RX, Nano A17 RX <- ESP32 selected UART TX, and GND <-> GND. CH340 is optional diagnostic equipment, not the Nano-to-ESP32 bridge; keep its VCC/5V/3.3V pins disconnected from independently powered targets. Implement/test magic, length, frame-number, and CRC32 framing using known frames.
2. Run the wired transport for at least one hour, recording transfer time, dispatch-to-visible latency, frame counts/drop rate, units, method, and conditions.
3. If AF-068 measurements show UART is insufficient for the target dashboard refresh workload, test the same known frame over documented native USB CDC and compare transfer/visible latency in a reproducible table; otherwise record that the fallback trigger was not met.

#### Done when
- Valid frame records and one-hour measurements identify endpoints, units, method, and drop/reset observations.
- USB CDC is either compared under its explicit trigger or documented as not triggered; no bandwidth conclusion is inferred without measurements.

#### If it fails
Power down before rewiring and preserve the first bad packet/log. Isolate framing, endpoint, transport, and display behavior separately; do not backfeed a target from a USB-UART adapter.

---

### AF-070 - SUPERSEDED

Replaced by: AF-068

(Do not export to Jira)

---

### AF-069 - End-to-end arbitrary user text rendering via ESP32 pipeline and candidate evaluation synthesis

**Milestone:** M1
**Depends on:** AF-068, AF-030, AF-035
**Labels:** validation controller-esp32 nano critical-path docs
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before changing HUB75 wiring or ribbon cables.
**Procedure:** EXP-011, EXP-013
**Context:** Prove arbitrary Nano text reaches panel #1 via ESP32 and produce the cited candidate record used by ADR-016.

#### Do
1. Type arbitrary Nano text and retain exact input, rendered frame, UART/transport record, and panel evidence; verify accepted characters/layout and hold for 10 minutes.
2. Index EXP-011/013 patterns, defects, thermal/refresh/PSRAM data, firmware hashes, packet logs, and photos. Check every candidate claim against source evidence and mark missing evidence explicitly.
3. If primary firmware recorded a defect requiring comparison, reproduce it and test WLED-MM or another documented alternative with identical inputs; retain the trigger, restoration path for boot/display failure, and a five-dimension comparison. Otherwise record that alternative-firmware evaluation was not triggered.
4. Fill the preliminary 13-criterion EXP-014/ADR-016 matrix with pass/fail/unknown and citations, distinguish measurement from judgment, commit the reviewed evidence/candidate log, and record its hash.

#### Done when
- Exact accepted text is visible and 10-minute evidence proves Nano -> renderer -> ESP32 transport -> panel.
- Every ESP32 candidate claim and matrix value has a citation or is explicitly unknown; any alternative-firmware conclusion preserves its trigger and comparison evidence.
- The evidence index and candidate pass log are committed with their hash.

#### If it fails
Stop and preserve artifacts; replay AF-035 and isolate the first divergent stage. Restore known-good firmware after boot/output failure, and never rewrite missing evidence or an unsupported score as a pass.

---

### AF-071 - SUPERSEDED

Replaced by: AF-069

(Do not export to Jira)

---

### AF-072 - SUPERSEDED

Replaced by: AF-069

(Do not export to Jira)

---

### AF-073 - SUPERSEDED

Replaced by: AF-069

(Do not export to Jira)

---

### AF-074 - SUPERSEDED

Replaced by: AF-067

(Do not export to Jira)

---

### AF-075 - SUPERSEDED

Replaced by: AF-069

(Do not export to Jira)
