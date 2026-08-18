# AI Frame — Experiments

This document tracks the experiments that validate the prototype architecture before a final design is committed.

**Central question:** How should the LicheeRV Nano-W transfer dynamically rendered content to the HUB75 display in a way that is reliable, inexpensive, programmable, and practical for V1? Multiple controller paths are tested before committing.

Experiments produce evidence, not decisions. When an experiment changes the intended design, record the resulting decision in [DECISIONS.md](DECISIONS.md).

## Principles

Experiments are small, incremental, reproducible, and judged by measurement rather than appearance. Change one major variable at a time; validate on a single panel before scaling to six. For each run record hardware, firmware/software versions, wiring, configuration, measurements, observations, and a conclusion.

## Status Values

```text
PLANNED | READY | IN PROGRESS | PASSED | FAILED | BLOCKED | SUPERSEDED
```

## Experiment Index

| ID       | Experiment                              | Status   | Depends On |
| -------- | --------------------------------------- | -------- | ---------- |
| EXP-001  | Hardware inventory and identification   | PLANNED  | —          |
| EXP-002  | PSU no-load bring-up                    | PLANNED  | —          |
| EXP-003  | Single-panel power test                 | PLANNED  | —          |
| EXP-004  | WF4 → one panel                         | PLANNED  | —          |
| EXP-005  | WF4 → 256×64 row                        | PLANNED  | EXP-004    |
| EXP-006  | WF4 → full 256×192 display              | PLANNED  | EXP-005    |
| EXP-007  | Nano → WF4 programmatic control         | PLANNED  | —          |
| EXP-008  | WF2 stock firmware                      | PLANNED  | —          |
| EXP-009  | WF2 alternative/open firmware           | PLANNED  | EXP-008    |
| EXP-010  | ESP32-S3 board identification           | PLANNED  | —          |
| EXP-011  | ESP32-S3 → HCT245 → one panel           | PLANNED  | —          |
| EXP-012  | ESP32-S3 → 256×64 row                   | PLANNED  | EXP-011    |
| EXP-013  | Nano → ESP32 wired frame transport      | PLANNED  | —          |
| EXP-014  | Controller architecture comparison      | PLANNED  | —          |
| EXP-015  | PSU loaded thermal test                 | PLANNED  | EXP-006    |
| EXP-016  | Full-system unattended reboot           | PLANNED  | —          |

---

## Shared Test References

### Standard Test Pattern Suite

Run these, in order, to characterize any panel or controller:

1. Solid fills — red, green, blue (channel isolation), then white and black (full/zero load)
2. Checkerboard — pixel-level addressing
3. Horizontal and vertical lines (vertical every 16 px) — row/column mapping
4. Linear gradient — continuous addressing and color steps
5. Moving line or text — refresh stability and latency
6. Coordinate grid with panel-boundary and region labels — multi-panel seam/order checks

### Standard Defect Checklist

When viewing any pattern, watch for:

- Wrong color order or washed/incorrect channels
- Repeated, shifted, or missing rows
- Duplicated half or reversed panel order
- Ghosting and severe flicker
- Missing pixels or stuck rows
- Refresh instability or controller resets

---

## EXP-001 — Hardware Inventory and Identification

**Goal:** Confirm the delivered hardware matches what was ordered and record exact revisions.

**Status:** PLANNED · **Depends-On:** —

**Hardware:** HD-WF4, HD-WF2, ESP32-S3 N16R8, LicheeRV Nano-W, P2 HUB75E panel, A-200-5 PSU, HUB75 ribbon cables.

### Procedure

1. Photograph front and back of every controller board, and of one LED panel.
2. Record all visible PCB revision markings and major IC markings.
3. Confirm HUB75 IN/OUT labels, panel power polarity, and PSU terminal labels.
4. Record ESP32 pin labels and whether the Nano-W includes an external Wi-Fi antenna/connector.
5. Save photos under `hardware/photos/`.

### Success Criteria

- Exact board revisions documented; no obvious mismatch with ordered components.
- Panel polarity and HUB75 orientation known.
- ESP32 board identifiable enough for later PCB design.

---

## EXP-002 — PSU No-Load Bring-Up

**Goal:** Verify the A-200-5 provides a stable, correctly polarized 5 V output.

**Status:** PLANNED · **Depends-On:** —

**Hardware:** A-200-5 (5 V / 40 A), fused/switched C14 inlet, T2A fuse, AC wiring, multimeter.

> [!WARNING]
> Mains voltage. Before energizing: insulate or enclose all mains terminals, connect protective earth, leave no touchable conductor, and verify L/N/PE. No breadboard or Dupont connections on mains wiring.

### Procedure

1. Verify the C14 inlet fuse is installed.
2. Verify L → PSU L, N → PSU N, PE → PSU FG/earth.
3. Continuity-check with power disconnected; inspect for stray wire strands.
4. Energize with no DC load; measure and record output voltage.
5. Leave energized ~10 min; check for heat, smell, buzzing, or unstable voltage.

### Success Criteria

- Correct polarity; ~5.0 V; stable; no abnormal behavior.

### Measurements

```text
No-load voltage:
After 10 min:
Ambient temperature:
Observations:
```

---

## EXP-003 — Single-Panel Power Test

**Goal:** Verify one P2 panel powers safely and stably from the PSU and harness.

**Status:** PLANNED · **Depends-On:** —

**Hardware:** One P2 128×64 panel, A-200-5 PSU, LED panel power harness, multimeter.

### Procedure

1. Disconnect AC; confirm panel 5 V and GND polarity.
2. Connect one panel to the PSU; recheck polarity with the multimeter.
3. Apply power; measure voltage at PSU terminals and at the panel power connector.
4. Leave powered 10–15 min; check connector, wire, and panel temperature, plus voltage drop.

### Success Criteria

- Correct polarity; ~5 V at the panel; no significant heating, smell, noise, or voltage drop.

---

## EXP-004 — HD-WF4 → One Panel

**Goal:** Confirm the HD-WF4 correctly drives one P2 128×64 HUB75E 1/32-scan panel.

**Status:** PLANNED · **Depends-On:** —

**Hardware:** HD-WF4, one P2 panel, HUB75 ribbon cable, PSU, panel power harness.

**Hypothesis:** Stock WF4 firmware supports the panel's HUB75E / 1/32-scan configuration.

### Procedure

1. Power down. Connect WF4 output X1 → panel HUB75 IN.
2. Power the panel from the PSU; power the WF4 as required.
3. In Huidu software, set 128 × 64, the correct scan type, and correct color order.
4. Run the Standard Test Pattern Suite; check against the Standard Defect Checklist.

### Success Criteria

- Full 128×64 panel addressed; correct row mapping and color channels; stable with no objectionable flicker.

---

## EXP-005 — HD-WF4 → 256×64 Row

**Goal:** Confirm the WF4 drives two chained panels as one 256×64 display row.

**Status:** PLANNED · **Depends-On:** EXP-004

**Hardware:** HD-WF4, two P2 panels, two HUB75 cables, PSU, panel power.

### Procedure

1. Chain the second panel to the first panel's HUB75 OUT.
2. Power both panels in parallel (see [PROJECT.md](PROJECT.md) Power Subsystem).
3. Configure logical dimensions as 256 × 64.
4. Display a continuous horizontal gradient, panel-boundary-crossing text, vertical lines every 16 px, and numbered regions.
5. Verify left/right ordering; run ≥30 minutes.

### Success Criteria

- Correct 256×64 mapping; no duplicated half or panel-order reversal; stable refresh with no controller-caused seam.

---

## EXP-006 — HD-WF4 → Full 256×192 Display

**Goal:** Confirm one WF4 drives the complete six-panel 2×3 display via three outputs.

**Status:** PLANNED · **Depends-On:** EXP-005

**Topology:**

```text
WF4 X1 ──► Top-L ──► Top-R
WF4 X2 ──► Mid-L ──► Mid-R
WF4 X3 ──► Bot-L ──► Bot-R
WF4 X4 ──► unused
```

**Hardware:** HD-WF4, six P2 panels, HUB75 cables, PSU, parallel power harness.

### Procedure

1. Wire the three 256×64 rows; power all six panels using parallel distribution.
2. Configure the controller for the intended physical layout.
3. Display a 256×192 test image: coordinate grid, panel boundaries, row and corner labels, RGB patches.
4. Verify row order, horizontal ordering, color order, and no repeated or missing regions.
5. Test several brightness levels; run ≥1 hour.

### Success Criteria

- Complete 256×192 image correct; physical layout matches the logical framebuffer; no severe flicker, refresh instability, controller reset, or voltage instability.

---

## EXP-007 — Nano → WF4 Programmatic Control

**Goal:** Confirm the Nano can programmatically update the WF4 with dynamically rendered content.

**Status:** PLANNED · **Depends-On:** —

**Desired architecture:** Python app → 256×192 frame → wired/local transport → HD-WF4 → display.

**Key questions:** Does the WF4 expose a useful wired control protocol? Is USB usable for dynamic content, or only offline config? Is the local Wi-Fi/TCP path reliable enough? Can updates keep up with clock, weather, calendar, album artwork, and simple animation?

### Procedure

- **Phase A — Stock tools:** Use Huidu software to send known content; map which interfaces handle configuration vs. file transfer vs. live updates.
- **Phase B — Protocol inspection:** Test documented/reverse-engineered Huidu protocol libraries; send one static image from a PC, then from the Nano; send sequential numbered images and measure transfer time, update latency, and maximum practical update frequency.
- **Phase C — Python integration:** Render 256×192 with Pillow; transfer from Python; automate repeated updates; run ≥1 hour.

### Success Criteria

- **Preferred:** programmatic Nano updates with no manual vendor software during operation; acceptable latency; stable; protocol implementable or wrappable in Python.
- **Strong preference:** wired communication.
- **Acceptable fallback:** local Wi-Fi/network protocol, if reliable and isolated enough.
- **Failure:** reject WF4 if updates require awkward manual workflows or cannot reach acceptable behavior.

---

## EXP-008 — HD-WF2 Stock Firmware

**Goal:** Characterize what the WF2 can do with the actual panels under its stock firmware.

**Status:** PLANNED · **Depends-On:** —

**Note:** Not the preferred controller; a cheap comparison point and a platform with more community reverse-engineering.

### Procedure

1. Identify the exact PCB revision; do not flash alternative firmware yet.
2. Connect one panel; repeat EXP-004's basic tests on both HUB75 outputs.
3. Determine the maximum practical stock layout and available communication methods.

### Success Criteria

Informational: record supported panel behavior, output behavior, scan compatibility, controller limits, and interface options.

---

## EXP-009 — HD-WF2 Alternative/Open Firmware

**Goal:** Determine whether existing community firmware makes the WF2 substantially more useful than stock.

**Status:** PLANNED · **Depends-On:** EXP-008

**Candidate software:** WLED-MM WF2 builds; ESP32-HUB75-MatrixPanel-DMA WF1/WF2 work; other open firmware found during research.

**Preconditions:** exact WF2 revision confirmed; recovery/reflash procedure understood; original firmware restorable (or loss acceptable).

### Procedure

1. Back up configuration/firmware where possible.
2. Select the best-supported existing firmware; flash a known existing image/build only (no custom firmware first).
3. Test one panel; measure stable resolution, refresh behavior, Wi-Fi, and serial/network control.
4. Test whether live frame input is practical.

### Success Criteria

Alternative firmware is useful only if it materially improves programmability, live updates, open protocol support, or reliability without substantial custom firmware maintenance.

---

## EXP-010 — ESP32-S3 Board Identification

**Goal:** Confirm the delivered generic N16R8 board matches the expected physical and electrical layout.

**Status:** PLANNED · **Depends-On:** —

### Measurements

```text
Board length / width:
Header spacing / pin pitch / pins per side:
USB connector location:
BOOT / RESET button:
Antenna location:
Module marking:
Flash / PSRAM configuration:
```

### Procedure

1. Photograph both sides; record module markings.
2. Compare pin labels with known ESP32-S3 DevKit layouts.
3. Identify GPIOs unavailable because of flash, octal PSRAM, USB, or other board functions.
4. Verify the N16R8 configuration in software; save for PCB design.

### Success Criteria

- Exact board geometry known; GPIO availability understood; board suitable for the planned adapter.

---

## EXP-011 — ESP32-S3 → HCT245 → One Panel

**Goal:** Confirm the ESP32-S3 N16R8 reliably refreshes one P2 HUB75E panel through HCT buffering.

**Status:** PLANNED · **Depends-On:** —

**Hardware:** ESP32-S3 N16R8, 2 × SN74HCT245N, 2 × 100 nF, HUB75E 2×8 header, perfboard/temporary adapter, one panel, PSU.

**Electrical rules:**

- HCT245 powered from 5 V; ESP32 and panel grounds common.
- Panel power comes directly from the PSU; panel current does not pass through the ESP32.
- One 100 nF capacitor close to each HCT245; HUB75 ribbon not hot-plugged.

**Firmware:** start with an existing library (ESP32-HUB75-MatrixPanel-DMA, or WLED/WLED-MM); avoid custom low-level firmware.

### Procedure

1. Build the buffered interface and run the Standard Test Pattern Suite.
2. Record refresh rate (if reported), color depth, memory/PSRAM usage, stability, controller temperature, and visible flicker.

### Success Criteria

- One 128×64 1/32-scan panel works reliably; correct RGB and row mapping; no severe ghosting; stable ≥1 hour.

---

## EXP-012 — ESP32-S3 → 256×64 Row

**Goal:** Confirm one ESP32 reliably refreshes two modules as a 256×64 row (the per-controller target for a three-ESP32 design).

**Status:** PLANNED · **Depends-On:** EXP-011

**Hardware:** ESP32-S3 N16R8, HCT245 buffer, two P2 panels.

### Procedure

1. Chain a second panel; power both in parallel.
2. Configure logical dimensions as 256×64; run static images, scrolling content, and changing full-screen colors.
3. Test brightness, color-depth, and buffer configurations; measure reported refresh rate; run ≥1 hour.

### Success Criteria

- Stable 256×64 output; acceptable refresh; no visible flicker; no PSRAM/DMA failures or regular crashes/resets.

---

## EXP-013 — Nano → ESP32 Wired Frame Transport

**Goal:** Find the simplest reliable wired method for transferring rendered frames from the Nano to the ESP32.

**Status:** PLANNED · **Depends-On:** —

### Candidate Transports

- **UART** — simple, widely supported, easy to debug; bandwidth-limited. One 256×64 RGB frame is `256 × 64 × 3 = 49,152 bytes ≈ 48 KiB`; at ~1.5 Mbps with framing, full-frame rate is limited but acceptable for a mostly-static dashboard.
- **Native USB** — much higher bandwidth and direct wired; unknowns are firmware support, complexity, and robustness.
- **Other local interfaces** — only if UART/USB prove inadequate.

### Procedure

1. Start with a small payload; add framing: magic/header, payload length, frame number, checksum (if useful).
2. Send one complete 256×64 RGB frame; verify pixel correctness.
3. Send sequential frames; measure transfer time, end-to-end latency, and dropped/corrupt frames.
4. Run ≥1 hour; repeat at increasing transport speeds.

### Success Criteria

For dashboard use: reliable uncorrupted transfer, acceptable update latency, simple to maintain, robust reconnect. Smooth video is not a V1 requirement.

---

## EXP-014 — Controller Architecture Comparison

**Goal:** Select the V1 display-controller architecture.

**Status:** PLANNED · **Depends-On:** — (evidence from EXP-004–007 and EXP-011–013 feeds [ADR-016](DECISIONS.md))

### Candidates

- **A — Nano + HD-WF4:** Nano → WF4 → rows 1/2/3.
- **B — Nano + three ESP32:** Nano → three ESP32 controllers, one per row.

WF2 is primarily a reference path unless testing produces an unexpected advantage.

### Scoring Matrix

| Criterion                | WF4 | ESP32 |
| ------------------------ | --: | ----: |
| Hardware cost            | TBD | TBD   |
| Wiring complexity        | TBD | TBD   |
| Software complexity      | TBD | TBD   |
| Open protocol            | TBD | TBD   |
| Python integration       | TBD | TBD   |
| Wired transport          | TBD | TBD   |
| Refresh quality          | TBD | TBD   |
| Reliability              | TBD | TBD   |
| Boot/recovery            | TBD | TBD   |
| Maintainability          | TBD | TBD   |
| Custom firmware required | TBD | TBD   |
| Mechanical size          | TBD | TBD   |
| Future flexibility       | TBD | TBD   |

### Decision Rule

Prefer the simplest architecture that satisfies V1 requirements. Do not choose the more open or elegant option if it adds substantial maintenance or firmware work without product benefit. Record the resulting decision in [DECISIONS.md](DECISIONS.md).

---

## EXP-015 — Loaded PSU and Thermal Test

**Goal:** Verify the 200 W PSU runs the real display load without unacceptable voltage sag or heating.

**Status:** PLANNED · **Depends-On:** EXP-006

**Preconditions:** controller architecture functioning; all six panels working; safe enclosure-free bench setup.

### Procedure

Test at 25%, 50%, 75%, and 100% brightness. At each level record PSU output voltage, voltage at the farthest panel, PSU/wire/connector temperature, controller stability, and visible display behavior.

Use representative content: mostly black, normal dashboard, full white (stress), and full RGB. Full white is a stress test, not the expected normal use.

### Success Criteria

- Stable voltage; no dangerous conductor/connector heating; no PSU shutdown or controller resets; acceptable PSU thermal behavior. If necessary, establish a software brightness ceiling below 100%.

---

## EXP-016 — Full-System Unattended Reboot

**Goal:** Confirm the assembled system behaves like an appliance after power or network loss.

**Status:** PLANNED · **Depends-On:** —

### Procedure

- **Power recovery:** start the system → confirm dashboard → disconnect mains 30 s → restore → record Nano boot time, controller boot, time to first useful image, and time to network data. Repeat ≥5 times.
- **Wi-Fi recovery:** disable the access point for several minutes → restore → verify automatic reconnection.
- **API failure:** simulate a failed API request → confirm the display app stays alive and cached/fallback content remains visible.

### Success Criteria

- No manual intervention; recovers after power loss; Wi-Fi reconnects automatically; application restarts automatically; transient API errors do not blank or crash the display.

---

## Future Experiments (EXP-017+)

- Enclosure thermal testing
- Maximum acceptable frame depth
- Viewing-angle testing
- Brightness calibration
- Nighttime brightness
- Ambient-light sensing
- Color calibration
- Wi-Fi signal strength inside the enclosure
- Long-duration stability testing
- OTA/update strategy
- Watchdog/recovery behavior
- API caching
- Spotify artwork rendering performance
- Calendar layout experiments
- Panel-to-panel visual uniformity
- Fanless thermal limits
- Power consumption during typical usage

---

## Experiment Record Template

Use this template when adding future experiments:

```markdown
## EXP-XXX — Experiment Name

**Goal:** What specific unknown are we trying to answer?

**Status:** PLANNED · **Depends-On:** —

**Hardware:** — / **Software / Firmware:** —

**Hypothesis:** What do we expect?

**Preconditions:** —

### Procedure

1. —

### Measurements

| Measurement | Result |
| ----------- | -----: |
| —           | —      |

### Success Criteria

- —

### Result

TBD

### Conclusion

TBD

### Follow-Up

TBD
```