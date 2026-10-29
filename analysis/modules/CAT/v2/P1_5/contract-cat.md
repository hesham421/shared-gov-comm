# CONTRACT — Catalog / الكتالوج (CAT) — outbound promise
══════════════════════════════════════════════════════════════════
Module : CAT   Version : v2   Profile : commerce   Stage : P1.5
Inputs : srs (P1, CAT v2 — REQ 148 · AC 174 · ENT 12 · RULE 53), registry-srs (re-stamped from the current registries at the eleventh gate-analysis revise; CON-CAT-002 wording amended by ADR-CAT-072; §4 given-usage repeated from SRS A9 as amended by ADR-CAT-076; CON-CAT-018 entity list extended by ADR-CAT-077; CON-CAT-006 and CON-CAT-007 state the unit of a Bundle component quantity — ADR-CAT-080)
Counts : CON 18 (entity items 6 · operation items 11 · event item 1)
Status : DRAFT — P1.5 pass 1 · no question raised · no ADR raised
══════════════════════════════════════════════════════════════════

## 0 — Scope of this promise

This contract states what other modules may depend on in CAT before CAT is built. It is a list of promises. It contains no implementation, no table, no path and no verb. The backend stage (P3.1) chooses those and marks each implementing block with `Honours: CON-CAT-…`.

| Item | Decided by |
|---|---|
| Consumers named in the SRS | PRC, INV, PUR, SLS, SFR, INS and TAX (A3 "Cross-module" column). Also the capability-flag owner (P-02, ADR-CAT-041) and the global search host (ADR-CAT-043) |
| Entities promised | Every SHARED (owner) ENT of the SRS: ENT-CAT-001, 002, 006, 007, 008, 009 |
| Entities not promised | The PRIVATE ENTs ENT-CAT-003, 004, 005, 010, 011, 012. Consumers see their data only through the operations below, as the SRS A3 states ("read through Variant reads", "SFR reads images through Item and Variant reads", "read through Variant and Item unit reads by SLS and TAX") |
| Operations promised | The cross-module reads listed in SRS SCR-REQ-CAT-001 B5 (read Item / Variant by identifier, storefront catalogue read, Items of a Category, barcode lookup). Also the reads that the SRS A3 gives to consumers (Category for SFR/INS, Item unit for INV/PUR/SLS, Variant images for SFR), the catalogue search (ADR-CAT-043) and the turn-off check (ADR-CAT-041) |
| Event promised | The catalogue-change event of REQ-CAT-008 / RULE-CAT-049. Downstream modules react to it ("Downstream modules react to catalogue changes without CAT reading them"). The SRS states this, so it is not an open point |

**Promises that apply to every item:**
- **Identifiers.** Every identifier is a BIGINT identity key that the database assigns (stack.db.pk_generation = identity). A consumer's HARD-FK column to a CAT entity is `BIGINT`. An identifier is never reused or re-assigned.
- **Deactivation (RULE-CAT-048, ARCH-5).** A SHARED record is never deleted. Deactivation sets `isActiveFl` to false. The record stays readable by its identifier, leaves new selections, and raises a catalogue-change event with change kind DEACTIVATED. CAT notifies consumers and never cascades into them.
- **Tenancy.** The given core resolves the tenant. Every operation answers within the caller's tenant, and a record of another tenant reads as *not found*.
- **Names.** `nameAr` is always present. Where `nameEn` is empty, the English display returns `nameAr` (REQ-CAT-029).
- **Lookup codes.** Lookup-backed values are returned as codes of the CAT-owned `mdl` keys (SRS A6). Consumers resolve labels through the core lookup module. A code that is deactivated later stays on existing records (REQ-CAT-076).
- **Authorization of in-process calls (ADR-CAT-061).** The operation items are in-process calls. CAT does not check a CAT permission on them: the **calling module authorizes its own caller** against its own permissions before it calls CAT (for example SFR for a storefront shopper, SLS for a cashier). The one exception is the catalogue search (CON-CAT-016), which answers an empty result to a user without VIEW on CAT_ITEMS. Tenancy still applies to every call. Checklist C13 states no exception for an in-process read, so this rule stands on condition: CAT has raised `CORE-GAP-in-process-authorization.md`, and ADR-CAT-066 follows the core's answer (for example a core-provided caller authority for in-process reads) without changing these signatures.
- **Obligation of the capability-flag writer (POL-CAT-005).** A write that turns `variants` or `bundles` off must first call CON-CAT-017 and must refuse the write when `allowed` is false, showing the returned message and dependent Items. CAT answers the check and never writes a flag. The writer (owner OPEN, P-02) does not yet honour this, so it is recorded as an OPEN platform finding in `business-policies-cat.md` (PLATFORM FINDINGS, row 2).

---

## 1 — Entity items

### CON-CAT-001 — Item (صنف / Item)

Entity    : ENT-CAT-001 — key columns: code (business key, series CAT_ITEM, read-only), itemPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-001, REQ-CAT-001, REQ-CAT-032, REQ-CAT-034

Consumers store `itemPk`. `code` is unique per tenant and never changes (RULE-CAT-004). The values of `itemKindCode` (STANDARD / BUNDLE) and `baseUomCode` are fixed after first save (RULE-CAT-022, RULE-CAT-007).

### CON-CAT-002 — Variant (متغيّر الصنف / Variant)

Entity    : ENT-CAT-002 — key columns: code (business key, series CAT_VARIANT, read-only), variantPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-002, REQ-CAT-025, REQ-CAT-140, REQ-CAT-141, REQ-CAT-033, REQ-CAT-017

Stock, price, barcode and the e-invoice override attach to the Variant (D-01). Every Item has at least one Variant, and an active Item always has exactly one active default Variant (`isDefaultFl` true), the Variant stock and price consumers fall back to. When the default Variant is deactivated, the flag moves to the Item's remaining active Variant with the lowest code; when Variants are generated for an Item with no active Variant, the first Variant created becomes the default (REQ-CAT-025, ADR-CAT-072). A consumer therefore reads the default from the Variant reads and never assumes it is the Variant created with the Item. Turning `variants` on never changes an existing `variantPk`, code or barcode (REQ-CAT-017).

### CON-CAT-003 — Category (فئة / Category)

Entity    : ENT-CAT-006 — key columns: code (business key, unique per tenant, fixed after creation), categoryPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-006, REQ-CAT-064, REQ-CAT-067

The Category tree is at most 5 levels deep and has no cycles (RULE-CAT-023).

### CON-CAT-004 — Item unit (وحدة الصنف / Item unit)

Entity    : ENT-CAT-008 — key columns: itemFk + uomCode (business key among the Item's active units), itemUnitPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-008, REQ-CAT-077, REQ-CAT-081

`conversionFactor` is the number of base units in one unit of this kind (decimal, 6 places). It is fixed after saving (RULE-CAT-028), so a quantity that a consumer recorded keeps its meaning.

### CON-CAT-005 — Barcode (باركود / Barcode)

Entity    : ENT-CAT-009 — key columns: barcodeValue (business key, unique among the tenant's active barcodes, SCALE compared only with SCALE), barcodePk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-009, REQ-CAT-083, REQ-CAT-087

Every barcode identifies one Variant in one unit (`uomCode`).

### CON-CAT-006 — Bundle component (مكوّن الحزمة / Bundle component)

Entity    : ENT-CAT-007 — key columns: bundleItemFk + componentVariantFk (business key among the Bundle's active lines), bundleComponentPk (identifier) · identifier type: BIGINT
Traces    : ENT-CAT-007, REQ-CAT-056, REQ-CAT-058

A Bundle keeps no stock of its own; consumers count stock on the component Variants (DP §8 P-06). A line's `quantity` (decimal, 3 places) is always the number of **base units of the component Variant's Item** in one Bundle — never a quantity in one of that Item's units — and every read returns that unit as `componentBaseUomCode` (ADR-CAT-080). The base unit is fixed after the Item's first save (RULE-CAT-007), so a consumer that splits a sold Bundle into component movements multiplies the Bundle quantity by `quantity` and records the result in the component's base unit. Component Variants always belong to STANDARD Items, and Bundles are never nested (RULE-CAT-019).

---

## 2 — Operation items

### CON-CAT-007 — Read an Item (قراءة صنف / Read an Item)
Signature : getItem(itemPk: BIGINT) → Item {itemPk, code, nameAr, nameEn, itemKindCode, baseUomCode, categoryFk, brandCode, taxClassCode, einvoiceCodeTypeCode, einvoiceCode, publishStateCode, isActiveFl; active Variants with their attribute values; Item units; barcodes; Bundle component lines (componentVariantFk, Variant code, quantity, componentBaseUomCode — the base unit of the component Variant's Item, in which the quantity is expressed); active media} · errors: not found
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, REQ-CAT-001, REQ-CAT-002, REQ-CAT-010, REQ-CAT-058

An inactive Item is still returned, with `isActiveFl` false. Every read that starts after a commit returns that commit's state (REQ-CAT-010).

### CON-CAT-008 — Read a Variant (قراءة متغيّر / Read a Variant)
Signature : getVariant(variantPk: BIGINT) → Variant {variantPk, itemFk, code, nameAr, nameEn (derived, D-15), isDefaultFl, isActiveFl, Item baseUomCode, Item taxClassCode, effective einvoiceCodeTypeCode and einvoiceCode (Variant override, else Item), einvoiceUnitCode of the base unit (empty when the unit has no active Unit e-invoice code)} · errors: not found
Entity    : ENT-CAT-002
Traces    : ENT-CAT-002, REQ-CAT-050, REQ-CAT-119, REQ-CAT-122, REQ-CAT-029

An inactive Variant is still returned, with `isActiveFl` false (REQ-CAT-050).

### CON-CAT-009 — Read an Item unit (قراءة وحدة صنف / Read an Item unit)
Signature : getItemUnit(itemUnitPk: BIGINT) → Item unit {itemUnitPk, itemFk, uomCode, conversionFactor, isPurchaseFl, isSalesFl, isActiveFl, einvoiceUnitCode (empty when none is active)} · errors: not found
Entity    : ENT-CAT-008
Traces    : ENT-CAT-008, REQ-CAT-079, REQ-CAT-122

### CON-CAT-010 — Look up a barcode or scale label (البحث بالباركود أو ملصق الميزان / Barcode and scale label lookup)
Signature : lookupBarcode(value: text) → {variantPk, itemPk, uomCode, embeddedValue (weight or price, with the layout's decimals; empty for a plain barcode), embeddedValueTypeCode (WEIGHT / PRICE; empty for a plain barcode)} · errors: not found — no active barcode or scale layout matches (RULE-CAT-047); scale label not readable — wrong check digit or unknown scale code (RULE-CAT-035)
Entity    : ENT-CAT-009
Traces    : ENT-CAT-009, REQ-CAT-086, REQ-CAT-090, REQ-CAT-097, REQ-CAT-100

Only active barcodes of active Variants of active Items, and active scale label layouts, match (REQ-CAT-085, REQ-CAT-094, REQ-CAT-086 — ADR-CAT-059).

### CON-CAT-011 — Read a Category (قراءة فئة / Read a Category)
Signature : getCategory(categoryPk: BIGINT) → Category {categoryPk, code, nameAr, nameEn, parentCategoryFk, sortOrder, isActiveFl} · errors: not found
Entity    : ENT-CAT-006
Traces    : ENT-CAT-006, REQ-CAT-066, REQ-CAT-067

### CON-CAT-012 — Category tree (شجرة الفئات / Category tree)
Signature : getCategoryTree(activeOnly: flag) → tree of Category, each node under its parent and ordered by sortOrder · errors: none
Entity    : ENT-CAT-006
Traces    : ENT-CAT-006, REQ-CAT-067

### CON-CAT-013 — Items of a Category (أصناف الفئة / Items of a Category)
Signature : listCategoryItems(categoryPk: BIGINT, page, size) → page of active Items of the Category and of its active sub-categories · errors: not found
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, ENT-CAT-006, REQ-CAT-068

### CON-CAT-014 — Storefront catalogue read (كتالوج المتجر الإلكتروني / Storefront catalogue)
Signature : listStorefrontItems(page, size) → page of Items with isActiveFl true and publishStateCode PUBLISHED · errors: none
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, REQ-CAT-115, REQ-CAT-113, REQ-CAT-114

### CON-CAT-015 — Images of a Variant (صور المتغيّر / Images of a Variant)
Signature : getVariantImages(variantPk: BIGINT) → list of image {fileDocumentRef, nameAr, nameEn, isPrimaryFl, sortOrder} — the active images bound to the Variant, or else the Item's active images bound to no Variant, primary first · errors: not found
Entity    : ENT-CAT-002
Traces    : ENT-CAT-002, REQ-CAT-109

`fileDocumentRef` points to the given `file` public image storage (`erp-core:modules.file`).

### CON-CAT-016 — Catalogue search for the global search (بحث الكتالوج / Catalogue search)
Signature : searchCatalogue(term: text) → at most 20 active Items {itemPk, code, nameAr, nameEn} whose nameAr, nameEn, code or active barcode contains the term, exact code or barcode matches first · errors: term too short — fewer than 2 characters (RULE-CAT-046); forbidden — the caller lacks VIEW on CAT_ITEMS, answered as an empty result
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, REQ-CAT-125, REQ-CAT-126, REQ-CAT-127

The global search box sits outside CAT (ADR-CAT-043).

### CON-CAT-017 — Capability turn-off check (فحص إيقاف القدرة / Capability turn-off check)
Signature : checkCapabilityTurnOff(flag: `variants` | `bundles`) → {allowed: flag, dependentCount: number, dependentItems: at most 20 {itemPk, code, nameAr}, messageAr and messageEn (the RULE-CAT-010 or RULE-CAT-011 message with {count} filled when not allowed; empty when allowed)} · errors: invalid input — flag other than `variants` or `bundles`
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023

This check is offered to the capability-flag owner (owner OPEN, P-02 — ADR-CAT-041). CAT does not own or change the flags.

---

## 3 — Event item

### CON-CAT-018 — Catalogue-change event (إشعار تغيير الكتالوج / Catalogue-change event)
Signature : catalogueChanged(entity: ENT id of the changed record — ENT-CAT-001, 002, 005, 006, 007, 008, 009 or 010; recordPk: BIGINT; itemPk: BIGINT (empty when entity is ENT-CAT-006); changeKind: CAT_CHANGE_KIND — CREATED / UPDATED / DEACTIVATED / REACTIVATED) → one event per committed change, delivered after commit · errors: none — a rolled-back change publishes no event (RULE-CAT-049)
Entity    : ENT-CAT-001
Traces    : ENT-CAT-001, ENT-CAT-002, ENT-CAT-006, REQ-CAT-008, REQ-CAT-009

The event is recorded in the same transaction as the change through the given `events` outbox, and it is delivered only after commit. Consumers read the current state through CON-CAT-007 … CON-CAT-015; the event carries no field values.

A Category (ENT-CAT-006) is a SHARED record read by SFR and INS, so its creation, change (including a move under another parent), deactivation and reactivation each publish one event with `entity` ENT-CAT-006, `recordPk` = categoryPk and an empty `itemPk`; consumers re-read it through CON-CAT-011 / CON-CAT-012 (ADR-CAT-077). Adding ENT-CAT-006 to the entity list is ADDITIVE: the payload keeps its fields and types, and no consumer edge cites the item yet (§6).

---

## 4 — Given usage (repeated verbatim from SRS A9)

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

The contract does not promise any entity that a given provides. Tenant, User, Role, LookupType/LookupValue, FileDocument and NumberSeries stay with `erp-core` [C:C-GIVEN-1]. A new tenant receives the CAT seeds through the given provisioning (REQ-CAT-134, ADR-CAT-058), so every operation item works in every tenant from its first request.

---

## 5 — Stability

- The items above are ADDITIVE in CS-CAT-001. Later versions may add items freely.
- Changing or removing any CON-CAT item, a key column, or the BIGINT identifier type is legal only in a BREAKING change set with an ADR. `gov.py graph` then raises a resolution event on every consumer edge that cites the item.

## 6 — Self-check

- [x] Every SHARED ENT of the SRS has an entity item (ENT-CAT-001, 002, 006, 007, 008, 009). No consumer's A8 block exists yet that names any other CAT entity, and no PRIVATE entity is promised.
- [x] Every operation item has a `Signature` line and names no path or verb.
- [x] Every item traces to at least one ENT and one REQ (C13.5).
- [x] No question was raised. The CON sequence starts at 001 because the current state holds no earlier contract, and it runs 001 … 018 without gaps (next free: CON-CAT-019). The contract itself raised no ADR; ADR-CAT-072 (gate-analysis round 6) amends the wording of CON-CAT-002 without changing its key columns or identifier type, and ADR-CAT-077 (gate-analysis round 9) adds ENT-CAT-006 to the entity list of CON-CAT-018 without changing its fields. ADR-CAT-078 (gate-analysis round 10) adds an operation for the barcode label screen and changes no contract item. ADR-CAT-079 (gate-analysis round 11) narrows the barcode printed on a label and changes no contract item; ADR-CAT-080 (same round) states the unit of a Bundle component quantity in CON-CAT-006 and adds `componentBaseUomCode` to the component lines of CON-CAT-007, without changing a key column or identifier type (ADDITIVE). Per the current registries the next free ADR is ADR-CAT-081.
- [x] §4 carries the SRS `given-usage` rows verbatim, in the same order.
- [x] Every item title carries ar and en.
