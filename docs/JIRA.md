# TRAE Prompt — Build a Foolproof AI Frame Project Plan and Jira Backlog

You are working inside the `ai-frame` repository.

Your task is **project planning and repository analysis first, not implementation**.

The end goal is to create an exhaustive, atomic, dependency-aware backlog that can later be imported into Jira and followed step by step by someone who is new to electronics and PCB development.

Do not assume that existing architecture uncertainties have already been resolved.

## Primary Product Goal

AI Frame is a Wi-Fi-connected ambient RGB LED-matrix display using six P2 HUB75E 128×64 panels.

The intended final display is:

* 2 panels wide
* 3 panels high
* 256×192 logical resolution

The application computer is currently the LicheeRV Nano-W running Linux/Python.

A dedicated display controller performs HUB75 refresh.

The final controller architecture and Nano→controller transport are intentionally unresolved.

## Required Hardware Milestone Sequence

The project MUST advance through these gates in order:

### Milestone 1 — One physical panel

One 128×64 panel must display arbitrary text end-to-end.

For this milestone to count:

1. A user supplies an arbitrary text string.
2. The **LicheeRV Nano-W** renders that text into a framebuffer.
3. The Nano sends the rendered content programmatically to a candidate controller.
4. The candidate controller drives the physical panel.
5. The requested text appears correctly.
6. No manual vendor-software operation is required for each text update.
7. The system remains stable for an appropriate sustained test.

Displaying text manually through Huidu software is useful as a hardware test, but DOES NOT satisfy this milestone.

### Milestone 2 — Two physical panels

Two panels operate as one 256×64 logical canvas.

Requirements include:

* independent parallel power to both panels;
* HUB75 signal chaining where appropriate;
* correct left/right ordering;
* arbitrary text crossing the physical seam;
* Nano-controlled end-to-end updates;
* sustained stability.

### Architecture Decision Gate

After viable candidate paths have been tested through the two-panel stage, compare them and explicitly resolve:

* ADR-016 — final display-controller architecture;
* ADR-017 — final Nano→controller transport.

Do not simply pick the most technically interesting solution.

Follow the project's stated principle: prefer the simplest architecture that reliably solves V1.

If one candidate has clearly failed before this point, document why and remove it from the critical path.

### Milestone 3 — Four physical panels

Introduce a NEW intermediate 2×2 / 256×128 milestone.

The current repo does not explicitly include this milestone, so propose an appropriate new experiment ID rather than pretending it already exists.

Requirements include:

* four independent panel power branches;
* two 256×64 signal rows;
* Nano rendering a 256×128 logical framebuffer;
* arbitrary content crossing both horizontal and vertical physical seams;
* synchronized-enough row updates;
* sustained stability.

Possible selected-controller topology:

WF4:

`X1 → top-left → top-right`
`X2 → bottom-left → bottom-right`

Multi-ESP32:

`ESP32 #1 → top-left → top-right`
`ESP32 #2 → bottom-left → bottom-right`

Only use the topology that remains valid after the architecture decision.

### Milestone 4 — Six physical panels

Final prototype:

`row 1: left → right`
`row 2: left → right`
`row 3: left → right`

Logical framebuffer: 256×192.

Requirements include:

* six independent parallel 5 V panel power branches;
* arbitrary text anywhere on the complete display;
* correct crossing of every physical seam;
* acceptable refresh quality;
* stable controller transport;
* PSU load/thermal validation;
* repeated boot/recovery testing;
* Wi-Fi recovery;
* unattended operation.

Only after this stage should the project shift primarily into packaging, reliability, thermal, and mounted-frame optimization.

---

# Repository Audit Requirements

Before creating the final backlog, inspect the ENTIRE repository.

At minimum inspect:

* `README.md`
* `docs/PROJECT.md`
* `docs/BOM.md`
* `docs/STATUS.md`
* `docs/EXPERIMENTS.md`
* `docs/DECISIONS.md`
* everything under `hardware/`
* everything under `software/`
* everything under `firmware/`
* hardware reference photos
* existing schematics/diagrams
* git status/history where useful

Also inspect all relevant branches, not only `main`.

At the time this prompt was written, important branches include:

* `main`
* `agent/connection-diagrams`
* `agent/prototype-wiring-diagrams`

The wiring branches contain proposed detailed connection documentation that may not yet be merged into `main`.

Treat branch-only work as useful evidence/proposed design, not automatically as an accepted architecture decision.

If additional branches exist, inspect them too.

---

# Source-of-Truth Rules

Never silently resolve contradictions.

When sources disagree:

1. Identify the contradiction.
2. Cite both locations.
3. Determine whether one is clearly stale.
4. If the repo does not contain enough evidence, create an explicit verification/decision task.

Accepted ADRs constrain the plan.

PENDING ADRs are NOT decisions.

DEFERRED decisions must remain deferred until their prerequisites exist.

Experiments produce evidence; they do not automatically alter architecture decisions.

Actual delivered hardware ultimately overrides assumptions derived from seller photographs.

Never invent:

* connector polarity;
* pin numbering;
* PCB revisions;
* GPIO availability;
* USB functionality;
* communication protocols;
* scan configuration;
* current capacity;
* firmware support.

If any of these are unknown, create a verification or spike task.

---

# Known Areas of Uncertainty That Must Be Represented Explicitly

At minimum investigate and represent tasks around:

## WF4

* Can the Nano push arbitrary dynamic framebuffer content programmatically?
* Is there a useful wired interface?
* Is the apparent USB functionality suitable for programmatic control, or primarily offline/update use?
* Is a local network/Wi-Fi protocol simpler and reliable enough?

Do not assume WF4 wins because it maps cleanly to three rows.

## ESP32-S3

* Exact delivered generic-board revision.
* Physical dimensions/header arrangement.
* Exposed/usable GPIOs.
* Compatibility of the proposed HUB75 GPIO mapping.
* PSRAM/DMA behavior.
* Refresh quality at 128×64 and 256×64.
* UART vs native USB transport.
* Required bandwidth and achievable update rate.
* Recovery after transport/controller restart.

Do not purchase multiple ESP32s or design the final adapter PCB before the first ESP32 reliably drives the required row and the architecture decision supports that path.

## WF2

Treat WF2 primarily as an experimental/reference/fallback path unless actual evidence gives it a clear advantage.

## Power

* Actual panel connector polarity.
* PSU behavior under real load.
* Voltage drop across wiring.
* thermal behavior;
* controller power-input details;
* final safe mains termination.

## Mechanical enclosure

Do not finalize the enclosure until these are known:

* controller architecture;
* controller count;
* final PCB dimensions if applicable;
* PSU thermal behavior;
* wiring topology;
* brightness/power ceiling;
* cable clearances.

---

# Task Granularity Rules

A task is **one meaningful project-level outcome**. A task may contain multiple independently checkable construction stages when those stages collectively produce one artifact or result. Detailed physical steps, soldering sequences, connector checks, test patterns, and safety checks live inside the task's `Do` section, an experiment, or a shared runbook unless they independently matter to project status.

**Compression must never remove execution guidance.** A project-level task may absorb multiple construction or test stages, but the resulting task and its referenced procedures must remain executable by a novice without relying on unstated electrical, wiring, firmware, or tool knowledge. If merging makes the physical procedure ambiguous, preserve the necessary detail inside the task/runbook or do not merge those tasks.

## Backlog-Worthiness / PM-Utility Test

Before a card is kept as a separate task, it must pass the PM-utility test. A task normally survives if several of the following are true:

1. Does it materially change project status?
2. Is it independently visible on a board?
3. Does it independently block, wait externally, or need a separate decision?
4. Does it produce a meaningful artifact, result, capability, procurement, or gate?
5. Would combining it with another card make the combined card confusing or unsafe?

## Merge-Trigger List

Merge a smaller card into its parent task when the card's only significance is one of the following:

* One conductor.
* One connector.
* One solder group.
* One subset of GPIO signals.
* One intermediate continuity check.
* One test-pattern variant.
* Recording evidence from the immediately preceding card.
* Committing evidence.
* Configuration existing solely to perform the same card's test.

Safety does not automatically require another card. When safety is part of a larger task, it requires ordered steps and a named safety profile inside that larger task.

## Novice-Executability / Goal-Path Standard

For every surviving hardware or integration task, the following must be verifiable from the task and its referenced runbooks:

* **Purpose** — Why is this task being done?
* **Starting state** — What must already be true before beginning?
* **Exact action** — What precise physical or logical action is performed?
* **Interfaces** — What connectors, pins, protocols, or APIs are touched?
* **Order** — What must happen before, during, and after this step?
* **Power state** — Is power applied, removed, or monitored?
* **Pre-power verification** — What must be checked before energizing?
* **Success state** — What observable state confirms completion?
* **Failure handling** — What is the safe recovery if the step fails?
* **Hidden knowledge** — What unstated electrical, wiring, firmware, or tool knowledge is required?
* **Next capability unlocked** — What downstream task becomes possible after this one?

Jira compatibility must not influence task granularity. Define the right project-level task first; mechanical Jira mapping is a separate, deferred step.

---

# Required Fields for Every Jira Task

Every task in the backlog uses this compact schema. The full reference with
examples is in `docs/pm/runbooks/task-format.md`.

## Required fields (present in every task)

* **ID + Title** — in the `### AF-XXX — Title` heading
* **Milestone** — `M0`, `M1`, `M2`, `MG`, `M3`, `M4`, `MR`, `MA`, `Cond-X`, `MF`
* **Depends on** — what must complete first (replaces Prerequisites + Blocked by)
* **Labels** — from the label taxonomy
* **Do** — numbered action steps (what to physically do)
* **Done when** — verifiable acceptance criteria (replaces Expected result + Acceptance criteria)
* **If it fails** — blocker/failure response

## Optional fields (use only when relevant)

* **Applies if:** condition (e.g., `ADR-016 selects ESP32`) — for conditional tasks; must state WHY the task is conditional
* **Safety:** profile name (e.g., `MAINS`, `HUB75`) — references `docs/pm/runbooks/safety.md`
* **Stop condition:** one sentence — required alongside Safety for dangerous tasks
* **Resolves:** `U-XXX` — when task resolves specific uncertainties
* **Procedure:** `EXP-XXX` — when a formal experiment defines the method
* **Wiring:** `path §section` — when specific wiring docs apply
* **Parts:** (list) — only for unusual/non-obvious items not already in BOM
* **Context:** (1 sentence) — only when the title + Depends on doesn't make purpose obvious

## Fields that do NOT appear in task bodies

These are either redundant with the heading/file structure, or belong in shared
runbooks rather than repeated per task:

* Task ID (in heading), Epic (in file/Jira metadata), Summary (in heading)
* Description / Why this task exists (use Context: only if needed)
* Required hardware / tools / software (BOM + procedure covers this)
* Long evidence-to-save sections (convention in `docs/pm/runbooks/evidence.md`)
* Full safety checklists (canonical rules in `docs/pm/runbooks/safety.md`)
* Verbose source references (keep only direct refs: `Procedure: EXP-011`)

## Factual validation rule

For any quantitative or electrical assertion in a task:

1. PROJECT.md / BOM.md accepted project specification
2. Actual received-hardware evidence
3. Manufacturer documentation already cited/recorded in repo
4. Accepted ADR
5. Otherwise → UNKNOWN

An agent must not convert UNKNOWN into a plausible value. Write a verification
task instead.

---

# Issue Types

To maximize Jira portability, use:

* `Epic`
* `Task`

Represent special task types through labels rather than assuming Jira has custom issue types.

Useful labels include:

* `spike`
* `decision`
* `hardware`
* `software`
* `firmware`
* `power`
* `safety`
* `controller-wf4`
* `controller-esp32`
* `controller-wf2`
* `mechanical`
* `critical-path`
* `blocked`
* `conditional`

---

# Safety Rules

The canonical safety checklists are maintained in `docs/pm/runbooks/safety.md`.
Tasks reference safety profiles by name (e.g., `**Safety:** MAINS`) plus a
one-line local stop condition for dangerous tasks.

The following rules remain authoritative and are consolidated in the runbook:

Mains work is high risk.

Tasks involving 230 V AC must clearly identify the hazard and require:

* power disconnected before wiring;
* verification of L/N/PE;
* protective earth;
* fuse protection;
* insulated connections;
* strain relief;
* continuity/inspection before energizing.

Never suggest:

* mains on breadboards;
* Dupont leads for mains;
* Dupont leads for high panel current;
* power-daisy-chaining the panels;
* measuring the complete display current through a normal multimeter's 10 A input;
* hot-plugging HUB75 cables.

---

# Architecture Rules from the Existing Project

Preserve these unless an explicit future ADR changes them:

* Application logic runs on Linux/Python.
* HUB75 real-time refresh is handled by a dedicated controller.
* The application renders a canonical framebuffer.
* Renderer code must remain independent of controller transport.
* Panels receive power in parallel.
* Signal chaining is allowed.
* Prefer wired Nano→controller transport where practical.
* Avoid unnecessary custom low-level firmware for V1.
* Do not build `Nano → ESP32 → WF4 → panel`; WF4 and ESP32 are alternative controller paths.

---

# Software Planning

The software and firmware areas are currently immature/mostly empty, so explicitly create bootstrap tasks.

The first software objective is NOT weather/calendar/Spotify.

It is:

`arbitrary input text → Pillow framebuffer on Nano → transport adapter → physical panel`

Build outward from that.

Plan software roughly in this order:

1. Nano OS.
2. Wi-Fi/SSH.
3. Minimal Python environment.
4. Pillow text renderer.
5. standard test-pattern renderer.
6. canonical framebuffer abstraction.
7. transport interface.
8. candidate transport implementation.
9. physical end-to-end text.
10. scaling framebuffer dimensions.
11. reliability/recovery.
12. only then higher-level widgets/APIs.

Later V1 features include:

* time/date;
* weather;
* Google Calendar;
* Spotify now playing;
* album artwork;
* arbitrary artwork;
* caching;
* offline fallback;
* structured logs;
* service supervision;
* boot-time startup.

Do not put these ahead of basic display validation.

---

# Conditional PCB Work

If and only if the ESP32 architecture is selected:

Create tasks for:

1. freeze exact ESP32 footprint;
2. freeze validated GPIO mapping;
3. KiCad schematic;
4. two SN74HCT245 stages;
5. decoupling;
6. HUB75 keyed connector;
7. power/GND;
8. ERC;
9. PCB layout;
10. DRC;
11. physical/connector-orientation review;
12. Gerbers;
13. drill files;
14. BOM;
15. CPL if assembled;
16. prototype manufacture;
17. continuity test;
18. one-row hardware validation;
19. replacement of perfboard only after validation.

Do not create the PCB as an unconditional prerequisite for the first working panel.

The perfboard adapter exists specifically to validate the architecture first.

---

# Mounted Frame Phase

After the six-panel prototype works, create detailed optimization tasks for:

* exact component dimensions;
* internal layout;
* minimum frame depth;
* PSU location;
* controller location;
* mains/low-voltage separation;
* cable routing;
* strain relief;
* protective-earth bonding;
* serviceability;
* ventilation;
* passive-cooling feasibility;
* thermal testing;
* brightness ceiling;
* nighttime brightness;
* Wi-Fi performance inside enclosure;
* panel mechanical mounting;
* prototype backplate;
* final frame;
* repeated power recovery;
* extended unattended testing.

Do not prematurely resolve ADR-024.

---

# Required Output

Do not start implementation yet.

> [!NOTE]
> **Jira export is deferred.** Jira compatibility must not influence task granularity.
> The artifacts in this section are planning deliverables for the repository backlog.
> Jira import tables and CSV drafts are future mechanical views generated from the
> canonical backlog when export is actually needed; they are not current project
> deliverables and must not drive how tasks are sized or split.

First produce:

## 1. Repository Audit

Summarize what exists, what is empty, what is branch-only, and what appears stale.

## 2. Requirements Matrix

Map major product requirements to the repo source that defines them.

## 3. Uncertainty / Decision Register

For each unknown include:

* question;
* evidence currently available;
* experiment/task that resolves it;
* downstream tasks it blocks.

## 4. Milestone Dependency Graph

Explicitly show:

`hardware receipt`
→ `safe power`
→ `Nano renderer`
→ `one panel`
→ `two panels`
→ `architecture decision`
→ `four panels`
→ `six panels`
→ `application completion`
→ `mounted-frame optimization`

Show parallel WF4/ESP32/WF2 branches where appropriate.

## 5. Ordered Atomic Backlog

Generate every task necessary to complete the project.

Order it so someone can execute it from top to bottom.

Clearly identify tasks that can happen in parallel.

## 6. Critical Path

Produce a separate shortest safe path to:

**arbitrary text rendered by the Nano appearing on one physical panel.**

Then show the critical path from there to:

* two panels;
* four panels;
* six panels.

## 7. Existing Experiment Coverage

Map every existing `EXP-xxx` to the new backlog.

Do not lose existing experiments simply because you split them into smaller Jira tasks.

## 8. ADR Coverage

For every ADR, indicate:

* accepted constraint;
* pending decision;
* tasks providing evidence;
* milestone at which it should be resolved.

## 9. Jira Import Table (Future Mechanical View)

When Jira export is eventually enabled, produce a normalized table containing the following columns aligned with the compact schema. This table is generated mechanically from the canonical backlog; it is not a current planning deliverable and must not influence task granularity.

* `Issue Type` — `Epic` or `Task`.
* `Epic` — Parent Epic identifier (e.g. `AF-001`).
* `Task ID` — Unique task identifier (`AF-XXX`).
* `Summary` — Concise title from the task heading.
* `Milestone` — Target milestone code (`M0`, `M1`, `M2`, `MG`, `M3`, `M4`, `MR`, `MA`, `Cond-X`, `MF`).
* `Depends On` — Prerequisite task IDs that must complete first.
* `Labels` — Space- or comma-delimited labels from the label taxonomy.
* `Applies If` — Condition for conditional execution (or empty / `N/A` if unconditional).
* `Context` — Context or purpose explaining why the task exists (maps to the task's `Context:` field if present, or brief summary).
* `Acceptance Criteria` — Verifiable criteria defining completion (represents the task's `Done when` criteria, abbreviated for table view).
* `Procedure / References` — Direct references to experiments (`EXP-XXX`), wiring sections, ADRs, or BOM specifications.

## 10. Jira CSV Draft (Future Mechanical View)

When Jira export is eventually enabled, generate a Jira-importable CSV draft matching the exact columns of the Jira Import Table:

`Issue Type,Epic,Task ID,Summary,Milestone,Depends On,Labels,Applies If,Context,Acceptance Criteria,Procedure / References`

Ensure standard RFC-4180 CSV compliance (proper quoting of fields with commas/newlines).

Do not push anything to Jira yet.

## 11. Coverage Audit

At the end, ask yourself:

* Is every purchased component used, tested, explicitly optional, or explicitly rejected?
* Does every experiment map to backlog work?
* Does every pending ADR have evidence-producing tasks?
* Does every milestone have objective acceptance criteria?
* Are hidden assumptions represented as tasks?
* Is any task too large to execute without discovering multiple hidden subtasks?
* Is there any point where a novice could accidentally connect power before verifying polarity?
* Is there any point where work is scheduled before the decision that makes it relevant?
* Does the shortest path genuinely reach Nano-generated arbitrary text on a physical panel?

Fix any problems you find before presenting the final plan.

After completing the analysis, present the plan for review. Do not execute hardware changes, rewrite architecture decisions, purchase hardware, or push Jira issues until explicitly instructed.
