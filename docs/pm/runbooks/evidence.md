# Shared Runbook: Evidence Conventions

**Document ID:** `RB-EVIDENCE`  
**Location:** `docs/pm/runbooks/evidence.md`  
**Purpose:** Canonical reference for evidence collection, naming conventions, photo capture standards, and quantitative data logging. Backlog tasks reference this runbook to maintain verifiable proof of completion without repeating verbose evidence instructions.

---

## 1. Overview & Core Principles

Every task in the AI Frame project requires verifiable, reproducible evidence before it can be marked as complete. Evidence serves three core goals:
1. **Safety Assurance:** Verification that hazardous wiring and critical isolations were inspected before energization.
2. **Architectural Grounding:** Quantitative bench data to feed ADRs and validate candidate hardware.
3. **No-Guessing Audit Trail:** Factual documentation of delivered components, physical markings, and measured parameters.

---

## 2. Standard Evidence Formats

Evidence is collected and stored using four standardized formats:

| Format | Storage Location / Path | Purpose & Content |
|---|---|---|
| **Measurement & Test Logs** | `docs/pm/evidence/AF-XXX-<description>.md` | Detailed markdown log of test procedures, raw numeric measurements, environment conditions, timestamps, and pass/fail evaluations. |
| **Photographic Evidence** | `hardware/photos/AF-XXX-<view>.jpg` or `docs/pm/evidence/photos/` | High-resolution still images documenting hardware receipt, physical wiring, multimeter readings, safety isolation, and visual display outputs. |
| **Terminal & Console Captures** | Embedded in measurement markdown log or `docs/pm/evidence/logs/AF-XXX-<desc>.log` | Raw ASCII/UTF-8 terminal sessions, test script output, serial monitor logs, compiler output, or kernel dmesg captures. |
| **Git Commit References** | Referenced in task evidence field | Git commit SHA (7-character short SHA or full 40-character SHA) for software, firmware, configuration, or documentation changes. |

---

## 3. Photo Requirements & Capture Standards

Photos must be clear, well-lit, and in focus. Blurry, low-resolution, or ambiguous photos are rejected.

### Specific Photo Categories

1. **Before / After Wiring States:**
   - Wide-angle bench view showing all connected hardware before power is applied.
   - Close-up showing finished harness assemblies, terminal crimps, ferrule seating, and heat-shrink insulation.
2. **Critical Safety Isolation (Mandatory Close-Ups):**
   - **CH340 Isolation:** Clear macro photo showing CH340 `5V` and `3.3V` header pins floating / disconnected, with only `TX`, `RX`, and `GND` wired.
   - **C14 Mains Inlet:** Close-up of rear tabs showing insulated 6.3 mm spade terminals, proper heat-shrink coverage, and the unswitched PE ground connection.
   - **Fuse Inspection:** Close-up of fuse seated in the C14 fuse drawer showing rating markings (e.g., `T2A 250V`).
3. **Multimeter Readings:**
   - Photo framing both the digital multimeter (DMM) display (showing exact numbers and units/decimal points) and the test probe tips touching the test points simultaneously.
4. **IC Markings & Board Silkscreen:**
   - Legible macro photos of integrated circuits (e.g., SG2002 on LicheeRV Nano, ESP32-S3 module shield, SN74HCT245N package markings, LED panel driver ICs).
   - Silkscreen markings showing PCB revision, pin 1 indicators, and connector labels (e.g., `HUB75E`, `IN`, `OUT`, `VCC`, `GND`).

---

## 4. Naming Conventions

All evidence files must follow strict kebab-case naming prefixed with the stable task ID:

- **Evidence Log Files:**
  `docs/pm/evidence/AF-XXX-<short-description>.md`  
  *Examples:*
  - `docs/pm/evidence/AF-014-psu-no-load-voltage.md`
  - `docs/pm/evidence/AF-033-pillow-rendering-smoke.md`
  - `docs/pm/evidence/AF-088-esp32-refresh-rate.md`

- **Photos:**
  `hardware/photos/AF-XXX-<subject-or-angle>.jpg` (or `.png`)  
  *Examples:*
  - `hardware/photos/AF-013-c14-rear-wiring-insulated.jpg`
  - `hardware/photos/AF-025-ch340-3pin-vcc-floating.jpg`
  - `hardware/photos/AF-044-psu-5v-multimeter-reading.jpg`
  - `hardware/photos/AF-055-panel1-solid-red-test.jpg`

- **Terminal Logs:**
  `docs/pm/evidence/logs/AF-XXX-<command-or-test>.log`  
  *Examples:*
  - `docs/pm/evidence/logs/AF-026-nano-boot-dmesg.log`
  - `docs/pm/evidence/logs/AF-035-pytest-framebuffer.log`

---

## 5. Quantitative Recording Rules

Never record subjective or vague impressions such as "looks fine", "room temperature", "voltage normal", or "high refresh rate". All engineering data must be recorded with exact quantities:

1. **Exact Numeric Values:**
   - Record exact numbers with units and decimal places (e.g., `5.08 V DC`, `23.4 °C`, `4.62 A`, `62.5 Hz`, `0.02 Ω`).
2. **Timestamps:**
   - Include date and time in ISO 8601 format (e.g., `2026-08-19T14:30:00Z` or `2026-08-19 14:30:00`) on all test sessions and log entries.
3. **Instrumentation & Setup Configuration:**
   - Note the measurement tool model, range setting, and measurement points (e.g., *"CHINT ZTW0138A DMM set to 20V DC range, measured across PSU +V and -V screw terminals"*).
4. **Explicit Pass/Fail Evaluation Against Tolerances:**
   - State the required nominal tolerance range and compare the measured value against it:
     - *Expected:* `5.00 V ± 0.25 V (4.75 V to 5.25 V)`
     - *Measured:* `5.04 V`
     - *Result:* `PASS`
5. **Hold & Stability Duration Logging:**
   - For burn-in, thermal, or stability hold tests (e.g., 10-minute PSU no-load hold, 1-hour sustained video playback):
     - Record **Start Time**, **End Time**, **Sampling Interval**, and a table of intermediate readings.
     - Document peak temperature, drift, or any visual artifacts.

---

## 6. Standard Evidence Markdown Template

When creating a measurement log in `docs/pm/evidence/AF-XXX-<desc>.md`, use this template:

```markdown
# Evidence Log: AF-XXX — [Task Title]

**Task ID:** AF-XXX  
**Date / Timestamp:** YYYY-MM-DD HH:MM  
**Executor:** [Name / Agent ID]  
**Safety Profile Applied:** [MAINS | HUB75 | CH340 | 5V-HIGH-CURRENT | N/A]  

## 1. Test Setup & Equipment
- **Instruments used:** [e.g., CHINT ZTW0138A DMM, HS-202D crimper]
- **Hardware tested:** [e.g., A-200-5 PSU, P2-32S Panel #1]
- **Tool settings / Range:** [e.g., DMM 20V DC mode]

## 2. Quantitative Measurements

| Test Point | Parameter | Expected Range / Limit | Measured Value | Result |
|---|---|---|---|---|
| PSU output terminal | DC Voltage (no-load) | 4.75 V – 5.25 V | 5.06 V | PASS |
| PE to PSU Frame | Continuity / Resistance | < 0.10 Ω | 0.01 Ω | PASS |

## 3. Visual & Safety Checks
- [x] Pre-power visual inspection complete: no exposed strands.
- [x] Critical safety isolation verified (see photo: `hardware/photos/AF-XXX-setup.jpg`).

## 4. Photos & Artifacts
- Attached photo: `hardware/photos/AF-XXX-dmm-voltage.jpg`
- Log file: `docs/pm/evidence/logs/AF-XXX-test.log`
- Commit hash: `abcdef1`

## 5. Conclusion
- [x] All acceptance criteria met. Task PASSED.
```
