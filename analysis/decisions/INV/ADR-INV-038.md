# ADR-INV-038 — Asserted texts and codes where the SRS, the catalogue and the API schema differ
Status      : ACCEPTED
Stage       : P4 (Test Plan)        Module: INV        Version: v1
Lane        : test-gen · claude:opus
Decided     : 2026-10-31
traces      : AC-INV-078, AC-INV-098, AC-INV-106, AC-INV-111, AC-INV-113, AC-INV-144, AC-INV-147, RULE-INV-027, RULE-INV-033, RULE-INV-034, RULE-INV-044, RULE-INV-046, RULE-INV-047, API-INV-029, API-INV-036, API-INV-038, API-INV-045, API-INV-047

## Context
A test case copies messages from the SRS and asserts the catalogue code. Four places do not line up:
1. **AC text and RULE message differ.**
   - AC-INV-144 quotes «فعِّل مفتاح القدرة «التشغيلة والصلاحية» أولًا.» / "Switch on the lot and expiry
     capability flag first.". RULE-INV-044, the Error Catalog row INV-422-CAPABILITY-OFF and the frontend
     binding carry «فعِّل مفتاح القدرة الخاص بهذا الإعداد أولًا.» / "Switch on the capability flag for this
     setting first.".
   - AC-INV-147 quotes «… 3 أصناف تُتتبَّع بالتشغيلة. ألغِ تتبع التشغيلة لها أولًا.» / "… 3 Items are tracked
     by lot. Clear their lot tracking first.". RULE-INV-046 (the answer the plan states for CON-INV-019) gives
     «لا يمكن إيقاف «{flag}»: {count} أصناف تعتمد عليه. ألغِ الإعداد لها أولًا.» / "{flag} cannot be switched
     off: {count} Items depend on it. Clear the setting on them first.".
2. **Schema bound and RULE code on one field.** api-spec-inv.yaml bounds `OpeningStockRequest.rows` (maxItems
   1000) and `StockCountLineAddRequest.countedQty` (minimum 0). A schema validator could answer INV-400 before
   the service raises INV-422-LOAD-ROWS (RULE-INV-027) or INV-422-COUNTED-NEGATIVE (RULE-INV-047). The plan's
   Validations lines and the operations' `x-error-codes` name the RULE codes.
3. **A Given the API refuses to build.** AC-INV-111 starts from a DRAFT adjustment with a zero-change line. Both
   API-INV-036 and API-INV-037 refuse that line (RULE-INV-034) at save.
4. **A Given the screen cannot reach.** AC-INV-106 scans on a POSTED count. The frontend renders a POSTED count
   read-only, and hides its scan bar.

## Decision
1. TC-INV-045 and TC-INV-046 assert the code and the RULE message. That is the SRS A5 text the server returns
   and the frontend plan binds. Each TC states the AC wording as a recorded divergence. The SRS owner aligns the
   two ACs at the next INV delta.
2. The TCs assert the catalogue code (INV-422-…). Where the AC data allows, a TC satisfies the schema and still
   triggers the rule: TC-INV-033 sends `reasonCode` as an empty string for "no reason".
   **Derivation rule (gate-analysis revise 1, G14 — the conflict is no longer deferred):** a bound that a catalogue
   code owns is **described, not enforced**, in api-spec-inv.yaml — it is written in the property's `description`
   with its code, and carries no `minimum`, `exclusiveMinimum`, `maximum`, `minItems` or `maxItems`. Removed on that
   ground: OpeningStockRequest `rows` minItems / maxItems (INV-422-LOAD-ROWS), row `rowNo` minimum, row `quantity`
   exclusiveMinimum and `unitCost` minimum (INV-422-LOAD-ROWS-INVALID with the row's own code),
   TransferLineRequest `quantity` exclusiveMinimum (INV-422-QUANTITY-INVALID), StockAdjustmentLineRequest `rowNo`
   and `unitCost` minimum, StockCountLineAddRequest / StockCountLineUpdateRequest `countedQty` and
   `countedMeasureQty` minimum (INV-422-COUNTED-NEGATIVE), InventorySettings `nearExpiryDays` minimum / maximum
   (INV-422-HORIZON-RANGE). Type, format, `maxLength` of a text and the paging bounds of `page` / `size`, which no
   catalogue code owns, stay enforced (INV-400). A validating server or mock therefore answers the rule's 422 code,
   as TC-INV-020, TC-INV-027 and the frontend mock expect. The rule is stated in the backend plan's DOC phase.
3. TC-INV-032 asserts the save-time refusal through API-INV-036. It then builds the AC's DRAFT below the API (a
   persisted fixture) and asserts the post-time refusal through API-INV-038.
4. The frontend TC of AC-INV-106 covers the reachable path. The count is open as DRAFT and is posted in another
   session. The scan then gets INV-409-DOCUMENT-CLOSED, routed by F2 (text RULE-INV-029, entry reloads read-only).

## Consequences
- No message is paraphrased. Every asserted text is an SRS text that a plan binds.
- Two SRS ACs carry wording the system will not produce. They are reported, not hidden.
- No REQ, RULE, API, SCR or code changes.

## Status
ACCEPTED — non-breaking.
