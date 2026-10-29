# ADR-INV-005 — Stock, moves, reservations and lots key on the Variant; a default Variant applies when the variants flag is off
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INV        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-29T10:34:05+00:00
Dialogue-key: 174f4ebb69b2
traces      : —

## Decision
Accept option A. One code path avoids doubling the both-states test burden (G2). It matches DP P-04, so DP P-04 stands as the working assumption. The brief carries no CAT module registry, so nothing conflicts. If CAT's registry decides otherwise, engine STEP C makes the CAT registry win at the P0.5 consistency check.
traces: OI-07

Source      : governance-shared\analysis\modules\INV\_state\briefs\P0-round2.response2.md
