# AI Frame — Project Status

**Last updated:** 18 August 2026
**Current phase:** Prototype hardware acquisition and architecture validation

## Current State

The initial prototype architecture has been defined and all required test hardware has been ordered. The project is waiting for components to arrive before hardware bring-up and controller validation begins.

## Active Blockers

- **External:** Waiting for prototype component shipment delivery.
- **Technical:** None currently critical. The display-controller interface architecture is the primary uncertainty, to be resolved by experiments.

## Milestones

| # | Milestone | Status |
|---|-----------|--------|
| 1 | Display one programmatically generated image on at least one P2 panel through a candidate display controller, driven from the LicheeRV Nano-W | NOT STARTED |
| 2 | Drive the complete 256×192 six-panel display from the Nano using the selected controller architecture | NOT STARTED |

## Next Actions (On Hardware Arrival)

1. Hardware inspection and inventory → [EXP-001](EXPERIMENTS.md)
2. ESP32 physical measurement → [EXP-010](EXPERIMENTS.md)
3. PSU no-load bring-up → [EXP-002](EXPERIMENTS.md)
4. Single-panel power test → [EXP-003](EXPERIMENTS.md)
5. Controller candidate experiments → [EXP-004](EXPERIMENTS.md) through [EXP-014](EXPERIMENTS.md)

## Project Lifecycle

1. ~~Product definition~~ ✓
2. ~~High-level architecture~~ ✓
3. ~~Prototype parts selection~~ ✓
4. ~~Parts ordered~~ ✓
5. **Hardware delivery** ← CURRENT
6. Electrical bring-up
7. Controller experiments
8. Architecture selection → [ADR-016](DECISIONS.md)
9. Custom PCB (if ESP32 path) → [ADR-018](DECISIONS.md)
10. Software integration
11. Full six-panel prototype
12. Frame / enclosure → [ADR-024](DECISIONS.md)
13. V1

## Related Documentation

- [PROJECT.md](PROJECT.md) — System specification and architecture
- [BOM.md](BOM.md) — Component inventory and procurement status
- [DECISIONS.md](DECISIONS.md) — Architecture decision records
- [EXPERIMENTS.md](EXPERIMENTS.md) — Validation test procedures and results
