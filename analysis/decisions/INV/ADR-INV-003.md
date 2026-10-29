# ADR-INV-003 — Capability flags and presets stay given cu settings behind one FeatureFlags facade, with no new module code
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: e5672323a4f6
traces      : —

## Decision
Accept option B. The profile rule says flags and presets are core settings read through FeatureFlags. `cu` already provides platform-defaults and tenant-overrides, which together are a preset plus overrides. A new code such as SET would rebuild `cu` (G6) and is not in the profile. Hosting in CAT (option C) would force PTY → CAT and INV → CAT-for-configuration edges.

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md
