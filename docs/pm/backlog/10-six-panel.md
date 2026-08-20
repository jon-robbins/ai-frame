# Phase 10 — Six-Panel Full Prototype & Thermal / Recovery (M4)

Covers the gated 256x192, 2x3 full display on the ADR-016-selected architecture. M4 proves the physical canvas, loaded operating observations, and physical boot/controller-transport/Wi-Fi recovery before MA begins. API failure, cached/stale-data behavior, and live-data resume are MA AF-133 responsibilities.

**AF-096 post-decision normalization for M4 conditional entries AF-110 and AF-111:** The AF-096 registry-wide sweep applies the accepted ADR-016 result before M4 execution. Before resolution, each card carries `conditional blocked blocked:adr-016` plus its controller label and an `Applies if` branch condition. For the winner, remove `conditional blocked blocked:adr-016` and `Applies if`, add `critical-path`, and retain the winning controller label. For the loser, retain or add `conditional blocked blocked:adr-016`, remove `critical-path` if present, and replace `Applies if` with exactly `Skip — ADR-016 selected WF4` when multi-ESP32 loses, or `Skip — ADR-016 selected multi-ESP32` when WF4 loses. Only the selected branch executes after AF-096.

### AF-109 — Verify panels #5 and #6 unpowered

**Milestone:** M4
**Depends on:** AF-104
**Labels:** hardware polarity-verify validation safety-review
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Keep the PSU, panels, and controllers de-energized. Stop if any V+/V-, HUB75 IN/OUT, keying, or top-edge marking is missing or contradictory.

#### Do
1. Retrieve panels #5 and #6 onto the ESD-safe bench while all panel and controller power remains disconnected.
2. Record each panel's V+/V- markings, HUB75 IN/OUT and key orientation, and top-edge orientation against the received-hardware evidence and panels #1 through #4.
3. Photograph both panel backs and save the comparison in `docs/pm/evidence/AF-109-panels5-6-reverify.md` with `hardware/photos/AF-109-panel5-reverify.jpg` and `hardware/photos/AF-109-panel6-reverify.jpg`.

#### Done when
- Panels #5 and #6 each have recorded polarity, HUB75 orientation, and physical orientation with no unresolved contradiction.
- Evidence identifies both panels and links to the received-hardware baseline.

#### If it fails
Keep everything disconnected, quarantine the ambiguous panel or connector, and resolve the discrepancy from received-hardware or manufacturer evidence before topology assembly or energization.

### AF-110 — Bring up WF4 six-panel topology and first light (EXP-006)

**Milestone:** M4
**Depends on:** AF-096, AF-109, AF-112
**Labels:** hardware firmware controller-wf4 power validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects WF4; this task supplies the selected controller's three-row topology and first light.
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize the PSU, controller, and panels before every HUB75 or panel-power connection change; never hot-plug. Stop output and de-energize before inspecting any abnormal panel, controller, cable, or power behavior.
**Procedure:** EXP-006

#### Do
1. With the bench de-energized, place panels #1/#2 as the top row, #3/#4 as the middle row, and #5/#6 as the bottom row. Connect `WF4 X1 -> panel #1 IN -> panel #1 OUT -> panel #2 IN`, `WF4 X2 -> panel #3 IN -> panel #3 OUT -> panel #4 IN`, and `WF4 X3 -> panel #5 IN -> panel #5 OUT -> panel #6 IN`; leave X4 unused. Verify every ribbon's keying and IN/OUT label before proceeding.
2. With power still disconnected, connect six separate panel-power branches in parallel, one per panel; do not daisy-chain panel power. Verify branch identity, V+/V- polarity, secure ferrules/connectors, and the absence of exposed conductors before recording the topology in `docs/pm/evidence/AF-110-wf4-m4-topology.md`.
3. Energize only through the documented EXP-006 and safety-runbook procedure; do not invent a signal-before-power or power-before-signal order. Measure and record the PSU output and farthest-panel branch voltage under the runbook's acceptance method before configuring WF4 as three ordered 256x64 rows forming 256x192.
4. Send the AF-112 Nano-originated top, middle, and bottom crops. Save controller configuration, first-light photos, source/crop identity, and transport logs in `docs/pm/evidence/AF-110-wf4-first-light.md`.

#### Done when
- The documented WF4 topology is X1 top, X2 middle, and X3 bottom, with every row using `controller OUT -> left panel IN -> left panel OUT -> right panel IN` hops as specified above; X4 is unused.
- All six panels use separate parallel power branches, and the recorded power checks pass before first light.
- Six panels display ordered Nano-originated 256x192 content with no unexplained blank, duplicate, reversed, or missing region.

#### If it fails
Stop output and de-energize fully. Isolate the ribbon, branch, connector orientation, configuration, controller transport, or framebuffer fault; correct the named source task and repeat the affected check before continuing.

### AF-111 — Bring up multi-ESP32 six-panel topology and first light

**Milestone:** M4
**Depends on:** AF-096, AF-100, AF-109, AF-112
**Labels:** hardware firmware controller-esp32 power validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32; this task supplies the selected controller's three-row topology and first light.
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize the PSU, controllers, and panels before every HUB75 or panel-power connection change; never hot-plug. Stop output and de-energize before inspecting any abnormal panel, controller, adapter, cable, or power behavior.

#### Do
1. Audit the third ESP32-S3 and HCT adapter path. Record `READY` with board identity, adapter status, firmware baseline, and intended bottom-row role, or `MISSING` with the exact required hardware in `docs/pm/evidence/AF-111-third-controller-readiness.md`. If and only if the outcome is `MISSING`, create AF-188 at execution time for procurement, receipt, and verification; AF-111 is externally blocked on that new card until it passes. Do not create AF-188 during this migration.
2. For a ready third path, build or verify the adapter by the proven AF-054 procedure and configure all controllers from the actually proven M3 ESP32 configuration. This is an ESP32 integration task, not EXP-006, which is WF4-only.
3. With the bench de-energized, assign controller #1 to the top row, controller #2 to the middle row, and controller #3 to the bottom row. Connect `controller #1 HUB75 OUT -> panel #1 IN -> panel #1 OUT -> panel #2 IN`, `controller #2 HUB75 OUT -> panel #3 IN -> panel #3 OUT -> panel #4 IN`, and `controller #3 HUB75 OUT -> panel #5 IN -> panel #5 OUT -> panel #6 IN`; verify connector keying and IN/OUT labels for every hop.
4. With power still disconnected, connect six separate panel-power branches in parallel, one per panel; do not daisy-chain panel power. Verify branch identity, V+/V- polarity, secure ferrules/connectors, and the absence of exposed conductors before recording topology and controller-to-row mapping in `docs/pm/evidence/AF-111-esp32-m4-topology.md`.
5. Energize only through the proven M3 ESP32/safety-runbook procedure; do not invent a signal-before-power or power-before-signal order. Measure and record the PSU output and farthest-panel branch voltage under the runbook's acceptance method, dispatch the AF-112 Nano row crops to controllers #1/#2/#3, and save configurations, first-light photos, and transport logs in `docs/pm/evidence/AF-111-esp32-first-light.md`.

#### Done when
- The readiness evidence states `READY`, or a project-visible `MISSING` outcome is resolved by execution-time AF-188 before this card continues.
- Controllers #1/#2/#3 drive the documented top/middle/bottom rows through the specified OUT-to-IN HUB75 hops, with six parallel panel-power branches and no hot-plug.
- Six panels display ordered Nano-originated 256x192 content with controller identity, configuration, topology, power-check, and first-light evidence saved.

#### If it fails
Keep the ESP32 path de-energized. Route missing third-controller hardware only through execution-time AF-188; otherwise isolate adapter, topology, configuration, power, transport, or framebuffer faults before repeating this task.

### AF-112 — Extend Nano framebuffer to 256x192 three-row dispatch

**Milestone:** M4
**Depends on:** AF-096, AF-101, AF-104
**Labels:** software nano validation

#### Do
1. Render Nano-originated RGB content into a 256x192 logical framebuffer without changing the renderer coordinate origin.
2. Produce three exact 256x64 crops: top `y=0..63`, middle `y=64..127`, and bottom `y=128..191`.
3. Dispatch the crops in top/middle/bottom order through the selected architecture's proven transport path. Save source frame, crop dimensions and bounds, dispatch identity, and logs in `docs/pm/evidence/AF-112-nano-256x192-dispatch.md`.

#### Done when
- The Nano produces one 256x192 source frame and three exact 256x64 row regions without changing the coordinate origin.
- Dispatch evidence identifies each crop, its order, dimensions, and Nano origin.

#### If it fails
Keep the display topology unmodified and inspect framebuffer dimensions, crop bounds, row order, and transport payload logs. Correct the software path before either first-light task continues.

### AF-113 — Validate complete 256x192 canvas

**Milestone:** M4
**Depends on:** AF-112, AF-110 OR AF-111
**Labels:** validation software stability thermal-review critical-path
**Safety:** HUB75
**Stop condition:** Stop output and de-energize if blanking, freezing, garbling, resets, smoke, smell, or abnormal heating occurs. De-energize before physical inspection or connector changes.
**Context:** Execute only after the selected first-light card passes. EXP-006 is WF4-only; the multi-ESP32 path uses its documented integration evidence, not an EXP-006 claim.

#### Do
1. Render a Nano-originated gradient, one-pixel line, and content straddling each of the three vertical `x=128` seams and two horizontal `y=64`/`y=128` seams. Save one close-up observation for each seam in `docs/pm/evidence/AF-113-m4-seams.md`.
2. Render a 256x192 coordinate grid with corners `(0,0)`, `(255,0)`, `(0,191)`, and `(255,191)`, all five seam labels, and panel-region identifiers. Check and save each physical location in `docs/pm/evidence/AF-113-coordinate-grid.md`.
3. Place arbitrary Nano content at multiple positions across all six panel regions, including content crossing each seam. Save source frames, transport evidence, full-display photos, and an explicit mapping assessment in `docs/pm/evidence/AF-113-arbitrary-content.md`.

#### Done when
- All five seams are crossed without an unexplained gap, duplication, reversal, or coordinate discontinuity.
- Corner, seam, and panel-region labels land on the expected physical locations.
- Arbitrary Nano content maps correctly anywhere on the 256x192 canvas.

#### If it fails
Stop output and de-energize before physical inspection. Preserve frames, photos, and logs; identify the failed topology, controller configuration, transport, framebuffer, or coordinate criterion before repeating the affected observation.

### AF-114 — SUPERSEDED

Replaced by: AF-113

(Do not export to Jira)

### AF-115 — Characterize dashboard brightness and loaded thermals (EXP-015)

**Milestone:** M4
**Depends on:** AF-113
**Labels:** validation thermal thermal-review power critical-path
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Stop output and de-energize if voltage instability, controller resets, blanking, smoke, smell, abnormal connector heating, or unsafe conductor heating occurs. Do not use a multimeter ammeter input in series with the full display load.
**Procedure:** EXP-015

#### Do
1. Use representative dashboard content and the EXP-015 stress content at 25%, 50%, 75%, and 100% brightness. At every level, record visual behavior, PSU output voltage, voltage at the farthest panel, PSU/wiring/connector temperatures, controller stability, and display behavior in `docs/pm/evidence/AF-115-exp-015-loaded-thermal.md`.
2. Record the actual measurement method, test duration, ambient conditions, controller architecture, and any stop or abort event for each level. Preserve observations rather than asserting unsupported numeric thermal limits.
3. Compare all four levels and identify observed acceptable operation, voltage or heating concerns, and any required follow-up without setting the production brightness ceiling; AF-120 owns that later decision.

#### Done when
- A complete four-level EXP-015 table records the required loaded voltage, thermal, controller, and display observations.
- The evidence identifies observed acceptable operation and every anomaly or abort without inventing limits.

#### If it fails
Stop and de-energize. Preserve the readings and fault evidence; correct the power distribution, cooling, controller, or display issue before repeating the affected level.

### AF-116 — SUPERSEDED

Replaced by: AF-115

(Do not export to Jira)

### AF-117 — Validate boot, controller-transport, and Wi-Fi recovery (EXP-016)

**Milestone:** M4
**Depends on:** AF-113
**Labels:** validation recovery-test power networking critical-path
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** Stop the test and de-energize before physical inspection if a power cycle produces abnormal panel, controller, cable, or power behavior. Do not unplug HUB75 while energized.
**Procedure:** EXP-016
**Context:** This card covers physical boot, controller-transport readiness, and Wi-Fi recovery only. API failure, cache/stale-data behavior, and live-data resume remain MA AF-133 work.

#### Do
1. Run five documented power-recovery cycles using the EXP-016 procedure. For each cycle, record time to first image, time to controller-transport readiness, time to Wi-Fi recovery where applicable, all-six-panel content integrity, and observations in `docs/pm/evidence/AF-117-exp-016-recovery.md`.
2. Simulate Wi-Fi loss and restoration without manually rebooting the Nano. Record reconnect and display/controller-transport recovery evidence in `docs/pm/evidence/AF-117-wifi-recovery.md`.
3. Record the worst observed physical recovery times and any abnormal blank, garble, reset, or manual intervention requirement. Do not add API fallback or live-data acceptance criteria to this M4 card.

#### Done when
- Five recorded boot/controller-transport recovery cycles show six-panel content integrity and no unresolved physical recovery failure.
- Wi-Fi loss and rejoin recover without manual reboot, with timing and display/controller-transport evidence saved.
- Evidence distinguishes physical recovery from MA AF-133 API/cache/live-data behavior.

#### If it fails
Preserve cycle logs and timing evidence, then isolate the power, controller boot, transport, Wi-Fi, or physical display cause before repeating the affected recovery test.

### AF-118 — SUPERSEDED

Replaced by: AF-117

(Do not export to Jira)

### AF-119 — M4 gate

**Milestone:** M4
**Depends on:** AF-113, AF-115, AF-117
**Labels:** validation gate-pass decision critical-path
**Context:** M4 converges full-canvas, loaded thermal, and physical recovery evidence. A PASS authorizes MA work; it does not assert MA AF-133 API/cache/live-data behavior.

#### Do
1. Review AF-113 evidence for six-panel 256x192 operation, all five seams, coordinate mapping, and arbitrary Nano content placement.
2. Review AF-115's four-level EXP-015 loaded observations and AF-117's five-cycle boot/controller-transport plus Wi-Fi recovery evidence.
3. Save a criterion-by-criterion PASS/FAIL record with evidence links and the selected architecture in `docs/pm/evidence/AF-119-m4-gate.md`.

#### Done when
- The gate record explicitly passes or fails full-canvas, thermal, and physical recovery criteria with linked evidence.
- A PASS links six-panel mapping, all five seams, loaded operating observations, and boot/controller-transport/Wi-Fi recovery without claiming MA-only API/cache/live-data outcomes.

#### If it fails
Name the failed criterion, preserve the evidence, and return to AF-113, AF-115, or AF-117 before repeating the gate.
