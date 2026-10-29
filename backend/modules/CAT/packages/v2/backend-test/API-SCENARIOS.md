<!-- source: PHASE:TEST-PLAN-BE / SUB:API-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-CAT-001, AC-CAT-002, AC-CAT-003, AC-CAT-004, AC-CAT-008, AC-CAT-010, AC-CAT-011, AC-CAT-012, AC-CAT-018, AC-CAT-021, AC-CAT-022, AC-CAT-025, AC-CAT-026, AC-CAT-029, AC-CAT-030, AC-CAT-033, AC-CAT-034, AC-CAT-037, AC-CAT-039, AC-CAT-040, AC-CAT-044, AC-CAT-045, AC-CAT-046, AC-CAT-047, AC-CAT-048, AC-CAT-049, AC-CAT-050, AC-CAT-051, AC-CAT-058, AC-CAT-059, AC-CAT-060, AC-CAT-066, AC-CAT-067, AC-CAT-068, AC-CAT-069, AC-CAT-070, AC-CAT-076, AC-CAT-079, AC-CAT-080, AC-CAT-085, AC-CAT-086, AC-CAT-088, AC-CAT-094, AC-CAT-095, AC-CAT-097, AC-CAT-098, AC-CAT-099, AC-CAT-103, AC-CAT-104, AC-CAT-107, AC-CAT-108, AC-CAT-109, AC-CAT-111, AC-CAT-117, AC-CAT-119, AC-CAT-120, AC-CAT-121, AC-CAT-122, AC-CAT-123, AC-CAT-124, AC-CAT-127, AC-CAT-129, AC-CAT-137, AC-CAT-139, AC-CAT-140, AC-CAT-141, AC-CAT-152, AC-CAT-157, AC-CAT-159, AC-CAT-160, AC-CAT-163, AC-CAT-164, AC-CAT-165, AC-CAT-171, AC-CAT-174, API-CAT-001, API-CAT-002, API-CAT-003, API-CAT-004, API-CAT-005, API-CAT-006, API-CAT-007, API-CAT-008, API-CAT-009, API-CAT-010, API-CAT-011, API-CAT-012, API-CAT-014, API-CAT-015, API-CAT-017, API-CAT-018, API-CAT-019, API-CAT-020, API-CAT-021, API-CAT-022, API-CAT-023, API-CAT-024, API-CAT-025, API-CAT-026, API-CAT-027, API-CAT-028, API-CAT-029, API-CAT-030, API-CAT-031, API-CAT-032, API-CAT-033, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-038, API-CAT-039, API-CAT-040, API-CAT-041, API-CAT-042, API-CAT-043, API-CAT-044, API-CAT-045, API-CAT-046, API-CAT-047, API-CAT-048, API-CAT-049, API-CAT-050, API-CAT-051, API-CAT-052, API-CAT-053, API-CAT-054, API-CAT-055, API-CAT-056, REQ-CAT-001, REQ-CAT-002, REQ-CAT-003, REQ-CAT-004, REQ-CAT-008, REQ-CAT-010, REQ-CAT-011, REQ-CAT-012, REQ-CAT-017, REQ-CAT-020, REQ-CAT-021, REQ-CAT-024, REQ-CAT-025, REQ-CAT-028, REQ-CAT-029, REQ-CAT-032, REQ-CAT-033, REQ-CAT-036, REQ-CAT-038, REQ-CAT-039, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-049, REQ-CAT-056, REQ-CAT-057, REQ-CAT-058, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-068, REQ-CAT-074, REQ-CAT-077, REQ-CAT-078, REQ-CAT-083, REQ-CAT-084, REQ-CAT-086, REQ-CAT-092, REQ-CAT-093, REQ-CAT-095, REQ-CAT-096, REQ-CAT-097, REQ-CAT-101, REQ-CAT-102, REQ-CAT-104, REQ-CAT-105, REQ-CAT-106, REQ-CAT-107, REQ-CAT-109, REQ-CAT-115, REQ-CAT-117, REQ-CAT-118, REQ-CAT-119, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-125, REQ-CAT-127, REQ-CAT-131, REQ-CAT-132, REQ-CAT-133, REQ-CAT-134, REQ-CAT-136, REQ-CAT-137, REQ-CAT-138, REQ-CAT-140, REQ-CAT-141, REQ-CAT-148 -->
<!-- SUB:API-SCENARIOS:START traces=AC-CAT-174,AC-CAT-171,REQ-CAT-148,AC-CAT-165,REQ-CAT-140,REQ-CAT-141,AC-CAT-163,AC-CAT-164,AC-CAT-157,AC-CAT-159,AC-CAT-160,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,AC-CAT-137,AC-CAT-139,AC-CAT-140,AC-CAT-141,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,AC-CAT-001,AC-CAT-002,AC-CAT-003,AC-CAT-004,AC-CAT-008,AC-CAT-010,AC-CAT-011,AC-CAT-012,AC-CAT-018,AC-CAT-021,AC-CAT-022,AC-CAT-025,AC-CAT-026,AC-CAT-029,AC-CAT-030,AC-CAT-033,AC-CAT-034,AC-CAT-037,AC-CAT-039,AC-CAT-040,AC-CAT-044,AC-CAT-045,AC-CAT-046,AC-CAT-047,AC-CAT-048,AC-CAT-049,AC-CAT-050,AC-CAT-051,AC-CAT-058,AC-CAT-059,AC-CAT-060,AC-CAT-066,AC-CAT-067,AC-CAT-068,AC-CAT-069,AC-CAT-070,AC-CAT-079,AC-CAT-080,AC-CAT-085,AC-CAT-086,AC-CAT-088,AC-CAT-094,AC-CAT-095,AC-CAT-097,AC-CAT-098,AC-CAT-099,AC-CAT-103,AC-CAT-104,AC-CAT-107,AC-CAT-109,AC-CAT-111,AC-CAT-117,AC-CAT-119,AC-CAT-120,AC-CAT-121,AC-CAT-122,AC-CAT-123,AC-CAT-124,AC-CAT-127,AC-CAT-129,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-008,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-017,REQ-CAT-020,REQ-CAT-021,REQ-CAT-024,REQ-CAT-025,REQ-CAT-028,REQ-CAT-029,REQ-CAT-032,REQ-CAT-033,REQ-CAT-036,REQ-CAT-038,REQ-CAT-039,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-077,REQ-CAT-078,REQ-CAT-083,REQ-CAT-084,REQ-CAT-086,REQ-CAT-092,REQ-CAT-093,REQ-CAT-095,REQ-CAT-096,REQ-CAT-097,REQ-CAT-101,REQ-CAT-102,REQ-CAT-105,REQ-CAT-107,REQ-CAT-109,REQ-CAT-115,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-125,REQ-CAT-127,AC-CAT-152,AC-CAT-076,REQ-CAT-074 -->
### SUB API-SCENARIOS

<!-- TC:TC-CAT-065:START traces=AC-CAT-001,REQ-CAT-001,API-CAT-002,API-CAT-038 -->
### TC-CAT-065 — Point-of-sale and storefront reads return the one back-office record
Derived from : AC-CAT-001  (REQ-CAT-001)
Exercises    : API-CAT-002 GET /api/v1/cat/items/{id} (also behind `CatalogueReadApi.getItem`, ADR-CAT-045) · API-CAT-038 GET /api/v1/cat/items
Rule / code  : — 
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an Item with code ITM-000001 created from the back-office through API-CAT-001 (series CAT_ITEM unused before); its back-office read (API-CAT-002) recorded
Host data    : none
Steps        : 1. Read the Item by itemPk as the point of sale (a Seller/cashier calling API-CAT-002). 2. Read it by itemPk as the storefront (in-process `CatalogueReadApi.getItem`, same service as API-CAT-002). 3. GET /api/v1/cat/items?text=ITM-000001.
Expected     : 1 and 2 → the same itemPk, code ITM-000001 and nameAr as the back-office read. 3 → totalElements 1: the tenant holds exactly 1 Item record with that code.
Test data    : ITM-000001 (from the AC); nameAr placeholder `<valid Arabic name>`
<!-- TC:TC-CAT-065:END -->

<!-- TC:TC-CAT-066:START traces=AC-CAT-002,REQ-CAT-002,API-CAT-002 -->
### TC-CAT-066 — Reading an Item returns only its active Variants with units and barcodes
Derived from : AC-CAT-002  (REQ-CAT-002)
Exercises    : API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an Item with 2 active Variants and 1 inactive Variant (generated through API-CAT-006, one deactivated through API-CAT-009), 1 active Item unit (API-CAT-010) and 3 active barcodes on its active Variants (API-CAT-014)
Host data    : none
Steps        : 1. GET the Item by itemPk.
Expected     : 200; data holds the Item, exactly the 2 active Variants in `variants`, the 1 Item unit in `itemUnits`, and the 3 barcodes across `variants[].barcodes`.
Test data    : counts 2 / 1 / 1 / 3 (from the AC); barcode values placeholders `<valid GTIN-1..3>`
<!-- TC:TC-CAT-066:END -->

<!-- TC:TC-CAT-067:START traces=AC-CAT-003,REQ-CAT-003,API-CAT-038 -->
### TC-CAT-067 — Searching Items by Category returns a sorted page
Derived from : AC-CAT-003  (REQ-CAT-003)
Exercises    : API-CAT-038 GET /api/v1/cat/items
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · 30 active Items, of which 12 belong to the Category «هواتف»
Host data    : none
Steps        : 1. GET /api/v1/cat/items?categoryFk=<«هواتف»>&page=0&size=10.
Expected     : 200; the first page holds 10 Items, totalElements 12, and the Items are sorted by nameAr ascending.
Test data    : 30 / 12 Items, Category «هواتف», page size 10 (from the AC); Item names are placeholders `<name-1..30>`
<!-- TC:TC-CAT-067:END -->

<!-- TC:TC-CAT-068:START traces=AC-CAT-004,REQ-CAT-004,API-CAT-003,API-CAT-002 -->
### TC-CAT-068 — Setting the brand of an Item is saved and audited
Derived from : AC-CAT-004  (REQ-CAT-004)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an active Item with an empty brandCode
Host data    : CAT_BRAND BRAND_068 — created active by the given mdl call POST /api/v1/mdl/lookup-types/{id}/values ({id} = the CAT_BRAND type found by POST /api/v1/mdl/lookup-types/search)
Steps        : 1. PUT the Item with its current fields and version and brandCode BRAND_068. 2. GET the Item.
Expected     : 1 → 200. 2 → brandCode BRAND_068 and updatedBy = the owner's user identifier.
Test data    : the AC names no brand value: BRAND_068 is this case's own host value (`<VALUE>_<TC seq>`)
<!-- TC:TC-CAT-068:END -->

<!-- TC:TC-CAT-069:START traces=AC-CAT-008,REQ-CAT-008,API-CAT-003 -->
### TC-CAT-069 — A committed nameEn change publishes exactly one UPDATED event
Derived from : AC-CAT-008  (REQ-CAT-008)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-049 (event recorded in the change transaction, delivered after commit) · —
Package      : CORE
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an active Item · the given outbox is observed
Host data    : none
Steps        : 1. PUT the Item with its current fields and version and a new nameEn. 2. Collect the cat.CatalogueChanged events delivered after the commit.
Expected     : 1 → 200. 2 → exactly 1 event, carrying entity ENT-CAT-001, recordPk = itemPk = the Item's itemPk and changeKind UPDATED.
Test data    : new nameEn placeholder `<English name ≤ 200>`
<!-- TC:TC-CAT-069:END -->

<!-- TC:TC-CAT-070:START traces=AC-CAT-010,REQ-CAT-010,API-CAT-003,API-CAT-002 -->
### TC-CAT-070 — Every channel reads the committed change
Derived from : AC-CAT-010  (REQ-CAT-010)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id} (also `CatalogueReadApi.getItem`)
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an Item with nameEn "Charger"
Host data    : none
Steps        : 1. PUT nameEn "Charger 25W" and wait for the commit (200). 2. Point-of-sale read (Seller/cashier, API-CAT-002) after the commit. 3. Storefront read (`CatalogueReadApi.getItem`) after the commit.
Expected     : 2 and 3 → nameEn "Charger 25W".
Test data    : "Charger", "Charger 25W" (from the AC)
<!-- TC:TC-CAT-070:END -->

<!-- TC:TC-CAT-071:START traces=AC-CAT-011,REQ-CAT-011,API-CAT-001 -->
### TC-CAT-071 — Behaviour depends on the capability flags, not on the activity
Derived from : AC-CAT-011  (REQ-CAT-011)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : —
Package      : CORE
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 for two tenants T1 and T2 whose activity presets differ (e.g. electronics and apparel) and whose `variants` and `bundles` values are equal
Host data    : none
Steps        : 1. In T1, POST an Item with entries E. 2. In T2, POST an Item with the same entries E.
Expected     : both 201; the two ItemDetail responses are equal in every field except itemPk, variantPk, code and the audit fields (createdBy, createdAt, updatedBy, updatedAt).
Test data    : entries E = placeholder `<nameAr, nameEn, baseUomCode PIECE, taxClassCode STANDARD>`
<!-- TC:TC-CAT-071:END -->

<!-- TC:TC-CAT-072:START traces=AC-CAT-012,REQ-CAT-012,API-CAT-025 -->
### TC-CAT-072 — Turning `variants` on takes effect on the next request
Derived from : AC-CAT-012  (REQ-CAT-012)
Exercises    : API-CAT-025 POST /api/v1/cat/variant-attributes
Rule / code  : —
Package      : CORE
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0, except that the `variants` flag is off
Host data    : none
Steps        : 1. Turn `variants` on in the given core settings (no CAT call, no restart). 2. In the next request, POST a Variant attribute.
Expected     : 2 → 201; the attribute is created and returned with isActiveFl true.
Test data    : attribute placeholder `<code, Arabic name>`
<!-- TC:TC-CAT-072:END -->

<!-- TC:TC-CAT-073:START traces=AC-CAT-018,REQ-CAT-017,API-CAT-002 -->
### TC-CAT-073 — Turning `variants` on changes no existing Item
Derived from : AC-CAT-018  (REQ-CAT-017)
Exercises    : API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0, except that `variants` is off · 200 Items, each with only its default Variant and the barcodes it carries · a snapshot of each Item read (itemPk, code, updatedAt, variants[].variantPk, code, version, barcodes)
Host data    : none
Steps        : 1. Turn `variants` on in the given core settings. 2. GET each of the 200 Items.
Expected     : every Item returns the same itemPk, variantPk, codes and barcodes as in the snapshot; 0 Items change their updatedAt and 0 Variants change their version (the Variant views expose no updatedAt — ADR-CAT-052 §5).
Test data    : 200 Items (from the AC; names placeholders)
<!-- TC:TC-CAT-073:END -->

<!-- TC:TC-CAT-074:START traces=AC-CAT-021,REQ-CAT-020,API-CAT-001,API-CAT-007 -->
### TC-CAT-074 — A Variant made while `bundles` was off becomes a Bundle component unchanged
Derived from : AC-CAT-021  (REQ-CAT-020)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0, except that `bundles` is off at the start · an active STANDARD Item created then, with Variant V; V's read (API-CAT-007) recorded
Host data    : none
Steps        : 1. Turn `bundles` on in the given core settings. 2. POST a new Bundle (itemKindCode BUNDLE) with bundleComponents [{componentVariantFk: V, quantity: 1}]. 3. GET V.
Expected     : 2 → 201; the Bundle is saved with 1 component line. 3 → V's version is unchanged from the recorded read: its updatedAt did not move (ADR-CAT-052 §5).
Test data    : Bundle nameAr placeholder `<valid Arabic name>`
<!-- TC:TC-CAT-074:END -->

<!-- TC:TC-CAT-075:START traces=AC-CAT-022,REQ-CAT-021,API-CAT-051 -->
### TC-CAT-075 — Variants turn-off is allowed when no Item uses attributes
Derived from : AC-CAT-022  (REQ-CAT-021)
Exercises    : API-CAT-051 GET /api/v1/cat/capability-checks/turn-off?flag=variants
Rule / code  : RULE-CAT-010 (not raised) · —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · no active Item uses Variant attributes
Host data    : none
Steps        : 1. GET the turn-off check with flag=variants.
Expected     : 200; data.allowed true, data.dependentCount 0, data.dependentItems empty.
Test data    : —
<!-- TC:TC-CAT-075:END -->

<!-- TC:TC-CAT-076:START traces=AC-CAT-025,REQ-CAT-024,API-CAT-001 -->
### TC-CAT-076 — An Item created from its Arabic name only takes the defaults
Derived from : AC-CAT-025  (REQ-CAT-024)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · no active Item named «كوب زجاج»
Host data    : none
Steps        : 1. POST an Item with nameAr «كوب زجاج» and no other entry (bundleComponents []).
Expected     : 201; itemKindCode STANDARD, baseUomCode PIECE, taxClassCode STANDARD, publishStateCode UNPUBLISHED and isActiveFl true.
Test data    : «كوب زجاج» (from the AC)
<!-- TC:TC-CAT-076:END -->

<!-- TC:TC-CAT-077:START traces=AC-CAT-026,REQ-CAT-025,API-CAT-001,API-CAT-002 -->
### TC-CAT-077 — A new Item gets exactly one default Variant
Derived from : AC-CAT-026  (REQ-CAT-025)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · no Item named «كوب زجاج» exists
Host data    : none
Steps        : 1. POST an Item with nameAr «كوب زجاج». 2. GET it.
Expected     : 2 → exactly 1 Variant, with isDefaultFl true, isActiveFl true and a code from the series CAT_VARIANT (pattern VAR-000000).
Test data    : «كوب زجاج» (from the AC)
<!-- TC:TC-CAT-077:END -->

<!-- TC:TC-CAT-078:START traces=AC-CAT-029,REQ-CAT-028,API-CAT-001,API-CAT-002 -->
### TC-CAT-078 — A mixed Arabic-Latin name is stored as written
Derived from : AC-CAT-029  (REQ-CAT-028)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-001 (passes) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: S0
Host data    : none
Steps        : 1. POST an Item with nameAr «سامسونج Galaxy A55 128GB». 2. GET it.
Expected     : 2 → nameAr «سامسونج Galaxy A55 128GB», character for character.
Test data    : «سامسونج Galaxy A55 128GB» (from the AC)
<!-- TC:TC-CAT-078:END -->

<!-- TC:TC-CAT-079:START traces=AC-CAT-030,REQ-CAT-029,API-CAT-002 -->
### TC-CAT-079 — The English display name falls back to the Arabic name
Derived from : AC-CAT-030  (REQ-CAT-029)
Exercises    : API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : CORE
Scenario     : HAPPY · data class EDGE · language en
Preconditions: S0 · an Item with nameAr «كوب زجاج» and an empty nameEn
Host data    : none
Steps        : 1. GET the Item.
Expected     : 200; displayNameEn «كوب زجاج».
Test data    : «كوب زجاج» (from the AC)
<!-- TC:TC-CAT-079:END -->

<!-- TC:TC-CAT-080:START traces=AC-CAT-033,REQ-CAT-032,API-CAT-001 -->
### TC-CAT-080 — The Item code is the next code of series CAT_ITEM
Derived from : AC-CAT-033  (REQ-CAT-032)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-004 (system-assigned) · —
Package      : CORE
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the last code issued by series CAT_ITEM is ITM-000041
Host data    : none
Steps        : 1. POST a new Item with a valid nameAr and no code.
Expected     : 201; code ITM-000042.
Test data    : ITM-000041 → ITM-000042 (from the AC)
<!-- TC:TC-CAT-080:END -->

<!-- TC:TC-CAT-081:START traces=AC-CAT-034,REQ-CAT-033,API-CAT-001 -->
### TC-CAT-081 — The Variant code is the next code of series CAT_VARIANT
Derived from : AC-CAT-034  (REQ-CAT-033)
Exercises    : API-CAT-001 POST /api/v1/cat/items (creates the default Variant)
Rule / code  : RULE-CAT-004 (system-assigned) · —
Package      : CORE
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the last code issued by series CAT_VARIANT is VAR-000107
Host data    : none
Steps        : 1. POST a new Item, which creates its default Variant.
Expected     : 201; variants[0].code VAR-000108.
Test data    : VAR-000107 → VAR-000108 (from the AC)
<!-- TC:TC-CAT-081:END -->

<!-- TC:TC-CAT-082:START traces=AC-CAT-037,REQ-CAT-036,API-CAT-048 -->
### TC-CAT-082 — The import sheet has 11 bilingual columns
Derived from : AC-CAT-037  (REQ-CAT-036)
Exercises    : API-CAT-048 GET /api/v1/cat/item-imports/sheet
Rule / code  : —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the setup wizard's item-import step (any Owner call to the endpoint)
Host data    : none
Steps        : 1. GET the import sheet.
Expected     : 200, content type application/vnd.openxmlformats-officedocument.spreadsheetml.sheet; 1 sheet whose header row holds 11 columns — nameAr, nameEn, baseUomCode, category code, brandCode, taxClassCode, einvoiceCodeTypeCode, einvoiceCode, GTIN barcode, descriptionAr, descriptionEn — each labelled in ar and en.
Test data    : —
<!-- TC:TC-CAT-082:END -->

<!-- TC:TC-CAT-083:START traces=AC-CAT-039,REQ-CAT-038,API-CAT-049,API-CAT-038,API-CAT-002 -->
### TC-CAT-083 — Valid import rows create Items, default Variants and GTIN barcodes; empty unit and tax cells take the defaults
Derived from : AC-CAT-039  (REQ-CAT-038)
Exercises    : API-CAT-049 POST /api/v1/cat/item-imports · API-CAT-038 GET /api/v1/cat/items · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-006 (a filled baseUomCode / taxClassCode cell is validated, never overridden) · D-09 defaults on empty cells
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an xlsx on the import sheet with 49 valid rows, 20 of which carry a GTIN (valid, unused, not beginning with an active scale prefix) · rows 2 and 3 («صنف فارغ 1», «صنف فارغ 2») leave the baseUomCode and taxClassCode cells empty; row 4 («صنف ممتلئ») carries baseUomCode KILOGRAM and taxClassCode ZERO_RATED (both seeded active values)
Host data    : none
Steps        : 1. POST the file. 2. GET /api/v1/cat/items and GET /api/v1/cat/variants. 3. GET the Items of rows 2, 3 and 4 (found by GET /api/v1/cat/items?text=<nameAr>).
Expected     : 1 → 200; data.itemsCreated 49, data.rowsRejected 0. 2 → 49 Items, 49 default Variants; 20 GTIN barcodes exist (20 of the Variants carry a firstBarcodeValue). 3 → the Items of rows 2 and 3 have baseUomCode PIECE and taxClassCode STANDARD (empty cells take the D-09 defaults); the Item of row 4 has baseUomCode KILOGRAM and taxClassCode ZERO_RATED (a filled cell is kept, not overridden by the default) — MODIFIED at the seventh gate-analysis revise (G6).
Test data    : 49 rows / 20 GTIN (from the AC); «صنف فارغ 1», «صنف فارغ 2», «صنف ممتلئ», KILOGRAM, ZERO_RATED (this case's own); other names and GTINs are placeholders
<!-- TC:TC-CAT-083:END -->

<!-- TC:TC-CAT-084:START traces=AC-CAT-040,REQ-CAT-039,API-CAT-049 -->
### TC-CAT-084 — The import result counts rows and explains the rejected row
Derived from : AC-CAT-040  (REQ-CAT-039)
Exercises    : API-CAT-049 POST /api/v1/cat/item-imports
Rule / code  : RULE-CAT-045 → CAT-200-IMPORT-ROW-REJECTED (row message)
Package      : SVC-API-INT
Scenario     : HAPPY · data class INVALID · language ALL
Preconditions: S0 · an xlsx on the import sheet with 50 data rows; row 7 has an empty nameAr; the other rows are valid
Host data    : none
Steps        : 1. POST the file.
Expected     : 200; data.rowsRead 50, data.itemsCreated 49, data.rowsRejected 1; data.rejectedRows[0] has rowNumber 7, column nameAr, messageAr «الصف 7، العمود nameAr: أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل. صحّح الصف وأعد استيراده.», messageEn "Row 7, column nameAr: Enter the Arabic name (up to 200 characters) with at least one Arabic letter. Correct the row and import it again.".
Test data    : 50 rows, row 7 (from the AC)
<!-- TC:TC-CAT-084:END -->

<!-- TC:TC-CAT-085:START traces=AC-CAT-044,REQ-CAT-042,API-CAT-025 -->
### TC-CAT-085 — Creating the attribute SIZE
Derived from : AC-CAT-044  (REQ-CAT-042)
Exercises    : API-CAT-025 POST /api/v1/cat/variant-attributes
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 (`variants` on) · no attribute with code SIZE exists, and the tenant has no Variant attribute (so the next sortOrder is 1)
Host data    : none
Steps        : 1. POST {code: SIZE, nameAr: «المقاس»}.
Expected     : 201; isActiveFl true and sortOrder 1.
Test data    : SIZE, «المقاس» (from the AC)
<!-- TC:TC-CAT-085:END -->

<!-- TC:TC-CAT-086:START traces=AC-CAT-045,REQ-CAT-043,API-CAT-027,API-CAT-026,API-CAT-007 -->
### TC-CAT-086 — An attribute's English name is saved and shown in Variant names
Derived from : AC-CAT-045  (REQ-CAT-043)
Exercises    : API-CAT-027 PUT /api/v1/cat/variant-attributes/{id} · API-CAT-026 GET /api/v1/cat/variant-attributes/{id} · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language en
Preconditions: S0 · the attribute SIZE with nameEn empty; its values carry nameEn; at least one active Variant carries a SIZE value
Host data    : none
Steps        : 1. PUT SIZE with its nameAr, sortOrder and version and nameEn "Size". 2. GET SIZE. 3. GET each Variant carrying a SIZE value.
Expected     : 2 → nameEn "Size". 3 → the derived nameEn of each such Variant includes its SIZE value's English name.
Test data    : "Size" (from the AC)
<!-- TC:TC-CAT-086:END -->

<!-- TC:TC-CAT-087:START traces=AC-CAT-046,REQ-CAT-044,API-CAT-028,API-CAT-041 -->
### TC-CAT-087 — A deactivated attribute leaves Variant generation
Derived from : AC-CAT-046  (REQ-CAT-044)
Exercises    : API-CAT-028 DELETE /api/v1/cat/variant-attributes/{id} · API-CAT-041 GET /api/v1/cat/variant-attributes
Rule / code  : RULE-CAT-017 (not raised: 0 uses) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · the attribute MATERIAL_TYPE, with values, used by 0 active Variants
Host data    : none
Steps        : 1. DELETE MATERIAL_TYPE. 2. GET /api/v1/cat/variant-attributes?isActiveFl=true (the generation list).
Expected     : 1 → 200, isActiveFl false. 2 → MATERIAL_TYPE is absent, so the generation list offers 0 values of that attribute.
Test data    : MATERIAL_TYPE (from the AC)
<!-- TC:TC-CAT-087:END -->

<!-- TC:TC-CAT-088:START traces=AC-CAT-047,REQ-CAT-045,API-CAT-029,API-CAT-026 -->
### TC-CAT-088 — A third value under SIZE gets sortOrder 3
Derived from : AC-CAT-047  (REQ-CAT-045)
Exercises    : API-CAT-029 POST /api/v1/cat/variant-attributes/{id}/values · API-CAT-026 GET /api/v1/cat/variant-attributes/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the active attribute SIZE with values S and M
Host data    : none
Steps        : 1. POST under SIZE {code: L, nameAr: «كبير»}. 2. GET SIZE.
Expected     : 1 → 201. 2 → L is listed under SIZE with isActiveFl true and sortOrder 3.
Test data    : S, M, L «كبير» (from the AC)
<!-- TC:TC-CAT-088:END -->

<!-- TC:TC-CAT-089:START traces=AC-CAT-048,REQ-CAT-046,API-CAT-030,API-CAT-007 -->
### TC-CAT-089 — Renaming a value renames every Variant that carries it
Derived from : AC-CAT-048  (REQ-CAT-046)
Exercises    : API-CAT-030 PUT /api/v1/cat/variant-attribute-values/{id} · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ar
Preconditions: S0 · the value L with nameAr «كبير», carried by active Variants (L is the value of the last attribute in sortOrder on those Variants)
Host data    : none
Steps        : 1. PUT L with nameAr «لارج L» and its sortOrder and version. 2. GET each Variant carrying L.
Expected     : 2 → each derived nameAr ends in «لارج L».
Test data    : «كبير» → «لارج L» (from the AC)
<!-- TC:TC-CAT-089:END -->

<!-- TC:TC-CAT-090:START traces=AC-CAT-049,REQ-CAT-047,API-CAT-031,API-CAT-041 -->
### TC-CAT-090 — A deactivated value leaves Variant generation
Derived from : AC-CAT-049  (REQ-CAT-047)
Exercises    : API-CAT-031 DELETE /api/v1/cat/variant-attribute-values/{id} · API-CAT-041 GET /api/v1/cat/variant-attributes
Rule / code  : RULE-CAT-017 (not raised: 0 uses) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · the value XS of SIZE, used by 0 active Variants
Host data    : none
Steps        : 1. DELETE XS. 2. GET /api/v1/cat/variant-attributes?isActiveFl=true.
Expected     : 1 → 200, isActiveFl false. 2 → the values offered for SIZE in generation (its active values) omit XS.
Test data    : XS (from the AC)
<!-- TC:TC-CAT-090:END -->

<!-- TC:TC-CAT-091:START traces=AC-CAT-050,REQ-CAT-048,API-CAT-041 -->
### TC-CAT-091 — Variant attributes are listed by sortOrder with their values
Derived from : AC-CAT-050  (REQ-CAT-048)
Exercises    : API-CAT-041 GET /api/v1/cat/variant-attributes
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · exactly 2 attributes: SIZE (sortOrder 1, 3 values) and COLOUR (sortOrder 2, 4 values)
Host data    : none
Steps        : 1. GET the list.
Expected     : 200; SIZE then COLOUR, with 3 and 4 values respectively, each value list ordered by sortOrder.
Test data    : SIZE, COLOUR (from the AC); value codes placeholders
<!-- TC:TC-CAT-091:END -->

<!-- TC:TC-CAT-092:START traces=AC-CAT-051,REQ-CAT-049,API-CAT-006,API-CAT-002 -->
### TC-CAT-092 — Generating Variants from two attributes
Derived from : AC-CAT-051  (REQ-CAT-049)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-012, RULE-CAT-013 (satisfied) · —
Package      : DATA-DOM-MASTER
Scenario     : HAPPY · data class VALID · language ar
Preconditions: S0 (`variants` on) · the STANDARD Item «قميص قطن» with only its default Variant · SIZE (sortOrder 1) with values M, L and COLOUR (sortOrder 2) with values «أحمر», «أزرق», all active
Host data    : none
Steps        : 1. POST a generation with selections SIZE [M, L] and COLOUR [«أحمر», «أزرق»]. 2. GET the Item.
Expected     : 2 → 4 active Variants, each with a distinct combination; the default Variant carries M / «أحمر»; one derived nameAr reads «قميص قطن / L / أزرق».
Test data    : «قميص قطن», M, L, «أحمر», «أزرق» (from the AC)
<!-- TC:TC-CAT-092:END -->

<!-- TC:TC-CAT-093:START traces=AC-CAT-058,REQ-CAT-056,API-CAT-001 -->
### TC-CAT-093 — Saving a Bundle with its two components
Derived from : AC-CAT-058  (REQ-CAT-056)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-018, RULE-CAT-019 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 (`bundles` on) · 2 active STANDARD Items, a phone (Variant P) and a case (Variant C)
Host data    : none
Steps        : 1. POST {nameAr: «باقة الهاتف مع الجراب», itemKindCode: BUNDLE, bundleComponents: [{componentVariantFk: P, quantity: 1}, {componentVariantFk: C, quantity: 1}]}.
Expected     : 201; itemKindCode BUNDLE, 1 default Variant, and 2 active component lines of quantity 1.
Test data    : «باقة الهاتف مع الجراب», phone × 1, case × 1 (from the AC)
<!-- TC:TC-CAT-093:END -->

<!-- TC:TC-CAT-094:START traces=AC-CAT-059,REQ-CAT-057,API-CAT-017,API-CAT-002 -->
### TC-CAT-094 — Changing a component quantity
Derived from : AC-CAT-059  (REQ-CAT-057)
Exercises    : API-CAT-017 PUT /api/v1/cat/items/{id}/bundle-components · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · a Bundle with 2 component lines of quantity 1
Host data    : none
Steps        : 1. PUT the complete line set with the second line's quantity 2 and the Bundle's version. 2. GET the Bundle.
Expected     : 1 → 200. 2 → 2 active lines with quantities 1 and 2.
Test data    : quantities 1, 1 → 1, 2 (from the AC)
<!-- TC:TC-CAT-094:END -->

<!-- TC:TC-CAT-095:START traces=AC-CAT-060,REQ-CAT-058,API-CAT-002 -->
### TC-CAT-095 — A Bundle read returns its component lines
Derived from : AC-CAT-060  (REQ-CAT-058)
Exercises    : API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · a Bundle with 3 active component lines
Host data    : none
Steps        : 1. GET the Bundle Item (as any caller with PERM_CAT_ITEMS_VIEW).
Expected     : 200; bundleComponents holds 3 lines, each with componentVariantFk, componentVariantCode (the Variant code), quantity and componentBaseUomCode (the base unit of the component Variant's Item — ADR-CAT-080; MODIFIED at the eleventh gate-analysis revise, G2).
Test data    : 3 lines (from the AC); component Variants placeholders
<!-- TC:TC-CAT-095:END -->

<!-- TC:TC-CAT-096:START traces=AC-CAT-066,REQ-CAT-064,API-CAT-021 -->
### TC-CAT-096 — Creating the sub-category PHONES
Derived from : AC-CAT-066  (REQ-CAT-064)
Exercises    : API-CAT-021 POST /api/v1/cat/categories
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the active top-level Category «إلكترونيات»
Host data    : none
Steps        : 1. POST {code: PHONES, nameAr: «هواتف», parentCategoryFk: «إلكترونيات»}.
Expected     : 201; Category PHONES with isActiveFl true and parentCategoryFk pointing to «إلكترونيات».
Test data    : PHONES, «هواتف», «إلكترونيات» (from the AC)
<!-- TC:TC-CAT-096:END -->

<!-- TC:TC-CAT-097:START traces=AC-CAT-067,REQ-CAT-065,API-CAT-023,API-CAT-002 -->
### TC-CAT-097 — Moving a Category to the top level keeps its Items
Derived from : AC-CAT-067  (REQ-CAT-065)
Exercises    : API-CAT-023 PUT /api/v1/cat/categories/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the Category PHONES under «إلكترونيات», holding 3 active Items
Host data    : none
Steps        : 1. PUT PHONES with its names, sortOrder and version and an empty parentCategoryFk. 2. GET one of its Items.
Expected     : 1 → 200; parentCategoryFk empty. 2 → categoryFk still PHONES.
Test data    : PHONES (from the AC)
<!-- TC:TC-CAT-097:END -->

<!-- TC:TC-CAT-098:START traces=AC-CAT-068,REQ-CAT-066,API-CAT-024,API-CAT-039,API-CAT-022 -->
### TC-CAT-098 — A deactivated Category leaves the selection list and stays readable
Derived from : AC-CAT-068  (REQ-CAT-066)
Exercises    : API-CAT-024 DELETE /api/v1/cat/categories/{id} · API-CAT-039 GET /api/v1/cat/categories · API-CAT-022 GET /api/v1/cat/categories/{id}
Rule / code  : RULE-CAT-048 → CAT-200-DEACTIVATED
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · a Category with 0 active sub-categories and 0 active Items
Host data    : none
Steps        : 1. DELETE the Category. 2. GET /api/v1/cat/categories?isActiveFl=true (the Item entry's Category list). 3. GET the Category by categoryPk.
Expected     : 1 → 200, isActiveFl false. 2 → the Category is absent. 3 → 200, the Category is returned.
Test data    : —
<!-- TC:TC-CAT-098:END -->

<!-- TC:TC-CAT-099:START traces=AC-CAT-069,REQ-CAT-067,API-CAT-039 -->
### TC-CAT-099 — The Category tree nests children under their parents
Derived from : AC-CAT-069  (REQ-CAT-067)
Exercises    : API-CAT-039 GET /api/v1/cat/categories
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · exactly 2 top-level Categories, the one with the lower sortOrder having 3 sub-categories and the other 0
Host data    : none
Steps        : 1. GET the tree.
Expected     : 200; 2 root nodes ordered by sortOrder, the first with 3 children.
Test data    : 2 roots, 3 / 0 children (from the AC); codes placeholders
<!-- TC:TC-CAT-099:END -->

<!-- TC:TC-CAT-100:START traces=AC-CAT-070,REQ-CAT-068,API-CAT-040 -->
### TC-CAT-100 — Items of a Category include its active sub-categories
Derived from : AC-CAT-070  (REQ-CAT-068)
Exercises    : API-CAT-040 GET /api/v1/cat/categories/{id}/items
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · «إلكترونيات» holding 2 active Items; its sub-category PHONES holding 5 active Items and 1 inactive Item
Host data    : none
Steps        : 1. GET the Items of «إلكترونيات» (page 0, size ≥ 7).
Expected     : 200; totalElements 7.
Test data    : 2 + 5 active, 1 inactive (from the AC)
<!-- TC:TC-CAT-100:END -->

<!-- TC:TC-CAT-101:START traces=AC-CAT-079,REQ-CAT-077,API-CAT-010 -->
### TC-CAT-101 — Adding the unit CARTON of 12
Derived from : AC-CAT-079  (REQ-CAT-077)
Exercises    : API-CAT-010 POST /api/v1/cat/items/{id}/units
Rule / code  : RULE-CAT-027 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an Item with base unit PIECE
Host data    : none
Steps        : 1. POST {uomCode: CARTON, conversionFactor: 12}.
Expected     : 201; Item unit CARTON with conversionFactor 12.000000, isPurchaseFl true, isSalesFl true and isActiveFl true.
Test data    : CARTON, 12 (from the AC)
<!-- TC:TC-CAT-101:END -->

<!-- TC:TC-CAT-102:START traces=AC-CAT-080,REQ-CAT-078,API-CAT-012,API-CAT-011 -->
### TC-CAT-102 — Clearing the sales flag of an Item unit
Derived from : AC-CAT-080  (REQ-CAT-078)
Exercises    : API-CAT-012 PUT /api/v1/cat/item-units/{id} · API-CAT-011 GET /api/v1/cat/item-units/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the Item unit CARTON with isSalesFl true, isPurchaseFl true, conversionFactor 12
Host data    : none
Steps        : 1. PUT {isPurchaseFl: true, isSalesFl: false, version}. 2. GET the unit.
Expected     : 2 → isSalesFl false, isPurchaseFl true and conversionFactor unchanged (12.000000).
Test data    : CARTON (from the AC)
<!-- TC:TC-CAT-102:END -->

<!-- TC:TC-CAT-103:START traces=AC-CAT-085,REQ-CAT-083,API-CAT-014 -->
### TC-CAT-103 — A GTIN added with no unit binds to the base unit
Derived from : AC-CAT-085  (REQ-CAT-083)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an active Variant with 0 barcodes, its Item's base unit PIECE · no active scale label layout
Host data    : none
Steps        : 1. POST {barcodeValue: 6223000000014, barcodeTypeCode: GTIN} (no uomCode).
Expected     : 201; the Variant has 1 active barcode with barcodeTypeCode GTIN and uomCode PIECE.
Test data    : 6223000000014 (from the AC)
<!-- TC:TC-CAT-103:END -->

<!-- TC:TC-CAT-104:START traces=AC-CAT-086,REQ-CAT-084,API-CAT-015 -->
### TC-CAT-104 — Generating an internal barcode
Derived from : AC-CAT-086  (REQ-CAT-084)
Exercises    : API-CAT-015 POST /api/v1/cat/variants/{id}/barcodes/internal
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · series CAT_INTERNAL_BARCODE last issued 0000000041 · an active Variant · no active layout with prefix 04
Host data    : none
Steps        : 1. POST the internal barcode request (no uomCode).
Expected     : 201; the Variant gets the active INTERNAL barcode 0400000000428 (04 + 0000000042 + check digit 8).
Test data    : 0000000041 → 0400000000428 (from the AC)
<!-- TC:TC-CAT-104:END -->

<!-- TC:TC-CAT-105:START traces=AC-CAT-088,REQ-CAT-086,API-CAT-043 -->
### TC-CAT-105 — Scanning a carton barcode returns the Variant and the carton unit
Derived from : AC-CAT-088  (REQ-CAT-086)
Exercises    : API-CAT-043 GET /api/v1/cat/barcodes/lookup
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the Variant VAR-000200 of an Item with the active Item unit CARTON · the active barcode 16223000000011 bound to unit CARTON of VAR-000200 · the caller is a Seller/cashier
Host data    : none
Steps        : 1. GET the lookup with value 16223000000011.
Expected     : 200; variantCode VAR-000200, its Item (itemPk, itemCode, itemNameAr) and uomCode CARTON.
Test data    : 16223000000011, VAR-000200, CARTON (from the AC)
<!-- TC:TC-CAT-105:END -->

<!-- TC:TC-CAT-106:START traces=AC-CAT-094,REQ-CAT-092,API-CAT-032 -->
### TC-CAT-106 — Creating the scale label layout 20
Derived from : AC-CAT-094  (REQ-CAT-092)
Exercises    : API-CAT-032 POST /api/v1/cat/scale-label-layouts
Rule / code  : RULE-CAT-034 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · no active scale label layout · no active GTIN / INTERNAL barcode beginning with 20
Host data    : none
Steps        : 1. POST {key: 20, scaleCodeLength: 5, embeddedValueTypeCode: WEIGHT, valueDecimals: 3}.
Expected     : 201; the layout is created with isActiveFl true.
Test data    : 20, 5, WEIGHT, 3 (from the AC)
<!-- TC:TC-CAT-106:END -->

<!-- TC:TC-CAT-107:START traces=AC-CAT-095,REQ-CAT-093,API-CAT-033,API-CAT-045 -->
### TC-CAT-107 — Changing a layout to price with 2 decimals
Derived from : AC-CAT-095  (REQ-CAT-093)
Exercises    : API-CAT-033 PUT /api/v1/cat/scale-label-layouts/{id} · API-CAT-045 GET /api/v1/cat/settings
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the layout with prefix 20 and embeddedValueTypeCode WEIGHT
Host data    : none
Steps        : 1. PUT {embeddedValueTypeCode: PRICE, valueDecimals: 2, version} (the update carries no key and no scaleCodeLength — ADR-CAT-064). 2. GET the settings.
Expected     : 2 → the layout 20 returns PRICE and 2, with its scaleCodeLength unchanged.
Test data    : WEIGHT → PRICE, 3 → 2 (from the AC)
<!-- TC:TC-CAT-107:END -->

<!-- TC:TC-CAT-108:START traces=AC-CAT-097,REQ-CAT-095,API-CAT-045 -->
### TC-CAT-108 — The catalogue settings list every layout and Unit e-invoice code
Derived from : AC-CAT-097  (REQ-CAT-095)
Exercises    : API-CAT-045 GET /api/v1/cat/settings
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · 1 active scale label layout · 8 active and 1 inactive Unit e-invoice codes (the seeded PIECE → EA deactivated through API-CAT-037, then PIECE → EA created again through API-CAT-035)
Host data    : none
Steps        : 1. GET the settings.
Expected     : 200; 1 layout and 9 Unit e-invoice codes, each with its isActiveFl (8 true, 1 false).
Test data    : counts 1 / 8 / 1 (from the AC); the inactive row is built from seeded values only
<!-- TC:TC-CAT-108:END -->

<!-- TC:TC-CAT-109:START traces=AC-CAT-098,REQ-CAT-096,API-CAT-014 -->
### TC-CAT-109 — Adding the scale code 01230 to a Variant
Derived from : AC-CAT-098  (REQ-CAT-096)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes
Rule / code  : RULE-CAT-033 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · 1 active scale label layout with scaleCodeLength 5 · an active Variant
Host data    : none
Steps        : 1. POST {barcodeValue: 01230, barcodeTypeCode: SCALE}.
Expected     : 201; the Variant has 1 active SCALE barcode 01230.
Test data    : SCALE 01230 (from the AC)
<!-- TC:TC-CAT-109:END -->

<!-- TC:TC-CAT-110:START traces=AC-CAT-099,REQ-CAT-097,API-CAT-043 -->
### TC-CAT-110 — Decoding a weight scale label
Derived from : AC-CAT-099  (REQ-CAT-097)
Exercises    : API-CAT-043 GET /api/v1/cat/barcodes/lookup
Rule / code  : RULE-CAT-035 (satisfied) · —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the active layout prefix 20, scaleCodeLength 5, WEIGHT, valueDecimals 3 · a Variant with the SCALE barcode 01230 · the caller is a Seller/cashier
Host data    : none
Steps        : 1. GET the lookup with value 2001230012342.
Expected     : 200; that Variant, embeddedValue 1.234, embeddedValueTypeCode WEIGHT, uomCode = the Item's base unit.
Test data    : 2001230012342 (prefix 20, code 01230, value 01234, check digit 2 — from the AC)
<!-- TC:TC-CAT-110:END -->

<!-- TC:TC-CAT-111:START traces=AC-CAT-103,REQ-CAT-101,API-CAT-042 -->
### TC-CAT-111 — Finding Variants for labels by Item name
Derived from : AC-CAT-103  (REQ-CAT-101)
Exercises    : API-CAT-042 GET /api/v1/cat/variants
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0, with the caller a Warehouse staff user (PERM_CAT_BARCODE_LABELS_VIEW) · exactly 3 active Variants whose Item nameAr contains «قميص»; at least one has no active barcode, and at least one has two
Host data    : none
Steps        : 1. GET /api/v1/cat/variants?text=قميص&page=0&size=20.
Expected     : 200; 3 rows, each with itemNameAr and firstBarcodeValue = its label barcode, the earliest-created active GTIN or INTERNAL barcode bound to its Item's base unit (empty when it has none — ADR-CAT-079; MODIFIED at the eleventh gate-analysis revise, G1).
Test data    : «قميص», page size 20 (from the AC)
<!-- TC:TC-CAT-111:END -->

<!-- TC:TC-CAT-112:START traces=AC-CAT-104,REQ-CAT-102,API-CAT-050 -->
### TC-CAT-112 — Printing 15 labels for 2 Variants
Derived from : AC-CAT-104  (REQ-CAT-102)
Exercises    : API-CAT-050 POST /api/v1/cat/barcode-labels
Rule / code  : RULE-CAT-036, RULE-CAT-037 (satisfied) · —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0, with the caller a Warehouse staff user · 2 active Variants, each with an active GTIN bound to its Item's base unit
Host data    : none
Steps        : 1. POST {lines: [{variantPk: first, copies: 10}, {variantPk: second, copies: 5}]}.
Expected     : 200, application/pdf; one printable document holding 15 labels, each showing the Variant nameAr, code and barcode.
Test data    : 10 + 5 copies (from the AC)
<!-- TC:TC-CAT-112:END -->

<!-- TC:TC-CAT-113:START traces=AC-CAT-107,REQ-CAT-105,API-CAT-018,API-CAT-002 -->
### TC-CAT-113 — Uploading a third image defaults its description and order
Derived from : AC-CAT-107  (REQ-CAT-105)
Exercises    : API-CAT-018 POST /api/v1/cat/items/{id}/media · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-038 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the Item «كوب زجاج» with 2 active images (sortOrder 1, 2)
Host data    : none
Steps        : 1. Upload a 1 MB PNG through the given FILE endpoint (public image storage) and POST its `fileDocumentRef` with no nameAr and no nameEn. 2. GET the Item.
Expected     : 1 → 201; nameAr «كوب زجاج», sortOrder 3 and a fileDocumentRef from the public image storage. 2 → 3 active media records.
Test data    : «كوب زجاج», 1 MB PNG (from the AC)
<!-- TC:TC-CAT-113:END -->

<!-- TC:TC-CAT-114:START traces=AC-CAT-109,REQ-CAT-107,API-CAT-020,API-CAT-002 -->
### TC-CAT-114 — A removed image leaves every image read
Derived from : AC-CAT-109  (REQ-CAT-107)
Exercises    : API-CAT-020 DELETE /api/v1/cat/media/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an Item with 3 active images (sortOrder 1, 2, 3)
Host data    : none
Steps        : 1. DELETE the image with sortOrder 2. 2. GET the Item.
Expected     : 2 → media holds 2 images.
Test data    : sortOrder 2 (from the AC)
<!-- TC:TC-CAT-114:END -->

<!-- TC:TC-CAT-115:START traces=AC-CAT-111,REQ-CAT-109,API-CAT-044 -->
### TC-CAT-115 — Images of a Variant fall back to the Item's unbound images
Derived from : AC-CAT-111  (REQ-CAT-109)
Exercises    : API-CAT-044 GET /api/v1/cat/variants/{id}/media
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an Item with Variants «أحمر» and «أزرق», 2 images bound to no Variant (one primary) and 1 image bound to «أحمر»
Host data    : none
Steps        : 1. GET the images of Variant «أحمر». 2. GET the images of Variant «أزرق».
Expected     : 1 → 1 image (the «أحمر» image). 2 → the 2 unbound images, primary first.
Test data    : «أحمر», «أزرق» (from the AC)
<!-- TC:TC-CAT-115:END -->

<!-- TC:TC-CAT-116:START traces=AC-CAT-117,REQ-CAT-115,API-CAT-046 -->
### TC-CAT-116 — The storefront reads published active Items only
Derived from : AC-CAT-117  (REQ-CAT-115)
Exercises    : API-CAT-046 GET /api/v1/cat/storefront-items (also `CatalogueReadApi.listStorefrontItems`, ADR-CAT-045)
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · exactly 10 Items: 6 active PUBLISHED, 3 active UNPUBLISHED, 1 inactive
Host data    : none
Steps        : 1. GET the storefront Items (page 0, size ≥ 10).
Expected     : 200; exactly the 6 active PUBLISHED Items (totalElements 6).
Test data    : 6 / 3 / 1 (from the AC)
<!-- TC:TC-CAT-116:END -->

<!-- TC:TC-CAT-117:START traces=AC-CAT-119,REQ-CAT-117,API-CAT-003,API-CAT-002 -->
### TC-CAT-117 — Saving the e-invoice code of an Item
Derived from : AC-CAT-119  (REQ-CAT-117)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-042 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an Item with no e-invoice code
Host data    : none
Steps        : 1. PUT the Item with einvoiceCodeTypeCode EGS and einvoiceCode «EG-123456789-1001». 2. GET it.
Expected     : 2 → EGS and «EG-123456789-1001».
Test data    : EGS «EG-123456789-1001» (from the AC)
<!-- TC:TC-CAT-117:END -->

<!-- TC:TC-CAT-118:START traces=AC-CAT-120,REQ-CAT-118,API-CAT-008,API-CAT-007 -->
### TC-CAT-118 — A Variant e-invoice override is stored on that Variant only
Derived from : AC-CAT-120  (REQ-CAT-118)
Exercises    : API-CAT-008 PUT /api/v1/cat/variants/{id} · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-042 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · an Item with e-invoice code EGS «EG-123456789-1001» and 2 Variants
Host data    : none
Steps        : 1. PUT the second Variant with {einvoiceCodeTypeCode: GS1, einvoiceCode: «6223000000014», version}. 2. GET both Variants.
Expected     : 2 → the second Variant stores GS1 «6223000000014»; the first stores no override (einvoiceCodeTypeCode and einvoiceCode empty).
Test data    : EGS «EG-123456789-1001», GS1 «6223000000014» (from the AC)
<!-- TC:TC-CAT-118:END -->

<!-- TC:TC-CAT-119:START traces=AC-CAT-121,REQ-CAT-119,API-CAT-007 -->
### TC-CAT-119 — Each Variant returns its effective e-invoice data
Derived from : AC-CAT-121  (REQ-CAT-119)
Exercises    : API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the Item and Variants of TC-CAT-118 after its step 1, with Item taxClassCode STANDARD
Host data    : none
Steps        : 1. GET the first Variant. 2. GET the second Variant.
Expected     : 1 → effectiveEinvoiceCodeTypeCode EGS, effectiveEinvoiceCode «EG-123456789-1001», taxClassCode STANDARD. 2 → effective GS1 «6223000000014», taxClassCode STANDARD.
Test data    : as AC-CAT-120 (from the AC)
<!-- TC:TC-CAT-119:END -->

<!-- TC:TC-CAT-120:START traces=AC-CAT-122,REQ-CAT-120,API-CAT-035 -->
### TC-CAT-120 — Creating the e-invoice unit of a unit of measure that has no active one
Derived from : AC-CAT-122  (REQ-CAT-120)
Exercises    : API-CAT-035 POST /api/v1/cat/unit-einvoice-codes
Rule / code  : RULE-CAT-006, RULE-CAT-043 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the seeded active CAT_UOM value DOZEN has no active Unit e-invoice code: its seeded code DOZEN → DZN (D-12) is deactivated through API-CAT-037
Host data    : none
Steps        : 1. POST {key: DOZEN, einvoiceUnitCode: EA}.
Expected     : 201; an active Unit e-invoice code for DOZEN with EA.
Test data    : EA (from the AC). The AC's unit of measure is not a seeded value; the case stays on seeded data and uses the seeded CAT_UOM value DOZEN, freed of its active Unit e-invoice code, in its place (ADR-CAT-053 §2)
<!-- TC:TC-CAT-120:END -->

<!-- TC:TC-CAT-121:START traces=AC-CAT-123,REQ-CAT-121,API-CAT-036,API-CAT-045 -->
### TC-CAT-121 — Changing the e-invoice unit of a unit of measure
Derived from : AC-CAT-123  (REQ-CAT-121)
Exercises    : API-CAT-036 PUT /api/v1/cat/unit-einvoice-codes/{id} · API-CAT-045 GET /api/v1/cat/settings
Rule / code  : —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the active Unit e-invoice code for DOZEN with EA, built as in TC-CAT-120 (the seeded DOZEN → DZN deactivated through API-CAT-037, then DOZEN → EA created through API-CAT-035)
Host data    : none
Steps        : 1. PUT the DOZEN → EA code with {einvoiceUnitCode: BX, version}. 2. GET the settings.
Expected     : 2 → BX for DOZEN.
Test data    : EA → BX (from the AC; both are seeded CAT_EINVOICE_UNIT values). The seeded CAT_UOM value DOZEN stands in for the AC's unseeded unit of measure (ADR-CAT-053 §2)
<!-- TC:TC-CAT-121:END -->

<!-- TC:TC-CAT-122:START traces=AC-CAT-124,REQ-CAT-122,API-CAT-037,API-CAT-011 -->
### TC-CAT-122 — A deactivated Unit e-invoice code gives an empty e-invoice unit
Derived from : AC-CAT-124  (REQ-CAT-122)
Exercises    : API-CAT-037 DELETE /api/v1/cat/unit-einvoice-codes/{id} · API-CAT-011 GET /api/v1/cat/item-units/{id}
Rule / code  : —
Package      : DATA-DOM-LOOKUP
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · the active Unit e-invoice code for DOZEN (the seeded DOZEN → DZN, D-12) · an Item with base unit PIECE and an active Item unit DOZEN (added through API-CAT-010); a GET of that Item unit returns einvoiceUnitCode DZN
Host data    : none
Steps        : 1. DELETE the Unit e-invoice code for DOZEN. 2. GET the Item unit DOZEN.
Expected     : 1 → 200, isActiveFl false. 2 → einvoiceUnitCode empty.
Test data    : the AC's unit of measure is not a seeded value; the case stays on seeded data and uses the seeded CAT_UOM value DOZEN in its place (ADR-CAT-053 §2); conversionFactor placeholder `<factor > 0>`
<!-- TC:TC-CAT-122:END -->

<!-- TC:TC-CAT-123:START traces=AC-CAT-127,REQ-CAT-125,API-CAT-047 -->
### TC-CAT-123 — The catalogue search caps at 20 and puts the exact code first
Derived from : AC-CAT-127  (REQ-CAT-125)
Exercises    : API-CAT-047 GET /api/v1/cat/catalogue-search
Rule / code  : —
Package      : SVC-API-SEARCH
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: S0, with the caller a Seller/cashier · 25 active Items whose nameAr contains «شاحن», one of them with code ITM-000077
Host data    : none
Steps        : 1. GET the search with term «شاحن». 2. GET the search with term «ITM-000077».
Expected     : 1 → 200, 20 Items. 2 → 200, ITM-000077 in position 1.
Test data    : 25 Items, «شاحن», ITM-000077 (from the AC); other names placeholders containing «شاحن»
<!-- TC:TC-CAT-123:END -->

<!-- TC:TC-CAT-124:START traces=AC-CAT-129,REQ-CAT-127,API-CAT-047 -->
### TC-CAT-124 — The catalogue contributes nothing without VIEW on CAT_ITEMS
Derived from : AC-CAT-129  (REQ-CAT-127)
Exercises    : API-CAT-047 GET /api/v1/cat/catalogue-search
Rule / code  : — (PERM_CAT_ITEMS_VIEW absent → empty result, not CAT-403-FORBIDDEN — SEC-BE)
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: S0, except that the caller holds a custom role without PERM_CAT_ITEMS_VIEW · active Items whose nameAr contains «شاحن»
Host data    : none
Steps        : 1. GET the search with term «شاحن».
Expected     : 200; data is an empty list (0 results); not 403.
Test data    : «شاحن» (from the AC)
<!-- TC:TC-CAT-124:END -->

<!-- TC:TC-CAT-190:START traces=AC-CAT-137,REQ-CAT-131,API-CAT-001,API-CAT-003,API-CAT-004,API-CAT-002 -->
### TC-CAT-190 — A Seller/cashier cannot create, update or deactivate an Item
Derived from : AC-CAT-137  (REQ-CAT-131)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-004 DELETE /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : — (PLATFORM-STD CAT-403-FORBIDDEN, ADR-CAT-044; SEC-BE)
Package      : SEC-BE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: S0, except that the caller is a Seller/cashier holding only PERM_CAT_ITEMS_VIEW · an active Item with nameEn "Charger", created by the Owner, and no other Item in the tenant
Host data    : none
Steps        : 1. POST an Item with a valid nameAr. 2. PUT the Item with its current fields and version and nameEn "Charger 25W". 3. DELETE the Item. 4. As the Owner, GET the Item and GET /api/v1/cat/items.
Expected     : 1, 2 and 3 → 403 each, code CAT-403-FORBIDDEN (the given's standard messageAr / messageEn — ADR-CAT-044). 4 → the Item keeps nameEn "Charger" and isActiveFl true, and totalElements is 1 (0 Items created).
Test data    : "Charger", "Charger 25W" (from the AC); nameAr placeholder `<valid Arabic name>`
<!-- TC:TC-CAT-190:END -->

<!-- TC:TC-CAT-191:START traces=AC-CAT-139,REQ-CAT-132,API-CAT-002,API-CAT-003 -->
### TC-CAT-191 — Another tenant's Item reads and writes as not found
Derived from : AC-CAT-139  (REQ-CAT-132)
Exercises    : API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-003 PUT /api/v1/cat/items/{id}
Rule / code  : — (PLATFORM-STD CAT-404-NOT-FOUND, ADR-CAT-044; given tenancy)
Package      : CORE
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 for two tenants A and B of this case only (the one TC that needs two tenants) · an Item X with nameEn "Charger" in tenant A · the caller of steps 1 and 2 is tenant B's Owner, holding every PERM_CAT_* permission
Host data    : none
Steps        : 1. GET /api/v1/cat/items/{itemPk of X}. 2. PUT X by its itemPk with nameEn "Charger 25W" and X's version. 3. As tenant A's Owner, GET X.
Expected     : 1 and 2 → 404 each, code CAT-404-NOT-FOUND. 3 → nameEn "Charger".
Test data    : "Charger", "Charger 25W" (from the AC)
<!-- TC:TC-CAT-191:END -->

<!-- TC:TC-CAT-192:START traces=AC-CAT-140,REQ-CAT-133,API-CAT-003,API-CAT-002 -->
### TC-CAT-192 — An update with a stale version is refused
Derived from : AC-CAT-140  (REQ-CAT-133)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : — (PLATFORM-STD CAT-409-STALE-VERSION, ADR-CAT-044)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · an active Item read twice, once by each of two Owner sessions, both returning the same version v
Host data    : none
Steps        : 1. Session 1: PUT the Item with nameEn "Charger 25W" and version v. 2. Session 2: PUT the Item with nameEn "Charger 30W" and version v. 3. GET the Item.
Expected     : 1 → 200. 2 → 409, code CAT-409-STALE-VERSION. 3 → nameEn "Charger 25W".
Test data    : "Charger 25W", "Charger 30W" (from the AC)
<!-- TC:TC-CAT-192:END -->

<!-- TC:TC-CAT-193:START traces=AC-CAT-141,REQ-CAT-134,API-CAT-045,API-CAT-001 -->
### TC-CAT-193 — A newly provisioned tenant has the CAT seeds and creates its first Item
Derived from : AC-CAT-141  (REQ-CAT-134)
Exercises    : API-CAT-045 GET /api/v1/cat/settings · API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-004 (codes from the copied series) · —
Package      : CORE
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: CAT is installed · a new tenant T is provisioned from PLATFORM through the given provisioning endpoint (`POST /api/v1/platform/tenants`), which runs the given copies and `CatTenantProvisioningContributor` (ADR-CAT-058) · T holds no catalogue record · the caller is T's Owner (S0 holds for T only through this provisioning, which is what the case proves)
Host data    : none
Steps        : 1. GET /api/v1/cat/settings. 2. POST an Item with nameAr «كوب زجاج».
Expected     : 1 → 200; unitEinvoiceCodes holds 8 rows, all active (PIECE → EA, CARTON → CT, BOX → BX, DOZEN → DZN, KILOGRAM → KGM, GRAM → GRM, METRE → MTR, LITRE → LTR). 2 → 201; code ITM-000001 and variants[0].code VAR-000001.
Test data    : «كوب زجاج» (from the AC); the 8 seeded rows of D-12
<!-- TC:TC-CAT-193:END -->

<!-- TC:TC-CAT-206:START traces=AC-CAT-152,REQ-CAT-093,API-CAT-033,API-CAT-043,API-CAT-007 -->
### TC-CAT-206 — Changing a layout's decimals changes only how its labels are read
Derived from : AC-CAT-152  (REQ-CAT-093)
Exercises    : API-CAT-033 PUT /api/v1/cat/scale-label-layouts/{id} · API-CAT-043 GET /api/v1/cat/barcodes/lookup · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-034 (key and scaleCodeLength set on create only — ADR-CAT-064) · RULE-CAT-035 (satisfied) · —
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · the active layout prefix 20, scaleCodeLength 5, WEIGHT, valueDecimals 3 · a Variant V with the SCALE barcode 01230
Host data    : none
Steps        : 1. As the Owner, PUT the layout {embeddedValueTypeCode: WEIGHT, valueDecimals: 2, version}. 2. As a Seller/cashier, GET the lookup with value 2001230012342. 3. GET V.
Expected     : 1 → 200; scaleCodeLength 5 and valueDecimals 2. 2 → 200; the Variant V, embeddedValue 12.34, embeddedValueTypeCode WEIGHT. 3 → V still carries the SCALE barcode 01230 with isActiveFl true.
Test data    : prefix 20, 01230, 2001230012342, valueDecimals 3 → 2 (from the AC)
<!-- TC:TC-CAT-206:END -->

<!-- TC:TC-CAT-207:START traces=AC-CAT-076,REQ-CAT-074,API-CAT-052 -->
### TC-CAT-207 — CAT serves the active values of its own lookup keys only
Derived from : AC-CAT-076  (REQ-CAT-074) — the server read behind the field; the field itself is TC-CAT-138
Exercises    : API-CAT-052 GET /api/v1/cat/lookups/{key}
Rule / code  : — (PLATFORM-STD CAT-404-LOOKUP-KEY-UNKNOWN, CAT-403-FORBIDDEN — ADR-CAT-044, ADR-CAT-065)
Package      : SVC-API-SEARCH
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0 · CAT_UOM has its 8 seeded values, all active · a second user U2 of the tenant who holds no PERM_CAT_* permission
Host data    : none
Steps        : 1. GET /api/v1/cat/lookups/CAT_UOM. 2. GET /api/v1/cat/lookups/MDL_COUNTRY. 3. As U2, GET /api/v1/cat/lookups/CAT_UOM.
Expected     : 1 → 200; data has 8 options, the codes PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE and LITRE, each with labelAr and labelEn as in the SRS lookups table. 2 → 404, code CAT-404-LOOKUP-KEY-UNKNOWN, messageAr «هذه القائمة ليست من قوائم الكتالوج. اختر إحدى قوائم الكتالوج.», messageEn "This list is not one of the catalogue lists. Choose a catalogue list.". 3 → 403, code CAT-403-FORBIDDEN.
Test data    : CAT_UOM and its 8 seeded values (from the AC); MDL_COUNTRY (a key CAT does not own — this case's own value)
<!-- TC:TC-CAT-207:END -->

<!-- TC:TC-CAT-213:START traces=AC-CAT-157,REQ-CAT-136,API-CAT-053,API-CAT-021,API-CAT-024,API-CAT-039 -->
### TC-CAT-213 — Reactivating a top-level Category keeps its code
Derived from : AC-CAT-157  (REQ-CAT-136)
Exercises    : API-CAT-053 PATCH /api/v1/cat/categories/{id} · API-CAT-021 POST /api/v1/cat/categories · API-CAT-024 DELETE /api/v1/cat/categories/{id} · API-CAT-039 GET /api/v1/cat/categories
Rule / code  : RULE-CAT-024 → CAT-409-CATEGORY-DUPLICATE (name taken meanwhile) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · the top-level Category PHONES «هواتف», deactivated through API-CAT-024
Host data    : none
Steps        : 1. POST a top-level Category {code: PHONES2, nameAr: «هواتف»}. 2. PATCH PHONES with {isActiveFl: true}. 3. DELETE PHONES2. 4. PATCH PHONES with {isActiveFl: true}. 5. GET /api/v1/cat/categories?isActiveFl=true.
Expected     : 1 → 201. 2 → 409, code CAT-409-CATEGORY-DUPLICATE, messageAr «يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا.», messageEn "The category code already exists, or the name already exists under the same parent. Use a different code or name."; PHONES stays inactive. 3 → 200. 4 → 200; isActiveFl true, code PHONES. 5 → the tree lists PHONES «هواتف» as an active root.
Test data    : PHONES, PHONES2, «هواتف» (from the AC; PHONES2 is this case's own)
<!-- TC:TC-CAT-213:END -->

<!-- TC:TC-CAT-215:START traces=AC-CAT-159,REQ-CAT-137,API-CAT-054,API-CAT-025,API-CAT-028,API-CAT-041 -->
### TC-CAT-215 — Reactivating a Variant attribute keeps its code and offers it again
Derived from : AC-CAT-159  (REQ-CAT-137)
Exercises    : API-CAT-054 PATCH /api/v1/cat/variant-attributes/{id} · API-CAT-025 POST /api/v1/cat/variant-attributes · API-CAT-028 DELETE /api/v1/cat/variant-attributes/{id} · API-CAT-041 GET /api/v1/cat/variant-attributes
Rule / code  : RULE-CAT-016 → CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE (name taken meanwhile) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · the Variant attribute SIZE «المقاس» with the active values S and M, deactivated through API-CAT-028 (0 active Variants carry it)
Host data    : none
Steps        : 1. POST an attribute {code: SIZE2, nameAr: «المقاس»}. 2. PATCH SIZE with {isActiveFl: true}. 3. DELETE SIZE2. 4. PATCH SIZE with {isActiveFl: true}. 5. GET /api/v1/cat/variant-attributes?isActiveFl=true.
Expected     : 1 → 201. 2 → 409, code CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE, messageAr «الرمز أو الاسم مستخدم بالفعل. استخدم رمزًا (حتى 30 حرفًا لاتينيًا أو رقمًا أو شرطة) واسمًا مختلفين.», messageEn "The code or name is already used. Use a different code (up to 30 Latin letters, digits or hyphens) and name."; SIZE stays inactive. 3 → 200. 4 → 200; isActiveFl true, code SIZE. 5 → SIZE is listed with its active values S and M.
Test data    : SIZE, SIZE2, «المقاس», S, M (from the AC; SIZE2 is this case's own)
<!-- TC:TC-CAT-215:END -->

<!-- TC:TC-CAT-216:START traces=AC-CAT-160,REQ-CAT-138,API-CAT-055,API-CAT-054,API-CAT-028,API-CAT-026 -->
### TC-CAT-216 — Reactivating a Variant attribute value needs an active attribute
Derived from : AC-CAT-160  (REQ-CAT-138)
Exercises    : API-CAT-055 PATCH /api/v1/cat/variant-attribute-values/{id} · API-CAT-054 PATCH /api/v1/cat/variant-attributes/{id} · API-CAT-028 DELETE /api/v1/cat/variant-attributes/{id} · API-CAT-026 GET /api/v1/cat/variant-attributes/{id}
Rule / code  : PLATFORM-STD CAT-400-REQUEST-INVALID (attribute inactive, ADR-CAT-044) · RULE-CAT-016 (satisfied)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · the attribute SIZE with the active value M and the inactive value XS «صغير جدًا» · 0 active Variants carry SIZE
Host data    : none
Steps        : 1. DELETE SIZE (API-CAT-028). 2. PATCH XS with {isActiveFl: true}. 3. PATCH SIZE with {isActiveFl: true}. 4. PATCH XS with {isActiveFl: true}. 5. GET SIZE.
Expected     : 1 → 200. 2 → 400, code CAT-400-REQUEST-INVALID, messageAr «الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة.», messageEn "The request is not valid. Check the highlighted fields and try again."; XS stays inactive. 3 → 200. 4 → 200; isActiveFl true, code XS. 5 → SIZE lists M and XS as active.
Test data    : SIZE, M, XS «صغير جدًا» (from the AC)
<!-- TC:TC-CAT-216:END -->

<!-- TC:TC-CAT-221:START traces=AC-CAT-163,REQ-CAT-140,API-CAT-009,API-CAT-007,API-CAT-002 -->
### TC-CAT-221 — Deactivating the default Variant moves the default to the active Variant with the lowest code
Derived from : AC-CAT-163  (REQ-CAT-140)
Exercises    : API-CAT-009 DELETE /api/v1/cat/variants/{id} · API-CAT-007 GET /api/v1/cat/variants/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-048 → CAT-200-DEACTIVATED (success notice) · default-flag move (ADR-CAT-072)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an active STANDARD Item whose 3 active Variants VAR-000010 (its default Variant, isDefaultFl true), VAR-000011 and VAR-000012 were generated through API-CAT-006 (series CAT_VARIANT positioned so that the codes are these); none is a Bundle component · the outbox is observed
Host data    : none
Steps        : 1. DELETE VAR-000010. 2. GET VAR-000010 and VAR-000011. 3. GET the Item. 4. Collect the cat.CatalogueChanged events delivered after step 1.
Expected     : 1 → 200, code CAT-200-DEACTIVATED. 2 → VAR-000010 isActiveFl false and isDefaultFl false; VAR-000011 isActiveFl true and isDefaultFl true. 3 → 2 active Variants, VAR-000011 listed first (default first — QR-CAT-013), and exactly 1 Variant of the Item with isDefaultFl true. 4 → one event DEACTIVATED for VAR-000010 and one event UPDATED for VAR-000011.
Test data    : VAR-000010, VAR-000011, VAR-000012 (from the AC)
<!-- TC:TC-CAT-221:END -->

<!-- TC:TC-CAT-222:START traces=AC-CAT-164,REQ-CAT-141,API-CAT-006,API-CAT-005,API-CAT-007,API-CAT-002 -->
### TC-CAT-222 — Generation on an Item with no active Variant gives the default to the first new Variant
Derived from : AC-CAT-164  (REQ-CAT-141)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-005 PATCH /api/v1/cat/items/{id} · API-CAT-007 GET /api/v1/cat/variants/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : default-flag move (ADR-CAT-072) · repair of RULE-CAT-050 allowed on an inactive Item (ADR-CAT-068)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: S0 (`variants` on) · a STANDARD Item whose only Variant V (isDefaultFl true) carries no attribute; the Item is deactivated through API-CAT-004, then V through API-CAT-009 (allowed: RULE-CAT-015 guards only an active Item), so the flag stays on the inactive V · the active attribute SIZE with the active value M
Host data    : none
Steps        : 1. POST to the Item the generation {selections: [{variantAttributeFk: SIZE, variantAttributeValueFks: [M]}]}. 2. GET V. 3. PATCH the Item with body {isActiveFl: true}. 4. GET the Item.
Expected     : 1 → 201; one new active Variant carrying M, with isDefaultFl true. 2 → V isActiveFl false and isDefaultFl false. 3 → 200; isActiveFl true. 4 → exactly 1 Variant of the Item with isDefaultFl true — the M Variant — and it is active.
Test data    : SIZE, M (from the AC); V placeholder
<!-- TC:TC-CAT-222:END -->

<!-- TC:TC-CAT-223:START traces=AC-CAT-051,AC-CAT-058,AC-CAT-059,AC-CAT-085,AC-CAT-080,AC-CAT-108,AC-CAT-067,AC-CAT-045,AC-CAT-048,REQ-CAT-049,REQ-CAT-056,REQ-CAT-057,REQ-CAT-083,REQ-CAT-084,REQ-CAT-078,REQ-CAT-106,REQ-CAT-065,REQ-CAT-043,REQ-CAT-046,API-CAT-001,API-CAT-006,API-CAT-014,API-CAT-015,API-CAT-017,API-CAT-012,API-CAT-019,API-CAT-023,API-CAT-027,API-CAT-030,API-CAT-002,API-CAT-011,API-CAT-022,API-CAT-026 -->
### TC-CAT-223 — A request that contradicts itself, names a record outside its parent or updates an inactive record is refused before any rule and writes nothing
Derived from : AC-CAT-051, AC-CAT-058, AC-CAT-059, AC-CAT-085, AC-CAT-080, AC-CAT-108, AC-CAT-067, AC-CAT-045, AC-CAT-048  (REQ-CAT-049, REQ-CAT-056, REQ-CAT-057, REQ-CAT-083, REQ-CAT-084, REQ-CAT-078, REQ-CAT-106, REQ-CAT-065, REQ-CAT-043, REQ-CAT-046 — the malformed counterpart of each accepted request)
Exercises    : API-CAT-006, API-CAT-001, API-CAT-017, API-CAT-014, API-CAT-015, API-CAT-012, API-CAT-019, API-CAT-023, API-CAT-027, API-CAT-030 (one row each case) · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-011 GET /api/v1/cat/item-units/{id} · API-CAT-022 GET /api/v1/cat/categories/{id} · API-CAT-026 GET /api/v1/cat/variant-attributes/{id}
Rule / code  : PLATFORM-STD CAT-400-REQUEST-INVALID — the request-consistency rule (rows 1 … 10) and the update-of-an-inactive-record rule (rows 11 … 15) of the backend plan's PHASE CORE API conventions, each checked before any RULE runs (ADR-CAT-044); constraint translation: no row answers CAT-500-SERVER-ERROR
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL · parameterised (one row per case)
Preconditions: S0 · a STANDARD Item I with only its default Variant D (no barcode) and no Variant attribute in use · the active attributes SIZE (values S, M) and COLOUR (value «أحمر») · a BUNDLE Item B with the active lines L1 {P, 1} and L2 {C, 1}, read with its version · a second BUNDLE Item B2 with the active line L3 {C, 1} · an active Variant Q of an active STANDARD Item · on I, the Item unit BOX deactivated through API-CAT-013, and the images M1 (primary) and M2, M2 removed through API-CAT-020; BOX and M2 read with their versions · the Category OLD «فئة قديمة», deactivated through API-CAT-024 · the attribute MATERIAL «الخامة», deactivated through API-CAT-028, and the value XS «صغير جدًا» of SIZE, deactivated through API-CAT-031, each read with its version · the outbox is observed
Host data    : none
Steps        : 1. Send each row below. 2. GET I, B and B2, and collect the cat.CatalogueChanged events delivered after step 1.

| Row | Endpoint | Request | Contradiction |
|---|---|---|---|
| 1 | API-CAT-006 | POST to I: selections [{SIZE, [S]}, {SIZE, [M]}] | an attribute selected twice |
| 2 | API-CAT-006 | POST to I: selections [{SIZE, [S, S]}] | a value id listed twice |
| 3 | API-CAT-006 | POST to I: selections [{SIZE, [«أحمر»]}] | a value of another attribute |
| 4 | API-CAT-001 | POST an Item with a valid nameAr, itemKindCode STANDARD and bundleComponents [{Q, 1}] | Bundle lines on a STANDARD Item |
| 5 | API-CAT-001 | POST an Item with itemKindCode BUNDLE and bundleComponents [{Q, 1}, {Q, 2}] | a component listed twice |
| 6 | API-CAT-017 | PUT B's lines [{bundleComponentPk: L3, C, 1}] with B's version | a line of another Bundle |
| 7 | API-CAT-017 | PUT B's lines [{bundleComponentPk: L1, Q, 1}, {bundleComponentPk: L2, C, 1}] with B's version | a kept line whose componentVariantFk changed |
| 8 | API-CAT-017 | PUT B's lines [{bundleComponentPk: L1, P, 1}, {bundleComponentPk: L1, P, 2}] with B's version | a line listed twice |
| 9 | API-CAT-014 | POST to D {barcodeValue: 0400000000428, barcodeTypeCode: INTERNAL} | type INTERNAL on the GTIN / SCALE endpoint |
| 10 | API-CAT-015 | POST to D {uomCode: PIECE, barcodeValue: 0400000000428} | a client-chosen value for an internal barcode |
| 11 | API-CAT-012 | PUT BOX {isPurchaseFl: true, isSalesFl: false, version} | an update of an inactive Item unit |
| 12 | API-CAT-019 | PUT M2 {nameAr: «صورة 2», sortOrder: 1, isPrimaryFl: true, version} | an update of a removed image that would make it primary |
| 13 | API-CAT-023 | PUT OLD {nameAr: «فئة قديمة 2», sortOrder, version} | an update of an inactive Category |
| 14 | API-CAT-027 | PUT MATERIAL {nameAr: «الخامة 2», sortOrder, version} | an update of an inactive Variant attribute |
| 15 | API-CAT-030 | PUT XS {nameAr: «صغير جدًا 2», sortOrder, version} | an update of an inactive Variant attribute value |

Expected     : 1 → every row answers 400, code CAT-400-REQUEST-INVALID, messageAr «الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة.», messageEn "The request is not valid. Check the highlighted fields and try again."; no row answers CAT-500-SERVER-ERROR or any RULE code. 2 → nothing was written: I still has exactly 1 Variant (D) with no attribute assignment and no barcode; B's lines are still L1 {P, 1} and L2 {C, 1} with its version unchanged; B2's line is still L3 {C, 1}; no Item was created by rows 4 and 5 (GET /api/v1/cat/items lists no new Item); BOX (GET by itemUnitPk) is still inactive with isSalesFl true and its version unchanged; the active images of I are still M1 alone, and M1 still has isPrimaryFl true — row 12 did not clear the active primary, so I keeps exactly one main image (RULE-CAT-039); OLD, MATERIAL and XS keep their names, versions and inactive state; 0 catalogue-change events were delivered.
Test data    : I, D, B, B2, L1 … L3, P, C, Q, M1, M2 (placeholders); SIZE S, M, XS «صغير جدًا»; COLOUR «أحمر»; MATERIAL «الخامة»; OLD «فئة قديمة»; BOX; 0400000000428 (this case's own)
<!-- TC:TC-CAT-223:END -->

<!-- TC:TC-CAT-224:START traces=AC-CAT-165,REQ-CAT-008,API-CAT-021,API-CAT-023,API-CAT-024,API-CAT-053 -->
### TC-CAT-224 — Every committed Category change publishes one catalogue-change event
Derived from : AC-CAT-165  (REQ-CAT-008)
Exercises    : API-CAT-021 POST /api/v1/cat/categories · API-CAT-023 PUT /api/v1/cat/categories/{id} · API-CAT-024 DELETE /api/v1/cat/categories/{id} · API-CAT-053 PATCH /api/v1/cat/categories/{id}
Rule / code  : RULE-CAT-049 (event recorded in the change transaction, delivered after commit) · RULE-CAT-048 → CAT-200-DEACTIVATED (success notice) · ADR-CAT-077
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · no Category with code PHONES · the active top-level Category «إلكترونيات» · the given outbox is observed
Host data    : none
Steps        : 1. POST {code: PHONES, nameAr: «هواتف»} (top level) and record its categoryPk `<pk>`. 2. PUT PHONES with nameAr «هواتف ذكية», parentCategoryFk «إلكترونيات», its sortOrder and version. 3. DELETE PHONES. 4. DELETE PHONES again. 5. PATCH PHONES with {isActiveFl: true}. 6. Collect the cat.CatalogueChanged events delivered after each step.
Expected     : 1 → 201; exactly 1 event: entity ENT-CAT-006, recordPk `<pk>`, empty itemPk, changeKind CREATED. 2 → 200; exactly 1 event, changeKind UPDATED (a rename and a move in one request). 3 → 200, code CAT-200-DEACTIVATED with messageAr «تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة.» and messageEn "Deactivated. The record stays available to earlier documents and no longer appears in new selections."; exactly 1 event, changeKind DEACTIVATED (AC-CAT-165). 4 → 200, the same notice, and 0 events: nothing changed. 5 → 200; exactly 1 event, changeKind REACTIVATED. Every event carries entity ENT-CAT-006, recordPk `<pk>` and an empty itemPk, and no field values (CON-CAT-018).
Test data    : PHONES, «هواتف», «هواتف ذكية», «إلكترونيات» (from AC-CAT-066 / AC-CAT-157; «هواتف ذكية» is this case's own)
<!-- TC:TC-CAT-224:END -->

<!-- TC:TC-CAT-231:START traces=AC-CAT-171,REQ-CAT-148,REQ-CAT-104,REQ-CAT-102,API-CAT-050,API-CAT-056,API-CAT-015,API-CAT-007 -->
### TC-CAT-231 — Warehouse staff generate the missing internal barcode from the label screen, then print
Derived from : AC-CAT-171  (REQ-CAT-148; the refused and the accepted print are REQ-CAT-104 and REQ-CAT-102)
Exercises    : API-CAT-050 POST /api/v1/cat/barcode-labels · API-CAT-056 POST /api/v1/cat/barcode-labels/variants/{id}/internal-barcode · API-CAT-015 POST /api/v1/cat/variants/{id}/barcodes/internal · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-037 → CAT-422-LABEL-VARIANT-NO-BARCODE (before the generation) · PLATFORM-STD CAT-403-FORBIDDEN (the Items page's API-CAT-015 without PERM_CAT_ITEMS_CREATE; API-CAT-056 without PERM_CAT_BARCODE_LABELS_CREATE) · RULE-CAT-049 (one CREATED event) · ADR-CAT-078
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0, except that the caller of steps 1 … 5 is a Warehouse staff user holding PERM_CAT_BARCODE_LABELS_VIEW / CREATE and PERM_CAT_ITEMS_VIEW and not PERM_CAT_ITEMS_CREATE · an active Variant V of an active Item with base unit PIECE and no active barcode, and an active Variant W of another active Item with no active barcode · series CAT_INTERNAL_BARCODE last issued 0000000099 · no active scale label layout · the outbox is observed
Host data    : none
Steps        : 1. POST the label request {lines: [{variantPk: V, copies: 1}]}. 2. POST to API-CAT-015 for V with body {}. 3. POST to API-CAT-056 for V with no body. 4. GET V. 5. POST the label request of step 1 again. 6. Count the cat.CatalogueChanged events delivered after step 1. 7. As a Seller/cashier (PERM_CAT_ITEMS_VIEW only), POST to API-CAT-056 for W.
Expected     : 1 → 422, code CAT-422-LABEL-VARIANT-NO-BARCODE, messageAr «لا يوجد باركود نشط بالوحدة الأساسية لـ 1 متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.», messageEn "1 variants have no active barcode in the base unit. Generate an internal barcode for them, then print."; no document. 2 → 403, code CAT-403-FORBIDDEN; nothing is saved and no number is drawn — the Items page's operation stays the Owner's. 3 → 201; data.barcodeValue 0400000001005 (04 + 0000000100 + check digit 5), barcodeTypeCode INTERNAL, uomCode PIECE, isActiveFl true. 4 → V carries exactly that one active barcode. 5 → 200, application/pdf, one document holding 1 label that shows V's nameAr, code and 0400000001005. 6 → exactly 1 event: entity ENT-CAT-009, changeKind CREATED. 7 → 403, code CAT-403-FORBIDDEN; W still has no barcode.
Test data    : 0000000099 → 0400000001005 (from the AC); V, W (placeholders)
<!-- TC:TC-CAT-231:END -->

<!-- TC:TC-CAT-235:START traces=AC-CAT-174,REQ-CAT-058,REQ-CAT-056,API-CAT-001,API-CAT-002,API-CAT-017 -->
### TC-CAT-235 — Every Bundle line returns the base unit its quantity is in
Derived from : AC-CAT-174  (REQ-CAT-058; the stored quantity is REQ-CAT-056)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-017 PUT /api/v1/cat/items/{id}/bundle-components
Rule / code  : — (ADR-CAT-080: the quantity is in the base unit of the component Variant's Item, returned as componentBaseUomCode)
Package      : SVC-API-CRUD
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: S0 (`bundles` on) · an active STANDARD Item «أرز» with base unit KILOGRAM and the active Item unit CARTON (conversionFactor 10), its Variant R · an active STANDARD Item «كوب» with base unit PIECE, its Variant C
Host data    : none
Steps        : 1. POST a Bundle {nameAr: «سلة أرز وأكواب», itemKindCode: BUNDLE, bundleComponents: [{componentVariantFk: R, quantity: 0.5}, {componentVariantFk: C, quantity: 2}]}. 2. GET the Bundle. 3. PUT the Bundle's lines with C's quantity 3 and the Bundle's version.
Expected     : 1 → 201; data.bundleComponents holds the R line with quantity 0.500 and componentBaseUomCode KILOGRAM, and the C line with quantity 2.000 and componentBaseUomCode PIECE. 2 → the same two lines; no line returns CARTON. 3 → 200; the returned lines carry componentBaseUomCode KILOGRAM and PIECE, and the C line quantity 3.000.
Test data    : KILOGRAM, CARTON, PIECE, 0.500, 2 (from the AC); «أرز», «كوب», «سلة أرز وأكواب», R, C (this case's own)
<!-- TC:TC-CAT-235:END -->
<!-- SUB:API-SCENARIOS:END -->
