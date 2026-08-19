# Phase 03 — HD-WF4 Single-Panel Bringup (M1)

Tasks are maintained as a compact, dependency-driven M1 phase catalog. Hardware facts must be verified against received evidence and the referenced wiring appendix.

---

### AF-038 — Identify HD-WF4 PCB revision, markings, and 4-port layout

**Milestone:** M1
**Depends on:** AF-024
**Labels:** hardware controller-wf4 docs
**Context:** Inspect HD-WF4 board; confirm **4 × HUB75 outputs (X1, X2, X3, X4)**; document PCB revision silk, power terminal type, and Wi-Fi antenna; photograph front/rear directly into hardware/photos/.

#### Do
1. Photograph the WF4 front and rear and record every visible revision, connector, terminal, and antenna marking.
2. Count and label the delivered HUB75 outputs, recording whether X1–X4 are physically present rather than inferring from the product name.
3. Save the observations and photos with the received-board identifier.

#### Done when
- Front/rear photos and the observed PCB revision are saved.
- The report records the actual output count and labels, power-input form, and antenna observation.
- Any mismatch with the expected four-port map is marked for wiring review.

#### If it fails
Do not power an unidentified board. Preserve the photos, compare the connector markings with the project record, and ask for a hardware review if the board is not the expected WF4 or its output labels are ambiguous.

### AF-039 — Verify HD-WF4 5V DC power input connector mating

**Milestone:** M1
**Depends on:** AF-038
**Labels:** hardware controller-wf4 power
**Context:** Inspect WF4 power connector (screw terminal or DC barrel); fabricate and test sample 18 AWG power lead with bootlace ferrules.

#### Do
1. Inspect the delivered WF4 power input and record whether it is a screw terminal or barrel connector, including polarity markings.
2. Prepare a short sample lead using the matching delivered termination method; inspect the crimp/ferrule and confirm it mates without force.
3. Leave the sample disconnected and record the connector and lead construction for AF-040.

#### Done when
- The actual WF4 input connector type and polarity marking are recorded.
- The sample lead mates securely and has no exposed strands or loose ferrule.
- No power is applied during this identification/mating check.

#### If it fails
Do not force a mismatched connector. Isolate the sample, photograph the mating surfaces, and obtain the correct termination or verify the board marking before AF-040.

### AF-040 — Wire HD-WF4 DC power to PSU 5V rail

**Milestone:** M1
**Depends on:** AF-039, AF-024
**Labels:** hardware controller-wf4 power safety-review polarity-verify
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Stop and disconnect power immediately if polarity, wiring, or an abnormal smell, heat, or sound is detected.
**Context:** Install 18 AWG power leads from PSU 5V rail to WF4 power inputs; verify delivered harness construction; verify continuity and polarity under power-OFF conditions, recording measured resistance.

#### Do
1. With PSU output disconnected, wire the observed WF4 input to the verified 5V rail using the termination from AF-039; record the actual harness wire/fuse construction.
2. Check polarity and continuity from the selected PSU terminals to the WF4 input, recording measured resistance and connector labels.
3. Inspect strain relief and isolation before leaving the lead ready for AF-042.

#### Done when
- Power-OFF continuity and polarity checks pass and their measured values are recorded.
- The actual wire gauge/fused status is documented; no unfounded branch-fuse claim is made.
- No bare conductor, loose termination, or accidental short is visible.

#### If it fails
Disconnect the PSU and WF4 first. Correct only the failed polarity, termination, or isolation issue; if the harness construction is unclear, stop and return to receipt evidence rather than energizing.

### AF-041 — Connect HUB75 signal ribbon from HD-WF4 X1 to panel #1 IN header

**Milestone:** M1
**Depends on:** AF-040, AF-022
**Labels:** hardware controller-wf4 polarity-verify
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before changing HUB75 wiring or ribbon cables.
**Context:** Install 16-pin ~40cm HUB75 ribbon cable between WF4 port X1 and panel #1 IN connector with power OFF; verify keyed notch engagement and label cable.

#### Do
1. With WF4 and panel power disconnected, identify the delivered cable key and both X1/panel-IN labels.
2. Mate the 16-pin ribbon without force, verify the key/notch orientation and continuity of the intended signal/ground paths, then apply an index label.
3. Photograph the final unpowered connection before proceeding.

#### Done when
- Cable keying and panel `IN` orientation are recorded from the delivered hardware.
- The ribbon mates fully and the recorded continuity checks pass with power off.
- The cable label and connection photos identify X1 and panel #1.

#### If it fails
Keep both endpoints unpowered. Remove and re-seat the cable only after inspecting keying; if continuity or orientation remains ambiguous, stop and return to AF-022 rather than testing firmware.

### AF-042 — Drive single P2 panel using HD-WF4 stock firmware (EXP-004)

**Milestone:** M1
**Depends on:** AF-041
**Labels:** hardware controller-wf4 power validation
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Stop and disconnect power immediately if polarity, wiring, or an abnormal smell, heat, or sound is detected.
**Procedure:** EXP-004
**Context:** Configure WF4 for 128×64 1/32 scan; execute Standard Test Pattern Suite across panel #1; evaluate against 10-point Standard Defect Checklist; monitor 10–15 min stability.

#### Do
1. Configure the stock WF4 for the observed panel data using 128×64 and 1/32 scan settings only where the panel evidence supports them.
2. Run the six patterns in `docs/EXPERIMENTS.md` and record color order, row mapping, seams, ghosting, flicker, missing pixels, and resets.
3. Hold the displayed pattern set for 10–15 minutes and record the observed stability and any temperature/voltage observations.

#### Done when
- The panel displays all six patterns with the observed geometry and color mapping recorded.
- Every item in the standard defect checklist is marked pass, fail, or not observed with evidence.
- The 10–15 minute hold log contains start/end times and any reset or visible instability.

#### If it fails
Power down before changing configuration or cabling. Preserve the failing pattern and controller settings, then isolate panel power, cable orientation, and WF4 configuration in that order; escalate unresolved panel mapping to AF-022/receipt evidence.

### AF-043 — Characterize Huidu vendor software workflow (EXP-007 Phase A)

**Milestone:** M1
**Depends on:** AF-042
**Labels:** software controller-wf4 validation
**Procedure:** EXP-007
**Context:** Identify the vendor tool and version appropriate for the delivered WF4, then document configuration, static file upload, and live update UI surfaces; record transfer times.

#### Do
1. Identify and record the vendor tool/version appropriate for the delivered WF4, then record which screen controls configure geometry, upload static content, or perform live updates.
2. Send one known image and one changed image through each available workflow, recording the interface, transfer completion, and observed update time.
3. Mark every step that requires a manual click or cannot be reproduced from the Nano.

#### Done when
- Configuration, static upload, and live-update surfaces are separately documented as observed or unavailable.
- At least two known image attempts have recorded outcomes and transfer observations.
- Manual UI dependencies and unknown protocol details are explicit.

#### If it fails
Keep the last known-good WF4 configuration. Save screenshots/logs for the first failed interface, verify network/USB enumeration separately, and do not infer a live protocol from a static upload failure.

### AF-044 — Reverse-engineer open-source Huidu protocol library for WF4

**Milestone:** M1
**Depends on:** AF-043
**Labels:** software controller-wf4 spike
**Procedure:** EXP-007
**Context:** Identify and inspect open-source Huidu protocol libraries; document packet framing, command structure, checksums, and network/USB payload formats.

#### Do
1. Identify candidate open-source Huidu libraries and record repository/version/license and supported transport.
2. Read the implementation far enough to document packet framing, commands, checksums, and payload formats actually evidenced by code or captures.
3. Separate confirmed facts from hypotheses and write a minimal host-side test plan for AF-183.

#### Done when
- At least one candidate library and exact version/source are recorded, or the search result is documented as unavailable.
- Confirmed framing/checksum/transport facts have source references; unknown fields remain unknown.
- AF-183 has a bounded, reproducible test input and expected observation.

#### If it fails
Do not transmit guessed packets. Preserve the library/source and analysis notes, mark unsupported fields unknown, and return to AF-043 if the candidate lacks enough evidence for a safe host test.

### AF-183 — Transmit test frame from host PC to WF4 via open protocol library

**Milestone:** M1
**Depends on:** AF-044
**Labels:** software controller-wf4 validation
**Context:** Transmit a static 128×64 test frame from host PC to WF4 using open protocol library without vendor software; verify correct panel rendering.

#### Do
1. Prepare one 128×64 frame with a coordinate/grid pattern and connect the host using the library-supported WF4 transport.
2. Send the frame without vendor software, record command output and the panel's visible mapping, then send a second frame with a changed marker.
3. Preserve the exact source revision, frame files, and observed result.

#### Done when
- Both send attempts have recorded exit status and visible panel result.
- The panel shows the expected marker without a required vendor-software click.
- The transport command, library revision, and frame artifacts are reproducible.

#### If it fails
Stop transmission and retain the failed frame/log. Check transport selection and frame encoding separately; if the controller changes state unexpectedly, restore the last known-good configuration before another send.

### AF-184 — Implement Python frame sender on Nano communicating with WF4

**Milestone:** M1
**Depends on:** AF-183, AF-037
**Labels:** software controller-wf4 nano
**Context:** Port open protocol transport to Python on Nano; send static 128×64 framebuffer from Nano to WF4 over USB/network link.

#### Do
1. Port the proven host sender to the Nano and run it from the Nano venv with the same known frame.
2. Send the frame over the observed USB/network path and record interface, command output, and panel result.
3. Keep the host and Nano frame bytes comparable so transport differences can be isolated.

#### Done when
- Nano execution sends a known 128×64 frame and the panel result matches the host result.
- The interface and exact command/environment are recorded.
- No manual vendor UI action is needed for the Nano send.

#### If it fails
Preserve host/Nano frame hashes and logs. Test name resolution, transport reachability, and payload encoding separately; revert only the Nano sender change if it affects the host-proven protocol.

### AF-185 — Measure Nano-to-WF4 frame transmission latency and update duration

**Milestone:** M1
**Depends on:** AF-184
**Labels:** software controller-wf4 validation
**Context:** Measure end-to-end latency from Nano frame dispatch to visible panel pixel update; measure raw byte transfer time per frame.

#### Do
1. Add a dispatch timestamp and a visible-frame marker to one Nano frame, then send repeated single frames to the WF4.
2. Measure raw transfer duration and dispatch-to-visible-update latency for each run using the available instruments/logs.
3. Record every sample, interface, frame size, and observation; do not apply a fabricated pass threshold.

#### Done when
- A table contains the raw transfer and visible-update measurements with units and method.
- Frame size and test interface are recorded for every sample.
- Any clock/measurement uncertainty is stated rather than converted into a guessed tolerance.

#### If it fails
Stop the run if frames cannot be identified reliably. Preserve timestamps and logs, verify the marker path with a single frame, and repeat only after the measurement method is demonstrably distinguishing dispatch from visible update.

### AF-186 — Benchmark maximum sustainable update frequency to WF4

**Milestone:** M1
**Depends on:** AF-185
**Labels:** software controller-wf4 validation
**Context:** Benchmark continuous sequential frame updates over 30 seconds; determine maximum sustainable frame rate (FPS) without frame drops or controller reset.

#### Do
1. Generate sequentially numbered frames and transmit continuously for 30 seconds using the Nano sender.
2. Count sent, displayed, dropped, duplicated, or reset-indicating frames from the logs and visible markers.
3. Record the highest observed sustainable update rate and the conditions under which it was measured, without declaring an invented threshold.

#### Done when
- The 30-second run has a complete sent/observed/drop/reset count.
- The measured sustainable rate is reported with frame dimensions and transport conditions.
- Any instability is correlated to a recorded frame number or controller event.

#### If it fails
Stop the sender after a reset or uncontrolled backlog, preserve the last log, and reduce only one run parameter at a time for diagnosis. Return to AF-185 if frame visibility cannot be distinguished from transport completion.

### AF-045 — Automated continuous Python rendering and transmission (EXP-007 Phase C)

**Milestone:** M1
**Depends on:** AF-186, AF-032
**Labels:** software controller-wf4 power thermal-review validation
**Procedure:** EXP-007
**Context:** Run automated continuous update loop from Nano (render → transport → WF4) for ≥1 hour; verify thermal stability, zero crashes, and zero required vendor software clicks.

#### Do
1. Start the Nano loop that renders changing text, exports the framebuffer, and sends it through the proven WF4 transport.
2. Run the loop for at least one hour while recording process restarts, controller resets, visible defects, and any available power/temperature observations.
3. Confirm updates require no vendor-software clicks and save the run log.

#### Done when
- The one-hour run log shows start/end time, frame activity, and every interruption.
- The loop completes without an unrecorded crash or manual vendor action.
- Thermal, power, or visual anomalies are recorded as observations, not silently treated as passes.

#### If it fails
Stop the loop and preserve its stdout/stderr and controller state. Reproduce the first interruption with a short run, then isolate renderer, transport, and controller causes before resuming the hour-long test.

### AF-046 — End-to-end arbitrary user text rendering to panel #1 via WF4

**Milestone:** M1
**Depends on:** AF-045, AF-030, AF-035
**Labels:** validation controller-wf4 critical-path
**Procedure:** EXP-007
**Context:** Execute full E2E test with arbitrary runtime user string typed on Nano terminal; verify exact character rendering on panel #1; perform 10-min stable hold.

#### Do
1. Type an arbitrary test string on the Nano terminal and capture the exact input, rendered frame, transport log, and panel photograph.
2. Verify the displayed characters and layout against the input, then hold the result for 10 minutes while recording resets or visible defects.
3. Confirm the run used no manual vendor-software update action.

#### Done when
- The panel displays the exact accepted characters from the recorded runtime input.
- The 10-minute hold has a start/end log with no unexplained reset or visual defect.
- The evidence proves the path was Nano → renderer → WF4 → panel without runtime vendor clicks.

#### If it fails
Stop the update loop and preserve input, frame, transport, and panel evidence. Replay the same input through AF-035, then isolate the first divergent stage before another physical-panel run.

### AF-047 — Collate and commit WF4 M1 subtrack evidence and candidate pass log

**Milestone:** M1
**Depends on:** AF-046
**Labels:** docs controller-wf4 validation
**Context:** Collate all EXP-004 and EXP-007 measurements, photos, and logs; commit evidence; record preliminary WF4 scores for ADR-016 matrix.

#### Do
1. Index the AF-042 and AF-043–AF-046 logs, photos, frame artifacts, and measured tables by task and experiment.
2. Check that each claimed WF4 result has a source artifact and mark missing evidence or unresolved observations explicitly.
3. Write the preliminary WF4 candidate pass log and commit only the evidence references and summary.

#### Done when
- EXP-004 and EXP-007 evidence has an index with no unreferenced pass claim.
- The candidate log states pass, fail, or unknown for the single-panel outcome and cites the supporting task evidence.
- The evidence commit hash is recorded.

#### If it fails
Do not rewrite missing measurements as passes. Mark the gap, return the specific experiment to its owning task, and update the candidate log only after the replacement evidence is reviewed.
