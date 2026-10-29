# ADR-PTY-003 — Capability flags and activity presets are given `cu` settings read through FeatureFlags; no new module is created
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:49:42+00:00
Dialogue-key: 120bbb305dfa
traces      : POL-PTY-005

## Decision
Accepted (option B). The profile rule states that activity presets and capability flags are core settings read through FeatureFlags. `cu` provides platform-defaults and tenant-overrides. A new `SET` module would need a profile change and would own no entity.
Amendments:
- The setup wizard is a composition of steps, each belonging to the module whose data it sets. It is not a module. If wizard progress needs persistence, it is a `cu` setting. P0.5 writes the stories.
- A flag that cannot be turned off while data depends on it needs a guard owned by the module holding that data. PTY states its guard in POL-PTY-005. How guards hook into `cu` is a P2/P3 design matter and is not decided here.
traces: OI-05, CAND-OPEN-003, CAND-OPEN-004

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0-round2.response2.md
