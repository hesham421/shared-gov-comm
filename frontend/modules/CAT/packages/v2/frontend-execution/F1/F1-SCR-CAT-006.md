<!-- source: PHASE:F1 / SUB:F1-SCR-CAT-006 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: API-CAT-032, API-CAT-033, API-CAT-034, API-CAT-035, API-CAT-036, API-CAT-037, API-CAT-045, API-CAT-051, API-CAT-052, REQ-CAT-021, REQ-CAT-022, REQ-CAT-023, REQ-CAT-092, REQ-CAT-093, REQ-CAT-094, REQ-CAT-095, REQ-CAT-098, REQ-CAT-120, REQ-CAT-121, REQ-CAT-122, REQ-CAT-123, REQ-CAT-124, REQ-CAT-134, SCR-CAT-006, UXD-CAT-001 -->
<!-- SUB:F1-SCR-CAT-006:START traces=API-CAT-052,REQ-CAT-134,REQ-CAT-021,REQ-CAT-022,REQ-CAT-023,REQ-CAT-092,REQ-CAT-093,REQ-CAT-094,REQ-CAT-095,REQ-CAT-098,REQ-CAT-120,REQ-CAT-121,REQ-CAT-122,REQ-CAT-123,REQ-CAT-124,API-CAT-032,API-CAT-033,API-CAT-034,API-CAT-035,API-CAT-036,API-CAT-037,API-CAT-045,API-CAT-051,UXD-CAT-001,SCR-CAT-006 -->
### F1 · SCR-CAT-006 — Catalogue settings

Bound schemas: `CatalogueSettings` with `ScaleLabelLayoutView[]` and `UnitEinvoiceCodeView[]` (API-CAT-045) · `ScaleLabelLayoutCreateRequest` / `ScaleLabelLayoutUpdateRequest` (API-CAT-032 / 033) · layout deactivation (API-CAT-034) · `UnitEinvoiceCodeCreateRequest` / `UnitEinvoiceCodeUpdateRequest` (API-CAT-035 / 036) · unit code deactivation (API-CAT-037) · `TurnOffCheckResult` with `DependentItem[]` (API-CAT-051).

UI types:
- `SettingsRouteState = { layout?: scaleLabelLayoutPk | 'new', unitCode?: unitEinvoiceCodePk | 'new', check?: 'variants' | 'bundles' }`.
- `LayoutForm`: create carries `key`, `scaleCodeLength` and the rest; update carries neither `key` nor `scaleCodeLength` (both fixed after creation — ADR-CAT-064) and adds `version`.
- `UnitCodeForm`: create carries `key` + `einvoiceUnitCode`; update carries `einvoiceUnitCode` + `version`.
<!-- SUB:F1-SCR-CAT-006:END -->
