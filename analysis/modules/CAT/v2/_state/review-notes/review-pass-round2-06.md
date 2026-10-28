# §3.6 checklist read line by line (C1–C22, F1–F12). No line is violated:

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: CAT        Version: v2
Written: 2026-10-28T16:19:59+00:00

- F3 (CAT_VAR_ATTR) and F9 are covered by CORE-GAP files (ADR-CAT-070, 076, 066, 071, 073).
- The sortOrder `MAX+1` in QR-CAT-024, 029 and 034 is a display order, not a document number, so F6 does not apply.
- The literals in QR-CAT-007, 009, 052 and 053 are closed system lookups that carry behaviour, not user-maintained lists.
