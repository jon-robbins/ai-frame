# Phase 04 — ESP32 Single-Panel Bringup & Level Shifter (M1)

Tasks are maintained as a compact, dependency-driven M1 phase catalog. Hardware facts must be verified against received evidence and the referenced wiring appendix.

---

### AF-048 — Identify ESP32-S3 DevKitC-1 N16R8 board geometry and markings (EXP-010)

**Milestone:** M1
**Depends on:** AF-024
**Labels:** hardware controller-esp32 docs validation
**Procedure:** EXP-010
**Context:** Measure board dimensions; count physical header pins (**2× 22-pin / 44 pins total** or 2× 20/24-pin); confirm N16R8 module markings; photograph directly into hardware/photos/.

#### Do
1. Photograph the delivered board, measure its dimensions, count each physical header position, and record module/PCB markings for EXP-010.
2. Compare the observations with project and manufacturer evidence; list discrepancies as unknowns.
3. Save the board photos and an identification table.

#### Done when
- Front/rear photos and the observed PCB revision are saved.
- The actual header count and module markings are recorded without selecting an unobserved board variant.
- Any mismatch with the expected map is marked for wiring review.

#### If it fails
Do not design from an assumed board variant. Preserve the photos and measurements, identify the exact received variant, and defer the pin map until discrepancies are resolved.

### AF-049 — Solder male pin headers to ESP32-S3 dev board if unsoldered

**Milestone:** M1
**Depends on:** AF-048
**Labels:** hardware controller-esp32 conditional
**Applies if:** AF-048 inspection shows the delivered ESP32 headers are unsoldered or the adapter build requires header installation.
**Context:** (Conditional) Solder 2.54mm male header strips onto ESP32-S3 board; perform visual inspection and continuity check across all pins.

#### Do
1. Verify AF-048 shows unsoldered headers; otherwise record this task not applicable.
2. If required, solder the delivered header strips and continuity-check installed pads.
3. Photograph the result.

#### Done when
- Applicability and header condition are recorded.
- Every installed pin passes visual and continuity inspection.

#### If it fails
Unpower USB before rework; repair bridges or loose joints and repeat checks.

### AF-050 — Confirm ESP32-S3 N16R8 16 MB flash and 8 MB octal PSRAM in software

**Milestone:** M1
**Depends on:** AF-048, AF-049
**Labels:** firmware controller-esp32 validation
**Context:** Connect ESP32 via USB; run esptool.py flash_id and execute Arduino/ESP-IDF chip info sketch confirming 16 MB flash and 8 MB octal PSRAM.

#### Do
1. Run `esptool.py flash_id` over USB.
2. Run the chip-info sketch and save flash/PSRAM reports.
3. Compare reports with the received module marking.

#### Done when
- Raw reports and board identity are saved.
- Flash/PSRAM result is confirmed, conflicting, or unknown.

#### If it fails
Preserve contradictory reports; check cable/boot mode before repeating.

### AF-051 — Identify unavailable ESP32-S3 GPIOs

**Milestone:** M1
**Depends on:** AF-050
**Labels:** firmware controller-esp32 docs
**Context:** Determine the delivered board’s actual GPIO inventory and occupied/reserved pins from its silkscreen, received evidence, manufacturer documentation, and the appendix; record the usable set without assuming a total count or fixed ranges.

#### Do
1. Inventory GPIO labels from the delivered board and authoritative sources.
2. Mark actually reserved/occupied pins with a source for each.
3. Publish the board-specific usable/unknown set.

#### Done when
- Actual inventory and reservations are recorded.
- No total count or fixed range is asserted without evidence.

#### If it fails
Do not solder from an incomplete map; resolve unknowns through the cited authority.

### AF-052 — Validate provisional GPIO mapping against physical board silkscreen

**Milestone:** M1
**Depends on:** AF-048, AF-051
**Labels:** hardware controller-esp32 docs validation
**Context:** Cross-verify 14 target GPIOs against physical board silkscreen and PIN_LEVEL_APPENDIX.md §3/§4; generate final validated pin allocation table.

#### Do
1. Compare each proposed signal with silkscreen and appendix §§3–4.
2. Verify delivered panel labels/keying.
3. Mark all 14 assignments confirmed, conflicting, or unknown.

#### Done when
- Every assignment has evidence.
- Conflicts block assembly.

#### If it fails
Stop on conflicts; correct the map before AF-053.

### AF-053 — Gather and inspect ESP32 adapter build materials

**Milestone:** M1
**Depends on:** AF-011, AF-052
**Labels:** hardware controller-esp32 docs
**Context:** Stage 2× SN74HCT245N DIP-20 ICs, perfboard, 2×8 keyed HUB75 header, 2× 100nF ceramic caps, optional 1000µF bulk cap, and 24 AWG wire; verify quantities and pin pitch.

#### Do
1. Stage the listed ICs, perfboard, header, capacitors, and wire.
2. Record quantities, markings, pitch, and shortages.
3. Photograph the staged parts.

#### Done when
- Required quantities and markings are recorded.
- Substitutes and optional parts are explicit.

#### If it fails
Quarantine mismatches and obtain an authority-backed replacement.

### AF-054 — Stage 1: Perfboard Layout & Socket/Header Mounting

**Milestone:** M1
**Depends on:** AF-053
**Labels:** hardware controller-esp32
**Context:** Layout component positions on perfboard; mount and solder 2× DIP-20 sockets/ICs (notches aligned) and 2×8 keyed HUB75 header (notch facing outward); check pad isolation.

#### Do
1. Mark the appendix layout on perfboard.
2. Mount/solder sockets and keyed header with orientation recorded.
3. Inspect alignment and pad isolation unpowered.

#### Done when
- Layout, orientation, and isolation pass inspection.
- Stage photo is saved.

#### If it fails
Keep unpowered; rework misalignment or bridges before continuing.

### AF-055 — Stage 2: Power, Ground, Decoupling & DIR/OE Control Wiring

**Milestone:** M1
**Depends on:** AF-054
**Labels:** hardware controller-esp32 power safety-review polarity-verify
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Stop and disconnect power immediately if polarity, wiring, or an abnormal smell, heat, or sound is detected.
**Context:** Wire and solder U1/U2 VCC (pin 20) → +5V, GND (pin 10) → GND, DIR (pin 1) → +5V (A→B), /OE (pin 19) → GND; solder 2× 100nF decoupling caps directly across pin 20↔10; wire both HUB75 GND positions to GND rail; solder optional 1000µF bulk cap; verify 8 continuity paths and VCC↔GND isolation. *(Absorbs legacy AF-056, AF-057, AF-058, AF-063, AF-064).*

#### Do
1. Wire U1/U2 power, DIR/OE, decoupling, and HUB75 grounds per appendix.
2. Record optional bulk-capacitor presence separately.
3. Verify eight paths and rail isolation unpowered.

#### Done when
- Eight paths and VCC/GND isolation pass.
- Optional hardware status is recorded.

#### If it fails
Do not energize; repair one failed connection and repeat isolation.

### AF-059 — Stage 3: RGB Data & Row Address A/B Signal Wiring

**Milestone:** M1
**Depends on:** AF-055, AF-052
**Labels:** hardware controller-esp32
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before changing HUB75 wiring or ribbon cables.
**Context:** Wire and solder the 8 high-speed RGB and lower address lines: ESP32 GPIOs (IO5, IO4, IO6, IO15, IO7, IO17, IO8, IO18) → U1 A-inputs (pins 2–9) → U1 B-outputs (pins 18–11) → HUB75 header (R1, G1, B1, R2, G2, B2, A, B); perform 16 individual continuity tests. *(Absorbs legacy AF-060).*

#### Do
1. Wire the eight confirmed RGB/A/B paths from AF-052.
2. Perform 16 continuity checks unpowered.
3. Record the final map and photo.

#### Done when
- All 16 paths match AF-052.
- No unverified connector pin is used.

#### If it fails
Disconnect power, isolate the failed net, and repeat all checks.

### AF-061 — Stage 4: Row Address C/D/E & Control Timing Signal Wiring

**Milestone:** M1
**Depends on:** AF-059
**Labels:** hardware controller-esp32
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before changing HUB75 wiring or ribbon cables.
**Context:** Wire and solder the 6 row address and control timing lines: ESP32 GPIOs (IO10, IO9, IO16, IO12, IO11, IO13) → U2 A-inputs (pins 2–7) → U2 B-outputs (pins 18–13) → HUB75 header (C, D, E, CLK, LAT, OE); confirm unused pins 8, 9, 11, 12 remain unconnected; perform 12 continuity tests. *(Absorbs legacy AF-062).*

#### Do
1. Wire the six confirmed C/D/E/timing paths through U2.
2. Leave unused channels unconnected.
3. Perform 12 continuity checks unpowered.

#### Done when
- Twelve paths pass.
- Unused channels and bridge inspection are recorded.

#### If it fails
Keep unpowered; repair the first failed conductor and retest.

### AF-065 — Stage 5: Full Adapter Unpowered Continuity & Isolation Audit

**Milestone:** M1
**Depends on:** AF-061
**Labels:** hardware controller-esp32 validation safety-review
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before changing HUB75 wiring or ribbon cables.
**Context:** Perform complete unpowered electrical audit: verify 14 end-to-end signal continuity paths (ESP32 header pin → HUB75 pin), 4 IC power paths, and ≥20 cross-pin isolation tests (adjacent pins open, VCC↔GND open, signals↔GND open) before energizing.

#### Do
1. Test 14 signal and four power paths end-to-end unpowered.
2. Test documented adjacent-pin and rail isolation points.
3. Sign the pre-power checklist.

#### Done when
- Every tested path has a result.
- All isolation tests pass before power.

#### If it fails
Do not energize; label, repair, and repeat the complete audit.

### AF-066 — Flash known ESP32 HUB75 driver firmware

**Milestone:** M1
**Depends on:** AF-050, AF-065
**Labels:** firmware controller-esp32
**Context:** Flash ESP32 with ESP32-HUB75-MatrixPanel-DMA library using validated 14-GPIO mapping, 128×64 panel config, 1/32 scan, and octal PSRAM DMA framebuffer allocation; verify serial initialization output.

#### Do
1. Build with the AF-052 validated map and observed panel settings.
2. Flash and capture serial initialization.
3. Record library/version and binary hash.

#### Done when
- Flash boots the recorded binary.
- Serial result and map are saved.

#### If it fails
Preserve logs and correct one build/configuration issue before reflashing.

### AF-067 — Single panel physical display test via ESP32+HCT245 (EXP-011)

**Milestone:** M1
**Depends on:** AF-066, AF-065, AF-021, AF-022
**Labels:** hardware controller-esp32 power validation
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Stop and disconnect power immediately if polarity, wiring, or an abnormal smell, heat, or sound is detected.
**Procedure:** EXP-011
**Context:** Connect panel #1 to adapter (HUB75 unpowered, strict power-up order); execute Standard Test Pattern Suite; evaluate 10-point defect checklist; record refresh rate and PSRAM metrics; run ≥1 hour.

#### Do
1. Connect panel #1 unpowered and follow the documented power-up order.
2. Run the six patterns and defect checklist.
3. Hold at least one hour and save metrics/logs.

#### Done when
- Pattern, defect, refresh, and PSRAM observations are recorded.
- One-hour outcome has evidence.

#### If it fails
Remove power before rewiring; isolate power, cable, adapter, then firmware.

### AF-068 — Nano-to-ESP32 wired frame transport bringup (EXP-013)

**Milestone:** M1
**Depends on:** AF-067, AF-037
**Labels:** firmware controller-esp32 nano spike validation
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before changing HUB75 wiring or ribbon cables.
**Procedure:** EXP-013
**Context:** Establish 3-wire UART link (TX, RX, GND strictly following CH340 safety rule); implement packet framing (magic, length, frame#, CRC32); measure transfer time, latency, and drop rate over ≥1 hour.

#### Do
1. Connect only TX/RX/GND and record endpoints.
2. Test magic/length/frame/CRC32 on known frames.
3. Run at least one hour and measure transfer, latency, and drops.

#### Done when
- Valid frames and counts are recorded.
- Measurements include units and method.

#### If it fails
Power down before rewiring; preserve first bad packet and isolate cause.

### AF-069 — End-to-end arbitrary user text rendering via ESP32 pipeline

**Milestone:** M1
**Depends on:** AF-068, AF-030, AF-035
**Labels:** validation controller-esp32 nano critical-path
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before changing HUB75 wiring or ribbon cables.
**Procedure:** EXP-013
**Context:** Execute full E2E test with arbitrary runtime user string typed on Nano terminal; verify exact character rendering on panel #1 via ESP32 transport; perform 10-min stable hold.

#### Do
1. Type arbitrary Nano text and save input/frame/UART/panel evidence.
2. Verify characters and hold 10 minutes.
3. Confirm no vendor-software click.

#### Done when
- Exact accepted text is visible.
- Ten-minute evidence proves the full path.

#### If it fails
Stop and preserve artifacts; replay AF-035 then isolate the first divergent stage.

### AF-070 — Native USB CDC transport fallback exploration for ESP32

**Milestone:** M1
**Depends on:** AF-068
**Labels:** firmware controller-esp32 spike conditional
**Applies if:** AF-068 shows UART bandwidth is insufficient for the target dashboard refresh workload.
**Context:** (Conditional / Spike) Evaluate ESP32-S3 native USB CDC transport if UART bandwidth is insufficient; measure frame transfer time and latency over USB.

#### Do
1. Run only if AF-068 measured UART insufficiency for the target workload.
2. Test an equivalent known frame over documented USB.
3. Compare transfer and visible latency with AF-068.

#### Done when
- AF-068 contains the trigger.
- USB comparison is reproducible and conclusion is explicit.

#### If it fails
Disconnect USB on failure and return to AF-068; do not infer a bandwidth result.

### AF-071 — Collate and commit measurement tables for EXP-011 and EXP-013

**Milestone:** M1
**Depends on:** AF-069
**Labels:** docs controller-esp32 validation
**Context:** Consolidate EXP-011 and EXP-013 markdown tables, metrics, photos, and serial logs; commit evidence under docs/pm/evidence/.

#### Do
1. Index EXP-011/013 tables, photos, firmware, and packet logs.
2. Check each result against source evidence.
3. Commit the reviewed measurement record.

#### Done when
- Every claim has provenance.
- Missing evidence is marked and commit hash recorded.

#### If it fails
Return gaps to AF-067/068; never rewrite them as passes.

### AF-072 — Collate and commit ESP32 M1 subtrack evidence and candidate pass log

**Milestone:** M1
**Depends on:** AF-071
**Labels:** docs controller-esp32 validation
**Context:** Collate complete ESP32 candidate evidence; commit subtrack pass log; prepare preliminary scoring inputs for ADR-016.

#### Do
1. Build the ESP32 evidence index.
2. Record pass/fail/unknown and ADR-016 inputs.
3. Commit the candidate pass log.

#### Done when
- Every candidate result is cited.
- Scores distinguish measurement from judgment.

#### If it fails
Mark unsupported scores unknown and return the specific gap.

### AF-073 — Evaluate alternative ESP32 firmware (WLED-MM vs custom sketch)

**Milestone:** M1
**Depends on:** AF-067
**Labels:** firmware controller-esp32 spike conditional
**Applies if:** AF-066’s primary ESP32 firmware path exhibits defects that require comparison with an alternative firmware.
**Context:** (Conditional / Spike) Compare WLED-MM against custom DMA driver if MatrixPanel-DMA exhibits defects; document 5-dimension comparison.

#### Do
1. Run only if AF-066 records a primary-firmware defect requiring comparison.
2. Reproduce it, then test the alternative with identical inputs.
3. Record a five-dimension comparison.

#### Done when
- Trigger and both test results are recorded.
- Observations are separated from recommendation.

#### If it fails
Restore known-good firmware on boot/output fault and preserve binaries/logs.

### AF-074 — Execute ESP32 full-white thermal stress test

**Milestone:** M1
**Depends on:** AF-067
**Labels:** hardware controller-esp32 thermal thermal-review validation
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Stop and disconnect power immediately if polarity, wiring, or an abnormal smell, heat, or sound is detected.
**Context:** Run full-white 100% brightness stress test on ESP32 + HCT adapter for 15 minutes; log temperatures of U1, U2, and ESP32 module at 0, 5, 10, 15 min.

#### Do
1. Record starting state and instrument.
2. Run full-white for 15 minutes with readings at 0/5/10/15 minutes.
3. Stop and log any anomaly.

#### Done when
- Four readings or instrument limitation are recorded.
- Test outcome and stop events are explicit.

#### If it fails
Disconnect on anomaly; inspect unpowered before any repeat.

### AF-075 — Synthesize ESP32 controller M1 candidate evaluation for ADR-016

**Milestone:** M1
**Depends on:** AF-072, AF-074
**Labels:** docs controller-esp32 decision validation
**Context:** Aggregate all ESP32 subtask data; fill preliminary 13-criterion scoring matrix for EXP-014 / ADR-016; record candidate pass/fail determination.

#### Do
1. Fill the 13-criterion matrix from reviewed evidence.
2. Mark each criterion pass/fail/unknown with citation.
3. Record preliminary determination only.

#### Done when
- All 13 criteria have cited values or unknowns.
- No unsupported threshold is presented as measured.

#### If it fails
Return uncited criteria to their owning task and preserve the incomplete matrix.
