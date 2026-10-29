<!-- source: PHASE:INT-UXD -->
<!-- traces: AC-CAT-013, AC-CAT-014, AC-CAT-075, AC-CAT-076, AC-CAT-078, AC-CAT-107, AC-CAT-111, AC-CAT-174, API-CAT-052, REQ-CAT-012, REQ-CAT-013, REQ-CAT-014, REQ-CAT-058, REQ-CAT-073, REQ-CAT-074, REQ-CAT-076, REQ-CAT-105, REQ-CAT-109, SCR-CAT-001, UXD-CAT-001, UXD-CAT-002, UXD-CAT-003 -->
<!-- PHASE:INT-UXD:START traces=UXD-CAT-001,UXD-CAT-002,UXD-CAT-003,REQ-CAT-058,AC-CAT-174,REQ-CAT-073,REQ-CAT-074,REQ-CAT-076,AC-CAT-075,AC-CAT-076,AC-CAT-078,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,AC-CAT-013,AC-CAT-014,REQ-CAT-105,REQ-CAT-109,AC-CAT-107,AC-CAT-111 -->
## PHASE INT-UXD — foreign-owned fields (owner: erp-core, the given)

SCR-CAT-001 renders three foreign-owned fields that the frontend plan cites: UXD-CAT-001 (lookup values, stored by the given mdl and read through CAT's `API-CAT-052` — ADR-CAT-065), UXD-CAT-002 (capability flags) and UXD-CAT-003 (image files). Their owner is the given erp-core, not a business module (ADR-CAT-050, ADR-CAT-052 §7). Engine §5 gives two cases per UXD — the field rendered, and the field empty or the owner failing — so the phase holds 6 TCs. That is no more than 8, so the phase has no SUB. All 6 are grouped under the owner erp-core.

<!-- TC:TC-CAT-177:START traces=UXD-CAT-001,REQ-CAT-073,REQ-CAT-074,REQ-CAT-076,AC-CAT-075,AC-CAT-076,AC-CAT-078,SCR-CAT-001,API-CAT-052 -->
### TC-CAT-177 — Lookup-backed fields render the owner's labels, and a value no longer active its code
Derived from : UXD-CAT-001  (REQ-CAT-073, REQ-CAT-074, REQ-CAT-076, AC-CAT-075, AC-CAT-076, AC-CAT-078)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (CAT_UOM-LOOKUP, CAT_TAX_CLASS-LOOKUP, CAT_BRAND-LOOKUP → API-CAT-052 GET /api/v1/cat/lookups/{key})
Rule / code  : — (interim rendering of an inactive value — ADR-CAT-073)
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: F0 · the owner's lookup read answers · an Item with baseUomCode CARTON and taxClassCode ZERO_RATED (seeded values) and brandCode RETIRED_177, saved while RETIRED_177 was active; RETIRED_177 is then deactivated
Host data    : CAT_BRAND RETIRED_177, labelled ar «علامة متوقفة» / en "Retired brand" (labels that differ from the code) — created active by the given mdl call POST /api/v1/mdl/lookup-types/{id}/values ({id} = the CAT_BRAND type found by POST /api/v1/mdl/lookup-types/search) before the Item is saved, then made inactive by DELETE /api/v1/mdl/lookup-values/{id} ({id} = the RETIRED_177 value)
Steps        : 1. Open the Item.
Expected     : The base unit shows ar «كرتونة» / en "Carton", and the tax class shows ar «خاضع لنسبة صفر» / en "Zero-rated": the labels the owner's lookup returns (SRS A6). The brand shows the raw code RETIRED_177 in both languages, and neither «علامة متوقفة» nor "Retired brand": the owner's read answers active values only (ADR-CAT-073, MODIFIED at the seventh gate-analysis revise, G1).
Test data    : CARTON, ZERO_RATED (seeded); RETIRED_177 «علامة متوقفة» / "Retired brand" (this case's own host value, `<VALUE>_<TC seq>`)
<!-- TC:TC-CAT-177:END -->

<!-- TC:TC-CAT-178:START traces=UXD-CAT-001,REQ-CAT-073,REQ-CAT-076,AC-CAT-078,SCR-CAT-001,API-CAT-052 -->
### TC-CAT-178 — A failed lookup read shows the raw code and disables the select
Derived from : UXD-CAT-001  (REQ-CAT-073, REQ-CAT-076, AC-CAT-078)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (CAT_TAX_CLASS-LOOKUP → API-CAT-052 GET /api/v1/cat/lookups/{key})
Rule / code  : — (platform generic message, §3.0 of the frontend plan)
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: F0 · the read of API-CAT-052 for CAT_TAX_CLASS fails (the mock server answers 500 CAT-500-SERVER-ERROR) · an Item with taxClassCode ZERO_RATED
Host data    : none
Steps        : 1. Open the Item.
Expected     : The tax class field shows the raw code ZERO_RATED, its select is disabled, and the platform's generic message is shown. The rest of the entry renders, with no blank screen or crash.
Test data    : ZERO_RATED (seeded)
<!-- TC:TC-CAT-178:END -->

<!-- TC:TC-CAT-179:START traces=UXD-CAT-002,REQ-CAT-012,REQ-CAT-014,AC-CAT-014,REQ-CAT-058,AC-CAT-174,SCR-CAT-001 -->
### TC-CAT-179 — Capability flags on render the flag-bound panes
Derived from : UXD-CAT-002  (REQ-CAT-012, REQ-CAT-014, AC-CAT-014)
Exercises    : SCR-CAT-001 `/cat/items/:itemPk` (CAPABILITY-FLAGS-INIT, platform app-config)
Rule / code  : —
Package      : F4-SCR-CAT-001
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: F0 (app-config reports `variants` and `bundles` on) · a BUNDLE Item whose lines are rice × 0.500 (component Item base unit KILOGRAM) and cup × 2 (component Item base unit PIECE), and a STANDARD Item
Host data    : none
Steps        : 1. Open `/cat/items` (search). 2. Open the STANDARD Item. 3. Open the BUNDLE Item.
Expected     : 1 → the Item kind filter is shown. 2 → `VariantsPane` with "Generate variants" is shown. 3 → `BundleComponentsPane` is shown; the rice line reads 0.500 followed by the unit ar «كيلوجرام» / en "Kilogram", and the cup line 2 followed by ar «قطعة» / en "Piece" — the CAT_UOM labels of each line's `componentBaseUomCode` (AC-CAT-174, ADR-CAT-080; MODIFIED at the eleventh gate-analysis revise, G2).
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
