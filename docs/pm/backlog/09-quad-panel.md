# Phase 09 — Quad-Panel 2×2 Prototype (M3)

Covers the gated four-panel 256×128 prototype on the architecture selected by ADR-016. The M3 path validates two 256×64 rows, both physical seam types, Nano-originated content, row-update behavior, coordinate mapping, and sustained stability under EXP-017.

**AF-096 post-decision normalization for surviving M3 conditional entries AF-098, AF-099, AF-187, and AF-100:** The AF-096 sweep is registry-wide: it walks every M3+ controller-specific or ADR-016-conditional entry that exists at execution time; these are this file's current known M3 targets. Before ADR-016, each entry carries `conditional blocked blocked:adr-016` plus its applicable controller label and uses `Applies if` for its branch condition. After AF-096, the executor reads ADR-016 and substitutes the actual winner. For the winner, remove `conditional blocked blocked:adr-016` and `Applies if`, add `critical-path`, and retain the winning controller label. For the loser, retain or add `conditional blocked blocked:adr-016`, remove `critical-path` if present, and replace `Applies if` with the exact resolved line `Skip — ADR-016 selected WF4` when multi-ESP32 loses, or `Skip — ADR-016 selected multi-ESP32` when WF4 loses. The executor substitutes the actual winner; no `[other controller]` placeholder may remain. Only the selected branch executes after AF-096; this pre-decision file does not claim a winner.

### AF-097 — Verify panels #3 and #4 unpowered

**Milestone:** M3
**Depends on:** AF-096
**Labels:** hardware polarity-verify validation safety-review
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Keep the PSU, panels, and controller de-energized and stop if any polarity, HUB75 IN/OUT, keying, or orientation marking is missing or contradictory.

#### Do
1. Retrieve panels #3 and #4 and keep all panel and controller power disconnected.
2. Record each panel's physical V+/V- markings, HUB75 IN/OUT and key orientation, and top-edge orientation against the received-hardware evidence.
3. Photograph both panels and save the records under `docs/pm/evidence/AF-097-panels3-4-reverify.md` and `hardware/photos/AF-097-panel3-reverify.jpg`, `hardware/photos/AF-097-panel4-reverify.jpg`.

#### Done when
- Panels #3 and #4 each have recorded polarity, HUB75 orientation, and physical orientation with no unresolved contradiction.
- Evidence identifies both panels and is linked to the received-hardware baseline.

#### If it fails
Keep everything disconnected, quarantine the ambiguous panel or connector, and resolve the discrepancy from received-hardware or manufacturer evidence before topology assembly or any energization.

### AF-098 — Assemble WF4 2×2 topology and achieve 256×128 first light (EXP-017)

**Milestone:** M3
**Depends on:** AF-097, AF-101
**Labels:** hardware firmware controller-wf4 power validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects WF4; this task supplies the selected controller's two-row topology and first light.
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize the PSU, controller, and panels before every HUB75 or panel-power connection change; never hot-plug. Stop output and de-energize before inspecting any abnormal panel, controller, cable, or power behavior.
**Procedure:** EXP-017

#### Do
1. With the bench de-energized, place panels #1/#2 as the top row and #3/#4 directly below them. Connect WF4 X1 -> panel #1 IN and panel #1 OUT -> panel #2 IN; connect WF4 X2 -> panel #3 IN and panel #3 OUT -> panel #4 IN, using the verified orientations.
2. Connect four separate panel-power branches in parallel to the PSU, one branch per panel; do not daisy-chain panel power. Photograph the topology and record branch identities and connector checks in `docs/pm/evidence/AF-098-wf4-m3-topology.md`.
3. Energize only through the documented EXP-017/runbook procedure. Configure WF4 for two 256×64 rows forming 256×128, send the AF-101 Nano-originated frame, and save configuration, first-light photos, and transport logs in `docs/pm/evidence/AF-098-wf4-first-light.md`.

#### Done when
- The documented topology is X1 top row and X2 bottom row, each chained left to right through the first panel's OUT into the next panel's IN; all four panels use separate parallel power branches.
- Four panels display the Nano-originated 256×128 frame with intended row order and no unexplained blank, duplicate, or reversed region.
- Wiring, configuration, source frame, transport log, and first-light evidence are saved before AF-104.

#### If it fails
Stop output and de-energize fully. Isolate the ribbon, branch, connector, orientation, configuration, or dispatch fault; correct the named source task and repeat the affected check before continuing.

### AF-099 — Audit second ESP32 controller-path readiness

**Milestone:** M3
**Depends on:** AF-097
**Labels:** hardware firmware controller-esp32 validation conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32; this task audits second-controller hardware readiness without assuming that a second controller has been purchased.

#### Do
1. Audit the delivered ESP32-S3 and HCT adapter inventory and identify whether a second controller path is physically available.
2. If suitable hardware is available, record `READY` in `docs/pm/evidence/AF-099-second-controller-readiness.md` with the identified board identity and adapter status.
3. If unavailable, record `MISSING` in the same evidence file with the exact ESP32-S3 board matching the proven first controller and any missing HCT adapter components per BOM; do not substitute or silently assume hardware.

#### Done when
- The evidence record states exactly one outcome: `READY` with identified hardware or `MISSING` with the exact required items.
- No unpurchased hardware, GPIO assignment, or working capability is asserted as fact, and this audit does not wait for procurement.

#### If it fails
Do not record an ambiguous outcome. Resolve the inventory discrepancy from received-hardware or manufacturer evidence before AF-187.

### AF-187 — Resolve second ESP32 controller-path hardware readiness

**Milestone:** M3
**Depends on:** AF-099
**Labels:** hardware controller-esp32 purchasing conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32; this task resolves AF-099 into verified second-controller hardware before assembly.

#### Do
1. Read AF-099's `READY` or `MISSING` outcome.
2. For `READY`, while de-energized verify the identified second controller and adapter against BOM and received-hardware evidence; document board identity, adapter status, firmware/configuration baseline, and intended bottom-row role in `docs/pm/evidence/AF-187-second-controller-hardware.md`.
3. For `MISSING`, order the exact recorded items under ADR-013. Record order references, items, and costs; on receipt, verify board and adapter identity and condition while de-energized, photograph them, update inventory, and complete the step-2 record. Do not wire, flash, or energize in this task.

#### Done when
- AF-099's outcome is resolved into verified, documented second-controller hardware: verified in place for `READY`, or ordered, received, and verified for `MISSING`.
- The evidence records board identity, adapter status, firmware/configuration baseline, intended bottom-row role, and purchase/receipt evidence where applicable.
- No unpurchased or unidentified hardware is asserted as fact; ADR-013's staged-purchase rule is respected.

#### If it fails
Keep the ESP32 path blocked and record the readiness status. Do not substitute an unidentified board or component; retry only after correct hardware is received and verified.

### AF-100 — Assemble ESP32 2×2 topology and achieve dual-controller first light (EXP-017)

**Milestone:** M3
**Depends on:** AF-187, AF-101
**Labels:** hardware firmware controller-esp32 power validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32 and AF-187 confirms the second controller path is ready; this task maps one controller to each 256×64 row and validates first light.
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize the PSU, controllers, and panels before every HUB75 or panel-power connection change; never hot-plug. Stop output and de-energize before inspecting any abnormal panel, controller, adapter, cable, or power behavior.
**Procedure:** EXP-017

#### Do
1. With all relevant hardware de-energized, connect ESP32 #1 -> panel #1 IN and panel #1 OUT -> panel #2 IN. Connect the AF-187-verified ESP32 #2 -> panel #3 IN and panel #3 OUT -> panel #4 IN.
2. Connect four separate parallel panel-power branches, one per panel; record controller-to-row mapping and save wiring photos in `docs/pm/evidence/AF-100-esp32-m3-topology.md`.
3. Energize only through the documented EXP-017/runbook procedure. Configure each controller for its 256×64 row, send both AF-101 Nano row crops, and record controller identities, firmware, transport endpoints, first-light output, and logs in `docs/pm/evidence/AF-100-esp32-first-light.md`.

#### Done when
- Each controller drives one left-to-right 256×64 row chained through the first panel's OUT into the second panel's IN; all four panels have separate parallel power branches and no hot-plug occurred.
- Four panels display the Nano-originated 256×128 frame with correct top/bottom order and no unexplained blank, duplicate, or reversed region.
- Both controller configurations and dual-dispatch evidence are saved before AF-104.

#### If it fails
Stop output and de-energize. Route unavailable or unidentified hardware to AF-187; isolate topology, adapter, configuration, or framebuffer/dispatch faults before repeating this task.

### AF-101 — Extend the Nano framebuffer to 256×128 dual-row dispatch

**Milestone:** M3
**Depends on:** AF-096
**Labels:** software nano validation
**Procedure:** EXP-017

#### Do
1. Render Nano-originated RGB content into a 256×128 logical framebuffer using the existing framebuffer and transport abstraction.
2. Produce two exact 256×64 row crops: y=0..63 and y=64..127, without changing the renderer's coordinate origin.
3. Dispatch both row crops through the selected architecture's proven transport path and save source frame, crop, dispatch, and log artifacts under `docs/pm/evidence/AF-101-nano-256x128-dispatch.md`.

#### Done when
- The Nano produces a 256×128 source frame and two exact 256×64 row regions without changing the renderer's coordinate origin.
- Dispatch records identify both row crops, their order, dimensions, and Nano origin.

#### If it fails
Keep the display unmodified and inspect framebuffer dimensions, crop bounds, row ordering, and transport payload logs. Correct the software path and repeat AF-101 before first light.

### AF-102 — SUPERSEDED

Replaced by: AF-098

(Do not export to Jira)

### AF-103 — SUPERSEDED

Replaced by: AF-100

(Do not export to Jira)

### AF-104 — Validate complete 2×2 canvas under EXP-017 and audit M3 gate

**Milestone:** M3
**Depends on:** AF-096, AF-101, AF-098 OR AF-100
**Labels:** validation software stability thermal-review decision critical-path
**Safety:** HUB75
**Stop condition:** Stop output and de-energize if blanking, freezing, garbling, resets, smoke, smell, or abnormal heating occurs. De-energize before physical inspection or connector changes.
**Context:** Read the accepted ADR-016 result and execute only after the selected first-light task passes: AF-098 for WF4 or AF-100 for multi-ESP32. This is the M3 gate aggregator; a PASS authorizes Gate B review only and does not begin M4 work.
**Procedure:** EXP-017

#### Do
1. From the Nano, render a gradient and one-pixel test content crossing both vertical x=128 seams and content crossing the horizontal y=64 seam. Render one 256×128 Nano-originated frame crossing all three seams simultaneously; save source frames, transport evidence, full-display photos, and pass/fail observations in `docs/pm/evidence/AF-104-exp-017-seams.md`.
2. Send repeated 256×128 frames with a shared visible event. Record top/bottom row timestamps or frame markers, observed deltas, test conditions, and an explicit dashboard-tolerable or not-tolerable assessment without inventing a numeric cutoff in `docs/pm/evidence/AF-104-row-sync-observation.md`.
3. Render a Nano 256×128 coordinate grid with corner labels (0,0), (255,0), (0,127), and (255,127), both x=128 boundaries, the y=64 boundary, and panel-region identifiers. Photograph/check each panel corner and boundary in `docs/pm/evidence/AF-104-coordinate-grid.md`.
4. Run the documented EXP-017 pattern sequence for at least 30 minutes on the passing architecture. Record pattern, start/end timestamps, runtime, display observations, controller events, logs, and measured Nano/controller temperatures and CPU where available in `docs/pm/evidence/AF-104-exp-017-stability.md`.
5. Review the selected-path evidence, check the 2×2 topology, 256×128 Nano content path, seams, row-update assessment, coordinates, and stability; mark the M3 gate PASS or FAIL and save the complete checklist in `docs/pm/evidence/AF-104-m3-gate.md`.

#### Done when
- Both x=128 seams and the y=64 seam are crossed without an unexplained gap, duplication, or coordinate discontinuity; a single Nano-originated frame visibly crosses all three.
- The row-update method, deltas, conditions, and dashboard assessment are recorded; all four corner labels and all seam labels land on the expected physical regions without row/column inversion.
- The EXP-017 run is at least 30 minutes, has reproducible timestamps and pattern evidence, and shows no unexplained blank, freeze, garble, or reset; available temperature and CPU measurements are included without invented thresholds.
- The gate record explicitly names the winning architecture, checks every M3 criterion, links complete evidence, and declares PASS or FAIL. A PASS does not start M4.

#### If it fails
Stop output and de-energize before physical inspection. Preserve frames, photos, logs, timestamps, and measurements; identify the failed criterion and return to the relevant topology, configuration, transport, or framebuffer task before repeating the affected observation and gate.

### AF-105 — SUPERSEDED

Replaced by: AF-104

(Do not export to Jira)

### AF-106 — SUPERSEDED

Replaced by: AF-104

(Do not export to Jira)

### AF-107 — SUPERSEDED

Replaced by: AF-104

(Do not export to Jira)

### AF-108 — SUPERSEDED

Replaced by: AF-104

(Do not export to Jira)
