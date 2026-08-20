# Phase 13 — Conditional ESP32 Custom PCB (Cond-X)

Covers the optional ESP32 HCT adapter PCB only when ADR-016 selects multi-ESP32. The seven survivors preserve all 19 historical KiCad stages through their `Do`/`Done when` coverage or their superseded lineage stubs.

**AF-096 post-decision normalization:** Before ADR-016, every active Cond-X survivor carries `conditional blocked blocked:adr-016` and `pcb`, plus `Applies if: ADR-016 selects multi-ESP32`. After AF-096, the multi-ESP32 winner removes those three labels and `Applies if`, adds `critical-path`, and retains `controller-esp32 pcb`. The WF4 loser retains or adds `conditional blocked blocked:adr-016 pcb`, removes `critical-path`, and replaces `Applies if` with exactly `Skip — ADR-016 selected WF4`. These are the same two conditional classes used elsewhere; do not create a third class.

### AF-152 — Freeze validated ESP32 mechanical/GPIO interface

**Milestone:** Cond-X
**Depends on:** AF-104, AF-095, AF-052
**Labels:** hardware firmware controller-esp32 pcb validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32.
**Safety:** 5V-HIGH-CURRENT, HUB75
**Stop condition:** Keep the ESP32, HCT adapter, and panels de-energized while measuring or reconciling interfaces. Stop if delivered geometry, connector keying, GPIO evidence, or row behavior contradicts the proposed footprint or mapping.

#### Do
1. Measure the actual ESP32 board, headers, connectors, clearances, and no-go areas; record the footprint and mounting constraints rather than assuming a board revision.
2. Reconcile the AF-052 mapping with AF-067, AF-087, and M3 evidence. Freeze the proven GPIO mapping, firmware baseline, connector orientation, and row behavior; record every PCB-blocking discrepancy.
3. Save measurements, photos, mapping, and review result in `docs/pm/evidence/AF-152-interface-freeze.md`.

#### Done when
- The footprint, no-go areas, validated GPIO mapping, firmware baseline, and row behavior are reviewable from evidence.
- No PCB-blocking assumption remains unresolved.

#### If it fails
Keep the existing proven perfboard path intact and de-energized. Resolve the mechanical, GPIO, connector, or evidence contradiction before schematic capture.

### AF-153 — SUPERSEDED

Replaced by: AF-152

(Do not export to Jira)

### AF-154 — Produce and electrically review complete HCT adapter schematic

**Milestone:** Cond-X
**Depends on:** AF-152
**Labels:** hardware controller-esp32 pcb validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32.
**Safety:** 5V-HIGH-CURRENT, HUB75
**Stop condition:** Do not release the schematic if any 5 V entry, ground return, HCT decoupling, connector orientation, pin 1, polarity, or GPIO-to-HUB75 net is ambiguous. Keep all hardware de-energized while checking the reference rig.

#### Do
1. Capture ESP32, both HCT245 stages, keyed HUB75, and safe 5 V entry from the AF-152 frozen interface; name signals and show pin 1, polarity, ground return, decoupling, and current paths.
2. Review component values, packages/footprints, availability and substitutions, connector orientation, power entry, and routing intent against the measured hardware and `hardware/schematics/PIN_LEVEL_APPENDIX.md`.
3. Record the reviewed revision and open issues, run ERC, resolve errors and critical warnings, and bound every remaining non-critical waiver in `docs/pm/evidence/AF-154-schematic-review.md`.

#### Done when
- The schematic contains every required net, component, polarity/orientation rule, and current-path annotation.
- Component, power, connector, and release reviews are recorded; ERC has zero errors and only justified waivers.

#### If it fails
Do not begin layout. Correct the named schematic, mapping, power, connector, or review issue and rerun ERC.

### AF-155 — SUPERSEDED

Replaced by: AF-154

(Do not export to Jira)

### AF-156 — SUPERSEDED

Replaced by: AF-154

(Do not export to Jira)

### AF-157 — SUPERSEDED

Replaced by: AF-154

(Do not export to Jira)

### AF-158 — SUPERSEDED

Replaced by: AF-154

(Do not export to Jira)

### AF-159 — SUPERSEDED

Replaced by: AF-154

(Do not export to Jira)

### AF-160 — Produce and validate fabrication-ready PCB layout

**Milestone:** Cond-X
**Depends on:** AF-154
**Labels:** hardware controller-esp32 pcb validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32.
**Safety:** 5V-HIGH-CURRENT, HUB75
**Stop condition:** Do not release layout with an unresolved DRC error, an unjustified waiver, a blocked connector, unseen pin 1, inadequate clearance, or unverified service access. Keep the reference hardware de-energized during fit checks.

#### Do
1. Place and route components and connectors with the AF-152 measured clearances, mounting/no-go areas, connector access, visible pin 1, trace/plane rules, decoupling placement, orientation, and serviceability requirements.
2. Review connector reach, power/current paths, ground return, component access, and mechanical fit against the actual hardware.
3. Run DRC, resolve errors, document bounded waivers, and release the reviewed layout revision in `docs/pm/evidence/AF-160-layout-review.md`.

#### Done when
- The layout has safe, reviewable fabrication geometry and no unresolved physical or service-access conflict.
- DRC has zero errors and only justified waivers tied to the released revision.

#### If it fails
Keep fabrication files unreleased. Correct the placement, routing, clearance, connector, or rule violation and repeat the review and DRC.

### AF-161 — SUPERSEDED

Replaced by: AF-160

(Do not export to Jira)

### AF-162 — SUPERSEDED

Replaced by: AF-160

(Do not export to Jira)

### AF-163 — Generate and review complete fabrication/assembly package

**Milestone:** Cond-X
**Depends on:** AF-160
**Labels:** hardware controller-esp32 pcb validation conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32.

#### Do
1. Generate and inspect Gerbers and drill files from the released layout.
2. Export and review BOM quantities, references, approved substitutions, and package choices; generate and inspect CPL/placement data where assembly uses it, or record why it is not applicable.
3. Cross-check Gerbers, drills, BOM, CPL, revision, and archive completeness. Save the mutually consistent release checklist in `docs/pm/evidence/AF-163-fabrication-package.md`.

#### Done when
- Archived Gerber/drill, BOM, and applicable CPL data are mutually consistent, reviewed, revisioned, and ready to order.
- The package checklist records every generated or intentionally inapplicable manufacturing artifact.

#### If it fails
Do not order. Return the discrepancy to schematic, layout, BOM, or placement generation and rebuild the complete package.

### AF-164 — SUPERSEDED

Replaced by: AF-163

(Do not export to Jira)

### AF-165 — SUPERSEDED

Replaced by: AF-163

(Do not export to Jira)

### AF-166 — SUPERSEDED

Replaced by: AF-163

(Do not export to Jira)

### AF-167 — Order, receive, and electrically inspect PCB prototype

**Milestone:** Cond-X
**Depends on:** AF-163
**Labels:** hardware controller-esp32 pcb validation conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32.
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Keep every board unpowered for receipt and continuity work. Stop acceptance if a board has physical damage, an unexpected open net, or VCC-to-GND continuity; route repeated faults back to the design rather than energizing the board.

#### Do
1. Order the reviewed release and save order, revision, quantity, and supplier evidence.
2. On receipt, inspect the boards for damage, markings, outline, holes, and connector footprints.
3. Perform every-net continuity and isolation testing on the prototype batch; explicitly check VCC-to-GND isolation and distinguish a one-board manufacturing fault from a repeated design fault. Save results in `docs/pm/evidence/AF-167-prototype-inspection.md`.

#### Done when
- Order, receipt, visual inspection, and every-net continuity/isolation evidence accept the prototype batch or identify a design-fault return path.
- No board proceeds to population with an unresolved power short or repeated unexplained net fault.

#### If it fails
Keep all boards unpowered. Quarantine failed boards and return repeated or design-linked faults to AF-154 or AF-160 before a replacement order.

### AF-168 — Populate and inspect one PCB prototype

**Milestone:** Cond-X
**Depends on:** AF-167
**Labels:** hardware controller-esp32 pcb validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32.
**Safety:** 5V-HIGH-CURRENT, HUB75
**Stop condition:** Keep the PCB unpowered during population and inspection. Stop if component value, package, polarity, pin 1, connector orientation, solder joint, or bridge is uncertain; inspect and correct before the board enters a powered rig.

#### Do
1. Populate one accepted board using the released BOM and assembly record; verify each component value, package, polarity, pin 1, connector orientation, decoupling, and solder joint.
2. Record every assembly deviation and complete unpowered continuity/isolation checks before the PCB is introduced into the row rig.
3. Save top/bottom photos and the inspection record in `docs/pm/evidence/AF-168-population-inspection.md`.

#### Done when
- One PCB is populated, visually inspected, and electrically checked with every assembly deviation recorded.
- The board is suitable to enter the powered row-validation rig without unresolved assembly uncertainty.

#### If it fails
Keep the board unpowered, correct the assembly or inspection fault, and repeat the affected unpowered check.

### AF-169 — Validate PCB row and replace perfboard implementation

**Milestone:** Cond-X
**Depends on:** AF-168, AF-067, AF-087
**Labels:** hardware controller-esp32 pcb validation safety-review conditional blocked blocked:adr-016
**Applies if:** ADR-016 selects multi-ESP32.
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** De-energize the row, ESP32, PCB, and panels before every connection change; never hot-plug. Stop output and de-energize on a blank, garble, reset, smoke, smell, abnormal heating, or new artifact relative to the proven perfboard baseline.

#### Do
1. Validate the fabricated board in the proven 256x64 row using the same ESP32, cables, configuration, and standard patterns as the perfboard reference. Compare pattern behavior and run at least one hour of stability observation.
2. With the rig de-energized, replace the perfboard adapter with the validated PCB in the same path; do not leave the perfboard in series or parallel. Recheck orientation, connector keying, and 5 V polarity before energizing through the proven procedure.
3. Update wiring, photo, and canonical documentation evidence so the validated rig identifies the v1.0 PCB. Save comparison, stability, swap, and documentation evidence in `docs/pm/evidence/AF-169-pcb-row-validation.md`.

#### Done when
- The PCB adapter drives the proven row with pattern behavior matching the perfboard reference and >=1-hour stability evidence.
- The validated rig uses the v1.0 PCB without perfboard in its signal path, and canonical evidence identifies that final hardware.

#### If it fails
Stop and de-energize. Restore the known-good perfboard reference only after preserving comparison evidence; isolate assembly, mapping, layout, power, cable, configuration, or controller behavior before redesign or retest.

### AF-170 — SUPERSEDED

Replaced by: AF-169

(Do not export to Jira)
