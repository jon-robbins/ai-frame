# Phase 06 — Single-Panel Exit Gate Aggregator (M1)

Tasks are maintained as a compact, dependency-driven M1 phase catalog. Hardware facts must be verified against received evidence and the referenced wiring appendix.

---

### AF-080 — Audit Milestone 1 single-panel exit gate

**Milestone:** M1
**Depends on:** AF-046 OR AF-069
**Labels:** validation decision critical-path
**Context:** Formal sign-off that one candidate controller path, not both, has met every M1 exit condition: AF-046 (WF4) OR AF-069 (ESP32) must pass, while the unused candidate is explicitly recorded as not required or separately assessed.

#### Do
1. Review AF-046 and AF-069 evidence and select the candidate that supplies the gate pass; record the selection and the unused alternative’s status.
2. Check the seven M1 conditions individually: arbitrary runtime text, Nano rendering, controller path, no runtime vendor clicks, 128×64 panel, 10-minute hold, and safety compliance.
3. Record each criterion as pass/fail/unknown with an evidence link and issue the gate result only when one candidate has seven passes.

#### Done when
- AF-046 or AF-069 is explicitly selected as the passing candidate, matching the OR dependency notation.
- All seven criteria have pass evidence from that candidate.
- The gate record identifies the candidate, evidence commits, and any non-selected-track unknowns.

#### If it fails
Do not advance the milestone on a partial candidate. Preserve the failed criterion, return it to AF-046 or AF-069 as appropriate, and rerun the gate audit only after that candidate’s evidence is corrected.
