# Phase 07 — Dual-Panel 256×64 Logical Canvas (M2)

Covers chaining two 128×64 P2 panels horizontally to form a 256×64 canvas. Validates seam crossing, display ordering, Standard Test Pattern Suite execution, and Defect Checklist validation for both HD-WF4 (EXP-005) and ESP32-S3 (EXP-012).

---

### AF-081 — Verify panel #2 polarity and HUB75 orientation unpowered

**Milestone:** M2
**Depends on:** AF-080
**Labels:** hardware power polarity-verify validation safety-review
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** Keep the PSU disconnected and the panel #2 power connector unmated. Before any continuity or short check, electrically isolate the panel #2 branch at both ends from the PSU DC terminals and any parallel branches; stop immediately if polarity, IN/OUT, key, or orientation markings are missing or contradictory, or if there is a polarity mismatch, cross-pair continuity, damaged connector, or exposed conductor.

#### Do
1. De-energize the bench, disconnect the PSU from the wall, and keep the panel #2 power connector unmated before handling or probing panel #2.
2. Record the panel #2 power-connector polarity markings and HUB75 pin-1/key plus IN/OUT markings from the delivered hardware; compare its physical top/orientation marking with panel #1 and photograph the evidence.
3. Identify the power-harness branch intended for panel #2 without assuming a branch number, then electrically isolate that branch at both ends from the PSU DC terminals and any parallel branches.
4. Using the recorded panel #2 markings, verify and record positive and ground continuity from the isolated branch to the corresponding panel connector contacts; check for cross-polarity and an unintended positive-to-ground short, then inspect the connector and crimps.

#### Done when
- Panel #2 power polarity, HUB75 pin-1/key/IN/OUT, and physical orientation relative to panel #1 are recorded from physical markings with no unresolved contradiction and a dated photo.
- Matching positive and ground paths are recorded as continuous, while cross-polarity and unintended positive-to-ground checks show no continuity.
- The panel #2 branch was isolated at both ends from PSU DC terminals and parallel branches for the checks, with the panel power connector unmated; branch identity and evidence are recorded without inventing a branch count or electrical threshold.

#### If it fails
Keep all power disconnected and the panel power connector unmated. Do not connect the harness or ribbon cable; quarantine the ambiguous panel, branch, connector, or crimp, correct or replace it using BOM-specified wiring, and resolve any marking ambiguity from receipt/manufacturer evidence before another unpowered check or energization.

---

### AF-082 — SUPERSEDED

Replaced by: AF-081

(Do not export to Jira)

---

### AF-083 — Validate WF4 256×64 dual-panel row with Nano-originated seam crossing (EXP-005)

**Milestone:** M2
**Depends on:** AF-081, AF-042, AF-046
**Labels:** hardware controller-wf4 power validation safety-review critical-path
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize panel and controller power before every HUB75 or panel-power connection/disconnection; never hot-plug. Do not touch or move energized HUB75 connections; stop the run and de-energize before physical inspection if an abnormal display or reset occurs.
**Procedure:** EXP-005

#### Do
1. De-energize the WF4, both panels, and the panel power source. Connect WF4 to panel #1 HUB75 IN, then panel #1 HUB75 OUT to panel #2 HUB75 IN using the verified orientations; connect each panel to a separate parallel power branch and do not daisy-chain panel power. Inspect and photograph the complete row while de-energized.
2. Energize only by the applicable documented EXP-005/runbook procedure. Configure the WF4 logical canvas as 256×64, then display a coordinate grid or numbered regions proving panel #1 is left and panel #2 is right; save configuration and ordering evidence.
3. Run the shared Standard Test Pattern Suite in its documented order: solid fills, checkerboard, horizontal/vertical lines including vertical lines every 16 px, linear gradient, moving line or text, and coordinate grid with panel-boundary and region labels. Apply every shared Standard Defect Checklist item to both panels; record observed refresh information when reported, runtime, controller events/resets, and photos/logs for each pattern.
4. Run the EXP-005 stability observation for at least 30 minutes, recording start/end times and artifacts. From the Nano, send through the proven WF4 programmatic path a smooth horizontal gradient, text straddling x=128, and a continuous one-pixel line at x=128 from y=0 through y=63; photograph each seam result and save the logical-coordinate pass/fail log with Nano input/render and transport evidence.

#### Done when
- The documented topology is WF4 → panel #1 IN → panel #1 OUT → panel #2 IN; both panels have separate parallel power branches, no panel-to-panel power daisy-chain, and all connections were made de-energized with no hot-plug.
- Saved configuration, numbered regions, and coordinates prove a 256×64 logical canvas with panel #1 left and panel #2 right, with no duplicated or reversed half.
- All shared suite patterns are run, all Standard Defect Checklist items pass for both panels, and refresh/runtime/controller observations plus pattern artifacts are saved.
- The WF4 row completes the EXP-005 ≥30-minute run without a controller-caused seam or reset.
- The gradient is continuous across px127→128, text/content crosses x=128 correctly, and the one-pixel x=128 line is continuous without a gap or double. Photos, the seam log, and Nano-origin/render/transport evidence prove the displayed 256×64 seam content originated from the Nano under EXP-005.

#### If it fails
Stop the run and de-energize before inspecting cables. Preserve the failed configuration, pattern/checklist item, seam photo, logs, and controller state; isolate panel order, 256×64 configuration, HUB75 keying/seating/orientation, branch polarity/routing, controller behavior, or the proven Nano transport path before correcting one documented cause and repeating this card.

---

### AF-084 — SUPERSEDED

Replaced by: AF-083

(Do not export to Jira)

---

### AF-085 — SUPERSEDED

Replaced by: AF-083

(Do not export to Jira)

---

### AF-086 — SUPERSEDED

Replaced by: AF-083

(Do not export to Jira)

---

### AF-087 — Validate ESP32 256×64 dual-panel row with Nano-originated seam crossing (EXP-012)

**Milestone:** M2
**Depends on:** AF-081, AF-067, AF-069
**Labels:** hardware firmware controller-esp32 power validation safety-review critical-path
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize panel and controller power before every HUB75 or panel-power connection/disconnection; never hot-plug. Do not touch or move energized HUB75 connections; stop the run and de-energize before physical inspection if an abnormal display or reset occurs.
**Procedure:** EXP-012

#### Do
1. De-energize the ESP32, both panels, and the panel power source. Connect the validated ESP32/HUB75 interface to panel #1 HUB75 IN, then panel #1 OUT to panel #2 IN using verified orientation; connect each panel to a separate parallel power branch and do not daisy-chain panel power. Inspect and photograph the complete row while de-energized.
2. Energize only by the applicable documented EXP-012/runbook procedure. Configure the ESP32 logical dimensions as a 256×64 two-panel row, prove left-to-right panel ordering with numbered regions or coordinates, and exercise documented static images, scrolling content, and changing full-screen colors.
3. Exercise supported brightness, color-depth, and buffer variations relevant to EXP-012. For each variation, record exactly what was tested, observed display effects, reported refresh rate, and available PSRAM/DMA/runtime metrics; save configuration and runtime logs without inventing a finite set or threshold.
4. Run the shared Standard Test Pattern Suite in its documented order: solid fills, checkerboard, horizontal/vertical lines including vertical lines every 16 px across the 256×64 row, linear gradient, moving line or text, and coordinate grid with panel-boundary and region labels. Apply the shared Standard Defect Checklist to both panels and record controller events/resets, photos/logs, and dynamic-test observations.
5. Run the EXP-012 stability observation for at least 1 hour, recording start/end times, runtime metrics, and artifacts. From the Nano, send through the proven Nano→ESP32 transport a smooth horizontal gradient, text straddling x=128, and a continuous one-pixel line at x=128 from y=0 through y=63; photograph each seam result and save the logical-coordinate pass/fail log with Nano input/render and transport evidence.

#### Done when
- The ESP32-to-panel #1-to-panel #2 HUB75 topology, verified IN/OUT orientation, separate parallel panel-power branches, and de-energized wiring evidence are recorded; no hot-plug occurred.
- Firmware configuration and runtime logs confirm a 256×64 row with verified left-to-right order. Supported brightness, color-depth, and buffer variations list exact tested settings, observed effects, reported refresh rate, and available PSRAM/DMA/runtime metrics; no untested variation or threshold is implied.
- All shared suite patterns and EXP-012 dynamic tests are run, all Standard Defect Checklist items pass for both panels, and refresh/runtime metrics, controller events, photos, and logs are recorded.
- The ESP32 completes the EXP-012 ≥1-hour run without regular crashes/resets or visible instability.
- The gradient is continuous across px127→128, text/content crosses x=128 correctly, and the one-pixel x=128 line is continuous without a gap or double. Photos, the seam log, and Nano-origin/render/transport evidence prove the displayed 256×64 seam content originated from the Nano under EXP-012.

#### If it fails
Stop the run and de-energize before inspecting cables. Preserve compile/boot/serial/runtime logs, failed pattern or metric, configuration, and seam evidence; isolate configuration, allocation, panel order, HUB75 keying/seating/orientation, branch polarity/routing, firmware, or Nano transport faults before documenting the next corrective experiment and repeating this card.

---

### AF-088 — SUPERSEDED

Replaced by: AF-087

(Do not export to Jira)

---

### AF-089 — SUPERSEDED

Replaced by: AF-087

(Do not export to Jira)

---

### AF-090 — SUPERSEDED

Replaced by: AF-087

(Do not export to Jira)

---

### AF-091 — Validate M2 dual-panel gate and consolidate subtrack evidence

**Milestone:** M2
**Depends on:** AF-083 OR AF-087
**Labels:** docs validation decision critical-path
**Context:** This is the M2 gate defined by the [backlog README](README.md), evaluated against [EXP-005](../../EXPERIMENTS.md#exp-005--hd-wf4--25664-row) for the WF4 path and [EXP-012](../../EXPERIMENTS.md#exp-012--esp32-s3--25664-row) for the ESP32 path. Either complete passing path satisfies M2; ADR-016 architecture selection happens later.

#### Do
1. Review the AF-083 WF4 and AF-087 ESP32 evidence. For each path, record status, tested configuration or variations, observed results, stability record, failed criteria if any, and links to wiring, configuration, pattern/checklist, runtime, seam, Nano-origin, render, transport, and display artifacts.
2. Check each path against the M2 criteria: two chained panels forming 256×64, independent parallel panel power, correct left-to-right order, content crossing the physical seam, Standard Test Pattern Suite and Standard Defect Checklist results for both panels, Nano-controlled output crossing x=128, and branch-specific stability duration (WF4 ≥30 minutes; ESP32 ≥1 hour).
3. Set the M2 outcome to PASS when either AF-083 or AF-087 has a complete passing evidence set; do not require both paths to pass. Otherwise record FAIL and identify each missing or failed criterion without inferring a result.
4. Save the two subtrack rows, M2 outcome, passing path or paths, source criteria, exact MG inputs, and explicit missing-input notes at `docs/pm/evidence/AF-091-m2-summary.md`.

#### Done when
- At least one controller path is explicitly recorded as passing all M2 256×64 criteria, including Nano-controlled content crossing x=128 and its branch-specific stability duration.
- The gate record names the passing path or paths and links the complete AF-083/AF-087 evidence; the other path has a linked result or explicit incomplete/non-passing/missing-input record.
- The consolidated summary contains both subtrack rows, tested settings/results, the explicit PASS or FAIL outcome, and architecture-decision inputs without changing or silently filling source measurements.

#### If it fails
Do not close M2 evidence or advance to MG. Record the failed criterion for each affected path, preserve the evidence, return to AF-083 or AF-087 for corrective work, restore the missing artifact or measurement, and rerun this gate only after the path has new complete evidence.

---

### AF-092 — SUPERSEDED

Replaced by: AF-091

(Do not export to Jira)
