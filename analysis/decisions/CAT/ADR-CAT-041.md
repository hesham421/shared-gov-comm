# ADR-CAT-041 — Capability flags read through settings; turn-off check offered to the flag owner

Status : ACCEPTED (non-breaking) · Stage : P1 · Module : CAT v2 · Date : 2026-10-26

## Context
The following are still OPEN:
- who owns the FeatureFlags API, the activity presets and the setup wizard (P-02, OI-05)
- how a flag is persisted: through given `cu` SettingsApi (DP §4 #15)

US-CAT-003 requires CAT behaviour to follow the flags. US-CAT-006 requires turning `variants` or `bundles` off to be blocked while catalogue data depends on it (G3). CAT cannot block a change in a module it does not own, and the flag owner cannot read CAT data unless CAT offers it.

## Decision
- CAT reads `variants` and `bundles` through com.erp.cu.crossmodule.SettingsApi, behind the FeatureFlags API name.
- When the value is unreadable, CAT refuses the flag-bound operation (RULE-CAT-009).
- CAT offers a turn-off check operation (REQ-CAT-021 … REQ-CAT-023). It returns allowed / count / dependent Items, and the flag owner calls it before persisting a turn-off.
- The catalogue settings screen shows the same check to the owner.

## Consequences
- The flag owner's SRS (once P-02 assigns a code) must call the CAT turn-off check.
- The P1.5 contract stage defines the check as a CAT cross-module operation.
- No activity name or preset is read by CAT (G1).

## Traces
US-CAT-003, US-CAT-004, US-CAT-006 · ENT-CAT-001, ENT-CAT-002, ENT-CAT-005 · REQ-CAT-011, REQ-CAT-013, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023 · RULE-CAT-008, RULE-CAT-009, RULE-CAT-010, RULE-CAT-011
