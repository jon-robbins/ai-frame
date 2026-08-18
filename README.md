# AI Frame

AI Frame is a thin, wall-mounted, Wi-Fi-connected ambient information display built from high-density RGB LED matrix panels.

The goal is to create an always-visible information surface that can show useful personal and contextual data—such as time, weather, calendar events, music, artwork, and other API-driven information—without behaving like a conventional monitor or requiring constant interaction.

## Prototype

The first prototype uses:

- Six P2 RGB HUB75E LED panels
- 256 × 192 total logical resolution
- Sipeed LicheeRV Nano-W as the Linux/Python application computer
- A dedicated HUB75 display controller
- 5 V / 40 A centralized power supply

The display-controller architecture is still being evaluated. Current candidates are:

- Huidu HD-WF4
- Huidu HD-WF2
- ESP32-S3 N16R8 with a buffered HUB75 interface

The application computer handles networking, APIs, rendering, and application logic. A separate controller handles the timing-sensitive HUB75 LED refresh.

```text
Internet / APIs
      │
      │ Wi-Fi
      ▼
┌────────────────────┐
│ LicheeRV Nano-W    │
│ Linux + Python     │
│                    │
│ APIs               │
│ Rendering          │
│ Application state  │
└─────────┬──────────┘
          │
     display data
          │
          ▼
┌────────────────────┐
│ Display Controller │
│ HUB75 refresh      │
└─────────┬──────────┘
          │
          ▼
   256 × 192 display
```

## Project Principles

AI Frame is intended to be:

* Thin and wall-mountable
* Quiet and preferably passively cooled
* Wi-Fi connected
* Python-friendly
* Built from inexpensive commodity hardware where practical
* Based on open hardware, software, and protocols where practical
* Reliable enough to run unattended
* Easy to repair and modify
* Free of unnecessary cloud dependencies

For the first version, the project deliberately avoids making custom low-level firmware, undocumented GPIO reverse engineering, complex PCB design, or direct HUB75 refresh from Linux/Python into hard dependencies.

## Current Status

Prototype hardware has been selected and ordered.

The next phase is hardware bring-up and architecture validation:

1. Verify the power system
2. Bring up one LED panel
3. Test the Huidu HD-WF4
4. Test the Huidu HD-WF2
5. Test the ESP32-S3 buffered HUB75 path
6. Test wired communication between the application computer and display controller
7. Select the final controller architecture
8. Design a small custom PCB if the ESP32 route is selected

## Documentation

Detailed project information is split into focused documents:

* [`docs/PROJECT.md`](docs/PROJECT.md) — Product definition, system requirements, and technical architecture
* [`docs/STATUS.md`](docs/STATUS.md) — Current project phase, completed work, and next steps
* [`docs/EXPERIMENTS.md`](docs/EXPERIMENTS.md) — Planned experiments, procedures, results, and conclusions
* [`docs/BOM.md`](docs/BOM.md) — Bill of materials, including model names, English and Chinese part names, quantities, and status
* [`docs/DECISIONS.md`](docs/DECISIONS.md) — Architecture decisions and the reasoning behind them

## Repository Structure

```text
ai-frame/
├── README.md
├── docs/
│   ├── PROJECT.md
│   ├── STATUS.md
│   ├── EXPERIMENTS.md
│   ├── BOM.md
│   └── DECISIONS.md
├── hardware/
│   ├── pcb/
│   ├── schematics/
│   └── photos/
├── software/
│   ├── app/
│   └── display/
└── firmware/
```

## Development Approach

The project is being developed experimentally rather than committing to an architecture before the hardware is tested.

Requirements, candidate implementations, experiments, and final decisions are intentionally documented separately:

* **Requirement** — what the product must be capable of
* **Implementation** — the hardware or software currently being evaluated
* **Experiment** — something not yet proven
* **Decision** — something tested and accepted

This distinction is intended to keep the repository from turning assumptions into specifications as the prototype evolves.

