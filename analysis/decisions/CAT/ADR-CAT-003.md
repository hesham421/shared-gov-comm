# ADR-CAT-003 — Seams the erp-core 1.5.0 manifest does not list are platform-own, with a re-match duty
Status      : RESOLVED-IN-DIALOGUE
Stage       : domain-profile        Module: CAT        Version: v1
Lane        : analysis-dialogue · round 2 · claude:sonnet
Decided     : 2026-10-22T14:34:22+00:00
Dialogue-key: 0688223f74c0
traces      : —

## Decision
Accept P-03 option A. The manifest lists no custom attributes, lot, dual-quantity, cash & bank or tax adapter, and assuming them would violate C-GIVEN-1. I added the duty to re-match them as EXISTING (given) when a later manifest provides them.
traces: KB §4.1, §5; C-GIVEN-1, C-GIVEN-3; block 4 rows 1, 3, 7, 8

Source      : governance-shared\analysis\modules\CAT\_state\briefs\domain-profile-round2.response2.md
