# Frontend test plan — Party / الأطراف (PTY) v1
══════════════════════════════════════════════════════════════════
Stage    : P4 — Test Plan · pass 2 · track frontend · plan test · lane test-gen
Module   : PTY   Version : v1   Profile : commerce
Sources  : _state/current-srs.md (P1 v1 — REQ 109 · AC 123 · RULE 33) · _state/current-registry-srs.md · _state/current-frontend-execution-plan.md (P3.2 — SCR-PTY-001 … 005, UXD-PTY-001, F1–F4, SEC-FE) · _state/current-api-spec.yaml (api-spec-pty.yaml — the mock the screens are served from) · _state/current-backend-execution-plan.md (catalog codes) · _state/current-registry-db.md · _state/current-dependency-graph.md
Counts   : TC 44 on this track (TC-PTY-122 … TC-PTY-153, TC-PTY-179 … TC-PTY-190 — the sequence continues after the backend plan's revise additions TC-PTY-154 … 178; no id renumbered) · AC covered here 36 · UXD 1/1 (2 TCs) · module AC coverage 136/136 with the backend plan · gate-analysis revise: G1, G17, G18 applied
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

<!-- PHASE:TEST-PLAN-FE:START traces=REQ-PTY-005,REQ-PTY-008,REQ-PTY-010,REQ-PTY-013,REQ-PTY-016,REQ-PTY-018,REQ-PTY-024,REQ-PTY-026,REQ-PTY-030,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-073,REQ-PTY-077,REQ-PTY-078,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-090,REQ-PTY-091,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-108,REQ-PTY-109,REQ-PTY-113,AC-PTY-007,AC-PTY-010,AC-PTY-011,AC-PTY-013,AC-PTY-018,AC-PTY-021,AC-PTY-023,AC-PTY-029,AC-PTY-031,AC-PTY-036,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-077,AC-PTY-078,AC-PTY-080,AC-PTY-082,AC-PTY-087,AC-PTY-088,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-101,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-122,AC-PTY-123,AC-PTY-132,AC-PTY-134,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
## TEST-PLAN-FE — module scenarios

42 TCs (> 8), split in two:
- UI-FLOWS — per-screen behaviour: 36 TCs, 12 of them added by the gate-analysis revise (TC-PTY-179 … 190);
- INT-FLOW — flows that cross screens, a host document or the flag lifecycle: 6 TCs.

<!-- SUB:UI-FLOWS:START traces=REQ-PTY-005,REQ-PTY-010,REQ-PTY-013,REQ-PTY-016,REQ-PTY-018,REQ-PTY-030,REQ-PTY-047,REQ-PTY-048,REQ-PTY-049,REQ-PTY-069,REQ-PTY-070,REQ-PTY-072,REQ-PTY-078,REQ-PTY-085,REQ-PTY-086,REQ-PTY-087,REQ-PTY-090,REQ-PTY-091,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-098,REQ-PTY-100,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,REQ-PTY-108,REQ-PTY-109,REQ-PTY-113,AC-PTY-007,AC-PTY-013,AC-PTY-018,AC-PTY-021,AC-PTY-023,AC-PTY-036,AC-PTY-055,AC-PTY-056,AC-PTY-057,AC-PTY-077,AC-PTY-078,AC-PTY-080,AC-PTY-088,AC-PTY-096,AC-PTY-097,AC-PTY-098,AC-PTY-101,AC-PTY-102,AC-PTY-106,AC-PTY-107,AC-PTY-109,AC-PTY-111,AC-PTY-114,AC-PTY-115,AC-PTY-116,AC-PTY-117,AC-PTY-122,AC-PTY-123,AC-PTY-132,AC-PTY-134,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003,SCR-PTY-004,SCR-PTY-005 -->
### UI-FLOWS — per-screen behaviour

<!-- TC:TC-PTY-122:START traces=AC-PTY-007,REQ-PTY-005,SCR-PTY-001 -->
### TC-PTY-122 — Quick register from a sale marks one input mandatory
Derived from : AC-PTY-007  (REQ-PTY-005)
Exercises    : SCR-PTY-001 Quick register sub-view (`CustomerQuickRegister`, opened in place from a sale)
Rule / code  : RULE-PTY-001 → — (the one mandatory name group)
Package      : F3-SCR-PTY-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier session holding PERM_PTY_CUSTOMER_CREATE; an open sale (test host) showing the Quick register; the mock answers API-PTY-001 with 201
Host data    : none
Steps        : 1. open the Quick register from the sale · 2. count the inputs marked mandatory · 3. enter nameEn "Walid Hassan" only · 4. Save «حفظ»
Expected     : step 2 → exactly 1 mandatory mark (the name group) · step 4 → one API-PTY-001 request whose body carries nameEn "Walid Hassan" and no customerGroupCode; the Customer is saved (the sub-view reports success to its host)
Test data    : nameEn "Walid Hassan" (AC)
<!-- TC:TC-PTY-122:END -->

<!-- TC:TC-PTY-123:START traces=AC-PTY-013,REQ-PTY-010,SCR-PTY-001 -->
### TC-PTY-123 — Duplicate email shown on the email field with the open-party action
Derived from : AC-PTY-013  (REQ-PTY-010)
Exercises    : SCR-PTY-001 /pty/customers/new
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN
Package      : F2-SCR-PTY-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: a session holding PERM_PTY_CUSTOMER_VIEW and _CREATE; active Party P-000120 holds email "nour@example.com"; the mock answers API-PTY-001 with 409 PTY-409-EMAIL-TAKEN and the RULE-PTY-002 text filled with P-000120
Host data    : none
Steps        : 1. open /pty/customers/new · 2. enter a name `<nameEn>` and email "Nour@Example.com" · 3. Save «حفظ» · 4. choose "Open that party" «فتح ذلك الطرف»
Expected     : step 3 → no navigation; inline on email: ar «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000120). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · en «This email is already registered to another active party (P-000120). Open that party instead of creating a new one.»; the typed values are kept; no address call is sent · step 4 → one API-PTY-026 request with email "Nour@Example.com" and isActive=true; the mock returns Customer 5001 of P-000120 (email "nour@example.com"); the route becomes /pty/customers/5001 (EMAIL-HOLDER-QUERY, ADR-PTY-043 — F-N3)
Test data    : email "Nour@Example.com", code P-000120 (AC); `<nameEn>` placeholder
<!-- TC:TC-PTY-123:END -->

<!-- TC:TC-PTY-124:START traces=AC-PTY-021,REQ-PTY-016,SCR-PTY-002 -->
### TC-PTY-124 — Supplier entry needs one name only
Derived from : AC-PTY-021  (REQ-PTY-016)
Exercises    : SCR-PTY-002 /pty/suppliers/new
Rule / code  : RULE-PTY-001 → — (the one mandatory name group)
Package      : F3-SCR-PTY-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_VIEW and _CREATE; the mock answers API-PTY-006 with 201 (isActiveFl true)
Host data    : none
Steps        : 1. open /pty/suppliers/new · 2. count the inputs marked mandatory · 3. enter nameEn "Delta Imports" only · 4. Save «حفظ»
Expected     : step 2 → exactly 1 mandatory mark (the name group) · step 4 → API-PTY-006 body carries nameEn "Delta Imports" and no partyId; the route is replaced by /pty/suppliers/:supplierId of the response, showing the Supplier active
Test data    : nameEn "Delta Imports" (AC)
<!-- TC:TC-PTY-124:END -->

<!-- TC:TC-PTY-125:START traces=AC-PTY-055,REQ-PTY-047,SCR-PTY-001 -->
### TC-PTY-125 — Deactivating the walk-in Customer refused on the record
Derived from : AC-PTY-055  (REQ-PTY-047)
Exercises    : SCR-PTY-001 /pty/customers/:customerId
Rule / code  : RULE-PTY-010 → PTY-422-WALK-IN-DEACTIVATE
Package      : F2-SCR-PTY-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW and _DELETE; the walk-in Customer 5000; the mock answers API-PTY-004 with 422 PTY-422-WALK-IN-DEACTIVATE
Host data    : none
Steps        : 1. open /pty/customers/5000 · 2. Deactivate and confirm
Expected     : banner on the record: ar «العميل النقدي لازم لنقطة البيع ولا يمكن إيقافه.» · en «The walk-in customer is required by the point of sale and cannot be deactivated.» · the record still shows the Customer active; no toast of RULE-PTY-006
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-125:END -->

<!-- TC:TC-PTY-126:START traces=AC-PTY-056,REQ-PTY-048,SCR-PTY-003 -->
### TC-PTY-126 — Credit limit on the walk-in Customer stopped on the form
Derived from : AC-PTY-056  (REQ-PTY-048)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : RULE-PTY-011 → PTY-422-WALK-IN-CREDIT
Package      : F3-SCR-PTY-003
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW and _UPDATE; the mock answers API-PTY-016 for Customer 5000 with isWalkInFl true and both credit values empty
Host data    : none
Steps        : 1. open /pty/customer-credits/5000 · 2. enter credit limit 1000 · 3. Save «حفظ»
Expected     : form-level message ar «لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان.» · en «The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit.»; no API-PTY-017 request is sent; the shown standing is unchanged
Test data    : customerId 5000, creditLimit 1000 (AC)
<!-- TC:TC-PTY-126:END -->

<!-- TC:TC-PTY-127:START traces=AC-PTY-057,REQ-PTY-049,SCR-PTY-001 -->
### TC-PTY-127 — "Make supplier" on the walk-in Customer refused
Derived from : AC-PTY-057  (REQ-PTY-049)
Exercises    : SCR-PTY-001 /pty/customers/:customerId
Rule / code  : RULE-PTY-012 → PTY-422-WALK-IN-SUPPLIER
Package      : F2-SCR-PTY-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW and PERM_PTY_SUPPLIER_CREATE; the walk-in Customer 5000; the mock answers API-PTY-006 with 422 PTY-422-WALK-IN-SUPPLIER
Host data    : none
Steps        : 1. open /pty/customers/5000 · 2. choose "Make supplier" «إضافة كمورّد» and confirm
Expected     : API-PTY-006 body is {partyId} only · banner on the record: ar «لا يمكن إضافة صفة المورّد إلى العميل النقدي.» · en «The walk-in customer cannot also be a supplier.» · the route stays /pty/customers/5000 (no navigation to Suppliers)
Test data    : customerId 5000 (AC)
<!-- TC:TC-PTY-127:END -->

<!-- TC:TC-PTY-128:START traces=AC-PTY-077,REQ-PTY-069,SCR-PTY-003 -->
### TC-PTY-128 — Customer credit list filtered on carries-credit
Derived from : AC-PTY-077  (REQ-PTY-069)
Exercises    : SCR-PTY-003 /pty/customer-credits
Rule / code  : — 
Package      : F2-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW; 4 Customers carry a credit limit or payment terms and 20 carry neither (the mock answers API-PTY-030 with carriesCredit=true by those 4)
Host data    : none
Steps        : 1. open /pty/customer-credits · 2. set carries-credit = yes and search
Expected     : the request carries carriesCredit=true and the route's search parameters mirror it · the table shows exactly the 4 Customers, each with its credit limit and payment terms columns
Test data    : counts 4 / 20 (AC); Customer identities are placeholders
<!-- TC:TC-PTY-128:END -->

<!-- TC:TC-PTY-129:START traces=AC-PTY-078,REQ-PTY-070,SCR-PTY-001 -->
### TC-PTY-129 — No credit menu, field or filter while the flag is off
Derived from : AC-PTY-078  (REQ-PTY-070)
Exercises    : SCR-PTY-001 /pty/customers/:customerId and the Party menu
Rule / code  : — 
Package      : F4-SCR-PTY-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off (application configuration); an Owner session holding every PTY permission; Customer 5001 (the mock answers API-PTY-002 without creditLimit and paymentTermDays)
Host data    : none
Steps        : 1. open the Party menu «الأطراف» · 2. open /pty/customers/5001 · 3. open the search form of /pty/customers · 4. open /pty/customer-credits
Expected     : step 1 → Customers «العملاء» and Suppliers «الموردون» only — 0 credit entries, no Credit «الائتمان» group · step 2 → 0 credit fields, no "Edit credit" «تعديل الائتمان» · step 3 → 0 credit filters · step 4 → the route is not mounted (not-found / home)
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-129:END -->

<!-- TC:TC-PTY-130:START traces=AC-PTY-088,REQ-PTY-078,SCR-PTY-003 -->
### TC-PTY-130 — Clear credit empties the limit and the payment terms
Derived from : AC-PTY-088  (REQ-PTY-078)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : — 
Package      : F2-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW and _UPDATE; Customer 5001 with credit limit 50000 and payment terms 45; the mock answers API-PTY-018 with both values null
Host data    : none
Steps        : 1. open /pty/customer-credits/5001 · 2. choose "Clear credit" «مسح الائتمان» · 3. confirm
Expected     : step 2 → confirmation ar «سيُمسح حد الائتمان ومدة السداد لهذا العميل. متابعة؟» · en "The credit limit and payment terms of this customer will be cleared. Continue?" · step 3 → one API-PTY-018 call; credit limit reads «لا ائتمان» / "No credit", payment terms empty. The dependency count is backend TC-PTY-103
Test data    : customerId 5001, 50000, 45 (AC)
<!-- TC:TC-PTY-130:END -->

<!-- TC:TC-PTY-131:START traces=AC-PTY-096,REQ-PTY-085,SCR-PTY-003 -->
### TC-PTY-131 — Credit standing shown to a Seller/Cashier read-only
Derived from : AC-PTY-096  (REQ-PTY-085)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : — 
Package      : F2-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; a Seller/Cashier session holding PERM_PTY_CUSTOMER_CREDIT_VIEW only; the mock answers API-PTY-016 for Customer 5001 with creditLimit 50000, openExposure 42000, availableCredit 8000, exposureAvailable true
Host data    : none
Steps        : 1. open /pty/customer-credits/5001
Expected     : the screen shows credit limit 50000, open exposure 42000 and available credit 8000; the credit form is read-only with no Save and no Clear credit
Test data    : values of the AC
<!-- TC:TC-PTY-131:END -->

<!-- TC:TC-PTY-132:START traces=AC-PTY-097,REQ-PTY-086,SCR-PTY-005 -->
### TC-PTY-132 — Owner saves WARN as the overrun action
Derived from : AC-PTY-097  (REQ-PTY-086)
Exercises    : SCR-PTY-005 /pty/credit-settings
Rule / code  : — 
Package      : F2-SCR-PTY-005
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_SETTINGS_VIEW and _UPDATE; the mock answers API-PTY-024 with BLOCK_WITH_OVERRIDE and API-PTY-025 with WARN
Host data    : none
Steps        : 1. open /pty/credit-settings · 2. select WARN «تنبيه فقط» · 3. Save «حفظ»
Expected     : the API-PTY-025 body is {"creditOverrunActionCode": "WARN"} (isDefault never sent) · after the save the screen shows WARN selected. The next credit check carrying WARN is backend TC-PTY-109
Test data    : WARN, BLOCK_WITH_OVERRIDE (AC; closed lookup labels as seeded)
<!-- TC:TC-PTY-132:END -->

<!-- TC:TC-PTY-133:START traces=AC-PTY-098,REQ-PTY-087,SCR-PTY-005 -->
### TC-PTY-133 — BLOCK_WITH_OVERRIDE preselected while nothing is saved
Derived from : AC-PTY-098  (REQ-PTY-087)
Exercises    : SCR-PTY-005 /pty/credit-settings
Rule / code  : — 
Package      : F3-SCR-PTY-005
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` just turned on; no setting saved — the mock answers API-PTY-024 with BLOCK_WITH_OVERRIDE and isDefault true; an Owner session holding PERM_PTY_CREDIT_SETTINGS_VIEW
Host data    : none
Steps        : 1. open /pty/credit-settings
Expected     : the two-option group shows BLOCK_WITH_OVERRIDE «منع مع إمكانية الموافقة» / "Block with approval" selected, with the note «القيمة الافتراضية» / "Default value"
Test data    : values of the AC
<!-- TC:TC-PTY-133:END -->

<!-- TC:TC-PTY-134:START traces=AC-PTY-101,REQ-PTY-090,SCR-PTY-005 -->
### TC-PTY-134 — Seller/Cashier cannot reach the overrun setting
Derived from : AC-PTY-101  (REQ-PTY-090)
Exercises    : SCR-PTY-005 /pty/credit-settings
Rule / code  : — (route guard; the server would answer PTY-403-FORBIDDEN — backend TC-PTY-113)
Package      : F4-SCR-PTY-005
Scenario     : PERMISSION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; a Seller/Cashier session without PERM_PTY_CREDIT_SETTINGS_UPDATE (and, per SRS B4, without _VIEW); the tenant setting is BLOCK_WITH_OVERRIDE
Host data    : none
Steps        : 1. open the Party menu · 2. deep-link to /pty/credit-settings
Expected     : step 1 → no Credit control settings «إعدادات ضبط الائتمان» entry · step 2 → the unauthorized page; no Save offered and no API-PTY-025 request sent; the setting read by an Owner stays BLOCK_WITH_OVERRIDE (F-N7)
Test data    : values of the AC
<!-- TC:TC-PTY-134:END -->

<!-- TC:TC-PTY-135:START traces=AC-PTY-102,REQ-PTY-091,SCR-PTY-004 -->
### TC-PTY-135 — Request approval from a sale's prefilled form
Derived from : AC-PTY-102  (REQ-PTY-091)
Exercises    : SCR-PTY-004 /pty/credit-overrides/new
Rule / code  : — 
Package      : F2-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; a Seller/Cashier session holding PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE and PERM_PTY_CUSTOMER_VIEW; Customer 5001 with credit limit 50000 and open exposure 49000; the mock answers API-PTY-019 with statusCode PENDING, customerFk 5001, saleAmount 2000, creditLimitAtRequest 50000, openExposure 49000, creditCheckResultCode OVER_LIMIT, sourceDocumentRef "POS-1-000455", createdBy the Seller/Cashier
Host data    : none
Steps        : 1. open /pty/credit-overrides/new?customerId=5001&saleAmount=2000&sourceDocumentRef=POS-1-000455 · 2. choose "Request approval" «طلب موافقة»
Expected     : step 1 → the form is prefilled (Customer 5001 labelled by its code and name) · step 2 → one API-PTY-019 request with {customerId 5001, saleAmount 2000, sourceDocumentRef "POS-1-000455"}; the route is replaced by /pty/credit-overrides/:requestId showing status Pending «معلّق», Customer 5001, sale amount 2000, credit limit at request 50000, open exposure 49000, result Over limit «تجاوز الحد», the sale reference and the Seller/Cashier as requester
Test data    : values of the AC
<!-- TC:TC-PTY-135:END -->

<!-- TC:TC-PTY-136:START traces=AC-PTY-106,REQ-PTY-094,SCR-PTY-004 -->
### TC-PTY-136 — Owner approves a PENDING request
Derived from : AC-PTY-106  (REQ-PTY-094)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F2-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; PENDING request CO-000031; the mock answers API-PTY-021 with statusCode APPROVED, decidedBy the Owner, decidedAt 2026-11-02 10:17
Host data    : none
Steps        : 1. open the Detail of CO-000031 · 2. Approve «موافقة» and confirm
Expected     : one API-PTY-021 call with no docNo · the Detail refreshes to status Approved «موافَق عليه», decided at 2026-11-02 10:17; the decision panel is gone. The decider's name is TC-PTY-152
Test data    : CO-000031, 2026-11-02 10:17 (AC)
<!-- TC:TC-PTY-136:END -->

<!-- TC:TC-PTY-137:START traces=AC-PTY-107,REQ-PTY-095,SCR-PTY-004 -->
### TC-PTY-137 — Owner rejects a PENDING request with a reason
Derived from : AC-PTY-107  (REQ-PTY-095)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F2-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; PENDING request CO-000032; the mock answers API-PTY-022 with statusCode REJECTED and decisionReason "Overdue invoices"
Host data    : none
Steps        : 1. open the Detail of CO-000032 · 2. enter the reason "Overdue invoices" · 3. Reject «رفض» and confirm
Expected     : the API-PTY-022 body is {"decisionReason": "Overdue invoices"} (no docNo) · the Detail shows status Rejected «مرفوض» and the reason "Overdue invoices"; the decision panel is gone
Test data    : CO-000032, "Overdue invoices" (AC)
<!-- TC:TC-PTY-137:END -->

<!-- TC:TC-PTY-138:START traces=AC-PTY-109,REQ-PTY-096,SCR-PTY-004 -->
### TC-PTY-138 — Cancel by someone other than the requester refused on the panel
Derived from : AC-PTY-109  (REQ-PTY-096)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : RULE-PTY-029 → PTY-403-NOT-REQUESTER
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW, _UPDATE and _DELETE (so the Detail of any request loads on the delivered backend — REQ-PTY-102); PENDING request CO-000033 created by Seller/Cashier S1; the server (or the mock) answers API-PTY-023 with 403 PTY-403-NOT-REQUESTER
Host data    : none
Steps        : 1. as the Owner, open the Detail of CO-000033 · 2. "Cancel request" «إلغاء الطلب» and confirm
Expected     : a banner on the decision panel — not the unauthorized page: ar «لا يلغي هذا الطلب إلا من قدّمه.» · en «Only the requester can cancel this request.» · the Detail still shows status Pending «معلّق»
Test data    : CO-000033, S1 (AC); the Owner stands for "a user other than the requester" (F-N4 removed)
<!-- TC:TC-PTY-138:END -->

<!-- TC:TC-PTY-139:START traces=AC-PTY-111,REQ-PTY-098,SCR-PTY-004 -->
### TC-PTY-139 — Approval of a request rejected meanwhile refused and refreshed
Derived from : AC-PTY-111  (REQ-PTY-098)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : RULE-PTY-026 → PTY-409-REQUEST-NOT-PENDING
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; the Detail of CO-000032 loaded while PENDING; CO-000032 then rejected (its statusCode is REJECTED when the approval reaches the server — F-N5); the mock answers API-PTY-021 with 409 PTY-409-REQUEST-NOT-PENDING and the refetch of API-PTY-020 with REJECTED
Host data    : none
Steps        : 1. Approve «موافقة» and confirm
Expected     : a banner on the decision panel: ar «هذا الطلب لم يعد معلّقاً (الحالة: مرفوض).» · en «This request is no longer pending (status: Rejected).» · the request is re-read: status Rejected «مرفوض», the decision panel is gone
Test data    : CO-000032 (AC)
<!-- TC:TC-PTY-139:END -->

<!-- TC:TC-PTY-140:START traces=AC-PTY-114,REQ-PTY-100,SCR-PTY-004 -->
### TC-PTY-140 — Rejection without a reason stopped on the reason field
Derived from : AC-PTY-114  (REQ-PTY-100)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : RULE-PTY-027 → PTY-422-REJECTION-REASON-REQUIRED
Package      : F3-SCR-PTY-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; PENDING request CO-000034
Host data    : none
Steps        : 1. open the Detail of CO-000034 · 2. leave the reason empty · 3. Reject «رفض»
Expected     : inline on the reason field: ar «أدخل سبب الرفض.» · en «Enter the reason for the rejection.» · no API-PTY-022 request sent; the Detail still shows status Pending «معلّق»
Test data    : CO-000034, empty reason (AC)
<!-- TC:TC-PTY-140:END -->

<!-- TC:TC-PTY-141:START traces=AC-PTY-115,REQ-PTY-101,SCR-PTY-004 -->
### TC-PTY-141 — Request list keeps the server's order
Derived from : AC-PTY-115  (REQ-PTY-101)
Exercises    : SCR-PTY-004 /pty/credit-overrides
Rule / code  : — 
Package      : F2-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; requests CO-000031 (APPROVED, 2026-11-02), CO-000035 (PENDING, 2026-11-01), CO-000036 (PENDING, 2026-11-03) — the mock answers API-PTY-032 in the order PENDING first then newest
Host data    : none
Steps        : 1. open /pty/credit-overrides with no filter
Expected     : the API-PTY-032 request carries no sort parameter · rows show CO-000036, CO-000035, CO-000031 in that order
Test data    : values of the AC
<!-- TC:TC-PTY-141:END -->

<!-- TC:TC-PTY-142:START traces=AC-PTY-116,REQ-PTY-102,SCR-PTY-004 -->
### TC-PTY-142 — Requester without UPDATE sees own requests and no decision commands
Derived from : AC-PTY-116  (REQ-PTY-102)
Exercises    : SCR-PTY-004 /pty/credit-overrides
Rule / code  : — 
Package      : SEC-FE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: `b2bCredit` on; Seller/Cashier S1 created CO-000035 and S2 created CO-000036; S1's session holds PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE, _DELETE but not _UPDATE; the mock answers API-PTY-032 for S1 with CO-000035 only (server-side visibility)
Host data    : none
Steps        : 1. as S1, open /pty/credit-overrides with no filter · 2. open CO-000035
Expected     : step 1 → the list shows CO-000035 only; the request carries no requester filter added by the screen · step 2 → Approve and Reject are not offered
Test data    : values of the AC
<!-- TC:TC-PTY-142:END -->

<!-- TC:TC-PTY-143:START traces=AC-PTY-117,REQ-PTY-103,SCR-PTY-004 -->
### TC-PTY-143 — Request Detail shows the decision outcome
Derived from : AC-PTY-117  (REQ-PTY-103)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F4-SCR-PTY-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW; request CO-000031 for Customer 5001, sale amount 2000, approved by the Owner at 2026-11-02 10:17 (the mock answers API-PTY-020 accordingly) — F-N6
Host data    : none
Steps        : 1. open /pty/credit-overrides/:requestId of CO-000031
Expected     : the read-only request fields show docNo CO-000031, status Approved «موافَق عليه», Customer 5001 (code and name), sale amount 2000 and decided at 2026-11-02 10:17; no decision panel (not PENDING)
Test data    : values of the AC
<!-- TC:TC-PTY-143:END -->

<!-- TC:TC-PTY-144:START traces=AC-PTY-122,REQ-PTY-108,SCR-PTY-003 -->
### TC-PTY-144 — Credit history pane lists changes newest first
Derived from : AC-PTY-122  (REQ-PTY-108)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId (inline credit history pane)
Rule / code  : — 
Package      : F4-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW; Customer 5001 had creditLimit changed on 2026-10-01 and 2026-11-04 and paymentTermDays changed on 2026-10-15 (the mock answers API-PTY-031 newest first)
Host data    : none
Steps        : 1. open /pty/customer-credits/5001 · 2. read the credit history pane «سجل الائتمان»
Expected     : 3 rows in the order 2026-11-04, 2026-10-15, 2026-10-01, each showing field, old value, new value and user; the pane is read-only and pages with the page body (no scroll region of its own)
Test data    : dates of the AC; old/new values are placeholders
<!-- TC:TC-PTY-144:END -->

<!-- TC:TC-PTY-145:START traces=AC-PTY-123,REQ-PTY-109,SCR-PTY-003 -->
### TC-PTY-145 — Unrecorded credit change shown and kept for retry
Derived from : AC-PTY-123  (REQ-PTY-109)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Package      : F2-SCR-PTY-003
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW and _UPDATE; Customer 5001 with credit limit `<previous creditLimit>`; the mock answers API-PTY-017 with 500 PTY-500-AUDIT-NOT-RECORDED
Host data    : none
Steps        : 1. open /pty/customer-credits/5001 · 2. enter credit limit 70000 · 3. Save «حفظ»
Expected     : a form banner: ar «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · en «The change could not be recorded in the audit history, so it was not saved. Try again.» · the form keeps 70000 for retry; the shown standing still reads `<previous creditLimit>`
Test data    : customerId 5001, 70000 (AC); `<previous creditLimit>` placeholder
<!-- TC:TC-PTY-145:END -->

<!-- TC:TC-PTY-179:START traces=AC-PTY-013,REQ-PTY-010,SCR-PTY-002 -->
### TC-PTY-179 — Duplicate email held by a Supplier-only Party opens that Supplier
Derived from : AC-PTY-013  (REQ-PTY-010)
Exercises    : SCR-PTY-002 /pty/suppliers/new
Rule / code  : RULE-PTY-002 → PTY-409-EMAIL-TAKEN
Package      : F2-SCR-PTY-002
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_VIEW, _CREATE and PERM_PTY_CUSTOMER_VIEW; active Party P-000204 holds email "delta@example.com" and only the Supplier role (Supplier 7004). The mock answers API-PTY-006 with 409 PTY-409-EMAIL-TAKEN (text filled with P-000204), API-PTY-026 with an empty page, and API-PTY-028 with Supplier 7004 (email "delta@example.com").
Host data    : none
Steps        : 1. open /pty/suppliers/new · 2. enter a name `<nameEn>` and email "Delta@Example.com" · 3. Save «حفظ» · 4. choose "Open that party" «فتح ذلك الطرف»
Expected     : step 3 → inline on email: ar «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000204). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · en «This email is already registered to another active party (P-000204). Open that party instead of creating a new one.» · step 4 → one API-PTY-026 request with email "Delta@Example.com" and isActive=true, then one API-PTY-028 request with the same filter; the route becomes /pty/suppliers/7004 (ADR-PTY-043)
Test data    : P-000204, Supplier 7004, "Delta@Example.com" (AC-PTY-013 / AC-PTY-136 data); `<nameEn>` placeholder
<!-- TC:TC-PTY-179:END -->

<!-- TC:TC-PTY-180:START traces=AC-PTY-036,REQ-PTY-030,SCR-PTY-001 -->
### TC-PTY-180 — One Save: the record call first, address calls after it, a failing line kept unsent
Derived from : AC-PTY-036  (REQ-PTY-030)
Exercises    : SCR-PTY-001 /pty/customers/new (CUSTOMER-SAVE + ADDRESSES-SYNC)
Rule / code  : RULE-PTY-016 → PTY-422-ADDRESS-TYPE-INVALID
Package      : F2-SCR-PTY-001
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW, _CREATE and _UPDATE. The mock answers API-PTY-001 with 201 (customerPk `<customerPk>`, partyFk `<partyFk>`). It answers the first API-PTY-011 call with 201 and the second with 422 PTY-422-ADDRESS-TYPE-INVALID (the type was deactivated meanwhile).
Host data    : none
Steps        : 1. open /pty/customers/new · 2. enter nameEn `<nameEn>` · 3. add two address lines (DELIVERY "12 Tahrir St" / BILLING "5 Nile St") in the drawer, each confirmed · 4. Save «حفظ»
Expected     : 
- The requests run strictly in order: API-PTY-001 first, then API-PTY-011 for line 1, then API-PTY-011 for line 2. No address call starts before the API-PTY-001 response.
- The route is replaced by /pty/customers/`<customerPk>`.
- Line 1 shows as saved. Line 2 stays in the draft marked unsent, with an inline message on its address type: ar «اختر نوع عنوان من القائمة.» · en «Choose an address type from the list.»
- No API-PTY-015 call is made, because the first line of a type becomes default on the server.
Test data    : DELIVERY, "12 Tahrir St" (AC-PTY-036); other values placeholders
<!-- TC:TC-PTY-180:END -->

<!-- TC:TC-PTY-181:START traces=AC-PTY-055,REQ-PTY-047,SCR-PTY-001 -->
### TC-PTY-181 — Commands disabled while the form is dirty; leaving asks to confirm
Derived from : AC-PTY-055  (REQ-PTY-047)
Exercises    : SCR-PTY-001 /pty/customers/:customerId
Rule / code  : — (dirty tracking — ADR-PTY-034 §5)
Package      : F3-SCR-PTY-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW, _UPDATE, _DELETE and PERM_PTY_SUPPLIER_CREATE; Customer 5001 loaded from the mock
Host data    : none
Steps        : 1. open /pty/customers/5001 · 2. change the mobile without saving · 3. inspect Deactivate «إيقاف» and Make supplier «إضافة كمورّد» · 4. navigate to /pty/customers · 5. cancel the prompt · 6. Save «حفظ» (mock 200) · 7. inspect the commands again
Expected     : step 3 → both commands disabled · step 4 → the prompt ar «لديك تغييرات غير محفوظة. هل تريد المغادرة؟» / en "You have unsaved changes. Leave anyway?" is shown · step 5 → the route stays /pty/customers/5001 with the typed mobile kept · step 7 → both commands enabled again
Test data    : customerId 5001; the mobile value is a placeholder
<!-- TC:TC-PTY-181:END -->

<!-- TC:TC-PTY-182:START traces=AC-PTY-080,REQ-PTY-072,SCR-PTY-003 -->
### TC-PTY-182 — Flag switched off mid-session: the credit save is refused and the credit screens close
Derived from : AC-PTY-080  (REQ-PTY-072)
Exercises    : SCR-PTY-003 /pty/customer-credits/:customerId
Rule / code  : RULE-PTY-023 → PTY-422-CREDIT-NOT-ENABLED
Package      : F2-SCR-PTY-003
Scenario     : STATE · data class EDGE · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW and _UPDATE. `b2bCredit` is on when /pty/customer-credits/5001 is opened. The flag is then switched off: the mock's app-config answers false from now on, and the mock answers API-PTY-017 with 422 PTY-422-CREDIT-NOT-ENABLED.
Host data    : none
Steps        : 1. on the open screen, enter creditLimit 1000 · 2. Save «حفظ» · 3. open the Party menu · 4. deep-link to /pty/customer-credits/5001
Expected     : step 2 → form banner ar «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · en «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» and the application configuration is re-read · step 3 → no Credit «الائتمان» group · step 4 → the route is not mounted (not-found / home)
Test data    : customerId 5001, creditLimit 1000 (AC)
<!-- TC:TC-PTY-182:END -->

<!-- TC:TC-PTY-183:START traces=AC-PTY-018,REQ-PTY-013,SCR-PTY-001 -->
### TC-PTY-183 — Customer search with no match shows the empty state
Derived from : AC-PTY-018  (REQ-PTY-013)
Exercises    : SCR-PTY-001 /pty/customers
Rule / code  : — 
Package      : F2-SCR-PTY-001
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: an Owner session holding PERM_PTY_CUSTOMER_VIEW and _CREATE; the mock answers API-PTY-026 with an empty page (totalElements 0)
Host data    : none
Steps        : 1. open /pty/customers?customerGroupCode=WHOLESALE&isActive=true
Expected     : the list shows ar «لا يوجد عملاء مطابقون» / en "No matching customers" with the action "New customer" «عميل جديد»; no error banner
Test data    : WHOLESALE (seeded), filters of AC-PTY-018
<!-- TC:TC-PTY-183:END -->

<!-- TC:TC-PTY-184:START traces=AC-PTY-117,REQ-PTY-103,SCR-PTY-004 -->
### TC-PTY-184 — An unknown request opens the not-found state
Derived from : AC-PTY-117  (REQ-PTY-103)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : PLATFORM-STD → PTY-404-CREDIT-OVERRIDE-NOT-FOUND
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW; the mock answers API-PTY-020 for id 999999 with 404 PTY-404-CREDIT-OVERRIDE-NOT-FOUND
Host data    : none
Steps        : 1. open /pty/credit-overrides/999999
Expected     : the not-found state ar «الطلب غير موجود» / en "Request not found" is shown; no decision panel and no unauthorized page
Test data    : requestId 999999 (test-local)
<!-- TC:TC-PTY-184:END -->

<!-- TC:TC-PTY-185:START traces=AC-PTY-115,REQ-PTY-101,SCR-PTY-004 -->
### TC-PTY-185 — A server failure on the request list shows the error state with Retry
Derived from : AC-PTY-115  (REQ-PTY-101)
Exercises    : SCR-PTY-004 /pty/credit-overrides
Rule / code  : PLATFORM-STD → PTY-500-INTERNAL
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; the mock answers the first API-PTY-032 call with 500 PTY-500-INTERNAL and the second with the three requests of AC-PTY-115
Host data    : none
Steps        : 1. open /pty/credit-overrides · 2. choose "Retry" «إعادة المحاولة»
Expected     : step 1 → the error state ar «تعذّر تحميل البيانات» / en "The data could not be loaded" with Retry · step 2 → rows CO-000036, CO-000035, CO-000031 in that order
Test data    : values of AC-PTY-115
<!-- TC:TC-PTY-185:END -->

<!-- TC:TC-PTY-186:START traces=AC-PTY-078,REQ-PTY-070,SCR-PTY-004 -->
### TC-PTY-186 — Credit override routes not mounted while the flag is off
Derived from : AC-PTY-078  (REQ-PTY-070)
Exercises    : SCR-PTY-004 /pty/credit-overrides, /pty/credit-overrides/new, /pty/credit-overrides/:requestId
Rule / code  : — (route guard `RequireFlag('b2bCredit')`)
Package      : F4-SCR-PTY-004
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; an Owner session holding every PTY permission
Host data    : none
Steps        : 1. deep-link to each of the three routes
Expected     : each route is not mounted (not-found / home); no API-PTY-019, 020 or 032 request is sent
Test data    : none beyond the AC
<!-- TC:TC-PTY-186:END -->

<!-- TC:TC-PTY-187:START traces=AC-PTY-078,REQ-PTY-070,SCR-PTY-005 -->
### TC-PTY-187 — Credit control settings route not mounted while the flag is off
Derived from : AC-PTY-078  (REQ-PTY-070)
Exercises    : SCR-PTY-005 /pty/credit-settings
Rule / code  : — (route guard `RequireFlag('b2bCredit')`)
Package      : F4-SCR-PTY-005
Scenario     : STATE · data class VALID · language ALL
Preconditions: `b2bCredit` off; an Owner session holding every PTY permission
Host data    : none
Steps        : 1. deep-link to /pty/credit-settings
Expected     : the route is not mounted (not-found / home); no API-PTY-024 request is sent
Test data    : none beyond the AC
<!-- TC:TC-PTY-187:END -->

<!-- TC:TC-PTY-188:START traces=AC-PTY-023,REQ-PTY-018,SCR-PTY-002 -->
### TC-PTY-188 — A Warehouse user sees Suppliers read-only
Derived from : AC-PTY-023  (REQ-PTY-018)
Exercises    : SCR-PTY-002 /pty/suppliers, /pty/suppliers/:supplierId
Rule / code  : — 
Package      : SEC-FE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: a Warehouse session holding PERM_PTY_SUPPLIER_VIEW only; the mock answers API-PTY-028 with Suppliers P-000201 and P-000202 and API-PTY-007 with Supplier 7002
Host data    : none
Steps        : 1. open /pty/suppliers with name "Delta" and active = yes · 2. open Supplier 7002 · 3. deep-link to /pty/suppliers/new
Expected     : step 1 → rows P-000201 and P-000202; no "New supplier" «مورّد جديد» · step 2 → every control read-only; no Save, Deactivate, Reactivate or Make customer; the Addresses pane has no Add, Remove or Make default · step 3 → the unauthorized page
Test data    : values of AC-PTY-023
<!-- TC:TC-PTY-188:END -->

<!-- TC:TC-PTY-189:START traces=AC-PTY-132,REQ-PTY-113,SCR-PTY-004 -->
### TC-PTY-189 — A second request for the same sale refused on the form
Derived from : AC-PTY-132  (REQ-PTY-113)
Exercises    : SCR-PTY-004 /pty/credit-overrides/new
Rule / code  : RULE-PTY-034 → PTY-409-REQUEST-OPEN-FOR-SALE
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: `b2bCredit` on, BLOCK_WITH_OVERRIDE; a Seller/Cashier session holding PERM_PTY_CREDIT_OVERRIDE_VIEW, _CREATE and PERM_PTY_CUSTOMER_VIEW; the mock answers API-PTY-019 with 409 PTY-409-REQUEST-OPEN-FOR-SALE (text filled with CO-000031)
Host data    : none
Steps        : 1. open /pty/credit-overrides/new?customerId=5001&saleAmount=2000&sourceDocumentRef=POS-1-000455 · 2. "Request approval" «طلب موافقة»
Expected     : form banner ar «لهذا البيع طلب موافقة مفتوح بالفعل (CO-000031). تابع ذلك الطلب بدلاً من إنشاء طلب جديد.» · en «This sale already has an open approval request (CO-000031). Follow that request instead of creating a new one.» · no navigation; the action "show this sale's requests" opens /pty/credit-overrides?customerId=5001&statusCode=PENDING
Test data    : values of the AC
<!-- TC:TC-PTY-189:END -->

<!-- TC:TC-PTY-190:START traces=AC-PTY-134,REQ-PTY-109,SCR-PTY-004 -->
### TC-PTY-190 — An approval the audit cannot record is shown and the request stays pending
Derived from : AC-PTY-134  (REQ-PTY-109)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : RULE-PTY-030 → PTY-500-AUDIT-NOT-RECORDED
Package      : F2-SCR-PTY-004
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW and _UPDATE; PENDING request CO-000031; the mock answers API-PTY-021 with 500 PTY-500-AUDIT-NOT-RECORDED and the refetch of API-PTY-020 with PENDING
Host data    : none
Steps        : 1. open the Detail of CO-000031 · 2. Approve «موافقة» and confirm
Expected     : a banner on the decision panel — not the generic error page: ar «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · en «The change could not be recorded in the audit history, so it was not saved. Try again.» · the request is re-read and still shows Pending «معلّق» with the decision panel
Test data    : CO-000031 (AC)
<!-- TC:TC-PTY-190:END -->
<!-- SUB:UI-FLOWS:END -->

<!-- SUB:INT-FLOW:START traces=REQ-PTY-008,REQ-PTY-024,REQ-PTY-026,REQ-PTY-073,REQ-PTY-077,AC-PTY-010,AC-PTY-011,AC-PTY-029,AC-PTY-031,AC-PTY-082,AC-PTY-087,SCR-PTY-001,SCR-PTY-002,SCR-PTY-003 -->
### INT-FLOW — flows across screens, host documents and the flag lifecycle

<!-- TC:TC-PTY-146:START traces=AC-PTY-010,REQ-PTY-008,SCR-PTY-001 -->
### TC-PTY-146 — Quick register hands the new Customer back to the sale
Derived from : AC-PTY-010  (REQ-PTY-008)
Exercises    : SCR-PTY-001 Quick register sub-view (`CustomerQuickRegister`, props onRegistered / onCancel)
Rule / code  : — 
Package      : F2-SCR-PTY-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: a Seller/Cashier session holding PERM_PTY_CUSTOMER_CREATE; an open sale (test host — F-N6) owning the sub-view's container; the mock answers API-PTY-001 with 201 carrying customerPk `<customerPk>` and code `<code>`
Host data    : none
Steps        : 1. open the Quick register from the sale · 2. enter nameEn "Omar Said" · 3. Save «حفظ»
Expected     : onRegistered is called once with {id: `<customerPk>`, code: `<code>`} of the response; onCancel is not called; the route does not change (the sale screen stays open)
Test data    : nameEn "Omar Said" (AC); `<customerPk>`, `<code>` placeholders from the mock response
<!-- TC:TC-PTY-146:END -->

<!-- TC:TC-PTY-147:START traces=AC-PTY-011,REQ-PTY-008,SCR-PTY-002 -->
### TC-PTY-147 — Quick register hands the new Supplier back to the purchase document
Derived from : AC-PTY-011  (REQ-PTY-008)
Exercises    : SCR-PTY-002 Quick register sub-view (`SupplierQuickRegister`)
Rule / code  : — 
Package      : F2-SCR-PTY-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_CREATE; an open purchase document (test host — F-N6); the mock answers API-PTY-006 with 201 carrying supplierPk `<supplierPk>` and code `<code>`
Host data    : none
Steps        : 1. open the Quick register from the purchase document · 2. enter nameAr «مؤسسة النور» · 3. Save «حفظ»
Expected     : onRegistered is called once with {id: `<supplierPk>`, code: `<code>`}; the route does not change
Test data    : nameAr «مؤسسة النور» (AC); `<supplierPk>`, `<code>` placeholders from the mock response
<!-- TC:TC-PTY-147:END -->

<!-- TC:TC-PTY-148:START traces=AC-PTY-029,REQ-PTY-024,SCR-PTY-002,SCR-PTY-001 -->
### TC-PTY-148 — Mobile changed on the Supplier screen shows on the Customer screen
Derived from : AC-PTY-029  (REQ-PTY-024)
Exercises    : SCR-PTY-002 /pty/suppliers/:supplierId → SCR-PTY-001 /pty/customers/:customerId
Rule / code  : — 
Package      : F2-SCR-PTY-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_VIEW, _UPDATE and PERM_PTY_CUSTOMER_VIEW; Party P-000130 holds a Customer `<customerId>` and a Supplier `<supplierId>`; the Customer screen of `<customerId>` was opened once before (its read is cached); the mock answers API-PTY-008 and the following API-PTY-002 with mobile "+966500001111"
Host data    : none
Steps        : 1. open /pty/suppliers/{supplierId} · 2. change mobile to "+966500001111" · 3. Save «حفظ» · 4. open /pty/customers/{customerId}
Expected     : step 3 → one API-PTY-008 call; no address call (the address set is unchanged) · step 4 → the Customer is re-read (its cached entry was invalidated by the Supplier save) and shows mobile "+966500001111"
Test data    : Party P-000130, "+966500001111" (AC); role identifiers are placeholders
<!-- TC:TC-PTY-148:END -->

<!-- TC:TC-PTY-149:START traces=AC-PTY-031,REQ-PTY-026,SCR-PTY-002,SCR-PTY-001 -->
### TC-PTY-149 — "Make customer" opens the new RETAIL Customer
Derived from : AC-PTY-031  (REQ-PTY-026)
Exercises    : SCR-PTY-002 /pty/suppliers/:supplierId → SCR-PTY-001 /pty/customers/:customerId
Rule / code  : — 
Package      : F4-SCR-PTY-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: an Owner session holding PERM_PTY_SUPPLIER_VIEW and PERM_PTY_CUSTOMER_CREATE, _VIEW; Supplier 7004 of Party P-000204 and no Customer for that Party; the mock answers API-PTY-001 with 201 (partyFk of P-000204, customerGroupCode RETAIL, isActiveFl true)
Host data    : none
Steps        : 1. open /pty/suppliers/7004 · 2. choose "Make customer" «إضافة كعميل» and confirm
Expected     : the API-PTY-001 body is {partyId} only (no Party field asked) · the route becomes /pty/customers/:customerPk of the response, showing customer group Retail «تجزئة» and the Customer active
Test data    : Supplier 7004, Party P-000204 (AC)
<!-- TC:TC-PTY-149:END -->

<!-- TC:TC-PTY-150:START traces=AC-PTY-082,REQ-PTY-073,SCR-PTY-001 -->
### TC-PTY-150 — Credit appears on the next screen load after the flag is turned on
Derived from : AC-PTY-082  (REQ-PTY-073)
Exercises    : SCR-PTY-001 /pty/customers/:customerId and the Party menu
Rule / code  : — 
Package      : F2-SCR-PTY-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: an Owner session holding every PTY permission, on /pty/customers/5001 with `b2bCredit` off (no credit group, no Credit menu group)
Host data    : none
Steps        : 1. turn `b2bCredit` on in the given application configuration (the mock's app-config now answers true; API-PTY-002 now carries the credit members) · 2. reload /pty/customers/5001
Expected     : the application configuration is re-read on the route mount · the Customer screen shows the credit limit and payment terms (read-only — ADR-PTY-033) · the menu shows the Credit «الائتمان» group with Customer credit «ائتمان العملاء», Credit override requests «طلبات تجاوز حد الائتمان» and Credit control settings «إعدادات ضبط الائتمان» · 0 setup steps are presented
Test data    : customerId 5001 (AC)
<!-- TC:TC-PTY-150:END -->

<!-- TC:TC-PTY-151:START traces=AC-PTY-087,REQ-PTY-077,SCR-PTY-003 -->
### TC-PTY-151 — Next action of the refused switch-off opens the filtered credit list
Derived from : AC-PTY-087  (REQ-PTY-077)
Exercises    : SCR-PTY-003 /pty/customer-credits?carriesCredit=true
Rule / code  : — 
Package      : F4-SCR-PTY-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CUSTOMER_CREDIT_VIEW; the refusal of AC-PTY-085 is shown by the flag owner (F-N8); 4 Customers carry credit (the mock answers API-PTY-030 with carriesCredit=true by those 4)
Host data    : none
Steps        : 1. choose "Show customers with credit" «عرض العملاء ذوي الائتمان» — the route /pty/customer-credits?carriesCredit=true opens
Expected     : the carries-credit filter shows yes (seeded from the route) and the table lists the 4 Customers
Test data    : count 4 (AC); Customer identities are placeholders
<!-- TC:TC-PTY-151:END -->
<!-- SUB:INT-FLOW:END -->
<!-- PHASE:TEST-PLAN-FE:END -->

<!-- PHASE:INT-UXD:START traces=UXD-PTY-001,REQ-PTY-094,REQ-PTY-103,AC-PTY-117,SCR-PTY-004 -->
## INT-UXD — foreign-owned field rendered by this module

One UXD (UXD-PTY-001 — `decidedBy`, a user of the given `erp-core:modules.sec`, rendered on SCR-PTY-004 through USER-LABEL-LOOKUP); 2 TCs (≤ 8 — no SUB), grouped under the owner `erp-core:modules.sec` (given).

<!-- TC:TC-PTY-152:START traces=UXD-PTY-001,REQ-PTY-094,REQ-PTY-103,AC-PTY-117,SCR-PTY-004 -->
### TC-PTY-152 — Decider rendered by name on the request Detail
Derived from : UXD-PTY-001  (REQ-PTY-094, REQ-PTY-103, AC-PTY-117)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F4-SCR-PTY-004
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: `b2bCredit` on; an Owner session holding PERM_PTY_CREDIT_OVERRIDE_VIEW; request CO-000031 APPROVED with decidedBy = the identifier `<deciderId>` of the deciding Owner; the given sec user read answers `<deciderId>` with display name `<decider name>` (owner present)
Host data    : none
Steps        : 1. open the Detail of CO-000031
Expected     : "Decided by" «صاحب القرار» shows `<decider name>` — never the raw identifier; one sec user read is made for `<deciderId>`
Test data    : CO-000031 (AC-PTY-117); `<deciderId>`, `<decider name>` placeholders (the owner's data)
<!-- TC:TC-PTY-152:END -->

<!-- TC:TC-PTY-153:START traces=UXD-PTY-001,REQ-PTY-094,REQ-PTY-103,AC-PTY-117,SCR-PTY-004 -->
### TC-PTY-153 — Decider read failing shows the declared empty value
Derived from : UXD-PTY-001  (REQ-PTY-094, REQ-PTY-103, AC-PTY-117)
Exercises    : SCR-PTY-004 /pty/credit-overrides/:requestId
Rule / code  : — 
Package      : F4-SCR-PTY-004
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: as TC-PTY-152, but the given sec user read for `<deciderId>` fails (owner read unavailable)
Host data    : none
Steps        : 1. open the Detail of CO-000031
Expected     : "Decided by" «صاحب القرار» reads «—»; the raw identifier is not shown; no error page — every other field of the Detail renders as in TC-PTY-143
Test data    : CO-000031 (AC-PTY-117); `<deciderId>` placeholder
<!-- TC:TC-PTY-153:END -->
<!-- PHASE:INT-UXD:END -->

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
| AC covered by the module (both plans) | 136 / 136 ✓ — no gap |
| REQ covered on this track | 33 (incl. REQ-PTY-070 and REQ-PTY-077, covered here only) |
| Frontend statements of G17 with a TC | 8 / 8 (F-N10) |
| SCR covered | 5 / 5 ✓ |
| UXD covered | 1 / 1 ✓ (rendered + owner failure) |
| Frontend plan units with ≥ 1 TC | 16 / 22 — F1-SCR-PTY-001 … 005 and ALIGN-FE carry none (F-N2) |
| TC / AC on this track | 42 module TCs / 36 ACs ≈ 1.17 (+ 2 UXD TCs); guard ~2× not exceeded |
| Host data | none (F-N9) |
| Open plan gap recorded for the gate | none — F-N4 removed: TC-PTY-138's actor (an Owner) loads the request on the delivered backend |
