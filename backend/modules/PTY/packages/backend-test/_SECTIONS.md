<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
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
