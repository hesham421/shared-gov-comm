<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# Frontend execution plan — Inventory / المخزون (INV)
══════════════════════════════════════════════════════════════════
Module : INV   Version : v1   Stage : P3.2 Part B (frontend execution plan)   Profile : commerce
Track  : frontend · plan exec · stack react-ts-vite (react-router · tanstack-query · react-hook-form · zod · zustand)
Bound  : api-spec-inv.yaml (OpenAPI 3.1.0, 52 operations API-INV-001 … 052) — the document a mock server serves
Design : ui-ux-spec-inv.md (SCR-INV-001 … 012, UXD-INV-001 … 011) · flow-diagram-inv.md
ADRs   : ADR-INV-033 (reads outside the document) · ADR-INV-034 (count commits per line) · ADR-INV-035 (browser reads
         the opening stock sheet — barcode only in v1) · ADR-INV-036 (balance labels for a Seller/Cashier — the
         warehouse label is on the row, closed at gate-analysis revise 1) · cited from P3.1 at gate-analysis revise 2:
         ADR-INV-046 (a line refusal names its line in `details`), ADR-INV-047 (OPENING_STOCK not on the adjustment
         screen); ADR-INV-033 amended (INV lookup values through API-INV-052) · cited from gate-analysis revise 3:
         ADR-INV-051 (a line without a lot within the lot-less stock), ADR-INV-053 (one idempotency key per opening
         stock load), ADR-INV-021 amended (every INV code has its own text) · cited from gate-analysis revise 4:
         ADR-INV-054 (the system measure of a posted dual-quantity count line is derived and shown)
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
  - {req: REQ-INV-075, api: [API-INV-018, API-INV-052]}
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
  - {req: REQ-INV-101, api: [API-INV-036, API-INV-052]}
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
  - {req: REQ-INV-139, api: [API-INV-022, API-INV-023, API-INV-024, API-INV-029, API-INV-032, API-INV-036, API-INV-037, API-INV-038]}
  - {req: REQ-INV-140, api: [API-INV-047]}
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
  - {code: "INV-422-DOC-DATE-FUTURE", rule: RULE-INV-051}
  - {code: "INV-409-IDEMPOTENCY-KEY-REUSED", rule: RULE-INV-050}
```

**Reconciliation against the SRS (once).**
- Every SRS B5 row of SCR-REQ-INV-001 … 012 has its operation in the document. The document adds API-INV-008
  (inventory context), which the SRS B5 of SCR-REQ-INV-001 lists. All 52 operations are bound to at least one
  screen action (operations coverage table, ALIGN-FE). No operation maps to no REQ.
- **Codes with no RULE.** These codes are routed with the catalogue text, by reference:
  - INV-400, INV-401, INV-403, INV-500, the `INV-404-*` not-found codes and INV-409-STALE-VERSION (ADR-INV-021,
    ADR-INV-032).
  - INV-422-HORIZON-RANGE: DEFAULT D-04; the text is that of AC-INV-133.
  - INV-422-LOAD-ROWS-INVALID: the wrapper of row failures; each `details[]` row carries its own code and text.
- **Reads that are not operations of the document** (ADR-INV-033):
  - CAT reads, as UXD-INV-001 … 011;
  - the session permissions, from the given `sec`.
  The INV_* lookup values are no longer such a read: INV serves its own six keys through API-INV-052 (`InvLookups`,
  ADR-INV-033 as amended at gate-analysis revise 2).
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
  - INV-403 → unauthorized view or message, text: catalogue INV-403 «ليست لديك صلاحية لهذا الإجراء.» /
    "You do not have permission for this action." (AC-INV-154);
  - INV-500 and network failures → generic retry message;
  - INV-409-STALE-VERSION → «reload» prompt with the catalogue text, which re-reads the record and keeps the
    user's unsaved input where it still applies;
  - `INV-404-*` → the catalogue text, and the drawer or route returns to the search.
- **Line refusals** (ADR-INV-046). A refusal raised by a rule on one line of a transfer, count or adjustment
  carries `details[0] {lineRef, lineRefKind, rowNo?, code, messageAr, messageEn}`. The line is found by
  `lineRefKind`: `LINE_PK` → the line with that identifier, `REQUEST_POSITION` → the n-th line the request sent;
  adjustment lines also carry `rowNo`. The code's text is placed on that line, its drawer opens on the field the code
  names, and the rest of the form is kept. With no `details`, the text goes to the lines pane.
- **Message binding.** In the F2 blocks, "text: RULE-INV-nnn message (SRS)" means the SRS A5 message of that RULE, in ar and en.
  It is the same text the server returns.
- **Filter state.** Filter objects carry `page` and `size` inside them. The filter object is the query key, and
  the URL search params mirror it, so a deep link restores the search.













## Hand-off

- The toolkit splits the plan and `registry-exec-fe-inv.md` into `/frontend-execution/` after the P4 verdict.
- The implementer reads the plan in phase order (F1 → F2 → F3 → F4 → SEC-FE → ALIGN-FE), takes intent from
  `ui-ux-spec-inv.md` and shapes from `api-spec-inv.yaml`. A mock server serves that document until the backend is
  delivered; the CAT, `mdl` and session fixtures come from ADR-INV-033.
- The implementer adds no route, component, permission or field that does not trace to an F-block. A gap is an
  ADR.
