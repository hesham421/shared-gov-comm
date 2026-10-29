# Frontend execution plan — Catalog / الكتالوج (CAT) — v2

Module : CAT · Version : v2 · Stage : P3.2 Part B · Track : frontend · Plan : exec · Profile : commerce
Stack  : `react-ts-vite` · routing `react-router` · server state `tanstack-query` · forms `react-hook-form` · validation `zod` · state `zustand` · one lazy chunk per composite screen
Bound to: `api-spec-cat.yaml` (OpenAPI 3.1.0, 56 operations, base path `/api/v1/cat`, bearer JWT). The frontend executor's mock server serves the same document until the backend is delivered.
Design : `ui-ux-spec-cat.md` (SCR-CAT-001 … 006, UXD-CAT-001 … 003) · `flow-diagram-cat.md`
Decisions: ADR-CAT-049 (Items: one Save owning ordered calls) · ADR-CAT-050 (display data served by the given) · ADR-CAT-051 (API coverage and binding notes); earlier: ADR-CAT-043, ADR-CAT-044, ADR-CAT-048; gate-analysis revise: ADR-CAT-056 (files through the given FILE endpoints), ADR-CAT-059 (reactivation invariants), ADR-CAT-060 (unit used by barcodes); second gate-analysis revise: ADR-CAT-063 (inactive parent refuses child writes), ADR-CAT-064 (layout keeps its scale code length), ADR-CAT-065 (CAT lookup keys served through API-CAT-052); third gate-analysis revise: ADR-CAT-068 (three repairs stay allowed on an inactive Item), ADR-CAT-069 (reactivation of Categories, Variant attributes and values); eighth gate-analysis revise: ADR-CAT-076 (image type and size held by the FILE category CAT_ITEM_IMAGE); ninth gate-analysis revise: ADR-CAT-077 (Category changes publish catalogue-change events); tenth gate-analysis revise: ADR-CAT-078 (the barcode label screen generates the missing internal barcode itself); eleventh gate-analysis revise: ADR-CAT-079 (a label shows only an active GTIN or INTERNAL barcode in the base unit), ADR-CAT-080 (a Bundle component quantity is in the component Item's base unit)

This is a delta run, but the current state holds no earlier frontend plan, so every block below is ADDED. The plan says WHAT each screen reads, validates and renders. Method, path, request and response schema, paging and envelope are read in `api-spec-cat.yaml` by `x-api-id`, not copied here.

## 3.0 Binding to the API document

Reconciliation against the SRS (run once, before any phase):
- **REQ → operation**: 147 of 148 REQs bind to at least one operation that a CAT screen calls (block below). REQ-CAT-115 (storefront read) is served by `API-CAT-046`, which no CAT screen calls; SFR consumes it (ADR-CAT-051). No REQ needs an operation the document lacks. Lookup values (REQ-CAT-073, 074, 076) are served by CAT's own read `API-CAT-052` through `OwnedLookups` (UXD-CAT-001 — ADR-CAT-065), and capability flags (REQ-CAT-011 … 015) by the given (UXD-CAT-002 — ADR-CAT-050).
- **Operation → REQ**: every one of the 56 operations carries `x-traces` to at least one REQ, so no operation is unknown. Two operations (`API-CAT-011`, `API-CAT-046`) are not called by a CAT screen (ADR-CAT-051).
- **Envelope**: success bodies are `ApiResponse<T>` (`data`, `code`, `messageAr`, `messageEn`). Errors are `LocalizedError` (`code`, `messageAr`, `messageEn`). Paged reads (`x-paginated`) take `page` / `size`. Two binary bodies exist: the import sheet (xlsx, `API-CAT-048`) and the label document (PDF, `API-CAT-050`).
- **Platform-standard codes** (ADR-CAT-044) map to no RULE and are routed generically: `CAT-400-REQUEST-INVALID` → generic message · `CAT-401-UNAUTHENTICATED` → login · `CAT-403-FORBIDDEN` → unauthorized view (or a hidden affordance for an auxiliary read — ADR-CAT-051 §4) · `CAT-404-NOT-FOUND` → not-found state · `CAT-409-STALE-VERSION` → stale-version banner with Reload · a rejection of the given FILE upload (step 1 of an image or an import — ADR-CAT-056) → inline on the file input, showing the FILE envelope's messageAr / messageEn · `CAT-500-SERVER-ERROR` → generic message with Try again. In every case the text is the envelope's `messageAr` / `messageEn`.

```yaml name=api-surface
mock: api-spec-cat.yaml
bindings:
  - {req: REQ-CAT-001, api: [API-CAT-002, API-CAT-007]}
  - {req: REQ-CAT-002, api: [API-CAT-002]}
  - {req: REQ-CAT-003, api: [API-CAT-038]}
  - {req: REQ-CAT-004, api: [API-CAT-003]}
  - {req: REQ-CAT-005, api: [API-CAT-004]}
  - {req: REQ-CAT-006, api: [API-CAT-005]}
  - {req: REQ-CAT-007, api: [API-CAT-001, API-CAT-003]}
  - {req: REQ-CAT-008, api: [API-CAT-001, API-CAT-003, API-CAT-021, API-CAT-023, API-CAT-024, API-CAT-053]}
  - {req: REQ-CAT-009, api: [API-CAT-001, API-CAT-003]}
  - {req: REQ-CAT-010, api: [API-CAT-002]}
  - {req: REQ-CAT-011, api: [API-CAT-001, API-CAT-006]}
  - {req: REQ-CAT-012, api: [API-CAT-006, API-CAT-025]}
  - {req: REQ-CAT-013, api: [API-CAT-001, API-CAT-006, API-CAT-017]}
  - {req: REQ-CAT-014, api: [API-CAT-006, API-CAT-025, API-CAT-038, API-CAT-041]}
  - {req: REQ-CAT-015, api: [API-CAT-001, API-CAT-017, API-CAT-038]}
  - {req: REQ-CAT-016, api: [API-CAT-001, API-CAT-006, API-CAT-017, API-CAT-025, API-CAT-029]}
  - {req: REQ-CAT-017, api: [API-CAT-006]}
  - {req: REQ-CAT-018, api: [API-CAT-006]}
  - {req: REQ-CAT-019, api: [API-CAT-006]}
  - {req: REQ-CAT-020, api: [API-CAT-017]}
  - {req: REQ-CAT-021, api: [API-CAT-051]}
  - {req: REQ-CAT-022, api: [API-CAT-051]}
  - {req: REQ-CAT-023, api: [API-CAT-051]}
  - {req: REQ-CAT-024, api: [API-CAT-001]}
  - {req: REQ-CAT-025, api: [API-CAT-001]}
  - {req: REQ-CAT-026, api: [API-CAT-001]}
  - {req: REQ-CAT-027, api: [API-CAT-001]}
  - {req: REQ-CAT-028, api: [API-CAT-001]}
  - {req: REQ-CAT-029, api: [API-CAT-002, API-CAT-007]}
  - {req: REQ-CAT-030, api: [API-CAT-001, API-CAT-003]}
  - {req: REQ-CAT-031, api: [API-CAT-001, API-CAT-003]}
  - {req: REQ-CAT-032, api: [API-CAT-001]}
  - {req: REQ-CAT-033, api: [API-CAT-001, API-CAT-006]}
  - {req: REQ-CAT-034, api: [API-CAT-001, API-CAT-003, API-CAT-008]}
  - {req: REQ-CAT-035, api: [API-CAT-001, API-CAT-006, API-CAT-015]}
  - {req: REQ-CAT-036, api: [API-CAT-048]}
  - {req: REQ-CAT-037, api: [API-CAT-049]}
  - {req: REQ-CAT-038, api: [API-CAT-049]}
  - {req: REQ-CAT-039, api: [API-CAT-049]}
  - {req: REQ-CAT-040, api: [API-CAT-049]}
  - {req: REQ-CAT-041, api: [API-CAT-049]}
  - {req: REQ-CAT-042, api: [API-CAT-025]}
  - {req: REQ-CAT-043, api: [API-CAT-027]}
  - {req: REQ-CAT-044, api: [API-CAT-028]}
  - {req: REQ-CAT-045, api: [API-CAT-029]}
  - {req: REQ-CAT-046, api: [API-CAT-030]}
  - {req: REQ-CAT-047, api: [API-CAT-031]}
  - {req: REQ-CAT-048, api: [API-CAT-026, API-CAT-041]}
  - {req: REQ-CAT-049, api: [API-CAT-006]}
  - {req: REQ-CAT-050, api: [API-CAT-007, API-CAT-009]}
  - {req: REQ-CAT-051, api: [API-CAT-006]}
  - {req: REQ-CAT-052, api: [API-CAT-006]}
  - {req: REQ-CAT-053, api: [API-CAT-009]}
  - {req: REQ-CAT-054, api: [API-CAT-028, API-CAT-031]}
  - {req: REQ-CAT-055, api: [API-CAT-025, API-CAT-027, API-CAT-029, API-CAT-030]}
  - {req: REQ-CAT-056, api: [API-CAT-001, API-CAT-017]}
  - {req: REQ-CAT-057, api: [API-CAT-017]}
  - {req: REQ-CAT-058, api: [API-CAT-002]}
  - {req: REQ-CAT-059, api: [API-CAT-001, API-CAT-017]}
  - {req: REQ-CAT-060, api: [API-CAT-001, API-CAT-017]}
  - {req: REQ-CAT-061, api: [API-CAT-006]}
  - {req: REQ-CAT-062, api: [API-CAT-004, API-CAT-009]}
  - {req: REQ-CAT-063, api: [API-CAT-003]}
  - {req: REQ-CAT-064, api: [API-CAT-021]}
  - {req: REQ-CAT-065, api: [API-CAT-023]}
  - {req: REQ-CAT-066, api: [API-CAT-022, API-CAT-024]}
  - {req: REQ-CAT-067, api: [API-CAT-022, API-CAT-039]}
  - {req: REQ-CAT-068, api: [API-CAT-040]}
  - {req: REQ-CAT-069, api: [API-CAT-021, API-CAT-023]}
  - {req: REQ-CAT-070, api: [API-CAT-021, API-CAT-023]}
  - {req: REQ-CAT-071, api: [API-CAT-024]}
  - {req: REQ-CAT-072, api: [API-CAT-001, API-CAT-003]}
  - {req: REQ-CAT-073, api: [API-CAT-001, API-CAT-003, API-CAT-052]}
  - {req: REQ-CAT-074, api: [API-CAT-001, API-CAT-010, API-CAT-014, API-CAT-052]}
  - {req: REQ-CAT-075, api: [API-CAT-001, API-CAT-003, API-CAT-010, API-CAT-014, API-CAT-032, API-CAT-035, API-CAT-036]}
  - {req: REQ-CAT-076, api: [API-CAT-003, API-CAT-052]}
  - {req: REQ-CAT-077, api: [API-CAT-010]}
  - {req: REQ-CAT-078, api: [API-CAT-012]}
  - {req: REQ-CAT-079, api: [API-CAT-013]}
  - {req: REQ-CAT-080, api: [API-CAT-010]}
  - {req: REQ-CAT-081, api: [API-CAT-012]}
  - {req: REQ-CAT-082, api: [API-CAT-003]}
  - {req: REQ-CAT-083, api: [API-CAT-014]}
  - {req: REQ-CAT-084, api: [API-CAT-015]}
  - {req: REQ-CAT-085, api: [API-CAT-016]}
  - {req: REQ-CAT-086, api: [API-CAT-043]}
  - {req: REQ-CAT-087, api: [API-CAT-014]}
  - {req: REQ-CAT-088, api: [API-CAT-014]}
  - {req: REQ-CAT-089, api: [API-CAT-014, API-CAT-015]}
  - {req: REQ-CAT-090, api: [API-CAT-043]}
  - {req: REQ-CAT-091, api: [API-CAT-014, API-CAT-015, API-CAT-032]}
  - {req: REQ-CAT-092, api: [API-CAT-032]}
  - {req: REQ-CAT-093, api: [API-CAT-033]}
  - {req: REQ-CAT-094, api: [API-CAT-034, API-CAT-043]}
  - {req: REQ-CAT-095, api: [API-CAT-045]}
  - {req: REQ-CAT-096, api: [API-CAT-014]}
  - {req: REQ-CAT-097, api: [API-CAT-043]}
  - {req: REQ-CAT-098, api: [API-CAT-032, API-CAT-033]}
  - {req: REQ-CAT-099, api: [API-CAT-014]}
  - {req: REQ-CAT-100, api: [API-CAT-043]}
  - {req: REQ-CAT-101, api: [API-CAT-042]}
  - {req: REQ-CAT-102, api: [API-CAT-050]}
  - {req: REQ-CAT-103, api: [API-CAT-050]}
  - {req: REQ-CAT-104, api: [API-CAT-050]}
  - {req: REQ-CAT-105, api: [API-CAT-018]}
  - {req: REQ-CAT-106, api: [API-CAT-019]}
  - {req: REQ-CAT-107, api: [API-CAT-020]}
  - {req: REQ-CAT-108, api: [API-CAT-018]}
  - {req: REQ-CAT-109, api: [API-CAT-044]}
  - {req: REQ-CAT-110, api: [API-CAT-018]}
  - {req: REQ-CAT-111, api: [API-CAT-020]}
  - {req: REQ-CAT-112, api: [API-CAT-018, API-CAT-019]}
  - {req: REQ-CAT-113, api: [API-CAT-005]}
  - {req: REQ-CAT-114, api: [API-CAT-005]}
  - {req: REQ-CAT-116, api: [API-CAT-005]}
  - {req: REQ-CAT-117, api: [API-CAT-001, API-CAT-003]}
  - {req: REQ-CAT-118, api: [API-CAT-008]}
  - {req: REQ-CAT-119, api: [API-CAT-002, API-CAT-007]}
  - {req: REQ-CAT-120, api: [API-CAT-035]}
  - {req: REQ-CAT-121, api: [API-CAT-036]}
  - {req: REQ-CAT-122, api: [API-CAT-007, API-CAT-037]}
  - {req: REQ-CAT-123, api: [API-CAT-001, API-CAT-003, API-CAT-008]}
  - {req: REQ-CAT-124, api: [API-CAT-035]}
  - {req: REQ-CAT-125, api: [API-CAT-047]}
  - {req: REQ-CAT-126, api: [API-CAT-047]}
  - {req: REQ-CAT-127, api: [API-CAT-047]}
  - {req: REQ-CAT-128, api: [API-CAT-013]}
  - {req: REQ-CAT-129, api: [API-CAT-006]}
  - {req: REQ-CAT-130, api: [API-CAT-005]}
  - {req: REQ-CAT-131, api: [API-CAT-001, API-CAT-003, API-CAT-004]}
  - {req: REQ-CAT-132, api: [API-CAT-002, API-CAT-003]}
  - {req: REQ-CAT-133, api: [API-CAT-003]}
  - {req: REQ-CAT-134, api: [API-CAT-045]}
  - {req: REQ-CAT-135, api: [API-CAT-003, API-CAT-008, API-CAT-010, API-CAT-012, API-CAT-014, API-CAT-015, API-CAT-018, API-CAT-019]}
  - {req: REQ-CAT-136, api: [API-CAT-053]}
  - {req: REQ-CAT-137, api: [API-CAT-054]}
  - {req: REQ-CAT-138, api: [API-CAT-055]}
  - {req: REQ-CAT-139, api: [API-CAT-049]}
  - {req: REQ-CAT-140, api: [API-CAT-009]}
  - {req: REQ-CAT-141, api: [API-CAT-006]}
  - {req: REQ-CAT-142, api: [API-CAT-005]}
  - {req: REQ-CAT-143, api: [API-CAT-005]}
  - {req: REQ-CAT-144, api: [API-CAT-005]}
  - {req: REQ-CAT-145, api: [API-CAT-006]}
  - {req: REQ-CAT-146, api: [API-CAT-003]}
  - {req: REQ-CAT-147, api: [API-CAT-017]}
  - {req: REQ-CAT-148, api: [API-CAT-056]}
unmapped: []
codes:
  - {code: "CAT-422-NAME-AR-INVALID", rule: RULE-CAT-001}
  - {code: "CAT-422-NAME-EN-TOO-LONG", rule: RULE-CAT-002}
  - {code: "CAT-409-ITEM-NAME-DUPLICATE", rule: RULE-CAT-003}
  - {code: "CAT-422-CODE-SYSTEM-ASSIGNED", rule: RULE-CAT-004}
  - {code: "CAT-500-CODE-NOT-ISSUED", rule: RULE-CAT-005}
  - {code: "CAT-422-LOOKUP-VALUE-INACTIVE", rule: RULE-CAT-006}
  - {code: "CAT-422-BASE-UOM-FIXED", rule: RULE-CAT-007}
  - {code: "CAT-422-CAPABILITY-OFF", rule: RULE-CAT-008}
  - {code: "CAT-500-CAPABILITY-UNREADABLE", rule: RULE-CAT-009}
  - {code: "CAT-200-VARIANTS-IN-USE", rule: RULE-CAT-010}
  - {code: "CAT-200-BUNDLES-IN-USE", rule: RULE-CAT-011}
  - {code: "CAT-201-EXISTING-VARIANTS-KEPT", rule: RULE-CAT-012}
  - {code: "CAT-409-VARIANT-COMBINATION-DUPLICATE", rule: RULE-CAT-013}
  - {code: "CAT-422-VARIANT-LIMIT-EXCEEDED", rule: RULE-CAT-014}
  - {code: "CAT-409-LAST-ACTIVE-VARIANT", rule: RULE-CAT-015}
  - {code: "CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE", rule: RULE-CAT-016}
  - {code: "CAT-409-ATTRIBUTE-VALUE-IN-USE", rule: RULE-CAT-017}
  - {code: "CAT-422-BUNDLE-COMPONENTS-REQUIRED", rule: RULE-CAT-018}
  - {code: "CAT-422-BUNDLE-COMPONENT-INVALID", rule: RULE-CAT-019}
  - {code: "CAT-422-BUNDLE-SINGLE-VARIANT", rule: RULE-CAT-020}
  - {code: "CAT-409-VARIANT-IN-ACTIVE-BUNDLE", rule: RULE-CAT-021}
  - {code: "CAT-422-ITEM-KIND-FIXED", rule: RULE-CAT-022}
  - {code: "CAT-422-CATEGORY-PARENT-INVALID", rule: RULE-CAT-023}
  - {code: "CAT-409-CATEGORY-DUPLICATE", rule: RULE-CAT-024}
  - {code: "CAT-409-CATEGORY-IN-USE", rule: RULE-CAT-025}
  - {code: "CAT-422-CATEGORY-INACTIVE", rule: RULE-CAT-026}
  - {code: "CAT-422-ITEM-UNIT-INVALID", rule: RULE-CAT-027}
  - {code: "CAT-422-CONVERSION-FACTOR-FIXED", rule: RULE-CAT-028}
  - {code: "CAT-409-BARCODE-DUPLICATE", rule: RULE-CAT-029}
  - {code: "CAT-422-GTIN-INVALID", rule: RULE-CAT-030}
  - {code: "CAT-422-BARCODE-UNIT-INVALID", rule: RULE-CAT-031}
  - {code: "CAT-409-BARCODE-SCALE-PREFIX", rule: RULE-CAT-032}
  - {code: "CAT-422-SCALE-CODE-LENGTH", rule: RULE-CAT-033}
  - {code: "CAT-422-SCALE-LAYOUT-INVALID", rule: RULE-CAT-034}
  - {code: "CAT-422-SCALE-LABEL-UNREADABLE", rule: RULE-CAT-035}
  - {code: "CAT-422-LABEL-COPIES-OUT-OF-RANGE", rule: RULE-CAT-036}
  - {code: "CAT-422-LABEL-VARIANT-NO-BARCODE", rule: RULE-CAT-037}
  - {code: "CAT-422-IMAGE-NOT-ACCEPTED", rule: RULE-CAT-038}
  - {code: "CAT-200-PRIMARY-IMAGE-REPLACED", rule: RULE-CAT-039}
  - {code: "CAT-422-MEDIA-VARIANT-MISMATCH", rule: RULE-CAT-040}
  - {code: "CAT-422-PUBLISH-BLOCKED", rule: RULE-CAT-041}
  - {code: "CAT-422-EINVOICE-CODE-INVALID", rule: RULE-CAT-042}
  - {code: "CAT-409-UNIT-EINVOICE-DUPLICATE", rule: RULE-CAT-043}
  - {code: "CAT-422-IMPORT-FILE-REJECTED", rule: RULE-CAT-044}
  - {code: "CAT-200-IMPORT-ROW-REJECTED", rule: RULE-CAT-045}
  - {code: "CAT-400-SEARCH-TERM-TOO-SHORT", rule: RULE-CAT-046}
  - {code: "CAT-404-BARCODE-NOT-FOUND", rule: RULE-CAT-047}
  - {code: "CAT-200-DEACTIVATED", rule: RULE-CAT-048}
  - {code: "CAT-500-CHANGE-NOT-SAVED", rule: RULE-CAT-049}
  - {code: "CAT-422-ITEM-NO-ACTIVE-VARIANT", rule: RULE-CAT-050}
  - {code: "CAT-409-ITEM-UNIT-HAS-BARCODES", rule: RULE-CAT-051}
  - {code: "CAT-422-VARIANT-ATTRIBUTE-MISSING", rule: RULE-CAT-052}
  - {code: "CAT-422-PARENT-INACTIVE", rule: RULE-CAT-053}
```

<!-- PHASE:F1:START traces=REQ-CAT-148,API-CAT-056,REQ-CAT-139,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,API-CAT-053,API-CAT-054,API-CAT-055,REQ-CAT-135,API-CAT-052,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-096,REQ-CAT-097,REQ-CAT-098,REQ-CAT-099,REQ-CAT-100,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-038,API-CAT-039,API-CAT-040,API-CAT-041,API-CAT-042,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,API-CAT-048,API-CAT-049,API-CAT-050,API-CAT-051,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001,SCR-CAT-002,SCR-CAT-003,SCR-CAT-004,SCR-CAT-005,SCR-CAT-006 -->
## F1 — Models & Types

Field and DTO binding: the request and response schemas of this module's operations in `api-spec-cat.yaml` are the source. The blocks below name which schema each screen binds, by operation, and add only the UI-side types that exist in neither the document nor the SRS (form state, pending lines, URL filter objects). TypeScript types are generated from the document (one generator run, no hand-written DTO). Every UI type below is a projection of a generated type.

Shared across screens:
- `ApiResponse<T>` and `Page<T>` (`content`, `page`, `size`, `totalElements`, `totalPages`) wrap every read. `LocalizedError` is the error body.
- `LookupOption = { code, labelAr, labelEn, sortOrder }` is the UI shape of a given lookup value (UXD-CAT-001), as `API-CAT-052` returns it. It carries no activity flag: the operation returns active values only (ADR-CAT-073). `CapabilityFlags = { variants: boolean | unknown, bundles: boolean | unknown }` is the UI shape of the given flags (UXD-CAT-002). `unknown` means unreadable (REQ-CAT-013).
- `PendingState = 'new' | 'changed' | 'deactivate' | 'reactivate' | 'failed'` marks a child line not yet sent (ADR-CAT-049; `reactivate` for an inactive value line — ADR-CAT-069). It is UI-only and never sent.

<!-- SUB:F1-SCR-CAT-001:START traces=API-CAT-053,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-135,API-CAT-052,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-096,REQ-CAT-097,REQ-CAT-099,REQ-CAT-100,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-038,API-CAT-039,API-CAT-041,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001 -->
### F1 · SCR-CAT-001 — Items

Bound schemas: search `ItemSummary` page (API-CAT-038) · read `ItemDetail` with `VariantSummary`, `AttributeValueView`, `BarcodeView`, `ItemUnitView`, `BundleComponentView`, `MediaView` (API-CAT-002) · `VariantDetail` (API-CAT-007) · create `ItemCreateRequest` (API-CAT-001) · update `ItemUpdateRequest` (API-CAT-003) · state `ItemStateRequest` (API-CAT-005) · deactivate response `ItemSummary` (API-CAT-004) · `VariantGenerationRequest` → `VariantGenerationResult` (API-CAT-006) · `VariantOverrideRequest` (API-CAT-008) · `ItemUnitCreateRequest` / `ItemUnitUpdateRequest` (API-CAT-010 / 012) · `BarcodeCreateRequest` / `InternalBarcodeRequest` (API-CAT-014 / 015) · `BundleComponentsRequest` (API-CAT-017) · `ItemMediaCreateRequest` (JSON carrying `fileDocumentRef`) / `ItemMediaUpdateRequest` (API-CAT-018 / 019) · Variant, unit, barcode and media deactivations (API-CAT-009, 013, 016, 020) · `CategoryNode` (API-CAT-039) · `VariantAttributeView` (API-CAT-041) · `BarcodeLookupResult` (API-CAT-043) · `MediaView` list (API-CAT-044) · `CatalogueSettings` (API-CAT-045) · `CatalogueSearchHit` (API-CAT-047).

UI types:
- `ItemSearchFilters = { text?, categoryFk?, brandCode?, itemKindCode?, publishStateCode?, isActiveFl?, page, size }`. It is the URL search params of `/cat/items`, parsed and defaulted (`isActiveFl: true`, `page: 0`, `size: 20`). `itemKindCode` is dropped while `bundles` is off.
- `ItemForm`. Header: the `ItemUpdateRequest` properties the user edits (`nameAr`, `nameEn`, `categoryFk`, `brandCode`, `taxClassCode`, `einvoiceCodeTypeCode`, `einvoiceCode`, `descriptionAr`, `descriptionEn`), plus `itemKindCode` and `baseUomCode` on create only, plus `version` on update. Collections: `bundleLines[]` (`bundleComponentPk?`, `componentVariantFk`, `quantity`, `sortOrder`, and the display-only `componentBaseUomCode` — from `BundleComponentView`, or, for a line added in the picker, the `baseUomCode` of the picked Variant's Item; never sent — ADR-CAT-080); `units[]` (`ItemUnitView` + `pending`); `barcodes[]` (`BarcodeView` + `pending`, or a new `{ variantFk, barcodeTypeCode, barcodeValue?, uomCode, internal: boolean }`); `variantOverrides[]` (`variantPk`, `einvoiceCodeTypeCode`, `einvoiceCode`, `version`); `variantDeactivations[]` (`variantPk`); `generation?` (`selections[]` as in `VariantGenerationRequest`); `media[]` (`MediaView` + `pending`, or a new `{ file: File, fileDocumentRef?, nameAr, nameEn?, variantFk? }` — `fileDocumentRef` is filled by the given FILE upload during Save, ADR-CAT-056).
- Never sent (ADR-CAT-051 §6): `code` on any request; `itemKindCode` / `baseUomCode` on update; `conversionFactor` on Item unit update. `bundleComponents` is omitted on create of a STANDARD Item: it is optional with default `[]` (ADR-CAT-051 §6 as amended).
- `ItemRouteState = { itemPk | 'new', variant?, drawer?: '<line-kind>:<id|new>', picker?: 'variant-generation' | 'bundle-components' }`, parsed from the route and its search params.
<!-- SUB:F1-SCR-CAT-001:END -->

<!-- SUB:F1-SCR-CAT-002:START traces=REQ-CAT-136,API-CAT-053,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-039,API-CAT-040,SCR-CAT-002 -->
### F1 · SCR-CAT-002 — Categories

Bound schemas: `CategoryNode` tree (API-CAT-039) · `CategoryView` (API-CAT-022) · `CategoryCreateRequest` (API-CAT-021) · `CategoryUpdateRequest` (API-CAT-023) · deactivate response `CategoryView` (API-CAT-024) · `CategoryStateRequest` `{isActiveFl: true}` → `CategoryView` (API-CAT-053, reactivate) · `ItemSummary` page (API-CAT-040).

UI types:
- `CategoryTreeFilters = { text?, isActiveFl? }` (URL search params).
- `CategoryRouteState = { node?: categoryPk | 'new', parent?: categoryPk, items?: 'open' }`.
- `CategoryForm` = `CategoryCreateRequest` on create; `CategoryUpdateRequest` (no `code`) + `version` on update.
- `CategoryItemsFilters = { categoryPk, page, size }`.
<!-- SUB:F1-SCR-CAT-002:END -->

<!-- SUB:F1-SCR-CAT-003:START traces=REQ-CAT-137,REQ-CAT-138,API-CAT-054,API-CAT-055,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-041,UXD-CAT-002,SCR-CAT-003 -->
### F1 · SCR-CAT-003 — Variant attributes

Bound schemas: `VariantAttributeView` with `values: VariantAttributeValueView[]` (API-CAT-041, API-CAT-026) · `VariantAttributeCreateRequest` / `VariantAttributeUpdateRequest` (API-CAT-025 / 027) · attribute deactivation (API-CAT-028) · `VariantAttributeStateRequest` `{isActiveFl: true}` (API-CAT-054, reactivate) · `VariantAttributeValueCreateRequest` / `VariantAttributeValueUpdateRequest` (API-CAT-029 / 030) · value deactivation (API-CAT-031) · `VariantAttributeValueStateRequest` `{isActiveFl: true}` (API-CAT-055, reactivate).

UI types:
- `AttributeListFilters = { text?, isActiveFl? }`.
- `AttributeRouteState = { attribute?: variantAttributePk | 'new', values?: 'open', returnTo?: string }`.
- `AttributeForm`. Header: `code` (create only), `nameAr`, `nameEn`, `sortOrder`, `version` (update). Values: `values[]` (`VariantAttributeValueView` + `pending`, or a new `{ code, nameAr, nameEn?, sortOrder }`).
<!-- SUB:F1-SCR-CAT-003:END -->

<!-- SUB:F1-SCR-CAT-004:START traces=REQ-CAT-139,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,API-CAT-048,API-CAT-049,SCR-CAT-004 -->
### F1 · SCR-CAT-004 — Item import

Bound schemas: the xlsx binary of API-CAT-048 (the response is a `Blob`) · `ItemImportRequest` (JSON carrying `fileDocumentRef`, API-CAT-049) → `ImportResult` with `ImportRowRejection[]`. The file is uploaded first through the given FILE client (private storage — ADR-CAT-056).

UI types:
- `ImportForm = { file: File | null }`.
- `ImportView = { phase: 'idle' | 'importing' | 'done' | 'rejected', result?: ImportResult, error?: LocalizedError }`.
<!-- SUB:F1-SCR-CAT-004:END -->

<!-- SUB:F1-SCR-CAT-005:START traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-148,API-CAT-007,API-CAT-042,API-CAT-050,API-CAT-056,SCR-CAT-005 -->
### F1 · SCR-CAT-005 — Barcode labels

Bound schemas: `VariantSearchRow` page (API-CAT-042) · `VariantDetail` (API-CAT-007, for the seeded line) · `LabelRequest` (API-CAT-050) → PDF binary · `LabelInternalBarcodeRequest` (empty body — the base unit) → `BarcodeView` (API-CAT-056, the missing internal barcode of a flagged line — ADR-CAT-078).

UI types:
- `VariantSearchFilters = { text?, page, size }` (search params of the picker).
- `LabelLine = { variantPk, itemPk, code, nameAr, itemNameAr, barcode: string | null, copies, flagged?: 'no-barcode' | 'not-found' }`. It is projected from `VariantSearchRow` (`firstBarcodeValue`, which the server already narrows to the label barcode) or from `VariantDetail` (the label barcode: the first active `BarcodeView` by `createdAt` whose `barcodeTypeCode` is GTIN or INTERNAL and whose `uomCode` equals `VariantDetail.baseUomCode` — ADR-CAT-079). A SCALE code or a barcode bound to an Item unit never fills `barcode`.
- `LabelForm = { lines: LabelLine[] }`. The request sends only `{ variantPk, copies }` per line.
<!-- SUB:F1-SCR-CAT-005:END -->

<!-- SUB:F1-SCR-CAT-006:START traces=API-CAT-052,REQ-CAT-134,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-045,API-CAT-051,UXD-CAT-001,SCR-CAT-006 -->
### F1 · SCR-CAT-006 — Catalogue settings

Bound schemas: `CatalogueSettings` with `ScaleLabelLayoutView[]` and `UnitEinvoiceCodeView[]` (API-CAT-045) · `ScaleLabelLayoutCreateRequest` / `ScaleLabelLayoutUpdateRequest` (API-CAT-032 / 033) · layout deactivation (API-CAT-034) · `UnitEinvoiceCodeCreateRequest` / `UnitEinvoiceCodeUpdateRequest` (API-CAT-035 / 036) · unit code deactivation (API-CAT-037) · `TurnOffCheckResult` with `DependentItem[]` (API-CAT-051).

UI types:
- `SettingsRouteState = { layout?: scaleLabelLayoutPk | 'new', unitCode?: unitEinvoiceCodePk | 'new', check?: 'variants' | 'bundles' }`.
- `LayoutForm`: create carries `key`, `scaleCodeLength` and the rest; update carries neither `key` nor `scaleCodeLength` (both fixed after creation — ADR-CAT-064) and adds `version`.
- `UnitCodeForm`: create carries `key` + `einvoiceUnitCode`; update carries `einvoiceUnitCode` + `version`.
<!-- SUB:F1-SCR-CAT-006:END -->
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=REQ-CAT-148,API-CAT-056,REQ-CAT-139,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,API-CAT-053,API-CAT-054,API-CAT-055,REQ-CAT-135,API-CAT-052,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-096,REQ-CAT-097,REQ-CAT-098,REQ-CAT-099,REQ-CAT-100,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-038,API-CAT-039,API-CAT-040,API-CAT-041,API-CAT-042,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,API-CAT-048,API-CAT-049,API-CAT-050,API-CAT-051,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001,SCR-CAT-002,SCR-CAT-003,SCR-CAT-004,SCR-CAT-005,SCR-CAT-006 -->
## F2 — Data Hooks

These blocks declare what each screen reads and changes, not hook code. Rules shared by every screen:
- **Hook layering**: components use the screen facade only, and the facade uses only the queries declared here (server-state library `tanstack-query`).
- **Cache keys**: page and page size live inside the filter object that forms the cache key. Every filter that changes the response is in the key. The filter object is the route's search params, so back/forward restores a list.
- **Navigation state**: the router holds every open drawer and second level. No `zustand` store holds server data or open state; `zustand` is used by CAT only for the per-user list page size preference.
- **Cache policy**: defaults (staleTime 0, gcTime 5 min, refetch on focus), with three deviations recorded in ADR-CAT-050 / 051:
  - lookups (UXD-CAT-001): staleTime 30 min, gcTime 60 min;
  - the Category tree used as options: staleTime 5 min;
  - capability flags (UXD-CAT-002): staleTime 0, refetched on every screen entry and window focus (REQ-CAT-012).
- **Error routing**: every server code routed to a control names its text by reference: the RULE message (SRS) in ar and en, as the envelope carries it. Platform codes route as §3.0 states.
- **Loading**: LOCAL everywhere (skeleton or button progress). No call is declared slow by the SRS, so no GLOBAL loading.

Lookups shared by all screens (one hook per key, each bound to CAT's own read `API-CAT-052` `GET /api/v1/cat/lookups/{key}` — UXD-CAT-001, ADR-CAT-065; options are the active values with code and ar/en labels; a code on a record that is not among the active options — a value deactivated later (REQ-CAT-076) — is rendered as the raw code, in both languages, because no read of an inactive value exists until `CORE-GAP-lookup-inactive-value-label.md` is answered (ADR-CAT-073); when it is, `API-CAT-052` gains an optional `codes` parameter and `LookupOption.isActiveFl`, and the hooks label every rendered code):
`CAT_BRAND-LOOKUP` · `CAT_UOM-LOOKUP` · `CAT_TAX_CLASS-LOOKUP` · `CAT_ITEM_KIND-LOOKUP` · `CAT_PUBLISH_STATE-LOOKUP` · `CAT_EINVOICE_ITEM_CODE_TYPE-LOOKUP` · `CAT_BARCODE_TYPE-LOOKUP` · `CAT_SCALE_VALUE_TYPE-LOOKUP` · `CAT_EINVOICE_UNIT-LOOKUP`. Each has cache key `[cat, lookups, <KEY>]`. On error the field shows the raw code and the select is disabled, with the generic message; `CAT-404-LOOKUP-KEY-UNKNOWN` would mean the screen asked for a key that is not CAT's, which is a defect.

`CAPABILITY-FLAGS-INIT` (UXD-CAT-002): reads `variants` and `bundles` through the platform app configuration, with cache key `[platform, app-config]`. An unreadable value is `unknown` and is treated as off for display, with RULE-CAT-009's text shown where the function would be.

<!-- SUB:F2-SCR-CAT-001:START traces=API-CAT-053,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-135,API-CAT-052,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-096,REQ-CAT-097,REQ-CAT-099,REQ-CAT-100,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-038,API-CAT-039,API-CAT-041,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001 -->
### F2 · SCR-CAT-001 — Items

```yaml name=screen-hooks
screen: SCR-CAT-001
hooks:
  - {hook: ITEM-SEARCH-QUERY, kind: read, api: [API-CAT-038], cache_key: "[cat, items, search, {text, categoryFk, brandCode, itemKindCode, publishStateCode, isActiveFl, page, size}]", errors: "CAT-400-REQUEST-INVALID → generic · CAT-401 → login · CAT-403 → unauthorized · CAT-500 → generic", loading: LOCAL, invalidation: "—"}
  - {hook: ITEM-QUERY, kind: read, api: [API-CAT-002], cache_key: "[cat, items, detail, itemPk]", errors: "CAT-404-NOT-FOUND → not-found state with Back to search · CAT-401 → login · CAT-403 → unauthorized · CAT-500 → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CATEGORY-OPTIONS-QUERY, kind: read, api: [API-CAT-039], cache_key: "[cat, categories, tree, {isActiveFl: true}]", errors: "CAT-403-FORBIDDEN → Category field and filter hidden (ADR-CAT-051) · other → generic", loading: NONE, invalidation: "—"}
  - {hook: ATTRIBUTE-OPTIONS-QUERY, kind: read, api: [API-CAT-041], cache_key: "[cat, variant-attributes, list, {isActiveFl: true}]", errors: "CAT-403-FORBIDDEN → Generate variants hidden · other → generic (the read is not flag-bound; the picker opens only while `variants` is on — UXD-CAT-002)", loading: LOCAL, invalidation: "—"}
  - {hook: COMPONENT-PICKER-ITEMS-QUERY, kind: read, api: [API-CAT-038], cache_key: "[cat, items, search, {text, itemKindCode: STANDARD, isActiveFl: true, page, size}]", errors: "generic", loading: LOCAL, invalidation: "—"}
  - {hook: COMPONENT-PICKER-VARIANTS-QUERY, kind: read, api: [API-CAT-002], cache_key: "[cat, items, detail, itemPk]", errors: "CAT-404-NOT-FOUND → Item row disabled · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: COMPONENT-VARIANT-LABEL-QUERY, kind: read, api: [API-CAT-007], cache_key: "[cat, variants, detail, variantPk]", errors: "CAT-404-NOT-FOUND → line shows componentVariantCode only · other → code only", loading: NONE, invalidation: "—"}
  - {hook: SCALE-AVAILABILITY-QUERY, kind: read, api: [API-CAT-045], cache_key: "[cat, settings]", errors: "CAT-403-FORBIDDEN → SCALE type not offered (ADR-CAT-051) · other → SCALE not offered", loading: NONE, invalidation: "—"}
  - {hook: VARIANT-IMAGES-QUERY, kind: read, api: [API-CAT-044], cache_key: "[cat, variants, media, variantPk]", errors: "generic inside the expanded row", loading: LOCAL, invalidation: "—"}
  - {hook: BARCODE-LOOKUP-QUERY, kind: read, api: [API-CAT-043], cache_key: "[cat, barcodes, lookup, value]", errors: "CAT-404-BARCODE-NOT-FOUND → under Find by barcode, text RULE-CAT-047 · CAT-422-SCALE-LABEL-UNREADABLE → under Find by barcode, text RULE-CAT-035 · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CATALOGUE-SEARCH-QUERY, kind: read, api: [API-CAT-047], cache_key: "[cat, catalogue-search, term]", errors: "CAT-400-SEARCH-TERM-TOO-SHORT → hint in the global search box, text RULE-CAT-046 · other → the catalogue group shows no hits", loading: LOCAL, invalidation: "—"}
  - {hook: CAT_BRAND-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_BRAND]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_UOM-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_UOM]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_TAX_CLASS-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_TAX_CLASS]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_ITEM_KIND-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_ITEM_KIND]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_PUBLISH_STATE-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_PUBLISH_STATE]", errors: "badge shows the code", loading: NONE}
  - {hook: CAT_EINVOICE_ITEM_CODE_TYPE-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_EINVOICE_ITEM_CODE_TYPE]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_BARCODE_TYPE-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_BARCODE_TYPE]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAPABILITY-FLAGS-INIT, kind: init, cache_key: "[platform, app-config]", errors: "unreadable → flag unknown, RULE-CAT-009 text at the flag-bound pane", loading: NONE}
  - {hook: IMAGE-FILE-URL, kind: lookup, cache_key: "[file, public, fileDocumentRef]", errors: "placeholder image", loading: NONE}
  - {hook: GIVEN-FILE-UPLOAD, kind: mutation, cache_key: "—", errors: "rejection of the given FILE upload → inline on the image file input, the FILE envelope's messageAr / messageEn", loading: LOCAL, invalidation: "—"}
  - {hook: ITEM-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: ITEM-SAVE, kind: mutation, api: [API-CAT-001, API-CAT-003, API-CAT-017, API-CAT-016, API-CAT-020, API-CAT-009, API-CAT-013, API-CAT-010, API-CAT-012, API-CAT-006, API-CAT-008, API-CAT-014, API-CAT-015, API-CAT-018, API-CAT-019], errors: "per call, see ITEM-SAVE below", loading: LOCAL, invalidation: "[cat, items, detail, itemPk] · [cat, items, search] · [cat, variants, detail] · [cat, variants, media] · [cat, variants, search] · [cat, categories, items] · [cat, catalogue-search]"}
  - {hook: ITEM-STATE-COMMAND, kind: mutation, api: [API-CAT-004, API-CAT-005], errors: "see ITEM-STATE-COMMAND below", loading: LOCAL, invalidation: "[cat, items, detail, itemPk] · [cat, items, search] · [cat, categories, items] · [cat, catalogue-search]"}
  - {hook: ITEM-FACADE, kind: facade}
```

#### ITEM-SEARCH-QUERY — API-CAT-038            traces=API-CAT-038,REQ-CAT-003,REQ-CAT-014,REQ-CAT-015
Read query. The filter object is the URL search params. `itemKindCode` is never sent while `bundles` is off (REQ-CAT-015). Results are sorted by nameAr on the server. The Category column is labelled from the row's own `categoryNameAr` (in en `categoryNameEn`, else `categoryNameAr`) — derived by the server for the Category the Item points to, active or inactive (ADR-CAT-075); the brand, kind and publish columns are labelled from their lookups, a code not among the active options showing as the raw code (ADR-CAT-073).

#### ITEM-QUERY — API-CAT-002            traces=API-CAT-002,REQ-CAT-001,REQ-CAT-002,REQ-CAT-010,REQ-CAT-029,REQ-CAT-058,REQ-CAT-119
Read query, enabled when `itemPk` is a number. An itemPk of another tenant answers CAT-404-NOT-FOUND (REQ-CAT-132) and shows the not-found state. It feeds the header, every pane, the Barcode unit options (`baseUomCode` + active `itemUnits[].uomCode` — RULE-CAT-031), the image Variant options (`variants[]`), and the version tokens of the Item, Variants, units and media. It is re-read after every Save, so the committed state is what is shown (REQ-CAT-010).

#### CATEGORY-OPTIONS-QUERY — API-CAT-039            traces=API-CAT-039,REQ-CAT-072
Read query feeding the Category tree select and the Category filter with **active nodes only** (`isActiveFl: true`), so an inactive Category is never offered for selection (REQ-CAT-066).

#### Category labels — API-CAT-038, API-CAT-002            traces=API-CAT-038,API-CAT-002,REQ-CAT-066,REQ-CAT-072
A rendered `categoryFk` — the results column, the Entry header and the read-only Category field — is labelled from the Item read itself: `ItemSummary` / `ItemDetail` carry `categoryCode`, `categoryNameAr` and `categoryNameEn` of the Category the Item points to, active or inactive (ADR-CAT-075). An Item listed with `isActiveFl=false`, or the Entry of an inactive Item, can point to a Category that was deactivated after it (RULE-CAT-025 allows that); its label still shows, read-only, until the user chooses an active Category (AC-CAT-142). The separate whole-tree label read (CATEGORY-LABEL-QUERY of earlier revisions) is dropped: a user whose role cannot read the Category tree (`API-CAT-039` answers CAT-403-FORBIDDEN) still sees the Category's name, and only the selection field and the filter are hidden (ADR-CAT-051 as amended).

#### ATTRIBUTE-OPTIONS-QUERY — API-CAT-041            traces=API-CAT-041,REQ-CAT-014,REQ-CAT-044,REQ-CAT-047,REQ-CAT-049
Read query feeding the Variant generation picker: active attributes with their active values, by sortOrder. It sends `isActiveFl=true`, which `API-CAT-041` applies to the attributes and to their values alike, so inactive attributes and values are not offered (REQ-CAT-044, REQ-CAT-047). Enabled only while `variants` is on and the picker is open.

#### COMPONENT-PICKER-ITEMS-QUERY / COMPONENT-PICKER-VARIANTS-QUERY — API-CAT-038, API-CAT-002            traces=API-CAT-038,API-CAT-002,REQ-CAT-020,REQ-CAT-060
The Bundle component picker searches active STANDARD Items. Expanding an Item reads it and offers its active Variants (`variants[]` with `isActiveFl`). A Variant already in `bundleLines` is disabled (RULE-CAT-019). Both stay inside PERM_CAT_ITEMS_VIEW (ADR-CAT-051 §3).

#### COMPONENT-VARIANT-LABEL-QUERY — API-CAT-007            traces=API-CAT-007,REQ-CAT-058
Labels each Bundle component line: `BundleComponentView` carries `componentVariantFk`, `componentVariantCode` and `componentBaseUomCode`, and this read adds the Variant nameAr / nameEn. The line shows its quantity followed by the unit `componentBaseUomCode`, labelled by CAT_UOM-LOOKUP (raw code when not among the active options — ADR-CAT-073): the quantity is always in the component Item's base unit (ADR-CAT-080). One read per distinct `componentVariantFk`, cached by `variantPk`.

#### SCALE-AVAILABILITY-QUERY — API-CAT-045            traces=API-CAT-045,REQ-CAT-096
Offers barcode type SCALE in the barcode drawer only while at least one `scaleLabelLayouts[]` entry has `isActiveFl` true. The drawer also shows the expected length, from the active layouts' `scaleCodeLength` (RULE-CAT-033). It shares its key with SCR-CAT-006, so a layout saved there refreshes this read.

#### VARIANT-IMAGES-QUERY — API-CAT-044            traces=API-CAT-044,REQ-CAT-109
Read on expanding a Variant row: the images bound to that Variant, or the Item's unbound images, primary first. Thumbnails come through IMAGE-FILE-URL (UXD-CAT-003).

#### BARCODE-LOOKUP-QUERY — API-CAT-043            traces=API-CAT-043,REQ-CAT-086,REQ-CAT-090,REQ-CAT-097,REQ-CAT-100
Read run on submit of "Find by barcode" (Enter, or the scanner's suffix). A hit navigates to `/cat/items/:itemPk?variant=<variantPk>`. When `embeddedValue` is present, a notice shows "scan-weight" or "scan-price" (by `embeddedValueTypeCode`) with `uomCode`. A miss shows the routed text, the input keeps its value, and focus moves to the text filter ("search by name", RULE-CAT-047's next action).

#### CATALOGUE-SEARCH-QUERY — API-CAT-047            traces=API-CAT-047,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127
Exported by the Items module as the catalogue provider of the shell's global search (ADR-CAT-043, ADR-CAT-051 §2). It is enabled only for terms of at least 2 characters; a shorter term shows RULE-CAT-046's text and sends nothing. Hits keep the server order (exact code or barcode first, at most 20). A hit opens `/cat/items/:itemPk`. A user without PERM_CAT_ITEMS_VIEW gets an empty list from the server (REQ-CAT-127). The provider registers only when the user holds that permission, so the shell shows no catalogue group otherwise.

#### ITEM-SAVE — API-CAT-001 … API-CAT-019            traces=API-CAT-001,API-CAT-003,API-CAT-017,API-CAT-016,API-CAT-020,API-CAT-009,API-CAT-013,API-CAT-010,API-CAT-012,API-CAT-006,API-CAT-008,API-CAT-014,API-CAT-015,API-CAT-018,API-CAT-019,REQ-CAT-004,REQ-CAT-024,REQ-CAT-025,REQ-CAT-049,REQ-CAT-140,REQ-CAT-141,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-056,REQ-CAT-057,REQ-CAT-077,REQ-CAT-083,REQ-CAT-084,REQ-CAT-105,REQ-CAT-118
Mutation, the screen's ONE Save (ADR-CAT-049). On an inactive Item only three parts can be pending — the Category (step 1, `API-CAT-003` with every other header field unchanged), the Bundle components (step 2) and a Variant generation (step 5) — because the Entry offers no other input there (F3, ADR-CAT-068). The calls are ORDERED: each is sent only after the previous succeeded, and a part whose lines are unchanged is not sent at all:
1. `API-CAT-001` (new Item; `bundleComponents` = `bundleLines`, or `[]` for STANDARD) or `API-CAT-003` (dirty header; carries `version`).
2. `API-CAT-017`: update of a BUNDLE Item whose `bundleLines` differ from the last read. It carries the Item `version` returned by step 1, or by the last read when step 1 was skipped.
3. Deactivations: barcodes `API-CAT-016` → images `API-CAT-020` → Variants `API-CAT-009` → Item units `API-CAT-013`.
4. Item units: adds `API-CAT-010`, then flag changes `API-CAT-012` (with `version`).
5. Variant generation `API-CAT-006` (when `generation` is set). The success envelope's `CAT-201-EXISTING-VARIANTS-KEPT` shows RULE-CAT-012's text.
6. Variant e-invoice overrides `API-CAT-008` (with each Variant's `version`).
7. Barcodes on saved Variants: `API-CAT-014` (GTIN / SCALE), `API-CAT-015` (internal).
8. Images: for each new image, the file is first uploaded through the given FILE client (GIVEN-FILE-UPLOAD, to the FILE category `CAT_ITEM_IMAGE` — public, PNG / JPEG / WEBP, ≤ 5 MB — ADR-CAT-056, ADR-CAT-076), then `API-CAT-018` is sent with the returned `fileDocumentRef`; then changes `API-CAT-019` (with `version`). A FILE upload rejection stops the sequence like a CAT rejection. The success code `CAT-200-PRIMARY-IMAGE-REPLACED` shows RULE-CAT-039's text.

After the last call, or at the first rejection, the Item is re-read and the pending lines that were not sent stay pending. The rejected line is marked `failed` with its routed text, and the "partial-save" copy is shown when any call before it succeeded. A new Item that is rejected after step 1 is replaced by `/cat/items/:itemPk`.

Errors (text = the RULE message in ar/en as the envelope carries it):
- `CAT-422-NAME-AR-INVALID` → inline on header nameAr, or on the image line's nameAr; text: RULE-CAT-001 message (SRS)
- `CAT-422-NAME-EN-TOO-LONG` → inline on nameEn (header or image line); text: RULE-CAT-002 message
- `CAT-409-ITEM-NAME-DUPLICATE` → inline on nameAr; text: RULE-CAT-003 message, whose {code} the server fills with the existing Item's code; action "Open the existing item" → `/cat/items?text=<code>&isActiveFl=true`, which lists exactly that Item. `<code>` is the one token of the envelope's messageEn that matches the CAT_ITEM code pattern (`ITM-` + 6 digits, D-11); when no token matches (a tenant changed the series pattern), the action falls back to `/cat/items?text=<nameAr>&isActiveFl=true`
- `CAT-422-CODE-SYSTEM-ASSIGNED` → form banner; text: RULE-CAT-004 message (the UI never sends a code; seeing it is a defect)
- `CAT-500-CODE-NOT-ISSUED` → form banner with Try again; text: RULE-CAT-005 message
- `CAT-422-LOOKUP-VALUE-INACTIVE` → inline on every lookup field changed in the rejected call, and the lookups are re-read; text: RULE-CAT-006 message
- `CAT-422-BASE-UOM-FIXED` / `CAT-422-ITEM-KIND-FIXED` → form banner; text: RULE-CAT-007 / RULE-CAT-022 message
- `CAT-422-CAPABILITY-OFF` → banner on the flag-bound pane, and the flags are re-read; text: RULE-CAT-008 message
- `CAT-500-CAPABILITY-UNREADABLE` → form banner with Try again; text: RULE-CAT-009 message
- `CAT-409-VARIANT-COMBINATION-DUPLICATE` → on the generation summary row; text: RULE-CAT-013 message
- `CAT-422-VARIANT-LIMIT-EXCEEDED` → on the generation summary row; text: RULE-CAT-014 message
- `CAT-422-BUNDLE-SINGLE-VARIANT` → on the generation summary row; text: RULE-CAT-020 message
- `CAT-422-VARIANT-ATTRIBUTE-MISSING` → on the generation summary row; text: RULE-CAT-052 message
- `CAT-409-LAST-ACTIVE-VARIANT` → on the Variant line; text: RULE-CAT-015 message
- `CAT-409-VARIANT-IN-ACTIVE-BUNDLE` → on the Variant line; text: RULE-CAT-021 message
- `CAT-422-BUNDLE-COMPONENTS-REQUIRED` → on the Bundle components pane; text: RULE-CAT-018 message
- `CAT-422-BUNDLE-COMPONENT-INVALID` → on the Bundle components pane; text: RULE-CAT-019 message
- `CAT-422-CATEGORY-INACTIVE` → inline on categoryFk, and the Category options are re-read; text: RULE-CAT-026 message
- `CAT-422-EINVOICE-CODE-INVALID` → inline on einvoiceCode (header, or the override line); text: RULE-CAT-042 message
- `CAT-422-ITEM-UNIT-INVALID` → on the unit line; text: RULE-CAT-027 message
- `CAT-422-CONVERSION-FACTOR-FIXED` → on the unit line; text: RULE-CAT-028 message
- `CAT-409-ITEM-UNIT-HAS-BARCODES` → on the unit line marked for deactivation; the line returns to active with the failed mark; text: RULE-CAT-051 message (the server fills {count})
- `CAT-409-BARCODE-DUPLICATE` → inline on barcodeValue; text: RULE-CAT-029 message (the server fills {code})
- `CAT-422-GTIN-INVALID` → inline on barcodeValue; text: RULE-CAT-030 message
- `CAT-422-BARCODE-UNIT-INVALID` → inline on the barcode uomCode; text: RULE-CAT-031 message
- `CAT-409-BARCODE-SCALE-PREFIX` → inline on barcodeValue; text: RULE-CAT-032 message
- `CAT-422-SCALE-CODE-LENGTH` → inline on barcodeValue; text: RULE-CAT-033 message
- `CAT-422-IMAGE-NOT-ACCEPTED` (a 21st active image) → inline on the image file; text: RULE-CAT-038 message · a rejection of the given FILE upload to the category `CAT_ITEM_IMAGE` (wrong type, above 5 MB — the file part of RULE-CAT-038, ADR-CAT-076) → the same place, showing the FILE envelope's messageAr / messageEn; the line is marked `failed` and no request goes to `API-CAT-018` for it
- `CAT-422-MEDIA-VARIANT-MISMATCH` → inline on the image variantFk; text: RULE-CAT-040 message
- `CAT-409-STALE-VERSION` → form banner "stale-version" + Reload (pending lines kept); text: catalogue CAT-409-STALE-VERSION
- `CAT-500-CHANGE-NOT-SAVED` → form banner with Try again; text: RULE-CAT-049 message
- `CAT-404-NOT-FOUND` → the line is dropped and the Item re-read; generic text
- `CAT-422-PARENT-INACTIVE` → form banner, and the Item is re-read, so the Entry turns to its inactive mode (read-only except the three repairs — F3); text: RULE-CAT-053 message
- unauthenticated → login · forbidden → unauthorized view · server → generic

Invalidation on success: `[cat, items, detail, itemPk]`, `[cat, items, search]`, `[cat, variants, detail]` (this Item's Variants), `[cat, variants, media]`, `[cat, variants, search]`, `[cat, categories, items]`, `[cat, catalogue-search]`.

#### ITEM-STATE-COMMAND — API-CAT-004, API-CAT-005            traces=API-CAT-004,API-CAT-005,REQ-CAT-130,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-005,REQ-CAT-006,REQ-CAT-062,REQ-CAT-113,REQ-CAT-114,REQ-CAT-116
Mutation, a record command outside the form, enabled only when the form has no pending change. Deactivate → `API-CAT-004`. Reactivate → `API-CAT-005` `{isActiveFl: true}`. Publish / Unpublish → `API-CAT-005` `{publishStateCode: PUBLISHED | UNPUBLISHED}` — exactly one property per request (ADR-CAT-048).

Errors:
- `CAT-422-PUBLISH-BLOCKED` → command banner; text: RULE-CAT-041 message
- `CAT-409-VARIANT-IN-ACTIVE-BUNDLE` → command banner; text: RULE-CAT-021 message
- `CAT-409-ITEM-NAME-DUPLICATE` (reactivate) → command banner; text: RULE-CAT-003 message (the server fills {code} with the code of the other active Item); action "Open the existing item" → `/cat/items?text=<code>&isActiveFl=true`, `<code>` taken from the message as in ITEM-SAVE — the repair is to rename or deactivate that Item, since the inactive Item's own name cannot change (AC-CAT-162)
- `CAT-422-LOOKUP-VALUE-INACTIVE` (reactivate) → command banner; text: RULE-CAT-006 message
- `CAT-422-ITEM-NO-ACTIVE-VARIANT` (reactivate) → command banner; text: RULE-CAT-050 message; action "Generate variants" opens `?picker=variant-generation` while `variants` is on (the repair of ADR-CAT-068); while it is off, the action is absent and the banner keeps the message's other next action (create a new Item)
- `CAT-422-CATEGORY-INACTIVE` (reactivate) → command banner, and the Category field is highlighted and focused; text: RULE-CAT-026 message; action "Choose a category" focuses the Category field (a categoryFk-only Save is the repair — ADR-CAT-068), and, with PERM_CAT_CATEGORIES_VIEW, a second action "Open the category" → `/cat/categories?node=<categoryFk>`, where it can be reactivated (ADR-CAT-069)
- `CAT-422-BUNDLE-COMPONENT-INVALID` (reactivate) → command banner on the Bundle components pane; text: RULE-CAT-019 message; action "Edit components" opens `?picker=bundle-components` while `bundles` is on (the repair of ADR-CAT-068)
- `CAT-500-CHANGE-NOT-SAVED` → banner; text: RULE-CAT-049 message
- the success code `CAT-200-DEACTIVATED` → notice; text: RULE-CAT-048 message

Invalidation: `[cat, items, detail, itemPk]`, `[cat, items, search]`, `[cat, categories, items]`, `[cat, catalogue-search]`.

#### ITEM-SCREEN-INIT — SCR-CAT-001
Permission read: PERM_CAT_ITEMS_VIEW / CREATE / UPDATE / DELETE, from the platform session. Lookups used: CAT_BRAND, CAT_UOM, CAT_TAX_CLASS, CAT_ITEM_KIND, CAT_PUBLISH_STATE, CAT_EINVOICE_ITEM_CODE_TYPE, CAT_BARCODE_TYPE. Flags: CAPABILITY-FLAGS-INIT. Entity by id when editing: ITEM-QUERY. A new Item starts with `baseUomCode` PIECE, `taxClassCode` STANDARD, `itemKindCode` STANDARD (REQ-CAT-024, AC-CAT-027).

#### ITEM-FACADE — SCR-CAT-001
It composes the queries above. The state it owns:
- the search filters from the URL, including page and size;
- the selected Variant (`?variant=`);
- the react-hook-form instance of `ItemForm`, with the pending lines;
- the open drawer and picker, from the URL;
- derived flags: `isDirty`, `canCommand` (= not dirty), `showVariants` (= `variants` on), `showBundle` (= `bundles` on and kind BUNDLE), `showKind` (= `bundles` on), `scaleOffered`;
- derived loading.

Imperative operations: `save()` (ITEM-SAVE), `deactivate()` / `reactivate()` / `publish()` / `unpublish()` (ITEM-STATE-COMMAND, each after a confirmation), `addLine(kind, value)` / `markDeactivate(kind, id)` (form state only), `applyGeneration(selection)` / `applyComponents(variants)` (from the second levels), `findByBarcode(value)`.
<!-- SUB:F2-SCR-CAT-001:END -->

<!-- SUB:F2-SCR-CAT-002:START traces=REQ-CAT-008,REQ-CAT-136,API-CAT-053,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-039,API-CAT-040,SCR-CAT-002 -->
### F2 · SCR-CAT-002 — Categories

```yaml name=screen-hooks
screen: SCR-CAT-002
hooks:
  - {hook: CATEGORY-TREE-QUERY, kind: read, api: [API-CAT-039], cache_key: "[cat, categories, tree, {text, isActiveFl}]", errors: "CAT-401 → login · CAT-403 → unauthorized · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: PARENT-OPTIONS-QUERY, kind: read, api: [API-CAT-039], cache_key: "[cat, categories, tree, {isActiveFl: true}]", errors: "generic", loading: NONE, invalidation: "—"}
  - {hook: CATEGORY-QUERY, kind: read, api: [API-CAT-022], cache_key: "[cat, categories, detail, categoryPk]", errors: "CAT-404-NOT-FOUND → drawer closes, not-found notice · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CATEGORY-ITEMS-QUERY, kind: read, api: [API-CAT-040], cache_key: "[cat, categories, items, {categoryPk, page, size}]", errors: "CAT-404-NOT-FOUND → empty list · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CATEGORY-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: CATEGORY-SAVE, kind: mutation, api: [API-CAT-021, API-CAT-023], errors: "see CATEGORY-SAVE below", loading: LOCAL, invalidation: "[cat, categories, tree] · [cat, categories, detail, categoryPk] · [cat, categories, items]"}
  - {hook: CATEGORY-DEACTIVATE, kind: mutation, api: [API-CAT-024], errors: "CAT-409-CATEGORY-IN-USE → drawer banner, text RULE-CAT-025 message, action opens Items of this category · CAT-500-CHANGE-NOT-SAVED → drawer banner with Try again, text RULE-CAT-049 message", loading: LOCAL, invalidation: "[cat, categories, tree] · [cat, categories, detail, categoryPk]"}
  - {hook: CATEGORY-REACTIVATE, kind: mutation, api: [API-CAT-053], errors: "see CATEGORY-REACTIVATE below", loading: LOCAL, invalidation: "[cat, categories, tree] · [cat, categories, detail, categoryPk] · [cat, categories, items]"}
  - {hook: CATEGORY-FACADE, kind: facade}
```

#### CATEGORY-TREE-QUERY / PARENT-OPTIONS-QUERY — API-CAT-039            traces=API-CAT-039,REQ-CAT-067,REQ-CAT-069
The tree view reads with the screen filters. The parent select reads active nodes only, and disables the node being edited and its descendants (computed from the `children` of the tree read — RULE-CAT-023). Depth is computed from the same tree to warn before save.

#### CATEGORY-QUERY — API-CAT-022            traces=API-CAT-022,REQ-CAT-066,REQ-CAT-067
Entity by id for the entry drawer (`?node=<categoryPk>`): it supplies the `version` and fresh values. A deactivated Category stays readable (REQ-CAT-066).

#### CATEGORY-ITEMS-QUERY — API-CAT-040            traces=API-CAT-040,REQ-CAT-068
Feeds the "Items of this category" summary row (total count + 3 names from page 0) and the second-level list (paged). A row jump seeds `/cat/items/:itemPk`, which identifies one Item.

#### CATEGORY-SAVE — API-CAT-021, API-CAT-023            traces=API-CAT-021,API-CAT-023,REQ-CAT-008,REQ-CAT-064,REQ-CAT-065,REQ-CAT-069,REQ-CAT-070
Mutation, the drawer's one Save: create (`API-CAT-021`) or update (`API-CAT-023`, with `version`, without `code`). Errors:
- `CAT-422-NAME-AR-INVALID` → inline on nameAr; text: RULE-CAT-001 message
- `CAT-422-NAME-EN-TOO-LONG` → inline on nameEn; text: RULE-CAT-002 message
- `CAT-422-CATEGORY-PARENT-INVALID` → inline on parentCategoryFk; text: RULE-CAT-023 message
- `CAT-409-CATEGORY-DUPLICATE` → inline on code (create) and nameAr; text: RULE-CAT-024 message
- `CAT-409-STALE-VERSION` → drawer banner + Reload; text: catalogue CAT-409-STALE-VERSION
- `CAT-500-CHANGE-NOT-SAVED` → drawer banner with Try again (the drawer keeps its values); text: RULE-CAT-049 message (ADR-CAT-077)
- other → as §3.0

#### CATEGORY-DEACTIVATE — API-CAT-024            traces=API-CAT-024,REQ-CAT-008,REQ-CAT-066,REQ-CAT-071
Record command in the drawer, after a confirmation. The success code `CAT-200-DEACTIVATED` → notice, text: RULE-CAT-048 message. `CAT-500-CHANGE-NOT-SAVED` → drawer banner with Try again, text: RULE-CAT-049 message (ADR-CAT-077).

#### CATEGORY-REACTIVATE — API-CAT-053            traces=API-CAT-053,REQ-CAT-008,REQ-CAT-136
Record command in the drawer of an inactive Category, after a confirmation, only while the drawer has no pending change; it sends `{isActiveFl: true}` (ADR-CAT-069). Errors:
- `CAT-422-CATEGORY-PARENT-INVALID` → drawer banner; text: RULE-CAT-023 message; action "Open the parent" → `?node=<parentCategoryFk>`, where the parent can be reactivated first
- `CAT-409-CATEGORY-DUPLICATE` → drawer banner and inline on nameAr; text: RULE-CAT-024 message (rename it first, then reactivate)
- `CAT-500-CHANGE-NOT-SAVED` → drawer banner with Try again; text: RULE-CAT-049 message (ADR-CAT-077)
- other → as §3.0

#### CATEGORY-SCREEN-INIT — SCR-CAT-002
Permission read PERM_CAT_CATEGORIES_VIEW / CREATE / UPDATE / DELETE; no lookups; entity by id: CATEGORY-QUERY. A new node takes `?parent=` as its parent.

#### CATEGORY-FACADE — SCR-CAT-002
It owns: the tree filters (URL); the open node (`?node=`) and `items=open`; the drawer form; the Items-of-category paging inside its filter object; derived loading. Operations: `save()`, `deactivate()`, `reactivate()`, `openItems()`, `openDirectItems()` ("Items directly in this category" → `/cat/items?categoryFk=<categoryPk>`; API-CAT-038 filters on the Category itself, not on its sub-categories, unlike the sub-tree count of CATEGORY-ITEMS-QUERY — so the jump carries its own label).
<!-- SUB:F2-SCR-CAT-002:END -->

<!-- SUB:F2-SCR-CAT-003:START traces=REQ-CAT-137,REQ-CAT-138,API-CAT-054,API-CAT-055,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-041,UXD-CAT-002,SCR-CAT-003 -->
### F2 · SCR-CAT-003 — Variant attributes

```yaml name=screen-hooks
screen: SCR-CAT-003
hooks:
  - {hook: ATTRIBUTE-LIST-QUERY, kind: read, api: [API-CAT-041], cache_key: "[cat, variant-attributes, list, {text, isActiveFl}]", errors: "CAT-403 → unauthorized · other → generic (the read is not flag-bound; while `variants` is off the screen sends nothing — UXD-CAT-002)", loading: LOCAL, invalidation: "—"}
  - {hook: ATTRIBUTE-QUERY, kind: read, api: [API-CAT-026], cache_key: "[cat, variant-attributes, detail, variantAttributePk]", errors: "CAT-404-NOT-FOUND → drawer closes, not-found notice · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CAPABILITY-FLAGS-INIT, kind: init, cache_key: "[platform, app-config]", errors: "unreadable → screen state with RULE-CAT-009 text", loading: NONE}
  - {hook: ATTRIBUTE-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized · variants off → RULE-CAT-008 state", loading: NONE}
  - {hook: ATTRIBUTE-SAVE, kind: mutation, api: [API-CAT-025, API-CAT-027, API-CAT-031, API-CAT-055, API-CAT-029, API-CAT-030], errors: "see ATTRIBUTE-SAVE below", loading: LOCAL, invalidation: "[cat, variant-attributes, list] · [cat, variant-attributes, detail, variantAttributePk] · [cat, items, detail] · [cat, variants, detail]"}
  - {hook: ATTRIBUTE-DEACTIVATE, kind: mutation, api: [API-CAT-028], errors: "CAT-409-ATTRIBUTE-VALUE-IN-USE → drawer banner, text RULE-CAT-017 message", loading: LOCAL, invalidation: "[cat, variant-attributes, list] · [cat, variant-attributes, detail, variantAttributePk]"}
  - {hook: ATTRIBUTE-REACTIVATE, kind: mutation, api: [API-CAT-054], errors: "CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE → drawer banner and inline on nameAr, text RULE-CAT-016 message · other → as §3.0", loading: LOCAL, invalidation: "[cat, variant-attributes, list] · [cat, variant-attributes, detail, variantAttributePk]"}
  - {hook: ATTRIBUTE-FACADE, kind: facade}
```

#### ATTRIBUTE-LIST-QUERY — API-CAT-041            traces=API-CAT-041,REQ-CAT-048,REQ-CAT-014
The list by sortOrder, each attribute with its values. It sends no `isActiveFl` filter by default (the Active filter starts empty), so `API-CAT-041` returns every attribute with every value, active and inactive, and inactive ones stay visible and can be reactivated (API-CAT-054, 055). When the user sets the Active filter, it filters the values as well as the attributes. Enabled only while `variants` is on (UXD-CAT-002); while it is off, the screen shows RULE-CAT-008's text and sends nothing.

#### ATTRIBUTE-QUERY — API-CAT-026            traces=API-CAT-026,REQ-CAT-048
Entity by id for the entry drawer: the attribute's `version` and its values with their `version`s seed the form. `API-CAT-026` takes no filter and returns every value of the attribute, active and inactive, so the values editor sends no filter and shows inactive values with their Reactivate action (REQ-CAT-138).

#### ATTRIBUTE-SAVE — API-CAT-025 … API-CAT-031, API-CAT-055            traces=API-CAT-025,API-CAT-027,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-055,REQ-CAT-042,REQ-CAT-043,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-054,REQ-CAT-055,REQ-CAT-138
Mutation, the drawer's ONE Save. The calls are ORDERED, and each is sent only after the previous succeeded:
1. `API-CAT-025` (new attribute) or `API-CAT-027` (dirty header, with `version`).
2. Value deactivations `API-CAT-031`. They go first, so that a nameAr freed among the active values can be reused (RULE-CAT-016).
3. Value reactivations `API-CAT-055` (lines marked `reactivate`, ADR-CAT-069), after the deactivations for the same reason.
4. Value creates `API-CAT-029` under the attribute's pk returned by step 1, or the existing pk.
5. Value changes `API-CAT-030` (with `version`).

The value calls are not sent at all when the value set is unchanged. At the first rejection the sequence stops: the attribute is re-read, the unsent values stay pending, the failed line is marked, and the "partial-save" copy is shown when an earlier call succeeded.

Errors:
- `CAT-422-NAME-AR-INVALID` → inline on nameAr (attribute or value line); text: RULE-CAT-001 message
- `CAT-422-NAME-EN-TOO-LONG` → inline on nameEn; text: RULE-CAT-002 message
- `CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE` → inline on code and nameAr of the attribute or value line; text: RULE-CAT-016 message
- `CAT-409-ATTRIBUTE-VALUE-IN-USE` → on the value line; text: RULE-CAT-017 message (the server fills {count})
- `CAT-422-CAPABILITY-OFF` → drawer banner, and the flags are re-read; text: RULE-CAT-008 message
- `CAT-500-CAPABILITY-UNREADABLE` → drawer banner; text: RULE-CAT-009 message
- `CAT-409-STALE-VERSION` → drawer banner + Reload; text: catalogue CAT-409-STALE-VERSION

Invalidation includes the Item reads, because derived Variant names change with the value names (AC-CAT-045, AC-CAT-048).

#### ATTRIBUTE-DEACTIVATE — API-CAT-028            traces=API-CAT-028,REQ-CAT-044,REQ-CAT-054
Record command in the drawer, after a confirmation, only while the drawer has no pending change.

#### ATTRIBUTE-REACTIVATE — API-CAT-054            traces=API-CAT-054,REQ-CAT-137
Record command in the drawer of an inactive attribute, after a confirmation, only while the drawer has no pending change; it sends `{isActiveFl: true}`. Its values keep their own state: an inactive value line offers "Reactivate", which marks it `reactivate` for the next Save (ATTRIBUTE-SAVE step 3 — ADR-CAT-069).

#### ATTRIBUTE-SCREEN-INIT — SCR-CAT-003
Permission read PERM_CAT_VARIANT_ATTRIBUTES_VIEW / CREATE / UPDATE / DELETE; the `variants` flag (UXD-CAT-002); no lookups; entity by id: ATTRIBUTE-QUERY. `returnTo` is kept to go back to the Items Entry after Save.

#### ATTRIBUTE-FACADE — SCR-CAT-003
It owns: the list filters (URL); the open attribute (`?attribute=`) and `values=open`; the drawer form with the value lines; derived loading. Operations: `save()`, `deactivate()`, `reactivate()`, `applyValues(set)` (from the second level, form state only).
<!-- SUB:F2-SCR-CAT-003:END -->

<!-- SUB:F2-SCR-CAT-004:START traces=REQ-CAT-139,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,API-CAT-048,API-CAT-049,SCR-CAT-004 -->
### F2 · SCR-CAT-004 — Item import

```yaml name=screen-hooks
screen: SCR-CAT-004
hooks:
  - {hook: IMPORT-SHEET-DOWNLOAD, kind: read, api: [API-CAT-048], cache_key: "none — a file download, not cached", errors: "CAT-403 → unauthorized · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: IMPORT-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: GIVEN-FILE-UPLOAD, kind: mutation, cache_key: "—", errors: "rejection of the given FILE upload → inline on the file input, the FILE envelope's messageAr / messageEn", loading: LOCAL, invalidation: "—"}
  - {hook: IMPORT-SUBMIT, kind: mutation, api: [API-CAT-049], errors: "see IMPORT-SUBMIT below", loading: LOCAL, invalidation: "[cat, items, search] · [cat, catalogue-search] · [cat, categories, items]"}
  - {hook: IMPORT-FACADE, kind: facade}
```

#### IMPORT-SHEET-DOWNLOAD — API-CAT-048            traces=API-CAT-048,REQ-CAT-036
Read on click. The binary body is saved as a file named after the import sheet.

#### IMPORT-SUBMIT — API-CAT-049            traces=API-CAT-049,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-139
Mutation, the screen's one submit. The chosen file is first uploaded through the given FILE client (GIVEN-FILE-UPLOAD, private storage — ADR-CAT-056), then `API-CAT-049` is sent with `{fileDocumentRef}`. The response `ImportResult` fills the result view. Errors and notices:
- `CAT-422-IMPORT-FILE-REJECTED` → inline on the file input; text: RULE-CAT-044 message
- a rejection of the given FILE upload → inline on the file input, showing the FILE envelope's messageAr / messageEn
- `CAT-404-NOT-FOUND` (the uploaded file is no longer available) → inline on the file input; generic text, and the file is uploaded again on the next Import
- the success code `CAT-200-IMPORT-ROW-REJECTED` → result list; each rejected row shows its `messageAr` / `messageEn` (RULE-CAT-045 message filled per row)
- `CAT-500-CODE-NOT-ISSUED` → banner with Try again; text: RULE-CAT-005 message (the whole import was rolled back — ADR-CAT-048 §3)
- `CAT-500-CHANGE-NOT-SAVED` → banner; text: RULE-CAT-049 message (also the answer when the import passed its 120-second limit and was rolled back whole — REQ-CAT-139, ADR-CAT-071; no Item was created, and the owner imports the file again, split into smaller files)

#### IMPORT-SCREEN-INIT — SCR-CAT-004
Permission read PERM_CAT_ITEM_IMPORT_VIEW / CREATE; no lookups; no entity. When mounted by the setup wizard, the wizard's "next" stays the wizard's control; this screen exposes `onImported(result)`.

#### IMPORT-FACADE — SCR-CAT-004
It owns: the chosen file, the import phase, the last result. Operations: `downloadSheet()`, `import()`, `reset()`.
<!-- SUB:F2-SCR-CAT-004:END -->

<!-- SUB:F2-SCR-CAT-005:START traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-148,API-CAT-007,API-CAT-042,API-CAT-050,API-CAT-056,SCR-CAT-005 -->
### F2 · SCR-CAT-005 — Barcode labels

```yaml name=screen-hooks
screen: SCR-CAT-005
hooks:
  - {hook: VARIANT-SEARCH-QUERY, kind: read, api: [API-CAT-042], cache_key: "[cat, variants, search, {text, page, size}]", errors: "CAT-403 → unauthorized · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: SEEDED-VARIANT-QUERY, kind: read, api: [API-CAT-007], cache_key: "[cat, variants, detail, variantPk]", errors: "CAT-404-NOT-FOUND / CAT-403-FORBIDDEN → seed dropped with a notice (ADR-CAT-051)", loading: LOCAL, invalidation: "—"}
  - {hook: LABEL-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: LABEL-PRINT, kind: mutation, api: [API-CAT-050], errors: "see LABEL-PRINT below", loading: LOCAL, invalidation: "—"}
  - {hook: LABEL-INTERNAL-BARCODE, kind: mutation, api: [API-CAT-056], errors: "see LABEL-INTERNAL-BARCODE below", loading: LOCAL, invalidation: "[cat, variants, search] · [cat, variants, detail, variantPk] · [cat, items, detail]"}
  - {hook: LABEL-FACADE, kind: facade}
```

#### VARIANT-SEARCH-QUERY — API-CAT-042            traces=API-CAT-042,REQ-CAT-101
Feeds the Variant picker: active Variants by code, name or barcode, paged. Each row carries `itemNameAr` and `firstBarcodeValue`, the label barcode — the earliest-created active GTIN or INTERNAL barcode bound to the Item's base unit (empty when none — ADR-CAT-079).

#### SEEDED-VARIANT-QUERY — API-CAT-007            traces=API-CAT-007,REQ-CAT-101
Runs once when the route carries `?variantPk=`. It adds that one Variant as a line, with its label barcode from `barcodes[]`: the earliest-created active barcode of type GTIN or INTERNAL whose `uomCode` equals `baseUomCode` (ADR-CAT-079).

#### LABEL-PRINT — API-CAT-050            traces=API-CAT-050,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104
Mutation, the screen's one submit. It sends `{lines: [{variantPk, copies}]}`, and the PDF body opens in a new tab. Errors:
- `CAT-422-LABEL-COPIES-OUT-OF-RANGE` → inline on the copies of the offending lines and on the total; text: RULE-CAT-036 message
- `CAT-422-LABEL-VARIANT-NO-BARCODE` → form banner; text: RULE-CAT-037 message (the server fills {count}). Lines whose `barcode` is empty are flagged `no-barcode`, each with "Generate internal barcode / إنشاء باركود داخلي" (LABEL-INTERNAL-BARCODE — shown with PERM_CAT_BARCODE_LABELS_CREATE, ADR-CAT-078) and, for a user holding PERM_CAT_ITEMS_VIEW, "Open item" → `/cat/items/:itemPk` (ADR-CAT-051 §5)
- `CAT-404-NOT-FOUND` → lines no longer found are flagged `not-found`; generic text

#### LABEL-INTERNAL-BARCODE — API-CAT-056            traces=API-CAT-056,REQ-CAT-148,REQ-CAT-104
Mutation, a record command on one line flagged `no-barcode`, run at once after a confirmation (it is not a second submit of the form). It sends an empty body, so the barcode is bound to the Item's base unit, and the server runs the rules and locks of the Items screen's internal-barcode operation (ADR-CAT-078). On success the line takes the returned `barcodeValue`, its flag is cleared, and Print can run. Errors (the line keeps its flag and shows the text):
- `CAT-422-PARENT-INACTIVE` → on the line; text: RULE-CAT-053 message (the Variant or its Item was deactivated meanwhile)
- `CAT-422-BARCODE-UNIT-INVALID` → on the line; text: RULE-CAT-031 message
- `CAT-422-LOOKUP-VALUE-INACTIVE` → on the line; text: RULE-CAT-006 message
- `CAT-409-BARCODE-SCALE-PREFIX` → on the line; text: RULE-CAT-032 message
- `CAT-409-BARCODE-DUPLICATE` → on the line; text: RULE-CAT-029 message (the server fills {code})
- `CAT-500-CODE-NOT-ISSUED` → on the line with Try again; text: RULE-CAT-005 message
- `CAT-500-CHANGE-NOT-SAVED` → on the line with Try again; text: RULE-CAT-049 message
- `CAT-404-NOT-FOUND` → the line is flagged `not-found`; generic text
- other → as §3.0

#### LABEL-SCREEN-INIT — SCR-CAT-005
Permission read PERM_CAT_BARCODE_LABELS_VIEW / CREATE; no lookups; the seed from `?variantPk=`.

#### LABEL-FACADE — SCR-CAT-005
It owns: the lines (form state), the picker filters (URL, `?picker=variants&text=`), the derived total and the pre-flagged lines without a barcode. Operations: `addVariants(rows)` (duplicates merged, copies 1), `removeLine()`, `generateInternalBarcode(line)` (LABEL-INTERNAL-BARCODE, on a `no-barcode` line), `print()`.
<!-- SUB:F2-SCR-CAT-005:END -->

<!-- SUB:F2-SCR-CAT-006:START traces=API-CAT-052,REQ-CAT-134,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-045,API-CAT-051,UXD-CAT-001,SCR-CAT-006 -->
### F2 · SCR-CAT-006 — Catalogue settings

```yaml name=screen-hooks
screen: SCR-CAT-006
hooks:
  - {hook: SETTINGS-QUERY, kind: read, api: [API-CAT-045], cache_key: "[cat, settings]", errors: "CAT-403 → unauthorized · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: TURN-OFF-CHECK-QUERY, kind: read, api: [API-CAT-051], cache_key: "[cat, capability-checks, turn-off, flag]", errors: "CAT-400-REQUEST-INVALID → inline on the flag selector · other → generic", loading: LOCAL, invalidation: "—"}
  - {hook: CAT_UOM-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_UOM]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_EINVOICE_UNIT-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_EINVOICE_UNIT]", errors: "select disabled, generic", loading: NONE}
  - {hook: CAT_SCALE_VALUE_TYPE-LOOKUP, kind: lookup, api: [API-CAT-052], cache_key: "[cat, lookups, CAT_SCALE_VALUE_TYPE]", errors: "select disabled, generic", loading: NONE}
  - {hook: SETTINGS-SCREEN-INIT, kind: init, cache_key: "—", errors: "no VIEW → unauthorized", loading: NONE}
  - {hook: LAYOUT-SAVE, kind: mutation, api: [API-CAT-032, API-CAT-033], errors: "see LAYOUT-SAVE below", loading: LOCAL, invalidation: "[cat, settings]"}
  - {hook: LAYOUT-DEACTIVATE, kind: mutation, api: [API-CAT-034], errors: "generic", loading: LOCAL, invalidation: "[cat, settings]"}
  - {hook: UNIT-CODE-SAVE, kind: mutation, api: [API-CAT-035, API-CAT-036], errors: "see UNIT-CODE-SAVE below", loading: LOCAL, invalidation: "[cat, settings] · [cat, variants, detail]"}
  - {hook: UNIT-CODE-DEACTIVATE, kind: mutation, api: [API-CAT-037], errors: "generic", loading: LOCAL, invalidation: "[cat, settings] · [cat, variants, detail]"}
  - {hook: SETTINGS-FACADE, kind: facade}
```

#### SETTINGS-QUERY — API-CAT-045            traces=API-CAT-045,REQ-CAT-095
Both lists in full, each entry with its active state. Each drawer edits a list entry (there is no read-by-id operation for these records), taking `version` from the list. A Unit e-invoice code row is labelled by the CAT_UOM lookup of its `key`, and by `valueAr` / `valueEn` when present.

#### TURN-OFF-CHECK-QUERY — API-CAT-051            traces=API-CAT-051,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023
Read run on "Check" with `flag`. Allowed → "allowed-yes" copy. Not allowed → the envelope's success code `CAT-200-VARIANTS-IN-USE` / `CAT-200-BUNDLES-IN-USE` with RULE-CAT-010 / RULE-CAT-011 message (the server fills {count}), `dependentCount`, and up to 20 `dependentItems` (code, nameAr). Each one links to `/cat/items/:itemPk`.

#### LAYOUT-SAVE — API-CAT-032, API-CAT-033            traces=API-CAT-032,API-CAT-033,REQ-CAT-092,REQ-CAT-093,REQ-CAT-098
Mutation, the layout drawer's one Save. Errors:
- `CAT-422-SCALE-LAYOUT-INVALID` → inline on key, scaleCodeLength and valueDecimals; text: RULE-CAT-034 message
- `CAT-409-BARCODE-SCALE-PREFIX` (create: existing barcodes start with the prefix) → inline on key; text: RULE-CAT-032 message
- `CAT-422-LOOKUP-VALUE-INACTIVE` → inline on embeddedValueTypeCode; text: RULE-CAT-006 message
- `CAT-409-STALE-VERSION` → drawer banner + Reload; text: catalogue CAT-409-STALE-VERSION

#### UNIT-CODE-SAVE — API-CAT-035, API-CAT-036            traces=API-CAT-035,API-CAT-036,REQ-CAT-120,REQ-CAT-121,REQ-CAT-124
Mutation, the unit code drawer's one Save. Errors:
- `CAT-409-UNIT-EINVOICE-DUPLICATE` → inline on key; text: RULE-CAT-043 message, with "Open the existing code" → `?unitCode=<pk>` (the active entry with that key, from SETTINGS-QUERY)
- `CAT-422-LOOKUP-VALUE-INACTIVE` → inline on key or einvoiceUnitCode; text: RULE-CAT-006 message
- `CAT-409-STALE-VERSION` → drawer banner + Reload

#### LAYOUT-DEACTIVATE / UNIT-CODE-DEACTIVATE — API-CAT-034, API-CAT-037            traces=API-CAT-034,API-CAT-037,REQ-CAT-094,REQ-CAT-122
Record commands in their drawers, after a confirmation.

#### SETTINGS-SCREEN-INIT — SCR-CAT-006
Permission read PERM_CAT_SETTINGS_VIEW / CREATE / UPDATE / DELETE; lookups CAT_UOM, CAT_EINVOICE_UNIT, CAT_SCALE_VALUE_TYPE; entity by id: the list entry. A new layout starts with scaleCodeLength 5, WEIGHT, decimals 3.

#### SETTINGS-FACADE — SCR-CAT-006
It owns: the open drawer (`?layout=` / `?unitCode=`), the chosen check flag (`?check=`), the drawer forms, derived loading. Operations: `saveLayout()`, `deactivateLayout()`, `saveUnitCode()`, `deactivateUnitCode()`, `runCheck(flag)`.
<!-- SUB:F2-SCR-CAT-006:END -->
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=REQ-CAT-148,API-CAT-056,REQ-CAT-139,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,API-CAT-053,API-CAT-054,API-CAT-055,API-CAT-052,REQ-CAT-135,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-096,REQ-CAT-097,REQ-CAT-098,REQ-CAT-099,REQ-CAT-100,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-038,API-CAT-039,API-CAT-040,API-CAT-041,API-CAT-042,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,API-CAT-048,API-CAT-049,API-CAT-050,API-CAT-051,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001,SCR-CAT-002,SCR-CAT-003,SCR-CAT-004,SCR-CAT-005,SCR-CAT-006 -->
## F3 — Forms & Validators

Forms use `react-hook-form` with a `zod` resolver per form. A client validator mirrors a RULE only where the client holds every input the rule needs. Its message is that RULE's message (SRS), in ar and en, by reference; the client fills a placeholder only when it knows the value. The server stays authoritative, and its codes are mapped back to the same fields (F2). A limit with no RULE (a maximum length from the document) is enforced by the input's `maxLength`, not by a message. A field the server fixes after first save is rendered read-only, not validated.

<!-- SUB:F3-SCR-CAT-001:START traces=API-CAT-053,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,API-CAT-052,REQ-CAT-135,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-096,REQ-CAT-097,REQ-CAT-099,REQ-CAT-100,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-038,API-CAT-039,API-CAT-041,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001 -->
### F3 · SCR-CAT-001 — Items

`ItemForm` schema:

| Field | Validator | Message |
|---|---|---|
| nameAr | required · 1–200 characters · contains ≥ 1 Arabic letter (U+0621–U+064A, the set of RULE-CAT-001) · stored as typed, Latin letters, digits and symbols kept (REQ-CAT-028) | RULE-CAT-001 |
| nameEn | ≤ 200 characters | RULE-CAT-002 |
| baseUomCode, taxClassCode, itemKindCode | required (pre-filled; create only for baseUomCode and itemKindCode) · an active option of its lookup | RULE-CAT-006 |
| categoryFk | optional · an active node of CATEGORY-OPTIONS-QUERY | RULE-CAT-026 |
| brandCode, einvoiceCodeTypeCode | optional · an active option | RULE-CAT-006 |
| einvoiceCodeTypeCode + einvoiceCode | both or neither · GS1 → 8–14 digits · EGS → starts with `EG-`, ≤ 100 characters | RULE-CAT-042 |
| descriptionAr, descriptionEn | input `maxLength` 2000 | — |

Line validators (each one-line drawer has its own schema; confirming returns the line to `ItemForm` only when it is valid):

| Line | Validator | Message |
|---|---|---|
| Variant override | einvoiceCodeTypeCode + einvoiceCode pair and format, as on the header | RULE-CAT-042 |
| Item unit (add) | uomCode required, ≠ baseUomCode, not the uomCode of another active or pending unit · conversionFactor > 0, ≤ 999,999.999999, with ≤ 6 decimals (the bounds of the API document) | RULE-CAT-027 |
| Barcode GTIN | 8, 12, 13 or 14 digits with a valid GS1 check digit | RULE-CAT-030 |
| Barcode GTIN / internal | does not begin with the key of an active scale label layout (from SCALE-AVAILABILITY-QUERY when readable) | RULE-CAT-032 ({prefix} filled) |
| Barcode SCALE | digits only · length equal to an active layout's scaleCodeLength | RULE-CAT-033 ({length} filled) |
| Barcode unit | the base unit or an active (saved) Item unit | RULE-CAT-031 |
| Bundle components | ≥ 1 line · quantity > 0, ≤ 999,999.999, with ≤ 3 decimals (the bounds of the API document) · each componentVariantFk once | RULE-CAT-018 · RULE-CAT-019 |
| Variant generation | 1–3 attributes, each with ≥ 1 value · active Variants after generation (current active + new combinations, computed from the selection) ≤ 300 | RULE-CAT-014 |
| Variant generation (used attributes) | every attribute the Item's active Variants already carry (from ITEM-QUERY `variants[].attributeValues`) is selected with ≥ 1 value; checked before the limits | RULE-CAT-052 |
| Item unit (deactivate) | no active barcode of the Item, saved and not marked for deactivation, is bound to the unit's uomCode | RULE-CAT-051 ({count} filled) |
| Image (add) | active + pending images ≤ 20 (the file's type and size are not checked by the client: the FILE category `CAT_ITEM_IMAGE` refuses a wrong file at upload, and the refusal is shown on the file input — ADR-CAT-076) | RULE-CAT-038 |
| Image nameAr / nameEn | nameAr as the header rule (default the Item's nameAr) · nameEn ≤ 200 | RULE-CAT-001 · RULE-CAT-002 |

Form behaviour:
- New Item: only nameAr is empty; the other three mandatory fields are pre-filled (REQ-CAT-024, REQ-CAT-026). Advanced is collapsed and opens automatically when one of its fields carries an error.
- Read-only after first save: code, baseUomCode, itemKindCode, a saved unit's uomCode and conversionFactor, a saved barcode's value, type and unit (RULE-CAT-004, 007, 022, 028).
- Inactive Item (`isActiveFl` false): the Entry renders read-only except three repairs (RULE-CAT-053, REQ-CAT-135, REQ-CAT-145 … REQ-CAT-147, ADR-CAT-068):
  - the **Category** field (categoryFk) stays editable, with the active Category options; Save then sends `API-CAT-003` with every other field unchanged;
  - **"Generate variants"** stays available while `variants` is on and the Item is STANDARD, with its picker and summary row;
  - the **Bundle components** pane stays editable (Add component, quantity, remove) while `bundles` is on and the Item is a BUNDLE.
  Every other header field is read-only, and every other add trigger (Add unit, Add barcode, Generate internal barcode, Add image, E-invoice override) and line edit is hidden. Reactivate and the line Deactivate actions remain, so the Item can still be cleaned up. An inactive Variant line shows no Add barcode and no E-invoice override. The reactivation refusal banners of ITEM-STATE-COMMAND link to these three affordances (F2).
- Unsaved changes: leaving the route with pending changes asks "unsaved-leave". Closing a drawer or picker discards only its draft.
- Server-to-field mapping: by the routing of ITEM-SAVE (F2). A code whose field cannot be told from the response marks every field changed in the rejected call.
<!-- SUB:F3-SCR-CAT-001:END -->

<!-- SUB:F3-SCR-CAT-002:START traces=REQ-CAT-136,API-CAT-053,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-039,API-CAT-040,SCR-CAT-002 -->
### F3 · SCR-CAT-002 — Categories

| Field | Validator | Message |
|---|---|---|
| code (create) | required · 1–30 Latin letters, digits or hyphens | field hint "code-format" (below) · server duplicate: RULE-CAT-024 |

An inactive node opens with every field read-only and Save hidden; only Reactivate is offered, because the server refuses a PUT on an inactive Category (CAT-400-REQUEST-INVALID — backend plan, update of an inactive record).
| nameAr | required · 1–200 · ≥ 1 Arabic letter | RULE-CAT-001 |
| nameEn | ≤ 200 | RULE-CAT-002 |
| parentCategoryFk | optional · active · not the node itself nor one of its sub-categories · resulting depth ≤ 5 | RULE-CAT-023 |
| sortOrder | integer ≥ 0 (input `min`) · default next under the parent | — |

Copy "code-format": ar «من 1 إلى 30 حرفًا لاتينيًا أو رقمًا أو شرطة.» · en "1 to 30 Latin letters, digits or hyphens." (UX copy restating RULE-CAT-024's format; not a catalogue message).
<!-- SUB:F3-SCR-CAT-002:END -->

<!-- SUB:F3-SCR-CAT-003:START traces=REQ-CAT-137,REQ-CAT-138,API-CAT-054,API-CAT-055,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-041,UXD-CAT-002,SCR-CAT-003 -->
### F3 · SCR-CAT-003 — Variant attributes

| Field | Validator | Message |
|---|---|---|
| attribute code (create) | required · 1–30 Latin letters, digits or hyphens | RULE-CAT-016 |
| attribute nameAr | required · 1–200 · ≥ 1 Arabic letter | RULE-CAT-001 |
| attribute nameEn | ≤ 200 | RULE-CAT-002 |
| value code (new line) | required · 1–30 Latin letters, digits or hyphens · not repeated among this attribute's lines | RULE-CAT-016 |
| value nameAr | required · 1–200 · ≥ 1 Arabic letter · not repeated among this attribute's active lines | RULE-CAT-001 · RULE-CAT-016 |
| value nameEn | ≤ 200 | RULE-CAT-002 |
| sortOrder (attribute and value) | integer ≥ 0 (input `min`) · default next | — |

The values editor validates its draft set before Confirm returns it. The entry's Save validates the whole form again.
<!-- SUB:F3-SCR-CAT-003:END -->

<!-- SUB:F3-SCR-CAT-004:START traces=REQ-CAT-139,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,API-CAT-048,API-CAT-049,SCR-CAT-004 -->
### F3 · SCR-CAT-004 — Item import

| Field | Validator | Message |
|---|---|---|
| file | required · extension `.xlsx` · ≤ 10 MB | RULE-CAT-044 |

The row count (≤ 5,000), the header row and every row rule are checked by the server only (RULE-CAT-044, RULE-CAT-045). The client never parses the sheet.
<!-- SUB:F3-SCR-CAT-004:END -->

<!-- SUB:F3-SCR-CAT-005:START traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-148,API-CAT-007,API-CAT-042,API-CAT-050,API-CAT-056,SCR-CAT-005 -->
### F3 · SCR-CAT-005 — Barcode labels

| Field | Validator | Message |
|---|---|---|
| lines | ≥ 1 line (Print is disabled while empty) | — |
| copies (per line) | integer 1–999 | RULE-CAT-036 |
| total copies | ≤ 5,000 | RULE-CAT-036 |
| barcode (per line) | lines with an empty label barcode (no active GTIN or INTERNAL barcode in the base unit — ADR-CAT-079) block Print and are flagged; each flagged line offers "Generate internal barcode" (REQ-CAT-148), which clears the flag on success | RULE-CAT-037 ({count} filled with the flagged lines) |
<!-- SUB:F3-SCR-CAT-005:END -->

<!-- SUB:F3-SCR-CAT-006:START traces=API-CAT-052,REQ-CAT-134,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-045,API-CAT-051,UXD-CAT-001,SCR-CAT-006 -->
### F3 · SCR-CAT-006 — Catalogue settings

Layout drawer:

| Field | Validator | Message |
|---|---|---|
| key (create) | two digits, `02` or `20`–`29` · not the key of another active layout (from SETTINGS-QUERY) | RULE-CAT-034 |
| scaleCodeLength (create) | integer 4–6 (default 5); read-only after creation (ADR-CAT-064) | RULE-CAT-034 |
| embeddedValueTypeCode | required · an active option (default WEIGHT) | RULE-CAT-006 |
| valueDecimals | integer 0–3 (default 3) | RULE-CAT-034 |
| valueAr, valueEn | input `maxLength` 200 | — |

Unit e-invoice code drawer:

| Field | Validator | Message |
|---|---|---|
| key (create) | required · an active CAT_UOM option · no active Unit e-invoice code with that key (from SETTINGS-QUERY) | RULE-CAT-006 · RULE-CAT-043 |
| einvoiceUnitCode | required · an active CAT_EINVOICE_UNIT option | RULE-CAT-006 |

Turn-off check: the flag is required and is one of `variants`, `bundles` (a two-option segmented control, so no message).
<!-- SUB:F3-SCR-CAT-006:END -->
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=REQ-CAT-148,API-CAT-056,REQ-CAT-139,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,API-CAT-053,API-CAT-054,API-CAT-055,REQ-CAT-135,API-CAT-052,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-096,REQ-CAT-097,REQ-CAT-098,REQ-CAT-099,REQ-CAT-100,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-038,API-CAT-039,API-CAT-040,API-CAT-041,API-CAT-042,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,API-CAT-048,API-CAT-049,API-CAT-050,API-CAT-051,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001,SCR-CAT-002,SCR-CAT-003,SCR-CAT-004,SCR-CAT-005,SCR-CAT-006 -->
## F4 — Screens & Routes

Route module: `/cat/*`, registered in the platform shell under the Catalog menu. Each composite screen is one lazy chunk (`cat-items`, `cat-categories`, `cat-variant-attributes`, `cat-item-import`, `cat-barcode-labels`, `cat-settings`). Every route element is wrapped in the permission guard of its page. Open drawers and second levels are search params of the route, so:
- going back closes them;
- dismissing closes only the topmost one;
- a deep link opens them.

Second levels render as siblings of the first level at the route outlet, never inside its element. They have no scroll region of their own: their list scrolls with the drawer body.

| Route | Screen | Guard | Lazy chunk |
|---|---|---|---|
| `/cat/items` | SCR-CAT-001 Search | PERM_CAT_ITEMS_VIEW | cat-items |
| `/cat/items/new` | SCR-CAT-001 Entry | PERM_CAT_ITEMS_CREATE | cat-items |
| `/cat/items/:itemPk` | SCR-CAT-001 Entry | PERM_CAT_ITEMS_VIEW | cat-items |
| `/cat/categories` | SCR-CAT-002 | PERM_CAT_CATEGORIES_VIEW | cat-categories |
| `/cat/variant-attributes` | SCR-CAT-003 | PERM_CAT_VARIANT_ATTRIBUTES_VIEW (+ `variants` on, else the RULE-CAT-008 state) | cat-variant-attributes |
| `/cat/item-import` | SCR-CAT-004 | PERM_CAT_ITEM_IMPORT_VIEW | cat-item-import |
| `/cat/barcode-labels` | SCR-CAT-005 | PERM_CAT_BARCODE_LABELS_VIEW | cat-barcode-labels |
| `/cat/settings` | SCR-CAT-006 | PERM_CAT_SETTINGS_VIEW | cat-settings |

<!-- SUB:F4-SCR-CAT-001:START traces=API-CAT-053,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-135,API-CAT-052,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-096,REQ-CAT-097,REQ-CAT-099,REQ-CAT-100,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-038,API-CAT-039,API-CAT-041,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001 -->
### F4 · SCR-CAT-001 — Items

#### ITEMS-SCREEN — SCR-CAT-001            traces=REQ-CAT-001,REQ-CAT-003,REQ-CAT-014,REQ-CAT-015,REQ-CAT-026,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,API-CAT-001,API-CAT-002,API-CAT-038
- **Guard**: `/cat/items` and `/cat/items/:itemPk` need PERM_CAT_ITEMS_VIEW; `/cat/items/new` needs PERM_CAT_ITEMS_CREATE. A missing permission redirects to the unauthorized view. The names come from the SRS B4 matrix and the backend registry.
- **Facade**: ITEM-FACADE only; pages call no query directly.
- **Cross-module**:
  - every lookup-backed field, filter and column → UXD-CAT-001, read through `API-CAT-052` (ADR-CAT-065);
  - the Variants pane, attribute fields, Item kind field and filter, and Bundle components pane → UXD-CAT-002;
  - image thumbnails → UXD-CAT-003.
- **Components**:
  - Search: `ItemsSearchPage` (filter bar bound to the URL, "Find by barcode" field, results table, paging, "New item", "Import").
  - Entry: `ItemEntryPage` (header with badges, Advanced section, panes `VariantsPane`, `UnitsPane`, `BarcodesPane`, `BundleComponentsPane`, `ImagesPane`, command bar, Save bar).
  - Exported: `catalogueSearchProvider` (CATALOGUE-SEARCH-QUERY) for the shell's global search.
- **Composition** (spec line: container page · every child collection inline · two pickers as second levels):
  - the panes are inline sections of the page body, in the body's own scroll;
  - a line is added or edited in `ItemLineDrawer` (`?drawer=unit:new`, `unit:<itemUnitPk>`, `barcode:new&variant=<variantPk>`, `internal-barcode:new&variant=<variantPk>`, `override:<variantPk>`, `image:new`, `image:<itemMediaPk>`) — a one-line drawer whose Confirm returns the line to `ItemForm`;
  - `VariantGenerationPicker` (`?picker=variant-generation`) and `BundleComponentPicker` (`?picker=bundle-components`) are their own components, rendered as siblings of `ItemEntryPage` at the route outlet and opened from the search params, not from a boolean the page holds. Their Confirm writes the selection into `ItemForm`; their Cancel discards the draft;
  - a pending generation shows as a summary row in `VariantsPane` with an "Edit" trigger;
  - no inline control is taller than the fields beside it; the image grid uses fixed-size thumbnails.
- **Saves**: ONE (`ITEM-SAVE`). It owns ordered calls (F2): each is sent only after the previous succeeded, and a part whose lines are unchanged is skipped (ADR-CAT-049). The record commands (Deactivate, Reactivate, Publish, Unpublish) are not saves. They are enabled only while `isDirty` is false.
- **Navigation out**:
  - "Print labels" on a Variant line → `/cat/barcode-labels?variantPk=<variantPk>`;
  - "Open Variant attributes" (picker, no active attribute) → `/cat/variant-attributes?attribute=new&returnTo=<this route>`;
  - "Import" → `/cat/item-import`;
  - RULE-CAT-003's next action → `/cat/items?text=<code>&isActiveFl=true`, `<code>` being the existing Item's code the server put in the message (fallback `text=<nameAr>` — F2 ITEM-SAVE);
  - the reactivation refusals of RULE-CAT-050, 026 and 019 → the repair affordance on the same Entry (`?picker=variant-generation`, the Category field, `?picker=bundle-components`), and, for RULE-CAT-026 with PERM_CAT_CATEGORIES_VIEW, `/cat/categories?node=<categoryFk>` (ADR-CAT-068, ADR-CAT-069).
- **States**: the Search empty and no-match copy; Entry not-found (CAT-404-NOT-FOUND) with "Back to search"; a skeleton per pane while loading; a pane-level error with Try again.
<!-- SUB:F4-SCR-CAT-001:END -->

<!-- SUB:F4-SCR-CAT-002:START traces=REQ-CAT-136,API-CAT-053,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-039,API-CAT-040,SCR-CAT-002 -->
### F4 · SCR-CAT-002 — Categories

#### CATEGORIES-SCREEN — SCR-CAT-002            traces=REQ-CAT-064,REQ-CAT-067,REQ-CAT-068,API-CAT-039,API-CAT-022,API-CAT-040
- **Guard**: `/cat/categories` needs PERM_CAT_CATEGORIES_VIEW. "New category" needs CREATE, Save on an existing node needs UPDATE, and Deactivate needs DELETE.
- **Facade**: CATEGORY-FACADE only.
- **Cross-module**: none.
- **Components**: `CategoriesPage` (filter bar, `CategoryTree`), `CategoryEntryDrawer` (`?node=`), and `CategoryItemsDrawer` (`&items=open`), the second level.
- **Composition**: container drawer. The Category record has no child collection. "Items of this category" is a summary row in the entry drawer (count + 3 names + "Show") that opens `CategoryItemsDrawer`. That drawer is a sibling of the entry drawer, opened from the search params; it is read-only and returns nothing.
- **Saves**: ONE per drawer (`CATEGORY-SAVE`). Deactivate (active node) and Reactivate (inactive node — `CATEGORY-REACTIVATE`) are record commands.
- **Navigation out**: a row of `CategoryItemsDrawer` → `/cat/items/:itemPk`; "Items directly in this category / الأصناف المباشرة في هذه الفئة" → `/cat/items?categoryFk=<categoryPk>`. The jump is labelled apart from "Items of this category": the drawer's count covers the active sub-tree (API-CAT-040), while the Items search filters on the exact `categoryFk` (API-CAT-038), so the two lists differ for a Category with sub-categories. No API change; widening API-CAT-038 to the sub-tree is left to a later delta.
<!-- SUB:F4-SCR-CAT-002:END -->

<!-- SUB:F4-SCR-CAT-003:START traces=REQ-CAT-137,REQ-CAT-138,API-CAT-054,API-CAT-055,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-041,UXD-CAT-002,SCR-CAT-003 -->
### F4 · SCR-CAT-003 — Variant attributes

#### VARIANT-ATTRIBUTES-SCREEN — SCR-CAT-003            traces=REQ-CAT-014,REQ-CAT-016,REQ-CAT-048,UXD-CAT-002,API-CAT-041,API-CAT-026
- **Guard**: `/cat/variant-attributes` needs PERM_CAT_VARIANT_ATTRIBUTES_VIEW. While `variants` is off, the route renders the RULE-CAT-008 state (text with «variants», next action "Catalogue capability settings" is the platform settings' entry, not a CAT route) and its menu entry is absent (REQ-CAT-014).
- **Facade**: ATTRIBUTE-FACADE only.
- **Cross-module**: `variants` flag → UXD-CAT-002.
- **Components**: `VariantAttributesPage` (filter bar, list), `AttributeEntryDrawer` (`?attribute=`), and `AttributeValuesDrawer` (`&values=open`), the second level.
- **Composition**: container drawer. The values are a summary row in the entry drawer (active count + first 5 names + "Edit values"). `AttributeValuesDrawer` is a sibling of the entry drawer, opened from the search params; its lines scroll with its body. Confirm returns the set to the entry's form state, and Cancel discards the draft.
- **Saves**: ONE per entry drawer (`ATTRIBUTE-SAVE`). It owns the attribute call, then the value calls, ordered: the value calls are sent only after the attribute call succeeded, and they are skipped when the value set is unchanged. Attribute Deactivate and Reactivate (`ATTRIBUTE-REACTIVATE`) are record commands; a value line's Reactivate is a pending line state sent by the Save (ADR-CAT-069).
- **Navigation out**: after Save, when `returnTo` is set → back to that Items Entry route, which re-reads the attribute options.
<!-- SUB:F4-SCR-CAT-003:END -->

<!-- SUB:F4-SCR-CAT-004:START traces=REQ-CAT-139,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,API-CAT-048,API-CAT-049,SCR-CAT-004 -->
### F4 · SCR-CAT-004 — Item import

#### ITEM-IMPORT-SCREEN — SCR-CAT-004            traces=REQ-CAT-036,REQ-CAT-039,API-CAT-048,API-CAT-049
- **Guard**: `/cat/item-import` needs PERM_CAT_ITEM_IMPORT_VIEW; the "Import" action needs PERM_CAT_ITEM_IMPORT_CREATE.
- **Facade**: IMPORT-FACADE only.
- **Cross-module**: none.
- **Components**: `ItemImportPage` (the three-step guide, "Download import sheet", file input, "Import", result panel with counts and the rejected-rows table). It is also exported as `ItemImportStep` for the setup wizard, with the same component and guard.
- **Composition**: container page. The import file has no secondary detail. The rejected rows are inline under the counts, read-only, in the page's own scroll.
- **Saves**: ONE (`IMPORT-SUBMIT`). Downloading the sheet is a read.
- **Navigation out**: "Open Items" → `/cat/items`.
<!-- SUB:F4-SCR-CAT-004:END -->

<!-- SUB:F4-SCR-CAT-005:START traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-148,API-CAT-007,API-CAT-042,API-CAT-050,API-CAT-056,SCR-CAT-005 -->
### F4 · SCR-CAT-005 — Barcode labels

#### BARCODE-LABELS-SCREEN — SCR-CAT-005            traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-104,API-CAT-042,API-CAT-007,API-CAT-050
- **Guard**: `/cat/barcode-labels` needs PERM_CAT_BARCODE_LABELS_VIEW; "Print" needs PERM_CAT_BARCODE_LABELS_CREATE.
- **Facade**: LABEL-FACADE only.
- **Cross-module**: none.
- **Components**: `BarcodeLabelsPage` (lines table with copies inputs and total, "Generate internal barcode" on a flagged line, "Add variants", "Print") and `VariantPickerDrawer` (`?picker=variants&text=`), the second level.
- **Composition**: container page. The label lines are inline in a pane of the page body. `VariantPickerDrawer` is a sibling of the page, opened from the search params. It returns the chosen Variants to the lines and saves nothing.
- **Saves**: ONE (`LABEL-PRINT`). It opens the PDF in a new tab. "Generate internal barcode" on a flagged line (`LABEL-INTERNAL-BARCODE`, API-CAT-056) is a record command on that line, not a save of the form (ADR-CAT-078).
- **Navigation out**: "Open item" on a flagged line → `/cat/items/:itemPk` (with PERM_CAT_ITEMS_VIEW).
<!-- SUB:F4-SCR-CAT-005:END -->

<!-- SUB:F4-SCR-CAT-006:START traces=API-CAT-052,REQ-CAT-134,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-045,API-CAT-051,UXD-CAT-001,SCR-CAT-006 -->
### F4 · SCR-CAT-006 — Catalogue settings

#### CATALOGUE-SETTINGS-SCREEN — SCR-CAT-006            traces=REQ-CAT-095,REQ-CAT-021,UXD-CAT-001,API-CAT-045,API-CAT-051
- **Guard**: `/cat/settings` needs PERM_CAT_SETTINGS_VIEW. "New layout" and "New unit code" need CREATE, Save on an existing entry needs UPDATE, and Deactivate needs DELETE.
- **Facade**: SETTINGS-FACADE only.
- **Cross-module**: embeddedValueTypeCode, key (CAT_UOM) and einvoiceUnitCode → UXD-CAT-001.
- **Components**: `CatalogueSettingsPage` (the two lists and the turn-off check panel), `ScaleLayoutDrawer` (`?layout=`), `UnitEinvoiceCodeDrawer` (`?unitCode=`).
- **Composition**: container drawer. Each drawer edits one record with no secondary detail. The turn-off check is a read panel on the page with no save.
- **Saves**: ONE per drawer (`LAYOUT-SAVE`, `UNIT-CODE-SAVE`). Deactivate is a record command in its drawer.
- **Navigation out**: a dependent Item of the check → `/cat/items/:itemPk`.
<!-- SUB:F4-SCR-CAT-006:END -->
<!-- PHASE:F4:END -->

<!-- PHASE:SEC-FE:START traces=REQ-CAT-131,REQ-CAT-148,API-CAT-056,SCR-CAT-001,SCR-CAT-002,SCR-CAT-003,SCR-CAT-004,SCR-CAT-005,SCR-CAT-006,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-127,API-CAT-001,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-009,API-CAT-010,API-CAT-018,API-CAT-047,API-CAT-049,API-CAT-050 -->
## SEC-FE

Permission names are the backend registry's (`PERM_<PAGE_CODE>_<ACTION>`, derived by the given `sec` module from the six CAT page codes). None is redeclared here. The user's permission set is platform session context (ADR-CAT-050 §Consequences). Rules for every screen:
- **Navigation**: a route without its VIEW permission redirects to the unauthorized view, and its menu entry is hidden.
- **Actions**: an action whose permission is missing is hidden. Fields the user may not change are read-only.
- **Forbidden responses**: a CAT-403-FORBIDDEN response is shown as the localized catalogue message from the envelope. On an auxiliary read it hides the affordance that read feeds (ADR-CAT-051 §4).

| SCR | Guard (no VIEW → unauthorized) | no CREATE → hidden | no UPDATE → read-only / hidden | no DELETE → hidden |
|---|---|---|---|---|
| SCR-CAT-001 | PERM_CAT_ITEMS_VIEW | "New item", "Add unit", "Add barcode", "Generate internal barcode", "Generate variants", "Add image" (`API-CAT-001`, 010, 014, 015, 006, 018) | header fields read-only, Save hidden on an existing Item; unit flags, Variant override, image changes, Bundle components, Reactivate / Publish / Unpublish hidden (`API-CAT-003`, 012, 008, 019, 017, 005) | Deactivate on the Item, Variant, unit, barcode and image lines (`API-CAT-004`, 009, 013, 016, 020) |
| SCR-CAT-002 | PERM_CAT_CATEGORIES_VIEW | "New category", "New sub-category" | entry fields read-only, Save and Reactivate hidden (`API-CAT-053`) | Deactivate |
| SCR-CAT-003 | PERM_CAT_VARIANT_ATTRIBUTES_VIEW (+ `variants` on) | "New attribute", "Add value" | entry and value fields read-only, Save and Reactivate (attribute, value line) hidden (`API-CAT-054`, 055) | Deactivate (attribute, value line) |
| SCR-CAT-004 | PERM_CAT_ITEM_IMPORT_VIEW | "Import" (`API-CAT-049`); "Download import sheet" stays | — (no UPDATE action) | — (no DELETE action) |
| SCR-CAT-005 | PERM_CAT_BARCODE_LABELS_VIEW | "Print" (`API-CAT-050`), "Generate internal barcode" on a flagged line (`API-CAT-056`) | — | — |
| SCR-CAT-006 | PERM_CAT_SETTINGS_VIEW | "New layout", "New unit code" | drawer fields read-only, Save hidden | Deactivate (layout, unit code) |

Further rules:
- **Mixed permissions inside the Items Save**: a call is never queued for an action the user cannot take, because the affordance is hidden. A 403 on a queued call stops the sequence like any rejection (ADR-CAT-049 §4).
- **Capability flags are not permissions**: flag-bound affordances are absent while their flag is off (REQ-CAT-014, REQ-CAT-015). The server still refuses a flag-off call (`CAT-422-CAPABILITY-OFF`, REQ-CAT-016), and that refusal is routed as F2 states.
- **Jumps to other pages**: a jump to another CAT page is shown only when the user holds that page's VIEW permission and, when that page is bound to a capability flag, only while the flag is on. On SCR-CAT-001 this governs "Print labels" (PERM_CAT_BARCODE_LABELS_VIEW), "Import" (PERM_CAT_ITEM_IMPORT_VIEW) and "Open Variant attributes" (PERM_CAT_VARIANT_ATTRIBUTES_VIEW and `variants` on); the same rule governs "Open item" and the dependent-Item links (PERM_CAT_ITEMS_VIEW) and "Items directly in this category" (PERM_CAT_ITEMS_VIEW) on the other screens. A user who lacks the target page's VIEW never reaches the unauthorized view through a CAT jump.
- **Global search**: `catalogueSearchProvider` registers only for a user holding PERM_CAT_ITEMS_VIEW. The server returns no Item results without it (REQ-CAT-127).
- **Tokens**: the bearer token is attached by the platform HTTP client. CAT code never reads or stores it. A CAT-401-UNAUTHENTICATED response goes to login and returns to the same route afterwards.
<!-- PHASE:SEC-FE:END -->

<!-- PHASE:ALIGN-FE:START traces=API-CAT-056,API-CAT-053,API-CAT-054,API-CAT-055,API-CAT-052,API-CAT-001,API-CAT-002,API-CAT-003,API-CAT-004,API-CAT-005,API-CAT-006,API-CAT-007,API-CAT-008,API-CAT-009,API-CAT-010,API-CAT-012,API-CAT-013,API-CAT-014,API-CAT-015,API-CAT-016,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-020,API-CAT-021,API-CAT-022,API-CAT-023,API-CAT-024,API-CAT-025,API-CAT-026,API-CAT-027,API-CAT-028,API-CAT-029,API-CAT-030,API-CAT-031,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-038,API-CAT-039,API-CAT-040,API-CAT-041,API-CAT-042,API-CAT-043,API-CAT-044,API-CAT-045,API-CAT-047,API-CAT-048,API-CAT-049,API-CAT-050,API-CAT-051,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,SCR-CAT-001,SCR-CAT-002,SCR-CAT-003,SCR-CAT-004,SCR-CAT-005,SCR-CAT-006 -->
## ALIGN-FE

The checks run against the plan itself, the API document and the SRS ceiling. The cross-artifact checks are `gov.py analyze`'s. Each row names the check that backs it. The result column is copied by the orchestrator from the analyze report; this author writes no mark of their own.

```
ALIGN — CAT v2
row           backing check       assertion                                                                                       result
SCREENS       orphans             every SCR is referenced by a plan block                                                         (stamped from the analyze report)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once                          (stamped from the analyze report)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it        (stamped from the analyze report)
READS         ux-reads-spec       every read a screen binds is an operation of api-spec-cat.yaml, and the plan names the document its mock server serves   (stamped from the analyze report)
UXD           orphans             every UXD is cited by a plan block — this is where a UX decision closes                         (stamped from the analyze report)
TRACES        traces              every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD       (stamped from the analyze report)
API           traces              every API this plan cites is an operation of api-spec-cat.yaml — never a line of the backend plan's prose   (stamped from the analyze report)
FOREIGN       xref-surface        every reference to another module's surface resolves in that module's own artifacts             (stamped from the analyze report)
REGISTRY      registry-agree      every UXD and SCR defined here is in the stage registry, and nothing else is                    (stamped from the analyze report)
LANGUAGES     languages           labels and messages in ar + en                                                                  (stamped from the analyze report)
MARKERS       markers             the parser reports no structural or semantic error for this track and plan                      (stamped from the analyze report)
DECISIONS     refs-exist          every ADR this plan cites exists on disk in analysis/decisions/CAT/                             (stamped from the analyze report)
COVERAGE      (the report)        the clauses the analyze report lists as having examined nothing — verbatim, or `none`           (stamped from the analyze report)
```

```yaml name=self-check
findings: 0
clean: true
```

Operations coverage (56 operations of `api-spec-cat.yaml`):

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| create Item | API-CAT-001 | SCR-CAT-001 Save (new) | `/cat/items/new` | ✓ |
| read Item | API-CAT-002 | SCR-CAT-001 open Entry; component picker expand | `/cat/items/:itemPk` | ✓ |
| update Item | API-CAT-003 | SCR-CAT-001 Save (header) | `/cat/items/:itemPk` | ✓ |
| deactivate Item | API-CAT-004 | SCR-CAT-001 Deactivate | `/cat/items/:itemPk` | ✓ |
| reactivate / publish / unpublish Item | API-CAT-005 | SCR-CAT-001 Reactivate · Publish · Unpublish | `/cat/items/:itemPk` | ✓ |
| generate Variants | API-CAT-006 | SCR-CAT-001 Save (pending generation) | `/cat/items/:itemPk?picker=variant-generation` | ✓ |
| read Variant | API-CAT-007 | SCR-CAT-001 component line label · SCR-CAT-005 seeded line | `/cat/items/:itemPk` · `/cat/barcode-labels?variantPk=` | ✓ |
| update Variant e-invoice override | API-CAT-008 | SCR-CAT-001 Save (override line) | `/cat/items/:itemPk?drawer=override:<variantPk>` | ✓ |
| deactivate Variant | API-CAT-009 | SCR-CAT-001 Save (Variant line marked) | `/cat/items/:itemPk` | ✓ |
| add Item unit | API-CAT-010 | SCR-CAT-001 Save (new unit line) | `/cat/items/:itemPk?drawer=unit:new` | ✓ |
| read Item unit | API-CAT-011 | — (read inside API-CAT-002; consumers' read — ADR-CAT-051) | — | ✗ ADR-CAT-051 |
| update Item unit | API-CAT-012 | SCR-CAT-001 Save (unit flags) | `/cat/items/:itemPk` | ✓ |
| deactivate Item unit | API-CAT-013 | SCR-CAT-001 Save (unit line marked) | `/cat/items/:itemPk` | ✓ |
| add barcode (GTIN or scale code) | API-CAT-014 | SCR-CAT-001 Save (new barcode line) | `/cat/items/:itemPk?drawer=barcode:new` | ✓ |
| generate internal barcode | API-CAT-015 | SCR-CAT-001 Save (internal barcode line) | `/cat/items/:itemPk?drawer=internal-barcode:new` | ✓ |
| deactivate barcode | API-CAT-016 | SCR-CAT-001 Save (barcode line marked) | `/cat/items/:itemPk` | ✓ |
| save Bundle components | API-CAT-017 | SCR-CAT-001 Save (component set changed) | `/cat/items/:itemPk?picker=bundle-components` | ✓ |
| upload image | API-CAT-018 | SCR-CAT-001 Save (new image line, after the given FILE upload) | `/cat/items/:itemPk?drawer=image:new` | ✓ |
| update image | API-CAT-019 | SCR-CAT-001 Save (image line changed) | `/cat/items/:itemPk?drawer=image:<itemMediaPk>` | ✓ |
| remove image | API-CAT-020 | SCR-CAT-001 Save (image line marked) | `/cat/items/:itemPk` | ✓ |
| create Category | API-CAT-021 | SCR-CAT-002 Save (new) | `/cat/categories?node=new` | ✓ |
| read Category | API-CAT-022 | SCR-CAT-002 open entry | `/cat/categories?node=<categoryPk>` | ✓ |
| update Category | API-CAT-023 | SCR-CAT-002 Save | `/cat/categories?node=<categoryPk>` | ✓ |
| deactivate Category | API-CAT-024 | SCR-CAT-002 Deactivate | `/cat/categories?node=<categoryPk>` | ✓ |
| reactivate Category | API-CAT-053 | SCR-CAT-002 Reactivate | `/cat/categories?node=<categoryPk>` | ✓ |
| create Variant attribute | API-CAT-025 | SCR-CAT-003 Save (new) | `/cat/variant-attributes?attribute=new` | ✓ |
| read Variant attribute | API-CAT-026 | SCR-CAT-003 open entry | `/cat/variant-attributes?attribute=<pk>` | ✓ |
| update Variant attribute | API-CAT-027 | SCR-CAT-003 Save | `/cat/variant-attributes?attribute=<pk>` | ✓ |
| deactivate Variant attribute | API-CAT-028 | SCR-CAT-003 Deactivate | `/cat/variant-attributes?attribute=<pk>` | ✓ |
| create Variant attribute value | API-CAT-029 | SCR-CAT-003 Save (new value line) | `/cat/variant-attributes?attribute=<pk>&values=open` | ✓ |
| update Variant attribute value | API-CAT-030 | SCR-CAT-003 Save (value line changed) | `/cat/variant-attributes?attribute=<pk>&values=open` | ✓ |
| deactivate Variant attribute value | API-CAT-031 | SCR-CAT-003 Save (value line marked) | `/cat/variant-attributes?attribute=<pk>&values=open` | ✓ |
| reactivate Variant attribute | API-CAT-054 | SCR-CAT-003 Reactivate | `/cat/variant-attributes?attribute=<pk>` | ✓ |
| reactivate Variant attribute value | API-CAT-055 | SCR-CAT-003 Save (value line marked reactivate) | `/cat/variant-attributes?attribute=<pk>&values=open` | ✓ |
| create scale label layout | API-CAT-032 | SCR-CAT-006 layout Save (new) | `/cat/settings?layout=new` | ✓ |
| update scale label layout | API-CAT-033 | SCR-CAT-006 layout Save | `/cat/settings?layout=<pk>` | ✓ |
| deactivate scale label layout | API-CAT-034 | SCR-CAT-006 layout Deactivate | `/cat/settings?layout=<pk>` | ✓ |
| create Unit e-invoice code | API-CAT-035 | SCR-CAT-006 unit code Save (new) | `/cat/settings?unitCode=new` | ✓ |
| update Unit e-invoice code | API-CAT-036 | SCR-CAT-006 unit code Save | `/cat/settings?unitCode=<pk>` | ✓ |
| deactivate Unit e-invoice code | API-CAT-037 | SCR-CAT-006 unit code Deactivate | `/cat/settings?unitCode=<pk>` | ✓ |
| search Items | API-CAT-038 | SCR-CAT-001 Search; component picker | `/cat/items` | ✓ |
| list Category tree | API-CAT-039 | SCR-CAT-002 tree; SCR-CAT-001 Category options | `/cat/categories` · `/cat/items` | ✓ |
| Items of a Category | API-CAT-040 | SCR-CAT-002 Items of this category | `/cat/categories?node=<pk>&items=open` | ✓ |
| list Variant attributes with values | API-CAT-041 | SCR-CAT-003 list; SCR-CAT-001 generation picker | `/cat/variant-attributes` · `/cat/items/:itemPk?picker=variant-generation` | ✓ |
| search Variants | API-CAT-042 | SCR-CAT-005 Variant picker | `/cat/barcode-labels?picker=variants` | ✓ |
| barcode and scale label lookup | API-CAT-043 | SCR-CAT-001 Find by barcode | `/cat/items` | ✓ |
| images of a Variant | API-CAT-044 | SCR-CAT-001 Variant row expand | `/cat/items/:itemPk` | ✓ |
| read catalogue settings | API-CAT-045 | SCR-CAT-006 lists; SCR-CAT-001 SCALE availability | `/cat/settings` · `/cat/items/:itemPk` | ✓ |
| storefront catalogue read | API-CAT-046 | — (SFR consumer — ADR-CAT-051) | — | ✗ ADR-CAT-051 |
| catalogue search (global search) | API-CAT-047 | SCR-CAT-001 exported global search provider → hit | `/cat/items/:itemPk` | ✓ |
| download import sheet | API-CAT-048 | SCR-CAT-004 Download import sheet | `/cat/item-import` | ✓ |
| import items | API-CAT-049 | SCR-CAT-004 Import (after the given FILE upload) | `/cat/item-import` | ✓ |
| print barcode labels | API-CAT-050 | SCR-CAT-005 Print | `/cat/barcode-labels` | ✓ |
| generate the missing internal barcode of a label line | API-CAT-056 | SCR-CAT-005 Generate internal barcode (flagged line) | `/cat/barcode-labels` | ✓ |
| capability turn-off check | API-CAT-051 | SCR-CAT-006 Check | `/cat/settings?check=<flag>` | ✓ |
| active values of a CAT lookup key | API-CAT-052 | every `*-LOOKUP` hook: SCR-CAT-001 lookup fields, filters and columns; SCR-CAT-006 drawers | `/cat/items` · `/cat/items/:itemPk` · `/cat/settings` | ✓ |

Totals: 54 of 56 operations have a screen action and a route. API-CAT-011 and API-CAT-046 have neither, by decision (ADR-CAT-051).
<!-- PHASE:ALIGN-FE:END -->

## Index

| Phase | SUBs |
|---|---|
| F1 — Models & Types | F1-SCR-CAT-001 … F1-SCR-CAT-006 |
| F2 — Data Hooks | F2-SCR-CAT-001 … F2-SCR-CAT-006 (one `screen-hooks` block each) |
| F3 — Forms & Validators | F3-SCR-CAT-001 … F3-SCR-CAT-006 |
| F4 — Screens & Routes | F4-SCR-CAT-001 … F4-SCR-CAT-006 |
| SEC-FE | — (never split) |
| ALIGN-FE | — (never split) |

Registry: `registry-exec-fe-cat.md` (same folder).

## Hand-off

After the `P4` verdict, the toolkit splits this plan and its registry into `/frontend-execution/` of the shared repo, and the result is tagged `cat-v2`. The implementer:
- reads the plan in profile-phase order (F1 → F2 → F3 → F4 → SEC-FE → ALIGN-FE);
- reads `ui-ux-spec-cat.md` for intent;
- reads `api-spec-cat.yaml` for every shape, served by its mock server until the backend is delivered;
- does not invent a route, component, permission or field that no F-block traces. A gap is raised as an ADR in `analysis/decisions/CAT/`.
