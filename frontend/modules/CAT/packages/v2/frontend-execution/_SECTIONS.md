<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
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
