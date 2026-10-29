# Backend test plan — Party / الأطراف (PTY) v1
══════════════════════════════════════════════════════════════════
Stage    : P4 — Test Plan · pass 2 · track backend · plan test · lane test-gen
Module   : PTY   Version : v1   Profile : commerce
Sources  : _state/current-srs.md (P1 v1 — REQ 114 · AC 140 · RULE 35) · _state/current-registry-srs.md · _state/current-registry-db.md (XM register `records: []`) · _state/current-backend-execution-plan.md (P3.1 — API 38 · Error Catalog 39 rows) · _state/current-frontend-execution-plan.md (P3.2 — read for the track split only) · _state/current-api-spec.yaml (api-spec-pty.yaml, OpenAPI 3.1.0, 38 operations) · _state/current-dependency-graph.md (PTY tier 1, no outbound edge)
Counts   : TC 156 on this track (TC-PTY-001 … TC-PTY-121, TC-PTY-154 … TC-PTY-178, TC-PTY-191 … TC-PTY-200 — the revise additions follow the frontend plan's TC-PTY-122 … 153 and TC-PTY-179 … 190, no id renumbered) · AC covered here 138/140 (AC-PTY-078, AC-PTY-087 by the frontend plan) · module AC coverage 140/140 · XM 0 · INT-XM absent · gate-analysis revise: G16, G19 and the new ACs applied · gate-analysis revise 2: G1, G2 (N7), G5, G6, G7, G8, G11 and API-PTY-038 applied (N16)
Framework: `profile.stack.testing` backend = agnostic — each TC below is the whole contract; the consumer repo chooses how to turn it into a test
Open ADRs: 0 · cited: ADR-PTY-016, ADR-PTY-024, ADR-PTY-025, ADR-PTY-026, ADR-PTY-028, ADR-PTY-031, ADR-PTY-035, ADR-PTY-036, ADR-PTY-037, ADR-PTY-038, ADR-PTY-039, ADR-PTY-040, ADR-PTY-041, ADR-PTY-042, ADR-PTY-043, ADR-PTY-045, ADR-PTY-046, ADR-PTY-048 (all ACCEPTED)
══════════════════════════════════════════════════════════════════

Every TC binds one `API-*` of api-spec-pty.yaml (or, where the plan has no endpoint, the in-process handler the backend plan names); the request and response shapes, statuses and error bodies are read there by `x-api-id`, never restated here. Rule messages are copied from the SRS character for character and asserted in both languages; the server returns them as `LocalizedException → {code, messageAr, messageEn}`.

## Derivation notes (non-mechanical choices — non-breaking)

This run writes only the two test plans; the choices below are recorded here for the analysis gate.

- **N1 — Track split.** Every AC whose Then is observable at an `API-*` (or at the in-process invitation handler) has one TC on this track. AC-PTY-078 (menu and screen content while `b2bCredit` is off) and AC-PTY-087 (the next action opens a screen) have no backend observable and are covered only by the frontend plan (TC-PTY-129, TC-PTY-151).
- **N2 — Package rule.** PERMISSION TCs name `SEC-BE` (its traces hold REQ-PTY-043, REQ-PTY-090, REQ-PTY-102). STATE TCs (an entity invariant or lifecycle transition) name the DATA-DOM SUB whose `traces=` hold the AC's REQ, else `CORE` when CORE's traces hold it. Every other TC names the SVC-API SUB that holds the `API-*` it exercises. `DOC`, `ALIGN-BE` and `INT-C` receive no TC: no AC asserts documentation, alignment or a cross-module edge (0 XM).
- **N3 — Named identifiers.** Identifiers, codes and times an AC names (Customer 5001, Party P-000120, CO-000031, user 88001, 2026-11-02 10:15) are the AC's values. Identity keys and number-series values are system-assigned, so the executor binds each named value to the row it creates (a deterministic number series and a test clock where the AC fixes a code or a time).
- **N4 — Host data.** CUSTOMER_GROUP `VIP` (AC-PTY-046) is not seeded. TC-PTY-020 creates it, then deactivates it, through the given mdl HTTP surface that SRS A6 names:
  - the CUSTOMER_GROUP type is found by `POST /api/v1/mdl/lookup-types/search`;
  - the value is added by `POST /api/v1/mdl/lookup-types/{id}/values`;
  - the value is deactivated by `DELETE /api/v1/mdl/lookup-values/{id}`.
  ADDRESS_TYPE `OFFICE` (AC-PTY-044) must not be an active value and is never created; TC-PTY-018's Host data line names the call it does not make.

  The gate found that C10.8 (tc-data) and C10.9 (tc-consistency) examined no subject. Both checks only examine the values of an open lookup that the SRS `lookups` block lists in `values` beyond `seeded`. That block listed `values` equal to `seeded`, so VIP and OFFICE were invisible to the checks. SRS A6 now lists them (`values: [RETAIL, WHOLESALE, VIP]`, `[BILLING, DELIVERY, OFFICE]`), so both clauses examine TC-PTY-018 and TC-PTY-020. No other TC names a non-seeded value of an open key, and the two values are demanded in no opposite states.
- **N5 — Observation points without an API.**
  - The Party active flag (DBF-PTY-008), the Customer's `secUserId` (DBF-PTY-017, never returned — CON-PTY-009) and the walk-in row count are read from the stored column.
  - Events, audit records, invitations, notifications and number series are observed at the given seam the backend plan names: `OutboxPublisher` for `pty.PartyRoleDeactivated`, the `@Audited` rows of entity types PTY_CUSTOMER and PTY_CREDIT_OVERRIDE_REQUEST, `AuditApi.record` business actions, `SecCustomerInvitationApi.issue`, `NotificationDispatchApi.dispatch`, and `NumberSeriesApi`.
  - The redemption of a CUSTOMER-realm invitation has no endpoint. TC-PTY-027 and TC-PTY-088 deliver a `sec.CustomerInvitationRedeemed` outbox message to `PtyCustomerInvitationRedeemedSubscriber` (backend plan CORE, SVC-API-INT).
- **N6 — AC-PTY-104 "a later update carrying docNo".** The request has no update endpoint; the change endpoints that carry `docNo` are API-PTY-021, 022, 036 (RULE-PTY-031). TC-PTY-041 uses API-PTY-021.
- **N7 — Consumer callers.** "Sales & POS / Purchasing / Settlements / Pricing requests …" is executed as a REST call by a principal holding the endpoint's permission (an in-process caller's security context must hold the PTY authority of the method it calls — ADR-PTY-039, which withdrew ADR-PTY-025's exemption). For AC-PTY-117 that principal is the request's requester (REQ-PTY-102 visibility).
- **N8 — Flag state not stated.** AC-PTY-090, 091, 095, 099, 100, 103, 118 run with `b2bCredit` on: with it off the credit check answers NOT_APPLICABLE and the request is refused by RULE-PTY-023 before the rule the AC targets.
- **N9 — PUT bodies.** API-PTY-003, 008, 013, 017 replace the editable set; fields the AC does not name are sent as currently stored.
- **N10 — Exposure.** Open exposure comes from registered `CustomerCreditExposureContributor` beans (ADR-PTY-028); a TC fixes it with test contributors reporting the AC's amounts.
- **N11 — Other modules' documents.** Sales invoices, purchase orders and sales orders an AC mentions as references (AC-PTY-019, 025, 039) are not created — they are other modules' behaviour; the TC asserts that this module's read still returns the record.
- **N12 — Boundary TCs.** The ACs that sit on a numeric limit (AC-PTY-049, 075, 092, 093, 095, 103) are themselves tagged BOUNDARY; no extra boundary TC is added.
- **N13 — Concurrency guards (gate-analysis G16).** Every guard the backend plan states (ADR-PTY-026, 040, 042) has one barrier-synchronised TC. Two threads, each with its own session and transaction, are released together. The TC asserts exactly one success and the loser's catalog code, or the single consistent end state where both calls are allowed to succeed. The guards and their TCs:
  - approve vs approve: TC-PTY-168;
  - approve vs cancel: TC-PTY-169;
  - double use of one approval: TC-PTY-170;
  - the walk-in race: TC-PTY-171;
  - the email race: TC-PTY-172;
  - the Party-lock serialisation of role deactivations: TC-PTY-173;
  - the Party-lock serialisation of Address ordinals and defaults: TC-PTY-174;
  - audit chaining of credit edits: TC-PTY-175;
  - the open-request race: TC-PTY-176;
  - a Customer deactivation racing a request creation (Customer lock, QR-PTY-039 cancellation): TC-PTY-192;
  - a Customer deactivation racing an approval and a use (QR-PTY-039 plus the compare-and-set of QR-PTY-034, three outcomes): TC-PTY-193;
  - the guest email race of API-PTY-034 (REQUIRES_NEW plus one re-match; TC-PTY-172 covers API-PTY-001 only): TC-PTY-194;
  - an Address type change under the Party lock, racing an Address create of the same type: TC-PTY-195.
  Each derives from the AC whose outcome the guard protects, so an AC can carry a second TC.
- **N14 — Tenancy and authority (gate-analysis G2, G3, G5, G14).**
  - TC-PTY-178 runs with two tenants (`@MultiTenantOnly`): a record of another tenant is not found.
  - TC-PTY-177 calls `PtyPartyApi` under a context without the PTY authority (ADR-PTY-039).
  - TC-PTY-167 asserts that either role's UPDATE is enough for the Address of a two-role Party.
- **N15 — Revise additions.** TC-PTY-154 … 166 cover AC-PTY-124 … 136, the ACs the gate-analysis revise added to the SRS.
- **N16 — Revise 2 additions.** TC-PTY-191 covers AC-PTY-137 (RULE-PTY-035, a request refused under WARN — ADR-PTY-045). TC-PTY-192 … 195 are the four guards of N13 added by G5. TC-PTY-196 covers AC-PTY-138 (Customer VIEW reads the credit values — ADR-PTY-046). TC-PTY-197 covers AC-PTY-139 (a term with spaces and dashes; `%` matched literally). TC-PTY-198 covers AC-PTY-140 (the Customer is checked before the flag). TC-PTY-199 covers the visibility of API-PTY-020 (another user's request answers 404). TC-PTY-200 covers API-PTY-038 (OwnedLookups — ADR-PTY-048); its VIP host data is the state TC-PTY-020 uses, so no value is required in opposite states.

<!-- PHASE:TEST-PLAN-BE:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-003,REQ-PTY-004,REQ-PTY-005,REQ-PTY-006,REQ-PTY-007,REQ-PTY-008,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-019,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-023,REQ-PTY-024,REQ-PTY-025,REQ-PTY-026,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-034,REQ-PTY-035,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,REQ-PTY-044,REQ-PTY-045,REQ-PTY-046,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-050,REQ-PTY-051,REQ-PTY-052,REQ-PTY-053,REQ-PTY-054,REQ-PTY-055,REQ-PTY-056,REQ-PTY-057,REQ-PTY-058,REQ-PTY-059,REQ-PTY-060,REQ-PTY-061,REQ-PTY-062,REQ-PTY-063,REQ-PTY-064,REQ-PTY-065,REQ-PTY-066,REQ-PTY-067,REQ-PTY-068,REQ-PTY-069,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-074,REQ-PTY-075,REQ-PTY-076,REQ-PTY-078,REQ-PTY-079,REQ-PTY-080,REQ-PTY-081,REQ-PTY-082,REQ-PTY-083,REQ-PTY-084,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-092,REQ-PTY-093,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-097,REQ-PTY-098,REQ-PTY-099,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,REQ-PTY-109,REQ-PTY-110,REQ-PTY-111,REQ-PTY-112,REQ-PTY-113,REQ-PTY-114,AC-PTY-001,AC-PTY-002,AC-PTY-003,AC-PTY-004,AC-PTY-005,AC-PTY-006,AC-PTY-007,AC-PTY-008,AC-PTY-009,AC-PTY-010,AC-PTY-011,AC-PTY-012,AC-PTY-013,AC-PTY-014,AC-PTY-015,AC-PTY-016,AC-PTY-017,AC-PTY-018,AC-PTY-019,AC-PTY-020,AC-PTY-021,AC-PTY-022,AC-PTY-023,AC-PTY-024,AC-PTY-025,AC-PTY-026,AC-PTY-027,AC-PTY-028,AC-PTY-029,AC-PTY-030,AC-PTY-031,AC-PTY-032,AC-PTY-033,AC-PTY-034,AC-PTY-035,AC-PTY-036,AC-PTY-037,AC-PTY-038,AC-PTY-039,AC-PTY-040,AC-PTY-041,AC-PTY-042,AC-PTY-043,AC-PTY-044,AC-PTY-045,AC-PTY-046,AC-PTY-047,AC-PTY-048,AC-PTY-049,AC-PTY-050,AC-PTY-051,AC-PTY-052,AC-PTY-053,AC-PTY-054,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-058,AC-PTY-059,AC-PTY-060,AC-PTY-061,AC-PTY-062,AC-PTY-063,AC-PTY-064,AC-PTY-065,AC-PTY-066,AC-PTY-067,AC-PTY-068,AC-PTY-069,AC-PTY-070,AC-PTY-071,AC-PTY-072,AC-PTY-073,AC-PTY-074,AC-PTY-075,AC-PTY-076,AC-PTY-077,AC-PTY-079,AC-PTY-080,AC-PTY-081,AC-PTY-082,AC-PTY-083,AC-PTY-084,AC-PTY-085,AC-PTY-086,AC-PTY-088,AC-PTY-089,AC-PTY-090,AC-PTY-091,AC-PTY-092,AC-PTY-093,AC-PTY-094,AC-PTY-095,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-099,AC-PTY-100,AC-PTY-101,AC-PTY-102,AC-PTY-103,AC-PTY-104,AC-PTY-105,AC-PTY-106,AC-PTY-107,AC-PTY-108,AC-PTY-109,AC-PTY-110,AC-PTY-111,AC-PTY-112,AC-PTY-113,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-118,AC-PTY-119,AC-PTY-120,AC-PTY-121,AC-PTY-122,AC-PTY-123,AC-PTY-124,AC-PTY-125,AC-PTY-126,AC-PTY-127,AC-PTY-128,AC-PTY-129,AC-PTY-130,AC-PTY-131,AC-PTY-132,AC-PTY-133,AC-PTY-134,AC-PTY-135,AC-PTY-136,AC-PTY-137,AC-PTY-138,AC-PTY-139,AC-PTY-140 -->
## TEST-PLAN-BE — module scenarios

156 TCs (> 12), split in two:
- RULE-SCENARIOS — violations, boundaries, state transitions and the barrier-synchronised concurrency guards: 80 TCs;
- API-SCENARIOS — happy paths, permission, paging and empty results: 76 TCs.

<!-- SUB:RULE-SCENARIOS:START traces=REQ-PTY-002,REQ-PTY-003,REQ-PTY-006,REQ-PTY-009,REQ-PTY-010,REQ-PTY-011,REQ-PTY-012,REQ-PTY-022,REQ-PTY-024,REQ-PTY-027,REQ-PTY-028,REQ-PTY-029,REQ-PTY-030,REQ-PTY-031,REQ-PTY-034,REQ-PTY-036,REQ-PTY-037,REQ-PTY-038,REQ-PTY-039,REQ-PTY-040,REQ-PTY-041,REQ-PTY-044,REQ-PTY-046,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-050,REQ-PTY-053,REQ-PTY-054,REQ-PTY-055,REQ-PTY-062,REQ-PTY-066,REQ-PTY-067,REQ-PTY-071,REQ-PTY-072,REQ-PTY-073,REQ-PTY-074,REQ-PTY-076,REQ-PTY-081,REQ-PTY-082,REQ-PTY-084,REQ-PTY-091,REQ-PTY-092,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-097,REQ-PTY-098,REQ-PTY-099,REQ-PTY-100,REQ-PTY-104,REQ-PTY-105,REQ-PTY-106,REQ-PTY-109,REQ-PTY-110,REQ-PTY-111,REQ-PTY-112,REQ-PTY-113,REQ-PTY-114,AC-PTY-003,AC-PTY-004,AC-PTY-005,AC-PTY-008,AC-PTY-012,AC-PTY-013,AC-PTY-014,AC-PTY-015,AC-PTY-017,AC-PTY-027,AC-PTY-029,AC-PTY-032,AC-PTY-033,AC-PTY-034,AC-PTY-035,AC-PTY-036,AC-PTY-040,AC-PTY-042,AC-PTY-043,AC-PTY-044,AC-PTY-045,AC-PTY-046,AC-PTY-049,AC-PTY-052,AC-PTY-054,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-061,AC-PTY-062,AC-PTY-063,AC-PTY-070,AC-PTY-074,AC-PTY-075,AC-PTY-079,AC-PTY-080,AC-PTY-081,AC-PTY-082,AC-PTY-083,AC-PTY-085,AC-PTY-092,AC-PTY-093,AC-PTY-095,AC-PTY-103,AC-PTY-104,AC-PTY-106,AC-PTY-107,AC-PTY-108,AC-PTY-109,AC-PTY-110,AC-PTY-111,AC-PTY-112,AC-PTY-113,AC-PTY-114,AC-PTY-118,AC-PTY-119,AC-PTY-120,AC-PTY-123,AC-PTY-124,AC-PTY-125,AC-PTY-126,AC-PTY-127,AC-PTY-128,AC-PTY-129,AC-PTY-130,AC-PTY-131,AC-PTY-132,AC-PTY-133,AC-PTY-134,AC-PTY-137,AC-PTY-140 -->
### RULE-SCENARIOS — violations, boundaries, state transitions

<!-- TC:TC-PTY-001:START traces=AC-PTY-004,REQ-PTY-003,API-PTY-002 -->
### TC-PTY-001 — Inactive Customer refused for a new document
Derived from : AC-PTY-004  (REQ-PTY-003)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id}?forNewDocument=true
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Customer 5002 of Party P-000121 with isActiveFl false; the caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5002 with forNewDocument=true (selection for a new sales invoice)
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.»
Test data    : customerId 5002, Party code P-000121 (AC)
<!-- TC:TC-PTY-001:END -->

<!-- TC:TC-PTY-002:START traces=AC-PTY-005,REQ-PTY-003,API-PTY-007 -->
### TC-PTY-002 — Inactive Supplier refused for a new document
Derived from : AC-PTY-005  (REQ-PTY-003)
Exercises    : API-PTY-007 GET /api/v1/pty/suppliers/{id}?forNewDocument=true
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Supplier 7001 of Party P-000200 with isActiveFl false; the caller (Purchasing principal) holds PERM_PTY_SUPPLIER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/suppliers/7001 with forNewDocument=true (selection for a new purchase order)
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000200 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000200 is inactive and cannot be used on a new document. Reactivate it or choose another party.»
Test data    : supplierId 7001, Party code P-000200 (AC)
<!-- TC:TC-PTY-002:END -->

<!-- TC:TC-PTY-003:START traces=AC-PTY-008,REQ-PTY-006,API-PTY-001 -->
### TC-PTY-003 — Party code taken from the core number series
Derived from : AC-PTY-008  (REQ-PTY-006)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : RULE-PTY-007 → — (assignment; no refusal)
Package      : CORE
Scenario     : STATE · data class VALID · language ALL
Preconditions: the given number series PTY_PARTY (`NumberSeriesApi`) returns P-000122 as its next value; caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with a registration carrying one name `<nameEn>` · 2. GET /api/v1/pty/customers/{customerPk of step 1}
Expected     : step 1 → 201, data.code P-000122 · step 2 → data.code P-000122; exactly one value was drawn from series PTY_PARTY
Test data    : series value P-000122 (AC); `<nameEn>` placeholder — the AC names no registration data
<!-- TC:TC-PTY-003:END -->

<!-- TC:TC-PTY-004:START traces=AC-PTY-012,REQ-PTY-009,API-PTY-001 -->
### TC-PTY-004 — Registration without any name refused
Derived from : AC-PTY-012  (REQ-PTY-009)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : RULE-PTY-001 → PTY-422-NAME-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: caller holds PERM_PTY_CUSTOMER_CREATE; the tenant's Party and Customer counts are recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameAr "" and nameEn ""
Expected     : 422 · code PTY-422-NAME-REQUIRED · messageAr «أدخل اسم الطرف بالعربية أو بالإنجليزية.» · messageEn «Enter the party name in Arabic or English.» · Party and Customer counts unchanged
Test data    : nameAr empty, nameEn empty (AC)
<!-- TC:TC-PTY-004:END -->

<!-- TC:TC-PTY-005:START traces=AC-PTY-013,REQ-PTY-010,API-PTY-001 -->
### TC-PTY-005 — Email held by another active Party refused, case ignored
Derived from : AC-PTY-013  (REQ-PTY-010)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: active Party P-000120 holds email "nour@example.com"; caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with name `<nameEn>` and email "Nour@Example.com"
Expected     : 409 · code PTY-409-EMAIL-TAKEN · messageAr «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000120). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · messageEn «This email is already registered to another active party (P-000120). Open that party instead of creating a new one.» · nothing saved (Party count unchanged). The action that opens P-000120 is the frontend's (TC-PTY-123)
Test data    : emails "nour@example.com", "Nour@Example.com", code P-000120 (AC); `<nameEn>` placeholder
<!-- TC:TC-PTY-005:END -->

<!-- TC:TC-PTY-006:START traces=AC-PTY-014,REQ-PTY-011,API-PTY-001 -->
### TC-PTY-006 — Email without "@" refused
Derived from : AC-PTY-014  (REQ-PTY-011)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : RULE-PTY-003 → PTY-422-EMAIL-FORMAT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with name `<nameEn>` and email "nour.example.com"
Expected     : 422 · code PTY-422-EMAIL-FORMAT · messageAr «صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com» · messageEn «The email format is not valid, for example: name@example.com» · nothing saved
Test data    : email "nour.example.com" (AC); `<nameEn>` placeholder
<!-- TC:TC-PTY-006:END -->

<!-- TC:TC-PTY-007:START traces=AC-PTY-015,REQ-PTY-011,API-PTY-006 -->
### TC-PTY-007 — Supplier mobile shorter than 7 digits refused
Derived from : AC-PTY-015  (REQ-PTY-011)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers
Rule / code  : RULE-PTY-004 → PTY-422-MOBILE-FORMAT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: caller holds PERM_PTY_SUPPLIER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with name `<nameEn>` and mobile "12345"
Expected     : 422 · code PTY-422-MOBILE-FORMAT · messageAr «رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +.» · messageEn «The mobile number has 7 to 15 digits and can start with +.» · nothing saved
Test data    : mobile "12345" (AC); `<nameEn>` placeholder
<!-- TC:TC-PTY-007:END -->

<!-- TC:TC-PTY-008:START traces=AC-PTY-017,REQ-PTY-012,API-PTY-003 -->
### TC-PTY-008 — Party code cannot be changed by an update
Derived from : AC-PTY-017  (REQ-PTY-012)
Exercises    : API-PTY-003 PUT /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-007 → PTY-422-CODE-READ-ONLY
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Customer 5001 with Party code P-000120; caller holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customers/5001 with the stored editable fields and code "P-999999" · 2. GET /api/v1/pty/customers/5001
Expected     : step 1 → 422 · code PTY-422-CODE-READ-ONLY · messageAr «رقم الطرف يُنشأ تلقائياً ولا يمكن تعديله.» · messageEn «The party code is assigned by the system and cannot be changed.» · step 2 → data.code P-000120
Test data    : customerId 5001, codes P-000120 / P-999999 (AC)
<!-- TC:TC-PTY-008:END -->

<!-- TC:TC-PTY-009:START traces=AC-PTY-027,REQ-PTY-022,API-PTY-008 -->
### TC-PTY-009 — Supplier payment terms above 365 refused
Derived from : AC-PTY-027  (REQ-PTY-022)
Exercises    : API-PTY-008 PUT /api/v1/pty/suppliers/{id}
Rule / code  : RULE-PTY-008 → PTY-422-PAYMENT-TERMS-RANGE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Supplier 7002; caller (Owner) holds PERM_PTY_SUPPLIER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/suppliers/7002 with the stored fields and paymentTermDays 400 · 2. GET /api/v1/pty/suppliers/7002
Expected     : step 1 → 422 · code PTY-422-PAYMENT-TERMS-RANGE · messageAr «مدة السداد يجب أن تكون بين 0 و365 يوماً.» · messageEn «Payment terms must be between 0 and 365 days.» · step 2 → paymentTermDays unchanged
Test data    : supplierId 7002, paymentTermDays 400 (AC)
<!-- TC:TC-PTY-009:END -->

<!-- TC:TC-PTY-010:START traces=AC-PTY-029,REQ-PTY-024,API-PTY-008,API-PTY-002 -->
### TC-PTY-010 — Mobile changed from the Supplier is the Customer's mobile
Derived from : AC-PTY-029  (REQ-PTY-024)
Exercises    : API-PTY-008 PUT /api/v1/pty/suppliers/{id} · API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : — 
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000130 holds a Customer `<customerId>` and a Supplier `<supplierId>`; caller (Owner) holds PERM_PTY_SUPPLIER_UPDATE and PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. PUT /api/v1/pty/suppliers/{supplierId} with the stored fields and mobile "+966500001111" · 2. GET /api/v1/pty/customers/{customerId}
Expected     : step 1 → 200 · step 2 → 200, data.mobile "+966500001111" (one Party identity, held once)
Test data    : Party P-000130, mobile "+966500001111" (AC); role identifiers are placeholders bound to the rows of P-000130
<!-- TC:TC-PTY-010:END -->

<!-- TC:TC-PTY-011:START traces=AC-PTY-032,REQ-PTY-027,API-PTY-006 -->
### TC-PTY-011 — Second Supplier on the same Party refused
Derived from : AC-PTY-032  (REQ-PTY-027)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers (partyId)
Rule / code  : RULE-PTY-009 → PTY-409-ROLE-HELD
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Party P-000130 already holds a Supplier; caller holds PERM_PTY_SUPPLIER_CREATE; the Supplier count is recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with partyId = the identifier of Party P-000130
Expected     : 409 · code PTY-409-ROLE-HELD · messageAr «هذا الطرف له صفة مورّد بالفعل.» · messageEn «This party already holds the Supplier role.» · Supplier count unchanged
Test data    : Party P-000130 (AC)
<!-- TC:TC-PTY-011:END -->

<!-- TC:TC-PTY-012:START traces=AC-PTY-033,REQ-PTY-028,API-PTY-004 -->
### TC-PTY-012 — Party inactive once its last active role is deactivated
Derived from : AC-PTY-033  (REQ-PTY-028)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-006 → — (confirmation)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000124 holds an active Customer `<customerId>` and an inactive Supplier; caller (Owner) holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/{customerId} · 2. read the stored active flag of Party P-000124 (DBF-PTY-008)
Expected     : step 1 → 200 · step 2 → false
Test data    : Party P-000124 (AC)
<!-- TC:TC-PTY-012:END -->

<!-- TC:TC-PTY-013:START traces=AC-PTY-034,REQ-PTY-029,API-PTY-005 -->
### TC-PTY-013 — Party active again when its Customer is reactivated
Derived from : AC-PTY-034  (REQ-PTY-029)
Exercises    : API-PTY-005 PATCH /api/v1/pty/customers/{id}/activation
Rule / code  : — 
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000124 with active flag false and an inactive Customer `<customerId>`; no other active Party holds its email; caller (Owner) holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/customers/{customerId}/activation with {"isActiveFl": true} · 2. read the stored active flag of Party P-000124 (DBF-PTY-008)
Expected     : step 1 → 200, data.isActiveFl true · step 2 → true
Test data    : Party P-000124 (AC); body per ADR-PTY-031
<!-- TC:TC-PTY-013:END -->

<!-- TC:TC-PTY-014:START traces=AC-PTY-035,REQ-PTY-029,API-PTY-005 -->
### TC-PTY-014 — Reactivation refused while another active Party holds the email
Derived from : AC-PTY-035  (REQ-PTY-029)
Exercises    : API-PTY-005 PATCH /api/v1/pty/customers/{id}/activation
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: inactive Party P-000124 holds email "a@example.com" and an inactive Customer `<customerId>`; active Party P-000140 holds email "a@example.com"; caller (Owner) holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/customers/{customerId}/activation with {"isActiveFl": true} · 2. GET /api/v1/pty/customers/{customerId}
Expected     : step 1 → 409 · code PTY-409-EMAIL-TAKEN · messageAr «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000140). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · messageEn «This email is already registered to another active party (P-000140). Open that party instead of creating a new one.» · step 2 → isActiveFl false; Party P-000124 stays inactive
Test data    : Parties P-000124, P-000140, email "a@example.com" (AC)
<!-- TC:TC-PTY-014:END -->

<!-- TC:TC-PTY-015:START traces=AC-PTY-040,REQ-PTY-034,API-PTY-011 -->
### TC-PTY-015 — First active Address of a type becomes the default
Derived from : AC-PTY-040  (REQ-PTY-034)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-017 → — (system-maintained default)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000120 holds a Customer and has no active BILLING Address; caller (Owner) holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. POST /api/v1/pty/parties/{partyId of P-000120}/addresses with addressTypeCode BILLING and line1 `<line1>`
Expected     : 201 · data.addressTypeCode BILLING · data.isDefaultFl true
Test data    : Party P-000120, type BILLING (AC; seeded value); `<line1>` placeholder
<!-- TC:TC-PTY-015:END -->

<!-- TC:TC-PTY-016:START traces=AC-PTY-042,REQ-PTY-036,API-PTY-014,API-PTY-029 -->
### TC-PTY-016 — Default reassigned to the most recently updated remaining Address
Derived from : AC-PTY-042  (REQ-PTY-036)
Exercises    : API-PTY-014 DELETE /api/v1/pty/parties/{partyId}/addresses/{id} · API-PTY-029 GET /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-017 → — (system-maintained default)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Party P-000120 with active DELIVERY Addresses "1" (default), "2" last updated 2026-10-01 and "3" last updated 2026-10-20 (updatedAt fixed by the test clock at each Address's last update); caller (Owner) holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/parties/{partyId}/addresses/{id of "1"} · 2. GET /api/v1/pty/parties/{partyId}/addresses
Expected     : step 1 → 200 · step 2 → Address "3" isDefaultFl true, Address "2" isDefaultFl false, Address "1" absent
Test data    : Address codes "1", "2", "3", dates 2026-10-01, 2026-10-20 (AC)
<!-- TC:TC-PTY-016:END -->

<!-- TC:TC-PTY-017:START traces=AC-PTY-043,REQ-PTY-037,API-PTY-011 -->
### TC-PTY-017 — Address without a first line refused
Derived from : AC-PTY-043  (REQ-PTY-037)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-015 → PTY-422-ADDRESS-LINE-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a Party `<partyId>` holding a Customer; caller holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. POST /api/v1/pty/parties/{partyId}/addresses with addressTypeCode BILLING and line1 ""
Expected     : 422 · code PTY-422-ADDRESS-LINE-REQUIRED · messageAr «أدخل السطر الأول من العنوان.» · messageEn «Enter the first address line.» · nothing saved
Test data    : type BILLING, line1 empty (AC); `<partyId>` placeholder
<!-- TC:TC-PTY-017:END -->

<!-- TC:TC-PTY-018:START traces=AC-PTY-044,REQ-PTY-038,API-PTY-011 -->
### TC-PTY-018 — Address type outside the active lookup values refused
Derived from : AC-PTY-044  (REQ-PTY-038)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-016 → PTY-422-ADDRESS-TYPE-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: lookup ADDRESS_TYPE holds the seeded active values BILLING and DELIVERY only; a Party `<partyId>` holding a Customer; caller holds PERM_PTY_CUSTOMER_UPDATE
Host data    : ADDRESS_TYPE OFFICE — absent: never created, so the ADDRESS_TYPE type found by POST /api/v1/mdl/lookup-types/search has no OFFICE value, and this case makes no POST /api/v1/mdl/lookup-types/{id}/values call (RULE-PTY-016 refuses absent and inactive values alike)
Steps        : 1. POST /api/v1/pty/parties/{partyId}/addresses with addressTypeCode "OFFICE" and line1 `<line1>`
Expected     : 422 · code PTY-422-ADDRESS-TYPE-INVALID · messageAr «اختر نوع عنوان من القائمة.» · messageEn «Choose an address type from the list.» · nothing saved
Test data    : addressTypeCode "OFFICE" (AC); `<partyId>`, `<line1>` placeholders
<!-- TC:TC-PTY-018:END -->

<!-- TC:TC-PTY-019:START traces=AC-PTY-045,REQ-PTY-039,API-PTY-003,API-PTY-002 -->
### TC-PTY-019 — Customer holds exactly one customer group
Derived from : AC-PTY-045  (REQ-PTY-039)
Exercises    : API-PTY-003 PUT /api/v1/pty/customers/{id} · API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-018 → — (valid value)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: Customer 5001 with customerGroupCode RETAIL; Owner holds PERM_PTY_CUSTOMER_UPDATE; the Pricing principal holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. PUT /api/v1/pty/customers/5001 with the stored fields and customerGroupCode WHOLESALE · 2. GET /api/v1/pty/customers/5001 as the Pricing principal
Expected     : step 1 → 200, data.customerGroupCode WHOLESALE · step 2 → data.customerGroupCode WHOLESALE (a single value)
Test data    : customerId 5001, RETAIL → WHOLESALE (AC; seeded values)
<!-- TC:TC-PTY-019:END -->

<!-- TC:TC-PTY-020:START traces=AC-PTY-046,REQ-PTY-040,API-PTY-003 -->
### TC-PTY-020 — Inactive customer group refused
Derived from : AC-PTY-046  (REQ-PTY-040)
Exercises    : API-PTY-003 PUT /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-018 → PTY-422-CUSTOMER-GROUP-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: lookup CUSTOMER_GROUP holds RETAIL and WHOLESALE active (seeded) and VIP inactive; an existing Customer `<customerId>`; caller holds PERM_PTY_CUSTOMER_UPDATE
Host data    : CUSTOMER_GROUP VIP — created active by the given mdl call POST /api/v1/mdl/lookup-types/{id}/values ({id} = the CUSTOMER_GROUP type found by POST /api/v1/mdl/lookup-types/search), then made inactive by DELETE /api/v1/mdl/lookup-values/{id} ({id} = the VIP value) — N4
Steps        : 1. PUT /api/v1/pty/customers/{customerId} with the stored fields and customerGroupCode "VIP" · 2. GET /api/v1/pty/customers/{customerId}
Expected     : step 1 → 422 · code PTY-422-CUSTOMER-GROUP-INVALID · messageAr «اختر مجموعة عملاء من القائمة.» · messageEn «Choose a customer group from the list.» · step 2 → customerGroupCode unchanged
Test data    : customerGroupCode "VIP" (AC); `<customerId>` placeholder
<!-- TC:TC-PTY-020:END -->

<!-- TC:TC-PTY-021:START traces=AC-PTY-049,REQ-PTY-041,API-PTY-027 -->
### TC-PTY-021 — Global search term of one character returns no Customer
Derived from : AC-PTY-049  (REQ-PTY-041)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — (term length below 2 → empty list, not an error)
Package      : SVC-API-SEARCH
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: 25 active Customers; caller (Seller/Cashier) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=n
Expected     : 200 · data is an empty list (0 customer results)
Test data    : 25 Customers, term "n" (AC); Customer names are placeholders
<!-- TC:TC-PTY-021:END -->

<!-- TC:TC-PTY-022:START traces=AC-PTY-052,REQ-PTY-044,API-PTY-033 -->
### TC-PTY-022 — Walk-in Customer created once at tenant provisioning
Derived from : AC-PTY-052  (REQ-PTY-044)
Exercises    : API-PTY-033 GET /api/v1/pty/customers/walk-in (read back) — provisioning through the given `TenantProvisioningContributor` (PTY contributor → the same ensure method)
Rule / code  : RULE-PTY-014 → — (idempotent ensure)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class VALID · language ALL
Preconditions: a new tenant whose provisioning has not run
Host data    : none
Steps        : 1. provision the tenant through the given tenant provisioning · 2. count the tenant's stored Customers with isWalkInFl true (DBF-PTY-018) · 3. GET /api/v1/pty/customers/walk-in as a holder of PERM_PTY_CUSTOMER_VIEW
Expected     : step 2 → exactly 1 · step 3 → 200, data.isWalkInFl true, nameAr «عميل نقدي», nameEn "Walk-in customer", customerGroupCode RETAIL, isActiveFl true
Test data    : names, group (AC; SRS D-08)
<!-- TC:TC-PTY-022:END -->

<!-- TC:TC-PTY-023:START traces=AC-PTY-055,REQ-PTY-047,API-PTY-004,API-PTY-002 -->
### TC-PTY-023 — Walk-in Customer cannot be deactivated
Derived from : AC-PTY-055  (REQ-PTY-047)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-010 → PTY-422-WALK-IN-DEACTIVATE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: the walk-in Customer 5000; caller (Owner) holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/5000 · 2. GET /api/v1/pty/customers/5000
Expected     : step 1 → 422 · code PTY-422-WALK-IN-DEACTIVATE · messageAr «العميل النقدي لازم لنقطة البيع ولا يمكن إيقافه.» · messageEn «The walk-in customer is required by the point of sale and cannot be deactivated.» · step 2 → isActiveFl true; no party-role-deactivated event published
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-023:END -->

<!-- TC:TC-PTY-024:START traces=AC-PTY-056,REQ-PTY-048,API-PTY-017 -->
### TC-PTY-024 — Walk-in Customer cannot receive a credit limit
Derived from : AC-PTY-056  (REQ-PTY-048)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-011 → PTY-422-WALK-IN-CREDIT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; the walk-in Customer 5000 (credit fields empty); caller (Owner) holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5000 with creditLimit 1000 and paymentTermDays null
Expected     : 422 · code PTY-422-WALK-IN-CREDIT · messageAr «لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان.» · messageEn «The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit.» · nothing saved; no audit event recorded
Test data    : customerId 5000, creditLimit 1000 (AC)
<!-- TC:TC-PTY-024:END -->

<!-- TC:TC-PTY-025:START traces=AC-PTY-057,REQ-PTY-049,API-PTY-006 -->
### TC-PTY-025 — Supplier role refused on the walk-in Party
Derived from : AC-PTY-057  (REQ-PTY-049)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers (partyId)
Rule / code  : RULE-PTY-012 → PTY-422-WALK-IN-SUPPLIER
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: the walk-in Customer 5000; caller (Owner) holds PERM_PTY_SUPPLIER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with partyId = the partyFk of Customer 5000 ("Make supplier" / «إضافة كمورّد»)
Expected     : 422 · code PTY-422-WALK-IN-SUPPLIER · messageAr «لا يمكن إضافة صفة المورّد إلى العميل النقدي.» · messageEn «The walk-in customer cannot also be a supplier.» · no Supplier saved
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-025:END -->

<!-- TC:TC-PTY-026:START traces=AC-PTY-061,REQ-PTY-053,API-PTY-034 -->
### TC-PTY-026 — Guest details without email or mobile refused
Derived from : AC-PTY-061  (REQ-PTY-053)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : RULE-PTY-019 → PTY-422-GUEST-CONTACT-REQUIRED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: caller (storefront principal) holds PERM_PTY_CUSTOMER_CREATE; the tenant's Party count is recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with nameEn "Lina Fares", email null, mobile null, deliveryAddress {line1 `<line1>`}
Expected     : 422 · code PTY-422-GUEST-CONTACT-REQUIRED · messageAr «أدخل البريد الإلكتروني أو رقم الجوال لإتمام الطلب.» · messageEn «Enter an email or a mobile number to complete the order.» · Party count unchanged; no invitation requested
Test data    : nameEn "Lina Fares" (AC); `<line1>` placeholder (the schema requires a delivery address)
<!-- TC:TC-PTY-026:END -->

<!-- TC:TC-PTY-027:START traces=AC-PTY-062,REQ-PTY-054 -->
### TC-PTY-027 — CUSTOMER-realm user already linked is not linked twice
Derived from : AC-PTY-062  (REQ-PTY-054)
Exercises    : outbox subscriber `PtyCustomerInvitationRedeemedSubscriber` (no API — backend plan CORE, SVC-API-INT)
Rule / code  : RULE-PTY-020 → subscriber outcome REFUSED, recorded through AuditApi (no catalog code)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Customer 5001 holds secUserId 88001; Customer 5011 has a pending CUSTOMER-realm invitation
Host data    : none
Steps        : 1. deliver to the subscriber a `sec.CustomerInvitationRedeemed` outbox message (subject module PTY, subject type CUSTOMER, subject reference 5011, redeemer 88001) · 2. read the stored secUserId (DBF-PTY-017) of 5011 and of 5001 · 3. deliver the same message again (same OutboxMessage id)
Expected     : step 1 → outcome REFUSED recorded through AuditApi with messageAr «حساب العميل هذا مرتبط بعميل آخر.» · messageEn «This customer account is already linked to another customer.»; the delivery completes · step 2 → 5011 empty, 5001 88001 · step 3 → nothing changes
Test data    : customers 5001, 5011, user 88001 (AC)
<!-- TC:TC-PTY-027:END -->

<!-- TC:TC-PTY-028:START traces=AC-PTY-070,REQ-PTY-062,API-PTY-034 -->
### TC-PTY-028 — Guest matched to an inactive Customer refused without revealing it
Derived from : AC-PTY-070  (REQ-PTY-062)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : RULE-PTY-021 → PTY-422-GUEST-NOT-COMPLETED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: active Party P-000130 with email "kareem@example.com", an active Supplier and an inactive Customer; caller (storefront principal) holds PERM_PTY_CUSTOMER_CREATE; Party and Customer counts recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "kareem@example.com", deliveryAddress {line1 `<line1>`}
Expected     : 422 · code PTY-422-GUEST-NOT-COMPLETED · messageAr «تعذّر إتمام الطلب بهذه البيانات. يُرجى التواصل مع البائع.» · messageEn «The order cannot be completed with these details. Please contact the seller.» · no Customer saved or attached (counts unchanged, P-000130's Customer still inactive); no invitation requested
Test data    : Party P-000130, email "kareem@example.com" (AC); `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-028:END -->

<!-- TC:TC-PTY-029:START traces=AC-PTY-074,REQ-PTY-066,API-PTY-017 -->
### TC-PTY-029 — Negative credit limit refused
Derived from : AC-PTY-074  (REQ-PTY-066)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-022 → PTY-422-CREDIT-LIMIT-NEGATIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001; caller (Owner) holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit -100 and the stored paymentTermDays
Expected     : 422 · code PTY-422-CREDIT-LIMIT-NEGATIVE · messageAr «حد الائتمان يجب أن يكون صفراً أو أكثر.» · messageEn «The credit limit must be zero or more.» · nothing saved
Test data    : customerId 5001, creditLimit -100 (AC)
<!-- TC:TC-PTY-029:END -->

<!-- TC:TC-PTY-030:START traces=AC-PTY-075,REQ-PTY-067,API-PTY-017 -->
### TC-PTY-030 — Customer payment terms of 366 days refused
Derived from : AC-PTY-075  (REQ-PTY-067)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-008 → PTY-422-PAYMENT-TERMS-RANGE
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; Customer 5001; caller (Owner) holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with the stored creditLimit and paymentTermDays 366
Expected     : 422 · code PTY-422-PAYMENT-TERMS-RANGE · messageAr «مدة السداد يجب أن تكون بين 0 و365 يوماً.» · messageEn «Payment terms must be between 0 and 365 days.» · nothing saved
Test data    : customerId 5001, paymentTermDays 366 — one above the RULE-PTY-008 limit 365 (AC)
<!-- TC:TC-PTY-030:END -->

<!-- TC:TC-PTY-031:START traces=AC-PTY-079,REQ-PTY-071,API-PTY-002 -->
### TC-PTY-031 — Customer read carries no credit fields while the flag is off
Derived from : AC-PTY-079  (REQ-PTY-071)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5001
Expected     : 200 · data has no creditLimit member and no paymentTermDays member (absent from the JSON, not null)
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-031:END -->

<!-- TC:TC-PTY-032:START traces=AC-PTY-080,REQ-PTY-072,API-PTY-017 -->
### TC-PTY-032 — Credit limit refused while the flag is off
Derived from : AC-PTY-080  (REQ-PTY-072)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; caller holds PERM_PTY_CUSTOMER_CREDIT_UPDATE; stored creditLimit (DBF-PTY-015) recorded
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit 1000 · 2. read the stored creditLimit of 5001
Expected     : step 1 → 422 · code PTY-422-CREDIT-NOT-ENABLED · messageAr «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · messageEn «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» · step 2 → unchanged
Test data    : customerId 5001, creditLimit 1000 (AC); the credit fields are written only through API-PTY-017 (API-PTY-003 excludes them)
<!-- TC:TC-PTY-032:END -->

<!-- TC:TC-PTY-033:START traces=AC-PTY-081,REQ-PTY-072,API-PTY-019 -->
### TC-PTY-033 — Credit override request refused while the flag is off
Derived from : AC-PTY-081  (REQ-PTY-072)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE; the request count is recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 500, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-CREDIT-NOT-ENABLED · messageAr «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · messageEn «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» · no request saved; no number drawn; no notification sent
Test data    : customerId 5001, saleAmount 500 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-033:END -->

<!-- TC:TC-PTY-034:START traces=AC-PTY-082,REQ-PTY-073,API-PTY-002,API-PTY-016 -->
### TC-PTY-034 — Credit data served from the next request after the flag is turned on
Derived from : AC-PTY-082  (REQ-PTY-073)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id} · API-PTY-016 GET /api/v1/pty/customer-credits/{customerId}
Rule / code  : — 
Package      : CORE
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; Owner holds every PTY permission
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5001 · 2. turn `b2bCredit` on through the given FeatureFlags (`SettingsApi`) — no PTY call · 3. GET /api/v1/pty/customers/5001 · 4. GET /api/v1/pty/customer-credits/5001
Expected     : step 1 → no credit members · step 3 → data carries the creditLimit and paymentTermDays members · step 4 → 200 (not PTY-422-CREDIT-NOT-ENABLED); no PTY setup call made between steps 2 and 3. The screen part of the AC is TC-PTY-150
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-034:END -->

<!-- TC:TC-PTY-035:START traces=AC-PTY-083,REQ-PTY-074,API-PTY-035 -->
### TC-PTY-035 — Credit check answers NOT_APPLICABLE while the flag is off
Derived from : AC-PTY-083  (REQ-PTY-074)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; Customer 5001; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 999999
Expected     : 200 · data.resultCode NOT_APPLICABLE · data.approvalRequired false
Test data    : customerId 5001, saleAmount 999999 (AC)
<!-- TC:TC-PTY-035:END -->

<!-- TC:TC-PTY-036:START traces=AC-PTY-085,REQ-PTY-076,API-PTY-037 -->
### TC-PTY-036 — Switch-off answer refuses while Customers carry credit
Derived from : AC-PTY-085  (REQ-PTY-076)
Exercises    : API-PTY-037 GET /api/v1/pty/flag-dependencies/b2bCredit
Rule / code  : RULE-PTY-024 → answer message (no catalog row — the refusal is the flag owner's)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class VALID · language ALL
Preconditions: `b2bCredit` on; exactly 4 Customers carry a creditLimit or a paymentTermDays; caller (the flag owner's principal) holds PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/flag-dependencies/b2bCredit · 2. read `b2bCredit` through the given FeatureFlags
Expected     : step 1 → 200 · data.dependentCustomerCount 4 · data.switchOffAllowed false · data.messageAr «لا يمكن إيقاف الائتمان: لدى 4 من العملاء حد ائتمان أو مدة سداد. امسحها من شاشة ائتمان العملاء ثم أعد المحاولة.» · data.messageEn «Credit cannot be turned off: 4 customers still have a credit limit or payment terms. Clear them on the Customer credit screen, then try again.» · data.nextActionCode SHOW_CUSTOMERS_WITH_CREDIT · data.nextActionLabelAr «عرض العملاء ذوي الائتمان» · data.nextActionLabelEn "Show customers with credit" · step 2 → still on (PTY changes no flag)
Test data    : 4 Customers carrying credit (AC); their identities and values are placeholders
<!-- TC:TC-PTY-036:END -->

<!-- TC:TC-PTY-037:START traces=AC-PTY-092,REQ-PTY-081,API-PTY-035 -->
### TC-PTY-037 — Projected exposure equal to the limit is within the limit
Derived from : AC-PTY-092  (REQ-PTY-081)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; one registered test exposure contributor reports 45000 for 5001; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 5000
Expected     : 200 · data.projectedExposure 50000 · data.resultCode WITHIN_LIMIT
Test data    : creditLimit 50000, exposure 45000, saleAmount 5000 (AC)
<!-- TC:TC-PTY-037:END -->

<!-- TC:TC-PTY-038:START traces=AC-PTY-093,REQ-PTY-082,API-PTY-035 -->
### TC-PTY-038 — One unit above the limit is OVER_LIMIT with the overrun action
Derived from : AC-PTY-093  (REQ-PTY-082)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; a test contributor reports 45000; the tenant's credit-overrun action is BLOCK_WITH_OVERRIDE; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 5001
Expected     : 200 · data.projectedExposure 50001 · data.resultCode OVER_LIMIT · data.overrunActionCode BLOCK_WITH_OVERRIDE
Test data    : creditLimit 50000, exposure 45000, saleAmount 5001 (AC)
<!-- TC:TC-PTY-038:END -->

<!-- TC:TC-PTY-039:START traces=AC-PTY-095,REQ-PTY-084,API-PTY-035 -->
### TC-PTY-039 — Contributor silent beyond 3 seconds gives EXPOSURE_UNAVAILABLE
Derived from : AC-PTY-095  (REQ-PTY-084)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; Customer 5001; two registered test contributors — one answers at once, the other answers only after more than 3 seconds; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 100
Expected     : 200 · data.resultCode EXPOSURE_UNAVAILABLE · data.openExposure null · data.overrunActionCode present and equal to the tenant's credit-overrun action (BLOCK_WITH_OVERRIDE while none is saved — REQ-PTY-087)
Test data    : saleAmount 100, 3-second budget (AC)
<!-- TC:TC-PTY-039:END -->

<!-- TC:TC-PTY-040:START traces=AC-PTY-103,REQ-PTY-091,API-PTY-019 -->
### TC-PTY-040 — Credit override request with sale amount 0 refused
Derived from : AC-PTY-103  (REQ-PTY-091)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-032 → PTY-422-SALE-AMOUNT-NOT-POSITIVE
Package      : SVC-API-CRUD
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `b2bCredit` on; a named (not walk-in) Customer `<customerId>`; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId `<customerId>`, saleAmount 0, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-SALE-AMOUNT-NOT-POSITIVE · messageAr «مبلغ البيع يجب أن يكون أكبر من صفر.» · messageEn «The sale amount must be greater than zero.» · nothing saved
Test data    : saleAmount 0 — the RULE-PTY-032 limit (AC); `<customerId>`, `<sale reference>` placeholders
<!-- TC:TC-PTY-040:END -->

<!-- TC:TC-PTY-041:START traces=AC-PTY-104,REQ-PTY-092,API-PTY-019,API-PTY-021,API-PTY-020 -->
### TC-PTY-041 — Request number from the series, read-only afterwards
Derived from : AC-PTY-104  (REQ-PTY-092)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides · API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval · API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-031 → PTY-422-DOC-NO-READ-ONLY
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; the given series PTY_CREDIT_OVERRIDE returns CO-000031 next; test clock 2026-11-02 10:15; a Customer `<customerId>` and sale amount `<saleAmount>` whose server-side credit check is not WITHIN_LIMIT; requester holds PERM_PTY_CREDIT_OVERRIDE_CREATE; an Owner holds PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides at 2026-11-02 10:15 · 2. as the Owner, PATCH …/{id}/approval with body {"docNo": "CO-999999"} · 3. GET /api/v1/pty/credit-overrides/{id}
Expected     : step 1 → 201, data.docNo CO-000031, data.docDate 2026-11-02 10:15 · step 2 → 422 · code PTY-422-DOC-NO-READ-ONLY · messageAr «رقم الطلب يُنشأ تلقائياً ولا يمكن تعديله.» · messageEn «The request number is assigned by the system and cannot be changed.» · step 3 → docNo CO-000031, statusCode PENDING (N6)
Test data    : CO-000031, CO-999999, 2026-11-02 10:15 (AC); `<customerId>`, `<saleAmount>` placeholders
<!-- TC:TC-PTY-041:END -->

<!-- TC:TC-PTY-042:START traces=AC-PTY-106,REQ-PTY-094,API-PTY-021 -->
### TC-PTY-042 — Approve a PENDING request
Derived from : AC-PTY-106  (REQ-PTY-094)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval
Rule / code  : RULE-PTY-026 → — (transition allowed)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: PENDING request CO-000031; an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE; test clock 2026-11-02 10:17
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000031}/approval with no body
Expected     : 200 · data.statusCode APPROVED · data.decidedBy the Owner's sec user identifier · data.decidedAt 2026-11-02 10:17
Test data    : CO-000031, 2026-11-02 10:17 (AC)
<!-- TC:TC-PTY-042:END -->

<!-- TC:TC-PTY-043:START traces=AC-PTY-107,REQ-PTY-095,API-PTY-022 -->
### TC-PTY-043 — Reject a PENDING request with a reason
Derived from : AC-PTY-107  (REQ-PTY-095)
Exercises    : API-PTY-022 PATCH /api/v1/pty/credit-overrides/{id}/rejection
Rule / code  : RULE-PTY-026, RULE-PTY-027 → — (transition allowed)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: PENDING request CO-000032; an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000032}/rejection with {"decisionReason": "Overdue invoices"}
Expected     : 200 · data.statusCode REJECTED · data.decisionReason "Overdue invoices" · data.decidedBy the Owner's sec user identifier · data.decidedAt set
Test data    : CO-000032, "Overdue invoices" (AC)
<!-- TC:TC-PTY-043:END -->

<!-- TC:TC-PTY-044:START traces=AC-PTY-108,REQ-PTY-096,API-PTY-023 -->
### TC-PTY-044 — Requester cancels a PENDING request
Derived from : AC-PTY-108  (REQ-PTY-096)
Exercises    : API-PTY-023 DELETE /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-026, RULE-PTY-029 → — (transition allowed)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: PENDING request CO-000033 created by Seller/Cashier S1; S1 holds PERM_PTY_CREDIT_OVERRIDE_DELETE
Host data    : none
Steps        : 1. as S1, DELETE /api/v1/pty/credit-overrides/{id of CO-000033}
Expected     : 200 · data.statusCode CANCELLED (the row is kept)
Test data    : CO-000033, S1 (AC)
<!-- TC:TC-PTY-044:END -->

<!-- TC:TC-PTY-045:START traces=AC-PTY-109,REQ-PTY-096,API-PTY-023 -->
### TC-PTY-045 — Only the requester can cancel
Derived from : AC-PTY-109  (REQ-PTY-096)
Exercises    : API-PTY-023 DELETE /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-029 → PTY-403-NOT-REQUESTER
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: PENDING request CO-000033 created by Seller/Cashier S1; Seller/Cashier S2 holds PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE and _DELETE (preset)
Host data    : none
Steps        : 1. as S2, DELETE /api/v1/pty/credit-overrides/{id of CO-000033} · 2. as an Owner, GET /api/v1/pty/credit-overrides/{id of CO-000033}
Expected     : step 1 → 403 · code PTY-403-NOT-REQUESTER · messageAr «لا يلغي هذا الطلب إلا من قدّمه.» · messageEn «Only the requester can cancel this request.» · step 2 → statusCode PENDING
Test data    : CO-000033, S1, S2 (AC)
<!-- TC:TC-PTY-045:END -->

<!-- TC:TC-PTY-046:START traces=AC-PTY-110,REQ-PTY-097,API-PTY-036 -->
### TC-PTY-046 — Approved request used by a sale within its amount
Derived from : AC-PTY-110  (REQ-PTY-097)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → — (use allowed)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` on; APPROVED request CO-000031 for Customer 5001 with saleAmount 2000 and sourceDocumentRef "POS-1-000455"; caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000031}/use with {"customerId": 5001, "saleAmount": 1950, "usedDocumentRef": "POS-1-000455"}
Expected     : 200 · data.statusCode USED · data.usedDocumentRef "POS-1-000455"
Test data    : CO-000031, customer 5001, amounts 2000 / 1950, "POS-1-000455" (AC)
<!-- TC:TC-PTY-046:END -->

<!-- TC:TC-PTY-047:START traces=AC-PTY-111,REQ-PTY-098,API-PTY-021 -->
### TC-PTY-047 — Approval of a rejected request refused
Derived from : AC-PTY-111  (REQ-PTY-098)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: request CO-000032 with statusCode REJECTED; an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE; CREDIT_OVERRIDE_STATUS labels as seeded (REJECTED = مرفوض / Rejected)
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000032}/approval · 2. GET /api/v1/pty/credit-overrides/{id of CO-000032}
Expected     : step 1 → 409 · code PTY-409-REQUEST-NOT-PENDING · messageAr «هذا الطلب لم يعد معلّقاً (الحالة: مرفوض).» · messageEn «This request is no longer pending (status: Rejected).» · step 2 → statusCode REJECTED
Test data    : CO-000032 (AC)
<!-- TC:TC-PTY-047:END -->

<!-- TC:TC-PTY-048:START traces=AC-PTY-112,REQ-PTY-099,API-PTY-036 -->
### TC-PTY-048 — Use for an amount above the approval refused
Derived from : AC-PTY-112  (REQ-PTY-099)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; APPROVED request CO-000031 for Customer 5001 with saleAmount 2000 and sourceDocumentRef "POS-1-000455"; caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000031}/use with customerId 5001, saleAmount 2500, usedDocumentRef "POS-1-000455" · 2. GET /api/v1/pty/credit-overrides/{id of CO-000031}
Expected     : step 1 → 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000031 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000031 does not cover this sale. Check the customer and the amount, or request a new approval.» · step 2 → statusCode APPROVED
Test data    : CO-000031, customer 5001, amounts 2000 / 2500 (AC); the sale reference equals the source sale so only the amount refuses
<!-- TC:TC-PTY-048:END -->

<!-- TC:TC-PTY-049:START traces=AC-PTY-113,REQ-PTY-099,API-PTY-036 -->
### TC-PTY-049 — A used approval cannot be used again
Derived from : AC-PTY-113  (REQ-PTY-099)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; request CO-000031 with statusCode USED (for Customer 5001, sourceDocumentRef "POS-1-000455"); caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000031}/use with customerId 5001, saleAmount 100, usedDocumentRef "POS-1-000455"
Expected     : 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000031 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000031 does not cover this sale. Check the customer and the amount, or request a new approval.» · statusCode stays USED
Test data    : CO-000031, customer 5001, amount 100 (AC); the sale reference equals the source sale so only the status refuses
<!-- TC:TC-PTY-049:END -->

<!-- TC:TC-PTY-050:START traces=AC-PTY-114,REQ-PTY-100,API-PTY-022 -->
### TC-PTY-050 — Rejection without a reason refused
Derived from : AC-PTY-114  (REQ-PTY-100)
Exercises    : API-PTY-022 PATCH /api/v1/pty/credit-overrides/{id}/rejection
Rule / code  : RULE-PTY-027 → PTY-422-REJECTION-REASON-REQUIRED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: PENDING request CO-000034; an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000034}/rejection with {"decisionReason": ""} · 2. GET /api/v1/pty/credit-overrides/{id of CO-000034}
Expected     : step 1 → 422 · code PTY-422-REJECTION-REASON-REQUIRED · messageAr «أدخل سبب الرفض.» · messageEn «Enter the reason for the rejection.» · step 2 → statusCode PENDING
Test data    : CO-000034, empty reason (AC)
<!-- TC:TC-PTY-050:END -->

<!-- TC:TC-PTY-051:START traces=AC-PTY-118,REQ-PTY-104,API-PTY-019 -->
### TC-PTY-051 — Request refused when the sale is within the limit
Derived from : AC-PTY-118  (REQ-PTY-104)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-025 → PTY-422-OVERRIDE-NOT-NEEDED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; a test contributor reports 40000; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 5000, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-OVERRIDE-NOT-NEEDED · messageAr «لا حاجة إلى موافقة: البيع ضمن حد ائتمان العميل.» · messageEn «No approval is needed: the sale is within the customer's credit limit.» · no request saved
Test data    : creditLimit 50000, exposure 40000, saleAmount 5000 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-051:END -->

<!-- TC:TC-PTY-052:START traces=AC-PTY-119,REQ-PTY-105,API-PTY-019 -->
### TC-PTY-052 — No credit override request for the walk-in Customer
Derived from : AC-PTY-119  (REQ-PTY-105)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-033 → PTY-422-WALK-IN-NO-CREDIT-SALE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; the walk-in Customer 5000; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5000, saleAmount 300, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-WALK-IN-NO-CREDIT-SALE · messageAr «لا يمكن البيع الآجل للعميل النقدي. سجّل العميل باسمه أولاً.» · messageEn «The walk-in customer cannot buy on credit. Register the customer by name first.» · no request saved
Test data    : customerId 5000, saleAmount 300 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-052:END -->

<!-- TC:TC-PTY-053:START traces=AC-PTY-123,REQ-PTY-109,API-PTY-017,API-PTY-016 -->
### TC-PTY-053 — Credit change not saved when the audit cannot record it
Derived from : AC-PTY-123  (REQ-PTY-109)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId} · API-PTY-016 GET /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit `<previous creditLimit>`; the given audit module rejects writes (test double for the `@Audited` listener and `AuditApi`); caller (Owner) holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit 70000 and the stored paymentTermDays · 2. GET /api/v1/pty/customer-credits/5001
Expected     : step 1 → 500 · code PTY-500-AUDIT-NOT-RECORDED · messageAr «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · messageEn «The change could not be recorded in the audit history, so it was not saved. Try again.» · step 2 → creditLimit `<previous creditLimit>`
Test data    : customerId 5001, creditLimit 70000 (AC); `<previous creditLimit>` placeholder
<!-- TC:TC-PTY-053:END -->

<!-- TC:TC-PTY-154:START traces=AC-PTY-124,REQ-PTY-110,API-PTY-035 -->
### TC-PTY-154 — Credit check refused for an inactive Customer
Derived from : AC-PTY-124  (REQ-PTY-110)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; Customer 5002 of Party P-000121 with isActiveFl false and creditLimit 10000; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5002 and saleAmount 100
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · no resultCode returned
Test data    : customerId 5002, P-000121, creditLimit 10000, saleAmount 100 (AC)
<!-- TC:TC-PTY-154:END -->

<!-- TC:TC-PTY-155:START traces=AC-PTY-125,REQ-PTY-110,API-PTY-019 -->
### TC-PTY-155 — Credit override request refused for an inactive Customer
Derived from : AC-PTY-125  (REQ-PTY-110)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; Customer 5002 of Party P-000121 with isActiveFl false; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE; request count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5002, saleAmount 500, sourceDocumentRef `<sale reference>`
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · request count unchanged; no number drawn from PTY_CREDIT_OVERRIDE
Test data    : customerId 5002, saleAmount 500 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-155:END -->

<!-- TC:TC-PTY-156:START traces=AC-PTY-126,REQ-PTY-110,API-PTY-011 -->
### TC-PTY-156 — Address refused on an inactive Party
Derived from : AC-PTY-126  (REQ-PTY-110)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Party P-000124 with isActiveFl false (its Customer and its Supplier inactive); the Owner holds PERM_PTY_CUSTOMER_UPDATE; Address count of P-000124 recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/parties/{identifier of P-000124}/addresses with addressTypeCode BILLING and line1 "5 Nile St"
Expected     : 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000124 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000124 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · Address count unchanged
Test data    : Party P-000124, BILLING, "5 Nile St" (AC)
<!-- TC:TC-PTY-156:END -->

<!-- TC:TC-PTY-157:START traces=AC-PTY-127,REQ-PTY-110,API-PTY-017,API-PTY-018 -->
### TC-PTY-157 — Credit save refused, credit clear allowed for an inactive Customer
Derived from : AC-PTY-127  (REQ-PTY-110)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId} · API-PTY-018 DELETE /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; Customer 5002 of Party P-000121 with isActiveFl false, creditLimit 10000, paymentTermDays 30; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5002 with creditLimit 20000 and paymentTermDays 30 · 2. DELETE /api/v1/pty/customer-credits/5002 · 3. GET /api/v1/pty/customer-credits/5002
Expected     : step 1 → 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · step 2 → 200 · step 3 → creditLimit null, paymentTermDays null
Test data    : customerId 5002, 10000, 30, 20000 (AC)
<!-- TC:TC-PTY-157:END -->

<!-- TC:TC-PTY-158:START traces=AC-PTY-128,REQ-PTY-111,API-PTY-004,API-PTY-020 -->
### TC-PTY-158 — Deactivating a Customer cancels its open requests
Derived from : AC-PTY-128  (REQ-PTY-111)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : — (system transition PENDING / APPROVED → CANCELLED)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` on; active Customer 5001 with requests CO-000040 PENDING, CO-000041 APPROVED and CO-000031 USED; the Owner holds PERM_PTY_CUSTOMER_DELETE and PERM_PTY_CREDIT_OVERRIDE_VIEW, _UPDATE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/5001 · 2. GET each of CO-000040, CO-000041, CO-000031
Expected     : step 1 → 200 · step 2 → CO-000040 CANCELLED, CO-000041 CANCELLED, CO-000031 USED; one audit row per cancelled request (entity type PTY_CREDIT_OVERRIDE_REQUEST)
Test data    : values of the AC
<!-- TC:TC-PTY-158:END -->

<!-- TC:TC-PTY-159:START traces=AC-PTY-129,REQ-PTY-111,API-PTY-036 -->
### TC-PTY-159 — An approval cancelled by the Customer's deactivation cannot be used after reactivation
Derived from : AC-PTY-129  (REQ-PTY-111)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING
Package      : SVC-API-INT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; request CO-000041 for Customer 5001 (saleAmount 2000, sourceDocumentRef "POS-1-000470") was APPROVED, Customer 5001 was then deactivated (CO-000041 CANCELLED) and reactivated; caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000041}/use with customerId 5001, saleAmount 1000, usedDocumentRef "POS-1-000470"
Expected     : 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000041 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000041 does not cover this sale. Check the customer and the amount, or request a new approval.» · CO-000041 stays CANCELLED
Test data    : CO-000041, customer 5001 (AC); amounts and reference chosen within the approval so only the status refuses
<!-- TC:TC-PTY-159:END -->

<!-- TC:TC-PTY-160:START traces=AC-PTY-130,REQ-PTY-112,API-PTY-021 -->
### TC-PTY-160 — Approval refused while the flag is off
Derived from : AC-PTY-130  (REQ-PTY-112)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval
Rule / code  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` off; PENDING request CO-000042; the Owner holds PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000042}/approval · 2. GET /api/v1/pty/credit-overrides/{id of CO-000042}
Expected     : step 1 → 422 · code PTY-422-CREDIT-NOT-ENABLED · messageAr «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · messageEn «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» · step 2 → statusCode PENDING, decidedBy and decidedAt empty
Test data    : CO-000042 (AC)
<!-- TC:TC-PTY-160:END -->

<!-- TC:TC-PTY-161:START traces=AC-PTY-131,REQ-PTY-112,API-PTY-023 -->
### TC-PTY-161 — Cancellation stays allowed while the flag is off
Derived from : AC-PTY-131  (REQ-PTY-112)
Exercises    : API-PTY-023 DELETE /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-023 → — (cancel is outside the flag rule)
Package      : DATA-DOM-TRANSACTIONAL
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; PENDING request CO-000043 created by Seller/Cashier S1; S1 holds PERM_PTY_CREDIT_OVERRIDE_DELETE
Host data    : none
Steps        : 1. as S1, DELETE /api/v1/pty/credit-overrides/{id of CO-000043}
Expected     : 200 · data.statusCode CANCELLED
Test data    : CO-000043, S1 (AC)
<!-- TC:TC-PTY-161:END -->

<!-- TC:TC-PTY-162:START traces=AC-PTY-132,REQ-PTY-113,API-PTY-019 -->
### TC-PTY-162 — Second open request for the same sale refused
Derived from : AC-PTY-132  (REQ-PTY-113)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-034 → PTY-409-REQUEST-OPEN-FOR-SALE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; Customer 5001 with creditLimit 50000 and a test contributor reporting 49000; PENDING request CO-000031 for Customer 5001 and sourceDocumentRef "POS-1-000455"; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE; request count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000455"
Expected     : 409 · code PTY-409-REQUEST-OPEN-FOR-SALE · messageAr «لهذا البيع طلب موافقة مفتوح بالفعل (CO-000031). تابع ذلك الطلب بدلاً من إنشاء طلب جديد.» · messageEn «This sale already has an open approval request (CO-000031). Follow that request instead of creating a new one.» · request count unchanged
Test data    : values of the AC
<!-- TC:TC-PTY-162:END -->

<!-- TC:TC-PTY-163:START traces=AC-PTY-133,REQ-PTY-099,API-PTY-036 -->
### TC-PTY-163 — An approval cannot be used on another sale
Derived from : AC-PTY-133  (REQ-PTY-099)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; APPROVED request CO-000031 for Customer 5001, saleAmount 2000, sourceDocumentRef "POS-1-000455"; caller holds PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. PATCH …/{id of CO-000031}/use with customerId 5001, saleAmount 1500, usedDocumentRef "POS-1-000460" · 2. GET /api/v1/pty/credit-overrides/{id of CO-000031}
Expected     : step 1 → 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000031 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000031 does not cover this sale. Check the customer and the amount, or request a new approval.» · step 2 → statusCode APPROVED, usedDocumentRef empty
Test data    : values of the AC
<!-- TC:TC-PTY-163:END -->

<!-- TC:TC-PTY-164:START traces=AC-PTY-134,REQ-PTY-109,API-PTY-021,API-PTY-020 -->
### TC-PTY-164 — Approval not saved when the audit cannot record it
Derived from : AC-PTY-134  (REQ-PTY-109)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval · API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; PENDING request CO-000031; the given audit module rejects writes (test double for `AuditApi.record` and the `@Audited` listener); the Owner holds PERM_PTY_CREDIT_OVERRIDE_UPDATE and _VIEW
Host data    : none
Steps        : 1. PATCH …/{id of CO-000031}/approval · 2. GET /api/v1/pty/credit-overrides/{id of CO-000031}
Expected     : step 1 → 500 · code PTY-500-AUDIT-NOT-RECORDED · messageAr «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · messageEn «The change could not be recorded in the audit history, so it was not saved. Try again.» · step 2 → statusCode PENDING, decidedBy and decidedAt empty
Test data    : CO-000031 (AC)
<!-- TC:TC-PTY-164:END -->

<!-- TC:TC-PTY-168:START traces=AC-PTY-111,REQ-PTY-098,API-PTY-021 -->
### TC-PTY-168 — Two concurrent approvals: exactly one succeeds
Derived from : AC-PTY-111  (REQ-PTY-098)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval (twice, in parallel)
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING (guard: QR-PTY-033 row lock + QR-PTY-034 compare-and-set — ADR-PTY-026)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; PENDING request CO-000050; two Owner sessions O1, O2 holding PERM_PTY_CREDIT_OVERRIDE_UPDATE; CREDIT_OVERRIDE_STATUS labels as seeded
Host data    : none
Steps        : 1. two threads, released together by a barrier, send PATCH …/{id of CO-000050}/approval as O1 and as O2 · 2. GET the request
Expected     : exactly one call → 200 (statusCode APPROVED); the other → 409 · code PTY-409-REQUEST-NOT-PENDING · messageAr «هذا الطلب لم يعد معلّقاً (الحالة: موافَق عليه).» · messageEn «This request is no longer pending (status: Approved).» · step 2 → APPROVED, decidedBy = the winner; exactly one APPROVE business audit action
Test data    : CO-000050 (test-local request number)
<!-- TC:TC-PTY-168:END -->

<!-- TC:TC-PTY-169:START traces=AC-PTY-108,REQ-PTY-098,API-PTY-021,API-PTY-023 -->
### TC-PTY-169 — Approval racing a cancellation: exactly one succeeds
Derived from : AC-PTY-108  (REQ-PTY-098)
Exercises    : API-PTY-021 PATCH …/approval · API-PTY-023 DELETE /api/v1/pty/credit-overrides/{id} (in parallel)
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING (guard: row lock + compare-and-set — ADR-PTY-026)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; PENDING request CO-000051 created by Seller/Cashier S1 (PERM_PTY_CREDIT_OVERRIDE_DELETE); an Owner holding PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. two threads, released together by a barrier: the Owner approves CO-000051, S1 cancels it · 2. GET the request
Expected     : exactly one call → 200; the other → 409 · code PTY-409-REQUEST-NOT-PENDING with the status label of the winner's state (موافَق عليه / Approved or ملغى / Cancelled) · step 2 → the winner's status (APPROVED or CANCELLED), never both decided
Test data    : CO-000051 (test-local)
<!-- TC:TC-PTY-169:END -->

<!-- TC:TC-PTY-170:START traces=AC-PTY-113,REQ-PTY-099,API-PTY-036 -->
### TC-PTY-170 — Two sales using one approval at once: exactly one succeeds
Derived from : AC-PTY-113  (REQ-PTY-099)
Exercises    : API-PTY-036 PATCH /api/v1/pty/credit-overrides/{id}/use (twice, in parallel)
Rule / code  : RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING (guard: row lock + `WHERE STATUS_CODE = 'APPROVED'`)
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; APPROVED request CO-000052 for Customer 5001, saleAmount 2000, sourceDocumentRef "POS-1-000480"; two Sales & POS sessions holding PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. two threads, released together by a barrier, send PATCH …/{id of CO-000052}/use with customerId 5001, saleAmount 1500, usedDocumentRef "POS-1-000480" · 2. GET the request
Expected     : exactly one call → 200 (USED); the other → 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000052 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000052 does not cover this sale. Check the customer and the amount, or request a new approval.» · step 2 → USED once
Test data    : CO-000052 (test-local)
<!-- TC:TC-PTY-170:END -->

<!-- TC:TC-PTY-171:START traces=AC-PTY-054,REQ-PTY-046,API-PTY-033 -->
### TC-PTY-171 — Two first walk-in requests at once create one walk-in Customer
Derived from : AC-PTY-054  (REQ-PTY-046)
Exercises    : API-PTY-033 GET /api/v1/pty/customers/walk-in (twice, in parallel)
Rule / code  : RULE-PTY-014 → — (guard: UQ_PTY_CUSTOMER_IS_WALK_IN_FL (TENANT_ID) + REQUIRES_NEW re-read — ADR-PTY-026)
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: a tenant with no walk-in Customer; two sessions holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. two threads, released together by a barrier, send GET /api/v1/pty/customers/walk-in · 2. count the tenant's stored Customers with isWalkInFl true and the Parties named «عميل نقدي»
Expected     : both calls → 200 with the same customerPk · step 2 → exactly 1 walk-in Customer and exactly 1 walk-in Party (the loser's REQUIRES_NEW Party rolled back)
Test data    : none beyond the AC
<!-- TC:TC-PTY-171:END -->

<!-- TC:TC-PTY-172:START traces=AC-PTY-013,REQ-PTY-010,API-PTY-001 -->
### TC-PTY-172 — Two registrations of one email at once: exactly one succeeds
Derived from : AC-PTY-013  (REQ-PTY-010)
Exercises    : API-PTY-001 POST /api/v1/pty/customers (twice, in parallel)
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN (guard: UQ_PTY_PARTY_EMAIL (TENANT_ID, LOWER(BTRIM(EMAIL))) — ADR-PTY-026)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: no active Party holds "race@example.com"; two sessions holding PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. two threads, released together by a barrier, send POST /api/v1/pty/customers with nameEn "Race A" / "Race B" and email "race@example.com"
Expected     : exactly one call → 201; the other → 409 · code PTY-409-EMAIL-TAKEN whose messageAr / messageEn name the winner's Party code · Party count +1
Test data    : "race@example.com" (test-local)
<!-- TC:TC-PTY-172:END -->

<!-- TC:TC-PTY-173:START traces=AC-PTY-033,REQ-PTY-028,API-PTY-004,API-PTY-009 -->
### TC-PTY-173 — Customer and Supplier of one Party deactivated at once leave the Party inactive
Derived from : AC-PTY-033  (REQ-PTY-028)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-009 DELETE /api/v1/pty/suppliers/{id} (in parallel)
Rule / code  : — (guard: the Party row lock QR-PTY-001 serialises the "last active role" count)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Party P-000170 holds an active Customer `<customerId>` and an active Supplier `<supplierId>`; the Owner holds PERM_PTY_CUSTOMER_DELETE and PERM_PTY_SUPPLIER_DELETE
Host data    : none
Steps        : 1. two threads, released together by a barrier: DELETE the Customer and DELETE the Supplier · 2. read the stored active flag of P-000170 (DBF-PTY-008)
Expected     : both calls → 200 · step 2 → false (never left true by two interleaved counts); exactly two `pty.PartyRoleDeactivated` outbox events
Test data    : Party P-000170 (test-local); role identifiers are placeholders
<!-- TC:TC-PTY-173:END -->

<!-- TC:TC-PTY-174:START traces=AC-PTY-040,REQ-PTY-034,API-PTY-011 -->
### TC-PTY-174 — Two Address creates on one Party at once get distinct codes and one default
Derived from : AC-PTY-040  (REQ-PTY-034)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses (twice, in parallel)
Rule / code  : RULE-PTY-017 → — (guard: Party row lock; UQ_PTY_ADDRESS_PARTY_CODE and UQ_PTY_ADDRESS_IS_DEFAULT_FL backstops — ADR-PTY-026, ADR-PTY-040)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Party P-000120 (holding a Customer) with no active BILLING Address and highest Address code "2"; the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. two threads, released together by a barrier, send POST …/addresses with addressTypeCode BILLING and line1 "A St" / "B St" · 2. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : both calls → 201 · the two Addresses carry codes "3" and "4" · exactly one of them has isDefaultFl true
Test data    : Party P-000120, BILLING (AC; seeded value); lines are test-local
<!-- TC:TC-PTY-174:END -->

<!-- TC:TC-PTY-175:START traces=AC-PTY-120,REQ-PTY-106,API-PTY-017 -->
### TC-PTY-175 — Two concurrent credit edits chain their audit old values
Derived from : AC-PTY-120  (REQ-PTY-106)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId} (twice, in parallel)
Rule / code  : RULE-PTY-030 → — (guard: the Customer row lock QR-PTY-006)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; audit rows of entity type PTY_CUSTOMER captured; two Owner sessions holding PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. two threads, released together by a barrier, send PUT …/5001 with creditLimit 60000 and with creditLimit 70000 (paymentTermDays as stored) · 2. GET /api/v1/pty/customer-credits/5001
Expected     : both calls → 200 · exactly two creditLimit audit rows: the first old 50000 → new X, the second old X → new Y, where {X, Y} = {60000, 70000} · step 2 → creditLimit Y
Test data    : 50000, 60000, 70000 (AC base value + test-local)
<!-- TC:TC-PTY-175:END -->

<!-- TC:TC-PTY-176:START traces=AC-PTY-132,REQ-PTY-113,API-PTY-019 -->
### TC-PTY-176 — Two requests for one sale at once: exactly one is created
Derived from : AC-PTY-132  (REQ-PTY-113)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides (twice, in parallel)
Rule / code  : RULE-PTY-034 → PTY-409-REQUEST-OPEN-FOR-SALE (guard: Customer row lock + UQ_PTY_CREDIT_OVERRIDE_REQUEST_OPEN_SOURCE — ADR-PTY-042)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; Customer 5001 with creditLimit 50000 and a test contributor reporting 49000; no open request for sourceDocumentRef "POS-1-000490"; two sessions holding PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. two threads, released together by a barrier, send POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000490"
Expected     : exactly one call → 201 (PENDING); the other → 409 · code PTY-409-REQUEST-OPEN-FOR-SALE whose messages name the winner's docNo · exactly one request for (5001, "POS-1-000490")
Test data    : "POS-1-000490" (test-local)
<!-- TC:TC-PTY-176:END -->

<!-- TC:TC-PTY-178:START traces=AC-PTY-003,REQ-PTY-002,API-PTY-002 -->
### TC-PTY-178 — A Customer of another tenant reads as not found
Derived from : AC-PTY-003  (REQ-PTY-002)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : PLATFORM-STD → PTY-404-CUSTOMER-NOT-FOUND (tenant filter — ADR-PTY-035)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: two tenants T1 and T2 (`@MultiTenantOnly`); Customer 5001 exists in T1; a principal of T2 holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. as the T2 principal, GET /api/v1/pty/customers/5001
Expected     : 404 · code PTY-404-CUSTOMER-NOT-FOUND · messageAr «العميل غير موجود.» · messageEn «The customer was not found.»
Test data    : customerId 5001 (AC); tenants test-local
<!-- TC:TC-PTY-178:END -->
<!-- TC:TC-PTY-191:START traces=AC-PTY-137,REQ-PTY-114,API-PTY-019 -->
### TC-PTY-191 — Credit override request refused under WARN
Derived from : AC-PTY-137  (REQ-PTY-114)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-035 → PTY-422-OVERRIDE-NOT-REQUIRED (ADR-PTY-045)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; the tenant setting PTY_CREDIT_OVERRUN_ACTION is WARN; Customer 5001 with creditLimit 50000; a test contributor reports 49000 (the server-side check answers OVER_LIMIT); caller (Sales & POS principal) holds PERM_PTY_CREDIT_OVERRIDE_CREATE; the request count and the PTY_CREDIT_OVERRIDE series value are recorded; `NotificationDispatchApi` calls captured
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000500"
Expected     : 422 · code PTY-422-OVERRIDE-NOT-REQUIRED · messageAr «لا حاجة إلى موافقة: إعداد تجاوز الائتمان هو التنبيه فقط، ويمكن إتمام البيع دون طلب.» · messageEn «No approval is needed: the credit-overrun setting is Warn only, so the sale can be completed without a request.» · request count unchanged; no number drawn from PTY_CREDIT_OVERRIDE; no notification dispatched
Test data    : WARN, customer 5001, 50000, 49000, 2000, "POS-1-000500" (AC)
<!-- TC:TC-PTY-191:END -->

<!-- TC:TC-PTY-192:START traces=AC-PTY-128,REQ-PTY-111,API-PTY-004,API-PTY-019 -->
### TC-PTY-192 — Deactivation racing a request creation leaves no open request
Derived from : AC-PTY-128  (REQ-PTY-111)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-019 POST /api/v1/pty/credit-overrides (in parallel)
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE (guard: the Customer row lock QR-PTY-006 taken by both, and the cancellation of open requests by QR-PTY-039 — ADR-PTY-041)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; active Customer 5001 of Party P-000120 with creditLimit 50000 and a test contributor reporting 49000; no request for sourceDocumentRef "POS-1-000510"; an Owner session holding PERM_PTY_CUSTOMER_DELETE and PERM_PTY_CREDIT_OVERRIDE_VIEW, _UPDATE; a Seller/Cashier session holding PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. two threads, released together by a barrier: the Owner sends DELETE /api/v1/pty/customers/5001; the Seller/Cashier sends POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000510" · 2. as the Owner, GET /api/v1/pty/credit-overrides?customerId=5001
Expected     : the deactivation → 200 in every interleaving. The creation ends in exactly one of two ways: (a) it waited and lost → 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000120 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000120 is inactive and cannot be used on a new document. Reactivate it or choose another party.», and no request for "POS-1-000510" exists; or (b) it committed first → 201, and the deactivation cancelled it. Single end state (step 2): Customer 5001 is inactive and none of its requests is PENDING or APPROVED
Test data    : customer 5001, P-000120, "POS-1-000510" (test-local reference)
<!-- TC:TC-PTY-192:END -->

<!-- TC:TC-PTY-193:START traces=AC-PTY-129,REQ-PTY-111,API-PTY-004,API-PTY-021,API-PTY-036 -->
### TC-PTY-193 — An approval and a use racing the Customer's deactivation
Derived from : AC-PTY-129  (REQ-PTY-111)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-021 PATCH …/approval · API-PTY-036 PATCH …/use (in parallel)
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING · RULE-PTY-028 → PTY-422-APPROVAL-NOT-COVERING (guard: QR-PTY-039 row locks in PK order and the compare-and-set of QR-PTY-034)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: `b2bCredit` on; active Customer 5001; PENDING request CO-000053 and APPROVED request CO-000054 (saleAmount 2000, sourceDocumentRef "POS-1-000520"), both for Customer 5001; an Owner session O1 holding PERM_PTY_CUSTOMER_DELETE; an Owner session O2 holding PERM_PTY_CREDIT_OVERRIDE_UPDATE and _VIEW; a Sales & POS session holding PERM_PTY_CREDIT_OVERRIDE_CREATE; CREDIT_OVERRIDE_STATUS labels as seeded
Host data    : none
Steps        : 1. three threads, released together by a barrier: O1 sends DELETE /api/v1/pty/customers/5001; O2 sends PATCH …/{id of CO-000053}/approval; the Sales & POS session sends PATCH …/{id of CO-000054}/use with customerId 5001, saleAmount 1500, usedDocumentRef "POS-1-000520" · 2. GET CO-000053 and CO-000054
Expected     : the deactivation → 200. The three outcomes the backend plan states (API-PTY-004 Concurrency):
- the approval either loses → 409 · code PTY-409-REQUEST-NOT-PENDING · messageAr «هذا الطلب لم يعد معلّقاً (الحالة: ملغى).» · messageEn «This request is no longer pending (status: Cancelled).», or wins → 200 and the deactivation then cancels it; CO-000053 ends CANCELLED either way;
- the use either loses → 422 · code PTY-422-APPROVAL-NOT-COVERING · messageAr «الموافقة CO-000054 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · messageEn «Approval CO-000054 does not cover this sale. Check the customer and the amount, or request a new approval.», and CO-000054 ends CANCELLED;
- or the use wins → 200, and CO-000054 ends USED with usedDocumentRef "POS-1-000520" (the cancellation skips a USED request).
Never: a request both cancelled and used, or an APPROVED request left after the deactivation committed
Test data    : CO-000053, CO-000054, "POS-1-000520" (test-local)
<!-- TC:TC-PTY-193:END -->

<!-- TC:TC-PTY-194:START traces=AC-PTY-063,REQ-PTY-055,REQ-PTY-050,API-PTY-034 -->
### TC-PTY-194 — Two guest orders with one new email at once attach to one Customer
Derived from : AC-PTY-063  (REQ-PTY-055)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers (twice, in parallel)
Rule / code  : — (guard: UQ_PTY_PARTY_EMAIL, the REQUIRES_NEW creation and one re-match — ADR-PTY-026)
Package      : SVC-API-INT
Scenario     : STATE · data class EDGE · language ALL
Preconditions: no active Party holds "race.guest@example.com"; two storefront sessions holding PERM_PTY_CUSTOMER_CREATE; `SecCustomerInvitationApi` calls captured; Party and Customer counts recorded
Host data    : none
Steps        : 1. two threads, released together by a barrier, send POST /api/v1/pty/guest-customers with nameEn "Race Guest", email "race.guest@example.com", deliveryAddress {line1 "1 Race St"} · 2. wait for both commits
Expected     : both calls → 200 with the same data.customerId; one carries data.created true, the other data.created false · Party +1, Customer +1, one DELIVERY Address · no error code is returned to either call (the loser's REQUIRES_NEW creation rolls back and its re-match attaches the order) · after commit, each call has requested its CUSTOMER-realm invitation for that Customer, addressed to "race.guest@example.com" (REQ-PTY-051, REQ-PTY-059)
Test data    : "race.guest@example.com", "Race Guest", "1 Race St" (test-local)
<!-- TC:TC-PTY-194:END -->

<!-- TC:TC-PTY-195:START traces=AC-PTY-036,REQ-PTY-030,REQ-PTY-031,REQ-PTY-034,API-PTY-011,API-PTY-013 -->
### TC-PTY-195 — An Address type change racing an Address create of the same type
Derived from : AC-PTY-036  (REQ-PTY-030)
Exercises    : API-PTY-013 PUT /api/v1/pty/parties/{partyId}/addresses/{id} · API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses (in parallel)
Rule / code  : RULE-PTY-017 → — (guard: the Party row lock QR-PTY-001; UQ_PTY_ADDRESS_IS_DEFAULT_FL backstop — ADR-PTY-026)
Package      : DATA-DOM-MASTER
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Party P-000120 (holding a Customer) with active DELIVERY Addresses "1" (default) and "2", and no BILLING Address; the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. two threads, released together by a barrier: PUT …/addresses/{id of "2"} with the stored fields and addressTypeCode BILLING; POST …/addresses with addressTypeCode BILLING and line1 "5 Nile St" · 2. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : the PUT → 200 and the POST → 201 (neither is refused) · step 2 → exactly one active BILLING Address has isDefaultFl true (whichever of the two ran first), Address "1" is still the DELIVERY default, and the two BILLING Addresses carry distinct codes
Test data    : Party P-000120, BILLING, DELIVERY (seeded), "5 Nile St" (AC-PTY-036 Party; line test-local)
<!-- TC:TC-PTY-195:END -->

<!-- TC:TC-PTY-198:START traces=AC-PTY-140,REQ-PTY-110,REQ-PTY-074,API-PTY-035 -->
### TC-PTY-198 — Flag off and an inactive Customer: the credit check refuses before answering NOT_APPLICABLE
Derived from : AC-PTY-140  (REQ-PTY-110)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : RULE-PTY-005 → PTY-422-ROLE-INACTIVE · PLATFORM-STD → PTY-404-CUSTOMER-NOT-FOUND (order: load the Customer → RULE-PTY-005 → flag)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` off; Customer 5002 of Party P-000121 with isActiveFl false; no Customer 999999 in the tenant; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5002 and saleAmount 100 · 2. POST /api/v1/pty/credit-checks with customerId 999999 and saleAmount 100
Expected     : step 1 → 422 · code PTY-422-ROLE-INACTIVE · messageAr «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · messageEn «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» · no resultCode (not NOT_APPLICABLE) · step 2 → 404 · code PTY-404-CUSTOMER-NOT-FOUND · messageAr «العميل غير موجود.» · messageEn «The customer was not found.»
Test data    : customerId 5002, P-000121, saleAmount 100 (AC); 999999 test-local
<!-- TC:TC-PTY-198:END -->
<!-- SUB:RULE-SCENARIOS:END -->

<!-- SUB:API-SCENARIOS:START traces=REQ-PTY-001,REQ-PTY-002,REQ-PTY-004,REQ-PTY-005,REQ-PTY-007,REQ-PTY-008,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-019,REQ-PTY-020,REQ-PTY-021,REQ-PTY-023,REQ-PTY-025,REQ-PTY-026,REQ-PTY-030,REQ-PTY-031,REQ-PTY-032,REQ-PTY-033,REQ-PTY-035,REQ-PTY-039,REQ-PTY-041,REQ-PTY-042,REQ-PTY-043,REQ-PTY-045,REQ-PTY-046,REQ-PTY-050,REQ-PTY-051,REQ-PTY-052,REQ-PTY-055,REQ-PTY-056,REQ-PTY-057,REQ-PTY-058,REQ-PTY-059,REQ-PTY-060,REQ-PTY-061,REQ-PTY-063,REQ-PTY-064,REQ-PTY-065,REQ-PTY-068,REQ-PTY-069,REQ-PTY-075,REQ-PTY-076,REQ-PTY-078,REQ-PTY-079,REQ-PTY-080,REQ-PTY-083,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,REQ-PTY-091,REQ-PTY-093,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-106,REQ-PTY-107,REQ-PTY-108,AC-PTY-001,AC-PTY-002,AC-PTY-003,AC-PTY-006,AC-PTY-007,AC-PTY-009,AC-PTY-010,AC-PTY-011,AC-PTY-016,AC-PTY-018,AC-PTY-019,AC-PTY-020,AC-PTY-021,AC-PTY-022,AC-PTY-023,AC-PTY-024,AC-PTY-025,AC-PTY-026,AC-PTY-028,AC-PTY-030,AC-PTY-031,AC-PTY-036,AC-PTY-037,AC-PTY-038,AC-PTY-039,AC-PTY-041,AC-PTY-045,AC-PTY-047,AC-PTY-048,AC-PTY-050,AC-PTY-051,AC-PTY-053,AC-PTY-054,AC-PTY-058,AC-PTY-059,AC-PTY-060,AC-PTY-063,AC-PTY-064,AC-PTY-065,AC-PTY-066,AC-PTY-067,AC-PTY-068,AC-PTY-069,AC-PTY-071,AC-PTY-072,AC-PTY-073,AC-PTY-076,AC-PTY-077,AC-PTY-084,AC-PTY-086,AC-PTY-088,AC-PTY-089,AC-PTY-090,AC-PTY-091,AC-PTY-094,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-099,AC-PTY-100,AC-PTY-101,AC-PTY-102,AC-PTY-105,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-120,AC-PTY-121,AC-PTY-122,AC-PTY-135,AC-PTY-136,AC-PTY-138,AC-PTY-139 -->
### API-SCENARIOS — endpoint behaviour, permission, paging

<!-- TC:TC-PTY-054:START traces=AC-PTY-001,REQ-PTY-001,API-PTY-026 -->
### TC-PTY-054 — Customer registered at the point of sale found once in the back-office
Derived from : AC-PTY-001  (REQ-PTY-001)
Exercises    : API-PTY-026 GET /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Customer registered from the point of sale (API-PTY-001 by the Sales & POS principal) received identifier 5001 and Party code P-000120; the Owner holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. as the Owner, GET /api/v1/pty/customers?code=P-000120
Expected     : 200 · data.totalElements 1 · data.content[0].customerPk 5001 · data.content[0].code P-000120
Test data    : customerId 5001, code P-000120 (AC)
<!-- TC:TC-PTY-054:END -->

<!-- TC:TC-PTY-055:START traces=AC-PTY-002,REQ-PTY-001,API-PTY-002,API-PTY-034,API-PTY-026 -->
### TC-PTY-055 — Storefront-attached Customer is the same back-office Customer
Derived from : AC-PTY-002  (REQ-PTY-001)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id} · API-PTY-026 GET /api/v1/pty/customers (after API-PTY-034)
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5001 with email `<email of 5001>`; the storefront has attached a guest order to it (API-PTY-034 with that email returned customerId 5001); the Owner holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5001 · 2. GET /api/v1/pty/customers?email=`<email of 5001>`
Expected     : step 1 → 200, data.customerPk 5001 · step 2 → data.totalElements 1 (no second Customer with that email in the tenant)
Test data    : customerId 5001 (AC); `<email of 5001>` placeholder — the AC names no email
<!-- TC:TC-PTY-055:END -->

<!-- TC:TC-PTY-056:START traces=AC-PTY-003,REQ-PTY-002,API-PTY-002 -->
### TC-PTY-056 — Customer read for a consuming module
Derived from : AC-PTY-003  (REQ-PTY-002)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5001 with Party code P-000120, nameEn "Nour Trading", customerGroupCode WHOLESALE, isActiveFl true; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/5001
Expected     : 200 · data.customerPk 5001 · data.code P-000120 · data.nameEn "Nour Trading" · data.customerGroupCode WHOLESALE · data.isActiveFl true
Test data    : values of the AC
<!-- TC:TC-PTY-056:END -->

<!-- TC:TC-PTY-057:START traces=AC-PTY-006,REQ-PTY-004,API-PTY-001 -->
### TC-PTY-057 — Registration creates one Party and one active Customer
Derived from : AC-PTY-006  (REQ-PTY-004)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier holding PERM_PTY_CUSTOMER_CREATE; Party and Customer counts recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameAr «محمد علي» and nothing else
Expected     : 201 · data.customerPk and data.partyFk returned · data.nameAr «محمد علي» · data.isActiveFl true · Party count +1, Customer count +1, the new Customer's partyFk = the new Party's identifier
Test data    : nameAr «محمد علي» (AC)
<!-- TC:TC-PTY-057:END -->

<!-- TC:TC-PTY-058:START traces=AC-PTY-007,REQ-PTY-005,API-PTY-001 -->
### TC-PTY-058 — One name is the only input a registration needs
Derived from : AC-PTY-007  (REQ-PTY-005)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier holding PERM_PTY_CUSTOMER_CREATE (registration opened from a sale)
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameEn "Walid Hassan" only
Expected     : 201 · the Customer is saved. The count of mandatory marks on the form is TC-PTY-122
Test data    : nameEn "Walid Hassan" (AC)
<!-- TC:TC-PTY-058:END -->

<!-- TC:TC-PTY-059:START traces=AC-PTY-009,REQ-PTY-007,API-PTY-001 -->
### TC-PTY-059 — Customer without a group gets RETAIL
Derived from : AC-PTY-009  (REQ-PTY-007)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameEn "Sara Adel" and no customerGroupCode
Expected     : 201 · data.customerGroupCode RETAIL
Test data    : nameEn "Sara Adel" (AC)
<!-- TC:TC-PTY-059:END -->

<!-- TC:TC-PTY-060:START traces=AC-PTY-010,REQ-PTY-008,API-PTY-001 -->
### TC-PTY-060 — In-place registration returns identifier and code
Derived from : AC-PTY-010  (REQ-PTY-008)
Exercises    : API-PTY-001 POST /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier holding PERM_PTY_CUSTOMER_CREATE, registering from an open sale
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with nameEn "Omar Said"
Expected     : 201 · data.customerPk present · data.code present ("P-" + 6 digits). Handing them to the sale screen is TC-PTY-146
Test data    : nameEn "Omar Said" (AC)
<!-- TC:TC-PTY-060:END -->

<!-- TC:TC-PTY-061:START traces=AC-PTY-011,REQ-PTY-008,API-PTY-006 -->
### TC-PTY-061 — In-place Supplier registration returns identifier and code
Derived from : AC-PTY-011  (REQ-PTY-008)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner holding PERM_PTY_SUPPLIER_CREATE, registering from an open purchase document
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with nameAr «مؤسسة النور»
Expected     : 201 · data.supplierPk present · data.code present
Test data    : nameAr «مؤسسة النور» (AC)
<!-- TC:TC-PTY-061:END -->

<!-- TC:TC-PTY-062:START traces=AC-PTY-016,REQ-PTY-012,API-PTY-003 -->
### TC-PTY-062 — Seller/Cashier updates a Customer's mobile
Derived from : AC-PTY-016  (REQ-PTY-012)
Exercises    : API-PTY-003 PUT /api/v1/pty/customers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5001 with mobile "+201001112222"; a Seller/Cashier holding PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customers/5001 with the stored fields and mobile "+201003334444"
Expected     : 200 · data.mobile "+201003334444" · data.updatedBy the Seller/Cashier's principal
Test data    : customerId 5001, mobiles (AC)
<!-- TC:TC-PTY-062:END -->

<!-- TC:TC-PTY-063:START traces=AC-PTY-018,REQ-PTY-013,API-PTY-026 -->
### TC-PTY-063 — Customer search by group and active state, paged
Derived from : AC-PTY-018  (REQ-PTY-013)
Exercises    : API-PTY-026 GET /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: exactly 3 active Customers with customerGroupCode WHOLESALE and 5 with RETAIL; the Owner holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers?customerGroupCode=WHOLESALE&isActive=true&size=20
Expected     : 200 · data.content has 3 rows, each customerGroupCode WHOLESALE · data.totalElements 3
Test data    : counts 3 / 5, page size 20 (AC; seeded group values)
<!-- TC:TC-PTY-063:END -->

<!-- TC:TC-PTY-064:START traces=AC-PTY-019,REQ-PTY-014,API-PTY-004,API-PTY-002 -->
### TC-PTY-064 — Deactivated Customer stays readable, with the confirmation
Derived from : AC-PTY-019  (REQ-PTY-014)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id} · API-PTY-002 GET /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-006 → confirmation (no catalog code)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Customer 5003 with Party code P-000123 (earlier sales invoices referencing it belong to Sales & POS — N11); the Owner holds PERM_PTY_CUSTOMER_DELETE and _VIEW
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/5003 · 2. GET /api/v1/pty/customers/5003
Expected     : step 1 → 200 · data {id 5003, code P-000123} · messageAr «تم إيقاف P-000123. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة.» · messageEn «P-000123 is deactivated. Earlier documents keep it; it is no longer offered on new documents.» · step 2 → 200, isActiveFl false
Test data    : customerId 5003, code P-000123 (AC)
<!-- TC:TC-PTY-064:END -->

<!-- TC:TC-PTY-065:START traces=AC-PTY-020,REQ-PTY-015,API-PTY-005,API-PTY-026 -->
### TC-PTY-065 — Reactivated Customer returned by the active search
Derived from : AC-PTY-020  (REQ-PTY-015)
Exercises    : API-PTY-005 PATCH /api/v1/pty/customers/{id}/activation · API-PTY-026 GET /api/v1/pty/customers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5003 with isActiveFl false; no other active Party holds its email; the Owner holds PERM_PTY_CUSTOMER_DELETE and _VIEW
Host data    : none
Steps        : 1. PATCH /api/v1/pty/customers/5003/activation with {"isActiveFl": true} · 2. GET /api/v1/pty/customers?isActive=true&code=`<code of 5003>`
Expected     : step 1 → 200, data.isActiveFl true · step 2 → Customer 5003 in data.content
Test data    : customerId 5003 (AC); body per ADR-PTY-031
<!-- TC:TC-PTY-065:END -->

<!-- TC:TC-PTY-066:START traces=AC-PTY-021,REQ-PTY-016,API-PTY-006 -->
### TC-PTY-066 — Supplier registration with one name
Derived from : AC-PTY-021  (REQ-PTY-016)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner holding PERM_PTY_SUPPLIER_CREATE; Party and Supplier counts recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with nameEn "Delta Imports" only
Expected     : 201 · data.nameEn "Delta Imports" · data.isActiveFl true · data.partyFk = the new Party · Party +1, Supplier +1. The mandatory-mark count is TC-PTY-124
Test data    : nameEn "Delta Imports" (AC)
<!-- TC:TC-PTY-066:END -->

<!-- TC:TC-PTY-067:START traces=AC-PTY-022,REQ-PTY-017,API-PTY-008 -->
### TC-PTY-067 — Supplier payment terms and tax number updated
Derived from : AC-PTY-022  (REQ-PTY-017)
Exercises    : API-PTY-008 PUT /api/v1/pty/suppliers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Supplier 7002 with paymentTermDays empty and taxRegistrationNo empty; the Owner holds PERM_PTY_SUPPLIER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/suppliers/7002 with the stored fields, paymentTermDays 30 and taxRegistrationNo "300123456700003"
Expected     : 200 · data.paymentTermDays 30 · data.taxRegistrationNo "300123456700003" (held on the Party)
Test data    : supplierId 7002, 30, "300123456700003" (AC)
<!-- TC:TC-PTY-067:END -->

<!-- TC:TC-PTY-068:START traces=AC-PTY-023,REQ-PTY-018,API-PTY-028 -->
### TC-PTY-068 — Supplier search by name and active state
Derived from : AC-PTY-023  (REQ-PTY-018)
Exercises    : API-PTY-028 GET /api/v1/pty/suppliers
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Suppliers "Delta Imports" (P-000201) and "Delta Steel" (P-000202), inactive Supplier "Delta Old" (P-000203); a Warehouse user holding PERM_PTY_SUPPLIER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/suppliers?name=Delta&isActive=true
Expected     : 200 · data.content holds exactly P-000201 and P-000202
Test data    : names and codes of the AC
<!-- TC:TC-PTY-068:END -->

<!-- TC:TC-PTY-069:START traces=AC-PTY-024,REQ-PTY-019,API-PTY-007 -->
### TC-PTY-069 — Supplier read for a consuming module
Derived from : AC-PTY-024  (REQ-PTY-019)
Exercises    : API-PTY-007 GET /api/v1/pty/suppliers/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Supplier 7002 with Party code P-000202 and paymentTermDays 30; caller (Settlements principal) holds PERM_PTY_SUPPLIER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/suppliers/7002
Expected     : 200 · data.supplierPk 7002 · data.code P-000202 · data.paymentTermDays 30 · data.isActiveFl true
Test data    : values of the AC
<!-- TC:TC-PTY-069:END -->

<!-- TC:TC-PTY-070:START traces=AC-PTY-025,REQ-PTY-020,API-PTY-009,API-PTY-007 -->
### TC-PTY-070 — Deactivated Supplier stays readable
Derived from : AC-PTY-025  (REQ-PTY-020)
Exercises    : API-PTY-009 DELETE /api/v1/pty/suppliers/{id} · API-PTY-007 GET /api/v1/pty/suppliers/{id}
Rule / code  : RULE-PTY-006 → confirmation (no catalog code)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Supplier 7003 with Party code P-000203 (an earlier purchase order referencing it belongs to Purchasing — N11); the Owner holds PERM_PTY_SUPPLIER_DELETE and _VIEW
Host data    : none
Steps        : 1. DELETE /api/v1/pty/suppliers/7003 · 2. GET /api/v1/pty/suppliers/7003
Expected     : step 1 → 200 · step 2 → 200, isActiveFl false
Test data    : supplierId 7003, code P-000203 (AC)
<!-- TC:TC-PTY-070:END -->

<!-- TC:TC-PTY-071:START traces=AC-PTY-026,REQ-PTY-021,API-PTY-010 -->
### TC-PTY-071 — Supplier reactivated
Derived from : AC-PTY-026  (REQ-PTY-021)
Exercises    : API-PTY-010 PATCH /api/v1/pty/suppliers/{id}/activation
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Supplier 7003 with isActiveFl false; its Party's email (if any) held by no other active Party; the Owner holds PERM_PTY_SUPPLIER_DELETE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/suppliers/7003/activation with {"isActiveFl": true}
Expected     : 200 · data.isActiveFl true
Test data    : supplierId 7003 (AC); body per ADR-PTY-031
<!-- TC:TC-PTY-071:END -->

<!-- TC:TC-PTY-072:START traces=AC-PTY-028,REQ-PTY-023,API-PTY-004 -->
### TC-PTY-072 — Deactivation publishes party-role-deactivated
Derived from : AC-PTY-028  (REQ-PTY-023)
Exercises    : API-PTY-004 DELETE /api/v1/pty/customers/{id}
Rule / code  : RULE-PTY-006 → confirmation + outbox event
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Customer 5003 with Party code P-000123; calls to the given `OutboxPublisher` are captured; the Owner holds PERM_PTY_CUSTOMER_DELETE
Host data    : none
Steps        : 1. DELETE /api/v1/pty/customers/5003 · 2. roll back a second, identical deactivation of another Customer inside a failing transaction (test hook)
Expected     : step 1 → 200 · exactly one `OutboxPublisher.publish("pty.PartyRoleDeactivated", {role CUSTOMER, identifier 5003, code P-000123})` inside the deactivation transaction (ADR-PTY-038) · step 2 → no outbox row stored for the rolled-back deactivation
Test data    : customerId 5003, code P-000123 (AC)
<!-- TC:TC-PTY-072:END -->

<!-- TC:TC-PTY-073:START traces=AC-PTY-030,REQ-PTY-025,API-PTY-006 -->
### TC-PTY-073 — "Make supplier" adds the role to the same Party
Derived from : AC-PTY-030  (REQ-PTY-025)
Exercises    : API-PTY-006 POST /api/v1/pty/suppliers (partyId)
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Customer 5004 of Party P-000124 and no Supplier for that Party; the Owner holds PERM_PTY_SUPPLIER_CREATE; the tenant's Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/suppliers with {"partyId": <identifier of P-000124>} only ("Make supplier" / «إضافة كمورّد»)
Expected     : 201 · data.partyFk = P-000124's identifier · data.isActiveFl true · Party count unchanged
Test data    : Customer 5004, Party P-000124 (AC)
<!-- TC:TC-PTY-073:END -->

<!-- TC:TC-PTY-074:START traces=AC-PTY-031,REQ-PTY-026,API-PTY-001 -->
### TC-PTY-074 — "Make customer" adds a RETAIL Customer to the Supplier's Party
Derived from : AC-PTY-031  (REQ-PTY-026)
Exercises    : API-PTY-001 POST /api/v1/pty/customers (partyId)
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Supplier 7004 of Party P-000204 and no Customer for that Party; the Owner holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/customers with {"partyId": <identifier of P-000204>} only ("Make customer" / «إضافة كعميل»)
Expected     : 201 · data.partyFk = P-000204's identifier · data.customerGroupCode RETAIL · data.isActiveFl true
Test data    : Supplier 7004, Party P-000204 (AC)
<!-- TC:TC-PTY-074:END -->

<!-- TC:TC-PTY-075:START traces=AC-PTY-036,REQ-PTY-030,API-PTY-011 -->
### TC-PTY-075 — Address created on a Party
Derived from : AC-PTY-036  (REQ-PTY-030)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Party P-000120 (holding a Customer) with no Address; the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. POST /api/v1/pty/parties/{identifier of P-000120}/addresses with addressTypeCode DELIVERY, line1 "12 Tahrir St", city "Cairo"
Expected     : 201 · data.partyFk = P-000120's identifier · data.addressTypeCode DELIVERY · data.code "1" · data.isActiveFl true
Test data    : values of the AC (DELIVERY seeded)
<!-- TC:TC-PTY-075:END -->

<!-- TC:TC-PTY-076:START traces=AC-PTY-037,REQ-PTY-031,API-PTY-013 -->
### TC-PTY-076 — Address city updated, Party unchanged
Derived from : AC-PTY-037  (REQ-PTY-031)
Exercises    : API-PTY-013 PUT /api/v1/pty/parties/{partyId}/addresses/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Address code "1" of Party P-000120 with city "Cairo"; the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/parties/{identifier of P-000120}/addresses/{id of "1"} with the stored fields and city "Giza"
Expected     : 200 · data.city "Giza" · data.partyFk unchanged
Test data    : values of the AC
<!-- TC:TC-PTY-076:END -->

<!-- TC:TC-PTY-077:START traces=AC-PTY-038,REQ-PTY-032,API-PTY-029 -->
### TC-PTY-077 — Party addresses read returns the active ones only
Derived from : AC-PTY-038  (REQ-PTY-032)
Exercises    : API-PTY-029 GET /api/v1/pty/parties/{partyId}/addresses
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Party P-000120 with 2 active Addresses (BILLING default, DELIVERY default) and 1 inactive DELIVERY Address; caller (storefront customer area principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : 200 · data holds exactly the 2 active Addresses, each isDefaultFl true
Test data    : values of the AC
<!-- TC:TC-PTY-077:END -->

<!-- TC:TC-PTY-078:START traces=AC-PTY-039,REQ-PTY-033,API-PTY-014,API-PTY-029,API-PTY-012 -->
### TC-PTY-078 — Deactivated Address leaves the list but stays readable
Derived from : AC-PTY-039  (REQ-PTY-033)
Exercises    : API-PTY-014 DELETE /api/v1/pty/parties/{partyId}/addresses/{id} · API-PTY-029 GET …/addresses · API-PTY-012 GET /api/v1/pty/addresses/{id}
Rule / code  : RULE-PTY-006 → confirmation (no catalog code)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Address 9001 of a Party `<partyId>` (an earlier sales order referencing it belongs to Sales & POS — N11); the Owner holds PERM_PTY_CUSTOMER_UPDATE and _VIEW
Host data    : none
Steps        : 1. DELETE /api/v1/pty/parties/{partyId}/addresses/9001 · 2. GET /api/v1/pty/parties/{partyId}/addresses · 3. GET /api/v1/pty/addresses/9001
Expected     : step 1 → 200 · step 2 → Address 9001 absent · step 3 → 200, isActiveFl false
Test data    : addressId 9001 (AC); `<partyId>` placeholder
<!-- TC:TC-PTY-078:END -->

<!-- TC:TC-PTY-079:START traces=AC-PTY-041,REQ-PTY-035,API-PTY-015,API-PTY-029 -->
### TC-PTY-079 — Marking an Address default clears the previous default
Derived from : AC-PTY-041  (REQ-PTY-035)
Exercises    : API-PTY-015 PATCH /api/v1/pty/parties/{partyId}/addresses/{id}/default · API-PTY-029 GET …/addresses
Rule / code  : RULE-PTY-017 → confirmation (no catalog code)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Party P-000120 with DELIVERY Address "1" default and DELIVERY Address "2" not default; ADDRESS_TYPE labels as seeded (DELIVERY = توصيل / Delivery); the Owner holds PERM_PTY_CUSTOMER_UPDATE
Host data    : none
Steps        : 1. PATCH …/addresses/{id of "2"}/default with {"isDefaultFl": true} · 2. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : step 1 → 200 · data.isDefaultFl true · messageAr «أصبح هذا العنوان هو العنوان الافتراضي من نوع توصيل.» · messageEn «This address is now the default Delivery address.» · step 2 → "2" isDefaultFl true, "1" isDefaultFl false
Test data    : Address codes "1", "2" (AC); body per ADR-PTY-031
<!-- TC:TC-PTY-079:END -->

<!-- TC:TC-PTY-080:START traces=AC-PTY-047,REQ-PTY-041,API-PTY-027 -->
### TC-PTY-080 — Global search returns Customers only, by name
Derived from : AC-PTY-047  (REQ-PTY-041)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language en
Preconditions: active Customers "Nour Trading" (P-000120, mobile "+201001112222") and "Nour Home" (P-000150); Supplier "Nour Supplies"; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW; request language en (display-name order)
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=nour
Expected     : 200 · data is exactly ["Nour Home", "Nour Trading"] in that order; "Nour Supplies" is not among them
Test data    : values of the AC
<!-- TC:TC-PTY-080:END -->

<!-- TC:TC-PTY-081:START traces=AC-PTY-048,REQ-PTY-041,API-PTY-027 -->
### TC-PTY-081 — Global search matches part of a mobile
Derived from : AC-PTY-048  (REQ-PTY-041)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an active Customer `<customerId>` with mobile "+201001112222"; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=1112222
Expected     : 200 · that Customer is in data
Test data    : mobile "+201001112222", term "1112222" (AC)
<!-- TC:TC-PTY-081:END -->

<!-- TC:TC-PTY-082:START traces=AC-PTY-050,REQ-PTY-042,API-PTY-027 -->
### TC-PTY-082 — Arabic letter forms unified in the global search
Derived from : AC-PTY-050  (REQ-PTY-042)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class EDGE · language ar
Preconditions: an active Customer with nameAr «أحمد مصطفى»; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=احمد
Expected     : 200 · the Customer «أحمد مصطفى» is in data
Test data    : nameAr «أحمد مصطفى», term «احمد» (AC)
<!-- TC:TC-PTY-082:END -->

<!-- TC:TC-PTY-083:START traces=AC-PTY-051,REQ-PTY-043,API-PTY-027 -->
### TC-PTY-083 — Global search reveals no Customer to a user without VIEW
Derived from : AC-PTY-051  (REQ-PTY-043)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — (empty list, not PTY-403-FORBIDDEN)
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a Warehouse user without PERM_PTY_CUSTOMER_VIEW; an active Customer "Nour Trading"
Host data    : none
Steps        : 1. as the Warehouse user, GET /api/v1/pty/customers/global-search?term=Nour
Expected     : 200 · data is an empty list (0 customer results)
Test data    : values of the AC
<!-- TC:TC-PTY-083:END -->

<!-- TC:TC-PTY-084:START traces=AC-PTY-053,REQ-PTY-045,API-PTY-033 -->
### TC-PTY-084 — Default Customer of an unnamed POS sale is the walk-in Customer
Derived from : AC-PTY-053  (REQ-PTY-045)
Exercises    : API-PTY-033 GET /api/v1/pty/customers/walk-in
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the tenant's walk-in Customer has identifier 5000; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/walk-in
Expected     : 200 · data.customerPk 5000 · data.isWalkInFl true
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-084:END -->

<!-- TC:TC-PTY-085:START traces=AC-PTY-054,REQ-PTY-046,API-PTY-033 -->
### TC-PTY-085 — Walk-in Customer created on first demand, once
Derived from : AC-PTY-054  (REQ-PTY-046)
Exercises    : API-PTY-033 GET /api/v1/pty/customers/walk-in
Rule / code  : RULE-PTY-014 → — (idempotent ensure)
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: a tenant with no Customer having isWalkInFl true; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/walk-in · 2. GET /api/v1/pty/customers/walk-in · 3. count the tenant's stored Customers with isWalkInFl true
Expected     : step 1 → 200, data.isWalkInFl true · step 2 → the same customerPk as step 1 · step 3 → 1
Test data    : none beyond the AC
<!-- TC:TC-PTY-085:END -->

<!-- TC:TC-PTY-086:START traces=AC-PTY-058,REQ-PTY-050,API-PTY-034,API-PTY-002,API-PTY-029 -->
### TC-PTY-086 — New Customer from a guest's first order
Derived from : AC-PTY-058  (REQ-PTY-050)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers · API-PTY-002 · API-PTY-029 (read back)
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: no active Party has email "lina@example.com" or mobile "+966551234567"; caller (storefront principal) holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with nameEn "Lina Fares", email "lina@example.com", mobile "+966551234567", deliveryAddress {line1 "King Fahd Rd 10", city "Riyadh"} · 2. GET /api/v1/pty/customers/{customerId of step 1} · 3. GET /api/v1/pty/parties/{partyFk}/addresses
Expected     : step 1 → 200, data.customerId returned · Party count +1 · step 2 → customerGroupCode RETAIL · step 3 → one DELIVERY Address, isDefaultFl true
Test data    : values of the AC
<!-- TC:TC-PTY-086:END -->

<!-- TC:TC-PTY-087:START traces=AC-PTY-059,REQ-PTY-051,API-PTY-034 -->
### TC-PTY-087 — Invitation requested for a Customer created from a guest order
Derived from : AC-PTY-059  (REQ-PTY-051)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: as TC-PTY-086 (the request creates Customer 5010 with email "lina@example.com"); calls to the given `SecCustomerInvitationApi` are captured
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with the details of AC-PTY-058 · 2. wait for the commit · 3. read the stored secUserId (DBF-PTY-017) of 5010
Expected     : exactly one CUSTOMER-realm invitation requested, addressed to "lina@example.com", for Customer 5010, after commit · step 3 → empty
Test data    : customerId 5010, "lina@example.com" (AC)
<!-- TC:TC-PTY-087:END -->

<!-- TC:TC-PTY-088:START traces=AC-PTY-060,REQ-PTY-052 -->
### TC-PTY-088 — Accepted invitation links the CUSTOMER-realm user
Derived from : AC-PTY-060  (REQ-PTY-052)
Exercises    : outbox subscriber `PtyCustomerInvitationRedeemedSubscriber` (no API — backend plan CORE, SVC-API-INT)
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a pending invitation for Customer 5010; user 88001 linked to no Customer
Host data    : none
Steps        : 1. deliver to the subscriber a `sec.CustomerInvitationRedeemed` outbox message (subject module PTY, subject type CUSTOMER, subject reference 5010, redeemer 88001) · 2. read the stored secUserId (DBF-PTY-017) of 5010 · 3. redeliver the same message (same OutboxMessage id)
Expected     : step 1 → outcome linked (not REFUSED) · step 2 → 88001 · step 3 → secUserId stays 88001, no second change (idempotent on OutboxMessage.id())
Test data    : customerId 5010, user 88001 (AC)
<!-- TC:TC-PTY-088:END -->

<!-- TC:TC-PTY-089:START traces=AC-PTY-063,REQ-PTY-055,API-PTY-034 -->
### TC-PTY-089 — Guest matched by email without regard to case
Derived from : AC-PTY-063  (REQ-PTY-055)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Party P-000120 with email "nour@example.com" and active Customer 5001; caller (storefront principal) holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "NOUR@example.com", deliveryAddress {line1 `<line1>`}
Expected     : 200 · data.customerId 5001 · Party count unchanged
Test data    : emails, P-000120, 5001 (AC); `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-089:END -->

<!-- TC:TC-PTY-090:START traces=AC-PTY-064,REQ-PTY-056,API-PTY-034 -->
### TC-PTY-090 — Guest matched by a normalised mobile
Derived from : AC-PTY-064  (REQ-PTY-056)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: exactly one active Party P-000150 with mobile "+20 100 555 6666" and active Customer 5015; no Party with email "guest1@example.com"; caller holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "guest1@example.com", mobile "00201005556666", deliveryAddress {line1 `<line1>`}
Expected     : 200 · data.customerId 5015 · Party count unchanged
Test data    : mobiles, email, P-000150, 5015 (AC); `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-090:END -->

<!-- TC:TC-PTY-091:START traces=AC-PTY-065,REQ-PTY-057,API-PTY-034 -->
### TC-PTY-091 — Customer role added to a matched Supplier's Party
Derived from : AC-PTY-065  (REQ-PTY-057)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Party P-000204 with email "delta@example.com" holding a Supplier and no Customer; caller holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "delta@example.com", deliveryAddress {line1 `<line1>`} · 2. GET /api/v1/pty/customers/{customerId of step 1}
Expected     : step 1 → 200, data.customerId returned · Party count unchanged · step 2 → partyFk = P-000204's identifier, customerGroupCode RETAIL
Test data    : P-000204, "delta@example.com" (AC); `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-091:END -->

<!-- TC:TC-PTY-092:START traces=AC-PTY-066,REQ-PTY-058,API-PTY-034,API-PTY-002,API-PTY-029 -->
### TC-PTY-092 — Guest details never change a matched Party
Derived from : AC-PTY-066  (REQ-PTY-058)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers · API-PTY-002 · API-PTY-029 (read back)
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: active Party P-000120 with email "nour@example.com", nameEn "Nour Trading", mobile "+201001112222", 1 active DELIVERY Address, and its active Customer `<customerId>`; caller holds PERM_PTY_CUSTOMER_CREATE and _VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with email "nour@example.com", nameEn "Someone Else", mobile "+201009998888", deliveryAddress {line1 "1 New St"} · 2. GET /api/v1/pty/customers/{customerId} · 3. GET /api/v1/pty/parties/{identifier of P-000120}/addresses
Expected     : step 1 → 200, data.customerId = `<customerId>` · step 2 → nameEn "Nour Trading", mobile "+201001112222" · step 3 → exactly 1 active DELIVERY Address
Test data    : values of the AC
<!-- TC:TC-PTY-092:END -->

<!-- TC:TC-PTY-093:START traces=AC-PTY-067,REQ-PTY-059,API-PTY-034 -->
### TC-PTY-093 — Invitation goes to the matched Party's recorded contact
Derived from : AC-PTY-067  (REQ-PTY-059)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Customer 5015 of Party P-000150 with secUserId empty, email empty and mobile "+201005556666"; no Party holds "guest1@example.com"; invitation calls captured; caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "guest1@example.com", mobile "00201005556666", deliveryAddress {line1 `<line1>`} · 2. wait for the commit
Expected     : data.customerId 5015 · exactly one CUSTOMER-realm invitation requested, addressed to "+201005556666"; none addressed to "guest1@example.com"
Test data    : values of the AC; `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-093:END -->

<!-- TC:TC-PTY-094:START traces=AC-PTY-068,REQ-PTY-060,API-PTY-034 -->
### TC-PTY-094 — No invitation for a Customer already linked
Derived from : AC-PTY-068  (REQ-PTY-060)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Customer 5001 (Party email "nour@example.com") holds secUserId 88001; invitation calls captured; caller holds PERM_PTY_CUSTOMER_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "nour@example.com", deliveryAddress {line1 `<line1>`} · 2. wait for the commit
Expected     : data.customerId 5001 · 0 invitations requested
Test data    : values of the AC; `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-094:END -->

<!-- TC:TC-PTY-095:START traces=AC-PTY-069,REQ-PTY-061,API-PTY-034 -->
### TC-PTY-095 — Mobile shared by two Parties creates a new Customer
Derived from : AC-PTY-069  (REQ-PTY-061)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: active Parties P-000160 and P-000161 both hold mobile "+201007770000"; no Party holds "sam@example.com"; caller holds PERM_PTY_CUSTOMER_CREATE; Party and Customer counts recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "sam@example.com", mobile "+201007770000", deliveryAddress {line1 `<line1>`}
Expected     : 200 · Party +1 and Customer +1 · data.customerId is neither the Customer of P-000160 nor of P-000161
Test data    : values of the AC; `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-095:END -->

<!-- TC:TC-PTY-096:START traces=AC-PTY-071,REQ-PTY-063,API-PTY-034 -->
### TC-PTY-096 — Walk-in Party never matched to a guest
Derived from : AC-PTY-071  (REQ-PTY-063)
Exercises    : API-PTY-034 POST /api/v1/pty/guest-customers
Rule / code  : RULE-PTY-013 → — (matching filter; no error)
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: the walk-in Customer's Party holds mobile "+201000000000" (entered by an Owner through API-PTY-003) and no other Party holds it; caller holds PERM_PTY_CUSTOMER_CREATE; Party count recorded
Host data    : none
Steps        : 1. POST /api/v1/pty/guest-customers with name `<nameEn>`, email "new@example.com", mobile "+201000000000", deliveryAddress {line1 `<line1>`} · 2. read the stored secUserId (DBF-PTY-017) of the walk-in Customer
Expected     : step 1 → 200 · Party +1 · data.customerId is not the walk-in Customer · step 2 → empty
Test data    : values of the AC; `<nameEn>`, `<line1>` placeholders
<!-- TC:TC-PTY-096:END -->

<!-- TC:TC-PTY-097:START traces=AC-PTY-072,REQ-PTY-064,API-PTY-017 -->
### TC-PTY-097 — Credit limit saved while the flag is on
Derived from : AC-PTY-072  (REQ-PTY-064)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit empty; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit 50000 and the stored paymentTermDays
Expected     : 200 · data.creditLimit 50000
Test data    : customerId 5001, creditLimit 50000 (AC)
<!-- TC:TC-PTY-097:END -->

<!-- TC:TC-PTY-098:START traces=AC-PTY-073,REQ-PTY-065,API-PTY-017 -->
### TC-PTY-098 — Payment terms saved while the flag is on
Derived from : AC-PTY-073  (REQ-PTY-065)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with paymentTermDays empty; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with the stored creditLimit and paymentTermDays 45
Expected     : 200 · data.paymentTermDays 45
Test data    : customerId 5001, 45 (AC)
<!-- TC:TC-PTY-098:END -->

<!-- TC:TC-PTY-099:START traces=AC-PTY-076,REQ-PTY-068,API-PTY-035 -->
### TC-PTY-099 — Empty credit limit means NO_CREDIT
Derived from : AC-PTY-076  (REQ-PTY-068)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5002 with creditLimit empty; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5002 and saleAmount 10
Expected     : 200 · data.resultCode NO_CREDIT
Test data    : customerId 5002, saleAmount 10 (AC)
<!-- TC:TC-PTY-099:END -->

<!-- TC:TC-PTY-100:START traces=AC-PTY-077,REQ-PTY-069,API-PTY-030 -->
### TC-PTY-100 — Customer credit list filtered on carries-credit
Derived from : AC-PTY-077  (REQ-PTY-069)
Exercises    : API-PTY-030 GET /api/v1/pty/customer-credits
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; 4 Customers carry a creditLimit or a paymentTermDays and 20 carry neither; the Owner holds PERM_PTY_CUSTOMER_CREDIT_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customer-credits?carriesCredit=true
Expected     : 200 · data.totalElements 4 · data.content holds exactly those 4, each with its creditLimit and paymentTermDays
Test data    : counts 4 / 20 (AC); Customer identities and values are placeholders
<!-- TC:TC-PTY-100:END -->

<!-- TC:TC-PTY-101:START traces=AC-PTY-084,REQ-PTY-075,API-PTY-037 -->
### TC-PTY-101 — Switch-off dependency counts Customers carrying either credit field
Derived from : AC-PTY-084  (REQ-PTY-075)
Exercises    : API-PTY-037 GET /api/v1/pty/flag-dependencies/b2bCredit
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: 3 Customers carry a creditLimit and 1 other Customer carries only a paymentTermDays; caller holds PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/flag-dependencies/b2bCredit
Expected     : 200 · data.dependentCustomerCount 4
Test data    : counts 3 + 1 (AC)
<!-- TC:TC-PTY-101:END -->

<!-- TC:TC-PTY-102:START traces=AC-PTY-086,REQ-PTY-076,API-PTY-037 -->
### TC-PTY-102 — No dependent Customer, no refusal
Derived from : AC-PTY-086  (REQ-PTY-076)
Exercises    : API-PTY-037 GET /api/v1/pty/flag-dependencies/b2bCredit
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; 0 Customers carry credit data; caller holds PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/flag-dependencies/b2bCredit
Expected     : 200 · data.dependentCustomerCount 0 · data.switchOffAllowed true · no RULE-PTY-024 message (messageAr / messageEn null)
Test data    : count 0 (AC)
<!-- TC:TC-PTY-102:END -->

<!-- TC:TC-PTY-103:START traces=AC-PTY-088,REQ-PTY-078,API-PTY-018,API-PTY-037 -->
### TC-PTY-103 — Clear credit empties both fields and lowers the dependency count
Derived from : AC-PTY-088  (REQ-PTY-078)
Exercises    : API-PTY-018 DELETE /api/v1/pty/customer-credits/{customerId} · API-PTY-037 GET …/flag-dependencies/b2bCredit
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000 and paymentTermDays 45; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE and PERM_PTY_CREDIT_SETTINGS_VIEW; the dependency count N is read first
Host data    : none
Steps        : 1. GET …/flag-dependencies/b2bCredit (N) · 2. DELETE /api/v1/pty/customer-credits/5001 ("Clear credit" / «مسح الائتمان») · 3. GET …/flag-dependencies/b2bCredit
Expected     : step 2 → 200, data.creditLimit null, data.paymentTermDays null · step 3 → dependentCustomerCount N − 1
Test data    : customerId 5001, 50000, 45 (AC)
<!-- TC:TC-PTY-103:END -->

<!-- TC:TC-PTY-104:START traces=AC-PTY-089,REQ-PTY-079,API-PTY-035 -->
### TC-PTY-104 — Credit check within the limit
Derived from : AC-PTY-089  (REQ-PTY-079)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; a test contributor reports open exposure 42000; caller (Sales & POS principal) holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 5000
Expected     : 200 · data.creditLimit 50000 · data.openExposure 42000 · data.projectedExposure 47000 · data.resultCode WITHIN_LIMIT
Test data    : values of the AC
<!-- TC:TC-PTY-104:END -->

<!-- TC:TC-PTY-105:START traces=AC-PTY-090,REQ-PTY-080,API-PTY-035 -->
### TC-PTY-105 — Open exposure is the sum of the contributors
Derived from : AC-PTY-090  (REQ-PTY-080)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on (N8); two registered test contributors report 30000 and 12000 for Customer 5001; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount `<saleAmount>`
Expected     : 200 · data.openExposure 42000
Test data    : 30000, 12000 (AC); `<saleAmount>` placeholder (> 0)
<!-- TC:TC-PTY-105:END -->

<!-- TC:TC-PTY-106:START traces=AC-PTY-091,REQ-PTY-080,API-PTY-035 -->
### TC-PTY-106 — No contributor registered means exposure 0
Derived from : AC-PTY-091  (REQ-PTY-080)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: `b2bCredit` on (N8); no `CustomerCreditExposureContributor` registered; Customer 5001; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount `<saleAmount>`
Expected     : 200 · data.openExposure 0
Test data    : customerId 5001 (AC); `<saleAmount>` placeholder (> 0)
<!-- TC:TC-PTY-106:END -->

<!-- TC:TC-PTY-107:START traces=AC-PTY-094,REQ-PTY-083,API-PTY-035 -->
### TC-PTY-107 — Walk-in Customer gets NO_CREDIT with the overrun action
Derived from : AC-PTY-094  (REQ-PTY-083)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; the walk-in Customer 5000 with creditLimit empty; the tenant's credit-overrun action is WARN; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5000 and saleAmount 100
Expected     : 200 · data.resultCode NO_CREDIT · data.overrunActionCode WARN
Test data    : values of the AC
<!-- TC:TC-PTY-107:END -->

<!-- TC:TC-PTY-108:START traces=AC-PTY-096,REQ-PTY-085,API-PTY-016 -->
### TC-PTY-108 — Credit standing shows limit, exposure and available credit
Derived from : AC-PTY-096  (REQ-PTY-085)
Exercises    : API-PTY-016 GET /api/v1/pty/customer-credits/{customerId}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; a test contributor reports 42000; a Seller/Cashier holding PERM_PTY_CUSTOMER_CREDIT_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customer-credits/5001
Expected     : 200 · data.creditLimit 50000 · data.openExposure 42000 · data.availableCredit 8000 · data.exposureAvailable true
Test data    : values of the AC
<!-- TC:TC-PTY-108:END -->

<!-- TC:TC-PTY-109:START traces=AC-PTY-097,REQ-PTY-086,API-PTY-025,API-PTY-024,API-PTY-035 -->
### TC-PTY-109 — Saved overrun action applies to the next credit check
Derived from : AC-PTY-097  (REQ-PTY-086)
Exercises    : API-PTY-025 PUT /api/v1/pty/credit-settings · API-PTY-024 GET /api/v1/pty/credit-settings · API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; tenant setting BLOCK_WITH_OVERRIDE; the Owner holds PERM_PTY_CREDIT_SETTINGS_UPDATE and _VIEW; a Customer `<customerId>` and sale amount `<saleAmount>` whose check is OVER_LIMIT
Host data    : none
Steps        : 1. PUT /api/v1/pty/credit-settings with {"creditOverrunActionCode": "WARN"} · 2. GET /api/v1/pty/credit-settings · 3. POST /api/v1/pty/credit-checks with `<customerId>`, `<saleAmount>`
Expected     : step 1 → 200 · step 2 → creditOverrunActionCode WARN · step 3 → resultCode OVER_LIMIT, overrunActionCode WARN
Test data    : WARN, BLOCK_WITH_OVERRIDE (AC; closed lookup); `<customerId>`, `<saleAmount>` placeholders
<!-- TC:TC-PTY-109:END -->

<!-- TC:TC-PTY-110:START traces=AC-PTY-098,REQ-PTY-087,API-PTY-035,API-PTY-024 -->
### TC-PTY-110 — BLOCK_WITH_OVERRIDE applies while nothing is saved
Derived from : AC-PTY-098  (REQ-PTY-087)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks · API-PTY-024 GET /api/v1/pty/credit-settings
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` just turned on; no tenant setting PTY_CREDIT_OVERRUN_ACTION saved; a Customer `<customerId>` and sale amount `<saleAmount>` whose check is OVER_LIMIT; caller holds PERM_PTY_CUSTOMER_VIEW and PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with `<customerId>`, `<saleAmount>` · 2. GET /api/v1/pty/credit-settings
Expected     : step 1 → resultCode OVER_LIMIT, overrunActionCode BLOCK_WITH_OVERRIDE · step 2 → creditOverrunActionCode BLOCK_WITH_OVERRIDE, isDefault true. The screen part is TC-PTY-133
Test data    : `<customerId>`, `<saleAmount>` placeholders
<!-- TC:TC-PTY-110:END -->

<!-- TC:TC-PTY-111:START traces=AC-PTY-099,REQ-PTY-088,API-PTY-035 -->
### TC-PTY-111 — Under WARN an overrun needs no approval
Derived from : AC-PTY-099  (REQ-PTY-088)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on (N8); credit-overrun action WARN; Customer 5001 with creditLimit 50000; a test contributor reports 49000; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 2000
Expected     : 200 · data.resultCode OVER_LIMIT · data.approvalRequired false
Test data    : values of the AC
<!-- TC:TC-PTY-111:END -->

<!-- TC:TC-PTY-112:START traces=AC-PTY-100,REQ-PTY-089,API-PTY-035 -->
### TC-PTY-112 — Under BLOCK_WITH_OVERRIDE an overrun needs approval
Derived from : AC-PTY-100  (REQ-PTY-089)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on (N8); credit-overrun action BLOCK_WITH_OVERRIDE; Customer 5001 with creditLimit 50000; a test contributor reports 49000; caller holds PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 2000
Expected     : 200 · data.resultCode OVER_LIMIT · data.approvalRequired true
Test data    : values of the AC
<!-- TC:TC-PTY-112:END -->

<!-- TC:TC-PTY-113:START traces=AC-PTY-101,REQ-PTY-090,API-PTY-025,API-PTY-024 -->
### TC-PTY-113 — Overrun setting refused to a user without UPDATE
Derived from : AC-PTY-101  (REQ-PTY-090)
Exercises    : API-PTY-025 PUT /api/v1/pty/credit-settings · API-PTY-024 GET /api/v1/pty/credit-settings
Rule / code  : PLATFORM-STD → PTY-403-FORBIDDEN (ADR-PTY-024)
Package      : SEC-BE
Scenario     : PERMISSION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; tenant setting BLOCK_WITH_OVERRIDE; a Seller/Cashier without PERM_PTY_CREDIT_SETTINGS_UPDATE; an Owner holding PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. as the Seller/Cashier, PUT /api/v1/pty/credit-settings with {"creditOverrunActionCode": "WARN"} · 2. as the Owner, GET /api/v1/pty/credit-settings
Expected     : step 1 → 403 · code PTY-403-FORBIDDEN · messageAr «ليست لديك صلاحية لتنفيذ هذا الإجراء.» · messageEn «You do not have permission to perform this action.» · step 2 → BLOCK_WITH_OVERRIDE
Test data    : values of the AC
<!-- TC:TC-PTY-113:END -->

<!-- TC:TC-PTY-114:START traces=AC-PTY-102,REQ-PTY-091,API-PTY-019 -->
### TC-PTY-114 — Request approval records the credit picture
Derived from : AC-PTY-102  (REQ-PTY-091)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : RULE-PTY-025, RULE-PTY-032, RULE-PTY-033 → — (all satisfied)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; Customer 5001 with creditLimit 50000; a test contributor reports 49000; a Seller/Cashier holding PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. as the Seller/Cashier, POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000455" ("Request approval" / «طلب موافقة»)
Expected     : 201 · data.statusCode PENDING · data.customerFk 5001 · data.saleAmount 2000 · data.creditLimitAtRequest 50000 · data.openExposure 49000 · data.creditCheckResultCode OVER_LIMIT · data.sourceDocumentRef "POS-1-000455" · data.createdBy the Seller/Cashier
Test data    : values of the AC
<!-- TC:TC-PTY-114:END -->

<!-- TC:TC-PTY-115:START traces=AC-PTY-105,REQ-PTY-093,API-PTY-019 -->
### TC-PTY-115 — Approvers notified of a new request
Derived from : AC-PTY-105  (REQ-PTY-093)
Exercises    : API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; exactly 2 active users hold PERM_PTY_CREDIT_OVERRIDE_UPDATE; the series PTY_CREDIT_OVERRIDE returns CO-000031 next; Customer "Nour Trading" `<customerId>` and a sale amount `<saleAmount>` whose check is not WITHIN_LIMIT; calls to the given `NotificationDispatchApi` captured
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-overrides for `<customerId>`, `<saleAmount>`, sourceDocumentRef `<sale reference>` · 2. wait for the commit
Expected     : 201, docNo CO-000031 · after commit, each of the 2 users receives exactly one `NotificationDispatchApi.dispatch` with template PTY_CREDIT_OVERRIDE_REQUESTED, channel IN_APP and variables {docNo CO-000031, name Nour Trading}, which renders ar «طلب تجاوز حد الائتمان CO-000031 للعميل Nour Trading بانتظار موافقتك.» · en «Credit override request CO-000031 for Nour Trading is waiting for your approval.» · no dispatch happens before the commit
Test data    : CO-000031, "Nour Trading", 2 approvers (AC); `<customerId>`, `<saleAmount>`, `<sale reference>` placeholders
<!-- TC:TC-PTY-115:END -->

<!-- TC:TC-PTY-116:START traces=AC-PTY-115,REQ-PTY-101,API-PTY-032 -->
### TC-PTY-116 — Request list puts PENDING first, then newest
Derived from : AC-PTY-115  (REQ-PTY-101)
Exercises    : API-PTY-032 GET /api/v1/pty/credit-overrides
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: requests CO-000031 (APPROVED, docDate 2026-11-02), CO-000035 (PENDING, 2026-11-01), CO-000036 (PENDING, 2026-11-03); an Owner holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE
Host data    : none
Steps        : 1. GET /api/v1/pty/credit-overrides with no filter and no sort
Expected     : 200 · data.content lists CO-000036, CO-000035, CO-000031 in that order
Test data    : values of the AC
<!-- TC:TC-PTY-116:END -->

<!-- TC:TC-PTY-117:START traces=AC-PTY-116,REQ-PTY-102,API-PTY-032 -->
### TC-PTY-117 — Requester without UPDATE sees only own requests
Derived from : AC-PTY-116  (REQ-PTY-102)
Exercises    : API-PTY-032 GET /api/v1/pty/credit-overrides
Rule / code  : — 
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: Seller/Cashier S1 created CO-000035, Seller/Cashier S2 created CO-000036; neither holds PERM_PTY_CREDIT_OVERRIDE_UPDATE; both hold _VIEW
Host data    : none
Steps        : 1. as S1, GET /api/v1/pty/credit-overrides with no filter
Expected     : 200 · data.content lists CO-000035 only
Test data    : values of the AC
<!-- TC:TC-PTY-117:END -->

<!-- TC:TC-PTY-118:START traces=AC-PTY-117,REQ-PTY-103,API-PTY-020 -->
### TC-PTY-118 — Credit override request read by its identifier
Derived from : AC-PTY-117  (REQ-PTY-103)
Exercises    : API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: request CO-000031 for Customer 5001, saleAmount 2000, approved by the Owner at 2026-11-02 10:17; caller (Sales & POS principal) is the request's requester holding PERM_PTY_CREDIT_OVERRIDE_VIEW (N7)
Host data    : none
Steps        : 1. GET /api/v1/pty/credit-overrides/{id of CO-000031}
Expected     : 200 · data.docNo CO-000031 · data.statusCode APPROVED · data.customerFk 5001 · data.saleAmount 2000 · data.decidedBy the Owner's sec user identifier · data.decidedAt 2026-11-02 10:17
Test data    : values of the AC
<!-- TC:TC-PTY-118:END -->

<!-- TC:TC-PTY-119:START traces=AC-PTY-120,REQ-PTY-106,API-PTY-017 -->
### TC-PTY-119 — Credit limit change recorded in the audit
Derived from : AC-PTY-120  (REQ-PTY-106)
Exercises    : API-PTY-017 PUT /api/v1/pty/customer-credits/{customerId}
Rule / code  : RULE-PTY-030 → — (audit recorded)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000; test clock 2026-11-04 09:00; audit rows of entity type PTY_CUSTOMER (the given `@Audited` listener) captured; the Owner holds PERM_PTY_CUSTOMER_CREDIT_UPDATE
Host data    : none
Steps        : 1. PUT /api/v1/pty/customer-credits/5001 with creditLimit 60000 and the stored paymentTermDays
Expected     : 200 · exactly one audit row {entity PTY_CUSTOMER 5001, change creditLimit old 50000 → new 60000, user the Owner, time 2026-11-04 09:00}; no change recorded for the unchanged paymentTermDays
Test data    : values of the AC
<!-- TC:TC-PTY-119:END -->

<!-- TC:TC-PTY-120:START traces=AC-PTY-121,REQ-PTY-107,API-PTY-021 -->
### TC-PTY-120 — Approval recorded in the audit
Derived from : AC-PTY-121  (REQ-PTY-107)
Exercises    : API-PTY-021 PATCH /api/v1/pty/credit-overrides/{id}/approval
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; PENDING request CO-000031 for Customer 5001 with saleAmount 2000; test clock 2026-11-02 10:17; `AuditApi.record` business actions captured; the Owner holds PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. PATCH /api/v1/pty/credit-overrides/{id of CO-000031}/approval
Expected     : 200 · exactly one business audit action APPROVE {docNo CO-000031, Customer 5001, saleAmount 2000, statusCode APPROVED, decider the Owner, time 2026-11-02 10:17} (the entity's own `@Audited` UPDATE row is separate)
Test data    : values of the AC
<!-- TC:TC-PTY-120:END -->

<!-- TC:TC-PTY-121:START traces=AC-PTY-122,REQ-PTY-108,API-PTY-031 -->
### TC-PTY-121 — Credit history newest first
Derived from : AC-PTY-122  (REQ-PTY-108)
Exercises    : API-PTY-031 GET /api/v1/pty/customer-credits/{customerId}/history
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001's creditLimit changed on 2026-10-01 and on 2026-11-04 and its paymentTermDays changed on 2026-10-15 (recorded by the `@Audited` listener of PTY_CUSTOMER through API-PTY-017); the Owner holds PERM_PTY_CUSTOMER_CREDIT_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customer-credits/5001/history
Expected     : 200 · data.content has 3 entries in the order 2026-11-04, 2026-10-15, 2026-10-01, each with field, oldValue, newValue and userName
Test data    : dates of the AC; old/new values are placeholders
<!-- TC:TC-PTY-121:END -->

<!-- TC:TC-PTY-165:START traces=AC-PTY-135,REQ-PTY-079,API-PTY-035,API-PTY-019 -->
### TC-PTY-165 — Credit amounts carry the Customer's currency
Derived from : AC-PTY-135  (REQ-PTY-079)
Exercises    : API-PTY-035 POST /api/v1/pty/credit-checks · API-PTY-019 POST /api/v1/pty/credit-overrides
Rule / code  : — 
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; the tenant setting PTY_BASE_CURRENCY_CODE is EGP; Customer 5001 created after that setting (currencyCode EGP) with creditLimit 50000; a test contributor reports 49000; caller holds PERM_PTY_CUSTOMER_VIEW and PERM_PTY_CREDIT_OVERRIDE_CREATE
Host data    : none
Steps        : 1. POST /api/v1/pty/credit-checks with customerId 5001 and saleAmount 5000 · 2. POST /api/v1/pty/credit-overrides with customerId 5001, saleAmount 5000, sourceDocumentRef `<sale reference>`
Expected     : step 1 → 200, data.currencyCode EGP · step 2 → 201, data.currencyCode EGP
Test data    : EGP, 50000, 5000 (AC); `<sale reference>` placeholder
<!-- TC:TC-PTY-165:END -->

<!-- TC:TC-PTY-166:START traces=AC-PTY-136,REQ-PTY-018,API-PTY-028 -->
### TC-PTY-166 — Supplier search by email finds a Supplier-only Party
Derived from : AC-PTY-136  (REQ-PTY-018)
Exercises    : API-PTY-028 GET /api/v1/pty/suppliers
Rule / code  : — 
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: active Party P-000204 holds email "delta@example.com" and only the Supplier role (Supplier 7004); no other Party holds that email; the Owner holds PERM_PTY_SUPPLIER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/suppliers?email=Delta@Example.com&isActive=true
Expected     : 200 · data.totalElements 1 · data.content[0].supplierPk 7004 · data.content[0].code P-000204 · data.content[0].email "delta@example.com"
Test data    : values of the AC
<!-- TC:TC-PTY-166:END -->

<!-- TC:TC-PTY-167:START traces=AC-PTY-036,REQ-PTY-030,API-PTY-011 -->
### TC-PTY-167 — Either role's UPDATE suffices for the Address of a two-role Party
Derived from : AC-PTY-036  (REQ-PTY-030)
Exercises    : API-PTY-011 POST /api/v1/pty/parties/{partyId}/addresses
Rule / code  : — (authority — ADR-PTY-039)
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: Party P-000130 holds a Customer and a Supplier; user U1 holds PERM_PTY_SUPPLIER_VIEW and PERM_PTY_SUPPLIER_UPDATE only (custom role); user U2 holds PERM_PTY_SUPPLIER_UPDATE only and Party P-000120 holds a Customer only
Host data    : none
Steps        : 1. as U1, POST /api/v1/pty/parties/{identifier of P-000130}/addresses with addressTypeCode DELIVERY and line1 "12 Tahrir St" · 2. as U2, POST /api/v1/pty/parties/{identifier of P-000120}/addresses with the same body
Expected     : step 1 → 201 (the Supplier role's UPDATE is enough) · step 2 → 403 · code PTY-403-FORBIDDEN · messageAr «ليست لديك صلاحية لتنفيذ هذا الإجراء.» · messageEn «You do not have permission to perform this action.» (P-000120 holds no Supplier)
Test data    : DELIVERY, "12 Tahrir St" (AC); users test-local
<!-- TC:TC-PTY-167:END -->

<!-- TC:TC-PTY-177:START traces=AC-PTY-003,REQ-PTY-002,API-PTY-002 -->
### TC-PTY-177 — An in-process caller without the PTY authority is refused
Derived from : AC-PTY-003  (REQ-PTY-002)
Exercises    : API-PTY-002 through the in-process `PtyPartyApi.readCustomer` (same service method — ADR-PTY-039)
Rule / code  : PLATFORM-STD → PTY-403-FORBIDDEN
Package      : SEC-BE
Scenario     : PERMISSION · data class INVALID · language ALL
Preconditions: Customer 5001; a security context holding no PTY permission (a CUSTOMER-realm principal); a second context holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. under the first context, call `PtyPartyApi.readCustomer(5001, false)` · 2. under the second context, the same call
Expected     : step 1 → `LocalizedException` with code PTY-403-FORBIDDEN · step 2 → the Customer 5001 (code P-000120)
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-177:END -->
<!-- TC:TC-PTY-196:START traces=AC-PTY-138,REQ-PTY-002,API-PTY-002,API-PTY-016 -->
### TC-PTY-196 — Customer VIEW without Customer credit VIEW reads the credit values, not the standing
Derived from : AC-PTY-138  (REQ-PTY-002)
Exercises    : API-PTY-002 GET /api/v1/pty/customers/{id} · API-PTY-016 GET /api/v1/pty/customer-credits/{customerId}
Rule / code  : PLATFORM-STD → PTY-403-FORBIDDEN (ADR-PTY-046)
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: `b2bCredit` on; Customer 5001 with creditLimit 50000, paymentTermDays 45 and currencyCode EGP; user U3 of a custom role holding PERM_PTY_CUSTOMER_VIEW only (no permission on PTY_CUSTOMER_CREDIT)
Host data    : none
Steps        : 1. as U3, GET /api/v1/pty/customers/5001 · 2. as U3, GET /api/v1/pty/customer-credits/5001
Expected     : step 1 → 200 · data.creditLimit 50000 · data.paymentTermDays 45 · data.currencyCode EGP · step 2 → 403 · code PTY-403-FORBIDDEN · messageAr «ليست لديك صلاحية لتنفيذ هذا الإجراء.» · messageEn «You do not have permission to perform this action.»
Test data    : values of the AC; U3 test-local
<!-- TC:TC-PTY-196:END -->

<!-- TC:TC-PTY-197:START traces=AC-PTY-139,REQ-PTY-041,API-PTY-027 -->
### TC-PTY-197 — Global search term with spaces and dashes matches a mobile; `%` matches literally
Derived from : AC-PTY-139  (REQ-PTY-041)
Exercises    : API-PTY-027 GET /api/v1/pty/customers/global-search
Rule / code  : — (QR-PTY-011 parameters `:tDigits` and the escaping of `%` / `_`)
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: an active Customer `<customerId>` with mobile "+20 100 111 2222"; no Customer whose code, names, mobile, email or tax number contains "%%"; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW
Host data    : none
Steps        : 1. GET /api/v1/pty/customers/global-search?term=100 111-2222 · 2. GET /api/v1/pty/customers/global-search?term=%25%25 (the term "%%")
Expected     : step 1 → 200 · that Customer is in data, with isWalkInFl false · step 2 → 200 · data is an empty list (the two `%` match only a literal "%%", never every row)
Test data    : mobile "+20 100 111 2222", term "100 111-2222" (AC); "%%" test-local
<!-- TC:TC-PTY-197:END -->

<!-- TC:TC-PTY-199:START traces=AC-PTY-116,REQ-PTY-102,REQ-PTY-103,API-PTY-020 -->
### TC-PTY-199 — A requester without UPDATE cannot read another user's request
Derived from : AC-PTY-116  (REQ-PTY-102)
Exercises    : API-PTY-020 GET /api/v1/pty/credit-overrides/{id}
Rule / code  : PLATFORM-STD → PTY-404-CREDIT-OVERRIDE-NOT-FOUND (visibility — REQ-PTY-102 applied to reads, ADR-PTY-039)
Package      : SEC-BE
Scenario     : PERMISSION · data class INVALID · language ALL
Preconditions: Seller/Cashier S1 created CO-000035 and Seller/Cashier S2 created CO-000036; both hold PERM_PTY_CREDIT_OVERRIDE_VIEW and neither holds PERM_PTY_CREDIT_OVERRIDE_UPDATE
Host data    : none
Steps        : 1. as S1, GET /api/v1/pty/credit-overrides/{id of CO-000036} · 2. as S1, GET /api/v1/pty/credit-overrides/{id of CO-000035}
Expected     : step 1 → 404 · code PTY-404-CREDIT-OVERRIDE-NOT-FOUND · messageAr «طلب تجاوز حد الائتمان غير موجود.» · messageEn «The credit override request was not found.» (the request's existence is not revealed) · step 2 → 200, data.docNo CO-000035
Test data    : values of the AC
<!-- TC:TC-PTY-199:END -->

<!-- TC:TC-PTY-200:START traces=AC-PTY-045,REQ-PTY-039,API-PTY-038 -->
### TC-PTY-200 — PTY serves its own lookup key with the active values only
Derived from : AC-PTY-045  (REQ-PTY-039)
Exercises    : API-PTY-038 GET /api/v1/pty/lookups/{key}
Rule / code  : PLATFORM-STD → PTY-404-LOOKUP-KEY-UNKNOWN (OwnedLookups — ADR-PTY-048)
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: lookup CUSTOMER_GROUP holds RETAIL and WHOLESALE active (seeded) and VIP inactive; a Seller/Cashier holding PERM_PTY_CUSTOMER_VIEW
Host data    : CUSTOMER_GROUP VIP — created active by the given mdl call POST /api/v1/mdl/lookup-types/{id}/values ({id} = the CUSTOMER_GROUP type found by POST /api/v1/mdl/lookup-types/search), then made inactive by DELETE /api/v1/mdl/lookup-values/{id} ({id} = the VIP value) — the same state as TC-PTY-020 (N4)
Steps        : 1. GET /api/v1/pty/lookups/CUSTOMER_GROUP · 2. GET /api/v1/pty/lookups/CAT_BRAND (a key another module owns)
Expected     : step 1 → 200 · data holds exactly RETAIL (labelAr «تجزئة», labelEn "Retail") and WHOLESALE (labelAr «جملة», labelEn "Wholesale"), in sortOrder; VIP is absent · step 2 → 404 · code PTY-404-LOOKUP-KEY-UNKNOWN · messageAr «هذه القائمة ليست من قوائم الأطراف. اختر إحدى قوائم الأطراف.» · messageEn «This list is not one of the party lists. Choose a party list.»
Test data    : RETAIL, WHOLESALE, VIP (SRS A6 `values`); CAT_BRAND test-local
<!-- TC:TC-PTY-200:END -->
<!-- SUB:API-SCENARIOS:END -->
<!-- PHASE:TEST-PLAN-BE:END -->

`INT-XM` is absent: the XM register of PTY v1 is `records: []` and the last phase of the backend plan (INT-C) carries no `XM-*` block — there is no edge to test.

## TC TRACEABILITY INDEX

### AC → TC · REQ → TC

| AC | REQ | TC | Scenario | Package |
|---|---|---|---|---|
| AC-PTY-001 | REQ-PTY-001 | TC-PTY-054 | HAPPY | SVC-API-SEARCH |
| AC-PTY-002 | REQ-PTY-001 | TC-PTY-055 | HAPPY | SVC-API-CRUD |
| AC-PTY-003 | REQ-PTY-002 | TC-PTY-056 (+ TC-PTY-177 in-process authority, TC-PTY-178 other tenant) | HAPPY | SVC-API-CRUD |
| AC-PTY-004 | REQ-PTY-003 | TC-PTY-001 | VIOLATION | SVC-API-CRUD |
| AC-PTY-005 | REQ-PTY-003 | TC-PTY-002 | VIOLATION | SVC-API-CRUD |
| AC-PTY-006 | REQ-PTY-004 | TC-PTY-057 | HAPPY | SVC-API-CRUD |
| AC-PTY-007 | REQ-PTY-005 | TC-PTY-058 (+ frontend TC-PTY-122) | HAPPY | SVC-API-CRUD |
| AC-PTY-008 | REQ-PTY-006 | TC-PTY-003 | STATE | CORE |
| AC-PTY-009 | REQ-PTY-007 | TC-PTY-059 | HAPPY | SVC-API-CRUD |
| AC-PTY-010 | REQ-PTY-008 | TC-PTY-060 (+ frontend TC-PTY-146) | HAPPY | SVC-API-CRUD |
| AC-PTY-011 | REQ-PTY-008 | TC-PTY-061 (+ frontend TC-PTY-147) | HAPPY | SVC-API-CRUD |
| AC-PTY-012 | REQ-PTY-009 | TC-PTY-004 | VIOLATION | SVC-API-CRUD |
| AC-PTY-013 | REQ-PTY-010 | TC-PTY-005 (+ TC-PTY-172 email race; frontend TC-PTY-123, 179) | VIOLATION | SVC-API-CRUD |
| AC-PTY-014 | REQ-PTY-011 | TC-PTY-006 | VIOLATION | SVC-API-CRUD |
| AC-PTY-015 | REQ-PTY-011 | TC-PTY-007 | VIOLATION | SVC-API-CRUD |
| AC-PTY-016 | REQ-PTY-012 | TC-PTY-062 | HAPPY | SVC-API-CRUD |
| AC-PTY-017 | REQ-PTY-012 | TC-PTY-008 | VIOLATION | SVC-API-CRUD |
| AC-PTY-018 | REQ-PTY-013 | TC-PTY-063 | HAPPY | SVC-API-SEARCH |
| AC-PTY-019 | REQ-PTY-014 | TC-PTY-064 | HAPPY | SVC-API-CRUD |
| AC-PTY-020 | REQ-PTY-015 | TC-PTY-065 | HAPPY | SVC-API-CRUD |
| AC-PTY-021 | REQ-PTY-016 | TC-PTY-066 (+ frontend TC-PTY-124) | HAPPY | SVC-API-CRUD |
| AC-PTY-022 | REQ-PTY-017 | TC-PTY-067 | HAPPY | SVC-API-CRUD |
| AC-PTY-023 | REQ-PTY-018 | TC-PTY-068 | HAPPY | SVC-API-SEARCH |
| AC-PTY-024 | REQ-PTY-019 | TC-PTY-069 | HAPPY | SVC-API-CRUD |
| AC-PTY-025 | REQ-PTY-020 | TC-PTY-070 | HAPPY | SVC-API-CRUD |
| AC-PTY-026 | REQ-PTY-021 | TC-PTY-071 | HAPPY | SVC-API-CRUD |
| AC-PTY-027 | REQ-PTY-022 | TC-PTY-009 | VIOLATION | SVC-API-CRUD |
| AC-PTY-028 | REQ-PTY-023 | TC-PTY-072 | HAPPY | SVC-API-CRUD |
| AC-PTY-029 | REQ-PTY-024 | TC-PTY-010 (+ frontend TC-PTY-148) | STATE | DATA-DOM-MASTER |
| AC-PTY-030 | REQ-PTY-025 | TC-PTY-073 | HAPPY | SVC-API-CRUD |
| AC-PTY-031 | REQ-PTY-026 | TC-PTY-074 (+ frontend TC-PTY-149) | HAPPY | SVC-API-CRUD |
| AC-PTY-032 | REQ-PTY-027 | TC-PTY-011 | VIOLATION | SVC-API-CRUD |
| AC-PTY-033 | REQ-PTY-028 | TC-PTY-012 (+ TC-PTY-173 concurrent role deactivations) | STATE | DATA-DOM-MASTER |
| AC-PTY-034 | REQ-PTY-029 | TC-PTY-013 | STATE | DATA-DOM-MASTER |
| AC-PTY-035 | REQ-PTY-029 | TC-PTY-014 | VIOLATION | SVC-API-CRUD |
| AC-PTY-036 | REQ-PTY-030 | TC-PTY-075 (+ TC-PTY-167 either role's UPDATE; TC-PTY-195 type change vs create) | HAPPY | SVC-API-CRUD |
| AC-PTY-037 | REQ-PTY-031 | TC-PTY-076 | HAPPY | SVC-API-CRUD |
| AC-PTY-038 | REQ-PTY-032 | TC-PTY-077 | HAPPY | SVC-API-SEARCH |
| AC-PTY-039 | REQ-PTY-033 | TC-PTY-078 | HAPPY | SVC-API-CRUD |
| AC-PTY-040 | REQ-PTY-034 | TC-PTY-015 (+ TC-PTY-174 concurrent Address creates) | STATE | DATA-DOM-MASTER |
| AC-PTY-041 | REQ-PTY-035 | TC-PTY-079 | HAPPY | SVC-API-CRUD |
| AC-PTY-042 | REQ-PTY-036 | TC-PTY-016 | STATE | DATA-DOM-MASTER |
| AC-PTY-043 | REQ-PTY-037 | TC-PTY-017 | VIOLATION | SVC-API-CRUD |
| AC-PTY-044 | REQ-PTY-038 | TC-PTY-018 | VIOLATION | SVC-API-CRUD |
| AC-PTY-045 | REQ-PTY-039 | TC-PTY-019 (+ TC-PTY-200 lookup read) | STATE | DATA-DOM-MASTER |
| AC-PTY-046 | REQ-PTY-040 | TC-PTY-020 | VIOLATION | SVC-API-CRUD |
| AC-PTY-047 | REQ-PTY-041 | TC-PTY-080 | HAPPY | SVC-API-SEARCH |
| AC-PTY-048 | REQ-PTY-041 | TC-PTY-081 | HAPPY | SVC-API-SEARCH |
| AC-PTY-049 | REQ-PTY-041 | TC-PTY-021 | BOUNDARY | SVC-API-SEARCH |
| AC-PTY-050 | REQ-PTY-042 | TC-PTY-082 | HAPPY | SVC-API-SEARCH |
| AC-PTY-051 | REQ-PTY-043 | TC-PTY-083 | PERMISSION | SEC-BE |
| AC-PTY-052 | REQ-PTY-044 | TC-PTY-022 | STATE | DATA-DOM-MASTER |
| AC-PTY-053 | REQ-PTY-045 | TC-PTY-084 | HAPPY | SVC-API-INT |
| AC-PTY-054 | REQ-PTY-046 | TC-PTY-085 (+ TC-PTY-171 walk-in race) | HAPPY | SVC-API-INT |
| AC-PTY-055 | REQ-PTY-047 | TC-PTY-023 (+ frontend TC-PTY-125) | VIOLATION | SVC-API-CRUD |
| AC-PTY-056 | REQ-PTY-048 | TC-PTY-024 (+ frontend TC-PTY-126) | VIOLATION | SVC-API-CRUD |
| AC-PTY-057 | REQ-PTY-049 | TC-PTY-025 (+ frontend TC-PTY-127) | VIOLATION | SVC-API-CRUD |
| AC-PTY-058 | REQ-PTY-050 | TC-PTY-086 | HAPPY | SVC-API-INT |
| AC-PTY-059 | REQ-PTY-051 | TC-PTY-087 | HAPPY | SVC-API-INT |
| AC-PTY-060 | REQ-PTY-052 | TC-PTY-088 | HAPPY | SVC-API-INT |
| AC-PTY-061 | REQ-PTY-053 | TC-PTY-026 | VIOLATION | SVC-API-INT |
| AC-PTY-062 | REQ-PTY-054 | TC-PTY-027 | VIOLATION | SVC-API-INT |
| AC-PTY-063 | REQ-PTY-055 | TC-PTY-089 (+ TC-PTY-194 guest email race) | HAPPY | SVC-API-INT |
| AC-PTY-064 | REQ-PTY-056 | TC-PTY-090 | HAPPY | SVC-API-INT |
| AC-PTY-065 | REQ-PTY-057 | TC-PTY-091 | HAPPY | SVC-API-INT |
| AC-PTY-066 | REQ-PTY-058 | TC-PTY-092 | HAPPY | SVC-API-INT |
| AC-PTY-067 | REQ-PTY-059 | TC-PTY-093 | HAPPY | SVC-API-INT |
| AC-PTY-068 | REQ-PTY-060 | TC-PTY-094 | HAPPY | SVC-API-INT |
| AC-PTY-069 | REQ-PTY-061 | TC-PTY-095 | HAPPY | SVC-API-INT |
| AC-PTY-070 | REQ-PTY-062 | TC-PTY-028 | VIOLATION | SVC-API-INT |
| AC-PTY-071 | REQ-PTY-063 | TC-PTY-096 | HAPPY | SVC-API-INT |
| AC-PTY-072 | REQ-PTY-064 | TC-PTY-097 | HAPPY | SVC-API-CRUD |
| AC-PTY-073 | REQ-PTY-065 | TC-PTY-098 | HAPPY | SVC-API-CRUD |
| AC-PTY-074 | REQ-PTY-066 | TC-PTY-029 | VIOLATION | SVC-API-CRUD |
| AC-PTY-075 | REQ-PTY-067 | TC-PTY-030 | BOUNDARY | SVC-API-CRUD |
| AC-PTY-076 | REQ-PTY-068 | TC-PTY-099 | HAPPY | SVC-API-INT |
| AC-PTY-077 | REQ-PTY-069 | TC-PTY-100 (+ frontend TC-PTY-128) | HAPPY | SVC-API-SEARCH |
| AC-PTY-078 | REQ-PTY-070 | frontend TC-PTY-129 (N1) | — | — |
| AC-PTY-079 | REQ-PTY-071 | TC-PTY-031 | STATE | SVC-API-CRUD |
| AC-PTY-080 | REQ-PTY-072 | TC-PTY-032 | VIOLATION | SVC-API-CRUD |
| AC-PTY-081 | REQ-PTY-072 | TC-PTY-033 | VIOLATION | SVC-API-CRUD |
| AC-PTY-082 | REQ-PTY-073 | TC-PTY-034 (+ frontend TC-PTY-150) | STATE | CORE |
| AC-PTY-083 | REQ-PTY-074 | TC-PTY-035 | STATE | SVC-API-INT |
| AC-PTY-084 | REQ-PTY-075 | TC-PTY-101 | HAPPY | SVC-API-INT |
| AC-PTY-085 | REQ-PTY-076 | TC-PTY-036 | VIOLATION | SVC-API-INT |
| AC-PTY-086 | REQ-PTY-076 | TC-PTY-102 | HAPPY | SVC-API-INT |
| AC-PTY-087 | REQ-PTY-077 | frontend TC-PTY-151 (N1) | — | — |
| AC-PTY-088 | REQ-PTY-078 | TC-PTY-103 (+ frontend TC-PTY-130) | HAPPY | SVC-API-CRUD |
| AC-PTY-089 | REQ-PTY-079 | TC-PTY-104 | HAPPY | SVC-API-INT |
| AC-PTY-090 | REQ-PTY-080 | TC-PTY-105 | HAPPY | SVC-API-INT |
| AC-PTY-091 | REQ-PTY-080 | TC-PTY-106 | HAPPY | SVC-API-INT |
| AC-PTY-092 | REQ-PTY-081 | TC-PTY-037 | BOUNDARY | SVC-API-INT |
| AC-PTY-093 | REQ-PTY-082 | TC-PTY-038 | BOUNDARY | SVC-API-INT |
| AC-PTY-094 | REQ-PTY-083 | TC-PTY-107 | HAPPY | SVC-API-INT |
| AC-PTY-095 | REQ-PTY-084 | TC-PTY-039 | BOUNDARY | SVC-API-INT |
| AC-PTY-096 | REQ-PTY-085 | TC-PTY-108 (+ frontend TC-PTY-131) | HAPPY | SVC-API-CRUD |
| AC-PTY-097 | REQ-PTY-086 | TC-PTY-109 (+ frontend TC-PTY-132) | HAPPY | SVC-API-CRUD |
| AC-PTY-098 | REQ-PTY-087 | TC-PTY-110 (+ frontend TC-PTY-133) | HAPPY | SVC-API-INT |
| AC-PTY-099 | REQ-PTY-088 | TC-PTY-111 | HAPPY | SVC-API-INT |
| AC-PTY-100 | REQ-PTY-089 | TC-PTY-112 | HAPPY | SVC-API-INT |
| AC-PTY-101 | REQ-PTY-090 | TC-PTY-113 (+ frontend TC-PTY-134) | PERMISSION | SEC-BE |
| AC-PTY-102 | REQ-PTY-091 | TC-PTY-114 (+ frontend TC-PTY-135) | HAPPY | SVC-API-CRUD |
| AC-PTY-103 | REQ-PTY-091 | TC-PTY-040 | BOUNDARY | SVC-API-CRUD |
| AC-PTY-104 | REQ-PTY-092 | TC-PTY-041 | STATE | DATA-DOM-TRANSACTIONAL |
| AC-PTY-105 | REQ-PTY-093 | TC-PTY-115 | HAPPY | SVC-API-CRUD |
| AC-PTY-106 | REQ-PTY-094 | TC-PTY-042 (+ frontend TC-PTY-136) | STATE | DATA-DOM-TRANSACTIONAL |
| AC-PTY-107 | REQ-PTY-095 | TC-PTY-043 (+ frontend TC-PTY-137) | STATE | DATA-DOM-TRANSACTIONAL |
| AC-PTY-108 | REQ-PTY-096 | TC-PTY-044 (+ TC-PTY-169 approve vs cancel) | STATE | DATA-DOM-TRANSACTIONAL |
| AC-PTY-109 | REQ-PTY-096 | TC-PTY-045 (+ frontend TC-PTY-138) | VIOLATION | SVC-API-CRUD |
| AC-PTY-110 | REQ-PTY-097 | TC-PTY-046 | STATE | DATA-DOM-TRANSACTIONAL |
| AC-PTY-111 | REQ-PTY-098 | TC-PTY-047 (+ TC-PTY-168 approve vs approve; frontend TC-PTY-139) | VIOLATION | SVC-API-CRUD |
| AC-PTY-112 | REQ-PTY-099 | TC-PTY-048 | VIOLATION | SVC-API-INT |
| AC-PTY-113 | REQ-PTY-099 | TC-PTY-049 (+ TC-PTY-170 double use) | VIOLATION | SVC-API-INT |
| AC-PTY-114 | REQ-PTY-100 | TC-PTY-050 (+ frontend TC-PTY-140) | VIOLATION | SVC-API-CRUD |
| AC-PTY-115 | REQ-PTY-101 | TC-PTY-116 (+ frontend TC-PTY-141) | HAPPY | SVC-API-SEARCH |
| AC-PTY-116 | REQ-PTY-102 | TC-PTY-117 (+ TC-PTY-199 read visibility; frontend TC-PTY-142) | PERMISSION | SEC-BE |
| AC-PTY-117 | REQ-PTY-103 | TC-PTY-118 (+ frontend TC-PTY-143) | HAPPY | SVC-API-CRUD |
| AC-PTY-118 | REQ-PTY-104 | TC-PTY-051 | VIOLATION | SVC-API-CRUD |
| AC-PTY-119 | REQ-PTY-105 | TC-PTY-052 | VIOLATION | SVC-API-CRUD |
| AC-PTY-120 | REQ-PTY-106 | TC-PTY-119 (+ TC-PTY-175 audit chaining) | HAPPY | SVC-API-CRUD |
| AC-PTY-121 | REQ-PTY-107 | TC-PTY-120 | HAPPY | SVC-API-CRUD |
| AC-PTY-122 | REQ-PTY-108 | TC-PTY-121 (+ frontend TC-PTY-144) | HAPPY | SVC-API-SEARCH |
| AC-PTY-123 | REQ-PTY-109 | TC-PTY-053 (+ frontend TC-PTY-145) | VIOLATION | SVC-API-CRUD |
| AC-PTY-124 | REQ-PTY-110 | TC-PTY-154 | VIOLATION | SVC-API-INT |
| AC-PTY-125 | REQ-PTY-110 | TC-PTY-155 | VIOLATION | SVC-API-CRUD |
| AC-PTY-126 | REQ-PTY-110 | TC-PTY-156 | VIOLATION | SVC-API-CRUD |
| AC-PTY-127 | REQ-PTY-110 | TC-PTY-157 | VIOLATION | SVC-API-CRUD |
| AC-PTY-128 | REQ-PTY-111 | TC-PTY-158 (+ TC-PTY-192 deactivation vs creation) | STATE | DATA-DOM-TRANSACTIONAL |
| AC-PTY-129 | REQ-PTY-111 | TC-PTY-159 (+ TC-PTY-193 deactivation vs approval and use) | VIOLATION | SVC-API-INT |
| AC-PTY-130 | REQ-PTY-112 | TC-PTY-160 | VIOLATION | SVC-API-CRUD |
| AC-PTY-131 | REQ-PTY-112 | TC-PTY-161 | STATE | DATA-DOM-TRANSACTIONAL |
| AC-PTY-132 | REQ-PTY-113 | TC-PTY-162 (+ TC-PTY-176 open-request race; frontend TC-PTY-189) | VIOLATION | SVC-API-CRUD |
| AC-PTY-133 | REQ-PTY-099 | TC-PTY-163 | VIOLATION | SVC-API-INT |
| AC-PTY-134 | REQ-PTY-109 | TC-PTY-164 (+ frontend TC-PTY-190) | VIOLATION | SVC-API-CRUD |
| AC-PTY-135 | REQ-PTY-079 | TC-PTY-165 | HAPPY | SVC-API-INT |
| AC-PTY-136 | REQ-PTY-018 | TC-PTY-166 | HAPPY | SVC-API-SEARCH |
| AC-PTY-137 | REQ-PTY-114 | TC-PTY-191 | VIOLATION | SVC-API-CRUD |
| AC-PTY-138 | REQ-PTY-002 | TC-PTY-196 | PERMISSION | SEC-BE |
| AC-PTY-139 | REQ-PTY-041 | TC-PTY-197 | HAPPY | SVC-API-SEARCH |
| AC-PTY-140 | REQ-PTY-110 | TC-PTY-198 | VIOLATION | SVC-API-INT |

### API → TC

| API | Operation | TC |
|---|---|---|
| API-PTY-001 | create Customer | TC-PTY-003, 004, 005, 006, 057, 058, 059, 060, 074, 172 |
| API-PTY-002 | read Customer | TC-PTY-001, 010, 019, 023, 031, 034, 055, 056, 064, 086, 092, 177, 178, 196 |
| API-PTY-003 | update Customer | TC-PTY-008, 019, 020, 062 |
| API-PTY-004 | deactivate Customer | TC-PTY-012, 023, 064, 072, 158, 173, 192, 193 |
| API-PTY-005 | reactivate Customer | TC-PTY-013, 014, 065 |
| API-PTY-006 | create Supplier | TC-PTY-007, 011, 025, 061, 066, 073 |
| API-PTY-007 | read Supplier | TC-PTY-002, 069, 070 |
| API-PTY-008 | update Supplier | TC-PTY-009, 010, 067 |
| API-PTY-009 | deactivate Supplier | TC-PTY-070, 173 |
| API-PTY-010 | reactivate Supplier | TC-PTY-071 |
| API-PTY-011 | create Address | TC-PTY-015, 017, 018, 075, 156, 167, 174, 195 |
| API-PTY-012 | read Address | TC-PTY-078 |
| API-PTY-013 | update Address | TC-PTY-076, 195 |
| API-PTY-014 | deactivate Address | TC-PTY-016, 078 |
| API-PTY-015 | set default Address | TC-PTY-079 |
| API-PTY-016 | read credit standing | TC-PTY-034, 053, 108, 196 |
| API-PTY-017 | update credit | TC-PTY-024, 029, 030, 032, 053, 097, 098, 119, 157, 175 |
| API-PTY-018 | clear credit | TC-PTY-103, 157 |
| API-PTY-019 | create Credit override request | TC-PTY-033, 040, 041, 051, 052, 114, 115, 155, 162, 165, 176, 191, 192 |
| API-PTY-020 | read Credit override request | TC-PTY-041, 118, 158, 164, 199 |
| API-PTY-021 | approve | TC-PTY-041, 042, 047, 120, 160, 164, 168, 169, 193 |
| API-PTY-022 | reject | TC-PTY-043, 050 |
| API-PTY-023 | cancel | TC-PTY-044, 045, 161, 169 |
| API-PTY-024 | read credit settings | TC-PTY-109, 110, 113 |
| API-PTY-025 | update credit settings | TC-PTY-109, 113 |
| API-PTY-026 | search Customers | TC-PTY-054, 055, 063, 065 |
| API-PTY-027 | Customer global search | TC-PTY-021, 080, 081, 082, 083, 197 |
| API-PTY-028 | search Suppliers | TC-PTY-068, 166 |
| API-PTY-029 | list Party addresses | TC-PTY-016, 077, 078, 079, 086, 092 |
| API-PTY-030 | search Customer credit | TC-PTY-100 |
| API-PTY-031 | read credit history | TC-PTY-121 |
| API-PTY-032 | search Credit override requests | TC-PTY-116, 117 |
| API-PTY-033 | walk-in Customer | TC-PTY-022, 084, 085, 171 |
| API-PTY-034 | guest order customer resolution | TC-PTY-026, 028, 055, 086, 087, 089, 090, 091, 092, 093, 094, 095, 096, 194 |
| API-PTY-035 | credit check | TC-PTY-035, 037, 038, 039, 099, 104, 105, 106, 107, 109, 110, 111, 112, 154, 165, 198 |
| API-PTY-036 | use Credit override request | TC-PTY-046, 048, 049, 159, 163, 170, 193 |
| API-PTY-037 | `b2bCredit` switch-off dependency | TC-PTY-036, 101, 102, 103 |
| API-PTY-038 | read a PTY lookup key | TC-PTY-200 |
| — (outbox subscriber `PtyCustomerInvitationRedeemedSubscriber`) | redemption of a CUSTOMER-realm invitation | TC-PTY-027, 088 |

### RULE / code → TC

| RULE | Catalog code | TC |
|---|---|---|
| RULE-PTY-001 | PTY-422-NAME-REQUIRED | TC-PTY-004 |
| RULE-PTY-002 | PTY-409-EMAIL-TAKEN | TC-PTY-005, 014, 172 |
| RULE-PTY-003 | PTY-422-EMAIL-FORMAT | TC-PTY-006 |
| RULE-PTY-004 | PTY-422-MOBILE-FORMAT | TC-PTY-007 |
| RULE-PTY-005 | PTY-422-ROLE-INACTIVE | TC-PTY-001, 002, 154, 155, 156, 157, 192, 198 |
| RULE-PTY-006 | confirmation (no code) | TC-PTY-064, 070, 072, 078 |
| RULE-PTY-007 | PTY-422-CODE-READ-ONLY (assignment: TC-PTY-003) | TC-PTY-008 |
| RULE-PTY-008 | PTY-422-PAYMENT-TERMS-RANGE | TC-PTY-009, 030 |
| RULE-PTY-009 | PTY-409-ROLE-HELD | TC-PTY-011 |
| RULE-PTY-010 | PTY-422-WALK-IN-DEACTIVATE | TC-PTY-023 |
| RULE-PTY-011 | PTY-422-WALK-IN-CREDIT | TC-PTY-024 |
| RULE-PTY-012 | PTY-422-WALK-IN-SUPPLIER | TC-PTY-025 |
| RULE-PTY-013 | matching filter / handler outcome (no code) | TC-PTY-096 |
| RULE-PTY-014 | idempotent ensure (no code) | TC-PTY-022, 085 |
| RULE-PTY-015 | PTY-422-ADDRESS-LINE-REQUIRED | TC-PTY-017 |
| RULE-PTY-016 | PTY-422-ADDRESS-TYPE-INVALID | TC-PTY-018 |
| RULE-PTY-017 | confirmation (no code) | TC-PTY-015, 016, 079 |
| RULE-PTY-018 | PTY-422-CUSTOMER-GROUP-INVALID | TC-PTY-020 (valid value: TC-PTY-019) |
| RULE-PTY-019 | PTY-422-GUEST-CONTACT-REQUIRED | TC-PTY-026 |
| RULE-PTY-020 | handler outcome REFUSED (no code) | TC-PTY-027 |
| RULE-PTY-021 | PTY-422-GUEST-NOT-COMPLETED | TC-PTY-028 |
| RULE-PTY-022 | PTY-422-CREDIT-LIMIT-NEGATIVE | TC-PTY-029 |
| RULE-PTY-023 | PTY-422-CREDIT-NOT-ENABLED | TC-PTY-032, 033, 160 (cancel allowed: TC-PTY-161) |
| RULE-PTY-024 | answer message (no code) | TC-PTY-036 |
| RULE-PTY-025 | PTY-422-OVERRIDE-NOT-NEEDED | TC-PTY-051 |
| RULE-PTY-026 | PTY-409-REQUEST-NOT-PENDING | TC-PTY-047, 168, 169, 193 |
| RULE-PTY-027 | PTY-422-REJECTION-REASON-REQUIRED | TC-PTY-050 |
| RULE-PTY-028 | PTY-422-APPROVAL-NOT-COVERING | TC-PTY-048, 049, 159, 163, 170, 193 |
| RULE-PTY-029 | PTY-403-NOT-REQUESTER | TC-PTY-045 |
| RULE-PTY-030 | PTY-500-AUDIT-NOT-RECORDED | TC-PTY-053, 164 (recorded: TC-PTY-119, 120, 175) |
| RULE-PTY-031 | PTY-422-DOC-NO-READ-ONLY | TC-PTY-041 |
| RULE-PTY-032 | PTY-422-SALE-AMOUNT-NOT-POSITIVE | TC-PTY-040 |
| RULE-PTY-033 | PTY-422-WALK-IN-NO-CREDIT-SALE | TC-PTY-052 |
| RULE-PTY-034 | PTY-409-REQUEST-OPEN-FOR-SALE | TC-PTY-162, 176 |
| RULE-PTY-035 | PTY-422-OVERRIDE-NOT-REQUIRED | TC-PTY-191 |
| PLATFORM-STD (ADR-PTY-024) | PTY-403-FORBIDDEN | TC-PTY-113, 167, 177, 196 |
| PLATFORM-STD (ADR-PTY-024) | PTY-404-CUSTOMER-NOT-FOUND (other tenant; unknown Customer) | TC-PTY-178, 198 |
| PLATFORM-STD (ADR-PTY-024) | PTY-404-CREDIT-OVERRIDE-NOT-FOUND (another user's request) | TC-PTY-199 |
| PLATFORM-STD (ADR-PTY-048) | PTY-404-LOOKUP-KEY-UNKNOWN | TC-PTY-200 |

### XM → TC

None — PTY v1 declares no `XM-*` edge (registry-db `xm-register: records: []`; backend plan INT-C carries no block).

### Package → TC

| Package (backend plan unit) | TC | Count |
|---|---|---|
| CORE | TC-PTY-003, 034 | 2 |
| DATA-DOM-MASTER | TC-PTY-010, 012, 013, 015, 016, 019, 022, 173, 174, 195 | 10 |
| DATA-DOM-TRANSACTIONAL | TC-PTY-041, 042, 043, 044, 046, 158, 161 | 7 |
| SVC-API-CRUD | TC-PTY-001, 002, 004, 005, 006, 007, 008, 009, 011, 014, 017, 018, 020, 023, 024, 025, 029, 030, 031, 032, 033, 040, 045, 047, 050, 051, 052, 053, 055, 056, 057, 058, 059, 060, 061, 062, 064, 065, 066, 067, 069, 070, 071, 072, 073, 074, 075, 076, 078, 079, 097, 098, 103, 108, 109, 114, 115, 118, 119, 120, 155, 156, 157, 160, 162, 164, 168, 169, 172, 175, 176, 178, 191, 192, 193 | 75 |
| SVC-API-SEARCH | TC-PTY-021, 054, 063, 068, 077, 080, 081, 082, 100, 116, 121, 166, 197, 200 | 14 |
| SVC-API-INT | TC-PTY-026, 027, 028, 035, 036, 037, 038, 039, 048, 049, 084, 085, 086, 087, 088, 089, 090, 091, 092, 093, 094, 095, 096, 099, 101, 102, 104, 105, 106, 107, 110, 111, 112, 154, 159, 163, 165, 170, 171, 194, 198 | 41 |
| SEC-BE | TC-PTY-083, 113, 117, 167, 177, 196, 199 | 7 |
| DOC · ALIGN-BE · INT-C | none — no AC asserts documentation, alignment or an integration edge (N2); recorded, not dropped | 0 |

## COVERAGE

| Measure | Result |
|---|---|
| AC covered on this track | 138 / 140 — AC-PTY-078 and AC-PTY-087 by frontend TC-PTY-129 and TC-PTY-151 (N1) |
| AC covered by the module (both plans) | 140 / 140 ✓ — no gap |
| REQ covered on this track | 112 / 114 — REQ-PTY-070 and REQ-PTY-077 by the frontend plan; REQ-PTY-052 and REQ-PTY-054 through the outbox subscriber (TC-PTY-088, TC-PTY-027) |
| REQ covered by the module | 114 / 114 ✓ |
| Concurrency guards with a barrier-synchronised TC | 13 / 13 (N13) |
| API covered | 38 / 38 ✓ |
| RULE covered | 35 / 35 ✓ (29 catalog codes asserted with their ar + en text; RULE-006, 013, 014, 017, 020, 024 by their confirmation, filter, ensure, outcome or answer) |
| XM edges covered | 0 / 0 — no edge; `INT-XM` absent |
| Backend plan units with ≥ 1 TC | 7 / 10 — DOC, ALIGN-BE, INT-C carry none (N2) |
| TC / AC on this track | 156 / 138 ≈ 1.13 (guard ~2×: not exceeded) |
| Host data | 1 value (CUSTOMER_GROUP VIP inactive — TC-PTY-020 and TC-PTY-200, created and deactivated by the named mdl calls); 1 value held absent (ADDRESS_TYPE OFFICE — TC-PTY-018); both listed in SRS A6 `values`, so C10.8 / C10.9 examine them (N4); no catalogue value is required in opposite states by two TCs |

