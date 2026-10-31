# Frontend execution plan — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Stage : P3.2 Part B (frontend execution plan)   Profile : commerce
Track  : frontend · plan exec · stack react-ts-vite (react-router · tanstack-query · react-hook-form · zod · zustand)
Bound  : api-spec-inv.yaml (OpenAPI 3.1.0, 51 operations API-INV-001 … 051) — the document a mock server serves
Design : ui-ux-spec-inv.md (SCR-INV-001 … 012, UXD-INV-001 … 011) · flow-diagram-inv.md
ADRs   : ADR-INV-033 (reads outside the document) · ADR-INV-034 (count commits per line) · ADR-INV-035 (browser reads
         the opening stock sheet — barcode only in v1) · ADR-INV-036 (balance labels for a Seller/Cashier — the
         warehouse label is on the row, closed at gate-analysis revise 1)
══════════════════════════════════════════════════════════════════

## 3.0 Binding to the API document

The plan cites operations by their `x-api-id`. Method, path, parameters, request and response schemas, paging
(`x-paginated`, `page` / `size`), the `ApiResponse<T>` envelope and the error responses (`x-error-codes`) are read
in `api-spec-inv.yaml`. They are not restated here. All operations sit under the module base path
`/api/v1/inv` and use bearer authentication.

```yaml name=api-surface
mock: api-spec-inv.yaml
bindings:
  - {req: REQ-INV-003, api: [API-INV-003]}
  - {req: REQ-INV-004, api: [API-INV-004]}
  - {req: REQ-INV-005, api: [API-INV-005]}
  - {req: REQ-INV-006, api: [API-INV-005]}
  - {req: REQ-INV-007, api: [API-INV-006]}
  - {req: REQ-INV-008, api: [API-INV-007]}
  - {req: REQ-INV-009, api: [API-INV-001, API-INV-002]}
  - {req: REQ-INV-010, api: [API-INV-008]}
  - {req: REQ-INV-011, api: [API-INV-008]}
  - {req: REQ-INV-012, api: [API-INV-008]}
  - {req: REQ-INV-013, api: [API-INV-028, API-INV-036, API-INV-047]}
  - {req: REQ-INV-014, api: [API-INV-022, API-INV-024]}
  - {req: REQ-INV-015, api: [API-INV-008]}
  - {req: REQ-INV-016, api: [API-INV-018]}
  - {req: REQ-INV-017, api: [API-INV-028, API-INV-036, API-INV-047]}
  - {req: REQ-INV-018, api: [API-INV-028, API-INV-036, API-INV-047]}
  - {req: REQ-INV-022, api: [API-INV-011]}
  - {req: REQ-INV-023, api: [API-INV-012]}
  - {req: REQ-INV-024, api: [API-INV-013]}
  - {req: REQ-INV-025, api: [API-INV-013]}
  - {req: REQ-INV-026, api: [API-INV-014]}
  - {req: REQ-INV-027, api: [API-INV-015]}
  - {req: REQ-INV-028, api: [API-INV-009, API-INV-010]}
  - {req: REQ-INV-029, api: [API-INV-008]}
  - {req: REQ-INV-030, api: [API-INV-008, API-INV-001]}
  - {req: REQ-INV-031, api: [API-INV-003]}
  - {req: REQ-INV-032, api: [API-INV-008]}
  - {req: REQ-INV-035, api: [API-INV-016]}
  - {req: REQ-INV-038, api: [API-INV-016, API-INV-017]}
  - {req: REQ-INV-039, api: [API-INV-016]}
  - {req: REQ-INV-040, api: [API-INV-017]}
  - {req: REQ-INV-041, api: [API-INV-016, API-INV-017]}
  - {req: REQ-INV-042, api: [API-INV-016, API-INV-017]}
  - {req: REQ-INV-055, api: [API-INV-019]}
  - {req: REQ-INV-056, api: [API-INV-018, API-INV-019]}
  - {req: REQ-INV-059, api: [API-INV-050]}
  - {req: REQ-INV-061, api: [API-INV-050]}
  - {req: REQ-INV-062, api: [API-INV-024]}
  - {req: REQ-INV-063, api: [API-INV-050, API-INV-051]}
  - {req: REQ-INV-064, api: [API-INV-038, API-INV-047]}
  - {req: REQ-INV-065, api: [API-INV-049]}
  - {req: REQ-INV-066, api: [API-INV-048]}
  - {req: REQ-INV-067, api: [API-INV-038, API-INV-024]}
  - {req: REQ-INV-070, api: [API-INV-047]}
  - {req: REQ-INV-071, api: [API-INV-047]}
  - {req: REQ-INV-072, api: [API-INV-047]}
  - {req: REQ-INV-073, api: [API-INV-046]}
  - {req: REQ-INV-074, api: [API-INV-047]}
  - {req: REQ-INV-075, api: [API-INV-018]}
  - {req: REQ-INV-076, api: [API-INV-018]}
  - {req: REQ-INV-078, api: [API-INV-022]}
  - {req: REQ-INV-079, api: [API-INV-023]}
  - {req: REQ-INV-080, api: [API-INV-024]}
  - {req: REQ-INV-081, api: [API-INV-025]}
  - {req: REQ-INV-082, api: [API-INV-022, API-INV-023]}
  - {req: REQ-INV-083, api: [API-INV-024]}
  - {req: REQ-INV-084, api: [API-INV-022, API-INV-023, API-INV-024]}
  - {req: REQ-INV-085, api: [API-INV-023, API-INV-025]}
  - {req: REQ-INV-086, api: [API-INV-020, API-INV-021]}
  - {req: REQ-INV-087, api: [API-INV-028]}
  - {req: REQ-INV-088, api: [API-INV-029]}
  - {req: REQ-INV-089, api: [API-INV-030, API-INV-031]}
  - {req: REQ-INV-090, api: [API-INV-027, API-INV-028, API-INV-029, API-INV-032]}
  - {req: REQ-INV-091, api: [API-INV-029]}
  - {req: REQ-INV-092, api: [API-INV-032]}
  - {req: REQ-INV-093, api: [API-INV-032]}
  - {req: REQ-INV-094, api: [API-INV-033]}
  - {req: REQ-INV-095, api: [API-INV-026, API-INV-027]}
  - {req: REQ-INV-096, api: [API-INV-029, API-INV-030, API-INV-031, API-INV-033]}
  - {req: REQ-INV-097, api: [API-INV-036]}
  - {req: REQ-INV-098, api: [API-INV-037]}
  - {req: REQ-INV-099, api: [API-INV-038]}
  - {req: REQ-INV-100, api: [API-INV-039]}
  - {req: REQ-INV-101, api: [API-INV-036]}
  - {req: REQ-INV-102, api: [API-INV-038]}
  - {req: REQ-INV-103, api: [API-INV-037, API-INV-039]}
  - {req: REQ-INV-104, api: [API-INV-034, API-INV-035]}
  - {req: REQ-INV-105, api: [API-INV-038, API-INV-047]}
  - {req: REQ-INV-106, api: [API-INV-038, API-INV-047]}
  - {req: REQ-INV-107, api: [API-INV-038, API-INV-047]}
  - {req: REQ-INV-108, api: [API-INV-042]}
  - {req: REQ-INV-109, api: [API-INV-040, API-INV-041]}
  - {req: REQ-INV-113, api: [API-INV-016, API-INV-017]}
  - {req: REQ-INV-114, api: [API-INV-038]}
  - {req: REQ-INV-115, api: [API-INV-016, API-INV-040]}
  - {req: REQ-INV-116, api: [API-INV-040]}
  - {req: REQ-INV-117, api: [API-INV-040]}
  - {req: REQ-INV-118, api: [API-INV-048, API-INV-049]}
  - {req: REQ-INV-119, api: [API-INV-049]}
  - {req: REQ-INV-121, api: [API-INV-016, API-INV-017]}
  - {req: REQ-INV-122, api: [API-INV-022, API-INV-023, API-INV-029, API-INV-047]}
  - {req: REQ-INV-123, api: [API-INV-038]}
  - {req: REQ-INV-124, api: [API-INV-045]}
  - {req: REQ-INV-125, api: [API-INV-045]}
  - {req: REQ-INV-126, api: [API-INV-045]}
  - {req: REQ-INV-127, api: [API-INV-045]}
  - {req: REQ-INV-128, api: [API-INV-045]}
  - {req: REQ-INV-129, api: [API-INV-043, API-INV-044]}
  - {req: REQ-INV-132, api: [API-INV-008]}
  - {req: REQ-INV-134, api: [API-INV-008]}
  - {req: REQ-INV-135, api: [API-INV-008]}
  - {req: REQ-INV-136, api: [API-INV-008]}
  - {req: REQ-INV-137, api: [API-INV-008]}
unmapped: []
codes:
  - {code: "INV-422-WAREHOUSE-CODE-READONLY", rule: RULE-INV-001}
  - {code: "INV-409-WAREHOUSE-NAME-TAKEN", rule: RULE-INV-002}
  - {code: "INV-409-WAREHOUSE-DEFAULT", rule: RULE-INV-003}
  - {code: "INV-409-WAREHOUSE-IN-USE", rule: RULE-INV-004}
  - {code: "INV-409-WAREHOUSE-DEFAULT-INACTIVE", rule: RULE-INV-006}
  - {code: "INV-422-WAREHOUSE-INACTIVE", rule: RULE-INV-007}
  - {code: "INV-422-WAREHOUSE-REQUIRED", rule: RULE-INV-008}
  - {code: "INV-422-BRANCH-INACTIVE", rule: RULE-INV-009}
  - {code: "INV-422-BRANCH-CODE-READONLY", rule: RULE-INV-010}
  - {code: "INV-409-BRANCH-NAME-TAKEN", rule: RULE-INV-011}
  - {code: "INV-409-BRANCH-DEFAULT", rule: RULE-INV-012}
  - {code: "INV-409-BRANCH-IN-USE", rule: RULE-INV-013}
  - {code: "INV-409-BRANCH-DEFAULT-INACTIVE", rule: RULE-INV-015}
  - {code: "INV-422-TRANSFER-WAREHOUSES", rule: RULE-INV-016}
  - {code: "INV-422-QUANTITY-INVALID", rule: RULE-INV-017}
  - {code: "INV-422-VARIANT-INACTIVE", rule: RULE-INV-018}
  - {code: "INV-409-NEGATIVE-STOCK", rule: RULE-INV-019}
  - {code: "INV-422-BUNDLE-NO-COMPONENTS", rule: RULE-INV-022}
  - {code: "INV-422-NO-KNOWN-COST", rule: RULE-INV-025}
  - {code: "INV-422-OPENING-COST-REQUIRED", rule: RULE-INV-026}
  - {code: "INV-422-LOAD-ROWS", rule: RULE-INV-027}
  - {code: "INV-422-LOAD-ROW-VARIANT", rule: RULE-INV-028}
  - {code: "INV-409-DOCUMENT-CLOSED", rule: RULE-INV-029}
  - {code: "INV-422-DOC-NO-READONLY", rule: RULE-INV-030}
  - {code: "INV-422-NO-LINES", rule: RULE-INV-031}
  - {code: "INV-422-BARCODE-UNKNOWN", rule: RULE-INV-032}
  - {code: "INV-422-REASON-REQUIRED", rule: RULE-INV-033}
  - {code: "INV-422-ZERO-CHANGE", rule: RULE-INV-034}
  - {code: "INV-422-LOT-EXPIRY-REQUIRED", rule: RULE-INV-035}
  - {code: "INV-422-LOT-EXPIRY-MISMATCH", rule: RULE-INV-036}
  - {code: "INV-422-LOT-REQUIRED", rule: RULE-INV-037}
  - {code: "INV-422-MEASURE-REQUIRED", rule: RULE-INV-040}
  - {code: "INV-422-MEASURE-UNIT-REQUIRED", rule: RULE-INV-041}
  - {code: "INV-409-LOT-TRACKING-IN-USE", rule: RULE-INV-042}
  - {code: "INV-409-DUAL-QUANTITY-IN-USE", rule: RULE-INV-043}
  - {code: "INV-422-CAPABILITY-OFF", rule: RULE-INV-044}
  - {code: "INV-422-BUNDLE-SETTINGS", rule: RULE-INV-045}
  - {code: "INV-422-COUNTED-NEGATIVE", rule: RULE-INV-047}
  - {code: "INV-422-DUPLICATE-LINE", rule: RULE-INV-048}
```

**Reconciliation against the SRS (once).**
- Every SRS B5 row of SCR-REQ-INV-001 … 012 has its operation in the document. The document adds API-INV-008
  (inventory context), which the SRS B5 of SCR-REQ-INV-001 lists. All 51 operations are bound to at least one
  screen action (operations coverage table, ALIGN-FE). No operation maps to no REQ.
- **Codes with no RULE.** These codes are routed with the catalogue text, by reference:
  - INV-400, INV-401, INV-403, INV-500, the `INV-404-*` not-found codes and INV-409-STALE-VERSION (ADR-INV-021,
    ADR-INV-032).
  - INV-422-HORIZON-RANGE: DEFAULT D-04; the text is that of AC-INV-133.
  - INV-422-LOAD-ROWS-INVALID: the wrapper of row failures; each `details[]` row carries its own code and text.
- **Reads that are not operations of the document** (ADR-INV-033):
  - CAT reads, as UXD-INV-001 … 011;
  - INV_* lookup values, from the given `mdl`;
  - the session permissions, from the given `sec`.
  None of them is a `read` hook.
- **Values absent from the document:** none. The warehouse label of balance rows and of availability rows is
  carried by the rows themselves (`warehouseCode`, `warehouseNameAr`, `warehouseNameEn` — ADR-INV-036).

**Shared conventions for all phases** (each SUB below assumes them):
- **Envelope.** Responses are unwrapped from `ApiResponse<T>.data` in one HTTP client. Errors are parsed as
  `ErrorResponse {code, messageAr, messageEn, details[]}`. Each catalogue text is shown in the user's language,
  from the response.
- **Generic error routing**, used wherever a SUB says "generic":
  - INV-400 → generic validation message;
  - INV-401 → login;
  - INV-403 → unauthorized view or message, with the catalogue text «ليست لديك صلاحية لهذا الإجراء.» /
    "You do not have permission for this action." (AC-INV-154);
  - INV-500 and network failures → generic retry message;
  - INV-409-STALE-VERSION → «reload» prompt with the catalogue text, which re-reads the record and keeps the
    user's unsaved input where it still applies;
  - `INV-404-*` → the catalogue text, and the drawer or route returns to the search.
- **Message binding.** In the F2 blocks, "text: RULE-INV-nnn" means the SRS A5 message of that RULE, in ar and en.
  It is the same text the server returns.
- **Filter state.** Filter objects carry `page` and `size` inside them. The filter object is the query key, and
  the URL search params mirror it, so a deep link restores the search.

<!-- PHASE:F1:START traces=SCR-INV-001,SCR-INV-002,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008,SCR-INV-009,SCR-INV-010,SCR-INV-011,SCR-INV-012,UXD-INV-001,UXD-INV-002,UXD-INV-003,UXD-INV-004,UXD-INV-005,UXD-INV-006,UXD-INV-007,UXD-INV-008,UXD-INV-009,UXD-INV-010,UXD-INV-011,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-010,REQ-INV-011,REQ-INV-012,REQ-INV-013,REQ-INV-014,REQ-INV-015,REQ-INV-016,REQ-INV-017,REQ-INV-018,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-029,REQ-INV-030,REQ-INV-031,REQ-INV-032,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-055,REQ-INV-056,REQ-INV-059,REQ-INV-061,REQ-INV-062,REQ-INV-063,REQ-INV-064,REQ-INV-065,REQ-INV-066,REQ-INV-067,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-075,REQ-INV-076,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-108,REQ-INV-109,REQ-INV-113,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-118,REQ-INV-119,REQ-INV-121,REQ-INV-122,REQ-INV-123,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,REQ-INV-137,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015,API-INV-016,API-INV-017,API-INV-018,API-INV-019,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-042,API-INV-043,API-INV-044,API-INV-045,API-INV-046,API-INV-047,API-INV-048,API-INV-049,API-INV-050,API-INV-051 -->
## F1 — Models & Types

**RF1.** The field and DTO binding is `api-spec-inv.yaml`. TypeScript types are generated from
`components.schemas` of the document. They are not hand-written, and none is restated here. Each SUB names the
schemas its screen uses and the client-only types it adds: filter objects, form state and option shapes.
Shared client types live in the module's `types` folder:
- `Envelope<T>` and `Page<T>` from `ApiResponse` / `PageMeta`;
- `InvError` from `ErrorResponse`;
- `LookupOption {code, labelAr, labelEn}` for INV_* lookup keys and the CAT unit-of-measure key;
- `CatVariantRef {variantPk, itemPk, code, nameAr, nameEn}` and `CatItemRef {itemPk, code, nameAr, nameEn,
  itemKindCode}`, the shapes the CAT reads return to INV screens (UXD, ADR-INV-033);
- `InventoryContext` from the schema of the same name.
Quantities and measures are decimal strings in form state, with 3 places. Costs have 4 places and values have 2.
They are converted to numbers only at the request boundary, so rounding never happens in the browser
(DEFAULT D-06).

<!-- SUB:F1-SCR-INV-001:START traces=SCR-INV-001,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-015,REQ-INV-029,REQ-INV-030,REQ-INV-031,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009 -->
### F1 · SCR-INV-001 — Warehouses
- Schemas: `Warehouse`, `WarehouseCreateRequest`, `WarehouseUpdateRequest`, `WarehouseEnvelope`, `WarehousePageEnvelope`, `VersionedActionRequest`, `Branch` (labels), `InventoryContext`.
- `WarehouseFilter {code?, name?, branchPk?, active: 'ACTIVE_ONLY' | 'ALL', page, size}`. The default is `{active: 'ACTIVE_ONLY', page: 0, size: 20}`.
- `WarehouseFormState {nameAr, nameEn, branchPk | null, version?}`. `branchPk` is null for a new warehouse while the branch field is hidden. The server assigns the default branch (REQ-INV-031).
- `WarehouseEntryNav = 'new' | warehousePk`, which comes from the `entry` search param.
<!-- SUB:F1-SCR-INV-001:END -->

<!-- SUB:F1-SCR-INV-002:START traces=SCR-INV-002,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-032,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015 -->
### F1 · SCR-INV-002 — Branches
- Schemas: `Branch`, `BranchCreateRequest`, `BranchUpdateRequest`, `BranchEnvelope`, `BranchPageEnvelope`, `VersionedActionRequest`, `InventoryContext`.
- `BranchFilter {code?, name?, active, page, size}`.
- `BranchFormState {nameAr, nameEn, version?}`.
- `BranchEntryNav = 'new' | branchPk`.
<!-- SUB:F1-SCR-INV-002:END -->

<!-- SUB:F1-SCR-INV-003:START traces=SCR-INV-003,UXD-INV-001,UXD-INV-002,UXD-INV-003,REQ-INV-012,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-113,REQ-INV-115,REQ-INV-121,REQ-INV-135,REQ-INV-137,API-INV-001,API-INV-008,API-INV-016,API-INV-017,API-INV-040 -->
### F1 · SCR-INV-003 — Stock balances
- Schemas: `StockBalanceRow`, `StockBalanceRowPageEnvelope`, `Availability`, `AvailabilityFigures`, `AvailabilityEnvelope`, `Warehouse` (labels), `LotRow` (lot options), `InventoryContext`.
- `BalanceFilter {variantPk?, term?, itemPk?, warehousePk?, lotPk?, nonZero, page, size}`. `term` holds a scanned barcode. A Variant chosen through UXD-INV-001 sets `variantPk`, and an Item chosen through UXD-INV-002 sets `itemPk`.
- `BalanceRowView`: the row plus `itemName` (UXD-INV-002), `warehouseLabel` (from the row's `warehouseCode` / `warehouseNameAr` / `warehouseNameEn`, for every role — ADR-INV-036) and `measureUnitLabel` (UXD-INV-003). `AvailabilityRowView` labels each per-warehouse row the same way.
<!-- SUB:F1-SCR-INV-003:END -->

<!-- SUB:F1-SCR-INV-004:START traces=SCR-INV-004,UXD-INV-004,REQ-INV-016,REQ-INV-055,REQ-INV-056,REQ-INV-075,REQ-INV-076,API-INV-001,API-INV-008,API-INV-018,API-INV-019,API-INV-040 -->
### F1 · SCR-INV-004 — Stock move history
- Schemas: `StockMove`, `StockMoveEnvelope`, `StockMovePageEnvelope`, `Warehouse` and `LotRow` (labels), `InventoryContext`.
- `MoveFilter {variantPk?, warehousePk?, lotPk?, moveTypeCode?, docNo?, sourceDocTypeCode?, sourceDocRef?, dateFrom?, dateTo?, page, size}`. `sourceDocTypeCode` + `sourceDocRef` identify one source document exactly; they are set only by the jumps from SCR-INV-005 and SCR-INV-007 and shown as a removable "document" chip, not as editable fields.
- Derived `showRunningBalance = variantPk != null && warehousePk != null` (REQ-INV-076). When one warehouse is active, `warehousePk` is the default warehouse taken from the context.
- `MoveDetailNav = stockMovePk`, which comes from the `move` search param.
<!-- SUB:F1-SCR-INV-004:END -->

<!-- SUB:F1-SCR-INV-005:START traces=SCR-INV-005,UXD-INV-005,REQ-INV-011,REQ-INV-014,REQ-INV-062,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-122,API-INV-001,API-INV-008,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-040,API-INV-044 -->
### F1 · SCR-INV-005 — Transfers
- Schemas: `Transfer`, `TransferLine`, `TransferLineRequest`, `TransferCreateRequest`, `TransferUpdateRequest`, `TransferEnvelope`, `TransferPageEnvelope`, `VersionedActionRequest`, `LotRow`, `ItemStockSettings`.
- `TransferFilter {docNo?, dateFrom?, dateTo?, fromWarehousePk?, toWarehousePk?, statusCode?, page, size}`.
- `TransferFormState {docDate, fromWarehousePk, toWarehousePk, noteText, lines: TransferLineDraft[], version?}`.
- `TransferLineDraft {key, transferLinePk?, variant: CatVariantRef, lotPk?, quantity, measureQty?}`. `key` is a client row key for the line drawer's navigation param.
- `LineDrawerNav = 'new' | key`, which comes from the `line` search param.
- `ItemTracking {lotTrackedFl, dualQuantityFl, measureUomCode}` is read per line Item through API-INV-044.
<!-- SUB:F1-SCR-INV-005:END -->

<!-- SUB:F1-SCR-INV-006:START traces=SCR-INV-006,UXD-INV-006,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-122,API-INV-001,API-INV-008,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-040,API-INV-044 -->
### F1 · SCR-INV-006 — Stock counts
- Schemas: `StockCount`, `StockCountLine`, `StockCountCreateRequest`, `StockCountLineAddRequest`, `StockCountLineUpdateRequest`, `StockCountEnvelope`, `StockCountLineEnvelope`, `StockCountPageEnvelope`, `StockCountPostResultEnvelope`, `VersionedActionRequest`, `LotRow`, `ItemStockSettings`.
- `CountFilter {docNo?, dateFrom?, dateTo?, warehousePk?, statusCode?, page, size}`.
- `CountStartState {warehousePk | null, noteText}`.
- `ScanState {barcode?, variant?: CatVariantRef, countedQty, lotPk?, countedMeasureQty?}`. `countedQty` defaults to `1`.
- `CountLineEditState {countedQty, countedMeasureQty?, version}` and `CountLineNav = stockCountLinePk`.
<!-- SUB:F1-SCR-INV-006:END -->

<!-- SUB:F1-SCR-INV-007:START traces=SCR-INV-007,UXD-INV-007,REQ-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-067,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-114,REQ-INV-123,API-INV-001,API-INV-008,API-INV-016,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-044 -->
### F1 · SCR-INV-007 — Stock adjustments
- Schemas: `StockAdjustment`, `StockAdjustmentLine`, `StockAdjustmentLineRequest`, `StockAdjustmentCreateRequest`, `StockAdjustmentUpdateRequest`, `StockAdjustmentEnvelope`, `StockAdjustmentPageEnvelope`, `VersionedActionRequest`, `Lot`, `LotRow`, `StockBalanceRow` (write-off seed), `ItemStockSettings`.
- `AdjustmentFilter {docNo?, dateFrom?, dateTo?, warehousePk?, reasonCode?, statusCode?, page, size}`.
- `AdjustmentFormState {warehousePk | null, docDate, reasonCode, noteText, lines: AdjustmentLineDraft[], version?}`. The update request carries no `warehousePk`, so the warehouse is fixed after the first save.
- `AdjustmentLineDraft {key, rowNo, variant: CatVariantRef, quantityChange, unitCost?, lotNo?, lotExpiryDate?, measureQtyChange?}`.
- `WriteOffSeed {reason: 'EXPIRY_WRITE_OFF', lotPk, warehousePk}`, which comes from the search params of `/new`.
<!-- SUB:F1-SCR-INV-007:END -->

<!-- SUB:F1-SCR-INV-008:START traces=SCR-INV-008,UXD-INV-008,REQ-INV-108,REQ-INV-109,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-134,API-INV-001,API-INV-008,API-INV-040,API-INV-041,API-INV-042 -->
### F1 · SCR-INV-008 — Lots and near expiry
- Schemas: `LotRow`, `LotRowPageEnvelope`, `Lot`, `LotEnvelope`, `LotUpdateRequest`, `Warehouse` (labels).
- `LotFilter {variantPk?, code?, warehousePk?, expiryFrom?, expiryTo?, nearExpiryOnly, page, size}`.
- `LotFormState {expiryDate, nameAr, nameEn, version}` and `LotEntryNav = lotPk`.
<!-- SUB:F1-SCR-INV-008:END -->

<!-- SUB:F1-SCR-INV-009:START traces=SCR-INV-009,UXD-INV-009,UXD-INV-010,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,API-INV-008,API-INV-043,API-INV-044,API-INV-045 -->
### F1 · SCR-INV-009 — Item stock settings
- Schemas: `ItemStockSettings`, `ItemStockSettingsRequest`, `ItemStockSettingsEnvelope`, `ItemStockSettingsPageEnvelope`.
- `ItemSettingsFilter {itemPk?, term?, lotTracked?, dualQuantity?, page, size}`.
- `ItemSettingsFormState {item: CatItemRef, lotTrackedFl, dualQuantityFl, measureUomCode | null, version | null}`. `version` is null when the Item has no record yet (ADR-INV-029).
- `ItemSettingsEntryNav = 'new' | itemPk`.
<!-- SUB:F1-SCR-INV-009:END -->

<!-- SUB:F1-SCR-INV-010:START traces=SCR-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-122,API-INV-001,API-INV-008,API-INV-046,API-INV-047 -->
### F1 · SCR-INV-010 — Opening stock
- Schemas: `OpeningStockRequest`, `StockAdjustmentEnvelope`, `ErrorResponse.details` (row failures).
- `OpeningStockFormState {warehousePk | null, docDate?, rows: OpeningRowDraft[]}`.
- `OpeningRowDraft {key, rowNo, barcode, quantity, unitCost, lotNo?, lotExpiryDate?, measureQty?}`. The request sends `barcode` as entered and leaves `variantPk` null; v1 names a Variant by barcode only (ADR-INV-035).
- `RowFailure {rowNo, code, messageAr, messageEn}`.
- `SheetColumnMap`: the header text (ar | en) for each field, used to read the sheet.
<!-- SUB:F1-SCR-INV-010:END -->

<!-- SUB:F1-SCR-INV-011:START traces=SCR-INV-011,REQ-INV-065,REQ-INV-066,REQ-INV-118,REQ-INV-119,API-INV-008,API-INV-048,API-INV-049 -->
### F1 · SCR-INV-011 — Inventory settings
- Schemas: `InventorySettings`, `InventorySettingsRequest`, `InventorySettingsEnvelope`.
- `SettingsFormState {allowNegativeStockFl, nearExpiryDays}`. `nearExpiryDays` is held as text and validated as a whole number.
<!-- SUB:F1-SCR-INV-011:END -->

<!-- SUB:F1-SCR-INV-012:START traces=SCR-INV-012,UXD-INV-011,REQ-INV-059,REQ-INV-061,REQ-INV-063,API-INV-008,API-INV-050,API-INV-051 -->
### F1 · SCR-INV-012 — Stock valuation
- Schemas: `StockValuation`, `StockValuationEnvelope`, `ValuationPageEnvelope` (with `grandTotalValue`).
- `ValuationFilter {variantPk?, term?, itemPk?, nonZero, page, size}`.
- `ValuationDetailNav = variantPk`, which comes from the `variant` search param.
<!-- SUB:F1-SCR-INV-012:END -->
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=SCR-INV-001,SCR-INV-002,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008,SCR-INV-009,SCR-INV-010,SCR-INV-011,SCR-INV-012,UXD-INV-001,UXD-INV-002,UXD-INV-003,UXD-INV-004,UXD-INV-005,UXD-INV-006,UXD-INV-007,UXD-INV-008,UXD-INV-009,UXD-INV-010,UXD-INV-011,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-010,REQ-INV-011,REQ-INV-012,REQ-INV-013,REQ-INV-014,REQ-INV-015,REQ-INV-016,REQ-INV-017,REQ-INV-018,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-029,REQ-INV-030,REQ-INV-031,REQ-INV-032,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-055,REQ-INV-056,REQ-INV-059,REQ-INV-061,REQ-INV-062,REQ-INV-063,REQ-INV-064,REQ-INV-065,REQ-INV-066,REQ-INV-067,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-075,REQ-INV-076,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-108,REQ-INV-109,REQ-INV-113,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-118,REQ-INV-119,REQ-INV-121,REQ-INV-122,REQ-INV-123,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,REQ-INV-137,AC-INV-004,AC-INV-007,AC-INV-008,AC-INV-011,AC-INV-026,AC-INV-029,AC-INV-030,AC-INV-043,AC-INV-044,AC-INV-045,AC-INV-047,AC-INV-069,AC-INV-078,AC-INV-079,AC-INV-081,AC-INV-082,AC-INV-083,AC-INV-088,AC-INV-090,AC-INV-091,AC-INV-093,AC-INV-094,AC-INV-097,AC-INV-098,AC-INV-101,AC-INV-103,AC-INV-106,AC-INV-109,AC-INV-111,AC-INV-113,AC-INV-115,AC-INV-121,AC-INV-122,AC-INV-128,AC-INV-129,AC-INV-130,AC-INV-132,AC-INV-133,AC-INV-139,AC-INV-141,AC-INV-142,AC-INV-143,AC-INV-144,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015,API-INV-016,API-INV-017,API-INV-018,API-INV-019,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-042,API-INV-043,API-INV-044,API-INV-045,API-INV-046,API-INV-047,API-INV-048,API-INV-049,API-INV-050,API-INV-051 -->
## F2 — Data Hooks

**RF2.** Each SUB declares what its screen needs from the API: one `screen-hooks` block, then a table that
explains each row. Server state lives in `tanstack-query`. Components call the screen's FACADE only. The facade
calls only the hooks declared for its screen. The default cache policy applies (staleTime 30 s, gcTime 5 min)
unless a row says otherwise. Loading is `LOCAL` everywhere. The SRS names no slow call, so no hook uses `GLOBAL`.

**Shared hooks.** Each key below has ONE hook, and every screen that uses the key shares it.

| Hook | Kind | Source | Cache key | Policy · notes |
|---|---|---|---|---|
| INV-CONTEXT | init | API-INV-008 | `['inv','context']` | staleTime 5 min; refetch on window focus. Every warehouse or branch create, deactivate or reactivate invalidates it, so a second warehouse or branch reveals its controls with no reload (REQ-INV-015, REQ-INV-032). Flags come from FeatureFlags through the server, never from the activity (REQ-INV-132) |
| SESSION-PERMISSIONS | init | authenticated session (given `sec`, ADR-INV-033) | — (session store) | the set of `PERM_INV_*` the user holds |
| WAREHOUSES-LOOKUP | lookup | API-INV-001 | `['inv','lookup','warehouses',{active,page:0,size:100}]` | staleTime 10 min. Selectors use `ACTIVE_ONLY`; labels use `ALL`, because history can show an inactive warehouse. An id not on the page is labelled through API-INV-002. Requires `PERM_INV_WAREHOUSES_VIEW`; without it the lookup does not run and no warehouse filter is offered — balance and availability rows carry their own label (ADR-INV-036). Options are `{warehousePk, code, nameAr, nameEn, isDefaultFl}` |
| BRANCHES-LOOKUP | lookup | API-INV-009 | `['inv','lookup','branches',{active,page:0,size:100}]` | staleTime 10 min. Selectors use `ACTIVE_ONLY`; labels use `ALL` |
| LOTS-LOOKUP | lookup | API-INV-040 | `['inv','lookup','lots',{variantPk,warehousePk,page:0,size:100}]` | scoped by its parent, the Variant (and the warehouse when one is chosen). Enabled only when `variantPk` is set, `lotExpiry` is on and the user holds `PERM_INV_LOTS_VIEW`. Options are `{lotPk, code, expiryDate, expiredFl, onHandQty}` |
| ITEM-TRACKING-LOOKUP | lookup | API-INV-044 | `['inv','item-settings',itemPk]` | `lotTrackedFl` / `dualQuantityFl` / `measureUomCode` of a line's Item, so the line knows whether to ask for a lot or a measure. An Item with no record answers false / false / empty (REQ-INV-129). Shared with the SCR-INV-009 entry |
| LOOKUP-INV_ADJUSTMENT_REASON, LOOKUP-INV_MOVE_TYPE, LOOKUP-INV_DOC_STATUS, LOOKUP-INV_SOURCE_DOC_TYPE | lookup | given `mdl` (ADR-INV-033) | `['mdl',<key>]` | session-long cache. Options are `LookupOption {code, labelAr, labelEn}`; open keys show the tenant's added values |
| CAT-VARIANT-LOOKUP | lookup | CAT (UXD-INV-001, 004, 005, 006, 007, 008, 011) | `['cat','variant-search',term]` · `['cat','barcode',barcode]` · `['cat','variant',variantPk]` | search by code or name (CON-CAT-016), barcode → Variant (CON-CAT-010), read Variant with its `itemPk` (CON-CAT-008). staleTime 5 min. An unknown barcode answers "not found" with no throw |
| CAT-ITEM-LOOKUP | lookup | CAT (UXD-INV-002, 009, 011) | `['cat','item-search',term]` · `['cat','item',itemPk]` | search (CON-CAT-016) and read Item (CON-CAT-007), with `itemKindCode` |
| CAT-UOM-LOOKUP | lookup | CAT unit-of-measure lookup (UXD-INV-003, 010) | `['cat','uom']` | session-long cache; labels `measureUomCode` in ar / en |

<!-- SUB:F2-SCR-INV-001:START traces=SCR-INV-001,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-015,REQ-INV-029,REQ-INV-030,REQ-INV-031,AC-INV-004,AC-INV-007,AC-INV-008,AC-INV-011,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009 -->
### F2 · SCR-INV-001 — Warehouses

```yaml name=screen-hooks
screen: SCR-INV-001
hooks:
  - {hook: WAREHOUSES-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSES-QUERY, kind: read, api: [API-INV-001], cache_key: "['inv','warehouses',filter{code,name,branchPk,active,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSE-QUERY, kind: read, api: [API-INV-002], cache_key: "['inv','warehouse',warehousePk]", errors: "INV-404-WAREHOUSE-NOT-FOUND → message, drawer closes", loading: LOCAL, invalidation: "—"}
  - {hook: BRANCHES-LOOKUP, kind: lookup, api: [API-INV-009], cache_key: "['inv','lookup','branches',{active}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSE-CREATE, kind: mutation, api: [API-INV-003], errors: "NAME-TAKEN → inline nameAr; BRANCH-INACTIVE / BRANCH-NOT-FOUND → inline branchPk", invalidation: "['inv','warehouses'], ['inv','lookup','warehouses'], ['inv','context']"}
  - {hook: WAREHOUSE-UPDATE, kind: mutation, api: [API-INV-004], errors: "NAME-TAKEN → inline nameAr; BRANCH-INACTIVE → inline branchPk; STALE-VERSION → reload", invalidation: "['inv','warehouses'], ['inv','warehouse',id], ['inv','lookup','warehouses']"}
  - {hook: WAREHOUSE-DEACTIVATE, kind: mutation, api: [API-INV-005], errors: "WAREHOUSE-DEFAULT / WAREHOUSE-IN-USE → message", invalidation: "['inv','warehouses'], ['inv','warehouse',id], ['inv','lookup','warehouses'], ['inv','context']"}
  - {hook: WAREHOUSE-REACTIVATE, kind: mutation, api: [API-INV-006], errors: "BRANCH-INACTIVE → message; STALE-VERSION → reload", invalidation: "['inv','warehouses'], ['inv','warehouse',id], ['inv','lookup','warehouses'], ['inv','context']"}
  - {hook: WAREHOUSE-SET-DEFAULT, kind: mutation, api: [API-INV-007], errors: "WAREHOUSE-DEFAULT-INACTIVE → message; STALE-VERSION → reload", invalidation: "['inv','warehouses'], ['inv','lookup','warehouses'], ['inv','context']"}
  - {hook: WAREHOUSES-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| WAREHOUSES-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE / DELETE of `INV_WAREHOUSES`. Context: `activeBranchCount`; while it is 1, the branch filter, column and field are hidden (REQ-INV-029, REQ-INV-030). Lookups: BRANCHES-LOOKUP when more than one branch is active. Entity by id: WAREHOUSE-QUERY when `entry=<id>` |
| WAREHOUSES-QUERY | read. Ordered by code on the server (REQ-INV-009). Key = the full filter |
| WAREHOUSE-QUERY | read, enabled only while the entry drawer is open on an id |
| WAREHOUSE-CREATE | mutation; body `{nameAr, nameEn, branchPk | null}`. INV-409-WAREHOUSE-NAME-TAKEN → inline on nameAr, text: RULE-INV-002 · INV-422-BRANCH-INACTIVE → inline on branchPk, text: RULE-INV-009 · INV-404-BRANCH-NOT-FOUND → inline on branchPk, text: catalogue · generic. On success the drawer closes and the search refetches |
| WAREHOUSE-UPDATE | mutation; body `{nameAr, nameEn, branchPk, version}`. `code` is never sent, and a hidden branch sends the record's `branchPk`. INV-409-WAREHOUSE-NAME-TAKEN → inline on nameAr, text: RULE-INV-002 · INV-422-BRANCH-INACTIVE → inline on branchPk, text: RULE-INV-009 · INV-422-WAREHOUSE-CODE-READONLY → message, text: RULE-INV-001 · INV-409-STALE-VERSION → reload · generic |
| WAREHOUSE-DEACTIVATE | mutation (DELETE, no body), after a confirmation. INV-409-WAREHOUSE-DEFAULT → message, text: RULE-INV-003, with the next action "Set as default" on another row · INV-409-WAREHOUSE-IN-USE → message, text: RULE-INV-004, with the next action "View balances", which opens SCR-INV-003 seeded with `warehousePk` |
| WAREHOUSE-REACTIVATE | mutation `{version}`. INV-422-BRANCH-INACTIVE → message, text: RULE-INV-009 · INV-409-STALE-VERSION → reload |
| WAREHOUSE-SET-DEFAULT | mutation `{version}` of the target. INV-409-WAREHOUSE-DEFAULT-INACTIVE → message, text: RULE-INV-006 · INV-409-STALE-VERSION → reload |
| WAREHOUSES-FACADE | Owns the list from query data, the filter `{code,name,branchPk,active,page,size}` mirrored to the URL, the entry navigation `entry`, the form's dirty state and derived loading. Operations: `save()` (create or update), `deactivate(id)`, `reactivate(id, version)`, `setDefault(id, version)`, `openEntry(id | 'new')`, `closeEntry()` |
<!-- SUB:F2-SCR-INV-001:END -->

<!-- SUB:F2-SCR-INV-002:START traces=SCR-INV-002,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-032,AC-INV-026,AC-INV-029,AC-INV-030,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015 -->
### F2 · SCR-INV-002 — Branches

```yaml name=screen-hooks
screen: SCR-INV-002
hooks:
  - {hook: BRANCHES-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: BRANCHES-QUERY, kind: read, api: [API-INV-009], cache_key: "['inv','branches',filter{code,name,active,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: BRANCH-QUERY, kind: read, api: [API-INV-010], cache_key: "['inv','branch',branchPk]", errors: "INV-404-BRANCH-NOT-FOUND → message, drawer closes", loading: LOCAL, invalidation: "—"}
  - {hook: BRANCH-CREATE, kind: mutation, api: [API-INV-011], errors: "BRANCH-NAME-TAKEN → inline nameAr", invalidation: "['inv','branches'], ['inv','lookup','branches'], ['inv','context']"}
  - {hook: BRANCH-UPDATE, kind: mutation, api: [API-INV-012], errors: "BRANCH-NAME-TAKEN → inline nameAr; STALE-VERSION → reload", invalidation: "['inv','branches'], ['inv','branch',id], ['inv','lookup','branches']"}
  - {hook: BRANCH-DEACTIVATE, kind: mutation, api: [API-INV-013], errors: "BRANCH-DEFAULT / BRANCH-IN-USE → message", invalidation: "['inv','branches'], ['inv','branch',id], ['inv','lookup','branches'], ['inv','context']"}
  - {hook: BRANCH-REACTIVATE, kind: mutation, api: [API-INV-014], errors: "STALE-VERSION → reload", invalidation: "['inv','branches'], ['inv','branch',id], ['inv','lookup','branches'], ['inv','context']"}
  - {hook: BRANCH-SET-DEFAULT, kind: mutation, api: [API-INV-015], errors: "BRANCH-DEFAULT-INACTIVE → message; STALE-VERSION → reload", invalidation: "['inv','branches'], ['inv','lookup','branches']"}
  - {hook: BRANCHES-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| BRANCHES-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE / DELETE of `INV_BRANCHES`. Entity by id: BRANCH-QUERY when `entry=<id>` |
| BRANCHES-QUERY | read, ordered by code (REQ-INV-028) |
| BRANCH-CREATE | mutation `{nameAr, nameEn}`. INV-409-BRANCH-NAME-TAKEN → inline on nameAr, text: RULE-INV-011 · generic |
| BRANCH-UPDATE | mutation `{nameAr, nameEn, version}`; `code` is never sent. INV-409-BRANCH-NAME-TAKEN → inline on nameAr, text: RULE-INV-011 · INV-422-BRANCH-CODE-READONLY → message, text: RULE-INV-010 · INV-409-STALE-VERSION → reload |
| BRANCH-DEACTIVATE | mutation (DELETE), after a confirmation. INV-409-BRANCH-DEFAULT → message, text: RULE-INV-012 · INV-409-BRANCH-IN-USE → message, text: RULE-INV-013, with the next action "View warehouses", which opens SCR-INV-001 seeded with `branchPk` |
| BRANCH-REACTIVATE | mutation `{version}`. INV-409-STALE-VERSION → reload |
| BRANCH-SET-DEFAULT | mutation `{version}`. INV-409-BRANCH-DEFAULT-INACTIVE → message, text: RULE-INV-015 · INV-409-STALE-VERSION → reload |
| BRANCHES-FACADE | Owns the list, the filter `{code,name,active,page,size}`, the entry navigation `entry` and derived loading. Operations: `save()`, `deactivate`, `reactivate`, `setDefault`, `openEntry`, `closeEntry` |
<!-- SUB:F2-SCR-INV-002:END -->

<!-- SUB:F2-SCR-INV-003:START traces=SCR-INV-003,UXD-INV-001,UXD-INV-002,UXD-INV-003,REQ-INV-012,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-113,REQ-INV-115,REQ-INV-121,REQ-INV-135,REQ-INV-137,AC-INV-043,AC-INV-044,AC-INV-045,AC-INV-047,API-INV-001,API-INV-008,API-INV-016,API-INV-017,API-INV-040 -->
### F2 · SCR-INV-003 — Stock balances

```yaml name=screen-hooks
screen: SCR-INV-003
hooks:
  - {hook: BALANCES-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: BALANCES-QUERY, kind: read, api: [API-INV-016], cache_key: "['inv','stock-balances',filter{variantPk,term,itemPk,warehousePk,lotPk,nonZero,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: AVAILABILITY-QUERY, kind: read, api: [API-INV-017], cache_key: "['inv','availability',variantPk,warehousePk]", errors: "BUNDLE-NO-COMPONENTS → summary message; VARIANT-NOT-FOUND / WAREHOUSE-NOT-FOUND → summary message", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active}]", errors: "INV-403 → warehouse filter not offered (labels come from the rows)", loading: LOCAL}
  - {hook: LOTS-LOOKUP, kind: lookup, api: [API-INV-040], cache_key: "['inv','lookup','lots',{variantPk,warehousePk}]", errors: "INV-403 → lot filter not offered", loading: LOCAL}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','variant-search',term] · ['cat','barcode',barcode]", errors: "not found → 'no results' in the picker", loading: LOCAL}
  - {hook: CAT-ITEM-LOOKUP, kind: lookup, cache_key: "['cat','item-search',term] · ['cat','item',itemPk]", errors: "not found → name left empty", loading: LOCAL}
  - {hook: CAT-UOM-LOOKUP, kind: lookup, cache_key: "['cat','uom']", errors: "unknown code → the code is shown", loading: NONE}
  - {hook: BALANCES-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| BALANCES-SCREEN-INIT | Permission read: `PERM_INV_STOCK_BALANCES_VIEW`, plus `PERM_INV_WAREHOUSES_VIEW` and `PERM_INV_LOTS_VIEW` to decide whether the warehouse and lot filter lookups run (ADR-INV-036 — labels never depend on them). Context: `activeWarehouseCount` (filter and column hidden while it is 1 — REQ-INV-012), `lotExpiryFl` (lot filter and columns), `dualQuantityFl` (measure column — REQ-INV-135) |
| BALANCES-QUERY | read. Each row carries the Variant code and name, the warehouse code and names (the warehouse column's label for every role — ADR-INV-036), lot code, expiry, expired mark (REQ-INV-115), on-hand, reserved and available (REQ-INV-038, REQ-INV-113) and the measure. A Variant with no record answers zeros (REQ-INV-041) and the screen shows them as such |
| AVAILABILITY-QUERY | read, enabled only when the filter names one Variant. It shows per-warehouse rows (labelled from their `warehouseCode` / names) and total availability (REQ-INV-040); for a bundle Variant this is the derived availability (REQ-INV-042). INV-422-BUNDLE-NO-COMPONENTS → message in the summary, text: RULE-INV-022 · `INV-404-*` → summary message, text: catalogue |
| WAREHOUSES-LOOKUP | fills the warehouse filter only (the rows label themselves). Without `PERM_INV_WAREHOUSES_VIEW` it does not run and the filter is not offered (ADR-INV-036) |
| LOTS-LOOKUP | lot filter options, scoped by the chosen Variant; run only with `PERM_INV_LOTS_VIEW` |
| CAT-VARIANT-LOOKUP | UXD-INV-001: the Variant filter. Choosing a Variant sets `variantPk`. A scanned barcode goes to `term` unchanged |
| CAT-ITEM-LOOKUP | UXD-INV-002: the Item filter sets `itemPk`. The Item name column reads `['cat','variant',variantPk]` → `itemPk` → `['cat','item',itemPk]`, deduplicated per page |
| CAT-UOM-LOOKUP | UXD-INV-003: labels `measureUomCode` beside the measure (REQ-INV-121) |
| BALANCES-FACADE | Owns the rows from query data, the filter (with page and size) mirrored to the URL, the summary data and derived loading. Operations: `setFilter`, `openMoves(row)`, which navigates to SCR-INV-004 with `variantPk`, `warehousePk` and `lotPk`. No mutation |
<!-- SUB:F2-SCR-INV-003:END -->

<!-- SUB:F2-SCR-INV-004:START traces=SCR-INV-004,UXD-INV-004,REQ-INV-016,REQ-INV-055,REQ-INV-056,REQ-INV-075,REQ-INV-076,AC-INV-082,AC-INV-083,API-INV-001,API-INV-008,API-INV-018,API-INV-019,API-INV-040 -->
### F2 · SCR-INV-004 — Stock move history

```yaml name=screen-hooks
screen: SCR-INV-004
hooks:
  - {hook: MOVES-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: MOVES-QUERY, kind: read, api: [API-INV-018], cache_key: "['inv','stock-moves',filter{variantPk,warehousePk,lotPk,moveTypeCode,docNo,sourceDocTypeCode,sourceDocRef,dateFrom,dateTo,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: MOVE-QUERY, kind: read, api: [API-INV-019], cache_key: "['inv','stock-move',stockMovePk]", errors: "INV-404-STOCK-MOVE-NOT-FOUND → message, detail closes", loading: LOCAL, invalidation: "—"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active:'ALL'}]", errors: "generic", loading: LOCAL}
  - {hook: LOTS-LOOKUP, kind: lookup, api: [API-INV-040], cache_key: "['inv','lookup','lots',{variantPk,warehousePk}]", errors: "generic", loading: LOCAL}
  - {hook: LOOKUP-INV_MOVE_TYPE, kind: lookup, cache_key: "['mdl','INV_MOVE_TYPE']", loading: NONE}
  - {hook: LOOKUP-INV_SOURCE_DOC_TYPE, kind: lookup, cache_key: "['mdl','INV_SOURCE_DOC_TYPE']", loading: NONE}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','variant-search',term]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: MOVES-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| MOVES-SCREEN-INIT | Permission read: `PERM_INV_STOCK_MOVES_VIEW`. Context: warehouse count (REQ-INV-012) and flags. Lookups: move type and document type |
| MOVES-QUERY | read, newest first (REQ-INV-075). `balanceAfterQty` is shown only while the filter names one Variant and one warehouse (REQ-INV-076). A jump from a posted transfer or adjustment filters by `sourceDocTypeCode` + `sourceDocRef`, so only that document's moves are listed, never another module's document with the same number. Moves are read-only; no mutation exists (REQ-INV-055) |
| MOVE-QUERY | read of the detail drawer opened by `move=<id>`: source document type, identifier and number (REQ-INV-056), offline flag and bundle |
| WAREHOUSES-LOOKUP / LOTS-LOOKUP | labels: warehouse with `active=ALL`; the lot label scoped by the row's Variant and warehouse |
| CAT-VARIANT-LOOKUP | UXD-INV-004: the Variant filter. Row Variant labels come from `StockMove.variantCode / variantNameAr / variantNameEn` |
| MOVES-FACADE | Owns the rows, the filter (with page and size), the `move` navigation param and derived `showRunningBalance`. No mutation |
<!-- SUB:F2-SCR-INV-004:END -->

<!-- SUB:F2-SCR-INV-005:START traces=SCR-INV-005,UXD-INV-005,REQ-INV-011,REQ-INV-014,REQ-INV-062,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-122,AC-INV-088,AC-INV-090,AC-INV-091,AC-INV-093,AC-INV-094,API-INV-001,API-INV-008,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-040,API-INV-044 -->
### F2 · SCR-INV-005 — Transfers

```yaml name=screen-hooks
screen: SCR-INV-005
hooks:
  - {hook: TRANSFERS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: TRANSFERS-QUERY, kind: read, api: [API-INV-020], cache_key: "['inv','transfers',filter{docNo,dateFrom,dateTo,fromWarehousePk,toWarehousePk,statusCode,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: TRANSFER-QUERY, kind: read, api: [API-INV-021], cache_key: "['inv','transfer',transferPk]", errors: "INV-404-TRANSFER-NOT-FOUND → message, back to search", loading: LOCAL, invalidation: "—"}
  - {hook: TRANSFER-CREATE, kind: mutation, api: [API-INV-022], errors: "TRANSFER-WAREHOUSES → inline toWarehousePk; line codes → the line", invalidation: "['inv','transfers']"}
  - {hook: TRANSFER-UPDATE, kind: mutation, api: [API-INV-023], errors: "DOCUMENT-CLOSED → message + read-only; STALE-VERSION → reload; line codes → the line", invalidation: "['inv','transfers'], ['inv','transfer',id]"}
  - {hook: TRANSFER-POST, kind: mutation, api: [API-INV-024], errors: "NO-LINES / NEGATIVE-STOCK / TRANSFER-WAREHOUSES → message; VARIANT-INACTIVE / LOT-REQUIRED / MEASURE-REQUIRED → the line; DOCUMENT-CLOSED; STALE-VERSION → reload", invalidation: "['inv','transfers'], ['inv','transfer',id], ['inv','stock-balances'], ['inv','availability'], ['inv','stock-moves'], ['inv','lookup','lots']"}
  - {hook: TRANSFER-CANCEL, kind: mutation, api: [API-INV-025], errors: "DOCUMENT-CLOSED → message + read-only", invalidation: "['inv','transfers'], ['inv','transfer',id]"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active}]", errors: "generic", loading: LOCAL}
  - {hook: LOTS-LOOKUP, kind: lookup, api: [API-INV-040], cache_key: "['inv','lookup','lots',{variantPk,warehousePk:fromWarehousePk}]", errors: "generic", loading: LOCAL}
  - {hook: ITEM-TRACKING-LOOKUP, kind: lookup, api: [API-INV-044], cache_key: "['inv','item-settings',itemPk]", errors: "INV-404-ITEM-NOT-FOUND → line message", loading: LOCAL}
  - {hook: LOOKUP-INV_DOC_STATUS, kind: lookup, cache_key: "['mdl','INV_DOC_STATUS']", loading: NONE}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','barcode',barcode] · ['cat','variant-search',term] · ['cat','variant',variantPk]", errors: "unknown barcode → inline on the barcode field", loading: LOCAL}
  - {hook: CAT-UOM-LOOKUP, kind: lookup, cache_key: "['cat','uom']", loading: NONE}
  - {hook: TRANSFERS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| TRANSFERS-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE / DELETE of `INV_TRANSFERS`. Context: `activeWarehouseCount`; below 2 the route shows the RULE-INV-016 message instead of the entry, and the menu hides the screen (REQ-INV-011, REQ-INV-014). Flags `lotExpiryFl`, `dualQuantityFl`. Entity by id: TRANSFER-QUERY on `/:id` |
| TRANSFERS-QUERY | read, newest first (REQ-INV-086) |
| TRANSFER-CREATE | mutation `{docDate, fromWarehousePk, toWarehousePk, noteText, lines[]}`; each line is `{variantPk, lotPk, quantity, measureQty}`. INV-422-TRANSFER-WAREHOUSES → inline on toWarehousePk, text: RULE-INV-016 · INV-422-QUANTITY-INVALID → line quantity, text: RULE-INV-017 · INV-422-VARIANT-INACTIVE → line Variant, text: RULE-INV-018 · INV-422-LOT-REQUIRED → line lot, text: RULE-INV-037 · INV-422-MEASURE-REQUIRED → line measure, text: RULE-INV-040 · INV-422-DUPLICATE-LINE → line Variant, text: RULE-INV-048 · `INV-404-WAREHOUSE / VARIANT / LOT-NOT-FOUND` → field or line, text: catalogue. A line code is placed on the line given by `details[].rowNo` (1-based line order) when present, otherwise in the lines pane. On success the route becomes `/:id` |
| TRANSFER-UPDATE | mutation: the create body plus `{version}`, with each line keeping its `transferLinePk`; it replaces header and lines (REQ-INV-079). The create routing applies, plus INV-409-DOCUMENT-CLOSED → message, text: RULE-INV-029, and the entry reloads read-only · INV-422-DOC-NO-READONLY → message, text: RULE-INV-030 · INV-409-STALE-VERSION → reload |
| TRANSFER-POST | mutation `{version}`; offered only on a saved, unchanged DRAFT. INV-422-NO-LINES → message, text: RULE-INV-031 · INV-409-NEGATIVE-STOCK → message, text: RULE-INV-019 (the server text carries the on-hand) · INV-422-TRANSFER-WAREHOUSES → message, text: RULE-INV-016 (a warehouse was deactivated since the save) · INV-422-VARIANT-INACTIVE → the line, text: RULE-INV-018 · INV-422-LOT-REQUIRED → the line's lot, text: RULE-INV-037 · INV-422-MEASURE-REQUIRED → the line's measure, text: RULE-INV-040 (the Variant or its Item stock settings changed since the save — ADR-INV-045; the user edits the line, saves, then posts) · INV-409-DOCUMENT-CLOSED → message, text: RULE-INV-029 · INV-409-STALE-VERSION → reload |
| TRANSFER-CANCEL | mutation (DELETE), after a confirmation. INV-409-DOCUMENT-CLOSED → message, text: RULE-INV-029 |
| WAREHOUSES-LOOKUP | from / to selectors use `ACTIVE_ONLY`; labels use `ALL` |
| LOTS-LOOKUP | line lot options, scoped by the line's Variant and the header's from-warehouse (REQ-INV-084); expired lots are marked |
| ITEM-TRACKING-LOOKUP | per line Item. It decides whether the line asks for a lot (lotExpiry on and `lotTrackedFl`) and a measure (dualQuantity on and `dualQuantityFl`, REQ-INV-122) |
| CAT-VARIANT-LOOKUP / CAT-UOM-LOOKUP | UXD-INV-005: barcode → Variant, pick by code or name, label of each saved line's `variantPk`, the line Item's `itemPk`, and the measure unit label |
| TRANSFERS-FACADE | Owns the list and filter, the form state (header and lines), the `line` navigation param, `isDirty`, the derived `isReadOnly` (status ≠ DRAFT) and derived loading. Operations: `save()` (create or update, ONE call), `post()` and `cancel()` (enabled only when the form is clean and DRAFT), `addLine(draft)` and `replaceLine(key, draft)` (form state only), `removeLine(key)` |
<!-- SUB:F2-SCR-INV-005:END -->

<!-- SUB:F2-SCR-INV-006:START traces=SCR-INV-006,UXD-INV-006,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-122,AC-INV-097,AC-INV-098,AC-INV-101,AC-INV-103,AC-INV-106,API-INV-001,API-INV-008,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-040,API-INV-044 -->
### F2 · SCR-INV-006 — Stock counts

```yaml name=screen-hooks
screen: SCR-INV-006
hooks:
  - {hook: COUNTS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: COUNTS-QUERY, kind: read, api: [API-INV-026], cache_key: "['inv','stock-counts',filter{docNo,dateFrom,dateTo,warehousePk,statusCode,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: COUNT-QUERY, kind: read, api: [API-INV-027], cache_key: "['inv','stock-count',stockCountPk]", errors: "INV-404-STOCK-COUNT-NOT-FOUND → message, back to search", loading: LOCAL, invalidation: "—"}
  - {hook: COUNT-START, kind: mutation, api: [API-INV-028], errors: "WAREHOUSE-REQUIRED / WAREHOUSE-INACTIVE → inline warehousePk", invalidation: "['inv','stock-counts']"}
  - {hook: COUNT-LINE-ADD, kind: mutation, api: [API-INV-029], errors: "BARCODE-UNKNOWN → inline barcode + pick by hand; COUNTED-NEGATIVE → inline countedQty; LOT-REQUIRED / MEASURE-REQUIRED → line drawer; DOCUMENT-CLOSED → read-only", invalidation: "['inv','stock-count',id]"}
  - {hook: COUNT-LINE-CHANGE, kind: mutation, api: [API-INV-030], errors: "COUNTED-NEGATIVE / MEASURE-REQUIRED → inline in the line drawer; STALE-VERSION → reload", invalidation: "['inv','stock-count',id]"}
  - {hook: COUNT-LINE-REMOVE, kind: mutation, api: [API-INV-031], errors: "DOCUMENT-CLOSED → read-only", invalidation: "['inv','stock-count',id]"}
  - {hook: COUNT-POST, kind: mutation, api: [API-INV-032], errors: "NO-LINES / NEGATIVE-STOCK / WAREHOUSE-INACTIVE → message; VARIANT-INACTIVE / LOT-REQUIRED / MEASURE-REQUIRED → the line card; STALE-VERSION → reload", invalidation: "['inv','stock-counts'], ['inv','stock-count',id], ['inv','stock-adjustments'], ['inv','stock-balances'], ['inv','availability'], ['inv','stock-moves'], ['inv','valuation']"}
  - {hook: COUNT-CANCEL, kind: mutation, api: [API-INV-033], errors: "DOCUMENT-CLOSED → read-only", invalidation: "['inv','stock-counts'], ['inv','stock-count',id]"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active}]", errors: "generic", loading: LOCAL}
  - {hook: LOTS-LOOKUP, kind: lookup, api: [API-INV-040], cache_key: "['inv','lookup','lots',{variantPk,warehousePk}]", errors: "generic", loading: LOCAL}
  - {hook: ITEM-TRACKING-LOOKUP, kind: lookup, api: [API-INV-044], cache_key: "['inv','item-settings',itemPk]", errors: "generic", loading: LOCAL}
  - {hook: LOOKUP-INV_DOC_STATUS, kind: lookup, cache_key: "['mdl','INV_DOC_STATUS']", loading: NONE}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','barcode',barcode] · ['cat','variant-search',term] · ['cat','variant',variantPk]", errors: "not found → RULE-INV-032 text inline on barcode", loading: LOCAL}
  - {hook: CAT-UOM-LOOKUP, kind: lookup, cache_key: "['cat','uom']", loading: NONE}
  - {hook: COUNTS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| COUNTS-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE / DELETE of `INV_COUNTS`. Context: `activeWarehouseCount`. While it is 1, "Start count" sends no warehouse and the server applies the default (REQ-INV-013); otherwise `/new` asks for it. Flags. Entity by id: COUNT-QUERY on `/:id` |
| COUNTS-QUERY | read, newest first (REQ-INV-095) |
| COUNT-START | mutation `{warehousePk | null, docDate, noteText}`. INV-422-WAREHOUSE-REQUIRED → inline on warehousePk, text: RULE-INV-008 · INV-422-WAREHOUSE-INACTIVE → inline on warehousePk, text: RULE-INV-007 · INV-404-WAREHOUSE-NOT-FOUND → inline, text: catalogue. On success the route becomes `/:id` |
| COUNT-LINE-ADD | mutation `{barcode | variantPk, countedQty, lotPk, countedMeasureQty}`, ONE call per scan (ADR-INV-034). The server adds to an existing line (REQ-INV-088). INV-422-BARCODE-UNKNOWN → inline on barcode, text: RULE-INV-032, and the Variant picker is offered (REQ-INV-091) · INV-422-VARIANT-INACTIVE → inline on barcode, text: RULE-INV-018 · INV-422-COUNTED-NEGATIVE → inline on countedQty, text: RULE-INV-047 · INV-422-LOT-REQUIRED → the line drawer opens at lot, text: RULE-INV-037 · INV-422-MEASURE-REQUIRED → the line drawer opens at measure, text: RULE-INV-040 · INV-409-DOCUMENT-CLOSED → message, text: RULE-INV-029, and the entry reloads read-only (REQ-INV-096) · `INV-404-*` → catalogue |
| COUNT-LINE-CHANGE | mutation `{countedQty, countedMeasureQty, version}` from the one-line drawer. INV-422-COUNTED-NEGATIVE → inline, text: RULE-INV-047 · INV-422-MEASURE-REQUIRED → inline, text: RULE-INV-040 · INV-409-DOCUMENT-CLOSED → read-only, text: RULE-INV-029 · INV-409-STALE-VERSION → reload |
| COUNT-LINE-REMOVE | mutation (DELETE line), after a confirmation in the drawer. INV-409-DOCUMENT-CLOSED → read-only, text: RULE-INV-029 |
| COUNT-POST | mutation `{version}`, after a confirmation. The answer carries the count and the resulting adjustment (or null when every difference is zero). INV-422-NO-LINES → message, text: RULE-INV-031 · INV-409-NEGATIVE-STOCK → message, text: RULE-INV-019 · INV-422-WAREHOUSE-INACTIVE → message, text: RULE-INV-007 · INV-422-VARIANT-INACTIVE → the line card, text: RULE-INV-018 · INV-422-LOT-REQUIRED → the line drawer at lot, text: RULE-INV-037 · INV-422-MEASURE-REQUIRED → the line drawer at measure, text: RULE-INV-040 (re-checked on post — ADR-INV-045) · INV-409-DOCUMENT-CLOSED → read-only · INV-409-STALE-VERSION → reload. A count never answers INV-422-NO-KNOWN-COST: an increase of a never-valued Variant enters at cost 0 (ADR-INV-030). On success the entry shows systemQty and differenceQty, plus a link to the adjustment |
| COUNT-CANCEL | mutation (DELETE), after a confirmation. INV-409-DOCUMENT-CLOSED → read-only, text: RULE-INV-029 |
| WAREHOUSES-LOOKUP / LOTS-LOOKUP / ITEM-TRACKING-LOOKUP | the warehouse selector at start; lot options scoped by the scanned Variant and the count's warehouse; whether a lot or measure must be asked before the line call is sent |
| CAT-VARIANT-LOOKUP / CAT-UOM-LOOKUP | UXD-INV-006. A scanned barcode is resolved first, to get `itemPk` → ITEM-TRACKING-LOOKUP. For an Item with no lot or measure, the scan is sent at once with `barcode`. Otherwise the line drawer asks for the lot or measure and sends `variantPk`. The hooks also pick a Variant by hand after an unknown barcode, and label each line's `variantPk` |
| COUNTS-FACADE | Owns the list and filter, the scan state, the `line` navigation param (a `stockCountLinePk`), derived `isReadOnly` and derived loading. The lines shown are COUNT-QUERY data, never a local copy. Operations: `start()`, `scan(barcode, qty)`, `addPicked(variant, qty, lot, measure)`, `changeLine(lineId, …)`, `removeLine(lineId)`, `post()`, `cancel()` |
<!-- SUB:F2-SCR-INV-006:END -->

<!-- SUB:F2-SCR-INV-007:START traces=SCR-INV-007,UXD-INV-007,REQ-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-067,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-114,REQ-INV-123,AC-INV-109,AC-INV-111,AC-INV-113,AC-INV-115,AC-INV-128,API-INV-001,API-INV-008,API-INV-016,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-044 -->
### F2 · SCR-INV-007 — Stock adjustments

```yaml name=screen-hooks
screen: SCR-INV-007
hooks:
  - {hook: ADJUSTMENTS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: ADJUSTMENTS-QUERY, kind: read, api: [API-INV-034], cache_key: "['inv','stock-adjustments',filter{docNo,dateFrom,dateTo,warehousePk,reasonCode,statusCode,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: ADJUSTMENT-QUERY, kind: read, api: [API-INV-035], cache_key: "['inv','stock-adjustment',stockAdjustmentPk]", errors: "INV-404-STOCK-ADJUSTMENT-NOT-FOUND → message, back to search", loading: LOCAL, invalidation: "—"}
  - {hook: WRITE-OFF-LOT-QUERY, kind: read, api: [API-INV-041], cache_key: "['inv','lot',lotPk]", errors: "INV-404-LOT-NOT-FOUND → message, no seeded line", loading: LOCAL, invalidation: "—"}
  - {hook: WRITE-OFF-BALANCE-QUERY, kind: read, api: [API-INV-016], cache_key: "['inv','stock-balances',{lotPk,warehousePk,nonZero:true,page:0,size:1}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: ADJUSTMENT-CREATE, kind: mutation, api: [API-INV-036], errors: "REASON-REQUIRED → inline reasonCode; WAREHOUSE-* → inline warehousePk; line codes → the line", invalidation: "['inv','stock-adjustments']"}
  - {hook: ADJUSTMENT-UPDATE, kind: mutation, api: [API-INV-037], errors: "DOCUMENT-CLOSED → read-only; STALE-VERSION → reload; line codes → the line", invalidation: "['inv','stock-adjustments'], ['inv','stock-adjustment',id]"}
  - {hook: ADJUSTMENT-POST, kind: mutation, api: [API-INV-038], errors: "NEGATIVE-STOCK / NO-LINES / WAREHOUSE-INACTIVE → message; VARIANT-INACTIVE, cost and lot codes → the line; STALE-VERSION → reload", invalidation: "['inv','stock-adjustments'], ['inv','stock-adjustment',id], ['inv','stock-balances'], ['inv','availability'], ['inv','stock-moves'], ['inv','valuation'], ['inv','lookup','lots'], ['inv','lots']"}
  - {hook: ADJUSTMENT-CANCEL, kind: mutation, api: [API-INV-039], errors: "DOCUMENT-CLOSED → read-only", invalidation: "['inv','stock-adjustments'], ['inv','stock-adjustment',id]"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active}]", errors: "generic", loading: LOCAL}
  - {hook: LOTS-LOOKUP, kind: lookup, api: [API-INV-040], cache_key: "['inv','lookup','lots',{variantPk,warehousePk}]", errors: "generic", loading: LOCAL}
  - {hook: ITEM-TRACKING-LOOKUP, kind: lookup, api: [API-INV-044], cache_key: "['inv','item-settings',itemPk]", errors: "generic", loading: LOCAL}
  - {hook: LOOKUP-INV_ADJUSTMENT_REASON, kind: lookup, cache_key: "['mdl','INV_ADJUSTMENT_REASON']", loading: NONE}
  - {hook: LOOKUP-INV_DOC_STATUS, kind: lookup, cache_key: "['mdl','INV_DOC_STATUS']", loading: NONE}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','barcode',barcode] · ['cat','variant-search',term] · ['cat','variant',variantPk]", errors: "unknown barcode → inline on the barcode field", loading: LOCAL}
  - {hook: CAT-UOM-LOOKUP, kind: lookup, cache_key: "['cat','uom']", loading: NONE}
  - {hook: ADJUSTMENTS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| ADJUSTMENTS-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE / DELETE of `INV_ADJUSTMENTS`. Context: `activeWarehouseCount`; while it is 1 there is no warehouse field and `warehousePk` is sent null (REQ-INV-010, REQ-INV-013, AC-INV-013). Flags: lot columns hidden with lotExpiry off (AC-INV-150), measure with dualQuantity off. Lookups: reason, status. Entity by id: ADJUSTMENT-QUERY on `/:id`. Seed: WRITE-OFF-* on `/new?reason=EXPIRY_WRITE_OFF&lotPk&warehousePk` |
| ADJUSTMENTS-QUERY | read, newest first (REQ-INV-104) |
| WRITE-OFF-LOT-QUERY | read of the seeded lot (code, `variantPk`, expiry), so the seeded line gets `variantPk` and `lotNo = code` |
| WRITE-OFF-BALANCE-QUERY | read of the one balance row identified by `lotPk` + `warehousePk`. The seeded line gets `quantityChange = −onHandQty` (AC-INV-128); with no row, no line is seeded |
| ADJUSTMENT-CREATE | mutation `{warehousePk | null, docDate, reasonCode, noteText, lines[]}`; each line is `{rowNo, variantPk, lotNo, lotExpiryDate, quantityChange, measureQtyChange, unitCost}`. INV-422-REASON-REQUIRED → inline on reasonCode, text: RULE-INV-033 · INV-422-WAREHOUSE-REQUIRED → inline on warehousePk, text: RULE-INV-008 · INV-422-WAREHOUSE-INACTIVE → inline, text: RULE-INV-007 · INV-422-VARIANT-INACTIVE → line, text: RULE-INV-018 · INV-422-QUANTITY-INVALID → line quantityChange or measureQtyChange, text: RULE-INV-017 · INV-422-NO-KNOWN-COST → line unitCost (more than 4 decimals), text: RULE-INV-025 · INV-422-ZERO-CHANGE → line quantityChange, text: RULE-INV-034 · INV-422-LOT-REQUIRED → line lot, text: RULE-INV-037 · INV-422-DUPLICATE-LINE → line, text: RULE-INV-048 · `INV-404-*` → catalogue. A line code goes to the line whose `rowNo` equals `details[].rowNo` |
| ADJUSTMENT-UPDATE | mutation `{reasonCode, noteText, version, lines[]}`; it replaces reason, note and lines (REQ-INV-098). The create routing applies, plus INV-409-DOCUMENT-CLOSED → read-only, text: RULE-INV-029 · INV-422-DOC-NO-READONLY → message, text: RULE-INV-030 · INV-409-STALE-VERSION → reload |
| ADJUSTMENT-POST | mutation `{version}`; offered only on a saved, unchanged DRAFT. INV-422-NO-LINES → message, text: RULE-INV-031 · INV-409-NEGATIVE-STOCK → message, text: RULE-INV-019 (REQ-INV-067) · INV-422-WAREHOUSE-INACTIVE → message, text: RULE-INV-007 · INV-422-VARIANT-INACTIVE → the line, text: RULE-INV-018 (both re-checked on post — ADR-INV-045) · INV-422-NO-KNOWN-COST → the line's unitCost, Advanced opened, text: RULE-INV-025 (REQ-INV-064, REQ-INV-102) · INV-422-OPENING-COST-REQUIRED → the line's unitCost, text: RULE-INV-026 · INV-422-LOT-EXPIRY-REQUIRED → the line's lot number and expiry, text: RULE-INV-035 (REQ-INV-105) · INV-422-LOT-EXPIRY-MISMATCH → the line's expiry, text: RULE-INV-036 (REQ-INV-107) · INV-422-LOT-REQUIRED → the line's lot, text: RULE-INV-037 · INV-422-REASON-REQUIRED / INV-422-ZERO-CHANGE → as on create · INV-409-DOCUMENT-CLOSED → read-only · INV-409-STALE-VERSION → reload |
| ADJUSTMENT-CANCEL | mutation (DELETE), after a confirmation. INV-409-DOCUMENT-CLOSED → read-only, text: RULE-INV-029 |
| LOTS-LOOKUP | for a decrease line of a lot-tracked Item: lot options scoped by the line's Variant and the header's warehouse; choosing one sets `lotNo = code`. For an increase line: when the typed `lotNo` matches an option, its expiry is prefilled (REQ-INV-106) |
| ITEM-TRACKING-LOOKUP | per line Item: whether lot and expiry, or a measure change, are asked (REQ-INV-123: a measure-only line is allowed for a dual-quantity Item) |
| CAT-VARIANT-LOOKUP / CAT-UOM-LOOKUP | UXD-INV-007: barcode → Variant, pick, label of each saved line's `variantPk`, the line Item, the measure unit label |
| ADJUSTMENTS-FACADE | Owns the list and filter, form state (header and lines, with `rowNo` kept in line order), the `line` navigation param, `isDirty`, `isReadOnly`, the line value total (posted documents) and derived loading. Operations: `save()` (ONE call), `post()`, `cancel()`, `addLine`, `replaceLine`, `removeLine` (form state only) |
<!-- SUB:F2-SCR-INV-007:END -->

<!-- SUB:F2-SCR-INV-008:START traces=SCR-INV-008,UXD-INV-008,REQ-INV-108,REQ-INV-109,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-134,AC-INV-121,AC-INV-122,AC-INV-129,AC-INV-130,API-INV-001,API-INV-008,API-INV-040,API-INV-041,API-INV-042 -->
### F2 · SCR-INV-008 — Lots and near expiry

```yaml name=screen-hooks
screen: SCR-INV-008
hooks:
  - {hook: LOTS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: LOTS-QUERY, kind: read, api: [API-INV-040], cache_key: "['inv','lots',filter{variantPk,code,warehousePk,expiryFrom,expiryTo,nearExpiryOnly,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: LOT-QUERY, kind: read, api: [API-INV-041], cache_key: "['inv','lot',lotPk]", errors: "INV-404-LOT-NOT-FOUND → message, drawer closes", loading: LOCAL, invalidation: "—"}
  - {hook: LOT-CORRECT, kind: mutation, api: [API-INV-042], errors: "STALE-VERSION → reload; INV-404-LOT-NOT-FOUND → message", invalidation: "['inv','lots'], ['inv','lot',id], ['inv','lookup','lots'], ['inv','stock-balances'], ['inv','availability']"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active}]", errors: "generic", loading: LOCAL}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','variant-search',term] · ['cat','variant',variantPk]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: LOTS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| LOTS-SCREEN-INIT | Permission read: `PERM_INV_LOTS_VIEW`, `PERM_INV_LOTS_UPDATE`. Context: `lotExpiryFl`; with it off the route answers "not available" and the menu hides it (REQ-INV-134). Warehouse count. Entity by id: LOT-QUERY when `entry=<id>` |
| LOTS-QUERY | read, ordered by expiry date (REQ-INV-109). `nearExpiryOnly` gives the near-expiry view (REQ-INV-116); rows carry `daysLeft`, `expiredFl` (REQ-INV-115) and `nearExpiryNotifiedAt` (REQ-INV-117) |
| LOT-CORRECT | mutation `{expiryDate, nameAr, nameEn, version}` (REQ-INV-108). INV-409-STALE-VERSION → reload · `INV-404-*` → catalogue · generic |
| CAT-VARIANT-LOOKUP | UXD-INV-008: the Variant filter, and labels of `LotRow.variantPk`, deduplicated per page |
| LOTS-FACADE | Owns the rows, the filter with page and size (`nearExpiryOnly` seeded by the notification link), the `entry` param and derived loading. Operations: `saveExpiry()`, `writeOff(row)` (navigates to SCR-INV-007 `/new?reason=EXPIRY_WRITE_OFF&lotPk&warehousePk`, offered for `expiredFl` rows with on-hand ≠ 0), `openBalances(row)` |
<!-- SUB:F2-SCR-INV-008:END -->

<!-- SUB:F2-SCR-INV-009:START traces=SCR-INV-009,UXD-INV-009,UXD-INV-010,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,AC-INV-139,AC-INV-141,AC-INV-142,AC-INV-143,AC-INV-144,API-INV-008,API-INV-043,API-INV-044,API-INV-045 -->
### F2 · SCR-INV-009 — Item stock settings

```yaml name=screen-hooks
screen: SCR-INV-009
hooks:
  - {hook: ITEM-SETTINGS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: ITEM-SETTINGS-SEARCH-QUERY, kind: read, api: [API-INV-043], cache_key: "['inv','item-settings-search',filter{itemPk,term,lotTracked,dualQuantity,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: ITEM-TRACKING-LOOKUP, kind: read, api: [API-INV-044], cache_key: "['inv','item-settings',itemPk]", errors: "INV-404-ITEM-NOT-FOUND → message, drawer closes", loading: LOCAL, invalidation: "—"}
  - {hook: ITEM-SETTINGS-SAVE, kind: mutation, api: [API-INV-045], errors: "LOT-TRACKING-IN-USE / DUAL-QUANTITY-IN-USE / BUNDLE-SETTINGS / MEASURE-UNIT-REQUIRED → inline; CAPABILITY-OFF → message; STALE-VERSION → reload", invalidation: "['inv','item-settings-search'], ['inv','item-settings',itemPk]"}
  - {hook: CAT-ITEM-LOOKUP, kind: lookup, cache_key: "['cat','item-search',term] · ['cat','item',itemPk]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: CAT-UOM-LOOKUP, kind: lookup, cache_key: "['cat','uom']", loading: NONE}
  - {hook: ITEM-SETTINGS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| ITEM-SETTINGS-SCREEN-INIT | Permission read: VIEW / CREATE / UPDATE of `INV_ITEM_STOCK_SETTINGS`. Context: `lotExpiryFl`, `dualQuantityFl`. With both off the screen is not reachable; each control shows only with its flag (REQ-INV-134, REQ-INV-135). Entity by id: ITEM-TRACKING-LOOKUP on `entry=<itemPk>` |
| ITEM-SETTINGS-SEARCH-QUERY | read. Rows carry the Item's code and names from the INV answer |
| ITEM-TRACKING-LOOKUP | read of one Item's settings for the entry (REQ-INV-129); the same shared key the line drawers use |
| ITEM-SETTINGS-SAVE | mutation `{lotTrackedFl, dualQuantityFl, measureUomCode, version | null}`; idempotent create-or-update (ADR-INV-029). INV-409-LOT-TRACKING-IN-USE → inline on lotTrackedFl, text: RULE-INV-042 (the server text carries the count) · INV-409-DUAL-QUANTITY-IN-USE → inline on dualQuantityFl, text: RULE-INV-043 · INV-422-BUNDLE-SETTINGS → inline on the Item, text: RULE-INV-045 · INV-422-MEASURE-UNIT-REQUIRED → inline on measureUomCode, text: RULE-INV-041 · INV-422-CAPABILITY-OFF → message, text: RULE-INV-044 (the catalogue text) · INV-409-STALE-VERSION → reload · INV-404-ITEM-NOT-FOUND → catalogue |
| CAT-ITEM-LOOKUP | UXD-INV-009: the Item pick of a new record. A BUNDLE Item is shown with its kind; the server refuses it (RULE-INV-045) |
| CAT-UOM-LOOKUP | UXD-INV-010: measureUomCode options and labels |
| ITEM-SETTINGS-FACADE | Owns the rows, the filter with page and size, the `entry` param, form state and derived loading. Operations: `save()`, `openEntry(itemPk | 'new')`, `closeEntry()` |
<!-- SUB:F2-SCR-INV-009:END -->

<!-- SUB:F2-SCR-INV-010:START traces=SCR-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-122,AC-INV-078,AC-INV-079,AC-INV-081,API-INV-001,API-INV-008,API-INV-046,API-INV-047 -->
### F2 · SCR-INV-010 — Opening stock

```yaml name=screen-hooks
screen: SCR-INV-010
hooks:
  - {hook: OPENING-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: OPENING-SHEET-QUERY, kind: read, api: [API-INV-046], cache_key: "none — imperative file download", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: OPENING-STOCK-POST, kind: mutation, api: [API-INV-047], errors: "LOAD-ROWS-INVALID → row failures list by rowNo; LOAD-ROWS → message; WAREHOUSE-* → inline warehousePk", invalidation: "['inv','stock-adjustments'], ['inv','stock-balances'], ['inv','availability'], ['inv','stock-moves'], ['inv','valuation'], ['inv','lookup','lots'], ['inv','lots']"}
  - {hook: WAREHOUSES-LOOKUP, kind: lookup, api: [API-INV-001], cache_key: "['inv','lookup','warehouses',{active:'ACTIVE_ONLY'}]", errors: "generic", loading: LOCAL}
  - {hook: OPENING-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| OPENING-SCREEN-INIT | Permission read: `PERM_INV_OPENING_STOCK_VIEW`, `_CREATE`. Context: warehouse count (selector hidden while 1); `lotExpiryFl` and `dualQuantityFl` decide the lot, expiry and measure columns (AC-INV-080) |
| OPENING-SHEET-QUERY | read of the xlsx file (REQ-INV-073), started by "Download sheet"; it saves the blob under the catalogue file name. Not cached |
| OPENING-STOCK-POST | mutation `{warehousePk | null, docDate, rows[]}`; each row is `{rowNo, barcode, variantPk: null, quantity, unitCost, lotNo, lotExpiryDate, measureQty}`, all in ONE call (ADR-INV-027, ADR-INV-035). INV-422-LOAD-ROWS-INVALID → the row failures list: each `details[]` entry is placed on the row with its `rowNo` and its own code's text. Those codes are INV-422-LOAD-ROW-VARIANT (RULE-INV-028), INV-422-QUANTITY-INVALID (RULE-INV-017), INV-422-NO-KNOWN-COST (RULE-INV-025, a cost with more than 4 decimals), INV-422-OPENING-COST-REQUIRED (RULE-INV-026), INV-422-VARIANT-INACTIVE (RULE-INV-018), INV-422-LOT-EXPIRY-REQUIRED (RULE-INV-035), INV-422-LOT-EXPIRY-MISMATCH (RULE-INV-036), INV-422-MEASURE-REQUIRED (RULE-INV-040), INV-422-DUPLICATE-LINE (RULE-INV-048), and nothing is posted (REQ-INV-072) · INV-422-LOAD-ROWS → message, text: RULE-INV-027 · INV-422-WAREHOUSE-REQUIRED → inline on warehousePk, text: RULE-INV-008 · INV-422-WAREHOUSE-INACTIVE → inline, text: RULE-INV-007 · INV-404-WAREHOUSE-NOT-FOUND → catalogue · generic. On success it navigates to SCR-INV-007 `/:id` of the returned adjustment |
| OPENING-FACADE | Owns the form state (rows with `rowNo`), the `line` navigation param, the row failures, the sheet-reading progress and derived loading. Operations: `downloadSheet()`, `loadSheet(file)` (reads the file in the browser, ADR-INV-035, and fills the rows; replacing existing rows asks for confirmation), `addRow`, `replaceRow`, `removeRow` (form state only), `post()` (ONE call) |
<!-- SUB:F2-SCR-INV-010:END -->

<!-- SUB:F2-SCR-INV-011:START traces=SCR-INV-011,REQ-INV-065,REQ-INV-066,REQ-INV-118,REQ-INV-119,AC-INV-132,AC-INV-133,API-INV-008,API-INV-048,API-INV-049 -->
### F2 · SCR-INV-011 — Inventory settings

```yaml name=screen-hooks
screen: SCR-INV-011
hooks:
  - {hook: SETTINGS-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: SETTINGS-QUERY, kind: read, api: [API-INV-048], cache_key: "['inv','settings']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: SETTINGS-SAVE, kind: mutation, api: [API-INV-049], errors: "HORIZON-RANGE → inline nearExpiryDays", invalidation: "['inv','settings'], ['inv','lots']"}
  - {hook: SETTINGS-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| SETTINGS-SCREEN-INIT | Permission read: `PERM_INV_SETTINGS_VIEW`, `_UPDATE`. Context: `lotExpiryFl`, which decides whether the horizon is shown |
| SETTINGS-QUERY | read. A tenant that never saved shows "allowed" and 30 days (REQ-INV-066, AC-INV-132) |
| SETTINGS-SAVE | mutation `{allowNegativeStockFl, nearExpiryDays}`. With lotExpiry off, the read horizon is sent unchanged. INV-422-HORIZON-RANGE → inline on nearExpiryDays, text: catalogue INV-422-HORIZON-RANGE «أدخل عدد أيام من 1 إلى 365.» / "Enter a number of days from 1 to 365." (AC-INV-133) · generic |
| SETTINGS-FACADE | Owns the form state seeded from SETTINGS-QUERY, `isDirty` and derived loading. Operation: `save()` |
<!-- SUB:F2-SCR-INV-011:END -->

<!-- SUB:F2-SCR-INV-012:START traces=SCR-INV-012,UXD-INV-011,REQ-INV-059,REQ-INV-061,REQ-INV-063,AC-INV-069,API-INV-008,API-INV-050,API-INV-051 -->
### F2 · SCR-INV-012 — Stock valuation

```yaml name=screen-hooks
screen: SCR-INV-012
hooks:
  - {hook: VALUATION-SCREEN-INIT, kind: init, api: [API-INV-008], cache_key: "['inv','context']", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: VALUATION-QUERY, kind: read, api: [API-INV-050], cache_key: "['inv','valuation',filter{variantPk,term,itemPk,nonZero,page,size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: VALUATION-DETAIL-QUERY, kind: read, api: [API-INV-051], cache_key: "['inv','valuation','variant',variantPk]", errors: "INV-404-VARIANT-NOT-FOUND → message, detail closes", loading: LOCAL, invalidation: "—"}
  - {hook: CAT-VARIANT-LOOKUP, kind: lookup, cache_key: "['cat','variant-search',term]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: CAT-ITEM-LOOKUP, kind: lookup, cache_key: "['cat','item-search',term]", errors: "not found → 'no results'", loading: LOCAL}
  - {hook: VALUATION-FACADE, kind: facade}
```

| Hook | Role · cache · errors (code → routing, text) |
|---|---|
| VALUATION-SCREEN-INIT | Permission read: `PERM_INV_STOCK_VALUATION_VIEW` |
| VALUATION-QUERY | read. Rows carry total quantity, weighted-average unit cost and stock value (REQ-INV-063, REQ-INV-059, REQ-INV-061). The page carries `grandTotalValue`, shown as the screen total |
| VALUATION-DETAIL-QUERY | read of the `variant=<id>` detail drawer, which adds the last move |
| CAT-VARIANT-LOOKUP / CAT-ITEM-LOOKUP | UXD-INV-011: the Variant and Item filters set `variantPk` / `itemPk`; a scanned barcode goes to `term` |
| VALUATION-FACADE | Owns the rows, the total, the filter with page and size, the `variant` param and derived loading. Operation: `openMoves(variantPk)`. No mutation |
<!-- SUB:F2-SCR-INV-012:END -->
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=SCR-INV-001,SCR-INV-002,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008,SCR-INV-009,SCR-INV-010,SCR-INV-011,SCR-INV-012,UXD-INV-001,UXD-INV-002,UXD-INV-004,UXD-INV-005,UXD-INV-006,UXD-INV-007,UXD-INV-008,UXD-INV-009,UXD-INV-010,UXD-INV-011,REQ-INV-003,REQ-INV-004,REQ-INV-009,REQ-INV-010,REQ-INV-013,REQ-INV-014,REQ-INV-017,REQ-INV-022,REQ-INV-023,REQ-INV-028,REQ-INV-029,REQ-INV-031,REQ-INV-039,REQ-INV-063,REQ-INV-065,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-074,REQ-INV-075,REQ-INV-078,REQ-INV-079,REQ-INV-082,REQ-INV-084,REQ-INV-086,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-095,REQ-INV-097,REQ-INV-098,REQ-INV-101,REQ-INV-102,REQ-INV-104,REQ-INV-105,REQ-INV-108,REQ-INV-109,REQ-INV-116,REQ-INV-118,REQ-INV-119,REQ-INV-122,REQ-INV-123,REQ-INV-124,REQ-INV-125,REQ-INV-128,REQ-INV-129,AC-INV-004,AC-INV-026,AC-INV-078,AC-INV-081,AC-INV-090,AC-INV-093,AC-INV-098,AC-INV-109,AC-INV-111,AC-INV-113,AC-INV-133,AC-INV-141,API-INV-001,API-INV-003,API-INV-004,API-INV-009,API-INV-011,API-INV-012,API-INV-016,API-INV-018,API-INV-020,API-INV-022,API-INV-023,API-INV-026,API-INV-028,API-INV-029,API-INV-030,API-INV-034,API-INV-036,API-INV-037,API-INV-040,API-INV-042,API-INV-043,API-INV-045,API-INV-047,API-INV-049,API-INV-050 -->
## F3 — Forms & Validators

**RF3.** Forms use `react-hook-form` with a `zod` resolver. Each schema mirrors the request schema of
`api-spec-inv.yaml`: its required fields, `maxLength`, `minimum` and `exclusiveMinimum`. Where the SRS RULE can be
checked in the browser, the schema adds it with the RULE's own message, in ar and en. The server stays the
authority: every server code reaches the same field through the F2 routing. Message keys are the RULE ids, and
the texts are the SRS A5 messages. Decimal fields accept at most 3 places (quantities and measures) or 4 places
(costs), per DEFAULT D-06 and RULE-INV-017. Validation runs on submit and then on change. Search forms validate
only date ranges, where "from" must not be after "to"; the message is «تاريخ البداية بعد تاريخ النهاية.» /
"The start date is after the end date.".

<!-- SUB:F3-SCR-INV-001:START traces=SCR-INV-001,REQ-INV-003,REQ-INV-004,REQ-INV-009,REQ-INV-029,REQ-INV-031,AC-INV-004,API-INV-001,API-INV-003,API-INV-004 -->
### F3 · SCR-INV-001 — Warehouses
- `warehouseSearchSchema`: `code` ≤ 20, `name` ≤ 100, `active` ∈ {ACTIVE_ONLY, ALL}.
- `warehouseFormSchema`:
  - `nameAr`: required, 1–100; empty → «اسم المستودع بالعربية مطلوب.» / "The warehouse name (Arabic) is required.".
  - `nameEn`: ≤ 100, optional.
  - `branchPk`: optional; required on update, and the record's value is kept when the field is hidden (REQ-INV-029, REQ-INV-031).
  - `code`: display only, never part of the form value (RULE-INV-001).
- Name uniqueness (RULE-INV-002) is a server check, shown inline on `nameAr`.
<!-- SUB:F3-SCR-INV-001:END -->

<!-- SUB:F3-SCR-INV-002:START traces=SCR-INV-002,REQ-INV-022,REQ-INV-023,REQ-INV-028,AC-INV-026,API-INV-009,API-INV-011,API-INV-012 -->
### F3 · SCR-INV-002 — Branches
- `branchSearchSchema`: `code` ≤ 20, `name` ≤ 100, `active`.
- `branchFormSchema`:
  - `nameAr`: required, 1–100; empty → «اسم الفرع بالعربية مطلوب.» / "The branch name (Arabic) is required.".
  - `nameEn`: ≤ 100.
  - `code`: display only (RULE-INV-010).
- Name uniqueness (RULE-INV-011) is a server check.
<!-- SUB:F3-SCR-INV-002:END -->

<!-- SUB:F3-SCR-INV-003:START traces=SCR-INV-003,UXD-INV-001,UXD-INV-002,REQ-INV-039,API-INV-016 -->
### F3 · SCR-INV-003 — Stock balances
- `balanceSearchSchema`:
  - `variantPk` / `itemPk`: from the pickers (UXD-INV-001, UXD-INV-002).
  - `term`: ≤ 64, a scanned barcode.
  - `warehousePk`, `lotPk`: from the lookups.
  - `nonZero`: boolean.
- A change of any filter resets `page` to 0. No entry form.
<!-- SUB:F3-SCR-INV-003:END -->

<!-- SUB:F3-SCR-INV-004:START traces=SCR-INV-004,UXD-INV-004,REQ-INV-075,API-INV-018 -->
### F3 · SCR-INV-004 — Stock move history
- `moveSearchSchema`:
  - `variantPk`: from UXD-INV-004.
  - `warehousePk`, `lotPk`.
  - `moveTypeCode`: an INV_MOVE_TYPE option.
  - `docNo`: ≤ 30.
  - `sourceDocTypeCode` (an INV_SOURCE_DOC_TYPE option) and `sourceDocRef` (integer): both or neither; set by the document jumps only.
  - `dateFrom` ≤ `dateTo`, both ISO dates.
- No entry form (REQ-INV-055).
<!-- SUB:F3-SCR-INV-004:END -->

<!-- SUB:F3-SCR-INV-005:START traces=SCR-INV-005,UXD-INV-005,REQ-INV-014,REQ-INV-078,REQ-INV-079,REQ-INV-082,REQ-INV-084,REQ-INV-086,REQ-INV-122,AC-INV-090,AC-INV-093,API-INV-020,API-INV-022,API-INV-023 -->
### F3 · SCR-INV-005 — Transfers
- `transferSearchSchema`: `docNo` ≤ 30, date range, `fromWarehousePk`, `toWarehousePk`, `statusCode` (an INV_DOC_STATUS option).
- `transferFormSchema`:
  - `fromWarehousePk` and `toWarehousePk`: required. Equal values give a refinement on `toWarehousePk`, text: RULE-INV-016 (REQ-INV-082).
  - `docDate`: date, defaulted to today.
  - `noteText`: ≤ 500.
  - `lines`: array. Two lines with the same `variantPk` and `lotPk` give a refinement on the second line, text: RULE-INV-048.
- `transferLineSchema` (the one-line drawer):
  - `variant`: required; empty → «اختر متغيّر الصنف.» / "Choose the Variant.".
  - `quantity`: > 0 with ≤ 3 decimals, text: RULE-INV-017.
  - `lotPk`: required when `lotExpiryFl` and the Item is `lotTrackedFl`, text: RULE-INV-037 (AC-INV-093).
  - `measureQty`: > 0, required when `dualQuantityFl` and the Item is `dualQuantityFl`, text: RULE-INV-040 (REQ-INV-122); ≤ 3 decimals, text: RULE-INV-017.
  The tracking flags come from ITEM-TRACKING-LOOKUP. The drawer's confirm validates the line and returns it to the form state.
- Post asks for no input. "At least one line" (RULE-INV-031) is also checked before Post is offered.
<!-- SUB:F3-SCR-INV-005:END -->

<!-- SUB:F3-SCR-INV-006:START traces=SCR-INV-006,UXD-INV-006,REQ-INV-013,REQ-INV-017,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-095,REQ-INV-122,AC-INV-098,API-INV-026,API-INV-028,API-INV-029,API-INV-030 -->
### F3 · SCR-INV-006 — Stock counts
- `countSearchSchema`: `docNo` ≤ 30, date range, `warehousePk`, `statusCode`.
- `countStartSchema`:
  - `warehousePk`: required when two or more warehouses are active, text: RULE-INV-008 (REQ-INV-017); null while one (REQ-INV-013).
  - `noteText`: ≤ 500.
- `scanSchema`:
  - `barcode` (≤ 64) or `variant`: one is required.
  - `countedQty`: ≥ 0 with ≤ 3 decimals, text: RULE-INV-047 (AC-INV-098).
  - `lotPk` and `countedMeasureQty`: required as in transfers, texts RULE-INV-037 and RULE-INV-040.
  The barcode input is numeric-keyboard friendly (`inputmode="numeric"`) and accepts a scanner's Enter as submit (REQ-INV-090).
- `countLineEditSchema`: `countedQty` ≥ 0 (RULE-INV-047), `countedMeasureQty` ≥ 0, `version`.
<!-- SUB:F3-SCR-INV-006:END -->

<!-- SUB:F3-SCR-INV-007:START traces=SCR-INV-007,UXD-INV-007,REQ-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-097,REQ-INV-098,REQ-INV-101,REQ-INV-102,REQ-INV-104,REQ-INV-105,REQ-INV-123,AC-INV-109,AC-INV-111,AC-INV-113,API-INV-034,API-INV-036,API-INV-037 -->
### F3 · SCR-INV-007 — Stock adjustments
- `adjustmentSearchSchema`: `docNo` ≤ 30, date range, `warehousePk`, `reasonCode` (an INV_ADJUSTMENT_REASON option), `statusCode`.
- `adjustmentFormSchema`:
  - `reasonCode`: required, an active option, text: RULE-INV-033 (AC-INV-113).
  - `warehousePk`: required on create when two or more are active, text: RULE-INV-008; null while one (REQ-INV-010, REQ-INV-013). Not editable after the first save.
  - `noteText`: ≤ 500.
  - `lines`: two lines with the same Variant and `lotNo` give a refinement, text: RULE-INV-048 (AC-INV-109).
- `adjustmentLineSchema`:
  - `variant`: required.
  - `quantityChange`: ≤ 3 decimals, text: RULE-INV-017. It must be ≠ 0 unless the Item is dual-quantity and `measureQtyChange` ≠ 0, text: RULE-INV-034 (AC-INV-111, REQ-INV-123).
  - `unitCost`: ≥ 0 with ≤ 4 decimals, text: RULE-INV-025; only on increase lines, optional (REQ-INV-102). Required when `reasonCode = OPENING_STOCK`, text: RULE-INV-026.
  - `lotNo`: ≤ 40.
  - `lotExpiryDate`: required with `lotNo` on an increase line of a lot-tracked Item, text: RULE-INV-035 (REQ-INV-105).
  - `lotNo` on a decrease line of a lot-tracked Item: required, text: RULE-INV-037.
  - `measureQtyChange`: ≤ 3 decimals, text: RULE-INV-017.
<!-- SUB:F3-SCR-INV-007:END -->

<!-- SUB:F3-SCR-INV-008:START traces=SCR-INV-008,UXD-INV-008,REQ-INV-108,REQ-INV-109,REQ-INV-116,API-INV-040,API-INV-042 -->
### F3 · SCR-INV-008 — Lots and near expiry
- `lotSearchSchema`: `variantPk`, `code` ≤ 40, `warehousePk`, `expiryFrom` ≤ `expiryTo`, `nearExpiryOnly`.
- `lotFormSchema`:
  - `expiryDate`: required; empty → «أدخل تاريخ انتهاء الصلاحية.» / "Enter the expiry date.".
  - `nameAr`, `nameEn`: ≤ 100.
  - `version`: required.
- An earlier or later expiry is allowed (REQ-INV-108).
<!-- SUB:F3-SCR-INV-008:END -->

<!-- SUB:F3-SCR-INV-009:START traces=SCR-INV-009,UXD-INV-009,UXD-INV-010,REQ-INV-124,REQ-INV-125,REQ-INV-128,REQ-INV-129,AC-INV-141,API-INV-043,API-INV-045 -->
### F3 · SCR-INV-009 — Item stock settings
- `itemSettingsSearchSchema`: `itemPk` (from UXD-INV-009) or `term` ≤ 100, `lotTracked`, `dualQuantity`.
- `itemSettingsFormSchema`:
  - `item`: required on a new record; empty → «اختر الصنف.» / "Choose the Item.".
  - `lotTrackedFl`: boolean, sent `false` while lotExpiry is off and the control is hidden (REQ-INV-128).
  - `dualQuantityFl`: boolean, likewise.
  - `measureUomCode`: required when `dualQuantityFl` is true, text: RULE-INV-041 (AC-INV-141).
  - `version`: nullable.
<!-- SUB:F3-SCR-INV-009:END -->

<!-- SUB:F3-SCR-INV-010:START traces=SCR-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-074,REQ-INV-105,REQ-INV-122,AC-INV-078,AC-INV-081,API-INV-047 -->
### F3 · SCR-INV-010 — Opening stock
- `openingStockSchema`:
  - `warehousePk`: required when two or more are active, text: RULE-INV-008.
  - `rows`: 1–1000 entries, text: RULE-INV-027 (AC-INV-078).
- `openingRowSchema`:
  - `rowNo`: integer ≥ 1.
  - `barcode`: required, ≤ 64; empty → «أدخل الباركود.» / "Enter the barcode." (v1 names a Variant by barcode only — ADR-INV-035).
  - `quantity`: > 0, text: RULE-INV-026; ≤ 3 decimals, text: RULE-INV-017.
  - `unitCost`: required and ≥ 0, text: RULE-INV-026 (REQ-INV-074, AC-INV-081); ≤ 4 decimals, text: RULE-INV-025.
  - `lotNo`: ≤ 40.
  - `lotExpiryDate`: required with `lotNo`, text: RULE-INV-035.
  - `measureQty`: ≥ 0 with ≤ 3 decimals, text: RULE-INV-017.
- Rows read from the sheet go through the same schema before posting. Rows that fail are listed by `rowNo` with their message, and nothing is sent until they pass (REQ-INV-072). Variant resolution is the server's (ADR-INV-035).
<!-- SUB:F3-SCR-INV-010:END -->

<!-- SUB:F3-SCR-INV-011:START traces=SCR-INV-011,REQ-INV-065,REQ-INV-118,REQ-INV-119,AC-INV-133,API-INV-049 -->
### F3 · SCR-INV-011 — Inventory settings
- `settingsSchema`:
  - `allowNegativeStockFl`: boolean.
  - `nearExpiryDays`: a whole number from 1 to 365; otherwise → «أدخل عدد أيام من 1 إلى 365.» / "Enter a number of days from 1 to 365." (REQ-INV-119, AC-INV-133). The saved value stays unchanged until the field passes.
<!-- SUB:F3-SCR-INV-011:END -->

<!-- SUB:F3-SCR-INV-012:START traces=SCR-INV-012,UXD-INV-011,REQ-INV-063,API-INV-050 -->
### F3 · SCR-INV-012 — Stock valuation
- `valuationSearchSchema`:
  - `variantPk` / `itemPk`: from UXD-INV-011.
  - `term`: ≤ 64, a barcode.
  - `nonZero`: boolean.
- No entry form.
<!-- SUB:F3-SCR-INV-012:END -->
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=SCR-INV-001,SCR-INV-002,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008,SCR-INV-009,SCR-INV-010,SCR-INV-011,SCR-INV-012,UXD-INV-001,UXD-INV-002,UXD-INV-003,UXD-INV-004,UXD-INV-005,UXD-INV-006,UXD-INV-007,UXD-INV-008,UXD-INV-009,UXD-INV-010,UXD-INV-011,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-010,REQ-INV-011,REQ-INV-012,REQ-INV-013,REQ-INV-014,REQ-INV-015,REQ-INV-016,REQ-INV-017,REQ-INV-018,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-029,REQ-INV-030,REQ-INV-031,REQ-INV-032,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-055,REQ-INV-056,REQ-INV-059,REQ-INV-061,REQ-INV-062,REQ-INV-063,REQ-INV-064,REQ-INV-065,REQ-INV-066,REQ-INV-067,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-075,REQ-INV-076,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-108,REQ-INV-109,REQ-INV-113,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-118,REQ-INV-119,REQ-INV-121,REQ-INV-122,REQ-INV-123,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,REQ-INV-137,AC-INV-006,AC-INV-010,AC-INV-012,AC-INV-013,AC-INV-014,AC-INV-015,AC-INV-017,AC-INV-028,AC-INV-032,AC-INV-033,AC-INV-034,AC-INV-035,AC-INV-036,AC-INV-037,AC-INV-044,AC-INV-062,AC-INV-065,AC-INV-069,AC-INV-071,AC-INV-072,AC-INV-076,AC-INV-077,AC-INV-079,AC-INV-080,AC-INV-082,AC-INV-083,AC-INV-085,AC-INV-086,AC-INV-087,AC-INV-089,AC-INV-092,AC-INV-094,AC-INV-095,AC-INV-096,AC-INV-097,AC-INV-099,AC-INV-100,AC-INV-101,AC-INV-102,AC-INV-104,AC-INV-105,AC-INV-107,AC-INV-108,AC-INV-110,AC-INV-112,AC-INV-114,AC-INV-115,AC-INV-116,AC-INV-121,AC-INV-122,AC-INV-128,AC-INV-129,AC-INV-130,AC-INV-131,AC-INV-132,AC-INV-135,AC-INV-138,AC-INV-140,AC-INV-142,AC-INV-143,AC-INV-145,AC-INV-150,AC-INV-151,AC-INV-153,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015,API-INV-016,API-INV-017,API-INV-018,API-INV-019,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-042,API-INV-043,API-INV-044,API-INV-045,API-INV-046,API-INV-047,API-INV-048,API-INV-049,API-INV-050,API-INV-051 -->
## F4 — Screens & Routes

**RF4.** The module registers one route tree under `/inventory`, with one lazy chunk per composite screen
(`react-router` lazy routes). Every route element is wrapped in a permission guard on its page's `VIEW`. Pages use
their FACADE only and never call a query. Open drawers (entry, line, detail) are search params of the route
(`entry`, `line`, `move`, `variant`): Back closes them, dismissing closes only the drawer, and a deep link opens
it. A drawer renders as a sibling of the page content, not inside it, and has no scroll region of its own. The
layout is RTL-first and responsive. The Inventory menu is built from the routes' guards and the context
(SEC-FE).

| SCR | Route(s) | Chunk | Guard (VIEW) |
|---|---|---|---|
| SCR-INV-001 | `/inventory/warehouses` (`?entry=new\|<id>`) | `inv-warehouses` | `PERM_INV_WAREHOUSES_VIEW` |
| SCR-INV-002 | `/inventory/branches` (`?entry=new\|<id>`) | `inv-branches` | `PERM_INV_BRANCHES_VIEW` |
| SCR-INV-003 | `/inventory/stock-balances` | `inv-stock-balances` | `PERM_INV_STOCK_BALANCES_VIEW` |
| SCR-INV-004 | `/inventory/stock-moves` (`?move=<id>`) | `inv-stock-moves` | `PERM_INV_STOCK_MOVES_VIEW` |
| SCR-INV-005 | `/inventory/transfers`, `/inventory/transfers/new`, `/inventory/transfers/:id` (`?line=new\|<key>`) | `inv-transfers` | `PERM_INV_TRANSFERS_VIEW` + two or more active warehouses |
| SCR-INV-006 | `/inventory/stock-counts`, `/inventory/stock-counts/new`, `/inventory/stock-counts/:id` (`?line=<lineId>`) | `inv-stock-counts` | `PERM_INV_COUNTS_VIEW` |
| SCR-INV-007 | `/inventory/stock-adjustments`, `/inventory/stock-adjustments/new`, `/inventory/stock-adjustments/:id` (`?line=new\|<key>`) | `inv-stock-adjustments` | `PERM_INV_ADJUSTMENTS_VIEW` |
| SCR-INV-008 | `/inventory/lots` (`?entry=<id>`) | `inv-lots` | `PERM_INV_LOTS_VIEW` + lotExpiry on |
| SCR-INV-009 | `/inventory/item-stock-settings` (`?entry=new\|<itemPk>`) | `inv-item-stock-settings` | `PERM_INV_ITEM_STOCK_SETTINGS_VIEW` + lotExpiry or dualQuantity on |
| SCR-INV-010 | `/inventory/opening-stock` (`?line=new\|<key>`) | `inv-opening-stock` (+ sheet reader, ADR-INV-035) | `PERM_INV_OPENING_STOCK_VIEW` |
| SCR-INV-011 | `/inventory/settings` | `inv-settings` | `PERM_INV_SETTINGS_VIEW` |
| SCR-INV-012 | `/inventory/stock-valuation` (`?variant=<id>`) | `inv-stock-valuation` | `PERM_INV_STOCK_VALUATION_VIEW` |

<!-- SUB:F4-SCR-INV-001:START traces=SCR-INV-001,REQ-INV-003,REQ-INV-004,REQ-INV-005,REQ-INV-006,REQ-INV-007,REQ-INV-008,REQ-INV-009,REQ-INV-015,REQ-INV-029,REQ-INV-030,REQ-INV-031,AC-INV-006,AC-INV-010,AC-INV-012,AC-INV-034,AC-INV-035,AC-INV-036,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009 -->
### WAREHOUSES-SCREEN — SCR-INV-001
- **Guard**: `PERM_INV_WAREHOUSES_VIEW`. The "New" action needs `_CREATE`. Save on an existing record, Reactivate and Set as default need `_UPDATE`; Deactivate needs `_DELETE`.
- **Facade**: WAREHOUSES-FACADE.
- **Cross-module**: none.
- **Composition**: container drawer. `WarehouseSearchPage`, with `WarehouseEntryDrawer` as its sibling, opened by `?entry`. Fields → none; branch selector → inline (one select, hidden while one active branch).
- **Saves**: ONE (Save → API-INV-003 or API-INV-004). Row actions are separate confirmed actions in the row menu: Deactivate API-INV-005, Reactivate API-INV-006, Set as default API-INV-007.
- **Components**: `WarehouseSearchForm`; `WarehouseResultTable`, where the branch column hides while one branch is active, a default badge and an active badge are shown, and a row link "Stock balances" goes to `/inventory/stock-balances?warehousePk=<id>`; `WarehouseEntryDrawer`.
- **Routes**: incoming `?branchPk=<id>` seeds the filter.
<!-- SUB:F4-SCR-INV-001:END -->

<!-- SUB:F4-SCR-INV-002:START traces=SCR-INV-002,REQ-INV-022,REQ-INV-023,REQ-INV-024,REQ-INV-025,REQ-INV-026,REQ-INV-027,REQ-INV-028,REQ-INV-032,AC-INV-028,AC-INV-032,AC-INV-033,AC-INV-037,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015 -->
### BRANCHES-SCREEN — SCR-INV-002
- **Guard**: `PERM_INV_BRANCHES_VIEW`; actions gated by `_CREATE` / `_UPDATE` / `_DELETE` as on SCR-INV-001.
- **Facade**: BRANCHES-FACADE.
- **Cross-module**: none.
- **Composition**: container drawer. `BranchSearchPage`, with `BranchEntryDrawer` as its sibling, opened by `?entry`. Fields → none.
- **Saves**: ONE (Save → API-INV-011 or API-INV-012). Row actions: Deactivate API-INV-013, Reactivate API-INV-014, Set as default API-INV-015.
- **Components**: `BranchSearchForm`; `BranchResultTable`, with a row link "Warehouses" to `/inventory/warehouses?branchPk=<id>`; `BranchEntryDrawer`.
<!-- SUB:F4-SCR-INV-002:END -->

<!-- SUB:F4-SCR-INV-003:START traces=SCR-INV-003,UXD-INV-001,UXD-INV-002,UXD-INV-003,REQ-INV-012,REQ-INV-035,REQ-INV-038,REQ-INV-039,REQ-INV-040,REQ-INV-041,REQ-INV-042,REQ-INV-113,REQ-INV-115,REQ-INV-121,REQ-INV-135,REQ-INV-137,AC-INV-015,AC-INV-044,AC-INV-129,AC-INV-135,AC-INV-151,AC-INV-153,API-INV-001,API-INV-008,API-INV-016,API-INV-017,API-INV-040 -->
### BALANCES-SCREEN — SCR-INV-003
- **Guard**: `PERM_INV_STOCK_BALANCES_VIEW`. The screen has no create, update or cancel action for any role (AC-INV-153).
- **Facade**: BALANCES-FACADE.
- **Cross-module**:
  - Variant filter → UXD-INV-001.
  - Item filter and Item name column → UXD-INV-002.
  - Measure unit → UXD-INV-003.
  - Warehouse label → read from the row (`warehouseCode`, `warehouseNameAr`, `warehouseNameEn`), for every role (ADR-INV-036).
- **Composition**: container page. Availability per warehouse → inline (`AvailabilitySummary` above the result when one Variant is filtered). No drawer. Submits: none.
- **Saves**: none (read-only inquiry).
- **Components**:
  - `BalanceSearchForm`: the Variant picker (UXD-INV-001), the Item picker (UXD-INV-002), a barcode field, the warehouse select (hidden while one; offered with warehouse VIEW), the lot select (lotExpiry on and lot VIEW), and "non-zero only".
  - `BalanceResultTable`: the warehouse column shows the row's warehouse code and name, and hides while one warehouse is active (AC-INV-015, AC-INV-044). The lot, expiry and `Expired` badge columns show only with lotExpiry on (AC-INV-129). The measure and unit columns show only with dualQuantity on (AC-INV-135, AC-INV-151). There is no cost column.
  - Row link "Moves" goes to `/inventory/stock-moves?variantPk&warehousePk[&lotPk]`.
- **Routes**: incoming `?warehousePk` or `?lotPk` seeds the filter.
<!-- SUB:F4-SCR-INV-003:END -->

<!-- SUB:F4-SCR-INV-004:START traces=SCR-INV-004,UXD-INV-004,REQ-INV-016,REQ-INV-055,REQ-INV-056,REQ-INV-075,REQ-INV-076,AC-INV-062,AC-INV-082,AC-INV-083,API-INV-001,API-INV-008,API-INV-018,API-INV-019,API-INV-040 -->
### MOVES-SCREEN — SCR-INV-004
- **Guard**: `PERM_INV_STOCK_MOVES_VIEW`.
- **Facade**: MOVES-FACADE.
- **Cross-module**: Variant filter → UXD-INV-004.
- **Composition**: container page. `MoveDetailDrawer` is a sibling opened by `?move`; it is read-only (→ none). Submits: none.
- **Saves**: none. Moves are never edited; the screen offers no edit or delete action (REQ-INV-055).
- **Components**:
  - `MoveSearchForm`.
  - `MoveResultTable`, newest first. The "Balance after move" column shows only while `showRunningBalance` (AC-INV-083). Document type labels come from INV_SOURCE_DOC_TYPE.
  - `MoveDetailDrawer`, which shows the source document type, identifier and number (AC-INV-062).
- **Routes**: incoming `?variantPk&warehousePk&lotPk`, `?sourceDocTypeCode&sourceDocRef` (one document exactly — from SCR-INV-005 and SCR-INV-007) or `?variantPk` seeds the filter.
<!-- SUB:F4-SCR-INV-004:END -->

<!-- SUB:F4-SCR-INV-005:START traces=SCR-INV-005,UXD-INV-005,REQ-INV-011,REQ-INV-014,REQ-INV-062,REQ-INV-078,REQ-INV-079,REQ-INV-080,REQ-INV-081,REQ-INV-082,REQ-INV-083,REQ-INV-084,REQ-INV-085,REQ-INV-086,REQ-INV-122,AC-INV-014,AC-INV-017,AC-INV-085,AC-INV-086,AC-INV-087,AC-INV-089,AC-INV-092,AC-INV-094,AC-INV-095,API-INV-001,API-INV-008,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-040,API-INV-044 -->
### TRANSFERS-SCREEN — SCR-INV-005
- **Guard**: `PERM_INV_TRANSFERS_VIEW`, and two or more active warehouses. Below two, the route renders the RULE-INV-016 message with a link to SCR-INV-001, and the menu hides the entry (REQ-INV-011, AC-INV-014). `/new` needs `_CREATE`. Save on `/:id` and Post need `_UPDATE`; Cancel needs `_DELETE`.
- **Facade**: TRANSFERS-FACADE.
- **Cross-module**: line Variant → UXD-INV-005.
- **Composition**: container page. The header (from / to warehouse, date, Advanced note) is inline. Lines → inline in a `TransferLinesPane` beneath the header. A line is added or changed in `TransferLineDrawer`, a sibling opened by `?line`. Its confirm returns the line to the form state and saves nothing; cancel discards the drawer's draft. Variant picker → inline: one combobox plus a barcode field inside the line drawer.
- **Saves**: ONE (Save → API-INV-022 on `/new`, API-INV-023 on `/:id`), sending header and lines in one call. Post (API-INV-024) and Cancel (API-INV-025) are document transitions offered only on a saved, clean DRAFT, so no action owns two calls. POSTED and CANCELLED render read-only (AC-INV-094).
- **Components**:
  - `TransferSearchForm`; `TransferResultTable`, newest first (AC-INV-095).
  - `TransferHeader`, `TransferLinesPane`, `TransferLineDrawer`. The lot select appears when the Item is lot-tracked and lotExpiry is on; measure appears when the Item is dual-quantity and dualQuantity is on.
  - `TransferActions` (Save · Post · Cancel).
  - On a POSTED entry, the link "Stock moves" goes to `/inventory/stock-moves?sourceDocTypeCode=INV_TRANSFER&sourceDocRef=<transferPk>`.
<!-- SUB:F4-SCR-INV-005:END -->

<!-- SUB:F4-SCR-INV-006:START traces=SCR-INV-006,UXD-INV-006,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-087,REQ-INV-088,REQ-INV-089,REQ-INV-090,REQ-INV-091,REQ-INV-092,REQ-INV-093,REQ-INV-094,REQ-INV-095,REQ-INV-096,REQ-INV-122,AC-INV-096,AC-INV-097,AC-INV-099,AC-INV-100,AC-INV-101,AC-INV-102,AC-INV-104,AC-INV-105,API-INV-001,API-INV-008,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-040,API-INV-044 -->
### COUNTS-SCREEN — SCR-INV-006
- **Guard**: `PERM_INV_COUNTS_VIEW`. "Start count" and `/new` need `_CREATE`. Scan, line change and Post need `_UPDATE`; Cancel needs `_DELETE`.
- **Facade**: COUNTS-FACADE.
- **Cross-module**: count line Variant → UXD-INV-006.
- **Composition**: container page. The header is inline and read-only after start. Lines → inline: a `CountScanBar` and stacked `CountLineCard`s beneath it. A line is changed or removed in `CountLineDrawer`, a sibling opened by `?line`. Variant picker → inline: it replaces the barcode field after an unknown barcode (AC-INV-101).
- **Saves**: ONE per action (ADR-INV-034). Each scan is one API-INV-029 call, and each drawer confirm is one API-INV-030 or API-INV-031 call. No action owns two calls. Start (API-INV-028), Post (API-INV-032) and Cancel (API-INV-033) are document transitions. POSTED and CANCELLED render read-only, with systemQty and differenceQty and a link to the resulting adjustment `/inventory/stock-adjustments/<adjustmentPk>` (AC-INV-102).
- **Layout (REQ-INV-090, AC-INV-100)**:
  - single column, `max-width: 100%`, no table and no horizontal scroll at 360 CSS px;
  - touch targets ≥ 44 px;
  - the barcode field is focused on load and after each scan;
  - each scan shows its result (Variant name and new counted total) as a non-blocking toast.
- **Components**:
  - `CountSearchForm`, `CountResultTable` (stacked cards below 600 px), newest first (AC-INV-105).
  - `CountStartForm`, shown on `/new` only when two or more warehouses are active; while one is active, "Start count" calls API-INV-028 directly (AC-INV-096).
  - `CountScanBar`, `CountLineCard`, `CountLineDrawer`, `CountActions` (Post · Cancel).
<!-- SUB:F4-SCR-INV-006:END -->

<!-- SUB:F4-SCR-INV-007:START traces=SCR-INV-007,UXD-INV-007,REQ-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-067,REQ-INV-097,REQ-INV-098,REQ-INV-099,REQ-INV-100,REQ-INV-101,REQ-INV-102,REQ-INV-103,REQ-INV-104,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-114,REQ-INV-123,AC-INV-013,AC-INV-107,AC-INV-108,AC-INV-110,AC-INV-112,AC-INV-114,AC-INV-115,AC-INV-116,AC-INV-128,AC-INV-150,API-INV-001,API-INV-008,API-INV-016,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-044 -->
### ADJUSTMENTS-SCREEN — SCR-INV-007
- **Guard**: `PERM_INV_ADJUSTMENTS_VIEW`. `/new` needs `_CREATE`. Save on `/:id` and Post need `_UPDATE`; Cancel needs `_DELETE`.
- **Facade**: ADJUSTMENTS-FACADE.
- **Cross-module**: line Variant → UXD-INV-007.
- **Composition**: container page. The header (reason, warehouse hidden while one, Advanced note) is inline. Lines → inline in `AdjustmentLinesPane` beneath the header. A line is added or changed in `AdjustmentLineDrawer`, a sibling opened by `?line`, which returns the line to the form state. Variant picker → inline inside the line drawer.
- **Saves**: ONE (Save → API-INV-036 on `/new`, API-INV-037 on `/:id`). Post (API-INV-038) and Cancel (API-INV-039) are offered only on a saved, clean DRAFT. POSTED and CANCELLED render read-only (AC-INV-115), with line values and the total.
- **Components**:
  - `AdjustmentSearchForm`; `AdjustmentResultTable`, newest first (AC-INV-116).
  - `AdjustmentHeader`, `AdjustmentLinesPane`, `AdjustmentLineDrawer`. Lot number and expiry columns are absent with lotExpiry off (AC-INV-150). The unit cost is behind "Advanced" on increase lines and is opened by a cost error.
  - `AdjustmentActions` (Save · Post · Cancel).
  - On a POSTED entry, the link "Stock moves" goes to `/inventory/stock-moves?sourceDocTypeCode=INV_ADJUSTMENT&sourceDocRef=<stockAdjustmentPk>`.
- **Routes**: `/new?reason=EXPIRY_WRITE_OFF&lotPk&warehousePk` seeds the reason and one decrease line from WRITE-OFF-LOT-QUERY and WRITE-OFF-BALANCE-QUERY (AC-INV-128). The seed fills form state only, and nothing is saved until Save.
<!-- SUB:F4-SCR-INV-007:END -->

<!-- SUB:F4-SCR-INV-008:START traces=SCR-INV-008,UXD-INV-008,REQ-INV-108,REQ-INV-109,REQ-INV-114,REQ-INV-115,REQ-INV-116,REQ-INV-117,REQ-INV-134,AC-INV-121,AC-INV-122,AC-INV-129,AC-INV-130,AC-INV-131,AC-INV-150,API-INV-001,API-INV-008,API-INV-040,API-INV-041,API-INV-042 -->
### LOTS-SCREEN — SCR-INV-008
- **Guard**: `PERM_INV_LOTS_VIEW`, with lotExpiry on (REQ-INV-134). Save expiry needs `_UPDATE` (Owner); for others the entry drawer is read-only.
- **Facade**: LOTS-FACADE.
- **Cross-module**: Variant filter and Variant label → UXD-INV-008.
- **Composition**: container drawer. `LotSearchPage`, with `LotEntryDrawer` as its sibling, opened by `?entry`. Fields → none.
- **Saves**: ONE (Save expiry → API-INV-042). Write off is navigation, not a save.
- **Components**:
  - `LotSearchForm` with a "Near expiry only" toggle (REQ-INV-116); the notification link opens `/inventory/lots?nearExpiryOnly=true` (REQ-INV-117).
  - `LotResultTable`, ordered by expiry: days left, plus an `Expired` badge «منتهية الصلاحية» / "Expired" (AC-INV-129).
  - Row actions:
    - "Write off", on expired rows with on-hand, goes to `/inventory/stock-adjustments/new?reason=EXPIRY_WRITE_OFF&lotPk&warehousePk`;
    - "Stock balances" goes to `/inventory/stock-balances?lotPk[&warehousePk]`.
  - `LotEntryDrawer`.
<!-- SUB:F4-SCR-INV-008:END -->

<!-- SUB:F4-SCR-INV-009:START traces=SCR-INV-009,UXD-INV-009,UXD-INV-010,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,AC-INV-138,AC-INV-140,AC-INV-142,AC-INV-143,AC-INV-145,API-INV-008,API-INV-043,API-INV-044,API-INV-045 -->
### ITEM-SETTINGS-SCREEN — SCR-INV-009
- **Guard**: `PERM_INV_ITEM_STOCK_SETTINGS_VIEW`, with lotExpiry or dualQuantity on. "New" needs `_CREATE`; Save on an existing record needs `_UPDATE`.
- **Facade**: ITEM-SETTINGS-FACADE.
- **Cross-module**: Item pick → UXD-INV-009; measure unit → UXD-INV-010.
- **Composition**: container drawer. `ItemSettingsSearchPage`, with `ItemSettingsEntryDrawer` as its sibling, opened by `?entry`. Fields → none. Item picker → inline (one combobox, new records only).
- **Saves**: ONE (Save → API-INV-045).
- **Components**:
  - `ItemSettingsSearchForm`, `ItemSettingsResultTable`.
  - `ItemSettingsEntryDrawer`. "Tracked by lot" shows only with lotExpiry on (REQ-INV-134). "Dual quantity" and "Measure unit" show only with dualQuantity on (REQ-INV-135). The measure unit is required when dual.
<!-- SUB:F4-SCR-INV-009:END -->

<!-- SUB:F4-SCR-INV-010:START traces=SCR-INV-010,REQ-INV-013,REQ-INV-017,REQ-INV-018,REQ-INV-064,REQ-INV-070,REQ-INV-071,REQ-INV-072,REQ-INV-073,REQ-INV-074,REQ-INV-105,REQ-INV-106,REQ-INV-107,REQ-INV-122,AC-INV-076,AC-INV-077,AC-INV-079,AC-INV-080,API-INV-001,API-INV-008,API-INV-046,API-INV-047 -->
### OPENING-STOCK-SCREEN — SCR-INV-010
- **Guard**: `PERM_INV_OPENING_STOCK_VIEW`. Post needs `_CREATE`.
- **Facade**: OPENING-FACADE.
- **Cross-module**: none (ADR-INV-035).
- **Composition**: container page. The header (warehouse, hidden while one) is inline. Rows → inline in `OpeningRowsPane` beneath it, which a loaded sheet fills. A row is added or changed in `OpeningRowDrawer`, a sibling opened by `?line`, which returns the row to the form state. The row failures list is inline above the pane.
- **Saves**: ONE (Post → API-INV-047, all rows in one call). Download sheet (API-INV-046) is a file read, not a save.
- **Components**:
  - `OpeningSheetBar`: "Download sheet", and "Load sheet", a file input that reads xlsx in the browser through the lazy sheet reader (ADR-INV-035).
  - `OpeningRowsPane`, showing row number, barcode, quantity, unit cost, lot and expiry (lotExpiry on) and measure (dualQuantity on), plus totals of rows and quantity.
  - `OpeningRowFailures`.
  - `OpeningActions` (Post).
  After a successful post the route goes to `/inventory/stock-adjustments/<stockAdjustmentPk>` (AC-INV-076).
<!-- SUB:F4-SCR-INV-010:END -->

<!-- SUB:F4-SCR-INV-011:START traces=SCR-INV-011,REQ-INV-065,REQ-INV-066,REQ-INV-118,REQ-INV-119,AC-INV-071,AC-INV-072,AC-INV-132,API-INV-008,API-INV-048,API-INV-049 -->
### SETTINGS-SCREEN — SCR-INV-011
- **Guard**: `PERM_INV_SETTINGS_VIEW`; Save needs `_UPDATE`.
- **Facade**: SETTINGS-FACADE.
- **Cross-module**: none.
- **Composition**: container page. Fields → none.
- **Saves**: ONE (Save → API-INV-049).
- **Components**: `InventorySettingsForm`, with an "Allow negative stock" switch and the "Near-expiry horizon (days)" number field, shown only with lotExpiry on.
<!-- SUB:F4-SCR-INV-011:END -->

<!-- SUB:F4-SCR-INV-012:START traces=SCR-INV-012,UXD-INV-011,REQ-INV-059,REQ-INV-061,REQ-INV-063,AC-INV-065,AC-INV-069,API-INV-008,API-INV-050,API-INV-051 -->
### VALUATION-SCREEN — SCR-INV-012
- **Guard**: `PERM_INV_STOCK_VALUATION_VIEW` (Owner).
- **Facade**: VALUATION-FACADE.
- **Cross-module**: Variant and Item filters → UXD-INV-011.
- **Composition**: container page. `ValuationDetailDrawer` is a sibling opened by `?variant`; it is read-only (→ none). Submits: none.
- **Saves**: none (read-only inquiry).
- **Components**:
  - `ValuationSearchForm`.
  - `ValuationResultTable`, with the grand total row from `grandTotalValue`.
  - `ValuationDetailDrawer`, with the link "Stock moves" to `/inventory/stock-moves?variantPk=<id>`.
<!-- SUB:F4-SCR-INV-012:END -->
<!-- PHASE:F4:END -->

<!-- PHASE:SEC-FE:START traces=SCR-INV-001,SCR-INV-002,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008,SCR-INV-009,SCR-INV-010,SCR-INV-011,SCR-INV-012,REQ-INV-011,REQ-INV-015,REQ-INV-132,REQ-INV-133,REQ-INV-134,REQ-INV-135,REQ-INV-136,REQ-INV-137,REQ-INV-138,AC-INV-014,AC-INV-018,AC-INV-150,AC-INV-152,AC-INV-153,AC-INV-154,API-INV-008 -->
## SEC-FE

**RF5, frontend half.** Permission names are the backend registry's (`PERM_<PAGE_CODE>_<ACTION>`, registry-exec-be
"Endpoints"), read from the session (ADR-INV-033) and not redeclared. The server is the authority: every INV
operation is authorized by the platform layer (REQ-INV-138). The UI only hides what the user cannot do:
- **Navigation guard**: no `VIEW` → the route renders the unauthorized view, and the menu entry is absent.
- **Forbidden responses**: INV-403 shows its localized catalogue message «ليست لديك صلاحية لهذا الإجراء.» /
  "You do not have permission for this action." (AC-INV-154). It never shows a raw status.
- **Flags**: capability flags come from the context (API-INV-008), which reads FeatureFlags on the server. No
  frontend code reads the activity or the activity preset (REQ-INV-132, REQ-INV-133). A lint rule in the module
  forbids importing the activity preset.

| SCR | Guard (no VIEW → unauthorized) | no CREATE | no UPDATE | no DELETE | Flag / count gate |
|---|---|---|---|---|---|
| SCR-INV-001 | `PERM_INV_WAREHOUSES_VIEW` | "New" hidden | entry read-only; Reactivate and Set as default hidden | Deactivate hidden | branch field, filter and column hidden while one active branch |
| SCR-INV-002 | `PERM_INV_BRANCHES_VIEW` | "New" hidden | entry read-only; Reactivate and Set as default hidden | Deactivate hidden | — |
| SCR-INV-003 | `PERM_INV_STOCK_BALANCES_VIEW` | not applicable | not applicable | not applicable | warehouse filter and column hidden while one (AC-INV-015); lot hidden with lotExpiry off; measure hidden with dualQuantity off |
| SCR-INV-004 | `PERM_INV_STOCK_MOVES_VIEW` | not applicable | not applicable | not applicable | as SCR-INV-003 |
| SCR-INV-005 | `PERM_INV_TRANSFERS_VIEW` | "New" hidden | entry read-only; Save and Post hidden | Cancel hidden | menu entry and route only with two or more active warehouses (AC-INV-014, AC-INV-018) |
| SCR-INV-006 | `PERM_INV_COUNTS_VIEW` | "Start count" hidden | scan bar, line drawer and Post hidden (read-only) | Cancel hidden | warehouse selector hidden while one |
| SCR-INV-007 | `PERM_INV_ADJUSTMENTS_VIEW` | "New" hidden | entry read-only; Save and Post hidden | Cancel hidden | warehouse hidden while one; lot hidden with lotExpiry off (AC-INV-150) |
| SCR-INV-008 | `PERM_INV_LOTS_VIEW` | not applicable | entry read-only (Save expiry hidden) | not applicable | menu entry and route only with lotExpiry on (AC-INV-150) |
| SCR-INV-009 | `PERM_INV_ITEM_STOCK_SETTINGS_VIEW` | "New" hidden | entry read-only | not applicable | route with lotExpiry or dualQuantity on; each control with its flag |
| SCR-INV-010 | `PERM_INV_OPENING_STOCK_VIEW` | Post and Load sheet hidden (download stays) | not applicable | not applicable | lot, expiry and measure columns with their flags |
| SCR-INV-011 | `PERM_INV_SETTINGS_VIEW` | not applicable | form read-only | not applicable | horizon with lotExpiry on |
| SCR-INV-012 | `PERM_INV_STOCK_VALUATION_VIEW` | not applicable | not applicable | not applicable | — |

**Inventory menu.** Each entry is shown when the user holds the screen's `VIEW` and its gate passes. With the
seeded role presets (REQ-INV-136, REQ-INV-137) this gives the following:
- Warehouse role: stock balances, stock moves, counts, adjustments, lots (lotExpiry on) and transfers (two or more
  active warehouses). Opening stock, stock valuation and inventory settings are not shown (AC-INV-152).
- Seller/Cashier: stock balances only (AC-INV-153).
- Owner: every entry its gates allow.
The menu re-reads INV-CONTEXT after any warehouse or branch change, so a second warehouse reveals the transfer
entry with no re-setup (REQ-INV-015).
<!-- PHASE:SEC-FE:END -->

<!-- PHASE:ALIGN-FE:START traces=SCR-INV-001,SCR-INV-002,SCR-INV-003,SCR-INV-004,SCR-INV-005,SCR-INV-006,SCR-INV-007,SCR-INV-008,SCR-INV-009,SCR-INV-010,SCR-INV-011,SCR-INV-012,UXD-INV-001,UXD-INV-002,UXD-INV-003,UXD-INV-004,UXD-INV-005,UXD-INV-006,UXD-INV-007,UXD-INV-008,UXD-INV-009,UXD-INV-010,UXD-INV-011,API-INV-001,API-INV-002,API-INV-003,API-INV-004,API-INV-005,API-INV-006,API-INV-007,API-INV-008,API-INV-009,API-INV-010,API-INV-011,API-INV-012,API-INV-013,API-INV-014,API-INV-015,API-INV-016,API-INV-017,API-INV-018,API-INV-019,API-INV-020,API-INV-021,API-INV-022,API-INV-023,API-INV-024,API-INV-025,API-INV-026,API-INV-027,API-INV-028,API-INV-029,API-INV-030,API-INV-031,API-INV-032,API-INV-033,API-INV-034,API-INV-035,API-INV-036,API-INV-037,API-INV-038,API-INV-039,API-INV-040,API-INV-041,API-INV-042,API-INV-043,API-INV-044,API-INV-045,API-INV-046,API-INV-047,API-INV-048,API-INV-049,API-INV-050,API-INV-051 -->
## ALIGN-FE

Each row names the check that backs it. Its mark is the analyze report's result for that check, copied in by
the orchestrator. A clause the report says examined nothing is written `— examined nothing`.

```
ALIGN — INV v1
row           backing check       assertion                                                                                         mark
SCREENS       orphans             every SCR is referenced by a plan block                                                           (stamped from the analyze report)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once                            (stamped from the analyze report)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it           (stamped from the analyze report)
READS         ux-reads-spec       every read a screen binds is an operation of api-spec-inv.yaml, and the plan names the document its mock server serves   (stamped from the analyze report)
UXD           orphans             every UXD is cited by a plan block — this is where a UX decision closes                           (stamped from the analyze report)
TRACES        traces              every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD          (stamped from the analyze report)
API           traces              every API this plan cites is an operation of api-spec-inv.yaml — never a line of the backend plan's prose   (stamped from the analyze report)
FOREIGN       xref-surface        every reference to another module's surface resolves in that module's own artifacts               (stamped from the analyze report)
REGISTRY      registry-agree      every UXD and SCR defined here is in the stage registry, and nothing else is                      (stamped from the analyze report)
LANGUAGES     languages           labels and messages in ar + en                                                                    (stamped from the analyze report)
MARKERS       markers             the parser reports no structural or semantic error for this track and plan                       (stamped from the analyze report)
DECISIONS     refs-exist          every ADR this plan cites exists on disk in analysis/decisions/INV/                              (stamped from the analyze report)
COVERAGE      (the report)        the clauses the analyze report lists as having examined nothing — verbatim, or `none`            (stamped from the analyze report)
```

```yaml name=self-check
findings: 0
clean: true
```

**Operations coverage** (operation │ API │ SCR action │ route │ status):

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| search warehouses | API-INV-001 | SCR-INV-001 search; warehouse lookup on SCR-INV-003 … 008, 010 | `/inventory/warehouses` | ✓ |
| read warehouse | API-INV-002 | SCR-INV-001 entry (edit); label fallback | `/inventory/warehouses?entry=<id>` | ✓ |
| create warehouse | API-INV-003 | SCR-INV-001 Save (new) | `/inventory/warehouses?entry=new` | ✓ |
| update warehouse | API-INV-004 | SCR-INV-001 Save (edit) | `/inventory/warehouses?entry=<id>` | ✓ |
| deactivate warehouse | API-INV-005 | SCR-INV-001 row Deactivate | `/inventory/warehouses` | ✓ |
| reactivate warehouse | API-INV-006 | SCR-INV-001 row Reactivate | `/inventory/warehouses` | ✓ |
| set as default warehouse | API-INV-007 | SCR-INV-001 row Set as default | `/inventory/warehouses` | ✓ |
| read inventory context | API-INV-008 | every SCR init; menu | `/inventory/*` | ✓ |
| search branches | API-INV-009 | SCR-INV-002 search; branch lookup on SCR-INV-001 | `/inventory/branches` | ✓ |
| read branch | API-INV-010 | SCR-INV-002 entry (edit) | `/inventory/branches?entry=<id>` | ✓ |
| create branch | API-INV-011 | SCR-INV-002 Save (new) | `/inventory/branches?entry=new` | ✓ |
| update branch | API-INV-012 | SCR-INV-002 Save (edit) | `/inventory/branches?entry=<id>` | ✓ |
| deactivate branch | API-INV-013 | SCR-INV-002 row Deactivate | `/inventory/branches` | ✓ |
| reactivate branch | API-INV-014 | SCR-INV-002 row Reactivate | `/inventory/branches` | ✓ |
| set as default branch | API-INV-015 | SCR-INV-002 row Set as default | `/inventory/branches` | ✓ |
| search stock balances | API-INV-016 | SCR-INV-003 search; SCR-INV-007 write-off seed | `/inventory/stock-balances` | ✓ |
| read availability of a Variant | API-INV-017 | SCR-INV-003 availability summary | `/inventory/stock-balances?variantPk=<id>` | ✓ |
| search stock moves | API-INV-018 | SCR-INV-004 search | `/inventory/stock-moves` | ✓ |
| read stock move | API-INV-019 | SCR-INV-004 move detail | `/inventory/stock-moves?move=<id>` | ✓ |
| search transfers | API-INV-020 | SCR-INV-005 search | `/inventory/transfers` | ✓ |
| read transfer | API-INV-021 | SCR-INV-005 entry | `/inventory/transfers/:id` | ✓ |
| create transfer | API-INV-022 | SCR-INV-005 Save (new) | `/inventory/transfers/new` | ✓ |
| update transfer | API-INV-023 | SCR-INV-005 Save (edit) | `/inventory/transfers/:id` | ✓ |
| post transfer | API-INV-024 | SCR-INV-005 Post | `/inventory/transfers/:id` | ✓ |
| cancel transfer | API-INV-025 | SCR-INV-005 Cancel | `/inventory/transfers/:id` | ✓ |
| search stock counts | API-INV-026 | SCR-INV-006 search | `/inventory/stock-counts` | ✓ |
| read stock count | API-INV-027 | SCR-INV-006 entry | `/inventory/stock-counts/:id` | ✓ |
| create stock count | API-INV-028 | SCR-INV-006 Start count | `/inventory/stock-counts/new` | ✓ |
| add counted quantity | API-INV-029 | SCR-INV-006 scan / add line | `/inventory/stock-counts/:id` | ✓ |
| change counted line | API-INV-030 | SCR-INV-006 line drawer confirm | `/inventory/stock-counts/:id?line=<lineId>` | ✓ |
| remove counted line | API-INV-031 | SCR-INV-006 line drawer remove | `/inventory/stock-counts/:id?line=<lineId>` | ✓ |
| post stock count | API-INV-032 | SCR-INV-006 Post | `/inventory/stock-counts/:id` | ✓ |
| cancel stock count | API-INV-033 | SCR-INV-006 Cancel | `/inventory/stock-counts/:id` | ✓ |
| search stock adjustments | API-INV-034 | SCR-INV-007 search | `/inventory/stock-adjustments` | ✓ |
| read stock adjustment | API-INV-035 | SCR-INV-007 entry | `/inventory/stock-adjustments/:id` | ✓ |
| create stock adjustment | API-INV-036 | SCR-INV-007 Save (new) | `/inventory/stock-adjustments/new` | ✓ |
| update stock adjustment | API-INV-037 | SCR-INV-007 Save (edit) | `/inventory/stock-adjustments/:id` | ✓ |
| post stock adjustment | API-INV-038 | SCR-INV-007 Post | `/inventory/stock-adjustments/:id` | ✓ |
| cancel stock adjustment | API-INV-039 | SCR-INV-007 Cancel | `/inventory/stock-adjustments/:id` | ✓ |
| search lots | API-INV-040 | SCR-INV-008 search / near-expiry view; lot lookup on SCR-INV-003 … 007 | `/inventory/lots` | ✓ |
| read lot | API-INV-041 | SCR-INV-008 entry; SCR-INV-007 write-off seed | `/inventory/lots?entry=<id>` | ✓ |
| correct lot expiry | API-INV-042 | SCR-INV-008 Save expiry | `/inventory/lots?entry=<id>` | ✓ |
| search Item stock settings | API-INV-043 | SCR-INV-009 search | `/inventory/item-stock-settings` | ✓ |
| read Item stock settings | API-INV-044 | SCR-INV-009 entry; line tracking on SCR-INV-005 … 007 | `/inventory/item-stock-settings?entry=<itemPk>` | ✓ |
| save Item stock settings | API-INV-045 | SCR-INV-009 Save | `/inventory/item-stock-settings?entry=<itemPk>` | ✓ |
| download opening stock sheet | API-INV-046 | SCR-INV-010 Download sheet | `/inventory/opening-stock` | ✓ |
| post opening stock | API-INV-047 | SCR-INV-010 Post | `/inventory/opening-stock` | ✓ |
| read inventory settings | API-INV-048 | SCR-INV-011 load | `/inventory/settings` | ✓ |
| update inventory settings | API-INV-049 | SCR-INV-011 Save | `/inventory/settings` | ✓ |
| search stock valuation | API-INV-050 | SCR-INV-012 search | `/inventory/stock-valuation` | ✓ |
| read stock valuation of a Variant | API-INV-051 | SCR-INV-012 Variant detail | `/inventory/stock-valuation?variant=<id>` | ✓ |

51 / 51 operations have a screen action and a route. No open item: the warehouse label for a Seller/Cashier on
SCR-INV-003 is read from the row (ADR-INV-036, closed at gate-analysis revise 1).
<!-- PHASE:ALIGN-FE:END -->

## Hand-off

- The toolkit splits the plan and `registry-exec-fe-inv.md` into `/frontend-execution/` after the P4 verdict.
- The implementer reads the plan in phase order (F1 → F2 → F3 → F4 → SEC-FE → ALIGN-FE), takes intent from
  `ui-ux-spec-inv.md` and shapes from `api-spec-inv.yaml`. A mock server serves that document until the backend is
  delivered; the CAT, `mdl` and session fixtures come from ADR-INV-033.
- The implementer adds no route, component, permission or field that does not trace to an F-block. A gap is an
  ADR.
