<!-- source: PHASE:F1 / SUB:F1-SCR-INV-009 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-INV-008, API-INV-043, API-INV-044, API-INV-045, REQ-INV-124, REQ-INV-125, REQ-INV-126, REQ-INV-127, REQ-INV-128, REQ-INV-129, REQ-INV-134, REQ-INV-135, SCR-INV-009, UXD-INV-009, UXD-INV-010 -->
<!-- SUB:F1-SCR-INV-009:START traces=SCR-INV-009,UXD-INV-009,UXD-INV-010,REQ-INV-124,REQ-INV-125,REQ-INV-126,REQ-INV-127,REQ-INV-128,REQ-INV-129,REQ-INV-134,REQ-INV-135,API-INV-008,API-INV-043,API-INV-044,API-INV-045 -->
### F1 · SCR-INV-009 — Item stock settings
- Schemas: `ItemStockSettings`, `ItemStockSettingsRequest`, `ItemStockSettingsEnvelope`, `ItemStockSettingsPageEnvelope`.
- `ItemSettingsFilter {itemPk?, term?, lotTracked?, dualQuantity?, page, size}`.
- `ItemSettingsFormState {item: CatItemRef, lotTrackedFl, dualQuantityFl, measureUomCode | null, version | null}`. `version` is null when the Item has no record yet (ADR-INV-029).
- `ItemSettingsEntryNav = 'new' | itemPk`.
<!-- SUB:F1-SCR-INV-009:END -->
