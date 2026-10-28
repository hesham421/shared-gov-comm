# Flow diagram — Catalog / الكتالوج (CAT) — v2

Module : CAT · Version : v2 · Stage : P3.2 Part A · Profile : commerce
Inputs : srs (SCR-REQ-CAT-001 … 006, REQ/AC), prd (US-CAT-001 … 022, priorities)
Screens: SCR-CAT-001 Items · SCR-CAT-002 Categories · SCR-CAT-003 Variant attributes · SCR-CAT-004 Item import · SCR-CAT-005 Barcode labels · SCR-CAT-006 Catalogue settings

This is a delta run, but the current state holds no earlier flow diagram, so every flow below is ADDED. The flows carry no atom of their own. Each one is named by its starting screen and a short name, and each cites a story and a screen that the SRS declares. Menu entries follow two rules: a menu entry appears only with the VIEW permission of its page code, and an entry bound to a capability flag appears only while that flag is on (UXD-CAT-002).

```
Catalog / الكتالوج
├── Items / الأصناف                         SCR-CAT-001   PERM_CAT_ITEMS_VIEW
├── Categories / الفئات                      SCR-CAT-002   PERM_CAT_CATEGORIES_VIEW
├── Variant attributes / خصائص المتغيّرات    SCR-CAT-003   PERM_CAT_VARIANT_ATTRIBUTES_VIEW · only while `variants` is on
├── Item import / استيراد الأصناف            SCR-CAT-004   PERM_CAT_ITEM_IMPORT_VIEW  (also setup wizard step 5)
├── Barcode labels / ملصقات الباركود         SCR-CAT-005   PERM_CAT_BARCODE_LABELS_VIEW
└── Catalogue settings / إعدادات الكتالوج    SCR-CAT-006   PERM_CAT_SETTINGS_VIEW
```

## SCR-CAT-001 flows

```
FLOW — SCR-CAT-001 · find and open an Item          traces=US-CAT-001,REQ-CAT-001,REQ-CAT-002,REQ-CAT-003,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : home / Catalog menu → Items · Search (filters: text, Category, brand, Item kind*, publish state, active; page of 20 sorted by nameAr) → row → Items · Entry `/cat/items/:itemPk` → back → Items · Search (filters kept)
Trigger   : the owner, seller/cashier or warehouse staff needs the one catalogue record of an Item
Priority  : HIGH (US-CAT-001)
Note      : * the Item kind filter is shown only while `bundles` is on (REQ-CAT-015)
```

```
FLOW — SCR-CAT-001 · create an Item from its Arabic name   traces=US-CAT-007,US-CAT-008,US-CAT-009,REQ-CAT-024,REQ-CAT-025,REQ-CAT-026,REQ-CAT-027,REQ-CAT-028,REQ-CAT-029,REQ-CAT-030,REQ-CAT-031,REQ-CAT-032,REQ-CAT-033,REQ-CAT-034,REQ-CAT-035,REQ-CAT-007,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : Items · Search → "New item" → Items · Entry `/cat/items/new` (nameAr; base unit PIECE, tax class STANDARD and kind STANDARD pre-filled; the rest under "Advanced") → Save → Items · Entry `/cat/items/:itemPk` (code ITM-…, default Variant VAR-… shown)
Trigger   : the owner adds an Item during setup or later
Priority  : HIGH (US-CAT-007, US-CAT-008, US-CAT-009)
Exit      : on RULE-CAT-003 the message offers "Open the existing item" → Items · Entry of that Item
```

```
FLOW — SCR-CAT-001 · maintain an Item and see it reach every channel   traces=US-CAT-001,US-CAT-002,US-CAT-014,US-CAT-021,REQ-CAT-004,REQ-CAT-007,REQ-CAT-008,REQ-CAT-009,REQ-CAT-010,REQ-CAT-063,REQ-CAT-072,REQ-CAT-073,REQ-CAT-074,REQ-CAT-075,REQ-CAT-076,REQ-CAT-082,REQ-CAT-117,REQ-CAT-118,REQ-CAT-119,REQ-CAT-123,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : Items · Entry `/cat/items/:itemPk` → change names, Category, brand, tax class, descriptions, e-invoice code (Advanced) or a Variant's e-invoice override (one-line drawer) → Save → re-read of the Item shows the committed values
Trigger   : the owner corrects or completes an Item
Priority  : HIGH (US-CAT-001, US-CAT-002, US-CAT-021); — (US-CAT-014)
```

```
FLOW — SCR-CAT-001 · generate Variants from attribute values   traces=US-CAT-011,US-CAT-005,REQ-CAT-014,REQ-CAT-017,REQ-CAT-018,REQ-CAT-019,REQ-CAT-049,REQ-CAT-050,REQ-CAT-051,REQ-CAT-052,REQ-CAT-053,REQ-CAT-061,SCR-CAT-001,SCR-CAT-003
Screens   : SCR-CAT-001, SCR-CAT-003
Sequence  : Items · Entry → Variants pane → "Generate variants" → Variant generation picker (second level: up to 3 active attributes, values per attribute) → Confirm (returns the selection) → Save → Variants pane lists the existing Variants with their first values and the new combinations
Trigger   : the owner's business uses variants (`variants` on) and the Item is STANDARD
Priority  : HIGH (US-CAT-011, US-CAT-005)
Exit      : no active attribute → the picker offers "Open Variant attributes" → SCR-CAT-003 `/cat/variant-attributes?attribute=new`
```

```
FLOW — SCR-CAT-001 · build a Bundle from existing Variants   traces=US-CAT-012,US-CAT-005,REQ-CAT-015,REQ-CAT-020,REQ-CAT-056,REQ-CAT-057,REQ-CAT-058,REQ-CAT-059,REQ-CAT-060,REQ-CAT-062,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : Items · Search → "New item" → Entry with kind BUNDLE → Bundle components pane → "Add component" → Bundle component picker (second level: active STANDARD Items → one of their active Variants) → Confirm → quantity per line → Save
Trigger   : the owner's business uses bundles (`bundles` on)
Priority  : HIGH (US-CAT-012)
```

```
FLOW — SCR-CAT-001 · units and barcodes of an Item   traces=US-CAT-015,US-CAT-016,US-CAT-017,REQ-CAT-077,REQ-CAT-078,REQ-CAT-079,REQ-CAT-080,REQ-CAT-081,REQ-CAT-083,REQ-CAT-084,REQ-CAT-085,REQ-CAT-087,REQ-CAT-088,REQ-CAT-089,REQ-CAT-091,REQ-CAT-096,REQ-CAT-099,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : Items · Entry → Units pane → "Add unit" (one-line drawer: unit, conversion factor, purchase/sales flags) → Barcodes pane → "Add barcode" (one-line drawer per saved Variant: GTIN, internal or SCALE, unit) → Save
Trigger   : the owner trades by the carton, receives goods with a GTIN, or sells by weight
Priority  : — (US-CAT-015, US-CAT-016); LOW (US-CAT-017)
```

```
FLOW — SCR-CAT-001 · images of an Item   traces=US-CAT-019,REQ-CAT-105,REQ-CAT-106,REQ-CAT-107,REQ-CAT-108,REQ-CAT-109,REQ-CAT-110,REQ-CAT-111,REQ-CAT-112,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : Items · Entry → Images pane → "Add image" (one-line drawer: file, descriptions, Variant) / change order or main image / remove → Save
Trigger   : the owner prepares the Item for the storefront
Priority  : MEDIUM (US-CAT-019)
```

```
FLOW — SCR-CAT-001 · publish, unpublish, deactivate, reactivate   traces=US-CAT-020,US-CAT-001,REQ-CAT-130,REQ-CAT-135,REQ-CAT-005,REQ-CAT-006,REQ-CAT-113,REQ-CAT-114,REQ-CAT-115,REQ-CAT-116,REQ-CAT-062,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : Items · Entry (no pending change) → Publish | Unpublish | Deactivate | Reactivate → confirmation → state badge updates → back to Items · Search
Trigger   : the owner decides what is offered online, or retires / returns an Item
Priority  : MEDIUM (US-CAT-020); HIGH (US-CAT-001)
Exit      : Reactivate refused → the banner links to the repair on the same Entry, which stays available on the inactive Item (ADR-CAT-068): no active Variant → "Generate variants" · inactive Category → the Category field, or Categories `?node=<categoryFk>` to reactivate it · invalid Bundle component → Bundle components pane → Save → Reactivate again
```

```
FLOW — SCR-CAT-001 · find an Item by barcode or scale label   traces=US-CAT-016,US-CAT-017,REQ-CAT-086,REQ-CAT-090,REQ-CAT-097,REQ-CAT-100,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : Items · Search → "Find by barcode" field (scan or type) → hit: Items · Entry `/cat/items/:itemPk?variant=<variantPk>` with the Variant, its unit and any embedded weight or price shown → miss: RULE-CAT-047 / RULE-CAT-035 message with "search by name" (text filter seeded with nothing; focus moves to it)
Trigger   : a seller/cashier or warehouse staff holds goods in hand
Priority  : — (US-CAT-016); LOW (US-CAT-017)
```

```
FLOW — SCR-CAT-001 · global search to an Item   traces=US-CAT-022,REQ-CAT-125,REQ-CAT-126,REQ-CAT-127,SCR-CAT-001
Screens   : SCR-CAT-001
Sequence  : platform global search box (outside CAT, ADR-CAT-043) → catalogue hits (≤ 20, exact code/barcode first) → hit → Items · Entry `/cat/items/:itemPk`
Trigger   : a seller/cashier types at least 2 characters in the one global search
Priority  : HIGH (US-CAT-022)
Note      : SCR-REQ-CAT-001 names "global search result" as an entry to Items; the search box is not a CAT screen and gets no SCR
```

```
FLOW — SCR-CAT-001 · catalogue shows only enabled functions   traces=US-CAT-003,US-CAT-004,US-CAT-005,REQ-CAT-011,REQ-CAT-012,REQ-CAT-013,REQ-CAT-014,REQ-CAT-015,REQ-CAT-016,SCR-CAT-001,SCR-CAT-003
Screens   : SCR-CAT-001, SCR-CAT-003
Sequence  : any Catalog entry → capability flags read (UXD-CAT-002) → `variants` off: no "Variant attributes" menu entry, no Variants pane, no attribute fields · `bundles` off: no kind field, no kind filter, no Bundle components pane → flag turned on in settings → next screen entry shows them
Trigger   : the owner's activity preset or a later flag change
Priority  : HIGH (US-CAT-003, US-CAT-004, US-CAT-005)
Exit      : direct link to `/cat/variant-attributes` while `variants` is off → RULE-CAT-008 text with the flag name
```

## SCR-CAT-002 flows

```
FLOW — SCR-CAT-002 · organise the Category tree   traces=US-CAT-013,REQ-CAT-136,REQ-CAT-064,REQ-CAT-065,REQ-CAT-066,REQ-CAT-067,REQ-CAT-069,REQ-CAT-070,REQ-CAT-071,SCR-CAT-002
Screens   : SCR-CAT-002
Sequence  : Catalog menu → Categories (tree by sortOrder) → node or "New category" → Category entry drawer `?node=<categoryPk|new>` → Save | Deactivate | Reactivate (inactive node — ADR-CAT-069) → tree refreshed
Trigger   : the owner sets up or reorganises the classification
Priority  : — (US-CAT-013)
```

```
FLOW — SCR-CAT-002 · Items of a Category   traces=US-CAT-013,REQ-CAT-068,REQ-CAT-072,SCR-CAT-002,SCR-CAT-001
Screens   : SCR-CAT-002, SCR-CAT-001
Sequence  : Categories → Category entry drawer → "Items of this category" summary row → second-level drawer (active Items of the Category and its active sub-categories) → row → Items · Entry `/cat/items/:itemPk` · or "Open in Items" → Items · Search `?categoryFk=<categoryPk>`
Trigger   : the owner checks what a Category holds before moving or deactivating it
Priority  : — (US-CAT-013)
```

## SCR-CAT-003 flows

```
FLOW — SCR-CAT-003 · maintain Variant attributes and their values   traces=US-CAT-011,US-CAT-004,REQ-CAT-137,REQ-CAT-138,REQ-CAT-014,REQ-CAT-016,REQ-CAT-042,REQ-CAT-043,REQ-CAT-044,REQ-CAT-045,REQ-CAT-046,REQ-CAT-047,REQ-CAT-048,REQ-CAT-054,REQ-CAT-055,SCR-CAT-003,SCR-CAT-001
Screens   : SCR-CAT-003, SCR-CAT-001
Sequence  : Catalog menu (only while `variants` is on) or Items · Variant generation picker → Variant attributes (list by sortOrder) → attribute or "New attribute" → attribute entry drawer `?attribute=<pk|new>` (Reactivate on an inactive attribute — ADR-CAT-069) → "Values" summary row → values drawer `&values=open` (returns the set) → Save → back to Items when opened from there
Trigger   : the owner defines size, colour or another dimension
Priority  : HIGH (US-CAT-011, US-CAT-004)
```

## SCR-CAT-004 flows

```
FLOW — SCR-CAT-004 · import Items from Excel   traces=US-CAT-010,REQ-CAT-036,REQ-CAT-037,REQ-CAT-038,REQ-CAT-039,REQ-CAT-040,REQ-CAT-041,REQ-CAT-139,SCR-CAT-004,SCR-CAT-001
Screens   : SCR-CAT-004, SCR-CAT-001
Sequence  : setup wizard step 5 "import items" or Items · Search → "Import" → Item import → "Download import sheet" → fill offline → choose xlsx → Import → result (rows read, Items created, rejected rows with row, column, reason) → "Open Items" → Items · Search
Trigger   : the owner brings an existing item list in during setup
Priority  : HIGH (US-CAT-010)
```

## SCR-CAT-005 flows

```
FLOW — SCR-CAT-005 · print barcode labels   traces=US-CAT-018,REQ-CAT-101,REQ-CAT-102,REQ-CAT-103,REQ-CAT-104,SCR-CAT-005,SCR-CAT-001
Screens   : SCR-CAT-005, SCR-CAT-001
Sequence  : Catalog menu, or Items · Entry → "Print labels" (`/cat/barcode-labels?variantPk=<variantPk>`) → Barcode labels → "Add variants" → Variant picker (second level: search by code, name, barcode) → copies per line → Print → printable document opens
Trigger   : warehouse staff label goods without a manufacturer barcode
Priority  : — (US-CAT-018)
Exit      : RULE-CAT-037 → lines without a barcode are marked; each offers "Open item" → Items · Entry `/cat/items/:itemPk` to generate an internal barcode
```

## SCR-CAT-006 flows

```
FLOW — SCR-CAT-006 · maintain scale label layouts and unit e-invoice codes   traces=US-CAT-017,US-CAT-021,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,SCR-CAT-006
Screens   : SCR-CAT-006
Sequence  : Catalog menu → Catalogue settings (both lists in full) → layout or "New layout" → layout drawer `?layout=<pk|new>` → Save | Deactivate · unit code or "New unit code" → unit code drawer `?unitCode=<pk|new>` → Save | Deactivate
Trigger   : the owner sets up a scale or the e-invoice units
Priority  : LOW (US-CAT-017); HIGH (US-CAT-021)
```

```
FLOW — SCR-CAT-006 · check whether a capability can be turned off   traces=US-CAT-006,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,SCR-CAT-006,SCR-CAT-001
Screens   : SCR-CAT-006, SCR-CAT-001
Sequence  : Catalogue settings → turn-off check panel → choose `variants` or `bundles` → Check (`?check=<flag>`) → allowed, or not allowed with count, message and up to 20 Items → Item → Items · Entry `/cat/items/:itemPk`
Trigger   : the owner considers switching a capability off
Priority  : HIGH (US-CAT-006)
```

## Coverage

| US | Flows | SCR |
|---|---|---|
| US-CAT-001 | find and open · maintain · publish/deactivate | SCR-CAT-001 |
| US-CAT-002 | maintain (re-read after Save, REQ-CAT-010) | SCR-CAT-001 |
| US-CAT-003 | enabled functions only | SCR-CAT-001, 003 |
| US-CAT-004 | enabled functions only · Variant attributes | SCR-CAT-001, 003 |
| US-CAT-005 | generate Variants · build a Bundle · enabled functions only | SCR-CAT-001 |
| US-CAT-006 | turn-off check | SCR-CAT-006 |
| US-CAT-007, 008, 009 | create from the Arabic name | SCR-CAT-001 |
| US-CAT-010 | import | SCR-CAT-004 |
| US-CAT-011 | generate Variants · Variant attributes | SCR-CAT-001, 003 |
| US-CAT-012 | build a Bundle | SCR-CAT-001 |
| US-CAT-013 | Category tree · Items of a Category | SCR-CAT-002 |
| US-CAT-014 | maintain (lookup fields) | SCR-CAT-001 |
| US-CAT-015 | units and barcodes | SCR-CAT-001 |
| US-CAT-016 | units and barcodes · find by barcode | SCR-CAT-001 |
| US-CAT-017 | units and barcodes · find by barcode · settings | SCR-CAT-001, 006 |
| US-CAT-018 | print labels | SCR-CAT-005 |
| US-CAT-019 | images | SCR-CAT-001 |
| US-CAT-020 | publish/unpublish | SCR-CAT-001 |
| US-CAT-021 | maintain (e-invoice) · settings | SCR-CAT-001, 006 |
| US-CAT-022 | global search to an Item | SCR-CAT-001 |

22 of 22 stories sit in at least one flow, and every flow names an SRS-backed screen. No flow was excluded and no navigation was invented. US-CAT-002 and US-CAT-003 ride on SCR-CAT-001, as ADR-CAT-051 §7 records.
