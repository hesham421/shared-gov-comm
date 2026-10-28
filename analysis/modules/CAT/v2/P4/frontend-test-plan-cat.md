# Frontend test plan — Catalog / الكتالوج (CAT) — v2

Module : CAT · Version : v2 · Stage : P4 · Track : frontend · Plan : test · Profile : commerce
Lane   : test-gen · questions forbidden · TC stream `TC-CAT-{seq:3}` shared with the backend test plan (backend: TC-CAT-001 … TC-CAT-124; this file: TC-CAT-125 … TC-CAT-182)

## Header

### Sources (generated current state)
| Input | File | Version | Use |
|---|---|---|---|
| srs | `_state/current-srs.md` | CAT v2 (P1) | derivation source: AC-CAT-*, RULE-CAT-* messages, lookups block with labels (A6) |
| registry-srs | `_state/current-registry-srs.md` | CAT v2 (P1) | ID ranges |
| registry-db | `_state/current-registry-db.md` | CAT v2 (P2) | no XM |
| backend-execution-plan | `_state/current-backend-execution-plan.md` | CAT v2 (P3.1) | the Error Catalog codes the screens route |
| frontend-execution-plan | `_state/current-frontend-execution-plan.md` | CAT v2 (P3.2) | SCR-CAT-001 … 006, routes, F2 code routing (`text:` bindings), F3 validators, F4 composition, UXD-CAT-001 … 003, split units (Package) |
| api-spec | `_state/current-api-spec.yaml` (`api-spec-cat.yaml`, OpenAPI 3.1.0, 2.0.0) | CAT v2 (P3.1) | the document the frontend mock server serves; shapes read by `x-api-id` |
| dependency-graph | `_state/current-dependency-graph.md` | CAT v2 | CAT tier 1 |

### Delta
The current state holds no earlier test plan, so every TC is ADDED. The frontend part of the TC stream continues from TC-CAT-124, the backend plan's last TC.

### Framework
`profile.stack.testing` is **agnostic**. Each block is the whole contract. This plan names no framework, runner, selector strategy or file layout.

### Open ADRs
- ADR-CAT-052 (this stage) is ACCEPTED and non-breaking. It covers the track split, frontend package binding, host data, option-set isolation, fields the API document does not expose, and UXD integration.
- ADR-CAT-053 (this stage, analyze round 2) is ACCEPTED and non-breaking. It records the eight server codes the frontend plan routes to a control without binding their words (`text:`): TC-CAT-130, 143, 144, 145, 150, 157, 165 and 171 assert the routing and the verbatim display of the envelope's messageAr / messageEn, not the words, which the backend TCs assert. It also replaces the unseeded AC value H87 by a seeded value in TC-CAT-175.
- The TCs also cite ADR-CAT-043, 048, 049, 050 and 051. None of them is BLOCKED.

### Shared precondition F0 (applies to every TC; each TC adds only what it states)
- The CAT frontend build is served in the platform shell, with the bound reads answered by the mock server of `api-spec-cat.yaml`, or by the delivered backend.
- The user is a preset Owner holding every `PERM_CAT_*` permission.
- The platform app-config reports `variants` and `bundles` on.
- The given erp-core lookup client serves the seeded CAT_* values with their SRS A6 labels.
- The TC runs in a tenant of its own (ADR-CAT-052 §4).
- Every asserted message is the RULE message of the SRS in ar and en, as the plan routes it (`text: RULE-CAT-0nn message`). It is checked once with the UI language in ar and once in en (language ALL). Where the plan routes a code to a control without binding its words, the TC asserts only the routing and the unchanged display of the envelope's messageAr / messageEn; the wording is asserted on the backend track (ADR-CAT-053 §1).
- "No request" means the mock server records no call to the named operation.

<!-- PHASE:TEST-PLAN-FE:START traces=AC-CAT-003,AC-CAT-005,AC-CAT-007,AC-CAT-013,AC-CAT-014,AC-CAT-015,AC-CAT-016,AC-CAT-020,AC-CAT-023,AC-CAT-025,AC-CAT-027,AC-CAT-028,AC-CAT-031,AC-CAT-032,AC-CAT-037,AC-CAT-040,AC-CAT-041,AC-CAT-044,AC-CAT-047,AC-CAT-050,AC-CAT-054,AC-CAT-056,AC-CAT-057,AC-CAT-066,AC-CAT-069,AC-CAT-070,AC-CAT-071,AC-CAT-072,AC-CAT-073,AC-CAT-075,AC-CAT-076,AC-CAT-078,AC-CAT-082,AC-CAT-088,AC-CAT-090,AC-CAT-092,AC-CAT-094,AC-CAT-097,AC-CAT-100,AC-CAT-102,AC-CAT-103,AC-CAT-104,AC-CAT-105,AC-CAT-106,AC-CAT-108,AC-CAT-112,AC-CAT-118,AC-CAT-125,AC-CAT-126,AC-CAT-127,AC-CAT-128,AC-CAT-129,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-007,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-019,REQ-CAT-022,REQ-CAT-024,REQ-CAT-026,REQ-CAT-027,REQ-CAT-030,REQ-CAT-031,REQ-CAT-036,REQ-CAT-039,REQ-CAT-040,REQ-CAT-042,REQ-CAT-045,REQ-CAT-048,REQ-CAT-052,REQ-CAT-054,REQ-CAT-055,REQ-CAT-064,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-073,REQ-CAT-074,REQ-CAT-076,REQ-CAT-080,REQ-CAT-086,REQ-CAT-088,REQ-CAT-090,REQ-CAT-092,REQ-CAT-095,REQ-CAT-098,REQ-CAT-100,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-106,REQ-CAT-110,REQ-CAT-116,REQ-CAT-123,REQ-CAT-124,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127 -->
## PHASE TEST-PLAN-FE — module scenarios

The phase holds 52 TCs, which is more than 8, so it is split into two SUBs:
- `UI-FLOWS` (51): per-screen flows — search, create/edit, a violation shown on screen, a permission-hidden affordance.
- `INT-FLOW` (1): the single module lifecycle flow.

<!-- SUB:UI-FLOWS:START traces=AC-CAT-003,AC-CAT-007,AC-CAT-013,AC-CAT-014,AC-CAT-015,AC-CAT-016,AC-CAT-020,AC-CAT-023,AC-CAT-025,AC-CAT-027,AC-CAT-028,AC-CAT-031,AC-CAT-032,AC-CAT-037,AC-CAT-040,AC-CAT-041,AC-CAT-044,AC-CAT-047,AC-CAT-050,AC-CAT-054,AC-CAT-056,AC-CAT-057,AC-CAT-066,AC-CAT-069,AC-CAT-070,AC-CAT-071,AC-CAT-072,AC-CAT-073,AC-CAT-075,AC-CAT-076,AC-CAT-078,AC-CAT-082,AC-CAT-088,AC-CAT-090,AC-CAT-092,AC-CAT-094,AC-CAT-097,AC-CAT-100,AC-CAT-102,AC-CAT-103,AC-CAT-104,AC-CAT-105,AC-CAT-106,AC-CAT-108,AC-CAT-112,AC-CAT-118,AC-CAT-125,AC-CAT-126,AC-CAT-127,AC-CAT-128,AC-CAT-129,REQ-CAT-003,REQ-CAT-007,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-019,REQ-CAT-022,REQ-CAT-024,REQ-CAT-026,REQ-CAT-027,REQ-CAT-030,REQ-CAT-031,REQ-CAT-036,REQ-CAT-039,REQ-CAT-040,REQ-CAT-042,REQ-CAT-045,REQ-CAT-048,REQ-CAT-052,REQ-CAT-054,REQ-CAT-055,REQ-CAT-064,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-073,REQ-CAT-074,REQ-CAT-076,REQ-CAT-080,REQ-CAT-086,REQ-CAT-088,REQ-CAT-090,REQ-CAT-092,REQ-CAT-095,REQ-CAT-098,REQ-CAT-100,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,REQ-CAT-106,REQ-CAT-110,REQ-CAT-116,REQ-CAT-123,REQ-CAT-124,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127 -->
### SUB UI-FLOWS

#### SCR-CAT-001 — Items

<!-- TC:TC-CAT-125:START traces=AC-CAT-003,REQ-CAT-003,SCR-CAT-001 -->
### TC-CAT-125 — Filtering Items by Category shows a sorted first page
Derived from : AC-CAT-003  (REQ-CAT-003)
Exercises    : SCR-CAT-001 `/cat/items` (ITEM-SEARCH-QUERY → API-CAT-038; CATEGORY-OPTIONS-QUERY → API-CAT-039)
Rule / code  : —
Package      : F2-SCR-CAT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · 30 active Items, of which 12 belong to the Category «هواتف»
Host data    : none
Steps        : 1. Open `/cat/items`. 2. Choose «هواتف» in the Category filter. 3. Set the page size to 10.
Expected     : The URL carries `categoryFk=<«هواتف»>&page=0&size=10`. The results table shows 10 rows and a total of 12, sorted by nameAr ascending, and the Category column reads «هواتف».
Test data    : 30 / 12 Items, «هواتف», page size 10 (from the AC)
<!-- TC:TC-CAT-125:END -->

<!-- TC:TC-CAT-126:START traces=AC-CAT-007,REQ-CAT-007,SCR-CAT-001 -->
### TC-CAT-126 — A duplicate Arabic name is shown on the nameAr field with its next action
Derived from : AC-CAT-007  (REQ-CAT-007)
Exercises    : SCR-CAT-001 `/cat/items/new` (ITEM-SAVE → API-CAT-001)
Rule / code  : RULE-CAT-003 → CAT-409-ITEM-NAME-DUPLICATE
Package      : F2-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · an active Item named «شاحن سامسونج 25W» · API-CAT-001 answers 409 CAT-409-ITEM-NAME-DUPLICATE for that name
Host data    : none
Steps        : 1. Open `/cat/items/new`. 2. Enter nameAr «شاحن سامسونج 25W». 3. Save. 4. Use the action "Open the existing item".
Expected     : 3 → inline on nameAr: ar «يوجد صنف نشط بالاسم نفسه. افتح الصنف الموجود أو غيّر الاسم.» · en "An active item already has this name. Open the existing item or change the name."; the route stays `/cat/items/new` (no Item created). 4 → navigates to `/cat/items?text=شاحن سامسونج 25W&isActiveFl=true`, which lists the existing Item.
Test data    : «شاحن سامسونج 25W» (from the AC)
<!-- TC:TC-CAT-126:END -->

<!-- TC:TC-CAT-127:START traces=AC-CAT-013,REQ-CAT-013,SCR-CAT-001,UXD-CAT-002 -->
### TC-CAT-127 — An unreadable capability flag on save shows the retry banner
Derived from : AC-CAT-013  (REQ-CAT-013)
Exercises    : SCR-CAT-001 `/cat/items/new` (ITEM-SAVE → API-CAT-001)
Rule / code  : RULE-CAT-009 → CAT-500-CAPABILITY-UNREADABLE
Package      : F2-SCR-CAT-001
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: F0 (app-config reports `bundles` on) · the server's settings read does not answer `bundles`, so API-CAT-001 answers 500 CAT-500-CAPABILITY-UNREADABLE · an active STANDARD Item with an active Variant
Host data    : none
Steps        : 1. Open `/cat/items/new`. 2. Enter a valid nameAr and choose Item kind BUNDLE. 3. Add one component line in the Bundle component picker. 4. Save.
Expected     : A form banner with "Try again": ar «تعذّر التحقق من إعدادات القدرات الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.» · en "Capability settings could not be checked, and nothing was saved. Try again in a moment.". The route stays `/cat/items/new`, so no record was saved.
Test data    : itemKindCode BUNDLE (from the AC); nameAr placeholder
<!-- TC:TC-CAT-127:END -->

<!-- TC:TC-CAT-128:START traces=AC-CAT-014,REQ-CAT-014,SCR-CAT-001,SCR-CAT-003,UXD-CAT-002 -->
### TC-CAT-128 — With `variants` off, no Variant function is shown
Derived from : AC-CAT-014  (REQ-CAT-014)
Exercises    : SCR-CAT-001 `/cat/items`, `/cat/items/:itemPk` · the Catalog menu (SCR-CAT-003 entry)
Rule / code  : —
Package      : F4-SCR-CAT-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: F0, except that app-config reports `variants` off and the user is a Seller/cashier (PERM_CAT_ITEMS_VIEW, PERM_CAT_VARIANT_ATTRIBUTES_VIEW) · an existing Item
Host data    : none
Steps        : 1. Open the Catalog menu. 2. Open the Items screen and the existing Item.
Expected     : 1 → the menu holds no "Variant attributes" entry. 2 → the Items screen shows 0 Variant attribute fields and 0 Variant list sections: `VariantsPane` is absent and no generation trigger exists.
Test data    : —
<!-- TC:TC-CAT-128:END -->

<!-- TC:TC-CAT-129:START traces=AC-CAT-015,REQ-CAT-015,SCR-CAT-001,UXD-CAT-002 -->
### TC-CAT-129 — With `bundles` off, the Item kind and Bundle components are absent
Derived from : AC-CAT-015  (REQ-CAT-015)
Exercises    : SCR-CAT-001 `/cat/items/new`, `/cat/items`
Rule / code  : —
Package      : F4-SCR-CAT-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: F0, except that app-config reports `bundles` off
Host data    : none
Steps        : 1. Open `/cat/items/new` (the Item entry). 2. Open `/cat/items` (the Item search).
Expected     : 1 → no Item kind field and 0 Bundle component sections. 2 → no Item kind filter; the URL never carries `itemKindCode`.
Test data    : —
<!-- TC:TC-CAT-129:END -->

<!-- TC:TC-CAT-130:START traces=AC-CAT-020,REQ-CAT-019,SCR-CAT-001 -->
### TC-CAT-130 — Generating Variants shows the "kept their barcodes" notice
Derived from : AC-CAT-020  (REQ-CAT-019)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk?picker=variant-generation` (ATTRIBUTE-OPTIONS-QUERY → API-CAT-041; ITEM-SAVE step 5 → API-CAT-006; ITEM-QUERY → API-CAT-002)
Rule / code  : RULE-CAT-012 → CAT-201-EXISTING-VARIANTS-KEPT (success notice)
Package      : F2-SCR-CAT-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: F0 · a STANDARD Item whose default Variant carries the active GTIN 6223000000014 · an active attribute with 3 active values
Host data    : none
Steps        : 1. Open the Item. 2. Open "Generate variants" and select the 3 values. 3. Confirm (a pending-generation summary row appears in `VariantsPane`). 4. Save.
Expected     : A success notice is shown for the envelope code CAT-201-EXISTING-VARIANTS-KEPT, displaying the envelope's messageAr / messageEn unchanged (the frontend plan routes this code without binding its words, so its wording is asserted on the backend by TC-CAT-009 — ADR-CAT-053 §1). After the re-read, `BarcodesPane` shows 6223000000014 under the default Variant, and the 2 new Variants have no barcode line.
Test data    : GTIN 6223000000014 (from the AC); values placeholders `<value-1..3>`
<!-- TC:TC-CAT-130:END -->

<!-- TC:TC-CAT-131:START traces=AC-CAT-025,REQ-CAT-024,SCR-CAT-001 -->
### TC-CAT-131 — Saving a new Item with only its Arabic name
Derived from : AC-CAT-025  (REQ-CAT-024)
Exercises    : SCR-CAT-001 `/cat/items/new` → `/cat/items/:itemPk` (ITEM-SAVE → API-CAT-001)
Rule / code  : —
Package      : F2-SCR-CAT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · no active Item named «كوب زجاج»
Host data    : none
Steps        : 1. Open `/cat/items/new`. 2. Enter only nameAr «كوب زجاج». 3. Save.
Expected     : The route becomes `/cat/items/:itemPk`. The header shows Item kind STANDARD, base unit PIECE, tax class STANDARD (labels from the lookups), the publish badge "Not published" (UNPUBLISHED) and the active state.
Test data    : «كوب زجاج» (from the AC)
<!-- TC:TC-CAT-131:END -->

<!-- TC:TC-CAT-132:START traces=AC-CAT-027,REQ-CAT-026,SCR-CAT-001 -->
### TC-CAT-132 — A new Item entry has 4 mandatory fields, 3 of them pre-filled
Derived from : AC-CAT-027  (REQ-CAT-026)
Exercises    : SCR-CAT-001 `/cat/items/new`
Rule / code  : —
Package      : F3-SCR-CAT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 (`bundles` on)
Host data    : none
Steps        : 1. Open `/cat/items/new`.
Expected     : Exactly 4 mandatory fields appear outside "Advanced": nameAr (empty), baseUomCode, taxClassCode and itemKindCode. The 3 pre-filled ones hold PIECE, STANDARD and STANDARD. "Advanced" is collapsed.
Test data    : PIECE, STANDARD, STANDARD (from the AC)
<!-- TC:TC-CAT-132:END -->

<!-- TC:TC-CAT-133:START traces=AC-CAT-028,REQ-CAT-027,SCR-CAT-001 -->
### TC-CAT-133 — An empty Arabic name is refused before any request
Derived from : AC-CAT-028  (REQ-CAT-027)
Exercises    : SCR-CAT-001 `/cat/items/new` (`ItemForm` validator)
Rule / code  : RULE-CAT-001 (client validator; server code CAT-422-NAME-AR-INVALID)
Package      : F3-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0
Host data    : none
Steps        : 1. Open `/cat/items/new` and leave nameAr empty. 2. Save.
Expected     : Inline on nameAr: ar «أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.» · en "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."; no request to API-CAT-001, so no Item is created.
Test data    : nameAr empty (from the AC)
<!-- TC:TC-CAT-133:END -->

<!-- TC:TC-CAT-134:START traces=AC-CAT-031,REQ-CAT-030,SCR-CAT-001 -->
### TC-CAT-134 — A Latin-only Arabic name is refused before any request
Derived from : AC-CAT-031  (REQ-CAT-030)
Exercises    : SCR-CAT-001 `/cat/items/new` (`ItemForm` validator)
Rule / code  : RULE-CAT-001 (client validator)
Package      : F3-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0
Host data    : none
Steps        : 1. Enter nameAr "Galaxy A55". 2. Save.
Expected     : Inline on nameAr: ar «أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.» · en "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."; no request to API-CAT-001.
Test data    : "Galaxy A55" (from the AC)
<!-- TC:TC-CAT-134:END -->

<!-- TC:TC-CAT-135:START traces=AC-CAT-032,REQ-CAT-031,SCR-CAT-001 -->
### TC-CAT-135 — An English name of 201 characters is refused before any request
Derived from : AC-CAT-032  (REQ-CAT-031)
Exercises    : SCR-CAT-001 `/cat/items/new` (`ItemForm` validator, Advanced section)
Rule / code  : RULE-CAT-002 (client validator)
Package      : F3-SCR-CAT-001
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: F0
Host data    : none
Steps        : 1. Enter a valid nameAr. 2. Open Advanced and paste a nameEn of 201 characters. 3. Save.
Expected     : Inline on nameEn: ar «الاسم بالإنجليزية لا يتجاوز 200 حرف.» · en "The English name can have at most 200 characters."; no request to API-CAT-001.
Test data    : nameEn `<201 characters>` (from the AC)
<!-- TC:TC-CAT-135:END -->

<!-- TC:TC-CAT-136:START traces=AC-CAT-054,REQ-CAT-052,SCR-CAT-001 -->
### TC-CAT-136 — A generation that would pass 300 active Variants is stopped in the picker
Derived from : AC-CAT-054  (REQ-CAT-052)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk?picker=variant-generation` (generation line validator)
Rule / code  : RULE-CAT-014 (client validator; server code CAT-422-VARIANT-LIMIT-EXCEEDED)
Package      : F3-SCR-CAT-001
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: F0 · a STANDARD Item with 290 active Variants · active values giving 20 new combinations
Host data    : none
Steps        : 1. Open "Generate variants". 2. Select the values giving 20 new combinations. 3. Confirm.
Expected     : The picker shows ar «الحد الأقصى 3 خصائص و300 متغيّر نشط للصنف الواحد.» · en "An item can have at most 3 attributes and 300 active variants."; Confirm returns nothing to the form, no request to API-CAT-006 is made, and the Item still shows 290 Variants.
Test data    : 290 + 20 (from the AC)
<!-- TC:TC-CAT-136:END -->

<!-- TC:TC-CAT-137:START traces=AC-CAT-075,REQ-CAT-073,SCR-CAT-001,UXD-CAT-001 -->
### TC-CAT-137 — The brand field offers only the active CAT_BRAND values
Derived from : AC-CAT-075  (REQ-CAT-073)
Exercises    : SCR-CAT-001 `/cat/items/new` (CAT_BRAND-LOOKUP, the given lookup client — UXD-CAT-001)
Rule / code  : —
Package      : F2-SCR-CAT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · the tenant's CAT_BRAND holds only this case's 6 host values: 5 active and 1 inactive (ADR-CAT-052 §4)
Host data    : CAT_BRAND BRAND1_137, BRAND2_137, BRAND3_137, BRAND4_137, BRAND5_137 — active; CAT_BRAND BRAND6_137 — inactive — each created by the given erp-core mdl lookup-value maintenance (com.erp.mdl.crossmodule.MdlLookupApi; CAT publishes no add-value verb/path — ADR-CAT-052 §3)
Steps        : 1. Open `/cat/items/new` and Advanced. 2. Open the brand field.
Expected     : Exactly the 5 active values are offered: BRAND1_137 … BRAND5_137. BRAND6_137 is not offered.
Test data    : the AC names no brand: 5 + 1 own host values (`<VALUE>_<TC seq>`)
<!-- TC:TC-CAT-137:END -->

<!-- TC:TC-CAT-138:START traces=AC-CAT-076,REQ-CAT-074,SCR-CAT-001,UXD-CAT-001 -->
### TC-CAT-138 — The base unit field offers the 8 seeded units with PIECE pre-selected
Derived from : AC-CAT-076  (REQ-CAT-074)
Exercises    : SCR-CAT-001 `/cat/items/new` (CAT_UOM-LOOKUP — UXD-CAT-001)
Rule / code  : —
Package      : F2-SCR-CAT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · the tenant's CAT_UOM holds only the 8 seeded values, all active; no value of another case is added to it (ADR-CAT-052 §4)
Host data    : none
Steps        : 1. Open `/cat/items/new`. 2. Open the base unit field.
Expected     : 8 values are offered — PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE, LITRE, labelled ar «قطعة · كرتونة · علبة · دستة · كيلوجرام · جرام · متر · لتر» / en "Piece · Carton · Box · Dozen · Kilogram · Gram · Metre · Litre" — with PIECE pre-selected.
Test data    : the 8 seeded CAT_UOM values (from the AC and SRS A6)
<!-- TC:TC-CAT-138:END -->

<!-- TC:TC-CAT-139:START traces=AC-CAT-078,REQ-CAT-076,SCR-CAT-001,UXD-CAT-001 -->
### TC-CAT-139 — A deactivated brand stays on its Items and is no longer offered
Derived from : AC-CAT-078  (REQ-CAT-076)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk`, `/cat/items/new` (ITEM-QUERY → API-CAT-002; CAT_BRAND-LOOKUP — UXD-CAT-001)
Rule / code  : —
Package      : F2-SCR-CAT-001
Scenario     : STATE · data class EDGE · language ALL
Preconditions: F0 · 6 Items with brandCode OLDBRAND, then OLDBRAND deactivated in the core lookup module
Host data    : CAT_BRAND OLDBRAND — active when the 6 Items are created, then inactive — created and deactivated by the given erp-core mdl lookup-value maintenance (com.erp.mdl.crossmodule.MdlLookupApi — ADR-CAT-052 §3)
Steps        : 1. Open one of the 6 Items. 2. Open `/cat/items/new` and its brand field.
Expected     : 1 → the brand field shows OLDBRAND (its label kept although inactive). 2 → the brand field offers 0 entries named OLDBRAND.
Test data    : OLDBRAND, 6 Items (from the AC)
<!-- TC:TC-CAT-139:END -->

<!-- TC:TC-CAT-140:START traces=AC-CAT-082,REQ-CAT-080,SCR-CAT-001 -->
### TC-CAT-140 — An Item unit equal to the base unit is refused in the unit drawer
Derived from : AC-CAT-082  (REQ-CAT-080)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk?drawer=unit:new` (Item unit line validator)
Rule / code  : RULE-CAT-027 (client validator; server code CAT-422-ITEM-UNIT-INVALID)
Package      : F3-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · an Item with base unit PIECE
Host data    : none
Steps        : 1. Open "Add unit". 2. Choose uomCode PIECE and enter conversionFactor 1. 3. Confirm.
Expected     : Inline in the drawer: ar «الوحدة يجب أن تختلف عن الوحدة الأساسية وألا تتكرر، ومعامل التحويل أكبر من صفر (حتى 6 أرقام عشرية).» · en "The unit must differ from the base unit and appear once, with a conversion factor above zero (up to 6 decimals)."; the line is not returned to the form and nothing is saved (no request to API-CAT-010).
Test data    : PIECE, 1 (from the AC)
<!-- TC:TC-CAT-140:END -->

<!-- TC:TC-CAT-141:START traces=AC-CAT-088,REQ-CAT-086,SCR-CAT-001 -->
### TC-CAT-141 — "Find by barcode" opens the Item on the scanned Variant
Derived from : AC-CAT-088  (REQ-CAT-086)
Exercises    : SCR-CAT-001 `/cat/items` → `/cat/items/:itemPk?variant=<variantPk>` (BARCODE-LOOKUP-QUERY → API-CAT-043)
Rule / code  : —
Package      : F2-SCR-CAT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0, with the user a Seller/cashier · the active barcode 16223000000011 bound to unit CARTON of the Variant VAR-000200
Host data    : none
Steps        : 1. Open `/cat/items`. 2. Scan 16223000000011 into "Find by barcode" (the scanner's suffix submits it).
Expected     : The screen navigates to `/cat/items/:itemPk?variant=<pk of VAR-000200>`: the Item of VAR-000200, with VAR-000200 selected.
Test data    : 16223000000011, VAR-000200, CARTON (from the AC)
<!-- TC:TC-CAT-141:END -->

<!-- TC:TC-CAT-142:START traces=AC-CAT-090,REQ-CAT-088,SCR-CAT-001 -->
### TC-CAT-142 — A GTIN with a wrong check digit is refused in the barcode drawer
Derived from : AC-CAT-090  (REQ-CAT-088)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk?drawer=barcode:new&variant=<variantPk>` (barcode line validator)
Rule / code  : RULE-CAT-030 (client validator; server code CAT-422-GTIN-INVALID)
Package      : F3-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · an Item with an active Variant
Host data    : none
Steps        : 1. Open "Add barcode" on the Variant. 2. Choose type GTIN and enter 6223000000015. 3. Confirm.
Expected     : Inline on barcodeValue: ar «باركود GTIN يجب أن يتكون من 8 أو 12 أو 13 أو 14 رقمًا برقم تحقق صحيح.» · en "A GTIN barcode must have 8, 12, 13 or 14 digits with a valid check digit."; the line is not returned and nothing is saved.
Test data    : 6223000000015 (from the AC)
<!-- TC:TC-CAT-142:END -->

<!-- TC:TC-CAT-143:START traces=AC-CAT-092,REQ-CAT-090,SCR-CAT-001 -->
### TC-CAT-143 — An unknown barcode shows the "search by name" next action
Derived from : AC-CAT-092  (REQ-CAT-090)
Exercises    : SCR-CAT-001 `/cat/items` (BARCODE-LOOKUP-QUERY → API-CAT-043)
Rule / code  : RULE-CAT-047 → CAT-404-BARCODE-NOT-FOUND
Package      : F2-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0, with the user a Seller/cashier · no active barcode 6229999999995 and no layout with prefix 62
Host data    : none
Steps        : 1. Scan 6229999999995 into "Find by barcode".
Expected     : The 404 CAT-404-BARCODE-NOT-FOUND is shown under "Find by barcode", displaying the envelope's messageAr / messageEn unchanged (the frontend plan routes this code without binding its words, so its wording is asserted on the backend by TC-CAT-046 — ADR-CAT-053 §1); no Variant opens, the input keeps 6229999999995, and focus moves to the text filter.
Test data    : 6229999999995 (from the AC)
<!-- TC:TC-CAT-143:END -->

<!-- TC:TC-CAT-144:START traces=AC-CAT-102,REQ-CAT-100,SCR-CAT-001 -->
### TC-CAT-144 — An unreadable scale label is explained under "Find by barcode"
Derived from : AC-CAT-102  (REQ-CAT-100)
Exercises    : SCR-CAT-001 `/cat/items` (BARCODE-LOOKUP-QUERY → API-CAT-043)
Rule / code  : RULE-CAT-035 → CAT-422-SCALE-LABEL-UNREADABLE
Package      : F2-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0, with the user a Seller/cashier · the active layout with prefix 20 · no SCALE barcode 09999
Host data    : none
Steps        : 1. Scan 20 09999 01234 x (x = the valid EAN-13 check digit) into "Find by barcode".
Expected     : The 422 CAT-422-SCALE-LABEL-UNREADABLE is shown under "Find by barcode", displaying the envelope's messageAr / messageEn unchanged (the frontend plan routes this code without binding its words, so its wording is asserted on the backend by TC-CAT-051 — ADR-CAT-053 §1); no Variant opens, and the input keeps the scanned value.
Test data    : 20 09999 01234 x (from the AC)
<!-- TC:TC-CAT-144:END -->

<!-- TC:TC-CAT-145:START traces=AC-CAT-108,REQ-CAT-106,SCR-CAT-001 -->
### TC-CAT-145 — Choosing another main image shows the replacement notice
Derived from : AC-CAT-108  (REQ-CAT-106)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk?drawer=image:<itemMediaPk>` (ITEM-SAVE step 8 → API-CAT-019; ITEM-QUERY → API-CAT-002)
Rule / code  : RULE-CAT-039 → CAT-200-PRIMARY-IMAGE-REPLACED (success notice)
Package      : F2-SCR-CAT-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: F0 · an Item with active images A (primary) and B
Host data    : none
Steps        : 1. Open image B's drawer. 2. Set "Main image". 3. Confirm. 4. Save.
Expected     : A success notice is shown for the envelope code CAT-200-PRIMARY-IMAGE-REPLACED, displaying the envelope's messageAr / messageEn unchanged (the frontend plan routes this code without binding its words, so its wording is asserted on the backend by TC-CAT-054 — ADR-CAT-053 §1). After the re-read, B is marked main and A is not; exactly one image is marked main.
Test data    : images A, B (from the AC)
<!-- TC:TC-CAT-145:END -->

<!-- TC:TC-CAT-146:START traces=AC-CAT-112,REQ-CAT-110,SCR-CAT-001 -->
### TC-CAT-146 — A 7 MB JPG is refused in the image drawer
Derived from : AC-CAT-112  (REQ-CAT-110)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk?drawer=image:new` (image line validator)
Rule / code  : RULE-CAT-038 (client validator; server code CAT-422-IMAGE-NOT-ACCEPTED)
Package      : F3-SCR-CAT-001
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: F0 · an Item
Host data    : none
Steps        : 1. Open "Add image". 2. Choose a 7 MB JPG. 3. Confirm.
Expected     : Inline on the file: ar «الصورة يجب أن تكون JPG أو PNG أو WEBP بحجم لا يتجاوز 5 ميجابايت، وبحد أقصى 20 صورة نشطة للصنف.» · en "The image must be JPG, PNG or WEBP of at most 5 MB, with at most 20 active images per item."; no image line is added and no request is made to API-CAT-018, so no media record is created.
Test data    : a 7 MB JPG (from the AC)
<!-- TC:TC-CAT-146:END -->

<!-- TC:TC-CAT-147:START traces=AC-CAT-118,REQ-CAT-116,SCR-CAT-001 -->
### TC-CAT-147 — Publishing an inactive Item shows the command banner
Derived from : AC-CAT-118  (REQ-CAT-116)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (ITEM-STATE-COMMAND → API-CAT-005)
Rule / code  : RULE-CAT-041 → CAT-422-PUBLISH-BLOCKED
Package      : F2-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · an inactive Item · the form has no pending change
Host data    : none
Steps        : 1. Open the Item. 2. Choose Publish and confirm.
Expected     : A command banner: ar «لا يمكن نشر صنف غير نشط أو بلا متغيّر نشط. فعّل الصنف أو أحد متغيّراته أولًا.» · en "An inactive item, or one with no active variant, cannot be published. Activate the item or one of its variants first."; the publish badge stays "Not published" (UNPUBLISHED).
Test data    : —
<!-- TC:TC-CAT-147:END -->

<!-- TC:TC-CAT-148:START traces=AC-CAT-125,REQ-CAT-123,SCR-CAT-001 -->
### TC-CAT-148 — An EGS code not starting with EG- is refused on the form
Derived from : AC-CAT-125  (REQ-CAT-123)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (`ItemForm` validator, Advanced section)
Rule / code  : RULE-CAT-042 (client validator; server code CAT-422-EINVOICE-CODE-INVALID)
Package      : F3-SCR-CAT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · an Item with no e-invoice code
Host data    : none
Steps        : 1. Open the Item and Advanced. 2. Choose e-invoice code type EGS and enter «123». 3. Collapse Advanced. 4. Save.
Expected     : Advanced re-opens. Inline on einvoiceCode: ar «أدخل نوع رمز الفاتورة الإلكترونية والرمز معًا: GS1 من 8 إلى 14 رقمًا، وEGS يبدأ بـ EG- ولا يتجاوز 100 حرف.» · en "Enter the e-invoice code type and code together: GS1 is 8 to 14 digits, EGS starts with EG- and has at most 100 characters."; no request to API-CAT-003, so nothing is saved.
Test data    : EGS «123» (from the AC)
<!-- TC:TC-CAT-148:END -->

<!-- TC:TC-CAT-149:START traces=AC-CAT-127,REQ-CAT-125,SCR-CAT-001 -->
### TC-CAT-149 — The catalogue group of the global search caps at 20 and ranks the exact code first
Derived from : AC-CAT-127  (REQ-CAT-125)
Exercises    : SCR-CAT-001 exported `catalogueSearchProvider` in the shell's global search (CATALOGUE-SEARCH-QUERY → API-CAT-047) → `/cat/items/:itemPk`
Rule / code  : —
Package      : F2-SCR-CAT-001
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: F0, with the user a Seller/cashier (PERM_CAT_ITEMS_VIEW) · 25 active Items whose nameAr contains «شاحن», one with code ITM-000077
Host data    : none
Steps        : 1. Type «شاحن» in the global search. 2. Type «ITM-000077». 3. Choose the first hit.
Expected     : 1 → the catalogue group shows 20 Items. 2 → ITM-000077 is in position 1, in server order. 3 → `/cat/items/:itemPk` of ITM-000077 opens.
Test data    : «شاحن», ITM-000077, 25 Items (from the AC)
<!-- TC:TC-CAT-149:END -->

<!-- TC:TC-CAT-150:START traces=AC-CAT-128,REQ-CAT-126,SCR-CAT-001 -->
### TC-CAT-150 — A one-character term shows the hint and sends nothing
Derived from : AC-CAT-128  (REQ-CAT-126)
Exercises    : SCR-CAT-001 `catalogueSearchProvider` (CATALOGUE-SEARCH-QUERY)
Rule / code  : RULE-CAT-046 → CAT-400-SEARCH-TERM-TOO-SHORT (routed to the global search box)
Package      : F2-SCR-CAT-001
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: F0, with the user a Seller/cashier
Host data    : none
Steps        : 1. Type «ش» in the global search.
Expected     : The too-short hint the plan routes for RULE-CAT-046 is shown in the global search box; 0 catalogue results; no request to API-CAT-047. The hint's wording is not asserted here: the frontend plan routes the rule without binding its words (ADR-CAT-053 §1); the wording is asserted on the backend by TC-CAT-064.
Test data    : «ش» (from the AC)
<!-- TC:TC-CAT-150:END -->

<!-- TC:TC-CAT-151:START traces=AC-CAT-129,REQ-CAT-127,SCR-CAT-001 -->
### TC-CAT-151 — Without VIEW on CAT_ITEMS the global search has no catalogue group
Derived from : AC-CAT-129  (REQ-CAT-127)
Exercises    : SCR-CAT-001 `catalogueSearchProvider` registration; route `/cat/items` guard
Rule / code  : — (PERM_CAT_ITEMS_VIEW absent)
Package      : SEC-FE
Scenario     : PERMISSION · data class VALID · language ALL
Preconditions: F0, except that the user holds a custom role without PERM_CAT_ITEMS_VIEW · active Items whose nameAr contains «شاحن»
Host data    : none
Steps        : 1. Type «شاحن» in the global search. 2. Open the Catalog menu. 3. Navigate to `/cat/items` directly.
Expected     : 1 → the catalogue contributes 0 results: the provider is not registered, no catalogue group is shown, and no request goes to API-CAT-047. 2 → no "Items" entry. 3 → redirect to the unauthorized view.
Test data    : «شاحن» (from the AC)
<!-- TC:TC-CAT-151:END -->

#### SCR-CAT-002 — Categories

<!-- TC:TC-CAT-152:START traces=AC-CAT-066,REQ-CAT-064,SCR-CAT-002 -->
### TC-CAT-152 — Creating PHONES under «إلكترونيات» from the tree
Derived from : AC-CAT-066  (REQ-CAT-064)
Exercises    : SCR-CAT-002 `/cat/categories?node=new&parent=<«إلكترونيات»>` (CATEGORY-SAVE → API-CAT-021; CATEGORY-TREE-QUERY → API-CAT-039)
Rule / code  : —
Package      : F2-SCR-CAT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · the active top-level Category «إلكترونيات»
Host data    : none
Steps        : 1. On «إلكترونيات», choose "New sub-category". 2. Enter code PHONES and nameAr «هواتف». 3. Save.
Expected     : The tree shows PHONES «هواتف» as an active child of «إلكترونيات»; re-opening the node shows parent «إلكترونيات».
Test data    : PHONES, «هواتف», «إلكترونيات» (from the AC)
<!-- TC:TC-CAT-152:END -->

<!-- TC:TC-CAT-153:START traces=AC-CAT-069,REQ-CAT-067,SCR-CAT-002 -->
### TC-CAT-153 — The Category tree shows children under their parents in sortOrder
Derived from : AC-CAT-069  (REQ-CAT-067)
Exercises    : SCR-CAT-002 `/cat/categories` (`CategoryTree`, CATEGORY-TREE-QUERY → API-CAT-039)
Rule / code  : —
Package      : F4-SCR-CAT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · exactly 2 top-level Categories; the lower sortOrder one has 3 sub-categories, the other 0
Host data    : none
Steps        : 1. Open `/cat/categories`.
Expected     : 2 root nodes in sortOrder; the first expands to 3 children; the second has no children.
Test data    : 2 roots, 3 / 0 children (from the AC)
<!-- TC:TC-CAT-153:END -->

<!-- TC:TC-CAT-154:START traces=AC-CAT-070,REQ-CAT-068,SCR-CAT-002 -->
### TC-CAT-154 — "Items of this category" counts the active sub-tree
Derived from : AC-CAT-070  (REQ-CAT-068)
Exercises    : SCR-CAT-002 `/cat/categories?node=<pk>&items=open` (CATEGORY-ITEMS-QUERY → API-CAT-040)
Rule / code  : —
Package      : F2-SCR-CAT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · «إلكترونيات» holding 2 active Items; its sub-category PHONES holding 5 active Items and 1 inactive Item
Host data    : none
Steps        : 1. Open the node «إلكترونيات». 2. Choose "Show" on the "Items of this category" row.
Expected     : 1 → the summary row shows a count of 7. 2 → `CategoryItemsDrawer` lists 7 Items, and the inactive one is not among them.
Test data    : 2 + 5 active, 1 inactive (from the AC)
<!-- TC:TC-CAT-154:END -->

<!-- TC:TC-CAT-155:START traces=AC-CAT-071,REQ-CAT-069,SCR-CAT-002 -->
### TC-CAT-155 — A sub-category cannot be chosen as the parent of its parent
Derived from : AC-CAT-071  (REQ-CAT-069)
Exercises    : SCR-CAT-002 `/cat/categories?node=<«إلكترونيات»>` (`CategoryForm` parent validator; PARENT-OPTIONS-QUERY)
Rule / code  : RULE-CAT-023 (client validator; server code CAT-422-CATEGORY-PARENT-INVALID)
Package      : F3-SCR-CAT-002
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · PHONES is a sub-category of «إلكترونيات»
Host data    : none
Steps        : 1. Open the node «إلكترونيات». 2. Open Advanced and the parent select. 3. Attempt to set PHONES as parent and Save.
Expected     : PHONES is offered disabled. If parentCategoryFk is set to PHONES, the field shows ar «الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.» · en "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels."; no request to API-CAT-023, so nothing is saved.
Test data    : PHONES, «إلكترونيات» (from the AC)
<!-- TC:TC-CAT-155:END -->

<!-- TC:TC-CAT-156:START traces=AC-CAT-072,REQ-CAT-070,SCR-CAT-002 -->
### TC-CAT-156 — A second «هواتف» under the same parent is refused on code and name
Derived from : AC-CAT-072  (REQ-CAT-070)
Exercises    : SCR-CAT-002 `/cat/categories?node=new&parent=<«إلكترونيات»>` (CATEGORY-SAVE → API-CAT-021)
Rule / code  : RULE-CAT-024 → CAT-409-CATEGORY-DUPLICATE
Package      : F2-SCR-CAT-002
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · an active «هواتف» under «إلكترونيات»
Host data    : none
Steps        : 1. Create a sub-category of «إلكترونيات» with an unused code and nameAr «هواتف». 2. Save.
Expected     : Inline on code and nameAr: ar «يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا.» · en "The category code already exists, or the name already exists under the same parent. Use a different code or name."; the drawer stays open, and the tree still shows one «هواتف».
Test data    : «هواتف», «إلكترونيات» (from the AC); code placeholder `<unused code>`
<!-- TC:TC-CAT-156:END -->

<!-- TC:TC-CAT-157:START traces=AC-CAT-073,REQ-CAT-071,SCR-CAT-002 -->
### TC-CAT-157 — Deactivating a Category with 4 active Items shows the in-use banner
Derived from : AC-CAT-073  (REQ-CAT-071)
Exercises    : SCR-CAT-002 `/cat/categories?node=<pk>` (CATEGORY-DEACTIVATE → API-CAT-024)
Rule / code  : RULE-CAT-025 → CAT-409-CATEGORY-IN-USE
Package      : F2-SCR-CAT-002
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · a Category holding 4 active Items
Host data    : none
Steps        : 1. Open the node. 2. Deactivate and confirm.
Expected     : The 409 CAT-409-CATEGORY-IN-USE is shown as a drawer banner displaying the envelope's messageAr / messageEn unchanged (the frontend plan routes this code without binding its words, so its wording, with {count} = 4, is asserted on the backend by TC-CAT-034 — ADR-CAT-053 §1), with an action that opens the Items of this category. The node stays active.
Test data    : 4 Items (from the AC)
<!-- TC:TC-CAT-157:END -->

#### SCR-CAT-003 — Variant attributes

<!-- TC:TC-CAT-158:START traces=AC-CAT-016,REQ-CAT-016,SCR-CAT-003,UXD-CAT-002 -->
### TC-CAT-158 — With `variants` off, the Variant attributes route shows the capability state
Derived from : AC-CAT-016  (REQ-CAT-016)
Exercises    : SCR-CAT-003 `/cat/variant-attributes` (route guard + ATTRIBUTE-SCREEN-INIT)
Rule / code  : RULE-CAT-008 → CAT-422-CAPABILITY-OFF ({flag} = variants)
Package      : F4-SCR-CAT-003
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0, except that app-config reports `variants` off
Host data    : none
Steps        : 1. Navigate to `/cat/variant-attributes` directly.
Expected     : The screen shows ar «هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «variants» من الإعدادات لاستخدامها.» · en "This function is not switched on for your business. Turn on the «variants» capability flag in settings to use it."; "New attribute" is absent; no request goes to API-CAT-041 or API-CAT-025, so nothing is created.
Test data    : —
<!-- TC:TC-CAT-158:END -->

<!-- TC:TC-CAT-159:START traces=AC-CAT-044,REQ-CAT-042,SCR-CAT-003 -->
### TC-CAT-159 — Creating the attribute SIZE
Derived from : AC-CAT-044  (REQ-CAT-042)
Exercises    : SCR-CAT-003 `/cat/variant-attributes?attribute=new` (ATTRIBUTE-SAVE step 1 → API-CAT-025)
Rule / code  : —
Package      : F2-SCR-CAT-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · the tenant has no Variant attribute
Host data    : none
Steps        : 1. Choose "New attribute". 2. Enter code SIZE and nameAr «المقاس». 3. Save.
Expected     : The list shows SIZE «المقاس» as active with sortOrder 1.
Test data    : SIZE, «المقاس» (from the AC)
<!-- TC:TC-CAT-159:END -->

<!-- TC:TC-CAT-160:START traces=AC-CAT-047,REQ-CAT-045,SCR-CAT-003 -->
### TC-CAT-160 — Adding the value L through the values editor
Derived from : AC-CAT-047  (REQ-CAT-045)
Exercises    : SCR-CAT-003 `/cat/variant-attributes?attribute=<SIZE>&values=open` (`AttributeValuesDrawer` validators; ATTRIBUTE-SAVE step 3 → API-CAT-029)
Rule / code  : RULE-CAT-016 (draft-set validator passes) · —
Package      : F3-SCR-CAT-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · the active attribute SIZE with values S and M
Host data    : none
Steps        : 1. Open SIZE and choose "Edit values". 2. Add a line with code L and nameAr «كبير». 3. Confirm (the draft set is validated). 4. Save.
Expected     : 3 → the summary row lists S, M, L. 4 → after the re-read, L is active under SIZE with sortOrder 3.
Test data    : S, M, L «كبير» (from the AC)
<!-- TC:TC-CAT-160:END -->

<!-- TC:TC-CAT-161:START traces=AC-CAT-050,REQ-CAT-048,SCR-CAT-003 -->
### TC-CAT-161 — The attribute list is in sortOrder with its values
Derived from : AC-CAT-050  (REQ-CAT-048)
Exercises    : SCR-CAT-003 `/cat/variant-attributes` (ATTRIBUTE-LIST-QUERY → API-CAT-041)
Rule / code  : —
Package      : F4-SCR-CAT-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · exactly SIZE (sortOrder 1, 3 values) and COLOUR (sortOrder 2, 4 values)
Host data    : none
Steps        : 1. Open `/cat/variant-attributes`.
Expected     : SIZE is listed before COLOUR, with 3 and 4 values respectively, each in sortOrder.
Test data    : SIZE, COLOUR (from the AC)
<!-- TC:TC-CAT-161:END -->

<!-- TC:TC-CAT-162:START traces=AC-CAT-056,REQ-CAT-054,SCR-CAT-003 -->
### TC-CAT-162 — Deactivating a value in use is refused on its line
Derived from : AC-CAT-056  (REQ-CAT-054)
Exercises    : SCR-CAT-003 `/cat/variant-attributes?attribute=<SIZE>&values=open` (ATTRIBUTE-SAVE step 2 → API-CAT-031)
Rule / code  : RULE-CAT-017 → CAT-409-ATTRIBUTE-VALUE-IN-USE
Package      : F2-SCR-CAT-003
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · the value M of SIZE carried by 12 active Variants
Host data    : none
Steps        : 1. Open SIZE and "Edit values". 2. Mark M for deactivation and Confirm. 3. Save.
Expected     : The M line is marked failed with ar «هذه الخاصية أو القيمة مستخدمة في 12 متغيّرًا نشطًا. أوقف هذه المتغيّرات أولًا.» · en "This attribute or value is used by 12 active variants. Deactivate those variants first."; after the re-read, M is still active.
Test data    : M, 12 Variants (from the AC)
<!-- TC:TC-CAT-162:END -->

<!-- TC:TC-CAT-163:START traces=AC-CAT-057,REQ-CAT-055,SCR-CAT-003 -->
### TC-CAT-163 — A second attribute SIZE is refused on code and name
Derived from : AC-CAT-057  (REQ-CAT-055)
Exercises    : SCR-CAT-003 `/cat/variant-attributes?attribute=new` (ATTRIBUTE-SAVE → API-CAT-025)
Rule / code  : RULE-CAT-016 → CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE
Package      : F2-SCR-CAT-003
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · the attribute SIZE exists
Host data    : none
Steps        : 1. Choose "New attribute". 2. Enter code SIZE and an unused nameAr. 3. Save.
Expected     : Inline on code and nameAr: ar «الرمز أو الاسم مستخدم بالفعل. استخدم رمزًا (حتى 30 حرفًا لاتينيًا أو رقمًا أو شرطة) واسمًا مختلفين.» · en "The code or name is already used. Use a different code (up to 30 Latin letters, digits or hyphens) and name."; the list still holds one SIZE.
Test data    : SIZE (from the AC); nameAr placeholder
<!-- TC:TC-CAT-163:END -->

#### SCR-CAT-004 — Item import

<!-- TC:TC-CAT-164:START traces=AC-CAT-037,REQ-CAT-036,SCR-CAT-004 -->
### TC-CAT-164 — Downloading the import sheet
Derived from : AC-CAT-037  (REQ-CAT-036)
Exercises    : SCR-CAT-004 `/cat/item-import` (IMPORT-SHEET-DOWNLOAD → API-CAT-048)
Rule / code  : —
Package      : F2-SCR-CAT-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · the screen mounted as the setup wizard's item-import step (`ItemImportStep`)
Host data    : none
Steps        : 1. Choose "Download import sheet".
Expected     : An xlsx file is saved, named after the import sheet. It has 1 sheet whose header row holds the 11 columns (nameAr, nameEn, baseUomCode, category code, brandCode, taxClassCode, einvoiceCodeTypeCode, einvoiceCode, GTIN barcode, descriptionAr, descriptionEn), each labelled in ar and en.
Test data    : —
<!-- TC:TC-CAT-164:END -->

<!-- TC:TC-CAT-165:START traces=AC-CAT-040,REQ-CAT-039,SCR-CAT-004 -->
### TC-CAT-165 — The result panel shows the counts and the rejected row
Derived from : AC-CAT-040  (REQ-CAT-039)
Exercises    : SCR-CAT-004 `/cat/item-import` (IMPORT-SUBMIT → API-CAT-049; result panel)
Rule / code  : RULE-CAT-045 → CAT-200-IMPORT-ROW-REJECTED (row message)
Package      : F4-SCR-CAT-004
Scenario     : HAPPY · data class INVALID · language ALL
Preconditions: F0 · an xlsx on the import sheet with 50 data rows, row 7 with an empty nameAr
Host data    : none
Steps        : 1. Choose the file. 2. Import.
Expected     : The result panel shows rows read 50, Items created 49 and rows rejected 1. The rejected-rows table has one row: row 7, column nameAr, and the reason displayed as the row's messageAr / messageEn from `ImportRowRejection`, unchanged (the frontend plan shows the server's row message without binding its words, so its wording is asserted on the backend by TC-CAT-084 — ADR-CAT-053 §1).
Test data    : 50 rows, row 7 (from the AC)
<!-- TC:TC-CAT-165:END -->

<!-- TC:TC-CAT-166:START traces=AC-CAT-041,REQ-CAT-040,SCR-CAT-004 -->
### TC-CAT-166 — A .csv file is refused on the file input
Derived from : AC-CAT-041  (REQ-CAT-040)
Exercises    : SCR-CAT-004 `/cat/item-import` (`ImportForm` validator)
Rule / code  : RULE-CAT-044 (client validator; server code CAT-422-IMPORT-FILE-REJECTED)
Package      : F3-SCR-CAT-004
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0
Host data    : none
Steps        : 1. Choose a .csv file. 2. Import.
Expected     : Inline on the file input: ar «الملف غير مقبول: يجب أن يكون بصيغة xlsx بحجم لا يتجاوز 10 ميجابايت وحتى 5000 صف، وبعناوين ورقة الاستيراد نفسها. نزّل ورقة الاستيراد واستخدمها.» · en "The file was not accepted: it must be xlsx, at most 10 MB and 5,000 rows, with the import sheet headers. Download the import sheet and use it."; no request to API-CAT-049, so 0 Items are created.
Test data    : a .csv file (from the AC)
<!-- TC:TC-CAT-166:END -->

#### SCR-CAT-005 — Barcode labels

<!-- TC:TC-CAT-167:START traces=AC-CAT-103,REQ-CAT-101,SCR-CAT-005 -->
### TC-CAT-167 — The Variant picker finds Variants by Item name
Derived from : AC-CAT-103  (REQ-CAT-101)
Exercises    : SCR-CAT-005 `/cat/barcode-labels?picker=variants&text=قميص` (VARIANT-SEARCH-QUERY → API-CAT-042)
Rule / code  : —
Package      : F2-SCR-CAT-005
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0, with the user a Warehouse staff user · exactly 3 active Variants whose Item nameAr contains «قميص», at least one with no active barcode
Host data    : none
Steps        : 1. Open `/cat/barcode-labels` and "Add variants". 2. Search «قميص» with page size 20.
Expected     : 3 rows, each showing its Item nameAr and its earliest-created active barcode, or an empty barcode cell when it has none.
Test data    : «قميص», 20 (from the AC)
<!-- TC:TC-CAT-167:END -->

<!-- TC:TC-CAT-168:START traces=AC-CAT-104,REQ-CAT-102,SCR-CAT-005 -->
### TC-CAT-168 — Printing 15 labels opens one document
Derived from : AC-CAT-104  (REQ-CAT-102)
Exercises    : SCR-CAT-005 `/cat/barcode-labels` (LABEL-PRINT → API-CAT-050)
Rule / code  : —
Package      : F4-SCR-CAT-005
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0, with the user a Warehouse staff user · 2 Variants, each with an active barcode
Host data    : none
Steps        : 1. Add the 2 Variants as lines. 2. Set copies 10 and 5 (the total shows 15). 3. Print.
Expected     : One PDF opens in a new tab, holding 15 labels; each shows the Variant nameAr, code and barcode.
Test data    : 10 + 5 copies (from the AC)
<!-- TC:TC-CAT-168:END -->

<!-- TC:TC-CAT-169:START traces=AC-CAT-105,REQ-CAT-103,SCR-CAT-005 -->
### TC-CAT-169 — 1,000 copies of one Variant are refused on the line
Derived from : AC-CAT-105  (REQ-CAT-103)
Exercises    : SCR-CAT-005 `/cat/barcode-labels` (`LabelForm` validator)
Rule / code  : RULE-CAT-036 (client validator; server code CAT-422-LABEL-COPIES-OUT-OF-RANGE)
Package      : F3-SCR-CAT-005
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: F0, with the user a Warehouse staff user · one line of a Variant with an active barcode
Host data    : none
Steps        : 1. Set copies 1000. 2. Print.
Expected     : Inline on the copies: ar «عدد النسخ لكل متغيّر من 1 إلى 999، وإجمالي الملصقات لا يتجاوز 5000.» · en "Copies per variant are 1 to 999, and the total cannot exceed 5,000 labels."; no request to API-CAT-050, so no document is produced.
Test data    : 1,000 copies (from the AC)
<!-- TC:TC-CAT-169:END -->

<!-- TC:TC-CAT-170:START traces=AC-CAT-106,REQ-CAT-104,SCR-CAT-005 -->
### TC-CAT-170 — Lines without a barcode are flagged and block printing
Derived from : AC-CAT-106  (REQ-CAT-104)
Exercises    : SCR-CAT-005 `/cat/barcode-labels` (`LabelForm` validator; LABEL-FACADE pre-flagged lines)
Rule / code  : RULE-CAT-037 (client validator, {count} filled; server code CAT-422-LABEL-VARIANT-NO-BARCODE)
Package      : F3-SCR-CAT-005
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0, with the user a Warehouse staff user · 3 Variants as lines, 2 of them with no active barcode
Host data    : none
Steps        : 1. Print.
Expected     : The 2 lines are flagged `no-barcode`, each with "Open item" → `/cat/items/:itemPk`. The form shows ar «لا يوجد باركود نشط لـ 2 متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.» · en "2 variants have no active barcode. Generate an internal barcode for them, then print."; no request to API-CAT-050, so no document is produced.
Test data    : 3 Variants, 2 without barcode (from the AC)
<!-- TC:TC-CAT-170:END -->

#### SCR-CAT-006 — Catalogue settings

<!-- TC:TC-CAT-171:START traces=AC-CAT-023,REQ-CAT-022,SCR-CAT-006 -->
### TC-CAT-171 — The variants turn-off check lists the 3 dependent Items
Derived from : AC-CAT-023  (REQ-CAT-022)
Exercises    : SCR-CAT-006 `/cat/settings?check=variants` (TURN-OFF-CHECK-QUERY → API-CAT-051)
Rule / code  : RULE-CAT-010 → CAT-200-VARIANTS-IN-USE
Package      : F2-SCR-CAT-006
Scenario     : VIOLATION · data class VALID · language ALL
Preconditions: F0 · 3 active Items whose active Variants carry attribute assignments
Host data    : none
Steps        : 1. In the turn-off check panel, choose `variants` and "Check". 2. Choose one of the listed Items.
Expected     : 1 → the panel shows the not-allowed answer for the envelope code CAT-200-VARIANTS-IN-USE, displaying the envelope's messageAr / messageEn unchanged (the frontend plan routes this code without binding its words, so its wording, with {count} = 3, is asserted on the backend by TC-CAT-010 — ADR-CAT-053 §1), a dependent count of 3 and the 3 Items by code and nameAr. 2 → `/cat/items/:itemPk` opens.
Test data    : 3 Items (from the AC)
<!-- TC:TC-CAT-171:END -->

<!-- TC:TC-CAT-172:START traces=AC-CAT-094,REQ-CAT-092,SCR-CAT-006 -->
### TC-CAT-172 — Creating the scale label layout 20 with its defaults
Derived from : AC-CAT-094  (REQ-CAT-092)
Exercises    : SCR-CAT-006 `/cat/settings?layout=new` (LAYOUT-SAVE → API-CAT-032)
Rule / code  : —
Package      : F2-SCR-CAT-006
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · no active scale label layout
Host data    : none
Steps        : 1. Choose "New layout" (scaleCodeLength 5, WEIGHT, decimals 3 pre-filled). 2. Enter key 20. 3. Save.
Expected     : The layouts list shows the layout 20 (5, WEIGHT, 3) as active.
Test data    : 20, 5, WEIGHT, 3 (from the AC)
<!-- TC:TC-CAT-172:END -->

<!-- TC:TC-CAT-173:START traces=AC-CAT-097,REQ-CAT-095,SCR-CAT-006 -->
### TC-CAT-173 — The settings page lists every layout and Unit e-invoice code
Derived from : AC-CAT-097  (REQ-CAT-095)
Exercises    : SCR-CAT-006 `/cat/settings` (SETTINGS-QUERY → API-CAT-045)
Rule / code  : —
Package      : F4-SCR-CAT-006
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: F0 · 1 active layout · 8 active and 1 inactive Unit e-invoice codes (built from seeded values, as in TC-CAT-108)
Host data    : none
Steps        : 1. Open `/cat/settings`.
Expected     : The layouts list holds 1 entry, and the Unit e-invoice code list holds 9 entries, each showing its active state (8 active, 1 inactive), labelled by the CAT_UOM label of its key.
Test data    : 1 / 8 / 1 (from the AC)
<!-- TC:TC-CAT-173:END -->

<!-- TC:TC-CAT-174:START traces=AC-CAT-100,REQ-CAT-098,SCR-CAT-006 -->
### TC-CAT-174 — A layout with prefix 62 is refused in the drawer
Derived from : AC-CAT-100  (REQ-CAT-098)
Exercises    : SCR-CAT-006 `/cat/settings?layout=new` (`LayoutForm` validator)
Rule / code  : RULE-CAT-034 (client validator; server code CAT-422-SCALE-LAYOUT-INVALID)
Package      : F3-SCR-CAT-006
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0
Host data    : none
Steps        : 1. Choose "New layout". 2. Enter key 62. 3. Save.
Expected     : Inline on key: ar «البادئة رقمان (02 أو من 20 إلى 29) غير مستخدمة في إعداد نشط آخر، وطول رمز الميزان من 4 إلى 6، والأرقام العشرية من 0 إلى 3.» · en "The prefix is two digits (02 or 20 to 29) not used by another active layout, the scale code length is 4 to 6, and the decimals are 0 to 3."; no request to API-CAT-032, so nothing is saved.
Test data    : 62 (from the AC)
<!-- TC:TC-CAT-174:END -->

<!-- TC:TC-CAT-175:START traces=AC-CAT-126,REQ-CAT-124,SCR-CAT-006,UXD-CAT-001 -->
### TC-CAT-175 — A second e-invoice unit for PIECE is refused in the drawer
Derived from : AC-CAT-126  (REQ-CAT-124)
Exercises    : SCR-CAT-006 `/cat/settings?unitCode=new` (`UnitCodeForm` validator, from SETTINGS-QUERY)
Rule / code  : RULE-CAT-043 (client validator; server code CAT-409-UNIT-EINVOICE-DUPLICATE)
Package      : F3-SCR-CAT-006
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: F0 · the active Unit e-invoice code PIECE → EA (seeded)
Host data    : none
Steps        : 1. Choose "New unit code". 2. Choose key PIECE and einvoiceUnitCode BX. 3. Save.
Expected     : Inline on key: ar «لهذه الوحدة رمز فاتورة إلكترونية نشط بالفعل. عدّله بدلًا من إضافة رمز جديد.» · en "This unit already has an active e-invoice unit. Change it instead of adding a new one.", with "Open the existing code" → `?unitCode=<pk of PIECE → EA>`; no request to API-CAT-035, so nothing is created.
Test data    : PIECE → EA (from the AC). The AC's second e-invoice unit is not a seeded value and no named add-value call exists for it, so the case uses the seeded active CAT_EINVOICE_UNIT value BX in its place (ADR-CAT-053 §2)
<!-- TC:TC-CAT-175:END -->
<!-- SUB:UI-FLOWS:END -->

<!-- SUB:INT-FLOW:START traces=AC-CAT-005,REQ-CAT-005,REQ-CAT-024,REQ-CAT-004,REQ-CAT-003 -->
### SUB INT-FLOW

<!-- TC:TC-CAT-176:START traces=AC-CAT-005,REQ-CAT-005,REQ-CAT-024,REQ-CAT-004,REQ-CAT-003,SCR-CAT-001 -->
### TC-CAT-176 — Item lifecycle: create → search → update → publish → deactivate → gone from active results
Derived from : AC-CAT-005  (REQ-CAT-005; the flow passes through REQ-CAT-024, REQ-CAT-003, REQ-CAT-004)
Exercises    : SCR-CAT-001 `/cat/items/new` → `/cat/items` → `/cat/items/:itemPk` (ITEM-SAVE → API-CAT-001, 003; ITEM-SEARCH-QUERY → API-CAT-038; ITEM-STATE-COMMAND → API-CAT-005, 004)
Rule / code  : RULE-CAT-041 (deactivation sets UNPUBLISHED) · RULE-CAT-048 → CAT-200-DEACTIVATED (notice)
Package      : F4-SCR-CAT-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: F0
Host data    : none
Steps        : 1. Create an Item with a valid nameAr only and Save. 2. Search `/cat/items` with its nameAr. 3. Open it, set a nameEn and Save. 4. Publish (confirm). 5. Deactivate (confirm). 6. Search again with the default filters (`isActiveFl=true`). 7. Search with `isActiveFl=false` and open the Item.
Expected     : 2 → the Item is listed. 4 → the badge shows "Published". 5 → a notice: ar «تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة.» · en "Deactivated. The record stays available to earlier documents and no longer appears in new selections."; the header shows inactive and "Not published". 6 → the Item is not listed. 7 → the Item is listed and opens: it is still readable.
Test data    : nameAr / nameEn placeholders `<valid Arabic name>`, `<English name>`
<!-- TC:TC-CAT-176:END -->
<!-- SUB:INT-FLOW:END -->
<!-- PHASE:TEST-PLAN-FE:END -->

<!-- PHASE:INT-UXD:START traces=UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,REQ-CAT-073,REQ-CAT-074,REQ-CAT-076,AC-CAT-075,AC-CAT-076,AC-CAT-078,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,AC-CAT-013,AC-CAT-014,REQ-CAT-105,REQ-CAT-109,AC-CAT-107,AC-CAT-111 -->
## PHASE INT-UXD — foreign-owned fields (owner: erp-core, the given)

SCR-CAT-001 renders three foreign-owned fields that the frontend plan cites: UXD-CAT-001 (lookup values), UXD-CAT-002 (capability flags) and UXD-CAT-003 (image files). Their owner is the given erp-core, not a business module (ADR-CAT-050, ADR-CAT-052 §7). Engine §5 gives two cases per UXD — the field rendered, and the field empty or the owner failing — so the phase holds 6 TCs. That is no more than 8, so the phase has no SUB. All 6 are grouped under the owner erp-core.

<!-- TC:TC-CAT-177:START traces=UXD-CAT-001,REQ-CAT-073,REQ-CAT-074,AC-CAT-075,AC-CAT-076,SCR-CAT-001 -->
### TC-CAT-177 — Lookup-backed fields render the owner's labels
Derived from : UXD-CAT-001  (REQ-CAT-073, REQ-CAT-074, AC-CAT-075, AC-CAT-076)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (CAT_UOM-LOOKUP, CAT_TAX_CLASS-LOOKUP via the given lookup client)
Rule / code  : —
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: F0 · the owner's lookup read answers · an Item with baseUomCode CARTON and taxClassCode ZERO_RATED (seeded values)
Host data    : none
Steps        : 1. Open the Item.
Expected     : The base unit shows ar «كرتونة» / en "Carton", and the tax class shows ar «خاضع لنسبة صفر» / en "Zero-rated": the labels the owner's lookup returns (SRS A6).
Test data    : CARTON, ZERO_RATED (seeded)
<!-- TC:TC-CAT-177:END -->

<!-- TC:TC-CAT-178:START traces=UXD-CAT-001,REQ-CAT-073,REQ-CAT-076,AC-CAT-078,SCR-CAT-001 -->
### TC-CAT-178 — A failed lookup read shows the raw code and disables the select
Derived from : UXD-CAT-001  (REQ-CAT-073, REQ-CAT-076, AC-CAT-078)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (CAT_TAX_CLASS-LOOKUP)
Rule / code  : — (platform generic message, §3.0 of the frontend plan)
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: F0 · the owner's CAT_TAX_CLASS lookup read fails · an Item with taxClassCode ZERO_RATED
Host data    : none
Steps        : 1. Open the Item.
Expected     : The tax class field shows the raw code ZERO_RATED, its select is disabled, and the platform's generic message is shown. The rest of the entry renders, with no blank screen or crash.
Test data    : ZERO_RATED (seeded)
<!-- TC:TC-CAT-178:END -->

<!-- TC:TC-CAT-179:START traces=UXD-CAT-002,REQ-CAT-012,REQ-CAT-014,AC-CAT-014,SCR-CAT-001 -->
### TC-CAT-179 — Capability flags on render the flag-bound panes
Derived from : UXD-CAT-002  (REQ-CAT-012, REQ-CAT-014, AC-CAT-014)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (CAPABILITY-FLAGS-INIT, platform app-config)
Rule / code  : —
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: F0 (app-config reports `variants` and `bundles` on) · a BUNDLE Item and a STANDARD Item
Host data    : none
Steps        : 1. Open `/cat/items` (search). 2. Open the STANDARD Item. 3. Open the BUNDLE Item.
Expected     : 1 → the Item kind filter is shown. 2 → `VariantsPane` with "Generate variants" is shown. 3 → `BundleComponentsPane` is shown.
Test data    : —
<!-- TC:TC-CAT-179:END -->

<!-- TC:TC-CAT-180:START traces=UXD-CAT-002,REQ-CAT-013,AC-CAT-013,SCR-CAT-001 -->
### TC-CAT-180 — Unreadable capability flags are treated as off and explained
Derived from : UXD-CAT-002  (REQ-CAT-013, AC-CAT-013)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (CAPABILITY-FLAGS-INIT)
Rule / code  : RULE-CAT-009 (text bound at the flag-bound pane)
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: F0, except that the platform app-config answers no value for `variants` (owner failure) · a STANDARD Item
Host data    : none
Steps        : 1. Open the Item.
Expected     : `variants` is treated as off for display, so `VariantsPane` and "Generate variants" are absent. In their place the screen shows ar «تعذّر التحقق من إعدادات القدرات الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.» · en "Capability settings could not be checked, and nothing was saved. Try again in a moment.". The rest of the entry renders.
Test data    : —
<!-- TC:TC-CAT-180:END -->

<!-- TC:TC-CAT-181:START traces=UXD-CAT-003,REQ-CAT-105,REQ-CAT-109,AC-CAT-107,AC-CAT-111,SCR-CAT-001 -->
### TC-CAT-181 — Image thumbnails render the files of the public image storage
Derived from : UXD-CAT-003  (REQ-CAT-105, REQ-CAT-109, AC-CAT-107, AC-CAT-111)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (`ImagesPane`, IMAGE-FILE-URL; VARIANT-IMAGES-QUERY → API-CAT-044)
Rule / code  : —
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: F0 · an Item with 2 active images (one primary) whose fileDocumentRef files the owner serves
Host data    : none
Steps        : 1. Open the Item. 2. Expand its default Variant row.
Expected     : 1 → `ImagesPane` shows 2 fixed-size thumbnails, each rendering the file the owner returns, with the primary first. 2 → the Variant row shows the same images (the fallback to unbound images), primary first.
Test data    : 2 images (placeholder files)
<!-- TC:TC-CAT-181:END -->

<!-- TC:TC-CAT-182:START traces=UXD-CAT-003,REQ-CAT-105,AC-CAT-107,SCR-CAT-001 -->
### TC-CAT-182 — A failed image file read shows the placeholder image
Derived from : UXD-CAT-003  (REQ-CAT-105, AC-CAT-107)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (IMAGE-FILE-URL)
Rule / code  : —
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: F0 · an Item with 1 active image whose file read from the owner fails
Host data    : none
Steps        : 1. Open the Item.
Expected     : The image line shows the placeholder image, with its nameAr / nameEn and sortOrder still shown. The pane and the entry render, with no blank screen or crash.
Test data    : 1 image (placeholder file)
<!-- TC:TC-CAT-182:END -->
<!-- PHASE:INT-UXD:END -->

## TC TRACEABILITY INDEX

### AC → TC (and REQ → TC) — this track
| AC | REQ | TC | | AC | REQ | TC |
|---|---|---|---|---|---|---|
| AC-CAT-003 | REQ-CAT-003 | TC-CAT-125 | | AC-CAT-071 | REQ-CAT-069 | TC-CAT-155 |
| AC-CAT-005 | REQ-CAT-005 | TC-CAT-176 | | AC-CAT-072 | REQ-CAT-070 | TC-CAT-156 |
| AC-CAT-007 | REQ-CAT-007 | TC-CAT-126 | | AC-CAT-073 | REQ-CAT-071 | TC-CAT-157 |
| AC-CAT-013 | REQ-CAT-013 | TC-CAT-127 (+ UXD TC-CAT-180) | | AC-CAT-075 | REQ-CAT-073 | TC-CAT-137 (+ UXD TC-CAT-177) |
| AC-CAT-014 | REQ-CAT-014 | TC-CAT-128 (+ UXD TC-CAT-179) | | AC-CAT-076 | REQ-CAT-074 | TC-CAT-138 (+ UXD TC-CAT-177) |
| AC-CAT-015 | REQ-CAT-015 | TC-CAT-129 | | AC-CAT-078 | REQ-CAT-076 | TC-CAT-139 (+ UXD TC-CAT-178) |
| AC-CAT-016 | REQ-CAT-016 | TC-CAT-158 | | AC-CAT-082 | REQ-CAT-080 | TC-CAT-140 |
| AC-CAT-020 | REQ-CAT-019 | TC-CAT-130 | | AC-CAT-088 | REQ-CAT-086 | TC-CAT-141 |
| AC-CAT-023 | REQ-CAT-022 | TC-CAT-171 | | AC-CAT-090 | REQ-CAT-088 | TC-CAT-142 |
| AC-CAT-025 | REQ-CAT-024 | TC-CAT-131 | | AC-CAT-092 | REQ-CAT-090 | TC-CAT-143 |
| AC-CAT-027 | REQ-CAT-026 | TC-CAT-132 | | AC-CAT-094 | REQ-CAT-092 | TC-CAT-172 |
| AC-CAT-028 | REQ-CAT-027 | TC-CAT-133 | | AC-CAT-097 | REQ-CAT-095 | TC-CAT-173 |
| AC-CAT-031 | REQ-CAT-030 | TC-CAT-134 | | AC-CAT-100 | REQ-CAT-098 | TC-CAT-174 |
| AC-CAT-032 | REQ-CAT-031 | TC-CAT-135 | | AC-CAT-102 | REQ-CAT-100 | TC-CAT-144 |
| AC-CAT-037 | REQ-CAT-036 | TC-CAT-164 | | AC-CAT-103 | REQ-CAT-101 | TC-CAT-167 |
| AC-CAT-040 | REQ-CAT-039 | TC-CAT-165 | | AC-CAT-104 | REQ-CAT-102 | TC-CAT-168 |
| AC-CAT-041 | REQ-CAT-040 | TC-CAT-166 | | AC-CAT-105 | REQ-CAT-103 | TC-CAT-169 |
| AC-CAT-044 | REQ-CAT-042 | TC-CAT-159 | | AC-CAT-106 | REQ-CAT-104 | TC-CAT-170 |
| AC-CAT-047 | REQ-CAT-045 | TC-CAT-160 | | AC-CAT-107 | REQ-CAT-105 | UXD TC-CAT-181, 182 (module TC on the backend: TC-CAT-113) |
| AC-CAT-050 | REQ-CAT-048 | TC-CAT-161 | | AC-CAT-108 | REQ-CAT-106 | TC-CAT-145 |
| AC-CAT-054 | REQ-CAT-052 | TC-CAT-136 | | AC-CAT-111 | REQ-CAT-109 | UXD TC-CAT-181 (module TC on the backend: TC-CAT-115) |
| AC-CAT-056 | REQ-CAT-054 | TC-CAT-162 | | AC-CAT-112 | REQ-CAT-110 | TC-CAT-146 |
| AC-CAT-057 | REQ-CAT-055 | TC-CAT-163 | | AC-CAT-118 | REQ-CAT-116 | TC-CAT-147 |
| AC-CAT-066 | REQ-CAT-064 | TC-CAT-152 | | AC-CAT-125 | REQ-CAT-123 | TC-CAT-148 |
| AC-CAT-069 | REQ-CAT-067 | TC-CAT-153 | | AC-CAT-126 | REQ-CAT-124 | TC-CAT-175 |
| AC-CAT-070 | REQ-CAT-068 | TC-CAT-154 | | AC-CAT-127 | REQ-CAT-125 | TC-CAT-149 |
| | | | | AC-CAT-128 | REQ-CAT-126 | TC-CAT-150 |
| | | | | AC-CAT-129 | REQ-CAT-127 | TC-CAT-151 |

TC-CAT-176 (lifecycle flow) also passes through REQ-CAT-024, REQ-CAT-003 and REQ-CAT-004. Every other AC-CAT-* is covered on the backend track: see the AC → TC table of `backend-test-plan-cat.md`.

### SCR → TC (TC-CAT-…)
| SCR | TCs |
|---|---|
| SCR-CAT-001 Items | 125 – 151, 176, 177 – 182 |
| SCR-CAT-002 Categories | 152 – 157 |
| SCR-CAT-003 Variant attributes | 128 (menu entry), 158 – 163 |
| SCR-CAT-004 Item import | 164 – 166 |
| SCR-CAT-005 Barcode labels | 167 – 170 |
| SCR-CAT-006 Catalogue settings | 171 – 175 |

### RULE / code → TC (TC-CAT-…) — refusals and notices routed on screen (msg-bound)
A row marked "routed, words unbound" is a code the frontend plan routes to a control without a `text:` binding. Its TC asserts the routing and the unchanged display of the envelope's messageAr / messageEn, and the wording is asserted by the backend TC named (ADR-CAT-053 §1). Every other row asserts the words, bound by a `text:` row of the plan.

| RULE | code | TCs | where the plan routes it |
|---|---|---|---|
| RULE-CAT-001 | CAT-422-NAME-AR-INVALID | 133, 134 | F3 `ItemForm` nameAr · F2 ITEM-SAVE |
| RULE-CAT-002 | CAT-422-NAME-EN-TOO-LONG | 135 | F3 `ItemForm` nameEn · F2 ITEM-SAVE |
| RULE-CAT-003 | CAT-409-ITEM-NAME-DUPLICATE | 126 | F2 ITEM-SAVE (inline nameAr + action) |
| RULE-CAT-008 | CAT-422-CAPABILITY-OFF | 158 | F2 ATTRIBUTE-LIST-QUERY / F4 route state |
| RULE-CAT-009 | CAT-500-CAPABILITY-UNREADABLE | 127, 180 | F2 ITEM-SAVE banner · F2 CAPABILITY-FLAGS-INIT |
| RULE-CAT-010 | CAT-200-VARIANTS-IN-USE | 171 | F2 TURN-OFF-CHECK-QUERY — routed, words unbound (wording: TC-CAT-010) |
| RULE-CAT-012 | CAT-201-EXISTING-VARIANTS-KEPT | 130 | F2 ITEM-SAVE step 5 — routed, words unbound (wording: TC-CAT-009) |
| RULE-CAT-014 | CAT-422-VARIANT-LIMIT-EXCEEDED | 136 | F3 Variant generation validator |
| RULE-CAT-016 | CAT-409-ATTRIBUTE-CODE-NAME-DUPLICATE | 163 (160 passes) | F2 ATTRIBUTE-SAVE · F3 value lines |
| RULE-CAT-017 | CAT-409-ATTRIBUTE-VALUE-IN-USE | 162 | F2 ATTRIBUTE-SAVE |
| RULE-CAT-023 | CAT-422-CATEGORY-PARENT-INVALID | 155 | F3 `CategoryForm` parent · F2 CATEGORY-SAVE |
| RULE-CAT-024 | CAT-409-CATEGORY-DUPLICATE | 156 | F2 CATEGORY-SAVE |
| RULE-CAT-025 | CAT-409-CATEGORY-IN-USE | 157 | F2 CATEGORY-DEACTIVATE — routed, words unbound (wording: TC-CAT-034) |
| RULE-CAT-027 | CAT-422-ITEM-UNIT-INVALID | 140 | F3 Item unit line · F2 ITEM-SAVE |
| RULE-CAT-030 | CAT-422-GTIN-INVALID | 142 | F3 barcode GTIN line · F2 ITEM-SAVE |
| RULE-CAT-034 | CAT-422-SCALE-LAYOUT-INVALID | 174 | F3 layout drawer · F2 LAYOUT-SAVE |
| RULE-CAT-035 | CAT-422-SCALE-LABEL-UNREADABLE | 144 | F2 BARCODE-LOOKUP-QUERY — routed, words unbound (wording: TC-CAT-051) |
| RULE-CAT-036 | CAT-422-LABEL-COPIES-OUT-OF-RANGE | 169 | F3 labels · F2 LABEL-PRINT |
| RULE-CAT-037 | CAT-422-LABEL-VARIANT-NO-BARCODE | 170 | F3 labels · F2 LABEL-PRINT |
| RULE-CAT-038 | CAT-422-IMAGE-NOT-ACCEPTED | 146 | F3 image line · F2 ITEM-SAVE |
| RULE-CAT-039 | CAT-200-PRIMARY-IMAGE-REPLACED | 145 | F2 ITEM-SAVE step 8 — routed, words unbound (wording: TC-CAT-054) |
| RULE-CAT-041 | CAT-422-PUBLISH-BLOCKED | 147 (176 state) | F2 ITEM-STATE-COMMAND |
| RULE-CAT-042 | CAT-422-EINVOICE-CODE-INVALID | 148 | F3 `ItemForm` e-invoice pair · F2 ITEM-SAVE |
| RULE-CAT-043 | CAT-409-UNIT-EINVOICE-DUPLICATE | 175 | F3 unit code drawer · F2 UNIT-CODE-SAVE |
| RULE-CAT-044 | CAT-422-IMPORT-FILE-REJECTED | 166 | F3 `ImportForm` · F2 IMPORT-SUBMIT |
| RULE-CAT-045 | CAT-200-IMPORT-ROW-REJECTED | 165 | F2 IMPORT-SUBMIT (row messages) — routed, words unbound (wording: TC-CAT-084) |
| RULE-CAT-046 | CAT-400-SEARCH-TERM-TOO-SHORT | 150 | F2 CATALOGUE-SEARCH-QUERY — routed, words unbound (wording: TC-CAT-064) |
| RULE-CAT-047 | CAT-404-BARCODE-NOT-FOUND | 143 | F2 BARCODE-LOOKUP-QUERY — routed, words unbound (wording: TC-CAT-046) |
| RULE-CAT-048 | CAT-200-DEACTIVATED | 176 | F2 ITEM-STATE-COMMAND |

The other rules are asserted on the backend track. No case here asserts the words of a refusal or notice that the frontend plan leaves unbound; those eight codes are recorded as a plan gap in ADR-CAT-053 §1, for a later P3.2 delta that adds their `text:` bindings.

### UXD → TC
| UXD | owner (source module) | rendered | empty / owner failure |
|---|---|---|---|
| UXD-CAT-001 lookup values | erp-core (given mdl) | TC-CAT-177 | TC-CAT-178 |
| UXD-CAT-002 capability flags | erp-core (given cu / app-config) | TC-CAT-179 | TC-CAT-180 |
| UXD-CAT-003 image files | erp-core (given file) | TC-CAT-181 | TC-CAT-182 |

### Package → TC (TC-CAT-…)
| Package (frontend plan unit) | # | TCs |
|---|---|---|
| F2-SCR-CAT-001 | 15 | 125, 126, 127, 130, 131, 137, 138, 139, 141, 143, 144, 145, 147, 149, 150 |
| F3-SCR-CAT-001 | 9 | 132, 133, 134, 135, 136, 140, 142, 146, 148 |
| F4-SCR-CAT-001 | 9 | 128, 129, 176, 177, 178, 179, 180, 181, 182 |
| F2-SCR-CAT-002 | 4 | 152, 154, 156, 157 |
| F3-SCR-CAT-002 | 1 | 155 |
| F4-SCR-CAT-002 | 1 | 153 |
| F2-SCR-CAT-003 | 3 | 159, 162, 163 |
| F3-SCR-CAT-003 | 1 | 160 |
| F4-SCR-CAT-003 | 2 | 158, 161 |
| F2-SCR-CAT-004 | 1 | 164 |
| F3-SCR-CAT-004 | 1 | 166 |
| F4-SCR-CAT-004 | 1 | 165 |
| F2-SCR-CAT-005 | 1 | 167 |
| F3-SCR-CAT-005 | 2 | 169, 170 |
| F4-SCR-CAT-005 | 1 | 168 |
| F2-SCR-CAT-006 | 2 | 171, 172 |
| F3-SCR-CAT-006 | 2 | 174, 175 |
| F4-SCR-CAT-006 | 1 | 173 |
| SEC-FE | 1 | 151 |
| F1-SCR-CAT-001 … 006 · ALIGN-FE | 0 | no TC — F1 is generated types with no observable behaviour; ALIGN-FE is the alignment self-check (ADR-CAT-052 §2) |

## COVERAGE

| Measure | Result |
|---|---|
| AC covered (this track) | 52 / 129 by module TCs. Of those, AC-CAT-014, 015, 027, 075 and 076 are covered on this track only. |
| AC covered (both plans) | 129 / 129 ✓, no gap. The other 77 ACs are covered by the backend track; see `backend-test-plan-cat.md` (ADR-CAT-052 §1). |
| REQ covered (this track) | 53 REQs by module TCs (plus REQ-CAT-012, 105 and 109 through UXD TCs) |
| REQ covered (both plans) | 127 / 127 ✓ |
| SCR covered | 6 / 6 ✓ (SCR-CAT-001 … 006) |
| UXD covered | 3 / 3 ✓. Each UXD has its rendered and its failure case (6 TCs in `INT-UXD`). |
| Refusal texts | every wording asserted here is bound by a `text:` row of the frontend plan (msg-bound) ✓. Eight routed codes have no binding (RULE-CAT-010, 012, 025, 035, 039, 045, 046, 047): their TCs assert routing and unchanged display only, and the gap is recorded in ADR-CAT-053 §1. |
| Track size | 58 TCs for 52 ACs and 3 UXDs (ratio < 2, within the guard) |
| Host data | CAT_BRAND BRAND1_137 … BRAND5_137 (active) and BRAND6_137 (inactive) for TC-CAT-137; CAT_BRAND OLDBRAND (active, then inactive) for TC-CAT-139. The AC value CAT_EINVOICE_UNIT H87 (AC-CAT-126) is not seeded and has no named add-value call, so TC-CAT-175 uses the seeded value BX in its place (ADR-CAT-053 §2). TC-CAT-137 and TC-CAT-138 assert closed option sets, so they run in a tenant that holds only their own values (ADR-CAT-052 §4). No value is demanded in opposite states. |
