# ADR-PTY-012 — Addresses and customer groups are stories under POL-PTY-001, and supplier payment terms stay registry scope with no story
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0.5        Module: PTY        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T05:53:58+00:00
Dialogue-key: 305f120b376c
traces      : POL-PTY-001, POL-PTY-003, POL-PTY-006, US-PTY-005, US-PTY-006

## Decision
- Reasoning: I accept option B.
- Addresses are named in KB §5 #4, and the registry makes them SHARED with SLS, PUR and SFR. Customer groups are shared classification per DP §7.1 and decision 8. Both serve the one-customer-base policy.
- Supplier payment terms have no policy. Tracing them to POL-PTY-006 (quick registration) or POL-PTY-003 (customer credit under `b2bCredit`) would misstate those policies, because supplier terms are flag-independent (registry AUTO).
- The TRACES RULE says a need that serves no policy "is scope" and stays in the registry. P1 derives it from the Payment term entity, so no P0 re-opening is needed and nothing is deferred.
- traces: US-PTY-005, US-PTY-006, POL-PTY-001, POL-PTY-006; engine §2 TRACES RULE, §3

Source      : governance-shared\analysis\modules\PTY\_state\briefs\P0.5-round2.response2.md
