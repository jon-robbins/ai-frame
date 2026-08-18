# AI Frame — Project Status

**Last updated:** 18 August 2026  
**Current phase:** Prototype hardware acquisition and architecture validation

---

## 1. Current Status

The initial prototype architecture has been defined and the required test hardware has been ordered.

The project is now waiting for components to arrive before hardware bring-up begins.

The major unresolved technical question is still the connection between the Linux application computer and the HUB75 display hardware:

> What display-controller architecture provides the simplest, most reliable, inexpensive, and programmable path from a Python-rendered framebuffer to the six HUB75E panels?

Three controller approaches will be tested before the final architecture is selected.

---

## 2. Completed

### Product and architecture

- Defined the product as a wall-mounted ambient information display
- Defined the prototype display size as six P2 HUB75E panels
- Defined the final logical resolution as **256 × 192**
- Decided to separate:
  - high-level Linux/Python application logic
  - real-time HUB75 refresh
- Selected the **Sipeed LicheeRV Nano-W** as the prototype application computer
- Selected a centralized **5 V / 40 A / 200 W** power architecture
- Defined parallel power distribution to the six LED modules
- Defined a fused, switched, earthed AC input architecture

### Display-controller research

Identified three controller paths to test:

1. **Huidu HD-WF4**
   - Primary stock-controller candidate
   - Four HUB75 outputs
   - Best physical match for the three display rows

2. **Huidu HD-WF2**
   - Secondary experimental controller
   - Useful because of existing community reverse-engineering and firmware work

3. **ESP32-S3 N16R8 + buffered HUB75 adapter**
   - Open/flexible alternative
   - Uses a generic ESP32-S3 with two SN74HCT245N logic buffers

### ESP32-HUB75 research

- Confirmed that a generic ESP32-S3 does not directly provide a finished HUB75 interface
- Selected **SN74HCT245N** as the prototype signal-buffer device
- Selected two buffers to cover the required HUB75E signals
- Selected a keyed 16-pin HUB75 header
- Selected 100 nF local decoupling capacitors
- Selected optional 1000 µF bulk capacitance
- Identified existing open-source HUB75 PCB designs as references
- Identified PatternFlow as a useful ESP32-S3 N16R8 physical/pinout reference
- Identified Seengreat RGB Matrix Adapter V3.9 as a useful HCT245 buffering reference
- Determined that a custom PCB can be fabricated very cheaply in China
- Decided **not** to fabricate the ESP32 adapter PCB until the exact purchased ESP32 board is physically measured

### Purchasing

The prototype parts list has been completed and ordered.

Major ordered items include:

- Six P2 HUB75E RGB LED panels
- Sipeed LicheeRV Nano-W
- Huidu HD-WF4
- Huidu HD-WF2
- One generic ESP32-S3 N16R8 development board
- Two SN74HCT245N buffers
- HUB75 headers
- Prototype PCB/perfboard
- Decoupling and bulk capacitors
- 5 V / 40 A / 200 W PSU
- Fused/switched IEC C14 inlet
- T2A fuses
- AC mains wiring and terminals
- 18 AWG DC power wire
- LED panel power harnesses
- HUB75 ribbon cables
- CH340 USB-UART adapter
- microSD card
- Multimeter
- Ferrule crimper and ferrules
- Soldering equipment
- Heat-shrink tubing
- Jumper wire and pin headers

Exact part details are maintained in [`BOM.md`](BOM.md).

---

## 3. Current Architecture Candidates

### Candidate A — Huidu HD-WF4

Expected topology:

```text
LicheeRV Nano-W
      │
      │ TBD transport
      ▼
   HD-WF4
      │
      ├── X1 ──► Top-left ──► Top-right
      ├── X2 ──► Mid-left ──► Mid-right
      ├── X3 ──► Bot-left ──► Bot-right
      └── X4 ──► unused
```

This is currently the simplest-looking hardware architecture.

The key unknown is whether the Nano can update the WF4 programmatically at the rate and flexibility required by the product.

---

### Candidate B — Huidu HD-WF2

The WF2 will be tested primarily to understand:

* stock Huidu behavior
* community firmware
* WLED-MM compatibility
* Huidu hardware revisions
* possible wired control paths

It is not currently expected to be the easiest controller for the complete 2 × 3 panel layout.

---

### Candidate C — ESP32-S3 N16R8

Prototype topology:

```text
LicheeRV Nano-W
      │
      │ wired frame transport
      ▼
ESP32-S3 N16R8
      │
      ▼
2 × SN74HCT245N
      │
      ▼
HUB75E
      │
      ▼
128×64 or 256×64 test display
```

If this architecture is selected for the final display, the likely configuration would use one ESP32 per 256 × 64 row.

Only one ESP32 has been ordered initially.

Additional boards will not be purchased until the first controller has been successfully validated.

---

## 4. Current Open Questions

### Highest priority

* Can the HD-WF4 correctly drive the actual P2 128 × 64, 1/32-scan panels?
* Can the WF4 correctly map three 256 × 64 panel chains into the intended 256 × 192 display?
* Can the LicheeRV Nano-W dynamically control the WF4 from software?
* Is there a practical **wired** Nano-to-WF4 transport?
* If not, is the available Huidu network protocol sufficient?

### ESP32 path

* Does the purchased ESP32-S3 N16R8 match expected physical dimensions and pinout?
* Does it reliably drive one actual panel through two SN74HCT245N buffers?
* Can it drive two panels as a stable 256 × 64 row?
* Does octal-PSRAM DMA behave correctly on the purchased board?
* What frame transport should be used between Nano and ESP32?
* Is serial fast enough for the intended dashboard update rate?
* Is native USB practical and supported enough to use instead?

### Power and mechanical

* Does the inexpensive 200 W PSU maintain a stable 5 V rail under realistic display loads?
* How hot does the PSU become?
* What brightness limit provides acceptable power consumption and temperature?
* What minimum enclosure depth will be required?
* How much ventilation is necessary?

---

## 5. Immediate Next Steps

No additional architecture purchases are required before testing.

When the shipment arrives:

### Step 1 — Inventory and identify hardware

Before powering anything:

1. Confirm all ordered parts arrived
2. Photograph the front and back of:

   * Huidu HD-WF4
   * Huidu HD-WF2
   * ESP32-S3 N16R8
   * LicheeRV Nano-W
   * one LED panel
3. Record PCB revision markings
4. Record IC markings where visible
5. Confirm panel HUB75 IN/OUT orientation
6. Confirm panel power polarity
7. Confirm PSU terminal labels

### Step 2 — Measure the ESP32 board

Measure:

* board width
* board length
* header spacing
* pin count
* pin pitch
* USB connector position
* button positions
* antenna location

Do not fabricate the custom ESP32-HUB75 PCB until these measurements are confirmed.

### Step 3 — Bring up the PSU

With no display electronics attached:

1. Wire the AC input safely
2. Confirm protective earth
3. Power the PSU
4. Measure the DC output
5. Adjust only if necessary
6. Confirm approximately 5.0 V
7. Check for abnormal heating, sound, smell, or instability

### Step 4 — Power one LED module

1. Connect one panel directly to the 5 V supply
2. Confirm polarity before energizing
3. Check voltage at the panel
4. Observe initial current/thermal behavior

### Step 5 — Begin controller experiments

Order of testing:

1. HD-WF4 → one panel
2. HD-WF4 → two-panel 256 × 64 row
3. HD-WF4 → full six-panel display
4. Nano → WF4 programmable content transfer
5. HD-WF2 stock behavior
6. HD-WF2 alternative/community firmware
7. ESP32-S3 → buffered HUB75 → one panel
8. ESP32-S3 → 256 × 64 row
9. Nano → ESP32 wired frame transport
10. Compare architectures and select final controller

Detailed procedures are maintained in [`EXPERIMENTS.md`](EXPERIMENTS.md).

---

## 6. Next Major Milestone

The next major milestone is:

> **Display one programmatically generated image from the LicheeRV Nano-W on at least one real P2 panel through a candidate display controller.**

The milestone after that is:

> **Drive the complete 256 × 192 six-panel display from the Nano using the selected controller architecture.**

---

## 7. Decisions Deferred Until Testing

The following decisions should **not** be made yet:

* Final display controller
* Number of ESP32 controllers
* Final Nano-to-controller transport
* Whether a custom ESP32 PCB is required
* Final ESP32 GPIO mapping
* Final enclosure depth
* Final brightness limit
* Final cooling/ventilation arrangement
* Production controller hardware

These decisions depend on physical test results rather than theoretical compatibility.

---

## 8. Current Blockers

### External

* Waiting for ordered prototype components to arrive

### Technical

No critical technical blocker is known yet.

The principal architecture uncertainty is the application-computer-to-display-controller interface.

---

## 9. Current Project State

```text
Product definition                 DONE
        │
        ▼
High-level architecture            DONE
        │
        ▼
Prototype parts selection          DONE
        │
        ▼
Parts ordered                      DONE
        │
        ▼
Hardware delivery              ← CURRENT
        │
        ▼
Electrical bring-up
        │
        ▼
Controller experiments
        │
        ▼
Architecture selection
        │
        ▼
Custom PCB if required
        │
        ▼
Software integration
        │
        ▼
Full six-panel prototype
        │
        ▼
Frame / enclosure
        │
        ▼
V1
```

---

## 10. Related Documentation

* [`PROJECT.md`](PROJECT.md) — Product definition, requirements, and system architecture
* [`EXPERIMENTS.md`](EXPERIMENTS.md) — Test plans and experimental results
* [`BOM.md`](BOM.md) — Ordered hardware and exact component specifications
* [`DECISIONS.md`](DECISIONS.md) — Accepted and pending architecture decisions

