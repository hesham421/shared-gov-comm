<!-- source: PHASE:SVC-API / SUB:SVC-API-CRUD -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-CAT-001, DBF-CAT-002, DBF-CAT-003, DBF-CAT-004, DBF-CAT-005, DBF-CAT-006, DBF-CAT-007, DBF-CAT-008, DBF-CAT-009, DBF-CAT-010, DBF-CAT-011, DBF-CAT-012, DBF-CAT-013, DBF-CAT-014, DBF-CAT-015, DBF-CAT-017, DBF-CAT-020, DBF-CAT-022, DBF-CAT-023, DBF-CAT-024, DBF-CAT-025, DBF-CAT-026, DBF-CAT-027, DBF-CAT-028, DBF-CAT-030, DBF-CAT-033, DBF-CAT-035, DBF-CAT-036, DBF-CAT-037, DBF-CAT-038, DBF-CAT-039, DBF-CAT-040, DBF-CAT-042, DBF-CAT-047, DBF-CAT-048, DBF-CAT-049, DBF-CAT-050, DBF-CAT-051, DBF-CAT-052, DBF-CAT-053, DBF-CAT-055, DBF-CAT-061, DBF-CAT-062, DBF-CAT-063, DBF-CAT-064, DBF-CAT-071, DBF-CAT-072, DBF-CAT-073, DBF-CAT-074, DBF-CAT-075, DBF-CAT-076, DBF-CAT-077, DBF-CAT-079, DBF-CAT-084, DBF-CAT-085, DBF-CAT-086, DBF-CAT-087, DBF-CAT-088, DBF-CAT-089, DBF-CAT-096, DBF-CAT-097, DBF-CAT-098, DBF-CAT-099, DBF-CAT-100, DBF-CAT-101, DBF-CAT-102, DBF-CAT-104, DBF-CAT-109, DBF-CAT-110, DBF-CAT-111, DBF-CAT-112, DBF-CAT-113, DBF-CAT-114, DBF-CAT-121, DBF-CAT-122, DBF-CAT-123, DBF-CAT-124, DBF-CAT-125, DBF-CAT-126, DBF-CAT-127, DBF-CAT-128, DBF-CAT-129, DBF-CAT-131, DBF-CAT-136, DBF-CAT-137, DBF-CAT-138, DBF-CAT-139, DBF-CAT-140, DBF-CAT-141, DBF-CAT-142, DBF-CAT-143, DBF-CAT-145, DBF-CAT-150, DBF-CAT-151, DBF-CAT-152, DBF-CAT-153, DBF-CAT-154, DBF-CAT-155, DBF-CAT-157, REQ-CAT-001, REQ-CAT-002, REQ-CAT-004, REQ-CAT-005, REQ-CAT-006, REQ-CAT-007, REQ-CAT-008, REQ-CAT-009, REQ-CAT-010, REQ-CAT-011, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-015, REQ-CAT-016, REQ-CAT-017, REQ-CAT-018, REQ-CAT-019, REQ-CAT-020, REQ-CAT-024, REQ-CAT-025, REQ-CAT-026, REQ-CAT-027, REQ-CAT-028, REQ-CAT-029, REQ-CAT-030, REQ-CAT-031, REQ-CAT-032, REQ-CAT-033, REQ-CAT-034, REQ-CAT-035, REQ-CAT-042, REQ-CAT-043, REQ-CAT-044, REQ-CAT-045, REQ-CAT-046, REQ-CAT-047, REQ-CAT-048, REQ-CAT-049, REQ-CAT-050, REQ-CAT-051, REQ-CAT-052, REQ-CAT-053, REQ-CAT-054, REQ-CAT-055, REQ-CAT-056, REQ-CAT-057, REQ-CAT-058, REQ-CAT-059, REQ-CAT-060, REQ-CAT-061, REQ-CAT-062, REQ-CAT-063, REQ-CAT-064, REQ-CAT-065, REQ-CAT-066, REQ-CAT-067, REQ-CAT-069, REQ-CAT-070, REQ-CAT-071, REQ-CAT-072, REQ-CAT-073, REQ-CAT-074, REQ-CAT-075, REQ-CAT-076, REQ-CAT-077, REQ-CAT-078, REQ-CAT-079, REQ-CAT-080, REQ-CAT-081, REQ-CAT-082, REQ-CAT-083, REQ-CAT-084, REQ-CAT-085, REQ-CAT-087, REQ-CAT-088, REQ-CAT-089, REQ-CAT-091, REQ-CAT-092, REQ-CAT-093, REQ-CAT-094, REQ-CAT-096, REQ-CAT-098, REQ-CAT-099, REQ-CAT-105, REQ-CAT-106, REQ-CAT-107, REQ-CAT-108, REQ-CAT-110, REQ-CAT-111, REQ-CAT-112, REQ-CAT-113, REQ-CAT-114, REQ-CAT-116, REQ-CAT-117, REQ-CAT-118, REQ-CAT-119, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-123, REQ-CAT-124, REQ-CAT-128, REQ-CAT-129, REQ-CAT-130, REQ-CAT-131, REQ-CAT-132, REQ-CAT-133, REQ-CAT-134, REQ-CAT-135, REQ-CAT-136, REQ-CAT-137, REQ-CAT-138, REQ-CAT-140, REQ-CAT-141, REQ-CAT-142, REQ-CAT-143, REQ-CAT-144, REQ-CAT-145, REQ-CAT-146, REQ-CAT-147 -->
<!-- SUB:SVC-API-CRUD:START traces=REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,REQ-CAT-135,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-017,DBF-CAT-020,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-025,DBF-CAT-026,DBF-CAT-027,DBF-CAT-028,DBF-CAT-030,DBF-CAT-033,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-040,DBF-CAT-042,DBF-CAT-047,DBF-CAT-048,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053,DBF-CAT-055,DBF-CAT-061,DBF-CAT-062,DBF-CAT-063,DBF-CAT-064,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077,DBF-CAT-079,DBF-CAT-084,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089,DBF-CAT-096,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102,DBF-CAT-104,DBF-CAT-109,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-121,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129,DBF-CAT-131,DBF-CAT-136,DBF-CAT-137,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-145,DBF-CAT-150,DBF-CAT-151,DBF-CAT-152,DBF-CAT-153,DBF-CAT-154,DBF-CAT-155,DBF-CAT-157,REQ-CAT-001,REQ-CAT-002,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-091,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-096,REQ-CAT-098,REQ-CAT-099,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124 -->
### SUB SVC-API-CRUD

<!-- API:API-CAT-001:START traces=REQ-CAT-131,REQ-CAT-134,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-011,REQ-CAT-013,REQ-CAT-015,REQ-CAT-016,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-056,REQ-CAT-059,REQ-CAT-060,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-117,REQ-CAT-123,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-013,DBF-CAT-014,DBF-CAT-015,DBF-CAT-023,DBF-CAT-024,DBF-CAT-027,DBF-CAT-028,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089 -->
### API-CAT-001 — create Item
Entity       : ENT-CAT-001, ENT-CAT-002, ENT-CAT-007 · operation CREATE · serves SCR-REQ-CAT-001 "create"
Honours: CON-CAT-001, CON-CAT-002, CON-CAT-006, CON-CAT-018
Endpoint     : /api/v1/cat/items   verb: POST
Layers       : CatItemController.create → CatItemService.create
Request      : body ItemCreateRequest {nameAr: string(200) required DBF-CAT-003; nameEn: string(200) DBF-CAT-004; itemKindCode: string(50) DBF-CAT-005; baseUomCode: string(50) DBF-CAT-006; taxClassCode: string(50) DBF-CAT-008; categoryFk: int64 DBF-CAT-015; brandCode: string(50) DBF-CAT-007; einvoiceCodeTypeCode: string(50) DBF-CAT-009; einvoiceCode: string(100) DBF-CAT-010; descriptionAr: string(2000) DBF-CAT-011; descriptionEn: string(2000) DBF-CAT-012; code: string(20); bundleComponents: array{componentVariantFk: int64 required DBF-CAT-089; quantity: decimal in the component Item's base unit (> 0, ≤ 999999.999, multipleOf 0.001 — ADR-CAT-080) DBF-CAT-085; sortOrder: int32 DBF-CAT-086} optional, default []} · `bundleComponents` is optional and must hold ≥ 1 line only when itemKindCode is BUNDLE (RULE-CAT-018); a STANDARD Item is created from nameAr alone (REQ-CAT-024) · excluded system fields: itemPk, publishStateCode, isActiveFl, tenantId, version, audit
Response     : 201 · ApiResponse<ItemDetail> · paginated: no
Validations  : request consistency first (PHASE CORE, API conventions — CAT-400-REQUEST-INVALID, nothing written): a non-empty `bundleComponents` on a STANDARD Item (itemKindCode STANDARD or omitted), or the same `componentVariantFk` twice in it; a line `quantity` outside the document's bounds (> 0, ≤ 999999.999, multipleOf 0.001 — PHASE CORE, request numbers) answers CAT-400-REQUEST-INVALID too · RULE-CAT-008 / RULE-CAT-009 (itemKindCode BUNDLE needs `bundles` on and readable) · RULE-CAT-001, RULE-CAT-002 (names) · RULE-CAT-004 (a supplied `code` is rejected) · RULE-CAT-006 (itemKindCode, baseUomCode, taxClassCode, brandCode, einvoiceCodeTypeCode active in their keys) · RULE-CAT-042 (e-invoice pair and format) · RULE-CAT-026 (categoryFk active) · RULE-CAT-003 (no other active Item with the normalised nameAr) · RULE-CAT-018, RULE-CAT-019 (kind BUNDLE: ≥ 1 line, quantity > 0 with ≤ 3 decimals, component Variants active, of STANDARD Items, listed once) · RULE-CAT-005 (series must issue) · RULE-CAT-049 (event only after commit)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ITEM-NAME-DUPLICATE, CAT-422-CODE-SYSTEM-ASSIGNED, CAT-500-CODE-NOT-ISSUED, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-BUNDLE-COMPONENTS-REQUIRED, CAT-422-BUNDLE-COMPONENT-INVALID, CAT-422-CATEGORY-INACTIVE, CAT-422-EINVOICE-CODE-INVALID, CAT-500-CHANGE-NOT-SAVED
Orchestration : load flags when itemKindCode is BUNDLE (CatCapabilityFlags) → apply defaults itemKindCode STANDARD (DBF-CAT-005), baseUomCode PIECE (DBF-CAT-006), taxClassCode STANDARD (DBF-CAT-008) → ItemDomain.create() checks names, code and the e-invoice pair (RULE-CAT-001, 002, 004, 042) → the service checks lookups (MdlLookupApi), Category (QR-CAT-032, FOR SHARE), name uniqueness (QR-CAT-002), BUNDLE lines (QR-CAT-017, FOR SHARE; each quantity through BundleComponentDomain.create()) → allocate the Item code from series CAT_ITEM (NumberSeriesApi) into DBF-CAT-002 → set publishStateCode UNPUBLISHED (DBF-CAT-013) and isActiveFl true (DBF-CAT-014) → persist the Item (QR-CAT-003, CAT_ITEM, identity ITEM_PK) → allocate the Variant code from series CAT_VARIANT into DBF-CAT-023 and persist the default Variant with isDefaultFl true (DBF-CAT-024), isActiveFl true (DBF-CAT-027), itemFk (DBF-CAT-028) (QR-CAT-011) → BUNDLE: persist each line with bundleItemFk (DBF-CAT-088), isActiveFl true (DBF-CAT-087), quantity default 1 (DBF-CAT-085), sortOrder default line order (DBF-CAT-086) (QR-CAT-040) → record cat.CatalogueChanged CREATED for the Item, the Variant and each line (OutboxPublisher) → return ItemDetail
Repository   : QR-CAT-032 FIND_ONE · QR-CAT-002 EXISTS · QR-CAT-017 FIND_ALL · QR-CAT-003 SAVE · QR-CAT-011 SAVE · QR-CAT-040 SAVE · join NONE · READ_WRITE
Concurrency  : codes — NumberSeriesApi allocates atomically, UQ_CAT_ITEM_CODE / UQ_CAT_VARIANT_CODE back it; name — two parallel creates both pass QR-CAT-002, the second insert hits UQ_CAT_ITEM_NAME_AR and is translated to CAT-409-ITEM-NAME-DUPLICATE; Category and component Variants/Items are read FOR SHARE, so a parallel deactivation (FOR UPDATE) waits or fails first (ADR-CAT-047)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : messages ar + en from the Error Catalog; nameAr mandatory, nameEn optional, displayNameEn falls back to nameAr
<!-- API:API-CAT-001:END -->

<!-- API:API-CAT-002:START traces=REQ-CAT-132,REQ-CAT-001,REQ-CAT-002,REQ-CAT-010,REQ-CAT-029,REQ-CAT-058,REQ-CAT-119,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-014,DBF-CAT-022,DBF-CAT-027,DBF-CAT-062,DBF-CAT-064,DBF-CAT-088,DBF-CAT-089,DBF-CAT-102,DBF-CAT-114,DBF-CAT-128 -->
### API-CAT-002 — read Item
Entity       : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-007
Endpoint     : /api/v1/cat/items/{id}   verb: GET
Layers       : CatItemController.get → CatItemQueryFacade.getItem (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getItem (also called by CatalogueReadApi.getItem, with no CAT permission check — ADR-CAT-061)
Request      : path {id: int64 required DBF-CAT-001}
Response     : 200 · ApiResponse<ItemDetail> · paginated: no
Validations  : none — an inactive Item is still returned with isActiveFl false; a committed change is visible to every read that starts after the commit (REQ-CAT-010); an itemPk of another tenant reads as CAT-404-NOT-FOUND (REQ-CAT-132)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the Item (QR-CAT-001) → its active Variants (QR-CAT-013) with their assignments and value names (QR-CAT-019) → derive Variant nameAr / nameEn, displayNameEn and the effective e-invoice pair → active Item units (QR-CAT-044) with their e-invoice units (QR-CAT-070) → active barcodes of the Variants (QR-CAT-047) → active Bundle lines with component codes and the base unit of each component Item, the unit of the line quantity (QR-CAT-039 — ADR-CAT-080) → active media, primary first (QR-CAT-054) → return ItemDetail
Repository   : QR-CAT-001 FIND_ONE · QR-CAT-013 FIND_ALL · QR-CAT-019 FIND_ALL · QR-CAT-044 FIND_ALL · QR-CAT-070 FIND_ALL · QR-CAT-047 FIND_ALL · QR-CAT-039 FIND_ALL · QR-CAT-054 FIND_ALL · join ADR-CAT-047 (QR-CAT-019) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : names in ar and en; codes returned as stored, labels resolved by the frontend
<!-- API:API-CAT-002:END -->

<!-- API:API-CAT-003:START traces=REQ-CAT-135,REQ-CAT-146,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-004,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-030,REQ-CAT-031,REQ-CAT-034,REQ-CAT-063,REQ-CAT-072,REQ-CAT-073,REQ-CAT-075,REQ-CAT-076,REQ-CAT-082,REQ-CAT-117,REQ-CAT-123,DBF-CAT-001,DBF-CAT-002,DBF-CAT-003,DBF-CAT-004,DBF-CAT-005,DBF-CAT-006,DBF-CAT-007,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-011,DBF-CAT-012,DBF-CAT-015,DBF-CAT-017,DBF-CAT-020 -->
### API-CAT-003 — update Item
Entity       : ENT-CAT-001 · operation UPDATE · serves SCR-REQ-CAT-001 "update"
Honours: CON-CAT-001, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}   verb: PUT
Layers       : CatItemController.update → CatItemService.update
Request      : path {id: int64 required DBF-CAT-001} · body ItemUpdateRequest {nameAr: string(200) required DBF-CAT-003; nameEn: string(200) DBF-CAT-004; categoryFk: int64 DBF-CAT-015; brandCode: string(50) DBF-CAT-007; taxClassCode: string(50) required DBF-CAT-008; einvoiceCodeTypeCode: string(50) DBF-CAT-009; einvoiceCode: string(100) DBF-CAT-010; descriptionAr: string(2000) DBF-CAT-011; descriptionEn: string(2000) DBF-CAT-012; version: int64 required DBF-CAT-017; code: string(20); itemKindCode: string(50); baseUomCode: string(50)} · code / itemKindCode / baseUomCode are echoes only, rejected when they differ from the stored value · excluded system fields: itemPk, publishStateCode, isActiveFl, tenantId, audit
Response     : 200 · ApiResponse<ItemDetail> · paginated: no
Validations  : RULE-CAT-001, RULE-CAT-002 · RULE-CAT-004 (code changed) · RULE-CAT-022 (itemKindCode changed) · RULE-CAT-007 (baseUomCode changed) · RULE-CAT-006 (only changed lookup fields; an unchanged value deactivated later is kept — REQ-CAT-076) · RULE-CAT-042 · RULE-CAT-026 (changed categoryFk) · RULE-CAT-003 (excluding this Item) · RULE-CAT-049 · the echoed version must equal the stored one (CAT-409-STALE-VERSION — REQ-CAT-133) · an itemPk of another tenant answers CAT-404-NOT-FOUND and changes nothing (REQ-CAT-132) · RULE-CAT-053 (on an inactive Item the only change accepted is categoryFk: every other field of the body must equal the stored value, else CAT-422-PARENT-INACTIVE; a categoryFk-only change is the repair of RULE-CAT-026, and on this path only RULE-CAT-026 and the version check run: RULE-CAT-001, 002, 003, 006 and 042 judge fields that do not change and are not evaluated, so a nameAr that another active Item took meanwhile does not block the repair of A7 invariant 2; RULE-CAT-003 is checked again when the Item is reactivated (API-CAT-005, REQ-CAT-144) — REQ-CAT-135, REQ-CAT-146, ADR-CAT-063, ADR-CAT-068)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-422-CODE-SYSTEM-ASSIGNED, CAT-422-ITEM-KIND-FIXED, CAT-422-BASE-UOM-FIXED, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-EINVOICE-CODE-INVALID, CAT-422-CATEGORY-INACTIVE, CAT-409-ITEM-NAME-DUPLICATE, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) → when it is inactive (DBF-CAT-014): compare every body field with the stored value and refuse any change other than categoryFk (DBF-CAT-015) with RULE-CAT-053; a categoryFk-only change goes straight to the Category check (QR-CAT-032 FOR SHARE, RULE-CAT-026) and the write with the version check, skipping ItemDomain.from and the lookup and name checks below, and records cat.CatalogueChanged UPDATED (ADR-CAT-068) → ItemDomain.from(stored, request) compares the echoed code / itemKindCode / baseUomCode (RULE-CAT-004, 022, 007) and checks names and the e-invoice pair (RULE-CAT-001, 002, 042) → the service validates the changed fields (MdlLookupApi; Category QR-CAT-032 FOR SHARE; name QR-CAT-002) → write nameAr, nameEn, categoryFk, brandCode, taxClassCode, einvoiceCodeTypeCode, einvoiceCode, descriptionAr, descriptionEn with the version check (QR-CAT-004; the platform sets updatedBy DBF-CAT-020) → record cat.CatalogueChanged UPDATED → return ItemDetail
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-032 FIND_ONE · QR-CAT-002 EXISTS · QR-CAT-004 UPDATE · join NONE · READ_WRITE
Concurrency  : lost update — the version check (optimistic lock on DBF-CAT-017) makes the second of two parallel updates fail with CAT-409-STALE-VERSION; name — UQ_CAT_ITEM_NAME_AR translated to CAT-409-ITEM-NAME-DUPLICATE; the Item lock orders the update against a parallel deactivation or reactivation, so RULE-CAT-053 and its categoryFk-only exception decide on the committed state
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-003:END -->

<!-- API:API-CAT-004:START traces=REQ-CAT-131,REQ-CAT-005,REQ-CAT-062,DBF-CAT-001,DBF-CAT-013,DBF-CAT-014,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089 -->
### API-CAT-004 — deactivate Item
Entity       : ENT-CAT-001 · operation DELETE · serves SCR-REQ-CAT-001 "deactivate"
Honours: CON-CAT-001, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}   verb: DELETE
Layers       : CatItemController.deactivate → CatItemService.deactivate
Request      : path {id: int64 required DBF-CAT-001}
Response     : 200 · ApiResponse<ItemSummary> · paginated: no
Validations  : RULE-CAT-021 (no Variant of this Item is an active component of an active Bundle) · RULE-CAT-048 (soft deactivation, success notice) · RULE-CAT-049; the publish-state effect of rule 041 (deactivation sets UNPUBLISHED) is applied in the orchestration and raises nothing · the Item's Variants and their barcodes are left active (REQ-CAT-005): while the Item is inactive its barcodes no longer resolve (RULE-CAT-047, ADR-CAT-059) but still hold their values under RULE-CAT-029, so the Item can be reactivated with them; the owner releases a GTIN with API-CAT-016, which stays allowed on an inactive Item
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-VARIANT-IN-ACTIVE-BUNDLE, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) → count active Bundles holding any of its Variants (QR-CAT-042) → set isActiveFl false (DBF-CAT-014) and publishStateCode UNPUBLISHED (DBF-CAT-013) (QR-CAT-004); its Variants, assignments and barcodes are not touched → record cat.CatalogueChanged DEACTIVATED → read the Item's Category, when categoryFk is set (QR-CAT-032), for the derived categoryCode / categoryNameAr / categoryNameEn (ADR-CAT-075) → return ItemSummary with CAT-200-DEACTIVATED; an already inactive Item returns the same success
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-042 COUNT · QR-CAT-004 UPDATE · QR-CAT-032 FIND_ONE · join NONE · READ_WRITE
Concurrency  : the Item row lock (QR-CAT-010) is decided on before the Bundle count; a parallel Bundle save reads the component Items FOR SHARE (QR-CAT-017), so the two never both pass (ADR-CAT-047)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : success notice ar + en (CAT-200-DEACTIVATED)
<!-- API:API-CAT-004:END -->

<!-- API:API-CAT-005:START traces=REQ-CAT-130,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-006,REQ-CAT-113,REQ-CAT-114,REQ-CAT-116,DBF-CAT-001,DBF-CAT-003,DBF-CAT-013,DBF-CAT-014,DBF-CAT-027,DBF-CAT-028 -->
### API-CAT-005 — reactivate, publish or unpublish Item
Entity       : ENT-CAT-001 · operation UPDATE · serves SCR-REQ-CAT-001 "reactivate", "publish", "unpublish"
Honours: CON-CAT-001, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}   verb: PATCH
Layers       : CatItemController.changeState → CatItemService.reactivate / publish / unpublish
Request      : path {id: int64 required DBF-CAT-001} · body ItemStateRequest {isActiveFl: boolean DBF-CAT-014; publishStateCode: string(50) DBF-CAT-013} · exactly one of the two; isActiveFl accepts only true (deactivation is DELETE) — ADR-CAT-048 · schema: `isActiveFl` enum [true]; `oneOf` [required isActiveFl, required publishStateCode] with `minProperties: 1`, `maxProperties: 1` and no other property, so an empty body, a body with both fields or `isActiveFl: false` fails the document check (CAT-400-REQUEST-INVALID) before the service runs
Response     : 200 · ApiResponse<ItemDetail> · paginated: no
Validations  : reactivate — the ACTIVE-state invariants of SRS A7 are re-established (ADR-CAT-059), in this order: RULE-CAT-050 (≥ 1 active Variant), RULE-CAT-026 (categoryFk empty or an active Category), RULE-CAT-019 (kind BUNDLE: every active line names an active Variant of an active STANDARD Item), RULE-CAT-003 (the normalised name is still unique among active Items); each refusal names a repair that stays allowed on the inactive Item (ADR-CAT-068): RULE-CAT-050 → generate Variants (API-CAT-006), RULE-CAT-026 → change only categoryFk (API-CAT-003) or reactivate the Category (API-CAT-053), RULE-CAT-019 → replace the Bundle components (API-CAT-017), RULE-CAT-003 → rename or deactivate the other active Item, whose code the message names (A7 invariant 4; the Item's own name cannot change while it is inactive — AC-CAT-162) · publish — RULE-CAT-006 (publishStateCode an active CAT_PUBLISH_STATE value), RULE-CAT-041 (Item active and ≥ 1 active Variant) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ITEM-NAME-DUPLICATE, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-PUBLISH-BLOCKED, CAT-422-ITEM-NO-ACTIVE-VARIANT, CAT-422-CATEGORY-INACTIVE, CAT-422-BUNDLE-COMPONENT-INVALID, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) → reactivate: count its active Variants (QR-CAT-015), read its Category FOR SHARE (QR-CAT-032), for a BUNDLE read the active lines (QR-CAT-039) and their Variants and Items FOR SHARE (QR-CAT-017), check the name (QR-CAT-002), set isActiveFl true (DBF-CAT-014), keep publishStateCode UNPUBLISHED (DBF-CAT-013), record REACTIVATED → publish: count active Variants (QR-CAT-015), set PUBLISHED (DBF-CAT-013), record UPDATED → unpublish: set UNPUBLISHED (DBF-CAT-013), isActiveFl unchanged, record UPDATED → write (QR-CAT-004) → return ItemDetail
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-015 COUNT · QR-CAT-032 FIND_ONE (FOR SHARE) · QR-CAT-039 FIND_ALL · QR-CAT-017 FIND_ALL (FOR SHARE) · QR-CAT-002 EXISTS · QR-CAT-004 UPDATE · join ADR-CAT-047 (QR-CAT-017, QR-CAT-039) · READ_WRITE
Concurrency  : publish decides on "≥ 1 active Variant" under the Item lock; Variant deactivation (API-CAT-009) takes the same lock, so an Item is never published with no active Variant; reactivation decides on its Variants under the Item lock and reads its Category and its component Variants / Items FOR SHARE, so a parallel deactivation of any of them (FOR UPDATE) cannot interleave; reactivation name clash backed by UQ_CAT_ITEM_NAME_AR
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-005:END -->

<!-- API:API-CAT-006:START traces=REQ-CAT-145,REQ-CAT-129,REQ-CAT-141,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-033,REQ-CAT-035,REQ-CAT-049,REQ-CAT-051,REQ-CAT-052,REQ-CAT-061,DBF-CAT-005,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-027,DBF-CAT-028,DBF-CAT-061,DBF-CAT-062,DBF-CAT-063,DBF-CAT-064,DBF-CAT-114 -->
### API-CAT-006 — generate Variants
Entity       : ENT-CAT-002, ENT-CAT-005 · operation CREATE · serves SCR-REQ-CAT-001 "generate Variants"
Honours: CON-CAT-002, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}/variants   verb: POST
Layers       : CatVariantController.generate → CatVariantService.generate
Request      : path {id: int64 required DBF-CAT-001} · body VariantGenerationRequest {selections: array (minItems 1, maxItems 3){variantAttributeFk: int64 required DBF-CAT-063; variantAttributeValueFks: array<int64> (minItems 1, maxItems 300) required DBF-CAT-064}}
Response     : 201 · ApiResponse<VariantGenerationResult> · paginated: no
Validations  : request consistency first (PHASE CORE, API conventions — CAT-400-REQUEST-INVALID, nothing written): the same `variantAttributeFk` in two `selections`, the same id twice in one `variantAttributeValueFks`, or a value id that is not a value of its selection's attribute (read with QR-CAT-031) — so neither UQ_CAT_VAR_ATTR_ASSIGN_ATTR nor FK_CAT_VAR_ATTR_ASSIGN_CAT_VAR_ATTR_VALUE is ever reached by a malformed request, and if either rejects a write all the same it is translated to RULE-CAT-013 (CAT-409-VARIANT-COMBINATION-DUPLICATE), never CAT-500 · an empty `selections`, or a selection whose `variantAttributeValueFks` is empty, answers CAT-400-REQUEST-INVALID before any rule runs (the document check of `minItems: 1` on both arrays, repeated by the service) — so a request that names no value never reaches RULE-CAT-052 or RULE-CAT-014, also on an Item with no attribute in use · RULE-CAT-008 / RULE-CAT-009 (`variants`) · allowed on an inactive Item: generation is the repair of RULE-CAT-050, so RULE-CAT-053 does not apply here and the rules below decide alone (REQ-CAT-145, ADR-CAT-068); on an inactive Item with 0 active Variants, RULE-CAT-052 requires no attribute and RULE-CAT-012 assigns nothing · RULE-CAT-020 (Item kind must be STANDARD) · selected attributes and values must be active (an inactive one is not offered — CAT-400-REQUEST-INVALID) · RULE-CAT-052 (every attribute the Item's active Variants already carry is in the selections with ≥ 1 value — checked first) · a `selections` of more than 3 items, or a value list of more than 300, fails the document check (`maxItems`) with CAT-400-REQUEST-INVALID · RULE-CAT-014 (≤ 3 attributes used, ≤ 300 active Variants after), decided arithmetically before any combination is built: the attributes used after the generation are counted as the union of the attributes the active Variants carry and the selected ones, and the active Variants after it as the current active count plus the product of the selected value counts minus the active Variants whose values all fall inside the selection (counted from QR-CAT-019, at most 300 rows) — no combination is materialised, no code drawn and nothing written when the limit is passed · RULE-CAT-012 (existing Variants first take the first selected value of each newly used attribute — success notice) · RULE-CAT-013 (one value per attribute; a request that names exactly one combination already carried by an active Variant is rejected; in a multi-value selection existing combinations are skipped — REQ-CAT-049) · RULE-CAT-005 · RULE-CAT-049; new Variants carry no barcode (REQ-CAT-019) and the existing ones keep variantPk, code and barcodes (REQ-CAT-017) · default flag (REQ-CAT-141, ADR-CAT-072): when the Item has 0 active Variants, the first Variant created (the lowest new code) takes isDefaultFl and the inactive former default loses it; when the Item has active Variants, the default stays where it is
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-BUNDLE-SINGLE-VARIANT, CAT-422-VARIANT-ATTRIBUTE-MISSING, CAT-422-VARIANT-LIMIT-EXCEEDED, CAT-409-VARIANT-COMBINATION-DUPLICATE, CAT-201-EXISTING-VARIANTS-KEPT, CAT-500-CODE-NOT-ISSUED, CAT-500-CHANGE-NOT-SAVED
Orchestration : read `variants` → lock the Item (QR-CAT-010) and check itemKindCode (DBF-CAT-005); an inactive Item (DBF-CAT-014) is accepted and stays inactive — the owner reactivates it afterwards (ADR-CAT-068) → load the selected attributes and values (QR-CAT-026, QR-CAT-031 FOR SHARE) → load the active Variants (QR-CAT-013) and their assignments (QR-CAT-019) → check that every attribute the active Variants carry is selected with ≥ 1 value (RULE-CAT-052) → count (QR-CAT-015) and check the limits arithmetically (the product of the selected value counts, not a list of combinations — RULE-CAT-014), refusing before any combination is built → for each existing active Variant persist an assignment of the first selected value of every newly used attribute: variantFk (DBF-CAT-062), variantAttributeFk (DBF-CAT-063), variantAttributeValueFk (DBF-CAT-064), isActiveFl true (DBF-CAT-061) (QR-CAT-018) → for each remaining combination allocate a code from CAT_VARIANT into DBF-CAT-023, persist the Variant with isDefaultFl false (DBF-CAT-024), isActiveFl true (DBF-CAT-027), itemFk (DBF-CAT-028) (QR-CAT-011) and its assignments (QR-CAT-018) → when the Item had 0 active Variants before this generation: clear isDefaultFl on the Item's former default entity and flush (QR-CAT-075, DBF-CAT-024), then set isDefaultFl true on the first Variant created (QR-CAT-014) — in that order, with the flush between the two writes, so UQ_CAT_VARIANT_DEFAULT holds after each statement (ADR-CAT-072) → record cat.CatalogueChanged CREATED for every new Variant and assignment, and UPDATED for the former default whose flag was cleared → return the Item's Variants
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-026 FIND_ALL · QR-CAT-031 FIND_ALL (FOR SHARE) · QR-CAT-013 FIND_ALL · QR-CAT-019 FIND_ALL · QR-CAT-015 COUNT · QR-CAT-018 SAVE · QR-CAT-011 SAVE · QR-CAT-075 UPDATE · QR-CAT-014 UPDATE · join ADR-CAT-047 · READ_WRITE
Concurrency  : two generations on one Item would both pass the 300 limit and the combination check — the Item row lock (QR-CAT-010) serialises them, and orders a generation against a parallel reactivation of the same Item (API-CAT-005 counts the Variants under that lock); a parallel deactivation of a selected value waits on its FOR SHARE read; UQ_CAT_VAR_ATTR_ASSIGN_ATTR backs one value per attribute; codes are allocated atomically; the default-flag move is decided under the same Item lock, so a parallel Variant deactivation cannot leave two defaults or none
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : derived Variant names in ar and en; notice ar + en (CAT-201-EXISTING-VARIANTS-KEPT)
<!-- API:API-CAT-006:END -->

<!-- API:API-CAT-007:START traces=REQ-CAT-001,REQ-CAT-029,REQ-CAT-050,REQ-CAT-119,REQ-CAT-122,DBF-CAT-006,DBF-CAT-008,DBF-CAT-009,DBF-CAT-010,DBF-CAT-022,DBF-CAT-023,DBF-CAT-024,DBF-CAT-025,DBF-CAT-026,DBF-CAT-027,DBF-CAT-028,DBF-CAT-154 -->
### API-CAT-007 — read Variant
Entity       : ENT-CAT-002, ENT-CAT-005 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-008
Endpoint     : /api/v1/cat/variants/{id}   verb: GET
Layers       : CatVariantController.get → CatVariantQueryFacade.getVariant (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getVariant (also called by CatalogueReadApi.getVariant, with no CAT permission check — ADR-CAT-061) — ADR-CAT-045
Request      : path {id: int64 required DBF-CAT-022}
Response     : 200 · ApiResponse<VariantDetail> · paginated: no
Validations  : none — an inactive Variant is returned with isActiveFl false (REQ-CAT-050)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the Variant (QR-CAT-012) and its Item (QR-CAT-001) → assignments and value names (QR-CAT-019) → derived nameAr / nameEn → active barcodes (QR-CAT-047) → effective e-invoice pair (override DBF-CAT-025 / DBF-CAT-026, else Item DBF-CAT-009 / DBF-CAT-010), Item taxClassCode (DBF-CAT-008) and baseUomCode (DBF-CAT-006) → e-invoice unit of the base unit (QR-CAT-070; empty when none is active) → return VariantDetail
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-001 FIND_ONE · QR-CAT-019 FIND_ALL · QR-CAT-047 FIND_ALL · QR-CAT-070 FIND_ALL · join ADR-CAT-047 · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : derived names in ar and en
<!-- API:API-CAT-007:END -->

<!-- API:API-CAT-008:START traces=REQ-CAT-135,REQ-CAT-034,REQ-CAT-118,REQ-CAT-123,DBF-CAT-022,DBF-CAT-023,DBF-CAT-025,DBF-CAT-026,DBF-CAT-030,DBF-CAT-033 -->
### API-CAT-008 — update Variant e-invoice override
Entity       : ENT-CAT-002 · operation UPDATE · serves SCR-REQ-CAT-001 "e-invoice override"
Honours: CON-CAT-002, CON-CAT-018
Endpoint     : /api/v1/cat/variants/{id}   verb: PUT
Layers       : CatVariantController.update → CatVariantService.updateOverride
Request      : path {id: int64 required DBF-CAT-022} · body VariantOverrideRequest {einvoiceCodeTypeCode: string(50) DBF-CAT-025; einvoiceCode: string(100) DBF-CAT-026; version: int64 required DBF-CAT-030; code: string(20)} · code is an echo only, rejected when it differs
Response     : 200 · ApiResponse<VariantDetail> · paginated: no
Validations  : RULE-CAT-053 (the Variant and its Item must be active) · RULE-CAT-004 (code changed) · RULE-CAT-006 (einvoiceCodeTypeCode) · RULE-CAT-042 (both or neither; GS1 8–14 digits; EGS starts with "EG-", ≤ 100) · RULE-CAT-049; both empty removes the override
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CODE-SYSTEM-ASSIGNED, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-EINVOICE-CODE-INVALID, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the Variant (QR-CAT-012) → lock its Item (QR-CAT-010; itemFk DBF-CAT-028) and refuse an inactive Variant (DBF-CAT-027) or Item (DBF-CAT-014) — RULE-CAT-053 → validate → write einvoiceCodeTypeCode (DBF-CAT-025) and einvoiceCode (DBF-CAT-026) with the version check (QR-CAT-014; updatedBy DBF-CAT-033 by the platform) → record cat.CatalogueChanged UPDATED → return VariantDetail
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-014 UPDATE · join NONE · READ_WRITE
Concurrency  : lost update — optimistic version (DBF-CAT-030) → CAT-409-STALE-VERSION; the Item lock orders it against a parallel Variant or Item deactivation (RULE-CAT-053)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-008:END -->

<!-- API:API-CAT-009:START traces=REQ-CAT-140,REQ-CAT-050,REQ-CAT-053,REQ-CAT-062,DBF-CAT-022,DBF-CAT-024,DBF-CAT-027,DBF-CAT-028,DBF-CAT-014,DBF-CAT-061,DBF-CAT-062,DBF-CAT-089,DBF-CAT-113,DBF-CAT-114 -->
### API-CAT-009 — deactivate Variant
Entity       : ENT-CAT-002, ENT-CAT-005, ENT-CAT-009 · operation DELETE · serves SCR-REQ-CAT-001 "deactivate Variant"
Honours: CON-CAT-002, CON-CAT-018
Endpoint     : /api/v1/cat/variants/{id}   verb: DELETE
Layers       : CatVariantController.deactivate → CatVariantService.deactivate
Request      : path {id: int64 required DBF-CAT-022}
Response     : 200 · ApiResponse<VariantDetail> · paginated: no
Validations  : RULE-CAT-015 (not the last active Variant of an active Item) · RULE-CAT-021 (not an active component of an active Bundle) · RULE-CAT-048 · RULE-CAT-049; the Variant's active barcodes are deactivated with it, which releases their values for RULE-CAT-029 (REQ-CAT-050, ADR-CAT-063); allowed on an inactive Item, so its Variants can be cleaned up · default flag (REQ-CAT-140, ADR-CAT-072): when the Variant being deactivated carries isDefaultFl and another Variant of the Item is active, isDefaultFl moves, in the same transaction, to the remaining active Variant with the lowest code; when it was the Item's last active Variant (an inactive Item), the flag stays on it until a generation moves it (API-CAT-006)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-LAST-ACTIVE-VARIANT, CAT-409-VARIANT-IN-ACTIVE-BUNDLE, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the Variant (QR-CAT-012) → lock its Item (QR-CAT-010; reads isActiveFl DBF-CAT-014) → count active Variants (QR-CAT-015) → count active Bundles holding it (QR-CAT-042) → set isActiveFl false (DBF-CAT-027) on the Variant entity (QR-CAT-014) → load its active assignments as entities and set each one's isActiveFl false (DBF-CAT-061) (QR-CAT-020) → load its active barcodes (DBF-CAT-113, by variantFk DBF-CAT-114) as entities and set each one's isActiveFl false (QR-CAT-072) — every row is saved as an entity, so the given @Audited and AuditableEntity hooks record each change and every recordPk is known (no bulk update) → when the Variant carried isDefaultFl (DBF-CAT-024) and the Item keeps an active Variant: read the remaining active Variants (QR-CAT-013, ordered by code after the default), clear isDefaultFl on the deactivated Variant and flush (QR-CAT-075), then set it on the remaining active Variant with the lowest code (QR-CAT-014) — in that order, with the flush between the two writes, so UQ_CAT_VARIANT_DEFAULT holds after each statement (ADR-CAT-072) → record cat.CatalogueChanged DEACTIVATED for the Variant, for each assignment entity and for each barcode entity (one event per record, with its recordPk — RULE-CAT-049), and UPDATED for the Variant that became the default → return VariantDetail with CAT-200-DEACTIVATED
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-015 COUNT · QR-CAT-042 COUNT · QR-CAT-014 UPDATE · QR-CAT-020 UPDATE · QR-CAT-072 UPDATE · QR-CAT-013 FIND_ALL · QR-CAT-075 UPDATE · join NONE · READ_WRITE
Concurrency  : two parallel deactivations of the last two Variants would each see two active — the Item row lock serialises them, so the second sees one and is refused; the default-flag move is decided under the same lock, so the surviving Variant ends as the one default (AC-CAT-146, ADR-CAT-072); a parallel Bundle save reads the Variant FOR SHARE
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : success notice ar + en (CAT-200-DEACTIVATED)
<!-- API:API-CAT-009:END -->

<!-- API:API-CAT-010:START traces=REQ-CAT-135,REQ-CAT-074,REQ-CAT-075,REQ-CAT-077,REQ-CAT-080,DBF-CAT-006,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102 -->
### API-CAT-010 — add Item unit
Entity       : ENT-CAT-008 · operation CREATE · serves SCR-REQ-CAT-001 "add"
Honours: CON-CAT-004, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}/units   verb: POST
Layers       : CatItemUnitController.create → CatItemUnitService.create
Request      : path {id: int64 required DBF-CAT-001} · body ItemUnitCreateRequest {uomCode: string(50) required DBF-CAT-097; conversionFactor: decimal (> 0, ≤ 999999.999999, multipleOf 0.000001) required DBF-CAT-098; isPurchaseFl: boolean DBF-CAT-099; isSalesFl: boolean DBF-CAT-100}
Response     : 201 · ApiResponse<ItemUnitView> · paginated: no
Validations  : a conversionFactor outside the document's bounds (> 0, ≤ 999999.999999, multipleOf 0.000001 — PHASE CORE, request numbers) answers CAT-400-REQUEST-INVALID before any rule runs · RULE-CAT-053 (the Item must be active) · RULE-CAT-006 (uomCode active in CAT_UOM) · RULE-CAT-027 (differs from the Item's baseUomCode DBF-CAT-006 and from its other active units; factor > 0, ≤ 999,999.999999, with ≤ 6 decimals) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-ITEM-UNIT-INVALID, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) and refuse an inactive one (DBF-CAT-014, RULE-CAT-053) → load its active units (QR-CAT-044) → validate → default isPurchaseFl / isSalesFl true (DBF-CAT-099 / DBF-CAT-100) → persist with itemFk (DBF-CAT-102) and isActiveFl true (DBF-CAT-101) (QR-CAT-045) → record cat.CatalogueChanged CREATED → return ItemUnitView
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-044 FIND_ALL · QR-CAT-045 SAVE · join NONE · READ_WRITE
Concurrency  : two parallel adds of the same unit both pass the check — UQ_CAT_ITEM_UNIT_UOM rejects the second, translated to CAT-422-ITEM-UNIT-INVALID; the Item lock orders them
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-010:END -->

<!-- API:API-CAT-011:START traces=REQ-CAT-079,REQ-CAT-122,DBF-CAT-096,DBF-CAT-097,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-101,DBF-CAT-102,DBF-CAT-154 -->
### API-CAT-011 — read Item unit
Entity       : ENT-CAT-008 · operation VIEW · serves SCR-REQ-CAT-001 "read"
Honours: CON-CAT-009
Endpoint     : /api/v1/cat/item-units/{id}   verb: GET
Layers       : CatItemUnitController.get → CatItemUnitQueryFacade.getItemUnit (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getItemUnit (also called by CatalogueReadApi.getItemUnit, with no CAT permission check — ADR-CAT-061) — ADR-CAT-045
Request      : path {id: int64 required DBF-CAT-096}
Response     : 200 · ApiResponse<ItemUnitView> · paginated: no
Validations  : none — an inactive unit is still returned (REQ-CAT-079)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load the unit (QR-CAT-043) → its e-invoice unit (QR-CAT-070; empty when none is active) → return ItemUnitView
Repository   : QR-CAT-043 FIND_ONE · QR-CAT-070 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_VIEW
Localization : codes returned as stored
<!-- API:API-CAT-011:END -->

<!-- API:API-CAT-012:START traces=REQ-CAT-135,REQ-CAT-078,REQ-CAT-081,DBF-CAT-096,DBF-CAT-098,DBF-CAT-099,DBF-CAT-100,DBF-CAT-104 -->
### API-CAT-012 — update Item unit
Entity       : ENT-CAT-008 · operation UPDATE · serves SCR-REQ-CAT-001 "update"
Honours: CON-CAT-004, CON-CAT-018
Endpoint     : /api/v1/cat/item-units/{id}   verb: PUT
Layers       : CatItemUnitController.update → CatItemUnitService.update
Request      : path {id: int64 required DBF-CAT-096} · body ItemUnitUpdateRequest {isPurchaseFl: boolean required DBF-CAT-099; isSalesFl: boolean required DBF-CAT-100; version: int64 required DBF-CAT-104; conversionFactor: decimal} · conversionFactor is an echo only, rejected when it differs
Response     : 200 · ApiResponse<ItemUnitView> · paginated: no
Validations  : the Item unit itself must be active (DBF-CAT-101; otherwise CAT-400-REQUEST-INVALID, nothing written — PHASE CORE, update of an inactive record; an inactive unit is added again with API-CAT-010) · RULE-CAT-053 (the Item must be active) · RULE-CAT-028 (conversionFactor fixed) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CONVERSION-FACTOR-FIXED, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-043) → lock its Item (QR-CAT-010; itemFk DBF-CAT-102) → re-read the unit (QR-CAT-043) under the lock and refuse an inactive unit (DBF-CAT-101) with CAT-400-REQUEST-INVALID → refuse an inactive Item (RULE-CAT-053) → compare the echoed factor (DBF-CAT-098) → write isPurchaseFl (DBF-CAT-099), isSalesFl (DBF-CAT-100) with the version check (QR-CAT-046) → record cat.CatalogueChanged UPDATED → return ItemUnitView
Repository   : QR-CAT-043 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-046 UPDATE · join NONE · READ_WRITE
Concurrency  : lost update — optimistic version (DBF-CAT-104) → CAT-409-STALE-VERSION; the Item lock orders it against a parallel Item deactivation (RULE-CAT-053) and a parallel unit deactivation (API-CAT-013 takes the same lock)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-012:END -->

<!-- API:API-CAT-013:START traces=REQ-CAT-128,REQ-CAT-079,DBF-CAT-096,DBF-CAT-101 -->
### API-CAT-013 — deactivate Item unit
Entity       : ENT-CAT-008 · operation DELETE · serves SCR-REQ-CAT-001 "deactivate Item unit"
Honours: CON-CAT-004, CON-CAT-018
Endpoint     : /api/v1/cat/item-units/{id}   verb: DELETE
Layers       : CatItemUnitController.deactivate → CatItemUnitService.deactivate
Request      : path {id: int64 required DBF-CAT-096}
Response     : 200 · ApiResponse<ItemUnitView> · paginated: no
Validations  : RULE-CAT-051 (no active barcode of the Item's Variants is bound to the unit's uomCode — ADR-CAT-060) · RULE-CAT-048 (soft, still readable by identifier) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ITEM-UNIT-HAS-BARCODES, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-043) → lock its Item (QR-CAT-010; itemFk DBF-CAT-102) → count the active barcodes of the Item's Variants bound to the unit's uomCode (QR-CAT-071 on DBF-CAT-112, DBF-CAT-113) → set isActiveFl false (DBF-CAT-101) (QR-CAT-046) → record cat.CatalogueChanged DEACTIVATED → return ItemUnitView with CAT-200-DEACTIVATED; an already inactive unit returns the same success
Repository   : QR-CAT-043 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-071 COUNT · QR-CAT-046 UPDATE · join ADR-CAT-047 (QR-CAT-071) · READ_WRITE
Concurrency  : the barcode count is decided under the Item lock; barcode adds (API-CAT-014, 015) take the same Item lock before RULE-CAT-031 reads the active units, so a unit is never deactivated while a parallel add binds a barcode to it (ADR-CAT-060)
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : success notice ar + en
<!-- API:API-CAT-013:END -->

<!-- API:API-CAT-014:START traces=REQ-CAT-135,REQ-CAT-074,REQ-CAT-075,REQ-CAT-083,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-091,REQ-CAT-096,REQ-CAT-099,DBF-CAT-006,DBF-CAT-022,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114,DBF-CAT-137,DBF-CAT-140 -->
### API-CAT-014 — add barcode (GTIN or scale code)
Entity       : ENT-CAT-009 · operation CREATE · serves SCR-REQ-CAT-001 "add GTIN", "add scale barcode"
Honours: CON-CAT-005, CON-CAT-018
Endpoint     : /api/v1/cat/variants/{id}/barcodes   verb: POST
Layers       : CatBarcodeController.create → CatBarcodeService.create
Request      : path {id: int64 required DBF-CAT-022} · body BarcodeCreateRequest {barcodeValue: string(48) required DBF-CAT-110; barcodeTypeCode: string(50) required DBF-CAT-111; uomCode: string(50) DBF-CAT-112} · type INTERNAL is not accepted here (API-CAT-015 generates it) — CAT-400-REQUEST-INVALID
Response     : 201 · ApiResponse<BarcodeView> · paginated: no
Validations  : request consistency first (PHASE CORE, API conventions — CAT-400-REQUEST-INVALID, nothing written): `barcodeTypeCode` INTERNAL, which only API-CAT-015 generates · RULE-CAT-053 (the Variant and its Item must be active) · RULE-CAT-006 (barcodeTypeCode, uomCode) · RULE-CAT-031 (unit is the Item's base unit or an active Item unit) · GTIN: RULE-CAT-030 (8/12/13/14 digits, GS1 check digit), RULE-CAT-032 (does not begin with an active layout key) · SCALE: RULE-CAT-033 (digits only, length = scaleCodeLength of an active layout; offered only while ≥ 1 active layout exists — REQ-CAT-096) · RULE-CAT-029 (unique among active barcodes; message names the owning Item code) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-BARCODE-UNIT-INVALID, CAT-422-GTIN-INVALID, CAT-409-BARCODE-SCALE-PREFIX, CAT-422-SCALE-CODE-LENGTH, CAT-409-BARCODE-DUPLICATE, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the Variant (QR-CAT-012) and lock its Item (QR-CAT-010 — RULE-CAT-031 and RULE-CAT-051 decide under the same lock, ADR-CAT-060) → refuse an inactive Variant (DBF-CAT-027) or Item (DBF-CAT-014) — RULE-CAT-053 → default uomCode to the Item's baseUomCode (DBF-CAT-006 → DBF-CAT-112) → active units (QR-CAT-044) → take the scale-prefix lock (QR-CAT-074, advisory, ADR-CAT-047) → active layouts (QR-CAT-059: key DBF-CAT-137, scaleCodeLength DBF-CAT-140) → duplicate check (QR-CAT-048) → persist with variantFk (DBF-CAT-114) and isActiveFl true (DBF-CAT-113) (QR-CAT-049) → record cat.CatalogueChanged CREATED → return BarcodeView
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-044 FIND_ALL · QR-CAT-074 NATIVE (scale-prefix lock) · QR-CAT-059 FIND_ALL · QR-CAT-048 EXISTS · QR-CAT-049 SAVE · join ADR-CAT-047 (QR-CAT-048) · READ_WRITE
Concurrency  : duplicate value — UQ_CAT_BARCODE_VALUE rejects the second of two parallel adds (→ CAT-409-BARCODE-DUPLICATE); prefix clash — barcode adds and layout creates take the same advisory lock (QR-CAT-074: `pg_advisory_xact_lock(:tenant, hashtext('CAT_SCALE_PREFIX'))`), so a layout and a clashing barcode are never both saved
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-014:END -->

<!-- API:API-CAT-015:START traces=REQ-CAT-135,REQ-CAT-035,REQ-CAT-084,REQ-CAT-089,REQ-CAT-091,DBF-CAT-006,DBF-CAT-022,DBF-CAT-110,DBF-CAT-111,DBF-CAT-112,DBF-CAT-113,DBF-CAT-114 -->
### API-CAT-015 — generate internal barcode
Entity       : ENT-CAT-009 · operation CREATE · serves SCR-REQ-CAT-001 "generate internal"
Honours: CON-CAT-005, CON-CAT-018
Endpoint     : /api/v1/cat/variants/{id}/barcodes/internal   verb: POST
Layers       : CatBarcodeController.generateInternal → CatBarcodeService.generateInternal (its body is shared with API-CAT-056, the barcode label page's entry — ADR-CAT-078)
Request      : path {id: int64 required DBF-CAT-022} · body InternalBarcodeRequest {uomCode: string(50) DBF-CAT-112}
Response     : 201 · ApiResponse<BarcodeView> · paginated: no
Validations  : request consistency first (PHASE CORE, API conventions — CAT-400-REQUEST-INVALID, nothing written): a body carrying any property other than `uomCode` (a client-chosen value or type is never accepted for an internal barcode) · RULE-CAT-053 (the Variant and its Item must be active) · RULE-CAT-006 (uomCode) · RULE-CAT-031 · RULE-CAT-005 (series CAT_INTERNAL_BARCODE must issue) · RULE-CAT-032 · RULE-CAT-029 (a generated `04…` value can equal a GTIN entered earlier by hand: on that clash one more number is drawn from CAT_INTERNAL_BARCODE and the value is composed again; only when the second value also clashes is CAT-409-BARCODE-DUPLICATE answered, with {code} the Item that owns the second value) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-BARCODE-UNIT-INVALID, CAT-500-CODE-NOT-ISSUED, CAT-409-BARCODE-SCALE-PREFIX, CAT-409-BARCODE-DUPLICATE, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the Variant (QR-CAT-012) and lock its Item (QR-CAT-010 — RULE-CAT-031 and RULE-CAT-051 decide under the same lock, ADR-CAT-060) → refuse an inactive Variant (DBF-CAT-027) or Item (DBF-CAT-014) — RULE-CAT-053 → default uomCode (DBF-CAT-006 → DBF-CAT-112) and check it (QR-CAT-044) → allocate 10 digits from series CAT_INTERNAL_BARCODE (NumberSeriesApi) → barcodeValue = `04` + digits + GS1 check digit (DBF-CAT-110), barcodeTypeCode INTERNAL (DBF-CAT-111) → scale-prefix lock (QR-CAT-074) and active layouts (QR-CAT-059) → duplicate check (QR-CAT-048); when the value is taken, draw one more number from CAT_INTERNAL_BARCODE, compose the value again and repeat the duplicate check once; when that value is taken too, answer CAT-409-BARCODE-DUPLICATE (the numbers drawn stay as gaps in the series) → persist with variantFk (DBF-CAT-114), isActiveFl true (DBF-CAT-113) (QR-CAT-049) → record cat.CatalogueChanged CREATED → return BarcodeView
Repository   : QR-CAT-012 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-044 FIND_ALL · QR-CAT-074 NATIVE (scale-prefix lock) · QR-CAT-059 FIND_ALL · QR-CAT-048 EXISTS · QR-CAT-049 SAVE · join NONE · READ_WRITE
Concurrency  : the digits are allocated atomically by NumberSeriesApi, so two parallel requests never draw the same number; the one retry covers a clash with a GTIN entered by hand; UQ_CAT_BARCODE_VALUE backs uniqueness; the advisory scale-prefix lock (QR-CAT-074) orders it against layout creation
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-015:END -->

<!-- API:API-CAT-016:START traces=REQ-CAT-085,DBF-CAT-109,DBF-CAT-113 -->
### API-CAT-016 — deactivate barcode
Entity       : ENT-CAT-009 · operation DELETE · serves SCR-REQ-CAT-001 "deactivate barcode"
Honours: CON-CAT-005, CON-CAT-018
Endpoint     : /api/v1/cat/barcodes/{id}   verb: DELETE
Layers       : CatBarcodeController.deactivate → CatBarcodeService.deactivate
Request      : path {id: int64 required DBF-CAT-109}
Response     : 200 · ApiResponse<BarcodeView> · paginated: no
Validations  : RULE-CAT-048 (soft; an inactive barcode is excluded from lookups and releases its value for RULE-CAT-029 — REQ-CAT-085) · RULE-CAT-049 · allowed on a barcode of an inactive Variant or Item (a child deactivation, never refused by RULE-CAT-053), so the owner can release a GTIN of a deactivated Item (REQ-CAT-005)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-050) → set isActiveFl false (DBF-CAT-113) (QR-CAT-051) → record cat.CatalogueChanged DEACTIVATED → return BarcodeView with CAT-200-DEACTIVATED
Repository   : QR-CAT-050 FIND_ONE · QR-CAT-051 UPDATE · join NONE · READ_WRITE
Concurrency  : NONE — a flag flip with no decision on other rows; repeated calls are idempotent
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : success notice ar + en
<!-- API:API-CAT-016:END -->

<!-- API:API-CAT-017:START traces=REQ-CAT-147,REQ-CAT-013,REQ-CAT-015,REQ-CAT-016,REQ-CAT-020,REQ-CAT-056,REQ-CAT-057,REQ-CAT-059,REQ-CAT-060,DBF-CAT-005,DBF-CAT-017,DBF-CAT-084,DBF-CAT-085,DBF-CAT-086,DBF-CAT-087,DBF-CAT-088,DBF-CAT-089 -->
### API-CAT-017 — save Bundle components
Entity       : ENT-CAT-007 · operation CREATE, UPDATE, DELETE · serves SCR-REQ-CAT-001 "maintain Bundle components"
Honours: CON-CAT-006, CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}/bundle-components   verb: PUT
Layers       : CatBundleController.replace → CatBundleService.replaceComponents
Request      : path {id: int64 required DBF-CAT-001} · body BundleComponentsRequest {lines: array{bundleComponentPk: int64 DBF-CAT-084; componentVariantFk: int64 required DBF-CAT-089; quantity: decimal in the component Item's base unit (> 0, ≤ 999999.999, multipleOf 0.001 — ADR-CAT-080) required DBF-CAT-085; sortOrder: int32 DBF-CAT-086}; version: int64 required DBF-CAT-017} · the lines are the Bundle's complete new set of active lines
Response     : 200 · ApiResponse<List<BundleComponentView>> · paginated: no
Validations  : request consistency first (PHASE CORE, API conventions — CAT-400-REQUEST-INVALID, nothing written), checked on the active lines of this Bundle (QR-CAT-039) read under the Item lock: a `bundleComponentPk` that is not an active line of the Bundle the path addresses; a kept line (its `bundleComponentPk` given) whose `componentVariantFk` differs from the stored one (DBF-CAT-089 is read-only after create — to change the component, the client drops the line and adds a new one without `bundleComponentPk`); the same `bundleComponentPk` or the same `componentVariantFk` twice in `lines`; a line `quantity` outside the document's bounds (> 0, ≤ 999999.999, multipleOf 0.001 — PHASE CORE, request numbers) — so UQ_CAT_BUNDLE_COMP_VARIANT is never reached by a malformed request, and if it rejects a write all the same it is translated to RULE-CAT-019, never CAT-500 · RULE-CAT-008 / RULE-CAT-009 (`bundles`) · allowed on an inactive Bundle: replacing the components is the repair of RULE-CAT-019, so RULE-CAT-053 does not apply here and the rules below decide alone (REQ-CAT-147, ADR-CAT-068) · the Item must be of kind BUNDLE (DBF-CAT-005; otherwise CAT-400-REQUEST-INVALID) · RULE-CAT-018 · RULE-CAT-019 · RULE-CAT-049; an active STANDARD Variant created while `bundles` was off is accepted unchanged (REQ-CAT-020)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-BUNDLE-COMPONENTS-REQUIRED, CAT-422-BUNDLE-COMPONENT-INVALID, CAT-500-CHANGE-NOT-SAVED
Orchestration : read `bundles` → lock the Item (QR-CAT-010) and check its version (DBF-CAT-017); an inactive Bundle (DBF-CAT-014) is accepted and stays inactive — the owner reactivates it afterwards (ADR-CAT-068) → load the component Variants and their Items FOR SHARE (QR-CAT-017) → load the active lines (QR-CAT-039) → new lines: persist with bundleItemFk (DBF-CAT-088), isActiveFl true (DBF-CAT-087), quantity (DBF-CAT-085), sortOrder default line order (DBF-CAT-086) (QR-CAT-040) → kept lines: update quantity / sortOrder (QR-CAT-041) → lines not in the set: isActiveFl false (DBF-CAT-087) (QR-CAT-041) → bump the Item version (QR-CAT-004) → record cat.CatalogueChanged CREATED / UPDATED / DEACTIVATED per line → return the active lines
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-017 FIND_ALL (FOR SHARE) · QR-CAT-039 FIND_ALL · QR-CAT-040 SAVE · QR-CAT-041 UPDATE · QR-CAT-004 UPDATE · join NONE · READ_WRITE
Concurrency  : component validity is decided on rows read FOR SHARE, so a parallel deactivation of a component Variant or Item (FOR UPDATE) cannot interleave; two parallel saves of one Bundle are serialised by the Item lock and the version check; UQ_CAT_BUNDLE_COMP_VARIANT backs "listed once"
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-017:END -->

<!-- API:API-CAT-018:START traces=REQ-CAT-135,REQ-CAT-105,REQ-CAT-108,REQ-CAT-110,REQ-CAT-112,DBF-CAT-003,DBF-CAT-122,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128,DBF-CAT-129 -->
### API-CAT-018 — upload image
Entity       : ENT-CAT-010 · operation CREATE · serves SCR-REQ-CAT-001 "add"
Honours: CON-CAT-018
Endpoint     : /api/v1/cat/items/{id}/media   verb: POST
Layers       : CatMediaController.upload → CatMediaService.upload
Request      : path {id: int64 required DBF-CAT-001} · body ItemMediaCreateRequest {fileDocumentRef: int64 required DBF-CAT-122; nameAr: string(200) DBF-CAT-123; nameEn: string(200) DBF-CAT-124; variantFk: int64 DBF-CAT-129} · application/json; the client uploaded the bytes beforehand through the given FILE endpoint to the FILE category `CAT_ITEM_IMAGE` (allowPublic = true, PNG / JPEG / WEBP, ≤ 5 MB), which returned fileDocumentRef; a file of another type, above 5 MB or not public is refused by that upload, in the FILE envelope, and never reaches CAT (ADR-CAT-056, ADR-CAT-076)
Response     : 201 · ApiResponse<MediaView> · paginated: no
Validations  : fileDocumentRef names a FILE document of the caller's tenant that is available and has a public URL (`FileDocumentLookupApi.isAvailable`, `publicUrl` — the only published reads); otherwise CAT-404-NOT-FOUND, which also covers a document of the private storage, because the storefront could not show it · RULE-CAT-053 (the Item must be active) · RULE-CAT-038 (≤ 20 active images per Item — the type, size and public storage part of the rule is the FILE category's, ADR-CAT-076; CAT reads no type or size and rebuilds no image validator) · one fileDocumentRef may be attached to several media rows, of this Item or of others: CAT does not check that the ref is unused, because removing an image never deletes its file (ADR-CAT-056 §4) · RULE-CAT-040 (variantFk of the same Item) · RULE-CAT-001, RULE-CAT-002 (nameAr defaults to the Item's nameAr) · RULE-CAT-049; the main-image effect of rule 039 (the first active image becomes primary — REQ-CAT-108) is applied in the orchestration and raises nothing
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-IMAGE-NOT-ACCEPTED, CAT-422-MEDIA-VARIANT-MISMATCH, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-422-PARENT-INACTIVE, CAT-500-CHANGE-NOT-SAVED
Orchestration : lock the Item (QR-CAT-010) and refuse an inactive one (DBF-CAT-014, RULE-CAT-053) → its active media (QR-CAT-054: count, max sortOrder, primary present) → variantFk check (QR-CAT-012) → check fileDocumentRef with `FileDocumentLookupApi.isAvailable` and `publicUrl`, and answer CAT-404-NOT-FOUND when it is not available or has no public URL → refuse a 21st active image (RULE-CAT-038) → persist with fileDocumentRef (DBF-CAT-122), nameAr default the Item's nameAr (DBF-CAT-003 → DBF-CAT-123), sortOrder next (DBF-CAT-125), isPrimaryFl true when no active image exists (DBF-CAT-126), isActiveFl true (DBF-CAT-127), itemFk (DBF-CAT-128) (QR-CAT-056) → record cat.CatalogueChanged CREATED → return MediaView; on any rejection or rollback no media row is created and CAT stores no byte (an unattached file stays with the given FILE module under its retention — ADR-CAT-056 §4)
Repository   : QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-054 FIND_ALL · QR-CAT-012 FIND_ONE · QR-CAT-056 SAVE · join NONE · READ_WRITE
Concurrency  : the 20-image limit and "first image is primary" are decided under the Item lock; UQ_CAT_ITEM_MEDIA_PRIMARY backs at most one primary
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_CREATE
Localization : image descriptions in ar and en
<!-- API:API-CAT-018:END -->

<!-- API:API-CAT-019:START traces=REQ-CAT-135,REQ-CAT-106,REQ-CAT-112,DBF-CAT-121,DBF-CAT-123,DBF-CAT-124,DBF-CAT-125,DBF-CAT-126,DBF-CAT-128,DBF-CAT-129,DBF-CAT-131 -->
### API-CAT-019 — update image
Entity       : ENT-CAT-010 · operation UPDATE · serves SCR-REQ-CAT-001 "update"
Honours: CON-CAT-018
Endpoint     : /api/v1/cat/media/{id}   verb: PUT
Layers       : CatMediaController.update → CatMediaService.update
Request      : path {id: int64 required DBF-CAT-121} · body ItemMediaUpdateRequest {nameAr: string(200) required DBF-CAT-123; nameEn: string(200) DBF-CAT-124; sortOrder: int32 required DBF-CAT-125; isPrimaryFl: boolean required DBF-CAT-126; variantFk: int64 DBF-CAT-129; version: int64 required DBF-CAT-131}
Response     : 200 · ApiResponse<MediaView> · paginated: no
Validations  : the media record itself must be active (DBF-CAT-127; otherwise CAT-400-REQUEST-INVALID, nothing written — PHASE CORE, update of an inactive record): a removed image is never updated and never takes isPrimaryFl, so the active primary of the Item stays where it is and RULE-CAT-039 holds — UQ_CAT_ITEM_MEDIA_PRIMARY covers active rows only and would not catch it; a removed image is replaced by a new upload (API-CAT-018) · RULE-CAT-053 (the Item must be active) · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-040 · RULE-CAT-039 (setting isPrimaryFl clears the former primary — success notice; clearing it on the only primary leaves it primary) · RULE-CAT-049
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-422-MEDIA-VARIANT-MISMATCH, CAT-422-PARENT-INACTIVE, CAT-200-PRIMARY-IMAGE-REPLACED, CAT-500-CHANGE-NOT-SAVED
Orchestration : load the media (QR-CAT-055) → lock its Item (QR-CAT-010; itemFk DBF-CAT-128) → re-read the media (QR-CAT-055) under the lock and refuse a removed one (DBF-CAT-127) with CAT-400-REQUEST-INVALID, before any primary flag is touched → refuse an inactive Item (RULE-CAT-053) → variantFk check (QR-CAT-012) → when isPrimaryFl turns true: clear the former primary first (QR-CAT-057) → write nameAr, nameEn, sortOrder, isPrimaryFl (DBF-CAT-126), variantFk with the version check (QR-CAT-057) → record cat.CatalogueChanged UPDATED → return MediaView (with CAT-200-PRIMARY-IMAGE-REPLACED when the primary moved)
Repository   : QR-CAT-055 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-012 FIND_ONE · QR-CAT-057 UPDATE · join NONE · READ_WRITE
Concurrency  : two parallel "make primary" requests are serialised by the Item lock; clearing before setting keeps UQ_CAT_ITEM_MEDIA_PRIMARY satisfied; a parallel removal (API-CAT-020) takes the same Item lock, so the active check decides on the committed state; version check against lost updates
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_UPDATE
Localization : notice ar + en (CAT-200-PRIMARY-IMAGE-REPLACED)
<!-- API:API-CAT-019:END -->

<!-- API:API-CAT-020:START traces=REQ-CAT-107,REQ-CAT-111,DBF-CAT-121,DBF-CAT-125,DBF-CAT-126,DBF-CAT-127,DBF-CAT-128 -->
### API-CAT-020 — remove image
Entity       : ENT-CAT-010 · operation DELETE · serves SCR-REQ-CAT-001 "remove image"
Honours: CON-CAT-018
Endpoint     : /api/v1/cat/media/{id}   verb: DELETE
Layers       : CatMediaController.remove → CatMediaService.remove
Request      : path {id: int64 required DBF-CAT-121}
Response     : 200 · ApiResponse<MediaView> · paginated: no
Validations  : RULE-CAT-049; the main-image effect of rule 039 (removing the primary promotes the remaining active image with the lowest sortOrder — REQ-CAT-111) is applied in the orchestration and raises nothing
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-055) → lock its Item (QR-CAT-010) → set isActiveFl false (DBF-CAT-127) and isPrimaryFl false (DBF-CAT-126) (QR-CAT-057) → when it was primary: the remaining active image with the lowest sortOrder (QR-CAT-054, DBF-CAT-125) gets isPrimaryFl true (QR-CAT-057) → record cat.CatalogueChanged DEACTIVATED (and UPDATED for the promoted image) → return MediaView; FILE_DOCUMENT_REF is kept and CAT never deletes the FILE document — its purge after the tenant's retention period is the given FILE module's (ADR-CAT-056 §4)
Repository   : QR-CAT-055 FIND_ONE · QR-CAT-010 FIND_ONE (FOR UPDATE) · QR-CAT-054 FIND_ALL · QR-CAT-057 UPDATE · join NONE · READ_WRITE
Concurrency  : the promotion decision is taken under the Item lock, so two parallel removals never leave zero or two primaries
Security     : SCR-REQ-CAT-001 Items · PERM_CAT_ITEMS_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-020:END -->

<!-- API:API-CAT-021:START traces=REQ-CAT-008,REQ-CAT-064,REQ-CAT-069,REQ-CAT-070,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077 -->
### API-CAT-021 — create Category
Entity       : ENT-CAT-006 · operation CREATE · serves SCR-REQ-CAT-002 "create"
Honours: CON-CAT-003, CON-CAT-018
Endpoint     : /api/v1/cat/categories   verb: POST
Layers       : CatCategoryController.create → CatCategoryService.create
Request      : body CategoryCreateRequest {code: string(30) required DBF-CAT-072; nameAr: string(200) required DBF-CAT-073; nameEn: string(200) DBF-CAT-074; parentCategoryFk: int64 DBF-CAT-077; sortOrder: int32 DBF-CAT-075}
Response     : 201 · ApiResponse<CategoryView> · paginated: no
Validations  : RULE-CAT-001, RULE-CAT-002 · RULE-CAT-024 (code unique per tenant, 1–30 Latin letters / digits / hyphens; nameAr unique among active siblings) · RULE-CAT-023 (parent active; depth ≤ 5) · RULE-CAT-049 (event only after commit — ADR-CAT-077)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-CATEGORY-DUPLICATE, CAT-422-CATEGORY-PARENT-INVALID, CAT-500-CHANGE-NOT-SAVED
Orchestration : take the tree lock (QR-CAT-073, advisory, ADR-CAT-047) → duplicate checks (QR-CAT-033) → parent (QR-CAT-032) and its depth (QR-CAT-037) → sortOrder default next under the parent (DBF-CAT-075) → persist with isActiveFl true (DBF-CAT-076) (QR-CAT-034) → record cat.CatalogueChanged CREATED {entity ENT-CAT-006, recordPk categoryPk, itemPk empty} (OutboxPublisher) → return CategoryView
Repository   : QR-CAT-073 NATIVE (tree lock) · QR-CAT-033 EXISTS · QR-CAT-032 FIND_ONE · QR-CAT-037 NATIVE · QR-CAT-034 SAVE · join ADR-CAT-047 (recursive CTE) · READ_WRITE
Concurrency  : duplicate code / sibling name backed by UQ_CAT_CATEGORY_CODE and UQ_CAT_CATEGORY_NAME_AR (→ CAT-409-CATEGORY-DUPLICATE); depth and parent decisions under the tenant tree lock (QR-CAT-073)
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_CREATE
Localization : names in ar and en
<!-- API:API-CAT-021:END -->

<!-- API:API-CAT-022:START traces=REQ-CAT-066,REQ-CAT-067,DBF-CAT-071,DBF-CAT-072,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-076,DBF-CAT-077 -->
### API-CAT-022 — read Category
Entity       : ENT-CAT-006 · operation VIEW · serves SCR-REQ-CAT-002 "read"
Honours: CON-CAT-011
Endpoint     : /api/v1/cat/categories/{id}   verb: GET
Layers       : CatCategoryController.get → CatCategoryQueryFacade.getCategory (@PreAuthorize on the screen VIEW permission) → CatCatalogueReadService.getCategory (also called by CatalogueReadApi.getCategory, with no CAT permission check — ADR-CAT-061)
Request      : path {id: int64 required DBF-CAT-071}
Response     : 200 · ApiResponse<CategoryView> · paginated: no
Validations  : none — an inactive Category is still returned (REQ-CAT-066)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load (QR-CAT-032) → return CategoryView
Repository   : QR-CAT-032 FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-022:END -->

<!-- API:API-CAT-023:START traces=REQ-CAT-008,REQ-CAT-065,REQ-CAT-069,REQ-CAT-070,DBF-CAT-071,DBF-CAT-073,DBF-CAT-074,DBF-CAT-075,DBF-CAT-077,DBF-CAT-079 -->
### API-CAT-023 — update Category
Entity       : ENT-CAT-006 · operation UPDATE · serves SCR-REQ-CAT-002 "update (move)"
Honours: CON-CAT-003, CON-CAT-018
Endpoint     : /api/v1/cat/categories/{id}   verb: PUT
Layers       : CatCategoryController.update → CatCategoryService.update
Request      : path {id: int64 required DBF-CAT-071} · body CategoryUpdateRequest {nameAr: string(200) required DBF-CAT-073; nameEn: string(200) DBF-CAT-074; parentCategoryFk: int64 DBF-CAT-077; sortOrder: int32 required DBF-CAT-075; version: int64 required DBF-CAT-079} · code is fixed after creation and not accepted
Response     : 200 · ApiResponse<CategoryView> · paginated: no
Validations  : the Category must be active (DBF-CAT-076; otherwise CAT-400-REQUEST-INVALID, nothing written — PHASE CORE, update of an inactive record; it is reactivated first with API-CAT-053) · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-024 (sibling name under the new parent) · RULE-CAT-023 (parent active, not itself, not one of its sub-categories; parent depth + height of the moved sub-tree ≤ 5); the Items keep their categoryFk (REQ-CAT-065) · RULE-CAT-049 (event only after commit — ADR-CAT-077)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-CATEGORY-DUPLICATE, CAT-422-CATEGORY-PARENT-INVALID, CAT-500-CHANGE-NOT-SAVED
Orchestration : load (QR-CAT-032) and refuse an inactive Category (DBF-CAT-076) with CAT-400-REQUEST-INVALID → take the tree lock (QR-CAT-073) → duplicate checks (QR-CAT-033) → ancestors of the new parent and descendants of this Category (QR-CAT-037) → write nameAr, nameEn, parentCategoryFk (DBF-CAT-077), sortOrder with the version check (QR-CAT-035) → record cat.CatalogueChanged UPDATED {entity ENT-CAT-006, recordPk categoryPk, itemPk empty} — a rename, a move under another parent and a new sortOrder alike (OutboxPublisher) → return CategoryView
Repository   : QR-CAT-032 FIND_ONE · QR-CAT-073 NATIVE (tree lock) · QR-CAT-033 EXISTS · QR-CAT-037 NATIVE · QR-CAT-035 UPDATE · join ADR-CAT-047 (recursive CTE) · READ_WRITE
Concurrency  : two parallel moves (A under B, B under A) would each pass the cycle check — the tenant tree lock (QR-CAT-073: `pg_advisory_xact_lock(:tenant, hashtext('CAT_CATEGORY_TREE'))`) serialises every create and move; version check against lost updates
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_UPDATE
Localization : names in ar and en
<!-- API:API-CAT-023:END -->

<!-- API:API-CAT-024:START traces=REQ-CAT-008,REQ-CAT-066,REQ-CAT-071,REQ-CAT-069,DBF-CAT-071,DBF-CAT-076,DBF-CAT-077,DBF-CAT-014,DBF-CAT-015 -->
### API-CAT-024 — deactivate Category
Entity       : ENT-CAT-006 · operation DELETE · serves SCR-REQ-CAT-002 "deactivate"
Honours: CON-CAT-003, CON-CAT-018
Endpoint     : /api/v1/cat/categories/{id}   verb: DELETE
Layers       : CatCategoryController.deactivate → CatCategoryService.deactivate
Request      : path {id: int64 required DBF-CAT-071}
Response     : 200 · ApiResponse<CategoryView> · paginated: no
Validations  : RULE-CAT-025 (no active sub-category, no active Item) · RULE-CAT-048 (soft deactivation, success notice, one DEACTIVATED event — AC-CAT-165) · RULE-CAT-049 (event only after commit — ADR-CAT-077)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-CATEGORY-IN-USE, CAT-200-DEACTIVATED, CAT-500-CHANGE-NOT-SAVED
Orchestration : take the tenant tree lock (QR-CAT-073 — the same lock as API-CAT-021, 023 and 053, ADR-CAT-047) → load and lock the Category (QR-CAT-032, FOR UPDATE) → count active sub-categories (DBF-CAT-077) and active Items (DBF-CAT-015, DBF-CAT-014) (QR-CAT-038) → set isActiveFl false (DBF-CAT-076) (QR-CAT-035) → record cat.CatalogueChanged DEACTIVATED {entity ENT-CAT-006, recordPk categoryPk, itemPk empty} (OutboxPublisher) → return CategoryView with CAT-200-DEACTIVATED; an already inactive Category returns the same success and records no event
Repository   : QR-CAT-073 NATIVE (tree lock) · QR-CAT-032 FIND_ONE (FOR UPDATE) · QR-CAT-038 COUNT · QR-CAT-035 UPDATE · join NONE · READ_WRITE
Concurrency  : the tree lock is taken before QR-CAT-038, so a parallel sub-category create or move (API-CAT-021, 023) or reactivation (API-CAT-053), which read the parent under the same lock, is serialised with the count: either the child commits first and the count refuses the deactivation (CAT-409-CATEGORY-IN-USE), or the deactivation commits first and the child sees an inactive parent (CAT-422-CATEGORY-PARENT-INVALID) — no active child is ever left under an inactive parent (RULE-CAT-023, AC-CAT-153); Item saves read the Category FOR SHARE (API-CAT-001, 003, 049), so an Item is never attached to a Category being deactivated
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_DELETE
Localization : success notice ar + en
<!-- API:API-CAT-024:END -->

<!-- API:API-CAT-025:START traces=REQ-CAT-012,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-055,DBF-CAT-036,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-040 -->
### API-CAT-025 — create Variant attribute
Entity       : ENT-CAT-003 · operation CREATE · serves SCR-REQ-CAT-003 "create"
Endpoint     : /api/v1/cat/variant-attributes   verb: POST
Layers       : CatVariantAttributeController.create → CatVariantAttributeService.create
Request      : body VariantAttributeCreateRequest {code: string(30) required DBF-CAT-036; nameAr: string(200) required DBF-CAT-037; nameEn: string(200) DBF-CAT-038; sortOrder: int32 DBF-CAT-039}
Response     : 201 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : RULE-CAT-008 / RULE-CAT-009 (`variants`, read on this request — REQ-CAT-012) · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-016
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : read `variants` → duplicate checks (QR-CAT-023) → sortOrder default next (DBF-CAT-039) → persist with isActiveFl true (DBF-CAT-040) (QR-CAT-024) → return VariantAttributeView
Repository   : QR-CAT-023 EXISTS · QR-CAT-024 SAVE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_VAR_ATTR_CODE / UQ_CAT_VAR_ATTR_NAME_AR reject the second of two parallel creates (→ CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE); a sortOrder tie is harmless (display order only)
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_CREATE
Localization : names in ar and en
<!-- API:API-CAT-025:END -->

<!-- API:API-CAT-026:START traces=REQ-CAT-048,DBF-CAT-035,DBF-CAT-036,DBF-CAT-037,DBF-CAT-039,DBF-CAT-040,DBF-CAT-047,DBF-CAT-053 -->
### API-CAT-026 — read Variant attribute
Entity       : ENT-CAT-003, ENT-CAT-004 · operation VIEW · serves SCR-REQ-CAT-003 "read"
Endpoint     : /api/v1/cat/variant-attributes/{id}   verb: GET
Layers       : CatVariantAttributeController.get → CatVariantAttributeService.get — ADR-CAT-045
Request      : path {id: int64 required DBF-CAT-035}
Response     : 200 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : none — a read stays allowed while `variants` is off (RULE-CAT-008), so existing attributes can be inspected and cleaned up
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load (QR-CAT-022) with its values by sortOrder (QR-CAT-026) → return VariantAttributeView
Repository   : QR-CAT-022 FIND_ONE · QR-CAT-026 FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_VIEW
Localization : names in ar and en
<!-- API:API-CAT-026:END -->

<!-- API:API-CAT-027:START traces=REQ-CAT-043,REQ-CAT-055,DBF-CAT-035,DBF-CAT-037,DBF-CAT-038,DBF-CAT-039,DBF-CAT-042 -->
### API-CAT-027 — update Variant attribute
Entity       : ENT-CAT-003 · operation UPDATE · serves SCR-REQ-CAT-003 "update"
Endpoint     : /api/v1/cat/variant-attributes/{id}   verb: PUT
Layers       : CatVariantAttributeController.update → CatVariantAttributeService.update
Request      : path {id: int64 required DBF-CAT-035} · body VariantAttributeUpdateRequest {nameAr: string(200) required DBF-CAT-037; nameEn: string(200) DBF-CAT-038; sortOrder: int32 required DBF-CAT-039; version: int64 required DBF-CAT-042} · code fixed after creation
Response     : 200 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : the attribute must be active (DBF-CAT-040; otherwise CAT-400-REQUEST-INVALID, nothing written — PHASE CORE, update of an inactive record; it is reactivated first with API-CAT-054) · RULE-CAT-008 / RULE-CAT-009 · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-016 (nameAr among active attributes); derived Variant names change with it (REQ-CAT-043)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : load (QR-CAT-022) and refuse an inactive attribute (DBF-CAT-040) with CAT-400-REQUEST-INVALID → read `variants` → duplicate check (QR-CAT-023) → write nameAr, nameEn, sortOrder with the version check (QR-CAT-025) → return VariantAttributeView
Repository   : QR-CAT-022 FIND_ONE · QR-CAT-023 EXISTS · QR-CAT-025 UPDATE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_VAR_ATTR_NAME_AR backs the name; version check against lost updates
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE
Localization : names in ar and en
<!-- API:API-CAT-027:END -->

<!-- API:API-CAT-028:START traces=REQ-CAT-044,REQ-CAT-054,DBF-CAT-035,DBF-CAT-040,DBF-CAT-061,DBF-CAT-063 -->
### API-CAT-028 — deactivate Variant attribute
Entity       : ENT-CAT-003 · operation DELETE · serves SCR-REQ-CAT-003 "deactivate (attribute and value)"
Endpoint     : /api/v1/cat/variant-attributes/{id}   verb: DELETE
Layers       : CatVariantAttributeController.deactivate → CatVariantAttributeService.deactivate
Request      : path {id: int64 required DBF-CAT-035}
Response     : 200 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : RULE-CAT-017 (no active assignment on an active Variant carries it); once inactive it is excluded from generation (REQ-CAT-044); allowed while `variants` is off, so attributes that depend on the flag can be cleaned up (RULE-CAT-008)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ATTRIBUTE-VALUE-IN-USE
Orchestration : load and lock (QR-CAT-022, FOR UPDATE) → count usage (QR-CAT-021 on DBF-CAT-063, DBF-CAT-061) → set isActiveFl false (DBF-CAT-040) (QR-CAT-025) → return VariantAttributeView
Repository   : QR-CAT-022 FIND_ONE (FOR UPDATE) · QR-CAT-021 COUNT · QR-CAT-025 UPDATE · join NONE · READ_WRITE
Concurrency  : generation reads the attribute FOR SHARE (QR-CAT-031 / QR-CAT-026), and a value create or reactivation reads it FOR SHARE (QR-CAT-022, API-CAT-029, 055), so neither a generation nor a new active value can commit under an attribute this deactivation makes inactive
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-028:END -->

<!-- API:API-CAT-029:START traces=REQ-CAT-016,REQ-CAT-045,REQ-CAT-055,DBF-CAT-035,DBF-CAT-048,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-052,DBF-CAT-053 -->
### API-CAT-029 — create Variant attribute value
Entity       : ENT-CAT-004 · operation CREATE · serves SCR-REQ-CAT-003 "create"
Endpoint     : /api/v1/cat/variant-attributes/{id}/values   verb: POST
Layers       : CatVariantAttributeController.createValue → CatVariantAttributeService.createValue
Request      : path {id: int64 required DBF-CAT-035} · body VariantAttributeValueCreateRequest {code: string(30) required DBF-CAT-048; nameAr: string(200) required DBF-CAT-049; nameEn: string(200) DBF-CAT-050; sortOrder: int32 DBF-CAT-051}
Response     : 201 · ApiResponse<VariantAttributeValueView> · paginated: no
Validations  : RULE-CAT-008 / RULE-CAT-009 · the attribute must be active (otherwise CAT-400-REQUEST-INVALID) · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-016 (code per attribute, nameAr among the attribute's active values)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : read `variants` → load the attribute FOR SHARE (QR-CAT-022) and refuse an inactive one → duplicate checks (QR-CAT-028) → variantAttributeFk from the path (DBF-CAT-053), sortOrder default next within the attribute (DBF-CAT-051) → persist with isActiveFl true (DBF-CAT-052) (QR-CAT-029) → return VariantAttributeValueView
Repository   : QR-CAT-022 FIND_ONE (FOR SHARE) · QR-CAT-028 EXISTS · QR-CAT-029 SAVE · join NONE · READ_WRITE
Concurrency  : the attribute is read FOR SHARE (QR-CAT-022), as on value reactivation (API-CAT-055), so a parallel attribute deactivation (API-CAT-028, FOR UPDATE) waits until the create commits, or the create waits until the deactivation commits and then sees an inactive attribute (CAT-400-REQUEST-INVALID) — an active value is never committed under an attribute deactivated meanwhile; UQ_CAT_VAR_ATTR_VALUE_CODE / UQ_CAT_VAR_ATTR_VALUE_NAME_AR reject the second of two parallel creates
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_CREATE
Localization : names in ar and en
<!-- API:API-CAT-029:END -->

<!-- API:API-CAT-030:START traces=REQ-CAT-046,REQ-CAT-055,DBF-CAT-047,DBF-CAT-049,DBF-CAT-050,DBF-CAT-051,DBF-CAT-055 -->
### API-CAT-030 — update Variant attribute value
Entity       : ENT-CAT-004 · operation UPDATE · serves SCR-REQ-CAT-003 "update"
Endpoint     : /api/v1/cat/variant-attribute-values/{id}   verb: PUT
Layers       : CatVariantAttributeController.updateValue → CatVariantAttributeService.updateValue
Request      : path {id: int64 required DBF-CAT-047} · body VariantAttributeValueUpdateRequest {nameAr: string(200) required DBF-CAT-049; nameEn: string(200) DBF-CAT-050; sortOrder: int32 required DBF-CAT-051; version: int64 required DBF-CAT-055}
Response     : 200 · ApiResponse<VariantAttributeValueView> · paginated: no
Validations  : the value must be active (DBF-CAT-052; otherwise CAT-400-REQUEST-INVALID, nothing written — PHASE CORE, update of an inactive record; it is reactivated first with API-CAT-055) · RULE-CAT-008 / RULE-CAT-009 · RULE-CAT-001, RULE-CAT-002 · RULE-CAT-016; derived Variant names follow the new name (REQ-CAT-046)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-CAPABILITY-OFF, CAT-500-CAPABILITY-UNREADABLE, CAT-422-NAME-AR-INVALID, CAT-422-NAME-EN-TOO-LONG, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : load (QR-CAT-027) and refuse an inactive value (DBF-CAT-052) with CAT-400-REQUEST-INVALID → read `variants` → duplicate check (QR-CAT-028) → write nameAr, nameEn, sortOrder with the version check (QR-CAT-030) → return VariantAttributeValueView
Repository   : QR-CAT-027 FIND_ONE · QR-CAT-028 EXISTS · QR-CAT-030 UPDATE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_VAR_ATTR_VALUE_NAME_AR backs the name; version check against lost updates
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE
Localization : names in ar and en
<!-- API:API-CAT-030:END -->

<!-- API:API-CAT-031:START traces=REQ-CAT-047,REQ-CAT-054,DBF-CAT-047,DBF-CAT-052,DBF-CAT-061,DBF-CAT-064 -->
### API-CAT-031 — deactivate Variant attribute value
Entity       : ENT-CAT-004 · operation DELETE · serves SCR-REQ-CAT-003 "deactivate (attribute and value)"
Endpoint     : /api/v1/cat/variant-attribute-values/{id}   verb: DELETE
Layers       : CatVariantAttributeController.deactivateValue → CatVariantAttributeService.deactivateValue
Request      : path {id: int64 required DBF-CAT-047}
Response     : 200 · ApiResponse<VariantAttributeValueView> · paginated: no
Validations  : RULE-CAT-017; once inactive it is excluded from generation (REQ-CAT-047); allowed while `variants` is off, so values that depend on the flag can be cleaned up (RULE-CAT-008)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ATTRIBUTE-VALUE-IN-USE
Orchestration : load and lock (QR-CAT-027, FOR UPDATE) → count usage (QR-CAT-021 on DBF-CAT-064, DBF-CAT-061) → set isActiveFl false (DBF-CAT-052) (QR-CAT-030) → return VariantAttributeValueView
Repository   : QR-CAT-027 FIND_ONE (FOR UPDATE) · QR-CAT-021 COUNT · QR-CAT-030 UPDATE · join NONE · READ_WRITE
Concurrency  : generation reads the value FOR SHARE (QR-CAT-031), so a generation and a deactivation never both pass
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-031:END -->

<!-- API:API-CAT-032:START traces=REQ-CAT-075,REQ-CAT-091,REQ-CAT-092,REQ-CAT-098,DBF-CAT-110,DBF-CAT-137,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143 -->
### API-CAT-032 — create scale label layout
Entity       : ENT-CAT-011 · operation CREATE · serves SCR-REQ-CAT-006 "create"
Endpoint     : /api/v1/cat/scale-label-layouts   verb: POST
Layers       : CatSettingsController.createLayout → CatSettingsService.createLayout
Request      : body ScaleLabelLayoutCreateRequest {key: string(2) required DBF-CAT-137; scaleCodeLength: int32 DBF-CAT-140; embeddedValueTypeCode: string(50) DBF-CAT-141; valueDecimals: int32 DBF-CAT-142; valueAr: string(200) DBF-CAT-138; valueEn: string(200) DBF-CAT-139}
Response     : 201 · ApiResponse<ScaleLabelLayoutView> · paginated: no
Validations  : RULE-CAT-006 (embeddedValueTypeCode) · RULE-CAT-034 (key 02 or 20–29 not used by another active layout; scaleCodeLength 4–6; valueDecimals 0–3) · RULE-CAT-032 (no active GTIN / INTERNAL barcode begins with the key — its "on scale label layout create" trigger)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-SCALE-LAYOUT-INVALID, CAT-409-BARCODE-SCALE-PREFIX
Orchestration : defaults scaleCodeLength 5 (DBF-CAT-140), embeddedValueTypeCode WEIGHT (DBF-CAT-141), valueDecimals 3 (DBF-CAT-142), valueAr «ملصق ميزان {key}» (DBF-CAT-138), valueEn "Scale label {key}" (DBF-CAT-139) → active key check (QR-CAT-061) → scale-prefix lock (QR-CAT-074) → barcodes beginning with the key (QR-CAT-053 on DBF-CAT-110) → persist with isActiveFl true (DBF-CAT-143) (QR-CAT-062) → return ScaleLabelLayoutView
Repository   : QR-CAT-061 EXISTS · QR-CAT-074 NATIVE (scale-prefix lock) · QR-CAT-053 EXISTS · QR-CAT-062 SAVE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_SCALE_LABEL_LAYOUT_KEY rejects a parallel duplicate key; the advisory scale-prefix lock (QR-CAT-074, shared with API-CAT-014 / 015 / 049) prevents a clashing barcode being saved meanwhile
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_CREATE
Localization : descriptions in ar and en
<!-- API:API-CAT-032:END -->

<!-- API:API-CAT-033:START traces=REQ-CAT-093,REQ-CAT-098,DBF-CAT-136,DBF-CAT-138,DBF-CAT-139,DBF-CAT-140,DBF-CAT-141,DBF-CAT-142,DBF-CAT-143,DBF-CAT-145 -->
### API-CAT-033 — update scale label layout
Entity       : ENT-CAT-011 · operation UPDATE · serves SCR-REQ-CAT-006 "update"
Endpoint     : /api/v1/cat/scale-label-layouts/{id}   verb: PUT
Layers       : CatSettingsController.updateLayout → CatSettingsService.updateLayout
Request      : path {id: int64 required DBF-CAT-136} · body ScaleLabelLayoutUpdateRequest {embeddedValueTypeCode: string(50) required DBF-CAT-141; valueDecimals: int32 required DBF-CAT-142; valueAr: string(200) DBF-CAT-138; valueEn: string(200) DBF-CAT-139; version: int64 required DBF-CAT-145} · key and scaleCodeLength (DBF-CAT-140) are fixed after creation and not accepted, because the layout's SCALE barcodes were validated against that length (RULE-CAT-033 — ADR-CAT-064)
Response     : 200 · ApiResponse<ScaleLabelLayoutView> · paginated: no
Validations  : the layout must be active (DBF-CAT-143; otherwise CAT-400-REQUEST-INVALID, as on API-CAT-036) — an inactive layout is not reactivated: the owner creates a new layout under the same key (API-CAT-032), which the partial UQ_CAT_SCALE_LABEL_LAYOUT_KEY allows · RULE-CAT-006 · RULE-CAT-034 (valueDecimals 0–3); a new embeddedValueTypeCode or valueDecimals changes only how the embedded value of a label scanned afterwards is read (API-CAT-043), and every SCALE barcode keeps resolving because its length cannot change (ADR-CAT-064)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-422-SCALE-LAYOUT-INVALID
Orchestration : load (QR-CAT-060) and refuse an inactive layout (DBF-CAT-143) with CAT-400-REQUEST-INVALID → validate → write embeddedValueTypeCode, valueDecimals, valueAr, valueEn with the version check (QR-CAT-063) → return ScaleLabelLayoutView
Repository   : QR-CAT-060 FIND_ONE · QR-CAT-063 UPDATE · join NONE · READ_WRITE
Concurrency  : version check against lost updates; no uniqueness is decided (key and scaleCodeLength are not updatable)
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_UPDATE
Localization : descriptions in ar and en
<!-- API:API-CAT-033:END -->

<!-- API:API-CAT-034:START traces=REQ-CAT-094,DBF-CAT-136,DBF-CAT-143 -->
### API-CAT-034 — deactivate scale label layout
Entity       : ENT-CAT-011 · operation DELETE · serves SCR-REQ-CAT-006 "deactivate (layouts and Unit e-invoice codes)"
Endpoint     : /api/v1/cat/scale-label-layouts/{id}   verb: DELETE
Layers       : CatSettingsController.deactivateLayout → CatSettingsService.deactivateLayout
Request      : path {id: int64 required DBF-CAT-136}
Response     : 200 · ApiResponse<ScaleLabelLayoutView> · paginated: no
Validations  : none beyond existence — values with its prefix stop being decoded (REQ-CAT-094); the SCALE barcodes validated against it stay stored and active, and decode again only while an active layout with the same scaleCodeLength exists (ADR-CAT-064)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load (QR-CAT-060) → set isActiveFl false (DBF-CAT-143) (QR-CAT-063) → return ScaleLabelLayoutView
Repository   : QR-CAT-060 FIND_ONE · QR-CAT-063 UPDATE · join NONE · READ_WRITE
Concurrency  : NONE — a flag flip with no decision on other rows
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-034:END -->

<!-- API:API-CAT-035:START traces=REQ-CAT-075,REQ-CAT-120,REQ-CAT-124,DBF-CAT-151,DBF-CAT-152,DBF-CAT-153,DBF-CAT-154,DBF-CAT-155 -->
### API-CAT-035 — create Unit e-invoice code
Entity       : ENT-CAT-012 · operation CREATE · serves SCR-REQ-CAT-006 "create"
Endpoint     : /api/v1/cat/unit-einvoice-codes   verb: POST
Layers       : CatSettingsController.createUnitEinvoiceCode → CatSettingsService.createUnitEinvoiceCode
Request      : body UnitEinvoiceCodeCreateRequest {key: string(50) required DBF-CAT-151; einvoiceUnitCode: string(50) required DBF-CAT-154}
Response     : 201 · ApiResponse<UnitEinvoiceCodeView> · paginated: no
Validations  : RULE-CAT-006 (key in CAT_UOM, einvoiceUnitCode in CAT_EINVOICE_UNIT) · RULE-CAT-043 (no active row for the key)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE, CAT-409-UNIT-EINVOICE-DUPLICATE
Orchestration : validate (MdlLookupApi) → active-key check (QR-CAT-066) → copy the CAT_UOM labels of the key into valueAr (DBF-CAT-152) and valueEn (DBF-CAT-153) → persist with isActiveFl true (DBF-CAT-155) (QR-CAT-067) → return UnitEinvoiceCodeView
Repository   : QR-CAT-066 EXISTS · QR-CAT-067 SAVE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_UNIT_EINVOICE_CODE_KEY rejects the second of two parallel creates (→ CAT-409-UNIT-EINVOICE-DUPLICATE)
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_CREATE
Localization : valueAr / valueEn from the lookup labels
<!-- API:API-CAT-035:END -->

<!-- API:API-CAT-036:START traces=REQ-CAT-075,REQ-CAT-121,DBF-CAT-150,DBF-CAT-154,DBF-CAT-157 -->
### API-CAT-036 — update Unit e-invoice code
Entity       : ENT-CAT-012 · operation UPDATE · serves SCR-REQ-CAT-006 "update"
Endpoint     : /api/v1/cat/unit-einvoice-codes/{id}   verb: PUT
Layers       : CatSettingsController.updateUnitEinvoiceCode → CatSettingsService.updateUnitEinvoiceCode
Request      : path {id: int64 required DBF-CAT-150} · body UnitEinvoiceCodeUpdateRequest {einvoiceUnitCode: string(50) required DBF-CAT-154; version: int64 required DBF-CAT-157}
Response     : 200 · ApiResponse<UnitEinvoiceCodeView> · paginated: no
Validations  : the row must be active (REQ-CAT-121; otherwise CAT-400-REQUEST-INVALID) · RULE-CAT-006
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-500-SERVER-ERROR, CAT-422-LOOKUP-VALUE-INACTIVE
Orchestration : load (QR-CAT-065) → validate → write einvoiceUnitCode (DBF-CAT-154) with the version check (QR-CAT-068) → return UnitEinvoiceCodeView
Repository   : QR-CAT-065 FIND_ONE · QR-CAT-068 UPDATE · join NONE · READ_WRITE
Concurrency  : version check against lost updates
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-036:END -->

<!-- API:API-CAT-037:START traces=REQ-CAT-122,DBF-CAT-150,DBF-CAT-155 -->
### API-CAT-037 — deactivate Unit e-invoice code
Entity       : ENT-CAT-012 · operation DELETE · serves SCR-REQ-CAT-006 "deactivate (layouts and Unit e-invoice codes)"
Endpoint     : /api/v1/cat/unit-einvoice-codes/{id}   verb: DELETE
Layers       : CatSettingsController.deactivateUnitEinvoiceCode → CatSettingsService.deactivateUnitEinvoiceCode
Request      : path {id: int64 required DBF-CAT-150}
Response     : 200 · ApiResponse<UnitEinvoiceCodeView> · paginated: no
Validations  : none beyond existence — reads of the unit then return an empty e-invoice unit (REQ-CAT-122)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR
Orchestration : load (QR-CAT-065) → set isActiveFl false (DBF-CAT-155) (QR-CAT-068) → return UnitEinvoiceCodeView
Repository   : QR-CAT-065 FIND_ONE · QR-CAT-068 UPDATE · join NONE · READ_WRITE
Concurrency  : NONE — a flag flip with no decision on other rows
Security     : SCR-REQ-CAT-006 Catalogue settings · PERM_CAT_SETTINGS_DELETE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-037:END -->

<!-- API:API-CAT-053:START traces=REQ-CAT-008,REQ-CAT-136,DBF-CAT-071,DBF-CAT-073,DBF-CAT-076,DBF-CAT-077,DBF-CAT-079 -->
### API-CAT-053 — reactivate Category
Entity       : ENT-CAT-006 · operation UPDATE · serves SCR-REQ-CAT-002 "reactivate"
Honours: CON-CAT-003, CON-CAT-018
Endpoint     : /api/v1/cat/categories/{id}   verb: PATCH
Layers       : CatCategoryController.reactivate → CatCategoryService.reactivate
Request      : path {id: int64 required DBF-CAT-071} · body CategoryStateRequest {isActiveFl: boolean required DBF-CAT-076} · schema: `isActiveFl` enum [true], no other property (deactivation is DELETE — API-CAT-024)
Response     : 200 · ApiResponse<CategoryView> · paginated: no
Validations  : RULE-CAT-023 (the parent DBF-CAT-077 is empty or active; depth and cycle are unchanged by a reactivation) · RULE-CAT-024 (no other active Category of the same parent carries its nameAr; the code DBF-CAT-072 is kept — UQ_CAT_CATEGORY_CODE spans active and inactive rows, so it is still its own) · no capability flag is read · its sub-categories and Items are not reactivated with it · an already active Category returns the same success and records no event (REQ-CAT-136, ADR-CAT-069) · RULE-CAT-049 (event only after commit — ADR-CAT-077)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-422-CATEGORY-PARENT-INVALID, CAT-409-CATEGORY-DUPLICATE, CAT-500-CHANGE-NOT-SAVED
Orchestration : take the tenant tree lock (QR-CAT-073) → load and lock the Category (QR-CAT-032, FOR UPDATE) → read its parent by parentCategoryFk (QR-CAT-032) and refuse an inactive one (RULE-CAT-023) → sibling name check on nameAr (DBF-CAT-073) under the same parent, excluding this Category (QR-CAT-033) → set isActiveFl true (DBF-CAT-076) with the version read under the lock (DBF-CAT-079) (QR-CAT-035) → record cat.CatalogueChanged REACTIVATED {entity ENT-CAT-006, recordPk categoryPk, itemPk empty} (OutboxPublisher) → return CategoryView
Repository   : QR-CAT-073 NATIVE (tree lock) · QR-CAT-032 FIND_ONE (FOR UPDATE) · QR-CAT-033 EXISTS · QR-CAT-035 UPDATE · join NONE · READ_WRITE
Concurrency  : the tree lock (QR-CAT-073) serialises it with a parallel deactivation of its parent (API-CAT-024) and with creates and moves (API-CAT-021, 023), so it never activates a child under a parent being deactivated; UQ_CAT_CATEGORY_NAME_AR backs the sibling name
Security     : SCR-REQ-CAT-002 Categories · PERM_CAT_CATEGORIES_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-053:END -->

<!-- API:API-CAT-054:START traces=REQ-CAT-137,DBF-CAT-035,DBF-CAT-037,DBF-CAT-040,DBF-CAT-042 -->
### API-CAT-054 — reactivate Variant attribute
Entity       : ENT-CAT-003 · operation UPDATE · serves SCR-REQ-CAT-003 "reactivate (attribute and value)"
Endpoint     : /api/v1/cat/variant-attributes/{id}   verb: PATCH
Layers       : CatVariantAttributeController.reactivate → CatVariantAttributeService.reactivate
Request      : path {id: int64 required DBF-CAT-035} · body VariantAttributeStateRequest {isActiveFl: boolean required DBF-CAT-040} · schema: `isActiveFl` enum [true], no other property (deactivation is DELETE — API-CAT-028)
Response     : 200 · ApiResponse<VariantAttributeView> · paginated: no
Validations  : RULE-CAT-016 (no other active Variant attribute carries its nameAr DBF-CAT-037; the code DBF-CAT-036 is kept — UQ_CAT_VAR_ATTR_CODE spans active and inactive rows) · no capability flag is read, like every reactivation (RULE-CAT-008) · its values keep their own state · an already active attribute returns the same success (REQ-CAT-137, ADR-CAT-069)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : load and lock (QR-CAT-022, FOR UPDATE) → name check among the active attributes, excluding this one (QR-CAT-023 with code empty) → set isActiveFl true (DBF-CAT-040) with the version read under the lock (DBF-CAT-042) (QR-CAT-025) → return VariantAttributeView
Repository   : QR-CAT-022 FIND_ONE (FOR UPDATE) · QR-CAT-023 EXISTS · QR-CAT-025 UPDATE · join NONE · READ_WRITE
Concurrency  : UQ_CAT_VAR_ATTR_NAME_AR rejects a parallel create or reactivation that takes the same nameAr (→ CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE)
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-054:END -->

<!-- API:API-CAT-055:START traces=REQ-CAT-138,DBF-CAT-047,DBF-CAT-049,DBF-CAT-052,DBF-CAT-053,DBF-CAT-055 -->
### API-CAT-055 — reactivate Variant attribute value
Entity       : ENT-CAT-004 · operation UPDATE · serves SCR-REQ-CAT-003 "reactivate (attribute and value)"
Endpoint     : /api/v1/cat/variant-attribute-values/{id}   verb: PATCH
Layers       : CatVariantAttributeController.reactivateValue → CatVariantAttributeService.reactivateValue
Request      : path {id: int64 required DBF-CAT-047} · body VariantAttributeValueStateRequest {isActiveFl: boolean required DBF-CAT-052} · schema: `isActiveFl` enum [true], no other property (deactivation is DELETE — API-CAT-031)
Response     : 200 · ApiResponse<VariantAttributeValueView> · paginated: no
Validations  : its attribute (DBF-CAT-053) must be active (otherwise CAT-400-REQUEST-INVALID, as on value create — API-CAT-029) · RULE-CAT-016 (no other active value of the same attribute carries its nameAr DBF-CAT-049; the code is kept — UQ_CAT_VAR_ATTR_VALUE_CODE spans active and inactive rows) · no capability flag is read (RULE-CAT-008) · an already active value returns the same success (REQ-CAT-138, ADR-CAT-069)
Errors       : CAT-400-REQUEST-INVALID, CAT-401-UNAUTHENTICATED, CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-500-SERVER-ERROR, CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Orchestration : load and lock the value (QR-CAT-027, FOR UPDATE) → read its attribute FOR SHARE (QR-CAT-022 by variantAttributeFk) and refuse an inactive one → name check among the attribute's active values, excluding this one (QR-CAT-028 with code empty) → set isActiveFl true (DBF-CAT-052) with the version read under the lock (DBF-CAT-055) (QR-CAT-030) → return VariantAttributeValueView
Repository   : QR-CAT-027 FIND_ONE (FOR UPDATE) · QR-CAT-022 FIND_ONE (FOR SHARE) · QR-CAT-028 EXISTS · QR-CAT-030 UPDATE · join NONE · READ_WRITE
Concurrency  : the attribute is read FOR SHARE, so a parallel attribute deactivation (API-CAT-028, FOR UPDATE) waits; UQ_CAT_VAR_ATTR_VALUE_NAME_AR backs the name
Security     : SCR-REQ-CAT-003 Variant attributes · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE
Localization : messages ar + en from the Error Catalog
<!-- API:API-CAT-055:END -->
<!-- SUB:SVC-API-CRUD:END -->
