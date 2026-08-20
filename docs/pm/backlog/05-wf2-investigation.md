# Phase 05 - HD-WF2 Reference Controller Investigation (M1)

Tasks are maintained as a compact, dependency-driven M1 phase catalog. Hardware facts must be verified against received evidence and the referenced wiring appendix.

---

### AF-076 - Identify HD-WF2 PCB revision, markings, and 2-port layout

**Milestone:** M1
**Depends on:** AF-024
**Labels:** firmware controller-wf2 docs spike
**Context:** Establish the exact received WF2 variant before testing.

#### Do
1. Photograph the WF2 and record each revision, power connector, USB interface, and HUB75 label.
2. Count and label observed outputs and save the identification table with photos.

#### Done when
- Board identity and physical output labels are recorded.
- Any mismatch remains explicit for reference-subtrack interpretation.

#### If it fails
Do not power an unidentified board; preserve evidence and resolve variant ambiguity.

---

### AF-077 - Characterize HD-WF2 stock firmware capabilities on single panel and both outputs (EXP-008)

**Milestone:** M1
**Depends on:** AF-076
**Labels:** firmware controller-wf2 validation spike
**Safety:** HUB75
**Stop condition:** Disconnect panel and controller power before moving the HUB75 cable between outputs.
**Procedure:** EXP-008
**Context:** Determine the stock WF2 baseline and whether both HUB75 outputs are usable using identical single-panel conditions.

#### Do
1. Configure stock WF2 for one delivered panel and record settings. With power off for each cable move, run the six standard patterns independently on Output-1 and Output-2 using the same panel/cable.
2. For each output, record pass/fail/unknown, color mapping, scan offset, symmetry, defects, maximum observed practical layout, and available Wi-Fi/USB/network controls.
3. Save a side-by-side comparison using identical inputs and photos where feasible.

#### Done when
- Pattern and communication results are evidence-backed for both outputs.
- Output comparison identifies any asymmetry, unavailable port, or unknown result without inference.

#### If it fails
Disconnect power before moving cables, label the failing port, preserve settings/evidence, and isolate panel, cable, and firmware before another run.

---

### AF-172 - SUPERSEDED

Replaced by: AF-077

(Do not export to Jira)

---

### AF-173 - Create reversible HD-WF2 stock-firmware backup

**Milestone:** M1
**Depends on:** AF-076, AF-077
**Labels:** firmware controller-wf2 docs safety-review
**Procedure:** EXP-009
**Context:** Preserve a recoverable stock state before flashing an alternative firmware.

#### Do
1. Record delivered revision, SoC, flash size, and accessible boot/interface before choosing a tool.
2. Select the documented backup method for that confirmed target. If USB-UART is used, follow the CH340 3-pin safety profile; otherwise use the confirmed interface's shutdown/isolation controls. Record parameters and artifacts.
3. Where repeatable reads exist, create two independent backups and compare artifacts/checksums. Otherwise document the recovery path and its verification evidence in a backup README.

#### Done when
- Two matching backups/checksums exist when repeatable reads are supported, or a verified documented recovery path exists otherwise, before alternative firmware work.
- No prohibited power/backfeed connection was made.

#### If it fails
Follow the confirmed interface's shutdown/isolation procedure before rewiring; preserve artifacts and diagnose method or parameters.

---

### AF-078 - Evaluate HD-WF2 alternative/open firmware and summarize reference subtrack (EXP-009)

**Milestone:** M1
**Depends on:** AF-077, AF-173
**Labels:** firmware controller-wf2 spike docs
**Procedure:** EXP-009
**Context:** Determine whether documented open firmware materially improves this reference controller and preserve evidence for ADR-012/016.

#### Do
1. Flash documented community firmware only after AF-173 backup evidence is retained; if boot or display fails, restore the verified stock state before further analysis.
2. Test one panel with standard patterns and the available live-frame API, then compare stock/open behavior using identical inputs without inventing thresholds.
3. Index EXP-008/009 logs, firmware hashes, photos, and tables; write a cited suitability summary for ADR-012/016, marking unsupported conclusions unknown, then commit the reviewed report and record its hash.

#### Done when
- Firmware version, panel/API results, defects, benefits, and regressions are recorded with evidence or marked unknown.
- The reference summary has an artifact index, citations, and commit hash.

#### If it fails
Restore verified stock firmware on boot/display failure, preserve binaries/logs, and return unsupported conclusions to the owning test rather than inferring suitability.

---

### AF-079 - SUPERSEDED

Replaced by: AF-078

(Do not export to Jira)
