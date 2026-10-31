<!-- source: PHASE:DOC -->
<!-- traces: DBF-INV-001, DBF-INV-040, REQ-INV-009, REQ-INV-039, REQ-INV-057, REQ-INV-075, REQ-INV-129 -->
<!-- PHASE:DOC:START traces=REQ-INV-009,REQ-INV-039,REQ-INV-057,REQ-INV-075,REQ-INV-129,DBF-INV-001,DBF-INV-040 -->
## DOC — API documentation

- **The API document** is `api-spec-inv.yaml` (OpenAPI 3.1.0), derived in this stage from the 52 `API-*` blocks
  and the Error Catalog: one operation per block (`operationId` = the block's operation, `x-api-id` = its id,
  `x-traces` = its traces), the request / response schemas from its Request / Response lines (field types from
  the DBF types via CORE R1), one error response per catalog row naming the block (`x-error-codes`), paging
  `page` / `size` with `x-paginated: true`, bearer JWT on every operation. The five in-process-only codes of §5.1
  sit under the document-level `x-inprocess-error-codes`. The frontend binds to this document and mocks it; the
  test stage cites it.
- **Schema bounds (derivation rule, ADR-INV-038).** A bound that a catalogue code owns is **described, not
  enforced**, in the schema: it is stated in the property's `description` with its code, and carries no
  `minimum`, `exclusiveMinimum`, `maximum`, `minItems` or `maxItems`, so a validating server or mock never
  answers INV-400 before the rule's 422 code. This covers OpeningStockRequest `rows` (1–1000, INV-422-LOAD-ROWS),
  row `rowNo`, `quantity` and `unitCost` (INV-422-LOAD-ROWS-INVALID with the row's own code), TransferLineRequest
  `quantity` (INV-422-QUANTITY-INVALID), StockAdjustmentLineRequest `rowNo` and `unitCost`, StockCountLine
  `countedQty` and `countedMeasureQty` (INV-422-COUNTED-NEGATIVE) and InventorySettings `nearExpiryDays`
  (INV-422-HORIZON-RANGE). Type, format, `maxLength` of a text and the paging bounds of `page` / `size`, which no
  catalogue code owns, stay enforced (INV-400).
- **Packages and manifest.** The code lives under `com.commerce.inv` (CORE); the module manifest
  `META-INF/erp/modules/inv.yaml` declares the api-paths `/api/v1/inv/**` this document covers.
- **The delivered backend** publishes its api-docs with springdoc as the group `inv` (paths `/api/v1/inv/**`);
  each controller method carries `@Operation(operationId = …, summary = <block heading>)`, `@ApiResponse` per
  catalog status, and the `x-api-id` extension, so `api-verify` can hold the published document to
  `api-spec-inv.yaml` operation by operation.
- **In-process surface.** `InventoryApi` carries Javadoc per method naming the contract item it honours
  (CON-INV-006 … 019) and the error codes it can throw; the two events carry their payload description
  (CON-INV-020, 021).
- **Error texts.** The message bundles `messages.properties` (EN) and `messages_ar.properties` (AR) — checklist C11
  — key every code of §5 / §5.1, the four generic codes included, to its texts verbatim (ADR-INV-021); downstream
  documents cite codes, never the text.
<!-- PHASE:DOC:END -->
