<!-- source: PHASE:F3 / SUB:F3-SCR-PTY-004 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-PTY-102, AC-PTY-106, AC-PTY-107, AC-PTY-109, AC-PTY-111, AC-PTY-114, AC-PTY-115, AC-PTY-116, AC-PTY-117, API-PTY-002, API-PTY-019, API-PTY-020, API-PTY-021, API-PTY-022, API-PTY-023, API-PTY-027, API-PTY-032, REQ-PTY-041, REQ-PTY-070, REQ-PTY-072, REQ-PTY-089, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-107, SCR-PTY-004, UXD-PTY-001 -->
<!-- SUB:F3-SCR-PTY-004:START traces=REQ-PTY-041,REQ-PTY-070,REQ-PTY-072,REQ-PTY-089,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-107,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,API-PTY-002,API-PTY-019,API-PTY-020,API-PTY-021,API-PTY-022,API-PTY-023,API-PTY-027,API-PTY-032,UXD-PTY-001,SCR-PTY-004 -->
### F3 · SCR-PTY-004 — Credit override requests

| Form | Fields and validators | Mandatory marks | Submits to |
|---|---|---|---|
| `OverrideCreateForm` | `customerId` required (form required message), chosen in the Customer picker or prefilled; `saleAmount` decimal > 0 → RULE-PTY-032; `sourceDocumentRef` required, trimmed, maximum length of the document | 3 (SRS B3) | OVERRIDE-CREATE |
| `RejectForm` (decision panel) | `decisionReason` required when rejecting, trimmed → RULE-PTY-027 | 1, shown on Reject | OVERRIDE-REJECT |
| `OverrideSearchForm` | `docNo`; `statusCode` from CREDIT_OVERRIDE_STATUS; `customerId` from the Customer picker; `docDateFrom` ≤ `docDateTo` (dates, the document's `format: date`) | 0 | the route's search parameters (OVERRIDE-SEARCH-QUERY) |

Prefill (`/pty/credit-overrides/new?customerId=…&saleAmount=…&sourceDocumentRef=…`): values are parsed and validated by the same schema before they seed the form; an unparsable value is dropped and its field left empty. Approve and Cancel have no form (confirmation only).
<!-- SUB:F3-SCR-PTY-004:END -->
