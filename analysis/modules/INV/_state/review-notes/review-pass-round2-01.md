# G1 stays MAJOR, with a cheaper fix

Lane   : review-pass · round 2 · claude:opus
Stage  : P4        Module: INV        Version: v1
Written: 2026-10-31T14:15:23+00:00

I checked it against the artifacts. API-INV-032 says "lotNo and lotExpiryDate stay empty" on the lines a count generates. The constraint UQ_INV_STOCK_ADJUSTMENT_LINE_ADJ_VAR_LOT is UNIQUE NULLS NOT DISTINCT over (TENANT_ID, STOCK_ADJUSTMENT_FK, VARIANT_FK, LOT_NO). So two counted lots of one Variant (L1, L2), or a line with no lot plus a lot line, collide on (adj, V, NULL). Multi-lot counts are central when lotExpiry is on, so they cannot be posted. That is MAJOR. The fix needs no schema change: write lotNo = the counted lot's code (DBF-INV-181) on each generated line. A line with no lot keeps lotNo NULL, and UQ_INV_STOCK_COUNT_LINE_CNT_VAR_LOT already allows only one such line per Variant per count. Engine step 3 resolves count-generated lines by lotFk, so RULE-INV-035 and RULE-INV-036 are still skipped. The round-1 fix (widening the constraint through a P2 change) is set aside, and no ADR is needed.
