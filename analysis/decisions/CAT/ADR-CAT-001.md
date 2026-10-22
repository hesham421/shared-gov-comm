# ADR-CAT-001 — Module codes and bounded contexts follow one context per module, with the codes in 7.3
Status      : RESOLVED-IN-DIALOGUE
Stage       : domain-profile        Module: CAT        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-22T14:34:22+00:00
Dialogue-key: 828797149abc
traces      : —

## Decision
Accept P-01 option A. The brief's DAG already makes each module its own delivery and boundary unit (G5, G14). I added that module-to-module edges are HARD-FK for mandatory keys and SOFT-READ for crossmodule or event reads.
traces: KB §5, §7; block 7.2, 7.3; G5, G14

Source      : governance-shared\analysis\modules\CAT\_state\briefs\domain-profile-round2.response2.md
