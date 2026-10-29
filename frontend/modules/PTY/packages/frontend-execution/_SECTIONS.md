<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# Frontend execution plan — Party / الأطراف (PTY) v1
══════════════════════════════════════════════════════════════════
Stage    : P3.2 Part B — frontend execution plan · Module : PTY · Version : v1 · Profile : commerce
Track    : frontend · Plan : exec · Lane : analysis
Bound to : api-spec-pty.yaml (OpenAPI 3.1.0, 38 operations, base path /api/v1/pty) — the API document derived from the P3.1 backend plan; served by the frontend executor's mock server until the backend is delivered
Design   : ui-ux-spec-pty.md (SCR-PTY-001 … 005, UXD-PTY-001) · flow-diagram-pty.md
Stack    : react-ts-vite · routing react-router · server state tanstack-query · forms react-hook-form · validation zod · client state zustand · one lazy chunk per composite screen
Decisions: ADR-PTY-033 · ADR-PTY-034 (raised here; §9 removed; amended by ADR-PTY-048) · ADR-PTY-012, ADR-PTY-024, ADR-PTY-031, ADR-PTY-037, ADR-PTY-039, ADR-PTY-041, ADR-PTY-042, ADR-PTY-043, ADR-PTY-045, ADR-PTY-046, ADR-PTY-048 (cited) — gate-analysis revise (G1, G20 and the backend changes the screens follow) · gate-analysis revise 2 (G10; lookups through API-PTY-038, RULE-PTY-035)
══════════════════════════════════════════════════════════════════

Reading order for the implementer: the API binding below, then F1 → F2 → F3 → F4 → SEC-FE → ALIGN-FE. Shapes (paths, methods, parameters, request and response schemas, paging, envelope, error bodies) are read in api-spec-pty.yaml by `x-api-id`; this plan cites the `API-*` id and never restates a shape. A route, component, permission or field not traceable to a block of this plan is not built — a gap is an ADR.

## Binding to the API document

The plan is bound to `api-spec-pty.yaml`. The binding below is the REQ → API link as the document's `x-traces` state it; `codes` is the runtime catalog code → RULE link that neither the document nor the SRS carries alone (29 rule rows of the catalog; the 10 PLATFORM-STD rows, ADR-PTY-024, have no RULE and are routed by the standard routing of F2).

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
  - {req: REQ-PTY-013, api: [API-PTY-026, API-PTY-038]}
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
  - {req: REQ-PTY-038, api: [API-PTY-011, API-PTY-013, API-PTY-038]}
  - {req: REQ-PTY-039, api: [API-PTY-001, API-PTY-003, API-PTY-038]}
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
  - {req: REQ-PTY-086, api: [API-PTY-025, API-PTY-038]}
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
  - {req: REQ-PTY-114, api: [API-PTY-019]}
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
  - {code: "PTY-422-OVERRIDE-NOT-REQUIRED", rule: RULE-PTY-035}
```

Reconciliation against the SRS (once):
- Every REQ that needs an operation has one. REQ-PTY-052 and REQ-PTY-054 need no HTTP operation: they are served by the in-process invitation handler (registry-exec-be Traceability; ADR-PTY-025) and have no frontend surface.
- Every operation maps to a REQ (each `x-traces` above). Six operations are bound to no PTY screen because their callers are other modules or the flag owner (SRS A8.1): API-PTY-012, API-PTY-033, API-PTY-034, API-PTY-035, API-PTY-036, API-PTY-037 — recorded in ADR-PTY-034 and in the ALIGN-FE operations coverage table. They are not used by this plan; nothing is unmapped.
- Values the document does not carry are not invented. RULE-PTY-002 offers an action that opens the conflicting Party, and the error body (`ErrorResponse`) does not carry that Party. The Party is found through the document's role searches instead: API-PTY-026, then API-PTY-028 (which now has an `email` filter), each with the entered email and `isActive=true`. The action keeps only the row whose normalised email equals the entered one (ADR-PTY-043, which supersedes ADR-PTY-034 §9). No interim remains.
- `currencyCode` (ADR-PTY-037) is carried by the credit types and is not rendered while the tenant has one currency; amounts are formatted in it.
- PTY's five lookup keys are read through PTY's own operation API-PTY-038 (`OwnedLookups`, ADR-PTY-048), by hooks of kind `lookup`.
- Reads the screens need that are not operations of this document — the `b2bCredit` flag (given application configuration), the session's permissions (given `sec`), the deciding user's name (given `sec`, UXD-PTY-001) — are hooks of kind `lookup` / `init`, never `read` (ADR-PTY-034).













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
