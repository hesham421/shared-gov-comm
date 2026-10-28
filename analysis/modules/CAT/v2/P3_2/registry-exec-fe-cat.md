## REGISTRY — P3.2 — CAT v2

### ID ranges
| Kind | Range | Count | Next free |
|---|---|---|---|
| UXD | UXD-CAT-001 … UXD-CAT-003 | 3 | UXD-CAT-004 |
| SCR | SCR-CAT-001 … SCR-CAT-006 | 6 | SCR-CAT-007 |

Both streams start at 001, because the current state held no frontend design for CAT. This stage mints no ID of another kind. API ids are cited from `api-spec-cat.yaml`.

### Screens
| SCR | Name (en / ar) | SCR-REQ | Owning ENT | Container | Permissions |
|---|---|---|---|---|---|
| SCR-CAT-001 | Items / الأصناف | SCR-REQ-CAT-001 | ENT-CAT-001 (+ 002, 005, 007, 008, 009, 010) | page | PERM_CAT_ITEMS_VIEW · _CREATE · _UPDATE · _DELETE |
| SCR-CAT-002 | Categories / الفئات | SCR-REQ-CAT-002 | ENT-CAT-006 | drawer | PERM_CAT_CATEGORIES_VIEW · _CREATE · _UPDATE · _DELETE |
| SCR-CAT-003 | Variant attributes / خصائص المتغيّرات | SCR-REQ-CAT-003 | ENT-CAT-003 (+ 004) | drawer | PERM_CAT_VARIANT_ATTRIBUTES_VIEW · _CREATE · _UPDATE · _DELETE |
| SCR-CAT-004 | Item import / استيراد الأصناف | SCR-REQ-CAT-004 | ENT-CAT-001 | page | PERM_CAT_ITEM_IMPORT_VIEW · _CREATE |
| SCR-CAT-005 | Barcode labels / ملصقات الباركود | SCR-REQ-CAT-005 | ENT-CAT-002 (+ 009) | page | PERM_CAT_BARCODE_LABELS_VIEW · _CREATE |
| SCR-CAT-006 | Catalogue settings / إعدادات الكتالوج | SCR-REQ-CAT-006 | ENT-CAT-011, ENT-CAT-012 | drawer | PERM_CAT_SETTINGS_VIEW · _CREATE · _UPDATE · _DELETE |

### UXD index
| UXD | Screen | Field | Owner module · source |
|---|---|---|---|
| UXD-CAT-001 | SCR-CAT-001, SCR-CAT-006 | brandCode, baseUomCode, taxClassCode, itemKindCode, publishStateCode, einvoiceCodeTypeCode, uomCode, barcodeTypeCode · embeddedValueTypeCode, key (CAT_UOM), einvoiceUnitCode | erp-core `modules.mdl` · the given's lookup client (no CAT API) |
| UXD-CAT-002 | SCR-CAT-001, SCR-CAT-003 | `variants` / `bundles` flag-bound panes, fields, filters, menu entry | erp-core `modules.cu` (FeatureFlags) · `GET /api/public/app-config` (no CAT API) |
| UXD-CAT-003 | SCR-CAT-001 | image thumbnail of `fileDocumentRef` | erp-core `modules.file` (public image storage) · the given's public file client (no CAT API) |

All three UXDs trace to REQ and AC and are cited by F2 / F4 blocks of the plan (ADR-CAT-050). No XM is implied.

### API coverage
- Used: 49 of the 51 documented operations, each with a screen action and a route (operations coverage table, ALIGN-FE).
- Unused: API-CAT-011 (read Item unit) and API-CAT-046 (storefront catalogue read). They are consumers' reads, recorded in ADR-CAT-051.
- Mock: `api-spec-cat.yaml`.
- `unmapped`: none.
- Runtime codes bound to a RULE: 49 of 49. The 8 platform-standard codes are routed generically (ADR-CAT-044).

### ALIGN
The verdict is stamped by the orchestrator from `gov.py analyze`. The self-check block is a placeholder (findings 0, clean true), and the ALIGN rows carry no author mark.

Findings fixed during authoring:
- The Items screen's child actions, each a separately committing operation, were folded into the one Save with a declared call order, instead of per-line saves (ADR-CAT-049).
- The Bundle component picker was moved off `API-CAT-042`, so that it stays inside the Items page permission (ADR-CAT-051 §3).

### ADRs
| ADR | Subject | Status |
|---|---|---|
| decisions/CAT/ADR-CAT-049 | Items screen: one Save owning ordered calls; record commands outside the form | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-050 | Display data served by the given (lookups, capability flags, image files) → UXD-CAT-001 … 003 | ACCEPTED (non-breaking) |
| decisions/CAT/ADR-CAT-051 | API coverage of the CAT screens and binding notes | ACCEPTED (non-breaking) |

The next free ADR is ADR-CAT-052. No ADR is BLOCKED, and no question was raised.

### Traceability
- REQ covered by at least one SCR or F-block: 127 / 127. Orphan REQ: none.
- US covered by at least one flow: 22 / 22.
- Every PHASE and SUB carries traces= (6 PHASE, 24 SUB). Every SCR traces to REQ and AC (and UXD where it shows foreign data), and every UXD traces to REQ and AC.

### Event
"P3.2 completed: CAT v2 — SCR 6 · UXD 3 · flows 18 · F-SUBs 24 · API used 49/51 · ADR 3 (049–051, none BLOCKED)"
