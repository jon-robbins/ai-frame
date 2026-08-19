# Phase 01 — Safe AC & DC Power Bring-Up (M0)

Covers C14 mains inlet wiring, crimp inspection, AC insulation and continuity validation, switching power supply (PSU) no-load voltage and hold testing (EXP-002), and single-panel cold DC power validation (EXP-003).

---

### AF-012 — Prepare C14 fused switched inlet assembly

**Milestone:** M0
**Depends on:** AF-178
**Labels:** hardware, power, safety
**Safety:** MAINS
**Stop condition:** Disconnect wall plug before assembling or probing C14 inlet.

#### Do
1. Install candidate T2A 5×20mm slow-blow fuse into C14 fuse carriage and insert carriage into inlet.
2. Prepare 18 AWG insulated spade jumper leads with heat-shrink tubing.
3. Wire switch jumper connections between C14 fused terminal and rocker switch input.
4. Verify unpowered continuity from AC inlet pins through fuse and switch to output spade tabs.

#### Done when
- Candidate T2A slow-blow fuse firmly seated in fuse carriage.
- Switch jumper connections fully insulated with heat-shrink tubing and zero bare metal exposed.
- Multimeter confirms direct continuity from AC pins to switched output tabs when switch is ON.
- Multimeter confirms open circuit when switch is OFF.

#### If it fails
Replace loose spade connectors, ensure full heat-shrink coverage, and re-test continuity before proceeding.

---

### AF-013 — Wire AC mains cable from C14 inlet to PSU terminal block

**Milestone:** M0
**Depends on:** AF-012
**Labels:** hardware, power, safety, wiring
**Safety:** MAINS
**Stop condition:** Do not plug into wall power while wiring AC terminals.

#### Do
1. Cut and strip 3-core 18 AWG / 0.75mm² AC mains cable.
2. Crimp 6.3mm insulated female spade terminals on C14 ends and bootlace ferrules / fork terminals on PSU ends.
3. Connect Live (brown/black) from C14 to PSU L terminal.
4. Connect Neutral (blue/white) from C14 to PSU N terminal.
5. Connect Protective Earth (green/yellow) from C14 ground tab to PSU FG (Frame Ground) terminal.
6. Secure mechanical cable strain relief clamping.

#### Done when
- Live, Neutral, and FG wires securely screwed into PSU terminal block.
- Mechanical strain relief secured so tension cannot pull terminals loose.
- Zero stray copper strands exposed outside crimp barrels or terminal blocks.

#### If it fails
Re-strip and re-crimp spade or ferrule terminals if any wire strands fray or connections loosen.

---

### AF-014 — Verify unpowered protective earth and chassis continuity

**Milestone:** M0
**Depends on:** AF-013
**Labels:** hardware, power, safety, test
**Safety:** MAINS
**Stop condition:** Keep power cord unplugged from wall.

#### Do
1. Keep AC mains cord completely disconnected from wall power.
2. Set digital multimeter to continuity / low-resistance mode.
3. Measure continuity between C14 inlet ground pin and PSU FG screw terminal.
4. Measure continuity between C14 inlet ground pin and PSU aluminum chassis / casing.
5. Record measured resistance values in the evidence log.

#### Done when
- Direct electrical continuity confirmed from C14 inlet ground pin to PSU FG terminal and metal chassis.
- Protective Earth confirmed continuous and unswitched in all switch states.
- Measured PE resistance recorded in evidence log.

#### If it fails
Inspect PE terminal crimps; scrape paint or anodization at chassis contact point if continuity is open.

---

### AF-015 — Verify unpowered AC line and neutral isolation

**Milestone:** M0
**Depends on:** AF-013
**Labels:** hardware, power, safety, test
**Safety:** MAINS
**Stop condition:** Keep power cord unplugged from wall.

#### Do
1. Keep AC power cord unplugged from wall.
2. With C14 rocker switch in OFF position, measure resistance between Live (L) and Neutral (N), Live (L) and PE/FG, and Neutral (N) and PE/FG.
3. Toggle C14 switch to ON position and repeat measurements between L-FG and N-FG.
4. Record all measured resistance values in evidence log.

#### Done when
- Open circuit (infinite resistance) confirmed between L and N when switch is OFF.
- Open circuit confirmed between Live and FG / chassis in both switch positions.
- Open circuit confirmed between Neutral and FG / chassis in both switch positions.

#### If it fails
Disassemble C14 wiring and inspect for solder bridges, short circuits, or miswired switch terminals.

---

### AF-016 — Complete pre-energization visual and mechanical safety inspection

**Milestone:** M0
**Depends on:** AF-014, AF-015
**Labels:** hardware, power, safety, review
**Safety:** MAINS
**Stop condition:** Do not energize until all 8 points on safety checklist pass.

#### Do
1. Execute the full 8-point MAINS checklist from `docs/pm/runbooks/safety.md`.
2. Verify terminal barrier cover is closed and securely seated over PSU screw terminals.
3. Verify AC cable strain relief holds firmly under gentle tug test.
4. Ensure bench area is clean, dry, non-conductive, and free of loose metal or wire trimmings.
5. Confirm wall outlet switch / plug is within arm's reach for emergency disconnect.

#### Done when
- All 8 MAINS safety checklist items verified and signed off in evidence log.
- PSU clear plastic terminal barrier cover is in place.
- Emergency disconnect path confirmed unobstructed.

#### If it fails
Abort energization until all safety checklist failures are fully resolved.

---

### AF-017 — Execute initial PSU no-load energization and verify indicator LED

**Milestone:** M0
**Depends on:** AF-016
**Labels:** hardware, power, safety, test
**Procedure:** EXP-002
**Safety:** MAINS
**Stop condition:** Immediately switch OFF and unplug if smoke, pop, buzzing, or burning odor occurs.

#### Do
1. Verify all DC output terminals have no load connected.
2. Plug IEC C13 power cable into wall outlet while maintaining emergency disconnect access.
3. Stand at arm's length with face turned away from PSU and toggle C14 switch ON.
4. Observe green power indicator LED on A-200-5 PSU.
5. Listen and check for abnormal acoustic noise, arcing, or thermal anomalies.

#### Done when
- Green power LED on A-200-5 PSU illuminates steadily.
- Zero popping, buzzing, smoke, or abnormal thermal symptoms.
- Initial energization timestamp and observations recorded in evidence log.

#### If it fails
Switch OFF immediately, unplug from wall, and inspect fuse and AC terminal wiring.

---

### AF-018 — Measure and calibrate PSU no-load output DC voltage

**Milestone:** M0
**Depends on:** AF-017
**Labels:** hardware, power, calibration
**Procedure:** EXP-002
**Safety:** MAINS
**Stop condition:** Switch OFF and unplug before adjusting wiring.

#### Do
1. Set digital multimeter to DC voltage mode.
2. Measure DC voltage across all 3 pairs of +V and COM screw terminals on PSU.
3. If voltage deviates from 5.00 V (acceptable range 4.80–5.25 V), gently adjust onboard V-ADJ potentiometer to calibrate output to ~5.05 V.
4. Run 10-minute continuous no-load stability hold, recording voltage and PSU casing temperature every 2 minutes.

#### Done when
- DC voltage across all 3 +V/COM terminal pairs measures ~5.0 V nominal.
- Output voltage drift is < 0.05 V over 10-minute hold duration.
- PSU case remains cool with zero thermal runaway.
- Voltage calibration values and 10-minute hold table logged in evidence file.

#### If it fails
If output voltage cannot be calibrated within tolerance or fluctuates erratically, power OFF and inspect or replace PSU.

---

### AF-019 — Wire 1-to-4 DC power distribution harness to PSU terminals

**Milestone:** M0
**Depends on:** AF-018
**Labels:** hardware, power, wiring
**Safety:** MAINS
**Stop condition:** Turn C14 switch OFF and unplug from wall before connecting DC harness.

#### Do
1. Switch C14 inlet OFF and unplug mains cable from wall.
2. Connect red fork terminals of 1-to-4 DC harness to PSU +V screw terminals.
3. Connect black fork terminals of 1-to-4 DC harness to PSU COM screw terminals.
4. Tighten screw terminals firmly and close clear plastic barrier cover.

#### Done when
- Red wire to +V and black wire to COM connections verified on terminal block.
- All screw terminals tightened securely with zero loose strands.
- PSU plastic terminal cover snapped closed.

#### If it fails
Correct any reversed fork terminals or loose screws before re-energizing.

---

### AF-020 — Verify DC polarity and voltage on unloaded panel power plugs

**Milestone:** M0
**Depends on:** AF-019
**Labels:** hardware, power, test
**Procedure:** EXP-003
**Safety:** MAINS
**Stop condition:** Power OFF before touching any connector pins.

#### Do
1. Plug in AC mains cord and toggle C14 switch ON with panel power connectors hanging unconnected.
2. Measure DC voltage at each of the 4 output connectors of the 1-to-4 harness using DMM fine probes.
3. Verify polarity on each plug (red wire = +5.0V, black wire = GND).
4. Record voltages in EXP-003 evidence log.

#### Done when
- +5.0V DC confirmed on designated positive pins of all 4 harness branch connectors.
- Zero polarity reversals detected across all connectors.
- Voltage readings across all 4 branches match within 0.05 V.

#### If it fails
Power OFF immediately; isolate, mark, and replace any miswired harness branch.

---

### AF-021 — Connect DC power cable to LED Panel 1 and verify mechanical fit

**Milestone:** M0
**Depends on:** AF-020, AF-174
**Labels:** hardware, display, power
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Power OFF PSU before plugging power cable into panel.

#### Do
1. Toggle C14 switch OFF and verify PSU LED discharges completely.
2. Inspect 4-pin power input header on LED Panel 1 (confirm VCC and GND silkscreen labels).
3. Insert verified 4-pin harness connector into Panel 1 power header.
4. Verify locking tab engages securely and connector is keyed correctly.

#### Done when
- 4-pin power plug firmly seated in Panel 1 power header.
- Locking tab engaged with correct physical orientation.
- Red harness wire aligns with VCC and black harness wire aligns with GND silkscreen on panel PCB.

#### If it fails
Do NOT force connector if keying does not match; re-verify panel pinout and connector orientation.

---

### AF-022 — Inspect Panel 1 HUB75 input port and verify unpowered isolation

**Milestone:** M0
**Depends on:** AF-021
**Labels:** hardware, display, safety
**Safety:** HUB75
**Stop condition:** Keep PSU powered OFF.

#### Do
1. Inspect 16-pin HUB75 DATA_IN shrouded header on Panel 1 (distinguish DATA_IN from DATA_OUT).
2. Verify pin 1 indicator and keyed notch orientation on the header.
3. With power harness plugged in but PSU powered OFF, measure resistance between Panel 1 VCC and GND terminals using DMM.
4. Measure unpowered resistance from VCC/GND to HUB75 ground pins.

#### Done when
- No dead short between Panel 1 VCC and GND (resistance rises > 100 Ω as capacitors charge).
- DATA_IN header keyed notch and pin 1 orientation documented.
- Logic ground continuity confirmed between power GND and HUB75 GND pins.

#### If it fails
Quarantine Panel 1 if a short circuit (0 Ω) exists between VCC and GND.

---

### AF-023 — Execute Panel 1 cold power energization and verify standby state

**Milestone:** M0
**Depends on:** AF-022
**Labels:** hardware, display, power, test
**Procedure:** EXP-003
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Switch OFF immediately if panel LEDs flash erratically, emit smoke, or driver ICs heat excessively.

#### Do
1. Energize PSU with Panel 1 connected (no controller or HUB75 ribbon cable attached).
2. Observe Panel 1 LED matrix face for unwanted flashing or lit LEDs.
3. Measure DC voltage at Panel 1 power connector terminals under quiescent/standby load.
4. Monitor driver IC temperatures during a 5-minute standby hold using infrared thermometer or finger touch.

#### Done when
- Panel 1 LEDs remain completely dark/blank during cold standby energization.
- Voltage at Panel 1 terminals measures ≥ 4.85 V DC (nominal ~5.0 V).
- All driver ICs and panel PCB remain cool / room temperature over 5-minute hold.
- Standby current and voltage recorded in EXP-003 evidence log.

#### If it fails
Power OFF immediately; inspect panel for shorted SMD LEDs, reversed capacitors, or damaged driver ICs.

---

### AF-024 — Conduct M0 safe power bring-up review and milestone exit verification

**Milestone:** M0
**Depends on:** AF-023, AF-180, AF-171
**Labels:** hardware, review, milestone-gate

#### Do
1. Compile all M0 test logs, multimeter reading tables, and inspection records (EXP-001, EXP-002, EXP-003).
2. Verify all AC mains wiring safety checks, PE continuity, and isolation measurements are documented.
3. Verify BOM status updates in `docs/BOM.md` and photo archiving in `hardware/photos/`.
4. Review M0 exit criteria against `docs/pm/backlog/README.md` Section 4.

#### Done when
- EXP-001, EXP-002, and EXP-003 evidence logs complete and linked in `docs/pm/evidence/`.
- AC and DC safety checklists fully verified and signed off.
- M0 exit gate criteria satisfied and documented, unblocking Milestone 1 parallel tracks.

#### If it fails
Block progression to M1 until all open M0 safety, wiring, or hardware intake issues are resolved.
