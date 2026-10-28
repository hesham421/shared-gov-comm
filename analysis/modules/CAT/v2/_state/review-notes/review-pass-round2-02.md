# §3.6 checklist re-read, no MAJOR violation

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T16:48:21+00:00

I read every line of the checklist (C1–C22, F1–F12) against the analysis.
- The C5 / F3 deviation for Variant attributes is recorded in ADR-CAT-070 and raised as a CORE-GAP, as F9 requires. It is not a violation.
- The `MAX(sortOrder)+1` reads (QR-CAT-024, 029, 034) produce a display order, not a document number, so F6 does not apply.
- The scale label layouts and Unit e-invoice codes are keyed reference rows with their own rules (ADR-CAT-042), not a settings table, so F5 and C14 do not apply.
- C21 and C22 are not applicable (no home cards, no GL posting).
