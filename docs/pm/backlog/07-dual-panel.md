# Phase 07 — Dual-Panel 256×64 Logical Canvas (M2)

Covers chaining two 128×64 P2 panels horizontally to form a 256×64 canvas. Validates seam crossing, display ordering, Standard Test Pattern Suite execution, and Defect Checklist validation for both HD-WF4 (EXP-005) and ESP32-S3 (EXP-012).

---

### AF-081 — Verify panel #2 polarity and HUB75 orientation unpowered

**Milestone:** M2
**Depends on:** AF-080
**Labels:** hardware polarity-verify validation safety-review
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Keep panel and controller de-energized and stop immediately if polarity, IN/OUT, key, or orientation markings are missing or contradictory.

#### Do
1. De-energize the bench before handling panel #2.
2. Record the panel #2 power-connector polarity markings and HUB75 pin-1/key plus IN/OUT markings from the delivered hardware.
3. Compare the panel’s physical top/orientation marking with panel #1 and photograph the evidence.

#### Done when
- Panel #2 power polarity is recorded from its physical markings with no unresolved contradiction.
- HUB75 pin-1/key orientation and IN/OUT are identified from the panel.
- Panel #2 physical orientation is recorded relative to panel #1, with a dated photo.

#### If it fails
Keep all power disconnected. Do not connect the harness or ribbon cable; isolate the ambiguous connector or orientation and escalate for receipt/manufacturer evidence.

### AF-082 — Verify panel #2 power connector and harness branch polarity unpowered

**Milestone:** M2
**Depends on:** AF-081
**Labels:** hardware power polarity-verify validation safety-review
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Keep the PSU disconnected and the panel #2 power connector unmated; before any continuity or short check, electrically isolate the panel #2 branch at both ends from the PSU DC terminals and any parallel branches; stop on any polarity mismatch, cross-pair continuity, damaged connector, or exposed conductor.

#### Do
1. De-energize the bench and disconnect the PSU from the wall before probing; keep the panel #2 power connector unmated.
2. Identify the power harness branch intended for panel #2 without assuming a branch number.
3. Electrically isolate the panel #2 branch at both ends from the PSU DC terminals and any parallel branches before continuity or short checks.
4. Using the panel #2 markings from AF-081, verify the branch’s positive and ground conductors map to the corresponding panel connector contacts and record the continuity results.
5. Check for an unintended positive-to-ground short on the isolated branch and inspect the connector/crimps.

#### Done when
- Matching positive and ground paths are recorded as continuous.
- Cross-polarity and unintended positive-to-ground checks show no continuity.
- The panel #2 branch was electrically isolated at both ends from the PSU DC terminals and any parallel branches for the checks, with the panel power connector unmated.
- The branch identity and evidence are recorded without inventing a branch count or electrical threshold.

#### If it fails
Leave the PSU disconnected and the panel power connector unmated. Quarantine the branch, connector, or crimp, correct or replace it using BOM-specified wiring, and repeat AF-082 before any energization.

### AF-083 — Wire WF4 two-panel row with panel 1 OUT to panel 2 IN

**Milestone:** M2
**Depends on:** AF-082, AF-042
**Labels:** hardware controller-wf4 power validation safety-review
**Safety:** HUB75
**Stop condition:** De-energize panel and controller power before every HUB75 or panel-power connection/disconnection; never hot-plug.
**Procedure:** EXP-005

#### Do
1. De-energize the WF4, both panels, and the panel power source.
2. Connect the WF4 output to panel #1 HUB75 IN, then connect panel #1 HUB75 OUT to panel #2 HUB75 IN using the verified orientations.
3. Connect panel #1 and panel #2 to separate parallel power branches using the verified connectors; do not daisy-chain panel power.
4. Inspect all connections while de-energized and record the wiring photo. Energize only by the applicable documented EXP-005/runbook procedure.

#### Done when
- The documented topology is WF4 → panel #1 IN → panel #1 OUT → panel #2 IN.
- Both panels have separate parallel power branches and no panel-to-panel power daisy-chain.
- All connections were made de-energized with no hot-plug, and the wiring evidence is saved.

#### If it fails
De-energize before touching anything. Inspect keying, IN/OUT selection, connector seating, branch polarity, and parallel power routing; correct one fault at a time and repeat the relevant prerequisite check.

### AF-084 — Configure WF4 EXP-005 for a 256×64 left-to-right row

**Milestone:** M2
**Depends on:** AF-083
**Labels:** firmware controller-wf4 validation critical-path
**Safety:** HUB75
**Stop condition:** Do not touch or disconnect energized HUB75/power; de-energize before physical inspection.
**Procedure:** EXP-005

#### Do
1. Configure the WF4 logical canvas as 256×64 using the applicable documented experiment procedure.
2. Display a coordinate grid or numbered regions that distinguish panel #1 on the left from panel #2 on the right.
3. Record the configuration, panel order, and observed output with screenshots or photos.

#### Done when
- The saved configuration identifies a 256×64 logical canvas.
- Numbered regions and coordinates show panel #1 left and panel #2 right, with no duplicated or reversed half.
- Configuration and ordering evidence is recorded.

#### If it fails
Stop the experiment and preserve the output evidence. Re-check the configured canvas dimensions, chain order, and panel orientation only after de-energizing for any physical inspection.

### AF-085 — Run WF4 EXP-005 Standard Test Pattern Suite and stability record

**Milestone:** M2
**Depends on:** AF-084
**Labels:** validation controller-wf4 critical-path
**Safety:** HUB75
**Stop condition:** Do not touch or move any energized HUB75 connection; stop the run and de-energize before physical inspection if an abnormal display or reset occurs.
**Procedure:** EXP-005

#### Do
1. Run the shared Standard Test Pattern Suite in its documented order: solid fills, checkerboard, horizontal/vertical lines including vertical lines every 16 px, linear gradient, moving line or text, and coordinate grid with panel-boundary and region labels.
2. Apply every item in the shared Standard Defect Checklist to both panels and record pass/fail observations.
3. Record observed refresh information when reported, runtime, controller events/resets, and photos/logs for each pattern.
4. Run the EXP-005 stable observation for at least 30 minutes and record start/end times and artifacts.

#### Done when
- All shared suite patterns are run on the 256×64 row and pattern artifacts are saved.
- The Standard Defect Checklist passes for both panels; recording a failed checklist item is not sufficient for task completion.
- Observed refresh/runtime/controller events and photos/logs are recorded.
- The WF4 row completes the EXP-005 ≥30-minute run without a controller-caused seam or reset.

#### If it fails
Stop the run and de-energize before inspecting cables. Record the failed pattern/checklist item, then isolate configuration, panel order, HUB75 seating, and controller behavior; repeat AF-084 or AF-085 only after the cause is documented.

### AF-086 — Verify WF4 seam crossing at x=128

**Milestone:** M2
**Depends on:** AF-085, AF-046
**Labels:** validation controller-wf4 critical-path
**Safety:** HUB75
**Stop condition:** Keep energized wiring untouched; de-energize panel and controller power before any seam-related cable or orientation inspection.

#### Do
1. From the Nano, render a smooth horizontal gradient and send it through the proven WF4 programmatic path from AF-046; photograph the transition across px127→128.
2. From the Nano, render content crossing x=128, including text straddling the two panels, and send it through the same proven WF4 programmatic path; photograph the seam.
3. From the Nano, render the seam test content, including a continuous one-pixel line at x=128 from y=0 through y=63, send it through the proven path, and photograph it.
4. Save the three photos and a pass/fail log naming the logical coordinates, together with Nano input/render artifacts and transport evidence that proves the 256×64 seam content originated from the Nano.

#### Done when
- The gradient is continuous across px127→128 with no unexplained discontinuity.
- Content crosses x=128 and remains correctly mapped across the physical seam.
- The one-pixel line at x=128 is continuous, not gapped or doubled.
- Photos and the seam log are saved.
- The evidence proves that the gradient, text, and seam-test content were rendered on the Nano and sent through the proven WF4 programmatic path, with the displayed 256×64 seam content originating from the Nano under EXP-005.

#### If it fails
De-energize before changing anything. Check panel order, 256×64 chain configuration, and HUB75 seating/orientation; correct the identified cause and repeat AF-084, AF-085, or AF-086 as applicable.

### AF-087 — Prepare ESP32 EXP-012 two-panel row wiring

**Milestone:** M2
**Depends on:** AF-082, AF-067
**Labels:** hardware controller-esp32 power validation safety-review
**Safety:** HUB75
**Stop condition:** De-energize panel and controller power before every HUB75 or panel-power connection/disconnection; never hot-plug.
**Procedure:** EXP-012

#### Do
1. De-energize the ESP32, both panels, and the panel power source.
2. Connect the validated ESP32/HUB75 interface to panel #1 HUB75 IN, then panel #1 OUT to panel #2 IN.
3. Connect panel #1 and panel #2 to separate parallel power branches; do not daisy-chain panel power.
4. Inspect and photograph the complete row while de-energized. Energize only by the applicable documented EXP-012/runbook procedure.

#### Done when
- The ESP32-to-panel #1-to-panel #2 HUB75 topology is recorded with verified IN/OUT orientation.
- Both panels have separate parallel power branches and no hot-plug occurred.
- Wiring evidence is saved and the row is ready for EXP-012 configuration.

#### If it fails
De-energize before touching the rig. Inspect the interface, keying, IN/OUT selection, connector seating, branch polarity, and parallel power routing; repeat AF-081 or AF-082 when the fault is in a common prerequisite.

### AF-088 — Configure ESP32 EXP-012 for a 256×64 row and record runtime metrics

**Milestone:** M2
**Depends on:** AF-087
**Labels:** firmware controller-esp32 validation critical-path
**Safety:** HUB75
**Stop condition:** Do not touch or disconnect energized HUB75/power; de-energize before physical inspection.
**Procedure:** EXP-012

#### Do
1. Configure the ESP32 logical dimensions as 256×64 with the two-panel row ordering required by EXP-012.
2. Exercise the documented static images, scrolling content, and changing full-screen colors.
3. Exercise supported brightness, color-depth, and buffer variations relevant to EXP-012; for each variation, record exactly what was tested, the observed display effects, reported refresh rate, and available runtime metrics such as PSRAM/DMA status.
4. Save the configuration and runtime logs, including the exact tested variations, observed results, and available metrics without inventing a finite set or threshold.

#### Done when
- The firmware configuration and runtime log confirm a 256×64 two-panel row.
- Left-to-right panel ordering is verified with numbered regions or coordinates.
- Supported brightness, color-depth, and buffer variations exercised for EXP-012 are listed with the exact tested settings, observed effects, reported refresh rate, and available PSRAM/DMA/runtime metrics; no untested variation is implied and no threshold is invented.

#### If it fails
Stop the run and preserve compile/boot logs. De-energize before inspecting HUB75 wiring; isolate configuration, allocation, panel-order, and firmware faults and document the next corrective experiment.

### AF-089 — Run ESP32 EXP-012 Standard Test Pattern Suite and stability record

**Milestone:** M2
**Depends on:** AF-088
**Labels:** validation controller-esp32 critical-path
**Safety:** HUB75
**Stop condition:** Do not touch or move any energized HUB75 connection; stop the run and de-energize before physical inspection if an abnormal display or reset occurs.
**Procedure:** EXP-012

#### Do
1. Run the shared Standard Test Pattern Suite in its documented order: solid fills, checkerboard, horizontal/vertical lines including vertical lines every 16 px across the 256×64 row, linear gradient, moving line or text, and coordinate grid with panel-boundary and region labels.
2. Apply the shared Standard Defect Checklist to both panels and record pass/fail observations.
3. Record observed refresh rate, runtime metrics, controller events/resets, and photos/logs for each pattern and the dynamic tests.
4. Run the EXP-012 stability observation for at least 1 hour and record start/end times, runtime metrics, and artifacts.

#### Done when
- All shared suite patterns and EXP-012 dynamic tests are run on the 256×64 row with evidence saved.
- The Standard Defect Checklist passes for both panels; recording a failed checklist item is not sufficient for task completion.
- Refresh/runtime metrics and stability logs are recorded for the full run.
- The ESP32 completes the EXP-012 ≥1-hour run without regular crashes/resets or visible instability.

#### If it fails
Stop the run and de-energize before inspecting cables. Preserve serial/runtime logs, identify the failed pattern or metric, and isolate firmware configuration, allocation, panel order, HUB75 seating, or power routing before repeating the relevant task.

### AF-090 — Verify ESP32 seam crossing at x=128

**Milestone:** M2
**Depends on:** AF-089, AF-069
**Labels:** validation controller-esp32 critical-path
**Safety:** HUB75
**Stop condition:** Keep energized wiring untouched; de-energize panel and controller power before any seam-related cable or orientation inspection.

#### Do
1. From the Nano, render a smooth horizontal gradient and send it through the proven Nano→ESP32 transport from AF-069; photograph the transition across px127→128.
2. From the Nano, render content crossing x=128, including text straddling the two panels, and send it through the same proven Nano→ESP32 transport; photograph the seam.
3. From the Nano, render the seam test content, including a continuous one-pixel line at x=128 from y=0 through y=63, send it through the proven transport, and photograph it.
4. Save the three photos and a pass/fail log naming the logical coordinates, together with Nano input/render artifacts and transport evidence that proves the 256×64 seam content originated from the Nano.

#### Done when
- The gradient is continuous across px127→128 with no unexplained discontinuity.
- Content crosses x=128 and remains correctly mapped across the physical seam.
- The one-pixel line at x=128 is continuous, not gapped or doubled.
- Photos and the seam log are saved.
- The evidence proves that the gradient, text, and seam-test content were rendered on the Nano and sent through the proven Nano→ESP32 transport, with the displayed 256×64 seam content originating from the Nano under EXP-012.

#### If it fails
De-energize before changing anything. Check panel order, 256×64 chain configuration, and HUB75 seating/orientation; correct the identified cause and repeat AF-088, AF-089, or AF-090 as applicable.

### AF-091 — Validate M2 dual-panel gate for at least one controller path

**Milestone:** M2
**Depends on:** AF-086 OR AF-090
**Labels:** validation decision critical-path
**Context:** This is the M2 gate defined by the [backlog README](README.md) and evaluated against both [EXP-005](../../EXPERIMENTS.md#exp-005--hd-wf4--25664-row) for the WF4 branch and [EXP-012](../../EXPERIMENTS.md#exp-012--esp32-s3--25664-row) for the ESP32 branch; each branch is evaluated against its corresponding experiment, and either complete passing path satisfies M2.

#### Do
1. Review the WF4 evidence from AF-081 through AF-086 and the ESP32 evidence from AF-081, AF-082, and AF-087 through AF-090.
2. For the WF4 path, record pass/fail against the M2 criteria using AF-085/AF-086 and EXP-005 evidence: two chained panels forming 256×64, independent parallel panel power, correct left-to-right order, content crossing the physical seam, Standard Test Pattern Suite and Standard Defect Checklist results for both panels, and at least 30 minutes of stable runtime. The passing WF4 candidate must also show Nano-rendered 256×64 content crossing x=128 sent through the proven WF4 programmatic path, with linked Nano-origin, render, transport, and display evidence.
3. For the ESP32 path, record pass/fail against the M2 criteria using AF-089/AF-090 and EXP-012 evidence: two chained panels forming 256×64, independent parallel panel power, correct left-to-right order, content crossing the physical seam, Standard Test Pattern Suite and Standard Defect Checklist results for both panels, and at least 1 hour of stable runtime. The passing ESP32 candidate must also show Nano-rendered 256×64 content crossing x=128 sent through the proven Nano→ESP32 transport, with linked Nano-origin, render, transport, and display evidence.
4. Record each path’s result and link it to its configuration, photos, pattern/checklist log, runtime record, and seam evidence. Set the M2 gate outcome to PASS when either WF4 AF-086 or ESP32 AF-090 has a complete passing evidence set; do not require both paths to pass. Otherwise record FAIL and identify the missing or failed criterion.

#### Done when
- At least one controller path, WF4 via AF-086 or ESP32 via AF-090, is explicitly recorded as passing the M2 256×64 dual-panel criteria, including Nano-controlled output with content crossing x=128 and its branch-specific stability duration (WF4 ≥30 minutes; ESP32 ≥1 hour).
- The gate record names the passing path or paths and links the supporting AF-081–090 evidence and source criteria.
- The M2 outcome is explicitly recorded as PASS or FAIL: PASS when either AF-086 or AF-090 passes, with no advancement claimed when neither path passes.

#### If it fails
Record the failed criterion for each path, preserve the evidence, and return to the corresponding source task for corrective work. Re-run AF-091 only after the affected path has new complete evidence.

### AF-092 — Consolidate M2 subtrack evidence and outcome inputs

**Milestone:** M2
**Depends on:** AF-091
**Labels:** docs validation decision critical-path
**Context:** This aggregator records the M2 outcome and the evidence inputs needed by the subsequent architecture decision, using the [backlog README](README.md) M2 gate, [JIRA.md](../../JIRA.md) Milestone 2, and [EXP-005/EXP-012](../../EXPERIMENTS.md) source procedures.

#### Do
1. Inventory the WF4 subtrack evidence from AF-081 through AF-086 and the ESP32 subtrack evidence from AF-081, AF-082, and AF-087 through AF-090, including configuration, wiring, pattern/checklist, runtime, and seam records.
2. Record one subtrack row for WF4 and one for ESP32 with status, tested configuration or variations, observed results, stability record, failed criteria if any, and evidence links.
3. Record the AF-091 M2 outcome, the passing controller path or paths, and the exact evidence inputs available for the architecture decision; identify missing inputs instead of inferring them.
4. Save the consolidated summary at `docs/pm/evidence/AF-092-m2-summary.md`.

#### Done when
- WF4 and ESP32 subtrack rows account for all applicable AF-081–090 evidence and identify each path’s result.
- The consolidated record includes the AF-091 outcome, the passing path or paths, links to their evidence, and a linked result or explicit missing-input record for the other candidate path, including non-passing or incomplete status when applicable; ADR-016 architecture selection happens later.
- The summary includes tested settings/results and missing-input notes without changing or silently filling source measurements.
- The record is sufficient to hand M2 evidence to the architecture decision without changing or silently filling source measurements.

#### If it fails
Do not close M2 evidence. Return to the affected source task, restore the missing artifact or measurement, and update the subtrack row and outcome inputs before repeating AF-092.
