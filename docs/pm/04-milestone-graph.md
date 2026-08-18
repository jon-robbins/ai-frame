# 04 — Milestone Dependency Graph

**Last updated:** 2026-08-19
**Purpose:** Big-picture execution order for a novice executor. 6 explicit gated milestones per user adjustment #5. Parallel branches for the two controller candidates before the architecture-decision gate; single winning path after.

---

## Legend

| Symbol | Meaning |
|--------|---------|
| 🔒  **GATED MILESTONE** | Explicit pass/fail gate with enumerated objective DoD. Work does not advance past the gate in the normal critical path until the gate is marked PASSED. |
| 🔀  **PARALLEL DIVERGENCE** | Multiple subtracks may run concurrently. No ordering between them. |
| 🔁  **CONVERGENCE / JOIN** | Multiple branches must produce results before the downstream gate or aggregator task can execute. |
| ┈┈┈  **Dashed line = CONDITIONAL BRANCH** | Executes only if ADR-016 or ADR-017 picks its path. Post-decision, the dashed losing branch is reclassified to `blocked conditional` and removed from critical path. |
| 📋  **DECISION TASK** | Records a decision (ADR). Downstream tasks' "Conditional / Skip condition" fields depend on the recorded outcome. |

---

## 1. Pre-decision view (M0 → M1 → M2 → MG)

Before the architecture decision is made, BOTH controller tracks (WF4 AND ESP32) advance in parallel. Either one suffices to pass the M1 and M2 gates (the gate's DoD says "at least one track passes"). Nano software work is fully independent and may begin BEFORE hardware even ships.

```mermaid
flowchart TD
    START([Start]) --> HW_DELIVERY[📦 Hardware delivery arrives<br/>BLOCKED: DELIVERY until then]
    START --> NANO_BOOTSTRAP[💻 Nano SW Bootstrap<br/>⚠️ INDEPENDENT — no hardware needed<br/>Flash SD, Python, Pillow, framebuffer]

    HW_DELIVERY --> M0[🔒 M0 Hardware Receipt & Safe Power<br/>Mains wiring extra-fine × polarity verify ×<br/>PSU no-load EXP-002 × 1-panel power EXP-003]

    NANO_BOOTSTRAP --> M1_GATE_PREP

    M0 --> WF4_M1[🔀 WF4 Controller M1 Track<br/>Identify, power, 1-panel stock EXP-004,<br/>Huidu EXP-007 A/B/C, Nano end-to-end]
    M0 --> ESP32_M1[🔀 ESP32 Controller M1 Track<br/>Identify/measure EXP-010,<br/>HCT245 12-stage build w/ per-point continuity,<br/>firmware, 1-panel EXP-011,<br/>Nano transport EXP-013, end-to-end]
    M0 --> WF2_M1[🔀 WF2 Reference EXP-008/009<br/>📌 Spike only, not critical path]

    WF4_M1 --> M1_GATE_PREP
    ESP32_M1 --> M1_GATE_PREP

    M1_GATE_PREP --> M1_GATE[🔒 M1 GATE — One Nano-Driven Arbitrary-Text Panel<br/>DoD: WF4 OR ESP32 track passes: arbitrary user-supplied<br/>string → Pillow → transport → correct text visible,<br/>no manual vendor clicks, stable 10 min hold]

    M1_GATE --> WF4_M2[WF4 M2 Track: EXP-005 256×64 2-panel,<br/>seam crossing, ordering]
    M1_GATE --> ESP32_M2[ESP32 M2 Track: EXP-012 256×64 2-panel,<br/>seam crossing, ordering]

    WF4_M2 --> M2_GATE
    ESP32_M2 --> M2_GATE

    M2_GATE[🔒 M2 GATE — Two-Panel 256×64 Logical Canvas<br/>DoD: seam-crossing verified, numbered regions correct,<br/>Standard Defect Checklist on both panels]

    M2_GATE --> EXP014[🧪 EXP-014 Scoring matrix<br/>13 criteria × 2 candidates<br/>every cell filled from measured data]
    M2_GATE --> EXP014b[…plus pre-work for ADR-017:<br/>EXP-007C + EXP-013 results<br/>→ transport candidates ranked]

    EXP014 --> MG[📋 🧠 🔒 MG GATE — Architecture Decision<br/>1. ADR-016: WF4 vs multi-ESP32 = WINNER picked<br/>using "simplest that reliably satisfies V1"<br/>2. ADR-017: transport protocol/interconnect picked<br/>3. Post-decision reclassification SWEEP applied:<br/>  — WINNING controller tasks: critical-path=yes, unconditional<br/>  — LOSING controller M3+ tasks: Conditional=yes, Skip, critical-path=no<br/>  — WF2 M3+: always conditional, reference only]
```

---

## 2. Post-decision view (MG → M3 → M4 → MR → MA → MF)

Single WINNING controller path from MG onward. Note Cond-X (custom ESP32 PCB) only activates if multi-ESP32 wins.

```mermaid
flowchart TD
    MG[MG GATE — WINNER + TRANSPORT SELECTED] --> M3[🔒 M3 GATE — Four-Panel 256×128 EXP-017<br/>2×2 topology<br/>WF4 winner: X1→top-row, X2→bottom-row<br/>ESP32 winner: controller#1→top-row, controller#2→bottom-row<br/>DoD: both horizontal seam AND vertical seam crossed correctly;<br/>both row updates synchronized-enough for dashboard use;<br/>≥ 30 min stable; full EXP-017 procedure executed + logged]

    M3 --> M4[🔒 M4 GATE — Six-Panel 256×192 Final Prototype<br/>2×3 layout<br/>WF4 winner: X1/X2/X3 each → one row<br/>ESP32 winner: 3 controllers each → one row<br/>+ EXP-015 PSU loaded thermal<br/>+ EXP-016 power recovery × 5 cycles<br/>DoD: arbitrary content anywhere on 6 panels;<br/>all seams correct; acceptable refresh rate;<br/>stability pass; recovery pass]

    M4 --> MR[MR — Reliability & Thermal Recovery Completion<br/>Brightness ceiling logged;<br/>24h stability at dashboard-normal content]

    MR --> CONDXPCB[┈┈ 📌 Cond-X: Custom ESP32 PCB<br/>Skip if ADR-016 ≠ multi-ESP32<br/>18 KiCad steps: schematic ERC → layout DRC →<br/>Gerber/Drill/BOM+CPL → manufacture 5 →<br/>receipt continuity → row validation → swap out perfboard]

    MR --> MA[MA — Application Software Completion<br/>All 12 JIRA software stages done;<br/>12 widgets working offline-fallback supervised]

    CONDXPCB --> MF_IF_ESP32[If COND-X ran → verified PCB replaces perfboard before frame closeout]

    MA --> MF_INPUTS[MF — Mounted Frame Inputs ready:<br/>dimensions, thermal ceiling, controller count + PCB layout (if applicable)]
    MF_IF_ESP32 --> MF_INPUTS

    MF_INPUTS --> MF_LAYOUT[Exact layout sketch, depth, ventilation, PE bonding,<br/>cable routing, strain relief, cutouts, panel mounts]

    MF_LAYOUT --> MF_BUILD[Build backplate, mount panels, harnesses, wiring, close frame]

    MF_BUILD --> MF_TEST[72h closed-frame wall-mounted test + Wi-Fi + thermal + brightness]

    MF_TEST --> MF_GATE[🔒 MF GATE — Mounted Frame Optimization Complete<br/>DoD: 18 JIRA MF checklist items satisfied;<br/>wall mounted; closed frame; 72h test pass;<br/>all seams correct; brightness acceptable at night;<br/>Wi-Fi stable; thermal within limits; docs fully updated]

    MF_GATE --> DONE([✅ V1 COMPLETE])
```

---

## 3. Milestone gates — enumerated objective DoD (brief)

Full per-gate pass/fail criteria live in the corresponding GATE-PASS task in the backlog. This is the 1-sentence per-gate reminder.

| Gate | Gate DoD — 1 sentence |
|------|----------------------|
| 🔒 M1 One Nano-Driven Arbitrary-Text Panel | At least ONE controller track passes: user types arbitrary string → Nano Pillow render → transport → correct text visible on 1 physical panel → no manual vendor-software clicks during update → stable 10 minute hold. Other track may fail without blocking the gate. |
| 🔒 M2 Two-Panel 256×64 Logical Canvas | At least ONE controller track passes EXP-005/EXP-012: 2 chained panels = 256×64; horizontal seam crossing verified; left→right panel order verified via numbered regions; Standard Test Pattern Suite + Standard Defect Checklist pass on both panels; ≥ 30 min stable run. |
| 🧠 🔒 MG Architecture Decision | EXP-014 scoring matrix (13 criteria × 2 candidates) fully filled from MEASURED data; ADR-016 WF4-vs-multi-ESP32 DOCUMENTED decision using "simplest that reliably satisfies V1" rule; ADR-017 transport documented; reclassification sweep APPLIED to ALL downstream tasks in backlog (flip losing controller M3+ to Conditional:yes + Skip + critical-path no). |
| 🔒 M3 Four-Panel 256×128 (EXP-017) | WINNING controller only; 2×2 topology; both HORIZONTAL seam (row boundary) AND VERTICAL seam (panel chain boundary) crossed by arbitrary content; both row controllers synchronized-enough (update delta acceptable for dashboard); coordinate grid + corner labels + boundary labels correct; sustained stability ≥ 30 min full display; full EXP-017 procedure + measurements logged. |
| 🔒 M4 Six-Panel 256×192 Final Prototype | 2×3 = 6 panels on WINNING controller architecture (3 rows × 2 panels each row); arbitrary Nano-driven content ANYWHERE on the 256×192 canvas; EVERY seam (2 vertical per row × 3 rows + 2 horizontal seams between rows) correctly crossed by content; acceptable refresh on dashboard duty; EXP-015 4-brightness thermal pass; EXP-016 ×5 power-cycle recovery pass; Wi-Fi recovery pass; API failure simulation + graceful downgrade pass. |
| 🔒 MF Mounted Frame Optimization | All 18 MF items from JIRA.md §Mounted Frame Phase satisfied: exact dims, layout sketch, PSU location & airflow, controller + Nano location, cable routing plan, strain relief all 4 categories, PE bonding all 3 categories, ventilation/fans determined, thermal inside closed-box at brightness ceiling OK, Wi-Fi OK inside closed frame, panels mechanically mounted with alignment, backplate + materials documented, aesthetic finish + wall bracket hardware selected, 72h unattended wall test pass, documentation fully updated. |

---

## 4. Parallelism map — what is safe to run at the same time

Safe parallelism is a **feature**, not an optimization. Running independent work simultaneously compresses wall-clock time AND exposes errors earlier (e.g., Nano software bugs found while waiting for hardware delivery).

| Phase | Concurrent group A | Concurrent group B | Concurrent group C (if applicable) |
|-------|--------------------|--------------------|------------------------------------|
| Before hardware ships | Nano SW bootstrap (flash, Python, Pillow, framebuffer, test patterns, transport stub) | — | — |
| M0 (safe power build) | C14 tab routing verify → crimp practice → L-wire → N-wire → PE-wire → visual/contin (strictly sequential, NO parallel inside mains wiring block) | Panel 2–6 polarity batch verifications (after PSU no-load, independent per-panel) | — |
| M1 | WF4 controller subtrack | ESP32 controller subtrack (including 12-stage HCT perfboard) | WF2 experimental reference subtrack |
| M1 also | Nano SW subtrack runs IN PARALLEL with all hardware — it started before hardware! | | |
| M2 | WF4 M2 2-panel EXP-005/006 | ESP32 M2 2-panel EXP-012 | — |
| MG | EXP-014 matrix drafting | (no parallel — the decision itself depends on both prior tracks' outputs) | — |
| M3/M4/MF | Within WINNING controller single track; parallel within Nano MA software (widgets can be developed concurrently with mechanical frame work) | | |

---

## 5. Post-decision reclassification procedure

This procedure appears VERBATIM inside the backlog's reclassification sweep task. Reproduced here so the graph doc is self-contained.

**When to apply:** Immediately after ADR-016 and ADR-017 are recorded ACCEPTED, BEFORE ANY M3 or later work physically begins.

**Walk EVERY task in the backlog whose Epic is M3, M4, MR, MA, MF, Cond-X.** For each:

1. **If the task's Labels field contains `controller-wf4` AND ADR-016 result = WF4 WIN:**
   - `Status flags - Critical path:` keep `yes` (was `candidate-yes` during M0–M2)
   - `Conditional:` no change (remains `no` or becomes `no`)
   - Skip condition: remove any pre-decision "Skip if other path wins" text
   - `Labels:` remove `candidate-yes`, keep `critical-path yes`, leave rest
2. **If the task's Labels field contains `controller-wf4` AND ADR-016 result = ESP32 WINS (WF4 LOSES):**
   - `Status flags - Critical path:` change from `candidate-yes` → `no`
   - `Conditional:` change → `yes`
   - **Add a Skip condition line to Description:** "Skip — this WF4 controller-specific task does not apply because ADR-016 selected multi-ESP32 architecture."
   - `Labels:` append `blocked conditional`
3. **If the task's Labels field contains `controller-esp32` AND ADR-016 result = multi-ESP32 WIN:**
   - `Status flags - Critical path:` keep `yes`
   - `Conditional:` remains or becomes `no`
   - Skip condition: cleared
   - Labels: remove `candidate-yes`
4. **If the task's Labels field contains `controller-esp32` AND ADR-016 result = WF4 WINS (ESP32 LOSES):**
   - `Status flags - Critical path:` → `no`
   - `Conditional:` → `yes`
   - **Skip condition:** "Skip — this ESP32 controller-specific task does not apply because ADR-016 selected WF4 architecture."
   - `Labels:` append `blocked conditional`
5. **Every Cond-X custom PCB task (Epic AF-010):**
   - If ADR-016 = multi-ESP32 → continue as normal (Conditional MAY flip to `no` if the team wants to commit to PCB now; leaving Conditional is fine and skipping is still OK since perfboard works)
   - If ADR-016 = WF4 → Apply Skip condition "Skip — custom PCB only for multi-ESP32 architecture; not needed for WF4."
6. **Transport implementation task (MA software stage 8):**
   - If ADR-017 = Huidu protocol → code Huidu implementation
   - If ADR-017 = UART → code UART implementation
   - If ADR-017 = USB → code USB implementation
   - If ADR-017 = native-USB → code native-USB implementation
   - If ADR-017 = TCP → code TCP implementation
   - **Do NOT implement more than one transport.** The non-selected transports become out-of-scope for V1.
7. **WF2 downstream M3+ tasks (if any exist):**
   - Always reclassify to conditional. WF2 is reference/fallback only; no third architecture.

**After applying:**
- Walk the critical-path document (06-critical-path.md) and update views B and C to reflect the now-known winning track (not dual-candidate anymore).
- Re-run the 3-way consistency regeneration (09-jira-import-table.md from #06, then 10-jira-import.csv from #09) because Labels and Conditional and Critical-path fields changed on 20+ tasks.
