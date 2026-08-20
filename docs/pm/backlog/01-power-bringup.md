# Phase 01 — Safe Power Bringup (M0)

Covers crimp practice, safe AC mains harness assembly, PSU no-load validation (EXP-002), and panel polarity/HUB75 verification with first-panel cold power bringup (EXP-003).

---

### AF-012 — SUPERSEDED

Replaced by: AF-014

(Do not export to Jira)

---

### AF-013 — Practice ferrule and spade crimping on offcut wire before mains work

**Milestone:** M0
**Depends on:** AF-011
**Labels:** hardware safety-review power
**Safety:** MAINS
**Stop condition:** Keep the wall plug disconnected throughout crimp practice; do not use a practice result that fails inspection or pull test on mains wiring.
**Resolves:** U-003

#### Do
1. Verify the delivered crimp tool has the correct die/range for the delivered insulated 6.3 mm spade terminals.
2. Cut 10 sacrificial 5 cm wire offcuts; verify and record actual conductor colors before relying on them.
3. Strip insulation to the delivered terminal manufacturer's required length without nicking strands.
4. Crimp five bootlace ferrule samples, checking for a square crimp, no stray strands, and correctly seated insulation.
5. Crimp five insulated 6.3 mm female spade samples, checking conductor and insulation-wing engagement.
6. Perform a firm manual pull test on every sample, label them F1-F5 and S1-S5, and photograph the inspection set.

#### Done when
- Five ferrule samples pass visual inspection for full strand capture, square crimp, no insulation pinch, and no metal cracks, plus a pull test.
- Five spade samples pass visual inspection for full strand capture, an undeformed tongue, insulation-wing grip, and no exposed wire gap, plus a pull test.
- A group photo of all 10 labeled samples is recorded in the evidence log.

#### If it fails
Adjust the stripper depth or ratchet nest and repeat on sacrificial wire until five consecutive ferrules and five consecutive spades pass; do not use the tool on mains wiring first.

---

### AF-014 — Assemble and verify AC mains harness

**Milestone:** M0
**Depends on:** AF-011, AF-013
**Labels:** hardware safety-review power polarity-verify
**Safety:** MAINS
**Stop condition:** Keep the wall plug disconnected before every wiring or probe change; do not energize until every MAINS checklist check passes and the wall plug remains accessible.
**Resolves:** U-001, U-003, U-004, U-026
**Procedure:** EXP-002

#### Do
1. Apply `docs/pm/runbooks/safety.md` Section 2 before any mains work: disconnect wall power, make the wall plug accessible, and use only the delivered insulated 6.3 mm female spades at C14 tabs and correctly sized bootlace ferrules at PSU screw terminals.
2. With the C14 and PSU disconnected, install the candidate T2A 5x20 mm slow-blow fuse, verify its marking and fit, then use the C14 markings and continuity testing to identify the delivered L, N, and PE tabs. Record the tab mapping and the delivered conductor color assigned to each function; do not infer function from conductor color.
3. Confirm the C14 fuse and switch route the identified L path, determine and record whether switching is single- or double-pole, and confirm PE is direct, unswitched, and unfused. Verify L-to-N is open with the switch OFF and L/N-to-PE isolation holds in both switch states.
4. Cut, strip, and terminate the L, N, and PE conductors with service slack and strain relief. Connect the identified switched L tab to PSU L, N tab to PSU N, and PE tab to PSU FG; fully seat every spade, tighten every PSU terminal, and leave no bare strands or exposed copper.
5. Perform the MAINS pre-energization visual and meter checks: direct L and N continuity to their PSU terminals as applicable, PE-to-FG and chassis continuity in both switch states, L/N-to-PE isolation in both switch states, and a wiggle test of every conductor while monitoring continuity.
6. Inspect all three spade and all three ferrule terminations under bright light for full insulation coverage, strain relief, service slack, secure screws, and no damaged insulation. Record the completed checklist and measured resistance values in the AF-014 evidence log.

#### Done when
- The delivered C14 L/N/PE tab mapping, conductor-color assignment, fuse fit/rating, and switch-pole behavior are recorded from markings and continuity evidence.
- L, N, and PE are connected to their corresponding PSU L, N, and FG terminals using insulated, strain-relieved spade and ferrule terminations with no exposed conductor.
- PE is direct, unswitched, and unfused, with recorded continuity to PSU FG and chassis in both switch states.
- L and N continuity, the disconnected C14's L-to-N open circuit with the switch OFF, and L/N-to-PE isolation in both switch states pass with recorded readings.
- The visual inspection and conductor wiggle tests find no loose terminal, intermittent continuity, damaged insulation, or exposed copper.
- The completed pre-energization checklist confirms the wall plug is accessible and the harness is safe to proceed to AF-018.

#### If it fails
Keep the wall plug disconnected. Rework only the failed conductor with a fresh terminal, or quarantine and replace a defective C14, fuse, cable, or PSU; then repeat the complete routing, visual, continuity, and isolation checks before energization.

---

### AF-015 — SUPERSEDED

Replaced by: AF-014

(Do not export to Jira)

---

### AF-016 — SUPERSEDED

Replaced by: AF-014

(Do not export to Jira)

---

### AF-017 — SUPERSEDED

Replaced by: AF-014

(Do not export to Jira)

---

### AF-018 — PSU no-load energization and voltage hold test (EXP-002)

**Milestone:** M0
**Depends on:** AF-014
**Labels:** hardware safety-review power validation
**Procedure:** EXP-002
**Safety:** MAINS
**Stop condition:** Immediately switch OFF and unplug if smoke, pop, buzzing, burning odor, or abnormal heating occurs; disconnect the wall plug before every device-side connection change.
**Resolves:** U-022

#### Do
1. Verify the AF-014 pre-energization checklist passed, every DC output terminal (+V and COM) is bare and disconnected from all loads, and the C14 switch is OFF.
2. With the wall plug disconnected, connect C13 to the C14 inlet; plug the wall end in last, stand at arm's length to the side of the PSU, and switch ON.
3. Observe the PSU indicator if present and listen for abnormal noise, popping, or arcing. Measure and record DC voltage across +V and COM.
4. If AF-011 recorded a supported voltage-adjust control and procedure, use only that documented procedure. Otherwise do not adjust the PSU; record the measured result and stop/escalate if the EXP-002 approximate-5 V and stability outcome is not met.
5. Hold the no-load test for 10 minutes, recording voltage, casing thermal state, and observations at t=0, 1, 5, and 10 minutes per EXP-002.
6. Switch OFF, unplug the wall end, and only then touch or change any device-side connection; save the EXP-002 evidence log.

#### Done when
- The PSU energizes without smoke, popping, buzzing, burning odor, or abnormal heating.
- The measured +V-to-COM output and any supported documented adjustment are recorded.
- A 10-minute hold records voltage and thermal observations at t=0, 1, 5, and 10 minutes and meets EXP-002's approximate-5 V, stable-output outcome.
- The EXP-002 measurement table is saved in the evidence log.

#### If it fails
Switch OFF immediately and unplug from the wall. Do not attempt an undocumented adjustment; record the result and stop/escalate, or replace the PSU if a documented adjustment cannot achieve the EXP-002 outcome.

---

### AF-019 — SUPERSEDED

Replaced by: AF-021

(Do not export to Jira)

---

### AF-020 — SUPERSEDED

Replaced by: AF-021

(Do not export to Jira)

---

### AF-021 — Verify panel power/HUB75 polarity and cold-power first panel

**Milestone:** M0
**Depends on:** AF-011, AF-018
**Labels:** hardware safety-review power polarity-verify validation
**Procedure:** EXP-003
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** Keep PSU power OFF and the wall plug disconnected before moving any panel-power or HUB75 connector; power OFF immediately for smoke, burning smell, rapidly increasing temperature, visible damage, or abnormal power behavior.
**Resolves:** U-002, U-003, U-031

#### Do
1. Apply `docs/pm/runbooks/safety.md` Sections 3 and 5: do not hot-plug HUB75, use separate parallel panel-power branches, do not daisy-chain panel power, and do not measure the PSU main current through a standard multimeter shunt.
2. With the PSU OFF and unplugged, use AF-011 receipt photos and a multimeter to verify every panel's power-header polarity against its delivered silkscreen and decoupling-capacitor polarity; record and mark each panel's V+/GND orientation.
3. With power still removed, identify and record every panel's HUB75 DATA_IN/DATA_OUT labels, pin-1 indicator, and key orientation. Test the delivered ribbon cable's end-to-end continuity and no signal-to-ground cross-short; do not mate an unkeyed connector until delivered-hardware markings establish the orientation.
4. For every harness branch, record delivered construction, branch label, positive-to-V+ continuity, COM-to-GND continuity, and positive-to-GND/COM-to-V+ isolation. Apply an orientation index where connector keying is insufficient.
5. Connect only verified harness branch #1 between PSU +V/COM and panel #1, recheck polarity at the mated panel connector, and confirm that no controller or HUB75 ribbon is connected to panel #1.
6. Energize panel #1 from the side, observe for 10 seconds, measure and record voltage at the PSU terminals and panel #1 connector, then complete EXP-003's 10-15 minute stability hold while recording voltage delta and abnormal heat, smell, noise, or behavior.
7. Switch OFF and unplug the PSU before changing any connection; save the per-panel/per-branch checklist and EXP-003 measurements in the AF-021 evidence log.

#### Done when
- Every panel and harness branch has an individually recorded polarity, orientation, continuity, and isolation result, including panels #2-#6 and branches #2-#6.
- Every panel's HUB75 IN/OUT, pin-1, and keyed orientation are recorded, and the delivered ribbon cable passes continuity and no-cross-short checks.
- Panel #1 is connected through its verified parallel branch with no controller or HUB75 ribbon attached before cold-power energization.
- PSU-terminal and panel #1-terminal voltages, their delta, and 10-15 minute EXP-003 stability observations are recorded.
- Panel #1 completes the cold-power test with no smoke, burning smell, abnormal noise, rapidly increasing temperature, visible damage, or abnormal power behavior.
- The evidence log preserves the individual panel/branch checklist and cold-power measurements needed for the M0 gate.

#### If it fails
Power OFF and unplug immediately. Quarantine the failed panel, cable, or harness branch; inspect the recorded polarity, connector orientation, insulation, and continuity evidence, correct or replace only the failed item, and repeat the affected unpowered verification before another energization attempt.

---

### AF-022 — SUPERSEDED

Replaced by: AF-021

(Do not export to Jira)

---

### AF-023 — SUPERSEDED

Replaced by: AF-021

(Do not export to Jira)

---

### AF-024 — Milestone M0 exit gate review and verification

**Milestone:** M0
**Depends on:** AF-018, AF-021, AF-180
**Labels:** docs validation

#### Do
1. Review AF-011 intake evidence and AF-180 BOM status, including all discrepancies and photo mappings.
2. Review AF-014 MAINS harness evidence, AF-018 EXP-002 no-load log, and AF-021 EXP-003 polarity, HUB75, branch, and cold-power evidence.
3. Verify every M0 exit criterion in `docs/pm/backlog/README.md` Section 4 against its evidence artifact; confirm evidence is committed in `docs/pm/evidence/` and photos are archived in `hardware/photos/`.
4. Record the gate result and block M1 unless every required receipt, safety, PSU, and first-panel criterion passes.

#### Done when
- EXP-001, EXP-002, and EXP-003 evidence is complete, reviewed, and committed.
- MAINS safety checks, DC polarity checks, HUB75 orientation checks, and first-panel cold-power evidence have no unresolved defect.
- `docs/BOM.md` reflects verified receipt status and documents unresolved or quarantined items.
- The M0 exit gate is explicitly approved, unblocking M1 parallel controller tracks.

#### If it fails
Block Milestone 1. Return to the surviving source task for each missing evidence item, unresolved discrepancy, or failed safety check, and repeat this gate only after the correction is evidenced.
