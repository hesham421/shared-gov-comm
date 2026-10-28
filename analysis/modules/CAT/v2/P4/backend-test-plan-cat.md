# Backend test plan — Catalog / الكتالوج (CAT) — v2

Module : CAT · Version : v2 · Stage : P4 · Track : backend · Plan : test · Profile : commerce
Lane   : test-gen · questions forbidden · TC stream `TC-CAT-{seq:3}` shared with the frontend test plan (this file: TC-CAT-001 … TC-CAT-124, TC-CAT-183 … TC-CAT-193, TC-CAT-197 … TC-CAT-207, TC-CAT-211 … TC-CAT-219, TC-CAT-221 … TC-CAT-231; frontend: TC-CAT-125 … TC-CAT-182, TC-CAT-194 … TC-CAT-196, TC-CAT-208 … TC-CAT-210, TC-CAT-220 and TC-CAT-232)

## Header

### Sources (generated current state)
| Input | File | Version | Use |
|---|---|---|---|
| srs | `_state/current-srs.md` | CAT v2 (P1) | derivation source: AC-CAT-001 … 171 (Given / When / Then), RULE-CAT-001 … 053 messages, lookups block |
| registry-srs | `_state/current-registry-srs.md` | CAT v2 (P1) | ID ranges (REQ 148 · AC 171 · RULE 53) |
| registry-db | `_state/current-registry-db.md` | CAT v2 (P2) | XM register `records: []` → no integration TC |
| backend-execution-plan | `_state/current-backend-execution-plan.md` | CAT v2 (P3.1) | API-CAT-001 … 056, Error Catalog codes, split units (Package) |
| frontend-execution-plan | `_state/current-frontend-execution-plan.md` | CAT v2 (P3.2) | cross-check of the track split (ADR-CAT-052 §1) |
| api-spec | `_state/current-api-spec.yaml` (`api-spec-cat.yaml`, OpenAPI 3.1.0, 2.0.0) | CAT v2 (P3.1) | shape of every operation a TC asserts on — read by `x-api-id` |
| dependency-graph | `_state/current-dependency-graph.md` | CAT v2 | CAT is tier 1, no outbound edge |

### Delta
The current state holds no earlier test plan, so every TC is ADDED. The TC stream starts at 001. Within v2, the third gate-analysis revise ADDED TC-CAT-211 … TC-CAT-216 and MODIFIED TC-CAT-003, TC-CAT-020 and TC-CAT-203 (the RULE-CAT-003 message names the existing Item's code — G12) and TC-CAT-185, TC-CAT-186 and TC-CAT-187 (each now repairs the Item and reactivates it — G3, ADR-CAT-068). The fourth gate-analysis revise ADDED TC-CAT-217 (an import past its 120-second limit is rolled back whole — G4, REQ-CAT-139, ADR-CAT-071) and MODIFIED TC-CAT-188 (the GTIN of a deactivated Item stays active and refuses another Variant — G6). The fifth gate-analysis revise ADDED TC-CAT-218 (an image file held in the private storage is refused, and one public file may back two images — G1) and MODIFIED TC-CAT-184 (a second row: an empty `selections`, or an attribute with no value, answers CAT-400-REQUEST-INVALID — G3). The sixth gate-analysis revise ADDED TC-CAT-219 (reactivation refused when another active Item took the name — G2, AC-CAT-162), TC-CAT-221 and TC-CAT-222 (the default Variant moves on deactivation and on generation — G3, ADR-CAT-072) and MODIFIED TC-CAT-048 (scans 2001230012342, the code of the precondition's SCALE barcode — G8), TC-CAT-199 (the read of the deactivated Variant returns no active barcode — G1), TC-CAT-185 and TC-CAT-200 (each also asserts the one active default Variant — G3). The seventh gate-analysis revise MODIFIED TC-CAT-083 (empty baseUomCode / taxClassCode cells take PIECE / STANDARD, a filled cell is kept — G6), TC-CAT-184 (row 3: four attributes answer CAT-400-REQUEST-INVALID — G2), TC-CAT-217 (fault point after the first 500 rows are saved, and an undelayed run that measures the 60-second target — G3, ADR-CAT-074) and re-pointed TC-CAT-185, 186, 187, 212, 219, 221 and 222 to the REQs split from REQ-CAT-025, 130 and 135 (REQ-CAT-140 … 147 — G7); it ADDED no TC. The eighth gate-analysis revise ADDED TC-CAT-223 (a request that contradicts itself or names a record outside its parent answers CAT-400-REQUEST-INVALID and writes nothing — G2) and MODIFIED TC-CAT-056 and TC-CAT-218 (the image type, size and storage are refused by the FILE category `CAT_ITEM_IMAGE` at upload; CAT answers CAT-404-NOT-FOUND for a reference with no public URL and RULE-CAT-038 for a 21st image — G1, ADR-CAT-076). The ninth gate-analysis revise ADDED TC-CAT-224 (every committed Category change publishes one catalogue-change event — G1, AC-CAT-165, ADR-CAT-077), TC-CAT-225 (the Arabic normalisation keeps Arabic-Indic digits and still folds letter variants — G2) and TC-CAT-226 (an import row whose GTIN begins with an active scale prefix is rejected — G3, AC-CAT-166); it MODIFIED no TC. The tenth gate-analysis revise ADDED TC-CAT-227 … TC-CAT-230 (the parallel-request cases of four guards the backend plan states: a barcode add and a scale-layout create, an Item unit deactivation and a barcode add, a Variant generation and a value deactivation, an Item save and its Category's deactivation — G2, AC-CAT-167 … 170) and TC-CAT-231 (warehouse staff generate the missing internal barcode from the label screen — G1, AC-CAT-171, ADR-CAT-078), and MODIFIED TC-CAT-027 and TC-CAT-039 (boundary rows on the Bundle quantity and the conversion factor — G3) and TC-CAT-223 (rows 11 … 15: a PUT on an inactive Item unit, removed image, inactive Category, attribute or value answers CAT-400-REQUEST-INVALID, and the removed image does not clear the active primary — G6). No id is renumbered or REMOVED.

### Framework
`profile.stack.testing` is **agnostic**. Each block below is the whole contract: the consumer repo chooses its tool and turns each TC into a test. This plan names no framework, annotation or file layout.

### Open ADRs
- ADR-CAT-052 (this stage) is ACCEPTED and non-breaking. It covers the track split, the package binding, the host-data creation surface, catalogue isolation, fields the API document does not expose, and fault injection.
- ADR-CAT-053 (this stage, analyze round 2) is ACCEPTED and non-breaking. It replaces the unseeded AC values BAG and H87 by seeded values in TC-CAT-063, 120, 121, 122, so that those cases stay on seeded data, and records the frontend plan's unbound refusal texts.
- The TCs also cite ADR-CAT-041, 044, 045, 047 and 048 (P1/P3.1), and, from the gate-analysis revise, ADR-CAT-056 (files through the given FILE endpoints), ADR-CAT-058 (tenant provisioning), ADR-CAT-059 (reactivation invariants), ADR-CAT-060 (unit used by barcodes) and ADR-CAT-062 (Arabic normalisation). From the gate-analysis round 2 they cite ADR-CAT-063 (inactive parent refuses child writes; a Variant's barcodes follow it), ADR-CAT-064 (layout keeps its scale code length) and ADR-CAT-065 (CAT lookup keys served through API-CAT-052). From the gate-analysis round 3 they cite ADR-CAT-047 as amended (Category tree lock on deactivation), ADR-CAT-068 (three repairs stay allowed on an inactive Item) and ADR-CAT-069 (reactivation of Categories, Variant attributes and values). From the gate-analysis round 6 they cite ADR-CAT-072 (the default Variant moves), and from round 7 ADR-CAT-074 (the item import saves entities; its 60-second figure is a measured target). From round 8 they cite ADR-CAT-076 (image type, size and public storage enforced by the FILE category `CAT_ITEM_IMAGE`; the import's byte read waits on the core), and from round 9 ADR-CAT-077 (Category changes publish catalogue-change events — TC-CAT-224). From round 10 they cite ADR-CAT-078 (the barcode label screen generates the missing internal barcode — TC-CAT-231). None of them is BLOCKED.
- Core dependency (ADR-CAT-076): TC-CAT-017 … 020, 083, 084, 217 and 226 exercise API-CAT-049, whose byte read and type / size check depend on `CORE-GAP-file-document-metadata-read.md`. They stand as written and run once the core publishes that read; until then they are reported as blocked by the core gap, not as failures.
- Host values of an open lookup key (CAT_BRAND) are created and deactivated through the given mdl HTTP surface: the CAT_BRAND type is found by `POST /api/v1/mdl/lookup-types/search`, a value is added by `POST /api/v1/mdl/lookup-types/{id}/values` and deactivated by `DELETE /api/v1/mdl/lookup-values/{id}` (SRS A6). Each Host data line names the call it uses.

### Shared precondition S0 (applies to every TC; each TC adds only what it states)
- The caller is a user of preset role Owner holding every `PERM_CAT_*` permission, with a valid bearer token.
- The capability flags `variants` and `bundles` are on and readable through the given `SettingsApi` (FeatureFlags).
- The CAT bootstrap is present: the 10 lookup keys with their seeded values, the series CAT_ITEM / CAT_VARIANT / CAT_INTERNAL_BARCODE, and the 8 seeded Unit e-invoice codes (D-12). Every test tenant receives them at its provisioning: the given copies the lookups and series, and `CatTenantProvisioningContributor` copies the Unit e-invoice codes (ADR-CAT-058). TC-CAT-193 proves this for a newly provisioned tenant.
- An uploaded file (an image for API-CAT-018, an xlsx for API-CAT-049) is first uploaded through the given erp-core FILE endpoint: images to the FILE category `CAT_ITEM_IMAGE` (allowPublic, PNG / JPEG / WEBP, at most 5 MB — registered by CAT for every tenant, ADR-CAT-076), import files to the private storage. The TC then sends the returned `fileDocumentRef` in the JSON body of the CAT call. "POST the file" below means these two steps (ADR-CAT-056).
- The TC runs in a tenant of its own, and no data is shared between TCs (ADR-CAT-052 §4).
- Error bodies are read as `LocalizedException → {code, messageAr, messageEn}`. Success notices are read from `ApiResponse<T>` `code` / `messageAr` / `messageEn` (ADR-CAT-048).
- "Owner" below is that user. Every message is asserted in ar and en, copied from the SRS.

<!-- PHASE:TEST-PLAN-BE:START traces=AC-CAT-167,AC-CAT-168,AC-CAT-169,AC-CAT-170,AC-CAT-171,REQ-CAT-148,AC-CAT-165,AC-CAT-166,REQ-CAT-140,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,AC-CAT-162,AC-CAT-163,AC-CAT-164,AC-CAT-161,REQ-CAT-139,AC-CAT-153,AC-CAT-154,AC-CAT-155,AC-CAT-156,AC-CAT-157,AC-CAT-158,AC-CAT-159,AC-CAT-160,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,AC-CAT-130,AC-CAT-131,AC-CAT-132,AC-CAT-133,AC-CAT-134,AC-CAT-135,AC-CAT-136,AC-CAT-137,AC-CAT-139,AC-CAT-140,AC-CAT-141,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,AC-CAT-001,AC-CAT-002,AC-CAT-003,AC-CAT-004,AC-CAT-005,AC-CAT-006,AC-CAT-007,AC-CAT-008,AC-CAT-009,AC-CAT-010,AC-CAT-011,AC-CAT-012,AC-CAT-013,AC-CAT-016,AC-CAT-017,AC-CAT-018,AC-CAT-019,AC-CAT-020,AC-CAT-021,AC-CAT-022,AC-CAT-023,AC-CAT-024,AC-CAT-025,AC-CAT-026,AC-CAT-028,AC-CAT-029,AC-CAT-030,AC-CAT-031,AC-CAT-032,AC-CAT-033,AC-CAT-034,AC-CAT-035,AC-CAT-036,AC-CAT-037,AC-CAT-038,AC-CAT-039,AC-CAT-040,AC-CAT-041,AC-CAT-042,AC-CAT-043,AC-CAT-044,AC-CAT-045,AC-CAT-046,AC-CAT-047,AC-CAT-048,AC-CAT-049,AC-CAT-050,AC-CAT-051,AC-CAT-052,AC-CAT-053,AC-CAT-054,AC-CAT-055,AC-CAT-056,AC-CAT-057,AC-CAT-058,AC-CAT-059,AC-CAT-060,AC-CAT-061,AC-CAT-062,AC-CAT-063,AC-CAT-064,AC-CAT-065,AC-CAT-066,AC-CAT-067,AC-CAT-068,AC-CAT-069,AC-CAT-070,AC-CAT-071,AC-CAT-072,AC-CAT-073,AC-CAT-074,AC-CAT-077,AC-CAT-078,AC-CAT-079,AC-CAT-080,AC-CAT-081,AC-CAT-082,AC-CAT-083,AC-CAT-084,AC-CAT-085,AC-CAT-086,AC-CAT-087,AC-CAT-088,AC-CAT-089,AC-CAT-090,AC-CAT-091,AC-CAT-092,AC-CAT-093,AC-CAT-094,AC-CAT-095,AC-CAT-096,AC-CAT-097,AC-CAT-098,AC-CAT-099,AC-CAT-100,AC-CAT-101,AC-CAT-102,AC-CAT-103,AC-CAT-104,AC-CAT-105,AC-CAT-106,AC-CAT-107,AC-CAT-108,AC-CAT-109,AC-CAT-110,AC-CAT-111,AC-CAT-112,AC-CAT-113,AC-CAT-114,AC-CAT-115,AC-CAT-116,AC-CAT-117,AC-CAT-118,AC-CAT-119,AC-CAT-120,AC-CAT-121,AC-CAT-122,AC-CAT-123,AC-CAT-124,AC-CAT-125,AC-CAT-126,AC-CAT-127,AC-CAT-128,AC-CAT-129,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-024,REQ-CAT-025,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-096,REQ-CAT-097,REQ-CAT-098,REQ-CAT-099,REQ-CAT-100,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,AC-CAT-143,AC-CAT-144,AC-CAT-145,AC-CAT-146,AC-CAT-147,AC-CAT-148,AC-CAT-149,AC-CAT-150,AC-CAT-151,REQ-CAT-135,AC-CAT-152,AC-CAT-076,REQ-CAT-074 -->
## PHASE TEST-PLAN-BE — module scenarios

The phase holds 166 TCs, which is more than 12, so it is split into two SUBs:
- `RULE-SCENARIOS` (92): each AC whose Then names a RULE refusal, a RULE notice or a RULE-governed state transition, including the parallel-request cases of the concurrency guards (TC-CAT-200 … 205, 211, 227 … 230) and the inactive-parent matrix (TC-CAT-212), the import time limit (TC-CAT-217), the image reference and 20-image checks (TC-CAT-218) the name clash on reactivation (TC-CAT-219), the Arabic normalisation of names with digits (TC-CAT-225) and the scale-prefix check of an import row (TC-CAT-226).
- `API-SCENARIOS` (74): endpoint-driven ACs — happy paths, reads, paging, search, events, permission, tenant isolation, optimistic lock, tenant provisioning, CAT lookup reads, reactivation of Categories, Variant attributes and values, the default-Variant moves (TC-CAT-221, 222), request consistency and updates of inactive records (TC-CAT-223), the Category change events (TC-CAT-224) and the label screen's internal barcode (TC-CAT-231).

<!-- SUB:RULE-SCENARIOS:START traces=AC-CAT-167,AC-CAT-168,AC-CAT-169,AC-CAT-170,AC-CAT-166,REQ-CAT-141,REQ-CAT-142,REQ-CAT-143,REQ-CAT-144,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,AC-CAT-162,AC-CAT-161,REQ-CAT-139,AC-CAT-153,AC-CAT-154,AC-CAT-155,AC-CAT-156,AC-CAT-158,REQ-CAT-136,AC-CAT-130,AC-CAT-131,AC-CAT-132,AC-CAT-133,AC-CAT-134,AC-CAT-135,AC-CAT-136,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-086,REQ-CAT-101,AC-CAT-005,AC-CAT-006,AC-CAT-007,AC-CAT-009,AC-CAT-013,AC-CAT-016,AC-CAT-017,AC-CAT-019,AC-CAT-020,AC-CAT-023,AC-CAT-024,AC-CAT-028,AC-CAT-031,AC-CAT-032,AC-CAT-035,AC-CAT-036,AC-CAT-038,AC-CAT-041,AC-CAT-042,AC-CAT-043,AC-CAT-052,AC-CAT-053,AC-CAT-054,AC-CAT-055,AC-CAT-056,AC-CAT-057,AC-CAT-061,AC-CAT-062,AC-CAT-063,AC-CAT-064,AC-CAT-065,AC-CAT-071,AC-CAT-072,AC-CAT-073,AC-CAT-074,AC-CAT-077,AC-CAT-078,AC-CAT-081,AC-CAT-082,AC-CAT-083,AC-CAT-084,AC-CAT-087,AC-CAT-089,AC-CAT-090,AC-CAT-091,AC-CAT-092,AC-CAT-093,AC-CAT-096,AC-CAT-100,AC-CAT-101,AC-CAT-102,AC-CAT-105,AC-CAT-106,AC-CAT-108,AC-CAT-110,AC-CAT-112,AC-CAT-113,AC-CAT-114,AC-CAT-115,AC-CAT-116,AC-CAT-118,AC-CAT-125,AC-CAT-126,AC-CAT-128,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-009,REQ-CAT-013,REQ-CAT-016,REQ-CAT-018,REQ-CAT-019,REQ-CAT-022,REQ-CAT-023,REQ-CAT-027,REQ-CAT-030,REQ-CAT-031,REQ-CAT-034,REQ-CAT-035,REQ-CAT-037,REQ-CAT-040,REQ-CAT-041,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,REQ-CAT-075,REQ-CAT-076,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-085,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-094,REQ-CAT-098,REQ-CAT-099,REQ-CAT-100,REQ-CAT-103,REQ-CAT-104,REQ-CAT-106,REQ-CAT-108,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-116,REQ-CAT-123,REQ-CAT-124,REQ-CAT-126,AC-CAT-143,AC-CAT-144,AC-CAT-145,AC-CAT-146,AC-CAT-147,AC-CAT-148,AC-CAT-149,AC-CAT-150,AC-CAT-151,REQ-CAT-135 -->
### SUB RULE-SCENARIOS

<!-- TC:TC-CAT-001:START traces=AC-CAT-005,REQ-CAT-005,API-CAT-004,API-CAT-002 -->
### TC-CAT-001 — Deactivating a published Item unpublishes it and keeps it readable
Derived from : AC-CAT-005  (REQ-CAT-005)
Exercises    : API-CAT-004 DELETE /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-041 (deactivation sets UNPUBLISHED) · RULE-CAT-048 → CAT-200-DEACTIVATED (success notice)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an active Item with publishStateCode PUBLISHED (created through API-CAT-001, published through API-CAT-005); none of its Variants is a component of an active Bundle
Host data    : none
Steps        : 1. DELETE the Item by itemPk. 2. GET the Item by itemPk.
Expected     : 1 → 200, `code` CAT-200-DEACTIVATED with messageAr «تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة.» and messageEn "Deactivated. The record stays available to earlier documents and no longer appears in new selections."; data.isActiveFl false, data.publishStateCode UNPUBLISHED. 2 → 200, the Item is returned with isActiveFl false and publishStateCode UNPUBLISHED.
Test data    : the Item of the precondition (any valid nameAr)
<!-- TC:TC-CAT-001:END -->

<!-- TC:TC-CAT-002:START traces=AC-CAT-006,REQ-CAT-006,API-CAT-005 -->
### TC-CAT-002 — Reactivating an inactive Item keeps it unpublished
Derived from : AC-CAT-006  (REQ-CAT-006)
Exercises    : API-CAT-005 PATCH /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-041 (reactivation stays UNPUBLISHED — A7 lifecycle) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an inactive STANDARD Item with 1 active Variant and no Category (deactivated through API-CAT-004), with no other active Item carrying its nameAr
Host data    : none
Steps        : 1. PATCH the Item with body {isActiveFl: true}.
Expected     : 200; data.isActiveFl true, data.publishStateCode UNPUBLISHED, and data.variants holds exactly 1 active Variant.
Test data    : the Item of the precondition
<!-- TC:TC-CAT-002:END -->

<!-- TC:TC-CAT-003:START traces=AC-CAT-007,REQ-CAT-007,API-CAT-001 -->
### TC-CAT-003 — Second Item with the same Arabic name is refused
Derived from : AC-CAT-007  (REQ-CAT-007)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-003 → CAT-409-ITEM-NAME-DUPLICATE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an active Item named «شاحن سامسونج 25W» with code ITM-000012 (series CAT_ITEM positioned at ITM-000011 before its creation)
Host data    : none
Steps        : 1. POST an Item with nameAr «شاحن سامسونج 25W». 2. GET /api/v1/cat/items?text=شاحن سامسونج 25W&isActiveFl=true.
Expected     : 1 → 409, code CAT-409-ITEM-NAME-DUPLICATE, messageAr «يوجد صنف نشط بالاسم نفسه: ITM-000012. افتح الصنف الموجود أو غيّر الاسم.», messageEn "An active item already has this name: ITM-000012. Open the existing item or change the name." — the message names the existing Item's code, the next action (MODIFIED at the third gate-analysis revise, G12). 2 → totalElements 1: no Item was created.
Test data    : nameAr «شاحن سامسونج 25W», ITM-000012 (from the AC)
<!-- TC:TC-CAT-003:END -->

<!-- TC:TC-CAT-004:START traces=AC-CAT-009,REQ-CAT-009,API-CAT-003 -->
### TC-CAT-004 — A rolled-back Item update publishes no catalogue-change event
Derived from : AC-CAT-009  (REQ-CAT-009)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-049 → CAT-500-CHANGE-NOT-SAVED
Package      : CORE
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · an active Item; the commit of the next catalogue change is made to fail at the persistence / given outbox seam (ADR-CAT-052 §6); the outbox is observed
Host data    : none
Steps        : 1. PUT the Item with a changed nameEn and its current version. 2. Count the cat.CatalogueChanged events delivered for its itemPk after step 1.
Expected     : 1 → 500, code CAT-500-CHANGE-NOT-SAVED, messageAr «تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة.», messageEn "The change was not saved and no change notice was sent. Try again.". 2 → 0 events; a GET of the Item returns the former nameEn.
Test data    : any nameEn ≤ 200 characters
<!-- TC:TC-CAT-004:END -->

<!-- TC:TC-CAT-005:START traces=AC-CAT-013,REQ-CAT-013,API-CAT-001 -->
### TC-CAT-005 — Unreadable bundles flag refuses a BUNDLE save
Derived from : AC-CAT-013  (REQ-CAT-013)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-009 → CAT-500-CAPABILITY-UNREADABLE
Package      : CORE
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0, except that the given settings service (SettingsApi) returns no value for the `bundles` flag (ADR-CAT-052 §6) · an active STANDARD Item with an active Variant V
Host data    : none
Steps        : 1. POST an Item with itemKindCode BUNDLE, a valid nameAr and bundleComponents [{componentVariantFk: V, quantity: 1}]. 2. GET /api/v1/cat/items?itemKindCode=BUNDLE.
Expected     : 1 → 500, code CAT-500-CAPABILITY-UNREADABLE, messageAr «تعذّر التحقق من إعدادات القدرات الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.», messageEn "Capability settings could not be checked, and nothing was saved. Try again in a moment.". 2 → totalElements 0: no record was saved.
Test data    : itemKindCode BUNDLE (from the AC); nameAr placeholder `<valid Arabic name>`
<!-- TC:TC-CAT-005:END -->

<!-- TC:TC-CAT-006:START traces=AC-CAT-016,REQ-CAT-016,API-CAT-025,API-CAT-041,API-CAT-006 -->
### TC-CAT-006 — Creating a Variant attribute or generating Variants while `variants` is off is refused
Derived from : AC-CAT-016  (REQ-CAT-016)
Exercises    : API-CAT-025 POST /api/v1/cat/variant-attributes · API-CAT-006 POST /api/v1/cat/items/{id}/variants
Rule / code  : RULE-CAT-008 → CAT-422-CAPABILITY-OFF ({flag} = variants)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0, except that the `variants` flag is off · an active STANDARD Item I with only its default Variant, and an active attribute COLOUR with active values «أحمر» and «أزرق», both created while `variants` was on
Host data    : none
Steps        : 1. POST a Variant attribute with code SIZE and nameAr «المقاس». 2. POST to I a generation with selections [{variantAttributeFk: COLOUR, variantAttributeValueFks: [«أحمر», «أزرق»]}]. 3. GET I.
Expected     : 1 → 422, code CAT-422-CAPABILITY-OFF, messageAr «هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «variants» من الإعدادات لاستخدامها.», messageEn "This function is not switched on for your business. Turn on the «variants» capability flag in settings to use it."; nothing is created (with `variants` turned back on, API-CAT-041 lists no attribute SIZE). 2 → 422, the same code, messageAr and messageEn. 3 → I still has exactly 1 active Variant (its default Variant).
Test data    : code SIZE, nameAr «المقاس» (values used by AC-CAT-044 for the same operation); COLOUR «أحمر», «أزرق» (values used by AC-CAT-052)
<!-- TC:TC-CAT-006:END -->

<!-- TC:TC-CAT-007:START traces=AC-CAT-017,REQ-CAT-016,API-CAT-001,API-CAT-017,API-CAT-002 -->
### TC-CAT-007 — Creating a BUNDLE Item or saving Bundle components while `bundles` is off is refused
Derived from : AC-CAT-017  (REQ-CAT-016)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-017 PUT /api/v1/cat/items/{id}/bundle-components · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-008 → CAT-422-CAPABILITY-OFF ({flag} = bundles)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0, except that the `bundles` flag is off · active STANDARD Items with active Variants V and V2 · an active Bundle B whose only line is {V, 1}, saved while `bundles` was on
Host data    : none
Steps        : 1. POST an Item with itemKindCode BUNDLE, a valid nameAr and bundleComponents [{componentVariantFk: V, quantity: 1}]. 2. PUT B's line set [{componentVariantFk: V, quantity: 1}, {componentVariantFk: V2, quantity: 1}] with B's version. 3. GET B.
Expected     : 1 → 422, code CAT-422-CAPABILITY-OFF, messageAr «هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «bundles» من الإعدادات لاستخدامها.», messageEn "This function is not switched on for your business. Turn on the «bundles» capability flag in settings to use it."; nothing is created (B stays the only Item of kind BUNDLE in the tenant). 2 → 422, the same code, messageAr and messageEn. 3 → 200; B still has exactly 1 line {V, 1}.
Test data    : itemKindCode BUNDLE; nameAr placeholder `<valid Arabic name>`; B, V, V2 placeholders
<!-- TC:TC-CAT-007:END -->

<!-- TC:TC-CAT-008:START traces=AC-CAT-019,REQ-CAT-018,API-CAT-006,API-CAT-002 -->
### TC-CAT-008 — The existing default Variant takes the first selected value
Derived from : AC-CAT-019  (REQ-CAT-018)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-012 → CAT-201-EXISTING-VARIANTS-KEPT (success notice)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · a STANDARD Item whose only Variant is its default Variant VAR-000010 (series CAT_VARIANT positioned so that the default Variant receives VAR-000010) · an active attribute «المقاس» (code SIZE) with active values S and M
Host data    : none
Steps        : 1. POST a generation with selections [{variantAttributeFk: SIZE, variantAttributeValueFks: [S, M]}]. 2. GET the Item.
Expected     : 1 → 201. 2 → VAR-000010 carries the value S, exactly 1 new Variant carries the value M, and the Item has 2 active Variants; VAR-000010 keeps its variantPk.
Test data    : VAR-000010, «المقاس», S, M (from the AC)
<!-- TC:TC-CAT-008:END -->

<!-- TC:TC-CAT-009:START traces=AC-CAT-020,REQ-CAT-019,API-CAT-006,API-CAT-002 -->
### TC-CAT-009 — Barcodes stay on the default Variant when Variants are generated
Derived from : AC-CAT-020  (REQ-CAT-019)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-012 → CAT-201-EXISTING-VARIANTS-KEPT (success notice)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · a STANDARD Item whose default Variant carries the active GTIN 6223000000014 (added through API-CAT-014) · an active attribute with 3 active values
Host data    : none
Steps        : 1. POST a generation that selects the 3 values of that attribute (3 Variants). 2. GET the Item.
Expected     : 1 → 201, code CAT-201-EXISTING-VARIANTS-KEPT, messageAr «احتفظت المتغيّرات الحالية بأرقامها وباركوداتها وأُسندت إليها القيم الأولى المختارة.», messageEn "Existing variants kept their codes and barcodes and took the first selected values.". 2 → GTIN 6223000000014 still belongs to the default Variant, and the 2 new Variants carry 0 barcodes.
Test data    : GTIN 6223000000014 (from the AC); the 3 values are placeholders `<value-1..3>` of one attribute
<!-- TC:TC-CAT-009:END -->

<!-- TC:TC-CAT-010:START traces=AC-CAT-023,REQ-CAT-022,API-CAT-051 -->
### TC-CAT-010 — Variants turn-off check is not allowed while 3 Items use attributes
Derived from : AC-CAT-023  (REQ-CAT-022)
Exercises    : API-CAT-051 GET /api/v1/cat/capability-checks/turn-off?flag=variants
Rule / code  : RULE-CAT-010 → CAT-200-VARIANTS-IN-USE
Package      : SVC-API-INT
Scenario     : VIOLATION · data class VALID · language ALL
Preconditions: S0 · 3 active Items whose active Variants carry active attribute assignments (generated through API-CAT-006); no other active Item carries an assignment
Host data    : none
Steps        : 1. GET the turn-off check with flag=variants.
Expected     : 200; data.allowed false, data.dependentCount 3, data.dependentItems holds the 3 Items by code and nameAr; code CAT-200-VARIANTS-IN-USE, messageAr «لا يمكن إيقاف المتغيّرات لأن 3 صنفًا يستخدمها. أوقف متغيّرات هذه الأصناف أو أبقِ المتغيّرات مفعّلة.», messageEn "Variants cannot be turned off because 3 items use them. Deactivate the variants of these items or keep variants on.".
Test data    : 3 Items (placeholders `<Item-1..3>`)
<!-- TC:TC-CAT-010:END -->

<!-- TC:TC-CAT-011:START traces=AC-CAT-024,REQ-CAT-023,API-CAT-051 -->
### TC-CAT-011 — Bundles turn-off check is not allowed while 2 Bundles are active
Derived from : AC-CAT-024  (REQ-CAT-023)
Exercises    : API-CAT-051 GET /api/v1/cat/capability-checks/turn-off?flag=bundles
Rule / code  : RULE-CAT-011 → CAT-200-BUNDLES-IN-USE
Package      : SVC-API-INT
Scenario     : VIOLATION · data class VALID · language ALL
Preconditions: S0 · 2 active Items of kind BUNDLE (created through API-CAT-001)
Host data    : none
Steps        : 1. GET the turn-off check with flag=bundles.
Expected     : 200; data.allowed false, data.dependentCount 2; code CAT-200-BUNDLES-IN-USE, messageAr «لا يمكن إيقاف الحزم لوجود 2 حزمة نشطة. أوقف هذه الحزم أو أبقِ الحزم مفعّلة.», messageEn "Bundles cannot be turned off because 2 active bundles exist. Deactivate these bundles or keep bundles on.".
Test data    : 2 Bundles (placeholders `<Bundle-1..2>`)
<!-- TC:TC-CAT-011:END -->

<!-- TC:TC-CAT-012:START traces=AC-CAT-028,REQ-CAT-027,API-CAT-001 -->
### TC-CAT-012 — Item without an Arabic name is refused
Derived from : AC-CAT-028  (REQ-CAT-027)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-001 → CAT-422-NAME-AR-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0
Host data    : none
Steps        : 1. POST an Item with nameAr "" (empty) and no other entry.
Expected     : 422, code CAT-422-NAME-AR-INVALID, messageAr «أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.», messageEn "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."; no Item is created.
Test data    : nameAr empty (from the AC)
<!-- TC:TC-CAT-012:END -->

<!-- TC:TC-CAT-013:START traces=AC-CAT-031,REQ-CAT-030,API-CAT-001 -->
### TC-CAT-013 — Arabic name without an Arabic letter is refused
Derived from : AC-CAT-031  (REQ-CAT-030)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-001 → CAT-422-NAME-AR-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0
Host data    : none
Steps        : 1. POST an Item with nameAr "Galaxy A55".
Expected     : 422, code CAT-422-NAME-AR-INVALID, messageAr «أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.», messageEn "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."; no Item is saved.
Test data    : nameAr "Galaxy A55" (from the AC)
<!-- TC:TC-CAT-013:END -->

<!-- TC:TC-CAT-014:START traces=AC-CAT-032,REQ-CAT-031,API-CAT-001 -->
### TC-CAT-014 — English name of 201 characters is refused
Derived from : AC-CAT-032  (REQ-CAT-031)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-002 → CAT-422-NAME-EN-TOO-LONG
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: S0
Host data    : none
Steps        : 1. POST an Item with a valid nameAr and a nameEn of 201 characters.
Expected     : 422, code CAT-422-NAME-EN-TOO-LONG, messageAr «الاسم بالإنجليزية لا يتجاوز 200 حرف.», messageEn "The English name can have at most 200 characters."; no Item is saved.
Test data    : nameEn = `<201 characters>` (limit 200 + 1, from the AC); nameAr placeholder `<valid Arabic name>`
<!-- TC:TC-CAT-014:END -->

<!-- TC:TC-CAT-015:START traces=AC-CAT-035,REQ-CAT-034,API-CAT-003,API-CAT-002,API-CAT-008,API-CAT-007 -->
### TC-CAT-015 — An update that changes the Item or Variant code is refused
Derived from : AC-CAT-035  (REQ-CAT-034)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-008 PUT /api/v1/cat/variants/{id} · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-004 → CAT-422-CODE-SYSTEM-ASSIGNED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an Item with code ITM-000042 (series CAT_ITEM positioned at ITM-000041 before its creation) and its default Variant D (D's code recorded)
Host data    : none
Steps        : 1. PUT the Item with its current fields and version and code ITM-999999. 2. GET the Item. 3. PUT D with its current fields and version and code VAR-999999. 4. GET D.
Expected     : 1 → 422, code CAT-422-CODE-SYSTEM-ASSIGNED, messageAr «يُعيَّن الرمز تلقائيًا ولا يمكن إدخاله أو تعديله.», messageEn "The code is assigned automatically and cannot be entered or changed.". 2 → code ITM-000042. 3 → 422, the same code, messageAr and messageEn. 4 → D keeps its recorded code.
Test data    : ITM-000042, ITM-999999 (from the AC); VAR-999999 (this case's own Variant code)
<!-- TC:TC-CAT-015:END -->

<!-- TC:TC-CAT-016:START traces=AC-CAT-036,REQ-CAT-035,API-CAT-001,API-CAT-038 -->
### TC-CAT-016 — Number series failure stores neither Item nor Variant
Derived from : AC-CAT-036  (REQ-CAT-035)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-038 GET /api/v1/cat/items
Rule / code  : RULE-CAT-005 → CAT-500-CODE-NOT-ISSUED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0, except that the given NumberSeriesApi returns an error for series CAT_ITEM (ADR-CAT-052 §6) · the tenant holds 0 Items
Host data    : none
Steps        : 1. POST an Item with a valid nameAr. 2. GET /api/v1/cat/items (no filter). 3. GET /api/v1/cat/variants.
Expected     : 1 → 500, code CAT-500-CODE-NOT-ISSUED, messageAr «تعذّر إصدار رمز جديد الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.», messageEn "A new code could not be issued, and nothing was saved. Try again in a moment.". 2 → totalElements 0 (0 Items). 3 → totalElements 0 (0 Variants).
Test data    : nameAr placeholder `<valid Arabic name>`
<!-- TC:TC-CAT-016:END -->

<!-- TC:TC-CAT-017:START traces=AC-CAT-038,REQ-CAT-037,API-CAT-049 -->
### TC-CAT-017 — Every import row is validated before any Item is created
Derived from : AC-CAT-038  (REQ-CAT-037)
Exercises    : API-CAT-049 POST /api/v1/cat/item-imports
Rule / code  : RULE-CAT-045 (row validation; RULE-CAT-001 for the column) → CAT-200-IMPORT-ROW-REJECTED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an xlsx built on the import sheet (API-CAT-048) with 50 data rows; row 7 has an empty nameAr; the other 49 rows are valid
Host data    : none
Steps        : 1. POST the file.
Expected     : 200; data.rejectedRows holds row 7 with column nameAr; 49 rows valid (data.itemsCreated 49); row 7 is rejected in the validation pass, which precedes any write (no Item exists for row 7, and no Item was written before the validation result).
Test data    : 50 rows, row 7 nameAr empty (from the AC); the other rows carry placeholder valid Arabic names `<name-1..49>`
<!-- TC:TC-CAT-017:END -->

<!-- TC:TC-CAT-018:START traces=AC-CAT-041,REQ-CAT-040,API-CAT-049,API-CAT-038 -->
### TC-CAT-018 — A .csv import file is refused whole
Derived from : AC-CAT-041  (REQ-CAT-040)
Exercises    : API-CAT-049 POST /api/v1/cat/item-imports · API-CAT-038 GET /api/v1/cat/items
Rule / code  : RULE-CAT-044 → CAT-422-IMPORT-FILE-REJECTED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the tenant holds 0 Items
Host data    : none
Steps        : 1. Upload a .csv file through the given FILE endpoint (private storage) and POST `{fileDocumentRef}` to API-CAT-049. 2. GET /api/v1/cat/items.
Expected     : 1 → 422, code CAT-422-IMPORT-FILE-REJECTED, messageAr «الملف غير مقبول: يجب أن يكون بصيغة xlsx بحجم لا يتجاوز 10 ميجابايت وحتى 5000 صف، وبعناوين ورقة الاستيراد نفسها. نزّل ورقة الاستيراد واستخدمها.», messageEn "The file was not accepted: it must be xlsx, at most 10 MB and 5,000 rows, with the import sheet headers. Download the import sheet and use it.". 2 → totalElements 0 (0 Items created).
Test data    : a .csv file (from the AC)
<!-- TC:TC-CAT-018:END -->

<!-- TC:TC-CAT-019:START traces=AC-CAT-042,REQ-CAT-041,API-CAT-049 -->
### TC-CAT-019 — An import row with an inactive brand is skipped, the others are created
Derived from : AC-CAT-042  (REQ-CAT-041)
Exercises    : API-CAT-049 POST /api/v1/cat/item-imports
Rule / code  : RULE-CAT-045 (RULE-CAT-006 for the column) → CAT-200-IMPORT-ROW-REJECTED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an xlsx on the import sheet with 10 data rows; row 4 carries brandCode «XYZ»; the other 9 rows are valid and carry no brandCode
Host data    : CAT_BRAND XYZ — absent: never created, so the CAT_BRAND type found by POST /api/v1/mdl/lookup-types/search has no XYZ, and this case makes no POST /api/v1/mdl/lookup-types/{id}/values call
Steps        : 1. POST the file.
Expected     : 200; data.itemsCreated 9; row 4 creates nothing; data.rejectedRows holds row 4 with column brandCode.
Test data    : brandCode «XYZ» on row 4 (from the AC); 9 valid rows with placeholder names `<name-1..9>`
<!-- TC:TC-CAT-019:END -->

<!-- TC:TC-CAT-020:START traces=AC-CAT-043,REQ-CAT-041,API-CAT-049 -->
### TC-CAT-020 — A repeated Arabic name in the same import file is rejected on the later row
Derived from : AC-CAT-043  (REQ-CAT-041)
Exercises    : API-CAT-049 POST /api/v1/cat/item-imports
Rule / code  : RULE-CAT-045 (RULE-CAT-003 as {reason}) → CAT-200-IMPORT-ROW-REJECTED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · no active Item named «كوب زجاج» in the tenant · an xlsx on the import sheet whose rows 2 and 9 both carry nameAr «كوب زجاج» (the other rows valid and distinct)
Host data    : none
Steps        : 1. POST the file. 2. GET /api/v1/cat/items?text=كوب زجاج&isActiveFl=true and record the code `<code>` of the Item found.
Expected     : 1 → 200; row 2 creates the Item; data.rejectedRows holds row 9 with column nameAr, and its reason reads ar «يوجد صنف نشط بالاسم نفسه: <code>. افتح الصنف الموجود أو غيّر الاسم.» · en "An active item already has this name: <code>. Open the existing item or change the name." (inside the RULE-CAT-045 row message). 2 → totalElements 1, and `<code>` is the code named in step 1's reason (MODIFIED at the third gate-analysis revise, G12).
Test data    : nameAr «كوب زجاج» on rows 2 and 9 (from the AC)
<!-- TC:TC-CAT-020:END -->

<!-- TC:TC-CAT-021:START traces=AC-CAT-052,REQ-CAT-050,API-CAT-009,API-CAT-002,API-CAT-007 -->
### TC-CAT-021 — A deactivated Variant leaves the Item but stays readable
Derived from : AC-CAT-052  (REQ-CAT-050)
Exercises    : API-CAT-009 DELETE /api/v1/cat/variants/{id} · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-048 (soft deactivation) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an Item with 4 active Variants, among them L / «أزرق» (generated through API-CAT-006 from SIZE M, L × COLOUR «أحمر», «أزرق»); that Variant is not a Bundle component
Host data    : none
Steps        : 1. DELETE the Variant L / «أزرق». 2. GET the Item. 3. GET the Variant by its variantPk.
Expected     : 1 → 200. 2 → 3 active Variants. 3 → 200, the Variant with isActiveFl false.
Test data    : L / «أزرق» (from the AC)
<!-- TC:TC-CAT-021:END -->

<!-- TC:TC-CAT-022:START traces=AC-CAT-053,REQ-CAT-051,API-CAT-006,API-CAT-002 -->
### TC-CAT-022 — A second Variant with an existing combination is refused
Derived from : AC-CAT-053  (REQ-CAT-051)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-013 → CAT-409-VARIANT-COMBINATION-DUPLICATE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · a STANDARD Item with an active Variant M / «أحمر» (attributes SIZE and COLOUR)
Host data    : none
Steps        : 1. POST a generation with selections SIZE [M] and COLOUR [«أحمر»]. 2. GET the Item.
Expected     : 1 → 409, code CAT-409-VARIANT-COMBINATION-DUPLICATE, messageAr «هذه التركيبة من القيم موجودة في متغيّر آخر لهذا الصنف.», messageEn "This combination of values already exists on another variant of this item.". 2 → the active Variant count is unchanged: no Variant was created.
Test data    : M / «أحمر» (from the AC)
<!-- TC:TC-CAT-022:END -->

<!-- TC:TC-CAT-023:START traces=AC-CAT-054,REQ-CAT-052,API-CAT-006,API-CAT-002 -->
### TC-CAT-023 — Generation beyond 300 active Variants is refused whole
Derived from : AC-CAT-054  (REQ-CAT-052)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-014 → CAT-422-VARIANT-LIMIT-EXCEEDED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: S0 · a STANDARD Item with 290 active Variants (generated through API-CAT-006) · active attribute values that give 20 combinations the Item does not yet carry, within at most 3 attributes
Host data    : none
Steps        : 1. POST a generation whose selection yields those 20 new combinations. 2. GET the Item.
Expected     : 1 → 422, code CAT-422-VARIANT-LIMIT-EXCEEDED, messageAr «الحد الأقصى 3 خصائص و300 متغيّر نشط للصنف الواحد.», messageEn "An item can have at most 3 attributes and 300 active variants.". 2 → still 290 active Variants (0 created).
Test data    : 290 existing + 20 new (from the AC); the attribute values are placeholders
<!-- TC:TC-CAT-023:END -->

<!-- TC:TC-CAT-024:START traces=AC-CAT-055,REQ-CAT-053,API-CAT-009,API-CAT-007 -->
### TC-CAT-024 — The last active Variant of an active Item stays active
Derived from : AC-CAT-055  (REQ-CAT-053)
Exercises    : API-CAT-009 DELETE /api/v1/cat/variants/{id} · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-015 → CAT-409-LAST-ACTIVE-VARIANT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an active Item with exactly 1 active Variant
Host data    : none
Steps        : 1. DELETE that Variant. 2. GET the Variant.
Expected     : 1 → 409, code CAT-409-LAST-ACTIVE-VARIANT, messageAr «لا يمكن إيقاف آخر متغيّر نشط لصنف نشط. أوقف الصنف نفسه بدلًا من ذلك.», messageEn "The last active variant of an active item cannot be deactivated. Deactivate the item instead.". 2 → isActiveFl true.
Test data    : —
<!-- TC:TC-CAT-024:END -->

<!-- TC:TC-CAT-025:START traces=AC-CAT-056,REQ-CAT-054,API-CAT-031,API-CAT-026 -->
### TC-CAT-025 — A value carried by 12 active Variants cannot be deactivated
Derived from : AC-CAT-056  (REQ-CAT-054)
Exercises    : API-CAT-031 DELETE /api/v1/cat/variant-attribute-values/{id} · API-CAT-026 GET /api/v1/cat/variant-attributes/{id}
Rule / code  : RULE-CAT-017 → CAT-409-ATTRIBUTE-VALUE-IN-USE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the value M (of attribute SIZE) carried by exactly 12 active Variants
Host data    : none
Steps        : 1. DELETE the value M. 2. GET the attribute SIZE.
Expected     : 1 → 409, code CAT-409-ATTRIBUTE-VALUE-IN-USE, messageAr «هذه الخاصية أو القيمة مستخدمة في 12 متغيّرًا نشطًا. أوقف هذه المتغيّرات أولًا.», messageEn "This attribute or value is used by 12 active variants. Deactivate those variants first.". 2 → M is still listed with isActiveFl true.
Test data    : value M, 12 Variants (from the AC)
<!-- TC:TC-CAT-025:END -->

<!-- TC:TC-CAT-026:START traces=AC-CAT-057,REQ-CAT-055,API-CAT-025,API-CAT-041 -->
### TC-CAT-026 — A second attribute with code SIZE is refused
Derived from : AC-CAT-057  (REQ-CAT-055)
Exercises    : API-CAT-025 POST /api/v1/cat/variant-attributes · API-CAT-041 GET /api/v1/cat/variant-attributes
Rule / code  : RULE-CAT-016 → CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the attribute SIZE exists
Host data    : none
Steps        : 1. POST an attribute with code SIZE and a nameAr not used by any attribute. 2. GET the attribute list.
Expected     : 1 → 409, code CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE, messageAr «الرمز أو الاسم مستخدم بالفعل. استخدم رمزًا (حتى 30 حرفًا لاتينيًا أو رقمًا أو شرطة) واسمًا مختلفين.», messageEn "The code or name is already used. Use a different code (up to 30 Latin letters, digits or hyphens) and name.". 2 → exactly one attribute with code SIZE: nothing created.
Test data    : code SIZE (from the AC); nameAr placeholder `<unused Arabic name>`
<!-- TC:TC-CAT-026:END -->

<!-- TC:TC-CAT-027:START traces=AC-CAT-061,REQ-CAT-059,REQ-CAT-056,REQ-CAT-057,API-CAT-001,API-CAT-017,API-CAT-002 -->
### TC-CAT-027 — A Bundle with no component line, or with a quantity outside its bounds, is refused
Derived from : AC-CAT-061  (REQ-CAT-059; the boundary rows bound the quantity of REQ-CAT-056 and REQ-CAT-057)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-017 PUT /api/v1/cat/items/{id}/bundle-components · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-018 → CAT-422-BUNDLE-COMPONENTS-REQUIRED (row 1) · PLATFORM-STD CAT-400-REQUEST-INVALID (rows 3 … 6 — the API document's bounds on `quantity`: `exclusiveMinimum: 0`, `maximum: 999999.999`, `multipleOf: 0.001`, checked before RULE-CAT-018; PHASE CORE "Request numbers" of the backend plan)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class BOUNDARY · language ALL · parameterised (one row per case)
Preconditions: S0 · an active STANDARD Item with an active Variant V · an active Bundle B whose only line is {V, 1}, read with its version
Host data    : none
Steps        : 1. Send each row below; every POST uses its own valid nameAr. 2. GET B and GET /api/v1/cat/items?itemKindCode=BUNDLE.

| Row | Endpoint | Request | Expected |
|---|---|---|---|
| 1 | API-CAT-001 | POST an Item with itemKindCode BUNDLE and bundleComponents [] | 422, code CAT-422-BUNDLE-COMPONENTS-REQUIRED, messageAr «أضف مكوّنًا واحدًا على الأقل بكمية أكبر من صفر (حتى 3 أرقام عشرية).», messageEn "Add at least one component with a quantity above zero (up to 3 decimals)." |
| 2 | API-CAT-001 | POST a BUNDLE with bundleComponents [{V, 999999.999}] | 201 — the largest quantity the column holds is accepted and read back as 999999.999 |
| 3 | API-CAT-001 | POST a BUNDLE with bundleComponents [{V, 1000000}] | 400, code CAT-400-REQUEST-INVALID, messageAr «الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة.», messageEn "The request is not valid. Check the highlighted fields and try again." — never CAT-500-SERVER-ERROR |
| 4 | API-CAT-001 | POST a BUNDLE with bundleComponents [{V, 0}] | 400, code CAT-400-REQUEST-INVALID, the same messageAr and messageEn |
| 5 | API-CAT-001 | POST a BUNDLE with bundleComponents [{V, 0.0005}] | 400, code CAT-400-REQUEST-INVALID, the same messageAr and messageEn (more than 3 decimals) |
| 6 | API-CAT-017 | PUT B's lines [{bundleComponentPk: B's line, V, 1000000}] with B's version | 400, code CAT-400-REQUEST-INVALID, the same messageAr and messageEn |

Expected     : 1 → every row answers as its table cell says. 2 → B still has the one line {V, 1} and its version is unchanged; the Bundles listed are B and the Bundle of row 2 only: rows 1 and 3 … 6 saved nothing.
Test data    : 0 component lines (from the AC); 999999.999, 1000000, 0, 0.0005 (the column bounds — gate-analysis round 10, G3); nameAr placeholders `<valid Arabic name-1..5>`
<!-- TC:TC-CAT-027:END -->

<!-- TC:TC-CAT-028:START traces=AC-CAT-062,REQ-CAT-060,API-CAT-001 -->
### TC-CAT-028 — The Variant of a Bundle cannot be a component of another Bundle
Derived from : AC-CAT-062  (REQ-CAT-060)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-019 → CAT-422-BUNDLE-COMPONENT-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an active Bundle B1 with its default Variant V(B1)
Host data    : none
Steps        : 1. POST a new Bundle B2 (itemKindCode BUNDLE) with bundleComponents [{componentVariantFk: V(B1), quantity: 1}].
Expected     : 422, code CAT-422-BUNDLE-COMPONENT-INVALID, messageAr «المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.», messageEn "A component must be an active variant of an item that is not a bundle, listed once per bundle."; B2 is not saved.
Test data    : B1, B2 (from the AC); B2 nameAr placeholder
<!-- TC:TC-CAT-028:END -->

<!-- TC:TC-CAT-029:START traces=AC-CAT-063,REQ-CAT-061,API-CAT-006,API-CAT-002 -->
### TC-CAT-029 — Variant attributes on a Bundle are refused
Derived from : AC-CAT-063  (REQ-CAT-061)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-020 → CAT-422-BUNDLE-SINGLE-VARIANT
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · a Bundle Item with its default Variant · the active attribute SIZE with active values
Host data    : none
Steps        : 1. POST a generation for the Bundle that selects SIZE values. 2. GET the Bundle.
Expected     : 1 → 422, code CAT-422-BUNDLE-SINGLE-VARIANT, messageAr «الحزمة لها متغيّر واحد فقط ولا تقبل خصائص المتغيّرات.», messageEn "A bundle has one variant only and takes no variant attributes.". 2 → still 1 Variant (0 created).
Test data    : SIZE values (placeholders `<SIZE value-1..n>`)
<!-- TC:TC-CAT-029:END -->

<!-- TC:TC-CAT-030:START traces=AC-CAT-064,REQ-CAT-062,API-CAT-009,API-CAT-007 -->
### TC-CAT-030 — A component of 2 active Bundles stays active
Derived from : AC-CAT-064  (REQ-CAT-062)
Exercises    : API-CAT-009 DELETE /api/v1/cat/variants/{id} · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-021 → CAT-409-VARIANT-IN-ACTIVE-BUNDLE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · a Variant V that is an active component of 2 active Bundles; V's Item has at least one other active Variant (so RULE-CAT-015 does not apply)
Host data    : none
Steps        : 1. DELETE V. 2. GET V.
Expected     : 1 → 409, code CAT-409-VARIANT-IN-ACTIVE-BUNDLE, messageAr «هذا المتغيّر مكوّن في 2 حزمة نشطة. أزله من هذه الحزم أو أوقفها أولًا.», messageEn "This variant is a component of 2 active bundles. Remove it from those bundles or deactivate them first.". 2 → isActiveFl true.
Test data    : 2 Bundles (from the AC)
<!-- TC:TC-CAT-030:END -->

<!-- TC:TC-CAT-031:START traces=AC-CAT-065,REQ-CAT-063,API-CAT-003,API-CAT-002 -->
### TC-CAT-031 — The Item kind cannot change after saving
Derived from : AC-CAT-065  (REQ-CAT-063)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-022 → CAT-422-ITEM-KIND-FIXED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · a saved STANDARD Item
Host data    : none
Steps        : 1. PUT the Item with its current fields and version and itemKindCode BUNDLE. 2. GET the Item.
Expected     : 1 → 422, code CAT-422-ITEM-KIND-FIXED, messageAr «لا يمكن تغيير نوع الصنف بعد حفظه. أنشئ صنفًا جديدًا بالنوع المطلوب.», messageEn "The item kind cannot be changed after saving. Create a new item of the required kind.". 2 → itemKindCode STANDARD.
Test data    : STANDARD → BUNDLE (from the AC)
<!-- TC:TC-CAT-031:END -->

<!-- TC:TC-CAT-032:START traces=AC-CAT-071,REQ-CAT-069,API-CAT-023,API-CAT-022 -->
### TC-CAT-032 — A Category cannot take its own sub-category as parent
Derived from : AC-CAT-071  (REQ-CAT-069)
Exercises    : API-CAT-023 PUT /api/v1/cat/categories/{id} · API-CAT-022 GET /api/v1/cat/categories/{id}
Rule / code  : RULE-CAT-023 → CAT-422-CATEGORY-PARENT-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the top-level Category «إلكترونيات» and its sub-category PHONES
Host data    : none
Steps        : 1. PUT «إلكترونيات» with its current names, sortOrder and version and parentCategoryFk = PHONES. 2. GET «إلكترونيات».
Expected     : 1 → 422, code CAT-422-CATEGORY-PARENT-INVALID, messageAr «الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.», messageEn "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels.". 2 → parentCategoryFk still empty: nothing saved.
Test data    : PHONES, «إلكترونيات» (from the AC); the code of «إلكترونيات» is a placeholder
<!-- TC:TC-CAT-032:END -->

<!-- TC:TC-CAT-033:START traces=AC-CAT-072,REQ-CAT-070,API-CAT-021,API-CAT-039 -->
### TC-CAT-033 — A second «هواتف» under the same parent is refused
Derived from : AC-CAT-072  (REQ-CAT-070)
Exercises    : API-CAT-021 POST /api/v1/cat/categories · API-CAT-039 GET /api/v1/cat/categories
Rule / code  : RULE-CAT-024 → CAT-409-CATEGORY-DUPLICATE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an active Category «هواتف» under «إلكترونيات»
Host data    : none
Steps        : 1. POST a Category with an unused code, nameAr «هواتف» and parentCategoryFk «إلكترونيات». 2. GET the Category tree.
Expected     : 1 → 409, code CAT-409-CATEGORY-DUPLICATE, messageAr «يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا.», messageEn "The category code already exists, or the name already exists under the same parent. Use a different code or name.". 2 → «إلكترونيات» still has one child «هواتف»: nothing created.
Test data    : «هواتف», «إلكترونيات» (from the AC); the second code is a placeholder `<unused code>`
<!-- TC:TC-CAT-033:END -->

<!-- TC:TC-CAT-034:START traces=AC-CAT-073,REQ-CAT-071,API-CAT-024,API-CAT-022 -->
### TC-CAT-034 — A Category holding 4 active Items stays active
Derived from : AC-CAT-073  (REQ-CAT-071)
Exercises    : API-CAT-024 DELETE /api/v1/cat/categories/{id} · API-CAT-022 GET /api/v1/cat/categories/{id}
Rule / code  : RULE-CAT-025 → CAT-409-CATEGORY-IN-USE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · a Category holding exactly 4 active Items and 0 active sub-categories
Host data    : none
Steps        : 1. DELETE the Category. 2. GET it.
Expected     : 1 → 409, code CAT-409-CATEGORY-IN-USE, messageAr «لا يمكن إيقاف الفئة لوجود 4 فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا.», messageEn "The category cannot be deactivated while it holds 4 active sub-categories or items. Move or deactivate them first.". 2 → isActiveFl true.
Test data    : 4 Items (from the AC)
<!-- TC:TC-CAT-034:END -->

<!-- TC:TC-CAT-035:START traces=AC-CAT-074,REQ-CAT-072,API-CAT-001 -->
### TC-CAT-035 — An Item cannot be saved into an inactive Category
Derived from : AC-CAT-074  (REQ-CAT-072)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-026 → CAT-422-CATEGORY-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · a Category with code OLD, deactivated through API-CAT-024
Host data    : none
Steps        : 1. POST an Item with a valid nameAr and categoryFk = OLD.
Expected     : 422, code CAT-422-CATEGORY-INACTIVE, messageAr «الفئة المختارة غير نشطة. اختر فئة نشطة.», messageEn "The selected category is inactive. Choose an active category."; nothing is saved.
Test data    : Category OLD (from the AC); nameAr placeholder
<!-- TC:TC-CAT-035:END -->

<!-- TC:TC-CAT-036:START traces=AC-CAT-077,REQ-CAT-075,API-CAT-001 -->
### TC-CAT-036 — A brand code that is not an active CAT_BRAND value is refused
Derived from : AC-CAT-077  (REQ-CAT-075)
Exercises    : API-CAT-001 POST /api/v1/cat/items
Rule / code  : RULE-CAT-006 → CAT-422-LOOKUP-VALUE-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · CAT_BRAND has no value «XYZ»
Host data    : CAT_BRAND XYZ — absent: never created, so the CAT_BRAND type found by POST /api/v1/mdl/lookup-types/search has no XYZ, and this case makes no POST /api/v1/mdl/lookup-types/{id}/values call
Steps        : 1. POST an Item with a valid nameAr and brandCode «XYZ».
Expected     : 422, code CAT-422-LOOKUP-VALUE-INACTIVE, messageAr «القيمة المختارة غير متاحة. اختر قيمة نشطة من القائمة.», messageEn "The selected value is not available. Choose an active value from the list."; nothing is saved.
Test data    : brandCode «XYZ» (from the AC)
<!-- TC:TC-CAT-036:END -->

<!-- TC:TC-CAT-037:START traces=AC-CAT-078,REQ-CAT-076,API-CAT-002 -->
### TC-CAT-037 — A brand deactivated later stays on existing Items
Derived from : AC-CAT-078  (REQ-CAT-076)
Exercises    : API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-006 (an unchanged value deactivated later is kept) · —
Package      : CORE
Scenario     : STATE · data class EDGE · language ALL
Preconditions: S0 · 6 Items with brandCode OLDBRAND (created through API-CAT-001 while OLDBRAND was active); then OLDBRAND is deactivated in the core lookup module
Host data    : CAT_BRAND OLDBRAND — created active by the given mdl call POST /api/v1/mdl/lookup-types/{id}/values ({id} = the CAT_BRAND type found by POST /api/v1/mdl/lookup-types/search) before the 6 Items are created, then made inactive by DELETE /api/v1/mdl/lookup-values/{id} ({id} = the OLDBRAND value)
Steps        : 1. GET one of the 6 Items.
Expected     : 200; data.brandCode OLDBRAND. (The other half of the AC — the brand field offers 0 entries named OLDBRAND — is asserted on the frontend, TC-CAT-139.)
Test data    : OLDBRAND, 6 Items (from the AC)
<!-- TC:TC-CAT-037:END -->

<!-- TC:TC-CAT-038:START traces=AC-CAT-081,REQ-CAT-079,API-CAT-013,API-CAT-002,API-CAT-011 -->
### TC-CAT-038 — A deactivated Item unit leaves the Item and stays readable
Derived from : AC-CAT-081  (REQ-CAT-079)
Exercises    : API-CAT-013 DELETE /api/v1/cat/item-units/{id} · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-011 GET /api/v1/cat/item-units/{id}
Rule / code  : RULE-CAT-048 → CAT-200-DEACTIVATED (success notice)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an Item with the active Item unit CARTON (added through API-CAT-010)
Host data    : none
Steps        : 1. DELETE the Item unit CARTON. 2. GET the Item. 3. GET the Item unit by itemUnitPk.
Expected     : 1 → 200, isActiveFl false. 2 → itemUnits lists 0 active CARTON units. 3 → 200, the unit returned with isActiveFl false.
Test data    : CARTON (from the AC)
<!-- TC:TC-CAT-038:END -->

<!-- TC:TC-CAT-039:START traces=AC-CAT-082,REQ-CAT-080,REQ-CAT-077,API-CAT-010,API-CAT-002 -->
### TC-CAT-039 — An Item unit equal to the base unit, or with a conversion factor outside its bounds, is refused
Derived from : AC-CAT-082  (REQ-CAT-080; the boundary rows bound the factor of REQ-CAT-077)
Exercises    : API-CAT-010 POST /api/v1/cat/items/{id}/units · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-027 → CAT-422-ITEM-UNIT-INVALID (row 1) · PLATFORM-STD CAT-400-REQUEST-INVALID (rows 3 … 5 — the API document's bounds on `conversionFactor`: `exclusiveMinimum: 0`, `maximum: 999999.999999`, `multipleOf: 0.000001`, checked before RULE-CAT-027; PHASE CORE "Request numbers" of the backend plan)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class BOUNDARY · language ALL · parameterised (one row per case)
Preconditions: S0 · an active Item with base unit PIECE and no Item unit
Host data    : none
Steps        : 1. Send each row below to the Item. 2. GET the Item.

| Row | Request | Expected |
|---|---|---|
| 1 | POST {uomCode: PIECE, conversionFactor: 1} | 422, code CAT-422-ITEM-UNIT-INVALID, messageAr «الوحدة يجب أن تختلف عن الوحدة الأساسية وألا تتكرر، ومعامل التحويل أكبر من صفر (حتى 6 أرقام عشرية).», messageEn "The unit must differ from the base unit and appear once, with a conversion factor above zero (up to 6 decimals)." |
| 2 | POST {uomCode: CARTON, conversionFactor: 999999.999999} | 201 — the largest factor the column holds is accepted and read back as 999999.999999 |
| 3 | POST {uomCode: BOX, conversionFactor: 1000000} | 400, code CAT-400-REQUEST-INVALID, messageAr «الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة.», messageEn "The request is not valid. Check the highlighted fields and try again." — never CAT-500-SERVER-ERROR |
| 4 | POST {uomCode: BOX, conversionFactor: 0} | 400, code CAT-400-REQUEST-INVALID, the same messageAr and messageEn |
| 5 | POST {uomCode: BOX, conversionFactor: 0.0000001} | 400, code CAT-400-REQUEST-INVALID, the same messageAr and messageEn (more than 6 decimals) |

Expected     : 1 → every row answers as its table cell says. 2 → the Item has exactly one active Item unit, CARTON with conversionFactor 999999.999999: rows 1 and 3 … 5 saved nothing.
Test data    : PIECE, factor 1 (from the AC); CARTON, BOX, 999999.999999, 1000000, 0, 0.0000001 (the column bounds — gate-analysis round 10, G3)
<!-- TC:TC-CAT-039:END -->

<!-- TC:TC-CAT-040:START traces=AC-CAT-083,REQ-CAT-081,API-CAT-012,API-CAT-011 -->
### TC-CAT-040 — The conversion factor of a saved Item unit cannot change
Derived from : AC-CAT-083  (REQ-CAT-081)
Exercises    : API-CAT-012 PUT /api/v1/cat/item-units/{id} · API-CAT-011 GET /api/v1/cat/item-units/{id}
Rule / code  : RULE-CAT-028 → CAT-422-CONVERSION-FACTOR-FIXED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the Item unit CARTON with conversionFactor 12
Host data    : none
Steps        : 1. PUT the unit with its current flags and version and conversionFactor 24. 2. GET the unit.
Expected     : 1 → 422, code CAT-422-CONVERSION-FACTOR-FIXED, messageAr «لا يمكن تعديل معامل التحويل بعد الحفظ. أوقف هذه الوحدة وأضفها من جديد بالمعامل الصحيح.», messageEn "The conversion factor cannot be changed after saving. Deactivate this unit and add it again with the correct factor.". 2 → conversionFactor 12.
Test data    : CARTON 12 → 24 (from the AC)
<!-- TC:TC-CAT-040:END -->

<!-- TC:TC-CAT-041:START traces=AC-CAT-084,REQ-CAT-082,API-CAT-003,API-CAT-002 -->
### TC-CAT-041 — The base unit of a saved Item cannot change
Derived from : AC-CAT-084  (REQ-CAT-082)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-007 → CAT-422-BASE-UOM-FIXED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · a saved Item with base unit PIECE
Host data    : none
Steps        : 1. PUT the Item with its current fields and version and baseUomCode KILOGRAM. 2. GET the Item.
Expected     : 1 → 422, code CAT-422-BASE-UOM-FIXED, messageAr «لا يمكن تغيير وحدة القياس الأساسية بعد حفظ الصنف. أضف وحدة أخرى للصنف بدلًا من ذلك.», messageEn "The base unit cannot be changed after the item is saved. Add another unit to the item instead.". 2 → baseUomCode PIECE.
Test data    : PIECE → KILOGRAM (from the AC)
<!-- TC:TC-CAT-041:END -->

<!-- TC:TC-CAT-042:START traces=AC-CAT-087,REQ-CAT-085,API-CAT-016,API-CAT-043 -->
### TC-CAT-042 — A deactivated barcode no longer resolves
Derived from : AC-CAT-087  (REQ-CAT-085)
Exercises    : API-CAT-016 DELETE /api/v1/cat/barcodes/{id} · API-CAT-043 GET /api/v1/cat/barcodes/lookup
Rule / code  : RULE-CAT-048 → CAT-200-DEACTIVATED · lookup miss RULE-CAT-047 → CAT-404-BARCODE-NOT-FOUND
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · the active barcode 6223000000014 on an active Variant · no active scale label layout
Host data    : none
Steps        : 1. DELETE the barcode. 2. GET the lookup with value 6223000000014.
Expected     : 1 → 200, data.isActiveFl false. 2 → no Variant is returned (404 CAT-404-BARCODE-NOT-FOUND).
Test data    : 6223000000014 (from the AC)
<!-- TC:TC-CAT-042:END -->

<!-- TC:TC-CAT-043:START traces=AC-CAT-089,REQ-CAT-087,API-CAT-014 -->
### TC-CAT-043 — An active barcode cannot be added to another Variant
Derived from : AC-CAT-089  (REQ-CAT-087)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes
Rule / code  : RULE-CAT-029 → CAT-409-BARCODE-DUPLICATE ({code} = ITM-000005)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the active GTIN 6223000000014 on a Variant of the Item with code ITM-000005 (series CAT_ITEM positioned at ITM-000004 before its creation) · another active Variant W (of another Item)
Host data    : none
Steps        : 1. POST to W the barcode {barcodeValue: 6223000000014, barcodeTypeCode: GTIN}.
Expected     : 409, code CAT-409-BARCODE-DUPLICATE, messageAr «هذا الباركود مستخدم في الصنف ITM-000005. افتح ذلك الصنف أو استخدم باركودًا آخر.», messageEn "This barcode is already used by item ITM-000005. Open that item or use another barcode."; nothing is saved.
Test data    : 6223000000014, ITM-000005 (from the AC)
<!-- TC:TC-CAT-043:END -->

<!-- TC:TC-CAT-044:START traces=AC-CAT-090,REQ-CAT-088,API-CAT-014 -->
### TC-CAT-044 — A GTIN with a wrong check digit is refused
Derived from : AC-CAT-090  (REQ-CAT-088)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes
Rule / code  : RULE-CAT-030 → CAT-422-GTIN-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an active Variant
Host data    : none
Steps        : 1. POST the barcode {barcodeValue: 6223000000015, barcodeTypeCode: GTIN}.
Expected     : 422, code CAT-422-GTIN-INVALID, messageAr «باركود GTIN يجب أن يتكون من 8 أو 12 أو 13 أو 14 رقمًا برقم تحقق صحيح.», messageEn "A GTIN barcode must have 8, 12, 13 or 14 digits with a valid check digit."; nothing is saved.
Test data    : 6223000000015 (from the AC)
<!-- TC:TC-CAT-044:END -->

<!-- TC:TC-CAT-045:START traces=AC-CAT-091,REQ-CAT-089,API-CAT-014 -->
### TC-CAT-045 — A barcode unit that is not a unit of the Item is refused
Derived from : AC-CAT-091  (REQ-CAT-089)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes
Rule / code  : RULE-CAT-031 → CAT-422-BARCODE-UNIT-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an Item with base unit PIECE and no Item unit BOX; its active Variant V
Host data    : none
Steps        : 1. POST to V a GTIN barcode with uomCode BOX.
Expected     : 422, code CAT-422-BARCODE-UNIT-INVALID, messageAr «وحدة الباركود يجب أن تكون الوحدة الأساسية للصنف أو إحدى وحداته النشطة.», messageEn "The barcode unit must be the item's base unit or one of its active units."; nothing is saved.
Test data    : uomCode BOX (from the AC; BOX is a seeded active CAT_UOM value); barcodeValue placeholder `<valid unused GTIN>`
<!-- TC:TC-CAT-045:END -->

<!-- TC:TC-CAT-046:START traces=AC-CAT-092,REQ-CAT-090,API-CAT-043 -->
### TC-CAT-046 — An unknown barcode returns no Variant with a next action
Derived from : AC-CAT-092  (REQ-CAT-090)
Exercises    : API-CAT-043 GET /api/v1/cat/barcodes/lookup
Rule / code  : RULE-CAT-047 → CAT-404-BARCODE-NOT-FOUND
Package      : SVC-API-SEARCH
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · no active barcode equals 6229999999995 · no scale label layout uses prefix 62 · the caller is a Seller/cashier (PERM_CAT_ITEMS_VIEW)
Host data    : none
Steps        : 1. GET the lookup with value 6229999999995.
Expected     : 404, code CAT-404-BARCODE-NOT-FOUND, messageAr «لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف.», messageEn "No item has this barcode. Search by name or add the barcode to the item."; no Variant is returned.
Test data    : 6229999999995 (from the AC)
<!-- TC:TC-CAT-046:END -->

<!-- TC:TC-CAT-047:START traces=AC-CAT-093,REQ-CAT-091,API-CAT-014 -->
### TC-CAT-047 — A GTIN beginning with an active scale prefix is refused
Derived from : AC-CAT-093  (REQ-CAT-091)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes
Rule / code  : RULE-CAT-032 → CAT-409-BARCODE-SCALE-PREFIX ({prefix} = 20)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an active scale label layout with prefix 20 (created through API-CAT-032) · an active Variant
Host data    : none
Steps        : 1. POST the barcode {barcodeValue: 2000000000008, barcodeTypeCode: GTIN}.
Expected     : 409, code CAT-409-BARCODE-SCALE-PREFIX, messageAr «الباركود يبدأ ببادئة ملصق ميزان نشط (20). استخدم باركودًا آخر أو عدّل إعداد الميزان.», messageEn "The barcode starts with the prefix of an active scale label layout (20). Use another barcode or change the scale settings."; nothing is saved.
Test data    : prefix 20, GTIN 2000000000008 (from the AC)
<!-- TC:TC-CAT-047:END -->

<!-- TC:TC-CAT-048:START traces=AC-CAT-096,REQ-CAT-094,API-CAT-034,API-CAT-043,API-CAT-007 -->
### TC-CAT-048 — A deactivated scale label layout stops decoding
Derived from : AC-CAT-096  (REQ-CAT-094)
Exercises    : API-CAT-034 DELETE /api/v1/cat/scale-label-layouts/{id} · API-CAT-043 GET /api/v1/cat/barcodes/lookup · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-047 → CAT-404-BARCODE-NOT-FOUND
Package      : DATA-DOM-LOOKUP
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · the active layout with prefix 20 (scaleCodeLength 5, WEIGHT, valueDecimals 3) · no other active layout · no active barcode equals 2001230012342 · a Variant V of an active Item with the active SCALE barcode 01230 — so, while the layout is active, 2001230012342 resolves to V (TC-CAT-110), and only the inactive layout explains the miss
Host data    : none
Steps        : 1. DELETE the layout. 2. As a Seller/cashier, GET the lookup with value 2001230012342. 3. GET V.
Expected     : 1 → 200, data.isActiveFl false. 2 → 404, code CAT-404-BARCODE-NOT-FOUND, messageAr «لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف.», messageEn "No item has this barcode. Search by name or add the barcode to the item."; no Variant. 3 → V still carries the SCALE barcode 01230 with isActiveFl true: the layout's deactivation leaves the barcode stored, only not decodable (REQ-CAT-094, ADR-CAT-064).
Test data    : prefix 20, 2001230012342 (prefix 20, scale code 01230, value 01234, check digit 2 — from the AC, the value of AC-CAT-099; MODIFIED at the sixth gate-analysis revise, G8)
<!-- TC:TC-CAT-048:END -->

<!-- TC:TC-CAT-049:START traces=AC-CAT-100,REQ-CAT-098,API-CAT-032,API-CAT-045 -->
### TC-CAT-049 — A scale label layout with prefix 62 is refused
Derived from : AC-CAT-100  (REQ-CAT-098)
Exercises    : API-CAT-032 POST /api/v1/cat/scale-label-layouts · API-CAT-045 GET /api/v1/cat/settings
Rule / code  : RULE-CAT-034 → CAT-422-SCALE-LAYOUT-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the catalogue settings hold no layout
Host data    : none
Steps        : 1. POST a layout with key 62 (other fields defaulted). 2. GET the settings.
Expected     : 1 → 422, code CAT-422-SCALE-LAYOUT-INVALID, messageAr «البادئة رقمان (02 أو من 20 إلى 29) غير مستخدمة في إعداد نشط آخر، وطول رمز الميزان من 4 إلى 6، والأرقام العشرية من 0 إلى 3.», messageEn "The prefix is two digits (02 or 20 to 29) not used by another active layout, the scale code length is 4 to 6, and the decimals are 0 to 3.". 2 → 0 layouts: nothing saved.
Test data    : prefix 62 (from the AC)
<!-- TC:TC-CAT-049:END -->

<!-- TC:TC-CAT-050:START traces=AC-CAT-101,REQ-CAT-099,API-CAT-014 -->
### TC-CAT-050 — A scale code shorter than the layout's length is refused
Derived from : AC-CAT-101  (REQ-CAT-099)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes
Rule / code  : RULE-CAT-033 → CAT-422-SCALE-CODE-LENGTH ({length} = 5)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: S0 · the only active layout has scaleCodeLength 5 · an active Variant
Host data    : none
Steps        : 1. POST the barcode {barcodeValue: 123, barcodeTypeCode: SCALE}.
Expected     : 422, code CAT-422-SCALE-CODE-LENGTH, messageAr «رمز الميزان يجب أن يتكون من 5 أرقام، ويلزم وجود إعداد ملصق ميزان نشط.», messageEn "The scale code must have 5 digits, and an active scale label layout is required."; nothing is saved.
Test data    : scaleCodeLength 5, SCALE 123 (from the AC)
<!-- TC:TC-CAT-050:END -->

<!-- TC:TC-CAT-051:START traces=AC-CAT-102,REQ-CAT-100,API-CAT-043 -->
### TC-CAT-051 — A scale label with an undefined scale code is not read
Derived from : AC-CAT-102  (REQ-CAT-100)
Exercises    : API-CAT-043 GET /api/v1/cat/barcodes/lookup
Rule / code  : RULE-CAT-035 → CAT-422-SCALE-LABEL-UNREADABLE
Package      : SVC-API-SEARCH
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the active layout with prefix 20 (scaleCodeLength 5) · no active SCALE barcode 09999 · a Variant with the active SCALE barcode 01230 · the caller is a Seller/cashier
Host data    : none
Steps        : 1. GET the lookup with the 13-digit value 20 09999 01234 x, where x is the valid EAN-13 check digit. 2. GET the lookup with the value 2001230012343 (scale code 01230 defined, check digit 3 where 2 is valid — TC-CAT-110).
Expected     : 1 → 422, code CAT-422-SCALE-LABEL-UNREADABLE, messageAr «تعذّر قراءة ملصق الميزان: رقم التحقق غير صحيح أو رمز الميزان غير معرَّف. اطلب الصنف بالاسم أو أضف رمز الميزان إليه.», messageEn "The scale label could not be read: the check digit is wrong or the scale code is not defined. Find the item by name or add the scale code to it."; no Variant. 2 → 422, the same code, messageAr and messageEn; no Variant.
Test data    : 20 09999 01234 x (from the AC; x computed by the executor as the EAN-13 check digit); 2001230012343 (wrong check digit — this case's own counter-case)
<!-- TC:TC-CAT-051:END -->

<!-- TC:TC-CAT-052:START traces=AC-CAT-105,REQ-CAT-103,API-CAT-050 -->
### TC-CAT-052 — A label request of 1,000 copies of one Variant is refused
Derived from : AC-CAT-105  (REQ-CAT-103)
Exercises    : API-CAT-050 POST /api/v1/cat/barcode-labels
Rule / code  : RULE-CAT-036 → CAT-422-LABEL-COPIES-OUT-OF-RANGE
Package      : SVC-API-INT
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: S0, with the caller a Warehouse staff user (PERM_CAT_BARCODE_LABELS_CREATE) · an active Variant with an active barcode
Host data    : none
Steps        : 1. POST the label request {lines: [{variantPk, copies: 1000}]}.
Expected     : 422, code CAT-422-LABEL-COPIES-OUT-OF-RANGE, messageAr «عدد النسخ لكل متغيّر من 1 إلى 999، وإجمالي الملصقات لا يتجاوز 5000.», messageEn "Copies per variant are 1 to 999, and the total cannot exceed 5,000 labels."; no document is produced (no PDF body).
Test data    : 1,000 copies (limit 999 + 1, from the AC)
<!-- TC:TC-CAT-052:END -->

<!-- TC:TC-CAT-053:START traces=AC-CAT-106,REQ-CAT-104,API-CAT-050 -->
### TC-CAT-053 — A label request with 2 Variants without a barcode is refused
Derived from : AC-CAT-106  (REQ-CAT-104)
Exercises    : API-CAT-050 POST /api/v1/cat/barcode-labels
Rule / code  : RULE-CAT-037 → CAT-422-LABEL-VARIANT-NO-BARCODE ({count} = 2)
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0, with the caller a Warehouse staff user · 3 active Variants, 2 of which have no active barcode
Host data    : none
Steps        : 1. POST the label request with the 3 Variants, 1 copy each.
Expected     : 422, code CAT-422-LABEL-VARIANT-NO-BARCODE, messageAr «لا يوجد باركود نشط لـ 2 متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.», messageEn "2 variants have no active barcode. Generate an internal barcode for them, then print."; no document is produced; the 2 Variants are listed to the caller (envelope field not bound by api-spec-cat.yaml — ADR-CAT-052 §5).
Test data    : 3 Variants, 2 without barcode (from the AC)
<!-- TC:TC-CAT-053:END -->

<!-- TC:TC-CAT-054:START traces=AC-CAT-108,REQ-CAT-106,API-CAT-019,API-CAT-002 -->
### TC-CAT-054 — Choosing another main image replaces the former one
Derived from : AC-CAT-108  (REQ-CAT-106)
Exercises    : API-CAT-019 PUT /api/v1/cat/media/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-039 → CAT-200-PRIMARY-IMAGE-REPLACED (success notice)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an Item with active images A (primary) and B (not primary)
Host data    : none
Steps        : 1. PUT image B with its current fields and version and isPrimaryFl true. 2. GET the Item.
Expected     : 1 → 200, code CAT-200-PRIMARY-IMAGE-REPLACED, messageAr «للصنف صورة رئيسية واحدة فقط؛ تم تعيين الصورة المختارة رئيسية بدل السابقة.», messageEn "An item has exactly one main image; the selected image replaced the previous main image.". 2 → B isPrimaryFl true, A isPrimaryFl false, exactly 1 primary image.
Test data    : images A, B (from the AC)
<!-- TC:TC-CAT-054:END -->

<!-- TC:TC-CAT-055:START traces=AC-CAT-110,REQ-CAT-108,API-CAT-018 -->
### TC-CAT-055 — The first image of an Item becomes the main image
Derived from : AC-CAT-110  (REQ-CAT-108)
Exercises    : API-CAT-018 POST /api/v1/cat/items/{id}/media
Rule / code  : RULE-CAT-039 (the first image is marked primary) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an Item with 0 active images
Host data    : none
Steps        : 1. Upload one PNG ≤ 5 MB through the given FILE endpoint (public image storage), then POST `{fileDocumentRef}` to the Item's media. 2. GET the Item.
Expected     : 1 → 201; data.isPrimaryFl true. 2 → exactly 1 active image of the Item has isPrimaryFl true.
Test data    : one PNG image (placeholder file ≤ 5 MB)
<!-- TC:TC-CAT-055:END -->

<!-- TC:TC-CAT-056:START traces=AC-CAT-112,REQ-CAT-110,API-CAT-018,API-CAT-002 -->
### TC-CAT-056 — A 7 MB JPG is refused by the image category at upload and never reaches CAT
Derived from : AC-CAT-112  (REQ-CAT-110)
Exercises    : the given FILE upload endpoint with category `CAT_ITEM_IMAGE` (registered by CAT — bootstrap, ADR-CAT-076) · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-038, file part — held by the FILE category `CAT_ITEM_IMAGE` (limit 5 MB), refused in the FILE module's own envelope; CAT raises no code (ADR-CAT-076)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: S0 · an Item with 20 active images
Host data    : none
Steps        : 1. Upload a 7 MB JPG through the given FILE endpoint to the category `CAT_ITEM_IMAGE`. 2. Upload a 1 MB GIF to the same category. 3. GET the Item.
Expected     : 1 → the FILE endpoint refuses the upload, in its own envelope, and returns no `fileDocumentRef`, so no request to API-CAT-018 can be made. 2 → the same: a GIF is not an allowed type of the category. 3 → the Item still has 20 active media records: no media record is created. (The 21st-image half of AC-CAT-112 is asserted by TC-CAT-218.)
Test data    : a 7 MB JPG (from the AC); a 1 MB GIF (this case's own)
<!-- TC:TC-CAT-056:END -->

<!-- TC:TC-CAT-057:START traces=AC-CAT-113,REQ-CAT-111,API-CAT-020,API-CAT-002 -->
### TC-CAT-057 — Removing the main image promotes the lowest sortOrder
Derived from : AC-CAT-113  (REQ-CAT-111)
Exercises    : API-CAT-020 DELETE /api/v1/cat/media/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-039 (promotion of the lowest sortOrder) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an Item with the primary image at sortOrder 1 and other active images at sortOrder 2 and 3
Host data    : none
Steps        : 1. DELETE the primary image. 2. GET the Item.
Expected     : 1 → 200. 2 → the image with sortOrder 2 has isPrimaryFl true, and exactly 1 active image is primary.
Test data    : sortOrders 1, 2, 3 (from the AC)
<!-- TC:TC-CAT-057:END -->

<!-- TC:TC-CAT-058:START traces=AC-CAT-114,REQ-CAT-112,API-CAT-019 -->
### TC-CAT-058 — An image cannot be bound to another Item's Variant
Derived from : AC-CAT-114  (REQ-CAT-112)
Exercises    : API-CAT-019 PUT /api/v1/cat/media/{id}
Rule / code  : RULE-CAT-040 → CAT-422-MEDIA-VARIANT-MISMATCH
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an image of Item A · an active Variant VB of Item B
Host data    : none
Steps        : 1. PUT the image of A with its current fields and version and variantFk = VB.
Expected     : 422, code CAT-422-MEDIA-VARIANT-MISMATCH, messageAr «المتغيّر المختار لا ينتمي إلى هذا الصنف.», messageEn "The selected variant does not belong to this item."; nothing is saved (a GET of A shows the image's variantFk unchanged).
Test data    : Items A, B (from the AC)
<!-- TC:TC-CAT-058:END -->

<!-- TC:TC-CAT-059:START traces=AC-CAT-115,REQ-CAT-113,API-CAT-005 -->
### TC-CAT-059 — Publishing an active Item sets PUBLISHED and publishes one change event
Derived from : AC-CAT-115  (REQ-CAT-113)
Exercises    : API-CAT-005 PATCH /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-041 (publish allowed: active, ≥ 1 active Variant) · RULE-CAT-049 (event after commit)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an active UNPUBLISHED Item with exactly 1 active Variant · the outbox is observed
Host data    : none
Steps        : 1. PATCH the Item with body {publishStateCode: PUBLISHED}. 2. Count the cat.CatalogueChanged events delivered for its itemPk after step 1.
Expected     : 1 → 200; data.publishStateCode PUBLISHED. 2 → exactly 1 event with changeKind UPDATED.
Test data    : PUBLISHED, UPDATED (seeded CAT_PUBLISH_STATE / CAT_CHANGE_KIND values)
<!-- TC:TC-CAT-059:END -->

<!-- TC:TC-CAT-060:START traces=AC-CAT-116,REQ-CAT-114,API-CAT-005,API-CAT-047 -->
### TC-CAT-060 — Unpublishing keeps the Item on sale at the point of sale
Derived from : AC-CAT-116  (REQ-CAT-114)
Exercises    : API-CAT-005 PATCH /api/v1/cat/items/{id} · API-CAT-047 GET /api/v1/cat/catalogue-search
Rule / code  : RULE-CAT-041 (publish state follows the Item) · —
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an active PUBLISHED Item
Host data    : none
Steps        : 1. PATCH the Item with body {publishStateCode: UNPUBLISHED}. 2. As a Seller/cashier, GET the catalogue search with term = the Item's code.
Expected     : 1 → 200; data.publishStateCode UNPUBLISHED, data.isActiveFl true. 2 → 200; the Item is among the hits.
Test data    : the Item's code (system-assigned)
<!-- TC:TC-CAT-060:END -->

<!-- TC:TC-CAT-061:START traces=AC-CAT-118,REQ-CAT-116,API-CAT-005,API-CAT-002 -->
### TC-CAT-061 — An inactive Item cannot be published
Derived from : AC-CAT-118  (REQ-CAT-116)
Exercises    : API-CAT-005 PATCH /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-041 → CAT-422-PUBLISH-BLOCKED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an inactive Item (deactivated through API-CAT-004)
Host data    : none
Steps        : 1. PATCH the Item with body {publishStateCode: PUBLISHED}. 2. GET the Item.
Expected     : 1 → 422, code CAT-422-PUBLISH-BLOCKED, messageAr «لا يمكن نشر صنف غير نشط أو بلا متغيّر نشط. فعّل الصنف أو أحد متغيّراته أولًا.», messageEn "An inactive item, or one with no active variant, cannot be published. Activate the item or one of its variants first.". 2 → publishStateCode UNPUBLISHED.
Test data    : —
<!-- TC:TC-CAT-061:END -->

<!-- TC:TC-CAT-062:START traces=AC-CAT-125,REQ-CAT-123,API-CAT-003,API-CAT-002 -->
### TC-CAT-062 — An EGS e-invoice code not starting with EG- is refused
Derived from : AC-CAT-125  (REQ-CAT-123)
Exercises    : API-CAT-003 PUT /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-042 → CAT-422-EINVOICE-CODE-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an Item with no e-invoice code
Host data    : none
Steps        : 1. PUT the Item with its current fields and version, einvoiceCodeTypeCode EGS and einvoiceCode «123». 2. GET the Item.
Expected     : 1 → 422, code CAT-422-EINVOICE-CODE-INVALID, messageAr «أدخل نوع رمز الفاتورة الإلكترونية والرمز معًا: GS1 من 8 إلى 14 رقمًا، وEGS يبدأ بـ EG- ولا يتجاوز 100 حرف.», messageEn "Enter the e-invoice code type and code together: GS1 is 8 to 14 digits, EGS starts with EG- and has at most 100 characters.". 2 → einvoiceCodeTypeCode and einvoiceCode still empty: nothing saved.
Test data    : EGS «123» (from the AC)
<!-- TC:TC-CAT-062:END -->

<!-- TC:TC-CAT-063:START traces=AC-CAT-126,REQ-CAT-124,API-CAT-035,API-CAT-045 -->
### TC-CAT-063 — A second active e-invoice unit for PIECE is refused
Derived from : AC-CAT-126  (REQ-CAT-124)
Exercises    : API-CAT-035 POST /api/v1/cat/unit-einvoice-codes · API-CAT-045 GET /api/v1/cat/settings
Rule / code  : RULE-CAT-043 → CAT-409-UNIT-EINVOICE-DUPLICATE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the active Unit e-invoice code PIECE → EA (seeded, D-12)
Host data    : none
Steps        : 1. POST {key: PIECE, einvoiceUnitCode: BX}. 2. GET the settings.
Expected     : 1 → 409, code CAT-409-UNIT-EINVOICE-DUPLICATE, messageAr «لهذه الوحدة رمز فاتورة إلكترونية نشط بالفعل. عدّله بدلًا من إضافة رمز جديد.», messageEn "This unit already has an active e-invoice unit. Change it instead of adding a new one.". 2 → PIECE still has exactly one active code (EA): nothing created.
Test data    : PIECE → EA (from the AC). The AC's second e-invoice unit is not a seeded value; the case stays on seeded data and uses the seeded active CAT_EINVOICE_UNIT value BX in its place (ADR-CAT-053 §2)
<!-- TC:TC-CAT-063:END -->

<!-- TC:TC-CAT-064:START traces=AC-CAT-128,REQ-CAT-126,API-CAT-047 -->
### TC-CAT-064 — A one-character catalogue search returns no results
Derived from : AC-CAT-128  (REQ-CAT-126)
Exercises    : API-CAT-047 GET /api/v1/cat/catalogue-search
Rule / code  : RULE-CAT-046 → CAT-400-SEARCH-TERM-TOO-SHORT
Package      : SVC-API-SEARCH
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: S0, with the caller a Seller/cashier (PERM_CAT_ITEMS_VIEW) · at least one active Item whose nameAr contains «ش»
Host data    : none
Steps        : 1. GET the catalogue search with term «ش».
Expected     : 400, code CAT-400-SEARCH-TERM-TOO-SHORT, messageAr «اكتب حرفين على الأقل للبحث.», messageEn "Type at least 2 characters to search."; 0 results.
Test data    : term «ش» (1 character, limit 2 − 1, from the AC)
<!-- TC:TC-CAT-064:END -->

<!-- TC:TC-CAT-183:START traces=AC-CAT-130,REQ-CAT-128,API-CAT-013,API-CAT-011 -->
### TC-CAT-183 — An Item unit used by 2 active barcodes stays active
Derived from : AC-CAT-130  (REQ-CAT-128)
Exercises    : API-CAT-013 DELETE /api/v1/cat/item-units/{id} · API-CAT-011 GET /api/v1/cat/item-units/{id}
Rule / code  : RULE-CAT-051 → CAT-409-ITEM-UNIT-HAS-BARCODES ({count} = 2)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an Item with base unit PIECE and the active Item unit CARTON (added through API-CAT-010) · 2 active GTIN barcodes bound to unit CARTON on its default Variant (added through API-CAT-014)
Host data    : none
Steps        : 1. DELETE the Item unit CARTON. 2. GET it by itemUnitPk.
Expected     : 1 → 409, code CAT-409-ITEM-UNIT-HAS-BARCODES, messageAr «هذه الوحدة مستخدمة في 2 باركود نشط. أوقف هذه الباركودات أولًا.», messageEn "This unit is used by 2 active barcodes. Deactivate those barcodes first.". 2 → isActiveFl true.
Test data    : CARTON, 2 barcodes (from the AC); the GTIN values are placeholders `<valid unused GTIN-1..2>`
<!-- TC:TC-CAT-183:END -->

<!-- TC:TC-CAT-184:START traces=AC-CAT-131,REQ-CAT-129,API-CAT-006,API-CAT-002 -->
### TC-CAT-184 — A generation that omits a used attribute, or names no value, is refused whole
Derived from : AC-CAT-131  (REQ-CAT-129)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-052 → CAT-422-VARIANT-ATTRIBUTE-MISSING (row 1) · PLATFORM-STD CAT-400-REQUEST-INVALID (row 2 — `minItems: 1` on `selections` and on `variantAttributeValueFks`; row 3 — `maxItems: 3` on `selections`, checked before RULE-CAT-014 — ADR-CAT-044)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL · parameterised (one row per case)
Preconditions: S0 · a STANDARD Item I whose 2 active Variants carry SIZE (values S, M) and COLOUR (value «أحمر»), generated through API-CAT-006 · SIZE also has the active value L · the active attributes MATERIAL (active value COTTON) and STYLE (active value SLIM) · a STANDARD Item J with only its default Variant and no Variant attribute in use
Host data    : none
Steps        : 1. Send each row below, in order. 2. GET I and GET J.

| Row | Item | Request | Expected |
|---|---|---|---|
| 1 | I | POST a generation with selections SIZE [L] only | 422, code CAT-422-VARIANT-ATTRIBUTE-MISSING, messageAr «اختر قيمة واحدة على الأقل لكل خاصية يستخدمها الصنف بالفعل.», messageEn "Select at least one value for every attribute the item already uses." |
| 2 | J | POST a generation with selections [] (empty), then POST one with selections [{variantAttributeFk: SIZE, variantAttributeValueFks: []}] | each 400, code CAT-400-REQUEST-INVALID, messageAr «الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة.», messageEn "The request is not valid. Check the highlighted fields and try again." — added at the fifth gate-analysis revise (G3) |
| 3 | J | POST a generation with 4 selections: SIZE [S], COLOUR [«أحمر»], MATERIAL [COTTON], STYLE [SLIM] | 400, code CAT-400-REQUEST-INVALID, the same messageAr and messageEn (the document check of `maxItems: 3` refuses it before RULE-CAT-014 runs; nothing is built) — added at the seventh gate-analysis revise (G2) |

Expected     : 1 → every row answers as its table cell says. 2 → I still has 2 active Variants and J still has exactly 1 (its default Variant): 0 Variants created and 0 attribute assignments added.
Test data    : SIZE S, M, L; COLOUR «أحمر» (from the AC); MATERIAL COTTON, STYLE SLIM (this case's own); I, J (placeholders)
<!-- TC:TC-CAT-184:END -->

<!-- TC:TC-CAT-185:START traces=AC-CAT-132,AC-CAT-154,REQ-CAT-130,REQ-CAT-145,REQ-CAT-141,API-CAT-005,API-CAT-002,API-CAT-006 -->
### TC-CAT-185 — An Item with no active Variant is refused reactivation, gets a Variant while inactive, then reactivates
Derived from : AC-CAT-132, AC-CAT-154  (REQ-CAT-130, REQ-CAT-145; the default it asserts is REQ-CAT-141)
Exercises    : API-CAT-005 PATCH /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-006 POST /api/v1/cat/items/{id}/variants
Rule / code  : RULE-CAT-050 → CAT-422-ITEM-NO-ACTIVE-VARIANT · repair allowed on an inactive Item (RULE-CAT-053 does not apply to API-CAT-006 — ADR-CAT-068)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: S0 · an Item with exactly 1 active Variant V and no Variant attribute in use. The Item is deactivated through API-CAT-004, then V through API-CAT-009, which is allowed because the Item is inactive (RULE-CAT-015 guards only an active Item). The Item now has 0 active Variants · the active attribute SIZE with the active value M
Host data    : none
Steps        : 1. PATCH the Item with body {isActiveFl: true}. 2. GET the Item. 3. POST to the Item the generation {selections: [{variantAttributeFk: SIZE, variantAttributeValueFks: [M]}]}. 4. PATCH the Item with body {isActiveFl: true}. 5. GET the Item.
Expected     : 1 → 422, code CAT-422-ITEM-NO-ACTIVE-VARIANT, messageAr «لا يمكن إعادة تفعيل صنف بلا متغيّر نشط. أنشئ متغيّرًا له أو أنشئ صنفًا جديدًا.», messageEn "An item with no active variant cannot be reactivated. Generate a variant for it, or create a new item.". 2 → isActiveFl false. 3 → 201; one new active Variant carrying M, with isDefaultFl true (the Item had 0 active Variants — ADR-CAT-072); no CAT-422-PARENT-INACTIVE. 4 → 200; isActiveFl true, publishStateCode UNPUBLISHED. 5 → isActiveFl true and exactly 1 active Variant (the M Variant), which is the Item's one default Variant.
Test data    : nameAr placeholder `<valid Arabic name>`; SIZE, M (from the AC)
<!-- TC:TC-CAT-185:END -->

<!-- TC:TC-CAT-186:START traces=AC-CAT-133,AC-CAT-155,REQ-CAT-142,REQ-CAT-146,REQ-CAT-135,API-CAT-005,API-CAT-002,API-CAT-003 -->
### TC-CAT-186 — An Item whose Category went inactive is refused reactivation, is moved while inactive, then reactivates
Derived from : AC-CAT-133, AC-CAT-155  (REQ-CAT-142, REQ-CAT-146; the refused nameEn change is REQ-CAT-135)
Exercises    : API-CAT-005 PATCH /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-003 PUT /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-026 → CAT-422-CATEGORY-INACTIVE · RULE-CAT-053 → CAT-422-PARENT-INACTIVE (a change other than categoryFk) · the categoryFk-only change is allowed (ADR-CAT-068)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: S0 · a Category with code OLD holding exactly 1 Item, active, with nameEn "Glass cup". The Item is deactivated through API-CAT-004, then OLD through API-CAT-024, which is allowed because OLD holds no active Item (RULE-CAT-025) · an active Category NEW
Host data    : none
Steps        : 1. PATCH the Item with body {isActiveFl: true}. 2. GET the Item and record its fields and version. 3. PUT the Item with its recorded fields and version, and nameEn "Glass cup 2" and categoryFk NEW. 4. PUT the Item with its recorded fields and version, and categoryFk NEW only. 5. PATCH the Item with body {isActiveFl: true}. 6. GET the Item.
Expected     : 1 → 422, code CAT-422-CATEGORY-INACTIVE, messageAr «الفئة المختارة غير نشطة. اختر فئة نشطة.», messageEn "The selected category is inactive. Choose an active category.". 2 → isActiveFl false, categoryFk still OLD. 3 → 422, code CAT-422-PARENT-INACTIVE, messageAr «هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا.», messageEn "This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant."; nothing is saved. 4 → 200; categoryFk NEW, nameEn "Glass cup", isActiveFl false. 5 → 200; isActiveFl true. 6 → categoryFk NEW, nameEn "Glass cup", isActiveFl true.
Test data    : Categories OLD and NEW, "Glass cup", "Glass cup 2" (from the AC)
<!-- TC:TC-CAT-186:END -->

<!-- TC:TC-CAT-187:START traces=AC-CAT-134,AC-CAT-156,REQ-CAT-143,REQ-CAT-147,API-CAT-005,API-CAT-002,API-CAT-017 -->
### TC-CAT-187 — A Bundle whose component went inactive is refused reactivation, gets new components while inactive, then reactivates
Derived from : AC-CAT-134, AC-CAT-156  (REQ-CAT-143, REQ-CAT-147)
Exercises    : API-CAT-005 PATCH /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-017 PUT /api/v1/cat/items/{id}/bundle-components
Rule / code  : RULE-CAT-019 → CAT-422-BUNDLE-COMPONENT-INVALID · repair allowed on an inactive Bundle (RULE-CAT-053 does not apply to API-CAT-017 — ADR-CAT-068)
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: S0 · a Bundle B with 2 component lines P × 1 and C × 1 on Variants of active STANDARD Items, where P's Item has a second active Variant. B is deactivated through API-CAT-004, then P through API-CAT-009, which is allowed because B is inactive (RULE-CAT-021) · an active Variant Q of an active STANDARD Item
Host data    : none
Steps        : 1. PATCH B with body {isActiveFl: true}. 2. GET B and record its version. 3. PUT B's line set {lines: [{componentVariantFk: C, quantity: 1}, {componentVariantFk: Q, quantity: 1}], version}. 4. PATCH B with body {isActiveFl: true}. 5. GET B.
Expected     : 1 → 422, code CAT-422-BUNDLE-COMPONENT-INVALID, messageAr «المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.», messageEn "A component must be an active variant of an item that is not a bundle, listed once per bundle.". 2 → isActiveFl false. 3 → 200; the active lines are C × 1 and Q × 1; no CAT-422-PARENT-INACTIVE. 4 → 200; isActiveFl true. 5 → isActiveFl true and exactly 2 active lines, C × 1 and Q × 1.
Test data    : Bundle B, Variants P, C and Q (from the AC; placeholders)
<!-- TC:TC-CAT-187:END -->

<!-- TC:TC-CAT-188:START traces=AC-CAT-135,REQ-CAT-086,REQ-CAT-005,API-CAT-004,API-CAT-043,API-CAT-002,API-CAT-014 -->
### TC-CAT-188 — A barcode of a deactivated Item no longer resolves, yet still holds its value
Derived from : AC-CAT-135  (REQ-CAT-086; the barcode kept active by REQ-CAT-005)
Exercises    : API-CAT-004 DELETE /api/v1/cat/items/{id} · API-CAT-043 GET /api/v1/cat/barcodes/lookup · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes
Rule / code  : RULE-CAT-047 → CAT-404-BARCODE-NOT-FOUND · RULE-CAT-029 → CAT-409-BARCODE-DUPLICATE
Package      : SVC-API-SEARCH
Scenario     : STATE · data class EDGE · language ALL
Preconditions: S0 · an active Item with code ITM-000005 (series CAT_ITEM positioned at ITM-000004 before its creation) whose active Variant carries the active GTIN 6223000000021 · an active Variant W of another active Item · no active scale label layout
Host data    : none
Steps        : 1. As the Owner, DELETE the Item. 2. As a Seller/cashier, GET the lookup with value 6223000000021. 3. As the Owner, GET the Item. 4. As the Owner, POST to W the barcode {barcodeValue: 6223000000021, barcodeTypeCode: GTIN}.
Expected     : 1 → 200. 2 → 404, code CAT-404-BARCODE-NOT-FOUND, messageAr «لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف.», messageEn "No item has this barcode. Search by name or add the barcode to the item."; no Variant is returned. 3 → isActiveFl false, and the barcode 6223000000021 is still returned with isActiveFl true: deactivating an Item leaves its barcodes active (REQ-CAT-005). 4 → 409, code CAT-409-BARCODE-DUPLICATE, messageAr «هذا الباركود مستخدم في الصنف ITM-000005. افتح ذلك الصنف أو استخدم باركودًا آخر.», messageEn "This barcode is already used by item ITM-000005. Open that item or use another barcode."; nothing is saved. (The owner releases the GTIN with API-CAT-016, which stays allowed on the inactive Item — TC-CAT-212 step 2.)
Test data    : 6223000000021 (from the AC; check digit 1); ITM-000005 (this case's own Item code)
<!-- TC:TC-CAT-188:END -->

<!-- TC:TC-CAT-189:START traces=AC-CAT-136,REQ-CAT-101,API-CAT-042 -->
### TC-CAT-189 — The Variant search for labels skips Variants of inactive Items
Derived from : AC-CAT-136  (REQ-CAT-101)
Exercises    : API-CAT-042 GET /api/v1/cat/variants
Rule / code  : — (active-Item filter, ADR-CAT-059)
Package      : SVC-API-SEARCH
Scenario     : STATE · data class EDGE · language ALL
Preconditions: S0, with the caller a Warehouse staff user · 2 active Variants of the active Item «حذاء رياضي» · 1 active Variant of the Item «حذاء جلد», which the Owner deactivated through API-CAT-004
Host data    : none
Steps        : 1. GET /api/v1/cat/variants?text=حذاء&page=0&size=20.
Expected     : 200; totalElements 2; both rows carry itemNameAr «حذاء رياضي»; 0 rows of «حذاء جلد».
Test data    : «حذاء رياضي», «حذاء جلد» (from the AC)
<!-- TC:TC-CAT-189:END -->

<!-- TC:TC-CAT-197:START traces=AC-CAT-143,REQ-CAT-016,API-CAT-002,API-CAT-004 -->
### TC-CAT-197 — An existing Bundle is read and deactivated while `bundles` is off
Derived from : AC-CAT-143  (REQ-CAT-016)
Exercises    : API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-004 DELETE /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-008 (not raised: reads, deactivations and reactivations stay allowed while the flag is off) · RULE-CAT-048 → CAT-200-DEACTIVATED (success notice)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0, except that the `bundles` flag is off · an active Bundle B with 2 component lines, saved through API-CAT-001 while `bundles` was on; no Variant of B is a component of another active Bundle
Host data    : none
Steps        : 1. GET B. 2. DELETE B. 3. GET B.
Expected     : 1 → 200; itemKindCode BUNDLE and bundleComponents holds the 2 lines. 2 → 200, code CAT-200-DEACTIVATED with messageAr «تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة.» and messageEn "Deactivated. The record stays available to earlier documents and no longer appears in new selections."; data.isActiveFl false. 3 → 200, isActiveFl false. No response carries code CAT-422-CAPABILITY-OFF.
Test data    : B with 2 lines (from the AC); component Variants placeholders
<!-- TC:TC-CAT-197:END -->

<!-- TC:TC-CAT-198:START traces=AC-CAT-144,REQ-CAT-135,API-CAT-010,API-CAT-002 -->
### TC-CAT-198 — An inactive Item refuses a new Item unit
Derived from : AC-CAT-144  (REQ-CAT-135)
Exercises    : API-CAT-010 POST /api/v1/cat/items/{id}/units · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-053 → CAT-422-PARENT-INACTIVE
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an Item with base unit PIECE and no Item unit CARTON, deactivated through API-CAT-004
Host data    : none
Steps        : 1. POST {uomCode: CARTON, conversionFactor: 12} to the Item. 2. GET the Item.
Expected     : 1 → 422, code CAT-422-PARENT-INACTIVE, messageAr «هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا.», messageEn "This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant.". 2 → the Item has no Item unit CARTON and isActiveFl false.
Test data    : PIECE, CARTON, 12 (from the AC)
<!-- TC:TC-CAT-198:END -->

<!-- TC:TC-CAT-199:START traces=AC-CAT-145,REQ-CAT-050,API-CAT-009,API-CAT-007,API-CAT-014,API-CAT-043 -->
### TC-CAT-199 — A deactivated Variant's barcode is deactivated and its GTIN can be used again
Derived from : AC-CAT-145  (REQ-CAT-050)
Exercises    : API-CAT-009 DELETE /api/v1/cat/variants/{id} · API-CAT-007 GET /api/v1/cat/variants/{id} · API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes · API-CAT-043 GET /api/v1/cat/barcodes/lookup
Rule / code  : RULE-CAT-048 (the Variant's barcodes follow it — ADR-CAT-063) · RULE-CAT-029 (satisfied once the value is released)
Package      : SVC-API-CRUD
Scenario     : STATE · data class VALID · language ALL
Preconditions: S0 · an Item with 2 active Variants V1 and V2, V1 carrying the active GTIN 6223000000014; V1 is not a Bundle component · an active Variant W of another Item
Host data    : none
Steps        : 1. DELETE V1. 2. GET V1. 3. POST to W the barcode {barcodeValue: 6223000000014, barcodeTypeCode: GTIN}. 4. As a Seller/cashier, GET the lookup with value 6223000000014.
Expected     : 1 → 200. 2 → V1 is returned with isActiveFl false and with no active barcode: `barcodes` holds no 6223000000014 (API-CAT-007 returns active barcodes only — QR-CAT-047). The release of the value is proven by steps 3 and 4. 3 → 201; an active GTIN barcode 6223000000014 of W. 4 → 200; the Variant W.
Test data    : 6223000000014 (from the AC; step 2 MODIFIED at the sixth gate-analysis revise, G1)
<!-- TC:TC-CAT-199:END -->

<!-- TC:TC-CAT-200:START traces=AC-CAT-146,REQ-CAT-053,API-CAT-009,API-CAT-002 -->
### TC-CAT-200 — Two parallel deactivations of the last two Variants: exactly one succeeds
Derived from : AC-CAT-146  (REQ-CAT-053)
Exercises    : API-CAT-009 DELETE /api/v1/cat/variants/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-015 → CAT-409-LAST-ACTIVE-VARIANT · guard: the Item row lock QR-CAT-010 FOR UPDATE, owned by API-CAT-009 (unit SVC-API-CRUD)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · an active Item with exactly 2 active Variants V1 and V2, neither a Bundle component
Host data    : none
Steps        : 1. Submit DELETE V1 and DELETE V2 simultaneously (two Owner sessions released together). 2. GET the Item.
Expected     : 1 → exactly one request answers 200; the other answers 409, code CAT-409-LAST-ACTIVE-VARIANT, messageAr «لا يمكن إيقاف آخر متغيّر نشط لصنف نشط. أوقف الصنف نفسه بدلًا من ذلك.», messageEn "The last active variant of an active item cannot be deactivated. Deactivate the item instead.". 2 → the Item has exactly 1 active Variant, and it carries isDefaultFl true, whichever of V1 and V2 survived (the default moves under the same Item lock — ADR-CAT-072).
Test data    : 2 Variants (from the AC); V1 is the default Variant
<!-- TC:TC-CAT-200:END -->

<!-- TC:TC-CAT-201:START traces=AC-CAT-147,REQ-CAT-052,API-CAT-006,API-CAT-002 -->
### TC-CAT-201 — Two parallel generations past 300 Variants: exactly one succeeds
Derived from : AC-CAT-147  (REQ-CAT-052)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-014 → CAT-422-VARIANT-LIMIT-EXCEEDED · guard: the Item row lock QR-CAT-010 FOR UPDATE, owned by API-CAT-006 (unit SVC-API-CRUD)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: S0 · a STANDARD Item with 280 active Variants (generated through API-CAT-006) · two selections S1 and S2, each yielding 15 combinations the Item does not carry, within at most 3 attributes, and sharing no combination
Host data    : none
Steps        : 1. Submit the generation S1 and the generation S2 simultaneously (two Owner sessions released together). 2. GET the Item.
Expected     : 1 → exactly one request answers 201; the other answers 422, code CAT-422-VARIANT-LIMIT-EXCEEDED, messageAr «الحد الأقصى 3 خصائص و300 متغيّر نشط للصنف الواحد.», messageEn "An item can have at most 3 attributes and 300 active variants.". 2 → 295 active Variants.
Test data    : 280 existing, 2 × 15 new (from the AC); the attribute values are placeholders
<!-- TC:TC-CAT-201:END -->

<!-- TC:TC-CAT-202:START traces=AC-CAT-148,REQ-CAT-106,API-CAT-019,API-CAT-002 -->
### TC-CAT-202 — Two parallel "make primary" requests leave exactly one primary image
Derived from : AC-CAT-148  (REQ-CAT-106)
Exercises    : API-CAT-019 PUT /api/v1/cat/media/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-039 → CAT-200-PRIMARY-IMAGE-REPLACED (success notice) · guard: the Item row lock QR-CAT-010 FOR UPDATE, owned by API-CAT-019 (unit SVC-API-CRUD), with UQ_CAT_ITEM_MEDIA_PRIMARY behind it
Package      : SVC-API-CRUD
Scenario     : STATE · data class EDGE · language ALL
Preconditions: S0 · an Item with active images A (primary), B and C, each read with its current version
Host data    : none
Steps        : 1. Submit PUT B with isPrimaryFl true and PUT C with isPrimaryFl true simultaneously (two Owner sessions released together). 2. GET the Item.
Expected     : 1 → both requests complete one after the other: each answers 200 with code CAT-200-PRIMARY-IMAGE-REPLACED, messageAr «للصنف صورة رئيسية واحدة فقط؛ تم تعيين الصورة المختارة رئيسية بدل السابقة.», messageEn "An item has exactly one main image; the selected image replaced the previous main image.". 2 → exactly 1 image has isPrimaryFl true, and it is B or C; A has isPrimaryFl false.
Test data    : images A, B, C (from the AC)
<!-- TC:TC-CAT-202:END -->

<!-- TC:TC-CAT-203:START traces=AC-CAT-149,REQ-CAT-007,API-CAT-001,API-CAT-038 -->
### TC-CAT-203 — Two parallel creates with the same Arabic name: exactly one Item
Derived from : AC-CAT-149  (REQ-CAT-007)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-038 GET /api/v1/cat/items
Rule / code  : RULE-CAT-003 → CAT-409-ITEM-NAME-DUPLICATE · guard: UQ_CAT_ITEM_NAME_AR, whose violation API-CAT-001 (unit SVC-API-CRUD) translates to the code
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · no active Item named «كوب زجاج»
Host data    : none
Steps        : 1. Submit two POSTs of an Item with nameAr «كوب زجاج» simultaneously (two Owner sessions released together). 2. GET /api/v1/cat/items?text=كوب زجاج&isActiveFl=true.
Expected     : 1 → exactly one request answers 201; the other answers 409, code CAT-409-ITEM-NAME-DUPLICATE, messageAr «يوجد صنف نشط بالاسم نفسه: {code}. افتح الصنف الموجود أو غيّر الاسم.», messageEn "An active item already has this name: {code}. Open the existing item or change the name.", {code} = the code returned by the winning request (MODIFIED at the third gate-analysis revise, G12). 2 → totalElements 1.
Test data    : «كوب زجاج» (from the AC)
<!-- TC:TC-CAT-203:END -->

<!-- TC:TC-CAT-204:START traces=AC-CAT-150,REQ-CAT-087,API-CAT-014,API-CAT-043 -->
### TC-CAT-204 — Two parallel adds of one GTIN: exactly one barcode
Derived from : AC-CAT-150  (REQ-CAT-087)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes · API-CAT-043 GET /api/v1/cat/barcodes/lookup
Rule / code  : RULE-CAT-029 → CAT-409-BARCODE-DUPLICATE · guard: UQ_CAT_BARCODE_VALUE, whose violation API-CAT-014 (unit SVC-API-CRUD) translates to the code
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · active Variants W1 and W2 of two different Items · no active barcode 6223000000038 in the tenant
Host data    : none
Steps        : 1. Submit POST to W1 and POST to W2 of the barcode {barcodeValue: 6223000000038, barcodeTypeCode: GTIN} simultaneously (two Owner sessions released together). 2. As a Seller/cashier, GET the lookup with value 6223000000038.
Expected     : 1 → exactly one request answers 201; the other answers 409, code CAT-409-BARCODE-DUPLICATE, with the RULE-CAT-029 messageAr «هذا الباركود مستخدم في الصنف {code}. افتح ذلك الصنف أو استخدم باركودًا آخر.» and messageEn "This barcode is already used by item {code}. Open that item or use another barcode.", {code} = the code of the Item whose Variant won. 2 → 200; exactly that winning Variant.
Test data    : 6223000000038 (from the AC)
<!-- TC:TC-CAT-204:END -->

<!-- TC:TC-CAT-205:START traces=AC-CAT-151,REQ-CAT-062,API-CAT-009,API-CAT-017,API-CAT-007,API-CAT-002 -->
### TC-CAT-205 — A parallel Variant deactivation and Bundle save: exactly one succeeds
Derived from : AC-CAT-151  (REQ-CAT-062)
Exercises    : API-CAT-009 DELETE /api/v1/cat/variants/{id} · API-CAT-017 PUT /api/v1/cat/items/{id}/bundle-components · API-CAT-007 GET /api/v1/cat/variants/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-021 → CAT-409-VARIANT-IN-ACTIVE-BUNDLE · RULE-CAT-019 → CAT-422-BUNDLE-COMPONENT-INVALID · guard: API-CAT-017 (unit SVC-API-CRUD) reads P FOR SHARE (QR-CAT-017) while API-CAT-009 holds P's Item lock (QR-CAT-010) — ADR-CAT-047
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · a Variant P of an active STANDARD Item that has a second active Variant · an active Bundle B with 1 line that is not P, read with its current version
Host data    : none
Steps        : 1. Submit DELETE P and PUT B's line set (its current line plus {componentVariantFk: P, quantity: 1}) simultaneously (two Owner sessions released together). 2. GET P. 3. GET B.
Expected     : 1 → exactly one request succeeds. Either (a) DELETE answers 200 and PUT answers 422, code CAT-422-BUNDLE-COMPONENT-INVALID, messageAr «المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.», messageEn "A component must be an active variant of an item that is not a bundle, listed once per bundle."; then 2 → P isActiveFl false and 3 → B's lines exclude P. Or (b) PUT answers 200 and DELETE answers 409, code CAT-409-VARIANT-IN-ACTIVE-BUNDLE, messageAr «هذا المتغيّر مكوّن في 1 حزمة نشطة. أزله من هذه الحزم أو أوقفها أولًا.», messageEn "This variant is a component of 1 active bundles. Remove it from those bundles or deactivate them first."; then 2 → P isActiveFl true and 3 → B's lines include P.
Test data    : P, B (from the AC)
<!-- TC:TC-CAT-205:END -->

<!-- TC:TC-CAT-211:START traces=AC-CAT-153,REQ-CAT-071,API-CAT-021,API-CAT-024,API-CAT-022,API-CAT-039 -->
### TC-CAT-211 — A parallel sub-category create and parent deactivation: exactly one succeeds
Derived from : AC-CAT-153  (REQ-CAT-071)
Exercises    : API-CAT-021 POST /api/v1/cat/categories · API-CAT-024 DELETE /api/v1/cat/categories/{id} · API-CAT-022 GET /api/v1/cat/categories/{id} · API-CAT-039 GET /api/v1/cat/categories
Rule / code  : RULE-CAT-025 → CAT-409-CATEGORY-IN-USE · RULE-CAT-023 → CAT-422-CATEGORY-PARENT-INVALID · guard: the tenant tree lock pg_advisory_xact_lock (TENANT_ID, 'CAT_CATEGORY_TREE'), taken by API-CAT-021 before it reads the parent and by API-CAT-024 before QR-CAT-038 (unit SVC-API-CRUD) — ADR-CAT-047 as amended
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · an active top-level Category «إلكترونيات» with 0 active sub-categories and 0 active Items · no Category with code PHONES
Host data    : none
Steps        : 1. Submit POST {code: PHONES, nameAr: «هواتف», parentCategoryFk: «إلكترونيات»} and DELETE «إلكترونيات» simultaneously (two Owner sessions released together). 2. GET «إلكترونيات». 3. GET /api/v1/cat/categories?isActiveFl=true.
Expected     : 1 → exactly one request succeeds. Either (a) POST answers 201 and DELETE answers 409, code CAT-409-CATEGORY-IN-USE, messageAr «لا يمكن إيقاف الفئة لوجود 1 فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا.», messageEn "The category cannot be deactivated while it holds 1 active sub-categories or items. Move or deactivate them first."; then 2 → isActiveFl true and 3 → PHONES is an active child of «إلكترونيات». Or (b) DELETE answers 200 and POST answers 422, code CAT-422-CATEGORY-PARENT-INVALID, messageAr «الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.», messageEn "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels."; then 2 → isActiveFl false and 3 → no Category PHONES. In neither branch does an active Category have an inactive parent.
Test data    : «إلكترونيات», PHONES, «هواتف» (from the AC)
<!-- TC:TC-CAT-211:END -->

<!-- TC:TC-CAT-212:START traces=AC-CAT-144,AC-CAT-154,AC-CAT-155,AC-CAT-156,REQ-CAT-135,REQ-CAT-145,REQ-CAT-146,REQ-CAT-147,API-CAT-003,API-CAT-006,API-CAT-008,API-CAT-010,API-CAT-012,API-CAT-014,API-CAT-015,API-CAT-017,API-CAT-018,API-CAT-019,API-CAT-009,API-CAT-013,API-CAT-016,API-CAT-020,API-CAT-005 -->
### TC-CAT-212 — RULE-CAT-053 on every endpoint that applies it, the three repairs and the child deactivations under an inactive Item
Derived from : AC-CAT-144, AC-CAT-154, AC-CAT-155, AC-CAT-156  (REQ-CAT-135; the repairs REQ-CAT-145, REQ-CAT-146, REQ-CAT-147)
Exercises    : API-CAT-003, 006, 008, 010, 012, 014, 015, 017, 018, 019 (one row each) · API-CAT-009, 013, 016, 020 (child deactivations) · API-CAT-005 (reactivation at the end)
Rule / code  : RULE-CAT-053 → CAT-422-PARENT-INACTIVE · repairs allowed (ADR-CAT-068) · RULE-CAT-048 → CAT-200-DEACTIVATED
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL · parameterised (one row per endpoint)
Preconditions: S0 (`variants` and `bundles` on) · a STANDARD Item I with base unit PIECE, nameEn "Cup", categoryFk empty, exactly 1 active Variant V1 carrying the active GTIN 6223000000014, the active Item unit CARTON (no barcode bound to it) and the active image M1 · an active Category NEW · the active attribute SIZE with active values M and L · a BUNDLE Item B with the active line {C, 1} on a Variant C of an active STANDARD Item · then I and B are deactivated through API-CAT-004 · each row reads the record it sends with its current version
Host data    : none
Steps        : 1. Send each row below to I (rows 1–8, 10, 11) or to B (row 9), in order, and record the answer. 2. DELETE the barcode 6223000000014 (API-CAT-016), the Item unit CARTON (API-CAT-013), the image M1 (API-CAT-020) and the Variant created by row 3 for L (API-CAT-009). 3. PATCH I with {isActiveFl: true}. 4. GET I.

| Row | Endpoint | Request on the inactive Item | Expected |
|---|---|---|---|
| 1 | API-CAT-003 | PUT I with its fields and nameEn "Cup 2" | 422 CAT-422-PARENT-INACTIVE |
| 2 | API-CAT-003 | PUT I with its fields and categoryFk NEW only | 200 — repair; categoryFk NEW |
| 3 | API-CAT-006 | POST generation SIZE [M, L] | 201 — repair; V1 takes M, a new Variant carries L |
| 4 | API-CAT-008 | PUT V1 override GS1 «6223000000014» | 422 CAT-422-PARENT-INACTIVE |
| 5 | API-CAT-010 | POST unit BOX, conversionFactor 6 | 422 CAT-422-PARENT-INACTIVE |
| 6 | API-CAT-012 | PUT CARTON with isSalesFl false | 422 CAT-422-PARENT-INACTIVE |
| 7 | API-CAT-014 | POST GTIN 6223000000021 to V1 | 422 CAT-422-PARENT-INACTIVE |
| 8 | API-CAT-015 | POST internal barcode to V1 | 422 CAT-422-PARENT-INACTIVE |
| 9 | API-CAT-017 | PUT B's lines [{C, 2}] | 200 — repair; B's line is C × 2 |
| 10 | API-CAT-018 | POST a new image (PNG ≤ 5 MB, uploaded through the given FILE endpoint) | 422 CAT-422-PARENT-INACTIVE |
| 11 | API-CAT-019 | PUT M1 with nameEn "Cup photo" | 422 CAT-422-PARENT-INACTIVE |

Expected     : 1 → every row answers as its table cell says; every 422 carries messageAr «هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا.», messageEn "This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant.", and changes nothing; no repair row (2, 3, 9) answers CAT-422-PARENT-INACTIVE. 2 → each deactivation answers 200 with code CAT-200-DEACTIVATED (the image removal answers 200). 3 → 200; isActiveFl true. 4 → nameEn "Cup", categoryFk NEW, exactly 1 active Variant (V1, carrying M), no active Item unit, no active image, no active barcode.
Test data    : "Cup", "Cup 2", NEW, SIZE M / L, BOX, CARTON, 6223000000014, 6223000000021, B, C (placeholders where unnamed)
<!-- TC:TC-CAT-212:END -->

<!-- TC:TC-CAT-214:START traces=AC-CAT-158,REQ-CAT-136,API-CAT-053,API-CAT-022 -->
### TC-CAT-214 — A Category under an inactive parent is reactivated only after its parent
Derived from : AC-CAT-158  (REQ-CAT-136)
Exercises    : API-CAT-053 PATCH /api/v1/cat/categories/{id} · API-CAT-022 GET /api/v1/cat/categories/{id}
Rule / code  : RULE-CAT-023 → CAT-422-CATEGORY-PARENT-INVALID
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the Category PHONES under «إلكترونيات»; PHONES then «إلكترونيات» deactivated through API-CAT-024
Host data    : none
Steps        : 1. PATCH PHONES with {isActiveFl: true}. 2. GET PHONES. 3. PATCH «إلكترونيات» with {isActiveFl: true}. 4. PATCH PHONES with {isActiveFl: true}.
Expected     : 1 → 422, code CAT-422-CATEGORY-PARENT-INVALID, messageAr «الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.», messageEn "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels.". 2 → isActiveFl false. 3 → 200; isActiveFl true. 4 → 200; isActiveFl true, parentCategoryFk «إلكترونيات».
Test data    : PHONES, «إلكترونيات» (from the AC)
<!-- TC:TC-CAT-214:END -->

<!-- TC:TC-CAT-217:START traces=AC-CAT-161,REQ-CAT-139,API-CAT-049,API-CAT-038,API-CAT-042,API-CAT-043,API-CAT-032 -->
### TC-CAT-217 — An import past its 120-second limit is rolled back whole
Derived from : AC-CAT-161  (REQ-CAT-139)
Exercises    : API-CAT-049 POST /api/v1/cat/item-imports · API-CAT-038 GET /api/v1/cat/items · API-CAT-042 GET /api/v1/cat/variants · API-CAT-043 GET /api/v1/cat/barcodes/lookup · API-CAT-032 POST /api/v1/cat/scale-label-layouts
Rule / code  : RULE-CAT-049 → CAT-500-CHANGE-NOT-SAVED · time budget and whole-import rollback of ADR-CAT-071, entity saves of ADR-CAT-074
Package      : SVC-API-INT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · the tenant holds 0 Items and no scale label layout · an xlsx on the import sheet with 5,000 valid rows (distinct Arabic names, no Category), 100 of which carry a valid, unused GTIN beginning with 622 · the write pass of API-CAT-049 is delayed past the 120-second transaction timeout at the persistence seam, after the first 500 rows are saved and before the next row is saved (ADR-CAT-052 §6, ADR-CAT-074), the same fault-injection seam as TC-CAT-004 · the outbox is observed
Host data    : none
Steps        : 1. POST the file. 2. GET /api/v1/cat/items (no filter). 3. GET /api/v1/cat/variants. 4. As a Seller/cashier, GET the lookup with the GTIN of the file's first GTIN row. 5. Count the cat.CatalogueChanged events delivered after step 1. 6. Remove the injected delay and POST a scale label layout {key: 21}. 7. With no delay injected, POST the same file again and measure the elapsed time of the request.
Expected     : 1 → 500, code CAT-500-CHANGE-NOT-SAVED, messageAr «تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة.», messageEn "The change was not saved and no change notice was sent. Try again.". 2 → totalElements 0: the first 500 rows, already saved, were rolled back with the rest. 3 → totalElements 0. 4 → 404, code CAT-404-BARCODE-NOT-FOUND: no barcode was stored. 5 → 0 events. 6 → 201 at once: the scale-prefix lock was released by the rollback. 7 → 200; data.itemsCreated 5000; the elapsed time is at most 120 s (the hard limit), and it is recorded as the measurement of the 60-second target of ADR-CAT-071 / ADR-CAT-074 on the reference deployment — reported, not a pass condition (MODIFIED at the seventh gate-analysis revise, G3).
Test data    : 5,000 rows, 100 GTINs (placeholders `<name-1..5000>`, `<GTIN-1..100>`); the 120-second limit, the 60-second target and the 500-row batch of ADR-CAT-071 / ADR-CAT-074; layout key 21 (this case's own)
<!-- TC:TC-CAT-217:END -->

<!-- TC:TC-CAT-218:START traces=AC-CAT-112,REQ-CAT-110,API-CAT-018,API-CAT-002 -->
### TC-CAT-218 — What CAT decides on an image: an unusable reference is not found, a 21st image is refused, one public file may back two images
Derived from : AC-CAT-112  (REQ-CAT-110; the CAT part of RULE-CAT-038 as amended by ADR-CAT-076)
Exercises    : API-CAT-018 POST /api/v1/cat/items/{id}/media · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : PLATFORM-STD CAT-404-NOT-FOUND (a reference that is not available or has no public URL — `FileDocumentLookupApi.isAvailable` / `publicUrl`) · RULE-CAT-038 → CAT-422-IMAGE-NOT-ACCEPTED (the 20-record count) · one fileDocumentRef may back several media records (ADR-CAT-056 §4). Type, size and public storage are not asserted here: they are the FILE category's (TC-CAT-056)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an active Item with 0 active images
Host data    : none
Steps        : 1. Upload a 1 MB PNG through the given FILE endpoint to the private storage (not the image category), then POST its `fileDocumentRef` as an image of the Item. 2. POST a `fileDocumentRef` that names no FILE document of the tenant. 3. GET the Item. 4. Upload a 1 MB PNG to the category `CAT_ITEM_IMAGE` and POST its `fileDocumentRef` as an image of the Item, twice. 5. Upload 18 more 1 MB PNGs to `CAT_ITEM_IMAGE` and POST each one (20 active images). 6. Upload one more 1 MB PNG to `CAT_ITEM_IMAGE` and POST it. 7. GET the Item.
Expected     : 1 → 404, code CAT-404-NOT-FOUND (the document has no public URL): the storefront could not show it. 2 → 404, code CAT-404-NOT-FOUND. 3 → 0 active media records: nothing was created. 4 → 201 both times. 5 → 201 each time. 6 → 422, code CAT-422-IMAGE-NOT-ACCEPTED, messageAr «الصورة يجب أن تكون JPG أو PNG أو WEBP بحجم لا يتجاوز 5 ميجابايت، وبحد أقصى 20 صورة نشطة للصنف.», messageEn "The image must be JPG, PNG or WEBP of at most 5 MB, with at most 20 active images per item.". 7 → exactly 20 active media records, 2 of them carrying the same fileDocumentRef, exactly 1 of them primary.
Test data    : 21 1 MB PNG files in the image category and 1 in the private storage (placeholders); a fileDocumentRef of no document (placeholder)
<!-- TC:TC-CAT-218:END -->

<!-- TC:TC-CAT-219:START traces=AC-CAT-162,REQ-CAT-144,API-CAT-005,API-CAT-001,API-CAT-004,API-CAT-002 -->
### TC-CAT-219 — Reactivating an Item whose name another active Item took meanwhile is refused
Derived from : AC-CAT-162  (REQ-CAT-144)
Exercises    : API-CAT-005 PATCH /api/v1/cat/items/{id} · API-CAT-001 POST /api/v1/cat/items · API-CAT-004 DELETE /api/v1/cat/items/{id} · API-CAT-002 GET /api/v1/cat/items/{id}
Rule / code  : RULE-CAT-003 → CAT-409-ITEM-NAME-DUPLICATE (on reactivation — A7 invariant 4)
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · an Item I1 «كوب زجاج» with 1 active Variant and no Category, deactivated through API-CAT-004 · then a second Item I2 «كوب زجاج» created through API-CAT-001 with code ITM-000031 (series CAT_ITEM positioned so that I2 receives ITM-000031) and left active
Host data    : none
Steps        : 1. PATCH I1 with body {isActiveFl: true}. 2. GET I1. 3. DELETE I2. 4. PATCH I1 with body {isActiveFl: true}.
Expected     : 1 → 409, code CAT-409-ITEM-NAME-DUPLICATE, messageAr «يوجد صنف نشط بالاسم نفسه: ITM-000031. افتح الصنف الموجود أو غيّر الاسم.», messageEn "An active item already has this name: ITM-000031. Open the existing item or change the name.". 2 → isActiveFl false: I1 stays inactive. 3 → 200 (the repair: the other active Item leaves the name). 4 → 200; isActiveFl true, publishStateCode UNPUBLISHED.
Test data    : «كوب زجاج», ITM-000031 (from the AC)
<!-- TC:TC-CAT-219:END -->

<!-- TC:TC-CAT-225:START traces=AC-CAT-007,REQ-CAT-007,API-CAT-001,API-CAT-038 -->
### TC-CAT-225 — Arabic normalisation keeps Arabic-Indic digits and still folds letter variants
Derived from : AC-CAT-007  (REQ-CAT-007; the normalisation of RULE-CAT-003 — ADR-CAT-062)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-038 GET /api/v1/cat/items
Rule / code  : RULE-CAT-003 → CAT-409-ITEM-NAME-DUPLICATE · guard: UQ_CAT_ITEM_NAME_AR on `CAT_NORMALIZE_AR(NAME_AR)` (db-script BLOCK 1, the removed set written by code point U+064B–U+065F, U+0670, U+0640) and the service normaliser `CatArabicNormalizer`, which removes the same set
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · the tenant holds 0 Items
Host data    : none
Steps        : 1. POST an Item with nameAr «علبة ٢٥». 2. POST an Item with nameAr «علبة ٣٠». 3. POST an Item with nameAr «أحمر» and record its code `<code>`. 4. POST an Item with nameAr «احمر». 5. GET /api/v1/cat/items?isActiveFl=true. 6. White-box check on the migrated database and the service class: evaluate `CAT_NORMALIZE_AR` and `CatArabicNormalizer` on «علبة ٢٥», «علبة ٣٠», «أحمر», «احمر» and «مُمتاز».
Expected     : 1 → 201. 2 → 201: the Arabic-Indic digits ٢٥ and ٣٠ are kept, so the two names differ. 3 → 201. 4 → 409, code CAT-409-ITEM-NAME-DUPLICATE, messageAr «يوجد صنف نشط بالاسم نفسه: <code>. افتح الصنف الموجود أو غيّر الاسم.», messageEn "An active item already has this name: <code>. Open the existing item or change the name.": أ still folds to ا. 5 → totalElements 3 («علبة ٢٥», «علبة ٣٠», «أحمر»). 6 → the two normalisers return the same string for each input: «علبة ٢٥», «علبة ٣٠», «احمر», «احمر» and «ممتاز» (the damma U+064F removed).
Test data    : «علبة ٢٥», «علبة ٣٠», «أحمر», «احمر», «مُمتاز» (this case's own; the gate-analysis round 9 finding G2)
<!-- TC:TC-CAT-225:END -->

<!-- TC:TC-CAT-226:START traces=AC-CAT-166,REQ-CAT-041,API-CAT-049,API-CAT-032,API-CAT-038 -->
### TC-CAT-226 — An import row whose GTIN begins with an active scale prefix is rejected, the other rows are created
Derived from : AC-CAT-166  (REQ-CAT-041)
Exercises    : API-CAT-032 POST /api/v1/cat/scale-label-layouts · API-CAT-049 POST /api/v1/cat/item-imports · API-CAT-038 GET /api/v1/cat/items
Rule / code  : RULE-CAT-045 (RULE-CAT-032 as {reason}) → CAT-200-IMPORT-ROW-REJECTED
Package      : SVC-API-INT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: S0 · the tenant holds 0 Items · an active scale label layout with prefix 20 (created through API-CAT-032) · an xlsx on the import sheet with 3 valid data rows («صنف أ», «صنف ب», «صنف ج»), of which only row 2 («صنف ب») carries a GTIN, 2000000000008
Host data    : none
Steps        : 1. POST the file. 2. GET /api/v1/cat/items.
Expected     : 1 → 200; data.rowsRead 3, data.itemsCreated 2, data.rowsRejected 1; data.rejectedRows[0] has rowNumber 2, column GTIN barcode, messageAr «الصف 2، العمود GTIN barcode: الباركود يبدأ ببادئة ملصق ميزان نشط (20). استخدم باركودًا آخر أو عدّل إعداد الميزان. صحّح الصف وأعد استيراده.», messageEn "Row 2, column GTIN barcode: The barcode starts with the prefix of an active scale label layout (20). Use another barcode or change the scale settings. Correct the row and import it again.". 2 → totalElements 2 («صنف أ», «صنف ج»); no Item «صنف ب» and no barcode 2000000000008 exist.
Test data    : prefix 20, GTIN 2000000000008 (from the AC); «صنف أ», «صنف ب», «صنف ج» (this case's own)
<!-- TC:TC-CAT-226:END -->

<!-- TC:TC-CAT-227:START traces=AC-CAT-167,REQ-CAT-091,API-CAT-014,API-CAT-032,API-CAT-045,API-CAT-007 -->
### TC-CAT-227 — A parallel barcode add and scale-layout create on one prefix: exactly one succeeds
Derived from : AC-CAT-167  (REQ-CAT-091)
Exercises    : API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes · API-CAT-032 POST /api/v1/cat/scale-label-layouts · API-CAT-045 GET /api/v1/cat/settings · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-032 → CAT-409-BARCODE-SCALE-PREFIX ({prefix} = 21) · guard: the scale-prefix advisory lock QR-CAT-074 (`pg_advisory_xact_lock(:tenant, hashtext('CAT_SCALE_PREFIX'))`), taken by API-CAT-014 before it reads the active layouts (QR-CAT-059) and by API-CAT-032 before it reads the barcodes beginning with the key (QR-CAT-053), both of unit SVC-API-CRUD — ADR-CAT-047
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · no scale label layout · no active barcode beginning with 21 · an active Variant V of an active Item with base unit PIECE
Host data    : none
Steps        : 1. Submit POST to V of the barcode {barcodeValue: 2100000000005, barcodeTypeCode: GTIN} and POST of the layout {key: 21} simultaneously (two Owner sessions released together). 2. GET the settings. 3. GET V.
Expected     : 1 → exactly one request answers 201; the other answers 409, code CAT-409-BARCODE-SCALE-PREFIX, messageAr «الباركود يبدأ ببادئة ملصق ميزان نشط (21). استخدم باركودًا آخر أو عدّل إعداد الميزان.», messageEn "The barcode starts with the prefix of an active scale label layout (21). Use another barcode or change the scale settings.". Either (a) the barcode won: 2 → no layout with key 21, and 3 → V carries the active GTIN 2100000000005; or (b) the layout won: 2 → the layout 21 is active, and 3 → V carries no barcode. In neither branch does an active GTIN begin with the prefix of an active layout.
Test data    : prefix 21, GTIN 2100000000005 (check digit 5) (from the AC)
<!-- TC:TC-CAT-227:END -->

<!-- TC:TC-CAT-228:START traces=AC-CAT-168,REQ-CAT-128,REQ-CAT-089,API-CAT-013,API-CAT-014,API-CAT-011,API-CAT-007 -->
### TC-CAT-228 — A parallel Item unit deactivation and barcode add on that unit: exactly one succeeds
Derived from : AC-CAT-168  (REQ-CAT-128; the refused add is REQ-CAT-089)
Exercises    : API-CAT-013 DELETE /api/v1/cat/item-units/{id} · API-CAT-014 POST /api/v1/cat/variants/{id}/barcodes · API-CAT-011 GET /api/v1/cat/item-units/{id} · API-CAT-007 GET /api/v1/cat/variants/{id}
Rule / code  : RULE-CAT-051 → CAT-409-ITEM-UNIT-HAS-BARCODES · RULE-CAT-031 → CAT-422-BARCODE-UNIT-INVALID · guard: the Item row lock QR-CAT-010 FOR UPDATE, taken by API-CAT-013 before QR-CAT-071 counts the barcodes bound to the unit and by API-CAT-014 before RULE-CAT-031 reads the active units (QR-CAT-044), both of unit SVC-API-CRUD — ADR-CAT-060
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · an active Item with base unit PIECE and the active Item unit CARTON, with 0 active barcodes bound to CARTON · an active Variant V of that Item · no active barcode 6223000000045 in the tenant
Host data    : none
Steps        : 1. Submit DELETE CARTON and POST to V of the barcode {barcodeValue: 6223000000045, barcodeTypeCode: GTIN, uomCode: CARTON} simultaneously (two Owner sessions released together). 2. GET CARTON by itemUnitPk. 3. GET V.
Expected     : 1 → exactly one request succeeds. Either (a) DELETE answers 200 and POST answers 422, code CAT-422-BARCODE-UNIT-INVALID, messageAr «وحدة الباركود يجب أن تكون الوحدة الأساسية للصنف أو إحدى وحداته النشطة.», messageEn "The barcode unit must be the item's base unit or one of its active units."; then 2 → CARTON isActiveFl false and 3 → V carries no barcode 6223000000045. Or (b) POST answers 201 and DELETE answers 409, code CAT-409-ITEM-UNIT-HAS-BARCODES, messageAr «هذه الوحدة مستخدمة في 1 باركود نشط. أوقف هذه الباركودات أولًا.», messageEn "This unit is used by 1 active barcodes. Deactivate those barcodes first."; then 2 → CARTON isActiveFl true and 3 → V carries the active GTIN 6223000000045 on unit CARTON. In neither branch is an active barcode bound to an inactive Item unit.
Test data    : CARTON, GTIN 6223000000045 (check digit 5) (from the AC)
<!-- TC:TC-CAT-228:END -->

<!-- TC:TC-CAT-229:START traces=AC-CAT-169,REQ-CAT-054,REQ-CAT-049,API-CAT-006,API-CAT-031,API-CAT-002,API-CAT-026 -->
### TC-CAT-229 — A parallel Variant generation and value deactivation: exactly one succeeds
Derived from : AC-CAT-169  (REQ-CAT-054; the generation is REQ-CAT-049)
Exercises    : API-CAT-006 POST /api/v1/cat/items/{id}/variants · API-CAT-031 DELETE /api/v1/cat/variant-attribute-values/{id} · API-CAT-002 GET /api/v1/cat/items/{id} · API-CAT-026 GET /api/v1/cat/variant-attributes/{id}
Rule / code  : RULE-CAT-017 → CAT-409-ATTRIBUTE-VALUE-IN-USE · PLATFORM-STD CAT-400-REQUEST-INVALID (an inactive value is not offered to a generation — ADR-CAT-044) · guard: API-CAT-006 reads the selected values FOR SHARE (QR-CAT-031) while API-CAT-031 locks the value FOR UPDATE (QR-CAT-027) before QR-CAT-021 counts its usage, both of unit SVC-API-CRUD — ADR-CAT-047
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 (`variants` on) · a STANDARD Item I with only its default Variant D and no Variant attribute in use · the active attribute SIZE with the active values M and L, L carried by 0 active Variants
Host data    : none
Steps        : 1. Submit POST to I of the generation {selections: [{variantAttributeFk: SIZE, variantAttributeValueFks: [M, L]}]} and DELETE L simultaneously (two Owner sessions released together). 2. GET I. 3. GET SIZE.
Expected     : 1 → exactly one request succeeds. Either (a) the generation answers 201 and DELETE answers 409, code CAT-409-ATTRIBUTE-VALUE-IN-USE, messageAr «هذه الخاصية أو القيمة مستخدمة في 1 متغيّرًا نشطًا. أوقف هذه المتغيّرات أولًا.», messageEn "This attribute or value is used by 1 active variants. Deactivate those variants first."; then 2 → I has 2 active Variants, D carrying M and one new Variant carrying L, and 3 → L isActiveFl true. Or (b) DELETE answers 200 and the generation answers 400, code CAT-400-REQUEST-INVALID, messageAr «الطلب غير صالح. راجع الحقول المحددة ثم أعد المحاولة.», messageEn "The request is not valid. Check the highlighted fields and try again."; then 2 → I still has only D, with no attribute assignment, and 3 → L isActiveFl false. In neither branch does an active Variant carry an inactive value.
Test data    : SIZE M, L (from the AC); I, D (placeholders)
<!-- TC:TC-CAT-229:END -->

<!-- TC:TC-CAT-230:START traces=AC-CAT-170,REQ-CAT-072,REQ-CAT-071,API-CAT-001,API-CAT-024,API-CAT-022,API-CAT-038 -->
### TC-CAT-230 — A parallel Item create into a Category and that Category's deactivation: exactly one succeeds
Derived from : AC-CAT-170  (REQ-CAT-072; the refused deactivation is REQ-CAT-071)
Exercises    : API-CAT-001 POST /api/v1/cat/items · API-CAT-024 DELETE /api/v1/cat/categories/{id} · API-CAT-022 GET /api/v1/cat/categories/{id} · API-CAT-038 GET /api/v1/cat/items
Rule / code  : RULE-CAT-025 → CAT-409-CATEGORY-IN-USE · RULE-CAT-026 → CAT-422-CATEGORY-INACTIVE · guard: API-CAT-001 reads the Category FOR SHARE (QR-CAT-032) while API-CAT-024 locks it FOR UPDATE (QR-CAT-032, after the tree lock QR-CAT-073) before QR-CAT-038 counts its active Items, both of unit SVC-API-CRUD — ADR-CAT-047
Package      : SVC-API-CRUD
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: S0 · the active Category TOOLS «أدوات» with 0 active sub-categories and 0 active Items · no active Item named «مفك براغي»
Host data    : none
Steps        : 1. Submit POST of the Item {nameAr: «مفك براغي», categoryFk: TOOLS} and DELETE TOOLS simultaneously (two Owner sessions released together). 2. GET TOOLS. 3. GET /api/v1/cat/items?categoryFk=<TOOLS>&isActiveFl=true.
Expected     : 1 → exactly one request succeeds. Either (a) POST answers 201 and DELETE answers 409, code CAT-409-CATEGORY-IN-USE, messageAr «لا يمكن إيقاف الفئة لوجود 1 فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا.», messageEn "The category cannot be deactivated while it holds 1 active sub-categories or items. Move or deactivate them first."; then 2 → TOOLS isActiveFl true and 3 → totalElements 1. Or (b) DELETE answers 200 with code CAT-200-DEACTIVATED and POST answers 422, code CAT-422-CATEGORY-INACTIVE, messageAr «الفئة المختارة غير نشطة. اختر فئة نشطة.», messageEn "The selected category is inactive. Choose an active category."; then 2 → TOOLS isActiveFl false and 3 → totalElements 0. In neither branch does an active Item point to an inactive Category.
Test data    : «أدوات» (from the AC); TOOLS, «مفك براغي» (this case's own)
<!-- TC:TC-CAT-230:END -->
<!-- SUB:RULE-SCENARIOS:END -->

<!-- SUB:API-SCENARIOS:START traces=AC-CAT-171,REQ-CAT-148,AC-CAT-165,REQ-CAT-140,REQ-CAT-141,AC-CAT-163,AC-CAT-164,AC-CAT-157,AC-CAT-159,AC-CAT-160,REQ-CAT-136,REQ-CAT-137,REQ-CAT-138,AC-CAT-137,AC-CAT-139,AC-CAT-140,AC-CAT-141,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,REQ-CAT-134,AC-CAT-001,AC-CAT-002,AC-CAT-003,AC-CAT-004,AC-CAT-008,AC-CAT-010,AC-CAT-011,AC-CAT-012,AC-CAT-018,AC-CAT-021,AC-CAT-022,AC-CAT-025,AC-CAT-026,AC-CAT-029,AC-CAT-030,AC-CAT-033,AC-CAT-034,AC-CAT-037,AC-CAT-039,AC-CAT-040,AC-CAT-044,AC-CAT-045,AC-CAT-046,AC-CAT-047,AC-CAT-048,AC-CAT-049,AC-CAT-050,AC-CAT-051,AC-CAT-058,AC-CAT-059,AC-CAT-060,AC-CAT-066,AC-CAT-067,AC-CAT-068,AC-CAT-069,AC-CAT-070,AC-CAT-079,AC-CAT-080,AC-CAT-085,AC-CAT-086,AC-CAT-088,AC-CAT-094,AC-CAT-095,AC-CAT-097,AC-CAT-098,AC-CAT-099,AC-CAT-103,AC-CAT-104,AC-CAT-107,AC-CAT-109,AC-CAT-111,AC-CAT-117,AC-CAT-119,AC-CAT-120,AC-CAT-121,AC-CAT-122,AC-CAT-123,AC-CAT-124,AC-CAT-127,AC-CAT-129,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-008,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-017,REQ-CAT-020,REQ-CAT-021,REQ-CAT-024,REQ-CAT-025,REQ-CAT-028,REQ-CAT-029,REQ-CAT-032,REQ-CAT-033,REQ-CAT-036,REQ-CAT-038,REQ-CAT-039,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-049,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-077,REQ-CAT-078,REQ-CAT-083,REQ-CAT-084,REQ-CAT-086,REQ-CAT-092,REQ-CAT-093,REQ-CAT-095,REQ-CAT-096,REQ-CAT-097,REQ-CAT-101,REQ-CAT-102,REQ-CAT-105,REQ-CAT-107,REQ-CAT-109,REQ-CAT-115,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-125,REQ-CAT-127,AC-CAT-152,AC-CAT-076,REQ-CAT-074 -->
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
Expected     : 200; bundleComponents holds 3 lines, each with componentVariantFk, componentVariantCode (the Variant code) and quantity.
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
Expected     : 200; 3 rows, each with itemNameAr and firstBarcodeValue = its earliest-created active barcode (empty when it has none).
Test data    : «قميص», page size 20 (from the AC)
<!-- TC:TC-CAT-111:END -->

<!-- TC:TC-CAT-112:START traces=AC-CAT-104,REQ-CAT-102,API-CAT-050 -->
### TC-CAT-112 — Printing 15 labels for 2 Variants
Derived from : AC-CAT-104  (REQ-CAT-102)
Exercises    : API-CAT-050 POST /api/v1/cat/barcode-labels
Rule / code  : RULE-CAT-036, RULE-CAT-037 (satisfied) · —
Package      : SVC-API-INT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: S0, with the caller a Warehouse staff user · 2 active Variants, each with an active barcode
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
Expected     : 1 → 422, code CAT-422-LABEL-VARIANT-NO-BARCODE, messageAr «لا يوجد باركود نشط لـ 1 متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.», messageEn "1 variants have no active barcode. Generate an internal barcode for them, then print."; no document. 2 → 403, code CAT-403-FORBIDDEN; nothing is saved and no number is drawn — the Items page's operation stays the Owner's. 3 → 201; data.barcodeValue 0400000001005 (04 + 0000000100 + check digit 5), barcodeTypeCode INTERNAL, uomCode PIECE, isActiveFl true. 4 → V carries exactly that one active barcode. 5 → 200, application/pdf, one document holding 1 label that shows V's nameAr, code and 0400000001005. 6 → exactly 1 event: entity ENT-CAT-009, changeKind CREATED. 7 → 403, code CAT-403-FORBIDDEN; W still has no barcode.
Test data    : 0000000099 → 0400000001005 (from the AC); V, W (placeholders)
<!-- TC:TC-CAT-231:END -->
<!-- SUB:API-SCENARIOS:END -->
<!-- PHASE:TEST-PLAN-BE:END -->

## PHASE INT-XM — not present

CAT has no `XM-*` edge. The registry-db XM register is `records: []`, SRS A8 has `consumes: []`, and the backend plan's last phase `INT-C` carries no block. The integration phase is therefore absent from this plan, not written as an empty block. Inbound use of CAT by other modules (INV, PRC, PUR, SLS, SFR, TAX, INS — dependency graph) is those modules' own edges, and they test them.

## TC TRACEABILITY INDEX

### AC → TC (and REQ → TC)
Read REQ → TC from the REQ column. REQ-CAT-016 → TC-CAT-006, 007. REQ-CAT-041 → TC-CAT-019, 020, 226. REQ-CAT-007 → TC-CAT-003, 203, 225. REQ-CAT-008 → TC-CAT-069, 224. REQ-CAT-086 → TC-CAT-105, 188. REQ-CAT-101 → TC-CAT-111, 189. REQ-CAT-135 → TC-CAT-186, 198, 212. REQ-CAT-141 → TC-CAT-185, 222. REQ-CAT-145 → TC-CAT-185, 212. REQ-CAT-146 → TC-CAT-186, 212. REQ-CAT-147 → TC-CAT-187, 212. REQ-CAT-038 → TC-CAT-083 (also the empty-cell defaults). Since the seventh gate-analysis revise, REQ-CAT-130 → TC-CAT-185, REQ-CAT-142 → TC-CAT-186, REQ-CAT-143 → TC-CAT-187, REQ-CAT-144 → TC-CAT-219, REQ-CAT-140 → TC-CAT-221 and REQ-CAT-025 → TC-CAT-077 (G7). REQ-CAT-136 → TC-CAT-213, 214. REQ-CAT-005 → TC-CAT-001, 188 (188 also asserts that the barcodes stay active). REQ-CAT-110 → TC-CAT-056, 218. Since the tenth gate-analysis revise, REQ-CAT-091 → TC-CAT-047, 227; REQ-CAT-128 → TC-CAT-183, 228; REQ-CAT-054 → TC-CAT-025, 229; REQ-CAT-072 → TC-CAT-035, 230; REQ-CAT-148 → TC-CAT-231. Every other REQ maps to one TC.

| AC | REQ | TC |
|---|---|---|
| AC-CAT-001 | REQ-CAT-001 | TC-CAT-065 |
| AC-CAT-002 | REQ-CAT-002 | TC-CAT-066 |
| AC-CAT-003 | REQ-CAT-003 | TC-CAT-067 |
| AC-CAT-004 | REQ-CAT-004 | TC-CAT-068 |
| AC-CAT-005 | REQ-CAT-005 | TC-CAT-001 |
| AC-CAT-006 | REQ-CAT-006 | TC-CAT-002 |
| AC-CAT-007 | REQ-CAT-007 | TC-CAT-003, 225 (normalisation) |
| AC-CAT-008 | REQ-CAT-008 | TC-CAT-069 |
| AC-CAT-009 | REQ-CAT-009 | TC-CAT-004 |
| AC-CAT-010 | REQ-CAT-010 | TC-CAT-070 |
| AC-CAT-011 | REQ-CAT-011 | TC-CAT-071 |
| AC-CAT-012 | REQ-CAT-012 | TC-CAT-072 |
| AC-CAT-013 | REQ-CAT-013 | TC-CAT-005 |
| AC-CAT-014 | REQ-CAT-014 | frontend only — TC-CAT-128 |
| AC-CAT-015 | REQ-CAT-015 | frontend only — TC-CAT-129 |
| AC-CAT-016 | REQ-CAT-016 | TC-CAT-006 |
| AC-CAT-017 | REQ-CAT-016 | TC-CAT-007 |
| AC-CAT-018 | REQ-CAT-017 | TC-CAT-073 |
| AC-CAT-019 | REQ-CAT-018 | TC-CAT-008 |
| AC-CAT-020 | REQ-CAT-019 | TC-CAT-009 |
| AC-CAT-021 | REQ-CAT-020 | TC-CAT-074 |
| AC-CAT-022 | REQ-CAT-021 | TC-CAT-075 |
| AC-CAT-023 | REQ-CAT-022 | TC-CAT-010 |
| AC-CAT-024 | REQ-CAT-023 | TC-CAT-011 |
| AC-CAT-025 | REQ-CAT-024 | TC-CAT-076 |
| AC-CAT-026 | REQ-CAT-025 | TC-CAT-077 |
| AC-CAT-027 | REQ-CAT-026 | frontend only — TC-CAT-132 |
| AC-CAT-028 | REQ-CAT-027 | TC-CAT-012 |
| AC-CAT-029 | REQ-CAT-028 | TC-CAT-078 |
| AC-CAT-030 | REQ-CAT-029 | TC-CAT-079 |
| AC-CAT-031 | REQ-CAT-030 | TC-CAT-013 |
| AC-CAT-032 | REQ-CAT-031 | TC-CAT-014 |
| AC-CAT-033 | REQ-CAT-032 | TC-CAT-080 |
| AC-CAT-034 | REQ-CAT-033 | TC-CAT-081 |
| AC-CAT-035 | REQ-CAT-034 | TC-CAT-015 |
| AC-CAT-036 | REQ-CAT-035 | TC-CAT-016 |
| AC-CAT-037 | REQ-CAT-036 | TC-CAT-082 |
| AC-CAT-038 | REQ-CAT-037 | TC-CAT-017 |
| AC-CAT-039 | REQ-CAT-038 | TC-CAT-083 |
| AC-CAT-040 | REQ-CAT-039 | TC-CAT-084 |
| AC-CAT-041 | REQ-CAT-040 | TC-CAT-018 |
| AC-CAT-042 | REQ-CAT-041 | TC-CAT-019 |
| AC-CAT-043 | REQ-CAT-041 | TC-CAT-020 |
| AC-CAT-044 | REQ-CAT-042 | TC-CAT-085 |
| AC-CAT-045 | REQ-CAT-043 | TC-CAT-086, 223 (update of an inactive attribute) |
| AC-CAT-046 | REQ-CAT-044 | TC-CAT-087 |
| AC-CAT-047 | REQ-CAT-045 | TC-CAT-088 |
| AC-CAT-048 | REQ-CAT-046 | TC-CAT-089, 223 (update of an inactive value) |
| AC-CAT-049 | REQ-CAT-047 | TC-CAT-090 |
| AC-CAT-050 | REQ-CAT-048 | TC-CAT-091 |
| AC-CAT-051 | REQ-CAT-049 | TC-CAT-092, 223 (malformed request) |
| AC-CAT-052 | REQ-CAT-050 | TC-CAT-021 |
| AC-CAT-053 | REQ-CAT-051 | TC-CAT-022 |
| AC-CAT-054 | REQ-CAT-052 | TC-CAT-023 |
| AC-CAT-055 | REQ-CAT-053 | TC-CAT-024 |
| AC-CAT-056 | REQ-CAT-054 | TC-CAT-025 |
| AC-CAT-057 | REQ-CAT-055 | TC-CAT-026 |
| AC-CAT-058 | REQ-CAT-056 | TC-CAT-093, 223 (malformed request) |
| AC-CAT-059 | REQ-CAT-057 | TC-CAT-094, 223 (malformed request) |
| AC-CAT-060 | REQ-CAT-058 | TC-CAT-095 |
| AC-CAT-061 | REQ-CAT-059 | TC-CAT-027 |
| AC-CAT-062 | REQ-CAT-060 | TC-CAT-028 |
| AC-CAT-063 | REQ-CAT-061 | TC-CAT-029 |
| AC-CAT-064 | REQ-CAT-062 | TC-CAT-030 |
| AC-CAT-065 | REQ-CAT-063 | TC-CAT-031 |
| AC-CAT-066 | REQ-CAT-064 | TC-CAT-096 |
| AC-CAT-067 | REQ-CAT-065 | TC-CAT-097, 223 (update of an inactive Category) |
| AC-CAT-068 | REQ-CAT-066 | TC-CAT-098 |
| AC-CAT-069 | REQ-CAT-067 | TC-CAT-099 |
| AC-CAT-070 | REQ-CAT-068 | TC-CAT-100 |
| AC-CAT-071 | REQ-CAT-069 | TC-CAT-032 |
| AC-CAT-072 | REQ-CAT-070 | TC-CAT-033 |
| AC-CAT-073 | REQ-CAT-071 | TC-CAT-034 |
| AC-CAT-074 | REQ-CAT-072 | TC-CAT-035 |
| AC-CAT-075 | REQ-CAT-073 | frontend only — TC-CAT-137 |
| AC-CAT-076 | REQ-CAT-074 | TC-CAT-207 (frontend half: TC-CAT-138) |
| AC-CAT-077 | REQ-CAT-075 | TC-CAT-036 |
| AC-CAT-078 | REQ-CAT-076 | TC-CAT-037 (frontend half: TC-CAT-139) |
| AC-CAT-079 | REQ-CAT-077 | TC-CAT-101 |
| AC-CAT-080 | REQ-CAT-078 | TC-CAT-102, 223 (update of an inactive Item unit) |
| AC-CAT-081 | REQ-CAT-079 | TC-CAT-038 |
| AC-CAT-082 | REQ-CAT-080 | TC-CAT-039 |
| AC-CAT-083 | REQ-CAT-081 | TC-CAT-040 |
| AC-CAT-084 | REQ-CAT-082 | TC-CAT-041 |
| AC-CAT-085 | REQ-CAT-083 | TC-CAT-103, 223 (malformed request) |
| AC-CAT-086 | REQ-CAT-084 | TC-CAT-104 |
| AC-CAT-087 | REQ-CAT-085 | TC-CAT-042 |
| AC-CAT-088 | REQ-CAT-086 | TC-CAT-105 |
| AC-CAT-089 | REQ-CAT-087 | TC-CAT-043 |
| AC-CAT-090 | REQ-CAT-088 | TC-CAT-044 |
| AC-CAT-091 | REQ-CAT-089 | TC-CAT-045 |
| AC-CAT-092 | REQ-CAT-090 | TC-CAT-046 |
| AC-CAT-093 | REQ-CAT-091 | TC-CAT-047 |
| AC-CAT-094 | REQ-CAT-092 | TC-CAT-106 |
| AC-CAT-095 | REQ-CAT-093 | TC-CAT-107 |
| AC-CAT-096 | REQ-CAT-094 | TC-CAT-048 |
| AC-CAT-097 | REQ-CAT-095 | TC-CAT-108 |
| AC-CAT-098 | REQ-CAT-096 | TC-CAT-109 |
| AC-CAT-099 | REQ-CAT-097 | TC-CAT-110 |
| AC-CAT-100 | REQ-CAT-098 | TC-CAT-049 |
| AC-CAT-101 | REQ-CAT-099 | TC-CAT-050 |
| AC-CAT-102 | REQ-CAT-100 | TC-CAT-051 |
| AC-CAT-103 | REQ-CAT-101 | TC-CAT-111 |
| AC-CAT-104 | REQ-CAT-102 | TC-CAT-112 |
| AC-CAT-105 | REQ-CAT-103 | TC-CAT-052 |
| AC-CAT-106 | REQ-CAT-104 | TC-CAT-053 |
| AC-CAT-107 | REQ-CAT-105 | TC-CAT-113 |
| AC-CAT-108 | REQ-CAT-106 | TC-CAT-054, 223 (update of a removed image) |
| AC-CAT-109 | REQ-CAT-107 | TC-CAT-114 |
| AC-CAT-110 | REQ-CAT-108 | TC-CAT-055 |
| AC-CAT-111 | REQ-CAT-109 | TC-CAT-115 |
| AC-CAT-112 | REQ-CAT-110 | TC-CAT-056, 218 |
| AC-CAT-113 | REQ-CAT-111 | TC-CAT-057 |
| AC-CAT-114 | REQ-CAT-112 | TC-CAT-058 |
| AC-CAT-115 | REQ-CAT-113 | TC-CAT-059 |
| AC-CAT-116 | REQ-CAT-114 | TC-CAT-060 |
| AC-CAT-117 | REQ-CAT-115 | TC-CAT-116 |
| AC-CAT-118 | REQ-CAT-116 | TC-CAT-061 |
| AC-CAT-119 | REQ-CAT-117 | TC-CAT-117 |
| AC-CAT-120 | REQ-CAT-118 | TC-CAT-118 |
| AC-CAT-121 | REQ-CAT-119 | TC-CAT-119 |
| AC-CAT-122 | REQ-CAT-120 | TC-CAT-120 |
| AC-CAT-123 | REQ-CAT-121 | TC-CAT-121 |
| AC-CAT-124 | REQ-CAT-122 | TC-CAT-122 |
| AC-CAT-125 | REQ-CAT-123 | TC-CAT-062 |
| AC-CAT-126 | REQ-CAT-124 | TC-CAT-063 |
| AC-CAT-127 | REQ-CAT-125 | TC-CAT-123 |
| AC-CAT-128 | REQ-CAT-126 | TC-CAT-064 |
| AC-CAT-129 | REQ-CAT-127 | TC-CAT-124 |
| AC-CAT-130 | REQ-CAT-128 | TC-CAT-183 (frontend routing: TC-CAT-196) |
| AC-CAT-131 | REQ-CAT-129 | TC-CAT-184 |
| AC-CAT-132 | REQ-CAT-130 | TC-CAT-185 |
| AC-CAT-133 | REQ-CAT-142 | TC-CAT-186 |
| AC-CAT-134 | REQ-CAT-143 | TC-CAT-187 |
| AC-CAT-135 | REQ-CAT-086 | TC-CAT-188 |
| AC-CAT-136 | REQ-CAT-101 | TC-CAT-189 |
| AC-CAT-137 | REQ-CAT-131 | TC-CAT-190 |
| AC-CAT-138 | REQ-CAT-131 | frontend only — TC-CAT-194 |
| AC-CAT-139 | REQ-CAT-132 | TC-CAT-191 |
| AC-CAT-140 | REQ-CAT-133 | TC-CAT-192 |
| AC-CAT-141 | REQ-CAT-134 | TC-CAT-193 |
| AC-CAT-142 | REQ-CAT-066 | frontend only — TC-CAT-195 |
| AC-CAT-143 | REQ-CAT-016 | TC-CAT-197 |
| AC-CAT-144 | REQ-CAT-135 | TC-CAT-198 |
| AC-CAT-145 | REQ-CAT-050 | TC-CAT-199 |
| AC-CAT-146 | REQ-CAT-053 | TC-CAT-200 |
| AC-CAT-147 | REQ-CAT-052 | TC-CAT-201 |
| AC-CAT-148 | REQ-CAT-106 | TC-CAT-202 |
| AC-CAT-149 | REQ-CAT-007 | TC-CAT-203 |
| AC-CAT-150 | REQ-CAT-087 | TC-CAT-204 |
| AC-CAT-151 | REQ-CAT-062 | TC-CAT-205 |
| AC-CAT-152 | REQ-CAT-093 | TC-CAT-206 |
| AC-CAT-153 | REQ-CAT-071 | TC-CAT-211 |
| AC-CAT-154 | REQ-CAT-145 | TC-CAT-185, 212 (frontend: TC-CAT-210) |
| AC-CAT-155 | REQ-CAT-146 | TC-CAT-186, 212 (frontend: TC-CAT-210) |
| AC-CAT-156 | REQ-CAT-147 | TC-CAT-187, 212 (frontend: TC-CAT-210) |
| AC-CAT-157 | REQ-CAT-136 | TC-CAT-213 |
| AC-CAT-158 | REQ-CAT-136 | TC-CAT-214 |
| AC-CAT-159 | REQ-CAT-137 | TC-CAT-215 |
| AC-CAT-160 | REQ-CAT-138 | TC-CAT-216 |
| AC-CAT-161 | REQ-CAT-139 | TC-CAT-217 |
| AC-CAT-162 | REQ-CAT-144 | TC-CAT-219 (frontend: TC-CAT-220) |
| AC-CAT-163 | REQ-CAT-140 | TC-CAT-221 |
| AC-CAT-164 | REQ-CAT-141 | TC-CAT-222 |
| AC-CAT-165 | REQ-CAT-008 | TC-CAT-224 |
| AC-CAT-166 | REQ-CAT-041 | TC-CAT-226 |
| AC-CAT-167 | REQ-CAT-091 | TC-CAT-227 |
| AC-CAT-168 | REQ-CAT-128 | TC-CAT-228 |
| AC-CAT-169 | REQ-CAT-054 | TC-CAT-229 |
| AC-CAT-170 | REQ-CAT-072 | TC-CAT-230 |
| AC-CAT-171 | REQ-CAT-148 | TC-CAT-231 (frontend: TC-CAT-232) |

### API → TC (TC-CAT-…)
| API | TCs |
|---|---|
| API-CAT-001 | 003, 005, 007, 012, 013, 014, 016, 027, 028, 035, 036, 071, 074, 076, 077, 078, 080, 081, 093, 190, 193, 203, 219, 223, 225, 230 |
| API-CAT-002 | 001, 007, 008, 009, 015, 021, 022, 023, 027, 029, 031, 037, 038, 039, 041, 054, 056, 057, 061, 062, 065, 066, 068, 070, 073, 077, 078, 079, 083, 092, 094, 095, 097, 113, 114, 117, 184, 185, 186, 187, 188, 190, 191, 192, 197, 198, 200, 201, 202, 205, 212, 219, 221, 222, 223, 229 |
| API-CAT-003 | 004, 015, 031, 041, 062, 068, 069, 070, 117, 186, 190, 191, 192, 212 |
| API-CAT-004 | 001, 188, 190, 197 |
| API-CAT-005 | 002, 059, 060, 061, 185, 186, 187, 212, 219, 222 |
| API-CAT-006 | 006, 008, 009, 022, 023, 029, 092, 184, 185, 201, 212, 222, 223, 229 |
| API-CAT-007 | 015, 021, 024, 030, 048, 074, 086, 089, 118, 119, 199, 205, 206, 221, 222, 227, 228, 231 |
| API-CAT-008 | 015, 118, 212 |
| API-CAT-009 | 021, 024, 030, 199, 200, 205, 212, 221 |
| API-CAT-010 | 039, 101, 198, 212 |
| API-CAT-011 | 038, 040, 102, 122, 183, 223, 228 |
| API-CAT-012 | 040, 102, 212, 223 |
| API-CAT-013 | 038, 183, 212, 228 |
| API-CAT-014 | 043, 044, 045, 047, 050, 103, 109, 188, 199, 204, 212, 223, 227, 228 |
| API-CAT-015 | 104, 212, 223, 231 |
| API-CAT-016 | 042, 212 |
| API-CAT-017 | 007, 027, 094, 187, 205, 212, 223 |
| API-CAT-018 | 055, 056, 113, 212, 218 |
| API-CAT-019 | 054, 058, 202, 212, 223 |
| API-CAT-020 | 057, 114, 212 |
| API-CAT-021 | 033, 096, 211, 213, 224 |
| API-CAT-022 | 032, 034, 098, 211, 214, 223, 230 |
| API-CAT-023 | 032, 097, 223, 224 |
| API-CAT-024 | 034, 098, 211, 213, 224, 230 |
| API-CAT-025 | 006, 026, 072, 085, 215 |
| API-CAT-026 | 025, 086, 088, 216, 223, 229 |
| API-CAT-027 | 086, 223 |
| API-CAT-028 | 087, 215, 216 |
| API-CAT-029 | 088 |
| API-CAT-030 | 089, 223 |
| API-CAT-031 | 025, 090, 229 |
| API-CAT-032 | 049, 106, 217, 226, 227 |
| API-CAT-033 | 107, 206 |
| API-CAT-034 | 048 |
| API-CAT-035 | 063, 120 |
| API-CAT-036 | 121 |
| API-CAT-037 | 122 |
| API-CAT-038 | 016, 018, 065, 067, 083, 203, 217, 225, 226, 230 |
| API-CAT-039 | 033, 098, 099, 211, 213 |
| API-CAT-040 | 100 |
| API-CAT-041 | 026, 087, 090, 091, 215 |
| API-CAT-042 | 111, 189, 217 |
| API-CAT-043 | 042, 046, 048, 051, 105, 110, 188, 199, 204, 206, 217 |
| API-CAT-044 | 115 |
| API-CAT-045 | 049, 063, 107, 108, 121, 193, 227 |
| API-CAT-046 | 116 |
| API-CAT-047 | 060, 064, 123, 124 |
| API-CAT-048 | 082 |
| API-CAT-049 | 017, 018, 019, 020, 083, 084, 217, 226 |
| API-CAT-050 | 052, 053, 112, 231 |
| API-CAT-051 | 010, 011, 075 |
| API-CAT-052 | 207 |
| API-CAT-053 | 213, 214, 224 |
| API-CAT-054 | 215, 216 |
| API-CAT-055 | 216 |
| API-CAT-056 | 231 |

### RULE / code → TC (TC-CAT-…)
| RULE | catalog code | TCs (violation / notice / state) |
|---|---|---|
| RULE-CAT-001 | CAT-422-NAME-AR-INVALID | 012, 013 (refusal) · 017 (import row) · 078 (passes) |
| RULE-CAT-002 | CAT-422-NAME-EN-TOO-LONG | 014 |
| RULE-CAT-003 | CAT-409-ITEM-NAME-DUPLICATE | 003, 203 (parallel creates), 219 (reactivation), 225 (normalisation: letter variants collide, Arabic-Indic digits kept) · 020 (import row reason) |
| RULE-CAT-004 | CAT-422-CODE-SYSTEM-ASSIGNED | 015 (Item and Variant) · 080, 081 (assignment) |
| RULE-CAT-005 | CAT-500-CODE-NOT-ISSUED | 016 · 231 (the label screen's internal barcode draws its number) |
| RULE-CAT-006 | CAT-422-LOOKUP-VALUE-INACTIVE | 036 · 019 (import row) · 037 (kept value) · 120 |
| RULE-CAT-007 | CAT-422-BASE-UOM-FIXED | 041 |
| RULE-CAT-008 | CAT-422-CAPABILITY-OFF | 006 (variants: API-CAT-025, 006), 007 (bundles: API-CAT-001, 017) · 197 (not raised on a read or a deactivation) |
| RULE-CAT-009 | CAT-500-CAPABILITY-UNREADABLE | 005 |
| RULE-CAT-010 | CAT-200-VARIANTS-IN-USE | 010 · 075 (allowed) |
| RULE-CAT-011 | CAT-200-BUNDLES-IN-USE | 011 |
| RULE-CAT-012 | CAT-201-EXISTING-VARIANTS-KEPT | 008, 009 · 092 |
| RULE-CAT-013 | CAT-409-VARIANT-COMBINATION-DUPLICATE | 022 · 092 |
| RULE-CAT-014 | CAT-422-VARIANT-LIMIT-EXCEEDED | 023, 201 (parallel generations) |
| RULE-CAT-015 | CAT-409-LAST-ACTIVE-VARIANT | 024, 200 (parallel deactivations) |
| RULE-CAT-016 | CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE | 026, 215 (reactivation) · 216 |
| RULE-CAT-017 | CAT-409-ATTRIBUTE-VALUE-IN-USE | 025, 229 (parallel with a generation, branch a) · 087, 090 (0 uses) |
| RULE-CAT-018 | CAT-422-BUNDLE-COMPONENTS-REQUIRED | 027 (row 1; rows 2 … 6 the quantity bounds) · 093 |
| RULE-CAT-019 | CAT-422-BUNDLE-COMPONENT-INVALID | 028, 187 (reactivation, then repaired), 205 (parallel, branch a) · 093, 212 |
| RULE-CAT-020 | CAT-422-BUNDLE-SINGLE-VARIANT | 029 |
| RULE-CAT-021 | CAT-409-VARIANT-IN-ACTIVE-BUNDLE | 030, 205 (parallel, branch b) |
| RULE-CAT-022 | CAT-422-ITEM-KIND-FIXED | 031 |
| RULE-CAT-023 | CAT-422-CATEGORY-PARENT-INVALID | 032, 214 (reactivation), 211 (parallel, branch b) |
| RULE-CAT-024 | CAT-409-CATEGORY-DUPLICATE | 033, 213 (reactivation) |
| RULE-CAT-025 | CAT-409-CATEGORY-IN-USE | 034, 211 (parallel, branch a), 230 (parallel with an Item create, branch a) |
| RULE-CAT-026 | CAT-422-CATEGORY-INACTIVE | 035, 186 (reactivation, then repaired), 230 (parallel with the deactivation, branch b) · 212 (categoryFk-only repair) |
| RULE-CAT-027 | CAT-422-ITEM-UNIT-INVALID | 039 (row 1; rows 2 … 5 the factor bounds) · 101 |
| RULE-CAT-028 | CAT-422-CONVERSION-FACTOR-FIXED | 040 |
| RULE-CAT-029 | CAT-409-BARCODE-DUPLICATE | 043, 188 (value still held by the barcode of a deactivated Item), 204 (parallel adds) · 199 (value released by a Variant deactivation) |
| RULE-CAT-030 | CAT-422-GTIN-INVALID | 044 |
| RULE-CAT-031 | CAT-422-BARCODE-UNIT-INVALID | 045, 228 (parallel with the unit's deactivation, branch a) |
| RULE-CAT-032 | CAT-409-BARCODE-SCALE-PREFIX | 047, 227 (parallel barcode add and layout create) · 226 (import row reason) |
| RULE-CAT-033 | CAT-422-SCALE-CODE-LENGTH | 050 · 109 |
| RULE-CAT-034 | CAT-422-SCALE-LAYOUT-INVALID | 049 · 106, 107, 206 (update carries no scaleCodeLength) |
| RULE-CAT-035 | CAT-422-SCALE-LABEL-UNREADABLE | 051 (undefined code; wrong check digit) · 110, 206 |
| RULE-CAT-036 | CAT-422-LABEL-COPIES-OUT-OF-RANGE | 052 · 112 |
| RULE-CAT-037 | CAT-422-LABEL-VARIANT-NO-BARCODE | 053, 231 (then repaired on the label screen — ADR-CAT-078) · 112 |
| RULE-CAT-038 | CAT-422-IMAGE-NOT-ACCEPTED | 218 (the 21st active image; a reference with no public URL answers CAT-404-NOT-FOUND) · 113, 218 (one public file backs two images) · 056 (the file part — type, size, public storage — refused by the FILE category `CAT_ITEM_IMAGE` at upload, ADR-CAT-076) |
| RULE-CAT-039 | CAT-200-PRIMARY-IMAGE-REPLACED | 054, 202 (parallel) · 055, 057 (first image, promotion) |
| RULE-CAT-040 | CAT-422-MEDIA-VARIANT-MISMATCH | 058 |
| RULE-CAT-041 | CAT-422-PUBLISH-BLOCKED | 061 · 001, 002, 059, 060 (state) |
| RULE-CAT-042 | CAT-422-EINVOICE-CODE-INVALID | 062 · 117, 118 |
| RULE-CAT-043 | CAT-409-UNIT-EINVOICE-DUPLICATE | 063 · 120 |
| RULE-CAT-044 | CAT-422-IMPORT-FILE-REJECTED | 018 |
| RULE-CAT-045 | CAT-200-IMPORT-ROW-REJECTED | 017, 019, 020, 084, 226 |
| RULE-CAT-046 | CAT-400-SEARCH-TERM-TOO-SHORT | 064 |
| RULE-CAT-047 | CAT-404-BARCODE-NOT-FOUND | 046, 188 (Item deactivated) · 042, 048 |
| RULE-CAT-048 | CAT-200-DEACTIVATED | 001, 038, 042, 098, 197, 221, 224 · 021, 199 (barcodes follow the Variant) |
| RULE-CAT-049 | CAT-500-CHANGE-NOT-SAVED | 004, 217 (import past its time limit, rolled back whole) · 059, 069, 224 (event after commit; 224 for every Category change — ADR-CAT-077) |
| RULE-CAT-050 | CAT-422-ITEM-NO-ACTIVE-VARIANT | 185 (then repaired by generation) |
| RULE-CAT-051 | CAT-409-ITEM-UNIT-HAS-BARCODES | 183, 228 (parallel with a barcode add, branch b) |
| RULE-CAT-052 | CAT-422-VARIANT-ATTRIBUTE-MISSING | 184 |
| RULE-CAT-053 | CAT-422-PARENT-INACTIVE | 198, 212 (every endpoint that applies it), 186 (a change other than categoryFk) · 185, 187, 212 (the three repairs are not refused — ADR-CAT-068) |
| PLATFORM-STD | CAT-403-FORBIDDEN, CAT-404-NOT-FOUND, CAT-409-STALE-VERSION, CAT-404-LOOKUP-KEY-UNKNOWN | 207 (404 for a key CAT does not own and 403 without a CAT permission on API-CAT-052 — ADR-CAT-065), 190 (403 on API-CAT-001, 003, 004 — AC-CAT-137), 191 (404 across tenants — AC-CAT-139), 192 (409 on a stale version — AC-CAT-140). TC-CAT-124 asserts that the catalogue search does *not* answer 403. CAT-400-REQUEST-INVALID has no AC of its own; it is asserted by TC-CAT-184 rows 2 and 3 (empty or oversized generation selections), TC-CAT-216 (value under an inactive attribute) TC-CAT-223 (a request that contradicts itself or names a record outside its parent — ten cases on API-CAT-001, 006, 014, 015, 017 — and a PUT on an inactive record — five cases on API-CAT-012, 019, 023, 027, 030; none answering CAT-500), TC-CAT-027 and TC-CAT-039 (a quantity or a conversion factor outside the document's bounds) and TC-CAT-229 (a generation naming a value deactivated meanwhile). CAT-403-FORBIDDEN is also asserted by TC-CAT-231 (API-CAT-015 without PERM_CAT_ITEMS_CREATE, API-CAT-056 without PERM_CAT_BARCODE_LABELS_CREATE). CAT-404-NOT-FOUND is also asserted by TC-CAT-218 (an image reference with no public URL — ADR-CAT-076). 401 and 500-SERVER-ERROR have no AC of their own and no TC. 413 and 415 left the catalog with the multipart surface (ADR-CAT-056) |

### XM → TC
none — no `XM-*` exists (registry-db `records: []`).

### Package → TC (TC-CAT-…)
| Package (backend plan unit) | # | TCs |
|---|---|---|
| CORE | 11 | 004, 005, 037, 069, 071, 072, 079, 080, 081, 191, 193 |
| DATA-DOM-MASTER | 1 | 092 |
| DATA-DOM-LOOKUP | 2 | 048, 122 |
| SVC-API-CRUD | 117 | 001, 002, 003, 006, 007, 008, 009, 012, 013, 014, 015, 016, 021, 022, 023, 024, 025, 026, 027, 028, 029, 030, 031, 032, 033, 034, 035, 036, 038, 039, 040, 041, 042, 043, 044, 045, 047, 049, 050, 054, 055, 056, 057, 058, 059, 060, 061, 062, 063, 065, 066, 068, 070, 073, 074, 076, 077, 078, 085, 086, 087, 088, 089, 090, 093, 094, 095, 096, 097, 098, 101, 102, 103, 104, 106, 107, 109, 113, 114, 117, 118, 119, 120, 121, 183, 184, 185, 186, 187, 192, 197, 198, 199, 200, 201, 202, 203, 204, 205, 206, 211, 212, 213, 214, 215, 216, 218, 219, 221, 222, 223, 224, 225, 227, 228, 229, 230 |
| SVC-API-SEARCH | 17 | 046, 051, 064, 067, 091, 099, 100, 105, 108, 110, 111, 115, 116, 123, 188, 189, 207 |
| SVC-API-INT | 16 | 010, 011, 017, 018, 019, 020, 052, 053, 075, 082, 083, 084, 112, 217, 226, 231 |
| SEC-BE | 2 | 124, 190 |
| DOC · ALIGN-BE · INT-C | 0 | no TC — `INT-C` has no block (no XM); `DOC` and `ALIGN-BE` implement no AC of their own (ADR-CAT-052 §2) |

## COVERAGE

| Measure | Result |
|---|---|
| AC covered (this track) | 165 / 171 ✓. The 6 others are observable on a screen only and are covered on the frontend track: AC-CAT-014 → TC-CAT-128, AC-CAT-015 → TC-CAT-129, AC-CAT-027 → TC-CAT-132, AC-CAT-075 → TC-CAT-137, AC-CAT-138 → TC-CAT-194, AC-CAT-142 → TC-CAT-195 (ADR-CAT-052 §1). AC-CAT-076 has its server read here (TC-CAT-207) and its field on the frontend (TC-CAT-138). |
| AC covered (both plans) | 171 / 171 ✓, no gap |
| REQ covered (this track) | 144 / 148 ✓. REQ-CAT-014, 015, 026 and 073 are covered on the frontend track (TC-CAT-128, 129, 132, 137). |
| REQ covered (both plans) | 148 / 148 ✓ (REQ-CAT-148, added at the tenth gate-analysis revise, by TC-CAT-231 and TC-CAT-232; REQ-CAT-140 … 147 were split from REQ-CAT-025, 130 and 135 at the seventh gate-analysis revise and are covered here by the TCs of their re-pointed ACs) |
| API covered | 56 / 56 ✓. This includes API-CAT-011 and API-CAT-046, which no CAT screen calls (ADR-CAT-051), and API-CAT-052 (TC-CAT-207); API-CAT-053 … 055 by TC-CAT-213 … 216; API-CAT-056 by TC-CAT-231. |
| RULE covered | 53 / 53 ✓, each with its catalog code where the rule raises one |
| Concurrency guards | 12 / 12 — stale version (TC-CAT-192) and the eleven parallel-request cases: RULE-CAT-015 (TC-CAT-200), 014 (201), 039 (202), 003 (203), 029 (204), 021 / 019 (205), 025 / 023 (211, the Category tree lock), 032 (227, the scale-prefix lock QR-CAT-074), 051 / 031 (228, the Item lock of ADR-CAT-060), 017 (229, the QR-CAT-031 FOR SHARE read), 025 / 026 (230, the QR-CAT-032 FOR SHARE read), each showing exactly one request succeeding, naming the refusal code, the guard and its owning unit |
| XM edges covered | 0 / 0 — no edge, so the `INT-XM` phase is absent |
| Track size | 166 TCs for 165 ACs (TC-CAT-223 covers the malformed counterparts of nine ACs; TC-CAT-225 is a second case of AC-CAT-007) (ratio 1.0, within the ~2× guard; TC-CAT-185 … 187 and 212 each cover more than one AC). No boundary twin is added; every BOUNDARY-class TC is the AC's own case. |
| Host data | CAT_BRAND BRAND_068 (TC-CAT-068, created active by `POST /api/v1/mdl/lookup-types/{id}/values`), CAT_BRAND OLDBRAND (TC-CAT-037, created by the same call, then made inactive by `DELETE /api/v1/mdl/lookup-values/{id}`); CAT_BRAND XYZ absent (TC-CAT-019, 036: never created). The AC values CAT_UOM BAG (AC-CAT-122 … 124) and CAT_EINVOICE_UNIT H87 (AC-CAT-126) are not seeded; TC-CAT-120, 121, 122 stay on seeded data with the unit DOZEN and TC-CAT-063 with the e-invoice unit BX in their place (ADR-CAT-053 §2). No value is demanded in opposite states, and every TC runs in its own tenant (ADR-CAT-052 §3, §4). |
