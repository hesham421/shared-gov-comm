# ADR-INV-046 — A line-level refusal names the failing line in `details`
Status      : ACCEPTED
Stage       : gate analysis (revise 2, G2)        Module: INV        Version: v1
Lane        : analysis · claude:opus
Decided     : 2026-10-31
traces      : API-INV-022, API-INV-023, API-INV-024, API-INV-029, API-INV-030, API-INV-032, API-INV-036, API-INV-037, API-INV-038, API-INV-047, ADR-INV-027, ADR-INV-045, RULE-INV-017, RULE-INV-018, RULE-INV-019, RULE-INV-025, RULE-INV-034, RULE-INV-035, RULE-INV-036, RULE-INV-037, RULE-INV-040, RULE-INV-047, RULE-INV-048, REQ-INV-079, REQ-INV-080, REQ-INV-092, REQ-INV-098, REQ-INV-099

## Context
The frontend plan places a line rule's refusal on the offending line, using `details[].rowNo`. The backend Error
Catalog added `details` only to API-INV-047 (ADR-INV-027). A transfer line and a count line have no row number,
and the post-time re-validation (ADR-INV-045) refuses a document by "the first failing line" without naming it.
When one line of many fails a post, the user cannot find it.

## Decision
1. **One shape.** For API-INV-022, 023, 024, 029, 030, 032, 036, 037 and 038, a refusal raised by a rule on one
   line returns the catalogue code and texts of that rule at the top level, and `details` with **one entry for
   the first failing line**: `{lineRef, rowNo?, code, messageAr, messageEn}`. A header refusal returns no
   `details`.
2. **lineRef** identifies the line:
   - a saved line: its identifier — `transferLinePk` (ENT-INV-007), `stockCountLinePk` (ENT-INV-009) or
     `stockAdjustmentLinePk` (ENT-INV-011);
   - an unsaved line of a create or update request: its 1-based position in the request's `lines`;
   - `lineRefKind` tells the two apart (`LINE_PK` or `REQUEST_POSITION`).
3. **rowNo** is added for an adjustment line (its ENT-INV-011.rowNo), so the screen and an opening stock load use
   the same number. API-INV-047 keeps its own form (ADR-INV-027): one entry per failing row, with `rowNo`.
4. The engine stops at the first failing line (all or nothing). Lines are checked in their stored order
   (transfer and count lines by identifier, adjustment lines by rowNo).
5. `ErrorResponse.details[]` in api-spec-inv.yaml gains `lineRef` and `lineRefKind`; `rowNo` stays optional.

## Consequences
- The frontend routes a line code by `details[0].lineRef` (or `rowNo` on adjustments) and opens that line.
- A count line (scan) refused on API-INV-029 carries `REQUEST_POSITION` 1, because the request carries one line.
- One backend and one frontend test case cover a post refused for one line of three.
- No field, RULE or code changes; `details` is additive in the API document.

## Status
ACCEPTED — non-breaking.
