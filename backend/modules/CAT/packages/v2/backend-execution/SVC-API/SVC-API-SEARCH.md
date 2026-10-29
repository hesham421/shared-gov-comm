<!-- source: PHASE:SVC-API / SUB:SVC-API-SEARCH -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-CAT-001, DBF-CAT-002, DBF-CAT-003, DBF-CAT-004, DBF-CAT-005, DBF-CAT-006, DBF-CAT-007, DBF-CAT-008, DBF-CAT-009, DBF-CAT-013, DBF-CAT-014, DBF-CAT-015, DBF-CAT-022, DBF-CAT-023, DBF-CAT-025, DBF-CAT-027, DBF-CAT-028, DBF-CAT-035, DBF-CAT-036, DBF-CAT-037, DBF-CAT-039, DBF-CAT-040, DBF-CAT-047, DBF-CAT-051, DBF-CAT-052, DBF-CAT-053, DBF-CAT-071, DBF-CAT-072, DBF-CAT-073, DBF-CAT-074, DBF-CAT-075, DBF-CAT-076, DBF-CAT-077, DBF-CAT-097, DBF-CAT-110, DBF-CAT-111, DBF-CAT-112, DBF-CAT-113, DBF-CAT-114, DBF-CAT-118, DBF-CAT-122, DBF-CAT-123, DBF-CAT-124, DBF-CAT-125, DBF-CAT-126, DBF-CAT-127, DBF-CAT-128, DBF-CAT-129, DBF-CAT-136, DBF-CAT-137, DBF-CAT-140, DBF-CAT-141, DBF-CAT-142, DBF-CAT-143, DBF-CAT-150, DBF-CAT-151, DBF-CAT-154, DBF-CAT-155, REQ-CAT-003, REQ-CAT-014, REQ-CAT-015, REQ-CAT-048, REQ-CAT-067, REQ-CAT-068, REQ-CAT-073, REQ-CAT-074, REQ-CAT-076, REQ-CAT-086, REQ-CAT-090, REQ-CAT-094, REQ-CAT-095, REQ-CAT-097, REQ-CAT-100, REQ-CAT-101, REQ-CAT-109, REQ-CAT-115, REQ-CAT-125, REQ-CAT-126, REQ-CAT-127, REQ-CAT-134 -->
<!-- SUB:SVC-API-SEARCH:START traces=REQ-CAT-134,DBF-CAT-052,DBF-CAT-001,DBF-CAT-006,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-007,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-022,DBF-CAT-023,DBF-CAT-027,DBF-CAT-028,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-039,DBF-CAT-040,DBF-CAT-047,DBF-CAT-051,DBF-CAT-053,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-118,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129,DBF-CAT-136,DBF-CAT-137,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-150,DBF-CAT-151,DBF-CAT-154,DBF-CAT-155,REQ-CAT-003,REQ-CAT-014,REQ-CAT-015,REQ-CAT-048,REQ-CAT-067,REQ-CAT-068,REQ-CAT-086,REQ-CAT-090,REQ-CAT-094,REQ-CAT-095,REQ-CAT-097,REQ-CAT-100,REQ-CAT-101,REQ-CAT-109,REQ-CAT-115,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127 -->
### SUB SVC-API-SEARCH

<!-- API:API-CAT-038:START traces=REQ-CAT-003,REQ-CAT-014,REQ-CAT-015,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-007,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-110,DBF-CAT-113 -->
### API-CAT-038 — search Items
Entity       : ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-001 "search", "list"
Endpoint     : /api/v1/cat/items   verb: GET
Layers       : CatItemController.search → CatItemService.search
Request      : query {text: string(200); categoryFk: int64 DBF-CAT-015; brandCode: string(50) DBF-CAT-007; itemKindCode: string(50) DBF-CAT-005; publishStateCode: string(50) DBF-CAT-013; isActiveFl: boolean DBF-CAT-014; page: int32; size: int32} · text matches nameAr (DBF-CAT-003), nameEn (DBF-CAT-004), code (DBF-CAT-002) or an active barcode (DBF-CAT-110, DBF-CAT-113)
Response     : 200 · ApiResponse<Page<ItemSummary>> · paginated: yes
Validations  : none — filters are the SRS B2 list; the kind filter is shown by the frontend only while `bundles` is on (REQ-CAT-015) and Variant fields only while `variants` is on (REQ-CAT-014); sorted by nameAr ascending; an empty page is success
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : build the criteria → page query (QR-CAT-005) → return the page of ItemSummary
Repository   : QR-CAT-005 FIND_BY_CRITERIA · join ADR-CAT-075 (left join to CAT_CATEGORY for the derived Category label; barcode match by EXISTS sub-query) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : names in ar and en; displayNameEn fallback
<!-- API:API-CAT-038:END -->

<!-- API:API-CAT-039:START traces=REQ-CAT-067,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077 -->
### API-CAT-039 — list Category tree
Entity       : ENT-CAT-006 · operation VIEW · serves SCR-REQ-CAT-002 "list (tree)"
Honours: CON-CAT-012
Endpoint     : /api/v1/cat/categories   verb: GET
Layers       : CatCategoryController.tree → CatCategoryQueryFacade.getCategoryTree (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getCategoryTree (also called by CatalogueReadApi.getCategoryTree, with no CAT permission check — ADR-CAT-061)
Request      : query {text: string(200); isActiveFl: boolean DBF-CAT-076} · text matches code (DBF-CAT-072), nameAr (DBF-CAT-073), nameEn (DBF-CAT-074); a matching node is returned with its ancestors
Response     : 200 · ApiResponse<List<CategoryNode>> · paginated: no
Validations  : none — every Category under its parent (DBF-CAT-077), each level ordered by sortOrder (DBF-CAT-075)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : load the tenant's Categories (QR-CAT-036) → filter → assemble the tree in memory → return the root nodes
Repository   : QR-CAT-036 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-039:END -->

<!-- API:API-CAT-040:START traces=REQ-CAT-068,DBF-CAT-071,DBF-CAT-076,DBF-CAT-077,DBF-CAT-014,DBF-CAT-015 -->
### API-CAT-040 — Items of a Category
Entity       : ENT-CAT-006, ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-002 "list Items of a Category"
Honours: CON-CAT-013
Endpoint     : /api/v1/cat/categories/{id}/items   verb: GET
Layers       : CatCategoryController.items → CatCategoryQueryFacade.listCategoryItems (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.listCategoryItems (also called by CatalogueReadApi.listCategoryItems, with no CAT permission check — ADR-CAT-061)
Request      : path {id: int64 required DBF-CAT-071} · query {page: int32; size: int32}
Response     : 200 · ApiResponse<Page<ItemSummary>> · paginated: yes
Validations  : none — active Items (DBF-CAT-014) of the Category and of its active sub-categories (DBF-CAT-076, DBF-CAT-077), sorted by nameAr
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the Category (QR-CAT-032) → page of Items over the active sub-tree (QR-CAT-006, on DBF-CAT-015) → return the page
Repository   : QR-CAT-032 FIND_ONE · QR-CAT-006 NATIVE · join ADR-CAT-047 (recursive CTE) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-040:END -->

<!-- API:API-CAT-041:START traces=REQ-CAT-014,REQ-CAT-048,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-039,DBF-CAT-040,DBF-CAT-047,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053 -->
### API-CAT-041 — list Variant attributes with values
Entity       : ENT-CAT-003, ENT-CAT-004 · operation VIEW · serves SCR-REQ-CAT-003 "list"
Endpoint     : /api/v1/cat/variant-attributes   verb: GET
Layers       : CatVariantAttributeController.list → CatVariantAttributeService.list
Request      : query {text: string(200); isActiveFl: boolean DBF-CAT-040, DBF-CAT-052} · text matches code (DBF-CAT-036) or nameAr (DBF-CAT-037) · `isActiveFl` filters both the attributes (DBF-CAT-040) and their values (DBF-CAT-052): an attribute is returned only when its isActiveFl equals the filter, and of each returned attribute only the values whose isActiveFl equals the filter; omitting it returns every attribute with every value, active and inactive
Response     : 200 · ApiResponse<List<VariantAttributeView>> · paginated: no
Validations  : none — a read stays allowed while `variants` is off (RULE-CAT-008); the menu entry is absent while off (REQ-CAT-014) · attributes by sortOrder (DBF-CAT-039), values by sortOrder (DBF-CAT-051) · callers: the Variant generation picker sends `isActiveFl=true` (active attributes with their active values — REQ-CAT-044, REQ-CAT-047); the SCR-CAT-003 list sends no filter by default, so inactive attributes and values stay visible and can be reactivated (API-CAT-054, 055)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : load attributes with values (QR-CAT-026, the `isActiveFl` filter applied to both reads) → return the list
Repository   : QR-CAT-026 FIND_ALL · join NONE (two ordered reads) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-041:END -->

<!-- API:API-CAT-042:START traces=REQ-CAT-101,DBF-CAT-003,DBF-CAT-006,DBF-CAT-111,DBF-CAT-112,DBF-CAT-022,DBF-CAT-023,DBF-CAT-027,DBF-CAT-028,DBF-CAT-110,DBF-CAT-113,DBF-CAT-118 -->
### API-CAT-042 — search Variants
Entity       : ENT-CAT-002, ENT-CAT-009 · operation VIEW · serves SCR-REQ-CAT-005 "search Variants"
Endpoint     : /api/v1/cat/variants   verb: GET
Layers       : CatVariantController.search → CatVariantService.search
Request      : query {text: string(200); page: int32; size: int32} · text matches the Variant code (DBF-CAT-023), the Item nameAr (DBF-CAT-003) or an active barcode (DBF-CAT-110)
Response     : 200 · ApiResponse<Page<VariantSearchRow>> · paginated: yes
Validations  : none — active Variants (DBF-CAT-027) of active Items (DBF-CAT-014) only (REQ-CAT-101, ADR-CAT-059), each with its Item nameAr and its label barcode — the earliest-created (DBF-CAT-118) active barcode of type GTIN or INTERNAL (DBF-CAT-111) bound to the Item's base unit (DBF-CAT-112 = DBF-CAT-006); empty when none (ADR-CAT-079); the text filter still matches any active barcode; sorted by Item nameAr then code
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : page query (QR-CAT-016) → derived Variant names for the page (QR-CAT-019) → return the page
Repository   : QR-CAT-016 FIND_BY_CRITERIA · QR-CAT-019 FIND_ALL · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-005 Barcode labels · PERM_CAT_BARCODE_LABELS_VIEW
Localization : derived names in ar and en
<!-- API:API-CAT-042:END -->

<!-- API:API-CAT-043:START traces=REQ-CAT-086,REQ-CAT-090,REQ-CAT-094,REQ-CAT-097,REQ-CAT-100,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-137,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143 -->
### API-CAT-043 — barcode and scale label lookup
Entity       : ENT-CAT-009, ENT-CAT-002 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-010
Endpoint     : /api/v1/cat/barcodes/lookup   verb: GET
Layers       : CatBarcodeController.lookup → CatBarcodeQueryFacade.lookup (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.lookup (also called by CatalogueReadApi.lookupBarcode, with no CAT permission check — ADR-CAT-061)
Request      : query {value: string(48) required DBF-CAT-110}
Response     : 200 · ApiResponse<BarcodeLookupResult> · paginated: no
Validations  : scale label — a 13-digit value beginning with the key (DBF-CAT-137) of an active layout (DBF-CAT-143): RULE-CAT-035 (valid EAN-13 check digit; the scaleCodeLength (DBF-CAT-140) digits after the prefix equal an active SCALE barcode) · plain barcode — RULE-CAT-047 (no active barcode on an active Variant of an active Item and no active layout matches — REQ-CAT-086, ADR-CAT-059); a scale code resolves only to an active Variant of an active Item as well
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-422-SCALE-LABEL-UNREADABLE, CAT-404-BARCODE-NOT-FOUND
Orchestration : active layouts (QR-CAT-059) → when the value is a scale label: check digit, cut the scale code, find the active SCALE barcode (QR-CAT-052, barcodeTypeCode DBF-CAT-111 = SCALE), embedded value = the next 10 − scaleCodeLength digits ÷ 10^valueDecimals (DBF-CAT-142), typed by embeddedValueTypeCode (DBF-CAT-141), in the Item's base unit → otherwise exact active barcode (QR-CAT-052) → return Variant, Item and the barcode's uomCode (DBF-CAT-112)
Repository   : QR-CAT-059 FIND_ALL · QR-CAT-052 FIND_ONE · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : messages ar + en from the Error Catalog (next action in each)
<!-- API:API-CAT-043:END -->

<!-- API:API-CAT-044:START traces=REQ-CAT-109,DBF-CAT-022,DBF-CAT-028,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129 -->
### API-CAT-044 — images of a Variant
Entity       : ENT-CAT-010, ENT-CAT-002 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-015
Endpoint     : /api/v1/cat/variants/{id}/media   verb: GET
Layers       : CatMediaController.ofVariant → CatMediaQueryFacade.getVariantImages (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getVariantImages (also called by CatalogueReadApi.getVariantImages, with no CAT permission check — ADR-CAT-061)
Request      : path {id: int64 required DBF-CAT-022}
Response     : 200 · ApiResponse<List<MediaView>> · paginated: no
Validations  : none — the active media bound to the Variant (DBF-CAT-129), else the Item's active media bound to no Variant; primary (DBF-CAT-126) first, then sortOrder (DBF-CAT-125)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the Variant (QR-CAT-012) → images with fallback (QR-CAT-058) → return the list
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-058 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : image descriptions in ar and en
<!-- API:API-CAT-044:END -->

<!-- API:API-CAT-045:START traces=REQ-CAT-134,REQ-CAT-095,DBF-CAT-136,DBF-CAT-137,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-150,DBF-CAT-151,DBF-CAT-154,DBF-CAT-155 -->
### API-CAT-045 — read catalogue settings
Entity       : ENT-CAT-011, ENT-CAT-012 · operation VIEW · serves SCR-REQ-CAT-006 "read"
Endpoint     : /api/v1/cat/settings   verb: GET
Layers       : CatSettingsController.get → CatSettingsService.getSettings
Request      : none
Response     : 200 · ApiResponse<CatalogueSettings> · paginated: no
Validations  : none — every layout and every Unit e-invoice code, active or not, each with isActiveFl (DBF-CAT-143, DBF-CAT-155)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : load the layouts (QR-CAT-064) and the Unit e-invoice codes (QR-CAT-069) → return CatalogueSettings
Repository   : QR-CAT-064 FIND_ALL · QR-CAT-069 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_VIEW
Localization : descriptions in ar and en
<!-- API:API-CAT-045:END -->

<!-- API:API-CAT-046:START traces=REQ-CAT-115,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-013,DBF-CAT-014 -->
### API-CAT-046 — storefront catalogue read
Entity       : ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-001 "list"
Honours: CON-CAT-014
Endpoint     : /api/v1/cat/storefront-items   verb: GET
Layers       : CatItemController.storefront → CatItemQueryFacade.listStorefrontItems (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.listStorefrontItems (also called by CatalogueReadApi.listStorefrontItems, with no CAT permission check — ADR-CAT-061) — ADR-CAT-045
Request      : query {page: int32; size: int32}
Response     : 200 · ApiResponse<Page<ItemSummary>> · paginated: yes
Validations  : none — only Items with isActiveFl true (DBF-CAT-014) and publishStateCode PUBLISHED (DBF-CAT-013), sorted by nameAr
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR
Orchestration : page query (QR-CAT-007) → return the page
Repository   : QR-CAT-007 FIND_BY_CRITERIA · join ADR-CAT-075 (left join to CAT_CATEGORY for the derived Category label) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW (back-office preview; shoppers reach the data only through the in-process interface — ADR-CAT-045)
Localization : names in ar and en
<!-- API:API-CAT-046:END -->

<!-- API:API-CAT-047:START traces=REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-014,DBF-CAT-110,DBF-CAT-113 -->
### API-CAT-047 — catalogue search (global search)
Entity       : ENT-CAT-001, ENT-CAT-009 · operation VIEW · serves SCR-REQ-CAT-001 "search"
Honours: CON-CAT-016
Endpoint     : /api/v1/cat/catalogue-search   verb: GET
Layers       : CatSearchController.search → CatSearchQueryFacade.searchCatalogue (`@PreAuthorize("isAuthenticated()")` only — not the screen VIEW permission, so a caller without PERM_CAT_ITEMS_VIEW reaches the read service and is answered 200 with an empty list, never 403) → CatCatalogueReadService.searchCatalogue (checks PERM_CAT_ITEMS_VIEW of the current principal in code and returns an empty list when the caller lacks it; also called by CatalogueReadApi.searchCatalogue, so the HTTP path and the in-process path answer alike — ADR-CAT-061)
Request      : query {term: string(200) required}
Response     : 200 · ApiResponse<List<CatalogueSearchHit>> · paginated: no
Validations  : the caller's PERM_CAT_ITEMS_VIEW is checked first, in code by CatCatalogueReadService.searchCatalogue — without it the result is empty (REQ-CAT-127, ADR-CAT-045) · RULE-CAT-046 (term ≥ 2 characters) · ≤ 20 active Items (DBF-CAT-014) whose nameAr (DBF-CAT-003), nameEn (DBF-CAT-004), code (DBF-CAT-002) or active barcode (DBF-CAT-110, DBF-CAT-113) contains the term, exact code or barcode matches first
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-500-SERVER-ERROR, CAT-400-SEARCH-TERM-TOO-SHORT
Orchestration : permission check → term check → ranked search (QR-CAT-008) → return the hits
Repository   : QR-CAT-008 NATIVE · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW (absent → empty result, not 403)
Localization : message ar + en (CAT-400-SEARCH-TERM-TOO-SHORT)
<!-- API:API-CAT-047:END -->

<!-- API:API-CAT-052:START traces=REQ-CAT-073,REQ-CAT-074,REQ-CAT-076,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-013,DBF-CAT-025,DBF-CAT-097,DBF-CAT-111,DBF-CAT-112,DBF-CAT-141,DBF-CAT-151,DBF-CAT-154 -->
### API-CAT-052 — active values of a CAT lookup key
Entity       : ENT-CAT-001 · operation VIEW · serves SCR-REQ-CAT-001 "read" (the lookup-backed fields and filters) and SCR-REQ-CAT-006 "read"
Endpoint     : /api/v1/cat/lookups/{key}   verb: GET
Layers       : CatLookupController.read → CatLookupService.read (`@PreAuthorize("hasAnyAuthority(…)")` on the VIEW permission of any CAT page, own module only) → `com.erp.common.lookup.OwnedLookups.read` over `MdlLookupApi` — ADR-CAT-065
Request      : path {key: string(50) required} · one of the 10 CAT keys of SRS A6: CAT_ITEM_KIND, CAT_PUBLISH_STATE, CAT_BRAND, CAT_UOM, CAT_TAX_CLASS, CAT_EINVOICE_ITEM_CODE_TYPE, CAT_EINVOICE_UNIT, CAT_BARCODE_TYPE, CAT_SCALE_VALUE_TYPE, CAT_CHANGE_KIND
Response     : 200 · ApiResponse<List<LookupOption>> · paginated: no
Validations  : the key must be one of the 10 CAT keys (any other key, or one mdl answers 404 for, gives CAT-404-LOOKUP-KEY-UNKNOWN; never a pass-through — checklist C6) · only the active values, in mdl's sortOrder (REQ-CAT-073, REQ-CAT-074); a value deactivated later is no longer offered, while the records that hold it keep it (REQ-CAT-076) · interim (ADR-CAT-073): the answer carries no inactive value and `LookupOption` no activity flag, because erp-core 1.6.0 publishes only `MdlLookupApi.readActiveValuesByKey`; a screen renders a code that is not among the active values as the raw code. When `CORE-GAP-lookup-inactive-value-label.md` is answered, a delta adds an optional `codes` query parameter (those values, active or not) and `isActiveFl` to `LookupOption`
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-404-LOOKUP-KEY-UNKNOWN
Orchestration : OwnedLookups.read(key, CAT_LOOKUP_KEYS, CatErrorCodes.LOOKUP_KEY_UNKNOWN, mdlLookupApi::readActiveValuesByKey) → map each LookupOptionView to LookupOption {code, labelAr, labelEn, sortOrder} → return the list; no CAT table is read
Repository   : none — no database operation (the values are read through MdlLookupApi)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : the VIEW permission of any CAT page — PERM_CAT_ITEMS_VIEW, PERM_CAT_CATEGORIES_VIEW, PERM_CAT_VARIANT_ATTRIBUTES_VIEW, PERM_CAT_ITEM_IMPORT_VIEW, PERM_CAT_BARCODE_LABELS_VIEW or PERM_CAT_SETTINGS_VIEW (every CAT screen shows lookup-backed fields)
Localization : labels in ar and en as mdl stores them
<!-- API:API-CAT-052:END -->
<!-- SUB:SVC-API-SEARCH:END -->
