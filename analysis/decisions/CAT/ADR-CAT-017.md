# ADR-CAT-017 — Capability flags and activity presets live in the given `cu`, behind a `FeatureFlags` library facade; no new module is created
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CAT        Version: v2
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-26T09:46:00+00:00
Dialogue-key: 868cbb2e9151
traces      : POL-CAT-002, POL-CAT-003, POL-CAT-004, POL-CAT-005

## Decision
traces: OI-05, POL-CAT-002, POL-CAT-003, POL-CAT-004, POL-CAT-005
Accept B and tighten it, because round 1 left two candidates ownerless:
- Flag values are the given `AppConfiguration` and get no entity ID. Activity presets are seeded settings defined by the flag × activity matrix in the platform summary. CAND-OPEN-003 and CAND-OPEN-004 are therefore closed as "given, no entity ID". No business module owns them.
- The facade is a shared-kernel library, not a module, and it owns no ID. It reaches `cu` only through `SettingsApi`, so G5 holds.
- Each module owns the flag-off guard for its own flags.
- The five-step setup wizard is a front-end shell with no domain entity. Each step calls its owning module: company is `tenant`, activity preset is `cu`, tax and e-invoice is TAX, payment methods is STL, item import is CAT.
- Option A would duplicate `cu` and needs a prefix the profile doesn't have. Option C would force PTY → CAT and break PTY's root status.
Sources: KB header, §3 D2, §4.1, §6.2, §6.9; manifest `cu`, `tenant`; G5, G6.

Source      : governance-shared\analysis\modules\CAT\v2\_state\briefs\P0-round2.response2.md
