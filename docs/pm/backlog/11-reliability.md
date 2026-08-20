# Phase 11 — Reliability & Burn-In Testing (MR)

Covers the evidence-backed dashboard brightness ceiling and one representative 24-hour finished-dashboard reliability run.

### AF-120 — Calibrate operating brightness ceiling

**Milestone:** MR
**Depends on:** AF-119, AF-133
**Labels:** validation thermal power critical-path
**Safety:** 5V-HIGH-CURRENT
**Stop condition:** Stop output and de-energize if AF-115 evidence shows voltage instability, resets, blanking, smoke, smell, or abnormal connector, conductor, PSU, or controller heating. Do not invent a thermal limit that the measurements do not support.

#### Do
1. Review AF-115's four-level loaded observations and AF-117's physical recovery evidence using representative dashboard duty, ambient conditions, and the selected controller architecture.
2. Select and record a dashboard operating brightness ceiling supported by that evidence. Record the worst observed physical recovery times and every limitation or required follow-up in `docs/pm/evidence/AF-120-operating-ceiling.md`.
3. Keep the result evidence-backed: distinguish measured observations from decisions and do not claim an unsupported universal thermal or timing threshold.

#### Done when
- The dashboard brightness ceiling, selected architecture, evidence links, and operating limitations are recorded.
- The record identifies the worst observed physical recovery times without treating them as unvalidated limits.

#### If it fails
Keep the operating setting at the last evidenced-safe level, preserve the observations, and return to the named M4 power, thermal, controller, or recovery cause before setting a ceiling.

### AF-121 — Complete 24-hour dashboard-normal burn-in

**Milestone:** MR
**Depends on:** AF-120, AF-133
**Conditional dependency:** AF-169 when Cond-X is active.
**Labels:** validation stability thermal recovery-test critical-path
**Safety:** 5V-HIGH-CURRENT, HUB75
**Stop condition:** Stop output and de-energize before physical inspection if the display blanks, freezes, garbles, resets, smells, smokes, or shows abnormal PSU, controller, cable, connector, or panel heating. Do not change HUB75 or panel-power connections while energized.

#### Do
1. Run normal mixed dashboard content at AF-120's recorded ceiling for at least 24 hours after AF-133 passes. Capture start, approximately eight-hour, and end evidence, including display state, process/service state, SSH reachability, and thermal observations.
2. When Cond-X is active, use the AF-169 final PCB in the representative rig for this run. Do not treat perfboard-only evidence as final-PCB burn-in evidence.
3. Save the timing, content/configuration, interruptions, recovery attempts, and observations in `docs/pm/evidence/AF-121-24h-burn-in.md`.

#### Done when
- A >=24-hour record shows no unresolved display, process, SSH, or thermal failure under normal dashboard duty.
- When Cond-X is active, the record identifies the AF-169 final PCB in the representative rig.

#### If it fails
Preserve logs, screenshots, and observations; isolate the dashboard, service, transport, controller, power, thermal, or final-PCB cause before restarting a new full-duration run.
