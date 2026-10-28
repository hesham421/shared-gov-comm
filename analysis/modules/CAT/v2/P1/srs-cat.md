# SRS — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module : CAT   Version : v2   Profile : commerce
Inputs : prd, domain-profile, project-registry (PRD approved 2026-10-26 — gate prd-approval)
Counts : REQ 148 · AC 171 · ENT 12 · RULE 53 · SCR-REQ 6 · ADR 21
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information

| Item | Value |
|---|---|
| Module | CAT — Catalog / الكتالوج |
| Feature code | CAT |
| Version | v2 (baseline v1; change set CS-CAT-001 ADDITIVE). No SRS exists in the current state, so this SRS is written in full and every P1 sequence starts at 001 |
| Date | 2026-10-26 |
| Status | DRAFT — P1 pass 1 |
| Prepared by | claude:opus (analysis lane) |
| Decisions applied | 21 ADR (ADR-CAT-038 … ADR-CAT-043; ADR-CAT-058, ADR-CAT-059, ADR-CAT-060, ADR-CAT-062 at the gate-analysis revise; ADR-CAT-063, ADR-CAT-064, ADR-CAT-065 at the second gate-analysis revise; ADR-CAT-068, ADR-CAT-069, ADR-CAT-070 at the third gate-analysis revise; ADR-CAT-072 at the sixth gate-analysis revise; ADR-CAT-074 at the seventh gate-analysis revise; ADR-CAT-076 at the eighth gate-analysis revise; ADR-CAT-077 at the ninth gate-analysis revise; ADR-CAT-078 at the tenth gate-analysis revise) · 15 DEFAULT (D-01 … D-15) |
| Revisions (third gate-analysis revise) | ADDED: REQ-CAT-136 … REQ-CAT-138, AC-CAT-153 … AC-CAT-160 · MODIFIED: REQ-CAT-135 and RULE-CAT-053 (three repair operations stay allowed on an inactive Item — ADR-CAT-068), RULE-CAT-003, AC-CAT-007 and AC-CAT-043 (the message names the existing Item's code), RULE-CAT-016, RULE-CAT-023 and RULE-CAT-024 (also checked on reactivation — ADR-CAT-069), A7 |
| Revisions (fourth gate-analysis revise) | ADDED: REQ-CAT-139, AC-CAT-161 (an import past its time limit is rolled back whole — ADR-CAT-071) · MODIFIED: REQ-CAT-005 and A7 (deactivating an Item leaves its barcodes active; a GTIN is released by deactivating the barcode), RULE-CAT-049 (traces REQ-CAT-139) |
| Revisions (sixth gate-analysis revise) | ADDED: AC-CAT-162 (reactivation refused when another active Item took the name), AC-CAT-163 and AC-CAT-164 (the default Variant moves — ADR-CAT-072) · MODIFIED: REQ-CAT-025, A7 and ENT-CAT-002.isDefaultFl (an active Item always has exactly one active default Variant), REQ-CAT-130 and RULE-CAT-003 (RULE-CAT-003 named on reactivation), RULE-CAT-029 (a GTIN repeated in one import file), AC-CAT-096 (scans the code of the precondition's SCALE barcode), AC-CAT-145 (the read of an inactive Variant returns no active barcode) |
| Revisions (seventh gate-analysis revise) | ADDED: REQ-CAT-140 and REQ-CAT-141 (split from REQ-CAT-025: one REQ per default-Variant move), REQ-CAT-142, REQ-CAT-143 and REQ-CAT-144 (split from REQ-CAT-130: one REQ per reactivation refusal), REQ-CAT-145, REQ-CAT-146 and REQ-CAT-147 (split from REQ-CAT-135: one REQ per repair allowed on an inactive Item) · MODIFIED: REQ-CAT-025, REQ-CAT-130 and REQ-CAT-135 (each keeps one behaviour), AC-CAT-163 and AC-CAT-164 re-pointed to REQ-CAT-140 / 141, AC-CAT-133, AC-CAT-134 and AC-CAT-162 to REQ-CAT-142 / 143 / 144, AC-CAT-154, AC-CAT-155 and AC-CAT-156 to REQ-CAT-145 / 146 / 147 (no AC renumbered), RULE-CAT-003, 019, 026 and 053 traces, A7, REQ-CAT-139 rationale (the 60-second figure is a measured target — ADR-CAT-074) |
| Revisions (eighth gate-analysis revise) | ADDED: none · MODIFIED: RULE-CAT-038 (the file part is enforced at upload by the FILE category CAT_ITEM_IMAGE; CAT refuses the 21st image), REQ-CAT-110 rationale, AC-CAT-112 (the 7 MB JPG refused by the image category, the 21st image by RULE-CAT-038), A9 given-usage (FileDocumentLookupApi lists only its published methods; the tenant row registers the image category) — ADR-CAT-076 |
| Revisions (ninth gate-analysis revise) | ADDED: AC-CAT-165 (a Category deactivation publishes one catalogue-change event — ADR-CAT-077), AC-CAT-166 (an import row whose GTIN begins with an active scale prefix is rejected) · MODIFIED: REQ-CAT-008 and RULE-CAT-049 (Category changes publish catalogue-change events — ADR-CAT-077), RULE-CAT-003 (the removed marks are named by code point; Arabic-Indic digits are kept), RULE-CAT-032 (also on import row), RULE-CAT-045 (also checks RULE-CAT-032) |
| Revisions (tenth gate-analysis revise) | ADDED: REQ-CAT-148 and AC-CAT-171 (the barcode label screen generates the missing internal barcode itself — ADR-CAT-078), AC-CAT-167 … AC-CAT-170 (four more parallel-request cases: a barcode add and a scale-layout create, an Item unit deactivation and a barcode add, a Variant generation and a value deactivation, an Item save and its Category's deactivation) · MODIFIED: RULE-CAT-018 and RULE-CAT-027 (the quantity and the conversion factor are bounded above), REQ-CAT-146 rationale (RULE-CAT-003 is checked again at reactivation), SCR-REQ-CAT-005 B1 / B3 / B5 |

## A2 — Functional context

### In scope
The catalogue that every channel uses, i.e. what is sold: Items, Variants, Variant attributes and their values, Categories, Bundles (component lines), Item units, Barcodes (GTIN, internal, scale codes), Item media, publish state, tax class and e-invoice data on Items and Variants, the e-invoice unit of each unit of measure, scale label layouts, Excel import of Items, barcode label printing, the catalogue search used by the global search, and catalogue-change events.

### Out of scope
- Prices, promotions and price labels (PRC). CAT holds no price [DP §7.2].
- Stock quantities, lots and dual-quantity movements (INV). CAT holds no stock quantity [DP §7.2].
- Serial numbers and warranty (SWR).
- Owning the capability flags, the activity presets and the setup wizard (owner OPEN, P-02 — ADR-CAT-041).
- Maintaining lookup values: brands and units of measure are kept in the core lookup module (`erp-core:modules.mdl`). CAT uses them and does not rebuild them [DP §5 G6].
- Importing Variants, Bundles or media from Excel. The v2 import takes one Item, with its default Variant, per row.
- Pre-assembled kits, activity-specific catalogue models and multi-vendor catalogues (business-policies SCOPE EXCEPTIONS, per the PRD DEFERRED note).

### Module function
CAT is the single catalogue of a tenant. The owner defines an Item once, and the back-office, the point of sale and the storefront all read that same record. CAT is first in the fixed module order and depends on no business module; downstream modules read it and react to its catalogue-change events [KB:raw-idea.md §5]. What the catalogue does depends only on the capability flags `variants` and `bundles` [KB:raw-idea.md §3 D2, §4.1].

### Detailed description
- **Owner** creates Items with only the Arabic name to start; the other mandatory entries are pre-filled. Under "Advanced" the owner adds the English name, Category, brand, descriptions and e-invoice data. When the `variants` flag is on, the owner describes an Item by up to three Variant attributes, and the system creates one Variant per combination. When the `bundles` flag is on, the owner builds Bundles from existing Variants. The owner also adds units (for example a carton of 12 pieces) and barcodes, uploads images, publishes Items to the storefront, imports an existing item list from Excel at the setup wizard's item-import step, and maintains the catalogue settings (scale label layouts and the e-invoice unit of each unit).
- **Seller/cashier** finds Items through the global search or by scanning a barcode or a scale label, and reads the same Item the back-office maintains.
- **Warehouse staff** read Items, look Variants up by barcode, and print barcode labels.
- **Storefront shopper** (customer role) sees only active, published Items through the storefront module, which reads the catalogue.

### Current situation
| Step | Party | Notes |
|---|---|---|
| Item kept in a spreadsheet or a separate POS list | Owner | Same item typed again in each tool [KB:raw-idea.md §1] |
| Online offer kept in a separate e-commerce back-end | Owner | Changes reach each channel by hand |
| Barcodes and weighed goods keyed at the counter | Seller/cashier | No shared barcode list |

### Current difficulties
The same item has to be maintained in three places. Names and codes drift between channels. Setup is slow, because tools demand many mandatory fields. Tools also force functions (variants, kits) on businesses that do not use them [KB:raw-idea.md §2, §6].

### Proposed system and benefits
- One catalogue record per Item, read by every channel (US-CAT-001, US-CAT-002).
- Behaviour driven by flags, with only the enabled functions shown (US-CAT-003, US-CAT-004).
- At most four mandatory entries per screen, with the Item's code assigned automatically (US-CAT-007, US-CAT-009) [KB:raw-idea.md §6.1].
- Excel import inside the setup wizard (US-CAT-010) [KB:raw-idea.md §6.9].

### General notes
- Tenancy is handled by the given core; CAT is tenant-agnostic [DP §5 G4].
- Every error message offers a next action [KB:raw-idea.md §6.5].
- Every identifier is a BIGINT identity the database assigns.
- Lookup values are loaded at runtime from `mdl`, and enumerations are never hard-coded.
- Document and business numbers come from the core sequence module.
- Change history is provided by the given `audit` and has no CAT requirement (PRD P-5).

## A3 — Entities and fields

**Standard fields per kind (profile `conventions.entity_defaults`), listed once and carried by every entity of the kind:**
- master: nameAr, nameEn, code, isActiveFl, createdBy, createdAt, updatedBy, updatedAt
- lookup: code, nameAr, nameEn, sortOrder, isActiveFl
- config: key, valueAr, valueEn, isActiveFl
- Audit fields (createdBy, createdAt, updatedBy, updatedAt) are filled by the system and never accepted from a caller.
- Dependent records (ENT-CAT-005, 007, 008, 009, 010) take their parent's kind, under ADR-CAT-039. They carry isActiveFl and the audit fields. Their code and names are either the parent's or are named in their own table.

Primary key naming follows `{entity}Pk`. Logical types are: text, number, decimal, flag, date-time, lookup, reference, identifier (BIGINT identity).

### ENT-CAT-001 — Item / صنف
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — the catalogue record that every Variant belongs to | SHARED (owner) | yes — `code`, by test (b): US-CAT-009 asks for it, and it appears on labels and receipts | create, read, search, update, deactivate, reactivate, publish, unpublish, import | read by PRC, INV, PUR, SLS, SFR, INS (they declare it in their own SRS) | DP §7.1; registry CAND-CAT-001; US-CAT-001 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| itemPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| code | text (≤ 20) | yes | core number series CAT_ITEM (D-11) | assigned on first save and read-only after (RULE-CAT-004) | الرمز | Code |
| nameAr | text (≤ 200) | yes | entered | RULE-CAT-001, RULE-CAT-003 | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 200) | no | entered | RULE-CAT-002; nameAr is returned when empty (REQ-CAT-029) | الاسم بالإنجليزية | English name |
| itemKindCode | lookup | yes | CAT_ITEM_KIND, default STANDARD | read-only after first save (RULE-CAT-022) | نوع الصنف | Item kind |
| baseUomCode | lookup | yes | CAT_UOM, default PIECE (D-09) | read-only after first save (RULE-CAT-007) | وحدة القياس الأساسية | Base unit of measure |
| categoryFk | reference | no | ENT-CAT-006 | RULE-CAT-026 | الفئة | Category |
| brandCode | lookup | no | CAT_BRAND | RULE-CAT-006 | العلامة التجارية | Brand |
| taxClassCode | lookup | yes | CAT_TAX_CLASS, default STANDARD (ADR-CAT-040) | RULE-CAT-006 | التصنيف الضريبي | Tax class |
| einvoiceCodeTypeCode | lookup | no | CAT_EINVOICE_ITEM_CODE_TYPE | RULE-CAT-042 | نوع رمز الفاتورة الإلكترونية | E-invoice code type |
| einvoiceCode | text (≤ 100) | no | entered | RULE-CAT-042 | رمز الفاتورة الإلكترونية | E-invoice code |
| descriptionAr | text (≤ 2000) | no | entered | — | الوصف بالعربية | Arabic description |
| descriptionEn | text (≤ 2000) | no | entered | — | الوصف بالإنجليزية | English description |
| publishStateCode | lookup | yes | CAT_PUBLISH_STATE, default UNPUBLISHED | RULE-CAT-041 | حالة النشر | Publish state |
| isActiveFl | flag | yes | default true | RULE-CAT-048 | نشط | Active |

### ENT-CAT-002 — Variant / متغيّر الصنف
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — a concrete version of an Item that can be sold and stocked; every Item has at least one (its default Variant) | SHARED (owner) | yes — `code`, by test (a): it is printed on labels and read by the point of sale | create (default, generate), read, search, update (e-invoice override), deactivate | read by PRC, INV, PUR, SLS, SFR (stock, price and barcode attach to the Variant) | DP §8 P-04; registry CAND-CAT-002; US-CAT-011 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| variantPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| itemFk | reference | yes | ENT-CAT-001 | fixed after creation | الصنف | Item |
| code | text (≤ 20) | yes | core number series CAT_VARIANT (D-11) | read-only (RULE-CAT-004) | الرمز | Code |
| nameAr | text (≤ 400) | yes | derived at read: Item nameAr, then the value nameAr of each assignment in attribute sortOrder, joined by " / " (D-15) | not entered | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 400) | no | derived the same way from the nameEn values, with nameAr used where nameEn is empty | not entered | الاسم بالإنجليزية | English name |
| isDefaultFl | flag | yes | true for exactly one Variant of the Item: the Variant created with the Item, until the default moves (REQ-CAT-140, REQ-CAT-141, ADR-CAT-072) | — | المتغيّر الأساسي | Default variant |
| einvoiceCodeTypeCode | lookup | no | CAT_EINVOICE_ITEM_CODE_TYPE | override (RULE-CAT-042) | نوع رمز الفاتورة الإلكترونية | E-invoice code type |
| einvoiceCode | text (≤ 100) | no | entered | override (RULE-CAT-042) | رمز الفاتورة الإلكترونية | E-invoice code |
| isActiveFl | flag | yes | default true | RULE-CAT-015, RULE-CAT-021, RULE-CAT-048 | نشط | Active |

### ENT-CAT-003 — Variant attribute / خاصية المتغيّر
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| lookup — a dimension that Variants differ by (size, colour). The tenant defines the attributes themselves at runtime, each with its own nested value list that Variant assignments reference by foreign key, so it is an entity rather than an mdl key (ADR-CAT-070) | PRIVATE | no — code entered by the owner | create, read, list, update, deactivate, reactivate | none (consumers read the names through Variant reads) | registry CAND-CAT-003; DP §8 P-03 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| variantAttributePk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| code | text (≤ 30) | yes | entered | RULE-CAT-016 | الرمز | Code |
| nameAr | text (≤ 200) | yes | entered | RULE-CAT-001, RULE-CAT-016 | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 200) | no | entered | RULE-CAT-002 | الاسم بالإنجليزية | English name |
| sortOrder | number | yes | default: next number in the list | — | الترتيب | Sort order |
| isActiveFl | flag | yes | default true | RULE-CAT-017 | نشط | Active |

### ENT-CAT-004 — Variant attribute value / قيمة خاصية المتغيّر
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| lookup — one value of a Variant attribute (for example M, أحمر); held in CAT with its attribute (ADR-CAT-070) | PRIVATE | no | create, read, list, update, deactivate, reactivate | none | US-CAT-011; DP §8 P-03 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| variantAttributeValuePk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| variantAttributeFk | reference | yes | ENT-CAT-003 | fixed after creation | الخاصية | Attribute |
| code | text (≤ 30) | yes | entered | RULE-CAT-016 | الرمز | Code |
| nameAr | text (≤ 200) | yes | entered | RULE-CAT-001, RULE-CAT-016 | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 200) | no | entered | RULE-CAT-002 | الاسم بالإنجليزية | English name |
| sortOrder | number | yes | default: next number within the attribute | — | الترتيب | Sort order |
| isActiveFl | flag | yes | default true | RULE-CAT-017 | نشط | Active |

### ENT-CAT-005 — Variant attribute assignment / إسناد قيمة خاصية للمتغيّر
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of ENT-CAT-002, ADR-CAT-039) — the value a Variant carries for one attribute | PRIVATE | no | create (through generation), read, deactivate (with its Variant) | none | US-CAT-011 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| variantAttributeAssignmentPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| variantFk | reference | yes | ENT-CAT-002 | — | المتغيّر | Variant |
| variantAttributeFk | reference | yes | ENT-CAT-003 | one per Variant (RULE-CAT-013) | الخاصية | Attribute |
| variantAttributeValueFk | reference | yes | ENT-CAT-004 of the same attribute | RULE-CAT-013 | القيمة | Value |
| isActiveFl | flag | yes | follows the Variant | RULE-CAT-010 | نشط | Active |

### ENT-CAT-006 — Category / فئة
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master — a node in the tenant's item classification tree | SHARED (owner) | no — code entered by the owner | create, read, list (tree), update, deactivate, reactivate | read by SFR (browse) and INS (reporting) | DP §8 P-05; registry CAND-CAT-004; US-CAT-013 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| categoryPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| code | text (≤ 30) | yes | entered | RULE-CAT-024 | الرمز | Code |
| nameAr | text (≤ 200) | yes | entered | RULE-CAT-001, RULE-CAT-024 | الاسم بالعربية | Arabic name |
| nameEn | text (≤ 200) | no | entered | RULE-CAT-002 | الاسم بالإنجليزية | English name |
| parentCategoryFk | reference | no | ENT-CAT-006 | empty means top level; RULE-CAT-023 | الفئة الأم | Parent category |
| sortOrder | number | yes | default: next number under the parent | — | الترتيب | Sort order |
| isActiveFl | flag | yes | default true | RULE-CAT-025, RULE-CAT-048 | نشط | Active |

### ENT-CAT-007 — Bundle component / مكوّن الحزمة
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of a BUNDLE Item, ADR-CAT-039) — one component Variant of a Bundle and its quantity | SHARED (owner) | no | create, read, update (quantity), deactivate (remove line) | read by INV and SLS to resolve a Bundle into its components (DP §8 P-06) | US-CAT-012; registry CAND-CAT-005 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| bundleComponentPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| bundleItemFk | reference | yes | ENT-CAT-001 of kind BUNDLE | — | الحزمة | Bundle |
| componentVariantFk | reference | yes | ENT-CAT-002 | RULE-CAT-019, RULE-CAT-021 | المتغيّر المكوِّن | Component variant |
| quantity | decimal (3 decimals) | yes | entered, default 1 | RULE-CAT-018 | الكمية | Quantity |
| sortOrder | number | yes | default: line order | — | الترتيب | Sort order |
| isActiveFl | flag | yes | default true | RULE-CAT-048 | نشط | Active |

### ENT-CAT-008 — Item unit / وحدة الصنف
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of ENT-CAT-001, ADR-CAT-039) — an extra unit an Item is bought or sold in, with its factor to the base unit | SHARED (owner) | no | create, read, update (purchase/sales flags), deactivate | read by INV, PUR, SLS for quantity conversion | US-CAT-015 (registry AUTO "Item unit conversion owned by CAT") |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| itemUnitPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| itemFk | reference | yes | ENT-CAT-001 | — | الصنف | Item |
| uomCode | lookup | yes | CAT_UOM | RULE-CAT-027 | وحدة القياس | Unit of measure |
| conversionFactor | decimal (6 decimals) | yes | entered: base units in one of this unit (carton = 12) | read-only after saving (RULE-CAT-028) | معامل التحويل | Conversion factor |
| isPurchaseFl | flag | yes | default true | — | للشراء | Used for purchasing |
| isSalesFl | flag | yes | default true | — | للبيع | Used for selling |
| isActiveFl | flag | yes | default true | RULE-CAT-048 | نشط | Active |

### ENT-CAT-009 — Barcode / باركود
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of ENT-CAT-002, ADR-CAT-039) — a scannable code identifying a Variant in one unit | SHARED (owner) | no (an internal barcode takes its digits from the core number series, D-05) | create (GTIN, internal, scale), read, lookup, deactivate | read by SLS (scanning at the point of sale) and PUR (receiving) | DP §8 P-07; registry CAND-CAT-007; US-CAT-016, US-CAT-017 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| barcodePk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| variantFk | reference | yes | ENT-CAT-002 | — | المتغيّر | Variant |
| barcodeValue | text (≤ 48) | yes | entered (GTIN, SCALE) or generated (INTERNAL) | serves as this record's code; read-only after saving; RULE-CAT-029 … RULE-CAT-033 | الباركود | Barcode |
| barcodeTypeCode | lookup | yes | CAT_BARCODE_TYPE | — | نوع الباركود | Barcode type |
| uomCode | lookup | yes | CAT_UOM, default the Item's baseUomCode | RULE-CAT-031 | وحدة القياس | Unit of measure |
| isActiveFl | flag | yes | default true | RULE-CAT-048 | نشط | Active |

### ENT-CAT-010 — Item media / وسائط الصنف
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| master (dependent of ENT-CAT-001, ADR-CAT-039) — one image of an Item (optionally bound to a Variant). The file itself is kept by the given `file` module | PRIVATE | no | create (upload), read, update, deactivate (remove) | none (SFR reads images through Item and Variant reads) | DP §8 P-12; registry CAND-CAT-006; US-CAT-019 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| itemMediaPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| itemFk | reference | yes | ENT-CAT-001 | — | الصنف | Item |
| variantFk | reference | no | ENT-CAT-002 of the same Item | RULE-CAT-040 | المتغيّر | Variant |
| fileDocumentRef | reference | yes | given FileDocument (`erp-core:modules.file`, public image storage) — GIVEN, no XM edge | RULE-CAT-038 | ملف الصورة | Image file |
| nameAr | text (≤ 200) | yes | image description, default the Item's nameAr | RULE-CAT-001 | وصف الصورة بالعربية | Arabic image description |
| nameEn | text (≤ 200) | no | image description | RULE-CAT-002 | وصف الصورة بالإنجليزية | English image description |
| sortOrder | number | yes | default: next number for the Item | — | الترتيب | Sort order |
| isPrimaryFl | flag | yes | true for exactly one active image (RULE-CAT-039) | — | الصورة الرئيسية | Main image |
| isActiveFl | flag | yes | default true | RULE-CAT-038 | نشط | Active |

### ENT-CAT-011 — Scale label layout / إعداد ملصق الميزان
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| config — the tenant's layout for the 13-digit labels its scale prints (ADR-CAT-042) | PRIVATE | no | create, read, update, deactivate | none | US-CAT-017; module-registry OI-10 (tenant setting, GS1 restricted-circulation prefixes) |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| scaleLabelLayoutPk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| key | text (2 digits) | yes | entered: the label prefix (02 or 20–29) | fixed after creation; RULE-CAT-034 | البادئة | Prefix |
| valueAr | text (≤ 200) | no | layout description, default «ملصق ميزان {key}» | — | الوصف بالعربية | Arabic description |
| valueEn | text (≤ 200) | no | layout description, default "Scale label {key}" | — | الوصف بالإنجليزية | English description |
| scaleCodeLength | number (4–6) | yes | default 5 | fixed after creation, because the SCALE barcodes of the layout were validated against it (ADR-CAT-064); the embedded value takes 10 − scaleCodeLength digits | طول رمز الميزان | Scale code length |
| embeddedValueTypeCode | lookup | yes | CAT_SCALE_VALUE_TYPE, default WEIGHT | — | نوع القيمة المضمّنة | Embedded value type |
| valueDecimals | number (0–3) | yes | default 3 | — | الأرقام العشرية | Decimals |
| isActiveFl | flag | yes | default true | — | نشط | Active |

### ENT-CAT-012 — Unit e-invoice code / رمز الوحدة في الفاتورة الإلكترونية
| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| config — the e-invoice unit that one unit of measure carries (ADR-CAT-042) | PRIVATE | no | create, read, update, deactivate | read through Variant and Item unit reads by SLS and TAX | US-CAT-021 |

| Field | Logical type | Required | Values / source | Notes | Label-ar | Label-en |
|---|---|---|---|---|---|---|
| unitEinvoiceCodePk | identifier (BIGINT identity) | yes | assigned by the database | — | المعرّف | Identifier |
| key | lookup | yes | CAT_UOM value (the unit of measure) | RULE-CAT-043 | وحدة القياس | Unit of measure |
| valueAr | text (≤ 200) | no | derived from the unit's nameAr | not entered | الاسم بالعربية | Arabic name |
| valueEn | text (≤ 200) | no | derived from the unit's nameEn | not entered | الاسم بالإنجليزية | English name |
| einvoiceUnitCode | lookup | yes | CAT_EINVOICE_UNIT; seeded per D-12 | RULE-CAT-043 | وحدة الفاتورة الإلكترونية | E-invoice unit |
| isActiveFl | flag | yes | default true | — | نشط | Active |

## A4 — Functional requirements (EARS) and acceptance criteria

### REQ-CAT-001 — One catalogue record for every channel
Pattern    : ubiquitous
Statement  : The system shall serve every Item and its Variants to the back-office, the point of sale and the storefront from one catalogue record per Item.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : The owner never maintains the same Item in three places.
Source     : US-CAT-001; POL-CAT-001; KB:raw-idea.md §1
Priority   : HIGH

#### AC-CAT-001 — [REQ-CAT-001]
Given : an Item with code ITM-000001 created from the back-office
When  : the point-of-sale read and the storefront read request that Item by its itemPk
Then  : both reads return the same itemPk, code ITM-000001 and nameAr as the back-office read, and the tenant holds exactly 1 Item record with that code

### REQ-CAT-002 — Read an Item
Pattern    : event
Statement  : When a caller requests an Item by its identifier, the system shall return the Item with its active Variants, Variant attribute assignments, Item units, barcodes, Bundle component lines and active media.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Rationale  : Every channel reads the whole catalogue entry in one request.
Source     : US-CAT-001
Priority   : HIGH

#### AC-CAT-002 — [REQ-CAT-002]
Given : an Item with 2 active Variants, 1 inactive Variant, 1 active Item unit and 3 active barcodes
When  : a caller requests the Item by its itemPk
Then  : the response holds the Item, exactly the 2 active Variants, the 1 Item unit and the 3 barcodes

### REQ-CAT-003 — Search Items
Pattern    : event
Statement  : When a user searches Items with any of the filters text, Category, brand, kind, publish state and active state, the system shall return a page of the matching Items sorted by nameAr.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : Back-office list of the single catalogue.
Source     : US-CAT-001
Priority   : HIGH

#### AC-CAT-003 — [REQ-CAT-003]
Given : 30 Items, of which 12 belong to the Category «هواتف»
When  : a user searches with that Category as filter and a page size of 10
Then  : page 1 holds 10 Items, the total count is 12, and the Items are sorted by nameAr ascending

### REQ-CAT-004 — Update an Item
Pattern    : event
Statement  : When an owner submits changes to an active Item, the system shall save the changed nameAr, nameEn, categoryFk, brandCode, taxClassCode, descriptionAr and descriptionEn values.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : The Item is changed once, in one place.
Source     : US-CAT-001
Priority   : HIGH

#### AC-CAT-004 — [REQ-CAT-004]
Given : an active Item with an empty brandCode
When  : the owner sets brandCode to an active CAT_BRAND value and saves
Then  : the next read returns that brandCode, and updatedBy holds the owner's user identifier

### REQ-CAT-005 — Deactivate an Item
Pattern    : event
Statement  : When an owner deactivates an Item, the system shall set its isActiveFl to false and its publishStateCode to UNPUBLISHED, and leave its Variants and their barcodes active.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-009
Rationale  : An Item that is no longer sold leaves every channel but stays readable by earlier documents. Its barcodes stay active so that the Item can be reactivated with them: while the Item is inactive they no longer resolve (REQ-CAT-086) but still hold their values under RULE-CAT-029. The owner releases a GTIN by deactivating the barcode (REQ-CAT-085, API-CAT-016), which stays allowed on an inactive Item (RULE-CAT-053 never refuses a child deactivation). Deactivating a Variant, by contrast, deactivates its barcodes (REQ-CAT-050).
Source     : US-CAT-001; RULE-CAT-041; RULE-CAT-048; ADR-CAT-063
Priority   : HIGH

#### AC-CAT-005 — [REQ-CAT-005]
Given : an active Item with publishStateCode PUBLISHED
When  : the owner deactivates it
Then  : isActiveFl is false, publishStateCode is UNPUBLISHED, a read by itemPk still returns the Item, and the response shows ar: «تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة.» · en: "Deactivated. The record stays available to earlier documents and no longer appears in new selections."

### REQ-CAT-006 — Reactivate an Item
Pattern    : event
Statement  : When an owner reactivates an inactive Item that meets the ACTIVE-state invariants of A7, the system shall set its isActiveFl to true and keep its publishStateCode UNPUBLISHED.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-006, ENT-CAT-007
Rationale  : An Item comes back without reappearing on the storefront by accident, and in a state an active Item may hold.
Source     : US-CAT-001; ADR-CAT-059
Priority   : HIGH

#### AC-CAT-006 — [REQ-CAT-006]
Given : an inactive STANDARD Item with 1 active Variant, no Category, and no other active Item with its nameAr
When  : the owner reactivates it
Then  : isActiveFl is true, publishStateCode is UNPUBLISHED, and the Item read returns exactly 1 active Variant

### REQ-CAT-007 — Same Item entered twice
Pattern    : unwanted
Statement  : If a create or update request gives an Item the nameAr of another active Item of the tenant, then the system shall reject the request with RULE-CAT-003.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : Prevents one Item from being kept as two records.
Source     : US-CAT-001; D-13
Priority   : HIGH

#### AC-CAT-007 — [REQ-CAT-007]
Given : an active Item named «شاحن سامسونج 25W» with code ITM-000012
When  : the owner creates another Item named «شاحن سامسونج 25W»
Then  : no Item is created and the response shows ar: «يوجد صنف نشط بالاسم نفسه: ITM-000012. افتح الصنف الموجود أو غيّر الاسم.» · en: "An active item already has this name: ITM-000012. Open the existing item or change the name.", naming the existing Item's code as the next action

#### AC-CAT-149 — [REQ-CAT-007]
Given : no active Item named «كوب زجاج»
When  : two requests that each create an Item named «كوب زجاج» are submitted in parallel
Then  : exactly 1 Item named «كوب زجاج» is created, and the other request is refused with code CAT-409-ITEM-NAME-DUPLICATE

### REQ-CAT-008 — Publish catalogue-change events
Pattern    : event
Statement  : When a change to an Item, Variant, Variant attribute assignment, Category, Bundle component, Item unit, barcode or Item media is committed, the system shall publish one catalogue-change event carrying the record identifier, the itemPk (empty for a Category) and the change kind.
Traces     : US-CAT-002
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-006, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Rationale  : Downstream modules react to catalogue changes without CAT reading them. The Category is a SHARED entity read by SFR (browse) and INS (reporting), so its creation, change, deactivation and reactivation are signalled too (RULE-CAT-048, ADR-CAT-077).
Source     : US-CAT-002; module-registry AUTO "CAT publishes catalogue-change events"; ADR-CAT-077
Priority   : HIGH

#### AC-CAT-008 — [REQ-CAT-008]
Given : an active Item
When  : the owner changes its nameEn and the change is committed
Then  : exactly 1 catalogue-change event is published, carrying the itemPk and change kind UPDATED

#### AC-CAT-165 — [REQ-CAT-008]
Given : an active Category with 0 active sub-categories and 0 active Items
When  : the owner deactivates it and the change is committed
Then  : exactly 1 catalogue-change event is published, carrying entity ENT-CAT-006, the categoryPk as record identifier, an empty itemPk and change kind DEACTIVATED

### REQ-CAT-009 — No event for a change that was not saved
Pattern    : unwanted
Statement  : If the transaction holding a catalogue change is rolled back, then the system shall publish no catalogue-change event for that change.
Traces     : US-CAT-002
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Channels never act on a change that does not exist.
Source     : US-CAT-002; RULE-CAT-049
Priority   : HIGH

#### AC-CAT-009 — [REQ-CAT-009]
Given : an Item update whose commit fails
When  : the transaction is rolled back
Then  : 0 catalogue-change events are published and the response shows ar: «تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة.» · en: "The change was not saved and no change notice was sent. Try again."

### REQ-CAT-010 — Committed change seen by every channel
Pattern    : ubiquitous
Statement  : The system shall return a committed catalogue change on every read that starts after the commit, whichever channel reads.
Traces     : US-CAT-002
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Every channel sells the same, current Item.
Source     : US-CAT-002
Priority   : HIGH

#### AC-CAT-010 — [REQ-CAT-010]
Given : the owner changed an Item's nameEn from "Charger" to "Charger 25W" and the change is committed
When  : the point-of-sale read and the storefront read request the Item after the commit
Then  : both reads return nameEn "Charger 25W"

### REQ-CAT-011 — Behaviour follows capability flags only
Pattern    : ubiquitous
Statement  : The system shall decide every catalogue behaviour that differs between trading activities from the capability flags `variants` and `bundles`, read through the FeatureFlags API, and never from the tenant's activity or activity preset.
Traces     : US-CAT-003
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : One codebase serves every activity (G1).
Source     : US-CAT-003; POL-CAT-002; KB:raw-idea.md §3 D2; ADR-CAT-041
Priority   : HIGH

#### AC-CAT-011 — [REQ-CAT-011]
Given : two tenants with different activities and the same values of `variants` and `bundles`
When  : each tenant creates an Item with the same entries
Then  : both responses are equal in every field except itemPk, variantPk, code and the audit fields

### REQ-CAT-012 — Flag change applied at once
Pattern    : event
Statement  : When the value of the `variants` or `bundles` capability flag changes, the system shall apply the new value from the next catalogue request onward without a restart.
Traces     : US-CAT-003
Entities   : ENT-CAT-003, ENT-CAT-007
Rationale  : The owner adjusts flags at any time.
Source     : US-CAT-003; KB:raw-idea.md §6.2
Priority   : HIGH

#### AC-CAT-012 — [REQ-CAT-012]
Given : the `variants` flag is off
When  : the owner turns it on and then creates a Variant attribute in the next request
Then  : the Variant attribute is created and returned with isActiveFl true

### REQ-CAT-013 — Flag value unreadable
Pattern    : unwanted
Statement  : If the value of a capability flag cannot be read, then the system shall refuse the operation that depends on that flag with RULE-CAT-009.
Traces     : US-CAT-003
Entities   : ENT-CAT-001, ENT-CAT-005, ENT-CAT-007
Rationale  : A flag-bound operation never runs on an unknown flag value.
Source     : US-CAT-003; ADR-CAT-041
Priority   : HIGH

#### AC-CAT-013 — [REQ-CAT-013]
Given : the settings service does not answer the `bundles` flag read
When  : the owner saves an Item of kind BUNDLE
Then  : no record is saved and the response shows ar: «تعذّر التحقق من إعدادات القدرات الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.» · en: "Capability settings could not be checked, and nothing was saved. Try again in a moment."

### REQ-CAT-014 — Variant functions hidden while off
Pattern    : state
Statement  : While the `variants` capability flag is off, the system shall exclude the Variant attribute functions, the Variant attribute fields and the Variant list from every catalogue screen, menu and search filter.
Traces     : US-CAT-004
Entities   : ENT-CAT-002, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005
Rationale  : No dead menus (G2).
Source     : US-CAT-004; POL-CAT-003; KB:raw-idea.md §4.1, §6.3
Priority   : HIGH

#### AC-CAT-014 — [REQ-CAT-014]
Given : the `variants` flag is off
When  : a seller/cashier opens the Catalog menu and the Items screen
Then  : the menu holds no "Variant attributes" entry, and the Items screen shows 0 Variant attribute fields and 0 Variant list sections

### REQ-CAT-015 — Bundle functions hidden while off
Pattern    : state
Statement  : While the `bundles` capability flag is off, the system shall exclude the BUNDLE kind, the Bundle component lines and the bundle filter from every catalogue screen, menu and search filter.
Traces     : US-CAT-004
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : No dead menus (G2).
Source     : US-CAT-004; POL-CAT-003
Priority   : HIGH

#### AC-CAT-015 — [REQ-CAT-015]
Given : the `bundles` flag is off
When  : the owner opens the Item entry and the Item search
Then  : the kind field and the kind filter are absent, and 0 Bundle component sections are shown

### REQ-CAT-016 — Flag-off operation refused
Pattern    : unwanted
Statement  : If a request creates an Item of kind BUNDLE, generates Variants, saves Bundle components, or creates or updates a Variant attribute or value while the capability flag that enables it is off, then the system shall reject the request with RULE-CAT-008.
Traces     : US-CAT-004
Entities   : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005, ENT-CAT-007
Rationale  : Hidden functions also stay closed to direct calls. Reading, deactivating and reactivating records that already exist stay allowed while the flag is off, so data that depends on a flag can always be cleaned up (POL-CAT-005 is not yet enforced by the flag writer — platform finding 2).
Source     : US-CAT-004; POL-CAT-003
Priority   : HIGH

#### AC-CAT-016 — [REQ-CAT-016]
Given : the `variants` flag is off
When  : a request creates a Variant attribute
Then  : nothing is created and the response shows ar: «هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «variants» من الإعدادات لاستخدامها.» · en: "This function is not switched on for your business. Turn on the «variants» capability flag in settings to use it."

#### AC-CAT-017 — [REQ-CAT-016]
Given : the `bundles` flag is off
When  : a request creates an Item of kind BUNDLE
Then  : nothing is created and the response shows ar: «هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «bundles» من الإعدادات لاستخدامها.» · en: "This function is not switched on for your business. Turn on the «bundles» capability flag in settings to use it."

#### AC-CAT-143 — [REQ-CAT-016]
Given : the `bundles` flag is off and an active Bundle created while it was on
When  : the owner reads the Bundle, then deactivates it
Then  : the read returns the Bundle with its component lines, the deactivation sets isActiveFl to false, and neither request answers code CAT-422-CAPABILITY-OFF

### REQ-CAT-017 — Turning variants on keeps existing Items
Pattern    : event
Statement  : When the `variants` capability flag is turned on, the system shall keep every existing Item with its default Variant, codes, barcodes and identifiers unchanged.
Traces     : US-CAT-005
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : No re-setup and no data move (G3, G8).
Source     : US-CAT-005; POL-CAT-004; KB:raw-idea.md §6.2, §6.8
Priority   : HIGH

#### AC-CAT-018 — [REQ-CAT-017]
Given : 200 Items, each with only its default Variant, while `variants` is off
When  : the owner turns `variants` on
Then  : the 200 Items return the same itemPk, variantPk, codes and barcodes as before, and 0 of these records changes its updatedAt

### REQ-CAT-018 — Existing Variants take the first values
Pattern    : event
Statement  : When Variants are generated for an Item that already has Variants, the system shall assign the first selected value of each newly used attribute to the existing Variants before it creates Variants for the remaining combinations.
Traces     : US-CAT-005
Entities   : ENT-CAT-002, ENT-CAT-005
Rationale  : Items already in use grow variants without losing their identity.
Source     : US-CAT-005; RULE-CAT-012
Priority   : HIGH

#### AC-CAT-019 — [REQ-CAT-018]
Given : an Item whose only Variant is its default Variant VAR-000010
When  : the owner selects the values S and M of the attribute «المقاس»
Then  : VAR-000010 carries the value S, 1 new Variant carries the value M, and the Item has 2 active Variants

### REQ-CAT-019 — Barcodes stay on the default Variant
Pattern    : unwanted
Statement  : If Variants are generated for an Item whose default Variant carries barcodes, then the system shall keep those barcodes on the default Variant and create the new Variants with no barcode.
Traces     : US-CAT-005
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : Goods already labelled keep scanning to the same Variant.
Source     : US-CAT-005; RULE-CAT-012
Priority   : HIGH

#### AC-CAT-020 — [REQ-CAT-019]
Given : a default Variant carrying the GTIN 6223000000014
When  : the owner generates 3 Variants for its Item
Then  : the GTIN 6223000000014 still belongs to the default Variant, the 2 new Variants carry 0 barcodes, and the response shows ar: «احتفظت المتغيّرات الحالية بأرقامها وباركوداتها وأُسندت إليها القيم الأولى المختارة.» · en: "Existing variants kept their codes and barcodes and took the first selected values."

### REQ-CAT-020 — Turning bundles on uses existing Variants
Pattern    : event
Statement  : When the `bundles` capability flag is turned on, the system shall accept every active Variant of an existing STANDARD Item as a Bundle component without changing that Variant.
Traces     : US-CAT-005
Entities   : ENT-CAT-002, ENT-CAT-007
Rationale  : Bundles are built from Items already sold.
Source     : US-CAT-005; POL-CAT-004
Priority   : HIGH

#### AC-CAT-021 — [REQ-CAT-020]
Given : an active STANDARD Item created while `bundles` was off
When  : the owner turns `bundles` on and adds its Variant to a new Bundle
Then  : the Bundle is saved with 1 component line, and the component Variant's updatedAt is unchanged

### REQ-CAT-021 — Turn-off check
Pattern    : event
Statement  : When a turn-off check is requested for the `variants` or `bundles` capability flag, the system shall return whether the turn-off is allowed, the count of dependent Items and at most 20 dependent Items by code and nameAr.
Traces     : US-CAT-006
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : The flag owner and the owner learn what blocks a turn-off and what to do next.
Source     : US-CAT-006; POL-CAT-005; ADR-CAT-041
Priority   : HIGH

#### AC-CAT-022 — [REQ-CAT-021]
Given : no active Item uses Variant attributes
When  : a turn-off check for `variants` is requested
Then  : the response returns allowed = true, a dependent count of 0 and an empty list

### REQ-CAT-022 — Variants turn-off blocked
Pattern    : unwanted
Statement  : If an active Variant attribute assignment exists on an active Variant of an active Item, then the system shall answer a turn-off check of the `variants` flag as not allowed with RULE-CAT-010.
Traces     : US-CAT-006
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : Data that depends on a flag blocks its turn-off (G3).
Source     : US-CAT-006; POL-CAT-005
Priority   : HIGH

#### AC-CAT-023 — [REQ-CAT-022]
Given : 3 active Items whose active Variants carry attribute assignments
When  : a turn-off check for `variants` is requested
Then  : the response returns allowed = false, a dependent count of 3, the 3 Items by code and nameAr, and ar: «لا يمكن إيقاف المتغيّرات لأن 3 صنفًا يستخدمها. أوقف متغيّرات هذه الأصناف أو أبقِ المتغيّرات مفعّلة.» · en: "Variants cannot be turned off because 3 items use them. Deactivate the variants of these items or keep variants on."

### REQ-CAT-023 — Bundles turn-off blocked
Pattern    : unwanted
Statement  : If an active Item of kind BUNDLE exists, then the system shall answer a turn-off check of the `bundles` flag as not allowed with RULE-CAT-011.
Traces     : US-CAT-006
Entities   : ENT-CAT-001
Rationale  : Data that depends on a flag blocks its turn-off (G3).
Source     : US-CAT-006; POL-CAT-005
Priority   : HIGH

#### AC-CAT-024 — [REQ-CAT-023]
Given : 2 active Items of kind BUNDLE
When  : a turn-off check for `bundles` is requested
Then  : the response returns allowed = false, a dependent count of 2, and ar: «لا يمكن إيقاف الحزم لوجود 2 حزمة نشطة. أوقف هذه الحزم أو أبقِ الحزم مفعّلة.» · en: "Bundles cannot be turned off because 2 active bundles exist. Deactivate these bundles or keep bundles on."

### REQ-CAT-024 — Create an Item from its Arabic name
Pattern    : event
Statement  : When an owner saves a new Item that carries only nameAr, the system shall create the Item with itemKindCode STANDARD, baseUomCode PIECE, taxClassCode STANDARD and publishStateCode UNPUBLISHED.
Traces     : US-CAT-007
Entities   : ENT-CAT-001
Rationale  : Adding Items does not slow down setup (G7).
Source     : US-CAT-007; POL-CAT-006; KB:raw-idea.md §6.1; D-09
Priority   : HIGH

#### AC-CAT-025 — [REQ-CAT-024]
Given : the Item entry with only nameAr «كوب زجاج» filled
When  : the owner saves
Then  : the Item is created with itemKindCode STANDARD, baseUomCode PIECE, taxClassCode STANDARD, publishStateCode UNPUBLISHED and isActiveFl true

### REQ-CAT-025 — Default Variant created with the Item
Pattern    : event
Statement  : When an Item is created, the system shall create one default Variant for it with isDefaultFl true.
Traces     : US-CAT-007
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Stock, price and barcode always have a Variant to attach to (D-01). The moves of the default flag are REQ-CAT-140 and REQ-CAT-141 (split from this REQ at the seventh gate-analysis revise).
Source     : US-CAT-007; DP §8 P-04; ADR-CAT-072
Priority   : HIGH

#### AC-CAT-026 — [REQ-CAT-025]
Given : no Item named «كوب زجاج» exists
When  : the owner creates it
Then  : the Item has exactly 1 Variant, with isDefaultFl true, isActiveFl true and a code from the series CAT_VARIANT

### REQ-CAT-026 — At most four mandatory entries
Pattern    : ubiquitous
Statement  : The system shall require at most four mandatory entries to create an Item, namely nameAr, baseUomCode, taxClassCode and itemKindCode, with every one of them except nameAr pre-filled.
Traces     : US-CAT-007
Entities   : ENT-CAT-001
Rationale  : Simplicity is measured as at most 4 mandatory fields per screen.
Source     : US-CAT-007; KB:raw-idea.md §3 D9, §6.1
Priority   : HIGH

#### AC-CAT-027 — [REQ-CAT-026]
Given : the `bundles` flag is on
When  : the owner opens a new Item entry
Then  : exactly 4 mandatory fields appear outside "Advanced", and 3 of them hold the values PIECE, STANDARD and STANDARD

### REQ-CAT-027 — Arabic name missing
Pattern    : unwanted
Statement  : If a create request for an Item carries no nameAr, then the system shall reject the request with RULE-CAT-001.
Traces     : US-CAT-007
Entities   : ENT-CAT-001
Rationale  : The Arabic name is the one entry the owner must give.
Source     : US-CAT-007; PRD P-3
Priority   : HIGH

#### AC-CAT-028 — [REQ-CAT-027]
Given : the Item entry with an empty nameAr
When  : the owner saves
Then  : no Item is created and the response shows ar: «أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.» · en: "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."

### REQ-CAT-028 — Brand and model text kept in the Arabic name
Pattern    : ubiquitous
Statement  : The system shall accept a nameAr that mixes Arabic letters with Latin letters, digits and symbols, and store it as written.
Traces     : US-CAT-008
Entities   : ENT-CAT-001
Rationale  : Brand and model text stays as written.
Source     : US-CAT-008; PRD P-3
Priority   : HIGH

#### AC-CAT-029 — [REQ-CAT-028]
Given : the Item entry
When  : the owner saves nameAr «سامسونج Galaxy A55 128GB»
Then  : the read returns nameAr «سامسونج Galaxy A55 128GB», character for character

### REQ-CAT-029 — English name falls back to Arabic
Pattern    : optional
Statement  : Where an Item has no nameEn, the system shall return its nameAr in place of nameEn for every English display.
Traces     : US-CAT-008
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : The English name is optional, and receipts never show an empty name.
Source     : US-CAT-008; PRD P-3
Priority   : HIGH

#### AC-CAT-030 — [REQ-CAT-029]
Given : an Item with nameAr «كوب زجاج» and an empty nameEn
When  : a reader requests the English display name
Then  : the value returned is «كوب زجاج»

### REQ-CAT-030 — Arabic name without an Arabic letter
Pattern    : unwanted
Statement  : If a nameAr has no Arabic letter or exceeds 200 characters, then the system shall reject the request with RULE-CAT-001.
Traces     : US-CAT-008
Entities   : ENT-CAT-001
Rationale  : The Arabic name is a real Arabic name (KSA VAT IR Art. 53).
Source     : US-CAT-008; D-07
Priority   : HIGH

#### AC-CAT-031 — [REQ-CAT-030]
Given : the Item entry
When  : the owner saves nameAr "Galaxy A55"
Then  : no Item is saved and the response shows ar: «أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل.» · en: "Enter the Arabic name (up to 200 characters) with at least one Arabic letter."

### REQ-CAT-031 — English name too long
Pattern    : unwanted
Statement  : If a nameEn exceeds 200 characters, then the system shall reject the request with RULE-CAT-002.
Traces     : US-CAT-008
Entities   : ENT-CAT-001
Rationale  : Bounded names on receipts and invoices.
Source     : US-CAT-008
Priority   : HIGH

#### AC-CAT-032 — [REQ-CAT-031]
Given : the Item entry
When  : the owner saves a nameEn of 201 characters
Then  : no Item is saved and the response shows ar: «الاسم بالإنجليزية لا يتجاوز 200 حرف.» · en: "The English name can have at most 200 characters."

### REQ-CAT-032 — Item code assigned automatically
Pattern    : event
Statement  : When an Item is first saved, the system shall assign it the next code of the core number series CAT_ITEM.
Traces     : US-CAT-009
Entities   : ENT-CAT-001
Rationale  : The code is never an entry the owner thinks up (NUMBERING rule).
Source     : US-CAT-009; module-registry AUTO (sequence); D-11
Priority   : HIGH

#### AC-CAT-033 — [REQ-CAT-032]
Given : the last code issued by series CAT_ITEM is ITM-000041
When  : the owner saves a new Item
Then  : the Item's code is ITM-000042

### REQ-CAT-033 — Variant code assigned automatically
Pattern    : event
Statement  : When a Variant is first saved, the system shall assign it the next code of the core number series CAT_VARIANT.
Traces     : US-CAT-009
Entities   : ENT-CAT-002
Rationale  : Variants carry a printable code without owner entry.
Source     : US-CAT-009; D-11
Priority   : HIGH

#### AC-CAT-034 — [REQ-CAT-033]
Given : the last code issued by series CAT_VARIANT is VAR-000107
When  : a Variant is created
Then  : the Variant's code is VAR-000108

### REQ-CAT-034 — Code supplied or changed
Pattern    : unwanted
Statement  : If a request supplies or changes the code of an Item or a Variant, then the system shall reject the request with RULE-CAT-004.
Traces     : US-CAT-009
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Codes stay unique and system-made.
Source     : US-CAT-009
Priority   : HIGH

#### AC-CAT-035 — [REQ-CAT-034]
Given : an Item with code ITM-000042
When  : an update request sets its code to ITM-999999
Then  : the code stays ITM-000042 and the response shows ar: «يُعيَّن الرمز تلقائيًا ولا يمكن إدخاله أو تعديله.» · en: "The code is assigned automatically and cannot be entered or changed."

### REQ-CAT-035 — Number series unavailable
Pattern    : unwanted
Statement  : If the core number series cannot issue a code for a new Item or Variant, then the system shall reject the save with RULE-CAT-005 and store no record.
Traces     : US-CAT-009
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : No Item is stored without a code.
Source     : US-CAT-009
Priority   : HIGH

#### AC-CAT-036 — [REQ-CAT-035]
Given : series CAT_ITEM returns an error
When  : the owner saves a new Item
Then  : 0 Items and 0 Variants are stored and the response shows ar: «تعذّر إصدار رمز جديد الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات.» · en: "A new code could not be issued, and nothing was saved. Try again in a moment."

### REQ-CAT-036 — Download the import sheet
Pattern    : event
Statement  : When an owner requests the item import sheet, the system shall return an xlsx file whose header row names each import column in ar and en.
Traces     : US-CAT-010
Entities   : ENT-CAT-001
Rationale  : The owner fills a known layout instead of guessing columns.
Source     : US-CAT-010; POL-CAT-007; KB:raw-idea.md §6.9
Priority   : HIGH

#### AC-CAT-037 — [REQ-CAT-036]
Given : the setup wizard's item-import step
When  : the owner requests the import sheet
Then  : an xlsx file is returned with 1 sheet whose header row holds 11 columns (nameAr, nameEn, baseUomCode, category code, brandCode, taxClassCode, einvoiceCodeTypeCode, einvoiceCode, GTIN barcode, descriptionAr, descriptionEn), each labelled in ar and en

### REQ-CAT-037 — Validate every imported row
Pattern    : event
Statement  : When an owner submits an xlsx item import file, the system shall validate every data row against RULE-CAT-045 before it creates any Item.
Traces     : US-CAT-010
Entities   : ENT-CAT-001, ENT-CAT-009
Rationale  : Imported Items meet the same rules as Items entered by hand.
Source     : US-CAT-010
Priority   : HIGH

#### AC-CAT-038 — [REQ-CAT-037]
Given : an import file of 50 data rows, of which row 7 has an empty nameAr
When  : the owner submits it
Then  : the result reports row 7 as rejected for column nameAr before any Item is created, and 49 rows as valid

### REQ-CAT-038 — Create Items from valid rows
Pattern    : event
Statement  : When the validation of an item import file ends, the system shall create one Item, its default Variant and, where the row carries a GTIN, its barcode for each valid row.
Traces     : US-CAT-010
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : The owner starts selling without re-typing the catalogue.
Source     : US-CAT-010
Priority   : HIGH

#### AC-CAT-039 — [REQ-CAT-038]
Given : an import file of 49 valid rows, 20 of which carry a GTIN
When  : validation ends
Then  : 49 Items, 49 default Variants and 20 GTIN barcodes are created

### REQ-CAT-039 — Import result
Pattern    : event
Statement  : When an item import ends, the system shall return the count of rows read, Items created and rows rejected, with the row number, column and ar/en reason of each rejected row.
Traces     : US-CAT-010
Entities   : ENT-CAT-001
Rationale  : The owner knows exactly what to fix (no dead ends).
Source     : US-CAT-010; KB:raw-idea.md §6.5
Priority   : HIGH

#### AC-CAT-040 — [REQ-CAT-039]
Given : an import of 50 rows of which 1 is rejected
When  : the import ends
Then  : the result shows rows read 50, Items created 49, rows rejected 1, and for row 7 the column nameAr with ar: «الصف 7، العمود nameAr: أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل. صحّح الصف وأعد استيراده.» · en: "Row 7, column nameAr: Enter the Arabic name (up to 200 characters) with at least one Arabic letter. Correct the row and import it again."

### REQ-CAT-040 — Import file not accepted
Pattern    : unwanted
Statement  : If an import file is not xlsx, exceeds 10 MB, holds more than 5,000 data rows or carries a header row that differs from the import sheet, then the system shall reject the whole file with RULE-CAT-044 and create no Item.
Traces     : US-CAT-010
Entities   : ENT-CAT-001
Rationale  : Bounded import inside the setup time target.
Source     : US-CAT-010; D-10
Priority   : HIGH

#### AC-CAT-041 — [REQ-CAT-040]
Given : a .csv file
When  : the owner submits it for import
Then  : 0 Items are created and the response shows ar: «الملف غير مقبول: يجب أن يكون بصيغة xlsx بحجم لا يتجاوز 10 ميجابايت وحتى 5000 صف، وبعناوين ورقة الاستيراد نفسها. نزّل ورقة الاستيراد واستخدمها.» · en: "The file was not accepted: it must be xlsx, at most 10 MB and 5,000 rows, with the import sheet headers. Download the import sheet and use it."

### REQ-CAT-041 — Failed row skipped
Pattern    : unwanted
Statement  : If a data row of an import file fails a rule, then the system shall skip that row, create the Items of the other valid rows and report the row with RULE-CAT-045.
Traces     : US-CAT-010
Entities   : ENT-CAT-001
Rationale  : One bad row never blocks the whole catalogue.
Source     : US-CAT-010
Priority   : HIGH

#### AC-CAT-042 — [REQ-CAT-041]
Given : an import file of 10 rows where row 4 carries the brandCode «XYZ», which is not an active CAT_BRAND value
When  : the owner submits it
Then  : 9 Items are created, row 4 creates nothing, and row 4 is reported for column brandCode

#### AC-CAT-043 — [REQ-CAT-041]
Given : an import file whose rows 2 and 9 both carry nameAr «كوب زجاج», with no such active Item in the tenant
When  : the owner submits it
Then  : row 2 creates the Item, row 9 is rejected for column nameAr, and the reason reads ar: «يوجد صنف نشط بالاسم نفسه: {code}. افتح الصنف الموجود أو غيّر الاسم.» · en: "An active item already has this name: {code}. Open the existing item or change the name.", with {code} the code the import gave to the Item of row 2

#### AC-CAT-166 — [REQ-CAT-041]
Given : an active scale label layout with prefix 20, and an import file of 3 valid rows whose row 2 carries the GTIN 2000000000008
When  : the owner submits it
Then  : 2 Items are created, row 2 creates nothing, and row 2 is reported for column GTIN barcode with the reason ar: «الباركود يبدأ ببادئة ملصق ميزان نشط (20). استخدم باركودًا آخر أو عدّل إعداد الميزان.» · en: "The barcode starts with the prefix of an active scale label layout (20). Use another barcode or change the scale settings."

### REQ-CAT-042 — Create a Variant attribute
Pattern    : event
Statement  : When an owner saves a new Variant attribute with a code and a nameAr, the system shall create the Variant attribute as active.
Traces     : US-CAT-011
Entities   : ENT-CAT-003
Rationale  : The tenant keeps its own list of dimensions.
Source     : US-CAT-011; POL-CAT-002
Priority   : HIGH

#### AC-CAT-044 — [REQ-CAT-042]
Given : the `variants` flag is on and no attribute with code SIZE exists
When  : the owner saves code SIZE and nameAr «المقاس»
Then  : the Variant attribute is created with isActiveFl true and sortOrder 1

### REQ-CAT-043 — Update a Variant attribute
Pattern    : event
Statement  : When an owner changes the nameAr, nameEn or sortOrder of a Variant attribute, the system shall save the change.
Traces     : US-CAT-011
Entities   : ENT-CAT-003
Rationale  : Names are corrected without touching the Variants.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-045 — [REQ-CAT-043]
Given : the Variant attribute SIZE with nameEn empty
When  : the owner sets nameEn "Size" and saves
Then  : the next read returns nameEn "Size", and the derived nameEn of every Variant carrying a SIZE value includes the value's English name

### REQ-CAT-044 — Deactivate a Variant attribute
Pattern    : event
Statement  : When an owner deactivates a Variant attribute, the system shall set its isActiveFl to false and exclude it from new Variant generation.
Traces     : US-CAT-011
Entities   : ENT-CAT-003
Rationale  : Old dimensions leave the selection lists.
Source     : US-CAT-011; RULE-CAT-017
Priority   : HIGH

#### AC-CAT-046 — [REQ-CAT-044]
Given : the Variant attribute MATERIAL_TYPE used by 0 active Variants
When  : the owner deactivates it
Then  : isActiveFl is false, and the Variant generation list offers 0 values of that attribute

### REQ-CAT-045 — Create a Variant attribute value
Pattern    : event
Statement  : When an owner saves a new value with a code and a nameAr under an active Variant attribute, the system shall create the Variant attribute value as active.
Traces     : US-CAT-011
Entities   : ENT-CAT-004
Rationale  : The tenant keeps its own values (M, أحمر).
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-047 — [REQ-CAT-045]
Given : the active Variant attribute SIZE with values S and M
When  : the owner saves value code L with nameAr «كبير»
Then  : the value L is created under SIZE with isActiveFl true and sortOrder 3

### REQ-CAT-046 — Update a Variant attribute value
Pattern    : event
Statement  : When an owner changes the nameAr, nameEn or sortOrder of a Variant attribute value, the system shall save the change.
Traces     : US-CAT-011
Entities   : ENT-CAT-004
Rationale  : Value names are corrected in one place.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-048 — [REQ-CAT-046]
Given : the value L with nameAr «كبير»
When  : the owner changes nameAr to «لارج L» and saves
Then  : the next read of every Variant carrying L returns a derived nameAr ending in «لارج L»

### REQ-CAT-047 — Deactivate a Variant attribute value
Pattern    : event
Statement  : When an owner deactivates a Variant attribute value, the system shall set its isActiveFl to false and exclude it from new Variant generation.
Traces     : US-CAT-011
Entities   : ENT-CAT-004
Rationale  : Discontinued values leave the selection lists.
Source     : US-CAT-011; RULE-CAT-017
Priority   : HIGH

#### AC-CAT-049 — [REQ-CAT-047]
Given : the value XS used by 0 active Variants
When  : the owner deactivates it
Then  : isActiveFl is false, and the Variant generation list for SIZE omits XS

### REQ-CAT-048 — List Variant attributes
Pattern    : event
Statement  : When a user opens the Variant attributes, the system shall return every Variant attribute with its values, ordered by sortOrder.
Traces     : US-CAT-011
Entities   : ENT-CAT-003, ENT-CAT-004
Rationale  : One view of the tenant's dimensions.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-050 — [REQ-CAT-048]
Given : 2 attributes (SIZE sortOrder 1 with 3 values; COLOUR sortOrder 2 with 4 values)
When  : the owner opens the Variant attributes
Then  : the response lists SIZE then COLOUR, with 3 and 4 values respectively, each list ordered by sortOrder

### REQ-CAT-049 — Generate Variants from attribute values
Pattern    : event
Statement  : When an owner selects values of one to three active Variant attributes for a STANDARD Item, the system shall create one Variant for each selected combination that the Item does not yet carry.
Traces     : US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : Every combination can be picked, counted and bought as itself.
Source     : US-CAT-011; POL-CAT-002; KB:raw-idea.md §4.1
Priority   : HIGH

#### AC-CAT-051 — [REQ-CAT-049]
Given : the STANDARD Item «قميص قطن» with only its default Variant, and `variants` on
When  : the owner selects SIZE values M and L and COLOUR values «أحمر» and «أزرق»
Then  : the Item has 4 active Variants (the default Variant takes M / «أحمر»), each with a distinct combination, and one derived nameAr reads «قميص قطن / L / أزرق»

### REQ-CAT-050 — Deactivate a Variant
Pattern    : event
Statement  : When an owner deactivates a Variant, the system shall set its isActiveFl to false, set the isActiveFl of its active attribute assignments and active barcodes to false, and keep the Variant readable by its identifier.
Traces     : US-CAT-011
Entities   : ENT-CAT-002, ENT-CAT-005, ENT-CAT-009
Rationale  : Discontinued combinations leave the channels but earlier documents still resolve them. A barcode of an inactive Variant can no longer be looked up (RULE-CAT-047), so it is released for RULE-CAT-029 instead of blocking its value (ADR-CAT-063).
Source     : US-CAT-011; RULE-CAT-048; ADR-CAT-063
Priority   : HIGH

#### AC-CAT-052 — [REQ-CAT-050]
Given : an Item with 4 active Variants
When  : the owner deactivates the Variant L / «أزرق»
Then  : the Item read returns 3 active Variants, and a read by that Variant's variantPk returns it with isActiveFl false

#### AC-CAT-145 — [REQ-CAT-050]
Given : an Item with 2 active Variants, the first carrying the active GTIN 6223000000014
When  : the owner deactivates the first Variant, then adds the GTIN 6223000000014 to a Variant of another Item
Then  : a read of the deactivated Variant returns it with isActiveFl false and with no active barcode, and the GTIN 6223000000014 is saved as an active barcode of the other Variant

### REQ-CAT-051 — Duplicate combination
Pattern    : unwanted
Statement  : If a Variant would carry the same combination of values as another active Variant of the same Item, then the system shall reject that Variant with RULE-CAT-013.
Traces     : US-CAT-011
Entities   : ENT-CAT-002, ENT-CAT-005
Rationale  : Each combination exists once.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-053 — [REQ-CAT-051]
Given : an Item with an active Variant M / «أحمر»
When  : a request creates another Variant M / «أحمر» on that Item
Then  : no Variant is created and the response shows ar: «هذه التركيبة من القيم موجودة في متغيّر آخر لهذا الصنف.» · en: "This combination of values already exists on another variant of this item."

### REQ-CAT-052 — Variant limits exceeded
Pattern    : unwanted
Statement  : If a generation request would give an Item more than 3 Variant attributes or more than 300 active Variants, then the system shall reject the whole request with RULE-CAT-014.
Traces     : US-CAT-011
Entities   : ENT-CAT-002, ENT-CAT-005
Rationale  : Bounded combinations per Item (D-10).
Source     : US-CAT-011; D-10
Priority   : HIGH

#### AC-CAT-054 — [REQ-CAT-052]
Given : an Item with 290 active Variants
When  : the owner generates 20 new combinations
Then  : 0 Variants are created and the response shows ar: «الحد الأقصى 3 خصائص و300 متغيّر نشط للصنف الواحد.» · en: "An item can have at most 3 attributes and 300 active variants."

#### AC-CAT-147 — [REQ-CAT-052]
Given : an Item with 280 active Variants
When  : two generation requests, each adding 15 combinations the Item does not carry, are submitted in parallel
Then  : exactly 1 request succeeds, the other is refused with code CAT-422-VARIANT-LIMIT-EXCEEDED, and the Item has 295 active Variants

### REQ-CAT-053 — Last active Variant
Pattern    : unwanted
Statement  : If an owner deactivates the last active Variant of an active Item, then the system shall reject the deactivation with RULE-CAT-015.
Traces     : US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : An active Item always has a Variant that can be sold.
Source     : US-CAT-011; DP §8 P-04
Priority   : HIGH

#### AC-CAT-055 — [REQ-CAT-053]
Given : an active Item with exactly 1 active Variant
When  : the owner deactivates that Variant
Then  : the Variant stays active and the response shows ar: «لا يمكن إيقاف آخر متغيّر نشط لصنف نشط. أوقف الصنف نفسه بدلًا من ذلك.» · en: "The last active variant of an active item cannot be deactivated. Deactivate the item instead."

#### AC-CAT-146 — [REQ-CAT-053]
Given : an active Item with exactly 2 active Variants
When  : two requests, each deactivating a different one of the 2 Variants, are submitted in parallel
Then  : exactly 1 request succeeds, the other is refused with code CAT-409-LAST-ACTIVE-VARIANT, and the Item keeps exactly 1 active Variant

### REQ-CAT-054 — Attribute or value in use
Pattern    : unwanted
Statement  : If an owner deactivates a Variant attribute or a Variant attribute value that an active Variant carries, then the system shall reject the deactivation with RULE-CAT-017.
Traces     : US-CAT-011
Entities   : ENT-CAT-003, ENT-CAT-004, ENT-CAT-005
Rationale  : Active Variants keep a valid description.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-056 — [REQ-CAT-054]
Given : the value M carried by 12 active Variants
When  : the owner deactivates M
Then  : M stays active and the response shows ar: «هذه الخاصية أو القيمة مستخدمة في 12 متغيّرًا نشطًا. أوقف هذه المتغيّرات أولًا.» · en: "This attribute or value is used by 12 active variants. Deactivate those variants first."

#### AC-CAT-169 — [REQ-CAT-054]
Given : a STANDARD Item with only its default Variant, and the active attribute SIZE with the active values M and L, L carried by 0 active Variants
When  : a request that generates Variants for the Item with selections SIZE [M, L] and a request that deactivates the value L are submitted in parallel
Then  : exactly 1 request succeeds; either the Item has a Variant carrying L and the deactivation is refused with code CAT-409-ATTRIBUTE-VALUE-IN-USE, or L is inactive, the generation is refused with code CAT-400-REQUEST-INVALID and the Item keeps only its default Variant; no active Variant carries an inactive value

### REQ-CAT-055 — Duplicate attribute or value
Pattern    : unwanted
Statement  : If a Variant attribute or a Variant attribute value repeats the code or nameAr of another one in the same scope, then the system shall reject the request with RULE-CAT-016.
Traces     : US-CAT-011
Entities   : ENT-CAT-003, ENT-CAT-004
Rationale  : Each dimension and value is named once.
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-057 — [REQ-CAT-055]
Given : the attribute SIZE exists
When  : the owner creates another attribute with code SIZE
Then  : nothing is created and the response shows ar: «الرمز أو الاسم مستخدم بالفعل. استخدم رمزًا (حتى 30 حرفًا لاتينيًا أو رقمًا أو شرطة) واسمًا مختلفين.» · en: "The code or name is already used. Use a different code (up to 30 Latin letters, digits or hyphens) and name."

### REQ-CAT-056 — Save a Bundle with its components
Pattern    : event
Statement  : When an owner saves an Item of kind BUNDLE, the system shall store its Bundle component lines, each with one component Variant and its quantity.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : A kit or promo pack is sold as one line.
Source     : US-CAT-012; POL-CAT-002; DP §8 P-06
Priority   : HIGH

#### AC-CAT-058 — [REQ-CAT-056]
Given : `bundles` is on and 2 active STANDARD Items exist (a phone and a case)
When  : the owner saves the Bundle «باقة الهاتف مع الجراب» with the phone Variant × 1 and the case Variant × 1
Then  : the Bundle Item is created with itemKindCode BUNDLE, 1 default Variant and 2 active component lines of quantity 1

### REQ-CAT-057 — Change Bundle components
Pattern    : event
Statement  : When an owner adds a component line to a Bundle, removes one or changes its quantity, the system shall save the Bundle's new component lines.
Traces     : US-CAT-012
Entities   : ENT-CAT-007
Rationale  : Packs change with promotions.
Source     : US-CAT-012
Priority   : HIGH

#### AC-CAT-059 — [REQ-CAT-057]
Given : a Bundle with 2 component lines of quantity 1
When  : the owner changes the second line's quantity to 2 and saves
Then  : the Bundle read returns 2 active lines with quantities 1 and 2

### REQ-CAT-058 — Bundle components returned to readers
Pattern    : ubiquitous
Statement  : The system shall return, with every read of a Bundle Item, its active Bundle component lines with the component Variant, its code and the quantity.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : Stock is counted on the component Variants; the Bundle keeps no stock of its own.
Source     : US-CAT-012; DP §8 P-06
Priority   : HIGH

#### AC-CAT-060 — [REQ-CAT-058]
Given : a Bundle with 3 active component lines
When  : a caller reads the Bundle Item
Then  : the response holds 3 component lines, each with componentVariantFk, the Variant code and the quantity

### REQ-CAT-059 — Bundle without a valid component
Pattern    : unwanted
Statement  : If a Bundle is saved with no active component line or with a quantity that is not above zero, then the system shall reject the save with RULE-CAT-018.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : A Bundle is made of real quantities.
Source     : US-CAT-012
Priority   : HIGH

#### AC-CAT-061 — [REQ-CAT-059]
Given : a new Bundle entry with 0 component lines
When  : the owner saves
Then  : nothing is saved and the response shows ar: «أضف مكوّنًا واحدًا على الأقل بكمية أكبر من صفر (حتى 3 أرقام عشرية).» · en: "Add at least one component with a quantity above zero (up to 3 decimals)."

### REQ-CAT-060 — Invalid Bundle component
Pattern    : unwanted
Statement  : If a component line names an inactive Variant, a Variant of a Bundle Item, or a Variant already listed in the same Bundle, then the system shall reject the save with RULE-CAT-019.
Traces     : US-CAT-012
Entities   : ENT-CAT-002, ENT-CAT-007
Rationale  : No nested Bundles, and each component is listed once (D-04).
Source     : US-CAT-012; D-04
Priority   : HIGH

#### AC-CAT-062 — [REQ-CAT-060]
Given : an active Bundle B1
When  : the owner adds the Variant of B1 as a component of a new Bundle B2
Then  : B2 is not saved and the response shows ar: «المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.» · en: "A component must be an active variant of an item that is not a bundle, listed once per bundle."

### REQ-CAT-061 — Variant attributes on a Bundle
Pattern    : unwanted
Statement  : If a request generates Variants or assigns Variant attribute values for an Item of kind BUNDLE, then the system shall reject the request with RULE-CAT-020.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : A Bundle is one sellable line with one Variant.
Source     : US-CAT-012; D-04
Priority   : HIGH

#### AC-CAT-063 — [REQ-CAT-061]
Given : a Bundle Item with its default Variant
When  : the owner selects SIZE values for it
Then  : 0 Variants are created and the response shows ar: «الحزمة لها متغيّر واحد فقط ولا تقبل خصائص المتغيّرات.» · en: "A bundle has one variant only and takes no variant attributes."

### REQ-CAT-062 — Component Variant deactivation
Pattern    : unwanted
Statement  : If an owner deactivates a Variant, or the Item of a Variant, that is a component of an active Bundle, then the system shall reject the deactivation with RULE-CAT-021.
Traces     : US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-007
Rationale  : An active Bundle never holds a component that cannot be sold.
Source     : US-CAT-012
Priority   : HIGH

#### AC-CAT-064 — [REQ-CAT-062]
Given : a Variant that is a component of 2 active Bundles
When  : the owner deactivates that Variant
Then  : the Variant stays active and the response shows ar: «هذا المتغيّر مكوّن في 2 حزمة نشطة. أزله من هذه الحزم أو أوقفها أولًا.» · en: "This variant is a component of 2 active bundles. Remove it from those bundles or deactivate them first."

#### AC-CAT-151 — [REQ-CAT-062]
Given : a Variant P of an active STANDARD Item that has a second active Variant, and an active Bundle B whose lines do not include P
When  : a request that deactivates P and a request that saves B's components with P added are submitted in parallel
Then  : exactly 1 request succeeds; either P is inactive and the Bundle save is refused with code CAT-422-BUNDLE-COMPONENT-INVALID, or B holds P and the deactivation is refused with code CAT-409-VARIANT-IN-ACTIVE-BUNDLE

### REQ-CAT-063 — Item kind changed after saving
Pattern    : unwanted
Statement  : If an update request changes the itemKindCode of a saved Item, then the system shall reject the request with RULE-CAT-022.
Traces     : US-CAT-012
Entities   : ENT-CAT-001
Rationale  : Downstream modules rely on the kind fixed at creation.
Source     : US-CAT-012; D-04
Priority   : HIGH

#### AC-CAT-065 — [REQ-CAT-063]
Given : a saved STANDARD Item
When  : an update sets itemKindCode to BUNDLE
Then  : the kind stays STANDARD and the response shows ar: «لا يمكن تغيير نوع الصنف بعد حفظه. أنشئ صنفًا جديدًا بالنوع المطلوب.» · en: "The item kind cannot be changed after saving. Create a new item of the required kind."

### REQ-CAT-064 — Create a Category
Pattern    : event
Statement  : When an owner saves a new Category with a code and a nameAr, the system shall create it as active under the chosen parent Category, or at the top level when no parent is chosen.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : Shoppers browse, and reports aggregate, by Category.
Source     : US-CAT-013; POL-CAT-001; D-03
Priority   : —

#### AC-CAT-066 — [REQ-CAT-064]
Given : the active top-level Category «إلكترونيات»
When  : the owner saves code PHONES, nameAr «هواتف» and parent «إلكترونيات»
Then  : the Category PHONES is created with isActiveFl true and parentCategoryFk pointing to «إلكترونيات»

### REQ-CAT-065 — Update a Category
Pattern    : event
Statement  : When an owner changes the nameAr, nameEn, sortOrder or parent of a Category, the system shall save the change.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : The tree is reorganised without re-entering Items.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-067 — [REQ-CAT-065]
Given : the Category PHONES under «إلكترونيات», holding 3 active Items
When  : the owner moves it to the top level and saves
Then  : PHONES has an empty parentCategoryFk, and all 3 Items keep categoryFk PHONES

### REQ-CAT-066 — Deactivate a Category
Pattern    : event
Statement  : When an owner deactivates a Category, the system shall set its isActiveFl to false and exclude it from new selections.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : Retired Categories leave the storefront and the selection lists.
Source     : US-CAT-013; RULE-CAT-048
Priority   : —

#### AC-CAT-068 — [REQ-CAT-066]
Given : a Category with 0 active sub-categories and 0 active Items
When  : the owner deactivates it
Then  : isActiveFl is false, the Item entry's Category list omits it, and a read by categoryPk still returns it

#### AC-CAT-142 — [REQ-CAT-066]
Given : an inactive Item «كوب زجاج» whose Category «أدوات قديمة» was deactivated after the Item
When  : the owner lists Items with the active filter set to inactive, then opens that Item
Then  : the list row and the Item header both show the Category label «أدوات قديمة» (not its identifier), and the Category selection list offers 0 entries named «أدوات قديمة»

### REQ-CAT-067 — Category tree
Pattern    : event
Statement  : When a user opens the Categories, the system shall return the Category tree with each Category under its parent, ordered by sortOrder.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : One view of the classification.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-069 — [REQ-CAT-067]
Given : 2 top-level Categories with 3 and 0 sub-categories
When  : the owner opens the Categories
Then  : the response holds 2 root nodes ordered by sortOrder, the first with 3 children

### REQ-CAT-068 — Items of a Category
Pattern    : event
Statement  : When a caller requests the Items of a Category, the system shall return the active Items of that Category and of its active sub-categories.
Traces     : US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : Storefront browsing and report aggregation by branch of the tree.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-070 — [REQ-CAT-068]
Given : «إلكترونيات» holding 2 active Items, and its sub-category PHONES holding 5 active Items and 1 inactive Item
When  : a caller requests the Items of «إلكترونيات»
Then  : 7 Items are returned

### REQ-CAT-069 — Invalid Category parent
Pattern    : unwanted
Statement  : If a Category would get an inactive parent, itself or one of its sub-categories as parent, or a depth above 5 levels, then the system shall reject the save with RULE-CAT-023.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : The tree stays a bounded, acyclic hierarchy (D-03).
Source     : US-CAT-013; D-03
Priority   : —

#### AC-CAT-071 — [REQ-CAT-069]
Given : PHONES is a sub-category of «إلكترونيات»
When  : the owner sets PHONES as the parent of «إلكترونيات»
Then  : nothing is saved and the response shows ar: «الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.» · en: "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels."

### REQ-CAT-070 — Duplicate Category
Pattern    : unwanted
Statement  : If a Category repeats the code of another Category, or the nameAr of another active Category under the same parent, then the system shall reject the save with RULE-CAT-024.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : Shoppers never see two identical Categories side by side.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-072 — [REQ-CAT-070]
Given : an active Category «هواتف» under «إلكترونيات»
When  : the owner creates a second «هواتف» under «إلكترونيات»
Then  : nothing is created and the response shows ar: «يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا.» · en: "The category code already exists, or the name already exists under the same parent. Use a different code or name."

### REQ-CAT-071 — Category deactivation blocked
Pattern    : unwanted
Statement  : If an owner deactivates a Category that holds active sub-categories or active Items, then the system shall reject the deactivation with RULE-CAT-025.
Traces     : US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : Active Items never point to a retired Category (ARCH-5).
Source     : US-CAT-013
Priority   : —

#### AC-CAT-073 — [REQ-CAT-071]
Given : a Category holding 4 active Items
When  : the owner deactivates it
Then  : it stays active and the response shows ar: «لا يمكن إيقاف الفئة لوجود 4 فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا.» · en: "The category cannot be deactivated while it holds 4 active sub-categories or items. Move or deactivate them first."

#### AC-CAT-153 — [REQ-CAT-071]
Given : an active Category «إلكترونيات» with 0 active sub-categories and 0 active Items
When  : a request that creates the sub-category PHONES under «إلكترونيات» and a request that deactivates «إلكترونيات» are submitted in parallel
Then  : exactly 1 request succeeds; either PHONES is created and the deactivation is refused with code CAT-409-CATEGORY-IN-USE, or «إلكترونيات» is inactive and the create is refused with code CAT-422-CATEGORY-PARENT-INVALID; no active Category has an inactive parent

### REQ-CAT-072 — Inactive Category on an Item
Pattern    : unwanted
Statement  : If an Item is saved with a categoryFk that points to an inactive Category, then the system shall reject the save with RULE-CAT-026.
Traces     : US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : Items are classified only into live Categories.
Source     : US-CAT-013
Priority   : —

#### AC-CAT-074 — [REQ-CAT-072]
Given : an inactive Category OLD
When  : the owner saves an Item with categoryFk OLD
Then  : nothing is saved and the response shows ar: «الفئة المختارة غير نشطة. اختر فئة نشطة.» · en: "The selected category is inactive. Choose an active category."

#### AC-CAT-170 — [REQ-CAT-072]
Given : an active Category «أدوات» with 0 active sub-categories and 0 active Items
When  : a request that creates an Item with categoryFk «أدوات» and a request that deactivates «أدوات» are submitted in parallel
Then  : exactly 1 request succeeds; either the Item is created in «أدوات» and the deactivation is refused with code CAT-409-CATEGORY-IN-USE, or «أدوات» is inactive and the create is refused with code CAT-422-CATEGORY-INACTIVE; no active Item points to an inactive Category

### REQ-CAT-073 — Brand values offered
Pattern    : event
Statement  : When a user opens the brand field of an Item, the system shall offer the active values of lookup CAT_BRAND, loaded at request time from the core lookup module.
Traces     : US-CAT-014
Entities   : ENT-CAT-001
Rationale  : Every channel describes brands the same way.
Source     : US-CAT-014; D-02
Priority   : —

#### AC-CAT-075 — [REQ-CAT-073]
Given : CAT_BRAND holds 5 active values and 1 inactive value
When  : the owner opens the brand field
Then  : exactly the 5 active values are offered

### REQ-CAT-074 — Unit values offered
Pattern    : event
Statement  : When a user opens a unit-of-measure field of an Item, an Item unit or a barcode, the system shall offer the active values of lookup CAT_UOM, loaded at request time from the core lookup module.
Traces     : US-CAT-014
Entities   : ENT-CAT-001, ENT-CAT-008, ENT-CAT-009
Rationale  : The same field uses the same lookup key on every screen.
Source     : US-CAT-014; D-02
Priority   : —

#### AC-CAT-076 — [REQ-CAT-074]
Given : CAT_UOM holds the 8 seeded values, all active
When  : the owner opens the base unit field
Then  : 8 values are offered, PIECE pre-selected

### REQ-CAT-075 — Lookup value not active
Pattern    : unwanted
Statement  : If a request carries a lookup-backed field value that is not an active value of the field's lookup key, then the system shall reject the request with RULE-CAT-006.
Traces     : US-CAT-014
Entities   : ENT-CAT-001, ENT-CAT-008, ENT-CAT-009, ENT-CAT-011, ENT-CAT-012
Rationale  : Only known codes enter the catalogue.
Source     : US-CAT-014
Priority   : —

#### AC-CAT-077 — [REQ-CAT-075]
Given : CAT_BRAND has no value «XYZ»
When  : the owner saves an Item with brandCode «XYZ»
Then  : nothing is saved and the response shows ar: «القيمة المختارة غير متاحة. اختر قيمة نشطة من القائمة.» · en: "The selected value is not available. Choose an active value from the list."

### REQ-CAT-076 — Deactivated lookup value kept on records
Pattern    : ubiquitous
Statement  : The system shall keep a lookup code on existing records after the value is deactivated in the core lookup module, and exclude that value from new selections.
Traces     : US-CAT-014
Entities   : ENT-CAT-001, ENT-CAT-008, ENT-CAT-009
Rationale  : Retiring a brand or unit never rewrites existing Items.
Source     : US-CAT-014
Priority   : —

#### AC-CAT-078 — [REQ-CAT-076]
Given : 6 Items with brandCode OLDBRAND, then OLDBRAND is deactivated in the core lookup module
When  : the owner reads one of the 6 Items and opens a new Item's brand field
Then  : the Item still returns brandCode OLDBRAND, and the brand field offers 0 entries named OLDBRAND

### REQ-CAT-077 — Add an Item unit
Pattern    : event
Statement  : When an owner adds a unit to an Item with its conversion factor to the base unit, the system shall create the Item unit as active, with isPurchaseFl and isSalesFl true unless the owner clears them.
Traces     : US-CAT-015
Entities   : ENT-CAT-001, ENT-CAT-008
Rationale  : Purchasing, selling and stock agree on quantities.
Source     : US-CAT-015 (registry AUTO "Item unit conversion owned by CAT")
Priority   : —

#### AC-CAT-079 — [REQ-CAT-077]
Given : an Item with base unit PIECE
When  : the owner adds unit CARTON with conversionFactor 12
Then  : the Item unit CARTON is created with conversionFactor 12.000000, isPurchaseFl true, isSalesFl true and isActiveFl true

### REQ-CAT-078 — Update an Item unit
Pattern    : event
Statement  : When an owner changes isPurchaseFl or isSalesFl of an Item unit, the system shall save the change.
Traces     : US-CAT-015
Entities   : ENT-CAT-008
Rationale  : The owner states whether a unit is used for buying, for selling or for both.
Source     : US-CAT-015
Priority   : —

#### AC-CAT-080 — [REQ-CAT-078]
Given : the Item unit CARTON with isSalesFl true
When  : the owner clears isSalesFl and saves
Then  : the next read returns isSalesFl false, isPurchaseFl true and conversionFactor unchanged

### REQ-CAT-079 — Deactivate an Item unit
Pattern    : event
Statement  : When an owner deactivates an Item unit, the system shall set its isActiveFl to false and keep it readable by its identifier.
Traces     : US-CAT-015
Entities   : ENT-CAT-008
Rationale  : Earlier documents keep resolving the unit they used.
Source     : US-CAT-015; RULE-CAT-048
Priority   : —

#### AC-CAT-081 — [REQ-CAT-079]
Given : an active Item unit CARTON
When  : the owner deactivates it
Then  : isActiveFl is false, the Item read lists 0 active CARTON units, and a read by itemUnitPk returns it

### REQ-CAT-080 — Invalid Item unit
Pattern    : unwanted
Statement  : If an Item unit repeats the base unit or another active unit of the same Item, or carries a conversion factor that is not above zero, then the system shall reject the save with RULE-CAT-027.
Traces     : US-CAT-015
Entities   : ENT-CAT-001, ENT-CAT-008
Rationale  : One factor per unit, with no ambiguous conversion.
Source     : US-CAT-015
Priority   : —

#### AC-CAT-082 — [REQ-CAT-080]
Given : an Item with base unit PIECE
When  : the owner adds Item unit PIECE with conversionFactor 1
Then  : nothing is saved and the response shows ar: «الوحدة يجب أن تختلف عن الوحدة الأساسية وألا تتكرر، ومعامل التحويل أكبر من صفر (حتى 6 أرقام عشرية).» · en: "The unit must differ from the base unit and appear once, with a conversion factor above zero (up to 6 decimals)."

### REQ-CAT-081 — Conversion factor changed
Pattern    : unwanted
Statement  : If an update request changes the conversionFactor of a saved Item unit, then the system shall reject the request with RULE-CAT-028.
Traces     : US-CAT-015
Entities   : ENT-CAT-008
Rationale  : Quantities recorded by downstream modules keep their meaning (D-14).
Source     : US-CAT-015; D-14
Priority   : —

#### AC-CAT-083 — [REQ-CAT-081]
Given : the Item unit CARTON with conversionFactor 12
When  : an update sets it to 24
Then  : the factor stays 12 and the response shows ar: «لا يمكن تعديل معامل التحويل بعد الحفظ. أوقف هذه الوحدة وأضفها من جديد بالمعامل الصحيح.» · en: "The conversion factor cannot be changed after saving. Deactivate this unit and add it again with the correct factor."

### REQ-CAT-082 — Base unit changed
Pattern    : unwanted
Statement  : If an update request changes the baseUomCode of a saved Item, then the system shall reject the request with RULE-CAT-007.
Traces     : US-CAT-015
Entities   : ENT-CAT-001
Rationale  : Stock and documents downstream are kept in the base unit (D-14).
Source     : US-CAT-015; D-14
Priority   : —

#### AC-CAT-084 — [REQ-CAT-082]
Given : a saved Item with base unit PIECE
When  : an update sets baseUomCode to KILOGRAM
Then  : the base unit stays PIECE and the response shows ar: «لا يمكن تغيير وحدة القياس الأساسية بعد حفظ الصنف. أضف وحدة أخرى للصنف بدلًا من ذلك.» · en: "The base unit cannot be changed after the item is saved. Add another unit to the item instead."

### REQ-CAT-083 — Add a GTIN barcode
Pattern    : event
Statement  : When an owner adds a GTIN barcode to a Variant, the system shall store it as an active barcode of that Variant, bound to the Item's base unit when no unit is chosen.
Traces     : US-CAT-016
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : Scanning the barcode printed on the goods finds the exact Variant.
Source     : US-CAT-016; DP §8 P-07
Priority   : —

#### AC-CAT-085 — [REQ-CAT-083]
Given : an active Variant with 0 barcodes and base unit PIECE
When  : the owner adds GTIN 6223000000014 with no unit chosen
Then  : the Variant has 1 active barcode with barcodeTypeCode GTIN and uomCode PIECE

### REQ-CAT-084 — Generate an internal barcode
Pattern    : event
Statement  : When an owner requests an internal barcode for a Variant, the system shall assign a 13-digit barcode that starts with 04, takes its next 10 digits from the core number series CAT_INTERNAL_BARCODE and ends with the GS1 check digit.
Traces     : US-CAT-016
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : Goods without a printed GTIN still scan (GS1 restricted-circulation prefix 04, D-05).
Source     : US-CAT-016; D-05
Priority   : —

#### AC-CAT-086 — [REQ-CAT-084]
Given : series CAT_INTERNAL_BARCODE last issued 0000000041
When  : the owner requests an internal barcode for a Variant
Then  : the Variant gets the active INTERNAL barcode 0400000000428 (04 + 0000000042 + check digit 8)

### REQ-CAT-085 — Deactivate a barcode
Pattern    : event
Statement  : When an owner deactivates a barcode, the system shall set its isActiveFl to false and exclude it from barcode lookups.
Traces     : US-CAT-016
Entities   : ENT-CAT-009
Rationale  : A withdrawn barcode stops scanning.
Source     : US-CAT-016; RULE-CAT-048
Priority   : —

#### AC-CAT-087 — [REQ-CAT-085]
Given : an active barcode 6223000000014
When  : the owner deactivates it and a lookup of 6223000000014 follows
Then  : isActiveFl is false and the lookup returns no Variant

### REQ-CAT-086 — Barcode lookup
Pattern    : event
Statement  : When a barcode value is submitted for lookup, the system shall return the active Variant of an active Item, that Item and the unit of the active barcode with that value.
Traces     : US-CAT-016
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : Selling and receiving find the exact Variant and unit; an Item that left every channel no longer scans (REQ-CAT-005).
Source     : US-CAT-016; ADR-CAT-059
Priority   : —

#### AC-CAT-088 — [REQ-CAT-086]
Given : the active barcode 16223000000011 bound to unit CARTON of a Variant VAR-000200
When  : a seller/cashier scans 16223000000011
Then  : the response returns VAR-000200, its Item and uomCode CARTON

#### AC-CAT-135 — [REQ-CAT-086]
Given : the active barcode 6223000000021 on the active Variant of an Item that the owner deactivated
When  : a seller/cashier scans 6223000000021
Then  : no Variant is returned and the response shows ar: «لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف.» · en: "No item has this barcode. Search by name or add the barcode to the item."

### REQ-CAT-087 — Duplicate barcode
Pattern    : unwanted
Statement  : If a barcode value equals an active barcode of the tenant, then the system shall reject the new barcode with RULE-CAT-029.
Traces     : US-CAT-016
Entities   : ENT-CAT-009
Rationale  : One scan resolves to one Variant.
Source     : US-CAT-016
Priority   : —

#### AC-CAT-089 — [REQ-CAT-087]
Given : the active GTIN 6223000000014 on an Item with code ITM-000005
When  : the owner adds 6223000000014 to another Variant
Then  : nothing is saved and the response shows ar: «هذا الباركود مستخدم في الصنف ITM-000005. افتح ذلك الصنف أو استخدم باركودًا آخر.» · en: "This barcode is already used by item ITM-000005. Open that item or use another barcode."

#### AC-CAT-150 — [REQ-CAT-087]
Given : no active barcode 6223000000038 in the tenant
When  : two requests that add the GTIN 6223000000038 to two different Variants are submitted in parallel
Then  : exactly 1 barcode 6223000000038 is saved, and the other request is refused with code CAT-409-BARCODE-DUPLICATE

### REQ-CAT-088 — Invalid GTIN
Pattern    : unwanted
Statement  : If a GTIN barcode does not have 8, 12, 13 or 14 digits with a valid GS1 check digit, then the system shall reject it with RULE-CAT-030.
Traces     : US-CAT-016
Entities   : ENT-CAT-009
Rationale  : A mistyped barcode never enters the catalogue.
Source     : US-CAT-016
Priority   : —

#### AC-CAT-090 — [REQ-CAT-088]
Given : a Variant
When  : the owner adds GTIN 6223000000015 (wrong check digit)
Then  : nothing is saved and the response shows ar: «باركود GTIN يجب أن يتكون من 8 أو 12 أو 13 أو 14 رقمًا برقم تحقق صحيح.» · en: "A GTIN barcode must have 8, 12, 13 or 14 digits with a valid check digit."

### REQ-CAT-089 — Barcode unit not of the Item
Pattern    : unwanted
Statement  : If a barcode names a unit that is neither the Item's base unit nor an active Item unit of that Item, then the system shall reject the barcode with RULE-CAT-031.
Traces     : US-CAT-016
Entities   : ENT-CAT-001, ENT-CAT-008, ENT-CAT-009
Rationale  : A carton barcode always converts to a known quantity.
Source     : US-CAT-016
Priority   : —

#### AC-CAT-091 — [REQ-CAT-089]
Given : an Item with base unit PIECE and no Item unit BOX
When  : the owner adds a barcode with uomCode BOX
Then  : nothing is saved and the response shows ar: «وحدة الباركود يجب أن تكون الوحدة الأساسية للصنف أو إحدى وحداته النشطة.» · en: "The barcode unit must be the item's base unit or one of its active units."

### REQ-CAT-090 — Barcode not found
Pattern    : unwanted
Statement  : If no active barcode and no active scale label layout matches a looked-up value, then the system shall return no Variant with RULE-CAT-047.
Traces     : US-CAT-016
Entities   : ENT-CAT-009
Rationale  : The cashier gets a next action instead of a dead end.
Source     : US-CAT-016; KB:raw-idea.md §6.5
Priority   : —

#### AC-CAT-092 — [REQ-CAT-090]
Given : no active barcode equals 6229999999995 and no scale label layout uses prefix 62
When  : a seller/cashier scans 6229999999995
Then  : no Variant is returned and the response shows ar: «لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف.» · en: "No item has this barcode. Search by name or add the barcode to the item."

### REQ-CAT-091 — Barcode clashes with a scale prefix
Pattern    : unwanted
Statement  : If a GTIN or internal barcode begins with the prefix of an active scale label layout, then the system shall reject the barcode with RULE-CAT-032.
Traces     : US-CAT-016
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : A scan is never read as both a barcode and a scale label.
Source     : US-CAT-016; D-05
Priority   : —

#### AC-CAT-093 — [REQ-CAT-091]
Given : an active scale label layout with prefix 20
When  : the owner adds GTIN 2000000000008
Then  : nothing is saved and the response shows ar: «الباركود يبدأ ببادئة ملصق ميزان نشط (20). استخدم باركودًا آخر أو عدّل إعداد الميزان.» · en: "The barcode starts with the prefix of an active scale label layout (20). Use another barcode or change the scale settings."

#### AC-CAT-167 — [REQ-CAT-091]
Given : no active scale label layout, no active barcode beginning with 21, and an active Variant
When  : a request that adds the GTIN 2100000000005 to the Variant and a request that creates a scale label layout with prefix 21 are submitted in parallel
Then  : exactly 1 request succeeds; either the barcode is saved and the layout create is refused with code CAT-409-BARCODE-SCALE-PREFIX, or the layout is created and the barcode is refused with code CAT-409-BARCODE-SCALE-PREFIX; no active GTIN begins with the prefix of an active layout

### REQ-CAT-092 — Create a scale label layout
Pattern    : event
Statement  : When an owner saves a new scale label layout with its prefix, scale code length, embedded value type and decimals, the system shall create the layout as active.
Traces     : US-CAT-017
Entities   : ENT-CAT-011
Rationale  : The owner states how the scale prints its labels.
Source     : US-CAT-017; ADR-CAT-042
Priority   : LOW

#### AC-CAT-094 — [REQ-CAT-092]
Given : no active scale label layout
When  : the owner saves prefix 20, scaleCodeLength 5, embeddedValueTypeCode WEIGHT and valueDecimals 3
Then  : the layout is created with isActiveFl true

### REQ-CAT-093 — Update a scale label layout
Pattern    : event
Statement  : When an owner changes the embedded value type, decimals or descriptions of a scale label layout, the system shall save the change and read the embedded value of every later scanned label with that prefix by the new type and decimals.
Traces     : US-CAT-017
Entities   : ENT-CAT-011, ENT-CAT-009
Rationale  : A scale replaced or re-set keeps working. The prefix and the scale code length are fixed after creation, because the layout's SCALE barcodes were validated against that length (RULE-CAT-033); a change of type or decimals changes only how the embedded value is read, and every SCALE barcode keeps resolving (ADR-CAT-064).
Source     : US-CAT-017; ADR-CAT-064
Priority   : LOW

#### AC-CAT-095 — [REQ-CAT-093]
Given : the layout with prefix 20 and embeddedValueTypeCode WEIGHT
When  : the owner changes embeddedValueTypeCode to PRICE and valueDecimals to 2
Then  : the next read returns PRICE and 2

#### AC-CAT-152 — [REQ-CAT-093]
Given : the active layout prefix 20, scaleCodeLength 5, WEIGHT, valueDecimals 3, and a Variant with SCALE barcode 01230
When  : the owner changes valueDecimals to 2 and a seller/cashier then scans 2001230012342
Then  : the response returns that Variant with a weight of 12.34, and the SCALE barcode 01230 is unchanged and active

### REQ-CAT-094 — Deactivate a scale label layout
Pattern    : event
Statement  : When an owner deactivates a scale label layout, the system shall set its isActiveFl to false, stop decoding values with its prefix, and keep every SCALE barcode stored and active.
Traces     : US-CAT-017
Entities   : ENT-CAT-011, ENT-CAT-009
Rationale  : A scale taken out of use stops being decoded. Its SCALE barcodes stay stored but are not decodable until an active layout with the same scale code length exists again (ADR-CAT-064).
Source     : US-CAT-017; ADR-CAT-064
Priority   : LOW

#### AC-CAT-096 — [REQ-CAT-094]
Given : the active layout with prefix 20 (scaleCodeLength 5), no other active layout, and a Variant with the active SCALE barcode 01230
When  : the owner deactivates the layout and a seller/cashier scans 2001230012342 (prefix 20, code 01230, valid check digit)
Then  : isActiveFl is false and the lookup returns no Variant with the RULE-CAT-047 message

### REQ-CAT-095 — Read the catalogue settings
Pattern    : event
Statement  : When an owner opens the catalogue settings, the system shall return every scale label layout and every Unit e-invoice code with its active state.
Traces     : US-CAT-017, US-CAT-021
Entities   : ENT-CAT-011, ENT-CAT-012
Rationale  : One place for the tenant's catalogue settings.
Source     : US-CAT-017; US-CAT-021; ADR-CAT-042
Priority   : HIGH

#### AC-CAT-097 — [REQ-CAT-095]
Given : 1 active scale label layout, 8 active and 1 inactive Unit e-invoice codes
When  : the owner opens the catalogue settings
Then  : the response lists 1 layout and 9 Unit e-invoice codes, each with its isActiveFl

### REQ-CAT-096 — Scale code entry offered
Pattern    : optional
Statement  : Where at least one active scale label layout exists, the system shall offer the SCALE barcode type in a Variant's barcode entry.
Traces     : US-CAT-017
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : Weighed-item entry appears only when it can be used, with no new flag (OI-10).
Source     : US-CAT-017; module-registry OI-10
Priority   : LOW

#### AC-CAT-098 — [REQ-CAT-096]
Given : 1 active scale label layout with scaleCodeLength 5
When  : the owner adds barcode type SCALE with value 01230 to a Variant
Then  : the Variant has 1 active SCALE barcode 01230

### REQ-CAT-097 — Decode a scale label
Pattern    : event
Statement  : When a submitted 13-digit value begins with the prefix of an active scale label layout, the system shall return the active Variant whose SCALE barcode equals the embedded scale code, with the embedded value as a weight or a price carrying the layout's decimals.
Traces     : US-CAT-017
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : Weighed goods are sold without re-keying.
Source     : US-CAT-017; DP §8 P-07
Priority   : LOW

#### AC-CAT-099 — [REQ-CAT-097]
Given : the active layout prefix 20, scaleCodeLength 5, WEIGHT, valueDecimals 3, and a Variant with SCALE barcode 01230
When  : a seller/cashier scans 2001230012342 (prefix 20, code 01230, value 01234, check digit 2)
Then  : the response returns that Variant with a weight of 1.234 in the Item's base unit

### REQ-CAT-098 — Invalid scale label layout
Pattern    : unwanted
Statement  : If a scale label layout carries a prefix outside 02 and 20–29, a prefix used by another active layout, a scale code length outside 4–6 or decimals outside 0–3, then the system shall reject the save with RULE-CAT-034.
Traces     : US-CAT-017
Entities   : ENT-CAT-011
Rationale  : Layouts follow the GS1 restricted-circulation prefixes for variable-measure items.
Source     : US-CAT-017; module-registry OI-10
Priority   : LOW

#### AC-CAT-100 — [REQ-CAT-098]
Given : the catalogue settings
When  : the owner saves a layout with prefix 62
Then  : nothing is saved and the response shows ar: «البادئة رقمان (02 أو من 20 إلى 29) غير مستخدمة في إعداد نشط آخر، وطول رمز الميزان من 4 إلى 6، والأرقام العشرية من 0 إلى 3.» · en: "The prefix is two digits (02 or 20 to 29) not used by another active layout, the scale code length is 4 to 6, and the decimals are 0 to 3."

### REQ-CAT-099 — Scale code length mismatch
Pattern    : unwanted
Statement  : If a SCALE barcode is not all digits, does not match the scale code length of an active layout, or is added while no active layout exists, then the system shall reject it with RULE-CAT-033.
Traces     : US-CAT-017
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : Every scale code can be found inside a printed label.
Source     : US-CAT-017
Priority   : LOW

#### AC-CAT-101 — [REQ-CAT-099]
Given : the only active layout has scaleCodeLength 5
When  : the owner adds SCALE barcode 123
Then  : nothing is saved and the response shows ar: «رمز الميزان يجب أن يتكون من 5 أرقام، ويلزم وجود إعداد ملصق ميزان نشط.» · en: "The scale code must have 5 digits, and an active scale label layout is required."

### REQ-CAT-100 — Scale label not readable
Pattern    : unwanted
Statement  : If a value that begins with an active layout prefix has a wrong check digit or an embedded scale code that no active SCALE barcode carries, then the system shall return no Variant with RULE-CAT-035.
Traces     : US-CAT-017
Entities   : ENT-CAT-009, ENT-CAT-011
Rationale  : A misread label never sells the wrong goods.
Source     : US-CAT-017
Priority   : LOW

#### AC-CAT-102 — [REQ-CAT-100]
Given : the active layout prefix 20 and no SCALE barcode 09999
When  : a seller/cashier scans a valid-check-digit label 20 09999 01234 x
Then  : no Variant is returned and the response shows ar: «تعذّر قراءة ملصق الميزان: رقم التحقق غير صحيح أو رمز الميزان غير معرَّف. اطلب الصنف بالاسم أو أضف رمز الميزان إليه.» · en: "The scale label could not be read: the check digit is wrong or the scale code is not defined. Find the item by name or add the scale code to it."

### REQ-CAT-101 — Find Variants for labels
Pattern    : event
Statement  : When a user searches Variants by code, name or barcode, the system shall return a page of the active Variants of active Items with their Item nameAr and their first active barcode.
Traces     : US-CAT-018
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : Warehouse staff pick the Variants to label; an Item that left every channel gets no new labels (REQ-CAT-005).
Source     : US-CAT-018; ADR-CAT-059
Priority   : —

#### AC-CAT-103 — [REQ-CAT-101]
Given : 3 active Variants of active Items whose Item nameAr contains «قميص»
When  : warehouse staff search Variants with «قميص» and page size 20
Then  : 3 Variants are returned, each with its Item nameAr and its earliest-created active barcode (empty when it has none)

#### AC-CAT-136 — [REQ-CAT-101]
Given : 2 active Variants of an active Item named «حذاء رياضي» and 1 active Variant of an inactive Item named «حذاء جلد»
When  : warehouse staff search Variants with «حذاء» and page size 20
Then  : exactly 2 Variants are returned, both of «حذاء رياضي», and 0 Variants of «حذاء جلد»

### REQ-CAT-102 — Produce barcode labels
Pattern    : event
Statement  : When a user submits a label request listing Variants with a copy count for each, the system shall return one printable document, produced through the core print service, holding per copy the Variant nameAr, the Variant code and the Variant's earliest-created active barcode.
Traces     : US-CAT-018
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : Goods without a printed GTIN can still be scanned at the point of sale.
Source     : US-CAT-018; module-registry AUTO (report, PrintApi)
Priority   : —

#### AC-CAT-104 — [REQ-CAT-102]
Given : 2 Variants that each carry an active barcode
When  : warehouse staff request 10 copies of the first and 5 of the second
Then  : one printable document holding 15 labels is returned, each label showing nameAr, code and barcode

### REQ-CAT-103 — Label copy limits
Pattern    : unwanted
Statement  : If a label request asks fewer than 1 or more than 999 copies of a Variant, or more than 5,000 labels in total, then the system shall reject the request with RULE-CAT-036.
Traces     : US-CAT-018
Entities   : ENT-CAT-002
Rationale  : Bounded print jobs (D-10).
Source     : US-CAT-018; D-10
Priority   : —

#### AC-CAT-105 — [REQ-CAT-103]
Given : a label request
When  : warehouse staff ask 1,000 copies of one Variant
Then  : no document is produced and the response shows ar: «عدد النسخ لكل متغيّر من 1 إلى 999، وإجمالي الملصقات لا يتجاوز 5000.» · en: "Copies per variant are 1 to 999, and the total cannot exceed 5,000 labels."

### REQ-CAT-104 — Variant without a barcode
Pattern    : unwanted
Statement  : If a label request lists a Variant with no active barcode, then the system shall reject the request with RULE-CAT-037 and list those Variants.
Traces     : US-CAT-018
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : No label is printed without a scannable code; the next action generates one.
Source     : US-CAT-018; KB:raw-idea.md §6.5
Priority   : —

#### AC-CAT-106 — [REQ-CAT-104]
Given : a label request for 3 Variants, 2 of which have no active barcode
When  : warehouse staff submit it
Then  : no document is produced, the 2 Variants are listed, and the response shows ar: «لا يوجد باركود نشط لـ 2 متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع.» · en: "2 variants have no active barcode. Generate an internal barcode for them, then print."

### REQ-CAT-105 — Add an image
Pattern    : event
Statement  : When an owner adds to an Item an image file already uploaded to the core public image storage, the system shall create an active Item media record that points to that file, with nameAr defaulting to the Item's nameAr.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : Shoppers see the Item as it really is; the bytes are kept by the core file module only.
Source     : US-CAT-019; DP §8 P-12; ADR-CAT-056
Priority   : MEDIUM

#### AC-CAT-107 — [REQ-CAT-105]
Given : an Item «كوب زجاج» with 2 active images
When  : the owner uploads a 1 MB PNG with no description
Then  : the Item has 3 active media records; the new one has nameAr «كوب زجاج», sortOrder 3 and a fileDocumentRef from the public image storage

### REQ-CAT-106 — Update an image
Pattern    : event
Statement  : When an owner changes the sortOrder, isPrimaryFl, nameAr, nameEn or variantFk of an Item media record, the system shall save the change.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : The owner orders the images, chooses the main one and binds images to Variants.
Source     : US-CAT-019; RULE-CAT-039
Priority   : MEDIUM

#### AC-CAT-108 — [REQ-CAT-106]
Given : an Item whose primary image is image A, and image B is not primary
When  : the owner sets isPrimaryFl on image B
Then  : B is primary, A is not, the Item has exactly 1 primary image, and the response shows ar: «للصنف صورة رئيسية واحدة فقط؛ تم تعيين الصورة المختارة رئيسية بدل السابقة.» · en: "An item has exactly one main image; the selected image replaced the previous main image."

#### AC-CAT-148 — [REQ-CAT-106]
Given : an Item with active images A (primary), B and C
When  : a request that sets isPrimaryFl on B and a request that sets isPrimaryFl on C are submitted in parallel
Then  : both requests complete one after the other, and the Item then has exactly 1 primary image, B or C

### REQ-CAT-107 — Remove an image
Pattern    : event
Statement  : When an owner removes an image from an Item, the system shall set the Item media record's isActiveFl to false and exclude it from every image read.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : Outdated images leave the storefront.
Source     : US-CAT-019
Priority   : MEDIUM

#### AC-CAT-109 — [REQ-CAT-107]
Given : an Item with 3 active images
When  : the owner removes the image with sortOrder 2
Then  : the Item read returns 2 images

### REQ-CAT-108 — First image is the main image
Pattern    : event
Statement  : When the first active image of an Item is added, the system shall mark it as primary.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : Every Item with images has a main image without an extra step.
Source     : US-CAT-019; RULE-CAT-039
Priority   : MEDIUM

#### AC-CAT-110 — [REQ-CAT-108]
Given : an Item with 0 active images
When  : the owner uploads one image
Then  : that image has isPrimaryFl true, and exactly 1 active image of the Item has isPrimaryFl true

### REQ-CAT-109 — Images of a Variant
Pattern    : event
Statement  : When a caller requests the images of a Variant, the system shall return the active media bound to that Variant, or the Item's active media bound to no Variant when none is bound to it, with the primary image first and the others by sortOrder.
Traces     : US-CAT-019
Entities   : ENT-CAT-002, ENT-CAT-010
Rationale  : The storefront shows the right image for each Variant.
Source     : US-CAT-019
Priority   : MEDIUM

#### AC-CAT-111 — [REQ-CAT-109]
Given : an Item with 2 images bound to no Variant, and 1 image bound to Variant «أحمر»
When  : the storefront requests the images of Variant «أحمر», then of Variant «أزرق»
Then  : the first request returns 1 image (the «أحمر» image), and the second returns the 2 unbound images, primary first

### REQ-CAT-110 — Image file not accepted
Pattern    : unwanted
Statement  : If an uploaded image is not JPG, PNG or WEBP, exceeds 5 MB, or would give the Item more than 20 active images, then the system shall reject the upload with RULE-CAT-038.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : Storefront pages load bounded media (D-10). The file part of RULE-CAT-038 (type, size, public storage) is enforced at upload by the FILE category CAT_ITEM_IMAGE that CAT registers, in the file module's own envelope; CAT itself refuses the 21st active image (ADR-CAT-076).
Source     : US-CAT-019; D-10; ADR-CAT-076
Priority   : MEDIUM

#### AC-CAT-112 — [REQ-CAT-110]
Given : an Item with 20 active images
When  : the owner uploads a 7 MB JPG, then a 1 MB PNG as a 21st image
Then  : the 7 MB JPG is refused at its upload to the image category, in the file module's envelope, and the 1 MB PNG is refused with ar: «الصورة يجب أن تكون JPG أو PNG أو WEBP بحجم لا يتجاوز 5 ميجابايت، وبحد أقصى 20 صورة نشطة للصنف.» · en: "The image must be JPG, PNG or WEBP of at most 5 MB, with at most 20 active images per item."; the Item keeps 20 active media records

### REQ-CAT-111 — Main image removed
Pattern    : unwanted
Statement  : If the primary image of an Item is removed while other active images remain, then the system shall mark the remaining active image with the lowest sortOrder as primary.
Traces     : US-CAT-019
Entities   : ENT-CAT-010
Rationale  : The Item never loses its main image while it has images.
Source     : US-CAT-019; RULE-CAT-039
Priority   : MEDIUM

#### AC-CAT-113 — [REQ-CAT-111]
Given : an Item with primary image sortOrder 1 and other images sortOrder 2 and 3
When  : the owner removes the primary image
Then  : the image with sortOrder 2 has isPrimaryFl true, and exactly 1 active image is primary

### REQ-CAT-112 — Image bound to another Item's Variant
Pattern    : unwanted
Statement  : If an Item media record names a variantFk of another Item, then the system shall reject the save with RULE-CAT-040.
Traces     : US-CAT-019
Entities   : ENT-CAT-002, ENT-CAT-010
Rationale  : An image always describes its own Item.
Source     : US-CAT-019
Priority   : MEDIUM

#### AC-CAT-114 — [REQ-CAT-112]
Given : an image of Item A
When  : the owner binds it to a Variant of Item B
Then  : nothing is saved and the response shows ar: «المتغيّر المختار لا ينتمي إلى هذا الصنف.» · en: "The selected variant does not belong to this item."

### REQ-CAT-113 — Publish an Item
Pattern    : event
Statement  : When an owner publishes an active Item, the system shall set its publishStateCode to PUBLISHED.
Traces     : US-CAT-020
Entities   : ENT-CAT-001
Rationale  : The owner decides what is offered online.
Source     : US-CAT-020; POL-CAT-008
Priority   : MEDIUM

#### AC-CAT-115 — [REQ-CAT-113]
Given : an active UNPUBLISHED Item with 1 active Variant
When  : the owner publishes it
Then  : publishStateCode is PUBLISHED and 1 catalogue-change event with change kind UPDATED is published

### REQ-CAT-114 — Unpublish an Item
Pattern    : event
Statement  : When an owner unpublishes an Item, the system shall set its publishStateCode to UNPUBLISHED and keep its isActiveFl unchanged.
Traces     : US-CAT-020
Entities   : ENT-CAT-001
Rationale  : The Item leaves the storefront and stays on sale in the back-office and at the point of sale.
Source     : US-CAT-020; POL-CAT-008
Priority   : MEDIUM

#### AC-CAT-116 — [REQ-CAT-114]
Given : an active PUBLISHED Item
When  : the owner unpublishes it
Then  : publishStateCode is UNPUBLISHED, isActiveFl is true, and the point-of-sale search still returns the Item

### REQ-CAT-115 — Storefront reads published Items only
Pattern    : event
Statement  : When the storefront requests catalogue Items, the system shall return only Items with isActiveFl true and publishStateCode PUBLISHED.
Traces     : US-CAT-020
Entities   : ENT-CAT-001
Rationale  : Shoppers see only what the owner chose to sell online.
Source     : US-CAT-020
Priority   : MEDIUM

#### AC-CAT-117 — [REQ-CAT-115]
Given : 10 Items: 6 active PUBLISHED, 3 active UNPUBLISHED, 1 inactive
When  : the storefront requests catalogue Items
Then  : exactly the 6 active PUBLISHED Items are returned

### REQ-CAT-116 — Publish blocked
Pattern    : unwanted
Statement  : If an owner publishes an inactive Item or an Item with no active Variant, then the system shall reject the request with RULE-CAT-041.
Traces     : US-CAT-020
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : The storefront never offers something that cannot be sold.
Source     : US-CAT-020
Priority   : MEDIUM

#### AC-CAT-118 — [REQ-CAT-116]
Given : an inactive Item
When  : the owner publishes it
Then  : publishStateCode stays UNPUBLISHED and the response shows ar: «لا يمكن نشر صنف غير نشط أو بلا متغيّر نشط. فعّل الصنف أو أحد متغيّراته أولًا.» · en: "An inactive item, or one with no active variant, cannot be published. Activate the item or one of its variants first."

### REQ-CAT-117 — Item e-invoice code
Pattern    : event
Statement  : When an owner saves the e-invoice code type and code of an Item, the system shall store them as the Item's e-invoice code.
Traces     : US-CAT-021
Entities   : ENT-CAT-001
Rationale  : Invoices from every channel carry an accepted classification code.
Source     : US-CAT-021; PRD P-4; DP §8 P-08
Priority   : HIGH

#### AC-CAT-119 — [REQ-CAT-117]
Given : an Item with no e-invoice code
When  : the owner saves einvoiceCodeTypeCode EGS and einvoiceCode «EG-123456789-1001»
Then  : the next read returns EGS and «EG-123456789-1001»

### REQ-CAT-118 — Variant e-invoice override
Pattern    : event
Statement  : When an owner saves an e-invoice code type and code on a Variant, the system shall store them as that Variant's override of the Item's e-invoice code.
Traces     : US-CAT-021
Entities   : ENT-CAT-002
Rationale  : A Variant classified differently from its Item is invoiced correctly.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-120 — [REQ-CAT-118]
Given : an Item with e-invoice code EGS «EG-123456789-1001» and 2 Variants
When  : the owner saves GS1 «6223000000014» on the second Variant
Then  : the second Variant stores GS1 «6223000000014», and the first Variant stores no override

### REQ-CAT-119 — Effective e-invoice data of a Variant
Pattern    : event
Statement  : When a caller reads a Variant, the system shall return its effective e-invoice code type and code, being the Variant's override when present and the Item's otherwise, together with the Item's taxClassCode.
Traces     : US-CAT-021
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Sales and Tax read one resolved value per Variant.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-121 — [REQ-CAT-119]
Given : the Item and Variants of AC-CAT-120, with taxClassCode STANDARD
When  : a caller reads each Variant
Then  : the first Variant returns EGS «EG-123456789-1001», the second returns GS1 «6223000000014», and both return taxClassCode STANDARD

### REQ-CAT-120 — Create a Unit e-invoice code
Pattern    : event
Statement  : When an owner saves an e-invoice unit for a unit of measure that has no active Unit e-invoice code, the system shall create the Unit e-invoice code as active.
Traces     : US-CAT-021
Entities   : ENT-CAT-012
Rationale  : Every unit carries the unit code the tax authority accepts.
Source     : US-CAT-021; D-12
Priority   : HIGH

#### AC-CAT-122 — [REQ-CAT-120]
Given : CAT_UOM gains the value BAG, which has no Unit e-invoice code
When  : the owner saves key BAG with einvoiceUnitCode EA
Then  : an active Unit e-invoice code for BAG with EA is created

### REQ-CAT-121 — Update a Unit e-invoice code
Pattern    : event
Statement  : When an owner changes the einvoiceUnitCode of an active Unit e-invoice code, the system shall save the change.
Traces     : US-CAT-021
Entities   : ENT-CAT-012
Rationale  : Mappings are corrected in one place.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-123 — [REQ-CAT-121]
Given : the Unit e-invoice code for BAG with EA
When  : the owner changes einvoiceUnitCode to BX
Then  : the next read returns BX for BAG

### REQ-CAT-122 — Deactivate a Unit e-invoice code
Pattern    : event
Statement  : When an owner deactivates a Unit e-invoice code, the system shall set its isActiveFl to false and return no e-invoice unit for that unit of measure.
Traces     : US-CAT-021
Entities   : ENT-CAT-012
Rationale  : A wrong mapping is withdrawn before a correct one is added.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-124 — [REQ-CAT-122]
Given : the active Unit e-invoice code for BAG
When  : the owner deactivates it
Then  : isActiveFl is false and reads of BAG return an empty e-invoice unit

### REQ-CAT-123 — Incomplete or malformed e-invoice code
Pattern    : unwanted
Statement  : If an Item or Variant carries only one of e-invoice code type and code, a GS1 code that is not 8 to 14 digits, or an EGS code that does not start with "EG-", then the system shall reject the save with RULE-CAT-042.
Traces     : US-CAT-021
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Codes the tax authority would refuse are caught at entry.
Source     : US-CAT-021; PRD P-4
Priority   : HIGH

#### AC-CAT-125 — [REQ-CAT-123]
Given : an Item
When  : the owner saves einvoiceCodeTypeCode EGS with einvoiceCode «123»
Then  : nothing is saved and the response shows ar: «أدخل نوع رمز الفاتورة الإلكترونية والرمز معًا: GS1 من 8 إلى 14 رقمًا، وEGS يبدأ بـ EG- ولا يتجاوز 100 حرف.» · en: "Enter the e-invoice code type and code together: GS1 is 8 to 14 digits, EGS starts with EG- and has at most 100 characters."

### REQ-CAT-124 — Second e-invoice unit for one unit
Pattern    : unwanted
Statement  : If an owner creates a Unit e-invoice code for a unit of measure that already has an active one, then the system shall reject the request with RULE-CAT-043.
Traces     : US-CAT-021
Entities   : ENT-CAT-012
Rationale  : One unit maps to one e-invoice unit.
Source     : US-CAT-021
Priority   : HIGH

#### AC-CAT-126 — [REQ-CAT-124]
Given : the active Unit e-invoice code PIECE → EA
When  : the owner creates PIECE → H87
Then  : nothing is created and the response shows ar: «لهذه الوحدة رمز فاتورة إلكترونية نشط بالفعل. عدّله بدلًا من إضافة رمز جديد.» · en: "This unit already has an active e-invoice unit. Change it instead of adding a new one."

### REQ-CAT-125 — Catalogue search for the global search
Pattern    : event
Statement  : When a search term of at least 2 characters is submitted to the catalogue search, the system shall return at most 20 active Items whose nameAr, nameEn, code or active barcode contains the term, with exact code or barcode matches first.
Traces     : US-CAT-022
Entities   : ENT-CAT-001, ENT-CAT-009
Rationale  : Items are reached from the one global search.
Source     : US-CAT-022; POL-CAT-009; KB:raw-idea.md §6.6; ADR-CAT-043
Priority   : HIGH

#### AC-CAT-127 — [REQ-CAT-125]
Given : 25 active Items whose nameAr contains «شاحن», one of them with code ITM-000077
When  : a seller/cashier searches «شاحن», then «ITM-000077»
Then  : the first search returns 20 Items, and the second returns ITM-000077 in position 1

### REQ-CAT-126 — Search term too short
Pattern    : unwanted
Statement  : If the search term has fewer than 2 characters, then the system shall return no results with RULE-CAT-046.
Traces     : US-CAT-022
Entities   : ENT-CAT-001
Rationale  : One-letter searches would return the whole catalogue.
Source     : US-CAT-022
Priority   : HIGH

#### AC-CAT-128 — [REQ-CAT-126]
Given : the global search
When  : a seller/cashier submits «ش»
Then  : 0 results are returned and the response shows ar: «اكتب حرفين على الأقل للبحث.» · en: "Type at least 2 characters to search."

### REQ-CAT-127 — Search without permission
Pattern    : unwanted
Statement  : If the user who submits the catalogue search lacks the VIEW permission of page CAT_ITEMS, then the system shall return no Item results.
Traces     : US-CAT-022
Entities   : ENT-CAT-001
Rationale  : The global search respects catalogue access through the platform authorisation layer.
Source     : US-CAT-022; profile security_model
Priority   : HIGH

#### AC-CAT-129 — [REQ-CAT-127]
Given : a custom role without VIEW on CAT_ITEMS
When  : its user searches «شاحن» in the global search
Then  : the catalogue contributes 0 results

### REQ-CAT-128 — Item unit used by active barcodes
Pattern    : unwanted
Statement  : If an owner deactivates an Item unit while active barcodes of the Item's Variants are bound to that unit, then the system shall reject the deactivation with RULE-CAT-051.
Traces     : US-CAT-016
Entities   : ENT-CAT-008, ENT-CAT-009
Rationale  : A barcode never resolves to a unit the Item no longer offers.
Source     : US-CAT-016; US-CAT-015; ADR-CAT-060
Priority   : —

#### AC-CAT-130 — [REQ-CAT-128]
Given : an Item with the active Item unit CARTON and 2 active barcodes bound to unit CARTON
When  : the owner deactivates the Item unit CARTON
Then  : CARTON stays active and the response shows ar: «هذه الوحدة مستخدمة في 2 باركود نشط. أوقف هذه الباركودات أولًا.» · en: "This unit is used by 2 active barcodes. Deactivate those barcodes first."

#### AC-CAT-168 — [REQ-CAT-128]
Given : an Item with base unit PIECE, the active Item unit CARTON with 0 active barcodes bound to it, and an active Variant V of the Item
When  : a request that deactivates the Item unit CARTON and a request that adds the GTIN 6223000000045 with unit CARTON to V are submitted in parallel
Then  : exactly 1 request succeeds; either CARTON is inactive and the barcode is refused with code CAT-422-BARCODE-UNIT-INVALID, or the barcode is saved on CARTON and the deactivation is refused with code CAT-409-ITEM-UNIT-HAS-BARCODES; no active barcode is bound to an inactive Item unit

### REQ-CAT-129 — Generation omits a used attribute
Pattern    : unwanted
Statement  : If a Variant generation request for an Item omits an attribute that the Item's active Variants already carry, or selects no value for it, then the system shall reject the whole request with RULE-CAT-052.
Traces     : US-CAT-011
Entities   : ENT-CAT-002, ENT-CAT-005
Rationale  : Every active Variant of an Item carries one value of each attribute the Item uses (RULE-CAT-013).
Source     : US-CAT-011
Priority   : HIGH

#### AC-CAT-131 — [REQ-CAT-129]
Given : a STANDARD Item whose 2 active Variants carry the attributes SIZE (values S, M) and COLOUR (value «أحمر»)
When  : the owner generates Variants with selections SIZE [L] only
Then  : 0 Variants are created and the response shows ar: «اختر قيمة واحدة على الأقل لكل خاصية يستخدمها الصنف بالفعل.» · en: "Select at least one value for every attribute the item already uses."

### REQ-CAT-130 — Reactivation of an Item with no active Variant
Pattern    : unwanted
Statement  : If an owner reactivates an Item that has no active Variant, then the system shall reject the reactivation with RULE-CAT-050 and keep the Item inactive.
Traces     : US-CAT-001
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Every transition into ACTIVE re-establishes the Item invariants of A7; this REQ holds invariant 1. The other three refusals on reactivation are REQ-CAT-142, REQ-CAT-143 and REQ-CAT-144 (split from this REQ at the seventh gate-analysis revise).
Source     : US-CAT-001; ADR-CAT-059
Priority   : HIGH

#### AC-CAT-132 — [REQ-CAT-130]
Given : an inactive Item whose only Variant was deactivated while the Item was inactive, so it has 0 active Variants
When  : the owner reactivates the Item
Then  : isActiveFl stays false and the response shows ar: «لا يمكن إعادة تفعيل صنف بلا متغيّر نشط. أنشئ متغيّرًا له أو أنشئ صنفًا جديدًا.» · en: "An item with no active variant cannot be reactivated. Generate a variant for it, or create a new item."

### REQ-CAT-131 — Operation without permission
Pattern    : unwanted
Statement  : If a user calls a catalogue operation without the permission of its page and action in the SRS B4 matrix, then the system shall refuse the request with CAT-403-FORBIDDEN and change no record, except for the catalogue search, which REQ-CAT-127 answers with no results.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : Only the roles that SRS B4 grants change the catalogue; the screens hide what the user may not do.
Source     : US-CAT-001; SRS B4; profile security_model; ADR-CAT-046
Priority   : HIGH

#### AC-CAT-137 — [REQ-CAT-131]
Given : a Seller/cashier who holds only PERM_CAT_ITEMS_VIEW, and an active Item with nameEn "Charger"
When  : the user creates an Item, sets the Item's nameEn to "Charger 25W", and deactivates the Item, in 3 requests
Then  : each of the 3 requests is refused with code CAT-403-FORBIDDEN, 0 Items are created, and the Item keeps nameEn "Charger" and isActiveFl true

#### AC-CAT-138 — [REQ-CAT-131]
Given : a Seller/cashier who holds only PERM_CAT_ITEMS_VIEW
When  : the user opens the Items entry of an existing Item
Then  : 0 Save actions and 0 Deactivate actions are shown, and every header field is read-only

### REQ-CAT-132 — Record of another tenant
Pattern    : unwanted
Statement  : If a request names a catalogue record of another tenant by its identifier, then the system shall answer with CAT-404-NOT-FOUND and change no record.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : One tenant's catalogue is invisible to every other tenant (contract §0, tenancy).
Source     : US-CAT-001; contract §0; given erp-core tenancy
Priority   : HIGH

#### AC-CAT-139 — [REQ-CAT-132]
Given : an Item with nameEn "Charger" in tenant A, and an Owner of tenant B holding every PERM_CAT_* permission
When  : the tenant B Owner reads that Item by its itemPk, then submits an update of its nameEn
Then  : both requests are answered with code CAT-404-NOT-FOUND, and the Item in tenant A keeps nameEn "Charger"

### REQ-CAT-133 — Stale version
Pattern    : unwanted
Statement  : If an update request carries a version older than the stored version of the record, then the system shall reject it with CAT-409-STALE-VERSION and keep the stored values.
Traces     : US-CAT-002
Entities   : ENT-CAT-001
Rationale  : A change made by one user is never silently overwritten by another.
Source     : US-CAT-002; ADR-CAT-044
Priority   : HIGH

#### AC-CAT-140 — [REQ-CAT-133]
Given : two owners read the same Item at the same version, and the first saves nameEn "Charger 25W"
When  : the second submits nameEn "Charger 30W" with the version read before the first save
Then  : the request is refused with code CAT-409-STALE-VERSION, and the Item keeps nameEn "Charger 25W"

### REQ-CAT-134 — Catalogue seeds of a new tenant
Pattern    : event
Statement  : When a tenant is provisioned, the system shall give it the CAT lookup values of A6, the number series CAT_ITEM, CAT_VARIANT and CAT_INTERNAL_BARCODE with their counters at 1, and the 8 seeded Unit e-invoice codes of D-12.
Traces     : US-CAT-009, US-CAT-021
Entities   : ENT-CAT-001, ENT-CAT-012
Rationale  : The first Item of a new tenant receives its codes, and its units carry their e-invoice units, without a setup step.
Source     : US-CAT-009; US-CAT-021; D-11; D-12; ADR-CAT-058
Priority   : HIGH

#### AC-CAT-141 — [REQ-CAT-134]
Given : a tenant provisioned from PLATFORM after CAT was installed, with no catalogue record yet
When  : its owner opens the catalogue settings, then creates an Item with nameAr «كوب زجاج»
Then  : the settings list 8 active Unit e-invoice codes, and the Item is created with code ITM-000001 and a default Variant with code VAR-000001

### REQ-CAT-135 — Change under an inactive parent
Pattern    : unwanted
Statement  : If a request changes an inactive Item other than by changing only its categoryFk, adds or updates an Item unit or adds or updates an image of an inactive Item, or adds a barcode or sets the e-invoice override of a Variant that is inactive or belongs to an inactive Item, then the system shall reject the request with RULE-CAT-053.
Traces     : US-CAT-001, US-CAT-016
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Rationale  : An inactive record has left every channel; a write under it creates data no channel can use and that the ACTIVE-state invariants (A7) never checked. Deactivating children stays allowed, so an inactive Item can always be cleaned up (ADR-CAT-063). The three repair operations that stay allowed on an inactive Item are REQ-CAT-145, REQ-CAT-146 and REQ-CAT-147 (split from this REQ at the seventh gate-analysis revise — ADR-CAT-068).
Source     : US-CAT-001; US-CAT-016; ADR-CAT-063; ADR-CAT-068
Priority   : HIGH

#### AC-CAT-144 — [REQ-CAT-135]
Given : an inactive Item with base unit PIECE and no Item unit CARTON
When  : the owner adds the Item unit CARTON with conversionFactor 12
Then  : no Item unit is created and the response shows ar: «هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا.» · en: "This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant."

### REQ-CAT-136 — Reactivate a Category
Pattern    : event
Statement  : When an owner reactivates an inactive Category whose parent is empty or active and whose nameAr no active Category under the same parent carries, the system shall set its isActiveFl to true and offer it again for selection.
Traces     : US-CAT-013
Entities   : ENT-CAT-006
Rationale  : A mistaken deactivation is undone without losing the Category or its code, which stays unique among active and inactive Categories (ADR-CAT-069).
Source     : US-CAT-013; ADR-CAT-069
Priority   : —

#### AC-CAT-157 — [REQ-CAT-136]
Given : an inactive top-level Category with code PHONES and nameAr «هواتف», and no active top-level Category named «هواتف»
When  : the owner reactivates it
Then  : isActiveFl is true, its code stays PHONES, and the Item entry's Category list offers it

#### AC-CAT-158 — [REQ-CAT-136]
Given : an inactive Category PHONES whose parent «إلكترونيات» is inactive
When  : the owner reactivates PHONES
Then  : PHONES stays inactive and the response shows ar: «الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات.» · en: "The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels."

### REQ-CAT-137 — Reactivate a Variant attribute
Pattern    : event
Statement  : When an owner reactivates an inactive Variant attribute whose nameAr no active Variant attribute carries, the system shall set its isActiveFl to true and offer it again to Variant generation.
Traces     : US-CAT-011
Entities   : ENT-CAT-003
Rationale  : A mistaken deactivation is undone without losing the attribute or its code (ADR-CAT-069).
Source     : US-CAT-011; ADR-CAT-069
Priority   : HIGH

#### AC-CAT-159 — [REQ-CAT-137]
Given : the inactive Variant attribute SIZE «المقاس», and no active attribute named «المقاس»
When  : the owner reactivates SIZE
Then  : isActiveFl is true, its code stays SIZE, and the Variant generation list offers SIZE with its active values

### REQ-CAT-138 — Reactivate a Variant attribute value
Pattern    : event
Statement  : When an owner reactivates an inactive value of an active Variant attribute whose nameAr no active value of that attribute carries, the system shall set its isActiveFl to true and offer it again to Variant generation.
Traces     : US-CAT-011
Entities   : ENT-CAT-004
Rationale  : A mistaken deactivation is undone without losing the value or its code (ADR-CAT-069).
Source     : US-CAT-011; ADR-CAT-069
Priority   : HIGH

#### AC-CAT-160 — [REQ-CAT-138]
Given : the active attribute SIZE with the inactive value XS «صغير جدًا», and no active value of SIZE named «صغير جدًا»
When  : the owner reactivates XS
Then  : isActiveFl is true, its code stays XS, and the Variant generation list for SIZE offers XS

### REQ-CAT-139 — Import past its time limit
Pattern    : unwanted
Statement  : If an item import does not complete within 120 seconds, then the system shall roll the whole import back, store no Item, Variant or barcode of it, publish no catalogue-change event for it, and answer with RULE-CAT-049.
Traces     : US-CAT-010
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Rationale  : An import is all or nothing, so a slow import never leaves half a catalogue behind; the codes already drawn stay as gaps in their series, and the owner imports the file again, split into smaller files. The target for a file of 5,000 valid rows is 60 seconds, a budget measured on the reference deployment by the test of this REQ (ADR-CAT-071, ADR-CAT-074); the 120-second limit is the guarantee.
Source     : US-CAT-010; ADR-CAT-071; ADR-CAT-074; RULE-CAT-049
Priority   : HIGH

#### AC-CAT-161 — [REQ-CAT-139]
Given : an import file of 5,000 valid rows, in a tenant with 0 Items, whose processing is held past 120 seconds
When  : the owner submits it
Then  : 0 Items, 0 Variants, 0 barcodes and 0 catalogue-change events are stored, and the response shows ar: «تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة.» · en: "The change was not saved and no change notice was sent. Try again."

### REQ-CAT-140 — Default Variant moves on its deactivation
Pattern    : event
Statement  : When the default Variant of an Item is deactivated while another Variant of the Item is active, the system shall move isDefaultFl, in the same transaction, to the remaining active Variant with the lowest code.
Traces     : US-CAT-007, US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : Consumers always find one active default Variant on an active Item to fall back to (CON-CAT-002, A7 invariant 5). Split from REQ-CAT-025 at the seventh gate-analysis revise.
Source     : US-CAT-007; ADR-CAT-072
Priority   : HIGH

#### AC-CAT-163 — [REQ-CAT-140]
Given : an active Item with 3 active Variants VAR-000010 (isDefaultFl true), VAR-000011 and VAR-000012, none of them a Bundle component
When  : the owner deactivates VAR-000010
Then  : VAR-000011 has isDefaultFl true, VAR-000010 has isDefaultFl false, and the Item has exactly 1 Variant with isDefaultFl true

### REQ-CAT-141 — Default Variant moves on generation for an Item with no active Variant
Pattern    : event
Statement  : When Variants are generated for an Item with no active Variant, the system shall give isDefaultFl to the first Variant created and clear it on the inactive former default.
Traces     : US-CAT-007, US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002
Rationale  : An Item repaired by generation returns to ACTIVE with an active default Variant (A7 invariant 5). Split from REQ-CAT-025 at the seventh gate-analysis revise.
Source     : US-CAT-007; ADR-CAT-072
Priority   : HIGH

#### AC-CAT-164 — [REQ-CAT-141]
Given : `variants` is on, an inactive STANDARD Item whose only Variant V (isDefaultFl true) was deactivated, and the active attribute SIZE with the active value M
When  : the owner generates Variants for the Item with selections SIZE [M], then reactivates the Item
Then  : the new Variant carrying M has isDefaultFl true, V has isDefaultFl false, and the reactivated Item has exactly 1 Variant with isDefaultFl true, which is active

### REQ-CAT-142 — Reactivation of an Item whose Category is inactive
Pattern    : unwanted
Statement  : If an owner reactivates an Item whose categoryFk points to an inactive Category, then the system shall reject the reactivation with RULE-CAT-026 and keep the Item inactive.
Traces     : US-CAT-001, US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : A7 invariant 2. Split from REQ-CAT-130 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-059
Priority   : HIGH

#### AC-CAT-133 — [REQ-CAT-142]
Given : an inactive Item with categoryFk OLD, where the Category OLD was deactivated after the Item
When  : the owner reactivates the Item
Then  : isActiveFl stays false and the response shows ar: «الفئة المختارة غير نشطة. اختر فئة نشطة.» · en: "The selected category is inactive. Choose an active category."

### REQ-CAT-143 — Reactivation of a Bundle with an invalid component
Pattern    : unwanted
Statement  : If an owner reactivates a Bundle holding a component line that RULE-CAT-019 refuses, then the system shall reject the reactivation with RULE-CAT-019 and keep the Bundle inactive.
Traces     : US-CAT-001, US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-007
Rationale  : A7 invariant 3. Split from REQ-CAT-130 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-059
Priority   : HIGH

#### AC-CAT-134 — [REQ-CAT-143]
Given : an inactive Bundle with 2 component lines, one of whose component Variants was deactivated after the Bundle was deactivated
When  : the owner reactivates the Bundle
Then  : isActiveFl stays false and the response shows ar: «المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها.» · en: "A component must be an active variant of an item that is not a bundle, listed once per bundle."

### REQ-CAT-144 — Reactivation of an Item whose name another active Item carries
Pattern    : unwanted
Statement  : If an owner reactivates an Item whose normalised nameAr another active Item of the tenant carries, then the system shall reject the reactivation with RULE-CAT-003 and keep the Item inactive.
Traces     : US-CAT-001
Entities   : ENT-CAT-001
Rationale  : A7 invariant 4; the repair is to rename or deactivate the other active Item, whose code the message names. Split from REQ-CAT-130 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-059; ADR-CAT-062
Priority   : HIGH

#### AC-CAT-162 — [REQ-CAT-144]
Given : an inactive Item named «كوب زجاج» with 1 active Variant and no Category, and another active Item named «كوب زجاج» with code ITM-000031, created while the first was inactive
When  : the owner reactivates the first Item
Then  : the first Item stays inactive and the response shows ar: «يوجد صنف نشط بالاسم نفسه: ITM-000031. افتح الصنف الموجود أو غيّر الاسم.» · en: "An active item already has this name: ITM-000031. Open the existing item or change the name."

### REQ-CAT-145 — Variant generation allowed on an inactive Item
Pattern    : state
Statement  : While an Item is inactive, the system shall accept a Variant generation for it under RULE-CAT-008, RULE-CAT-012, RULE-CAT-013, RULE-CAT-014, RULE-CAT-020 and RULE-CAT-052, and leave the Item inactive.
Traces     : US-CAT-001, US-CAT-011
Entities   : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Rationale  : Generation is the repair of REQ-CAT-130, so an Item with no active Variant can return to ACTIVE (ADR-CAT-068). Split from REQ-CAT-135 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-068
Priority   : HIGH

#### AC-CAT-154 — [REQ-CAT-145]
Given : `variants` is on, and an inactive STANDARD Item with 0 active Variants, 0 Variant attributes in use and the active attribute SIZE with the active value M
When  : the owner generates Variants for the Item with selections SIZE [M], then reactivates the Item
Then  : the generation creates 1 active Variant carrying M, and the reactivation sets isActiveFl to true

### REQ-CAT-146 — Category-only change allowed on an inactive Item
Pattern    : state
Statement  : While an Item is inactive, the system shall accept an update whose only change is categoryFk under RULE-CAT-026, and leave the Item inactive.
Traces     : US-CAT-001, US-CAT-013
Entities   : ENT-CAT-001, ENT-CAT-006
Rationale  : The categoryFk-only change is the repair of REQ-CAT-142 (ADR-CAT-068); any other change stays refused by REQ-CAT-135. Only RULE-CAT-026 and the version check run on this path: the name, lookup and e-invoice rules (RULE-CAT-001, 002, 003, 006, 042) judge fields that do not change here, so a name another active Item took meanwhile does not block the repair; RULE-CAT-003 is checked again at reactivation (REQ-CAT-144). Split from REQ-CAT-135 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-068
Priority   : HIGH

#### AC-CAT-155 — [REQ-CAT-146]
Given : an inactive Item with nameEn "Glass cup" and categoryFk OLD, where the Category OLD is inactive, and an active Category NEW
When  : the owner saves the Item with categoryFk NEW and every other field unchanged, then saves it with nameEn "Glass cup 2", then reactivates it
Then  : the first save sets categoryFk to NEW, the second save is refused with code CAT-422-PARENT-INACTIVE and nameEn stays "Glass cup", and the reactivation sets isActiveFl to true

### REQ-CAT-147 — Bundle component replacement allowed on an inactive Bundle
Pattern    : state
Statement  : While a Bundle Item is inactive, the system shall accept the replacement of its component lines under RULE-CAT-008, RULE-CAT-018 and RULE-CAT-019, and leave the Bundle inactive.
Traces     : US-CAT-001, US-CAT-012
Entities   : ENT-CAT-001, ENT-CAT-007
Rationale  : Replacing the components is the repair of REQ-CAT-143 (ADR-CAT-068). Split from REQ-CAT-135 at the seventh gate-analysis revise.
Source     : US-CAT-001; ADR-CAT-068
Priority   : HIGH

#### AC-CAT-156 — [REQ-CAT-147]
Given : `bundles` is on, an inactive Bundle with the component lines P × 1 and C × 1 where the Variant P was deactivated, and an active Variant Q of an active STANDARD Item
When  : the owner replaces the Bundle's component lines with C × 1 and Q × 1, then reactivates the Bundle
Then  : the Bundle holds exactly 2 active lines (C × 1 and Q × 1), and the reactivation sets isActiveFl to true

### REQ-CAT-148 — Generate the missing barcode from the label screen
Pattern    : event
Statement  : When a user holding CREATE on page CAT_BARCODE_LABELS requests an internal barcode for a Variant listed on the barcode label screen without an active barcode, the system shall assign it an internal barcode as REQ-CAT-084 states, under the rules of that operation, and bind it to the Item's base unit.
Traces     : US-CAT-018
Entities   : ENT-CAT-002, ENT-CAT-009
Rationale  : The next action of RULE-CAT-037 («Generate an internal barcode for them, then print») must be reachable by the role that prints labels; warehouse staff hold no CREATE on CAT_ITEMS, so the label screen offers the generation itself (ADR-CAT-078).
Source     : US-CAT-018; RULE-CAT-037; KB:raw-idea.md §6.5; ADR-CAT-078
Priority   : —

#### AC-CAT-171 — [REQ-CAT-148]
Given : warehouse staff holding PERM_CAT_BARCODE_LABELS_CREATE but not PERM_CAT_ITEMS_CREATE, an active Variant of an active Item with base unit PIECE and no active barcode, and series CAT_INTERNAL_BARCODE last issued 0000000099
When  : the user generates an internal barcode for that Variant from the barcode label screen, then prints 1 label of it
Then  : the Variant gets the active INTERNAL barcode 0400000001005 bound to unit PIECE, and one printable document holding 1 label is returned

## A5 — Business rules

### RULE-CAT-001 — Arabic name required
Scope      : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-006, ENT-CAT-010
Trigger    : on create · on update · on import row
Statement  : The system shall require a nameAr of 1 to 200 characters that contains at least one Arabic letter when a record of scope is saved. An Arabic letter is a character in U+0621 to U+064A; the client, the service and the database check use this one set.
Message    : ar: أدخل الاسم بالعربية (حتى 200 حرف) متضمنًا حرفًا عربيًا واحدًا على الأقل. · en: Enter the Arabic name (up to 200 characters) with at least one Arabic letter.
Traces     : REQ-CAT-027, REQ-CAT-030, REQ-CAT-042, REQ-CAT-045, REQ-CAT-064, REQ-CAT-105
Data source: ENT-CAT-001.nameAr, ENT-CAT-003.nameAr, ENT-CAT-004.nameAr, ENT-CAT-006.nameAr, ENT-CAT-010.nameAr
Source     : PRD P-3; D-07
Test-Hint  : Latin-only text and 201 characters both fail; «سامسونج A55» passes.

### RULE-CAT-002 — English name length
Scope      : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-006, ENT-CAT-010
Trigger    : on create · on update · on import row
Statement  : The system shall prevent saving a nameEn longer than 200 characters.
Message    : ar: الاسم بالإنجليزية لا يتجاوز 200 حرف. · en: The English name can have at most 200 characters.
Traces     : REQ-CAT-031
Data source: ENT-CAT-001.nameEn, ENT-CAT-003.nameEn, ENT-CAT-004.nameEn, ENT-CAT-006.nameEn, ENT-CAT-010.nameEn
Source     : PRD P-3

### RULE-CAT-003 — One active Item per name
Scope      : ENT-CAT-001
Trigger    : on create · on update · on reactivation · on import row
Statement  : The system shall prevent saving or reactivating an Item when its nameAr, after Arabic normalisation (the tashkeel marks U+064B–U+065F and U+0670 and the tatweel U+0640 removed, and nothing else — the Arabic-Indic digits U+0660–U+0669 are kept; أ إ آ ٱ read as ا, ة as ه, ى as ي; Latin letters lower-cased; leading, trailing and repeated spaces removed — ADR-CAT-062), equals the normalised nameAr of another active Item of the tenant or of an earlier valid row of the same import file.
Message    : ar: يوجد صنف نشط بالاسم نفسه: {code}. افتح الصنف الموجود أو غيّر الاسم. · en: An active item already has this name: {code}. Open the existing item or change the name.
Traces     : REQ-CAT-007, REQ-CAT-041, REQ-CAT-144
Data source: ENT-CAT-001.nameAr, ENT-CAT-001.isActiveFl, ENT-CAT-001.code ({code} = the code of the other active Item; for an earlier row of the same import file, the code the import gave to that row's Item)
Source     : D-13; US-CAT-001; ADR-CAT-062
Test-Hint  : «شنطة سفر» and «شنطه سفر» are one name; «أحمر» and «احمر» are one name; «علبة ٢٥» and «علبة ٣٠» are two names.

### RULE-CAT-004 — Codes are system-assigned
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on create · on update
Statement  : The system shall assign the code of an Item from series CAT_ITEM and of a Variant from series CAT_VARIANT on first save, and prevent any request from supplying or changing a code (NUMBERING: document numbers come from the core sequence module).
Message    : ar: يُعيَّن الرمز تلقائيًا ولا يمكن إدخاله أو تعديله. · en: The code is assigned automatically and cannot be entered or changed.
Traces     : REQ-CAT-032, REQ-CAT-033, REQ-CAT-034
Data source: ENT-CAT-001.code, ENT-CAT-002.code
Source     : US-CAT-009; profile NUMBERING rule; D-11

### RULE-CAT-005 — No record without a code
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on create
Statement  : The system shall prevent storing an Item or a Variant when the core number series returns no code.
Message    : ar: تعذّر إصدار رمز جديد الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات. · en: A new code could not be issued, and nothing was saved. Try again in a moment.
Traces     : REQ-CAT-035, REQ-CAT-148
Data source: ENT-CAT-001.code, ENT-CAT-002.code
Source     : US-CAT-009

### RULE-CAT-006 — Lookup-backed fields hold active values
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-008, ENT-CAT-009, ENT-CAT-011, ENT-CAT-012
Trigger    : on create · on update · on import row
Statement  : The system shall require every changed lookup-backed field to hold an active value of its lookup key (A6), and shall keep an unchanged value that was deactivated later.
Message    : ar: القيمة المختارة غير متاحة. اختر قيمة نشطة من القائمة. · en: The selected value is not available. Choose an active value from the list.
Traces     : REQ-CAT-073, REQ-CAT-074, REQ-CAT-075, REQ-CAT-076
Data source: ENT-CAT-001.brandCode, ENT-CAT-001.baseUomCode, ENT-CAT-001.taxClassCode, ENT-CAT-001.einvoiceCodeTypeCode, ENT-CAT-002.einvoiceCodeTypeCode, ENT-CAT-008.uomCode, ENT-CAT-009.uomCode, ENT-CAT-009.barcodeTypeCode, ENT-CAT-011.embeddedValueTypeCode, ENT-CAT-012.key, ENT-CAT-012.einvoiceUnitCode
Source     : profile LOOKUPS rule; D-02

### RULE-CAT-007 — Base unit fixed after first save
Scope      : ENT-CAT-001
Trigger    : on update
Statement  : The system shall prevent changing baseUomCode of a saved Item.
Message    : ar: لا يمكن تغيير وحدة القياس الأساسية بعد حفظ الصنف. أضف وحدة أخرى للصنف بدلًا من ذلك. · en: The base unit cannot be changed after the item is saved. Add another unit to the item instead.
Traces     : REQ-CAT-082
Data source: ENT-CAT-001.baseUomCode
Source     : D-14

### RULE-CAT-008 — Flag-bound operation refused while its flag is off
Scope      : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005, ENT-CAT-007
Trigger    : on create of an Item of kind BUNDLE · on Variant generation · on Bundle component save · on create or update of a Variant attribute · on create or update of a Variant attribute value
Statement  : The system shall prevent creating an Item of kind BUNDLE and saving Bundle components while the `bundles` flag is off, and generating Variants and creating or updating Variant attributes and Variant attribute values while the `variants` flag is off, reading the flag through the FeatureFlags API. Reading, deactivating and reactivating records that already exist stay allowed while a flag is off, so data that depends on the flag can be cleaned up.
Message    : ar: هذه الوظيفة غير مفعّلة لنشاطك. فعّل مفتاح القدرة «{flag}» من الإعدادات لاستخدامها. · en: This function is not switched on for your business. Turn on the «{flag}» capability flag in settings to use it.
Traces     : REQ-CAT-014, REQ-CAT-015, REQ-CAT-016
Data source: ENT-CAT-001.itemKindCode, ENT-CAT-005.variantAttributeValueFk, ENT-CAT-007.componentVariantFk
Source     : POL-CAT-003; DP §5 G1, G2; ADR-CAT-041
Test-Hint  : Test each flag in both states (G2).

### RULE-CAT-009 — Unknown flag value refuses the operation
Scope      : ENT-CAT-001, ENT-CAT-003, ENT-CAT-004, ENT-CAT-005, ENT-CAT-007
Trigger    : on any operation RULE-CAT-008 refuses while its flag is off
Statement  : The system shall prevent an operation that RULE-CAT-008 binds to a flag when the FeatureFlags read of that flag returns no value.
Message    : ar: تعذّر التحقق من إعدادات القدرات الآن، ولم يُحفظ شيء. أعد المحاولة بعد لحظات. · en: Capability settings could not be checked, and nothing was saved. Try again in a moment.
Traces     : REQ-CAT-013
Data source: ENT-CAT-001.itemKindCode, ENT-CAT-005.variantAttributeValueFk
Source     : ADR-CAT-041

### RULE-CAT-010 — Variants cannot be turned off while in use
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Trigger    : on turn-off check of `variants`
Statement  : The system shall answer "not allowed" when an active Variant attribute assignment exists on an active Variant of an active Item.
Message    : ar: لا يمكن إيقاف المتغيّرات لأن {count} صنفًا يستخدمها. أوقف متغيّرات هذه الأصناف أو أبقِ المتغيّرات مفعّلة. · en: Variants cannot be turned off because {count} items use them. Deactivate the variants of these items or keep variants on.
Traces     : REQ-CAT-021, REQ-CAT-022
Data source: ENT-CAT-005.variantFk, ENT-CAT-005.isActiveFl, ENT-CAT-002.isActiveFl, ENT-CAT-002.itemFk, ENT-CAT-001.isActiveFl
Source     : POL-CAT-005; DP §5 G3

### RULE-CAT-011 — Bundles cannot be turned off while in use
Scope      : ENT-CAT-001
Trigger    : on turn-off check of `bundles`
Statement  : The system shall answer "not allowed" when an active Item of kind BUNDLE exists.
Message    : ar: لا يمكن إيقاف الحزم لوجود {count} حزمة نشطة. أوقف هذه الحزم أو أبقِ الحزم مفعّلة. · en: Bundles cannot be turned off because {count} active bundles exist. Deactivate these bundles or keep bundles on.
Traces     : REQ-CAT-021, REQ-CAT-023
Data source: ENT-CAT-001.itemKindCode, ENT-CAT-001.isActiveFl
Source     : POL-CAT-005; DP §5 G3

### RULE-CAT-012 — Existing Variants keep their identity
Scope      : ENT-CAT-002, ENT-CAT-005, ENT-CAT-009
Trigger    : on Variant generation
Statement  : The system shall assign to the Item's existing active Variants the first selected value of every attribute they do not yet carry, keeping their variantPk, code and barcodes, before it creates Variants for the remaining combinations.
Message    : ar: احتفظت المتغيّرات الحالية بأرقامها وباركوداتها وأُسندت إليها القيم الأولى المختارة. · en: Existing variants kept their codes and barcodes and took the first selected values.
Traces     : REQ-CAT-018, REQ-CAT-019
Data source: ENT-CAT-002.isDefaultFl, ENT-CAT-002.itemFk, ENT-CAT-005.variantFk, ENT-CAT-005.variantAttributeFk, ENT-CAT-009.variantFk
Source     : POL-CAT-004; KB:raw-idea.md §6.8

### RULE-CAT-013 — One value per attribute and unique combinations
Scope      : ENT-CAT-002, ENT-CAT-005
Trigger    : on Variant generation
Statement  : The system shall require each active Variant of an Item to carry exactly one value of each attribute used by the Item, and prevent two active Variants of the same Item from carrying the same combination. A generation whose selections omit an attribute the Item already uses, or select no value for it, is refused under RULE-CAT-052 before any combination is checked.
Message    : ar: هذه التركيبة من القيم موجودة في متغيّر آخر لهذا الصنف. · en: This combination of values already exists on another variant of this item.
Traces     : REQ-CAT-049, REQ-CAT-051
Data source: ENT-CAT-005.variantFk, ENT-CAT-005.variantAttributeFk, ENT-CAT-005.variantAttributeValueFk, ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl
Source     : US-CAT-011

### RULE-CAT-014 — Variant limits per Item
Scope      : ENT-CAT-002, ENT-CAT-005
Trigger    : on Variant generation
Statement  : The system shall prevent a generation that would give an Item more than 3 Variant attributes or more than 300 active Variants.
Message    : ar: الحد الأقصى 3 خصائص و300 متغيّر نشط للصنف الواحد. · en: An item can have at most 3 attributes and 300 active variants.
Traces     : REQ-CAT-052
Data source: ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl, ENT-CAT-005.variantAttributeFk
Source     : D-10

### RULE-CAT-015 — Last active Variant stays active
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on Variant deactivation
Statement  : The system shall prevent deactivating the last active Variant of an active Item.
Message    : ar: لا يمكن إيقاف آخر متغيّر نشط لصنف نشط. أوقف الصنف نفسه بدلًا من ذلك. · en: The last active variant of an active item cannot be deactivated. Deactivate the item instead.
Traces     : REQ-CAT-053
Data source: ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl, ENT-CAT-001.isActiveFl
Source     : DP §8 P-04; D-01

### RULE-CAT-016 — Attribute and value codes and names unique
Scope      : ENT-CAT-003, ENT-CAT-004
Trigger    : on create · on update · on reactivation
Statement  : The system shall require a code of 1 to 30 Latin letters, digits or hyphens, unique per tenant for a Variant attribute and per attribute for a value, and a nameAr unique among the active records of the same scope; a record being reactivated keeps its code and must not carry the nameAr of another active record of its scope.
Message    : ar: الرمز أو الاسم مستخدم بالفعل. استخدم رمزًا (حتى 30 حرفًا لاتينيًا أو رقمًا أو شرطة) واسمًا مختلفين. · en: The code or name is already used. Use a different code (up to 30 Latin letters, digits or hyphens) and name.
Traces     : REQ-CAT-042, REQ-CAT-045, REQ-CAT-055, REQ-CAT-137, REQ-CAT-138
Data source: ENT-CAT-003.code, ENT-CAT-003.nameAr, ENT-CAT-003.isActiveFl, ENT-CAT-004.variantAttributeFk, ENT-CAT-004.code, ENT-CAT-004.nameAr, ENT-CAT-004.isActiveFl
Source     : US-CAT-011

### RULE-CAT-017 — Attribute or value in use stays active
Scope      : ENT-CAT-003, ENT-CAT-004
Trigger    : on deactivation
Statement  : The system shall prevent deactivating a Variant attribute or value that an active assignment of an active Variant carries.
Message    : ar: هذه الخاصية أو القيمة مستخدمة في {count} متغيّرًا نشطًا. أوقف هذه المتغيّرات أولًا. · en: This attribute or value is used by {count} active variants. Deactivate those variants first.
Traces     : REQ-CAT-044, REQ-CAT-047, REQ-CAT-054
Data source: ENT-CAT-005.variantAttributeFk, ENT-CAT-005.variantAttributeValueFk, ENT-CAT-005.isActiveFl, ENT-CAT-002.isActiveFl
Source     : US-CAT-011

### RULE-CAT-018 — Bundle has components with quantities
Scope      : ENT-CAT-001, ENT-CAT-007
Trigger    : on Bundle save
Statement  : The system shall require at least one active component line per BUNDLE Item, each with a quantity above zero, at most 999,999.999 and with at most 3 decimals. The bound fits the column (NUMERIC(18,3)); a quantity outside it is refused by the API document's check before this rule runs (CAT-400-REQUEST-INVALID), and the rule's domain check stays as the backstop the screens mirror.
Message    : ar: أضف مكوّنًا واحدًا على الأقل بكمية أكبر من صفر (حتى 3 أرقام عشرية). · en: Add at least one component with a quantity above zero (up to 3 decimals).
Traces     : REQ-CAT-056, REQ-CAT-059
Data source: ENT-CAT-007.bundleItemFk, ENT-CAT-007.quantity, ENT-CAT-007.isActiveFl
Source     : US-CAT-012; D-04

### RULE-CAT-019 — Valid Bundle components
Scope      : ENT-CAT-002, ENT-CAT-007
Trigger    : on Bundle save · on component line change · on reactivation of the Bundle
Statement  : The system shall require each component Variant to be active, to belong to an active STANDARD Item, and to appear once among the active lines of the Bundle.
Message    : ar: المكوّن يجب أن يكون متغيّرًا نشطًا لصنف ليس حزمة، ولا يتكرر في الحزمة نفسها. · en: A component must be an active variant of an item that is not a bundle, listed once per bundle.
Traces     : REQ-CAT-057, REQ-CAT-060, REQ-CAT-143, REQ-CAT-147
Data source: ENT-CAT-007.componentVariantFk, ENT-CAT-007.bundleItemFk, ENT-CAT-007.isActiveFl, ENT-CAT-002.isActiveFl, ENT-CAT-002.itemFk, ENT-CAT-001.itemKindCode, ENT-CAT-001.isActiveFl
Source     : D-04; ADR-CAT-059

### RULE-CAT-020 — A Bundle has one Variant
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005
Trigger    : on Variant generation
Statement  : The system shall prevent generating Variants or assigning Variant attribute values for an Item of kind BUNDLE.
Message    : ar: الحزمة لها متغيّر واحد فقط ولا تقبل خصائص المتغيّرات. · en: A bundle has one variant only and takes no variant attributes.
Traces     : REQ-CAT-061
Data source: ENT-CAT-001.itemKindCode, ENT-CAT-002.itemFk
Source     : D-04

### RULE-CAT-021 — Component of an active Bundle stays active
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-007
Trigger    : on Variant deactivation · on Item deactivation
Statement  : The system shall prevent deactivating a Variant, or the Item that owns it, while the Variant is an active component of an active Bundle.
Message    : ar: هذا المتغيّر مكوّن في {count} حزمة نشطة. أزله من هذه الحزم أو أوقفها أولًا. · en: This variant is a component of {count} active bundles. Remove it from those bundles or deactivate them first.
Traces     : REQ-CAT-062
Data source: ENT-CAT-007.componentVariantFk, ENT-CAT-007.bundleItemFk, ENT-CAT-007.isActiveFl, ENT-CAT-001.isActiveFl
Source     : ARCH-5; US-CAT-012

### RULE-CAT-022 — Item kind fixed after first save
Scope      : ENT-CAT-001
Trigger    : on update
Statement  : The system shall prevent changing itemKindCode of a saved Item.
Message    : ar: لا يمكن تغيير نوع الصنف بعد حفظه. أنشئ صنفًا جديدًا بالنوع المطلوب. · en: The item kind cannot be changed after saving. Create a new item of the required kind.
Traces     : REQ-CAT-063
Data source: ENT-CAT-001.itemKindCode
Source     : D-04

### RULE-CAT-023 — Category hierarchy
Scope      : ENT-CAT-006
Trigger    : on create · on update · on reactivation
Statement  : The system shall require the parent Category to be active, to be neither the Category itself nor one of its sub-categories, and the resulting depth to be at most 5 levels; a Category being reactivated must have an empty or active parent.
Message    : ar: الفئة الأم يجب أن تكون نشطة، وألا تكون الفئة نفسها أو إحدى فئاتها الفرعية، وألا يتجاوز العمق 5 مستويات. · en: The parent category must be active, must not be the category itself or one of its sub-categories, and the depth cannot exceed 5 levels.
Traces     : REQ-CAT-069, REQ-CAT-136
Data source: ENT-CAT-006.parentCategoryFk, ENT-CAT-006.isActiveFl
Source     : D-03

### RULE-CAT-024 — Category code and sibling name unique
Scope      : ENT-CAT-006
Trigger    : on create · on update · on reactivation
Statement  : The system shall require a code of 1 to 30 Latin letters, digits or hyphens unique per tenant, and a nameAr unique among the active Categories of the same parent; a Category being reactivated keeps its code and must not carry the nameAr of another active Category of its parent.
Message    : ar: يوجد رمز الفئة نفسه، أو الاسم نفسه تحت الفئة الأم نفسها. استخدم رمزًا أو اسمًا مختلفًا. · en: The category code already exists, or the name already exists under the same parent. Use a different code or name.
Traces     : REQ-CAT-070, REQ-CAT-136
Data source: ENT-CAT-006.code, ENT-CAT-006.nameAr, ENT-CAT-006.parentCategoryFk, ENT-CAT-006.isActiveFl
Source     : US-CAT-013

### RULE-CAT-025 — Category in use stays active
Scope      : ENT-CAT-006
Trigger    : on deactivation
Statement  : The system shall prevent deactivating a Category that has active sub-categories or active Items.
Message    : ar: لا يمكن إيقاف الفئة لوجود {count} فئة فرعية أو صنف نشط فيها. انقلها أو أوقفها أولًا. · en: The category cannot be deactivated while it holds {count} active sub-categories or items. Move or deactivate them first.
Traces     : REQ-CAT-071
Data source: ENT-CAT-006.parentCategoryFk, ENT-CAT-006.isActiveFl, ENT-CAT-001.categoryFk, ENT-CAT-001.isActiveFl
Source     : ARCH-5; US-CAT-013

### RULE-CAT-026 — Item Category active
Scope      : ENT-CAT-001
Trigger    : on create · on update · on import row · on reactivation
Statement  : The system shall require a changed categoryFk of an Item, and the categoryFk of an Item being reactivated, to be empty or to point to an active Category.
Message    : ar: الفئة المختارة غير نشطة. اختر فئة نشطة. · en: The selected category is inactive. Choose an active category.
Traces     : REQ-CAT-072, REQ-CAT-142, REQ-CAT-146
Data source: ENT-CAT-001.categoryFk, ENT-CAT-006.isActiveFl
Source     : US-CAT-013; ADR-CAT-059

### RULE-CAT-027 — Valid Item unit
Scope      : ENT-CAT-008
Trigger    : on create
Statement  : The system shall require an Item unit's uomCode to differ from the Item's baseUomCode and from the uomCode of its other active Item units, and its conversionFactor to be above zero, at most 999,999.999999 and with at most 6 decimals. The bound fits the column (NUMERIC(18,6)); a factor outside it is refused by the API document's check before this rule runs (CAT-400-REQUEST-INVALID), and the rule's domain check stays as the backstop the screens mirror.
Message    : ar: الوحدة يجب أن تختلف عن الوحدة الأساسية وألا تتكرر، ومعامل التحويل أكبر من صفر (حتى 6 أرقام عشرية). · en: The unit must differ from the base unit and appear once, with a conversion factor above zero (up to 6 decimals).
Traces     : REQ-CAT-077, REQ-CAT-080
Data source: ENT-CAT-008.itemFk, ENT-CAT-008.uomCode, ENT-CAT-008.conversionFactor, ENT-CAT-008.isActiveFl, ENT-CAT-001.baseUomCode
Source     : US-CAT-015

### RULE-CAT-028 — Conversion factor fixed after saving
Scope      : ENT-CAT-008
Trigger    : on update
Statement  : The system shall prevent changing conversionFactor of a saved Item unit.
Message    : ar: لا يمكن تعديل معامل التحويل بعد الحفظ. أوقف هذه الوحدة وأضفها من جديد بالمعامل الصحيح. · en: The conversion factor cannot be changed after saving. Deactivate this unit and add it again with the correct factor.
Traces     : REQ-CAT-081
Data source: ENT-CAT-008.conversionFactor
Source     : D-14

### RULE-CAT-029 — Barcode unique among active barcodes
Scope      : ENT-CAT-009
Trigger    : on create · on import row
Statement  : The system shall prevent saving a barcode whose barcodeValue equals the barcodeValue of an active barcode of the tenant or of an earlier valid row of the same import file, with SCALE barcodes compared only with SCALE barcodes.
Message    : ar: هذا الباركود مستخدم في الصنف {code}. افتح ذلك الصنف أو استخدم باركودًا آخر. · en: This barcode is already used by item {code}. Open that item or use another barcode.
Traces     : REQ-CAT-087
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.barcodeTypeCode, ENT-CAT-009.isActiveFl, ENT-CAT-001.code ({code} = the code of the Item that owns the active barcode; for an earlier row of the same import file, the code the import gave to that row's Item)
Source     : US-CAT-016

### RULE-CAT-030 — GTIN format
Scope      : ENT-CAT-009
Trigger    : on create · on import row
Statement  : The system shall require a GTIN barcodeValue of 8, 12, 13 or 14 digits whose last digit is the GS1 check digit.
Message    : ar: باركود GTIN يجب أن يتكون من 8 أو 12 أو 13 أو 14 رقمًا برقم تحقق صحيح. · en: A GTIN barcode must have 8, 12, 13 or 14 digits with a valid check digit.
Traces     : REQ-CAT-088
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.barcodeTypeCode
Source     : US-CAT-016; module-registry OI-10

### RULE-CAT-031 — Barcode unit belongs to the Item
Scope      : ENT-CAT-009
Trigger    : on create
Statement  : The system shall require a barcode's uomCode to equal the Item's baseUomCode or the uomCode of an active Item unit of the same Item.
Message    : ar: وحدة الباركود يجب أن تكون الوحدة الأساسية للصنف أو إحدى وحداته النشطة. · en: The barcode unit must be the item's base unit or one of its active units.
Traces     : REQ-CAT-089
Data source: ENT-CAT-009.uomCode, ENT-CAT-009.variantFk, ENT-CAT-008.uomCode, ENT-CAT-008.isActiveFl, ENT-CAT-001.baseUomCode
Source     : US-CAT-016

### RULE-CAT-032 — Barcodes avoid scale prefixes
Scope      : ENT-CAT-009
Trigger    : on create (GTIN, INTERNAL) · on import row · on scale label layout create
Statement  : The system shall prevent a GTIN or INTERNAL barcodeValue that begins with the key of an active scale label layout.
Message    : ar: الباركود يبدأ ببادئة ملصق ميزان نشط ({prefix}). استخدم باركودًا آخر أو عدّل إعداد الميزان. · en: The barcode starts with the prefix of an active scale label layout ({prefix}). Use another barcode or change the scale settings.
Traces     : REQ-CAT-091, REQ-CAT-041
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.barcodeTypeCode, ENT-CAT-011.key, ENT-CAT-011.isActiveFl
Source     : D-05

### RULE-CAT-033 — Scale code length
Scope      : ENT-CAT-009
Trigger    : on create (SCALE)
Statement  : The system shall require a SCALE barcodeValue to consist of digits only, with a length equal to the scaleCodeLength of an active scale label layout.
Message    : ar: رمز الميزان يجب أن يتكون من {length} أرقام، ويلزم وجود إعداد ملصق ميزان نشط. · en: The scale code must have {length} digits, and an active scale label layout is required.
Traces     : REQ-CAT-096, REQ-CAT-099
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.barcodeTypeCode, ENT-CAT-011.scaleCodeLength, ENT-CAT-011.isActiveFl
Source     : US-CAT-017; module-registry OI-10

### RULE-CAT-034 — Valid scale label layout
Scope      : ENT-CAT-011
Trigger    : on create · on update
Statement  : The system shall require a key of 02 or 20 to 29 that no other active layout uses, a scaleCodeLength of 4 to 6 and valueDecimals of 0 to 3. The key and the scaleCodeLength are set on create only; an update carries neither (ADR-CAT-064).
Message    : ar: البادئة رقمان (02 أو من 20 إلى 29) غير مستخدمة في إعداد نشط آخر، وطول رمز الميزان من 4 إلى 6، والأرقام العشرية من 0 إلى 3. · en: The prefix is two digits (02 or 20 to 29) not used by another active layout, the scale code length is 4 to 6, and the decimals are 0 to 3.
Traces     : REQ-CAT-092, REQ-CAT-093, REQ-CAT-098
Data source: ENT-CAT-011.key, ENT-CAT-011.scaleCodeLength, ENT-CAT-011.valueDecimals, ENT-CAT-011.isActiveFl
Source     : module-registry OI-10 (GS1 restricted-circulation prefixes)

### RULE-CAT-035 — Scale label decoding
Scope      : ENT-CAT-009, ENT-CAT-011
Trigger    : on barcode lookup of a 13-digit value with an active layout prefix
Statement  : The system shall require a valid EAN-13 check digit and an embedded scale code equal to an active SCALE barcodeValue before returning a Variant.
Message    : ar: تعذّر قراءة ملصق الميزان: رقم التحقق غير صحيح أو رمز الميزان غير معرَّف. اطلب الصنف بالاسم أو أضف رمز الميزان إليه. · en: The scale label could not be read: the check digit is wrong or the scale code is not defined. Find the item by name or add the scale code to it.
Traces     : REQ-CAT-097, REQ-CAT-100
Data source: ENT-CAT-011.key, ENT-CAT-011.scaleCodeLength, ENT-CAT-011.embeddedValueTypeCode, ENT-CAT-011.valueDecimals, ENT-CAT-009.barcodeValue, ENT-CAT-009.isActiveFl
Source     : US-CAT-017

### RULE-CAT-036 — Label copy limits
Scope      : ENT-CAT-002
Trigger    : on label request
Statement  : The system shall require 1 to 999 copies per Variant and at most 5,000 labels per request.
Message    : ar: عدد النسخ لكل متغيّر من 1 إلى 999، وإجمالي الملصقات لا يتجاوز 5000. · en: Copies per variant are 1 to 999, and the total cannot exceed 5,000 labels.
Traces     : REQ-CAT-103
Data source: ENT-CAT-002.variantPk (the copy counts are inputs of the label request, not stored data)
Source     : D-10

### RULE-CAT-037 — Labelled Variant carries a barcode
Scope      : ENT-CAT-002, ENT-CAT-009
Trigger    : on label request
Statement  : The system shall require every Variant of a label request to carry at least one active barcode.
Message    : ar: لا يوجد باركود نشط لـ {count} متغيّر. أنشئ لها باركودًا داخليًا ثم اطبع. · en: {count} variants have no active barcode. Generate an internal barcode for them, then print.
Traces     : REQ-CAT-104, REQ-CAT-148
Data source: ENT-CAT-009.variantFk, ENT-CAT-009.isActiveFl
Source     : US-CAT-018

### RULE-CAT-038 — Image file and count
Scope      : ENT-CAT-010
Trigger    : on upload
Statement  : The system shall require a JPG, PNG or WEBP file of at most 5 MB, held in public storage, and at most 20 active media records per Item. The file part is enforced at upload by the FILE category CAT_ITEM_IMAGE that CAT registers (allowPublic, PNG / JPEG / WEBP, 5 MB), whose refusal is the file module's own; CAT refuses the 21st active media record with this message, and answers a file reference that is not available or has no public URL as not found (ADR-CAT-076).
Message    : ar: الصورة يجب أن تكون JPG أو PNG أو WEBP بحجم لا يتجاوز 5 ميجابايت، وبحد أقصى 20 صورة نشطة للصنف. · en: The image must be JPG, PNG or WEBP of at most 5 MB, with at most 20 active images per item.
Traces     : REQ-CAT-110
Data source: ENT-CAT-010.fileDocumentRef, ENT-CAT-010.itemFk, ENT-CAT-010.isActiveFl
Source     : D-10

### RULE-CAT-039 — Exactly one main image
Scope      : ENT-CAT-010
Trigger    : on upload · on update · on removal
Statement  : The system shall keep exactly one active media record with isPrimaryFl true for every Item that has active media, by marking the first image primary, clearing the former primary when another is chosen, and promoting the lowest sortOrder when the primary is removed.
Message    : ar: للصنف صورة رئيسية واحدة فقط؛ تم تعيين الصورة المختارة رئيسية بدل السابقة. · en: An item has exactly one main image; the selected image replaced the previous main image.
Traces     : REQ-CAT-106, REQ-CAT-108, REQ-CAT-111
Data source: ENT-CAT-010.isPrimaryFl, ENT-CAT-010.itemFk, ENT-CAT-010.sortOrder, ENT-CAT-010.isActiveFl
Source     : US-CAT-019; DP §8 P-12

### RULE-CAT-040 — Image Variant belongs to the Item
Scope      : ENT-CAT-010
Trigger    : on upload · on update
Statement  : The system shall require a media record's variantFk, when set, to point to a Variant of the same Item.
Message    : ar: المتغيّر المختار لا ينتمي إلى هذا الصنف. · en: The selected variant does not belong to this item.
Traces     : REQ-CAT-112
Data source: ENT-CAT-010.variantFk, ENT-CAT-010.itemFk, ENT-CAT-002.itemFk
Source     : US-CAT-019

### RULE-CAT-041 — Publish state follows the Item
Scope      : ENT-CAT-001
Trigger    : on publish · on Item deactivation
Statement  : The system shall prevent publishing an inactive Item or an Item with no active Variant, and set publishStateCode to UNPUBLISHED when an Item is deactivated.
Message    : ar: لا يمكن نشر صنف غير نشط أو بلا متغيّر نشط. فعّل الصنف أو أحد متغيّراته أولًا. · en: An inactive item, or one with no active variant, cannot be published. Activate the item or one of its variants first.
Traces     : REQ-CAT-005, REQ-CAT-113, REQ-CAT-116
Data source: ENT-CAT-001.isActiveFl, ENT-CAT-001.publishStateCode, ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl
Source     : POL-CAT-008

### RULE-CAT-042 — E-invoice code pair and format
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on create · on update · on import row
Statement  : The system shall require einvoiceCodeTypeCode and einvoiceCode to be both present or both empty, a GS1 code of 8 to 14 digits, and an EGS code that starts with "EG-" and has at most 100 characters.
Message    : ar: أدخل نوع رمز الفاتورة الإلكترونية والرمز معًا: GS1 من 8 إلى 14 رقمًا، وEGS يبدأ بـ EG- ولا يتجاوز 100 حرف. · en: Enter the e-invoice code type and code together: GS1 is 8 to 14 digits, EGS starts with EG- and has at most 100 characters.
Traces     : REQ-CAT-117, REQ-CAT-118, REQ-CAT-123
Data source: ENT-CAT-001.einvoiceCodeTypeCode, ENT-CAT-001.einvoiceCode, ENT-CAT-002.einvoiceCodeTypeCode, ENT-CAT-002.einvoiceCode
Source     : PRD P-4; Egypt ETA e-invoice SDK (itemType)

### RULE-CAT-043 — One e-invoice unit per unit of measure
Scope      : ENT-CAT-012
Trigger    : on create
Statement  : The system shall prevent creating a Unit e-invoice code for a key that already has an active one.
Message    : ar: لهذه الوحدة رمز فاتورة إلكترونية نشط بالفعل. عدّله بدلًا من إضافة رمز جديد. · en: This unit already has an active e-invoice unit. Change it instead of adding a new one.
Traces     : REQ-CAT-120, REQ-CAT-124
Data source: ENT-CAT-012.key, ENT-CAT-012.isActiveFl
Source     : US-CAT-021

### RULE-CAT-044 — Import file acceptance
Scope      : ENT-CAT-001
Trigger    : on import submission
Statement  : The system shall require an xlsx file of at most 10 MB with at most 5,000 data rows and a header row equal to the import sheet's 11 columns.
Message    : ar: الملف غير مقبول: يجب أن يكون بصيغة xlsx بحجم لا يتجاوز 10 ميجابايت وحتى 5000 صف، وبعناوين ورقة الاستيراد نفسها. نزّل ورقة الاستيراد واستخدمها. · en: The file was not accepted: it must be xlsx, at most 10 MB and 5,000 rows, with the import sheet headers. Download the import sheet and use it.
Traces     : REQ-CAT-040
Data source: ENT-CAT-001.nameAr, ENT-CAT-001.nameEn, ENT-CAT-001.baseUomCode, ENT-CAT-001.categoryFk, ENT-CAT-001.brandCode, ENT-CAT-001.taxClassCode, ENT-CAT-001.einvoiceCodeTypeCode, ENT-CAT-001.einvoiceCode, ENT-CAT-009.barcodeValue, ENT-CAT-001.descriptionAr, ENT-CAT-001.descriptionEn (the column set the header row is checked against; type, size and row count are read from the submitted file)
Source     : US-CAT-010; D-10

### RULE-CAT-045 — Import row validation
Scope      : ENT-CAT-001, ENT-CAT-009
Trigger    : on import row
Statement  : The system shall validate each data row against RULE-CAT-001, RULE-CAT-002, RULE-CAT-003, RULE-CAT-006, RULE-CAT-026, RULE-CAT-029, RULE-CAT-030, RULE-CAT-032 and RULE-CAT-042, skip every row that fails, and report its row number, column and reason. A category code that matches no active Category is a failure of column category with the RULE-CAT-026 message.
Message    : ar: الصف {row}، العمود {column}: {reason}. صحّح الصف وأعد استيراده. · en: Row {row}, column {column}: {reason}. Correct the row and import it again.
Traces     : REQ-CAT-037, REQ-CAT-041
Data source: ENT-CAT-001.nameAr, ENT-CAT-001.nameEn, ENT-CAT-001.baseUomCode, ENT-CAT-001.categoryFk, ENT-CAT-001.brandCode, ENT-CAT-001.taxClassCode, ENT-CAT-001.einvoiceCodeTypeCode, ENT-CAT-001.einvoiceCode, ENT-CAT-009.barcodeValue
Source     : US-CAT-010; KB:raw-idea.md §6.5

### RULE-CAT-046 — Search term minimum
Scope      : ENT-CAT-001
Trigger    : on catalogue search
Statement  : The system shall require a search term of at least 2 characters before searching.
Message    : ar: اكتب حرفين على الأقل للبحث. · en: Type at least 2 characters to search.
Traces     : REQ-CAT-126
Data source: ENT-CAT-001.nameAr, ENT-CAT-001.nameEn, ENT-CAT-001.code, ENT-CAT-009.barcodeValue
Source     : D-10

### RULE-CAT-047 — Barcode not found
Scope      : ENT-CAT-009
Trigger    : on barcode lookup
Statement  : The system shall return no Variant, with a next action, when no active barcode of an active Variant of an active Item and no active scale label layout matches the value.
Message    : ar: لا يوجد صنف بهذا الباركود. ابحث بالاسم أو أضف الباركود إلى الصنف. · en: No item has this barcode. Search by name or add the barcode to the item.
Traces     : REQ-CAT-090, REQ-CAT-086
Data source: ENT-CAT-009.barcodeValue, ENT-CAT-009.isActiveFl, ENT-CAT-002.isActiveFl, ENT-CAT-001.isActiveFl, ENT-CAT-011.key, ENT-CAT-011.isActiveFl
Source     : KB:raw-idea.md §6.5; ADR-CAT-059

### RULE-CAT-048 — Deactivation keeps records for consumers (ARCH-5)
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-006, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009
Trigger    : on deactivation
Statement  : The system shall deactivate a SHARED (owner) record by setting isActiveFl to false, never by deletion, keep it readable by identifier for consuming modules, exclude it from new selections and publish a catalogue-change event with change kind DEACTIVATED.
Message    : ar: تم الإيقاف. يبقى السجل متاحًا للمستندات السابقة ولا يظهر في الاختيارات الجديدة. · en: Deactivated. The record stays available to earlier documents and no longer appears in new selections.
Traces     : REQ-CAT-005, REQ-CAT-050, REQ-CAT-066, REQ-CAT-079, REQ-CAT-085
Data source: ENT-CAT-001.isActiveFl, ENT-CAT-002.isActiveFl, ENT-CAT-006.isActiveFl, ENT-CAT-007.isActiveFl, ENT-CAT-008.isActiveFl, ENT-CAT-009.isActiveFl
Source     : ARCH-5 (effect on SOFT-READ consumers: notify, never cascade)

### RULE-CAT-049 — Change events only after commit
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-006, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Trigger    : on commit or rollback of a catalogue change
Statement  : The system shall record each catalogue-change event in the same transaction as the change and deliver it only after commit.
Message    : ar: تعذّر حفظ التغيير ولم يُرسل أي إشعار بالتغيير. أعد المحاولة. · en: The change was not saved and no change notice was sent. Try again.
Traces     : REQ-CAT-008, REQ-CAT-009, REQ-CAT-139
Data source: ENT-CAT-001.itemPk, ENT-CAT-002.variantPk, ENT-CAT-006.categoryPk
Source     : US-CAT-002; given `events` outbox; ADR-CAT-077

### RULE-CAT-050 — A reactivated Item has an active Variant
Scope      : ENT-CAT-001, ENT-CAT-002
Trigger    : on reactivation
Statement  : The system shall prevent reactivating an Item that has no active Variant.
Message    : ar: لا يمكن إعادة تفعيل صنف بلا متغيّر نشط. أنشئ متغيّرًا له أو أنشئ صنفًا جديدًا. · en: An item with no active variant cannot be reactivated. Generate a variant for it, or create a new item.
Traces     : REQ-CAT-130
Data source: ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl
Source     : A7 ACTIVE-state invariants; ADR-CAT-059

### RULE-CAT-051 — Item unit used by active barcodes stays active
Scope      : ENT-CAT-008, ENT-CAT-009
Trigger    : on Item unit deactivation
Statement  : The system shall prevent deactivating an Item unit while active barcodes of the Variants of the same Item are bound to its uomCode.
Message    : ar: هذه الوحدة مستخدمة في {count} باركود نشط. أوقف هذه الباركودات أولًا. · en: This unit is used by {count} active barcodes. Deactivate those barcodes first.
Traces     : REQ-CAT-128
Data source: ENT-CAT-008.itemFk, ENT-CAT-008.uomCode, ENT-CAT-009.uomCode, ENT-CAT-009.variantFk, ENT-CAT-009.isActiveFl, ENT-CAT-002.itemFk
Source     : US-CAT-016; ADR-CAT-060

### RULE-CAT-052 — Generation names every attribute the Item uses
Scope      : ENT-CAT-002, ENT-CAT-005
Trigger    : on Variant generation
Statement  : The system shall require a generation request to select at least one active value of every attribute that the Item's active Variants already carry.
Message    : ar: اختر قيمة واحدة على الأقل لكل خاصية يستخدمها الصنف بالفعل. · en: Select at least one value for every attribute the item already uses.
Traces     : REQ-CAT-129
Data source: ENT-CAT-005.variantFk, ENT-CAT-005.variantAttributeFk, ENT-CAT-005.isActiveFl, ENT-CAT-002.itemFk, ENT-CAT-002.isActiveFl
Source     : US-CAT-011; RULE-CAT-013

### RULE-CAT-053 — An inactive parent refuses child writes
Scope      : ENT-CAT-001, ENT-CAT-002, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010
Trigger    : on Item update that changes more than categoryFk · on Item unit create or update · on image create or update · on barcode create · on Variant e-invoice override
Statement  : The system shall prevent changing any field of an inactive Item other than categoryFk, creating or changing an Item unit or image of an inactive Item, and adding a barcode to, or setting the e-invoice override of, a Variant that is inactive or belongs to an inactive Item. Three repair operations stay allowed on an inactive Item, each under its own rules: Variant generation (RULE-CAT-008, 012, 013, 014, 020, 052), an update whose only change is categoryFk (RULE-CAT-026) and the replacement of the Bundle components (RULE-CAT-008, 018, 019) — ADR-CAT-068. Deactivating a child record stays allowed.
Message    : ar: هذا الصنف أو المتغيّر غير نشط ولا يمكن تعديله. أعد تفعيل الصنف أو اختر متغيّرًا نشطًا. · en: This item or variant is inactive and cannot be changed. Reactivate the item, or choose an active variant.
Traces     : REQ-CAT-135, REQ-CAT-145, REQ-CAT-146, REQ-CAT-147, REQ-CAT-148
Data source: ENT-CAT-001.isActiveFl, ENT-CAT-001.categoryFk, ENT-CAT-002.isActiveFl, ENT-CAT-002.itemFk, ENT-CAT-008.itemFk, ENT-CAT-009.variantFk, ENT-CAT-010.itemFk
Source     : US-CAT-001; ADR-CAT-063; ADR-CAT-068
Test-Hint  : Each refused write on an inactive Item is refused; each child deactivation and each of the three repairs on it succeeds.

## A6 — Lookups

```yaml name=lookups
lookups:
  - {key: CAT_ITEM_KIND, seeded: [STANDARD, BUNDLE], open: false, values: [STANDARD, BUNDLE], fields: [itemKindCode], owner: CAT, entity: ENT-CAT-001, control: lookup, source: "US-CAT-012; D-04"}
  - {key: CAT_PUBLISH_STATE, seeded: [UNPUBLISHED, PUBLISHED], open: false, values: [UNPUBLISHED, PUBLISHED], fields: [publishStateCode], owner: CAT, entity: ENT-CAT-001, control: lookup, source: "POL-CAT-008; US-CAT-020"}
  - {key: CAT_BRAND, seeded: [], open: true, values: [OLDBRAND, XYZ, BRAND_068, BRAND1_137, BRAND2_137, BRAND3_137, BRAND4_137, BRAND5_137, BRAND6_137, RETIRED_177], fields: [brandCode], owner: CAT, entity: ENT-CAT-001, control: lookup, source: "US-CAT-014; module-registry LOOKUPS OWNED (brand); DP §8 P-05"}
  - {key: CAT_UOM, seeded: [PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE, LITRE], open: true, values: [PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE, LITRE, BAG], fields: [baseUomCode, uomCode, key], owner: CAT, entity: "ENT-CAT-001, ENT-CAT-008, ENT-CAT-009, ENT-CAT-012", control: lookup, source: "US-CAT-014, US-CAT-015; module-registry LOOKUPS OWNED (unit-of-measure); D-12"}
  - {key: CAT_TAX_CLASS, seeded: [STANDARD, ZERO_RATED, EXEMPT], open: true, values: [STANDARD, ZERO_RATED, EXEMPT], fields: [taxClassCode], owner: CAT, entity: ENT-CAT-001, control: lookup, source: "US-CAT-021; ADR-CAT-040"}
  - {key: CAT_EINVOICE_ITEM_CODE_TYPE, seeded: [GS1, EGS], open: false, values: [GS1, EGS], fields: [einvoiceCodeTypeCode], owner: CAT, entity: "ENT-CAT-001, ENT-CAT-002", control: lookup, source: "PRD P-4; Egypt ETA e-invoice SDK itemType"}
  - {key: CAT_EINVOICE_UNIT, seeded: [EA, KGM, GRM, MTR, LTR, CT, BX, DZN], open: true, values: [EA, KGM, GRM, MTR, LTR, CT, BX, DZN, H87], fields: [einvoiceUnitCode], owner: CAT, entity: ENT-CAT-012, control: lookup, source: "US-CAT-021; Egypt ETA e-invoice SDK unitType; D-12"}
  - {key: CAT_BARCODE_TYPE, seeded: [GTIN, INTERNAL, SCALE], open: false, values: [GTIN, INTERNAL, SCALE], fields: [barcodeTypeCode], owner: CAT, entity: ENT-CAT-009, control: lookup, source: "module-registry OI-10; D-05"}
  - {key: CAT_SCALE_VALUE_TYPE, seeded: [WEIGHT, PRICE], open: false, values: [WEIGHT, PRICE], fields: [embeddedValueTypeCode], owner: CAT, entity: ENT-CAT-011, control: lookup, source: "US-CAT-017; module-registry OI-10"}
  - {key: CAT_CHANGE_KIND, seeded: [CREATED, UPDATED, DEACTIVATED, REACTIVATED], open: false, values: [CREATED, UPDATED, DEACTIVATED, REACTIVATED], fields: [], owner: CAT, entity: "catalogue-change event (REQ-CAT-008)", control: event payload, source: "US-CAT-002"}
```

Labels per language:

| Key | Value | ar | en |
|---|---|---|---|
| CAT_ITEM_KIND | STANDARD · BUNDLE | صنف عادي · حزمة | Standard item · Bundle |
| CAT_PUBLISH_STATE | UNPUBLISHED · PUBLISHED | غير منشور · منشور | Not published · Published |
| CAT_UOM | PIECE · CARTON · BOX · DOZEN · KILOGRAM · GRAM · METRE · LITRE | قطعة · كرتونة · علبة · دستة · كيلوجرام · جرام · متر · لتر | Piece · Carton · Box · Dozen · Kilogram · Gram · Metre · Litre |
| CAT_TAX_CLASS | STANDARD · ZERO_RATED · EXEMPT | خاضع للنسبة العامة · خاضع لنسبة صفر · معفى | Standard rate · Zero-rated · Exempt |
| CAT_EINVOICE_ITEM_CODE_TYPE | GS1 · EGS | GS1 · EGS | GS1 · EGS |
| CAT_EINVOICE_UNIT | EA · KGM · GRM · MTR · LTR · CT · BX · DZN | وحدة · كيلوجرام · جرام · متر · لتر · كرتونة · علبة · دستة | Each · Kilogram · Gram · Metre · Litre · Carton · Box · Dozen |
| CAT_BARCODE_TYPE | GTIN · INTERNAL · SCALE | GTIN · داخلي · ميزان | GTIN · Internal · Scale |
| CAT_SCALE_VALUE_TYPE | WEIGHT · PRICE | وزن · سعر | Weight · Price |
| CAT_CHANGE_KIND | CREATED · UPDATED · DEACTIVATED · REACTIVATED | إنشاء · تعديل · إيقاف · إعادة تفعيل | Created · Updated · Deactivated · Reactivated |

Rationale:
- All keys are held in the core lookup module (`mdl`) and loaded at runtime [DP §5 G6].
- CAT_BRAND starts empty because the list belongs to the tenant. Its `values` list every brand the analysis names: OLDBRAND and XYZ from the acceptance criteria, and BRAND_068, BRAND1_137 … BRAND6_137 and RETIRED_177, the per-case values of the test plans (named `<VALUE>_<TC seq>` so that no two cases share one).
- BAG (CAT_UOM) and H87 (CAT_EINVOICE_UNIT) are named in acceptance criteria as values added later; they are not seeded.
- A value beyond the seeded ones is host data. It is created through the given mdl call `POST /api/v1/mdl/lookup-types/{id}/values` and deactivated through `DELETE /api/v1/mdl/lookup-values/{id}`, where `{id}` is the lookup type found by `POST /api/v1/mdl/lookup-types/search`; CAT never seeds it.
- The screens read the active values of a key through CAT's own read of its keys (`GET /api/v1/cat/lookups/{key}`, ADR-CAT-065), never through a pass-through to arbitrary mdl keys.

Consumed lookups: none.

## A7 — Status lifecycle

Only ENT-CAT-001 Item has more than two transitions; it combines isActiveFl and publishStateCode. No other entity has a status field with more than two states.

```
          create
            │
            ▼
   ┌──────────────────────┐   publish (RULE-CAT-041)  ┌──────────────────────┐
   │ ACTIVE · UNPUBLISHED │ ────────────────────────▶ │ ACTIVE · PUBLISHED   │
   │                      │ ◀──────────────────────── │                      │
   └──────────────────────┘        unpublish          └──────────────────────┘
        │        ▲                                            │
 deactivate      │ reactivate                                 │ deactivate
 (RULE-CAT-021,  │ (stays UNPUBLISHED)                        │ (RULE-CAT-041 sets
  RULE-CAT-048)  │                                            │  UNPUBLISHED)
        ▼        │                                            ▼
   ┌──────────────────────┐ ◀─────────────────────────────────┘
   │ INACTIVE·UNPUBLISHED │
   └──────────────────────┘
```

**ACTIVE-state invariants (ADR-CAT-059).** An Item in ACTIVE (·UNPUBLISHED or ·PUBLISHED) always has:
1. at least one active Variant (RULE-CAT-015 while active; RULE-CAT-050 on reactivation);
2. an empty categoryFk, or one that points to an active Category (RULE-CAT-026);
3. when its kind is BUNDLE, active component lines whose Variants are active and belong to active STANDARD Items (RULE-CAT-019, RULE-CAT-021);
4. a normalised nameAr that no other active Item carries (RULE-CAT-003);
5. exactly one default Variant (isDefaultFl true), and it is active.

The default Variant moves with the Item's active Variants (REQ-CAT-140, REQ-CAT-141, ADR-CAT-072). When the default Variant is deactivated while another Variant of the Item is active, isDefaultFl moves in the same transaction, under the Item lock, to the remaining active Variant with the lowest code. When Variants are generated for an Item with no active Variant, the first Variant created takes isDefaultFl and the inactive former default loses it. An Item therefore holds at most one Variant with isDefaultFl true at any time, and while it has an active Variant, that Variant is active.

Every transition into ACTIVE (create, import row, reactivate) re-establishes these invariants, not only the guard of its own trigger. While the Item is inactive, the records it points to may still change (its Category, its component Variants), and its own children may be deactivated, but no child of it may be created or changed (RULE-CAT-053, ADR-CAT-063). Reactivation is therefore refused (REQ-CAT-130, REQ-CAT-142, REQ-CAT-143, REQ-CAT-144) until the invariants hold again. Each broken invariant has a repair that stays allowed on the inactive Item, so an inactive Item can always return to ACTIVE (ADR-CAT-068):
- invariant 1 (no active Variant): Variant generation creates an active Variant (REQ-CAT-049, REQ-CAT-145; it needs `variants` on — while the flag is off, the next action is to turn it on or to create a new Item, as RULE-CAT-050's message says);
- invariant 2 (inactive Category): an update whose only change is categoryFk points the Item to an active Category or to none (REQ-CAT-072, REQ-CAT-146), or the Category itself is reactivated (REQ-CAT-136, ADR-CAT-069);
- invariant 3 (invalid component): the Bundle components are replaced with valid lines (REQ-CAT-057, REQ-CAT-147; it needs `bundles` on);
- invariant 4 (name taken): the other active Item is renamed or deactivated; the Item's own name cannot change while it is inactive.

Deactivating an Item leaves its Variants and their barcodes active, so that the Item can be reactivated with them (REQ-CAT-005). While the Item is inactive, those barcodes no longer resolve (REQ-CAT-086, ADR-CAT-059) but still hold their values under RULE-CAT-029; the owner releases a GTIN by deactivating the barcode (REQ-CAT-085, API-CAT-016), which stays allowed on an inactive Item. Deactivating a Variant, by contrast, deactivates its barcodes and releases their values (REQ-CAT-050, ADR-CAT-063).

The other records that can be deactivated also have a way back: Categories, Variant attributes and Variant attribute values are reactivated under their own uniqueness and hierarchy rules (REQ-CAT-136 … REQ-CAT-138), and a Variant is replaced by regenerating its combination. The channel reads of an Item's children (barcode lookup, Variant search, labels) also require an active Item (REQ-CAT-086, REQ-CAT-101). Only the reads by identifier return inactive records (RULE-CAT-048).

No module-specific approval flow: no story asks for one, and the profile forbids a workflow engine.

## A8 — Module dependencies

```yaml name=module-dependencies
consumes: []
```

CAT is first in the fixed module order and consumes no entity of another business module [KB:raw-idea.md §5]. Its uses of erp-core are given dependencies, listed in A9; they create no XM edge.

| External service | Purpose | Integration kind |
|---|---|---|
| — | CAT calls no external service; e-invoice submission to tax authorities belongs to TAX | — |

## A9 — Given usage

```yaml name=given-usage
rows:
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "load the values of the CAT_* lookup keys (brand, unit of measure, tax class, e-invoice codes, barcode type, scale value type, item kind, publish state) at runtime"}
  - {given: erp-core, module: modules.mdl, via: com.erp.mdl.crossmodule.MdlLookupApi, purpose: "serve CAT's own lookup keys to the screens (API-CAT-052) through the common owned-lookups capability, com.erp.common.lookup.OwnedLookups.read(key, the 10 CAT keys, CAT-404-LOOKUP-KEY-UNKNOWN, MdlLookupApi::readActiveValuesByKey) — never a pass-through for other keys (ADR-CAT-065); erp-core's manifest lists owned-lookups under common but publishes no interface for it, so the row names the mdl interface the call goes through"}
  - {given: erp-core, module: modules.cu, via: com.erp.cu.crossmodule.SettingsApi, purpose: "read the `variants` and `bundles` capability flags behind the FeatureFlags API (ADR-CAT-041)"}
  - {given: erp-core, module: modules.sequence, via: com.erp.sequence.crossmodule.NumberSeriesApi, purpose: "issue Item codes (CAT_ITEM), Variant codes (CAT_VARIANT) and internal barcode digits (CAT_INTERNAL_BARCODE)"}
  - {given: erp-core, module: modules.file, via: docs/api-docs/file/index.md, purpose: "the client uploads Item images to the FILE category CAT_ITEM_IMAGE (allowPublic = true, PNG / JPEG / WEBP, at most 5 MB — the FILE endpoint refuses any other file, ADR-CAT-076) and import files to the private storage through the given FILE upload endpoints, and passes the returned FILE_DOCUMENT.ID to CAT; CAT has no multipart endpoint (ADR-CAT-056)"}
  - {given: erp-core, module: modules.file, via: com.erp.file.crossmodule.FileDocumentLookupApi, purpose: "the published methods only: isAvailable (a referenced file exists and is available in the caller's tenant — API-CAT-018, API-CAT-049), publicUrl and publicUrls (an image has a public URL, and image URLs for reads); the type, size and bytes of an import file are not published and wait on CORE-GAP-file-document-metadata-read (ADR-CAT-076)"}
  - {given: erp-core, module: modules.file, via: com.erp.file.crossmodule.FilePrivateStoreApi, purpose: "the private store that holds the uploaded xlsx import file (SCR-REQ-CAT-004, ADR-CAT-056), named in CORE-GAP-file-document-metadata-read as the candidate in-process read of that file's type, size and bytes for the import (RULE-CAT-044, API-CAT-049); CAT stores no file through it (the client uploads through the FILE endpoints) and wires the read when the core publishes it (ADR-CAT-076)"}
  - {given: erp-core, module: modules.events, via: com.erp.events.OutboxPublisher, purpose: "record catalogue-change events in the change transaction and deliver them after commit (RULE-CAT-049)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.crossmodule.PrintApi, purpose: "produce the printable barcode label document (REQ-CAT-102) and the xlsx item import sheet (REQ-CAT-036)"}
  - {given: erp-core, module: modules.report, via: com.erp.report.ReportProvider, purpose: "register the catalogue reports CAT_BARCODE_LABEL and CAT_ITEM_IMPORT_SHEET (ADR-CAT-057)"}
  - {given: erp-core, module: modules.sec, via: com.erp.sec.permission.PermissionContributor, purpose: "register the CAT page codes so the security module derives their VIEW/CREATE/UPDATE/DELETE permissions"}
  - {given: erp-core, module: modules.tenant, via: com.erp.tenant.TenantProvisioningContributor, purpose: "copy the 8 seeded Unit e-invoice codes into each newly provisioned tenant and register the FILE category CAT_ITEM_IMAGE there (ADR-CAT-076); the given copies the CAT lookup values and number series itself (ADR-CAT-058, REQ-CAT-134)"}
```

No ENT re-creates an entity that a given provides. Tenant, User, Role, LookupType/LookupValue, FileDocument and NumberSeries are used, never re-declared [C:C-GIVEN-1].

# PART B — SCREEN REQUIREMENTS

## SCR-REQ-CAT-001 — Items / الأصناف

### B1 — Definition
Purpose      : Find, create and maintain Items with their Variants, units, barcodes, Bundle components, images, e-invoice data and publish state.
Entities     : ENT-CAT-001, ENT-CAT-002, ENT-CAT-005, ENT-CAT-007, ENT-CAT-008, ENT-CAT-009, ENT-CAT-010, ENT-CAT-006 (reference)
Operations   : search, list, create, read, update, deactivate, reactivate, publish, unpublish, generate Variants, deactivate Variant, e-invoice override, add/deactivate Item unit, add GTIN / generate internal / add scale barcode, deactivate barcode, add/update/remove image, maintain Bundle components
Users        : Owner (all), Seller/cashier (read), Warehouse staff (read)
Navigation   : Catalog / الكتالوج → Items / الأصناف; from: home, global search result, Categories (Items of a Category); to: Variant attributes, Barcode labels, Item import
Content shape: header + repeating lines (Variants, Item units, barcodes, Bundle components, images)
Traces       : REQ-CAT-001 … REQ-CAT-007, REQ-CAT-014 … REQ-CAT-020, REQ-CAT-024 … REQ-CAT-035, REQ-CAT-049 … REQ-CAT-063, REQ-CAT-072 … REQ-CAT-091, REQ-CAT-096, REQ-CAT-105 … REQ-CAT-119, REQ-CAT-128 … REQ-CAT-133, REQ-CAT-135, REQ-CAT-140 … REQ-CAT-147
Composite    : Search + Entry — one screen requirement
Inactive Item: the Entry of an inactive Item is read-only except Reactivate, the line deactivations and the three repairs — the Category field, "Generate variants" (while `variants` is on) and the Bundle components (while `bundles` is on) (REQ-CAT-135, REQ-CAT-145 … REQ-CAT-147, RULE-CAT-053, ADR-CAT-068).

### B2 — Search / list
| Filter | Source | Result column |
|---|---|---|
| Text (nameAr, nameEn, code, barcode) | ENT-CAT-001.nameAr / nameEn / code; ENT-CAT-009.barcodeValue | code, nameAr, nameEn |
| Category | ENT-CAT-001.categoryFk → ENT-CAT-006 | Category nameAr |
| Brand | ENT-CAT-001.brandCode — CAT_BRAND | Brand |
| Item kind (shown only while `bundles` is on — REQ-CAT-015) | ENT-CAT-001.itemKindCode — CAT_ITEM_KIND | Item kind |
| Publish state | ENT-CAT-001.publishStateCode — CAT_PUBLISH_STATE | Publish state |
| Active | ENT-CAT-001.isActiveFl | Active |

Sorted by nameAr (REQ-CAT-003). Applies REQ-CAT-014 and REQ-CAT-015 to filters.

### B3 — Input
- **Header (mandatory, outside Advanced — 4 fields, REQ-CAT-026):** ENT-CAT-001.nameAr, baseUomCode (pre-filled PIECE), taxClassCode (pre-filled STANDARD), itemKindCode (pre-filled STANDARD; shown while `bundles` is on).
- **Header (read-only):** ENT-CAT-001.code, publishStateCode, isActiveFl.
- **Advanced:** ENT-CAT-001.nameEn, categoryFk, brandCode, descriptionAr, descriptionEn, einvoiceCodeTypeCode, einvoiceCode.
- **Variants section (while `variants` is on — REQ-CAT-014):** ENT-CAT-002 lines (code, derived names, attribute values from ENT-CAT-005, e-invoice override, isActiveFl). The action "Generate variants" selects values of ENT-CAT-003/ENT-CAT-004.
- **Units section:** ENT-CAT-008 lines (uomCode, conversionFactor, isPurchaseFl, isSalesFl).
- **Barcodes section:** ENT-CAT-009 lines per Variant (barcodeValue, barcodeTypeCode, uomCode). SCALE is offered per REQ-CAT-096.
- **Bundle components section (kind BUNDLE, while `bundles` is on):** ENT-CAT-007 lines (componentVariantFk, quantity, sortOrder).
- **Images section:** ENT-CAT-010 lines (image file — uploaded through the core FILE endpoints, then referenced by fileDocumentRef (ADR-CAT-056) — nameAr, nameEn, variantFk, sortOrder, isPrimaryFl).

| Action | Operation | RULEs applied |
|---|---|---|
| Save | create (REQ-CAT-024, REQ-CAT-025) / update (REQ-CAT-004) | RULE-CAT-001, 002, 003, 004, 005, 006, 007, 022, 026, 042, 053 |
| Deactivate / Reactivate | REQ-CAT-005 / REQ-CAT-006, REQ-CAT-130, REQ-CAT-142, REQ-CAT-143, REQ-CAT-144 | RULE-CAT-021, 041, 048 / RULE-CAT-003, 019, 026, 050 |
| Publish / Unpublish | REQ-CAT-113 / REQ-CAT-114 | RULE-CAT-041 |
| Generate variants (also on an inactive Item — ADR-CAT-068) | REQ-CAT-049, REQ-CAT-129, REQ-CAT-141, REQ-CAT-145 | RULE-CAT-008, 012, 013, 014, 020, 052 |
| Deactivate variant (its barcodes and assignments with it) | REQ-CAT-050, REQ-CAT-140 | RULE-CAT-015, 021, 048 |
| Save variant e-invoice override | REQ-CAT-118 | RULE-CAT-042, 053 |
| Add unit / change flags / deactivate unit | REQ-CAT-077 / 078 / 079, REQ-CAT-128 | RULE-CAT-027, 028, 048, 051, 053 |
| Add GTIN / Generate internal barcode / Add scale code | REQ-CAT-083 / 084 / 096 | RULE-CAT-029, 030, 031, 032, 033, 053 |
| Deactivate barcode | REQ-CAT-085 | RULE-CAT-048 |
| Save Bundle components (also on an inactive Bundle — ADR-CAT-068) | REQ-CAT-056, REQ-CAT-057, REQ-CAT-147 | RULE-CAT-008, 018, 019 |
| Upload / update / remove image | REQ-CAT-105 / 106 / 107 | RULE-CAT-038, 039, 040, 053 |
| Import items | opens SCR-REQ-CAT-004 | — |
| Print labels | opens SCR-REQ-CAT-005 | — |

### B4 — Access
Page code: CAT_ITEMS (one SEC_PAGES row)

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | ✓ | ✓ |
| Seller/cashier | ✓ | — | — | — |
| Warehouse staff | ✓ | — | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create Item | POST | /api/v1/cat/items | Item fields | Item with default Variant | RULE-CAT-001, 002, 003, 004, 005, 006, 008, 026, 042 | REQ-CAT-024, 025, 032, 033 |
| search Items | GET | /api/v1/cat/items | filters of B2, paging | Page of Item | — | REQ-CAT-003 |
| read Item | GET | /api/v1/cat/items/{id} | id | Item with lines | — | REQ-CAT-002, 058, 119 |
| update Item | PUT | /api/v1/cat/items/{id} | Item fields | Item | RULE-CAT-001, 002, 003, 004, 006, 007, 022, 026, 042, 053 | REQ-CAT-004, 117, 135, 146 |
| deactivate Item | DELETE | /api/v1/cat/items/{id} | id | confirmation | RULE-CAT-021, 041, 048 | REQ-CAT-005 |
| reactivate / publish / unpublish Item | PATCH | /api/v1/cat/items/{id} | isActiveFl or publishStateCode | Item | RULE-CAT-003, 019, 026, 041, 050 | REQ-CAT-006, 113, 114, 130, 142, 143, 144 |
| generate Variants | POST | /api/v1/cat/items/{id}/variants | selected attribute values | list of Variant | RULE-CAT-008, 012, 013, 014, 020, 052 (allowed on an inactive Item — ADR-CAT-068) | REQ-CAT-049, 129, 141, 145 |
| update Variant override | PUT | /api/v1/cat/variants/{id} | einvoiceCodeTypeCode, einvoiceCode | Variant | RULE-CAT-004, 042, 053 | REQ-CAT-118, 135 |
| deactivate Variant | DELETE | /api/v1/cat/variants/{id} | id | confirmation | RULE-CAT-015, 021, 048 | REQ-CAT-050, 140 |
| add Item unit | POST | /api/v1/cat/items/{id}/units | uomCode, conversionFactor, flags | Item unit | RULE-CAT-006, 027, 053 | REQ-CAT-077, 135 |
| update Item unit | PUT | /api/v1/cat/item-units/{id} | isPurchaseFl, isSalesFl | Item unit | RULE-CAT-028, 053 | REQ-CAT-078, 135 |
| deactivate Item unit | DELETE | /api/v1/cat/item-units/{id} | id | confirmation | RULE-CAT-048, 051 | REQ-CAT-079, 128 |
| add barcode | POST | /api/v1/cat/variants/{id}/barcodes | barcodeValue, barcodeTypeCode, uomCode | Barcode | RULE-CAT-006, 029, 030, 031, 032, 033, 053 | REQ-CAT-083, 096, 135 |
| generate internal barcode | POST | /api/v1/cat/variants/{id}/barcodes/internal | uomCode | Barcode | RULE-CAT-005, 031, 053 | REQ-CAT-084, 135 |
| deactivate barcode | DELETE | /api/v1/cat/barcodes/{id} | id | confirmation | RULE-CAT-048 | REQ-CAT-085 |
| barcode lookup (incl. scale label) | GET | /api/v1/cat/barcodes/lookup | value | Variant, Item, unit, embedded value | RULE-CAT-035, 047 | REQ-CAT-086, 090, 097, 100 |
| save Bundle components | PUT | /api/v1/cat/items/{id}/bundle-components | component lines | list of Bundle component | RULE-CAT-008, 018, 019 (allowed on an inactive Item — ADR-CAT-068) | REQ-CAT-056, 057, 147 |
| add image | POST | /api/v1/cat/items/{id}/media | fileDocumentRef (the file uploaded through the core FILE endpoints — ADR-CAT-056), nameAr, nameEn, variantFk | Item media | RULE-CAT-038, 039, 040, 053 | REQ-CAT-105, 108, 135 |
| update image | PUT | /api/v1/cat/media/{id} | sortOrder, isPrimaryFl, names, variantFk | Item media | RULE-CAT-039, 040, 053 | REQ-CAT-106, 135 |
| remove image | DELETE | /api/v1/cat/media/{id} | id | confirmation | RULE-CAT-039 | REQ-CAT-107, 111 |
| images of a Variant | GET | /api/v1/cat/variants/{id}/media | id | list of Item media | — | REQ-CAT-109 |
| active values of a CAT lookup key | GET | /api/v1/cat/lookups/{key} | one of the 10 CAT keys of A6 | list of code + ar/en label | — | REQ-CAT-073, 074, 076 |

Cross-module read operations (contract stage P1.5, not HTTP): read Item / Variant by identifier (REQ-CAT-001, 010, 119), storefront catalogue read (REQ-CAT-115), Items of a Category (REQ-CAT-068), barcode lookup (REQ-CAT-086).

## SCR-REQ-CAT-002 — Categories / الفئات

### B1 — Definition
Purpose      : Organise Items into a tree of Categories and sub-categories.
Entities     : ENT-CAT-006, ENT-CAT-001 (Items of a Category)
Operations   : list (tree), create, read, update (move), deactivate, reactivate, list Items of a Category
Users        : Owner (all), Seller/cashier and Warehouse staff (read)
Navigation   : Catalog / الكتالوج → Categories / الفئات; from: Items; to: Items (filtered by Category)
Content shape: true hierarchy (parent/child)
Traces       : REQ-CAT-064 … REQ-CAT-072, REQ-CAT-136
Composite    : Master + Detail (tree + node entry) — one screen requirement

### B2 — Search / list
| Filter | Source | Result column |
|---|---|---|
| Text (code, nameAr, nameEn) | ENT-CAT-006.code / nameAr / nameEn | code, nameAr, nameEn |
| Active | ENT-CAT-006.isActiveFl | Active |

Tree ordered by sortOrder under each parent (REQ-CAT-067).

### B3 — Input
- Mandatory (2 fields): ENT-CAT-006.code, nameAr.
- Advanced: nameEn, parentCategoryFk, sortOrder.

| Action | Operation | RULEs applied |
|---|---|---|
| Save | REQ-CAT-064 / REQ-CAT-065, REQ-CAT-008 | RULE-CAT-001, 002, 023, 024, 049 |
| Deactivate | REQ-CAT-066, REQ-CAT-008 | RULE-CAT-025, 048, 049 |
| Reactivate | REQ-CAT-136, REQ-CAT-008 | RULE-CAT-023, 024, 049 |
| Show Items | REQ-CAT-068 | — |

### B4 — Access
Page code: CAT_CATEGORIES

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | ✓ | ✓ |
| Seller/cashier | ✓ | — | — | — |
| Warehouse staff | ✓ | — | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create Category | POST | /api/v1/cat/categories | code, nameAr, nameEn, parentCategoryFk, sortOrder | Category | RULE-CAT-001, 002, 023, 024, 049 | REQ-CAT-064, 008 |
| list Category tree | GET | /api/v1/cat/categories | text, isActiveFl | tree of Category | — | REQ-CAT-067 |
| read Category | GET | /api/v1/cat/categories/{id} | id | Category | — | REQ-CAT-067 |
| update Category | PUT | /api/v1/cat/categories/{id} | names, parentCategoryFk, sortOrder | Category | RULE-CAT-023, 024, 049 (code fixed) | REQ-CAT-065, 008 |
| deactivate Category | DELETE | /api/v1/cat/categories/{id} | id | confirmation | RULE-CAT-025, 048, 049 | REQ-CAT-066, 008 |
| reactivate Category | PATCH | /api/v1/cat/categories/{id} | isActiveFl true | Category | RULE-CAT-023, 024, 049 | REQ-CAT-136, 008 |
| Items of a Category | GET | /api/v1/cat/categories/{id}/items | id, paging | Page of Item | — | REQ-CAT-068 |

## SCR-REQ-CAT-003 — Variant attributes / خصائص المتغيّرات

### B1 — Definition
Purpose      : Keep the tenant's Variant attributes (size, colour) and their values.
Entities     : ENT-CAT-003, ENT-CAT-004
Operations   : list, create, read, update, deactivate, reactivate (attribute and value)
Users        : Owner (all), Seller/cashier and Warehouse staff (read)
Navigation   : Catalog / الكتالوج → Variant attributes / خصائص المتغيّرات (menu entry present only while `variants` is on — REQ-CAT-014); from: Items; to: Items
Content shape: header + repeating lines (attribute + its values)
Traces       : REQ-CAT-014, REQ-CAT-016, REQ-CAT-042 … REQ-CAT-048, REQ-CAT-054, REQ-CAT-055, REQ-CAT-137, REQ-CAT-138
Composite    : Master + Detail — one screen requirement

### B2 — Search / list
| Filter | Source | Result column |
|---|---|---|
| Text (code, nameAr) | ENT-CAT-003.code / nameAr | code, nameAr, nameEn |
| Active | ENT-CAT-003.isActiveFl | Active |

Ordered by sortOrder (REQ-CAT-048).

### B3 — Input
- Attribute, mandatory (2 fields): ENT-CAT-003.code, nameAr. Advanced: nameEn, sortOrder.
- Value lines, mandatory (2 fields): ENT-CAT-004.code, nameAr. Advanced: nameEn, sortOrder.

| Action | Operation | RULEs applied |
|---|---|---|
| Save attribute / value | REQ-CAT-042, 043 / REQ-CAT-045, 046 | RULE-CAT-001, 002, 008, 016 |
| Deactivate attribute / value (allowed while `variants` is off — RULE-CAT-008) | REQ-CAT-044 / REQ-CAT-047 | RULE-CAT-017 |
| Reactivate attribute / value (allowed while `variants` is off — RULE-CAT-008) | REQ-CAT-137 / REQ-CAT-138 | RULE-CAT-016 |

### B4 — Access
Page code: CAT_VARIANT_ATTRIBUTES

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | ✓ | ✓ |
| Seller/cashier | ✓ | — | — | — |
| Warehouse staff | ✓ | — | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| list attributes with values | GET | /api/v1/cat/variant-attributes | text, isActiveFl | list of Variant attribute with values | — (a read stays allowed while `variants` is off — RULE-CAT-008) | REQ-CAT-048 |
| create attribute | POST | /api/v1/cat/variant-attributes | code, nameAr, nameEn, sortOrder | Variant attribute | RULE-CAT-001, 002, 008, 016 | REQ-CAT-042 |
| update attribute | PUT | /api/v1/cat/variant-attributes/{id} | nameAr, nameEn, sortOrder | Variant attribute | RULE-CAT-001, 002, 008, 016 | REQ-CAT-043 |
| deactivate attribute | DELETE | /api/v1/cat/variant-attributes/{id} | id | confirmation | RULE-CAT-017 | REQ-CAT-044 |
| reactivate attribute | PATCH | /api/v1/cat/variant-attributes/{id} | isActiveFl true | Variant attribute | RULE-CAT-016 | REQ-CAT-137 |
| create value | POST | /api/v1/cat/variant-attributes/{id}/values | code, nameAr, nameEn, sortOrder | Variant attribute value | RULE-CAT-001, 002, 008, 016 | REQ-CAT-045 |
| update value | PUT | /api/v1/cat/variant-attribute-values/{id} | nameAr, nameEn, sortOrder | Variant attribute value | RULE-CAT-001, 002, 008, 016 | REQ-CAT-046 |
| deactivate value | DELETE | /api/v1/cat/variant-attribute-values/{id} | id | confirmation | RULE-CAT-017 | REQ-CAT-047 |
| reactivate value | PATCH | /api/v1/cat/variant-attribute-values/{id} | isActiveFl true | Variant attribute value | RULE-CAT-016 | REQ-CAT-138 |

## SCR-REQ-CAT-004 — Item import / استيراد الأصناف

### B1 — Definition
Purpose      : Bring an existing item list in from Excel and learn what to fix for every row that could not come in.
Entities     : ENT-CAT-001, ENT-CAT-002, ENT-CAT-009
Operations   : download import sheet, submit file (custom), read result
Users        : Owner
Navigation   : Setup wizard step 5 "import items" (wizard owner: P-02) → Item import / استيراد الأصناف; also Catalog / الكتالوج → Items → Import; to: Items
Content shape: other (upload + result list)
Traces       : REQ-CAT-036 … REQ-CAT-041, REQ-CAT-139
Composite    : Wizard step — one screen requirement

### B2 — Search / list
Not applicable. The result list shows rejected rows (row number, column, reason ar/en) from REQ-CAT-039.

### B3 — Input
- Mandatory (1 field): the xlsx file, uploaded through the core FILE endpoints to the private storage and passed to CAT as fileDocumentRef (ADR-CAT-056).

| Action | Operation | RULEs applied |
|---|---|---|
| Download import sheet | REQ-CAT-036 | — |
| Import | REQ-CAT-037, 038, 039 | RULE-CAT-044, 045 |

### B4 — Access
Page code: CAT_ITEM_IMPORT

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| download import sheet | GET | /api/v1/cat/item-imports/sheet | — | xlsx file (report CAT_ITEM_IMPORT_SHEET rendered by the core print service — ADR-CAT-057) | — | REQ-CAT-036 |
| import items | POST | /api/v1/cat/item-imports | fileDocumentRef of the uploaded xlsx file | import result (counts, rejected rows) | RULE-CAT-044, 045, 049 | REQ-CAT-037, 038, 039, 040, 041, 139 |

## SCR-REQ-CAT-005 — Barcode labels / ملصقات الباركود

### B1 — Definition
Purpose      : Print barcode labels for chosen Variants.
Entities     : ENT-CAT-002, ENT-CAT-009
Operations   : search Variants, generate a missing internal barcode (custom — ADR-CAT-078), create label request (print)
Users        : Owner, Warehouse staff
Navigation   : Catalog / الكتالوج → Barcode labels / ملصقات الباركود; from: Items; to: Items (read the Item of a line)
Content shape: header + repeating lines (Variant, copies)
Traces       : REQ-CAT-101 … REQ-CAT-104, REQ-CAT-148
Composite    : Search + Entry — one screen requirement

### B2 — Search / list
| Filter | Source | Result column |
|---|---|---|
| Text (Variant code, Item nameAr, barcode) | ENT-CAT-002.code; ENT-CAT-001.nameAr; ENT-CAT-009.barcodeValue | Variant code, Variant nameAr, barcode |

### B3 — Input
- Lines, mandatory (2 fields): Variant (ENT-CAT-002), copies.

| Action | Operation | RULEs applied |
|---|---|---|
| Generate internal barcode (on a line without an active barcode) | REQ-CAT-148 | RULE-CAT-005, 006, 029, 031, 032, 053 |
| Print | REQ-CAT-102 | RULE-CAT-036, 037 |

### B4 — Access
Page code: CAT_BARCODE_LABELS

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | — | — |
| Warehouse staff | ✓ | ✓ | — | — |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| search Variants | GET | /api/v1/cat/variants | text, paging | Page of Variant with first barcode | — | REQ-CAT-101 |
| generate internal barcode for a label line | POST | /api/v1/cat/barcode-labels/variants/{id}/internal-barcode | uomCode (optional; default the base unit) | Barcode | RULE-CAT-005, 006, 029, 031, 032, 053 | REQ-CAT-148 |
| print labels | POST | /api/v1/cat/barcode-labels | list of variantPk + copies | printable document | RULE-CAT-036, 037 | REQ-CAT-102, 103, 104 |

## SCR-REQ-CAT-006 — Catalogue settings / إعدادات الكتالوج

### B1 — Definition
Purpose      : Maintain the scale label layouts and the e-invoice unit of each unit of measure, and see what blocks turning a catalogue capability flag off.
Entities     : ENT-CAT-011, ENT-CAT-012, ENT-CAT-001, ENT-CAT-002, ENT-CAT-005 (turn-off check)
Operations   : read, create, update, deactivate (layouts and Unit e-invoice codes); turn-off check (custom)
Users        : Owner
Navigation   : Catalog / الكتالوج → Catalogue settings / إعدادات الكتالوج; from: Items; to: Items (dependent Items of a turn-off check)
Content shape: other (two flat lists + check panel)
Traces       : REQ-CAT-021 … REQ-CAT-023, REQ-CAT-092 … REQ-CAT-095, REQ-CAT-098, REQ-CAT-120 … REQ-CAT-124, REQ-CAT-134
Composite    : Master + Detail per list — one screen requirement

### B2 — Search / list
Not applicable: both lists are short and shown in full (REQ-CAT-095).

### B3 — Input
- Scale label layout, mandatory (4 fields): ENT-CAT-011.key, scaleCodeLength (pre-filled 5), embeddedValueTypeCode (pre-filled WEIGHT), valueDecimals (pre-filled 3). Advanced: valueAr, valueEn. key and scaleCodeLength are read-only after creation (ADR-CAT-064).
- Unit e-invoice code, mandatory (2 fields): ENT-CAT-012.key, einvoiceUnitCode.
- Turn-off check panel: flag (`variants`, `bundles`). Result: allowed, count, up to 20 Items.

| Action | Operation | RULEs applied |
|---|---|---|
| Save layout / deactivate layout | REQ-CAT-092, 093 / REQ-CAT-094 | RULE-CAT-006, 034 |
| Save / deactivate Unit e-invoice code | REQ-CAT-120, 121 / REQ-CAT-122 | RULE-CAT-006, 043 |
| Check turn-off | REQ-CAT-021 | RULE-CAT-010, 011 |

### B4 — Access
Page code: CAT_SETTINGS

| Role | VIEW | CREATE | UPDATE | DELETE |
|---|---|---|---|---|
| Owner | ✓ | ✓ | ✓ | ✓ |

### B5 — API expectations
| Operation | Verb | Path | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read catalogue settings | GET | /api/v1/cat/settings | — | layouts + Unit e-invoice codes | — | REQ-CAT-095 |
| create layout | POST | /api/v1/cat/scale-label-layouts | key, scaleCodeLength, embeddedValueTypeCode, valueDecimals, valueAr, valueEn | Scale label layout | RULE-CAT-006, 034 | REQ-CAT-092 |
| update layout | PUT | /api/v1/cat/scale-label-layouts/{id} | fields except key and scaleCodeLength | Scale label layout | RULE-CAT-034 | REQ-CAT-093 |
| deactivate layout | DELETE | /api/v1/cat/scale-label-layouts/{id} | id | confirmation | — | REQ-CAT-094 |
| create Unit e-invoice code | POST | /api/v1/cat/unit-einvoice-codes | key, einvoiceUnitCode | Unit e-invoice code | RULE-CAT-006, 043 | REQ-CAT-120 |
| update Unit e-invoice code | PUT | /api/v1/cat/unit-einvoice-codes/{id} | einvoiceUnitCode | Unit e-invoice code | RULE-CAT-006 | REQ-CAT-121 |
| deactivate Unit e-invoice code | DELETE | /api/v1/cat/unit-einvoice-codes/{id} | id | confirmation | — | REQ-CAT-122 |
| capability turn-off check | GET | /api/v1/cat/capability-checks/turn-off | flag | allowed, count, Items | RULE-CAT-010, 011 | REQ-CAT-021, 022, 023 |

# STANDALONE

## Traceability matrix

| US | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-CAT-001 | REQ-CAT-001 … 007, 130, 131, 132, 135, 142 … 147 | AC-CAT-001 … 007, 132 … 134, 137 … 139, 144, 149, 154 … 156, 162 | RULE-CAT-003, 019, 026, 041, 048, 050, 053 | ENT-CAT-001, 002, 005, 006, 007, 008, 009, 010 | SCR-REQ-CAT-001 |
| US-CAT-002 | REQ-CAT-008 … 010, 133 | AC-CAT-008 … 010, 140, 165 | RULE-CAT-049 | ENT-CAT-001, 002, 005, 006, 007, 008, 009, 010 | SCR-REQ-CAT-001 |
| US-CAT-003 | REQ-CAT-011 … 013 | AC-CAT-011 … 013 | RULE-CAT-009 | ENT-CAT-001, 002, 003, 005, 007 | SCR-REQ-CAT-001, 003 |
| US-CAT-004 | REQ-CAT-014 … 016 | AC-CAT-014 … 017, 143 | RULE-CAT-008 | ENT-CAT-001, 002, 003, 004, 005, 007 | SCR-REQ-CAT-001, 003 |
| US-CAT-005 | REQ-CAT-017 … 020 | AC-CAT-018 … 021 | RULE-CAT-012 | ENT-CAT-001, 002, 005, 007, 009 | SCR-REQ-CAT-001 |
| US-CAT-006 | REQ-CAT-021 … 023 | AC-CAT-022 … 024 | RULE-CAT-010, 011 | ENT-CAT-001, 002, 005 | SCR-REQ-CAT-006 |
| US-CAT-007 | REQ-CAT-024 … 027, 140, 141 | AC-CAT-025 … 028, 163, 164 | RULE-CAT-001 | ENT-CAT-001, 002 | SCR-REQ-CAT-001 |
| US-CAT-008 | REQ-CAT-028 … 031 | AC-CAT-029 … 032 | RULE-CAT-001, 002 | ENT-CAT-001, 002 | SCR-REQ-CAT-001 |
| US-CAT-009 | REQ-CAT-032 … 035, 134 | AC-CAT-033 … 036, 141 | RULE-CAT-004, 005 | ENT-CAT-001, 002, 012 | SCR-REQ-CAT-001, 006 |
| US-CAT-010 | REQ-CAT-036 … 041, 139 | AC-CAT-037 … 043, 161, 166 | RULE-CAT-003, 032, 044, 045, 049 | ENT-CAT-001, 002, 009 | SCR-REQ-CAT-004 |
| US-CAT-011 | REQ-CAT-042 … 055, 129, 137, 138, 140, 141, 145 | AC-CAT-044 … 057, 131, 145, 146, 147, 154, 159, 160, 163, 164, 169 | RULE-CAT-001, 013, 014, 015, 016, 017, 048, 052 | ENT-CAT-001, 002, 003, 004, 005 | SCR-REQ-CAT-001, 003 |
| US-CAT-012 | REQ-CAT-056 … 063, 143, 147 | AC-CAT-058 … 065, 134, 151, 156 | RULE-CAT-018, 019, 020, 021, 022 | ENT-CAT-001, 002, 005, 007 | SCR-REQ-CAT-001 |
| US-CAT-013 | REQ-CAT-064 … 072, 136, 142, 146 | AC-CAT-066 … 074, 133, 142, 153, 155, 157, 158, 170 | RULE-CAT-001, 023, 024, 025, 026, 048 | ENT-CAT-001, 006 | SCR-REQ-CAT-002 |
| US-CAT-014 | REQ-CAT-073 … 076 | AC-CAT-075 … 078 | RULE-CAT-006 | ENT-CAT-001, 008, 009, 011, 012 | SCR-REQ-CAT-001 |
| US-CAT-015 | REQ-CAT-077 … 082 | AC-CAT-079 … 084 | RULE-CAT-007, 027, 028, 048 | ENT-CAT-001, 008 | SCR-REQ-CAT-001 |
| US-CAT-016 | REQ-CAT-083 … 091, 128, 135 | AC-CAT-085 … 093, 130, 135, 150, 167, 168 | RULE-CAT-029, 030, 031, 032, 047, 048, 051, 053 | ENT-CAT-001, 002, 008, 009, 011 | SCR-REQ-CAT-001 |
| US-CAT-017 | REQ-CAT-092 … 100 | AC-CAT-094 … 102, 152 | RULE-CAT-033, 034, 035 | ENT-CAT-009, 011, 012 | SCR-REQ-CAT-001, 006 |
| US-CAT-018 | REQ-CAT-101 … 104, 148 | AC-CAT-103 … 106, 136, 171 | RULE-CAT-005, 029, 031, 032, 036, 037, 053 | ENT-CAT-002, 009 | SCR-REQ-CAT-005 |
| US-CAT-019 | REQ-CAT-105 … 112 | AC-CAT-107 … 114, 148 | RULE-CAT-001, 038, 039, 040 | ENT-CAT-002, 010 | SCR-REQ-CAT-001 |
| US-CAT-020 | REQ-CAT-113 … 116 | AC-CAT-115 … 118 | RULE-CAT-041 | ENT-CAT-001, 002 | SCR-REQ-CAT-001 |
| US-CAT-021 | REQ-CAT-095, 117 … 124, 134 | AC-CAT-097, 119 … 126, 141 | RULE-CAT-042, 043 | ENT-CAT-001, 002, 012 | SCR-REQ-CAT-001, 006 |
| US-CAT-022 | REQ-CAT-125 … 127 | AC-CAT-127 … 129 | RULE-CAT-046 | ENT-CAT-001, 009 | — (the global search box sits outside CAT and calls the catalogue search — ADR-CAT-043) |

Every story maps to ≥ 1 REQ, including ≥ 1 REQ with pattern `unwanted`. Every REQ has ≥ 1 AC. Every RULE traces to ≥ 1 REQ. Every SCR-REQ traces to REQs.

### Flag × activity coverage (CM-7, CAT cells of KB §4.2)
| Flag | Activity cells (✓ required · ○ optional) | Requirement (flag on) | Requirement (flag off) |
|---|---|---|---|
| `variants` | Electronics & appliances ○ · Apparel & footwear ✓ · Household/cosmetics ○ · Furniture & lighting ✓ · Sanitary & plumbing ✓ · Fabrics & furnishings ✓ | REQ-CAT-049, REQ-CAT-017, REQ-CAT-018 | REQ-CAT-014, REQ-CAT-016, REQ-CAT-022 |
| `bundles` | Electronics & appliances ✓ · Household/cosmetics ✓ · Power tools & hardware ✓ · Furniture & lighting ✓ | REQ-CAT-056, REQ-CAT-020 | REQ-CAT-015, REQ-CAT-016, REQ-CAT-023 |
| `dualQuantity` | Grocery ○ · Fabrics ✓ (metre) · Building materials & steel ✓ · Produce by weight ✓ | catalogue side only: REQ-CAT-077 (Item units), REQ-CAT-097 (scale labels); the flag's quantity behaviour is traced by INV | — (CAT has no flag-off behaviour; the flag is read by INV) |
| `serialTracking`, `lotExpiry`, `b2bCredit` | — | no CAT requirement; traced by SWR, INV, PRC/PTY | — |

## Decisions applied

| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| D-01 | Every Item has ≥ 1 Variant (a default Variant). Stock, price, barcode and e-invoice override attach to the Variant | DP §8 P-04; registry OI-07 | Change if P-04 is decided otherwise |
| D-02 | Brand and unit of measure are mdl lookup keys CAT_BRAND / CAT_UOM owned by CAT | DP §8 P-05; PRD US-CAT-014 Source (module-registry LOOKUPS OWNED) | Make them CAT entities if attributes per brand are needed |
| D-03 | Category is a CAT tree, at most 5 levels; Items attach at any level | DP §8 P-05; US-CAT-013 | Change the depth limit |
| D-04 | Bundle composition lives in CAT (Item kind BUNDLE + component lines). No nested Bundles; a Bundle has one Variant; the kind is fixed after first save | DP §8 P-06; US-CAT-012 | Allow nesting through a later delta |
| D-05 | Barcodes: 0..n per Variant, each bound to a unit. Internal barcodes are EAN-13 with GS1 restricted-circulation prefix 04 and digits from series CAT_INTERNAL_BARCODE. Scale codes are SCALE barcodes | DP §8 P-07; module-registry OI-10 | Change the internal prefix |
| D-06 | E-invoice code type and code on the Item, with an optional override on the Variant | US-CAT-021; DP §8 P-08 | Move to Variant-only |
| D-07 | nameAr mandatory with ≥ 1 Arabic letter; nameEn optional, with nameAr used in its place | PRD P-3; DP §8 P-09 | Drop the Arabic-letter check |
| D-08 | Image files are kept in the given `file` public image storage; CAT holds order, main image, Variant binding and descriptions | DP §8 P-12; registry OI-15 | — |
| D-09 | New Item defaults: kind STANDARD, base unit PIECE, tax class STANDARD, publish state UNPUBLISHED | KB:raw-idea.md §6.1; US-CAT-007 | Change the default values |
| D-10 | Limits: 3 attributes and 300 active Variants per Item; 20 images of ≤ 5 MB; import ≤ 10 MB and ≤ 5,000 rows; search ≥ 2 characters and ≤ 20 results; 1–999 copies and ≤ 5,000 labels per request | KB:raw-idea.md §6.1, §6.10 (bounded, fast setup) | Raise any limit |
| D-11 | Number series CAT_ITEM (pattern ITM-000001), CAT_VARIANT (VAR-000001), CAT_INTERNAL_BARCODE (10 digits) | profile NUMBERING rule; US-CAT-009 | Change the patterns in the core sequence module |
| D-12 | CAT_UOM seeds PIECE, CARTON, BOX, DOZEN, KILOGRAM, GRAM, METRE, LITRE, mapped to e-invoice units EA, CT, BX, DZN, KGM, GRM, MTR, LTR | US-CAT-021 Source (Egypt ETA SDK unitType) | Re-map against the authority's published code table |
| D-13 | Two active Items cannot share one nameAr | KB:raw-idea.md §6.5 (next action: open the existing Item); US-CAT-001 | Turn into a warning |
| D-14 | Base unit and Item unit conversion factor are fixed after first save | US-CAT-015 (purchasing, selling and stock agree on quantities) | Allow change while no downstream document exists (needs a downstream check) |
| D-15 | Variant names are derived from the Item name and the value names, never entered | US-CAT-011 | Add an owner override name |
| ADR-CAT-038 | The project-registry open items affecting CAT (OI-06 … OI-12, OI-15) are applied per the approved PRD sources and DP §8 recommendations | project-registry §8; PRD | ACCEPTED (non-breaking) |
| ADR-CAT-039 | Dependent records take their parent's kind and carry the audit fields and isActiveFl | profile entity kinds | ACCEPTED (non-breaking) |
| ADR-CAT-040 | Tax class is the CAT-owned lookup CAT_TAX_CLASS; TAX maps it to rates later | KB:raw-idea.md §5 (fixed order) | ACCEPTED (non-breaking) |
| ADR-CAT-041 | Capability flags are read through SettingsApi behind FeatureFlags; CAT offers a turn-off check for the flag owner (P-02) | DP §4 #15; KB:raw-idea.md §6.2 | ACCEPTED (non-breaking) |
| ADR-CAT-042 | Scale label layout and Unit e-invoice code are CAT config entities | module-registry OI-10; US-CAT-021 | ACCEPTED (non-breaking) |
| ADR-CAT-043 | CAT provides the item search; the global search box sits outside CAT | KB:raw-idea.md §6.6 | ACCEPTED (non-breaking) |
| ADR-CAT-058 | The given copies the CAT lookup values and number series into each new tenant; `CatTenantProvisioningContributor` copies the 8 Unit e-invoice codes (REQ-CAT-134, A9) | given erp-core `modules.tenant` / `mdl` / `sequence` | ACCEPTED (non-breaking) |
| ADR-CAT-059 | Every transition into ACTIVE re-establishes the Item invariants (A7, REQ-CAT-130, RULE-CAT-050); channel reads see only active Items (REQ-CAT-086, REQ-CAT-101) | gate-analysis G6 | ACCEPTED (non-breaking) |
| ADR-CAT-060 | An Item unit used by active barcodes stays active (REQ-CAT-128, RULE-CAT-051) | gate-analysis G7 | ACCEPTED (non-breaking) |
| ADR-CAT-062 | Arabic normalisation for RULE-CAT-003 and for search | gate-analysis G18 | ACCEPTED (non-breaking) |
| ADR-CAT-063 | An inactive parent refuses child writes (REQ-CAT-135, RULE-CAT-053); deactivating a Variant also deactivates its barcodes (REQ-CAT-050) | gate-analysis round 2, G2 | ACCEPTED (non-breaking) |
| ADR-CAT-064 | The scale code length of a layout is fixed after creation; type and decimals change only how the embedded value is read; a deactivated layout leaves its SCALE barcodes stored (REQ-CAT-093, REQ-CAT-094) | gate-analysis round 2, G3 | ACCEPTED (non-breaking) |
| ADR-CAT-065 | CAT serves its own lookup keys to the screens through `OwnedLookups` (`GET /api/v1/cat/lookups/{key}`); amends ADR-CAT-050 §1 and §2 | gate-analysis round 2, G5; erp-core checklist C6 | ACCEPTED (non-breaking) |
| ADR-CAT-068 | Three repair operations stay allowed on an inactive Item — Variant generation, a categoryFk-only update and the replacement of the Bundle components (REQ-CAT-135, REQ-CAT-145 … REQ-CAT-147, RULE-CAT-053, A7); amends ADR-CAT-063 | gate-analysis round 3, G1 | ACCEPTED (non-breaking) |
| ADR-CAT-069 | Categories, Variant attributes and Variant attribute values can be reactivated (REQ-CAT-136 … REQ-CAT-138) | gate-analysis round 3, G10 | ACCEPTED (non-breaking) |
| ADR-CAT-070 | Variant attributes and their values are CAT tables, not mdl lookup types (erp-core checklist C5 / F3); CORE-GAP-tenant-lookup-types raised | gate-analysis round 3, G11 | ACCEPTED (non-breaking) |
| ADR-CAT-072 | The default Variant moves: on deactivation of the default to the remaining active Variant with the lowest code, and on generation for an Item with no active Variant to the first Variant created; an active Item always has exactly one active default Variant (REQ-CAT-025, REQ-CAT-140, REQ-CAT-141, A7, CON-CAT-002) | gate-analysis round 6, G3 | ACCEPTED (non-breaking) |
| ADR-CAT-074 | The item import saves entities (no JDBC batching); the 60-second figure for 5,000 rows is a target measured on the reference deployment, the 120-second limit is the guarantee (REQ-CAT-139); amends ADR-CAT-071 | gate-analysis round 7, G3 | ACCEPTED (non-breaking) |
| ADR-CAT-076 | Image type, size and public storage are enforced at upload by the FILE category CAT_ITEM_IMAGE; CAT checks a file reference's availability and public URL and the 20-image count (RULE-CAT-038, REQ-CAT-110); the import's byte read and type / size check (RULE-CAT-044) wait on CORE-GAP-file-document-metadata-read; amends ADR-CAT-056 §2 | gate-analysis round 8, G1; erp-core conventions §Files rule 3, modules.file | ACCEPTED (non-breaking) |
| ADR-CAT-077 | Category changes publish catalogue-change events: ENT-CAT-006 joins REQ-CAT-008, RULE-CAT-049 and CON-CAT-018 (itemPk empty for a Category), so a Category creation, change, deactivation or reactivation is signalled to SFR and INS (RULE-CAT-048) | gate-analysis round 9, G1 | ACCEPTED (non-breaking) |
| ADR-CAT-078 | The barcode label screen generates the missing internal barcode of a line itself, under the label page's CREATE permission and the rules and locks of the Items screen's internal-barcode operation, so the next action of RULE-CAT-037 is reachable by warehouse staff (REQ-CAT-148, SCR-REQ-CAT-005) | gate-analysis round 10, G1; KB:raw-idea.md §6.5 | ACCEPTED (non-breaking) |

No ADR is BLOCKED. No question was raised.

## Access summary

| Page code | Screen | Owner | Seller/cashier | Warehouse staff |
|---|---|---|---|---|
| CAT_ITEMS | SCR-REQ-CAT-001 Items / الأصناف | VIEW, CREATE, UPDATE, DELETE | VIEW | VIEW |
| CAT_CATEGORIES | SCR-REQ-CAT-002 Categories / الفئات | VIEW, CREATE, UPDATE, DELETE | VIEW | VIEW |
| CAT_VARIANT_ATTRIBUTES | SCR-REQ-CAT-003 Variant attributes / خصائص المتغيّرات | VIEW, CREATE, UPDATE, DELETE | VIEW | VIEW |
| CAT_ITEM_IMPORT | SCR-REQ-CAT-004 Item import / استيراد الأصناف | VIEW, CREATE | — | — |
| CAT_BARCODE_LABELS | SCR-REQ-CAT-005 Barcode labels / ملصقات الباركود | VIEW, CREATE | — | VIEW, CREATE |
| CAT_SETTINGS | SCR-REQ-CAT-006 Catalogue settings / إعدادات الكتالوج | VIEW, CREATE, UPDATE, DELETE | — | — |

Custom roles receive actions through the given `sec` module. Permission names are derived from these page codes by the security module, and none is seeded here.
══════════════════════════════════════════════════════════════════
