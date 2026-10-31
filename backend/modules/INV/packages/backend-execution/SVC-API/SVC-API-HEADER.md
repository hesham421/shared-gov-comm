<!-- source: PHASE:SVC-API — preamble before the first SUB -->
<!-- traces: REQ-INV-139, REQ-INV-140, REQ-INV-003, REQ-INV-009, REQ-INV-022, REQ-INV-028, REQ-INV-039, REQ-INV-040, REQ-INV-043, REQ-INV-051, REQ-INV-052, REQ-INV-063, REQ-INV-070, REQ-INV-075, REQ-INV-078, REQ-INV-080, REQ-INV-087, REQ-INV-092, REQ-INV-097, REQ-INV-099, REQ-INV-108, REQ-INV-109, REQ-INV-124, REQ-INV-129, REQ-INV-138, DBF-INV-001, DBF-INV-014, DBF-INV-027, DBF-INV-040, DBF-INV-087, DBF-INV-115, DBF-INV-143, DBF-INV-176, DBF-INV-192, DBF-INV-205 -->
## SVC-API — services and endpoints

52 endpoints (≥ 8) — grouped CRUD (writes), SEARCH (reads), INT (the in-process surface other modules call).
Every endpoint: base path `/api/v1/inv/{resource}`; success wrapped in `ApiResponse<T>`; lists in `Page<T>`;
errors through `LocalizedException → {code, messageAr, messageEn}`; the permission checked before any
processing — `@PreAuthorize("hasAuthority(T(com.commerce.inv.permission.InvPermissions).PERM_…)")` on the
controller method **and** on every public method of the REST-facing service it calls, INV's own constants only,
never a role name (checklist C13, ADR-INV-042).
Services (REST-facing, package `service`): `WarehouseService`, `BranchService`, `StockQueryService`,
`TransferService`, `StockCountService`, `StockAdjustmentService`, `OpeningStockService`, `LotService`,
`ItemStockSettingsService`, `ValuationService`, `InventorySettingsService`, `InventoryContextService`.
Engines (internal, package `engine`, no permission check, no REST path — ADR-INV-042): `StockPostingEngine`
(the posting engine below), `AvailabilityEngine` (availability and bundle derivation), `ReservationEngine`
(reserve, release, consume), `InventoryReadEngine` (the reads `InventoryApi` serves). A REST service reaches an
engine only after its own `@PreAuthorize` has passed.
