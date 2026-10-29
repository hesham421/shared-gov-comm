<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# Frontend test plan — Party / الأطراف (PTY) v1
══════════════════════════════════════════════════════════════════
Stage    : P4 — Test Plan · pass 2 · track frontend · plan test · lane test-gen
Module   : PTY   Version : v1   Profile : commerce
Sources  : _state/current-srs.md (P1 v1 — REQ 114 · AC 140 · RULE 35) · _state/current-registry-srs.md · _state/current-frontend-execution-plan.md (P3.2 — SCR-PTY-001 … 005, UXD-PTY-001, F1–F4, SEC-FE) · _state/current-api-spec.yaml (api-spec-pty.yaml — the mock the screens are served from) · _state/current-backend-execution-plan.md (catalog codes) · _state/current-registry-db.md · _state/current-dependency-graph.md
Counts   : TC 44 on this track = 42 module TCs (TEST-PLAN-FE: UI-FLOWS 36 + INT-FLOW 6) + 2 UXD TCs (INT-UXD) (TC-PTY-122 … TC-PTY-153, TC-PTY-179 … TC-PTY-190 — the sequence continues after the backend plan's revise additions TC-PTY-154 … 178, and the backend plan's revise 2 additions continue at TC-PTY-191; no id renumbered) · AC covered here 36 · UXD 1/1 (2 TCs) · module AC coverage 140/140 with the backend plan · gate-analysis revise: G1, G17, G18 applied
Framework: `profile.stack.testing` frontend = agnostic — each TC below is the whole contract; the consumer repo chooses how to turn it into a test
Open ADRs: 0 · cited: ADR-PTY-012, ADR-PTY-024, ADR-PTY-025, ADR-PTY-031, ADR-PTY-033, ADR-PTY-034, ADR-PTY-037, ADR-PTY-039, ADR-PTY-041, ADR-PTY-042, ADR-PTY-043 (all ACCEPTED; no PENDING item — ADR-PTY-043 replaces ADR-PTY-034 §9)
══════════════════════════════════════════════════════════════════

Every TC binds one `SCR-*` the frontend plan places and its route. Server responses are those of api-spec-pty.yaml (the frontend executor's mock until the backend is delivered); a TC that names a response states the AC's values it must carry. A refusal asserted by its text is one whose code the frontend plan routes with `text: RULE-PTY-<seq> message (SRS)` (msg-bound list at the end); the text is copied from the SRS in both languages.

## Derivation notes (non-mechanical choices — non-breaking)

This run writes only the two test plans; the choices below are recorded here for the analysis gate.

- **F-N1 — Scope of this track.** The frontend TCs cover the 30 ACs the frontend plan's F1–F4 / SEC-FE blocks cite, plus UXD-PTY-001. Every other AC is covered by the backend plan (its operations are consumer-only or have no screen-specific behaviour). AC-PTY-078 and AC-PTY-087 are covered on this track only.
- **F-N2 — Package rule.** Client validators, mandatory marks and form initial values → the `F3-SCR-*` SUB of the screen; refusal routing, invalidation and the outcome of a mutation → `F2-SCR-*`; routes, menus, route guards (no VIEW → the unauthorized page, menu entry not rendered), composition and navigation → `F4-SCR-*`; permission-hidden affordances inside a screen the user can open → `SEC-FE`; the UXD field → the F4 SUB of the screen that renders it (F4-SCR-PTY-004). AC-PTY-101's actor holds no permission on PTY_CREDIT_SETTINGS (F-N7), so TC-PTY-134 asserts the route guard and the menu entry of F4-SCR-PTY-005 ("Guard: … no VIEW → the unauthorized page"; menu entry PERM_PTY_CREDIT_SETTINGS_VIEW) and is that unit's acceptance. `F1-SCR-PTY-001 … 005` (types only) and `ALIGN-FE` receive no TC — recorded in COVERAGE, not dropped.
- **F-N3 — AC-PTY-013 action.** The AC's action opens Party P-000120. The frontend plan's EMAIL-HOLDER-QUERY finds the Party by email through the Customer search and then the Supplier search (ADR-PTY-043), keeping the row whose normalised email equals the entered one.
  - TC-PTY-123 asserts the Customer path.
  - TC-PTY-179 asserts the Supplier-only path that the former interim missed.
  - The message text itself names the Party code.
- **F-N4 — removed (gate-analysis G18).** TC-PTY-138's actor is now an Owner holding VIEW, UPDATE and DELETE on PTY_CREDIT_OVERRIDE. On the delivered backend that actor can load any request, so the precondition is reachable without the mock, and the server's refusal PTY-403-NOT-REQUESTER (RULE-PTY-029) is what the screen shows.
- **F-N5 — AC-PTY-111 reachability.** The decision panel exists only while PENDING; the approval of a REJECTED request is reached when the Detail was loaded while PENDING and the request was rejected meanwhile — the request is REJECTED when the approval reaches the server, as the AC states.
- **F-N6 — Consumer actors.** AC-PTY-117 ("Sales & POS requests CO-000031") is exercised on screen as the Owner opening the request's Detail; AC-PTY-010 / AC-PTY-011 run the exported Quick register inside a test host standing in for the sale / purchase document — the host's own screen is the other module's behaviour (C10.5); the TC asserts only that the PTY sub-view returns `{id, code}` and does not navigate.
- **F-N7 — AC-PTY-101 actor.** A Seller/Cashier holds no permission on PTY_CREDIT_SETTINGS (SRS B4): the access-denied response on screen is the unauthorized page, with no Save offered.
- **F-N8 — AC-PTY-087 entry.** The switch-off refusal of AC-PTY-085 is shown by the capability-flag owner (OI-05 open; API-PTY-037 has no PTY route — ADR-PTY-034); TC-PTY-151 starts from the route the next action opens.
- **F-N9 — Host data.** None on this track: every lookup value a case names is seeded (CUSTOMER_GROUP RETAIL, WHOLESALE; ADDRESS_TYPE BILLING, DELIVERY; CREDIT_OVERRUN_ACTION and CREDIT_OVERRIDE_STATUS are closed).
- **F-N10 — Frontend statements without a TC before the revise (gate-analysis G17).** The new TCs and the statements they cover:

  | TC | Statement covered | Package |
  |---|---|---|
  | TC-PTY-180 | the ordered save and partial failure | F2-SCR-PTY-001 |
  | TC-PTY-181 | commands disabled while dirty, and the leave prompt | F3-SCR-PTY-001 |
  | TC-PTY-182 | the flag switched off mid-session | F2-SCR-PTY-003 |
  | TC-PTY-183 | the empty state | F2-SCR-PTY-001 |
  | TC-PTY-184 | the not-found state | F2-SCR-PTY-004 |
  | TC-PTY-185 | the error state with Retry | F2-SCR-PTY-004 |
  | TC-PTY-186, 187 | the flag guard on SCR-PTY-004 and SCR-PTY-005 | F4-SCR-PTY-004, F4-SCR-PTY-005 |
  | TC-PTY-188 | the Warehouse read-only view | SEC-FE |

  SCR-PTY-003's flag guard is TC-PTY-129 and TC-PTY-182. TC-PTY-189 and 190 show the two backend refusals added by the revise: RULE-PTY-034, and RULE-PTY-030 on decisions.





## TC TRACEABILITY INDEX

### AC → TC · REQ → TC

| AC | REQ | TC | Scenario | Package | Backend TC |
|---|---|---|---|---|---|
| AC-PTY-007 | REQ-PTY-005 | TC-PTY-122 | HAPPY | F3-SCR-PTY-001 | TC-PTY-058 |
| AC-PTY-010 | REQ-PTY-008 | TC-PTY-146 | HAPPY | F2-SCR-PTY-001 | TC-PTY-060 |
| AC-PTY-011 | REQ-PTY-008 | TC-PTY-147 | HAPPY | F2-SCR-PTY-002 | TC-PTY-061 |
| AC-PTY-013 | REQ-PTY-010 | TC-PTY-123 (+ TC-PTY-179 Supplier-only Party) | VIOLATION | F2-SCR-PTY-001, F2-SCR-PTY-002 | TC-PTY-005 |
| AC-PTY-018 | REQ-PTY-013 | TC-PTY-183 (empty state) | HAPPY | F2-SCR-PTY-001 | TC-PTY-063 |
| AC-PTY-023 | REQ-PTY-018 | TC-PTY-188 (Warehouse read-only) | PERMISSION | SEC-FE | TC-PTY-068 |
| AC-PTY-036 | REQ-PTY-030 | TC-PTY-180 (ordered save, partial failure) | VIOLATION | F2-SCR-PTY-001 | TC-PTY-075 |
| AC-PTY-021 | REQ-PTY-016 | TC-PTY-124 | HAPPY | F3-SCR-PTY-002 | TC-PTY-066 |
| AC-PTY-029 | REQ-PTY-024 | TC-PTY-148 | HAPPY | F2-SCR-PTY-002 | TC-PTY-010 |
| AC-PTY-031 | REQ-PTY-026 | TC-PTY-149 | HAPPY | F4-SCR-PTY-002 | TC-PTY-074 |
| AC-PTY-055 | REQ-PTY-047 | TC-PTY-125 (+ TC-PTY-181 commands disabled while dirty) | VIOLATION | F2-SCR-PTY-001, F3-SCR-PTY-001 | TC-PTY-023 |
| AC-PTY-056 | REQ-PTY-048 | TC-PTY-126 | VIOLATION | F3-SCR-PTY-003 | TC-PTY-024 |
| AC-PTY-057 | REQ-PTY-049 | TC-PTY-127 | VIOLATION | F2-SCR-PTY-001 | TC-PTY-025 |
| AC-PTY-077 | REQ-PTY-069 | TC-PTY-128 | HAPPY | F2-SCR-PTY-003 | TC-PTY-100 |
| AC-PTY-078 | REQ-PTY-070 | TC-PTY-129 (+ TC-PTY-186, 187 flag guard on SCR-PTY-004, 005) | STATE | F4-SCR-PTY-001, F4-SCR-PTY-004, F4-SCR-PTY-005 | — (frontend only) |
| AC-PTY-080 | REQ-PTY-072 | TC-PTY-182 (flag off mid-session) | STATE | F2-SCR-PTY-003 | TC-PTY-032 |
| AC-PTY-082 | REQ-PTY-073 | TC-PTY-150 | STATE | F2-SCR-PTY-001 | TC-PTY-034 |
| AC-PTY-087 | REQ-PTY-077 | TC-PTY-151 | HAPPY | F4-SCR-PTY-003 | — (frontend only) |
| AC-PTY-088 | REQ-PTY-078 | TC-PTY-130 | HAPPY | F2-SCR-PTY-003 | TC-PTY-103 |
| AC-PTY-096 | REQ-PTY-085 | TC-PTY-131 | HAPPY | F2-SCR-PTY-003 | TC-PTY-108 |
| AC-PTY-097 | REQ-PTY-086 | TC-PTY-132 | HAPPY | F2-SCR-PTY-005 | TC-PTY-109 |
| AC-PTY-098 | REQ-PTY-087 | TC-PTY-133 | STATE | F3-SCR-PTY-005 | TC-PTY-110 |
| AC-PTY-101 | REQ-PTY-090 | TC-PTY-134 | PERMISSION | F4-SCR-PTY-005 | TC-PTY-113 |
| AC-PTY-102 | REQ-PTY-091 | TC-PTY-135 | HAPPY | F2-SCR-PTY-004 | TC-PTY-114 |
| AC-PTY-106 | REQ-PTY-094 | TC-PTY-136 | HAPPY | F2-SCR-PTY-004 | TC-PTY-042 |
| AC-PTY-107 | REQ-PTY-095 | TC-PTY-137 | HAPPY | F2-SCR-PTY-004 | TC-PTY-043 |
| AC-PTY-109 | REQ-PTY-096 | TC-PTY-138 | VIOLATION | F2-SCR-PTY-004 | TC-PTY-045 |
| AC-PTY-111 | REQ-PTY-098 | TC-PTY-139 | VIOLATION | F2-SCR-PTY-004 | TC-PTY-047 |
| AC-PTY-114 | REQ-PTY-100 | TC-PTY-140 | VIOLATION | F3-SCR-PTY-004 | TC-PTY-050 |
| AC-PTY-115 | REQ-PTY-101 | TC-PTY-141 (+ TC-PTY-185 error state) | HAPPY | F2-SCR-PTY-004 | TC-PTY-116 |
| AC-PTY-116 | REQ-PTY-102 | TC-PTY-142 | PERMISSION | SEC-FE | TC-PTY-117 |
| AC-PTY-117 | REQ-PTY-103 | TC-PTY-143 (+ TC-PTY-152, 153 via UXD-PTY-001; TC-PTY-184 not-found state) | HAPPY | F4-SCR-PTY-004 | TC-PTY-118 |
| AC-PTY-122 | REQ-PTY-108 | TC-PTY-144 | HAPPY | F4-SCR-PTY-003 | TC-PTY-121 |
| AC-PTY-123 | REQ-PTY-109 | TC-PTY-145 | VIOLATION | F2-SCR-PTY-003 | TC-PTY-053 |
| AC-PTY-132 | REQ-PTY-113 | TC-PTY-189 | VIOLATION | F2-SCR-PTY-004 | TC-PTY-162 |
| AC-PTY-134 | REQ-PTY-109 | TC-PTY-190 | VIOLATION | F2-SCR-PTY-004 | TC-PTY-164 |

The remaining 100 ACs are covered by the backend plan only (its AC → TC table); none is uncovered.

### SCR → TC

| SCR | Route(s) exercised | TC |
|---|---|---|
| SCR-PTY-001 Customers | /pty/customers, /pty/customers/new, /pty/customers/:customerId, Quick register | TC-PTY-122, 123, 125, 127, 129, 146, 148, 149, 150, 180, 181, 183 |
| SCR-PTY-002 Suppliers | /pty/suppliers, /pty/suppliers/new, /pty/suppliers/:supplierId, Quick register | TC-PTY-124, 147, 148, 149, 179, 188 |
| SCR-PTY-003 Customer credit | /pty/customer-credits, /pty/customer-credits/:customerId | TC-PTY-126, 128, 130, 131, 144, 145, 151, 182 |
| SCR-PTY-004 Credit override requests | /pty/credit-overrides, /new, /:requestId | TC-PTY-135, 136, 137, 138, 139, 140, 141, 142, 143, 152, 153, 184, 185, 186, 189, 190 |
| SCR-PTY-005 Credit control settings | /pty/credit-settings | TC-PTY-132, 133, 134, 187 |

### RULE / code → TC (refusals asserted on screen)

| RULE | Code | TC |
|---|---|---|
| RULE-PTY-002 | PTY-409-EMAIL-TAKEN | TC-PTY-123 |
| RULE-PTY-010 | PTY-422-WALK-IN-DEACTIVATE | TC-PTY-125 |
| RULE-PTY-011 | PTY-422-WALK-IN-CREDIT (client refinement first) | TC-PTY-126 |
| RULE-PTY-012 | PTY-422-WALK-IN-SUPPLIER | TC-PTY-127 |
| RULE-PTY-026 | PTY-409-REQUEST-NOT-PENDING | TC-PTY-139 |
| RULE-PTY-027 | PTY-422-REJECTION-REASON-REQUIRED (client validator first) | TC-PTY-140 |
| RULE-PTY-029 | PTY-403-NOT-REQUESTER | TC-PTY-138 |
| RULE-PTY-030 | PTY-500-AUDIT-NOT-RECORDED | TC-PTY-145, 190 |
| RULE-PTY-002 | PTY-409-EMAIL-TAKEN (Supplier-only Party) | TC-PTY-179 |
| RULE-PTY-016 | PTY-422-ADDRESS-TYPE-INVALID (partial failure of the ordered save) | TC-PTY-180 |
| RULE-PTY-023 | PTY-422-CREDIT-NOT-ENABLED (flag off mid-session) | TC-PTY-182 |
| RULE-PTY-034 | PTY-409-REQUEST-OPEN-FOR-SALE | TC-PTY-189 |
| PLATFORM-STD | PTY-404-CREDIT-OVERRIDE-NOT-FOUND, PTY-500-INTERNAL (states) | TC-PTY-184, 185 |

### UXD → TC

| UXD | Owner | Screen | Rendered | Empty / owner failure |
|---|---|---|---|---|
| UXD-PTY-001 (decidedBy) | given `erp-core:modules.sec` | SCR-PTY-004 | TC-PTY-152 | TC-PTY-153 |

### Package → TC

| Package (frontend plan unit) | TC | Count |
|---|---|---|
| F2-SCR-PTY-001 | TC-PTY-123, 125, 127, 146, 150, 180, 183 | 7 |
| F2-SCR-PTY-002 | TC-PTY-147, 148, 179 | 3 |
| F2-SCR-PTY-003 | TC-PTY-128, 130, 131, 145, 182 | 5 |
| F2-SCR-PTY-004 | TC-PTY-135, 136, 137, 138, 139, 141, 184, 185, 189, 190 | 10 |
| F2-SCR-PTY-005 | TC-PTY-132 | 1 |
| F3-SCR-PTY-001 | TC-PTY-122, 181 | 2 |
| F3-SCR-PTY-002 | TC-PTY-124 | 1 |
| F3-SCR-PTY-003 | TC-PTY-126 | 1 |
| F3-SCR-PTY-004 | TC-PTY-140 | 1 |
| F3-SCR-PTY-005 | TC-PTY-133 | 1 |
| F4-SCR-PTY-001 | TC-PTY-129 | 1 |
| F4-SCR-PTY-002 | TC-PTY-149 | 1 |
| F4-SCR-PTY-003 | TC-PTY-144, 151 | 2 |
| F4-SCR-PTY-004 | TC-PTY-143, 152, 153, 186 | 4 |
| F4-SCR-PTY-005 | TC-PTY-134, 187 | 2 |
| SEC-FE | TC-PTY-142, 188 | 2 |
| F1-SCR-PTY-001 … 005 · ALIGN-FE | none — types-only units and the alignment phase (F-N2); recorded, not dropped | 0 |

### msg-bound — refusal texts asserted on screen and their binding in the frontend plan

| TC | Code | Frontend plan row that binds the text |
|---|---|---|
| TC-PTY-123 | PTY-409-EMAIL-TAKEN | F2 SCR-PTY-001 CUSTOMER-SAVE — "inline on email, text: RULE-PTY-002 message (SRS)" |
| TC-PTY-125 | PTY-422-WALK-IN-DEACTIVATE | F2 SCR-PTY-001 CUSTOMER-DEACTIVATE — "banner on the record, text: RULE-PTY-010 message (SRS)" |
| TC-PTY-126 | PTY-422-WALK-IN-CREDIT | F3 SCR-PTY-003 CreditForm refinement → RULE-PTY-011; F2 CREDIT-SAVE — "form banner, text: RULE-PTY-011 message (SRS)" |
| TC-PTY-127 | PTY-422-WALK-IN-SUPPLIER | F2 SCR-PTY-001 CUSTOMER-MAKE-SUPPLIER — "banner on the record, text: RULE-PTY-012 message (SRS)" |
| TC-PTY-138 | PTY-403-NOT-REQUESTER | F2 SCR-PTY-004 OVERRIDE-CANCEL — "banner on the decision panel (not the unauthorized page), text: RULE-PTY-029 message (SRS)" |
| TC-PTY-139 | PTY-409-REQUEST-NOT-PENDING | F2 SCR-PTY-004 OVERRIDE-APPROVE — "banner on the decision panel and refetch of the request, text: RULE-PTY-026 message (SRS)" |
| TC-PTY-140 | PTY-422-REJECTION-REASON-REQUIRED | F3 SCR-PTY-004 RejectForm → RULE-PTY-027; F2 OVERRIDE-REJECT — "inline on decisionReason, text: RULE-PTY-027 message (SRS)" |
| TC-PTY-145 | PTY-500-AUDIT-NOT-RECORDED | F2 SCR-PTY-003 CREDIT-SAVE — "form banner, form kept for retry, text: RULE-PTY-030 message (SRS)" |
| TC-PTY-179 | PTY-409-EMAIL-TAKEN | F2 SCR-PTY-002 SUPPLIER-SAVE — "inline on email, text: RULE-PTY-002 message (SRS), with the action open that party (EMAIL-HOLDER-QUERY, ADR-PTY-043)" |
| TC-PTY-180 | PTY-422-ADDRESS-TYPE-INVALID | F2 ADDRESSES-SYNC — "inline on addressTypeCode of the failing line, text: RULE-PTY-016 message (SRS)" |
| TC-PTY-182 | PTY-422-CREDIT-NOT-ENABLED | F2 SCR-PTY-003 CREDIT-SAVE — "form banner, text: RULE-PTY-023 message (SRS), refetch app-config" |
| TC-PTY-189 | PTY-409-REQUEST-OPEN-FOR-SALE | F2 SCR-PTY-004 OVERRIDE-CREATE — "form banner, text: RULE-PTY-034 message (SRS)" |
| TC-PTY-190 | PTY-500-AUDIT-NOT-RECORDED | F2 SCR-PTY-004 OVERRIDE-APPROVE — "banner on the decision panel, the request is re-read (still PENDING), text: RULE-PTY-030 message (SRS)" |

Every refusal asserted by its text is bound in the frontend plan; no plan gap of this kind.

## COVERAGE

| Measure | Result |
|---|---|
| AC covered on this track | 36 — every AC the frontend plan cites (F-N1) |
| AC covered by the module (both plans) | 140 / 140 ✓ — no gap (AC-PTY-137 … AC-PTY-140, added by the gate-analysis revise 2, are covered by backend TC-PTY-191, 196, 197, 198) |
| REQ covered on this track | 33 (incl. REQ-PTY-070 and REQ-PTY-077, covered here only) |
| Frontend statements of G17 with a TC | 8 / 8 (F-N10) |
| SCR covered | 5 / 5 ✓ |
| UXD covered | 1 / 1 ✓ (rendered + owner failure) |
| Frontend plan units with ≥ 1 TC | 16 / 22 — F1-SCR-PTY-001 … 005 and ALIGN-FE carry none (F-N2) |
| TC / AC on this track | 42 module TCs / 36 ACs ≈ 1.17 (+ 2 UXD TCs); guard ~2× not exceeded |
| Host data | none (F-N9) |
| Open plan gap recorded for the gate | none — F-N4 removed: TC-PTY-138's actor (an Owner) loads the request on the delivered backend |
