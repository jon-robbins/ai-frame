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
4. Inspect all delivered LED panels: record visible silkscreen and driver-IC markings, power-header polarity labels, HUB75 IN/OUT labels, pin-1 indicators, keyed orientation, and count all six panels against the BOM.
5. Inspect the HD-WF4 and HD-WF2 controllers, Nano-W and microSD/accessories, ESP32-S3 and logic/adapter parts: record board and IC markings, revisions, connector and port counts, terminal labels, physical damage, and header condition without inferring any undocumented revision or pinout.
6. Inspect the A-200-5 PSU, C14 inlet, candidate fuse, DC harnesses, HUB75 cables, AC cable, spade terminals, and ferrules: record PSU and terminal markings, C14 tab layout, fuse marking and carriage fit, conductor colors, branch counts, connector types/keying, wire markings, insulation, and visible crimp condition. With all power disconnected, verify each harness branch has no positive-to-negative short and gently pull-test accessible crimp terminals for movement.
7. Photograph legible close-ups of all required markings and discrepancies, log any mismatch or damage, and quarantine unsafe or uncertain parts rather than using them.

#### Done when
- The intake manifest reconciles every BOM line item to a received, missing, damaged, quarantined, or unidentifiable state.
- The manifest maps every BOM category to its `AF-011` photo filenames in `hardware/photos/`.
- All six panels and both controller boards are identified, with their visible connector orientation and marking evidence recorded.
- Nano-W, ESP32-S3, logic/adapter parts, PSU/C14/fuse, harnesses, and cables have their receipt observations recorded without substituting assumed specifications for delivered evidence.
- Harness branch count, construction observations, unpowered positive-to-negative isolation results, and accessible-crimp pull-test results are recorded for every delivered branch.
- All discrepancies, damaged items, and unresolved identification questions are logged and quarantined where safety requires it.

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

Replaced by: AF-174, AF-175, AF-176, AF-177, AF-178, AF-179

(Do not export to Jira)
