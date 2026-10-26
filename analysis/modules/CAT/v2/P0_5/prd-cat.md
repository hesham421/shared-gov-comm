# PRD — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module          : CAT     Version : v2
Source artifacts: platform-summary, module-registry (module-registry-cat.md), business-policies (business-policies-cat.md)
Stories         : 22   Policies covered : 9/9   Deferred : 0
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

Boundary (from the module registry): what is sold. Items, variants, variant attributes, categories, bundles, barcodes, media and publish state. CAT holds no price and no stock quantity [DP §7.2]. Roles are the preset roles of KB §3 D8: owner, seller/cashier, warehouse staff. The storefront shopper is the customer role of KB §3 D5.

Note for the approver: US-CAT-009, US-CAT-015 and US-CAT-018 rest on module-registry AUTO-decisions (automatic item codes, item unit conversion, barcode labels through `report`), which the user has not confirmed before. If you reject one, strike the story at this gate. Do not edit it.

## USER STORIES

US-CAT-001
  Title          : en: One catalogue for every channel · ar: كتالوج واحد لكل القنوات
  Story          : As an owner, I need to define an item once and have the back-office, the point of sale and the storefront all use it, so that I never maintain the same item in three places.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-001
  Source         : KB:raw-idea.md §1 "one catalogue, one stock, one customer base"; §2 (differentiator: one product for back-office + POS + storefront)
  Status         : DRAFT

US-CAT-002
  Title          : en: Catalogue changes reach every channel · ar: وصول تغييرات الكتالوج إلى كل القنوات
  Story          : As a seller/cashier, I need a change the owner makes to an item to reach the point of sale and the storefront, so that every channel sells the same, current item.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-001
  Source         : KB:raw-idea.md §1; §5 rules (dependencies flow downward); module-registry AUTO "CAT publishes catalogue-change events; it never reads a downstream module"
  Status         : DRAFT

US-CAT-003
  Title          : en: Catalogue follows capability flags, not the activity · ar: كتالوج يتبع مفاتيح القدرة لا النشاط
  Story          : As an owner, I need the catalogue to behave according to the capabilities my activity preset switched on, and as I later adjust them, so that my business is served by what I actually use rather than by the label of my activity.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-002
  Source         : KB:raw-idea.md §3 D2 "the activity is a preset that sets flags"; §6.2 "presets then customise"
  Status         : DRAFT

US-CAT-004
  Title          : en: Only enabled catalogue functions are shown · ar: عرض وظائف الكتالوج المُفعَّلة فقط
  Story          : As a seller/cashier or warehouse staff member, I need to see only the catalogue functions my business has switched on, without variant or bundle functions it does not use, so that the screens stay simple and I can start working quickly.
  Priority       : HIGH
  Success metric : a new employee sells after ≤ 10 minutes (KB §6.10)
  Traces         : POL-CAT-003
  Source         : KB:raw-idea.md §4.1 "the UI shows fields/menus/reports only when the flag is on"; §6.3 "no dead menus"
  Status         : DRAFT

US-CAT-005
  Title          : en: Turn a catalogue capability on at any time · ar: تشغيل قدرة للكتالوج في أي وقت
  Story          : As an owner, I need to switch variants or bundles on whenever my business grows into them and use them with the items I already have, so that I never redo my setup or move my data.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-004
  Source         : KB:raw-idea.md §6.2 "flags may be turned on at any time"; §6.8 "No data migration, no mode switch"; module-registry OI-07
  Status         : DRAFT

US-CAT-006
  Title          : en: Know why a capability cannot be turned off, and what to do next · ar: معرفة سبب تعذّر إيقاف قدرة والإجراء التالي
  Story          : As an owner, I need to be told when variants or bundles cannot be switched off because my catalogue still depends on them, and what I can do about it, so that my data stays intact and I am never left at a dead end.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-005
  Source         : KB:raw-idea.md §6.2 "turning off is blocked when data depends on it"; §6.5 "every error offers a next action"
  Status         : DRAFT

US-CAT-007
  Title          : en: Create an item with minimal entries · ar: إنشاء صنف بأقل المدخلات
  Story          : As an owner, I need to add a new item by filling in only a handful of essentials, with everything else optional and available under "Advanced", so that adding items does not slow down setting up my shop.
  Priority       : HIGH
  Success metric : ≤ 4 mandatory entries per item (KB §6.1); setup and first invoice in ≤ 30 minutes without training (KB §6.10)
  Traces         : POL-CAT-006
  Source         : KB:raw-idea.md §3 D9; §6.1 "≤ 4 mandatory fields per screen"; §6.10
  Status         : DRAFT

US-CAT-008
  Title          : en: Item name in Arabic and English · ar: اسم الصنف بالعربية والإنجليزية
  Story          : As an owner, I need to name an item in Arabic, keeping brand or model text as written, and to add an English name when I want one, so that receipts, invoices and the storefront show the item in the language my customers read.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-006, POL-CAT-001
  Source         : module-registry OI-12 / business-policies OI-12 ("applied by P0.5 as a standard language rule"); profile language policy `ar, en`; KSA VAT Implementing Regulations Art. 53
  Status         : DRAFT

US-CAT-009
  Title          : en: Automatic item codes · ar: ترقيم الأصناف تلقائيًا
  Story          : As an owner, I need each new item to receive its code automatically, so that the code is never one of the entries I must think up.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-006
  Source         : module-registry AUTO "Item codes are numbered automatically by given `sequence`, so a code is never a mandatory entry" (registry AUTO, not user-confirmed); KB:raw-idea.md §6.1
  Status         : DRAFT

US-CAT-010
  Title          : en: Import items from Excel in the setup wizard · ar: استيراد الأصناف من Excel في معالج الإعداد
  Story          : As an owner, I need to bring my existing item list in from an Excel file at the item-import step of the setup wizard, and to know what to fix for anything that could not come in, so that I start selling without re-typing my catalogue.
  Priority       : HIGH
  Success metric : setup (including item import) and first invoice in ≤ 30 minutes without training (KB §6.10)
  Traces         : POL-CAT-007
  Source         : KB:raw-idea.md §6.9 "Setup wizard (5 steps): … import items from Excel"; §6.5 "no dead ends"; §6.10
  Status         : DRAFT

US-CAT-011
  Title          : en: Item variants (size, colour, …) · ar: متغيّرات الصنف (المقاس، اللون، …)
  Story          : As an owner whose business uses variants, I need to describe an item by its attributes (such as size and colour) and have each combination available as its own variant, so that sellers, warehouse staff and storefront shoppers pick, count and buy the exact variant.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-002, POL-CAT-003, POL-CAT-004
  Source         : KB:raw-idea.md §4.1 `variants` "catalogue, cart, stock at variant level"; §4.2 (apparel: variants required); module-registry OI-06, OI-07
  Status         : DRAFT

US-CAT-012
  Title          : en: Bundles · ar: الحزم
  Story          : As an owner whose business uses bundles, I need to put together a bundle (a kit or promo pack) from items I already sell and sell it as one line, so that I can offer packs without keeping separate stock for them.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-002, POL-CAT-003, POL-CAT-004
  Source         : KB:raw-idea.md §4.1 `bundles` "composite items / kits / promo packs"; §4.2 (electronics: bundles required); module-registry OI-09
  Status         : DRAFT

US-CAT-013
  Title          : en: Item categories · ar: فئات الأصناف
  Story          : As an owner, I need to organise my items into categories and sub-categories, so that storefront shoppers can browse them and my reports can group them.
  Priority       : —
  Success metric : —
  Traces         : POL-CAT-001
  Source         : KB:raw-idea.md §5 #1 "Catalog — item, variants, media, categories, publish state"; module-registry OI-08
  Status         : DRAFT

US-CAT-014
  Title          : en: Brands and units of measure · ar: العلامات التجارية ووحدات القياس
  Story          : As an owner, I need to keep my own list of brands and units of measure and use them on my items, so that every channel describes items the same way.
  Priority       : —
  Success metric : —
  Traces         : POL-CAT-001
  Source         : module-registry LOOKUPS OWNED (brand, unit-of-measure); OI-08; DP §7.1
  Status         : DRAFT

US-CAT-015
  Title          : en: Units an item is bought and sold in · ar: وحدات شراء وبيع الصنف
  Story          : As an owner trading wholesale, I need to say that an item is bought or sold by the carton as well as by the piece, and how many pieces a carton holds, so that purchasing, selling and stock all agree on quantities.
  Priority       : —
  Success metric : —
  Traces         : POL-CAT-001
  Source         : module-registry AUTO "Item unit conversion owned by CAT" (registry AUTO, not user-confirmed); KB:raw-idea.md §1 "wholesale traders"
  Status         : DRAFT

US-CAT-016
  Title          : en: Item barcodes · ar: باركود الأصناف
  Story          : As a seller/cashier, I need to find the exact item or variant by scanning any barcode it carries (the manufacturer's or our own), so that selling and receiving are fast and accurate.
  Priority       : —
  Success metric : —
  Traces         : POL-CAT-001
  Source         : module-registry OI-10 "A Variant carries 0..n barcodes (GTIN … or internal)"
  Status         : DRAFT

US-CAT-017
  Title          : en: Weighed items from the scale label · ar: الأصناف الموزونة من ملصق الميزان
  Story          : As a seller/cashier in a shop that sells by weight, I need the point of sale to recognise the item and its weight or price from the label my scale prints, and as an owner I need to tell the system how my scale labels are laid out, so that weighed goods are sold without re-keying.
  Priority       : LOW
  Success metric : —
  Traces         : POL-CAT-001
  Source         : module-registry OI-10 (scale code; scale-barcode pattern as a tenant setting; GS1 restricted-circulation prefixes; no new flag); KB:raw-idea.md §4.2 "Produce / meat by weight" (outside the stated launch priority)
  Status         : DRAFT

US-CAT-018
  Title          : en: Barcode label printing · ar: طباعة ملصقات الباركود
  Story          : As a warehouse staff member, I need to print barcode labels for items and variants, so that goods without a manufacturer barcode can still be scanned at the point of sale.
  Priority       : —
  Success metric : —
  Traces         : POL-CAT-001
  Source         : module-registry AUTO "Barcode labels print through given `report` (PrintApi, Jasper)" (registry AUTO, not user-confirmed); registry DEPENDENCIES (erp-core:modules.report, barcode labels)
  Status         : DRAFT

US-CAT-019
  Title          : en: Item images and media · ar: صور ووسائط الصنف
  Story          : As an owner, I need to add images to an item, choose its main image and their order, show the right image for each variant, and describe each image in Arabic and English, so that shoppers see the item as it really is.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-CAT-001
  Source         : KB:raw-idea.md §5 #1 (media; files public); module-registry OI-15
  Status         : DRAFT

US-CAT-020
  Title          : en: Publish an item to the storefront · ar: نشر الصنف في المتجر الإلكتروني
  Story          : As an owner, I need to decide which items are offered online and to take an item off the storefront without removing it from my shop, so that I control what I sell online.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-CAT-008
  Source         : KB:raw-idea.md §5 #1 "publish state"; business-policies POL-CAT-008 rationale
  Status         : DRAFT

US-CAT-021
  Title          : en: Item tax and e-invoice data · ar: بيانات الضريبة والفاتورة الإلكترونية للصنف
  Story          : As an owner, I need to record each item's tax class and its e-invoice item code (once for the item, and differently for a variant when needed), and to have my units carry their e-invoice unit, so that invoices issued from any channel are accepted by the tax authority.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-001
  Source         : KB:raw-idea.md §2 "mandatory e-invoicing … forces every small trader to adopt compliant software"; module-registry OI-11; Egypt ETA e-invoice SDK (itemType, unitType)
  Status         : DRAFT

US-CAT-022
  Title          : en: Find items through the global search · ar: العثور على الأصناف من البحث الشامل
  Story          : As a seller/cashier, I need to find any item from the one global search, the same search I use for customers and documents, so that I reach it from wherever I am.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CAT-009
  Source         : KB:raw-idea.md §6.6 "One global search (items, customers, documents)"
  Status         : DRAFT

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-CAT-001 | POL-CAT-001 | KB §1, §2 |
| US-CAT-002 | POL-CAT-001 | KB §1, §5; registry AUTO (events) |
| US-CAT-003 | POL-CAT-002 | KB §3 D2, §6.2 |
| US-CAT-004 | POL-CAT-003 | KB §4.1, §6.3, §6.10 |
| US-CAT-005 | POL-CAT-004 | KB §6.2, §6.8; OI-07 |
| US-CAT-006 | POL-CAT-005 | KB §6.2, §6.5 |
| US-CAT-007 | POL-CAT-006 | KB §3 D9, §6.1, §6.10 |
| US-CAT-008 | POL-CAT-006, POL-CAT-001 | OI-12; KSA VAT IR Art. 53 |
| US-CAT-009 | POL-CAT-006 | registry AUTO (sequence); KB §6.1 |
| US-CAT-010 | POL-CAT-007 | KB §6.9, §6.5, §6.10 |
| US-CAT-011 | POL-CAT-002, POL-CAT-003, POL-CAT-004 | KB §4.1, §4.2; OI-06, OI-07 |
| US-CAT-012 | POL-CAT-002, POL-CAT-003, POL-CAT-004 | KB §4.1, §4.2; OI-09 |
| US-CAT-013 | POL-CAT-001 | KB §5 #1; OI-08 |
| US-CAT-014 | POL-CAT-001 | OI-08; DP §7.1 |
| US-CAT-015 | POL-CAT-001 | registry AUTO (unit conversion); KB §1 |
| US-CAT-016 | POL-CAT-001 | OI-10 |
| US-CAT-017 | POL-CAT-001 | OI-10; KB §4.2 |
| US-CAT-018 | POL-CAT-001 | registry AUTO (report) |
| US-CAT-019 | POL-CAT-001 | KB §5 #1; OI-15 |
| US-CAT-020 | POL-CAT-008 | KB §5 #1 |
| US-CAT-021 | POL-CAT-001 | KB §2; OI-11 |
| US-CAT-022 | POL-CAT-009 | KB §6.6 |

Policy coverage: POL-CAT-001 (001, 002, 008, 013–019, 021) · POL-CAT-002 (003, 011, 012) · POL-CAT-003 (004, 011, 012) · POL-CAT-004 (005, 011, 012) · POL-CAT-005 (006) · POL-CAT-006 (007, 008, 009) · POL-CAT-007 (010) · POL-CAT-008 (020) · POL-CAT-009 (022). 9/9.

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| P-1 | Trace for catalogue-content stories with no dedicated policy (US-CAT-002, -013…-016, -018, -021) | Trace to POL-CAT-001. Each entity is content of the one catalogue, and each story names the channel it serves. Stories resting on registry AUTO-decisions (US-CAT-009, -015, -018) say so in their Source and are flagged to the approver | Converged in dialogue (round 2); user confirms at prd-approval | engine §3 (entity seed); KB §1; registry OI-08, OI-10, OI-11, OI-15 |
| P-2 | Basis for story priority | HIGH where KB states or clearly implies it: §6 simplicity (D9), D2, §1/§2 differentiator and e-invoicing, launch presets needing variants and bundles (§4.2). MEDIUM for storefront-facing stories (storefront live in wave 2, §8.2). LOW for weighed items (outside launch priority). "—" otherwise. All stories still ship with CAT in wave 1 (D10) | Converged in dialogue (round 2); user confirms at prd-approval | KB §1, §2, §3 D2, D9, D10, §4.2, §6, §8 |
| P-3 | Item-name language rule (OI-12) | NEED story US-CAT-008. The rule "Arabic name mandatory, English name optional; the Arabic name may carry Latin brand or model text" is handed to P1 as a standard language rule, not a POL | Converged in dialogue (round 2); user confirms at prd-approval | registry OI-12; profile `ar, en`; KSA VAT IR Art. 53; engine §8 |
| P-4 | Standard values of lookup `einvoice-item-code-type` | GS1, EGS (mdl lookup values; no ID) | Converged in dialogue (round 2); user confirms at prd-approval | registry LOOKUPS OWNED (values applied by P0.5 from the ETA source); Egypt ETA e-invoice SDK `itemType`; ZATCA UBL (GTIN as an optional standard item identifier, no type list) |
| P-5 | Catalogue change history and catalogue permissions | No story. Permissions are a P1 permission matrix; change history is given `audit` behaviour with no CAT policy behind it | Converged in dialogue (round 2); user confirms at prd-approval | engine §8; registry DEPENDENCIES (audit, sec) |
| P-6 | Scope exceptions as DEFERRED stories | None. They are decided exclusions (business-policies SCOPE EXCEPTIONS), not undecided items | Converged in dialogue (round 2); user confirms at prd-approval | engine §4; business-policies SCOPE EXCEPTIONS |

## DEFERRED
| US | Reason | Activation trigger |
|---|---|---|
| — | No story is deferred. Excluded scope (pre-assembled kits, activity-specific catalogue models, multi-vendor catalogue) is recorded in business-policies-cat SCOPE EXCEPTIONS with its activation triggers | — |

## APPROVAL
Approved by : —   Date : —
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════
