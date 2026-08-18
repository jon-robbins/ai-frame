# AI Frame — Project Specification

## 1. Product Definition

### 1.1 What is AI Frame?

AI Frame is a thin, wall-mounted, Wi-Fi-connected ambient information display built from high-density RGB LED matrix panels.

It is intended to behave like an appliance or information surface rather than a conventional computer monitor. Information should remain persistently visible without requiring the user to unlock a phone, open an application, or actively interact with the display.

The first prototype uses six P2 HUB75E RGB LED modules arranged as a 2 × 3 matrix.

```text
┌───────────────┬───────────────┐
│    128×64     │    128×64     │
├───────────────┼───────────────┤
│    128×64     │    128×64     │
├───────────────┼───────────────┤
│    128×64     │    128×64     │
└───────────────┴───────────────┘

Logical resolution: 256 × 192
Physical active area: 512 × 384 mm
```

---

### 1.2 Primary Functionality

V1 should be capable of displaying dynamically generated information including:

* Time and date
* Weather
* Google Calendar events
* Spotify now-playing information
* Album artwork
* Artwork and decorative graphics
* Icons and status indicators
* Other API-driven information
* Locally generated dashboards

The display should be able to update content programmatically without requiring manual intervention through vendor software for every update.

---

### 1.3 Problem Being Solved

Useful personal information is generally hidden behind interactive devices.

Checking weather, upcoming appointments, music state, household status, or other information normally requires intentionally opening an application.

AI Frame makes selected information:

* continuously available,
* glanceable,
* spatially persistent,
* and integrated into the physical environment.

The intended experience is closer to a clock, picture frame, or household appliance than to a mounted computer monitor.

---

### 1.4 Product Principles

AI Frame should prioritize:

* Passive rather than interaction-first use
* Thin wall-mounted construction
* Quiet operation
* Passive cooling where practical
* Wi-Fi connectivity
* Linux and Python for high-level application development
* Cheap commodity hardware where practical
* Chinese-market components where they provide good unit economics
* Open protocols, hardware, and software where practical
* Local rendering
* Minimal unnecessary cloud dependence
* Replaceable and repairable components
* Reliable unattended operation
* Automatic recovery after power loss
* No exposed mains wiring
* Clear separation between application logic and real-time LED refresh

---

### 1.5 V1 Scope

V1 is primarily an information display.

V1 must prove:

1. Reliable power delivery
2. Stable operation of all six LED panels
3. Wi-Fi connectivity
4. Linux/Python application execution
5. API access
6. Image/layout rendering
7. Programmatic transfer of rendered content to the display controller
8. Stable HUB75 refresh without objectionable flicker
9. Automatic startup after power loss
10. Practical packaging into a wall-mounted enclosure

---

### 1.6 V1 Non-Goals

V1 should not require:

* Custom ESP-IDF firmware written from scratch
* Custom DMA implementation
* Direct HUB75 refresh from Linux userspace
* Maintaining a private firmware fork
* Complex FPGA development
* Reverse-engineering undocumented hardware as a production dependency
* A complex custom mainboard
* A conventional HDMI monitor
* A Raspberry Pi solely because it is familiar

Simple firmware flashing, configuration, Python development, soldering, wiring, and a small adapter PCB are acceptable.

---

# 2. System Overview

The system is divided into several functional subsystems.

```text
                  ┌───────────────────┐
                  │     Internet      │
                  └─────────┬─────────┘
                            │ Wi-Fi
                            ▼
                  ┌───────────────────┐
                  │ Application       │
                  │ Computer          │
                  │                   │
                  │ Linux / Python    │
                  │ API clients       │
                  │ Rendering         │
                  └─────────┬─────────┘
                            │
                       display data
                            │
                            ▼
                  ┌───────────────────┐
                  │ Display           │
                  │ Controller        │
                  │                   │
                  │ HUB75 refresh     │
                  └─────────┬─────────┘
                            │ HUB75E
                            ▼
                  ┌───────────────────┐
                  │ 6 × RGB Panels    │
                  │ 256 × 192 total   │
                  └───────────────────┘
```

Power is provided independently:

```text
230 V AC wall
      │
      ▼
Fused / switched inlet
      │
      ▼
5 V / 40 A PSU
      │
      ├──► LED panels
      ├──► display controller
      └──► application computer
           where appropriate
```

---

# 3. Primary System Components

## 3.1 Display

Current implementation:

* 6 × P2 indoor RGB LED modules
* HUB75E interface
* 128 × 64 pixels per module
* 1/32 scan
* 5 V
* 256 × 128 mm per module

Combined logical resolution:

```text
256 × 192
```

Combined RGB pixel count:

```text
49,152 pixels
```

The modules are physically arranged:

```text
2 wide × 3 high
```

---

## 3.2 Application Computer

Current implementation:

**Sipeed LicheeRV Nano-W**

Role:

* Run Linux
* Run Python
* Connect to Wi-Fi
* Call external APIs
* Maintain application state
* Render graphics
* Compose the complete logical framebuffer
* Send display content to the display controller

The application computer is deliberately separated from the real-time HUB75 refresh engine.

---

## 3.3 Display Controller

The final display controller has not yet been selected.

Three approaches are being evaluated:

### Candidate A — Huidu HD-WF4

Purpose-built LED controller with four HUB75 outputs.

Currently the most attractive stock-controller candidate because three outputs can map cleanly to the three physical panel rows.

### Candidate B — Huidu HD-WF2

Smaller Huidu controller with two HUB75 outputs.

Primarily useful as:

* an experimental controller,
* a reverse-engineering platform,
* and a test platform for community firmware.

It does not map as naturally to the final three-row display.

### Candidate C — ESP32-S3 N16R8

Generic ESP32-S3 development board with:

* 16 MB flash
* 8 MB octal PSRAM

Used with a buffered HUB75 adapter.

This is the most open and flexible controller path, but requires more integration work than using stock Huidu hardware.

The final controller decision is intentionally deferred until experiments are complete.

---

## 3.4 Power Supply

Current implementation:

**A-200-5 switching power supply**

Nominal output:

```text
5 V
40 A
200 W
```

Maximum nominal LED-panel load:

```text
6 × 23 W = 138 W
```

Approximate maximum panel current:

```text
138 W / 5 V = 27.6 A
```

This leaves useful nominal headroom for:

* controller electronics,
* application computer,
* wiring losses,
* and avoiding operation at the absolute PSU limit.

Actual thermal and voltage performance must still be tested.

---

## 3.5 AC Power Interface

The finished prototype will use:

* IEC C14 panel inlet
* integrated rocker switch
* integrated fuse holder
* protective earth
* internal L/N/PE wiring
* enclosed AC terminals

The design must not expose mains wiring during normal operation.

---

## 3.6 Network Interface

Wi-Fi is provided by the LicheeRV Nano-W.

The application computer is the primary networked device.

The LED controller does not inherently need Internet access if it can receive display data from the Nano through a local wired connection.

---

## 3.7 Frame and Enclosure

The final enclosure has not yet been designed.

Requirements include:

* Wall mountable
* Thin relative to the display area
* No obvious appearance of a conventional monitor
* Ventilation sufficient for PSU and electronics
* Safe separation of mains and low-voltage wiring
* Accessible service points
* Mechanical support for six modules
* Cable strain relief
* Space for PSU and control electronics
* No exposed live terminals

The existing PSU is approximately 50 mm thick and is likely to be one of the major constraints on final frame depth.

---

# 4. Component Requirements

## 4.1 Display Requirements

The display subsystem must:

* Support six 128 × 64 RGB modules
* Support HUB75E
* Support the panel's 1/32 scan mode
* Produce a logical 256 × 192 canvas
* Display correct RGB colors
* Avoid visible row-addressing errors
* Avoid objectionable flicker
* Maintain stable operation continuously
* Support adjustable brightness
* Permit each panel to receive power directly rather than through another panel

Signal chaining is allowed.

Power chaining through panel connectors is not.

---

## 4.2 Application Computer Requirements

The application computer must:

* Run Linux
* Run Python
* Support Wi-Fi
* Support HTTPS
* Make REST/API calls
* Store credentials/configuration locally
* Render at least a 256 × 192 RGB framebuffer
* Load and manipulate images
* Render text and fonts
* Communicate with the display controller
* Boot without keyboard or monitor
* Start the application automatically
* Recover after power interruption

Current hardware provides:

```text
SoC: Sophgo SG2002
RAM: 256 MB DDR3
Storage: microSD
Network: Wi-Fi
OS class: Linux / Buildroot / Debian-family options
```

The 256 MB RAM constraint favors a lightweight application stack.

Preferred application technologies include:

* Python
* asyncio
* aiohttp
* Pillow

Heavy desktop or data-science stacks should not be treated as dependencies.

---

## 4.3 Display Controller Requirements

The final display controller must:

* Drive HUB75E
* Support 1/32 scan
* Maintain sufficiently high refresh rates
* Receive dynamically generated content
* Operate unattended
* Recover after power interruption
* Accept content from the application computer
* Fit within the enclosure
* Be inexpensive enough for reasonable product unit economics

Strong preferences:

* Wired computer-to-controller communication
* Documented or reverse-engineered protocol
* Existing stable firmware
* Open-source support
* No custom real-time firmware development
* Commodity hardware

The controller does not need to perform:

* API calls
* business logic
* dashboard composition
* authentication
* complex layout rendering

unless a future architecture intentionally combines those roles.

---

## 4.4 Power Requirements

The system must:

* Accept local mains AC
* Convert mains to regulated 5 V DC
* Supply at least the expected display load
* Maintain voltage stability under changing LED brightness
* Provide protective earth where required
* Include a fuse
* Include a master power switch
* Use appropriate wire gauge
* Avoid carrying total display current through a single small-gauge conductor
* Avoid routing panel power through controller PCBs unless explicitly designed for it

The six panels should receive power through multiple parallel branches.

---

## 4.5 Network Requirements

The product must:

* Join a normal Wi-Fi network
* Reach HTTPS APIs
* Reconnect after temporary Wi-Fi failure
* Continue displaying useful cached/local content where practical during Internet failure
* Avoid requiring a cloud relay merely to update the local display

---

## 4.6 Mechanical Requirements

The finished product should:

* Hold all six modules securely
* Maintain panel alignment
* Be wall mountable
* Minimize visible bezels or gaps
* Hide controller electronics
* Hide PSU wiring
* Provide airflow around heat-producing components
* Allow disassembly for repair
* Provide strain relief for AC input
* Keep low-voltage and mains wiring organized and separated

---

## 4.7 Software Requirements

The application software should:

* Be written primarily in Python
* Render into a canonical 256 × 192 canvas
* Separate data acquisition from rendering
* Separate rendering from transport
* Support independent widgets/components
* Allow display-controller transport to be swapped without rewriting the application
* Cache API data where appropriate
* Handle API/network errors gracefully
* Start automatically at boot
* Log failures sufficiently for debugging

The software architecture should make this abstraction possible:

```text
Data sources
    │
    ▼
Application state
    │
    ▼
Renderer
    │
    ▼
256×192 framebuffer
    │
    ▼
Transport adapter
    │
    ▼
Selected display controller
```

The renderer should not need to know whether the transport is:

* Huidu protocol,
* serial,
* USB,
* TCP,
* or another local interface.

---

# 5. Technical Architecture

## 5.1 High-Level Architecture

```text
┌─────────────────────────────────────────────┐
│                 INTERNET                    │
│                                             │
│ Weather   Calendar   Spotify   Other APIs  │
└─────────────────────┬───────────────────────┘
                      │
                    Wi-Fi
                      │
                      ▼
             ┌──────────────────┐
             │ LicheeRV Nano-W  │
             │                  │
             │ Linux            │
             │ Python           │
             │ API clients      │
             │ State            │
             │ Renderer         │
             └────────┬─────────┘
                      │
                 rendered data
                      │
                      ▼
             ┌──────────────────┐
             │ Display          │
             │ Controller       │
             └────────┬─────────┘
                      │
                    HUB75E
                      │
         ┌────────────┼────────────┐
         ▼            ▼            ▼
      Top row      Middle row    Bottom row
      256×64         256×64        256×64
```

---

## 5.2 Power Architecture

### AC side

```text
230 V AC wall
      │
      ▼
IEC C13 power cable
      │
      ▼
┌──────────────────────────┐
│ Fused + switched C14     │
│ panel inlet              │
└────────────┬─────────────┘
             │
        L / N / PE
             │
             ▼
┌──────────────────────────┐
│ A-200-5 PSU              │
│ 230 V AC → 5 V DC        │
│ 40 A / 200 W             │
└──────────────────────────┘
```

Protective earth must be connected to:

* PSU FG/earth terminal
* exposed conductive enclosure parts where applicable

---

### DC side

```text
                     5 V / 40 A PSU
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
        Power branch   Power branch   Electronics
              │            │
       ┌──────┼──────┐     │
       ▼      ▼      ▼     ▼
     panels  panels  ... controller /
                         application computer
```

Each LED module receives its own suitable power branch from the distribution harness.

Do not use:

```text
PSU → Panel 1 → Panel 2 → Panel 3 power
```

Prefer:

```text
               PSU
                │
       ┌────────┼────────┐
       ▼        ▼        ▼
    Panel 1  Panel 2  Panel 3 ...
```

---

## 5.3 Network Architecture

```text
Internet
   │
   ▼
Wi-Fi router/AP
   │
   ▼
LicheeRV Nano-W
   │
   ├── Weather API
   ├── Google Calendar
   ├── Spotify
   └── Other APIs
```

The Nano is responsible for Internet-facing application logic.

The controller should ideally operate as a local peripheral.

---

## 5.4 Display Data Path

Conceptually:

```text
API responses
     │
     ▼
Application state
     │
     ▼
Python renderer
     │
     ▼
256×192 RGB framebuffer
     │
     ▼
Controller-specific transport
     │
     ▼
Display controller
     │
     ▼
HUB75 refresh
     │
     ▼
LED panels
```

The unresolved section is currently:

```text
Nano ───── ? ─────► Display controller
```

Determining the best implementation of this transport is the primary architecture experiment.

---

# 6. HUB75 Refresh Architecture

HUB75 is not equivalent to HDMI, SPI display memory, or a conventional framebuffer peripheral.

The panels require continuous refresh.

The controller repeatedly generates signals such as:

```text
R1 G1 B1
R2 G2 B2
A B C D E
CLK
LAT
OE
```

A simplified refresh process is:

```text
Select row
   │
   ▼
Shift RGB data using CLK
   │
   ▼
Latch data using LAT
   │
   ▼
Control illumination using OE
   │
   ▼
Repeat with PWM bitplanes
   │
   ▼
Advance to next row
   │
   ▼
Repeat continuously
```

Timing must remain sufficiently deterministic to avoid:

* flicker,
* ghosting,
* incorrect rows,
* brightness artifacts,
* and unstable colors.

Therefore:

```text
Linux / Python
      │
      │ generates desired image
      ▼
Display controller
      │
      │ generates real-time HUB75 signals
      ▼
LED matrix
```

Direct HUB75 bit-banging from normal Linux/Python is outside the V1 architecture.

---

# 7. Confirmed and Candidate Controller Topologies

## 7.1 WF4 Physical Panel Topology

The preferred WF4 mapping is:

```text
             HD-WF4

X1 ──► TOP-L ──OUT──► TOP-R

X2 ──► MID-L ──OUT──► MID-R

X3 ──► BOT-L ──OUT──► BOT-R

X4 ──► unused
```

This creates three independent 256 × 64 signal chains.

This physical arrangement is preferred because it directly mirrors the actual display geometry.

Exact software mapping in Huidu configuration tools must still be experimentally verified.

---

## 7.2 WF2 Topology

WF2 provides only two outputs.

There is no equally clean physical mapping from two outputs to three 256 × 64 rows.

Therefore WF2 is currently treated primarily as an experimental controller rather than the default final topology.

---

## 7.3 ESP32-S3 Buffered HUB75 Topology

The ESP32 uses 3.3 V GPIO while the chosen robust HUB75 interface uses 5 V HCT buffering.

```text
                  ESP32-S3 N16R8
                        │
                 3.3 V GPIO signals
                        │
             ┌──────────┴──────────┐
             ▼                     ▼
       SN74HCT245N #1       SN74HCT245N #2
             │                     │
             └──────────┬──────────┘
                        │
                  buffered signals
                        │
                        ▼
               16-pin HUB75E
                        │
                        ▼
                    LED panel
```

Approximately fourteen signals are required, so two 8-channel buffers are sufficient.

Panel high-current 5 V power remains separate from the signal adapter.

---

## 7.4 Potential Multi-ESP32 Final Architecture

If the ESP32 route is ultimately selected:

```text
                       LicheeRV Nano-W
                             │
                      frame transport
                             │
             ┌───────────────┼───────────────┐
             │               │               │
             ▼               ▼               ▼
       ESP32-S3 #1      ESP32-S3 #2      ESP32-S3 #3
             │               │               │
             ▼               ▼               ▼
          Top row         Middle row       Bottom row
          256×64           256×64           256×64
```

The Nano would render the complete 256 × 192 image and divide it into three row images:

```text
Full framebuffer
256×192
   │
   ├── crop y=0..63     → controller 1
   ├── crop y=64..127   → controller 2
   └── crop y=128..191  → controller 3
```

This architecture remains a candidate, not a final decision.

---

# 8. Boot and Runtime Behavior

The eventual appliance should behave approximately as follows:

```text
Power applied
     │
     ▼
PSU stabilizes
     │
     ├──► display controller boots
     │
     └──► Nano boots Linux
                 │
                 ▼
          Wi-Fi connection
                 │
                 ▼
        AI Frame application
                 │
                 ▼
        load cached/default UI
                 │
                 ▼
          fetch remote data
                 │
                 ▼
         render framebuffer
                 │
                 ▼
       send to controller(s)
                 │
                 ▼
          periodic updates
```

The display should not remain blank solely because the Internet is temporarily unavailable.

A future implementation should retain enough cached/local state to display:

* clock,
* last-known data,
* offline status,
* or a fallback screen.

---

# 9. Failure and Recovery Requirements

The system should recover automatically from:

* Temporary Wi-Fi failure
* API timeouts
* API authentication failure
* Display-controller restart
* Nano restart
* Complete power outage

A transient API failure should not crash the entire application.

The renderer and transport layers should be restartable independently where practical.

The system should eventually support:

* structured logs,
* service supervision,
* automatic application restart,
* and a simple health indicator.

---

# 10. Safety Requirements

## Mains

AC mains wiring must:

* remain enclosed,
* use appropriately rated conductors,
* use insulated terminals,
* include protective earth,
* include fuse protection,
* include strain relief,
* and remain physically separated from low-voltage signal wiring where practical.

No breadboard or Dupont connections are permitted on the mains side.

---

## DC Power

High-current 5 V wiring must:

* use suitable conductor gauge,
* use parallel distribution,
* avoid loose temporary connections,
* avoid routing total panel current through development boards,
* and be checked for heating during load testing.

---

## HUB75

Do not hot-plug HUB75 ribbon cables while the panel/controller is powered.

---

# 11. Design Constraints

The current prototype is constrained by:

### Cost

The architecture should maintain sensible unit economics.

A controller costing ¥130–160 is unattractive if a comparable result can be achieved using:

* ¥30-class ESP32 hardware,
* inexpensive Huidu controllers,
* or a very cheap custom PCB.

### Software complexity

V1 should remain accessible through normal:

* Linux administration,
* Python,
* configuration,
* and existing firmware.

### Memory

The Nano has 256 MB RAM, so software must remain lightweight.

### Panel size

The complete display contains 49,152 RGB pixels.

A single microcontroller may not necessarily provide satisfactory HUB75 refresh for the complete 256 × 192 matrix.

### Mechanical depth

The PSU and electrical safety clearances may determine minimum enclosure thickness.

---

# 12. Current Architectural Unknowns

The largest unresolved issue is:

> How should the LicheeRV Nano transfer dynamically rendered display content to the HUB75 refresh controller?

Questions still requiring experimental answers include:

1. Can the HD-WF4 accept sufficiently frequent programmatic updates?
2. Is there a practical wired interface between Nano and WF4?
3. Can the known Huidu network protocol satisfy the product requirements if a wired interface is unsuitable?
4. Can the WF4 correctly map the three 256 × 64 physical chains into one 256 × 192 canvas?
5. How useful is the WF2 with stock firmware?
6. How useful is existing open/community WF2 firmware?
7. Does the purchased ESP32-S3 N16R8 reliably drive the actual P2 128 × 64 panels through HCT245 buffers?
8. Can one ESP32 reliably drive two chained panels at 256 × 64?
9. What wired transport should be used between the Nano and an ESP32?
10. Is serial bandwidth sufficient for the desired update rate?
11. Is native USB practical?
12. What final enclosure depth is required?
13. What brightness limit should be used for acceptable thermals and power consumption?

These questions are tracked and tested in `EXPERIMENTS.md`.

---

# 13. Architectural Decisions Already Established

The following principles are currently considered accepted unless testing gives a strong reason to revisit them:

### Application logic runs on Linux

The LicheeRV Nano-W is the prototype application computer.

### Application logic and HUB75 refresh are separate responsibilities

Linux/Python generates the desired image.

A dedicated controller generates the actual HUB75 timing.

### Panels receive parallel power distribution

Signal cables may be chained.

Panel power should not be daisy-chained through the modules.

### Final controller remains undecided

WF4, WF2, and ESP32 approaches are intentionally being tested before committing.

### ESP32 bulk purchase is deferred

Only one generic N16R8 board is being tested initially.

Additional controllers will only be purchased after successful validation.

### Custom ESP32 adapter PCB fabrication is deferred until physical measurement

The purchased generic ESP32 development board will be measured before designing the mating PCB to avoid mechanical footprint errors.

---

# 14. Related Documentation

See:

* [`STATUS.md`](STATUS.md) for current progress and next actions
* [`EXPERIMENTS.md`](EXPERIMENTS.md) for validation procedures and results
* [`BOM.md`](BOM.md) for exact parts, brands, models, Chinese names, quantities, and purchase status
* [`DECISIONS.md`](DECISIONS.md) for formal architecture decisions and rationale

Hardware-specific files will eventually live under:

```text
hardware/
├── pcb/
├── schematics/
└── photos/
```
