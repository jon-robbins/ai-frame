# Phase 01 — Safe Power Bringup (M0)

Covers C14 mains inlet wiring, ferrule/spade crimping, unpowered AC isolation and continuity verification, switching power supply no-load energization (EXP-002), and single-panel cold DC power bringup (EXP-003).

---

### AF-012 — Verify C14 rear tab routing through fuse and switch

**Milestone:** M0
**Depends on:** AF-178
**Labels:** hardware safety-review power polarity-verify
**Safety:** MAINS
**Stop condition:** Disconnect wall plug before probing C14 inlet.
**Resolves:** U-001, U-026

#### Do
1. Disconnect C14 inlet assembly completely from any AC mains supply or PSU wiring.
2. Install candidate T2A 5×20mm slow-blow fuse into C14 fuse carriage and insert into inlet.
3. Using multimeter in continuity / resistance mode, test continuity from Line (L) male prong to fuse terminal.
4. Verify continuity from fuse output tab to switch input tab, and from switch output tab to rear L wire tab.
5. Test and record whether C14 switch is single-pole or double-pole (determine whether L and N are both switched, or L only, during continuity testing to rear N wire tab).
6. Verify Protective Earth (PE) center prong connects directly to rear PE ground tab and is completely unswitched and unfused.
7. Verify open circuit between L and N tabs with switch in OFF position.
8. Verify open circuit between L/N and PE tabs in both switch positions.

#### Done when
- Direct continuity confirmed through fuse and switch to L rear tab when switch is ON.
- Switch pole configuration tested and recorded (single-pole vs double-pole switching behavior documented).
- Direct PE continuity confirmed and measured resistance recorded; ground path is completely unswitched in all switch positions.
- Open circuit confirmed between L and N when switch is OFF.
- Complete electrical isolation confirmed between L/N and PE in all switch states.

#### If it fails
Do NOT attempt wire rework if internal C14 module routing is shorted or miswired; quarantine and replace the C14 inlet assembly.

---

### AF-013 — Ferrule and spade crimp practice and inspection on offcut wire

**Milestone:** M0
**Depends on:** AF-178, AF-179
**Labels:** hardware safety-review power
**Resolves:** U-003

#### Do
1. Verify the delivered crimp tool has the correct die/range for the purchased insulated 6.3 mm spade terminals.
2. Cut 10 sacrificial 5 cm offcut lengths from conductors of the purchased 3-core 0.75 mm² AC cable or wire offcuts (verify actual conductor colors on receipt).
3. Strip insulation from wire ends to the terminal manufacturer's/delivered-terminal required length without nicking conductor strands.
4. Practice crimping 5 bootlace ferrule samples using the ratchet ferrule crimper (verify square crimp, no stray strands, insulation properly seated).
5. Practice crimping 5 insulated 6.3 mm female spade terminal samples using the ratchet crimp tool (verify two-stage crimp on conductor and insulation wings).
6. Perform firm manual pull test on each sample to verify zero wire movement or pullout.
7. Label samples F1–F5 and S1–S5 and capture inspection photo.

#### Done when
- 5 ferrule samples pass 4-point visual inspection (full strand capture, square crimp, no insulation pinch, no metal cracks) and pull test.
- 5 spade samples pass 4-point visual inspection (full strand capture, undeformed tongue, insulation wing grip, zero exposed wire gap) and pull test.
- Group photo of 10 labeled samples captured and recorded in evidence log.

#### If it fails
If any sample fails visual check or pull test, adjust stripper depth and ratchet nest; repeat practice until 5 consecutive ferrules and spades pass cleanly.

---

### AF-014 — Wire C14 Live conductor to PSU L terminal

**Milestone:** M0
**Depends on:** AF-012, AF-013
**Labels:** hardware safety-review power
**Safety:** MAINS
**Stop condition:** Keep AC power cord unplugged from wall while wiring mains terminals.
**Resolves:** U-001, U-003

#### Do
1. Cut Live conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically brown) to the measured installed length with sufficient service slack to avoid strain and permit terminal servicing.
2. Strip insulation to required terminal length and crimp a 6.3 mm insulated female spade terminal onto the C14 end.
3. Strip insulation to required terminal length and crimp a bootlace ferrule onto the PSU end.
4. Insert ferrule into PSU L screw-barrier clamp and tighten screw firmly.
5. Push 6.3 mm spade connector firmly onto C14 Live output tab with full insulation seating.
6. Measure continuity from C14 Line male prong to PSU L terminal screw head with DMM.
7. Perform wiggle test (wiggle wire at both ends 3 times while monitoring continuous DMM beep).

#### Done when
- Live wire connects C14 switched Live tab to PSU L screw terminal with single continuous conductor.
- Direct continuity confirmed and measured resistance recorded from C14 L prong to PSU L screw head when switch is ON; reading remains stable during wiggle test.
- Wiggle test shows zero dropouts or intermittent continuity.
- Both crimp terminations pass 4-point visual inspection with service slack intact.

#### If it fails
If continuity drops or connection is loose, undo screw/spade, inspect crimp barrels, re-crimp with fresh terminal, and re-test.

---

### AF-015 — Wire C14 Neutral conductor to PSU N terminal

**Milestone:** M0
**Depends on:** AF-014
**Labels:** hardware safety-review power
**Safety:** MAINS
**Stop condition:** Keep AC power cord unplugged from wall while wiring mains terminals.
**Resolves:** U-001, U-003

#### Do
1. Cut Neutral conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically blue) to the measured installed length with sufficient service slack to avoid strain and permit terminal servicing.
2. Strip insulation to required terminal length and crimp a 6.3 mm insulated female spade terminal onto the C14 end.
3. Strip insulation to required terminal length and crimp a bootlace ferrule onto the PSU end.
4. Insert ferrule into PSU N screw-barrier clamp and tighten screw firmly.
5. Push 6.3 mm spade connector firmly onto C14 Neutral output tab with full insulation seating.
6. Measure continuity from C14 Neutral male prong to PSU N terminal screw head with DMM.
7. Perform wiggle test (wiggle wire at both ends 3 times while monitoring continuous DMM beep).

#### Done when
- Neutral wire connects C14 Neutral tab to PSU N screw terminal with single continuous conductor.
- Direct continuity confirmed and measured resistance recorded from C14 N prong to PSU N screw head when switch is ON; reading remains stable during wiggle test.
- Wiggle test shows zero dropouts or intermittent continuity.
- Both crimp terminations pass 4-point visual inspection with service slack intact.

#### If it fails
If continuity drops or connection is loose, undo screw/spade, inspect crimp barrels, re-crimp with fresh terminal, and re-test.

---

### AF-016 — Wire C14 Protective Earth conductor to PSU FG terminal

**Milestone:** M0
**Depends on:** AF-015
**Labels:** hardware safety-review power polarity-verify
**Safety:** MAINS
**Stop condition:** Keep AC power cord unplugged from wall while wiring mains terminals.
**Resolves:** U-001, U-003, U-004

#### Do
1. Cut Protective Earth conductor from the purchased 3-core 0.75 mm² AC cable (verify actual conductor colors on receipt, typically green/yellow) to the measured installed length with sufficient service slack to avoid strain and permit terminal servicing.
2. Strip insulation to required terminal length and crimp a 6.3 mm insulated female spade terminal onto the C14 end.
3. Strip insulation to required terminal length and crimp a bootlace ferrule onto the PSU end.
4. Insert ferrule into PSU FG (Frame Ground) screw-barrier clamp and tighten screw firmly.
5. Push 6.3 mm spade connector firmly onto C14 Earth/Ground tab with full insulation seating.
6. Measure continuity from C14 center Earth male prong to PSU FG terminal and PSU metal chassis.
7. Verify PE continuity with switch in OFF position, then repeat with switch in ON position.
8. Perform spot isolation check (verify no continuity between PE and L or N).

#### Done when
- Earth wire connects C14 ground tab directly to PSU FG screw terminal and chassis.
- Direct PE continuity confirmed and measured resistance recorded from C14 Earth prong to PSU FG terminal and chassis in both OFF and ON switch states; reading remains stable during wiggle test.
- Wiggle test confirms uninterrupted earth bonding.
- Zero continuity (complete electrical isolation) between PE and L or N terminals.

#### If it fails
If PE continuity depends on switch state or is open, immediately remove all AC wiring and re-verify C14 internal routing per AF-012.

---

### AF-017 — Visual and continuity/isolation inspection of AC mains wiring

**Milestone:** M0
**Depends on:** AF-016
**Labels:** hardware safety-review power polarity-verify
**Safety:** MAINS
**Stop condition:** Do not energize until all visual, continuity, and isolation checks pass.
**Resolves:** U-001, U-004

#### Do
1. Perform 7-point visual inspection under bright light with magnifying glass:
   - Check all 6 crimp joints (3 spade, 3 ferrule) for zero stray conductor strands.
   - Verify all 3 PSU screw terminals are tightened firmly.
   - Verify wire insulation is intact with no nicks or pinching.
   - Verify zero bare copper exposed outside terminals or screw clamps.
   - Verify cable routing maintains slack and strain relief.
2. Perform electrical continuity and isolation verification:
   - Verify Line continuity from C14 inlet L prong through fuse and switch to PSU L terminal.
   - Verify Neutral continuity from C14 inlet N prong to PSU N terminal.
   - Verify Protective Earth continuity from C14 center PE prong to PSU FG terminal and chassis.
   - Verify electrical isolation (no short circuit) between L and PE, and between N and PE in both switch states.
3. Wiggle all 3 wires during checks to ensure no intermittent connections or shorts.
4. Sign off pre-energization safety checklist in evidence log.

#### Done when
- 7-point visual inspection passes with zero defects or exposed conductors.
- Direct continuity verified for L, N, and PE circuits from C14 inlet to corresponding PSU terminals.
- Electrical isolation confirmed with zero short circuits from L→PE or N→PE in all switch states.
- Zero intermittent contact detected during wire wiggle test.
- Pre-energization checklist signed off in `docs/pm/evidence/AF-017-ac-wiring-inspection.md`.

#### If it fails
DO NOT energize. Identify any failed joint, disassemble, re-crimp, and re-run all visual and electrical checks from scratch.

---

### AF-018 — PSU no-load energization and voltage hold test (EXP-002)

**Milestone:** M0
**Depends on:** AF-017
**Labels:** hardware safety-review power validation
**Procedure:** EXP-002
**Safety:** MAINS
**Stop condition:** Immediately switch OFF and unplug if smoke, pop, buzzing, or burning odor occurs.
**Resolves:** U-022

#### Do
1. Verify all DC output terminals (+V and COM) are completely bare and disconnected from any loads.
2. Verify C14 switch is in OFF position.
3. With the C14 switch OFF, fully connect the C13 connector to the C14 inlet while the wall plug is disconnected. Plug the wall end into the outlet last. After the test, switch OFF and unplug the wall end before touching or changing any device-side connection.
4. Stand at arm's length to the side of the PSU (do not lean over PSU) and flip C14 switch ON.
5. Observe power indicator if present and listen for abnormal acoustic noise, popping, or arcing.
6. Using DMM in DC voltage mode, measure output voltage across +V and COM terminals.
7. If AF-178 confirmed a labeled voltage-adjust control and adjustment procedure, adjustment may be performed as documented. If AF-178 did not confirm a supported V-ADJ control and procedure, do not attempt adjustment; record the measured output and assess it against the EXP-002 approximate-5 V and stability outcome.
8. Execute 10-minute hold test, logging DC voltage across +V and COM, casing thermal state, and observations at t = 0, 1, 5, and 10 minutes per EXP-002.
9. Switch C14 OFF, unplug mains cord from wall outlet, and record measured values in EXP-002 evidence log.

#### Done when
- Power indicator on A-200-5 illuminates (if present) without popping, buzzing, or smoke.
- Output voltage across +V and COM is measured and recorded in the evidence log; if AF-178 confirmed a supported control/procedure, any adjustment is documented, and if AF-178 did not confirm one, no adjustment is attempted.
- 10-minute hold completed with stable output and no abnormal behavior (no excessive heating, smell, buzzing, or voltage instability), with the EXP-002 approximate-5 V outcome met; if that outcome is not met without a supported adjustment, the measured result is recorded and the task is stopped/escalated.
- EXP-002 measurement table (no-load voltage, voltage after 10 min, ambient temp, observations) logged in evidence log.

#### If it fails
Switch OFF immediately and unplug from wall. If AF-178 did not confirm a supported V-ADJ control and procedure, do not attempt adjustment; record the measured output and stop/escalate if the EXP-002 approximate-5 V or stability outcome is not met. If AF-178 confirmed a supported control and procedure but the documented adjustment cannot achieve the EXP-002 outcome, mark PSU defective and request supplier replacement.

---

### AF-019 — Verify panel power harness branch #1 polarity

**Milestone:** M0
**Depends on:** AF-018, AF-179
**Labels:** hardware safety-review power polarity-verify
**Resolves:** U-002, U-003

#### Do
1. Verify PSU is switched OFF and unplugged from wall power.
2. Select delivered 1-to-4 DC power harness, verify delivered harness construction (check wire gauge, check if fused or unfused), and label branch #1 as `PANEL-1` on wire and 4-pin connector.
3. Identify and verify the delivered positive termination discovered by AF-179, then test continuity from it (PSU +V) to the 4-pin connector V+ pin with DMM (expected: beep).
4. Identify and verify the delivered negative/COM termination discovered by AF-179, then test continuity from it (PSU COM) to the 4-pin connector GND pin with DMM (expected: beep).
5. Cross-test the verified positive termination to GND and the verified negative/COM termination to V+ (expected: open circuit, no beep).
6. Check 4-pin connector keying against panel header; if unkeyed, apply high-visibility orientation index mark with Sharpie.

#### Done when
- Delivered harness construction verified (wire gauge, fused/unfused status, and branch isolation).
- Direct continuity confirmed from the verified positive termination to panel V+ connector pin.
- Direct continuity confirmed from the verified negative/COM termination to panel GND connector pin.
- Zero cross-continuity (complete isolation) between +5V and GND lines.
- Connector orientation is unambiguous (keyed shroud or Sharpie alignment index).

#### If it fails
If cross-continuity detects a short circuit, quarantine harness branch #1 and test spare harness branch.

---

### AF-020 — Verify panel #1 PCB power terminal polarity against capacitor

**Milestone:** M0
**Depends on:** AF-174
**Labels:** hardware safety-review power polarity-verify
**Resolves:** U-002

#### Do
1. Place LED Panel 1 on ESD-safe bench with rear PCB accessible.
2. Locate 4-pin power input header and adjacent electrolytic decoupling capacitor.
3. Identify capacitor negative terminal by white/colored stripe on capacitor body.
4. Using DMM in continuity / resistance mode, record resistance and continuity between power header pins and capacitor terminals (use capacitor negative terminal as a corroborating cross-check for GND).
5. Compare measured results against PCB VCC / GND silkscreen labels.
6. Record measured VCC↔GND resistance behavior; if persistently shorted, stop and investigate before declaring defective.
7. Mark verified polarity directly onto panel backing with permanent Sharpie.

#### Done when
- PCB VCC/GND silkscreen recorded and cross-checked against decoupling capacitor polarity.
- Measured resistance and continuity behavior between power header terminals and capacitor logged.
- Absence of persistent VCC↔GND short circuit confirmed.
- Polarities clearly marked with Sharpie.

#### If it fails
If a persistent low-resistance short exists between VCC and GND pins, stop and investigate circuit path / capacitor before applying power.

---

### AF-021 — Single-panel cold DC power test (EXP-003)

**Milestone:** M0
**Depends on:** AF-018, AF-019, AF-020
**Labels:** hardware safety-review power validation
**Procedure:** EXP-003
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Power OFF PSU immediately for smoke, burning smell, rapidly increasing temperature, visible electrical damage, or abnormal power behavior.
**Resolves:** U-002, U-003

#### Do
1. With PSU OFF and unplugged, connect the verified positive termination of harness branch #1 to PSU +V and the verified negative/COM termination to PSU COM.
2. Plug 4-pin harness connector into Panel 1 power header, verifying alignment marks.
3. Confirm NO HUB75 ribbon cable or controller is connected to Panel 1.
4. Plug AC mains cable into wall, stand to the side, and toggle C14 switch ON.
5. Observe panel for 10 seconds, checking for abnormal noise, spark, or rapid heating.
6. Measure DC voltage at PSU output terminals and at Panel 1 power connector terminals; record both and compute delta.
7. Run 10–15 minute stability hold, recording PSU voltage, panel terminal voltage, delta, and checking for abnormal heating, smell, noise, or voltage drop per EXP-003.
8. Switch C14 OFF and unplug from wall power.

#### Done when
- Cold standby energization initiated with no smoke, burning smell, or abnormal power behavior.
- Voltage at PSU terminals and at Panel 1 power connector measured and recorded, with delta computed per EXP-003.
- 10–15 minute stability hold completed with no significant heating, smell, noise, or abnormal voltage drop.
- Driver ICs and panel PCB show no significant heating over the 10–15 minute hold.
- EXP-003 data logged in `docs/pm/evidence/AF-021-exp-003-panel-power.md`.

#### If it fails
Power OFF immediately. Check polarity alignment; inspect panel for shorted SMD LEDs, reversed capacitors, or damaged driver ICs.

---

### AF-022 — Verify HUB75 cable and panel #1 IN header orientation

**Milestone:** M0
**Depends on:** AF-174, AF-179
**Labels:** hardware safety-review polarity-verify
**Resolves:** U-031

#### Do
1. Inspect rear of Panel 1 to identify HUB75 DATA_IN shrouded header vs DATA_OUT header, noting silkscreen labels and orientation.
2. Inspect 16-pin ~40cm HUB75 ribbon cable connectors for keyed central polarizing notches on both ends and pin 1 indicator/stripe.
3. Test-fit cable connector into Panel 1 DATA_IN header to verify keyed notch and pin 1 orientation on delivered hardware.
4. Using DMM in continuity mode, verify pin-to-pin continuity on ground pins and signal pins from end to end.
5. Verify no cross-short between signal pins and logic ground pins on cable.

#### Done when
- DATA_IN header on Panel 1 unambiguously identified and orientation verified on delivered hardware.
- Keyed notch and pin 1 alignment verified on delivered ribbon cable and panel header.
- End-to-end ground continuity and signal continuity verified on HUB75 cable.
- Zero internal short circuits between signal lines and ground.

#### If it fails
Discard defective or shorted ribbon cable and replace with a verified spare ~40cm HUB75 cable.

---

### AF-023 — Batch verify polarity for panels #2–#6 and harness branches

**Milestone:** M0
**Depends on:** AF-019, AF-020, AF-022
**Labels:** hardware safety-review power polarity-verify
**Resolves:** U-002

#### Do
1. For each remaining LED panel (Panel 2 through Panel 6):
   - Execute AF-020 capacitor-to-pin polarity verification on panel PCB.
   - Execute AF-022 DATA_IN header identification and label verification.
   - Mark verified polarity and DATA_IN orientation on panel rear with permanent Sharpie.
2. For each remaining DC harness branch (Branch #2 through Branch #6):
   - Verify delivered harness construction (check wire gauge, check if fused or unfused, verify polarity).
   - Execute AF-019 continuity and cross-isolation checks.
   - Apply Sharpie branch labels (`PANEL-2` through `PANEL-6`) and connector orientation marks.
3. Record individual per-panel and per-branch check results in evidence log.

#### Done when
- Polarity verified against onboard capacitor for all 5 remaining panels (Panels 2–6).
- DATA_IN header verified and marked on Panels 2–6.
- Delivered harness construction, continuity, and isolation confirmed on all 5 remaining harness branches (Branches 2–6).
- All 15 sub-checks enumerated and recorded individually in `docs/pm/evidence/AF-023-batch-polarity.md`.

#### If it fails
Quarantine any individual panel or harness branch that fails continuity, polarity, or isolation without blocking the remaining panels.

---

### AF-024 — Milestone M0 exit gate review and verification

**Milestone:** M0
**Depends on:** AF-021, AF-023, AF-180
**Labels:** docs validation

#### Do
1. Review all completed M0 test logs, measurement tables, and inspection checklists:
   - EXP-001 hardware inventory manifest and BOM update (`AF-011`..`AF-180`, with `AF-171` superseded).
   - AC mains visual, PE bonding, and isolation inspection records (`AF-012`..`AF-017`).
   - EXP-002 PSU no-load energization and voltage verification logs (`AF-018`).
   - EXP-003 single-panel cold DC power logs and batch polarity records (`AF-019`..`AF-023`).
2. Verify all M0 exit criteria from `docs/pm/backlog/README.md` Section 4 are 100% satisfied.
3. Confirm evidence files are committed in `docs/pm/evidence/` and photos archived in `hardware/photos/`.
4. Sign off M0 exit gate to unblock Milestone 1 parallel development tracks.

#### Done when
- EXP-001, EXP-002, and EXP-003 evidence logs complete, reviewed, and committed.
- All 8-point MAINS safety checks and DC polarity pre-checks verified without open defects.
- All BOM line items expected in receipt state reconciled with status updated to `RECEIVED` in `docs/BOM.md`.
- Formal M0 exit gate approved, unblocking M1 parallel controller tracks.

#### If it fails
Block progression to Milestone 1 until all outstanding M0 hardware discrepancies, safety checklist failures, or missing evidence logs are resolved.
