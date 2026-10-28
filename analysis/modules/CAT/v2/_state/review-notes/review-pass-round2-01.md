# Round-1 findings G1, G2 and G3 confirmed

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T16:48:21+00:00

All three are confirmed against the artifacts.
- **G1.** QR-CAT-047 and QR-CAT-016 order the barcodes by `CREATED_AT` alone, with no filter on type or unit. RULE-CAT-037 counts any active barcode, including a SCALE or CARTON one.
- **G2.** ENT-CAT-007, REQ-CAT-056 and CON-CAT-006 never state the unit of a Bundle component quantity.
- **G3.** The frontend plan's header and ALIGN-FE heading say "55 operations", but its totals and registry say 54 of 56. The amended note of ADR-CAT-051 says 55 operations with 53 used.
