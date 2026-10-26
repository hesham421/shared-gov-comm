## MODULE REGISTRY — Catalog / الكتالوج (CAT)
══════════════════════════════════════════════════════════════════
Module Code    : CAT   (profile.vocabulary.module_prefixes)
Bounded context: catalog
Layer / Type   : L1 / master data     Execution tier : 1.1
Source         : NEW (no prior module-registry-cat in the generated current state)
Knowledge      : profiles/commerce/knowledge/raw-idea.md §1, §4, §5 #1, §6; domain-profile §3 #1, §4 #1, §7
Readiness      : READY (dialogue converged in round 2)
══════════════════════════════════════════════════════════════════

Boundary: what is sold. Items, variants, variant attributes, categories, bundles, barcodes, media and publish state. No price and no stock quantity [DP §7.2].

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity (ar/en) | Kind | PRIVATE / SHARED | Source |
|---|---|---|---|
| صنف / Item | master | SHARED (PRC, INV, PUR, SLS, TAX, SFR, INS) | DP §3 #1, §7.1; KB §5 #1. A bundle is an Item of the bundle kind (OI-09) |
| متغيّر الصنف / Variant | master | SHARED (PRC, INV, PUR, SLS, SWR, SFR) | OI-07: every Item has at least one Variant. Stock, price, barcode and e-invoice item code key on the Variant |
| خاصية المتغيّر / Variant attribute | master | PRIVATE | OI-06: platform-own (no custom-attributes capability in erp-core 1.6.0); KB §4.1 |
| قيمة خاصية المتغيّر / Variant attribute value | master | PRIVATE | OI-06; KB §4.1 (size, colour, …) |
| فئة / Category | master | SHARED (SFR, INS) | OI-08: CAT-owned hierarchy; KB §5 #1 |
| مكوّن الحزمة / Bundle component | master | SHARED (INV, SLS, PRC) | OI-09: CAT owns the composition; INV and SLS explode it to components |
| وسائط الصنف / Item media | master | PRIVATE | OI-15: ordering, primary image, alt text (ar/en), variant association. Binaries live in given `file` |
| باركود / Barcode | master | SHARED (SLS, PUR, INV) | OI-10: 0..n per Variant (GTIN or internal) |
| تحويل وحدات الصنف / Item unit conversion | master | SHARED (PUR, SLS, INV) | AUTO (below): the units an item is bought or sold in, converted to its base unit |

Not entities:
- Publish state is an attribute of Item, with values from the lookup `publish-state`.
- The e-invoice item code is an attribute of Variant, defaultable from the Item (OI-11).
- Brand, unit of measure and item tax class are lookup types in given `mdl` that CAT masters (LOOKUPS OWNED). They receive no entity ID.

LOOKUPS OWNED    (value lists this module masters; stored in given `erp-core:modules.mdl`)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| brand | Brand / علامة تجارية | — none named | OI-08; DP §7.1 |
| unit-of-measure | Unit of measure / وحدة القياس | — none named | OI-08; DP §7.1. Each value carries its e-invoice unit code (OI-11) |
| item-tax-class | Item tax class / الفئة الضريبية للصنف | — none named | OI-11: TAX maps class × country to rate and category |
| einvoice-item-code-type | E-invoice item code type / نوع رمز الصنف في الفاتورة الإلكترونية | — none named (standard values applied by P0.5 from the ETA source) | OI-11 |
| publish-state | Publish state / حالة النشر | — none named | DP §7.1; KB §5 #1 |
Rule (profile): all LOV values are runtime-loaded from the core lookup module (mdl), with no hardcoded enums in APIs or field specs. Activity presets and capability flags are core settings, read through FeatureFlags.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| — | None. CAT is a root module; every lookup it reads is CAT-owned in given `mdl` |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| FileDocument | given erp-core:modules.file | SOFT-READ (file id) | Item media binaries in the public image store |
| LookupType / LookupValue | given erp-core:modules.mdl | SOFT-READ | brand, unit-of-measure, item-tax-class, einvoice-item-code-type, publish-state |
| AppConfiguration | given erp-core:modules.cu | SOFT-READ (via FeatureFlags) | Flags `variants` and `bundles`; scale-barcode pattern (OI-10) |
| NumberSeries | given erp-core:modules.sequence | SOFT-READ | Automatic item codes |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| erp-core:modules.file | (given — no XM edge; the SRS declares the use in its `given-usage` block) | FileImageStoreApi (public item images), FileDocumentLookupApi |
| erp-core:modules.mdl | (given — no XM edge) | MdlLookupApi; owned lookup types (common `owned-lookups`) |
| erp-core:modules.cu | (given — no XM edge) | SettingsApi behind the FeatureFlags facade; CAT contributes the flag-off guard for `variants` and `bundles` |
| erp-core:modules.sequence | (given — no XM edge) | NumberSeriesApi (item codes) |
| erp-core:modules.events | (given — no XM edge) | DomainEventPublisher / OutboxPublisher (catalogue changes for PRC, INV, SFR, INS) |
| erp-core:modules.audit | (given — no XM edge) | AuditApi (catalogue change history) |
| erp-core:modules.sec | (given — no XM edge) | PermissionContributor (catalogue permissions for the Owner, Seller/Cashier and Warehouse presets) |
| erp-core:modules.report | (given — no XM edge) | PrintApi / PrintTemplateSource (barcode labels) |
| erp-core:modules.common | (given — no XM edge) | base-entity, validation, search (global search), paging, idempotency, api-envelope |
ROOT: YES (no business-module dependency; DAG #1 [KB §5])

POLICIES REGISTERED   (POL ids defined in `business-policies-cat.md`; the statements live there only — this index keeps the registry and the policies in agreement)
| Policy | Name (en / ar) | Pattern | Status |
|---|---|---|---|
| POL-CAT-001 | One catalogue for every channel / كتالوج واحد لكل القنوات | ubiquitous | CONFIRMED |
| POL-CAT-002 | Catalogue behaviour from capability flags only / سلوك الكتالوج من مفاتيح القدرة فقط | ubiquitous | CONFIRMED |
| POL-CAT-003 | Show only what is enabled / عرض المُفعَّل فقط | state | CONFIRMED |
| POL-CAT-004 | Turning a catalogue flag on / تشغيل مفتاح قدرة للكتالوج | event | CONFIRMED |
| POL-CAT-005 | Turning a catalogue flag off while data depends on it / إيقاف مفتاح قدرة تعتمد عليه بيانات | unwanted | CONFIRMED |
| POL-CAT-006 | Minimal item creation / إنشاء صنف بأقل المدخلات | ubiquitous | CONFIRMED |
| POL-CAT-007 | Item import in the setup wizard / استيراد الأصناف في معالج الإعداد | event | CONFIRMED |
| POL-CAT-008 | Publish state controls storefront visibility / حالة النشر تحكم الظهور في المتجر | state | CONFIRMED |
| POL-CAT-009 | Items in the one global search / الأصناف في البحث الشامل | ubiquitous | CONFIRMED |
Next free policy id: 010. Every new POL-CAT id is added to this index in the same version that defines it.

AUTO-DECISIONS
AUTO: Status NEW, artifacts written in full  FROM: engine §2.1 STEP 3 — no `current-module-registry-cat` in the generated current state  IF WRONG: supply the prior registry; a later round re-casts this file as EXTENDED and keeps its entries
AUTO: Variant attribute is a CAT master entity (with attribute values), not an mdl lookup type  FROM: STEP 3/4 — KB §4.1 (attributes drive variant generation per item); domain practice (Shopify product options, Odoo product attributes)  IF WRONG: model attributes as CAT-owned mdl lookup types; Variant attribute value becomes LookupValue
AUTO: Item unit conversion owned by CAT  FROM: STEP 4 — wholesale buys and sells by carton or piece [KB §1 "wholesale traders"]; dual quantity (count + weight) stays in INV [KB §4.1]  IF WRONG: one unit per item; drop the entity
AUTO: Item codes are numbered automatically by given `sequence`, so a code is never a mandatory entry  FROM: KB §6.1 (≤ 4 mandatory fields), §5 (sequences used); manifest `sequence`  IF WRONG: the code is a manual entry, defaulted from the series
AUTO: Barcode labels print through given `report` (PrintApi, Jasper)  FROM: STEP 4 retail practice; KB header (Jasper via core PrintApi)  IF WRONG: drop label printing from CAT; INS owns it
AUTO: CAT publishes catalogue-change events; it never reads a downstream module  FROM: KB §5 rules (downward only); DP §6  IF WRONG: consumers query a CAT crossmodule API instead of subscribing
AUTO: CAT holds no price and no stock quantity  FROM: DP §7.2 catalog boundary  IF WRONG: none (boundary is stated)
AUTO: Given `module` (`module-catalog`) is not a hit for CAT  FROM: engine §1.5 STEP 0 — `module-catalog` is the catalogue of business modules and entitlements, not of items  IF WRONG: re-match against erp-core:modules.module
AUTO: The module registry indexes every POL-CAT id (POLICIES REGISTERED); the policy text stays in business-policies-cat only  FROM: engine §4 (the module registry IS this stage's registry output); contract C3.7 registry-agree (business-policies ↔ module-registry, kind POL)  IF WRONG: none (settled by the dialogue DECISION on the policy index)

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| OI-06 | Variant attributes: core seam or platform-own (P-03, CAT part) | Platform-own in CAT. erp-core 1.6.0 lists no custom-attributes capability | Round-1 recommendation; confirmed at convergence | manifest §1.5; KB §4.1 |
| OI-07 | Item / Variant model (P-04) | Every Item has ≥ 1 Variant. A default Variant exists while `variants` is off. Stock, price, barcode and e-invoice item code key on the Variant. Turning `variants` on later needs no migration | Round-1 recommendation; confirmed at convergence | KB §4.1, §6.2, §6.8; Shopify ProductVariant (every product has ≥ 1 variant); Odoo product.template / product.product |
| OI-08 | Category, Brand, UoM placement (P-05) | Category is a CAT-owned hierarchy (mdl lists are flat). Brand and UoM are CAT-owned lookup types in given `mdl` (no entity ID) | Round-1 recommendation; confirmed at convergence | KB §5 #1; manifest `mdl`, `common` owned-lookups; profile LOV rule |
| OI-09 | Bundle ownership (P-06) | CAT owns the composition (Bundle component). A bundle is an Item of the bundle kind and holds no stock of its own; INV moves and SLS sells its components. Pre-assembled kits are out of scope (manufacturing). CAND-INV-008 is withdrawn | Round-1 recommendation; confirmed at convergence | KB §3 D1, §4.1; NetSuite Kit/Package vs Assembly items |
| OI-10 | Barcodes and weighed-item codes (P-07) | A Variant carries 0..n barcodes (GTIN-8/12/13/14 or internal). Items sold by a weight or measure unit carry a scale code. The tenant's scale-barcode pattern (GS1 restricted-circulation prefixes 20–29, embedded weight or price) is a `cu` setting. No new flag | Round-1 recommendation; confirmed at convergence | KB §4.2 (produce by weight); GS1 General Specifications (RCN prefixes 20–29) |
| OI-11 | Catalogue-side e-invoice obligations (P-08) | The Variant carries the e-invoice item code (type GS1 or EGS + code), defaulted from the Item. The Item carries an item tax class from the CAT-owned lookup. TAX maps class × country to rate and category (ZATCA S/Z/E/O). UoM values carry the e-invoice unit code. No CAT → TAX edge; TAX reads CAT | Round-1 recommendation; confirmed at convergence | KB §2, §5 #8; Egypt ETA e-invoice SDK (itemType GS1/EGS, unitType); ZATCA XML implementation standard (UNCL5305 categories); Adobe Commerce product tax class |
| OI-12 | Item name languages (P-09) | Arabic name mandatory, English name optional. The Arabic name may contain Latin brand or model text. Applied by P0.5 as a standard language rule; not a client-specific POL | Round-1 recommendation; confirmed at convergence | profile language policy `ar, en`; KSA VAT Implementing Regulations Art. 53 (tax invoice in Arabic, other languages alongside) |
| OI-15 | Catalogue media (P-12) | Binaries live in given `file` (public image store). CAT owns Item media: ordering, primary image, alt text (ar/en), variant association | Round-1 recommendation; confirmed at convergence | KB §5 #1 (files public); manifest `file` |
| POL-IDX | Policy index in the module registry | `POLICIES REGISTERED` lists every POL-CAT id; the policy text lives only in `business-policies-cat.md` (satisfies C3.7) | Round-1 proposal; accepted in round 2 | engine §3.4, §4; contract C3.7 |
══════════════════════════════════════════════════════════════════
