# Phase 03 - HD-WF4 Single-Panel Bringup (M1)

Tasks are maintained as a compact, dependency-driven M1 phase catalog. Hardware facts must be verified against received evidence and the referenced wiring appendix.

---

### AF-038 - Identify HD-WF4 PCB revision, markings, and 4-port layout

**Milestone:** M1
**Depends on:** AF-024
**Labels:** hardware controller-wf4 docs
**Context:** Establish the exact received controller variant before wiring or powering.

#### Do
1. Photograph the WF4 front and rear; record visible revision, connectors, terminals, antenna, and power-input form.
2. Count and label physically present HUB75 ports, recording whether X1-X4 exist instead of inferring from the product name.
3. Save observations/photos under the received-board identifier and flag any mismatch for wiring review.

#### Done when
- Photos, observed revision, output count/labels, power-input form, and antenna observation are saved.
- Any mismatch with the expected four-port map is explicit.

#### If it fails
Do not power an unidentified board. Preserve photos and resolve variant or output-label ambiguity before wiring.

---

### AF-039 - SUPERSEDED

Replaced by: AF-040

(Do not export to Jira)

---

### AF-040 - Wire HD-WF4 DC power to PSU 5 V rail

**Milestone:** M1
**Depends on:** AF-038, AF-024
**Labels:** hardware controller-wf4 power safety-review polarity-verify
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Stop and disconnect power immediately if polarity, wiring, or an abnormal smell, heat, or sound is detected.
**Context:** Deliver safe, verified 5 V to the WF4 using the connector and termination actually present on the received board.

#### Do
1. Inspect and record the WF4 input connector, polarity marking, and mating method; fabricate a sample lead with the matching delivered termination and verify secure mating without force or exposed strands.
2. With PSU output disconnected, wire the WF4 to the verified 5 V rail, recording actual wire gauge and any observed fuse/harness construction without making an unsupported branch-fuse claim.
3. With power off, measure and record continuity/polarity from selected PSU terminals to WF4 input; inspect strain relief and isolation before leaving the lead ready for panel testing.

#### Done when
- The actual connector/polarity/mating arrangement and power-off continuity measurements are recorded.
- Wire gauge/fused status is evidence-backed, and no bare conductor, loose termination, or accidental short is visible.

#### If it fails
Disconnect the PSU and WF4 first. Correct only the failed polarity, termination, or isolation item; return to received evidence rather than energizing an unclear harness.

---

### AF-041 - SUPERSEDED

Replaced by: AF-042

(Do not export to Jira)

---

### AF-042 - Drive single P2 panel using HD-WF4 stock firmware (EXP-004)

**Milestone:** M1
**Depends on:** AF-040, AF-021
**Labels:** hardware controller-wf4 power validation
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize panel and controller before changing HUB75 wiring; stop and disconnect power for polarity, wiring, smell, heat, or sound anomalies.
**Procedure:** EXP-004
**Context:** Validate a delivered panel with stock firmware before protocol or Nano-control work.

#### Do
1. With power off, connect the keyed 16-pin ribbon from WF4 X1 to panel #1 `IN`; verify key/notch, `IN` orientation, intended ground/signal continuity, and label/photo the unpowered connection.
2. Configure stock WF4 for observed 128x64 and 1/32 scan settings only where panel evidence supports them. Run the six standard patterns and the standard defect checklist, recording geometry, color order, row mapping, seams, ghosting, flicker, missing pixels, and resets.
3. Hold the displayed pattern set for 10-15 minutes, logging start/end, visible stability, and any available temperature/voltage observations.

#### Done when
- Keying/orientation and the final X1-to-panel connection are evidenced before energization.
- All six patterns and every defect-checklist item are recorded as pass, fail, or not observed.
- The stability log records the full hold and any reset or visible anomaly.

#### If it fails
Power down before changing configuration or cabling. Preserve the failing pattern/settings and isolate panel power, cable orientation, then WF4 configuration; escalate unresolved mapping to receipt evidence.

---

### AF-043 - SUPERSEDED

Replaced by: AF-183

(Do not export to Jira)

---

### AF-044 - SUPERSEDED

Replaced by: AF-183

(Do not export to Jira)

---

### AF-183 - Establish host-controllable and Nano-controllable frame path to WF4

**Milestone:** M1
**Depends on:** AF-042, AF-035
**Labels:** software controller-wf4 nano validation
**Procedure:** EXP-007
**Context:** Prove the WF4 can receive known frames first from a host and then from the Nano without a vendor UI click.

#### Do
1. Identify the delivered-board vendor tool/version; document separately the observed geometry configuration, static upload, and live-update UI surfaces, two known-image attempts, transfer observations, and every unavoidable manual click.
2. Identify candidate open-source Huidu libraries; record repository/version/license/transport, evidence-backed packet framing/commands/checksums/payload facts, unknown fields, and a bounded host test plan. Do not transmit guessed packets.
3. Send a 128x64 coordinate/grid frame and a changed-marker frame from the host without vendor software; retain command output, panel mapping, library revision, and frame artifacts.
4. Port the proven sender to the Nano venv; send the same known frame via the observed USB/network path, compare host/Nano frame bytes, and record the exact Nano command, environment, interface, and panel result.

#### Done when
- Vendor workflow limitations and library facts are documented with sources rather than assumptions.
- Host and Nano each render known changed-marker frames correctly without a required vendor click.
- Commands, source revision, frame artifacts, environment, and results are reproducible.

#### If it fails
Preserve frames, hashes, logs, and last known-good configuration. Diagnose transport reachability and payload encoding separately; do not send guessed packets or alter the host-proven protocol while isolating the Nano port.

---

### AF-184 - SUPERSEDED

Replaced by: AF-183

(Do not export to Jira)

---

### AF-185 - SUPERSEDED

Replaced by: AF-045

(Do not export to Jira)

---

### AF-186 - SUPERSEDED

Replaced by: AF-045

(Do not export to Jira)

---

### AF-045 - Run automated continuous Nano-to-WF4 update loop, benchmark sustainability, and collate M1 subtrack evidence

**Milestone:** M1
**Depends on:** AF-183, AF-032
**Labels:** software controller-wf4 power thermal-review validation docs
**Procedure:** EXP-007
**Context:** Measure the programmatic WF4 path, run it continuously, and create an evidence-backed M1 candidate record.

#### Do
1. Add a dispatch timestamp and visible marker to known Nano frames; measure raw transfer duration and dispatch-to-visible latency, recording every sample, frame size, interface, unit, method, and measurement uncertainty.
2. Send sequentially numbered frames continuously for 30 seconds; count sent, displayed, dropped, duplicated, and reset-indicating frames, then record the highest observed sustainable rate and conditions without inventing a threshold.
3. Run the Nano render -> framebuffer -> WF4 loop for at least one hour, logging frame activity, restarts, controller resets, visible defects, vendor-click absence, and available thermal/power observations.
4. Index EXP-004/007 logs, photos, frames, and tables; mark missing evidence explicitly, write the WF4 candidate pass/fail/unknown log with citations, commit the reviewed evidence index, and record its hash.

#### Done when
- Latency and 30-second benchmark tables include units, method, conditions, and all observed drop/reset data.
- The one-hour run log includes start/end, interruptions, and observations rather than implied passes.
- Every candidate claim cites an artifact, missing evidence remains explicit, and the evidence commit hash is recorded.

#### If it fails
Stop on a reset or uncontrolled backlog and preserve stdout/stderr/controller state. Reproduce the first interruption with a short run, then isolate renderer, transport, controller, or measurement method before repeating the sustained run.

---

### AF-047 - SUPERSEDED

Replaced by: AF-045

(Do not export to Jira)

---

### AF-046 - End-to-end arbitrary user text rendering to panel #1 via WF4

**Milestone:** M1
**Depends on:** AF-045, AF-030, AF-035
**Labels:** validation controller-wf4 critical-path
**Procedure:** EXP-007
**Context:** Prove arbitrary runtime text entered on the Nano appears on the physical panel through the proven WF4 path.

#### Do
1. Type an arbitrary test string on the Nano and retain exact input, rendered frame, transport log, and panel photograph.
2. Verify displayed characters/layout against the accepted input, then hold for 10 minutes while logging resets or visible defects.
3. Confirm no runtime vendor-software update action was used.

#### Done when
- The panel displays the exact accepted characters from the recorded runtime input.
- The hold log has start/end times and no unexplained reset or visible defect.
- Evidence proves Nano -> renderer -> WF4 -> panel without runtime vendor clicks.

#### If it fails
Preserve input, frame, transport, and panel evidence. Replay the input through AF-035 and isolate the first divergent stage before another physical-panel run.
