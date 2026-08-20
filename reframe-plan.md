Yes. I would **stop the current migration plan before M4** and replace it with a compression-first refactor. The existing plan is still useful as an inventory of what remains, but its assumption that the remaining 62 historical tasks should mostly survive as live cards is no longer the right objective. 

The governing principle should become:

> **A Jira task represents a meaningful project-management outcome, not every independently testable construction step.**
>
> Detailed physical steps, soldering sequences, connector checks, test patterns, and safety checks belong inside the task's `Do` section, an experiment, or a shared runbook unless they independently matter to project status.

That requires changing some of the structure we've already migrated, because the current compact schema still says an atomic task is one independently verifiable outcome, which has encouraged us to split things too aggressively.

# Compression-First PM Refactor Plan

## Phase 0 — Freeze the current migration

Do **not** execute the latest M4→MF migration plan.

Keep it as a reference for:

* historical ID inventory;
* factual corrections already discovered;
* experiment/ADR mappings;
* Jira generator requirements;
* safety findings;
* source-of-truth cleanup.

But explicitly supersede its **task-granularity strategy**.

No Jira regeneration yet.

---

## Phase 1 — Change the definition of a Jira-worthy task

Modify:

* `docs/pm/runbooks/task-format.md`
* `docs/pm/backlog/README.md`
* relevant explanation in `docs/JIRA.md`

Replace the current interpretation of:

> one independently verifiable outcome

with:

> **one meaningful project-level outcome**

A task may contain multiple independently checkable construction stages when those stages collectively produce one artifact/result.

### New Jira-worthiness test

Before creating or retaining a live AF task, ask:

1. **Does completing this materially change project status?**
2. **Would I want to see this independently on a Jira board?**
3. **Can this independently block another workstream, require an external wait, or require a separate decision?**
4. **Does it produce a meaningful artifact, experiment result, capability, procurement result, or gate?**
5. **Would combining it with adjacent work make the resulting task genuinely confusing or unsafe?**

A task normally survives if several of those are true.

A task should normally be merged when its only significance is:

* one conductor;
* one connector;
* one solder group;
* one subset of GPIO signals;
* one intermediate continuity check;
* one test-pattern variant;
* recording evidence from work performed in the immediately preceding card;
* committing evidence;
* configuration that exists solely to perform the test in the same card.

Safety does **not** automatically require another Jira issue. Safety can instead require explicit ordered steps and a named safety profile inside the larger task.

### New desired structure

For example:

**Jira card**

> `AF-054 — Build and electrically validate ESP32 HUB75 adapter`

**Inside that card**

1. lay out ICs/header;
2. wire power/ground/decoupling;
3. wire RGB/A/B;
4. wire C/D/E/CLK/LAT/OE;
5. inspect;
6. continuity-test every intended path;
7. isolation-test unintended paths;
8. photograph and record results.

That is one useful outcome: **a verified adapter now exists**.

---

# Phase 2 — Retroactively compress M0–M3

Before migrating anything else, audit the backlog we've already converted.

Create a temporary migration document such as:

`docs/pm/task-compression-audit.md`

For every current live task, record:

| Current ID(s)          | Decision | Surviving ID | Project-level outcome                | Detail moved to         |
| ---------------------- | -------- | ------------ | ------------------------------------ | ----------------------- |
| AF-014–017             | MERGE    | AF-014       | Assemble and verify AC mains harness | task Do + MAINS runbook |
| AF-054/055/059/061/065 | MERGE    | AF-054       | Build and validate ESP32 adapter     | task Do + wiring docs   |
| …                      | …        | …            | …                                    | …                       |

Use the existing stable-ID mechanism:

* keep the ID that most naturally represents the resulting outcome;
* rename/rewrite that task;
* absorbed historical IDs become `SUPERSEDED`;
* each superseded entry says `Replaced by: AF-XXX`;
* rewire downstream dependencies to the surviving task;
* preserve full lineage in `refactor-map.md`;
* never reuse the absorbed IDs.

### M0 compression

The current M0 structure demonstrates the problem especially clearly. AF-014, AF-015 and AF-016 individually wire L, N and PE, followed by AF-017 just to inspect the complete harness.

Those should become something closer to:

* **Verify C14 routing and protection**
* **Practice/validate mains terminations** — worthwhile separately because this is novice skill acquisition before dangerous work.
* **Assemble and verify C14→PSU mains harness** — L, N, PE and final continuity/isolation are one task.
* **Run PSU no-load test**
* **Prepare/verify panel power connection**
* **Run first-panel power test**
* batch-verification tasks for the remaining panels/harnesses where useful, rather than one card per connector.

The detailed L/N/PE instructions remain; they simply stop being three board cards.

### M1 ESP32 compression

Strong merge candidate:

`AF-054 + AF-055 + AF-059 + AF-061 + AF-065 → AF-054`

New outcome:

> **Build and electrically validate one ESP32 HUB75 adapter**

Keep separately:

* identify/measure the ESP32;
* validate GPIO map;
* acquire missing adapter materials if that is genuinely blocked externally;
* build validated adapter;
* flash/configure firmware;
* validate one physical panel;
* validate Nano→ESP32 transport.

Those are meaningful project transitions.

### M1 WF4/WF2/Nano

Use the same rule.

Do **not** automatically merge everything. Candidate-controller experiments, protocol investigations and Nano bootstrap outcomes often deserve separate cards because they answer different architectural questions.

But collapse sequences like:

> configure → run one test → save evidence

when all three are simply parts of **“validate WF4 one-panel operation.”**

### M2 compression

Current M2 has separate wiring/configuration/pattern/seam cards for both controller candidates.

Target structure should be closer to:

* verify/prepare second panel;
* **validate WF4 256×64 path**;
* **validate ESP32 256×64 path**;
* verify Nano-originated seam-crossing behavior where that genuinely isn't already part of those validations;
* M2 gate / evidence consolidation.

A controller path should generally be **one experiment card**, not four Jira cards.

### MG

MG is already close to useful granularity:

* scoring/evidence matrix;
* controller decision;
* transport decision;
* downstream reclassification.

I would probably leave those four.

### M3 compression

Current M3 should be reviewed especially around:

* topology assembly + first light;
* seam tests;
* synchronization;
* coordinates;
* stability.

EXP-017 already defines one coherent four-panel validation experiment. Having separate cards for every subsection makes the board look busier without necessarily conveying more useful status.

A likely target is:

* verify panels #3/#4;
* prepare selected controller topology;
* extend Nano to 256×128;
* **validate complete 2×2 display under EXP-017**
  including seams, row update observation, coordinate mapping and stability;
* M3 gate.

The ESP32 branch may retain a separate procurement task if additional hardware is missing because **waiting for a shipment is project-visible status**.

---

# Phase 3 — Set compression targets before migrating M4+

Do not use historical task counts as requirements.

The old:

> Cond-X has 19 historical steps, therefore 19 live tasks

logic should disappear.

Instead, define approximate **review targets**, not hard quotas:

| Phase  |                       Rough live-card target |
| ------ | -------------------------------------------: |
| M0     |                                        ~6–10 |
| M1     | ~15–25 because it contains real parallel R&D |
| M2     |                                         ~4–6 |
| MG     |                                           ~4 |
| M3     |                                         ~5–7 |
| M4     |                                         ~7–9 |
| MR     |                                           ~2 |
| MA     |                                         ~6–8 |
| Cond-X |                                         ~6–8 |
| MF     |                                        ~7–10 |

These are warning signals, not validation rules.

If M4 ends up with 10 meaningful tasks, fine. If it ends up with 19 because we made one card for every solder group, something has gone wrong.

The overall goal should be roughly **65–85 live tasks**, not ~177.

---

# Phase 4 — Migrate M4 under the new model

I would abandon the proposed eight-new-ID third-controller chain.

A much more useful M4 backlog is approximately:

### AF-109 — Verify panels #5/#6 for final integration

Polarity, orientation, physical condition.

### AF-110 — Bring up six-panel WF4 topology

Conditional WF4 branch.

Inside it:

* X1/X2/X3 topology;
* six parallel branches;
* configuration;
* first light.

### AF-111 — Bring up six-panel ESP32 topology

Conditional ESP32 branch.

Inside it:

* ensure third controller path exists;
* build another adapter using the proven adapter procedure;
* configure the third controller from the **actually proven ESP32 configuration**;
* assemble three rows;
* first light.

Only split out a new procurement card if hardware is actually missing or purchasing is known in advance to be an externally blocked step.

### AF-112 — Extend canonical framebuffer to 256×192

One software outcome.

### AF-113 — Validate full 256×192 canvas

Absorb the detailed seam/coordinate/full-pattern work where practical.

Outcome:

> arbitrary Nano-generated content maps correctly anywhere on all six panels.

### AF-115 — Characterize brightness and loaded thermals

Brightness sweep + EXP-015 belong together because the useful PM outcome is:

> operating brightness/thermal behavior is understood.

AF-116 can be absorbed if it adds no independent project-level status.

### AF-117 — Validate boot, transport, Wi-Fi and API recovery

EXP-016/resilience outcome.

AF-118 can be absorbed if it is merely another subsection of that recovery exercise.

### AF-119 — M4 gate

Aggregate full-canvas, thermal and recovery evidence.

That leaves M4 at roughly **8 cards rather than 19**.

---

# Phase 5 — Compress the remaining future phases before writing them

## MR

The existing two outcomes make sense:

* determine operating brightness ceiling;
* 24-hour reliability test.

Keep roughly as-is.

## MA

Do **not** preserve 12 software-planning stages merely because JIRA originally listed twelve conceptual steps.

Compress them into capabilities such as:

1. **Productionize Nano runtime**

   * OS baseline
   * networking
   * Python environment

2. **Implement production renderer/framebuffer**

   * Pillow renderer
   * test patterns
   * framebuffer abstraction

3. **Implement selected controller transport**

4. **Validate application→physical-display integration**

5. **Implement service supervision/recovery**

6. **Implement dashboard data/widget framework**

7. **Implement caching/offline/API-failure behavior**

8. **MA acceptance/gate**, if a separate aggregator is useful.

Exact module names remain implementation choices unless already grounded.

## Cond-X

The 19 PCB steps in JIRA are **procedure/checklist coverage requirements**, not necessarily 19 cards.

Compress to roughly:

1. freeze ESP32 mechanical + GPIO interface;
2. produce PCB schematic;
3. produce PCB layout and pass ERC/DRC;
4. produce fabrication package — Gerbers, drill files, BOM, CPL where applicable;
5. order and receive prototype PCB;
6. electrically validate fabricated PCB;
7. validate one row and replace perfboard adapters.

All 19 original items must still be covered somewhere in the `Do`/`Done when` sections or PCB runbook.

That's the important distinction:

> **Coverage stays exhaustive; Jira stays concise.**

## MF

Likewise, the 18 frame requirements can become roughly:

1. capture physical/thermal design constraints;
2. design frame/internal layout;
3. design mains, PE, cable routing and strain relief;
4. design ventilation/thermal strategy;
5. fabricate backplate/frame;
6. install and align electronics/panels;
7. closed-enclosure thermal + Wi-Fi + recovery validation;
8. wall mount + 72-hour final validation/gate.

Do not create separate board cards for “route cable category A,” “route cable category B,” etc. Those become checklists.

---

# Phase 6 — Change the audit so it rewards simplicity

The coverage audit should no longer effectively ask:

> “Did every old step survive as a task?”

Instead it should ask two separate questions.

### Coverage audit

Every requirement, EXP step, ADR prerequisite and safety requirement must map to **somewhere**:

* live task;
* `Do` step;
* `Done when`;
* experiment;
* runbook;
* explicitly rejected/deferred item.

Nothing is lost.

### PM-utility audit

For every live Jira task:

* What project-level state changes when this moves to Done?
* Is that state useful to see independently on a board?
* Does this card merely represent part of constructing the same artifact as the preceding/following card?
* Would a project manager ever independently prioritize, defer or assign it?
* Could its details live safely in the containing task/runbook instead?

Add explicit anti-bloat checks:

* no task solely for one conductor;
* no task solely for one signal group unless it represents a genuine isolated experiment;
* no task solely for committing evidence;
* no task solely because a procedure has another numbered step;
* repeated per-panel work is batched where safe and logically identical;
* separate procurement tasks only when procurement creates a real external waiting state.

The current task-format requirement for objective `Done when` criteria remains useful. What changes is **the level at which we define the outcome**.

---

# Phase 7 — Reconcile IDs and support docs

After compression:

1. Mark absorbed IDs `SUPERSEDED`.
2. Rewire `Depends on` references to surviving IDs.
3. Update uncertainty resolvers.
4. Update experiment coverage.
5. Update ADR coverage.
6. Update critical path.
7. Update milestone graph.
8. Update `refactor-map.md` with all merge lineage.

The critical path should become dramatically easier to read.

A good sanity test is:

> Can you glance at the M0→M4 critical path and understand the major progression without reading a wiring procedure?

If not, the backlog is still too granular.

---

# Phase 8 — Only then regenerate Jira

Keep the correct JIRA-owned import schema from the latest plan. 

But generate Jira **after compression**, so the importer faithfully represents the useful backlog rather than forcing the repository to preserve hundreds of cards.

Generator rules remain:

* canonical epic registry + phase files;
* omit `SUPERSEDED`;
* stable IDs;
* one in-memory collection for Markdown + CSV;
* RFC-4180 CSV;
* dependency validation;
* no old monolith as authority.

---

## Revised execution order

I would now do:

**1. Rewrite granularity rules
→ 2. Compression audit M0–M3
→ 3. Apply M0/M1 compression
→ 4. Apply M2/MG/M3 compression
→ 5. Review the compressed backlog as a PM tool
→ 6. Migrate compressed M4
→ 7. Migrate compressed MR/MA/Cond-X/MF
→ 8. Reconcile support docs
→ 9. Regenerate Jira
→ 10. Run coverage + PM-utility audits
→ 11. Supersede the monolith and finalize migration**

I would make **“M0–M3 compression review” a hard gate before writing M4**. That gives us a real sample of whether the new philosophy works. If we can reduce those phases substantially without losing safety or execution clarity, then we use that pattern for everything remaining.

The core goal should now be:

> **The repo can still tell a novice exactly how to do the work, while Jira tells us only what meaningful work remains.**

Those are different interfaces, and trying to make every procedural instruction into a Jira task is what caused the bloat in the first place.

Agreed. I’d remove the Jira-export work as a major workstream entirely.

The markdown backlog should be the product. If the phase files have consistent headings, IDs, milestones, dependencies, conditions, labels, context, `Do`, `Done when`, and failure handling, converting that to Jira later is mechanical and not worth shaping the refactor around.

So I’d revise the plan around **three outputs only**:

1. **A genuinely useful Markdown backlog**

   * small enough to understand at a glance;
   * cards represent meaningful project outcomes;
   * procedures/checklists live inside cards or runbooks;
   * stable AF IDs and dependencies remain reliable.

2. **Complete traceability**

   * every old task is `KEEP`, `MERGE/SUPERSEDED`, or otherwise accounted for;
   * experiments, ADRs, uncertainties, safety requirements and purchased hardware remain covered;
   * nothing disappears merely because several Jira cards became one.

3. **A PM-utility audit**

   * does each live task deserve to exist as a task?
   * is the critical path understandable?
   * are phases reasonably sized?
   * could someone actually use this backlog to see what remains without scrolling through hundreds of microscopic actions?

### Revised execution sequence

**1. Rewrite the granularity rules**

Change `task-format.md`, backlog README, and relevant Jira/planning language so that:

> one task = one meaningful project-level outcome

Explicitly state that multiple construction/test stages can be checklist items inside one task.

**2. Compression-audit M0–M3**

For every existing task, assign:

* `KEEP`
* `MERGE INTO AF-XXX`
* `SUPERSEDED BY AF-XXX`

Do this before changing files so we can review the proposed compression globally.

The audit should specifically flag:

* one-wire/one-connector tasks;
* individual soldering groups;
* configure/test/evidence triplets;
* evidence-only commit tasks;
* repetitive per-panel work;
* experiment subsections that should be one experiment task.

**3. Apply compression to M0/M1**

This is the best stress test because those phases currently contain the most procedural granularity.

Examples:

* merge L/N/PE wiring + inspection into **assemble and verify mains harness**;
* merge ESP32 adapter construction stages into **build and electrically validate HUB75 adapter**;
* keep actual procurement, architecture experiments, firmware bring-up and physical validation separately where they change project status.

Then review whether the resulting backlog is actually nicer to use.

**4. Apply compression to M2/MG/M3**

Target controller validation at experiment/outcome level.

For example M3 should likely look roughly like:

* prepare extra panels;
* prepare selected controller hardware;
* implement 256×128 Nano path;
* validate complete 2×2 system with EXP-017;
* M3 gate.

Not one Jira card for seam testing, one for coordinate labels, one for synchronization, one for stability unless one genuinely deserves independent scheduling.

**5. Establish the pattern for future phases**

Only after M0–M3 looks good, migrate M4+ directly at the new granularity.

Roughly:

* **M4:** ~6–9 meaningful outcomes
* **MR:** ~2
* **MA:** ~6–8
* **Cond-X:** ~6–8 rather than 19 PCB procedure cards
* **MF:** ~7–10 rather than one task per mechanical consideration

No hard quotas; these are bloat alarms.

**6. Reconcile traceability docs**

Update:

* `refactor-map.md`
* experiment coverage
* ADR coverage
* uncertainty register
* critical path
* milestone graph
* PM README

The experiment coverage document should distinguish between:

> “This requirement is covered”

and

> “This requirement has its own task.”

Those should not be synonymous.

**7. Run two audits**

First, a **coverage/safety audit**:

* every experiment procedure covered somewhere;
* every ADR prerequisite represented;
* every uncertainty has a resolver;
* no missing hardware/safety requirement;
* dependencies valid;
* no premature architecture assumptions.

Second, a new **backlog usefulness audit**:

* can this task be meaningfully prioritized independently?
* does Done change project status?
* is it merely a substep of creating the same artifact?
* would someone looking at the board understand progress from its title?
* could its procedure safely live inside another task?
* is the phase comprehensible without opening every card?

Any task failing that test should be merged.

**8. Finalize Markdown as canonical**

Then supersede the monolith and declare the phase files canonical.

I would reduce Jira export to essentially one sentence in the final plan:

> **Jira export is deferred.** Once the canonical Markdown backlog is stable, generate/import Jira issues mechanically from the structured fields; Jira compatibility must not influence task granularity.

That keeps the priority straight: **first build a good project plan; Jira is just one possible view of it.**
