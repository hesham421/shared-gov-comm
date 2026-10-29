<!-- source: PHASE:DATA-DOM / SUB:DATA-DOM-MASTER -->
<!-- context: DATA-DOM-HEADER.md — phase-level preamble -->
<!-- traces: DBF-CAT-001, DBF-CAT-002, DBF-CAT-003, DBF-CAT-004, DBF-CAT-005, DBF-CAT-006, DBF-CAT-007, DBF-CAT-008, DBF-CAT-009, DBF-CAT-010, DBF-CAT-011, DBF-CAT-012, DBF-CAT-013, DBF-CAT-014, DBF-CAT-015, DBF-CAT-016, DBF-CAT-017, DBF-CAT-018, DBF-CAT-019, DBF-CAT-020, DBF-CAT-021, DBF-CAT-022, DBF-CAT-023, DBF-CAT-024, DBF-CAT-025, DBF-CAT-026, DBF-CAT-027, DBF-CAT-028, DBF-CAT-029, DBF-CAT-030, DBF-CAT-031, DBF-CAT-032, DBF-CAT-033, DBF-CAT-034, DBF-CAT-060, DBF-CAT-061, DBF-CAT-062, DBF-CAT-063, DBF-CAT-064, DBF-CAT-065, DBF-CAT-066, DBF-CAT-067, DBF-CAT-068, DBF-CAT-069, DBF-CAT-070, DBF-CAT-071, DBF-CAT-072, DBF-CAT-073, DBF-CAT-074, DBF-CAT-075, DBF-CAT-076, DBF-CAT-077, DBF-CAT-078, DBF-CAT-079, DBF-CAT-080, DBF-CAT-081, DBF-CAT-082, DBF-CAT-083, DBF-CAT-084, DBF-CAT-085, DBF-CAT-086, DBF-CAT-087, DBF-CAT-088, DBF-CAT-089, DBF-CAT-090, DBF-CAT-091, DBF-CAT-092, DBF-CAT-093, DBF-CAT-094, DBF-CAT-095, DBF-CAT-096, DBF-CAT-097, DBF-CAT-098, DBF-CAT-099, DBF-CAT-100, DBF-CAT-101, DBF-CAT-102, DBF-CAT-103, DBF-CAT-104, DBF-CAT-105, DBF-CAT-106, DBF-CAT-107, DBF-CAT-108, DBF-CAT-109, DBF-CAT-110, DBF-CAT-111, DBF-CAT-112, DBF-CAT-113, DBF-CAT-114, DBF-CAT-115, DBF-CAT-116, DBF-CAT-117, DBF-CAT-118, DBF-CAT-119, DBF-CAT-120, DBF-CAT-121, DBF-CAT-122, DBF-CAT-123, DBF-CAT-124, DBF-CAT-125, DBF-CAT-126, DBF-CAT-127, DBF-CAT-128, DBF-CAT-129, DBF-CAT-130, DBF-CAT-131, DBF-CAT-132, DBF-CAT-133, DBF-CAT-134, DBF-CAT-135, REQ-CAT-049 -->
<!-- SUB:DATA-DOM-MASTER:START traces=DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-016,DBF-CAT-017,DBF-CAT-018,DBF-CAT-019,DBF-CAT-020,DBF-CAT-021,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-025,DBF-CAT-026,DBF-CAT-027,DBF-CAT-028,DBF-CAT-029,DBF-CAT-030,DBF-CAT-031,DBF-CAT-032,DBF-CAT-033,DBF-CAT-034,DBF-CAT-060,DBF-CAT-061,DBF-CAT-062,DBF-CAT-063,DBF-CAT-064,DBF-CAT-065,DBF-CAT-066,DBF-CAT-067,DBF-CAT-068,DBF-CAT-069,DBF-CAT-070,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077,DBF-CAT-078,DBF-CAT-079,DBF-CAT-080,DBF-CAT-081,DBF-CAT-082,DBF-CAT-083,DBF-CAT-084,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089,DBF-CAT-090,DBF-CAT-091,DBF-CAT-092,DBF-CAT-093,DBF-CAT-094,DBF-CAT-095,DBF-CAT-096,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102,DBF-CAT-103,DBF-CAT-104,DBF-CAT-105,DBF-CAT-106,DBF-CAT-107,DBF-CAT-108,DBF-CAT-109,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-115,DBF-CAT-116,DBF-CAT-117,DBF-CAT-118,DBF-CAT-119,DBF-CAT-120,DBF-CAT-121,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129,DBF-CAT-130,DBF-CAT-131,DBF-CAT-132,DBF-CAT-133,DBF-CAT-134,DBF-CAT-135,REQ-CAT-049 -->
### SUB DATA-DOM-MASTER

### ENT-CAT-001 — Item / صنف      kind: master
BINDINGS       table CAT_ITEM · PK ITEM_PK (DBF-CAT-001) · PK generation `identity` → `ITEM_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_ITEM · db-script CAT v2 (P2)
BUSINESS CODE  code · CODE (DBF-CAT-002) · format `ITM-` + 6 digits (series CAT_ITEM, pattern `{PREFIX}-{SEQ:6}`) · uniqueness UQ_CAT_ITEM_CODE (TENANT_ID, CODE) · generation NumberSeriesApi on first save · never in a create/update body, always in the response
DEFAULT FIELDS master → nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt (all bound below) + the given's tenantId, version
#### FIELDS ENT-CAT-001 — CAT_ITEM
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-001 | itemPk | ITEM_PK | BIGINT | NOT NULL | yes | PK_CAT_ITEM, UQ_CAT_ITEM_PK | المعرّف / Identifier |
| DBF-CAT-002 | code | CODE | VARCHAR(20) | NOT NULL | yes (system) | UQ_CAT_ITEM_CODE | الرمز / Code |
| DBF-CAT-003 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | CHK_CAT_ITEM_NAME_AR, UQ_CAT_ITEM_NAME_AR | الاسم بالعربية / Arabic name |
| DBF-CAT-004 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | الاسم بالإنجليزية / English name |
| DBF-CAT-005 | itemKindCode | ITEM_KIND_CODE | VARCHAR(50) | NOT NULL | after create | — | نوع الصنف / Item kind |
| DBF-CAT-006 | baseUomCode | BASE_UOM_CODE | VARCHAR(50) | NOT NULL | after create | — | وحدة القياس الأساسية / Base unit of measure |
| DBF-CAT-007 | brandCode | BRAND_CODE | VARCHAR(50) | NULL | no | — | العلامة التجارية / Brand |
| DBF-CAT-008 | taxClassCode | TAX_CLASS_CODE | VARCHAR(50) | NOT NULL | no | — | التصنيف الضريبي / Tax class |
| DBF-CAT-009 | einvoiceCodeTypeCode | EINVOICE_CODE_TYPE_CODE | VARCHAR(50) | NULL | no | CHK_CAT_ITEM_EINVOICE_CODE | نوع رمز الفاتورة الإلكترونية / E-invoice code type |
| DBF-CAT-010 | einvoiceCode | EINVOICE_CODE | VARCHAR(100) | NULL | no | CHK_CAT_ITEM_EINVOICE_CODE | رمز الفاتورة الإلكترونية / E-invoice code |
| DBF-CAT-011 | descriptionAr | DESCRIPTION_AR | VARCHAR(2000) | NULL | no | — | الوصف بالعربية / Arabic description |
| DBF-CAT-012 | descriptionEn | DESCRIPTION_EN | VARCHAR(2000) | NULL | no | — | الوصف بالإنجليزية / English description |
| DBF-CAT-013 | publishStateCode | PUBLISH_STATE_CODE | VARCHAR(50) | NOT NULL | yes (PATCH only) | — | حالة النشر / Publish state |
| DBF-CAT-014 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE/PATCH only) | — | نشط / Active |
| DBF-CAT-015 | categoryFk | CATEGORY_FK | BIGINT | NULL | no | FK_CAT_ITEM_CAT_CATEGORY | الفئة / Category |
| DBF-CAT-016 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-017 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-018 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-019 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-020 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-021 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request excludes itemPk, code (a supplied code is rejected — RULE-CAT-004), publishStateCode (set UNPUBLISHED), isActiveFl (set true), tenantId, version, audit · update-request excludes itemPk, publishStateCode, isActiveFl, tenantId, audit; it carries version (optimistic lock) and may echo code / itemKindCode / baseUomCode, which are rejected when they differ from the stored value (RULE-CAT-004, 022, 007) · response ItemDetail / ItemSummary include itemPk, code, every stored field, version, audit, displayNameEn (derived)
LOOKUP FIELDS  itemKindCode · ITEM_KIND_CODE (DBF-CAT-005) · CAT_ITEM_KIND · baseUomCode · BASE_UOM_CODE (DBF-CAT-006) · CAT_UOM · brandCode · BRAND_CODE (DBF-CAT-007) · CAT_BRAND · taxClassCode · TAX_CLASS_CODE (DBF-CAT-008) · CAT_TAX_CLASS · einvoiceCodeTypeCode · EINVOICE_CODE_TYPE_CODE (DBF-CAT-009) · CAT_EINVOICE_ITEM_CODE_TYPE · publishStateCode · PUBLISH_STATE_CODE (DBF-CAT-013) · CAT_PUBLISH_STATE — each stores the code, values read through MdlLookupApi (frontend: API-CAT-052 for the key — ADR-CAT-065)
DOMAIN RULES
- RULE-CAT-001 — Arabic name required · scope ENT-CAT-001, 003, 004, 006, 010 · trigger on create · on update · on import row · statement: The system shall require a nameAr of 1 to 200 characters that contains at least one Arabic letter when a record of scope is saved. · DB CHK_CAT_ITEM_NAME_AR, CHK_CAT_VAR_ATTR_NAME_AR, CHK_CAT_VAR_ATTR_VALUE_NAME_AR, CHK_CAT_CATEGORY_NAME_AR, CHK_CAT_ITEM_MEDIA_NAME_AR (Arabic letter) + domain (presence, length) · layer domain (ItemDomain, VariantAttributeDomain, VariantAttributeValueDomain, CategoryDomain, ItemMediaDomain) · message CAT-422-NAME-AR-INVALID
- RULE-CAT-002 — English name length · scope ENT-CAT-001, 003, 004, 006, 010 · trigger on create · on update · on import row · statement: The system shall prevent saving a nameEn longer than 200 characters. · DB column length 200 + domain · layer domain (the five companions of RULE-CAT-001) · message CAT-422-NAME-EN-TOO-LONG
- RULE-CAT-003 — One active Item per name · scope ENT-CAT-001 · trigger on create · on update (not on the categoryFk-only repair of an inactive Item, API-CAT-003 — checked again at its reactivation) · on reactivation · on import row · statement: The system shall prevent saving or reactivating an Item when its nameAr, after Arabic normalisation (the tashkeel marks U+064B–U+065F and U+0670 and the tatweel U+0640 removed, and nothing else — the Arabic-Indic digits U+0660–U+0669 are kept; أ إ آ ٱ read as ا, ة as ه, ى as ي; Latin letters lower-cased; leading, trailing and repeated spaces removed — ADR-CAT-062), equals the normalised nameAr of another active Item of the tenant or of an earlier valid row of the same import file. · DB UQ_CAT_ITEM_NAME_AR (partial, on CAT_NORMALIZE_AR(NAME_AR), whose removed set is written by code point — db-script BLOCK 1) + service (QR-CAT-002; same-file rows in memory, compared through the service normaliser `CatArabicNormalizer`, which removes exactly the same code points and folds the same letters as CAT_NORMALIZE_AR, so the in-file check and the index always agree — «علبة ٢٥» and «علبة ٣٠» stay two names, «أحمر» and «احمر» one) · layer service · message CAT-409-ITEM-NAME-DUPLICATE ({code} = the code QR-CAT-002 returns for the other active Item, or, for an earlier row of the same import file, the code the import gave to that row's Item — the next action)
- RULE-CAT-004 — Codes are system-assigned · scope ENT-CAT-001, 002 · trigger on create · on update · statement: The system shall assign the code of an Item from series CAT_ITEM and of a Variant from series CAT_VARIANT on first save, and prevent any request from supplying or changing a code (NUMBERING: document numbers come from the core sequence module). · DB UQ_CAT_ITEM_CODE, UQ_CAT_VARIANT_CODE + domain (ItemDomain / VariantDomain reject a supplied or changed code; the service allocates it) · layer domain · message CAT-422-CODE-SYSTEM-ASSIGNED
- RULE-CAT-005 — No record without a code · scope ENT-CAT-001, 002 · trigger on create · statement: The system shall prevent storing an Item or a Variant when the core number series returns no code. · DB — · layer service (transaction rolled back) · message CAT-500-CODE-NOT-ISSUED
- RULE-CAT-006 — Lookup-backed fields hold active values · scope ENT-CAT-001, 002, 008, 009, 011, 012 · trigger on create · on update · on import row · statement: The system shall require every changed lookup-backed field to hold an active value of its lookup key (A6), and shall keep an unchanged value that was deactivated later. · DB — (D-P2-05) · layer service via MdlLookupApi · message CAT-422-LOOKUP-VALUE-INACTIVE
- RULE-CAT-007 — Base unit fixed after first save · scope ENT-CAT-001 · trigger on update · statement: The system shall prevent changing baseUomCode of a saved Item. · DB — · layer domain (ItemDomain.from) · message CAT-422-BASE-UOM-FIXED
- RULE-CAT-008 — Flag-bound operation refused while its flag is off · scope ENT-CAT-001, 003, 004, 005, 007 · trigger on create of an Item of kind BUNDLE (API-CAT-001) · on Variant generation (API-CAT-006) · on Bundle component save (API-CAT-017) · on create or update of a Variant attribute (API-CAT-025, 027) · on create or update of a Variant attribute value (API-CAT-029, 030) · statement: The system shall prevent creating an Item of kind BUNDLE and saving Bundle components while the `bundles` flag is off, and generating Variants and creating or updating Variant attributes and Variant attribute values while the `variants` flag is off, reading the flag through the FeatureFlags API. Reading, deactivating and reactivating records that already exist stay allowed while a flag is off (API-CAT-002, 004, 005, 009, 026, 028, 031, 041 read no flag), so data that depends on the flag can be cleaned up. · DB — · layer service (`CatCapabilityFlags`) · message CAT-422-CAPABILITY-OFF ({flag} = `variants` or `bundles`)
- RULE-CAT-009 — Unknown flag value refuses the operation · scope ENT-CAT-001, 003, 004, 005, 007 · trigger on any operation RULE-CAT-008 refuses while its flag is off · statement: The system shall prevent an operation that RULE-CAT-008 binds to a flag when the FeatureFlags read of that flag returns no value. · DB — · layer service · message CAT-500-CAPABILITY-UNREADABLE
- RULE-CAT-011 — Bundles cannot be turned off while in use · scope ENT-CAT-001 · trigger on turn-off check of `bundles` · statement: The system shall answer "not allowed" when an active Item of kind BUNDLE exists. · DB — · layer service (QR-CAT-009) · message CAT-200-BUNDLES-IN-USE ({count})
- RULE-CAT-022 — Item kind fixed after first save · scope ENT-CAT-001 · trigger on update · statement: The system shall prevent changing itemKindCode of a saved Item. · DB — · layer domain (ItemDomain.from) · message CAT-422-ITEM-KIND-FIXED
- RULE-CAT-026 — Item Category active · scope ENT-CAT-001 · trigger on create · on update · on import row · on reactivation · statement: The system shall require a changed categoryFk of an Item, and the categoryFk of an Item being reactivated, to be empty or to point to an active Category. · DB FK_CAT_ITEM_CAT_CATEGORY (existence) + service (active, QR-CAT-032) · layer service · message CAT-422-CATEGORY-INACTIVE
- RULE-CAT-041 — Publish state follows the Item · scope ENT-CAT-001 · trigger on publish · on Item deactivation · statement: The system shall prevent publishing an inactive Item or an Item with no active Variant, and set publishStateCode to UNPUBLISHED when an Item is deactivated. · DB — · layer service · message CAT-422-PUBLISH-BLOCKED
- RULE-CAT-042 — E-invoice code pair and format · scope ENT-CAT-001, 002 · trigger on create · on update · on import row · statement: The system shall require einvoiceCodeTypeCode and einvoiceCode to be both present or both empty, a GS1 code of 8 to 14 digits, and an EGS code that starts with "EG-" and has at most 100 characters. · DB CHK_CAT_ITEM_EINVOICE_CODE, CHK_CAT_VARIANT_EINVOICE_CODE (pair) + domain (format) · layer domain (ItemDomain, VariantDomain) · message CAT-422-EINVOICE-CODE-INVALID
- RULE-CAT-044 — Import file acceptance · scope ENT-CAT-001 · trigger on import submission · statement: The system shall require an xlsx file of at most 10 MB with at most 5,000 data rows and a header row equal to the import sheet's 11 columns. · DB — · layer service (the file type, size and bytes are read through the core once CORE-GAP-file-document-metadata-read is answered — a delivery dependency, ADR-CAT-076) · message CAT-422-IMPORT-FILE-REJECTED
- RULE-CAT-045 — Import row validation · scope ENT-CAT-001, 009 · trigger on import row · statement: The system shall validate each data row against RULE-CAT-001, RULE-CAT-002, RULE-CAT-003, RULE-CAT-006, RULE-CAT-026, RULE-CAT-029, RULE-CAT-030, RULE-CAT-032 and RULE-CAT-042, skip every row that fails, and report its row number, column and reason. · DB — · layer service · message CAT-200-IMPORT-ROW-REJECTED ({row}, {column}, {reason} = the failed rule's message)
- RULE-CAT-046 — Search term minimum · scope ENT-CAT-001 · trigger on catalogue search · statement: The system shall require a search term of at least 2 characters before searching. · DB — · layer service · message CAT-400-SEARCH-TERM-TOO-SHORT
- RULE-CAT-048 — Deactivation keeps records for consumers (ARCH-5) · scope ENT-CAT-001, 002, 006, 007, 008, 009 · trigger on deactivation · statement: The system shall deactivate a SHARED (owner) record by setting isActiveFl to false, never by deletion, keep it readable by identifier for consuming modules, exclude it from new selections and publish a catalogue-change event with change kind DEACTIVATED. · DB — (no DELETE statement exists) · layer service · message CAT-200-DEACTIVATED
- RULE-CAT-049 — Change events only after commit · scope ENT-CAT-001, 002, 005, 006, 007, 008, 009, 010 · trigger on commit or rollback of a catalogue change · statement: The system shall record each catalogue-change event in the same transaction as the change and deliver it only after commit. · DB — · layer service via OutboxPublisher · message CAT-500-CHANGE-NOT-SAVED
- RULE-CAT-050 — A reactivated Item has an active Variant · scope ENT-CAT-001, 002 · trigger on reactivation · statement: The system shall prevent reactivating an Item that has no active Variant. · DB — · layer service (QR-CAT-015 under the Item lock) · message CAT-422-ITEM-NO-ACTIVE-VARIANT
- RULE-CAT-053 — An inactive parent refuses child writes · scope ENT-CAT-001, 002, 008, 009, 010 · trigger on Item update that changes more than categoryFk (API-CAT-003) · on Variant e-invoice override (008) · on Item unit create or update (010, 012) · on barcode create (014, 015, 056) · on image create or update (018, 019) · statement: The system shall prevent changing any field of an inactive Item other than categoryFk, creating or changing an Item unit or image of an inactive Item, and adding a barcode to, or setting the e-invoice override of, a Variant that is inactive or belongs to an inactive Item. Three repair operations stay allowed on an inactive Item, each under its own rules: Variant generation (API-CAT-006 — RULE-CAT-008, 012, 013, 014, 020, 052), an update whose only change is categoryFk (API-CAT-003 — RULE-CAT-026) and the replacement of the Bundle components (API-CAT-017 — RULE-CAT-008, 018, 019) — ADR-CAT-068. Deactivating a child record stays allowed. · DB — · layer service (the Item's isActiveFl DBF-CAT-014, and for 008 / 014 / 015 / 056 the Variant's isActiveFl DBF-CAT-027, read under the Item lock QR-CAT-010, so a parallel deactivation of the Item cannot interleave — ADR-CAT-063) · message CAT-422-PARENT-INACTIVE
STATE MACHINE  status columns IS_ACTIVE_FL (DBF-CAT-014) × PUBLISH_STATE_CODE (DBF-CAT-013) · values ACTIVE·UNPUBLISHED, ACTIVE·PUBLISHED, INACTIVE·UNPUBLISHED · initial ACTIVE·UNPUBLISHED (create) · transitions: publish ACTIVE·UNPUBLISHED → ACTIVE·PUBLISHED (owner, API-CAT-005, RULE-CAT-041); unpublish ACTIVE·PUBLISHED → ACTIVE·UNPUBLISHED (owner, API-CAT-005); deactivate ACTIVE·* → INACTIVE·UNPUBLISHED (owner, API-CAT-004, RULE-CAT-021, 041, 048); reactivate INACTIVE·UNPUBLISHED → ACTIVE·UNPUBLISHED (owner, API-CAT-005, stays UNPUBLISHED; guarded by the ACTIVE-state invariants of SRS A7 — RULE-CAT-050, 026, 019, 003, ADR-CAT-059; each broken invariant has a repair allowed while INACTIVE — Variant generation API-CAT-006, a categoryFk-only update API-CAT-003, Bundle components API-CAT-017, or the Category's own reactivation API-CAT-053 — ADR-CAT-068) · terminal none · invalid transition (publish while inactive / without active Variant) → RULE-CAT-041
CROSS-MODULE   none — no XM id touches this entity (read by other modules through `CatalogueReadApi`, PHASE SVC-API)
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         ItemDomain.create(request, defaults) · ItemDomain.from(stored, request) — RULE-CAT-001, 002, 004 (code supplied or changed), 007, 022, 042 (pair and format); the service orchestrates RULE-CAT-003, 005, 006, 008, 009, 019, 021, 026, 041, 048, 050, 053 and the locks
AUDIT          @Audited(entityType = "CAT_ITEM")
REPOSITORY OPS QR-CAT-001, QR-CAT-002, QR-CAT-003, QR-CAT-004, QR-CAT-005, QR-CAT-006, QR-CAT-007, QR-CAT-008, QR-CAT-009, QR-CAT-010
Honours: CON-CAT-001

### ENT-CAT-002 — Variant / متغيّر الصنف      kind: master
BINDINGS       table CAT_VARIANT · PK VARIANT_PK (DBF-CAT-022) · PK generation `identity` → `VARIANT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_VARIANT
BUSINESS CODE  code · CODE (DBF-CAT-023) · format `VAR-` + 6 digits (series CAT_VARIANT) · uniqueness UQ_CAT_VARIANT_CODE (TENANT_ID, CODE) · generation NumberSeriesApi on first save · never in a body, always in the response
DEFAULT FIELDS master → code, isActiveFl, audit stored; nameAr / nameEn are derived at read, not stored (D-15, D-P2-08)
#### FIELDS ENT-CAT-002 — CAT_VARIANT
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-022 | variantPk | VARIANT_PK | BIGINT | NOT NULL | yes | PK_CAT_VARIANT, UQ_CAT_VARIANT_PK, UQ_CAT_VARIANT_PK_ITEM | المعرّف / Identifier |
| DBF-CAT-023 | code | CODE | VARCHAR(20) | NOT NULL | yes (system) | UQ_CAT_VARIANT_CODE | الرمز / Code |
| DBF-CAT-024 | isDefaultFl | IS_DEFAULT_FL | BOOLEAN | NOT NULL | yes (system) | UQ_CAT_VARIANT_DEFAULT | المتغيّر الأساسي / Default variant |
| DBF-CAT-025 | einvoiceCodeTypeCode | EINVOICE_CODE_TYPE_CODE | VARCHAR(50) | NULL | no | CHK_CAT_VARIANT_EINVOICE_CODE | نوع رمز الفاتورة الإلكترونية / E-invoice code type |
| DBF-CAT-026 | einvoiceCode | EINVOICE_CODE | VARCHAR(100) | NULL | no | CHK_CAT_VARIANT_EINVOICE_CODE | رمز الفاتورة الإلكترونية / E-invoice code |
| DBF-CAT-027 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-028 | itemFk | ITEM_FK | BIGINT | NOT NULL | yes (system) | FK_CAT_VARIANT_CAT_ITEM, UQ_CAT_VARIANT_PK_ITEM | الصنف / Item |
| DBF-CAT-029 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-030 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-031 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-032 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-033 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-034 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP no create body (Variants are created with the Item — default — or by generation); update-request (override) carries einvoiceCodeTypeCode, einvoiceCode, version and may echo code (rejected when it differs — RULE-CAT-004) · response VariantSummary / VariantDetail include variantPk, itemFk, code, derived nameAr / nameEn, isDefaultFl, isActiveFl, stored override, effective e-invoice pair, taxClassCode and baseUomCode of the Item, einvoiceUnitCode, attribute values, barcodes, version
LOOKUP FIELDS  einvoiceCodeTypeCode · EINVOICE_CODE_TYPE_CODE (DBF-CAT-025) · CAT_EINVOICE_ITEM_CODE_TYPE
DOMAIN RULES   (RULE-CAT-004, 005, 006, 008, 041, 042, 048, 049 — full text under ENT-CAT-001)
- RULE-CAT-012 — Existing Variants keep their identity · scope ENT-CAT-002, 005, 009 · trigger on Variant generation · statement: The system shall assign to the Item's existing active Variants the first selected value of every attribute they do not yet carry, keeping their variantPk, code and barcodes, before it creates Variants for the remaining combinations. · DB — · layer service · message CAT-201-EXISTING-VARIANTS-KEPT (success notice)
- RULE-CAT-014 — Variant limits per Item · scope ENT-CAT-002, 005 · trigger on Variant generation · statement: The system shall prevent a generation that would give an Item more than 3 Variant attributes or more than 300 active Variants. · DB — · layer service (under the Item lock, QR-CAT-010, QR-CAT-015) · message CAT-422-VARIANT-LIMIT-EXCEEDED
- RULE-CAT-015 — Last active Variant stays active · scope ENT-CAT-001, 002 · trigger on Variant deactivation · statement: The system shall prevent deactivating the last active Variant of an active Item. · DB — · layer service (under the Item lock) · message CAT-409-LAST-ACTIVE-VARIANT
- RULE-CAT-020 — A Bundle has one Variant · scope ENT-CAT-001, 002, 005 · trigger on Variant generation · statement: The system shall prevent generating Variants or assigning Variant attribute values for an Item of kind BUNDLE. · DB — · layer service · message CAT-422-BUNDLE-SINGLE-VARIANT
- RULE-CAT-036 — Label copy limits · scope ENT-CAT-002 · trigger on label request · statement: The system shall require 1 to 999 copies per Variant and at most 5,000 labels per request. · DB — (copy counts are request inputs) · layer service · message CAT-422-LABEL-COPIES-OUT-OF-RANGE
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-027) only — two states, deactivate via API-CAT-009 (RULE-CAT-015, 021, 048), which also deactivates the Variant's assignments (QR-CAT-020) and active barcodes (QR-CAT-072 — ADR-CAT-063); a Variant is reactivated only by regenerating its combination (REQ-CAT-049 creates a new one) — no other transition; an inactive Variant takes no new barcode and no e-invoice override (RULE-CAT-053) · DEFAULT FLAG isDefaultFl (DBF-CAT-024) is held by exactly one Variant of the Item and moves with the active Variants (REQ-CAT-140, REQ-CAT-141, ADR-CAT-072): deactivating the default while another Variant is active moves it, in the same transaction under the Item lock, to the remaining active Variant with the lowest code (API-CAT-009); generating Variants for an Item with 0 active Variants gives it to the first Variant created and clears it on the inactive former default (API-CAT-006); the former default's flag is always cleared before the new one is set (QR-CAT-014), so UQ_CAT_VARIANT_DEFAULT holds after each statement; an active Item therefore always has exactly one active default Variant (SRS A7 invariant 5, CON-CAT-002)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         VariantDomain.create(item, code) · VariantDomain.from(stored, request) — RULE-CAT-004 (code supplied or changed), 042 (override pair and format); the service orchestrates RULE-CAT-012, 013, 014, 015, 020, 021, 036, 052
AUDIT          @Audited(entityType = "CAT_VARIANT")
REPOSITORY OPS QR-CAT-011, QR-CAT-012, QR-CAT-013, QR-CAT-014, QR-CAT-015, QR-CAT-016, QR-CAT-017, QR-CAT-075
Honours: CON-CAT-002

### ENT-CAT-005 — Variant attribute assignment / إسناد قيمة خاصية للمتغيّر      kind: master (dependent of ENT-CAT-002, ADR-CAT-039)
BINDINGS       table CAT_VAR_ATTR_ASSIGN · PK VARIANT_ATTRIBUTE_ASSIGNMENT_PK (DBF-CAT-060) · PK generation `identity` → `VARIANT_ATTRIBUTE_ASSIGNMENT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_VAR_ATTR_ASSIGN
BUSINESS CODE  none
DEFAULT FIELDS dependent master → isActiveFl + audit (no own code or name: it shows the attribute and value names)
#### FIELDS ENT-CAT-005 — CAT_VAR_ATTR_ASSIGN
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-060 | variantAttributeAssignmentPk | VARIANT_ATTRIBUTE_ASSIGNMENT_PK | BIGINT | NOT NULL | yes | PK_CAT_VAR_ATTR_ASSIGN | المعرّف / Identifier |
| DBF-CAT-061 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (follows the Variant) | — | نشط / Active |
| DBF-CAT-062 | variantFk | VARIANT_FK | BIGINT | NOT NULL | yes (system) | FK_CAT_VAR_ATTR_ASSIGN_CAT_VARIANT, UQ_CAT_VAR_ATTR_ASSIGN_ATTR | المتغيّر / Variant |
| DBF-CAT-063 | variantAttributeFk | VARIANT_ATTRIBUTE_FK | BIGINT | NOT NULL | yes (system) | FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR, UQ_CAT_VAR_ATTR_ASSIGN_ATTR | الخاصية / Attribute |
| DBF-CAT-064 | variantAttributeValueFk | VARIANT_ATTRIBUTE_VALUE_FK | BIGINT | NOT NULL | yes (system) | FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE | القيمة / Value |
| DBF-CAT-065 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-066 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-067 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-068 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-069 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-070 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP no own request: rows are written by Variant generation (API-CAT-006) and deactivated with their Variant (API-CAT-009) · response AttributeValueView inside every Variant view
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-008, 009, 012, 014, 020 — full text under ENT-CAT-001 / ENT-CAT-002)
- RULE-CAT-010 — Variants cannot be turned off while in use · scope ENT-CAT-001, 002, 005 · trigger on turn-off check of `variants` · statement: The system shall answer "not allowed" when an active Variant attribute assignment exists on an active Variant of an active Item. · DB — · layer service (QR-CAT-009) · message CAT-200-VARIANTS-IN-USE ({count})
- RULE-CAT-013 — One value per attribute and unique combinations · scope ENT-CAT-002, 005 · trigger on Variant generation · statement: The system shall require each active Variant of an Item to carry exactly one value of each attribute used by the Item, and prevent two active Variants of the same Item from carrying the same combination; a generation that omits an attribute the Item already uses is refused under RULE-CAT-052 first. · DB UQ_CAT_VAR_ATTR_ASSIGN_ATTR (one active value per attribute) + FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE (value of the same attribute) + service (unique combination, under the Item lock) · layer service · message CAT-409-VARIANT-COMBINATION-DUPLICATE
- RULE-CAT-052 — Generation names every attribute the Item uses · scope ENT-CAT-002, 005 · trigger on Variant generation · statement: The system shall require a generation request to select at least one active value of every attribute that the Item's active Variants already carry. · DB — · layer service (QR-CAT-019 under the Item lock, checked before RULE-CAT-013 and RULE-CAT-014) · message CAT-422-VARIANT-ATTRIBUTE-MISSING
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-061) follows its Variant
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, DELETE
DOMAIN         VariantAttributeAssignmentDomain.create(variant, attribute, value) — the value belongs to the attribute (RULE-CAT-013, single record); combinations, RULE-CAT-014 and RULE-CAT-052 are cross-record and stay in the service
AUDIT          @Audited(entityType = "CAT_VAR_ATTR_ASSIGN")
REPOSITORY OPS QR-CAT-018, QR-CAT-019, QR-CAT-020, QR-CAT-021

### ENT-CAT-006 — Category / فئة      kind: master
BINDINGS       table CAT_CATEGORY · PK CATEGORY_PK (DBF-CAT-071) · PK generation `identity` → `CATEGORY_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_CATEGORY
BUSINESS CODE  code · CODE (DBF-CAT-072) · owner-entered, 1–30 Latin letters, digits or hyphens (CHK_CAT_CATEGORY_CODE) · uniqueness UQ_CAT_CATEGORY_CODE (TENANT_ID, CODE) · fixed after creation (contract CON-CAT-003)
DEFAULT FIELDS master → nameAr, nameEn, code, isActiveFl + audit
#### FIELDS ENT-CAT-006 — CAT_CATEGORY
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-071 | categoryPk | CATEGORY_PK | BIGINT | NOT NULL | yes | PK_CAT_CATEGORY, UQ_CAT_CATEGORY_PK | المعرّف / Identifier |
| DBF-CAT-072 | code | CODE | VARCHAR(30) | NOT NULL | after create | UQ_CAT_CATEGORY_CODE, CHK_CAT_CATEGORY_CODE | الرمز / Code |
| DBF-CAT-073 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | UQ_CAT_CATEGORY_NAME_AR, CHK_CAT_CATEGORY_NAME_AR | الاسم بالعربية / Arabic name |
| DBF-CAT-074 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | الاسم بالإنجليزية / English name |
| DBF-CAT-075 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-076 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-077 | parentCategoryFk | PARENT_CATEGORY_FK | BIGINT | NULL | no | FK_CAT_CATEGORY_CAT_CATEGORY, CHK_CAT_CATEGORY_PARENT_CATEGORY_FK | الفئة الأم / Parent category |
| DBF-CAT-078 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-079 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-080 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-081 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-082 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-083 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: code, nameAr, nameEn, parentCategoryFk, sortOrder (default next under the parent) · update-request: nameAr, nameEn, parentCategoryFk, sortOrder, version (code excluded) · response CategoryView / CategoryNode
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-001, 002, 048, 049 — full text under ENT-CAT-001; every committed Category change records a catalogue-change event — ADR-CAT-077)
- RULE-CAT-023 — Category hierarchy · scope ENT-CAT-006 · trigger on create · on update · on reactivation (API-CAT-053) · statement: The system shall require the parent Category to be active, to be neither the Category itself nor one of its sub-categories, and the resulting depth to be at most 5 levels; a Category being reactivated must have an empty or active parent. · DB CHK_CAT_CATEGORY_PARENT_CATEGORY_FK (not itself) + service (active, no cycle, depth — QR-CAT-037 under the tree lock) · layer service · message CAT-422-CATEGORY-PARENT-INVALID
- RULE-CAT-024 — Category code and sibling name unique · scope ENT-CAT-006 · trigger on create · on update · on reactivation (API-CAT-053) · statement: The system shall require a code of 1 to 30 Latin letters, digits or hyphens unique per tenant, and a nameAr unique among the active Categories of the same parent; a Category being reactivated keeps its code and must not carry the nameAr of another active Category of its parent. · DB UQ_CAT_CATEGORY_CODE, UQ_CAT_CATEGORY_NAME_AR, CHK_CAT_CATEGORY_CODE · layer service (QR-CAT-033) + constraint translation · message CAT-409-CATEGORY-DUPLICATE
- RULE-CAT-025 — Category in use stays active · scope ENT-CAT-006 · trigger on deactivation · statement: The system shall prevent deactivating a Category that has active sub-categories or active Items. · DB — · layer service (QR-CAT-038 under the Category lock) · message CAT-409-CATEGORY-IN-USE ({count})
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-076) only — deactivate via API-CAT-024 (RULE-CAT-025), reactivate via API-CAT-053 (RULE-CAT-023, 024 — ADR-CAT-069); the code is kept, so a mistaken deactivation never loses it; an inactive Category takes no update (API-CAT-023 answers CAT-400-REQUEST-INVALID) until it is reactivated
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         CategoryDomain.create(request) · CategoryDomain.from(stored, request) — RULE-CAT-001, 002, 024 (code format), 023 (not its own parent); active parent, cycle, depth, sibling names and RULE-CAT-025 in the service
AUDIT          @Audited(entityType = "CAT_CATEGORY")
REPOSITORY OPS QR-CAT-032, QR-CAT-033, QR-CAT-034, QR-CAT-035, QR-CAT-036, QR-CAT-037, QR-CAT-038, QR-CAT-073
Honours: CON-CAT-003

### ENT-CAT-007 — Bundle component / مكوّن الحزمة      kind: master (dependent of a BUNDLE Item, ADR-CAT-039)
BINDINGS       table CAT_BUNDLE_COMP · PK BUNDLE_COMPONENT_PK (DBF-CAT-084) · PK generation `identity` → `BUNDLE_COMPONENT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_BUNDLE_COMP
BUSINESS CODE  none — business key BUNDLE_ITEM_FK + COMPONENT_VARIANT_FK among active lines (UQ_CAT_BUNDLE_COMP_VARIANT)
DEFAULT FIELDS dependent master → isActiveFl + audit
#### FIELDS ENT-CAT-007 — CAT_BUNDLE_COMP
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-084 | bundleComponentPk | BUNDLE_COMPONENT_PK | BIGINT | NOT NULL | yes | PK_CAT_BUNDLE_COMP | المعرّف / Identifier |
| DBF-CAT-085 | quantity | QUANTITY | NUMERIC(18,3) | NOT NULL | no | CHK_CAT_BUNDLE_COMP_QUANTITY | الكمية / Quantity |
| DBF-CAT-086 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-087 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (line removal) | UQ_CAT_BUNDLE_COMP_VARIANT (partial) | نشط / Active |
| DBF-CAT-088 | bundleItemFk | BUNDLE_ITEM_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_BUNDLE_COMP_CAT_ITEM | الحزمة / Bundle |
| DBF-CAT-089 | componentVariantFk | COMPONENT_VARIANT_FK | BIGINT | NOT NULL | after create | FK_CAT_BUNDLE_COMP_CAT_VARIANT, UQ_CAT_BUNDLE_COMP_VARIANT | المتغيّر المكوِّن / Component variant |
| DBF-CAT-090 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-091 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-092 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-093 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-094 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-095 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP line-request (inside ItemCreateRequest and BundleComponentsRequest): componentVariantFk, quantity (default 1), sortOrder (default line order) · response BundleComponentView with componentVariantCode and componentBaseUomCode (both derived) · quantity is always in the base unit of the component Variant's Item, never in one of its Item units (ADR-CAT-080)
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-008, 009, 048, 049 — full text under ENT-CAT-001)
- RULE-CAT-018 — Bundle has components with quantities · scope ENT-CAT-001, 007 · trigger on Bundle save · statement: The system shall require at least one active component line per BUNDLE Item, each with a quantity above zero, at most 999,999.999 and with at most 3 decimals. · DB CHK_CAT_BUNDLE_COMP_QUANTITY + API document (`exclusiveMinimum: 0`, `maximum: 999999.999`, `multipleOf: 0.001` — CAT-400-REQUEST-INVALID first, PHASE CORE request numbers) + domain (BundleComponentDomain: quantity > 0, ≤ 999,999.999, scale ≤ 3) + service (≥ 1 active line) · layer domain + service · message CAT-422-BUNDLE-COMPONENTS-REQUIRED
- RULE-CAT-019 — Valid Bundle components · scope ENT-CAT-002, 007 · trigger on Bundle save · on component line change · on reactivation of the Bundle · statement: The system shall require each component Variant to be active, to belong to an active STANDARD Item, and to appear once among the active lines of the Bundle. · DB UQ_CAT_BUNDLE_COMP_VARIANT + service (active, STANDARD — QR-CAT-017 FOR SHARE) · layer service · message CAT-422-BUNDLE-COMPONENT-INVALID
- RULE-CAT-021 — Component of an active Bundle stays active · scope ENT-CAT-001, 002, 007 · trigger on Variant deactivation · on Item deactivation · statement: The system shall prevent deactivating a Variant, or the Item that owns it, while the Variant is an active component of an active Bundle. · DB — · layer service (QR-CAT-042 under the Item lock) · message CAT-409-VARIANT-IN-ACTIVE-BUNDLE ({count})
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-087) only — a removed line is deactivated (API-CAT-017)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         BundleComponentDomain.create(request) · BundleComponentDomain.from(stored, request) — RULE-CAT-018 (quantity > 0 with ≤ 3 decimals); line count, RULE-CAT-019 and RULE-CAT-021 in the service
AUDIT          @Audited(entityType = "CAT_BUNDLE_COMP")
REPOSITORY OPS QR-CAT-039, QR-CAT-040, QR-CAT-041, QR-CAT-042
Honours: CON-CAT-006

### ENT-CAT-008 — Item unit / وحدة الصنف      kind: master (dependent of ENT-CAT-001, ADR-CAT-039)
BINDINGS       table CAT_ITEM_UNIT · PK ITEM_UNIT_PK (DBF-CAT-096) · PK generation `identity` → `ITEM_UNIT_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_ITEM_UNIT
BUSINESS CODE  none — business key ITEM_FK + UOM_CODE among active units (UQ_CAT_ITEM_UNIT_UOM)
DEFAULT FIELDS dependent master → isActiveFl + audit
#### FIELDS ENT-CAT-008 — CAT_ITEM_UNIT
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-096 | itemUnitPk | ITEM_UNIT_PK | BIGINT | NOT NULL | yes | PK_CAT_ITEM_UNIT | المعرّف / Identifier |
| DBF-CAT-097 | uomCode | UOM_CODE | VARCHAR(50) | NOT NULL | after create | UQ_CAT_ITEM_UNIT_UOM | وحدة القياس / Unit of measure |
| DBF-CAT-098 | conversionFactor | CONVERSION_FACTOR | NUMERIC(18,6) | NOT NULL | after create | CHK_CAT_ITEM_UNIT_CONVERSION_FACTOR | معامل التحويل / Conversion factor |
| DBF-CAT-099 | isPurchaseFl | IS_PURCHASE_FL | BOOLEAN | NOT NULL | no | — | للشراء / Used for purchasing |
| DBF-CAT-100 | isSalesFl | IS_SALES_FL | BOOLEAN | NOT NULL | no | — | للبيع / Used for selling |
| DBF-CAT-101 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-102 | itemFk | ITEM_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_ITEM_UNIT_CAT_ITEM | الصنف / Item |
| DBF-CAT-103 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-104 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-105 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-106 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-107 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-108 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: uomCode, conversionFactor, isPurchaseFl (default true), isSalesFl (default true) · update-request: isPurchaseFl, isSalesFl, version; may echo conversionFactor, rejected when it differs (RULE-CAT-028) · response ItemUnitView with einvoiceUnitCode (derived)
LOOKUP FIELDS  uomCode · UOM_CODE (DBF-CAT-097) · CAT_UOM
DOMAIN RULES   (RULE-CAT-006, 048, 049 — full text under ENT-CAT-001)
- RULE-CAT-027 — Valid Item unit · scope ENT-CAT-008 · trigger on create · statement: The system shall require an Item unit's uomCode to differ from the Item's baseUomCode and from the uomCode of its other active Item units, and its conversionFactor to be above zero, at most 999,999.999999 and with at most 6 decimals. · DB UQ_CAT_ITEM_UNIT_UOM, CHK_CAT_ITEM_UNIT_CONVERSION_FACTOR + API document (`exclusiveMinimum: 0`, `maximum: 999999.999999`, `multipleOf: 0.000001` — CAT-400-REQUEST-INVALID first, PHASE CORE request numbers) + domain (ItemUnitDomain.create: differs from the Item's base unit, factor > 0, ≤ 999,999.999999, scale ≤ 6) + service (differs from the other active units) · layer domain + service · message CAT-422-ITEM-UNIT-INVALID
- RULE-CAT-028 — Conversion factor fixed after saving · scope ENT-CAT-008 · trigger on update · statement: The system shall prevent changing conversionFactor of a saved Item unit. · DB — · layer domain (ItemUnitDomain.from) · message CAT-422-CONVERSION-FACTOR-FIXED
- RULE-CAT-051 — Item unit used by active barcodes stays active · scope ENT-CAT-008, 009 · trigger on Item unit deactivation · statement: The system shall prevent deactivating an Item unit while active barcodes of the Variants of the same Item are bound to its uomCode. · DB — · layer service (QR-CAT-071 under the Item lock — ADR-CAT-060) · message CAT-409-ITEM-UNIT-HAS-BARCODES ({count})
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-101) only — deactivate via API-CAT-013 (RULE-CAT-051); an inactive unit takes no update (API-CAT-012 answers CAT-400-REQUEST-INVALID) and is not reactivated: the unit is added again (API-CAT-010)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         ItemUnitDomain.create(request, item) · ItemUnitDomain.from(stored, request) — RULE-CAT-027 (differs from the Item's baseUomCode; factor > 0 with ≤ 6 decimals), 028; other active units and RULE-CAT-051 in the service
AUDIT          @Audited(entityType = "CAT_ITEM_UNIT")
REPOSITORY OPS QR-CAT-043, QR-CAT-044, QR-CAT-045, QR-CAT-046, QR-CAT-071
Honours: CON-CAT-004

### ENT-CAT-009 — Barcode / باركود      kind: master (dependent of ENT-CAT-002, ADR-CAT-039)
BINDINGS       table CAT_BARCODE · PK BARCODE_PK (DBF-CAT-109) · PK generation `identity` → `BARCODE_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_BARCODE
BUSINESS CODE  barcodeValue · BARCODE_VALUE (DBF-CAT-110) serves as the record's code · GTIN / SCALE entered; INTERNAL = `04` + 10 digits of series CAT_INTERNAL_BARCODE + GS1 check digit (D-05) · uniqueness UQ_CAT_BARCODE_VALUE (TENANT_ID, BARCODE_VALUE) among active rows · read-only after save
DEFAULT FIELDS dependent master → isActiveFl + audit
#### FIELDS ENT-CAT-009 — CAT_BARCODE
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-109 | barcodePk | BARCODE_PK | BIGINT | NOT NULL | yes | PK_CAT_BARCODE | المعرّف / Identifier |
| DBF-CAT-110 | barcodeValue | BARCODE_VALUE | VARCHAR(48) | NOT NULL | after create | UQ_CAT_BARCODE_VALUE, CHK_CAT_BARCODE_BARCODE_VALUE | الباركود / Barcode |
| DBF-CAT-111 | barcodeTypeCode | BARCODE_TYPE_CODE | VARCHAR(50) | NOT NULL | after create | — | نوع الباركود / Barcode type |
| DBF-CAT-112 | uomCode | UOM_CODE | VARCHAR(50) | NOT NULL | after create | — | وحدة القياس / Unit of measure |
| DBF-CAT-113 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-114 | variantFk | VARIANT_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_BARCODE_CAT_VARIANT | المتغيّر / Variant |
| DBF-CAT-115 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-116 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-117 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-118 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-119 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-120 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request: barcodeValue, barcodeTypeCode, uomCode (default the Item's baseUomCode) · internal-request: uomCode only · no update · response BarcodeView
LOOKUP FIELDS  barcodeTypeCode · BARCODE_TYPE_CODE (DBF-CAT-111) · CAT_BARCODE_TYPE · uomCode · UOM_CODE (DBF-CAT-112) · CAT_UOM
DOMAIN RULES   (RULE-CAT-006, 048, 049 — full text under ENT-CAT-001)
- RULE-CAT-029 — Barcode unique among active barcodes · scope ENT-CAT-009 · trigger on create · on import row · statement: The system shall prevent saving a barcode whose barcodeValue equals the barcodeValue of an active barcode of the tenant or of an earlier valid row of the same import file, with SCALE barcodes compared only with SCALE barcodes. · DB UQ_CAT_BARCODE_VALUE (D-P2-11: lengths keep SCALE apart) + service (QR-CAT-048 returns the owning Item code; same-file rows in memory) · layer service · message CAT-409-BARCODE-DUPLICATE ({code} = the code QR-CAT-048 returns for the Item that owns the active barcode, or, for an earlier row of the same import file, the code the import gave to that row's Item)
- RULE-CAT-030 — GTIN format · scope ENT-CAT-009 · trigger on create · on import row · statement: The system shall require a GTIN barcodeValue of 8, 12, 13 or 14 digits whose last digit is the GS1 check digit. · DB CHK_CAT_BARCODE_BARCODE_VALUE (digits) + domain (BarcodeDomain: length, GS1 check digit) · layer domain · message CAT-422-GTIN-INVALID
- RULE-CAT-031 — Barcode unit belongs to the Item · scope ENT-CAT-009 · trigger on create · statement: The system shall require a barcode's uomCode to equal the Item's baseUomCode or the uomCode of an active Item unit of the same Item. · DB — · layer service (QR-CAT-044) · message CAT-422-BARCODE-UNIT-INVALID
- RULE-CAT-032 — Barcodes avoid scale prefixes · scope ENT-CAT-009 · trigger on create (GTIN, INTERNAL) · on import row · on scale label layout create · statement: The system shall prevent a GTIN or INTERNAL barcodeValue that begins with the key of an active scale label layout. · DB — · layer service (QR-CAT-059 / QR-CAT-053 under the scale-prefix lock) · message CAT-409-BARCODE-SCALE-PREFIX ({prefix})
- RULE-CAT-033 — Scale code length · scope ENT-CAT-009 · trigger on create (SCALE) · statement: The system shall require a SCALE barcodeValue to consist of digits only, with a length equal to the scaleCodeLength of an active scale label layout. · DB CHK_CAT_BARCODE_BARCODE_VALUE + service (length, QR-CAT-059) · layer service · message CAT-422-SCALE-CODE-LENGTH ({length})
- RULE-CAT-035 — Scale label decoding · scope ENT-CAT-009, 011 · trigger on barcode lookup of a 13-digit value with an active layout prefix · statement: The system shall require a valid EAN-13 check digit and an embedded scale code equal to an active SCALE barcodeValue before returning a Variant. · DB — · layer service · message CAT-422-SCALE-LABEL-UNREADABLE
- RULE-CAT-037 — Labelled Variant carries a barcode in its base unit · scope ENT-CAT-002, 009 · trigger on label request · statement: The system shall require every Variant of a label request to carry at least one active barcode of type GTIN or INTERNAL whose uomCode equals its Item's baseUomCode, and shall print the earliest-created such barcode as the Variant's label barcode; a SCALE code, or a barcode bound to an Item unit, is never printed and does not count (ADR-CAT-079). · DB — · layer service (QR-CAT-047 with its label filter on DBF-CAT-111, DBF-CAT-112 against DBF-CAT-006) · message CAT-422-LABEL-VARIANT-NO-BARCODE ({count}; the Variants are listed)
- RULE-CAT-047 — Barcode not found · scope ENT-CAT-009 · trigger on barcode lookup · statement: The system shall return no Variant, with a next action, when no active barcode of an active Variant of an active Item and no active scale label layout matches the value (ADR-CAT-059). · DB — · layer service · message CAT-404-BARCODE-NOT-FOUND
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-113) only — deactivate via API-CAT-016, or together with its Variant via API-CAT-009 (QR-CAT-072 — ADR-CAT-063); an inactive barcode never matches a lookup and no longer holds its value for RULE-CAT-029
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, DELETE
DOMAIN         BarcodeDomain.create(request, item, internalDigits) — RULE-CAT-030 (GTIN length and GS1 check digit), the INTERNAL value (04 + digits + check digit), digits-only SCALE values; uniqueness, unit, scale prefixes and lengths (RULE-CAT-029, 031, 032, 033) in the service
AUDIT          @Audited(entityType = "CAT_BARCODE")
REPOSITORY OPS QR-CAT-047, QR-CAT-048, QR-CAT-049, QR-CAT-050, QR-CAT-051, QR-CAT-052, QR-CAT-053, QR-CAT-072
Honours: CON-CAT-005

### ENT-CAT-010 — Item media / وسائط الصنف      kind: master (dependent of ENT-CAT-001, ADR-CAT-039)
BINDINGS       table CAT_ITEM_MEDIA · PK ITEM_MEDIA_PK (DBF-CAT-121) · PK generation `identity` → `ITEM_MEDIA_PK BIGINT GENERATED ALWAYS AS IDENTITY`, constraint PK_CAT_ITEM_MEDIA
BUSINESS CODE  none — the file is a given FileDocument (FILE_DOCUMENT_REF, DBF-CAT-122, given soft read, no XM)
DEFAULT FIELDS dependent master → nameAr, nameEn, isActiveFl + audit
#### FIELDS ENT-CAT-010 — CAT_ITEM_MEDIA
| DBF | property | column | type | null | read-only | constraint | label ar / en |
|---|---|---|---|---|---|---|---|
| DBF-CAT-121 | itemMediaPk | ITEM_MEDIA_PK | BIGINT | NOT NULL | yes | PK_CAT_ITEM_MEDIA | المعرّف / Identifier |
| DBF-CAT-122 | fileDocumentRef | FILE_DOCUMENT_REF | BIGINT | NOT NULL | yes (system) | — (given soft read) | ملف الصورة / Image file |
| DBF-CAT-123 | nameAr | NAME_AR | VARCHAR(200) | NOT NULL | no | CHK_CAT_ITEM_MEDIA_NAME_AR | وصف الصورة بالعربية / Arabic image description |
| DBF-CAT-124 | nameEn | NAME_EN | VARCHAR(200) | NULL | no | — | وصف الصورة بالإنجليزية / English image description |
| DBF-CAT-125 | sortOrder | SORT_ORDER | INTEGER | NOT NULL | no | — | الترتيب / Sort order |
| DBF-CAT-126 | isPrimaryFl | IS_PRIMARY_FL | BOOLEAN | NOT NULL | no | UQ_CAT_ITEM_MEDIA_PRIMARY | الصورة الرئيسية / Main image |
| DBF-CAT-127 | isActiveFl | IS_ACTIVE_FL | BOOLEAN | NOT NULL | yes (DELETE only) | — | نشط / Active |
| DBF-CAT-128 | itemFk | ITEM_FK | BIGINT | NOT NULL | yes (path) | FK_CAT_ITEM_MEDIA_CAT_ITEM | الصنف / Item |
| DBF-CAT-129 | variantFk | VARIANT_FK | BIGINT | NULL | no | FK_CAT_ITEM_MEDIA_CAT_VARIANT | المتغيّر / Variant |
| DBF-CAT-130 | tenantId | TENANT_ID | BIGINT | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-131 | version | VERSION | BIGINT | NOT NULL | no (optimistic-lock check) | — | — (platform column) |
| DBF-CAT-132 | createdBy | CREATED_BY | VARCHAR(100) | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-133 | createdAt | CREATED_AT | TIMESTAMPTZ | NOT NULL | yes (system) | — | — (platform column) |
| DBF-CAT-134 | updatedBy | UPDATED_BY | VARCHAR(100) | NULL | yes (system) | — | — (platform column) |
| DBF-CAT-135 | updatedAt | UPDATED_AT | TIMESTAMPTZ | NULL | yes (system) | — | — (platform column) |
DTO MEMBERSHIP create-request (JSON, ItemMediaCreateRequest): fileDocumentRef (the FILE document the client uploaded through the given FILE endpoint — ADR-CAT-056), nameAr (default the Item's nameAr), nameEn, variantFk · update-request: sortOrder, isPrimaryFl, nameAr, nameEn, variantFk, version · response MediaView
LOOKUP FIELDS  none
DOMAIN RULES   (RULE-CAT-001, 002, 049 — full text under ENT-CAT-001)
- RULE-CAT-038 — Image file and count · scope ENT-CAT-010 · trigger on upload · statement: The system shall require at most 20 active media records per Item. The file part of the rule — JPG, PNG or WEBP, at most 5 MB, public storage — is held by the FILE category `CAT_ITEM_IMAGE` (allowPublic = true, PNG / JPEG / WEBP, limit 5 MB) that CAT registers: the given FILE upload endpoint refuses any other file, in its own envelope, before CAT is called (ADR-CAT-076; CAT rebuilds no image validator — `modules.file` "Do not rebuild"). A fileDocumentRef that is not available in the caller's tenant, or has no public URL (`FileDocumentLookupApi.isAvailable` / `publicUrl` — for example a document of the private storage, which the storefront could not show, UXD-CAT-003, CON-CAT-015), answers CAT-404-NOT-FOUND, not this rule. One fileDocumentRef may be attached to several media records — of the same Item or of different Items — because removing an image never deletes its file (ADR-CAT-056 §4); the 20-record limit counts media records, not distinct files. · DB — · layer service (QR-CAT-054 count under the Item lock) · message CAT-422-IMAGE-NOT-ACCEPTED
- RULE-CAT-039 — Exactly one main image · scope ENT-CAT-010 · trigger on upload · on update · on removal · statement: The system shall keep exactly one active media record with isPrimaryFl true for every Item that has active media, by marking the first image primary, clearing the former primary when another is chosen, and promoting the lowest sortOrder when the primary is removed. · DB UQ_CAT_ITEM_MEDIA_PRIMARY (at most one) + service (exactly one, under the Item lock) · layer service · message CAT-200-PRIMARY-IMAGE-REPLACED (success notice)
- RULE-CAT-040 — Image Variant belongs to the Item · scope ENT-CAT-010 · trigger on upload · on update · statement: The system shall require a media record's variantFk, when set, to point to a Variant of the same Item. · DB FK_CAT_ITEM_MEDIA_CAT_VARIANT (composite TENANT_ID, VARIANT_FK, ITEM_FK) + service · layer service · message CAT-422-MEDIA-VARIANT-MISMATCH
STATE MACHINE  IS_ACTIVE_FL (DBF-CAT-127) only — removal deactivates (API-CAT-020); a removed image takes no update (API-CAT-019 answers CAT-400-REQUEST-INVALID, so it never takes isPrimaryFl) and is replaced by a new upload (API-CAT-018)
CROSS-MODULE   none
OPERATIONS     VIEW, CREATE, UPDATE, DELETE
DOMAIN         ItemMediaDomain.create(request, item) · ItemMediaDomain.from(stored, request) — RULE-CAT-001, 002 (descriptions; nameAr defaults to the Item's nameAr); RULE-CAT-038, 039 and 040 in the service
AUDIT          @Audited(entityType = "CAT_ITEM_MEDIA")
REPOSITORY OPS QR-CAT-054, QR-CAT-055, QR-CAT-056, QR-CAT-057, QR-CAT-058
<!-- SUB:DATA-DOM-MASTER:END -->
