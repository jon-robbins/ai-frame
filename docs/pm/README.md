# 12 — Project Management Tracker README (Navigation & Operational Index)

**AI Frame Project Management Tracker — V1 planning backlog covering all hardware, software, mechanical, and decision-making work for the six-panel LED dashboard build.** 12 deliverables per the requirements in `/docs/JIRA.md`.

---

## 2. File Map Table

| # | Path | Name (short human) | Canonical? | Description (1 sentence) |
|---|------|---------------------|------------|---------------------------|
| 01 | `01-repository-audit.md` | Repository Audit | Audit | File inventory, branch divergences documented, contradictions surfaced, starting-state snapshot feeding uncertainty register. |
| 02 | `02-requirements-matrix.md` | Requirements Matrix | Yes | Every V1 requirement → provenance source (JIRA.md paragraph, PROJECT.md scope line, safety-rule paragraph), severity, downstream task. |
| 03 | `03-uncertainty-register.md` | Uncertainty Register | Yes | All 43 U-### rows: Question → Evidence now → Resolving AF-### task(s) → Downstream blocks → Severity (safety-critical / architecture-blocking / integration-risk / cosmetic). |
| 04 | `04-milestone-graph.md` | Milestone Dependency Graph | Yes | Mermaid pre/post-decision diagrams covering 6 gated milestones, gate legend (🔒 symbol semantics), parallel-branch fork/join layout, post-MG single winner chain. |
| 05 | `05-backlog.md` | Canonical Backlog | **Yes — SINGLE SOURCE OF TRUTH** | 10 Epics (AF-001..AF-010 frozen) + 163 Tasks (AF-011..AF-173) with full 20-field schema. Stable AF-### IDs. NEVER edit 09 or 10 by hand — edit HERE, then regenerate 09 + 10. |
| 06 | `06-critical-path.md` | Critical-Path Analysis | Yes | 3 views: (A) Pre-decision M0→M1 WF4 + ESP32 dual-candidate parallel chains with Nano SW independent track; (B) M1→M2 scaling path; (C) Post-decision full single-chain through MG reclassification → M3 → M4 → MF. Includes 3-state `critical-path` legend and verbatim AF-096 reclassification 6-step sweep procedure. |
| 07 | `07-experiment-coverage.md` | Experiment Coverage | Audit | EXP-001..EXP-017 reverse lookup: each EXP → covering AF-### task IDs, uncovered steps list, new gap-fix tasks column, plus Appendix A with full PROPOSED EXP-017 12-step procedure for M3 (2×2 topology validation). |
| 08 | `08-adr-coverage.md` | ADR Coverage | Audit | ADR-001..ADR-024 reverse lookup: each ADR → status (ACCEPTED/PENDING/DEFERRED) → accepted constraint text OR pending predicate → evidence-producing AF-### tasks → milestone at which it resolves. Plus status summary bar count. |
| 09 | `09-jira-import-table.md` | Jira Import Markdown Table | Derived | 11 columns mechanically extracted from every Epic and Task row of 05. 173 data rows. NEVER HAND-EDIT. Always regenerated alongside 10 when 05 changes. |
| 10 | `10-jira-import.csv` | Jira Import CSV | Derived | Same 11 columns as 09 in RFC-4180 quoted CSV format, CRLF line endings, 173 data rows. NEVER HAND-EDIT. Always regenerated with 09. |
| 11 | `11-coverage-audit.md` | Coverage Audit Report | Audit | 10-item audit covering BOM roster / EXP procedure / ADR pending evidence / milestone gates / uncertainty register / task sizes / polarity safety ordering / gating rules / critical-path validity / 3-way cross-file consistency. ALL 10 PASS as of 2026-08-19. |
| 12 | `README.md` (THIS FILE) | Navigation + Operational README | n/a | Navigation index, quick-start for novice executor, Stable ID rules, READY/BLOCKED legend, AF-096 sweep how-to, label legend 28-item taxonomy, completion-marking convention, audit status link. |

---

## 3. Quick-start for novice executor

**Step 1 — Orient yourself.** Read [`01-repository-audit.md`](01-repository-audit.md) top to bottom. You need to understand: (a) what files exist in the repo vs what's empty placeholders, (b) the 2 documented branch divergences (main is canonical — `PROTOTYPE_WIRING.md` + `PIN_LEVEL_APPENDIX.md` are the start-of-execution wiring references; the older CONNECTIONS.md on the non-merged branch is a draft), and (c) the "source-of-truth principle" list of 8 things you must never guess at (panel mfr P/N, driver IC, exact WF4 firmware version, etc.) — all of these spawn VERIFY tasks in the backlog so your first action on each is always "measure / photograph / confirm with actual part on bench".

**Step 2 — Big picture.** Open [`04-milestone-graph.md`](04-milestone-graph.md) for the 6-gate execution flow diagram showing the parallel branches. Memorize the legend: M0→M1→M2→MG (decision)→M3→M4→MF. The two controller candidates (WF4 and ESP32) run in parallel through M1 and M2. At MG you pick ONE winner for M3 onwards. The Nano software track is independent and can run BEFORE hardware arrives.

**Step 3 — Work the backlog TOP TO BOTTOM.** Open [`05-backlog.md`](05-backlog.md) and read it in strict file order (AF-011 → AF-012 → ... → AF-173). **Do not skip around.** The ordering is deliberately rigid for two non-negotiable reasons:
- Hardware tasks depend on earlier SAFETY and POLARITY passes. If you skip a "verify polarity OFF" task and later energize a panel, you can destroy hardware or cause a mains safety incident.
- Software tasks reference earlier framework decisions (transport abstraction, framebuffer API, controller decision gating). Implementing out of order produces code that must be rewritten after MG.

**Step 4 — Hardware hasn't shipped yet? Start with the software-nano READY subtrack.** Jump to tasks `AF-025..AF-037` (Nano SW Bootstrap). These have Labels that include both `READY` (no `blocked:delivery` tag — they only require a microSD card, a PC, the Nano board + USB, and optional CH340). You can complete the entire Nano software framework (image write → boot → Wi-Fi → Python venv → Pillow → framebuffer class → transport abstract → mock doubles → end-to-end smoke → resolution-agnosticism → commit) before the BOM parcel even arrives. This subtrack takes 1–3 days and is on the critical path regardless of which controller wins.

**Step 5 — MG Epic and the reclassification sweep.** When you reach Epic AF-004 (Architecture Decision Gate, tasks AF-093..AF-096) and finish writing ADR-016 with its winner name, **YOU MUST STOP and run the post-decision reclassification SWEEP manually on 05-backlog.md BEFORE opening any M3 or EXP-017 work.** Execute task AF-096's 6-step procedure verbatim (exact copy in Section 6 below, and also in [`06-critical-path.md`](06-critical-path.md) §6). Do NOT skip AF-096. If you skip it, you will start Cond-X PCB tasks (that should be SKIPPED) or start the WRONG controller's M3 chain.

---

## 4. Stable ID Legend

Header block condensed from [`05-backlog.md`](05-backlog.md) §Stable ID Registry:

- **AF-001 .. AF-010 = 10 Epics — FROZEN.** Assigned once 2026-08-19. Never add a new epic, never remove one. If a new phase is discovered, it lives as a new section inside the nearest existing epic.
- **Tasks begin at AF-011.** Current highest task ID is NOT hardcoded in this README (IDs can grow): run this command to find the live max ID:
  ```bash
  grep -oE '^### AF-[0-9]+' docs/pm/05-backlog.md | sort -t- -k2 -n | tail -1
  ```

### Stable ID Rules — Non-negotiable

**(a) Never renumber.** IDs are burned into 09/10, experiment coverage, ADR coverage, uncertainty register, critical-path diagrams, and audit reports. Renumbering = invalidating every cross-reference. Even if you discover a better order, leave the IDs as-is and document the correct execution order in a task's `Blocked by` field.

**(b) Never reuse.** Even if a task is deleted or proven wrong by experiment, its ID stays GONE forever. Do not write a new task with the same AF-### that previously meant something different.

**(c) Never delete.** If a task turns out too large during execution → do NOT delete the original AF-### and replace it. Do this instead:
1. Mark the original `Conditional: yes`
2. Add a `Skip condition:` line saying "Split into sub-tasks; partial work only completed here"
3. Add to Labels: `blocked conditional`
4. **APPEND NEW tasks at the END** of 05-backlog.md with brand-new AF-### IDs after the current max ID (see grep command above). Do NOT renumber earlier valid IDs to fit the new ones in the middle.

**(d) To obsolete / skip a task (e.g., loser controller track, WF2 all tasks, Cond-X all tasks if WF4 wins):** Set `Conditional: yes`, add a `Skip condition:` line describing exactly when to skip and what decision triggers it, add Labels `blocked conditional`. **Leave the task permanently in the file.** Future auditors need to see evidence this scope existed and why it was removed.

**(e) To add a newly-discovered task** (EXP uncovered step, new safety OFF-verify for a new energize step, new VERIFY for a new assumption found on the bench): **APPEND at END** with a brand-new AF-### after current max. Update 09 and 10, then re-run audit items 1–10 and record the new date.

---

## 5. READY / BLOCKED Legend (Computation Rule, from Design Spec §6a)

A task is **READY** if and only if **both** of the following are TRUE:

- **(a) Label gate:** the task's `**Labels:**` list does **NOT** contain the tag `blocked` (regardless of which sub-category blocker — any `blocked:*` tag counts because the label taxonomy always pairs `blocked` with the specific category).
- **(b) Prerequisite gate:** the task's `**Blocked by:**` field is either `—` (empty) OR **every** AF-### listed in Blocked by has been marked COMPLETED (by the executor per the marking convention in Section 8).

A task is **BLOCKED** if **either** of the following is TRUE:

- **(a) Labels gate:** Labels contain tag `blocked` (categories detailed below).
- **(b) Prerequisite gate:** any single AF-### in Blocked by is not yet COMPLETED.

### Blocker categories (semantic comments in Prerequisites field + reflected in Labels taxonomy)

| Blocker category label tag | Semantics in Prerequisites field |
|----------------------------|----------------------------------|
| `blocked:delivery` | `BLOCKED: DELIVERY` — hardware order not yet physically received and opened on bench. All M0 hardware tasks carry this. Nano SW AF-025/026/027 may be partially unblocked (need microSD only, not full parcel). |
| `blocked:exp` OR `blocked:exp-exp-XXX` | `BLOCKED: EXP-XXX` — needs experiment result from an earlier experiment task. E.g., `blocked:exp-exp-015` means wait for MR thermal data before MF design; `blocked:exp-exp-004` means wait for WF4 EXP-004 1-panel basic pass before M2 chain. |
| `blocked:adr` OR `blocked:adr-016` | `BLOCKED: ADR-XXX` — needs an architecture or transport decision. E.g., all Cond-X tasks carry `blocked:adr-016` because they only run if multi-ESP32 wins ADR-016. Post-MG the AF-096 sweep removes these tags from the winner. |
| `blocked:exp-af-###` | Generic prerequisite block on a specific AF task. Matches the Prerequisites field's `COMPLETED: AF-###` format. |

---

## 6. Post-decision sweep HOW-TO (AF-096 procedure CONDENSED)

**When to run this:** Immediately after completing tasks AF-093 (EXP-014 matrix filled with measurements), AF-094 (ADR-016 written ACCEPTED with winner), AF-095 (ADR-017 written ACCEPTED with transport).

This is the AF-096 6-step procedure — copied **VERBATIM** from [`06-critical-path.md`](06-critical-path.md) §6. Run these 6 steps MANUALLY by editing file `docs/pm/05-backlog.md`:

**Step 1.** Walk the backlog from the first M3 Epic AF-005 task through to the END of the file. For every task whose Labels contain `controller-wf4` OR `controller-esp32`:

**Step 2. Winner branch (the controller ADR-016 SELECTED):**
- Set its status flag `critical-path:` from `candidate-yes` → `yes`
- Keep `Conditional: no` (or change to `Conditional: no` if it was `yes` for pre-decision ambiguity)
- Remove any `blocked:adr-016` tag from Labels
- Keep or add `critical-path yes` alongside the controller tag.

**Step 3. Loser branch (the controller ADR-016 did NOT select):**
- Change `Conditional` from `no` → `yes`
- Add a `Skip condition:` line in Description or Status flags section reading exactly: `"Skip: ADR-016 selected [INSERT WINNER NAME HERE]; this is the loser track, may still run for reference data collection"`
- Change its status flag `critical-path:` from `candidate-yes` → `no`
- Labels: add tags `blocked conditional`
- Remove any `blocked:adr` or `blocked:adr-016` (the decision is now made — the block reason is different)

**Step 4. Any task labeled `controller-wf2` in M3 or later:**
- **ALWAYS set `Conditional: yes`**
- **ALWAYS add Skip condition:** `"WF2 is reference-only track; WF2 is not selected for V1 per ADR-012"`
- **ALWAYS set `critical-path: no`**
- **ALWAYS add Labels** `blocked conditional`
- Do this REGARDLESS of ADR-016 winner. WF2 never ships.

**Step 5. Epic AF-010 Cond-X tasks (AF-152..AF-170):**
- **IF multi-ESP32 won ADR-016:** REMOVE the `Conditional: yes` flag (change to `Conditional: no`), REMOVE the Skip condition line, REMOVE from Labels `blocked` and `conditional` and `blocked:adr-016`, set `critical-path: yes`. These become HARD requirements that feed into MF enclosure design.
- **IF WF4 won ADR-016:** keep `Conditional=yes`, keep the Skip condition unchanged, keep tags `blocked conditional blocked:adr-016`, set `critical-path: no`. None of them run.

**Step 6. Save, commit, regenerate.** Save the edited 05-backlog.md. Commit with message:
```
docs(pm): apply ADR-016/017 reclassification sweep; winner=<WINNER_NAME_HERE>
```
Then regenerate the derivation files `09-jira-import-table.md` and `10-jira-import.csv` from the updated 05 using the Task 9 / Task 10 extraction procedure. Then update `06-critical-path.md` post-decision single-path View C to reflect the winner. Then **RE-RUN audit items 1–10** (11-coverage-audit.md) with a new date.

---

## 7. Label Legend (Complete 28-item Taxonomy with 1-sentence definitions)

Labels are lowercase, space-separated, applied per task in `**Labels:**` field of every Epic and every non-Epic task. Label taxonomy rules: (1) a task can have multiple labels; (2) `blocked` always travels with a category sub-label `blocked:*` when known; (3) `critical-path` label always appears alongside the 3-state status flag (yes / candidate-yes / no) in Status flags.

| # | Label tag | 1-sentence definition |
|---|-----------|------------------------|
| 1 | `software` | Any task whose primary deliverable is Python/Pillow/Nano/OS code, scripts, venv setup, API endpoints, software dependencies, lint/type checks — not firmware. |
| 2 | `hardware` | Any task touching physical hardware build: wiring, soldering, connectors, panels, PSU mounting, ferrules, crimps, headers, harnesses, mechanical screw assembly — not firmware or pure software. |
| 3 | `firmware` | ESP32/WF2/WF4 flash/compile/sketch/library work: uploading binaries to controller chips, setting board config, flashing alt open firmware (WF2 reference spike). |
| 4 | `mechanical` | Frame design, backplate cutouts, enclosure material selection, 3D printing, extrusion cuts, bracket sizing, wall mounting, layout sketches, AC/DC barrier placement, strain relief grommets/glands. |
| 5 | `docs` | Any task whose primary output is writing/documentation: committing evidence, updating BOM STATUSes, drafting ADR records, filling result tables, saving photos, generating reports. |
| 6 | `validation` | Test pattern suites, continuity checks, gate aggregators, thermal sweeps, recovery tests — any task whose deliverable is a PASS/FAIL verification of a prior build item (not the build itself). |
| 7 | `spike` | Exploratory / reference / non-critical experimental work, specifically WF2 alt-fw tasks and any optional prototype that does not block a gate (WF2 is always `critical-path: no`). |
| 8 | `safety-review` | Tasks that explicitly run or review the 8-point mains checklist, or other explicit safety gates: visual inspections, cross-continuity matrices, polarity confirmations, PE bond checks — do not proceed without review sign-off. |
| 9 | `delivery-review` | EXP-001 inventory identify/receipt tasks, receipt inspection, BOM status ORDERED→RECEIVED flips, parcel-opening work. |
| 10 | `decision` | ADR drafting tasks (AF-094 = ADR-016 write, AF-095 = ADR-017 write) + MG aggregator (AF-096 = reclassification sweep). |
| 11 | `critical-path` | Always present alongside the 3-state flag (see Section 6 / 06-critical-path.md legend): paired with `critical-path: yes` / `candidate-yes` / `no` in the Status flags block. Tag itself uses the same taxonomy across all files. |
| 12 | `blocked` | Not READY (see Section 5). Always travels with a category sub-label below when the block reason is known. Also combined with `conditional` for Cond-X / loser-branch skip tasks (after reclassification sweep). |
| 13 | `blocked:delivery` | Waiting for hardware order physical delivery — the BOM shipping box hasn't arrived yet. All M0 hardware tasks carry this until AF-011 can start. |
| 14 | `blocked:exp` | Waiting on a prior experiment result. `blocked:exp-exp-015` means "EXP-015 data in AF-116 and MR aggregate AF-120 must complete before this MF task can start." |
| 15 | `blocked:adr` | Waiting on an architecture or transport decision. Most commonly: `blocked:adr-016` (waiting to see if Cond-X PCB runs). |
| 16 | `controller-wf4` | Tasks specific to the Huidu HD-WF4 stock controller candidate track: ID, power, WF4 X1/X2/X3 HUB75 topology, vendor software. |
| 17 | `controller-esp32` | Tasks specific to the ESP32-S3 + HCT245 adapter candidate track: perfboard builds, 12-stage HCT stages, firmware, ESP32 transport, 3-controller 6-panel topology. |
| 18 | `controller-wf2` | Tasks specific to the HD-WF2 reference-only experimental controller. Always paired with `critical-path no`, often with `spike`. NEVER runs in V1 production. |
| 19 | `nano` | Tasks for the LicheeRV Nano-W SBC hardware + software: booting, serial, Wi-Fi, Python on Nano. Nano SW subtask carries both `software nano`. |
| 20 | `pcb` | Conditional custom PCB KiCad schematic/layout/fab tasks in Epic AF-010. All tasks in AF-152..AF-170 have this. Only runs if ESP32 wins ADR-016. |
| 21 | `conditional` | Always paired with `Conditional: yes` in Status flags + a `Skip condition:` line explaining the trigger. Combined with `blocked` to create a blocked-conditional blocker (the gate AF-096 sweep sets/clears this). |
| 22 | `power` | Any task touching the PSU, 5 V distribution branches, C14 AC inlet wiring, ferrules, harnesses, fuses — any work near mains or high-current 5 V. Always triggers the "preceded by polarity-OFF-verify" safety pair rule (Audit #7). |
| 23 | `purchasing` | Placing or updating BOM orders, OPTIONAL additional purchases triggered by downstream results (e.g., if thermal data says "need fan", post-decision OPTIONAL fan buy is purchasing). |
| 24 | `thermal` | EXP-015 loaded temperature measurement, MR thermal analysis, closed-box thermal checks, brightness ceiling determination, PSU temperature logging at 4 brightness levels. |
| 25 | `thermal-review` | Tasks that aggregate or synthesize thermal data (like AF-120 MR aggregate analysis that writes the brightness ceiling rule). Distinct from `thermal` (measurement). |
| 26 | `polarity-verify` | Explicit power-OFF polarity check tasks. These MUST precede every energize task (the Audit #7 pair rule). If a new energize task is appended, a corresponding OFF-verify must exist before it. |
| 27 | `purchasing` (repeat note) | Duplicate slot reserved for any future "delivery post-receipt reorder" tasks (e.g., extra WF4 ordered after a controller board dies mid-build). |
| 28 | `docs` (repeat note) | Duplicate slot reserved for audit re-run documentation, new result commits, post-sweep evidence commits (every sweep commit produces a docs-labeled row). |

---

## 8. How to mark a task COMPLETED in the backlog

**Convention: never edit a task's core fields after completion.** The 20-field schema (Task ID, Epic, Summary, Description, Why exists, Prerequisites, Blocked by, Hardware, Tools, Software, Exact execution steps, Expected result, Acceptance criteria / DoD, Safety, Known uncertainties, Failure response, Source references, Labels, Status flags) — ALL 20 fields are frozen the moment the executor starts the task. Do not rewrite Summary, do not change Labels, do not adjust execution steps retrospectively.

**Where status updates go: ONLY inside the task's `**Evidence to save:**` section. Append after the existing bullet list. Use exactly this format:**

### PASS completion format
Inside the `Evidence to save:` block, after the existing bullets, APPEND a single line:
```
- Status: COMPLETED YYYY-MM-DD by [initials]; Evidence committed: <git 7-char commit hash>; Pass/Fail: PASS
```
- `YYYY-MM-DD` = actual calendar date you finished (e.g., `2026-09-15`).
- `[initials]` = 2–3 letter identifier (e.g., `JR`).
- `<git 7-char commit hash>` = the commit containing all the evidence files (photos, log files, measurement tables, filled result spreadsheets, etc.). If the executor can't commit they can write `Evidence saved to local path docs/pm/evidence/AF-XXX/` but git-committed evidence is the STRONG preference.
- `Pass/Fail: PASS` = every Acceptance criteria / DoD bullet passed.

### FAIL completion format
If a task FAILS and you are escalating (not just retrying the same inputs):
```
- Status: FAILED YYYY-MM-DD by [initials]; Failure reason: <1 sentence describing which DoD bullet failed and the measurement/observation that failed it>; Escalated to VERIFY task AF-XXX
```
- Then **CREATE a follow-up VERIFY task AF-XXX** as a NEW task APPENDED at the END of 05-backlog.md with the next free AF-### ID after current max. This VERIFY task should isolate the failure mode, document it, propose a root-cause fix path, link back to the failed original task in its Source references. Update 09+10, re-run audit.

Never silently retry an indefinitely failing task. If a DoD bullet fails, mark FAILED once, document the measurement, escalate to a new explicit VERIFY task with its own AF ID. The original AF stays forever FAILED with that evidence — it's the audit trail showing what went wrong and when.

---

## 9. Audit status link

✅ **Coverage audit passed.** See [`11-coverage-audit.md`](11-coverage-audit.md) for the full 10-item report with per-item methodologies, concrete findings (counts, grep excerpts, table outputs), and fix logs (0 source fixes required on this run — all items passed on first audit pass with correct interpretation of aggregation/filter rules).

**Audit run:** 2026-08-19 (current date as of this README write).

**⚠️ WARNING:** If you modify ANY content in `docs/pm/05-backlog.md` after this date (append new tasks, change labels, change Conditional status, apply the AF-096 sweep, mark tasks COMPLETED/FAILED per Section 8 convention), you MUST then:

1. Regenerate `09-jira-import-table.md` and `10-jira-import.csv` from the updated 05 using the Tasks 9–10 mechanical extraction procedure (same columns, same sort order: epics AF-001→AF-010 first, then tasks AF-011 ascending).
2. **RE-RUN all 10 audit items (1–10) of the coverage audit.** Record the new pass results and update the `Audit run:` date line at the top of 11-coverage-audit.md.
3. Do NOT leave the audit showing a PASS date of 2026-08-19 if the canonical files have drifted past that date. The audit is evidence, not a permanent stamp.
