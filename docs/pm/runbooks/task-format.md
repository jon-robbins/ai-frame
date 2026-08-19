# Shared Runbook: Task Format & Authoring Schema

**Document ID:** `RB-TASK-FORMAT`  
**Location:** `docs/pm/runbooks/task-format.md`  
**Purpose:** Canonical reference for task authoring, compact schema rules, the 5-level factual validation hierarchy, and superseded task conventions.

---

## 1. Canonical Task Template

Every task in the backlog must adhere strictly to the following compact schema:

```markdown
### AF-XXX — [Verb] [specific outcome]

**Milestone:** M1
**Depends on:** AF-YYY
**Labels:** hardware, controller-esp32
**Applies if:** ADR-016 selects ESP32       # optional, states WHY conditional
**Safety:** HUB75                           # optional profile name
**Stop condition:** Disconnect panel/controller power before changing ribbon cable  # required with Safety
**Resolves:** U-XXX                         # optional
**Procedure:** EXP-XXX                      # optional
**Wiring:** hardware/... §X                 # optional
**Parts:** (list)                           # optional
**Context:** (1 sentence)                   # optional

#### Do
1. ...
2. ...

#### Done when
- ...
- ...

#### If it fails
...
```

---

## 2. Schema Rules & Field Definitions

### 2.1 Title & Identity Rules
- **Heading Format:** `### AF-XXX — [Verb] [specific outcome]`
- **Active Imperative Verb:** Titles must start with a concrete action verb (e.g., `Verify`, `Crimp`, `Flash`, `Measure`, `Assemble`, `Render`, `Connect`, `Validate`).
- **Atomic Outcome:** A task is **one meaningful project-level outcome**. A task may contain multiple
  independently checkable construction stages when those stages collectively produce
  one artifact or result. Detailed physical steps, soldering sequences, connector
  checks, test patterns, and safety checks live inside the task's `Do` section, an
  experiment, or a shared runbook unless they independently matter to project status.

  **Compression must never remove execution guidance.** A project-level task may absorb
  multiple construction or test stages, but the resulting task and its referenced
  procedures must remain executable by a novice without relying on unstated electrical,
  wiring, firmware, or tool knowledge. If merging makes the physical procedure
  ambiguous, preserve the necessary detail inside the task/runbook or do not merge
  those tasks.
- **Stable ID:** The `AF-XXX` identifier is permanent. Never renumber, reuse, or delete an assigned task ID.

### 2.2 Header Metadata Fields

| Field | Requirement | Definition / Constraint |
|---|---|---|
| **`Milestone:`** | Mandatory | Target milestone: `M0`, `M1`, `M2`, `MG`, `M3`, `M4`, `MR`, `MA`, `Cond-X`, `MF`. |
| **`Depends on:`** | Mandatory | Comma-separated list of prerequisite stable IDs (e.g., `AF-EXAMPLE-1, AF-EXAMPLE-2`) or `—` if none. |
| **`Labels:`** | Mandatory | Space-separated lowercase tags from the project taxonomy (e.g., `hardware`, `controller-esp32`, `safety-review`, `critical-path`). |
| **`Applies if:`** | Optional | Required for conditional tasks. Must explicitly state the architectural trigger (e.g., `ADR-016 selects ESP32`). |
| **`Safety:`** | Optional | Profile name referencing `docs/pm/runbooks/safety.md`: `MAINS`, `HUB75`, `CH340`, or `5V-HIGH-CURRENT`. |
| **`Stop condition:`** | Conditional | **Mandatory whenever `Safety:` is present.** A single, clear operational rule or immediate shutdown threshold. |
| **`Resolves:`** | Optional | Comma-separated Uncertainty IDs from `docs/pm/03-uncertainty-register.md` (e.g., `U-001, U-002`). |
| **`Procedure:`** | Optional | Experiment ID from `docs/EXPERIMENTS.md` (e.g., `EXP-003`). |
| **`Wiring:`** | Optional | Direct section reference to wiring documentation (e.g., `hardware/schematics/PROTOTYPE_WIRING.md §3`). |
| **`Parts:`** | Optional | List of non-standard or specialized parts when not obvious from BOM. |
| **`Context:`** | Optional | Exactly one sentence explaining background or rationale when not obvious from title and dependencies. |

### 2.3 Task Body Sections

- **`#### Do`**
  - Numbered list (`1.`, `2.`, `3.`) of physical, command-line, or code steps.
  - Every step must be discrete and unambiguous with no hidden prerequisite actions.
- **`#### Done when`**
  - Bulleted list of objective, verifiable acceptance criteria.
  - Every bullet must be measurable and pass/fail observable (voltage tolerance, beep count, color/pattern match, exact test exit code).
  - Maximum 8 bullets per task.
- **`#### If it fails`**
  - Concrete recovery and isolation procedure.
  - Specifies what to de-energize first, what to swap, and which verification task to escalate to. Never "try again blindly".

### 2.4 Fields Excluded from Task Bodies

To avoid clutter and maintenance overhead, do **NOT** include:
- Redundant headers (`Task ID`, `Epic`, `Summary` in the body).
- Verbose 20-field boilerplate copies.
- Verbose safety checklists (use `Safety: PROFILE` instead).
- Verbose evidence-to-save listings (governed globally by `docs/pm/runbooks/evidence.md`).

### 2.5 Task Granularity & Compression Rules

#### 2.5.1 Backlog-Worthiness / PM-Utility Test

Before a card is kept as a separate task, it must pass the PM-utility test. A task normally survives if several of the following are true:

1. Does it materially change project status?
2. Is it independently visible on a board?
3. Does it independently block, wait externally, or need a separate decision?
4. Does it produce a meaningful artifact, result, capability, procurement, or gate?
5. Would combining it with another card make the combined card confusing or unsafe?

#### 2.5.2 Merge-Trigger List

Merge a smaller card into its parent task when the card's only significance is one of the following:

- One conductor.
- One connector.
- One solder group.
- One subset of GPIO signals.
- One intermediate continuity check.
- One test-pattern variant.
- Recording evidence from the immediately preceding card.
- Committing evidence.
- Configuration existing solely to perform the same card's test.

Safety does not automatically require another card. When safety is part of a larger task, it requires ordered steps and a named safety profile inside that larger task.

#### 2.5.3 Novice-Executability / Goal-Path Standard

For every surviving hardware or integration task, the following must be verifiable from the task and its referenced runbooks:

- **Purpose** — Why is this task being done?
- **Starting state** — What must already be true before beginning?
- **Exact action** — What precise physical or logical action is performed?
- **Interfaces** — What connectors, pins, protocols, or APIs are touched?
- **Order** — What must happen before, during, and after this step?
- **Power state** — Is power applied, removed, or monitored?
- **Pre-power verification** — What must be checked before energizing?
- **Success state** — What observable state confirms completion?
- **Failure handling** — What is the safe recovery if the step fails?
- **Hidden knowledge** — What unstated electrical, wiring, firmware, or tool knowledge is required?
- **Next capability unlocked** — What downstream task becomes possible after this one?

---

## 3. The 5-Level Factual Validation Hierarchy

When writing quantitative values, pin assignments, electrical tolerances, component ratings, or architectural rules, authors and AI agents must follow this strict 5-level hierarchy:

```
Level 1: PROJECT.md / BOM.md accepted specification
         ↓ (if unspecified)
Level 2: Actual received-hardware evidence (measurements, silkscreen, photos)
         ↓ (if unreceived)
Level 3: Manufacturer documentation cited in repository
         ↓ (if uncited)
Level 4: Accepted ADR (in docs/DECISIONS.md)
         ↓ (if unaccepted)
Level 5: Otherwise → UNKNOWN
```

### Critical Validation Rule
**An agent must NEVER convert an `UNKNOWN` into an invented or plausible value.**  
If a parameter (e.g., exact panel driver IC, controller UART pinout, C14 tab layout) is not grounded at Levels 1–4, mark it as `UNKNOWN`, define an explicit `VERIFY ON RECEIPT` step, or create a dedicated verification task.

---

## 4. Superseded Task Handling

If a task is replaced, obsoleted, or superseded during planning or post-decision sweeps:
1. Do **NOT** delete the task header or assign its ID to another task.
2. Replace the task body with the standard superseded notice:

```markdown
### AF-XXX — SUPERSEDED

Replaced by: AF-YYY, AF-ZZZ

(Do not export to Jira)
```

3. Update dependent tasks to point to the replacement task ID(s).

---

## 5. Examples of Well-Formed Tasks

### Example 1: Hardware & Safety Task

```markdown
### AF-EXAMPLE-1 — Verify C14 Live and Neutral wiring insulation and continuity

**Milestone:** M0
**Depends on:** —
**Labels:** hardware, safety-review, power
**Safety:** MAINS
**Stop condition:** Disconnect wall plug immediately if any terminal is loose or uninsulated.
**Resolves:** U-001
**Procedure:** EXP-002
**Wiring:** hardware/schematics/PROTOTYPE_WIRING.md §3

#### Do
1. Disconnect C13 mains power cable from wall socket.
2. Inspect rear tabs of C14 module to verify 6.3 mm insulated female spades are fully seated on L and N tabs.
3. Verify heat-shrink insulation fully covers the crimp barrels with no exposed copper strands.
4. Set digital multimeter to continuity / resistance mode.
5. Measure continuity from C14 inlet L pin to PSU L terminal (verify continuity when switch is ON, open circuit when switch is OFF) and record measured resistance.
6. Measure continuity from C14 inlet N pin to PSU N terminal (verify continuity when switch is ON, open circuit when switch is OFF) and record measured resistance.
7. Verify open circuit (isolation) between L and N lines with switch in OFF position.

#### Done when
- Multimeter confirms direct continuity on L and N lines with switch in ON position, and measured resistance is recorded.
- Multimeter confirms open circuit on L and N lines when switch is in OFF position.
- Multimeter confirms open circuit (isolation) between L and N lines.
- Visual inspection confirms zero bare conductor visible at all spade and ferrule terminations.

#### If it fails
De-energize completely. Re-crimp spade terminals with fresh heat-shrink. Do not plug into wall until continuity passes.
```

### Example 2: Software / Application Task

```markdown
### AF-EXAMPLE-2 — Implement Pillow framebuffer text renderer on Nano

**Milestone:** M1
**Depends on:** AF-EXAMPLE-1
**Labels:** software, nano, validation

#### Do
1. Implement `render_text(canvas, text, font, pos, color)` function in `software/app/framebuffer.py`.
2. Write unit tests in `software/tests/test_framebuffer.py` validating 256×192 RGB888 byte array output.
3. Run test suite using `pytest`.

#### Done when
- `pytest software/tests/test_framebuffer.py` passes with 100% assertions green.
- Arbitrary ASCII text renders into expected RGB888 byte array buffer without memory leaks.

#### If it fails
Check Python Pillow version and font file availability. Verify RGB channel ordering (RGB888 vs BGR888).
```

### Example 3: Conditional Architecture Task

```markdown
### AF-EXAMPLE-3 — Assemble SN74HCT245 logic level buffer prototype on perfboard

**Milestone:** M1
**Depends on:** AF-EXAMPLE-2
**Labels:** hardware, controller-esp32, conditional
**Applies if:** ADR-016 selects ESP32
**Safety:** HUB75
**Stop condition:** Disconnect 5V bench power before making any wiring changes.
**Resolves:** U-012
**Procedure:** EXP-011
**Wiring:** hardware/schematics/PIN_LEVEL_APPENDIX.md §3, §4, §6

#### Do
1. Place and solder 2× SN74HCT245N ICs and 2×8 keyed box header onto 5×7 cm prototype perfboard per `hardware/schematics/PIN_LEVEL_APPENDIX.md`.
2. Solder 100 nF ceramic decoupling capacitor directly across pin 20 (VCC) and pin 10 (GND) of each SN74HCT245N.
3. Wire DIR (pin 1) to +5 V and /OE (pin 19) to GND on both ICs.
4. Wire ESP32 GPIO inputs to HCT245 A-side inputs and HCT245 B-side outputs to 2×8 keyed box header per PIN_LEVEL_APPENDIX §3, §4, §6.
5. Continuity-test all signal paths and verify power-to-ground isolation before applying power.

#### Done when
- Multimeter confirms zero short circuits between +5 V rail and GND rail.
- Continuity verified and recorded on all 14 HUB75 signal lines from ESP32 pins through HCT245 outputs to 2×8 IDC header pins.
- Decoupling capacitors and power/ground connections verified per schematic.

#### If it fails
Inspect solder joints for bridges using magnifying glass. Reflow suspicious joints before applying power.
```
