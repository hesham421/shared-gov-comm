# New G8 (MAJOR). Posting a count ignores a measure difference. ENT-INV-009 captures `countedMeasureQty`, but REQ-INV-092 and API-INV-032 compute only `differenceQty`. They create lines only for "non-zero differences", and give `measureQtyChange` "(dual)" no formula. If a dual-quantity line has the same piece count but a different measure, no correction is made.

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T13:49:07+00:00


