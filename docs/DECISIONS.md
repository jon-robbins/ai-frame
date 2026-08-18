# AI Frame — Architecture Decision Log

**Last updated:** 18 August 2026

## Decision-Making Principle

When several architectures satisfy the requirements, prefer the simplest architecture that reliably solves the product problem. Do not select a solution merely because it is more technically interesting, more open, more programmable, or more theoretically elegant. Openness and flexibility are valuable but must be weighed against reliability, firmware maintenance, integration time, cost, and debugging complexity. For V1, working hardware with a maintainable application interface matters more than architectural purity.

## Status Values

`PROPOSED | ACCEPTED | PENDING | SUPERSEDED | REJECTED | DEFERRED`

A `PENDING` decision is not part of the final architecture.

## Decision Index

| ID | Decision | Status |
|----|----------|--------|
| ADR-001 | Six P2 HUB75E panels in a 2×3 layout | ACCEPTED |
| ADR-002 | Canonical 256×192 framebuffer with transport abstraction | ACCEPTED |
| ADR-003 | Separate application compute from HUB75 refresh | ACCEPTED |
| ADR-004 | Linux + Python on the LicheeRV Nano-W | ACCEPTED |
| ADR-006 | Centralized 5 V power supply | ACCEPTED |
| ADR-007 | Parallel panel power distribution | ACCEPTED |
| ADR-008 | Fused, switched, earthed AC input | ACCEPTED |
| ADR-009 | Prefer a wired computer-to-controller link | ACCEPTED |
| ADR-010 | Test multiple controllers before selection | ACCEPTED |
| ADR-011 | HD-WF4 as primary stock-controller candidate | ACCEPTED |
| ADR-012 | HD-WF2 as experimental controller | ACCEPTED |
| ADR-013 | ESP32 evaluation and adapter PCB strategy | ACCEPTED |
| ADR-014 | HCT buffering for the ESP32→HUB75 interface | ACCEPTED |
| ADR-016 | Final display-controller architecture | PENDING |
| ADR-017 | Final Nano→controller transport | PENDING |
| ADR-021 | Avoid expensive integrated ESP32-HUB75 boards | ACCEPTED |
| ADR-022 | Avoid custom low-level firmware as a V1 dependency | ACCEPTED |
| ADR-024 | Final frame and enclosure | DEFERRED |

---

## ADR-001 — Six P2 HUB75E Panels in a 2×3 Layout

**Status:** ACCEPTED

**Context:** The first prototype needs a concrete display surface suitable for wall-mounted ambient information.

**Decision:** Use six P2 indoor RGB HUB75E modules in a 2 × 3 arrangement — logical resolution 256 × 192, physical area 512 × 384 mm, 49,152 total pixels.

**Rationale:** The panels provide high pixel density, modular construction, strong brightness, low component cost, and a display size suited to the product.

---

## ADR-002 — Canonical 256×192 Framebuffer with Transport Abstraction

**Status:** ACCEPTED

**Context:** The controller is still experimental, so renderer code must not depend on how content reaches the panels.

**Decision:** Render the whole display into a canonical 256 × 192 RGB framebuffer before controller-specific transport. The renderer talks to a display-transport interface and does not know which physical controller is installed.

**Rationale:** This lets the application think in terms of one 256 × 192 image, never three controller rows or six modules, so layout code survives controller changes.

**Consequences:** Transport code may send the full framebuffer, split it into rows, or encode it into a proprietary format without touching the renderer.

**Rejected Alternatives:** Controller-coupled rendering, where layout code depends directly on the chosen controller.

---

## ADR-003 — Separate Application Compute from HUB75 Refresh

**Status:** ACCEPTED

**Context:** Application work and LED refresh have fundamentally different execution requirements.

**Decision:** Use separate functional layers — Linux/Python for application logic, a dedicated controller for real-time HUB75 refresh.

**Rationale:** Linux/Python suits networking, APIs, rendering, state, and scheduling; HUB75 requires continuous scanning, deterministic timing, PWM, GPIO clocking, and row addressing. Combining them would increase V1 complexity.

**Rejected Alternatives:** Direct HUB75 refresh from Linux/Python GPIO — normal userspace scheduling cannot guarantee pixel-clock, latch, OE, and bitplane timing.

---

## ADR-004 — Linux + Python on the LicheeRV Nano-W

**Status:** ACCEPTED

**Context:** The product needs HTTP APIs, authentication, image manipulation, text rendering, and scheduling — easier in Linux/Python than in firmware.

**Decision:** Run Linux as the application environment and Python as the primary language, on a Sipeed LicheeRV Nano-W (Sophgo SG2002, 256 MB DDR3, microSD, Wi-Fi). Preferred stack: Python, `asyncio`, `aiohttp`, `Pillow`.

**Rationale:** The Nano provides enough general-purpose compute at substantially lower cost than a Raspberry Pi-class board.

**Consequences:** 256 MB RAM requires a lightweight stack. Avoid Chromium, Jupyter, pandas, and local large ML models as required runtime dependencies.

**Rejected Alternatives:** Raspberry Pi as the default application computer — unnecessary cost and capability for the workload; reconsider only if the Nano proves incapable of a reliable Linux/Python environment for the actual workload.

---

## ADR-006 — Centralized 5 V Power Supply

**Status:** ACCEPTED

**Context:** The display and controller electronics need a regulated 5 V rail.

**Decision:** Power the system from one centralized 5 V / 40 A / 200 W switching PSU (current prototype: A-200-5).

**Rationale:** Nominal maximum panel load is 6 × 23 W = 138 W, leaving headroom within a 200 W supply.

**Consequences:** The inexpensive PSU must still pass loaded-voltage, thermal, and extended-runtime testing before it is accepted as production hardware.

---

## ADR-007 — Parallel Panel Power Distribution

**Status:** ACCEPTED

**Context:** Six panels draw substantial current that must not flow through a single chain.

**Decision:** Power panels via multiple parallel branches from the 5 V supply. Each panel gets its own distribution branch.

**Rationale:** Parallel distribution reduces voltage drop, connector load, conductor heating, and reliance on panel PCB traces carrying downstream current. HUB75 signal chaining remains acceptable.

**Rejected Alternatives:** Power daisy-chaining (`PSU → Panel 1 → Panel 2 → Panel 3`), which concentrates current and drops voltage along the chain.

---

## ADR-008 — Fused, Switched, Earthed AC Input

**Status:** ACCEPTED

**Context:** The device is intended to become a permanent wall appliance, so bench-style AC wiring is unacceptable.

**Decision:** Route mains as: wall → IEC C13 cable → C14 fused/switched inlet → L/N/PE → PSU.

**Rationale:** This provides fuse protection, a master switch, protective earth, insulated terminals, suitable AC wire, and strain relief, with no exposed mains during normal operation.

---

## ADR-009 — Prefer a Wired Computer-to-Controller Link

**Status:** ACCEPTED

**Context:** The Nano must transfer dynamic content to the controller reliably.

**Decision:** Prefer a wired data connection between the Nano and the selected controller.

**Rationale:** A wired link gives simpler topology, lower latency, less dependence on wireless conditions, easier recovery, and fewer configuration dependencies. A local Wi-Fi/network protocol remains acceptable if experiments show it is substantially simpler and reliable enough.

---

## ADR-010 — Test Multiple Controllers Before Selection

**Status:** ACCEPTED

**Context:** Spec sheets alone cannot answer whether a controller can drive the full display reliably.

**Decision:** Do not choose the final HUB75 controller on specifications alone; physically test Huidu HD-WF4, Huidu HD-WF2, and ESP32-S3 N16R8 with an HCT245 interface.

**Rationale:** The key unknowns are correct refresh of the complete display, receiving dynamic content, clean Nano integration, reliable recovery, and V1 simplicity — all of which require physical testing.

---

## ADR-011 — HD-WF4 as Primary Stock-Controller Candidate

**Status:** ACCEPTED

**Context:** Among stock controllers, the WF4 best matches the three-row display geometry.

**Decision:** Treat the HD-WF4 as the primary stock-controller candidate, mapping `X1→top row`, `X2→middle row`, `X3→bottom row`, `X4` unused.

**Rationale:** Four HUB75 outputs represent the three 256 × 64 rows naturally, cleaner than forcing the geometry through a two-output controller.

**Consequences:** Not final until programmatic Nano→WF4 content updates are proven practical.

---

## ADR-012 — HD-WF2 as Experimental Controller

**Status:** ACCEPTED

**Context:** The WF2 has useful community work but only two HUB75 outputs.

**Decision:** Treat the HD-WF2 primarily as a test board, community-firmware platform, reverse-engineering reference, and comparison controller.

**Rationale:** Two outputs do not map naturally to three rows, so the WF2 should not become the V1 controller merely because it is more hackable.

---

## ADR-013 — ESP32 Evaluation and Adapter PCB Strategy

**Status:** ACCEPTED

**Context:** The ESP32 path is the most open but its board mechanics and refresh capability are unproven.

**Decision:** Buy and test one generic ESP32-S3 N16R8 before purchasing more. Defer the custom ESP32-HUB75 adapter PCB until the board is physically received and measured. Promote it to final hardware only after hardware validation, controller-architecture selection, measurement, and GPIO-mapping validation.

**Rationale:** Generic DevKit boards can differ mechanically (length, width, header spacing, pin count/pitch, USB position, buttons, antenna), so measuring first avoids designing around an incorrect footprint — the expensive failure would be wasted design time, not prototype PCB cost (which is near zero for a few boards).

**Consequences:** Additional ESP32s are purchased only after one reliably drives 256 × 64 with acceptable refresh and transport. Candidate PCB contents: ESP32 socket/headers, 2 × SN74HCT245N, 2 × 100 nF, HUB75E keyed connector, 5 V/GND, optional bulk capacitor, mounting holes.

**Rejected Alternatives:** Buying three ESP32 boards before validating one — premature given revision, GPIO, PSRAM/DMA, and firmware risks.

---

## ADR-014 — HCT Buffering for the ESP32→HUB75 Interface

**Status:** ACCEPTED

**Context:** The ESP32 drives 3.3 V GPIO while the robust HUB75 path uses 5 V signaling.

**Decision:** Place two SN74HCT245N buffers between the ESP32-S3 and the HUB75E connector.

**Rationale:** HCT logic at 5 V has TTL-compatible input thresholds that reliably accept 3.3 V ESP32 outputs, giving a cleaner, more robust interface than driving the panel directly.

**Rejected Alternatives:**
- **Plain 74HC245** — 3.3 V HIGH does not guarantee enough input margin when HC logic runs at 5 V.
- **TXS0108E** and **generic MOSFET bidirectional level shifters** — poor fit for fast, unidirectional HUB75 clock/data paths.

---

## ADR-016 — Final Display Controller

**Status:** PENDING

**Context:** The controller choice is deliberately deferred until experiments produce evidence.

**Decision:** Select between HD-WF4 and a multi-ESP32 arrangement after physical testing.

**Decision Criteria:** reliability, simple software integration, acceptable refresh quality, low maintenance, low unit cost, wired control where practical, existing stable firmware, reasonable openness, reliable boot/recovery, and mechanical simplicity.

**Consequences:** Required evidence comes primarily from EXP-004 through EXP-007 and EXP-011 through EXP-014.

**Rejected Alternatives:** Cascade architecture (`Nano → ESP32 → WF4 → panel`) — the ESP32 and the Huidu board solve the same controller problem; the alternatives are `Nano → WF4 → panel` or `Nano → ESP32 → panel`, never both stacked.

---

## ADR-017 — Final Nano→Controller Transport

**Status:** PENDING

**Context:** The application must send dynamically generated content without manual vendor-software intervention.

**Decision:** Select the transport (Huidu protocol, USB, UART, native ESP32 USB, or a local TCP/network protocol) based on controller experiments.

**Rationale:** Smooth video is not required — the display serves clocks, weather, calendar changes, artwork, album covers, status changes, and modest animation, so a lower frame rate is acceptable if transport is simple and reliable.

---

## ADR-021 — Avoid Expensive Integrated ESP32-HUB75 Boards

**Status:** ACCEPTED

**Context:** Integrated ESP32-HUB75 controllers are convenient but costly relative to prototype paths.

**Decision:** Do not purchase ¥130–160 integrated ESP32-HUB75 controllers (e.g. Waveshare ESP32-S3-RGB-Matrix) unless the cheaper paths fail.

**Rationale:** Prototype alternatives cost far less — generic ESP32-S3 ~¥30, HCT adapter parts very low cost, WF2 ~¥35, WF4 ~¥57 — so buying the integrated board first offers poor unit economics.

---

## ADR-022 — Avoid Custom Low-Level Firmware as a V1 Dependency

**Status:** ACCEPTED

**Context:** V1 should be built on existing firmware rather than a new embedded refresh stack.

**Decision:** Prefer existing firmware, configuration, known binaries, established libraries, and Linux/Python software over a custom embedded refresh stack.

**Rationale:** The purpose of V1 is to build the product, not to create a new LED-controller firmware project.

**Consequences:**
- Acceptable: flashing an existing `.bin`, configuring WLED/WLED-MM, using MatrixPanel-DMA examples, Arduino-style configuration, GPIO mapping, Python transport software.
- Avoid: custom ESP-IDF architecture, a custom DMA engine, maintaining a fork indefinitely, undocumented register work, extensive board reverse-engineering.

---

## ADR-024 — Final Frame and Enclosure

**Status:** DEFERRED

**Context:** Enclosure design depends on controller architecture and thermal behavior that are still unknown.

**Decision:** Do not finalize the mechanical enclosure until those inputs are known.

**Rationale:** The enclosure should be wall-mounted, relatively thin, visually unlike a monitor, mechanically rigid, serviceable, electrically safe, and adequately ventilated. The A-200-5 PSU (~199 × 110 × 50 mm) is likely to influence minimum finished depth.

**Consequences:** Required inputs: actual PSU thermals, controller selection and count, PCB dimensions, internal cable routing, and brightness/power limits.

---

## Pending Decision Flow

Pending decisions resolve through experiments: parts arrive → basic power tests → test HD-WF4 and ESP32 in parallel → compare results → decide ADR-016 → final controller → custom PCB if necessary. Current progress and next actions live in [STATUS.md](STATUS.md); procedures and results in [EXPERIMENTS.md](EXPERIMENTS.md).