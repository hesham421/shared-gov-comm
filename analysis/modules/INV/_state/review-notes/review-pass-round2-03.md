# G19 is added as MAJOR. The FOR UPDATE on `IS_DEFAULT_FL = TRUE` returns no row to the second of two concurrent default switches, which contradicts TC-INV-248. The fix is a per-tenant advisory lock, the same pattern as ADR-INV-044.

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T12:23:59+00:00


