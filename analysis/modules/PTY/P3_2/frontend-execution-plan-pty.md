# Frontend execution plan — Party / الأطراف (PTY) v1
══════════════════════════════════════════════════════════════════
Stage    : P3.2 Part B — frontend execution plan · Module : PTY · Version : v1 · Profile : commerce
Track    : frontend · Plan : exec · Lane : analysis
Bound to : api-spec-pty.yaml (OpenAPI 3.1.0, 37 operations, base path /api/v1/pty) — the API document derived from the P3.1 backend plan; served by the frontend executor's mock server until the backend is delivered
Design   : ui-ux-spec-pty.md (SCR-PTY-001 … 005, UXD-PTY-001) · flow-diagram-pty.md
Stack    : react-ts-vite · routing react-router · server state tanstack-query · forms react-hook-form · validation zod · client state zustand · one lazy chunk per composite screen
Decisions: ADR-PTY-033 · ADR-PTY-034 (raised here; §9 removed) · ADR-PTY-012, ADR-PTY-024, ADR-PTY-031, ADR-PTY-037, ADR-PTY-039, ADR-PTY-041, ADR-PTY-042, ADR-PTY-043 (cited) — gate-analysis revise (G1, G20 and the backend changes the screens follow)
══════════════════════════════════════════════════════════════════

Reading order for the implementer: the API binding below, then F1 → F2 → F3 → F4 → SEC-FE → ALIGN-FE. Shapes (paths, methods, parameters, request and response schemas, paging, envelope, error bodies) are read in api-spec-pty.yaml by `x-api-id`; this plan cites the `API-*` id and never restates a shape. A route, component, permission or field not traceable to a block of this plan is not built — a gap is an ADR.

## Binding to the API document

The plan is bound to `api-spec-pty.yaml`. The binding below is the REQ → API link as the document's `x-traces` state it; `codes` is the runtime catalog code → RULE link that neither the document nor the SRS carries alone (27 rule rows of the catalog; the 9 PLATFORM-STD rows, ADR-PTY-024, have no RULE and are routed by the standard routing of F2).

```yaml name=api-surface
mock: api-spec-pty.yaml
bindings:
  - {req: REQ-PTY-001, api: [API-PTY-001, API-PTY-002, API-PTY-026, API-PTY-034]}
  - {req: REQ-PTY-002, api: [API-PTY-002]}
  - {req: REQ-PTY-003, api: [API-PTY-002, API-PTY-007]}
  - {req: REQ-PTY-004, api: [API-PTY-001]}
  - {req: REQ-PTY-005, api: [API-PTY-001]}
  - {req: REQ-PTY-006, api: [API-PTY-001, API-PTY-006]}
  - {req: REQ-PTY-007, api: [API-PTY-001]}
  - {req: REQ-PTY-008, api: [API-PTY-001, API-PTY-006]}
  - {req: REQ-PTY-009, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008]}
  - {req: REQ-PTY-010, api: [API-PTY-001, API-PTY-003, API-PTY-005, API-PTY-006, API-PTY-008, API-PTY-010]}
  - {req: REQ-PTY-011, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008]}
  - {req: REQ-PTY-012, api: [API-PTY-003]}
  - {req: REQ-PTY-013, api: [API-PTY-026]}
  - {req: REQ-PTY-014, api: [API-PTY-002, API-PTY-004]}
  - {req: REQ-PTY-015, api: [API-PTY-005]}
  - {req: REQ-PTY-016, api: [API-PTY-006]}
  - {req: REQ-PTY-017, api: [API-PTY-008]}
  - {req: REQ-PTY-018, api: [API-PTY-028]}
  - {req: REQ-PTY-019, api: [API-PTY-007]}
  - {req: REQ-PTY-020, api: [API-PTY-007, API-PTY-009]}
  - {req: REQ-PTY-021, api: [API-PTY-010]}
  - {req: REQ-PTY-022, api: [API-PTY-006, API-PTY-008]}
  - {req: REQ-PTY-023, api: [API-PTY-004, API-PTY-009]}
  - {req: REQ-PTY-024, api: [API-PTY-001, API-PTY-003, API-PTY-006, API-PTY-008]}
  - {req: REQ-PTY-025, api: [API-PTY-006]}
  - {req: REQ-PTY-026, api: [API-PTY-001]}
  - {req: REQ-PTY-027, api: [API-PTY-001, API-PTY-006]}
  - {req: REQ-PTY-028, api: [API-PTY-004, API-PTY-009]}
  - {req: REQ-PTY-029, api: [API-PTY-001, API-PTY-005, API-PTY-006, API-PTY-010]}
  - {req: REQ-PTY-030, api: [API-PTY-011]}
  - {req: REQ-PTY-031, api: [API-PTY-013]}
  - {req: REQ-PTY-032, api: [API-PTY-029]}
  - {req: REQ-PTY-033, api: [API-PTY-012, API-PTY-014]}
  - {req: REQ-PTY-034, api: [API-PTY-011]}
  - {req: REQ-PTY-035, api: [API-PTY-015]}
  - {req: REQ-PTY-036, api: [API-PTY-014]}
  - {req: REQ-PTY-037, api: [API-PTY-011, API-PTY-013]}
  - {req: REQ-PTY-038, api: [API-PTY-011, API-PTY-013]}
  - {req: REQ-PTY-039, api: [API-PTY-001, API-PTY-003]}
  - {req: REQ-PTY-040, api: [API-PTY-001, API-PTY-003]}
  - {req: REQ-PTY-041, api: [API-PTY-027]}
  - {req: REQ-PTY-042, api: [API-PTY-027]}
  - {req: REQ-PTY-043, api: [API-PTY-027]}
  - {req: REQ-PTY-044, api: [API-PTY-033]}
  - {req: REQ-PTY-045, api: [API-PTY-033]}
  - {req: REQ-PTY-046, api: [API-PTY-033]}
  - {req: REQ-PTY-047, api: [API-PTY-004]}
  - {req: REQ-PTY-048, api: [API-PTY-017]}
  - {req: REQ-PTY-049, api: [API-PTY-006]}
  - {req: REQ-PTY-050, api: [API-PTY-034]}
  - {req: REQ-PTY-051, api: [API-PTY-034]}
  - {req: REQ-PTY-053, api: [API-PTY-034]}
  - {req: REQ-PTY-055, api: [API-PTY-034]}
  - {req: REQ-PTY-056, api: [API-PTY-034]}
  - {req: REQ-PTY-057, api: [API-PTY-034]}
  - {req: REQ-PTY-058, api: [API-PTY-034]}
  - {req: REQ-PTY-059, api: [API-PTY-034]}
  - {req: REQ-PTY-060, api: [API-PTY-034]}
  - {req: REQ-PTY-061, api: [API-PTY-034]}
  - {req: REQ-PTY-062, api: [API-PTY-034]}
  - {req: REQ-PTY-063, api: [API-PTY-034]}
  - {req: REQ-PTY-064, api: [API-PTY-017]}
  - {req: REQ-PTY-065, api: [API-PTY-017]}
  - {req: REQ-PTY-066, api: [API-PTY-017]}
  - {req: REQ-PTY-067, api: [API-PTY-017]}
  - {req: REQ-PTY-068, api: [API-PTY-016, API-PTY-035]}
  - {req: REQ-PTY-069, api: [API-PTY-030]}
  - {req: REQ-PTY-070, api: [API-PTY-016, API-PTY-024, API-PTY-030]}
  - {req: REQ-PTY-071, api: [API-PTY-002]}
  - {req: REQ-PTY-072, api: [API-PTY-017, API-PTY-019]}
  - {req: REQ-PTY-073, api: [API-PTY-016, API-PTY-017, API-PTY-030]}
  - {req: REQ-PTY-074, api: [API-PTY-035]}
  - {req: REQ-PTY-075, api: [API-PTY-037]}
  - {req: REQ-PTY-076, api: [API-PTY-037]}
  - {req: REQ-PTY-077, api: [API-PTY-030, API-PTY-037]}
  - {req: REQ-PTY-078, api: [API-PTY-018]}
  - {req: REQ-PTY-079, api: [API-PTY-035]}
  - {req: REQ-PTY-080, api: [API-PTY-016, API-PTY-035]}
  - {req: REQ-PTY-081, api: [API-PTY-035]}
  - {req: REQ-PTY-082, api: [API-PTY-035]}
  - {req: REQ-PTY-083, api: [API-PTY-035]}
  - {req: REQ-PTY-084, api: [API-PTY-035]}
  - {req: REQ-PTY-085, api: [API-PTY-016]}
  - {req: REQ-PTY-086, api: [API-PTY-025]}
  - {req: REQ-PTY-087, api: [API-PTY-024, API-PTY-035]}
  - {req: REQ-PTY-088, api: [API-PTY-025, API-PTY-035]}
  - {req: REQ-PTY-089, api: [API-PTY-019, API-PTY-025, API-PTY-035]}
  - {req: REQ-PTY-090, api: [API-PTY-025]}
  - {req: REQ-PTY-091, api: [API-PTY-019]}
  - {req: REQ-PTY-092, api: [API-PTY-019]}
  - {req: REQ-PTY-093, api: [API-PTY-019]}
  - {req: REQ-PTY-094, api: [API-PTY-021]}
  - {req: REQ-PTY-095, api: [API-PTY-022]}
  - {req: REQ-PTY-096, api: [API-PTY-023]}
  - {req: REQ-PTY-097, api: [API-PTY-036]}
  - {req: REQ-PTY-098, api: [API-PTY-021, API-PTY-022, API-PTY-023]}
  - {req: REQ-PTY-099, api: [API-PTY-036]}
  - {req: REQ-PTY-100, api: [API-PTY-022]}
  - {req: REQ-PTY-101, api: [API-PTY-032]}
  - {req: REQ-PTY-102, api: [API-PTY-032]}
  - {req: REQ-PTY-103, api: [API-PTY-020]}
  - {req: REQ-PTY-104, api: [API-PTY-019]}
  - {req: REQ-PTY-105, api: [API-PTY-019]}
  - {req: REQ-PTY-106, api: [API-PTY-017, API-PTY-018]}
  - {req: REQ-PTY-107, api: [API-PTY-021, API-PTY-022]}
  - {req: REQ-PTY-108, api: [API-PTY-031]}
  - {req: REQ-PTY-109, api: [API-PTY-017, API-PTY-018, API-PTY-021, API-PTY-022]}
  - {req: REQ-PTY-110, api: [API-PTY-011, API-PTY-013, API-PTY-015, API-PTY-017, API-PTY-019, API-PTY-035, API-PTY-036]}
  - {req: REQ-PTY-111, api: [API-PTY-004, API-PTY-036]}
  - {req: REQ-PTY-112, api: [API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-036]}
  - {req: REQ-PTY-113, api: [API-PTY-019]}
unmapped: []
codes:
  - {code: "PTY-422-NAME-REQUIRED", rule: RULE-PTY-001}
  - {code: "PTY-409-EMAIL-TAKEN", rule: RULE-PTY-002}
  - {code: "PTY-422-EMAIL-FORMAT", rule: RULE-PTY-003}
  - {code: "PTY-422-MOBILE-FORMAT", rule: RULE-PTY-004}
  - {code: "PTY-422-ROLE-INACTIVE", rule: RULE-PTY-005}
  - {code: "PTY-422-CODE-READ-ONLY", rule: RULE-PTY-007}
  - {code: "PTY-422-PAYMENT-TERMS-RANGE", rule: RULE-PTY-008}
  - {code: "PTY-409-ROLE-HELD", rule: RULE-PTY-009}
  - {code: "PTY-422-WALK-IN-DEACTIVATE", rule: RULE-PTY-010}
  - {code: "PTY-422-WALK-IN-CREDIT", rule: RULE-PTY-011}
  - {code: "PTY-422-WALK-IN-SUPPLIER", rule: RULE-PTY-012}
  - {code: "PTY-422-ADDRESS-LINE-REQUIRED", rule: RULE-PTY-015}
  - {code: "PTY-422-ADDRESS-TYPE-INVALID", rule: RULE-PTY-016}
  - {code: "PTY-422-CUSTOMER-GROUP-INVALID", rule: RULE-PTY-018}
  - {code: "PTY-422-GUEST-CONTACT-REQUIRED", rule: RULE-PTY-019}
  - {code: "PTY-422-GUEST-NOT-COMPLETED", rule: RULE-PTY-021}
  - {code: "PTY-422-CREDIT-LIMIT-NEGATIVE", rule: RULE-PTY-022}
  - {code: "PTY-422-CREDIT-NOT-ENABLED", rule: RULE-PTY-023}
  - {code: "PTY-422-OVERRIDE-NOT-NEEDED", rule: RULE-PTY-025}
  - {code: "PTY-409-REQUEST-NOT-PENDING", rule: RULE-PTY-026}
  - {code: "PTY-409-REQUEST-OPEN-FOR-SALE", rule: RULE-PTY-034}
  - {code: "PTY-422-REJECTION-REASON-REQUIRED", rule: RULE-PTY-027}
  - {code: "PTY-422-APPROVAL-NOT-COVERING", rule: RULE-PTY-028}
  - {code: "PTY-403-NOT-REQUESTER", rule: RULE-PTY-029}
  - {code: "PTY-500-AUDIT-NOT-RECORDED", rule: RULE-PTY-030}
  - {code: "PTY-422-DOC-NO-READ-ONLY", rule: RULE-PTY-031}
  - {code: "PTY-422-SALE-AMOUNT-NOT-POSITIVE", rule: RULE-PTY-032}
  - {code: "PTY-422-WALK-IN-NO-CREDIT-SALE", rule: RULE-PTY-033}
```

Reconciliation against the SRS (once):
- Every REQ that needs an operation has one. REQ-PTY-052 and REQ-PTY-054 need no HTTP operation: they are served by the in-process invitation handler (registry-exec-be Traceability; ADR-PTY-025) and have no frontend surface.
- Every operation maps to a REQ (each `x-traces` above). Six operations are bound to no PTY screen because their callers are other modules or the flag owner (SRS A8.1): API-PTY-012, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037 — recorded in ADR-PTY-034 and in the ALIGN-FE operations coverage table. They are not used by this plan; nothing is unmapped.
- Values the document does not carry are not invented. RULE-PTY-002 offers an action that opens the conflicting Party, and the error body (`ErrorResponse`) does not carry that Party. The Party is found through the document's role searches instead: API-PTY-026, then API-PTY-028 (which now has an `email` filter), each with the entered email and `isActive=true`. The action keeps only the row whose normalised email equals the entered one (ADR-PTY-043, which supersedes ADR-PTY-034 §9). No interim remains.
- `currencyCode` (ADR-PTY-037) is carried by the credit types and is not rendered while the tenant has one currency; amounts are formatted in it.
- Reads the screens need that are not operations of this document — lookup values (given `mdl`), the `b2bCredit` flag (given application configuration), the session's permissions (given `sec`), the deciding user's name (given `sec`, UXD-PTY-001) — are hooks of kind `lookup` / `init`, never `read` (ADR-PTY-034).

<!-- PHASE:F1:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,REQ-PTY-109,AC-PTY-007,AC-PTY-010,AC-PTY-011,AC-PTY-013,AC-PTY-021,AC-PTY-029,AC-PTY-031,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-077,AC-PTY-078,AC-PTY-082,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-101,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-122,AC-PTY-123,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-024,API-PTY-025,API-PTY-026,API-PTY-027,API-PTY-028,API-PTY-029,API-PTY-030,API-PTY-031,API-PTY-032,UXD-PTY-001,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
## F1 — Models & Types

Field/DTO binding: see `api-spec-pty.yaml` — the request/response schemas of this module's operations are the source, not restated here. Types are generated from the document (`openapi-typescript` into `src/modules/pty/api/schema.d.ts`), regenerated whenever the document changes; no hand-written copy of a schema exists. Frontend-only types are written by hand and are listed per screen below; each is a view of a generated type, never a new field.

Shared (all screens):
- `ApiEnvelope<T>` = the document's `ApiResponse` with `data: T`; `PageOf<T>` = `Page` with `content: T[]`; `PtyError` = `ErrorResponse` (`code`, `messageAr`, `messageEn`).
- `LookupOption` = `{ code; labelAr; labelEn }` — the shape of a given `mdl` lookup value as the shell's lookup client returns it.
- `Lang = 'ar' | 'en'`; `localName(row, lang)` picks `nameAr`/`nameEn` with fallback to the other (RULE-PTY-001 guarantees one).
- Filter objects carry `page` and `size` **inside** them (state rule of F2); their keys are the document's query parameter names.

<!-- SUB:F1-SCR-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-049,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,AC-PTY-007,AC-PTY-010,AC-PTY-013,AC-PTY-055,AC-PTY-057,AC-PTY-078,AC-PTY-082,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-026,API-PTY-029,SCR-PTY-001 -->
### F1 · SCR-PTY-001 — Customers

| Type | Kind | Source |
|---|---|---|
| `Customer`, `CustomerRow`, `CustomerCreateRequest`, `CustomerUpdateRequest`, `ActivationRequest`, `Confirmation` | generated | API-PTY-001, 002, 003, 004, 005, 026 |
| `Supplier`, `SupplierCreateRequest` | generated | API-PTY-006 (Make supplier sends `{partyId}` only) |
| `Address`, `AddressRequest`, `DefaultAddressRequest` | generated | API-PTY-011, 013, 014, 015, 029 |
| `CustomerSearchFilter` | frontend | the query parameters of API-PTY-026 (`code`, `name`, `mobile`, `email`, `taxRegistrationNo`, `customerGroupCode`, `isActive`, `page`, `size`, `sort`) |
| `CustomerFormValues` | frontend | the editable subset of `CustomerUpdateRequest` (no `code` — RULE-PTY-007; no credit field — ADR-PTY-033) + `addresses: AddressDraft[]` |
| `AddressDraft` | frontend | `AddressRequest` + `isDefaultFl` + `key` (addressPk, or a client key for a new line) + `op: 'unchanged' \| 'new' \| 'changed' \| 'removed'` + `markDefault: boolean` |
| `QuickRegisterResult` | frontend | `{ id: Customer.customerPk; code: Customer.code }` — what the in-place registration returns to the host (REQ-PTY-008) |
| `CustomerCreditView` | frontend | `Pick<Customer, 'creditLimit' \| 'paymentTermDays' \| 'currencyCode'>` — read-only display only, rendered when the flag is on and the fields are present (REQ-PTY-071, ADR-PTY-033); `currencyCode` formats the amount and is not shown while the tenant has one currency (ADR-PTY-037) |
| `EmailHolderLookup` | frontend | `{ email }` — the RULE-PTY-002 action's input (ADR-PTY-043) |
<!-- SUB:F1-SCR-PTY-001:END -->

<!-- SUB:F1-SCR-PTY-002:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,AC-PTY-011,AC-PTY-021,AC-PTY-029,AC-PTY-031,API-PTY-001,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-028,API-PTY-029,SCR-PTY-002 -->
### F1 · SCR-PTY-002 — Suppliers

| Type | Kind | Source |
|---|---|---|
| `Supplier`, `SupplierRow`, `SupplierCreateRequest`, `SupplierUpdateRequest`, `ActivationRequest`, `Confirmation` | generated | API-PTY-006, 007, 008, 009, 010, 028 |
| `Customer`, `CustomerCreateRequest` | generated | API-PTY-001 (Make customer sends `{partyId}` only) |
| `Address`, `AddressRequest`, `DefaultAddressRequest` | generated | API-PTY-011, 013, 014, 015, 029 |
| `SupplierSearchFilter` | frontend | the query parameters of API-PTY-028 (`code`, `name`, `mobile`, `email`, `taxRegistrationNo`, `isActive`, `page`, `size`, `sort`) |
| `EmailHolderLookup` | frontend | `{ email }` — the RULE-PTY-002 action's input; reads API-PTY-026 then API-PTY-028 and keeps the row whose normalised email equals it (ADR-PTY-043); shared with SCR-PTY-001 |
| `SupplierFormValues` | frontend | the editable subset of `SupplierUpdateRequest` (no `code`) + `addresses: AddressDraft[]` |
| `AddressDraft`, `QuickRegisterResult` | frontend | shared with SCR-PTY-001 (`QuickRegisterResult.id` = `Supplier.supplierPk`) |
<!-- SUB:F1-SCR-PTY-002:END -->

<!-- SUB:F1-SCR-PTY-003:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-106,REQ-PTY-108,REQ-PTY-109,AC-PTY-056,AC-PTY-077,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-122,AC-PTY-123,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-030,API-PTY-031,SCR-PTY-003 -->
### F1 · SCR-PTY-003 — Customer credit

| Type | Kind | Source |
|---|---|---|
| `CustomerCredit`, `CustomerCreditRow`, `CustomerCreditUpdateRequest`, `CreditHistoryEntry` | generated | API-PTY-016, 017, 018, 030, 031 |
| `CreditSearchFilter` | frontend | the query parameters of API-PTY-030 (`code`, `name`, `customerGroupCode`, `carriesCredit`, `page`, `size`, `sort`) |
| `CreditHistoryFilter` | frontend | `{ customerId, page, size }` — API-PTY-031 |
| `CreditFormValues` | frontend | `CustomerCreditUpdateRequest` as form state (`creditLimit`, `paymentTermDays`; empty input = `null`, REQ-PTY-068) |
<!-- SUB:F1-SCR-PTY-003:END -->

<!-- SUB:F1-SCR-PTY-004:START traces=REQ-PTY-041,REQ-PTY-070,REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-107,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,API-PTY-002,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032,UXD-PTY-001,SCR-PTY-004 -->
### F1 · SCR-PTY-004 — Credit override requests

| Type | Kind | Source |
|---|---|---|
| `CreditOverrideRequest`, `CreditOverrideRow`, `CreditOverrideCreateRequest`, `CreditOverrideDecisionRequest`, `CreditOverrideRejectRequest` | generated | API-PTY-019, 020, 021, 022, 023, 032 |
| `CustomerSearchHit`, `Customer` | generated | API-PTY-027 (Customer picker), API-PTY-002 (label of a prefilled Customer) |
| `OverrideSearchFilter` | frontend | the query parameters of API-PTY-032 (`docNo`, `statusCode`, `customerId`, `docDateFrom`, `docDateTo`, `page`, `size`; `sort` not sent by default — the server orders PENDING first then newest, REQ-PTY-101) |
| `OverrideCreateValues` | frontend | `CreditOverrideCreateRequest` as form state, seeded from the route's prefill parameters |
| `RejectValues` | frontend | `{ decisionReason }` — the body of API-PTY-022 without `docNo` (never sent, RULE-PTY-031) |
| `UserLabel` | frontend | `{ userId; displayName }` — UXD-PTY-001, from the given `sec` user read |
<!-- SUB:F1-SCR-PTY-004:END -->

<!-- SUB:F1-SCR-PTY-005:START traces=REQ-PTY-070,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-097,AC-PTY-098,AC-PTY-101,API-PTY-024,API-PTY-025,SCR-PTY-005 -->
### F1 · SCR-PTY-005 — Credit control settings

| Type | Kind | Source |
|---|---|---|
| `CreditSettings` | generated | API-PTY-024, 025 (`isDefault` is read-only and never sent) |
| `SettingsFormValues` | frontend | `{ creditOverrunActionCode }` |
<!-- SUB:F1-SCR-PTY-005:END -->
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,REQ-PTY-109,REQ-PTY-110,REQ-PTY-112,REQ-PTY-113,AC-PTY-007,AC-PTY-010,AC-PTY-011,AC-PTY-013,AC-PTY-018,AC-PTY-021,AC-PTY-029,AC-PTY-031,AC-PTY-036,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-077,AC-PTY-078,AC-PTY-080,AC-PTY-082,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-101,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-122,AC-PTY-123,AC-PTY-132,AC-PTY-134,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-024,API-PTY-025,API-PTY-026,API-PTY-027,API-PTY-028,API-PTY-029,API-PTY-030,API-PTY-031,API-PTY-032,UXD-PTY-001,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
## F2 — Data Hooks

What each screen reads and mutates. Hook code is the implementer's; this phase fixes the operation each hook calls (by `API-*` id of api-spec-pty.yaml — method, path and schemas are read there, never restated), its cache key, error routing, loading and invalidation.

**State rule.** Page and page size live inside the filter object that forms the cache key, never as separate state; the filter object is mirrored in the route's search parameters, so a deep link reproduces the list. Components use the screen's facade only; the facade uses the declared hooks only (server state: tanstack-query; the only zustand store is the shell's session/flags store read by the INIT hooks).

**Cache policy.** tanstack-query defaults for every PTY read. Two deviations, both ADR-PTY-034: lookups are long-lived (`staleTime` 30 min, `gcTime` 60 min — the engine's lookup rule); the application configuration is refetched on every route mount (`staleTime` 0, `refetchOnMount: 'always'`) so a `b2bCredit` change shows from the next screen load (REQ-PTY-073).

**Loading.** `LOCAL` everywhere (placeholder rows / a busy Save button). No call is declared slow by the SRS, so nothing is `GLOBAL`.

**Standard routing (STD)** — applied by every hook in addition to its own rows; text is always the server's `messageAr` / `messageEn` in the active language:
- `PTY-400-VALIDATION` → inline on the field the request names when it maps to a form field, else a form banner; text: catalogue PTY-400-VALIDATION.
- `PTY-401-UNAUTHENTICATED` → login (the shell's sign-in, returning to the current route).
- `PTY-403-FORBIDDEN` → on a read: the unauthorized page; on a mutation: a form banner (the form is kept); text: catalogue PTY-403-FORBIDDEN.
- `PTY-404-*-NOT-FOUND` → the screen's not-found state (ui-ux-spec States); text: catalogue code.
- `PTY-500-INTERNAL` → generic error with Retry; text: catalogue PTY-500-INTERNAL.

Rule-bound refusals (`codes` of the API binding) are routed per hook below as `CODE → <place>, text: RULE-PTY-<seq> message (SRS)`; the SRS A5 message is the text in ar and en, and the server returns exactly that text.

### Shared hooks (used by several screens)

#### LOOKUP hooks — one per key, shared across screens
`CUSTOMER_GROUP-LOOKUP`, `ADDRESS_TYPE-LOOKUP`, `CREDIT_OVERRUN_ACTION-LOOKUP`, `CREDIT_CHECK_RESULT-LOOKUP`, `CREDIT_OVERRIDE_STATUS-LOOKUP` · kind `lookup` · endpoint: the given `erp-core` `mdl` lookup read (the platform's lookup client; not an operation of api-spec-pty.yaml — ADR-PTY-034) · key `["mdl","lookups",<KEY>]` · options shape `LookupOption` (code + label ar + label en) · long-lived cache. Choosers show active values only; a row whose stored code is no longer active still renders its label.

#### APP-CONFIG-INIT and PERMISSIONS-INIT
Kind `init`. `APP-CONFIG-INIT` reads the given application configuration (`GET /api/public/app-config`, KB technical base — feature flags) and exposes `b2bCredit`; key `["app-config"]`; refetched on every route mount. `PERMISSIONS-INIT` reads the session's permission names from the given `sec` session (key `["auth","permissions"]`). Each screen's SCREEN-INIT composes them.

#### PARTY-ADDRESSES-QUERY — API-PTY-029     traces=API-PTY-029,REQ-PTY-032
Kind read · key `["pty","parties",partyFk,"addresses"]` · enabled once `partyFk` is known (an existing Customer or Supplier) · errors STD (`PTY-404-PARTY-NOT-FOUND` → not-found state) · loading LOCAL. Returns active Addresses only; the pane is built from it.

#### ADDRESSES-SYNC — API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015     traces=API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,REQ-PTY-030,REQ-PTY-031,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038
Kind mutation · called only by a screen's SAVE, after the record call succeeded, with the record's `partyFk` · sends the draft's changes **sequentially** in this order: removed lines → API-PTY-014; changed lines → API-PTY-013; new lines → API-PTY-011; lines marked default that are not already the default → API-PTY-015 with `{"isDefaultFl": true}` (ADR-PTY-031). A new line that is the first of its type is not sent to API-PTY-015 — the server makes it the default (REQ-PTY-034). Not sent at all when no line changed. Stops at the first failure; the remaining lines stay in the draft marked unsent.
Errors: `PTY-422-ADDRESS-LINE-REQUIRED` → inline on line1 of the failing line (its drawer reopens), text: RULE-PTY-015 message (SRS) · `PTY-422-ADDRESS-TYPE-INVALID` → inline on addressTypeCode of the failing line, text: RULE-PTY-016 message (SRS) · `PTY-422-ROLE-INACTIVE` (the Party has no active role — ADR-PTY-041) → form banner, the remaining lines stay in the draft marked unsent, text: RULE-PTY-005 message (SRS) · `PTY-404-ADDRESS-NOT-FOUND` / `PTY-404-PARTY-NOT-FOUND` → form banner + refetch of the addresses, text: catalogue code · STD.
Success: the confirmation texts the responses carry (RULE-PTY-006 on deactivation, RULE-PTY-017 on a new default) are shown once as a toast. Invalidation: `["pty","parties",partyFk,"addresses"]`.

#### EMAIL-HOLDER-QUERY — API-PTY-026, API-PTY-028     traces=API-PTY-026,API-PTY-028,REQ-PTY-010,REQ-PTY-013,REQ-PTY-018
Kind read. It runs only when the user chooses "Open that party" «فتح ذلك الطرف» on a PTY-409-EMAIL-TAKEN refusal (ADR-PTY-043).
1. Read API-PTY-026 with `{email: <entered email>, isActive: true, page: 0, size: 20}`.
2. Keep the row whose `email`, trimmed and lower-cased, equals the entered one. Found → navigate to `/pty/customers/:customerPk`.
3. Otherwise, when the session holds PERM_PTY_SUPPLIER_VIEW, read API-PTY-028 with the same filter and keep the matching row. Found → navigate to `/pty/suppliers/:supplierPk`.
4. Neither found (the user may not see the role the Party holds) → the action is withdrawn; the message, which names the Party code, stays.
Keys `["pty","customers","search",{…}]` and `["pty","suppliers","search",{…}]`, the same cache entries as the search screens. Errors: STD (a failure leaves the refusal shown, with no navigation). Loading: LOCAL, on the action.

#### USER-LABEL-LOOKUP — UXD-PTY-001     traces=UXD-PTY-001,REQ-PTY-094,REQ-PTY-103
Kind lookup · the given `sec` user read (ADR-PTY-034) · key `["sec","users",userId]` · long-lived · one call per distinct identifier · on failure the field reads «—» (never the raw identifier, never an error page).

<!-- SUB:F2-SCR-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-049,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,REQ-PTY-110,AC-PTY-007,AC-PTY-010,AC-PTY-013,AC-PTY-018,AC-PTY-036,AC-PTY-055,AC-PTY-057,AC-PTY-078,AC-PTY-082,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-026,API-PTY-029,SCR-PTY-001 -->
### F2 · SCR-PTY-001 — Customers

```yaml name=screen-hooks
screen: SCR-PTY-001
hooks:
  - hook: CUSTOMER-SEARCH-QUERY
    kind: read
    api: [API-PTY-026]
    cache_key: "[pty, customers, search, {code, name, mobile, email, taxRegistrationNo, customerGroupCode, isActive, page, size, sort}]"
    errors: "STD"
    loading: LOCAL
    invalidation: "—"
  - hook: CUSTOMER-QUERY
    kind: read
    api: [API-PTY-002]
    cache_key: "[pty, customers, customerId]"
    errors: "STD; PTY-404-CUSTOMER-NOT-FOUND → not-found state, text: catalogue PTY-404-CUSTOMER-NOT-FOUND"
    loading: LOCAL
    invalidation: "—"
  - hook: PARTY-ADDRESSES-QUERY
    kind: read
    api: [API-PTY-029]
    cache_key: "[pty, parties, partyFk, addresses]"
    errors: "STD; PTY-404-PARTY-NOT-FOUND → not-found state, text: catalogue PTY-404-PARTY-NOT-FOUND"
    loading: LOCAL
    invalidation: "—"
  - hook: EMAIL-HOLDER-QUERY
    kind: read
    api: [API-PTY-026, API-PTY-028]
    cache_key: "[pty, customers, search, {email, isActive}] then [pty, suppliers, search, {email, isActive}]"
    errors: "STD; a failure leaves the RULE-PTY-002 refusal shown without navigation"
    loading: LOCAL
    invalidation: "—"
  - hook: CUSTOMER_GROUP-LOOKUP
    kind: lookup
    cache_key: "[mdl, lookups, CUSTOMER_GROUP]"
    loading: LOCAL
  - hook: ADDRESS_TYPE-LOOKUP
    kind: lookup
    cache_key: "[mdl, lookups, ADDRESS_TYPE]"
    loading: LOCAL
  - hook: CUSTOMER-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: CUSTOMER-SAVE
    kind: mutation
    api: [API-PTY-001, API-PTY-003, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015]
    errors: "STD; PTY-422-NAME-REQUIRED → inline on the name group, text: RULE-PTY-001 message (SRS); PTY-409-EMAIL-TAKEN → inline on email, text: RULE-PTY-002 message (SRS), with the action open that party (EMAIL-HOLDER-QUERY, ADR-PTY-043); PTY-422-EMAIL-FORMAT → inline on email, text: RULE-PTY-003 message (SRS); PTY-422-MOBILE-FORMAT → inline on mobile, text: RULE-PTY-004 message (SRS); PTY-422-CUSTOMER-GROUP-INVALID → inline on customerGroupCode, text: RULE-PTY-018 message (SRS); PTY-422-CODE-READ-ONLY → form banner, text: RULE-PTY-007 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state; address codes as ADDRESSES-SYNC, PTY-422-ROLE-INACTIVE → form banner, text: RULE-PTY-005 message (SRS)"
    loading: LOCAL
    invalidation: "[pty, customers] · [pty, suppliers] · [pty, customer-credits] · [pty, parties, partyFk, addresses]"
  - hook: CUSTOMER-QUICK-REGISTER
    kind: mutation
    api: [API-PTY-001]
    errors: "STD; PTY-422-NAME-REQUIRED → inline on the name group, text: RULE-PTY-001 message (SRS); PTY-409-EMAIL-TAKEN → inline on email, text: RULE-PTY-002 message (SRS); PTY-422-EMAIL-FORMAT → inline on email, text: RULE-PTY-003 message (SRS); PTY-422-MOBILE-FORMAT → inline on mobile, text: RULE-PTY-004 message (SRS)"
    loading: LOCAL
    invalidation: "[pty, customers, search]"
  - hook: CUSTOMER-DEACTIVATE
    kind: mutation
    api: [API-PTY-004]
    errors: "STD; PTY-422-WALK-IN-DEACTIVATE → banner on the record, text: RULE-PTY-010 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, customers] · [pty, customer-credits]"
  - hook: CUSTOMER-REACTIVATE
    kind: mutation
    api: [API-PTY-005]
    errors: "STD; PTY-409-EMAIL-TAKEN → banner on the record, text: RULE-PTY-002 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, customers] · [pty, customer-credits]"
  - hook: CUSTOMER-MAKE-SUPPLIER
    kind: mutation
    api: [API-PTY-006]
    errors: "STD; PTY-409-ROLE-HELD → banner on the record, text: RULE-PTY-009 message (SRS); PTY-422-WALK-IN-SUPPLIER → banner on the record, text: RULE-PTY-012 message (SRS); PTY-404-PARTY-NOT-FOUND → banner, text: catalogue PTY-404-PARTY-NOT-FOUND"
    loading: LOCAL
    invalidation: "[pty, suppliers]"
  - hook: CUSTOMER-FACADE
    kind: facade
```

#### SCR-PTY-001 · CUSTOMER-SEARCH-QUERY — API-PTY-026     traces=API-PTY-026,REQ-PTY-013,REQ-PTY-001
Kind read query. Cache key is the whole `CustomerSearchFilter` (every filter, page, size, sort). Default filter `{page: 0, size: 20}`; `isActive` unset lists active and inactive with the status column.

#### SCR-PTY-001 · CUSTOMER-QUERY — API-PTY-002     traces=API-PTY-002,REQ-PTY-012,REQ-PTY-071
Kind read query, `forNewDocument` not sent (false) — the back-office opens inactive Customers too. The credit fields are present only while the flag is on; the screen renders them read-only (ADR-PTY-033). Gives `partyFk` to PARTY-ADDRESSES-QUERY and ADDRESSES-SYNC.

#### SCR-PTY-001 · CUSTOMER-SAVE — API-PTY-001 · API-PTY-003 + ADDRESSES-SYNC     traces=API-PTY-001,API-PTY-003,REQ-PTY-004,REQ-PTY-012,REQ-PTY-030,REQ-PTY-031,REQ-PTY-033,REQ-PTY-035
Kind mutation, the screen's **one** Save. It owns ordered calls:
1. the record call — new: API-PTY-001 without `partyId` (body from the form; `customerGroupCode` sent as chosen); existing: API-PTY-003 without `code`, sent only when the header changed;
2. ADDRESSES-SYNC with the `partyFk` the record call returned (or the loaded one) — sent **only after** call 1 succeeded (or was skipped as unchanged), and **not sent at all** when the address set is unchanged.
A refusal of call 1 sends nothing else; the form and the address draft stay as typed. If call 2 fails after call 1 succeeded, the record is saved: a new Customer moves to `/pty/customers/:customerId` (replace) keeping the unsent lines in the draft, and the error is shown on the failing line. RULE-PTY-002 action: "Open that party" «فتح ذلك الطرف» runs EMAIL-HOLDER-QUERY and opens the Customer, or else the Supplier, that holds the entered email (ADR-PTY-043).

#### SCR-PTY-001 · CUSTOMER-QUICK-REGISTER — API-PTY-001     traces=API-PTY-001,REQ-PTY-005,REQ-PTY-008,AC-PTY-007,AC-PTY-010
Kind mutation used by the in-place Quick register only. Sends names, mobile, email; `customerGroupCode` omitted (server default RETAIL, REQ-PTY-007). On success returns `QuickRegisterResult {id, code}` to the host through its callback; the host's screen stays open.

#### SCR-PTY-001 · CUSTOMER-DEACTIVATE / CUSTOMER-REACTIVATE — API-PTY-004 · API-PTY-005     traces=API-PTY-004,API-PTY-005,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-028,REQ-PTY-029,REQ-PTY-047,AC-PTY-055
Kind mutation (commands). Deactivate shows the response's confirmation text (RULE-PTY-006) as a toast. Reactivate sends `{"isActiveFl": true}` (ADR-PTY-031).

#### SCR-PTY-001 · CUSTOMER-MAKE-SUPPLIER — API-PTY-006     traces=API-PTY-006,REQ-PTY-025,REQ-PTY-027,REQ-PTY-049,AC-PTY-057
Kind mutation (command). Body `{partyId: partyFk}` only — no Party field is asked (REQ-PTY-025). On success navigates to `/pty/suppliers/:supplierPk` of the response.

#### SCR-PTY-001 · CUSTOMER-SCREEN-INIT and CUSTOMER-FACADE
INIT: permissions PERM_PTY_CUSTOMER_VIEW / _CREATE / _UPDATE / _DELETE, PERM_PTY_SUPPLIER_CREATE (Make supplier), PERM_PTY_SUPPLIER_VIEW (the Supplier step of EMAIL-HOLDER-QUERY), PERM_PTY_CUSTOMER_CREDIT_VIEW (Edit credit); `b2bCredit`; lookups CUSTOMER_GROUP, ADDRESS_TYPE; the Customer by id when the route carries `:customerId`.
FACADE: owns the search filter (from the route), the selected Customer, the address draft (`AddressDraft[]` built from PARTY-ADDRESSES-QUERY), `isDirty`, derived `isBusy`; exposes `search(filter)`, `save()`, `deactivate()`, `reactivate()`, `makeSupplier()`, `quickRegister(values)`, `addLine/updateLine/removeLine/markDefault` (draft only). Commands are rejected by the facade while `isDirty` (ADR-PTY-034).
<!-- SUB:F2-SCR-PTY-001:END -->

<!-- SUB:F2-SCR-PTY-002:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-110,AC-PTY-011,AC-PTY-013,AC-PTY-021,AC-PTY-029,AC-PTY-031,API-PTY-001,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-028,API-PTY-029,SCR-PTY-002 -->
### F2 · SCR-PTY-002 — Suppliers

```yaml name=screen-hooks
screen: SCR-PTY-002
hooks:
  - hook: SUPPLIER-SEARCH-QUERY
    kind: read
    api: [API-PTY-028]
    cache_key: "[pty, suppliers, search, {code, name, mobile, email, taxRegistrationNo, isActive, page, size, sort}]"
    errors: "STD"
    loading: LOCAL
    invalidation: "—"
  - hook: SUPPLIER-QUERY
    kind: read
    api: [API-PTY-007]
    cache_key: "[pty, suppliers, supplierId]"
    errors: "STD; PTY-404-SUPPLIER-NOT-FOUND → not-found state, text: catalogue PTY-404-SUPPLIER-NOT-FOUND"
    loading: LOCAL
    invalidation: "—"
  - hook: PARTY-ADDRESSES-QUERY
    kind: read
    api: [API-PTY-029]
    cache_key: "[pty, parties, partyFk, addresses]"
    errors: "STD; PTY-404-PARTY-NOT-FOUND → not-found state, text: catalogue PTY-404-PARTY-NOT-FOUND"
    loading: LOCAL
    invalidation: "—"
  - hook: EMAIL-HOLDER-QUERY
    kind: read
    api: [API-PTY-026, API-PTY-028]
    cache_key: "[pty, customers, search, {email, isActive}] then [pty, suppliers, search, {email, isActive}]"
    errors: "STD; a failure leaves the RULE-PTY-002 refusal shown without navigation"
    loading: LOCAL
    invalidation: "—"
  - hook: ADDRESS_TYPE-LOOKUP
    kind: lookup
    cache_key: "[mdl, lookups, ADDRESS_TYPE]"
    loading: LOCAL
  - hook: SUPPLIER-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: SUPPLIER-SAVE
    kind: mutation
    api: [API-PTY-006, API-PTY-008, API-PTY-011, API-PTY-013, API-PTY-014, API-PTY-015]
    errors: "STD; PTY-422-NAME-REQUIRED → inline on the name group, text: RULE-PTY-001 message (SRS); PTY-409-EMAIL-TAKEN → inline on email, text: RULE-PTY-002 message (SRS), with the action open that party (EMAIL-HOLDER-QUERY, ADR-PTY-043); PTY-422-EMAIL-FORMAT → inline on email, text: RULE-PTY-003 message (SRS); PTY-422-MOBILE-FORMAT → inline on mobile, text: RULE-PTY-004 message (SRS); PTY-422-PAYMENT-TERMS-RANGE → inline on paymentTermDays, text: RULE-PTY-008 message (SRS); PTY-422-CODE-READ-ONLY → form banner, text: RULE-PTY-007 message (SRS); PTY-404-SUPPLIER-NOT-FOUND → not-found state; address codes as ADDRESSES-SYNC, PTY-422-ROLE-INACTIVE → form banner, text: RULE-PTY-005 message (SRS)"
    loading: LOCAL
    invalidation: "[pty, suppliers] · [pty, customers] · [pty, customer-credits] · [pty, parties, partyFk, addresses]"
  - hook: SUPPLIER-QUICK-REGISTER
    kind: mutation
    api: [API-PTY-006]
    errors: "STD; PTY-422-NAME-REQUIRED → inline on the name group, text: RULE-PTY-001 message (SRS); PTY-409-EMAIL-TAKEN → inline on email, text: RULE-PTY-002 message (SRS); PTY-422-EMAIL-FORMAT → inline on email, text: RULE-PTY-003 message (SRS); PTY-422-MOBILE-FORMAT → inline on mobile, text: RULE-PTY-004 message (SRS)"
    loading: LOCAL
    invalidation: "[pty, suppliers, search]"
  - hook: SUPPLIER-DEACTIVATE
    kind: mutation
    api: [API-PTY-009]
    errors: "STD; PTY-404-SUPPLIER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, suppliers]"
  - hook: SUPPLIER-REACTIVATE
    kind: mutation
    api: [API-PTY-010]
    errors: "STD; PTY-409-EMAIL-TAKEN → banner on the record, text: RULE-PTY-002 message (SRS); PTY-404-SUPPLIER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, suppliers]"
  - hook: SUPPLIER-MAKE-CUSTOMER
    kind: mutation
    api: [API-PTY-001]
    errors: "STD; PTY-409-ROLE-HELD → banner on the record, text: RULE-PTY-009 message (SRS); PTY-404-PARTY-NOT-FOUND → banner, text: catalogue PTY-404-PARTY-NOT-FOUND"
    loading: LOCAL
    invalidation: "[pty, customers]"
  - hook: SUPPLIER-FACADE
    kind: facade
```

#### SCR-PTY-002 · SUPPLIER-SEARCH-QUERY — API-PTY-028     traces=API-PTY-028,REQ-PTY-018
Kind read query; key = the whole `SupplierSearchFilter`; default `{page: 0, size: 20}`. The `email` filter (ADR-PTY-043) is also read by EMAIL-HOLDER-QUERY.

#### SCR-PTY-002 · SUPPLIER-QUERY — API-PTY-007     traces=API-PTY-007,REQ-PTY-017,REQ-PTY-024
Kind read query, `forNewDocument` not sent. Gives `partyFk` to the address hooks.

#### SCR-PTY-002 · SUPPLIER-SAVE — API-PTY-006 · API-PTY-008 + ADDRESSES-SYNC     traces=API-PTY-006,API-PTY-008,REQ-PTY-016,REQ-PTY-017,REQ-PTY-022,REQ-PTY-030,REQ-PTY-031,REQ-PTY-035
Kind mutation, the screen's one Save; same ordering as CUSTOMER-SAVE: record call (API-PTY-006 new without `partyId` / API-PTY-008 existing without `code`, only when the header changed) → ADDRESSES-SYNC only after it succeeded and only when the address set changed. Invalidates the Customer keys too: a Party holding both roles shows the change on both screens (REQ-PTY-024, AC-PTY-029).

#### SCR-PTY-002 · SUPPLIER-QUICK-REGISTER — API-PTY-006     traces=API-PTY-006,REQ-PTY-008,REQ-PTY-016,AC-PTY-011,AC-PTY-021
Kind mutation for the in-place Quick register from a purchase document; returns `{id: supplierPk, code}` to the host.

#### SCR-PTY-002 · SUPPLIER-DEACTIVATE / SUPPLIER-REACTIVATE — API-PTY-009 · API-PTY-010     traces=API-PTY-009,API-PTY-010,REQ-PTY-020,REQ-PTY-021,REQ-PTY-023,REQ-PTY-028,REQ-PTY-029
Kind mutation (commands). Deactivation toast = the response's RULE-PTY-006 text; reactivation sends `{"isActiveFl": true}`.

#### SCR-PTY-002 · SUPPLIER-MAKE-CUSTOMER — API-PTY-001     traces=API-PTY-001,REQ-PTY-026,REQ-PTY-027,AC-PTY-031
Kind mutation (command). Body `{partyId: partyFk}` only; the server sets customer group RETAIL. On success navigates to `/pty/customers/:customerPk`.

#### SCR-PTY-002 · SUPPLIER-SCREEN-INIT and SUPPLIER-FACADE
INIT: PERM_PTY_SUPPLIER_VIEW / _CREATE / _UPDATE / _DELETE, PERM_PTY_CUSTOMER_CREATE (Make customer), PERM_PTY_CUSTOMER_VIEW (the Customer step of EMAIL-HOLDER-QUERY); lookup ADDRESS_TYPE; the Supplier by id when the route carries `:supplierId`. FACADE: as CUSTOMER-FACADE for Suppliers (filter, selection, address draft, `isDirty`, commands rejected while dirty).
<!-- SUB:F2-SCR-PTY-002:END -->

<!-- SUB:F2-SCR-PTY-003:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-106,REQ-PTY-108,REQ-PTY-109,REQ-PTY-110,AC-PTY-056,AC-PTY-077,AC-PTY-080,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-122,AC-PTY-123,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-030,API-PTY-031,SCR-PTY-003 -->
### F2 · SCR-PTY-003 — Customer credit

```yaml name=screen-hooks
screen: SCR-PTY-003
hooks:
  - hook: CREDIT-SEARCH-QUERY
    kind: read
    api: [API-PTY-030]
    cache_key: "[pty, customer-credits, search, {code, name, customerGroupCode, carriesCredit, page, size, sort}]"
    errors: "STD; PTY-422-CREDIT-NOT-ENABLED → close to home with a banner, text: RULE-PTY-023 message (SRS), and refetch app-config"
    loading: LOCAL
    invalidation: "—"
  - hook: CREDIT-STANDING-QUERY
    kind: read
    api: [API-PTY-016]
    cache_key: "[pty, customer-credits, customerId]"
    errors: "STD; PTY-404-CUSTOMER-NOT-FOUND → not-found state, text: catalogue PTY-404-CUSTOMER-NOT-FOUND; PTY-422-CREDIT-NOT-ENABLED → close to home with a banner, text: RULE-PTY-023 message (SRS)"
    loading: LOCAL
    invalidation: "—"
  - hook: CREDIT-HISTORY-QUERY
    kind: read
    api: [API-PTY-031]
    cache_key: "[pty, customer-credits, customerId, history, {page, size}]"
    errors: "STD; PTY-404-CUSTOMER-NOT-FOUND → not-found state; PTY-422-CREDIT-NOT-ENABLED → close to home with a banner, text: RULE-PTY-023 message (SRS)"
    loading: LOCAL
    invalidation: "—"
  - hook: CUSTOMER_GROUP-LOOKUP
    kind: lookup
    cache_key: "[mdl, lookups, CUSTOMER_GROUP]"
    loading: LOCAL
  - hook: CREDIT-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: CREDIT-SAVE
    kind: mutation
    api: [API-PTY-017]
    errors: "STD; PTY-422-CREDIT-LIMIT-NEGATIVE → inline on creditLimit, text: RULE-PTY-022 message (SRS); PTY-422-PAYMENT-TERMS-RANGE → inline on paymentTermDays, text: RULE-PTY-008 message (SRS); PTY-422-WALK-IN-CREDIT → form banner, text: RULE-PTY-011 message (SRS); PTY-422-ROLE-INACTIVE → form banner, the form is kept, text: RULE-PTY-005 message (SRS); PTY-422-CREDIT-NOT-ENABLED → form banner, text: RULE-PTY-023 message (SRS), refetch app-config; PTY-500-AUDIT-NOT-RECORDED → form banner, form kept for retry, text: RULE-PTY-030 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, customer-credits] · [pty, customers, customerId]"
  - hook: CREDIT-CLEAR
    kind: mutation
    api: [API-PTY-018]
    errors: "STD; PTY-422-CREDIT-NOT-ENABLED → banner, text: RULE-PTY-023 message (SRS); PTY-500-AUDIT-NOT-RECORDED → banner, text: RULE-PTY-030 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, customer-credits] · [pty, customers, customerId]"
  - hook: CREDIT-FACADE
    kind: facade
```

#### SCR-PTY-003 · CREDIT-SEARCH-QUERY — API-PTY-030     traces=API-PTY-030,REQ-PTY-069,REQ-PTY-077,AC-PTY-077,AC-PTY-087
Kind read query; key = the whole `CreditSearchFilter`. `carriesCredit` is a tri-state chooser (yes / no / any); the route `?carriesCredit=true` seeds it (REQ-PTY-077).

#### SCR-PTY-003 · CREDIT-STANDING-QUERY — API-PTY-016     traces=API-PTY-016,REQ-PTY-068,REQ-PTY-080,REQ-PTY-085,AC-PTY-096
Kind read query. `exposureAvailable: false` → open exposure and available credit read «غير متاحة / Unavailable»; an empty `creditLimit` reads «لا ائتمان / No credit» (REQ-PTY-068).

#### SCR-PTY-003 · CREDIT-HISTORY-QUERY — API-PTY-031     traces=API-PTY-031,REQ-PTY-108,AC-PTY-122
Kind read query, newest first as the server returns it; page size 10 in the inline pane, the pane's pager changes `page` inside the key.

#### SCR-PTY-003 · CREDIT-SAVE — API-PTY-017     traces=API-PTY-017,REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-106,REQ-PTY-109,AC-PTY-056,AC-PTY-123
Kind mutation, the screen's one Save; one call. On success the history key is refreshed (the audit row is new, REQ-PTY-106).

#### SCR-PTY-003 · CREDIT-CLEAR — API-PTY-018     traces=API-PTY-018,REQ-PTY-078,AC-PTY-088
Kind mutation (command, with confirmation «سيُمسح حد الائتمان ومدة السداد لهذا العميل. متابعة؟» / "The credit limit and payment terms of this customer will be cleared. Continue?").

#### SCR-PTY-003 · CREDIT-SCREEN-INIT and CREDIT-FACADE
INIT: `b2bCredit` (the route is not mounted while off); PERM_PTY_CUSTOMER_CREDIT_VIEW / _UPDATE, PERM_PTY_CREDIT_OVERRIDE_VIEW and PERM_PTY_CREDIT_SETTINGS_VIEW (links); lookup CUSTOMER_GROUP; the standing by id when the route carries `:customerId`. FACADE: filter, selection, history page, `isDirty`; `save()`, `clearCredit()` (rejected while dirty).
<!-- SUB:F2-SCR-PTY-003:END -->

<!-- SUB:F2-SCR-PTY-004:START traces=REQ-PTY-041,REQ-PTY-070,REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-107,REQ-PTY-110,REQ-PTY-112,REQ-PTY-113,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-132,AC-PTY-134,API-PTY-002,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032,UXD-PTY-001,SCR-PTY-004 -->
### F2 · SCR-PTY-004 — Credit override requests

```yaml name=screen-hooks
screen: SCR-PTY-004
hooks:
  - hook: OVERRIDE-SEARCH-QUERY
    kind: read
    api: [API-PTY-032]
    cache_key: "[pty, credit-overrides, search, {docNo, statusCode, customerId, docDateFrom, docDateTo, page, size}]"
    errors: "STD; PTY-422-CREDIT-NOT-ENABLED → close to home with a banner, text: RULE-PTY-023 message (SRS)"
    loading: LOCAL
    invalidation: "—"
  - hook: OVERRIDE-QUERY
    kind: read
    api: [API-PTY-020]
    cache_key: "[pty, credit-overrides, requestId]"
    errors: "STD; PTY-404-CREDIT-OVERRIDE-NOT-FOUND → not-found state, text: catalogue PTY-404-CREDIT-OVERRIDE-NOT-FOUND"
    loading: LOCAL
    invalidation: "—"
  - hook: OVERRIDE-CUSTOMER-PICK-QUERY
    kind: read
    api: [API-PTY-027]
    cache_key: "[pty, customers, global-search, term]"
    errors: "STD"
    loading: LOCAL
    invalidation: "—"
  - hook: OVERRIDE-CUSTOMER-LABEL-QUERY
    kind: read
    api: [API-PTY-002]
    cache_key: "[pty, customers, customerId]"
    errors: "STD; PTY-404-CUSTOMER-NOT-FOUND → inline on the Customer picker, text: catalogue PTY-404-CUSTOMER-NOT-FOUND"
    loading: LOCAL
    invalidation: "—"
  - hook: USER-LABEL-LOOKUP
    kind: lookup
    cache_key: "[sec, users, userId]"
    errors: "any failure → the field reads —"
    loading: LOCAL
  - hook: CREDIT_OVERRIDE_STATUS-LOOKUP
    kind: lookup
    cache_key: "[mdl, lookups, CREDIT_OVERRIDE_STATUS]"
    loading: LOCAL
  - hook: CREDIT_CHECK_RESULT-LOOKUP
    kind: lookup
    cache_key: "[mdl, lookups, CREDIT_CHECK_RESULT]"
    loading: LOCAL
  - hook: OVERRIDE-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: OVERRIDE-CREATE
    kind: mutation
    api: [API-PTY-019]
    errors: "STD; PTY-422-SALE-AMOUNT-NOT-POSITIVE → inline on saleAmount, text: RULE-PTY-032 message (SRS); PTY-422-WALK-IN-NO-CREDIT-SALE → inline on the Customer picker, text: RULE-PTY-033 message (SRS); PTY-422-ROLE-INACTIVE → inline on the Customer picker, text: RULE-PTY-005 message (SRS); PTY-409-REQUEST-OPEN-FOR-SALE → form banner, text: RULE-PTY-034 message (SRS), with the action show this sale's requests (`/pty/credit-overrides?customerId=<customerId>&statusCode=PENDING`); PTY-422-OVERRIDE-NOT-NEEDED → form banner, text: RULE-PTY-025 message (SRS); PTY-422-CREDIT-NOT-ENABLED → form banner, text: RULE-PTY-023 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → inline on the Customer picker, text: catalogue PTY-404-CUSTOMER-NOT-FOUND"
    loading: LOCAL
    invalidation: "[pty, credit-overrides]"
  - hook: OVERRIDE-APPROVE
    kind: mutation
    api: [API-PTY-021]
    errors: "STD; PTY-409-REQUEST-NOT-PENDING → banner on the decision panel and refetch of the request, text: RULE-PTY-026 message (SRS); PTY-422-DOC-NO-READ-ONLY → banner, text: RULE-PTY-031 message (SRS); PTY-422-CREDIT-NOT-ENABLED → banner on the decision panel and refetch of app-config, text: RULE-PTY-023 message (SRS); PTY-500-AUDIT-NOT-RECORDED → banner on the decision panel, the request is re-read (still PENDING), text: RULE-PTY-030 message (SRS); PTY-404-CREDIT-OVERRIDE-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, credit-overrides]"
  - hook: OVERRIDE-REJECT
    kind: mutation
    api: [API-PTY-022]
    errors: "STD; PTY-422-REJECTION-REASON-REQUIRED → inline on decisionReason, text: RULE-PTY-027 message (SRS); PTY-409-REQUEST-NOT-PENDING → banner on the decision panel and refetch, text: RULE-PTY-026 message (SRS); PTY-422-DOC-NO-READ-ONLY → banner, text: RULE-PTY-031 message (SRS); PTY-422-CREDIT-NOT-ENABLED → banner on the decision panel and refetch of app-config, text: RULE-PTY-023 message (SRS); PTY-500-AUDIT-NOT-RECORDED → banner on the decision panel, the reason is kept for retry, text: RULE-PTY-030 message (SRS); PTY-404-CREDIT-OVERRIDE-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, credit-overrides]"
  - hook: OVERRIDE-CANCEL
    kind: mutation
    api: [API-PTY-023]
    errors: "STD; PTY-403-NOT-REQUESTER → banner on the decision panel (not the unauthorized page), text: RULE-PTY-029 message (SRS); PTY-409-REQUEST-NOT-PENDING → banner and refetch, text: RULE-PTY-026 message (SRS); PTY-404-CREDIT-OVERRIDE-NOT-FOUND → not-found state"
    loading: LOCAL
    invalidation: "[pty, credit-overrides]"
  - hook: OVERRIDE-FACADE
    kind: facade
```

#### SCR-PTY-004 · OVERRIDE-SEARCH-QUERY — API-PTY-032     traces=API-PTY-032,REQ-PTY-101,REQ-PTY-102,AC-PTY-115,AC-PTY-116
Kind read query; key = the whole `OverrideSearchFilter`. No `sort` is sent by default so the server's order holds (PENDING first, then newest). The server returns only the caller's own requests when the caller lacks UPDATE (REQ-PTY-102); the screen does not filter again. The `customerId` filter is chosen in the Customer picker or seeded by the route (`?customerId=` from SCR-PTY-003 — one Customer).

#### SCR-PTY-004 · OVERRIDE-QUERY — API-PTY-020     traces=API-PTY-020,REQ-PTY-103,AC-PTY-117
Kind read query for the Detail. `decidedBy` (an identifier) is labelled by USER-LABEL-LOOKUP (UXD-PTY-001); `createdBy` is a name already and is shown as is.

#### SCR-PTY-004 · OVERRIDE-CUSTOMER-PICK-QUERY — API-PTY-027     traces=API-PTY-027,REQ-PTY-041
Kind read query feeding the Customer picker (create sub-view and the Search filter). Enabled when the typed term has at least 2 characters, debounced 300 ms; at most 10 active Customers come back (REQ-PTY-041). Options show code + name in the active language.

#### SCR-PTY-004 · OVERRIDE-CUSTOMER-LABEL-QUERY — API-PTY-002     traces=API-PTY-002,REQ-PTY-091
Kind read query that labels a Customer id the screen holds but did not pick: the prefilled `customerId` of the New request and the seeded `customerId` filter. Same key as CUSTOMER-QUERY (one cache entry per Customer).

#### SCR-PTY-004 · OVERRIDE-CREATE — API-PTY-019     traces=API-PTY-019,REQ-PTY-072,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-104,REQ-PTY-105,AC-PTY-102
Kind mutation, the New request's one submit ("Request approval"). On success navigates (replace) to `/pty/credit-overrides/:requestId` of the response, which shows the assigned number and PENDING.

#### SCR-PTY-004 · OVERRIDE-APPROVE / OVERRIDE-REJECT / OVERRIDE-CANCEL — API-PTY-021 · API-PTY-022 · API-PTY-023     traces=API-PTY-021,API-PTY-022,API-PTY-023,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-107,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114
Kind mutation (decision commands, each a single call with confirmation). Approve sends no body (the body is optional; `docNo` is never sent). Reject sends `{decisionReason}`. Cancel has no body. After success the request key is refreshed and the Detail shows the new status.

#### SCR-PTY-004 · OVERRIDE-SCREEN-INIT and OVERRIDE-FACADE
INIT: `b2bCredit`; PERM_PTY_CREDIT_OVERRIDE_VIEW / _CREATE / _UPDATE / _DELETE, PERM_PTY_CUSTOMER_VIEW (picker), PERM_PTY_CUSTOMER_CREDIT_VIEW (link); lookups CREDIT_OVERRIDE_STATUS, CREDIT_CHECK_RESULT; the request by id when the route carries `:requestId`; the prefill parameters on `/new`. FACADE: filter, selected request, picker term, `create()`, `approve()`, `reject(reason)`, `cancel()`; the decision commands exist only while `statusCode = PENDING`.
<!-- SUB:F2-SCR-PTY-004:END -->

<!-- SUB:F2-SCR-PTY-005:START traces=REQ-PTY-070,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-097,AC-PTY-098,AC-PTY-101,API-PTY-024,API-PTY-025,SCR-PTY-005 -->
### F2 · SCR-PTY-005 — Credit control settings

```yaml name=screen-hooks
screen: SCR-PTY-005
hooks:
  - hook: SETTINGS-QUERY
    kind: read
    api: [API-PTY-024]
    cache_key: "[pty, credit-settings]"
    errors: "STD"
    loading: LOCAL
    invalidation: "—"
  - hook: CREDIT_OVERRUN_ACTION-LOOKUP
    kind: lookup
    cache_key: "[mdl, lookups, CREDIT_OVERRUN_ACTION]"
    loading: LOCAL
  - hook: SETTINGS-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: SETTINGS-SAVE
    kind: mutation
    api: [API-PTY-025]
    errors: "STD; PTY-403-FORBIDDEN → form banner, the shown setting is reloaded, text: catalogue PTY-403-FORBIDDEN"
    loading: LOCAL
    invalidation: "[pty, credit-settings]"
  - hook: SETTINGS-FACADE
    kind: facade
```

#### SCR-PTY-005 · SETTINGS-QUERY — API-PTY-024     traces=API-PTY-024,REQ-PTY-087,AC-PTY-098
Kind read query. `isDefault: true` → the «القيمة الافتراضية / Default value» note; the returned action (BLOCK_WITH_OVERRIDE when nothing is saved) is preselected.

#### SCR-PTY-005 · SETTINGS-SAVE — API-PTY-025     traces=API-PTY-025,REQ-PTY-086,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-097,AC-PTY-101
Kind mutation, the one Save; body `{creditOverrunActionCode}` (`isDefault` is read-only, never sent).

#### SCR-PTY-005 · SETTINGS-SCREEN-INIT and SETTINGS-FACADE
INIT: `b2bCredit`; PERM_PTY_CREDIT_SETTINGS_VIEW / _UPDATE; lookup CREDIT_OVERRUN_ACTION. FACADE: the setting, `isDirty`, `save()`.
<!-- SUB:F2-SCR-PTY-005:END -->
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,REQ-PTY-109,AC-PTY-007,AC-PTY-010,AC-PTY-011,AC-PTY-013,AC-PTY-021,AC-PTY-029,AC-PTY-031,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-077,AC-PTY-078,AC-PTY-082,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-101,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-122,AC-PTY-123,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-024,API-PTY-025,API-PTY-026,API-PTY-027,API-PTY-028,API-PTY-029,API-PTY-030,API-PTY-031,API-PTY-032,UXD-PTY-001,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
## F3 — Forms & Validators

Forms use react-hook-form with a zod resolver. The client validators repeat the SRS rules only to answer early; the server stays the authority and its refusals are routed as F2 declares. Every client message is the SRS A5 message of the rule, in ar and en, so the client and the server say the same words. Maximum lengths are the document's `maxLength` values (read in api-spec-pty.yaml, not restated). On submit, empty text inputs become `null`; system fields (`code`, `docNo`, audit fields, `isActiveFl`, `isWalkInFl`) are never in a form and never sent.

Messages used below (SRS A5, verbatim):

| Rule | ar | en |
|---|---|---|
| RULE-PTY-001 | أدخل اسم الطرف بالعربية أو بالإنجليزية. | Enter the party name in Arabic or English. |
| RULE-PTY-003 | صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com | The email format is not valid, for example: name@example.com |
| RULE-PTY-004 | رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +. | The mobile number has 7 to 15 digits and can start with +. |
| RULE-PTY-008 | مدة السداد يجب أن تكون بين 0 و365 يوماً. | Payment terms must be between 0 and 365 days. |
| RULE-PTY-011 | لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان. | The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit. |
| RULE-PTY-015 | أدخل السطر الأول من العنوان. | Enter the first address line. |
| RULE-PTY-016 | اختر نوع عنوان من القائمة. | Choose an address type from the list. |
| RULE-PTY-018 | اختر مجموعة عملاء من القائمة. | Choose a customer group from the list. |
| RULE-PTY-022 | حد الائتمان يجب أن يكون صفراً أو أكثر. | The credit limit must be zero or more. |
| RULE-PTY-027 | أدخل سبب الرفض. | Enter the reason for the rejection. |
| RULE-PTY-032 | مبلغ البيع يجب أن يكون أكبر من صفر. | The sale amount must be greater than zero. |
| (form) required | هذا الحقل مطلوب. | This field is required. |
| (form) max length | الحد الأقصى {n} حرفاً. | At most {n} characters. |

Shared validators (`src/modules/pty/forms/validators.ts`):
- `partyNames` — `nameAr`, `nameEn` optional, trimmed; refinement "at least one non-empty" → RULE-PTY-001 on the name group (one mandatory mark for the group — AC-PTY-007, AC-PTY-021).
- `email` — optional; when non-empty: exactly one `@`, non-empty local part, domain part containing at least one `.` → RULE-PTY-003.
- `mobile` — optional; when non-empty, after removing spaces and dashes: `^\+?\d{7,15}$` → RULE-PTY-004.
- `paymentTermDays` — optional whole number 0 … 365 → RULE-PTY-008.
- `addressLine` — `addressTypeCode` required, one of the active ADDRESS_TYPE codes → RULE-PTY-016; `line1` required, trimmed → RULE-PTY-015; other address fields optional with their maximum lengths.

<!-- SUB:F3-SCR-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-049,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,AC-PTY-007,AC-PTY-010,AC-PTY-013,AC-PTY-055,AC-PTY-057,AC-PTY-078,AC-PTY-082,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-026,API-PTY-029,SCR-PTY-001 -->
### F3 · SCR-PTY-001 — Customers

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `CustomerEntryForm` | `partyNames`; `customerGroupCode` required, active CUSTOMER_GROUP code → RULE-PTY-018 (default RETAIL); `mobile`; `email`; `taxRegistrationNo` optional (Advanced); `addresses: AddressDraft[]` (each line passed `addressLine` when confirmed in its drawer) | 2 (name group, customer group — SRS B3) | CUSTOMER-SAVE |
| `AddressLineForm` (drawer) | `addressLine`; `markDefault` boolean | 2 (address type, line 1) | returns the line to `CustomerEntryForm` — no call |
| `CustomerQuickRegisterForm` | `partyNames`; `mobile`; `email` | 1 (name group — AC-PTY-007) | CUSTOMER-QUICK-REGISTER |
| `CustomerSearchForm` | the `CustomerSearchFilter` fields; `customerGroupCode` from CUSTOMER_GROUP (any value, active or not); `isActive` yes / no / any | 0 | the route's search parameters (CUSTOMER-SEARCH-QUERY) |

Dirty tracking: `formState.isDirty` of `CustomerEntryForm` covers the header and the address draft; the facade's commands (Deactivate, Reactivate, Make supplier) are disabled while it is true, and leaving the route while dirty asks «لديك تغييرات غير محفوظة. هل تريد المغادرة؟» / "You have unsaved changes. Leave anyway?". The read-only credit values (ADR-PTY-033) are not form fields.
<!-- SUB:F3-SCR-PTY-001:END -->

<!-- SUB:F3-SCR-PTY-002:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,AC-PTY-011,AC-PTY-021,AC-PTY-029,AC-PTY-031,API-PTY-001,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-028,API-PTY-029,SCR-PTY-002 -->
### F3 · SCR-PTY-002 — Suppliers

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `SupplierEntryForm` | `partyNames`; `mobile`; `email`; `paymentTermDays` (shown whatever the state of `b2bCredit`, ADR-PTY-012); `taxRegistrationNo` optional (Advanced); `addresses: AddressDraft[]` | 1 (name group — SRS B3, AC-PTY-021) | SUPPLIER-SAVE |
| `AddressLineForm` (drawer) | shared with SCR-PTY-001 | 2 | returns the line — no call |
| `SupplierQuickRegisterForm` | `partyNames`; `mobile`; `email` | 1 (name group) | SUPPLIER-QUICK-REGISTER |
| `SupplierSearchForm` | the `SupplierSearchFilter` fields; `isActive` yes / no / any | 0 | the route's search parameters (SUPPLIER-SEARCH-QUERY) |

A Warehouse user (VIEW only) gets every form control read-only and no Save. Dirty tracking and command disabling as SCR-PTY-001.
<!-- SUB:F3-SCR-PTY-002:END -->

<!-- SUB:F3-SCR-PTY-003:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-106,REQ-PTY-108,REQ-PTY-109,AC-PTY-056,AC-PTY-077,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-122,AC-PTY-123,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-030,API-PTY-031,SCR-PTY-003 -->
### F3 · SCR-PTY-003 — Customer credit

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `CreditForm` | `creditLimit` optional decimal ≥ 0 → RULE-PTY-022 (empty = `null` = no credit allowance, REQ-PTY-068); `paymentTermDays` → RULE-PTY-008; refinement: when the loaded standing has `isWalkInFl = true` and either field is non-empty → RULE-PTY-011 on the form | 0 (SRS B3) | CREDIT-SAVE |
| `CreditSearchForm` | the `CreditSearchFilter` fields; `carriesCredit` yes / no / any | 0 | the route's search parameters (CREDIT-SEARCH-QUERY) |

A Seller/Cashier (VIEW only) gets `CreditForm` read-only and no Save or Clear credit. Clear credit is a command: disabled while `CreditForm` is dirty.
<!-- SUB:F3-SCR-PTY-003:END -->

<!-- SUB:F3-SCR-PTY-004:START traces=REQ-PTY-041,REQ-PTY-070,REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-107,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,API-PTY-002,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032,UXD-PTY-001,SCR-PTY-004 -->
### F3 · SCR-PTY-004 — Credit override requests

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `OverrideCreateForm` | `customerId` required (form required message), chosen in the Customer picker or prefilled; `saleAmount` decimal > 0 → RULE-PTY-032; `sourceDocumentRef` required, trimmed, maximum length of the document | 3 (SRS B3) | OVERRIDE-CREATE |
| `RejectForm` (decision panel) | `decisionReason` required when rejecting, trimmed → RULE-PTY-027 | 1, shown on Reject | OVERRIDE-REJECT |
| `OverrideSearchForm` | `docNo`; `statusCode` from CREDIT_OVERRIDE_STATUS; `customerId` from the Customer picker; `docDateFrom` ≤ `docDateTo` (dates, the document's `format: date`) | 0 | the route's search parameters (OVERRIDE-SEARCH-QUERY) |

Prefill (`/pty/credit-overrides/new?customerId=…&saleAmount=…&sourceDocumentRef=…`): values are parsed and validated by the same schema before they seed the form; an unparsable value is dropped and its field left empty. Approve and Cancel have no form (confirmation only).
<!-- SUB:F3-SCR-PTY-004:END -->

<!-- SUB:F3-SCR-PTY-005:START traces=REQ-PTY-070,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-097,AC-PTY-098,AC-PTY-101,API-PTY-024,API-PTY-025,SCR-PTY-005 -->
### F3 · SCR-PTY-005 — Credit control settings

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `CreditSettingsForm` | `creditOverrunActionCode` required, one of the CREDIT_OVERRUN_ACTION codes (WARN, BLOCK_WITH_OVERRIDE), rendered as a two-option radio group | 1 (SRS B3) | SETTINGS-SAVE |

Without PERM_PTY_CREDIT_SETTINGS_UPDATE the radio group is read-only and there is no Save.
<!-- SUB:F3-SCR-PTY-005:END -->
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,REQ-PTY-109,REQ-PTY-110,REQ-PTY-112,REQ-PTY-113,AC-PTY-007,AC-PTY-010,AC-PTY-011,AC-PTY-013,AC-PTY-021,AC-PTY-029,AC-PTY-031,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-077,AC-PTY-078,AC-PTY-082,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-101,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-122,AC-PTY-123,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-024,API-PTY-025,API-PTY-026,API-PTY-027,API-PTY-028,API-PTY-029,API-PTY-030,API-PTY-031,API-PTY-032,UXD-PTY-001,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
## F4 — Screens & Routes

Routes are registered by the PTY module's route file under the shell's layout; each composite screen is one lazy chunk. Every route element is wrapped by `RequirePermission(<PERM>)`; credit routes are also wrapped by `RequireFlag('b2bCredit')` (not mounted while off — REQ-PTY-070; mounted on the next screen load once on — REQ-PTY-073). Pages call their facade only.

Menu (Party «الأطراف»): Customers «العملاء» (PERM_PTY_CUSTOMER_VIEW) · Suppliers «الموردون» (PERM_PTY_SUPPLIER_VIEW) · group Credit «الائتمان», shown only while `b2bCredit` is on: Customer credit «ائتمان العملاء» (PERM_PTY_CUSTOMER_CREDIT_VIEW), Credit override requests «طلبات تجاوز حد الائتمان» (PERM_PTY_CREDIT_OVERRIDE_VIEW), Credit control settings «إعدادات ضبط الائتمان» (PERM_PTY_CREDIT_SETTINGS_VIEW). An entry the user cannot open is not shown; an empty group is not shown (no dead menus).

Second-level and line drawers: a drawer's open state is a nested route (going back closes it, dismissing closes only it, a deep link opens it). The layout route renders the first level and an `<Outlet/>` for the drawer as **siblings**; the drawer is portalled at the layout level, never inside the first level's element. A drawer carries no scroll region of its own (its content scrolls with the drawer body). A drawer **returns a value**: Confirm writes the line into the page's form state through the facade, Cancel discards the drawer's local draft; no drawer calls the API.

<!-- SUB:F4-SCR-PTY-001:START traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-047,REQ-PTY-049,REQ-PTY-070,REQ-PTY-071,REQ-PTY-073,REQ-PTY-110,AC-PTY-007,AC-PTY-010,AC-PTY-013,AC-PTY-055,AC-PTY-057,AC-PTY-078,AC-PTY-082,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-026,API-PTY-029,SCR-PTY-001 -->
### CUSTOMER-SCREEN — SCR-PTY-001            traces=REQ-PTY-004,REQ-PTY-008,REQ-PTY-012,REQ-PTY-013,REQ-PTY-030,API-PTY-001,API-PTY-003,API-PTY-026,API-PTY-029
Chunk        : `pty-customers`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/customers` | `CustomerSearchPage` | PERM_PTY_CUSTOMER_VIEW |
| `/pty/customers/new` | `CustomerEntryLayout` (mode new) | PERM_PTY_CUSTOMER_CREATE |
| `/pty/customers/new/addresses/new` · `/pty/customers/new/addresses/:addressKey` | `AddressLineDrawer` (child of the layout) | PERM_PTY_CUSTOMER_UPDATE |
| `/pty/customers/:customerId` | `CustomerEntryLayout` (mode edit; read-only without UPDATE) | PERM_PTY_CUSTOMER_VIEW |
| `/pty/customers/:customerId/addresses/new` | `AddressLineDrawer` | PERM_PTY_CUSTOMER_UPDATE |
| `/pty/customers/:customerId/addresses/:addressKey` | `AddressLineDrawer` (read-only without UPDATE) | PERM_PTY_CUSTOMER_VIEW |
Guard        : as the table; no VIEW → the unauthorized page
Facade       : CUSTOMER-FACADE (F2) · pages do not call queries directly
Cross-module : none
Composition  : `container page`. `CustomerEntryLayout` holds the form provider and renders, as siblings, `CustomerEntryView` (header group, Advanced group, read-only credit group, record footer, the inline `AddressPane`) and the drawer `<Outlet/>`. `AddressPane` is inline beneath the header: one row per line (type, label, line 1, city, default mark) with Add / open / Remove / Make default — all edit the draft only. `AddressLineDrawer` is the one-line drawer: opened from the route, returns the line to the form state, no save. `CustomerQuickRegister` (exported from `src/modules/pty/public`) is the Quick register sub-view: props `{ onRegistered(result: QuickRegisterResult), onCancel() }`; the host owns its container and open state (ADR-PTY-034); it has no address pane.
Saves        : ONE — `Save` «حفظ» runs CUSTOMER-SAVE: record call first, ADDRESSES-SYNC only after it succeeded, skipped when the address set is unchanged (F2). Commands (Deactivate, Reactivate, Make supplier) are separate confirmed calls, disabled while the form is dirty.
Navigation   : row click → `/pty/customers/:customerId` · New customer → `/pty/customers/new` · "Edit credit" «تعديل الائتمان» (flag on, PERM_PTY_CUSTOMER_CREDIT_VIEW) → `/pty/customer-credits/:customerId` · after Make supplier → `/pty/suppliers/:supplierPk` · RULE-PTY-002 action "Open that party" «فتح ذلك الطرف» → `/pty/customers/:customerPk` of the Customer holding the email, else `/pty/suppliers/:supplierPk` of the Supplier holding it (EMAIL-HOLDER-QUERY, ADR-PTY-043) · after a first save → replace with `/pty/customers/:customerId`.
<!-- SUB:F4-SCR-PTY-001:END -->

<!-- SUB:F4-SCR-PTY-002:START traces=REQ-PTY-006,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-110,AC-PTY-011,AC-PTY-021,AC-PTY-029,AC-PTY-031,API-PTY-001,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-028,API-PTY-029,SCR-PTY-002 -->
### SUPPLIER-SCREEN — SCR-PTY-002            traces=REQ-PTY-008,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-030,API-PTY-006,API-PTY-008,API-PTY-028,API-PTY-029
Chunk        : `pty-suppliers`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/suppliers` | `SupplierSearchPage` | PERM_PTY_SUPPLIER_VIEW |
| `/pty/suppliers/new` | `SupplierEntryLayout` (mode new) | PERM_PTY_SUPPLIER_CREATE |
| `/pty/suppliers/new/addresses/new` · `/pty/suppliers/new/addresses/:addressKey` | `AddressLineDrawer` | PERM_PTY_SUPPLIER_UPDATE |
| `/pty/suppliers/:supplierId` | `SupplierEntryLayout` (mode edit; read-only without UPDATE — Warehouse) | PERM_PTY_SUPPLIER_VIEW |
| `/pty/suppliers/:supplierId/addresses/new` | `AddressLineDrawer` | PERM_PTY_SUPPLIER_UPDATE |
| `/pty/suppliers/:supplierId/addresses/:addressKey` | `AddressLineDrawer` (read-only without UPDATE) | PERM_PTY_SUPPLIER_VIEW |
Guard        : as the table; no VIEW → the unauthorized page
Facade       : SUPPLIER-FACADE (F2)
Cross-module : none
Composition  : `container page`; same structure as SCR-PTY-001 — `SupplierEntryLayout` renders `SupplierEntryView` (with the inline `AddressPane`) and the drawer `<Outlet/>` as siblings; `AddressLineDrawer` and `AddressPane` are the shared components. `SupplierQuickRegister` is exported from `src/modules/pty/public` for purchase documents (props as `CustomerQuickRegister`).
Saves        : ONE — `Save` runs SUPPLIER-SAVE (record call, then ADDRESSES-SYNC only after success and only when the set changed). Commands (Deactivate, Reactivate, Make customer) are separate confirmed calls, disabled while dirty.
Navigation   : row click → `/pty/suppliers/:supplierId` · New supplier → `/pty/suppliers/new` · after Make customer → `/pty/customers/:customerPk` · RULE-PTY-002 action "Open that party" «فتح ذلك الطرف» → as SCR-PTY-001 (EMAIL-HOLDER-QUERY, ADR-PTY-043) · after a first save → replace with `/pty/suppliers/:supplierId`.
<!-- SUB:F4-SCR-PTY-002:END -->

<!-- SUB:F4-SCR-PTY-003:START traces=REQ-PTY-048,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-080,REQ-PTY-085,REQ-PTY-106,REQ-PTY-108,REQ-PTY-109,REQ-PTY-110,AC-PTY-056,AC-PTY-077,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-122,AC-PTY-123,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-030,API-PTY-031,SCR-PTY-003 -->
### CREDIT-SCREEN — SCR-PTY-003            traces=REQ-PTY-064,REQ-PTY-069,REQ-PTY-077,REQ-PTY-085,REQ-PTY-108,API-PTY-016,API-PTY-017,API-PTY-030,API-PTY-031
Chunk        : `pty-customer-credit`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/customer-credits` | `CreditSearchPage` | `b2bCredit` + PERM_PTY_CUSTOMER_CREDIT_VIEW |
| `/pty/customer-credits/:customerId` | `CreditEntryPage` (read-only without UPDATE) | `b2bCredit` + PERM_PTY_CUSTOMER_CREDIT_VIEW |
Guard        : flag off → not mounted (the shell's not-found / home); no VIEW → the unauthorized page
Facade       : CREDIT-FACADE (F2)
Cross-module : none
Composition  : `container page`. `CreditEntryPage` = read-only identity group, `CreditForm` (credit limit, payment terms), read-only standing (open exposure, available credit), then the inline `CreditHistoryPane` (read-only table with the body's own pager — no nested scroll region, no save).
Saves        : ONE — `Save` runs CREDIT-SAVE (one call). Clear credit is a separate confirmed command, disabled while dirty.
Navigation   : row click → `/pty/customer-credits/:customerId` · "Credit override requests" (PERM_PTY_CREDIT_OVERRIDE_VIEW) → `/pty/credit-overrides?customerId=:customerId` · "Credit control settings" (PERM_PTY_CREDIT_SETTINGS_VIEW) → `/pty/credit-settings` · entry `/pty/customer-credits?carriesCredit=true` seeds the filter (REQ-PTY-077).
<!-- SUB:F4-SCR-PTY-003:END -->

<!-- SUB:F4-SCR-PTY-004:START traces=REQ-PTY-041,REQ-PTY-070,REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-107,REQ-PTY-110,REQ-PTY-112,REQ-PTY-113,AC-PTY-078,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,API-PTY-002,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032,UXD-PTY-001,SCR-PTY-004 -->
### OVERRIDE-SCREEN — SCR-PTY-004            traces=REQ-PTY-091,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-101,REQ-PTY-103,UXD-PTY-001,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032
Chunk        : `pty-credit-overrides`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/credit-overrides` | `OverrideSearchPage` | `b2bCredit` + PERM_PTY_CREDIT_OVERRIDE_VIEW |
| `/pty/credit-overrides/new` | `OverrideCreatePage` (prefill from the search parameters) | `b2bCredit` + PERM_PTY_CREDIT_OVERRIDE_CREATE |
| `/pty/credit-overrides/:requestId` | `OverrideDetailPage` with the inline `DecisionPanel` | `b2bCredit` + PERM_PTY_CREDIT_OVERRIDE_VIEW |
Guard        : flag off → not mounted; no VIEW → the unauthorized page
Facade       : OVERRIDE-FACADE (F2)
Cross-module : UXD-PTY-001 — `decidedBy` is rendered through USER-LABEL-LOOKUP (given `sec`); no other foreign field
Composition  : `container page`. `OverrideDetailPage` = read-only request fields, then the inline `DecisionPanel` (PENDING only): `RejectForm` reason field + Approve / Reject (UPDATE) and Cancel request (DELETE) commands. `CustomerPicker` (shared by the create page and the search form) is an inline combobox fed by OVERRIDE-CUSTOMER-PICK-QUERY; its list scrolls with the page, not in its own region.
Saves        : ONE per sub-view — New request: "Request approval" runs OVERRIDE-CREATE. Detail: the decision — Approve, Reject or Cancel request, each one confirmed call; never combined with another call.
Navigation   : row click → `/pty/credit-overrides/:requestId` · New request (CREATE) → `/pty/credit-overrides/new` · after create → replace with `/pty/credit-overrides/:requestId` · "Customer credit" (PERM_PTY_CUSTOMER_CREDIT_VIEW) → `/pty/customer-credits/:customerFk` · notification deep link → `/pty/credit-overrides/:requestId` · entry `?customerId=` seeds the Customer filter.
<!-- SUB:F4-SCR-PTY-004:END -->

<!-- SUB:F4-SCR-PTY-005:START traces=REQ-PTY-070,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-078,AC-PTY-097,AC-PTY-098,AC-PTY-101,API-PTY-024,API-PTY-025,SCR-PTY-005 -->
### SETTINGS-SCREEN — SCR-PTY-005            traces=REQ-PTY-086,REQ-PTY-087,REQ-PTY-090,API-PTY-024,API-PTY-025
Chunk        : `pty-credit-settings`
Routes       :
| Route | Element | Guard |
|---|---|---|
| `/pty/credit-settings` | `CreditSettingsPage` (read-only without UPDATE) | `b2bCredit` + PERM_PTY_CREDIT_SETTINGS_VIEW |
Guard        : flag off → not mounted; no VIEW → the unauthorized page
Facade       : SETTINGS-FACADE (F2)
Cross-module : none
Composition  : `container page`; one radio group, no secondary detail.
Saves        : ONE — `Save` runs SETTINGS-SAVE (one call).
Navigation   : none out; reached from the menu and from SCR-PTY-003.
<!-- SUB:F4-SCR-PTY-005:END -->
<!-- PHASE:F4:END -->

<!-- PHASE:SEC-FE:START traces=REQ-PTY-018,REQ-PTY-043,REQ-PTY-070,REQ-PTY-073,REQ-PTY-090,REQ-PTY-096,REQ-PTY-102,AC-PTY-023,AC-PTY-078,AC-PTY-082,AC-PTY-101,AC-PTY-109,AC-PTY-116,API-PTY-001,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-017,API-PTY-018,API-PTY-019,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-025,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
## SEC-FE

The frontend half of security. The server decides (every operation of api-spec-pty.yaml is guarded by the permission the backend registry names); the UI only avoids offering what will be refused. Permission names are the backend registry's 16 names (`PERM_<PAGE_CODE>_<ACTION>`, SRS B4) — none is declared here. A forbidden response is shown as the localized catalog message of `PTY-403-FORBIDDEN` (reads: the unauthorized page; mutations: a banner, the form kept — F2 STD).

| SCR | Navigation guard (no VIEW → unauthorized) | no CREATE | no UPDATE | no DELETE | Other |
|---|---|---|---|---|---|
| SCR-PTY-001 | PERM_PTY_CUSTOMER_VIEW on every route | "New customer" hidden, `/new` guarded; Quick register not offered by the host | Entry read-only, no Save; Address pane read-only (Add, Remove, Make default hidden) | Deactivate and Reactivate hidden | "Make supplier" needs PERM_PTY_SUPPLIER_CREATE as well; "Edit credit" needs `b2bCredit` + PERM_PTY_CUSTOMER_CREDIT_VIEW; the read-only credit values render only while `b2bCredit` is on (REQ-PTY-070, AC-PTY-078, AC-PTY-082) |
| SCR-PTY-002 | PERM_PTY_SUPPLIER_VIEW on every route | "New supplier" hidden, `/new` guarded | Entry read-only, no Save; Address pane read-only (Warehouse) | Deactivate and Reactivate hidden | "Make customer" needs PERM_PTY_CUSTOMER_CREATE as well |
| SCR-PTY-003 | `b2bCredit` + PERM_PTY_CUSTOMER_CREDIT_VIEW | no CREATE affordance exists | `CreditForm` read-only, no Save, no Clear credit (Seller/Cashier) | no DELETE affordance exists | links to SCR-PTY-004 / SCR-PTY-005 need their VIEW |
| SCR-PTY-004 | `b2bCredit` + PERM_PTY_CREDIT_OVERRIDE_VIEW | "New request" hidden, `/new` guarded | Approve and Reject hidden; the list holds the user's own requests only — server-side (REQ-PTY-102, AC-PTY-116) | Cancel request hidden | Cancel is offered to DELETE holders on PENDING requests; the server refuses a non-requester with RULE-PTY-029 (AC-PTY-109); the Customer picker needs PERM_PTY_CUSTOMER_VIEW — without it the global search returns nothing (REQ-PTY-043) |
| SCR-PTY-005 | `b2bCredit` + PERM_PTY_CREDIT_SETTINGS_VIEW | no CREATE affordance exists | radio group read-only, no Save (REQ-PTY-090, AC-PTY-101) | no DELETE affordance exists | — |

Menu: an entry whose VIEW the user lacks is not rendered; the Credit group is not rendered while `b2bCredit` is off (REQ-PTY-070) and appears on the next screen load after it is turned on, with no setup step (REQ-PTY-073). Permission and flag reads come from PERMISSIONS-INIT and APP-CONFIG-INIT only; no screen reads a role or an activity name (KB §3 D2). Route parameters and prefill parameters are validated by the zod schemas of F3 before use; nothing from a parameter is rendered as HTML.
<!-- PHASE:SEC-FE:END -->

<!-- PHASE:ALIGN-FE:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-003,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-019,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,REQ-PTY-044,REQ-PTY-045,REQ-PTY-046,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-050,REQ-PTY-051,REQ-PTY-052,REQ-PTY-053,REQ-PTY-054,REQ-PTY-055,REQ-PTY-056,REQ-PTY-057,REQ-PTY-058,REQ-PTY-059,REQ-PTY-060,REQ-PTY-061,REQ-PTY-062,REQ-PTY-063,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-070,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-074,REQ-PTY-075,REQ-PTY-076,REQ-PTY-077,REQ-PTY-078,REQ-PTY-079,REQ-PTY-080,REQ-PTY-081,REQ-PTY-082,REQ-PTY-083,REQ-PTY-084,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-097,REQ-PTY-098,REQ-PTY-099,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,REQ-PTY-109,REQ-PTY-110,REQ-PTY-111,REQ-PTY-112,REQ-PTY-113,API-PTY-001,API-PTY-002,API-PTY-003,API-PTY-004,API-PTY-005,API-PTY-006,API-PTY-007,API-PTY-008,API-PTY-009,API-PTY-010,API-PTY-011,API-PTY-012,API-PTY-013,API-PTY-014,API-PTY-015,API-PTY-016,API-PTY-017,API-PTY-018,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-024,API-PTY-025,API-PTY-026,API-PTY-027,API-PTY-028,API-PTY-029,API-PTY-030,API-PTY-031,API-PTY-032,API-PTY-033,API-PTY-034,API-PTY-035,API-PTY-036,API-PTY-037,UXD-PTY-001,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
## ALIGN-FE

Against this plan, the API document and the SRS ceiling. Every row names the check that backs it; the mark is the `gov.py analyze` report's result for that check, copied by the orchestrator — a clause the report says examined nothing is written `— examined nothing`, never ✓. The author writes no mark.

```
ALIGN — PTY v1
row           backing check       mark (from the analyze report)   assertion
SCREENS       orphans             stamped by the orchestrator      every SCR is referenced by a plan block
COMPOSITION   screen-composition  stamped by the orchestrator      every SCR names where its secondary detail sits and that it saves once
CONTAINER     composition-rule    stamped by the orchestrator      every SCR names its container, and a child collection sits where that container puts it
READS         ux-reads-spec       stamped by the orchestrator      every read a screen binds is an operation of api-spec-pty.yaml, and the plan names the document its mock server serves
UXD           orphans             stamped by the orchestrator      every UXD is cited by a plan block — this is where a UX decision closes
TRACES        traces              stamped by the orchestrator      every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces              stamped by the orchestrator      every API this plan cites is an operation of api-spec-pty.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface        stamped by the orchestrator      every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree      stamped by the orchestrator      every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages           stamped by the orchestrator      labels and messages in ar + en
MARKERS       markers             stamped by the orchestrator      the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist          stamped by the orchestrator      every ADR this plan cites exists on disk in analysis/decisions/PTY/
COVERAGE      (the report)        stamped by the orchestrator      the clauses the analyze report lists as having examined nothing — verbatim, or `none`
```

```yaml name=self-check
findings: 0
clean: true
```

### Operations coverage

Every operation of api-spec-pty.yaml, the screen action that calls it and its route. The six operations whose callers are other modules or the flag owner have no PTY route — marked ✗ by design, recorded in ADR-PTY-034; their REQs are bound in the API binding above. REQ-PTY-052 and REQ-PTY-054 have no operation (in-process invitation handler, ADR-PTY-025) and no frontend surface.

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| createCustomer | API-PTY-001 | SCR-PTY-001 Save (new) · Quick register; SCR-PTY-002 Make customer | /pty/customers/new · host of Quick register · /pty/suppliers/:supplierId | ✓ bound |
| readCustomer | API-PTY-002 | SCR-PTY-001 Entry load; SCR-PTY-004 Customer label | /pty/customers/:customerId · /pty/credit-overrides/new | ✓ bound |
| updateCustomer | API-PTY-003 | SCR-PTY-001 Save (existing) | /pty/customers/:customerId | ✓ bound |
| deactivateCustomer | API-PTY-004 | SCR-PTY-001 Deactivate | /pty/customers/:customerId | ✓ bound |
| reactivateCustomer | API-PTY-005 | SCR-PTY-001 Reactivate | /pty/customers/:customerId | ✓ bound |
| createSupplier | API-PTY-006 | SCR-PTY-002 Save (new) · Quick register; SCR-PTY-001 Make supplier | /pty/suppliers/new · host of Quick register · /pty/customers/:customerId | ✓ bound |
| readSupplier | API-PTY-007 | SCR-PTY-002 Entry load | /pty/suppliers/:supplierId | ✓ bound |
| updateSupplier | API-PTY-008 | SCR-PTY-002 Save (existing) | /pty/suppliers/:supplierId | ✓ bound |
| deactivateSupplier | API-PTY-009 | SCR-PTY-002 Deactivate | /pty/suppliers/:supplierId | ✓ bound |
| reactivateSupplier | API-PTY-010 | SCR-PTY-002 Reactivate | /pty/suppliers/:supplierId | ✓ bound |
| createAddress | API-PTY-011 | SCR-PTY-001 / SCR-PTY-002 Save (new lines) | /pty/customers/:customerId · /pty/suppliers/:supplierId (and /new) | ✓ bound |
| readAddress | API-PTY-012 | — (callers: Sales & POS, Purchasing, Storefront) | — consumer-only | ✗ by design — ADR-PTY-034 |
| updateAddress | API-PTY-013 | SCR-PTY-001 / SCR-PTY-002 Save (changed lines) | /pty/customers/:customerId · /pty/suppliers/:supplierId | ✓ bound |
| deactivateAddress | API-PTY-014 | SCR-PTY-001 / SCR-PTY-002 Save (removed lines) | /pty/customers/:customerId · /pty/suppliers/:supplierId | ✓ bound |
| setDefaultAddress | API-PTY-015 | SCR-PTY-001 / SCR-PTY-002 Save (default switch) | /pty/customers/:customerId · /pty/suppliers/:supplierId | ✓ bound |
| readCreditStanding | API-PTY-016 | SCR-PTY-003 Entry load | /pty/customer-credits/:customerId | ✓ bound |
| updateCredit | API-PTY-017 | SCR-PTY-003 Save | /pty/customer-credits/:customerId | ✓ bound |
| clearCredit | API-PTY-018 | SCR-PTY-003 Clear credit | /pty/customer-credits/:customerId | ✓ bound |
| createCreditOverrideRequest | API-PTY-019 | SCR-PTY-004 Request approval | /pty/credit-overrides/new | ✓ bound |
| readCreditOverrideRequest | API-PTY-020 | SCR-PTY-004 Detail load | /pty/credit-overrides/:requestId | ✓ bound |
| approveCreditOverrideRequest | API-PTY-021 | SCR-PTY-004 Approve | /pty/credit-overrides/:requestId | ✓ bound |
| rejectCreditOverrideRequest | API-PTY-022 | SCR-PTY-004 Reject | /pty/credit-overrides/:requestId | ✓ bound |
| cancelCreditOverrideRequest | API-PTY-023 | SCR-PTY-004 Cancel request | /pty/credit-overrides/:requestId | ✓ bound |
| readCreditSettings | API-PTY-024 | SCR-PTY-005 load | /pty/credit-settings | ✓ bound |
| updateCreditSettings | API-PTY-025 | SCR-PTY-005 Save | /pty/credit-settings | ✓ bound |
| searchCustomers | API-PTY-026 | SCR-PTY-001 Search; SCR-PTY-001 / SCR-PTY-002 "Open that party" (EMAIL-HOLDER-QUERY) | /pty/customers | ✓ bound |
| globalSearchCustomers | API-PTY-027 | SCR-PTY-004 Customer picker (the shell's global search is the other caller) | /pty/credit-overrides · /pty/credit-overrides/new | ✓ bound |
| searchSuppliers | API-PTY-028 | SCR-PTY-002 Search; SCR-PTY-001 / SCR-PTY-002 "Open that party" (EMAIL-HOLDER-QUERY) | /pty/suppliers | ✓ bound |
| listPartyAddresses | API-PTY-029 | SCR-PTY-001 / SCR-PTY-002 Addresses pane | /pty/customers/:customerId · /pty/suppliers/:supplierId | ✓ bound |
| searchCustomerCredits | API-PTY-030 | SCR-PTY-003 Search | /pty/customer-credits | ✓ bound |
| readCreditHistory | API-PTY-031 | SCR-PTY-003 Credit history pane | /pty/customer-credits/:customerId | ✓ bound |
| searchCreditOverrideRequests | API-PTY-032 | SCR-PTY-004 Search | /pty/credit-overrides | ✓ bound |
| defaultPosCustomer | API-PTY-033 | — (caller: Sales & POS) | — consumer-only | ✗ by design — ADR-PTY-034 |
| resolveGuestCustomer | API-PTY-034 | — (caller: Storefront) | — consumer-only | ✗ by design — ADR-PTY-034 |
| checkCredit | API-PTY-035 | — (callers: Sales & POS, Storefront) | — consumer-only | ✗ by design — ADR-PTY-034 |
| useCreditOverrideRequest | API-PTY-036 | — (callers: Sales & POS, Storefront) | — consumer-only | ✗ by design — ADR-PTY-034 |
| b2bCreditSwitchOffDependency | API-PTY-037 | — (caller: the capability-flag owner, OI-05 open) | — consumer-only | ✗ by design — ADR-PTY-034 |

Totals: 37 operations · 31 bound to a PTY screen action and route · 6 consumer-only (ADR-PTY-034) · 0 unmapped.
<!-- PHASE:ALIGN-FE:END -->

## Index

| Key | Display | SUBs |
|---|---|---|
| F1 | F1 — Models & Types | F1-SCR-PTY-001 … F1-SCR-PTY-005 |
| F2 | F2 — Data Hooks | F2-SCR-PTY-001 … F2-SCR-PTY-005 |
| F3 | F3 — Forms & Validators | F3-SCR-PTY-001 … F3-SCR-PTY-005 |
| F4 | F4 — Screens & Routes | F4-SCR-PTY-001 … F4-SCR-PTY-005 |
| SEC-FE | SEC-FE | — (never split) |
| ALIGN-FE | ALIGN-FE | — (never split) |

Mutual consistency (§3.4): SCR-PTY-001 … 005 of the ui-ux-spec each have one SUB in F1, F2, F3 and F4; every SUB names an SCR of the spec; UXD-PTY-001 is cited by F1-SCR-PTY-004, F2-SCR-PTY-004, F3-SCR-PTY-004 and F4-SCR-PTY-004.

## Hand-off

After the `P4` verdict the toolkit splits this plan and `registry-exec-fe-pty.md` into `/frontend-execution/` of the shared repo (`gov.py split --track frontend --module PTY --version 1`), tagged `pty-v1`. The implementer reads the plan in key order (F1 → ALIGN-FE), the ui-ux-spec for intent, and `api-spec-pty.yaml` for every shape — served by its mock server until the backend is delivered. A route, component, permission or field not traceable to a block here is a gap for an ADR, not an invention.
