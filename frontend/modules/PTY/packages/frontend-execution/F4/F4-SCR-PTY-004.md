<!-- source: PHASE:F4 / SUB:F4-SCR-PTY-004 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-078, AC-PTY-102, AC-PTY-106, AC-PTY-107, AC-PTY-109, AC-PTY-111, AC-PTY-114, AC-PTY-115, AC-PTY-116, AC-PTY-117, API-PTY-002, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-027, API-PTY-032, REQ-PTY-041, REQ-PTY-070, REQ-PTY-072, REQ-PTY-089, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-107, REQ-PTY-110, REQ-PTY-112, REQ-PTY-113, REQ-PTY-114, SCR-PTY-004, UXD-PTY-001 -->
<!-- SUB:F4-SCR-PTY-004:START traces=REQ-PTY-041,REQ-PTY-070,REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-107,REQ-PTY-110,REQ-PTY-112,REQ-PTY-113,REQ-PTY-114,AC-PTY-078,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,API-PTY-002,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032,UXD-PTY-001,SCR-PTY-004 -->
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
Navigation   : row click → `/pty/credit-overrides/:requestId` · New request (CREATE) → `/pty/credit-overrides/new` · after create → replace with `/pty/credit-overrides/:requestId` · "Customer credit" (PERM_PTY_CUSTOMER_CREDIT_VIEW) → `/pty/customer-credits/:customerFk` · notification deep link → `/pty/credit-overrides/:requestId`: the module's route file declares the mapping referenceType `PTY_CREDIT_OVERRIDE_REQUEST` → `/pty/credit-overrides/:referenceId`, matching the dispatch of API-PTY-019 (referenceType `PTY_CREDIT_OVERRIDE_REQUEST`, referenceId = creditOverrideRequestPk); an unknown referenceType opens nothing · entry `?customerId=` seeds the Customer filter.
<!-- SUB:F4-SCR-PTY-004:END -->
