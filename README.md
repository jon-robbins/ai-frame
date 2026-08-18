# AI Frame

AI Frame is a thin, wall-mounted, Wi-Fi-connected ambient information display built from high-density RGB LED matrix panels.

It surfaces personal and contextual data — time, weather, calendar, music, artwork, and other API-driven content — without behaving like a conventional monitor or requiring constant interaction.

## Prototype

The first prototype combines six P2 RGB HUB75E panels (256 × 192 logical resolution), a Sipeed LicheeRV Nano-W running Linux/Python as the application computer, a dedicated HUB75 display controller, and a centralized 5 V / 40 A supply.

```text
Internet/APIs
     │ Wi-Fi
     ▼
LicheeRV Nano-W (Linux + Python)
     │ display data
     ▼
Display controller ──► 256 × 192 display
```

The display-controller architecture is still under evaluation; candidates and selection rationale live in [docs/PROJECT.md](docs/PROJECT.md) and [docs/DECISIONS.md](docs/DECISIONS.md).

## Project Principles

- Thin and wall-mountable
- Quiet, preferably passively cooled
- Wi-Fi connected
- Python-friendly
- Built from inexpensive commodity hardware where practical
- Based on open hardware, software, and protocols where practical
- Reliable enough to run unattended
- Easy to repair and modify
- Free of unnecessary cloud dependencies

## Project Status

Prototype hardware is ordered; the next phase is hardware bring-up and architecture validation. See [docs/STATUS.md](docs/STATUS.md) for current phase and blockers, and [docs/EXPERIMENTS.md](docs/EXPERIMENTS.md) for the validation plan.

## Documentation

- [docs/PROJECT.md](docs/PROJECT.md) — Product definition, system requirements, and technical architecture
- [docs/STATUS.md](docs/STATUS.md) — Current phase, completed work, and next steps
- [docs/EXPERIMENTS.md](docs/EXPERIMENTS.md) — Planned experiments, procedures, and results
- [docs/BOM.md](docs/BOM.md) — Bill of materials (English and Chinese part names, models, quantities, status)
- [docs/DECISIONS.md](docs/DECISIONS.md) — Architecture decisions and rationale

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

The project evolves experimentally: hardware is tested before committing to an architecture. Requirements, implementations, experiments, and decisions are documented separately to keep assumptions from becoming specifications.

- **Requirement** — what the product must be capable of
- **Implementation** — hardware/software currently being evaluated
- **Experiment** — something not yet proven
- **Decision** — something tested and accepted