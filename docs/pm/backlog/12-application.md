# Phase 12 — Application Software Pipeline (MA)

Covers the production Nano runtime, controller-agnostic rendering path, selected transport, recovery behavior, and an unattended useful dashboard.

### AF-122 — Productionize Nano runtime baseline

**Milestone:** MA
**Depends on:** AF-119
**Labels:** software nano security recovery-test critical-path

#### Do
1. Apply the supported headless OS baseline, network and SSH hardening, and reproducible Python environment for the Nano; record versions, service account, configuration location, and restore method.
2. Document a rollback path that restores the last known working runtime without silently overwriting dashboard configuration or evidence.
3. Save the boot-to-runtime baseline and service prerequisites in `docs/pm/evidence/AF-122-nano-runtime-baseline.md`.

#### Done when
- A documented secure boot-to-runtime baseline can be reproduced on the Nano.
- The runtime, service prerequisites, configuration, and rollback method are explicit.

#### If it fails
Restore the recorded working baseline, preserve the failure output, and correct the OS, network, SSH, package, or service prerequisite before application work continues.

### AF-123 — SUPERSEDED

Replaced by: AF-122

(Do not export to Jira)

### AF-124 — SUPERSEDED

Replaced by: AF-122

(Do not export to Jira)

### AF-125 — Production renderer and reusable pattern library

**Milestone:** MA
**Depends on:** AF-122
**Labels:** software renderer validation critical-path

#### Do
1. Implement or verify multiline text, alignment, font handling, and the standard reusable pattern library without embedding final-canvas dimensions as renderer constants.
2. Add documented dimension, clipping, alignment, and sentinel-pixel output checks for representative text and patterns.
3. Save API usage, test artifacts, and output-check results in `docs/pm/evidence/AF-125-renderer-patterns.md`.

#### Done when
- The renderer and pattern API produce documented outputs for multiline text, alignment, fonts, and standard patterns.
- Output checks verify dimensions and sentinel behavior.

#### If it fails
Preserve failing artifacts and correct font availability, coordinate, clipping, or RGB output behavior before framebuffer work continues.

### AF-126 — SUPERSEDED

Replaced by: AF-125

(Do not export to Jira)

### AF-127 — Canonical framebuffer and transport contract

**Milestone:** MA
**Depends on:** AF-125
**Labels:** software framebuffer transport validation critical-path

#### Do
1. Define and test canonical framebuffer creation, pixel/region access, row-region extraction, RGB serialization, transport status, error reporting, and reconnect contract behavior.
2. Verify dimensions, edge crops, byte lengths, and expected failure behavior without coupling the renderer to a specific controller.
3. Save the contract, test commands, and results in `docs/pm/evidence/AF-127-framebuffer-transport-contract.md`.

#### Done when
- Tests prove framebuffer dimensions, crop edges, serialization bytes, status, and failure behavior.
- The renderer remains controller-agnostic and a concrete transport can implement the documented contract.

#### If it fails
Keep the selected controller topology unchanged; correct the contract or tests before implementing a production transport.

### AF-128 — SUPERSEDED

Replaced by: AF-127

(Do not export to Jira)

### AF-129 — Implement selected controller transport

**Milestone:** MA
**Depends on:** AF-096, AF-095, AF-127
**Labels:** software transport validation critical-path

#### Do
1. Read ADR-016 and ADR-017, then implement only the selected controller and transport path using the proven M1/M3 evidence and AF-127 contract.
2. Configure the selected path explicitly, expose send, failure, and reconnect state, and document deselected transports as intentionally skipped rather than partially supported.
3. Save production-frame, configuration, failure, and reconnect evidence in `docs/pm/evidence/AF-129-selected-transport.md`.

#### Done when
- The selected transport sends production frames and exposes failure/reconnect state through the contract.
- The evidence identifies the accepted ADR-016/ADR-017 path and explicitly excludes deselected transports.

#### If it fails
Preserve transport logs and return to the selected-path configuration, controller, or contract fault; do not substitute a deselected transport without a new ADR decision.

### AF-130 — Validate production six-panel application-to-display integration

**Milestone:** MA
**Depends on:** AF-113, AF-129
**Labels:** validation software transport critical-path
**Safety:** HUB75, 5V-HIGH-CURRENT
**Stop condition:** Stop output and de-energize before physical inspection if blanking, garbling, resets, smoke, smell, or abnormal heating occurs. Do not hot-plug HUB75 or panel power.

#### Do
1. Send arbitrary multiline and aligned production content through AF-129 to the selected six-panel path.
2. Send 500 sequential frames, record send/display errors and recovery observations, and centralize 256x192 configuration outside renderer constants.
3. Save source content, transport logs, display observations, and dimension ownership in `docs/pm/evidence/AF-130-production-e2e.md`.

#### Done when
- The physical six-panel run has no unresolved send or display error across the recorded sequence.
- Dimension ownership is explicit and the renderer does not hard-code the final canvas.

#### If it fails
Preserve the failing frame and logs, then isolate renderer, framebuffer, selected transport, controller, or physical display behavior before repeating the affected sequence.

### AF-131 — SUPERSEDED

Replaced by: AF-130

(Do not export to Jira)

### AF-132 — Add application recovery supervision

**Milestone:** MA
**Depends on:** AF-129, AF-130
**Labels:** software recovery-test observability critical-path

#### Do
1. Add structured logs, reconnect/backoff behavior, controller-loss handling, and configurable watchdog escalation around the selected transport and dashboard process.
2. Exercise an expected controller or transport loss and recovery without hiding the cause; record the service state transitions and operator-visible evidence.
3. Save configuration and exercised recovery evidence in `docs/pm/evidence/AF-132-application-supervision.md`.

#### Done when
- Failure, backoff, reconnect, controller-loss, and watchdog behavior are demonstrated with structured logs.
- Recovery configuration is explicit and does not claim API/cache behavior owned by AF-133.

#### If it fails
Keep the service observable, preserve logs, and correct the process, transport, controller, or watchdog behavior before dashboard startup is accepted.

### AF-133 — Deliver dashboard widgets, caching, offline fallback, and service startup

**Milestone:** MA
**Depends on:** AF-130, AF-132
**Labels:** software dashboard recovery-test validation critical-path

#### Do
1. Implement useful time/date, data/artwork, and dashboard widgets with durable cache storage and a visible stale-data state.
2. Configure unattended service startup and verify reboot-to-dashboard behavior.
3. Exercise API failure while preserving cached/stale display behavior, then restore the API and verify live-data resume. Save evidence in `docs/pm/evidence/AF-133-dashboard-resilience.md`.

#### Done when
- The dashboard starts unattended and displays useful content after reboot.
- API loss displays cached/stale data, and restored service resumes live data with evidence.

#### If it fails
Preserve logs and cached artifacts, then correct widget, cache, startup, API, or recovery behavior before MR burn-in begins.
