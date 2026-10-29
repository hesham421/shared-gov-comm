<!-- source: PHASE:F2 / SUB:F2-SCR-PTY-004 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-102, AC-PTY-106, AC-PTY-107, AC-PTY-109, AC-PTY-111, AC-PTY-114, AC-PTY-115, AC-PTY-116, AC-PTY-117, AC-PTY-132, AC-PTY-134, API-PTY-002, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-027, API-PTY-032, API-PTY-038, REQ-PTY-041, REQ-PTY-070, REQ-PTY-072, REQ-PTY-089, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-107, REQ-PTY-110, REQ-PTY-112, REQ-PTY-113, REQ-PTY-114, SCR-PTY-004, UXD-PTY-001 -->
<!-- SUB:F2-SCR-PTY-004:START traces=REQ-PTY-041,REQ-PTY-070,REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-107,REQ-PTY-110,REQ-PTY-112,REQ-PTY-113,REQ-PTY-114,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-132,AC-PTY-134,API-PTY-002,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032,API-PTY-038,UXD-PTY-001,SCR-PTY-004 -->
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
    api: [API-PTY-038]
    cache_key: "[pty, lookups, CREDIT_OVERRIDE_STATUS]"
    loading: LOCAL
  - hook: CREDIT_CHECK_RESULT-LOOKUP
    kind: lookup
    api: [API-PTY-038]
    cache_key: "[pty, lookups, CREDIT_CHECK_RESULT]"
    loading: LOCAL
  - hook: OVERRIDE-SCREEN-INIT
    kind: init
    cache_key: "[app-config] + [auth, permissions]"
    loading: LOCAL
  - hook: OVERRIDE-CREATE
    kind: mutation
    api: [API-PTY-019]
    errors: "STD; PTY-422-SALE-AMOUNT-NOT-POSITIVE → inline on saleAmount, text: RULE-PTY-032 message (SRS); PTY-422-WALK-IN-NO-CREDIT-SALE → inline on the Customer picker, text: RULE-PTY-033 message (SRS); PTY-422-ROLE-INACTIVE → inline on the Customer picker, text: RULE-PTY-005 message (SRS); PTY-409-REQUEST-OPEN-FOR-SALE → form banner, text: RULE-PTY-034 message (SRS), with the action show this sale's requests (`/pty/credit-overrides?customerId=<customerId>&statusCode=PENDING`); PTY-422-OVERRIDE-NOT-NEEDED → form banner, text: RULE-PTY-025 message (SRS); PTY-422-OVERRIDE-NOT-REQUIRED → form banner, text: RULE-PTY-035 message (SRS); PTY-422-CREDIT-NOT-ENABLED → form banner, text: RULE-PTY-023 message (SRS); PTY-404-CUSTOMER-NOT-FOUND → inline on the Customer picker, text: catalogue PTY-404-CUSTOMER-NOT-FOUND"
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
Kind read query; key = the whole `OverrideSearchFilter`. No `sort` is sent by default so the server's order holds (PENDING first, then newest). The server returns only the caller's own requests when the caller lacks UPDATE (REQ-PTY-102); the screen does not filter again. The `customerId` filter is chosen in the Customer picker (active Customers only, walk-in hidden) or seeded by the route (`?customerId=` from SCR-PTY-003 — one Customer, active or not; an inactive Customer's filter is reachable by URL only).

#### SCR-PTY-004 · OVERRIDE-QUERY — API-PTY-020     traces=API-PTY-020,REQ-PTY-103,AC-PTY-117
Kind read query for the Detail. `decidedBy` (an identifier) is labelled by USER-LABEL-LOOKUP (UXD-PTY-001); `createdBy` is a name already and is shown as is.

#### SCR-PTY-004 · OVERRIDE-CUSTOMER-PICK-QUERY — API-PTY-027     traces=API-PTY-027,REQ-PTY-041
Kind read query feeding the Customer picker (create sub-view and the Search filter). Enabled when the typed term has at least 2 characters, debounced 300 ms; at most 10 active Customers come back (REQ-PTY-041). Options show code + name in the active language. A hit with `isWalkInFl: true` is not offered: the walk-in Customer cannot carry a request (RULE-PTY-033), so the picker hides it instead of letting the submit be refused. The picker offers active Customers only; a `customerId` filter for an inactive Customer (whose requests stay readable) is reachable by URL only (`/pty/credit-overrides?customerId=…`, as SCR-PTY-003 opens it).

#### SCR-PTY-004 · OVERRIDE-CUSTOMER-LABEL-QUERY — API-PTY-002     traces=API-PTY-002,REQ-PTY-091
Kind read query that labels a Customer id the screen holds but did not pick: the prefilled `customerId` of the New request and the seeded `customerId` filter. Same key as CUSTOMER-QUERY (one cache entry per Customer).

#### SCR-PTY-004 · OVERRIDE-CREATE — API-PTY-019     traces=API-PTY-019,REQ-PTY-072,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-104,REQ-PTY-105,REQ-PTY-114,AC-PTY-102
Kind mutation, the New request's one submit ("Request approval"). On success navigates (replace) to `/pty/credit-overrides/:requestId` of the response, which shows the assigned number and PENDING.

#### SCR-PTY-004 · OVERRIDE-APPROVE / OVERRIDE-REJECT / OVERRIDE-CANCEL — API-PTY-021 · API-PTY-022 · API-PTY-023     traces=API-PTY-021,API-PTY-022,API-PTY-023,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-107,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114
Kind mutation (decision commands, each a single call with confirmation). Approve sends no body (the body is optional; `docNo` is never sent). Reject sends `{decisionReason}`. Cancel has no body. After success the request key is refreshed and the Detail shows the new status.

#### SCR-PTY-004 · OVERRIDE-SCREEN-INIT and OVERRIDE-FACADE
INIT: `b2bCredit`; PERM_PTY_CREDIT_OVERRIDE_VIEW / _CREATE / _UPDATE / _DELETE, PERM_PTY_CUSTOMER_VIEW (picker), PERM_PTY_CUSTOMER_CREDIT_VIEW (link); lookups CREDIT_OVERRIDE_STATUS, CREDIT_CHECK_RESULT; the request by id when the route carries `:requestId`; the prefill parameters on `/new`. FACADE: filter, selected request, picker term, `create()`, `approve()`, `reject(reason)`, `cancel()`; the decision commands exist only while `statusCode = PENDING`.
<!-- SUB:F2-SCR-PTY-004:END -->
