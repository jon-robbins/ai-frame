# Phase 08 — Architecture Decision Gate (MG)

Covers controller candidate comparison across 13 scoring criteria (EXP-014), architectural decision recording (ADR-016 for controller selection, ADR-017 for transport protocol), and the full backlog reclassification sweep for downstream phases.

**Current sources:** [`README.md`](../../../README.md), [`docs/DECISIONS.md`](../../DECISIONS.md), [`docs/EXPERIMENTS.md`](../../EXPERIMENTS.md), [`docs/JIRA.md`](../../JIRA.md), [`docs/pm/backlog/README.md`](README.md), and [`docs/pm/runbooks/task-format.md`](../runbooks/task-format.md) plus the applicable safety/evidence runbooks. The stale [`02-requirements-matrix.md`](../02-requirements-matrix.md) is excluded.

---

### AF-093 — Complete EXP-014 measured scoring matrix

**Milestone:** MG
**Depends on:** AF-091
**Labels:** decision validation
**Procedure:** EXP-014
**Context:** Complete the current 13-criterion comparison between WF4 and the three-controller ESP32 architecture (Nano + three ESP32 controllers) using measured evidence from the candidate tracks and current project sources.

#### Do
1. Gather the linked WF4 and three-controller ESP32 measurements, observations, and missing-input records from the M1/M2 evidence summaries.
2. Fill every EXP-014 scoring cell for both candidates; retain `UNKNOWN` or an explicitly unsupported status where current evidence does not establish a value.
3. Record the criterion-level evidence links and prepare the completed matrix for ADR-016 and ADR-017 review without selecting a controller.

#### Done when
- All 13 EXP-014 criteria have an evidence-backed entry for both candidates, or an explicit unknown/missing-input record.
- The matrix distinguishes measured evidence from interpretation and contains no invented specification or untraceable score.
- The completed matrix is linked from the MG evidence record and is ready for architecture review; no winner is asserted.

#### If it fails
Do not fill gaps by inference. Return to the named candidate evidence task or record the missing input and keep the corresponding matrix cell unknown.

### AF-094 — Decide and record ADR-016 controller architecture

**Milestone:** MG
**Depends on:** AF-093
**Labels:** decision controller-wf4 controller-esp32
**Context:** Decide the WF4 versus three-controller ESP32 architecture (Nano + three ESP32 controllers) from the completed EXP-014 matrix, applying the project rule to prefer the simplest architecture that reliably satisfies V1; the draft may remain `PENDING` only while review is incomplete.

#### Do
1. Summarize the WF4 and three-controller ESP32 evidence against the EXP-014 criteria and the simplest-reliable-V1 rule.
2. Record ADR-016 in DECISIONS.md with the selected architecture, compared alternatives, rationale, consequences, rejected stacked-controller alternative, and direct evidence links, following the existing ADR schema.
3. Review the recorded decision against the linked evidence and set ADR-016 to `ACCEPTED` with the Decision naming exactly one architecture; this task is not complete while ADR-016 remains `PENDING`.

#### Done when
- ADR-016 is `ACCEPTED` and its Decision names exactly one architecture — WF4 or the three-controller ESP32 architecture (Nano + three ESP32 controllers).
- The entry follows the DECISIONS.md ADR schema with an evidence-linked comparison, rationale, consequences, and the rejected alternative.
- The accepted decision is linked from the MG evidence record for use by AF-095 and AF-096.

#### If it fails
Keep ADR-016 `PENDING`, identify the missing evidence or review objection, and return to AF-093 or the named candidate evidence source; do not record an acceptance the matrix evidence does not support.

### AF-095 — Decide and record ADR-017 transport

**Milestone:** MG
**Depends on:** AF-094
**Labels:** decision validation
**Context:** Using the controller architecture accepted in ADR-016, decide the Nano-to-controller transport from current transport evidence; the draft may remain `PENDING` only while review is incomplete.

#### Do
1. Compare the current Huidu, USB, UART, native-USB, and local TCP/network transport evidence — restricted to the transports available on the accepted ADR-016 architecture — against reliable dynamic updates, reconnect behavior, maintainability, and V1 needs.
2. Record ADR-017 in DECISIONS.md with the selected transport, alternatives, evidence links, rationale, and consequences, following the existing ADR schema and retaining the project’s allowance for lower-rate dashboard updates where reliable.
3. Review the recorded decision and set ADR-017 to `ACCEPTED` with the Decision naming exactly one transport; this task is not complete while ADR-017 remains `PENDING`.

#### Done when
- ADR-017 is `ACCEPTED` and its Decision names exactly one transport consistent with the accepted ADR-016 architecture.
- The entry follows the DECISIONS.md ADR schema with an evidence-linked comparison, rationale, and consequences.
- The accepted decision is linked from the MG evidence record for use by AF-096.

#### If it fails
Keep ADR-017 `PENDING`, record the missing transport evidence or review objection, and return to the relevant transport experiment or AF-094; do not record an acceptance the transport evidence does not support.

### AF-096 — Execute post-decision reclassification sweep

**Milestone:** MG
**Depends on:** AF-094, AF-095
**Labels:** decision validation
**Context:** After both ADR-016 and ADR-017 are reviewed and accepted, normalize downstream conditional controller tasks to one executable path; AF-096 must not execute while either ADR is `PENDING`, and M3 must remain blocked until this sweep completes, without changing either decision record’s evidence or choosing either decision here.

#### Do
1. Verify that ADR-016 and ADR-017 both contain reviewed and accepted decisions; do not run the sweep while either ADR remains `PENDING`, and do not begin or authorize M3 until AF-096 is complete.
2. Enumerate every M3+ registry entry that is controller-specific or ADR-016-conditional at execution time — any task carrying a `controller-wf4`/`controller-esp32` label, `conditional`, `blocked:adr-016`, or an `Applies if` referencing ADR-016 — across all phase files from `09-quad-panel.md` onward, including M4/Cond-X and later controller-specific work populated by then. Current known M3 targets: AF-098, AF-099, AF-187, AF-100. Do not choose or revise ADR-016 or ADR-017 in this task.
3. For the winner, remove `conditional`, `blocked`, and `blocked:adr-016` from `Labels`, remove `Applies if`, retain the winning controller label, and add `critical-path`.
4. For the loser, retain `conditional blocked blocked:adr-016`, remove `critical-path`, and replace `Applies if` with exactly `Skip — ADR-016 selected WF4` when the three-controller ESP32 architecture loses or exactly `Skip — ADR-016 selected multi-ESP32` when WF4 loses. No `[other controller]` placeholder may remain.
5. Record the enumerated target list, before/after task IDs, labels, resolved lines, both accepted ADR statuses, and source decisions in the MG evidence record; only the selected branch is executable after the sweep.

#### Done when
- The sweep records both reviewed/accepted ADR statuses and applies the exact winner/loser field mutations to every enumerated downstream branch entry — not a fixed task list — before M3 can proceed.
- The winner has its controller label plus `critical-path` and no conditional/blocked labels or `Applies if`; the loser has `conditional blocked blocked:adr-016`, no `critical-path`, and the exact resolved skip line.
- The sweep is traceable to the reviewed ADR-016 result and does not silently alter ADR-017 or claim a transport winner.

#### If it fails
Do not partially reclassify the registry. Restore the affected field set to its pre-decision form, keep downstream branch work blocked, and resolve the ADR review or source discrepancy before rerunning AF-096.
