# Phase 00 — Hardware Receipt & Inventory (M0)

Covers delivery intake, physical inspection, inventory verification, and receipt evidence for every BOM category (executes EXP-001).

---

### AF-011 — Receive, inspect, and inventory all delivered hardware against BOM

**Milestone:** M0
**Depends on:** —
**Labels:** hardware delivery-review
**Procedure:** EXP-001
**Resolves:** U-006, U-014, U-015, U-021, U-026, U-031

#### Do
1. Prepare an ESD-safe workspace, camera, multimeter, the delivery paperwork, and `docs/BOM.md`; inspect each shipping box for transit damage before opening it.
2. Unbox all items, reconcile each packing-slip line to its BOM line item, and create an intake manifest that records received, missing, damaged, quarantined, or unidentifiable items.
3. Photograph every item category using `hardware/photos/AF-011-<category>-<item>-<front-or-rear>.jpg`; use `panels`, `controllers`, `nano`, `esp32-logic`, `psu-c14`, and `power-harness-cables` as category names, and record each photo filename beside its BOM line item in the intake manifest.
4. Inspect all six LED panels against the BOM: verify each is a 128x64 P2 panel with 2.0 mm pitch, SMD1515 marking, and nominal 256x128 mm dimensions; record visible silkscreen and driver-IC markings, power-header polarity labels, HUB75 IN/OUT labels, pin-1 indicators, and keyed orientation. Record an unavailable or conflicting marking as a discrepancy rather than assuming it matches.
5. Inspect the HD-WF4 and HD-WF2 controllers against the BOM: record board and IC markings, revisions, terminal labels, physical damage, and header condition; verify the WF4 has four HUB75 output ports and the WF2 has two. Inspect the Nano-W against its BOM entry: verify SG2002 and 256 MB DDR3 markings, then inspect its Wi-Fi connector, Type-C ports, LEDs, pin headers, and the Lenovo 64 GB microSD card. Inspect the ESP32-S3 and logic/adapter parts: verify the N16R8 markings for 16 MB flash and 8 MB octal PSRAM, and verify every buffer IC is SN74HCT245N HCT series, not HC, LS, or AHCT.
6. Inspect the A-200-5 PSU against the BOM: verify its rating plate identifies a single 5 V / 40 A / 200 W output rail, confirm the FG terminal and safety barrier are present, and record the terminal labels. Inspect the C14 inlet and candidate T2A 5x20 mm slow-blow fuse for tab layout, marking, and carriage fit. Inspect both 1-to-4 DC harnesses and verify eight branches total; record conductor colors, wire markings, connector types/keying, fused or unfused construction, insulation, and visible crimp condition. With all power disconnected, verify each branch has no positive-to-negative short and gently pull-test accessible crimp terminals for movement.
7. Photograph legible close-ups of all required markings and discrepancies, log any mismatch or damage, and quarantine unsafe or uncertain parts rather than using them.

#### Done when
- The intake manifest reconciles every BOM line item to a received, missing, damaged, quarantined, or unidentifiable state.
- The manifest maps every BOM category to its `AF-011` photo filenames in `hardware/photos/`.
- All six panels either match the BOM's 128x64 P2, 2.0 mm, SMD1515, nominal 256x128 mm specification or are explicitly logged as a discrepancy and quarantined.
- The WF4's four and the WF2's two HUB75 output ports are recorded, or a port-count mismatch is logged and the controller is quarantined.
- Nano-W SG2002 and 256 MB DDR3 evidence, ESP32-S3 N16R8 16 MB flash and 8 MB octal PSRAM evidence, and SN74HCT245N HCT-series evidence are recorded; any missing, damaged, or non-HCT part is quarantined.
- The PSU rating plate confirms one 5 V / 40 A / 200 W rail with an FG terminal, or the PSU is quarantined and is not energized.
- Both 1-to-4 harnesses either provide eight total branches with recorded construction, unpowered positive-to-negative isolation, and accessible-crimp pull-test results, or the failed harness is quarantined.
- All discrepancies, damaged items, and unresolved identification questions are logged; only BOM-matching, safe items are acceptable for downstream use.

#### If it fails
Quarantine the affected item, photograph the discrepancy and packing documentation, record the BOM line item as unresolved, and obtain a corrected item before using it in AF-013, AF-014, AF-018, or AF-021. Re-run the affected intake checks after replacement.

---

### AF-174 — SUPERSEDED

Replaced by: AF-011

(Do not export to Jira)

---

### AF-175 — SUPERSEDED

Replaced by: AF-011

(Do not export to Jira)

---

### AF-176 — SUPERSEDED

Replaced by: AF-011

(Do not export to Jira)

---

### AF-177 — SUPERSEDED

Replaced by: AF-011

(Do not export to Jira)

---

### AF-178 — SUPERSEDED

Replaced by: AF-011

(Do not export to Jira)

---

### AF-179 — SUPERSEDED

Replaced by: AF-011

(Do not export to Jira)

---

### AF-180 — Update BOM receipt status and component inventory notes from intake findings

**Milestone:** M0
**Depends on:** AF-011
**Labels:** docs delivery-review
**Resolves:** U-006

#### Do
1. Review AF-011's intake manifest, photo-to-BOM mapping, and recorded discrepancies.
2. Update `docs/BOM.md` Status to `RECEIVED` only for line items verified in AF-011.
3. Record exact delivered markings, revisions, and unresolved or quarantined discrepancies in BOM notes.

#### Done when
- `docs/BOM.md` records the receipt status for every verified line item.
- BOM notes preserve exact markings, revisions, and unresolved or quarantined discrepancies from AF-011.

#### If it fails
Leave unverified items out of the `RECEIVED` state, correct the AF-011 manifest or evidence mapping, and repeat the BOM update review.

---

### AF-171 — SUPERSEDED

**Depends on:** AF-011

Replaced by: AF-011

(Do not export to Jira)
