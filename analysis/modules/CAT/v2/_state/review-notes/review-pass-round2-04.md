# G5 is replaced by G6, which applies the P4 pattern. Updates of inactive records are handled inconsistently. API-CAT-033 and 036 refuse them. API-CAT-012, 019, 023, 027 and 030 say nothing. API-CAT-019 shows the harm: QR-CAT-055 loads a media row whether it is active or not, so a PUT on a removed image with isPrimaryFl true clears the active primary. That breaks RULE-CAT-039. Raised to MAJOR.

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T16:19:59+00:00


