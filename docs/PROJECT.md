# AI Frame — Project Specification

**Last updated:** 18 August 2026

## 1. Product Definition

### 1.1 Purpose

AI Frame is a thin, wall-mounted, Wi-Fi-connected ambient information display built from high-density RGB LED matrix panels.

It behaves like an appliance or information surface, not a computer monitor: information stays persistently visible without unlocking a phone, opening an app, or actively interacting with the display.

The first prototype uses six P2 HUB75E RGB LED modules arranged as a 2 × 3 matrix — logical resolution 256 × 192, physical active area 512 × 384 mm.

### 1.2 Functionality (V1)

V1 displays dynamically generated information:

- Time and date
- Weather
- Google Calendar events
- Spotify now-playing and album artwork
- Artwork and decorative graphics
- Icons and status indicators
- Other API-driven information
- Locally generated dashboards

Content updates programmatically, without manual vendor-software intervention for every update.

### 1.3 Product Principles

- Passive, ambient use rather than interaction-first
- Thin wall-mounted construction
- Quiet operation, passive cooling where practical
- Wi-Fi connectivity
- Linux and Python for application development
- Cheap commodity hardware where practical
- Chinese-market components where they give good unit economics
- Open protocols, hardware, and software where practical
- Local rendering
- Minimal unnecessary cloud dependence
- Replaceable and repairable components
- Reliable unattended operation with automatic recovery after power loss
- No exposed mains wiring
- Clear separation between application logic and real-time LED refresh

### 1.4 V1 Scope

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

### 1.5 V1 Non-Goals

V1 does not require custom ESP-IDF firmware from scratch, a custom DMA implementation, direct HUB75 refresh from Linux userspace, a private firmware fork, FPGA development, reverse-engineered hardware as a production dependency, a complex custom mainboard, a conventional HDMI monitor, or a Raspberry Pi chosen only for familiarity. Simple flashing, configuration, Python development, soldering, wiring, and a small adapter PCB are acceptable.

Rejected approaches and their rationale live in [DECISIONS.md](DECISIONS.md).

---

## 2. System Architecture

### 2.1 Block Diagram

```text
            Internet
               │ Wi-Fi
               ▼
     Application Computer          Linux / Python
     (LicheeRV Nano-W)             API clients · state · rendering
               │
          display data
               │
               ▼
     Display Controller             real-time HUB75 refresh
               │
             HUB75E
               │
     ┌─────────┼─────────┐
     ▼         ▼         ▼
 6 × RGB panels (256 × 192 logical)
```

Power is delivered independently and in parallel to panels, controller, and application computer (see [3.4 Power Subsystem](#34-power-subsystem)).

### 2.2 Core Decoupling Contract

The application computer renders the desired image; a dedicated controller generates real-time HUB75 timing. These responsibilities are separate:

- Linux/Python composes the logical 256 × 192 framebuffer.
- The display controller owns the real-time signal generation.
- The renderer does not know which physical transport is used.

Direct HUB75 bit-banging from normal Linux/Python is outside the V1 architecture.

---

## 3. Subsystem Specifications

### 3.1 Display Subsystem

- 6 × P2 indoor RGB LED modules, HUB75E interface
- 128 × 64 pixels per module, 2 mm pitch, 256 × 128 mm per module
- 1/32 scan, 5 V
- Combined: 256 × 192 logical, 49,152 RGB pixels, arranged 2 wide × 3 high

HUB75 is not HDMI or framebuffer memory — panels require continuous refresh. The controller generates the signals `R1 G1 B1 R2 G2 B2`, row-select lines `A B C D E`, plus `CLK`, `LAT`, and `OE` (~14 signals total for the buffered path). Timing must remain deterministic enough to avoid flicker, ghosting, row errors, and color instability.

Requirements: correct RGB color, no visible row-addressing errors, no objectionable flicker, adjustable brightness, stable continuous operation, and each panel receives power directly rather than through another panel.

Signal chaining (panel OUT → next panel IN) is allowed. Power chaining through panel connectors is not.

### 3.2 Compute Subsystem (Application Computer)

Hardware: Sipeed LicheeRV Nano-W — Sophgo SG2002 SoC, 256 MB DDR3, microSD storage, Wi-Fi, USB 2 OTG Type-C, Linux.

OS classes: Linux / Buildroot / Debian-family options. The 256 MB RAM budget favors a lightweight stack — Python with `asyncio`, `aiohttp`, and `Pillow`; heavy desktop or data-science stacks are not dependencies.

Role: run Linux and Python, connect to Wi-Fi, call external APIs, maintain application state, render a 256 × 192 framebuffer, load and manipulate images, render text/fonts, and send display content to the controller. It boots headless, starts the application automatically, and recovers after power interruption.

### 3.3 Display Controller Subsystem

The final controller is unselected; three candidates are under evaluation:

- **HD-WF4** — purpose-built controller, four HUB75 outputs; three outputs map cleanly to the three 256 × 64 panel rows. Primary stock-controller candidate.
- **HD-WF2** — two HUB75 outputs; experimental, reverse-engineering, and community-firmware test platform. Does not map cleanly to three rows.
- **ESP32-S3 N16R8** — generic dev board, 16 MB flash, 8 MB octal PSRAM, used with a buffered (2 × SN74HCT245N) HUB75 adapter. Most open and flexible path, but more integration work than stock Huidu hardware.

Requirements: drive HUB75E at 1/32 scan, maintain sufficient refresh rate, receive dynamically generated content, operate unattended and recover after power loss, fit the enclosure, and keep sensible unit economics. Preferences: wired computer-to-controller link, documented or reverse-engineered protocol, existing stable firmware, open-source support, no custom real-time firmware development, commodity hardware. It must not perform API calls, business logic, dashboard composition, authentication, or complex layout rendering.

**Multi-ESP32 (candidate, not final):** if the ESP32 path is selected, the Nano renders the full 256 × 192 image and splits it into three row images:

```text
crop y=0..63    → controller 1 (top row,    256×64)
crop y=64..127  → controller 2 (middle row, 256×64)
crop y=128..191 → controller 3 (bottom row, 256×64)
```

Controller selection and its rationale are recorded as ADRs in [DECISIONS.md](DECISIONS.md) (ADR-010 through ADR-016).

### 3.4 Power Subsystem

AC path:

```text
230 V AC → IEC C13 cable → C14 fused/switched inlet (L/N/PE) → A-200-5 PSU (5 V / 40 A / 200 W nominal)
```

DC path: parallel distribution. Each LED module receives its own power branch from the distribution harness. Do not daisy-chain panel power (`PSU → Panel 1 → Panel 2 → Panel 3`); use separate parallel branches.

Nominal panel load is 6 × 23 W = 138 W (~27.6 A at 5 V), leaving headroom for controller electronics, the application computer, and wiring losses. Thermal and voltage performance must still be tested. Protective earth connects to the PSU FG/earth terminal and exposed conductive enclosure parts.

Exact parts, brands, models, and purchase status are in [BOM.md](BOM.md).

### 3.5 Enclosure & Mechanical

Requirements: wall-mountable, thin relative to display area, not monitor-like in appearance, ventilation for PSU and electronics, safe separation of mains and low-voltage wiring, accessible service points, mechanical support for six modules, cable strain relief, space for PSU and control electronics, and no exposed live terminals. The ~50 mm-thick PSU is likely a major constraint on final frame depth. Enclosure decisions are tracked in [DECISIONS.md](DECISIONS.md) (ADR-024).

---

## 4. Software Architecture

### 4.1 Application Pipeline

```text
Data sources → Application state → Renderer → 256×192 framebuffer → Transport adapter → Display controller
```

Written primarily in Python against a canonical 256 × 192 canvas. Data acquisition, rendering, and transport are separate concerns; widgets/components are independent; API data is cached where appropriate. API and network errors are handled gracefully.

### 4.2 Transport Abstraction

The renderer is agnostic to the transport: Huidu protocol, serial, USB, TCP, or another local interface. The controller transport can be swapped without rewriting the application.

The open question — how the Nano transfers rendered content to the controller — is the primary architecture experiment, tracked in [EXPERIMENTS.md](EXPERIMENTS.md).

### 4.3 Fault Tolerance & Recovery

The system recovers automatically from temporary Wi-Fi failure, API timeouts, API authentication failure, display-controller restart, Nano restart, and complete power outage. A transient API failure must not crash the application; the renderer and transport layers are independently restartable where practical.

Supporting mechanisms (target): structured logs, service supervision (`systemd`), automatic application restart, and a simple health indicator.

### 4.4 Boot Sequence

Power applied → PSU stabilizes → display controller boots and Nano boots Linux → Wi-Fi connection → AI Frame application starts → load cached/default UI → fetch remote data → render framebuffer → send to controller(s) → periodic updates.

### 4.5 Offline Behavior

The display must not go blank during an Internet outage. It retains cached/local state to show the clock, last-known data, an offline status indicator, or a fallback screen, and reconnects after Wi-Fi failure.

---

## 5. Safety Requirements

- **Mains:** enclosed wiring, appropriately rated conductors, insulated terminals, protective earth, fuse protection, strain relief, and physical separation from low-voltage wiring. No breadboard or Dupont connections on the mains side.
- **DC:** suitable conductor gauge, parallel distribution, no loose temporary connections, no routing total panel current through development boards, and heating checks during load testing.
- **HUB75:** do not hot-plug ribbon cables while the panel or controller is powered.

---

## 6. Document Map

- [BOM.md](BOM.md) — exact parts, brands, models, Chinese names, quantities, prices, and purchase status
- [DECISIONS.md](DECISIONS.md) — architecture decisions, rationale, and rejected approaches
- [EXPERIMENTS.md](EXPERIMENTS.md) — validation procedures, results, and open architectural unknowns
- [STATUS.md](STATUS.md) — current phase, blockers, milestones, and next actions

Hardware-specific files eventually live under `hardware/pcb/`, `hardware/schematics/`, and `hardware/photos/`.