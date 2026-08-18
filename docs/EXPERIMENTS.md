# AI Frame — Experiments

This document tracks the experiments required to validate the prototype architecture.

The main unresolved question is:

> How should the LicheeRV Nano-W transfer dynamically rendered content to the HUB75 display hardware in a way that is reliable, inexpensive, programmable, and practical for V1?

The project is intentionally testing multiple controller paths before committing to a final architecture.

---

# 1. Experimental Principles

Experiments should be:

- Small and incremental
- Reproducible
- Measured rather than judged only by appearance
- Limited to one major unknown at a time
- Performed with one panel before scaling to all six
- Documented immediately after testing
- Used to drive architecture decisions

Do not change multiple variables at once unless necessary.

For each experiment, record:

- hardware used
- firmware/software version
- wiring
- configuration
- measured voltages
- observed refresh behavior
- failures
- photos/screenshots where useful
- final conclusion

---

# 2. Status Definitions

Use the following statuses:

```text
PLANNED
READY
IN PROGRESS
PASSED
FAILED
BLOCKED
SUPERSEDED
```

---

# 3. Experiment Index

| ID      | Experiment                            |  Status | Purpose                                 |
| ------- | ------------------------------------- | ------: | --------------------------------------- |
| EXP-001 | Hardware inventory and identification | PLANNED | Confirm exact boards and revisions      |
| EXP-002 | PSU no-load bring-up                  | PLANNED | Verify safe, stable 5 V output          |
| EXP-003 | Single-panel power test               | PLANNED | Validate panel power and polarity       |
| EXP-004 | WF4 → one panel                       | PLANNED | Confirm WF4 compatibility               |
| EXP-005 | WF4 → 256×64 row                      | PLANNED | Test horizontal chaining                |
| EXP-006 | WF4 → full 256×192 display            | PLANNED | Validate complete panel topology        |
| EXP-007 | Nano → WF4 programmatic control       | PLANNED | Test dynamic display transport          |
| EXP-008 | WF2 stock firmware                    | PLANNED | Characterize WF2 behavior               |
| EXP-009 | WF2 alternative/open firmware         | PLANNED | Evaluate hackable controller path       |
| EXP-010 | ESP32-S3 board identification         | PLANNED | Confirm dimensions, GPIOs, revision     |
| EXP-011 | ESP32-S3 → HCT245 → one panel         | PLANNED | Validate DIY HUB75 path                 |
| EXP-012 | ESP32-S3 → 256×64 row                 | PLANNED | Test realistic per-controller load      |
| EXP-013 | Nano → ESP32 wired frame transport    | PLANNED | Validate application-to-controller link |
| EXP-014 | Controller architecture comparison    | PLANNED | Select WF4 vs ESP32 path                |
| EXP-015 | PSU loaded thermal test               | PLANNED | Validate real-world power headroom      |
| EXP-016 | Full-system unattended reboot         | PLANNED | Validate appliance behavior             |

---

# 4. EXP-001 — Hardware Inventory and Identification

## Question

Did the delivered hardware match what was ordered, and what exact revisions were received?

## Purpose

Generic Chinese boards and Huidu controllers may have PCB revisions that affect:

* pin mapping
* firmware compatibility
* connector layout
* component selection
* custom PCB design

The exact hardware must be identified before flashing firmware or fabricating an adapter PCB.

## Hardware

Inspect:

* Huidu HD-WF4
* Huidu HD-WF2
* ESP32-S3 N16R8
* Sipeed LicheeRV Nano-W
* P2 HUB75E panel
* A-200-5 PSU
* HUB75 ribbon cables

## Procedure

1. Photograph front and back of every controller board.
2. Photograph one LED panel front and back.
3. Record all visible PCB revision markings.
4. Record major IC markings where readable.
5. Confirm HUB75 IN and OUT connector labels.
6. Confirm panel power polarity.
7. Confirm PSU terminal labels.
8. Record ESP32 pin labels.
9. Record whether the Nano-W includes an external Wi-Fi antenna or antenna connector.
10. Save photographs under:

```text
hardware/photos/
```

## Success Criteria

* Exact board revisions documented
* No obvious mismatch with ordered components
* Panel polarity known
* HUB75 orientation known
* ESP32 board identifiable enough for later PCB design

## Result

TBD

## Conclusion

TBD

---

# 5. EXP-002 — PSU No-Load Bring-Up

## Question

Does the A-200-5 provide a stable and correctly polarized 5 V output?

## Hardware

* A-200-5 5 V / 40 A PSU
* fused/switched C14 inlet
* T2A fuse
* AC wiring
* multimeter

## Safety

This experiment involves mains voltage.

Before energizing:

* all mains terminals must be insulated or enclosed
* protective earth must be connected
* no exposed conductor should be touchable
* verify L, N, and PE before power is applied

Do not use temporary breadboard or Dupont connections on mains wiring.

## Procedure

1. Verify the C14 inlet fuse is installed.
2. Verify:

   * L → PSU L
   * N → PSU N
   * PE → PSU FG/earth
3. Perform continuity checks with power disconnected.
4. Inspect for stray wire strands.
5. Energize the PSU with no DC load.
6. Measure DC output voltage.
7. Record voltage.
8. Leave energized for approximately 10 minutes.
9. Check for:

   * abnormal heat
   * smell
   * buzzing
   * unstable voltage

## Success Criteria

* Output polarity correct
* Voltage approximately 5.0 V
* No abnormal behavior
* Voltage remains stable

## Measurements

```text
No-load voltage:
After 10 min:
Ambient temperature:
Observations:
```

## Result

TBD

## Conclusion

TBD

---

# 6. EXP-003 — Single-Panel Power Test

## Question

Can one P2 panel be powered safely and stably from the selected PSU and harness?

## Hardware

* one P2 128×64 panel
* A-200-5 PSU
* LED panel power harness
* multimeter

## Procedure

1. Disconnect AC power.
2. Confirm panel 5 V and GND polarity.
3. Connect one panel to the PSU.
4. Recheck polarity using the multimeter.
5. Apply power.
6. Measure voltage at:

   * PSU terminals
   * panel power connector
7. Leave powered for 10–15 minutes.
8. Check:

   * connector temperature
   * wire temperature
   * panel temperature
   * voltage drop

The panel may not display meaningful content without a controller.

## Success Criteria

* Correct polarity
* Approximately 5 V at the panel
* No significant connector heating
* No abnormal smell or noise
* No major voltage drop

## Result

TBD

## Conclusion

TBD

---

# 7. EXP-004 — HD-WF4 → One Panel

## Question

Can the Huidu HD-WF4 correctly drive one of the actual P2 128×64 HUB75E 1/32-scan panels?

## Hardware

* Huidu HD-WF4
* one P2 panel
* HUB75 ribbon cable
* PSU
* panel power harness

## Hypothesis

The stock WF4 firmware supports the panel's HUB75E / 1/32-scan configuration.

## Procedure

1. Power down everything.
2. Connect:

   * WF4 output X1 → panel HUB75 IN
3. Power the panel independently from the PSU.
4. Power the WF4 as required.
5. Configure the panel using the appropriate Huidu software.
6. Set:

   * 128 × 64
   * appropriate scan type
   * correct color order
7. Display test patterns:

   * full red
   * full green
   * full blue
   * white
   * black
   * checkerboard
   * horizontal lines
   * vertical lines
8. Observe for:

   * incorrect colors
   * repeated rows
   * shifted rows
   * ghosting
   * flicker
   * missing pixels

## Success Criteria

* Entire 128×64 panel addressed
* Correct row mapping
* Correct color channels
* Stable image
* No objectionable flicker

## Result

TBD

## Conclusion

TBD

---

# 8. EXP-005 — HD-WF4 → 256×64 Row

## Question

Can the WF4 drive two 128×64 panels chained horizontally as one 256×64 display row?

## Hardware

* HD-WF4
* two P2 panels
* two HUB75 cables
* PSU
* panel power

## Wiring

```text
WF4 X1
   │
   ▼
Panel A IN
Panel A OUT
   │
   ▼
Panel B IN
```

Power remains parallel:

```text
PSU ──► Panel A
 │
 └────► Panel B
```

## Procedure

1. Confirm EXP-004 has passed.
2. Connect the second panel to the first panel's HUB75 OUT.
3. Power both panels separately.
4. Configure logical dimensions as 256 × 64.
5. Display:

   * one continuous horizontal gradient
   * panel-boundary crossing text
   * vertical lines every 16 pixels
   * numbered regions across the full width
6. Verify left/right ordering.
7. Run continuously for at least 30 minutes.

## Success Criteria

* Correct 256×64 mapping
* No duplicated half
* No panel-order reversal
* Stable refresh
* No visible seam caused by controller mapping

## Result

TBD

## Conclusion

TBD

---

# 9. EXP-006 — HD-WF4 → Full 256×192 Display

## Question

Can one HD-WF4 drive the complete six-panel 2×3 display using three independent outputs?

## Proposed Topology

```text
WF4 X1 ──► Top-L ──► Top-R

WF4 X2 ──► Mid-L ──► Mid-R

WF4 X3 ──► Bot-L ──► Bot-R

WF4 X4 ──► unused
```

## Procedure

1. Confirm EXP-005 has passed.
2. Wire the three 256×64 rows.
3. Power all six panels using parallel power distribution.
4. Configure the controller for the intended physical layout.
5. Display a 256×192 test image containing:

   * coordinate grid
   * panel boundaries
   * row labels
   * corner labels
   * RGB test patches
6. Verify:

   * row order
   * horizontal ordering
   * color order
   * no repeated regions
   * no missing rows
7. Test several brightness levels.
8. Run continuously for at least one hour.

## Success Criteria

* Complete 256×192 image displays correctly
* Physical layout matches logical framebuffer
* No severe flicker
* No obvious refresh instability
* No controller reset
* No significant voltage instability

## Result

TBD

## Conclusion

TBD

---

# 10. EXP-007 — Nano → WF4 Programmatic Control

## Question

Can the LicheeRV Nano-W programmatically update the WF4 with dynamically rendered content?

This is one of the most important experiments in the project.

## Desired Architecture

```text
Python application
      │
      ▼
256×192 image/frame
      │
      ▼
wired/local transport
      │
      ▼
HD-WF4
      │
      ▼
display
```

## Questions to Answer

* Does the WF4 expose a useful wired control protocol?
* Is USB usable for arbitrary dynamic content?
* Is USB only intended for offline content/configuration?
* Can the controller be updated over Ethernet-like or serial interfaces?
* If wired control is unsuitable, can its local Wi-Fi/TCP protocol be used reliably?
* Can updates happen frequently enough for:

  * clock changes
  * weather
  * calendar
  * album artwork
  * simple animations

## Procedure

### Phase A — stock tools

1. Use Huidu software to send known content.
2. Observe available connection methods.
3. Determine which interfaces are intended for:

   * configuration
   * file transfer
   * live updates

### Phase B — protocol inspection

4. Test existing documented/reverse-engineered Huidu protocol libraries.
5. Send one static image from a normal computer.
6. Repeat from the LicheeRV Nano.
7. Send sequential numbered images.
8. Measure:

   * transfer time
   * display update latency
   * maximum practical update frequency

### Phase C — Python integration

9. Render a 256×192 image with Pillow.
10. Transfer it from Python.
11. Automate repeated updates.
12. Test for at least one hour.

## Success Criteria

Preferred:

* Nano can update the display programmatically
* no manual vendor software required during operation
* update latency acceptable for dashboard use
* connection is stable
* protocol can be implemented or wrapped in Python

Strong preference:

* wired communication

Acceptable fallback:

* local Wi-Fi/network protocol, if reliable and isolated enough

## Failure Condition

WF4 should be rejected as the final controller if dynamic application-driven updates require awkward manual workflows or cannot achieve acceptable update behavior.

## Result

TBD

## Conclusion

TBD

---

# 11. EXP-008 — HD-WF2 Stock Firmware

## Question

What can the HD-WF2 do with the actual panels using its stock firmware?

## Purpose

The WF2 is not currently the preferred final controller, but it provides a cheap comparison point and a platform with more community reverse-engineering.

## Procedure

1. Identify exact PCB revision.
2. Do not flash alternative firmware yet.
3. Connect one panel.
4. Repeat the basic tests from EXP-004.
5. Test both available HUB75 outputs.
6. Determine maximum practical stock layout.
7. Investigate available stock communication methods.

## Success Criteria

This experiment is primarily informational.

Record:

* supported panel behavior
* output behavior
* scan compatibility
* controller limits
* interface options

## Result

TBD

## Conclusion

TBD

---

# 12. EXP-009 — HD-WF2 Alternative/Open Firmware

## Question

Does existing community firmware make the WF2 substantially more useful than its stock firmware?

## Candidate Software

Potential candidates include:

* WLED-MM WF2 builds
* ESP32-HUB75-MatrixPanel-DMA WF1/WF2 work
* other existing open firmware identified during research

## Preconditions

Do not proceed until:

* exact WF2 revision is confirmed
* recovery/reflash procedure is understood
* original firmware can be restored or loss is considered acceptable

## Procedure

1. Record board revision.
2. Save any configuration or firmware backup possible.
3. Select the best-supported existing firmware.
4. Flash only a known existing image/build.
5. Do not begin by writing custom firmware.
6. Test one panel.
7. Measure:

   * stable resolution
   * refresh behavior
   * Wi-Fi behavior
   * serial/network control
8. Test whether live frame input is practical.

## Success Criteria

The alternative firmware is useful only if it materially improves:

* programmability
* live updates
* open protocol support
* or reliability

without requiring substantial custom firmware maintenance.

## Result

TBD

## Conclusion

TBD

---

# 13. EXP-010 — ESP32-S3 Board Identification

## Question

Does the delivered generic N16R8 board match the expected physical and electrical layout?

## Measurements

Record:

```text
Board length:
Board width:
Header spacing:
Pin pitch:
Pins per side:
USB connector location:
BOOT button:
RESET button:
Antenna location:
Module marking:
Flash configuration:
PSRAM configuration:
```

## Procedure

1. Photograph both sides.
2. Record module markings.
3. Compare pin labels with known ESP32-S3 DevKit layouts.
4. Identify GPIOs unavailable because of:

   * flash
   * octal PSRAM
   * USB
   * other board functions
5. Verify N16R8 configuration in software.
6. Save measurements for PCB design.

## Success Criteria

* Exact board geometry known
* GPIO availability understood
* Board is suitable for the planned adapter

## Result

TBD

## Conclusion

TBD

---

# 14. EXP-011 — ESP32-S3 → HCT245 → One Panel

## Question

Can the generic ESP32-S3 N16R8 reliably refresh one actual P2 HUB75E panel using proper HCT buffering?

## Hardware

* ESP32-S3 N16R8
* 2 × SN74HCT245N
* 2 × 100 nF capacitors
* HUB75E 2×8 header
* perfboard or temporary adapter
* one panel
* PSU

## Architecture

```text
ESP32-S3
    │
    │ 3.3 V GPIO
    ▼
2 × SN74HCT245N
    │
    │ buffered outputs
    ▼
HUB75E
    │
    ▼
128×64 panel
```

## Electrical Rules

* HCT245 powered from 5 V
* ESP32 and panel grounds common
* panel power comes directly from the PSU
* panel current does not pass through ESP32
* one 100 nF capacitor close to each HCT245
* HUB75 ribbon not hot-plugged

## Firmware

Start with an existing supported library or existing firmware.

Preferred candidates:

* ESP32-HUB75-MatrixPanel-DMA
* WLED/WLED-MM if appropriate for the exact hardware

Avoid beginning with custom low-level firmware.

## Test Patterns

Display:

* red
* green
* blue
* white
* checkerboard
* gradients
* moving line
* text

## Measurements

Record:

* refresh rate if reported
* color depth
* memory allocation
* PSRAM usage
* stability
* controller temperature
* visible flicker

## Success Criteria

* One 128×64 1/32-scan panel works reliably
* correct RGB
* correct row mapping
* no severe ghosting
* stable operation for at least one hour

## Result

TBD

## Conclusion

TBD

---

# 15. EXP-012 — ESP32-S3 → 256×64 Row

## Question

Can one N16R8 ESP32 reliably refresh two 128×64 modules as a 256×64 row?

This is the relevant target if the final design uses three ESP32 controllers.

## Wiring

```text
ESP32
  │
HCT245
  │
  ▼
Panel A
 OUT
  │
  ▼
Panel B
```

Power:

```text
PSU ──► Panel A
 │
 └────► Panel B
```

## Procedure

1. Confirm EXP-011 passes.
2. Chain a second panel.
3. Configure logical dimensions as 256×64.
4. Run:

   * static images
   * scrolling content
   * changing full-screen colors
5. Measure reported refresh rate if available.
6. Test different:

   * brightness levels
   * color-depth settings
   * buffer configurations
7. Run for at least one hour.

## Success Criteria

* Stable 256×64 output
* acceptable refresh
* no visible flicker under normal viewing
* no PSRAM/DMA failures
* no regular crashes/resets

## Result

TBD

## Conclusion

TBD

---

# 16. EXP-013 — Nano → ESP32 Wired Frame Transport

## Question

What is the simplest reliable wired method for transferring rendered frames from the Nano to the ESP32?

## Candidate Transports

### UART

Advantages:

* simple
* widely supported
* easy to debug

Potential limitation:

* bandwidth

A single 256×64 RGB frame is:

```text
256 × 64 × 3 bytes
= 49,152 bytes
≈ 48 KiB
```

At approximately 1.5 Mbps UART with normal framing, practical full-frame rate will be limited.

This may still be entirely acceptable for a mostly static dashboard.

### Native USB

Potential advantages:

* much higher bandwidth
* direct wired connection

Unknowns:

* firmware support
* implementation complexity
* robustness

### Other local interfaces

Only consider if UART/USB are inadequate.

## Test Application

Nano:

```text
Pillow
  │
  ▼
generate numbered frame
  │
  ▼
transport
```

ESP32:

```text
receive frame
  │
  ▼
framebuffer
  │
  ▼
HUB75 refresh
```

## Procedure

1. Start with a small test payload.
2. Add framing:

   * magic/header
   * payload length
   * frame number
   * checksum if useful
3. Send one complete 256×64 RGB frame.
4. Verify pixel correctness.
5. Send sequential frames.
6. Measure:

   * transfer time
   * end-to-end latency
   * dropped/corrupt frames
7. Run continuously for one hour.
8. Repeat at increasing transport speeds.

## Success Criteria

For dashboard use:

* reliable frame transfer
* no corruption
* acceptable update latency
* simple enough to maintain
* robust reconnect behavior

Smooth video is not a V1 requirement.

## Result

TBD

## Conclusion

TBD

---

# 17. EXP-014 — Controller Architecture Comparison

## Question

Which display-controller architecture should become the V1 design?

## Candidates

### A — Nano + HD-WF4

```text
Nano
 │
 ▼
WF4
 │
 ├── row 1
 ├── row 2
 └── row 3
```

### B — Nano + multiple ESP32 controllers

```text
             Nano
              │
      ┌───────┼───────┐
      ▼       ▼       ▼
    ESP32   ESP32   ESP32
      │       │       │
    row 1   row 2   row 3
```

WF2 is primarily an experimental/reference path unless testing produces an unexpected advantage.

## Comparison Criteria

Score each architecture on:

| Criterion                | WF4 | ESP32 |
| ------------------------ | --: | ----: |
| Hardware cost            | TBD |   TBD |
| Wiring complexity        | TBD |   TBD |
| Software complexity      | TBD |   TBD |
| Open protocol            | TBD |   TBD |
| Python integration       | TBD |   TBD |
| Wired transport          | TBD |   TBD |
| Refresh quality          | TBD |   TBD |
| Reliability              | TBD |   TBD |
| Boot/recovery            | TBD |   TBD |
| Maintainability          | TBD |   TBD |
| Custom firmware required | TBD |   TBD |
| Mechanical size          | TBD |   TBD |
| Future flexibility       | TBD |   TBD |

## Decision Rule

Prefer the simplest architecture that satisfies the V1 product requirements.

Do not choose the more open or theoretically elegant architecture if it creates substantially more maintenance or firmware work without meaningful product benefit.

## Result

TBD

## Decision

TBD

---

# 18. EXP-015 — Loaded PSU and Thermal Test

## Question

Can the selected 200 W PSU run the real display load without unacceptable voltage sag or heating?

## Preconditions

* controller architecture functioning
* all six panels working
* safe enclosure-free bench setup available

## Procedure

Test at several brightness levels:

```text
25%
50%
75%
100%
```

At each level record:

* PSU output voltage
* voltage at farthest panel
* PSU temperature
* wire temperature
* connector temperature
* controller stability
* visible display behavior

Use representative content including:

* mostly black
* normal dashboard
* full white
* full RGB test

Do not rely on full-white operation as the normal expected use case, but use it as a stress test.

## Success Criteria

* stable voltage
* no dangerous conductor/connector heating
* no PSU shutdown
* no controller resets
* acceptable PSU thermal behavior

If necessary, establish a software brightness ceiling below 100%.

## Result

TBD

## Conclusion

TBD

---

# 19. EXP-016 — Full-System Unattended Reboot

## Question

Does the assembled system behave like an appliance after loss of power or network connectivity?

## Procedure

### Power recovery

1. Start the complete system.
2. Confirm dashboard is running.
3. Disconnect mains.
4. Wait 30 seconds.
5. Restore mains.
6. Record:

   * Nano boot time
   * controller boot behavior
   * time until first useful image
   * time until network data updates

Repeat at least five times.

### Wi-Fi recovery

1. Start normally.
2. Disable Wi-Fi access point.
3. Leave offline for several minutes.
4. Restore access point.
5. Verify automatic reconnection.

### API failure

1. Simulate a failed API request.
2. Confirm the display application remains alive.
3. Confirm cached/fallback content remains visible where appropriate.

## Success Criteria

* no keyboard or manual intervention required
* display recovers after power loss
* Wi-Fi reconnects automatically
* application restarts automatically
* temporary API errors do not blank or crash the complete display

## Result

TBD

## Conclusion

TBD

---

# 20. Future Experiments

Experiments to add after the basic architecture is selected may include:

* enclosure thermal testing
* maximum acceptable frame depth
* viewing-angle testing
* brightness calibration
* nighttime brightness
* ambient-light sensing
* color calibration
* Wi-Fi signal strength inside the enclosure
* long-duration stability testing
* OTA/update strategy
* watchdog/recovery behavior
* API caching
* Spotify artwork rendering performance
* calendar layout experiments
* panel-to-panel visual uniformity
* fanless thermal limits
* power consumption during typical usage

---

# 21. Experiment Record Template

Use this template when adding future experiments:

````markdown
## EXP-XXX — Experiment Name

### Status

PLANNED

### Question

What specific unknown are we trying to answer?

### Hypothesis

What do we expect?

### Preconditions

- TBD

### Hardware

- TBD

### Software / Firmware

- TBD

### Wiring

```text
TBD
```

### Procedure

1. TBD
2. TBD
3. TBD

### Measurements

| Measurement | Result |
| ----------- | -----: |
| TBD         |    TBD |

### Success Criteria

* TBD

### Result

TBD

### Conclusion

TBD

### Follow-Up

TBD

````

---

# 22. Relationship to Architecture Decisions

Experiments produce evidence.

They do not automatically become architecture decisions.

Once an experiment changes the intended design, record the resulting decision separately in:

[`DECISIONS.md`](DECISIONS.md)

For example:

```text
EXP-007:
Nano → WF4 updates are reliable
        │
        ▼
ADR:
Select WF4 as V1 display controller
```

or:

```text
EXP-007:
WF4 dynamic updates are inadequate

EXP-011–013:
ESP32 path works reliably
        │
        ▼
ADR:
Select ESP32 row-controller architecture
```

This keeps experimental results separate from final project decisions.

