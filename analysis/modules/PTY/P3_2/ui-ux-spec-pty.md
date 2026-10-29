# UI/UX spec — Party / الأطراف (PTY) v1
══════════════════════════════════════════════════════════════════
Stage   : P3.2 Part A — UX design · Module : PTY · Version : v1 · Profile : commerce
Inputs  : srs (A3 fields and labels, A5 messages, B1–B5 per screen) · prd · api-spec-pty.yaml
Mints   : SCR-PTY-001 … SCR-PTY-005 (one per SRS screen entry) · UXD-PTY-001
Decisions: ADR-PTY-033 (credit values on the Customer screen) · ADR-PTY-034 (frontend binding decisions) · ADR-PTY-043 (the Party named by a RULE-PTY-002 refusal, supersedes ADR-PTY-034 §9) · ADR-PTY-037 (currency hidden while the tenant has one)
══════════════════════════════════════════════════════════════════

Common conventions (apply to every block below):
- Labels are bilingual; the active language shows its label (ar right-to-left, en left-to-right). Labels are the SRS A3 labels, quoted as `ar / en`.
- Lookup values (CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT, CREDIT_OVERRIDE_STATUS) are shown with their `mdl` label in the active language (SRS A6), never as raw codes.
- Amounts are in the tenant's base currency, formatted for the active language.
- "Advanced" is a collapsed group opened by «خيارات متقدمة / Advanced» (progressive disclosure, KB §6.1).
- A **command** is a state change with its own confirmation (Deactivate, Reactivate, Make supplier, Make customer, Clear credit, Approve, Reject, Cancel request). A command is never a second save: it is disabled while the screen's form holds unsaved changes (ADR-PTY-034).
- Credit screens and credit values exist only while `b2bCredit` is on (REQ-PTY-070, REQ-PTY-073).
- Permissions are the SRS B4 matrix; names follow `PERM_<PAGE_CODE>_<ACTION>`; `VIEW` is the gateway of every screen.

## SCR-PTY-001 — العملاء / Customers                 traces=REQ-PTY-001,REQ-PTY-004,REQ-PTY-005,REQ-PTY-008,REQ-PTY-012,REQ-PTY-013,REQ-PTY-014,REQ-PTY-015,REQ-PTY-025,REQ-PTY-030,REQ-PTY-035,REQ-PTY-070,REQ-PTY-071,AC-PTY-007,AC-PTY-010,AC-PTY-078,AC-PTY-082
SRS screen        : SCR-REQ-PTY-001 · page code PTY_CUSTOMER · entities ENT-PTY-001, ENT-PTY-002, ENT-PTY-004
UI pattern        : Search + Entry (composite) — flat record with a repeating Address section
Sub-views         : Search · Entry (· Quick register — the Entry's primary field group opened in place from a sale, REQ-PTY-008) under this ONE SCR
Fields shown      : Search filters = result columns: code (رقم العميل / Customer code) · name (الاسم بالعربية / Name (Arabic), الاسم بالإنجليزية / Name (English)) · mobile (رقم الجوال / Mobile) · email (البريد الإلكتروني / Email) · taxRegistrationNo (الرقم الضريبي / Tax registration number) · customerGroupCode (مجموعة العملاء / Customer group, lookup CUSTOMER_GROUP) · isActiveFl (نشط / Active); the walk-in Customer is marked by isWalkInFl (عميل نقدي / Walk-in customer) in the list.
                    Entry: code (رقم العميل / Customer code, read-only, RULE-PTY-007) · nameAr (الاسم بالعربية / Name (Arabic)) · nameEn (الاسم بالإنجليزية / Name (English)) — one mandatory mark on the name group (RULE-PTY-001) · customerGroupCode (مجموعة العملاء / Customer group, mandatory, prefilled RETAIL, RULE-PTY-018) · mobile (رقم الجوال / Mobile, RULE-PTY-004) · email (البريد الإلكتروني / Email, RULE-PTY-002, RULE-PTY-003) · Advanced: taxRegistrationNo (الرقم الضريبي / Tax registration number) · isActiveFl (نشط / Active, read-only) · isWalkInFl (عميل نقدي / Walk-in customer, read-only mark) · creditLimit (حد الائتمان / Credit limit, read-only) and paymentTermDays (مدة السداد (بالأيام) / Payment terms (days), read-only) — shown only while `b2bCredit` is on and present in the read, never editable here (ADR-PTY-033) · record footer, read-only: createdBy (أنشأه / Created by), createdAt (تاريخ الإنشاء / Created at), updatedBy (عدّله / Updated by), updatedAt (تاريخ التعديل / Updated at).
                    Addresses pane (ENT-PTY-004, one row per active Address): addressTypeCode (نوع العنوان / Address type, mandatory, lookup ADDRESS_TYPE, RULE-PTY-016) · line1 (العنوان (السطر الأول) / Address line 1, mandatory, RULE-PTY-015) · city (المدينة / City) · nameAr (اسم العنوان بالعربية / Address label (Arabic)) · nameEn (اسم العنوان بالإنجليزية / Address label (English)) · Advanced: line2 (العنوان (السطر الثاني) / Address line 2), region (المنطقة / المحافظة / Region), postalCode (الرمز البريدي / Postal code) · isDefaultFl (افتراضي / Default, one per type, RULE-PTY-017) · code (رمز العنوان / Address code, read-only).
                    Quick register: nameAr, nameEn (one mandatory mark on the name group — AC-PTY-007), mobile, email; nothing else (customer group defaults to RETAIL on the server, REQ-PTY-007).
Composition       : container page · Addresses → inline (pane beneath the header; one Address line is added or edited in a one-line drawer that returns the line to the page) · Quick register → none · submits: one
Permissions       : PTY_CUSTOMER — Owner VIEW · CREATE · UPDATE · DELETE; Seller/Cashier VIEW · CREATE · UPDATE; Warehouse none → PERM_PTY_CUSTOMER_VIEW (gateway), PERM_PTY_CUSTOMER_CREATE (New customer, Quick register), PERM_PTY_CUSTOMER_UPDATE (Save of an existing Customer, every Address change), PERM_PTY_CUSTOMER_DELETE (Deactivate, Reactivate). "Make supplier" also needs PERM_PTY_SUPPLIER_CREATE. "Edit credit" needs PERM_PTY_CUSTOMER_CREDIT_VIEW.
Cross-module data : none
Navigation        : routes /pty/customers (Search; filters live in the URL) · /pty/customers/new · /pty/customers/:customerId · Address line drawer /pty/customers/:customerId/addresses/new | /pty/customers/:customerId/addresses/:addressKey (same under /pty/customers/new while registering).
                    In: menu Party → Customers; global search result → /pty/customers/:customerId (customerPk identifies one Customer); a sale → Quick register mounted in place, returns {customerPk, code}; the RULE-PTY-002 refusal action "Open that party" «فتح ذلك الطرف» → /pty/customers/:customerPk of the active Customer holding the entered email, else /pty/suppliers/:supplierPk of the active Supplier holding it (email is unique among active Parties, so it identifies one Party; found through the Customer then the Supplier search by email — ADR-PTY-043).
                    Out: "Edit credit" «تعديل الائتمان» → SCR-PTY-003 /pty/customer-credits/:customerId (flag on); after "Make supplier" «إضافة كمورّد» → SCR-PTY-002 /pty/suppliers/:supplierPk of the created Supplier. partyFk is carried for the Address calls only and is not rendered.
States            : empty — Search: «لا يوجد عملاء مطابقون» / "No matching customers" with "New customer" «عميل جديد» (CREATE); Addresses pane: «لا توجد عناوين» / "No addresses" with "Add address" «إضافة عنوان» (UPDATE) · loading — local placeholder rows in the list and the record; the Save button shows progress and is disabled · error — generic «تعذّر تحميل البيانات» / "The data could not be loaded" with "Retry" «إعادة المحاولة»; a missing record shows «العميل غير موجود» / "Customer not found" with "Back to customers" (catalog codes are Part B's) · offline — not required by the SRS.
Actions           : Save «حفظ» → create (REQ-PTY-004) / update (REQ-PTY-012) with the Address changes · [cmd] Deactivate «إيقاف» → REQ-PTY-014 · [cmd] Reactivate «إعادة تنشيط» → REQ-PTY-015 · [cmd] Make supplier «إضافة كمورّد» → REQ-PTY-025 · Address line: Confirm «تأكيد» (returns the line) · Remove «إزالة» · Make default «تعيين كافتراضي» (all applied by the one Save).

## SCR-PTY-002 — الموردون / Suppliers                traces=REQ-PTY-008,REQ-PTY-016,REQ-PTY-017,REQ-PTY-018,REQ-PTY-020,REQ-PTY-021,REQ-PTY-022,REQ-PTY-026,REQ-PTY-030,REQ-PTY-035,AC-PTY-011,AC-PTY-021
SRS screen        : SCR-REQ-PTY-002 · page code PTY_SUPPLIER · entities ENT-PTY-001, ENT-PTY-003, ENT-PTY-004
UI pattern        : Search + Entry (composite) — flat record with a repeating Address section
Sub-views         : Search · Entry (· Quick register — the Entry's primary field group opened in place from a purchase document, REQ-PTY-008) under this ONE SCR
Fields shown      : Search filters = result columns: code (رقم المورّد / Supplier code) · name (الاسم بالعربية / Name (Arabic), الاسم بالإنجليزية / Name (English)) · mobile (رقم الجوال / Mobile) · email (البريد الإلكتروني / Email — SRS B2, ADR-PTY-043) · taxRegistrationNo (الرقم الضريبي / Tax registration number) · isActiveFl (نشط / Active).
                    Entry: code (رقم المورّد / Supplier code, read-only, RULE-PTY-007) · nameAr (الاسم بالعربية / Name (Arabic)) · nameEn (الاسم بالإنجليزية / Name (English)) — one mandatory mark on the name group (RULE-PTY-001) · mobile (رقم الجوال / Mobile, RULE-PTY-004) · email (البريد الإلكتروني / Email, RULE-PTY-002, RULE-PTY-003) · paymentTermDays (مدة السداد (بالأيام) / Payment terms (days), RULE-PTY-008; shown whatever the state of `b2bCredit`, ADR-PTY-012) · Advanced: taxRegistrationNo (الرقم الضريبي / Tax registration number) · isActiveFl (نشط / Active, read-only) · record footer, read-only: createdBy (أنشأه / Created by), createdAt (تاريخ الإنشاء / Created at), updatedBy (عدّله / Updated by), updatedAt (تاريخ التعديل / Updated at).
                    Addresses pane: as SCR-PTY-001 (same fields, labels and RULE ids).
                    Quick register: nameAr, nameEn (one mandatory mark on the name group — AC-PTY-021), mobile, email.
Composition       : container page · Addresses → inline (pane beneath the header; one Address line is added or edited in a one-line drawer that returns the line to the page) · Quick register → none · submits: one
Permissions       : PTY_SUPPLIER — Owner VIEW · CREATE · UPDATE · DELETE; Seller/Cashier none; Warehouse VIEW → PERM_PTY_SUPPLIER_VIEW (gateway; a Warehouse user sees the Entry read-only), PERM_PTY_SUPPLIER_CREATE (New supplier, Quick register), PERM_PTY_SUPPLIER_UPDATE (Save of an existing Supplier, every Address change), PERM_PTY_SUPPLIER_DELETE (Deactivate, Reactivate). "Make customer" also needs PERM_PTY_CUSTOMER_CREATE.
Cross-module data : none
Navigation        : routes /pty/suppliers (Search; filters live in the URL) · /pty/suppliers/new · /pty/suppliers/:supplierId · Address line drawer /pty/suppliers/:supplierId/addresses/new | /pty/suppliers/:supplierId/addresses/:addressKey (same under /pty/suppliers/new).
                    In: menu Party → Suppliers; a purchase document → Quick register mounted in place, returns {supplierPk, code}; SCR-PTY-001 "Make supplier" → /pty/suppliers/:supplierPk.
                    Out: after "Make customer" «إضافة كعميل» → SCR-PTY-001 /pty/customers/:customerPk of the created Customer; the RULE-PTY-002 refusal action "Open that party" «فتح ذلك الطرف» → as SCR-PTY-001. partyFk is carried for the Address calls only and is not rendered.
States            : empty — Search: «لا يوجد موردون مطابقون» / "No matching suppliers" with "New supplier" «مورّد جديد» (CREATE); Addresses pane: «لا توجد عناوين» / "No addresses" · loading — local placeholder rows; Save shows progress · error — generic «تعذّر تحميل البيانات» / "The data could not be loaded" with "Retry"; a missing record shows «المورّد غير موجود» / "Supplier not found" · offline — not required by the SRS.
Actions           : Save «حفظ» → create (REQ-PTY-016) / update (REQ-PTY-017) with the Address changes · [cmd] Deactivate «إيقاف» → REQ-PTY-020 · [cmd] Reactivate «إعادة تنشيط» → REQ-PTY-021 · [cmd] Make customer «إضافة كعميل» → REQ-PTY-026 · Address line actions as SCR-PTY-001.

## SCR-PTY-003 — ائتمان العملاء / Customer credit     traces=REQ-PTY-064,REQ-PTY-065,REQ-PTY-068,REQ-PTY-069,REQ-PTY-077,REQ-PTY-078,REQ-PTY-085,REQ-PTY-108,AC-PTY-077,AC-PTY-087,AC-PTY-096,AC-PTY-122
SRS screen        : SCR-REQ-PTY-003 · page code PTY_CUSTOMER_CREDIT · entities ENT-PTY-001, ENT-PTY-002 · exists only while `b2bCredit` is on
UI pattern        : Search + Entry (composite) — flat record list with a per-Customer history panel
Sub-views         : Search · Entry (credit standing + credit history) under this ONE SCR
Fields shown      : Search filters: code (رقم العميل / Customer code) · name (الاسم بالعربية / Name (Arabic), الاسم بالإنجليزية / Name (English)) · customerGroupCode (مجموعة العملاء / Customer group) · carries-credit «لديه ائتمان / Carries credit» (yes / no / any); result columns add creditLimit (حد الائتمان / Credit limit) and paymentTermDays (مدة السداد (بالأيام) / Payment terms (days)).
                    Entry: code, nameAr, nameEn, customerGroupCode (read-only, labels as above) · isWalkInFl (عميل نقدي / Walk-in customer, read-only mark) · creditLimit (حد الائتمان / Credit limit, editable, empty = no credit allowance, RULE-PTY-022, RULE-PTY-011) · paymentTermDays (مدة السداد (بالأيام) / Payment terms (days), editable, RULE-PTY-008, RULE-PTY-011) · open exposure «المديونية القائمة / Open exposure» (read-only; «غير متاحة / Unavailable» when the exposure could not be computed) · available credit «الائتمان المتاح / Available credit» (read-only, credit limit minus open exposure, REQ-PTY-085).
                    Credit history pane (read-only, newest first, REQ-PTY-108): field «الحقل / Field» · old value «القيمة السابقة / Old value» · new value «القيمة الجديدة / New value» · user «المستخدم / User» · time «الوقت / Time».
Composition       : container page · Credit history → inline (read-only pane beneath the credit fields, paged with the body, no save of its own) · submits: one
Permissions       : PTY_CUSTOMER_CREDIT — Owner VIEW · UPDATE; Seller/Cashier VIEW; Warehouse none → PERM_PTY_CUSTOMER_CREDIT_VIEW (gateway; Seller/Cashier sees the Entry read-only), PERM_PTY_CUSTOMER_CREDIT_UPDATE (Save, Clear credit). The page is absent from every menu while `b2bCredit` is off. "Credit override requests" link needs PERM_PTY_CREDIT_OVERRIDE_VIEW; "Credit control settings" link needs PERM_PTY_CREDIT_SETTINGS_VIEW.
Cross-module data : none
Navigation        : routes /pty/customer-credits (Search; filters live in the URL) · /pty/customer-credits/:customerId.
                    In: menu Party → Credit → Customer credit; SCR-PTY-001 "Edit credit" → /pty/customer-credits/:customerId; SCR-PTY-004 Detail → /pty/customer-credits/:customerFk; the `b2bCredit` switch-off refusal "Show customers with credit" «عرض العملاء ذوي الائتمان» → /pty/customer-credits?carriesCredit=true (REQ-PTY-077).
                    Out: "Credit override requests" «طلبات تجاوز حد الائتمان» → SCR-PTY-004 /pty/credit-overrides?customerId=:customerId (the filter identifies one Customer); "Credit control settings" «إعدادات ضبط الائتمان» → SCR-PTY-005 /pty/credit-settings.
States            : empty — Search: «لا يوجد عملاء مطابقون» / "No matching customers"; history pane: «لا توجد تغييرات على الائتمان» / "No credit changes yet" · loading — local placeholders; Save shows progress · error — generic «تعذّر تحميل البيانات» / "The data could not be loaded" with "Retry"; flag turned off meanwhile → the screen closes to home with the RULE-PTY-023 message · offline — not required by the SRS.
Actions           : Save «حفظ» → REQ-PTY-064, REQ-PTY-065 · [cmd] Clear credit «مسح الائتمان» → REQ-PTY-078 · Credit history «سجل الائتمان» → the inline pane (REQ-PTY-108).

## SCR-PTY-004 — طلبات تجاوز حد الائتمان / Credit override requests   traces=REQ-PTY-091,REQ-PTY-092,REQ-PTY-094,REQ-PTY-095,REQ-PTY-096,REQ-PTY-101,REQ-PTY-102,REQ-PTY-103,AC-PTY-102,AC-PTY-106,AC-PTY-109,AC-PTY-115,AC-PTY-117,UXD-PTY-001
SRS screen        : SCR-REQ-PTY-004 · page code PTY_CREDIT_OVERRIDE · entities ENT-PTY-005, ENT-PTY-002 · exists only while `b2bCredit` is on
UI pattern        : Search + Entry (composite) — flat record list with a decision panel
Sub-views         : Search · Entry (New request) · Detail (with the decision panel) under this ONE SCR
Fields shown      : Search filters: docNo (رقم الطلب / Request number) · statusCode (حالة الطلب / Request status, lookup CREDIT_OVERRIDE_STATUS) · customerFk (العميل / Customer — chosen in a Customer picker) · docDate (تاريخ الطلب / Request date, range from–to); result columns add saleAmount (مبلغ البيع / Sale amount) · creditLimitAtRequest (حد الائتمان عند الطلب / Credit limit at request) · openExposure (المديونية القائمة / Open exposure) · creditCheckResultCode (نتيجة فحص الائتمان / Credit check result, lookup CREDIT_CHECK_RESULT) · createdBy (مقدّم الطلب / Requested by). The Customer is shown as its code and name.
                    New request: customerFk (العميل / Customer, mandatory, Customer picker, RULE-PTY-033) · saleAmount (مبلغ البيع / Sale amount, mandatory, RULE-PTY-032, RULE-PTY-025) · sourceDocumentRef (مرجع مستند البيع / Sale document reference, mandatory) — all three prefilled when opened from a sale.
                    Detail (read-only): docNo (رقم الطلب / Request number) · docDate (تاريخ الطلب / Request date) · statusCode (حالة الطلب / Request status) · customerFk as code and name (العميل / Customer) · saleAmount · creditLimitAtRequest · openExposure · creditCheckResultCode · sourceDocumentRef (مرجع مستند البيع / Sale document reference) · createdBy (مقدّم الطلب / Requested by) · createdAt (تاريخ الإنشاء / Created at) · decidedBy (صاحب القرار / Decided by — user name, UXD-PTY-001) · decidedAt (تاريخ القرار / Decided at) · decisionReason (سبب القرار / Decision reason) · usedDocumentRef (مستند البيع المستخدِم / Used on sale document) · updatedBy (عدّله / Updated by) · updatedAt (تاريخ التعديل / Updated at).
                    Decision panel (PENDING only): decisionReason (سبب القرار / Decision reason, mandatory on reject, RULE-PTY-027).
Composition       : container page · Decision panel → inline (one reason field and the decision commands beneath the request; no form save of its own) · submits: one (New request: "Request approval"; Detail: the decision)
Permissions       : PTY_CREDIT_OVERRIDE — Owner VIEW · CREATE · UPDATE · DELETE; Seller/Cashier VIEW · CREATE · DELETE; Warehouse none → PERM_PTY_CREDIT_OVERRIDE_VIEW (gateway; users without UPDATE see their own requests only, REQ-PTY-102), PERM_PTY_CREDIT_OVERRIDE_CREATE (New request), PERM_PTY_CREDIT_OVERRIDE_UPDATE (Approve, Reject), PERM_PTY_CREDIT_OVERRIDE_DELETE (Cancel request; the server limits it to the requester, RULE-PTY-029). The Customer picker needs PERM_PTY_CUSTOMER_VIEW. The link to Customer credit needs PERM_PTY_CUSTOMER_CREDIT_VIEW.
Cross-module data : decidedBy (user name) → UXD-PTY-001 (erp-core `sec`, given)
Navigation        : routes /pty/credit-overrides (Search; filters live in the URL) · /pty/credit-overrides/new (optional prefill ?customerId=…&saleAmount=…&sourceDocumentRef=…) · /pty/credit-overrides/:requestId.
                    In: menu Party → Credit → Credit override requests; a sale whose credit check requires approval → /pty/credit-overrides/new with the prefill; the approver's in-app notification (REQ-PTY-093) → /pty/credit-overrides/:requestId (one request); SCR-PTY-003 → /pty/credit-overrides?customerId=:customerId.
                    Out: Detail "Customer credit" «ائتمان العميل» → SCR-PTY-003 /pty/customer-credits/:customerFk.
States            : empty — Search: «لا توجد طلبات» / "No requests" (with "New request" «طلب جديد» for CREATE) · loading — local placeholders; the decision commands show progress · error — generic «تعذّر تحميل البيانات» / "The data could not be loaded" with "Retry"; a missing request shows «الطلب غير موجود» / "Request not found" · offline — not required by the SRS.
Actions           : "Request approval" «طلب موافقة» → REQ-PTY-091 · [cmd] "Approve" «موافقة» → REQ-PTY-094 · [cmd] "Reject" «رفض» → REQ-PTY-095 · [cmd] "Cancel request" «إلغاء الطلب» → REQ-PTY-096. Decision commands are offered only while the status is PENDING.

## SCR-PTY-005 — إعدادات ضبط الائتمان / Credit control settings   traces=REQ-PTY-086,REQ-PTY-087,REQ-PTY-088,REQ-PTY-089,REQ-PTY-090,AC-PTY-097,AC-PTY-098,AC-PTY-101
SRS screen        : SCR-REQ-PTY-005 · page code PTY_CREDIT_SETTINGS · entity ENT-PTY-002 (the setting itself lives in given `cu`) · exists only while `b2bCredit` is on
UI pattern        : single entry screen — flat record (one setting)
Sub-views         : Entry only (one setting per tenant)
Fields shown      : credit-overrun action «إجراء تجاوز حد الائتمان / Credit-overrun action» (mandatory, lookup CREDIT_OVERRUN_ACTION: WARN «تنبيه فقط / Warn only», BLOCK_WITH_OVERRIDE «منع مع إمكانية الموافقة / Block with approval»; BLOCK_WITH_OVERRIDE preselected while nothing is saved, REQ-PTY-087) · a read-only note «القيمة الافتراضية / Default value» while the shown action is the default.
Composition       : container page · Credit-overrun action → none · submits: one
Permissions       : PTY_CREDIT_SETTINGS — Owner VIEW · UPDATE; Seller/Cashier none; Warehouse none → PERM_PTY_CREDIT_SETTINGS_VIEW (gateway), PERM_PTY_CREDIT_SETTINGS_UPDATE (Save; without it the choice is read-only).
Cross-module data : none
Navigation        : route /pty/credit-settings. In: menu Party → Credit → Credit control settings; SCR-PTY-003 "Credit control settings". Out: none.
States            : empty — not applicable (the default action is always shown) · loading — local placeholder; Save shows progress · error — generic «تعذّر تحميل البيانات» / "The data could not be loaded" with "Retry" · offline — not required by the SRS.
Actions           : Save «حفظ» → REQ-PTY-086.

## UXD-PTY-001 — Decided-by user name on Credit override requests   traces=REQ-PTY-094,REQ-PTY-103,AC-PTY-106,AC-PTY-117
Screen            : SCR-PTY-004 (Detail)
Field             : decidedBy (صاحب القرار / Decided by) — the API carries the deciding user's identifier (BIGINT, `CreditOverrideRequest.decidedBy`); the screen renders the user's name
Owner module      : erp-core `modules.sec` (given) — the user directory
Read              : the given `sec` user read, one long-lived cached lookup per user identifier; not an operation of api-spec-pty.yaml (ADR-PTY-034)
Fallback          : while the name cannot be read, the identifier is not shown raw; the field reads «—»

## Reconciliation self-check (A.6)

```
RECONCILIATION — PTY v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)
   → 20 flows, US-PTY-001 … 008 and 011 … 017 used; each flow lands on an SRS screen.
     US-PTY-009 and US-PTY-010 have no PTY screen (SRS A8.1 operations for Storefront) — not used in a flow; no navigation invented.
B2 no RULE-* contradicts a flow/spec outcome
   → RULE-PTY-010 / RULE-PTY-012 refusals are reachable from SCR-PTY-001 commands (AC-PTY-055, AC-PTY-057) — commands are offered, not hidden.
     RULE-PTY-029: Cancel is offered to DELETE holders; the server refuses a non-requester (AC-PTY-109).
     SRS SCR-REQ-PTY-001 B3 "Credit fields are not on this screen" vs AC-PTY-082 "the Customer screen shows the creditLimit and paymentTermDays fields":
     resolved non-breaking in ADR-PTY-033 (read-only values on SCR-PTY-001, editing on SCR-PTY-003).
B3 every field/permission on a screen exists in the SRS
   → extra: none (open exposure / available credit are SRS B3 read-only items; carries-credit is the SRS B2 filter).
     missing: none. secUserId (ENT-PTY-002) is not rendered: no SRS screen lists it and the Customer read does not carry it.
B4 every screen entry of the SRS has exactly one SCR-* block
   → SCR-REQ-PTY-001 → SCR-PTY-001 · SCR-REQ-PTY-002 → SCR-PTY-002 · SCR-REQ-PTY-003 → SCR-PTY-003 · SCR-REQ-PTY-004 → SCR-PTY-004 · SCR-REQ-PTY-005 → SCR-PTY-005
RESULT  reconciled 5 · reworked 1 (SCR-PTY-001 credit values) · ADRs ADR-PTY-033, ADR-PTY-034
```
