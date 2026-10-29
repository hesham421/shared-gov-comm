# SRS — Party / الأطراف (PTY)
══════════════════════════════════════════════════════════════════
Module : PTY   Version : v1   Profile : commerce
Inputs : prd, domain-profile, project-registry (PRD approved — gate prd-approval, commit dd46f92)
Counts : REQ 113 · AC 136 · ENT 5 · RULE 34 · SCR-REQ 5 · ADR 4 raised at P1 (ADR-PTY-014…017; inherited: ADR-PTY-011…013) + gate-analysis revise (ADR-PTY-035, 037, 038, 039, 041, 042, 043 applied here)
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information

| Item | Value |
|---|---|
| Module | Party / الأطراف |
| Feature code | PTY |
| Version | v1 |
| Date | 2026-10-29 |
| Status | DRAFT — P1 pass 1 · gate-analysis revise (findings G1, G4, G11, G12, G13, G15, G19 applied) |
| Prepared by | SRS engine (claude:opus), lane `analysis` |
| Profile | commerce (COMMERCE) |
| Stories covered | 17 / 17 (US-PTY-001 … US-PTY-017) |
| Decisions applied | 4 new ADRs (ADR-PTY-014 … ADR-PTY-017), 3 inherited ADRs (ADR-PTY-011 … ADR-PTY-013), 9 DEFAULTs (D-01 … D-09) |

## A2 — Functional context

### In scope
- One Party identity per counterparty, carrying the names, code, contacts and tax registration number once (US-PTY-001, US-PTY-004).
- The Customer role and the Supplier role of a Party: registration with one mandatory input, update, search, deactivation, reactivation, and adding the second role in place (US-PTY-002, US-PTY-003, US-PTY-004).
- Party addresses typed by address type, with one default per type (US-PTY-005).
- Customer groups on every Customer (US-PTY-006).
- Customers in the one global search (US-PTY-007).
- The tenant's walk-in Customer for unnamed point-of-sale sales (US-PTY-008).
- Guest-to-Customer conversion after the first storefront order and safe matching to an existing Customer (US-PTY-009, US-PTY-010).
- Customer credit under capability flag `b2bCredit`: credit limit, payment terms, visibility by flag, switch-off protection, credit check, overrun handling setting, overrun approval, credit change history (US-PTY-011 … US-PTY-017).

### Out of scope
- Price lists and their link to customer groups — Pricing (PRC) reads the customer group from this module.
- Receivable balances, ageing, collections and payments — Settlements (STL); this module only receives each Customer's open exposure through the credit exposure contribution (ADR-PTY-017).
- Sales, POS and storefront documents — Sales & POS (SLS) and Storefront (SFR) call this module's operations (A8.1).
- Credentials, sign-in and the CUSTOMER realm — given `erp-core:modules.sec`.
- Owner of the capability-flag switch (FeatureFlags API, presets, setup wizard) — OPEN in the project registry (OI-05, P-02); this module answers its switch-off dependency question (REQ-PTY-075).
- Business-policies SCOPE EXCEPTIONS: marketplace sellers; multi-currency party balances (credit amounts are in the tenant's base currency). The currency dimension exists in the model (ENT-PTY-002.currencyCode, ENT-PTY-005.currencyCode) and is hidden while the tenant has one currency (ADR-PTY-037).
- Suppliers in the global search (ADR-PTY-013).

### Module function
Party is the foundation module that answers "who": every counterparty the trader sells to or buys from is held once as a Party, and takes the Customer role, the Supplier role or both. Every channel — back-office, point of sale and storefront — reads the same Customer. Under the `b2bCredit` capability flag the module also holds each Customer's credit limit and payment terms, answers credit checks for sales, and runs the single-step approval of credit overruns.

### Detailed description
- **Owner** registers and maintains Customers and Suppliers, keeps addresses, places Customers in customer groups, sets credit limits and payment terms (flag on), chooses the credit-overrun action, approves or rejects Credit override requests, and reads credit change history.
- **Seller/Cashier** registers a Customer from within a sale with one name, finds Customers through the global search, sells to the walk-in Customer when no customer is named, sees a Customer's credit standing, and raises a Credit override request when a credit sale goes above the limit under the blocking action.
- **Warehouse** reads Suppliers (goods receipt context).
- **Guest** (storefront) checks out without signing in; the storefront passes the guest's details to this module, which attaches the order to the matching Customer or creates a new one, and has given `sec` invite the shopper to a CUSTOMER-realm account.
- **Consuming modules** (SLS, PUR, STL, SFR, PRC) read Customers, Suppliers and addresses by identifier; SLS and SFR request credit checks and use approved Credit override requests; STL and SLS report open exposure through the credit exposure contribution.

### Current situation
| Step | Party | Notes |
|---|---|---|
| 1 | Seller | Customers are kept per channel (paper or spreadsheet at the counter, a separate list in the online channel). |
| 2 | Owner | Suppliers are kept apart from customers; a trader who is both is typed twice. |
| 3 | Seller | Walk-in shoppers are written as "cash" with no common record. |
| 4 | Owner | Credit limits are remembered or noted by hand; overruns are found after the sale. |

### Current difficulties
- The same customer exists several times across channels, with split history and balance.
- Credit overruns are discovered late and approval is informal.
- No record of who changed a customer's credit terms.

### Proposed system and benefits
- One Party per counterparty, one Customer record for all channels (POL-PTY-001).
- Registration with one mandatory input, in place from a sale or a purchase (POL-PTY-006; KB §6.1, §6.4).
- Credit control that appears only when `b2bCredit` is on, is checked before the sale, and is approved by a permitted user with an audited trail (POL-PTY-003, POL-PTY-004, POL-PTY-005, POL-PTY-008).

### General notes
- Capability flags are read through the FeatureFlags API only; no requirement reads an activity name or preset (DP §5 G1; profile check CM-6).
- Every identifier is a BIGINT identity key assigned by the database (profile IDENTIFIERS).
- Lookup values are loaded at runtime from given `mdl` (profile LOOKUPS).
- Business numbers come from given `sequence` (profile NUMBERING).
- No workflow engine: the Credit override request carries a module-specific single-step approval traced to US-PTY-015 and US-PTY-016 (ADR-PTY-016).
- Deferred items: none.

#### Flag × activity coverage — `b2bCredit` (profile check CM-7; KB §4.2)
`b2bCredit` is the only capability flag this module reads. Each activity's preset sets the flag; requirements read the flag, never the activity.

| Activity (KB §4.2) | Preset value of `b2bCredit` | Flag-on requirements | Flag-off requirements |
|---|---|---|---|
| Electronics & appliances, mobiles, computers | ✓ on | REQ-PTY-064, REQ-PTY-065, REQ-PTY-073, REQ-PTY-079, REQ-PTY-085, REQ-PTY-091 | REQ-PTY-070, REQ-PTY-071, REQ-PTY-072, REQ-PTY-074 |
| Apparel & footwear | ✓ on | same as above | same as above |
| Household, cosmetics, stationery, toys, accessories | ✓ on | same as above | same as above |
| Grocery & food | ✓ on | same as above | same as above |
| Cleaning supplies, feed | ✓ on | same as above | same as above |
| Auto spare parts | ✓ on | same as above | same as above |
| Power tools & hardware | ✓ on | same as above | same as above |
| Furniture & lighting | off | REQ-PTY-073 (turned on later) | REQ-PTY-070, REQ-PTY-071, REQ-PTY-072, REQ-PTY-074 |
| Sanitary & plumbing | ✓ on | same as first row | same as first row |
| Fabrics & furnishings | ✓ on | same as first row | same as first row |
| Building materials & steel | ✓ on | same as first row | same as first row |
| Produce / meat by weight | off | REQ-PTY-073 (turned on later) | REQ-PTY-070, REQ-PTY-071, REQ-PTY-072, REQ-PTY-074 |
| Every activity — switching the flag off | — | REQ-PTY-075, REQ-PTY-076 | — |

## A3 — Entities and fields

**Standard fields — per profile** (profile.conventions.entity_defaults; audit fields are system-filled and never accepted from a caller):

| Kind | Standard fields |
|---|---|
| master | nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt |
| transactional | docNo, docDate, statusCode, createdBy, createdAt, updatedBy, updatedAt |
| every kind (platform columns) | tenantId, version — the given `AuditableEntity`'s tenant key and optimistic-lock version, system-filled, never accepted from a caller and never returned (ADR-PTY-035) |

Every identifier field below is a BIGINT identity key the database assigns (profile IDENTIFIERS). Logical types only; physical types belong to P2.

Entities provided by the given and used here without an ENT id (§3.1 GIVEN): User (CUSTOMER-realm and staff users, `erp-core:modules.sec`), AppConfiguration (`erp-core:modules.cu`), LookupType / LookupValue (`erp-core:modules.mdl`), NumberSeries (`erp-core:modules.sequence`), AuditEvent (`erp-core:modules.audit`), Tenant (`erp-core:modules.tenant`). See A9.

### ENT-PTY-001 — Party / الطرف

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — a long-lived counterparty identity referenced by many documents | SHARED (owner) — SLS, PUR, STL, TAX, SFR read names, contacts and tax registration number | yes — `code`: printed on invoices and statements given to the counterparty (test a); system-generated on first save from given `sequence`, read-only after (RULE-PTY-007; D-01) | create (through a role), read, update, deactivate (derived from roles) | read by consumers through the Customer / Supplier reads | module-registry decision 4 (as cited by the PRD); ADR-PTY-014 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| partyPk | identifier (BIGINT) | yes (system) | database identity | — | معرّف الطرف | Party ID |
| code | text (≤ 20) | yes (system) | given `sequence` | standard master field; RULE-PTY-007 | رقم الطرف | Party code |
| nameAr | text (≤ 200) | conditional | — | standard master field; at least one of nameAr / nameEn (RULE-PTY-001) | الاسم بالعربية | Name (Arabic) |
| nameEn | text (≤ 200) | conditional | — | standard master field; RULE-PTY-001 | الاسم بالإنجليزية | Name (English) |
| mobile | text (≤ 20) | no | — | RULE-PTY-004; not unique (D-03) | رقم الجوال | Mobile |
| email | text (≤ 254) | no | — | RULE-PTY-002, RULE-PTY-003; compared without letter case | البريد الإلكتروني | Email |
| taxRegistrationNo | text (≤ 30) | no | — | Advanced section (D-05) | الرقم الضريبي | Tax registration number |
| isActiveFl | flag | yes (system) | — | standard master field; false when no role is active (REQ-PTY-028, REQ-PTY-029) | نشط | Active |
| createdBy | reference (given sec User, BIGINT) | yes (system) | — | standard | أنشأه | Created by |
| createdAt | date-time | yes (system) | — | standard | تاريخ الإنشاء | Created at |
| updatedBy | reference (given sec User, BIGINT) | yes (system) | — | standard | عدّله | Updated by |
| updatedAt | date-time | yes (system) | — | standard | تاريخ التعديل | Updated at |

### ENT-PTY-002 — Customer / العميل

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — the buying role of a Party, referenced by sales, settlements and storefront documents | SHARED (owner) — SLS, STL, SFR (HARD-FK by consumers); PRC reads `customerGroupCode` | no own number — presents the Party `code` (one identity, ADR-PTY-014) | create, read, update, deactivate, reactivate, credit update (flag on) | given `sec` User linked by `secUserId` (A9) | registry CAND-PTY-001; module-registry decisions 4, 6, 9 (as cited by the PRD) |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| customerPk | identifier (BIGINT) | yes (system) | database identity | — | معرّف العميل | Customer ID |
| partyFk | reference → ENT-PTY-001 | yes | — | set on create; never changed | الطرف | Party |
| nameAr | presented from ENT-PTY-001.nameAr | — | — | standard master field, held once on the Party | الاسم بالعربية | Name (Arabic) |
| nameEn | presented from ENT-PTY-001.nameEn | — | — | standard master field, held once on the Party | الاسم بالإنجليزية | Name (English) |
| code | presented from ENT-PTY-001.code | — | — | standard master field, held once on the Party | رقم العميل | Customer code |
| customerGroupCode | lookup | yes (prefilled RETAIL) | CUSTOMER_GROUP (A6) | RULE-PTY-018; REQ-PTY-007 | مجموعة العملاء | Customer group |
| creditLimit | decimal (≥ 0, tenant base currency) | no | — | shown and accepted only while `b2bCredit` is on (RULE-PTY-023); RULE-PTY-022, RULE-PTY-011; empty = no credit allowance (REQ-PTY-068) | حد الائتمان | Credit limit |
| paymentTermDays | number (0 – 365) | no | — | shown and accepted only while `b2bCredit` is on (RULE-PTY-023); RULE-PTY-008, RULE-PTY-011 | مدة السداد (بالأيام) | Payment terms (days) |
| currencyCode | text (3, ISO 4217) | yes (system) | the tenant base currency when the Customer is created | the currency of creditLimit and of the open exposure; never accepted from a caller; hidden in the UI while the tenant has one currency (ADR-PTY-037) | العملة | Currency |
| secUserId | reference (given sec User, BIGINT) | no (system) | `erp-core:modules.sec` | set when the CUSTOMER-realm invitation is accepted (REQ-PTY-052); RULE-PTY-020 | حساب العميل في المتجر الإلكتروني | Storefront customer account |
| isWalkInFl | flag | yes (system) | — | true on the tenant's walk-in Customer only (RULE-PTY-010 … RULE-PTY-014) | عميل نقدي | Walk-in customer |
| isActiveFl | flag | yes | default true | standard master field; RULE-PTY-005, RULE-PTY-006 | نشط | Active |
| createdBy | reference (given sec User, BIGINT) | yes (system) | — | standard | أنشأه | Created by |
| createdAt | date-time | yes (system) | — | standard | تاريخ الإنشاء | Created at |
| updatedBy | reference (given sec User, BIGINT) | yes (system) | — | standard | عدّله | Updated by |
| updatedAt | date-time | yes (system) | — | standard | تاريخ التعديل | Updated at |

### ENT-PTY-003 — Supplier / المورّد

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — the selling-to-us role of a Party, referenced by purchasing and settlements documents | SHARED (owner) — PUR, STL (HARD-FK by consumers) | no own number — presents the Party `code` (ADR-PTY-014) | create, read, update, deactivate, reactivate | — | registry CAND-PTY-002; ADR-PTY-012 (supplier payment terms, flag-independent) |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| supplierPk | identifier (BIGINT) | yes (system) | database identity | — | معرّف المورّد | Supplier ID |
| partyFk | reference → ENT-PTY-001 | yes | — | set on create; never changed | الطرف | Party |
| nameAr | presented from ENT-PTY-001.nameAr | — | — | standard master field, held once on the Party | الاسم بالعربية | Name (Arabic) |
| nameEn | presented from ENT-PTY-001.nameEn | — | — | standard master field, held once on the Party | الاسم بالإنجليزية | Name (English) |
| code | presented from ENT-PTY-001.code | — | — | standard master field, held once on the Party | رقم المورّد | Supplier code |
| paymentTermDays | number (0 – 365) | no | — | independent of `b2bCredit` (ADR-PTY-012); RULE-PTY-008 | مدة السداد (بالأيام) | Payment terms (days) |
| isActiveFl | flag | yes | default true | standard master field; RULE-PTY-005, RULE-PTY-006 | نشط | Active |
| createdBy | reference (given sec User, BIGINT) | yes (system) | — | standard | أنشأه | Created by |
| createdAt | date-time | yes (system) | — | standard | تاريخ الإنشاء | Created at |
| updatedBy | reference (given sec User, BIGINT) | yes (system) | — | standard | عدّله | Updated by |
| updatedAt | date-time | yes (system) | — | standard | تاريخ التعديل | Updated at |

### ENT-PTY-004 — Address / العنوان

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — a reusable location of a Party, read by documents | SHARED (owner) — SLS, PUR, SFR (module-registry ENTITIES OWNED, as cited by the PRD) | no | create, read, update, deactivate, set default | read by consumers by Party (REQ-PTY-032) | registry CAND-PTY-003; US-PTY-005 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| addressPk | identifier (BIGINT) | yes (system) | database identity | — | معرّف العنوان | Address ID |
| partyFk | reference → ENT-PTY-001 | yes | — | set on create; never changed | الطرف | Party |
| addressTypeCode | lookup | yes | ADDRESS_TYPE (A6) | RULE-PTY-016 | نوع العنوان | Address type |
| nameAr | text (≤ 100) | no | — | standard master field used as the address label | اسم العنوان بالعربية | Address label (Arabic) |
| nameEn | text (≤ 100) | no | — | standard master field used as the address label | اسم العنوان بالإنجليزية | Address label (English) |
| code | text (≤ 10) | yes (system) | — | standard master field; system-assigned ordinal within the Party (1, 2, 3 …); not a business number | رمز العنوان | Address code |
| line1 | text (≤ 200) | yes | — | RULE-PTY-015 | العنوان (السطر الأول) | Address line 1 |
| line2 | text (≤ 200) | no | — | Advanced section | العنوان (السطر الثاني) | Address line 2 |
| city | text (≤ 100) | no | — | — | المدينة | City |
| region | text (≤ 100) | no | — | Advanced section | المنطقة / المحافظة | Region |
| postalCode | text (≤ 20) | no | — | Advanced section | الرمز البريدي | Postal code |
| isDefaultFl | flag | yes (system-maintained) | — | one default per Party per address type (RULE-PTY-017) | افتراضي | Default |
| isActiveFl | flag | yes | default true | standard master field; RULE-PTY-006 | نشط | Active |
| createdBy | reference (given sec User, BIGINT) | yes (system) | — | standard | أنشأه | Created by |
| createdAt | date-time | yes (system) | — | standard | تاريخ الإنشاء | Created at |
| updatedBy | reference (given sec User, BIGINT) | yes (system) | — | standard | عدّله | Updated by |
| updatedAt | date-time | yes (system) | — | standard; RULE-PTY-017 reads it | تاريخ التعديل | Updated at |

### ENT-PTY-005 — Credit override request / طلب تجاوز حد الائتمان

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional — one approval request per credit sale above the limit, with a status lifecycle (A7) | SHARED (owner) — SLS, SFR read and use an APPROVED request (A8.1) | yes — `docNo`: it is the numbered request itself and is quoted between seller and approver (test b, c); from given `sequence` (RULE-PTY-031) | create, read, search, approve, reject, cancel, use | the source sale is held as a text reference (`sourceDocumentRef`), not a key, because Party sits above SLS in the DAG (ADR-PTY-016) | US-PTY-015, US-PTY-016; ADR-PTY-016 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| creditOverrideRequestPk | identifier (BIGINT) | yes (system) | database identity | — | معرّف الطلب | Request ID |
| docNo | text (≤ 20) | yes (system) | given `sequence` | standard transactional field; RULE-PTY-031 | رقم الطلب | Request number |
| docDate | date-time | yes (system) | — | standard transactional field; time of creation | تاريخ الطلب | Request date |
| statusCode | lookup | yes (system) | CREDIT_OVERRIDE_STATUS (A6) | standard transactional field; lifecycle A7 | حالة الطلب | Request status |
| customerFk | reference → ENT-PTY-002 | yes | — | RULE-PTY-028, RULE-PTY-033 | العميل | Customer |
| saleAmount | decimal (> 0, tenant base currency) | yes | — | the amount the request covers; RULE-PTY-032, RULE-PTY-025, RULE-PTY-028 | مبلغ البيع | Sale amount |
| creditLimitAtRequest | decimal | no (system) | copied from ENT-PTY-002.creditLimit | empty when the Customer has no credit limit | حد الائتمان عند الطلب | Credit limit at request |
| openExposure | decimal | no (system) | from the credit exposure contribution (REQ-PTY-080) | empty when exposure was unavailable | المديونية القائمة | Open exposure |
| currencyCode | text (3, ISO 4217) | yes (system) | copied from ENT-PTY-002.currencyCode | the currency of saleAmount, creditLimitAtRequest and openExposure; hidden in the UI while the tenant has one currency (ADR-PTY-037) | العملة | Currency |
| creditCheckResultCode | lookup | yes (system) | CREDIT_CHECK_RESULT (A6) | the result that prompted the request; RULE-PTY-025 | نتيجة فحص الائتمان | Credit check result |
| sourceDocumentRef | text (≤ 60) | yes | supplied by the consuming module | reference of the sale the request is for | مرجع مستند البيع | Sale document reference |
| decidedBy | reference (given sec User, BIGINT) | no (system) | — | set on approval or rejection | صاحب القرار | Decided by |
| decidedAt | date-time | no (system) | — | set on approval or rejection | تاريخ القرار | Decided at |
| decisionReason | text (≤ 500) | conditional | — | required on rejection (RULE-PTY-027) | سبب القرار | Decision reason |
| usedDocumentRef | text (≤ 60) | no (system) | supplied by the consuming module | set when the request becomes USED | مستند البيع المستخدِم | Used on sale document |
| createdBy | reference (given sec User, BIGINT) | yes (system) | — | standard; the requester (RULE-PTY-029) | مقدّم الطلب | Requested by |
| createdAt | date-time | yes (system) | — | standard | تاريخ الإنشاء | Created at |
| updatedBy | reference (given sec User, BIGINT) | yes (system) | — | standard | عدّله | Updated by |
| updatedAt | date-time | yes (system) | — | standard | تاريخ التعديل | Updated at |

## A4 — Functional requirements (EARS) and acceptance criteria

### REQ-PTY-001 — One Customer record for every channel
  Pattern    : ubiquitous
  Statement  : The system shall hold each Customer as one Customer record whose identifier is the same for the back-office, the point of sale and the storefront.
  Traces     : US-PTY-001
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : One customer base across channels.
  Source     : POL-PTY-001; KB §1 "one customer base"
  Priority   : HIGH

#### AC-PTY-001 — [REQ-PTY-001]
  Given  : a Customer was registered from the point of sale and received identifier 5001 and Party code P-000120
  When   : the Owner searches Customers in the back-office by code P-000120
  Then   : exactly one result is returned, with identifier 5001 and code P-000120

#### AC-PTY-002 — [REQ-PTY-001]
  Given  : the storefront has attached a guest order to the Customer with identifier 5001
  When   : the Owner opens Customer 5001 in the back-office
  Then   : the Customer shown has identifier 5001 and no second Customer with the same email exists in the tenant

### REQ-PTY-002 — Customer read for consuming modules
  Pattern    : event
  Statement  : When a consuming module requests a Customer by its identifier, the system shall return the Customer's identifier, Party code, nameAr, nameEn, mobile, email, taxRegistrationNo, customerGroupCode and isActiveFl.
  Traces     : US-PTY-001
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : Every channel reads the same Customer data.
  Source     : POL-PTY-001; registry §5 (Customer consumers SLS, STL, SFR)
  Priority   : HIGH

#### AC-PTY-003 — [REQ-PTY-002]
  Given  : Customer 5001 has code P-000120, nameEn "Nour Trading", customerGroupCode WHOLESALE and isActiveFl true
  When   : Sales & POS requests Customer 5001
  Then   : the response carries identifier 5001, code P-000120, nameEn "Nour Trading", customerGroupCode WHOLESALE and isActiveFl true

### REQ-PTY-003 — Inactive role refused for new documents
  Pattern    : unwanted
  Statement  : If a consuming module requests a Customer or a Supplier for a new document and that Customer or Supplier is inactive, then the system shall refuse the request with the message of RULE-PTY-005.
  Traces     : US-PTY-001, US-PTY-003
  Entities   : ENT-PTY-002, ENT-PTY-003
  Rationale  : Deactivated counterparties stay out of new documents (ARCH-5 effect on consumers).
  Source     : ARCH-5; RULE-PTY-005
  Priority   : HIGH

#### AC-PTY-004 — [REQ-PTY-003]
  Given  : Customer 5002 (code P-000121) has isActiveFl false
  When   : Sales & POS requests Customer 5002 for a new sales invoice
  Then   : the request is refused with ar «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · en «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.»

#### AC-PTY-005 — [REQ-PTY-003]
  Given  : Supplier 7001 (code P-000200) has isActiveFl false
  When   : Purchasing requests Supplier 7001 for a new purchase order
  Then   : the request is refused with ar «P-000200 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · en «P-000200 is inactive and cannot be used on a new document. Reactivate it or choose another party.»

### REQ-PTY-004 — Customer registration
  Pattern    : event
  Statement  : When a user submits a Customer registration that carries at least one of nameAr or nameEn, the system shall create one Party and one active Customer linked to that Party in a single save.
  Traces     : US-PTY-002, US-PTY-001
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : A new customer joins the one customer base at once.
  Source     : POL-PTY-006, POL-PTY-001; ADR-PTY-014
  Priority   : HIGH

#### AC-PTY-006 — [REQ-PTY-004]
  Given  : a Seller/Cashier holding CREATE on page PTY_CUSTOMER
  When   : the Seller/Cashier submits a Customer registration with nameAr «محمد علي» and nothing else
  Then   : one Party with nameAr «محمد علي» and one Customer with isActiveFl true and partyFk equal to that Party's identifier are saved, and the save returns both identifiers

### REQ-PTY-005 — One mandatory input for Customer registration
  Pattern    : ubiquitous
  Statement  : The system shall require no input for a Customer registration other than one of nameAr or nameEn.
  Traces     : US-PTY-002
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : At most four mandatory inputs per screen; registration needs one.
  Source     : POL-PTY-006; KB §6.1; D-02
  Priority   : HIGH

#### AC-PTY-007 — [REQ-PTY-005]
  Given  : the Customer registration form opened from a sale
  When   : the Seller/Cashier enters nameEn "Walid Hassan" only and saves
  Then   : the Customer is saved; the number of inputs marked mandatory on the form is 1

### REQ-PTY-006 — Party code assignment
  Pattern    : event
  Statement  : When a Party is first saved, the system shall assign its code from the core number series.
  Traces     : US-PTY-002, US-PTY-003
  Entities   : ENT-PTY-001
  Rationale  : The party code is printed on documents given to the counterparty.
  Source     : profile NUMBERING; D-01; RULE-PTY-007
  Priority   : HIGH

#### AC-PTY-008 — [REQ-PTY-006]
  Given  : the core number series for Party returns P-000122 as its next value
  When   : a new Customer registration is saved
  Then   : the new Party's code is P-000122

### REQ-PTY-007 — Default customer group
  Pattern    : event
  Statement  : When a Customer is created without a customerGroupCode, the system shall set its customerGroupCode to RETAIL.
  Traces     : US-PTY-002, US-PTY-006
  Entities   : ENT-PTY-002
  Rationale  : Keeps registration to one mandatory input while every Customer has a customer group.
  Source     : D-02, D-04
  Priority   : HIGH

#### AC-PTY-009 — [REQ-PTY-007]
  Given  : a Customer registration carrying nameEn "Sara Adel" and no customerGroupCode
  When   : the registration is saved
  Then   : the saved Customer has customerGroupCode RETAIL

### REQ-PTY-008 — Registration in place from another module's document
  Pattern    : optional
  Statement  : Where a Customer or Supplier registration is opened from within another module's document, the system shall return the identifier and code of the created Customer or Supplier to that document.
  Traces     : US-PTY-002, US-PTY-003
  Entities   : ENT-PTY-001, ENT-PTY-002, ENT-PTY-003
  Rationale  : Action in place: the sale or purchase continues without leaving it.
  Source     : KB §6.4; POL-PTY-006
  Priority   : HIGH

#### AC-PTY-010 — [REQ-PTY-008]
  Given  : a Seller/Cashier is on an open sale and opens the Customer registration from it
  When   : the Seller/Cashier saves a Customer with nameEn "Omar Said"
  Then   : the sale receives the new Customer's identifier and code, and the sale screen stays open with that Customer selected

#### AC-PTY-011 — [REQ-PTY-008]
  Given  : an Owner is on an open purchase document and opens the Supplier registration from it
  When   : the Owner saves a Supplier with nameAr «مؤسسة النور»
  Then   : the purchase document receives the new Supplier's identifier and code

### REQ-PTY-009 — Party name required
  Pattern    : unwanted
  Statement  : If a Party create or update carries neither nameAr nor nameEn, then the system shall reject the save with the message of RULE-PTY-001.
  Traces     : US-PTY-002, US-PTY-003
  Entities   : ENT-PTY-001
  Rationale  : A counterparty without a name cannot be found or printed.
  Source     : RULE-PTY-001
  Priority   : HIGH

#### AC-PTY-012 — [REQ-PTY-009]
  Given  : a Customer registration with nameAr empty and nameEn empty
  When   : the user saves it
  Then   : nothing is saved and the message ar «أدخل اسم الطرف بالعربية أو بالإنجليزية.» · en «Enter the party name in Arabic or English.» is shown

### REQ-PTY-010 — Duplicate email refused
  Pattern    : unwanted
  Statement  : If a Party create or update carries an email already held by another active Party of the tenant, then the system shall reject the save with the message of RULE-PTY-002.
  Traces     : US-PTY-002, US-PTY-001
  Entities   : ENT-PTY-001
  Rationale  : The one customer base stays free of duplicates.
  Source     : POL-PTY-001; D-03; RULE-PTY-002
  Priority   : HIGH

#### AC-PTY-013 — [REQ-PTY-010]
  Given  : active Party P-000120 holds email "nour@example.com"
  When   : a user saves a new Customer with email "Nour@Example.com"
  Then   : nothing is saved and the message ar «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000120). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · en «This email is already registered to another active party (P-000120). Open that party instead of creating a new one.» is shown with an action that opens Party P-000120

### REQ-PTY-011 — Contact format check
  Pattern    : unwanted
  Statement  : If a Party create or update carries an email or a mobile that fails its format rule, then the system shall reject the save with the message of RULE-PTY-003 or RULE-PTY-004.
  Traces     : US-PTY-002, US-PTY-003
  Entities   : ENT-PTY-001
  Rationale  : Contacts are used for matching and invitations.
  Source     : RULE-PTY-003, RULE-PTY-004; ADR-PTY-015
  Priority   : HIGH

#### AC-PTY-014 — [REQ-PTY-011]
  Given  : a Customer registration with email "nour.example.com"
  When   : the user saves it
  Then   : nothing is saved and the message ar «صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com» · en «The email format is not valid, for example: name@example.com» is shown

#### AC-PTY-015 — [REQ-PTY-011]
  Given  : a Supplier registration with mobile "12345"
  When   : the user saves it
  Then   : nothing is saved and the message ar «رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +.» · en «The mobile number has 7 to 15 digits and can start with +.» is shown

### REQ-PTY-012 — Customer update
  Pattern    : event
  Statement  : When a user saves changes to a Customer, the system shall update the submitted fields of the Customer and of its Party.
  Traces     : US-PTY-001, US-PTY-002
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : Customer details are kept current in one place.
  Source     : POL-PTY-001; RULE-PTY-007
  Priority   : HIGH

#### AC-PTY-016 — [REQ-PTY-012]
  Given  : Customer 5001 with mobile "+201001112222"
  When   : a Seller/Cashier holding UPDATE on page PTY_CUSTOMER saves mobile "+201003334444"
  Then   : Customer 5001's Party holds mobile "+201003334444" and updatedBy is that Seller/Cashier

#### AC-PTY-017 — [REQ-PTY-012]
  Given  : Customer 5001 with code P-000120
  When   : an update request carries code P-999999
  Then   : the code stays P-000120 and the message ar «رقم الطرف يُنشأ تلقائياً ولا يمكن تعديله.» · en «The party code is assigned by the system and cannot be changed.» is returned

### REQ-PTY-013 — Customer search
  Pattern    : event
  Statement  : When a user searches Customers, the system shall return a page of Customers matching the submitted filters on code, name, mobile, email, taxRegistrationNo, customerGroupCode and isActiveFl.
  Traces     : US-PTY-001, US-PTY-006
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : The back-office list of the one customer base.
  Source     : POL-PTY-001
  Priority   : HIGH

#### AC-PTY-018 — [REQ-PTY-013]
  Given  : 3 active Customers with customerGroupCode WHOLESALE and 5 with RETAIL
  When   : the Owner searches Customers with customerGroupCode WHOLESALE and isActiveFl true, page size 20
  Then   : a page with 3 Customers is returned, each with customerGroupCode WHOLESALE, and the total count is 3

### REQ-PTY-014 — Customer deactivation
  Pattern    : event
  Statement  : When an Owner deactivates a Customer, the system shall set the Customer's isActiveFl to false and keep every existing reference to it readable.
  Traces     : US-PTY-001
  Entities   : ENT-PTY-002
  Rationale  : Deactivation never breaks earlier documents (ARCH-5).
  Source     : ARCH-5; RULE-PTY-006, RULE-PTY-010
  Priority   : HIGH

#### AC-PTY-019 — [REQ-PTY-014]
  Given  : active Customer 5003 (code P-000123) referenced by 2 earlier sales invoices
  When   : the Owner deactivates Customer 5003
  Then   : Customer 5003 has isActiveFl false, a read of Customer 5003 still returns it, and the message ar «تم إيقاف P-000123. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة.» · en «P-000123 is deactivated. Earlier documents keep it; it is no longer offered on new documents.» is shown

### REQ-PTY-015 — Customer reactivation
  Pattern    : event
  Statement  : When an Owner reactivates an inactive Customer, the system shall set the Customer's isActiveFl to true.
  Traces     : US-PTY-001
  Entities   : ENT-PTY-002
  Rationale  : A returning customer keeps the same record and history.
  Source     : POL-PTY-001; RULE-PTY-002
  Priority   : HIGH

#### AC-PTY-020 — [REQ-PTY-015]
  Given  : Customer 5003 with isActiveFl false and no other active Party holding its email
  When   : the Owner reactivates Customer 5003
  Then   : Customer 5003 has isActiveFl true and is returned by the Customer search filtered on isActiveFl true

### REQ-PTY-016 — Supplier registration
  Pattern    : event
  Statement  : When an Owner submits a Supplier registration that carries at least one of nameAr or nameEn, the system shall create one Party and one active Supplier linked to that Party in a single save.
  Traces     : US-PTY-003
  Entities   : ENT-PTY-001, ENT-PTY-003
  Rationale  : Suppliers are registered with one mandatory input, also while recording a purchase.
  Source     : POL-PTY-006; ADR-PTY-014
  Priority   : HIGH

#### AC-PTY-021 — [REQ-PTY-016]
  Given  : an Owner holding CREATE on page PTY_SUPPLIER
  When   : the Owner submits a Supplier registration with nameEn "Delta Imports" only
  Then   : one Party with nameEn "Delta Imports" and one Supplier with isActiveFl true linked to it are saved; the number of inputs marked mandatory on the form is 1

### REQ-PTY-017 — Supplier update
  Pattern    : event
  Statement  : When an Owner saves changes to a Supplier, the system shall update the submitted fields of the Supplier and of its Party.
  Traces     : US-PTY-003
  Entities   : ENT-PTY-001, ENT-PTY-003
  Rationale  : Purchasing and payments address the right counterparty.
  Source     : POL-PTY-006; ADR-PTY-012; RULE-PTY-007, RULE-PTY-008
  Priority   : HIGH

#### AC-PTY-022 — [REQ-PTY-017]
  Given  : Supplier 7002 with paymentTermDays empty and taxRegistrationNo empty
  When   : the Owner saves paymentTermDays 30 and taxRegistrationNo "300123456700003"
  Then   : Supplier 7002 holds paymentTermDays 30 and its Party holds taxRegistrationNo "300123456700003"

### REQ-PTY-018 — Supplier search
  Pattern    : event
  Statement  : When a user searches Suppliers, the system shall return a page of Suppliers matching the submitted filters on code, name, mobile, email, taxRegistrationNo and isActiveFl.
  Traces     : US-PTY-003, US-PTY-001
  Entities   : ENT-PTY-001, ENT-PTY-003
  Rationale  : Suppliers are found by what the user knows; the email filter reaches the active Party named by a RULE-PTY-002 refusal when it holds only the Supplier role (ADR-PTY-043).
  Source     : US-PTY-003; RULE-PTY-002; ADR-PTY-043
  Priority   : HIGH

#### AC-PTY-023 — [REQ-PTY-018]
  Given  : active Suppliers "Delta Imports" (P-000201) and "Delta Steel" (P-000202), and inactive Supplier "Delta Old" (P-000203)
  When   : a Warehouse user searches Suppliers with name "Delta" and isActiveFl true
  Then   : a page with exactly P-000201 and P-000202 is returned

#### AC-PTY-136 — [REQ-PTY-018]
  Given  : active Party P-000204 holds email "delta@example.com" and only the Supplier role (Supplier 7004); no other Party holds that email
  When   : an Owner searches Suppliers with email "Delta@Example.com" and isActiveFl true
  Then   : a page with exactly 1 Supplier is returned: identifier 7004, code P-000204

### REQ-PTY-019 — Supplier read for consuming modules
  Pattern    : event
  Statement  : When a consuming module requests a Supplier by its identifier, the system shall return the Supplier's identifier, Party code, nameAr, nameEn, mobile, email, taxRegistrationNo, paymentTermDays and isActiveFl.
  Traces     : US-PTY-003
  Entities   : ENT-PTY-001, ENT-PTY-003
  Rationale  : Purchasing and Settlements read one Supplier record.
  Source     : registry §5 (Supplier consumers PUR, STL)
  Priority   : HIGH

#### AC-PTY-024 — [REQ-PTY-019]
  Given  : Supplier 7002 with code P-000202 and paymentTermDays 30
  When   : Settlements requests Supplier 7002
  Then   : the response carries identifier 7002, code P-000202, paymentTermDays 30 and isActiveFl true

### REQ-PTY-020 — Supplier deactivation
  Pattern    : event
  Statement  : When an Owner deactivates a Supplier, the system shall set the Supplier's isActiveFl to false and keep every existing reference to it readable.
  Traces     : US-PTY-003
  Entities   : ENT-PTY-003
  Rationale  : Deactivation never breaks earlier purchase documents (ARCH-5).
  Source     : ARCH-5; RULE-PTY-006
  Priority   : HIGH

#### AC-PTY-025 — [REQ-PTY-020]
  Given  : active Supplier 7003 (code P-000203) referenced by 1 earlier purchase order
  When   : the Owner deactivates Supplier 7003
  Then   : Supplier 7003 has isActiveFl false and a read of Supplier 7003 still returns it

### REQ-PTY-021 — Supplier reactivation
  Pattern    : event
  Statement  : When an Owner reactivates an inactive Supplier, the system shall set the Supplier's isActiveFl to true.
  Traces     : US-PTY-003
  Entities   : ENT-PTY-003
  Rationale  : A returning supplier keeps the same record.
  Source     : US-PTY-003; RULE-PTY-002
  Priority   : HIGH

#### AC-PTY-026 — [REQ-PTY-021]
  Given  : Supplier 7003 with isActiveFl false
  When   : the Owner reactivates Supplier 7003
  Then   : Supplier 7003 has isActiveFl true

### REQ-PTY-022 — Supplier payment terms range
  Pattern    : unwanted
  Statement  : If a Supplier save carries a paymentTermDays below 0 or above 365, then the system shall reject the save with the message of RULE-PTY-008.
  Traces     : US-PTY-003
  Entities   : ENT-PTY-003
  Rationale  : Payment terms drive due dates in Settlements.
  Source     : ADR-PTY-014; RULE-PTY-008
  Priority   : HIGH

#### AC-PTY-027 — [REQ-PTY-022]
  Given  : Supplier 7002
  When   : the Owner saves paymentTermDays 400
  Then   : nothing is saved and the message ar «مدة السداد يجب أن تكون بين 0 و365 يوماً.» · en «Payment terms must be between 0 and 365 days.» is shown

### REQ-PTY-023 — Party role deactivation published
  Pattern    : event
  Statement  : When a Customer or a Supplier is deactivated, the system shall publish a party-role-deactivated domain event carrying the role, its identifier and the Party code.
  Traces     : US-PTY-001, US-PTY-003
  Entities   : ENT-PTY-002, ENT-PTY-003
  Rationale  : Consuming modules are notified of the deactivation (ARCH-5 notify).
  Source     : ARCH-5; RULE-PTY-006; given `events`
  Priority   : HIGH

#### AC-PTY-028 — [REQ-PTY-023]
  Given  : active Customer 5003 (code P-000123)
  When   : the Owner deactivates Customer 5003
  Then   : one party-role-deactivated event with role CUSTOMER, identifier 5003 and code P-000123 is published

### REQ-PTY-024 — One Party identity per counterparty
  Pattern    : ubiquitous
  Statement  : The system shall hold the names, code, mobile, email, taxRegistrationNo and addresses of a counterparty once, on its Party, shared by its Customer and its Supplier.
  Traces     : US-PTY-004
  Entities   : ENT-PTY-001, ENT-PTY-002, ENT-PTY-003
  Rationale  : A trader bought from and sold to is held once.
  Source     : module-registry decision 4 (as cited by the PRD); ADR-PTY-014
  Priority   : HIGH

#### AC-PTY-029 — [REQ-PTY-024]
  Given  : Party P-000130 holds both a Customer and a Supplier
  When   : the Owner changes the Party's mobile from the Supplier screen to "+966500001111"
  Then   : a read of the Customer of Party P-000130 returns mobile "+966500001111"

### REQ-PTY-025 — Add the Supplier role to a Customer
  Pattern    : event
  Statement  : When an Owner adds the Supplier role to a Customer, the system shall create a Supplier linked to the Customer's Party without asking for any Party field.
  Traces     : US-PTY-004
  Entities   : ENT-PTY-001, ENT-PTY-002, ENT-PTY-003
  Rationale  : Adding the other role is an action in place.
  Source     : module-registry decision 4 (as cited by the PRD); KB §6.4
  Priority   : HIGH

#### AC-PTY-030 — [REQ-PTY-025]
  Given  : Customer 5004 of Party P-000124 and no Supplier for that Party
  When   : the Owner chooses "Make supplier" / «إضافة كمورّد» on Customer 5004
  Then   : one Supplier with partyFk equal to Party P-000124's identifier and isActiveFl true is saved, and the Party count of the tenant is unchanged

### REQ-PTY-026 — Add the Customer role to a Supplier
  Pattern    : event
  Statement  : When an Owner adds the Customer role to a Supplier, the system shall create a Customer linked to the Supplier's Party with customerGroupCode RETAIL without asking for any Party field.
  Traces     : US-PTY-004
  Entities   : ENT-PTY-001, ENT-PTY-002, ENT-PTY-003
  Rationale  : Adding the other role is an action in place.
  Source     : module-registry decision 4 (as cited by the PRD); KB §6.4; D-04
  Priority   : HIGH

#### AC-PTY-031 — [REQ-PTY-026]
  Given  : Supplier 7004 of Party P-000204 and no Customer for that Party
  When   : the Owner chooses "Make customer" / «إضافة كعميل» on Supplier 7004
  Then   : one Customer with partyFk equal to Party P-000204's identifier, customerGroupCode RETAIL and isActiveFl true is saved

### REQ-PTY-027 — Role already held
  Pattern    : unwanted
  Statement  : If a Customer or a Supplier is added to a Party that already holds that role, then the system shall reject the addition with the message of RULE-PTY-009.
  Traces     : US-PTY-004
  Entities   : ENT-PTY-002, ENT-PTY-003
  Rationale  : One Customer and one Supplier at most per Party.
  Source     : RULE-PTY-009
  Priority   : HIGH

#### AC-PTY-032 — [REQ-PTY-027]
  Given  : Party P-000130 already holds a Supplier
  When   : a request adds a Supplier to Party P-000130
  Then   : nothing is saved and the message ar «هذا الطرف له صفة مورّد بالفعل.» · en «This party already holds the Supplier role.» is returned

### REQ-PTY-028 — Party inactive when no role is active
  Pattern    : event
  Statement  : When the last active role of a Party is deactivated, the system shall set the Party's isActiveFl to false.
  Traces     : US-PTY-004
  Entities   : ENT-PTY-001
  Rationale  : The Party's active state follows its roles.
  Source     : ADR-PTY-014
  Priority   : HIGH

#### AC-PTY-033 — [REQ-PTY-028]
  Given  : Party P-000124 holds an active Customer and an inactive Supplier
  When   : the Owner deactivates the Customer of Party P-000124
  Then   : Party P-000124 has isActiveFl false

### REQ-PTY-029 — Party active again with a role
  Pattern    : event
  Statement  : When a role of an inactive Party is reactivated or added, the system shall set the Party's isActiveFl to true.
  Traces     : US-PTY-004
  Entities   : ENT-PTY-001
  Rationale  : The Party's active state follows its roles.
  Source     : ADR-PTY-014; RULE-PTY-002
  Priority   : HIGH

#### AC-PTY-034 — [REQ-PTY-029]
  Given  : Party P-000124 with isActiveFl false and an inactive Customer
  When   : the Owner reactivates the Customer of Party P-000124
  Then   : Party P-000124 has isActiveFl true

#### AC-PTY-035 — [REQ-PTY-029]
  Given  : inactive Party P-000124 holds email "a@example.com" and active Party P-000140 holds email "a@example.com"
  When   : the Owner reactivates the Customer of Party P-000124
  Then   : nothing is saved and the message ar «هذا البريد الإلكتروني مسجّل لطرف نشط آخر (P-000140). افتح ذلك الطرف بدلاً من إنشاء طرف جديد.» · en «This email is already registered to another active party (P-000140). Open that party instead of creating a new one.» is shown

### REQ-PTY-030 — Address creation
  Pattern    : event
  Statement  : When a user saves a new Address on a Party with an addressTypeCode and a line1, the system shall create the Address linked to that Party.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-001, ENT-PTY-004
  Rationale  : Addresses are kept once, on the Party.
  Source     : POL-PTY-001; registry CAND-PTY-003
  Priority   : HIGH

#### AC-PTY-036 — [REQ-PTY-030]
  Given  : Party P-000120 with no Address
  When   : the Owner saves an Address with addressTypeCode DELIVERY, line1 "12 Tahrir St" and city "Cairo"
  Then   : one Address with partyFk equal to Party P-000120's identifier, addressTypeCode DELIVERY, code "1" and isActiveFl true is saved

### REQ-PTY-031 — Address update
  Pattern    : event
  Statement  : When a user saves changes to an Address, the system shall update the submitted fields of that Address.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-004
  Rationale  : Addresses stay current for every consumer.
  Source     : US-PTY-005
  Priority   : HIGH

#### AC-PTY-037 — [REQ-PTY-031]
  Given  : Address code "1" of Party P-000120 with city "Cairo"
  When   : the Owner saves city "Giza"
  Then   : the Address holds city "Giza" and its partyFk is unchanged

### REQ-PTY-032 — Party addresses read
  Pattern    : event
  Statement  : When a user or a consuming module requests the addresses of a Party, the system shall return the Party's active Addresses with their addressTypeCode and isDefaultFl.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-004
  Rationale  : Sales, purchasing and the storefront customer area use the same address.
  Source     : module-registry ENTITIES OWNED (Address SHARED with SLS, PUR, SFR), as cited by the PRD
  Priority   : HIGH

#### AC-PTY-038 — [REQ-PTY-032]
  Given  : Party P-000120 with 2 active Addresses (BILLING default, DELIVERY default) and 1 inactive DELIVERY Address
  When   : the storefront customer area requests the addresses of Party P-000120
  Then   : exactly the 2 active Addresses are returned, each with isDefaultFl true

### REQ-PTY-033 — Address deactivation
  Pattern    : event
  Statement  : When a user deactivates an Address, the system shall set the Address's isActiveFl to false and keep every existing reference to it readable.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-004
  Rationale  : Earlier documents keep their address (ARCH-5).
  Source     : ARCH-5; RULE-PTY-006
  Priority   : HIGH

#### AC-PTY-039 — [REQ-PTY-033]
  Given  : active Address 9001 referenced by 1 earlier sales order
  When   : the Owner deactivates Address 9001
  Then   : Address 9001 has isActiveFl false, is absent from the Party's addresses read, and a read of Address 9001 by its identifier still returns it

### REQ-PTY-034 — First address of a type becomes default
  Pattern    : event
  Statement  : When the first active Address of an address type is saved on a Party, the system shall set that Address's isDefaultFl to true.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-004
  Rationale  : Documents always find a default address without a choice.
  Source     : D-09; KB §6.8; RULE-PTY-017
  Priority   : HIGH

#### AC-PTY-040 — [REQ-PTY-034]
  Given  : Party P-000120 with no active BILLING Address
  When   : the Owner saves a BILLING Address on Party P-000120
  Then   : the new Address has isDefaultFl true

### REQ-PTY-035 — Default address switch
  Pattern    : event
  Statement  : When a user marks an Address as default for its address type, the system shall set isDefaultFl to false on the Party's previous default Address of the same type.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-004
  Rationale  : One default per Party per address type.
  Source     : D-09; RULE-PTY-017
  Priority   : HIGH

#### AC-PTY-041 — [REQ-PTY-035]
  Given  : Party P-000120 with DELIVERY Address code "1" as default and DELIVERY Address code "2" not default
  When   : the Owner marks Address code "2" as default
  Then   : Address "2" has isDefaultFl true, Address "1" has isDefaultFl false, and the message ar «أصبح هذا العنوان هو العنوان الافتراضي من نوع توصيل.» · en «This address is now the default Delivery address.» is shown

### REQ-PTY-036 — Default reassigned when the default is deactivated
  Pattern    : unwanted
  Statement  : If the default Address of an address type is deactivated while the Party has another active Address of that type, then the system shall set isDefaultFl to true on the most recently updated remaining active Address of that type.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-004
  Rationale  : A Party with addresses of a type keeps a default of that type.
  Source     : D-09; RULE-PTY-017
  Priority   : HIGH

#### AC-PTY-042 — [REQ-PTY-036]
  Given  : Party P-000120 with DELIVERY Address "1" (default), DELIVERY Address "2" updated 2026-10-01 and DELIVERY Address "3" updated 2026-10-20
  When   : the Owner deactivates Address "1"
  Then   : Address "3" has isDefaultFl true and Address "2" has isDefaultFl false

### REQ-PTY-037 — Address line required
  Pattern    : unwanted
  Statement  : If an Address save carries no line1, then the system shall reject the save with the message of RULE-PTY-015.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-004
  Rationale  : An address without a street line cannot be used for delivery or billing.
  Source     : RULE-PTY-015
  Priority   : HIGH

#### AC-PTY-043 — [REQ-PTY-037]
  Given  : a new Address with addressTypeCode BILLING and line1 empty
  When   : the user saves it
  Then   : nothing is saved and the message ar «أدخل السطر الأول من العنوان.» · en «Enter the first address line.» is shown

### REQ-PTY-038 — Address type must be valid
  Pattern    : unwanted
  Statement  : If an Address save carries an addressTypeCode that is not an active value of lookup ADDRESS_TYPE, then the system shall reject the save with the message of RULE-PTY-016.
  Traces     : US-PTY-005
  Entities   : ENT-PTY-004
  Rationale  : Address types come from the lookup only.
  Source     : profile LOOKUPS; RULE-PTY-016
  Priority   : HIGH

#### AC-PTY-044 — [REQ-PTY-038]
  Given  : lookup ADDRESS_TYPE has active values BILLING and DELIVERY only
  When   : an Address save carries addressTypeCode "OFFICE"
  Then   : nothing is saved and the message ar «اختر نوع عنوان من القائمة.» · en «Choose an address type from the list.» is returned

### REQ-PTY-039 — One customer group per Customer
  Pattern    : ubiquitous
  Statement  : The system shall hold exactly one customerGroupCode on every Customer, taken from the active values of lookup CUSTOMER_GROUP.
  Traces     : US-PTY-006
  Entities   : ENT-PTY-002
  Rationale  : Price lists and reports follow the customer group across every channel.
  Source     : POL-PTY-001; DP §7.1 "Price list … for a channel or customer group"; module-registry LOOKUPS OWNED (customer-group), as cited by the PRD
  Priority   : —

#### AC-PTY-045 — [REQ-PTY-039]
  Given  : Customer 5001 with customerGroupCode RETAIL
  When   : the Owner saves customerGroupCode WHOLESALE on Customer 5001
  Then   : Customer 5001 holds customerGroupCode WHOLESALE only, and a read of Customer 5001 by Pricing returns WHOLESALE

### REQ-PTY-040 — Customer group must be valid
  Pattern    : unwanted
  Statement  : If a Customer save carries a customerGroupCode that is not an active value of lookup CUSTOMER_GROUP, then the system shall reject the save with the message of RULE-PTY-018.
  Traces     : US-PTY-006
  Entities   : ENT-PTY-002
  Rationale  : Customer groups come from the lookup only.
  Source     : profile LOOKUPS; RULE-PTY-018
  Priority   : —

#### AC-PTY-046 — [REQ-PTY-040]
  Given  : lookup CUSTOMER_GROUP has active values RETAIL and WHOLESALE, and value VIP is inactive
  When   : a Customer save carries customerGroupCode VIP
  Then   : nothing is saved and the message ar «اختر مجموعة عملاء من القائمة.» · en «Choose a customer group from the list.» is returned

### REQ-PTY-041 — Customers in the global search
  Pattern    : event
  Statement  : When a user holding VIEW on page PTY_CUSTOMER enters a global search term of at least 2 characters, the system shall return at most 10 active Customers whose code, nameAr, nameEn, mobile, email or taxRegistrationNo contains the term without regard to letter case, with exact code matches first and the rest ordered by name ascending.
  Traces     : US-PTY-007
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : The customer is reached from one search without knowing which screen to open.
  Source     : POL-PTY-007; KB §6.6; D-07; ADR-PTY-013 (customers only)
  Priority   : HIGH

#### AC-PTY-047 — [REQ-PTY-041]
  Given  : active Customers "Nour Trading" (P-000120, mobile "+201001112222") and "Nour Home" (P-000150), and Supplier "Nour Supplies"
  When   : a Seller/Cashier enters "nour" in the global search
  Then   : the customer results are exactly "Nour Home" then "Nour Trading"; "Nour Supplies" is not among the customer results

#### AC-PTY-048 — [REQ-PTY-041]
  Given  : active Customer with mobile "+201001112222"
  When   : a Seller/Cashier enters "1112222" in the global search
  Then   : that Customer is returned in the customer results

#### AC-PTY-049 — [REQ-PTY-041]
  Given  : 25 active Customers
  When   : a Seller/Cashier enters the single character "n" in the global search
  Then   : 0 customer results are returned

### REQ-PTY-042 — Arabic letter forms in search
  Pattern    : ubiquitous
  Statement  : The system shall compare Arabic search terms with Arabic names treating أ, إ and آ as ا, ة as ه, and ى as ي.
  Traces     : US-PTY-007
  Entities   : ENT-PTY-001
  Rationale  : Users type the same Arabic name with different letter forms.
  Source     : D-07; KB §6.6
  Priority   : HIGH

#### AC-PTY-050 — [REQ-PTY-042]
  Given  : active Customer with nameAr «أحمد مصطفى»
  When   : a Seller/Cashier enters «احمد» in the global search
  Then   : the Customer «أحمد مصطفى» is returned in the customer results

### REQ-PTY-043 — Global search without Customer access
  Pattern    : unwanted
  Statement  : If a user without VIEW on page PTY_CUSTOMER enters a global search term, then the system shall return no Customer in the global search results.
  Traces     : US-PTY-007
  Entities   : ENT-PTY-002
  Rationale  : Search never reveals customers to a user without access.
  Source     : profile security_model; §7.1
  Priority   : HIGH

#### AC-PTY-051 — [REQ-PTY-043]
  Given  : a Warehouse user without VIEW on page PTY_CUSTOMER and an active Customer "Nour Trading"
  When   : the Warehouse user enters "Nour" in the global search
  Then   : 0 customer results are returned

### REQ-PTY-044 — Walk-in Customer at tenant provisioning
  Pattern    : event
  Statement  : When a tenant is provisioned, the system shall create the tenant's walk-in Customer with nameAr «عميل نقدي», nameEn "Walk-in customer", customerGroupCode RETAIL and isWalkInFl true.
  Traces     : US-PTY-008
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : The walk-in customer is ready from the first day.
  Source     : POL-PTY-009; module-registry decision 9 (as cited by the PRD); D-08; RULE-PTY-014
  Priority   : HIGH

#### AC-PTY-052 — [REQ-PTY-044]
  Given  : a new tenant whose provisioning has not run
  When   : the tenant is provisioned
  Then   : exactly one Customer with isWalkInFl true, nameAr «عميل نقدي», nameEn "Walk-in customer", customerGroupCode RETAIL and isActiveFl true exists in that tenant

### REQ-PTY-045 — Walk-in Customer for an unnamed point-of-sale sale
  Pattern    : event
  Statement  : When a consuming module requests the default Customer for a point-of-sale sale that names no Customer, the system shall return the tenant's walk-in Customer.
  Traces     : US-PTY-008
  Entities   : ENT-PTY-002
  Rationale  : Every invoice has a party for ageing and reports.
  Source     : POL-PTY-009; KB §6.8, §6.10
  Priority   : HIGH

#### AC-PTY-053 — [REQ-PTY-045]
  Given  : the tenant's walk-in Customer has identifier 5000
  When   : Sales & POS requests the default Customer for a sale with no Customer named
  Then   : Customer 5000 with isWalkInFl true is returned

### REQ-PTY-046 — Walk-in Customer created on demand
  Pattern    : unwanted
  Statement  : If the default Customer for a point-of-sale sale is requested and the tenant has no walk-in Customer, then the system shall create the walk-in Customer of REQ-PTY-044 before returning it.
  Traces     : US-PTY-008
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : Tenants provisioned before this module was enabled still sell at once.
  Source     : POL-PTY-009; RULE-PTY-014
  Priority   : HIGH

#### AC-PTY-054 — [REQ-PTY-046]
  Given  : a tenant with no Customer having isWalkInFl true
  When   : Sales & POS requests the default Customer for a sale with no Customer named
  Then   : one walk-in Customer is created and returned, and a second identical request returns the same identifier

### REQ-PTY-047 — Walk-in Customer cannot be deactivated
  Pattern    : unwanted
  Statement  : If a user deactivates the walk-in Customer, then the system shall reject the deactivation with the message of RULE-PTY-010.
  Traces     : US-PTY-008
  Entities   : ENT-PTY-002
  Rationale  : The point of sale always has a walk-in Customer.
  Source     : POL-PTY-009; RULE-PTY-010
  Priority   : HIGH

#### AC-PTY-055 — [REQ-PTY-047]
  Given  : the walk-in Customer 5000
  When   : the Owner deactivates Customer 5000
  Then   : Customer 5000 keeps isActiveFl true and the message ar «العميل النقدي لازم لنقطة البيع ولا يمكن إيقافه.» · en «The walk-in customer is required by the point of sale and cannot be deactivated.» is shown

### REQ-PTY-048 — Walk-in Customer carries no credit
  Pattern    : unwanted
  Statement  : If a save sets a creditLimit or a paymentTermDays on the walk-in Customer, then the system shall reject the save with the message of RULE-PTY-011.
  Traces     : US-PTY-008, US-PTY-011
  Entities   : ENT-PTY-002
  Rationale  : An unnamed shopper cannot be granted credit.
  Source     : POL-PTY-009, POL-PTY-003; RULE-PTY-011
  Priority   : HIGH

#### AC-PTY-056 — [REQ-PTY-048]
  Given  : `b2bCredit` is on and the walk-in Customer 5000
  When   : the Owner saves creditLimit 1000 on Customer 5000
  Then   : nothing is saved and the message ar «لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان.» · en «The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit.» is shown

### REQ-PTY-049 — Walk-in Customer has no Supplier role
  Pattern    : unwanted
  Statement  : If a Supplier is added to the Party of the walk-in Customer, then the system shall reject the addition with the message of RULE-PTY-012.
  Traces     : US-PTY-008, US-PTY-004
  Entities   : ENT-PTY-002, ENT-PTY-003
  Rationale  : The walk-in Party stands for unnamed shoppers only.
  Source     : RULE-PTY-012
  Priority   : HIGH

#### AC-PTY-057 — [REQ-PTY-049]
  Given  : the walk-in Customer 5000
  When   : the Owner chooses "Make supplier" / «إضافة كمورّد» on Customer 5000
  Then   : no Supplier is saved and the message ar «لا يمكن إضافة صفة المورّد إلى العميل النقدي.» · en «The walk-in customer cannot also be a supplier.» is shown

### REQ-PTY-050 — New Customer from a guest's first order
  Pattern    : event
  Statement  : When the storefront submits a guest's order details and no active Party matches them under REQ-PTY-055 or REQ-PTY-056, the system shall create a Party, a Customer with customerGroupCode RETAIL and a DELIVERY Address from those details, and return the Customer's identifier.
  Traces     : US-PTY-009
  Entities   : ENT-PTY-001, ENT-PTY-002, ENT-PTY-004
  Rationale  : The guest becomes a recognised Customer after the first order.
  Source     : POL-PTY-002; KB §3 D5; DP §5 G9; ADR-PTY-015
  Priority   : MEDIUM

#### AC-PTY-058 — [REQ-PTY-050]
  Given  : no active Party has email "lina@example.com" or mobile "+966551234567"
  When   : the storefront submits guest details nameEn "Lina Fares", email "lina@example.com", mobile "+966551234567", line1 "King Fahd Rd 10", city "Riyadh"
  Then   : one Party, one Customer with customerGroupCode RETAIL and one DELIVERY Address with isDefaultFl true are saved, and the new Customer's identifier is returned

### REQ-PTY-051 — Invitation for a Customer created from a guest order
  Pattern    : event
  Statement  : When a Customer is created from a guest order, the system shall request from the core security module a CUSTOMER-realm invitation addressed to the Customer's email, or to its mobile when the email is empty.
  Traces     : US-PTY-009
  Entities   : ENT-PTY-002
  Rationale  : The guest becomes a CUSTOMER-realm account by confirming a contact they own.
  Source     : POL-PTY-002; KB §3 D5; ADR-PTY-015; given `sec`
  Priority   : MEDIUM

#### AC-PTY-059 — [REQ-PTY-051]
  Given  : REQ-PTY-050 has created Customer 5010 with email "lina@example.com"
  When   : the creation completes
  Then   : one CUSTOMER-realm invitation addressed to "lina@example.com" for Customer 5010 is requested from the core security module, and Customer 5010's secUserId is empty

### REQ-PTY-052 — Link the CUSTOMER-realm user on acceptance
  Pattern    : event
  Statement  : When the core security module reports that a CUSTOMER-realm invitation issued for a Customer has been accepted, the system shall set that Customer's secUserId to the accepting user's identifier.
  Traces     : US-PTY-009, US-PTY-010
  Entities   : ENT-PTY-002
  Rationale  : The account is linked only after the shopper confirms a contact they own.
  Source     : module-registry decision 5 (as cited by the PRD); ADR-PTY-015; RULE-PTY-013, RULE-PTY-020
  Priority   : MEDIUM

#### AC-PTY-060 — [REQ-PTY-052]
  Given  : an invitation for Customer 5010 is pending
  When   : the core security module reports its acceptance by user 88001
  Then   : Customer 5010 holds secUserId 88001

### REQ-PTY-053 — Guest contact required
  Pattern    : unwanted
  Statement  : If guest order details carry neither an email nor a mobile, then the system shall reject them with the message of RULE-PTY-019.
  Traces     : US-PTY-009
  Entities   : ENT-PTY-001
  Rationale  : Matching and the invitation need a contact.
  Source     : ADR-PTY-015; RULE-PTY-019
  Priority   : MEDIUM

#### AC-PTY-061 — [REQ-PTY-053]
  Given  : guest details with nameEn "Lina Fares", email empty and mobile empty
  When   : the storefront submits them
  Then   : no Party is saved and the message ar «أدخل البريد الإلكتروني أو رقم الجوال لإتمام الطلب.» · en «Enter an email or a mobile number to complete the order.» is returned

### REQ-PTY-054 — One Customer per CUSTOMER-realm user
  Pattern    : unwanted
  Statement  : If an accepted invitation would link a CUSTOMER-realm user that is already linked to another Customer, then the system shall leave the Customer unlinked and report the message of RULE-PTY-020.
  Traces     : US-PTY-009
  Entities   : ENT-PTY-002
  Rationale  : One storefront account maps to one Customer.
  Source     : ADR-PTY-015; RULE-PTY-020
  Priority   : MEDIUM

#### AC-PTY-062 — [REQ-PTY-054]
  Given  : Customer 5001 holds secUserId 88001 and Customer 5011 has a pending invitation
  When   : the core security module reports acceptance of Customer 5011's invitation by user 88001
  Then   : Customer 5011's secUserId stays empty and the message ar «حساب العميل هذا مرتبط بعميل آخر.» · en «This customer account is already linked to another customer.» is reported

### REQ-PTY-055 — Guest matched by email
  Pattern    : event
  Statement  : When the storefront submits guest order details whose email equals, without regard to letter case, the email of an active Party, the system shall attach the order to that Party's Customer without creating a Party.
  Traces     : US-PTY-010
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : A guest who is already a Customer does not become a duplicate.
  Source     : POL-PTY-002, POL-PTY-001; module-registry decision 5 (as cited by the PRD); ADR-PTY-015
  Priority   : MEDIUM

#### AC-PTY-063 — [REQ-PTY-055]
  Given  : active Party P-000120 with email "nour@example.com" and active Customer 5001
  When   : the storefront submits guest details with email "NOUR@example.com"
  Then   : Customer 5001's identifier is returned and the tenant's Party count is unchanged

### REQ-PTY-056 — Guest matched by mobile
  Pattern    : event
  Statement  : When the storefront submits guest order details whose email matches no active Party and whose normalised mobile equals the normalised mobile of exactly one active Party, the system shall attach the order to that Party's Customer without creating a Party.
  Traces     : US-PTY-010
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : A guest known by mobile only is still the same Customer.
  Source     : POL-PTY-002, POL-PTY-001; ADR-PTY-015 (normalisation)
  Priority   : MEDIUM

#### AC-PTY-064 — [REQ-PTY-056]
  Given  : exactly one active Party P-000150 with mobile "+20 100 555 6666" and active Customer 5015; no Party with email "guest1@example.com"
  When   : the storefront submits guest details with email "guest1@example.com" and mobile "00201005556666"
  Then   : Customer 5015's identifier is returned and the tenant's Party count is unchanged

### REQ-PTY-057 — Customer role added to a matched Party
  Pattern    : event
  Statement  : When guest order details match an active Party that holds no Customer, the system shall add a Customer with customerGroupCode RETAIL to that Party and attach the order to it.
  Traces     : US-PTY-010, US-PTY-004
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : A supplier who shops online is held once as one Party.
  Source     : POL-PTY-001; ADR-PTY-014, ADR-PTY-015
  Priority   : MEDIUM

#### AC-PTY-065 — [REQ-PTY-057]
  Given  : active Party P-000204 with email "delta@example.com" holding a Supplier and no Customer
  When   : the storefront submits guest details with email "delta@example.com"
  Then   : one Customer linked to Party P-000204 with customerGroupCode RETAIL is saved and its identifier is returned

### REQ-PTY-058 — Guest details never change a matched Party
  Pattern    : unwanted
  Statement  : If guest order details are attached to an existing Party, then the system shall leave that Party's names, contacts and Addresses unchanged.
  Traces     : US-PTY-010
  Entities   : ENT-PTY-001, ENT-PTY-004
  Rationale  : Nobody alters a Customer's data by typing that Customer's contact.
  Source     : module-registry decision 5 (as cited by the PRD); ADR-PTY-015
  Priority   : MEDIUM

#### AC-PTY-066 — [REQ-PTY-058]
  Given  : Party P-000120 with nameEn "Nour Trading", mobile "+201001112222" and 1 active DELIVERY Address
  When   : guest details with email "nour@example.com", nameEn "Someone Else", mobile "+201009998888" and line1 "1 New St" are attached to Party P-000120
  Then   : Party P-000120 still has nameEn "Nour Trading", mobile "+201001112222" and exactly 1 active DELIVERY Address

### REQ-PTY-059 — Invitation to the recorded contact of a matched Customer
  Pattern    : event
  Statement  : When guest order details are attached to a Customer that has no secUserId, the system shall request from the core security module a CUSTOMER-realm invitation addressed to the email recorded on that Customer's Party, or to its recorded mobile when the email is empty.
  Traces     : US-PTY-010
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : The shopper reaches the existing account only by confirming it is theirs.
  Source     : module-registry decision 5 (as cited by the PRD); ADR-PTY-015
  Priority   : MEDIUM

#### AC-PTY-067 — [REQ-PTY-059]
  Given  : Customer 5015 of Party P-000150 has secUserId empty, email empty and mobile "+201005556666"
  When   : guest details with email "guest1@example.com" and mobile "00201005556666" are attached to Customer 5015
  Then   : one CUSTOMER-realm invitation addressed to "+201005556666" is requested, and no invitation addressed to "guest1@example.com" is requested

### REQ-PTY-060 — No invitation for an already linked Customer
  Pattern    : unwanted
  Statement  : If guest order details are attached to a Customer that already has a secUserId, then the system shall attach the order without requesting an invitation.
  Traces     : US-PTY-010
  Entities   : ENT-PTY-002
  Rationale  : The shopper signs in to the existing account instead.
  Source     : ADR-PTY-015
  Priority   : MEDIUM

#### AC-PTY-068 — [REQ-PTY-060]
  Given  : Customer 5001 holds secUserId 88001
  When   : guest details with email "nour@example.com" are attached to Customer 5001
  Then   : Customer 5001's identifier is returned and 0 invitations are requested

### REQ-PTY-061 — Mobile matching more than one Party
  Pattern    : unwanted
  Statement  : If guest order details match no active Party by email and their normalised mobile equals the mobile of more than one active Party, then the system shall create a new Customer under REQ-PTY-050.
  Traces     : US-PTY-010
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : An order is never attached to a Customer chosen at random.
  Source     : ADR-PTY-015
  Priority   : MEDIUM

#### AC-PTY-069 — [REQ-PTY-061]
  Given  : active Parties P-000160 and P-000161 both hold mobile "+201007770000" and no Party holds email "sam@example.com"
  When   : the storefront submits guest details with email "sam@example.com" and mobile "+201007770000"
  Then   : one new Party and one new Customer are saved, and neither P-000160 nor P-000161 receives the order

### REQ-PTY-062 — Matched Customer inactive
  Pattern    : unwanted
  Statement  : If guest order details match an active Party whose Customer is inactive, then the system shall refuse the guest order details with the message of RULE-PTY-021.
  Traces     : US-PTY-010
  Entities   : ENT-PTY-002
  Rationale  : A Customer the Owner deactivated does not buy online under a new record.
  Source     : ADR-PTY-015; RULE-PTY-021
  Priority   : MEDIUM

#### AC-PTY-070 — [REQ-PTY-062]
  Given  : active Party P-000130 with email "kareem@example.com", an active Supplier and an inactive Customer
  When   : the storefront submits guest details with email "kareem@example.com"
  Then   : no Customer is saved or attached and the message ar «تعذّر إتمام الطلب بهذه البيانات. يُرجى التواصل مع البائع.» · en «The order cannot be completed with these details. Please contact the seller.» is returned

### REQ-PTY-063 — Walk-in Customer and inactive Parties outside guest matching
  Pattern    : ubiquitous
  Statement  : The system shall exclude the walk-in Customer's Party and every inactive Party from guest order matching.
  Traces     : US-PTY-010, US-PTY-008
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : The walk-in record never becomes a storefront account.
  Source     : RULE-PTY-013; ADR-PTY-015
  Priority   : MEDIUM

#### AC-PTY-071 — [REQ-PTY-063]
  Given  : the walk-in Customer's Party holds mobile "+201000000000" (entered by an Owner) and no other Party holds it
  When   : the storefront submits guest details with email "new@example.com" and mobile "+201000000000"
  Then   : a new Party and Customer are saved, and the walk-in Customer's secUserId stays empty

### REQ-PTY-064 — Credit limit on a Customer
  Pattern    : complex
  Statement  : While `b2bCredit` is on, when an Owner saves a creditLimit on a Customer, the system shall save it as that Customer's credit limit.
  Traces     : US-PTY-011
  Entities   : ENT-PTY-002
  Rationale  : Wholesale customers buy on credit within an agreed limit.
  Source     : POL-PTY-003; KB §4.1; ADR-PTY-014; RULE-PTY-022, RULE-PTY-023
  Priority   : HIGH

#### AC-PTY-072 — [REQ-PTY-064]
  Given  : `b2bCredit` is on and Customer 5001 has creditLimit empty
  When   : the Owner saves creditLimit 50000 on Customer 5001
  Then   : Customer 5001 holds creditLimit 50000

### REQ-PTY-065 — Payment terms on a Customer
  Pattern    : complex
  Statement  : While `b2bCredit` is on, when an Owner saves a paymentTermDays on a Customer, the system shall save it as that Customer's payment terms.
  Traces     : US-PTY-011
  Entities   : ENT-PTY-002
  Rationale  : Wholesale customers pay within agreed terms.
  Source     : POL-PTY-003; KB §4.1; ADR-PTY-014; RULE-PTY-008, RULE-PTY-023
  Priority   : HIGH

#### AC-PTY-073 — [REQ-PTY-065]
  Given  : `b2bCredit` is on and Customer 5001 has paymentTermDays empty
  When   : the Owner saves paymentTermDays 45 on Customer 5001
  Then   : Customer 5001 holds paymentTermDays 45

### REQ-PTY-066 — Credit limit not negative
  Pattern    : unwanted
  Statement  : If a Customer save carries a creditLimit below 0, then the system shall reject the save with the message of RULE-PTY-022.
  Traces     : US-PTY-011
  Entities   : ENT-PTY-002
  Rationale  : A negative limit has no meaning.
  Source     : RULE-PTY-022
  Priority   : HIGH

#### AC-PTY-074 — [REQ-PTY-066]
  Given  : `b2bCredit` is on and Customer 5001
  When   : the Owner saves creditLimit -100
  Then   : nothing is saved and the message ar «حد الائتمان يجب أن يكون صفراً أو أكثر.» · en «The credit limit must be zero or more.» is shown

### REQ-PTY-067 — Customer payment terms range
  Pattern    : unwanted
  Statement  : If a Customer save carries a paymentTermDays below 0 or above 365, then the system shall reject the save with the message of RULE-PTY-008.
  Traces     : US-PTY-011
  Entities   : ENT-PTY-002
  Rationale  : Payment terms drive due dates in Settlements.
  Source     : ADR-PTY-014; RULE-PTY-008
  Priority   : HIGH

#### AC-PTY-075 — [REQ-PTY-067]
  Given  : `b2bCredit` is on and Customer 5001
  When   : the Owner saves paymentTermDays 366
  Then   : nothing is saved and the message ar «مدة السداد يجب أن تكون بين 0 و365 يوماً.» · en «Payment terms must be between 0 and 365 days.» is shown

### REQ-PTY-068 — No credit limit means no credit allowance
  Pattern    : ubiquitous
  Statement  : The system shall treat a Customer whose creditLimit is empty as a Customer with no credit allowance.
  Traces     : US-PTY-011, US-PTY-014
  Entities   : ENT-PTY-002
  Rationale  : Credit is granted explicitly, never by omission.
  Source     : ADR-PTY-017
  Priority   : HIGH

#### AC-PTY-076 — [REQ-PTY-068]
  Given  : `b2bCredit` is on and Customer 5002 has creditLimit empty
  When   : Sales & POS requests a credit check for Customer 5002 and sale amount 10
  Then   : the result is NO_CREDIT

### REQ-PTY-069 — Customer credit list
  Pattern    : event
  Statement  : When a user searches the Customer credit list, the system shall return a page of Customers matching the submitted filters on code, name, customerGroupCode and carries-credit, with each Customer's creditLimit and paymentTermDays.
  Traces     : US-PTY-011, US-PTY-013
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : The Owner reviews and maintains credit terms in one list.
  Source     : POL-PTY-003, POL-PTY-005
  Priority   : HIGH

#### AC-PTY-077 — [REQ-PTY-069]
  Given  : `b2bCredit` is on; 4 Customers carry a creditLimit or a paymentTermDays and 20 carry neither
  When   : the Owner searches the Customer credit list with carries-credit = yes
  Then   : a page with exactly the 4 Customers is returned, each showing its creditLimit and paymentTermDays

### REQ-PTY-070 — Credit out of sight while the flag is off
  Pattern    : state
  Statement  : While `b2bCredit` is off, the system shall omit the creditLimit and paymentTermDays fields of Customers, the credit filters, the credit history and the Customer credit, Credit override requests and Credit control settings screens from every menu and screen.
  Traces     : US-PTY-012
  Entities   : ENT-PTY-002, ENT-PTY-005
  Rationale  : Only what is enabled is shown; no dead menus.
  Source     : POL-PTY-004; KB §4.1, §6.3; DP §5 G2
  Priority   : HIGH

#### AC-PTY-078 — [REQ-PTY-070]
  Given  : `b2bCredit` is off and an Owner holding every PTY permission
  When   : the Owner opens the Party menu and the Customer screen of Customer 5001
  Then   : the menu shows Customers and Suppliers only (0 credit entries), and the Customer screen shows 0 credit fields and 0 credit filters

### REQ-PTY-071 — Credit fields omitted from reads while the flag is off
  Pattern    : state
  Statement  : While `b2bCredit` is off, the system shall omit creditLimit and paymentTermDays from every Customer read returned to users and consuming modules.
  Traces     : US-PTY-012
  Entities   : ENT-PTY-002
  Rationale  : No channel shows credit data while the business does not sell on credit.
  Source     : POL-PTY-004; KB §4.1
  Priority   : HIGH

#### AC-PTY-079 — [REQ-PTY-071]
  Given  : `b2bCredit` is off
  When   : Sales & POS requests Customer 5001
  Then   : the response carries no creditLimit and no paymentTermDays fields

### REQ-PTY-072 — Credit data refused while the flag is off
  Pattern    : unwanted
  Statement  : If a Customer save carries a creditLimit or a paymentTermDays, or a Credit override request is submitted, while `b2bCredit` is off, then the system shall reject it with the message of RULE-PTY-023.
  Traces     : US-PTY-012
  Entities   : ENT-PTY-002, ENT-PTY-005
  Rationale  : Hidden credit data cannot be written through the API.
  Source     : POL-PTY-004; RULE-PTY-023
  Priority   : HIGH

#### AC-PTY-080 — [REQ-PTY-072]
  Given  : `b2bCredit` is off and Customer 5001
  When   : an update request for Customer 5001 carries creditLimit 1000
  Then   : nothing is saved and the message ar «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · en «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» is returned

#### AC-PTY-081 — [REQ-PTY-072]
  Given  : `b2bCredit` is off
  When   : Sales & POS submits a Credit override request for Customer 5001 and sale amount 500
  Then   : no Credit override request is saved and the message of AC-PTY-080 is returned

### REQ-PTY-073 — Credit visible as soon as the flag is turned on
  Pattern    : event
  Statement  : When `b2bCredit` is turned on, the system shall show the credit fields, the credit filters and the credit screens from the next screen load without any setup step.
  Traces     : US-PTY-012
  Entities   : ENT-PTY-002
  Rationale  : A flag is turned on at any time without re-setup.
  Source     : POL-PTY-004; KB §6.2
  Priority   : HIGH

#### AC-PTY-082 — [REQ-PTY-073]
  Given  : `b2bCredit` is off and the Owner is on the Customer screen
  When   : `b2bCredit` is turned on and the Owner reloads the Customer screen
  Then   : the Customer screen shows the creditLimit and paymentTermDays fields, the menu shows Customer credit, Credit override requests and Credit control settings, and 0 setup steps are presented

### REQ-PTY-074 — Credit check not applicable while the flag is off
  Pattern    : state
  Statement  : While `b2bCredit` is off, the system shall answer every credit check with result NOT_APPLICABLE.
  Traces     : US-PTY-012, US-PTY-014
  Entities   : ENT-PTY-002
  Rationale  : Sales proceed without credit control when the business does not sell on credit.
  Source     : POL-PTY-004; ADR-PTY-017
  Priority   : HIGH

#### AC-PTY-083 — [REQ-PTY-074]
  Given  : `b2bCredit` is off
  When   : Sales & POS requests a credit check for Customer 5001 and sale amount 999999
  Then   : the result is NOT_APPLICABLE

### REQ-PTY-075 — Switch-off dependency answer
  Pattern    : event
  Statement  : When the capability-flag owner asks whether `b2bCredit` can be switched off, the system shall answer with the number of Customers that carry a creditLimit or a paymentTermDays.
  Traces     : US-PTY-013
  Entities   : ENT-PTY-002
  Rationale  : Turning a flag off is blocked while data depends on it.
  Source     : POL-PTY-005; KB §6.2; DP §5 G3; ADR-PTY-017; RULE-PTY-024
  Priority   : HIGH

#### AC-PTY-084 — [REQ-PTY-075]
  Given  : 3 Customers carry a creditLimit and 1 other Customer carries only a paymentTermDays
  When   : the capability-flag owner asks whether `b2bCredit` can be switched off
  Then   : the answer is 4

### REQ-PTY-076 — Switch-off refused while credit is in use
  Pattern    : unwanted
  Statement  : If switching `b2bCredit` off is requested while at least one Customer carries a creditLimit or a paymentTermDays, then the system shall refuse the switch-off with the message of RULE-PTY-024 and offer the next action of REQ-PTY-077.
  Traces     : US-PTY-013
  Entities   : ENT-PTY-002
  Rationale  : Credit data is never lost or hidden by accident; every error offers a next action.
  Source     : POL-PTY-005; KB §6.2, §6.5; RULE-PTY-024
  Priority   : HIGH

#### AC-PTY-085 — [REQ-PTY-076]
  Given  : `b2bCredit` is on and 4 Customers carry credit data
  When   : the Owner switches `b2bCredit` off
  Then   : `b2bCredit` stays on, the message ar «لا يمكن إيقاف الائتمان: لدى 4 من العملاء حد ائتمان أو مدة سداد. امسحها من شاشة ائتمان العملاء ثم أعد المحاولة.» · en «Credit cannot be turned off: 4 customers still have a credit limit or payment terms. Clear them on the Customer credit screen, then try again.» is shown with the action "Show customers with credit" / «عرض العملاء ذوي الائتمان»

#### AC-PTY-086 — [REQ-PTY-076]
  Given  : `b2bCredit` is on and 0 Customers carry credit data
  When   : the Owner switches `b2bCredit` off
  Then   : this module reports 0 dependent Customers and does not refuse the switch-off

### REQ-PTY-077 — Next action after a refused switch-off
  Pattern    : event
  Statement  : When the Owner chooses the next action offered with the message of RULE-PTY-024, the system shall open the Customer credit list filtered to Customers that carry a creditLimit or a paymentTermDays.
  Traces     : US-PTY-013
  Entities   : ENT-PTY-002
  Rationale  : No dead ends.
  Source     : KB §6.5
  Priority   : HIGH

#### AC-PTY-087 — [REQ-PTY-077]
  Given  : the refusal of AC-PTY-085 is shown
  When   : the Owner chooses "Show customers with credit" / «عرض العملاء ذوي الائتمان»
  Then   : the Customer credit list opens with filter carries-credit = yes and shows the 4 Customers

### REQ-PTY-078 — Clear a Customer's credit
  Pattern    : event
  Statement  : When an Owner clears the credit of a Customer, the system shall set that Customer's creditLimit and paymentTermDays to empty.
  Traces     : US-PTY-013, US-PTY-011
  Entities   : ENT-PTY-002
  Rationale  : The Owner can remove the data that blocks switching the flag off.
  Source     : POL-PTY-005; RULE-PTY-030
  Priority   : HIGH

#### AC-PTY-088 — [REQ-PTY-078]
  Given  : `b2bCredit` is on and Customer 5001 holds creditLimit 50000 and paymentTermDays 45
  When   : the Owner chooses "Clear credit" / «مسح الائتمان» on Customer 5001
  Then   : Customer 5001 holds creditLimit empty and paymentTermDays empty, and the switch-off dependency answer decreases by 1

### REQ-PTY-079 — Credit check
  Pattern    : complex
  Statement  : While `b2bCredit` is on, when a consuming module requests a credit check for a Customer and a sale amount, the system shall return the Customer's creditLimit, its open exposure, the projected exposure equal to the open exposure plus the sale amount, the Customer's currencyCode in which these amounts are expressed, and a result from lookup CREDIT_CHECK_RESULT.
  Traces     : US-PTY-014
  Entities   : ENT-PTY-002
  Rationale  : The seller knows the credit standing before completing a credit sale.
  Source     : POL-PTY-008, POL-PTY-003; module-registry EXTENSION POINT "Customer credit exposure" (as cited by the PRD); ADR-PTY-017
  Priority   : HIGH

#### AC-PTY-089 — [REQ-PTY-079]
  Given  : `b2bCredit` is on; Customer 5001 has creditLimit 50000 and open exposure 42000
  When   : Sales & POS requests a credit check for Customer 5001 and sale amount 5000
  Then   : the response carries creditLimit 50000, open exposure 42000, projected exposure 47000 and result WITHIN_LIMIT

#### AC-PTY-135 — [REQ-PTY-079]
  Given  : `b2bCredit` is on; the tenant's base currency is EGP and Customer 5001 was created with currencyCode EGP and creditLimit 50000
  When   : Sales & POS requests a credit check for Customer 5001 and sale amount 5000
  Then   : the response carries currencyCode EGP, and a Credit override request then created for Customer 5001 holds currencyCode EGP

### REQ-PTY-080 — Open exposure from contributing modules
  Pattern    : ubiquitous
  Statement  : The system shall compute a Customer's open exposure as the sum of the exposure amounts reported for that Customer by every registered credit exposure contributor, and as 0 when no contributor is registered.
  Traces     : US-PTY-014
  Entities   : ENT-PTY-002
  Rationale  : Receivables and open orders live in modules that depend on Party, so they report into it.
  Source     : module-registry EXTENSION POINT "Customer credit exposure" (as cited by the PRD); ADR-PTY-017
  Priority   : HIGH

#### AC-PTY-090 — [REQ-PTY-080]
  Given  : two registered contributors report 30000 and 12000 for Customer 5001
  When   : a credit check for Customer 5001 is computed
  Then   : the open exposure is 42000

#### AC-PTY-091 — [REQ-PTY-080]
  Given  : no credit exposure contributor is registered
  When   : a credit check for Customer 5001 is computed
  Then   : the open exposure is 0

### REQ-PTY-081 — Within the limit
  Pattern    : event
  Statement  : When the projected exposure of a credit check is less than or equal to the Customer's creditLimit, the system shall return result WITHIN_LIMIT.
  Traces     : US-PTY-014
  Entities   : ENT-PTY-002
  Rationale  : A sale within the limit proceeds without approval.
  Source     : POL-PTY-008
  Priority   : HIGH

#### AC-PTY-092 — [REQ-PTY-081]
  Given  : `b2bCredit` is on; Customer 5001 has creditLimit 50000 and open exposure 45000
  When   : a credit check for sale amount 5000 is requested
  Then   : the projected exposure is 50000 and the result is WITHIN_LIMIT

### REQ-PTY-082 — Over the limit
  Pattern    : unwanted
  Statement  : If the projected exposure of a credit check exceeds the Customer's creditLimit, then the system shall return result OVER_LIMIT together with the tenant's credit-overrun action.
  Traces     : US-PTY-014
  Entities   : ENT-PTY-002
  Rationale  : The seller learns of the overrun before the sale, with what to do next.
  Source     : POL-PTY-008; ADR-PTY-017
  Priority   : HIGH

#### AC-PTY-093 — [REQ-PTY-082]
  Given  : `b2bCredit` is on; Customer 5001 has creditLimit 50000 and open exposure 45000; the credit-overrun action is BLOCK_WITH_OVERRIDE
  When   : a credit check for sale amount 5001 is requested
  Then   : the projected exposure is 50001, the result is OVER_LIMIT and the returned credit-overrun action is BLOCK_WITH_OVERRIDE

### REQ-PTY-083 — No credit allowance
  Pattern    : unwanted
  Statement  : If a credit check is requested for a Customer whose creditLimit is empty, then the system shall return result NO_CREDIT together with the tenant's credit-overrun action.
  Traces     : US-PTY-014, US-PTY-008
  Entities   : ENT-PTY-002
  Rationale  : Customers without a limit, the walk-in Customer included, buy on credit only through the overrun handling.
  Source     : ADR-PTY-017; REQ-PTY-068
  Priority   : HIGH

#### AC-PTY-094 — [REQ-PTY-083]
  Given  : `b2bCredit` is on; the walk-in Customer 5000 has creditLimit empty; the credit-overrun action is WARN
  When   : a credit check for Customer 5000 and sale amount 100 is requested
  Then   : the result is NO_CREDIT and the returned credit-overrun action is WARN

### REQ-PTY-084 — Exposure unavailable
  Pattern    : unwanted
  Statement  : If a registered credit exposure contributor fails to report within 3 seconds during a credit check, then the system shall return result EXPOSURE_UNAVAILABLE together with the tenant's credit-overrun action.
  Traces     : US-PTY-014
  Entities   : ENT-PTY-002
  Rationale  : A credit sale is never passed as within the limit on incomplete data.
  Source     : ADR-PTY-017
  Priority   : HIGH

#### AC-PTY-095 — [REQ-PTY-084]
  Given  : `b2bCredit` is on; one of two registered contributors does not answer within 3 seconds
  When   : a credit check for Customer 5001 and sale amount 100 is requested
  Then   : the result is EXPOSURE_UNAVAILABLE, the open exposure is empty, and the response carries the credit-overrun action

### REQ-PTY-085 — Credit standing on the Customer credit screen
  Pattern    : complex
  Statement  : While `b2bCredit` is on, when a user opens a Customer on the Customer credit screen, the system shall display its creditLimit, its open exposure and its available credit equal to the creditLimit minus the open exposure.
  Traces     : US-PTY-014
  Entities   : ENT-PTY-001, ENT-PTY-002
  Rationale  : Seller and Owner see the standing before agreeing a credit sale.
  Source     : POL-PTY-008, POL-PTY-003
  Priority   : HIGH

#### AC-PTY-096 — [REQ-PTY-085]
  Given  : `b2bCredit` is on; Customer 5001 has creditLimit 50000 and open exposure 42000
  When   : a Seller/Cashier opens Customer 5001 on the Customer credit screen
  Then   : the screen displays credit limit 50000, open exposure 42000 and available credit 8000

### REQ-PTY-086 — Choose the credit-overrun action
  Pattern    : event
  Statement  : When an Owner saves a credit-overrun action from lookup CREDIT_OVERRUN_ACTION, the system shall save it as the tenant's credit-overrun setting in the core settings module.
  Traces     : US-PTY-015
  Entities   : ENT-PTY-002
  Rationale  : The credit control matches how strict the business wants to be.
  Source     : POL-PTY-008; business-policies decision 7 (as cited by the PRD); given `cu`
  Priority   : HIGH

#### AC-PTY-097 — [REQ-PTY-086]
  Given  : `b2bCredit` is on and the tenant's credit-overrun setting is BLOCK_WITH_OVERRIDE
  When   : the Owner saves WARN on the Credit control settings screen
  Then   : the tenant's credit-overrun setting reads WARN, and the next credit check returning OVER_LIMIT carries action WARN

### REQ-PTY-087 — Default credit-overrun action
  Pattern    : state
  Statement  : While the tenant has no saved credit-overrun setting, the system shall apply BLOCK_WITH_OVERRIDE.
  Traces     : US-PTY-015
  Entities   : ENT-PTY-002
  Rationale  : The policy default is block with override.
  Source     : POL-PTY-008; business-policies decision 7 "The default is block with override" (as cited by the PRD)
  Priority   : HIGH

#### AC-PTY-098 — [REQ-PTY-087]
  Given  : `b2bCredit` was just turned on and no credit-overrun setting has been saved
  When   : a credit check returns OVER_LIMIT
  Then   : the returned credit-overrun action is BLOCK_WITH_OVERRIDE and the Credit control settings screen shows BLOCK_WITH_OVERRIDE selected

### REQ-PTY-088 — No approval needed under WARN
  Pattern    : complex
  Statement  : While the credit-overrun action is WARN, when a credit check returns OVER_LIMIT, NO_CREDIT or EXPOSURE_UNAVAILABLE, the system shall mark the result as not requiring a Credit override request.
  Traces     : US-PTY-015
  Entities   : ENT-PTY-002
  Rationale  : Under WARN the seller is warned and completes the sale.
  Source     : POL-PTY-008; ADR-PTY-016
  Priority   : HIGH

#### AC-PTY-099 — [REQ-PTY-088]
  Given  : the credit-overrun action is WARN; Customer 5001 has creditLimit 50000 and open exposure 49000
  When   : a credit check for sale amount 2000 is requested
  Then   : the result is OVER_LIMIT with approval-required = no

### REQ-PTY-089 — Approval required under BLOCK_WITH_OVERRIDE
  Pattern    : complex
  Statement  : While the credit-overrun action is BLOCK_WITH_OVERRIDE, when a credit check returns OVER_LIMIT, NO_CREDIT or EXPOSURE_UNAVAILABLE, the system shall mark the result as requiring an APPROVED Credit override request.
  Traces     : US-PTY-015, US-PTY-016
  Entities   : ENT-PTY-002, ENT-PTY-005
  Rationale  : Under blocking, the sale waits for a permitted user's approval.
  Source     : POL-PTY-008; ADR-PTY-016
  Priority   : HIGH

#### AC-PTY-100 — [REQ-PTY-089]
  Given  : the credit-overrun action is BLOCK_WITH_OVERRIDE; Customer 5001 has creditLimit 50000 and open exposure 49000
  When   : a credit check for sale amount 2000 is requested
  Then   : the result is OVER_LIMIT with approval-required = yes

### REQ-PTY-090 — Credit-overrun setting protected
  Pattern    : unwanted
  Statement  : If a user without UPDATE on page PTY_CREDIT_SETTINGS saves the credit-overrun action, then the system shall refuse the save through the platform authorisation layer.
  Traces     : US-PTY-015
  Entities   : ENT-PTY-002
  Rationale  : Only the Owner sets how strict credit control is.
  Source     : profile security_model; D-06
  Priority   : HIGH

#### AC-PTY-101 — [REQ-PTY-090]
  Given  : a Seller/Cashier without UPDATE on page PTY_CREDIT_SETTINGS and the setting BLOCK_WITH_OVERRIDE
  When   : the Seller/Cashier submits WARN as the credit-overrun action
  Then   : the platform access-denied response is returned and the setting still reads BLOCK_WITH_OVERRIDE

### REQ-PTY-091 — Create a Credit override request
  Pattern    : complex
  Statement  : While the credit-overrun action is BLOCK_WITH_OVERRIDE, when a user requests approval for a credit check result of OVER_LIMIT, NO_CREDIT or EXPOSURE_UNAVAILABLE, the system shall create a Credit override request with status PENDING holding the Customer, the sale amount, the creditLimit, the open exposure, the credit check result, the sale document reference and the requester.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-002, ENT-PTY-005
  Rationale  : The seller always has a next action when the limit blocks the sale.
  Source     : POL-PTY-008; KB §6.5; ADR-PTY-016; RULE-PTY-023, RULE-PTY-025, RULE-PTY-032, RULE-PTY-033
  Priority   : HIGH

#### AC-PTY-102 — [REQ-PTY-091]
  Given  : BLOCK_WITH_OVERRIDE; Customer 5001 has creditLimit 50000 and open exposure 49000; a credit check for 2000 returned OVER_LIMIT on sale "POS-1-000455"
  When   : the Seller/Cashier chooses "Request approval" / «طلب موافقة»
  Then   : one Credit override request is saved with statusCode PENDING, customerFk 5001, saleAmount 2000, creditLimitAtRequest 50000, openExposure 49000, creditCheckResultCode OVER_LIMIT, sourceDocumentRef "POS-1-000455" and createdBy the Seller/Cashier

#### AC-PTY-103 — [REQ-PTY-091]
  Given  : a Credit override request submitted with saleAmount 0
  When   : the request is saved
  Then   : nothing is saved and the message ar «مبلغ البيع يجب أن يكون أكبر من صفر.» · en «The sale amount must be greater than zero.» is returned

### REQ-PTY-092 — Request number
  Pattern    : event
  Statement  : When a Credit override request is first saved, the system shall assign its docNo from the core number series and its docDate from the save time.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : Seller and approver refer to the request by number.
  Source     : profile NUMBERING; RULE-PTY-031
  Priority   : HIGH

#### AC-PTY-104 — [REQ-PTY-092]
  Given  : the core number series for Credit override requests returns CO-000031 as its next value
  When   : a Credit override request is saved at 2026-11-02 10:15
  Then   : its docNo is CO-000031 and its docDate is 2026-11-02 10:15; a later update carrying docNo CO-999999 leaves CO-000031 and returns ar «رقم الطلب يُنشأ تلقائياً ولا يمكن تعديله.» · en «The request number is assigned by the system and cannot be changed.»

### REQ-PTY-093 — Notify the approvers
  Pattern    : event
  Statement  : When a Credit override request is created, the system shall send an in-app notification to every active user holding UPDATE on page PTY_CREDIT_OVERRIDE.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : The request does not wait unseen.
  Source     : POL-PTY-008; ADR-PTY-016; given `notif`
  Priority   : HIGH

#### AC-PTY-105 — [REQ-PTY-093]
  Given  : 2 active users hold UPDATE on page PTY_CREDIT_OVERRIDE
  When   : Credit override request CO-000031 for "Nour Trading" is created
  Then   : each of the 2 users receives one in-app notification reading ar «طلب تجاوز حد الائتمان CO-000031 للعميل Nour Trading بانتظار موافقتك.» · en «Credit override request CO-000031 for Nour Trading is waiting for your approval.»

### REQ-PTY-094 — Approve a Credit override request
  Pattern    : event
  Statement  : When a user holding UPDATE on page PTY_CREDIT_OVERRIDE approves a PENDING Credit override request, the system shall set its statusCode to APPROVED and record decidedBy and decidedAt.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : A trusted customer is still served.
  Source     : POL-PTY-008; ADR-PTY-016; D-06; RULE-PTY-026
  Priority   : HIGH

#### AC-PTY-106 — [REQ-PTY-094]
  Given  : PENDING request CO-000031 and an Owner holding UPDATE on page PTY_CREDIT_OVERRIDE
  When   : the Owner approves CO-000031 at 2026-11-02 10:17
  Then   : CO-000031 has statusCode APPROVED, decidedBy the Owner and decidedAt 2026-11-02 10:17

### REQ-PTY-095 — Reject a Credit override request
  Pattern    : event
  Statement  : When a user holding UPDATE on page PTY_CREDIT_OVERRIDE rejects a PENDING Credit override request with a decisionReason, the system shall set its statusCode to REJECTED and record decidedBy, decidedAt and the decisionReason.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : The seller learns the outcome and the reason.
  Source     : POL-PTY-008; ADR-PTY-016; RULE-PTY-026, RULE-PTY-027
  Priority   : HIGH

#### AC-PTY-107 — [REQ-PTY-095]
  Given  : PENDING request CO-000032
  When   : the Owner rejects it with decisionReason "Overdue invoices"
  Then   : CO-000032 has statusCode REJECTED, decisionReason "Overdue invoices" and decidedBy the Owner

### REQ-PTY-096 — Cancel a Credit override request
  Pattern    : event
  Statement  : When the requester cancels a PENDING Credit override request, the system shall set its statusCode to CANCELLED.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : A sale settled another way leaves no open request.
  Source     : ADR-PTY-016; RULE-PTY-026, RULE-PTY-029
  Priority   : HIGH

#### AC-PTY-108 — [REQ-PTY-096]
  Given  : PENDING request CO-000033 created by Seller/Cashier S1
  When   : S1 cancels CO-000033
  Then   : CO-000033 has statusCode CANCELLED

#### AC-PTY-109 — [REQ-PTY-096]
  Given  : PENDING request CO-000033 created by Seller/Cashier S1
  When   : Seller/Cashier S2 cancels CO-000033
  Then   : CO-000033 stays PENDING and the message ar «لا يلغي هذا الطلب إلا من قدّمه.» · en «Only the requester can cancel this request.» is shown

### REQ-PTY-097 — Use an approved request
  Pattern    : event
  Statement  : When a consuming module completes a sale against an APPROVED Credit override request for the same Customer, the same sale document reference and a sale amount less than or equal to the request's saleAmount, the system shall set the request's statusCode to USED and record usedDocumentRef.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : One approval covers one sale — the sale it was requested for.
  Source     : ADR-PTY-016; ADR-PTY-042; RULE-PTY-028
  Priority   : HIGH

#### AC-PTY-110 — [REQ-PTY-097]
  Given  : APPROVED request CO-000031 for Customer 5001 with saleAmount 2000
  When   : Sales & POS completes sale "POS-1-000455" for Customer 5001 and amount 1950 against CO-000031
  Then   : CO-000031 has statusCode USED and usedDocumentRef "POS-1-000455"

### REQ-PTY-098 — Decision on a request that is not pending
  Pattern    : unwanted
  Statement  : If an approval, a rejection or a cancellation is submitted for a Credit override request whose statusCode is not PENDING, then the system shall refuse it with the message of RULE-PTY-026.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : A request is decided once.
  Source     : RULE-PTY-026
  Priority   : HIGH

#### AC-PTY-111 — [REQ-PTY-098]
  Given  : request CO-000032 with statusCode REJECTED
  When   : the Owner approves CO-000032
  Then   : CO-000032 stays REJECTED and the message ar «هذا الطلب لم يعد معلّقاً (الحالة: مرفوض).» · en «This request is no longer pending (status: Rejected).» is shown

### REQ-PTY-099 — Use that does not match the approval
  Pattern    : unwanted
  Statement  : If a consuming module uses a Credit override request that is not APPROVED, or is for another Customer, or for another sale document reference than its sourceDocumentRef, or covers a smaller saleAmount than the sale, then the system shall refuse the use with the message of RULE-PTY-028.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : An approval cannot be stretched to another sale.
  Source     : RULE-PTY-028; ADR-PTY-042
  Priority   : HIGH

#### AC-PTY-112 — [REQ-PTY-099]
  Given  : APPROVED request CO-000031 for Customer 5001 with saleAmount 2000
  When   : Sales & POS uses CO-000031 for Customer 5001 and amount 2500
  Then   : CO-000031 stays APPROVED and the message ar «الموافقة CO-000031 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · en «Approval CO-000031 does not cover this sale. Check the customer and the amount, or request a new approval.» is returned

#### AC-PTY-113 — [REQ-PTY-099]
  Given  : request CO-000031 with statusCode USED
  When   : Sales & POS uses CO-000031 again for Customer 5001 and amount 100
  Then   : the use is refused with the message of AC-PTY-112

#### AC-PTY-133 — [REQ-PTY-099]
  Given  : APPROVED request CO-000031 for Customer 5001, saleAmount 2000 and sourceDocumentRef "POS-1-000455"
  When   : Sales & POS uses CO-000031 for Customer 5001, amount 1500 and usedDocumentRef "POS-1-000460"
  Then   : CO-000031 stays APPROVED, usedDocumentRef stays empty and the message of AC-PTY-112 is returned

### REQ-PTY-100 — Rejection reason required
  Pattern    : unwanted
  Statement  : If a rejection of a Credit override request carries no decisionReason, then the system shall refuse the rejection with the message of RULE-PTY-027.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : The seller can tell the customer why.
  Source     : RULE-PTY-027
  Priority   : HIGH

#### AC-PTY-114 — [REQ-PTY-100]
  Given  : PENDING request CO-000034
  When   : the Owner rejects it with decisionReason empty
  Then   : CO-000034 stays PENDING and the message ar «أدخل سبب الرفض.» · en «Enter the reason for the rejection.» is shown

### REQ-PTY-101 — Search Credit override requests
  Pattern    : event
  Statement  : When a user searches Credit override requests, the system shall return a page of requests matching the submitted filters on docNo, statusCode, Customer and docDate range, with PENDING requests first and then by docDate descending.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : The approver works through the pending queue.
  Source     : ADR-PTY-016
  Priority   : HIGH

#### AC-PTY-115 — [REQ-PTY-101]
  Given  : requests CO-000031 (APPROVED, 2026-11-02), CO-000035 (PENDING, 2026-11-01) and CO-000036 (PENDING, 2026-11-03)
  When   : the Owner searches with no filter
  Then   : the page lists CO-000036, CO-000035, CO-000031 in that order

### REQ-PTY-102 — Requesters see their own requests
  Pattern    : state
  Statement  : While a user does not hold UPDATE on page PTY_CREDIT_OVERRIDE, the system shall return to that user only the Credit override requests the user created.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : Sellers follow their own requests; approvers see all.
  Source     : ADR-PTY-016; D-06
  Priority   : HIGH

#### AC-PTY-116 — [REQ-PTY-102]
  Given  : Seller/Cashier S1 created CO-000035 and Seller/Cashier S2 created CO-000036; neither holds UPDATE on page PTY_CREDIT_OVERRIDE
  When   : S1 searches Credit override requests with no filter
  Then   : the page lists CO-000035 only

### REQ-PTY-103 — Read a Credit override request
  Pattern    : event
  Statement  : When a consuming module requests a Credit override request by its identifier, the system shall return its docNo, statusCode, customerFk, saleAmount, decidedBy, decidedAt and decisionReason.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : The sale learns the outcome without leaving its screen.
  Source     : ADR-PTY-016
  Priority   : HIGH

#### AC-PTY-117 — [REQ-PTY-103]
  Given  : request CO-000031 approved by the Owner at 2026-11-02 10:17
  When   : Sales & POS requests CO-000031 by its identifier
  Then   : the response carries docNo CO-000031, statusCode APPROVED, customerFk 5001, saleAmount 2000, decidedBy the Owner and decidedAt 2026-11-02 10:17

### REQ-PTY-104 — Request refused when the sale is within the limit
  Pattern    : unwanted
  Statement  : If a Credit override request is submitted for a sale whose credit check result is WITHIN_LIMIT, then the system shall refuse it with the message of RULE-PTY-025.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : Approvals are raised only for real overruns.
  Source     : RULE-PTY-025
  Priority   : HIGH

#### AC-PTY-118 — [REQ-PTY-104]
  Given  : Customer 5001 has creditLimit 50000 and open exposure 40000
  When   : a Credit override request for sale amount 5000 is submitted
  Then   : no request is saved and the message ar «لا حاجة إلى موافقة: البيع ضمن حد ائتمان العميل.» · en «No approval is needed: the sale is within the customer's credit limit.» is returned

### REQ-PTY-105 — No Credit override request for the walk-in Customer
  Pattern    : unwanted
  Statement  : If a Credit override request is submitted for the walk-in Customer, then the system shall refuse it with the message of RULE-PTY-033.
  Traces     : US-PTY-016, US-PTY-008
  Entities   : ENT-PTY-002, ENT-PTY-005
  Rationale  : Credit is granted to named Customers only.
  Source     : RULE-PTY-033
  Priority   : HIGH

#### AC-PTY-119 — [REQ-PTY-105]
  Given  : BLOCK_WITH_OVERRIDE and the walk-in Customer 5000
  When   : a Credit override request for Customer 5000 and sale amount 300 is submitted
  Then   : no request is saved and the message ar «لا يمكن البيع الآجل للعميل النقدي. سجّل العميل باسمه أولاً.» · en «The walk-in customer cannot buy on credit. Register the customer by name first.» is returned

### REQ-PTY-106 — Audit of credit changes
  Pattern    : event
  Statement  : When a Customer's creditLimit or paymentTermDays changes, the system shall record in the core audit module an event holding the Customer, the field, the old value, the new value, the user and the time.
  Traces     : US-PTY-017
  Entities   : ENT-PTY-002
  Rationale  : Credit decisions stay accountable.
  Source     : POL-PTY-003, POL-PTY-008; module-registry AUTO (credit changes recorded in given `audit`), as cited by the PRD; RULE-PTY-030
  Priority   : —

#### AC-PTY-120 — [REQ-PTY-106]
  Given  : Customer 5001 holds creditLimit 50000
  When   : the Owner saves creditLimit 60000 at 2026-11-04 09:00
  Then   : one audit event is recorded with Customer 5001, field creditLimit, old value 50000, new value 60000, the Owner as user and time 2026-11-04 09:00

### REQ-PTY-107 — Audit of override decisions
  Pattern    : event
  Statement  : When a Credit override request is approved or rejected, the system shall record in the core audit module an event holding the request docNo, the Customer, the saleAmount, the new statusCode, the decider and the time.
  Traces     : US-PTY-017, US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : Overrun approvals are accountable.
  Source     : POL-PTY-008; module-registry AUTO (as cited by the PRD)
  Priority   : —

#### AC-PTY-121 — [REQ-PTY-107]
  Given  : PENDING request CO-000031 for Customer 5001 and saleAmount 2000
  When   : the Owner approves it at 2026-11-02 10:17
  Then   : one audit event is recorded with docNo CO-000031, Customer 5001, saleAmount 2000, statusCode APPROVED, the Owner as decider and time 2026-11-02 10:17

### REQ-PTY-108 — Credit history of a Customer
  Pattern    : event
  Statement  : When a user opens the credit history of a Customer, the system shall list the audit events of that Customer's creditLimit and paymentTermDays changes with field, old value, new value, user and time, newest first.
  Traces     : US-PTY-017
  Entities   : ENT-PTY-002
  Rationale  : The Owner sees who changed credit terms and when.
  Source     : POL-PTY-003; given `audit`
  Priority   : —

#### AC-PTY-122 — [REQ-PTY-108]
  Given  : `b2bCredit` is on; Customer 5001 had creditLimit changed on 2026-10-01 and on 2026-11-04, and paymentTermDays changed on 2026-10-15
  When   : the Owner opens the credit history of Customer 5001
  Then   : 3 entries are listed in the order 2026-11-04, 2026-10-15, 2026-10-01, each with field, old value, new value and user

### REQ-PTY-109 — Unrecorded credit change refused
  Pattern    : unwanted
  Statement  : If the core audit module cannot record a creditLimit or paymentTermDays change, or the approval or rejection of a Credit override request, then the system shall reject the change or the decision with the message of RULE-PTY-030.
  Traces     : US-PTY-017, US-PTY-016
  Entities   : ENT-PTY-002, ENT-PTY-005
  Rationale  : No credit change and no overrun decision is saved without its history.
  Source     : RULE-PTY-030
  Priority   : —

#### AC-PTY-123 — [REQ-PTY-109]
  Given  : the core audit module rejects writes
  When   : the Owner saves creditLimit 70000 on Customer 5001
  Then   : Customer 5001 keeps its previous creditLimit and the message ar «تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة.» · en «The change could not be recorded in the audit history, so it was not saved. Try again.» is shown

#### AC-PTY-134 — [REQ-PTY-109]
  Given  : `b2bCredit` is on; PENDING request CO-000031; the core audit module rejects writes
  When   : the Owner approves CO-000031
  Then   : CO-000031 stays PENDING with decidedBy and decidedAt empty, and the message of AC-PTY-123 is shown

### REQ-PTY-110 — Inactive Customer or Party refused for credit operations and address writes
  Pattern    : unwanted
  Statement  : If a credit check, a creditLimit or paymentTermDays save, or the creation or use of a Credit override request targets an inactive Customer, or an Address create, update or set-default targets an inactive Party, then the system shall refuse it with the message of RULE-PTY-005, while the reads, the credit history, Clear credit and Address deactivation stay allowed.
  Traces     : US-PTY-014, US-PTY-016, US-PTY-011, US-PTY-005
  Entities   : ENT-PTY-001, ENT-PTY-002, ENT-PTY-004, ENT-PTY-005
  Rationale  : A deactivated counterparty receives no new credit and no new address, and can still be cleaned up (ARCH-5).
  Source     : ARCH-5; RULE-PTY-005; ADR-PTY-041
  Priority   : HIGH

#### AC-PTY-124 — [REQ-PTY-110]
  Given  : `b2bCredit` is on; Customer 5002 (code P-000121) has isActiveFl false and creditLimit 10000
  When   : Sales & POS requests a credit check for Customer 5002 and sale amount 100
  Then   : the request is refused with ar «P-000121 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · en «P-000121 is inactive and cannot be used on a new document. Reactivate it or choose another party.» and no result code is returned

#### AC-PTY-125 — [REQ-PTY-110]
  Given  : `b2bCredit` is on, BLOCK_WITH_OVERRIDE; Customer 5002 (code P-000121) has isActiveFl false
  When   : a Credit override request for Customer 5002 and sale amount 500 is submitted
  Then   : no request is saved and the message of AC-PTY-124 is returned

#### AC-PTY-126 — [REQ-PTY-110]
  Given  : Party P-000124 has isActiveFl false (its Customer and its Supplier are inactive)
  When   : the Owner saves a new BILLING Address with line1 "5 Nile St" on Party P-000124
  Then   : no Address is saved and the message ar «P-000124 غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر.» · en «P-000124 is inactive and cannot be used on a new document. Reactivate it or choose another party.» is returned

#### AC-PTY-127 — [REQ-PTY-110]
  Given  : `b2bCredit` is on; Customer 5002 (code P-000121) has isActiveFl false, creditLimit 10000 and paymentTermDays 30
  When   : the Owner saves creditLimit 20000 on Customer 5002, then chooses "Clear credit" / «مسح الائتمان» on Customer 5002
  Then   : the save is refused with the message of AC-PTY-124 and creditLimit stays 10000; the clear succeeds and Customer 5002 holds creditLimit empty and paymentTermDays empty

### REQ-PTY-111 — Open requests closed when their Customer is deactivated
  Pattern    : event
  Statement  : When a Customer is deactivated, the system shall set every PENDING or APPROVED Credit override request of that Customer to CANCELLED in the same save.
  Traces     : US-PTY-016, US-PTY-001
  Entities   : ENT-PTY-002, ENT-PTY-005
  Rationale  : An approval never outlives its Customer's deactivation.
  Source     : ARCH-5; ADR-PTY-041
  Priority   : HIGH

#### AC-PTY-128 — [REQ-PTY-111]
  Given  : active Customer 5001 with request CO-000040 PENDING, request CO-000041 APPROVED and request CO-000031 USED
  When   : the Owner deactivates Customer 5001
  Then   : CO-000040 and CO-000041 have statusCode CANCELLED and CO-000031 stays USED

#### AC-PTY-129 — [REQ-PTY-111]
  Given  : Customer 5001 was deactivated while request CO-000041 was APPROVED, and was reactivated afterwards
  When   : Sales & POS uses CO-000041 for Customer 5001
  Then   : CO-000041 stays CANCELLED and the message ar «الموافقة CO-000041 لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة.» · en «Approval CO-000041 does not cover this sale. Check the customer and the amount, or request a new approval.» is returned

### REQ-PTY-112 — Decisions and use refused while the flag is off
  Pattern    : unwanted
  Statement  : If an approval, a rejection or a use of a Credit override request is submitted while `b2bCredit` is off, then the system shall refuse it with the message of RULE-PTY-023, while a cancellation and the reads of the request stay allowed.
  Traces     : US-PTY-012, US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : Hidden credit control takes no decision; requests resume when the flag is turned on.
  Source     : POL-PTY-004; RULE-PTY-023; ADR-PTY-041
  Priority   : HIGH

#### AC-PTY-130 — [REQ-PTY-112]
  Given  : `b2bCredit` is off; PENDING request CO-000042
  When   : the Owner approves CO-000042
  Then   : CO-000042 stays PENDING and the message ar «الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد.» · en «Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.» is returned

#### AC-PTY-131 — [REQ-PTY-112]
  Given  : `b2bCredit` is off; PENDING request CO-000043 created by Seller/Cashier S1
  When   : S1 cancels CO-000043
  Then   : CO-000043 has statusCode CANCELLED

### REQ-PTY-113 — One open request per sale
  Pattern    : unwanted
  Statement  : If a Credit override request is submitted for a Customer and a sale document reference that already have a PENDING or APPROVED Credit override request, then the system shall refuse it with the message of RULE-PTY-034.
  Traces     : US-PTY-016
  Entities   : ENT-PTY-005
  Rationale  : One sale waits on one approval; a second request would let two approvers decide the same sale.
  Source     : ADR-PTY-042; RULE-PTY-034
  Priority   : HIGH

#### AC-PTY-132 — [REQ-PTY-113]
  Given  : `b2bCredit` is on, BLOCK_WITH_OVERRIDE; PENDING request CO-000031 for Customer 5001 and sourceDocumentRef "POS-1-000455"
  When   : a second Credit override request for Customer 5001, sale amount 2000 and sourceDocumentRef "POS-1-000455" is submitted
  Then   : no request is saved and the message ar «لهذا البيع طلب موافقة مفتوح بالفعل (CO-000031). تابع ذلك الطلب بدلاً من إنشاء طلب جديد.» · en «This sale already has an open approval request (CO-000031). Follow that request instead of creating a new one.» is returned

## A5 — Business rules

Rules that read capability flag `b2bCredit` read it through the FeatureFlags API only (DP §5 G1); the flag is noted on a `Flag` line and is not a `Data source` field.

### RULE-PTY-001 — Party name required
  Scope      : ENT-PTY-001
  Trigger    : on create · on update
  Statement  : The system shall require at least one of nameAr or nameEn on every Party.
  Message    : ar: أدخل اسم الطرف بالعربية أو بالإنجليزية. · en: Enter the party name in Arabic or English.
  Traces     : REQ-PTY-009, REQ-PTY-004, REQ-PTY-016
  Data source: ENT-PTY-001.nameAr, ENT-PTY-001.nameEn
  Source     : D-02

### RULE-PTY-002 — Email unique among active Parties
  Scope      : ENT-PTY-001
  Trigger    : on create · on update of email · on reactivation of a role
  Statement  : The system shall prevent two active Parties of a tenant from holding the same email, compared without letter case and surrounding spaces.
  Message    : ar: هذا البريد الإلكتروني مسجّل لطرف نشط آخر ({code}). افتح ذلك الطرف بدلاً من إنشاء طرف جديد. · en: This email is already registered to another active party ({code}). Open that party instead of creating a new one.
  Traces     : REQ-PTY-010, REQ-PTY-015, REQ-PTY-021, REQ-PTY-029
  Data source: ENT-PTY-001.email, ENT-PTY-001.isActiveFl
  Source     : POL-PTY-001; D-03
  Test-Hint  : the message offers an action that opens the Party named by {code}

### RULE-PTY-003 — Email format
  Scope      : ENT-PTY-001
  Trigger    : on create · on update of email
  Statement  : The system shall validate that a non-empty email has one "@" with a non-empty local part and a domain part containing at least one ".".
  Message    : ar: صيغة البريد الإلكتروني غير صحيحة، مثال: name@example.com · en: The email format is not valid, for example: name@example.com
  Traces     : REQ-PTY-011
  Data source: ENT-PTY-001.email
  Source     : ADR-PTY-015

### RULE-PTY-004 — Mobile format
  Scope      : ENT-PTY-001
  Trigger    : on create · on update of mobile
  Statement  : The system shall validate that a non-empty mobile, after removing spaces and dashes, is an optional leading "+" followed by 7 to 15 digits.
  Message    : ar: رقم الجوال يتكون من 7 إلى 15 رقماً، ويمكن أن يبدأ بعلامة +. · en: The mobile number has 7 to 15 digits and can start with +.
  Traces     : REQ-PTY-011
  Data source: ENT-PTY-001.mobile
  Source     : ADR-PTY-015 (ITU-T E.164 maximum of 15 digits)

### RULE-PTY-005 — Inactive role refused for new documents, credit operations and address writes
  Scope      : ENT-PTY-001, ENT-PTY-002, ENT-PTY-003, ENT-PTY-004, ENT-PTY-005
  Trigger    : on a consuming module's request for a new document · on a credit check · on a creditLimit or paymentTermDays save · on create or use of a Credit override request · on an Address create, update or set-default
  Statement  : The system shall prevent an inactive Customer or Supplier from being used on a new document; an inactive Customer from a credit check, a creditLimit or paymentTermDays save and the creation or use of a Credit override request; and an inactive Party from an Address create, update or set-default. Reads, credit history, Clear credit, the cancellation of a request and Address deactivation stay allowed.
  Message    : ar: {code} غير نشط ولا يمكن استخدامه في مستند جديد. أعد تنشيطه أو اختر طرفاً آخر. · en: {code} is inactive and cannot be used on a new document. Reactivate it or choose another party.
  Traces     : REQ-PTY-003, REQ-PTY-110
  Data source: ENT-PTY-001.isActiveFl, ENT-PTY-002.isActiveFl, ENT-PTY-003.isActiveFl
  Source     : ARCH-5; ADR-PTY-041
  Test-Hint  : {code} = the Party code of the Customer, the Supplier or the Party

### RULE-PTY-006 — Effect of deactivation on consuming modules
  Scope      : ENT-PTY-002, ENT-PTY-003, ENT-PTY-004
  Trigger    : on deactivation
  Statement  : The system shall keep a deactivated Customer, Supplier or Address readable by every existing reference, notify consuming modules through a party-role-deactivated event for Customers and Suppliers, and prevent its selection on new documents (prevent new use + notify; no cascade).
  Message    : ar: تم إيقاف {code}. تبقى المستندات السابقة كما هي، ولن يُعرض في المستندات الجديدة. · en: {code} is deactivated. Earlier documents keep it; it is no longer offered on new documents.
  Traces     : REQ-PTY-014, REQ-PTY-020, REQ-PTY-023, REQ-PTY-033
  Data source: ENT-PTY-002.isActiveFl, ENT-PTY-003.isActiveFl, ENT-PTY-004.isActiveFl
  Source     : ARCH-5

### RULE-PTY-007 — Party code system-assigned and read-only
  Scope      : ENT-PTY-001
  Trigger    : on create · on update
  Statement  : The system shall assign the Party code from the core number series on first save and prevent every later change to it.
  Message    : ar: رقم الطرف يُنشأ تلقائياً ولا يمكن تعديله. · en: The party code is assigned by the system and cannot be changed.
  Traces     : REQ-PTY-006, REQ-PTY-012, REQ-PTY-017
  Data source: ENT-PTY-001.code
  Source     : profile NUMBERING ("document numbers come from the core sequence module; never generated in a business module"); D-01

### RULE-PTY-008 — Payment terms range
  Scope      : ENT-PTY-002, ENT-PTY-003
  Trigger    : on create · on update
  Statement  : The system shall require a non-empty paymentTermDays to be a whole number from 0 to 365.
  Message    : ar: مدة السداد يجب أن تكون بين 0 و365 يوماً. · en: Payment terms must be between 0 and 365 days.
  Traces     : REQ-PTY-022, REQ-PTY-065, REQ-PTY-067
  Data source: ENT-PTY-002.paymentTermDays, ENT-PTY-003.paymentTermDays
  Source     : ADR-PTY-014

### RULE-PTY-009 — One role of each kind per Party
  Scope      : ENT-PTY-002, ENT-PTY-003
  Trigger    : on create of a Customer or a Supplier
  Statement  : The system shall prevent a Party from holding more than one Customer or more than one Supplier.
  Message    : ar: هذا الطرف له صفة {roleAr} بالفعل. · en: This party already holds the {roleEn} role.
  Traces     : REQ-PTY-027
  Data source: ENT-PTY-002.partyFk, ENT-PTY-003.partyFk
  Source     : ADR-PTY-014
  Test-Hint  : {roleAr}/{roleEn} = عميل/Customer or مورّد/Supplier

### RULE-PTY-010 — Walk-in Customer stays active
  Scope      : ENT-PTY-002
  Trigger    : on deactivation
  Statement  : The system shall prevent deactivation of the Customer whose isWalkInFl is true.
  Message    : ar: العميل النقدي لازم لنقطة البيع ولا يمكن إيقافه. · en: The walk-in customer is required by the point of sale and cannot be deactivated.
  Traces     : REQ-PTY-047, REQ-PTY-014
  Data source: ENT-PTY-002.isWalkInFl
  Source     : POL-PTY-009

### RULE-PTY-011 — Walk-in Customer carries no credit
  Scope      : ENT-PTY-002
  Trigger    : on update of creditLimit or paymentTermDays
  Statement  : The system shall prevent a creditLimit or a paymentTermDays on the Customer whose isWalkInFl is true.
  Message    : ar: لا يُمنح العميل النقدي حد ائتمان أو مدة سداد. سجّل العميل باسمه لمنحه الائتمان. · en: The walk-in customer cannot have a credit limit or payment terms. Register the customer by name to grant credit.
  Traces     : REQ-PTY-048
  Data source: ENT-PTY-002.isWalkInFl, ENT-PTY-002.creditLimit, ENT-PTY-002.paymentTermDays
  Source     : POL-PTY-009, POL-PTY-003

### RULE-PTY-012 — Walk-in Party has no Supplier
  Scope      : ENT-PTY-002, ENT-PTY-003
  Trigger    : on create of a Supplier
  Statement  : The system shall prevent a Supplier on the Party of the Customer whose isWalkInFl is true.
  Message    : ar: لا يمكن إضافة صفة المورّد إلى العميل النقدي. · en: The walk-in customer cannot also be a supplier.
  Traces     : REQ-PTY-049
  Data source: ENT-PTY-002.isWalkInFl, ENT-PTY-002.partyFk
  Source     : POL-PTY-009

### RULE-PTY-013 — Walk-in Customer never matched nor linked
  Scope      : ENT-PTY-001, ENT-PTY-002
  Trigger    : on guest order matching · on invitation acceptance
  Statement  : The system shall exclude the walk-in Customer's Party and inactive Parties from guest matching, and prevent a secUserId on the walk-in Customer.
  Message    : ar: لا يمكن ربط العميل النقدي بحساب عميل في المتجر الإلكتروني. · en: The walk-in customer cannot be linked to a storefront customer account.
  Traces     : REQ-PTY-063, REQ-PTY-052
  Data source: ENT-PTY-002.isWalkInFl, ENT-PTY-001.isActiveFl
  Source     : ADR-PTY-015

### RULE-PTY-014 — One walk-in Customer per tenant
  Scope      : ENT-PTY-002
  Trigger    : on create of the walk-in Customer
  Statement  : The system shall prevent a second Customer with isWalkInFl true in a tenant.
  Message    : ar: لهذا المستأجر عميل نقدي بالفعل. · en: This tenant already has a walk-in customer.
  Traces     : REQ-PTY-044, REQ-PTY-046
  Data source: ENT-PTY-002.isWalkInFl
  Source     : POL-PTY-009; module-registry decision 9 (as cited by the PRD)

### RULE-PTY-015 — Address line required
  Scope      : ENT-PTY-004
  Trigger    : on create · on update
  Statement  : The system shall require a non-empty line1 on every Address.
  Message    : ar: أدخل السطر الأول من العنوان. · en: Enter the first address line.
  Traces     : REQ-PTY-037
  Data source: ENT-PTY-004.line1
  Source     : US-PTY-005

### RULE-PTY-016 — Address type from the lookup
  Scope      : ENT-PTY-004
  Trigger    : on create · on update
  Statement  : The system shall require addressTypeCode to be an active value of lookup ADDRESS_TYPE.
  Message    : ar: اختر نوع عنوان من القائمة. · en: Choose an address type from the list.
  Traces     : REQ-PTY-038
  Data source: ENT-PTY-004.addressTypeCode
  Source     : profile LOOKUPS

### RULE-PTY-017 — One default Address per type per Party
  Scope      : ENT-PTY-004
  Trigger    : on create · on set-default · on deactivation
  Statement  : The system shall keep exactly one active default Address per Party per address type while the Party has an active Address of that type, choosing the most recently updated remaining one when the default is deactivated.
  Message    : ar: أصبح هذا العنوان هو العنوان الافتراضي من نوع {typeAr}. · en: This address is now the default {typeEn} address.
  Traces     : REQ-PTY-034, REQ-PTY-035, REQ-PTY-036
  Data source: ENT-PTY-004.partyFk, ENT-PTY-004.addressTypeCode, ENT-PTY-004.isDefaultFl, ENT-PTY-004.isActiveFl, ENT-PTY-004.updatedAt
  Source     : D-09

### RULE-PTY-018 — Customer group from the lookup
  Scope      : ENT-PTY-002
  Trigger    : on create · on update
  Statement  : The system shall require customerGroupCode to be an active value of lookup CUSTOMER_GROUP.
  Message    : ar: اختر مجموعة عملاء من القائمة. · en: Choose a customer group from the list.
  Traces     : REQ-PTY-040
  Data source: ENT-PTY-002.customerGroupCode
  Source     : profile LOOKUPS

### RULE-PTY-019 — Guest contact required
  Scope      : ENT-PTY-001
  Trigger    : on guest order details submission
  Statement  : The system shall require at least one of email or mobile in guest order details.
  Message    : ar: أدخل البريد الإلكتروني أو رقم الجوال لإتمام الطلب. · en: Enter an email or a mobile number to complete the order.
  Traces     : REQ-PTY-053
  Data source: ENT-PTY-001.email, ENT-PTY-001.mobile
  Source     : ADR-PTY-015

### RULE-PTY-020 — One Customer per CUSTOMER-realm user
  Scope      : ENT-PTY-002
  Trigger    : on invitation acceptance
  Statement  : The system shall prevent two Customers of a tenant from holding the same secUserId.
  Message    : ar: حساب العميل هذا مرتبط بعميل آخر. · en: This customer account is already linked to another customer.
  Traces     : REQ-PTY-054, REQ-PTY-052
  Data source: ENT-PTY-002.secUserId
  Source     : ADR-PTY-015

### RULE-PTY-021 — Guest matched to an inactive Customer
  Scope      : ENT-PTY-002
  Trigger    : on guest order matching
  Statement  : The system shall prevent attaching guest order details to a matched Party whose Customer is inactive.
  Message    : ar: تعذّر إتمام الطلب بهذه البيانات. يُرجى التواصل مع البائع. · en: The order cannot be completed with these details. Please contact the seller.
  Traces     : REQ-PTY-062
  Data source: ENT-PTY-002.isActiveFl
  Source     : ADR-PTY-015
  Test-Hint  : the message does not reveal that a Customer with this contact exists

### RULE-PTY-022 — Credit limit not negative
  Scope      : ENT-PTY-002
  Trigger    : on update of creditLimit
  Statement  : The system shall require a non-empty creditLimit to be 0 or more.
  Message    : ar: حد الائتمان يجب أن يكون صفراً أو أكثر. · en: The credit limit must be zero or more.
  Traces     : REQ-PTY-066, REQ-PTY-064
  Data source: ENT-PTY-002.creditLimit
  Source     : POL-PTY-003

### RULE-PTY-023 — Credit data only while the flag is on
  Scope      : ENT-PTY-002, ENT-PTY-005
  Trigger    : on update of creditLimit or paymentTermDays · on create of a Credit override request · on approve, reject or use of a Credit override request
  Statement  : The system shall prevent saving a Customer's creditLimit or paymentTermDays, and creating, approving, rejecting or using a Credit override request, while `b2bCredit` is off; cancelling a request stays allowed.
  Message    : ar: الائتمان غير مفعّل. فعّل مفتاح b2bCredit من الإعدادات لاستخدام حد الائتمان ومدة السداد. · en: Credit is not enabled. Turn on b2bCredit in settings to use credit limits and payment terms.
  Traces     : REQ-PTY-072, REQ-PTY-064, REQ-PTY-065, REQ-PTY-091, REQ-PTY-112
  Data source: ENT-PTY-002.creditLimit, ENT-PTY-002.paymentTermDays, ENT-PTY-005.saleAmount, ENT-PTY-005.statusCode
  Flag       : b2bCredit (FeatureFlags)
  Source     : POL-PTY-004

### RULE-PTY-024 — Flag switch-off blocked while credit is in use
  Scope      : ENT-PTY-002
  Trigger    : on a request to switch `b2bCredit` off
  Statement  : The system shall prevent switching `b2bCredit` off while at least one Customer carries a creditLimit or a paymentTermDays.
  Message    : ar: لا يمكن إيقاف الائتمان: لدى {count} من العملاء حد ائتمان أو مدة سداد. امسحها من شاشة ائتمان العملاء ثم أعد المحاولة. · en: Credit cannot be turned off: {count} customers still have a credit limit or payment terms. Clear them on the Customer credit screen, then try again.
  Traces     : REQ-PTY-075, REQ-PTY-076
  Data source: ENT-PTY-002.creditLimit, ENT-PTY-002.paymentTermDays
  Flag       : b2bCredit (FeatureFlags)
  Source     : POL-PTY-005; KB §6.2; DP §5 G3

### RULE-PTY-025 — Request only for a real overrun
  Scope      : ENT-PTY-005
  Trigger    : on create
  Statement  : The system shall prevent creating a Credit override request unless its credit check result is OVER_LIMIT, NO_CREDIT or EXPOSURE_UNAVAILABLE.
  Message    : ar: لا حاجة إلى موافقة: البيع ضمن حد ائتمان العميل. · en: No approval is needed: the sale is within the customer's credit limit.
  Traces     : REQ-PTY-104, REQ-PTY-091
  Data source: ENT-PTY-005.saleAmount, ENT-PTY-005.openExposure, ENT-PTY-005.creditLimitAtRequest, ENT-PTY-005.creditCheckResultCode
  Source     : ADR-PTY-016

### RULE-PTY-026 — Decisions only on PENDING requests
  Scope      : ENT-PTY-005
  Trigger    : on approve · on reject · on cancel
  Statement  : The system shall prevent approving, rejecting or cancelling a Credit override request whose statusCode is not PENDING.
  Message    : ar: هذا الطلب لم يعد معلّقاً (الحالة: {statusAr}). · en: This request is no longer pending (status: {statusEn}).
  Traces     : REQ-PTY-098, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096
  Data source: ENT-PTY-005.statusCode
  Source     : ADR-PTY-016; A7

### RULE-PTY-027 — Rejection reason required
  Scope      : ENT-PTY-005
  Trigger    : on reject
  Statement  : The system shall require a non-empty decisionReason when a Credit override request is rejected.
  Message    : ar: أدخل سبب الرفض. · en: Enter the reason for the rejection.
  Traces     : REQ-PTY-100, REQ-PTY-095
  Data source: ENT-PTY-005.decisionReason
  Source     : ADR-PTY-016; KB §6.5

### RULE-PTY-028 — Use only within the approval
  Scope      : ENT-PTY-005
  Trigger    : on use by a consuming module
  Statement  : The system shall prevent using a Credit override request unless its statusCode is APPROVED, its customerFk is the sale's Customer, its sourceDocumentRef is the sale's document reference and its saleAmount is greater than or equal to the sale amount.
  Message    : ar: الموافقة {docNo} لا تغطي هذا البيع. تحقّق من العميل والمبلغ، أو اطلب موافقة جديدة. · en: Approval {docNo} does not cover this sale. Check the customer and the amount, or request a new approval.
  Traces     : REQ-PTY-099, REQ-PTY-097, REQ-PTY-111
  Data source: ENT-PTY-005.statusCode, ENT-PTY-005.customerFk, ENT-PTY-005.sourceDocumentRef, ENT-PTY-005.saleAmount
  Source     : ADR-PTY-016; ADR-PTY-042

### RULE-PTY-029 — Only the requester cancels
  Scope      : ENT-PTY-005
  Trigger    : on cancel
  Statement  : The system shall prevent a user other than the request's creator from cancelling a Credit override request.
  Message    : ar: لا يلغي هذا الطلب إلا من قدّمه. · en: Only the requester can cancel this request.
  Traces     : REQ-PTY-096
  Data source: ENT-PTY-005.createdBy
  Source     : ADR-PTY-016

### RULE-PTY-030 — Credit change or overrun decision recorded or not saved
  Scope      : ENT-PTY-002, ENT-PTY-005
  Trigger    : on update of creditLimit or paymentTermDays · on approve · on reject
  Statement  : The system shall prevent saving a creditLimit or paymentTermDays change, or the approval or rejection of a Credit override request, that the core audit module has not recorded; the change or the decision is rolled back.
  Message    : ar: تعذّر تسجيل التغيير في سجل التدقيق، لذا لم يُحفظ. أعد المحاولة. · en: The change could not be recorded in the audit history, so it was not saved. Try again.
  Traces     : REQ-PTY-109, REQ-PTY-106, REQ-PTY-107
  Data source: ENT-PTY-002.creditLimit, ENT-PTY-002.paymentTermDays, ENT-PTY-005.statusCode
  Source     : POL-PTY-003, POL-PTY-008; module-registry AUTO (audit), as cited by the PRD

### RULE-PTY-031 — Request number system-assigned and read-only
  Scope      : ENT-PTY-005
  Trigger    : on create · on update
  Statement  : The system shall assign docNo from the core number series on first save and prevent every later change to it.
  Message    : ar: رقم الطلب يُنشأ تلقائياً ولا يمكن تعديله. · en: The request number is assigned by the system and cannot be changed.
  Traces     : REQ-PTY-092
  Data source: ENT-PTY-005.docNo
  Source     : profile NUMBERING

### RULE-PTY-032 — Sale amount positive
  Scope      : ENT-PTY-005
  Trigger    : on create
  Statement  : The system shall require saleAmount to be greater than 0.
  Message    : ar: مبلغ البيع يجب أن يكون أكبر من صفر. · en: The sale amount must be greater than zero.
  Traces     : REQ-PTY-091
  Data source: ENT-PTY-005.saleAmount
  Source     : ADR-PTY-016

### RULE-PTY-033 — No credit override for the walk-in Customer
  Scope      : ENT-PTY-005
  Trigger    : on create
  Statement  : The system shall prevent creating a Credit override request whose Customer has isWalkInFl true.
  Message    : ar: لا يمكن البيع الآجل للعميل النقدي. سجّل العميل باسمه أولاً. · en: The walk-in customer cannot buy on credit. Register the customer by name first.
  Traces     : REQ-PTY-105
  Data source: ENT-PTY-002.isWalkInFl, ENT-PTY-005.customerFk
  Source     : POL-PTY-009, POL-PTY-008

### RULE-PTY-034 — One open Credit override request per sale
  Scope      : ENT-PTY-005
  Trigger    : on create
  Statement  : The system shall prevent a second Credit override request with statusCode PENDING or APPROVED for the same Customer and the same sale document reference.
  Message    : ar: لهذا البيع طلب موافقة مفتوح بالفعل ({docNo}). تابع ذلك الطلب بدلاً من إنشاء طلب جديد. · en: This sale already has an open approval request ({docNo}). Follow that request instead of creating a new one.
  Traces     : REQ-PTY-113
  Data source: ENT-PTY-005.customerFk, ENT-PTY-005.sourceDocumentRef, ENT-PTY-005.statusCode, ENT-PTY-005.docNo
  Source     : ADR-PTY-042
  Test-Hint  : {docNo} = the open request's number; a REJECTED, CANCELLED or USED request does not block a new one

## A6 — Lookups

```yaml name=lookups
lookups:
  - {key: CUSTOMER_GROUP, seeded: [RETAIL, WHOLESALE], open: true, values: [RETAIL, WHOLESALE, VIP], fields: [customerGroupCode], owner: PTY, entity: ENT-PTY-002, control: lookup, source: "module-registry LOOKUPS OWNED (customer-group); US-PTY-006; D-04; VIP is host data named by AC-PTY-046"}
  - {key: ADDRESS_TYPE, seeded: [BILLING, DELIVERY], open: true, values: [BILLING, DELIVERY, OFFICE], fields: [addressTypeCode], owner: PTY, entity: ENT-PTY-004, control: lookup, source: "module-registry LOOKUPS OWNED (address-type); US-PTY-005; D-04; OFFICE is a value AC-PTY-044 names as absent"}
  - {key: CREDIT_OVERRUN_ACTION, seeded: [WARN, BLOCK_WITH_OVERRIDE], open: false, values: [WARN, BLOCK_WITH_OVERRIDE], owner: PTY, control: lookup, source: "POL-PTY-008; business-policies decision 7 — the tenant setting is held in given cu (A9)"}
  - {key: CREDIT_CHECK_RESULT, seeded: [WITHIN_LIMIT, OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE, NOT_APPLICABLE], open: false, values: [WITHIN_LIMIT, OVER_LIMIT, NO_CREDIT, EXPOSURE_UNAVAILABLE, NOT_APPLICABLE], fields: [creditCheckResultCode], owner: PTY, entity: ENT-PTY-005, control: lookup, source: "ADR-PTY-017"}
  - {key: CREDIT_OVERRIDE_STATUS, seeded: [PENDING, APPROVED, REJECTED, CANCELLED, USED], open: false, values: [PENDING, APPROVED, REJECTED, CANCELLED, USED], fields: [statusCode], owner: PTY, entity: ENT-PTY-005, control: lookup, source: "ADR-PTY-016; A7"}
```

Host values of the open keys (CUSTOMER_GROUP, ADDRESS_TYPE) are maintained by tenant users through the given mdl HTTP surface. The key's type is found by `POST /api/v1/mdl/lookup-types/search`, a value is added by `POST /api/v1/mdl/lookup-types/{id}/values` and deactivated by `DELETE /api/v1/mdl/lookup-values/{id}`. PTY adds no value itself. `values` lists every value the analysis names: VIP (AC-PTY-046, inactive host data) and OFFICE (AC-PTY-044, a value that is never created) are not seeded.

Labels and rationale (all values are loaded at runtime from given `mdl`; the registry's kebab-case names `customer-group` and `address-type` are carried as the keys CUSTOMER_GROUP and ADDRESS_TYPE required by the key grammar):

| Key | Value | Label-ar | Label-en | Rationale |
|---|---|---|---|---|
| CUSTOMER_GROUP | RETAIL | تجزئة | Retail | PRD example; default for new Customers (REQ-PTY-007). Open: the Owner adds values as data |
| CUSTOMER_GROUP | WHOLESALE | جملة | Wholesale | PRD example; the `b2bCredit` audience |
| ADDRESS_TYPE | BILLING | فوترة | Billing | PRD example. Open |
| ADDRESS_TYPE | DELIVERY | توصيل | Delivery | PRD example; storefront guest address type (REQ-PTY-050) |
| CREDIT_OVERRUN_ACTION | WARN | تنبيه فقط | Warn only | POL-PTY-008. Closed: behaviour is coded per value |
| CREDIT_OVERRUN_ACTION | BLOCK_WITH_OVERRIDE | منع مع إمكانية الموافقة | Block with approval | POL-PTY-008 default (REQ-PTY-087) |
| CREDIT_CHECK_RESULT | WITHIN_LIMIT | ضمن الحد | Within limit | REQ-PTY-081. Closed |
| CREDIT_CHECK_RESULT | OVER_LIMIT | تجاوز الحد | Over limit | REQ-PTY-082 |
| CREDIT_CHECK_RESULT | NO_CREDIT | لا ائتمان | No credit | REQ-PTY-083 |
| CREDIT_CHECK_RESULT | EXPOSURE_UNAVAILABLE | المديونية غير متاحة | Exposure unavailable | REQ-PTY-084 |
| CREDIT_CHECK_RESULT | NOT_APPLICABLE | غير مطبّق | Not applicable | REQ-PTY-074 |
| CREDIT_OVERRIDE_STATUS | PENDING | معلّق | Pending | A7. Closed |
| CREDIT_OVERRIDE_STATUS | APPROVED | موافَق عليه | Approved | A7 |
| CREDIT_OVERRIDE_STATUS | REJECTED | مرفوض | Rejected | A7 |
| CREDIT_OVERRIDE_STATUS | CANCELLED | ملغى | Cancelled | A7 |
| CREDIT_OVERRIDE_STATUS | USED | مستخدَم | Used | A7 |

Consumed lookups: none.

## A7 — Status lifecycle

ENT-PTY-001 … ENT-PTY-004: active / inactive only (2 states) — not applicable.

ENT-PTY-005 — Credit override request (`statusCode`, lookup CREDIT_OVERRIDE_STATUS):

```
            approve (RULE-PTY-026)            use (RULE-PTY-028)
 PENDING ───────────────────────► APPROVED ─────────────────────► USED
    │
    ├── reject (RULE-PTY-026, RULE-PTY-027) ──► REJECTED
    │
    └── cancel (RULE-PTY-026, RULE-PTY-029) ──► CANCELLED

 PENDING, APPROVED ── system, on deactivation of the Customer (REQ-PTY-111) ──► CANCELLED
```

Initial state: PENDING (REQ-PTY-091). Final states: USED, REJECTED, CANCELLED. Approve, reject and use are refused while `b2bCredit` is off; cancel is not (RULE-PTY-023, REQ-PTY-112). At most one PENDING or APPROVED request exists per Customer and sale document reference (RULE-PTY-034). Module-specific approval flow: single step, traced to US-PTY-015 and US-PTY-016 (ADR-PTY-016); no workflow engine.

## A8 — Module dependencies

```yaml name=module-dependencies
consumes: []
```

Party is DAG #4 with no business-module dependency (KB §5); it reaches only the given `erp-core` (A9), which creates no XM edge. Modules below Party in the DAG consume ENT-PTY-001 … ENT-PTY-005 (A3 Ownership).

| External service | Purpose | Integration kind |
|---|---|---|
| — | none | — |

### A8.1 — Operations offered to consuming modules (stack-neutral; P3.1 designs them)

| Operation | Verb | Path (base /api/v1/pty) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read Customer | GET | /customers/{id} | id | Customer (credit fields only while `b2bCredit` is on) | RULE-PTY-005 | REQ-PTY-002, REQ-PTY-003, REQ-PTY-071 |
| read Supplier | GET | /suppliers/{id} | id | Supplier | RULE-PTY-005 | REQ-PTY-019, REQ-PTY-003 |
| read Party addresses | GET | /parties/{partyId}/addresses | partyId | list of Address | — | REQ-PTY-032 |
| default Customer for a POS sale | GET | /customers/walk-in | — | walk-in Customer | RULE-PTY-014 | REQ-PTY-045, REQ-PTY-046 |
| guest order details | POST | /guest-customers | names, email, mobile, delivery address | Customer id | RULE-PTY-019, RULE-PTY-021, RULE-PTY-013 | REQ-PTY-050, REQ-PTY-053, REQ-PTY-055 … REQ-PTY-063 |
| credit check | POST | /credit-checks | customerId, saleAmount | creditLimit, open exposure, projected exposure, currencyCode, result, credit-overrun action, approval-required | RULE-PTY-005 | REQ-PTY-074, REQ-PTY-079 … REQ-PTY-084, REQ-PTY-088, REQ-PTY-089, REQ-PTY-110 |
| create Credit override request | POST | /credit-overrides | customerId, saleAmount, sourceDocumentRef | Credit override request | RULE-PTY-005, RULE-PTY-023, RULE-PTY-025, RULE-PTY-032, RULE-PTY-033, RULE-PTY-034 | REQ-PTY-091, REQ-PTY-072, REQ-PTY-104, REQ-PTY-105, REQ-PTY-110, REQ-PTY-113 |
| read Credit override request | GET | /credit-overrides/{id} | id | Credit override request | — | REQ-PTY-103 |
| use Credit override request | PATCH | /credit-overrides/{id}/use | id, customerId, saleAmount, usedDocumentRef (= sourceDocumentRef) | Credit override request | RULE-PTY-005, RULE-PTY-023, RULE-PTY-028 | REQ-PTY-097, REQ-PTY-099, REQ-PTY-110, REQ-PTY-112 |
| `b2bCredit` switch-off dependency | GET | /flag-dependencies/b2bCredit | — | count of Customers carrying credit | RULE-PTY-024 | REQ-PTY-075, REQ-PTY-076 |
| customer global search | GET | /customers/global-search | term | at most 10 Customers | — | REQ-PTY-041 … REQ-PTY-043 |

Extension point declared by this module (module-registry EXTENSION POINT "Customer credit exposure", as cited by the PRD): a downstream module registers as a credit exposure contributor and reports the open exposure of a Customer (REQ-PTY-080, REQ-PTY-084). Domain event published to consuming modules: `pty.PartyRoleDeactivated` on the given outbox, consumed by idempotent outbox subscribers (REQ-PTY-023, ADR-PTY-038).

## A9 — Given usage

```yaml name=given-usage
rows:
  - {given: erp-core, module: modules.sec, via: com.erp.sec.crossmodule.SecCustomerInvitationApi, purpose: "Issue the CUSTOMER-realm invitation for a Customer created from or matched to a guest order, with PTY's template, from PTY's after-commit listener (REQ-PTY-051, REQ-PTY-059)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.crossmodule.SecUserDirectoryApi, purpose: "Resolve the CUSTOMER-realm user linked to a Customer and the users holding UPDATE on PTY_CREDIT_OVERRIDE; show user names in credit history and request decisions (REQ-PTY-093, REQ-PTY-108)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.permission.PermissionContributor, purpose: "PtyPermissions declares permission module PTY with the five PTY screens (SEC_PAGES) and their VIEW / CREATE / UPDATE / DELETE actions; every service method checks its constant (B4, ADR-PTY-039)"}
  - {given: erp-core, module: modules.cu, via: com.erp.cu.crossmodule.SettingsApi, purpose: "Read capability flag b2bCredit through the FeatureFlags API, read / save the tenant's credit-overrun setting and read the tenant base currency PTY_BASE_CURRENCY_CODE (REQ-PTY-070 … REQ-PTY-074, REQ-PTY-086, REQ-PTY-087; ADR-PTY-037)"}
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "Load CUSTOMER_GROUP, ADDRESS_TYPE, CREDIT_OVERRUN_ACTION, CREDIT_CHECK_RESULT and CREDIT_OVERRIDE_STATUS values at runtime (A6)"}
  - {given: erp-core, module: modules.sequence, via: com.erp.sequence.crossmodule.NumberSeriesApi, purpose: "Assign the Party code and the Credit override request docNo from the series PTY_PARTY and PTY_CREDIT_OVERRIDE seeded by the PTY migration (REQ-PTY-006, REQ-PTY-092)"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantProvisioningContributor, purpose: "Create the tenant's walk-in Customer when a tenant is provisioned (REQ-PTY-044)"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantRunner, purpose: "Re-bind the tenant of PTY's after-commit work through the given's published tenant runner on the given event executor (REQ-PTY-051, REQ-PTY-059, REQ-PTY-093; ADR-PTY-044)"}
  - {given: erp-core, module: modules.audit, via: com.erp.audit.crossmodule.AuditApi, purpose: "Record credit-limit and payment-term changes through the @Audited listener of Customer, record approve / reject decisions as business actions, and read the credit history back (REQ-PTY-106 … REQ-PTY-109)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxPublisher, purpose: "Publish pty.PartyRoleDeactivated to consuming modules inside the deactivation transaction (REQ-PTY-023, ADR-PTY-038)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxSubscriber, purpose: "Consume sec.CustomerInvitationRedeemed, idempotent on OutboxMessage.id(), to set secUserId (REQ-PTY-052, REQ-PTY-054)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.DomainEventPublisher, purpose: "Publish PTY's own in-process events that only PTY's after-commit listeners consume — invitation issue and approver notification — never an event for another module (REQ-PTY-051, REQ-PTY-059, REQ-PTY-093)"}
  - {given: erp-core, module: modules.notif, via: com.erp.notif.crossmodule.NotificationDispatchApi, purpose: "Dispatch template PTY_CREDIT_OVERRIDE_REQUESTED, seeded by the PTY migration, to each approver of a new Credit override request (REQ-PTY-093, ADR-PTY-036)"}
  - {given: erp-core, module: modules.module, via: docs/api-docs/module/index.md, purpose: "Declare PTY to the given module catalogue with the module manifest META-INF/erp/modules/pty.yaml in the format the given's module api documents: code, package, permission module, api-paths, settings, events and migration (ADR-PTY-044)"}
```

No ENT re-creates a given entity: CUSTOMER-realm users stay in given `sec` (Customer holds only `secUserId`), the credit-overrun setting stays in given `cu`, lookup values stay in given `mdl`, number series in given `sequence`, audit events in given `audit`.

The platform columns of A3 (tenantId, version and the audit fields) follow the given's entity base class in `erp-core:modules.common`. That module publishes no extension point, interface or api, so the base class is a build dependency of PTY's code, not a use through a published seam, and it has no `given-usage` row (ADR-PTY-044).

# PART B — SCREEN REQUIREMENTS

## SCR-REQ-PTY-001 — العملاء / Customers

### B1 — Definition
  Purpose      : Find, register and maintain Customers and their addresses; add the Supplier role in place.
  Entities     : ENT-PTY-001, ENT-PTY-002, ENT-PTY-004
  Operations   : search · list · create · read · update · deactivate · reactivate · add Supplier role · address create / update / deactivate / set default
  Users        : Owner, Seller/Cashier
  Navigation   : Party → Customers → Customers; from: home, global search result, a sale (registration in place); to: Customer credit (SCR-REQ-PTY-003, flag on), Suppliers (SCR-REQ-PTY-002, after "Make supplier")
  Content shape: flat record with a repeating Address section
  Traces       : REQ-PTY-001, REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-024, REQ-PTY-025, REQ-PTY-027, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-039, REQ-PTY-040, REQ-PTY-047, REQ-PTY-049, REQ-PTY-070, REQ-PTY-073, REQ-PTY-110
  Composite    : Search + Entry = one screen requirement; the entry part is also opened in place from a sale (REQ-PTY-008)

### B2 — Search / list
- Filters = result columns: ENT-PTY-001.code, ENT-PTY-001.nameAr / nameEn, ENT-PTY-001.mobile, ENT-PTY-001.email, ENT-PTY-001.taxRegistrationNo, ENT-PTY-002.customerGroupCode (lookup CUSTOMER_GROUP), ENT-PTY-002.isActiveFl.
- Behaviour: REQ-PTY-013. The walk-in Customer is listed and marked by ENT-PTY-002.isWalkInFl.

### B3 — Input
- Mandatory fields outside Advanced: 2 — ENT-PTY-001.nameAr or ENT-PTY-001.nameEn (one of them, RULE-PTY-001); ENT-PTY-002.customerGroupCode (prefilled RETAIL, RULE-PTY-018). Optional main fields: ENT-PTY-001.mobile (RULE-PTY-004), ENT-PTY-001.email (RULE-PTY-002, RULE-PTY-003). Read-only: ENT-PTY-001.code (RULE-PTY-007).
- Advanced: ENT-PTY-001.taxRegistrationNo.
- Address section (repeating; mandatory 2): ENT-PTY-004.addressTypeCode (lookup ADDRESS_TYPE, RULE-PTY-016), ENT-PTY-004.line1 (RULE-PTY-015); optional ENT-PTY-004.city, ENT-PTY-004.nameAr / nameEn; Advanced ENT-PTY-004.line2, ENT-PTY-004.region, ENT-PTY-004.postalCode; default mark ENT-PTY-004.isDefaultFl (RULE-PTY-017).
- Credit fields are not on this screen; they belong to SCR-REQ-PTY-003 (REQ-PTY-070).
- Actions: Save → create (REQ-PTY-004) / update (REQ-PTY-012) · Deactivate → REQ-PTY-014 (RULE-PTY-006, RULE-PTY-010) · Reactivate → REQ-PTY-015 (RULE-PTY-002) · "Make supplier" / «إضافة كمورّد» → REQ-PTY-025 (RULE-PTY-009, RULE-PTY-012) · Address save / deactivate / set default → REQ-PTY-030, REQ-PTY-031, REQ-PTY-033, REQ-PTY-035 (RULE-PTY-017).

### B4 — Access
| Page code | Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|---|
| PTY_CUSTOMER | Owner | ✓ | ✓ | ✓ | ✓ |
| PTY_CUSTOMER | Seller/Cashier | ✓ | ✓ | ✓ | — |
| PTY_CUSTOMER | Warehouse | — | — | — | — |

Address create, update and deactivation require UPDATE on this page. "Make supplier" also requires CREATE on PTY_SUPPLIER. VIEW on this page also gates Customers in the global search (REQ-PTY-043).

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create Customer | POST | /api/v1/pty/customers | nameAr, nameEn, mobile, email, taxRegistrationNo, customerGroupCode, partyId (to add the role to an existing Party) | Customer | RULE-PTY-001, RULE-PTY-002, RULE-PTY-003, RULE-PTY-004, RULE-PTY-009, RULE-PTY-018 | REQ-PTY-004, REQ-PTY-026 |
| search Customers | GET | /api/v1/pty/customers | filters of B2, paging | Page of Customer | — | REQ-PTY-013 |
| read Customer | GET | /api/v1/pty/customers/{id} | id | Customer | — | REQ-PTY-002 |
| update Customer | PUT | /api/v1/pty/customers/{id} | fields of B3 | Customer | RULE-PTY-001, RULE-PTY-002, RULE-PTY-003, RULE-PTY-004, RULE-PTY-007, RULE-PTY-018 | REQ-PTY-012 |
| deactivate Customer | DELETE | /api/v1/pty/customers/{id} | id | confirmation | RULE-PTY-006, RULE-PTY-010 | REQ-PTY-014 |
| reactivate Customer | PATCH | /api/v1/pty/customers/{id}/activation | id | Customer | RULE-PTY-002 | REQ-PTY-015 |
| add Supplier role | POST | /api/v1/pty/suppliers | partyId | Supplier | RULE-PTY-009, RULE-PTY-012 | REQ-PTY-025 |
| create Address | POST | /api/v1/pty/parties/{partyId}/addresses | fields of the Address section | Address | RULE-PTY-005, RULE-PTY-015, RULE-PTY-016, RULE-PTY-017 | REQ-PTY-030, REQ-PTY-034, REQ-PTY-110 |
| list Addresses | GET | /api/v1/pty/parties/{partyId}/addresses | partyId | list of Address | — | REQ-PTY-032 |
| update Address | PUT | /api/v1/pty/parties/{partyId}/addresses/{id} | fields | Address | RULE-PTY-005, RULE-PTY-015, RULE-PTY-016 | REQ-PTY-031, REQ-PTY-110 |
| deactivate Address | DELETE | /api/v1/pty/parties/{partyId}/addresses/{id} | id | confirmation | RULE-PTY-006, RULE-PTY-017 | REQ-PTY-033, REQ-PTY-036 |
| set default Address | PATCH | /api/v1/pty/parties/{partyId}/addresses/{id}/default | id | Address | RULE-PTY-005, RULE-PTY-017 | REQ-PTY-035, REQ-PTY-110 |

## SCR-REQ-PTY-002 — الموردون / Suppliers

### B1 — Definition
  Purpose      : Find, register and maintain Suppliers and their addresses; add the Customer role in place.
  Entities     : ENT-PTY-001, ENT-PTY-003, ENT-PTY-004
  Operations   : search · list · create · read · update · deactivate · reactivate · add Customer role · address create / update / deactivate / set default
  Users        : Owner, Warehouse (read)
  Navigation   : Party → Suppliers → Suppliers; from: home, a purchase document (registration in place), Customers ("Make supplier"); to: Customers (SCR-REQ-PTY-001, after "Make customer")
  Content shape: flat record with a repeating Address section
  Traces       : REQ-PTY-006, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-024, REQ-PTY-026, REQ-PTY-027, REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-110
  Composite    : Search + Entry = one screen requirement; the entry part is also opened in place from a purchase document (REQ-PTY-008)

### B2 — Search / list
- Filters = result columns: ENT-PTY-001.code, ENT-PTY-001.nameAr / nameEn, ENT-PTY-001.mobile, ENT-PTY-001.email, ENT-PTY-001.taxRegistrationNo, ENT-PTY-003.isActiveFl.
- Behaviour: REQ-PTY-018. The email filter is also how the RULE-PTY-002 refusal reaches a Supplier-only Party (ADR-PTY-043).

### B3 — Input
- Mandatory fields outside Advanced: 1 — ENT-PTY-001.nameAr or ENT-PTY-001.nameEn (RULE-PTY-001). Optional main fields: ENT-PTY-001.mobile (RULE-PTY-004), ENT-PTY-001.email (RULE-PTY-002, RULE-PTY-003), ENT-PTY-003.paymentTermDays (RULE-PTY-008; shown whatever the state of `b2bCredit`, ADR-PTY-012). Read-only: ENT-PTY-001.code (RULE-PTY-007).
- Advanced: ENT-PTY-001.taxRegistrationNo.
- Address section: as SCR-REQ-PTY-001 B3 (same fields, same RULE ids).
- Actions: Save → create (REQ-PTY-016) / update (REQ-PTY-017) · Deactivate → REQ-PTY-020 (RULE-PTY-006) · Reactivate → REQ-PTY-021 (RULE-PTY-002) · "Make customer" / «إضافة كعميل» → REQ-PTY-026 (RULE-PTY-009) · Address actions → REQ-PTY-030, REQ-PTY-031, REQ-PTY-033, REQ-PTY-035.

### B4 — Access
| Page code | Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|---|
| PTY_SUPPLIER | Owner | ✓ | ✓ | ✓ | ✓ |
| PTY_SUPPLIER | Seller/Cashier | — | — | — | — |
| PTY_SUPPLIER | Warehouse | ✓ | — | — | — |

"Make customer" also requires CREATE on PTY_CUSTOMER.

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create Supplier | POST | /api/v1/pty/suppliers | nameAr, nameEn, mobile, email, taxRegistrationNo, paymentTermDays, partyId (to add the role to an existing Party) | Supplier | RULE-PTY-001, RULE-PTY-002, RULE-PTY-003, RULE-PTY-004, RULE-PTY-008, RULE-PTY-009, RULE-PTY-012 | REQ-PTY-016, REQ-PTY-025 |
| search Suppliers | GET | /api/v1/pty/suppliers | filters of B2, paging | Page of Supplier | — | REQ-PTY-018 |
| read Supplier | GET | /api/v1/pty/suppliers/{id} | id | Supplier | — | REQ-PTY-019 |
| update Supplier | PUT | /api/v1/pty/suppliers/{id} | fields of B3 | Supplier | RULE-PTY-001, RULE-PTY-002, RULE-PTY-003, RULE-PTY-004, RULE-PTY-007, RULE-PTY-008 | REQ-PTY-017, REQ-PTY-022 |
| deactivate Supplier | DELETE | /api/v1/pty/suppliers/{id} | id | confirmation | RULE-PTY-006 | REQ-PTY-020 |
| reactivate Supplier | PATCH | /api/v1/pty/suppliers/{id}/activation | id | Supplier | RULE-PTY-002 | REQ-PTY-021 |
| add Customer role | POST | /api/v1/pty/customers | partyId | Customer | RULE-PTY-009 | REQ-PTY-026 |
| Address operations | — | as SCR-REQ-PTY-001 B5 | — | — | as SCR-REQ-PTY-001 B5 | REQ-PTY-030 … REQ-PTY-038 |

## SCR-REQ-PTY-003 — ائتمان العملاء / Customer credit

### B1 — Definition
  Purpose      : Set and review each Customer's credit limit and payment terms, see the credit standing, clear credit, read the credit history. Shown only while `b2bCredit` is on.
  Entities     : ENT-PTY-001, ENT-PTY-002
  Operations   : search · list · read (credit standing) · update (credit limit, payment terms) · clear credit · read credit history
  Users        : Owner, Seller/Cashier (read)
  Navigation   : Party → Credit → Customer credit; from: Customers (SCR-REQ-PTY-001), the `b2bCredit` switch-off refusal (REQ-PTY-077); to: Credit override requests (SCR-REQ-PTY-004)
  Content shape: flat record list with a per-Customer history panel
  Traces       : REQ-PTY-048, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-069, REQ-PTY-070, REQ-PTY-072, REQ-PTY-073, REQ-PTY-077, REQ-PTY-078, REQ-PTY-085, REQ-PTY-106, REQ-PTY-108, REQ-PTY-109, REQ-PTY-110
  Composite    : Search + Entry = one screen requirement

### B2 — Search / list
- Filters = result columns: ENT-PTY-001.code, ENT-PTY-001.nameAr / nameEn, ENT-PTY-002.customerGroupCode (lookup CUSTOMER_GROUP), carries-credit (ENT-PTY-002.creditLimit or ENT-PTY-002.paymentTermDays non-empty); result columns add ENT-PTY-002.creditLimit and ENT-PTY-002.paymentTermDays.
- Behaviour: REQ-PTY-069; opened pre-filtered by REQ-PTY-077.

### B3 — Input
- Mandatory fields outside Advanced: 0. Editable: ENT-PTY-002.creditLimit (RULE-PTY-022, RULE-PTY-011, RULE-PTY-023, RULE-PTY-030), ENT-PTY-002.paymentTermDays (RULE-PTY-008, RULE-PTY-011, RULE-PTY-023, RULE-PTY-030).
- Read-only: open exposure and available credit (REQ-PTY-085); credit history (REQ-PTY-108).
- Actions: Save → REQ-PTY-064, REQ-PTY-065 · "Clear credit" / «مسح الائتمان» → REQ-PTY-078 · "Credit history" / «سجل الائتمان» → REQ-PTY-108.

### B4 — Access
| Page code | Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|---|
| PTY_CUSTOMER_CREDIT | Owner | ✓ | — | ✓ | — |
| PTY_CUSTOMER_CREDIT | Seller/Cashier | ✓ | — | — | — |
| PTY_CUSTOMER_CREDIT | Warehouse | — | — | — | — |

The page is absent from every menu while `b2bCredit` is off (REQ-PTY-070).

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search Customer credit | GET | /api/v1/pty/customer-credits | filters of B2, paging | Page of Customer credit rows | RULE-PTY-023 | REQ-PTY-069 |
| read credit standing | GET | /api/v1/pty/customer-credits/{customerId} | customerId | creditLimit, paymentTermDays, open exposure, available credit | RULE-PTY-023 | REQ-PTY-085 |
| update credit | PUT | /api/v1/pty/customer-credits/{customerId} | creditLimit, paymentTermDays | Customer credit row | RULE-PTY-005, RULE-PTY-008, RULE-PTY-011, RULE-PTY-022, RULE-PTY-023, RULE-PTY-030 | REQ-PTY-064, REQ-PTY-065, REQ-PTY-106, REQ-PTY-110 |
| clear credit | DELETE | /api/v1/pty/customer-credits/{customerId} | customerId | confirmation | RULE-PTY-030 | REQ-PTY-078 |
| read credit history | GET | /api/v1/pty/customer-credits/{customerId}/history | customerId, paging | Page of audit entries | — | REQ-PTY-108 |

## SCR-REQ-PTY-004 — طلبات تجاوز حد الائتمان / Credit override requests

### B1 — Definition
  Purpose      : Raise, follow, approve, reject and cancel Credit override requests. Shown only while `b2bCredit` is on.
  Entities     : ENT-PTY-005, ENT-PTY-002
  Operations   : search · list · create (from a sale) · read · approve · reject · cancel
  Users        : Owner (approver), Seller/Cashier (requester)
  Navigation   : Party → Credit → Credit override requests; from: a sale whose credit check requires approval, the approver's in-app notification (REQ-PTY-093), Customer credit; to: Customer credit (SCR-REQ-PTY-003)
  Content shape: flat record list with a decision panel
  Traces       : REQ-PTY-070, REQ-PTY-072, REQ-PTY-089, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-098, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-107, REQ-PTY-109, REQ-PTY-110, REQ-PTY-112, REQ-PTY-113
  Composite    : Search + Entry = one screen requirement

### B2 — Search / list
- Filters = result columns: ENT-PTY-005.docNo, ENT-PTY-005.statusCode (lookup CREDIT_OVERRIDE_STATUS), ENT-PTY-005.customerFk, ENT-PTY-005.docDate (range); result columns add ENT-PTY-005.saleAmount, ENT-PTY-005.creditLimitAtRequest, ENT-PTY-005.openExposure, ENT-PTY-005.creditCheckResultCode, ENT-PTY-005.createdBy.
- Behaviour: REQ-PTY-101, REQ-PTY-102.

### B3 — Input
- Create (prefilled by the sale, mandatory 3): ENT-PTY-005.customerFk (RULE-PTY-033), ENT-PTY-005.saleAmount (RULE-PTY-032, RULE-PTY-025), ENT-PTY-005.sourceDocumentRef. System-filled: docNo (RULE-PTY-031), docDate, statusCode, creditLimitAtRequest, openExposure, creditCheckResultCode.
- Decision panel: ENT-PTY-005.decisionReason (mandatory on reject, RULE-PTY-027).
- Actions: "Request approval" / «طلب موافقة» → REQ-PTY-091 (RULE-PTY-023, RULE-PTY-025, RULE-PTY-032, RULE-PTY-033) · "Approve" / «موافقة» → REQ-PTY-094 (RULE-PTY-026) · "Reject" / «رفض» → REQ-PTY-095 (RULE-PTY-026, RULE-PTY-027) · "Cancel request" / «إلغاء الطلب» → REQ-PTY-096 (RULE-PTY-026, RULE-PTY-029).

### B4 — Access
| Page code | Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|---|
| PTY_CREDIT_OVERRIDE | Owner | ✓ | ✓ | ✓ | ✓ |
| PTY_CREDIT_OVERRIDE | Seller/Cashier | ✓ | ✓ | — | ✓ |
| PTY_CREDIT_OVERRIDE | Warehouse | — | — | — | — |

UPDATE on PTY_CREDIT_OVERRIDE is the credit-override permission named by POL-PTY-008 (approve / reject); DELETE is the cancel action, limited to the requester by RULE-PTY-029 (D-06).

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create Credit override request | POST | /api/v1/pty/credit-overrides | customerId, saleAmount, sourceDocumentRef | Credit override request | RULE-PTY-005, RULE-PTY-023, RULE-PTY-025, RULE-PTY-031, RULE-PTY-032, RULE-PTY-033, RULE-PTY-034 | REQ-PTY-091, REQ-PTY-092, REQ-PTY-110, REQ-PTY-113 |
| search Credit override requests | GET | /api/v1/pty/credit-overrides | filters of B2, paging | Page of Credit override request | — | REQ-PTY-101, REQ-PTY-102 |
| read Credit override request | GET | /api/v1/pty/credit-overrides/{id} | id | Credit override request | — | REQ-PTY-103 |
| approve | PATCH | /api/v1/pty/credit-overrides/{id}/approval | id | Credit override request | RULE-PTY-023, RULE-PTY-026, RULE-PTY-030 | REQ-PTY-094, REQ-PTY-107, REQ-PTY-109, REQ-PTY-112 |
| reject | PATCH | /api/v1/pty/credit-overrides/{id}/rejection | id, decisionReason | Credit override request | RULE-PTY-023, RULE-PTY-026, RULE-PTY-027, RULE-PTY-030 | REQ-PTY-095, REQ-PTY-107, REQ-PTY-109, REQ-PTY-112 |
| cancel | DELETE | /api/v1/pty/credit-overrides/{id} | id | confirmation | RULE-PTY-026, RULE-PTY-029 | REQ-PTY-096 |

## SCR-REQ-PTY-005 — إعدادات ضبط الائتمان / Credit control settings

### B1 — Definition
  Purpose      : Choose whether a sale above a Customer's credit limit only warns or waits for approval. Shown only while `b2bCredit` is on.
  Entities     : ENT-PTY-002 (the setting governs its credit checks; the value itself is a tenant setting in given `cu`)
  Operations   : read · update
  Users        : Owner
  Navigation   : Party → Credit → Credit control settings; from: Customer credit; to: —
  Content shape: flat record (one setting)
  Traces       : REQ-PTY-070, REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090
  Composite    : single entry screen

### B2 — Search / list
Not applicable — one setting per tenant.

### B3 — Input
- Mandatory fields outside Advanced: 1 — credit-overrun action (lookup CREDIT_OVERRUN_ACTION; prefilled BLOCK_WITH_OVERRIDE, REQ-PTY-087).
- Actions: Save → REQ-PTY-086.

### B4 — Access
| Page code | Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|---|
| PTY_CREDIT_SETTINGS | Owner | ✓ | — | ✓ | — |
| PTY_CREDIT_SETTINGS | Seller/Cashier | — | — | — | — |
| PTY_CREDIT_SETTINGS | Warehouse | — | — | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read credit settings | GET | /api/v1/pty/credit-settings | — | credit-overrun action | — | REQ-PTY-087 |
| update credit settings | PUT | /api/v1/pty/credit-settings | credit-overrun action | credit-overrun action | — | REQ-PTY-086, REQ-PTY-090 |

# STANDALONE

## Traceability matrix

| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-PTY-001 | REQ-PTY-001, REQ-PTY-002, REQ-PTY-003, REQ-PTY-004, REQ-PTY-010, REQ-PTY-012, REQ-PTY-013, REQ-PTY-014, REQ-PTY-015, REQ-PTY-018, REQ-PTY-023, REQ-PTY-111 | AC-PTY-001, AC-PTY-002, AC-PTY-003, AC-PTY-004, AC-PTY-005, AC-PTY-006, AC-PTY-013, AC-PTY-016, AC-PTY-017, AC-PTY-018, AC-PTY-019, AC-PTY-020, AC-PTY-028, AC-PTY-128, AC-PTY-129, AC-PTY-136 | RULE-PTY-001, RULE-PTY-002, RULE-PTY-005, RULE-PTY-006, RULE-PTY-007, RULE-PTY-010 | ENT-PTY-001, ENT-PTY-002, ENT-PTY-003 | SCR-REQ-PTY-001, SCR-REQ-PTY-002 |
| US-PTY-002 | REQ-PTY-004, REQ-PTY-005, REQ-PTY-006, REQ-PTY-007, REQ-PTY-008, REQ-PTY-009, REQ-PTY-010, REQ-PTY-011, REQ-PTY-012 | AC-PTY-006, AC-PTY-007, AC-PTY-008, AC-PTY-009, AC-PTY-010, AC-PTY-011, AC-PTY-012, AC-PTY-013, AC-PTY-014, AC-PTY-015, AC-PTY-016, AC-PTY-017 | RULE-PTY-001, RULE-PTY-002, RULE-PTY-003, RULE-PTY-004, RULE-PTY-007 | ENT-PTY-001, ENT-PTY-002, ENT-PTY-003 | SCR-REQ-PTY-001, SCR-REQ-PTY-002 |
| US-PTY-003 | REQ-PTY-003, REQ-PTY-006, REQ-PTY-008, REQ-PTY-009, REQ-PTY-011, REQ-PTY-016, REQ-PTY-017, REQ-PTY-018, REQ-PTY-019, REQ-PTY-020, REQ-PTY-021, REQ-PTY-022, REQ-PTY-023 | AC-PTY-004, AC-PTY-005, AC-PTY-008, AC-PTY-010, AC-PTY-011, AC-PTY-012, AC-PTY-014, AC-PTY-015, AC-PTY-021, AC-PTY-022, AC-PTY-023, AC-PTY-024, AC-PTY-025, AC-PTY-026, AC-PTY-027, AC-PTY-028, AC-PTY-136 | RULE-PTY-001, RULE-PTY-002, RULE-PTY-003, RULE-PTY-004, RULE-PTY-005, RULE-PTY-006, RULE-PTY-007, RULE-PTY-008 | ENT-PTY-001, ENT-PTY-002, ENT-PTY-003 | SCR-REQ-PTY-001, SCR-REQ-PTY-002 |
| US-PTY-004 | REQ-PTY-024, REQ-PTY-025, REQ-PTY-026, REQ-PTY-027, REQ-PTY-028, REQ-PTY-029, REQ-PTY-049, REQ-PTY-057 | AC-PTY-029, AC-PTY-030, AC-PTY-031, AC-PTY-032, AC-PTY-033, AC-PTY-034, AC-PTY-035, AC-PTY-057, AC-PTY-065 | RULE-PTY-002, RULE-PTY-009, RULE-PTY-012 | ENT-PTY-001, ENT-PTY-002, ENT-PTY-003 | SCR-REQ-PTY-001, SCR-REQ-PTY-002 |
| US-PTY-005 | REQ-PTY-030, REQ-PTY-031, REQ-PTY-032, REQ-PTY-033, REQ-PTY-034, REQ-PTY-035, REQ-PTY-036, REQ-PTY-037, REQ-PTY-038, REQ-PTY-110 | AC-PTY-036, AC-PTY-037, AC-PTY-038, AC-PTY-039, AC-PTY-040, AC-PTY-041, AC-PTY-042, AC-PTY-043, AC-PTY-044, AC-PTY-126 | RULE-PTY-005, RULE-PTY-006, RULE-PTY-015, RULE-PTY-016, RULE-PTY-017 | ENT-PTY-001, ENT-PTY-004 | SCR-REQ-PTY-001, SCR-REQ-PTY-002 |
| US-PTY-006 | REQ-PTY-007, REQ-PTY-013, REQ-PTY-039, REQ-PTY-040 | AC-PTY-009, AC-PTY-018, AC-PTY-045, AC-PTY-046 | RULE-PTY-018 | ENT-PTY-001, ENT-PTY-002 | SCR-REQ-PTY-001 |
| US-PTY-007 | REQ-PTY-041, REQ-PTY-042, REQ-PTY-043 | AC-PTY-047, AC-PTY-048, AC-PTY-049, AC-PTY-050, AC-PTY-051 | — | ENT-PTY-001, ENT-PTY-002 | — (operation for consuming modules, A8.1) |
| US-PTY-008 | REQ-PTY-044, REQ-PTY-045, REQ-PTY-046, REQ-PTY-047, REQ-PTY-048, REQ-PTY-049, REQ-PTY-063, REQ-PTY-083, REQ-PTY-105 | AC-PTY-052, AC-PTY-053, AC-PTY-054, AC-PTY-055, AC-PTY-056, AC-PTY-057, AC-PTY-071, AC-PTY-094, AC-PTY-119 | RULE-PTY-010, RULE-PTY-011, RULE-PTY-012, RULE-PTY-013, RULE-PTY-014, RULE-PTY-033 | ENT-PTY-001, ENT-PTY-002, ENT-PTY-003, ENT-PTY-005 | SCR-REQ-PTY-001, SCR-REQ-PTY-003, SCR-REQ-PTY-004 |
| US-PTY-009 | REQ-PTY-050, REQ-PTY-051, REQ-PTY-052, REQ-PTY-053, REQ-PTY-054 | AC-PTY-058, AC-PTY-059, AC-PTY-060, AC-PTY-061, AC-PTY-062 | RULE-PTY-013, RULE-PTY-019, RULE-PTY-020 | ENT-PTY-001, ENT-PTY-002, ENT-PTY-004 | — (operation for consuming modules, A8.1) |
| US-PTY-010 | REQ-PTY-052, REQ-PTY-055, REQ-PTY-056, REQ-PTY-057, REQ-PTY-058, REQ-PTY-059, REQ-PTY-060, REQ-PTY-061, REQ-PTY-062, REQ-PTY-063 | AC-PTY-060, AC-PTY-063, AC-PTY-064, AC-PTY-065, AC-PTY-066, AC-PTY-067, AC-PTY-068, AC-PTY-069, AC-PTY-070, AC-PTY-071 | RULE-PTY-013, RULE-PTY-020, RULE-PTY-021 | ENT-PTY-001, ENT-PTY-002, ENT-PTY-004 | — (operation for consuming modules, A8.1) |
| US-PTY-011 | REQ-PTY-048, REQ-PTY-064, REQ-PTY-065, REQ-PTY-066, REQ-PTY-067, REQ-PTY-068, REQ-PTY-069, REQ-PTY-078, REQ-PTY-110 | AC-PTY-056, AC-PTY-072, AC-PTY-073, AC-PTY-074, AC-PTY-075, AC-PTY-076, AC-PTY-077, AC-PTY-088, AC-PTY-127 | RULE-PTY-005, RULE-PTY-008, RULE-PTY-011, RULE-PTY-022, RULE-PTY-023 | ENT-PTY-001, ENT-PTY-002 | SCR-REQ-PTY-003 |
| US-PTY-012 | REQ-PTY-070, REQ-PTY-071, REQ-PTY-072, REQ-PTY-073, REQ-PTY-074, REQ-PTY-112 | AC-PTY-078, AC-PTY-079, AC-PTY-080, AC-PTY-081, AC-PTY-082, AC-PTY-083, AC-PTY-130, AC-PTY-131 | RULE-PTY-023 | ENT-PTY-002, ENT-PTY-005 | SCR-REQ-PTY-001, SCR-REQ-PTY-003, SCR-REQ-PTY-004, SCR-REQ-PTY-005 |
| US-PTY-013 | REQ-PTY-069, REQ-PTY-075, REQ-PTY-076, REQ-PTY-077, REQ-PTY-078 | AC-PTY-077, AC-PTY-084, AC-PTY-085, AC-PTY-086, AC-PTY-087, AC-PTY-088 | RULE-PTY-024 | ENT-PTY-001, ENT-PTY-002 | SCR-REQ-PTY-003 |
| US-PTY-014 | REQ-PTY-068, REQ-PTY-074, REQ-PTY-079, REQ-PTY-080, REQ-PTY-081, REQ-PTY-082, REQ-PTY-083, REQ-PTY-084, REQ-PTY-085, REQ-PTY-110 | AC-PTY-076, AC-PTY-083, AC-PTY-089, AC-PTY-090, AC-PTY-091, AC-PTY-092, AC-PTY-093, AC-PTY-094, AC-PTY-095, AC-PTY-096, AC-PTY-124, AC-PTY-135 | RULE-PTY-005 | ENT-PTY-001, ENT-PTY-002 | SCR-REQ-PTY-003 |
| US-PTY-015 | REQ-PTY-086, REQ-PTY-087, REQ-PTY-088, REQ-PTY-089, REQ-PTY-090 | AC-PTY-097, AC-PTY-098, AC-PTY-099, AC-PTY-100, AC-PTY-101 | — | ENT-PTY-002, ENT-PTY-005 | SCR-REQ-PTY-004, SCR-REQ-PTY-005 |
| US-PTY-016 | REQ-PTY-089, REQ-PTY-091, REQ-PTY-092, REQ-PTY-093, REQ-PTY-094, REQ-PTY-095, REQ-PTY-096, REQ-PTY-097, REQ-PTY-098, REQ-PTY-099, REQ-PTY-100, REQ-PTY-101, REQ-PTY-102, REQ-PTY-103, REQ-PTY-104, REQ-PTY-105, REQ-PTY-107, REQ-PTY-109, REQ-PTY-110, REQ-PTY-111, REQ-PTY-112, REQ-PTY-113 | AC-PTY-100, AC-PTY-102, AC-PTY-103, AC-PTY-104, AC-PTY-105, AC-PTY-106, AC-PTY-107, AC-PTY-108, AC-PTY-109, AC-PTY-110, AC-PTY-111, AC-PTY-112, AC-PTY-113, AC-PTY-114, AC-PTY-115, AC-PTY-116, AC-PTY-117, AC-PTY-118, AC-PTY-119, AC-PTY-121, AC-PTY-125, AC-PTY-128, AC-PTY-129, AC-PTY-130, AC-PTY-131, AC-PTY-132, AC-PTY-133, AC-PTY-134 | RULE-PTY-005, RULE-PTY-023, RULE-PTY-025, RULE-PTY-026, RULE-PTY-027, RULE-PTY-028, RULE-PTY-029, RULE-PTY-030, RULE-PTY-031, RULE-PTY-032, RULE-PTY-033, RULE-PTY-034 | ENT-PTY-002, ENT-PTY-005 | SCR-REQ-PTY-004, SCR-REQ-PTY-005 |
| US-PTY-017 | REQ-PTY-106, REQ-PTY-107, REQ-PTY-108, REQ-PTY-109 | AC-PTY-120, AC-PTY-121, AC-PTY-122, AC-PTY-123, AC-PTY-134 | RULE-PTY-030 | ENT-PTY-002, ENT-PTY-005 | SCR-REQ-PTY-003, SCR-REQ-PTY-004 |

Checks: every story → ≥ 1 REQ; every REQ → ≥ 1 AC; every RULE traces to ≥ 1 REQ (A5); every SCR-REQ traces to ≥ 1 REQ (B1). REQs without a screen are operations for consuming modules (A8.1) or platform behaviour (global search, provisioning, audit).

## Decisions applied

| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| D-01 | The Party `code` is a business number from given `sequence`, assigned on first save, read-only | profile NUMBERING; §3.3 test (a) — printed on invoices and statements | Use separate number series per role if the Owner wants customer and supplier numbers to differ |
| D-02 | Customer and Supplier registration need one name only; customer group prefilled RETAIL | KB §6.1, §6.10; DP §5 G7; POL-PTY-006 | Make mobile mandatory (still ≤ 4 mandatory inputs) |
| D-03 | Email is unique among active Parties, compared without letter case; mobile is not unique | KB §1 "one customer base"; POL-PTY-001 | Make mobile unique as well |
| D-04 | Seeds: CUSTOMER_GROUP = RETAIL, WHOLESALE; ADDRESS_TYPE = BILLING, DELIVERY; both open | PRD US-PTY-005, US-PTY-006 examples; module-registry LOOKUPS OWNED | Add or rename values as lookup data |
| D-05 | Party carries an optional `taxRegistrationNo` in the Advanced section | KB §2 (mandatory e-invoicing), §5 #8 | Make it mandatory for the WHOLESALE customer group |
| D-06 | The credit-override permission is UPDATE on PTY_CREDIT_OVERRIDE, held by the Owner preset; Seller/Cashier holds VIEW, CREATE and DELETE (cancel own) | KB §3 D8 (role presets); PRD note "which preset holds that permission is a P1 decision" | Grant UPDATE on PTY_CREDIT_OVERRIDE to a custom role |
| D-07 | Customer global search: term ≥ 2 characters, at most 10 results, exact code first then name, Arabic letter forms unified | KB §6.6 | Change the thresholds |
| D-08 | Walk-in Customer: nameAr «عميل نقدي», nameEn "Walk-in customer", customerGroupCode RETAIL | POL-PTY-009; module-registry decision 9 (as cited by the PRD); KB §6.8 | The Owner renames it on the Customer screen |
| D-09 | First Address of a type becomes default; on deactivation of the default the most recently updated remaining one becomes default | KB §6.8 (defaults) | Leave the type without a default until the user picks one |
| ADR-PTY-011 | Story priority from delivery waves | inherited (P0.5) | ACCEPTED |
| ADR-PTY-012 | Supplier payment terms are module scope, flag-independent | inherited (P0.5) — applied in ENT-PTY-003.paymentTermDays | ACCEPTED |
| ADR-PTY-013 | Customers only in the global search | inherited (P0.5) — applied in REQ-PTY-041 | ACCEPTED |
| ADR-PTY-014 | Party identity with Customer and Supplier roles; credit limit and payment terms (in days) on the Customer; Address SHARED (owner) | module-registry decisions 4, 6 (as cited by the PRD); registry OI-16 | ACCEPTED — non-breaking |
| ADR-PTY-015 | Guest matching and conversion: email then unique mobile; contact normalisation and formats; CUSTOMER-realm invitation through given `sec` for new and matched Customers; link on acceptance; guest details never overwrite | module-registry decision 5 (as cited by the PRD); KB §3 D5 | ACCEPTED — non-breaking |
| ADR-PTY-016 | Credit overrun approval held as the PTY Credit override request (single step, A7), sale held by text reference | POL-PTY-008; DAG (KB §5); DP §5 G11 | ACCEPTED — non-breaking |
| ADR-PTY-017 | Open exposure from registered credit exposure contributors (0 when none, EXPOSURE_UNAVAILABLE after 3 s); empty credit limit = no credit; `b2bCredit` switch-off answered to the flag owner | module-registry EXTENSION POINT "Customer credit exposure" (as cited by the PRD); KB §6.2 | ACCEPTED — non-breaking |
| ADR-PTY-035 | Platform columns tenantId and version on every entity (A3 standard fields) | erp-core checklist C1, C2; gate-analysis G2 | ACCEPTED — non-breaking (supersedes ADR-PTY-021) |
| ADR-PTY-037 | currencyCode on ENT-PTY-002 and ENT-PTY-005, hidden while the tenant has one currency; tenant base currency read through SettingsApi until CORE-GAP-tenant-base-currency is answered | business-policies SCOPE EXCEPTIONS (currency dimension in the model); profile check CM-8; gate-analysis G11 | ACCEPTED — non-breaking (supersedes ADR-PTY-020) |
| ADR-PTY-038 | `pty.PartyRoleDeactivated` published on the given outbox (A8.1, A9) | erp-core checklist C19, F11; gate-analysis G4 | ACCEPTED — non-breaking |
| ADR-PTY-039 | In-process callers hold the PTY authority; either UPDATE suffices for the Address of a Party holding both roles (B4) | erp-core checklist C13; gate-analysis G5, G14 | ACCEPTED — non-breaking (revises ADR-PTY-025) |
| ADR-PTY-041 | Inactive Customer or Party refused for credit operations and address writes; open requests cancelled on Customer deactivation; decisions and use refused while `b2bCredit` is off (RULE-PTY-005, RULE-PTY-023, A7, REQ-PTY-110 … REQ-PTY-112) | ARCH-5; gate-analysis G12 | ACCEPTED — non-breaking |
| ADR-PTY-042 | One open request per sale (RULE-PTY-034, REQ-PTY-113); a use is bound to the source sale (RULE-PTY-028) | gate-analysis G13 | ACCEPTED — non-breaking |
| ADR-PTY-043 | Supplier search by email (REQ-PTY-018, SCR-REQ-PTY-002 B2) so a RULE-PTY-002 refusal reaches a Supplier-only Party | RULE-PTY-002; gate-analysis G1 | ACCEPTED — non-breaking |
| ADR-PTY-044 | A9 `given-usage` rows name only seams the given publishes: tenant re-binding through `TenantRunner`, the module manifest through the module api doc; the entity base class of `modules.common` has no row | C-GIVEN-2; P2 repair round 1 | ACCEPTED — non-breaking |

## Access summary

| Page code | Screen | Owner | Seller/Cashier | Warehouse | Flag |
|---|---|---|---|---|---|
| PTY_CUSTOMER | SCR-REQ-PTY-001 Customers | VIEW, CREATE, UPDATE, DELETE | VIEW, CREATE, UPDATE | — | — |
| PTY_SUPPLIER | SCR-REQ-PTY-002 Suppliers | VIEW, CREATE, UPDATE, DELETE | — | VIEW | — |
| PTY_CUSTOMER_CREDIT | SCR-REQ-PTY-003 Customer credit | VIEW, UPDATE | VIEW | — | `b2bCredit` |
| PTY_CREDIT_OVERRIDE | SCR-REQ-PTY-004 Credit override requests | VIEW, CREATE, UPDATE, DELETE | VIEW, CREATE, DELETE | — | `b2bCredit` |
| PTY_CREDIT_SETTINGS | SCR-REQ-PTY-005 Credit control settings | VIEW, UPDATE | — | — | `b2bCredit` |

Permissions are derived by the security module from the page codes (PERM_<PAGE_CODE>_<ACTION>); this SRS does not seed them. Custom roles receive actions through given `sec`.
══════════════════════════════════════════════════════════════════

