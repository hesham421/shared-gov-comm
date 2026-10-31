<!-- source: PHASE:F2 / SUB:F2-SCR-INV-010 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INV-078, AC-INV-079, AC-INV-081, API-INV-001, API-INV-008, API-INV-046, API-INV-047, REQ-INV-013, REQ-INV-017, REQ-INV-018, REQ-INV-064, REQ-INV-070, REQ-INV-071, REQ-INV-072, REQ-INV-073, REQ-INV-074, REQ-INV-105, REQ-INV-106, REQ-INV-107, REQ-INV-122, REQ-INV-140, SCR-INV-010 -->
<!-- SUB:F2-SCR-INV-010:START traces=SCR-INV-010,REQ-INV-140,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-122,AC-INV-078,AC-INV-079,AC-INV-081,API-INV-001,API-INV-008,API-INV-046,API-INV-047 -->
### F2 · SCR-INV-010 — Opening stock

```yaml name=screen-hooks
screen: SCR-INV-010
hooks:
  - {hook: OPENING-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: OPENING-SHEET-QUERY, kind: read, api: [API-INV-046], cache_key: "none — imperative file download", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: OPENING-STOCK-POST, kind: mutation, api: [API-INV-047], errors: "LOAD-ROWS-INVALID → row failures list by rowNo; LOAD-ROWS → message; WAREHOUSE-* → inline warehousePk; IDEMPOTENCY-KEY-REUSED → message + new key", invalidation: "['inv','stock-adjustments'], ['inv','stock-balances'], ['inv','availability'], ['inv','stock-moves'], ['inv','valuation'], ['inv','lookup','lots'], ['inv','lots']"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active:'ACTIVE_ONLY'}]", errors: "generic", loading: LOCAL}
  - {hook: OPENING-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| OPENING-SCREEN-INIT | Permission read: `PERM_INV_OPENING_STOCK_VIEW`, `_CREATE`. Context: warehouse count (selector hidden while 1); `lotExpiryFl` and `dualQuantityFl` decide the lot, expiry and measure columns (AC-INV-080) |
| OPENING-SHEET-QUERY | read of the xlsx file (REQ-INV-073), started by "Download sheet"; it saves the blob under the catalogue file name. Not cached |
| OPENING-STOCK-POST | mutation `{idempotencyKey, warehousePk | null, docDate, rows[]}` — the form's key, the same on every retry of the load, so a double click or a retry after a timeout posts once and answers the first adjustment (ADR-INV-053). INV-409-IDEMPOTENCY-KEY-REUSED → message, text: RULE-INV-050 message (SRS), and the form takes a new key (the key was used by another load); the rows are kept; each row is `{rowNo, barcode, variantPk: null, quantity, unitCost, lotNo, lotExpiryDate, measureQty}`, all in ONE call (ADR-INV-027, ADR-INV-035). INV-422-LOAD-ROWS-INVALID → the row failures list: each `details[]` entry is placed on the row with its `rowNo` and its own code's text. Those codes are INV-422-LOAD-ROW-VARIANT (RULE-INV-028), INV-422-QUANTITY-INVALID (RULE-INV-017), INV-422-NO-KNOWN-COST (RULE-INV-025, a cost with more than 4 decimals), INV-422-OPENING-COST-REQUIRED (RULE-INV-026), INV-422-VARIANT-INACTIVE (RULE-INV-018), INV-422-LOT-EXPIRY-REQUIRED (RULE-INV-035), INV-422-LOT-EXPIRY-MISMATCH (RULE-INV-036), INV-422-MEASURE-REQUIRED (RULE-INV-040), INV-422-DUPLICATE-LINE (RULE-INV-048), and nothing is posted (REQ-INV-072) · INV-422-LOAD-ROWS → message, text: RULE-INV-027 message (SRS) · INV-422-DOC-DATE-FUTURE → inline on docDate, text: RULE-INV-051 message (SRS) · INV-422-WAREHOUSE-REQUIRED → inline on warehousePk, text: RULE-INV-008 message (SRS) · INV-422-WAREHOUSE-INACTIVE → inline, text: RULE-INV-007 message (SRS) · INV-404-WAREHOUSE-NOT-FOUND → catalogue · generic. On success it navigates to SCR-INV-007 `/:id` of the returned adjustment |
| OPENING-FACADE | Owns the form state (rows with `rowNo`), the `line` navigation param, the row failures, the sheet-reading progress and derived loading. Operations: `downloadSheet()`, `loadSheet(file)` (reads the file in the browser, ADR-INV-035, and fills the rows; replacing existing rows asks for confirmation), `addRow`, `replaceRow`, `removeRow` (form state only), `post()` (ONE call) |
<!-- SUB:F2-SCR-INV-010:END -->
