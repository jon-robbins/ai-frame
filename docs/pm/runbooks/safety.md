# Shared Runbook: Safety Profiles

**Document ID:** `RB-SAFETY`  
**Location:** `docs/pm/runbooks/safety.md`  
**Purpose:** Canonical reference for all safety profiles and checklists across the project. Backlog tasks reference these 4 named profiles instead of repeating verbose safety checklists.

---

## 1. Safety Profile Overview & Usage Rules

To maintain conciseness and zero drift across the project backlog, tasks involving physical hardware, electrical connections, or bench debugging specify safety requirements using two standardized fields:

1. **`Safety:`** `<PROFILE-NAME>` — References one of the 4 named profiles defined below (`MAINS`, `HUB75`, `CH340`, or `5V-HIGH-CURRENT`).
2. **`Stop condition:`** `<local stop condition>` — A mandatory, concrete one-sentence rule describing the immediate physical action or threshold required before proceeding or when unexpected behavior occurs (e.g., *"Disconnect wall power immediately if any component heats abnormally or fails continuity."*).

Tasks that do not touch physical hardware or dangerous voltages (such as software-only or pure documentation tasks) omit the `Safety:` and `Stop condition:` fields or state `N/A`.

### Profile Quick Reference

| Profile Name | Operational Scope | Primary Risk | Mandatory Stop / Control Action |
|---|---|---|---|
| `MAINS` | 230 V AC wiring, C14 inlet, PSU AC terminals, PSU energization | Lethal AC electric shock, mains short circuit, fire | Wall plug accessible for instant emergency disconnect; power OFF before any wire changes. |
| `HUB75` | HUB75 ribbon cable connection, chaining, swapping | Controller / buffer / panel driver damage from transient currents | Disconnect panel and controller power before connecting or moving ribbon cables. |
| `CH340` | USB-UART serial debugging on LicheeRV Nano, ESP32, etc. | Back-feeding 5 V/3.3 V to powered boards, ground-loop damage | When the target is independently powered, connect TX/RX/GND only. Leave CH340 VCC/5V/3.3V disconnected. |
| `5V-HIGH-CURRENT` | 5 V panel power distribution, harness wiring, PSU load tests | Overcurrent, excessive voltage drop, burnt wiring/multimeter shunt | Dedicated parallel power branches; never daisy-chain; never measure full current through 10 A DMM shunt. |

---

## 2. Profile 1: `MAINS` — 230 V AC Wiring & PSU Energization

This profile applies to every task involving AC mains wiring (C13/C14), fuse installation, terminal crimping/insulation, PSU primary-side connections, and initial AC energization.

### Mandatory Pre-Energization & Wiring Checklist

Before energizing or making any physical changes to mains-voltage circuits, complete all 8 steps in order:

1. **Disconnect wall power before any wiring change:** Unplug the IEC C13 mains cable from the wall outlet or bench supply before touching any AC terminal or wire.
2. **Verify L/N/PE terminal identification at C14 inlet:** Inspect physical markings and use continuity testing to confirm the Live (L), Neutral (N), and Protective Earth (PE) pin/tab layout on the rear of the C14 inlet assembly. Do not rely on unverified pinouts.
3. **Confirm protective earth (PE) continuity to PSU FG:** Verify direct electrical continuity between the C14 ground pin/tab and the switching power supply frame ground (`FG` / earth screw terminal). Verify PE continuity and record the measured resistance. Do not impose a numeric pass threshold until a validated requirement exists. **PE must NOT be switched or fused.**
4. **Confirm the installed fuse matches prototype requirements:** Confirm the installed fuse matches the validated C14/PSU prototype requirement. Current candidate: **T2A 5×20 mm slow-blow glass fuse**; verify fit and rating before first energization.
5. **All AC connections use insulated spade or ferrule terminals:** Use 6.3 mm insulated female spade terminals on C14 tabs and correctly sized bootlace ferrules on PSU screw terminals. Ensure no bare conductor strands are exposed outside the insulation.
6. **Strain relief on AC cable before terminals:** Secure the 3-core AC mains cable with mechanical strain relief / clamping so tension on the cable cannot pull terminals loose.
7. **Visual + continuity inspection of all AC wiring before energizing:** Perform a visual inspection and complete continuity/isolation checks (L to N open circuit when switched off; PE continuous to chassis; no short from L/N to PE) before plugging into the wall.
8. **Wall plug accessible for emergency disconnect throughout operation:** Ensure the physical wall plug or power strip switch is within arm's reach and completely unobstructed during all energized testing.

### Prohibitions (Never)

- **NEVER** use mains voltage (230 V AC) on a breadboard or perfboard.
- **NEVER** use Dupont jumper leads for mains or high-current connections.
- **NEVER** energize mains with your face or eyes directly over the switching power supply.
- **NEVER** switch or place a fuse on the Protective Earth (PE) conductor.
- **NEVER** leave exposed, uninsulated conductors on any AC mains joint.

---

## 3. Profile 2: `HUB75` — Panel Signal Connections

This profile applies to all tasks involving connecting, routing, chaining, or debugging 16-pin HUB75 IDC ribbon cables between controllers (HD-WF4, HD-WF2, ESP32-S3 adapter) and P2 LED matrix modules.

### Core Signal Rules & Handling

- **Zero Hot-Plugging:** Disconnect panel and controller power before connecting, disconnecting, or moving HUB75 ribbon cables (Never hot-plug HUB75 cables).
- **Connector & Orientation Verification:** Verify pin 1, key orientation, and IN/OUT markings on the delivered panel/controller before connection. Do not infer orientation solely from cable stripe or a generic HUB75 pinout.
- **Signal-Ground Integrity:** Ensure common logic ground exists across the display controller, buffer circuits, and panel logic grounds.
- **Factual Grounding on Power Sequencing:** Do **NOT** invent arbitrary power-up sequencing rules (e.g., requiring signal before power or power before signal) unless explicitly cited from official manufacturer documentation or validated test data.

### Prohibitions (Never)

- **NEVER** insert or remove HUB75 ribbon cables on energized boards.
- **NEVER** force an unkeyed connector into a HUB75 header without verifying pin 1, key orientation, and silkscreen labels on the delivered hardware.

---

## 4. Profile 3: `CH340` — USB-UART Serial Debug

This profile applies to serial console connection and debugging tasks for the LicheeRV Nano-W (SG2002), ESP32-S3, or other microcontrollers using a CH340 USB-to-TTL serial adapter.

### 3-Pin Connection & Isolation Rules

- **Strict 3-Pin Connection (TX, RX, GND):** When the target is independently powered, connect TX/RX/GND only. Leave CH340 VCC/5V/3.3V disconnected.
  - CH340 `TX` → Target `RX` (e.g., Nano `A17`)
  - CH340 `RX` ← Target `TX` (e.g., Nano `A16`)
  - CH340 `GND` ↔ Target `GND`
- **Disconnect Before High-Current Powering:** Disconnect CH340 before powering target from PSU harness to avoid ground-loop or back-feed transients during system energization.

### Prohibitions (Never)

- **NEVER** connect CH340 `5V` or `3.3V` output pins to an independently powered target microcontroller.
- **NEVER** connect a 5 V TTL UART line directly to 3.3 V logic pins without verified level tolerance.

---

## 5. Profile 4: `5V-HIGH-CURRENT` — Panel Power Distribution

This profile applies to the 5 V DC power distribution network, 1-to-4 power harnesses, PSU DC output terminals, panel power connectors, and high-brightness/full-load display testing.

### Parallel Power Distribution & Harness Rules

- **Mandatory Parallel Distribution:** Panels must receive parallel power branches using the validated distribution arrangement. **Do not daisy-chain panel power through another panel.**
- **Use BOM-Specified Power Harness & Wiring:** Use the BOM-specified power harness/wiring (1-to-4 pure copper harness and 18 AWG silicone power wire) and verify delivered harness construction during receipt inspection; do not use Dupont wiring for panel power.
- **Multimeter Current Measurement Prohibition:** **Do not measure full display current through multimeter 10A shunt in series.** The full display load (up to ~27.6 A peak for 6 panels) exceeds the 10 A rating and will damage the meter or blow the meter fuse. For high-current measurements, use an external DC current clamp or verified dedicated shunt.
- **Polarity Confirmation:** Check physical polarity (+5 V vs GND) on each panel power header and harness connector with a multimeter continuity/voltage check before mating connectors.

### Prohibitions (Never)

- **NEVER** daisy-chain DC power from Panel 1 to Panel 2 via board pass-throughs.
- **NEVER** route panel power through Dupont wires, breadboards, or unrated thin jumper leads.
- **NEVER** connect a standard multimeter in series with the 40 A PSU main DC output rail.
