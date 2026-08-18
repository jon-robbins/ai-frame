# AI Frame — Architecture Decision Log

This document records architectural decisions for AI Frame.

It exists to answer:

> What did we decide, why did we decide it, and what alternatives were rejected or deferred?

Detailed experiments belong in [`EXPERIMENTS.md`](EXPERIMENTS.md).  
Current progress belongs in [`STATUS.md`](STATUS.md).  
Exact hardware belongs in [`BOM.md`](BOM.md).

---

# 1. Decision Status

Use the following statuses:

```text
PROPOSED
ACCEPTED
PENDING
SUPERSEDED
REJECTED
DEFERRED
```

A `PENDING` decision should not be treated as part of the final architecture.

---

# 2. Decision Index

| ID      | Decision                                                           | Status   |
| ------- | ------------------------------------------------------------------ | -------- |
| ADR-001 | Use six P2 HUB75E panels in a 2×3 layout                           | ACCEPTED |
| ADR-002 | Use 256×192 as the canonical framebuffer                           | ACCEPTED |
| ADR-003 | Separate application compute from HUB75 refresh                    | ACCEPTED |
| ADR-004 | Use Linux + Python for application logic                           | ACCEPTED |
| ADR-005 | Use LicheeRV Nano-W as the prototype application computer          | ACCEPTED |
| ADR-006 | Use centralized 5 V power                                          | ACCEPTED |
| ADR-007 | Distribute panel power in parallel                                 | ACCEPTED |
| ADR-008 | Use fused, switched, earthed mains input                           | ACCEPTED |
| ADR-009 | Prefer a wired application-computer → display-controller link      | ACCEPTED |
| ADR-010 | Test multiple display-controller architectures before selection    | ACCEPTED |
| ADR-011 | Use HD-WF4 as the primary stock-controller candidate               | ACCEPTED |
| ADR-012 | Treat HD-WF2 primarily as an experimental controller               | ACCEPTED |
| ADR-013 | Test one ESP32-S3 N16R8 before buying additional controllers       | ACCEPTED |
| ADR-014 | Use HCT-class buffering for the ESP32 HUB75 interface              | ACCEPTED |
| ADR-015 | Defer custom ESP32 adapter PCB fabrication until board measurement | ACCEPTED |
| ADR-016 | Final display-controller architecture                              | PENDING  |
| ADR-017 | Final Nano → controller transport                                  | PENDING  |
| ADR-018 | Custom ESP32 adapter PCB as production hardware                    | PENDING  |
| ADR-019 | Avoid Raspberry Pi for V1                                          | ACCEPTED |
| ADR-020 | Avoid direct HUB75 refresh from Linux/Python                       | ACCEPTED |
| ADR-021 | Avoid expensive integrated ESP32-HUB75 boards for prototype        | ACCEPTED |
| ADR-022 | Avoid custom low-level firmware as a V1 dependency                 | ACCEPTED |
| ADR-023 | Use a transport abstraction between renderer and controller        | ACCEPTED |
| ADR-024 | Final frame/enclosure design                                       | DEFERRED |

---

# ADR-001 — Six P2 HUB75E Panels in a 2×3 Layout

**Status:** ACCEPTED

## Decision

Use six P2 indoor RGB HUB75E modules arranged:

```text
┌─────────┬─────────┐
│ 128×64  │ 128×64  │
├─────────┼─────────┤
│ 128×64  │ 128×64  │
├─────────┼─────────┤
│ 128×64  │ 128×64  │
└─────────┴─────────┘
```

## Resulting Display

```text
Logical resolution: 256 × 192
Physical area:      512 × 384 mm
Total pixels:       49,152
```

## Reason

The panels were already selected and provide the intended combination of:

* high pixel density,
* modular construction,
* strong brightness,
* low component cost,
* and a display size suitable for wall-mounted ambient information.

---

# ADR-002 — Canonical 256×192 Framebuffer

**Status:** ACCEPTED

## Decision

The application should render the entire display into a canonical:

```text
256 × 192 RGB framebuffer
```

before controller-specific transport occurs.

## Reason

This prevents application layout code from depending on the eventual display-controller architecture.

The application should think in terms of:

```text
256×192 image
```

rather than:

```text
three controller rows
```

or:

```text
six physical LED modules
```

## Consequence

Controller-specific transport code may:

* send the complete framebuffer,
* divide it into rows,
* encode it into a proprietary format,
* or otherwise transform it,

without changing the renderer.

---

# ADR-003 — Separate Application Compute from HUB75 Refresh

**Status:** ACCEPTED

## Decision

Use separate functional layers for:

1. application logic
2. real-time LED refresh

Architecture:

```text
Linux / Python
      │
      │ desired image
      ▼
Display controller
      │
      │ deterministic HUB75 timing
      ▼
LED panels
```

## Reason

Application tasks and HUB75 refresh have fundamentally different requirements.

Linux/Python is appropriate for:

* networking
* APIs
* rendering
* state
* scheduling

HUB75 requires:

* continuous scanning
* deterministic timing
* PWM
* GPIO clocking
* row addressing
* output-enable control

Combining these responsibilities would unnecessarily increase V1 complexity.

---

# ADR-004 — Linux + Python for Application Logic

**Status:** ACCEPTED

## Decision

Use Linux as the application operating environment and Python as the primary application language.

## Preferred Software

Likely lightweight components include:

```text
Python
asyncio
aiohttp
Pillow
```

## Reason

The product requires:

* HTTP APIs
* authentication
* image manipulation
* text rendering
* scheduling
* straightforward application development

These are substantially easier to implement and maintain in Linux/Python than in microcontroller firmware.

---

# ADR-005 — LicheeRV Nano-W as Prototype Application Computer

**Status:** ACCEPTED

## Decision

Use the Sipeed LicheeRV Nano-W for the prototype application computer.

## Relevant Specification

```text
SoC:      Sophgo SG2002
RAM:      256 MB DDR3
Storage:  microSD
Network:  Wi-Fi
OS:       Linux-capable
```

## Reason

It provides enough general-purpose computing capability for the intended application at substantially lower cost than a Raspberry Pi-class computer.

## Constraint

256 MB RAM requires a lightweight application stack.

The prototype should avoid making heavy components such as:

* Chromium
* Jupyter
* pandas
* local large ML models

part of the required runtime.

---

# ADR-006 — Centralized 5 V Power Supply

**Status:** ACCEPTED

## Decision

Power the display from one centralized:

```text
5 V / 40 A / 200 W
```

switching PSU.

Current prototype model:

```text
A-200-5
```

## Reason

Maximum nominal panel load is approximately:

```text
6 × 23 W = 138 W
```

leaving nominal headroom within a 200 W supply.

## Requirement

The inexpensive PSU must still pass:

* loaded voltage testing
* thermal testing
* extended runtime testing

before being accepted as production hardware.

---

# ADR-007 — Parallel Panel Power Distribution

**Status:** ACCEPTED

## Decision

Power LED panels using multiple parallel branches from the 5 V supply.

Preferred:

```text
              PSU
       ┌───────┼───────┐
       ▼       ▼       ▼
    Panel 1 Panel 2 Panel 3 ...
```

Do not use panel power connectors to create a long high-current chain:

```text
PSU → Panel 1 → Panel 2 → Panel 3
```

## Reason

Parallel distribution reduces:

* voltage drop
* connector load
* conductor heating
* dependence on panel PCB traces carrying downstream current

HUB75 **signal** chaining remains acceptable.

---

# ADR-008 — Fused, Switched, Earthed AC Input

**Status:** ACCEPTED

## Decision

Use:

```text
Wall
  │
IEC C13 cable
  │
C14 fused/switched inlet
  │
L / N / PE
  │
5 V PSU
```

## Requirements

* Fuse protection
* Master power switch
* Protective earth
* Insulated mains terminals
* Suitable AC wire
* Strain relief
* No exposed mains during normal operation

## Reason

The device is intended to become a permanent wall appliance, so temporary bench-style AC wiring is unacceptable.

---

# ADR-009 — Prefer Wired Computer-to-Controller Communication

**Status:** ACCEPTED

## Decision

Prefer a wired data connection between the LicheeRV Nano-W and the selected display controller.

## Preferred Architecture

```text
Internet
   │ Wi-Fi
   ▼
Nano
   │ wired
   ▼
Display controller
```

rather than using Wi-Fi for both layers unless necessary.

## Reason

A wired internal connection should provide:

* simpler topology
* lower latency
* less dependence on wireless conditions
* easier recovery
* fewer configuration dependencies

## Qualification

A local network/Wi-Fi controller protocol remains acceptable if experiments show that it is substantially simpler and reliable enough for the product.

---

# ADR-010 — Test Multiple Display Controllers Before Selection

**Status:** ACCEPTED

## Decision

Do not choose the final HUB75 controller based solely on specifications.

Test:

1. Huidu HD-WF4
2. Huidu HD-WF2
3. ESP32-S3 N16R8 + HCT245 interface

## Reason

The most important unknown is not simply whether a controller can illuminate the panels.

It is whether it can:

* correctly refresh the complete display,
* receive dynamically generated content,
* integrate cleanly with the Nano,
* recover reliably,
* and remain simple enough for V1.

Those questions require physical testing.

---

# ADR-011 — HD-WF4 as Primary Stock-Controller Candidate

**Status:** ACCEPTED

## Decision

Treat the Huidu HD-WF4 as the primary stock-controller candidate.

## Preferred Physical Mapping

```text
WF4 X1 → Top-L → Top-R

WF4 X2 → Mid-L → Mid-R

WF4 X3 → Bot-L → Bot-R

WF4 X4 → unused
```

## Reason

Four HUB75 outputs allow the three physical display rows to be represented naturally as three 256×64 chains.

This is substantially cleaner than trying to force the display geometry through a two-output controller.

## Open Issue

The controller is not final until programmatic Nano → WF4 updates are proven practical.

---

# ADR-012 — HD-WF2 as Experimental Controller

**Status:** ACCEPTED

## Decision

Treat the HD-WF2 primarily as:

* a test board,
* community-firmware platform,
* reverse-engineering reference,
* and comparison controller.

## Reason

The WF2 has useful community work around it, but two HUB75 outputs do not map naturally to the intended three-row physical display.

It should not become the V1 controller simply because it is more hackable.

---

# ADR-013 — Buy Only One ESP32-S3 Initially

**Status:** ACCEPTED

## Decision

Purchase and test one generic ESP32-S3 N16R8 controller before buying additional units.

## Reason

Potential issues include:

* board revision differences
* physical footprint differences
* GPIO availability
* PSRAM/DMA behavior
* HUB75 refresh limitations
* firmware compatibility

Buying three controllers before validating one provides little benefit.

## Trigger for Additional Purchase

Additional ESP32 controllers may be purchased if experiments demonstrate that one controller can reliably drive:

```text
256 × 64
```

with acceptable refresh and transport performance.

---

# ADR-014 — Use HCT Buffering for ESP32 → HUB75

**Status:** ACCEPTED

## Decision

Use two SN74HCT245N devices between the ESP32-S3 and HUB75E connector.

```text
ESP32 3.3 V GPIO
       │
       ▼
2 × SN74HCT245N
       │
       ▼
HUB75E
```

## Reason

HCT logic powered at 5 V provides TTL-compatible input thresholds that reliably accept 3.3 V ESP32 outputs.

The buffer also provides a cleaner and more robust signal interface than connecting the panel directly to the ESP32.

## Rejected Alternatives

### Plain 74HC245 as preferred design

Not preferred because 3.3 V HIGH does not provide as much guaranteed margin when HC logic is operated at 5 V.

### TXS0108E

Rejected for this application.

### Generic MOSFET bidirectional level shifters

Rejected.

These are intended for different signaling requirements and are poor choices for fast HUB75 clock/data paths.

---

# ADR-015 — Defer Adapter PCB Until ESP32 Is Measured

**Status:** ACCEPTED

## Decision

Do not fabricate the custom ESP32-HUB75 adapter PCB until the purchased development board is physically received and measured.

## Measurements Required

```text
board length
board width
header spacing
pin count
pin pitch
USB connector position
buttons
antenna position
```

## Reason

Generic ESP32-S3 DevKit-style boards can differ mechanically even when sold under similar descriptions.

At prototype PCB prices, fabrication is cheap.

The expensive failure would be spending design time around an incorrect mechanical footprint.

---

# ADR-016 — Final Display Controller

**Status:** PENDING

## Candidates

### Option A — HD-WF4

```text
Nano
 │
 ▼
WF4
 │
 ├── Row 1
 ├── Row 2
 └── Row 3
```

### Option B — Multiple ESP32-S3 controllers

```text
             Nano
              │
      ┌───────┼───────┐
      ▼       ▼       ▼
    ESP32   ESP32   ESP32
      │       │       │
    Row 1   Row 2   Row 3
```

## Decision Criteria

The final choice should prioritize:

1. Reliability
2. Simple software integration
3. Acceptable refresh quality
4. Low maintenance burden
5. Low unit cost
6. Wired control where practical
7. Existing stable firmware
8. Reasonable openness/documentation
9. Reliable boot/recovery
10. Mechanical simplicity

## Required Evidence

Primarily:

* EXP-004 through EXP-007
* EXP-011 through EXP-014

---

# ADR-017 — Final Nano → Controller Transport

**Status:** PENDING

## Candidates

Depending on controller:

* Huidu protocol
* USB
* UART
* native ESP32 USB
* local TCP/network protocol

## Requirement

The application must be able to send dynamically generated content without manual vendor-software intervention.

## V1 Performance Requirement

Smooth video is **not** required.

The display primarily needs to support:

* clocks
* weather
* calendar changes
* artwork
* album covers
* status changes
* modest animation

This means a lower frame rate may be entirely acceptable if transport is simple and reliable.

---

# ADR-018 — Custom ESP32 Adapter PCB as Final Hardware

**Status:** PENDING

## Current Position

A small custom PCB is likely worthwhile **if the ESP32 architecture is selected**.

Expected contents:

```text
ESP32 socket/headers
2 × SN74HCT245
2 × 100 nF
HUB75E keyed connector
5 V / GND
optional bulk capacitor
mounting holes
```

## Decision Trigger

Only proceed as a final design after:

* ESP32 hardware validation
* controller architecture selection
* exact board measurement
* GPIO mapping validation

## Prototype Fabrication

Chinese prototype services can fabricate approximately five simple boards for very low cost, so bare PCB cost is not considered a significant architectural constraint.

---

# ADR-019 — Do Not Use Raspberry Pi for V1

**Status:** ACCEPTED

## Decision

Do not use a Raspberry Pi as the default application computer.

## Reason

The product does not require:

* desktop-class graphics
* HDMI
* large RAM
* heavy local applications

The required tasks are primarily:

* Python
* networking
* API calls
* small-image rendering

A cheaper Linux SBC provides better unit economics.

## Reconsider If

The Nano proves incapable of providing a reliable Linux/Python environment for the actual application workload.

---

# ADR-020 — Do Not Refresh HUB75 Directly from Linux/Python

**Status:** ACCEPTED

## Decision

Do not make direct Linux GPIO HUB75 refresh part of the architecture.

## Reason

HUB75 requires timing-sensitive continuous scanning.

Normal Linux userspace scheduling and Python are inappropriate places to guarantee:

* pixel clock timing
* latch timing
* OE timing
* PWM bitplanes
* scan-row sequencing

A dedicated controller should own this responsibility.

---

# ADR-021 — Avoid Expensive Integrated ESP32-HUB75 Boards for Prototype

**Status:** ACCEPTED

## Decision

Do not purchase ¥130–160 integrated ESP32-S3 HUB75 controllers unless the inexpensive paths fail.

Examples considered include the Waveshare ESP32-S3-RGB-Matrix and similar integrated controllers.

## Reason

Prototype alternatives cost approximately:

```text
Generic ESP32-S3:    ~¥30
HCT adapter parts:   very low cost
WF2:                 ~¥35
WF4:                 ~¥57
```

Buying the expensive integrated controller provides poor unit economics before cheaper architectures have been tested.

---

# ADR-022 — Avoid Custom Low-Level Firmware as V1 Dependency

**Status:** ACCEPTED

## Decision

V1 should prefer:

* existing firmware
* configuration
* known binaries
* established libraries
* Linux/Python software

over writing a custom embedded refresh stack.

## Acceptable

* flashing an existing `.bin`
* configuring WLED/WLED-MM
* using MatrixPanel-DMA examples
* normal Arduino-style configuration if necessary
* setting GPIO mappings
* Python transport software

## Avoid as Requirements

* custom ESP-IDF architecture
* custom DMA engine
* maintaining a fork indefinitely
* undocumented register work
* extensive board reverse engineering

## Reason

The purpose of V1 is to build the product, not to create a new LED-controller firmware project.

---

# ADR-023 — Use a Controller-Agnostic Transport Interface in Software

**Status:** ACCEPTED

## Decision

The application renderer should not know which physical display controller is installed.

Preferred software boundary:

```text
APIs
 │
 ▼
Application state
 │
 ▼
Renderer
 │
 ▼
256×192 RGB framebuffer
 │
 ▼
Display transport interface
 │
 ├── WF4 transport
 └── ESP32 transport
```

## Reason

The controller is still experimental.

Separating transport from rendering allows:

* controller experiments,
* future hardware changes,
* testing,
* and migration

without rewriting dashboard/layout code.

---

# ADR-024 — Final Frame and Enclosure

**Status:** DEFERRED

## Decision

Do not finalize the mechanical enclosure until the controller architecture and thermal behavior are known.

## Known Requirements

The enclosure should ultimately be:

* wall-mounted
* relatively thin
* visually unlike a conventional monitor
* mechanically rigid
* serviceable
* electrically safe
* adequately ventilated

## Major Constraint

The current A-200-5 PSU is approximately:

```text
199 × 110 × 50 mm
```

and is likely to influence minimum finished depth.

## Inputs Required Before Decision

* actual PSU thermals
* controller selection
* controller count
* PCB dimensions
* internal cable routing
* brightness/power limits

---

# 3. Rejected / Avoided Approaches

The following approaches have been considered and are currently rejected for V1.

## Direct panel power daisy chaining

**Status:** REJECTED

Reason:

High current and voltage-drop concerns.

---

## Nano directly driving HUB75

**Status:** REJECTED

Reason:

Linux/Python is not the appropriate real-time refresh environment.

---

## ESP32 → WF2/WF4 → panels

**Status:** REJECTED

Reason:

The ESP32 and Huidu board solve the same controller problem.

There is no reason to insert both:

```text
Nano → ESP32 → WF4 → panel
```

The relevant alternatives are:

```text
Nano → WF4 → panel
```

or:

```text
Nano → ESP32 → panel
```

---

## Generic TXS0108E / bidirectional level-shifter boards

**Status:** REJECTED

Reason:

Poor fit for HUB75 high-speed clock/data signaling.

---

## Buying three ESP32 boards before validation

**Status:** REJECTED

Reason:

One controller must first prove:

* panel compatibility
* PSRAM/DMA behavior
* refresh quality
* transport viability

---

## Expensive integrated controller as first choice

**Status:** REJECTED

Reason:

Poor unit economics relative to inexpensive prototype paths.

---

# 4. Decision-Making Principle

When several architectures satisfy the requirements, prefer:

> The simplest architecture that reliably solves the product problem.

Do not select a solution merely because it is:

* more technically interesting,
* more open,
* more programmable,
* or more theoretically elegant.

Openness and flexibility are valuable, but they must be weighed against:

* reliability
* firmware maintenance
* integration time
* cost
* debugging complexity

For V1, working hardware with a maintainable application interface is more important than maximizing architectural purity.

---

# 5. Pending Decision Flow

The current decision process is:

```text
                Parts arrive
                     │
                     ▼
             Basic power tests
                     │
                     ▼
        ┌────────────┴─────────────┐
        │                          │
        ▼                          ▼
   Test HD-WF4                Test ESP32
        │                          │
        ▼                          ▼
Full 256×192?               Stable 256×64?
        │                          │
        ▼                          ▼
Nano integration?           Nano integration?
        │                          │
        └────────────┬─────────────┘
                     ▼
             Compare results
                     │
                     ▼
              ADR-016 decided
                     │
                     ▼
              Final controller
                     │
                     ▼
            PCB if necessary
```

The architecture should remain deliberately undecided until these experiments produce evidence.

