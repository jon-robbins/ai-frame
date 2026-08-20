# Compression-First PM Refactor Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use subagent-driven-development (recommended) or executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild the backlog around meaningful project-level outcomes while preserving novice executability — compress M0–M3 retroactively, migrate M4–MF at the new granularity, reconcile traceability, and make the Markdown backlog the canonical product with Jira export deferred.

**Architecture:** The phase-file registry under `docs/pm/backlog/` stays canonical; task granularity changes from "one independently verifiable outcome" to "one meaningful project-level outcome" (construction/test stages become `Do` steps inside cards, runbook items, or experiment coverage). **Compression targets the PM surface, not the technical guidance.** Stable AF IDs survive; absorbed tasks become `SUPERSEDED` with `Replaced by:` lineage. The prior migration plan (`2026-08-19-pm-migration-completion.md`) is FROZEN as reference only — its task-granularity strategy (including the AF-188..AF-195 third-controller chain and Jira regeneration workstream) is superseded.

**Tech Stack:** Markdown docs, grep/awk verification. No Python generator in this plan — Jira derivation is deferred until the backlog is stable.

**Spec:** This implementation plan is self-contained and authoritative for the compression refactor. If `reframe-plan.md` (repo root, 2026-08-19) is present locally, it is supporting planning history only. The frozen plan `docs/superpowers/plans/2026-08-19-pm-migration-completion.md` remains reference for historical ID inventory, factual corrections (HUB75 OUT→IN notation, EXP-006 vs EXP-012 scope, 19 KiCad steps, AF-052 identity), generator requirements, and safety findings.

## Global Constraints

- **Governing principle (verbatim from spec):** "A Jira task represents a meaningful project-management outcome, not every independently testable construction step." Detailed physical steps, soldering sequences, connector checks, test patterns, and safety checks belong inside the task's `Do` section, an experiment, or a shared runbook unless they independently matter to project status.
- **Compression targets the PM surface, not the technical guidance (verbatim from spec):** "Compress the PM surface, not the technical guidance." A project-level task may absorb multiple construction or test stages, but the resulting task and its referenced procedures must remain executable by a novice without relying on unstated electrical, wiring, firmware, or tool knowledge. If merging makes the physical procedure ambiguous, preserve the necessary detail inside the task/runbook or do not merge those tasks.
- **Novice-executability / goal-path standard (verbatim from spec):** For every surviving hardware or integration task the following must be verifiable:
  - **Purpose:** Why does this task exist and what system capability does it create?
  - **Starting state:** What parts, previous results, configuration, and verified conditions must already exist?
  - **Exact action:** Can a novice tell what to physically build/connect/configure, rather than being told vaguely to "wire the controller" or "set up the display"?
  - **Interfaces:** Are both ends of important electrical/data connections clearly identified?
  - **Order:** Are actions presented in a safe, unambiguous sequence?
  - **Power state:** Is it explicit when everything must be unpowered and when energization becomes allowed?
  - **Pre-power verification:** Where relevant, does the task say what must be inspected/measured before power is connected?
  - **Success state:** Can I objectively tell whether the task worked?
  - **Failure handling:** If something fails, does it tell me what to isolate/check rather than merely saying "troubleshoot"?
  - **Hidden knowledge:** Does execution depend on electrical/firmware knowledge that an experienced engineer might assume but I would not know?
  - **Next capability unlocked:** What can I now do that I could not do before?
- **Audit row rule:** one row per proposed surviving outcome / merge group, with every source AF ID appearing exactly once in the `Current ID(s)` column. No ID may belong to two proposed survivor groups.
- **Three outputs only:** (1) a genuinely useful Markdown backlog; (2) complete traceability; (3) a coverage/safety audit, a PM-utility audit, and a novice-executability / goal-path audit. Nothing else is a deliverable.
- **Coverage stays exhaustive; cards stay concise.** Every historical step, EXP procedure step, ADR prerequisite, and safety requirement must map to somewhere — live task, `Do` step, `Done when`, experiment, runbook, or explicitly rejected/deferred item. Merging cards never deletes coverage.
- **Jira export is deferred.** `09-jira-import-table.md` and `10-jira-import.csv` are marked stale/deferred, NOT regenerated. Jira compatibility must not influence task granularity.
- **Stable IDs are frozen:** never renumber, reuse, or delete. Absorbed tasks become `### AF-XXX — SUPERSEDED` with a `Replaced by: AF-YYY` line; dependencies rewire to the survivor; full lineage recorded in `refactor-map.md`. Next available ID: **AF-188**. No new IDs are planned; allocate AF-188+ only when Gate A/B or the goal-path audit discovers a genuinely missing project-level outcome or a real externally blocked action that cannot sensibly belong inside an existing card.
- **Registry validation is self-contained and repeatable:** after every compression task, run a single inline Python validator that (a) enumerates all IDs, (b) enumerates SUPERSEDED IDs, (c) inspects `Depends on:` only for active tasks, (d) fails if an active dependency references a missing ID, and (e) fails if an active dependency references a SUPERSEDED ID.
- **Safety encoding:** safety does NOT automatically justify a separate card. Named safety profiles (`MAINS`, `HUB75`, `CH340`, `5V-HIGH-CURRENT` from `docs/pm/runbooks/safety.md`) plus ordered steps and task-specific `Stop condition:` live inside the containing task. This supersedes the earlier "L/N/PE as separate tasks" exception — the mains harness (L, N, PE, continuity/isolation) is one card, with a separate card only for novice termination practice (skill acquisition before dangerous work).
- **Targets are bloat alarms, not quotas:** M0 ~6–10, M1 ~15–25, M2 ~4–6, MG ~4, M3 ~5–7, M4 ~7–9, MR ~2, MA ~6–8, Cond-X ~6–8, MF ~7–10; overall ~65–85 live tasks. A phase may exceed its alarm if every card passes the utility test.
- **Two hard human gates:** **Gate A** after Task 2 (compression audit reviewed globally before any phase-file edit); **Gate B** after Task 6 (M0–M3 compressed backlog reviewed as a PM tool before any M4+ migration). Execution stops and presents at both.
- **Controller-conditionality unchanged:** two-class AF-096 winner/loser semantics, `Applies if: ADR-016 selects multi-ESP32` sole clause, ADR-013 prerequisites as dependencies (from the approved frozen-plan text — carried forward verbatim).
- Carry-forward factual corrections from the frozen plan: HUB75 chains in explicit OUT→IN hops; EXP-006 = WF4 six-panel procedure, ESP32 M4 = integration informed by EXP-011/012/013; Cond-X = 19 KiCad steps; AF-052 = GPIO-map validation; MA monolith identifiers are historical proposals, not contracts.

---

### Task 1: Rewrite the granularity rules

**Files:**
- Modify: `docs/pm/runbooks/task-format.md`
- Modify: `docs/pm/backlog/README.md` (task-rules section)
- Modify: `docs/JIRA.md` (granularity/planning-language section only)

**Interfaces:**
- Produces: the new task definition consumed by every later task; the backlog-worthiness / PM-utility test; the merge-trigger list; the safety-inside-task rule. All later tasks quote these rules in their compression decisions.

- [ ] **Step 1: Replace the task-size definition in `task-format.md`**

Replace the "one independently verifiable outcome" definition (and its size guidance) with:

```markdown
A task is **one meaningful project-level outcome**. A task may contain multiple
independently checkable construction stages when those stages collectively produce
one artifact or result. Detailed physical steps, soldering sequences, connector
checks, test patterns, and safety checks live inside the task's `Do` section, an
experiment, or a shared runbook unless they independently matter to project status.

**Compression must never remove execution guidance.** A project-level task may absorb
multiple construction or test stages, but the resulting task and its referenced
procedures must remain executable by a novice without relying on unstated electrical,
wiring, firmware, or tool knowledge. If merging makes the physical procedure
ambiguous, preserve the necessary detail inside the task/runbook or do not merge
those tasks.
```

- [ ] **Step 2: Add the backlog-worthiness / PM-utility test (verbatim from spec)**

The five questions (materially change project status? / independently visible on a board? / independently block, wait externally, or need a separate decision? / produce a meaningful artifact, result, capability, procurement, or gate? / would combining make it confusing or unsafe?) — a task normally survives if several are true.

- [ ] **Step 3: Add the merge-trigger list (verbatim from spec)**

Merge when a card's only significance is: one conductor; one connector; one solder group; one subset of GPIO signals; one intermediate continuity check; one test-pattern variant; recording evidence from the immediately preceding card; committing evidence; configuration existing solely to perform the same card's test. Plus: safety does not automatically require another card — it requires ordered steps and a named safety profile inside the larger task.

- [ ] **Step 4: Add the novice-executability / goal-path standard (verbatim from spec)**

For every surviving hardware or integration task the following must be verifiable:
**Purpose, Starting state, Exact action, Interfaces, Order, Power state, Pre-power verification, Success state, Failure handling, Hidden knowledge, Next capability unlocked.**

- [ ] **Step 5: Mirror the definition in `backlog/README.md` and update `docs/JIRA.md`**

`backlog/README.md` task-rules section: same definition, pointer to `task-format.md` as canonical. `docs/JIRA.md`: update granularity language to the new definition; change required-output sections so that Jira import table / CSV generation are documented as future mechanical views, NOT current project deliverables. Add explicit note: Jira export is deferred; Jira compatibility must not influence task granularity.

- [ ] **Step 6: Verify + commit**

```bash
grep -c 'one meaningful project-level outcome' docs/pm/runbooks/task-format.md docs/pm/backlog/README.md   # = 1 each
grep -n 'Compression must never remove execution guidance' docs/pm/runbooks/task-format.md docs/pm/backlog/README.md   # = 1 each
grep -n 'Jira export is deferred' docs/JIRA.md
git add docs/pm/runbooks/task-format.md docs/pm/backlog/README.md docs/JIRA.md
git commit -m "docs(pm): redefine task granularity as one meaningful project-level outcome"
```

---

### Task 2: Compression audit M0–M3 (GATE A — no phase-file edits in this task)

**Files:**
- Create: `docs/pm/task-compression-audit.md`

**Interfaces:**
- Consumes: all live tasks in `docs/pm/backlog/00-hardware-receipt.md` .. `09-quad-panel.md` (107 active), the Task-1 rules, spec Phase 2/3 targets.
- Produces: the decision table every apply-task executes; per-phase surviving-ID lists that Tasks 7–8's dependency rewiring consumes.

- [ ] **Step 1: Build the audit table using the Gate A review template**

For EVERY live task in files 00–09, one row. The required columns are:

```markdown
| Current ID(s) | Decision | Surviving ID | Project-level outcome | Purpose | Starting state | What I actually do | Physical/observable Done state | Next capability unlocked | Absorbed IDs | Execution guidance source | Novice guidance gap / required fix |
```

`Decision` ∈ `KEEP / MERGE / SUPERSEDED`. The `What I actually do` column is a concise description of the physical operation. The detailed ordered instructions (exact connections, power state at each stage, pre-power inspections, parts lists, failure isolation) live in the `Execution guidance source` column: the surviving task `Do`/`Done when`, a named experiment, a wiring section, or a runbook. The `Novice guidance gap` column captures anything missing that must be added when the merge is applied. Vague entries such as "wire the controller" or "set up the display" are defects; fix them before presenting.

Spec-mandated rows included verbatim:

- `AF-014, AF-015, AF-016, AF-017 → MERGE → AF-014` — "Assemble and verify AC mains harness". Purpose: 230 V AC can safely reach the 5 V PSU with protection, switching, and PE. Starting state: C14 inlet, fuse/switch, PSU, crimp terminals, multimeter. What I actually do: identify L/N/PE from the C14 tab routing/markings and PSU terminal labels (do not infer from conductor color alone); record which delivered conductor color is assigned to each; wire L/N/PE; install fuse/switch correctly; PE continuity to PSU FG; L/N open-circuit when switched off; absence of L/N→PE shorts; label. Done: harness assembly is complete and all required **pre-energization** inspection/continuity/isolation checks pass; the system is ready to proceed to AF-018. Next capability unlocked: AF-018 can safely attempt the first PSU no-load energization. Absorbed IDs: AF-014, AF-015, AF-016, AF-017. Execution guidance source: surviving AF-014 `Do` section + MAINS safety runbook (`docs/pm/runbooks/safety.md` §2). Novice guidance gap: ensure AF-014 explicitly invokes the MAINS runbook checks — verified L/N/PE identification, PE→FG continuity, fuse verification, isolation checks, and wall-plug accessibility — rather than duplicating them.
- `AF-054, AF-055, AF-059, AF-061, AF-065 → MERGE → AF-054` — "Build and electrically validate one ESP32 HUB75 adapter". Purpose: ESP32 outputs safely drive one HUB75 panel row through level-shifted 5 V signals. Starting state: ESP32, perfboard, 2× SN74HCT245N, 100 nF caps, HUB75 connector, BOM/ADR-014. What I actually do: layout ICs/header; wire power/ground/decoupling (place 100 nF ceramics close to each HCT245 VCC/GND pair; observe polarity only if the optional bulk electrolytic is used); wire RGB (R1/R2/G1/G2/B1/B2); wire row-address A/B and C/D/E/CLK/LAT/OE; inspect solder joints; 14-signal continuity; isolation check; photograph. Done: adapter passes unpowered continuity/isolation and the ESP32 can later be flashed without rework. Next capability unlocked: the verified adapter is ready for ESP32 firmware configuration and powered single-panel validation. Absorbed IDs: AF-054, AF-055, AF-059, AF-061, AF-065. Execution guidance source: surviving AF-054 `Do` section + validated GPIO map (AF-052) + HUB75 safety runbook (`docs/pm/runbooks/safety.md` §3) + ADR-014 wiring rationale. Novice guidance gap: ensure the `Do` section lists every pin pair, a 14-signal continuity checklist, and a clear photo/diagram.
- M2 target shape: verify/prepare second panel; validate WF4 256×64 path (one card); validate ESP32 256×64 path (one card); Nano seam-crossing check only if not already inside those validations; M2 gate/evidence consolidation.
- MG: keep the four (matrix, controller decision, transport decision, reclassification).
- M3 target shape: verify panels #3/#4; prepare selected controller topology; extend Nano to 256×128; validate complete 2×2 under EXP-017 (seams, row-update observation, coordinates, ≥30-min stability inside); M3 gate. ESP32 branch keeps the AF-099 audit → AF-187 procurement-resolution chain (external wait = project-visible status).

- [ ] **Step 2: Flag the spec's bloat categories per row**

Annotate rows hitting: one-wire/one-connector; individual solder groups; configure/test/evidence triplets; evidence-only commits; repetitive per-panel work; experiment subsections that should be one experiment task.

- [ ] **Step 3: Reconcile against targets and compute goal-path coverage**

Per-phase projected live counts vs. the alarm table; every over-target phase needs a one-line justification. Include the projected overall live count (goal ~65–85).

Additionally, trace the three goal paths **through M3 only** using the compressed surviving tasks and referenced procedures:

**Content/data path (M0–M3):** `Nano application / test patterns → framebuffer → selected transport → display controller → HUB75 signals → physical panels` (full user/API path is deferred until Task 8 / MA migration)
**Power path (M0–M3):** `230 V AC → protected/switched inlet → 5 V PSU → safe parallel DC distribution → panels/controllers` through first-panel and 256×64/128 energization
**Prototype progression (M0–M3):** `1 panel → 2 panels / 256×64 → architecture decision → 4 panels / 256×128`

For every arrow, the audit must name the surviving task(s) and/or runbook/experiment that explain how the transition works. Record any gap as a `COVERAGE GAP` row in the audit with a fix plan.

**STOP — Gate A.** Present `task-compression-audit.md` to the user. Do not edit any phase file until Gate A is approved.

- [ ] **Step 4: Verify + commit + GATE A**

```bash
# Verify audit row rule: every active M0–M3 ID appears exactly once in the first column,
# no unknown IDs appear, and no ID belongs to two merge groups.
python3 - <<'PY'
from pathlib import Path
import re, sys
live = set()
for f in sorted(Path('docs/pm/backlog').glob('0*.md')):
    for line in f.read_text().splitlines():
        if re.match(r'###\s+AF-\d{3}\s+', line) and 'SUPERSEDED' not in line:
            live.add(re.search(r'AF-\d{3}', line).group())
first_col_ids = []
seen = set()
dupes = set()
for line in Path('docs/pm/task-compression-audit.md').read_text().splitlines():
    if not line.startswith('|') or 'AF-' not in line: continue
    cell = line.split('|')[1].strip()
    ids = re.findall(r'AF-\d{3}', cell)
    for i in ids:
        if i in seen: dupes.add(i)
        seen.add(i)
    first_col_ids.extend(ids)
seen_set = set(first_col_ids)
unknown = seen_set - live
missing = live - seen_set
errs = []
if unknown: errs.append(f'Unknown IDs in first column: {sorted(unknown)}')
if missing: errs.append(f'Missing IDs from first column: {sorted(missing)}')
if dupes: errs.append(f'Duplicate IDs across rows: {sorted(dupes)}')
if errs:
    print('\n'.join(errs))
    sys.exit(1)
print('audit row rule: OK')
PY

git add docs/pm/task-compression-audit.md
git commit -m "docs(pm): add M0-M3 compression audit (Gate A input)"
```

**STOP — present `task-compression-audit.md` to the user. No phase file is edited until the audit is approved.**

---

### Task 3: Apply M0 compression (files 00–01)

**Files:**
- Modify: `docs/pm/backlog/00-hardware-receipt.md`, `docs/pm/backlog/01-power-bringup.md`
- Modify: `docs/pm/refactor-map.md` (merge lineage section)

- [ ] **Step 1:** Execute the approved audit rows for M0: rewrite surviving cards (title = project-level outcome; absorbed detail into `Do`/`Done when`; safety profiles + stop conditions inside); mark absorbed cards `### AF-XXX — SUPERSEDED` with `Replaced by: AF-YYY`; rewire in-file and cross-file `Depends on:` to survivors.
- [ ] **Step 2:** Append M0 merge lineage to `refactor-map.md` (every absorbed ID → survivor).
- [ ] **Step 3: Verify + commit**

```bash
# REUSABLE REGISTRY VALIDATOR: self-contained Python.
# Fails if an active task depends on a missing or SUPERSEDED ID.
python3 - <<'PY'
from pathlib import Path
import re, sys
ids = {}
deps = {}
for f in sorted(Path('docs/pm/backlog').glob('*.md')):
    cur = None
    for line in f.read_text().splitlines():
        m = re.match(r'###\s+(AF-\d{3})\s+', line)
        if m:
            cur = m.group(1)
            ids[cur] = ('SUPERSEDED' in line, f.name)
            continue
        if cur and line.startswith('**Depends on:**'):
            deps[cur] = re.findall(r'AF-\d{3}', line)
sup = {i for i, (s, _) in ids.items() if s}
errs = []
for i, ds in deps.items():
    if i in sup: continue
    for d in ds:
        if d not in ids: errs.append(f'{i} -> {d} MISSING')
        elif d in sup: errs.append(f'{i} -> {d} SUPERSEDED')
if errs:
    print('\n'.join(errs))
    sys.exit(1)
print('registry_check: OK')
PY

grep -c 'Replaced by:' docs/pm/backlog/01-power-bringup.md   # = audit's M0 absorbed count
git add -A docs/pm/backlog/00-hardware-receipt.md docs/pm/backlog/01-power-bringup.md docs/pm/refactor-map.md
git commit -m "docs(pm): compress M0 backlog to project-level outcomes per approved audit"
```

---

### Task 4: Apply M1 compression (files 02–06)

**Files:**
- Modify: `docs/pm/backlog/02-nano-bootstrap.md`, `03-wf4-single-panel.md`, `04-esp32-single-panel.md`, `05-wf2-investigation.md`, `06-single-panel-gate.md`
- Modify: `docs/pm/refactor-map.md`

- [ ] **Step 1:** Execute approved audit rows for M1. ESP32 adapter merge per Task-2 spec row (AF-054 survivor, 8-step Do). Keep separate: candidate-controller experiments, protocol investigations, Nano bootstrap outcomes, GPIO validation, externally-blocked procurement, firmware bring-up, physical panel validation, transport validation. Collapse configure→test→evidence triplets into "validate WF4 one-panel operation"-style cards.
- [ ] **Step 2:** Lineage to `refactor-map.md`; rewire dependencies everywhere (including M2/M3/MG files referencing absorbed M1 IDs — grep the whole registry).
- [ ] **Step 3: Verify + commit**

```bash
python3 - <<'PY'
from pathlib import Path
import re, sys
ids = {}
deps = {}
for f in sorted(Path('docs/pm/backlog').glob('*.md')):
    cur = None
    for line in f.read_text().splitlines():
        m = re.match(r'###\s+(AF-\d{3})\s+', line)
        if m:
            cur = m.group(1)
            ids[cur] = ('SUPERSEDED' in line, f.name)
            continue
        if cur and line.startswith('**Depends on:**'):
            deps[cur] = re.findall(r'AF-\d{3}', line)
sup = {i for i, (s, _) in ids.items() if s}
errs = []
for i, ds in deps.items():
    if i in sup: continue
    for d in ds:
        if d not in ids: errs.append(f'{i} -> {d} MISSING')
        elif d in sup: errs.append(f'{i} -> {d} SUPERSEDED')
if errs:
    print('\n'.join(errs))
    sys.exit(1)
print('registry_check: OK')
PY

git add -A docs/pm/backlog/ docs/pm/refactor-map.md
git commit -m "docs(pm): compress M1 backlog to project-level outcomes per approved audit"
```

---

### Task 5: Apply M2 + MG compression (files 07–08)

**Files:**
- Modify: `docs/pm/backlog/07-dual-panel.md`, `docs/pm/backlog/08-architecture-decision.md`
- Modify: `docs/pm/refactor-map.md`

- [ ] **Step 1:** Execute approved rows. M2 → ~4–6 cards (per-path validation cards, not wiring/config/pattern/seam quadruplets). MG → keep AF-093..AF-096 four; absorb sub-cards if any exist beyond them per audit. AF-096's registry-driven sweep text updated ONLY for ID changes from compression (two-class semantics verbatim).
- [ ] **Step 2:** Lineage + dependency rewiring (MG's AF-096 sweep enumeration references any absorbed IDs → survivors).
- [ ] **Step 3: Verify + commit**

```bash
python3 - <<'PY'
from pathlib import Path
import re, sys
ids = {}
deps = {}
for f in sorted(Path('docs/pm/backlog').glob('*.md')):
    cur = None
    for line in f.read_text().splitlines():
        m = re.match(r'###\s+(AF-\d{3})\s+', line)
        if m:
            cur = m.group(1)
            ids[cur] = ('SUPERSEDED' in line, f.name)
            continue
        if cur and line.startswith('**Depends on:**'):
            deps[cur] = re.findall(r'AF-\d{3}', line)
sup = {i for i, (s, _) in ids.items() if s}
errs = []
for i, ds in deps.items():
    if i in sup: continue
    for d in ds:
        if d not in ids: errs.append(f'{i} -> {d} MISSING')
        elif d in sup: errs.append(f'{i} -> {d} SUPERSEDED')
if errs:
    print('\n'.join(errs))
    sys.exit(1)
print('registry_check: OK')
PY

test "$(grep '^### AF-' docs/pm/backlog/07-dual-panel.md | grep -vc 'SUPERSEDED')" -le 8   # alarm check per audit
git add -A docs/pm/backlog/07-dual-panel.md docs/pm/backlog/08-architecture-decision.md docs/pm/refactor-map.md
git commit -m "docs(pm): compress M2/MG backlog per approved audit"
```

---

### Task 6: Apply M3 compression (file 09) — GATE B after

**Files:**
- Modify: `docs/pm/backlog/09-quad-panel.md`
- Modify: `docs/pm/refactor-map.md`
- Modify: `docs/pm/task-compression-audit.md` (new `## Gate B proposed M4+ survivor map` section)

- [ ] **Step 1:** Execute approved rows → target ~5–7 cards: verify panels #3/#4; prepare selected controller topology (WF4 and ESP32 branch cards with two-class conditional encoding); extend Nano to 256×128; validate complete 2×2 under EXP-017 (seams, row-update, coordinates, ≥30-min stability inside `Do`/`Done when`); M3 gate. ESP32 branch keeps AF-099 → AF-187 → assemble chain.
- [ ] **Step 2:** Lineage + rewiring; header normalization paragraph re-lists this file's conditional entries by surviving IDs.
- [ ] **Step 3:** Build the proposed M4–MF survivor map in `task-compression-audit.md` under `## Gate B proposed M4+ survivor map`. For each phase (M4, MR, MA, Cond-X, MF) list: surviving ID, project-level outcome, Purpose, What I actually do, Done state, Next capability unlocked, Absorbed IDs, Execution guidance source. Trace the full three goal paths through the finished product and record any `COVERAGE GAP`.
- [ ] **Step 4: Verify + commit + GATE B**

```bash
python3 - <<'PY'
from pathlib import Path
import re, sys
ids = {}
deps = {}
for f in sorted(Path('docs/pm/backlog').glob('*.md')):
    cur = None
    for line in f.read_text().splitlines():
        m = re.match(r'###\s+(AF-\d{3})\s+', line)
        if m:
            cur = m.group(1)
            ids[cur] = ('SUPERSEDED' in line, f.name)
            continue
        if cur and line.startswith('**Depends on:**'):
            deps[cur] = re.findall(r'AF-\d{3}', line)
sup = {i for i, (s, _) in ids.items() if s}
errs = []
for i, ds in deps.items():
    if i in sup: continue
    for d in ds:
        if d not in ids: errs.append(f'{i} -> {d} MISSING')
        elif d in sup: errs.append(f'{i} -> {d} SUPERSEDED')
if errs:
    print('\n'.join(errs))
    sys.exit(1)
print('registry_check: OK')
PY

test "$(grep '^### AF-' docs/pm/backlog/09-quad-panel.md | grep -vc 'SUPERSEDED')" -le 9   # alarm check per audit
git add -A docs/pm/backlog/09-quad-panel.md docs/pm/refactor-map.md docs/pm/task-compression-audit.md
git commit -m "docs(pm): compress M3 and add Gate B M4+ survivor proposal"
```

**STOP — Gate B.**
Gate B reviews:
- the actual compressed M0–M3 backlog (phase-file card lists + live counts);
- the proposed survivor map for AF-109–AF-170 (M4/MR/MA/Cond-X/MF) in `task-compression-audit.md`;
- the complete planned goal paths through the finished product.

Do not write files 10–14 until Gate B is approved.

---

### Task 7: Migrate compressed M4 (file 10)

**Files:**
- Modify: `docs/pm/backlog/10-six-panel.md` (stub)
- Modify: `docs/pm/refactor-map.md`

**Interfaces:**
- Consumes: monolith `05-backlog.md` AF-109..AF-119; approved M3 compressed structure; carry-forward factual corrections.
- Produces: ~8 live M4 cards using ONLY historical IDs (no AF-188+ allocation).

- [ ] **Step 1: Implement the Gate-B-approved M4 survivor map**

The example structure below is a **proposal only**; the Gate-B-approved survivor map in `task-compression-audit.md` is authoritative. If Gate B changed the survivors, follow the approved map, not this text.

Proposed M4 survivor set: AF-109 verify panels #5/#6; AF-110 bring up six-panel WF4 topology (conditional; X1/X2/X3 chains in OUT→IN hops, six power branches, config, first light inside — the surviving card or linked runbook must include exact WF4 configuration steps and the `panel OUT → next panel IN` chain so a novice can wire it); AF-111 bring up six-panel ESP32 topology (conditional; inside one card: ensure third-controller path, build adapter via proven adapter procedure, configure third controller from the actually-proven ESP32 configuration, assemble three rows, first light — the card/runbook must identify ctrl#1/#2/#3 row roles, HUB75 IN/OUT hops, and no-hot-plug de-energized connection rules; all HUB75 and panel-power connection changes occur de-energized, connector orientation and panel polarity are verified before energizing, and the proven energization procedure is followed without inventing signal-before-power or power-before-signal ordering); AF-112 extend framebuffer to 256×192; AF-113 validate full 256×192 canvas (absorbs seam/coordinate/pattern detail; outcome: arbitrary Nano content maps correctly anywhere); AF-115 characterize brightness + loaded thermals (absorbs the EXP-015-only card if it adds no independent status); AF-117 validate boot/transport/Wi-Fi/API recovery (absorbs the Wi-Fi-only card if merely a subsection); AF-119 M4 gate (converges full-canvas, thermal, recovery evidence).
- [ ] **Step 2:** AF-114/AF-116/AF-118 (and any other absorbed per Gate B) → `SUPERSEDED` + `Replaced by:`; AF-119 `Depends on:` the surviving convergence set.
- [ ] **Step 3:** Procurement contingency note inside AF-111: a separate procurement card is created at execution time ONLY if required third-controller hardware is actually missing (externally blocked = project-visible); it would take AF-188.
- [ ] **Step 4:** Refactor-map PART VI (compressed matrix + factual audit incl. EXP-006=WF4-only, ESP32=integration distinction); verify + commit.

```bash
python3 - <<'PY'
from pathlib import Path
import re, sys
ids = {}
deps = {}
for f in sorted(Path('docs/pm/backlog').glob('*.md')):
    cur = None
    for line in f.read_text().splitlines():
        m = re.match(r'###\s+(AF-\d{3})\s+', line)
        if m:
            cur = m.group(1)
            ids[cur] = ('SUPERSEDED' in line, f.name)
            continue
        if cur and line.startswith('**Depends on:**'):
            deps[cur] = re.findall(r'AF-\d{3}', line)
sup = {i for i, (s, _) in ids.items() if s}
errs = []
for i, ds in deps.items():
    if i in sup: continue
    for d in ds:
        if d not in ids: errs.append(f'{i} -> {d} MISSING')
        elif d in sup: errs.append(f'{i} -> {d} SUPERSEDED')
if errs:
    print('\n'.join(errs))
    sys.exit(1)
print('registry_check: OK')
PY

test "$(grep '^### AF-' docs/pm/backlog/10-six-panel.md | grep -vc 'SUPERSEDED')" -le 10
for i in 109 110 111 112 113 115 117 119; do grep -q "^### AF-$i " docs/pm/backlog/10-six-panel.md || echo "MISSING AF-$i"; done
git add -A docs/pm/backlog/10-six-panel.md docs/pm/refactor-map.md
git commit -m "docs(pm): migrate M4 at compressed granularity (8 project-level outcomes)"
```

---

### Task 8: Migrate compressed MR/MA/Cond-X/MF (files 11–14)

**Files:**
- Modify: `docs/pm/backlog/11-reliability.md`, `12-application.md`, `13-esp32-pcb.md`, `14-mounted-frame.md`
- Modify: `docs/pm/backlog/README.md` §4 (Cond-X row: 19 KiCad steps — carry-forward fix)
- Modify: `docs/pm/refactor-map.md`

- [ ] **Step 1: Implement the Gate-B-approved M4+ survivor map.** The example sets below are proposals only; the Gate-B-approved map in `task-compression-audit.md` is authoritative.

  - **MR (~2 cards):** determine operating brightness ceiling; 24-hour reliability test.
  - **MA (~6–8 cards):** productionize Nano runtime; production renderer/framebuffer; selected controller transport; application→display integration validation; service supervision/recovery; dashboard data/widget framework; caching/offline/API-failure behavior; optional MA gate. Module names stay implementation choices (monolith identifiers = historical proposals).
  - **Cond-X (~7 cards):** freeze ESP32 mechanical+GPIO interface; PCB schematic; layout + ERC/DRC pass; fabrication package (Gerbers/drill/BOM/CPL); order + receive prototype; electrically validate fabricated PCB; validate one row + replace perfboard adapters. ALL 19 original KiCad items covered in `Do`/`Done when` or the PCB runbook — coverage exhaustive, cards concise. Two-class conditional semantics; ADR-013 prerequisites as `Depends on: AF-096, AF-108, AF-052`.
  - **MF (~8 cards):** capture physical/thermal constraints; design frame/internal layout; design mains/PE/cable routing + strain relief; design ventilation/thermal strategy; fabricate backplate/frame; install + align electronics/panels; closed-enclosure thermal + Wi-Fi + recovery validation; wall mount + 72-hour final validation/gate. Mains cards: `Safety: MAINS` profile + specific stop conditions inside; L/N/PE and cable categories are checklist items, not cards.
- [ ] **Step 2:** Refactor-map PARTs VII–X; verify + commit.

```bash
python3 - <<'PY'
from pathlib import Path
import re, sys
ids = {}
deps = {}
for f in sorted(Path('docs/pm/backlog').glob('*.md')):
    cur = None
    for line in f.read_text().splitlines():
        m = re.match(r'###\s+(AF-\d{3})\s+', line)
        if m:
            cur = m.group(1)
            ids[cur] = ('SUPERSEDED' in line, f.name)
            continue
        if cur and line.startswith('**Depends on:**'):
            deps[cur] = re.findall(r'AF-\d{3}', line)
sup = {i for i, (s, _) in ids.items() if s}
errs = []
for i, ds in deps.items():
    if i in sup: continue
    for d in ds:
        if d not in ids: errs.append(f'{i} -> {d} MISSING')
        elif d in sup: errs.append(f'{i} -> {d} SUPERSEDED')
if errs:
    print('\n'.join(errs))
    sys.exit(1)
print('registry_check: OK')
PY

test "$(grep '^### AF-' docs/pm/backlog/13-esp32-pcb.md | grep -vc 'SUPERSEDED')" -le 9
grep -c '19 KiCad steps' docs/pm/backlog/README.md   # = 1
git add -A docs/pm/backlog/ docs/pm/refactor-map.md
git commit -m "docs(pm): migrate MR/MA/Cond-X/MF at compressed granularity"
```

---

### Task 9: Reconcile traceability docs

**Files:**
- Modify: `docs/pm/refactor-map.md` (final lineage roll-up), `07-experiment-coverage.md`, `08-adr-coverage.md`, `03-uncertainty-register.md`, `06-critical-path.md`, `04-milestone-graph.md`, `docs/pm/README.md`, `docs/pm/09-jira-import-table.md` (banner only). Do NOT modify `docs/pm/10-jira-import.csv` — CSV has no general-purpose banner/comment mechanism.

- [ ] **Step 1:** `07-experiment-coverage.md`: re-point covering tasks to compressed IDs/locations; adopt the spec's distinction — "covered" (somewhere: task/Do/runbook/experiment) vs "has its own task"; EXP-006 → WF4/M4 cards; ESP32 M4 = EXP-011/012/013-informed integration.
- [ ] **Step 2:** `08-adr-coverage.md`: ADR-016/017 → AF-093..AF-096 flow; ADR-013/014 → compressed adapter/Cond-X cards.
- [ ] **Step 3:** `03-uncertainty-register.md`: every resolver points to a surviving ID.
- [ ] **Step 4:** `06-critical-path.md`: rewrite §6 sweep semantics reference to compressed IDs (two-class model verbatim); sanity test from spec — "Can you glance at the M0→M4 critical path and understand the major progression without reading a wiring procedure?"
- [ ] **Step 5:** `04-milestone-graph.md`, `docs/pm/README.md`: re-point to compressed registry; PM README stops describing 05 as canonical.
- [ ] **Step 6:** Mark stale/deferred explicitly:
  - `docs/pm/09-jira-import-table.md`: replace derivation claims with "**DEFERRED — stale monolith-era derivative.** Regeneration postponed until the canonical Markdown backlog is stable; Jira compatibility must not influence task granularity."
  - `docs/pm/README.md`: explicitly state that both `09-jira-import-table.md` and `10-jira-import.csv` are stale monolith-era derivatives and regeneration is deferred.
  - `docs/pm/10-jira-import.csv`: remain **byte-untouched**.
- [ ] **Step 7:** Exhaustive traceability check. Using the complete set of absorbed IDs from `task-compression-audit.md`, verify that none remain as a **live**:
  - uncertainty resolver in `03-uncertainty-register.md`;
  - experiment-covering task in `07-experiment-coverage.md`;
  - ADR evidence/resolution task in `08-adr-coverage.md`;
  - critical-path node in `06-critical-path.md`;
  - milestone-graph dependency in `04-milestone-graph.md`.
  Historical appearances in `refactor-map.md` and the `Current ID(s)` column of `task-compression-audit.md` are expected and must remain.
- [ ] **Step 8:** Verify + commit.

```bash
python3 - <<'PY'
from pathlib import Path
import re, sys
absorbed = set()
for line in Path('docs/pm/task-compression-audit.md').read_text().splitlines():
    if not line.startswith('|') or 'AF-' not in line: continue
    cell = line.split('|')[1].strip()
    for tok in re.findall(r'AF-\d{3}', cell):
        absorbed.add(tok)
files = {
    'uncertainty': 'docs/pm/03-uncertainty-register.md',
    'experiment': 'docs/pm/07-experiment-coverage.md',
    'adr': 'docs/pm/08-adr-coverage.md',
    'critical': 'docs/pm/06-critical-path.md',
    'milestone': 'docs/pm/04-milestone-graph.md',
}
errs = []
for name, path in files.items():
    text = Path(path).read_text()
    for a in absorbed:
        for m in re.finditer(re.escape(a), text):
            # get line context
            start = text.rfind('\n', 0, m.start()) + 1
            end = text.find('\n', m.end())
            line = text[start:end].strip()
            # allowed if it explicitly mentions lineage/replaced/superseded
            if any(k in line.lower() for k in ('replaced by', 'superseded', 'absorbed', 'lineage', 'historical')):
                continue
            errs.append(f'{path}: {a} in "{line}"')
if errs:
    print('\n'.join(errs))
    sys.exit(1)
print('traceability check: OK')
PY

git add -A docs/pm/
git commit -m "docs(pm): reconcile traceability docs to compressed backlog; defer Jira export"
```

---

### Task 10: Three audits + finalize canonical Markdown

**Files:**
- Modify: `docs/pm/11-coverage-audit.md` (rewrite as three audits)
- Modify: `docs/pm/05-backlog.md` (title + SINGLE-SOURCE blockquote only), `docs/pm/backlog/README.md` (banner removal, canonical declaration), `docs/pm/STOPPING-POINT-2026-08-19-COMPRESSION.md` (new)

- [ ] **Step 1: Rewrite `11-coverage-audit.md`** as three independent audits:
  - **(a) Coverage/safety audit** — every EXP procedure step, ADR prerequisite, uncertainty resolver, purchased component, and safety requirement maps somewhere (live task, `Do`, `Done when`, experiment, runbook, or explicitly deferred item); dependencies valid; fixed-point DAG solvability; conditional semantics consistent.
  - **(b) PM-utility audit** — the spec's per-card questions + anti-bloat checks (no one-conductor card, no evidence-only card, batched per-panel work, procurement only for real external waits).
  - **(c) Novice-executability and goal-path audit** — for every surviving hardware/integration task and for the three complete goal paths, verify the standard (Purpose / Starting state / Exact action / Interfaces / Order / Power state / Pre-power verification / Success state / Failure handling / Hidden knowledge / Next capability unlocked); trace `Content/data path`, `Power path`, and `Prototype progression`; every arrow must name the task/runbook/experiment that explains the transition; no arrow represented only by vague language.
- [ ] **Step 2: Run all three audits; fix findings; record PASS with date.**
- [ ] **Step 3: Supersede 05** — replace title + SINGLE-SOURCE-OF-TRUTH blockquote with the HISTORICAL/SUPERSEDED banner pointing to `docs/pm/backlog/` (task content untouched).
- [ ] **Step 4:** Remove migration-warning banner from `backlog/README.md`; declare phase files canonical; record next available ID; new stopping point (counts per phase, all audits PASS, Jira-deferred note); final grep for authoritative 05 references; final commit.

```bash
! grep -rniE '05-backlog.*(canonical|source of truth)|(canonical|source of truth).*05-backlog' docs/pm/README.md docs/pm/backlog/README.md
grep -n 'SINGLE SOURCE OF TRUTH' docs/pm/05-backlog.md   # 0 matches
git add -A docs/pm/
git commit -m "docs(pm): pass coverage+utility audits; finalize Markdown backlog as canonical"
```

---

## Self-Review

- **Spec coverage:** self-contained plan governs all decisions. Task 1 → granularity rules + novice-executability standard; Task 2 → Gate A audit (M0–M3) with goal-path traceability through M3; Tasks 3–6 → apply M0–M3 compression; Gate B → M4+ proposed survivor map before writing phase files; Tasks 7–8 → migrate M4–MF at compressed granularity; Task 9 → reconcile traceability docs + defer Jira export; Task 10 → three audits (coverage/safety, PM-utility, novice-executability/goal-path) + finalize canonical Markdown.
- **Placeholder scan:** no TBD/TODO; audit rows specify the spec's mandated merges verbatim and include the Gate A/B review columns (Purpose, Starting state, What I actually do, Done state, Next capability unlocked, Execution guidance source, Novice guidance gap); remaining per-task decisions are deliberately the audit's job (Gate A/B review them globally).
- **Consistency:** stable IDs frozen; AF-188 reserved only for genuinely missing project-level outcomes or externally blocked actions discovered by audit/gates; two-class AF-096 semantics carried verbatim; all carry-forward factual corrections embedded; `registry_check()` reusable dependency check ensures no active task depends on a SUPERSEDED ID; live-card counts use `grep -vc SUPERSEDED`; CSV left untouched.
