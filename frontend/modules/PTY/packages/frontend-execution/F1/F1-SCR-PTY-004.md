<!-- source: PHASE:F1 / SUB:F1-SCR-PTY-004 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-102, AC-PTY-106, AC-PTY-107, AC-PTY-109, AC-PTY-111, AC-PTY-114, AC-PTY-115, AC-PTY-116, AC-PTY-117, API-PTY-002, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-027, API-PTY-032, REQ-PTY-041, REQ-PTY-070, REQ-PTY-072, REQ-PTY-089, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-107, SCR-PTY-004, UXD-PTY-001 -->
<!-- SUB:F1-SCR-PTY-004:START traces=REQ-PTY-041,REQ-PTY-070,REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-107,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,API-PTY-002,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032,UXD-PTY-001,SCR-PTY-004 -->
### F1 · SCR-PTY-004 — Credit override requests

| Type | Kind | Source |
|---|---|---|
| `CreditOverrideRequest`, `CreditOverrideRow`, `CreditOverrideCreateRequest`, `CreditOverrideDecisionRequest`, `CreditOverrideRejectRequest` | generated | API-PTY-019, 020, 021, 022, 023, 032 |
| `CustomerSearchHit`, `Customer` | generated | API-PTY-027 (Customer picker — `CustomerSearchHit.isWalkInFl` hides the walk-in Customer), API-PTY-002 (label of a prefilled Customer) |
| `OverrideSearchFilter` | frontend | the query parameters of API-PTY-032 (`docNo`, `statusCode`, `customerId`, `docDateFrom`, `docDateTo`, `page`, `size`; `sort` not sent by default — the server orders PENDING first then newest, REQ-PTY-101) |
| `OverrideCreateValues` | frontend | `CreditOverrideCreateRequest` as form state, seeded from the route's prefill parameters |
| `RejectValues` | frontend | `{ decisionReason }` — the body of API-PTY-022 without `docNo` (never sent, RULE-PTY-031) |
| `UserLabel` | frontend | `{ userId; displayName }` — UXD-PTY-001, from the given `sec` user read |
<!-- SUB:F1-SCR-PTY-004:END -->
