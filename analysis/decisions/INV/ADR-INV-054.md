# ADR-INV-054 — A count posts the measure difference of a dual-quantity Item; the system measure is derived
Status      : ACCEPTED
Stage       : gate analysis (revise 4, G8)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : REQ-INV-092, AC-INV-159, REQ-INV-123, RULE-INV-034, RULE-INV-040, API-INV-027, API-INV-032, DBF-INV-028, DBF-INV-131, DBF-INV-132, DBF-INV-163, ADR-INV-017, SRS D-15

## Context
Posting a count recorded systemQty and differenceQty for the piece count only, and created an adjustment line only
for a non-zero differenceQty; the generated line's measureQtyChange was "(dual)" with no formula. A dual-quantity
line whose pieces matched on-hand but whose counted measure differed produced no line, so the measure was never
corrected; when a line was created, its measure change was undefined. The fix also asks that the system measure be
shown beside systemQty after posting — derived, or a new column (SRS D-15).

Options for showing the system measure: (a) two new columns on INV_STOCK_COUNT_LINE (system measure, measure
difference) — two DBF ids, a db-script delta and a manifest change for a value that is already recoverable;
(b) derive it on read from what is stored.

## Decision
1. **Measure difference.** While dualQuantity is on, for a count line of a dual-quantity Item, posting computes
   measure difference = countedMeasureQty (DBF-INV-131) − COALESCE(ON_HAND_MEASURE_QTY (DBF-INV-028), 0) of the same
   balance row (Variant, warehouse, lot), read under the same balance lock as systemQty (engine step 1).
2. **Line creation.** One COUNT_DIFFERENCE adjustment line is created for each count line whose differenceQty ≠ 0
   **or** whose measure difference ≠ 0, with quantityChange = differenceQty and measureQtyChange (DBF-INV-163) = the
   measure difference (null when it is 0). A line with differenceQty 0 and a non-zero measure difference is a
   measure-only move (ADR-INV-017: quantity 0, measure |difference|, value 0); RULE-INV-034 holds because its measure
   change is non-zero. The adjustment-line part of RULE-INV-040 is not applied to a generated line: it carries its
   count line's measure difference, and the count line already carried the counted measure.
3. **System measure — option (b), derived.** It is not stored. A read of a posted count (API-INV-027, API-INV-032)
   returns systemMeasureQty = countedMeasureQty − the measureQtyChange of the resulting adjustment's line for that
   Variant and lot, or countedMeasureQty when the count line produced no adjustment line (both quantities matched).
   It is null for an Item that is not dual-quantity and while the count is DRAFT. No DBF is added.

## Consequences
- SRS: REQ-INV-092 states the measure rule; new AC-INV-159; D-15 names the derivation; RULE-INV-040 notes the
  generated line.
- Backend plan: API-INV-032 orchestration and validations, API-INV-027 response; §3 "Derived, not held".
- api-spec-inv.yaml: `systemMeasureQty` (read-only, nullable) on StockCountLine.
- Test: TC-INV-290 counts 3 pieces at 118.000 m against 3 pieces at 120.000 m and asserts one measure-only
  ADJUSTMENT_OUT of 2.000 m with on-hand staying 3.
- If the adjustment of a posted count were ever removed the derivation would fail; adjustments are never deleted
  and posted documents are read-only (RULE-INV-029), so the derivation is stable.

## Status
ACCEPTED — non-breaking (no consumer is built yet).
