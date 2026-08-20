# Phase 14 — Mounted Frame Optimization & Closeout (MF)

Covers a measured, serviceable, safe six-panel enclosure through wall installation and final product gate.

### AF-134 — Capture enclosure constraints and complete internal layout

**Milestone:** MF
**Depends on:** AF-121, AF-133
**Conditional dependency:** AF-169 when Cond-X is active.
**Labels:** hardware mechanical thermal validation critical-path

#### Do
1. Measure panels, PSU, controllers, Nano, harnesses, connectors, cable bend radius, service loops, wall hardware, and the final PCB when Cond-X is active.
2. Create a scaled placement, clearance, airflow, service-loop, and minimum-depth plan that identifies removable/serviceable components and conflicts.
3. Save dimensions and the reviewed layout in `docs/pm/evidence/AF-134-enclosure-constraints.md`.

#### Done when
- A dimensioned layout resolves component placement, clearance, airflow, service access, and minimum-depth constraints.
- When Cond-X is active, the layout identifies the AF-169 final PCB rather than a perfboard substitute.

#### If it fails
Do not cut material or route power. Re-measure the conflicting item and revise the layout until every constraint has a documented resolution.

### AF-135 — SUPERSEDED

Replaced by: AF-134

(Do not export to Jira)

### AF-136 — SUPERSEDED

Replaced by: AF-134

(Do not export to Jira)

### AF-137 — Design PSU/controller placement and ventilation

**Milestone:** MF
**Depends on:** AF-134, AF-120
**Labels:** hardware mechanical thermal power critical-path

#### Do
1. Position the PSU, selected controller(s), Nano, and support electronics using AF-134 clearances and service requirements.
2. Design intake, exhaust, and a fan contingency from AF-120 and measured thermal evidence; identify airflow path and prevent blocked vents or service access.
3. Save placement, airflow, and fan-contingency decisions in `docs/pm/evidence/AF-137-placement-ventilation.md`.

#### Done when
- The layout identifies electronics locations, airflow path, service access, and no blocked fan or vent path.
- Ventilation decisions cite measured evidence rather than invented heat limits.

#### If it fails
Revise the placement or ventilation design before fabrication; do not compensate for an unresolved airflow conflict by raising the operating brightness ceiling.

### AF-138 — SUPERSEDED

Replaced by: AF-137

(Do not export to Jira)

### AF-147 — Build, mount, and align six-panel backplate

**Milestone:** MF
**Depends on:** AF-134, AF-137
**Labels:** hardware mechanical validation critical-path
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** Keep panels and electronics de-energized while mounting or aligning. Stop if a panel is unsupported, stressed, misaligned, has damaged cabling, or cannot be serviced without forced cable movement.

#### Do
1. Build the backplate and fastener/alignment system from the measured layout; dry-fit all six panels and service clearances before final fastening.
2. Mount the panels, verify flatness, seam alignment, secure fastening, cable clearance, and access for future service while all electronics remain de-energized.
3. Save fastener, alignment, flatness, and seam evidence in `docs/pm/evidence/AF-147-backplate-alignment.md`.

#### Done when
- Six panels are securely mounted with documented flatness, seam alignment, cable clearance, and service access.
- The backplate supports the electrical routing plan without pinching or stressing connectors.

#### If it fails
Keep electronics de-energized and correct the mechanical support, alignment, clearance, or access issue before routing power.

### AF-148 — SUPERSEDED

Replaced by: AF-147

(Do not export to Jira)

### AF-139 — Implement safe mains/DC separation, cable routing, strain relief, and PE bonding

**Milestone:** MF
**Depends on:** AF-147, AF-137
**Labels:** hardware power safety-review critical-path
**Safety:** MAINS, 5V-HIGH-CURRENT, HUB75
**Stop condition:** Disconnect the wall plug before opening, routing, terminating, inspecting, or changing any enclosure conductor. Stop immediately for uncertain L/N/PE identification, missing PE-to-FG/chassis continuity, missing strain relief, exposed conductor, damaged insulation, unprotected sharp edge, mixed AC/DC routing, or any continuity/isolation failure. Do not energize until the MAINS runbook checks pass.

#### Do
1. With mains disconnected at the wall, build the required barrier and route mains and DC in separate paths. Identify L/N/PE from terminal markings and continuity, not wire color alone; keep mains conductors protected from edges and accessible only for service.
2. Add strain relief, cable categories/labels, and service loops; bond PE to PSU FG and chassis as designed, then perform continuity and isolation checks under the MAINS, 5V-HIGH-CURRENT, and HUB75 runbooks.
3. Save routing, barrier, strain-relief, PE/FG/chassis continuity, and inspection evidence in `docs/pm/evidence/AF-139-enclosure-power-safety.md`.

#### Done when
- The enclosure has documented mains/DC separation, strain relief, cable protection, service loops, and PE/FG/chassis bonding.
- Required continuity and isolation evidence passes before any closed-enclosure energization.

#### If it fails
Keep the wall plug disconnected. Correct the barrier, routing, termination, strain relief, bonding, or insulation fault and repeat the full affected inspection.

### AF-140 — SUPERSEDED

Replaced by: AF-139

(Do not export to Jira)

### AF-141 — SUPERSEDED

Replaced by: AF-139

(Do not export to Jira)

### AF-142 — SUPERSEDED

Replaced by: AF-139

(Do not export to Jira)

### AF-143 — SUPERSEDED

Replaced by: AF-137

(Do not export to Jira)

### AF-144 — Validate closed-enclosure thermal behavior and operating brightness

**Milestone:** MF
**Depends on:** AF-139, AF-147, AF-120, AF-133
**Labels:** validation thermal power critical-path
**Safety:** MAINS, 5V-HIGH-CURRENT, HUB75
**Stop condition:** Stop output, switch off, and disconnect the wall plug before inspection if there is smoke, smell, abnormal PSU/controller/wiring/air heating, voltage instability, reset, blanking, or unsafe enclosure behavior. Do not open the enclosure while energized.

#### Do
1. Close the enclosure and run normal dashboard content at AF-120's recorded ceiling for at least 60 minutes using the approved energization procedure.
2. Record PSU, controller, wiring, and enclosure-air temperatures, display stability, voltage observations, ambient conditions, and any dimming/abort behavior in `docs/pm/evidence/AF-144-closed-thermal.md`.
3. Set and record dim behavior only from measured closed-enclosure evidence.

#### Done when
- The closed frame is stable at the documented operating setting with complete measured evidence.
- The record identifies any dim behavior, limitation, or correction path without inventing a thermal limit.

#### If it fails
Stop, switch off, disconnect at the wall, and allow the enclosure to cool before inspection. Correct the thermal, airflow, power, routing, controller, or display cause before repeating the full closed test.

### AF-145 — SUPERSEDED

Replaced by: AF-144

(Do not export to Jira)

### AF-146 — Validate closed-frame Wi-Fi

**Milestone:** MF
**Depends on:** AF-144, AF-133
**Labels:** validation networking recovery-test critical-path
**Safety:** MAINS, HUB75
**Stop condition:** Do not open or move powered enclosure wiring. Stop output, switch off, and disconnect the wall plug before physical mitigation if connectivity loss coincides with abnormal display, controller, or power behavior.

#### Do
1. With the enclosure closed and panels on, measure and record dashboard connectivity and service behavior from the intended installation location.
2. If reception is inadequate, apply a documented antenna, placement, or network mitigation with the enclosure de-energized for any physical change, then repeat the measurement.
3. Save signal and mitigation evidence in `docs/pm/evidence/AF-146-closed-wifi.md`.

#### Done when
- Evidence supports reliable dashboard connectivity with the enclosure closed at the intended location.
- Any mitigation is documented and retested.

#### If it fails
Preserve measurements and correct placement, antenna, enclosure, or network conditions before wall mounting.

### AF-149 — Finish, wall-mount, and run final 72-hour validation

**Milestone:** MF
**Depends on:** AF-144, AF-146, AF-147, AF-133
**Labels:** hardware mechanical validation stability critical-path
**Safety:** MAINS, 5V-HIGH-CURRENT, HUB75
**Stop condition:** Disconnect at the wall before mounting, opening, or changing enclosure wiring. Stop the run and de-energize for insecure mounting, exposed mains parts, PE concern, smoke, smell, abnormal heating, display failure, or any condition that could make the wall installation unsafe.

#### Do
1. Select/install finish and rated wall hardware for the measured wall and frame load; mount the frame and verify secure attachment, cable exit, service access, and no strain on conductors.
2. Run normal dashboard content for at least 72 hours with periodic SSH, photo, thermal, Wi-Fi, data, and service observations.
3. Save mounting evidence and the full run record in `docs/pm/evidence/AF-149-mounted-72h-validation.md`.

#### Done when
- The frame is securely wall-mounted with documented safe cable routing and service access.
- The installed system completes a >=72-hour normal-dashboard record with no unresolved product failure.

#### If it fails
Stop and de-energize. Secure or remove the frame as needed, preserve evidence, and correct the mounting, enclosure, power, thermal, Wi-Fi, service, data, or display cause before a new complete run.

### AF-150 — SUPERSEDED

Replaced by: AF-149

(Do not export to Jira)

### AF-151 — MF final gate

**Milestone:** MF
**Depends on:** AF-149
**Labels:** validation gate-pass decision critical-path

#### Do
1. Review AF-134 through AF-149 evidence for enclosure constraints, placement, ventilation, backplate alignment, PE/routing/strain relief, closed thermal behavior, Wi-Fi, service access, dashboard content, and wall-mounted 72-hour operation.
2. Save an explicit PASS/FAIL result with criterion-to-evidence links in `docs/pm/evidence/AF-151-mf-final-gate.md`.

#### Done when
- The final gate explicitly passes or fails every safety and product criterion with linked evidence.
- A PASS proves the mounted finished product; a FAIL identifies the correction path without obscuring the failed evidence.

#### If it fails
Name the failed criterion and return to the named prerequisite task before repeating the gate.
