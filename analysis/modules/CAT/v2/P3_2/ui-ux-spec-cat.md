# UI/UX spec — Catalog / الكتالوج (CAT) — v2

Module : CAT · Version : v2 · Stage : P3.2 Part A · Profile : commerce
Inputs : srs (SCR-REQ-CAT-001 … 006, ENT/RULE/permission matrix), prd (US-CAT-001 … 022)
Mints  : SCR-CAT-001 … SCR-CAT-006 · UXD-CAT-001 … UXD-CAT-003 (both streams start at 001; the current state holds no earlier frontend design)
Decisions: ADR-CAT-049 (Items: one Save, ordered calls) · ADR-CAT-050 (display data served by the given) · ADR-CAT-051 (API coverage, binding notes) · gate-analysis round 2: ADR-CAT-063 (inactive parent refuses child writes) · ADR-CAT-064 (layout keeps its scale code length) · ADR-CAT-065 (CAT lookup keys served through API-CAT-052) · gate-analysis round 3: ADR-CAT-068 (three repairs stay allowed on an inactive Item) · ADR-CAT-069 (reactivation of Categories, Variant attributes and values)

This is a delta run, but the current state holds no earlier ui-ux-spec, so every block below is ADDED. Screens follow the composite-screen rule: Search + Entry and Master + Detail are ONE screen with ONE `SCR-*`, and the sub-views sit under it. Fields and permissions are copied from the SRS, and labels are given in ar and en. The spec states intent. Components, hooks and routes in code belong to the frontend execution plan.

## Conventions shared by every screen

- **Direction and language**: RTL in ar, LTR in en. Every label, button, notice and message is given in both. Server messages are shown from the envelope's `messageAr` / `messageEn`, never composed (ADR-CAT-048).
- **Progressive disclosure**: a record surface shows its mandatory fields first; the rest sit under "Advanced / متقدم", collapsed by default (REQ-CAT-026).
- **Read-only access**: a user with VIEW only sees the same surface with every input read-only and every action hidden.
- **Capability flags** (UXD-CAT-002): a flag-bound field, pane, filter or menu entry is absent, not disabled, while its flag is off (REQ-CAT-014, REQ-CAT-015).
- **Lookup fields** (UXD-CAT-001): options are the active values of the field's lookup key, labelled per language. A deactivated value already on a record is still shown, with its label, on that record (REQ-CAT-076).
- **Jumps to other pages**: a button or link that opens another CAT page is shown only when the user holds that page's VIEW permission and, for a flag-bound page, only while its flag is on. Examples on Items: "Print labels" needs PERM_CAT_BARCODE_LABELS_VIEW, "Import" needs PERM_CAT_ITEM_IMPORT_VIEW, and "Open Variant attributes" needs PERM_CAT_VARIANT_ATTRIBUTES_VIEW with `variants` on.
- **Deactivate**: a record is never deleted. "Deactivate / إيقاف" asks for confirmation and then shows the server's RULE-CAT-048 notice.
- **States** (every screen): empty (the next action is offered) · loading (skeleton in the area being read) · error (generic localized message from the envelope, with "Try again / أعد المحاولة") · offline: the SRS sets no offline requirement for CAT, so only the platform's connection indicator is shown.

Shared UX copy (not catalogue messages):

| Key | ar | en |
|---|---|---|
| advanced | متقدم | Advanced |
| save | حفظ | Save |
| cancel | إلغاء | Cancel |
| confirm | تأكيد | Confirm |
| deactivate | إيقاف | Deactivate |
| reactivate | إعادة تفعيل | Reactivate |
| try-again | أعد المحاولة | Try again |
| unsaved-leave | لديك تغييرات غير محفوظة. هل تريد المغادرة دون حفظ؟ | You have unsaved changes. Leave without saving? |
| stale-version | عدّل مستخدم آخر هذا السجل. أعد تحميله ثم أعد تطبيق تغييراتك. | Someone else changed this record. Reload it, then apply your changes again. |
| pending-badge | سيُحفظ عند الحفظ | Saved on Save |
| partial-save | حُفظ جزء من التغييرات. السطر المعلَّم لم يُحفظ؛ صحّحه ثم اضغط حفظ لإكمال الباقي. | Part of the changes was saved. The marked line was not saved; correct it, then press Save to finish the rest. |
| save-first | احفظ تغييراتك أولًا. | Save your changes first. |
| flag-unreadable | (RULE-CAT-009 message, SRS) | (RULE-CAT-009 message, SRS) |

## SCR-CAT-001 — Items / الأصناف                 traces=REQ-CAT-135,AC-CAT-144,AC-CAT-154,AC-CAT-155,AC-CAT-156,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,REQ-CAT-004,REQ-CAT-005,REQ-CAT-006,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-020,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-054,REQ-CAT-055,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-061,REQ-CAT-062,REQ-CAT-063,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-082,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-086,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-090,REQ-CAT-091,REQ-CAT-096,REQ-CAT-097,REQ-CAT-099,REQ-CAT-100,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,REQ-CAT-128,REQ-CAT-129,REQ-CAT-130,REQ-CAT-131,REQ-CAT-132,REQ-CAT-133,AC-CAT-001,AC-CAT-002,AC-CAT-003,AC-CAT-004,AC-CAT-005,AC-CAT-006,AC-CAT-007,AC-CAT-008,AC-CAT-009,AC-CAT-010,AC-CAT-011,AC-CAT-012,AC-CAT-013,AC-CAT-014,AC-CAT-015,AC-CAT-016,AC-CAT-017,AC-CAT-018,AC-CAT-019,AC-CAT-020,AC-CAT-021,AC-CAT-025,AC-CAT-026,AC-CAT-027,AC-CAT-028,AC-CAT-029,AC-CAT-030,AC-CAT-031,AC-CAT-032,AC-CAT-033,AC-CAT-034,AC-CAT-035,AC-CAT-036,AC-CAT-051,AC-CAT-052,AC-CAT-053,AC-CAT-054,AC-CAT-055,AC-CAT-056,AC-CAT-057,AC-CAT-058,AC-CAT-059,AC-CAT-060,AC-CAT-061,AC-CAT-062,AC-CAT-063,AC-CAT-064,AC-CAT-065,AC-CAT-074,AC-CAT-075,AC-CAT-076,AC-CAT-077,AC-CAT-078,AC-CAT-079,AC-CAT-080,AC-CAT-081,AC-CAT-082,AC-CAT-083,AC-CAT-084,AC-CAT-085,AC-CAT-086,AC-CAT-087,AC-CAT-088,AC-CAT-089,AC-CAT-090,AC-CAT-091,AC-CAT-092,AC-CAT-093,AC-CAT-098,AC-CAT-099,AC-CAT-101,AC-CAT-102,AC-CAT-107,AC-CAT-108,AC-CAT-109,AC-CAT-110,AC-CAT-111,AC-CAT-112,AC-CAT-113,AC-CAT-114,AC-CAT-115,AC-CAT-116,AC-CAT-117,AC-CAT-118,AC-CAT-119,AC-CAT-120,AC-CAT-121,AC-CAT-127,AC-CAT-128,AC-CAT-129,AC-CAT-130,AC-CAT-131,AC-CAT-132,AC-CAT-133,AC-CAT-134,AC-CAT-135,AC-CAT-137,AC-CAT-138,AC-CAT-139,AC-CAT-140,AC-CAT-142,UXD-CAT-001,UXD-CAT-002,UXD-CAT-003

Source            : SCR-REQ-CAT-001 · page code CAT_ITEMS · owning ENT-CAT-001 (with ENT-CAT-002, 005, 007, 008, 009, 010; ENT-CAT-006 by reference)
UI pattern        : Search + Entry (composite). Content shape: header + repeating lines (Variants, Item units, barcodes, Bundle components, images)
Sub-views         : Search · Entry — under this ONE SCR. The Entry has two second-level views: the Variant generation picker and the Bundle component picker
Fields shown      : see the tables below. Every SRS field the screen renders is listed with its ar/en label. Identifiers (`itemPk`, `variantPk`, …) are not rendered; they are carried on the Navigation line
Composition       : container page · header → inline · Variants → inline · Item units → inline · barcodes → inline · Bundle components → inline · images → inline · Variant generation picker → summary row + second level · Bundle component picker → summary row + second level · submits: one
Permissions       : PERM_CAT_ITEMS_VIEW (gateway) · PERM_CAT_ITEMS_CREATE · PERM_CAT_ITEMS_UPDATE · PERM_CAT_ITEMS_DELETE — Owner: VIEW, CREATE, UPDATE, DELETE · Seller/cashier: VIEW · Warehouse staff: VIEW (SRS B4)
Cross-module data : brandCode, baseUomCode, taxClassCode, itemKindCode, publishStateCode, einvoiceCodeTypeCode, Item unit uomCode, barcode barcodeTypeCode / uomCode → UXD-CAT-001 (erp-core mdl) · Variants pane, attribute fields, kind field/filter, Bundle components pane → UXD-CAT-002 (erp-core cu, FeatureFlags) · image thumbnail of fileDocumentRef → UXD-CAT-003 (erp-core file)
Navigation        : `/cat/items` (Search; filters in the URL) · `/cat/items/new` · `/cat/items/:itemPk` (Entry; `?variant=<variantPk>` highlights a Variant) · `?drawer=<line-kind>:<id|new>` opens a one-line drawer · `?picker=variant-generation|bundle-components` opens a second level · from: home, Catalog menu, global search hit (`/cat/items/:itemPk`), Categories (`/cat/items?categoryFk=<categoryPk>` or `/cat/items/:itemPk`), Barcode labels and Catalogue settings (`/cat/items/:itemPk`) · to: Variant attributes (`/cat/variant-attributes?attribute=new`), Barcode labels (`/cat/barcode-labels?variantPk=<variantPk>`), Item import (`/cat/item-import`)
States            : empty — Search with no Item: "No items yet" + "New item" and "Import" · Entry Variants pane with only the default Variant: "Generate variants" · loading · error (generic) · offline (not required by the SRS)

### Search sub-view

| Filter | Source | Label-ar | Label-en | Note |
|---|---|---|---|---|
| text | nameAr / nameEn / code / barcodeValue | بحث | Search | free text |
| categoryFk | ENT-CAT-006 tree (active) | الفئة | Category | hidden when the Category tree cannot be read (ADR-CAT-051) |
| brandCode | CAT_BRAND (UXD-CAT-001) | العلامة التجارية | Brand | |
| itemKindCode | CAT_ITEM_KIND (UXD-CAT-001) | نوع الصنف | Item kind | only while `bundles` is on (REQ-CAT-015) |
| publishStateCode | CAT_PUBLISH_STATE (UXD-CAT-001) | حالة النشر | Publish state | |
| isActiveFl | flag | نشط | Active | default: active |
| Find by barcode | barcode or 13-digit scale label (REQ-CAT-086, REQ-CAT-097) | بحث بالباركود | Find by barcode | an action field, not an entity field; a hit opens the Entry |

Result columns (sorted by nameAr, REQ-CAT-003): code الرمز / Code · nameAr الاسم بالعربية / Arabic name · nameEn الاسم بالإنجليزية / English name (the English display falls back to nameAr — REQ-CAT-029) · Category الفئة / Category (nameAr of the node, active or inactive — an inactive Item may point to a Category deactivated after it) · brand العلامة التجارية / Brand · Item kind نوع الصنف / Item kind (while `bundles` is on) · publish state حالة النشر / Publish state · active نشط / Active. Paging: page size 20, changeable.

### Entry sub-view — header (ENT-CAT-001)

| Field | Label-ar | Label-en | Placement | Read-only |
|---|---|---|---|---|
| nameAr | الاسم بالعربية | Arabic name | mandatory | no |
| baseUomCode | وحدة القياس الأساسية | Base unit of measure | mandatory, pre-filled PIECE | after first save (RULE-CAT-007) |
| taxClassCode | التصنيف الضريبي | Tax class | mandatory, pre-filled STANDARD | no |
| itemKindCode | نوع الصنف | Item kind | mandatory, pre-filled STANDARD; only while `bundles` is on | after first save (RULE-CAT-022) |
| code | الرمز | Code | header badge | always (assigned on first save, RULE-CAT-004) |
| publishStateCode | حالة النشر | Publish state | header badge | always (changed by Publish / Unpublish) |
| isActiveFl | نشط | Active | header badge | always (changed by Deactivate / Reactivate) |
| nameEn | الاسم بالإنجليزية | English name | Advanced | no |
| categoryFk | الفئة | Category | Advanced (tree select, active Categories) | no |
| brandCode | العلامة التجارية | Brand | Advanced | no |
| descriptionAr | الوصف بالعربية | Arabic description | Advanced | no |
| descriptionEn | الوصف بالإنجليزية | English description | Advanced | no |
| einvoiceCodeTypeCode | نوع رمز الفاتورة الإلكترونية | E-invoice code type | Advanced | no |
| einvoiceCode | رمز الفاتورة الإلكترونية | E-invoice code | Advanced | no |

Exactly 4 mandatory fields sit outside Advanced while `bundles` is on (3 while it is off), and all but nameAr are pre-filled (REQ-CAT-026, AC-CAT-027).

### Entry sub-view — Variants pane (ENT-CAT-002, ENT-CAT-005; only while `variants` is on)

| Field | Label-ar | Label-en | Read-only |
|---|---|---|---|
| code | الرمز | Code | always |
| nameAr | الاسم بالعربية | Arabic name | always (derived, D-15) |
| nameEn | الاسم بالإنجليزية | English name | always (derived) |
| isDefaultFl | المتغيّر الأساسي | Default variant | always |
| attribute values (ENT-CAT-005 variantAttributeFk → variantAttributeValueFk) | الخاصية / القيمة | Attribute / Value | always (set by generation) |
| einvoiceCodeTypeCode | نوع رمز الفاتورة الإلكترونية | E-invoice code type | edited in the override drawer |
| einvoiceCode | رمز الفاتورة الإلكترونية | E-invoice code | edited in the override drawer |
| isActiveFl | نشط | Active | changed by the line's "Deactivate" |

Line actions: "E-invoice override / تجاوز رمز الفاتورة" (one-line drawer) · "Deactivate / إيقاف" (marks the line pending) · expand → the Variant's images (REQ-CAT-109). Pane action: "Generate variants / توليد المتغيّرات" opens the Variant generation picker. It is absent for a BUNDLE Item (RULE-CAT-020). The picker's summary row in the pane shows the pending selection ("SIZE: M, L · COLOUR: أحمر, أزرق — Edit").

**Variant generation picker** (second level): it lists the active attributes (ENT-CAT-003 code / nameAr — الرمز / Code, الاسم بالعربية / Arabic name) with their active values (ENT-CAT-004 code / nameAr), ordered by sortOrder. Up to 3 attributes, multi-select of values per attribute. Confirm returns the selection to the form; Cancel discards the draft. The picker saves nothing. When no active attribute exists, it shows "Open Variant attributes / افتح خصائص المتغيّرات".

### Entry sub-view — Item units pane (ENT-CAT-008)

| Field | Label-ar | Label-en | Read-only |
|---|---|---|---|
| uomCode | وحدة القياس | Unit of measure | after saving |
| conversionFactor | معامل التحويل | Conversion factor | after saving (RULE-CAT-028) |
| isPurchaseFl | للشراء | Used for purchasing | no |
| isSalesFl | للبيع | Used for selling | no |
| isActiveFl | نشط | Active | changed by "Deactivate" |

Pane action: "Add unit / إضافة وحدة" (one-line drawer). Line actions: change the flags (inline checkboxes) · Deactivate.

### Entry sub-view — Barcodes pane (ENT-CAT-009, grouped by saved Variant)

| Field | Label-ar | Label-en | Read-only |
|---|---|---|---|
| barcodeValue | الباركود | Barcode | after saving |
| barcodeTypeCode | نوع الباركود | Barcode type | after saving; SCALE offered only while an active scale label layout exists (REQ-CAT-096) |
| uomCode | وحدة القياس | Unit of measure | after saving; options: the base unit + the active Item units (RULE-CAT-031), default the base unit |
| isActiveFl | نشط | Active | changed by "Deactivate" |

Pane actions per Variant: "Add barcode / إضافة باركود" (one-line drawer: type GTIN or SCALE, value, unit) · "Generate internal barcode / إنشاء باركود داخلي" (one-line drawer: unit only). While `variants` is off, the pane shows the default Variant's barcodes without a Variant column.

### Entry sub-view — Bundle components pane (ENT-CAT-007; kind BUNDLE and `bundles` on)

| Field | Label-ar | Label-en | Read-only |
|---|---|---|---|
| componentVariantFk | المتغيّر المكوِّن | Component variant | shown as Variant code + nameAr; chosen in the picker |
| quantity | الكمية | Quantity | no (3 decimals) |
| sortOrder | الترتيب | Sort order | no (drag to reorder) |

Pane action: "Add component / إضافة مكوّن" opens the **Bundle component picker** (second level). The picker searches active STANDARD Items (code, nameAr), expands an Item into its active Variants (code, nameAr), and multi-selects Variants. Variants already listed are disabled (RULE-CAT-019). Confirm returns new lines with quantity 1; the picker saves nothing. Line action: Remove.

### Entry sub-view — Images pane (ENT-CAT-010)

| Field | Label-ar | Label-en | Read-only |
|---|---|---|---|
| fileDocumentRef | ملف الصورة | Image file | thumbnail (UXD-CAT-003); chosen once on add and uploaded through the given FILE endpoint when Save runs (ADR-CAT-056) |
| nameAr | وصف الصورة بالعربية | Arabic image description | no (default the Item's nameAr) |
| nameEn | وصف الصورة بالإنجليزية | English image description | no |
| variantFk | المتغيّر | Variant | no (options: this Item's Variants; only while `variants` is on) |
| sortOrder | الترتيب | Sort order | no (drag to reorder) |
| isPrimaryFl | الصورة الرئيسية | Main image | no (one per Item; "Set as main") |
| isActiveFl | نشط | Active | changed by "Remove" |

Pane action: "Add image / إضافة صورة" (one-line drawer: file JPG/PNG/WEBP ≤ 5 MB, descriptions, Variant).

### Entry sub-view — record commands (outside the form)

"Publish / نشر" · "Unpublish / إلغاء النشر" · "Deactivate / إيقاف" · "Reactivate / إعادة تفعيل" · "Print labels / طباعة الملصقات" (jump). Each runs at once after a confirmation, and only while the form has no pending change. Otherwise the button is disabled and shows the "save first" hint (ADR-CAT-049 §5).

An inactive Item opens read-only, except for the three repairs that let it be reactivated (REQ-CAT-135, ADR-CAT-063, ADR-CAT-068):
- the **Category** field stays editable (active Categories only), so an Item whose Category went inactive can be moved;
- **"Generate variants"** stays available while `variants` is on, so an Item with no active Variant can get one;
- the **Bundle components** pane stays editable while `bundles` is on, so a Bundle with an invalid component can be fixed.

Every other field and line is read-only, and no other line can be added. "Reactivate", "Save" (for the three repairs) and the line "Deactivate" actions stay available. When Reactivate is refused, its banner names the reason and links to the matching repair: RULE-CAT-050 → "Generate variants" · RULE-CAT-026 → the Category field (and "Open the category" in Categories, where it can be reactivated) · RULE-CAT-019 → the Bundle components pane. If a save still reaches an inactive Item or Variant with another change, the form shows the RULE-CAT-053 message.

Screen copy:

| Key | ar | en |
|---|---|---|
| title | الأصناف | Items |
| new-item | صنف جديد | New item |
| import | استيراد | Import |
| empty-search | لا توجد أصناف بعد. أنشئ صنفًا أو استورد قائمتك من Excel. | No items yet. Create an item or import your list from Excel. |
| no-match | لا توجد أصناف مطابقة. غيّر عوامل البحث. | No matching items. Change the filters. |
| variants | المتغيّرات | Variants |
| units | الوحدات | Units |
| barcodes | الباركودات | Barcodes |
| bundle-components | مكوّنات الحزمة | Bundle components |
| images | الصور | Images |
| open-existing | افتح الصنف الموجود | Open the existing item |
| scan-weight | الوزن المضمّن: {value} {unit} | Embedded weight: {value} {unit} |
| scan-price | السعر المضمّن: {value} | Embedded price: {value} |

## SCR-CAT-002 — Categories / الفئات                 traces=REQ-CAT-136,AC-CAT-153,AC-CAT-157,AC-CAT-158,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-068,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,REQ-CAT-072,AC-CAT-066,AC-CAT-067,AC-CAT-068,AC-CAT-069,AC-CAT-070,AC-CAT-071,AC-CAT-072,AC-CAT-073,AC-CAT-074

Source            : SCR-REQ-CAT-002 · page code CAT_CATEGORIES · owning ENT-CAT-006 (ENT-CAT-001 for the Items of a Category)
UI pattern        : Master + Detail (tree + node entry), composite. Content shape: true hierarchy (parent/child)
Sub-views         : Tree (master, with text and active filters) · Category entry (detail) — under this ONE SCR. The entry has one second-level view: the Items of this Category
Fields shown      : code الرمز / Code (mandatory; read-only after creation — the update request carries no code) · nameAr الاسم بالعربية / Arabic name (mandatory) · nameEn الاسم بالإنجليزية / English name (Advanced) · parentCategoryFk الفئة الأم / Parent category (Advanced; tree select of active Categories, empty = top level; the node itself and its sub-categories are disabled — RULE-CAT-023) · sortOrder الترتيب / Sort order (Advanced; default next under the parent) · isActiveFl نشط / Active (read-only; changed by Deactivate on an active node and by Reactivate / إعادة تفعيل on an inactive one — REQ-CAT-136, ADR-CAT-069). Tree node: code, nameAr (nameEn in en, falling back to nameAr), inactive nodes greyed when the active filter is off. Items of this Category (second level): code الرمز / Code · nameAr الاسم بالعربية / Arabic name · nameEn الاسم بالإنجليزية / English name — paged
Composition       : container drawer · Category record → none · items of this category → summary row + second level · submits: one
Permissions       : PERM_CAT_CATEGORIES_VIEW (gateway) · PERM_CAT_CATEGORIES_CREATE · PERM_CAT_CATEGORIES_UPDATE · PERM_CAT_CATEGORIES_DELETE — Owner: VIEW, CREATE, UPDATE, DELETE · Seller/cashier: VIEW · Warehouse staff: VIEW (SRS B4)
Cross-module data : none
Navigation        : `/cat/categories?text=&isActiveFl=` (tree) · `?node=<categoryPk|new>` opens the entry drawer (`&parent=<categoryPk>` seeds the parent of a new node) · `&items=open` opens the Items of this Category · from: Catalog menu, Items · to: Items Entry `/cat/items/:itemPk` (one Item of the list) · Items Search `/cat/items?categoryFk=<categoryPk>` ("Open in Items", seeds the one Category)
States            : empty — no Category: "No categories yet" + "New category" · Items of this Category empty: "No active items in this category" · loading · error (generic) · offline (not required by the SRS)

The "Items of this category" summary row shows the total count (from the first page) and a capped preview of 3 names. Opening it shows the paged list in a second-level drawer, a sibling of the entry drawer. The list is read-only: it returns nothing and saves nothing.

Screen copy:

| Key | ar | en |
|---|---|---|
| title | الفئات | Categories |
| new-category | فئة جديدة | New category |
| new-sub | فئة فرعية جديدة | New sub-category |
| items-of | أصناف هذه الفئة | Items of this category |
| open-in-items | افتح في الأصناف | Open in Items |
| empty-tree | لا توجد فئات بعد. أنشئ أول فئة. | No categories yet. Create the first category. |
| empty-items | لا توجد أصناف نشطة في هذه الفئة. | No active items in this category. |

## SCR-CAT-003 — Variant attributes / خصائص المتغيّرات                 traces=REQ-CAT-137,REQ-CAT-138,AC-CAT-159,AC-CAT-160,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,AC-CAT-011,AC-CAT-012,AC-CAT-013,AC-CAT-014,AC-CAT-016,AC-CAT-017,AC-CAT-044,AC-CAT-045,AC-CAT-046,AC-CAT-047,AC-CAT-048,AC-CAT-049,AC-CAT-050,AC-CAT-056,AC-CAT-057,UXD-CAT-002

Source            : SCR-REQ-CAT-003 · page code CAT_VARIANT_ATTRIBUTES · owning ENT-CAT-003 (values ENT-CAT-004)
UI pattern        : Master + Detail, composite. Content shape: header + repeating lines (attribute + its values)
Sub-views         : List (master, text and active filters, ordered by sortOrder) · Attribute entry (detail) — under this ONE SCR. The entry has one second-level view: the values editor
Fields shown      : attribute — code الرمز / Code (mandatory; read-only after creation) · nameAr الاسم بالعربية / Arabic name (mandatory) · nameEn الاسم بالإنجليزية / English name (Advanced) · sortOrder الترتيب / Sort order (Advanced; default next) · isActiveFl نشط / Active (read-only; changed by Deactivate, or by Reactivate / إعادة تفعيل on an inactive attribute — REQ-CAT-137). Value lines — code الرمز / Code (mandatory; read-only after creation) · nameAr الاسم بالعربية / Arabic name (mandatory) · nameEn الاسم بالإنجليزية / English name · sortOrder الترتيب / Sort order · isActiveFl نشط / Active (changed by the line's Deactivate, or by its Reactivate on an inactive value — REQ-CAT-138, ADR-CAT-069). List row: code, nameAr, nameEn, active, value count
Composition       : container drawer · attribute record → none · values → summary row + second level · submits: one
Permissions       : PERM_CAT_VARIANT_ATTRIBUTES_VIEW (gateway) · PERM_CAT_VARIANT_ATTRIBUTES_CREATE · PERM_CAT_VARIANT_ATTRIBUTES_UPDATE · PERM_CAT_VARIANT_ATTRIBUTES_DELETE — Owner: VIEW, CREATE, UPDATE, DELETE · Seller/cashier: VIEW · Warehouse staff: VIEW (SRS B4)
Cross-module data : availability of the whole screen and its menu entry → UXD-CAT-002 (`variants` flag, erp-core cu)
Navigation        : `/cat/variant-attributes?text=&isActiveFl=` (list) · `?attribute=<variantAttributePk|new>` opens the entry drawer · `&values=open` opens the values editor · `&returnTo=<encoded Items Entry path>` returns to the Items Entry after Save · from: Catalog menu (only while `variants` is on), Items Variant generation picker · to: Items Entry (when `returnTo` is set)
States            : empty — no attribute: "No variant attributes yet" + "New attribute" · flag off: RULE-CAT-008 text with «variants» (direct link while the flag is off) · loading · error (generic) · offline (not required by the SRS)

The "Values" summary row shows the active value count and a capped preview of the first 5 value names in sortOrder, with an "Edit values / تعديل القيم" trigger. The values editor is a second-level drawer, a sibling of the entry drawer, opened from `&values=open`. It lists the value lines in the body's own scroll, offers "Add value / إضافة قيمة" (a new line), reorder, Deactivate per active line and Reactivate per inactive line (each marked pending). Confirm returns the edited set to the entry's form state; Cancel discards the draft. The editor saves nothing. The entry's Save is the one save.

Screen copy:

| Key | ar | en |
|---|---|---|
| title | خصائص المتغيّرات | Variant attributes |
| new-attribute | خاصية جديدة | New attribute |
| values | القيم | Values |
| edit-values | تعديل القيم | Edit values |
| add-value | إضافة قيمة | Add value |
| empty | لا توجد خصائص بعد. أنشئ خاصية مثل المقاس أو اللون. | No variant attributes yet. Create one such as size or colour. |

## SCR-CAT-004 — Item import / استيراد الأصناف                 traces=REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,AC-CAT-037,AC-CAT-038,AC-CAT-039,AC-CAT-040,AC-CAT-041,AC-CAT-042,AC-CAT-043

Source            : SCR-REQ-CAT-004 · page code CAT_ITEM_IMPORT · owning ENT-CAT-001 (ENT-CAT-002, ENT-CAT-009 created per row)
UI pattern        : Wizard step (upload + result list). It is also mounted as step 5 "import items" of the setup wizard (wizard owner: P-02)
Sub-views         : Upload · Result — under this ONE SCR
Fields shown      : the xlsx file ملف الاستيراد / Import file (mandatory, the only input). Result: rows read الصفوف المقروءة / Rows read · Items created الأصناف المنشأة / Items created · rows rejected الصفوف المرفوضة / Rows rejected · rejected rows: row number رقم الصف / Row · column العمود / Column · reason السبب / Reason (the row's messageAr / messageEn). The 11 import columns are the import sheet's (REQ-CAT-036); they are not entered here
Composition       : container page · import file → none · rejected rows → inline · submits: one
Permissions       : PERM_CAT_ITEM_IMPORT_VIEW (gateway) · PERM_CAT_ITEM_IMPORT_CREATE — Owner: VIEW, CREATE (SRS B4)
Cross-module data : none
Navigation        : `/cat/item-import` · from: Catalog menu, Items Search "Import", setup wizard step 5 · to: Items Search `/cat/items` ("Open Items")
States            : empty — before a file is chosen: the three steps (download, fill, import) · loading — "Importing…" while the file is processed (LOCAL, the button shows progress) · error (file rejected: RULE-CAT-044 text; generic otherwise) · offline (not required by the SRS)

The rejected-rows list sits inline under the counts, in the page's own scroll. It is read-only and has no save of its own.

Screen copy:

| Key | ar | en |
|---|---|---|
| title | استيراد الأصناف | Item import |
| download-sheet | تنزيل ورقة الاستيراد | Download import sheet |
| choose-file | اختر ملف xlsx | Choose an xlsx file |
| import | استيراد | Import |
| importing | جارٍ الاستيراد… | Importing… |
| steps | 1) نزّل ورقة الاستيراد · 2) املأها · 3) استوردها | 1) Download the import sheet · 2) Fill it in · 3) Import it |
| open-items | افتح الأصناف | Open Items |

## SCR-CAT-005 — Barcode labels / ملصقات الباركود                 traces=REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,AC-CAT-103,AC-CAT-104,AC-CAT-105,AC-CAT-106,AC-CAT-136

Source            : SCR-REQ-CAT-005 · page code CAT_BARCODE_LABELS · owning ENT-CAT-002 (ENT-CAT-009 read)
UI pattern        : Search + Entry, composite. Content shape: header + repeating lines (Variant, copies)
Sub-views         : Lines (entry) · Variant search (second level picker) — under this ONE SCR
Fields shown      : line — Variant (ENT-CAT-002) shown as code الرمز / Code · Variant nameAr الاسم بالعربية / Arabic name · Item nameAr الصنف / Item · barcode الباركود / Barcode (earliest-created active barcode, read-only) · copies عدد النسخ / Copies (mandatory, 1–999, default 1). Footer: total labels إجمالي الملصقات / Total labels. Picker rows: Variant code, Variant nameAr, Item nameAr, barcode (empty when none)
Composition       : container page · label lines → inline · variant picker → summary row + second level · submits: one
Permissions       : PERM_CAT_BARCODE_LABELS_VIEW (gateway) · PERM_CAT_BARCODE_LABELS_CREATE — Owner: VIEW, CREATE · Warehouse staff: VIEW, CREATE (SRS B4)
Cross-module data : none
Navigation        : `/cat/barcode-labels` · `?variantPk=<variantPk>` seeds one line (from Items) · `?picker=variants&text=` opens the Variant picker · from: Catalog menu, Items Entry "Print labels" · to: Items Entry `/cat/items/:itemPk` ("Open item" on a line without a barcode)
States            : empty — no line: "Add variants to print their labels" + "Add variants" · picker with no match: "No variants match" · loading · error (generic; RULE-CAT-036 / 037 texts at the lines) · offline (not required by the SRS)

The lines sit inline in a pane under the screen header. A copies field is a single number input, no taller than the fields beside it. The Variant picker is a second-level drawer, a sibling of the page. It searches by code, name or barcode (paged), multi-selects, and on Confirm returns the chosen Variants as new lines (copies 1, duplicates merged). It saves nothing. Print is the one submit, and it opens the printable document.

Screen copy:

| Key | ar | en |
|---|---|---|
| title | ملصقات الباركود | Barcode labels |
| add-variants | إضافة متغيّرات | Add variants |
| print | طباعة | Print |
| empty | أضف متغيّرات لطباعة ملصقاتها. | Add variants to print their labels. |
| no-barcode | لا يوجد باركود نشط | No active barcode |
| open-item | افتح الصنف | Open item |

## SCR-CAT-006 — Catalogue settings / إعدادات الكتالوج                 traces=AC-CAT-152,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,REQ-CAT-134,AC-CAT-022,AC-CAT-023,AC-CAT-024,AC-CAT-094,AC-CAT-095,AC-CAT-096,AC-CAT-097,AC-CAT-100,AC-CAT-122,AC-CAT-123,AC-CAT-124,AC-CAT-125,AC-CAT-126,AC-CAT-141,UXD-CAT-001

Source            : SCR-REQ-CAT-006 · page code CAT_SETTINGS · owning ENT-CAT-011 and ENT-CAT-012 (ENT-CAT-001, 002, 005 read by the turn-off check)
UI pattern        : Master + Detail per list, composite. Content shape: two flat lists + check panel
Sub-views         : Scale label layouts (list + entry) · Unit e-invoice codes (list + entry) · Turn-off check panel — under this ONE SCR
Fields shown      : scale label layout — key البادئة / Prefix (mandatory; read-only after creation) · scaleCodeLength طول رمز الميزان / Scale code length (mandatory, pre-filled 5; read-only after creation — ADR-CAT-064) · embeddedValueTypeCode نوع القيمة المضمّنة / Embedded value type (mandatory, pre-filled WEIGHT) · valueDecimals الأرقام العشرية / Decimals (mandatory, pre-filled 3) · valueAr الوصف بالعربية / Arabic description (Advanced) · valueEn الوصف بالإنجليزية / English description (Advanced) · isActiveFl نشط / Active (read-only; changed by Deactivate). Unit e-invoice code — key وحدة القياس / Unit of measure (mandatory; read-only after creation) · valueAr الاسم بالعربية / Arabic name and valueEn الاسم بالإنجليزية / English name (read-only, derived) · einvoiceUnitCode وحدة الفاتورة الإلكترونية / E-invoice unit (mandatory) · isActiveFl نشط / Active (read-only; changed by Deactivate). Turn-off check — flag المفتاح / Flag (`variants` · `bundles`) · result: allowed مسموح / Allowed · count العدد / Count · Items (code الرمز / Code, nameAr الاسم بالعربية / Arabic name, up to 20) · message (from the envelope)
Composition       : container drawer · scale label layout → none · unit e-invoice code → none · turn-off check → none · submits: one
Permissions       : PERM_CAT_SETTINGS_VIEW (gateway) · PERM_CAT_SETTINGS_CREATE · PERM_CAT_SETTINGS_UPDATE · PERM_CAT_SETTINGS_DELETE — Owner: VIEW, CREATE, UPDATE, DELETE (SRS B4)
Cross-module data : embeddedValueTypeCode, key (CAT_UOM), einvoiceUnitCode → UXD-CAT-001 (CAT lookup keys, read through API-CAT-052)
Navigation        : `/cat/settings` · `?layout=<scaleLabelLayoutPk|new>` opens the layout drawer · `?unitCode=<unitEinvoiceCodePk|new>` opens the unit code drawer · `?check=variants|bundles` runs and shows the turn-off check · from: Catalog menu, Items · to: Items Entry `/cat/items/:itemPk` (a dependent Item of the check)
States            : empty — no layout: "No scale label layouts" + "New layout"; no unit code: "No unit e-invoice codes" + "New unit code"; check not run: the flag selector and "Check" · loading · error (generic) · offline (not required by the SRS)

Each drawer edits ONE record and has ONE Save. The page itself has no save. The turn-off check is a read: it saves nothing.

Screen copy:

| Key | ar | en |
|---|---|---|
| title | إعدادات الكتالوج | Catalogue settings |
| layouts | إعدادات ملصق الميزان | Scale label layouts |
| new-layout | إعداد جديد | New layout |
| unit-codes | رموز الوحدات في الفاتورة الإلكترونية | Unit e-invoice codes |
| new-unit-code | رمز وحدة جديد | New unit code |
| turn-off-check | فحص إيقاف قدرة | Capability turn-off check |
| check | فحص | Check |
| allowed-yes | يمكن الإيقاف الآن. | It can be turned off now. |

## Cross-module display dependencies — UXD

## UXD-CAT-001 — Lookup values and labels of the CAT_* keys                 traces=API-CAT-052,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,AC-CAT-075,AC-CAT-076,AC-CAT-077,AC-CAT-078

Screen        : SCR-CAT-001 (brandCode, baseUomCode, taxClassCode, itemKindCode, publishStateCode, einvoiceCodeTypeCode, Item unit uomCode, barcode barcodeTypeCode / uomCode) · SCR-CAT-006 (embeddedValueTypeCode, Unit e-invoice code key, einvoiceUnitCode)
Owner module  : erp-core `modules.mdl` (given; the keys are CAT-owned, the values are stored by mdl and read by CAT through `OwnedLookups`)
Need          : the active values of each key as options, and the label (ar/en) of any value shown on a record, including one deactivated later
Source        : CAT's own read `GET /api/v1/cat/lookups/{key}` (API-CAT-052), which serves the active values of one CAT_* key through `OwnedLookups` to any user holding a CAT VIEW permission (ADR-CAT-065, amending ADR-CAT-050 §1)

## UXD-CAT-002 — Capability flags `variants` and `bundles`                 traces=REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,AC-CAT-011,AC-CAT-012,AC-CAT-013,AC-CAT-014,AC-CAT-015

Screen        : SCR-CAT-001 (Variants pane, attribute fields, Item kind field and filter, Bundle components pane) · SCR-CAT-003 (the whole screen and its menu entry)
Owner module  : erp-core `modules.cu` behind FeatureFlags (given)
Need          : the current value of each flag on every screen entry and on window focus; unreadable → the flag-bound function is unavailable with RULE-CAT-009's text
Source        : the platform's app configuration read `GET /api/public/app-config` [KB:raw-idea.md, technical base] (ADR-CAT-050)

## UXD-CAT-003 — Image file of an Item media record                 traces=REQ-CAT-105,REQ-CAT-109,AC-CAT-107,AC-CAT-111

Screen        : SCR-CAT-001 (Images pane thumbnails, Variant images on expand)
Owner module  : erp-core `modules.file`, public image storage (given)
Need          : a displayable image for `fileDocumentRef`
Source        : the platform shell's public file client of the given (ADR-CAT-050)

## Reconciliation self-check (SRS B1–B4 ↔ draft)

```
RECONCILIATION — CAT v2
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → 22/22; US-CAT-022's flow ends on SCR-CAT-001 (SRS "from: global search result"); no flow excluded
B2 no RULE-* contradicts a flow/spec outcome                           → none found; the one-Save composition of SCR-CAT-001 changes when child actions are sent, not what they do (ADR-CAT-049)
B3 every field/permission on a screen exists in the SRS               → extra: none ("Find by barcode" is the B5 lookup action, not a field); missing: none
B4 every screen entry of the SRS has exactly one SCR-* block          → SCR-REQ-CAT-001…006 ↔ SCR-CAT-001…006, one each
RESULT  reconciled 6 · reworked 0 · ADRs ADR-CAT-049, ADR-CAT-050, ADR-CAT-051
```

