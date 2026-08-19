# Phase 09 — Quad-Panel 2×2 Prototype (M3)

Covers the gated four-panel 256×128 prototype on the architecture selected by ADR-016. The M3 path validates two 256×64 rows, both physical seam types, Nano-originated content, row-update behavior, coordinate mapping, and sustained stability under EXP-017.

**AF-096 post-decision normalization for AF-098, AF-099, AF-100, AF-102, and AF-103:** These tasks retain both pre-decision branch definitions. Before ADR-016, each registry entry carries the labels `conditional blocked blocked:adr-016` plus its applicable controller label, and uses `Applies if` for its branch condition. After AF-096, the executor reads ADR-016 and substitutes the actual winner. For the winner, remove the labels `conditional blocked blocked:adr-016` and remove `Applies if`; add `critical-path` and retain the winning controller label. For the loser, retain or add `conditional blocked blocked:adr-016`, remove `critical-path` if present, and replace `Applies if` with the exact resolved line `Skip — ADR-016 selected WF4` when multi-ESP32 loses, or `Skip — ADR-016 selected multi-ESP32` when WF4 loses. The executor substitutes the actual winner; no `[other controller]` placeholder may remain. Only the selected branch executes after AF-096; do not claim a winner in this pre-decision file.

### AF-097 — Verify panels #3 and #4 unpowered

**Milestone:** M3
**Depends on:** AF-096
**Labels:** hardware polarity-verify validation safety-review
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Keep the PSU, panels, and controller de-energized and stop if any polarity, HUB75 IN/OUT, keying, or orientation marking is missing or contradictory.

#### Do
1. Retrieve panels #3 and #4 and keep all panel and controller power disconnected.
2. Record each panel’s physical V+/V− markings, HUB75 IN/OUT and key orientation, and top-edge orientation against the received-hardware evidence.
3. Photograph both panels and save the records under `docs/pm/evidence/AF-097-panels3-4-reverify.md` and `hardware/photos/AF-097-panel3-reverify.jpg`, `hardware/photos/AF-097-panel4-reverify.jpg`.

#### Done when
- Panels #3 and #4 each have recorded polarity, HUB75 orientation, and physical orientation with no unresolved contradiction.
- Evidence identifies both panels and is linked to the received-hardware baseline.

#### If it fails
Keep everything disconnected, quarantine the ambiguous panel or connector, and resolve the discrepancy from received-hardware or manufacturer evidence before AF-098, AF-099, or any energization.

### AF-098 — Assemble WF4 2×2 topology and parallel power branches

**Milestone:** M3
**Depends on:** AF-097
**Labels:** hardware controller-wf4 power validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects WF4; this task supplies the selected controller’s two-row topology.
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** De-energize the PSU, controller, and panels before any HUB75 or panel-power connection change; never hot-plug.
**Procedure:** EXP-017

#### Do
1. With the bench de-energized, place panels #1/#2 as the top row and #3/#4 directly below them.
2. Connect WF4 X1 → panel #1 IN → panel #2 IN and WF4 X2 → panel #3 IN → panel #4 IN, using the verified orientations.
3. Connect four separate panel power branches in parallel to the PSU, one branch per panel; do not daisy-chain panel power.
4. Photograph the complete topology and record branch identities, connector checks, and any measured observations at `docs/pm/evidence/AF-098-wf4-m3-topology.md`.

#### Done when
- The documented topology is X1 top row and X2 bottom row, each row chained left to right.
- All four panels have separate parallel power branches and no panel-power daisy-chain.
- Wiring and branch evidence is complete before AF-102.

#### If it fails
De-energize fully before inspection. Isolate the affected ribbon, branch, connector, or orientation issue and repeat AF-097 or the relevant wiring check before continuing.

### AF-099 — Prepare the second ESP32 controller path

**Milestone:** M3
**Depends on:** AF-097
**Labels:** hardware firmware controller-esp32 validation conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32; this task prepares the second row controller without assuming that a second controller has been purchased.

#### Do
1. Audit the delivered ESP32-S3 and HCT adapter inventory and identify whether a second controller path is physically available.
2. If available, document its board identity, adapter status, firmware/configuration baseline, and intended bottom-row role; prepare the Nano transport endpoint without changing the proven first-controller path.
3. If unavailable, record `BLOCKED: second controller hardware not received/purchased` in `docs/pm/evidence/AF-099-second-controller-prep.md`; do not substitute, purchase, or silently assume hardware.

#### Done when
- A second-controller path is either prepared from identified available hardware or explicitly recorded as blocked for missing hardware.
- No unpurchased hardware, GPIO assignment, or working capability is asserted as fact.

#### If it fails
Do not wire or flash an unidentified board. Preserve the inventory and logs, and resolve the missing hardware or identity issue before AF-100.

### AF-100 — Assemble ESP32 2×2 topology

**Milestone:** M3
**Depends on:** AF-099
**Labels:** hardware controller-esp32 power validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32 and AF-099 confirms the second controller path is available; this task maps one controller to each 256×64 row.
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** De-energize controller, panels, and PSU before every HUB75 or panel-power connection change; never hot-plug.
**Procedure:** EXP-017

#### Do
1. With all relevant hardware de-energized, connect ESP32 #1 → panel #1 IN → panel #2 IN.
2. Connect the prepared ESP32 #2 → panel #3 IN → panel #4 IN.
3. Connect four separate parallel panel power branches, one per panel, and record the controller-to-row mapping.
4. Save wiring photos and the topology record under `docs/pm/evidence/AF-100-esp32-m3-topology.md`.

#### Done when
- Each controller drives one left-to-right 256×64 row and all four panels have separate power branches.
- The second controller identity and wiring are evidenced; no hot-plug occurred.

#### If it fails
De-energize before touching the rig. Return to AF-099 for an unavailable or unidentified controller, or isolate the failed row, ribbon, branch, or adapter and correct it before repeating AF-100.

### AF-101 — Extend the Nano framebuffer to 256×128 dual-row dispatch

**Milestone:** M3
**Depends on:** AF-096
**Labels:** software nano validation
**Procedure:** EXP-017

#### Do
1. Render Nano-originated RGB content into a 256×128 logical framebuffer using the existing framebuffer and transport abstraction.
2. Produce two 256×64 row crops: y=0..63 and y=64..127.
3. Dispatch both row crops through the selected architecture’s proven transport path and save source frame, crop, dispatch, and log artifacts under `docs/pm/evidence/AF-101-nano-256x128-dispatch.md`.

#### Done when
- The Nano produces a 256×128 source frame and two exact 256×64 row regions without changing the renderer’s coordinate origin.
- Dispatch records identify both row crops, their order, dimensions, and Nano origin.

#### If it fails
Keep the display unmodified and inspect framebuffer dimensions, crop bounds, row ordering, and transport payload logs. Correct the software path and repeat AF-101 before first light.

### AF-102 — Configure WF4 256×128 first light

**Milestone:** M3
**Depends on:** AF-098, AF-101
**Labels:** firmware controller-wf4 validation conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects WF4; this task validates first light on the assembled WF4 topology.
**Safety:** HUB75
**Stop condition:** Stop output and de-energize before inspecting any abnormal panel, controller, cable, or power behavior.
**Procedure:** EXP-017

#### Do
1. Apply the documented EXP-017/runbook energization procedure to the verified WF4 topology.
2. Configure the WF4 for two 256×64 rows forming a 256×128 display and record the saved configuration.
3. Send a Nano-originated 256×128 test frame through the AF-101 dispatch path and record first-light output and logs in `docs/pm/evidence/AF-102-wf4-first-light.md`.

#### Done when
- Four panels display the 256×128 Nano-originated frame with the intended row order and no unexplained blank, duplicate, or reversed region.
- Configuration, source frame, transport log, and first-light evidence are saved.

#### If it fails
Stop output and de-energize. Check configuration, row assignment, panel order, and verified connections; route hardware faults to AF-098 and software/transport faults to AF-101.

### AF-103 — Bring up ESP32 dual-controller first light

**Milestone:** M3
**Depends on:** AF-100, AF-101
**Labels:** firmware controller-esp32 validation conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32; this task validates first light on the prepared two-controller topology.
**Safety:** HUB75
**Stop condition:** Stop output and de-energize before inspecting any abnormal panel, controller, adapter, cable, or power behavior.
**Procedure:** EXP-017

#### Do
1. Apply the documented EXP-017/runbook energization procedure to the verified two-controller topology.
2. Configure each controller for its 256×64 row and record controller identities, firmware, row assignment, and transport endpoints.
3. Send both Nano-generated row crops from AF-101 and record first-light output, logs, and any available runtime observations in `docs/pm/evidence/AF-103-esp32-first-light.md`.

#### Done when
- Four panels display the 256×128 Nano-originated frame with the intended top/bottom row order and no unexplained blank, duplicate, or reversed region.
- Both controller configurations and the dual dispatch evidence are saved.

#### If it fails
Stop output and de-energize. Route a missing/identity issue to AF-099, topology faults to AF-100, and framebuffer/dispatch faults to AF-101; preserve logs before retrying.

### AF-104 — Validate EXP-017 seam crossing

**Milestone:** M3
**Depends on:** AF-096, AF-101
**Labels:** validation critical-path
**Context:** After AF-096 completes, read the accepted ADR-016 result and run this task only after the selected first-light task passes: AF-102 for WF4 or AF-103 for the applicable ESP32 row topology; skip the unselected first-light task, which is not a dependency.
**Procedure:** EXP-017

#### Do
1. From the Nano, render a gradient and one-pixel test content crossing both vertical x=128 seams: top row and bottom row.
2. Render content crossing the horizontal y=64 seam across the full width.
3. Render a single Nano-originated 256×128 frame whose content crosses both vertical seams and the horizontal seam simultaneously.
4. Save the source frame, transport evidence, full-display photos, and pass/fail observations at `docs/pm/evidence/AF-104-exp-017-seams.md`.

#### Done when
- Both vertical x=128 seams and the horizontal y=64 seam are correctly crossed without an unexplained gap, duplication, or coordinate discontinuity.
- A single Nano-originated 256×128 content frame visibly crosses all three seam locations simultaneously.
- Evidence covers both the rendered source and physical output for the selected, passing branch; the unselected branch is skipped.

#### If it fails
Stop output and de-energize before physical inspection. Isolate row/chain mapping versus crop/transport errors, correct the relevant upstream task, and repeat AF-104.

### AF-105 — Observe dual-row synchronization behavior

**Milestone:** M3
**Depends on:** AF-096, AF-101
**Labels:** validation software critical-path
**Context:** After AF-096 completes, read the accepted ADR-016 result and run this task only after the selected first-light task passes: AF-102 for WF4 or AF-103 for the applicable ESP32 row topology; skip the unselected first-light task, which is not a dependency.
**Procedure:** EXP-017

#### Do
1. Send repeated Nano-originated 256×128 frames with a visible event shared across both rows.
2. Observe and record timestamps or frame markers for the corresponding top and bottom row updates, including the observed deltas and test conditions.
3. Assess whether the observed behavior is tolerable for the dashboard use case; do not create or apply a numeric cutoff.
4. Save capture/log evidence at `docs/pm/evidence/AF-105-row-sync-observation.md`.

#### Done when
- Observed row-update deltas, method, timestamps/frame markers, and conditions are recorded.
- The record contains an explicit dashboard-tolerable or not-tolerable assessment grounded in the observation, without an invented threshold, for the selected branch; the unselected branch is skipped.

#### If it fails
Stop the observation if output becomes abnormal, preserve captures, and investigate dispatch ordering, transport behavior, or controller timing before repeating AF-105.

### AF-106 — Render Nano coordinate grid and boundary labels

**Milestone:** M3
**Depends on:** AF-104
**Labels:** validation software critical-path
**Procedure:** EXP-017

#### Do
1. Render on the Nano a 256×128 coordinate grid with corner labels for (0,0), (255,0), (0,127), and (255,127).
2. Label both vertical x=128 boundaries and the horizontal y=64 boundary, while retaining visible panel-region identifiers.
3. Display the frame through the passing architecture and photograph/check each panel corner and boundary; save the checklist at `docs/pm/evidence/AF-106-coordinate-grid.md`.

#### Done when
- All four corner labels, both x=128 seam labels, and the y=64 seam label land on the expected physical regions.
- The source frame and physical verification demonstrate Nano-originated coordinates with no row/column inversion.

#### If it fails
Stop output and de-energize before inspecting cables. Determine whether the fault is coordinate generation, crop dispatch, controller row assignment, or panel order; correct the source task and repeat AF-106.

### AF-107 — Observe EXP-017 four-panel stability for at least 30 minutes

**Milestone:** M3
**Depends on:** AF-106
**Labels:** validation stability thermal-review critical-path
**Safety:** HUB75
**Stop condition:** Stop output and de-energize if blanking, freezing, garbling, resets, smoke, smell, or abnormal heating occurs.
**Procedure:** EXP-017

#### Do
1. Run a documented EXP-017 pattern sequence for at least 30 minutes on the passing architecture.
2. Record pattern, start/end timestamps, runtime, display observations, controller events, logs, and measured Nano/controller temperatures and CPU where available.
3. Save the complete observation at `docs/pm/evidence/AF-107-exp-017-stability.md`.

#### Done when
- The recorded runtime is at least 30 minutes and the pattern and timestamps are reproducible from the evidence.
- Logs and observations show no unexplained blank, freeze, garble, or reset during the run.
- Measured temperatures and CPU data are included where available, with no invented threshold.

#### If it fails
Stop output and de-energize. Preserve logs and measurements, identify whether the fault is thermal, transport, controller, power, or software related, correct the upstream task, and repeat the stability observation.

### AF-108 — Validate the M3 EXP-017 gate

**Milestone:** M3
**Depends on:** AF-096, AF-105, AF-107
**Labels:** validation decision critical-path
**Context:** This is the M3 gate aggregator; a pass authorizes the next milestone review but does not begin M4 work.

#### Do
1. Confirm AF-096 selected and recorded the winning architecture, then review the winning-path evidence from AF-097 through AF-107.
2. Check the 2×2 topology, 256×128 Nano framebuffer/content path, both x=128 vertical seams, y=64 horizontal seam, Nano-controlled content crossing all seam types, row-sync assessment, coordinate evidence, and AF-107 stability record.
3. Confirm the complete EXP-017 evidence set is linked, mark the M3 gate PASS or FAIL, and save `docs/pm/evidence/AF-108-m3-gate.md`.
4. If PASS, record that M4 has not begun; if FAIL, identify the exact upstream task requiring correction.

#### Done when
- The winning architecture is named and all M3 gate criteria from the backlog README and JIRA are explicitly checked.
- Evidence includes 2×2/256×128, both seam types, Nano-originated content, row-sync assessment, coordinate labels, at least 30 minutes of stability, and complete EXP-017 records.
- The outcome is explicitly PASS or FAIL, and no M4 task is started by this gate.

#### If it fails
Record the failed criterion, keep M4 blocked, and return to the named source task for correction and new evidence before re-gating.
